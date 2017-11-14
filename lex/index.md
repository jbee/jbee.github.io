---
layout: default
title: "Lex"
categories: [project, parsing]
links: { "issues": "https://github.com/jbee/lex/issues", "github": "https://github.com/jbee/lex" }
---

# *L*inear *ex*pressions

* spec
* pattern matching
* parsing
* terminals

A language designer often describes patterns for 
terminals for a general parser that outputs an [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree).

Here *terminal* is understood as any sequence of 
characters that is one atomic thing. 
A leaf in a parse tree.
Like identifiers, numbers or string literals. 

Question: **Does the input start with a pattern that 
makes it a certain terminal and where does it end?**

With the goal of implementing a full general parser 
in very little code the use of regular expressions is 
not an option. 
Besides, they are a complex hard to predict general tool.
A specific solution tweaked to the needs can be simpler 
and more predictable.


## Design
The goal is a minimal set of features that allow to
define intuitive patterns to match quite sophisticated 
terminals.

The algorithm should have a single interpreter matching 
function that is state- and allocation-free to be both 
simple and fast. The design is obviously thread-safe.

Properties that can be tested on identified terminals,
like length limitations, are language specific 
limitations and that can be checked at a later stage.


## Rules
Rules are byte instructions designed to give the 
appearance of syntax, but there is none.
It is a byte-encoded interpreted language.
Thou some instructions are no-ops used as markers.

Character sets:

* `{abc}`   a set of bytes `a`,`b` and `c`
* `{^ac}`  a set of any byte but `a` and `c`
* `{a-c}`   a set of `a`, `b` and `c` given as a range
* `}a-c{`   range of `a` to `c` and all non ASCII bytes
* `#`       any ASCII digit (=`{0-9}`)
* `@`       any ASCII letter (=`{a-zA-Z}`)
* `_`       any ASCII whitespace character
* `^`       any byte that is not an ASCII whitespace character
* `*`       any single byte
* `$`       any non ASCII byte (`1xxx xxxx`)

All bytes within a set `{`...`}` are literal.
Hence, `}` cannot be included in a set explicitly.
`{^}` matches `^` while any set starting with `^` 
defining further members is exclusive.

Repetition:

* `+`      try previous set, group or literal again

Groups:

* `(abc)`  a group with sequence `abc` that *must* occur
* `[abc]`  a group with sequence `abc` that *can* occur

Groups are most useful when nested and used in 
combination with `+`, like `(a[b(c)+])`.
A regex `*` can be build using `[x]+`.

Scanning:

* `~`      skip until following set, group or literal matches

Clarification: `a~b` matches *`axxxb`*`xxb` (green part). 
To end the match only on a specific sequence or pattern
use a group: `a~(bc)` matches *`axxxbxxbc`*`xxbc`.

Any other byte (not *{}()[]#@^_$+~*) is matched 
literally. 
Sets can be used to match any of the instruction symbols 
literally, like `{~}`.


## Encoding

In general the parsing is not (and should not be) aware 
of encoding. This plays well with some encodings, like
UTF-8, and badly with others.

UTF-8 literals can be matched by defining the pattern
in UTF-8 as well. Sets can only allow `}...{` or  
disallow `{...}` any non ASCII byte what includes or
excludes any non ASCII UTF-8 symbol. 
Most often this is sufficient for lexing.


## Principles and Properties

* a match is always sufficient 
* `+` is always greedy (stops on first mismatch)
* `~` is always non-greedy (stops on first match)
* there is no backtracking
* sets are limited to ASCII (a single byte)

Consequently the parser must make progress either in
input or pattern.
Otherwise a mismatch has been found at the current
input position.

The worst cases are scanning `~` and optional groups `[...]`
that try to match and otherwise recover by making progress
in either the input (`~`) or the pattern (`[...]`).
In all other cases progress is always made in both.
Almost always mismatches are identified immediately.

The computational complexity is O(n). 
In worst case n is the longer length (of input or pattern).
In best case n is the shorter length (of input or pattern).
No heap memory is needed to process matching. 
A matching function can be written in about 
[100 LOC](https://github.com/jbee/lex/blob/master/java/se/jbee/lex/Lex.java).


## Examples
Examples of a pattern an the inputs they match:

Dates

* `####/##/##`: *yyyy/mm/dd*
* `##[##]/##/##`: *yy/mm/dd* or *yyyy/mm/dd*
* `##{/-.}##{/-.}##`: *dd/mm/yy*, *dd-mm-yy* or *dd.mm.yy*
* `#[#]/#[#]/##[##]`: *d/m/yy*, *dd/mm/yy*, *d/m/yyyy* or *dd/mm/yyyy*

Times

* `##:##`: *hh:mm*
* `##:##[:##]` : *hh:mm* or *hh:mm:ss*
* `##:##:##[.###]`: *hh:mm:ss* or *hh:mm:ss.SSS* (ms)
* `##[:##][:##].###`: *hh:mm:ss.SSS*, *mm:ss.SSS* or *ss.SSS*

Numbers

* `#+`: simple integers; `1`, `42`, `345`, ...
* `#+[,###]+`: with dividers; `42`, `42,000`, `42,000,000`, ...
* `#+[.#+]`: simple floating points; `0.2`, `10`, `12.45`, ...
* `#+[{_#}+#][{lL}]`: java integer literals; `5L`, `5_3`, `11__12`,
* `0b{01}+[_+{01}+]+`: java binary literal; `0b0000_1010`, ...
* `0x{0-9A-Fa-f}+[_+{0-9A-Fa-f}+]+`: java hex literals; `0xCAFE_BABE`, ...

Strings

* `"{^"}+"`: quoted string (no escaping); `"foo"`, `"1"`, ...
* `"~"`: quoted string (no escaping); `"foo"`, `"1"`, ...
* `"~({^\}")`: quoted string (escaping); `"foo"`, `"foo\"bar"`, ... 
* `"""~("""")`: triple quoted string; `"""foo"""`, `"""foo"bar"baz"""`, ...

Identifiers

* `@+[{-_}{a-zA-Z0-9}+]+`: general letters and digits; `foo`, `Foo`, `foo-bar`, `fooBar`, `foo_bar`, `foo1`, `foo2bar`, ...
* `{$}@}a-zA-Z0-9_{`: php style; `$foo`, `$föö`, `$FOO2`, `$foo_bar`

Phone Numbers

* `[{+}]#+[{ -}#+]+`: local or international; `0150 963852`, `0150-963852`, `+49 12345 698547`, ...


## Philosophy

Complex patterns might have multiple cases.
In such cases a common super-pattern is used that
potentially matches more but does not conflict with
other patterns. Full conformity is then checked in 
later stages of the parsing.

A (simplified) pattern to e.g. match any java number is:

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
so that such irregularities do not exist. In case of
java's numbers we could disallow a floating point to 
start with a bare `.`, what would give the language the 
property of all number literals starting with a digit.

Therefore the limitation is less considered a problem 
and more a guide to keep a language regular.

Also cases are often better handled as different 
terminal tokens of the language. So in practice cases
do occur on a different level making this a non-issue.


## Performance
In a very much non-systematic [measurement](https://github.com/jbee/lex/blob/master/java/se/jbee/lex/TestLexPerf.java) using 
[JMH](http://openjdk.java.net/projects/code-tools/jmh/)
the performance is on par with regular expressions in
most cases. 

The compiling phase needed for regular expressions was
not included in the time measured in the assumption 
that this cost usually is only payed once. 
If this cost would be included Lex is most likely
faster as it has no such cost.

Scanning through text can be about 5 times faster than 
regular expressions when using a performance optimisation 
for `~`. That one comes at the cost of about [40 LOC](https://github.com/jbee/lex/blob/master/java/se/jbee/lex/Lex.java#L173-L221) --
what is a lot -- considering the whole unoptimized 
implementation is about 100 LOC.
The fast path only works when `~` is followed by
literal printable ASCII symbols `~fo+` or a group 
starting with such a symbol, like `~(fo+)`. 
Deeper nesting would also work, like `~((fo+)bar)`.
The speed gain is proportional to the length of the 
literal sequence. A longer sequence is found faster.
Theoretically it can be extended to also work with sets
but that is another 40 LOC or so.
