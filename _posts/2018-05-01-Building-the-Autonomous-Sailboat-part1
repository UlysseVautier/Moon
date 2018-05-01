---
layout: post
title: "Building the Autonomous Sailboat (Part 1)"
date: 2018-05-01
excerpt: "Building the Autonomous Sailboat. Hardware Part concerning electronic boards, Arduino, Raspberry Pi and Sensors"
tags: [Autonomous, Sailboat, Hardware, Arduino, Raspberry Pi, Sensors]
comments: true
---

<figure>
	<a href="https://plymouth-sailboat.github.io/"><img src="https://raw.githubusercontent.com/Plymouth-Sailboat/plymouth-sailboat.github.io/master/img/IMG_0318-e1518717177945.jpg"></a>
	<figcaption><a href="https://plymouth-sailboat.github.io/" title="Autonomous Sailboat">The small sailboat that will compete for the World Robotic Sailing Championship (WRSC)</a>.</figcaption>
</figure>

## The Autonomous Sailboat

My Ph.D. at Plymouth University is on the subject of Autonomous Sailboat.
As is the case in most robotic Ph.D.s, experimental results are important.
In that regard, a full autonomous Sailboat had to be built !

With the help of past experience in robotics and industrialisation, I wanted to make a robust, simple - to use and to build - and affordable Sail-robot.
This goal comes from my view on research : It is meant to help people, by sharing the knowledge and make it accessible to as many people as possible.
Why make a million dollars robot for select laboratories ? It doesn't make sense in research.
Of course, sometimes the price cannot be as cheap as you want (depending on the subject you are in, the material needed, etc...), but this is not the case for our sailboat !

I will explain all the process of building this boat, from the software architecture to the hardware architecture, the choice in materials and the whole philosphy about this boat.

## The Hardware

Part 1 of this 2 parts series will focus on the hardware part.

<figure class="half">
	<a href="https://github.com/Plymouth-Sailboat/Sailboat-Meca"><img src="https://raw.githubusercontent.com/UlysseVautier/ulyssevautier.github.io/master/assets/img/ArduinoIntcroped.png"></a>
	<a href="https://github.com/Plymouth-Sailboat/Sailboat-Meca"><img src="https://raw.githubusercontent.com/UlysseVautier/ulyssevautier.github.io/master/assets/img/RPiIntcroped.png"></a>
	<figcaption>The Boards connecting the Arduino with the Raspberry Pi and the sensors</figcaption>
</figure>

There is nothing special about what has been done for the sailboat.
However a lot of work has been spent on this and maybe others might find interesting all the process (this can be applied to any robots of any sorts).

### What's in there ?

To transform a sailboat (or anything) into an autonomous robot, we need to take control of everything and add "eyes" to it.
We need to be able to control everything that can possibily make it move. And we need to add sensors so that it becomes aware of its environment.
Add control algorithms to it and that's basically the definition of a robot - being able to change its environment, being aware of the environment and making decisions.

In the sailboat, that means we need to control the sail and the rudder (our actuators), and we need to add the GPS (position), the IMU (heading and velocity) and the Wind Sensor (to know the force and the direction of the wind).
We can actually add any sensor we want, depending in the necessities for our controls.
The hardware is built to have access to all of that, and be able to apply controls to the actuators regarding the data acquired by the sensors.

### Robustness and Ergonomics

The hardware in the sailboat was built while keeping into mind robustness and ergonomics. This might sound contradictory but actually most of the time a tough product is a good product. (a good product is not necessarily a tough product).
But this doesn't seem as easy as just saying "Let's do a robust robot with a nice design !" and that's it. 
A lot of time thinking about all the potential use of the hardware must be examined and solved.
The first and obvious challenge making a hardware for a sailboat is waterproofing ! (or weatherproofing).
