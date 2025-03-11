========================
Lab 0: Environment Setup
========================

*************
Introduction
*************
In Lab 0, you need to prepare the environment for future development.
You should install the target toolchain and use it to build a bootable image for the VisionFive 2 (VF2) board.

*****************
Goals of this lab
*****************

* Set up the development environment.
* Understand cross-platform development.
* Test your VF2 board.

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

    Install the RISC-V unknown-elf toolchain on your Linux host computer.
    You can build the toolchain from source by following these steps:

    1. **Install the necessary dependencies:**

       On Ubuntu, execute the following command:

       ::
       
           sudo apt-get install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev ninja-build

    2. **Clone the RISC-V GNU toolchain repository:**

       ::
       
           git clone https://github.com/riscv-collab/riscv-gnu-toolchain
           cd riscv-gnu-toolchain

    3. **Configure and build the toolchain:**

       ::
       
           ./configure --prefix=/opt/riscv --with-arch=rv64gc --with-abi=lp64d
           make

       This process will install the toolchain binaries in the `/opt/riscv` directory.

    4. **Add the toolchain to your PATH:**

       ::
       
           export PATH=/opt/riscv/bin:$PATH

    For more detailed instructions, refer to the official repository: https://github.com/riscv-collab/riscv-gnu-toolchain

Emulator
########

An emulator allows you to run and test your RISC-V applications on your host machine without the actual hardware.
QEMU is a popular emulator that supports RISC-V architecture.

.. admonition:: Todo

    Install QEMU on your host computer.
    Follow the instructions specific to your operating system to install QEMU with RISC-V support.

Debugger
########

For debugging purposes, you can use GDB along with QEMU or directly with the VF2 hardware.

.. admonition:: Todo

    Ensure GDB is installed on your host computer.
    You can use the RISC-V GNU toolchain's GDB or install it separately.

****************
Deploy to VF2
****************

Recover OpenSBI and U-Boot (Optional)
#####################################

In case you need to recover or update the bootloader on your VF2, you can follow these steps.

.. admonition:: Todo

    1. Download the latest recovery binary (``jh7110-recovery-<Version>.bin``) from the official repository: https://github.com/starfive-tech/Tools/tree/master/recovery
    2. Connect the jumper wires between the USB-to-Serial converter and the Debug pins of VF2's 40-pin GPIO header.
    3. Set the boot mode jumpers (Switch_2) on your board to UART mode (RGPIO_1, RGPIO_0: 1,1).
    4. Configure the serial port baud rate to ``115200`` bps.
    5. Power up the board; you should see an output like ``CCCCCCCCCCCCCCCCCCCCCC``.
    6. Transfer the recovery binary using XMODEM.
    7. Follow the on-screen instructions to update the SPL and U-Boot binaries.
    8. Power off and switch the jumpers back to Flash mode (RGPIO_1, RGPIO_0: 0,0).

For detailed instructions, refer to the official guide: https://doc-en.rvspace.org/VisionFive2/Quick_Start_Guide/VisionFive2_SDK_QSG/recovering_bootloader%20-%20vf2.html

Prepare a Bootable Image
########################

To boot your VF2, you need to prepare a bootable SD card with the appropriate images.

.. admonition:: Todo

    1. Create a VFAT partition image:
       ::
       
           dd if=/dev/zero of=starfive-visionfive2-vfat.part bs=512 count=131072
           mkfs.vfat starfive-visionfive2-vfat.part

    2. Prepare the necessary input files:
       - ``u-boot-spl.bin.normal.out``
       - ``visionfive2_fw_payload.img``
       - ``starfive-visionfive2-vfat.part``

    3. Create a ``vf2-genimage.cfg`` configuration file with the following content:
       ::
       
           image sdcard.img {
               hdimage {
                   gpt = true
               }
               partition spl {
                   image = "u-boot-spl.bin.normal.out"
                   partition-type-uuid = 2E54B353-1271-4842-806F-E436D6AF6985
                   offset = 2M
                   size = 2M
               }
               partition uboot {
                   image = "visionfive2_fw_payload.img"
                   partition-type-uuid = 5B193300-FC78-40CD-8002-E86C45580B47
                   offset = 4M
                   size = 4M
               }
               partition image {
                   partition-type-uuid = EBD0A0A2-B9E5-4433-87C0-68B6B72699C7
                   image = "starfive-visionfive2-vfat.part"
                   offset = 8M
                   size = 64M
               }
           }

    4. Generate the SD card image using ``genimage``:
       ::
       
           ./genimage --config vf2-genimage.cfg

    5. Flash the generated ``sdcard.img`` to your SD card:
       ::
       
           sudo dd if=sdcard.img of=/dev/sdX

       Replace ``/dev/sdX`` with the appropriate device identifier for your SD card.

For detailed instructions, refer to: https://hackmd.io/@chiahsuantw/vf2-sdcard

Connect the TTY-to-USB to VF2
#############################

To interact with the VF2 via serial console:

.. admonition:: Todo

   
::contentReference[oaicite:3]{index=3}
 
