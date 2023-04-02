---
title: C++基础
tags: 
- C++
categories:
- Books
- C++ primer
- C++ 基础
---
# c++基础

## 变量和基本类型

### 变量

**初始值**

初始化不是赋值：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302140943235.png" alt="image-20230214094335116" style="zoom:50%;" />

**列表初始化**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302140945334.png" alt="image-20230214094532303" style="zoom:50%;" />

- c++11新标准

**默认初始化**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302140951806.png" alt="image-20230214095109752" style="zoom:50%;" />

**分离式编译**

将程序分割为若干文件，每个文件可独立编译。

如果要在多个文件里使用同一个变量，就必须将声明和定义分离。此时，变量的定义必须出现且只能出现在一个文件中，而其他用到该变量的文件必须对其进行声明。

**作用域**

同一个名字在不同的作用域可以指向不同的实体

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141000805.png" alt="image-20230214100001756" style="zoom:50%;" />

### 复合类型

基于其他类型定义的类型（引用，指针等）

**引用**

“引用”通常指的是左值引用，右值引用是c++11新标准

定义引用时，是把引用和初始值对象bind在一起，而不是将初始值拷贝给引用。无法令引用重新绑定到另外一个对象，因此引用必须初始化。引用只能绑定在对象上，而不能与字面值绑定。

**指针**

指针本身就是一个对象，可以赋值&拷贝。且无需定义时赋值。

```*p = 0```可通过解引用对所指对象赋值

**空指针**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141027006.png" alt="image-20230214102744969" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141028075.png" alt="image-20230214102846034" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141029729.png" alt="image-20230214102929664" style="zoom:50%;" />

**void*指针**

可以存放任意对象的地址，但不能直接操作其所指对象。

**复合类型声明**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141036532.png" alt="image-20230214103604481" style="zoom:50%;" />

**指向指针的指针**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141037939.png" alt="image-20230214103716894" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141037768.png" alt="image-20230214103754721" style="zoom:50%;" />

**指向指针的引用**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141038714.png" alt="image-20230214103844673" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141040973.png" alt="image-20230214104041922" style="zoom:50%;" />

### const 限定符

**为什么要用const限定符？**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141042570.png" alt="image-20230214104256517" style="zoom:50%;" />

const对象必须进行初始化：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141043707.png" alt="image-20230214104327674" style="zoom:50%;" />

**编译器如何处理const？**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141045649.png" alt="image-20230214104541612" style="zoom:50%;" />

为了避免对同一变量的重复定义，默认情况下，const对象被设定为仅在文件内有效。

若想要共享const 变量：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141050428.png" alt="image-20230214105008379" style="zoom:50%;" />

**const的引用（常量引用）**

常量引用不能修改其所引用的常量

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141054588.png" alt="image-20230214105435558" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141055958.png" alt="image-20230214105515932" style="zoom:50%;" />

但对const的引用可能引用一个并非const的对象，允许通过其他手段改变它的值。

**const的引用初始化**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141057000.png" alt="image-20230214105711959" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141058187.png" alt="image-20230214105806146" style="zoom:50%;" />

**编译器是如果处理这种情况的？**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141059952.png" alt="image-20230214105941891" style="zoom:50%;" />

**指针和const**

- 指向常量的指针

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141104076.png" alt="image-20230214110444036" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141106500.png" alt="image-20230214110620450" style="zoom:50%;" />

- const 指针

不变的指针本身的值，而非所指向的那个值。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302141108143.png" alt="image-20230214110859103" style="zoom:50%;" />

**顶层const & 底层const**

顶层const：表示指针本身是一个常量

底层const：表示指针所指对象是一个常量

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302160929459.png" alt="image-20230216092934328" style="zoom:50%;" />

**常量表达式**

值不会改变且在编译过程中就能得到计算结果

例如：字面值，常量表达式初始化的const对象

**constexpr变量**

c++11新标准：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302160935408.png" alt="image-20230216093514355" style="zoom:50%;" />

类型有所限制，需要为字面值类型：

- 算术类型
- 引用
- 指针

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302160937123.png" alt="image-20230216093747081" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302160939110.png" alt="image-20230216093934066" style="zoom:50%;" />

### 处理类型

**类型别名**

传统方法使用typedef来定义类型别名

c++11新标准使用了别名声明来定义类型的别名：

`using SI=Sales_item;`

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302160946472.png" alt="image-20230216094646420" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302160946157.png" alt="image-20230216094621115" style="zoom:50%;" />

**auto类型说明符**

要知道表达式的类型有时并不容易，所有c++11引入了auto。一条声明语句只能有一个基本数据类型。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302160950827.png" alt="image-20230216095029779" style="zoom:50%;" />

**decltype类型指示符**

选择并返回操作数的数据类型

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302160959564.png" alt="image-20230216095916533" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161001578.png" alt="image-20230216100113527" style="zoom:50%;" />

而auto一般会忽略顶层const和引用。

decltype如果使用的是表达式，它返回表达式结果对应的类型：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161005956.png" alt="image-20230216100549920" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161007880.png" alt="image-20230216100713837" style="zoom:50%;" />

![image-20230216100302778](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161003839.png)

### 自定义数据结构

一般，自定义数据结构会保存在单独的.h文件中，但是这个会导致某个头文件被多次包含。预处理器可以确保头文件多次包含仍能安全工作。预处理器是在编译前执行的一段程序。

常见预处理功能：

- #include：用指定头文件的内容替代 #include

- 头文件保护符：需要用到预处理变量（**必须唯一**）

  - #define：将一个名字设定为预处理变量
  - #ifdef: 当且仅当变量已定义时为真
  - #ifndef: 当且仅当变量未定义时为真

  一旦检查为真，则执行后续操作直到遇到#endif为止。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161035228.png" alt="image-20230216103540178" style="zoom: 67%;" />

如果#ifndef检查结果为**假**，**编译器将忽略**#ifndef到#endif之间的部分。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161038915.png" alt="image-20230216103811852" style="zoom:50%;" />

## 字符串、向量和数组

**using声明**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161041445.png" alt="image-20230216104154399" style="zoom:50%;" />

### string

#### 定义和初始化

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161049553.png" alt="image-20230216104948488" style="zoom:50%;" />

拷贝初始化：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161050995.png" alt="image-20230216105050969" style="zoom:50%;" />

直接初始化：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161050519.png" alt="image-20230216105059491" style="zoom:50%;" />

#### string对象上的操作

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161053640.png" alt="image-20230216105306553" style="zoom:50%;" />

**读取未知数量的string对象**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161058425.png" alt="image-20230216105809383" style="zoom:50%;" />

**使用getline读取一整行**

- 注意getline不会存换行符到string里

**string::size_type类型**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161104405.png" alt="image-20230216110408376" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161107291.png" alt="image-20230216110740235" style="zoom:50%;" />

#### 处理string对象中的字符

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161109173.png" alt="image-20230216110909071" style="zoom: 50%;" />

**使用for range处理每个字符**（c++ 11）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161114270.png" alt="image-20230216111425244" style="zoom:50%;" />

如果想在for中改变字符值，需要将循环变量定义为引用类型

**如何只处理一部分字符？**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161119835.png" alt="image-20230216111913794" style="zoom:50%;" />

下标运算符的返回值是该位置字符的引用。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302161122996.png" alt="image-20230216112221945" style="zoom:50%;" />

SOME string

### vector

vector是一个类模板。可以把模板看作为编译器生成类或函数编写的一份说明。

**实例化：**编译器根据模板创建类或函数的过程

引用不是对象，所有不存在包含引用的vector

#### 定义和初始化

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302180935709.png" alt="image-20230218093530626" style="zoom:50%;" />

一般情况下：

- 如果用圆括号，提供的值是用来构造vector对象的

- 如果用花括号，表示我们想列表初始化该vector对象

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302180945381.png" alt="image-20230218094509308" style="zoom:50%;" />

如上例，只有v5使用了列表初始化

注：不能用下标去访问不存在的元素，否则会产生缓冲区溢出。

### 迭代器

一种通用的机制来访问容器的对象。所有标准库容器都可以使用迭代器。

#### 使用迭代器

- begin：返回指向第一个元素的迭代器
- end：返回指向容器“尾元素的下一位置”的迭代器（尾后迭代器，表示我们已经处理完了容器中的所有元素）

如果容器为空，则begin和end返回的是同一个迭代器。

**迭代器运算符**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181005718.png" alt="image-20230218100531646" style="zoom:50%;" />

c++程序员习惯地使用!=，因为这种编程风格在所有容器上均有效。所有标准库容器都定义了==和!=，而其中的大多数都没有定义<。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181014039.png" alt="image-20230218101424954" style="zoom:67%;" />

这种风格又被称为泛型编程。

如果对象只需读操作而无须写操作的的话，最好使用常量类型。c++11增加了cbegin()，得到const_iterator类型的返回值

**访问迭代器所指对象的成员**

`(*it).empty()`可以访问成员，注意（）必不可少。使用箭头运算符可以简化表达式，`it->empty()`

**一些限制**

不能在range for中向vector对象添加元素，否则会使该对象的迭代器失效。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181028445.png" alt="image-20230218102827400" style="zoom:50%;" />

#### 迭代器运算

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181031493.png" alt="image-20230218103105378" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181032776.png" alt="image-20230218103251746" style="zoom: 67%;" />

**二分使用迭代器运算**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181049323.png" alt="image-20230218104928261" style="zoom:50%;" />

### 内置数组

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181052977.png" alt="image-20230218105241936" style="zoom:50%;" />

#### 定义和初始化

**字符数组的特殊性**

我们可以使用字符串字面值对此类数组进行初始化，一定要注意字面值结尾还有一个空字符。

**不允许拷贝和赋值**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181057506.png" alt="image-20230218105716467" style="zoom:50%;" />

**理解复杂的数组声明**

因为数组本身是对象，所有允许定义数组的指针和数组的引用。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181105048.png" alt="image-20230218110517014" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181104959.png" alt="image-20230218110452905" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181108098.png" alt="image-20230218110843045" style="zoom:50%;" />

#### 指针和数组

使用数组的时候，编译器一般会把它转化为指针。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181111322.png" alt="image-20230218111152263" style="zoom:50%;" />

使用数组最为一个auto变量的初始值时，推断得到的类型为指针：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181112612.png" alt="image-20230218111251575" style="zoom:50%;" />

值得注意的是，当使用decltype关键字时, 不会进行转换，其返回的类型为10个整数构成的数组。

```c++
decltype(ia) ia3 = {0,1,2,3,4,5,6,7,8,9};
```

**指针也是迭代器**

指向数组元素的指针拥有更多的功能。

```c++
int arr[] = {0,1,2,3};
int * p  = arr;
++p; // p 指向 arr[1]
```

使用指针也能遍历数组中的元素，但要获取指向数组尾元素下一位置的指针：

```c++
int *e = &arr[10]; // 唯一用处：提供地址以初始化e
for(int *b = arr; b!=e;b++) cout <<*b<<endl;
```

尽管可以得到尾后指针，但是这种方法不是很安全，所有c++11引入了两个名为begin和end的函数，使用数组作为他们的参数：

```c++
int *beg = begin(arr);
int *last = end(arr);
```

**下标和指针**

对数组使用下标运输符时，其实是对数组元素的指针执行下标运算。

只要指针指向的是数组中的元素，都可以执行下标运算。

```c++
int *p= &ia[2];
int j = p[1];
int k = p[-2];
```

但需要保证结果地址必须指向原来指针所指同一数组的元素。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302181139680.png" alt="image-20230218113923620" style="zoom:50%;" />

#### c风格字符串

> ctring库

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302210903596.png" alt="image-20230221090254487" style="zoom:50%;" />

- 传入此类函数的指针必须指向以空字符作为结束的数组

- 使用strcpy充满安全风险，极易引发严重错误：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302210908107.png" alt="image-20230221090835067" style="zoom:50%;" />

#### 与旧代码的接口

- 若程序某处需要c风格字符串：`const char * str = s.c_str()`
- 允许**使用数组来初始化vector对象**：`vector<int> ivec(begin(int_arr),end(int_arr))`

### 多维数组

> 数组的数组

**初始化**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302210919438.png" alt="image-20230221091917398" style="zoom:50%;" />

内层嵌套的花括号并非必须，但是形式上更为简洁。

**多维数组的下标引用**

`int (&row)[4] = ia[1]; // 把row绑定到ia的第二个4元素数组上`

**使用范围for处理多维数组**

```c++
size_t cnt = 0;
// 因为要改变元素的值，所以要声明为引用类型
for( auto &row : ia)
    for( auto &col: row){
        col = cnt;
        ++cnt;
    }
// 第一层循环也必须声明为引用类型，为了防止自动转换为int*类型，导致第二层循环报错
```

**指针和多维数组**

多维数组名转换得到的指针实际上是指向第一个内层数组的指针

## 表达式

### 基础

**左值和右值**

> 左值可以位于赋值语句的左侧，而右值却不能（c中），c++会更复杂

当一个对象被用作右值时，用的是对象的值（内容）；当对象被用作左值时，用的对象的身份（在内存中的位置）。

- 需要右值的地方可以用左值来代替
- 反之不行，不能把右值当作左值使用

要用到左值的运算符：

- 赋值运算符：非常量左值作为左侧运算对象，得到的结果仍然是左值
- 取地址符：作用于一个左值运算对象，返回一个指向该运算对象的指针（该指针是一个右值）
- 解引用运算符，下标运算符
- 递增递减运算符

使用关键字decltype时，如果表达式结果时左值，得到引用类型。

### 算术运算符

- c++11，规定商一律向0取整
- 取模运算：
  - m%(-n) = m%n
  - (-m)%n = -(m%n)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302211014504.png" alt="image-20230221101456471" style="zoom:50%;" />

### 逻辑与关系运算符

**短路求值**

当且仅当左侧运算对象无法确定表达式结果时，才会去计算右侧运算对象的值。

### 赋值运算符

**满足右结合律**

```c++
int ival, jval;
ival = jval = 0;
```

多重赋值语句的每一个对象，其类型或者与右边对象的类型相同，或可由右边对象类型转换得到

```c++
string s1,s2;
s1 =s2 = "OK";
```

赋值语句通常会出现在条件中（优先级低）

```c++
int i;
while((i=get_value())!=42) //不断读取循环直到42为止
```

### 递增递减运算符

除非必须，否则不用递增递减运算符的后置版本。

前置版本将对象本身作为左值返回，而后置版本则将对象原始值的副本作为右值返回（需要将原始值存下来以便返回未修改的内容）

**混用解引用和递增运算符**

```c++
auto pbeg = v.begin();
while(pbeg != v.end && *pbeg >=0)
    cout<<*pbeg++<<endl;
```

++优先级较高，因此等价于*(pbeg++)。即把pbeg的值+1，然后返回pbeg初始值的副本作为其求职结果，此时解引用的运算对象是pbeg未增加之前的值。

因为简洁，上述写法优于：

```c++
cout<<*pbeg<<endl;
++pbeg;
```

### 位运算符

> 作用于整数类型的运算对象

- bitset可以表示任意大小的二进制位集合

- `^` 位异或
- `|` 位或

强烈建议将位运算符用于处理无符号类型

```c++
unsigned char bits = 0233;
~bits; // 先将char类型的对象提升为int类型，再按位取反
```

### sizeof运算符

返回一条表达式或一个类型名字所占的字节数，其所得值是size_t类型的常量表达式

- sizeof (type)
- sizeof expr

sizeof 并不会实际计算其运算对象的值：

```c++
Sales_data data, *p;
sizeof(Sales_data);
sizeof data;
sizeof p;
sizeof *p;
```

- c++11允许我们通过作用域运算符来获取类成员的大小：

```c++
sizeof Sales_data::revenue;
```

- 对数组执行sizeof得到整个数组所占空间的大小
- 对string或vector对象执行sizeof不会计算对象中的元素占用了多少空间

### 类型转换

#### 显示转换

**命名的强制类型转换：**

`cast-name<type>(expression);`

cast-name:

- static_cast

  只要不包含底层const，都可以用；

  需要将一个较大的算数类型赋值给较小的类型时；

  对于编译器无法自动执行的类型转换，如找回存在于void*指针：

  ```c++
  void* p = &d;
  double *dp = static_cast<double *>(p);
  ```

- dynamic_cast

- const_cast

  只能改变运算对象的底层const；

  ```c++
  const char *pc;
  char *p = const_cast<char*>(pc);// correct, but writing to p is undefined
  ```

  需要对象本身并不是一个常量；

  ```c++
  char *q = static_cast<char*>(pc);//wrong
  static_cast<string>(cp); //correct, 可以将字符串字面值转换成string类型;
  const_cast<string>(cp); // wrong
  ```

  const_cast常常用于函数重载中

- reinterpret_cast

  为运算对象的位模式提供较低层次上的重新解释

注：

尽量避免强制类型转换（充满风险）

## 语句

**悬垂else**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302231000860.png" alt="image-20230223100028778" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302231001608.png" alt="image-20230223100115547" style="zoom:50%;" />

**switch**

switch的case标签必须是整型常量表达式

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302231005686.png" alt="image-20230223100531646" style="zoom:50%;" />

**default标签**

如果没有任意一个case标签能匹配上switch表达式的值，就执行

### try语句块和异常处理

何为异常？

- 存在于运行时的反常行为

c++中的异常处理包括：

- throw表达式

  异常检测部分使用throw表达式表示它遇到了无法处理的问题。

- try语句块

  异常处理部分，以try关键字开始，并以一个或多个catch子句结束。try抛出异常，而catch进行处理

- 一套异常类

  用于在throw表达式和catch字句之间传递异常的具体信息

#### throw表达式

```c++
if(item1.isbn()!=item2.isbn())
    throw runtime_error("Data must refer to same ISBN");
```

#### try语句块

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302231039875.png" alt="image-20230223103946807" style="zoom:50%;" />

注：**编写异常安全的代码十分困难**

在异常发生期间，正确执行了“清理工作”的程序被称为异常安全的代码。必须时刻清楚，异常何时发生，如何确保对象有效、资源无泄漏、程序处于合理状态等。

#### 标准异常

异常类定义在了4个头文件中：

- exception头文件
- stdexcept头文件
  - exception
  - runtime_error
  - range_error
  - overflow_error
  - underflow_error
  - logic_error
  - domain_error
  - invalid_argument
  - length_error
  - out_of_range
- new头文件
- type_info头文件

## 函数

### 函数基础

#### 局部对象

- 名字的作用域

  是程序文本的一部分

- 对象的生命周期

  是程序执行过程中该对象存在的一段时间

**自动对象**

- 形参
- 普通局部变量

**局部静态对象**

- 在程序执行第一次经过对象定义语句时初始化，直到程序终止时才被销毁

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302231104524.png" alt="image-20230223110436478" style="zoom:50%;" />

#### 函数声明

- 变量（只能被定义一次的实体）在头文件中声明，在源文件中定义
- 函数也应在头文件中声明，在源文件中定义
- 定义函数的源文件应该把含有函数声明的头文件包含进来

### 参数传递

- 【引用传递】如果形参是引用类型：它将绑定到对应的实参上
- 【值传递】否则：将实参的值拷贝后赋给形参

注：在c++中，强烈建议用引用类型的形参代替指针

**使用引用可以避免拷贝**

有的类类型（包括IO类型）根本就不支持拷贝操作，函数只能通过引用形参访问该类型对象：

```c++
bool isShorter(const string &s1, const string &s2){
    return s1.size() < s2.size();
}// 当函数无需修改引用形参的值的时候最好使用常量引用
```

**使用引用形参令函数同时返回多个值**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302251002348.png" alt="image-20230225100224234" style="zoom:50%;" />

#### const形参和实参

- 用实参初始化形参时，会忽略掉（形参）顶层const。

- C++允许我们使用字面值初始化常量引用

- 引用尽量使用常量引用，使用普通引用会极大地限制函数所能接受的实参类型，从而导致错误：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302251017688.png" alt="image-20230225101715639" style="zoom:50%;" />

#### 数组形参

定义和使用作用在数组上的函数时，数组有两个特殊性质：

- 不允许拷贝数组
- 使用数组时会将其转化为指针

尽管不能以值传递的形式传递数组，但是我们可以把形参写成类似数组的形式：

- void print(const int *);
- void print(const int []);
- void print(const int [10]);

上面三种表示是等价的。

**管理数组实参**的方法：

- 使用标记指定数组长度：要求数组本身包含一个结束标记。典型示例：C风格字符串：

```c++
void print( const char *cp){
    if(cp) // 若cp不是空指针
        while(*cp) // 若cp所指字符不是空字符
            cout<<*cp++;
}
```

该方法时候那些有明显结束标记且该标记不会与普通数据混淆的情况。

- 使用标准库规范：传递指向数组首元素和尾后元素的指针：

```c++
void print(const int *beg, const int *end){
    while(beg!=end)
        cout<<*beg++<<endl;
}
int j[2] = {0,1};
print(begin(j),end(j))
```

- 显示传递一个表示数组大小的形参

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302251036560.png" alt="image-20230225103601509" style="zoom:50%;" />

**传递多维数组**

传递的本质上是一个指向数组的指针，维度大小不能省略：

```c++
void print(int (*matrix) [10], int rowSize){...}
```

上面的语句将matrix声明为指向包含10个整型元素的数组的指针。

等价定义：

```c++
void print(int matrix[][10], int rowSize){...}
```

**main：处理命令行选项**

```c++
int main(int argc, char *argv[]){...}
```

第二个形参argv是一个数组，它的元素是指向C风格字符串的指针。第一个形参表示数组中字符串的数量。

等价定义：

```c++
int main(int argc, char **argv){...}
```

其中argv指向char*。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302251052902.png" alt="image-20230225105204836" style="zoom:50%;" />

- 当使用argv中的实参时，从argv[1]开始

#### 含有可变形参的函数

为了能编写处理不同数量实参（数量未知）的函数，c++11提供了两种方法：

- 所有实参类型相同：传递一个名为`initializer_list；`的标准库类型
- 类型不同：可变参数模板

**initializer_list形参**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302251101263.png" alt="image-20230225110103177" style="zoom:50%;" />

其元素永远是常量值，无法改变。

```c++
void error_msg(initializer_lists<string> il){
    for(auto beg = il.begin();beg != il.end();++beg)
        cout<<*beg<<" ";
    cout<<endl;
}
```



<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302251107674.png" alt="image-20230225110715611" style="zoom:50%;" />

### 返回类型及return语句

**值是如何被返回的？**

- 如果返回string

```c++
string make_plural(size_t ctr, const string &word,
                  const string &ending){
    return (ctr>1)?word+ending :word;
}

```

意味着返回值将被拷贝到调用点，因此该函数返回一个未命名的临时string对象或是word的一个副本；

- 如果返回引用

```c++
const string &shorterString(const string &s1, const string &s2){
    return s1.size()<=s2.size()?s1:s2;
}
```

不管是调用函数还是返回结果，都不会真正拷贝string对象。

**不用返回局部对象的引用或是指针**

否则会引发未定义的行为：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302251125231.png" alt="image-20230225112526172" style="zoom:50%;" />

**引用返回左值**

函数的返回类型决定了函数调用是否是左值：调用一个返回引用的函数得到左值，其他得到右值。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302251130091.png" alt="image-20230225113055036" style="zoom:50%;" />

**列表初始化返回值** 

> 可以返回类类型，甚至内置类型

c++11规定，可以返回花括号包围的值的列表。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302251135795.png" alt="image-20230225113534738" style="zoom:50%;" />

列表初始化，your best helper！

**主函数main的返回值**

- 我们允许main函数没有return而直接结束，编译器会隐式地插入一条返回0的return语句
- cstdlib头文件定义了两个预处理变量：
  - EXIT_FAILURE
  - EXIT_SUCCESS

**返回数组指针**

数组不能被拷贝，所以函数不能返回数组。但是函数可以返回数组的指针或引用。

使用类型别名可以简化该过程：

```c++
typedef int arrT[10]; // arrT是一个类型别名，表示含有10个整数的数组
using arrT = int[10]; // 等价声明
arrT* func(int i); // 返回一个指向包含10个整型的数组的指针
```

注意第一个typedef表达的含义，推荐用using，比较直观

**声明一个返回数组指针的函数**

`Type (*function(parameter_list))[dimension]`

例：`int (*func(int i))[10];`

由此可以看出，使用类型别名是很方便的；

**使用尾置返回类型**

> 对复杂类型比较有效

c++11新标准有一种可以简化上述func声明的方法：尾置返回类型，任何函数的定义都能使用尾置返回：

`auto func(int i) -> int (*) [10]`

这种表达也比较清楚，值得推荐。

**使用decltype**

这个方法比较取巧，你得首先知道返回的指针将指向哪个数组：

```c++
int odd[]{1,3,5,7,9};
int even[]{0,2,4,6,8};
decltype(odd) *arrPtr(int i){
    return (i%2)? &odd: &even;
}
```

### 函数重载

> 函数名字相同但是形参列表不同我们称之为重载。

比如分别根据名字、电话、账户号码查找记录：

```c++
Record lookup(const Account&);
Record lookup(const Phone&);
Record lookup(const Name&);
```

编译器根据实参类型确定应该调用哪一个函数。

**重载和const**

有无const无法区分两个同名，同类型函数：

```c++
Record lookup(Phone);
Record lookup(const Phone);
```

上面两个声明是等价的。

但如果形参是指针或是引用，则通过区分其指向的是常量还是非常量对象可以实现重载：

```c++
Record lookup(Account&);
Record lookup(const Account&);

Record lookup(Account*);
Record lookup(const Account*);
```

上面的几个函数都能作用于非常量对象。但只有带const形参的才能接受常量对象。

**何时不应该重载函数？**

> 应服务于代码的可读性，使程序更容易理解

**const_cast和重载**

对于shorter string这一函数来讲，我们如果送入的是两个非常量引用，函数的返回值却是string常量引用，这显然是不符合逻辑的，可以使用const_cast实现重载：

```c++
const string &shorter(const string &s1, const string &s2){
    return s1.size()<=s2.size()?s1:s2;
}

string &shorter(string &s1, string &s2){
    auto &r = shorter(const_cast<const string&>(s1),
                      const_cast<const string&>(s2));
    return const_cast<string&>(r);
}
```

**重载与作用域**

通常情况下，在局部作用域声明函数不是一个很好的选择。因为外层作用域的同名实体会被隐藏。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302281020354.png" alt="image-20230228102057293" style="zoom:50%;" />

### 特殊用途语言特性

#### 默认实参

- 尽量让不怎么使用默认值的形参出现在前面，而让那些经常使用默认值的形参出现在后面。

- 对于函数声明，通常的习惯是将其放在头文件中并且一个函数只声明一次
- 局部变量不能作为默认实参，函数，全局变量等可以：

```c++
sz wd = 80;
char def = '';
sz ht();
string screen(sz = ht(), sz = wd, char = def);
```

#### 内联函数

将规模较小的操作定义成函数有诸多好处：

- 理解更容易
- 行为统一
- 修改容易
- 可重复使用

但也有缺点：

- 调用函数会慢一点（保存寄存器，返回时恢复，拷贝实参等）

**使用内联函数可避免函数调用的开销**

> 定义为内联函数(inline)

- 适合于规模较小
- 流程直接
- 频繁调用的函数

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302280952305.png" alt="image-20230228095236226" style="zoom:50%;" />

#### constexpr函数

> 指能用于常量表达式的函数

函数的返回类型及所有形参的类型都得是字面值类型。函数体必须有且只有一条return语句。

允许constexpr函数返回值并非一个常量：

```c++
constexpr size_t scale(size_t cnt){ return new_sz() * cnt;}

//需要传入常量表达式
```

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302281006444.png" alt="image-20230228100611379" style="zoom:50%;" />

注：内联函数和constexpr函数通常定义于头文件内

#### 调试帮助

**assert 预处理宏**

> assert(expr)

- 对expr求值，若表达式为假，终止程序运行
- 为真则什么都不做

- 常用于检查不能发生的条件

**NDEBUG 预处理变量**

可以使用`#define`语句定义NDEBUG，从而关闭调试状态

- `__func__`：当前调试的函数的名字
- `__FILE__`：当前文件名
- `__LINE__`：当前行号
- `__TIME__`：文件编译时间
- `__DATE__`：文件编译日期

### 实参类型转换

- 精确匹配
  - 类型相同
  - 从数组类型、函数类型转换成对应的指针类型
- 通过const转换实现的匹配
- 通过类型提升实现的匹配
- 通过算数类型转换、指针转换实现

### 函数指针

函数指针指向的是函数而非对象，指向某种特定的函数类型。

**函数类型**

由它的返回类型和形参类型共同决定，与函数名无关；

例如：

```c++
bool lengthCompare(const string&, const string&);
```

的类型为`bool(const string&, const string&)`

想要声明该函数的指针，只需指针替换函数名即可：

```c++
bool (*pf)(const string&, const string&);
```

**使用函数指针**

当我们把函数名作为一个值使用时，该函数自动地转换成指针。下面两个方式，将lengthCompare的地址赋给pf是等价的：

```c++
pf = lengthCompare;
pf = &lengthCompare;
```

此外，使用函数指针时，无需提前解引用指针。

在不同函数类型的指针间不存在转换规则，但是可以为函数指针赋0或是nullptr

**函数指针形参**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302281126736.png" alt="image-20230228112613675" style="zoom:50%;" />

直接使用函数指针过程冗长且烦琐。类型别名或decltype能让我们简化使用了函数指针的代码：

```c++
// Func and Func2是函数类型
typedef bool Func(const string&, const string&);
typedef decltype(lengthCompare) Func2;
// FuncP and FuncP2是指向函数的指针
typedef bool (*FuncP)(const string&, const string&);
typedef decltype(lengthCompare) *FuncP2;
```

可以使用上述简化useBigger的表达：

```c++
void useBigger(const string&, const string&, Func); // 自动将Func表示的函数转换为指针
void useBigger(const string&, const string&, FuncP2);
```

这两种声明是等价的。

**函数指针返回值**

与形参不同的是，返回值必须写成指针形式，编译器不会自动将函数返回类型当成对应的指针类型进行处理，想声明一个返回函数指针的函数，最简单的方法是使用类型别名：

```c++
using F = int(int*, int);//F是函数类型，不是指针
using PF= int(*)(int*, int);//PF是指针类型
```

声明返回值是函数指针的函数：

```c++
PF fl(int);
F *fl(int);
```

上面两个声明都是正确的。

当然也可以直接声明fl，但可读性较差：

```c++
int (*fl(int))(int*,int);
```

使用尾置返回类型不失一个可行解：

```c++
auto fl(int) -> int (*)(int*,int);
```

**将auto或是decltype用于函数指针类型**

```c++
decltype(sumLength) *getFcn(const string &);
```

- 当decltype作用于函数时，它返回的是函数类型，而非指针，所以需要显示地加上`*`。

## 类

- 类的基本思想是数据抽象和封装
- 数据抽象是一种依赖于接口与实现分离的编程技术
- 类的接口：用户所能执行的操作
- 类的实现：类的数据成员、负责接口实现的函数体、定义类的各种私有函数
- 封装实现了类的接口和实现的分离，因为封装后的类隐藏了它的实现细节，用户只能使用接口

### 定义抽象数据类型

#### 引入this指针

- 成员函数通过一个名为this的额外隐式参数来访问调用它的那个对象：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303020929271.png" alt="image-20230302092928150" style="zoom:50%;" />

#### 引入const成员函数

- 紧随参数列表之后的const关键字可以修改隐式this指针的类型：

```c++
std::string isbn() const{return bookNo;}
```

默认情况下this的类型是指向类类型非常量版本的常量指针。例如在Sales_data的成员函数中，this的类型为`Sales_data * const`。这意味着我们不能把this指针绑定到一个常量对象上。也就是说，我们不能在一个常量对象上调用普通的成员函数。所有我们应该把this指针声明成`const Sales_data * const`。在isbn函数体内部不会改变this所指对象，所以参数列表之后加上const关键字有助于提高灵活性。该函数称为常量成员函数。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303020942883.png" alt="image-20230302094203828" style="zoom:50%;" />

#### 类作用域和成员函数

- 值得注意的是，即使bookNo定义在isbn之后，isbn也还是能够使用bookNo。因为编译器分两步处理类：首先编译成员的声明，然后才轮到成员函数体（如果有的话）。

- 在类的外部定义成员函数的话，定义必须与声明匹配

#### 定义一个返回this对象的函数

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303020953105.png" alt="image-20230302095301038" style="zoom:50%;" />

#### 定义类的相关非成员函数

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303020954564.png" alt="image-20230302095441502" style="zoom:50%;" />

#### 构造函数

比较复杂，它没有返回类型。构造函数不能被声明为const的，因此，构造函数在const对象的构造过程中可以向其写值。

**合成的默认构造函数**

> 编译器创建的构造函数

**对于普通的类来讲，必须定义它自己的默认构造函数**

原因包含：

- 一旦我们定义了其他构造函数，那么除非我们再定义一个默认的构造函数，否则类将没有默认构造函数。
- 编译器创建的构造函数可能执行错误的操作：例如定义在块中的内置类型和复合类型的对象被默认初始化后，其值将是未定义的。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303021009907.png" alt="image-20230302100947843" style="zoom:50%;" />

- 有时候，编译器不能为某些类合成默认的构造函数：如果类中包含另一个其他类类型的成员，且这个成员的类型没有默认构造函数。那么编译器将无法初始化该成员。

**定义Sales_data的构造函数**

- 默认构造函数

`Sales_data() = default;`要求编译器生成构造函数

- 构造函数的初始化列表

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303021017113.png" alt="image-20230302101737065" style="zoom:50%;" />

- 在类外部定义构造函数

```c++
Sales_data::Sales_data(std::istream &is){
    read(is, *this);
}
```

#### 拷贝、赋值和析构

- 拷贝何时发生？初始化变量、以值传递的方式返回一个对象等
- 如果我们不主动定义拷贝、赋值和析构，编译器会为我们合成
- 但是某些类不能依赖于合成的版本：
  - 管理动态内存的类

### 访问控制与封装

- 在c++中，使用访问说明符来加强类的封装性
- public成员定义类的接口
- private部分封装了类的实现细节

- class和struct唯一的一点区别是默认访问权限不太一样

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303021038481.png" alt="image-20230302103814416" style="zoom:67%;" />

#### 友元

类可以允许其他类或者函数访问它的非公有成员，方法是令其他类或者函数成为它的友元。只需增加一条以friend关键字开头的函数声明即可

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303021052758.png" alt="image-20230302105250700" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303021052309.png" alt="image-20230302105241253" style="zoom:50%;" />

**封装的益处：**

- 确保用户代码不会无意间破坏封装对象的状态
- 被封装的类的具体实现细节可以随时改变而无需调整用户级别的代码

**友元的声明**

友元声明仅仅指定了访问的权限。如果我们希望类的用户能够调用某个友元函数，必须在友元声明之外再专门对函数进行一次声明。

为了使友元对类的用户可见，通常把友元的声明与类本身放置在同一个头文件中。

### 类的其他特性

#### 类型成员

> 其实就是自定义某种类型在类中的别名

```c++
class Screen{
public:
	typedef std::string::size_type pos;
    // 等价：using pos = std::string::size_type
private:
    pos cursor = 0;
    pos height = 0, width = 0;
    std::string contents;
};
```

- 类型成员必须先定义后使用
- 出现在类开始的地方

#### 类的成员的类内初始值
```c++
class Screen{
public:
	typedef std::string::size_type pos;
    // 等价：using pos = std::string::size_type
    Screen() = default; // 必须的默认构造函数
    Screen(pos, ht, wd, char c):height(ht),width(wd),
    contents(ht*wd,c){}
private:
    pos cursor = 0; // 类内初始值，所以构造函数不用显式初始化
    pos height = 0, width = 0;
    std::string contents;
};
```

```c++
class Window_mgr{
    private:
    std::vector<Screen> screens{Screen(24,80,' ')};
}
```

#### 可变数据成员

- 一个可变数据成员永远不会是const，即使它是const对象的成员
- 因此，一个const成员函数可以改变一个可变成员的值

```c++
class Screen{
    public:
    void some_member() const;
    private:
    mutable size_t access_ctr;
};

void Screen::some_member() const{
    ++access_ctr;
}
```

#### 内联成员函数

- 定义在类内部的成员函数是自动inline的
- 也可以显式使用inline来声明成员函数

#### 从成员函数返回*this

```c++
inline
Screen &Screen::move(pos r, pos c){
    pos row = r*witdh;
    cursor = row+c;
    return *this;    // 以左值的形式返回
}
inline
Screen &Screen::set(pos r, pos c, char ch){
    contents[r*width+c] = ch;
    return *this;    // 以左值的形式返回
}
inline
Screen &Screen::set(char ch){
    contents[cursor] = ch;
    return *this;    // 以左值的形式返回
}
```

- set重载了成员函数‘

- 函数返回的是对象本身而非副本

- 可以将一系列操作连接在一条表达式：

  `myScreen.move(4,0).set('#');`

- 一个const成员函数如果以引用的形式返回*this，那么它返回的类型将是常量引用。

因此可以使用重载的方式，让编译器选择函数：

```c++
Screen &display(std::ostream &os){
    do_display(os);return *this;
}
const Screen &display(std::ostream &os) const{
    do_display(os);return *this;
}
```

建议**对公共代码使用私有功能函数**：

```c++
private:
	void do_display(std::ostream &os) const{
        os<<contents;
    }
```

- 避免使用重复代码
- display可能变复杂
- 只在do_display一处添加调试信息要简单些
- 不会带来额外开销

#### 友元再探

**友元类**

```c++
class Screen{
    friend class Window_mgr;
};
class Window_mgr{
    public:
    using ScreenIndex = std::vector<Screen>::size_type;
    void clear(ScreenIndex);
    //后续省略
};

void Window_mgr::clear(ScreenIndex i){
    Screen &s = screens[i];
    s.contens = string(s.height*s.width,' ');
}
```

**友元成员函数**

```c++
class Screen{
    friend void Window_mgr::clear(ScreenIndex);
};
```

使用友元成员函数，限制比较多：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303071019041.png" alt="image-20230307101949930" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303071023282.png" alt="image-20230307102347202" style="zoom:50%;" />

- 友元声明**影响的是访问权限，本身并非普通意义上的声明**

### 类的作用域

**名字查找**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303071038221.png" alt="image-20230307103813155" style="zoom:50%;" />

### 构造函数再探

#### 构造函数初始值列表

**构造函数的初始值有时必不可少**

- const或引用
- 类类型且没有默认构造函数

正确方式：

```c++
class ConstRef{
public:
    constRef(int ii);
private:
    int i;
    const int ci;
    int &ri;
};
ConstRef::ConstRef(int ii):i(ii),ci(ii),ri(i){}
```

**成员初始化的顺序**

- 构造函数初始值列表只说明用于初始化成员的值，而**不限定初始化的具体执行顺序**

**默认参数和构造函数**

```c++
class Sales_data{
    public:
    Sales_data(std::string s=""): bookNo(s){}
}
```

- 该构造函数实际上为我们的类提供了默认构造函数

#### 委托构造函数

- c++11新标准
- 委托构造函数使用它所属类的其他构造函数执行它自己的初始化过程

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303071109894.png" alt="image-20230307110932815" style="zoom:50%;" />

#### 隐式类类型转换

如果构造函数只接受一个实参，它实际上定义了转换为此类类型的隐式转换机制，如：

```c++
string null_book = "9-999-99999-9";
item.combine(null_book);
```

编译器会用string构造一个临时的Sales_data对象，然后传给combine。

**类类型转换并不总有效**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303071126500.png" alt="image-20230307112614433" style="zoom:50%;" />

下面的显式转换也是可行的：

```c++
item.combine(static_cast<Sales_data>(cin));
```

标准库中：

- string构造函数，不是explicit的，接受单参数的(const char*)
- 接受一个容量参数的vector构造函数是explicit的。

#### 聚合类

聚合类：

- 所以成员都是public
- 没有任何构造函数
- 没有类内初始值
- 没有基类，也没有virtual函数

> 不推荐使用

#### 字面值常量类

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303090910996.png" alt="image-20230309091038850" style="zoom:50%;" />

### 类的静态成员

> 有时类会需要它的一些成员与类本身直接相关

- 一旦利率浮动,我们希望所有对象都能使用新值
- 效率上,没必要每个对象都存储利率

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303090916295.png" alt="image-20230309091640234" style="zoom:50%;" />

- 可以使用作用域运算符直接访问类的静态成员:

```c++
double r = Account::rate();
```

- 仍然可以通过类的对象,引用,指针来访问静态成员:

```c++
Account ac1;
Account *ac2 = &ac1;
r = ac1.rate();
r = ac2->rate();
```

- 一般来说,我们不能在类的内部初始化静态成员, 而应该在类的外部定义和初始化

- 为了确保对象只定义一次,最好的办法是把静态数据成员与其他函数的定义放在同一个文件中
- 类内初始化需要满足:

```c++
// 类内初始化
static constexpr int period = 30;
// 类外定义(好的实践)
constexpr int Account::period;
```

**静态成员能适用于某些场景,而不同成员不行**

- 静态数据成员可以说不完全类型

```c++
class Bar{
    
private:
    static Bar mem1;
    Bar *mem2;
    Bar mem3; // 有误,数据成员必须是完全类型
}
```

- 可以使用静态数据成员作为默认实参:

```c++
class Screen{
public:
    Screen& clear(char = bkground);
    
private:
    static const char bkground;
}
```

