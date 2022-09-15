---
title: linux笔记： Makefile基础
tags: 
- makefile
categories:
- linux
---

# Makefile基础

## 思想

- 成百上千个源文件，按照类型，功能，模块分别放到若干个目录中，Makefile文件定义了一系列的规则来指定哪些文件需要先编译，哪些文件需要后编译
- 自动化编译，一旦写好就可以整个工程完全自动编译

## 使用规则

- **规则**：

  - 目标 ...：依赖 ...

    tab	命令

    ​		...

  - 目标：最终要生成的文件

  - 依赖：生成目标所需要的文件或是目标

  - 命令：通过执行命令对依赖操作生成目标（需添加tab缩进）

- 可以有多个规则，一般来说其他规则都是**为了第一个规则服务的**

- 命令在执行之前，会先**检查依赖**：

  - 若依赖存在，直接执行命令；
  - 若不存在，看之后的规则能否生成当前规则所需的依赖，若能即正常执行；若不能则报错

- **检测更新**，在执行规则中的命令时，会比较目标和依赖文件的时间，**不做不必要的工作**
  - 若依赖的时间比目标的时间晚，则会重新生成目录；
  - 否则不需要更新，即对应规则中的命令不执行；

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207121604695.png" alt="image-20220712160431625" style="zoom:50%;" />

- 一般来说，写的越详细，效率越高（检测更新）：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207121607583.png" alt="image-20220712160746521" style="zoom:50%;" />

- 但是上面的写得特别繁琐，可以用变量改进
- 变量：
  - 自定义变量
  - 预定义变量
    - CC：C编译器的名称
    - $@：目标的完整名称
    - $<：所有的依赖文件名称
    - $^：第一个依赖文件名称
  - 获取变量值：$(变量名)

- 模式匹配：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207121620059.png" alt="image-20220712162046026" style="zoom:50%;" />

- 函数：

  - $(wildcard PATTERN...)

    功能：获取指定目录下指定类型的文件列表

    实例：`$(wildcard *.c ./sub/*.c)`

  - $(patsubst <pattern> , <replacement>,<text>)

    功能：进行字符串替换

    实例：`$(patsubstr %.c ,%.o, x.c bar.c)`

- clean规则：

  - make clean
  - 伪目标：.PHONY:clean 不真实地生成clean目标文件因此一定会执行
