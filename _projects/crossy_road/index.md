---
layout: post
title: Crossy Road ASIC
description:  Did in an ASIC design with two friends for fun. We were able to design a playable version of Crossy Road that could be hooked up to a VGA display. 

main-image: /gds.png
---

---
## Running on FPGA
{% include image-gallery.html images="crossy_road.gif" height="500" %}

## Chip
{% include image-gallery.html images="gds.png" height="500" %}

## How We Built it
Used SystemVerilog and the SkyWater 130nm toolchain and Yosys/OpenLane for GDS generation. I developed the core game logic and optimized the size from 5000+ logic elements to ~1,150.  
Tapeout and results will finish in mid 2026.
Matt Chen and Ryan Leahy did it with me
