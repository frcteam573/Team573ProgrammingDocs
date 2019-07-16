.. Team 573 Programming Documentation documentation master file, created by
   sphinx-quickstart on Thu Sep 13 19:58:13 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

FRC Robot Code
==============================================================
This section goes over tips and how to's for C++ robot code specifically for FRC robot.

Stock Code Structure
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
In Robot.cpp, there are different sections of the code that have different functions when we run them. 

* note: "int" stands for initialization * 

First is the robot int section. This portion of the code runs as soon as the robot is turned on. It doesn't run again until the robot is power cycled again. 

The next section that comes is the autonomous int section. This portion of the code runs as soon as the autonomous mode is enabled. It doesn't run again until autonomous mode is disabled and enabled again. After that comes autonomous periodic. This portion of the code runs after autonomous int finishes running, and runs the entire time autonomous mode is enabled. It contains all of the functions you will run or choose to run during the autonomous period.

The last two sections are teleop int and teleop periodic. Teleop int, like the other int sections, runs when teleop is enabled, and doesn't run unless teleop is disabled and enabled again. Teleop periodic, which comes after teleop int, runs after autonomous int finishes running and runs the entire time teleop is enabled. It contains all of the functions you will run or choose to run during the teleop period.

How to build and deploy code
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
WPILIB
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Many of the functions we use to control robot actions are part of a library created for FRC called WPILIB.

The help for it can be found here. (http://first.wpi.edu/FRC/roborio/release/docs/cpp/)

This is a great resource and the examples in the sections below use fucntions from this library.


Controller Inputs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For FRC we generally use two XBOX controllers to allow for our driver and operator to control the robot. However other controllers, such as joysticks, or individaul buttons can be used.

In order to use them they must be plugged into our Driver Station laptop via a usb port, and given a controller number. This number can be easily changed on the driver station at anytime, so its good to double check that they are correct before using them.

In order to read in some inputs from our XBOX controller we can use the following code.

In RobotMap.h

.. code-block:: c++

	#pragma once
	#include <WPILib.h>

	constexpr int Driver1 = 0; //Defining USB input 0


In Robot.cpp

.. code-block:: c++

	#include <WPILib.h>
	#include "RobotMap.h"
	#include <Joystick.h>

	class Robot : public frc::TimedRobot {
	public:


		frc::Joystick controller1{ Driver1 };  // Xbox controller 1

		void TeleopPeriodic() override {

			double leftin = controller1.GetRawAxis(1); //Get Drive Left Joystick Y Axis Value
			double rightin = controller1.GetRawAxis(5); //Get Drive right Joystick Y Axis Value
			bool AButton = controller1.GetRawButton(1); //Get A button Value (True or False)

		}
	}

The values used in the GetRawAxis and GetRawButton functions are based upon the following button map for the XBox controller. This image is wrong for axis. They start at 0, but follow the same ordering.

.. image:: /_static/XBoxControlMapping.jpg

Motor Outputs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Every year we have lots of different motor types on our robot, luckily most of them can be controlled using the same function.

Below is a simple example of how to map joystick inputs directly to drive motor outputs. This is a simple version of tank drive which we used on the 2018 robot.
While everything is done within the Robot.cpp file in the example normally we create several subsystem files, such as Drive.cpp to store functions that we call from Robot.cpp
To see an example of this see the 2018 robot code repo.

In RobotMap.h

.. code-block:: c++

	#pragma once
	#include <WPILib.h>

	constexpr int Driver1 = 0; //Defining USB input 0
	constexpr int LeftDrivePWM = 0; // Defining Left Drive PWM is in RoboRio Port 0
	constexpr int RightDrivePWM = 1; // Defining Left Drive PWM is in RoboRio Port 1

In Robot.cpp

.. code-block:: c++

	#include <WPILib.h>
	#include <RobotMap.h>
	#include <Joystick.h>
	#include <Talon.h>

	class Robot : public frc::TimedRobot {
	public:

		frc::Joystick controller1{ Driver1 };  // Xbox controller 1
		frc::Talon LeftDrive{ LeftDrivePWM }; //Defining a Talon controller called LeftDrive
		frc::Talon RightDrive{ 	RightDrivePWM }; //Defining a Talon controller called RightDrive


		void TeleopPeriodic() override {

			double leftin = controller1.GetRawAxis(1); //Get Drive Left Joystick Y Axis Value
			double rightin = controller1.GetRawAxis(5); //Get Drive right Joystick Y Axis Value

			LeftDrive.Set(leftin); //Set left value to left drive
			RightDrive.Set(rightin); //Set right value to right drive

		}
	}

Important note - Once you set a motor output value it will keep it until it is changed again. Which means if you want a motor to stop you have to set it equal to zero otherwise it will keep going.

Pnuematic Outputs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The team has in the past used a pnematic system on the robot on certain mechanisms. Including grippers, brakes, and shifting transmissions.
While these actuate systems similar to motors, they are controlled differently since they only have two positions (extended or retracted)

In RobotMap.h

.. code-block:: c++

	#pragma once
	#include <WPILib.h>

	constexpr int Driver1 = 0; //Defining USB input 0
	constexpr int ShifterPort1 = 4; // Defining Shift port number on PCM
	constexpr int ShifterPort2 = 5; // Defining Shift port number on PCM

	DoubleSolenoid * Shifter; // Defining a DoubleSolenoid named Shifter
	constexpr int PCM = 1; // Defining PCM CAN ID

In Robot.cpp

.. code-block:: c++

	#include <WPILib.h>
	#include "RobotMap.h"
	#include <Joystick.h>

	class Robot : public frc::TimedRobot {
	public:

		frc::Joystick controller1{ Driver1 };  // Xbox controller 1

		Shifter = new DoubleSolenoid(PCM, ShifterPort1, ShifterPort2); // Setting shifter ports and PCM CAN ID

		void TeleopPeriodic() override {

			bool button = controller1.GetRawButton(1); //Get A button Value (True or False)
			
			if (button){
				Shifter->Set(DoubleSolenoid::Value::kReverse); // Setting shifter to reverse position
			}
			else {
				Shifter->Set(DoubleSolenoid::Value::kForward); // Setting shifter to forward position
			}

		}
	}

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

Encoders
---------------------------------------------------
An encoder measures the rotational speed and rotations of something. We use them mainly on drivetrains to measure distance and appendages to measure speed and/or distance.


In RobotMap.h

.. code-block:: c++

	#pragma once
	#include <WPILib.h>
	#include <Encoder.h>

	Encoder * LeftDriveEncoder; //Define a new encoder called LeftDriveEncoder


In Robot.cpp

.. code-block:: c++

	#include <WPILib.h>
	#include "RobotMap.h"
	#include <Encoder.h>

	class Robot : public frc::TimedRobot {
	public:

		LeftDriveEncoder = new Encoder(0, 1, false, Encoder::k4X); //Define ports (0 and 1) for LeftDriveEncoder, fasle to not reverse direction and encoding type of k4x


		void TeleopPeriodic() override {

			double leftEncoderVal = LeftDriveEncoder->Get(); // Get current rotation count from LeftDriveEncoder

			double leftDistance = leftEncoderVal * 5 / 963; // Convert rotation count to a physical distance. This depends upon the robot itself.

			frc::SmartDashboard::PutString("DB/String 2", to_string(leftDistance)); // Write out values to dashboard field DB/String 2, this is useful for debugging.

		}
	}

Other useful commands to use with the Encoder are

LeftDriveEncoder->Reset(); // Resets number of rotations to zero.



Gyros
---------------------------------------------------
A gyro measures angle and angluar velocity. We use it to help keep the robot driving in a straight line. One thing to watch out for with gyros is drift. Over time a stationary gyro's output will increase. 
With that said the ones we use on the team are generally good enough for 15 seconds without having to reset it back to zero. 

A basic example of how to include a gryo in your code.

In RobotMap.h

.. code-block:: c++

	#pragma once
	#include <WPILib.h>
	#include <ADXRS450_Gyro.h>


In Robot.cpp

.. code-block:: c++

	#include <WPILib.h>
	#include "RobotMap.h"
	#include <ADXRS450_Gyro.h>

	class Robot : public frc::TimedRobot {
	public:
		frc::ADXRS450_Gyro MyGyro{}; //Defines MyGyro no port is need since this gyro type is assumed to plug into a specific port in the RoboRio.


		void TeleopPeriodic() override {

			double gyroval = MyGyro.GetAngle(); // Get current angle from gyro called MyGyro

			frc::SmartDashboard::PutString("Gyro", to_string(gyroval)); // Write out values to dashboard field Gyro, this is useful for debugging.

		}
	}

Other useful commands to use with the Encoder are

MyGyro.Reset(); // Resets current angle to zero.

Digital Inputs
---------------------------------------------------
A digital input is any sensor that returns a on or off response. This includes buttons, limit switchs, and lightgates.

They can all get coded for in the same way even if they physically look different.

A basic example of how to include a digital input in your code.

In RobotMap.h

.. code-block:: c++

	#pragma once
	#include <WPILib.h>

	constexpr int BoxlightgateDIO = 6;

	DigitalInput * Boxlightgate; //Define a new digital input called Boxlightgate


In Robot.cpp

.. code-block:: c++

	#include <WPILib.h>
	#include "RobotMap.h"


	class Robot : public frc::TimedRobot {
	public:

		Boxlightgate = new DigitalInput(BoxlightgateDIO); //Defines Boxlightgate to use DIO port 6 on RoboRio


		void TeleopPeriodic() override {

			bool lightgatebool = Boxlightgate->Get(); // Get current status of Boxlightgate (True or False)

			frc::SmartDashboard::PutBoolean("Box in Robot", lightgatebool); // Write out values to dashboard field Box in Robot, this is useful for debugging.

		}
	}


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

To get over this we can use a intergral controller. It is structured very similarily to a proportional controller except we look at acculated error instead of current error.

Acculated Error = Sum over time(Desired position - Current Position)

Motor Value = Ki * Acculated Error

In actual use we generally limit the time over which the error is accumlated otherwise it would get so big that it would go out of control. 

Deriviative Control
-------------------------------------------------------------------
Now imagine another situation where our proportional controller causes us to over shoot 15 ft to 16 ft then back to 14ft over and over again. This oscillation happens because the change in error is so rapid that the motor output cannot change quickly enough to react to it, so it over shoots.

To help correct this we can use a derivative controller. It is structured very similarily to a proportional controller except we look at the change in error instead of current error.

Change in Error = (Desired position - Current Position)t=0 - (Desired position - Current Position)t=-1

Motor Value = Kd * Change in Error

Combined Controller
-------------------------------------------------------------------
Depending on our system we can use any combination of these three types of controllers. A controller that includes all three is called a PID controller can looks like.

Motor Ouptut = Kp * Error + Ki * Acculated Error + Kd * Change in Error

Just a reminder that Kp, Ki and Kd can be negative and are "tuned" depending upon the system your trying to control. Generally in FRC we just guess and check these values until the system behaves like we want.

How to code a P loop
-------------------------------------------------------------------
Back to code. Here is an example of how to get a robot to drive straight with the aid of a gyro. It uses a P controller with some limiters to make sure the system stays in control.

In RobotMap.h

.. code-block:: c++

	#pragma once
	#include <WPILib.h>
	#include <ADXRS450_Gyro.h>

	ADXRS450_Gyro * MyGyro; //Define a new gyro called MyGyro

	constexpr int Driver1 = 0; //Defining USB input 0
	constexpr int LeftDrivePWM = 0; // Defining Left Drive PWM is in RoboRio Port 0
	constexpr int RightDrivePWM = 1; // Defining Left Drive PWM is in RoboRio Port 1

	Talon * LeftDrive; //Defining a Talon controller called LeftDrive
	Talon * RightDrive; //Defining a Talon controller called RightDrive

In Robot.cpp

.. code-block:: c++

	#include <WPILib.h>
	#include "RobotMap.h"
	#include <ADXRS450_Gyro.h>
	#include <Joystick.h>

	class Robot : public frc::TimedRobot {
	public:

		MyGyro = new ADXRS450_Gyro(); //Defines MyGyro no port is need since this gyro type is assumed to plug into a specific port in the RoboRio.
		frc::Joystick controller1{ Driver1 };  // Xbox controller 1

		void TeleopPeriodic() override {

			//Hardcoded P function
			double y = controller1.GetRawAxis(1); //Get Drive Left Joystick Y Axis Value
			double gyroCurrent = MyGyro->GetAngle(); // Get current Gyro angle
			double gyroError = 0 - gyroCurrent; // Calculate gyro error assuming we want to be at angle 0
			double kPGyro = -0.04; // Hardcoded Kp value.
			double PIDTurn = kPGyro * gyroError; // Determine turn value needed to be added to controller to get it to go straight.
		

			// Limiting the PIDTurn value so it doesn't wash out forward and backward control of the robot
			if(PIDTurn > .8) {
		
				PIDTurn = .8;
		
			} else if(PIDTurn < -.8) {
		
				PIDTurn = -.8;
		
			}

			// Limiting the forward and backward value so it doesn't wash out PID turn control of the robot
			if(y > .75) {
		
			y = .75;
		
			} else if(y < -.75) {
		
			y = -.75;
		
			}

			//Hardcoding arcade drive with y and pivot axes
			double leftPower = (y + PIDTurn);
			double rightPower = (y - PIDTurn);

			// Limiting left and right drive train power settings.
			if (leftPower > .9){
				leftPower = .9;
			}
			else if (leftPower < -.9){
				leftPower = -.9;
			}

			if (rightPower > .9){
				rightPower = .9;
			}
			else if (rightPower < -.9){
				rightPower = -.9;
			}
			LeftDrive->Set(leftPower); //Set left value to left drive
			RightDrive->Set(rightPower); //Set right value to right drive

		}

		}
	}

Vision with Limelight Camera
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
While a camera is just another type of sensor there are alot of settings with it so it deserves it own section.

Last year we used a limelight camera which is a self contained vision system that does all the vision processing on board and just sends numbers back to the RoboRio.

The limelight documentation can be found here.(http://docs.limelightvision.io/en/latest/) It is really good and there are some great examples in there of how to set up the camera and use it.

Below is the code we used in order to get values from the limelight last year its a little different from the code provided on their website.

In order for the code below to function correctly the camera must be setup with the correct team number and ip settings as desrcibed in the limelight documentation. (link above)

.. code-block:: c++

	//------------------------ vision read-in -----------------------------

		std::shared_ptr<NetworkTable> table =  NetworkTable::GetTable("limelight"); // Setup Network Table
		table->PutNumber("ledMode",1); // Turn off the LEDs
		table->PutNumber("pipeline",4); // Select the proper vision pipeline

		// Read in values about the target.

		float targetOffsetAngle_Horizontal = table->GetNumber("tx",0); // X location in frame relative to center
		float targetOffsetAngle_Vertical = table->GetNumber("ty",0); // y location in frame relative to center
		float targetArea = table->GetNumber("ta",0); // Target Area
		float targetSkew = table->GetNumber("ts",0); // Target Skew
		float targetExists = table->GetNumber("tv",0); // Is there a valid target



Once these values are read in they can be used in control loops just like any other sensor value.



.. toctree::
   :maxdepth: 2



