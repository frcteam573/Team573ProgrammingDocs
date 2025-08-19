.. Team 573 Programming Documentation documentation master file, created by
   sphinx-quickstart on Thu Sep 13 19:58:13 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

FRC Robot Code
==============================================================
This section goes over tips and how to's for command based Python robot code specifically for FRC robot.

Stock Code Structure
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The FIRST Robotics Competition provides a code structure that must be used. The main file is called Robot.py. Within that file there are different functions that run during particular parts of each match. 

* note: "init" stands for initialization * 

First is the robot init function. This portion of the code runs as soon as the robot is turned on. It doesn't run again until the robot is power cycled again. 

Next is the robot periodic function. This portion of the codes run every loop that the robot is on, and in any state. This is where you should put any code that needs to run all the time, such as reading sensors or updating the dashboard.

The next function is the autonomous init section. This portion of the code runs as soon as the autonomous mode is enabled. It doesn't run again until autonomous mode is disabled and enabled again. After that comes autonomous periodic. This portion of the code runs after autonomous init finishes running, and it constantly loops through itself the entire time autonomous mode is enabled. Here is where you need to put all of the functions you will run during the autonomous period.

The last two functions are teleop init and teleop periodic. Teleop init, like the other init sections, runs when teleop is enabled, and doesn't run unless teleop is disabled and enabled again. Teleop periodic, which comes after teleop init, runs after teleop init finishes running and runs the entire time teleop is enabled. Due to how we struture our code, this is generally empty.

How to build and deploy code
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
WPILIB - Robotpy
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Many of the functions we use to control robot actions are part of a library created for FRC called WPILIB. Robotpy is a wrapper used to allow use of WPILIB in Python.

The help for it can be found here. (https://robotpy.readthedocs.io/en/stable/)

This is a great resource and the examples in the sections below use fucntions from this library.

Command Based Programming - Subsystems
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Subsystems in code are generally similar to subsystems on the robot. In code it is a collection of motors, sensors and other components that work together to perform a specific function.
For example, a drivetrain subsystem would include the motors that drive the wheels, the encoders that measure distance and speed, and the gyro that measures angle.
Each subsystem can only have at most 1 command scheduled for it to run every loop. This means that commands are designed for specific subsystems.

An example of a subsystem is below. It is an elevator subsystem that has a motor and an encoder to measure distance.

.. code-block:: python
	""" File defines the Elevator subsystem for the robot."""
	import math

	import commands2
	import wpilib
	import wpilib.drive
	import rev
	from rev import SparkFlex, SparkClosedLoopController, SparkBase, SparkFlexConfig
	import config
	from wpilib import SmartDashboard
	import constants
	from toolkit_commands.utils.common_functions import max_min_check

	class Elevator(commands2.SubsystemBase):

		def __init__(self) -> None:
			super().__init__()
			# Setups the elevator subsystem with a motors, encoders, and a PID controller.
			self.m_elevator2 = rev.SparkFlex(62, rev.SparkFlex.MotorType.kBrushless)
			self.m_elevator1 = rev.SparkFlex(61, rev.SparkFlex.MotorType.kBrushless)        
			self.s_elevator_encoder = self.m_elevator1.getEncoder()
			self.pid_controller = self.m_elevator1.getClosedLoopController()
			self._set_config() # Sets the configuration for the elevator motors.
			
		def getElevatorEncoderPos(self):
			# Returns the current position of the elevator encoder.
			return self.s_elevator_encoder.getPosition()
		
		def changeElevatorMode(self,state):
			# Changes the elevator mode and updates the SmartDashboard.
			constants.cachedShooterMode = state
			wpilib.SmartDashboard.putBoolean("Mode",constants.cachedShooterMode)
			

		def setElevatorSpeed(self, speed: float) -> bool:
			'''Sets the speed of the elevator motors.
			
			Args:
				speed: The speed to set the motors to, -1 to 1.
				
			'''
			#Checks if the speed is within the range of the elevator encoder, if not it sets it to 0.
			speed = max_min_check(self.s_elevator_encoder,speed,config.elevator.elevator_minimum,config.elevator.elevator_maximum)

			#Set elevator motors to the given speed.
			self.m_elevator2.set(-speed)
			self.m_elevator1.set(speed)


		def setElevatorposition(self, pos: float) :   
			# Sets the elevator position using a PID controller.
			self.pid_controller.setReference(pos, SparkBase.ControlType.kPosition) # Sets elevator desired position to PID controller

			#Checks if the elevator is at the desired position and returns True if it is, otherwise returns False.
			if abs(self.s_elevator_encoder.getPosition()-pos) < config.elevator_inPos_Threshold:
				return True
			else:
				return False

		def _set_config(self):
			# Configures the elevator motors with PID settings and soft limits.
			configSM = SparkFlexConfig() # Create config for the elevator motor
			configPID = configSM.closedLoop # Create PID config for the elevator motor

			#Add soft position limits for the elevator motors
			configSM.softLimit.forwardSoftLimit(config.elevator.elevator_maximum)
			configSM.softLimit.reverseSoftLimit(config.elevator.elevator_minimum)
			
			configSM.smartCurrentLimit(50) # Set the current limit for the elevator motor


			# Set the PID settings for the elevator motor
			configPID.P(config.elevator.kP)
			configPID.I(config.elevator.kI)
			configPID.D(config.elevator.kD)
			configPID.velocityFF(config.elevator.kF)
			configPID.outputRange(config.elevator.reverse_output_limit,config.elevator.forward_output_limit)
			
			#Configure the first elevator motor with the config
			self.m_elevator1.configure(configSM,SparkBase.ResetMode.kResetSafeParameters,
						SparkBase.PersistMode.kPersistParameters)
			

			# Configure the second elevator motor to follow the first one
			configSM2 = SparkFlexConfig()
			configSM2.follow(self.m_elevator1.getDeviceId(),True)
			self.m_elevator2.configure(configSM2,SparkBase.ResetMode.kResetSafeParameters,
						SparkBase.PersistMode.kPersistParameters)

In the init section the motors and sensors for the subsystem and defined.
The rest of the function are used to control the subsytem and are called in the commands for the subsystem.

Command Based Programming - Commands
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Commands are the actions that the robot can perform. They are generally used to control a specific subsystem, such as moving an elevator or driving a drivetrain.
As shown in the example below, commands have sections the 3 that are required are initialize (run once each time the command is started), execute (run everytime the command is scheduled), and end (run once when the command is ended). Other optional functions are isFinished (when returns True the command is ended) and interrupted.
.. code-block:: python
	import typing
	import commands2
	import wpilib

	from oi.keymap import Keymap

	# from commands.wrist import Wrist

	from subsystems.elevator import Elevator     
	import config 
	import constants

			
	class setElevatorPosition(commands2.Command):
		#Commands elevator to move to a specific position.
		def __init__(
			# This defines what subsystem this command is for, so it can be used in the command scheduler.
			self,
			app: Elevator,
			setPoint: 0,
		) -> None:
			super().__init__()
			self.app = app
			self.addRequirements(app)
			self.setPointin = setPoint # This is the setpointin for the elevator to move to, it can be L1, L2, L3, or L4.
			self.setPoint = 0
			
		def initialize(self):
			#This is run when the command is first run.
			self.wrist_passed_FSP = False #This is used to check if the wrist has passed the clearspace for the elevator to move.
			
		def execute(self):
			'''Called every time the command is scheduled to run.'''
			# This match is used to translate the setPointin to the actual setpoint for the elevator to move to.
			match self.setPointin:
				case "L1":
					if constants.cachedShooterMode:
						self.setPoint = config.elevator.coral_L1
					else:
						self.setPoint = config.elevator.algae_processor       
				case "L2":
					if constants.cachedShooterMode:
						self.setPoint = config.elevator.coral_L2
					else:
						self.setPoint = config.elevator.algae_L2
				case "L3":
					if constants.cachedShooterMode:
						self.setPoint = config.elevator.coral_L3
					else:
						self.setPoint = config.elevator.algae_L3
				case "L4":
					if constants.cachedShooterMode:
						self.setPoint = config.elevator.coral_L4
					else:
						self.setPoint = config.elevator.algae_barge
			
			# This is used to not allow the elevator to move up if their is not  a coral in the intake.
			if constants.cachedShooterMode and config.current_ele_pos > -2 and not wpilib.SmartDashboard.getBoolean('Coral In', True):
				self.app.setElevatorposition(config.elevator.coral_L1)
			else:
				if config.wrist_in_zone or self.wrist_passed_FSP:
					self.wrist_passed_FSP = True
					config.current_ele_pos = self.app.getElevatorEncoderPos() #Update current elevator position.
					in_pos = self.app.setElevatorposition(self.setPoint) # Sets the elevator position to the setpoint.
					return in_pos #Returns True if the elevator is at the desired position, otherwise returns False.

				else:
					self.app.setElevatorSpeed(0) # Stops the elevator motors if the wrist is not safe position
		
		def end(self, interrupted=False) -> None:
			#This is run when the command is finished.
			self.app.setElevatorSpeed(0)

Controller Inputs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For FRC we generally use two XBOX controllers to allow for our driver and operator to control the robot. However other controllers, such as joysticks, or individaul buttons can be used.

In order to use them they must be plugged into our Driver Station laptop via a usb port, and given a controller number. This number can be easily changed on the driver station at anytime, so its good to double check that they are correct before using them.

Due to the command based structure we bind or link buttons to specific commands. This is generally done in our Keymap.py and OI.py files.

In Keymap.py

.. code-block:: python

	""" This creates buttons map for the controllers"""
	import commands2
	import wpilib
	from utils.oi import (
		JoystickAxis,
		XBoxController,
	)

	# -- Create controllers --
	controllerDRIVER = XBoxController
	controllerOPERATOR = XBoxController

	class Controllers:
		DRIVER = 0
		OPERATOR = 1

		DRIVER_CONTROLLER = wpilib.Joystick(0)
		OPERATOR_CONTROLLER = wpilib.Joystick(1)
	#-- Create keymap class --
	class Keymap:
		class Elevator:
			setLevel3 = commands2.button.JoystickButton(Controllers.DRIVER_CONTROLLER, controllerDRIVER.Y)

		class Drivetrain:
			followPath = commands2.button.JoystickButton(Controllers.DRIVER_CONTROLLER, controllerDRIVER.A)


In OI.py

.. code-block:: python

	import commands.elevator
	import commands.drivetrain

	import commands
	import config
	from oi.keymap import Controllers, Keymap

	import constants
	from robotcontainer import Robot
	import robotcontainer

	class OI:
	
		@staticmethod
		def init() -> None:
			pass

		@staticmethod
		def map_controls():
			pass

	# Below is the mapping used to call commands based on user joystick input.		
	Keymap.Elevator.setLevel3.whileTrue(commands.elevator.setPosition(Robot.elevator,position=10))

We have class for XBoxController which allows the code to just use button names instead of needing to know the specific port for each button.
.. image:: /_static/XBoxControlMapping.jpg


Sensor Inputs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
On each robot we (the programming team) pushes for sensors to be included to help us control the robot. 
It is best to have a rough idea of what sensors we will need while the mechanical team is designing the robot. 
Since the programming team wants the sensors it's our job to make sure they get included in the design so they can be used properly.

At a minimum we should be asking for:
	- One encoder per independent drive wheel
	- A gyro for the robot (Placed as close to where the robot turns about as possible.)

In the past we have also used:
	- Encoders on motors which control arms
	- Encoders on motors which control wheel speed for shooting robots.
	- Camera to track objects
	- Ultrasonic sensors to measure distance
	- Limit switchs (Light gates and press types)

While it maybe tempting to assume that if we have a sensor we can control a mechanism, this is not always the case. A mechanism's mechanical properties greatly influence its controlability. As the programming team it is important to raise any controlability concerns with the mechanical team. 

An example from this team. In 2016, the team made a shooter that moved up and down on a pivot to aim before shooting. While there was a camera that could tell the arm to angle up or down to align with the goal, the way the motor and counter balance were designed the arm would bounce when it was roughly at 45 degrees. This was because the gas springs (counter balance) would overcome gravity at that point. This meant that there was no way to keep the arm steady around 45 degrees to shoot. This forced the team to change the distance it would shoot from and put extra stress on the motors. If you want more information about this you can talk to Coach Eric, Coach Lisa or Coach Marc who were all on the team then.

.. image:: /_static/2016_Robot.jpg

Encoders
---------------------------------------------------
An encoder measures the rotational speed and rotations of something. Luckily for brushless motor an encoder is already built into the motor.

.. code-block:: python 

	self.m_elevator1 = rev.SparkFlex(61, rev.SparkFlex.MotorType.kBrushless)        
    self.s_elevator_encoder = self.m_elevator1.getEncoder()



Gyros
---------------------------------------------------
A gyro measures angle and angluar velocity. We use it to help keep the robot driving in a straight line. One thing to watch out for with gyros is drift. Over time a stationary gyro's output will increase. 
With that said the ones we use on the team are generally good enough for 2+ minutes without having to reset it. 


Digital Inputs
---------------------------------------------------
A digital input is any sensor that returns a on or off response. This includes buttons, limit switchs, and lightgates.

They can all get coded for in the same way even if they physically look different.

A basic example of how to include a digital input in your code.

Defining lightgate

.. code-block:: python

	self.s_claw_lightgate2 = wpilib.DigitalInput(1) # Claw Lightgate sensor


Calling it in subsystem

.. code-block:: python

	if self.s_claw_lightgate2.get() == True: # This check if lightgate is not blocked else it stops intaking coral.
            speed = speed*.5
    else: 
        speed = 0
        coralIn = True
    self.m_claw.set(speed) #Sets the speed of the claw motor.


Control Loops
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Sensors allow us to automate robot functions to make it easier to drive and/or harder to destory. While we can do quite a bit with if statements such as,

if this sensor is true
	do this
else 
	don't do this

It doesn't help us much when we want to do more complicated things such as make a robot drive forward for a certain distance. But with control loops we can get a robot to behave a lot more like a human would.
Speed up when we are far away and slow down as we approach. All with a few lines of code and bit of math.

Proportional Control
-------------------------------------------------------------------

Imagine you have a robot and you've been asked to make it drive forward 15 feet and stop. Luckily you have an encoder that outputs in feet already on the robot so all you need is to set up the logic.

Before we code anything let look at the situation. Your robot is currently 15 feet away from where is needs to be. This gap distance is the error between where you are and where you want to be.

Error = Desired position - Current Postition

As you drive forward your error will get smaller and smaller as you approach 15 ft. (It will also turn negative if you pass 15 ft)

So this error follows the same pattern we want the robot's speed to follow (Large (fast) far away and small (slow) when its close) So its a good candaite to help us control the robot.

But we cannot just set the motor value, with a range of -1 to 1, to the error value, which could be anything. We need a scale factor to transform this error into a value we can send to the motor controller. 
Let's call this value Kp. So our motor value is

Motor Value = Kp * Error

This is called a proportional control loop because you're controlling your system based propotionally to the current system error.

In many cases this is good enough, but in certain cases we many need to look at additional ways to control the system.

Intergral Control
-------------------------------------------------------------------
Imagine in our situtation our proportional controller gets us to 14.5 ft, but we need it to get a little bit closer to 15. While our factor Kp works great to get us close once the error gets below 0.5 ft the output is so small the robot doesn't move.

To get over this we can use a intergral controller. It is structured similar to a proportional controller except we look at accumulated error instead of current error.

Acculated Error = Sum over time(Desired position - Current Position)

Motor Value = Ki * Acculated Error

In the real world, use we generally limit the time over which the error is accumlated (for example the last 10 samples) otherwise it would get so big that it would go out of control. 

Deriviative Control
-------------------------------------------------------------------
Now imagine another situation where our proportional controller causes us to over shoot 15 ft to 16 ft then back to 14ft over and over again. This oscillation happens because the change in error is so rapid that the motor output cannot change quickly enough to react to it, so it over shoots.

To help correct this we can use a derivative controller. It is structured similarly to a proportional controller except we look at the change in error instead of current error.

Change in Error = (Desired position - Current Position)t=0 - (Desired position - Current Position)t=-1

Motor Value = Kd * Change in Error

Combined Controller
-------------------------------------------------------------------
Depending on our system we can use any combination of these three types of controllers. A controller that includes all three is called a PID controller can looks like.

Motor Ouptut = Kp * Error + Ki * Acculated Error + Kd * Change in Error

Just a reminder that Kp, Ki and Kd can be negative and are "tuned" depending upon the system your trying to control. Generally in FRC we just guess and check these values until the system behaves like we want.

How to Use This
-------------------------------------------------------------------
Motor contorllers in FRC have built in PID controllers that we can use. The example below shows how to set up and use a PID controller for an elevator subsystem.

.. code-block:: python

	# In the init section of the subsystem
	self.pid_controller = self.m_elevator1.getClosedLoopController()
	
	# In a function to set the position of the elevator
	self.pid_controller.setReference(pos, SparkBase.ControlType.kPosition) # Sets elevator desired position to PID controller

Vision with Limelight Camera
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
While a camera is just another type of sensor there are alot of settings with it so it deserves it own section.

In the past, we used a limelight camera which is a self contained vision system that does all the vision processing on board and just sends numbers back to the RoboRio.

The limelight documentation can be found here.(http://docs.limelightvision.io/en/latest/) It is really good and there are some great examples in there of how to set up the camera and use it.

In order for the code below to function correctly the camera must be setup with the correct team number and ip settings as desrcibed in the limelight documentation. (link above)

Once these values are read in they can be used in control loops just like any other sensor value.

Vision with Photon Vision
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
We've also used Photon Vision for vision processing. It is a bit more complicated to set up, but it allows for more flexibility in camera choice and processing power.

The Photon Vision documentation can be found here.(https://docs.photonvision.org/en/latest/) It is really good and there are some great examples in there of how to set up the camera and use it.


.. toctree::
   :maxdepth: 2



