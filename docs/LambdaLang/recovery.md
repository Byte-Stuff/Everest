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