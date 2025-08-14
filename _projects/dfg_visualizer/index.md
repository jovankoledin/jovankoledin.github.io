---
layout: post
title: Data Flow Graph Visualizer
description:  While building a compiler for a graph based hardware accelearator I realized I had no way to test the execution graphs I was generating. I built this tool to simulate memory interactions and observe the execution results each cycle for a data flow graph. 

main-image: /example.png
---

---
## Example Input
{% include image-gallery.html images="dfg.png" height="500" %}

## Example Visualizer Window
{% include image-gallery.html images="example.png" height="500" %}

## How I Built it
I used Python and Tkinter to make the GUI. The nodes in my graph are represented as classes that get stored by my GUI and tracked with a global memory buffer inside the program. The coolest thing in my opinion is the memory virtualizer that shows how loads and stores interact with an existing memory. I also like the logging feature that shows the results of each node at each cycle.
