# VSD_OpenLane_Sky130_Workshop

## Introduction

This repository documents the backend flow of VLSI design, assuming that we already have a fully verified RTL design that has been synthesized, verified, and for which a netlist has been generated and validated.

In this repo, we will demonstrate how to transform the synthesized netlist into a complete physical design. The stages we will cover include:

- Floorplanning  
- Placement and Routing (PnR)  
- Clock Tree Synthesis (CTS)  
- Static Timing Analysis (STA)  
- Design Rule Check (DRC)  

We will use the **PicoRV32** — a small and stable RISC-V core — as our RTL design for this backend flow.

Additionally, we will create a **standard cell library** that will be used to generate the netlist from the RTL code. We will analyze critical aspects of the design, such as **hold time**, **setup time**, and more.

For the backend flow, we will utilize **OpenLane** and the **SkyWater 130nm PDK**.
## Introduction to QFN-48 Package, chip, pads, core, die and Ips
![image](https://github.com/user-attachments/assets/bdc4f0c6-fd47-4a44-81a6-af4c5e79d527)
## RISC-V Instruction Set Architecture (ISA)
To communicate with the computer and make the it work in the way we want to work we write the instruction through programming languages such as C which is converted and compiled in assembly language program using RISC-V isa which is then converted into machine language and then into binary language which is understandable by the CPU. And additionally in order for the cpu to implement the Risc-V instruction it needs hardware which is modelled and created by the HDLs 

## Software to hardware
![image](https://github.com/user-attachments/assets/ed400b9e-72d4-4f74-9408-bd078deecaa3)
The above picture describes how the application are run on a CPU. The application software or APPS or written in programming languages which are run on OS. The OS is a software layer which take the full control of the hardware and allocate various hardware resource like clock time, storage etc.. to the various apps running on the system. All the codes are first compiled into instruction according to format of the ISA used and outputs an .exe file .The assembler takes the exe file and converts it into binary language which is understood and executable by the compiler. 

## Introduction to all components of open-source digital asic design
There are few things which are essential pillars for build an ASIC they are, 
![image](https://github.com/user-attachments/assets/b9aaf5a8-9159-43c6-82ff-7f4215c5952d)

## RTL IPs and EDA Tools

**RTL IPs** (Register-Transfer Level Intellectual Properties) are design files written in HDLs (like Verilog or VHDL) that describe how digital hardware should be structured and behave. They form the functional blueprint of digital circuits to be implemented in ASICs.

**EDA tools** (Electronic Design Automation tools) are specialized software suites used to create, simulate, analyze, and verify the entire design flow — from RTL to physical layout. These tools simplify and automate the chip design process and are crucial in performing tasks such as:

- RTL simulation and verification  
- Synthesis (RTL to gate-level netlist)  
- Placement and routing (PnR)  
- Timing analysis (STA)  
- Physical verification (DRC, LVS)  

By automating these steps, EDA tools have revolutionized modern IC design workflows.

## What is a PDK?

A **PDK** (Process Design Kit) is a collection of files and data that act as the interface between semiconductor fabrication facilities (fabs) and IC designers. It encapsulates all the technology-specific information needed by EDA tools to accurately design and verify a chip for a specific manufacturing process.

## Historical Context

In the early days of IC design ("the age of gods"), the design and manufacturing processes were tightly coupled and controlled by a few large companies. Those who controlled the fabrication physics also dictated the creative direction of chip design.

This changed when **Lynn Conway** and **Carver Mead** introduced the concept of *structured design*, promoting the separation of design and technology. This led to the rise of:

- Pure-play foundries (e.g., TSMC, GlobalFoundries)  
- Fabless companies (e.g., Qualcomm, AMD)  

This separation created the need for a standard communication interface between the fab and the designer — hence the **PDK**.

## What a PDK Includes

- **Process Design Rules** (for DRC - Design Rule Check, and PEX - Parasitic Extraction)  
- **Device Models** (SPICE models for simulation)  
- **Digital Standard Cell Libraries** (logic gates, flip-flops, etc.)  
- **I/O Libraries** (pads and buffers for interfacing the chip externally)  

## Open Source ASIC

**Open source ASIC** has been a dream for many engineers. To achieve an open-source ASIC ecosystem, we need open-source solutions across all three pillars of ASIC design:

1. RTL Design  
2. EDA Tools  
3. PDK  

While open-source RTL and EDA tools have been developed and well-established for a long time, **open-source PDKs** were missing until recently. A notable advancement is the collaboration between **Google** and **SkyWater** to release the open-source **Sky130nm PDK**, which represents a significant step toward fully open-source ASIC development.

![image](https://github.com/user-attachments/assets/6bfa45ee-e521-40c9-a10f-dcdb404950eb)

## The RTL to GDSII flow
Now though we have open source tools there is no standard flow or procedure. A methodology/ flow is need from RTL to GDSII.

![image](https://github.com/user-attachments/assets/eb5427fb-f3fc-424b-b5a0-b80708fca70c)

The first step is to develop a RTL design of the logic and then converting the RTL design in to netlist using components from the standard cell library [SCL]. It is described using HDL languages. The standard cells have regular layouts and typically enclosed fixed height rectangles. Each cells have different  views like the liberty view which includes the electrical view and spice view. We have layout view which shows the physical structure of the cell and a lot more.

The floor and power planning is process of allocating the location and amount are for each logic block it also includes Macro Floor-Planning: Dimensions, pin locations, rows definition.
The power planning includes the construction of power distribution networks, power pads, power straps, and power rings.
The placement is usually done in to steps, first global placements which includes the optimal placement of logic blocks which are not necessarily legal it can even overlap which will followed by Detailled placement where the cell level placement is done guided by the design rules and must this placement must compulsorily legal and pass the DRC check.
The CTS – clock tree synthesis involves planning and construction of clock delivering line to all the sequential elements with minimum skew. This usually takes the shape of a tree or the letter I or letter H or letter X.
The next stage is routing which involves laying down the wires connecting different logic block using the available  metal layers and interconnects.
The final stage is sign off which involves design rule checking, layout vs schematic (LVS) and timing verfications.

## Introduction to open lane
Started as an open source flow for a true opensource tapeout experiment. striVe is a family of open everything SoCs open PDk open EDA, open RTL. The following table details about the striVe family,
![image](https://github.com/user-attachments/assets/16fec5e3-bc53-4b8a-bdbc-878cd947c2df)
The main goal of openlane is to produce a clean GDSII with no human intervention. It is tuned for Skywater 130nm open PDK. Can be used to harden macros and chips. There are two modes of operation,
- Autonomous
- Interactive

It has inbuild design space exploration feature through it explore and finds the best one out. Additionaly it also comes with large number of design examples.

## openlane ASIC flow
![image](https://github.com/user-attachments/assets/9c510bf6-bef2-4809-b770-b7c7f68e9a44)
The RTL synthesis uses YOSYS plus ABC for creating the netlist, followed by STA — static timing analysis using OpenSTA. In order to explore the design space, we have the Synth Exploration tool. For testing, we have Fault to perform:

- Scan Insertion  
- Automatic Test Pattern Generation (ATPG)  
- Test Pattern Compaction  
- Fault Coverage  
- Fault Simulation  

Then we have the physical implementation, which is done through the OpenROAD app to perform:

- Also called automated PnR (Place and Route)  
- Floor/Power Planning  
- End Decoupling Capacitors and Tap Cell Insertion  
- Placement: Global and Detailed  
- Post Placement Optimization  
- Clock Tree Synthesis (CTS)  
- Routing: Global and Detailed  

Next, LEC — Logical Equivalence Check — is performed to verify the newly synthesized and optimized physical design is logically the same as that of the netlist.

### Antenna Rules
When a metal wire segment is fabricated, it can act as an antenna.  
- Reactive ion etching causes charge to accumulate on the wire.  
- Transistor gates can be damaged during fabrication.  

Usually, it is the job of the routing tool to verify these rules. There are two solutions:  
- Bridging attaches a higher layer intermediary  
  - Requires router awareness (not there yet!)  
- Add antenna diode cell to leak away charges  
  - Antenna diodes are provided by the SCL  

In OpenLane, a preventive approach is followed:  
- Add a Fake Antenna Diode next to every cell input after placement  
- Run the Antenna Checker (Magic) on the routed layout  
- If the checker reports a violation on the cell input pin, replace the Fake Diode cell with a real one  

### Utilization Factor and Aspect Ratio
1. The first step is to define the height and width of the core and die.  
We first convert our logic gates into standard shapes like rectangles and get their shape and area occupied by them.
![image](https://github.com/user-attachments/assets/92b01e72-dc9c-4b50-808a-c1b98c3dc034)
![image](https://github.com/user-attachments/assets/4ab0aae0-a734-40ea-8e89-3773417adff8)
First inorder to initiate the panning we will ignore the wire and find the total are occupied by the logic.
![image](https://github.com/user-attachments/assets/254d07a3-965d-4b90-bed5-1b08de537e7b)
If the total die area is equal to the area occupied by the logic gate it is 100% utilization.
![image](https://github.com/user-attachments/assets/56a569c0-18f5-4640-8fa9-13271d14b64f)
 And the next factor is,
![image](https://github.com/user-attachments/assets/d474e4f2-f082-4fc2-ae5c-ae2ef4ae5f4a)
If aspect ratio is 1 then it is a square.

2. The second step is to define the location of preplaced cells.  
Sometimes, we need to split up single logic into two individual blocks by separating the logic and extending the I/O pins, and then black boxing the logic.  
These blocks are called IPs, which are available as black boxes. These are placed in the floor in specific user-defined locations and cannot be altered by the tool. These are called **preplaced cells**.  
All preplaced cells are implemented once and used multiple times.  
Always try to situate the blocks that talk with the I/O pins near the respective pins. Based on that, place the preplaced cells. Once the preplaced cell is placed, it can never be moved.

Any connecting line will have parasitic impedance (resistance and inductance), which can never be avoided. Hence, there might be a voltage drop as it is passed through the wires.  
Therefore, we add a **decoupling capacitor** across the power supply lines of IP blocks, which will get charged. Whenever the supply drops from the set level, the capacitor will discharge and provide the required power supply.  
If this is not done, then the supply voltage will reduce, hence the output voltage swing will reduce.

![image](https://github.com/user-attachments/assets/16e272df-52dc-4501-988d-89d4b6cbeb86)
If the drop is more than the tolerable level then it might cause unwanted errors.

![image](https://github.com/user-attachments/assets/d577fe51-f0d5-4af9-9dcd-801a5663e92d)
And this is how the preplaced cells are placed,
![image](https://github.com/user-attachments/assets/367722c0-1386-436a-b43b-27feeb2ef4f0)
As we have seen above we have taken care about the local communication. Lets black box the whole cell,
![image](https://github.com/user-attachments/assets/564de8ab-d115-484f-bc69-ab70c4a6d13c)













