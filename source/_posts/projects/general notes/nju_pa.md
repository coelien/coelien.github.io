---
title:  PA1项目心得记录
tags: 
- PA
- ics2022
categories:
- ics2022
- pa作业
---

#  PA1 notes

## Some preparations

> How to get all things done remotely?

Because I have my linux server in my bedroom, which is far away for me to run graphic UI applications in my work place. (Obviously, commands through shell are fine).

So I STFW and found that mobaXterm actually support x11 forwarding, which can show the GNOME desktop. I made some configuration to realize it:

- change the remote environment to **Gnome desktop**:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303241703801.png" alt="image-20230324170347757" style="zoom: 50%;" />

- Edit **/etc/ssh/ssh_config** to have the following settings

  - ForwardAgent yes

  - ForwardX11 yes

  - ForwardX11Trusted yes

- Edit **/etc/ssh/sshd_config** to have the following setting
  - X11Forwarding yes

- set xdmcp on gdm3
  - vim /etc/gdm3/custom.conf

![img](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303241705304.png)

- apt install gnome-tweaks

- reboot. (all things done!)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303241722650.png" alt="image-20230324172253275" style="zoom:50%;" />

> multi thread compiling v.s. single thread compiling

single thread:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303241725857.png" alt="image-20230324172506827" style="zoom:50%;" />

16 threads:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303241726633.png" alt="image-20230324172657610" style="zoom:50%;" />

> if we don't change any file, object files should be the same, so there is no need compiling again!

By using ccache the compiler time is longer, which is confusing

<img src="C:/Users/sixwa/AppData/Roaming/Typora/typora-user-images/image-20230327203333113.png" alt="image-20230327203333113" style="zoom: 67%;" />

<img src="C:/Users/sixwa/AppData/Roaming/Typora/typora-user-images/image-20230327203613713.png" alt="image-20230327203613713" style="zoom: 67%;" />

> How does can Super Mario runs on the linux machine?

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303301937919.png" alt="image-20230330193733878" style="zoom:50%;" />

- for super mario, it can't figure out it runs on the real Red White devices or runs on the simulated "Red White devices"

### NEMU

> What is NEMU?

- NEMU is a simulated computer system. Physical computer's essential functions is implemented by programs in NEMU.

- 在NEMU中, 每一个硬件部件都由一个程序相关的数据对象来模拟, 例如变量, 数组, 结构体等;
- 对这些硬件部件的操作则通过对相应数据对象的操作来模拟

### ISA

> Firstly... you need to choose ISA

ISA is **instruction set architecture**, also called computer architecture, is an **abstraction model** of computer

A device that execute instructions described by that ISA such as a CPU, is called an **implementation**.

ISA defines supported **instructions**, **data types**, **registers,** the hardware supporting for managing main memory, **fundamental features**(such as memory consistency, addressing modes, virtual memory), and **the input/output model**

因此, ISA的本质就是类似这样的规范. 所以ISA的存在形式既不是硬件电路, 也不是软件代码, 而是一本**规范手册**.

和螺钉螺母的生产过程类似, 计算机硬件是按照ISA规范手册构造出来的, 而程序也是按照ISA规范手册编写(或生成)出来的

### Goals

至于ISA规范里面都有哪些内容, 我们应该如何**构造一个符合规范的计算机**, **程序应该如何遵守这些规范**来在计算机上运行 is our goal.

## Chapter of Creating the World

- storage(存储器): we need some place to store our program

- CPU: we need someone to calculate(compute) CPU是负责处理数据的核心电路单元

   同时**天下也没有免费的午餐**, 存储器的**大容量**也是需要**付出相应的代价的, 那就是速度慢**, 这是先驱也无法违背的材料特性规律

  - 如果每完成一次累加都需要把它写回存储器, 然后又把它从存储器中读出来继续加, 这样就太不方便了.先驱为CPU创造了**寄存器**, 可以让CPU把正在处理中的数据暂时存放在其中.

> 如果没有寄存器, 计算机还可以工作吗?

- to let the CPU become the **most loyal servant**, pioneer designs "**instructions**", to tell CPU to do what kind of operation to data

  > 能否让程序来**自动控制**计算机的执行?

- 先驱和CPU作了一个简单的约定: 当执行完一条指令之后, 就**继续执行**下一条指令. 

- 但CPU怎么知道现在执行到哪一条指令呢? 为此, 先驱为CPU创造了一个**特殊的计数器, 叫"程序计数器"**

从此以后, 计算机就只需要做一件事情:

```c
while (1) {
  从PC指示的存储器位置取出指令;
  执行指令;
  更新PC;
}
```

一个**足够简单的计算机**:  我们只要将一段指令序列放置在存储器中, 然后让PC指向第一条指令, 计算机就会自动执行这一段指令序列, 永不停止

一个最简单的真实计算机需要满足哪些条件:

- **结构上**, TRM有存储器, 有PC, 有寄存器, 有加法器
- **工作方式上**, TRM不断地重复以下过程: 从PC指示的存储器位置取出指令, 执行指令, 然后更新PC

> 计算机是个状态机

既然计算机是一个**数组逻辑电路,** 那么我们可以把计算机划分成两部分, 一部分由所有**时序逻辑部件**(存储器, 计数器, 寄存器)构成, 另一部分则是剩余的**组合逻辑部件(**如加法器等). 这样以后, 我们就可以从状态机模型的视角来理解计算机的工作过程了: 在每个时钟周期到来的时候, 计算机**根据当前时序逻辑部件的状态**, 在组合逻辑部件的作用下, **计算出并转移到下一时钟周期的新状态**.

> 程序是一个状态机

程序是指令的集合。那么**指令在状态机的模型里面是什么呢**？

所以在状态机模型里面, 指令可以看成是**计算机进行一次状态转移的输入激励**.

假设某个计算机有4个8位的寄存器, 一个4位PC, 以及一段16字节的内存(也就是存储器)

那么这个计算机可以表示比特总数为：

```c
4*8 + 4 + 16*8 = 164
```

因此这个计算机总共可以有$N = 2^B = 2^{164}$种不同的状态

假设这个在这个计算机中, 所有指令的行为都是确定的, 那么给定`N`个状态中的任意一个, 其转移之后的新状态也是唯一确定的.

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303302042331.png" style="zoom:50%;" />

> 通过状态机的视角来解释**"程序在计算机上运行"的本质**

给定一个程序, 把它放到计算机的内存中, 就相当于在状态数量为`N`的状态转移图中指定了一个初始状态, 程序运行的过程就是从这个初始状态开始, 每执行完一条指令, 就会进行一次确定的状态转移. 也就是说, 程序也可以看成一个状态机! 这个状态机是上文提到的**大状态机(状态数量为`N`)的子集**.

例如：1+2+...+100

```c
// PC: instruction    | // label: statement
0: mov  r1, 0         |  pc0: r1 = 0;
1: mov  r2, 0         |  pc1: r2 = 0;
2: addi r2, r2, 1     |  pc2: r2 = r2 + 1;
3: add  r1, r1, r2    |  pc3: r1 = r1 + r2;
4: blt  r2, 100, 2    |  pc4: if (r2 < 100) goto pc2;   // branch if less than
5: jmp 5              |  pc5: goto pc5;
```

的状态机转换为：

(0,x,x) -> (1,0,x)->(2,0,0)->(3,0,1)->(4,1,1)->(2,1,1)->(3,1,2)->(4,3,2)->....->(2,4851,98)->(3,4851,99)->(4,4950,99)->(2,4950,99)->(3,4950,100)->(4,5050,100)->(5,5050,100)->....

- 以代码(或指令序列)为表现形式的**静态视角**
  - 描述精简
  - 分支, 循环和函数调用的组合使得我们可以通过少量代码实现出很复杂的功能. 
  - 但这也可能会使得我们对程序行为的理解造成困难
- 以状态机的状态转移为运行效果的**动态视角**
  - 直接刻画了"程序在计算机上运行"的本质
  - 但对于程序的局部行为, 尤其是从静态视角来看难以理解的行为, 状态机视角可以让我们清楚地了解相应的细节.

 因为在PA中你需要不断地和代码打交道. 如果你不能从**微观视角理解某些关键代码的行为**, 你也无法从**宏观视角完全弄清楚程序究竟是如何运行的**.

## RTFSC

在NEMU中模拟的计算机称为"**客户(guest)计算机**", 在NEMU中运行的程序称为"**客户程序**".

### 框架代码初探

```c
ics2022
├── abstract-machine   # 抽象计算机
├── am-kernels         # 基于抽象计算机开发的应用程序
├── fceux-am           # 红白机模拟器
├── init.sh            # 初始化脚本
├── Makefile           # 用于工程打包提交
├── nemu               # NEMU
└── README.md
```

#### NEMU的四个主要模块

- monitor
- CPU
- memory
- device

**Monitor模块**

> 监控客户计算机的运行状态而引入的

- 负责与GNU/Linux进行交互（读入客户程序）
- 有调试器的功能

如果没有monitor，对NEMU的调试将极为困难

#### NEMU源文件组织

```
nemu
├── configs                    # 预先提供的一些配置文件
├── include                    # 存放全局使用的头文件
│   ├── common.h               # 公用的头文件
│   ├── config                 # 配置系统生成的头文件, 用于维护配置选项更新的时间戳
│   ├── cpu
│   │   ├── cpu.h
│   │   ├── decode.h           # 译码相关
│   │   ├── difftest.h
│   │   └── ifetch.h           # 取指相关
│   ├── debug.h                # 一些方便调试用的宏
│   ├── device                 # 设备相关
│   ├── difftest-def.h
│   ├── generated
│   │   └── autoconf.h         # 配置系统生成的头文件, 用于根据配置信息定义相关的宏
│   ├── isa.h                  # ISA相关
│   ├── macro.h                # 一些方便的宏定义
│   ├── memory                 # 访问内存相关
│   └── utils.h
├── Kconfig                    # 配置信息管理的规则
├── Makefile                   # Makefile构建脚本
├── README.md
├── resource                   # 一些辅助资源
├── scripts                    # Makefile构建脚本
│   ├── build.mk
│   ├── config.mk
│   ├── git.mk                 # git版本控制相关
│   └── native.mk
├── src                        # 源文件
│   ├── cpu
│   │   └── cpu-exec.c         # 指令执行的主循环
│   ├── device                 # 设备相关
│   ├── engine
│   │   └── interpreter        # 解释器的实现
│   ├── filelist.mk
│   ├── isa                    # ISA相关的实现
│   │   ├── mips32
│   │   ├── riscv32
│   │   ├── riscv64
│   │   └── x86
│   ├── memory                 # 内存访问的实现
│   ├── monitor
│   │   ├── monitor.c
│   │   └── sdb                # 简易调试器
│   │       ├── expr.c         # 表达式求值的实现
│   │       ├── sdb.c          # 简易调试器的命令处理
│   │       └── watchpoint.c   # 监视点的实现
│   ├── nemu-main.c            # 你知道的...
│   └── utils                  # 一些公共的功能
│       ├── log.c              # 日志文件相关
│       ├── rand.c
│       ├── state.c
│       └── timer.c
└── tools                      # 一些工具
    ├── fixdep                 # 依赖修复, 配合配置系统进行使用
    ├── gen-expr
    ├── kconfig                # 配置系统
    ├── kvm-diff
    ├── qemu-diff
    └── spike-diff
```

- 为了支持不同的ISA, 框架代码把NEMU分成两部分: ISA无关的基本框架和ISA相关的具体实现。NEMU把ISA相关的代码专门放在`nemu/src/isa/`目录下, 并通过`nemu/include/isa.h`提供ISA相关API的声明. 这样以后, `nemu/src/isa/`**之外**的其它代码就展示了**NEMU的基本框架**. 
- 体现**抽象的思想**: 框架代码将ISA之间的差异**抽象成API**, 基本框架会调用这些API, 从而无需关心ISA的具体细节. 如果你将来打算选择一个不同的ISA来进行二周目的攻略, 你就能明显体会到抽象的好处了: **基本框架的代码完全不用修改**!

- NEMU ISA相关说明文档：[这里](https://nju-projectn.github.io/ics-pa-gitbook/ics2022/nemu-isa-api.html)

### 配置系统和项目构建

#### 配置系统kconfig

> 为什么要使用配置系统？

可配置选项的数量可能会非常多, 而且配置选项之间可能会存在关联。比如打开配置选项A之后, 配置选项B就必须是某个值. 直接让开发者去管理这些配置选项是很容易出错的, 比如修改选项A之后, 可能会忘记修改和选项A有关联的选项B. 配置系统的出现则是为了解决这个问题.

NEMU中的配置系统位于`nemu/tools/kconfig`

kconfig定义了一套简单的语言, 开发者可以使用这套语言来编写"配置描述文件".

 在"配置描述文件"中, 开发者可以描述:

- 配置选项的属性, 包括类型, 默认值等
- 不同配置选项之间的关系
- 配置选项的层次关系

当你键入`make menuconfig`的时候, 背后其实发生了如下事件:

- 检查`nemu/tools/kconfig/build/mconf`程序是否存在, 若不存在, 则编译并生成`mconf`

- 检查`nemu/tools/kconfig/build/conf`程序是否存在, 若不存在, 则编译并生成`conf`

- 运行命令`mconf nemu/Kconfig`, 此时`mconf`将会解析`nemu/Kconfig`中的描述, 以菜单树的形式展示各种配置选项, 供开发者进行选择
- 退出菜单时, `mconf`会把开发者选择的结果记录到`nemu/.config`文件中
- 运行命令`conf --syncconfig nemu/Kconfig`, 此时`conf`将会解析`nemu/Kconfig`中的描述, 并读取选择结果`nemu/.config`, 结合两者来生成如下文件:
  - 可以被包含到C代码中的宏定义(`nemu/include/generated/autoconf.h`)
  - 可以被包含到Makefile中的变量定义(`nemu/include/config/auto.conf`)
  - 可以被包含到Makefile中的, 和"配置描述文件"相关的依赖规则(`nemu/include/config/auto.conf.cmd`), 为了阅读代码, 我们可以不必关心它
  - 通过时间戳来维护配置选项变化的目录树`nemu/include/config/`, 它会配合另一个工具`nemu/tools/fixdep`来使用, 用于在更新配置选项后节省不必要的文件编译, 为了阅读代码, 我们可以不必关心它。

所以, 目前我们只需要关心配置系统生成的如下文件:

- `nemu/include/generated/autoconf.h`, 阅读C代码时使用
- `nemu/include/config/auto.conf`, 阅读Makefile时使用

#### 项目构建和Makefile

##### 与配置系统进行关联

通过包含`nemu/include/config/auto.conf`, 与kconfig生成的变量进行关联. 因此在通过menuconfig更新配置选项后, Makefile的行为可能也会有所变化.
