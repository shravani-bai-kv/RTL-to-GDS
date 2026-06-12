# Day 1: Inception of Open-Source EDA, OpenLANE & Sky130 PDK

## 🔬 Core Concepts & Technical Takeaways

### 1. Physical Architecture: Package, Die, and Core
When interacting with an embedded hardware board, the component commonly referred to as the "chip" is technically the **IC Package**. This external housing acts as a protective shield for the fragile silicon layout inside.

* **The Silicon Die:** Represents the complete, singular piece of manufactured silicon containing both the core processing logic and peripheral interface blocks.
* **The Core Area:** The dedicated internal real estate of the die where the primary digital logic gates and sequential elements are routed.
* **I/O Pads:** Positioned around the core's perimeter, these structural structures serve as the electrical interface points. They link the core logic to the external package pins via ultra-thin bond wires.

### 2. Physical Design Terminology
* **Foundry:** The dedicated semiconductor fabrication plant where the layout geometries are physically printed onto silicon wafers.
* **Foundry IPs:** Intellectual Property blocks tied to the fabrication process that require specialized foundry tuning (e.g., Phase-Locked Loops (PLLs), SRAM blocks).
* **Macros:** Pre-verified, complex digital blocks that can be reused across different SoC layouts.

---

## 💻 The Instruction Set Architecture (ISA) Translation Bridge

For a software application (written in a high-level language like C) to drive physical hardware gates, it must transition through a layered translation stack:

```text
[High-Level Application Code (C)]
               │
               ▼  (Compiler Execution)
[Target ISA Assembly Code (RISC-V)]
               │
               ▼  (Assembler Execution)
[Binary Machine Code (1s & 0s)]
               │
               ▼  (Hardware Mapping)
[RTL Hardware Description (Verilog)]
               │
               ▼  (Physical Flow: Synthesis -> PnR)
[Final Physical Silicon Layout (GDSII)]
### 🛠️ Lab Setup & Design Preparation
To begin the interactive flow inside the Docker container environment, the following operational steps were executed:

```bash
# Enter the OpenLane workflow root and launch the interactive shell
cd openlane
docker
./flow.tcl -interactive
```
<img width="756" height="492" alt="wth version" src="https://github.com/user-attachments/assets/d3dc0c1a-1407-40ab-823d-708ec4ea8d73" />

```bash
# Import the openlane tool package bundle
package require openlane 0.9
```
```bash
# Initialize the design directory structure for the target design
prep -design picorv32a
```
<img width="1266" height="812" alt="Screenshot 2026-06-12 163108 final complete" src="https://github.com/user-attachments/assets/0db63553-3ece-45c4-88d8-d6f0e182db13" />

### 📊 Post-Synthesis Metrics Analysis

Following the successful completion of the `run_synthesis` command, the generated Yosys log reports were audited to analyze structural cell utilization and calculate the total gate distribution:
<img width="1258" height="803" alt="Screenshot 2026-06-12 165226systhesis" src="https://github.com/user-attachments/assets/21d39de3-1174-4194-8767-4d7ee554e8cb" />

## 📉 Understanding the Post-Synthesis Flop Ratio

In digital synchronous ASIC design, evaluating the balance between sequential logic and combinational logic is critical for assessing performance, power, and area (PPA). This relationship is quantified using the **Flop Ratio**.

### 1. What is the Flop Ratio?
The Flop Ratio represents the percentage of total hardware components on the silicon die that are dedicated to data storage and state management (Flip-Flops) versus raw processing logic (combinational gates). 

It is calculated using the following formula:

$$\text{Flop Ratio (\%)} = \left( \frac{\text{Total Sequential Cells (DFF)}}{\text{Total Design Cells}} \right) \times 100$$

---
<img width="1052" height="632" alt="Screenshot 2026-06-12 170313reprt sysn" src="https://github.com/user-attachments/assets/ff7a3a1f-a8d4-4c81-a42a-85cf322300be" />

### 2. Engineering Insights from the Metrics

Based on your design metrics for the `picorv32a` core:
* **Total Cells:** 14,809
* **Sequential Cells (DFF):** 1,613
* **Calculated Flop Ratio:** **10.89%**
<img width="1280" height="853" alt="flopcounts" src="https://github.com/user-attachments/assets/9a4c6fe2-a20b-4e5d-8b02-2a245ad4add4" />

#### Structural Implications:
* **Datapath Density (89.11% Combinational):** A flop ratio of roughly 11% indicates that the design is heavily dominated by combinational logic blocks (adders, multiplexers, shifters, and control logic). This is characteristic of a RISC processor core like `picorv32a`, where complex instructions require deep mathematical and logical execution paths between register clock cycles.
* **Clock Tree Synthesis (CTS) Impact:** Because only ~11% of the total layout cells require a direct clock source, the automated clock distribution network will be less complex than a design with a high flop ratio (like an institutional memory controller or a deep pipeline FIFO). This translates to lower dynamic clock power dissipation and simpler skew management during the physical routing phase.
* **Pipeline Depth Balance:** The ratio reveals a balanced pipeline structure. If the flop ratio were drastically higher (e.g., 30%+), it would indicate an aggressively pipelined layout where registers are heavily interspersed to slice execution paths for ultra-high frequency targets.
