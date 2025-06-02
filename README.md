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




























