# Lambda & LambdaLang

## Definition

LambdaLang is the language running on Lambda, the 'CPU'

F-Recovery (or Firmware Recovery), is an error screen that the Firmware triggers when a specific interrupt is triggered. It can also trigger itself when it detects bad arguments upon calling another interrupt.

## CPU modes

The CPU has a few modes it keeps track of:
- Kernel mode: this keeps track of if the CPU is in User or Kernel mode
- Firmware mode: this tells the CPU that the firmware is still in control, and that no program took over yet, as such the Firmware will handle interrupts.

## Instructions

All instruction where the arugment is noted as 'arg' means that it can be either a register or an arbitrary value

### Memory handling

| Instruction | Argument 1 | Argument 2 | Argument 3 | Description | CPU mode |
| --- | --- | --- | --- | --- | --- |
| MOV | arg | reg | None | Move argument 2 into argument 1 | User |
| LDI | reg | val | Offset | Load a value into reg | User |
| LDM | reg | addr | offset | Load from general memory to reg | User |
| STM | addr | arg | offset | Store arg to general memory | User |
| LDC | reg | addr | offset | Load from code memory to reg | Kernel |
| STC | addr | arg | offset | Write to code memory at addr | Kernel |
| LV1 | reg | addr | offset | Load from VRAM 1 to reg | Kernel |
| SV1 | addr | arg | offset | Store arg to VRAM 1 | Kernel |
| LV2 | reg | addr | offset | Load from VRAM 2 to reg | Kernel |
| SV2 | addr | arg | offset | Store arg to VRAM 2 | Kernel |
| LSB | reg | addr | offset | Load from screen buffer to reg | Kernel |
| SSB | addr | arg | offset | Store arg to screen buffer | Kernel |
| LID | reg | addr | offset | Load from internal disk addr to reg | Kernel |
| SID | addr | arg | offset | Store arg to internal disk addr | Kernel |
| LXD | reg | addr | offset | Load from external disk addr to reg | Kernel |
| SXD | addr | arg | offset | Store arg to external disk addr | Kernel |
| LPT | reg | addr | offset | Load from PageTable addr to reg | Kernel |
| SPT | addr | arg | offset | Store arg to PageTable addr | Kernel |
| PSH | arg | Offset | Push the value to the stack | User |
| POP | reg | Offset | Move the stack to the destination | User |
| SSP | val | offset | None | Change the stack pointer to val | Kernel |
| SFP | val | offset | None | Change the frame pointer to val | Kernel |
| SPP | val | offset | None | Change the PageTable pointer to val | Kernel |
| GWV | reg | val | Get real-world value | Kernel |

?> **Tip** \
Please keep in mind that, while the STM and LDM Instructions are available in User mode, they WILL be controlled by the MMU

!> **Warning** \
Trying to write to any memory not paged in the page table beforehand (in user mode) will result in an error, as well as an interrupt

### Mathematical Operations

| Instruction | Argument 1 | Argument 2 | Description | CPU mode |
| --- | --- | --- | --- | --- |
| ADD | reg | reg | Add the end reg to the start reg | User |
| SUB | reg | reg | Subtract the end reg to the start reg | User |
| MUL | reg | reg | Multiply the end reg with the start reg | User |
| DIV | reg | reg | Divide the end reg with the start reg | User |
| MOD | reg | reg | Modulo the end reg with the start reg | User |
| INC | reg | val | Increment the start reg with the end value | User |
| ADD | reg | reg | Decrement the start reg with the end value | User |

### Jumping

| Instruction | Argument 1 | Argument 2 | Description | CPU mode |
| --- | --- | --- | --- | --- |
| CMP | reg | reg | Updates CPU flags based on registers | User |
| JMP | reg | None | Jumps to reg | User |
| JEQ | reg | None | Jumps to reg if Z flag is 1 | User |
| JNE | reg | None | Jumps to reg if Z flag is 0 | User |
| JGT | reg | None | Jumps to reg if C flag is 0 and Z flag is 0 | User |
| JLT | reg | None | Jumps to reg if C flag is 1 and Z flag is 0 | User |
| JGE | reg | None | Jumps to reg if C flag is 0 OR Z flag is 1 | User |
| JLE | reg | None | Jumps to reg if C flag is 1 OR Z flag is 1 | User |
| JZE | reg | None | Jumps to reg if Z flag is 1 | User |
| RET | None | None | Returns last jump | User |

### Other

| Instruction | Argument 1 | Argument 2 | Argument 3 | Description | CPU mode |
| --- | --- | --- | --- | --- | --- |
| ITR | None | None | None | Triggers an interrupt | User |
| IRR | None | None | None | Returns last interrupt | Kernel |
| HLT | None | None | None | Halt CPU | Kernel |
| REF | None | None | None | Refresh Screen | Kernel |
| CPV | reg | Position | None | Move the VRAM item to the position in NSB (see Display) | Kernel |
| LTO | reg | reg | reg | Letter of: Moves the letter Argument 1 from the text Argument 2 into Argument 3 | User |
| LGO | reg | reg | None | Length of: Takes the length of Argument 1 and puts it in Argument 2 | User |

## Firmware reserved addresses

Mode 0 addresses only apply when the Firmware is still in control
Mode 1 addresses only apply when the Kernel is in control
See [Here](./README.md#cpu-modes)

| Address | Use | Mode |
| :---: | :---: | :---: |
| 200000 | action to perform by the firmware | 0 |
| 200000 | codeMemory address for the Kernel's Interrupt handler | 1 |
| 199990 -> depends on screen size, given by EDA | Store characters on screen | 0 & 1 |

## Display

The display is structured in 4 lists:
- The 'OldScreenBuffer' list
- The 'NewScreenBuffer' list
- 2 VRAM lists

### The 'OldScreenBuffer' list

This list stores what's already on screen, only used to refer with the 'NSB' list.

### The 'NewScreenBuffer' list

This list stores what you want drawn on screen. It's able to be written to directly.

!> **Warning** \
Writing pixel per pixel here is rather slow, try loading to the VRAM first, and then load from the VRAM to the 'NSB'

### The 2 VRAM lists

These lists store other assets you can transfer to the 'NSB'
Lambda can add, remove and edit VRAM items.
The VRAM is organised like this:

- color data
- "-2" (end of data)

When passing data from the VRAM to the NSB, you pass 2 arguments:
- The position where the item in VRAM starts
- The starting position

!> Warning: \
A VRAM item cannot start at a different list than where it ends, as it isn't currently supported!

Lambda looks for the name, and copies everything starting at the given position. \
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
The firmare keeps track of certain registers to handle those requests, any invalid interrupt will immediately cause F-Recovery to trigger.

#### Firmware Interrupts

| Register 1 | Used registers | Description |
| :---: | --- | --- |
| 1 | 2-4 | Write text on screen |

### Kernel Interrupt

When your program (or kernel), switches the CPU mode from Firmware to Kernel Control, the CPU will instead take up a memory address at address 1, to redirect the Program Counter to what your program put in that address. From there your program will take care of handling requests, and so it has to manage the memory taking itself.

## EDA

The EFI Data Area is where the Firmware places system information at the disposal of any Operating System it stores these:
- Total memory size [199989]
- Screen Size [199999 & 199998]
- Character Grid Size [199997 & 199996]
- Boot Flags [199995 -> 199990]

## EFI Boot Process

- Play boot chime (not yet done)
- EFI initialization 
- - Character Grid (Fallback Character grid used first, then dynamically redone based on screen size)
- - Screen (Sets the memory address for)
- - Memory

And then it all depends on if you press keys during boot

### Normal (no key pressed)

EFI loads the bootcode, and executes it

### Verbose (v key)

This one works ALONG any other mode, making it verbose (shows debug information)

### External Boot Mode (e key)

This mode makes you import your boot "drive" before loading and executing that instead, treat it like booting from a USB or CD/DVD

### Option Changer (O key)

If for whatever reason, the EFI fails to auto detect memory or screen size, you can use this menu to manually change them, be careful, as it means that switching it from auto to manual mode, and imputing wrong information WILL cause bad results

### Disk Export Mode (D key)

If you need to export your internal storage, boot with this mode

### Firmware Flasher Mode (F key)

This mode allows the rewriting PART of the firmware.

?> **Don't worry!** \
You cannot completely brick everything as you can just rewrite it if things go south, but please keep in mind that the Firmware has access to EVERYTHING unrestricted.