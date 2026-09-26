
# Module 5 – Final Steps for RTL to GDS Using TritonRoute and OpenSTA

## 1. Introduction

The next step after **Physical Design (PD)** is **Routing**.

### Routing

Routing is the process of establishing a connection between two points:

- Source
- Target

The objective is to connect the required source and target points while satisfying routing constraints and design rules.

---
<img width="940" height="623" alt="image" src="https://github.com/user-attachments/assets/7d7d7858-5363-483c-8d8b-ec0cd79c69f9" />

# 2. Maze Routing

Maze routing is a technique used to find a path between a source and a target.

One of the well-known maze-routing algorithms is:

**Lee's Algorithm (1961)**

The objective is to find the best possible routing path while avoiding obstacles and placement blockages.

### Basic Idea

- Consider the routing area as a grid.
- Start from the source.
- Look for adjacent grid boxes.
- Number the adjacent grid boxes.
- Continue expanding through the available grid.
- Avoid blocked regions.
- Continue until the target is reached.
- Trace the route back from the target to the source.

---

# 3. Lee's Algorithm

Lee's algorithm is a grid-based maze-routing algorithm.

### Procedure

1. Start from the source.
2. Look for adjacent grid boxes.
3. Assign numbers to the adjacent grid boxes.
4. Continue numbering the available grid points.
5. Avoid placement blockages.
6. Continue until the target is reached.
7. Trace the path from the target back to the source.

### Example

If a grid point is numbered `3`, an adjacent grid point can be numbered `4`.

```text
       3
       |
       4
```

The routing grid is used to search for a valid path.

---
<img width="940" height="759" alt="image" src="https://github.com/user-attachments/assets/2ab55b99-feb8-4bb8-9eca-78e1841da084" />

# 4. Routing Grid

A **routing grid** represents the routing area as a set of grid locations.

The routing algorithm searches the grid to find a valid path between the source and target.

### Important Points

- Adjacent grid boxes are examined.
- Grid locations are numbered.
- Placement blockages are considered.
- Obstacles are avoided.
- The final route must satisfy design rules.
- The final route must provide valid connectivity.

---

# 5. Placement Blockage

A **placement blockage** is a region that restricts placement or routing.

The routing algorithm must avoid the blocked region and find an alternative path.

```text
Source
  |
  |
  |        Placement Blockage
  |          ███████████
  |          ███████████
  |                |
  |                |
  +----------------+---------- Target
```

---

# 6. DRC – Design Rule Check

**DRC stands for Design Rule Check.**

DRC checks whether the physical layout satisfies the design rules defined by the technology.

One important aspect of DRC is checking the required distance between wires.

### Three Important Design Rules

1. Wire Width
2. Wire Pitch
3. Wire Spacing

---

# 7. Wire Width

**Wire width** is the width of the metal wire.

The wire width must satisfy the minimum width requirement specified by the technology.

```text
       Wire
┌────────────────────┐
│                    │
└────────────────────┘
       ← Width →
```

If the wire width is less than the required value, a DRC violation may occur.

---

# 8. Wire Pitch

**Wire pitch** refers to the distance associated with the repeated placement of wires.

The routing arrangement must maintain the required pitch according to the technology rules.

---

# 9. Wire Spacing

**Wire spacing** is the distance between two wires.

```text
┌────────────────────┐
│       Wire 1       │
└────────────────────┘

        ↑
        │
      Spacing
        │
        ↓

┌────────────────────┐
│       Wire 2       │
└────────────────────┘
```

Insufficient wire spacing can result in a DRC violation or signal short.

---

# 10. Signal Short

A **signal short** occurs when two signals that should remain electrically separate become unintentionally connected.

Signal shorts can occur because of:

- Insufficient wire spacing
- Incorrect routing
- Improper layer usage
- Incorrect via placement
- Overlapping wires

### Solving Signal Short

To solve a signal short, another metal layer can be introduced.

```text
Metal Layer Mn
      |
      ↓
Metal Layer Mn+1
```

An additional metal layer provides another routing path.

---

# 11. Metal Layer

Metal layers are used to route signals in the physical design.

If routing cannot be completed on one metal layer, another metal layer can be used.

```text
Metal Layer Mn
────────────────────
        |
        | Via
        ↓
────────────────────
Metal Layer Mn+1
```

---

# 12. Via

A **via** is used to establish an electrical connection between different metal layers.

```text
Metal Layer Mn
────────────────────
        |
        |
       Via
        |
        |
────────────────────
Metal Layer Mn+1
```

Vias must satisfy the required design rules.

---
<img width="940" height="374" alt="image" src="https://github.com/user-attachments/assets/174ec9b3-88c2-439e-94b3-55ffaa099fda" />

# 13. Via Width

**Via width** specifies the required width of a via.

The via must satisfy the minimum width requirement defined by the technology.

Incorrect via dimensions can cause a DRC violation.

---
<img width="940" height="293" alt="image" src="https://github.com/user-attachments/assets/6dcf9812-6f83-47c5-b769-1c00e7c6f342" />

# 14. Via Spacing

**Via spacing** is the required distance between vias.

```text
Via 1              Via 2
  □                  □
  <---- spacing ---->
```

The required via spacing must be maintained.

---
<img width="940" height="297" alt="image" src="https://github.com/user-attachments/assets/fba6b1a8-e71e-41e4-90d1-4271e6a24757" />

# 15. Design Rules to be Checked

Two important design rules related to vias are:

### 1. Via Width

```text
Via Width
→ No Signal Short
```

### 2. Via Spacing

```text
Via Spacing
→ No Signal Short
```

These rules help maintain valid connections between metal layers.

---

# 16. Routing Commands

The current DEF can be checked using:

```tcl
echo $::env(CURRENT_DEF)
```

<img width="709" height="208" alt="image" src="https://github.com/user-attachments/assets/2a644131-ab2b-44b0-ac4e-1618a450f755" />

The routing-related commands include:

```tcl
gen_pdn
```

<img width="940" height="629" alt="image" src="https://github.com/user-attachments/assets/2dbf5cf5-ed29-4d5a-b248-77c9c293e025" />
This is a **floorplan of a VLSI chip** showing **where different physical components are placed and how power is distributed**.

| Component | Purpose |
|---|---|
| **I/O Pads** | Interface between the chip and the outside world |
| **Core** | Main logic area of the chip |
| **Standard Cell Rows** | Places logic cells in organized rows |
| **Standard Cells** | Implements digital logic |
| **Power Ring** | Distributes VDD/GND around the core |
| **Power Stripes** | Carries power into the core |
| **RAM Macro** | Large pre-designed memory block |
| **Block Power Ring** | Supplies power around the macro |
| **Block Halo** | Keeps routing and placement space around the macro |
| **I/O-to-Core Spacing** | Provides space between the I/O pads and the core |
and:

```tcl
echo $::env(CURRENT_DEF)
```

The routing stage can then be initiated using:

```tcl
run_routing
```

---

# 17. Routing Using TritonRoute

The routing stage using TritonRoute can be divided into:

- Fast Route / Global Route
- Detailed Route

<img width="940" height="415" alt="image" src="https://github.com/user-attachments/assets/93902d6f-cf16-4004-aa3e-62c6b22bcc50" />

```text
Routing
   |
   +----------------------+
   |                      |
Fast Route            Detail Route
Global Route          Detailed Route
```

---

# 18. Fast Route / Global Route

**Fast Route** is the global routing stage.

It generates an initial routing solution and routing guides.

```text
Fast Route
     ↓
Global Route
     ↓
Routing Guides
```

Fast routing determines the general routing topology before detailed routing.

---

# 19. Detailed Route

**Detailed Route** is the stage where the actual physical routing is generated.

```text
Routing Guides
      ↓
TritonRoute
      ↓
Detailed Route
      ↓
Physical Wires and Vias
```

The detailed routing process must satisfy the required routing constraints and design rules.

---

# 20. TritonRoute

**TritonRoute** performs detailed routing.

## Problem Statement

The problem is to determine a detailed routing solution between the required points while satisfying routing constraints and design rules.

## Inputs

The inputs to TritonRoute are:

- LEF
- DEF
- Processed Route Guides

## Output

The output is:

**Detailed routing selection with optimized wire length and via count.**

---

# 21. TritonRoute Constraints

The main constraints are:

- Route guide honoring
- Connectivity constraints
- Design rules

TritonRoute must generate a route that satisfies these constraints.

---

# 22. TritonRoute Functions

TritonRoute:

- Performs unit detailed routing.
- Honors processed route guides obtained after fast routing.
- Assumes route guides satisfy inter-guide connectivity constraints and design rules.
- Works on an MLP-based panel routing approach.
- Performs intra-layer parallel routing.
- Performs inter-layer sequential routing.

---

# 23. Processed Route Guides

The processed route guides are obtained after fast routing.

<img width="940" height="441" alt="image" src="https://github.com/user-attachments/assets/4c26b31e-86fc-4224-9193-a618d13c6709" />

### Requirements of Processed Route Guides

Processed route guides:

- Should have unit width.
- Should be in the preferred direction.
- Should provide the required routing information.
- Should satisfy routing constraints.

---

# 24. Intra-Layer Parallel and Inter-Layer Sequential Routing

TritonRoute performs:

### Intra-Layer Parallel Routing

Routing is performed in parallel within the same metal layer.

### Inter-Layer Sequential Routing

Routing is performed sequentially between different metal layers.

```text
Intra-Layer
    ↓
Parallel Routing

Inter-Layer
    ↓
Sequential Routing
```

<img width="940" height="280" alt="image" src="https://github.com/user-attachments/assets/f37149fc-e25d-47c6-94ed-1ada98f8a16a" />

---

# 25. Inter-Guide Connectivity

**Inter-guide connectivity** refers to the connectivity between two routing guides.

Two guides can be connected when:

1. They are on the same metal layer with touching edges.

**OR**

2. They are on neighboring metal layers with a non-zero vertically overlapped area.

---

# 26. Inter-Guide Connectivity – Same Metal Layer

Two guides are connected if they are on the same metal layer and their edges are touching.

```text
Guide 1 ───────────────┐
                       │
                       └────────────── Guide 2
                            Touching Edges
```

---

# 27. Inter-Guide Connectivity – Neighboring Metal Layers

Two guides can also be connected when they are on neighboring metal layers and have a non-zero vertical overlap.

```text
Metal Layer M1
────────────────────────
        │
        │
        │ Vertical Overlap
        │
────────────────────────
Metal Layer M2
```

---

# 28. Handling Connectivity

Connectivity is handled using **Access Points (APs)**.

### AP – Access Point

An **Access Point** is a grid point on the metal layer used to establish a connection.

An access point can be used to connect:

- Lower-layer segments
- Upper-layer segments
- Pins
- I/O ports

---

# 29. Access Point

An access point is a grid point on the metal layer.

It is used to connect:

- Lower-layer segments
- Upper-layer segments
- Pins
- I/O ports

```text
Upper Metal Layer
────────────────────
        |
        | AP
        |
────────────────────
Lower Metal Layer
```

---

# 30. Access Point Connectivity

Access points provide possible locations for establishing valid routing connections.

The router selects appropriate access points while considering:

- Connectivity
- Metal layers
- Routing direction
- Via availability
- Design rules

<img width="940" height="331" alt="image" src="https://github.com/user-attachments/assets/be3492e7-9b4a-4dac-837d-1dfee5fc4685" />

---

# 31. Routing Topology

Routing topology represents the structure used to connect all the required points.

The routing topology should:

- Connect all required points.
- Maintain connectivity.
- Follow the routing guides.
- Avoid blockages.
- Satisfy design rules.
- Minimize unnecessary routing resources.

---

# 32. Routing Topology Algorithm

The routing topology can be optimized using a minimum spanning tree approach.

## Algorithm: Optimization of Routing Topology

```text
1. for all i = 1 to n-1 do

2.     for all j = i+1 to n do

3.         cost_ij ← dist(AP_i, AP_j)

4.     end for

5. end for

6. T ← MST(APs, costs)

7. Return e_ij ∈ T
```

---

# 33. Cost Calculation

The cost between two access points is calculated using their distance.

```text
cost_ij ← dist(AP_i, AP_j)
```

Here:

- `AP_i` = Access Point `i`
- `AP_j` = Access Point `j`
- `cost_ij` = Cost of connecting the two access points
- `dist()` = Distance between the access points

---

# 34. Minimum Spanning Tree

The routing topology algorithm uses a **Minimum Spanning Tree (MST)**.

```text
T ← MST(APs, costs)
```

The MST provides a tree connecting the required access points based on the calculated connection costs.

---

# 35. Routing Topology Optimization

The objective of routing topology optimization is to obtain a valid and efficient routing structure.

Important considerations include:

- Wire length
- Via count
- Connectivity
- Routing guides
- Design rules
- Routing resources

---

<img width="808" height="100" alt="image" src="https://github.com/user-attachments/assets/56b801ff-f618-45a5-9fd5-7a951b6d4dd7" />

# 36. DRC and Routing

After routing, the design should be checked for design-rule violations.

Important checks include:

- Wire width
- Wire pitch
- Wire spacing
- Via width
- Via spacing
- Signal shorts
- Connectivity

---

# 37. Signal Short Prevention

Signal shorts can be avoided by:

- Maintaining the required wire spacing.
- Following the routing guides.
- Using different metal layers when necessary.
- Maintaining proper via spacing.
- Avoiding overlapping signals.
- Following technology-specific design rules.

---

# 38. Overall Routing Flow

The routing flow can be represented as:

```text
Physical Design
      ↓
Current DEF
      ↓
Fast / Global Route
      ↓
Processed Route Guides
      ↓
TritonRoute
      ↓
Access Points
      ↓
Routing Topology
      ↓
Detailed Route
      ↓
DRC / Connectivity Checks
```

---

# 39. Routing Flow Using TritonRoute

```text
Placed Design
      ↓
Routing Grid
      ↓
Fast Route / Global Route
      ↓
Routing Guides
      ↓
Process Route Guides
      ↓
TritonRoute
      ↓
Inter-Guide Connectivity
      ↓
Access Point Selection
      ↓
Routing Topology
      ↓
Detailed Routing
      ↓
DRC
```

---

# 40. Routing Constraints

The routing process must satisfy several constraints.

### Main Routing Constraints

- Route guide honoring
- Connectivity constraints
- Design rules
- Wire width
- Wire pitch
- Wire spacing
- Via width
- Via spacing
- Preferred routing direction
- Metal-layer constraints
- Placement blockages
- Routing blockages

---

# 41. OpenSTA

**OpenSTA** is used for **Static Timing Analysis (STA)**.

After physical implementation and routing, timing analysis is performed to verify the timing behavior of the design.

OpenSTA can be used to analyze:

- Setup timing
- Hold timing
- Clock paths
- Data paths
- Arrival time
- Required time
- Slack

---

# 42. Static Timing Analysis

**Static Timing Analysis (STA)** is used to verify whether the implemented design satisfies its timing requirements.

STA analyzes the timing paths without requiring exhaustive functional simulation.

---

# 43. Setup Timing

**Setup timing** checks whether data reaches the receiving sequential element sufficiently before the active clock edge.

A setup violation occurs when the setup timing requirement is not satisfied.

---

# 44. Hold Timing

**Hold timing** checks whether the data remains stable for the required time after the active clock edge.

A hold violation occurs when the hold timing requirement is not satisfied.

---

# 45. Slack

Slack represents the timing margin of a path.

```text
Slack = Required Time - Arrival Time
```

### Positive Slack

Positive slack indicates that the timing requirement is satisfied for that path.

### Negative Slack

Negative slack indicates a timing violation.

---

# 46. Clock Path Analysis

OpenSTA analyzes the timing of clock paths.

Important parameters include:

- Clock arrival time
- Clock delay
- Clock skew
- Setup requirement
- Hold requirement

---

# 47. Data Path Analysis

OpenSTA analyzes the timing of data paths between sequential elements.

The analysis considers:

- Data arrival time
- Required arrival time
- Path delay
- Setup timing
- Hold timing
- Slack

---

# 48. Post-Route Verification

After detailed routing, the design must be checked for:

- Connectivity
- DRC violations
- Signal shorts
- Wire spacing
- Wire width
- Via spacing
- Via width
- Routing completeness
- Timing violations

---

# 49. Final RTL-to-GDS Flow

The complete RTL-to-GDS flow can be represented as:

```text
RTL
 ↓
Synthesis
 ↓
Floorplanning
 ↓
Placement
 ↓
Clock Tree Synthesis
 ↓
Fast / Global Routing
 ↓
Routing Guides
 ↓
Detailed Routing using TritonRoute
 ↓
DRC / Connectivity Verification
 ↓
Static Timing Analysis using OpenSTA
 ↓
Final Verification
 ↓
GDSII
```

---

# 50. Final Steps for RTL to GDS

The final steps include:

1. Physical Design
2. Routing
3. Fast / Global Routing
4. Generation of Routing Guides
5. Processing of Routing Guides
6. Detailed Routing using TritonRoute
7. Access Point Handling
8. Routing Topology Optimization
9. DRC Checks
10. Connectivity Checks
11. Post-Route Timing Analysis
12. Static Timing Analysis using OpenSTA
13. Final Verification
14. GDSII Generation

---

# 51. Key Concepts

## Routing

Establishes physical connections between source and target points.

## Maze Routing

Uses a grid-based search technique to find a valid routing path.

## Lee's Algorithm

A classical maze-routing algorithm introduced in 1961.

## DRC

Checks whether the physical layout follows the required design rules.

## TritonRoute

Performs detailed routing using processed routing guides.

## Routing Guide

Provides routing information and guides the detailed routing process.

## Access Point

Provides a valid grid point for establishing a routing connection.

## Routing Topology

Represents the structure used to connect the required access points.

## OpenSTA

Performs Static Timing Analysis on the implemented design.

---

# 52. Conclusion

Routing is one of the final major stages of the physical design flow.

The routing process establishes physical connections between source and target points while satisfying technology-specific design rules and connectivity constraints.

**Lee's Algorithm** provides a classical grid-based approach to maze routing.

**TritonRoute** performs detailed routing using processed route guides generated after fast/global routing. It handles routing constraints, inter-guide connectivity, access points, and routing topology.

The routing process must satisfy important design rules such as:

- Wire width
- Wire pitch
- Wire spacing
- Via width
- Via spacing

After detailed routing, the design is checked for DRC violations, connectivity problems, and signal shorts.

Finally, **OpenSTA** is used for static timing analysis to check setup timing, hold timing, data paths, clock paths, arrival time, required time, and slack.

The final objective is to obtain a physically connected and verified design that can proceed toward **GDSII generation**.

---

# 53. Complete Flow Summary

```text
RTL
  ↓
Synthesis
  ↓
Floorplanning
  ↓
Placement
  ↓
Clock Tree Synthesis
  ↓
Physical Design
  ↓
Routing
  ↓
Maze Routing / Routing Concepts
  ↓
Fast / Global Route
  ↓
Processed Route Guides
  ↓
TritonRoute
  ↓
Inter-Guide Connectivity
  ↓
Access Points
  ↓
Routing Topology
  ↓
Detailed Route
  ↓
Wire and Via Connections
  ↓
DRC
  ↓
Connectivity Verification
  ↓
OpenSTA
  ↓
Static Timing Analysis
  ↓
Final Verification
  ↓
GDSII
```

# Module 5 Complete

**Final Steps for RTL to GDS Using TritonRoute and OpenSTA**

