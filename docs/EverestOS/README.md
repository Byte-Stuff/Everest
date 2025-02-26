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

- The firmware loads the first address in the Internal storage (boot.ll)
- boot.ll starts tmpfs.ll, and uses it's partition table to find the main partition, and load boot2.ll
- boot2.ll cleans boot.ll out of memory, and uses the already loaded partition table to find and execute /Everest/System/Core/ekrnl.ll
- ekrnl.ll then starts booting the OS:
- - it cleans boot2.ll out
- - starts the OS environment
- - Switches the CPU to Kernel Interrupts
- - Loads /Everest/System/Core/efs.ll, and takes it's partition table, replacing the temporary one.
- - it sets up the bases for multitasking (e.g Memory Management, CPU Scheduling, Drivers...)
- - The Kernel then starts the Init process
- The Init process takes care of starting up the rest while the Kernel runs, mainly for starting up the GUI, background processes, it basically bootstraps the OS.