---
title: CS106L笔记：Multi-threading
tags: 
- CS106L
- C++
categories:
- courses
- c++
---

# Multi-threading

## Thread

- threads are ways to parallelise  execution

> what's the problem?

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303290928367.png" alt="image-20230329092820246" style="zoom:50%;" />

- DATA RACE!

- use LOCK!

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303290929264.png" alt="image-20230329092948175" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303290930801.png" alt="image-20230329093018757" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303290931865.png" alt="image-20230329093102789" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303290938335.png" alt="image-20230329093809261" style="zoom:50%;" />

```c++
//std::mutex mtx;
void greet(int id){
    //std::lock_guard<std::mutex> lg(mtx);
    cout<<"my name is "<<id<<endl;
}
int main(){
    cout<<"Greetings from my threads..."<<endl;
    std::thread thread1(greet,id1);
    std::thread thread2(greet,id2);
    thread1.join();
    thread2.join();
    cout<<"All greetings done!"<<endl;
    return 0;
}
```

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303290947072.png" alt="image-20230329094700034" style="zoom:50%;" />

- **Data Racing** is unpredictable!

> use lock_guard to ensure the greeting is atomic:

```c++
std::mutex mtx;
void greet(int id){
    std::lock_guard<std::mutex> lg(mtx);
    cout<<"my name is "<<id<<endl;
}
```

> use join to let the main wait the thread to finish

```c++
thread1.join();
thread2.join();
```

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303290959453.png" alt="image-20230329095900412" style="zoom:50%;" />

- the results seems fine!

```c++
//...work with vector if threads
vector<std::thread> threads;

for(size_t i=0; i < kNumThreads; ++i){
    threads.push_back(std::thread(greet,i));
}

for(std::thread& t: threads){
    t.join();
}
//...
```

