---
nav_order: 11
---
# Lab 4 Enhancement: Interrupt generation and exception handling

External interrupt generation logic can be done inside the wrapper fairly easily. For example, if you want an interrupt to be raised when any of the pushbuttons are pressed, you can do interrupt = `PB[3] | PB[2] | PB[1] | PB[0]`. This interrupt can be then be fed into ARM/RV module from the wrapper.

Other possible exception/interrupt sources are 
* other peripherals - e.g., counter from Wrapper
* invalid memory address - `bad_MEM_addr` from the address decoder of Wrapper
* unaligned address - easy to detect in Wrapper or RV. The additional instructions page has some info on it
* illegal instruction - from your instruction decoder
* division by zero - from MCycle


Some other tips:
* You can have a hardcoded exception handler address input to the multiplexer controlling the PC input. The interrupt input itself can be used as (part of the) multiplexer select input.
*   You can write your interrupt service routine in your assembly code, figure out the starting address, and use this value as hardcoded input to the mux. An alternative is to decide on a fixed handler address, and fill up spaces/NOPs in your code until the handler starts address. For example, if you fix the handler starting address to be 0x100 and your 'main' program contains 30 instructions, you will need to add 34 NOPs before the first instruction in the handler code so that handler code will indeed be at 0x100.
*   You should also have some mechanism to save `PC+4` into a register (say, `LR` (ARM) or `ra`/`x1` (RISC-V)) and to restore it when the handler has finished (use `MOV PC, LR` or `ret` to return from the handler).
*   If you wish to have vectored interrupts, you need to associate a number with each interrupt. You need a table/ROM with the starting addresses, which will be indexed by the interrupt number. The output of this ROM is to be fed into the PC multiplexer. The PC multiplexer control signal can be the logical OR of all the interrupt lines.