# EverestOS

## Installation

# Inner Workings

## Bootloader

The EverBoot Bootloader is the first running piece of code. It scans the drive for the OS bootcode, usually stored as name: 'boot' in the Boot partition

## Kernel

The Everest Kernel (or ekrnl), is the base of the Operating System

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
- - - - ekrnl.ll
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
- /Recovery
- - /boot
- - - boot2.ll
- - /ressources
- - - rimg.pci
- /Registry
- - /System
- - - /Kernel
- - - - krnl.conf
- - - - init.conf

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

## The Registry

Instead of using files for configurations, it was decided to use a modified Windows-like approach: the Registry \
The Registry is a folder in the Root, it takes care of configurations, for example:\
/Registry/System/Kernel/krnl.conf, contains the configuration for the kernel.

### Main Registry keys

| File | Item | Description | Location |
| :---: | :---: | --- | --- |
| krnl.conf | krnlver | Kernel version number | System/Kernel/
| krnl.conf | safeshut | Last shutdown was performed correctly? | System/Kernel/
| krnl.conf | upd | did an update? Or has to continue? Revert? | System/Kernel/
| init.conf | dps | Display server location | System/Kernel/

## Apps



### App file structure

app.app
- app.ll
- /Ressources
- - appicon.img

## Boot Process

WIP