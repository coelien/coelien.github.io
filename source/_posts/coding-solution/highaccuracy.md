---
title: coding笔记：高精度加减乘除
tags: 
- 高精度
categories:
- coding
---

# 高精度算数

## 主要思想

- 如何存储大数？使用数组（**vector**）

- 怎么存储？**低位在前，高位在后**

  之所以这样是因为进位容易，不需要将数组整体向后移动一位

- **模拟人工**算数流程

## 加法

```c++
vector<int> highAccAdd(vector<int>&A,vector<int>&B){
    int c = 0;
    vector<int> C;
    if (A.size()<B.size()) return highAccAdd(B,A);
    for(int i=0;i<A.size();i++){
        if(i<B.size()) c+= B[i];
        c += A[i];
        C.push_back(c%10);
        c /= 10;
    }
    if(c) C.push_back(1);
    return C;
}
```

## 减法

- 减法我们规定只能大数减小数，否则不够借位；
- 小数减大数可以转化为负的大数减小数
- (c+10)%10合并了c<0时借位和>0时不用借位的情况；

```c++
bool biggerThan(vector<int>&A,vector<int>&B){
    if(A.size()!=B.size()) return A.size()>B.size();
    else{
        for(int i=A.size()-1;i>=0;i--)
            if(A[i]!=B[i]) return A[i]>B[i];
    }
    return true;
}
```

```c++
vector<int> highAccMinus(vector<int>&A,vector<int>&B){
    int c = 0;
    vector<int> C;
    // 我们假设A一定大于B,且均为正数
    for(int i=0;i<A.size();i++){
        c+=A[i];
        if(i<B.size()) c-=B[i];
        C.push_back((c+10)%10);
        if(c<0) c=-1;
        else c=0;
    }
    while(C.size()>1&&C.back()==0) C.pop_back();
    return C;
}
```

## 乘法

- 高精度乘法实际上是一个大数乘以小数
- 注意要去除前导零；

```c++
vector<int> highAccMul(vector<int>&A,int b){
    int c = 0;
    vector<int> C;
    for(int i=0;i<A.size()||c;i++){
        if(i<A.size()) c+=A[i]*b;
        C.push_back(c%10);
        c/=10;
    }
    while(C.size()>1&&C.back()==0) C.pop_back();
    return C;
}
```

## 除法

- 模拟手工除法，注意这次是从高位开始循环，不同于之前的算数
- 每次的借位可以使用c来存储，使用c*10+A[i]来表示当前的被除数

```c++
vector<int> highAccDiv(vector<int>&A,int b,int*r){
    int c=0;
    vector<int>C;
    for(int i=A.size()-1;i>=0;i--){
        c= c*10+A[i];
        C.push_back(c/b);
        c%=b;
    }
    *r = c;
    reverse(C.begin(),C.end());
    while(C.size()>1&&C.back()==0) C.pop_back();
    return C;
}
```

