---
layout: post
title: Optimizing Python program
draft: true
---

Some time ago I stumbled upon yet another programming language
performance meter: [gflarity/peg-performance][]. Though author insists
on maintaining exactly the same code style and technique, I would
disagree. Each instrument has it's own nuances in semantics and
runtime behavior, my point is that when used correctly Python is not
that different from Java/C even for such computationally-heavy
tasks. This is a post about optimizing [Python solution][] from 25s to
5s (using my MacBook Pro as a test machine).

[Python solution]: https://github.com/gflarity/peg-performance/tree/master/src/main/python
[gflarity/peg-performance]: https://github.com/gflarity/peg-performance

First of all a bit about semantics of test program: it is a brute
force solution to [peg solitaire][], **FIXME** which counts the number
of winning positions.

[peg solitaire]: http://en.wikipedia.org/wiki/Peg_solitaire
