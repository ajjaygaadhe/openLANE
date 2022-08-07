# openLANE

OpenLane facilitates the RTL2GDS flow using open source tools. The process design kit used here for the demonstration of the flow is skywater 130nm. The design picorv32a is used.


To invoke openlane from the command prompt: 
$ docker run -it -v $(pwd):/ openLANE_flow -v $ PDK_ROOT: $ PDK_ROOT -e PDK_ROOT = $ PDK_ROOT -u $ (id -u $ USER):$(id -g $ USER) openlane:current

The varient sky130_fd_sc_hd varient is used for the design of picorv32a. The varient sky130_fd_sc_hd consist of technology files- process corner timing files-tt,ss,ff.

$ ./flow.tcl -design     The openlane runs the design automatically through the steps- synthesis, floorplanning, placement, CTS,PDN (power distribution network), routing. 
To run the openlane intractively- $ ./flow.tcl -interactive
It opens the openlane prompt %

The very first step in openlane is to import all packages.
A) To import packages- % package require openlane 0.9

The directory /home/work/tools/../openlane/designs/picorv32a contains the design of picorv32a. 

B) To setup the design in openlane 
% prep -design picorv32a 
This will merge the lef and tlef files to merged lef. The merged lef file is created in the picorv32a/runs/<date>/tmp directory
  
C) % run_synthesis 
  The design is converted to the netlist and .v file is created in the /picorv32a/runs/<date>/results/synthesis directory.
  
  The flop ratio = (No of D FFs/ No. of Cells) =1613/14876=10.8429 
 
D) The floorplanning involves the setting up of die area,core area,aspect ratio, utilization factor, placing of I/Os, macro placements.
  % run_floorplan  --To run the floorplan
  the def file is created in the /picorv32a/runs/<date>/results/floorplan directory




