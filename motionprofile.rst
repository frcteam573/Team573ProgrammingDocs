.. Team 573 Programming Documentation documentation master file, created by
   sphinx-quickstart on Thu Sep 13 19:58:13 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Motion Profiling Of A FRC Drivebase
==============================================================

Why Should I Care?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
In many past games the autonomus modes have required robots to move on the play field in order to score or better position themseleves. While dead reckoning (saying move forward for 5 seconds, then do X for 2 more second, etc...) has worked ok in the past, the team in 2018 was able to sucessfully use enconders on each side of the drive base, along with a gyro to move about the field with relative accuracy. In 2018, P loops where used based on distance still required to move and angle required to reach. Examples of these functions are below. 

While this works ok, it requires lots of testing, in order to get the paths to work. It also wastes time as each section must be given time to hit its mark, since the P loops must cover such as large range (0 to 90 degrees for example) they must be tuned such that they cannot move too quickly. To improve this 573 started looking at using motion profiling in the 2019 FRC season. 

Using motion profiling will,

-Smooth Out the Robot Motion
-Allow for the Robot to Move Faster
-Allow for Changes in the Paths Quickly with Minimal Testing

The rest of this page documents the theory behind our method, how we implemented it and some of the issues we ran into.

A great lecture we used to help get our head around this topic was done in 2015 by FRC team 254, https://www.youtube.com/watch?v=8319J1BEHwM the flowchart below is from that presentation.

.. image:: /_static/Flowpath.JPG

Path Planning / Waypoints
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Figure out what points you want to go to and in what oreintation. Get a layout of the field with X and Y coordinates. Plot out the path you want.

Trajectory Generation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Apply velocities to path, to trim max velocities and accelerations.

A good generator is online at. https://github.com/vannaka/Motion_Profile_Generator

The output from this can be input into the Talon SRX motion profiling, or into our own.

Follow the Path
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Need well timed loop, use TimedRobot. 

Feed Forward Control
-------------------------------------------------------------

Output = F + PID

Output = Kv *(Current Velocity - Expected Velocity) + PID 

Robot Kinematics
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
This is assumed by the trajectory generator we used above,

In order to create motion profiles we must first describe the system, in our case a FRC robot with 6 wheel tank drive. Since there is a drop center we assume a 2 wheel differental drive robot in our inital model. Since the robot stays on a flat floor we can assume the field as a 2D plane. We used the information in the paper in the link below to determine the forward kinematics of the robot.

http://www8.cs.umu.se/kurser/5DV122/HT13/material/Hellstrom-ForwardKinematics.pdf

Some important items from the paper.

-For a robot on a two dimensional surface, the 2D pose (x,y,θ), where θ denotes the heading, is sufficient to describe its motion.

-For a robot with differential drive, the direction of motion is controlled by separately controlling speeds vl and vr of the left and right wheels respectively.

-The forward kinematics equations for a robot (or other vehicle) with differential drive are used to solve the following problem:
Standing in the pose (x, y, θ) at time t, determine the pose (x’, y’, θ’) at time t + δt given the control parameters vl and vr .

The final forward kinematics were determined to be,

.. image:: /_static/ForwardKinematics.JPG

The forward kinematics allow us to calculate the new position and orientation (pose) of the robot, if the previous pose and wheel encorder counts were known.

.. toctree::
   :maxdepth: 2



