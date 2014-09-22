2D lander (HP 15C)
==================

Implemented on a Swiss Micros DM15_M1B (max memory variant).

TO CONFIRM: should run fine on a HP-15C (check the register/step requirements).

STATUS: PROTOTYPE, INCOMPLETE, not fully tested yet. Be patient.

Aim
===

2D variant of the classic - 1D - lander calculator app.

Simple formulas used, don't expect stable orbits etc.

Select a scenario (B,C,D, see below) to initialise data, 
then repeat 'A' to define burn time segments until reaching target (or crash).

Enter following data:

   - STO 1: throttle %
   - STO 2: burn time
   - STO 3: angle above horizon (TODO)

Press 'A' to run the burn time. Calculator will run in predefined time segments (r14) 
until burn time has been consumed.

Display: (TODO)
   - '0' means target reached
   - flashing means crash/overshoot
   
About the files
===============
Most files have been generated with the SwissMicro encoding/decoding scripts (see /extern/swissmicros/decode RAM dump.htm)
- DM15_M1B.txt: can be uploaded via the serial link (see also firmware.txt and instructions.php.txt)
- code_dump.txt: decoded program, line per line
- mnemonic.txt: equivalent program in mnemonic form. note that this file /might/ be more recent than the DM file.
- HP.xml: simple NotePad++ syntax highlighter

MEM reports
===========

DM15     : ?
DM15_M80 : ?
DM15_M1B : 30 166 34-6

Note: these values might not have been updated with every latest commit.

Labels
======

 A burn
 B (TODO) init lander descent (from 45000 ft). Try to land in one piece.
 C (TODO) init lander ascent (from surface). Try to reach 40000 ft circular orbit at 200km downrange
 D (BUSY) init 200km Earth orbit
 E -

 0 -
 1 -
 2 -
 3 -
 4 -
 5 -
 6 -
 7 -
 8 -
 9 (TODO) init moon params
10 -
11 -
12 general init
13 setup orbit
14 (BUSY) init earth params
15 calc loop
16 stop
17 calc
18 calc burn
19 burn loop

Registers
=========

'>' are the ones you may fiddle with...

 0 mf - fuel left (kg)
 1 > th - throttle (%)
 2 > t - burn time (s)
 3 > b - burn angle (from local horizontal, +=up) (deg)
 4 h - height (m)
 5 d - range (km)
 6 v - velocity (km/h)
 7 a - angle above horizon (deg)
 8 px (m)
 9 py (m)
10 vx (m/s)
11 vy (m/s)
12 ax (m/s2)
13 ay (m/s2)
14 dt0 - delta t preset (s)
15 g0 - gravity, surface (m/s2)
16 r0 - central body radius (m)
17 dt - delta t (s)
18 fu - fuel used for burn (kg)
19 T - remaining time in calculation loop (s)

(I)
20 f - max fuel flow (kg/s)
21 -
22 m0 - vehicle mass, dry (kg)
23 mf0 - initial fuel mass (kg)
24 I - thrust per fuel unit (N/kg)

Flags
=====
 1 fuel empty
 2 -
 3 -
 4 -
 5 -
 6 -
 7 -
 8 -
 9 (TODO) landed/crashed?
 
NOTES
=====
- BST in run mode should backstep repeatedly when held

DONE
====
v0.1 (2014-09-21)
- test version for SwissMicro issue

BUSY
====
- check routines in Earth orbit

TODO
====

- add crash detection
- review units
- add output display, convert local speed angle
- add input checks
- get rid of feet
- review criteria
- add some screenshots of the logic
- abort option!
- optimizations
   - precomputed factors?