Joysticks
==========

.. image:: ./_static/logitech-gamepad-f310-4.jpg
   :width: 40%
.. image:: ./_static/3d-pro-gaming-joystick-images.png
   :width: 40%

A Joystick represents a USB controller, plugged into the Driver Station laptop. It is given a port, which corresponds to its port assigned on the FRC Driver Station program.

For any controller, make sure to write down its button numbers, axis numbers, and which axes are inverted. Also check for a switch on the back, to see if it switches the controller type.
Access this information by plugging it into the computer, and looking at the Joysticks tab on FRC Driver Station.

Its buttons can be accessed with:

.. code-block:: java

	joystick.getRawButton(int buttonNumber)
	
However, we normally use event handlers to manage button presses, instead of getRawButton.

When it comes to thumbsticks on a controller, or any other free moving component of the joystick, these are accessed by:

.. code-block:: java

	joystick.getRawAxis(int axisNumber)
	
This is very commonly used to control the robot’s driving.
Joystick axes should be given deadzones, so that any input under 0.1 should be seen as 0. Otherwise, when motors are set to low values like 0.02, they can’t actually move and instead just make annoying squeaking sounds.

Often, axes are flipped on the controller, which cause up to be negative and down to be positive. Firstly, check for a switch on the back of the controller that will switch its controller mode, which will often fix this issue. If there isn’t one, then just flip it manually in code.

Sometimes, the triggers are seen as axes instead of buttons. Firstly, check for a switch on the back of the controller that will switch its controller mode, which will often fix this issue. If there isn’t one, either make your own custom button handler, or implement the Button interface.

Very rarely, the POV (known as a D-pad) is used.
Use:

.. code-block:: java

	getPOV(int povNumber)

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents:
	
	