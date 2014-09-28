2D lander (HP 15C)
==================

STATUS: PROTOTYPE, INCOMPLETE, not fully tested yet. Be patient.

Aim
===

2D variant of the classic (1D) lander calculator app.

Simple formulas used ("Euler symplectic"), don't expect stable orbits.

Select a scenario (B,C see 'Labels' below) to initialise data.

Enter following data:

   - STO 1: throttle %
   - STO 2: burn time
   - STO 3: burn angle above horizon

Press 'A' to run the burn time. Calculator will run in predefined time segments (r14) 
until burn time has been consumed.

Display: (TODO)
   - '0' means target reached
   - flashing means crash/overshoot
   
Apollo 11 profiles
==================

   Descent 
      From: 50kft = 15.24km
      Target: 260NM = 480km (~12')
      6': 100%
      2': 50%
      4': 50%-25%
      
   Ascent
      Target: alt: 60kft = 18.24km (~level) @range: 167NM = 309km (~7')
      (roughly circular, the real orbit is slightly elliptic)
      
MEM reports
===========

This app was implemented on a Swiss Micros DM15_M1B_V16 (max memory variant).

DM15     : ?
DM15_M80 : ?
DM15_M1B : 23 161 46-0

Note: these values might not have been updated with every latest commit.

Labels
======

 A main routine
 B (BUSY) init lander descent (See A11 profile for details)
 C (TODO) init lander ascent (from surface)
 D -
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
 9 sub: init moon params
.0 -
.1 -
.2 sub: general init
.3 sub: setup circular orbit for given h (r4)
.4 -
.5 section: main calc loop
.6 section: stop
.7 section: calc new pos, speed
.8 section: calc gravity acc, overall acc
.9 section: calc fuel used, burn acc

Registers
=========

'>' are the ones you may fiddle with...

 0 mf - fuel left (kg)
 1 > th - throttle (%)
 2 > t - burn time (s)
 3 > b - burn angle (elevation, +=up) (deg)
 4 h - height (m) (int: R)
 5 d - range (km) (int: delta)
 6 v - velocity/magnitude (km/h)
 7 a - velocity/elevation (deg)
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
21 F - max thrust (N)
22 m0 - vehicle mass, dry (kg)
23 mf0 - initial fuel mass (kg)

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
 
About the files
===============
The dumps have been generated with SwissMicro encoding/decoding scripts (see /extern/swissmicros/decode RAM dump.htm)
- DM15_M1B.txt: can be uploaded via the serial link (see also firmware.txt and instructions.php.txt)
- code_dump.txt: decoded program, line per line
- mnemonic.txt: equivalent program in mnemonic form. note that this file /might/ be more recent than the DM file.
- HP.xml: simple NotePad++ syntax highlighter

NOTES
=====
- BST in run mode should backstep repeatedly when held
- RCL (i) should have a variant RCL (x); both should consume X (but I'm RPL minded... see also http://www.hpmuseum.org/cgi-sys/cgiwrap/hpmuseum/archv017.cgi?read=116747)

RELEASE HISTORY
===============

v0.2 (2014-09-27)
- "bug in firmware" appears to be a bug in my head (ENTER/RCL issue, see UM P36)

v0.1 (2014-09-21)
- test version for SwissMicro issue

DONE
====
- check routines in Earth orbit
- range bug/velocity convert
- remove Earth stuff

BUSY
====
- lander
- add profile

TODO
====

- add crash detection
- add output display, fuel left,  ... -> copy to stack?
- interrupt flag
- add input checks
- review criteria
- abort option
- review units
   - output: option for ft/nm units
- add some screenshots of the logic
- move to Markdown
- add total time counter
- optimizations
   - better use of LST X
   - fit into 15C memory (19 46 0-0)
   - precomputed factors?
   - better approximations (RK4?)
   - renumber variables according to keyboard layout