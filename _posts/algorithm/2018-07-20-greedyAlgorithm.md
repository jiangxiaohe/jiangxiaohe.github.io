---
layout: post
title: 贪心算法总结
tags:
- DSA
categories: algorithm
description: 总结贪心算法，包括
---

# 贪心算法总结
0. 贪心概述
1. 区间不相交问题
2. 哈夫曼编码树
3. 洛谷合并果子问题

# 贪心概述

贪心的想法是通过局部最优解来推导全局最优解

# 区间不相交问题

给定N个开区间，从中选择尽可能多的开区间，使得这些开区间两两没有交集

策略：先把包含的区间删除大的，保留小的。然后对于剩下的所有区间，不存在包含关系。所以肯定是相交或者相离。将所有将所有的区间按照右端点排列（因为没有包含的情况，所以，按照左端点排列的情况是完全一样的），这时从右向左或者从左向右选依次选区间即可，记得选了一个区间之后，要更新剩下的有效区间。

上述方法得首先去除包含区间，不去除的也可以，还是按照右端点排序，按照左端点最大来选取。或者按照左端点排序，按照右端点最小来选取。

* LeetCode435. 无重叠区间

```cpp
//注意这里排序的定义，首先选择的是左端点最大元素，左端点元素相同的，选择右端点小的元素
//或者首先选择右端点最小的元素，右端点相同时，选择左端点最大的元素
static bool cmp(Interval& a,Interval& b){
	if(a.start!=b.start)return a.start>b.start;
	else return a.end<b.end;
}
int eraseOverlapIntervals(vector<Interval>& intervals) {
	int right=0x7fffffff;
	int sum=0;
	sort(intervals.begin(),intervals.end(),cmp);
	for(int i=0;i<intervals.size();i++){
		if(intervals[i].end<=right){
			sum++;right=intervals[i].start;
		}
	}
	return intervals.size()-sum;
}
```

# 哈夫曼编码树

1. 规则：
	1. 规定哈弗曼树的左子树编码为0，右子树编码为1；
	2. 若两个字符权值相同，则ASCII码值小的字符为左孩子，大的为右孩子；
	3. 创建的新节点所代表的字符与它的左孩子的字符相同；
	4. 所有字符为ASCII码表上32-96之间的字符（即“ ”到“`”之间的字符）

```cpp
 /*本题看似简单，实现哈夫曼编码书即可，但是还是折腾近三个小时，需要注意以下三点：
 priority_queue自定义结构体的写法，结构体中定义友元函数，或者重载<，但是一定要写上两个const和引用
 由于结构体存在指针，出队列的元素应给其分配永久存储空间new，而不是将指针指向局部变量
 由于多次输入输出，所以，将特殊类型如结构体，队列，节点都定义为局部变量，每次循环重新定义即可
 */
 struct node{
     //这个题目被卡主的地方就是优先级队列中结构体的写法，const和引用都得写,
     char c;
     int f;
     node* lchild;
     node* rchild;
     node(char cc='a',int ff='0',node* l=NULL,node* r=NULL):c(cc),f(ff),lchild(l),rchild(r){}
     //这里要加上const 和引用，否则报错，这两条是priority-queue的特殊规则了吧，暂时先不深究了，先记住。结构体优先级队列必备知识
     //node(const node& b):c(b.c),f(b.f),lchild(b.lchild),rchild(b.rchild){}
     //这里需要注意写法，friend不能省，不能只写引用，要么引用和const都不写，要么都写
     friend bool operator<(const node& a,const node& b){
         return a.f>b.f||(a.f==b.f&&a.c>b.c);
     }
     //或者时下面这种写法，两个const不能省
     /*bool operator < (const node& b)const{
         return f>b.f||(f==b.f&&c>b.c);
     }*/
 };

 void travel(node* root,string s){
     if(root==NULL)return;
     travel(root->lchild,s+"0");
     if(root->lchild==NULL&&root->rchild==NULL){
         cout<<root->c<<":"<<s<<endl;
     }
     travel(root->rchild,s+"1");
 }
 int main(){
     freopen("d:\\input.txt","r",stdin);
     int n;
     char c[2];int m;
     while(1){
         //这里要注意，把特殊变量每次需要清空的定义为局部变量，就可以自动清空了
         node tt;
         priority_queue<node> pq;
         string s;
         if(scanf("%d",&n)==EOF)break;
         for(int i=0;i<n;i++){
             scanf("%s %d",c,&m);
             tt.c=c[0];tt.f=m;
             pq.push(tt);
         }

         node* nodet;
         while(pq.size()!=1){
             //原来第二次遇到的问题在这里，notet的元素指向a，b的指针，而在下一次循环中，a，b的值发生了变化，指针的指向也变化，原因在于指针指向的是一个临时变量
             //出队的元素不再入队，应建立永久变量
             node* a=new node(pq.top());
             pq.pop();
             node* b=new node(pq.top());
             pq.pop();
             //千万注意，这里的指针不能指向临时变量
             nodet=new node(a->c,a->f+b->f,a,b);
             pq.push(*nodet);
         }
         tt=pq.top();
         pq.pop();
         travel(&tt,s);
     }

     return 0;
 }
```

# 洛谷合并果子

在一个果园里，多多已经将所有的果子打了下来，而且按果子的不同种类分成了不同的堆。多多决定把所有的果子合成一堆。

每一次合并，多多可以把两堆果子合并到一起，消耗的体力等于两堆果子的重量之和。可以看出，所有的果子经过 n-1n−1 次合并之后， 就只剩下一堆了。多多在合并果子时总共消耗的体力等于每次合并所耗体力之和。

因为还要花大力气把这些果子搬回家，所以多多在合并果子时要尽可能地节省体力。假定每个果子重量都为 11，并且已知果子的种类 数和每种果子的数目，你的任务是设计出合并的次序方案，使多多耗费的体力最少，并输出这个最小的体力耗费值。

此题类似哈夫曼编码树，贪心求解，每次合并重量最小的两堆。（与合并果子问题区别的是合并石子问题，只能合并相邻的两堆，局部最优解并不是全局最优解，贪心失效，是DP问题）
