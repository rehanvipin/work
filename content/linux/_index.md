---
title: "Linux stuff"
weight: 4
bookFlatSection: true
bookToc: false
bookCollapseSection: true
---
# How does computer software work?

Especially the ones running on a Linux OS.

## TODO
1. Linux Audio.
How does it work, how does it break so easily, and how can it be fixed.
Understand output of `inxi -SMAz`

## OS?
An OS is software that manages the computer's resources:

- CPU (by scheduling procs)
- Memory (virtual mem, sharing mem etc)
- Disk (via file system)
- Peripheral devices (provide abstraction to talk to device)
- Networking (resource sharing over net)

In multithreaded programs, threads could run on different CPUs.
Some fancy machines might have multiple processors. A processor may have multiple cores. A core could run multiple threads via hyperthreading.