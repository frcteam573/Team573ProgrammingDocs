.. Team 573 Programming Documentation documentation master file, created by
   sphinx-quickstart on Thu Sep 13 19:58:13 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Introduction to C++ Programming
==============================================================
On Team 573 we have used C++ to program our FRC robots for the past 2 years. This section will hopefully give you a good starting point when you need to figure out how to do something for the team.

Where to Start Learning C++ Programming
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
There are many great websites to teach you how to use C++ code. I like code academy, here is a c++ overview, https://www.codecademy.com/learn/learn-c-plus-plus If you want to get started from scratch in programming at home this is a great place to get started. The rest of this section briefly covers some of the things in C++ that we use in FRC robotics

Variable Types
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Here is a table of commonly use variable types in C++.

=========== =====================================================================================
Short Name	Definition
----------- -------------------------------------------------------------------------------------
int			Whole numbers
----------- -------------------------------------------------------------------------------------
double		Number with a decimal point.
----------- -------------------------------------------------------------------------------------
char		Single letter or number
----------- -------------------------------------------------------------------------------------
bool		Boolean value (true or false)
----------- -------------------------------------------------------------------------------------
Void		No type (used many for functions that don't return anything)
----------- -------------------------------------------------------------------------------------
string		Used for words in code
=========== =====================================================================================

Loops
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Since the main loop of FRC robot code loop continously we don't use many loops in our code. Still its good to know about them.

While Loop

.. code-block:: c++

	while(True){
	  // Do Something
	} 

For Loop

.. code-block:: c++

	for(int i=0,i<10,i++){
	  // Do Something
	}

Conditionals
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
We use coniditional statements all the time in FRC robot code.
We use them based upon controller inputs or sensors. Any example of some are below.

If Statement

.. code-block:: c++

	if(i == 573){
	  // Do Something
	} 


Else If Statement

.. code-block:: c++

	if(i == 573){
	  // Do Something
	} 
	elseif(i == 1){
	  // Do Something Else
	}

Else Statement

.. code-block:: c++

	if(i == 573){
	  // Do Something
	} 
	else if(i == 1){
	  // Do Something Elseif
	}
	else{
	  // Do Something Else
	}


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
