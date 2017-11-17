---
layout: default
title: "That Alien"
summary: "Name generation with programming basics"
categories: [writing, spacecrafts]
---

# That Alien

* exercise
* game
* name 
* generation

A short story on inventing names without a CS or linguistic major.


## Problem
Still working on a 4X space game with multiple races. 
There are leaders, ships, planets and stars to name.
Each race has its language. 
Their names have a certain sound and melody.
A human player should able to associate entities with
another player based on their name. 
Sounds terribly complicated. Doesn't have to be.

<big>?</big>


## Solution
For each language we create a list of sets of name fragments.
Each fragment is one or more letters. 
Fragments, sets and lists are not uniform in any way.

```
[ { "a", "be" }, { "ce", "d", "ehf"}, { "t" }, ... ]
```

To build a random name pick a random element from every 
set and concatenate them in list order. Almost done.

To allow for short(er) names add a probability for each
set that states the chance you pick a fragment from
that set at all. Yield a random chance to see if the 
set is skipped.

```
[ { 90, "a", "be" }, { 75, "ce", "d", "ehf"}, { 50, "t" },... ]
```

To avoid duplicate names remember all composed names
already used and generate new random names until a new
one is found.

Possible names from the example: `"ace"`, `"bed"`, `"becet"`, `"at"`, ...

The algorithm is trivial. 
Picking fragments that **always** can be pronounced 
when concatenated with any of the next possibly picked 
sets members is the tricky part.
As we can play with the fragments in our minds easily
this allows for the fun of a design activity.


## Result

Through the nature of the algorithm the names created
always have a particular sound and rhythm.
Different styles can easily be desigened.

With lists of 4-5 fragment sets, each having about 7-8
members on average and fragments of 1-2 characters 
there are several thousand names possible.

I found it most useful to think of a few concrete names
that should be possible and divide them into fragments.
Than add more fragments that fit the fragments
already there.

Some examples of different languages I use:

1. *Topo, Guttigio, Gupo, Luida, Vallerio, Bopo, Pello, Datti, Fulle, Da, Bullirio, Vatti, Luittivino, Gulligio, Pelli, Tollegrino, Luipo, Lui, Luillorio, Vatticesco, Cocollorio, Botti, Bugi, Luigigio, Totti, ...*
2. *Vedok, Ote, Kedukon, Thuvebal, Nenor, Rode, Raro, Rate, Hava, Tatak, Racu, Tato, Kotukon, Thuca, Keceros, Kene, Ador, Yodubal, Rocod, Nerubal, Vede, Neru, Averos, Ogu, Bavebal, Oto, Keta, Vedo, Tadud, Otu, Alo, Nevar, One, Kovokon, Ovak, Kore, ...*
3. *Stinkon, Euchin, Lorber, Stinbe, Benker, Stiuser, Hamd, March, Zardend, Gersin, Gemchon, Maud, Samchen, Ers, Emcher, Bench, Stimchenia, Zarsen, Gemg, Haub, Sand, Margong, Zandon, Zank, Manb, Hankond, Stirb, Mamson, Mark, Lomb, Stinsin, Sambe, Berg, Maus, Lorb, End, Zaug, Stirs,...*

