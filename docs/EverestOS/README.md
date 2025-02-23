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
- - - /Core
- - - - kernel.ll
- - - - esfs.ll
- - - - esfs.fish
- - - /Components
- - - - /Gemstone
- - - - - gstone.ll
- - - - - /icons
- - - - /WowChestra
- - - - - wowchestra.ll
- - /drv
- - - display.ll
- - - keyb.ll
- - - mouse.ll
- /Libraries
- /Users

### System Files

| File | Description |
| :---: | --- |
| boot.ll | Bootloader |
| boot2.ll | Bootcode of the OS |
| kernel.ll | The Everest Kernel |
| display.ll | display driver |
| keyb.ll | keyboard driver |
| mouse.ll | mouse driver |
| gstone.ll | the Gemstone display server |
| wowchestra.ll | The window manager |


## Apps



### App file structure

app.app
- app.ll
- /Ressources
- - appicon.img