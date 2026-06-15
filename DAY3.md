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
