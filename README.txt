2D lander (HP 15C)
==================

STATUS: PROTOTYPE, INCOMPLETE, not fully tested yet. Be patient.

Aim
===

2D variant of the classic - 1D - lander calculator app.

Simple formulas used ("Euler"), don't expect stable orbits.

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
   
This app was implemented on a Swiss Micros DM15_M1B (max memory variant).

TO CONFIRM: should run fine on a HP-15C (check the register/step requirements).

About the files
===============
The dumps have been generated with SwissMicro encoding/decoding scripts (see /extern/swissmicros/decode RAM dump.htm)
- DM15_M1B.txt: can be uploaded via the serial link (see also firmware.txt and instructions.php.txt)
- code_dump.txt: decoded program, line per line
- mnemonic.txt: equivalent program in mnemonic form. note that this file /might/ be more recent than the DM file.
- HP.xml: simple NotePad++ syntax highlighter

MEM reports
===========

DM15     : ?
DM15_M80 : ?
DM15_M1B : 30 164 36-5

Note: these values might not have been updated with every latest commit.

Labels
======

 A burn
 B (TODO) init lander descent (from 45000 ft). Try to land in one piece.
 C (TODO) init lander ascent (from surface). Try to reach 40000 ft circular orbit at 200km downrange
 D (BUSY) init 420km Earth orbit
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
.0 -
.1 -
.2 sub: general init
.3 sub: setup circular orbit
.4 sub: init Earth params
.5 section: calc loop
.6 section: stop
.7 section: calc new pos, speed
.8 section: calc gravity acc, burn acc
.9 section: burn

Registers
=========

'>' are the ones you may fiddle with...

 0 mf - fuel left (kg)
 1 > th - throttle (%)
 2 > t - burn time (s)
 3 > b - burn angle (from local horizontal, +=up) (deg)
 4 h - height (m) (also: R)
 5 d - range (km) (also: delta)
 6 v - velocity (km/h)
 7 a - angle above horizon (deg)
 8 px (m)
 9 py (m)
.0 vx (m/s)
.1 vy (m/s)
.2 ax (m/s2)
.3 ay (m/s2)
.4 dt0 - delta t preset (s)
.5 g0 - gravity, surface (m/s2)
.6 r0 - central body radius (m)
.7 dt - delta t (s)
.8 fu - fuel used for burn (kg)
.9 T - remaining time in calculation loop (s)

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
- RCL (i) should have a variant RCL (x); both should consume X (but I'm RPL minded... see also http://www.hpmuseum.org/cgi-sys/cgiwrap/hpmuseum/archv017.cgi?read=116747)

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
   - better use of LST X
   - precomputed factors?
   - better approximations (symplectic Euler?)