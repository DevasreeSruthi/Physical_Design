# Module 4 — Timing Analysis, CTS and Post-CTS STA

## Overview

Timing analysis is one of the most important stages in digital VLSI design. A circuit must not only produce the correct logical output, but it must also produce that output within the required time.

A synthesized design may be logically correct, but physical implementation introduces several real-world effects such as:

- Cell propagation delay
- Input slew
- Output load
- Fanout
- Wire resistance
- Wire capacitance
- Interconnect delay
- Clock insertion delay
- Clock skew
- Setup time
- Hold time
- Routing parasitics
- Clock-tree imbalance
- Signal integrity effects

Therefore, timing analysis must continue throughout the physical design flow.

The major relationship studied in this module is:

```text
Standard-Cell Timing
        |
        v
Floorplanning and Placement
        |
        v
Clock Tree Synthesis
        |
        v
Routing and Parasitic Extraction
        |
        v
Static Timing Analysis
        |
        v
Timing Optimization
        |
        v
Timing Closure
```

---

# 1. Timing-Aware ASIC Design

A digital circuit must satisfy two major requirements:

1. Functional correctness
2. Timing correctness

Functional correctness determines whether the circuit performs the intended logical operation.

Timing correctness determines whether the circuit performs that operation within the specified clock period.

A design can therefore be represented as:

```text
                 DIGITAL DESIGN
                       |
              +--------+--------+
              |                 |
       Functional Check    Timing Check
              |                 |
        Logic Correct?      Fast Enough?
              |                 |
              +--------+--------+
                       |
                FINAL DESIGN
```

A design that fails timing may require optimization even when its logic is completely correct.

---

# 2. Where Timing Fits in the ASIC Flow

Timing is not checked only at the end of the design.

It is considered at multiple stages of the ASIC physical design flow.

```text
RTL Design
    |
    v
RTL Simulation
    |
    v
Logic Synthesis
    |
    v
Gate-Level Netlist
    |
    v
Floorplanning
    |
    v
Placement
    |
    v
Clock Tree Synthesis
    |
    v
Routing
    |
    v
Parasitic Extraction
    |
    v
Static Timing Analysis
    |
    v
Timing Optimization
    |
    v
Timing Closure
```

Each stage provides additional physical information.

## Before Physical Implementation

Timing estimation is mainly based on:

- Standard-cell timing models
- Logical connectivity
- Estimated interconnect
- Timing constraints

## After Placement

Timing analysis can additionally consider:

- Actual cell locations
- Estimated wire lengths
- Physical congestion
- Interconnect distance
- Placement-dependent capacitance

## After Routing

Timing analysis considers:

- Actual routed wire lengths
- Wire resistance
- Wire capacitance
- Coupling effects
- Clock-tree delays
- Extracted parasitics

Therefore:

```text
More Physical Information
          |
          v
More Accurate Timing Model
          |
          v
Better Timing Analysis
```

---

# 3. Why Timing Changes After Physical Implementation

A synthesized netlist describes which cells are connected.

It does not completely describe how far apart those cells are physically located.

Consider the following logical representation:

```text
FF1 ----> Logic ----> FF2
```

After placement, the cells may be physically far apart:

```text
+------+                         +------+
| FF1  |                         | FF2  |
+------+                         +------+
    |                               ^
    |                               |
    +-------- Long Interconnect ----+
```

A long interconnect can introduce:

- Greater resistance
- Greater capacitance
- Larger delay
- More signal degradation
- More power consumption

The relationship can be represented as:

```text
Wire Length Increases
        |
        v
Resistance Increases
        |
        v
Capacitance Increases
        |
        v
Interconnect Delay Increases
        |
        v
Timing Margin Decreases
```

Therefore, timing must be rechecked after placement, CTS, and routing.

---

# 4. Standard-Cell Timing Characterization

Standard cells are characterized before they are used in a digital design.

A standard-cell library contains timing information for cells such as:

- Inverters
- Buffers
- NAND gates
- NOR gates
- Multiplexers
- Flip-flops
- Latches
- Clock-gating cells

The library may contain information about:

- Cell propagation delay
- Rise delay
- Fall delay
- Input capacitance
- Output capacitance
- Input transition
- Output transition
- Setup time
- Hold time
- Recovery time
- Removal time
- Leakage power
- Internal power

A standard cell can be represented as:

```text
Input Signal
     |
     v
+-------------+
| Standard    |
|    Cell     |
+-------------+
     |
     v
Output Signal
```

The output delay of a cell is not always constant.

It depends on:

- Input slew
- Output load
- Logic function
- Operating voltage
- Temperature
- Process corner

---

# 5. Input Slew

Input slew represents the transition speed of an input signal.

It describes how quickly a signal changes from logic `0` to logic `1`, or from logic `1` to logic `0`.

## Fast Transition

```text
Voltage
  |
1 |          ________
  |         /
  |        /
0 |_______/
  +----------------------> Time
```

## Slow Transition

```text
Voltage
  |
1 |             ________
  |           /
  |         /
0 |________/
  +----------------------> Time
```

A slow input transition may cause:

- Increased cell delay
- Increased output transition time
- Higher short-circuit power
- Timing degradation in the next cell

The relationship is:

```text
Input Slew Becomes Slower
          |
          v
Cell Delay May Increase
          |
          v
Output Transition Becomes Slower
          |
          v
Next Cell Timing Is Affected
```

---

# 6. Output Load

Output load is the electrical load driven by a cell output.

The load may include:

- Input capacitance of the next cell
- Input capacitance of multiple cells
- Wire capacitance
- Coupling capacitance
- Pin capacitance

For example:

```text
                 +----> Cell A
                 |
Driver Cell -----+----> Cell B
                 |
                 +----> Cell C
```

The driver must charge and discharge all connected loads.

As the load increases:

```text
Output Load Increases
          |
          v
Charging and Discharging Takes Longer
          |
          v
Output Delay Increases
```

High fanout is a common reason for timing degradation.

---

# 7. Fanout

Fanout is the number of loads driven by a particular output.

Example:

```text
              +----> Load 1
              |
              +----> Load 2
Driver -------+
              +----> Load 3
              |
              +----> Load 4
```

The driver has a fanout of four.

High fanout may cause:

- Increased capacitive load
- Slow output transition
- Increased propagation delay
- Increased power consumption
- Clock-tree imbalance

A high-fanout net may require buffering.

```text
High-Fanout Net
       |
       v
Insert Buffers
       |
       v
Divide the Load
       |
       v
Improve Transition and Delay
```

---

# 8. Delay Tables

Standard-cell libraries commonly represent delay using lookup tables.

A simplified timing table can be represented as:

| Input Slew | Low Load | Medium Load | High Load |
|------------|----------|-------------|-----------|
| Low        | D1       | D2          | D3        |
| Medium     | D4       | D5          | D6        |
| High       | D7       | D8          | D9        |

The timing engine uses the input slew and output load to obtain an appropriate delay.

Conceptually:

```text
Input Slew
     +
Output Load
     |
     v
Timing Lookup Table
     |
     v
Cell Delay
```

This means that a cell cannot simply be represented by one fixed delay value.

The delay may change according to:

- Input transition
- Output load
- Process corner
- Supply voltage
- Temperature

---

# 9. Cell Delay vs Interconnect Delay

The total delay of a timing path contains both cell delay and interconnect delay.

A simplified equation is:

```text
Total Delay = Cell Delay + Interconnect Delay
```

For a path containing multiple cells:

```text
Total Path Delay =
Cell Delay 1
+ Wire Delay 1
+ Cell Delay 2
+ Wire Delay 2
+ Cell Delay 3
+ Wire Delay 3
```

Before placement, interconnect delay may be estimated.

After routing, extracted parasitic information provides a more realistic value.

```text
Cell Delay
     |
     +------------------+
     |                  |
     v                  v
Input Slew          Output Load
     |                  |
     +--------+---------+
              |
              v
          Cell Delay
```

The total path delay is affected by both logical cells and physical wires.

---

# 10. Interconnect Parasitics

Physical wires have electrical properties.

The main parasitic parameters are:

- Resistance
- Capacitance
- Coupling capacitance

## Resistance

Resistance opposes current flow through a wire.

## Capacitance

Capacitance represents the ability of a wire to store electrical charge.

## Coupling Capacitance

Coupling capacitance exists between nearby wires and may cause unwanted interaction.

A simplified wire model is:

```text
Driver ---- R ---- R ---- R ---- Receiver
             |      |      |
             C      C      C
             |      |      |
            GND    GND    GND
```

Longer wires generally produce larger parasitic effects.

```text
Longer Wire
     |
     v
Higher Parasitic R and C
     |
     v
Greater Interconnect Delay
```

---

# 11. Transition Degradation

A signal may become slower while travelling through logic cells and interconnects.

```text
Input Signal
     |
     v
Logic Cell
     |
     v
Interconnect
     |
     v
Large Load
     |
     v
Slower Output Transition
```

A slow transition at the output of one cell becomes the input transition of the next cell.

```text
Slow Input Transition
          |
          v
Increased Cell Delay
          |
          v
Slow Output Transition
          |
          v
Next Cell Delay Changes
```

Transition degradation may affect the complete timing path.

---

# 12. Sequential Timing Model

Sequential circuits store data using flip-flops or latches.

A common synchronous path is:

```text
Launch Flip-Flop
       |
       v
Combinational Logic
       |
       v
Capture Flip-Flop
```

The launch flip-flop sends data into the combinational logic.

The capture flip-flop receives the data at a later clock edge.

```text
                Data Path
       +--------------------------+
       |                          |
       v                          v
    +------+   Logic Cells     +------+
    | FF1  | -----------------> | FF2  |
    +------+                    +------+
       ^                           ^
       |                           |
       +----------- Clock ---------+
```

The data must satisfy:

- Setup timing
- Hold timing

---

# 13. Clock-to-Q Delay

Clock-to-Q delay is the time required for a flip-flop output to change after the active clock edge.

```text
Clock
______/‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾

Q
__________/‾‾‾‾‾‾‾‾‾‾‾‾‾
          ^
          |
      Clock-to-Q Delay
```

A simplified register-to-register path includes:

```text
Clock-to-Q Delay
       +
Combinational Logic Delay
       +
Interconnect Delay
       +
Capture Register Setup Time
```

Clock-to-Q delay is commonly represented as `Tcq`.

---

# 14. Setup Timing

Setup time is the minimum amount of time for which data must remain stable before the active clock edge of the receiving flip-flop.

```text
Data
------------------- stable --------------------
                                  |
                                  |
Clock                             v
___________________________/‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾
                            ^
                            |
                       Capture Edge
```

The data must arrive sufficiently early before the capture edge.

A simplified setup condition is:

```text
Tclk >= Tcq + Tcomb + Twire + Tsetup + Tuncertainty
```

Where:

- `Tclk` = clock period
- `Tcq` = clock-to-Q delay
- `Tcomb` = combinational logic delay
- `Twire` = interconnect delay
- `Tsetup` = setup time
- `Tuncertainty` = timing margin or clock uncertainty

If data arrives too late:

```text
Data Arrival Time > Required Arrival Time
```

then a setup violation occurs.

---

# 15. Setup Path Components

A typical setup timing path contains:

```text
Launch FF
   |
   | Clock-to-Q
   v
Combinational Logic
   |
   | Cell Delay + Wire Delay
   v
Capture FF
   |
   +--> Setup Requirement
```

The total data path delay may contain:

```text
Tcq
 +
Logic Delay
 +
Interconnect Delay
 +
Setup Time
```

The timing engine evaluates all of these components together.

---

# 16. Hold Timing

Hold time is the minimum time for which data must remain stable after the active clock edge of the receiving flip-flop.

```text
Clock
____________________/‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾
                    ^
                    |
                Capture Edge

Data
-------------------- stable -------------------
                    |
                    +---- Hold Window
```

If new data reaches the receiving flip-flop too early, the old data may not be held long enough.

A simplified hold condition is:

```text
Tcq + Tcomb + Twire + Tskew >= Thold
```

The exact expression depends on the clock arrival relationship and the sign convention used for skew.

---

# 17. Setup and Hold Failure Mechanisms

## Setup Violation

A setup violation occurs when data arrives too late.

```text
Data Arrives Too Late
          |
          v
Setup Window Is Missed
          |
          v
Setup Violation
```

## Hold Violation

A hold violation occurs when data changes too early after the capture edge.

```text
Data Changes Too Early
          |
          v
Hold Window Is Violated
          |
          v
Hold Violation
```

The difference can be summarized as:

```text
Setup Failure = Late Data

Hold Failure  = Early Data
```

---

# 18. Timing Window Visualization

The receiving flip-flop has a timing window around the active clock edge.

```text
                         Capture Edge
                              |
                              v
Data       -------------------|-------------------
                              |
                       Required Stability
                       Before and After Edge
```

The data must satisfy both conditions:

```text
Before Capture Edge
        |
        v
Setup Requirement

After Capture Edge
        |
        v
Hold Requirement
```

---

# 19. Ideal Clock Analysis

Before Clock Tree Synthesis, the clock may be treated as ideal.

An ideal clock assumes that the clock reaches all sequential elements at approximately the same time.

```text
                    Ideal Clock
                         |
             +-----------+-----------+
             |           |           |
             v           v           v
            FF1         FF2         FF3
```

In this model:

```text
Clock Arrival at FF1 ≈ Clock Arrival at FF2
Clock Arrival at FF2 ≈ Clock Arrival at FF3
```

Ideal-clock analysis is useful during early design stages.

However, it does not represent the actual physical clock network.

---

# 20. Real Clock Network

After physical implementation, the clock travels through actual wires and buffers.

```text
                     Clock Source
                          |
                          v
                     Clock Buffer
                          |
             +------------+------------+
             |            |            |
             v            v            v
          Buffer       Buffer       Buffer
             |            |            |
             v            v            v
            FF1          FF2          FF3
```

Each branch may have a different:

- Wire length
- Number of buffers
- Load
- Resistance
- Capacitance
- Insertion delay

Therefore:

```text
Clock Arrival at FF1
       may differ from
Clock Arrival at FF2
```

This difference is known as clock skew.

---

# 21. Clock Tree Synthesis

Clock Tree Synthesis, or CTS, creates a physical clock distribution network.

The clock network distributes the clock from the source to all required sequential elements.

```text
Clock Source
     |
     v
Clock Root
     |
     v
Clock Tree Construction
     |
     +----------+----------+
     |          |          |
     v          v          v
  Buffer     Buffer     Buffer
     |          |          |
     v          v          v
    FF1        FF2        FF3
```

CTS attempts to build a clock network that satisfies timing and electrical constraints.

The main objectives of CTS include:

- Controlling clock skew
- Controlling clock latency
- Managing fanout
- Maintaining acceptable transition
- Reducing clock imbalance
- Supporting reliable routing
- Controlling clock power
- Avoiding excessive buffering

---

# 22. Objectives of Clock Tree Synthesis

## 22.1 Clock Skew Control

CTS attempts to reduce the difference in clock arrival times at sequential endpoints.

```text
Clock Arrival FF1 = 1.20 ns
Clock Arrival FF2 = 1.25 ns

Skew = 0.05 ns
```

## 22.2 Clock Transition Control

The clock signal must have an acceptable rise and fall transition.

A slow clock transition can cause:

- Timing uncertainty
- Increased power
- Clock waveform distortion
- Possible library constraint violations

## 22.3 Fanout Control

The clock source should not directly drive an excessive number of loads.

## 22.4 Clock Latency Control

CTS attempts to control the time required for the clock to reach its endpoints.

## 22.5 Power Control

The clock network switches frequently and may consume significant dynamic power.

## 22.6 Physical Feasibility

The clock tree must be physically routable and must satisfy design-rule constraints.

---

# 23. Clock Tree Topologies

Different clock-tree structures may be used during physical implementation.

Examples include:

- Balanced tree
- H-tree
- Mesh-based clock network
- Clock spine
- Multi-level buffered tree

The selected topology depends on:

- Design size
- Clock frequency
- Physical dimensions
- Skew requirements
- Power limits
- Routing resources

---

# 24. H-Tree Clock Distribution

An H-tree is a clock distribution structure designed to create approximately balanced physical paths.

A simplified H-tree can be represented as:

```text
                         Clock Source
                              |
                              |
                    +---------+---------+
                    |                   |
                    |                   |
                    +---------+---------+
                              |
                    +---------+---------+
                    |                   |
                    |                   |
                   FF1                 FF2
```

The structure divides the clock path into branches with similar physical lengths.

The purpose is to reduce clock arrival-time differences.

```text
Balanced Physical Paths
          |
          v
Similar Clock Latencies
          |
          v
Reduced Clock Skew
```

In practical designs, buffers and routing resources are added to satisfy electrical and physical requirements.

---

# 25. Clock Buffering

Clock buffers are used to drive large clock loads.

Without buffering:

```text
Clock Source
     |
     +----> FF1
     +----> FF2
     +----> FF3
     +----> FF4
     +----> FF5
     +----> FF6
```

The source may experience excessive load.

With buffering:

```text
                  Clock Source
                       |
                       v
                    Buffer
                       |
             +---------+---------+
             |         |         |
             v         v         v
            B1        B2        B3
             |         |         |
             v         v         v
           FF1       FF2       FF3
```

Buffering helps to:

- Divide the load
- Improve transition
- Control fanout
- Reduce delay
- Improve signal integrity

However, too many buffers may increase:

- Area
- Power
- Clock latency
- Routing complexity

---

# 26. Clock Insertion Delay

Clock insertion delay is the time required for the clock signal to travel from the clock source to a sequential endpoint.

```text
Clock Source --------------------> FF1
       |
       +---------- Clock Path ----------+
                                        |
                                        v
                                  Clock Arrival
```

For example:

```text
Clock Source to FF1 = 1.2 ns
Clock Source to FF2 = 1.4 ns
```

The insertion delays are different.

This difference contributes to clock skew.

---

# 27. Clock Skew

Clock skew is the difference between the arrival times of the clock at two sequential elements.

A simplified expression is:

```text
Clock Skew =
Clock Arrival Time at Capture Register
-
Clock Arrival Time at Launch Register
```

For example:

```text
Clock arrival at launch register  = 1.2 ns
Clock arrival at capture register = 1.5 ns

Clock skew = 1.5 - 1.2
           = 0.3 ns
```

The sign of skew depends on the selected launch and capture endpoints.

---

# 28. Positive and Negative Clock Skew

## Positive Skew

Positive skew occurs when the capture clock arrives later than the launch clock.

```text
Launch Clock Arrival  = 1.0 ns
Capture Clock Arrival = 1.2 ns
```

Positive skew may provide more time for setup, but it can reduce hold margin.

## Negative Skew

Negative skew occurs when the capture clock arrives earlier than the launch clock.

```text
Launch Clock Arrival  = 1.2 ns
Capture Clock Arrival = 1.0 ns
```

Negative skew may reduce setup time, but it may improve hold margin.

The effect depends on the timing path and analysis convention.

---

# 29. Why Clock Skew Matters

Clock skew affects both setup and hold timing.

```text
                 Clock Skew
                     |
             +-------+-------+
             |               |
             v               v
        Setup Timing     Hold Timing
             |               |
             v               v
       Available Data    Minimum Data
          Time             Delay
```

Clock skew must therefore be considered together with the data path.

A clock network with poor balance can cause:

- Setup violations
- Hold violations
- Unequal timing margins
- Increased clock uncertainty
- Difficult timing closure

---

# 30. Clock Latency vs Clock Skew

Clock latency and clock skew are different concepts.

## Clock Latency

Clock latency is the delay from the clock source to a particular endpoint.

```text
Clock Source ---------> Flip-Flop
       |
       +---- Clock Latency
```

## Clock Skew

Clock skew is the difference between the clock arrival times at two endpoints.

```text
Clock Source
     |
     +----> FF1 : 1.0 ns
     |
     +----> FF2 : 1.3 ns

Clock Skew = 1.3 - 1.0
           = 0.3 ns
```

Therefore:

```text
Clock Latency = Absolute Arrival Delay

Clock Skew = Difference Between Arrival Delays
```

---

# 31. Clock Net Shielding

Clock nets are sensitive because they switch frequently and connect many sequential elements.

Nearby signal wires may create coupling effects.

A simplified arrangement is:

```text
Signal Wire
--------------------------------

Ground Shield
================================

Clock Wire
********************************

Ground Shield
================================

Signal Wire
--------------------------------
```

Shielding can reduce unwanted coupling between the clock net and neighboring signal wires.

Potential benefits include:

- Reduced coupling noise
- Improved signal integrity
- Better waveform quality
- More predictable clock delay
- Reduced cross-talk effects

Shielding may require additional routing resources.

---

# 32. Clock Tree Power

The clock network may consume significant dynamic power because it switches every clock cycle.

Dynamic power can be expressed conceptually as:

```text
Pdynamic ∝ C × V² × f × α
```

Where:

- `C` = switched capacitance
- `V` = supply voltage
- `f` = switching frequency
- `α` = switching activity

The clock network has high switching activity.

Power may increase due to:

- Excessive clock capacitance
- Large clock buffers
- Long clock wires
- High switching frequency
- Excessive clock-tree depth

Therefore, CTS must consider timing and power together.

---

# 33. Physical Placement and Timing

Placement determines where standard cells are physically located.

Consider two different placements.

## Short Interconnect

```text
+------+      +------+      +------+
| FF1  | ---> | Logic| ---> | FF2  |
+------+      +------+      +------+
```

## Long Interconnect

```text
+------+
| FF1  |
+------+
    |
    |
    |------------------------------------------|
                                               |
                                               v
                                           +------+
                                           | FF2  |
                                           +------+
```

The second case generally introduces larger interconnect effects.

Therefore:

```text
Placement
    |
    v
Wire Length
    |
    v
Parasitics
    |
    v
Interconnect Delay
    |
    v
Timing
```

Placement and timing are therefore strongly connected.

---

# 34. Placement and Congestion

Placement must also consider routing congestion.

A region containing too many cells may become difficult to route.

```text
High Cell Density
       |
       v
Routing Congestion
       |
       v
Longer / Difficult Routing
       |
       v
Higher Parasitics
       |
       v
Timing Degradation
```

Therefore, placement optimization may consider:

- Timing
- Area
- Congestion
- Power
- Wire length

---

# 35. Physical Implementation Feedback Loop

Physical design is iterative.

Timing results can cause changes in:

- Placement
- Cell sizing
- Buffer insertion
- Routing
- Clock tree structure
- Logic optimization

A simplified loop is:

```text
Placement
    |
    v
Clock Tree Synthesis
    |
    v
Routing
    |
    v
Parasitic Extraction
    |
    v
Static Timing Analysis
    |
    v
Timing Violations?
    |
    +---------- No ----------> Continue
    |
    |
   Yes
    |
    v
Timing Optimization
    |
    v
Re-run Physical Implementation
    |
    +--------------------------+
                               |
                               v
                         Static Timing
                            Analysis
```

This iterative process continues until timing requirements are satisfied or the design reaches an acceptable implementation state.

---

# 36. Timing Path Classification

Timing paths are commonly classified into four major categories.

## Register-to-Register

```text
FF --> Combinational Logic --> FF
```

## Input-to-Register

```text
Input --> Logic --> FF
```

## Register-to-Output

```text
FF --> Logic --> Output
```

## Input-to-Output

```text
Input --> Logic --> Output
```

For synchronous digital designs, register-to-register paths are particularly important.

---

# 37. Register-to-Register Timing Path

A typical register-to-register timing path is:

```text
Launch Register
      |
      | Clock-to-Q
      v
Combinational Logic
      |
      | Cell Delay
      |
      | Interconnect Delay
      v
Capture Register
```

The timing engine determines whether data can travel through the entire path within the available timing window.

The path can be divided into:

```text
Launch Clock
     |
     v
Launch Register
     |
     v
Clock-to-Q Delay
     |
     v
Combinational Logic
     |
     v
Interconnect
     |
     v
Capture Register
```

---

# 38. Data Arrival Time

Data Arrival Time, commonly abbreviated as DAT, represents when data reaches the endpoint of a timing path.

A simplified expression is:

```text
Data Arrival Time =
Launch Clock Arrival
+
Clock-to-Q Delay
+
Combinational Delay
+
Interconnect Delay
```

For example:

```text
Launch clock arrival = 0.5 ns
Clock-to-Q delay     = 0.1 ns
Logic delay          = 0.7 ns
Wire delay           = 0.2 ns
```

Therefore:

```text
DAT = 0.5 + 0.1 + 0.7 + 0.2

DAT = 1.5 ns
```

Actual STA calculations depend on the clock definitions, constraints, library data, and analysis corner.

---

# 39. Data Required Time

Data Required Time represents the latest acceptable arrival time of data at the capture endpoint.

A simplified setup relationship is:

```text
Required Time =
Capture Clock Arrival
-
Setup Time
-
Timing Margins
```

For example:

```text
Capture clock arrival = 2.0 ns
Setup time            = 0.2 ns
```

Then:

```text
Required Time = 2.0 - 0.2

Required Time = 1.8 ns
```

If the actual data arrival time is:

```text
Arrival Time = 1.5 ns
Required Time = 1.8 ns
```

then the data arrives before the deadline.

---

# 40. Slack

Slack represents the timing margin between when data actually arrives and when it is required to arrive.

For setup timing:

```text
Setup Slack =
Required Arrival Time
-
Actual Arrival Time
```

Example:

```text
Required Time = 1.8 ns
Arrival Time  = 1.5 ns

Slack = 1.8 - 1.5
      = +0.3 ns
```

Positive slack indicates timing margin.

```text
Positive Slack
      |
      v
Timing Requirement Satisfied
```

Negative slack indicates a timing violation.

```text
Negative Slack
      |
      v
Timing Requirement Violated
```

---

# 41. Slack Visualization

```text
Required Arrival
      |
      v
      |---------------------------|
      |                           |
      |<------ Timing Margin ---->|
      |                           |
Arrival                         Deadline
```

For setup:

```text
Positive Slack
      |
      v
Data Arrives Before Deadline
```

For hold:

```text
Positive Hold Slack
      |
      v
Data Remains Stable Long Enough
```

---

# 42. Negative Slack

Negative slack indicates that the timing requirement has not been satisfied.

For example:

```text
Required Time = 1.5 ns
Arrival Time  = 1.8 ns
```

Therefore:

```text
Slack = 1.5 - 1.8
      = -0.3 ns
```

The negative value indicates a setup violation of `0.3 ns`.

```text
Negative Slack
      |
      v
Timing Violation
      |
      v
Optimization Required
```

---

# 43. Pre-CTS Timing

Before CTS, the clock may be treated as ideal.

```text
Ideal Clock
     |
     +----------> Launch FF
     |
     +----------> Capture FF
```

The analysis focuses mainly on:

- Data-path delay
- Cell delay
- Estimated interconnect
- Setup and hold constraints
- Clock period
- Input/output constraints

However, actual clock-tree effects are not yet fully represented.

---

# 44. Post-CTS Timing

After CTS, the clock network becomes physically meaningful.

```text
Clock Source
     |
     v
Clock Tree
     |
     +--------> Launch FF
     |
     +--------> Capture FF
```

Now timing analysis considers:

- Clock latency
- Clock skew
- Clock transition
- Clock buffers
- Clock wire delays
- Clock parasitics
- Data-path delay
- Setup timing
- Hold timing

Therefore, timing can change significantly after CTS.

---

# 45. What Changes After CTS?

Before CTS:

```text
Clock
  |
  +---- FF1
  |
  +---- FF2
  |
  +---- FF3
```

After CTS:

```text
Clock Source
      |
      v
    Buffer
      |
   +--+--+
   |     |
   v     v
  B1     B2
  |       |
  v       v
 FF1     FF2
```

The clock now has:

- Physical buffers
- Physical wires
- Different path lengths
- Different loads
- Actual insertion delays

Therefore:

```text
Ideal Clock
    |
    v
CTS
    |
    v
Real Clock
    |
    v
Clock Skew + Clock Latency
    |
    v
Post-CTS Timing
```

---

# 46. Post-CTS Timing Effects

CTS can change both setup and hold timing.

For setup:

```text
Clock Arrival Difference
          |
          v
Available Data Time Changes
          |
          v
Setup Slack Changes
```

For hold:

```text
Clock Arrival Difference
          |
          v
Minimum Data Delay Requirement Changes
          |
          v
Hold Slack Changes
```

Therefore, timing must be checked again after CTS.

---

# 47. Setup Optimization

If setup timing fails, the data path is effectively too slow.

Possible optimization methods include:

- Cell upsizing
- Buffer insertion
- Logic restructuring
- Reducing logic depth
- Improving placement
- Reducing wire length
- Improving routing
- Reducing fanout
- Optimizing clock skew

A simplified setup optimization flow is:

```text
Setup Violation
      |
      v
Identify Critical Path
      |
      v
Find Major Delay Contributor
      |
      +-------------------+
      |                   |
      v                   v
Cell Delay           Interconnect Delay
      |                   |
      v                   v
Cell Sizing          Placement/Routing
      |                   |
      +---------+---------+
                |
                v
             Re-run STA
```

---

# 48. Hold Optimization

Hold violations occur when data arrives too early.

Possible solutions include:

- Adding delay cells
- Adding buffers
- Increasing data-path delay
- Adjusting clock-tree characteristics
- Routing adjustments

A simplified flow is:

```text
Hold Violation
      |
      v
Identify Short Data Path
      |
      v
Add Controlled Delay
      |
      v
Recalculate Timing
      |
      v
Check Setup Timing Again
```

Hold optimization must be performed carefully because increasing data-path delay can affect setup timing.

---

# 49. Setup vs Hold Optimization

| Timing Problem | Main Issue | Typical Direction |
|---|---|---|
| Setup Violation | Data arrives too late | Speed up data path |
| Hold Violation | Data arrives too early | Add delay to data path |

The two timing problems require different optimization strategies.

```text
                 Timing Violation
                       |
              +--------+--------+
              |                 |
              v                 v
            Setup             Hold
              |                 |
              v                 v
        Data Too Slow      Data Too Fast
              |                 |
              v                 v
       Reduce Delay        Add Delay
```

---

# 50. Clock Glitch Analysis

Clock signals are especially sensitive to glitches because sequential elements respond to clock transitions.

A simplified clean clock is:

```text
Clock
____/‾‾‾‾\____/‾‾‾‾\____
```

An unwanted glitch may appear as:

```text
Clock
____/‾‾\_/‾‾‾\____/‾‾‾‾
```

A clock glitch can cause an unintended sequential event.

Potential causes include:

- Incorrect clock-gating implementation
- Combinational logic on clock paths
- Poor signal integrity
- Crosstalk
- Incorrect clock-tree design

Therefore, clock networks are generally treated as special timing structures.

---

# 51. Static Timing Analysis

Static Timing Analysis, or STA, is a method used to analyze timing behavior without applying exhaustive input test vectors.

Instead of simulating every possible functional input sequence, STA analyzes timing paths mathematically using:

- Netlist
- Standard-cell timing models
- Clock definitions
- Timing constraints
- Physical delays
- Parasitic information

A simplified flow is:

```text
Netlist
   +
Timing Libraries
   +
Constraints
   +
Parasitics
   |
   v
Static Timing Analysis
   |
   v
Timing Reports
   |
   +----> Setup
   |
   +----> Hold
   |
   +----> Slew
   |
   +----> Clock
```

---

# 52. Why STA Is Important

STA can analyze a large number of timing paths efficiently.

It identifies:

- Critical paths
- Setup violations
- Hold violations
- Slew violations
- Clock problems
- Timing margins
- Worst timing paths

The major benefit is that timing can be evaluated systematically without requiring functional simulation for every possible input combination.

---

# 53. Timing Graph

STA can conceptually represent a digital design as a timing graph.

```text
      Node
       |
       v
+--------------+
| Timing Point |
+--------------+
       |
       v
    Timing Arc
       |
       v
+--------------+
| Next Timing  |
|    Point     |
+--------------+
```

A simplified register path becomes:

```text
FF1/Q
  |
  v
Cell A
  |
  v
Cell B
  |
  v
Cell C
  |
  v
FF2/D
```

STA traverses such paths and calculates timing quantities.

---

# 54. Timing Constraints

STA requires timing constraints to determine what the circuit is expected to do.

Important constraints include:

- Clock period
- Clock waveform
- Input delay
- Output delay
- Clock uncertainty
- Input transition
- Output load
- False paths
- Multicycle paths

A simplified timing setup is:

```text
Timing Constraints
        |
        +---- Clock
        |
        +---- Input Delay
        |
        +---- Output Delay
        |
        +---- Uncertainty
        |
        +---- Exceptions
        |
        v
       STA
```

Without correct constraints, timing reports may not represent the intended design requirements.

---

# 55. Clock Period and Maximum Frequency

Clock frequency and clock period are related by:

```text
Fmax = 1 / Tclk
```

or:

```text
Tclk = 1 / Fclk
```

For example, if:

```text
Tclk = 10 ns
```

then:

```text
Fclk = 1 / 10 ns
```

which corresponds to approximately:

```text
Fclk = 100 MHz
```

The actual maximum achievable frequency depends on the complete timing path and constraints.

---

# 56. Timing Budget

The clock period provides a limited timing budget.

For a simplified register-to-register path:

```text
Clock Period
+------------------------------------------------+
|                                                |
| Tcq | Logic Delay | Wire Delay | Setup Margin |
|                                                |
+------------------------------------------------+
```

The total delay must fit inside the available timing budget.

For example:

```text
Clock Period = 10 ns

Tcq           = 1 ns
Logic Delay   = 5 ns
Wire Delay    = 2 ns
Setup         = 1 ns
Margin        = 1 ns
```

Total:

```text
1 + 5 + 2 + 1 + 1 = 10 ns
```

The timing budget is fully consumed.

---

# 57. Critical Path

The critical path is a timing path with the most restrictive timing margin for the analysis being considered.

A simplified example:

```text
Path 1:
Delay = 4 ns

Path 2:
Delay = 6 ns

Path 3:
Delay = 8 ns
```

If the clock period is limited, Path 3 may be the most timing-sensitive path.

Conceptually:

```text
Many Timing Paths
       |
       v
Timing Analysis
       |
       v
Critical Timing Paths
       |
       v
Optimization
```

Critical paths are important because improving them can improve overall timing closure.

---

# 58. Worst Negative Slack

Worst Negative Slack, or WNS, represents the worst timing slack observed for a particular timing check.

For setup timing:

```text
WNS = Minimum Setup Slack
```

Example:

```text
Path 1 = +0.5 ns
Path 2 = +0.2 ns
Path 3 = -0.3 ns
Path 4 = +0.1 ns
```

Then:

```text
WNS = -0.3 ns
```

A negative WNS indicates that at least one analyzed path violates the timing requirement.

---

# 59. Total Negative Slack

Total Negative Slack, or TNS, represents the combined negative slack of violating paths.

Conceptually:

```text
Path 1 = +0.2 ns
Path 2 = -0.1 ns
Path 3 = -0.3 ns
Path 4 = -0.2 ns
```

Then the total negative contribution is:

```text
TNS = -0.1 - 0.3 - 0.2

TNS = -0.6 ns
```

TNS provides information about the overall amount of negative timing margin across violating paths.

---

# 60. WNS vs TNS

WNS and TNS provide different information.

| Metric | Meaning |
|---|---|
| WNS | Worst individual timing violation |
| TNS | Total negative timing contribution across violating paths |

Example:

```text
Path A = -0.5 ns
Path B = -0.2 ns
Path C = -0.1 ns
```

Then:

```text
WNS = -0.5 ns

TNS = -0.8 ns
```

Therefore:

```text
WNS -> How bad is the worst path?

TNS -> How widespread is the timing problem?
```

Both are useful during timing closure.

---

# 61. Timing Violation Debugging

When a timing violation occurs, the first step is to identify the affected path.

A simplified debugging flow is:

```text
Timing Violation
       |
       v
Identify Endpoint
       |
       v
Identify Launch Point
       |
       v
Trace Timing Path
       |
       v
Find Major Delay Contributor
       |
       +--------------------+
       |                    |
       v                    v
Cell Delay            Wire Delay
       |                    |
       v                    v
Cell Optimization      Physical Optimization
       |                    |
       +---------+----------+
                 |
                 v
             Re-run STA
```

Possible causes include:

- Large combinational logic depth
- Slow standard cells
- High fanout
- Long interconnect
- Poor placement
- High capacitance
- Clock skew
- Excessive transition
- Routing congestion

---

# 62. Example of a Long Timing Path

Consider:

```text
FF1
 |
 v
BUF
 |
 v
NAND
 |
 v
NOR
 |
 v
MUX
 |
 v
BUF
 |
 v
FF2
```

The path contains multiple cells.

The total delay may be:

```text
Tcq
+
BUF Delay
+
NAND Delay
+
NOR Delay
+
MUX Delay
+
BUF Delay
+
Wire Delays
+
Setup Time
```

If the path is too slow:

```text
Long Timing Path
       |
       v
Large Data Delay
       |
       v
Negative Setup Slack
```

Optimization may involve reducing logic depth, resizing cells, buffering, or improving placement and routing.

---

# 63. High-Fanout Timing Problem

Consider:

```text
                +----> FF1
                |
                +----> FF2
                |
Driver ---------+----> FF3
                |
                +----> FF4
                |
                +----> FF5
```

The driver sees a large load.

Possible optimization:

```text
                  Driver
                    |
             +------+------+
             |             |
             v             v
            B1             B2
          / | \           / | \
         /  |  \         /  |  \
       FF1 FF2 FF3     FF4 FF5 FF6
```

This reduces the load seen by each driver stage.

---

# 64. Buffer Insertion

Buffer insertion can improve timing by controlling transition and fanout.

Before buffering:

```text
Driver
  |
  +---- Load 1
  +---- Load 2
  +---- Load 3
  +---- Load 4
  +---- Load 5
  +---- Load 6
```

After buffering:

```text
              Driver
                |
          +-----+-----+
          |           |
          v           v
         B1           B2
       / | \         / | \
      v  v  v       v  v  v
     L1 L2 L3      L4 L5 L6
```

Benefits can include:

- Reduced effective fanout
- Improved transition
- Better signal quality
- Reduced delay

However, buffer insertion increases:

- Cell count
- Area
- Power
- Routing demand

Therefore, it is a trade-off.

---

# 65. Cell Sizing

Cell sizing means selecting a stronger or weaker drive-strength version of a standard cell.

For example:

```text
Small Driver
     |
     v
Large Load
     |
     v
Slow Transition
```

Replacing it with a stronger driver:

```text
Large Driver
     |
     v
Large Load
     |
     v
Improved Transition
```

Cell sizing can improve timing.

However, stronger cells may consume:

- More area
- More dynamic power
- More leakage power

Therefore:

```text
Timing Improvement
        |
        v
Power and Area Cost
```

must be considered.

---

# 66. Timing vs Power vs Area

Physical design is a multi-objective optimization problem.

Improving timing may increase:

- Area
- Power
- Cell count
- Routing demand

A simplified relationship is:

```text
                 Timing
                    ^
                    |
                    |
          +---------+---------+
          |                   |
          |                   |
        Area  <------------> Power
```

Examples:

### Upsizing a Cell

```text
Drive Strength ↑
       |
       v
Delay ↓
       |
       +----> Area ↑
       |
       +----> Power ↑
```

### Adding Buffers

```text
Fanout Per Stage ↓
       |
       v
Transition Improves
       |
       v
Timing May Improve
       |
       +----> Area ↑
       |
       +----> Power ↑
```

Timing optimization must therefore be performed carefully.

---

# 67. Routing and Parasitics

Routing converts logical connections into physical wires.

A simplified routing flow is:

```text
Placed Cells
     |
     v
Global Routing
     |
     v
Detailed Routing
     |
     v
Physical Wires
     |
     v
Parasitic Extraction
     |
     v
STA
```

The extracted parasitics may include:

- Resistance
- Ground capacitance
- Coupling capacitance

These values are used to obtain more accurate timing results.

---

# 68. Parasitic Extraction

Parasitic extraction determines electrical properties of physical interconnects.

A simplified model is:

```text
Physical Layout
       |
       v
Extract Wires
       |
       v
Calculate R and C
       |
       v
Parasitic Model
       |
       v
Static Timing Analysis
```

The extracted parasitic values improve the accuracy of post-route timing analysis.

---

# 69. Pre-Route vs Post-Route Timing

## Pre-Route

Interconnect is estimated.

```text
Netlist
   |
   v
Estimated Wires
   |
   v
Estimated Timing
```

## Post-Route

Actual routed geometry is available.

```text
Routed Design
     |
     v
Parasitic Extraction
     |
     v
Actual R and C
     |
     v
More Accurate Timing
```

Therefore:

```text
Pre-Route Timing
       |
       v
Estimated Physical Effects

Post-Route Timing
       |
       v
Extracted Physical Effects
```

---

# 70. Post-CTS Timing Analysis

After CTS, timing analysis includes the physical clock network.

The flow becomes:

```text
Placed Design
     |
     v
Clock Tree Synthesis
     |
     v
Clock Buffers + Clock Wires
     |
     v
Clock Latency and Skew
     |
     v
Timing Analysis
     |
     v
Setup / Hold Evaluation
```

Post-CTS STA is important because the clock is no longer ideal.

---

# 71. Setup Analysis After CTS

For setup analysis, the clock relationship between launch and capture registers affects the available time.

A simplified conceptual equation is:

```text
Setup Slack =
Available Capture Time
-
Data Arrival Time
```

The available capture time depends on:

- Clock period
- Launch clock latency
- Capture clock latency
- Clock skew
- Setup time
- Clock uncertainty

Therefore:

```text
Clock Tree
    |
    v
Clock Arrival
    |
    v
Clock Skew
    |
    v
Setup Timing
```

---

# 72. Hold Analysis After CTS

Hold analysis is especially sensitive to clock skew.

A simplified conceptual relationship is:

```text
Hold Slack =
Actual Minimum Data Delay
-
Required Minimum Delay
```

The requirement depends on:

- Launch clock arrival
- Capture clock arrival
- Flip-flop hold time
- Clock uncertainty
- Minimum data-path delay

Therefore:

```text
CTS
 |
 v
Clock Skew
 |
 v
Hold Requirement Changes
 |
 v
Hold STA
```

---

# 73. Clock Skew and Timing Trade-Off

Clock skew can affect setup and hold in opposite ways.

```text
             Clock Skew
                  |
        +---------+---------+
        |                   |
        v                   v
     Setup                 Hold
        |                   |
        v                   v
 More Capture Time      Less Margin
        |                   |
        v                   v
 Potential Benefit      Potential Risk
```

Therefore, CTS optimization must consider both setup and hold timing rather than optimizing only one metric.

---

# 74. Timing Exceptions

Not every logically connected path must necessarily be analyzed as a normal single-cycle timing path.

Timing constraints may define exceptions such as:

- False paths
- Multicycle paths
- Clock groups
- Disabled timing arcs

## False Path

A false path is a path that is not expected to be functionally active for the timing scenario being analyzed.

```text
Logic Path
    |
    v
False Path Constraint
    |
    v
Excluded from Normal Timing Analysis
```

## Multicycle Path

A multicycle path is allowed more than one clock cycle for data propagation.

```text
Launch
  |
  |------ Cycle 1 ------|
  |------ Cycle 2 ------|
                         |
                         v
                      Capture
```

Timing exceptions must represent the actual design behavior.

Incorrect constraints can produce misleading timing reports.

---

# 75. Clock Uncertainty

Clock uncertainty represents timing margin associated with clock behavior and modeling effects.

It may account for factors such as:

- Clock jitter
- Variation
- Modeling margin
- Other clock-related uncertainty

A simplified representation is:

```text
Ideal Clock Period
        |
        v
Subtract Clock Uncertainty
        |
        v
Available Timing Window
```

For example:

```text
Clock Period = 10 ns
Uncertainty = 0.2 ns
```

The effective timing budget is reduced by the uncertainty according to the analysis model.

---

# 76. Timing Variation

Manufacturing and operating conditions can cause timing variation.

Important factors include:

- Process
- Voltage
- Temperature

These are commonly represented through PVT conditions.

```text
                 PVT
                  |
        +---------+---------+
        |         |         |
        v         v         v
     Process   Voltage  Temperature
        |         |         |
        +---------+---------+
                  |
                  v
             Cell Timing
```

Different conditions can produce different timing results.

---

# 77. Timing Corners

A design may be analyzed under different library and operating conditions.

Conceptually:

```text
Timing Analysis
      |
      +----> Fast Condition
      |
      +----> Typical Condition
      |
      +----> Slow Condition
```

Different timing checks may be sensitive to different conditions.

The objective is to verify that the design satisfies its timing requirements across the relevant analysis scenarios.

---

# 78. Critical Path Identification

Critical path identification is an important part of timing closure.

A simplified process is:

```text
All Timing Paths
       |
       v
Calculate Slack
       |
       v
Sort / Identify Critical Paths
       |
       v
Analyze Worst Paths
       |
       v
Optimize
```

The critical path may be dominated by:

- Logic delay
- Interconnect delay
- Fanout
- Cell transition
- Clock skew
- Routing congestion

---

# 79. Timing Report Interpretation

A timing report generally contains information such as:

- Startpoint
- Endpoint
- Clock information
- Data path
- Cell delays
- Net delays
- Arrival time
- Required time
- Slack

A simplified report may look conceptually like:

```text
Startpoint: FF1/Q
Endpoint:   FF2/D

Clock:
  launch clock
  capture clock

Data Path:
  FF1/Q
    |
    +-- BUF
    |
    +-- NAND
    |
    +-- MUX
    |
    +-- FF2/D

Arrival Time  = 8.2 ns
Required Time = 8.5 ns

Slack = +0.3 ns
```

The actual format depends on the STA tool.

---

# 80. Conceptual Timing Report

A simplified timing table is:

| Timing Parameter | Example |
|---|---:|
| Startpoint | FF1/Q |
| Endpoint | FF2/D |
| Clock Period | 10 ns |
| Clock-to-Q | 0.8 ns |
| Logic Delay | 4.5 ns |
| Wire Delay | 1.2 ns |
| Setup Time | 0.5 ns |
| Arrival Time | 6.5 ns |
| Required Time | 9.5 ns |
| Slack | +3.0 ns |

The values above are illustrative.

They demonstrate the structure of timing analysis rather than a specific measured SKY130 result.

---

# 81. WNS and TNS Relationship

WNS and TNS provide complementary information.

Consider:

```text
Path 1 = +0.5 ns
Path 2 = +0.2 ns
Path 3 = -0.3 ns
Path 4 = -0.1 ns
Path 5 = -0.2 ns
```

Then:

```text
WNS = -0.3 ns

TNS = -0.3 - 0.1 - 0.2
    = -0.6 ns
```

Interpretation:

```text
WNS
 |
 +----> Worst Individual Violation

TNS
 |
 +----> Overall Negative Timing Across Violating Paths
```

Both metrics are useful during optimization.

---

# 82. Timing Closure

Timing closure is the process of repeatedly analyzing and optimizing the design until timing requirements are satisfied.

A simplified flow is:

```text
Design
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
STA
  |
  v
Timing Violations?
  |
  +-------- No --------> Timing Closure
  |
 Yes
  |
  v
Identify Critical Paths
  |
  v
Optimize
  |
  v
Re-run STA
  |
  +---------------------> Repeat
```

Timing closure is usually iterative rather than a single operation.

---

# 83. Timing Closure Optimization Techniques

Common techniques include:

## Cell Sizing

Use stronger cells on timing-critical paths.

```text
Weak Cell
   |
   v
Slow Path
   |
   v
Stronger Cell
   |
   v
Reduced Delay
```

## Buffer Insertion

Useful for high-fanout nets.

```text
High Fanout
     |
     v
Buffer Insertion
     |
     v
Load Distribution
```

## Logic Optimization

Reduce unnecessary logic or logic depth.

```text
Deep Logic
   |
   v
Logic Optimization
   |
   v
Reduced Logic Depth
```

## Placement Optimization

Move cells closer together.

```text
Long Wire
   |
   v
Better Placement
   |
   v
Shorter Wire
   |
   v
Reduced Delay
```

## Routing Optimization

Improve physical routing to reduce parasitic effects.

---

# 84. Setup Timing Optimization Flow

```text
Setup Violation
      |
      v
Find Worst Path
      |
      v
Check Cell Delay
      |
      +---- High Cell Delay
      |          |
      |          v
      |      Cell Sizing
      |
      +---- High Fanout
      |          |
      |          v
      |      Buffering
      |
      +---- Long Wire
      |          |
      |          v
      |      Placement/Routing
      |
      +---- Large Logic Depth
                 |
                 v
           Logic Optimization
                 |
                 v
              Re-run STA
```

---

# 85. Hold Timing Optimization Flow

```text
Hold Violation
      |
      v
Find Shortest Timing Path
      |
      v
Check Data Arrival
      |
      v
Add Controlled Delay
      |
      +----> Buffer
      |
      +----> Delay Cell
      |
      +----> Routing Adjustment
      |
      v
Re-run STA
      |
      v
Check Setup Timing
```

Hold fixing must not create new setup violations.

---

# 86. Timing Optimization and Power

Timing optimization may increase power.

For example:

```text
Upsizing Cell
      |
      +----> Delay Decreases
      |
      +----> Capacitance Increases
      |
      +----> Power May Increase
```

Similarly:

```text
Buffer Insertion
      |
      +----> Timing May Improve
      |
      +----> Cell Count Increases
      |
      +----> Power May Increase
```

Therefore:

```text
Timing Optimization
        |
        +----> Area
        |
        +----> Power
        |
        +----> Routing
        |
        +----> Congestion
```

must be considered together.

---

# 87. Common Timing Problems

Several common problems can appear during physical implementation.

## Problem 1: High Fanout

```text
High Fanout
     |
     v
High Load
     |
     v
Slow Transition
     |
     v
Timing Violation
```

## Problem 2: Long Interconnect

```text
Long Wire
    |
    v
High RC
    |
    v
High Delay
```

## Problem 3: Excessive Logic Depth

```text
Many Logic Levels
       |
       v
Large Combinational Delay
       |
       v
Setup Violation
```

## Problem 4: Clock Skew

```text
Unequal Clock Paths
       |
       v
Clock Skew
       |
       v
Setup / Hold Impact
```

## Problem 5: Routing Congestion

```text
Congestion
    |
    v
Difficult Routing
    |
    v
Longer / Poorer Routes
    |
    v
Timing Degradation
```

---

# 88. High-Fanout Optimization

High-fanout signals may require buffer trees.

Before optimization:

```text
                +---- FF1
                |
                +---- FF2
                |
Driver ---------+---- FF3
                |
                +---- FF4
                |
                +---- FF5
                |
                +---- FF6
```

After optimization:

```text
                    Driver
                       |
                 +-----+-----+
                 |           |
                 v           v
                B1           B2
              / | \         / | \
             /  |  \       /  |  \
           FF1 FF2 FF3   FF4 FF5 FF6
```

This creates a hierarchical fanout structure.

---

# 89. Clock Transition

Clock transition describes how quickly the clock signal changes state.

A good clock waveform should have sufficiently sharp edges.

```text
Fast Clock Transition

      ______
     /
____/

Slow Clock Transition

       ______
      /
     /
____/
```

Poor transition may affect:

- Sequential cell timing
- Clock power
- Signal integrity
- Clock-tree constraints

CTS therefore controls clock transition through appropriate buffering and routing.

---

# 90. Clock Tree Balance

A balanced clock tree attempts to deliver the clock to sequential endpoints with controlled latency differences.

```text
                    Clock Source
                         |
                       Buffer
                         |
                +--------+--------+
                |                 |
              Buffer            Buffer
                |                 |
               FF1               FF2
```

The objective is not necessarily to make every physical path identical in every aspect, but to control the clock arrival relationship according to the timing requirements.

---

# 91. Clock Tree and Data Path Interaction

Clock timing cannot be considered independently from data timing.

```text
                 Clock Network
                       |
                       v
               Launch / Capture
                   Timing
                       ^
                       |
                 Data Network
```

Both networks determine the final timing result.

A simplified register-to-register relationship is:

```text
Launch Clock
     |
     v
Launch FF
     |
     v
Data Path
     |
     v
Capture FF
     ^
     |
Capture Clock
```

Therefore:

```text
Data Delay + Clock Delay
          |
          v
      Timing Result
```

---

# 92. Clock Gating Considerations

Clock gating is used to reduce unnecessary clock switching.

A simplified clock-gating structure is:

```text
Clock ----+
          |
          v
     Clock Gating Cell
          ^
          |
       Enable
          |
          v
      Gated Clock
```

Clock-gating cells are specially designed to avoid unwanted glitches caused by arbitrary combinational logic.

Timing analysis must consider:

- Gating checks
- Enable timing
- Clock transitions
- Generated clock relationships

---

# 93. Generated Clocks

Some designs create clocks derived from other clocks.

Conceptually:

```text
Primary Clock
      |
      v
Clock Divider / PLL / Logic
      |
      v
Generated Clock
      |
      v
Sequential Logic
```

STA must understand the relationship between the original clock and generated clock.

Incorrect clock definitions can result in incorrect timing analysis.

---

# 94. Signal Integrity and Timing

Signal integrity effects can influence timing.

Nearby wires can interact through coupling capacitance.

```text
Aggressor Wire
--------------------------

Victim Wire
==========================
```

When the aggressor switches, the victim may experience an unwanted voltage disturbance.

This may affect:

- Delay
- Transition
- Noise margin
- Clock behavior

Therefore, post-route timing analysis can include signal-integrity effects depending on the analysis setup.

---

# 95. Timing and Routing Congestion

Routing congestion affects timing because congested regions may force wires to take longer or less optimal routes.

```text
Placement
    |
    v
Routing Congestion
    |
    v
Longer Routes
    |
    v
Higher RC
    |
    v
Higher Delay
    |
    v
Timing Violation
```

This is why timing and congestion are often optimized together.

---

# 96. Physical Design Timing Feedback

Timing information can influence multiple physical-design decisions.

```text
                  Timing Analysis
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
      Placement         CTS          Routing
          |              |              |
          +--------------+--------------+
                         |
                         v
                    Re-analysis
```

This creates a feedback loop.

---

# 97. Complete Timing Flow

The complete timing-aware physical design flow can be summarized as:

```text
RTL
 |
 v
Synthesis
 |
 v
Gate-Level Netlist
 |
 v
Floorplanning
 |
 v
Placement
 |
 v
Pre-CTS Timing
 |
 v
Clock Tree Synthesis
 |
 v
Post-CTS Timing
 |
 v
Routing
 |
 v
Parasitic Extraction
 |
 v
Post-Route STA
 |
 v
Timing Optimization
 |
 v
Timing Closure
```

---

# 98. Pre-CTS vs Post-CTS vs Post-Route

| Stage | Clock Model | Interconnect | Timing Accuracy |
|---|---|---|---|
| Pre-CTS | Mostly ideal | Estimated | Early estimate |
| Post-CTS | Physical clock tree | More realistic | Improved |
| Post-Route | Physical clock + routed wires | Extracted parasitics | More detailed |

This comparison shows why timing results may change throughout physical implementation.

---

# 99. Why Both Setup and Hold Must Be Checked

Checking only setup timing is not sufficient.

A design can have:

```text
Setup Timing = PASS
Hold Timing  = FAIL
```

or:

```text
Setup Timing = FAIL
Hold Timing  = PASS
```

Therefore both must be checked.

```text
                Timing Analysis
                      |
              +-------+-------+
              |               |
              v               v
           Setup            Hold
              |               |
              v               v
           PASS/FAIL       PASS/FAIL
              |               |
              +-------+-------+
                      |
                      v
               Overall Timing
```

---

# 100. Setup and Hold Example

Consider a simplified path:

```text
FF1 ---> Logic ---> FF2
```

Suppose:

```text
Clock Period = 10 ns
Tcq          = 1 ns
Logic Delay  = 6 ns
Wire Delay   = 1 ns
Setup Time   = 1 ns
```

Then:

```text
Total = 1 + 6 + 1 + 1

Total = 9 ns
```

Available clock period:

```text
10 ns
```

Therefore the simplified setup margin is:

```text
10 - 9 = 1 ns
```

This is an illustrative example.

---

# 101. Timing Debugging Mindset

When a timing violation appears, it is useful to follow a structured approach.

```text
Timing Violation
      |
      v
Which Path?
      |
      v
Which Endpoint?
      |
      v
Which Launch Register?
      |
      v
What Causes Delay?
      |
      +----------+-----------+
      |          |           |
      v          v           v
   Cell       Fanout       Wire
   Delay                   Delay
      |          |           |
      +----------+-----------+
                 |
                 v
            Optimization
                 |
                 v
              Re-run STA
```

The goal is to identify the actual source of the timing problem rather than changing cells randomly.

---

# 102. Timing Report: Important Fields

When reading a timing report, important fields include:

| Field | Meaning |
|---|---|
| Startpoint | Beginning of the analyzed timing path |
| Endpoint | End of the analyzed timing path |
| Launch Clock | Clock launching the data |
| Capture Clock | Clock capturing the data |
| Data Arrival Time | Time at which data reaches the endpoint |
| Data Required Time | Latest acceptable arrival time |
| Slack | Timing margin |
| Cell Delay | Delay introduced by cells |
| Net Delay | Delay introduced by interconnect |
| Clock Latency | Clock propagation delay |
| Clock Skew | Difference between clock arrivals |

Understanding these fields makes STA reports easier to interpret.

---

# 103. Timing Path Breakdown

A timing path may be divided into several components:

```text
Launch Clock
     |
     v
Clock Network
     |
     v
Launch Register
     |
     v
Clock-to-Q
     |
     v
Logic Cell 1
     |
     v
Wire 1
     |
     v
Logic Cell 2
     |
     v
Wire 2
     |
     v
Capture Register
     |
     v
Setup/Hold Requirement
```

This breakdown helps identify the source of timing degradation.

---

# 104. Cell Delay Dominated Path

Sometimes the majority of the delay comes from logic cells.

```text
FF1
 |
 v
Large Logic Depth
 |
 v
Multiple Cells
 |
 v
FF2
```

The solution may involve:

- Logic restructuring
- Cell sizing
- Reducing logic depth
- Replacing slow cells with faster alternatives

---

# 105. Interconnect Dominated Path

Sometimes the majority of the delay comes from wires.

```text
FF1
 |
 +------------------------------------+
                                      |
                                      v
                                     FF2
```

Possible solutions include:

- Moving cells closer
- Improving placement
- Improving routing
- Reducing fanout
- Adding appropriate buffers

The important distinction is:

```text
Cell-Dominated Delay
        |
        v
Optimize Logic / Cells

Interconnect-Dominated Delay
        |
        v
Optimize Placement / Routing
```

---

# 106. Timing Closure and Physical Optimization

Timing closure requires coordination between several physical design stages.

```text
                 Timing Closure
                       |
        +--------------+--------------+
        |              |              |
        v              v              v
    Placement         CTS          Routing
        |              |              |
        +--------------+--------------+
                       |
                       v
                      STA
                       |
                       v
                Timing Violations
                       |
                       v
                   Optimize
                       |
                       +------> Repeat
```

No single optimization technique is sufficient for every timing problem.

---

# 107. Area, Power and Timing Trade-Off

A physical design must balance several objectives.

```text
                  DESIGN
                     |
       +-------------+-------------+
       |             |             |
       v             v             v
     Area          Power         Timing
       |             |             |
       +-------------+-------------+
                     |
                     v
              Physical Design
```

For example:

```text
Increase Cell Size
       |
       +----> Delay decreases
       |
       +----> Area increases
       |
       +----> Power may increase
```

Therefore, timing closure must be performed without unnecessarily degrading other design metrics.

---

# 108. SKY130 Timing Context

The SKY130 process design ecosystem provides standard-cell libraries and physical design information that can be used for digital implementation and timing analysis.

A timing-aware flow can involve:

```text
RTL
 |
 v
Synthesis
 |
 v
Standard Cells
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
Parasitic Extraction
 |
 v
STA
```

Timing analysis uses the relevant standard-cell timing models and physical information associated with the selected implementation flow.

Actual numerical timing values should always be taken from the specific library, corner, constraints, and design being analyzed.

---

# 109. Timing Analysis and Standard-Cell Libraries

Standard-cell timing libraries provide the information required for STA.

Conceptually:

```text
Standard Cell Library
        |
        +---- Cell Delay
        |
        +---- Transition
        |
        +---- Setup
        |
        +---- Hold
        |
        +---- Power
        |
        v
Timing Analysis
```

The timing engine combines this library information with:

- Netlist
- Constraints
- Physical data
- Parasitics
- Clock information

to calculate timing.

---

# 110. Timing Analysis and Constraints

The same physical design can produce different timing results under different constraints.

For example:

```text
Same Netlist
     |
     +---- Clock = 10 ns
     |
     +---- Clock = 5 ns
```

A path that passes at one clock period may fail at another.

Therefore:

```text
Netlist
+
Library
+
Physical Data
+
Timing Constraints
        |
        v
Timing Result
```

All these inputs must be consistent.

---

# 111. Timing Analysis and Clock Frequency

A smaller clock period means less time is available for data propagation.

```text
Long Clock Period
+----------------------------------+

More Timing Budget
```

Compared with:

```text
Short Clock Period
+------------------+

Less Timing Budget
```

Therefore:

```text
Clock Frequency ↑
        |
        v
Clock Period ↓
        |
        v
Available Timing ↓
        |
        v
Timing Becomes More Restrictive
```

---

# 112. Timing Margin

Timing margin represents the amount of additional time available beyond the minimum requirement.

```text
Required Time
      |
      |<------ Margin ------>|
      |
Actual Arrival
```

Positive margin indicates available timing headroom.

Negative margin indicates a violation.

A design with larger positive timing margin generally has more room for variation, but the actual required margin depends on the design methodology and constraints.

---

# 113. Timing Closure Iteration

A realistic timing closure process may involve multiple iterations.

```text
Iteration 1
   |
   v
STA
   |
   v
Violations
   |
   v
Optimization
   |
   v

Iteration 2
   |
   v
STA
   |
   v
Remaining Violations
   |
   v
Optimization
   |
   v

Iteration 3
   |
   v
STA
   |
   v
Improved Timing
```

The process continues until the required timing objectives are achieved.

---

# 114. Common Timing Optimization Actions

| Problem | Possible Action |
|---|---|
| High cell delay | Cell sizing |
| High fanout | Buffer insertion |
| Long wire | Placement optimization |
| High logic depth | Logic restructuring |
| Slow transition | Stronger driver / buffering |
| Setup violation | Reduce data-path delay |
| Hold violation | Add controlled delay |
| Clock skew | CTS optimization |
| Routing congestion | Placement/routing optimization |
| Large capacitance | Reduce load or improve buffering |

The correct action depends on the actual timing report.

---

# 115. Timing Closure Flowchart

The complete timing closure concept can be summarized as:

```text
                    Physical Design
                          |
                          v
                     Run STA
                          |
                          v
                +-------------------+
                | Timing Violations?|
                +-------------------+
                    |           |
                   No          Yes
                    |           |
                    v           v
             Timing Closure   Analyze
                                |
                                v
                        Identify Root Cause
                                |
              +-----------------+----------------+
              |                 |                |
              v                 v                v
         Cell Problem     Placement Problem   Clock Problem
              |                 |                |
              v                 v                v
        Cell Optimization   Physical Opt.     CTS Optimization
              |                 |                |
              +-----------------+----------------+
                                |
                                v
                             Re-run STA
                                |
                                +------> Repeat
```

---

# 116. Complete Physical Design Timing Flow

```text
RTL Design
     |
     v
Functional Verification
     |
     v
Logic Synthesis
     |
     v
Gate-Level Netlist
     |
     v
Floorplanning
     |
     v
Power Planning
     |
     v
Placement
     |
     v
Pre-CTS Timing Analysis
     |
     v
Clock Tree Synthesis
     |
     v
Post-CTS Timing Analysis
     |
     v
Routing
     |
     v
Parasitic Extraction
     |
     v
Post-Route Static Timing Analysis
     |
     v
Timing Optimization
     |
     v
Timing Closure
```

---

# 117. Timing Closure Decision Process

A timing engineer can use the following conceptual decision process:

```text
STA Report
    |
    v
Check WNS
    |
    v
Check TNS
    |
    v
Find Critical Paths
    |
    v
Classify Problem
    |
    +----> Cell Delay
    |
    +----> Fanout
    |
    +----> Interconnect
    |
    +----> Clock Skew
    |
    +----> Transition
    |
    +----> Constraint
    |
    v
Apply Appropriate Optimization
    |
    v
Re-run STA
```

This structured approach reduces unnecessary optimization.

---

# 118. Importance of Post-CTS STA

Post-CTS STA is important because the clock network is now physically implemented.

Before CTS:

```text
Ideal Clock
     |
     v
Approximate Timing
```

After CTS:

```text
Real Clock Tree
     |
     +---- Clock Latency
     |
     +---- Clock Skew
     |
     +---- Clock Transition
     |
     v
More Realistic Timing
```

Post-CTS analysis therefore provides a more realistic understanding of sequential timing.

---

# 119. Importance of Post-Route STA

Post-route STA provides even more physical information.

```text
Routing
   |
   v
Actual Wire Geometry
   |
   v
Parasitic Extraction
   |
   v
R and C Information
   |
   v
Post-Route STA
```

This analysis can reveal timing changes caused by:

- Actual wire lengths
- Routing parasitics
- Coupling effects
- Clock routing
- Congestion
- Physical implementation

---

# 120. Timing Closure Mindset

Timing closure should be treated as an engineering optimization process rather than simply checking whether a single number is positive or negative.

The engineer should ask:

1. Which path is failing?
2. Is the problem setup or hold?
3. Is the delay caused by cells or wires?
4. Is fanout too high?
5. Is placement contributing to the problem?
6. Is clock skew contributing?
7. Is the transition too slow?
8. Can the problem be fixed without excessive power or area cost?
9. Did the optimization create another violation?
10. Does the final implementation satisfy the required constraints?

This creates a systematic debugging approach.

---

# 121. Key Engineering Relationships

Several important relationships should be remembered.

## Relationship 1

```text
Input Slew ↑
     |
     v
Cell Delay May ↑
```

## Relationship 2

```text
Output Load ↑
     |
     v
Cell Delay ↑
```

## Relationship 3

```text
Wire Length ↑
     |
     v
Parasitic R and C ↑
     |
     v
Interconnect Delay ↑
```

## Relationship 4

```text
Clock Skew
     |
     +----> Setup Timing
     |
     +----> Hold Timing
```

## Relationship 5

```text
Fanout ↑
     |
     v
Load ↑
     |
     v
Transition Degrades
     |
     v
Delay ↑
```

## Relationship 6

```text
Clock Frequency ↑
     |
     v
Clock Period ↓
     |
     v
Timing Budget ↓
```

---

# 122. Important Timing Equations

## Clock Frequency

```text
F = 1 / T
```

## Clock Period

```text
T = 1 / F
```

## Simplified Setup Requirement

```text
Tclk >= Tcq + Tcomb + Twire + Tsetup + Tuncertainty
```

## Simplified Setup Slack

```text
Setup Slack =
Required Arrival Time
-
Actual Arrival Time
```

## Simplified Hold Condition

```text
Tcq + Tcomb + Twire + Tskew >= Thold
```

## Clock Skew

```text
Clock Skew =
Capture Clock Arrival
-
Launch Clock Arrival
```

## Dynamic Power Relationship

```text
Pdynamic ∝ C × V² × f × α
```

These equations are simplified conceptual relationships. Exact STA calculations depend on the timing methodology, constraints, library models, clock definitions, and analysis corner.

---

# 123. Practical Questions for Timing Analysis

## What happens if the combinational path becomes longer?

```text
Logic Depth ↑
     |
     v
Data Delay ↑
     |
     v
Setup Slack ↓
```

## What happens if fanout increases?

```text
Fanout ↑
     |
     v
Load Capacitance ↑
     |
     v
Delay ↑
```

## What happens if the wire becomes longer?

```text
Wire Length ↑
     |
     v
Parasitic RC ↑
     |
     v
Interconnect Delay ↑
```

## What happens if clock skew changes?

```text
Clock Skew Changes
     |
     +----> Setup Timing Changes
     |
     +----> Hold Timing Changes
```

## What happens after CTS?

```text
Ideal Clock
     |
     v
Physical Clock Tree
     |
     v
Actual Clock Latency and Skew
     |
     v
Post-CTS Timing
```

---

# 124. Conceptual Comparison of Setup and Hold

| Feature | Setup | Hold |
|---|---|---|
| Main concern | Data arrives too late | Data changes too early |
| Checked around | Capture edge | Capture edge |
| Main path issue | Excessive maximum delay | Insufficient minimum delay |
| Common cause | Long logic/wire path | Very short data path |
| Typical fix | Speed up path | Add controlled delay |
| Clock skew effect | Important | Important |

The same path can have different setup and hold behavior.

---

# 125. Conceptual Comparison of Pre-CTS and Post-CTS

| Feature | Pre-CTS | Post-CTS |
|---|---|---|
| Clock | Mostly ideal | Physical clock tree |
| Clock latency | Estimated/idealized | Physical |
| Clock skew | Limited/estimated | Actual tree-based |
| Clock buffers | Not fully implemented | Implemented |
| Clock routing | Not final | Physically constructed |
| Timing accuracy | Early estimate | More realistic |

---

# 126. Conceptual Comparison of Post-CTS and Post-Route

| Feature | Post-CTS | Post-Route |
|---|---|---|
| Clock tree | Implemented | Implemented |
| Data routing | Not final | Final/near-final |
| Parasitics | Estimated or partial | Extracted |
| Wire RC | Approximate | More accurate |
| Coupling | Limited/estimated | Can be analyzed from extraction |
| Timing | More realistic | Most physically detailed |

---

# 127. Why Timing Closure Is Iterative

Timing closure is iterative because optimizing one aspect can affect another.

For example:

```text
Increase Cell Size
       |
       v
Delay Decreases
       |
       +----> Power Increases
       |
       +----> Area Increases
```

Another example:

```text
Add Buffer
     |
     v
Fanout Reduced
     |
     v
Timing Improves
     |
     +----> Area Increases
     |
     +----> Power Increases
```

Another example:

```text
Move Cells Closer
     |
     v
Wire Length Decreases
     |
     v
Timing Improves
     |
     +----> Congestion May Increase
```

Therefore, physical design requires repeated analysis.

---

# 128. Engineering Trade-Offs in CTS

CTS also involves trade-offs.

```text
               CTS
                |
      +---------+---------+
      |         |         |
      v         v         v
    Skew      Power     Area
      |         |         |
      +---------+---------+
                |
                v
         Clock Quality
```

Reducing skew may require:

- Additional buffers
- Longer or different routing
- Larger cells
- More routing resources

Therefore, CTS must balance timing and implementation cost.

---

# 129. Engineering Trade-Offs in Timing Closure

A complete timing optimization problem can be viewed as:

```text
                    TIMING
                      ^
                      |
                      |
             +--------+--------+
             |                 |
             |                 |
          AREA <------------> POWER
             |
             |
             v
        PHYSICAL DESIGN
```

The objective is to achieve timing requirements while maintaining acceptable:

- Area
- Power
- Congestion
- Signal integrity
- Routing resources

---

# 130. Timing Analysis Summary Flow

```text
                 TIMING ANALYSIS
                        |
        +---------------+---------------+
        |               |               |
        v               v               v
     Cell Delay     Wire Delay      Clock Delay
        |               |               |
        +---------------+---------------+
                        |
                        v
                 Timing Path
                        |
                        v
                Setup / Hold Check
                        |
                        v
                     Slack
                        |
             +----------+----------+
             |                     |
             v                     v
        Positive Slack       Negative Slack
             |                     |
             v                     v
         Pass Timing          Violation
                                   |
                                   v
                              Optimization
                                   |
                                   v
                                Re-run STA
```

---

# 131. Complete CTS and STA Relationship

The relationship between CTS and STA can be summarized as:

```text
Placement
   |
   v
Clock Tree Synthesis
   |
   v
Clock Buffers
   |
   v
Clock Routing
   |
   v
Clock Latency
   |
   v
Clock Skew
   |
   v
Setup / Hold Analysis
   |
   v
Static Timing Analysis
   |
   v
Timing Optimization
```

CTS creates the physical clock environment that STA evaluates.

---

# 132. Complete Module Flowchart

The complete concept covered in this module can be summarized as:

```text
              STANDARD-CELL TIMING
                       |
                       v
                  CELL DELAY
                       |
                       v
                 FLOORPLANNING
                       |
                       v
                   PLACEMENT
                       |
                       v
                DATA PATH DELAY
                       |
                       v
             CLOCK TREE SYNTHESIS
                       |
          +------------+------------+
          |                         |
          v                         v
     Clock Latency             Clock Skew
          |                         |
          +------------+------------+
                       |
                       v
                POST-CTS STA
                       |
          +------------+------------+
          |                         |
          v                         v
        Setup                      Hold
          |                         |
          +------------+------------+
                       |
                       v
                    ROUTING
                       |
                       v
              PARASITIC EXTRACTION
                       |
                       v
                POST-ROUTE STA
                       |
                       v
                WNS / TNS / Slack
                       |
                       v
              TIMING OPTIMIZATION
                       |
                       v
                 TIMING CLOSURE
```

---

# 133. Key Learning Outcomes

After studying this module, the following concepts should be understood:

- Importance of timing in ASIC design
- Relationship between logical and physical timing
- Standard-cell timing characterization
- Input slew
- Output load
- Fanout
- Cell delay
- Interconnect delay
- Parasitic resistance and capacitance
- Setup timing
- Hold timing
- Clock-to-Q delay
- Ideal clock
- Real clock network
- Clock Tree Synthesis
- Clock buffering
- Clock latency
- Clock skew
- H-tree clock distribution
- Clock shielding
- Placement and timing interaction
- Static Timing Analysis
- Timing constraints
- Data Arrival Time
- Data Required Time
- Slack
- WNS
- TNS
- Critical paths
- Setup optimization
- Hold optimization
- Post-CTS timing
- Post-route timing
- Timing closure
- Power-area-timing trade-offs

---

# 134. Key Takeaways

## Timing Is More Than Cell Delay

Timing depends on both:

```text
Cell Delay
+
Interconnect Delay
+
Clock Behavior
```

---

## Physical Design Changes Timing

```text
Placement
   |
   v
Wire Length
   |
   v
Parasitics
   |
   v
Timing
```

---

## CTS Makes the Clock Physical

```text
Ideal Clock
     |
     v
CTS
     |
     v
Physical Clock Tree
     |
     v
Latency + Skew
```

---

## Setup and Hold Are Different

```text
Setup -> Data Must Not Arrive Late

Hold  -> Data Must Not Arrive Early
```

---

## STA Provides Timing Visibility

```text
Netlist
+
Libraries
+
Constraints
+
Physical Information
+
Parasitics
        |
        v
      STA
        |
        v
Timing Reports
```

---

## Timing Closure Is Iterative

```text
Analyze
   |
   v
Identify Violation
   |
   v
Optimize
   |
   v
Re-analyze
   |
   +------> Repeat
```

---

# 135. Final Conclusion

Timing analysis connects logical design with physical implementation.

A digital circuit may be logically correct, but physical effects such as cell delay, wire resistance, capacitance, fanout, clock latency, and clock skew determine whether the circuit can operate reliably at the required frequency.

The timing-aware physical design flow can therefore be summarized as:

```text
RTL
 |
 v
Synthesis
 |
 v
Placement
 |
 v
Clock Tree Synthesis
 |
 v
Routing
 |
 v
Parasitic Extraction
 |
 v
Static Timing Analysis
 |
 v
Timing Optimization
 |
 v
Timing Closure
```

Clock Tree Synthesis plays a particularly important role because the clock is the reference used by sequential elements. Once the clock becomes physically implemented, its latency, skew, transition, buffering, and routing must be considered.

Static Timing Analysis then evaluates the interaction between the clock network and data paths.

The major timing relationships can be summarized as:

```text
Input Slew
    |
    v
Cell Delay

Output Load
    |
    v
Cell Delay

Wire Length
    |
    v
Parasitic RC
    |
    v
Interconnect Delay

Clock Tree
    |
    v
Clock Latency
    |
    v
Clock Skew
    |
    v
Setup / Hold Timing

Setup / Hold Analysis
    |
    v
Slack
    |
    v
WNS / TNS
    |
    v
Timing Optimization
    |
    v
Timing Closure
```

The central idea of this module is:

> **A physically implemented digital design must be analyzed not only for logical correctness, but also for whether every relevant timing path satisfies its required constraints.**

Timing closure is therefore an iterative engineering process involving:

- Standard-cell optimization
- Placement optimization
- Clock Tree Synthesis
- Buffer insertion
- Cell sizing
- Routing optimization
- Parasitic extraction
- Static Timing Analysis

A successful physical design flow continuously connects:

```text
LOGIC
  ↓
PHYSICAL IMPLEMENTATION
  ↓
CLOCK DISTRIBUTION
  ↓
PARASITICS
  ↓
TIMING ANALYSIS
  ↓
OPTIMIZATION
  ↓
TIMING CLOSURE
```

This provides the foundation for understanding how a synthesized RTL design progresses toward a timing-aware physical implementation in a SKY130-based ASIC design flow.
