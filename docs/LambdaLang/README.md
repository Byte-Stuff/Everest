# LambdaLang

## Definition

LambdaLang is the language running on the "CPU"
The Base is the hard coded part (in scratch), e.g: The MOV command

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

!> **Warn** \
User processes CANNOT execute the ICF for security reasons.
Instead you should ask the OS to do it for you.

## Addresses

The memory is structured in multiple lists, and addresses are translated accordingly

| Start | End | List | CPU mode access |
| :---: | :---: | --- | :---: |
| 0 | 200k | Memory | User (with MMU paging) |
| 200k | 400k | Page table | Kernel |
| 400k | 600k | NewScreenBuffer | Kernel |
| 600k | 1M | VRAM | Kernel |
| 1M | 1.2M | Internal Disk | Kernel |
| 1.2M | 1.4M | External Disk | Kernel |

?> **Notice** \
The MMU doesn't cover anything other than memory, making page tables for anything above 200k is useless

!> **Warn** \
Trying to write to any memory not paged in the page table beforehand (in user mode) will result in an error, as well an in an interrupt

## Display

The display is structured in 4 lists:
- The 'OldScreenBuffer' list
- The 'NewScreenBuffer' list
- 2 VRAM lists

### The 'OldScreenBuffer' list

This list stores what's already on screen, only used to refer with the 'NSB' list.

### The 'NewScreenBuffer' list

This list stores what you want drawn on screen. It's able to be written to directly (see Addresses).

!> **Warn** \
Writing pixel per pixel here is rather slow, try loading to the VRAM first, and then load from the VRAM to the 'NSB'

### The 2 VRAM lists

These lists store other assets you can transfer to the 'NSB'
The Base can add, remove and edit VRAM items.
The VRAM is organised like this:

- name of item
- color data
- "-2" (end of data)

When passing data from the VRAM to the NSB, you pass 2 arguments:
- The name of the VRAM item
- The starting position in 
The Base looks for the name, and copies everything starting at the given position.
To translate x and y data to a position, use the TLP command
The color data can be RGB, RGBA, hex... Although there are 'special' colors:
- "-1" (empty, for having holes in your image)
- "-2" (end of data)
- "n[+ or -][3 digit number]" (newline, along with an offset for the next line, can be negative)
And of course, a delimiter: ';'

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