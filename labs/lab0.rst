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

    1. **Install the necessary dependencies**

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

Prepare a Bootable Image
########################

To boot your VF2, you need to prepare a bootable SD card with the appropriate images as provided.


Connect the TTY-to-USB to VF2
#############################

To interact with the VF2 via a serial console, follow these steps:

1. **Connect the USB-to-Serial Converter:**

   - Identify the UART pins on the VF2's 40-pin GPIO header.
   - Connect the USB-to-Serial converter to your host computer and to the VF2's UART pins. Ensure the connections are as follows:

     - **GND** on the converter to **GND** on the VF2.
     - **TXD** on the converter to **RXD** on the VF2.
     - **RXD** on the converter to **TXD** on the VF2.

   Refer to the VF2's hardware documentation for the exact pin locations.

2. **Install Serial Communication Software:**

   On your host computer, install a terminal emulator such as `minicom` or `screen`. For example, to install `minicom` on Ubuntu:

   ::
   
       sudo apt-get install minicom

3. **Configure the Serial Connection:**

   - Determine the device name assigned to the USB-to-Serial converter. Typically, it appears as `/dev/ttyUSB0` or similar. You can identify it by running:

     ::
     
         dmesg | grep tty

   - Launch `screen` or `minicom` with the appropriate device and baud rate settings. For example: 

     ::
     
         sudo screen /dev/ttyUSB0 115200

   Adjust the device name (`/dev/ttyUSB0`) as necessary based on your system.

4. **Power On the VF2:**

   With the serial connection established, power on the VF2 board. You should observe boot messages in the terminal emulator, indicating successful communication.

5. **Log In to the VF2:**

   Once the boot process completes, you will be prompted to log in. 

   After logging in, you can interact with the VF2's operating system via the serial console.

Debugging
#########

Effective debugging is crucial for development. Here's how to set up debugging for the VF2:

1. **Install GDB:**

   Ensure that the GNU Debugger (GDB) is installed on your host computer. If you've built the RISC-V GNU toolchain as previously instructed, GDB should be included. Otherwise, install it separately:

   ::
   
       sudo apt-get install gdb-multiarch

2. **Debugging with QEMU:**

   To debug your applications using QEMU:

   - Start QEMU with the `-s` and `-S` options to enable debugging:

     ::
     
         qemu-system-riscv64 -M virt -kernel path/to/your/kernel.elf -nographic -s -S

     This command starts QEMU and waits for a debugger to connect.

   - In another terminal, launch GDB and connect to QEMU:

     ::
     
         riscv64-unknown-elf-gdb
         (gdb) target remote localhost:1234

   You can now set breakpoints, step through code, and inspect variables within GDB.

3. **Debugging on the VF2 Hardware:**

   For on-target debugging:

   - Ensure that GDB and the GDB server are installed on the VF2.

   - On the VF2, start the GDB server, specifying the target application and port:

     ::
     
         gdbserver :1234 /path/to/your/application

   - On your host computer, connect GDB to the VF2:

     ::
     
         riscv64-unknown-elf-gdb
         (gdb) target remote vf2_ip_address:1234

   Replace `vf2_ip_address` with the actual IP address of your VF2 board. You can now perform remote debugging from your host machine.

**Note:** Ensure that your firewall settings allow for the necessary network connections between your host computer and the VF2.

By completing these steps, you have set up the development environment, prepared the VF2 for deployment, and configured debugging tools to aid in your development process.
 
