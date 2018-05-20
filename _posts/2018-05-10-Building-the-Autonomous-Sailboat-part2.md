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

The Arduino Mega acquires all the data from the sensors and acts on the actuators (sail and rudder). It is basically the lowest level of decision making computer on the boat. That is why its code has to be robust and secure. It also has to be configurable and depend on the hardware of the boat. I will talk about this component based software architecture here. But first of all let's see how the code is done in the Arduino.

To see for yourself all of the source code, don't hesitate to look at the [Arduino](https://github.com/Plymouth-Sailboat/SailBoatArduinoInterface) repository with all the source-code inside.

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

As you understand now, the libraries that were made for our boats are each of the components interfaces : the sensors and motors, and the Sailboat entity interface. We have a wind sensor library, an IMU (XSens) library, a GPS library and a Sailboat library. The controllers are not in libraries but directly as classes inside the .ino (Arduino project file) to be able to change them easily.

### Configuration

To be able to configure easily the boats configuration related to its sensors (e.g. sensors offsets, values, pin number, I2C address, etc...) a configuration header is put in the Sailboat library. This configuration file only contains `#define` and `#ifdef` for each element used by the libraries.

That way, to change from one boat to the other, you only need to `#define SAILBOAT_SMALL` or `#define SAILBOAT_BIG` for our cases.

### Communication to the Raspberry Pi

To communicate to the raspberry pi, we use the [rosserial-arduino](http://wiki.ros.org/rosserial_arduino) package from ROS. That way we don't need to create our own protocol which could take a long time. Also this makes it possible for the Arduino to be completely compatible with ROS and subscribing and publishing to topics easily. Be careful that this package is big and takes a lot of space in the Arduino Mega.

Each components have their `communicateData()` function in which they send the message to the topic they are publishing to.

### Modes

What we call modes in the Arduino is the Controllers. The Sailboat has a container of Controller that it sets corresponding to the message the raspberry pi sends him. It corresponds to basic controllers on the rudder and sail. Each of the controller waits for a message with the according information from the Raspberry Pi. We have :

* Rudder and Sail control : Rudder and Sail are directly taken from the Raspberry Pi message
* Only Rudder control : the optimal Sail angle is applied to the Sail
* Sail and cap control : Sail angle is taken from the Raspberry Pi and the rudder is put according to the cap
* Heading control : With only a cap, the sail and the rudder are controlled accordingly

Then we have special controllers that don't need the Raspberry Pi. They are meant in case the Raspberry Pi don't respond :

* Return Home control : The sailboat goes to the GPS point it first saw when it was turned on
* RC control : The radio controller takes over everything

You can see the whole explaination at the [Arduino Wiki](https://github.com/Plymouth-Sailboat/SailBoatArduinoInterface/wiki/Arduino-Mode)

### Watchdogs and Security

For this kind of robots, it is always good to have some securities in case anything goes wrong. As the Arduino is the last computer that directly commands the actuators and sensors, it has to have some security if things are broken.

For that, we put watchdogs which will automatically put the sailboat in "safe" mode so that we can retreive it easily.  
First of all, when the boat is turned on, all the sails are opened at maximum so that the wind don't tear the sails or push the boat in the water.  
Once it is controlled by the Raspberry Pi, the latter has to send every 30 seconds a message to tell the Arduino it is awake. That way if the Raspberry Pi don't respond anymore, the Arduino puts itself in *Return Home control* to be able to retreive it.  
One last watchdog is inside the Arduino. If a bug happens (no controller at all in the Sailboat), the Arduino resets and put the *Return Home control*.

## Raspberry Pi/ROS

The Raspberry Pi only does the high level control such as path-planning, intensive calculation controllers and more. The Raspberry Pi runs with ROS for its controllers. It receives all the sensors data from the Arduino and can send messages to the Arduino asking it to put certain angles to the sail and rudder. As you have seen above, because the Arduino is using rosserial-arduino package, it directly sends the sensors data to ROS topics that controllers on the Raspberry Pi can subscribe to. We will look at the architecture that was done here to simplify the making of controllers.

<figure>
	<a href="https://github.com/Plymouth-Sailboat/SailBoatArduinoInterface"><img src="https://raw.githubusercontent.com/UlysseVautier/ulyssevautier.github.io/master/assets/img/raspberrypi3.jpg"></a>
	<figcaption>Raspberry Pi 3 Model B</figcaption>
</figure>

### Features

We use ROS as a middleware for our controllers. It enables us to use other well-known packages in the ROS community. But that also means we have to standardize our nodes to be compatible with other packages as much as possible.

For this, the messages sent to or from the Arduino are all standard messages, used for other packages.

By doing this, we also become compatible to Gazebo simulator for example, by adding a URDF or frames to our boat.

### Class Architecture

We again highly use polymorphism to simplify the work. I will mostly talk about the C++ part, but a python structure has been done the same way for python users.

The same way as the Arduino, we have a controller that derives from the Controller class that has 2 simple methods : `setup()` and `control()`. When making a controller, you only need to implement those to get the controller working.

The class above called Controller implements all the initialization, subscribing to all the topics from the Arduino and resetting the watchdog of the Arduino. All this is done automatically when the `setup()` method is called from the derived class.

### List of Controllers

At the moment of this writing, some simple controllers have been implemented :

* Potential Field : the well known potential field algorithm for robots navigation
* Waypoint Follower : Simplest algorithm wher the boat goes from one GPS point to the other
* Line Follower : The boat tries to get from one point to the other while following the line between those two points
* Position Keeping : An algorithm where the boat tries to stop at a certain GPS point

### Making Your Own Controllers

For details about all that has been said, a wiki was done at the repositories : [Sailboat ROS](https://github.com/Plymouth-Sailboat/SailBoatROS/wiki) and [Arduino Wiki](https://github.com/Plymouth-Sailboat/SailBoatArduinoInterface/wiki)