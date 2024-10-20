
# Work in progress
******* New features in V2 *******
- Ability to use the hexadecimal text dump from RARS directly without any conversion software or copy-pasting needed.
- Instruction and data memory sizes can be bigger than 128 words. Be mindful of the potentially increased synthesis time though.
- Addresses except IROM_BASE and DMEM_BASE are hierarchically derived instead of hard-coding.
- Byte and half-word write to data memory and 7-segment display (sb and sh support) - aligned memory addresses and pre-shifted/aligned data required. Please read the relevant comments carefully.
--Note: byte and half-word read don't require any Wrapper support - you can simply read the whole byte, extract the byte/half-word, and extend as necessary.
- Possible to use a different Memory Configuration from RARS, *except* supporting 32'hFFFF0000 as the MMIO base in the RARS default. MMIO_BASE = DMEM_BASE + 2**DMEM_DEPTH_BITS in all configs.
- Possible to use block RAMs (sync read) for instruction and data memories in the pipelined version. Allows faster synthesis times and possibly clock rates for larger memory sizes.
- Renamed for simplicity and concistency with assembly program labels: INSTR_MEM->IROM; DATA_CONST_MEM->DROM; DATA_VAR_MEM->DRAM. DROM and DROM combined into DMEM in v3
- Updated files: Wrapperv2.v, RVv2.v, and ProgramCounterv2.v (necessary only if the Memory Configuration is changed in RARS).


V2:
To use FPGA block RAMs for instruction and data memories in pipelined version (Allows faster synthesis times and possibly clock rates for larger memory sizes):
(Disclaimer: Not tested fully). Change to always@(posedge clk) where relevant. Read the comments for more info. 
If enabled, Instr, ReadData_in are delayed by 1 cycle. Therefore, what you get can be used as InstrD, ReadDataW directly. MMIO reads are also delayed. Register file outputs are RD1E and RD2E directly if it is done for the register file.

The required byte/half-word will still have to be extracted from ReadDataW and zero/sign extended in W stage if using lb/lbu/lh/lhu.
*/

/* 
V3:
Renaming: CONSOLE -> UART. CONSOLE_OUT -> UART_TX, CONSOLE_IN -> UART_RX
*/

//----------------------------------------------------------------
// V2: Sizes of various segments, base addresses, and peripheral address offsets.
//----------------------------------------------------------------
// Set the number of bits for the byte address. 
// Depth (size) = 2**DEPTH_BITS. e.g.,if DEPTH_BITS = 9, depth = 512 bytes = 128 words. 
// Make sure that the align directive in the assembly programme is set according to the sizes of the various segments.
// The size of a data segment affects the *next* segment alignment and address.
// Keep in mind that large memory sizes can cause synthesis times to be longer, esp if not using synch read (block RAM)

// Base addresses of various segments
// The RARS default memory configuration is IROM_BASE = 32'h00400000 and DMEM_BASE = 32'h10010000 in RARS.
// The RARS default MMIO base is 32'hFFFF0000, but this is hard to support. So we use MMIO_BASE = DMEM_BASE + 2**DMEM_DEPTH_BITS in all memory configurations
// We use compact memory configuration with .txt at 0 where IROM_BASE = 32'h00000000 and DMEM_BASE = 32'h00002000, but you can change this to either of the other two if you wish.
// Do not use absolute addresses (e.g., using li pseudoinstruction for addresses) for memory/MMIO unless you know what you are doing. If you are building on the sample HelloWorld, use la for SEVENSEG.
//  Relative addresses (e.g., la pseudoinstruction) work fine for all starting addresses and segment sizes

// Other devices for you to use: Built in mic (could be interesting), RGB LEDs (not too interesting), USB host, etc.
// Not too difficult using components/modules from https://github.com/Digilent/Nexys-4-DDR-OOB

// Future work: Implementing a bootloader. The bootloader will be in a ROM. Instruction memory will be a RAM.
// If a button (say, BTN_C) is pressed,the bootloader waits for serial data, stores it into Instruction and data memory.
// It will then call main. If button not pressed

// Actually a good idea not to have the next peripheral offset immediately following this, to add future registers for OLED

// In embedded systems, constants are kept in a flash memory (ROM)
// Techincally, RAM should not be initalized. Initialization works for RAMs in FPGAs, but not for standard RAMs. You must store before you can load.

// Possible spurious UART_RX_ack and a lost character if we don't have a MemRead signal. 
// Alternatively, make sure ALUResult is never the address of UART other than when accessing it.
// Also, the character received from the PC in the CLK cycle immediately following a character read by the processor is lost. 
	// This is not that much of a problem in practice though (need to check if it still exists after adding MemRead).

 DIP // Only the least significant 16 bits read from this location are valid.
PB // Only the least significant 3 bits read from this location are valid. Order (2 downto 0) ->  BTNL, BTNC, BTNR
mapped to LED_OFF. Only the least significant 8 bits written to this location are used.
The 32-bit value will appear as 8 Hex digits on the display.

UART_TX
The least significant 8 bits written to this location are sent to PC via UART.
						// Check if UART_TX_ready (UART_TX_ready_OFF) is set before writing to this location (especially if your CLK_DIV_BITS is small).
						// Consecutive STRs to this location not permitted (there should be at least 1 instruction gap between STRs to this location).

  //  This bit should be set in the testbench to indicate that it is ok to write a new character to UART_TX from your program.

  							
							// Also, note that there is no Tx FIFO implemented. DO NOT send characters from PC at a rate faster than 
							//  your processor (program) can read them. This means sending only 1 char every few seconds if your CLK_DIV_BITS is 26.
							// 	This is not a problem if your processor runs at a high speed.

  24-bit pixel so as to see easily on the display.

  The accel actually gives 12 bit, but we use low-res
