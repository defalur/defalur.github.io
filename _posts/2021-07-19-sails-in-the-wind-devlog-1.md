---
layout: post
title:  "Sails in the wind, Devlog 1"
date:   2021-07-20 21:48:00 +0200
categories: devlog update
visible: 1
---
## A new exciting start!

Now is the time I take a big step and start a devlog to document my progress on
my personnal project, which I decided to call: *Sails In The Wind*. My initial
idea for this project is to make a game that revolves around sailing across the
ocean to do many tasks. I've been thinking about exploring new places, courier missions,
races, and other types of activities in which a sailor could take part. The goal is to
make a simple game, with very few mechanics and hopefully being able to finish it and release it
as a full game in the future.

I decided to make this game with the Godot game engine, which I've been wanting
to try for a while and I think that this project is a great opportunity to do
this experiment.

Now onto the first devlog and my current progress!

## Making a ship and the wind that pushes it

### The ship
The first thing I need in a game about sailing ships is a ship. I started by drawing
a simple top down ship sprite with a sail sprite. I added corresponding node to my main
scene and created a script to allow me to rotate the sail and the ship, this is the easy part.

![Ship]({{site.baseurl}}/assets/devlog_1/boat_1.gif)

### The Wind

Now comes the interesting part! First I need to create the wind itself. For this task
I decided to use a very simple solution and have a node manage the wind for me by giving a single
direction at any time. I can make this direction change over time and simulate a nice wind.
A more complex simulation could try to create currents and similar things but I think it's
too complicated for something that would not be too noticeable by the player.

Now I have my wind, represented as a vector, and regularly updated. This wind vector is given
to the ship node for the simulation. How do I simulate wind on a ship? I remembered a lesson I
had in a 3D animation class where we had to make a simple cloth simulation that could react to wind
and we noticed that we can use a dot product to compute the strength of the force applied on the cloth
by the wind. Our sail works in the same way. The second parameter we need to compute is the direction of
the force applied on the sail. This is also simple because of the sail is flat, we can use the normal to the sail
and we get the direction. We can use the dot product to get how much of the total wind force is in the
direction of the normal of the sail. Thus, we get the following formula:

![sail_force]({{site.baseurl}}/assets/devlog_1/force.png)

When we consider V as the wind, we can decompose it with the N and T vectors using the dot product.
For our force calculation, we only need to compute the force in the direction of N.

Now that we have the force applied on the sail, we have to convert it into motion on the ship.
In many instances, the force will not be aligned with the ship, however, we don't often
see ships moving like crabs on the water, this is because of the shape of their hulls, that
basically make them behave like they are on rails, thanks to drag. Like we did for computing the force from the wind,
we can use the dot product to know how much of that force will be transfered forward.

![forward_force]({{site.baseurl}}/assets/devlog_1/force_2.png)

In my model, I don't want to worry too much about having a very precise simulation so I decided
to have infinite side drag, and a reasonable amount of forward drag that will want to slow the ship down
a bit. Thus the formula for computing the acceleration of the ship is very simple:

![acceleration]({{site.baseurl}}/assets/devlog_1/force_3.png)

For my current model, the ship's mass is 1 and I chose to have a low coefficient of friction (K = 0.1) which seems to give
a reasonable simulation for now.

From that acceleration we can change the ship's velocity and the simulation is complete!
I also added support for multiple sails because I might need bigger boats in the future.

![Ship_moving]({{site.baseurl}}/assets/devlog_1/boat_2.gif)

## Creating an ocean

This game needs to have nice looking water, this is after all what you'll see most when playing.
I put some thoughts into what I wanted to do, and I decided on using a shader to create a procedural
texture that I can scroll infinitely without really seeing any repetition. In order to create
the impression of an infinite plane without using too much resources, I put a quad behind the ship and
use a shader that I scroll depending on the ship's position to give the impression of moving. However,
from the camera's perspective, nothing is really moving, which is neat! The shader was taken from [here](https://godotshaders.com/shader/anime-esque-water-shader/)
and was slightly modified to allow for scrolling it infinitely.

## A bit of polish

When testing my ship on the ocean, I noticed that there was something missing to really make it
satisfying, and that thing is the wake of the boat! The idea I had to implement a simple wake
was to use a particle system. I made particles move to the sides and grow with time and that gave a pretty nice
effet. The last problem I had to solve was to make the particle system stop when the boat was not moving
and change the intensity depending on the speed of the boat. The best way I found to make the intensity of the wake
change was to change the scale of the particles. A faster boat means bigger particles and a more intense wake, the
effet works suprisigly well with particles that become more transparent over time.

![Ship_with_wake]({{site.baseurl}}/assets/devlog_1/boat_3.gif)

## The future

The next big step in this development process is to add a little bit of polish and to generate islands!
I'll make my next devlog when I have a first prototype of the island generation, maybe with settlements.
