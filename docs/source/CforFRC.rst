.. Team 573 Programming Documentation documentation master file, created by
   sphinx-quickstart on Thu Sep 13 19:58:13 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Introduction to Python Programming
==============================================================
On Team 573 we have used Python to program our FRC robots since 2023 years. This section will hopefully give you a good starting point when you need to figure out how to do something for the team.

Where to Start Learning Python Programming
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
There are many great websites to teach you how to use Python code. Here is a Python overview, https://developers.google.com/edu/python If you want to get started from scratch in programming at home this is a great place to get started. The rest of this section briefly covers some of the things in Python that we use in FRC robotics

Variable Types
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A great feature of Python is that it auto assigns variable types based on its inital assignment.
You can also explicitly define variable types. Here is a table of commonly use variable types in Python.

=========== =====================================================================================
Short Name	Definition
----------- -------------------------------------------------------------------------------------
int			Whole numbers
----------- -------------------------------------------------------------------------------------
float		Number with a decimal point.
----------- -------------------------------------------------------------------------------------
list		Comma seperate list of variables of any other type.
----------- -------------------------------------------------------------------------------------
bool		Boolean value (true or false)
----------- -------------------------------------------------------------------------------------
string		Used for words in code
=========== =====================================================================================

Loops
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Since the main loop of FRC robot code loop continously we don't use many loops in our code. Still its good to know about them.

While Loop

.. code-block:: python

	while True:
	  # Do Something
	

For Loop

.. code-block:: python

	list = [0,1,2,3]
	for i in list:
	  # Do Something
	

Conditionals
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
We use coniditional statements all the time in FRC robot code.
We use them based upon controller inputs or sensors. Any example of some are below.

If Statement

.. code-block:: python

	if i == 573:
	  # Do Something


Else If Statement

.. code-block:: python

	if i == 573:
	  # Do Something
	elif i == 1:
	  # Do Something Else
	

Else Statement

.. code-block:: python

	if i == 573:
	  # Do Something
	elif i == 1:
	  # Do Something Elseif
	else:
	  # Do Something Else


In order to use a conditional you generally need to compare to values. The table below shows several 
comparators.

=========== =====================================================================================
Symbol		Comparision
----------- -------------------------------------------------------------------------------------
==			Is Equal
----------- -------------------------------------------------------------------------------------
!=			Is Not Equal
----------- -------------------------------------------------------------------------------------
<			Less Than
----------- -------------------------------------------------------------------------------------
<=			Less Than Or Equal
----------- -------------------------------------------------------------------------------------
>			Greater Than
----------- -------------------------------------------------------------------------------------
>=			Greater Than Or Equal
----------- -------------------------------------------------------------------------------------
&&			And
----------- -------------------------------------------------------------------------------------
||			Or
=========== =====================================================================================

.. toctree::
   :maxdepth: 2
