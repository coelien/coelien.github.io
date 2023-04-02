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

## Goals

- OS design and implementation
- Hands-on Exp.
- OS Purposes
  - ABSTRACTION of the hardware
  - MULTIPLEX the hardware
  - ISOLATION
  - SHARING
  - SECURITY(PERMISSION)
  - PERFORMANCE
  - RANGE of USES

## OS ORGANIZATION

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303310940238.png" alt="image-20230331094036040" style="zoom:50%;" />

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

