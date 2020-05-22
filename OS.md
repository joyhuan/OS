# OS 
## 02 Architectural Support for OS
- History of Operating Systems 
  - Phase 1: Hardware expensive, humans cheap 
  - Phase 2: Hardware cheap humans expensive 
  - Phase 3: H/W very cheap humans very expensive 
- On System Start Up 
  - BIOS copies bootloader into memory
  - Bootloader copies OS kernel into memory
  - Kernel:
    - Initializes data structures (devices, core map, interrupt vector table, etc.)
    - Copies first process from disk
    - Change privilege mode & PC
    - And the dance begins!
- Supporting dual mode operation 
  1. Privilege mode bit (0=kernel, 1=user)
      Where? x86 → EFLAGS reg., MIPS →status reg.
  2. Privileged instructions
      user mode -> no way to execute unsafe insns
  3. Memory protection
      user mode -> memory accesses outside a process’ memory region are prohibited
  4. Timer interrupts
      kernel must be able to periodically regain control from running process
  5. Efficient mechanism for switching
- Memory Protection 
  - Step 1: Virtualize Memory
    - Virtual address space: set of memory addresses that process can "touch" (CPU works with virtual addresses)
    - Pysical address space: set of memory addresses supported by hardware 
  - Step 2: Address Translation 
    - Function mapping <pid, vAddr> -> <pAddr>
- Interrupts
  - Timer Interrupts: 
    - Hardware timer set to expire after specified delay (time/instructions)
    - Time's up? Control passes back to kernel.
  - More Generally: Hardware Interrupts
    - External Event has happened. 
    - OS needs to check it out. 
    - Process stops what it's doing, invokes OS, which handles the interrupt. 
  - Interrupt Management
      - **Interrupt controllers** manage interrupts 
  - Aside 1: Interrupt Driven I/O
      - Memory-mapped I/O
          - Device communication goes over the memory bus 
          - I/O operations by dedicated device hardware correspond to reads/writes to special addresses 
          - Devices appear as if part of the memory address space 
      - Interrupt-driven operation with memory-mapped I/O: 
          - CPU initiates device operation (e.g. read from disk): writes an operation descriptor to a designated memory location
          - CPU continues its regular computation
          - The device asynchronously performs the operation 
          - When the operation is complete, interrupts the CPU 
          - Could happen for each byte read! 
  - Aside 2: Direct Memory Access (DMA)
- Efficient mechanism for switching modes 
  
## 03 Processes & Threads 
- What is a Program? 
  - executable code (machine instructions)
  - data (information manipulated by these instructions)

  that together describe a computation 
  - Resides on disk 
  - Obtained via compilation & linking 
- What is a Process? 
  - An instance of a program 
  - An abstraction of a computer: 
  
  Address Space + Execution Context + Environment 
- Process != Program 
  - A program is passive:
  
    code + data 
  - A process is alive: 
  
    code + data + stack + registers + PC ... 
- CPU runs each process directly
  - But somehow each process has its own: 
    - Registers 
    - Memory 
    - I/O resources 
    - "thread of control"
- Process Control Block (PCB) 
  For each process, the OS has a PCB containing:
    - location in memory
    - location of executable on disk
    - which user is executing this process
    - process identifier (pid)
    - process status (ready, waiting, finished, etc.)
    - scheduling information
    - kernel SP (points in interrupt stack)
    - interrupt stack contains saved process registers
    - … and more!
- System Call Interface 
  - Beginning a Process via fork()
    - Kernel has to: 
      - Allocate ProcessID 
      - Create & initialize PCB in the kernel 
      - Creat a new address space 
      - Initialize the address space with a copy of the entire contents of the address space of the parent 
      - Inherit execution context of parent (eg. open files)
      - Inform scheduler that new process is ready to run 
- What is a Shell? 
  - Job control system 
  - runs programs on behalf of the user 
  - allows programmer to create/manage programs 
    - sh Original Unix shell 
    - csh 
    - bash 
  - Runs at user-level. Uses syscalls: fork, exec, etc.
- Sendign a Signal 
  - Kernel delivers a signal to a destination process for one of the following reasons: 
    - Kernel detected a system event (eg. div-by-zero (SIGFPE) or termination of a child (SIGCHLD)) )
    - A process invoked the **kill system call** requesting kernel to send signal to a process
      - debugging
      - suspension
      - resumption
      - timer expiration 

- Receiving a Signal 
  - Three possible ways to react: 
    1. Ignore the signal (do nothing)
    2. Terminate process (+ optional core dump)
    3. Catch the signal by executing a user-level function called signal handler
      - Like a hardware exception handler being called in response to an asynchronous interrupt 

- Threads! 
  - Other terms for threads
    - Lightweight Process 
    - Thread of Control 
    - Task 
  -  Process vs. Thread 
    - Process
      - Privilege Level 
      - Address Space 
      - Code, Data, Heap 
      - Shared I/O resources 
      - One of more Threads: 
        - Stack
        - Registers 
        - PC, SP 
    - Process abstraction combines two concepts
        - Concurrency: each process is a sequential execution stream of instructions
        - Protection: Each process has own address space
    - Threads decouple concurrency & protection
        - A thread represents a sequential execution stream of instructions.
        - A process defines the address space that may be shared by multiple threads
        - Threads must be mutually trusting. Why?

  - Implementation of Threads 
     - One abstraction, two implementations:
        - 1. “kernel threads”: each thread has its own PCB in the kernel, but the PCBs point to the same physical memory
        - 2. “user threads”: one PCB for the process; threads implemented entirely in user space. Each thread has its own Thread Control Block (TCB)
  
## 04  
