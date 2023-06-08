---
title: MIT6.S081笔记：lecture 2 introduce to C
tags: 
- MIT6.S081
- OS
categories:
- courses
- Operating System
---

# MIT6.S081笔记：lecture 2 introduce to C

## Materials learning

### What is different about C? (vs. Python)  

- C是一个高级汇编语言
  - 其代码直接对应于机器指令
  - python dict相反，会使用很多隐藏于之下的code

- C是编译而不是解释的

  - 可以直接在解释器上执行，非常快

- C是静态类型的

  - python中，在变量中，类型与变量值相关联：

    每个值存于一个内存区域中，这个区域包含了这个值的类型相关信息

  - C中，类型与变量相关联，并解释为字节值：

    值并不包含任何类型信息，所有信息均存于变量

  - 类型错误在编译时被捕获

- C使用手动内存管理，没有垃圾收集机制

  - 显示的“malloc”和“free”，直接访问内存
  - 更快，也更容易出现错误

- C中的Intergers和floats有特定的但不明确的边界

  - 不同的类型在不同的平台有着不同的涵义（同为int可能机器不一样，所表示的范围也就不一样）<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230418141816129.png" alt="image-20230418141816129" style="zoom:50%;" />

    <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230418141956989.png" alt="image-20230418141956989" style="zoom:50%;" />

### 大端和小端

> 怎样在内存中表示：number like 0x12345678 (= 305419896) ？

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230418142542936.png" alt="image-20230418142542936" style="zoom:50%;" />

- RISC-V platform is little-endian  

### C中的内存类型

- 堆内存

  **动态**内存，需要显示分配（未初始化过的）

- 静态内存

  - 在任何函数之外定义的变量，以及通过**static声明**的变量
  - 只有**单个**的拷贝，被存储在了一个**预先定义且不会改变**的内存地址
  - 初始化为0

- 栈内存

  - 函数中的局部变量
  - 未初始化

### Key topic in C ：内存安全

- use after free
- double free
- uninitialize memory
- buffer overflow
- memory leak
- type confusion

### Key topic in C：指针

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230418144504866.png" alt="image-20230418144504866" style="zoom:50%;" />

- 指针本质上是int，它表示的是变量所在的内存地址

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230418144704947.png" alt="image-20230418144704947" style="zoom:50%;" />

- 不同类型的指针如上图
- 指针的大小
  - 与特定平台相关
  - RISC-V使用64bit指针（与long大小相同）
  - This means, in 6.S081, we can cast a pointer to a long, but not to an int.  

### C中的数组

- 固定大小

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230418145811951.png" alt="image-20230418145811951" style="zoom:50%;" />

- 在内存中连续存储：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230418145342202.png" alt="image-20230418145342202" style="zoom:50%;" />

### 定义 v.s. 声明

- C中的声明是必须的，C需要其类以及类型签名
  - If it hasn’t seen a declaration yet, it **won’t know the correct types** to use.  

- 定义在代码中只能出现一次
- 通常情况下，会把声明放在单独的头文件里

### 定义静态函数和变量

- 如果一个函数在两个文件中出现，他们会发生冲突。
- 为了避免冲突，可以使用static关键字

```c
static void function_2(int xyz);
static void function_2(int xyz) {
	printf(“%d\n”, xyz);
}
```

- 上面的函数只可以在当前定义的文件中使用
- 也可以在局部变量上声明static关键字：

```c
int add_cumulative_numbers(int increase) {
    static int total_sum = 0;
    total_sum += increase;
    return total_sum;
}
```

- 在调用的初次会被初始化为0，并且会保存其值

- 作为函数指针：

```c
static void my_function_1(int);
static void my_function_2(int);
void pointer_example(int variant) {
    void (*local)(int);
    if (variant == 1) {
    	local = my_function_1;
    } else {
    	local = my_function_2;
    }
    local(100); // call function via variable
}
```

### C中的字符串

- 尾字符为`'\0' = (0x00)`。注意，其值为0
- 计算字符串的长度：

```c
int strlen(const char *str) {
    int i;
    //可通过判定是否为终结字符来判断是否结束
    for (i = 0; str[i] != 0; i++) {}
    return i;
}
```

### 类型定义

- 类型别名：

```c
// from kernel/types.h:
typedef unsigned char uint8;
typedef unsigned short uint16;
typedef unsigned int uint32;
typedef unsigned long uint64;
```

- 方便使用+更加清楚
- 跨平台：If we ever port xv6 to another platform where the sizes don’t match up like this, we can **just change the one file that defines these typedefs**, and the rest of the code will update to match!  

### 头文件和源文件

- 头文件包含用来共享的声明
- 源文件包含不同代码的定义，如：

```c
// kernel/spinlock.h: declarations describing the spinlock interface
// kernel/spinlock.c: actual definitions of spinlock code
```

### C预处理器

- 常见预处理指令：

```c
#include “spinlock.h” // -> Incorporate the contents of spinlock.h here
#define NPROC 64 // -> Replace all instances of ‘NPROC’ with ‘64’
#define TWICE(x) ((x)*2)
//-> Replace all instance of ‘TWICE(x)’ with ‘((x) * 2)’ … for any expression ‘x
```

```c
#ifdef DEBUG // only if DEBUG was defined by #define
	printf(“some debug message: %d”, my_value);
#else
	// do something else instead of printing
#endif
```

```c
#ifndef VAR // if VAR wasn’t defined by #define
#if EXPR // if EXPR evaluates to true
```

### Include Guard

```c
// at the start of the something.h file
#ifndef SOMETHING_H
#define SOMETHING_H
// ... the normal contents go here ...
#endif /* SOMETHING_H */
```

### 常见内存函数

- malloc(n):  返回分配内存的首地址
- free(ptr)
- memset(ptr,v,n)：set every byte from ptr[0] to ptr[n-1] to v
- memmove(dst, src, n): copies src[0]...src[n-1] to dst[0]...dst[n-1]  
- memcpy(dst, src, n):  (Discouraged! Prefer memmove.)  

### 常见字符串操作函数

- strlen(str)
- strcmp(a,b)
- strcpy(dst, src)

**一定要记住NULL终结符**

### Unions

```c
union my_union {
    float x;
    int y;
}
```

- only safely use a single field of a union at a time  
- You won’t need these much.   

### C位操作

```c
unsigned int my_int;
// Set the Nth bit of an integer:
my_int |= 1 << N;
// Clear the Nth bit of an integer
my_int &= ~(1 << N);
// Check if any bits in MASK are set
if (my_int & MASK) { /* ... */ }
// Check if all bits in MASK are set
if ((my_int & MASK) == MASK) { /* ... */ }
// Check if integer is a power of two
if (my_int && !(my_int & (my_int - 1))) { /* ... */ }
```

### Finally: a pointer challenge  

```c
Source: The Ksplice Pointer Challenge - Oracle Linux Blog
int main() {
    int x[5]; // x is at 0x7fffdfbf7f00
    printf("%p\n", x); // -> 0x7fffdfbf7f00
    printf("%p\n", x+1); // -> 0x7fffdfbf7f04
    printf("%p\n", &x); // -> 0x7fffdfbf7f00
    printf("%p\n", &x+1); // -> 0x7fffdfbf7f14
    return 0;
}
```

## Exercise testing

