# Lab 1: Familiarisation with Assembly Language and HDL/FPGA

## Information

This lab aims to teach you the tools you will need for this module - namely:

* Assembly language programming and simulation for the RISC-V architecture, and

* HDL simulation and FPGA implementation

This lab may seem quite confusing and tedious at first - this is normal, and nothing to be worried about. It's not directly related to what you're learning in the lectures, and some/all of the tools may be new to you. You might not fully understand the connection between all of the things that you do in this lab, and that's fine. It will all likely make sense once you start doing the subsequent labs, and if it doesn't (or if you just want to understand what you're doing), feel free to ask during the lab sessions, or in the Discussions area of this repository :) 

**NOTE**: Lab 1 is an **individual exercise**. While you will be in teams for later lab work, you will work on your own for this first one. 

## Tasks

### Software (Assembly simulation only)

The goal of this section is to get you familiar with the [RISC-V assembler/simulator]() by simulating a sample program. 

Here, we will do a software simulation of an RISC-V based system with memory-mapped input-output. Assume that the system we are simulating has LEDs mapped to the address `0x00002400`, such that the data written (using `sw`) to this address location will appear on the LEDs. Also, assume that the system has DIP switches mapped to the address `0x00002404` such that the data read from this location (using `lw`) will reflect the positions of the switches. The program which does that is provided for you, with the details mentioned below. 

Simulate [sample.asm](). You should understand every line of code, and every directive, in this file - you might be quizzed on these later, *hint hint nudge nudge*. Read [RISC-V Memory Map]() to understand the program better. 

Modify the data in the location pointed to by DIPs and see if the location pointed to by LEDs reflects it. Please see the screenshot below which illustrates how to inspect/modify the memory at location `0x2400` and `0x2404`. The value you enter at the location `0x2404` before executing instruction #4 (line 28 in the file) should go into the register `s4` after instruction #4, and should be reflected at the location `0x2400` after instruction #5 (line 29 in the code). Are they exactly the same? 

![Screenshot of addresses in RARS](rars_address_ss.png)

Change the `DELAY_VAL` and see if the delay introduced by the delay loop changes accordingly. 

Understand the assembly language instructions and the corresponding hexadecimal (binary) representations. *During Lab 1 evaluation, you should be in a position to convert a given 32-bit binary/hex instruction to the corresponding assembly language instruction and vice versa.*

### Hardware (HDL simulation + hardware)

#### Block Diagram for the system

![Block diagram](block_diagram.png)

#### Optional Tasks (for those new to FPGAs)

Go through the [Getting Started]() manual, where the complete FPGA design flow is illustrated - design source creation and editing, simulation, synthesis, implementation, bitstream generation, and FPGA configuration. Note : For Nexys 4 / Nexys 4 DDR, the FPGA part number is XC7A100T-1CSG324C. For Basys 3 board, the FPGA part number is XC7A35T-1CPG236C. Appropriate changes will be required in the project settings.

This manual provides an illustration of the tools you will use in CG3207 through examples of a simple combinational (full adder) and a simple sequential circuit (16-bit shift register). It will familiarize you with industry leader Xilinx's Vivado Design Suite - a comprehensive integrated development environment (IDE) for FPGA design flow, Digilent Inc.'s Nexys 4 Development Board featuring an FPGA from Xilinx's state-of-the-art Artix-7 family, and VHDL/Verilog.

Program the FPGA using the simple_count.bit given in this zip file (VHDL) or this zip file (Verilog). See the counter in action. This can be used to test the board and will help you get started. Note : Works only for Nexys 4 board.
	
1. Download the above file and unzip it.
2. Open the `simple_count.xpr` Xilinx Vivado Project.
3. Have a look a the code files `simple_count.vhd` (VHDL) / `simple_count.v` (Verilog), and `Nexys4_Master.xdc` in \simple_count.srcs folder.
4. Either Generate Programming File, or simply use the existing `simple_count.bit` file in `\simple_count.runs\impl_1\`.
5. Program the Nexys 4 board with the simple_count.bit file.
6. Notice that LED0, LED1, LED2, and LED3 together behave like a 4-bit counter on the Nexys 4 board. The MSBs of a 30-bit string `000000000000000000000000000000` are shown on the LEDs. The rest of the string change values too fast for the human eye. Also, note that if the BTNC button on the Nexys 4 board is pressed, the counter is paused - refer to the VHDL/Verilog code (.vhd/.v) and constraints file (.xdc).
7. Augment the sample counter (in step 1) as follows :
	When a button DIR is pressed, the counter should toggle the count direction. Upon pressing the RESET button, the counter value should be reset (synchronously or asynchronously) to `0000` when counting up, and to `1111` when counting down. 
	The counter has 4 different speeds - 1x (the default speed), 2x, 3x, and 4x. A button SPEED is used to change the speed of the counter -  each press of SPEED increases the speed, and if the current speed is 4x, it wraps around to 1x.
	Note : You will need to debounce DIR and SPEED buttons to observe the desired results (why?). However, debouncing is left as an optional task. The priority of buttons (i.e., what should happen when two or more buttons are pressed together) is left to your discretion.

## Submission Info

## Tips

* Read Chapter 2B thoroughly and make sure your code is synthesizable.

* If you want the clock to be effective only under certain situations, use a clock enable.

* Synchronize all inputs at the earliest possible opportunity.

* Synthesize each entity / module by setting it as the top-level module and check for errors / warnings.

* See the elaborated design to see if the schematic matches your intended design.

* Inspect the synthesis report to see if the primitives (basic digital building blocks) inferred make sense.

* Xilinx Vivado's text editor is not very good. You might want to use a better editor like Notepad++ to keep the code properly intended.

* It might take a bit of time, effort, and frustration before you are able to write good synthesizable code. Sometimes, you can’t get a better teacher than experience, especially so when it comes to writing good Verilog code. Just hang in there and you will be ok soon. The best bet is to go through the notes and have the hardware in mind while writing the code.