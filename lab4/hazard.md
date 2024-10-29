---
nav_order: 3
parent: "Lab 4: (Near) Complete Processor + Pipelining + Bells + Whistles"
---
# Lab 4 Enhancement: Resolving Hazards

Here are the general steps to add hardware to resolve hazards.

1. Resolve data hazards by adding the hazard hardware as shown in the lecture. Note that this will require creating additional pipeline entries such as `RA1E`, `RA2E`, `RA2M`.

2. Verify that your design works still works. If not, it's time to debug. 

3. Change the design as given in slide 31 of Chapter 6 (assuming your program does not use `LDR PC, ...`).

4. Verify that your design works still works. If not, debug some more. 

5. Insert the control hazard hardware as shown in slide 33.

6. Verify that your design works still works. If it does, BINGO - you are on track to get a pretty good mark for the performance enhancement part!!! You may need to clear (parts of) the pipeline registers to have proper initial values for the various outputs of the hazard unit.

While flushing a stage, the reset/CLR for the pipeline register feeding that stage is asserted, causing an `NOP` to go into that stage. An easier solution (for E and later stages) is to just reset only those bits which affects the machine state (such as `FlagWE`, `MemWE` etc).