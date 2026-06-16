# Pre-layout timing analysis and importance of good clock tree
Theory
Implementation
Day 4 tasks:-
1. Fix up small DRC errors and verify the design is ready to be inserted into our flow.
2. Save the finalized layout with custom name and open it.
3.Generate lef from the layout.
4. Copy the newly generated lef and associated required lib files to 'picorv32a' design 'src' directory.
5. Edit 'config.tcl' to change lib file and add the new extra lef into the openlane flow.
6. Run openlane flow synthesis with newly inserted custom inverter cell.
7. Remove/reduce the newly introduced violations with the introduction of custom inverter cell by modifying design parameters.
8. Once synthesis has accepted our custom inverter we can now run floorplan and placement and verify the cell is accepted in PnR flow.
9. Do Post-Synthesis timing analysis with OpenSTA tool.
10. Make timing ECO fixes to remove all violations.
11. Replace the old netlist with the new netlist generated after timing ECO fix and implement the floorplan, placement and cts.
12. Post-CTS OpenROAD timing analysis.
13. Explore post-CTS OpenROAD timing analysis by removing 'sky130_fd_sc_hd__clkbuf_1' cell from clock buffer list variable 'CTS_CLK_BUFFER_LIST'.

### 1. Fix up small DRC errors and verify the design is ready to be inserted into our flow.
Conditions to be verified before moving forward with custom designed cell layout:

Condition 1: The input and output ports of the standard cell should lie on the intersection of the vertical and horizontal tracks.
Condition 2: Width of the standard cell should be odd multiples of the horizontal track pitch.
Condition 3: Height of the standard cell should be even multiples of the vertical track pitch.

Commands to open the custom inverter layout
```bash
# Change directory to vsdstdcelldesign
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign

# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
```
Screenshot of tracks.info of sky130_fd_sc_hd

<img width="261" height="270" alt="image" src="https://github.com/user-attachments/assets/27f26540-b553-4ac8-a289-3c9c77421c50" />

Commands for tkcon window to set grid as tracks of locali layer
```bash
# Get syntax for grid command
help grid

# Set grid values accordingly
grid 0.46um 0.34um 0.23um 0.17um
```
Screenshot of commands run
before setting the grid values
<img width="1437" height="733" alt="image" src="https://github.com/user-attachments/assets/188421c0-cfa9-4f52-8798-246b40aba1e2" />
after setting the grid value
<img width="1513" height="738" alt="image" src="https://github.com/user-attachments/assets/ac70a468-5a0e-47f9-bdd5-a4516d2f924e" />
Condition 1 verified
<img width="1463" height="842" alt="image" src="https://github.com/user-attachments/assets/3597aa03-f87a-438d-b491-d7e459fbc640" />
Condition 2 verified
<img width="1571" height="737" alt="image" src="https://github.com/user-attachments/assets/77b844f3-c7d4-4138-889d-ad0217f28cb7" />

### 2. Save the finalized layout with custom name and open it.
Command for tkcon window to save the layout with custom name
```bash
# Command to save as
save sky130_vsdinv.mag
```
Command to open the newly saved layout
```bash
# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_vsdinv.mag &
```

<img width="981" height="337" alt="image" src="https://github.com/user-attachments/assets/9f286393-6351-43cf-a609-9b00b69bd5e8" />
<img width="1272" height="262" alt="image" src="https://github.com/user-attachments/assets/daf20c88-6546-4d88-a959-ea68ebe0c436" />

### 3. Generate lef from the layout.
Command for tkcon window to write lef
```bash
# lef command
lef write
```
Screenshot of command run
<img width="1786" height="736" alt="image" src="https://github.com/user-attachments/assets/f0b1079b-ef84-4bcf-8eca-40bb048d62e0" />
Screenshot of newly created lef file
<img width="1366" height="945" alt="image" src="https://github.com/user-attachments/assets/147a1e42-f8b4-4399-a738-489b5d3b0390" />

### 4. Copy the newly generated lef and associated required lib files to 'picorv32a' design 'src' directory.
Commands to copy necessary files to 'picorv32a' design 'src' directory
```bash
# Copy lef file
cp sky130_vsdinv.lef ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# Copy lib files
cp libs/sky130_fd_sc_hd__* ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
```
Screenshot of commands run
<img width="1743" height="152" alt="image" src="https://github.com/user-attachments/assets/72dd1168-7bda-4cd6-804b-b176ab21115a" />

To view Typical lib file 
<img width="1018" height="895" alt="image" src="https://github.com/user-attachments/assets/26d04825-b9a2-4bb3-addc-553b471e25d4" />

To view Slow lib file
<img width="852" height="907" alt="image" src="https://github.com/user-attachments/assets/2646fa17-d3d1-4245-b2f0-516692e85dc4" />

To view Fasr lib file
<img width="1031" height="886" alt="image" src="https://github.com/user-attachments/assets/e4e8ba9c-c043-452f-b775-c8f8d81b6ada" />

