# Day 2 - Chip Floorplanning Considerations & Pre-Placed Cells
## Section 1: Chip Floorplanning Considerations
### 1. Core and Die Definitions
Before placing components, the physical boundaries of the chip must be defined:
* **Die:** The entire size of the silicon chip, including the core and the outer wrapper area reserved for I/O pins and pads.
* **Core:** The inner area of the chip where the actual fundamental logic cells (netlist) and macro blocks are placed.

---

### 2. Key Floorplanning Metrics

#### A. Utilization Factor
The **Utilization Factor** defines how densely packed the core area is with logical cells.

$$\text{Utilization Factor} = \frac{\text{Area Occupied by Netlist}}{\text{Total Area of the Core}}$$

> **Note:** In practical chip design, a utilization factor of **1 (100%)** is never targeted. Designing at 100% leaves no space for subsequent routing wires, clock tree synthesis (CTS) buffers, or timing optimization cells. Realistic designs target a utilization between **50% and 70%**.

#### B. Aspect Ratio
The **Aspect Ratio** defines the geometric shape of the chip's core by looking at the ratio of its height to its width.

$$\text{Aspect Ratio} = \frac{\text{Height of the Core}}{\text{Width of the Core}}$$

> **Note:** An aspect ratio of **1.0** signifies a perfectly **square** chip core. Any value other than 1.0 implies a rectangular shape.

---

### 3. Concept of Pre-Placed Cells

A complex netlist rarely consists of only basic, standalone logic gates (like individual AND/OR gates). Instead, it is typically structured into large, reusable, and functional blocks.

#### A. The Evolution from Gate-Level to Black Boxes
The floorplanning phase establishes order by taking chaotic gate-level netlists and grouping them logically:

1. **Identifying Functional Blocks:** As seen in the circuit schematic, independent sub-circuits (e.g., Block 1 containing gates A1–A4, and Block 2 containing gates A5–A8) are identified based on their connectivity.
2. **"Black Boxing" the Layout:** Once these gates are grouped, the internal logic is hidden or "black-boxed." The EDA tool no longer looks at the individual gates inside; it only sees an abstract macro block with fixed physical boundaries.
3. **Modular Separation (IPs/Macros):** These black boxes are treated as completely independent Intellectual Properties (IPs) or modules. They are given explicit input/output ports (e.g., Block 1 has outputs `O1` to `O4`, which feed directly into Block 2's inputs `a` to `d`).

<img width="1140" height="727" alt="preplaced cells" src="https://github.com/user-attachments/assets/1b01c209-9a6d-4937-9831-98cbdb50d9a5" />


---

#### B. Noise Margin & Signal Integrity

When digital signals travel across a chip, electrical interference introduces unwanted voltage spikes, or **noise bumps**. To prevent data corruption, circuits rely on predefined voltage thresholds.

#### a. Voltage Threshold Definitions
* **$V_{OH}$ (Voltage Output High):** Minimum guaranteed output voltage for Logic '1'.
* **$V_{IH}$ (Voltage Input High):** Minimum required input voltage to register a Logic '1'.
* **$V_{IL}$ (Voltage Input Low):** Maximum allowable input voltage to register a Logic '0'.
* **$V_{OL}$ (Voltage Output Low):** Maximum guaranteed output voltage for Logic '0'.
* **Undefined Region:** The unstable voltage zone between $V_{IH}$ and $V_{IL}$.

#### b. Impact of Noise Induced Bumps
1.  **Below $V_{IL}$:** The noise bump is minor and stays within the Noise Margin Low ($NM_L$) boundary. The signal is safely interpreted as **Logic '0'**.
2.  **Between $V_{IL}$ and $V_{IH}$:** The noise bump pushes the signal into the **Undefined Region**, causing unpredictable circuit behavior.
3.  **Above $V_{IH}$:** A severe noise spike forces a Logic '0' signal into the Logic '1' range, causing catastrophic data corruption.

> **Floorplan Connection:** To prevent noise bumps from pushing signals into the undefined or flipped regions, **De-coupling Capacitors (Decaps)** must be placed close to switching blocks during the floorplanning stage to suppress transient voltage spikes.
### 4. De-coupling Capacitors (Decaps)
When standard cells switch logic states (e.g., 0 to 1), they draw a sudden surge of current from the power grid. This can cause a local voltage drop (IR drop) or ground bounce, destabilizing the noise margin of nearby logic circuits.

* **The Solution:** Large **De-coupling Capacitors (Decaps)** are placed completely surrounding the pre-placed macro blocks and throughout the core.
* **How they work:** Decaps act as local electrical reservoirs. They charge up during steady-state conditions and discharge to supply the required transient current during fast switching operations, decoupling the local circuit from the main power supply's impedance path.

---

### 5. Power Planning
While decaps protect local regions, a comprehensive grid is needed across the entire chip canvas to prevent large-scale voltage drops.

* **The Issue with Single Power Lines:** If a single VDD/VSS wire runs across the chip, the wire's inherent resistance ($R$) combined with switching currents ($I$) creates a significant $I \cdot R$ drop by the time it reaches the center cells, potentially violating timing or functionality.
* **The Solution:** Power planning introduces a matrix structure:
  * **Power Rings:** Running around the entire periphery of the core/die.
  * **Power Stripes/Mesh:** Vertical and horizontal metal rails crossing the entire core area. Standard cells simply tap into the nearest upper/lower power rails, dramatically reducing effective resistance and keeping the local voltage stable.

---

### 6. Pin Placement and Logical Cell Placement Blockage
The final step of the floorplan stage defines how the internal core communicates with the external world.

* **Pin Placement:** Input, output, and clock pins are strategically placed along the core/die boundaries. Clock pins are often placed centrally to minimize clock skew, while data pins are placed closest to their corresponding internal logic groups.
* **Logical Cell Placement Blockages:** To prevent automated P&R tools from cluttering the area directly between the boundary pins and the core cells (which is strictly reserved for routing tracks), a **placement blockage** is applied to those outer boundary regions. This guarantees clear routing channels for the physical connections.

## Lab Observations

Steps to Run Floorplan Using OpenLANE
This section details how to execute the automated floorplanning stage within the OpenLANE interactive flow.

* **Flow Initialization:** Navigate to your OpenLANE directory and launch the Docker container.
* **Design Setup:** Prepare the configuration environment for the target design block using the following interactive commands:
  ```bash
  ./flow.tcl -interactive
  package require openlane 0.9
  prep -design picorv32a
  ```bash

  Running Floorplan: Execute the core floorplanning step:
```bash
run_floorplan
```bash
Output: This command processes the synthesized netlist alongside your config.tcl parameters to generate a DEF (Design Exchange Format) file containing core boundaries and pin coordinates.

Insert your SKY_L6 screenshot below:

SKY_L7 - Review Floorplan Files and Steps to View Floorplan
Once the floorplan completes, you need to verify the generated files before moving forward.

Directory Navigation: Locate your newly generated DEF file by navigating to:

Plaintext
<openlane_dir>/designs/picorv32a/runs/<run_tag>/results/floorplan/picorv32a.floorplan.def
Configuration Priorities: If a specific design variable isn't explicitly defined inside your local config.tcl file, OpenLANE automatically inherits system-level defaults.

Visualization Tool: To visually inspect the physical layout, you will utilize Magic VLSI Layout Tool along with the Sky130 technology (.tech) file and the merged LEF file.

Insert your SKY_L7 screenshot below:

SKY_L8 - Review Floorplan Layout in Magic
This step covers loading the floorplanned layout into Magic to physically inspect the layout constraints.

Command to Open Magic: Run the following command in your terminal to load the design definitions into the tool:

Bash
magic -T <path_to_sky130A.tech> lef read <path_to_merged.lef> def read picorv32a.floorplan.def &
Visual Verification Points:

Die vs. Core: Verify the outer die and inner core spaces match your target configurations.

I/O Pins: Check that the input and output pins are properly distributed along the margins of the core.

Standard Cell Area: At this stage, standard cells will not be visible inside the core region; they are blocked out and waiting for the subsequent placement step.

Insert your SKY_L8 screenshot below:
