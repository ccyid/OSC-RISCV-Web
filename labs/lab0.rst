========================
Lab 0: Environment Setup
========================

*************
Introduction
*************
In Lab 0, you need to prepare the environment for future development.
You should install the target toolchain, and use them to build a bootable image for VF2.

*****************
Goals of this lab
*****************

* Set up the development environment.
* Understand what's cross-platform development.
* Test your VF2.

.. important::
  This lab is an introductory lab.
  It won't be part of your final grade, but you still need to finish all ``todo`` parts,
  or you'll be in trouble in the next lab.

***************************
Cross-Platform Development
***************************

Cross Compiler
##############

VF2 uses the StarFive JH-7110 System-on-Chip (SoC) with a 64-bit RISC-V processor.
To compile your source code to 64-bit RISC-V machine code, you need a cross compiler if you develop
on a non-RISC-V environment.

.. admonition:: Todo

    Install the cross compiler ``gcc-riscv64-unknown-elf`` on your host computer. #FIX (you can either install or build from source...)

#FIX Warning: recommend to do labs on linux

Linker
######

You might not notice the existence of linkers before.
It's because the compiler uses the default linker script for you. (``ld --verbose`` to check the content)
In bare-metal programming, you should set the memory layout yourself.

This is an incomplete linker script for you.
You should extend it in the following lab.

.. code-block:: 

  SECTIONS
  {
    . = 0x80200000;
    .text : { *(.text) }
  }


QEMU
####

In cross-platform development,
it's easier to validate your code on an emulator first.
You can use QEMU to test your code first before validating them on a real VF2.

.. warning::
  QEMU provides a machine option ``virt`` (general machine) #FIX , it doesn't behave the same as a real VF2.
  You should validate your code on your VF2, too.

.. admonition:: Todo

    Install ``qemu-system-riscv64``.


********************************
From Source Code to Kernel Image
********************************

You have the basic knowledge of the toolchain for cross-platform development. Now, it’s time to practice them.

From Source Code to Object Files
################################

Source code is converted to object files by a cross compiler.
After saving the following assembly as ``a.S``,
you can convert it to an object file by ``riscv64-unknown-elf-gcc -c a.S``.

.. code-block::

  .section ".text"
  _start:
    wfi
    j _start

From Object Files to ELF
########################

A linker links object files to an ELF file.
An ELF file can be loaded and executed by program loaders.
Program loaders are usually provided by the operating system in a regular development environment.
In bare-metal programming, ELF can be loaded by some bootloaders.


To convert the object file from the previous step to an ELF file,
you can save the provided linker script as ``linker.ld``, and run the following command.

.. code-block::

  riscv64-unknown-elf-ld -T linker.ld -o kernel.elf a.o

From ELF to Kernel Image
########################

VF2's bootloader can't load ELF files.
Hence, you need to convert the ELF file to a raw binary image.
You can use ``objcopy`` to convert ELF files to raw binary.

.. code-block:: 

  riscv64-unknown-elf-objcopy -O binary kernel.elf kernel.bin

Check on QEMU
#############

After building, you can use QEMU to see the dumped assembly.

.. code-block::

  qemu-system-riscv64 -M virt -kernel kernel.bin -display none -d in_asm

.. admonition:: Todo

    Build your first kernel image, and check it on QEMU.

*******************
Deploy to REAL VF2
*******************

From Kernel Image to FIT Image
##############################

install mkimage

sudo apt-get install u-boot-tools

kernel.its requires the following content:
kernel.bin
jh7110-starfive-visionfive-2-v1.3b.dtb
initramfs.cpio #FIX make sure if it's required.

mkimage -f src/kernel.its kernel.fit 

fit get... #FIX

Flash Bootable Image to SD Card
###############################

To prepare a bootable image for VF2, you have to prepare at least the following stuff.

* A specific configured FAT16/32 partition contains #FIX

  * Kernel FIT image (kernel.fit)
  * U-boot config file (vf2_uEnv.txt)

There are two ways to do it.

1.
  We already prepared a `bootable image
  <https://github.com/nycu-caslab/OSC2024/raw/main/supplement/vf2-sdcard.img>`_. #FIX

  You can use the following command to flash it to your SD card.

  .. code-block::

    dd if=vf2-sdcard.img of=/dev/sdb

  .. warning:: /dev/sdb should be replaced by your SD card device. You can check it by `lsblk`

  It's already partitioned with firmware inside and contains a FAT32 filesystem.
  You can mount the partition to check.

2.
  You can also partition the disk and prepare the booting firmware yourself. See https://hackmd.io/@chiahsuantw/vf2-sdcard for details. #FIX
  
.. admonition:: Todo

    Use either one of the methods to set up your SD card.

Interact with VF2
##################

In our provided bootable image, it contains a kernel image that can echoes what you type through UART.
You can use it to test if your Lab kits function well.

1. If you use method 2 to set up your bootable image, you should download `kernel.bin <https://github.com/nycu-caslab/OSC2024/raw/main/supplement/kernel.bin>`_
, and put it into your boot partition. It's identical to the one in the provided bootable image. #FIX

2. Plug in the UART to USB converter to your host machine, and open it through a serial console such as screen or putty with the correct baud rate.

3. Connect TX, RX, GND to the corresponding pins on VF2, and turn on your VF2. You can follow the picture below to set up your UART.

4. After your VF2 powers on, you can type some letters, and your serial console should print what you just typed.

.. code-block::

  sudo screen /dev/ttyUSB0 115200

.. image:: images/UART.png #FIX

*********
Debugging
*********

Debug on QEMU
#############

Debugging on QEMU is a relatively easier way to validate your code.
QEMU could dump memory, registers, and expose them to a debugger.

.. admonition:: Todo

    Install ``gdb-multiarch`` on your host computer.

You can use the following command waiting for gdb connection.

.. code-block::

  qemu-system-riscv64 -M virt -kernel kernel.bin -display none -S -s

Then you can use the following command in gdb to load debugging information and connect to QEMU.

.. code-block::

  file kernel.elf
  target remote :1234

Debug on Real VF2
##################

You could either use print log or JTAG to debug on a real VF2.
We don't provide JTAG in this course, you can try it if you have one.