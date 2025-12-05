# MIPS16 Processor – ASIC Implementation

This repository documents the ASIC implementation flow of a ready implemented MIPS16 processor, covering floorplanning, power planning, placement, CTS, routing, congestion analysis,IR-drop verification, and multi-corner timing sign-off.
The objective is to achieve the highest possible operating frequency while maintaining smallest area, power integrity, and routing feasibility.

---

## Project Overview

This project implements a full digital backend flow starting from RTL synthesis and ending at sign-off. The goals include:

- Maximizing clock frequency
- Minimizing core area without causing routing failures
- Keeping IR-drop within 2% of supply voltage (≤ 22 mV at VDD = 1.1 V)
- Achieving timing closure in FF and SS corners
- Ensuring formal equivalence between RTL and post-layout netlists

---

## Design Requirements

- Initial Clock Period: 4 ns  
- Initial Core Utilization: 0.25  
- Input/Output Delays: 30% of clock period  
- Clock Uncertainty: 0.35 ns  
- Floorplan: H-shaped core region  
- Pin Placement:
  - Input pins on left (Metal5)
  - Output pins on right (Metal5)
- Max IR-drop: 22 mV  

---

## Floorplanning

- Core area derived from:  
  **Core Area = Total Standard Cell Area / Core Utilization**
- H-shaped core used for balanced placement and routing.
- Inputs placed on the left; outputs on the right to structure connectivity.

---

## Power Planning

### Trial 1
- Power grid built from Metal6 to Metal10.
- Virtual power pads inserted around the core.
- Maximum IR-drop: **8.6 mV** (within limit).

### Trial 2
- Power mesh changed to Metal8–Metal10 to free routing layers.
- Strap width and count reduced to compensate for higher utilization.
- Maximum IR-drop: **19.72 mV** (within limit).
- Power ring removed due to small design size.

---

## Placement

- Standard cells placed within H-shaped region.
- Trial 1 Cell Density: 0.4–0.5 → Not congested  
- Trial 2 Cell Density: 0.5–0.6 → Moderate  
- Pin density high on the right side due to concentrated outputs.
- Congestion evaluated before CTS and routing.

---

## Routing & DRC Fixing

- After CTS and routing in Trial 1, nine DRC violations were found near the high pin-density region.
- All violations resolved by moving related standard cells closer to output pins.

---

## Timing Sign-off

### Trial 1 – Clock 4 ns, Utilization 0.25
| Corner | Setup Slack | Hold Slack |
|--------|-------------|------------|
| FF     | 2.2284 ns   | 0.0022 ns  |
| SS     | 2.1405 ns   | 0.1548 ns  |

### Trial 2 – Clock 2 ns, Utilization 0.4
| Corner | Setup Slack | Hold Slack |
|--------|-------------|------------|
| FF     | 0.6543 ns   | 0.0008 ns  |
| SS     | 0.4018 ns   | 0.1538 ns  |

All setup and hold violations were fixed using buffer insertion and cell resizing.

---

## Experimental Trials Summary

### Trial 1
- Clock: 4 ns (250 MHz)  
- Core Utilization: 0.25  
- Low congestion, strong timing margins.

### Trial 2
- Clock: 2 ns (500 MHz)  
- Core Utilization: 0.4  
- Modified power mesh for more routing resources.
- Clean routing, timing closure achieved.

---

## Final Conclusions

- Maximum stable operating frequency: **500 MHz (2 ns)**  
- Higher frequency possible based on slack, but blocked by routing congestion
- Core utilization **cannot exceed 0.4** without causing routing failure
- Power mesh adjustments increase routing resources but do not eliminate congestion entirely

---
