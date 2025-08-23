[gcode_macro PURGE_BLOB]
gcode:
    SAVE_GCODE_STATE NAME=PRIME_NOZZLE_STATE
    # FROM: https://gist.github.com/brandon3055/f5297f191fa576d2228e700ac6857abc
    # In order to use this you need to increase max_extrude_cross_section in your extruder config or klipper will complain. I set mine to 30
    {% set BEDWIDTH = printer.toolhead.axis_maximum.x|int %}

    # Set the width of your flexplate handle here
    {% set FLEXPLATE_HANDLE_WIDTH = 80|int %}
    # And if your flexplate is not centered, you can adjust that here
    {% set FLEXPLATE_HANDLE_LEFT_X = 0.5 * BEDWIDTH - 0.5 * FLEXPLATE_HANDLE_WIDTH %}

    {% set FLEXPLATE_HANDLE_CENTER_X = FLEXPLATE_HANDLE_LEFT_X + 0.5 * FLEXPLATE_HANDLE_WIDTH%}
    {% set PURGE_X_LOC = (FLEXPLATE_HANDLE_LEFT_X + ( printer.system_stats.cputime * 1000 ) % FLEXPLATE_HANDLE_WIDTH) %}           ; Generate pseudo random start pos so we dont wear out that one spot on the bed. 
    {% set PURGE_TRAVEL_DIR = (1 if PURGE_X_LOC < FLEXPLATE_HANDLE_CENTER_X else -1) %}

    RESPOND TYPE=echo MSG="Bed width        W={BEDWIDTH}mm"
    RESPOND TYPE=echo MSG="FlexPlate handle W={FLEXPLATE_HANDLE_WIDTH}mm starting @X={FLEXPLATE_HANDLE_LEFT_X}mm"
    RESPOND TYPE=echo MSG="Using purge loc  X={PURGE_X_LOC}mm, Traveling { 'LEFT' if PURGE_TRAVEL_DIR > 0 else 'RIGHT' }"

    M117 Purging at X{ PURGE_X_LOC }                                                       
    
    # The start pos will be within the "handle" of the flex plate where there is some extra Y space available so we can go all the way to Y0
    # If your setup does not have this extra space then just increase the y valie in the line "G1 X{ PURGE_X_LOC } Y0 Z1 F18000"
    G92 E0                          ; zero the extruder
    G90                             ; absolute positioning
    G1 X{ PURGE_X_LOC } Y0 Z1 F18000   ; Go to start pos for perge line
    G1 Z0.4 F600                    ; Lower to purge height
    G91                             ; relative positioning
    G1 X5 E40 F40                   ; Extrude blob of filament on the bed
    M106 P0                         ; Enable Cooling Fan
    G1 X20 Z5 F100                  ; Slow drag away from the blob with fans helping cool and break strings. Also raiz z zo the blob clears the fan duct
    G1 X{ 5*PURGE_TRAVEL_DIR }  Z-5.1 F9000              ; Now that the blob has cleared the duct we go back down for a short 0.3mm height extrusion
    G1 X{ 5*PURGE_TRAVEL_DIR } E2 F180                   ; Slow 5mm extrude move to help with stringing
    G1 X{ 30*PURGE_TRAVEL_DIR} E-1 F6000                ; Fast move and retract to break strings and reduce ooze
    G1 Z1 F100                      ; Lift
    M106 P0 S0                      ; Disable Cooling Fan
    G92 E0                          ; zero the extruder
    G90                             ; absolute positioning

    RESTORE_GCODE_STATE NAME=PRIME_NOZZLE_STATE