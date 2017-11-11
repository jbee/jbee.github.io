---
layout: default
title: "Outer Space"
categories: [writing, spacecrafts]
---

# Outer Space

* exercise
* game
* galaxy 
* map
* generation

A short story on solving fair random distribution without complicated math.

## Problem
I'm working on a little multi-player [4X](https://en.wikipedia.org/wiki/4X) space game, think [MOO2](https://en.wikipedia.org/wiki/Master_of_Orion_II:_Battle_at_Antares).
There is a galaxy with stars. 
Each player (AI or human) starts on a home world; each in another solar system.

For fairness all home worlds should be surrounded by about the same number of
other stars in about the same average distance. 
Also the home worlds should be distributed in the galaxy so that the closest 
distance between two of them is as large as we can make it so players get to
develop before they are confronted with potential enemies.

The galaxy should look "natural" and random:
No arrangement of stars in uniform shapes or groups.
Takes fun out of the game. 

<big>?</big>

## Solution
First the galaxy is divided into a grid of rectangular cells of equal size.
The number of grid rows and columns (and thereby the number and size of the cells) 
are a function of the player count and the galaxy size so that we get some more 
cells than players. Division, multiplication and rounding get you there.

<svg	xmlns="http://www.w3.org/2000/svg"
	xmlns:xlink="http://www.w3.org/1999/xlink"
	width="450px" height="300px">
<rect x="0" y="0" width="450" height="300" style="stroke:#000000;stroke-width:0; fill:#000000;"/>
<polyline points="0,100 450,100" style="stroke:#DB6559;stroke-width:1px;"/>
<polyline points="0,200 450,200" style="stroke:#DB6559;stroke-width:1px;"/>
<polyline points="150,0 150,300" style="stroke:#DB6559;stroke-width:1px;"/>
<polyline points="300,0 300,300" style="stroke:#DB6559;stroke-width:1px;"/>
</svg>

For each player pick a random cell from remaining set and remove it from that set.

The picked cell's rectangular area is filled:
Pick a random point within the cell. Check that is has at least a
certain minimum distance to all points (stars in galaxy) picked so far (green).
Otherwise (red) pick another point until the minimum number of stars close to 
each player is reached for that cell. 

<svg	xmlns="http://www.w3.org/2000/svg"
	xmlns:xlink="http://www.w3.org/1999/xlink"
	width="450px" height="300px">
<rect x="0" y="0" width="450" height="300" style="stroke:#000000;stroke-width:0; fill:#000000;"/>
<polyline points="0,100 450,100" style="stroke:#DB6559;stroke-width:1px;"/>
<polyline points="0,200 450,200" style="stroke:#DB6559;stroke-width:1px;"/>
<polyline points="150,0 150,300" style="stroke:#DB6559;stroke-width:1px;"/>
<polyline points="300,0 300,300" style="stroke:#DB6559;stroke-width:1px;"/>

<circle cx="120" cy="175" r="20" style="stroke-width:0;fill:#74D07D;" fill-opacity="0.3"/>
<circle cx="120" cy="175" r="2" style="stroke-width:0;fill:#74D07D;"/>
<circle cx="25" cy="166" r="20" style="stroke-width:0;fill:#74D07D;" fill-opacity="0.3"/>
<circle cx="25" cy="166" r="2" style="stroke-width:0;fill:#74D07D;"/>
<circle cx="66" cy="110" r="20" style="stroke-width:0;fill:#74D07D;" fill-opacity="0.3"/>
<circle cx="66" cy="110" r="2" style="stroke-width:0;fill:#74D07D;"/>
<circle cx="80" cy="154" r="20" style="stroke-width:0;fill:#74D07D;" fill-opacity="0.3"/>
<circle cx="80" cy="154" r="2" style="stroke-width:0;fill:#74D07D;"/>
<circle cx="100" cy="134" r="20" style="stroke-width:0;fill:#DB6559;" fill-opacity="0.3"/>
<circle cx="100" cy="134" r="2" style="stroke-width:0;fill:#DB6559;"/>

</svg>

The home world is the point of the cell that is closest to the center of the cell (cyan).
Pick next cell until there is a filled cell for each player.
Some might still be empty.

<svg	xmlns="http://www.w3.org/2000/svg"
	xmlns:xlink="http://www.w3.org/1999/xlink"
	width="450px" height="300px">
<rect x="0" y="0" width="450" height="300" style="stroke:#000000;stroke-width:0; fill:#000000;"/>
<polyline points="0,100 450,100" style="stroke:#DB6559;stroke-width:1px;"/>
<polyline points="0,200 450,200" style="stroke:#DB6559;stroke-width:1px;"/>
<polyline points="150,0 150,300" style="stroke:#DB6559;stroke-width:1px;"/>
<polyline points="300,0 300,300" style="stroke:#DB6559;stroke-width:1px;"/>

<circle cx="120" cy="175" r="20" style="stroke-width:0;fill:#DED087;" fill-opacity="0.3"/>
<circle cx="120" cy="175" r="2" style="stroke-width:0;fill:#DED087;"/>
<circle cx="25" cy="166" r="20" style="stroke-width:0;fill:#DED087;" fill-opacity="0.3"/>
<circle cx="25" cy="166" r="2" style="stroke-width:0;fill:#DED087;"/>
<circle cx="66" cy="110" r="20" style="stroke-width:0;fill:#DED087;" fill-opacity="0.3"/>
<circle cx="66" cy="110" r="2" style="stroke-width:0;fill:#DED087;"/>
<circle cx="80" cy="154" r="20" style="stroke-width:0;fill:#65DBEF;" fill-opacity="0.3"/>
<circle cx="80" cy="154" r="2" style="stroke-width:0;fill:#65DBEF;"/>

<circle cx="330" cy="55" r="20" style="stroke-width:0;fill:#DED087;" fill-opacity="0.3"/>
<circle cx="330" cy="55" r="2" style="stroke-width:0;fill:#DED087;"/>
<circle cx="440" cy="66" r="20" style="stroke-width:0;fill:#DED087;" fill-opacity="0.3"/>
<circle cx="440" cy="66" r="2" style="stroke-width:0;fill:#DED087;"/>
<circle cx="358" cy="89" r="20" style="stroke-width:0;fill:#DED087;" fill-opacity="0.3"/>
<circle cx="358" cy="89" r="2" style="stroke-width:0;fill:#DED087;"/>
<circle cx="388" cy="44" r="20" style="stroke-width:0;fill:#65DBEF;" fill-opacity="0.3"/>
<circle cx="388" cy="44" r="2" style="stroke-width:0;fill:#65DBEF;"/>

<circle cx="188" cy="255" r="20" style="stroke-width:0;fill:#DED087;" fill-opacity="0.3"/>
<circle cx="188" cy="255" r="2" style="stroke-width:0;fill:#DED087;"/>
<circle cx="280" cy="277" r="20" style="stroke-width:0;fill:#DED087;" fill-opacity="0.3"/>
<circle cx="280" cy="277" r="2" style="stroke-width:0;fill:#DED087;"/>
<circle cx="166" cy="220" r="20" style="stroke-width:0;fill:#DED087;" fill-opacity="0.3"/>
<circle cx="166" cy="220" r="2" style="stroke-width:0;fill:#DED087;"/>
<circle cx="230" cy="240" r="20" style="stroke-width:0;fill:#65DBEF;" fill-opacity="0.3"/>
<circle cx="230" cy="240" r="2" style="stroke-width:0;fill:#65DBEF;"/>

<circle cx="170" cy="125" r="20" style="stroke-width:0;fill:#DED087;" fill-opacity="0.3"/>
<circle cx="170" cy="125" r="2" style="stroke-width:0;fill:#DED087;"/>
<circle cx="260" cy="167" r="20" style="stroke-width:0;fill:#DED087;" fill-opacity="0.3"/>
<circle cx="260" cy="167" r="2" style="stroke-width:0;fill:#DED087;"/>
<circle cx="288" cy="134" r="20" style="stroke-width:0;fill:#DED087;" fill-opacity="0.3"/>
<circle cx="288" cy="134" r="2" style="stroke-width:0;fill:#DED087;"/>
<circle cx="215" cy="150" r="20" style="stroke-width:0;fill:#65DBEF;" fill-opacity="0.3"/>
<circle cx="215" cy="150" r="2" style="stroke-width:0;fill:#65DBEF;"/>

</svg>

Finally add some additional random points/stars within the bounds of the whole 
galaxy that still have the quality of not being to close to any of the points/stars
picked before. 

We are done. Only math required is basic arithmetic and distance between two
points in a 2D plane.


## Result
The result is a natural looking galaxy where home worlds are 
distributed in no particular shape but still each home world has about the same
quality. The grid made sure the home worlds are not to close to each other.
The computational complexity is linear with the number of players.
By sizing cells appropriate it can be made sure there is enough space for the 
number of random points we want to place in each cell without failing too often.

Also the algorithm naturally allows for the kind of parameters we want to have:

* number of players
* size of the galaxy
* total number of stars
* minimum distance between stars
* minimum number of stars per player
* minimum size of a players "territory"

How the grid is divided can easily be tweaked to these parameters.

While the problem looks for a combination of minima and maxima this
simple approximation solves it with very little math, code and complexity. 
