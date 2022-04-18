This is a short set of recommendations and requirements for setting up klicky and calibrate_z scripts.

This uses the following 3 sets of macros: 
https://github.com/jlas1/Klicky-Probe 
https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/edwardyeeks/Decontaminator_Purge_Bucket_&_Nozzle_Scrubber
https://github.com/protoloft/klipper_z_calibration

1. You must enable and correctly set relative_reference_index under [bed mesh] section in printer.cfg. This is calculated by ((X sample points * Y sample points) -1) /2  EG for 5x5 grid, use 12, or for 7x7 grid, use 24.
2. You must not have load_gcode_state or G28 or G28 Z in your print_start macro *after* calibrate_z or it will overwrite your offset settings.
3. You must not have clear_bed_mesh in your print_end script unless you also have print_start either loading or generating a new mesh each time. 

The order of operations in your print_start should be as follows:
```
[gcode_macro PRINT_START]
#   Use PRINT_START for the slicer starting script
gcode:
  G90 #set absolute positioning
  G28 #home all axis
  Attach_Probe_Lock #prevent probe docking until unlocked https://github.com/jlas1/Klicky-Probe
  Z_TILT_ADJUST #Trident *or* 
  QUAD_GANTRY_LEVEL #V2.4
  CLEAN_NOZZLE #requires brush/purge bucket and Decontaminator macros
  G28 Z #rehome Z axis 
  CALIBRATE_Z #automatic Z offset  
  BED_MESH_PROFILE LOAD=default #load saved mesh *or*
  BED_MESH_CALIBRATE #generate new mesh
  Dock_Probe_Unlock #removes probe lock
```
 You can add heatup/wait functions and additional nozzle cleaning/G28 Z if you wish but do them *before* Calibrate_Z.  