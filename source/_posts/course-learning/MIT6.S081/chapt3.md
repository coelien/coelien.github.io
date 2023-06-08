---
title: xv6笔记：chapt3 Page tables
tags: 
- MIT6.S081
- OS
- xv6
categories:
- Books
- Operating System
- xv6
---

# xv6笔记：chapt3 Page tables

> Why using page tables?

Isolate different process's address spaces and to multiplex them onto a single physical memory

> As a reminder

- RISC-V instructions manipulate virtual addresses.
- RAM is indexed with physical addresses

## Paging hardware

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230524100813171.png" alt="image-20230524100813171" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230524101110770.png" alt="image-20230524101110770" style="zoom:50%;" />

- a page table gives the os control over **virtual-to-physical address translations** at the granularity of 4096 bytes, such a chunk is called a page

- 页表在物理内存中以三级树的形式存储
  - 根页表是一个大小为4096-byte的页表，他有512个页表项
  - 每一级页表都保存这下一级页表的物理地址
- 如果翻译地址时有任何页表项不存在的话，分页硬件会抛出一个页错误异常，由内核来解决
- 通常情况下，大部分虚拟地址是没有映射的，这时候三级结构可以有效减少内存占用。
- 但是这种方式潜在的缺陷是，CPU需要从内存读入三次PTEs来进行转译
- 幸运的是，我们可以使用缓存页表的方式（TLB）来访存次数

### PTE状态bits

- PTE_V: 表示PTE是否存在，若未设置会抛出异常
- PTE_R：表示是否允许读取页

- PTE_W：表示是否允许写入页

- PTE_X：表示是否CPU会把页中的内容解释为命令，并执行
- PTE_U：表示是否允许用户模式下的命令访问页

（定义在kernel/riscv.h）

notes:

- 物理内存 == DRAM存储单元
- 一个字节的物理内存拥有一个地址
- 指令只使用虚拟地址,该地址由分页硬件翻译为物理地址
- 虚拟内存不是实际的对象而是内核对物理内存提供的抽象

## 内核地址空间

每个进程，有一张页表描述了进程的用户地址空间，还有一个单独的页表描述内核地址空间

### 内核内存布局(kernel memory layout)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230526151103105.png" alt="image-20230526151103105" style="zoom:50%;" />

- kernel gets at RAM and memory mapped device registers using **"direct mapping"**

- kernel virtual address that aren't direct-mapped:
  - trampoline page
  - kernel stack pages

## 进程虚拟地址空间布局

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230601101542690.png" alt="image-20230601101542690" style="zoom:50%;" />

当一个进程向os申请用户内存的过程：

- 使用kalloc分配物理页表
- 把页表项加入进程页表中
- 在加入的页表项中设置PTE_W, PTE_X, PTE_R, PTE_U, and PTE_V flags 

使用页表的好处：

- 不同的页表把用户地址翻译到不同的物理内存的页，所以每个进程都有自己的用户内存。
- 每个进程看到的内存是从0开始的连续虚拟地址，然而实际上进程的物理内存是非连续的
- 因为内核将陷入页映射到了用户地址空间的顶部，所有物理内存的单个页出现在了所有地址空间中。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230601104335288.png" alt="image-20230601104335288" style="zoom:50%;" />

## code: sbrk

- sbrk是增加或减少一个进程占用内存的系统调用，它的实现在`growproc`中。
- `growproc`会调用`uvmalloc`或是`uvmdealloc`来增加或减少一个进程占用的内存

## code: exec

- exec 是创建用户区域地址空间的系统调用。它从一个文件中初始化用户地址空间。







