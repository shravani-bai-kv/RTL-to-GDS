# Day 5 Final steps for RTL2GDS using tritonRoute and openSTA

Theory
Implementation
Section 5 tasks:-
Perform generation of Power Distribution Network (PDN) and explore the PDN layout.
Perfrom detailed routing using TritonRoute.
Post-Route parasitic extraction using SPEF extractor.
Post-Route OpenSTA timing analysis with the extracted parasitics of the route.
All section 5 logs, reports and results can be found in following run folder:

### 1. Perform generation of Power Distribution Network (PDN) and explore the PDN layout.
Commands to perform all necessary stages up until now
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

# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis

# Following commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or

# Now we are ready to run placement
run_placement

# Incase getting error
unset ::env(LIB_CTS)

# With placement done we are now ready to run CTS
run_cts

# Now that CTS is done we can do power distribution network
gen_pdn
```
Screenshots of power distribution network run
<img width="1007" height="541" alt="image" src="https://github.com/user-attachments/assets/b8092660-1220-4e93-ad15-74270e2b98c8" />
<img width="1011" height="541" alt="image" src="https://github.com/user-attachments/assets/2bc00d49-7359-477a-93cf-d9962cdb83db" />
Commands to load PDN def in magic in another terminal
```bash
# Change directory to path containing generated PDN def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/tmp/floorplan/

# Command to load the PDN def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read 14-pdn.def &
```
Screenshots of PDN def
<img width="980" height="497" alt="image" src="https://github.com/user-attachments/assets/e934e00c-7bd3-4a41-9d90-336de20dbb67" />
<img width="995" height="535" alt="image" src="https://github.com/user-attachments/assets/85ced065-62d4-4ceb-b341-f0e61b0c385b" />
<img width="1012" height="542" alt="image" src="https://github.com/user-attachments/assets/4efd7e63-1e9b-494f-9477-06aed86dc0e7" />

### 2. Perfrom detailed routing using TritonRoute and explore the routed layout.
Command to perform routing
```bash
# Check value of 'CURRENT_DEF'
echo $::env(CURRENT_DEF)

# Check value of 'ROUTING_STRATEGY'
echo $::env(ROUTING_STRATEGY)

# Command for detailed route using TritonRoute
run_routing
```
Screenshots of routing run
<img width="1011" height="541" alt="image" src="https://github.com/user-attachments/assets/d7f2bb90-ab3a-4f63-9591-5675721486f7" />
<img width="1012" height="541" alt="image" src="https://github.com/user-attachments/assets/63697540-dcc9-4462-9392-52cbe1bcb38b" />
<img width="1012" height="540" alt="image" src="https://github.com/user-attachments/assets/b7c36efc-ddc4-4e40-862d-758c96fca249" />

Commands to load routed def in magic in another terminal
```bash
# Change directory to path containing routed def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/results/routing/

# Command to load the routed def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```

Screenshots of routed def
<img width="986" height="526" alt="image" src="https://github.com/user-attachments/assets/4aa311af-7f0a-4700-b7c3-1b3c99532738" />
<img width="1006" height="545" alt="image" src="https://github.com/user-attachments/assets/9b354787-f6a4-4e82-b008-70c618692238" />
<img width="992" height="535" alt="image" src="https://github.com/user-attachments/assets/33512aa5-65a7-4405-90a4-7028aabda6e2" />
<img width="992" height="526" alt="image" src="https://github.com/user-attachments/assets/07db3f4d-d3c5-480e-a88a-c7a8a93772ff" />

Screenshot of fast route guide present in openlane/designs/picorv32a/runs/26-03_08-45/tmp/routing directory
<img width="711" height="538" alt="image" src="https://github.com/user-attachments/assets/76848d7d-eb60-4b95-9b5c-bfc6ed37c2b6" />
### 3. Post-Route parasitic extraction using SPEF extractor.
Commands for SPEF extraction using external tool
```bash
# Change directory
cd Desktop/work/tools/SPEF_EXTRACTOR

# Command extract spef
python3 main.py /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/tmp/merged.lef /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/results/routing/picorv32a.def
```
### 4. Post-Route OpenSTA timing analysis with the extracted parasitics of the route.
Commands to be run in OpenLANE flow to do OpenROAD timing analysis with integrated OpenSTA in OpenROAD
```bash
# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/26-03_08-45/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/26-03_08-45/results/routing/picorv32a.def

# Creating an OpenROAD database to work with
write_db pico_route.db

# Loading the created database in OpenROAD
read_db pico_route.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/26-03_08-45/results/synthesis/picorv32a.synthesis_preroute.v

# Read library for design
read_liberty $::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Read in the custom sdc we created
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Setting all cloks as propagated clocks
set_propagated_clock [all_clocks]

# Read SPEF
read_spef /openLANE_flow/designs/picorv32a/runs/26-03_08-45/results/routing/picorv32a.spef

# Generating custom timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4

# Exit to OpenLANE flow
exit
```

Screenshots of commands run and timing report generated

<img width="1011" height="546" alt="image" src="https://github.com/user-attachments/assets/a13e1161-535e-4359-a532-850f7a84bc62" />
<img width="1012" height="540" alt="image" src="https://github.com/user-attachments/assets/4209d49b-7a8e-4b6b-8e4b-7bde82576a3e" />
<img width="1013" height="632" alt="image" src="https://github.com/user-attachments/assets/e662552c-6ec4-4554-acf1-ae0e9853c6fd" />
<img width="1001" height="537" alt="image" src="https://github.com/user-attachments/assets/7fbd58d2-dff9-47a4-9ee1-96144e2bf4ac" />






