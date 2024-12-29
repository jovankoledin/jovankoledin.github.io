## Paper Review: A Reconfigurable Architecture for 3-D CMOS/Nanomaterial Hybrid Digital Circuits (2007)

I was exploring the most cited FPGA papers by patent citations, and came across this interesting paper that covered an area I had very little knowledge of, materials science. I wanted to understand the technology being proposed in this paper and its underlying fundamentals better so I decided to make a post about it here. I will attempt to summarize the paper and technology being proposed and provide some naive commentary on why I think it was so impactful. To perform this search I used [IEE Xplore](https://ieeexplore.ieee.org/search/searchresult.jsp?action=search&matchBoolean=true&queryText=(%22All%20Metadata%22:FPGA)&highlight=true&returnType=SEARCH&matchPubs=true&sortType=patent-citations&returnFacets=ALL) and searched for ‘FPGA’ and used Sort By -> Most Cited By Patents.

Chen Dong Et al. introduced a novel CMOS FPGA stacking method that used modern material applications such as carbon nanotube bundles and nanowire crossbars to achieve a 4x footprint reduction and a 2.6x performance gain. Some papers before this one proposed the use of nano materials and some proposed the idea of stacking 2D layers. But none before proposed the use of both novel methods in combination.

### Routing and Memory 

The biggest performance and power bottlenecks for an FPGA lies in its programmable interconnects, they are responsible for up to 80% of the total delay and 85% of the total power consumption. The resistivity of copper interconnects increases as you reduce the dimensions due to electron surface scattering and grain-boundary scattering. The nano materials used in this paper's architecture are rolled-up seamless cylinders of graphene sheet made of benzene-type hexagonal carbon rings, which have a constant resistance without the scattering effects of Copper. The name of the new carbon nanotubes used in this design are called Single Walled Carbon Nanotube bundles (SWCNT) which are shown in Figure 1. The carbon nanotubes outperform Copper in propagation delay and current carrying capability (100x better). These foundational improvements over copper account for much of the logic density, interconnect and power performance, and thermal behavior gains in this architecture. 

![Alt text](/images/PaperRev1/Figure1.png)  
Figure 1. SWCNT bundle vias [[1]](https://ieeexplore.ieee.org/document/4383239)  

The SWCNT vias also provide increased thermal conductivity and lower max temp rise when compared to the traditional copper connections, which are visualized in Figure 2. The thermal conductivity is over 15x higher than copper, which can lead to overheating issues. By integrating SWCNT bundle vias with copper interconnects the temperature rise is much lower as shown in Figure 2. This thermal property of the SWCNT bundle is specifically useful for 3-D ICs to combat thermal penalty. Large bundles of SWCNTs can be used as thermal vias to directly connect to the heat sink and efficiently dissipate the excessive heat

![Alt text](/images/PaperRev1/Figure2.png)  
Figure 2. Max. temperature rise for Cu and SWCNT bundle vias [[1]](https://ieeexplore.ieee.org/document/4383239)  

The next nanomaterial used in this paper's architecture is used for memory and routing applications. Nanowire crossbars were a recently developed technology by HP research labs that can route signals and simultaneously store data (NRAM). From the paper: “Recent progress of memory design in nanotechnology leads to the implementation of CNT memory (NRAM) using photolithography. This nonvolatile nanotube random-access memory is faster and denser than DRAM. It has much lower power consumption than DRAM or flash and has similar speed to SRAM” Cheng Dong Et al. think that using NRAM is a good candidate for implementing block memory. The NRAM in this paper is implemented using nanowire crossbars with a hysteretic  resistive block in between crossovers as shown in the two terminal nanodevices in Figure 3. The memory devices are easily programmed and useful for routing elements, allowing them to provide significant improvements over SRAM and traditional routing elements. 

![Alt text](/images/PaperRev1/Figure3.png)  
Figure 3. Nanowire crossbar [[1]](https://ieeexplore.ieee.org/document/4383239)  

### 3D Architecture

Baseline previous 2D FPGA designs consisted of many tiles, where each tile contained one switch block (SB) two connect blocks (CB) and one configurable logic block (CLB). They were connected through a global routing structure which consisted of 2-D segmented interconnect channels. Figure 4 shows a common 2D FPGA architecture with a number of tiles each containing the SB, CB and CLB elements.

![Alt text](/images/PaperRev1/Figure4.png)  
Figure 4. Baseline FPGA schematic [[1]](https://ieeexplore.ieee.org/document/4383239)  

For more details on how the CLBs work see my first post on FPGAs. But I do want to briefly dig into the SB and CB architectures, in Figure 5 we can see their typical designs. Figure 5a shows two ways of wiring CBs, one with pass transistors and the other with a mux. For the SB in Figure 5b we can see that wires from four directions (each wire represents one track in the horizontal or vertical routing channels) are connected through bi-directional tri-state buffers.

![Alt text](/images/PaperRev1/Figure5.png)  
Figure 5. (a)  Two designs of CB connections. (b) One design of SB connections [[1]](https://ieeexplore.ieee.org/document/4383239)  

The 3D method in this paper is implemented by creating vias through different layers containing tiles of CBs, SBs, and CLBs.”The large 2-D footprint of the FPGA is efficiently distributed into three layers of 3-D nFPGA. 3-D nFPGA consists of a 3 1/2-layer structure, which can integrate the CMOS-based logic devices, nanowire-based memory/routing elements, post-silicon block memories and CNT-based vias in three dimensions” Refer to Figure 6 to see how a 2D FPGA turns into a 3D one.

![Alt text](/images/PaperRev1/Figure6.png)  
Figure 6. (a)  2-D baseline FPGA becomes (b) 3 1/2 layer 3-D nFPGA [[1]](https://ieeexplore.ieee.org/document/4383239)  

The communications between all layers is done via the CNT vias we discussed earlier. For an example connection between layer 1 and 2 you can look to Figure 7. RSB stands for (Reduced Switch Block) they are calling it reduced because it takes up less space than the previous SBs due to the implementation of their nanowire crossbars that save space due to moving all the programming SRAM cells of the switch blocks to the Crossbar Layer and implementing them by the nanowire crossbar memories. The 3 ½ layer stack can be further stacked to implement even more performant architectures; two of the inner 2 ½ layer stacks (3 ½ minus NRAM) can be stacked back to back easily. 

![Alt text](/images/PaperRev1/Figure7.png)  
Figure 7. Layer 1, crossbar layer, and layer 2 [[1]](https://ieeexplore.ieee.org/document/4383239)  

The rest of the paper dives into further interconnect and deeper 3D architecture details. It then goes into analysis/simulation techniques to detail how they measured delay and power consumption. No actual chip was fabbed during this research process but they did a seemingly solid job of simulation.  

### Conclusion

The main contribution of this paper is the combination of materials science and FPGA architecture breakthroughs; first, the implementation of Carbon Nanowire Tubes (CNT)  instead of copper for wire and interconnect material, second, stacking of multiple 2D layers to create a single 3D chip. The novel combination of these methods resulted in a 2.65x increase in Fmax (max clock frequency) with ‘small’ power consumption when compared to the traditional CMOS baseline 2-D FPGAs. The CNTs were used for connections in the FPGA, the NRAM storage, and the vias between layers. The CNT are better than copper wires in almost every way, higher conductivity, higher thermal conductivity, higher current carrying capability, and lower propagation delay, they also have constant resistance during downscaling which eliminates the problem of electron surface scattering encountered with copper wires. The 3D stacking of multiple 2D layers resulted in increased performance without an increase in surface area, it also shortened the distance between CLBs in stacked layers which resulted in reduced propagation delay and increased performance.

I understand why this paper is the 10th most cited FPGA paper by patents, the performance and power gains from this new architecture and use of nanomaterials is very impressive. It is also interesting how this paper does not propose completely novel technologies, just the combination of two new ones that were developed by other people. It is good to know that if I am going to do my own research it doesn't have to contain 100% novel contributions, I can just build on developments from others. If I am going to do more paper reviews on this page I should probably pick some with less information. This paper was incredibly dense and took me way more time to get through than I thought it would have. I also didn't get to go into the deeper testing, and architecture details just because I didn't want this post to get too long and I didnt think those details were completely necessary for the average reader to understand the contributions of the paper. If I am to do more paper reviews that go more in depth on each contribution by the authors, the papers will have to be simpler. 

#### Sources

1. C. Dong, D. Chen, S. Haruehanroengra and W. Wang, "3-D nFPGA: A Reconfigurable Architecture for 3-D CMOS/Nanomaterial Hybrid Digital Circuits," in IEEE Transactions on Circuits and Systems I: Regular Papers, vol. 54, no. 11, pp. 2489-2501, Nov. 2007





