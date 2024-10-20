# OMT Framework Decompilation

AWE Games (aka AWE Productions) created some movie-based games in the early 2000's. They apparently used an in-house engine based on a framework called OMT. This document details the research found when attempting to extract the assets from titles using this engine.

Known titles:

- Jimmy Neutron Boy Genius (2001, PC)
- Jimmy Neutron vs Jimmy Negatron (2002, PC)
- SpongeBob SquarePants: Battle for Bikini Bottom (2003, PC)

## ASE

Dynamic 3D models use 3DS Max ASCII Export (*.ase) as the file format.

## GAM

Appears to be a file containing data from a level editor, currently unknown how to interpret.

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
