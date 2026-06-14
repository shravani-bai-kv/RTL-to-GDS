# Day 2 - Good floorplan vs bad floorplan and introduction to library cells
## SECTION 1 - Chip Floorplanning Considerations & Pre-Placed Cells
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
---

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
---

## Lab Observations
### Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command

* **Flow Initialization:** Navigate to your OpenLANE directory and launch the Docker container.
* **Design Setup:** Prepare the configuration environment for the target design block using the following interactive commands:
  ```bash
  ./flow.tcl -interactive
  package require openlane 0.9
  prep -design picorv32a
  ```
 Now that the design is prepped and ready, we can run synthesis using following command
 ```bash
 run_synthesis
```
 Running Floorplan: Execute the core floorplanning step:
```bash
run_floorplan
```
Output: This command processes the synthesized netlist alongside your config.tcl parameters to generate a DEF (Design Exchange Format) file containing core boundaries and pin coordinates.

<img width="1010" height="558" alt="floorplan" src="https://github.com/user-attachments/assets/a5664c75-d345-4bb1-b3ac-7f3a9907576e" />


## Review Floorplan Files and Steps to View Floorplan
Once the floorplan completes, verifying the generated files before moving forward.
Directory Navigation: Locate your newly generated DEF file by navigating to:

```bash
Plaintext
<openlane_dir>/designs/picorv32a/runs/<run_tag>/results/floorplan/picorv32a.floorplan.def
```
Configuration Priorities: If a specific design variable isn't explicitly defined inside your local config.tcl file, OpenLANE automatically inherits system-level defaults.

Visualization Tool: To visually inspect the physical layout, you will utilize Magic VLSI Layout Tool along with the Sky130 technology (.tech) file and the merged LEF file.

<img width="731" height="488" alt="floorplan row" src="https://github.com/user-attachments/assets/7584083a-7645-4a15-a727-ef5b04d6b966" />
## Floorplan Analysis: Die Area Calculation

The DEF file explicitly defines the boundaries of the chip design using the `DIEAREA` statement. The values provided are in **database units**, which must be converted to **microns** using the defined unit scale.

### 1. Extracting Values from the DEF File

* **`UNITS DISTANCE MICRONS 1000 ;`** This indicates that $1000 \text{ database units} = 1 \mu\text{m}$ (micron).
* **`DIEAREA ( 0 0 ) ( 660685 671405 ) ;`**
  This defines a rectangular boundary stretching from the lower-left corner coordinate $(X_1, Y_1)$ to the upper-right corner coordinate $(X_2, Y_2)$:
  * $X_1 = 0, \quad Y_1 = 0$
  * $X_2 = 660685, \quad Y_2 = 671405$

---

### 2. Area Calculations

#### Step A: Calculate Dimensions in Microns
To convert the database coordinates to microns, we divide each dimension length by the unit scale factor ($1000$):

$$\text{Die Width (X)} = \frac{X_2 - X_1}{1000} = \frac{660685 - 0}{1000} = 660.685 \, \mu\text{m}$$

$$\text{Die Height (Y)} = \frac{Y_2 - Y_1}{1000} = \frac{671405 - 0}{1000} = 671.405 \, \mu\text{m}$$

#### Step B: Calculate Total Area
The total die area is the product of its width and height:

$$\text{Total Area} = \text{Width} \times \text{Height}$$

$$\text{Total Area} = 660.685 \, \mu\text{m} \times 671.405 \, \mu\text{m} \approx 443587.19 \, \mu\text{m}^2$$

### Summary of Results
* **Die Width:** $660.685 \, \mu\text{m}$
* **Die Height:** $671.405 \, \mu\text{m}$
* **Total Die Area:** **$443,587.19 \, \mu\text{m}^2$** (or $\approx 0.444 \, \text{mm}^2$)

## Review Floorplan Layout in Magic
This step covers loading the floorplanned layout into Magic to physically inspect the layout constraints.

Command to Open Magic: Run the following command in your terminal to load the design definitions into the tool:

```bash
# Change directory to the path containing the generated floorplan DEF file
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/17-03_12-06/results/floorplan/

# Load the floorplan DEF in the Magic tool using the Sky130 PDK tech file and merged LEF
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
      lef read ../../tmp/merged.lef \
      def read picorv32a.floorplan.def &
```

## Visual Verification Points:

Die vs. Core: Verify the outer die and inner core spaces match your target configurations.

Screenshot of floorplan def in magic
<img width="1448" height="745" alt="fp in def magic" src="https://github.com/user-attachments/assets/1520b5e1-47e3-460f-af42-fa5e7b685ccc" />
<img width="1437" height="738" alt="equididtant placement of ports" src="https://github.com/user-attachments/assets/4b0361da-87c7-491c-b2a7-fe78ebb75fd2" />
<img width="1428" height="787" alt="port layer as set through config tcl" src="https://github.com/user-attachments/assets/d46ac3c6-7921-44ca-94d7-18cb75a44fef" />

## Running Placement in OpenLANE

```bash
# Once floorplanning is completed successfully, run the placement stage.
# This step places the standard cells on the defined floorplan rows while optimizing for wire length and timing.
run_placement
```
Screenshots of placement run
<img width="1042" height="586" alt="plc run" src="https://github.com/user-attachments/assets/8dcc36d0-ca78-4b4c-a0e7-7f2d4523f083" />
<img width="922" height="487" alt="placrment" src="https://github.com/user-attachments/assets/c9d7b782-c088-4d66-8f7a-ae8c0bf747a2" />

## Loading the Generated Placement in Magic

```bash
# 1. Change directory to path containing generated placement def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/17-03_12-06/results/placement/

# 2. Command to load the placement def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
      lef read ../../tmp/merged.lef \
      def read picorv32a.placement.def &
```
Screenshots of floorplan def in magic after Placement
<img width="1440" height="745" alt="floorplan def in magic" src="https://github.com/user-attachments/assets/47132254-96e4-4f50-acde-49e72605c33b" />

Standard cells legally placed
<img width="1437" height="737" alt="fp zoom" src="https://github.com/user-attachments/assets/103acdba-afb6-4fa9-8fd7-c1a06008af9c" />

## SECTION - 2 Library Binding and Placement

### 1. Netlist Binding and Initial Place Design
The placement stage does not create logic; it translates the logical gates defined in your synthesized gate-level netlist into physical realities inside the core boundaries.

* **The Component Binding Process:** Every gate in the netlist (e.g., `AND2_X1`, `OR3_X2`, `FA_X1`) must be mapped to a concrete cell present in the PDK Standard Cell Library (`.lib`).
* **Library Properties Loaded:** During binding, the EDA tool extracts critical data for every cell:
  * **Physical Footprint:** Exact width, height, and boundary layout.
  * **Pin Geometry:** The precise $X/Y$ coordinates, metal layers, and shapes of the input/output pins, as well as the $V_{DD}$ and $V_{SS}$ rails.
* **Initial Placement (Global Routing Prep):** Standard cells are initially placed into designated **standard cell rows** mapped out during the floorplanning stage. The tool distributes them purely based on connectivity vectors to avoid overlapping while respecting cell site boundaries.
---

### 2. Optimize Placement Using Estimated Wire-Length and Capacitance
Initial placement focuses strictly on cell legality and rough positioning. Once finished, the tool optimizes placement to address electrical performance.

* **Interconnect Estimation (HPWL):** Because actual metal routing traces have not yet been drawn, the tool uses the **Half-Perimeter Wire Length (HPWL)** formula to estimate the physical span of each net:
  $$\text{HPWL} = (X_{\max} - X_{\min}) + (Y_{\max} - Y_{\min})$$
* **The RC Parasitic Problem:** Longer estimated wires equate to higher parasitic resistance ($R$) and capacitance ($C$). High capacitive loads degrade signal transition edges (slew rates) and increase delays ($T_{pd} \propto R \cdot C$).
* **Placement Driving Forces:** The placement engine identifies critical paths suffering from high estimated capacitance and physically pulls strongly-connected cells closer together, shortening the net span before timing is severely impacted.
---

### 3. Final Placement Optimization
After shifting cells to shorten critical nets, the tool enters a fine-grained optimization and legalization phase to fix electrical and timing violations.

* **Buffer/Repeater Insertion:** For long-distance signals (like global control lines or multi-fanout nets) where target cells cannot be placed any closer together, the engine splits the net by inserting **buffers**. This breaks down a massive $RC$ delay network into smaller, manageable stages.
* **Cell Upsizing/Sizing Optimization:** If a standard cell must drive an inherently heavy capacitive load, the optimizer replaces it with a larger drive-strength equivalent variant (e.g., swapping an `INV_X1` for an `INV_X4`). Larger cells feature wider transistors capable of sourcing/sinking current rapidly to meet strict setup timing windows.
* **Legalization:** Any optimization that shifts a cell or inserts a buffer can result in cell overlaps or fractional row alignment errors. The tool finishes by executing a local legalization pass to securely lock all cells onto the valid grid rows without altering optimized timing results.
---

### 4. Need for Libraries and Characterization
The Physical Design tool is fundamentally blind to physics; it relies completely on standard libraries to understand cell behavior.

* **The EDA-Foundry Link:** Libraries act as an abstraction layer. The fabrication foundry provides structural layout rules, while characterization translates those rules into timing and power models that EDA tools can rapidly process during optimization runs.
* **What is Characterization?** It is the extensive process of simulating every single cell using SPICE tools (like `ngspice`) across varying **PVT (Process, Voltage, Temperature)** corners.
* **Crucial Characterized Parameters Outputted to `.lib`:**
  * **Propagation Delay:** Time elapsed from $50\%$ of the input signal transition to $50\%$ of the output signal transition.
  * **Transition Time (Slew):** The time a signal takes to switch between specific threshold margins (typically $10\%$ to $90\%$ or $20\%$ to $80\%$).
  * **Timing Constraints:** Exact Setup time and Hold time limitations required by sequential storage elements like D-Flip-Flops.
---

### 5. Congestion Aware Placement Using RePlAce
Optimizing entirely for the shortest wire-lengths creates a fatal flaw: cells crowd together into localized dense clusters, leaving no physical gaps or routing channels for actual wires to thread through.

* **What is RePlAce?** RePlAce is the default analytical, non-linear global placement engine integrated natively inside the OpenLANE architecture.
* **Electrostatic Analogy:** RePlAce models standard cells as charged particles moving inside an electric field. Cells that share nets exert attractive forces on one another, while density overflow creates a repulsive force.
* **Density Balancing Over Congestion:** The engine solves mathematical equations to distribute cell density smoothly across the core. By continuously spreading cells out evenly, it proactively avoids routing congestion "hotspots", ensuring that the downstream detailed routing tool (`TritonRoute`) has ample physical tracks to complete the layout without DRC clean-up errors.
---

## SECTION - 3 Cell Design and Characterization Flows

### 1. Inputs for Cell Design Flow
Standard cell design requires a meticulous collection of industry-standard files provided by the foundry and PDK. These inputs act as the foundational guardrails before any physical layout or architectural circuit simulation can begin.

* **PDK (Process Design Kit) Requirements:** * **DRC & LVS Rules:** Foundries establish strict sub-micron manufacturing constraints (minimum metal width, spacing, and enclosure rules) that must be strictly coded into the EDA environment.
  * **SPICE Models:** Transistor models (BSIM) containing precise physical parameters (threshold voltage, channel length modulation, parasitics) for NMOS and PMOS devices.
* **User/Design Parameters:** * **Cell Height:** Determined during the floorplanning step to ensure the cell fits uniformly within the standard cell rows.
  * **Supply Voltage ($V_{DD}$):** The target power rail operating point.
  * **Metal Layers:** Specific layer assignments (typically local interconnects like `li1` and `met1` in Sky130) earmarked for pin connections.

---

### 2. Circuit Design Step
This stage focuses entirely on the electrical modeling and schematic creation of the component to ensure it behaves correctly under diverse load conditions.

* **Schematic Capture:** The designer implements the logic gate (e.g., an Inverter or NAND gate) by structurally connecting PMOS and NMOS networks to meet the exact logical truth table.
* **Transistor W/L Sizing:** PMOS and NMOS channel widths ($W$) and lengths ($L$) are carefully scaled. Since PMOS holes have inherently lower mobility than NMOS electrons ($\mu_n \approx 2.5 \times \mu_p$), PMOS devices are typically sized wider to balance rise and fall times.
* **Circuit Simulation (SPICE):** The schematic is simulated using tools like `ngspice` to verify raw functionality and basic electrical integrity before any physical layout shapes are drawn.

---

### 3. Layout Design Step
Layout design translates the symbolic electrical schematic into physical, geometric polygons that mask the actual silicon material layers.

* **PMOS/NMOS Placement:** Standard active diffusion regions ($N$-well, $P$-substrate) are drawn alongside polysilicon gate structures to form the physical PMOS and NMOS channels.
* **Stick Diagrams to Layout:** Designers map out the routing networks using stick diagrams to optimize area before converting them into absolute layout coordinates in layout tools like **Magic**.
* **Parasitic Extraction (PEX):** Once layout shapes are routed, the physical proximity of metal layers introduces unwanted unintended resistance and capacitance. PEX tools extract these layout parasitics into a `.spf` or post-layout SPICE netlist.
* **Physical Verification:**
  * **DRC (Design Rule Checking):** Confirms no geometric spacing, width, or density rules have been broken.
  * **LVS (Layout Versus Schematic):** Checks if the netlist extracted from the geometric layout layers perfectly matches the logical schematic captured in the previous step.

---

### 4. Typical Characterization Flow
Characterization takes the finalized, DRC/LVS-clean layout data and runs extensive matrix simulations to compile the timing, power, and environmental footprint parameters into the final operational software library (`.lib`).

* **GDSII/LEF and SPICE Ingestion:** The characterization engine (such as *Cadence Liberate* or open-source equivalents) imports the post-layout SPICE netlist containing extracted $R$ and $C$ parasitics.
* **The Characterization Matrix:** Simulations are run across an exhaustive matrix grid of:
  * **Input Slew Rates:** Variable rise/fall slopes of the incoming input signals.
  * **Output Load Capacitances:** Variable external loads connected to the output pin.
* **PVT Corners:** The entire matrix is simulated across distinct Process (Typical, Fast, Slow), Voltage (High, Low, Nominal), and Temperature (e.g., $-40^\circ\text{C}$ to $125^\circ\text{C}$) corners.
* **Output Generation:** The software tracks the switching thresholds and formats the data cleanly into industry-standard timing models (like Non-Linear Delay Models - NLDM) packaged neatly inside a readable `.lib` file.
---

## SECTION - 4 General Timing Characterization Parameters

### 1. Timing Threshold Definitions
To compute delays and transition times accurately, the EDA tools cannot look at ideal square waveforms; they must evaluate real-world analog waveforms. Standard cells are characterized using precise percentage thresholds of the power supply voltage ($V_{DD}$).

* **Input Thresholds:**
  * `slew_low_rise_thr`: The lower voltage point on an incoming rising edge (typically $20\%$ of $V_{DD}$ or $10\%$ of $V_{DD}$).
  * `slew_high_rise_thr`: The upper voltage point on an incoming rising edge (typically $80\%$ of $V_{DD}$ or $90\%$ of $V_{DD}$).
  * `slew_low_fall_thr` & `slew_high_fall_thr`: The corresponding low and high reference voltage points on an incoming falling edge.

* **Output Thresholds:**
  * Similar threshold points ($10\%/90\%$ or $20\%/80\%$) are defined for the output pin waveform to calculate the output slew rate.

* **Switching Threshold ($V_{th}$):**
  * This is the exact midpoint reference value (typically $50\%$ of $V_{DD}$). It serves as the primary standard anchor point for measuring propagation delays between input and output pins.

---

### 2. Propagation Delay and Transition Time
Using the threshold definitions established above, the characterization engine extracts the two most critical timing parameters required for Static Timing Analysis (STA).

* **Propagation Delay ($T_{pd}$):**
  Propagation delay is measured as the time difference between the $50\%$ point of the input transition and the $50\%$ point of the corresponding output transition.
  * **Rise Delay ($T_{pLH}$):** Measured from the $50\%$ point of the input switching to the $50\%$ point of the output *rising* edge.
  * **Fall Delay ($T_{pHL}$):** Measured from the $50\%$ point of the input switching to the $50\%$ point of the output *falling* edge.
  
  $$\text{Propagation Delay} = \text{Time}(\text{Output at } 50\% V_{DD}) - \text{Time}(\text{Input at } 50\% V_{DD})$$

* **Transition Time (Slew Rate):**
  Transition time refers to how long it takes for a signal edge to switch between its low state and high state. Slow transitions cause higher short-circuit power consumption and degrade downstream timing paths.
  * **Rise Time ($T_R$):** The time a signal takes to rise from the lower threshold to the upper threshold (e.g., from $20\%$ to $80\%$ of $V_{DD}$).
  * **Fall Time ($T_F$):** The time a signal takes to drop from the upper threshold to the lower threshold (e.g., from $80\%$ to $20\%$ of $V_{DD}$).
 ---
