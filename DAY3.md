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

### 2. Load the custom inverter layout in magic and explore.
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
Screenshot of created spice file
<img width="898" height="345" alt="created spice" src="https://github.com/user-attachments/assets/86c4f0a5-e0fc-49f7-aee8-e9a592db1781" />

## 4. Editing the spice model file for analysis through simulation.
Measuring unit distance in layout grid
<img width="1013" height="646" alt="size" src="https://github.com/user-attachments/assets/0632e6c7-4a32-470d-bcea-ba1d94b6cb76" />
Final edited spice file ready for ngspice simulation
<img width="1033" height="685" alt="edit" src="https://github.com/user-attachments/assets/3b6b742a-171a-4a59-a3c0-21c0dce924ef" />

## 5. Post-layout ngspice simulations.
Commands for ngspice simulation
```bash
# Command to directly load spice file for simulation to ngspice
ngspice sky130_inv.spice

# Now that we have entered ngspice with the simulation spice file loaded we just have to load the plot
plot y vs time a
```
Screenshot of ngspice run
<img width="1516" height="943" alt="ngspice wf" src="https://github.com/user-attachments/assets/c0759e9d-012a-41dc-b6b0-2791ffef5376" />
Screenshot of generated plot
<img width="1743" height="868" alt="wf" src="https://github.com/user-attachments/assets/afb9c197-f57c-4533-a461-3065288229c1" />
#### Rise Transition Time Calculation

The rise transition time measures the speed of the output signal as it switches from a low logic state to a high logic state. It is calculated by finding the time difference between the 20% and 80% thresholds of the maximum output voltage ($3.3\text{ V}$).

##### Threshold Voltage Values:
* **20% of Output ($V_{IL}$):** $0.20 \times 3.3\text{ V} = 660\text{ mV}$
* **80% of Output ($V_{IH}$):** $0.80 \times 3.3\text{ V} = 2.64\text{ V}$

#### Formula:

$$\text{Rise Transition Time} = t_{\text{output@80\%}} - t_{\text{output@20\%}}$$

---

*To find these values during your post-layout simulation, place your cursors on the output waveform ($Y$) in `ngspice` at $660\text{ mV}$ and $2.64\text{ V}$ respectively, then subtract the time points.*

20% Screenshots
<img width="988" height="586" alt="image" src="https://github.com/user-attachments/assets/07188fde-aa7f-42e8-8178-7cf2e15e0675" />
<img width="1031" height="573" alt="image" src="https://github.com/user-attachments/assets/28505fcb-9a30-4847-b08a-3f49314eec5b" />
80% Screenshots
<img width="962" height="552" alt="image" src="https://github.com/user-attachments/assets/294b9ded-2a91-48e3-b2a8-56ca741314b0" />
<img width="865" height="521" alt="image" src="https://github.com/user-attachments/assets/f57ffdde-fff3-4202-9c7a-0f01ce1ad85a" />
#### Transition Time Metrics

#### Rise Transition Time

$$\text{Rise Time} = t_{\text{80\%}} - t_{\text{20\%}}$$
* $2.24638\text{ ns} - 2.18242\text{ ns} = \mathbf{0.06396\text{ ns}\ (63.96\text{ ps})}$
---
#### Fall Transition Time

$$\text{Fall Time} = t_{\text{20\%}} - t_{\text{80\%}}$$

* **80% Mark ($V_{IH}$):** $2.64\text{ V}$
* **20% Mark ($V_{IL}$):** $660\text{ mV}$

20% Screenshots
<img width="945" height="541" alt="image" src="https://github.com/user-attachments/assets/031a57dd-833d-4f3a-b67d-5764969ed451" />
<img width="735" height="463" alt="image" src="https://github.com/user-attachments/assets/dc38cdd6-eaf8-4dbe-80ab-fd40916d72f5" />

80% Screenshots
<img width="931" height="533" alt="image" src="https://github.com/user-attachments/assets/bfdf6446-3ace-4035-bccd-869deaa09e46" />
<img width="831" height="447" alt="image" src="https://github.com/user-attachments/assets/219115bf-e0ee-4f8e-bfc9-4700e9c47a8b" />

#### Fall Transition Time

$$\text{Fall Time} = t_{\text{20\%}} - t_{\text{80\%}}$$
* $4.0955\text{ ns} - 4.0536\text{ ns} = \mathbf{0.0419\text{ ns}\ (41.9\text{ ps})}$
---
#### Propagation Delay (Rise Cell Delay)
The rise cell delay measures the time gap between the input shifting low and the output pulling high, evaluated at the midpoint threshold.
* **50% Reference Voltage:** $1.65\text{ V}$
$$\text{Rise Cell Delay} = t_{\text{output@50\%}} - t_{\text{input@50\%}}$$
50% Screenshots
<img width="948" height="540" alt="image" src="https://github.com/user-attachments/assets/b15cd21d-1a53-44e1-88fe-1a0b25c0e666" />
<img width="742" height="522" alt="image" src="https://github.com/user-attachments/assets/2b31bdea-3f3f-435b-9a37-b933c56bdfd9" />

#### Rise Cell Delay Result

$$\text{Rise Delay} = t_{\text{out@50\%}} - t_{\text{in@50\%}}$$
* $2.21144\text{ ns} - 2.15008\text{ ns} = \mathbf{0.06136\text{ ns}\ (61.36\text{ ps})}$
---

#### Fall Cell Delay Calculation
The fall cell delay tracks the time it takes for the output to drop after the input switches high, measured at the $50\%$ switching point.
* **50% Reference Voltage:** $1.65\text{ V}$

$$\text{Fall Cell Delay} = t_{\text{out@50\%}} - t_{\text{in@50\%}}$$
50% Screenshots
<img width="947" height="537" alt="image" src="https://github.com/user-attachments/assets/734f95ed-5b47-42e1-97e5-78045eb1863f" />
<img width="603" height="515" alt="image" src="https://github.com/user-attachments/assets/9a5b8ffe-4f27-4190-9e69-b11df51a3e15" />

#### Fall Cell Delay Result
$$\text{Fall Delay} = t_{\text{out@50\%}} - t_{\text{in@50\%}}$$
* $4.07\text{ ns} - 4.05\text{ ns} = \mathbf{0.02\text{ ns}\ (20\text{ ps})}$

