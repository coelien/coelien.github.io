---
title: xv6笔记：chapt2 Operating system organization
tags: 
- MIT6.S081
- OS
- xv6
categories:
- Books
- Operating System
- xv6
---

# xv6笔记：chapt2 Operating system organization

## Requirements for an OS

- support several activities at once
- **multiplexing**: time-share the resources of the computer among these processes, every process has a chance to execute.

- **isolation**: if one process has a bug and malfunctions, it shouldn't affect processes that don't depend on the buggy process
- **interaction**: for instance, pipelines

## Focus

This chapter focus on the **mainstream designs** centered around a monolithic kernel.(used by many operating systems like linux)

> xv6 runs on **RISC-V** microprocessor, RISC-V is a 64-bit CPU, and xv6 is written in "LP64"C.

> RISC-V reference: https://riscv.org/technical/specifications/

## The Support Hardware

Xv6 is written for the support hardware simulated by qemu’s  **“-machine virt”** option  

hardware includes:

- RAM
- ROM containing boot code
- serial connection to the keyboard/screen
- disk

## Abstracting physical resources

> why need strong isolation?

- the applications may not be well behaved (we do not trust application to periodically give up the CPU)
- the applications may have bugs

> How to achieve strong isolation?

- **forbid** applications from directly accessing sensitive hardware resources
- instead to **abstract the resources into services**

### Example 1

for example, Unix applications interact with storage only through the file system's open, **read, write, close system calls**, instead of reading and writing disk directly.

- application will not pay attention to the details, they only need pathnames.

### Example 2

> the details are **transparent** to the application

- the applications don't have to be aware of the **time sharing**
- Unix **transparently switches** hardware CPUs among processes

### Example 3

- Unix processes use exec to build up their memory image(instead of directly interacting with physical memory)
- let the OSto decide where to place a process in memory
- OS may also store some of a process' s data on disk if the memory is tight

### Example 4

- many forms of interaction among Unix processes occur via **file descriptors**.
- file descriptors abstract away many details (where data in a pipe is stored)
- file descriptors simplify the interactions ( if one application in a pipeline fails, the
  kernel generates an end-of-file signal for the next process in the pipeline)

## User mode, supervisor mode, and system calls  

### Three modes in RISC-V

- machine mode : 
  - full privilege
  - CPU starts in machine mode
  - this mode is intended for configuring a computer
- supervisor mode: 
  - execute privileged instructions :
  - such as enabling and disabling interrupts
  - reading and writing the register that holds the address of a page table
  - the software in supervisor mode can also execute privileged instructions and is said to be running in **kernel space**  
  - The software running in kernel space (or in supervisor mode) is called the kernel.  
  - It is important that the kernel control the entry point for transitions to supervisor mode;  (avoid the malicious application)
- user mode

## Kernel organization

### monolithic kernel

> the entire operating system resides in the kernel ( linux )

**advantages**

- **convenient**, OS designer doesn't have to decide which part of OS doesn't need full hardware privilege
- easier for different parts of the operating system to **cooperate**
- high performance to OS-intensive applications , because subsystems of the kernel can be **tightly integrated**  

**disadvantages**

- interfaces between different parts of the operating system is complex
- easy for os developer to make a mistake

### microkernel 

> kernel is simple (Minix, L4, QNX), used widely in ebedded settings

- **minimize** the amount of operating system code that runs
- execute the bulk of the operating system in user mode 
- This organization allows kernel to be relatively simple

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230413173447183.png" alt="image-20230413173447183" style="zoom:50%;" />

- in the above picture, file system runs as a user-level process
- OS services running as processes are called **servers**

- application interacts with the server by an **inter-process communication mechanism** to send messages from one user-mode process to another
- the kernel interface consists of a few low-level functions for **starting applications**, **sending messages**, **accessing device hardware**, etc  

### Which one is better?

> It depends

- faster performance (mono)
- smaller code size 
- reliability of the kernel (micro)
- reliability of the complete operating system  

## Code: xv6 kernel organization

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230414095605940.png" alt="image-20230414095605940" style="zoom: 33%;" />

It is kind of simple surprisingly. With about 30 files, you can build a real world kernel!

The inter-module interfaces are defined in  `defs.h  `

## Process overview

- In Unix OS, the *unit of isolation* is **process**

- **process abstraction** can prevent one process from **destroying or spying** on another process's memory, CPU, file descriptors, etc.   

- **process abstraction** also prevents a process from wrecking the kernel itself  
- **process abstraction** needs to be carefully designed and implemented so that no buggy or malicious applications may **trick** the kernel or hardware into doing something bad (for example, avoiding isolation)

### key design ideas in abstracting process

- an address space to give a process the illusion of its own memory 
- a thread to give the process the illusion of its own CPU  

In xv6, a process consists of **one address space and one thread,** in the real world, a process may have more than one thread to **take advantage of multiple CPUs**  

### mechanism to implement process

- the user/supervisor mode flag

- address spaces

- time-slicing of threads  

>  How to enforce isolation?

- provides the **illusion** to a program that it has its **own private machine**

- provides a program with what appears to be a **private** memory system, or **address space**

- so other process cannot read or write

> How to implement process in xv6?

xv6 uses **page tables**(which are implemented by hardware ) to give each process its own address space.

The RISC-V page table translates (or “**maps**”) a **virtual address** (the address that an RISC-V instruction manipulates) to a **physical address** (an address that the CPU chip sends to main memory).


<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230414102804045.png" alt="image-20230414102804045" style="zoom:50%;" />

each process has a separate page table that defines process's address space. Process's address space includes:

- user memory starting at address 0
- instructions
- global variables
- stack
- heap
- At the top of the address space xv6 reserves a page for a trampoline and a page mapping the process’s trapframe.  

> transition into kernel and back

xv6 use these two pages to transition into the kernel and back:

- **the trampoline page** contains the code to transition in and out of the kernel  
- a page **mapping the process’s trapframe**  is necessary to save/restore the state of the user process  

> What limit the maximum size of a process’s address space?

- pointers on the RISC-V are 64 bits wide  
- hardware only uses low 39 bits
- xv6 only uses 38 of those 39 bits  
- Thus, the maximum address is 2^38 - 1 = 0x3fffffffff , which is MAXVA, (kernel/riscv.h)

### The process state structure in xv6

- xv6 maintains many pieces of state for each process: `struct proc` (kernel/proc.h)
- the most important pieces of kernel state:
  - page table
  - kernel stack
  - run state
- use notation: `p->xxx` to refer to the elements of the proc structure
- p->state indicates whether the **process is allocated**, **ready to run**, **running**, **waiting for I/O**, or
  **exiting**.  
- p->pagetable holds the process’s page table, in the format that the RISC-V hardware expects.   

### Thread and Process

- each process has a thread of execution which can be suspended and later resumed.
- much of the state of a thread is stored on the thread's stacks. 
- each process has two stacks:
  - **user stack**
  - **kernel stack**(p->kstack): when the process enters the kernel, t**he kernel code executes on** the process's kernel stack
- Thread alternates between actively using its user stack and its kernel stack
- **The kernel stack is separate and protected from user code**, so that even if a process has wrecked its user stack, the kernel can execute.

## Code: starting xv6, the first process and system call  

> How the kernel starts and runs the first process?

- initialize itself
- run a boot loader (stored in read-only memory), the boot loader loads the xv6 kernel into memory

The loader loads the xv6 kernel into memory at physical address 0x80000000.  (because the address range 0x0:0x80000000 contains I/O devices  )

- in machine mode, the CPU executes xv6 starting  at `_entry` (kernel/entry.S)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230414112242963.png" alt="image-20230414112242963" style="zoom:50%;" />

The RISC-V starts with paging hardware disabled: **virtual addresses map directly to physical addresses.**  

The instructions at `_entry` **set up a stack** so that xv6 can run C code. 

> Xv6 declares space for an initial stack, `stack0`, in the file start.c. The code at `_entry` loads the stack pointer register `sp` with the address `stack0+4096`, the top of the stack, because the stack on RISC-V grows down. 

- Now kernel has a stack. `_entry` calls into C code at `start`

> start performs some configuration that is only allowed in machine mode, and then switches to supervisor mode

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230414145207866.png" alt="image-20230414145207866" style="zoom: 50%;" />

- Before jumping into supervisor mode, it programs clock chip to generate timer interrupts.
- start "returns"  to supervisor mode by calling `mret`. This causes the program counter to change to main  
- main initializes several devices and subsystems

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230414153246851.png" alt="image-20230414153246851" style="zoom:50%;" />

- main then creates the first process by calling `userinit`

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230414153736861.png" alt="image-20230414153736861" style="zoom:50%;" />

- In the first process, it makes the first system call exec loads the number into register a7 and calls `ecall` to re-enter the kernel

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230414154046404.png" alt="image-20230414154046404" style="zoom:50%;" />

- the kernel use the number stored in a7 to call the desired system call

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230414154610669.png" alt="image-20230414154610669" style="zoom:50%;" />

- the system call table maps SYS_EXEC to sys_exec
- exec replaces the memory and registers of the current process with the new program

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230414155105101.png" alt="image-20230414155105101" style="zoom:50%;" />

- In this case, the new program will be the following, once the kernel has completed `exec`,  it returns to user space in the `/init` process. 
- Init creates a new console device if needed, then open it as file descriptors 0,1,2. Then it starts a shell on  the console. The system is up.

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230414161806283.png" alt="image-20230414161806283" style="zoom:50%;" />

## Security Model

> How operating system deals with buggy or malicious code?

### Typical security assumptions and goals

**for user code**



**for kernel code**

- Kernel code is expected to be bug-free  

- certainly to contain nothing malicious  

### Real life things are not so straightforward

- It’s difficult to prevent clever user code from making a system unusable (or causing it to panic) by consuming kernel-protected resources – **disk space, CPU time, process table slots**, etc.  

- It’s usually impossible to write **bug-free code** or design **bug-free hardware**;   
