Design Files
############

.. csv-table::
    :file: design_files.csv
    :widths: 25,25,25,25
    :header-rows: 1

**skimmer_clamp_M3**
    Print-in-place skimmer nozzle clamping mechanism. Fixed atop a drilled Nunc
    OmniTray lid to allow precise positioning of the skimmer nozzle tip height.
    CAD files include modelled M3 fastener hardware as assembled.
**perfusion_lid_drill_jig**
    A guide for marking hole locations on Nunc® OmniTray® lids for drilling.
    Slides over top of the lid with holes for a fine-tipped marker.
**petri_drill_jig_<size>_<inlet/outlet>**
    As above, but for alternative petri dish culture chambers. Two pieces, inlet
    and outlet sides, that slide together to accommodate variability in petri
    dish dimension between manufacturers. 
**valve_clip_base**
    Simple stand for the BOM pinch valve clip. Holds the valve horizontal for
    use on e.g. an incubator shelf.
**skimmer_height_block_<##>mm** 
    Blocks of defined heights, <##>: 1.2, 1.4,1.6, and 1.8 mm. Placed under the
    skimmer nozzle before tightening the skimmer clamp for repeatable height
    setting.
**fetbox_complete** 
    CAD model of the FETbox hardware controller with PCB and key electronic
    components modelled as assembled.
**fetbox_enclosure_base** 
    Bottom half of the FETbox enclosure, into the which the PCB, power inlet,
    and MOSFET output jacks are mounted.
**fetbox_enclosure_lid** 
    the top cover of the FETbox enclosure. Has ventilation for the output 
    MOSFETs and Arduino. It snap-fits onto the fetbox_enclosure_base.
**FETbox_rev0_Gerber_JLCPCB** 
    Compressed (.zip) manufacturing Gerber (CAM)files. Ready for upload to the
    JLCPCB website for PCB ordering.
**FETbox_rev0_PCB** 
    Autodesk EAGLE PCB source files for the FETbox_rev0_PCB. SCH and BRD files
    are co-dependant and must be downloaded together in order to open/modify the
    design.
**Firmware_FETbox** 
    Arduino “sketch”/firmware for the Arduino Nano developmentboard used in the
    FETbox hardware controller. Opened with Arduino IDE software
**plateflo** 
    A Python package containing modules to simplify coding serial control of the
    FETbox and Ismatec Reglo peristaltic pumps. 
    
    See :doc:`/software/fetbox`,
    :doc:`/software/scheduler`, and more generally
    :doc:`/software/API/api_index` for more tutorials and usage usage.  