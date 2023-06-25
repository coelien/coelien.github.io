---
title: MIT6.S081笔记：lecture 4
tags: 
- MIT6.S081
- OS
categories:
- courses
- Operating System
---

# MIT6.S081笔记：lecture 4 Page Tables

### Isolation

- why use virtual memory?

![image-20230613135254259](https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230613135254259.png)

- make bad code doesn't affect the os
- want sth really seperates these memories from different programs

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230613135851754.png" alt="image-20230613135851754" style="zoom:50%;" />

### How to give isolation?

**Address Spaces**

- give applications including the kernel its own address space

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230613140224359.png" alt="image-20230613140224359" style="zoom:50%;" />

- How to multiplex all these different address spaces on a single physical memory?

  **PAGE TABLES** (HW: memory management unit)

**Blueprint**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230613144656206.png" alt="image-20230613144656206" style="zoom:50%;" />

- every app has its own map which basically defines its address space

### PAGE TABLE

- not per address, per page! each page is 4kB

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230613145933296.png" alt="image-20230613145933296" style="zoom:50%;" />

- in xv6, virtual memory is less than physical memory space
- and it's inefficient to store all entries(2^27)

#### RISC-V PAGE TABLE

### <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230613152532911.png" alt="image-20230613152532911" style="zoom:50%;" />

- Use cache(TLB) to avoid 3 times of memory translations
- When switching page table,  also need to flush the TLB

### Kernel memory address space layout

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230613161038236.png" alt="image-20230613161038236" style="zoom:50%;" />

- every process has a corresponding kernel stack