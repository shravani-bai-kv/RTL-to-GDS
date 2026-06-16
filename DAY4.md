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

To view Fast lib file

<img width="1031" height="886" alt="image" src="https://github.com/user-attachments/assets/e4e8ba9c-c043-452f-b775-c8f8d81b6ada" />

### 5. Edit 'config.tcl' to change lib file and add the new extra lef into the openlane flow
Commands to be added to config.tcl to include our custom cell in the openlane flow

```bash
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```


Edited config.tcl to include the added lef and change library to ones we added in src directory
<img width="1105" height="920" alt="image" src="https://github.com/user-attachments/assets/9afb973c-86c9-40b3-966e-cac1ba86d5df" />

### 6. Run openlane flow synthesis with newly inserted custom inverter cell.
Commands to invoke the OpenLANE flow include new lef and perform synthesis
```bash
# Change directory to openlane flow directory
cd Desktop/work/tools/openlane_working_dir/openlane

# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
# Since we have aliased the long command to 'docker' we can invoke the OpenLANE flow docker sub-system by just running this command
docker
```
```bash
# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a

# Adiitional commands to include newly added lef to openlane flow
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```
<img width="1687" height="692" alt="image" src="https://github.com/user-attachments/assets/faea326e-9a89-41c0-9da0-9bb2c172875f" />
<img width="1655" height="607" alt="image" src="https://github.com/user-attachments/assets/a5b6f7c9-530d-4b66-8904-762858ad2eff" />
<img width="1866" height="945" alt="image" src="https://github.com/user-attachments/assets/a3f4640d-c7d8-4991-83a8-65b6595d7a63" />

### 7. Remove/reduce the newly introduced violations with the introduction of custom inverter cell by modifying design parameters.
Noting down current design values generated before modifying parameters to improve timing

<img width="1015" height="557" alt="image" src="https://github.com/user-attachments/assets/a4b26911-7edb-4406-89cf-59603de884c1" />
<img width="1007" height="557" alt="image" src="https://github.com/user-attachments/assets/94741540-49b6-4073-b924-e5184344a06b" />


Commands to view and change parameters to improve timing and run synthesis
```bash
# Now once again we have to prep design so as to update variables
prep -design picorv32a -tag 24-03_10-03 -overwrite

# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to display current value of variable SYNTH_STRATEGY
echo $::env(SYNTH_STRATEGY)

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to display current value of variable SYNTH_BUFFERING to check whether it's enabled
echo $::env(SYNTH_BUFFERING)

# Command to display current value of variable SYNTH_SIZING
echo $::env(SYNTH_SIZING)

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Command to display current value of variable SYNTH_DRIVING_CELL to check whether it's the proper cell or not
echo $::env(SYNTH_DRIVING_CELL)

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```

Screenshots of commands run
<img width="1616" height="945" alt="image" src="https://github.com/user-attachments/assets/46627418-29a0-41be-97af-67ec25debfb9" />

Comparing to previously noted run values area has increased and worst negative slack has become 0
<img width="937" height="385" alt="image" src="https://github.com/user-attachments/assets/6a554dce-da00-4c97-a730-36eab2f6d52e" />
<img width="1320" height="837" alt="image" src="https://github.com/user-attachments/assets/76290721-6d5a-4c1e-bbe6-430859d5b0b1" />

### 8. Once synthesis has accepted our custom inverter we can now run floorplan and placement and verify the cell is accepted in PnR flow.

