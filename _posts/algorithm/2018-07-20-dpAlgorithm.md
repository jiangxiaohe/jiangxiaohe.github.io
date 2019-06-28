---
layout: post
title: DP算法总结
tags:
- DSA
categories: algorithm
description: 总结DP算法，包括
---


# 动态规划
0. 动态规划程序特点
1. 01背包
2. 完全背包
3. 多重背包
4. 最大连续子序列和
5. 最长不下降子序列LIS longest Increasing Sequence
6. 最长公共子序列LCS longest Common Subsequence
7. 最长回文子串
8. DAG最长路
9. 树状数组
10. 合并石子问题
11. 基于状态转移的dp
12. 差分

参考好文：
[教你彻底学会动态规划——入门篇](https://blog.csdn.net/baidu_28312631/article/details/47418773)
[动态规划详解](https://blog.csdn.net/yuxin6866/article/details/52507623)


# 0. 动态规划概述

最优子问题：子问题的最优解释原问题的最优解

重叠子问题：子问题会重复出现。

无后效性：当前节点记录了历史信息，一旦当前状态确定，就不会再改变，且未来的决策只能在已有的一个或者若干个状态的基础上进行，历史信息只能通过已有的状态去影响未来的决策。

如何设计状态和状态转移方程，是动态规划的核心和最难的部分。

常见的写法有两种，递归写法和递推写法。递归写法也称作记忆式搜索,避免重复计算。而递推写法是从边界出发，通过状态转移方程扩展到整个dp数组。
```cpp
int Fib(int n){
    if(n==0||n==1)return 1;
    if(dp[n]!=-1)return dp[n];//已经计算过
    dp[n]=dp[n-1]+dp[n-2];
    return dp[n];
}
```

# 1. 01背包
> 有编号分别为a,b,c,d,e的五件物品\
它们的重量分别是2,2,6,5,4，它们的价值分别是6,3,5,4,6\
每件物品数量只有一个，现在给你个承重为10的背包\
如何让背包里装入的物品具有最大的价值总和？

dp[i][j]表示只放1..i这i个物品，背包容量为j的01背包容量为j的01背包问题的解

初始化dp[i][0]=0,dp[0][j]=0;

dp[i][j]=max(v[i]+dp[i-1][j-w[i]],dp[i-1][j])

```cpp
int main(){
    //freopen("d:\\input.txt","r",stdin);
    int weight[6]={0,2,2,6,5,4};
    int value[6]={0,6,3,5,4,6};
    int capacity=10;
    //初始化表格，边缘为0
    int F[6][capacity+1];
    for(int i=0;i<=capacity;i++)F[0][i]=0;
    for(int i=0;i<=5;i++)F[i][0]=0;

    for(int i=1;i<=5;i++)
        for(int j=1;j<=capacity;j++){
            if(j>=weight[i])
                F[i][j]=max(F[i-1][j],value[i]+F[i-1][j-weight[i]]);
            else F[i][j]=F[i-1][j];
            //printf("%d %d %d\n",i,j,F[i][j]);
        }

    printf("%d",F[5][capacity]);
    return 0;
}
```
# 2. 完全背包
> 有编号分别为a,b,c,d的四件物品\
它们的重量分别是2,3,4,7\
它们的价值分别是1,3,5,9\
每件物品数量无限个\
现在给你个承重为10的背包\
如何让背包里装入的物品具有最大的价值总和？

dp[i][j]表示只放1..i这i个物品，背包容量为j的01背包容量为j的01背包问题的解

初始化dp[i][0]=0,dp[0][j]=0;

相比01背包问题，只是状态转移方程发生了变化。
dp[i][j]=max(v[i]+dp[i][j-w[i]],dp[i-1][j])

```cpp
#include<iostream>
#include<cstdio>
using namespace std;

int main(){
    //freopen("d:\\input.txt","r",stdin);
    int m=5;//4件物品，秩1,2,3,4,其中0作为初始化值
    int weight[m]={0,2,3,4,7};
    int value[m]={0,1,3,5,9};
    int capacity=11;//背包容量，秩依次为1-10，0为初始化值
    //初始化表格，边缘为0
    int F[m][capacity];
    for(int i=0;i<capacity;i++)F[0][i]=0;
    for(int i=0;i<m;i++)F[i][0]=0;

    for(int i=1;i<m;i++)
        for(int j=1;j<capacity;j++){
            if(j>=weight[i])
                F[i][j]=max(F[i-1][j],value[i]+F[i][j-weight[i]]);
            else F[i][j]=F[i-1][j];
            //printf("%d %d %d\n",i,j,F[i][j]);
        }

    printf("%d",F[m-1][capacity-1]);
    return 0;
}
```
# 3. 多重背包
> 有编号分别为a,b,c的三件物品\
它们的重量分别是1，2，2\
它们的价值分别是6，10，20\
他们的数目分别是10，5，2\
现在给你个承重为 8 的背包\
如何让背包里装入的物品具有最大的价值总和？\
多重背包和01背包、完全背包的区别：
多重背包中每个物品的个数都是给定的，可能不是一个，绝对不是无限个。

思路：多重背包问题有三种解法

第一种是暴力枚举法，基本不用

第二种是转化为01背包，需要更新重量数组和价值数组

第三种是对于每一种物体，01背包是存在两种状态，即存在和不存在，而多重背包是存在n+1中状态，即取0、1、2...n个。然后对于第i件物品，从n+1种状态中去最大值即可


```cpp
#include<iostream>
#include<cstdio>
using namespace std;
//freopen("d:\\input.txt","r",stdin);
int m=4;//3件物品，秩1,2,3,其中0作为初始化值
int weight[m]={0,1,2,2};
int value[m]={0,6,10,20};
int num[m]={0,10,5,2};
int capacity=9;//背包容量，秩依次为1-8，0为初始化值,多重第一种解法时注意次处定义

```

解法1：暴力枚举法，只适用于元素个数较少的情况，因为复杂度为各个元素的数量之积
int F[MAX][MAX][MAX];//各个系数分别表示三件物品的数量

时间复杂度O(a^b*c^d)到到了质数爆炸的时候

这种暴力枚举还有一个缺点就是如果物品种类很多，那么，额外占用的空间和每一件物品写一个for语句的嵌套就太多了，转化为01背包就成了更新二维数组的情况
```cpp
F[0][0][0]=0;
int mmax=0;
for(int a=0;a<=num[1];a++)
    for(int b=0;b<=num[2];b++)
        for(int c=0;c<=num[3];c++){
            F[a][b][c]=0;
            if(a*weight[1]+b*weight[2]+c*weight[3]<capacity){
                F[a][b][c]=a*value[1]+b*value[2]+c*value[3];
            }
            printf("%d %d %d %d\n",a,b,c,F[a][b][c]);
            if(F[a][b][c]>mmax)mmax=F[a][b][c];
        }
```
解法2：转化为一重背包问题，这种方法复杂度稍高
```cpp
int n=0;//物品的总个数，即转化为01背包的矩阵的纵轴
for(int i=1;i<m;i++){
    n+=num[i];
}
int w[n+1],v[n+1];
int k=1;
for(int i=1;i<m;i++){
    for(int j=0;j<num[i];j++){
        w[k]=weight[i];v[k]=value[i];
        //printf("%d %d %d\n",k,w[k],v[k]);
        k++;
    }
}
int F[n+1][capacity];
for(int i=0;i<=n;i++)F[i][0]=0;
for(int i=0;i<capacity;i++)F[0][i]=0;

for(int i=1;i<=n;i++)
    for(int j=1;j<capacity;j++){
        F[i][j]=0;
        if(j>=w[i])F[i][j]=max(F[i-1][j-w[i]]+v[i],F[i-1][j]);
        else F[i][j]=F[i-1][j];
        printf("%d %d %d\n",i,j,F[i][j]);
    }
printf("%d",F[n][capacity-1]);
```
方法3：
第i种物品，有n[i] + 1种状态，即取一件，取两件。。。取n[i]件
（当n[i]大于v / w[i]时永远达不到这一种状态，此处可优化）。
可以通过比较这n种状态的最大值进行迭代递归即可。
当迭代到第一种物品时，使用第一种物品填充背包，
直到背包剩余空间小于第一种物品重量或者第一种物品无剩余时结束迭代。

使用f[i][v]表示从前i种物品中选取部分放入容量为v的背包的最大价值。
则每种物品的n[i] + 1种状态分别表示为f[i - 1][v], f[i - 1][v - w[i]] + p[i],f[i - 1][v - 2 * w[i]] + 2 * p[i]…
其中f[i][v]必然等于n[i] + 1种状态的最大值，即f[i][v] = max(f[i - 1][v], f[i - 1][v - w[i]] + p[i],f[i - 1][v - 2 * w[i]] + 2 * p[i]…)，依次递归，知道i = 0，结束递归。第i种物品的i + 1中状态使用数组表示。
```cpp
int F[m][capacity];
for(int i=0;i<capacity;i++)F[0][i]=0;
for(int i=1;i<m;i++){//共1,2,m-1件物品
    for(int w=0;w<capacity;w++){//最大容量为capacity-1
        //接下来表示对于物品i的num[i]+1中状态
        F[i][w]=0;
        for(int k=0;k<=num[i];k++){
            if(w>=k*weight[i])F[i][w]=max(F[i-1][w-k*weight[i]]+k*value[i],F[i][w]);
        }
    }
}
```
题目：
* 洛谷P1541乌龟棋
# 4. 最大连续子序列和
> 给定数字序列v[0],v[1]...v[n-1]，求i,j,使得v[i]+v[i+1]+...+v[j]最大，并且输出最大值和i,j

dp[i]表示以v[i]结尾的连续子序列的最大和

dp[i]=max(v[i],dp[i-1]+v[i])

备注：用双指针法解最大连续子序列和问题可以在时间复杂度O(n)不变的基础上，将空间复杂度降到O(1)

# 5. 最大不下降子序列LIS
在一个数字序列中，找到一个最长的子序列，可以非连续，使得这个序列是不下降的

方法一：dp[i]表示以v[i]结尾的最长不下降子序列的长度，复杂度O(n*n)

dp[i]=max(1,dp[j]+1),j=1,2..i-1,&&v[j]<=v[i]

```
_for(i,0,n){
    dp[i]=1;
    _for(j,0,i)
        if(v[i]>=v[j])dp[i]=max(dp[i],dp[j]+1);
    _max=max(dp[i],_max);
}
```

方法二：d[k]表示长度为k的不下降子序列末尾元素的最小值,复杂度O(nlogn)

首先考虑新进来一个元素a[i]：如果这个元素大于等于d[len]，直接让d[len+1]=a[i]，然后len++。这个很好理解，当前最长的长度变成了len+1，而且d数组也添加了一个元素。如果这个元素小于d[len]呢？说明它不能接在最后一个后面了。那我们就看一下它该接在谁后面。

准确的说，并不是接在谁后面。而是替换掉谁。因为它接在前面的谁后面都是没有意义的，再接也超不过最长的len，所以是替换掉别人。那么替换掉谁呢？就是替换掉那个最该被它替换的那个。也就是在d数组中第一个大于它的。第一个意味着前面的都小于等于它。假设第一个大于它的是d[j]，说明d[1..j-1]都小于等于它，那么它完全可以接上d[j-1]然后生成一个长度为j的不下降子序列，而且这个子序列比当前的d[j]这个子序列更有潜力（因为这个数比d[j]小）。所以就替换掉它就行了，也就是d[j]=a[i]。其实这个位置也是它唯一能够替换的位置（前面的替了不满足d[k]最小值的定义，后面替换了不满足不下降序列）

至于第一个大于它的怎么找，直接用STL中的upper_bound即可，每次复杂度logn。

# 6. 最长公共子序列
> 给定两个字符串或者数字序列，求一个字符串，使得这个字符串是A和B的最长公共部分（子序列可以不连续）

时间复杂度O(mn)

空间复杂度O(mn)

dp[i][j]表示第一个序列以v[i]结尾，第二个序列以u[j]结尾，两个子序列的最长公共部分的长度

边界：dp[i][0]=0;dp[0][j]=0;
```
if(v[i]==u[j])
    dp[i][j]=dp[i-1][u-1]+1;
else
    dp[i][j]=max(dp[i-1][j],dp[i][j-1]);
```

# 7. 最长回文子串
> 给出一个字符串S，求S的最长回文子串的长度

方法一：

dp[i][j]表示S[i]到S[j]是不是回文子串\
初始化时要初始化dp[i][i]=1;dp[i][i+1]根据情况判断\
然后将字符串的长度从2增加到n，即可递归求得最长回文子串的长度

方法二：

中心元素拓展法，其实和方法一原理是一致的，但是比方法一更优，计算量稍微小点。

方法三：manacher算法，O(n)

```cpp
string longestPalindrome(string s) {
    if(s.size()==0)return s;
    string ss;
    ss.push_back('$');
    ss.push_back('#');
    for(int i=0;i<s.size();i++){
        ss.push_back(s[i]);ss.push_back('#');
    }
    ss.push_back('\0');
    //cout<<ss<<endl;
    int n=ss.size();
    int m=0,mid=0;//记录子串长度最大值，中心，半径
    int dp[n+1];//dp[i]表示也就是以 i 为中心的最长回文半径，原字符串中的长度为dp[i]-1
    dp[0]=1;
    int mx=1,id=0;//mx是右边界，不包含
    for(int i=1;i<n-1;i++){
        if(i<mx)
            dp[i]=min(dp[2*id-i],mx-i);
        else dp[i]=1;
        while(ss[i+dp[i]]==ss[i-dp[i]])dp[i]++;//不用考虑边界条件，因为左边有'$',右边有\0
        if(i+dp[i]>mx){
            mx=i+dp[i];
            id=i;
        }
        //当下面的判断为等号时，取最靠后的最大子串，当为大于号时，取最靠前的最大子串
        if(dp[i]-1>=m){m=dp[i]-1;mid=i;}
    }
    //求得新子串中的中心元素id和最长半径，换算到原子串
    int lo,hi;
    //注意这里substr的第一个参数是开始元素的秩，第二个元素时子串长度
    lo=(mid-dp[mid]+2)/2-1;hi=(mid+dp[mid]-3)/2;

    //printf("mid=%d dp[mid]=%d lo=%d hi=%d\n",mid,dp[mid],lo,hi);
    return s.substr(lo,dp[mid]-1);
}
```
# 8. DAG最长路
> DAG有向无环图中的关键路径

* 问题1：求整个DAG中的最长路径。

逆拓扑排序法：通过拓扑排序计算各个节点的最早开始时间和最晚开始时间，根据两者是否相等判断是否是关键活动，然后DFS即可输出最长路径


dp法：不需要求拓扑排序

建立dp[i]数组，表示从顶点i出发能获得的最长路径，这样所有dp[i]的最大值就是DAG最长路。

那么，如果顶点i的所有后继节点的dp已知，则可求出dp[i]

* 1递归。对整个dp数组初始化为0，如果访问当前节点的出度为0，就会自然返回0，而出度不是0的节点会递归求解，递归过程中计算过的点会被保存且不会被重复计算。

记录路径：类似Dijkstra中的path数组，在判断更新的过程中，更新该节点的后继节点。如果有多条DAG最长路，要求解字典序最小的，只需要将邻接表按照字典序排列即可。

```cpp
int DP(int i){//计算从i点出发的最长路径
    if(dp[i]>0)return dp[i];
    _for(j,0,grid[i].size()){
        int next=grid[i][j].id,len=grid[i][j].len;
        int t=DP[next];
        if(t+len>dp[i]){
            dp[i]=t+len;
            path[i]=next;
        }
    }
    return dp[i];
}
```

* 问题2：固定终点，求DAG的最长路径
和问题1的区别在于边界，问题1中没有固定终点，所以所有出度为0的点都是边界，而这个问题中只有固定终点dp[T]=0。

如果把其他出度为0的节点初始化dp[]为0的话，会出现解答错误，得到的结果可能终点不是T。真确的做法是令dp初始化为-inf。另外，增加vis数组表明该节点dp是否计算过

```cpp
int DP(int i){//计算从i点出发的最长路径
    if(vis[i])return dp[i];
    vis[i]=true;
    _for(j,0,grid[i].size()){
        int next=grid[i][j].id,len=grid[i][j].len;
        int t=DP[next];
        if(t+len>dp[i]){
            dp[i]=t+len;
            path[i]=next;
        }
    }
    return dp[i];
}
```

# 9 树状数组
> 已知一个数列，你需要进行下面两种操作：
1.将某一个数加上x
2.求出某区间每一个数的和

此问题用裸树状数组即可。

> 已知一个数列，你需要进行下面两种操作：
1.将某区间每一个数数加上x
2.求出某一个数的值

此问题用树状数组+差分[模板](https://blog.csdn.net/zp1ng/article/details/77860179)

树状数组是一个动态维护前缀和的数据结构，可以在O(logn)的时间内计算区间和、修改单点的值。

# 10 合并石子问题
石子合并问题是最经典的DP问题。首先它有如下3种题型：
1. 有N堆石子，现要将石子有序的合并成一堆，规定如下：每次只能移动任意的2堆石子合并，合并花费为新合成的一堆石子的数量。求将这N堆石子合并成一堆的总花费最小（或最大）。
2. 有N堆石子，现要将石子有序的合并成一堆，规定如下：每次只能移动相邻的2堆石子合并，合并花费为新合成的一堆石子的数量。求将这N堆石子合并成一堆的总花费最小（或最大）。
3. 问题(2)的是在石子排列是直线情况下的解法，如果把石子改为环形排列，又怎么做呢？

* 问题1是哈夫曼树的变形，具体见贪心算法部分。
* 问题2是石子问题，状态转移方程为：可用平行四边形优化至复杂度
`dp[i][j]`表示将第i堆到第j堆石子合并起来所耗费的能量，显然dp[i][i]=0;
$$dp[i][j]=min\{ dp[i][k]+dp[k+1][j]+sum[i][j] \}$$
用`dpk[i][j]`表示dp[i][j]选出的最佳k值，由平行四边形优化可得
$$  dp[i][j-1]<=dpk[i][j]<=dpk[i+1][j]$$

# 11 基于状态转移的dp
CCF201312-4 有趣的数

# 12 差分
[未完待续](https://zhuanlan.zhihu.com/p/33336151)
> 即维护的数据是序列中两数之差。这样可以令区间内各个数字分别加给定值的操作变得简单。区间加等差也变成了差分数组中加相等的数。
