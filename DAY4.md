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
Now that our custom inverter is properly accepted in synthesis we can now run floorplan using following command
```bash
run_floorplan
```
<img width="1363" height="713" alt="image" src="https://github.com/user-attachments/assets/cb05e908-b220-4d45-af34-47501271758e" />
<img width="1048" height="502" alt="image" src="https://github.com/user-attachments/assets/300579e9-1198-48dc-8b08-0eebb0adb94a" />

Since we are facing unexpected un-explainable error while using run_floorplan command, we can instead use the following set of commands available based on information from Desktop/work/tools/openlane_working_dir/openlane/scripts/tcl_commands/floorplan.tcl and also based on Floorplan Commands section in Desktop/work/tools/openlane_working_dir/openlane/docs/source/OpenLANE_commands.md

```bash
# Follwing commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or
```

Screenshots of command run
<img width="1618" height="921" alt="image" src="https://github.com/user-attachments/assets/87819750-4a46-491a-b52b-c2cfc4e3854b" />
<img width="1587" height="512" alt="image" src="https://github.com/user-attachments/assets/a64f3e5e-08c6-4818-80df-133f7510c503" />

Now that floorplan is done we can do placement using following command
```bash
# Now we are ready to run placement
run_placement
```

Screenshots of command run
<img width="1451" height="365" alt="image" src="https://github.com/user-attachments/assets/1b559f61-6b0d-4898-a790-f4b49a15f7aa" />
<img width="1595" height="865" alt="image" src="https://github.com/user-attachments/assets/02e33566-b7c7-48e7-b61c-9e0835587227" />

Commands to load placement def in magic in another terminal

```bash
# Change directory to path containing generated placement def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/24-03_10-03/results/placement/

# Command to load the placement def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```
Screenshot of command run
<img width="1860" height="320" alt="image" src="https://github.com/user-attachments/assets/2afed4a0-97f4-42d8-baa9-d05e381bb435" />

Screenshot of placement def in magic
<img width="1460" height="737" alt="image" src="https://github.com/user-attachments/assets/d9f6a682-af25-436d-8ddc-cc44ca9436a0" />

Screenshot of custom inverter inserted in placement def with proper abutment
<img width="861" height="443" alt="image" src="https://github.com/user-attachments/assets/e4bb9352-12c8-45c2-b962-903c90967ed6" />

Command for tkcon window to view internal layers of cells
```bash
# Command to view internal connectivity layers
expand
```
<img width="1410" height="677" alt="image" src="https://github.com/user-attachments/assets/71fae6e8-c7dc-45cb-98cb-785f0280460d" />

Abutment of power pins with other cell from library clearly visible
<img width="830" height="597" alt="image" src="https://github.com/user-attachments/assets/dafa1925-4170-4579-ac2e-0f8a4ab07499" />

### 9. Do Post-Synthesis timing analysis with OpenSTA tool.
Since we are having 0 wns after improved timing run we are going to do timing analysis on initial run of synthesis which has lots of violations and no parameters were added to improve timing

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

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```
Commands run final screenshot
<img width="952" height="435" alt="image" src="https://github.com/user-attachments/assets/5aae5ebc-a832-4f33-a933-6c04eff13bb9" />

Newly created pre_sta.conf for STA analysis in openlane directory
<img width="1007" height="532" alt="image" src="https://github.com/user-attachments/assets/ed4b23e8-1109-470e-a689-0604cd96088a" />

Newly created my_base.sdc for STA analysis in openlane/designs/picorv32a/src directory based on the file openlane/scripts/base.sdc
<img width="1292" height="740" alt="image" src="https://github.com/user-attachments/assets/82da8a2b-6df8-4052-9077-f4c7a4c6dec9" />

```bash
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Command to invoke OpenSTA tool with script
sta pre_sta.conf
```
<img width="1290" height="648" alt="image" src="https://github.com/user-attachments/assets/bfd8447d-6900-45cc-a458-6be26b5e81c3" />
<img width="1283" height="730" alt="image" src="https://github.com/user-attachments/assets/c375dff7-166f-452b-8566-10143a722207" />

Since more fanout is causing more delay we can add parameter to reduce fanout and do synthesis again

Commands to include new lef and perform synthesis
```bash
# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a -tag 16-06_15-46 -overwrite

# Adiitional commands to include newly added lef to openlane flow
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Command to set new value for SYNTH_MAX_FANOUT
set ::env(SYNTH_MAX_FANOUT) 4

# Command to display current value of variable SYNTH_DRIVING_CELL to check whether it's the proper cell or not
echo $::env(SYNTH_DRIVING_CELL)

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```
Screenshot of running command
<img width="1300" height="710" alt="image" src="https://github.com/user-attachments/assets/2aca8ea3-97e4-486e-89b1-1f6bb5880bd2" />
Commands run final screenshot
<img width="1296" height="735" alt="image" src="https://github.com/user-attachments/assets/47e3db29-0951-4a1a-896e-4f6d87c89d19" />

Commands to run STA in another termina
```bash
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Command to invoke OpenSTA tool with script
sta pre_sta.conf
```
Screenshots of commands run
<img width="1292" height="682" alt="image" src="https://github.com/user-attachments/assets/b4315fc4-304f-4bd3-9839-ef09e32f1538" />
<img width="1296" height="737" alt="image" src="https://github.com/user-attachments/assets/92dcb3b1-b630-46e9-8bc5-618872d234c6" />

### 10. Make timing ECO fixes to remove all violations.
NOR gate of drive strength 2 is driving 5 fanouts
<img width="1286" height="487" alt="image" src="https://github.com/user-attachments/assets/729bced7-6e1b-4bbc-aaf3-c6f813b642f8" />

Commands to perform analysis and optimize timing by replacing with NOR gate of drive strength 5
```bash
# Reports all the connections to a net
report_net -connections _13285_

# Checking command syntax
help replace_cell

# Replacing cell
replace_cell _16145_ sky130_fd_sc_hd__nor3_2

# Generating custom timing report
report_checks -fields {net cap slew input_pins} -digits 4
```
Screenshot of command run
<img width="658" height="387" alt="image" src="https://github.com/user-attachments/assets/65ec3790-3536-4f86-98f6-1ff800515ebe" />

Before slew
<img width="1077" height="496" alt="image" src="https://github.com/user-attachments/assets/4aa4c338-0a3d-43de-b28a-2b1dd1ace770" />
After slew
(it reduced a little bit if we change other one it may still reduce)
<img width="1247" height="311" alt="image" src="https://github.com/user-attachments/assets/a512d093-5158-417b-becb-7805eedc219a" />

Commands to perform analysis and optimize timing by replacing
```bash
# Reports all the connections to a net
report_net -connections _11643_

# Replacing cell
replace_cell _14481_ sky130_fd_sc_hd__or4_4

# Generating custom timing report
report_checks -fields {net cap slew input_pins} -digits 4
```

<img width="1236" height="917" alt="image" src="https://github.com/user-attachments/assets/9e38fb7f-c9d9-4f39-8611-a40c9bd54f89" />
<img width="982" height="826" alt="image" src="https://github.com/user-attachments/assets/c094f8a8-dbe8-43ba-8d86-cb1d201626bd" />

Result - slack reduced
<img width="1247" height="546" alt="image" src="https://github.com/user-attachments/assets/dde32fc8-7dee-4fec-834a-d69b31b29e42" />
<img width="1617" height="942" alt="image" src="https://github.com/user-attachments/assets/60af7842-5e05-40f0-8505-e7f51674aaaf" />

Commands to verify instance is replaced or no
```bash
# Generating custom timing report
report_checks -from _29043_ -to _30440_ -through _14506_
```
<img width="1127" height="837" alt="image" src="https://github.com/user-attachments/assets/4626312f-3b07-4faf-ae9e-06143062b9d4" />
We started ECO fixes at wns -5.5905 and now we stand at wns -5.1920 we reduced around 0.3985 ns of violation

### 11. Replace the old netlist with the new netlist generated after timing ECO fix and implement the floorplan, placement and cts
Now to insert this updated netlist to PnR flow and we can use write_verilog and overwrite the synthesis netlist but before that we are going to make a copy of the old old netlist
Commands to make copy of netlist
```bash
# Change from home directory to synthesis results directory
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-03_18-52/results/synthesis/

# List contents of the directory
ls

# Copy and rename the netlist
cp picorv32a.synthesis.v picorv32a.synthesis_old.v

# List contents of the directory
ls
```
Screenshot of commands run
<img width="1613" height="517" alt="image" src="https://github.com/user-attachments/assets/c2054ccc-eb83-4d88-967b-4addc79fac53" />

Commands to write verilog
```bash
# Check syntax
help write_verilog

# Overwriting current synthesis netlist
write_verilog /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-03_18-52/results/synthesis/picorv32a.synthesis.v

# Exit from OpenSTA since timing analysis is done
exit
```
Screenshot of commands run
<img width="1452" height="448" alt="image" src="https://github.com/user-attachments/assets/153f2642-0529-46a4-b4ec-5b79e91710b9" />
Verified that the netlist is overwritten by checking that instance is present or not
<img width="677" height="861" alt="image" src="https://github.com/user-attachments/assets/a357d23c-5986-4b42-a16d-288a25641fc5" />

Since we confirmed that netlist is replaced and will be loaded in PnR but since we want to follow up on the earlier 0 violation design we are continuing with the clean design to further stages
Commands load the design and run necessary stages
```bash
# Now once again we have to prep design so as to update variables
prep -design picorv32a -tag 16-06_15-46 -overwrite

# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis

# Follwing commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or

# Now we are ready to run placement
run_placement

# Incase getting error
unset ::env(LIB_CTS)

# With placement done we are now ready to run CTS
run_cts
```
<img width="1122" height="366" alt="image" src="https://github.com/user-attachments/assets/9bf8e3c0-bd0e-4987-81ba-3bc77f0b2711" />
<img width="975" height="380" alt="image" src="https://github.com/user-attachments/assets/45251530-5a3e-4079-a0a7-0f6b2675b9b3" />
<img width="1187" height="655" alt="image" src="https://github.com/user-attachments/assets/b4f2d6cf-5cba-4f59-bfa7-6eafb5d97bc2" />
<img width="1612" height="602" alt="image" src="https://github.com/user-attachments/assets/86c25341-2b35-46ce-aa0d-7466e9a4db84" />
<img width="1623" height="622" alt="image" src="https://github.com/user-attachments/assets/2c5014bc-0f63-4fb7-bdb9-efc16b76e812" />
<img width="1543" height="622" alt="image" src="https://github.com/user-attachments/assets/7904b5a6-7600-4a9e-865e-96ea8646a657" />
<img width="721" height="397" alt="image" src="https://github.com/user-attachments/assets/34ce07e9-deea-4422-a631-2943f1f6b27b" />
<img width="1376" height="552" alt="image" src="https://github.com/user-attachments/assets/6f7ee0af-5cb2-456e-b552-e45fb7b9b789" />

### 12. Post-CTS OpenROAD timing analysis.
Commands to be run in OpenLANE flow to do OpenROAD timing analysis with integrated OpenSTA in OpenROAD
```bash
# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/24-03_10-03/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/24-03_10-03/results/cts/picorv32a.cts.def

# Creating an OpenROAD database to work with
write_db pico_cts.db

# Loading the created database in OpenROAD
read_db pico_cts.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/24-03_10-03/results/synthesis/picorv32a.synthesis_cts.v

# Read library for design
read_liberty $::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Read in the custom sdc we created
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Setting all cloks as propagated clocks
set_propagated_clock [all_clocks]

# Check syntax of 'report_checks' command
help report_checks

# Generating custom timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4

# Exit to OpenLANE flow
exit
```
<img width="1617" height="771" alt="image" src="https://github.com/user-attachments/assets/6da23801-72cc-451c-9b31-78225688b23a" />
<img width="1606" height="772" alt="image" src="https://github.com/user-attachments/assets/086de9ea-3f07-47a8-a7d3-9b57fef9148a" />
<img width="1598" height="767" alt="image" src="https://github.com/user-attachments/assets/21b91391-749c-4202-b403-053ce7f1464d" />
<img width="1605" height="767" alt="image" src="https://github.com/user-attachments/assets/8bb2565d-75e3-4d87-a390-ef89c841e2a0" />

### 13. Explore post-CTS OpenROAD timing analysis by removing 'sky130_fd_sc_hd__clkbuf_1' cell from clock buffer list variable 'CTS_CLK_BUFFER_LIST'.
Commands to be run in OpenLANE flow to do OpenROAD timing analysis after changing CTS_CLK_BUFFER_LIST
```bash
# Checking current value of 'CTS_CLK_BUFFER_LIST'
echo $::env(CTS_CLK_BUFFER_LIST)

# Removing 'sky130_fd_sc_hd__clkbuf_1' from the list
set ::env(CTS_CLK_BUFFER_LIST) [lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0]

# Checking current value of 'CTS_CLK_BUFFER_LIST'
echo $::env(CTS_CLK_BUFFER_LIST)

# Checking current value of 'CURRENT_DEF'
echo $::env(CURRENT_DEF)

# Setting def as placement def
set ::env(CURRENT_DEF) /openLANE_flow/designs/picorv32a/runs/24-03_10-03/results/placement/picorv32a.placement.def

# Run CTS again
run_cts

# Checking current value of 'CTS_CLK_BUFFER_LIST'
echo $::env(CTS_CLK_BUFFER_LIST)

# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/24-03_10-03/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/24-03_10-03/results/cts/picorv32a.cts.def

# Creating an OpenROAD database to work with
write_db pico_cts1.db

# Loading the created database in OpenROAD
read_db pico_cts.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/24-03_10-03/results/synthesis/picorv32a.synthesis_cts.v

# Read library for design
read_liberty $::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Read in the custom sdc we created
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Setting all cloks as propagated clocks
set_propagated_clock [all_clocks]

# Generating custom timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4

# Report hold skew
report_clock_skew -hold

# Report setup skew
report_clock_skew -setup

# Exit to OpenLANE flow
exit

# Checking current value of 'CTS_CLK_BUFFER_LIST'
echo $::env(CTS_CLK_BUFFER_LIST)

# Inserting 'sky130_fd_sc_hd__clkbuf_1' to first index of list
set ::env(CTS_CLK_BUFFER_LIST) [linsert $::env(CTS_CLK_BUFFER_LIST) 0 sky130_fd_sc_hd__clkbuf_1]

# Checking current value of 'CTS_CLK_BUFFER_LIST'
echo $::env(CTS_CLK_BUFFER_LIST)
```
<img width="1017" height="541" alt="image" src="https://github.com/user-attachments/assets/e3177339-5424-4d70-ba6a-0abe7cb8747f" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/52247d1e-c501-4f74-832c-c204e9371a69" />






