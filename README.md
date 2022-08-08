# openLANE

OpenLane facilitates the RTL2GDS flow using open source tools. The process design kit used here for the demonstration of the flow is skywater 130nm. The design picorv32a is used.


To invoke openlane from the command prompt: 
$ docker run -it -v $(pwd):/ openLANE_flow -v $ PDK_ROOT: $ PDK_ROOT -e PDK_ROOT = $ PDK_ROOT -u $ (id -u $ USER):$(id -g $ USER) openlane:current

The varient sky130_fd_sc_hd varient is used for the design of picorv32a. The varient sky130_fd_sc_hd consist of technology files- process corner timing files-tt,ss,ff.

$ ./flow.tcl -design 

The openlane runs the design automatically through the steps- synthesis, floorplanning, placement, CTS,PDN (power distribution network), routing. 
To run the openlane intractively- $ ./flow.tcl -interactive
It opens the openlane prompt %

![Opening OpenLANE_2](https://user-images.githubusercontent.com/55655753/183442071-6614564b-2f9d-45df-8d5b-1d5165fe8979.png)


The very first step in openlane is to import all packages.

A) To import packages- % package require openlane 0.9

The directory /home/work/tools/../openlane/designs/picorv32a contains the design of picorv32a. 

B) To setup the design in openlane 
% prep -design picorv32a 
This will merge the lef and tlef files to merged lef. The merged lef file is created in the picorv32a/runs/<date>/tmp directory
  
C) % run_synthesis 
  The design is converted to the netlist and .v file is created in the /picorv32a/runs/<date>/results/synthesis directory.
  
  ![SynthesisRun_1](https://user-images.githubusercontent.com/55655753/183442155-c2bacb55-06d8-47bf-b66c-4974d253f107.png)

  
  The flop ratio = (No of D FFs/ No. of Cells) =1613/14876=10.8429 
 
D) The floorplanning involves the setting up of die area,core area,aspect ratio, utilization factor, placing of I/Os, macro placements.

  % run_floorplan  --To run the floorplan
  The def file is created in the /picorv32a/runs/<date>/results/floorplan directory
  
  ![def_die_dimension](https://user-images.githubusercontent.com/55655753/183442440-10490ca5-801d-4731-8a02-2eeb76970d61.PNG)
  The die area-  Unit distance microns 1000
  (0 0) (660685  671405)
  It gives the area (660685/1000)*(671405/1000) Sq. Micrometer
  Magic tools is used for layout. To open magic : $ magic -T tech file 1  lef read file2  def file3 &
  
  file1: it is the tech file present in the pdks. the tech file location here is: 
  /pdks/sky130A/libs.techmagic/sky130A.tech
  
  file2: it is the (merged) lef file in the picorv32a/runs/<date>/tmp directory
  
  file3: def file created post floorplan in the floorplan directory in the - /picorv32a/runs/datedirectory/results/
  These three are the input files to generate layout in the magic
  
![OpenMagic](https://user-images.githubusercontent.com/55655753/183442749-123feb6c-958c-4bbf-87cf-785874c02693.PNG)

![magic layout_1](https://user-images.githubusercontent.com/55655753/183443060-3eecec59-2c87-4e72-987f-b3546bdaae56.PNG)

![magic layout](https://user-images.githubusercontent.com/55655753/183443068-2c81e6d8-6a21-46ce-aa32-4a32ea73c46f.PNG)

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
    
 ![postplacement magic invoke](https://user-images.githubusercontent.com/55655753/183443427-779c254f-44b9-4c99-9a97-94bd0236d220.PNG)

 To chek the layout in the magic:
 /placement $ magic -T <tech file> <lef file> <def>....(same as in the floorplan stage)
   
 ![postplacement layout](https://user-images.githubusercontent.com/55655753/183443595-6cef97f1-e466-443d-a284-9f0b1b7d658f.PNG)
  
Characterization Flow: Circuit Design -> Layout Design -> Characterization (timing,Noise,Power)
    
Inverter Characterization:
Layout of the inverter in the magic:
    
![Inverter on magic](https://user-images.githubusercontent.com/55655753/183443909-6b306b82-832c-489b-b9d5-b60af64dc27e.PNG)
    
![inverter layout](https://user-images.githubusercontent.com/55655753/183444035-8fcd9140-019b-4ac0-aa1e-4e5d16d1ea6f.PNG)

The first step in magic layout tool is to create a box defining the cell width and height.

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
   
   % extract all
    % ext2spice cthresh 0 rthresh 0
    %ext2spice
    
    
Inveter Spice deck:
Adding tran analysis in the spice deck
![spice deck_inveter](https://user-images.githubusercontent.com/55655753/183444299-47e732ea-4697-4363-8ab6-f2312e1f7738.PNG)

    
Running spice deck on ngspice:

ngspice-> plot y vs time a
    
![running spice file](https://user-images.githubusercontent.com/55655753/183444704-f3e89089-8775-4955-957b-e15881002795.PNG)

![inverter_output_ngspice](https://user-images.githubusercontent.com/55655753/183444487-c15b6cda-cd2f-410c-9b91-ea71894a5d57.PNG)

Timing analysis:
    
 1.in_rise_thr: 50% of the vdd of the rising edge of the input pulse=4.0049ns
 
 2.out_fall_thr:50% of the vdd of the falling edge of the output pulse=4.0132ns
 
 3.in_fall_thr: 50% of the vdd of the falling edge of the input pulse=2.067ns
 
 4.out_rise_thr:50% of the vdd of the rising edge of the output pulse=2.086ns
 
 The propagation time is the difference of the (1 and 2)= 83ps
   
    
 slew_low_rise_thr:2.06121ns
 
 slew_high_rise_thr:2.10548ns
 
 tr=44.27ps
 
 slew_low_fall_thr=4.01618ns
 
 slew_high_fall_thr=4.01161ns
 
 tf=4.57ps
    
![tf_high_inverter](https://user-images.githubusercontent.com/55655753/183445105-a0aa321c-c7f5-4265-be69-2d14de547f09.PNG)
![tf_low_inverter](https://user-images.githubusercontent.com/55655753/183445115-f99c8354-b577-47d7-9d6c-0d53930c8ed5.PNG)
![tr_inverter_high](https://user-images.githubusercontent.com/55655753/183445119-9d1c6add-74ce-47aa-89ba-21967ec16163.PNG)
![tr_inverter_low](https://user-images.githubusercontent.com/55655753/183445124-d9310874-ff29-4691-8cce-9da43c7cfe4e.PNG)
  
Converting labels to ports in inverter:

Press 'g' to activate grid on the layout

tkcon - % grid 0.46um 0.34um 0.23um 0.17um   setting up of the grid according to th etrack information available in track.info

select input 'A' in the layout where port is to be defined. Edit and select text to define port number and metal layer. The same is to be done for the output 'Y', power and ground terminal. 

Next step is to define the purpose of the ports defined.
Select the port and on tkcon execute %port class and % port use
  
![labeltoport_Y](https://user-images.githubusercontent.com/55655753/183453903-7297d636-a199-4972-a90c-749269eadbb2.PNG)
![inverter in defined grids](https://user-images.githubusercontent.com/55655753/183453889-7f0fd19e-b866-488a-a852-8fa4abb1c9c3.PNG)
  
CTS:
% run_cts
  
Enter into openroad: 
  
% openroad
  
% read lef i.e merged lef in /tmp
  
%read def  i.e obtained post cts
  
% write_db pico_cts.db  (db is created)
  
In openalen flow PDN, power distribution network is happened post cts
%gen_pdn

% run _routing  
    
    
    
    
    
    
    
    
    
  




