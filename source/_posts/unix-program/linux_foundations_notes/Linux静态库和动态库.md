---
title: linux笔记：静态库和动态库浅析
tags: 
- 静态库和动态库
- linux
categories:
- linux
---

# linux静态库和动态库

## 静态库和动态库的工作原理

- 静态库：进行链接时，会把静态库中的代码打包到可执行文件中
- 动态库：进行链接时，不会把动态库中的代码打包到可执行文件中

它们的区别在于链接阶段如何处理: 静态链接方式和动态链接方式

## 静态库与动态库的优缺点

| 链接方式 | 优点                               | 缺点                                              |
| -------- | ---------------------------------- | ------------------------------------------------- |
| 静态库   | 加载速度快,无需提供静态库,移植方便 | 占多份内存,消耗系统资源,浪费内存,更新发布部署麻烦 |
| 动态库   | 进程间资源共享,更新,部署,发布简单  | 加载慢,需要提供动态库                             |



## 静态库的制作和使用

- 当前的目录结构如下：
  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206061732768.png" alt="image-20220606173223714" style="zoom:50%;" />

- 首先到源代码目录进行编译和汇编得到目标文件，但不进行连接：

`gcc -c *.c -I ../include/`

- 制作静态库libxxx.a:

`ar rcs libcalc.a *.o`并移到lib目录下

注意：需提供静态库.a文件以及头文件，才可以成功对main.c进行编译

-  编译测试文件main.c，需指定包含**头文件的路径、静态库的目录和静态库的名称**

`gcc main.c -o app -I ./include/ -L ./lib/ -l calc`

- 运行测试文件./app ，结果如图：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206061823645.png" alt="image-20220606182353621" style="zoom:50%;" />

## gcc编译选项总结

| gcc编译选项        | 说明                                               |
| ------------------ | -------------------------------------------------- |
| -E                 | 预处理指定的源文件，不进行编译                     |
| -S                 | 编译指定的源文件，但是不进行汇编                   |
| -c                 | 编译、汇编指定的源文件，但是不进行链接             |
| [file2] -o [file1] | 将文件 file2 编译成可执行文件 file1                |
| -I directory       | 指定 include 包含文件的搜索目录                    |
| -g                 | 在编译的时候，生成调试信息，该程序可以被调试器调试 |
| -D                 | 在程序编译的时候，指定一个宏                       |
| -Wall              | 生成所有警告信息                                   |
| -On                | n的取值范围：0~3。编译器的优化选项的4个级别        |
| -l                 | 在程序编译的时候，指定使用的库                     |
| -L                 | 指定编译的时候，搜索的库的路径                     |
| -fPIC/fpic         | 生成与位置无关的代码                               |
| -shared            | 生成共享目标文件，通常用在建立共享库时             |

## 动态库的制作和使用

- 命名：
  - Linux：libxxx.so
  - windows：libxxx.dll
- 动态库制作：
  - 得到和**位置无关**的代码 -fpic
  - 得到动态库：gcc -shared a.o b.o -o libcalc.so

-  编译测试文件main.c：

`gcc main.c -o app -I ./include/ -L ./lib/ -l calc`

- 运行测试文件./app ，结果如图：

## 动态库的搜索路径

定位共享库（动态库）文件时，需要知道其绝对路径，此时就需要系统的动态载入器来获取该路径，并将其载入内存，它是通过ld-linux.so来完成的。搜索顺序如下：

- 环境变量LD_LIBRARY_PATH

1. export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:动态库所在目录；(当前会话有效)
2. vim ~/.bashrc 把上面的代码拷贝到最后一行；（用户级别）
3. vim /etc/profile 同理（系统级别）

- /etc/ld.so.cache

需要修改/etc/ld.so.conf文件, 并运行ldconfig进行更新

- /lib
- /usr/lib

  第三种和第四种不建议，推荐前两种配置