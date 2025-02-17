# LambdaLang

## Also a test


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