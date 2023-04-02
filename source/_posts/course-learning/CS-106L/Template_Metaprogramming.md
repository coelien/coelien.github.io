---
title: CS106L笔记：Template Metaprogramming
tags: 
- CS106L
- C++
categories:
- courses
- c++
---

# Template Metaprogramming

## Motivating example

```c++
#include<vector>
#include<deque>
#include<set>
#include<string>
#include<algorithm>

using namespace std;
int main(){
    vector<string> names{"Anna","Ethan","Nikhil","Avery"};
    auto anna_iter = find(name.begin(),name.end(),"Anna");
    auto avery_iter = find(name.begin(),name.end(),"Avery");
    
    return std::distance(anna_iter,avery_iter);
}
```

> how do you implement your own distance function?

```c++
template<typename It>
size_t my_distance(It first, It last){
	return last-first;
}
```

- the above code is O(1),  which is efficient.
- but the above implementation only works for random access iterator

**another optimization!**

```c++
template<typename It>
size_t my_distance(It first, It last){
	size_t result = 0;
    while(first!=last){
        ++first;
        ++result;
    }
    return result;
}
```

- but the second way is O(N), **for any kind of iterators**, which is unexpected

> How does STL achieve this?

- You have to extract type information from iterator of what it is.
- need to determine if it is random access iterator
- run the piece of code based on the type of iterator:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291051896.png" alt="image-20230329105106863" style="zoom: 67%;" />

## Computations on Types

**Comparision of values vs. type computations : A Preview**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291055082.png" alt="image-20230329105536999" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291056035.png" alt="image-20230329105611948" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291056873.png" alt="image-20230329105640787" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291057528.png" alt="image-20230329105723424" style="zoom:50%;" />

 

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291059815.png" alt="image-20230329105942716" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291102824.png" alt="image-20230329110203735" style="zoom:50%;" />

## Meta-functions

**template types**

- use them for template classes

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291107666.png" alt="image-20230329110744559" style="zoom:50%;" />

**template values**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291111031.png" alt="image-20230329111146933" style="zoom:50%;" />

**meta-function**

abstractly:

- It is a function that operates on some types/values("parameters")
- and outputs some types/values("return values")

concretely:

- It is a **struct** that has **public member types/fields**(output) which depend on what the **template types/values**(input) are instantiated with.

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291116242.png" alt="image-20230329111659110" style="zoom:50%;" />

- a simple example:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291119223.png" alt="image-20230329111946116" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291127504.png" alt="image-20230329112718402" style="zoom:50%;" />

```c++
//input type, output value
template<typename T, typename U>
struct is_same{
    static const bool value = ????;
};

//input value, output value
template<int V, int W>
struct is_same{
    static const bool value = (V==W);
};

```

## Template Deduction

**Template Rules**

- **Template Specialization**: you can have a "generic template", as well as "specialized" templates for  particular types

*Fully specialize:*

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291241387.png" alt="image-20230329124148296" style="zoom:50%;" />

**why specialization?**: using a bit array to store booleans is much more efficient.

*partially specialize:*

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291246152.png" alt="image-20230329124600043" style="zoom:50%;" />

#### Some examples using Template Deduction

> let's build up a collection of predicate meta-functions

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291250681.png" alt="image-20230329125007552" style="zoom:50%;" />

**why does the second one return true?**

- the compiler tries to first match the specialized template
- the second one got matched, so return true

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291254740.png" alt="image-20230329125420606" style="zoom:50%;" />

- kind of interesting, you can figure out the type info, because of **template deduction rules**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291257238.png" alt="image-20230329125712112" style="zoom:50%;" />

- of course, same technique can be used to return a **type**

Actually it is a "hack", because we  are using the compiler's template matching rules to implement an if/else statement for types(we can't directly compare them)

## Let finish our myDistance implement:

```c++
using category = typename std::iterator_traits<It>::iterator_category;
if (std::is_same<category, std::random_access_iterator_tag>::value){
    return last - first;
}else{
    size_t result = 0;
    while(first!=last){
        ++first;
        ++result;
    }
    return result;
}
```

- need to remove the offending code when the if statement knows that part won't be run;

- pre-C++17: **std::enable_if**

- C++17: **if constexpr**

```c++
using category = typename std::iterator_traits<It>::iterator_category;
if constexpr (std::is_same<category, std::random_access_iterator_tag>::value){
    return last - first;
}//else ...
```

> what is **constexpr**?

you can calculate this expression at compile time.(stronger form of const)

> what is **if constexpr**?

calculate boolean at compile-time , Replace the entire if/else with the code that will be actually be run