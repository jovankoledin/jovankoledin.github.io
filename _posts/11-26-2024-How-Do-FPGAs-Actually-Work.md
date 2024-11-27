## How do FPGAs Actually Work?
Jovan Koledin

I have read many articles and watched a lot of Youtube videos explaining the basics of FPGAs and how to write software for them.   
They usually start by explaining LUTs and D-Flip Flops and maybe even Block Memory. They then might go into the parallel nature   
of FPGA computation and software execution. They give you some pointers for how to make the transition from writing software   
for processors that execute instructions sequentially (CPUs) to writing software for the parallel processing capabilities of FPGAs.  
But I always felt a little unsatisfied after these explanations and still had lingering questions in the back of my mind. How do  
these different FPGA building blocks come together to execute an instruction specified by our VHDL code (like add)? How exactly   
does a FPGA execute your textual code/instructions in parallel? Where does your compiled bitstream go once you generate it and   
how does it create the connections inside the FPGA? How are the LUTs and Flip Flops actually made, with Transistors? In this post  
I will try to answer all of these questions to help you gain a more intuitive and fundamental understanding of FPGAs.

### How are FPGAs made and what does a typical architecture look like?

To answer my earlier question, yes, FPGAs are made up of millions or billions+ of Transistors. We know that chip manufacturers   
like TSMC can take a digital design for a chip and etch the huge number of transistors onto a silicon wafer and package the   
silicon wafers into the tiny chips we see on our circuit boards. Transistors are essentially little On/Off switches and when   
combined together can make any type of logic, think adders, multipliers, storage elements, or even switch boxes. Ok, so now  
we know that we can create different types of logic elements such as adders, multipliers, storage elements, and switch boxes  
using transistors let's take a closer look at an example FPGA architecture to see how these elements come together:

![Alt text](images/FPGA/Figure1.png)  
Figure 1. Generic FPGA architecture [1]  

In Figure 1 (above) you can see a very simplified architecture diagram of a FPGA chip. There are three key components highlighted,  
Logic Blocks, Interconnection Resources, and I/O Cells. In this post I will not talk much about the I/O Cells but you can just think  
about them as the handlers for the incoming signals to the FPGA pins. Now back to the Logic Blocks and interconnection resources,  
what are they, and how are they made?

### Logic Blocks

![Alt text](images/FPGA/Figure2.png)  
Figure 2. Simplified logic block/cell example [2]  

In Figure 2 (above again :])  we can see a more detailed view of a simplified logic block (LB), logic cell (LC) is the same thing,   
as well as a configurable logic block (CLB), or logic element (LE) depending on the naming convention. Inside this LB there are usually  
four key components that we will discuss more here, the ‘3-LUT’ which is a three input Look-Up-Table, the ‘FA’ which is a Full-Adder,  
the ‘mux’ which is a multiplexer, and the ‘DFF’ which is a D-Flip-Flop. Each of these components come together in a Gate Array format  
(think 2d array) to give the FPGA its signature Field Programmability, or in layman's terms: reconfigurability. 

### Look Up Table (LUT)

![Alt text](images/FPGA/Figure3.png)  
Figure 3. Look-Up-Table (LUT) Architecture [3]  

Let's start with the ‘3-LUT’, we usually just call these look up tables. In Figure 3 (above :)) we can see the architecture of a basic 3-LUT,  
there are the three input pins (‘3’) A, B, C and a Output pin. We also have 8 2:1 Mux’s or multiplexers and some 8 bit SRAM (Static Random   
Access Memory) broken out into 8 pins, if you remember from earlier we saw the multiplexers in our LB diagram. Since we have seen the Mux’s  
before we should start by talking about those first. Multiplexers are essentially little switch boxes. The 2:1 Mux’s in Figure 3 have 4 pins.  
Two input pins that go to the 1 and 0, a set pin that goes into the bottom and an output pin (the one on the right). Below is the truth table  
for one of these multiplexers:

![Alt text](images/FPGA/Figure4.png)  
Figure 4. 2:1 Multiplexer Truth Table [5]  

Multiplexers act as switch boxes by routing the information from one of the input pins to the output pin (Y) depending on the state of the Set pin (S0).  
That's cool and all but how do these help us with the LUT? If we look back at Figure 3 we can see that 8 of them are connected together in a   
kind of pyramid shape, when we change our input signals (A, B, C) we are essentially selecting a single SRAM bit (we will get to SRAM in a second,  
just think of it as storage) to be sent to the Output. For example if we input 001 where C is the least significant bit, into our 3-LUT we will get   
the data stored in index 4 of our SRAM which can either be a 1 or 0. You are now probably confused and still wondering how that helps us execute   
an instruction like adding two numbers which we might specify in our HDL code. Lets now explain the second key component of the LUT, Static Random  
Access Memory (SRAM). 

![Alt text](images/FPGA/Figure5.png)  
Figure 5. SRAM Architecture [4]  

In Figure 5 (above :-)) we can see an example architecture for a 8 8 bit SRAM. SRAM is a type of random-access memory (RAM) that uses flip-flops   
to store each bit, SRAM is static (S) which means it maintains its state in the presence of power, but it’s volatile which means once power is turned   
off it loses its state. Each SRAM cell is usually made up of six MOSFET transistors (6T SRAM cell). Each bit gets stored using a flip-flop formed by  
four transistors with the other two transistors being access transistors that serve to control the access to the flip-flop during read and write   
operations. We can write/read to a SRAM memory cell by setting the Row and Column Address wires. 

So now that we know how multiplexers and SRAM work we can understand how the LUT works. It is used to read specific bits of data from the SRAM using   
the 8 different multiplexers and the input (A, B, C)  wires to perform a logic operation. For example if we wanted to create a LUT that returned a 1  
when the C line was high and the A and B lines were low we would simply have to write a 1 to the 4 index in our SRAM using the column and row address  
lines in Figure 5. Now that we understand the LUTs and Mux’s it is time to move on to another component in our logic block from figure 2, the Full   
Adder (FA).

### Full Adder (FA)

![Alt text](images/FPGA/Figure6a.png)  
![Alt text](images/FPGA/Figure6b.jpg)  
Figure 6. Full Adder Truth Table [6]  

The logic behind a full adder is shown in Figure 6 (above…) It consists of three inputs pins with the C - IN denoting a carry signal that could be   
from a previous adder if there are multiple chained together. The reason FPGAs have a dedicated FA circuit and dont just use more complex LUTs to  
accomplish the adder logic is due to the commonality of the adding operation and increased speed you can gain by creating a hard FA block a opposed  
to relying on a slower and more spatially intensive memory lookup method like a LUT.  Full Adders are very simple components that are also built  
using a combination of transistors as shown in Figure 7. 

![Alt text](images/FPGA/Figure7.png)  
Figure 7. Conventional CMOS full adder [7]  

The full adders allow us to implement addition in an extremely fast manner within our FPGA. Even though a single full adder in one of our logic blocks  
can only add one bit values, if we combine multiple logic blocks together and chain their adders we can add numbers of any bit size as long as we have  
enough logic blocks. 

### D Flip-Flop

![Alt text](images/FPGA/Figure8.png)  
Figure 8. D Flip-Flop Truth Table [8]  

Now it is time to discuss the final component in our logic blocks, the D-Flip-Flop. A D Flip-Flop is a simple component with a Truth Table as shown   
in Figure 8 (above!). Simply, when the clk pin is high the Q pin is set to whatever D value is present. When the clk pin is low Q holds its state.  
D-Flip-Flops act as a storage component, as shown in the first two elements in the truth table maintaining whatever value Q was previously set to   
(effectively storing the previous D value). What are the D Flip flops purpose in the logic blocks in our FPGA? As you may be able to guess they act   
as a mini memory storage block that allow us to store state and data information between clock cycles. By combining multiple D flip-flops  with logic  
gates from our LUTs you can create more intricate sequential circuits like counters shift registers, and state machines within your FPGA. 


### Interconnection Resources

We started this post by looking at the diagram in Figure 1 and getting a fundamental understanding of the Logic Blocks that are present throughout  
our FPGA and how they are programmed by the user. But we still have the other crucial part of our basic FPGA, the interconnection resources. These   
interconnects in our FPGAs are essentially built using many different wires that are running between all of the logic blocks in our FPGA. When we   
want to connect any two specific logic blocks, for example to implement higher bit adders, we tell the switch block to close the connection between   
those two LBs that we want to chain together. 

![Alt text](images/FPGA/Figure9.png)    
Figure 9. FPGA programmable interconnect diagram [1]  

In the figure (9) above we can see how the switch matrix is at the center of many different wires that are running throughout our FPGA and connecting   
the different CLB/LBs together. The switch box/matrix is realized by using small transistors controlled by configuration data stored in the FPGA's  
internal memory (SRAM), enabling flexible routing between different logic elements depending on the specific circuit you want to implement. You can  
think of the switch matrix as being very similar to a LUT, but instead of the SRAM data going to the inputs of the Mux’s, we instead hook up the   
SRAM bits to the Set pins on our Mux’s and hook up the input and output pins of our Mux’s to different CLB within our FPGA.

### Conclusion

I hope I have been able to help you better answer the questions presented at the beginning of this article and maybe even help you answer them   
yourself. Just in case you are still confused I will now try to provide answers to them and solidify your understanding even more. 

#### How do these different FPGA building blocks (LUTs, D-Flip-Flops, Block memory) come together to execute an instruction specified by our VHDL  

LUTs allow us to create anytype of combinational logic that we want, think AND, OR, NOR, etc… These different types of combinational logic allow   
us to perform different operations like add, multiply, subtract, etc… The flip-flops allow us to store critical information about the data in our  
FPGA between clock cycles and let us do sequential operations like store state or implement counters. Block Memory is usually the SRAM that we   
discussed allows us to configure our LUTs and interconnection resources in the Field.

#### How exactly does a FPGA execute your textual code/instructions in parallel? 

It doesn't exactly EXECUTE your code, like how a microprocessor would. Instead, your code describes the types of logic you want to implement and   
the synthesizer in your software environment creates the circuits that describe your logic. Those circuits are then created on the FPGA by writing  
to the SRAM blocks that determine your LUT and interconnect configurations.  

#### Where does your compiled bitstream go once you generate it and how does it create the connections inside the FPGA? 

It gets written to the SRAM using the Column Address and Row Address lines as shown in figure 5. It creates the connections by setting the desired  
pins we want to set to the outputs of our multiplexers.

#### How are the LUTs and Flip Flops actually made, with Transistors? 

Yup! View Figures 7 and 5 to see how some of the LUT and Flip-Flop components might be made. For more on Flip-Flop architecture check out source [9]. 

#### Sources:

1. https://www.eetimes.com/all-about-fpgas/    
2. https://en.wikipedia.org/wiki/Field-programmable_gate_array    
3. https://hardwarebee.com/the-ultimate-guide-to-fpga-architecture/    
4. https://barth-dev.de/knowledge-corner/digital-design/memory-array-architectures/    
5. https://www.geeksforgeeks.org/multiplexers-in-digital-logic/    
6. https://www.geeksforgeeks.org/full-adder-in-digital-logic/    
7. https://www.researchgate.net/figure/Conventional-CMOS-full-adder_fig1_249567605    
8. https://electromagneticworld.blogspot.com/2017/05/flip-flop-circuits.html    
9. https://en.wikipedia.org/wiki/Flip-flop_(electronics)    
