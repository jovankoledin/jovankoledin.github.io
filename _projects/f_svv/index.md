---
layout: post
title: Verilog Visualizer
description: Xilinx Vivado block level view withought needing Xilinx Vivado


main-image: /serdes.png
---

---
## System Verilog Visualizer (SVV)
Reads your SystemVerilog or Verilog (SV/V) code and shows you a visualization of its module heirarchies.  
Inspired by the block level view that Xilinx Vivado automatically gives you of your HDL code.  
With SVV you don't need to install a 3GB+ software package and deal with licensing to examine your large SV/V projects from a high level.

### How to Use (WSL)
```bash
wsl
pip install graphviz
git clone https://github.com/jovankoledin/system_verilog_visualizer.git
cd system_verilog_visualizer
./build.sh <input.sv> <output_name_no_extension>
```
Output will be in: "output_name_no_extension.png".  
If you have X11 or Wayland running the .png will also be displayed automatically.

### Example output
The exampe SV file has a serdes_top module that contains a serdes_deserializer and serdes_serializer module inside it.
{% include image-gallery.html images="serdes.png" height="500" %}  


[Repo link](https://github.com/jovankoledin/system_verilog_visualizer/tree/main)
