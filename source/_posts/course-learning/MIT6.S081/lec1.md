---
title: MIT6.S081笔记：lecture 1
tags: 
- MIT6.S081
- OS
categories:
- courses
- Operating System
---

# MIT6.S081笔记：lecture 1

> care about what is going on under the hook
>
> like infrastructure
>
> care about high performance

## Course Goals

- OS design and implementation
- Hands-on experience **extending a small os**
- Hands-on experience **writing systems software**

## OS Purposes

- **ABSTRACTION** of the hardware for convenience and portability
- **MULTIPLEX** the hardware among many applications
- **ISOLATION**
- **SHARING** among cooperating applications
- **SECURITY**(PERMISSION)
- **PERFORMANCE**
- RANGE of **USES**

## OS ORGANIZATION

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303310940238.png" alt="image-20230331094036040" style="zoom:50%;" />

- user app: vi, gcc, DB, &c
- kernel services
- hardware

we care a lot about the **interfaces and internal kernel structure**

## What services does an O/S kernel typically provide?

- process
- memory allocation
- file contents
- file names, directories
- access control(securities)
- many others: users, IPC, network, time, terminals

## What's the kernel interface?

- "System calls"
- like open()
- write()
- fork()

## Why is O/S design+implementation hard and interesting?

- unforgiving environment: hard to debug
- many design tensions:
  - efficient vs abstract/portable/general-purpose
  - powerful vs simple interfaces
  - flexible vs secure
- features interact: `fd = open(); fork()`
- uses are varied: laptops, smart-phones, cloud, virtual machine, embedded
- evolving hardware: NVRAM, multi-core, fast networks

## API for the Kernel

- fd = open("out",1)
- write(fd,"hellp\n",6)
- pid = fork();

system calls are special because they jump into the kernel

> Why studying operating system both challenging and interresting?

- unforgiving
- tensions
  - efficient - abstract interfaces
  - powerful os services - simple interfaces
  - flexible - secure

- interact

```
fd = open()
pid = fork()
```

## Class structure

* Online course information:
  https://pdos.csail.mit.edu/6.S081/ -- schedule, assignments, labs
  Piazza -- announcements, discussion, lab help

* Lectures
  * O/S ideas
  * case study of xv6, a small O/S, via code and xv6 book
  * lab background
  * O/S papers
  * submit a question about each reading, before lecture.

* Labs: 
  The point: hands-on experience
  Mostly one week each.
  Three kinds:
    Systems programming (due next week...)
    O/S primitives, e.g. thread switching.
    O/S kernel extensions to xv6, e.g. network.
  Use piazza to ask/answer lab questions.
  Discussion is great, but please do not look at others' solutions!

* Grading:
  70% labs, based on tests (the same tests you run).
  20% lab check-off meetings: we'll ask you about randomly-selected labs.
  10% home-work and class/piazza discussion.
  No exams, no quizzes.
  Note that most of the grade is from labs. Start them early!

## Introduction to UNIX system calls

* what happens when a program calls a system call like open()?

looks like a function call, but it's actually a **special instruction**
hardware **saves** some user registers
hardware **increases privilege level**
hardware **jumps to a known "entry point"** in the kernel
now **running C code** in the kernel
kernel calls **system call implementation**
  open() looks up name in file system
  it might wait for the disk
  it updates kernel data structures (cache, FD table)
**restore** user registers
**reduce privilege level**
**jump back to calling point in the program**, which resumes
we'll see more detail later in the course

### fork.c

> the shell create a new process for each time you type

- the fork() system call create a new process

- the kernel makes a copy of the calling process

  - instructions,

  - data,

  - registers,

  - file descriptors,

  - current directory

  - "parent" and "child" processes

    only difference: **fork() returns a pid in parent, 0 in child**

> How can  we run program in that process?

### exec.c

- **replace** calling process with an executable file
- discards instruction and data memory
- loads instructions and memory from the file
- **preserves file descriptors**

### forkexec.c

> **a common UNIX idiom**

- fork() a child process
- exec() a command in the child
- parent wait()s for child to finish

the shell does **fork/exec/wait** for every command you type

**to run in the background** -- & -- the shell skips the wait()

> some convention

- exit(status)->wait(&status)
- 0 = success, 1=command enountered an error
- **note:** the fork() copies, but exec() discards the copied memory. this may seem wasteful, you'll transparently eliminate the copy in the "copy-on-write" lab

### redirect.c

> redirect the output of a command

what does the shell do for this?

```
  $ echo hello > out
```

- fork
- change FD 1 in child
- exec echo

**note:** open() always chooses lowest unused FD; 1 due to close(1).

fork, FDs, and exec interact nicely to implement **I/O redirection**

**only sh** has to know about I/O redirection, not each program

### pipe1.c

> communicate through a pipe

how does the shell implement?

- an FD can refer to a "pipe", as well as a file
- the pipe() system call creates two FDs
  - read from the first FD
  - write to the second FD
- the kernel maintains a buffer for each pipe[u/k diagram]
  - write() appends to the buffer
  - read() waits until there is data

### pipe2.c

> communicate between processes

pipes combine well with fork() to implement ls | grep x

- shell creates a pipe
- then fork twice
- then connects ls 's FD 1 to pipe's write FD
- connects grep's FD 0 to the pipe

## Asking about design decisions

- why these I/O and process abstractions? why not something else?

- why provide a file system? why not let programs use the disk their own way?
- why FDs? why not pass a filename to write()?
- Why are files streams of bytes, not disk blocks or formatted records?
- why not combine fork and exec?
  - separate fork-then-exec give child a chance to change FDs before exec

## Summary

* We've looked at UNIX's I/O, file system, and process abstractions.
* The interfaces are simple -- just integers and I/O buffers.
* The abstractions combine well, e.g. for I/O redirection.
