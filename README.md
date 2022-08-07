# openLANE

OpenLane facilitates the RTL2GDS flow using open source tools. The process design kit used here for the demonstration of the flow is skywater 130nm. The design picorv32a is used.


To invoke openlane from the command prompt: 
$docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) openlane:current

The varient sky130_fd_sc_hd varient is used for the design of picorv32a. The varient sky130_fd_sc_hd consist of technology files- process corner timing files-tt,ss,ff.

$ ./flow.tcl -design     The openlane runs the design automatically through the steps- synthesis, floorplanning, placement, CTS,PDN (power distribution network), routing. 
To run the openlane intractively- $ ./flow.tcl -interactive
