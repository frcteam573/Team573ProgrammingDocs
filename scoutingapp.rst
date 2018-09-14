.. Team 573 Programming Documentation documentation master file, created by
   sphinx-quickstart on Thu Sep 13 19:58:13 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Scouting Apps
==============================================
In order to help the team pick the best alliance partners at our events the team scouts the other teams at the events.
To make this easier on the scouters, the programming team has in the past created customized scouting apps for them to record data from.

We have scouting tablets that are used to collect match and pit scouting. In the end we combine all the data together and consloidate it into a single scouting spreadsheet we can use to make a pick list.
This section talks about how we have done it in the past, but we can always try something new in the future.

Intro to Python
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The scouting apps have been developed using Python. Python is a very popular programming language with lots of resources online to learn it.

How to install Python
-------------------------------------------
You can download Python here. (https://www.python.org/downloads/) Python2.x and Python3.x are still being used. The scouting apps from 2018 required Python2.7

Following the install instructions will install it on your computer. It will also install a text editor call Idle, you can develop python scripts in this or you can find another text editor online.

Where to start
------------------------------------------
You can start to learn Python at codeacadmey or use the beginner's guide found at https://wiki.python.org/moin/BeginnersGuide

While these are get to get started Python can be used for such a wide variety of task, it is sometimes best to learn as you go in order to build your application once you learn some basics.

Pip Installer
------------------------------------------
Python is so powerful becuase of the large number of open source libraries that you can use for your projects. The pip installer allows you to easily download and install those libraries.

To install openpyxl library for example, you just need to run the following command in a command window.

.. code-block:: bat

	pip install openpyxl

SQLite
-----------------------------------------
SQLite is a type of SQL database in which data can be stored and retrieved. It is easy to setup on a local computer and placing data into it from a python script is fairly simple. 
It is also installed with python.

However to get a tool to view the tables in a GUI you need to download it from here (https://www.sqlite.org/index.html)

2018 Scouting Apps
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The scouting application rely on a Python library called htmlPy. This allows for Python to be used to take input from a webpage and store it in a SQLite database.

In general the applications have 5 main files

main.py - Python file which connects back end to html page
back_end.py - Python file to map inputs from html page and put the data in database
index.html - Input website 
styles.css - Input website stylesheet
data.db - SQLite database

We also used a few other Python scripts to collect and combined the SQLite databases from each scouter's tablet.

The repo with the 2018 apps can be found here. (https://github.com/savage301/Team573_2018ScoutingApps)

.. toctree::
   :maxdepth: 2
   


