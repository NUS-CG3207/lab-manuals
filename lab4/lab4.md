# Lab 4: (Near) Complete Processor + Bells + Whistles

## Tasks
Lab 4 involves 1 **compulsory task** and the rest is **open-ended.**

### Compulsory Task

Implement pipelining without$ hazard hardware. The pipelining should be done such that the processor supports all the requirements for Lab 2 and Lab 3 \[Post-synthesis simulation and hardware\] **15 marks**.
$ We do not mean that you should demonstrate without hazard hardware. Hazard hardware is not a basic requirement and you can get up to 15 marks even without it. If you have implemented hazard hardware, it is an enhancement, and you should demonstrate the enhanced version.

*   The WE of the PC given in the templates is active high, while the one given in the pipelined design (Chapter 6) is active low. You might want to change the PC WE in the templates to active low to be consistent.
*   Even though the pipeline registers are shown as big registers storing a lot of stuff, each data stored can be thought of as being in a separate register. In other words, in your VHDL code, you don't have to try and 'collect' all the bits to form a single register entity. 
*   Systematic procedure for Pipelining (applies to ARM as well as RISC-V, the latter will have different instruction mnemonics, but the concepts are the same).
    *   Start with inserting the appropriate suffixes (F/D/E/M/W) to the port/signal/reg/wire names. **Refer to** **slide 11 of Chapter 6.**  
        *   Generally, for each component, all its port suffixes will be the same. Exceptions: For RegFile, you use the suffix W for A3 and WD3. 
        *   For every signal that is supposed to go through a pipeline register, make an appropriate datapath connection as a 
            *   \[VHDL\] concurrent statement (NOT inside a clocked process, for now). For example, ExtImmE <= ExtImmD; in the architecture part of ARM.vhd.
            *   \[Verilog\] combinational always block using non-blocking (<=) assignments. For example, ExtImmE <= ExtImmD; inside an always @(\*) in ARM.v. You will need to make the reg / wire type adjustments etc as appropriate. It would be a good idea to initialize the regs corresponding to control signals to 0.
        *   You will need to make explicit connections for the signals/ports/inputs which were implicitly connected in the template code - for example, ALUControl wasn't explicitly connected in your Lab 2, as the connection was implicitly made. So have a 
            *   \[VHDL\] concurrent statement like ALUControlE <= ALUControlD; in ARM architecture.
            *   \[Verilog\] combinational always block using non-blocking (<=) assignments, such as  ALUControlE <= ALUControlD; inside an always @ (\*).
        *    WA3/rdW will need to be connected as RdD/rdD->RdE/rdE->RdM/rdM->WA3/rdW. Similarly other signals such as RegWrite, PCE, etc. that need to be delayed in the pipelined version.
        *    For RISC-V, a multiplexer needs to be inserted to select between PCF and PCE, controlled by PCSrcE (for now, i.e., in the single-cycle processor, PCF and PCE will be the same).
        *    Verify that your design works (**exactly as it used to previously**, i.e., without any change to the assembly language program - no NOPs needed at this stage, as we haven't inserted pipeline registers yet) after inserting the suffixes and making the connections as described above.
    *   Now,  
        *   \[VHDL\] move all the signals which are supposed to go through a particular pipeline register into one clocked process in ARM architecture (everything in one big clocked process is fine too, but it is better to keep it in separate process for better organization).
        *   \[Verilog\] change combinational always blocked to clocked ones : always@(\*) → always@(posedge CLK).
        *   For both, initialize all signals / regs to zeros. Add a condition where if RESET is 1, everything is made zeros, else, LHS is assigned RHS at clock edge.
    *   Modify the RegFile slightly - 
        *   \[VHDL\] CLK'event and CLK='1' → CLK'event and CLK='0'.
        *   \[Verilog\] always@(posedge CLK) → always@(negedge CLK)
*   Change your assembly language code by inserting an appropriate number of NOPs wherever there is a data / control hazard. 
    *   For a start, just insert NOPs such that each pair of instructions having a data hazard are spaced by at least 2 instructions (for example, insert 2 NOPs if the two instructions are consecutive).
    *   After each branch, insert 4 NOPs.
*   Verify that your design works as it used to (it will just be slower because of all the NOPs).
*   Resolve data hazards by adding the hazard hardware as shown in the lecture. Note that this will require creating additional pipeline entries such as RA1E, RA2E, RA2M.
*   Verify that your design works still works.
*   Change the design as given in slide 31 of Chapter 6 (assuming your program does not use LDR PC, ...).
*   Verify that your design works still works.
*   Insert the control hazard hardware as shown in slide 33.
*   Verify that your design works still works. If it does, BINGO - you are on track to get a pretty good mark for the performance enhancement part !!! You may need to clear (parts of) the pipeline registers to have proper initial values for the various outputs of the hazard unit.
*   While flushing a stage, the reset/CLR for the pipeline register feeding that stage is asserted, causing an NOP to go into that stage. An easier solution (for E and later stages) is to just reset only those bits which affects the machine state (such as FlagWE, MemWE etc).

### Enhancements (Open-ended)

 Less than half of the weight of Lab 4 is for **performance enhancements** (**10 marks in total**). It is open-ended without fixed requirements. You don't have to do everything; **one significant improvement which you think is worth 10 marks is good enough**. You could try out things such as

1) Implement additional instructions (the marks you get depends on how much additional logic you need to incorporate for the additional instructions) or features of instructions that are not completely implemented.

*   *   Expand the processor functionality such that it supports DP instructions that were not supported earlier (except multiply type), i.e., **xor**, **xori**, **slt**, **sltu**, **slti**, **sltiu**, **slli**, **srli**, **srai**. Support **all the 16 Data Processing instructions** a.k.a ALU functions.
*   The branch instructions that were not supported earlier, i.e., **blt**, **bge**, **bltu**, **bgeu**, **jal** (add support for link, i.e., rd = PC+4).
*   *   See Section "A3.4 Data processing instructions" in page A3-9 to A3-11 (page 75) of ARM [Architecture Reference Manual](https://canvas.nus.edu.sg/courses/62251/files/folder/Lab%20Resources?preview=4733362) for the details of the instructions. Page A3-11 has links to Sections 4.xx where the instruction behavior is explained in more detail. Make sure you look at the ARM (32-bit) instructions, not Thumb (16-bit) instructions.
*   It mainly involves modifying the ALU and the ALU Decoder.
*   The C flag has to be an output from the CondLogic component/module, to act as an input for the ALU component/module (to support ADC instruction).
*   Implement it efficiently, hopefully without additional adders.
*   Note the following  
    *   You don't have to worry about how xPSR is dealt with in the special case of Rd=R15 as mentioned in the manual.
    *   The carry flag doesn't need to be affected by shifter\_carry\_out (it is a possible enhancement, see 3rd paragraph of subsection 1 in the enhancements section below).
    *   Note that when I bit of the instruction is 0, and bit\[7\] and bit\[4\] of the instruction are both 1's, the instruction is not a usual DP instruction (could be MUL etc.).
*   Examples of instructions / additional instruction features you can implement include SMULL, UMULL, SWP, BL, pre-indexed and post-indexed LDR/STR, Register Shifted Register Src2, etc. Note that BX is not supported in ARMv3 - you can use  MOV PC, LR if you need similar functionality. 
    *   This will require additional read/write ports for the register file _or_
    *   You can use micro-operations to avoid the need for more than 2 read ports / more than two write ports for some of the instructions - you will need additional logic to write results one by one over two cycles while PC is stalled. For example, for SMULL, UMULL etc, you can have an internal register (which is not in the visible register set) for storing the second word, to be written in the second cycle. You will also need to generate two sets of control signals - one for the first cycle and one for the second cycle. A multiplexer is required to select one of the two sets of control signals which are passed to the E pipeline register over successive clock cycles.
*   Your could support Src2 (for DP instructions) with rotated immediate.
    
    *   You can use the same shifter unit that we have used so far, with appropriate connections (input multiplexers and control signals). 
    *   This will need a 32-bit mux at the shifter input, a 1-bit mux for Shamt5(0) \[which is '0' when _rot_ is used\], as well as a 2-bit mux for sh \[which is "11" for immediate Src2\] - all the muxes can be controlled using the same control signal. 
    *   An alternative (more hardware efficient) is to move the shifter after the multiplexer controlled by ALUSrc (i.e., to just before SrcB of the ALU), and you can continue to use the same ALUSrc control signal. This will eliminate the need for a 32-bit multiplexer. However, you will need a 5-bit multiplexer for Shamt5, with a 2-bit control signal - select Shamt5 for register Src2, (Shamt5\[4:1\], 0) for immediate Src2 and "00000" for all others. A 2-bit mux for sh will be required too, which selects "11" for immediate Src2, sh for register Src2, and either for all others (as the shift amount is zero for others anyway).
*   Your shifter could be modified to generate a carry ('C') based on the last bit that is shifted out (shifter\_carry\_out). This can be used to set the carry flag for instructions such as MOVS, ANDS, ORRS, etc. (see ARM reference manual for the explanation of MOVS etc.).
    
    *   This will require FlagW to be used differently (now that 'C' and 'V' are not always written together).
    *   Effective 'C' will be instruction-dependent - need more logic to channel the appropriate carry into the input of 'C' flipflop/register. This logic can be inside the ALU (in which case the carry from the shifter will have to be an input to the ALU) or in the main ARM module.
*   You could also implement instruction options which are not fully implemented previously, such as implementing register shift register as Src2 for DP instructions, MUL could set 'Z' and 'N' flags, etc.
*   For RISC-V, you could implement **jalr**, multiply type instructions that were not implemented in lab 3, etc.
*   *   You can modify the ARM processor to work even for LDR PC,.. without that much of an effort. If LDR with PC as destination needs to be supported, we need to create a separate signal PCWriteLDR specifically for this case. PCWriteLDRM + PCWriteLDRW should stall D and Flush E., in addition to PCSrcE flushing D and E (effectively, we will be flushing 4 instructions). You will need to retain the multiplexer as in the original circuit to enable the result read from the memory to go into PC. So essentially, you will have 2 multiplexers for PC input - one as in the original branch, and one for early BTA.
*   Forwarding data from W to D will allow you to write register file at the positive edge, which will most likely reduce your critical path and improve the frequency at which your processor can operate.
*   If you really want to push your limits, you can implement stuff like dynamic branch prediction.

2) Implement some kind of exception handling / support for interrupts

*   External interrupt generation logic can be done inside the wrapper fairly easily. For example, if you want an interrupt to be raised when any of the pushbuttons are pressed, you can do interrupt = PB\[3\] | PB\[2\] | PB\[1\] | PB\[0\]. This interrupt can be then be fed into ARM/RV module from the wrapper.
*   You can have a hardcoded exception handler address input to the multiplexer controlling the PC input. The interrupt input itself can be used as (part of the) multiplexer select input.
*   You can write your interrupt service routine in your assembly code, figure out the starting address, and use this value as hardcoded input to the mux. An alternative is to decide on a fixed handler address, and fill up spaces/NOPs in your code until the handler starts address. For example, if you fix the handler starting address to be 0x100 and your 'main' program contains 30 instructions, you will need to add 34 NOPs before the first instruction in the handler code so that handler code will indeed be at 0x100.
*   You should also have some mechanism to save PC+4 into a register (say, LR) and to restore it when the handler has finished (use MOV PC, LR to return from the handler).
*   Other possible exception/interrupt sources are other peripherals, illegal instruction, division by zero, etc.
*   If you wish to have vectored interrupts, you need to associate a number with each interrupt. You need a table/ROM with the starting addresses, which will be indexed by the interrupt number. The output of this ROM is to be fed into the PC multiplexer. The PC multiplexer control signal can be the logical OR of all the interrupt lines.

### Design Instructions

*   You are encouraged to have your own, comprehensive programs to have a convincing demo.
*   You will have to tweak the templates given in Lab 2 for use in Lab 4. Specifically, you will have to change the ALUControl to 4 bits.
*   All 32-bit combinational arithmetic and logical operations have to be performed inside the ALU. For Lab 4, you can build on the basic ALU provided as a part of Lab 2 templates.   
    _Exceptions_: '+' for calculating PC+4, PC+8, multiplication, division. 
*   In the ALU, DO NOT use additional '+' signs -> this could infer additional adders. The existing addition framework should be good enough.
*   All operators are permitted on 32-bit values outside the ARM. For example, you will have to do 32-bit comparisons in the wrapper for address decoding.
*   Use of arithmetic operators  
    1.  Do not use '\*' operator. It is synthesizable, but in this lab, we are implementing multi-cycle multiplication. '/' is not synthesizable, except on constants.
    2.  All operators (including '\*\*', '\*', '/', sll, rem, mod etc.) are allowed on constants (operations on constants are done at synthesis time, and will not infer any hardware).

## Submission Info

*   Lab 4 will be evaluated in **Week 12**. The presentation schedule can be found on Canvas. 
*   Include
    
    *   **.v/vhd** files you have created/modified \[ RTL Sources, Testbench(es) \] 
    *   **.bit** files 
    *   **.s** files (assembly programs)
    *   **.ppt** file - 2 to 6 slides showing performance enhancement techniques you have implemented.
    
    in an archive with the filename **GroupXX****\_Monday/Friday\_Lab4.zip** (replace XX with your group number) and upload it to Canvas. One submission per group is sufficient – if there are multiple submissions, the file with the latest timestamp will be taken as the final submission. **_Do not_** zip and upload the complete project folder – only those files mentioned above should be included. **The files should be the exact same files that you had used for the demo.**
