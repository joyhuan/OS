# Topic 

Side: It's interesting how everything starts to fit into each other and makes much better sense when I look back after three years. 
## 02 Gates & Logic 
## 03 Numbers & Arithmetic 
## 04 State & FSMs
## 05 Intro to a MIPS Processor 
## 06 Pipelining 
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
## 07 Calling Conventions 
- The Stack 
    - Stack	contains stack frames (aka “activation records”)
        - 1	stack frame	per	dynamic	function
        - Exists only for the duration of function
        - Grows	down, “top”	of stack is $sp, r29	
        - Example: lw $r1, 0($sp) puts word at top of stack into $r1	
    - Each stack frame contains:
        - Local	variables, return address (later), register backups (later)
- The Heap 
    - Heap holds dynamically allocated memory 
        - Program must maintain pointers to anything allocated 
        - Data exists from malloc() to free() 
- Data Segment 
    - Data segment contains global variables 
        - Exist for all time, accessible to all routines 
        - Accessed w/global pointer 
            - $gp, r28, points to middle of segment 
- Calling Convention for Procedure Calls 
    - Transfer Control 
        - Caller -> Routine 
        - Routine -> Caller 
        - Return Address lives in Stack Frame 
            - Stack Manipulated by push/pop operations 
    - Pass Arguments to and from the routine 
        - fixed length, variable length, recursively 
        - Get return value back to the caller 
        - Pros 
            - Consistent way of passing arguments to and from subroutines 
            - Creates single location for all arguments 
                - Caller makes room for $a0-$a3 on stack 
                - Callee must copy values from $a0-$a3 to stack 
                    - callee may treat all args as an array in memory 
                - Particularly helpful for functions w/ variable length inputs 
            - Aside: not a bad place to store inputs if callee needs to call a functino (your input cannot stay in $a0 if you need to call another function!)
    - Manage Registers
        - Allow each routine to use registers 
        - Prevent routines from clobbering each others' data 
        - Register Management 
            
            Functions: 
            - Are compiled in isolation 
            - Make use of general purpose registers 
            - Call other functions in the middle of their execution 
                - These functions also use general purpose registers! 
                - No way to coordinate between caller & callee 
            
            Need a convention for register management 
- **Missing Caller and Callee for now; will append shortly in future**            
## 07 Performance 
### Performance: Latency vs. Throughput 
Latency(execution time): time to finish a fixed task 

Throughput(bandwidth): # of tasks in fixed time 
    - Different: exploit parallelism for throughput, not latency 
    - Often contradictory (latency vs. throughput)
        - Will see many egs of this 
    - Use definition of performance that matched your goals 
        - Scientific program: latency; web server: throughput? 

- Processor Performance Equation 

    Program runtime: seconds/program = instructions/program * cycles/instruction * seconds/cycle 

    - Instructions per program: “dynamic instruction count”
        - Runtime count of instructions executed by the program
        - Determined by	program, compiler, ISA
    - Cycles per instruction: “CPI” (typical range: 2 to 0.5)
        - How many cycles does an instruction take to execute?
        - Determined by	program, compiler, ISA, micro-architecture
    - Seconds per cycle: clock period, length of each cycle
        - Inverse metric: cycles/second	(Hertz)	or cycles/ns (Ghz)
        - Determined by	micro-architecture,	technology parameters
    For	lower latency (=better performance) minimize all three
        - Difficult: often pull against one another
- Mhz (MegaHertz) and Ghz(GigaHertz)
    - 1 Hertz = 1 cycle/second 
    - 1 Ghz = 1 cycle/nanosecond, 1 Ghz = 1000 Mhz 
    - General public (mostly) ignores CPI
        - Equates clock frequency with performance!
    - Which	processor would	you	buy?
        - Processor	A:	CPI	=	2,	clock	=	5	GHz
        - Processor	B:	CPI	=	1,	clock	=	3	GHz
        - Probably	A,	but	B is faster (assuming same ISA/compiler)
    - Classic example
        - 800 MHz PentiumIII faster than 1 GHz Pentium4!	
        - Example: Core i7 faster clock-per-clock than	Core 2
        - Same	ISA	and	compiler!
    - Meta-point: danger of	partial	performance	metrics!
- MIPS (performance metric, not the ISA)
    - (Micro) architects of ten ignore dynamic instruction count 
        - Typically	have one ISA, one compiler -> treat	it as fixed
    - CPU performance equation becomes
        - Latency: seconds/insn = cycles/insn * seconds/cycle 
        - Throughput: insn/seconds  = insn/cycles * cycles/second 
    - MIPS(millions of instructions per second
        - Cycles / second: clock frequency (in MHz) 
        - Ex: CPI = 2, clock = 500 MHz -> 0.5 * 500 MHz = 250 MIPS 
    - Pitfall: may vary inversely with actual performance 
         - Compiler removes insns, program faster, but lower MIPS 
         - Work per instruction varies (multiply vs. add, FP vs. integer))
- How to make the computer faster? 
    - Decrease latency 
    - Critical Path 
        - Longest path determining the minimum time needed for an operation 
        - Determines minimum length of clock cycle i.e. determines maximum clock frequency
- Amdahl’s Law 
    - Execution time after improvement = execution time affected by improvement/amount of improment + execution time unaffected
    - Or: Speedup is limited by popularity of improved feature 
    - Corollary: **build a balanced system** 
        - Don’t optimize 1% to the detriment of other 99% 
        - Don’t over-engineer capabilities that cannot be utilized 
    - Caveat: Law of diminishing returns
