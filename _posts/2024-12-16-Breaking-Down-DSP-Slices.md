## Breaking Down DSP Slices

If you have spent any time digging into FPGAs you have probably seen the acronym “DSP” in a spec sheet or chip description. You also probably already know they stand for “Digital Signal Processor” and might help with speeding up computations in your FPGA. But if you are like me at all then you probably have some questions about them. In this post I will take apart one of the most popular DSP slices to shine a light on the black box of their architecture and use cases. Why are they so ubiquitous in modern FPGA architectures? How do they work? Even if I don't want to do any “Digital Signal Processing”, why is my FPGA utilizing some of them after I synthesize my HDL? How does the Vivado compiler/synthesis tools decide when to employ them to implement my RTL design?

### How do DSP Slices Work?

We are going to take a deeper dive into the AMD/Xilinx 7 Series DSP48E1 which is shown below in Figure 1. From their spec sheet [1](https://docs.amd.com/v/u/en-US/ug479_7Series_DSP48E1): “FPGAs are efficient for digital signal processing (DSP) applications because they can implement custom, fully parallel algorithms. DSP applications use many binary multipliers and accumulators that are best implemented in dedicated DSP slices.”. From this quote we see the high level demand for why hardened dedicated DSP blocks would be implemented into FPGAs, but we can also start to get an idea for how they might work at a deeper level. 

![Alt text](/images/DSP/Figure1.png)  
Figure 1. Basic DSP48E1 Slice architecture [[1]](https://docs.amd.com/v/u/en-US/ug479_7Series_DSP48E1)  

Ok, now that we have a higher level understanding behind their demand and have taken a glance at their architecture, lets dig into the features of the DSP slice in question. From the datasheet [[1]](https://docs.amd.com/v/u/en-US/ug479_7Series_DSP48E1) section Simplified DSP48E1 Slice Operation: “The math portion of the DSP48E1 slice consists of a 25-bit pre-adder, a 25-bit by 18-bit two’s complement multiplier followed by three 48-bit datapath multiplexers (with outputs X, Y, and Z). This is followed by a three-input adder/subtractor or two-input logic unit” From this I hear there are three main things we can do with the DSP slices: Do two or three input addition/subtraction (pre-adder and three-input adder subtractor), do 25-bit by 18-bit multiplication, and implement a two-input logic unit (similar to the LUTs we discussed in my earlier post about FPGAs). If we refer to Figure 2 we can see how these functionalities are implemented with the inputs, outputs and multiplexers. A, and D feed directly into the pre-adder, then either one of them is most likely used in the 25-bit by 18-bit multiplier against our B input. We then can implement the 2-input logic unit or 3 input adder/subtractor with our X, Y, Z multiplexer outputs at the circle unit junction that contains the three different signs (logic, +, -). 

![Alt text](/images/DSP/Figure2.png)  
Figure 2. Simplified DSP Slice Operation [[1]](https://docs.amd.com/v/u/en-US/ug479_7Series_DSP48E1)  

Looking at Figure 2 more we can see that the control signals to our multiplexers, ALUs, and D-FFs are left out. For a more detailed implementation we glance at Figure 3, below.

![Alt text](/images/DSP/Figure3.png)  
Figure 3. 7 Series FPGA DSP48E1 Slice [[1]](https://docs.amd.com/v/u/en-US/ug479_7Series_DSP48E1)  

From Figure 3 we can see the size of our input signals: The A data port is 30 bits wide, the B data port is 18 bits wide, the C data port is 48 bits wide, and the preadder D data port is 25 bits wide. Also, it shows us that there are many more inputs besides the B, A, D, and C ones, the implementation of carry signals makes sense if we want to chain a DSP slice with other ones or with Configurable Logic blocks to perform operations of a size beyond the defined ones for one slice. 

Hopefully this section could clear up some of your questions about how the DSP slices work at a high level and what their basic features and uses are. To review a little, they are useful for larger bit width multiplication, addition, subtraction, and logic operations that aren’t natively supported by typical LUTs. They can be configured for these different use cases by control signals and can also be chained together if a user wishes to perform even larger bit-width operations. 

### On HDL Synthesis and DSP Utilization

Now that we understand what is going on under the hood a little better for our DSP48E1s we can move up the stack to explore how the Xilinx Synthesis engine chooses to employ a DSP slice. We will be referencing the XST User Guide for Virtex-6, Spartan-6 and 7 Series Devices [[2]](https://docs.amd.com/v/u/en-US/xst_v6s6). From this User Guide: “The XST software: 1. Takes the description of a design in an HDL (VHDL or Verilog) file. 2. Converts it to a synthesized netlist of Xilinx technology-specific logical resources” Essentially XST will read our Verilog or VHDL code and decide which resources on our FPGA need to be recruited to implement the desired functionality our code demands.  

Looking through the User Guide with ‘Ctrl F’ and searching for “DSP” we start to see which functions the XST recruits DSP Blocks for. The first one is for Counters and Accumulators, Counters usually count the number of occurrences of a specific event, and Accumulators usually store the sum of anything. In your code they might look like the below example from the XST User Guide:  

![Alt text](/images/DSP/Figure4.png)  
If the counter or accumulator can fit on one DSP block then the XST engine will usually choose to use a DSP block to implement it, however if it is too big then it will usually just choose to use the typical LUT-FF CLB Slices. By default, standalone adders, subtractors, and adder/subtractors are not implemented with DSP slices. If a user wants more control over when to use DSP slices or not then Implementation of macros on either slice logic or DSP block resources can be controlled by Use DSP Block which has a default value of auto, to force implementation of adders, counters or accumulators on DSP blocks set Use DSP Block to yes.  Another bonus piece of info I found after rooting through the User Guide was that DSP Blocks by default assume signed operands and are usually recruited for signed operations. 

#### Multipliers

By default XST tries to implement multipliers with DSP blocks, but its operands must have a minimum size. The minimum size can vary depending on the device family. Also, if the engine can optimize performance by not using a DSP block then it will implement the multiplier with a slice. Also from the XST User Guide[[2]](https://docs.amd.com/v/u/en-US/xst_v6s6) “When implementing a Multiplier in a single DSP block, XST tries to take advantage of the pipelining capabilities of DSP blocks. XST pulls up to two levels of Registers present: 
- On the multiplication operands 
- Behind the multiplication”

During Multiply-Add and Multiply-Accumulate operations XST will try to utilize the pipelining abilities of DSP blocks by employing registers at every level of operation. By default all MAC operations will be performed on DSP blocks, but if the MAC operation exceeds the size of a single DSP block then it will split up the operation into two and make independent decisions on each. See below for an example MAC operation from the XST User Guide:  
![Alt text](/images/DSP/Figure5.png)   

In summary, DSP resource utilization is most common when employing: counters, accumulators, multiply-add, and multiply-accumulate Macros. However if the bit-width of these operations exceeds what can fit on one DSP block then the Synthesizer will usually outsource the operations to multiple LUT-FF slices. 

#### Sources:
1. https://docs.amd.com/v/u/en-US/ug479_7Series_DSP48E1
2. https://docs.amd.com/v/u/en-US/xst_v6s6
