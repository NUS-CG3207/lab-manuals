Lab 2: Implementation of a RISC-V 32-bit (RV32I) Processor
==========================================================

## Objective

In this lab, you will be implementing the basic RISC-V processor supporting only limited 32-bit integer instructions.

Essentially, it should support the following instructions \[HDL simulation as well as hardware\](20 marks)

*   **add**, **addi**, **sub**, **and**, **andi**, **or**, **ori**
*   **lw**, **sw**
*   **beq**, **bne**, **jal** (without linking, that is, without saving the return address). 

Further, improve the processor by adding the following features \[HDL simulation as well as hardware\]

*   **lui**, **auipc** (5 marks)
*   **sll**, **srl**, **sra** (5 marks)

No extra marks will be awarded for performance enhancements / adding support for more instructions (that's for Labs 3 and 4). However, a lack of convincing demos (with carefully crafted assembly language programs) can result in the deduction of marks.

## Design Files

The design files can be found here - [Lab\_2\_Template\_files](https://github.com/NUS-CG3207/lab-skeletons/tree/main/lab2) (Only Verilog version provided. ChatGPT can help you convert this to VHDL pretty well if you are a fan of VHDL). Import all the relevant files into your project - all the .v files, as well as TOP\_<Nexys/Basys depending on your board>.vhd and uart.vhd - irrespective of whether you use UART. Choose the appropriate constraint file for your board$. The files are pretty self-explanatory. It is possible to mix VHDL and Verilog files in the same project. Note that TOP/uart.vhd is the same as that for the ARM version. All other files have differences, though in many cases, the differences are minor.

The file hierarchy is as follows

*   TOP\_<board>.vhd (top-level module for hardware implementation) → Wrapper.v (unit under test for simulation) → RV.v → Components of RV (PC_Logic.v, Decoder.v, ALU.v etc).
*   TOP\_<board>.vhd → uart.vhd
*   test\_Wrapper.v (top-level module for simulation) → Wrapper.v (unit under test for simulation) → RV.v → Components of RV (PC_Logic.v, Decoder.v, ALU.v etc).
*   ALU.v → Shifter.v

Ensure that the top-level module is set correctly in your project. It should be TOP (TOP\_<board>.vhd) for implementation, and test\_Wrapper for simulation. It can be set by right-clicking the appropriate file under Design sources (for implementation) and Simulation sources (for simulation) and choosing Set as Top.

There are basically 4 files you need to populate / modify  - **PC_Logic.v** , **Decoder.v** , **RV.v** ; **y**ou will also need to paste your code / constant memories into **Wrapper.v** (see the section on RISCV Programming Instructions below).  A 5th file, **ALU.v** should also be modified to incorporate shifts.

Some other important considerations :

*   Ensure the top-level module for synthesis is TOP (TOP.vhd) for synthesis (by right-clicking the file under Design Sources).
*   Ensure that the top-level module is test\_Wrapper (test\_Wrapper.v) for simulation (by right-clicking the file under Simulation Sources) - this is especially important as Vivado might pick up TOP as top-level module for simulation, which is wrong.
*   You might also need to modify CLK\_DIV\_BITS in TOP.vhd depending on the processor clock speed you want to achieve (keep it to a low number like 5 if you are using UART). This need not be changed for simulation as TOP.vhd is not simulated.  
    
*   Read the comments (especially about the input and output ports / interfaces) in the Wrapper.v carefully.

$It is a good idea to delay importing the constraints file and the TOP\_<board>.vhd file until you are ready to test on hardware. Not having the constraints file during the design / simulation phase can help avoid some warnings related to synthesis when you try synthesizing a module that does not have the interfaces specified in the constraints file.

Wrapper.v is almost identical to that of the ARM version. The only real difference is in the memory map. A signal MemRead has also been added for better functionality of UART. The TOP\_<board>.vhd is identical to that of the ARM version.

## Design Requirements

*   **You are required to simulate your design and verify its functionality.** All debugging should be done in simulation, not in hardware. Furthermore, while developing and testing your design, _you should also try synthesizing to ensure that your design is synthesizable without avoidable warnings and errors._
*   Learn how to use debugging options such as single stepping, breakpoints, running for a specified time, etc which can help tremendously. However, note that some options such as single stepping work a bit differently from conventional software debugging, due to the inherent parallel nature of HDLs, as well as the fact that non-blocking assignments do not have an instantaneous effect on the LHS.
*   All arithmetic, logical, and shift operations (except PC logic) on 32-bit numbers should be done in the ALU.
*   **DO NOT create additional entities/modules**. Components such as multiplexers are easily implemented using when-else / with-select / if / case statements. Leave PC_Logic and Decoder separate (do not combine them into one ControlUnit entity). However, the interfaces for entities could be modified slightly to meet the design requirements.
    *   DO NOT modify the ports of the entity RISC-V, unless you want to take responsibility for the top-level wrapper module.
*   It is a good idea to use '-' (VHDL) or 'X' (Verilog) for don't cares, as it could simplify the combinational logic. However, there are 2 issues
    *   Using don't cares with signals which change the processor state (RegWrite, PCSrc, MemWrite) would make the system vulnerable to illegal instructions.
    *   Don't cares can cause different behavior in simulation and synthesis. Don't cares are treated as a don't cares in simulation, whereas in synthesis, it could be a random 0 or 1 (whichever simplifies logic better).
*   Reset resets only the program counter. The register initial values are not guaranteed to be zero. This requires you to write to a register before using/reading it.
*   you might need to **modify your Lab 1 program to use only those instructions you have implemented in YOUR processor** (**and simulate it in RARS** to ensure the functionality) before you get started
*   Use your own, well-crafted assembly language programs for a convincing demo (to demonstrate that all the required instructions and variants work). **One single program demonstrating all the features would be desirable**. By 'convincing demo', what we mean is having an assembly language program that tests all the features of all instructions of a particular type. For example, if you demonstrate **addi**, you don't really have to show **andi**, **ori** as it can be expected to work, as the datapath activated is the same. Instructions such as conditional branches should be used such as both possibilities - i.e., branch taken and branch not taken should be demonstrated. In other words, it should provide an exhaustive 'coverage' of your HDL code. Your program should be crafted such that if one instruction misbehaves, the overall behavior of the program should be different (this is the case for most programs, as long as you use the result from every instruction in a subsequent instruction).
*   The assembly language program you use, should be (obviously) such that only those instructions that **you** have implemented in your HDL are used.
*   The provided HelloWorld program is perhaps **NOT** the first assembly language program you should attempt on _your_ processor. Use the **Lab 1 assembly language program** (with appropriate modifications) instead. HelloWorld is neither meant to be a program for you to get started nor meant to be a comprehensive program that tests everything. It is just a fancy example program. Make sure that all the instructions used by the assembly programme are implemented in your design (HDL) before you can expect the functionality!
*   The provided test\_Wrapper is meant to test the HelloWorld assembly language program and provides stimuli simulating a UART Console. Slight **modifications will be needed** to test other assembly language programs you write. For example, if you are giving inputs using DIP switches, you will need to provide appropriate stimuli, i.e., DIP = something at an appropriate point in time in your testbench.
*   You could use the program that you simulated in RARS in Lab 1 as a starting point if you have implemented **lui** and **auipc***. However, you will need to make appropriate modifications to include instructions such as **DP reg type**, **bne**, and shifts in a meaningful manner. The test\_Wrapper should be modified to give appropriate stimuli, as mentioned in the previous point. *You can use it even before incorporating lui and auipc, but you will need to change it such that s1 and s2 are loaded from memory.
*   There is no requirement that you should use all the peripherals supported by the Wrapper. As long as your demo is convincing, it is fine to use only a limited set of peripherals (say, LEDs and DIP switches \- at least one input and one output). [RISC-V Memory Map](../rv_memmap.md) page has more details about the address and usage of the supported peripherals.
*   You can add more peripherals (RGB LED, accelerometer, VGA display, etc.) to the Wrapper if you wish. The corresponding changes will also need to be done in the top-level .vhd and .xdc files.

### RISC Programming Instructions

Please follow the instructions in the [RISC-V Programming](../rv_programming.md) page to configure RARS and to write programs. The .hex file generated by the program is inserted into the ROMs within Wrapper.v to "program" the RISC-V processor using the procedure mentioned on that page.

Simulate your assembly language program thoroughly - else when something goes wrong, you won't know if the problem is with your HDL code (hardware) or the assembly language program (software).

## Tips

*   Please **SIMULATE** your design before spending your time on bitstream generation. Make sure your design synthesizes without warnings (if at all there are warnings, you should know the reasons and you should ensure that the warnings do not affect the functionality). If you don't simulate and click 'generate bitstream' hoping it would work on the board, you are probably wasting your time. This can't be emphasized enough.
*   Synthesize modules that you edit, such as decoder and conditional logic by setting them as top-level modules even before simulation. The synthesis tool is much smarter than the simulation tool - **synthesis reports and warnings** can give you a wealth of information.
*   Looking at RTS Analysis > Open Elaborated design gives you insights into the schematic (block design) inferred from your code. This can be very useful in debugging. Pay particular attention to the bit widths for each connection etc.
    *   You can get even more information from the synthesis report.
*   You will get a warning about indices\_reg. This is related to the seven-segment display and can also be ignored. You will also get warnings about Funct7 bits other than Funct7\[5\] being unused. This is also expected.
*   You may also get warnings about Shifter connections and ALUFlags until you connect them.
*   If you are synthesizing after setting a module other than TOP as the top-level module, you will get warnings such as those below. If your intention is to check the synthesizability of modules one by one, these warnings can be safely ignored (Why?).
    *   'set\_property' expects at least one object.  
        
    *   create\_clock:No valid object(s) found for '-objects \[get\_ports CLK\_undiv\]'.  
        
*   You can get a very very good sense of whether it will work on hardware by doing a **post-synthesis functional simulation** by Simulate > Post-synthesis functional simulation. The same testbench can be used, so it requires zero extra effort. However, debugging is much harder than it is with behavioral simulation as some of the internal signals are optimized away and/or renamed (still easier than it is with hardware). For post-synthesis functional simulation, either the Wrapper or the TOP should be set as the top-level module for synthesis, and then the module should be synthesized before it can be (Post-synthesis) simulated.
*   By default, Vivado will run significantly slower in Windows than in Linux, due to the differences in the number of threads used. A workaround is mentioned here - https://docs.amd.com/r/2021.2-English/ug904-vivado-implementation/Multithreading-with-the-Vivado-Tools
*   If you try to run the design from a 100 MHz clock directly (CLK\_DIV\_BITS = 0, i.e., without dividing the clock), you will most certainly get a critical warning that the timing constraints are not met (Why?).  Your design may or may not work on hardware, and it is unreliable even if it works. A pipelined design (Lab 4) should work directly from 100 MHz. 
*   You can go into the subunits and see their value for each instruction (Scope-Objects) - this is much easier than what most of you think. This is more powerful than dragging the various signals into the waveform. Note that the values you see are those at the time the simulation has stopped/paused, not the time corresponding to the yellow vertical bar in the waveforms window. Double-clicking the Scope-Objects will lead you to the source code - you can then hover the mouse pointer above various objects to see their values.
*   Make sure your radix in the waveform window / Scope-Objects is set correctly. Looking at hexadecimal and assuming them to be decimal or vice versa is a common mistake. Saving the waveform window (.wcfg) and adding it to the project will ensure that such settings get saved. For Console input/output, setting the radix to ASCII can be useful.
*   Have the RARS simulator side by side so that you can compare the register/memory values between that in RARS and HDL register/memory objects. While you single step in RARS, you can also run by 10 more ns to have the same effect in HDL simulation. It helps to have the PC and Instr values in the waveform window to see the correspondence between RARS and HDL simulations, i.e., to ensure that you are looking at the same instruction on the two tools.
*   If you get a number of warnings (~100) about unconnected stuff being removed, chances are that you haven't initialized the ROMs.
*   The default processor clock frequency is 5 MHz which is ideal for UART, but too fast for LEDs - you will see the LEDs constantly lit, but different LEDs may have different brightness (why?). To see the output on LEDs, you need to have the LEDs changing state slow enough. There are two ways to do it - 1) by using a slow clock for the processor. This is done by setting the CLK\_DIV\_BITS to a value of around 26. 2) by having a fast clock CLK\_DIV\_BITS = 1 for 50MHz), but using software delays (using a high value for DELAY\_VAL between LED writes).
    
    However, note that you should use a very low DELAY\_VAL during simulation. Else, you might have to run simulation for a long time to get past the delay. Make sure you change either of them to a high value before implementation / bitstream generation  
    Similar considerations apply while sending data via UART.
    
*   To implement **lui** and **auipc**, you will need to have a multiplexer at SrcA. The ALUSrcA control signal needs to be implemented properly too.
*   The signals connected to the ports are given the same name as the ports themselves. So making the datapath connection is as easy as having a concurrent statement (VHDL) such as Opcode  <= Instr(6 downto 0) / continuous assignment (Verilog) such as assign Opcode  \= Instr\[6: 0\].
*   Don't forget to **Relaunch** simulation (not just Restart) once you have made any changes to your HDL.

## Submission Info

*   You will have to demonstrate your design during your designated lab session in **Week 7**. The presentation schedule can be found on Canvas. 
*   One single program demonstrating all the features would be desirable.
    
*   Please upload a _single archive_ containing all the relevant files to Canvas within 1 hour of your demo.
*   Include
    
    *   **.vhd/.v** files you have created/modified \[ RTL Sources, Testbench(es) \]
    *   **.bit** files 
    *   **.asm** files
    *   a **readme.txt**, mentioning the purpose of each file (only those you have created / modified) briefly
    
    in an archive with the filename **Lab2\_<lab day><group number>.zip**, e.g. Lab2\_Monday01.zip. One submission per group is sufficient – if there are multiple submissions, the file with the latest timestamp will be taken as the final submission. **_Do not_** zip and upload the complete project folder – only those files mentioned above should be included. **The files should be the exact same files that you used for the demo**.
