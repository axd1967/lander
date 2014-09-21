Lander (HP 15C)
-----------------

(Implemented on a Swiss Micros DM-15)

STATUS: PROTOTYPE, not tested yet.

Aim
===
2D variant of the classic lander calculator app.

Select a scenario (B,C,D, see below).

Enter following data:
   - throttle % (STO 1)
   - burn time (STO 2)
   - angle above horizon (STO 3)

Press 'A' to run the burn time.


TODO
====
- add crash detection
- add output display
- optimizations

Labels
======
 Note: 'x' -> not implemented yet
 
 A burn
 B x init lander descent (from 45000 ft)
 C x init lander ascent (from surface)
 D init 200km Earth orbit
 E

 0
 1
 2
 3
 4
 5
 6
 7
 8
 9 x init moon params
10
11
12 general init
13 setup orbit
14 init earth params
15 calc loop
16 stop
17 calc
18 calc burn
19 burn loop

Registers
=========
 0 fuel left (kg)
 1 throttle (%)
 2 burn time (s)
 3 burn angle (from local horizontal, +=up) (deg)
 4 height (m)
 5 range (km)
 6 velocity (km/h)
 7 angle above horizon
 8 px
 9 py
10 vx
11 vy
12 ax
13 ay
14 delta t preset
15 gravity, surface
16 central body radius
17 delta t
18 fuel used for burn
19 remaining time in calculation loop

20 max fuel flow (kg/s)
21 
22 vehicle mass, dry
23 initial fuel mass
24 thrust per fuel unit (N/kg)
