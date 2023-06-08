---
title: coding笔记：AcWing 1299 五指山
tags: 
- math
- 裴蜀定理
- 扩张gcd
categories:
- coding
- math
---

# coding笔记：AcWing 1299 五指山

## Content

大圣在佛祖的手掌中。

我们假设佛祖的手掌是一个圆圈，圆圈的长为 n，逆时针记为：0,1,2,…,n−1，而大圣每次飞的距离为 d。

现在大圣所在的位置记为 x，而大圣想去的地方在 y。

要你告诉大圣至少要飞多少次才能到达目的地。

**注意**：孙悟空的筋斗云只沿着逆时针方向翻。

## Input

有多组测试数据。

第一行是一个正整数 T，表示测试数据的组数；

每组测试数据包括一行，四个非负整数，分别为如来手掌圆圈的长度 n，筋斗所能飞的距离 d，大圣的初始位置 x和大圣想去的地方 y。

## Output

对于每组测试数据，输出一行，给出大圣最少要翻多少个筋斗云才能到达目的地。

如果无论翻多少个筋斗云也不能到达，输出 Impossible。

## Solution

这道题简单在数学公式较好得到，利用的都是现成的公式：比如扩展gcd，比如不定方程的通解。只要在数学知识上补充相应的概念，这道题还是好解的，否则很难。

### first attempt-纯暴力(O(N))

```c++
#include<iostream>

using namespace std;

int main(){
    int t;
    cin>>t;
    while(t--){
        int n,d,x,y;
        cin>>n>>d>>x>>y;
        int i = x;
        int count = 0;
        bool arr = true;
        while(true){
            if(i == y){
                break;
            }else if(i == x && count > 0){
                arr = false;
                break;
            }
            i = (i+d) % n;
            ++count;
        }
        if(arr)
            cout<<count<<endl;
        else
            cout<<"Impossible"<<endl;
    }
    return 0;
}
```

### second attempt-扩展gcd

可将题目抽象为数学公式：

`x+c*d = k*n+y`

即`c*d + k*n = y-x ` (k可正可负，我们不关心)

根据数学常识我们知道，上式如果能够成立，y-x必须是(d,n)最大公约数的倍数，否则无解。

因此我们可以使用扩展gcd算法先求出`c0*d + k0*n = (d,n)`时的解，再同比例扩大`(y-x)/(d,n)`即可

但是题目要求我们必须求出C的最小值，因此根据C的通解：`c = c0 +u*n/(n,d)`

我们可以求出C的最小值（正值的最小值）：`(c0 % (n/gcd)+n/gcd) %(n/gcd)`

```c++
#include<iostream>

using namespace std;
typedef long long LL;

int ext_gcd(int a, int b, LL &x, LL &y){
    if(!b){
        x = 1;
        y = 0;
        return a;
    }
    int d = ext_gcd(b, a%b, y,x);
    y -= a/b*x;
    return d;
}
int main(){
    int t;
    cin>>t;
    while(t--){
        int n,d,x,y;
        LL c,k;
        cin>>n>>d>>x>>y;
        int gcd = ext_gcd(d,n,c,k);
        if((y-x) % gcd){
            cout<<"Impossible"<<endl;
        }else{
            c = c * (y-x) / gcd;
            c = (c % (n/gcd)+n/gcd) %(n/gcd);
            cout<<c<<endl;
        }
    }
    return 0;
}
```

