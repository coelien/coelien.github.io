# chapter 1 unix基础知识

## 1.1 引言

从程序员的角度，快速浏览UNIX。

## 1.2 UNIX体系结构

操作系统狭义上指的就是内核，内核的接口称为系统调用，公用函数库建立在系统调用之上。应用程序既可以使用公用函数库，也可以使用系统调用。shell是一个特殊的应用程序，为运行其他应用提供接口。

广义上讲，操作系统包含内核和一些其他软件。实际上Linux是GNU操作系统的内核，但大家普遍上称其为操作系统

## 1.3 登录

可在/etc/passwd文件中查看登录名，目前系统已经加密口令移到了另一个文件中

## 1.4 文件和目录

- 目录：**一个包含目录项的文件**。逻辑上认为每个目录项都包含一个文件名和文件属性信息。stat命令可以查看文件属性。目录项的逻辑视图与实际在磁盘上的存储是不同的。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207151723488.png" alt="image-20220715172312460" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207151720796.png" alt="image-20220715172038726" style="zoom:50%;" />

- 创建新目录时，会自动创建两个文件名：`.` 和 `..`
- 列出一个目录所有文件的名字

## 1.5 输入和输出

- 文件描述符：小的非负整数，内核用以标识特定进程正在访问的文件。当打开或是创建一个新文件时，内核都返回一个文件描述符，在读写的时候可以使用它
- 标准输入，标准输出和标准错误：每当运行一个新程序时，shell都会默认为其打开3个文件描述符，即标准输入，标准输出和标准错误，若不做特殊处理，这三个文件描述符都链接向终端。
- 不带缓冲的IO：
  - read：返回读取的字节数。到达文件尾端时，返回0；出错时，返回-1
  - write：返回写入的字节数
```c
#include "apue.h"

#define    BUFFSIZE   4096

int
main(void)
{
   int       n;
   char   buf[BUFFSIZE];

   while ((n = read(STDIN_FILENO, buf, BUFFSIZE)) > 0)
      if (write(STDOUT_FILENO, buf, n) != n)
         err_sys("write error");

   if (n < 0)
      err_sys("read error");

   exit(0);
}
```
- 标准IO的优势：
  - 无需担心选取最佳的缓冲区大小
  - 简化了对输入的处理：如fgets函数可以读入一个完整的行，而read要指定字节数

```c
#include "apue.h"

int
main(void)
{
   int       c;

   while ((c = getc(stdin)) != EOF)
      if (putc(c, stdout) == EOF)
         err_sys("output error");

   if (ferror(stdin))
      err_sys("input error");

   exit(0);
}
```

stdin和stdout也在头文件<stdio.h>中定义，分别代表标准输入和标准输出。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207151750815.png" alt="image-20220715175012785" style="zoom:50%;" />

> 文件结束符通常是ctrl-D

## 1.6 程序和进程

- 打印进程ID

getpid()函数可以得到进程ID，数据类型为pid_t，标准会保证它可以存储在长整型当中。

```c
#include "apue.h"

int
main(void)
{
   printf("hello world from process ID %ld\n", (long)getpid());
   exit(0);
}
```

- 进程控制
  - fgets
    - fgets从标准输入一次读取一行，当键入文件结束符作为行的第一个字符时，fgets返回null指针，进程终止
    - fgets返回的每一行都以换行符结尾，后接一个null字节。需要将换行符替换为null，因为execlp要求的参数是以null结束的
  - fork
    - 用fork可以创建一个新进程，fork对于父进程返回子进程的pid，而对于子进程则返回0；
    - 因为fork创建一个新进程，所以它被调用一次（在父进程），但返回了两次（在父进程和子进程）
  - execlp
    - execlp用以执行从标准输入读入的命令，实际上是用新的程序文件代替了子进程原先执行的程序文件
    - fork和exec两者的组合就是产生(spawn)一个新进程
    - 子进程调用execlp执行新程序文件，而父进程希望等待子进程终止，这就是通过调用waitpid实现的。
  - waitpid
    - 需要指定要等待进程的pid，返回子进程的终止状态(status变量)

```c
#include "apue.h"
#include <sys/wait.h>

int
main(void)
{
   char   buf[MAXLINE];  /* from apue.h */
   pid_t  pid;
   int       status;

   printf("%% "); /* print prompt (printf requires %% to print %) */
   while (fgets(buf, MAXLINE, stdin) != NULL) {
      if (buf[strlen(buf) - 1] == '\n')
         buf[strlen(buf) - 1] = 0; /* replace newline with null */

      if ((pid = fork()) < 0) {
         err_sys("fork error");
      } else if (pid == 0) {    /* child */
         execlp(buf, buf, (char *)0);
         err_ret("couldn't execute: %s", buf);
         exit(127);
      }

      /* parent */
      if ((pid = waitpid(pid, &status, 0)) < 0)
         err_sys("waitpid error");
      printf("%% ");
   }
   exit(0);
}
```

该命令的主要缺陷是不能向所执行的命令传递参数，如下图，需要进一步通过某种约定对行进行解析。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181653086.png" alt="image-20220718165339040" style="zoom:50%;" />

- 线程与线程ID
  - 使用线程的好处
    - 有多个控制线程分别作用于问题的不同部分，解决起来就会容易得多
    - 多个线程可以充分利用多处理器系统的并行能力
  - 一个进程内的所有线程共享**同一地址空间、文件描述符、栈以及进程相关的属性**，所以访问共享数据时需要采取同步措施以避免不一致性
  - 线程也通过ID标识，但它只对它所属的进程有作用

## 1.7 出错处理

- 系统函数出错时通常会返回一个负值
- Linux出错常量在errno中定义
- C标准定义了两个函数，用于打印出错消息
  - strerror：将errno映射为一个出错消息字符串
  - perror：基于errno在标准错误上产生一条出错消息
- 出错恢复：
  - 致命性错误
  - 非致命性错误
    - 延迟一段时间，重试等方法

## 1.8 用户标识

- 用户ID：
  - 用户ID是系统用来标识不同用户的，每个用户有唯一的ID，内核通过使用用户ID来检验该用户是否有执行某些操作的权限
  - ID为0的用户为超级用户(root)
- 组ID：
  - 被用于将若干用户集合到项目或部门中去，该机制允许同组的各个成员之间共享资源
  - 组文件将组名映射为组ID，通常是/etc/group
- 存储用户ID和组ID仅需4字节

- 打印用户id和组id
  - getuid()：用户id
  - getgid()：组id

```c
#include "apue.h"

int
main(void)
{
   printf("uid = %d, gid = %d\n", getuid(), getgid());
   exit(0);
}
```

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181733182.png" alt="image-20220718173304157" style="zoom: 67%;" />

## 1.9 信号

> 用户通知进程发生了某种状况，如若发生了除0错误，会将名为SIGFPE的信号发送给该进程。

- 进程有以下处理信号的方式
  - 忽略信号
  - 按系统默认方式处理
  - 提供一个函数，信号发生时调用该函数
- 终端键盘上产生信号的两种方法：
  - 中断键：ctrl-c
  - 退出键：ctrl-\

