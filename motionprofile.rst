.. Team 573 Programming Documentation documentation master file, created by
   sphinx-quickstart on Thu Sep 13 19:58:13 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Motion Profiling Of A FRC Drivebase
==============================================================

Why Should I Care?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
In many past games the autonomus modes have required robots to move on the play field in order to score or better position themseleves. While dead reckoning (saying move forward for 5 seconds, then do X for 2 more second, etc...) has worked ok in the past, the use of sensor allows for more repeatable auto modes. The team in 2018 was able to sucessfully use enconders on each side of the drive base, along with a gyro to move about the field with relative accuracy. In 2018, P loops where used based on distance still required to move and angle required to reach. Examples of these functions are below. 

Straight Line Distance

.. code-block:: c++

	void Drive::EncoderSetpoint(double setpoint) {

		//Wheel moves 2.17 inches per motor revolution
		//Calculating distance covered by robot through encoder

		double leftEncoderVal = LeftDriveEncoder->Get();
		double rightEncoderVal = RightDriveEncoder->Get();

		double leftDistance = leftEncoderVal * 5 / 963;
		frc::SmartDashboard::PutString("DB/String 2", to_string(leftDistance));
		double rightDistance = rightEncoderVal * 5 / 963;
		frc::SmartDashboard::PutString("DB/String 3", to_string(rightEncoderVal));
		//double avgDistance = (leftDistance + rightDistance) / 2;
		double avgDistance = rightDistance;

		double distanceError = setpoint - avgDistance;
		double kPEncoder = -0.4;
		double output = kPEncoder * distanceError;

		if(output > .75) {

			output = .75;

		} else if(output < -.75) {

			output = -.75;

		}

		//------------------- Gyro stabilization -------------------------------

		double gyroCurrent = MyGyro->GetAngle();
		double gyroError = 0 - gyroCurrent;
		double kPGyro = -0.04;
		double PIDTurn = kPGyro * gyroError;

		frc::SmartDashboard::PutString("DB/String 4", to_string(gyroCurrent));

		if(PIDTurn > .5) {

			PIDTurn = .5;

		} else if(PIDTurn < -.5) {

			PIDTurn = -.5;

		}

		//Hardcoding arcade drive with y and pivot axes
		double leftPower = output + PIDTurn;
		double rightPower = output - PIDTurn;

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

Turn to Angle

.. code-block:: c++

	void Drive::GyroSetpoint(double degrees) {

		double gyroCurrent = MyGyro->GetAngle();
		double gyroError = degrees - gyroCurrent;
		double PIDTurn;
		double kP = -0.015;
		PIDTurn = kP * gyroError;

		frc::SmartDashboard::PutString("DB/String 1", to_string(gyroCurrent));

		if(PIDTurn > .8) {

			PIDTurn = .8;

		} else if(PIDTurn < -.8) {

			PIDTurn = -.8;

		}

		//Hardcoding arcade drive with y and pivot axes
		double leftPower = PIDTurn;
		double rightPower = -1 * PIDTurn;

		LeftDrive->Set(leftPower); //Set left value to left drive
		RightDrive->Set(rightPower); //Set right value to right drive

	}

While this works ok, it requires lots of testing in order to get the paths to work. It also wastes time as each section must be given time to hit its mark. Since the P loops must cover such as large range (0 to 90 degrees for example) they must be tuned such that they cannot move too quickly. To improve this 573 started looking at using motion profiling in the 2019 FRC season. 

Using motion profiling will,

-Smooth Out the Robot Motion

-Allow for the Robot to Move Faster

-Allow for Changes in the Paths Quickly with Minimal Testing

The rest of this page documents the theory behind our method, how we implemented it and some of the issues we ran into.

A great lecture we used to help get our head around this topic was done in 2015 by FRC team 254, https://www.youtube.com/watch?v=8319J1BEHwM the flowchart below is from that presentation.

.. image:: /_static/Flowpath.JPG

Path Planning / Waypoints
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Path planning is simply figuring out where you want to go from where you currently are. While this can be very complicated for unknown environments, a FRC field is in a known state at the beginning of each macth. So to plan your path you must decide where you're starting, what points you want to go to and in what oreintation. 

To do this you can get a layout of the field with X and Y coordinates and plot out the path(s) you want.

Then you connect the dots. Generally using a cubic or quartic function. If you want to know how to do this you can talk to Coach Eric, but the motion profile generator discussed in the Trajectory Generation section does this for you.

In 2020 and 2021, we generated a path using a simple java program and ran the outputs through a python script which created a text file we could copy and paste into the code with the correct data our function required. 


Trajectory Generation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
You create a trajectory from your path. Basically you are using the path and physical constraints of your robot, such as peak velocity, peak acceleration, and drive base type/size. You apply wheel velocities so the robot follows the path. This creates a trajectory, or a table of the speeds required for each wheel at each point in time. This table will be used to follow the path in our feed forward controller.

Based on the work done in 2021 season, we've taken a good generator from online, and added some custom code. This way the output is in the format we want. It also allows for us to handle loops in the heading. Its located https://github.com/savage301/Motion_Profile_Generator

The output from this can be input into the Talon SRX motion profiling, or into our own code.

Follow the Path
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The next step is to have the robot follow the path. To do this we need well timed loop, since our trajectory table is based upon time. You should use TimedRobot and make sure your cycle time matches the one used for the table. 

Here is one of the paths we used last year:

.. image:: /_static/pathplan.PNG

Feed Forward Control
-------------------------------------------------------------
In order to determine the output to send to the motors we use a feed forward control loop on each wheel

Output = F + PID (If possible try with just P term)

Output = Kv *(Expected Velocity) + Kp*(Current Position - Expected Position)

Then adjust it to account for heading error, to get left and right side drive values.

Output_Left = Output + Kh * (Current Heading - Expected Heading)

Output_Right = Output - Kh * (Current Heading - Expected Heading)

We get the current velocity and position from the wheel encoders. The expected values we can look up from our trajectory table.

Robot Kinematics
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
This is assumed by the trajectory generator we used above. So unless your instrested in the theory behind it you can skip this section.

In order to create motion profiles we must first describe the system, in our case a FRC robot with 6 wheel tank drive. Since there is a drop center, we assume a 2 wheel differental drive robot in our inital model. Since the robot stays on a flat floor, we can assume the field as a 2D plane. We used the information in the paper in the link below to determine the forward kinematics of the robot.

http://www8.cs.umu.se/kurser/5DV122/HT13/material/Hellstrom-ForwardKinematics.pdf

Some important items from the paper.

-For a robot on a two dimensional surface, the 2D pose (x,y,θ), where θ denotes the heading, is sufficient to describe its motion.

-For a robot with differential drive, the direction of motion is controlled by separately controlling speeds vl and vr of the left and right wheels respectively.

-The forward kinematics equations for a robot (or other vehicle) with differential drive are used to solve the following problem:
Standing in the pose (x, y, θ) at time t, determine the pose (x’, y’, θ’) at time t + δt given the control parameters vl and vr .

The final forward kinematics were determined to be,

.. image:: /_static/ForwardKinematics.JPG

The forward kinematics allow us to calculate the new position and orientation (pose) of the robot, if the previous pose and wheel encorder counts were known.

Current State of Team 573 Motion Profiling
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
During the 2019 season, Team 573 used motion profiling for the first time. You can see in detail how it was done in the 2019 Robot code, but the short version is.

-Generate multiple trajectorys using motion profile generation found, https://github.com/vannaka/Motion_Profile_Generator a head of time.

-Hard code those trajectorys in code.

-Lookup wheel position and velocities from trajectorys as the robot goes through the motion. 

-Use a PID loop to control robot to follow trajectory.

During the 2020/2021 seasons, Team 573 used motion profiling again with greater sucess particularly in the at home autonomous challenges. You can see in detail how it was done in the 2021 Robot code, but the short version is.

-Generate trajectorys using motion profile generation found, https://github.com/vannaka/Motion_Profile_Generator a head of time.

-Run those trajectorys through team built Python Code to convert the heading to account for continous gyro and when robots need to go backwards.

-Hard code those trajectorys in code.

-Lookup wheel position, velocities and robot heading from trajectorys as the robot goes through the motion. 

-Use a Velocity feedforward P loop to control robot to follow trajectory.

Video of 2021 Auto Mode

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <iframe src="https://www.youtube.com/embed/ySGWh0Dqs-A" frameborder="0" allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe>
    </div>

.. toctree::
   :maxdepth: 2
