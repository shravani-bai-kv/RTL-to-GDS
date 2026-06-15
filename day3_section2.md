# Openlane Sky130 Workshop - Day 3 (SKY130_D3_SK2)
## Inception of Layout – CMOS Fabrication Process (Lessons L1 to L7)

This documentation provides a comprehensive, step-by-step breakdown of the 16-mask CMOS fabrication process featured in the  Day 3 curriculum. It details the physical realization of a CMOS inverter from raw silicon substrate up to the global metallization layers.

---

## 📑 Table of Contents
1. [Step 1: Substrate Selection & Active Region Creation (SKY_L1)](#step-1-substrate-selection--active-region-creation-sky_l1)
2. [Step 2: Twin-Well Formation (SKY_L2)](#step-2-twin-well-formation-sky_l2)
3. [Step 3: Gate Terminal Formation (SKY_L3)](#step-3-gate-terminal-formation-sky_l3)
4. [Step 4: Lightly Doped Drain (LDD) Formation (SKY_L4)](#step-4-lightly-doped-drain-ldd-formation-sky_l4)
5. [Step 5: Source & Drain Formation (SKY_L5)](#step-5-source--drain-formation-sky_l5)
6. [Step 6: Local Interconnect Formation (SKY_L6)](#step-6-local-interconnect-formation-sky_l6)
7. [Step 7: Higher-Level Metal Formation (SKY_L7)](#step-7-higher-level-metal-formation-sky_l7)

---

## Step 1: Substrate Selection & Active Region Creation (SKY_L1)
The fabrication begins by establishing a high-quality physical substrate foundation and creating isolated semiconductor pockets to prevent cross-device electrical interference.

### 1. Substrate Properties
* **Substrate Type:** P-type substrate wafer.
* **Resistivity:** $5 \sim 50\ \Omega\cdot\text{cm}$
* **Doping Concentration:** $10^{15}\ \text{cm}^{-3}$
* **Crystalline Orientation:** $(100)$ orientation crystal structure.

### 2. Layer Stack Growth
* A thin buffer layer of **Silicon Dioxide ($\text{SiO}_2$)** ($\sim 40\text{nm}$) is thermally grown directly on top of the P-substrate.
* A protective **Silicon Nitride ($\text{Si}_3\text{N}_4$)** layer ($\sim 80\text{nm}$) is deposited via Chemical Vapor Deposition (CVD).
* A light-sensitive **Photoresist** layer ($\sim 1\mu\text{m}$) is uniformly spun across the surface.

### 3. Photolithography & LOCOS Isolation (Mask 1)
* **Mask 1** is applied to define the active areas destined for individual transistors.
* The wafer is exposed to UV light, developed, and chemically etched to strip away the unexposed photoresist and underlying nitride layer.
* **Local Oxidation of Silicon (LOCOS):** The wafer is loaded into an oxidation furnace. The remaining nitride blocks oxygen diffusion, while a deep, thick **Field Oxide (FOX)** layer grows in the unshielded sections. This provides structural and electrical isolation between active areas.
* The protective nitride and buffer oxide are chemically cleaned, leaving distinct active P-type silicon islands surrounded by isolation walls.

---

## Step 2: Twin-Well Formation (SKY_L2)
Because an NMOS requires a P-type channel background and a PMOS requires an N-type channel background, distinct, deep structural wells must be created on the single substrate.

### 1. N-Well Formation (Mask 2)
* Photoresist is applied and developed using **Mask 2** to shield the right (NMOS) region while exposing the left (PMOS) region.
* **Ion Implantation:** High-energy N-type dopants (typically **Phosphorus ($P$)**) are accelerated into the exposed substrate to generate the N-well pool.

### 2. P-Well Formation (Mask 3)
* The previous resist is stripped, reapplied, and patterned with **Mask 3** to protect the new N-well zone while exposing the NMOS region.
* **Ion Implantation:** High-energy ($\sim 200\text{keV}$) P-type dopant ions (typically **Boron ($B$)**) are implanted into the substrate to establish a distinct, localized P-well profile.

### 3. High-Temperature Drive-In
* The wafer is transferred to a furnace for a high-temperature thermal drive-in phase. This diffuses the implanted Phosphorus and Boron ions deeper and more uniformly into the substrate, finalizing the co-planar twin-well matrix.

---

## Step 3: Gate Terminal Formation (SKY_L3)
The gate terminal behaves as the electrostatic valve regulating current flow across the transistor channel. Its quality directly correlates to threshold voltage ($V_t$) uniformity.

### 1. Surface Preparation & Gate Oxide Growth
* All sacrificial oxide coatings are stripped away to reveal a pure, damage-free silicon surface layer.
* An ultra-clean, ultra-thin **Gate Oxide layer** ($\text{SiO}_2$) is thermally grown over the active regions. 

### 2. Polysilicon Layer Deposition
* A layer of **Polysilicon** (polycrystalline silicon) is deposited across the entirety of the wafer.
* To drastically lower electrical resistance, this polysilicon layer is heavily doped with N-type impurities, transforming it into a highly conductive gate terminal material.

### 3. Gate Patterning (Mask 4)
* Photoresist is coated and exposed via **Mask 4 (Gate Mask)** to map out the precise dimensions (channel length $L$ and width $W$) of the transistor gates.
* A highly selective dry chemical plasma etch cuts away the excess unshielded polysilicon. This leaves self-aligned, cleanly defined **Polysilicon Gate structures** positioned right above the gate oxide channel.

---

## Step 4: Lightly Doped Drain (LDD) Formation (SKY_L4)
As transistor physical feature dimensions scale downward into deep-submicron geometries, high electrical field spikes degrade device life and reliability. LDD processing injects a engineering profile to mitigate these short-channel limitations.

### 1. The Engineering Tradeoffs
* **Hot Carrier Effect (HCE):** Shrinking the channel length ($d$) while scaling voltages leaves a massive local electric field gradient ($E = \frac{V}{d}$). This field accelerates carriers to extreme kinetic speeds ("hot electrons"), which crack **Si-Si bonds** and embed inside the gate oxide, drifting device $V_t$.
* **Short Channel Effects (SCE):** Source/Drain depletion profiles naturally widen and clash, reducing the gate's capacity to firmly pinch off the subthreshold leakage current.

### 2. $N^-$ and $P^-$ Implantation (Masks 5 to 8)
* **NMOS LDD ($N^-$):** **Mask 8** shields the PMOS side, and a low-dose concentration of N-type ions (Phosphorus) is shot into the P-well. The polysilicon gate acts as a self-aligning shield.
* **PMOS LDD ($P^-$):** A corresponding configuration shields the NMOS side while a low-dose P-type concentration (Boron) is implanted into the N-well.
* This leaves shallow, lightly doped extension profiles that ease the voltage drop near the channel boundary.

### 3. Side-Wall Spacers
* A conformal film of Silicon Dioxide ($\text{SiO}_2$) or Silicon Nitride ($\text{Si}_3\text{N}_4$) is laid down over the top of the gates.
* An anisotropic (highly directional vertical) dry etch is deployed to shave the oxide flat off the horizontal surfaces, leaving behind wedge-shaped **side-wall spacers** running along the vertical edge columns of the polysilicon gates.

---

## Step 5: Source & Drain Formation (SKY_L5)
With the delicate LDD channel tips safely hidden underneath the protective side-wall spacers, the heavy bulk source and drain contact boundaries can be safely blasted into place.

### 1. Heavy $N^+$ Implantation (Mask 9)
* Photoresist is mapped using **Mask 9** to fully seal the PMOS N-well.
* High-dose, high-energy N-type dopants (typically **Arsenic**) are implanted into the P-well. 
* The side-wall spacers force these heavy ions to embed further outward from the gate edge, preserving the critical $N^-$ buffer zone underneath the spacer blocks and building a stepped junction profile.

### 2. Heavy $P^+$ Implantation (Mask 10)
* Photoresist is stripped and swapped using **Mask 10** to isolate the NMOS P-well.
* High-dose P-type dopant ions (typically **Boron**) are shot into the N-well to finish the PMOS source and drain regions.

### 3. Rapid Thermal Annealing (RTA)
* The heavy ion bombardment shatters the target silicon crystal lattice framework. 
* The wafer goes through an energetic RTA thermal anneal to mend the damaged crystal structures and force the newly implanted dopant atoms into active substitution positions inside the silicon grid.

---

## Step 6: Local Interconnect Formation (SKY_L6)
Before global aluminum or copper interconnect wiring lines can map out the logic networks, the raw active silicon faces must step through low-resistance structural interfaces.

### 1. Hydrofluoric (HF) Acid Strip
* The protective oxide screening sheets over the active areas are dissolved inside a selective **Hydrofluoric (HF) acid chemical wash**. 
* This exposes raw bare silicon exclusively over the source, drain, and polysilicon gate pads.

### 2. Silicidation (Salicide Process)
* A thin layer of a transition metal (such as Titanium, Cobalt, or Nickel) is uniformly sputtered across the surface.
* The wafer is heated, prompting a direct chemical reaction anywhere the metal contacts bare silicon/polysilicon, generating a low-resistance **Silicide** contact skin. Unreacted metal resting on oxide isolation zones is selectively stripped away.

### 3. Contact Windows (Mask 11)
* A thick, protective insulating blanket of Phosphosilicate Glass (PSG) or Inter-Level Dielectric (ILD) is deposited.
* Photoresist is patterned using **Mask 11** to map out access paths.
* A vertical dry plasma etch bores contact holes down through the oxide blanket until they terminate at the clean silicide surface pads.

### 4. Tungsten Contact Plugging
* The opened contact pipes are lined with a **Titanium Nitride (TiN)** adhesion barrier and filled with **Tungsten ($W$)**. 
* The surface is polished back to leave clean, flat Tungsten plugs touching the gates, sources, and drains.

---

## Step 7: Higher-Level Metal Formation (SKY_L7)
The final stage establishes the global multi-tier metal mesh layer required to bridge logic tracks, power routing networks, and ground lines across the layout blocks.

### 1. Inter-Metal Dielectric (IMD) & Planarization
* An isolating sheet of oxide material (IMD) is deposited over the newly constructed local tungsten nodes.
* **Chemical Mechanical Planarization (CMP):** The bumpy topography is polished back to a perfectly mirrored flat surface to prevent focus distortion in subsequent lithography steps.

### 2. Metal 1 (M1) Processing
* Vias are cut down through the polished IMD blanket layer to lock onto the tungsten plug nodes.
* A thin metallic contact coat is laid down, followed by a thick deposition of wiring metal (typically **Aluminum** or **Copper**).
* Photolithography and etching pattern this metal film into discrete horizontal signal lines, establishing the **Metal 1 (M1)** routing tier.

### 3. Multi-Level Metallization Stack Scaling
* The stack loops through this cycle (IMD deposition $\rightarrow$ CMP polish $\rightarrow$ Via mask etch $\rightarrow$ Metal fill $\rightarrow$ Track mask etch) to stack subsequent tracking layers (Metal 2, Metal 3, etc.).
* **Geometric Scaling Rules:**
  * **Lower-Tier Tracks (M1 - M2):** Maintained with thin, highly dense width parameters optimized for compact local signal routing inside standard cells.
  * **Upper-Tier Tracks (M3 and Above):** Fabricated with progressively **thicker and wider dimensions** to accommodate large current distributions safely ($\text{VDD}$ and $\text{VSS}$ planes) without triggering electromigration breakdowns.
