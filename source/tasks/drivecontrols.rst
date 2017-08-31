==================
Drive Programming
==================

Programming the drive controls is one of the most important tasks of the programming team. This section is dedicated to programming `Tank Drive <drivetraintypes.html>`_. For other drivetrain types, see `Advanced Drive Programming <advanceddriveprogramming.html>`_.

Note that this guide does not cover pneumatic gearshifting and assumes a single gear robot.


Tank Drive Control Schemes
===========================

There are 2 common control schemes for a tank drive robot. Both use 2 joysticks, and work best with the xbox controller. Your team may design a different scheme based on this year's game, but these are the most common and most useful.

- Tank Drive

	In the tank drive scheme, the y-axis of each joystick controls the left and right drivetrain, respectively. This scheme gives the most direct control over the motors of the robot, but has the disadvantage of being difficult to drive straight.

	.. image:: ./gfx/tankdrive.png
		:width: 70%


- Car Drive

	This scheme is designed to make the robot handle like a car, with the left joystick y-axis controlling robot speed and the right joystick x-axis controls turning. This gives less direct control over the drivetrains, but is much easier to drive straight and is preferred by many drivers.

	.. image:: ./gfx/cardrive.png
		:width: 70%


Drive System Programming
==========================


When writing a drive control system, it is defined in 2 parts:
	- The Drive `subsystem <../robotcode/structure/subsystems.html>`_, which controls the motors directly
	- The Drive Scheme `command <../robotcode/structure/commands.html>`_, which listens for controller input and outputs to the motors via the Drive subsystem

----------------------------
Writing the Drive Subsystem
----------------------------

The first step is to create the drive subsystem, which will define how drive control schemes interact with the drive motors.
Below we `instantiate our motor controllers <../robotcode/components/actuators/motors.html>`_ and define functions for setting speed of the left and right drivetrain.

This code assumes Spark motor controllers and a 4-motor drivetrain.

.. code-block:: java

	public class Drive extends Subsystem {

		//instantiate motor controllers with PWM ports we define in RobotMap
		Spark FRONT_RIGHT_MOTOR = new Spark(RobotMap.FR_MOTOR);
		Spark BACK_RIGHT_MOTOR = new Spark(RobotMap.BR_MOTOR);
		Spark FRONT_LEFT_MOTOR = new Spark(RobotMap.FL_MOTOR);
		Spark FRONT_RIGHT_MOTOR = new Spark(RobotMap.FR_MOTOR);

		public Drive() {

		}

		public void initDefaultCommand() {
			//set drive scheme as default command, so robot can drive around
			//here we are using Tank Drive
			setDefaultCommand(new TankDrive());
		}

		//set both motors for left drivetrain
		//takes value between -1 and 1
		public void setLeftDrivetrian(double speed) {
			FRONT_LEFT_MOTOR.set(speed);
			BACK_LEFT_MOTOR.set(speed);
		}

		//set motors for right drivetrain
		public void setRightDrivetrain(double speed) {
			FRONT_RIGHT_MOTOR.set(speed);
			BACK_LEFT_MOTOR.set(speed);
		}


	}


Now we can output to the drivetrains without having to instantiate motor controllers and set them individually every time we want to set drivetrain speed.

-----------------------------
Writing the TankDrive Command
-----------------------------

Now for the TankDrive command, we need to
	+ Get controller input
	+ Output to motors

.. code-block:: java

	public class TankDrive extends Command {

		public TankDrive() {
			//requires the Drive subsystem (instantiated as drive in Robot.java)
			requires(Robot.drive);
		}

		protected void initialize() {
		}

		protected void execute() {
			//get joystick values
			//here we assume xbox is defined as a static controller OI
			double leftY = OI.xbox.getRawAxis(OI.xboxLeftY);
			double rightY = OI.xbox.getRawAxis(OI.xboxRightY);

			//set joystick values to drivetrains
			Robot.drive.setLeftDrivetrain(-leftY);
			Robot.drive.setRightDriveTrain(-rightY); 

		}

		protected boolean isFinished() {
			return false;
		}

		protected void end() {
			//if ended, stop robot
			Robot.drive.setLeftDrivetrain(0);
			Robot.drive.setRightDriveTrain(0);
		}

		protected void interrupted() {
			//if interrupted, end command
			end();
		}

	}

Note that we used -leftY and -rightY when setting drivetrain motors because the xbox drivers return -1 at the top and 1 on the bottom, so inverting the values lets us set the motor values. 


Sensitivity and Control Curves
===============================

This is all you need to get basic Tank Drive working, but it's not very easy to control. Mapping the output of the joystick directly to the motors doesn't give much room for the driver to do lower speed precision driving.

This direct mapping of the joystick position to the motor output is a *linear control curve*, and looks like so, with the
x axis being the joystick position (-1 being all the way down, and 1 being all the way up) [1]_, and the y axis the power output to the motors from -1 to 1.

	.. image:: ./gfx/linearcurve.png
		:width: 70%

This relationship can be shown mathematically as 
.. code-block::
	motor output = joystick value

or

	.. image:: ./gfx/oei.png
		:width: 15%

If we call the motor output O and the joystick value (input) I.

Note that this relationship doesn't give a lot of room to the lower motor speeds, which are important for precision driving. 
Higher speeds tend to be less important when it comes to accuracy, a driver needs to be able to control lower speeds well to get game tasks done quickly.

In order to maximize the amount of joystick room given to lower values, we use an odd polynomial curve (we multiply the values together to make the curve tend to stay at lower values for longer). This makes the formula:

	.. image:: ./gfx/oin.png
		:width: 15%

Where n is odd [2]_. 

The higher n is, the more room is given to lower values. This can be tweaked based on your driver's preferences. 
In the following example we use n=3.
This is represented by the equation:

	.. image:: ./gfx/i3.png
		:width: 15%

\
	.. image:: ./gfx/x3curve.png
		:width: 70%

Changing n to a higher values gives even more preference to lower values. Here we have n=5 for O=I^5.

	.. image:: ./gfx/x5curve.png
		:width: 70%


Great! Now our drivers can do higher precision driving, and still move at max speed when needed. There is a bit of a problem,
though. Robots run on carpet, which means there's a lot of internal resistance. Outputting to the motors at very low values
often won't do anything at all. This means that there's a certain threshold T after which  the robot will start to move, but it won't move before that.

It's pretty easy to see what this does to our curves in this example, where our example robot starts moving at T = 0.1 [3]_:

	.. image:: ./gfx/x3t.png
		:width: 70%

In this example we're using I^3 for our curve.

If the robot doesn't start moving until 0.1, then almost half of the joystick is a dead zone! This also negates our large area
for low sensitivity,  making for a pretty aggressive curve once the robot does start moving.

This is resolved by moving the curve *above* our movement threshold, so we still get our nice precision sensitivity and no dead space.

The piecewise formula for this is [4]_

	.. image:: ./gfx/piecewiseO.png
		:width: 55%

For 
	+ **O** is the output to the motors
	+ **I** is the input from the joystick
	+ **n** is the odd number that determines how much the curve is stretched toward the lower values
	+ **T** is the threshold after which the robot will start to move

If we plot an example for T=0.1 and n=3:

	.. image:: ./gfx/adjO3.gif
		:width: 70%

We can see that the robot drives accurately, with most of the joystick dedicated to low values, and there are no dead zones, 
as the curve never crosses T=0.1.

--------------
Implementation
--------------

The implementation and addition of this curve to our example curve is a simple task, and can be done by adding an applyCurve function to the *Drive* subsystem.

Here we call our T value TORQUE_RESISTANCE_THRESHOLD and assume it was defined in RobotMap, and use n=3.

.. code-block:: java

	private double applyCurve(double joystickPosition) {
		//apply piecewise logic
		if (joystickPosition > 0) {
			return (1 - RobotMap.TORQUE_RSISTANCE_THRESHOLD) * Math.pow(joystickPosition, 3) + RobotMap.TORQUE_RSISTANCE_THRESHOLD;
		}
		else if (joystickPosition < 0){ 
			return (1 - RobotMap.TORQUE_RSISTANCE_THRESHOLD) * Math.pow(joystickPosition, 3) - RobotMap.TORQUE_RSISTANCE_THRESHOLD;
		}

		//return 0 if joystickPosition is 0
		return 0;
	}

Next, we modify setLeftDriveTrain and setRightDriveTrain to use applyCurve before setting the motors. Note that because these functions will also be used for things like autonomous where you wouldn't want a curve, we add the argument useCurve to the function so the function call can opt whether to apply a drive curve.

.. code-block:: java

		//set both motors for left drivetrain
		//takes value between -1 and 1
		public void setLeftDrivetrian(double speed, boolean useCurve) {
			//use a temp variable to set the motors
			double sp = speed;

			//modify temp variable with curve if useCurve is true
			if (useCurve){
				sp = applyCurve(speed);
			}

			//set the motors with temp variable
			FRONT_LEFT_MOTOR.set(sp);
			BACK_LEFT_MOTOR.set(sp);
		}

		//set motors for right drivetrain
		public void setRightDrivetrain(double speed, boolean useCurve) {
			//use a temp variable to set the motors
			double sp = speed;

			//modify temp variable with curve if useCurve is true
			if (useCurve){
				sp = applyCurve(speed);
			}

			//set the motors with temp variable
			FRONT_RIGHT_MOTOR.set(speed);
			BACK_LEFT_MOTOR.set(sp);
		}

And finally we modify the TankDrive command to pass in a value of true for useCurve.

.. code-block:: java

	//set joystick values to drivetrains
	Robot.drive.setLeftDrivetrain(-leftY, true);
	Robot.drive.setRightDriveTrain(-rightY, true);

And that's it! We have written a function Drive subsystem and drive command that can drive a tank drive robot using the standard Tank Drive dual joystick scheme that uses a precise sensitivity curve.


.. [1] The xbox joystick actually uses 1 for all they down and -1 for all the way up, but we'll ignore that for the simplicity 	of the math and just account for it in the code

.. [2] We only use odd values here because putting negative outputs to an even value makes them positive, resulting in the robot being unable to go backwards anymore. This can be fixed for even powers by doing some tricks with absolute values, but it's not discussed here.

.. [3] Note that threshold have negative counterparts too: if the robot doesn't start moving until T=0.1, then it presumably won't start moving backwards until T=-0.1

.. [4] If you're wondering how this was derived, it was done by taking the original I^n function, moving it up by T, and solving O=a*I^n+T for a when O=1 and I=1 (which should still be the final position of the curve), and then piecewise splitting it to make it work.