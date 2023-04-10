---
title: xv6笔记：chapt1 Operating system interfaces
tags: 
- MIT6.S081
- OS
- xv6
categories:
- Books
- Operating System
- xv6
---

# xv6笔记：chapt1 Operating system interfaces

## xv6 - the operating system

xv6 takes the traditional form of a kernel, **a special program that provides services to running programs**. 

Each process has memory containing **instructions, data, and a stack**. the stack organizes the program's procedure calls.

When a process needs to invoke a kernel service, it invokes a system call, one of the calls in the operating system’s interface. The system call enters the kernel; the kernel performs the service and returns. Thus a process **alternates** between executing in **user space and kernel space**.  

The kernel uses the **hardware protection mechanisms** provided by a CPU: each process in the user space **can access only its own memory**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230404125528893.png" alt="image-20230404125528893" style="zoom:50%;" />

When a user program invokes **a system call**, the hardware raises the **privilege level** and starts executing a pre-arranged function in the kernel.  

xv6 provides a subset of system calls that Unix kernel traditionally offer:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230404125856294.png" alt="image-20230404125856294" style="zoom:50%;" />

## xv6’s services  

### shell - no mystery about it

- shell is a user program, not part of the kernel. 
- shell illustrates the power of system call interface
- xv6's shell is a simple implementation of the essence of Unix Bourne shell. 
- It's implementation can be found at(user/sh.c)

### Processes and memory

#### create the process

`int pid = fork();`

```c
int
fork1(void)
{
  int pid;

  pid = fork();
  if(pid == -1) // <0 error arise
    panic("fork");
  return pid;
}
```

#### exit the process

`exit(0);`

- **exit system call** let the calling process stop executing and to release resources (memory and open files)  

#### wait the process

`wait(0);// doesn't care about the exit status of the a child , pass 0`

- **wait system call** return PID of an exited (or killed) child  of current process, and copies the exit status of the child to the address passed to wait  
- If the caller has no children, wait immediately returns -1.  

#### exec new process

```c
case EXEC:
    ecmd = (struct execcmd*)cmd;
    if(ecmd->argv[0] == 0)
      exit(1);
    exec(ecmd->argv[0], ecmd->argv);
    fprintf(2, "exec %s failed\n", ecmd->argv[0]);
    break;
```

- **The exec system** call **replaces** the calling process’s memory with a new memory image loaded from a file stored in the file system.  

- When exec succeeds, it **does not return** to the calling program  

- **Exec** takes two arguments: the **name of the file** containing the executable and an array of **string arguments**.  

### I/O and File descriptors

#### File descriptor

> What is file descriptor?

- A small integer representing a **kernel managed object** that a process may read from or write to.

> How can we obtain file descriptor?

- By opening a **file, directory, device**
- By creating a pipe
- By **duplicating** an existing descriptor

> What's the benefit of the file descriptor interface?

- **Abstracts away** the difference between **files, pipes, devices**
- Making them all look like **streams of bytes**

#### File descriptors and process

```c
// Ensure that three file descriptors are open.
  while((fd = open("console", O_RDWR)) >= 0){
    if(fd >= 3){
      close(fd);
      break;
    }
  }
```

- shell ensures that it always has **three** file descriptors open   

#### Read and Write to Files

example : the essence of `cat`: copies data from its standard input to its standard output  

```c
char buf[512];
int n;
for(;;){
	n = read(0,buf,sizeof buf);
	if(n==0)
		break;
    if(n<0){
        fprintf(2,"read error\n");
        exit(1);
    }
    if(write(1,buf,n)!=n){
        fprintf(2,"write error\n");
        exit(1);
    }
}
```

**Abstraction: **

- Cat doesn’t know whether it is reading from a file, console, or a pipe.  
- Similarly cat doesn’t know whether it is printing to a console, a file, or whatever.  

#### Close the file

- releases a file descriptor  
- making it free for reuse by a future `open`, `pipe`, or `dup `system call (see below)  

- A newly allocated file descriptor is always the **lowest-numbered** unused descriptor of the current process

#### IO redirection

```c++
char *argv[2];
argv[0] = "cat";
argv[1] = 0;
if(fork() == 0) {
    close(0);
    open("input.txt", O_RDONLY);
    exec("cat", argv);
}
```

- fork copies file descriptor table, but share underlying file offset

```c++
if(fork() == 0) {
    write(1, "hello ", 6);
    exit(0);
} else {
    wait(0);
    write(1, "world\n", 6);
}
```

- dup duplicates the existing file descriptor, returning a new one refers to the same underlying object

```c
fd = dup(1);
write(1, "hello ", 6);
write(fd, "world\n", 6);
```

> what is the meaning of 2>&1?

- Tells the shell to give command a file descriptor 2 that is a duplicate of descriptor 1.

- The xv6 shell doesn’t support I/O redirection for the error file descriptor, but now you know how to implement it.  

### Pipes

> what is pipe?

- A pipe is a small kernel buffer exposed to process as a pair of file descriptors, one for reading and one for writing
- Writing data to one end of the pipe makes that data available for reading from the other end of the pipe.   
- provide a way for processes to communicate

an example:

```c
int p[2];
char *argv[2];
argv[0] = "wc";
argv[1] = 0;
pipe(p);
if(fork() == 0) {
    close(0);
    dup(p[0]);
    close(p[0]);
    close(p[1]);
	exec("/bin/wc", argv);
} else {
    close(p[0]);
    write(p[1], "hello world\n", 12);
    close(p[1]);
}
```

- if no data is available, a read on a pipe waits for either data to be written or for **all file descriptors to be closed**.
- It's  important for the child to **close the write end of the pipe** before executing `wc` above.

- if one of wc ’s file descriptors referred to the write end of the pipe, wc would **never see end-of-file**  

> How does xv6 implement `grep fork sh.c | wc -l `? 

```c
case PIPE:
    pcmd = (struct pipecmd*)cmd;
    if(pipe(p) < 0)
      panic("pipe");
    if(fork1() == 0){
      close(1);
      dup(p[1]);
      close(p[0]);
      close(p[1]);
      runcmd(pcmd->left);
    }
    if(fork1() == 0){
      close(0);
      dup(p[0]);
      close(p[0]);
      close(p[1]);
      runcmd(pcmd->right);
    }
    close(p[0]);
    close(p[1]);
	//wait for both to finish
    wait(0);
    wait(0);
    break;
```

- may create a tree of processes, The leaves of the tree are commands and the interior nodes are processes that wait until the left child and right child complete.

- use pipes is much more efficient over temporary files:

  `echo hello world >/tmp/xyz; wc </tmp/xyz `

### File System

- The following two code segments open the same file:

  ```c++
  chdir("/a");
  chdir("b");
  open("c", O_RDONLY);
  
  open("/a/b/c", O_RDONLY);
  ```

#### create new files and directories

- `mkdir` creates a new directory
- `open` with the O_CREATE flag creates a new data file
- `mknod` creates a new device file.  

```c++
mkdir("/dir");
fd = open("/dir/file", O_CREATE|O_WRONLY);
close(fd);
mknod("/console", 1, 1);
```

- file itself is code a inode, it has many names called links. 
