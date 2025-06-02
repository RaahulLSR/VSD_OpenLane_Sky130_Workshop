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

Now we will see about the global placement. It is not feasible to add decoupling capacitance to all the components in the die. It is used only to few specific and sensitive components that needs to  be isolated from the main supply. But how to over come the same problem for other signal lines. 

![image](https://github.com/user-attachments/assets/ada0f197-77fd-461e-8808-7fbe82e297ea)
And also at some particular situation most of the bits might suddenly drop from logic 1 to logic 0 which will result in a spike of current along the ground lane and similarly at some point of time if a lot of signals swap from logic 0 to logic 1 then it will need huge supply from the power. If the power is taken from one signal place to all the aareas it might cause all these issue and a lot more so to mitigate all these we use power grids.
![image](https://github.com/user-attachments/assets/b78f05b1-344f-4ecd-93ee-aaa69dbe9f2d)
![image](https://github.com/user-attachments/assets/263d18b9-df1e-4d3c-b08b-4c9f337a1ea8)

## Circuit
![image](https://github.com/user-attachments/assets/cd9a09d2-12b0-4a73-b7b0-1dbadacfd242)
![image](https://github.com/user-attachments/assets/55c94849-bc73-4bb8-b89f-dfedc61dbf02)
From now on we will use above two circuits for explaining all the concepts below. Though they are two separate circuits they must be placed in a single die.


## Pin Placement
The next step is to place the I/O pads in the die.

![image](https://github.com/user-attachments/assets/01f00ead-2194-4416-b74b-f5cd68bd3e85)
You can note here that the clk pins are much wider than other pins — this is done to reduce the resistance for the clock signal.  
And once the pads are placed, none of the blocks can be placed in the area of the pads. This area is logically blocked for any tool, so as not to place any circuit in that area.

## Binding with the Netlist
The next step is to assign the logical gates — which were, until now, merely just mathematical and logical operations — a physical structure, a shape, and an area according to the standard cell.  
All the standard cells contain particular information for each cell, like:  
- Size  
- Shape  
- Timing delay  

Apart from this, there will be various versions of the same cell, which will provide:  
- Higher speed but larger area and capacitance  
- Or smaller area and capacitance, but slower speed  

## Placement
The next step is to place the standard cells of the logic blocks in the die.  
While doing this, we need to place them in an optimized way, such that no signal travels along long wires, and blocks that are associated with pins must be placed near to the respective pins.

![image](https://github.com/user-attachments/assets/d7dbb75f-e153-4a49-98f2-6abab4032ec1)
As can be seen:  
For the yellow circuit —  
The yellow circuit is alone placed close because it might be a high-frequency circuit.  
But FF1 is kept very far away from Din2, so in order to reduce the loss, we ought to keep **repeaters** between FF1 and Din2.

![image](https://github.com/user-attachments/assets/67bd5c32-d580-4cd9-a3ad-e7d3e3b5705f)

For the orange circuit:  
All the elements are equidistantly placed, and hence it is a perfect placement.

A similar kind of analysis can be done for the other circuits, and buffers are placed wherever required.  
The final circuit looks like this:

![image](https://github.com/user-attachments/assets/31ff2589-e3c1-4798-a274-c3bac5eb1f32)

Here is the final design,
![image](https://github.com/user-attachments/assets/707edeed-0e03-49e1-9c2a-e7a3f07ee0d4)

Library characterization
One common things that travel throughout all the stages of the backend flow is the library so we need to know all things about library

![image](https://github.com/user-attachments/assets/f66b8e25-a9cb-47f3-b994-7e5ade31ab86)
Library contain all the cells and all the aspects of individual cells detailly stuffed into it.
![image](https://github.com/user-attachments/assets/0956938b-6274-4d34-86fe-2566e9c58dfd)
It also have cells of same function but differ in threshold size capacitance and lot more variations
The following picture shows the cell design flow,

![image](https://github.com/user-attachments/assets/b64771ee-229b-483d-bbf2-bd2b9374d7be)
Let's start with an inverter, which is the basic CMOS cell.  
The inputs are:  
- Process Design Kits (PDKs): DRC & LVS rules, SPICE models, library, and user-defined specs.  

**DRC rules** are the rules that need to be followed while designing a cell. These are mostly lambda-based rules. There might be thousands of rules provided by the foundry for designing the cell.

The **SPICE model** includes all formulas, as the following picture aptly depicts:

![image](https://github.com/user-attachments/assets/09db08b0-f2b5-4e8d-8923-7208204f167f)
![image](https://github.com/user-attachments/assets/f6dd285f-7d16-4494-b8b3-872becc2eace)
The cell height is decided by the distance between the power and ground grid lines in the die.  
The cell width is determined by the drive strength of the cell — more width means higher drive strength. And the higher the drive strength, the more the number of output lines.

The next parameter is the supply voltage. It is decided by the system-level designer and must be taken as input by the library design engineer. While deciding this, we must also take care of the noise level in the chip.

The next parameter is about metal layers. There is a specification that certain libraries must be built on separate metal layers. This also must be taken care of.

The next parameter is pin location. There are a few specifications on the location of I/O pins, which must be followed by the designer.

With all the above inputs, we must come up with a cell design that adheres to all the specifications and rules.

---

### The next step is **Design**, which has 3 steps:
- Circuit design  
- Layout design  
- Characterization  

**Circuit design** involves two steps:  
1. To implement the logic function  
2. To model the PMOS and NMOS W/L ratio according to the needed specifications of drain current and \( V_m \)


![image](https://github.com/user-attachments/assets/059b42ec-85c1-4119-ab92-90d677ea0b01)
The output of circuit design is CDL – circuit design language.

The next is the layout design,
We implement the designed logic physically using MOSFETs.
This is done first by developing the network, then develop Euler’s path out of it.
Then develop stick diagram and then layout diagram out of it. The layout diagram is the diagram which says exactly how different regions and layers are created and aligned. The stick diagram is an abstract diagram and doesn’t have to follow any rules while the layout diagram must adhere to all the user specified rules and DRC rules.

![image](https://github.com/user-attachments/assets/5fe79cbf-8e1c-4fdc-ba36-6f7171b60e3f)

The output of this step is CDSII, LEF, and extracted netlist.  
These contain all the essential information.  
CDSII has the layout design.  
LEF has all the input and output ports.  
Spice netlist has all the parasitic resistance and capacitance.  

And a lot more information.  

The next step is characterization flow.  
From the input stage and design stage, we have the layout,

![image](https://github.com/user-attachments/assets/63260f74-181d-493f-97e2-76af82245fe1)
And the description of the entire buffer,
![image](https://github.com/user-attachments/assets/80e6c394-68cb-4839-a201-4b4805261d28)
And we have the spice extracted netlist,
![image](https://github.com/user-attachments/assets/b16cc718-c705-49a3-900e-9e79c439df2c)
The first step inn characterization is to read the model file
![image](https://github.com/user-attachments/assets/1d1c50a3-b698-4305-b959-bae55c4fed3f)
Next step is to read the netlist.  

The next step is to define and recognize the behaviour of the circuit.  
The next step is to read the subcircuit.  
And the next step is to attach the power supply.  
The next step is to apply and read the stimulus.  
The next step is to vary the load capacitance.  
Provide the necessary simulation command to spice.  
Next step is to feed all the inputs from the above step to characterization software, here we use GUNA, which will give us the following files:  
- Timing  
- Noise  
- Power  
- .libs  
- Function  

Timing threshold definition:
![image](https://github.com/user-attachments/assets/41fd85c4-5c86-4f74-91cf-5c26a7da6339)
1. Slew Low Rise Threshold (slew_low_rise_thr)  
Definition: The lower voltage level (typically a percentage of VDD) used as the starting point of the rising edge for measuring slew.  

Example: 20% of VDD  

Purpose: Marks the beginning of the rise transition.  

2. Slew High Rise Threshold (slew_high_rise_thr)  
Definition: The upper voltage level (typically a percentage of VDD) used as the ending point of the rising edge for measuring slew.  

Example: 80% of VDD  

Purpose: Marks the end of the rise transition.  

Slew (rise) = Time between slew_low_rise_thr and slew_high_rise_thr  

3. Slew Low Fall Threshold (slew_low_fall_thr)  
Definition: The upper voltage level used as the starting point of the falling edge for measuring slew.  

Example: 80% of VDD (since fall goes from high to low)  

Purpose: Start of the fall transition  

4. Slew High Fall Threshold (slew_high_fall_thr)  
Definition: The lower voltage level used as the end point of the falling edge for measuring slew.  

Example: 20% of VDD  

Purpose: End of the fall transition  

Slew (fall) = Time between slew_low_fall_thr and slew_high_fall_thr  

5. Input Rise Threshold (input_rise_thr) or in_rise_thr  
Definition: A specific voltage level used to measure input arrival time during a rising transition.  

Typical value: 50% of VDD  

Purpose: Defines the timing point for input rise delay.  

6. Input Fall Threshold (input_fall_thr) or in_fall_thr  
Definition: Voltage level used to measure input arrival time during a falling transition.  

Typical value: 50% of VDD  

Purpose: Defines the timing point for input fall delay.  

7. Output Rise Threshold (output_rise_thr) or out_rise_thr  
Definition: Voltage level used to measure output transition time or delay during a rising edge.  

Typical value: 50% of VDD  

Purpose: Marks the point at which the output is considered to have "risen".  

8. Output Fall Threshold (output_fall_thr) or out_fall_thr  
Definition: Voltage level used to measure output transition time or delay during a falling edge.  

Typical value: 50% of VDD  

Purpose: Marks the point at which the output is considered to have "fallen".  

Propagation delay  
Let's take the following condition  

![image](https://github.com/user-attachments/assets/02a79f05-59c1-4be1-b55a-0409836ffc09)
Here the delay is 23ps
But if the threshold is moved up for example see the below case,
![image](https://github.com/user-attachments/assets/57cdf3ab-2017-4bbb-88d9-b67a1a8f2082)
Here the output switches before the input which is not expected. This would lead to unexpected behaviour. This is caused by wrong choice of threshold.

And sometimes even after proper choice of threshold we might run into negative delay which are cause by high capacitance or long wires which might lead to waveforms like this,
![image](https://github.com/user-attachments/assets/2a78d802-8822-471c-89c6-cdd67e21bf8b)
![image](https://github.com/user-attachments/assets/8249ec78-3ec4-4a96-a39b-3e1111cfdb33)
For rise waveform the slew is given by  
time(slew_high_rise_thr) — time(slew_low_rise_thr)  

For fall waveform the slew is given by,  
time(slew_high_fall_thr) — time(slew_low_fall_thr)  

Spice deck  

Step-by-Step: SPICE Deck Creation for CMOS Inverter  
Step 1: Title Line  
Every SPICE deck starts with a title line. This line is ignored by the simulator for calculations but is useful for documentation.  
* CMOS Inverter Simulation  
________________________________________  
Step 2: Define Nodes and Transistors  
Write the netlist using MOSFET models (e.g., PMOS and NMOS). Each line describes one component.  
Assume the following connections:  
- PMOS: gate to in, drain to out, source to VDD  
- NMOS: gate to in, drain to out, source to GND  

M1 out in VDD VDD PMOS L=0.18u W=1u  
M2 out in 0   0   NMOS L=0.18u W=0.5u  
- M1: PMOS, connected between VDD and out  
- M2: NMOS, connected between out and GND (0)  
Syntax: M<name> <drain> <gate> <source> <bulk> <model> L=<length> W=<width>  
________________________________________  
Step 3: Add Voltage Sources  
Define power supply and input voltage sources.  
VDD VDD 0 1.8  
VIN in  0 PULSE(0 1.8 0 1n 1n 5n 10n)  
- VDD: DC supply of 1.8V  
- VIN: Pulse input that goes from 0 to 1.8V (like a square wave)  
PULSE(V1 V2 Tdelay Trise Tfall Ton Tperiod) defines a clock-like waveform.  
________________________________________  
Step 4: Load Capacitance (optional)  
Add a load capacitance to model output loading.  
Cload out 0 10f  
This simulates parasitic/load capacitance at the output.  
________________________________________  
Step 5: Include Model Files  
Specify the transistor models you are using (typically provided by the foundry).  
.include 'CMOS180nm.lib'  
Or if defining basic level models manually:  
.model NMOS NMOS LEVEL=1 VTO=0.7 KP=120u  
.model PMOS PMOS LEVEL=1 VTO=-0.7 KP=50u  
________________________________________  
Step 6: Simulation Command  
Set up the type of simulation. For example, transient analysis:  
.tran 0.1n 100n  
This runs a transient analysis from 0 to 100ns with 0.1ns time steps.  
________________________________________  
Step 7: Output/Plot Commands  
Specify what outputs to display or store.  
.control  
run  
plot v(in) v(out)  
.endc  
This is for NGSPICE or similar environments.  
________________________________________  
Step 8: End the Netlist  
End the deck with .end  
spice  
CopyEdit  
.end  
________________________________________  

Full Example: CMOS Inverter SPICE Deck  
* CMOS Inverter Simulation  

M1 out in VDD VDD PMOS L=0.18u W=1u  
M2 out in 0   0   NMOS L=0.18u W=0.5u  

VDD VDD 0 1.8  
VIN in  0 PULSE(0 1.8 0 1n 1n 5n 10n)  

Cload out 0 10f  

.model NMOS NMOS LEVEL=1 VTO=0.7 KP=120u  
.model PMOS PMOS LEVEL=1 VTO=-0.7 KP=50u  

.tran 0.1n 100n  

.control  
run  
plot v(in) v(out)  
.endc  

.end  

## The 16-Mask CMOS Process  
Introduction  
The 16-mask CMOS process is a standard planar CMOS fabrication technique used to manufacture integrated circuits that contain both NMOS and PMOS transistors. It involves a sequence of photolithographic steps, each defined by a mask, to selectively process regions of a silicon wafer. The 16 masks collectively create regions for wells, isolation, doping, gate formation, contacts, and interconnects.  

This process is called “16-mask” because it uses 16 separate photolithographic masks, though the exact number can vary slightly depending on the foundry or specific technology node.  

### Summary of the 16 Masks  
Here’s an overview of the typical 16 masks used in a CMOS process:

| #  | Mask Name                      | Purpose                                               |
|----|--------------------------------|-------------------------------------------------------|
| 1  | Active (Active Area or Diffusion) | Defines where diffusion (source/drain) can occur.      |
| 2  | N-Well                         | Defines n-well areas for PMOS transistors.             |
| 3  | P-Well                         | Defines p-well areas for NMOS transistors (in twin-tub process). |
| 4  | Field Oxide (LOCOS/Isolation)  | Isolation regions via field oxide or STI.               |
| 5  | Threshold Adjustment (VT)      | For selective threshold voltage implantations.          |
| 6  | Channel Stop                   | Prevents leakage by doping field regions.                |
| 7  | Poly (Gate)                   | Defines polysilicon gates of transistors.                |
| 8  | Lightly Doped Drain (LDD) N+  | Forms lightly doped n-type extension for NMOS.           |
| 9  | Lightly Doped Drain (LDD) P+  | Forms lightly doped p-type extension for PMOS.           |
| 10 | Spacer Etch (Sidewall Spacers) | Defines oxide spacers for self-alignment.                |
| 11 | Source/Drain N+                | Heavily doped n-type source/drain (NMOS).                |
| 12 | Source/Drain P+                | Heavily doped p-type source/drain (PMOS).                |
| 13 | Contact                       | Defines contact holes between metal and active regions.  |
| 14 | Metal1                       | First layer of metal interconnect.                        |
| 15 | Via1                         | Connects Metal1 to Metal2.                                |
| 16 | Metal2                       | Second layer of metal interconnect.                       |
  

1 selecting substrate  
It has the following characteristics,  
P-type, high resistivity (950 ohms), doping level (10^15 cm^-3), orientation (100). The doping must be less than well doping.

2. create active region  
Step 1: Start with a Bare Silicon Wafer  
• Typically a lightly doped p-type wafer is used.  
• This is the substrate on which all further processing takes place.  
________________________________________  
Step 2: Grow Pad Oxide (≈20 nm)  
• A thin oxide layer (~40 nm) is thermally grown over the entire wafer.  
• This protects the wafer surface during later processing (especially during field oxide growth).  
________________________________________  
Step 3: Deposit Silicon Nitride Layer (~100–150 nm)  
• A layer of silicon nitride (Si₃N₄) is deposited using LPCVD.  
• This acts as an oxidation barrier in regions where active devices will be built.  
• It prevents the growth of field oxide in active regions.  
________________________________________  
Step 4: Apply Photoresist and Expose Field Oxide Pattern (Field/Isolation Mask)  
• Coat the wafer with photoresist.  
• Use a photomask (called the field oxide mask) to expose only the field regions (non-active areas).  
• These are the areas outside the active regions, which will be isolated by oxide.  
![image](https://github.com/user-attachments/assets/920cc494-c24c-4a5c-be0c-0df9bcb23599)

________________________________________  
Step 5: Develop and Etch Silicon Nitride + Pad Oxide  
• Develop the photoresist to remove the exposed parts.  
• Use dry or wet etching to remove the nitride + pad oxide in field regions.  
• Result: Only areas where active devices will be built still have the nitride and pad oxide; other areas are bare silicon.  
![image](https://github.com/user-attachments/assets/e1c2ad26-ff3a-40aa-8bbc-d9a0eb21bbba)

________________________________________  
Step 6: Grow Thick Field Oxide in Field Regions  
• Use LOCOS (Local Oxidation of Silicon): Expose the wafer to high temperature (~1000°C) in an oxygen-rich environment.  
• In exposed silicon (field regions), thick field oxide (≈400–500 nm) grows.  
• In active areas (protected by nitride), oxidation is blocked.  
These bulges are called isolation and any area between two bulges are isolated hence one transistor is prevented from communicating with the nearby one.  
![image](https://github.com/user-attachments/assets/61fb558a-42ac-4403-a732-b7e2d42171c5)

________________________________________  
Step 7: Strip Silicon Nitride and Pad Oxide  
• After oxide growth, remove the remaining Si₃N₄ and pad oxide using:  
o Hot phosphoric acid (for nitride)  
o Buffered HF (for pad oxide)  
• Now, the wafer has:  
o Field regions filled with thick oxide  
o Active regions as exposed clean silicon  
![image](https://github.com/user-attachments/assets/6f045073-c971-4f27-a4c0-0a27e652603c)

________________________________________  
Step 8: Active Region Finalized  
• At this point, the active areas (where transistors will be built) are clearly defined and surrounded by field oxide isolation.  
• These regions will later be:  
o Doped (source/drain)  
o Gated (gate oxide + polysilicon)  
o Contacted (metal via contact holes)

3. nwell and pwell creation  
1. Starting Material  
• Usually, the wafer is p-type silicon (lightly doped with boron).  
• Sometimes, an n-type substrate is used for twin-well processes.  
________________________________________  
2. Photoresist Application and Masking  
• Photoresist coating: A layer of photoresist is spun onto the wafer.  
• Photolithography: Using a well mask, the photoresist is exposed to UV light, defining regions where the wells are to be formed.  
• For n-well formation:  
o The mask opens areas where n-well doping will be implanted.  
o The rest is covered by photoresist to block implantation.  
• Similarly, for p-well formation, another mask defines p-well regions.
![image](https://github.com/user-attachments/assets/7c8bb587-047d-46aa-9d1e-dd8eb9c89d1e)
![image](https://github.com/user-attachments/assets/7bafa97b-96a2-4f7f-84d7-e4338854c41a)

________________________________________  
3. Ion Implantation of Dopants  
• Ion Implantation: A high-energy beam of ions is accelerated and shot into the silicon through the photoresist openings.  
• N-Well Implantation:  
o Dopant: Phosphorus (P) or Arsenic (As) ions.  
o Purpose: Convert exposed p-type substrate into n-type well.  
o Implant energy: Typically in the range of 50–200 keV (kiloelectron volts).  
o Dose: 10^12 to 10^14 ions/cm² depending on desired doping level.  
• P-Well Implantation:  
o Dopant: Boron (B) ions.  
o Purpose: Convert exposed n-type substrate into p-type well.  
o Similar implant energies and doses but tailored for boron.
![image](https://github.com/user-attachments/assets/cc9a831b-83e8-414d-96d7-8fa3c5fdf990)
![image](https://github.com/user-attachments/assets/3bfd0cb7-7f77-41c7-91d7-0e3587da3a0b)

________________________________________  

4. Drive-in Diffusion (Annealing)  
• After implantation, the dopants reside near the wafer surface and create lattice damage.  
• A high-temperature anneal (usually 1000–1100 °C for tens of minutes to hours) is performed.  
• Annealing serves two purposes:  
o Activate dopants: Dopants substitute into silicon lattice sites electrically activating them.  
o Drive-in diffusion: Dopants diffuse deeper into the silicon to form a well of the desired depth (typically 1–3 microns).  
• Annealing also repairs implantation damage and reduces defects.  
• The depth and doping concentration of the well depend on:  
o Implant energy and dose.  
o Annealing temperature and time.
![image](https://github.com/user-attachments/assets/32e626db-6d27-4492-80a6-8470da84ebaa)
---

The gate formation  
Next we cover the N-well region and perform ion implantation to implant Boron to create P layer on the P-well; similarly, on the other side with Arsenic. Remember the energy is kept as low as possible to just maintain the depth required. And as the oxide layer is affected by repeated implantation it will be damaged and hence needs to be recovered, which will be done.  
![image](https://github.com/user-attachments/assets/1867f8c0-54bc-4439-8d04-d8ed46d3616f)

Finally, we arrive at this step.  
![image](https://github.com/user-attachments/assets/f82e41d1-c942-49d5-a19b-30e554184c73)

Next, we remove the oxide layer to recover from the damage. This is done with HCL etching.  
![image](https://github.com/user-attachments/assets/ee6832ba-8c9c-4a43-812e-6b275ab62b5d)

And the oxide layer is regrown to exactly the same thickness.  
![image](https://github.com/user-attachments/assets/d2feaed7-ac0f-4567-afae-3af665162769)

Next, the gate formation is done by first creating a thick polysilicon layer.  
![image](https://github.com/user-attachments/assets/b2797589-f29d-432c-a043-03e98b7dfd3c)

Next, we develop the mask like this.  
![image](https://github.com/user-attachments/assets/2507b563-972e-4ea4-ba2d-266d8b0a95ff)

From the top, the mask 6 looks like this.  
![image](https://github.com/user-attachments/assets/3c30e5ff-2b24-4786-b7ab-7b0bbff6f807)

And next, we etch away the other poly layer and the mask to result in the following structure.  
![image](https://github.com/user-attachments/assets/2aeccd71-04b7-4bfe-a9a0-ea70ba65c58e)

LDD formation  

What is LDD?  
• LDD is a lightly doped region between the channel and heavily doped source/drain.  
• It reduces the electric field near the drain, minimizing hot carrier injection and device degradation.  
• Typically done with a light implant of the same type as the source/drain but at lower concentration.  
The Need for LDD:  
The LDD structure was introduced primarily to combat hot electron effects and short channel effects (SCEs) in scaled-down MOSFETs.  
________________________________________  
1. Hot Electron Effect (HEE)  
What is the Hot Electron Effect?  
• In short-channel MOSFETs, when a high drain voltage (V<sub>D</sub>) is applied, a strong electric field develops near the drain end of the channel.  
• Electrons (in NMOS) or holes (in PMOS) are accelerated by this high electric field and gain significant kinetic energy.  
• These “hot carriers” may:  
o Inject into the gate oxide, causing oxide damage.  
o Get trapped in the oxide, shifting threshold voltage (V<sub>th</sub>).  
o Degrade the reliability and lifetime of the device.  
How LDD Helps:  
• LDD introduces a lightly doped region between the channel and the heavily doped drain.  
• This spreads out the electric field, reducing the peak field intensity at the drain end.  
• With a lower field, electrons gain less energy, minimizing:  
o Hot carrier injection  
o Gate oxide damage  
o Device degradation  
________________________________________  
2. Short Channel Effects (SCEs)  
What are Short Channel Effects?  
As the channel length (distance between source and drain) decreases:  
• Drain potential starts influencing the channel potential, leading to:  
o Drain-Induced Barrier Lowering (DIBL): Reduces the source-channel barrier, increasing leakage current.  
o V<sub>th</sub> roll-off: Threshold voltage reduces with shorter channel.  
o Increased off-state current (I<sub>off</sub>) and power dissipation.  
How LDD Helps:  
• LDD extends the effective channel length slightly via lightly doped regions.  
• This moderates the electric field near the drain and helps retain better control by the gate.  
• It mitigates DIBL and suppresses leakage by:  
o Improving the potential profile near source/drain ends.  
o Reducing parasitic short-channel leakage paths.  

Next, as usual, we cover the n-well and expose the p-well to ion implant.  
![image](https://github.com/user-attachments/assets/06e48d79-9773-4d82-880c-038c59d3f755)

And the gate protects the area below it from ion implantation while the exposed area is implanted with phosphorus. The doping concentration and energy is chosen such that it doesn’t penetrate so deep and just stays there and the doping is kept low and hence called LDD. A similar work is done on the n-side as well.  
![image](https://github.com/user-attachments/assets/bc1eafb5-684c-4936-8bc0-7f85a8ac96d6)
Next we create side wall spacers,
We first deposit Si3N4 on the trnaisitor,
![image](https://github.com/user-attachments/assets/ae4d0ccc-c3fe-4f05-b55c-90b5296e4347)
And then perform anistropic etching which removes Si3N4 from other regions but prevents the removal of near the sidewall,
![image](https://github.com/user-attachments/assets/c7549b54-d7d2-4120-b21e-1ee8baa1d069)
This protects the Lightly doped region below it.

The next step is form a thin layer of oxide to prevent the effect of channelling. It is the effect that happens when the vector velocity of ions of match with crystalline structure of p type substrate if that happens then the ions will move deep into the p substrate without any reaction at the surface. The oxide layer kind off disperses the ions as it enters the layer.
The next we cover the nwell and expose the pwell to arsenic ion implantation,

![image](https://github.com/user-attachments/assets/50a12b44-cb5a-4b89-9a0c-496c5c01a797)

This creates a N+ implant inside the n implant and here is where the sidewall spacer play crucial role by protecting the N layer below it,
![image](https://github.com/user-attachments/assets/99f379bb-026c-4ef1-a7ec-5aa617e74207)
A similar action is done on nwell region to with Boron

![image](https://github.com/user-attachments/assets/038aaa85-c469-4f5f-ac13-8b8bb65fe6d0)
After which the whole setup is put inside a hot furnace and expose it to high temperature, this step will make the implantation penetrate more into the substrate. 

![image](https://github.com/user-attachments/assets/a728d2f6-fb48-45fb-8e1f-7dc5ec3faec0)
The next step is to build the contacts,
Firstly before that we need to remove the thin oxide which we added to expose our drain and source region for contact,

![image](https://github.com/user-attachments/assets/afefbfaf-1c66-4df9-8190-064de7f56536)
After the process we will result in this,

![image](https://github.com/user-attachments/assets/75751120-38d2-4d85-b6e2-a74d5c383c10)
Next we will start building the local interconnect for which we will use titanium because of its low resitivity. We will deposit titanium using sputtering, the process by which the titanium is hit by argon gas which makes the titanium atoms sputter and deposit on to the substrate,


![image](https://github.com/user-attachments/assets/ce567b0c-3331-43f8-b0b4-10af906860c6)

![image](https://github.com/user-attachments/assets/f4d1936b-d257-49a6-96a9-0fc8560676c1)
These are heated and with N2 ambient which result in TiSi2
This is used for local interconnect. Another substance is also created TiN which is only used for local interconnect because of the kind of resistance it offers.

And most of the time we need only few connect to raise up and for those alone can build the following structure

![image](https://github.com/user-attachments/assets/7c32ddfc-6212-4110-9d61-274a5cd7a2f2)
The TiN is etched and using RCA cleaning
Which result in

![image](https://github.com/user-attachments/assets/c43e5588-4915-44c8-9509-7657609af68c)
Next we lay down si02 doped with phosphorous or Boron,


![image](https://github.com/user-attachments/assets/27410408-44dd-4951-bdc2-5c9b135681f7)
Next we 	go under CMP chemical mechanical polishing too create a ultra flat smooth finishing,

![image](https://github.com/user-attachments/assets/b8445f17-0acb-462a-9304-66eb2b28b41c)
Next we drill hole to access the connect of titanium

![image](https://github.com/user-attachments/assets/b781df09-13ac-4185-88c8-2d53303d0f5d)
And next we lay down a thin layer of TiN, it acts as a good barrier layer between multiple layers,

![image](https://github.com/user-attachments/assets/d2fda670-e41b-4846-b046-aeeaff6fa764)

Next step is to deposit a blanket tungsten layer for good connection between layers,

![image](https://github.com/user-attachments/assets/28cb156f-1158-42eb-bcd9-7da655f6aefe)
Then again we go under CMP to planarize the layer,


![image](https://github.com/user-attachments/assets/2f3b3178-9cdd-4438-8d5a-4dcbf3b6aedf)
Next step is to connect these tungsten to connect it to the higher metal layers,
For which we lay down aluminium and use mask 13 to create the connecting wires,

![image](https://github.com/user-attachments/assets/761ccf52-e630-459e-b810-7ee65d509dce)

![image](https://github.com/user-attachments/assets/78af8dca-9bb4-452e-a5ac-3987b486f595)
The next step is to take the metals to further higher metal layers, for which we deposit SiO2 and then drill holes using photolithography and lay down TiN 

![image](https://github.com/user-attachments/assets/b5618916-ed53-45f1-b238-a6679fe3a8a9)

![image](https://github.com/user-attachments/assets/6442af36-afd7-4716-b534-270f7ceb7016)
Then deposit tungsten

![image](https://github.com/user-attachments/assets/0614e3ac-cd26-43b9-a596-d88577b03a39)
Then again create aluminium connects again,

![image](https://github.com/user-attachments/assets/aac00557-dca0-400f-97d5-f3d2ffc10900)
Then we deposit Si3N4 to protect chip from the outside world,

![image](https://github.com/user-attachments/assets/3e742232-3f43-4f45-96ed-2043864b5808)
And finally use mask 16 to drill hole to bring the interconnects outside

![image](https://github.com/user-attachments/assets/1a40a1df-b509-48fd-beaf-1c90b9777d82)

This covers the 16 masks for a CMOS process.

## Delay table
A delay table characterizes how long a gate (e.g., inverter, NAND) takes to switch its output from one logic state to another based on:
- Input transition time (slew)
- Output load (capacitance)

It helps in modeling gate behavior under different operating conditions.
![image](https://github.com/user-attachments/assets/e011629c-e7c0-4d38-bd7a-6b3019e2823b)
For example, suppose we want to find the delay for a circuit with input skew of 40 ps and output load of 50 ps. Then, x9 will be the delay. Suppose we want to find the delay for a value not available in the table, then we create an equation based on the known set of data, from which an equation will be deduced and the value will be extrapolated.

There are a few observations:
If the load felt by buffers at every load is the same, then the delay is the same.
Similarly, identical buffers must be at the same level to make the clock delay the same or to keep the slack minimal.

Setup timing analysis:
We use an ideal clock first and then use a real clock for analysis.
For now, let’s assume the clock frequency of F = 1 GHz and clock period T = 1/F = 1 ns.

![image](https://github.com/user-attachments/assets/1556dbe5-a6ec-4c6e-ae48-4c770ad79ebe)

Lets assume the above circuit.
And the signal change by the first flop must be captured by the second flop in next subsequent clock.
For this to happen the combination delay of the circuit between the flop must be less than the time period.
![image](https://github.com/user-attachments/assets/f233a5f2-4c80-4ee5-b41d-f7488624d0a1)
But in sequential circuits there is a concept of setup time which says for a flop to capture the input the input signal must be stable atleast by the setup time unit. And hence reduce the available time.
![image](https://github.com/user-attachments/assets/1ec7aa9e-b5c1-4290-84f8-c2d3d7e98bd8)
Similarly there is an uncertainty in the clock source and it is not highly accurate. And that clock cannot generate clock at exactly zero so some variations is ought to occur so the time difference between two clock edges will not be exactly T. This is called clock jitter.
![image](https://github.com/user-attachments/assets/77f530e8-993a-44dd-8b6d-db0e9e58b84f)

So to model the jitter into our analysis we bring it  in,

![image](https://github.com/user-attachments/assets/7563dd7d-17b0-4312-9a21-205d416bb7f4)

So lets perform thee setup time analysis in our circuit,
But when it come to real circuit we also need to include the wire delay also with that,
![image](https://github.com/user-attachments/assets/09f8d021-ddb3-4aed-bfea-7fa18c91751f)
![image](https://github.com/user-attachments/assets/7c29d76d-590e-45e3-98f7-6be6af33f299)


## Clock tree synthesis CTS
Clock Tree Synthesis (CTS) is a critical step in digital ASIC physical design that focuses on distributing the clock signal uniformly across all sequential elements—typically flip-flops—in a chip. The primary goal of CTS is to minimize clock skew (the difference in arrival times of the clock signal at different elements) and clock latency (the delay from the clock source to its endpoints) to ensure reliable and synchronous operation of the circuit. During CTS, a clock tree is constructed using buffers, inverters, and sometimes clock gating cells, arranged in a hierarchical fashion from the root (clock source) to the leaves (flip-flops or registers). By balancing wire lengths and buffer stages, CTS ensures that the clock signal reaches all endpoints as simultaneously as possible, thus optimizing setup and hold time margins. It also plays a crucial role in power optimization and signal integrity by managing the clock network's capacitance and power consumption.


For example,
 ![image](https://github.com/user-attachments/assets/a8c3987a-62cc-458c-afa9-8ff561ca5d75)

Consider the above circuit the delay in wire for FF1 and FF2 is not same and hence will create a skew which is not wanted.
So we devop a tree such that the clock signal reaches all the flop at almost same delay this is done using midpoint strategy like this,
![image](https://github.com/user-attachments/assets/c910ee81-2415-48cf-8abb-8bd09c1b975e)
There are various trees like this and most commonly used are , ‘H’ ‘I’ ‘X’ etc..
This is the completed circuit,

![image](https://github.com/user-attachments/assets/cd612ac6-c969-46b5-a36c-36eea78a4860)
But wait, here is a catch: the clock lines are so long that the clock signal will be affected by their resistance and capacitance, which should not happen. The solution is to use buffers again, but these are not the same buffers which we used for logic. These buffers are carefully created so that the rise time and fall time remain the same for all.
![image](https://github.com/user-attachments/assets/35f8f38d-2ff1-43d9-b9a8-478501183f37)


Next since clock signal is the heartbeat of our circuit we cannot leave simply because it might get corrupted or disturbed by cross talk so to protect the clock nets by shielding using wires around it which are connected to ground or Vcc meaning they don’t change. Without shielding there is a high possibility of glitch. The following image describe the glitch,


![image](https://github.com/user-attachments/assets/97b288b1-5023-4d77-97e7-0bb660147240)
![image](https://github.com/user-attachments/assets/f4edacaf-1fff-452e-b649-471ccf0d1068)
And if this happens in any critical area it might cause the entire system to get corrupted.

![image](https://github.com/user-attachments/assets/206da2d9-a66b-4e1c-83a8-dd16e601de72)
Now since we added buffer we need to consider the delays through them,
So the analysis change like this,

![image](https://github.com/user-attachments/assets/f017b112-97db-4410-8140-10c4f9f810f3)

The above equation simplifies to 
 ![image](https://github.com/user-attachments/assets/9a4faf50-bca2-4cc0-9dd9-12e8fb951c57)

And the difference is called skew.
Including the setup time and jitter we get the complete equation,
 ![image](https://github.com/user-attachments/assets/52a50e87-37fa-4398-9729-b06e99151d1e)
And the below image explains about the slack, data required time and arrival time,

![image](https://github.com/user-attachments/assets/258691c2-11c0-44af-bd4c-a701cb645b81)

## Holdtime analysis
The hold time is the minimum time after the active edge of the clock for which the input must be stable. And the anlysis syas that the the combinational delay must be greater than the hold delay,

![image](https://github.com/user-attachments/assets/c772fc39-2607-4de6-9f78-8f6bc9e856b7)
Adding buffer delay the equation comes out like this,
![image](https://github.com/user-attachments/assets/f5ad1e16-94f6-44ef-9844-2d4fb42b06a6)

## Routing

Routing is a key stage in the physical design of integrated circuits (ICs) where electrical connections (wires) are established between the various components (like standard cells, macros, or I/O pins) laid out on the silicon.  
Routing is typically divided into two phases:

### 1. Global Routing
- Determines approximate paths for connections (called nets).
- Divides the chip into routing regions or grids.
- Assigns each net to a routing region, estimating congestion and guiding detailed routing.

### 2. Detailed Routing
- Performs actual wire placement layer by layer.
- Considers design rules (e.g., minimum spacing, via rules, metal layers).
- Ensures no shorts, opens, or DRC (Design Rule Check) violations.

### Lee’s Algorithm (Maze Routing)

Lee’s Algorithm, also called Maze Routing, is a classic algorithm used in the detailed routing stage to find the shortest path between two points on a grid, often used in grid-based routing.

#### How It Works

It treats the routing space as a 2D grid, and works in two phases:

#### 1. Wave Propagation (Expansion Phase)
- Starting from the source point, it propagates a wavefront outward by labeling neighboring grid points with increasing distance (cost).
- For each new point visited, its value is set to:  
  `value = current_value + 1`
- Obstacles (occupied cells, forbidden zones) are blocked.
- The wave continues until it reaches the target.

#### 2. Backtracing (Traceback Phase)
- Once the destination is reached, the algorithm traces back by always moving to the neighboring cell with a lower value, until the source is reached.
- This gives the shortest path in terms of grid steps.

This algorithm always uses the shortest path and the one with the least amount of bends in it. The routing is also controlled by the DRC rules.

### DRC Check

The few minimum rules that need to be followed by tools while designing, some of them are:

1. Minimum width
![image](https://github.com/user-attachments/assets/82884db3-8a76-4095-bf2f-05dacdcaf410)

The width cannot be below this one. This is the physical limitation by the manufacturing process associated with it.

2.	Wire pitch
![image](https://github.com/user-attachments/assets/741a2d24-ba65-4abf-b574-277052483c4e)
The center to centre distance cannot be less than =the wire pitch.

3.	Wire spacing 
The minimum space between two wire should not be less than wire spacing.

![image](https://github.com/user-attachments/assets/1b03f631-96f6-4543-bb88-7913deb54cc7)
DRC  also make sure that two wire of same layer do not cross each or become short unless it is done in the RTL design itself.

This is rectified by taking one wire to another layer, from this,

![image](https://github.com/user-attachments/assets/d88f8083-0eb1-4e2f-9a4e-37574dcb38ca)
To this,
![image](https://github.com/user-attachments/assets/8146b231-437e-4548-b93a-a93a28aff8f0)
Some more DRC rules regarding via are,
![image](https://github.com/user-attachments/assets/42635e87-8f0d-43f6-94f8-c10440ab6df0)
Minimum width of via as shown above.

![image](https://github.com/user-attachments/assets/6c5a7ee4-e684-47b4-99fb-1668e1b278a0)
minimum via spacing as shown above.

The step after DRC is parasitic parameter extraction which is done through spice softwares which will generate spef file.

## TRITONROUTE
Now we will look into Triton Route the opensource software used for routing.

It is done through two stages,
![image](https://github.com/user-attachments/assets/9d699507-5821-4389-b19a-c395682e3be4)
The detail route does the proper routing adhering to all the DRC rules and netlist.

The routing is done layer wise. And the routing of lower layer runs first and then the upper layer. All vias are created only during the process of upper layer routing only.

The output of fast route is the initial route guide,
 ![image](https://github.com/user-attachments/assets/f34eac2c-5653-4757-8869-64529c5d7f2e)

The above image tells the process of the initial route.

All layers have a rpeffered direction. If the layer is used in not preferred direction then it will be atke to the other layers wherever required.

The intial route guide ccan be read with the following ideas,

![image](https://github.com/user-attachments/assets/98e3c238-b7bc-41bc-a175-fbd2a1b30887)
There are three types of routing,
![image](https://github.com/user-attachments/assets/5219f76d-5804-48f1-a95b-68e61fbd08cc)

1. Intra-layer Routing
Definition:
Intra-layer routing refers to routing that occurs entirely within a single metal layer (e.g., all wires are drawn on Metal-1 or Metal-2).
Key Characteristics:
•	Simpler routing—no need for vias.
•	Used when short connections can be completed on the same layer.
•	Common in short nets or local interconnects.
Limitation:
•	Limited space → may cause congestion in dense layouts.
•	Cannot cross over obstacles or other wires unless multiple routing layers are used.
________________________________________
2. Inter-layer Routing
Definition:
Inter-layer routing involves connecting wires across different metal layers using vias.
Example:
A net may start on Metal-1, rise via a via1 to Metal-2, and continue routing there.
Key Characteristics:
•	Offers more routing resources, avoids congestion.
•	Allows routing over obstacles by changing layers.

Common Metal Layer Convention:
•	Metal-1: horizontal
•	Metal-2: vertical
•	Metal-3: horizontal


For the triton route detail route the input outputs are given below,


![image](https://github.com/user-attachments/assets/e917dbd0-ae21-431b-ae0a-faf9ef9c11da)

How does triton route handle connectivity 

It uses Access points.
![image](https://github.com/user-attachments/assets/89677515-b85c-4184-ae60-fb18941b9d0b)
![image](https://github.com/user-attachments/assets/4232995b-18ce-407b-882f-59b2007f032f)
•  Preprocessing:
•	During pin access analysis, TritonRoute identifies all legal access points on each pin (usually on M1 or M2 layers).
•	It considers metal enclosure rules, via constraints, and DRC-clean geometry.
•  Routing Phase:
•	When connecting nets, TritonRoute chooses the best access point for each pin based on:
o	Shortest path
o	Congestion
o	DRC clean routing
•	It builds a path from source AP → track → destination AP.

The following shows the routing algorithm,
![image](https://github.com/user-attachments/assets/f1429c4a-f44c-4671-9c5e-0423a82cd6ab)

# LAB
## Directory setup : 

![image](https://github.com/user-attachments/assets/f6adb59d-3746-4651-96ee-f526d4f89e39)

![image](https://github.com/user-attachments/assets/7d99814c-f65e-43d8-a6c2-972438cb96ed)
As it can be inferred from the above picture the sky130 has two folders namely,
- Libs.ref – which contain all the reference file
- Libs.tec – which contain all the technology related and tools related files


![image](https://github.com/user-attachments/assets/38c3c3d2-bbd3-4c25-82af-51b3de34ec47)

![image](https://github.com/user-attachments/assets/395f7ac9-d4d4-4a4c-be9e-2bade135260e)

To invoke the openlane use the following,

![image](https://github.com/user-attachments/assets/7ef155dc-d3a3-4420-86fd-a31baa3b043c)

The contents of config.tcl
 
![image](https://github.com/user-attachments/assets/e00c8db3-427f-475f-a05f-074a091560eb)

The config.tcl has the highest priority and whatever setting is setup in this file will overwrite any setting of the openlane.
![image](https://github.com/user-attachments/assets/444cc340-b4f0-4dde-94b9-6dcb998a4776)

This step merges all the .lef files found in the design directory.

## What is a .lef File?
LEF = Library Exchange Format
It provides a technology-agnostic description of cell layout geometry and routing resources. It's used primarily in floorplanning, placement, and routing stages of the physical design process.
1. Standard Cell Abstracts
•	Only boundary, pin location, and metal layer usage are included (not full transistor-level layouts).
•	These abstracts allow fast placement and routing without full layout detail.
2. Technology Information
•	Track grids
•	Via definitions
•	Metal layer widths, spacings, and direction (horizontal/vertical)
3. Macros (e.g., SRAM, PLL)
•	Physical dimensions and placement obstructions
•	Pin names, positions, and metal layer access
4. Routing Blockages
•	Helps routers avoid forbidden zones

![image](https://github.com/user-attachments/assets/2d02a5ea-1072-4a4d-b711-bef765d64f6b)
As you can see from the above figure after the execution of design prep command a new file named runs has been created. The contents of the runs folder are as follows,
 
![image](https://github.com/user-attachments/assets/431bdc60-03ea-405c-83b0-72c1848534b5)

And another important thing to note here is another config.tcl folder is present inside the newly created directory.

As you can see below the merged lef file is created in the temp directory,
 
![image](https://github.com/user-attachments/assets/65381cc1-a5e4-4291-a664-cf0b003a9d37)

One advantage in openlane is that all the parameters are updated on the fly that is as you change the setting and run the next command the new setting will be reflected in the design

The next command is,
run_synthesis
which will  invoke yosys and run abc command for us.
On completion it prints the usage statistics like this,
![image](https://github.com/user-attachments/assets/c7cba9e7-fe44-4c37-a9ab-223107dd0921)
You can get more information on,
https://github.com/The-OpenROAD-Project/OpenLane
https://www.youtube.com/watch?v=Vhyv0eq_mLU

steps to characterize the synthesis result
![image](https://github.com/user-attachments/assets/a5a1e111-b405-4ff5-9024-54f8b4d4b4cf)
Number of D flip flops used in the design here it is 1613.
And total number of cells is 14876 
And so % dflip flop is 1613/14876 x 100 = 10.84%

And the run also create the report in the reports directory as seen below,
![image](https://github.com/user-attachments/assets/1c32a22e-82cd-4990-a1ef-7998b7e3c80a)

The next stage is floor plan.
To run the automated flow we can use the command
run_floorplan 

The Readme.md file at the configuration directory consist of all the switches,variable and parameters that can be set in various stages of the design,
 ![image](https://github.com/user-attachments/assets/3f7b6657-7dbd-42ef-ac57-2f2702a8b2f0)

The configuration folder also contain 
 ![image](https://github.com/user-attachments/assets/28d58791-45a3-47eb-9582-8bbbbe2def87)

Various .tcl files which will contain the current and default values of the switches and  parameters





After running the floorplan, we will receive the confirmation as,
 
![image](https://github.com/user-attachments/assets/2cb84c30-f266-488f-863e-ecbcc29fbba0)

And on the execution they also produce the output at the runs directory of the design with the current date as the file name as you can see below,
 ![image](https://github.com/user-attachments/assets/4275b15e-2fbb-47ff-b7e3-32c69cf218bf)

The floor plan is stored in the results directory where we can find .def file which contains the information,
 ![image](https://github.com/user-attachments/assets/fa7d415a-bc09-46b9-afc6-98be15a0b032)

The first line gives the die area. The units are given in microns

To visualize the created floorplan  
![image](https://github.com/user-attachments/assets/52e0bddc-6a68-4b84-9483-411cb32be286)

It pops out the window,  
![image](https://github.com/user-attachments/assets/eb6b40c4-1554-4820-b56e-509f08e11a65)

Review of floor plan in magic
To zoom in press Z
To select a particular block move the cursor to the block and press S
To draw the box use left mouse click for bottom left corner and right click for top right corner
Apart from this we also a have another window which helps us to find more details about the currently highlighted component.
If we wanted to know about component select the component and enter what in the Command line window to know about the detailed information of the block.

The floor plan doesn’t place the standard cell but the standard cells are there placed at the lower left corner of the die as you can see below,
 
![image](https://github.com/user-attachments/assets/ab4662ea-abdc-41fd-ac86-c2bab883a5c5)

The next stage if placement
It occurs in two stages
•	Global 
•	Detailed
Global placement is coarse and not legalized while the detailed placement is legalized and timing analysed.

To run the placement  the command is 
run_placement

our objective is to converge the overflow and optimize the half parameter wire length.
![image](https://github.com/user-attachments/assets/00703e55-1153-4ff1-bffd-3985a9247b20)
To view the placement execute the following command,
 
![image](https://github.com/user-attachments/assets/203c5539-b456-4a44-9c5d-dc1a0e2f2ddc)
![image](https://github.com/user-attachments/assets/b425b18d-a0f1-404e-895b-1a8972c6cb75)
Openlane allows to change the parameters on the fly for example,
If we want to change the way the I/O are placed,or anyother changes, you can play around with the below values,

![image](https://github.com/user-attachments/assets/6c286e98-942a-48d8-ae58-9cb5eeed49aa)
Set and change to new values and run the floor_plan again to see the reflected changes.
Before proceeding further we will go indepth in creating a cell.


## NGspice

NGSpice is an open-source, powerful, and widely used circuit simulation tool based on the original SPICE (Simulation Program with Integrated Circuit Emphasis). It allows engineers, students, and researchers to simulate analog, digital, and mixed-signal electronic circuits. NGSpice supports a wide variety of analysis types including DC, AC, transient, noise, and parametric sweeps, making it ideal for verifying circuit behavior before physical implementation.

NGSpice reads netlists (circuit descriptions) written in SPICE syntax, processes the component connections and models (like MOSFETs, BJTs, resistors, capacitors, etc.), and simulates how the circuit responds to various inputs and conditions. It also integrates well with tools like KiCad, Xyce, and LTspice, and supports scripting through TCL or embedding in Python/Matlab workflows. Thanks to its open-source nature, NGSpice is especially popular in academia and VLSI prototyping, where accessibility and flexibility are key.

*** MODEL Descriptions ***
.include "tsmc_025um_model.mod"  
.LIB "tsmc_025um_model.mod" CMOS_MODELS

*** NETLIST Description ***
* CMOS Inverter  
M1 out in vdd vdd pmos W=0.375u L=0.25u  
M2 out in 0   0   nmos W=0.375u L=0.25u  

* Load Capacitance  
Cload out 0 10f  

* Power Supply  
Vdd vdd 0 2.5  
Vin in  0 0  

*** SIMULATION Commands ***
.op  
.dc Vin 0 2.5 0.05  
.end  

🧾 Netlist Explanation  
*** MODEL Descriptions ***  
.include "tsmc_025um_model.mod"  
.LIB "tsmc_025um_model.mod" CMOS_MODELS  
•	`.include` loads the SPICE model file (e.g., transistor characteristics like threshold voltage, mobility) from the specified file.  
•	`.LIB` selects a specific library section (here, CMOS_MODELS) from that model file. This activates the transistor models used in your circuit.  

________________________________________  
*** NETLIST Description ***  
* CMOS Inverter  
M1 out in vdd vdd pmos W=0.375u L=0.25u  
M2 out in 0   0   nmos W=0.375u L=0.25u  
•	These lines define two MOSFETs forming a CMOS inverter:  
o	M1: A PMOS transistor, with:  
	drain = out, gate = in, source = vdd, body = vdd.  
	Width W = 0.375µm, Length L = 0.25µm.  
o	M2: An NMOS transistor, with:  
	drain = out, gate = in, source = 0 (ground), body = 0.  
	Also with W = 0.375µm, L = 0.25µm.  
•	Together, M1 and M2 implement a standard CMOS inverter.  

________________________________________  
* Load Capacitance  
Cload out 0 10f  
•	Defines a load capacitor of 10 femtofarads between the output (out) and ground.  
•	This models the parasitic or connected load at the output.  

________________________________________  
* Power Supply  
Vdd vdd 0 2.5  
Vin in  0 0  
•	Vdd: A DC voltage source providing 2.5V to the vdd node (power supply).  
•	Vin: Input voltage source for the inverter. It's initialized at 0V but will be swept later.  

________________________________________  
*** SIMULATION Commands ***  
.op  
.dc Vin 0 2.5 0.05  
•	`.op`: Requests the operating point analysis, giving DC voltages/currents at each node before sweeping.  
•	`.dc`: Performs a DC sweep of Vin from 0V to 2.5V in 0.05V steps, allowing you to observe how the inverter output behaves as input changes.  

________________________________________  
.end  
•	Marks the end of the SPICE file.  

Procedure for working with NGSPICE,  
1. Source  
•  

2. To execute the command is  
run  

3. Set plot  
Which lists the available plots  

4. On entering the required plot  

5. Type in the nodes which need to be visualized.  

![image](https://github.com/user-attachments/assets/aed8b834-915d-4965-8046-adb7083f7296)

Creating cell.
Git clone from 
https://github.com/nickson-jose/vsdstdcelldesign.git

To develop a inverter cell from scratch please refer to the above git.
then copy the .tech file from the pdk/magic directory to the newly cloned directory and then visualize the inverter in magic.
![image](https://github.com/user-attachments/assets/5c9f1f40-ff8b-4b4e-819e-93b5c2202ca9)

![image](https://github.com/user-attachments/assets/5f334fbe-e7ac-4ba0-89e8-c7ec79437973)
The above image is colour palete which describes the various layers used in the design 

To verify that our design is correct we must extract the spice model from the physical design, which can be done in the following way,
![image](https://github.com/user-attachments/assets/9545e93a-83f8-499f-8b94-ad1aeccd6ee5)
To extract the spice model,
Execute
ext2spice cthresh 0 rthresh 0
ext2spice
which will create a .spice model as below

![image](https://github.com/user-attachments/assets/1ee2c58c-8631-443e-9e76-17dd3e24610c)
![image](https://github.com/user-attachments/assets/39f5b00c-cc1c-4ee3-bfcf-136239c967ef)

So now to check the spice make the following changes

![image](https://github.com/user-attachments/assets/686b1ebf-7cf4-4e59-8fdb-b55f2963ecbd)

By executing the spice file using ngspice and plotting the graphs we could see,

![image](https://github.com/user-attachments/assets/3abcf7dc-77dc-4ec0-a7a2-b8e6de8a8c9d)

We could infer the rise time delay and fall time delay by comparing the 50% values of both input and output.

Next is to explore the Magic tool.  
For all information about Magic, please refer to the below link:  
http://opencircuitdesign.com/magic/index.html

The technology file is one single file that tells all about the design to the tool. It tells a lot of things like:  
- All layers  
- Colours  
- Patterns  
- DRC rules  
- Device extraction rules  
- Rules to read and write DEF and LEF files  

**CIF – Caltech Intermediate Format**  
It is a human-readable ASCII format of the GDS format. It was once used earlier and is sometimes used instead of GDS in the Magic tool.

The basic DRC rules are called edge-based rules. They work by finding the edge between two layers and checking in front or behind if anything violates the rule.  
To know more about DRC rules, please refer to:  
http://opencircuitdesign.com/magic/index.html

For all information about SkyWater and the PDK, please refer to the links below:  
https://github.com/google/skywater-pdk-sky130-raw-data  
https://skywater-pdk.readthedocs.io/en/main/

Next for exercise download,

In order to know what rule is violated in design 
Select the are to be analyzed and command
drc why
to see what is the error like this,
![image](https://github.com/user-attachments/assets/2bb98653-d261-42cb-b9be-deb7f7cf685a)
Lab exercise 1 to fix poly.9
First measure the distance
![image](https://github.com/user-attachments/assets/d4507a5c-a9ad-4e24-9095-76af20361405)

The distance is less than the value but not been flagged so we edit the tech 
To include these in the DRC and then check back again after which we can see
![image](https://github.com/user-attachments/assets/11c348fd-4a49-41c9-befd-2c4ccd6903c6)
As you can note now the errors are shown.
Challenging DRC error
It is something that can be described any single rule check.
For this we need to use Boolean operator like or/and/not and filter out.
For example the nwell.6 rule state that every deep nwell must be surrounded by a normal nwell  layer by atleast the default unit.

![image](https://github.com/user-attachments/assets/8175be8b-ba52-473f-95a4-7eed764b816a)

See the drc is not flagged for which
And the Boolean operators are used like this,

![image](https://github.com/user-attachments/assets/0ae309fe-f0a8-4b16-8e4d-207a417a8f19)
Now let’s get back to the OpenLane flow and use the libraries we created.  
Firstly, for placement we do not need all the information about the standard cell, and thus we do not need the `.mag` file. This is where the `.lef` file comes in, which just has the I/O pins, area, power rails, and essential information.

Now let’s extract the `.lef` file from the `.mag` file.

These are a few guidelines for the standard cell:
• The I/O ports must lie on the intersection of vertical and horizontal tracks.  
• The width of the standard cell must be an odd multiple of track pitch.  
• The height must be an odd multiple of vertical pitch.  
We can get the tracks info at 
![image](https://github.com/user-attachments/assets/1715e173-b787-452a-abf6-09f1b97a8795)
![image](https://github.com/user-attachments/assets/5448b10e-d8fb-4319-a8ba-bfab1379bfc0)
First turn on the grid,
![image](https://github.com/user-attachments/assets/a497fedc-1e6c-42e2-be27-c16410d9a593)
To check is the i/O ports are at the tracks,
![image](https://github.com/user-attachments/assets/32ab1290-1e2a-4157-8acf-f3539fc46c6d)
On enter we could see that the grids have changed to,
![image](https://github.com/user-attachments/assets/64937429-d9c1-440a-adbe-0236f6142606)
Also from the same grids we could infer that if the cells width and length are in odd multiples by counting the grids.

The next step is to define ports in the design for which please refer to the following,
https://github.com/nickson-jose/vsdstdcelldesign?tab=readme-ov-file#create-port-definition

the next port is to define the purpose of each port,
![image](https://github.com/user-attachments/assets/b85b2fac-591c-4bd0-b973-edf88dd9b5aa)
We define in the same way for all,
![image](https://github.com/user-attachments/assets/f30a3f49-0da8-4348-aa48-21438d09ead7)
Lets save it using a custom name using the
Save 
Command
For ease of use I have used the name as AAA_LSR_inv.mag
The AAA is used because I wanted to list it at the top in stat and followed by my name and inverter.

Now we have our new file, lets extract the lef file from the mag using command
lef write
![image](https://github.com/user-attachments/assets/f975fcd5-2725-4339-b517-4e52be3d06d2)
It creates a lef file in the directory as,
 ![image](https://github.com/user-attachments/assets/074a3fdf-ba41-44a4-8454-b3a746f6b11c)

Which contains
 
![image](https://github.com/user-attachments/assets/7f7f89d4-14bc-4ee2-98c8-0b7cf132b648)

Now lets include our custom cell into the openlane flow.
For this the first step is to copy the libraries.
There are different flaovours of library,
Typical fast and slow we must copy all these into the SRC file.
![image](https://github.com/user-attachments/assets/83d558ff-e943-4db9-a5d0-a30a894d1122)
![image](https://github.com/user-attachments/assets/318197bf-28e2-4041-a242-e9e1cb62bc18)
![image](https://github.com/user-attachments/assets/abdc88b4-041f-44df-a8a8-64e731e4858b)
These three different types of library each has definition of our cell.
As you can see these are named differently the names are named according to the following convention.

## Library naming convention
PVT :
•	P – process
•	V – voltage
•	T – temperature
### Name  : “sky130_fd_sc_hd_tt_025C_1v80”
here,
>fd  -- foundry  -- meaning cells are model according to the foundry specification.

> Sc – standard cell 

> Hd – high density – this indicates that the library is optimized for minimal area.

> 025C – meaning the standard verified temperature is 25 degree centigrade

> 1v80 – the operating  voltage is 1.8 V .

After copying the src looks like this,
![image](https://github.com/user-attachments/assets/a5f84db4-0111-4b7b-ab7f-0e95a55b9629)
Next we must modify the config.tcl to include the lef file during the flow for which make the following change
![image](https://github.com/user-attachments/assets/c596fb67-4c15-4d2d-bc2e-bb7b9eba8691)
Next we invoke the openlane 
![image](https://github.com/user-attachments/assets/dd6bb44b-b792-480c-8033-f27fa60f0440)
After the regular process we must also include the below lines for the dditonal .lef file to be included in the flow.
 
![image](https://github.com/user-attachments/assets/04a6e05d-bfc8-48ef-bf5d-c7a0a9ee79bc)

As you see in the stat  
![image](https://github.com/user-attachments/assets/19286d39-5b27-48e0-97e1-d7beaedfdcde)

That we have successfully included our own inverter into the design.
But as we can wee there is a negative violation of  
-711.59
![image](https://github.com/user-attachments/assets/818408f4-1fde-40c3-99d2-64c1684d3f8a)

Ok lets change the strategy 
![image](https://github.com/user-attachments/assets/c190e619-aea4-4b10-b5bc-b716e44fcedd)
 
Aftr changing few more paramters we have achieved 
![image](https://github.com/user-attachments/assets/526a0c75-9612-4dc3-b7c8-644d2e0aa21f)
 
The new stats is,
![image](https://github.com/user-attachments/assets/dbf869e2-e51c-4e78-82df-cf24aef18f26)
Next step is to run floor plan and placement
Here is the output,

![image](https://github.com/user-attachments/assets/4b8bab61-06d1-450f-855b-fe19da9b55dd)
The next process is to perform STA for which we will se open STA.
For which we need to follow the below procedres,
![image](https://github.com/user-attachments/assets/b2180311-6c4a-4ac2-810c-c2f2e5995bb3)
 
And the command is 
![image](https://github.com/user-attachments/assets/57d82ad8-9321-42ca-ae84-349cff2f76d5)
Which gives a detailed timing report like this 


Which can be used to optimize the design.
We can also use replace command to replace a slower cell with a faster cell but since we have zero slack already it is being skipped. But if modification are done we must use
Write_verilog 
To reflect the changes to the design.
On any change we must do floorplan and placement again to reflect the changes.
This cycle must be repeted until all timing violations are rectified.

The next step is to run clock tree synthesis
Command : cts
This will invoke tritoncts the opensource cts tool.
Now 











