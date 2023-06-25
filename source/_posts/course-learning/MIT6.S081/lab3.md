---
title: MIT6.s081 lab3记录：页表
tags: 
- MIT6.S081
- pipe
- CSP
categories:
- courses
- Operating System
---

# MIT6.s081 lab3记录：页表

## Print a page table

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230616163749792.png" alt="image-20230616163749792" style="zoom:50%;" />

```
page table 0x0000000087f6e000
 ..0: pte 0x0000000021fda801 pa 0x0000000087f6a000
 .. ..0: pte 0x0000000021fda401 pa 0x0000000087f69000
 .. .. ..0: pte 0x0000000021fdac1f pa 0x0000000087f6b000
 .. .. ..1: pte 0x0000000021fda00f pa 0x0000000087f68000
 .. .. ..2: pte 0x0000000021fd9c1f pa 0x0000000087f67000
 ..255: pte 0x0000000021fdb401 pa 0x0000000087f6d000
 .. ..511: pte 0x0000000021fdb001 pa 0x0000000087f6c000
 .. .. ..509: pte 0x0000000021fdd813 pa 0x0000000087f76000
 .. .. ..510: pte 0x0000000021fddc07 pa 0x0000000087f77000
 .. .. ..511: pte 0x0000000020001c0b pa 0x0000000080007000
```

- What does page 0 contain? 

  code and data

  ![image-20230616151448572](https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230616151448572.png)

-  What is in page 2?

  user stack

- When running in user mode, could the process read/write the memory mapped by page 1?

  ```
  pte: 0x0000000021fda00f
  ```

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230616160818729.png" alt="image-20230616160818729" style="zoom:50%;" />

  can't read , write, and execute in user mode, this could be the guard page

- What does the third to last page contain?

  the last page is the trapoline page, but the last two i am not sure, may be the heap and trapframe

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230616160412436.png" alt="image-20230616160412436" style="zoom:50%;" />

## Detecting which pages have been accessed ([hard](https://pdos.csail.mit.edu/6.828/2021/labs/guidance.html))

### Task

- implement pgaccess()：报告哪些页被访问了
- 参数：
  - 第一个用户页的起始虚拟地址
  - 需要检查的页的数量
  - 用来存结果的用户缓存地址