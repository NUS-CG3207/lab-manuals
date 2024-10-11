# Lab 4: (Near) Complete Processor + Bells + Whistles

Lab 4 involves 1 **compulsory task** and the rest is **open-ended.**

## Compulsory Task \[15 marks\]

Implement pipelining without hazard hardware. The pipelining should be done such that the processor supports all the requirements for Lab 2 and Lab 3 \[Post-synthesis simulation and hardware\] 

Note: When we say "without hazard hardware", we mean that hazard hardware is not a basic requirement. You can score the full 15 marks for the basic component of this assessment without implementing hazard hardware - just be careful to include the appropriate number of `NOP` instructions so your code still works. This is not to say, though, that hazard hardware cannot or should not be implemented - but if you do include it, it will be counted as an enhancement (see the next section). 

For some tips on implementing pipelining, [see this page](pipeline.md).

## Open-ended Enhancement \[10 marks\]

This is the fun part - you get 10 marks for Lab 4 for implementing performance enhancements of your choice. There is no fixed requirement to get these marks, and while we suggest some enhancements below, it doesn't mean you need to implement all (or any!) of these. Just one significant performance enhancement will suffice, and this need not be limited to the ones we have listed. 

Some potential improvements you can think about implementing are:

* [Implement hazard detection and resolution](hazard.md)
* [Implement additional instructions](additional_instructions.md)
* [Implement exception handling and interrupt support](interrupts.md)


## Design Instructions

*   You are encouraged to have your own, comprehensive programs to have a convincing demo.
*   You will have to tweak the templates given in Lab 2 for use in Lab 4. Specifically, you will have to change the ALUControl to 4 bits.
*   All 32-bit combinational arithmetic and logical operations have to be performed inside the ALU. For Lab 4, you can build on the basic ALU provided as a part of Lab 2 templates.   
    *Exceptions*: `+` for calculating `PC+4`, `PC+8`, multiplication, division. 
*   In the ALU, DO NOT use additional `+` signs -> this could infer additional adders. The existing addition framework should be good enough.
*   All operators are permitted on 32-bit values outside the ARM/RISC-V module. For example, you will have to do 32-bit comparisons in the wrapper for address decoding.
*   Use of arithmetic operators  
    1.  Do not use `*` operator. It is synthesizable, but in this lab, we are implementing multi-cycle multiplication. `/` is not synthesizable, except on constants.
    2.  All operators (including `**`, `*`, `/`, `sll`, `rem`, `mod` etc.) are allowed on constants (operations on constants are done at synthesis time, and will not infer any hardware).

## Submission Info

*   Lab 4 will be evaluated in **Week 12**. The presentation schedule can be found on Canvas. 
*   Include
    
    *   **.v/vhd** files you have created/modified \[ RTL Sources, Testbench(es) \] 
    *   **.bit** files 
    *   **.s** files (assembly programs)
    *   **.ppt** file - 2 to 6 slides showing performance enhancement techniques you have implemented.
    
    in an archive with the filename **GroupXX****\_Monday/Friday\_Lab4.zip** (replace XX with your group number) and upload it to Canvas. One submission per group is sufficient – if there are multiple submissions, the file with the latest timestamp will be taken as the final submission. **_Do not_** zip and upload the complete project folder – only those files mentioned above should be included. **The files should be the exact same files that you had used for the demo.**
