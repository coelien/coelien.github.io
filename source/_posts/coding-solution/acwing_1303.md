---
title: coding笔记： acwing 1303 斐波那契前 n 项和
tags: 
- 快速幂
- 矩阵乘法
categories:
- coding
- math
---

# Acwing 1303 斐波那契前 n 项和

## Content

大家都知道 Fibonacci 数列吧，f1=1,f2=1,f3=2,f4=3,…,fn=fn−1+fn−2。现在问题很简单，输入 n 和 m，求 fn
的前 n 项和 Sn mod m。

## Input

共一行，包含两个整数 n 和 m。

## Output

输出前 n 项和 Sn mod m 的值。

## Code

这道题其实很有意思，其计算通式为 $F_n = F_1 *A^{n-1}$。它是一种求斐波那契数列的变体，结合了线性代数和快速幂的思想。解题的难点在于如何构造矩阵A。

#### 求斐波那契数列（logn）

令$F_n = [f_n, f_{n+1}]$，$F_{n+1} = [f_{n+1},f_{n+2}]$。

由$[f_{n+1},f_{n+2}] = [f_n, f_{n+1}] \begin{bmatrix} 0 & 1\\ 1&1 \end{bmatrix}$，可得

$A = \begin{bmatrix} 0 & 1\\ 1&1 \end{bmatrix}$

同理可以推广到前n项和。

#### 求斐波那契数列前n项和（logn）

令$F_n = [f_n, f_{n+1}, S_n]$，$F_{n+1} = [f_{n+1},f_{n+2}, S_{n+1}]$。

由$[f_{n+1},f_{n+2}, S_{n+1}] = [f_n, f_{n+1}, S_n] \begin{bmatrix} 0 & 1 & 0\\ 1&1&1 \\ 0&0&1\end{bmatrix}$，可得

$A = \begin{bmatrix} 0 & 1 & 0\\ 1&1&1 \\ 0&0&1\end{bmatrix}$

```c++
#include<iostream>
#include<cstring>

using namespace std;

const int N = 3;
typedef long long LL;
int n,m;
void mul(int f[N], int a[][N]){
    int temp[N] = {0};
    for(int i=0;i<N;++i){
        for(int j=0;j<N;++j){
            temp[i] = (temp[i] + (LL)f[j] * a[j][i]) % m;
        }
    }
    memcpy(f,temp,sizeof temp);

}

void mul(int a[][N]){
    int temp[N][N]={0};
    for(int i=0;i<N;++i){
        for(int j=0;j<N;++j){
            for(int k=0;k<N;++k){
                temp[i][j] = (temp[i][j] + (LL)a[i][k] * a[k][j]) % m;
            }
        }
    }
    memcpy(a,temp,sizeof temp);
}

int main(){
    scanf("%d%d",&n,&m);
    int a[N][N] = {
        {0,1,0},
        {1,1,1},
        {0,0,1}
    };
    int f1[N] = {1,1,1};
    n--;
    while(n){
        if(n&1) mul(f1,a);
        mul(a);
        n>>=1;
    }
    cout<<f1[2]<<endl;
    return 0;
}
```

