========================
Lab 2: Booting
========================

*************
Introduction
*************

Booting is the process of initializing the system environment to run various user programs after a computer reset. This includes loading the kernel, initializing subsystems, matching device drivers, and launching the initial user program to bring up remaining services in user space.

In Lab 2, you'll implement a bootloader for the VF2 board that loads kernel images through UART. Additionally, you'll develop a simple allocator and gain an understanding of initial ramdisk and device trees.

*****************
Goals of this lab
*****************

- Implement a bootloader that loads kernel images through UART.
- Implement a simple memory allocator.
- Understand the concept and usage of initial ramdisk.
- Understand the structure and purpose of device trees.

************
Background
************

The boot process on the VF2 involves multiple stages, including the execution of the bootloader, loading of the kernel, and initialization of system components. Understanding this process is crucial for developing low-level system software.

In this lab, you'll implement a bootloader that loads the actual kernel through UART, providing flexibility during development and debugging.

*****************
Basic Exercises
*****************

Basic Exercise 1 - Reboot - 10%
################################

The VF2 board may not have a dedicated reset button. Implementing a software-based reboot mechanism can be useful for development and testing purposes.

.. admonition:: Todo

    Implement a function to reboot the VF2 board by interacting with the appropriate system registers or invoking system calls.

Basic Exercise 2 - UART Bootloader - 30%
#########################################

Loading the kernel image onto the VF2 board can be streamlined by implementing a bootloader that receives the kernel over UART. This eliminates the need to physically move storage media between the host and the VF2 during development.

.. admonition:: Todo

    Develop a bootloader that listens for a kernel image transmitted over UART and loads it into memory for execution. Design a simple protocol for data transmission to ensure reliability.

Basic Exercise 3 - Initial Ramdisk - 30%
#########################################

An initial ramdisk (initrd) is a temporary root file system loaded into memory during the boot process. It provides essential files and drivers needed to mount the actual root file system.

.. admonition:: Todo

    Create an initial ramdisk containing necessary utilities and drivers for the VF2. Modify the bootloader to load this initrd into memory during the boot process.

Basic Exercise 4 - Simple Allocator - 10%
##########################################

Efficient memory management is fundamental in operating systems. Implementing a simple memory allocator will provide insight into dynamic memory allocation strategies.

.. admonition:: Todo

    Implement a basic memory allocator that manages dynamic memory allocation and deallocation for the kernel.

********************
Advanced Exercises
********************

Advanced Exercise 1 - Bootloader Self-Relocation - 10%
#######################################################

To accommodate different memory layouts and ensure compatibility, a bootloader may need to relocate itself during execution.

.. admonition:: Todo

    Modify the bootloader to support self-relocation, allowing it to move to a different memory region if necessary.

Advanced Exercise 2 - Device Tree - 30%
#######################################

A device tree is a data structure that describes the hardware components of a system. It provides the operating system with information about the available hardware without hardcoding details into the kernel.

.. admonition:: Todo

    Integrate device tree support into your bootloader. Parse the device tree to initialize hardware components appropriately during the boot process.
