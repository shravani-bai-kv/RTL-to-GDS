# OpenLane ASIC Implementation & Layout Engineering

This repository serves as an engineering logbook capturing the implementation flow of digital designs from architectural descriptions down to physical layout (GDSII). The workflow is executed using the automated OpenLane infrastructure targeting the SkyWater 130nm process node.

## 🎛️ Design Specifications
* **Core Under Test:**  picorv32a
* **Target Node:** sky130_fd_sc_hd
* **Framework:** OpenLane / OpenROAD toolchain

---

## 📅 Daily Progress Logs
Click on any of the sections below to view the dedicated file containing technical walkthroughs, daily summaries, and specific tool output data:

* [**Day 1:** Workspace Initialization & Logic Synthesis](./DAY1.md)
* [**Day 2:** Floorplanning Layout & Physical Placement](./DAY2.md)
* [**Day 3:** Standard Cell Characterization & Spice Simulations](./DAY3.md)
* [**Day 4:** Timing Constraints & Clock Tree Synthesis (CTS)](./DAY4.md)
* [**Day 5:** Route Optimization & GDSII Mask Generation](./DAY5.md)

---

## 🛠️ Debugging Log & Engineering Solutions

### 🔍 Log Entry 1: [Insert Short Error Name Here]
* **Observed Failure:** [Describe what error popped up on your screen or paste a line of the log].
* **Investigation & Fix:** [Write what you changed to make it work, e.g., "Corrected the path tracking in the design config layout file"].

---

## 📁 Repository Layout
```text
├── DAY1.md
├── DAY2.md
│   └── day2_section2.md  <-- Holds the 16 mask cmos process
├── DAY3.md
├── DAY4.md
├── DAY5.md
└── README.md
