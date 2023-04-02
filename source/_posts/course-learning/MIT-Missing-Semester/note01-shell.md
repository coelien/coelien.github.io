---
title: MIT课程笔记： Notes on Missing semester 01
tags: 
- shell
- tool
categories:
- 课程学习
- shell
---

# Notes on Missing semester

## lesson 1

### environment variables

- PATH

shell搜索程序时，会去寻找PATH列表的所有路径下匹配的程序

### 实用命令

- which

显示所运行程序的所在路径

- cd -

回到上次所在目录，非常好用！

- ls ..

显示上层的所有文件

- ctrl - l

清除屏幕，光标回到顶部

- xdg-open

用合适的程序打开文件

### 程序的交互

![image-20220915112501170](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151125273.png)

## lesson 2 Shell Tools and Scripting

### 变量定义

- `foo=bar`即可在shell中定义变量

注：不能有空格，若有空格，我们其实是执行foo程序，其第一个参数为=，第二个参数为bar

- 对于字符串来说，我们要注意空格，以及quote，因为通常情况下，空格会在shell中起到参数分割的作用：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209160955902.png" alt="image-20220916095505840" style="zoom:50%;" />

如上图，单引号的内容不会被替换，字面字符串

### 预留的特殊变量

一般情况下，命令会使用stdout返回输出，使用stderr返回错误，并返回一个程序代码（表示执行状态）。0代表正常执行，非0代表有错误发生

- $0：脚本名称

- $1~9：第一个到第九个参数

- $#：参数的数量

- $$：命令的pid

- $@：得到所有参数

- $?：得到前一条命令的错误代码

  - 正确执行：$?得到0

  - 错误执行：

    <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161014169.png" alt="image-20220916101421140" style="zoom:50%;" />

  - 特殊的命令true or false:

    <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161015522.png" alt="image-20220916101543485" style="zoom:50%;" />

- $_：得到前一条命令的最后一个参数
- !!：当某条命令没有permissions可以使用：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161010640.png" alt="image-20220916101058607" style="zoom:50%;" />

**程序输出到变量中**

- 使用$()包住命令

```shell
foo=$(pwd)
echo $foo
```

- 使用字符串替换

例一：`echo "We are in $(pwd)"`

例二：`for file in $(ls)`

- 使用**过程替换**（好用！！）

> 当命令期待从文件中获取输入时很方便

`<(ls)`可以将命令的输出存到临时文件中，并返回临时文件的句柄

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161034442.png" alt="image-20220916103446414" style="zoom: 50%;" />

上图的命令可以打印本层和上层目录的所有文件

### 逻辑运算

- 或||：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161021671.png" alt="image-20220916102127646" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161022703.png" alt="image-20220916102240676" style="zoom:50%;" />

- 与&&：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161025657.png" alt="image-20220916102507619" style="zoom:50%;" />

- 无条件执行；（连接多条命令）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161026912.png" alt="image-20220916102608880" style="zoom:50%;" />

### 测试表达式（包含比较）

test 程序可以评估表达式的真假，如果是真，返回0；否则返回1。

- -f file 如果file存在并且是一个常规文件，则返回True

### 通配符 

**wild cards**

> 简化表达式

`*` 匹配任意多个字符

`? ` 匹配一个字符

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161101849.png" alt="image-20220916110137809" style="zoom:50%;" />

**`{}`扩展列表**

只有一系列命令中有公共子串，我们就可以使用花括号来自动进行扩展。

例一：创建4个文件

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161104277.png" alt="image-20220916110422251" style="zoom:50%;" />

上面的命令等价于：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161104138.png" alt="image-20220916110451110" style="zoom:50%;" />

例二：将所有py和sh文件移到某个文件夹中

`mv *{.py,.sh} folder`
例三：将project1拷贝到新目录中，并在双方的test目录下建立文件

![image-20220916110633163](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161106193.png)

第二个命令 一共有2*3=6个参数

例四：与例三功能类似

同理，可以进行如下操作：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161109920.png" alt="image-20220916110918896" style="zoom:50%;" />

建立不同的文件,并比较foo文件夹和bar文件夹的差异：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161115706.png" alt="image-20220916111508674" style="zoom:50%;" />

**语法检查**

`shellcheck file` 有利于帮助我们找到语法错误，**debug好用！**

### 控制流程

**顺序执行**

- 定义一个函数

```shell
 mcd(){
	mkdir -p "$1"
	cd "$1"
}
```

- source可以在shell中定义函数mcd，之后就可以使用mcd快速执行脚本

**if条件**

- 当在bash里使用判断时，使用`if [[判断表达式]]`

**循环语句**

- 循环的语法为：for name in "xxx"; do

### shell函数和脚本的区别

- shell函数只被加载一次,script每次执行时都会被加载；
- 函数在当前的shell环境中被执行，而脚本在它们自己的进程里执行，也就是说，函数可以改变环境变量（改变当前目录），但是脚本不行；

### 实用工具

#### find usage

- 第一步：--help
- 第二步：man
- 第三步：比起使用man获得详尽的文档，使用tldr获得常用命令的示例更加有用，例如`tldr ffmpeg`

#### find files

> programmers can't avoid doing this

**find**

查找当前目录下名称为src的目录：

`find . -name src -type d`

查找当前目录下路径里有test的python文件：

`find . -path '**/test/*.py' -type f`

 查找前一天以来修改的所有文件：

`find . -mtime -1`

寻找作用大小为500k~10M的zip文件：

`find . -size +500k -size -10M -name '*.tar.gz'`

将找到的每一个文件删除：

`find . -name "*.tmp" -exec rm {} \;`

找到所有PNG文件，并将它们转换为JPG：

`find . -name "*.png" -exec convert {} {}.jpg \;`

find -iname 代表忽略大小写

**fd**

使用fd做为find的替代： simple, fast, and user-friendly

`fd ".*py"`

**locate**

寻找文件的效率更高，locate使用了数据库，通过索引来进行高校查找

有好就有坏：locate只使用文件名来查找

#### find content/code

搜索当前文件夹下所有包含foobar的位置

`grep -R foobar .`

-v 可以打印不满足匹配的所有行

快速搜索scratch目录下所有python文件中，包含”import request“的位置，并显示其上下文，上下5行。

`rg "import requests" -t py -C 5  ~/scratch`

快速搜索所有不满足匹配的sh文件（包含隐藏文件）

`rg -u --files-without-match "^#\!" -t sh`

`--stats` 额外打印关于搜索的所有信息

#### find command

- 使用up/down方向键，缺点是不方便

- 使用history

展示所有历史命令，并找到convert相关命令：

`history 1| grep convert`

- 使用backward-searching

ctrl + R，重复键入可以在匹配结果中替换

- fzf：增加了交互功能

![image-20220916141326989](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161413034.png)

**过去子串搜索**(前缀匹配)

#### directory navigation

tree, broot

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161418809.png" alt="image-20220916141831747" style="zoom:50%;" />

nnn

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209161420513.png" alt="image-20220916142051466" style="zoom:50%;" />

### 示例

```shell
#!/bin/bash

echo "Starting program at $(date)" # Date will be substituted

echo "Running program $0 with $# arguments with pid $$"

for file in "$@"; do
    grep foobar "$file" > /dev/null 2> /dev/null
    # When pattern is not found, grep has exit status 1
    # We redirect STDOUT and STDERR to a null register since we do not care about them
    if [[ $? -ne 0 ]]; then
        echo "File $file does not have any foobar, adding one"
        echo "# foobar" >> "$file"
    fi
done
```

- 中间判断的含义为：$?是否不等于0
- 当在bash里使用判断时，使用`if [[判断表达式]]`
- 循环的语法为：for name in "xxx"; do

### 作业练习

- 不断运行同一个脚本，看他什么时候出错，题目要求如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209171349272.png" alt="image-20220917134900194" style="zoom:50%;" />

解答如下：

```shell
cnt=0
rm {std,err}*.log

while ./bug.sh >> std_output.log 2>> err_output.log; do
    cnt=$((cnt+1));
done

cat {std,err}*.log

echo $cnt
```

- 寻找某个目录下最近修改的文件（递归地），是否可以列出所有文件？

列出所有文件命令：

`find ../ -path "../kgclue/**" -type f | xargs ls -lt`

结果如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209171441323.png" alt="image-20220917144127242" style="zoom: 50%;" />

列出最近修改的文件：

`find ../ -path "../kgclue/**" -type f | xargs ls -lt 2>/dev/null | head -n1
