---
title: CS106L笔记：STL
tags: 
- CS106L
- C++
categories:
- courses
- c++
---

# C++ Learning Roadmap

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303010901484.png" alt="image-20230301090101345" style="zoom:50%;" />

# STL

## overview of STL

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302221106498.png" alt="image-20230222110624393" style="zoom:50%;" />

## why use c++ for development?

- community
- developing or evolving
- open source (unlike Java , which is owned by Oracle, so you can use c++ for business need.)
- super extensible (boost library like c++ STL plus plus)

## Containers Adaptors

- stack & queue

**Adapt vector and deque containers to suit this particular need**.

- why not just user a vector/deque?

Express ideas and intent directly in code. And compartmentalize messy constructs.

## Associative Containers

- have no idea of sequence 
- Data is accessed using the key instead of indexes.
- includes:
  - std::map<T1,T2>：associate one key type of data with other value type of data
  - std::set<T>
  - std::unordered_map<T1,T2>
  - std::unordered_set<T>
- map & set are based on ordering property of keys(needs to be comparable)

- when to use map/set , when to use unordered ones?

map/set: iterate through **a range of elements**

unordered: **access individual elements** by key

- How do we iterate over associative containers?

use **Iterators**

## Multimaps

- Maps store unique keys
- sometimes we want the map to have the same key pointing different values

```c++
multimap<int, int> myMMap;
myMMap.insert(make_pair(3,3));
myMMap.insert({3,12});
cout<<myMMap.count(3)<<endl; // prints 2
```

## Iterators

- why iterator is powerful?
  - many scenarios require looking at elements, regardless of what type of container is storing those elements.
  - go through sequence in a standardized way( use the exact same code to perform a logical action, regardless of the data structure, independent of the container)

- philosophy in Iterators
  - let us view a non-linear collection in a linear manner(iterate one by one without visiting twice)

## Map Iterators

- the iterator of map<string, int> points to a std::pair

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302241043837.png" alt="image-20230224104257692" style="zoom:50%;" />

`auto time = std::make_pair(1,45);`

- find vs. count

count is just a call to the find. So find is faster

## Iterator types

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302241112715.png" alt="image-20230224111208654" style="zoom:50%;" />

the one on the right is more powerful than the left one.

# Templates

## Why use template functions(generic functions)?

- They are such a powerful tool that could solve a bunch of similar problems.

**what if there are multiple potential templates functions?**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303031055828.png" alt="image-20230303105544750" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303031056493.png" alt="image-20230303105613382" style="zoom:50%;" />

## Concept Lifting

- Question the assumptions you place on the parameters whether they are really necessary?
- Can you solve a more general problems by relaxing the constraints?

> An amazing example:

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303011004199.png" alt="image-20230301100400139" style="zoom:50%;" />

## Implicit interfaces

- a template function defines an implicit interface that each template parameter must satisfy

## Concept

- A predicate, evaluate at compile time, that is a part of the interface

## predicate(谓语)

predicate is a function which takes in some numbers of arguments and returns a boolean

- unary predicate: one argument

- binary predicate: two arguments

more generic version of countOccurences:

```c++
template<typename InputIterator, typename UniaryPredicate>
int countOccurences(InputIterator begin, InputIterator end, UniaryPredicate predicate){
    int count = 0;
    for(auto iter = begin; iter!=end; ++iter)
        if(predicate(*iter)) ++count;
    return count;
}
```

- what if we want predicate to be more flexible? 

- pre c++11 solution:

  **use classses**, of course,we have to override operators.

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303031008082.png" alt="image-20230303100838940" style="zoom:50%;" />

- c++11 solution:

  **use lambda**, it's an **object** but acts like a function, because it generate the class and create an instance of it.

  **you can use auto in parameters to templatize the lambda**

```c++
int main(){
    int limit = getInteger("minimum for A?");
    vector<int> grades = readStudenGrades();
    auto func = [limit](auto val){return val>=limit;};
    countOccurences(grades.begin(),grades.end(),func);
}
```

- you can also capture by reference, only capture the variables you need

```c++
set<string> teas{"black","green","oolong"};
string banned = "boba";
auto likedByAvery = [&teas, banned](auto type){
    return teas.count(type) && type!=banned;
};
```

## Algorithms

### Why use algorithms?

- Abstractions
- Generic
- Correct
- Heavily optimized

### std::sort

```c++
auto compareRating = [] (const Course& c1, const Course& c2){
    return c1.rating < c2.rating;
};

std::sort(courses.begin(),courses.end(),compareRating);

std::copy(courses.begin(),courses.end(),std::ostream_iterator<Course>(std::cout),"\n");
```

### std::nth_element

```c++
auto m = v.begin() + v.size()/2;
std::nth_element(v.begin(), m, v.end());
std::cout << "\nThe median is " << v[v.size()/2] << '\n';

std::nth_element(v.begin(), v.begin()+1, v.end(), std::greater{});
std::cout << "\nThe second largest element is " << v[1] << '\n';
std::cout << "The largest element is " << v[0] << '\n';
printVec(v);
```

### std::stable_partition

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303081108267.png" alt="image-20230308110804100" style="zoom:50%;" />

- return: Iterator to the first element of the second group

```c++
auto isDep = [](auto element){
    return element.name.size() > 2 && element.name.substr(0,2)=="CS";
};
std::stable_partition(courses.begin(), courses.end(),isDep);
std::copy(courses.begin(),courses.end(),std::ostream_iterator<Course>(std::cout),"\n");
```

### std::copy_if

```c++
#include <algorithm>
#include <iostream>
#include <iterator>
#include <numeric>
#include <vector>
 
int main()
{
    std::vector<int> from_vector(10);
    std::iota(from_vector.begin(), from_vector.end(), 0);
 
    std::vector<int> to_vector;
    std::copy(from_vector.begin(), from_vector.end(),
              std::back_inserter(to_vector));
// or, alternatively,
//  std::vector<int> to_vector(from_vector.size());
//  std::copy(from_vector.begin(), from_vector.end(), to_vector.begin());
// either way is equivalent to
//  std::vector<int> to_vector = from_vector;

    std::cout << "to_vector contains: ";
 
    std::copy(to_vector.begin(), to_vector.end(),
              std::ostream_iterator<int>(std::cout, " "));
    std::cout << '\n';
 
    std::cout << "odd numbers in to_vector are: ";
 
    std::copy_if(to_vector.begin(), to_vector.end(),
                 std::ostream_iterator<int>(std::cout, " "),
                 [](int x) { return x % 2 != 0; });
    std::cout << '\n';
 
    std::cout << "to_vector contains these multiples of 3: ";
 
    to_vector.clear();
    std::copy_if(from_vector.begin(), from_vector.end(),
                 std::back_inserter(to_vector),
                 [](int x) { return x % 3 == 0; });
 
    for (int x : to_vector)
        std::cout << x << ' ';
    std::cout << '\n';
}
```

上面两例copy_if的最后一个参数使用了Iterator Adaptor

- back_inserter
- ostream_iterator<int>

### std::remove_if

- remove can change the content, but does not change the size of the container(because it is not a member of std::vector)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303081233485.png" alt="image-20230308123326380" style="zoom:50%;" />

- **erase-remove idiom**:

```c++
v.erase(std::remove_if(v.begin(),v.end(),pred),v.end());
```

### std::find

## Wrapping Up The STL

### Abstraction in the STL

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303110946513.png" alt="image-20230311094615384" style="zoom:33%;" />

```c++
std::string str_tolower(std::string s) {
    std::transform(s.begin(), s.end(), s.begin(), 
                // static_cast<int(*)(int)>(std::tolower)         // wrong
                // [](int c){ return std::tolower(c); }           // wrong
                // [](char c){ return std::tolower(c); }          // wrong
                   [](unsigned char c){ return std::tolower(c); } // correct
                  );
    return s;
}
```

fileToString: get the file into a string 

```c++
string fileToString(ifstream& file){
    string ret="";
    string line;
    while(std::getline(file,line)){
        std::transform(line.begin(),line.end(), line.begin(),[](unsigned char c){return std::tolower(c);});
        ret += (line + " ");
    }
    return ret;
}
```

countOccurrences: find the number of the word that appears in a text

- why can't we use std::count()?

cause, iterator in string actually points to a character, which can only count character

- use std::search() instead

 Searches for the first occurrence of the sequence of elements `[s_first, s_last)` in the range `[first, last)`

```c++
int countOccurrences(){
    string toFind = " "+ word+" ";
    // <regex>
    auto curr = text.begin();
    auto end = text.end();
    int count = 0;
    
    while(curr!=end){
        auto found = std::search(curr,end,toFind.begin(),toFind.end());
        if(found == end) break;
        ++count;
        curr = found + word.size();
    }
    return count;
}
```

```c++
int dotProduct(const vector<int>& vec1,const vector<int>& vec2){
    std::inner_product(vec1.begin(),vec1.end(),vec2.begin(),0);  
}

int mag(const vector<int>& vec){
    return std::sqrt(dotProduct(vec,vec));
}
```

