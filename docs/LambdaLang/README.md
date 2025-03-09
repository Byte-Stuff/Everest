# LambdaLang

## Definition

LambdaLang is the language running on the "CPU"
The Base is the hard coded part (in scratch), e.g: The MOV command

F-Recovery (or Firmware Recovery), is an error screen that the Firmware triggers when invalid code is detected while an interrupt is triggered. It can also be called.

## CPU modes

The CPU has a few modes it keeps track of, most notably:
- Kernel mode: this keeps track of if the CPU is in User or Kernel mode
- Firmware mode: this tells the CPU that the firmware is still in control, and that no program took over yet, as such the Firmware will handle interrupts.

## Instructions

### Memory handling

| Instruction | Argument 1 | Argument 2 | Description | CPU mode |
| --- | --- | --- | --- | --- |
| LDM | reg | addr | Load from general memory to reg | User |
| STM | addr | reg | Store reg to general memory | User |
| LDC | reg | addr | Load from code memory to reg | Kernel |
| STC | addr | reg | Write to code memory at addr | Kernel |
| LV1 | reg | addr | Load from VRAM 1 to reg | Kernel |
| SV1 | addr | reg | Store reg to VRAM 1 | Kernel |
| LV2 | reg | addr | Load from VRAM 2 to reg | Kernel |
| SV2 | addr | reg | Store reg to VRAM 2 | Kernel |
| LSB | reg | addr | Load from screen buffer to reg | Kernel |
| SSB | addr | reg | Store reg to screen buffer | Kernel |
| LID | reg | addr | Load from internal disk addr to reg | Kernel |
| SID | addr | reg | Store reg to internal disk addr | Kernel |
| LXD | reg | addr | Load from external disk addr to reg | Kernel |
| SXD | addr | reg | Store reg to external disk addr | Kernel |
| PSH | reg | None | Push the value to the stack | User |
| POP | reg | None | Move the stack to the destination | User |

?> **Tip** \
Please keep in mind that, while thr SDM and LDM Instructions are available in User mode, they WILL be controlled by the MMU

!> **Warning** \
Trying to write to any memory not paged in the page table beforehand (in user mode) will result in an error, as well as an interrupt

### Mathematical Operations

| Instruction | Argument 1 | Argument 2 | Description | CPU mode |
| --- | --- | --- | --- | --- |
| ADD | reg | reg | Add the end value to the start value | User |

### Jumping

| Instruction | Argument 1 | Argument 2 | Description | CPU mode |
| --- | --- | --- | --- | --- |
| CMP | reg | reg | Updates CPU flags based on registers | User |
| JMP | reg | None | Jumps to reg | User |
| JEQ | reg | None | Jumps to reg if Z flag is 1 | User |
| JNE | reg | None | Jumps to reg if Z flag is 0 | User |
| JGT | reg | None | Jumps to reg if C flag is 0 and Z flag is 0 | User |
| JLT | reg | None | Jumps to reg if C flag is 1 | User |
| JGE | reg | None | Jumps to reg if C flag is 0 | User |
| JLE | reg | None | Jumps to reg if C flag is 1 and Z flag is 1 | User |
| JZE | reg | None | Jumps to reg if Z flag is 1 | User |
| RET | None | None | Returns last jump | User |

### Other

| Instruction | Argument 1 | Argument 2 | Description | CPU mode |
| --- | --- | --- | --- | --- |
| ITR | None | None | Triggers an interrupt | User |
| IRR | None | None | Returns last interrupt | Kernel |
| HLT | None | None | Halt CPU | Kernel |
| REF | None | None | Refresh Screen | Kernel |
| CPV | reg | Position | Move the VRAM item to the position in NSB (see Display) | Kernel |

## Firmware reserved addresses

| Address | Use | Mode |
| :---: | :---: | :---: |
| 1 | action to perform by the firmware | 0 |
| 1 | codeMemory address for the Kernel's Interrupt handler | 1 |
| 2 | General argument 1 | 0 |
| 3 | General argument 2 | 0 |
| 4 | General argument 3 | 0 |
| 5 | General argument 4 | 0 |

## Firmware Recovery

F-Recovery triggers when:
- An error interrupt is triggered while in Firmware Mode
- No boot file is found

F-Recovery has multiple images, which have different meanings:

- The prohibitory symbol:

![Invalid](../images/InvalidInvalid.png ':size=200')

An error interrupt has been triggered, and execution has been stopped, press the green flag to reboot

- The missing folder:

![Missing](../images/Missing.png ':size=210')

No startup code has been found in address 1 of the internal list

## Display

The display is structured in 4 lists:
- The 'OldScreenBuffer' list
- The 'NewScreenBuffer' list
- 2 VRAM lists

### The 'OldScreenBuffer' list

This list stores what's already on screen, only used to refer with the 'NSB' list.

### The 'NewScreenBuffer' list

This list stores what you want drawn on screen. It's able to be written to directly (see Addresses).

!> **Warning** \
Writing pixel per pixel here is rather slow, try loading to the VRAM first, and then load from the VRAM to the 'NSB'

### The 2 VRAM lists

These lists store other assets you can transfer to the 'NSB'
The Base can add, remove and edit VRAM items.
The VRAM is organised like this:

- color data
- "-2" (end of data)

When passing data from the VRAM to the NSB, you pass 2 arguments:
- The position where the item in VRAM starts
- The starting position

!> Warning: \
A VRAM item cannot start at a different list than where it ends, as it isn't currently supported!

The Base looks for the name, and copies everything starting at the given position. \
To translate x and y data to a position, use the TLP command \
The color data can be RGB, RGBA, hex... Although there are 'special' colors:
- "-1" (empty, for having holes in your image)
- "-2" (end of data)
- "n[+ or -][3 digit number]" (newline, along with an offset for the next line, can be negative) \
And of course, a delimiter: ';' (only when stored in the disk, they're just items in the VRAM list otherwise)

example: \
0;0;0;n-001; \
0;0;0;0;0;n+000; \
0;0;0;0;0;n+001; \
0;0;0;-2

would give this: \
□■■■□\
■■■■■ \
■■■■■ \
□■■■□

## Interrupts

Like any CPU, you can trigger an interrupt from LambdaLang using the ITR instruction. This will stop the code and save it to the Interrupt Stack.\
Interrupts are separated in two different usage methods:

### Firmware Interrupt

In this case, the firmware itself is taking care of interrupts, and it's only available in the Firmware CPU mode. \
The firmare has a few addresses it keeps track of to handle those requests, from address 0, to address 10, are reserved for the Firmware, any invalid interrupt will immediately cause F-Recovery to trigger.

### Kernel Interrupt

When your program (or kernel), switches the CPU mode from Firmware to Kernel Control, the CPU will instead take up a memory address at address 1, to redirect the Program Counter to what your program put in that address. From there your program will take care of handling requests, and so it has to manage the memory taking itself.