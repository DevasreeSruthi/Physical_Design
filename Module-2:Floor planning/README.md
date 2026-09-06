
# Module 2 — 

## Good Floor Plan vs Bad Floor Plan and Introduction to Library Cells

---

# 1. Chip Floor Planning Considerations

## 1.1 Utilisation Factor and Aspect Ratio

### Define Width and Height of Core and Die

Take 2 flip-flops.

Consider a netlist with:

- 2 flip-flops
- 2 gates

Convert this into physical dimensions.
<img width="940" height="572" alt="image" src="https://github.com/user-attachments/assets/08771416-4cc5-4a3f-bac9-8555932ba94a" />

### Rough Dimension

Assume:

- Area of 1 flip-flop = 1 sq. unit
- Area of 1 standard cell/gate = 1 sq. unit

Therefore:

```text
2 flip-flops + 2 gates

= 2 + 2
= 4 sq. units
````
<img width="561" height="586" alt="image" src="https://github.com/user-attachments/assets/62674572-4198-4948-b9ff-39cfacbc2e2e" />

The cells have to be placed on a single plate.

---
<img width="940" height="376" alt="image" src="https://github.com/user-attachments/assets/3309b9be-ab7d-43f8-8787-42838d64365b" />

# 2. Core and Die of a Chip

Placing all logical cells on the core occupies some percentage of the total core area.

If all logical cells occupy the complete core:

```text
100% utilisation
```

## Utilisation Factor

```text
                     Area occupied by netlist
Utilisation Factor = -------------------------
                         Total area of core
```

Or:

$$
Utilisation\ Factor =
\frac{Area\ occupied\ by\ netlist}
{Total\ area\ of\ core}
$$

---

# 3. Aspect Ratio

Aspect ratio is defined as:

```text
              Height
Aspect Ratio = ------
              Width
```

$$
Aspect\ Ratio = \frac{Height}{Width}
$$

---

# 4. Concept of Preplaced Cells

## Definition

Preplaced cells are cells whose locations are fixed before the placement process.

```text
One side   → Inputs
Other side → Outputs
```

The preplaced cells:

* Are never touched/moved.
* Cannot be moved once placed.
* Have to be well placed.
* Need to be carefully positioned during floorplanning.
<img width="940" height="635" alt="image" src="https://github.com/user-attachments/assets/c110650a-74e9-4d12-8cb5-a3c22d549534" />

---

# 5. Decoupling Capacitors

Decoupling capacitors are used for providing charge when required.

```text
All capacitors are charged to V.

When they need to discharge to zero volts:

V → 0 V
```
<img width="940" height="567" alt="image" src="https://github.com/user-attachments/assets/04e81840-b61b-40a3-aed2-999c9ff0c9ad" />

This discharge can cause current to flow through the ground tap point.

If many capacitors discharge through a single ground tap point, it can cause:

```text
Ground bounce
```

Therefore, proper placement of decoupling capacitors is required.

Blocks are surrounded by decoupling capacitors so that sufficient charge can be supplied locally.

---
<img width="940" height="567" alt="image" src="https://github.com/user-attachments/assets/df484c65-ac33-46e7-8323-71fdb5c25790" />

# 6. Power Planning

Power planning deals with supplying power to the cells.

Important power connections include:

```text
VDD
VSS
Power rings
Power straps
Power taps
```
<img width="940" height="160" alt="image" src="https://github.com/user-attachments/assets/1cfb766c-75a2-4276-8f50-2a1b667cb36c" />

---

# 7. Ground Bounce

When capacitors are charged to V and need to discharge to zero volts:

```text
V → 0 V
```
<img width="940" height="253" alt="image" src="https://github.com/user-attachments/assets/d8e7b39e-8e9b-4a5b-b346-7c8c12d3d957" />

If many capacitors discharge through a single ground tap point, large current can flow through the ground connection.

This causes:

```text
Ground bounce
```

Therefore, the ground network has to be properly designed.

---

# 8. VDD Voltage Droop

Capacitors that are initially at zero volts may need to charge to V.

```text
0 V → V
```

If many capacitors charge through a single VDD tap point, a large current is required.

This can cause:

```text
VDD voltage droop
```

Therefore, power supply distribution must be properly planned.

---
<img width="940" height="239" alt="image" src="https://github.com/user-attachments/assets/bc18fb76-605c-41a1-9ad5-9639e6e61cde" />
<img width="940" height="528" alt="image" src="https://github.com/user-attachments/assets/a4052dd6-71df-4530-9123-935452040e6d" />

# 9. Pin Placement

Pin placement is an important part of floorplanning.

The connectivity information between gates is coded using:

```text
VHDL
Verilog
```

The connections are represented as a:

```text
Netlist
```

The inputs and outputs have to be physically placed.

```text
One side   → Inputs
Other side → Outputs
```

Proper pin placement helps in reducing routing problems.

---
<img width="940" height="592" alt="image" src="https://github.com/user-attachments/assets/7c187a99-5cd6-4b6b-a297-3c1d9fa18139" />

# 10. Floorplanning

Floorplan is created before placement.

The general physical-design sequence is:

```text
Logic Synthesis
      ↓
Floorplanning
      ↓
Placement
      ↓
CTS
      ↓
Routing
      ↓
STA
```

Where:

```text
CTS = Clock Tree Synthesis
STA = Static Timing Analysis
<img width="940" height="579" alt="image" src="https://github.com/user-attachments/assets/deb2980a-3ad3-48f4-8413-e58b262b4463" />

```
<img width="940" height="386" alt="image" src="https://github.com/user-attachments/assets/7bd49b5a-0833-4350-9fb3-ae51ba0a63c0" />
<img width="581" height="452" alt="image" src="https://github.com/user-attachments/assets/45830aa5-08ac-49c2-b10c-209040a43c4a" />

---

# 11. Netlist and Physical Cells

A netlist contains the logical connectivity of the design.

The logical design must be converted into physical cells.

```text
Netlist
   ↓
Physical cells
   ↓
Placement
```

The physical cells are obtained from the standard-cell library.

---

# 12. Standard Cell Library

A standard-cell library contains predefined cells used during physical implementation.

Examples:

```text
AND
OR
Buffer
Flip-Flop
Latch
ICG
```

The  also mention:

```text
PFF
```

The cells have predefined physical and electrical characteristics.

---

## OpenLane Physical Design Flow

### 1. Run Synthesis

```tcl
run_synthesis
```

### Meaning

Synthesis converts the **RTL code into a gate-level netlist**.

```text
RTL
 ↓
Synthesis
 ↓
Gate-Level Netlist
```

During synthesis:

- RTL code is read and analyzed.
- Logic is optimized.
- Logic is mapped to standard cells available in the library.
- Gates such as AND, OR, INV, BUF, and Flip-Flops are selected.
- A gate-level netlist is generated.

### In simple words

**Synthesis tells us WHAT logic/cells are required to implement the RTL.**

```text
RTL → Standard Cells / Gates
```

---

### 2. Run Floorplanning

```tcl
run_floorplanning
```

### Meaning

Floorplanning decides the **overall physical organization of the chip**.

It determines things such as:

- Core size
- Die size
- Core width
- Core height
- Aspect ratio
- Utilization
- Pin placement
- Placement of preplaced cells
- Power planning considerations

```text
Gate-Level Netlist
        ↓
Floorplanning
        ↓
Core + Die + Pins + Physical Organization
```

### Core and Die

The **die** is the complete chip area.

The **core** is the area inside the die where the standard cells are placed.

```text
+-----------------------------+
|             DIE             |
|                             |
|     +-------------------+   |
|     |       CORE        |   |
|     |                   |   |
|     |  Standard Cells   |   |
|     |                   |   |
|     +-------------------+   |
|                             |
+-----------------------------+
```

### Utilization

Utilization tells how much of the core area is occupied by standard cells.

```text
Utilization =
Area occupied by standard cells
-------------------------------- × 100%
Core area
```

### Aspect Ratio

```text
Aspect Ratio = Height / Width
```

### Why is floorplanning important?

A good floorplan helps in:

- Reducing wire length
- Reducing congestion
- Improving timing
- Improving power distribution
- Making routing easier

A bad floorplan can cause:

- Long wires
- High capacitance
- Routing congestion
- Timing problems
- Higher power

### In simple words

**Floorplanning decides HOW BIG the physical area is and HOW the chip is organized.**

```text
Core + Die + Pins + Power + Physical Organization
```

---
<img width="759" height="569" alt="image" src="https://github.com/user-attachments/assets/9f51dcf4-ab0a-4e78-a23a-3cdc227ebdb4" />

### 3. Run Placement

```tcl
run_placement
```

### Meaning

Placement decides the **physical location of each standard cell inside the core**.

After synthesis, we know which cells are required.

After floorplanning, we know the available physical area.

Placement decides:

```text
Where exactly should each standard cell be placed?
```

Example:

```text
+--------------------------------+
|                                |
|   INV       AND                |
|                                |
|            FF                  |
|                                |
|   BUF                 OR       |
|                                |
+--------------------------------+
```

### What happens during placement?

- Standard cells are placed inside the core.
- The cells are positioned according to the floorplan.
- The tool tries to reduce wire length.
- The tool tries to reduce congestion.
- Timing is considered.
- Cell locations are optimized.

### Why is placement important?

The distance between cells affects the interconnect.

```text
Cell A ------------------------ Cell B
           Long wire
```

Long wires can cause:

- Higher capacitance
- Higher delay
- More power consumption
- Routing problems

A shorter connection is generally preferred:

```text
Cell A -------- Cell B
       Short wire
```

### Repeaters / Buffers

For long connections, buffers or repeaters may be inserted to improve signal quality and timing.

```text
Cell A ─────────────── Cell B
```

can become:

```text
Cell A ─── Buffer ─── Buffer ─── Cell B
```

### In simple words

**Placement decides WHERE EXACTLY each standard cell should be located inside the core.**

```text
Standard Cells → Physical Locations
```
<img width="940" height="715" alt="image" src="https://github.com/user-attachments/assets/d819a429-174c-43e3-a97b-342f7133f6ca" />

---

# Easy Way to Remember

### Synthesis

**WHAT?**

```text
RTL
 ↓
run_synthesis
 ↓
What cells/gates are required?
```

### Floorplanning

**HOW BIG / HOW ORGANIZED?**

```text
Gate-Level Netlist
 ↓
run_floorplanning
 ↓
Core + Die + Pins + Physical Organization
```

### Placement

**WHERE EXACTLY?**

```text
Floorplan
 ↓
run_placement
 ↓
Exact physical locations of standard cells
```

# Overall Flow

```text
RTL
 ↓
run_synthesis
 ↓
Gate-Level Netlist
 ↓
run_floorplanning
 ↓
Core / Die / Pins / Physical Organization
 ↓
run_placement
 ↓
Standard Cell Placement
```
# 13. Cell Design Flow

Cell design is divided into three major parts:

```text
Circuit Design
      ↓
Layout Design
      ↓
Characterization
```

---

# 14. Cell Design Inputs

The required inputs include:

```text
PDK
DRC & LVS Rules
SPICE Models
Library
User Defined Specifications
```

---

## 14.1 PDK

```text
PDK = Process Design Kit
```

The PDK provides process-related information required for cell design.

---

## 14.2 DRC & LVS Rules

The design requires:

```text
DRC Rules
LVS Rules
```

These are used to verify the physical layout.

---

## 14.3 SPICE Models

SPICE models are used for circuit simulation and characterization.

---

## 14.4 Library

Library information is required during cell design and characterization.

---

## 14.5 User Defined Specifications

The cell must satisfy the specifications defined for the design.

---
<img width="940" height="741" alt="image" src="https://github.com/user-attachments/assets/5edd1e09-0a6b-427f-bc02-38155c5ec269" />
<img width="852" height="609" alt="image" src="https://github.com/user-attachments/assets/dee3a077-122e-46de-9a10-bd6afca20616" />


# 15. Circuit Design

The first major stage of cell design is:

```text
Circuit Design
```

The transistor-level circuit is designed according to the required functionality.

Examples of cells include:

```text
AND
OR
Inverter
Buffer
Flip-Flop
Latch
ICG
```

---
<img width="940" height="696" alt="image" src="https://github.com/user-attachments/assets/97626aeb-c63d-4e41-95fb-eefc28774a8c" />

# 16. Layout Design

After circuit design, the physical layout of the cell is created.

The layout defines:

* Physical dimensions
* Transistor placement
* Metal connections
* Input pins
* Output pins
* Power connections

The layout has to satisfy the physical design rules.

---
<img width="628" height="719" alt="image" src="https://github.com/user-attachments/assets/6c2b051a-f873-409e-9901-21ad6284517a" />

# 17. Characterization

After circuit and layout design, the cell is characterized.

Characterization determines the behaviour of the cell.

The  mention:

```text
Timing
Power
Noise
Functionality
```
<img width="940" height="505" alt="image" src="https://github.com/user-attachments/assets/696a1feb-6a9a-470b-ac52-a85b43fe35a0" />

---

# 18. Characterization

Characterization is required to determine the characteristics of the cell.

The  mention:

```text
Need for libraries and characterization
```

The characterization information is eventually used to create library data.

---

# 19. Characterization Flow

```text
Circuit Design
      ↓
Layout Design
      ↓
Characterization
      ↓
Library
```

The characterization process determines the timing, power, noise and functional behaviour.

---
<img width="940" height="371" alt="image" src="https://github.com/user-attachments/assets/fd146ab7-8da4-4133-b182-0e4e9862023a" />


# 20. Characterization Parameters

The following threshold parameters:

```text
slew-low-rise-thr
slew-high-rise-thr

slew-low-fall-thr
slew-high-fall-thr

in-rise-thr
in-fall-thr

out-rise-thr
out-fall-thr
```

These are used for timing and slew measurements.

---

# 21. Input Rise Threshold

For an input rising transition:

```text
Input

        /
       /
      /
-----/------------
    ↑
in-rise-thr
```

The input crosses the defined rising threshold.

```text
in-rise-thr
```

---

# 22. Input Fall Threshold

For an input falling transition:

```text
Input

------------
        \
         \
          \
           \---
             ↑
          in-fall-thr
```

The input crosses the defined falling threshold.

```text
in-fall-thr
```

---

# 23. Output Rise Threshold

For an output rising transition:

```text
Output

        /
       /
      /
-----/------------
    ↑
out-rise-thr
```

The output crosses:

```text
out-rise-thr
```

---

# 24. Output Fall Threshold

For an output falling transition:

```text
Output

------------
        \
         \
          \
           \---
             ↑
          out-fall-thr
```

The output crosses:

```text
out-fall-thr
```

---

# 25. Slew

Slew represents the transition time between two threshold levels.

There are two types:

```text
Rise Slew
Fall Slew
```

---

# 26. Rise Slew

Rise slew is measured between:

```text
slew-low-rise-thr
slew-high-rise-thr
```

The rising waveform passes through the lower and upper rise thresholds.

```text
slew-low-rise-thr
        ↓
        /
       /
      /
     /
    ↓
slew-high-rise-thr
```

The rise slew is:

```text
Time at slew-high-rise-thr
-
Time at slew-low-rise-thr
```

Therefore:


---

# 27. Fall Slew

Fall slew is measured between:

```text
slew-low-fall-thr
slew-high-fall-thr
```

The falling waveform passes through the defined thresholds.

The fall slew is calculated using the difference between the threshold crossing times.


---

# 28. 50% Threshold

The  mention 50% threshold values.

```text
in-rise-thr  = 50%
in-fall-thr  = 50%

out-rise-thr = 50%
out-fall-thr = 50%
```

These threshold points are used for delay calculations.

---

# 29. Propagation Delay

Propagation delay is the time difference between the input transition and the corresponding output transition.

General form:

```text
Propagation Delay
=
Output transition time
-
Input transition time
```

---

# 30. Rise Propagation Delay

For a rising transition:

```text
Input
  ↓
in-rise-thr
  ↓
Cell
  ↓
out-rise-thr
  ↓
Output
```

Therefore:

```text
Rise Delay
=
Time(out-rise-thr)
-
Time(in-rise-thr)
```



---

# 31. Fall Propagation Delay

For a falling transition:

```text
Input
  ↓
in-fall-thr
  ↓
Cell
  ↓
out-fall-thr
  ↓
Output
```

Therefore:

```text
Fall Delay
=
Time(out-fall-thr)
-
Time(in-fall-thr)
```


---

# 32. Preparation Delay


```text
Preparation Delay
```

The threshold values shown are:

```text
in-rise-thr  (50%)
in-fall-thr  (50%)

out-rise-thr (50%)
out-fall-thr (50%)
```

These threshold crossing times are used in determining timing delays.

---

# 33. Timing Calculation

For a rising transition:

```text
Time at output rise threshold
-
Time at input rise threshold
```

For a falling transition:

```text
Time at output fall threshold
-
Time at input fall threshold
```

General form:

```text
Delay = T(out) - T(in)
```

---

# 34. Slew Calculation

For rising transition:

```text
Time(slew-high-rise-thr)
-
Time(slew-low-rise-thr)
```

For falling transition:

```text
Time(slew-high-fall-thr)
-
Time(slew-low-fall-thr)
```

---


# 35. Timing Characterization

Timing characterization determines the timing behaviour of the cell.

It considers:

```text
Input transition
Output transition
Input slew
Output slew
Load
Delay
```

The cell is characterized under different conditions.

---

# 36. Power Characterization

Power characterization determines the power behaviour of the cell.

The characterization information can be used by physical-design tools.

---

# 37. Noise Characterization

Noise characterization determines the noise-related behaviour of the cell.

The  noise a\is one of the important characterization parameters.

---

# 38. Functional Characterization

Functional characterization verifies that the cell performs the intended logical function.

Examples:

```text
AND cell
→ Performs AND function

OR cell
→ Performs OR function

Inverter
→ Produces complement of input
```

---

# 39. Library Data

After characterization, information is generated for the library.

The information includes:

```text
Timing
Power
Noise
Functionality
Cell information
```

---

# 40. Complete Cell Design Flow

```text
Inputs
  │
  ├── PDK
  ├── DRC & LVS Rules
  ├── SPICE Models
  ├── Library
  └── User Defined Specifications
  │
  ▼
Circuit Design
  │
  ▼
Layout Design
  │
  ▼
Characterization
  │
  ├── Timing
  ├── Power
  ├── Noise
  └── Function
  │
  ▼
Library
```

---

# 41. Physical Design Flow

The following overall flow:

```text
Logic Synthesis
      ↓
Floorplanning
      ↓
Placement
      ↓
CTS
      ↓
Routing
      ↓
STA
```

Where:

```text
CTS = Clock Tree Synthesis
STA = Static Timing Analysis
```

---

# 42. Common Physical Design Stages

The common stages are:

```text
Logic Synthesis
Floorplanning
Placement
CTS
Routing
STA
```

---

# 43. Placement

Placement is the process of assigning physical locations to standard cells.

```text
Floorplan is ready for placement & routing stage
```

After floorplanning, placement is performed.

---

# 44. Placement of Standard Cells

Standard cells are placed inside the core.

The placement has to consider:

* Physical cell dimensions
* Preplaced cells
* Inputs
* Outputs
* Routing
* Power structures
* Congestion

---

# 45. Initial Placement

Initial placement determines the first physical locations of the cells.

```text
Netlist
   ↓
Standard cells
   ↓
Initial placement
```

The initial placement may later be optimized.

---

# 46. Optimized Placement

Placement is optimized based on physical requirements.

The  mention:

```text
Wire length
Capacitance
```

These parameters affect timing and signal quality.

General optimization flow:

```text
Initial Placement
       ↓
Wire length estimation
       ↓
Capacitance estimation
       ↓
Timing evaluation
       ↓
Optimization
       ↓
Final/Optimized Placement
```

---

# 47. Wire Length

Longer wires can result in:

* Larger delay
* Larger capacitance
* More routing problems

Therefore, placement tries to reduce unnecessary wire length.

```text
Long wire
   ↓
Higher parasitic effects
   ↓
Higher delay
```

---

# 48. Capacitance

Wire capacitance affects signal transition and timing.

Higher capacitance can lead to:

```text
Higher delay
Slower transition
Higher drive requirement
```

Therefore, placement optimization considers capacitance.

---

# 49. Buffer / Repeater Insertion

If a connection is long, buffers/repeaters may be inserted.

```text
Long connection
      ↓
High RC / delay
      ↓
Buffer / Repeater
      ↓
Improved signal propagation
```

The  mention:

```text
Buffer / Repeater
```

as part of placement optimization.

---

# 50. Floorplan Quality

A good floorplan should provide:

```text
Good utilization
Good aspect ratio
Proper pin placement
Proper preplaced-cell placement
Adequate routing space
Good power distribution
Low congestion
```

---

# 51. Good Floorplan

A good floorplan helps in achieving:

```text
Good Placement
      ↓
Good Routing
      ↓
Better Timing
      ↓
Better Power
      ↓
Better Overall Design
```

---

# 52. Bad Floorplan

A bad floorplan can cause:

```text
Poor placement
      ↓
Routing congestion
      ↓
Long wires
      ↓
Higher capacitance
      ↓
Timing problems
      ↓
More optimization
```

---

# 53. Core and Die Representation

```text
+--------------------------------------+
|                  DIE                 |
|                                      |
|      +--------------------------+    |
|      |           CORE           |    |
|      |                          |    |
|      |     Standard Cells       |    |
|      |     Logic Cells          |    |
|      |                          |    |
|      +--------------------------+    |
|                                      |
+--------------------------------------+
```

---



# 54. Preplaced Cells — Important Points

```text
Preplaced cells
      ↓
Fixed locations
      ↓
Cannot be moved during normal placement
      ↓
Must be carefully positioned
```

Examples of physical objects that may need fixed placement include power-related cells and other special cells.

---

# 55. Power Planning — Important Points

```text
Power Planning
      │
      ├── VDD
      ├── VSS
      ├── Power Rings
      ├── Power Straps
      ├── Power Taps
      └── Decoupling Capacitors
```

---

# 56. Power Problems

## Ground Bounce

```text
Capacitors discharge
        ↓
Large current
        ↓
Ground network
        ↓
Ground voltage disturbance
        ↓
Ground bounce
```

## VDD Droop

```text
Capacitors charge
        ↓
Large current demand
        ↓
VDD network
        ↓
Voltage reduction
        ↓
VDD droop
```

---

# 57. Library Cell Design Inputs and Outputs

```text
INPUTS
│
├── PDK
├── DRC & LVS Rules
├── SPICE Models
├── Library
└── User Specifications
│
▼
CELL DESIGN
│
├── Circuit Design
├── Layout Design
└── Characterization
│
▼
OUTPUTS
│
├── Timing
├── Power
├── Noise
├── Function
└── Library Information
```




# End of Module 2

```
```
