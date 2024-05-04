## Alignment Procedure for 1A3 Monochromatic Mode
*Currently for Rhodes & Eiger16M on Delhi Table; will be updated for RAMSIV*

K.Nygren 2024-2 cycle

This procedure starts after all tables/motors are on and configured, all pre-amps are on and gains set to reasonable values, ion chambers have nitrogen gas flowing. 

Check the foil box is working properly (compressed air at 10 O'clock position coming into the hutch)

If you are using the eiger, you can use the `eig_off` command to prevent eiger images from being collected with every scan (mode desired for alignment). When you want to take images with the Eiger you can use `eig_on`.  

#### check CESR conditions
- With `mos_control` off: tune mostab knob until maximum counts are achieved (look at counter_id1a3 on station computer or analog counter). Refer to beamtime notes, staff scientist, or old conditions for sanity check on whether counts are in appropriate range for CESR conditions / energy that week. 

#### mbdscan vs dscan and local enable

- `mbdscan` is a custom version of `dscan` that 
    - checks whether an enable condition is met 
    - checks whether there is beam in the hutch
    - automatically opens and shuts the "fast shutter" before and after the scan, respectively
    - moves the laser out of the way if it is in the beam path

*Local enable* is a signal that the station uses to determine if the beam conditions are suitable for data collection or alignment. Our local enable will most commonly be looking at either the CESR enable or the CHESS enable. 

CHESS enable will not be given typically until noon on Wednesdays - you can select **CESR enable** at this time, as opposed to CHESS enable in one of the following ways: 

1.  Use the local enable menu: type `local_enable_mode` and select 3 to switch to **CESR enable**. 
2.  Use the command `select_enable` *1*


Be sure to move the local_enable back to CHESS after the alignment period!

#### align pre-slits

- the slits in 1A3 do not sit on X,Z stages, so the slits are configured to move with macro motors. The preslit blades are motors m0r, m0l, m0t, m0b; macromotors are m0x and m0z for the x and z translations respectively. 
- `set_pre_height` *value in mm* and `set_pre_width` *value in mm* will set the height and width of the preslits; typically we will set these slightly larger than the largest beam size for the week (height = 1.2, width = 2.2)
-  to center the slits on the beam:
    - `mbdscan` *m0x -1 1 50 0.2*; fit peak in PyMCA - ion chamber value is *a3ic1/a3ic0*;`umv` *m0x fit_peak_position*; `set` *m0x 0*; record changes in beamtime notes (copy paste SPEC printout)
    - `mbdscan` *m0z -1 1 50 0.2*; fit peak in PyMCA fit peak in PyMCA - ion chamber value is *a3ic1/a3ic0*;`umv` *m0z fit_peak_position*; `set` *m0z 0*; record changes in beamtime notes (copy paste SPEC printout)

Note: The profiles should look like fairly nice flat tophats, if they do not either `map` or CESR may need to be adjusted to have the correct profile coming into the hutch. 

#### energy scans

- Know what energy you are at: type `getE` and enter - if this is the energy you want to be at this week, use the following procedure to tune up the energy: 
    - nominalE for the purposes of these instructions is the target energy
    - make sure you are are at the target E 
    - type `find_foil` and enter. The foil should be inserted and the foil status MEDM light should be green for the energy you are targeting 
    1. Run `Escan_custom` *nominalE 0.5 100 3* OR `Escan` *nominalE nominalE-0.5 nominalE+0.5 100 3*
    2. Fit *step function* in PyMCA using ion chamber ratio  *a3ic2/a3ic1*; NOTE: if you do not see a step make sure the foil is actually inserting. The foil box is a pile of garbage. 
    3. `moveE` to value found in Energy value of the step function
    4. `setE` to **target Energy** value
    
    - repeat 1-4. Once the fit value is appropriately close to the energy value (within a few eV), type `Escan_save` to have the scan set as the master for our data reduction workflow purposes. Make sure you have confirmed that you are actually at the **target energy** position before proceeding. 

#### align fast shutter

- `mbdscan` *shutx -1 1 50 0.2*
    -  fit in pyMCA using ion chambers *a3ic3/a3ic2*
    - `set` *shutx* 0; copy printout to beamtime notes

- `mbdscan` *shutz -1 1 50 0.2*
    -  fit in pyMCA using ion chambers *a3ic3/a3ic2*
    - `set` *shutz* 0; copy printout to beamtime notes

- Check that the shutter is actually working: type `opens` and `closes` and watch ion chamber a3ic2 in the counter_id1a3 MEDM screen or the analog counters to verify it turns to zero with `closes`.

#### align beam defining slits 

- the slits in 1A3 do not sit on X,Z stages, so the slits are configured to move with macro motors. The beam defining slit blades are motors m1r, m1l, m1t, m1b; macromotors are m1x and m1z for the x and z translations respectively. 

Note: the beam defining slits are a different style (Khozu) than the pre slits and the guard slits and have half the travel (7 mm vs 14 mm max beam height/width). 

- `set_beam_height` *value in mm* and `set_beam_width` *value in mm* - typically height = 1.0 & width = 1.0 for alignment

- `mbdscan` *m1x -1 1 50 0.2*
    -  fit in pyMCA using ion chambers *a3ic4/a3ic3*
    - `set` *m1x* 0; copy printout to beamtime notes

- `mbdscan` *m1z -1 1 50 0.2*
    -  fit in pyMCA using ion chambers *a3ic4/a3ic3*
    - `set` *m1z* 0; copy printout to beamtime notes

#### align guard slits 

- the slits in 1A3 do not sit on X,Z stages, so the slits are configured to move with macro motors. The guard slit blades are motors m2r, m2l, m2t, m2b; macromotors are m2x and m2z for the x and z translations respectively. 

- `set_guard_height` *value in mm* and `set_guard_width` *value in mm* - typically values are set 0.2 mm larger than the beam defining slits. (typical height = 1.2 & width = 1.2 for alignment). 

- `mbdscan` *m2x -1 1 50 0.2*
    -  fit in pyMCA using ion chambers *a3ic5/a3ic4*
    - `set` *m2x* 0; copy printout to beamtime notes

- `mbdscan` *m2z -1 1 50 0.2*
    -  fit in pyMCA using ion chambers *a3ic5/a3ic4*
    - `set` *m2z* 0; copy printout to beamtime notes

#### align diode 
- afix diode to the diode stand (typically with kapton tape to the carbon fibre tube infront of the garage door on the Delhi table); make sure diode is connected to its BNC connector. Verify the pre amp is on for the diode. 

- Either (i) use camera and fluorecence paper, (ii) take a burn, or (iii) use the nominally aligned laser (if you had the same set up from the previous week) to find the beam position in the diode plane. Move the diode to the nominal position where X-rays are. Do not simply search in space aimlessly - futile effort. 

- `tw` *bsx* and *bsz* until you have maximized the counts reading the counter_id1a3 MEDM or analog diode counter.
- `mbdscan` *bsx -1 1 50 0.2*
    -  fit in pyMCA using *diode*
    - `set` *bsx* 0; copy printout to beamtime notes
- `mbdscan` *bsz -1 1 50 0.2*
    -  fit in pyMCA using *diode*
    - `set` *bsz* 0; copy printout to beamtime notes

p.s. it is known that the bsz is tragically slow. 

## align sample staging (rhodes table, bose, RAMS, etc.)

### rhodes table alignment

#### place alignment sample in beam (ball bearing)
- current sample is white-painted ball bearing
- place bearing in center of beam x,z
- `set_beam_height` 0.5; `set_beam_width` 0.5 #typical for alignment

#### align rome
- `mbdscan` *rsampX -0.5 0.5 50 0.2* ; record rsampX center x1
- `umvr` *rsampY 25*
- `mbdscan` *rsampX -0.5 0.5 50 0.2* ; record rsampX center x2
- SPEC> `p deg(atan((x1-x2)/25))`; record as delta_rome
- `umvr` *rome delta_rome*

check rome
- `mbdscan` *rsampX -0.5 0.5 50 0.2* ; record rsampX center x2
- `umvr` *rsampY -25*
- `mbdscan` *rsampX -0.5 0.5 50 0.2* ; record rsampX center x1
- SPEC> *p deg(atan((x1-x2)/25))*; record as delta_rome
- `umvr` *rome delta_rome*

repeat the procedure above until you are confident in the rome value, then `set` *rome 0* and record in beamtime notes.  

#### align rotation center of 410 


- `mbdscan` *rsampX -0.5 0.5 50 0.2* ; record rsampX center x1
- `umvr` *ome 180*
- `mbdscan` *rsampX -0.5 0.5 50 0.2* ; record rsampX center x2
- SPEC> *p (x1+x2)/2 ; record as rsampX rot_center
- `umv` *rsampX rot_center*

here you can use sampX (or sampXc if using composite motors) and re-center the ball bearing

check rotation center

- `mbdscan` *rsampX -0.5 0.5 50 0.2* ; record rsampX center x1
- `umvr` *ome -180*
- `mbdscan` *rsampX -0.5 0.5 50 0.2* ; record rsampX center x2
- SPEC> *p (x1+x2)/2 ; record as rsampX rot_center
- `umv` *rsampX rot_center*

repeat the procedure above until you are confident in the rotation center value for rsampX, then `set` *rsampX 0* and record in beamtime notes.  At this point, you can also go into **config** and set rsampX to "CAN'T MOVE."

#### square ome / pseudo motor reference

The sample staging can be a 2- or 4- stage stack. The motor naming convention is as follows for linear motors. 


2-stage stack:


| motor mnemonic | motortype | description |
|------ | ----- | ----- | 
| sampX | galil | sample motor nominally aligned with lab frame x-axis when ome=0 |
| sampY | galil | sample motor nominally aligned with lab frame y-axis when ome=0 |
| sampXp | macro | sample pseudo-motor aligned with x-axis at any ome |
| sampYp | macro | sample pseudo-motor aligned with y-axis at any ome |

4-stage stack:

| motor mnemonic | motortype | description |
|------ | ----- | ----- | 
| sampXt | galil | sample motor nominally aligned with lab frame x-axis when ome=0, top of x-stack |
| sampXb | galil | sample motor nominally aligned with lab frame x-axis when ome=0, bottom of x-stack |
| sampYt | galil | sample motor nominally aligned with lab frame y-axis when ome=0, top of y-stack |
| sampYb | galil | sample motor nominally aligned with lab frame y-axis when ome=0, bottom of y-stack |
| samp*c | macro | sample pseudo-motor noiminally aligned with *-axis - each linear motion will be split 50/50 between top and bottom stages within a stack |
| samp*p | macro | sample pseudo-motor aligned with *-axis at any ome |
| samp*cp | macro | sample pseudo-motor aligned with *-axis at any ome that utilizes the composite motor motion (both top and bottom stages for travel). **most commonly used motor-type after alignment** |

- `mbdscan` sampX -0.5 0.5 50 0.2 ; record sampX center x1
- `umvr` *sampY -25*
- `mbdscan` sampX -0.5 0.5 50 0.2 ; record sampX center x2
- *SPEC> p deg(atan((x1-x2)/25))*; record as delta_ome
- `umvr` *ome* delta_ome #didnt check which sign - if wrong do inverse

check ome

- `mbdscan` sampX -0.5 0.5 50 0.2 ; record sampX center x2
- `umvr` *sampY 25*
- `mbdscan` sampX -0.5 0.5 50 0.2 ; record sampX center x1
- *SPEC> p deg(atan((x1-x2)/25))*; record as delta_ome
- `umvr` *ome* delta_ome #didnt check which sign - if wrong do inverse

repeat the procedure above until you are confident in the rome value, then `set` *ome 0* and record in beamtime notes.  

#### laser alignment 

- make sure that ball bearing is at the center of rotation and in the center of the beam in x,z. One of many ways to do this is below:
    - `umv` *ome 0*
    - `mbdscan` *sampXp -0.5 0.5 50 0.2* ; sampXp postion = x1
    - `umv`sampXp x1
    - `umv` *ome 180*
    - `mbdscan` *sampXp -0.5 0.5 50 0.2* ; sampXp postion = x2
    - `umv`sampXp x2
    - `umv` *ome 0* #back to starting position

The laser sits on a series of motors lovingly referred to as the "budget bot". The motors, as such, are labeled bbx, bby, bbz, and bbr for the x, y, z, and rotation motion. bbr = 0 when the laser is pointed downstream. 

- `laser_in` command will bring the laser nominally in line with the X-rays. This may happen through a motion in either bbr or bbx, depending on the last configuration and how things are set up in the laser Macros. 

For the laser aligment we will use `dscan` and not `mbdscan` since we will not be using the x-ray beam and wish to keep the fast shutter closed as not to destroy the laser. If you do run accidentally run `mbdscan`, the laser will move out of the beam as part of this routine. simply run `laser_in` to put the laser back. 

- if the laser is being placed for the first time in a long time, place a glass slide at the front and back of the rhodes table and take a burn (will drop in image). Move bbz and iterate bbx and bbr until the laser passes through both burn marks. 

NOTE: the bbx stage is not in the x-ray reference frame. sample psuedo motors should be used for finding the delta bbr position. 

- once nominally aligned, at ball bearing centered on rotation axis, place laser at correct height: 
    - `dscan` *bbz -1 1 50 0.2* ; record bbz_center
    - `umv` *bbz bbz_center*
    - `set` *bbz 0*
- find the correct laser angle (bbr)
    - `dscan` *sampXp -1 1 50 0.2* ; record x1
    - `umvr` *sampYp 10*
    - `dscan` *sampXp -1 1 50 0.2* ; record x2
    - spec> *p deg(atan((x1-x2)/10)) ; record delta_bbr
    - `umvr` *bbr delta_bbr* #check sign on this
    - `tw` bbx until you have the laser on the sample

    repeat this processes until you are satisfied with the bbr
    - `set` *bbr 0*
- find the postion of bbx: 
    - move to the positions in sampXp, sampYp, and rsampZ that ball bearing is centered on the X-rays
    - `tw` bbx until laser is on ball
    - `dscan` *bbx -1 1 50 0.2* ; record bbz_center
    - `umv` *bbx bbz_center*
    - `set` *bbx 0*

- **zeroing the laser** the laser is going to be calibrated so that it reads zero *on the rotation axis*. 
    - know the diameter of your ball bearing (its probably 1/8" diameter, radius = 1.5875 mm)
    - move to the positions in sampXp, sampYp, and rsampZ that ball bearing is centered on the X-rays
    - `umvr` *sampYp positive-radius-of-ball*
    -  walk into the hutch and zero the laser (blue "zero" button on the laser controller display). The laser should now read zero.
    - `umvr` *sampYp (-)radius-of-ball*
    - `get_keyence` *1* ; the value returned should be the radius of the ball bearing. If it is not, something is wrong and stop and figure it out. 

## Switching Enables
At this point, it is likely that CHESS is enabled post morning alignments
To switch to **CHESS enable** from CESR enable, you can either:
1.  Use the local enable menu: type `local_enable_mode` and select 0 to switch to **CHESS enable**. 
2.  Use the `select_enable` command with no argument (or *0* if you wish to use an argument).


## Alignment of Detector

- measure the nominal sample to detector distance with a tape measure

- place Ce02 washer and square in the beam. The procedure may look something like: 
    - `tw` appropriate psuedo motors to place ceo2 at the center of the beam and on the rotation axis
    - `dscan` *sampXp -4 4 50 0.2* ; fit linear line
    - SPEC> deg(atan(slope)) ; record square_ome
    - `umvr` *ome -(square_ome)*
    - `dscan` *sampXp -4 4 50 0.2* ; verify sample is square

- place CeO2 exactly on rotation center (in sampYp)
    - `get_keyence` *1* ; record Head A: value as distance1
    - `umvr` ome 180
    - `get_keyence` *1* ; record Head A: value as distance2
    -  distance2 - ((distance2+distance1)/2)  = delta_y
    - `umvr` *sampYp delta_y*
    - `get_keyence` *1* ; should read average between distance 1 and distance 2
    - `umvr` ome -180 ; back to ome 0
    - `get_keyence` *1* ; should read average between distance 1 and distance 2

- CeO2 is now centered and ready for detector measurements

#### Eiger16M detector Alignment

- `ff_det_menu` select detector **Eiger216M**
- in a chess_id1a3 terminal window on the station computer type `start_EIG16M_CdTe_MEDM` to launch the MEDM screen 

To toggle the detector on and off, use `eig_on` and `eig_off`. 

**MAKE SURE BEAMSTOP IS WHERE YOU THINK IT IS**

*link HEXRD documentation for fast powder refinement*

In the hexrdgui, load the Eiger16M data. If the gui does not preload with the Eiger Data - a starting state can be found at **/nfs/chess/aux/user/chess_id1a3/HEXRD/DetectorFiles/Eiger16M_monolith_state.h5**

The Eiger16M has no "image transforms" (mirror about the horizontal, vertical, rotation etc.). The hexrdgui has the following reference frame:

| Direction w.r.t. Beam (facing downstream)| CHESS Lab Ref Frame | HEXRD Ref Frame |
| --------------------- | ------------- | --------------- |
| transverse        | +X right  | +X right |
| vertical          | +Z up     | +Y up    |
| beam direction    | +Y        | -Z       |

The Eiger16M sits on the Delhi Table. The following motor nmemonics correspond to the HEXRD planar detector translations and tilts: 

| Tilt Rotation | SPEC Motor mnemonic | HEXRD Transform |
| -------------------    | ---------- | --------------- |
| horizontal translation | detx  | Translation X (0)|
| vertical translation   | tzt6  | Translation Y (1)|
| detector distance      | dety  | Translation Z (2)|
| rotation about horiz.  | txr6  | Tilt X (0)|
| rotation about vert.   | tzr6  | Tilt Y (1)|
| rotation about beam    | tyr6  | Tilt Z (2)|

Best practice is to align the detector with the table tilts such that all values are practically zero (tilts must be under 1 degree), with the exception of the detector distance which should be chosen appropriately for the experiment. 

The procedure is as follows: 

1. `close_garage_door` to protect the detector. The diode is part of the table assembly and may move out of the direct beam at this time. 

2. `tw` *dety* with eyes in the hutch until the detector face is approximately the correct distance to the sample. Use a tape measure to verify if this is the first time the table has been placed during the cycle. 

3. repeat the *diode alignment* (see **align diode**)

4. set beam size for calibration measurements (typically `set_beam_height` 0.5; `set_beam_width` 0.5)

5. get ready to take x-rays: `open_garage_door`; `eig_on`

6. `tseries` *1* *5*

7. perform fast powder calibration in HEXRD. Move the table macro motors in the (-) direction of the HEXRD reporting values of translations and tilts to square and center the detector. 
    - move `dety` to the final position first
    - `tseries` *1* *5*; perform fast powder calibration
    - resolve the tilts next (motors txr6, txy6, txz6)
    - `tseries` *1* *5*; perform fast powder calibration
    - resolve the translations (motors detx, tzt6)


## Powder Calibration Scans 

#### CeO2 washer

- a CeO2 washer should be provided that has a known width. 

- place Ce02 washer and square in the beam. The procedure may look something like: 
    - `tw` appropriate psuedo motors to place ceo2 at the center of the beam and on the rotation axis
    - `dscan` *sampXp -4 4 50 0.2* ; fit linear line
    - SPEC> deg(atan(slope)) ; record square_ome
    - `umvr` *ome -(square_ome)*
    - `dscan` *sampXp -4 4 50 0.2* ; verify sample is square

- place CeO2 exactly on rotation center (in sampYp)
    - `get_keyence` *1* ; record Head A: value as distance1
    - `umvr` ome 180
    - `get_keyence` *1* ; record Head A: value as distance2
    -  distance2 - ((distance2+distance1)/2)  = delta_y
    - `umvr` *sampYp delta_y*
    - `get_keyence` *1* ; should read average between distance 1 and distance 2
    - `umvr` ome -180 ; back to ome 0
    - `get_keyence` *1* ; should read average between distance 1 and distance 2

#### calibration command
- in user directory: `newsample` ceo2-####-#; where the #### is the two digit month followed by two digit date; # is an integer value that increments if you take more than one calibrant on the same day. 
- confirm you can safely rotate the omega stage in an automated way through atleast 180 degrees. Check cable managment, sample extents, etc.  
- `cal_measure_auto` (enter): this command will tell you what you need to provide to perform the automated measurement. it will take an image at the current ome; relative 180 (+ or - as specificed); and a darkfield successively. A metadata file (.par) and key (.json) pair will also be generated in the raw directory folder. 



