.. Team 573 Programming Documentation documentation master file, created by
   sphinx-quickstart on Thu Sep 13 19:58:13 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Using an Arduino on a FRC Robot
==============================================================
This section goes over tips and how to use an Ardiuno on an FRC robot to manage sensor inputs and control outputs, such as LED lights.

Wiring Up the Arduino
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The electrical team will be handling wiring up the Ardiuno, but the programming team does care about the connection type so we can make sure we are interfacing with the RoboRio with the correct protocol. 

USB Connection 
------------------------------------------------------------
While you can power the arduino directly from the RoboRio USB port we've had issues communicating over that connection. 

I2C Connection
------------------------------------------------------------
Looking online you should be able to use the I2C connection to power and talk to the Roborio from the arduino. We currently haven't used this since we use the I2C port on the ardiuno to talk to our laser range sensors.

DIO Ports
------------------------------------------------------------
You can program the arduino to change its DIO ports to high or low or read DIO ports to communicate with the RoboRio. This requires the code on the arduino to contain the logic needed to change those DIO values, which may require it to be updated often. 

Sensor Inputs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
On each robot we (the programming team) pushes for sensors to be included to help us control the robot. 
It is best to have a rough idea of what sensors we will need while the mechanical team is designing the robot. 
Since the programming team wants the sensors it's our job to make sure they get included in the design so they can be used properly.

Laser Range Finder
---------------------------------------------------------------
We have tested using the adafruit vl53l0x laser range finder with an ardiuno uno. Using stock code from adafruit's website we can get the distance measured by the sensor to the ardiuno in mm. Then using a simple if statement we set a DIO on the ardiuno and read that back from a DIO port on the RoboRio. The arduino and RoboRio code to do this is below.

https://learn.adafruit.com/adafruit-vl53l0x-micro-lidar-distance-sensor-breakout

In Robot.cpp

.. code-block:: c++

	DigitalInput Arduino {0};
	bool valueout = Arduino.Get();

In ArduinioCode.ino

.. code-block:: c++

	#include "Adafruit_VL53L0X.h"

	Adafruit_VL53L0X lox = Adafruit_VL53L0X();

	void setup() {
	  Serial.begin(115200);
	  pinMode(7, OUTPUT);

	  // wait until serial port opens for native USB devices
	  while (! Serial) {
	    delay(1);
	  }
	  
	  Serial.println("Adafruit VL53L0X test");
	  if (!lox.begin()) {
	    Serial.println(F("Failed to boot VL53L0X"));
	    while(1);
	  }
	  // power 
	  Serial.println(F("VL53L0X API Simple Ranging example\n\n")); 
	}


	void loop() {
	  VL53L0X_RangingMeasurementData_t measure;
	    
	  Serial.print("Reading a measurement... ");
	  lox.rangingTest(&measure, false); // pass in 'true' to get debug data printout!
	  
	  double Distance = measure.RangeMilliMeter;
	  double limit = 500;

	  if (Distance < limit){
	    digitalWrite(7, LOW);
	  }
	  if (Distance > limit) {
	    digitalWrite(7, HIGH);
	  }
	  
	  if (measure.RangeStatus != 4) {  // phase failures have incorrect data
	    Serial.print("Distance (mm): "); Serial.println(measure.RangeMilliMeter);
	  } else {
	    Serial.println(" out of range ");
	  }
	    
	  delay(100);
	}

LED Light Control
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Sometimes the team uses LEDs on the robot for decoration and indicator lights. When we use RGB leds it is conveient to use an arduino to control the light colors based upon input from the RoboRio. 

There are some ardiuno libraries that are written to control RGB LED light strips such as FastLED. Some sample code which uses this library can be found on AndyMark's website. 

https://www.andymark.com/products/rgb-individually-addressable-led-light-strip-with-arduino-controller-kit


.. toctree::
   :maxdepth: 2