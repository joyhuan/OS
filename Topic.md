# Topic 

Side: It's interesting how everything starts to fit into each other and makes much better sense when I look back after three years. 
## Gates & Logic 
## Numbers & Arithmetic 
## State & FSMs
## Intro to a MIPS Processor 
## Pipelining 
### 5-stage Pipeline
#### Implementation 
- Principles of Pipelined Implementation 
    - Break datapath into multiple cycles 
        - Parallel execution increases throughput 
        - Balanced pipeline very importatnt 
    - Add pipeline registeres (flip-flops) for isolation 
        - Each stage begins by reading values *from* latch 
        - Each stage ends by writing values *to* latch 
    - Resolve hazards 
- Pipeline Stages 
    
    Fetch -> Decode -> Execute -> Memory -> Writeback 
#### Working Example 
### Hazards 
#### Structural 
Same resource needed for different purposes at the same time (Possible: ALU, Register File, Memory)
- Resolving Register Structural Hazard 
    - negate RF clock: write first half, read second half 
- Dependences and Hazards 
    - Dependence: relationship between two insns 
        - Data: two insns use same storage location 
        - Control: 1 insn affects whether another executes at all 
        - *Not a bad thing*, programs would be boring otherwise 
        - Enforced by making older insn go before younger one 
    - Hazard: dependence & possibility of wrong insn order 
         - Effects of wrong insn order cannot be externally visible 
         - *Bad thing*: most solutions either complicate the hardware or reduce performance 
#### Data Hazards 
Instruction output needed before it's available 

- Detecting Data Harzards 

    - Stall	=	(IF/ID.Ra !=	0	&&	 (IF/ID.Ra ==	ID/EX.Rd   ||	IF/ID.Ra ==	EX/M.Rd))
- Possible Responses to Data Hazards 
    - Do nothing 
        - Change the ISA to match implementation 
        - "Hey compiler: don't create code w/data hazards!"
        (We can do better than this)
    - Stall 
        - Pause current and subsequent instructions till safe 
    - Forward/bypass
        - Forward data value to where it is needed 
        (Only words if value actually exists already)
- Stall 
    - **Much nitty-gritty are neglected here; will return if needed in future**
    
- Takeaways 
    - Data hazards occur when a	operand	(register) depends on the result of	a previous instruction	that may not be computed yet. Pipelined	processors need to detect data hazards.
    - Stalling, preventing a dependent instruction from advancing, is one way to resolve data	hazards. Stalling introduces NOPs (“bubbles”) into a pipeline. Introduce NOPs by	
        1. preventing the PC from updating,	
        2. preventing writes to	IF/ID registers from changing, and	
        3. preventing writes to memory and register file. Nops significantly decrease performance.
    - Forwarding bypasses some pipelined stages forwarding a result to a dependent instruction operand (register). Better performance than stalling.
#### Control Hazards 
Next instruction PC unknown at time of Fetch 
- Zap & Flush 
    - prevent PC update
    - clear IF/ID latch
    - branch continues
- Reducing the cost of control hazard 
    - Delay Slot 
        - You MUST do this 
        - MIPS ISA: 1 insn after ctrl insn *always* executed 
            - Whether branch taken or not 
    - Resolve Branch at Decode 
        - Some groups do this for Project 2, your choice 
        - Move branch calc from EX to ID
        - Alternative: just zap 2nd instruction when branch taken 
    - Branch Prediction 
        - Not in 3410, but every processor worth *anything* does this (no offense!)
- Takeaways 
    - Control hazards occur because the	PC following a control instruction is not known	until	control	instruction	is	executed. If branch	is taken -> need to	zap	instructions. 1	cycle performance penalty.
    - Delay	Slots can potentially increase performance due to control hazards. The instruction	in	the	delay slot will always be executed. Requires software(compiler)	to make use of delay slot. Put nop in delay slot if not able to	put	useful instruction in delay slot.
    - We can reduce	cost of	a control hazard by	moving branch decision and calculation from Ex	stage to ID	stage. With a delay slot, this removes the need to flush instructions on taken branches.
