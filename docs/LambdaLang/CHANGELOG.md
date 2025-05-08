# Changelog

This changelog uses the dd/mm/yyyy format

## REV 223 08/05/2025

### Added
- SPP instruction
- JLE instruction
- SPT instruction
- LPT instruction
- New custom exception screen, able to be triggered via an interrupt. It is able to contain 4 lines of errors
- EFI Boot info to the documentation (still to be coded)
- verbose flag at address 199996
- Boot sound
- Automatic character grid size based on screen size

### Changed
- How jump instructions work, should be more reliable
- EFI boot process

### Fixed
- MMU, now adjusted to new memory scheme

## REV 211 12/04/2025

### Added
- New LTO instruction, works like Letter Of, see [here](./README.md#other)
- New LGO instruction, works like Length Of, see [here](./README.md#other)

### Changed
- New display writing uses memory addresses 199999 -> 198298 see [Firmware reserved addresses](./README.md#firmware-reserved-addresses)

### Removed
- Old display writing

## REV 207 10/04/2025

### Added
- Temporary new way to write to the screen, using character data stored in memory like this: \
Character/CharColor/CharBrightness/BackColor/BackBrightness
Example: A000+100000-100 for white A with black background \
UNFINISHED!!!

### Changed
- Back color and brightness of letters can be set

### Fixed
- Back color of letters in Firmware Display mode being in the wrong place

### DEPRECATED
- Old writing mechanism

## REV 204 05/04/2025

### Added
- New CPU halted screen

### Changed
- Firmware Font VGA -> Terminus
- Firmware exception script no longer runs without screen refresh
- Firmware exception script takes care of halting
- CPU loop now checks if CPU halted (new system)