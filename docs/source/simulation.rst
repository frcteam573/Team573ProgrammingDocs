.. Team 573 Programming Documentation documentation master file, created by
   sphinx-quickstart on Thu Sep 13 19:58:13 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Simulation
==============================================================

After 2025 and attempt to add simulation to the code was made. This is housed in 2025 Offseason Repo - Adding SIM to code - (https://github.com/savage301/Team573CTRESwervewithSIM)
This repo has working simulation code for the drivetrain, photonvision and an elevator example. Also has functioning FRC Path Planner integration.

Drivetrain
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The drivetrain simulation is based upon the stock CTRE swervedrive simulation code. This is working currently and will only need to have the tuner_constants.py and tuner-swerve-project.json files updated based upon the swerve modules chosen. This can be done through CTRE Phoenix Tuner. If drivetrain specific controls, i.e press button drivetrain auto drives to position, are needed they should be added in robotcontainer.py. There are examples there of how to call these functions.

Vision Sim
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Photonvision library has simulation built in. It is in the vision folder in the repo above. In order to tweak how much weight the vision estimate is given during pose estimation you can change Vision Settings in config.py

Other Motor Sim
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
There is a subsystem example for an elevator with setpoint control. If you need to add another subsystem you can use this as an example. subsystems/elevator.py includes definition of a Mech2d type object, which allows for visiualization of the elevator during simulation. It also relies upon a simulation for a motor based on CTRE motors. Controls for this can be defined in OI/OI.py and OI/keymap.py. Finally commands for the subsystem should be under commands/elevator.

FRC PathPlanner
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The repoistory is setup to work with FRC path planner and Named Commands. The default currently is the Tests auto which follows a path, raises the elevator to a set height, then run a seperate path. Examples of how to defined Named Commands can be found in robotcontainer.py

.. figure:: /_static/Auto.gif

Advanatage Scope
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Advanatage Scope is software that allows for simulation and replay of logs to visualize robot data. When running simulator you can see robot movenment and can even fake driving from behind the glass. The goal is to use this in 2026 to allow for quicker debugging of issues during build and at events.

.. figure:: /_static/Behind the glass.png


.. toctree::
   :maxdepth: 2
   


