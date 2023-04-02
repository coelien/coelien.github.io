---
title: CS106L笔记：OOP
tags: 
- CS106L
- C++
categories:
- courses
- c++
---

# Object Oriented Programming

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303140956539.png" alt="image-20230314095605396" style="zoom:50%;" />

## Fundamentals

**.h vs .cpp**

- .h defines public interface API
- .cpp handle all the dirty details

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303141002323.png" alt="image-20230314100210163" style="zoom:50%;" />

**why so may extensions?**

- header file: .h, .hh, .hpp
- source file: .cc, .cpp, .cxx, .c++, .C

- Depends on the compiler



<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303141007195.png" alt="image-20230314100748047" style="zoom:50%;" />

## Const

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303230937511.png" alt="image-20230323093656327" style="zoom:50%;" />

### Why use **CONST**?

There is no excuse for ignoring the safety mechanisms provided with a product, and there is particularly no excuse for programmers too lazy to write const-correct code.

> same as **Why don't we use global variables?**

- "Non-const variables can be **read or modified by any part of the function**" <**NOT SAFE!!!**>

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303141019331.png" alt="image-20230314101927243" style="zoom: 33%;" />

- if we don't use const, we **can't spot the bug easily** in the above code

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303141022931.png" alt="image-20230314102259807" style="zoom: 33%;" />

- if we use const instead of the one above:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303141024534.png" alt="image-20230314102420495" style="zoom:33%;" />

- we could get errors from the compiler(**compiler time error**):

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303141025176.png" alt="image-20230314102522080" style="zoom:50%;" />

- Besides, const allows us to **reason about** whether a variable will be changed

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303141026766.png" alt="image-20230314102658693" style="zoom: 33%;" />

### **const and Classes**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303141037650.png" alt="image-20230314103712508" style="zoom:50%;" />

- can't call non-const member function on const object:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303141039891.png" style="zoom: 33%;" />

### Const Iterators

- const vector<int>::iterator itr **acts like** int* const  itr

- to make an iterator read-only, define a new const_iterator

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303141047618.png" alt="image-20230314104743484" style="zoom: 43%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303141050956.png" alt="image-20230314105022855" style="zoom:50%;" />

## Operators

### Operator Overloading

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303160923676.png" alt="image-20230316092301560" style="zoom: 33%;" />

> When dealing with **user-defined class**:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303160926232.png" alt="image-20230316092614143" style="zoom: 33%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303160929617.png" alt="image-20230316092944550" style="zoom:33%;" />

```c++
// member
vector<string>& vector<string>::operator+=(const string& s){
    push_back(element);
    return *this;
}
vec += "hello";
vec.operator+=("hello");
```

```c++
// const member
StringVector StringVector::operator+(const StringVector& s) const{}
// non-member
StringVector operator+(const StringVector& s1, const StringVector& s2){}
```

**So, implement as Member of Non_Member?**

- [], (), ->, = must be implemented as member
- <<, implemented as non-member
- +,< if binary op treats both operands equally, implement as non-member
- += if binary op doesn't treat both operands equally( change lhs)

```c++
// non-member function
std::ostream& operator<<(std::ostream& os, const Fraction& F){
    os << f.num<< "/" << f.denom;
    
}
```

### POLA(Principle of Least Astonishment)

> design operators primarily to mimic conventional usage

- **if operator is ambiguous ,  don't overload that operator**

- **use non-member functions for symmetric operators**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303161020191.png" alt="image-20230316102026110" style="zoom:33%;" />

> The second one doesn't work if overloaded as member function.

- **always provide all out of a set of related operators**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303161022725.png" alt="image-20230316102230667" style="zoom: 33%;" />

> don't astonish others: if you implement < , you should also implement > etc.

- **always think about const vs non-const for member functions**:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303161042328.png" alt="image-20230316104219230" style="zoom:50%;" />

![image-20230316104332635](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303161043742.png)

Functors (basically lambdas)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303161027210.png" alt="image-20230316102703125" style="zoom: 50%;" />

Advanced Multi-threading Support (C++20)

```c++
awaiter operator co_await() const noexcept {
    return awaiter{*this};
}
```

## Special Member Functions

```c++
StringVector vec7 = vec4;// copy constructor
vec7 = vec2;// copy assignment
return vec7; // copy constructor
```

**when we don't use default copy constructor?** 

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303161120965.png" alt="image-20230316112057876" style="zoom:50%;" />

**when we have ownership issues**:

- ownership issues: a member is a handle on a resource outside of the class
- pointers
- mutexes
- filestreams

### Rule of Three

- **if you explicitly define a copy constructor, copy assignment of destructor, you should define all three**

when we need to declare a destructor, we have to take care of the ownership issues, the issues happen to other two.

## Move Semantics

> emplace_back?

- based on the **philosophy**: if you don't need any copy, don't do it

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303191420432.png" style="zoom:50%;" />

- for **larger** **objects**

### All things have its beginning

> What happens when running the following codes?

```c++
StrVector readNames(size_t size){
    StrVector names(size,"Ito");
    return names;
}

int main(){
    StrVector name1 = readNames(54321234);
    StrVector name2;
    name2 = readNames(54321234);
}
```

- you may find it surprising, lots of copy constructor and destructor!

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303191435725.png" alt="image-20230319143511666" style="zoom:50%;" />

- so any optimization?

- **copy elision**, c++17, compiler make the copy only once in the caller function(main)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303191445093.png" alt="image-20230319144541047" style="zoom: 50%;" />

> still have any further optimization ?
>
> YES!! The cool thing **move** can save us more time.

in the following code:

```c++
name2 = readNames(54321234);
```

- readNames is a temporary value, and will become homeless and will be destroyed, why we need to copy it? There is no need!
- We could **move(steal) the resources** instead!

### lvalue vs. rvalue

- lvalue: has a name, can find address
- rvalue: no name, temporary

```c++
v1[1] = 4*i; // v1[1] is a reference to named value "lvalue"
v1[2] = *ptr; // *ptr is a "lvalue" because it points to a named value
```

  **lvalue reference vs. rvalue reference**

```c++
auto&& v4 = v1+v2; // save the rvalue, or expands its life time!
```

- any changes to v4 will change (v1+v2) temporary object.

```c++
auto& ptr3 = &val; // Wrong! can't bind lvalue reference to rvalue
auto&& val2 = val; // Wrong! can't bind rvalue reference to lvalue
```

- but we could bind const lvalue reference to rvalue

```c++
const auto& ptr3 = ptr + 5; // correct! const save us a lot!
```

> why it works?

because you are **not changing it** anyway, so it is fine.

### move constructor and assignment

> why rvalue are key to move semantics?

- lvalue is **NOT disposable**, so you can copy from, but definitely cannot move from.
- rvalue is **disposable**, so you can move from

```c++
// StringVector(StringVector&& other) noexcept;
// StringVector& operator=(StringVector&& rhs) noexcept;

Axess& operator=(Axess&& rhs){
    students = std::move(rhs.students); // avoid copy!
}
```

- rhs itself is actually lvalue, so we need to **std::move**

### swap

> The first very primitive attempt:

```c++
template<typename T>
void swap(T& v1, T& v2){
    T temp = v1;
    v1 = v2;
    v2 = temp;
}
```

- Bad! make three copies, sadly.

> with move, second attempt with the knowledge of rvalue reference:

```c++
template<typename T>
void swap(T& v1, T& v2){
    T temp = std::move(v1);
    v1 = std::move(v2);
    v2 = std::move(temp);
}
```

- Cool! We use no copy with move!

### Rule of Five

- If you declare one, you should declare all of them.(plus move constructor and move assignment)

## Inheritance

### Namespaces

```c++
namespace Lecture{
    int count(const vector<int>& v){
        int ctr = 0;
        for(auto i:v){
            if(i==1)
                ++ctr;
        }
        return ctr;
    }
    
}
cout<<Lecture::count(vec)<<endl;
```

### Motivation

**interface**

```c++
class Drink{
	virtual void make() = 0;
};
```

- cannot be instantiated



**Abstract Classes**

- cannot be instantiated

- has at least one pure virtual function



**Non-virtual Destructors**

```c++
class Base{
    ~Base(){}
};

class Derived: public Base{
    ~Derived(){}
}

Base *b = new Derived();
delete b; // Never calls the destructor for derived
```



**Templetes vs. Derived Classes**

> When to use each?
>
> **static** vs. **dynamic** polymorphism

- Templetes: at complile time generate real codes
- Derived Classes: at run time

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303231129665.png" alt="image-20230323112910581" style="zoom:50%;" />

## RAII

> How many code paths are there ?

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251104958.png" alt="image-20230325110400783" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251109312.png" alt="image-20230325110927161" style="zoom:50%;" />

> Can you guarantee there is no memory leek in the code?

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251111388.png" alt="image-20230325111156256" style="zoom:50%;" />

- For normal code paths, this code is fine.
- But if there is an exception, we will never reach the `delete e`.

> How to enforce exception safety?

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251127109.png" alt="image-20230325112707980" style="zoom:50%;" />

**RAII: Resource Acquisition Is Initialization**

(Scope Based Memory Management) (Constructor Acquires, Destructor Releases)(Pointer to Implementation)

**What is RAII?**

- all resources should be **acquired** in the **constructor**
- all resources should be **released** in the **destructor**

an example: **ifstream satisfies RAII**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251136084.png" alt="image-20230325113657964" style="zoom:50%;" />

- you should not call close

another example

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251142706.png" alt="image-20230325114215587" style="zoom:50%;" />

**fix**:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251142623.png" alt="image-20230325114229502" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251143604.png" alt="image-20230325114333457" style="zoom:50%;" />

besides:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251147106.png" alt="image-20230325114736038" style="zoom:33%;" />

> Is automatic memory management a good or bad thing?

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251154720.png" alt="image-20230325115402637" style="zoom:50%;" />

### Unique Pointer

- uniquely owns its resource and deletes it when the object is destroyed.
- can't be copied.

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251154195.png" alt="image-20230325115455131" style="zoom: 50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251159944.png" alt="image-20230325115919877" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251200256.png" alt="image-20230325120030213" style="zoom:50%;" />

### Shared pointer

- Resources can be stored by any number of shared_ptrs.
- Delete when none of them point to it

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303251203948.png" alt="image-20230325120328899" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303291012305.png" alt="image-20230329101204208" style="zoom:50%;" />

