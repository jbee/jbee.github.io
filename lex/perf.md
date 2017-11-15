---
layout: default
title: "Byte Golf"
summary: "byte level performance tricks"
categories: [writing, detail, performance]
---

# `byte` Golf

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
It allows to compare for both ranges with one check.


## Use subtraction to check a range
To check that a byte `x` is within a range, like 
`0` to `9`, subtraction can be used. 
The idea is: `unsignedOf(x-lower) <= upper-lower`.

```java
((0xFFFF & (c-'0')) <= 9)
```
Here `0xFFFF &` has the effect of a `unsignedOf`.
`upper-lower` was precomputed to 9.
Makes two checks into one.


## Use `long` to return two `int`s
Java doesn't allow multiple return values. 
Options are a `int[]` or creating a class. 
Both cause heap allocation. 
Use a `long` to avoid it:

```java
static long ab(int a, int b) {
	return (long)a << 32 | b & 0xFFFFFFFFL;
}
a = (int)(ab >> 32);
b = (int) ab;
```


## Use a `long` as a printable ASCII bitmask
A fast way to check if a byte symbol is one of a certain
set of ASCII symbols is a bitmask. 
Printable ASCII range is 32-127. A `long` has 64 bits.
To make this fit the upper-case trick from above is
used to fold 96-127 on 64-95. 32-95 is mapped onto 0-64.

```java
private static long bit(byte b) {
	return 1L << ((b > 95 ? (b & 0xDF) : b) -32);
}
```
This obviously is no exact equivalent but helpful for
a coarse first check. On a match do a exact check.
I might have invented this one. Who knows.


## Skip bytes to find a byte sequence
At first it might be counter intuitive that a longer 
sequence of bytes is easier to find. 
Because not all bytes have to be inspected.
When searching for *Hello* only every 5th byte has to 
be checked to see if it is one of 4 letters: H, e, l ,o.
A bitmask like described above can be used to do that.
On a match, find the start and check for a exact match.
Otherwise continue jumping forward in steps of the
length of the search term.
