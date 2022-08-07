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
  
  The def file is created in the /picorv32a/runs/<date>/results/floorplan directory
  
  The die area-  Unit distance microns 1000
                 (0 0) (660685  671405)
  It gives the area (660685/1000)*(671405/1000) Sq. Micrometer
  
 Magic tools is used for layout. To open magic : $ magic -T <tech file 1> <lef read file2> <def file3> &
  
  <file1>: it is the tech file present in the pdks. the tech file location here is: /pdks/sky130A/libs.techmagic/sky130A.tech
  <file2>: it is the (merged) lef file in the picorv32a/runs/<date>/tmp directory
  <file3>: def file created post floorplan in the floorplan directory in the /picorv32a/runs/<date>/results/
    
  These three are the input files to generate layout in the magic
  
    In the magic layout: Press 'S' to select perticular object on the layout. 
    Tkcon opens parallely with magic layput window. The tkcon prompt facilitates various opeartion of the layout. 
    To zoom in press right mouse click followed by left mouse click to make a rectangle box surronding an area to zoom in. 
    To zoom out to normal view press v.
    
  E) Placement :
    Floorplan ensures I/O pin placing, dcaps whereas placement ensures the placement of standard cells. 
    There are two stages of the placement- A) Global B )Detailed  Placement
    Legalization happens in the detailed placement. Legallization ensures: 
    1. Standard cells are placed exacyly in the rows 
    2. Standard cells are placed very adjacent to each other 
    3. No overlapping between the standard cells.
    
    Run placement: % run_placement
    
    To chek the layout in the magic:
   /placement $ magic -T <tech file> <lef file> <def>....(same as in the floorplan stage)
    
    
    Characterization Flow: Circuit Design -> Layout Design -> Characterization (timing,Noise,Power)
    
    Inverter Characterization:
    
    Layout of the inverter in the magic: The first step inmagic layout tool is to create a box defining the cell width and height.
    on tkcon : % propoerty Fixed_BBox {0 0 138 272}
               % box     // to check dimensions
    PMOS layer :
    1. The licon is the contact between metal-I and locali layer
    2. nsubstratecontact is the contact between locali and Nwell
    
    NMOS layer:
    1.psubstratecontact is the contact between locali and psubstrate
    2. licon is the contact between Metal I and locali
    
    Spice extracted netlist of inverter::
    To extract the spice netlist from layput on tkcon execute following:
    % extrcat all
    %ets2spice cthresh 0 rthresh 0
    
    
    
    
    
    
    
    
    
    
  




