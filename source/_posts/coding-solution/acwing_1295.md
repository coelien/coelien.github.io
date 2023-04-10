---
title: coding笔记： acwing 1295 X的因子链
tags: 
- 组合数学
- 质因数分解
- 求质数
- 数论
categories:
- coding
- math
---

# Acwing 1295 X的因子链

## Content

输入正整数 X，求 X的大于 1的因子组成的满足任意前一项都能整除后一项的严格递增序列的最大长度，以及满足最大长度的序列的个数。

## Input

输入包含多组数据，每组数据占一行，包含一个正整数表示 X

## Output

对于每组数据，输出序列的最大长度以及满足最大长度的序列的个数。

## Solution

> 算术基本定理

$X = p_1^{a_1}*p_2^{a_2}*...*p_k^{a_k}$

每個大於1的[自然數](https://zh.wikipedia.org/wiki/自然数)，要麼本身就是[質數](https://zh.wikipedia.org/wiki/质数)，要麼可以寫為2個或以上的質數的[積](https://zh.wikipedia.org/wiki/积)

显然，满足要求的序列最大长度为$a1+a2+...+ak$。难的是如何求出序列的个数

> X的所有质因子的排列数

有重复的数该怎么求呢？（以$2^2*3^3*5$为例）

- 假设所有数互不相同，那么总共排列有6！个
- 把一样的排列归到一族里去，每一族里的元素有多少个呢？2！*3！个
- 那么一共有$\frac{6!}{2!*3!}$个不同的组

推广：

- 总排列数=$\frac{(a_1+a_2+...+a_k)!}{a_1!*a_2!*...*a_k!}$（多重集合的排列数问题）

> 分解质因数

组数比较大，直接分解可能会超时

可以预求出每个数的最小质因子(筛法求素数)，分解时分别求N的质因子P，N/P的质因子，以此类推。因此是logN的复杂度

> 筛法求素数

- 求出1~n中的所有质数，以及每一个数的一个最小质因子

```c++
// 线性筛法：O(N)
int primes[N];
int min_p[N];
bool st[N];
int cnt = 0;
void getPrimes(int n){

    for(int i=2;i<=n;++i){
        if(!st[i]) min_p[i]=i, primes[cnt++] = i;
        for(int j=0; primes[j] <= n/i; ++j){
            st[primes[j]*i] = true;
            min_p[primes[j]*i] = primes[j];
            // pj一定不大于i的最小质因子
            if(i%primes[j] == 0) break;
        }
    }
}

```

## Code

```c++
#include <iostream>

using namespace std;

typedef long long LL;
const int M = (1<<20) + 10;
int primes[M];
int minp[M];
bool st[M];
int cnt = 0;
void getPrimes(int n){
    for(int i=2;i<=n;++i){
        if(!st[i]) minp[i]=i,primes[cnt++]=i;
        for(int j=0;primes[j]*i<=n;++j){
            st[primes[j]*i] = true;
            minp[primes[j]*i] = primes[j];
            if(i%primes[j] == 0) break;
        }

    }
}
void acwing_1296(){
    getPrimes(1<<20);
    // for(int i=0;i<20;++i) printf("%d ",primes[i]);
    int fact[30];
    int sum[30];
    int x;
    while(scanf("%d",&x)!=-1){
        int k = 0, total = 0;
        while(x!=1){
            int p = minp[x];
            // cout<<p<<endl;
            fact[k] = p;
            sum[k]=0;
            while(x%p==0){
                x/=p;
                ++sum[k];
                ++total;
            }
            ++k;
        }
        LL res = 1;
        // cout<<total<<endl;
        for(int i=1;i<=total;++i) res*=i;
        for(int i=0;i<k;++i)
            for(int j=1;j<=sum[i];++j){
                res/=j;
            }
        printf("%d %lld\n",total, res);
    }

}
int main(){
    //acwing_1246();
    acwing_1296();
}
```

