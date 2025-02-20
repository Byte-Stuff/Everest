# EverestOS

## Installation

# Inner Workings

## Bootloader

The EverBoot Bootloader is the first running piece of code. It scans the drive for the OS bootcode, usually stored as name: 'boot' in the Boot partition

## FileStructure

BOOT(partition)
- /boot
- - tmpfs.fish
- - boot.ll
- tmpfs.ll

ROOT(partition)
- /Everest
- - /boot
- - - boot2.ll
- - /System
- - - /sLib
- - - kernel.ll
- - /drv
- - - display.ll
- /Libraries
- /Users

## Apps



### App file structure

app.app
- app.ll
- /Ressources
- - appicon.img