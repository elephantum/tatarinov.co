---
layout: post
title: Optimizing Python program
published: false

---

TLDR for the lazy ones:
---

* If you want pure-python program go faster, use built-in
datatypes instead of inventing your own classes.
* Do not use pure-python for computationally-heavy tasks, you
will be penalized by emulation costs heavily, consider geterogenous
approach whenever possible.

Story of 32s &rarr; 7s Python program optimization.
---

Some time ago
I stumbled upon yet another programming language performance meter:
[gflarity/peg-performance][]. Though author insists on maintaining
exactly the same code style and technique, I would disagree. Each
instrument has it's own nuances in semantics and runtime behavior, my
point is that when used correctly Python is not that different from
Java/C even for such computationally-heavy tasks.

This is a post about optimizing [Python solution][] from 32s to 7s
(using my MacBook Pro as a test machine).

[Python solution]: https://github.com/gflarity/peg-performance/tree/master/src/main/python
[gflarity/peg-performance]: https://github.com/gflarity/peg-performance

First of all a bit about semantics of test program: it is a brute
force solution to [peg solitaire][], **FIXME** which counts the number
of winning positions.

[peg solitaire]: http://en.wikipedia.org/wiki/Peg_solitaire

Initial speed test, w/o any modifications:

    monkeyBook:python elephantum$ python2.6 performance.py
    Games played:      137846
    Solutions found:   1550
    Time elapsed:      32062ms

Interesting fact: PyPy does not speed things up, contrary, makes
program go slower

  * PyPy: 53s
  * CPython: 32s.

Next, make things easier to work with in interactive mode, run under profiler.

    In [1]: import performance
    In [2]: %prun performance.performance()
    ...
       ncalls  tottime  percall  cumtime  percall filename:lineno(function)
       322323   16.437    0.000   40.673    0.000 performance.py:171(legalMoves)
      1332220   10.704    0.000   15.878    0.000 performance.py:77(possibleMoves)
     31287410    9.123    0.000    9.123    0.000 performance.py:131(__eq__)
      7136772    3.114    0.000    3.114    0.000 performance.py:60(__init__)
       323873    2.374    0.000    4.401    0.000 performance.py:137(__init__)
     323873/1    1.964    0.000   47.924   47.924 performance.py:215(search)

Let's examine `legalMoves` function, we can see that it's body
consists mostly from loop with `possibleMoves` call inside. It would
be reasonable to start optimization with `possibleMoves`.

`Coordinate.possibleMoves` is a function which generate list of moves,
possible from this given position regardless occupied state. Function
itself is pretty simple, all it does is filling the list based on
`self.row` and `self.hole` properties.. Last word should be a hint to
optimization. Why? Because property is a function call:
`Coordinate.row` &rarr; `Coordinate.get_row()` &rarr; `self.row`,
function calls are not instant. Interpreter spends time to lookup
symbol, create stack frame, execute code, remove stack frame. While
one property call isn't that slow, but multiplied by million times it
can introduce measurable delay into execution.

Originally author introduced properties to make values read-only (by
specifying only read functions to property constructor). We trust
ourselves, and can drop strict read-only constraint, substituting it
to soft: we promise not to alter properties in runtime.

Change: [get rid of properties](https://github.com/elephantum/peg-performance/commit/e6e56c56a6a54d5ee66757b22c184405acb157ae)

    monkeyBook:python elephantum$ python2.6 performance.py
    Games played:    137846
    Solutions found:   1550
    Time elapsed:     31746ms

A little bit better, but not enough. Another profiler run shows that
time distribution didn't change. Let's go further and eliminate `Move`
and `Coordinate` classes, substituting them to tuples.

Changes: [Move: class -> tuple](https://github.com/elephantum/peg-performance/commit/5d5c47dca432c19a04404a8093739d49132cfcdb) and [Coordinate: class -> tuple](https://github.com/elephantum/peg-performance/commit/d75bb91871c170dfa85ad0773d2f44f57fbbaa63)

    monkeyBook:python elephantum$ python2.6 performance.py
    Games played:    137846
    Solutions found:   1550
    Time elapsed:     11170ms

Cut is so big because we eliminated `__eq__` which alone consumed 9
seconds.

Profile:

      1332220    5.019    0.000    7.990    0.000 performance.py:47(possibleMoves)
       322323    4.230    0.000   12.268    0.000 performance.py:139(legalMoves)
      7136772    1.820    0.000    1.820    0.000 performance.py:39(Coordinate)
       323873    1.720    0.000    2.152    0.000 performance.py:103(__init__)
     323873/1    1.432    0.000   16.654   16.654 performance.py:183(search)
      3568378    0.677    0.000    0.677    0.000 performance.py:36(Move)

Drop in bunch of semantic adjustments. Changes:
[possibleMoves - generator](https://github.com/elephantum/peg-performance/commit/4b5c53cddf782d806b0f69ba7393eecbbd3190a7),
[occupiedHoles - set](https://github.com/elephantum/peg-performance/commit/b4802a016fc9ef01cbb346b33ff97d8b0d69b526),
[split GameState constructor](https://github.com/elephantum/peg-performance/commit/2d804eabbdbe1db3a36d8a84d30fb7a0b32b2a2a)

    monkeyBook:python elephantum$ python2.6 performance.py
    Games played:    137846
    Solutions found:   1550
    Time elapsed:      8872ms

And the last one, eliminate the rest of multi-step tuple
dereferencing. It helps, because there are additional costs for
executing each individual operation. By combining multiple operations
into one we save time on methods lookup, arguments parsing, results
wrapping. Each individual delay is puny, but repeated million times it
introduces significant portion of runtime. Change:
[one assign tuple dereference](https://github.com/elephantum/peg-performance/commit/274542629aabc96f21c5e014c9199967d92bbd72):

    monkeyBook:python elephantum$ python2.6 performance.py
    Games played:    137846
    Solutions found:   1550
    Time elapsed:      6990ms

Conclusion
---

Classes while providing much greater flexibility
penalize performance heavily by providing several layers of
dereferencing. In order to avoid this penalty, it would be reasonable
to use tuples instead of classes in performance critical tasks.

Also, there is an unobvious, but dramatic difference between emulated
and built-in operations. Gathering several explicit operations into
one bulk, whenever possible, will help partially eliminate emulation
delays through relying on single built-in method call instead multiple
emulated operations.

It would be even wiser to avoid python for certain type of operations
and use C-extensions, Cython, whatever-else to implement
performance-critical parts of application. But this approach is out of
scope of this article.
