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
