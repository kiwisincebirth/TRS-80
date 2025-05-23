# TRS-80

## Model 1 ROM Disassembled

This repository contains reverse engineered source code for Tandy TRS-80 Model 1 Level 2 BASIC ROMS. 

This source code has support for both V1.3 (the default), and V1.2 of the ROMS. For more information on the 
differences see references below. Several additional (optional) patches can be applied (e.g. FreHD autoboot)
be setting a #DEFINE in the code

### Motivation

I read a post asking why Model 1 BASIC hadn’t been ported to a modern hobbyist CP/M environment. Of course there are 
many reasons why this has not been done but one of the issues is (after doing a search) I could not locate original 
source code, which would be needed for such a port. I did locate several disassemblies, but none we adequately 
complete, and would 

I am unsure why the original source code has not been published (in its original form), since the ROM contents have 
been very heavily documented over the years. However, this repository aims to address this.

### Description

Main Features
* Fully Compilable Source Code for Model 1 Level 2 12KB ROMS

While based of a disassembly (which can be low quality) the following work has been done: 
* Replaced all disassembler generated labels with meaningful labels
* Ensured all jumps (JR and JP) reference valid code labels.
* Replaced all $3xxx hardware references with `.EQU` definitions
* Replaced all $4xxx buffer references with `.EQU` definitions, with a single `.EQU $4000` reference
* Replaced all generated op-code with Byte `.DB` definitions for DataTables/Text/Constants/Etc
* Added code documentation from various sources at the code block / function level
* Replaced incorrect op-codes, where Better Else "Trick" was used. See Reference below.

On the last point I was unaware of these optimisations until I worked on this code.

### Build and Test

This source code has been compiled with Telemark Assembler, and tested using a DIFF tool to ensure binary 
compatibility of the generated output. There are several `DEFINE`'s that can be set
in the code (very start) to enable certain features

By default the build will create a Version 1.3 of the TRS-80 ROM. Version 1.2 is also supported, 
but V1.1 and prior versions are not supported

The base ROM version can be defined.
* DEFINE VER12 - uncomment tis Define for V1.2 of the ROM.
* DEFINE EACA80 - Used to enable Dick Smith System-80 (EACA) hardware support. 
  NOTE: While not mandatory you should also specify `VER12` since the System-80 ROM was based on V1.2.
  V1.3 has not been formally tested, but assume should work, and opens ability to also specify `FREHD`  

There are several optional features.
* DEFINE FREHDBT - Enables the FreHD auto boot feature, i.e. the Auto boot ROM. This implies a Version 1.3 
  ROM as a base, please do not DEFINE VER12. 
  Consider also enabling NMIHARD to ensure reset (on non-floppy machine) will force a reset.
* DEFINE NMIHARD - Set NMI (reset) as always perform a hard reset. Normally on non-floppy systems NMI performs
  a soft reset returing to the `READY>` prompt with the basic program intact. This is useful in system without 
  floppy disk to force a full reset (0066h)
* DEFINE LOWCASE - Disable Alpha character translation of letters A-Z,a-z to the values on range 00h to 1Fh. 
  This is useful when a lower case mod is installed, but an alternate video driver has not been installed, 
  or where the font rom on the machine has the alternate characters in the 00h 1Fh range (0471h)

Some additional defines, which are build options rather than features
* DEFINE SIZE16K - Will pad the end of the rom with $FF to 16KB size. Use if want to append multiple ROM 
  images for used in large paged rom
* DEFINE DONTEND - Disable `.END` directive if `#INCLUDE`ing the source inside another file.
* DEFINE _EMBED - Experimental, used to strip all HW, and IO routines leaving just BASIC language as standalone code 

### Example Usage

I have created an implementation of Level 2 Basic that runs as an executable CP/M 2.2 (or greater). 

Switch to the branch "cpmbasic" which takes this source code and you will find TBASIC.Z80 which will
comple to a CP/M COM executable file. It is not well documented (see intose the ASM file), it has many
limitatations but does work. And also includes the LOAD filename.bas which will load a TRS-80 basic program.
You do need to be careful though RAM starts at 3000H (not 4000H), so POKE's to what would normally be video
RAM would easily corrupt your program

### Contributing

If you wish to improve the quality of this source code (better documentation)I  would be happy to accept Pull Requests, 
please ensure you test that the build works using Telemark Assembler.

### Legal

The source code is NOT the original code, it is a derivative work assembled from multiple sources.

### References

Following References
* [http://www.trs-80.org/model-1-level-2-basic-rom-versions/] - different ROM Versions defined
* [https://www.classic-computers.org.nz/system-80/hardware_rom.htm] - Dick Smith System 80 (EACA) Rom Differences
* [https://www.trs-80.com/wordpress/roms/] - Very good source code documentation
* [https://gitlab.com/retroabandon/trs80i34-re] - The disassembly I based this work on.
* [https://rosettacode.org/wiki/Category:Z80_Assembly#Inlined_bytecode] - Better Else Z80 optimisation
* [https://wikiti.brandonw.net/index.php?title=Z80_Optimization#Better_else] - Better Else optimisation
* [https://www.cpcalive.com/docs/TASMMAN.HTM] - Telemark Assembler
* Microsoft BASIC Decoded & Other Mysteries - James Farvour

