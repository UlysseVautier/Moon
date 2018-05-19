---
layout: post
title: "Building the Autonomous Sailboat (Part 2)"
date: 2018-05-10
excerpt: "Building the Autonomous Sailboat. Software Part concerning the inside of the Arduino and the Raspberry Pi"
tags: [Autonomous, Sailboat, Software, Arduino, Raspberry Pi, ROS]
comments: true
---

<figure>
	<a href="https://plymouth-sailboat.github.io/"><img src="https://raw.githubusercontent.com/UlysseVautier/ulyssevautier.github.io/master/assets/img/bigboat.jpg"></a>
	<figcaption><a href="https://plymouth-sailboat.github.io/" title="Autonomous Sailboat">The big boat on which research is done (2m length)</a>.</figcaption>
</figure>

## The Software Part

If you haven't read the first part yet, I suggest you go take a look at it, to understand the hardware inside the boats [here](https://ulyssevautier.github.io/Building-the-Autonomous-Sailboat-part1/).

As we explained on the first post, the main computing boards are the Arduino Mega and the Raspberry Pi. They were chosen to help people make their own autonomous sailboat, using these open-source accessible hardware.  
We will be focusing here on the software part, in the Arduino and the Raspberry Pi. I will explain the architecture, how all the sensors and actuators are handled, how to build a customizable architecture depending on the hardware of the boat, etc...

The [Arduino](https://github.com/Plymouth-Sailboat/SailBoatArduinoInterface) source code and the [Raspberry Pi ROS](https://github.com/Plymouth-Sailboat/SailBoatROS) source code are all available online.

## The Arduino

The Arduino Mega acquires all the data from the sensors and acts on the actuators (sail and rudder). It is basically the lowest level of decision making computer on the boat. That is why its code has to be robust and secure. It also has to be configurable and depend on the hardware of the boat. I will talk about this component based software architecture here. But first of all let's see how the code is done in the Arduino

### The Code Inside the Chip

<figure>
	<a href="https://github.com/Plymouth-Sailboat/SailBoatArduinoInterface"><img src="https://raw.githubusercontent.com/UlysseVautier/ulyssevautier.github.io/master/assets/img/ARDUINO_MEGA_A03.png"></a>
	<figcaption>The Arduino MEGA 2560 Rev. 3</figcaption>
</figure>

The Arduino Mega 2560 is called after the microcontroller it has : the [Atmel ATmega2560](http://www.microchip.com/wwwproducts/en/ATmega2560). What is great with the Arduino is how easy it is to use it. The company Arduino has made a tremendous amount of work in making a library and compiler that transform the simple functions you put into code, into physical interactions. Now of course, every microcontroller can be uploaded with code, but the amazing part about Arduino is that no other companies have been able to make a simple and easy to use library for microcontrollers.

To compile code into the Arduino, an [IDE](https://www.arduino.cc/en/Main/Software) is given on which you can write code in C++ and upload it in the chip. The community has also made a lot of libraries that are easily downloadable through the IDE library manager, and that are made to communicate with every imagineable sensors, motors, LCD screens, etc... And of course it is not hard to make your own C++ library.

I won't put a guide on how to use the Arduino as there are already tons of them online, but I will talk more about the software architecture that is inside the Arduino.

### Software Architecture

As this Arduino will control different boats with different hardware attached to it, it was needed to build an architecture that would make it easy to add or remove features, hadware sensors or motors.

For this it was obvious to use as much as possible the capability of the **OOP** (Object-Orientated Programming) of C++. I don't generally like talking about design patterns in C++ since it is basically giving names to solutions that uses C++. But if you want to have some culture (which is always useful) we could say the design pattern used ressembles the [Entity-Component-System](https://en.wikipedia.org/wiki/Entity–component–system) based architecture. Of course the version in the boat is far more simpler.

Basically, the Sailboat is a C++ class that contains Sensors, Actuators and Controllers. Each of these are *Components*, meaning they each have more or less the same behaviours. E.g. Sensors acquire data and send them, whichever it be a wind sensor or the GPS or the IMU.  
The Sailboat itself is an *Entity*, it contains those *Components* and act on them, call them or whatever.

Let's see an example of a Sensor class :

```cpp
#include <SensorsInterface.h>
			 
class WindSensor : public SensorROS{
	public:
		WindSensor() : SensorROS(10, "wind", &msg){}
		
		void init();
		void updateMeasures();
		void communicateData();
		
		double getMeasure();
		
};
```
Here is a basic sensor template. It initializes it's variables `init()`, the updates are called by the sailboat every 10ms `updateMeasures()` and then it communicates its data `communicateData()`. All this is done automatically thanks to the magic of polymorphism. If you want to make another Sensor you just need to implement those methods and add the sensor to the sailboat.

Now the Controller works on the same principle. All controllers have the same behaviour : `init()` to initialize some variables and `control()` to act upon the actuators. All components have access to the *Entity* so the controllers have access to all sensors.

Now you understand that the Actuactors are the same.

The *System* in our case is the Arduino ino code which instantiate the Sailboat class and links it to all the Arduino chip.

This architecture is quite simple and is basically what C++ offers with polymorphism : being able to group classes with the same behaviours and call them all whatever specific methods or variables they have.

<figure>
	<a href="https://github.com/Plymouth-Sailboat/SailBoatArduinoInterface"><img src="https://raw.githubusercontent.com/UlysseVautier/ulyssevautier.github.io/master/assets/img/arduinoarchitecture.bmp"></a>
	<figcaption>Software Architecture inside the Arduino</figcaption>
</figure>

### Libraries

### Configuration

## Raspberry Pi/ROS

### Features

### Class Architecture

### List of Controllers

### Making Your Own Controllers
