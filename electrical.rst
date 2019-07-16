.. Team 573 Programming Documentation documentation master file, created by
   sphinx-quickstart on Thu Sep 13 19:58:13 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

FRC Electrical Quick Reference
==============================================================

FRC Control System Overview
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A good overview of FRC electronics can be found below,

https://wpilib.screenstepslive.com/s/currentCS/m/cs_hardware/c/86642

Interface Control Document (ICD)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
We use a spreadsheet called an interface control document to organize what ports are used for which sensors/motors. This is a shared document between electrical, programming and mechanical. If changes need to be made the leads of programming and electrical must agree. It is important that communication is maintained between the electrical and programming subteams, so that the ICD is consistent with the robot all season!!

.. image:: /_static/ICD.PNG


CAN
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
CAN is a method for commucating between the RoboRio and other electronics. We only use it to talk to the power distrbution board (PDP) and Pnuematic Control Module (PCM). In order to update the firmware and set the CAN ID you can follow the steps here (https://wpilib.screenstepslive.com/s/currentCS/m/cs_hardware/l/216217-updating-and-configuring-pneumatics-control-module-and-power-distribution-panel)



.. toctree::
   :maxdepth: 2



