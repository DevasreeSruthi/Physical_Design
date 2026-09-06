# Open-Source EDA, OpenLane and Sky130 PDK

## Module 1: Inception of Open-Source EDA, OpenLane and Sky130 PDK

This README contains structured notes on:

- How to talk to computers
- QFN-48 package, chip, pads, core, die and IPs
- RISC-V Instruction Set Architecture (ISA)
- SoC design using OpenLane
- Digital ASIC design flow
- PDK and Process Design Kits
- Sky130 open-source PDK
- RTL-to-GDSII physical design flow
- Synthesis, floorplanning, placement, CTS, routing and signoff
- OpenLane ASIC flow
- Design Space Exploration
- Basic OpenLane/Sky130 setup commands

---
<img width="844" height="667" alt="image" src="https://github.com/user-attachments/assets/bcc5da3e-1b89-4af4-90d9-a1982016b2da" />

# 1. How to Talk to Computers

## 1.1 Introduction to QFN-48 Package

A **QFN-48 (Quad Flat No-lead, 48-pin)** package is a semiconductor package containing the chip/die and providing electrical connections between the chip and the outside world.

### Package hierarchy
<img width="940" height="704" alt="image" src="https://github.com/user-attachments/assets/3010168f-55c8-40ec-8286-807f243ebd43" />

A typical IC can be understood through the following hierarchy:

```text
Package
   |
   +-- Chip / Die
         |
         +-- Core
         |
         +-- Pads
         |
         +-- IPs
```

### Important terms

| Term | Meaning |
|---|---|
| Package | The physical package that contains and protects the silicon die and provides external connections. |
| Chip / Die | The silicon device containing the implemented circuit. |
| Core | The main internal region where the digital/analog circuitry is implemented. |
| Pads | Electrical interface points connecting internal signals to the package/external world. |
| IP | Intellectual Property block; a reusable circuit/design block. |
<img width="940" height="591" alt="image" src="https://github.com/user-attachments/assets/4850fdaf-8c09-4748-bc01-d6c89a90dbde" />

> **Note:** The package size and pad arrangement depend on the particular package. The handwritten notes mention a 7 mm × 7 mm package as an example.

---

# 2. Open Chip: Pads, Core and Die

## Pads

Pads provide the interface for signals entering and leaving the chip.

They can carry:

- Input signals
- Output signals
- Power
- Ground
- Clock and control signals

## Core

The **core** is the region where the main logic and other circuit blocks are placed.

For a digital SoC, this can include:

- CPU
- SRAM
- ADC/DAC interfaces
- PLL
- SPI
- Other IP blocks

## Die

The **die** is the complete piece of fabricated silicon containing the circuit.

```text
+----------------------------------+
|              DIE                 |
|                                  |
|     +----------------------+     |
|     |        CORE          |     |
|     |                      |     |
|     |  CPU + SRAM + IPs    |     |
|     |                      |     |
|     +----------------------+     |
|                                  |
|     PAD   PAD   PAD   PAD        |
+----------------------------------+
```

---

# 3. Example: RISC-V SoC

A RISC-V based SoC can contain a CPU core together with memory and peripheral IPs.

```text
RISC-V SoC
   |
   +-- CPU / SoC
   +-- SRAM
   +-- ADC
   +-- DAC
   +-- PLL
   +-- SPI
```
<img width="746" height="415" alt="image" src="https://github.com/user-attachments/assets/ea4f894b-619b-45e4-84fb-e9ce6f6ea457" />

Some of these blocks can be implemented using **foundry IPs**, while reusable/open-source blocks can be used as **design IP**.

---

# 4. RISC-V Instruction Set Architecture (ISA)

## What is an ISA?

An **Instruction Set Architecture (ISA)** defines the interface between software and hardware.

RISC-V is an open standard ISA.

```text
Application Software
        |
        v
      ISA
        |
        v
    CPU Hardware
        |
        v
    Physical Layout
```
<img width="940" height="346" alt="image" src="https://github.com/user-attachments/assets/526bbe8c-2b84-4567-96da-be5e3285160a" />

## From C Program to Hardware

```text
C Program
   |
   v
Compiler
   |
   v
Assembly Language
   |
   v
Machine Language
   |
   v
CPU executes instructions
```

For a RISC-V CPU:

```text
C Program
   |
   v
RISC-V Assembly
   |
   v
RISC-V Machine Code
   |
   v
RISC-V CPU
```

---

# 5. Software-to-Hardware Flow

```text
RISC-V Architecture
        |
        v
Implementation
        |
        v
RTL
        |
        v
Physical Design / Layout
        |
        v
GDSII
```
<img width="1155" height="609" alt="image" src="https://github.com/user-attachments/assets/c256f6e4-0e14-4fe9-b8ba-9400ab12e1bb" />

### RTL

**RTL (Register Transfer Level)** describes the digital hardware using an HDL such as:

- Verilog
- SystemVerilog
- VHDL

---

# 6. Digital ASIC Design

## What is an ASIC?

An **ASIC (Application-Specific Integrated Circuit)** is an integrated circuit designed for a specific application.

```text
RTL + EDA Tools + PDK Data
             |
             v
            ASIC
```

### Main inputs

1. **RTL design**
2. **EDA tools**
3. **PDK data**

---

# 7. Open-Source Digital ASIC Design

Open-source ASIC design combines:

```text
                  +----------------+
                  |   EDA Tools    |
                  | OpenROAD etc.  |
                  +-------+--------+
                          |
                          v
+-------------+      +--------+      +----------------+
| RTL Designs | ---> |  ASIC  | <--- |   PDK Data    |
+-------------+      +--------+      +----------------+
```

### RTL design sources

- LibreCores
- OpenCores
- GitHub repositories

### EDA tools

- OpenLane
- OpenROAD
- Yosys
- Magic
- Netgen
- KLayout

### PDK data

The PDK supplies technology-specific information required to convert a logical design into a manufacturable layout.

---

# 8. What is a PDK?

## PDK = Process Design Kit

A **PDK (Process Design Kit)** is a collection of files and models used by designers and EDA tools to design an IC for a particular semiconductor manufacturing process.

```text
IC Design
    |
    v
PDK / Technology Interface
    |
    v
Fabrication Process
```

The PDK acts as an interface between the **fab/process technology** and the **designers/EDA tools**.

---

# 9. What Does a PDK Contain?

A PDK can contain:

- Process design rules
- Device models
- Standard-cell libraries
- I/O libraries
- Technology files
- Extraction information
- Verification rules
- Layout information
- SPICE models
- DRC rules
- LVS rules
- PEX/extraction rules

### Process Design Rules

- DRC — Design Rule Check
- LVS — Layout Versus Schematic
- PEX — Parasitic Extraction

---

# 10. Open-Source PDK

An important open-source example is the **SkyWater 130 nm PDK**, commonly called **Sky130**.

```text
Sky130
  |
  +-- 130 nm process
  +-- Standard-cell libraries
  +-- I/O libraries
  +-- Device models
  +-- DRC/LVS/PEX information
  +-- Technology files
```

---

# 11. Is 130 nm Fast?

The notes mention:

```text
Intel Pentium 4:
~3.46 GHz
```

and approximately:

```text
~327 MHz post-layout clock frequency
```

for a single-cycle RISC-V CPU in a Sky130-based implementation.

> **Important:** Clock frequency is highly design-dependent. These numbers should be treated as examples from the notes, not as a guaranteed Sky130 specification.

---

# 12. ASIC RTL-to-GDSII Flow

## Objective

```text
RTL
 |
 v
GDSII
```

A simplified flow is:

```text
RTL
 |
 v
Synthesis
 |
 v
Floorplan + Power Planning
 |
 v
Placement
 |
 v
CTS
 |
 v
Routing
 |
 v
Signoff
 |
 v
GDSII
```

Where:

- **CTS** = Clock Tree Synthesis
- **GDSII** = final physical layout database

---

# 13. OpenLane RTL-to-GDSII Flow

```text
RTL
 |
 v
Synthesis
 |
 v
FP + PP
 |
 v
Place
 |
 v
CTS
 |
 v
Route
 |
 v
Signoff
 |
 v
GDSII
```

| Abbreviation | Meaning |
|---|---|
| RTL | Register Transfer Level |
| FP | Floorplanning |
| PP | Power Planning |
| CTS | Clock Tree Synthesis |
| GDSII | Graphic Design System II layout database |

---

# 14. Synthesis

## Definition

**Synthesis** converts RTL into a circuit/netlist made from components in a standard-cell library.

```text
RTL
 |
 v
Synthesis
 |
 v
Gate-level Netlist
 |
 v
Standard Cells
```

Example RTL:

```verilog
always @(posedge clk) begin
    if (c)
        q <= a;
    else
        q <= b;
end
```

---

# 15. Standard-Cell Libraries

Standard cells have predefined layouts and are designed to be placed in regular rows.

Examples:

- AND
- OR
- NOT
- NAND
- NOR
- Flip-flops
- Buffers
- Inverters

Different views can include:

```text
Electrical
HDL
SPICE
Layout
   |
   +-- Abstract
   +-- Detailed
```

---

# 16. Floorplanning and Power Planning

## Floorplanning

Floorplanning determines the physical organization of major blocks on the chip.

### Chip floorplanning

- Position major system-building blocks
- Define chip dimensions
- Place I/O pads
- Define core boundaries

```text
+-----------------------------------+
|              CHIP                 |
|                                   |
|   +---------+     +----------+   |
|   |  CPU    |     |   SRAM   |   |
|   +---------+     +----------+   |
|                                   |
|       Other Macros / IPs          |
|                                   |
+-----------------------------------+
```

### Macro floorplanning

- Macro dimensions
- Macro positions
- Macro locations
- Rows/placement regions
- Relationships between large blocks

---

# 17. Power Planning

Power planning creates the power-delivery structure needed to distribute:

- VDD
- VSS/GND

Typical structures include:

- Power rings
- Power straps
- Power rails

```text
VDD  =================================
        |     |     |     |     |
        |     |     |     |     |
VSS  =================================
```

---

# 18. Placement

Placement puts standard cells into the floorplan.

```text
Floorplan
   |
   v
Placement
   |
   v
Cells positioned on rows
```

Placement is commonly performed in two stages:

### Global Placement

Optimizes:

- Wirelength
- Congestion
- Timing

### Detailed Placement

Legalizes and refines cell positions according to placement rules and site alignment.

---

# 19. Clock Tree Synthesis (CTS)

**Clock Tree Synthesis** creates a clock distribution network that delivers the clock signal to sequential elements.

Objectives:

- Deliver the clock to required sequential elements
- Minimize clock skew
- Maintain good timing
- Build a physically reasonable network

```text
                 CLK
                  |
                Buffer
               /      \
          Buffer      Buffer
          /   \       /   \
        FF    FF     FF    FF
```

### Clock Skew

**Clock skew** is the difference in clock arrival time between different sequential elements.

---

# 20. Routing

Routing implements the physical interconnect between placed cells and macros using available metal layers.

```text
Placed Cells
     |
     v
Routing
     |
     v
Physical Metal Interconnect
```

### Global Routing

- Divides the design into routing regions
- Generates routing guides
- Estimates and optimizes interconnect paths

### Detailed Routing

- Uses routing guides
- Creates actual physical wires
- Obeys technology design rules
- Connects nets using metal layers and vias

```text
Global Routing
      |
      v
Routing Guides
      |
      v
Detailed Routing
      |
      v
Actual Metal Wiring
```

---

# 21. Signoff

Signoff is the final verification stage before manufacturing.

## DRC — Design Rule Check

Checks whether the physical layout follows manufacturing design rules.

## LVS — Layout Versus Schematic

Checks whether the extracted physical layout corresponds to the intended circuit/netlist.

## PEX — Parasitic Extraction

Extracts parasitic effects such as:

- Resistance
- Capacitance

## STA — Static Timing Analysis

Important timing concepts:

- Setup time
- Hold time
- Clock uncertainty
- Propagation delay
- Slack
- Critical paths

---

# 22. OpenLane

## What is OpenLane?

OpenLane is an open-source RTL-to-GDSII ASIC flow.

```text
RTL
 |
 v
OpenLane
 |
 +--> Synthesis
 |
 +--> Floorplanning
 |
 +--> Power Planning
 |
 +--> Placement
 |
 +--> CTS
 |
 +--> Routing
 |
 +--> Signoff
 |
 v
GDSII
```

---

# 23. OpenLane and Open-Source EDA

Common open-source components include:

- OpenLane
- OpenROAD
- OpenDB
- OpenSTA
- Yosys
- Magic
- Netgen
- KLayout

---

# 24. OpenLane ASIC Flow

## Main Goal

The goal is to produce a clean GDSII layout with minimal or no violations.

A clean flow aims for:

- No LVS violations
- No DRC violations
- Timing constraints satisfied
- Successful physical implementation

```text
RTL
 |
 v
Synthesis
 |
 v
Floorplan
 |
 v
Power Planning
 |
 v
Placement
 |
 v
CTS
 |
 v
Routing
 |
 v
Signoff
 |
 v
GDSII
```

---

# 25. OpenLane Modes of Operation

1. **Autonomous**
2. **Interactive**

## Autonomous Mode

The flow runs automatically using configuration parameters.

## Interactive Mode

The user can inspect intermediate results and interact with the flow during development/debugging.

---

# 26. OpenLane and Sky130

```text
RTL Design
    +
OpenLane
    +
Sky130 PDK
    |
    v
ASIC Layout
    |
    v
GDSII
```

This enables an end-to-end digital ASIC flow using open-source tools and an open-source 130 nm PDK.

---

# 27. Containerized OpenLane

Advantages include:

- Functional environment out of the box
- Reproducible tool environment
- Easier installation
- Easier dependency management
- Repeatable flow execution

Docker can be used to run the required environment.

---

# 28. Design Space Exploration

## Definition

**Design Space Exploration (DSE)** means trying different flow configurations to find a good implementation.

Possible configurations:

```text
Configuration 1
Configuration 2
Configuration 3
...
Configuration N
```

The objective is to find a configuration with a desirable combination of:

- Area
- Timing
- Power
- Congestion
- Utilization
- Routing quality

```text
             Design Space
                  |
        +---------+---------+
        |         |         |
      Config A  Config B  Config C
        |         |         |
        v         v         v
      Results   Results   Results
        \         |         /
         \        |        /
          +-------+-------+
                  |
                  v
           Best Configuration
```

---

# 29. Useful Linux Commands

## List files

```bash
ls
ls -ltr
ls -lthr
```

## Show current directory

```bash
pwd
```

## Change directory

```bash
cd <directory>
cd ..
cd ../
cd ../../
cd /
```

## Clear terminal

```bash
clear
```

## Show help

```bash
ls --help
```

---

# 30. Running OpenLane with Docker

```bash
docker run -it -v $(pwd):/openlane $PDK_ROOT/openlane
```

> The exact image name, mounted directories and environment variables depend on the OpenLane installation/version.

---

# 31. PDK Environment Variables

```bash
echo $PDK_ROOT
```

Example:

```bash
export PDK_ROOT=/path/to/pdks
```

---

# 32. OpenLane Working Environment

```text
openlane/
├── designs/
├── pdks/
├── scripts/
├── config.tcl
└── runs/
```

> The exact structure varies by OpenLane version.

---

# 33. Overall Learning Flow

```text
                    SOFTWARE
                       |
                       v
                 C / Applications
                       |
                       v
                  RISC-V ISA
                       |
                       v
                    RTL/HDL
                       |
                       v
                  ASIC Design
                       |
          +------------+------------+
          |                         |
          v                         v
       EDA Tools                  PDK
          |                         |
          +------------+------------+
                       |
                       v
                    Synthesis
                       |
                       v
              Floorplan + Power
                       |
                       v
                   Placement
                       |
                       v
                       CTS
                       |
                       v
                    Routing
                       |
                       v
                    Signoff
                       |
                       v
                     GDSII
                       |
                       v
                  Fabrication
```

---

# 34. Key Terms 

| Term | Full Form / Meaning |
|---|---|
| ASIC | Application-Specific Integrated Circuit |
| EDA | Electronic Design Automation |
| RTL | Register Transfer Level |
| HDL | Hardware Description Language |
| ISA | Instruction Set Architecture |
| RISC-V | Open standard RISC instruction set architecture |
| PDK | Process Design Kit |
| DRC | Design Rule Check |
| LVS | Layout Versus Schematic |
| PEX | Parasitic Extraction |
| STA | Static Timing Analysis |
| CTS | Clock Tree Synthesis |
| IP | Intellectual Property |
| SoC | System on Chip |
| QFN | Quad Flat No-lead |
| GDSII | Graphic Design System II |
| SCL | Standard Cell Library |
| FP | Floorplanning |
| PP | Power Planning |
| DSE | Design Space Exploration |

---


- **Placement** positions standard cells.
- **CTS** distributes the clock.
- **Routing** creates physical interconnects.
- **Signoff** verifies the physical implementation.
- **GDSII** represents the final physical layout database.
- **OpenLane** automates the RTL-to-GDSII flow.
- **Sky130** provides an open 130 nm process technology/PDK for open-source ASIC work.
