Build Guide
###########

1. 3D Printing
^^^^^^^^^^^^^^

Parts are designed and oriented optimally for fused deposition modeling (FDM) 3D printing. Parts pictured in proceeding sections were printed on a Prusa i3 MK3S 3D printer in “Galaxy Black” Prusament PLA. 

.. sidebar:: Print Settings

    Unless otherwise noted, the following print settings can be used as a general guideline when slicing the included design files for printing:

    * 0.4 mm extrusion width
    * 0.2 mm layer height
    * 3 perimeters; 5 top and bottom layers
    * 20% infill
    * No supports

    Parts that warrant special print considerations are discussed in the following sections.

5.1.1	Hardware Controller Enclosure

Depending on several printer-specific conditions including e.g. parting cooling, print speed, and material properties it may be necessary to clean up the overhang and bridge areas indicated in [FIGURE]. 

5.1.2	Skimmer Nozzle Clamp

This part was modelled with a print-in-place sliding mechanism for clamping the P200 tip in place without damaging it with the sharp surface of the bolt face and thread start. The clamp slide should break free with minimal effort and then slide freely after using the method described in 5.3.1 – Skimmer Nozzle Mount.
Because of the small size of this part, printing just one at a time can lead to overheating artifacts. Print at least two or three at once while optimizing print settings.
Most popular slicing software have the option to reduce the material flowrate on un-supported extrusions. The first layers of the underside of the clamp benefit from this feature. Using PrusaSlicer 2.2.0 [1] we had success with a bridge flow ratio of 0.7, for example. It may also be advantageous to use a 0.1 mm layer height for this part as less material is deposited at once and is less likely to adhere irreversibly unintended layers below.

5.1.3	Skimmer Nozzle Height Block

The thickness of the nozzle height setting blocks (and consequently the skimmer nozzle height) is particularly sensitive to first layer height calibration and it is recommended that the printed height be validated with a micrometer, high quality calipers, or more empirically by measuring the residual plate volume as was done in [FIGURE].

1. Perfusion Hardware Controller
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

a. PCB Ordering
===============

The hardware controller printed circuit board (PCB) was designed with
professional manufacture in mind and is not necessarily optimized for e.g. DIY
milling/etching. There are many services available for small-run prototype PCB
production, making it feasible to order several bare PCBs at an affordable rate.
At the time of writing, JLCPCB (China)[2] offers such a service and provided the
boards pictured herein. We opted for a black PCB and ENIG-RoHS surface finish;
however, these are optional and primarily cosmetic options. The included Gerber
computer aided manufacturing (CAM) files were generated according to JLCPCB’s
capabilities [3] via Autodesk EAGLE[4] using oxullo’s helper files [5], [6].
Similarly, controller_rev0_PCB.brd can be used to generate CAM files for other
PCB manufacturers using appropriate specifications (usually provided in a *.cam
file) and EAGLE design software.

To place an order with JLCPCB, upload controller_rev0_Gerber_JLCPCB.zip and
select the PCB colour and surface finish as desired. The board dimensions will
be derived automatically from the Gerber files. Other options can be left in
their default state: 2 layers, 1 design, single PCB delivery format, 1.6mm
thickness, 1 oz copper weight, no gold fingers, no production file confirmation,
fully tested flying probe, no castellated holes, no order number removal.

b. PCB Assembly
===============

Tips
----

For ease of assembly, it is recommended to solder diodes and resistors to the
PCB prior to the output jacks and MOSFETS as the shorter components are more
difficult to access once the taller MOSFETs and output jacks are mounted. 

Axial component leads need to be bent 90° prior to soldering, this can be done with
any pair of plyers, however a 3D-printed jig such as
https://www.thingiverse.com/thing:26025 can make the task less finicky. Hole
spacing on all diodes and resistors on the PCB are 0.4”/10.16 mm.

Instructions
------------

1.	Solder the fly-back diodes (D1-D5) to the board.

2.	Solder the 10kΩ pull-down resistors (R2, R4, R6, R8, R10) to the board.

3.	If current-limiting gate resistors are desired, through-holes are included
        for these in the odd-numbered resistor positions (R1, R3, etc.). NB:
        otherwise, the central pads at these positions must be bridged with
        solder.

4.	Solder the 3.5mm output jacks to the PCB. Note that due to the mass of
        copper around these solder pads conducting heat away, it may be
        necessary to
        increase the soldering iron temperature to make a proper joint here.

5.	Solder the MOSFETs to the PCB. As in the previous step, some of the MOSFETs
        pads will require more heat to make a proper joint here.

6.	Trim 2×30 pin strips from the female headers using the side cutters.

7.	Socket the Arduino Nano into the trimmed header strips before soldering the
        female headers to the board. This is to ensure proper alignment and easy
        insertion should the Arduino need to be removed or replaced in future.
        The Arduino can be removed after 1 or 2 pins from each header are
        securely soldered to the PCB.

8.	Solder the filter capacitor (C1) to the power input of board.

9.	Trim excess leads from the bottom of the board using the side cutters if you
        have not done so already.

10.	Cut 2×2-3 cm of >18AWG wire for the 12V DC input and strip a few millimeters
        from each end.

11.	Solder one end of each wire to the barrel and center pin tabs of the DC
        jack, apply heat shrink tubing to the tabs if available.

12.	Solder the DC jack center pin wire to one of the +12V solder pads at the
        power input. Likewise, for the barrel wire to one of the GND pads.
        Additional pads, connected in parallel, are provided in case one wishes
        to power additional devices from the board input.

13.	Bend the wires into a gentle loop to permit the DC jack to align with the
        enclosure mounting hole.

Expandability & Customization

All Arduino pins are broken-out onto the board adjacent to the female headers to facilitate easy modification and/or expansion of the hardware controller functionality. 
There are also 40 numbered, unconnected, 0.1” pitch solder pads provided along the bottom of the board for e.g. additional connectors, sensors, switches, MOSFETs, stepper drivers, etc. The +12V input and GND are routed next to these as well as the regulated +5V and +3.3V pins from the Arduino Nano for convenience. NB: observe the current limitations for these onboard supply lines, ~500 mA and 150 mA respectively.

5.2.3	Final Assembly
1.	Using an M3×8 bolt, thread all four standoffs on the inside of the bottom half of the enclosure by driving the bolt in then out, one at a time. There will be significant resistance as the bolt cuts a thread into the printed plastic. NB: do not tighten once the bolt head sits flush.
2.	Ensure there is adequate clearance for the M3 bolt to pass through the PCB mounting holes of the PCB. Machining tolerances can vary with manufacturer and a quick pass with a 3 mm drill bit or with the M3 bolt itself might be necessary.
3.	Unscrew all nuts and remove any washers from the DC jack and controller output jacks.
4.	Socket the Arduino Nano into the controller board with its USB port oriented as printed on the PCB silkscreen.
5.	Insert the board at an angle into the mounting holes of the enclosure. The board will sit flat with the base of the enclosure once these are through.
6.	Secure the PCB to the enclosure bottom using four M3×8 bolts. NB: do not over-tighten, it is easy to strip the plastic threads in the enclosure bottom.
7.	Re-install the washers and nuts for the DC input jack and controller output jacks. Do not overtighten the nuts on the output jacks.
8.	Snap the enclosure lid in place with the convective cooling slots over the MOSFET array.

5.2.4	Firmware Upload
The firmware for hardware controller board is supplied as an ‘sketch’ for upload via the Arduino IDE software.
1.	Install the Arduino desktop IDE software and USB drivers per the instructions for your system [7].
2.	Connect the hardware controller using a USB mini-B cable.
3.	Open the hardware controller sketch, hardware_controller_0.ino with Arduino IDE.
4.	Set the target board, processor and serial port:
Tools -> Board -> Arduino Nano
Tools -> Processor -> ATmega328P
Tools -> Port -> [Arduino Nano port]
The Tools -> Get Board Info option can sometimes set these automatically.

5.	Upload the sketch to the hardware controller Arduino:
Sketch -> Upload

6.	Once uploaded, verify that the upload was successful:
a.	Open the serial monitor:
Tools -> Serial Monitor
b.	Set the line ending to Newline and the baud rate to 115200.
c.	Type @# into the serial monitor and press Send. 
If the sketch was successfully uploaded, the board will respond with hwr_ctrl in the serial monitor.

3. Nunc OmniTray Perfusion Plate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

5.3.1	Skimmer Nozzle Clamp
1.	Insert the M2.5 hex nut and thread in the M2.5 bolt until finger tight.
2.	Using a 2 mm hex driver, tighten sharply until the slide breaks free, then continue until the clamp slide has moved through its entire range of motion.
3.	Back off the bolt until it is clear of the slide travel.
4.	Using a small flat screwdriver or a P200 tip, push the slide back to its starting position.

5.3.2	Perfusion Plate Lid
1.	Using the perfusion_plate_jig and a fine-tipped marker, transfer the four nozzle hole locations to the Nunc OmniTray lid.
2.	Using a 2.2mm PCB milling bit, drill all four marked holes.
Tip: use a peck drilling technique to limit plastic melt and improve hole dimensional accuracy.
3.	Clean all plastic debris from the lid and wipe with 70% EtOH.
4.	Apply a small amount of cyanoacrylate glue to the bottom of a skimmer nozzle clamp.
5.	Align the clamp with the drilled skimmer hole as pictured [FIGURE], press firmly, then allow to cure.
6.	Trim 3mm* from the end of four P200 pipette tips.
*Depending on the actual final diameter of the drilled holes and the P200 manufacturer, it may be necessary to trim slightly more or less from the tip to get a good fit as described in (8).
7.	Place the lid atop an OmniTray base.
8.	Insert the trimmed inlet and outlet P200 tips firmly into place. It may be necessary to twist the P200s into final position. With a proper fit, a P200 will sit securely with the nozzle end just above the plate base (<1 mm). Small cracks may form during this step, they can be disregarded. 
9.	Set the skimmer nozzle height:
a.	Insert a trimmed P200 into the nozzle clamp
b.	Select a nozzle height block for the desired plate volume [FIGURE]
c.	Place the height block in the plate bottom, underneath the skimmer nozzle
d.	Ensure the P200 tip touches the height block [FIGURE] and the plate lid sits flat on the base when no force is applied to the skimmer P200
e.	While holding the P200 is position, tighten the nozzle clamp bolt using a hex wrench until the P200 barrel deforms slightly
f.	Verify the skimmer nozzle position has not changed during tightening
10.	Cut two segments of PharMed BPT tubing, 6cm in length, join one end with a Y-piece fitting
11.	Press fit the open ends of the tubing into the outlet nozzles.
12.	Cut a ~2cm segment of tubing, place it over the remaining Y-piece barb.
13.	UV-sterilize the plate lid prior to use. This can be done, for example, with a standard tissue culture cabinet UV cycle by placing the lid(s) bottom-side-up as close to the UV lamp(s) as possible.

4. System Setup
^^^^^^^^^^^^^^^

The follow are general assembly guidelines. The exact configuration is likely to vary widely with experimental design. Several example configurations are provided for illustrative purposes.

5.4.1	Fluidics Build
1.	The inlet/outlet pump(s), media reservoir(s), and fluidic tubing upstream of culture plates should be kept in the same atmosphere (i.e. incubator) as the culture plate(s) to avoid degassing in the circuit and to allow for gas exchange at the reservoir(s).
2.	Skimmer pump(s), hardware controller(s), and the waste reservoir can be placed exterior to the incubator. Minimize fluid head for the outlet and skimmer pumps where possible.
3.	When setting up to culture in a humidified incubator, preheat the inlet/outlet peristaltic pump(s) in a dry incubator/oven several degrees higher than the final target temperature. This will minimize formation of potentially damaging condensation on the electronics and mechanical components.
4.	Connect the inlet and outlet tubing where the plate would go in the fluid circuit using a straight fitting [FIGURE?]. Following EtOH sterilization and media priming this joint is split and connected directly to the perfusion plate, see the Sterilization & Priming for detailed instruction.

5.4.2	Sterilization & Priming
Once the fluidics circuit is built, it must be sterilized and primed with culture medium.
1.	Connect a 70% EtOH to all reservoir connections.
2.	Disengage the outlet pump tubing such that the inlet pumps can pump freely all the way to the waste.
3.	Run the inlet pump at full speed until at least several volumes have passed through the fluid circuit.
NB: If using pinch valves in the circuit, cycle their positions regularly (via software, for example) to ensure full EtOH penetration. See the “sterilization_purge_prime.py” example script.
4.	Connect all reservoir inlets to their final culture media reservoirs.
5.	Repeat (3), then leave the valves in the state they will occupy at the experiment start and purge until the first media to be delivered occupies the tubing upstream of the plate.
6.	Engage all peristaltic pump head clamps to prevent backflow in the following steps.
7.	Sterilize the skimmer tubing by pumping 70% EtOH through, then run dry to clear. Wrap exposed tubing end(s) in sterilized aluminum foil until ready to connect to perfusion plate.

5.4.3	Perfusion Plate Connection
1.	Seed an unaltered Nunc OmniTray plate with the desired tissue culture model. A thin matrix may be applied to the culture surface in order to embed suspension cells, spheroids, or primary tissue for example. Allow cells sufficient time to adhere or embed prior to initiating flow.
2.	In the tissue culture cabinet, place the assembled and sterilized perfusion lid onto the seeded plate base. Verify that the inlet/outlet nozzle sit below the media surface and do not sit against the culture surface.
3.	Transfer the assembled perfusion plate to the incubator.
4.	Split the joint described in 5.4.1-(4) [FIGURE].
5.	Connect the outlet tubing using the straight fitting to the short segment of tubing in 5.3.2-(12), and press fit the inlet tubing into inlet P200 nozzle [FIGURE].
6.	Connect the skimmer tubing by pressing gently into the clamped skimmer P200 nozzle.
7.	The system is now ready for operation.
