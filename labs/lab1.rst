========================
Lab 1: Hello World
========================

*************
Introduction
*************

In Lab 1, you will practice bare-metal programming by implementing a simple shell on the VF2 board. This involves setting up the Universal Asynchronous Receiver-Transmitter (UART) for serial communication between your host computer and the VF2.

*****************
Goals of this lab
*****************

- Practice bare-metal programming.
- Understand how to access VF2's peripherals.
- Set up UART for serial communication.

*****************
Basic Exercises
*****************

Basic Exercise 1 - Basic Initialization - 25%
#############################################

When a program is loaded onto the VF2, it requires:

- All its data to be present at the correct memory addresses.
- The program counter to be set to the correct memory address.
- The `.bss` segment to be initialized to zero.
- The stack pointer to be set to a proper address.

Upon booting, the VF2's bootloader loads the kernel image to a specific physical address and starts executing the loaded program. If the linker script is correct, the first two requirements are met. However, both the `.bss` segment and the stack pointer are not properly initialized. Therefore, you need to initialize them at the very beginning to avoid undefined behaviors.

.. admonition:: Todo

    Initialize the VF2 after it is booted by the bootloader. This includes setting up the stack pointer and zeroing out the `.bss` segment.

Basic Exercise 2 - UART Setup - 25%
####################################

For all the labs, you'll use UART as a bridge between the VF2 and your host computer. The VF2 provides UART interfaces that can be used for serial communication. In this exercise, you need to set up the UART for communication.

.. admonition:: Todo

    Follow the VF2's hardware documentation to set up the UART interface. This involves configuring the UART registers to initialize the baud rate, data bits, stop bits, and enabling the transmitter and receiver.

Basic Exercise 3 - Simple Shell - 25%
######################################

After setting up UART, you should implement a simple shell to allow the VF2 to interact with the host computer. The shell should be able to execute the following commands:

- `help`: Print all available commands.
- `hello`: Print "Hello World!"

.. important::

    Be mindful of text alignment issues on screen I/O. Consider handling carriage return (`\r`) and newline (`\n`) characters appropriately on both input and output.

.. admonition:: Todo

    Implement a simple shell supporting the listed commands. Ensure that the shell can read input from the UART and display output back to the host computer.

Basic Exercise 4 - System Information - 25%
############################################

Retrieve and display the VF2's system information, such as board revision and memory size. This exercise will help you understand how to interact with the VF2's hardware to obtain system details.

.. admonition:: Todo

    Implement functionality to retrieve the VF2's hardware information and print it. You should at least display the board revision and the base address and size of the system memory.
