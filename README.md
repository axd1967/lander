Lander is a 3D extension of the "classic" lander app for the HP-15C. The code might/might not fit in a 15C (23 regs + 42x7=294 steps?, to confirm, check the [release history](#rh).)

# Phases

The code might/might not fit in a 15C (to confirm, keep an eye on the [MEM report](#mem) and [optimisations](#opt)).

The simulation has two phases: descent, and ascent.

## Descent

The task is to deorbit (from near-circular orbit), and steer the lander so that it touches down smoothly (negative altitude and both velocity vectors < 25km/h).

Zero altitude displayed means success, flashing negative altitude reports the depth of the crater that will be named after you. Velocities, thrust, and *time* are zeroed (last horizontal/vertical V are still in the stack). Pitch is set to 0 (so this should allow a "jump" to another location...).

## Abort

Should it be necessary, the descent stage can be jettisoned by initiating the ascent phase.

## Ascent

This phase can occur after touchdown, or after abort.

The ascent stage separates from the descent stage, and the task is to reach orbit.

Note: to skip descent phase:

1. GSB C
1. 0 STO 6 STO 7
1. GSB .4

# Using the app

1. Select scenario (B or C, see '[Labels](#labels)' below) to initialise data.
1. Throttle inputs:
   - STO 1: throttle value (0.0 ... 1.0)
   - STO 2: total burn time (s)
   - STO 3: pitch (angle from vertical, pos=prograde, deg)
1. Press 'A' to run the burn time. Calculator will run in predefined time segments (r14) 
until burn time or remaining fuel has been consumed.
1. Intermediate (PSE) output:
   - altitude
   - remaining time

Output:

    T: hor V (km/h)
    Z: vertical V (km/h)
    Y: range (km)
    X: altitude (m)

## Apollo profile <a name="apollo"></a>

Note: these data are approximative.

### Descent 
* From: 50kft (15.24km), 6014.64 km/h
* Target: 260NM (480km) (~12')
* 6': 100%
* 2': 50%
* 4': 50%-25%
      
### Ascent
* Target: alt: 60kft (18.24km), 6009.4 km/h (~level) @range: 167NM (309km) (~7')
* roughly circular, the real orbit was slightly elliptic (~ 16 x 90 km)

See also [LM-1](LM-1).

## Tips
* remember that weight changes over time, which means that acceleration due to thrust will increase
* if not enough fuel left, burn time is reduced to match remaining fuel

## Notes

Simple formulas used ("[Euler symplectic](https://en.wikipedia.org/wiki/Semi-implicit_Euler_method)"), don't expect stable orbits: as as simple example, just run idle in the initial orbit and observe the decay.

**MEM report** <A name="mem"></a>
# Programming
      
## MEM reports

This app was implemented on a Swiss Micros DM15_M1B_V16 (max memory variant=230 reg).

    DM15_M1B :  23 147 60-4

Note: these values might not have been updated with every commit or release. A "pure" HP15C variant is in the [TODO](#opt) list (as a variant branch).

## Labels <a name="labels"></a>

     A main burn routine
     B init lander descent phase (see Apollo profile for details)
     C init ascent phase (separate from descent stage, pitch/0, thrust/100%)
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
    .1 section: compute fuel used
    .2 sub: general init
    .3 sub: setup circular orbit for given h (r4)
    .4 sub: crash analysis
    .5 section: main calc loop
    .6 section: stop, generate output
    .7 section: calc new pos, v
    .8 section: reduce fuel left
    .9 sub: calc eng + grav acc

## Registers
Notes:
* '>' Lander registers user can manipulate
* '\' statistics registers

    0   mf - fuel left (kg)
    1 > th - throttle (%)
    2\> t - burn time (s)
    3\> b - pitch (0=vertical, negative=retrograde) (deg)
    4\  h - height (m) (int: R)
    5\  d - range (km) (int: delta)
    6\  vv - velocity/vertical (km/h)
    7\  vh - velocity/horiz (km/h)
    8   px (m)
    9   py (m)
    .0  vx (m/s)
    .1  vy (m/s)
    .2  ax (m/s2)
    .3  ay (m/s2)
    .4  dt0 - delta t internal step (s)
    .5  g0 - gravity, surface (m/s2)
    .6  r0 - central body radius (m)
    .7  dt - user delta t (s)
    .8  fu - fuel used for current burn segment (kg)
    .9  T - remaining time in calculation loop (s)
      
    (indirect:)
    20 f - max fuel flow (kg/s)
    21 F - max thrust (N)
    22 m0 - vehicle mass, dry (kg)
    23 mf0 - initial fuel (kg)

## Flags

    1 fuel empty
    2 - (RESERVED - interrupt)
    3 -
    4 -
    5 -
    6 -
    7 -
    8 -
    9 crashed
 
## About the files

The dumps have been generated with [SwissMicro](http://www.swissmicros.com/) (modified, see  /vendor/swissmicros/master) [encoding/decoding scripts](/extern/swissmicros/decode RAM dump.htm).
- [code_dump.txt](code_dump.txt): decoded program, line per line; sometimes contains comments
- [mnemonic.txt](mnemonic.txt): equivalent program in mnemonic form. note that this file /might/ be more recent than the DM file.
- [HP.xml](HP.xml): simple Notepad++ syntax highlighter
- [DM15_M1B.txt](DM15_M1B.txt): can be uploaded via the serial link (see also [firmware](extern/swissmicros/firmware.txt) and [instructions](extern/swissmicros/instructions.php.txt))

# RELEASE HISTORY <a name="rh"></a>

## v0.4.1 (2014-10-11)
- **new**: add some [docs](LM-1)
- change: crash criteria
- solve: bug in crash analysis

## v0.4 (2014-10-09)

- add crash detection
- show intermediate altitude
- markdown (doc)

## v0.3 (2014-10-08)

- check routines in Earth orbit
- range bug/velocity convert
- remove Earth stuff
- add Apollo profile
- lander routines
- change V output system (from v/elev to vert/hor)

## v0.2 (2014-09-27)
- "bug in firmware" appears to be a bug in my head (ENTER/RCL issue, see UM P36)

## v0.1 (2014-09-21)
- test version for SwissMicro HTML decoder issue

## Upcoming

### DONE

- change the pitch coordinates (0=up)
- bug: descent stage did not take ascent stage weight into account
- update data

### BUSY
- use [Velocity Verlet](https://en.wikipedia.org/wiki/Verlet_integration#Velocity_Verlet) to improve accuracy (a bit...)

- ascent phase
- check successful ascent
- abort option

### TODO

- bug: vc for any given r/d
- review units? (use US system?)
- add total time counter
- add input checks
- compute output after init
- add some screenshots of the logic
- add scoring
   - within X km of planned touchdown
   - with minimum fuel used
- optimizations 
   - 15C memory issue (19 46 0-0 => 23 42 0-0) (variant?) <a name="opt"></a>
      - avoid two-byte steps
         - rename labels
      - better use of LST X?
      - reduce regs?
         - use stack for input
      - remove tests
      - remove crash detection
      - remove ascent feature
      - remove PSE
      - remove multistep (r19, r17)
      - last ditch
         - constant mass (remove weight variance due to depleting fuel)
         - manual setup (user must initialise registers/flags)
            - astro constants (should not change once initialised)
            - vehicle data (every run)
   - speed (variant?)
      - skip burn calcs on zero throttle
      - precomputed factors? (needs regs)
   - accuracy
      - Verlet: moment of fuel update (STO-0)
      - take RCS mass change (~60kg) into account?
      - RK4?
   - handling
      - renumber variables according to keyboard layout
      - interrupt flag

## RPN notes
1. BST in run mode should backstep repeatedly when held
1. RCL (i) should have a variant RCL (x); IMO latter should *consume* X.
1. some sequences that bit me as an RPL user (and I wonder how novices dealt with it):
   - assuming the stack looks like this: T:1 Z:2 Y:3 X:4, then keying
   - 888 [X<>Y] 999 
      - expect:  T:2 Z:3   Y:888 X:999
      - get:     T:3 Z:888 Y:4   X:999
   - [x^2] [ENT] [RCL] <r> 
      - expect:  T:3 Z:16 Y:16 X:[r] 
      - get:     T:2 Z:3  Y:16 X:[r] (Owner's Hb, Ed 2.4, p.36)
1. Number of steps is not very helpful, because some steps actually use *two* bytes. (LBL ./GTO/(GSB?) ./flags/x></DSE/ISG/STO/RCL <op>/(i) - see UM p. 218)

# Links

1. https://delicious.com/axd/moonlanding,HP15C
1. https://www.physicsforums.com/threads/improving-the-accuracy-of-a-java-simulation-of-n-orbital-bodies.380098/
1. http://stackoverflow.com/questions/4038554/2d-orbital-physics
1. https://en.wikipedia.org/wiki/Apollo_Lunar_Module
1. http://www.klabs.org/history/apollo_11_alarms/eyles_2004/eyles_2004.htm
1. http://spaceref.com/missions-and-programs/nasa/apollo/apollo-lunar-landing-mission-symposium/apollo-lunar-module-landing-strategy.html

## Another simulation (data only)
Very interesting pages, might prove my implementation is a joke, anyway, here you are... I didn't compare the results (yet).

1. http://www.braeunig.us/apollo/LM-descent.htm
1. http://www.braeunig.us/apollo/LM-ascent.htm
