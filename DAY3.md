# Sky130 Day 3 - Design Library Cell Using Magic Layout and ngspice Characterization

## SECTION - 1 THEORY
---
### 1. IO Placer Revision
This lab focuses on configuring and revising the Input/Output (IO) placement. IO placement is crucial for defining how external signals interface with the core design. 
* Reviewing the orientation, spacing, and tracking of IO pads.
* Ensuring the design meets physical constraints before moving deeper into the cell layout.
---
### 2. SPICE Deck Creation for CMOS Inverter
To simulate a CMOS inverter, a SPICE deck (netlist) must be constructed manually or extracted. The deck defines:
* **Model Files:** Inclusion of the specific Sky130 NMOS and PMOS transistor models.
* **Component Connectivity:** Defining nodes for the Drain, Gate, Source, and Substrate (Bulk) for both devices.
* **Component Values:** Specifying transistor width ($W$) and length ($L$).
* **Stimulus & Sources:** Setting up power supplies ($V_{DD}$) and input pulse signals ($V_{in}$).
---
### 3. SPICE Simulation Lab for CMOS Inverter
Once the SPICE deck is prepared, the next step involves running the transient simulation using **ngspice**.
* Loading the generated `.spice` file into the ngspice tool.
* Plotting the input vs. output voltage waveforms over time to visually verify the digital inverter functionality (i.e., output is low when input is high, and vice versa).
---
### 4. Switching Threshold $V_m$
The switching threshold ($V_m$) is a critical static characteristic where the input voltage equals the output voltage ($V_{in} = V_{out}$).
* **DC Transfer Characteristics:** Running a DC sweep simulation in ngspice to plot $V_{out}$ against $V_{in}$.
* **Analysis:** Finding the intersection point where $V_{in} = V_{out}$. A perfectly symmetrical inverter will have $V_m \approx \frac{V_{DD}}{2}$.
---
### 5. Static and Dynamic Simulation of CMOS Inverter
This lab extends the analysis to evaluate both the steady-state performance and the timing delays of the CMOS inverter.

#### Static Parameters
* **Noise Margins:** Determining $NM_H$ (Noise Margin High) and $NM_L$ (Noise Margin Low) from the DC characteristic curves.

#### Dynamic Parameters
* **Rise Time ($t_r$):** Time taken for the output waveform to rise from 10% to 90% of its final value.
* **Fall Time ($t_f$):** Time taken for the output waveform to drop from 90% to 10% of its initial value.
* **Propagation Delay:** Measuring the time difference between the input changing by 50% and the output changing by 50% ($t_{pdHL}$ and $t_{pdLH}$).

## Laboratory Observations

```bash
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Clone the repository with custom inverter design
git clone [https://github.com/nickson-jose/vsdstdcelldesign](https://github.com/nickson-jose/vsdstdcelldesign)

# Change into repository directory
cd vsdstdcelldesign

# Copy magic tech file to the repo directory for easy access
cp /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .

# Check contents whether everything is present
ls

# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
```
<img width="1222" height="863" alt="cloning git and opening mag and inv" src="https://github.com/user-attachments/assets/a12bcad6-f3a3-47b3-b8a2-1aa8547c557c" />
<img width="1770" height="516" alt="to copy sky130A" src="https://github.com/user-attachments/assets/66f26ac0-4f0a-4b3a-8619-ff272fc2731a" />
<img width="1206" height="261" alt="image" src="https://github.com/user-attachments/assets/ed173d3c-04d6-4bee-9856-207dd99b6ae7" />
<img width="1450" height="737" alt="inverter" src="https://github.com/user-attachments/assets/1546571f-d471-4f7e-b1bb-25df9d4a269c" />

## SECTION 2
### 📝 Detailed CMOS Fabrication Theory

The complete step-by-step physical implementation and fabrication flow—detailing the 16-mask CMOS process from substrate selection up to higher-level metallization (Lessons L1 to L7)—has been fully documented in a separate dedicated file.

👉 You can find the comprehensive notes here: [day3_section2.md](./day3_section2.md)

## SECTION - 3 
## LAB OBSERVATIONS
# Section 3: Custom Inverter Design, Extraction, and Simulation

This section covers the end-to-end process of pulling a custom inverter cell layout, analyzing it within Magic VLSI, performing parasitic extraction, and verifying its timing behavior via post-layout SPICE simulations.

---

## Task Breakdown Summary

* **Tasks 1–5:** Repository cloning, layout exploration, SPICE netlist generation, and characterization simulations.
  * *Artifact Directory:* `Section 3 - Tasks 1 to 5 (vsdstdcelldesign)`
* **Task 6:** Debugging and resolving Design Rule Check (DRC) violations in the legacy SkyWater tech file.
  * *Artifact Directory:* `Section 3 - Task 6 (drc_tests)`

---

## Step-by-Step Implementation
### Firstly Task 6
### 1. Fetching the Custom Cell Repository
First, navigate to your active OpenLANE workspace, clone the targeted inverter layout repository, and bring in the local SkyWater 130nm technology file for local referencing.

```bash
# Navigate to the core working environment
cd Desktop/work/tools/openlane_working_dir/openlane

# Clone the custom inverter cell layout assets
git clone [https://github.com/nickson-jose/vsdstdcelldesign](https://github.com/nickson-jose/vsdstdcelldesign)

# Move into the newly created project folder
cd vsdstdcelldesign

# Link the Sky130 PDK tech file to this directory for Magic compatibility
cp /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .

# Verify the file transfer and repository structure
ls

# Initialize Magic to review the standard cell layout
magic -T sky130A.tech sky130_inv.mag &
```
Screenshot of commands run
<img width="1222" height="863" alt="cloning git and opening mag and inv" src="https://github.com/user-attachments/assets/59f164d5-571c-4ea5-aaa9-deafc059c732" />
<img width="1770" height="516" alt="to copy sky130A" src="https://github.com/user-attachments/assets/702dbe4a-4c5c-4748-b295-cb60e9200a03" />
Load the custom inverter layout in magic and explore.
<img width="1450" height="737" alt="inverter" src="https://github.com/user-attachments/assets/c799fa13-a0ec-41af-93e1-1aa7fcd9eed6" />

NMOS and PMOS identified
<img width="1438" height="732" alt="nmos" src="https://github.com/user-attachments/assets/771ad604-1e14-4421-82b3-5a7d86b54294" />
<img width="1366" height="676" alt="pmos" src="https://github.com/user-attachments/assets/899d8771-7185-42d1-b936-93acae5909d8" />

Output Y connectivity to PMOS and NMOS drain verified
<img width="1438" height="740" alt="drain of both" src="https://github.com/user-attachments/assets/30cd21b6-333c-412d-8d4e-80bf4e5d0421" />
PMOS source connectivity to VDD (here VPWR) verified
<img width="1437" height="740" alt="gate of pmos" src="https://github.com/user-attachments/assets/3004e6ee-d2b5-4341-99b4-dd5d50d2348b" />
NMOS source connectivity to VSS (here VGND) verified
<img width="1437" height="737" alt="gate of nmos" src="https://github.com/user-attachments/assets/e0d89132-b470-4226-b983-3fc10a6b40d1" />

Deleting or rearrangeing necessary layout part to see DRC error
<img width="1447" height="737" alt="drc error" src="https://github.com/user-attachments/assets/8d26d6a3-9034-40c3-8df2-a1e985adbbc8" />

### 3. Parasitic Netlist Extraction

To analyze the electrical performance of your layout, you need to extract its physical properties into a simulation-ready netlist. Run the following sequence of commands within the Magic **Tkcon** window:

```tcl
# 1. Verify your current working directory path
pwd

# 2. Extract the physical layout geometry into intermediate .ext files
extract all

# 3. Configure the extraction engine to include all parasitic resistances and capacitances
ext2spice cthresh 0 rthresh 0

# 4. Generate the final .spice netlist file from the extracted data
ext2spice
```
Screenshot of tkcon window after running above commands
<img width="1417" height="811" alt="ext2spice" src="https://github.com/user-attachments/assets/d75ee4af-55be-459c-8337-feaf6aa30061" />
