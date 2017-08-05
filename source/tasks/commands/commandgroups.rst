Creating a Command Group
========================

This article covers how to write a Command Group. A Command Group is a chain of existing commands that can be run in order or at the same time..

For example, "Open the claw and then dispense the gear" or "Move the elevator up while driving forward" are examples of possible Command Groups. Other possible commands can include "push the solenoid forward and then wait 1 second to make sure it's pushed out", as well as autonomous code.

If you only need one, self-contained action that isn't composed of a chain of other commands, see `Command <commands.html>`_.

Preparation
-----------

1. Find out what commands you have
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Since a CommandGroup is composed of a chain of existing commands, you need to know what commands you have at your disposal, and if you have to fill in any gaps for commands that don't yet exist.

If you need a command for the command group that does not yet exist, make sure to either `create the command yourself <commands.html>`_, or contact someone else who can create it instead.

2. Make a plan
^^^^^^^^^^^^^^

It's important to understand what tools a command group has at its disposal. There are two ways of adding a command to a command group:

1. addSequential(Command command)

	This method adds a command to the end of the command group. So, the first command you add with addSequential will be the first command to start. Then, the second command added will start when the first is done, and so on.

	Example:

	.. code-block:: java

		// drive forwards
		addSequential(new DriveForward(1000, 10));

		// then open the grabber
		addSequential(new OpenGrabber());

		// then drive backwards
		addSequential(new DriveForward(-1000, 10)); 

2. addParallel(Command command)

	This command schedules a command to start at the same time as the next command added with addSequential.

	Example:

	.. code-block:: java

		// drive forwards
		addSequential(new DriveForward(1000, 10));

		addParallel(new LiftRake());
		addParallel(new IntakeOut());
		// then, lift the rake, run intake outwards, and open the grabber at the same time
		addSequential(new OpenGrabber());           

		// then drive backwards
		addSequential(new DriveForward(-1000, 10));

With this information, it's hopefully possible to make an easy to follow plan using the commands that already exist.

Writing the Command
-------------------

1. Make the command group object
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Right click on the project and choose **New** -> **Other**.

.. image:: ./_static/scrn6.png
   :width: 80%

Then, in the New window that appears, scroll down to **WPILib Robot Java Development** and choose **CommandGroup**.

.. image:: ./_static/scrn7.png
   :width: 60%
.. image:: ./_static/scrn8.png
   :width: 60%

Then, name your command.

.. image:: ./_static/scrn9.png
   :width: 70%

Press **Finish**, and it should be created.

.. image:: ./_static/scrn10.png
   :width: 80%

2. Write the command group
^^^^^^^^^^^^^^^^^^^^^^^^^^

It is usually easy to write a command group's plan in code. The only difficulty usually lies in autonomous plans, since often, math on field measurements and long, complex chains of commands that depend on certain parameters are involved.

Here are some examples of command groups:

 - `Deflector Flip <examples/deflector.html>`_
 	Pushes a piece of plastic down, and then back up, to dislodge any gears stuck on top of it. 

 - `Acquire gear from a wall <examples/wallacquire.html>`_
 	Goes through the sequence of events that prepares the robot to acquire a gear from the wall.

 - `Shooting sequence <examples/shootingsequence.html>`_
 	Prepares the shot and then shoots.

 - `Basic autonomous <examples/autonomous.html>`_
 	Example of one autonomous plan.

 - `Autonomous chooser <examples/autonomouschooser.html>`_
 	A list of autonomous plans that can be chosen in SmartDashboard.

List of example command groups:

.. toctree::
	:glob:
	:maxdepth: 10

	examples/commandgroupexamples

3. Run the command group
^^^^^^^^^^^^^^^^^^^^^^^^

Since a CommandGroup is also a Command, you can run a CommandGroup in the same way as you would `run a command <commands.html#running-the-command>`_.