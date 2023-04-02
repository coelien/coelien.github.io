---
title: coding笔记： acwing二刷
tags: 
- acwing二刷
categories:
- coding
---

# acwing二刷

## 基础算法

> 快速排序

### AcWing 785. 快速排序

给定你一个长度为 n的整数数列。

请你使用快速排序对这个数列按照从小到大进行排序。

并将排好序的数列按顺序输出。

#### 输入格式

输入共两行，第一行包含整数 n。

第二行包含n个整数（所有整数均在 1∼e9 范围内），表示整个数列。

#### 输出格式

输出共一行，包含n个整数，表示排好序的数列。

#### 数据范围

1≤n≤100000

#### 输入样例：

```
5
3 1 2 4 5
```

#### 输出样例：

```
1 2 3 4 5
```

```c++
//785. 快速排序
#include<iostream>
using namespace std;
const int  N = 100010;
int a[N];
void quick_sort(int le,int ri){
    if(le>=ri) return;
    int x = a[le+ri>>1];
    int l = le - 1, r = ri+1;

    while(l<r) {
        do { ++l; } while (a[l] < x);
        do { --r; } while (a[r] > x);
        if (l < r) swap(a[r], a[l]);
    }
    quick_sort(le,r);
    quick_sort(r+1,ri);
}
int main(){
    int n;
    scanf("%d",&n);
    for(int i=0;i<n;i++) scanf("%d",&a[i]);
    quick_sort(0,n-1);
    for(int i=0;i<n;i++) printf("%d ",a[i]);
    return 0;

}
```
