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
You can download Python here. (https://www.python.org/downloads/) Python2.x and Python3.x are still being used. The scouting apps from 2018 require Python2.7 and the scouting apps from 2019 require 3.6.

Following the install instructions will install it on your computer. It will also install a text editor call Idle, you can develop python scripts in this or you can find another text editor online. Sublime Text is a good editor with text highlighting that is free.

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

Creating a Virtual Environment
----------------------------------------
A virtual environment is a way to segment your Python projects on you computer. Since each project might rely on different and possibly conflicting libraries its best to create an environment for each Django project you have. This is something you'll need to do on your machine, before you download the github repository containing the project.

Lets say I wanted to create a virtual enviornment called MW_573, then in Powershell you would perform the following commands.

.. code-block:: bat

	pip install virtualenv
	:: Navigate to the folder you want to put the virtual enivorment in in our case C:\dev
	C:\dev
	::Create new folder for virtual environment
	mkdir MW_573
	cd MW_573
	::Create virtual enivornment
	virtualenv .
	::Activate your environment
	.\Scripts\MW_573
	::Verify that it was setup correctly Run pip freeze and it should return nothing
	pip freeze
	::You can install any Python package to you environemtn with pip installer as nomormal, but it will only apply to your virtual environment.
	::To install all libraries for a project at once run
	pip install -r requirements.txt
	::When you done working you can deactivate the environment
	deactivate
	::When you want to start working again just navigate to 
	C:\dev\MW_573
	::And activate it again
	.\Scripts\activate

Django
------------------------------------------
Django is a webframework for python.

It's website is https://www.djangoproject.com/ and is a great resource for tutorials and finding libraries that can help. 

To learn more about it I recommend stating there. For specific questions you can try sites like https://stackoverflow.com/ if you've run into a problem someone else probably has to. Stack Overflow has tons of coding questions answered. 

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

2019 Scouting Apps
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The scouting application relies on a Python webframe work call Django. This allows for Python to take input from a webpage and store it in a SQLite database. This framework is used on thousand of active websites and allows for more flexiability than htmlPy, which was used for the 2018 scouting app.

The repo with the 2019 app can be found here. (https://github.com/savage301/2019_Scouting_App) This will be a private repo until the end of the 2019 season. If you need access please talk to Coach Eric.

Currently the framework for the 2019 app has been setup and is on github. Now you can make a branch and work on a section (such as the pit scouting app). We will divide up the work at a future meeting.

.. toctree::
   :maxdepth: 2