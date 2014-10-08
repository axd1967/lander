2D lander (HP 15C)

STATUS: PROTOTYPE, INCOMPLETE, not fully tested yet. Be patient.

# Aim

2D variant of the classic (1D) lander calculator app.

Simple formulas used ("Euler symplectic"), don't expect stable orbits.

## Run
1. Select a scenario (B, see 'Labels' below) to initialise data.
1. Enter following data:
   - STO 1: throttle (0..1)
   - STO 2: burn time (s)
   - STO 3: burn angle above horizon (deg)
1. Press 'A' to run the burn time. Calculator will run in predefined time segments (r14) 
until burn time has been consumed.
1. Intermediate (PSE) output:
   - remaining fuel (kg)
   - remaining time (s)

Output:

    T: hor speed (km/h)
    Z: vertical spd (km/h)
    Y: range (km)
    X: altitude (m)

## Apollo profile

### Descent 
* From: 50kft (15.24km)
* Target: 260NM (480km) (~12')
* 6': 100%
* 2': 50%
* 4': 50%-25%
      
### Ascent
* Target: alt: 60kft (18.24km) (~level) @range: 167NM (309km) (~7')
* roughly circular, the real orbit was slightly elliptic
      
## MEM reports

This app was implemented on a Swiss Micros DM15_M1B_V16 (max memory variant=230 reg).

* DM15     : (19  46 00-0)
* DM15_M80 : (19 110 00-0)
* DM15_M1B :  23 158 49-2 -> currently can't fit on a classic 15C

Note: these values might not have been updated with every latest commit.

## Labels

    A main routine
    B init lander descent (See A11 profile for details)
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

## Registers

'>' Lander registers user can manipulate
'\' statistics registers (may lose content when using stat functions)

    0  mf - fuel left (kg)
    1  > th - throttle (%)
    2\ > t - burn time (s)
    3\ > b - burn angle (elevation, +=up) (deg)
    4\ h - height (m) (int: R)
    5\ d - range (km) (int: delta)
    6\ v - velocity/vertical (km/h)
    7\ a - velocity/horiz (km/h)
    8  px (m)
    9  py (m)
    .0  vx (m/s)
    .1  vy (m/s)
    .2  ax (m/s2)
    .3  ay (m/s2)
    .4  dt0 - delta t preset (s)
    .5  g0 - gravity, surface (m/s2)
    .6  r0 - central body radius (m)
    .7  dt - delta t (s)
    .8  fu - fuel used for burn (kg)
    .9  T - remaining time in calculation loop (s)
      
    (indirect:)
    20 f - max fuel flow (kg/s)
    21 F - max thrust (N)
    22 m0 - vehicle mass, dry (kg)
    23 mf0 - initial fuel mass (kg)

## Flags

    1 fuel empty
    2 - (TODO - interrupt)
    3 -
    4 -
    5 -
    6 -
    7 -
    8 -
    9 (TODO) landed/crashed?
 
## About the files

The dumps have been generated with [SwissMicro](http://www.swissmicros.com/) (modified, see  /vendor/swissmicros/master) [encoding/decoding scripts](/extern/swissmicros/decode RAM dump.htm).
- [DM15_M1B.txt](DM15_M1B.txt): can be uploaded via the serial link (see also [firmware](extern/swissmicros/firmware.txt) and [instructions](extern/swissmicros/instructions.php.txt))
- [code_dump.txt](code_dump.txt): decoded program, line per line; sometimes contains comments
- [mnemonic.txt](mnemonic.txt): equivalent program in mnemonic form. note that this file /might/ be more recent than the DM file.
- [HP.xml](HP.xml): simple Notepad++ syntax highlighter

# RELEASE HISTORY

## v0.2 (2014-09-27)
- "bug in firmware" appears to be a bug in my head (ENTER/RCL issue, see UM P36)

## v0.1 (2014-09-21)
- test version for SwissMicro issue

## DONE

- check routines in Earth orbit
- range bug/velocity convert
- remove Earth stuff
- add Apollo profile
- lander
- change speed output system

## BUSY
- markdown

## TODO

- add crash detection
- interrupt flag
- add input checks
- review criteria
- abort option
- review units
   - output: option for ft/nm units
- add some screenshots of the logic
- add total time counter
- compute output after init
- optimizations
   - better use of LST X
   - fit into 15C memory (19 46 0-0)
   - precomputed factors?
   - better approximations (RK4?)
   - renumber variables according to keyboard layout

# NOTES
1. BST in run mode should backstep repeatedly when held
1. RCL (i) should have a variant RCL (x); both should *consume* X (but I'm RPL minded... see also http://www.hpmuseum.org/cgi-sys/cgiwrap/hpmuseum/archv017.cgi?read=116747)
1. some sequences that bit me as an RPL user (and I wonder how novices dealt with it):
   - assuming the stack looks like this: T:1 Z:2 Y:3 X:4, then keying
   - 888 [X<>Y] 999 
      - expect:  T:2 Z:3   Y:888 X:999
      - get:     T:3 Z:888 Y:4   X:999
   - [x^2] [ENT] [RCL] <r> 
      - expect:  T:3 Z:16 Y:16 X:[r] 
      - get:     T:2 Z:3  Y:16 X:[r] (Owner's Hb, Ed 2.4, p.36)
