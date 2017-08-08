Autonomous Code
===============

Every FRC match begins with a 15-second autonomous period. During this time, drivers are not allowed to touch the controls, and the robot runs off of preprogrammed instructions.

For most cases, autonomous code is simply just writing and running a `CommandGroup <./../commands/commandgroups.html>`_ that is specifically designed to accomplish certain autonomous tasks.

1. Find out what commands you have
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Just like for any command group, before writing an autonomous plan, you need to know what commands you have to work with. For every action that needs to be performed in an autonomous plan (e.g. driving, dropping a gear, shooting a ball, etc), there also needs to be a command for that action. If you need a subsystem to do some action, but there isn't a command for it, you may need to either `write the command yourself <./../commands/commands.html>`_ or find someone who can.

Take special note of the drive commands. Autonomous mode requires that the robot navigate the field accurately using only input from its sensors. This is often possible to a high degree of accuracy simply using encoders and a gyro, so look for commands that allow you to drive the robot forward some number of encoder ticks, or rotate to some angle. These will be essential.

2. Make a plan
^^^^^^^^^^^^^^

This section assumes that you know how to use addSequential and addParallel. For more information on this, see `Command Groups <./../commands/commandgroups.html#make-a-plan>`_.

First, talk to Strategy and ask what they want as an autonomous plan. They should be able to give you an idea of the tasks that need to be done in the autonomous mode. For an example of an autonomous plan, see `Example Autonomous <autoexample.html>`_.

Then, examine the field. Calculate dimensions and distances, and figure out how you will command the robot to move from one position to another by using the commands you have. Note that you may need different autonomous plans for each of the 3 starting positions. The field may even be asymmetric, which means you'll even more plans to account for those differences. 

The way to handle this is to have many autonomous plans, and then allow the drivers to choose which one to use. See `Autonomous Chooser <autochooser.html>`_.

3. Write the Autonomous Plan
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

`Creating the command group file <./../commands/commandgroups.html#make-the-command-group-s-file>`_ for autonomous is the same process as any other command group.

Now, simply follow the plan you created. As far as movement around the field goes, since every field is different, it's usually a good idea to put each distance in terms of the field size. For example, if I want the robot to move halfway across the field, it's smarter to put:

.. code-block:: java

	final double FIELD_LENGTH = 648.0;

	// drive forward halfway across the field
	addSequential(new DriveForward(FIELD_LENGTH * 0.5));

than

.. code-block:: java

	addSequential(new DriveForward(324.0));

By putting your commands in terms of field measurements, not only is it easier to adjust to differing field sizes, it's also more readable, since the parameter being passed into the commands are directly related to the math done in the plan that gets the robot to the correct position.

Again, an example is very useful here. If this doesn't make sense, try `Example Autonomous <autoexample.html>`_.

4. Testing the Autonomous Plan
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Testing autonomous takes the longest and is possibly the most important part of having a successful autonomous.

On how to test autonomous plans once the robot is ready to test, see `Testing Autonomous <autotesting.html>`_.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Further Reading

	autoexample
	autochooser
	autotesting