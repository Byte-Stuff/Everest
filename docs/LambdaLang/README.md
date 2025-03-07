# LambdaLang

## Definition

LambdaLang is the language running on the "CPU"
The Base is the hard coded part (in scratch), e.g: The MOV command

F-Recovery (or Firmware Recovery), is an error screen that the Firmware triggers when invalid code is detected while an interrupt is triggered. It can also be called.

## CPU modes

The CPU has a few modes it keeps track of, most notably:
- Kernel mode: this keeps track of if the CPU is in User or Kernel mode
- Firmware mode: this tells the CPU that the firmware is still in control, and that no program took over yet, as such the Firmware will handle interrupts.

## Command list

| Command name  | Argument 1 | Argument 2 | Description | CPU mode |
| --- | --- | --- | --- | --- |
| MOV | Value to be moved | Destination (see addresses) | Move the value Argument 1 to Argument 2 | Depends on the destination |
| ADI | Start Value | End Value | Add the end value to the start value | User |
| PSH | Value | None | Push the value to the stack | User |
| POP | Destination | None | Move the stack to the destination | User |
| CMP | Value 1 | Value 2 | Checks if value 1 is <, >, or = and stores in the CMP register | User |
| JMP | Destination | None | Jumps to the destination | User |
| JIL | Destination | None | Jumps to the destination if CMP is less | User |
| JIE | Destination | None | Jumps to the destination if CMP is equal | User |
| JIG | Destination | None | Jumps to the destination if CMP is greater | User |
| ITR | None | None | Triggers an interrupt | User |
| RET | None | None | Returns last jump | User |
| IRR | None | None | Returns last interrupt | Kernel |
| HLT | None | None | Halt CPU | Kernel |
| REF | None | None | Refresh Screen | Kernel |
| CPV | VRAM Item | Position | Move the VRAM item to the position in NSB (see Display) | Kernel |
| TLP | X | Y | Translate X & Y data to a position on the screen | Kernel |
| ICF | FileData | posoffset | Places a file's code at the current code address, plus the offset | Kernel |

!> **Warning** \
User processes CANNOT execute the ICF instruction for security reasons.

Instead you should ask the OS to do it for you.

## Addresses

### Memory addresses (general)

The memory is structured in multiple lists, and addresses are translated accordingly

| Start | End | List | CPU mode access |
| :---: | :---: | --- | :---: |
| 0 | 200k | Memory | User (with MMU paging) |
| 200k | 400k | Page table | Kernel |
| 400k | 600k | NewScreenBuffer | Kernel |
| 600k | 1M | VRAM | Kernel |
| 1M | 1.2M | Internal Disk | Kernel |
| 1.2M | 1.4M | External Disk | Kernel |

### Firmware reserved addresses

| Address | Use | Mode |
| :---: | :---: |
| 1 | action to perform by the firmware | 0 |
| 1 | codeMemory address for the Kernel's Interrupt handler | 1 |
| 2 | General argument 1 | 0 |
| 3 | General argument 2 | 0 |
| 4 | General argument 3 | 0 |
| 5 | General argument 4 | 0 |

?> **Notice** \
The MMU doesn't cover anything other than memory, making page tables for anything above 200k is useless

!> **Warning** \
Trying to write to any memory not paged in the page table beforehand (in user mode) will result in an error, as well an in an interrupt

## Firmware Recovery

F-Recovery triggers when:
- An error interrupt is triggered while in Firmware Mode
- No boot file is found

F-Recovery has multiple images, which have different meanings:

- The prohibitory symbol:

<img src='/images/InvalidInvalid.png' width=25% height=25%></img>

An error interrupt has been triggered, and execution has been stopped

- The missing folder:

<img src='/images/Missing.png' width=25% height=25%></img>

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

Like any CPU, you can trigger an interrupt from LambdaLang using the ITR instruction. This will stop the code, save it to the InterruptStack. Interrupts are separated in two different usage methods:

### Firmware Interrupt

In this case, the firmware itself is taking care of interrupts, and it's only available in the Firmware CPU mode. \
The firmare has a few addresses it keeps track of to handle those requests, from address 0, to address 10, are reserved for the Firmware, any invalid interrupt will immediately cause F-Recovery to trigger.

### Kernel Interrupt

When your program (or kernel), switches the CPU mode from Firmware to Kernel Control, the CPU will instead take up a memory address at address 1, to redirect the Program Counter to what your program put in that address. From there your program will take care of handling requests, and so it has to manage the memory taking itself.