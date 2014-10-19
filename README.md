Lander is a 2D extension of the "classic" lander app for the HP-15C.

The code might/might not fit in a 15C (26 regs + 39x7=273 bytes?, to confirm, keep an eye on the [MEM report](#mem) and [optimisations](#opt)).

# Phases

The simulation has two phases: descent, and ascent.

## Descent

The task is to deorbit (from near-circular orbit), and steer the lander so that it touches down smoothly (negative altitude and both velocity vectors < 25km/h).

Zero altitude displayed means success, flashing negative altitude reports the depth of the crater that will be named after you. Velocities, thrust, and *time* are zeroed (last horizontal/vertical V are still in the stack). Pitch is set to 0 (so this should allow a "jump" to another location...).

Output:

    T: hor V (km/h)
    Z: vertical V (km/h)
    Y: range since release (km)
    X: altitude (m)

## Abort

Should it be necessary, the descent stage can be jettisoned by initiating the ascent phase.

## Ascent

This phase can occur after touchdown, or after abort.

The ascent stage separates from the descent stage, and the task is to reach the CSM that has remained in orbit. There is no check for success (yet).

Output:

    T: closing speed (km/h) (+= overtaking CSM)
    Z: vertical V (km/h)
    Y: ground range to CSM (km) (+=before)
    X: altitude difference to CSM (m) (+=above)

Note: to skip descent phase and start from surface, execute following steps:

1. GSB C
1. 0 STO 6 STO 7
1. GSB .4

# Using the app

1. Select scenario (B or C, see '[Labels](#labels)' below) to initialise data.
1. Throttle inputs:
   - STO 1: throttle value (0.0 ... 1.0)
   - STO 2: total burn time (s) (suggested: 10s!)
   - STO 3: pitch (angle from vertical, +=prograde, deg)
1. Press 'A' to run the burn time. Calculator will run in predefined time segments (r14) 
until burn time or remaining fuel has been consumed.
1. Intermediate (PSE) output:
   - altitude
   - remaining time

## Apollo profile <a name="apollo"></a>

Note: these data are informative.

### Descent 
* From: 50kft (15.24km), 6014.64 km/h
* Target: 260NM (480km) (~12')
* 6': 100%
* 2': 50%
* 4': 50%-25%
      
### Ascent
* Target: alt: 60kft (18.24km), 6009.4 km/h (~level) @range: 167NM (309km) (~7')
* roughly circular, the real orbit was slightly elliptic (~ 16 x 90 km)
* 10": 0
* 5': 50-70
* 2': 70-90
* current implementation does not change initial CSM altitude

See also [LM-1](LM-1).

## Tips
* remember that weight changes over time, which means that acceleration due to thrust will increase
* if not enough fuel left, burn time is reduced to match remaining fuel
* CSM continues orbiting, this has an impact on *when* to initiate lift-off

## Notes

Simple formulas used ("[Euler symplectic](https://en.wikipedia.org/wiki/Semi-implicit_Euler_method)"), don't expect stable orbits: as as simple example, just run idle in the initial orbit and observe the decay. This is more a game than a realistic simulation; deviations get worse with larger time steps (r2). It might be interesting to have an idea of the lower bound for the time step before other effects start to appear.

# Programming
      
**MEM report** <A name="mem"></a>

This app was implemented on a Swiss Micros DM15C firmware DM15_M1B_V16 (max memory variant=230 reg).

    DM15_M1B :  23 143 64-2

Note: these values might not have been updated with every commit or release. A "pure" HP15C variant is in the [TODO](#opt) list.

## Labels <a name="labels"></a>

     A main burn routine
     B init descent phase
     C init ascent phase
     D -
     E -
     
     0 -
     1 -
     2 -
     3 -
     4 -
     5 -
     6 sub: compute distance to CSM
     7 sub: STO (x) (consumes X)
     8 sub: RCL (x) (consumes X)
     9 sub: init moon params
    .0 -
    .1 section: compute fuel used
    .2 sub: general init
    .3 sub: return circular orbit vc for given h (above sfce)
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

List:

    0   mf - fuel left (kg)
    1 > th - throttle (%)
    2\> t - burn time (s)
    3\> b - pitch (0=vertical, +=prograde) (deg)
    4\  h - height (m) /ascent: altitude above CSM (int: R)
    5\  d - ground range (km) /ascent: ground range before CSM (int: delta)
    6\  vv - velocity/vertical (km/h)
    7\  vh - velocity/horiz /ascent: hor. closing speed (km/h) 
    8   px (m)
    9   py (m)
    .0  vx (m/s)
    .1  vy (m/s)
    .2  ax (m/s2)
    .3  ay (m/s2)
    .4  total time 
    .5  g0 - gravity, surface (m/s2)
    .6  r0 - central body radius (m)
    .7  CSM alt (circular orbit)
    .8  
    .9  CSM vel (vc)
      
    (indirect:)
    20 f - max fuel flow (kg/s)
    21 F - max thrust (N)
    22 m0 - vehicle mass, dry (kg)
    23 mf0 - initial fuel (kg)
    24
    25
    26

## Flags

    0 -
    1 
    2 ascent mode: if set, output (range, alt and vh) refer to orbiting CSM
    3 -
    4 -
    5 -
    6 -
    7 -
    8
    9 crashed
 
## About the files

The dumps have been generated with [Swiss Micros](http://www.swissmicros.com/) (modified, see /vendor/swissmicros/master) [encoding/decoding scripts](/extern/swissmicros/decode RAM dump.htm).
- [mnemonic.txt](mnemonic.txt): equivalent program in mnemonic form. note that this file /might/ be more recent than the DM file. This is the most recent version of the code.
- [code_dump.txt](code_dump.txt): decoded program, line per line; sometimes contains comments. Informative, can be different from the mnemonic file.
- [DM15_M1B.txt](DM15_M1B.txt): can be uploaded via the serial link (see also [firmware](extern/swissmicros/firmware.txt) and [instructions](extern/swissmicros/instructions.php.txt))

NOTE: the HTML encoder is a buggy tool: the "dump from calc" is not always usable when it has been generated from the mnemonic form.

# RELEASE HISTORY <a name="rh"></a>

## V1.0 (2014-10-18)

- **new** add orbiting CSM (constant circular orbit, r24)
   - ascent phase
   - abort option
- **new** total time counter (r26)
- **changed** pitch coordinates (0=up)
- **changed** use [Velocity Verlet](https://en.wikipedia.org/wiki/Verlet_integration#Velocity_Verlet) to improve accuracy (a bit...)
- bug: descent stage did not take ascent stage weight into account
- update data
- refactor indirect register accesses
   - cost: -2x7 = -14 bytes (subroutines)
   - benefit: 
      - +5 (get rid of STO/RCL I; R_down; STO/RCL(i)) 
      - -2 (ENTER) per STO subroutine call
      - -1 per RCL subroutine call

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

### BUSY

### TODO

- review units? (use US system?)
- raise CSM orbit for ascent
- add input checks
- compute output after init
- add some screenshots of the logic
- add CSM rejoin check
- add scoring
   - within X km of planned touchdown
   - within X params of CSM
   - with minimum fuel used
- optimizations 
   - fit in 15C memory (19 46 0-0 => 23 42 0-0 => -210B) <a name="opt"></a>
      * remove multistep (r19, r17, r14), use r2 ~23B + 3r
      * remove PSE (2B)
      * positive g, negative in formula (1B)
      - remove crash detection (related to ascent init)
      - remove output stack (4B)
      * remove fuel tests
      - remove conversions
      - avoid two-byte steps (UM p.218)
         - rename SUB/GTO . labels
         - flags?
         - memory ops (STO +-*/)
      - better use of LST X?
      - reduce regs?
         - use stack for input
      - last ditch
         - constant mass (remove weight variance due to depleting fuel)
         - manual setup (user must initialise registers/flags)
            - astro constants (should not change once initialised)
            - vehicle data (every run)
      - remove ascent feature
         - sub C: 41B
   - speed (variant?)
      - skip burn calcs on zero throttle
      - precomputed factors? (needs regs)
      - r10-r19 need 6 steps for adressing: can they be swapped for normal (r0-r9) regs?
      - verlet: refactor thrust (is constant across step)
      - reorganise subroutine calls to avoid too much searching for the label
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
1. USER mode has subtle differences when programming: u STO x (see p. 176)

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
