#	MiSTer Input Tester

## Overview

A custom mini-system running an input test utility.  3 test screens available:
 - Digital - shows joypad buttons for first two input devices
 - Analog - shows analog axes for first two input devices
 - Advanced - shows all standard HPS sourced inputs (joystick, analog, paddle, spinner) simultaneously as well as a ps2 keyboard console

Mode switching instructions are shown at the bottom of the screen.

## Acknowledgements

Created by JimmyStones (http://github.com/jimmystones), with thanks to:
- alanswx (https://github.com/alanswx) for ideas, cross-platform simulation support, and the tutorial this core was based on
- Jotego (https://github.com/jotego) for his very tidy JTFRAME modules
- Porkchop Express (https://misteraddons.com/) for testing a bunch of devices I couldn't

## Hardware

A simple bespoke (i.e. I made it up with no real plan) 8-bit system with a 40x30 character display, each character can be set to one of 256 colours.  No sound capabilities.  All relevant MiSTer inputs are mapped into memory regions.

### Components
 - Z80 CPU (tv80 by Guy Hutchison, based on VHDL T80 core by Daniel Wallner)
 - jtframe_vtimer for VGA timing (from JTFRAME (https://github.com/jotego/jtframe) by Jotego)
 - jtframe_cen24 for clock enables (from JTFRAME (https://github.com/jotego/jtframe) by Jotego)
 - 16Kb program ROM (port 1 cpu, port 2 download)
 - 16Kb work RAM (port 1 cpu)
 - 2Kb character ROM (port 1 cpu, port 2 download)
 - 2Kb character RAM (port 1 cpu, port 2 graphics)
 - 2Kb colour RAM (port 1 cpu, port 2 graphics)
 - 7 Memory-mapped IO regions (all read-only).  _Yes I know about MREQ but SFRs turned out to be annoying in sdcc so :)_
   - Hardware state (H/V Sync, H/V Blank etc) (8 bytes)
   - joystick_5->0 from HPS (192 bytes)
   - joystick_analog_5->0 from HPS (96 bytes)
   - paddle_5->0 from HPS (48 bytes)
   - spinner_5->0 from HPS (96 bytes)
   - ps2_key from HPS (11 bytes)
   - ps2_mouse from HPS (48 bytes, combination of ps2_mouse and ps2_mouseext with some padding to align bytes) - currently unused

### Memory Map
Start|End|Length|Name
---|---|---|---
0x0000|0x3FFF|0x4000|Program ROM
0x4000|0x47FF|0x0800|Char ROM
0x6000|0x6000|0x0001|System inputs (video signals)
0x7000|0x70BF|0x00C0|Joystick inputs
0x7100|0x715F|0x0060|Analog inputs
0x7200|0x722F|0x0030|Paddle inputs
0x7300|0x735F|0x0060|Spinner inputs
0x7400|0x740B|0x000C|PS2 keyboard input
0x7500|0x752F|0x0030|PS2 mouse input
0x8000|0x87FF|0x0800|Char RAM
0x8800|0x8FFF|0x0800|Colour RAM
0xC000|0xFFFF|0x4000|Work RAM

## Software

/src contains a C program compiled to Z80 assembly using sdcc.  

All required ROMs (compiled program and default MiSTer font) are built into this core.  

Selecting Load BIOS in the core allows hot loading of binary OS ROM files for easier development.
Selecting Load Font in the core allows hot loading of MiSTer .pf font files (https://github.com/MiSTer-devel/Fonts_MiSTer).

### Building the OS

#### Required packages
- `sdcc` 
- `srecord`
#### Build steps
- Run `make` in `/src/Makefile` to create binary ROM
- Run `od -An -t x1 -v src/os.bin > rtl/rom.hex` to create hex version of ROM for Quartus build

## License
This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.
