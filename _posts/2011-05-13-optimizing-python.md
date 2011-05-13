---
layout: post
title: Optimizing Python program
---

Some time ago I stumbled upon yet another programming language performance meter: [gflarity/peg-performance](https://github.com/gflarity/peg-performance). Though author insists on maintaining exactly the same code style and technique, I would disagree. Each instrument has it's own nuances in semantics and runtime behavior, my point is that used correctly Python is not that different from Java/C even for such computationally-heavy tasks. This is a post about optimizing [Python solution](https://github.com/gflarity/peg-performance/tree/master/src/main/python) from 25s to 5s (using my MacBook Pro as a test machine).

Second para.