# Day 1: Inception of Open-Source EDA & OpenLane Workspace

## 📑 Core Theory & Lecture Notes

### 🔹 SKY130_D1_SK1: Architectural Hardware-Software Interface
* **Introduction to QFN-48 Packaging:** Learned about chip physical layout constraints, IC packaging structures, and how pads link to physical pins.
* **Introduction to RISC-V ISA:** Explored the foundational open-source instruction set architecture, exploring how compilers translate high-level code down to assembly instructions.
* **From Software Application to Silicon:** Traced the complete structural transition mapping applications through OS kernels, compilers, and assemblers down to pure hardware execution.

### 🔹 SKY130_D1_SK2: SoC Architecture, Design Ecosystems & OpenLane
* **Introduction to ASIC Components:** Understood the core building blocks of a modern System-on-Chip (SoC), including pads, core structures, macros, and standard cells.
* **Simplified RTL2GDS Flow:** Walked through the fundamental phases of physical design implementation (Synthesis, Floorplanning, Placement, CTS, Routing, and Sign-off).
* **Introduction to OpenLane Architecture:** Explored the automated open-source design flow engine and how it integrates separate EDA tools into a consolidated macro-driven pipeline.

---

## 🛠️ Hands-On Workspace Setup (SKY130_D1_SK3)

### 1. OpenLane Directory Exploration
* Investigated the root workspace, PDK directory hierarchies (`sky130_fd_sc_hd`), and design tool tracking paths.

### 2. Design Preparation Stage
* Initialized the runtime workspace and loaded the automated target configurations for the design setup.
```bash
# Sourcing tool packages and invoking the interactive OpenLane shell
cd openlane
docker
./flow.tcl -interactive
package require openlane 0.9

# Preparing the core design layout environment
prep -design picorv32a
