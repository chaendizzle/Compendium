Gear manipulator subsystem
==========================

In this example, we create a subsystem designed to control the Gear Manipulator subsystem from 2017.

In this subsystem, a belt motor moves a carriage between two endpoints, the front and the back of the robot. At each end, there is a reed switch that tells when the carriage has reached that end. There is also an encoder that tracks the position of the belt.

Attached to the carriage, there is also a claw that is made up of two grabbers that open and close, controlled by double solenoids. One grabber is in the front, and one grabber is in the back. They open and close to allow gears in.

Start out by `creating the subsystem file <./../subsystems.html#create-the-subsystem-s-file>`_. You'll start out with this code:

.. code-block:: java

    /**
     *
     */
    public class GearManipulator extends Subsystem {

        // Put methods for controlling this subsystem
        // here. Call these from Commands.

        public GearManipulator()
        {

        }

        public void initDefaultCommand() {
            // Set the default command for a subsystem here.
            //setDefaultCommand(new MySpecialCommand());
        }
    }

Now, create the Victor that represents the climber motor.

Declare it in the class:

.. code-block:: java

	private static Victor CLIMBER_MOTOR;

and initialize it in the constructor:

.. code-block:: java

    public Climber()
    {
        CLIMBER_MOTOR = new Victor(RobotMap.CLIMBER_MOTOR_PIN);
    }

If RobotMap.CLIMBER_MOTOR_PIN does not yet exist, make sure to add it to RobotMap also.

Then, add the method setClimberMotor.

.. code-block:: java

	public static void setClimberMotor(double value)
	{
	    CLIMBER_MOTOR.set(value);
	}

And that's it! Now, the Climber subsystem has a method that sets the climber motor, using its speed controller component.

On creating a command that uses this subsystem, see `ClimberContinuous <./../../commands/examples/commands/climber.html>`_.

Final code
^^^^^^^^^^

.. code-block:: java

    /**
     * Climber subsystem
     */
    public class Climber extends Subsystem
    {

        private static Victor CLIMBER_MOTOR;

        public Climber()
        {
            CLIMBER_MOTOR = new Victor(RobotMap.CLIMBER_MOTOR_PIN);
        }

        public void initDefaultCommand()
        {

        }

        public static void setClimberMotor(double value)
        {
            CLIMBER_MOTOR.set(value);
        }
    }

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Examples
	
	