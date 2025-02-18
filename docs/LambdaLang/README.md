# LambdaLang

## Definition

LambdaLang is the language running on the "CPU"

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