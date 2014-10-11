Lander is a simulation similar to the "classic" lander app for the HP-15C, but extended to 2 dimensions.

The aim is to deorbit (from near-circular orbit), and steer the lander so that it touches down smoothly (negative altitude and both velocity vectors < 25km/h).

The code might/might not fit in a 15C (to confirm, keep an eye on the [MEM report](#mem) and [optimisations](#opt))

## Run
1. Select scenario B (see '[Labels](#labels)' below) to initialise data.
1. Throttle inputs:
   - STO 1: throttle value (0.0 ... 1.0)
   - STO 2: total burn time (s)
   - STO 3: burn elevation (angle above horizon, deg)
1. Press 'A' to run the burn time. Calculator will run in predefined time segments (r14) 
until burn time or remaining fuel has been consumed.
1. Intermediate (PSE) output:
   - remaining fuel
   - altitude
   - remaining time

Output:

    T: hor speed (km/h)
    Z: vertical speed (km/h)
    Y: range (km)
    X: altitude (m)

## Apollo profile <a name="A11"></a>

### Descent 
* From: 50kft (15.24km)
* Target: 260NM (480km) (~12')
* 6': 100%
* 2': 50%
* 4': 50%-25%
      
### Ascent
* Target: alt: 60kft (18.24km) (~level) @range: 167NM (309km) (~7')
* roughly circular, the real orbit was slightly elliptic

See also [LM-1](LM-1).

## Tips
* due to inaccuracies in the algorithm, you need a firm (200deg) deorbit burn elevation
* remember that weight changes over time, which means that acceleration due to thrust will increase
* if not enough fuel left, burn time is reduced to match remaining fuel

## Notes

Simple formulas used ("[Euler symplectic](https://en.wikipedia.org/wiki/Semi-implicit_Euler_method)"), don't expect stable orbits: as as simple example, just run idle in the initial orbit and observe the decay.
      
**MEM report** <A name="mem"></a>

This app was implemented on a Swiss Micros DM15_M1B_V16 (max memory variant=230 reg).

    DM15_M1B   :  23 155 52-3
    
Note: these values might not have been updated with every commit or release. A "pure" HP15C variant is in the [TODO](#opt) list (as a variant branch).

## Labels <a name="labels"></a>

     A main routine
     B init lander descent (see A11 profile for details)
     C - (RESERVED - ascent)
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
    .4 sub: crash analysis
    .5 section: main calc loop
    .6 section: stop
    .7 section: calc new pos, speed
    .8 section: calc gravity acc, overall acc
    .9 section: calc fuel used, burn acc

## Registers
Notes:
* '>' Lander registers user can manipulate
* '\' statistics registers (may lose content when using stat functions)

List:

    0   mf - fuel left (kg)
    1 > th - throttle (%)
    2\> t - burn time (s)
    3\> b - burn angle (elevation, +=up) (deg)
    4\  h - height (m) (int: R)
    5\  d - range (km) (int: delta)
    6\  v - velocity/vertical (km/h)
    7\  a - velocity/horiz (km/h)
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
    23 mf0 - initial fuel mass (kg)

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
- [DM15_M1B.txt](DM15_M1B.txt): can be uploaded via the serial link (see also [firmware](extern/swissmicros/firmware.txt) and [instructions](extern/swissmicros/instructions.php.txt))
- [code_dump.txt](code_dump.txt): decoded program, line per line; sometimes contains comments
- [mnemonic.txt](mnemonic.txt): equivalent program in mnemonic form. note that this file /might/ be more recent than the DM file.
- [HP.xml](HP.xml): simple Notepad++ syntax highlighter

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
- change speed output system (from v/elev to vert/hor)

## v0.2 (2014-09-27)
- "bug in firmware" appears to be a bug in my head (ENTER/RCL issue, see UM P36)

## v0.1 (2014-09-21)
- test version for SwissMicro HTML decoder issue

## DONE

## BUSY

## TODO

- interrupt flag
- add input checks
- abort option
- ascent phase
- review units? (use US system?)
- change the pitch coordinates (0=up)
- confirm thrust data
- add some screenshots of the logic
- add total time counter
- add scoring
   - within X km of planned touchdown
   - with minimum fuel used
- compute output after init
- investigate why there is no need for an 1/2 a sqr(t) term (section .7 uses symplectic, giving a.sqr(t) )
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
      - [Verlet](https://en.wikipedia.org/wiki/Verlet_integration)
      - RK4?
   - handling
      - renumber variables according to keyboard layout

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

# Simulation
Very interesting pages, might prove my implementation is a joke, anyway, here you are... I didn't compare the results (yet).
1. http://www.braeunig.us/apollo/LM-descent.htm
1. http://www.braeunig.us/apollo/LM-ascent.htm
