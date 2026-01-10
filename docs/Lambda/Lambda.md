# Lambda

# Definition

Lambda is the "hardware" of the Ever project, it acts as the whole computer, aka the CPU, Memory, GPU, screen, etc...
It is powered by LambdaLang, a language close to assembly. To optimize the available space and to make it simpler, Lambda does not work with binary, instead the CPU understands plaintext as is and can run it.

# Hardware

## CPU

The processor of the Lambda System, each instruction can have up to 3 arguments.
It has 16 General Register. It can handle text as is without converting to numbers, and has a few security features.
 
## Memory

The Lambda Systems have 200000 blocks of available memory, it can be expanded further if the scratch list length limit is removed or if more memory lists are added. (To be experimented)

## Video

The Lambda Systems have 200000 blocks of memory for whats currently on screen, along with 200000 blocks of memory for the video buffer of what will be on screen.
There also is 2 cells of 200000 blocks of video memory (VRAM)

## Storage
 
Lambda based systems include 200000 blocks of storage which can be extracted for later use, as well as a 200000 block external "volatile" memory for easily installing software.

# Software

## EFI/Firmware

Lambda systems come with a two parted firmware package:
- The "immutable" firmware is baked inside scratch and hard-coded, it allows the firmware to be recovered and assures the security of the other part
- The "flashable" firmware is the first bit of code that runs, it can be flashed to change its behavior