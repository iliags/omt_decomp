# OMT Framework Decompilation

AWE Games (aka AWE Productions) created some movie-based games in the early 2000's. They used an in-house engine based on a framework called OMT. This document details found when attempting to reverse engineer titles using this engine.

Known titles (not exhaustive):

- Jimmy Neutron Boy Genius (2001, PC)
- Jimmy Neutron vs Jimmy Negatron (2002, PC)
- SpongeBob SquarePants: Employee of the Month (2002, PC)
- SpongeBob SquarePants: Battle for Bikini Bottom (2003, PC)

## ASE

Dynamic 3D models use 3DS Max ASCII Export (*.ase) as the file format.

## GAM

Appears to be a file containing data from a level editor. There are text labels which can infer what type of data it is, but not necessarily what is valid input.

### Position/Rotation

Sample:

```txt
// From Level1.gam in Jimmy Neutron Boy Genius (2001, PC)

// Raw
50 6F 73 69 74 69 6F 6E 58 00 00 00 03 00 00 00 04 46 1A 37 C4 09
50 6F 73 69 74 69 6F 6E 59 00 00 00 03 00 00 00 04 41 FB 36 32 09
50 6F 73 69 74 69 6F 6E 5A 00 00 00 03 00 00 00 04 44 81 38 3A 09

// Partially translated
PositionX 00 00 00 03 00 00 00 04 46 1A 37 C4 09 // Most likely 0 with rounding error
PositionY 00 00 00 03 00 00 00 04 41 FB 36 32 09
PositionZ 00 00 00 03 00 00 00 04 44 81 38 3A 09

// Tentative Translation
PositionX 0.0
PositionY 0.0
PositionZ 0.0
```

It appears as though the format is little endian, and a floating point number (f32). The remainder part of the float (example from x position: `46 1A 37 C4`) is likely a rounding error from the original software.

## GRN

Some titles use Granny3D from RAD Game tools, files use the *.grn extension.

## OMT

A general container format which holds sounds, textures, and perhaps static 3D models.

### Audio

QuickBMS script to extract wav files:

```bms
endian big
idstring "0MF2"
get OFFSET long
goto OFFSET
findloc OFFSET string "Wave"
goto OFFSET
getdstring NAME 4
get FILES long
for i = 0 < FILES
    get ID long
    get OFFSET long
    get SIZE long
    get NAMESZ byte
    getdstring NAME NAMESZ
    log NAME OFFSET SIZE
next i
```

Rename extracted files to have a *.wav extension and they should open in any media player.

### Textures/Models

Extract texture/model information:

```bms
endian big
idstring "0MF2"
get OFFSET long
goto OFFSET
findloc OFFSET string "Canv"
goto OFFSET
getdstring NAME 4
get FILES long
for i = 0 < FILES
    get ID long
    get OFFSET long
    get SIZE long
    get NAMESZ byte
    getdstring NAME NAMESZ
    log NAME OFFSET SIZE
next i
```

The newly extracted files use an unknown format `OmGW` using an ID of `OmCv`. It is currently unknown how to convert these files into another format.
