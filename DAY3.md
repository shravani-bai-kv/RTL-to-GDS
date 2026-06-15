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

