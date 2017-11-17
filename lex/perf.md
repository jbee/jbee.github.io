---
layout: default
title: "Byte Golf"
summary: "byte level performance tricks"
categories: [writing, detail, performance]
---

# Byte Golf

* performance
* optimisation
* low-level

A very brief overview of optimisations that are (or can
be) used to optimize `int` or `byte` based computations 
like those done by [Lex](index.html).

All of these tricks have been around for ages.
Nothing new. This is just a brief summary.
They work for most languages. Here given for Java.
Long explanations can be found elsewhere. Or ask me.


## Use `&` to upper case ASCII letters
`a`-`z` can be turned into `A` to `Z` by:

```java
letter & 0xDF
```
Gain: compare both ranges with one check.


## Use subtraction to check a range
To check that a byte `x` is within a range, like 
`0` to `9`, subtraction can be used. 
The idea is: `unsignedOf(x-lower) <= upper-lower`.

```java
((0xFFFF & (c-'0')) <= 9)
```
Here `0xFFFF &` has the effect of a `unsignedOf`.
`upper-lower` was precomputed to 9.
Gain: two checks as one.


## Use `long` as `int` pair
Java doesn't allow multiple return values. 
Options are a `int[]`, a class or using long:

```java
static long ab(int a, int b) {
	return (long)a << 32 | b & 0xFFFFFFFFL;
}
a = (int)(ab >> 32);
b = (int) ab;
```
Gain: no heap allocation.


## Use `long` as bitmask for printable ASCII
Printable ASCII range is 32-127.
To make this fit `long`'s 64 bit the upper-case trick 
from above is used to fold 96-127 on 64-95. 
Finally 32-95 is mapped to 0-64.

```java
private static long bit(byte b) {
	return 1L << ((b > 95 ? (b & 0xDF) : b) -32);
}
```
Gain: heap free fast bitmask for ASCII.

Helpful for a coarse first check. On a match do a exact check.
I might have discovered the folding. Who knows.


## Hop to find a byte sequence
At first it might be counter intuitive that a longer 
sequence of bytes is easier to find. 
Because not all bytes have to be inspected.
When searching for *Hello* only every 5th byte has to 
be checked to see if it is one of 4 letters: H, e, l ,o.
A bitmask (described above) can be used to do that.

On a match, find the start and check for a exact match.
Otherwise continue hopping forward in steps of the
length of the fix search term.

Gain: n times speedup for text seach (n term length).
