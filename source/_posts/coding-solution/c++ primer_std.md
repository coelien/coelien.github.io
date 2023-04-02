---
title: C++标准库
tags: 
- C++
- std
categories:
- Books
- C++ primer
- C++ 标准库
---

# C++标准库

## 动态内存

动态分配的对象的生存期与它们在哪里创建是无关的，只有当显式地被释放时，这些对象才会被销毁。

**静态内存**

用来保存局部地static对象、类static数据成员、以及定义在任何函数之外地变量。

**栈内存**

栈内存用来保存定义在函数内的非static对象

> **分配在静态内存或是栈内存中的对象由编译器自动创建或销毁**

**堆、自由空间**

程序用堆来存储**动态分配**的对象，即程序运行时分配的对象；**其生存期由程序来控制**，不用时，我们的代码必须显式销毁它们

### 动态内存与智能指针

> 使用新标准库的智能指针可以更好地管理动态内存，在memory头文件中

- shared_ptr：允许多个指针指向同一个对象
- unique_ptr：独占所指向的对象

#### shared_ptr类

**shared_ptr和unique_ptr都支持的操作**

- shared_ptr<T> sp 空智能指针
- p 用作条件判断，若指向一个对象则为true
- *p 解引用
- p->mem
- p.get()
- swap(p,q)

**仅shared_ptr支持的操作**

- make_shared<T> (args) 指向一个动态分配的T类型的对象，返回一个shared_ptr
- shared_ptr<T> p(q)  p是shared_ptr q的拷贝
- p = q 递减p的引用次数，递增q的引用次数
- p.unique() 若p.use_count()为1，返回true
- p.use_count() 返回与p共享对象的智能指针数量

**make_shared函数**

- **最安全**的分配和使用动态内存的方法

```c++
shared_ptr<int> p3 = make_shared<int>(42);
shared_ptr<string> p4 = make_shared<string>(10,'9');
auto p6 = make_shared<vector<string>>();
```

**shared_ptr的拷贝和赋值**

当进行拷贝和赋值时，每个shared_ptr都会记录有多少个其他shared_ptr指向相同的对象

**shared_ptr自动销毁所管理的对象**

- 使用析构函数

**shared_ptr会自动释放相关联的内存**

```c++
void use_factory(T arg){
    shared_ptr<Foo> p = factory(arg);
    //use p
}
```

当p被销毁时，p所指向的对象也会被销毁(如果没有其他指针指向它)
```c++
shared_ptr<Foo> use_factory(T arg){
    shared_ptr<Foo> p = factory(arg);
    return p;
}
```

在这种情况下，return向其调用者返回一个p的拷贝。引用计数器++，所以不会销毁p所指的对象

**使用动态生存期资源的类**

- 程序不知道自己需要使用多少对象
- 程序不知道所需对象的准确类型
- 程序需要在多个对象间 共享数据

> **容器类出于第一个原因使用动态内存；**

目前为止，我们使用的类中，分配的资源与对应对象的生存期一致，但某些类分配的资源具有与原对象相独立的生存期。例如，如果两个类共享底层的数据，当某个类被销毁时，我们不能单方面地销毁底层数据：

```c++
Blob<string> b1;
{
    Blob<string> b2 = {"a","an","the"};
    b1 = b2;
}
```

> **使用shared_ptr共享数据**

```c++
class StrBlob{
    private:
    	std::shared_ptr<std::vector<std::string>> data;
}
```

```c++
StrBlob::StrBlob(initializer_list<string> il):
				data(make_shared<vector<string>>(il)){}
```

#### 直接管理内存

**使用new动态分配和初始化对象**

在自由空间分配的内存是无名的, 因此new无法为其分配的对象命名, 而是返回一个指向该对象的指针:

`int * p  = new int // p指向一个动态分配的,未初始化的无名对象`

**默认初始化和值初始化**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303121148569.png" alt="image-20230312114827483" style="zoom:50%;" />

**括号包围的初始化器**

```c++
auto p1 = new auto(obj)// c++11标准
```

**用new分配const对象**

```c++
const string * pcs = new const string;
```

- const对象必须进行初始化, 定义了默认构造函数的类类型可以隐式初始化。

**内存耗尽**

```c++
#include<new>
int *p1 = new int; //分配失败， 抛出std::bad_alloc
int *p2 = new (nothrow) int; //分配失败，返回空指针
```

**调用者必须记得释放内存**

```c++
Foo* factory(T arg){
    return new Foo(arg);
}
```

### 动态数组

