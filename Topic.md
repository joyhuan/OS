# Topic 

Side: It's interesting how everything starts to fit into each other and make much better sense when I look back after three years. 
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

#### Control Hazards 
Next instruction PC unknown at time of Fetch 
