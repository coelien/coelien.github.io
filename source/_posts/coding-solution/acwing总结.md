---
title: coding笔记：Acwing知识点总结
tags: 
- 算法总结
categories:
- coding
---

# Acwing 知识点总结

> 在这个总结里主要讲算法模板和思路，具体的题目代码见刷题.md

## 第一讲 基础算法

### 排序

#### 快排

**主要思想**

快排属于分治算法，**分治算法**都有三步：

1. 分成子问题
2. 递归处理子问题
3. 子问题合并

寻找一个哨兵，将小于这个哨兵的元素放于其左边，大于他的元素放于其右；递归的排序其左区间和右区间即可完成排序，快排没有子问题合并这一步

**think point**

- 如何选取哨兵的位置？

  哨兵的位置应该随机选取，或是取中间：若是取左边作为哨兵，若数组本身就是有序的，每次分划的时间为O(n)，之后的子问题规模即为0和n-1，总共的复杂度即为$O(n+n-1+n-2+...)=O(n^2)$，而取中间作为哨兵可以保证每次可以把区间分为长度近似相等的两部分，因而复杂度为递归深度*O(n)即为$O(nlog_2n)$

- 每次划分两部分后，最终哨兵的位置是否需要获知？

  根据每次分划后是否需要知道哨兵的位置，可以有两种思路来进行快排：

**哨兵最终位置未知**

第一种思路是acwing的模板思路，缺点是有一些坑，不好理解(i,j位置不能互换，不然可能陷入死循环等)但是代码较为整洁，适合笔试用

```c++
void quicksort(int q[], int l, int r) {
    if (l>=r) return;
    // 使用区间中点作为哨兵
    int mid = l+r >>1;
//    swap(q[mid],q[l]);
    int x = q[mid], i = l-1, j= r+1;
    //忽略了哨兵的位置，我不在乎哨兵的位置具体在哪里
    //我只知道，j右边的数都大于等于哨兵，j左边的数都小于等于哨兵
    //之所以不取等于是防止数组越界，还有可以更好地划分子问题
    //缺点是可能会有很多次无效交换
    while(i<j) {
        do i++; while (q[i] < x);
        do j--; while (q[j] > x);
        if (i < j) swap(q[i], q[j]);
    }
    quicksort(q,l,j);
    quicksort(q,j+1,r);
}
```

算法证明：

明显地，j右边的数大于等于x,i左边的数小于等于x。现需证明while结束时，q[l..j]均<=x，若成立，即可证明j这个端点可以划分整个区间。因为i>=j，所以q[1..j-1]均<=x。因为退出循环前的i<j判断为false，所以最后一次交换不执行，所以q[j]必然<=x。综上可以证明q[l..j]均<=x，所以j这个端点可以划分整个区间。

**哨兵最终位置已知**

第二种是数据结构教材的官方思路，优点是步骤明确，方便理解，较少坑，缺点是代码冗长

```c++
void quicksort2(int q[], int l, int r) {
    if (l>=r) return;
    // 首先保存哨兵的值
    int pio = q[l];
    int i = l;
    int j = r;
    while(i<j){
        // 之所以可以取=，是因为之前有判断，一定不会越界
        while(i<j && q[j]>=pio) j--;
        if(i<j) q[i] = q[j];
        else break;
        while(i<j && q[i]<=pio) i++;
        if(i<j) q[j] = q[i];
    }
    //哨兵的位置是确定的=i=j，所以只需要对其左边和右边分别进行排序即可
    q[i] = pio;
    quicksort2(q,l,i-1);
    quicksort2(q,i+1,r);
}
```

**算法改进**

> 实际上是对于某类特殊数据的算法增强

如果数组的每个**数据都相同**时，上面的第一种思路可以pass，但是第二种思路会超过时限，究其原因为：分化后的子问题规模即为0和n-1，总共的复杂度即为$O(n+n-1+n-2+...)=O(n^2)$，所以对于这种情况需要特殊处理，而且不能简单地将中点与左端点交换（实际上没有改变算法流程），第二种思路地修改点如下：

```c++
while(i<j){
        // 之所以可以取=，是因为之前有判断，一定不会越界
        while(i<j && q[j]>pio) j--;
        if(i<j) q[i++] = q[j];
        else break;
        while(i<j && q[i]<pio) i++;
        if(i<j) q[j--] = q[i];
    }
```

经测试，第二种思路改进后速度较第一种思路能快上一点点

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202205091219072.png" alt="image-20220509121919959" style="zoom:50%;" />

**快排应用—快速选择**

**主要思想**

如果先排序再取第k个数，会导致$O(nlog_2n)$的复杂度，因此采用快速选择算法，基于快排的模板进行修改即可：

```c++
int findKthNumber(int q[],int l,int r,int k){
    if(l==r) return q[l];
    int mid = l+r>>1;
    int x = q[mid];
    int i = l-1,j = r+1;
    while(i<j){
        do{i++;}while(q[i]<x);
        do{j--;}while(q[j]>x);
        if(i<j) swap(q[i],q[j]);
    }
    int lol = j-l+1;
    if(k<=lol) return findKthNumber(q,l,j,k);
    else return findKthNumber(q,j+1,r,k-lol);
}
```

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202205091742503.png" alt="image-20220509174254407" style="zoom: 33%;" />

- 如上图所示，修改点在于如何递归地处理子问题：在（l,r）中寻找第k个数。
- 而快排给出了找到良好分界点的方式，即j左边的数包括j自身都小于等于x，j右边的数都大于等于x。
- 因此蓝色区间的数都小于等于红色区间的数，从而如果k小于等于蓝色区间的数的个数，那么第k个数一定在蓝色区间中；反之，如果k大于蓝色区间的数的个数，那么第k个数一定大于等于x,即在红色区间中可以找到。

**疑惑与解释**

最难理解的地方其实在于边界处，如果k==lol，即第k个数刚好是蓝色区间的个数，我们是否可以直接返回x呢？答案是否定的。原因在于j这个点其实决定了蓝色区间的长度，但是这不代表x就处于j这个位置，即[l..j]可能都小于x，因此第k个数不一定就是x，下图是一个实例，直观解释了这一点：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202205091759950.png" alt="image-20220509175915922" style="zoom:67%;" />

上图中x是236，j=22指向192这个数，lol=2=k，可见215，192都小于x(236)，236，244都大于等于x，但是返回的却不是第k个数。**k=lol只能说明下一步要去寻找蓝色区间里最大的数**。

#### 归排

**主要思想**

首先把待排序数组分成子问题（排序两个长度近似相等的数组），递归处理子问题直到只剩一个元素，两个数组排序完成后需要合并成一个新的排序好的数组，这就是第三步：子问题合并。代码如下：

```c++
#include <iostream>

using namespace std;
const int N = 1e6 + 10;
int n;
int q[N];
int temp[N];

void mergesort(int q[], int l, int r) {
    if (l >= r) return;
    int mid = l + r >> 1;
    mergesort(q, l, mid), mergesort(q, mid + 1, r);
    int k = l, i = l, j = mid + 1;
    while (i <= mid && j <= r) {
        if (q[i] <= q[j]) temp[k++] = q[i++];
        else temp[k++] = q[j++];
    }
    while (i <= mid) temp[k++] = q[i++];
    while (j <= r) temp[k++] = q[j++];
    for (int i = l; i < r + 1; i++)
        q[i] = temp[i];
}

int main() {
    scanf("%d",&n);
    for(int i=0;i<n;i++) scanf("%d",&q[i]);
    mergesort(q,0,n-1);

    for (int i=0;i<n;i++) printf("%d ",q[i]);
    return 0;
}
```

**归并排序应用—逆序对数量**

这个题目相当于套在了归并排序的大框框里。注意合并两个已排序子序列时如何去计算逆序对数量。

### 二分

#### 整数二分

- 整数二分因为涉及+1，-1，所以为了防止死循环，需要考虑边界情况。
- 当我们定义一个性质，该性质可以将数据二分时（即存在边界时），二分算法是可以将该边界找出来的。

**主要思想**

- 假设在整个区间内部是可以找到答案的，我们对整个区间不断进行二分
- 每次都要选择答案所在的区间去进行下一步搜索（每次会把整个区间的大小缩小一半）
- 二分一定会保证区间里有答案的，但是存在特例，即原问题无解的情况（无解一定和题目有关）
- 在原问题无解的情况下，若没有遇到数组边界，找到的区间一定是满足条件的，但不是query；而如果遇到数组边界，那么就可能不会满足找到的区间一定是满足条件的（以整数的范围这道题为例）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209071515734.png" alt="image-20220508181123806" style="zoom: 33%;" />

<p align="center">该图即为寻找满足check的最右端端点示意图</p>

上图对应的代码即为二分模板1的代码，如下所示：

```c++
bool check(int mid) {return false;}
double function(double){return 0.0;}
// 二分模板1
int bsearch1(int l,int r){
    // 考虑边界，l+1=r时，算法是否正确
    // l+1=r时,需要修改mid=l,会陷入死循环
    // 所以修改mid=(l+r+1)/2,遇到边界时，mid=r,可以顺利退出
    // r会小于l的可能仅在最后一句，但遇到边界且不满足条件时，计算的mid不可能=l,所以退出循环时r不可能小于l,即l=r
    // 返回r,l均可
    while (l<r){
        int mid = l+r+1 >>1;
        // 若check 代表满足条件
        if(check(mid)) l = mid;
        else r = mid-1;
    }
    return l;
}
// 二分模板2
int bsearch2(int l,int r){
    // 寻找不满足的断点
    // 考虑边界，l+1=r时，算法是正确的
    // 退出循环时，l==r的原理同上
    while(l<r){
        int mid = l+r>>1;
        if(check(mid)) l = mid+1;
        else r = mid;
    }
    return l;
}
```

#### 浮点数二分

- 较整数二分简单，因为不需要考虑边界（因为没有整除，每次都可以完美地将边界缩小一半）
- 思想与整数二分一样，只要满足时时刻刻答案都在我们的区间即可； 
- 但我们的区间足够小时，我们就可以认为已经找到了答案；
- 有两种方式可以去写浮点数二分的循环条件：一个是固定循环100次，如果原来区间长度为1时，结束时区间长度即为$1/2^{100}$；或是指定一个很小的数epsilon，当区间长度小于这个数即可退出。

```c++
double fbsearch(double l,double r,double target){
    // 浮点数二分查找
    while(r-l>1e-8){
        double mid = (l+r)/2;
        double val = function(mid);
        if (val>=target) r = mid;
        else l = mid;
    }
    return l;
}
```

**应用：数的三次方根**

求数的三次方根可以使用二分查找来做。因为函数$x^3$是单调的，所以很容易判断mid和target的大小关系，从而缩小查找范围，代码如下：

```c++
double fbiSearch(double l, double r, double target, double (*pf)(double)) {
    while (r - l > 1e-7) {
        double mid = (l + r) / 2;
        if (pf(mid) <= target) l = mid;
        else r = mid;
    }
    return r;
}
double calTrip(double x){
    return x*x*x;
}
int findRoot3(){
    double n;
    double (*pf)(double)=calTrip;
    cin>>n;
    if(n<0) printf("-%.6f",fbiSearch(0,ceil(-n),-n,calTrip));
    else printf("%.6f",fbiSearch(0,ceil(n),n,calTrip));
    return 0;
}
```

简要介绍下引入的函数指针，有了函数指针就可以方便地将计算方法传入二分查找算法来找解。`double (*pf)(double)=calTrip;`可以定义一个函数指针，非常有用。

### 高精度算数

**主要思想**

- 如何存储大数？使用数组（**vector**）

- 怎么存储？**低位在前，高位在后**

  之所以这样是因为进位容易，不需要将数组整体向后移动一位

- **模拟人工**算数流程

**加法**

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

**减法**

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

**乘法**

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

**除法**

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

### 前缀和和差分

**前缀和**

- 定义：使用s[N]表示数组q[N]的前缀和数组，s[i]表示q[1-i]的元素的和
- 公式：$s[i] = s[i-1]+q[i]$
- 注意，下标范围为1-N，且s[0]=0

**差分**

- 定义：**前缀和的逆运算**，假想一个d[N]，使得q[N]是d的前缀和数组，那么d[N]就称为q的差分。
- 公式：$d[i] = q[i]-q[i-1]$
- 注意，d下标范围为1-N

#### 前缀和和差分的应用

**求一段数的和O(1)**

- 只需要事先处理得到q数组的前缀和s数组，就可以通过$s[l]-s[r-1]$在O(1)时间内完成一段数的求和

**对一段数加上某个数O(1)**

- 假设我们已经有了差分数组，我们只需要对其求一遍前缀和即可得到原数组，时间复杂度为O(n)
- 我们对d[i]加上c后，a[i]~最后都会加上c（根据前缀和的定义），所有如果想要给一段数(l,r)都加上c，就只需要d[l]+c，d[r+1]-c

**对子矩阵求和O(1)**

- 对一维进行扩展可以得到二维区间和=$S_{x_2,y_2}-S_{x_2,y_{1}-1}-S_{x_1-1,y_2}+S_{x_1-1,y_1-1}$，图像直观解释如下图：

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209071520034.png" alt="image-20220606134133525" style="zoom:50%;" />

**对二维区间加上某个数O(1)**

画图可以清楚的解释该算法：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209071520511.png" alt="image-20220606134339757" style="zoom: 33%;" />

代码如下：

```c++
template<int n,int m>
void diffAdd2D(int (&diff) [n][m], int x1,int y1,int x2,int y2,int c){
    diff[x1][y1]+=c;
    diff[x1][y2+1]-=c;
    diff[x2+1][y1]-=c;
    diff[x2+1][y2+1]+=c;
}
```

注：在代码里我使用了二维数组传参的一种优美方法（使用模板推导出二维数组维度），在函数调用时无须指明维度大小，在函数体内也可以直接索引，从而可以减少代码量。

同理可以进行推广到三维甚至多维数组，代码如下：

```c++
template<int n, int m, int q>
void test3Dimens(int (&test)[n][m][q], int x1,int y1,int z1,int c){
    test[x1][y1][z1]-=c;
}

const int Q = 110;
int test3D[Q][Q][Q];

int multiDimenTest(){
    test3D[1][2][3]=6;
    test3Dimens(test3D,1,2,3,6);
    cout<<test3D[1][2][3];
    return 0;
}
```

**推广：3D空间求和O(1)**

> 应该不会考类似的题，但是对于拓展思维应该还是有用的

```c++
template<int n, int m, int q>
void 3D_Sum(int (&test)[n][m][q], int x1,int y1,int z1,int x2,int y2,int z2){
    return test[x2][y2][z2]-test[x2][y1-1][z2]-test[x1-1][y2][z2]-test[x2][y2][z1-1]+test[x2][y1-1][z1-1]+test[x1-1][y2][z1-1]+test[x1-1][y1-1][z2]-test[x1-1][y1-1][z1-1];
}
```

### 双指针算法

#### 指向两个序列

- 维护某种次序，例如归并排序合并两个有序序列的操作。

#### 指向一个序列（更常见）

- 两个指针维护的是一段区间，例如快排

**代码模板**

```c++
for(i=0,j=0;i<n;i++){
    while(j<i && check(i,j)) j++;
    //每道题的具体处理逻辑
}
```

**核心思想**

- 运用了某些单调的性质，可以把朴素算法O(n2)优化到O(n)

**算法应用**

- 把一行字符串中的每个单词分别输出出来，且每个单词占一行

```c++
#include <iostream>
#include <string.h>
int main(){
    char str[1000];
    gets(str);
    int n = strlen(str);
    for(int i=0;i<n;i++){
        int j=i;
        while(j<n && str[j]!=' ') j++;
        for(int k=i;k<j;k++) cout<<str[k];
        cout<<endl;
        i=j;
    }
    return 0;
}
```

- **找出最长的不包含重复数字的连续子序列**

可以先想暴力的算法，再进行优化

暴力：先枚举终点，再枚举起点 O(n2)

优化：当区间终点向后移动时，区间起点也一定向后移动

**可以开一个大数组**，动态地确定一下当前区间每个数出现多少次。当加入一个数，如果出现次数>1，则说明重复了，需要更新起点的位置。当数据很大时可以用哈希表来做。

### 位运算

**求整数N其二进制表示中第k位数字是几**

> 个位是第0位，十位是第1位

- 先把第k位移到最后一位:n>>k
- 看看个位是几:x&1

**lowbit(x)：返回x的最后一位1**

公式：x & -x = x & (~x+1)

> c++中一个整数的负数是原数的补码，即-x=~x+1

直观解释如下图：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209071619788.png" alt="image-20220907161904730" style="zoom:50%;" />

**应用：统计整数的二进制表示中1的个数**

每一次把它的最后一位1去掉，当x=0时，说明没有1了，减了多少次就说明有多少个1。

### 离散化

> 特指整数的离散化，而且是保序的离散化
>
> 总共的取值范围跨度很大，但是我们只用到了其中的一小部分，非常稀疏。

值域比较大，但是数的个数比较小。过程如图所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209071632861.png" alt="image-20220907163221798" style="zoom:50%;" />

- a[]中可能有重复元素，所以需要去重

```c++
//把一个数组排序+去重
vector<int> alls;
sort(alls.begin(),alls.end());
// unique将所有元素去重，并且范围去重后元素的尾端点
alls.erase(unique(alls.begin(),alls.end()),alls.end());
```

- 如何算出a[i]离散化后的值？

通过二分求出离散化后的值：找到第一个大于等于x的位置

```c++
int find(int x){
    int l=0,r=alls.size()-1;
    while(l<r){
        int mid = l+r>>1;
        if(alls[mid]>=x) r = mid;
        else l = mid+1;
    }
    return r+1;//映射到1,2,...,n
}
```

**应用**：求区间所有数的和

结合前缀和和离散化，将每一个可能用到的下标存到vector alls中，操作的新下标即离散后化的值，每个操作在新数组上进行

### 区间合并

> 将所有有交集的区间进行合并，输出合并后的区间个数

**贪心的思想**

按照区间的左端点排序，扫描所有区间，当前区间和前面维护的区间的位置关系如下图：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209071719327.png" alt="image-20220907171913269" style="zoom:50%;" />

 可以使用pair来存储区间端点，使用vector来存储所有区间。使用st,ed表示当前维护区间的端点，若遍历区间与维护区间没有交集，就可以将维护区间加入result数组中去，否则更新维护区间的端点。

## 第二讲 数据结构

> 这里默认已经对基本的数据结构有所了解，因此重点在于数组模拟练习与总结。对于算法题而讲，使用数据模拟的好处不言而喻（速度快），是STL外的一大解题利器。

> 变量和代码解释都写在了注释里，可以方便之后的回顾。p.s. 为了方便，有的简单的注释就直接用英语打了，可以减少输入法的繁琐切换。

### 链表

#### 单链表

- 数据准备及初始化

```c++
//单链表
const int N = 100010 //就实际工程而言，定死可能不太优美，注意优化方案
int e[N];// store the value of the nodes
int ne[N];// store the ptrs of next node 
int head;// store the index of head node
int idx;// store the current available index

void init(){
    head = -1;//注意：一开始链表为空，使用-1来辨识空结点
    idx = 0;
}
```

- 数据操纵

```c++
//insert to the head of the list
void add_to_head(int x){
    e[idx] = x;
    ne[idx] = head;
    head = idx++;
}
//insert the node after the k(index) node
void insert_k(int k, int x){
    e[idx] = x;
    ne[idx] = ne[k];
    ne[k] = idx++;
}
//remove the node after the k(index) node
void remove_k(int k){
    ne[k] = ne[ne[k]];
}
```

- 应用：删除倒数第二个结点 O(n)

用一个额外的pre来保存当前遍历结点的前两个结点，只要当前结点的下一结点非空就一直遍历，否则退出循环，代码如下：

```c++
void remove_l2(){
    int pre = -1;
    int cnt=0;
    int cu = head;
    while(ne[cu]!=-1){
        cnt++;
        if(cnt>1){
            if(pre==-1) pre = head;
            else pre = ne[pre];
        }
        cu = ne[cu];
	}
    remove_k(pre);
}
```

#### 双链表

- 数据准备与初始化

```c++
int e[N];
int l[N]; //store the pre idex of current node
int r[N]; //store the next idex of current node
int idx; // store the current available node
void init(){
    r[0]=1; // 默认0号点为左边界，1号点为右边界
    l[1]=0;
    idx = 2;
}
```

- 数据操纵

```c++
// insert a node at the head of the list
void insert_left(int x){
    insert_k_right(0,x);
}
//insert a node at the end of the list
void insert_right(int x){
    insert_k_left(1,x);
}
//insert a node to the k(index) node 's left
void insert_k_left(int k, int x){
    insert_k_right(l[k],x);
}
//insert a node to the k(index) node 's right
void insert_k_right(int k, int x){
    e[idx] = x;
    r[idx] = r[k];
    l[idx] = k;
    l[r[k]]=idx;
    r[k] = idx++;
}
// delete the k(index) node 
void delete_k(int k){
    l[r[k]] = l[k];
    r[l[k]] = r[k];
}
```

### 栈

> 先进后出

- 数据准备与初始化

```c++
int stk[N];
int tt=0;
```

- 数据操纵

```c++
//push to the top of stack
void push(int x){
    stk[++tt]=x;
}
//pop the top of the stack
void pop(){
    if(tt>0) tt--;
}
//check if the stack is empty
bool isEmpty(){
    if(tt) return false;
    else return true;
}
//query the top of the stack
int top(){
    return stk[tt];
}
```

一种可能的优化：面向对象的封装+任意类型的数据

```c++
#include<iostream>
const int N = 100010;

template<class T>
class Stack {
    T stk[N];
    int tt;
public:
    Stack(){
        init();
    }

    //init
    void init() { tt = 0; }

    // pop
    T pop() { return stk[tt--]; }

    // push
    void push(T x) { stk[++tt] = x; }

    // isempty
    bool isEmpty() {
        if (tt > 0) return false;
        else return true;
    }
    void printStack(){
        printf("--------------\n");
        for(int i =1;i<=tt;i++) printf("%c ",stk[i]);
        printf("--------------\n");
    }

    // top
    T queryTop() { return stk[tt]; }
};
```

### 队列

> 先进先出

- 数据准备与初始化

```c++
int q[N];
int hh,tt;//指向队头和队尾元素

void init(){
    hh=0;
    tt=-1;
}
```

- 数据操纵

```c++
//向队尾插入一个数
void push(int x){
    q[++tt] = x;
}
//从队头弹出一个数
int pop(){
    if(!isEmpty()) return q[hh++];
    else printf("error");
}
//判断队列是否为空
bool isEmpty(){
    if(hh>tt) return true;
    else return false;
}
//查询队头元素
int top(){
    if(!isEmpty()) return q[hh];
    else printf("error");
}
```

### 单调栈和单调队列

#### 主要思路

- 先想一个朴素做法
- 再想队列或栈中哪些元素是没有用的，然后把所有这些没有用的元素删掉
- 再看一下有没有单调性，怎么去优化这个问题

#### 应用场景

- 单调栈：给定一个序列，求序列中的每一个数左边离它最近的，且比它小的数在什么地方

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091706762.png" alt="image-20220909170603704" style="zoom:50%;" />

主要思想即，比当前遍历元素大的栈中的数永远不会再用到。每个元素进栈一次，最多出栈一次，因此时间复杂度为O(N)。

```c++
//暴力做法
for(int i=0;i<n;i++)
    for(j=i-1;j>=0;j--)
        if(a[i]>a[j]){
            cout<<a[j]<<endl;
            break;
        }
#include<stack>
//单调栈做法
int main(){
    int n;
    cin>>n;
    stack<int> st;
    for(int i=0;i<n;i++){
        int x;
        cin>>x;
        while(st.size() && x <= st.top()){
            st.pop();
        }
        if(!st.size())
            cout<<-1<<' ';
        else
            cout<<st.top()<<' ';
        st.push(x);
    }
}
```

- 单调队列：求一个滑动窗口里的最大值或最小值

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131530337.png" alt="image-20220913153009278" style="zoom: 33%;" />

主要思想是使用一个队列来维护窗口里的所有数（下标），只有前面一个数比后面的一个数大，我们就可以断定，前面的点一定没有用，因此可以把这个点删掉。只要有逆序对，我们就可以把大的点删掉，把所有这些点删掉的话，整个数列就会变为一个严格单调上升的队列，其最小值正是队头。

```c++
//暴力做法:O(Nk)
//遍历队列中的所有数

//优化
#include<queue>
using namespace std;
const int N = 1000010;
int a[N];
int main(){
    int n,k;
    scanf("%d%d",&n,&k);
    for(int i=0;i<n;i++) scanf("%d",&a[i]);
    queue<int> que;
    for(int i=0;i<n;i++){
        int head = que.front();
        if(que.size()&&i-k==head) que.pop();
        while(que.size&&a[que.back()]>= a[i]) que.pop_back();
        que.push(i);
        if(i>=k-1) printf("%d ",que.front());
    }
}
```



### Trie

- 概念：用于高效存储和查找字符串集合的数据结构

- 数据准备与初始化

```c++
int son[N][26]; //假设只有大写字母A-Z,存储当前结点的所有子结点
int cnt[N]; //记录以i为结尾的字符串的个数
int idx; //存储当前可用的索引
//下标为0的点既是根结点又是空结点
void init(){
    idx = 1;
}
```

- 数据操纵

```c++
char str[N];
// add a string to the trie
void insert(char str[]){
    if(!str[0]) return;
    int cu = 0;
    for(int i=0;str[i];i++){
        int k = str[i]-'A';
        if(!son[cu][k]) son[cu][k] = idx++;
        cu = son[cu][k];
    }
    cnt[cu]++;
}
// query the number of occurances of the string in the trie
int find_occur(char str []){
    if(!str[0]) return 0;
    int cu = 0;
    for(int i=0;str[i];i++){
        int k = str[i]-'A';
        if(!son[cu][k]) return 0;
        cu = son[cu][k];
    }
    return cnt[cu];
}
```

### 并查集

- 概念：可以快速处理并维护
  - 将两个集合合并
  - 询问两个元素是否在一个集合中
- 如果使用暴力解法，合并两个集合需要O(n)的时间，而并查集可以在O(1)的时间内近似完成这两个操作。

- 数据准备与初始化

```c++
int fa[N]; // store the parent index of each node
// only the root node has the property of fa[root]=root'
void init(){
    int num;
    cin>>num;
    for(int i=1;i<=num;i++) fa[i]=i;
}
```

- 数据操作

```c++
// find the set(root) index which num x is in
int find_root(int x){
    //if current node is not root
    if(fa[x]!=x) fa[x]=find_root(fa[x]); // path compression
    return x;
}
// combine two sets which num a/b is in
void combine(int a, int b){
    int r_a = find_root(a);
    int r_b = find_root(b);
    if(r_a != r_b) fa[a]= b;
}
// query whether two num are in the same set
bool inSameSet(int a, int b){
    return find_root(a)==find_root(b);
}
```

### 堆

- 在STL中也称为优先队列priority queue

- 数据准备与初始化

```c++
int heap[N];//heap is a 完全二叉树
int size;
int cnt; // cnt the num of insert
int cti[N],itc[N]; // preserve a count to index mapping and a index to count mapping
// cause 2*i is the left node index and 2*i+1 is the right node index
// so we have to start at the index 1
```

- 数据操纵

```c++
// swap
void myswap(int x, int y){
    swap(heap[x],heap[y]);
    swap(cti[itc[x]],cti[itc[y]]);
    swap(itc[x],itc[y]);
}
// build a heap
void buildHeap(){
    for(int i = size/2;i;i--){
        down(i);
    }
}
// up
void up(int x){
    if(x/2 && heap[x/2]>heap[x]){
        myswap(x/2,x);
        up(x/2);
    }
}
// down
void down(int x){
    int min_i = x;
    if(2*x<=size && heap[min_i]>heap[2*x]) min_i = 2*x;
    if(2*x+1<=size && heap[min_i]>heap[2*x+1]) min_i = 2*x + 1;
    if(min_i!=x) {
        myswap(min_i,x);
        down(min_i);
    }
}
// insert a num
void insert(int num){
    heap[++size] = num;
    itc[size] = ++cnt;
    cti[cnt] = size;
    up(size);
}
// output the min num of the heap
int min_val(){
    return heap[1];
}
// delete the min num of the heap
void delete_min(){
    myswap(1,size--);
    down(1);
}
// delete the kth insert num
void delete_k(int k){
    // put the num at position size of the heap(end) to the position cti[k]
    // if we don't know the itc(index to count mapping)
    // we can't update the new cti[itc[size]] which is actually cti[k]
    // so we need a two way mapping
    int tmp = cti[k]; // store this value because the function below it may change its value
    myswap(tmp, size--);
    up(tmp),down(tmp);
}
// modify the kth insert num to x
void modify_k(int k, int x){
    heap[cti[k]] = x;
    up(cti[k]),down(cti[k]);
}
```

### 哈希表

#### 存储结构

**开放寻址法**

> 以开放寻址为主例，因为开的空间小，且写起来简单；拉链法仅贴出代码

- 数据准备与初始化

```c++
int h[N];
//列表长度取>操作集合2倍大小的最小质数，不容易起冲突
int null = 0x3f3f3f3f;// 该数大于10^9，一般看作最大整型，这里取不可能出现的数作为空值

// 使用下面的函数即可得到N的大小为200003
void find_min_prime(int x){
    for(int i=x;;i++){
        bool flag = true;
        for(int j=2;j*j<i;j++){
            if(i%j==0) {
                flag=false;
                break;
            }
        }
        if(flag) {
            printf("%d",i);
            break;
        }
    }
}
void init(){
    memset(h,0x3f,sizeof h);//将所有entry置为null
}
```

- 数据操纵

```c++
// find the index where the num should be(exists if h[index] not null) 
int find(int x){
    int k = (x%N+N)%N; // not to make it negative, compute the hash Which is often the mod operation. 
    while(h[k]!=null && h[k]!=x){
        k++;
        if(k==N) k=0;
    }
    return k;
}
// query whether the num is in the hash table
bool hasNum(int x){
    if(h[find(x)]!=null) return true;
    else return false;
}
// insert a num into hash table
void insert(int x){
    if(!hasNum(x)) h[find(x)]= x;   
}
```

**拉链法**

- 数据准备与初始化

```c++
int h[N] // N = 100003 
int e[N],ne[N],idx;
memset(h,-1,sizeof h);//-1代表为空
```

- 数据操纵

```c++
//拉链法
// insert a num into hash table, can be duplicate in this situation
int insert(int x){
    int k = (x%N+N)%N;
    e[idx] = x;
    ne[idx] = h[k];
    h[k]= idx++;
}
// query whether the num is in the hash table
bool hasNum(int x){
    int k = (x%N+N)%N;
    int cu = h[k];
    while(cu!=-1) {
        if(e[cu]==x) return true;
        cu = ne[cu];
    }
    return false;
}
```

#### 字符串哈希

> 字符串前缀哈希法

首先，要预处理出所有前缀的哈希：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209081535305.png" alt="image-20220908153513235" style="zoom:50%;" />

**note:** h[1]=前缀的哈希值

**如何求字符串哈希值**？

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209081538036.png" alt="image-20220908153854968" style="zoom:50%;" />

- 把字符串看作一个P进制的数
- 把P进制数转换为10进制数
- 取模，就可以把任何一个字符串映射为0~Q-1之间的数

注意：

- 不要把某个字母映射成0
- 假定我们rp足够好，不存在冲突
- p=131或13331，Q=2^64时，99.9%都不会出现冲突
- **预处理前缀哈希**：h(i) = h(i-1)*p + str(i)

- 利用前缀哈希可以**求出所有子串的哈希值**: h(R) = h[L-1]*p^(R-L+1)

- 另外用unsigned long long来存Q，即可以省略取模这步（因为会溢出）

**字符串哈希的作用？**

> 除了不能求循环节，其他好像都代替KMP

快速判断两个字符串是否相同：O(1)

```c++
typedef unsigned long long ULL;

const int N = 100010, P=131;

int n,m;
char str[N];
ULL h[N],p[N];

ULL get(int l, int r){
    return h[r]-h[l-1]*p[r-l+1];
}
int main(){
    scanf("%d%d%s",&n,&m,str+1);
    p[0]=1;
    for(int i=1;i<=n;i++){
        p[i] = p[i-1]*P;
        h[i] = h[i-1]*P+str[i];
    }
    while(m--){
        int l1,r1,l2,r2;
        scanf("%d%d%d%d",&l1,&r1,&l2,&r2);
        
        if(get(l1,r1)==get(l2,r2)) puts("Yes");
        else puts("No");
    }
    return 0;
    
}
```

### STL

#### vector

```c++
vector<int> a;
a.size();
a.empty();
a.clear();//清空
a.front()/back();
a.push_back()/pop_back();
a.begin()/end();//end为最后一个数的后面一个数
//系统为某一程序分配空间时，所需时间与空间大小无关，但与申请次数有关
//所以vector通过倍增尽可能减少申请次数

//遍历
for(vector<int>::interator i = a.begin();i!=a.end();i++) cout<<*i<<' ';
//前面一大串类型可以直接写为auto，比较省事
//范围遍历
for(auto x:a) cout<<x<<' ';

//比较运算
//a<b(字典序)

```

#### pair

```c++
pair<int,string>p;
//支持比较运算,字典序
p={20,"abc"};
```

#### string

```c++
string a = "sdf";
a+="uyiu";

a.substr(1,2);//从1开始，长度为2的子串

//c_str()：返回string对应的字符数组的头指针
printf("%s\n",a.c_str());
```

#### queue

```c++
//从队尾插入，从队头删除
push()
front()
back()
pop()
```

#### priority_queue

```c++
priority_queue<int> heap;
//定义小根堆
priority_queue<int,vector<int>,greater<int>> heap;
push()
top() //返回堆顶元素
pop();

```

#### stack

```
push()
top()
pop()
```

#### deque

双端队列，加强版vector，但速度慢

```c++
#include<deque>
front()/back();
push_back()/pop_back();
push_front()/pop_front();
begin()/end()
```

#### set

基于平衡二叉树，本质上动态维护一个有序序列，支持begin()/end()

```c++
set<int>s;//不能有重复元素
s.insert(); //插入一个数log(n)
s.find();
s.count();//返回某个数的个数
s.erase();
// 输入是一个数x，删除所有x的复杂度为O(k+log(n))
// 输入是一个迭代器，删除这个迭代器
s.lower_bound()/upper_bound();
// lower_bound返回大于等于x的最小的数的迭代器
// upper_bound返回大于x最小的数的迭代器
multiset<int>ms;//可以有重复元素
```

#### map

基于平衡二叉树，本质上动态维护一个有序序列

```c++
insert();//插入的是一个pair
erase();//输入是pair或迭代器
find();
[]//时间复杂度是log(n)

map<string, int> a;
a["hht"] = 1;
cout<<a["hht"]<<endl;
```

#### unordered_set

哈希表，绝大部分操作时间复杂度为O(1)

```c++
//凡是和排序有关的操作都是不支持的
```

#### bitset

压位：若需要开一个空间为10000*10000 bool数组 10^8B=100mb 若题目很不巧，空间为64mb，我们就可以用bitset来存，可以节省8位空间。

```c++
bitset<10000> s;
~,&,|,^
<<,>>
count()返回有多少个1
any()判断是否至少有一个1
none判断是否全为0
set()把所有位，置为1
set(k,v)把第k位变成v
reset()把所有位，置为0
flip(k)把第k位取反
```

### KMP

> KMP算法在各种参考书中均有讲解，但均较为复杂，且不易于理解其中原理，现佐以笔记，记录自己的学习心得
>
> 因为kmp算法相对抽象，因此本文大多辅以图像方便理解
>
> 参考视频：https://www.bilibili.com/video/BV1MY4y1Y7Nh

**问题描述**

给定一个模式串 S，以及一个模板串 P，所有字符串中只包含大小写英文字母以及阿拉伯数字。

模板串 P 在模式串 S 中多次作为子串出现。

求出模板串 P 在模式串 S 中所有出现的位置的起始下标。

**输入格式**

第一行输入整数 N，表示字符串 P 的长度。

第二行输入字符串 P。

第三行输入整数 M，表示字符串 S 的长度。

第四行输入字符串 S。

**输出格式**

共一行，输出所有出现位置的起始下标（下标从 0 开始计数），整数之间用空格隔开。

**数据范围**

1≤N≤10^5
1≤M≤10^6

#### 核心思想

- 先想暴力枚举怎么做的

```c++
for(int i=1;i<=n;i++){
    bool flag = true;
    int t_i = i;
    for(int j=1;j<=m;j++,t_i++){
        if(S[t_i]!=P[j]){
            flag = false;
            break;
        }
    }
}
```

复杂度：O(nm)，浪费了额外信息

- 如何去优化的

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131558146.png" alt="image-20220526112430740" style="zoom: 33%;" />

如图，假设现在已匹配了j个字符，但是第j+1个字符不匹配，即$S[i]!=P[j+1]$，这里S为红色的目标串，P为蓝色的模式串。不匹配时，需将模式传尽可能少的往后移动（我的理解是为了防止漏掉正解），使得移动后的模式串仍与目标串匹配且长度为next[j]，这里采用的思想是**不浪费过去已匹配的信息的同时确保找到所有的解**，这里只需简单的令$j=next[j]$即可减少无用的重复匹配次数，然后再判断P[next[j]+1]和S[i]是否相等，不相等则重复上述步骤直到j=0。

**最大前后缀相等长度**

上述的next[j] = u 的定义为：**P[1,j]的前缀和后缀相等的最大长度(不包括本身)**，之所有取最大前文也有解释，是为了确保找到所有解。

#### next数组求解

既然问题的关键在于求解next数组，那么找到行之有效的算法是很必要的。这里的思想为**归纳法**：假设我们已经求解出了next[1,i-1]，现在求出next[i]即可归纳求出整个next数组。依旧可以看上面的图，因为next[i-1]已知，我们可以把模式串移到对应的位置，判断P[i]是否等于P[next[i-1]+1]。若相等说明next[i] = next[i-1] + 1；若不等令P[next[next[i-1]]+1]和P[i]再进行判断，若一直不满足，那么直到next[...] = 0，说明P[1,i]的前缀和后缀相等的最大长度为0，因此next[i]则为0。

**next[i] = next[i-1] + 1**

下面用反证法解释下为什么该式是成立的：若存在一个更长的前缀和后缀相等的子串，那么同时去掉一个刚匹配的字符，则剩下的串比next[i-1]也要长，这与假设next[i-1]已经是最长的相互矛盾，因此next[i]所存的就是P[1,i]的前缀和后缀相等的最大长度。

```c++
// 求解next数组
// 注：next[1]=0如果第一个字符匹配失败了
for(int i=2,j=0;i<=n;i++){
    while(j && P[j+1]!=P[i]) j = ne[j];
    if(P[j+1]==P[i]) j++;
    ne[i] = j;
}

// kmp匹配
for(int i=1,j=0;i<=m;i++){
    while(j && P[j+1]!=S[i]) j = ne[j];
    if(P[j+1]==S[i]) j++;
    if(j==n){
        print("%d ", i-n);
        j = ne[j];
    }
}
```

#### KMP算法应用

**字符串的循环节**

next数组的解法可以让我们求出字符串的循环节：n-next[n]

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202205261218096.png" alt="image-20220526121842989" style="zoom:33%;" />

由图，因为绿色字符串的前缀和后缀相等的最大长度为next[n]，因此我们可以将该串向后移动n-next[n]，使得重叠部分恰好相等。又因为下面的串是平移的，因此上面串的第一段就等于下面串的第一段且长度为n-next[n]，又下面串的第一段与上面串的重叠部分相等（第二段），因此上面串的第一段和第二段相等，以此类推上面串的所有段都相等且以n-next[n]为长度进行循环，因此是字符串的循环节。

- 不具有循环节的串

例如，“abcdefgh”,不重复的串是不可能有循环节的，若是以其作为模式串，next[i]均为0，kmp算法最多比较O(m+n)次

- 循环节最多的串

例如，“aaaaaaa”，每个字符都相等的串的循环次数最多，若是以其为模式串，kmp算法最多比较匹配O(2m)次

> 详细证明可参考[博客](https://blog.csdn.net/niukai1768/article/details/79579709)

**字符串所有前后缀相等的子串**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131558067.png" alt="image-20220526125319859" style="zoom:33%;" />

这个求解实际和循环节的求解是完全相似的。每次我们将字符串向后平移(实际上是令j = next[j])，求出next数组后我们可以很方便的求出其所有前缀与后缀相等的子串`P[:next[j]], j = next[j] util j = 0`

**现证明该算法为什么能找到所有前后缀相等的子串**

假设存在长度为k的前后缀未能通过上述递归式找到。我们先去找长度大于k的长度为i_t的前后缀，假设找到了且为图中第一段的next[n]，那么下一段next[next[n]]即是以next[n]为结尾的最长的前后缀，因为k也是前后缀，但是k比next[next[n]]长与next[next[n]]的定义相互矛盾，所以k必然不存在，因此算法找到了所有前后缀相等的子串。

## 第三讲 搜索

### DFS

> 执着的算法

- 思想

  会尽可能往深了搜，搜不到了就回溯，每一次回溯完之后判断当前是不是所有的路径均已遍历，若均已遍历，再进行回溯，否则搜索未遍历的路径

- 两个重要概念

  - **回溯**

    注意**恢复现场**

  - **剪枝**

    **最优性剪枝**：当前的路径判断一定不如最优解，就可以剪枝了

    **可行性剪枝**：提前判断当前方案一定是不合法的，那么下面的子树就可以不用进行搜索了

- **顺序**：重要的是顺序，即我们要**用一个什么样的顺序将某一道题的所有方案全部遍历一遍**

- 举例：

全排列问题—最经典DFS的问题

```c++
const int N = 10;
int path[N];
bool st[N];
//求1-n的全排列数量
void dfs(int h,int n){
    if(h==n){
        for(int i=0;i<n;i++)
            printf("%d ",path[i]);
        puts("");
    }
    for(int i=1;i<=n;i++){
        if(!st[i]){
            path[h] = i;
            st[i] = true;
            dfs(i,h+1,n);
            st[i] = false;
        }
    }
}
```

n-皇后问题

1. 第一种搜索顺序：按全排列思路枚举8皇后问题。枚举**每一行这个皇后应该放到哪一列上去**（经过了某种程度的优化，因为两个皇后不可能在同一行），与全排列的搜索顺序完全一致，同一对角线上的元素必定满足上图的直线方程，所以可以用截距b作为直线的编号。我们可以用剪枝的思想来优化算法，即提前判断当前方案肯定是不合法的，就没必要搜索下去了。O(n*n!)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206101454519.png" alt="image-20220610145412486" style="zoom:50%;" />



```c++
const int N = 20;
bool col[N];
bool diag[2*N],udiag[2*N];
char Q[N][N];
// 无需对queencnt计数，比视频里的方法快了4，5ms
void initBoard(int n){
    for(int i = 1;i<=n;i++) {
        for (int j = 1; j <=n; j++)
            Q[i][j] = '.';
    }
}
void nqueen_dfs(int r,int n){
    for(int i=1;i<=n;i++){
        if(!col[i]&&!diag[i+r]&&!udiag[i-r+n]){
            
            col[i] = diag[i+r]= udiag[i-r+n] = true;
            Q[r][i] = 'Q';
            
            if(r==n){
                for(int k=1;k<=n;k++){
                    for(int j=1;j<=n;j++){
                        printf("%c",Q[k][j]);
                    }
                    puts("");
                }
                puts("");
                Q[r][i] = '.';
                col[i] = diag[i+r]= udiag[i-r+n] = false;
                return;
            }
            
            nqueen_dfs(r+1,n);
            Q[r][i] = '.';
            col[i] = diag[i+r]= udiag[i-r+n] = false;
        }
    }
}
```

2. 第二种搜索顺序： 更加原始的一种搜索方式，对于每一个格子，选择是否放皇后， 挨个枚举所有格子，当枚举到最后一个格子(N^2)的时候即得到答案。O($2^{n^2}$)

对于这种思路我们应该如何处理呢？

```c++
//挨个枚举所有格子，放是一个分支，不放是另一个分支  
void dfs(int x, int y, int s){
    if(y==n) y=0,x++;
    if(x==n){
        if(s==n){
            for(int i=0;i<n;i++) puts(g[i]);
            puts("");
        }
    }
    // 不放皇后
    dfs(x,y+1,s);
    // 放皇后
    if(!row[x] && !col[y] && !dg[x+y] && !udg[x-y+n]){
        g[x][y]='Q';
        row[x] = col[y] = dg[x+y] = udg[x-y+n] = true;
        dfs(x,y+1,s+1);
        row[x] = col[y] = dg[x+y] = udg[x-y+n] = false;
        g[x][y]='.';
    }
    
}
```

### BFS

> 稳重的算法

- 思想

  每次扩展一层，只有当前层全搜索过了之后，才会去搜索下一层，第一次搜到的话一定是最短的

**DFS和BFS对比**

| 搜索方式 | 占用空间 | 数据结构 | 最短路性质                    | 应用场景                         |
| -------- | -------- | -------- | ----------------------------- | -------------------------------- |
| DFS      | O(h)     | stack    | 不可以搜到最短路              | 对空间要求高的                   |
| BFS      | O(2^h)   | queue    | 可以搜到最短路（边权重为1时） | 最小步数，最短距离，最少操作次数 |

- 举例：

走迷宫—最短路问题；dp问题实际上是没有环的最短路

- 基本框架
  - 将初始状态放入队列
  - 只有队列不空便循环:
    - 每次把队头拿出来
    - 扩展队头

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091630296.png" alt="image-20220909163046195" style="zoom: 33%;" />

```c++
//迷宫问题--如何将框架应用起来
#include<queue>
//数据准备与初始化
int g[N][N]; // store the map
int d[N][N]; // store the distance to the start entry,初始化为-1代表没访问过
int dir[5]={-1,0,1,0,-1}
int bfs(){
    queue<int> que;
    que.push({0,0});
    memset(d,-1,sizeof d);
    d[0][0]=0;
    while(que.size()){
        auto cu = que.front();
        for(int i=0;i<4;i++){
            int x = cu.first +dir[i];
            int y = cu.second+dir[i+1];
            if(x<n && x>=0 && y<m && y>=0 && d[x][y]==-1 && g[x][y]==0){
                d[x][y] = d[cu.first][cu.second]+1;
                que.push({x,y});
            }
        }
        
    }
    return d[n-1][m-1];
}
int main(){
    cin>>n>>m;
    
    for(int i=0;i<n;i++)
        for(int j=0;j<m;j++)
            cin>>g[i][j];
    cout<<bfs()<<endl;
    return 0;
}
```

## 第四讲 图论

> 树是一种特殊的图，即树是无环连通图，所以只讲图这个更广泛的数据结构就可以了

### 树和图的存储

**无向图**

边是无方向的，对于每一个无向边，可以建两条边：建一条由a到b的，再建一条由b到a的，因此无向图是一种特殊的有向图，我们只需考虑有向图就可以了

**有向图**

> 有向图有度数的概念：入度和出度

- 邻接矩阵

开个二维数组即可，适合存储稠密图（浪费空间）

- 邻接表

n个点每个点都有一个单链表，存储从该点可以到的所有的点

考虑最坏情况需要开的数组大小（来存单链表），如果是完全无向无图（n个点），每个点都可以到达其他所有点（n-1个边），所以总共要开$n*(n-1)$的大小

插入一条新的边（头插法）

```c++
#include<iostream>
using namespace std;
const int N = 100010,M = N*2;// 如果是树的话可以这么做
int h[N],e[M],ne[M],idx;

void add(int a, int b){
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
} 

void init(){
    memset(h,-1,sizeof h);
}
```

### 树和图的遍历

**深度搜索**

```c++
bool st[N];//存储哪些点已经被遍历过了
// u 表示当前已经遍历到的点
void dfs(int u){
    st[u] = true;//标记一下，这个点已经被搜过了
    for(int i=h[u];i!=-1;i=ne[i]){
        int j = e[i];// 找到i这个索引所对应的图的结点的编号是多少
        if(!st[j]) dfs(j);
    }
}
```

- 实例：树的重心

**宽度搜索**

求从1号点到n号点的最短路，如果无解，输出-1：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209141437414.png" alt="image-20220914143716317" style="zoom:50%;" />

```c++
int bfs(){
    int hh=0,tt=0;
    q[0]=1;
    memset(d,-1,sizeof d);
    d[1]=0;
    while(hh<=tt){
        int t = q[hh++];
        for(int i=h[t];i!=-1;i=ne[i]){
            int j = e[i];
            if(d[j]==-1){
                d[j] = d[t] + 1;
                q[++tt] = j;
            }
        }
    }
    return d[n];
}
```

- 实例：求拓扑序

> 有向无环图才会有拓扑序列，所以也被称为拓扑图，且不一定是唯一的

**求解思路**

所有入度为0的点都可以排在当前最前面的位置；

枚举t的所有出边t->j，删掉t->j，入度d[j]--，如果d[j]==0，就让j入队。

### 最短路

> 难点在于建图，而不在算法的原理上：给你一个背景，如何把问题**抽象**成最短路，侧重于**实现思路**，而不侧重于原理

最短路问题总结见下图：(参考acwing视频)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209141454073.png" alt="image-20220616110720546" style="zoom:50%;" />

#### 单源最短路

> 一个点到其他所有点的最短路

**所有边权都是正数**

> n为点数，m为边数

- **朴素dijkstra算法**：O(n^2)

*如果是稠密图(m ~ n^2)*，尽量使用朴素dijkstra算法，其算法流程如下：

1. **初始化：**dist[1]=0, dist[i] = 正无穷，S=当前已确定最短路的点的集合{}
2. **n次迭代：**
   - for i : 1~n 在不在S中的点中找到最小值
   - 将距离最短的点加入S（基于贪心的思想）
   - 使用t更新其他点的距离

因为是稠密图，所有推荐用邻接矩阵来存，存在重边的话，就只保留距离最短的那条边即可

```c++
#include<cstring>
using namespace std;
const int N = 510;
int n,m;
int g[N][N];
int dist[N];
bool st[N];

int dijkstra(){
    memset(dist,0x3f,sizeof dist);
    dist[1] = 0;
    for(int i=0;i<n;i++){
        int t = -1;
        for(int j=1;j<=n;j++)
            if(!st[j]&&(t==-1||dist[t]>dist[j]))
                t= j;
        st[j] = true;
        for(int j=1;j<=n;j++)
            dist[j] = min(dist[j],dist[t]+g[t][j]);
    }
    
    if(dist[n]==0x3f3f3f3f) return -1;
    return dist[n];
}
int main(){
    scanf("%d%d", &n,&m);
    memset(g, 0x3f, sizeof g);
    while(m--){
        int a,b,c;
        scanf("%d%d%d",&a,&b,&c);
        g[a][b] = min(g[a][b],c);
    }
    int t = dijkstra();
    printf("%d\n",t);
    return 0;
}
```

- **堆优化版的dijkstra算法**：O(mlogn)

如果是稀疏图(m ~ n)，用邻接表来存，那么应该用堆优化版dijkstra算法

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209141540804.png" alt="image-20220914154000699" style="zoom:50%;" />

- 堆

  - 手写堆

  - 优先队列（直接用STL即可，有可能有冗余）

```c++
const int N = 150010;
int h[N],e[N],ne[N],w[N],idx;
typedef pair<int,int> PII;

int dijkstra(){
    memset(dist, 0x3f, sizeof dist);
    dist[1]=0;
    priority_queue<PII,vector<PII>,greater<PII>> heap;
    heap.push({0,1});
    while(heap.size()){
        auto t = heap.top();
        heap.pop();
        int ver = t.second;
        int dis = t.first;
        if(st[ver]) continue;
        st[ver] = true;
        for(int i = h[ver];i!=-1;i=ne[i]){
            int j  = e[i];
            if(dist[j]>dis+w[i]){
                heap.push({dist[j],j});
            }                    
        }
    }
    
    if(dist[n]==0x3f3f3f3f) return -1;
    return dist[n];
}
```



**存在负权边**

> 若能求出最短路，对应路径内中一定没有负权回路

- **Bellman-Fold** ：O(nm)

外层循环n次，内层循环所有边，一个比较朴素的算法。

**实际意义**：迭代k次，dist[]存的是经过**不超过k条边的从1到其他点的最短路距离**，若第n次依然有更新，说明存在负权回路

**应用场景**：有边数限制的最短路（有负环也就无所谓了）

```c++
int bellman_ford(){
    memset(dist,0x3f,sizeof dist);
    for(int i=0;i<k;i++){
        memcpy(backup,dist,sizeof dist);
        for(int j=0;j<m;j++){
            int a = edges[j].a;
            int b = edges[j].b;
            int w = edges[j].w;
            dist[b] = min(dist[b],backup[a]+w);
        }
    }
    
    if(dist[n]>0x3f3f3f3f/2) return -1;
    return dist[n];
}
```

- **SPFA**： 一般O(m) ，最坏O(nm) 

没有负环就可以用SPFA，99%情况下都可以用。

在同一次迭代，dist[b]要想变小，只有可能是dist[a]变小了。因此用一个队列，队列存的是所有变小的结点。只要队列不空，我们就取出队头，再更新t的所有出边。主要思路就是，**更新过谁再拿它来更新别人**。

```c++
int spfa(){
    memset(dist,0x3f, sizeof dist);
    dist[1]=0;
    queue<int>q;
    q.push(1);
    //存的是当前的数是不是在队列当中，防止存重复
    st[1] = true;
    
    while(q.size()){
        int t = q.front();
        q.pop();
        st[t] = false;
        for(int i=h[t];i!=-1;i=ne[i]){
            int j = e[i];
            if(dist[j]>dist[t]+w[i]){
                dist[j] = dist[t] + w[i];
                if(!st[j]) {
                    q.push(j);
                    st[j] = true;
                }
            }
        }
    }
    if(dist[n]==0x3f3f3f3f) return -1;
    return dist[n];
}
```

**spfa求负环**

若cnt[x]>=n，说明存在负环。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209141737291.png" alt="image-20220914173710231" style="zoom:50%;" />

至少有两个点是相同的，意味着路径上存在着一个负环。（一定会变小，不然不会存在这个环）

#### 多源汇总最短路

> 不止一个起点，任选两个点，从其中一个点到另一个点的最短路问题

**Floyd算法**：O(n^3)

采用**动态规划**的思想：d[i,j]存储所有边，floyd算法包含三重循环：

- 第一重：k从1到n
- 第二重：i从1到n
- 第三重：j从1到n:
  - d(i,j) = min(d(i,j), d(i,k) + d(k,j))

### 最小生成树

> 对应的图基本都是无向图

#### Prim算法

**朴素版**：稠密图。O(n^2)

 算法流程和dijkstra算法流程非常类似 。首先把所有距离初始化为正无穷。接下来是n次迭代：

1. 找到集合外距离最近的点。(集合S表示当前已经在连通块中的所有点。)
2. 把t加入到集合中去。
3. 用t更新其他点**到集合的距离**。（最短距离）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151534554.png" alt="image-20220915153409498" style="zoom:50%;" />

选择的点所对应的边(最短的边)加入生成树中去

```c++
#include <cstring>
const int N = 510,INF = 0x3f3f3f3f;
int n,m;
int g[N][N];
int dist[N];
bool st[N];

int prim(){
    memset(dist,0x3f,sizeof dist);
    int res = 0;
    for(int i=0;i<n;i++){
        int t = -1;
        for(int j=1;j<=n;j++){
            if(!st[j]&&(t==-1||dist[t]>dist[j]))
                t=j;
        }

        if(i && dist[t]==INF) return INF;
        if(i) res += dist[t];
        st[t] = true;
        for(int j=1;j<=n;j++){
            dist[j] = min(dist[j],g[t][j]);
        }
    }
    return res;
}

int main(){
    scanf("%d%d",&n,&m);
    memset(g,0x3f,sizeof g);
    while(m--){
        int a,b,c;
        scanf("%d%d%d",&a,&b,&c);
        g[a][b]=g[b][a]=min(g[a][b],c);
    }
    
    int t = prim();
    if(t==INF) puts("impossible");
    else printf("%d\n",t);
}

```

**堆优化版**: 稀疏图。O(mlogn) 一般不会用到。

#### Kruskal算法

O(mlogm)

**经验论**：若是稀释图用kruskal算法，稠密图用Prim算法

1. 将所有边按权重从小到大排序（快排，算法瓶颈O(mlogm)）
2. 枚举每条边，若当前a,b不连通，将这条边加入集合中（并查集,O(m)）

简单而优美的算法

```c++
//只需要把每条边存下来就可以了，不需要用邻接表或邻接矩阵
const int N = 100010;
int n,m;
int p[N];
struct Edge{
    int a,b,w;
    bool operator<(const Edge &W) const{
        return w<W.w;
    }
}edges[N];

int find(x){
    if(x!=p[x]) p[x] = find(p[x]);
    return p[x];
}

int main(){
    scanf("%d%d",&n,&m);
    for(int i=0;i<m;i++){
        int a,b,w;
        scanf("%d%d%d",&a,&b,&w);
        edges[i] = {a,b,w};
    }
    
    sort(edges,edges+m);
    
    for(int i=1;i<=n;i++) p[i]=i;
    int res = 0, cnt = 0;
    for(int i=0;i<m;i++){
        int a = edges[i].a,b = edges[i].b,w=edges[i].w;
        a = find(a),b = find(b);
        if(a!=b){
            res+=w;
            cnt++;
            p[a] =b;
        }
    }
    
    if(cnt<n-1) puts("impossible");
    else printf("%d\n",res);
    
    return 0;
}
```

### 二分图

**概念**：可以将所有点划分到两个集合，使得所有边都是在集合之间的，集合内部没有边。

#### 染色法

给定一个图，判断它是不是二分图。

- 时间复杂度：O(n+m)
- **重要性质**：一个图是二分图当且仅当图中不含奇数环。
- 深度优先遍历：若在染色过程中出现了矛盾，就说明不是二分图

遍历每个点，如果该点未染色，那么深度遍历该点：dfs(i,1)

```c++
const int N = 100010, M = 200010;
int n,m;
int h[N],e[M],ne[M],idx;
int color[N];

void add(int a, int b){
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
}

bool dfs(int u, int c){
    color[u] = c;
    for(int i = h[u];i!=-1;i=ne[i]){
        int j = e[i];
        if(!color[j]) {
            if(!dfs(j,3-c)) return false;
        }
        else if(color[j]==c) return false;
    }
    return true;
}

int main(){
    scanf("%d%d",&n,&m);
    memset(h,-1,sizeof h);
    
    while(m--){
        int a,b;
        scanf("%d%d",&a,&b);
        add(a,b),add(b,a);
    }
    bool flag;
    for(int i=1;i<=n;i++)
        if(!color[i]){
            if(!dfs(i,1)){
                flag = false;
                break;
            }
        }
    if(flag) puts("Yes");
    else puts("No");
}
```

#### 匈牙利算法

- 时间复杂度：O(nm)

- 给定一个二分图，求最大匹配的数量。匹配成功：没有两条边共用一个点

- 存的时候，只需要存左边指向右边的边即可，match存的是右边点当前匹配的点， 

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151704010.png" alt="image-20220915170431922" style="zoom: 50%;" />

```c++
const int N = 510, M= 100010;
int n2,n2,m;
int h[N],e[M],ne[M],idx;
//match存的是右边的点对应的点
int match[N];
//判重，每次不要重复搜一个点
bool st[N];

bool find(int x){
    for(int i=h[x];i!=-1;i=ne[i]){
        int j = e[i];
        if(!st[j]){
            st[j] = true;
            if(!match[j]||find(match[j])){
                match[j] = x;
                return true;
            }
        }
    }    
    return false;       
}


int main(){
    scanf("%d%d%d",&n1,&n2,&m);
    memset(h,-1,sizeof h);
    
    while(m--){
        int a,b;
        scanf("%d%d",&a,&b);
        add(a,b);
    }
    
    int res = 0;
    for(int i=1;i<=n1;i++){
        //表示这些右边的点还没有考虑过（保证每个右边的点只考虑一遍）
        memset(st,false,sizeof st);
        if(find(i)) res ++;
    }
    printf("%d\n",res);
}
```



## 第五讲 数论

### 质数

质数和和数是针对严格大于1的整数定义的，如果只包含1和本身两个约数，就被称为质数，否则为和数。

**质数的判定**

- 试除法 **O(n)**

```c++
bool is_prime(int n){
    if(n<2) return false;
    for(int i=2;i<n;i++)
        if(n%i==0) return false;
    return true;
}
```

我们可以发现n的所有约数都是成对出现的，即若d为n的约束，那么n/d也为n的约数。所以我们不需要枚举所有数，只需要枚举$d<=n/d$的数即可，也就是说d只需枚举到$\sqrt{n}$即可

- 优化 O($\sqrt{n}$)

不推荐使用sqrt(n)这个函数，也不推荐`i*i<=n`的写法，因为可能存在溢出风险，所以推荐写法为：`i<=n/i`

**分解质因数**

- 试除法 O(n)

从小到大尝试n的所有因数，因为每次都会把因子除干净，所以不用担心满足`n%i==0`的数i会是和数。

```c++
void divide(int n){
    for(int i=2;i<n;i++){
        if(n%i==0){ // i 一定是质数
            int cnt = 0;
            while(n%i==0){
                n/=i;
                cnt++;
            }
            printf("%d %d",i,cnt);
        }
    }
}
```

- 优化 最坏O($\sqrt{n}$)

**hints:** n中最多只包含一个大于sqrt(n)的质因子，所以可以枚举所有小于sqrt的质因子，如果最后剩下的因子n，若大于1直接输出即可。

**求从1~n的所有质数**

- 筛法 O(nlogn)

```c++
bool st[N]; //为false代表没有被筛掉
int prime[N];
void get_prime(int n){
    int cnt = 0;
     for(int i=2;i<=n;i++){
         if(!st[i]){
             prime[cnt++]=i;
         }
         for(int j=i+i;j<=n;j+=i) st[j] = true;
     }
}
```

- 优化：埃氏筛法 O(nloglogn)

根据基本分解定理，每个数只需要被素数筛掉就可以了，所有可以把第二个循环放到if里。

- 线性筛法

```c++
void get_primes(int n){
    for(int i=2;i<=n;i++){
        if(!st[i]) primes[cnt++] = i;
        for(int j = 0;primes[j] <= n/i ; j++){
            st[primes[j]*i] = true;
            if(i%primes[j] == 0) break;
        }
    }
}
```

**正确性**证明：

论断：**如果是和数，只会被最小质因子筛掉，且一定能被筛掉**

1. 从小到大枚举所有质数，每一次把当前质数同i的乘积筛掉

2. 第一次出现`i%primes[j] == 0`发生时意味着primes[j]一定是i的最小质因子，那么primes[j]也一定是primes[j]*i的最小质因子

3. 若我们没有枚举到i的任何一个质因子，说明primes[j]一定小于i的所有质因子，所有primes[j]也一定是primes[j]*i的最小质因子

4. 对于一个和数x，假设pj是x的最小质因子，当i（一定会）枚举到x/pj时，就会把x筛掉

线性筛法为什么是**线性的**？

   每个数都只有一个最小质因子，每个数也只用最小质因子来筛，所以每个数只会被筛一次，所以是线性的。不加break就可能重复筛。

### 约数

**求所有约数**

- 试除法+优化

```c++
vector<int> get_divisors(int n){
    vector<int> res;
    for(int i=1;i<=n/2;i++){
        if(n%i == 0){
            res.push_back(i);
            if(i!=n/i) res.push_back(n/i);
        }
    }
    sort(res.begin(),res.end());
    return res;
}
```

**求一个数的约数个数**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206251714848.png" alt="image-20220625171427739" style="zoom:50%;" />

n的每一个约数d就和我们从$\beta_1 到 \beta_k$的一种选法一一对应，所以约数个数为所有选法数量：$(\alpha_1+1)*(\alpha_2+1)*...*(\alpha_{k-1}+1)*(\alpha_k+1)$

```c++

```



**求一个数的约数之和**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206251729585.png" alt="image-20220625172930529" style="zoom:50%;" />

每个约数均不相同，且是选法数量之一。

`while(a--) t = t*p+1`可以方便的求等比数列的和，其中a为指数，p为底数，也可以用公式做（尝试）

**求最大公约数**

> 辗转相除法

核心：**（a，b）= （b，a mod b)**

```c++
int gcd(int a,b){
    return b ? gcd(b,a mod b) : a;
}
```

### 欧拉函数

**概念**：

$\phi(n)$为1~n中与n互质的数的个数

**欧拉公式**：

首先对n分解质因数 $p_1^{\alpha_1}*p_2^{\alpha_2}*...*p_k^{\alpha_k}$，根据公式即可计算出结果$\phi(n) = n*(1-1/p_1)*(1-1/p_2)*...*(1-1/p_k) $

**证明**：

使用容斥原理可以证明

1.  从1~n中去掉$p1,p2,3 $
2.  加上所有$pi*pj$的倍数
3.  减去所有$pi*pj*pk$的倍数
4.  以此类推

>  某些情况下，需要求出1~n中每一个数的欧拉函数

**筛法求欧拉函数 O(n)**

```c++
void get_eulers(int n){
    for(int i=2;i<=n;i++){
        if(!st[i]) {
            primes[cnt++] = i;
            phi[i] = i-1;
        }
        for(int j = 0;primes[j] <= n/i ; j++){
            st[primes[j]*i] = true;
            if(i%primes[j] == 0) {
                phi[primes[j]*i] = primes[j]*phi[i];
                break;
            };
            phi[primes[j]*i] = (primes[j]-1)*phi[i];
        }
    }
}
```

**应用：欧拉定理**

若a与n互质，$a^{\phi(n)}=1(mod(n))$

> 注：等于号代表同余

**欧拉定理的特例：费马小定理**

若n为质数，假设a与n互质，那么有$a^{n-1}=1(mod(n))$

### 快速幂

在O(log k)的情况下快速地求出$a^k mod (p)$的结果。

把$a^k$拆成若干个预处理出来的数的乘积的形式（用2进制表示即可），即把k换成2进制即可。

 ```c++
typedef Long Long LL;
int qmi(int a , int k, int p){
    int res = 1;
    while(k){
        if(k&1) res = (LL)res*a%p;
        k>>=1;
        a=(LL)a*a%p;
    }
}
 ```

**快速幂求乘法逆元**

> 其实本质上就是一个快速幂

**证明：**

希望把除法变乘法：$a/b = a*b^{-1}mod(m)$

左右两边同乘以b：$a = a* b*b^{-1}mod(m)$

因而 $b*b^{-1} = 1 mod(m)$

之后可以用**费马定理**来解决，但**注意m要为质数，且b与m互质**

因而 $b*b^{m-2} = 1 mod(m)$

所以**b的模m的逆元为 $b^{m-2}$**

### 扩展欧几里得算法

**裴蜀定理**

有任意一对正整数a、b，那么一定存在整数x、y，使得ax+by = gcd(a,b)

> 注：x，y可取负整数

**解决什么问题**

为裴蜀定理构造解

```c++
int ext_gcd(int a, int b, int &x, int &y){
     if(!b){
         x = 1,y=0;
         return a;
     }
     int ans = ext_gcd(b,a%b,y,x);
     y -= a/b *x;
     return ans;
}
```

**简单应用：求解线性同余方程 $ax=b$ $mod(m)$**

等价于$ax - my=b$，所以若(a,-m)|b成立则有解，可以用扩展欧几里得算法算出x；否则无解

### 中国剩余定理

> 求解一元线性同余方程组

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206291515290.png" alt="image-20220629151521256" style="zoom: 67%;" />

通解的构造方法：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206291517625.png" alt="image-20220629151745571" style="zoom: 67%;" />

求逆元ti可以用扩展欧几里得算法解出：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206291534519.png" alt="image-20220629153424432" style="zoom: 25%;" />

通解构造的正确性可通过带入法进行验证。

### 高斯消元

> 线性代数相关知识

一般可以再O(n^3)的时间复杂度内，求解n个方程n个未知数的多元线性方程组。

**解的情况**

- 无解：通过行变换推出0=非0，矛盾
- 无穷多组解：0=0，n个未知数，n-1个方程
- 唯一解：完美阶梯形

注：**初等行变换不改变方程组的解，而且可以将方程组变为上三角的形式**：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207041033411.png" alt="image-20220704103319314" style="zoom:50%;" />

**算法流程**

- 枚举每一列，找出绝对值最大的这一行
- 将这行换到上面去
- 将该行第一个数变为1
- 将下面所有行的当前列消成0
- 若有唯一解，那么用消元法即可得到解

```c++
double a[N][N] ;
int gauss(){
    int c,r;
    for(int i=r;i<n;i++){
        
    }
}
```

### 组合数

**公式法**

由公式$C^b_a = \frac{a!}{b!(a-b)!}$，可以计算出组合数：

```c++
int p;
int C(int a, int b){
    int res = 1;
    for(int i=1,j=a;i<=b;i++,j--){
        res = res*j % p;
        res = res *qmi(i,p-2) % p;
    }
    return res;
}
```

**递推式法**

> 10万组询问，a,b范围较小：1<= a,b<=2000

根据**递推式，先预处理出所有的$C^b_a$ 组合数**（两重循环即可），到时候查表即可在O(ab)的时间复杂度下完成计算。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207041132945.png" alt="image-20220704113215891" style="zoom:50%;" />

**预处理公式法**

> 1万组询问，a,b范围适中：1<=a,b<=1e5

根据公式，**预处理出中间的一步:阶乘**。用fact[i]表示i! mod 10^9+7，infact[i]表示$(i!)^{-1}$ mod 10^9+7

也可以用查表的方式O(NlogN)来求$C^b_a = fact[a]* infact[b-a]\%(10^9+7)*infact[b]\%(10^9+7)$ 

**卢卡斯定理**

> 20组询问，a,b范围巨大：1<=a,b<=10^18，1<=p<=10^5

$C^b_a = C^{b\%p}_{a\%p}*C^{b/p}_{a/p} (\%p)$

算法复杂度为O($logN* p*logp$)，前半部分为递归层数，后半部分为每层递归里求组合数的复杂度；

证明可参考该[博客](https://blog.csdn.net/qq_40679299/article/details/80489761)

```c++
int lukas(int a,int b){
    if(a<p && b<p) return C(a,b);
    return (LL)C(a%p,b%p) * lucas(a/p,b/p) % p;
}
```

**分解质因数+高精度**

先把$C^b_a$分解质因数 ，即$p_1^{\alpha_1}*p_2^{\alpha_2}*...*p_k^{\alpha_k}$，之后只需要实现一个高精度乘法即可。

- 对每一个数分解质因数，效率较低O(NlogN)

- 改进：先处理出所有的质因子，再求出这个质因子的次数是多少。由公式$C^b_a = \frac{a!}{b!(a-b)!}$，求p次数的方法为，先看分子里p的个数，再看分母里p的个数，差值即为真正的p的个数。那么如何得到阶乘里p的个数呢？通过下面的算法可以巧妙地计算出阶乘里p的个数：

```c++
// 暴力解法
// 分别求出1~n的质因子p的数量再相加：O(NlogN)
int get(int n, int p){
    for(int i=1;i<=n;i++){
        int t = i;
        while(t%p==0){
           t/=p;
           cnt[i]++;
        }
    }
    int res = 0;
    for(int i=1;i<=n;i++){
        res += cnt[i];
    }
}
// 优化
// 计算出阶乘里p的个数O(logN)
int get(int n, int p){
    int res = 0;
    while(n){
        res += n/p;
        n/=p;
    }
    return res;
}
```

### 卡特兰数

$C_{2n}^{n}-C^{n-1}_{2n}=1/(n+1)*C^n_{2n}$

 **应用**

火车进站问题

合法括号序列问题

### 容斥原理

对于n个圆，计算其所占的面积  $=1-2+3-4+5-...+(-1)^{n-1}*n$，数字代表i个圆的交集的组合。

对于集合来讲，集合的个数也满足上式。

**上式一共有多少项？**

$C_n^0+C_n^1+C_n^2+...+C_n^n=$从n个数里挑任意多个数的方案数$=2^n$

所以一共有$2^n-1$项，所以时间复杂度为$=O(2^n)$

**为什么是+、-、+、-？**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207111517043.png" alt="image-20220711151708971" style="zoom:50%;" />

根据组合恒等式，对于集合中的某个数x，它在k个集合中出现过，它只会在右边的等式里计算一次。

**实际应用**

### 博弈论

**Nim游戏**

给定n堆石子，两位玩家轮流操作，每次操作可以从任意一堆石子中拿走任意数量的石子，最后无法进行操作的人视为失败。

- 先手必胜状态：可以走到某一个必败状态
- 先手必败状态：走不到任何一个必败状态

定理：对于n堆石子(a1,a2,a3,...,an)，如果a1^a2^...^an=0，先手必败，否则先手必胜

**思考题：台阶Nim游戏**

**集合-Nim游戏**

局面（状态）

SG（终点）=0；

SG（x）=mex{SG(y1),SG(y2),...,SG(yk)};

若SG(x)!=0，必胜，否则必败。
