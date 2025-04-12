# Changelog

This changelog uses the dd/mm/yyyy format

## Unreleased

### Added
- New LTO instruction, works like Letter Of, see ![here](./README.md#other)
- New LGO instruction, works like Length Of, see ![here](./README.md#other)

### Changed
- New display writing uses memory addresses 199999 -> 198918 see ![Firmware reserved addresses](./README.md#firmware-reserved-addresses)

### Removed
- Old display writing

## REV 207 10/04/2024

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

## REV 204 05/04/2024

### Added
- New CPU halted screen

### Changed
- Firmware Font VGA -> Terminus
- Firmware exception script no longer runs without screen refresh
- Firmware exception script takes care of halting
- CPU loop now checks if CPU halted (new system)