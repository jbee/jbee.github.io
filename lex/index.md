---
layout: default
title: "Linear EXpressions"
summary: "Simple and fast first match text pattern matching"
categories: [project, parsing]
links: { "issues": "https://github.com/jbee/lex/issues", "github": "https://github.com/jbee/lex" }
tags: [spec, v2, pattern matching, parsing, terminals]
---

# Linear EXpressions

Linear expression are a intuitive pattern language to 
find or check patterns and their end position in byte sequences.
They are simpler, often faster and more predictable than 
[regular expressions](https://en.wikipedia.org/wiki/Regular_expression).

In case of a mismatch they point out the problematic 
position in both pattern and input sequence.

They do not offer features like capturing or input replacement.

## Motivation

Linear expressions are my solution to define patterns 
for terminals in an [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree).
*Terminal* here is understood as any sequence of 
characters (or bytes) that describes one atomic thing. 
A leaf in a parse tree.
Like identifiers or number and string literals.

With the goal of implementing a full general parser 
with very little code the use of regular expressions is 
not an option. 
Besides, they are a complex general tool that is 
hard to predict and master.
A specific solution tweaked to the needs can be simpler 
and more predictable.


## Design
Goal: a minimal set of features that allow to define 
intuitive patterns to match quite complex terminals.

A thread-safe interpreter matching function that is 
allocation-free and has neither global state nor a 
compilation or validation phase.
The function is embeddable into a general parser
and returns the match end position in input and pattern.

The matching does not care about language level 
constraints, like a exact length limitation. 
These can be checked at a later compilation stage.

The implementation does not require further libraries
and is only based on fundamental programing concepts 
that are easy to port to about any language.


## Rules
Rules are byte instructions designed to give the 
appearance of syntax, but there is none.
It is a byte-encoded interpreted language.

### Repetition

* `+`      try previous set, group or literal again

A `+` followed by more `+` is a slower version of one `+`.

----

### Groups

* `(abc)` a sequence `abc` that *must* occur
* `[abc]` a sequence `abc` that *can* occur

Groups can be nested. The opening bracket controls the type of group.
The regex _*_ (zero or more) can be build using `[...]+`.

Open groups at the end of a pattern are implicitly closed.

----

### Embedding

To embed an expression in another byte encoded instruction language the pattern
can be enclosed in backticks: `` `...` ``. The `` ` `` instruction exits the
current group unless it is the first byte in a group or pattern. 
As such it serves as a marker for the beginning of an embedded expression.

----

### Scanning

* `~`      skip input until following set, group or literal matches

A `~` followed by more `~` its a slower version of one `~`.
A `~` followed by a `+` is always a mismatch.


----

### Sets

* `{abc}`   matches `a`,`b` or `c`
* `{a-c}`   matches `a`, `b` or `c` (given as a range)
* `{^ac}`   matches any byte but `a` or `c`

Within a set `{...}` bytes are matched literally, except:
* `\`_x_    matches `x` literally (typical escaping).
* `@`_x_    matches _x ^ @_ (flips the 7th bit and thereby adds 64 for _x_ &lt; 64 and subtracts 64 for _x_ &gt;= 64)
* `?`       matches any non-ASCII byte
* `-`       matches a range (when not at the beginning or following `?`)
* `^`       marks the set as exclusive set (when at the beginning)
* `}`       marks the end of the set

A range's upper and lower bounds are inclusive and can use 
flipped `@`_x_ or escaped `\`_x_ bounds. 
However, the upper bound is always interpreted literally. 

There are extra shorthands instructions for common sets:
* `#`       any ASCII digit (=`{0-9}`)
* `@`       any ASCII letter (=`{a-zA-Z}`)
* `$`       ASCII newline (\n or \r; = `{@J@M}`)
* `_`       any ASCII whitespace character (= `{ @I@J@M}`)
* `^`       any byte but ASCII whitespace characters (= `{^ @I@J@M}`)
* `?`       any single byte (= `{^}`)

----

### Literals

Any other byte (not `` {}()[]#@^_$+~?`\ ``) is matched literally. 
To match instructions literally they can be escaped with `\`.
In general any byte can be escaped (also true in sets).


## Encoding

In general the matching is not (and should not be) aware 
of encoding. This plays well with some encodings, like
UTF-8, and badly with others.

UTF-8 literals can be matched by defining the pattern
in UTF-8 too. Sets can allow any non-ASCII byte 
with `{?...}+` what includes any non ASCII UTF-8 symbol. 
Most often this is sufficient for lexing.


## Principles and Properties

* result is always the **first** match
* matching proceeds left to right (in both input and pattern)
* matching is not line based
* matching never goes backwards for input position
* matching only goes backwards in pattern for `+`
* `+` is always greedy (stops on first mismatch)
* `~` is always non-greedy (stops on first match)
* sets are limited to ASCII (or single bytes)
* `\` escaping can be applied to any subsequent byte anywhere
* there are no modes
* any sequence of bytes is a valid pattern and has defined semantics

Consequently the parser must make progress either in
input or pattern.
Otherwise a mismatch has been found at the current
input position.
This also implies that identifying a mismatch is always 
faster than a successful match.

The worst cases are scanning `~`, optional groups `[...]`
and excessive sets `{...}` that try to match and otherwise 
recover by making progress in either the input (`~`) or 
the pattern (`[...]`, `{...}`).
In all other cases progress is always made in both.
Consequently mismatches are most often identified immediately.

The computational complexity is (almost) never worse than O(n)<sup>1)</sup>. 
In worst case n is the longer length (of input or pattern).
In best case n is the shorter length (of input or pattern).
Whence the name *linear expressions*.

No heap memory is needed to process matching.
A matching function can be written in about 
[100 LOC](https://github.com/jbee/lex/blob/master/java/se/jbee/lex/Lex.java).

The matching is designed to find the **first match**. 
Matching always ends on first not recovered mismatch.
Hence, the greedy `+` will not backtrack in case of a mismatch.

It is by design impossible to find the longest or shortest match as
this would already theoretically contradict the goal properties described above.
Some pattern can be designed so that the first match must as well be the 
shortest or longest but generally this is not possible.


## Examples
Examples of a pattern an the inputs they match:

### Dates

* `####/##/##`: *yyyy/mm/dd*
* `##[##]/##/##`: *yy/mm/dd* or *yyyy/mm/dd*
* `##{-/.}##{-/.}##`: *dd/mm/yy*, *dd-mm-yy* or *dd.mm.yy*
* `#[#]/#[#]/##[##]`: *d/m/yy*, *dd/mm/yy*, *d/m/yyyy* or *dd/mm/yyyy*

---- 

### Times

* `##:##`: *hh:mm*
* `##:##[:##]` : *hh:mm* or *hh:mm:ss*
* `##:##:##[.###]`: *hh:mm:ss* or *hh:mm:ss.SSS* (ms)
* `##[:##][:##].###`: *hh:mm:ss.SSS*, *mm:ss.SSS* or *ss.SSS*

----

### Numbers

* `#+`: simple integers; `1`, `42`, `345`, ...
* `#+[,###]+`: with dividers; `42`, `42,000`, `42,000,000`, ...
* `#+[.#+]`: simple floating points; `0.2`, `10`, `12.45`, ...
* `#+[{_#}+#][{lL}]`: java integer literals; `5L`, `5_3`, `11__12`,
* `[#+[{_,}###]+].#+[fFdD]`: java floating point literals; `1.0f`, `.2`, ...
* `0b{01}+[_+{01}+]+`: java binary literal; `0b0000_1010`, ...
* `0x{0-9A-Fa-f}+[_+{0-9A-Fa-f}+]+`: java hex literals; `0xCAFE_BABE`, ...

----

### Strings

* `"{^"}+"`: quoted string (no escaping); `"foo"`, `"1"`, ...
* `"~"`: quoted string (no escaping); `"foo"`, `"1"`, ...
* `"~({^\}")`: quoted string (escaping); `"foo"`, `"foo\"bar"`, ... 
* `"""~(""")`: triple quoted string; `"""foo"""`, `"""foo"bar"baz"""`, ...

----

### Identifiers

* `@+[{-_}{a-zA-Z0-9}+]+`: general letters and digits; `foo`, `Foo`, `foo-bar`, `fooBar`, `foo_bar`, `foo1`, `foo2bar`, ...
* `\$@{?a-zA-Z0-9_}+`: php style; `$foo`, `$föö`, `$FOO2`, `$foo_bar`

----

### Phone Numbers

* `[\+]#+[{- }#+]+`: local or international; `0150 963852`, `0150-963852`, `+49 12345 698547`, ...


## Philosophy

Linear expressions have no OR construct.
While this makes them (mostly) linear some patterns 
naturally ask for multiple cases.

In such cases a common super-pattern is used that
potentially matches more but does not conflict with
other patterns. Full conformity is then checked in 
later stages of the parsing.

A rough pattern to e.g. match any java number is:

```
{.0-9}[{.xb0-9}[{0-9A-Fa-f_}+][.#+]][{dDfFlL}]
```
It would wrongly match `.` or `..` or `..0.0` or
hexadecimal numbers with a floating point tail.
But it does not accept something as a number that is 
another valid language token.
The number tokens would need an additional check at a 
later stage, similar to a overall length check.

A better solution, however, is to design the language
so that such irregularities do not occur. In case of
java's numbers we could disallow a floating point to 
start with a bare `.`, what would give the language the 
property of all number literals starting with a digit
and the pattern could be simplified to:

```
#[{xb}][{0-9A-Fa-f_}+][.#+]][{dDfFlL}]
```

Therefore the limitation is less considered a problem 
and more a guide to keep a language regular.

Also cases are often better handled as different 
terminal tokens of the language. So in practice cases
do occur on a different level making this a non-issue.

Integers, floats, binary and hexadecimal literals could be:
```
#+
#+.#+
0b{01_}+
0x{0-9A-Fa-f_}+
```
The _OR_ would occur on parser level where they could be
in reverse order (compared to above) to match first correctly.

## Performance
In a very much non-systematic [measurement](https://github.com/jbee/lex/blob/master/java/se/jbee/lex/TestLexPerf.java) using 
[JMH](http://openjdk.java.net/projects/code-tools/jmh/)
the performance is on par with regular expressions in
most cases. 

The compiling phase needed for regular expressions was
not included in the time measured in the assumption 
that this cost usually is only payed once. 
If this cost would be included Lex is most often
faster as it has no such cost.

Consider also that Lex is allocation free while 
RegEx most likely is not.

Scanning through text can be about 5 times faster than 
regular expressions when using a [performance optimisation](perf.html)
for `~`. That one comes at the cost of about [50 LOC](https://github.com/jbee/lex/blob/master/java/se/jbee/lex/Lex.java#L208-L260) --
what is a lot -- considering the whole basic 
implementation is about 100 LOC.

The fast path only works when `~` is followed by
literal printable ASCII symbols `~fo+` or a group 
starting with such a symbol, like `~(fo+)`. 
Deeper nesting would also work, like `~((fo+)bar)` or
even `~(foo~(bar))`.
The speed gain is proportional to the length of the 
literal sequence. A longer sequence is found faster.
Theoretically it can be extended to also work with sets
but that is another 50 LOC or so.
Rough number: a 20 MB text file can be searched in about 100ms.
No [grep](https://www.gnu.org/software/grep/) but pretty good for a small tool.


## Clarifications

On Groups

* `)`, `]` and even `}` are identical and close 
  the currently open group: `(...)` = `(...]` = `(...}`; `[...]` = `[...)` = `[...}`

On Scanning

* `a~b` matches *`axxxb`*`xxb` (darker part). 
  To end the match only on a specific sequence or pattern
  use a group: `a~(bc)` matches *`axxxbxxbc`*`xxbc`.


On Sets

* Incomplete ranges, escapes or flips are ineffective 
(no match), so `{x-}` = `{x}`.
* `{-}`, `{^-}` or `{?-...}` aren't ranges but interpret `-` literally.
* Escaping `\t` is not _TAB_ but matching literally `t`; 
  a _TAB_ can be encoded as `@I` (or use the actual _TAB_ byte).

* As upper bounds of ranges are always interpreted literally 
  `{+-?}` is the range from `+` to `?`.

On Literals

* `\t` is not _TAB_ but literally `t`. 
  Sets provide `@` to encode non-printable ASCIIs with printable ones.
  Or just encode them as the actual non-printable byte.

-----------------
<sup>1)</sup> <small>A pattern with cascaded scans `~(x~(y~z))` on a input with 
a lot of `x` might be as bad as almost O(n<sup>2</sup>). This is both abusing 
the tool and highly unlikely. </small>

