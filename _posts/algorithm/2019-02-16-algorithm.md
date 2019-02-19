---
layout: post
title: 《算法笔记》笔记
tags:
- DSA
categories: algorithm
description: 整理笔记，复习常考算法
---

# PAT考试简介：

* PAT对输出要求很高，要注意输出的不同情况，多测试。不要急于写代码，要多思考几分钟，想清楚了再写。
* 最好就是一次AC，一次不过的看看题意是不是理解错了，一次不过很难找出
* 理解并掌握基础数据结构，包括：线性表、树、图；
* 理解并熟练编程实现经典高级算法，包括哈希映射、并查集、最短路径、拓扑排序、关键路径、贪心、深度优先搜索、广度优先搜索、回溯剪枝等；

# 考前易错点总结：

1. 节点的取值范围是0-9999，是四位数，但是输出的时候没有用%04d导致出错
2. 用正负来表示性别，要注意到+-0的处理
3. 给定的图有可能不连通
4. 要特别注意题目要求，尤其是输出格式
5. 多项式运算，有两项指数相同
6. 输出数组的秩、还是输出数组中对应位置的值
7. 排序名次默认1,2,2,2,5
8. 关于日期的处理中间过程均用最小单位秒或者天，天的话一天一天加
9. 字符的个数为标准三位时，优先用进制法散列，字符串不确定时用map<string,int>散列
10. 可能超范围，尽量用longlong
11. 数组都用全局变量
12. 大整数运算、科学计数法运算等记得去掉前导0
13. 尽量多用函数来使流程清晰
14. 只计算连通域的话尽量用并查集或者BFS来写，防止DFS栈溢出
15. buildTree不返回值导致段错误，PAT考试30分的教训啊
16. 写程序前充分理解题意，手算算例，和答案一致了再开始做，避免做无用功

# 重点模板回顾：

1. 第K大问题
2. 树状数组
3. Dijkstra
4. 并查集
5. 二叉树插入算法
6. AVL树的插入算法
7. DFS回溯
8. 分数运算模板
9. 二分模板，大于等于和大于，lowbound
10. 排序题目不同数据结构如何自定义排序
11. 堆排序上滤下滤

# 4入门篇：算法初步
# 4.1 排序
* 基本算法模板背诵，选择、插入、堆、快速、归并排序
* PAT考试排序基本套路：题目也许很复杂，需要用结构体存入数据，数据量较少的时候用结构体数组，但是用数组存放结构体的指针更好，成本更小。PAT考试考排序就是考复杂的排序逻辑，然后让你根据题意写cmp函数。

排序算法性能比较|稳定性|是否就地|输入敏感|复杂度|备注
-|-|-|-|-|-
插入排序|稳定|就地|输入敏感|最小n最大n^2|
选择排序|不稳定|就地|输入敏感|最小n最大n^2|
堆排序|不稳定|就地|输入不敏感|nlogn|
快速排序|不稳定|就地|输入敏感|最好nlogn最大n^2平均nlogn|
归并排序|稳定|非就地|输入不敏感|nlogn|

* 有地方说用链表实现的插入排序是稳定的，因为并没有交换产生，将选择的元素插入到原序列，而一般而言的说的选择排序时基于向量实现的，找到最大元素和未排序序列的首元素交换位置，显然，是非稳定的。
* 快速排序过程中，寻找中轴点的过程交换是非稳定的

# 4.2 散列
* 做题套路：基本考察点就是用256数组存储各个字符出现的次数，判断各个字符是否有效。用set去重，用map计数，基本就这三种套路
1. 数据不大时，直接把输入的数当做数组下标来对这个数据的性质进行统计，是很实用的以空间换时间的方法
2. hash开放定址法：线性试探法试探H(key)+1。平方试探法试探H(key)+1,H(key)-1,H(key)+4,H(key)-4。注意有些题目说明了平方试探发只试探一边。封闭定址法有：链地址法。
3. 字符串hash：A-Z映射为0-25,一个字符串看做26进制数。注意如果前三位字母，后一位数字，可以把各位的权值分别设置为26、26、26、10，即非标准的进制法。BCD4，可以看做前三位的731和后面的4拼接，即7314

# 4.3 递归
1. 全排列。其实这个更是DFS的应用，应熟记此方法。具体见全排列总结
2. **n皇后**。相比邓老师书中用试探回溯法，自己模拟栈的情况，然后不断的试探+回溯，不如类比为全排列（根据每行每列只能放一个数，用数组下标表示行，数组中的元素表示列，或者相反，即把每一个全排列和每一种n皇后的分布对应起来），然后验证该排列是否满足n皇后的对角线不相等即可。全排列的实现和DFS法一致，步进递归。当然，对于n皇后的特殊性，比如前三个元素已经选定但是冲突时，就应该避免继续递归而即使break掉。这个用函数递归系统栈实现的方法和邓老师书中自建栈实现的方法原理是一样的，但是自建栈明显要繁琐很多，也不好调试。所以，DFS全排列法解n皇后更佳。

# 4.4 贪心
0. 解题没什么套路，多花点时间思考贪心策略的正确性，如果策略错了，很难检查出来错误。如果程序运行没错，但是答案不对，很可能就是贪心策略不对
1. 区间贪心：设定左边界，按照右端点从小到大排序，右端点相同的选左端点大的（应对重叠区间）

# 4.5 二分
0. 解题套路：熟练写二分函数，mid=(lo+hi)/2的取值范围是[lo,hi),注意此取值范围正确写上下界
1. mid=(lo+hi)/2，如果lo+hi会越界，则用mid=(hi-lo)/2+lo等价语句
2. 最常用的是[lo,hi)区间查找大于等于e的最小元素，或查找大于e的最小元素，两者的区别只是把mid>=e和mid>e
3. 二分法的题目都可以归结为这样一个问题：寻找序列中第一个满足某条件的元素的位置
4. 求sqrt(2)
5. 装水问题：在半圆R里面装水，求水的高度h，使得水的面积/半圆的面积=r。显然，高度h和水的面积是单调的，这样就可以二分来做，逼近正确答案
6. 木棒切割问题：给出N根木棒，长度均已知，切割成K段长度相同的木棒（必须为整数），则每段的长度最大是多少？显然，切割后木棒越长，则能切割的根数越少，二分法去逼近木棒的长度，计算该长度下能够切割的根数，问题就变成了切割的根数>=K时，木棒长度的最大值。
7. 快速幂：计算a^b%m，如果b达到了10^18，显然，递归相乘方法是不可取的。应采取快速幂方法。快速幂方法基于二分法，也称为二分幂。b为奇数时，f(b)=a*f(b/2)^2;b为偶数时，f(b)=f(b/2)^2.
8. 快速幂的迭代写法：将b写为二进制，那么b就可以写成若干次二次幂之和，比如b=13时，13=8+4+1.可行的方法是每次判断b的末尾是否为1，为1加上该位置对应的二次幂，然后更新下一个位置的二次幂，b右移一位


```cpp
//大于等于e最小元素
int lower_bound(vector<int>& vec,int lo,int hi,int e){
	int mid;
	while(lo<hi){
		mid=(lo+hi)/2;
		if(vec[mid]>=e)hi=mid;
		else lo=mid+1;
	}
	return lo;
}
//大于e的最小元素
int upper_bound(vector<int>& vec,int lo,int hi,int e){
	int mid;
	while(lo<hi){
		mid=(lo+hi)/2;
		if(vec[mid]>e)hi=e;
		else lo=mid+1;
	}
	return lo;
}
//伪代码
int solve(int lo,int hi){
	int mid;
	while(lo<hi){
		mid=(lo+hi)/2;
		if(条件成立)hi=mid;
		else lo=mid+1;
	}
	return lo;//这里lo==hi,所以返回值是谁是一样的。
}
//小于等于e的最大元素
//小于e的最大元素
//计算sqrt(2)
double sqrt(){
	double lo=1.0,hi=2.0,mid;
	while(hi-lo>1e-10){
		mid=(lo+hi)/2;
		if(mid*mid>2)hi=mid;
		else lo=mid;
	}
	return mid;
}
//快速幂递归写法
LL binPow(LL a,LL b,LL m){
	if(b==0)return 1;
	if(b%2)//这里可以用b&1代替
		return a*binPow(a,b-1,m)%m;
	else{
		LL tmp=binPow(a,b/2,m);
		return tmp*tmp%m;
	}
}
//快速幂迭代写法
LL binPow(LL a,LL b,LL m){
	if(b==0)return 1;
    LL ans=1,tmp=a;
    while(b>0){
        if (b % 2==1) {
			ans * = tmp;
		}
		b /= 2;
		tmp = tmp * tmp;
    }
}
```

# 4.6 双指针
0. 套路：
1. 给定递增序列，找两个数使得其和为定值m。暴力枚举O(n^2)，更简单的是双指针，初始时i=0,j=n-1；如果vec[i]+vec[j]>m,j--使和变小，vec[i]+vec[j]<m,i++，使和变大，相等时即找到一种方案，令i++,j--继续寻找下一组方案
2. 归并排序的merge算法也是双指针问题
3. 快速排序确定轴点的算法，邓老师书中的也属于双指针版本

# 4.7 随即选择算法：第K大问题
1.仿照快排的轴点选择算法，在[lo,hi]插入的位置p即为数组的第m=p-lo+1大的数，如果m==k,则找到了第k大的数，如果，m>k，则继续在左侧区间找第k大的数，否则，在右侧区间找第k-m大的数。此方法的最坏时间复杂度是O(n^2)，期望复杂度O(n)

```cpp
//[lo,hi]勤于扩展、懒于交换，全部元素相同时是最坏情况
int partition(vector<int>& vec,int lo,int hi){
	swap(vec[lo],vec[rand()%(hi-lo+1)+lo]);
	int tmp=vec[lo];
	while(lo<hi){
		while(lo<hi&&vec[hi]>=tmp)hi--;
		vec[lo]=vec[hi];
		while(lo<hi&&vec[lo]<=tmp)lo++;
		vec[hi]=vec[lo];
	}
	vec[lo]=tmp;
	return lo;
}
int randSelect(vector<int>& vec,int lo,int hi,int k){
	if(lo==hi)return vec[lo];
	int p=partition(vec,lo,hi);
	//_for(i,lo,p)if(vec[i]>vec[p])printf("%derror\n",i);
	//_for(i,p,hi+1)if(vec[i]<vec[p])printf("%derror\n",i);
	int m=p-lo+1;
	if(k==m)return vec[p];
	if(k>m)
		return randSelect(vec,p+1,hi,k-m);
	else
		return randSelect(vec,lo,p-1,k);
}
```

# 5 入门篇：数学问题
0. 套路：熟悉常见题目
1. 该部分不涉及很深的算法，却与数学息息相关，不需要特别的数学知识，只要掌握简单的数理逻辑即可。
2. 最大公约数`int gcd(int a,int b){return !b?a:gcd(b,a%b);}`这段代码十分简洁，并且实现了如果a<b,，先互换。辗转相除法也称欧几里得算法。
3. 最小公倍数，在最大公约数的基础上计算，为ab/gcd(a,b)
# 5.4 分数的四则运算
0. 套路：实际做题过程中，还是套晴神模板最好，不需要单独设符号位，符号位在分子上即可，每次运算后化简。
1. 建立分数结构类，实现加减乘除运算，符号位不用单独处理，计算时先不管分母正负，处理结果时如果分母为负，令分子分母同时取相反数即可，每次运算结束需要进行约分(注意分子不为0时除以gcd，为0时令分母为1)
```cpp
int gcd(int a,int b){return b==0?a:gcd(b,a%b);}
struct Fraction{
	int up,down;
	Fraction(){}
	Fraction(int a,int b):up(a),down(b){}
};
Fraction reduction(Fraction result){
	if(result.down<0){result.up=-result.up;result.down=-result.down;}
	if(result.up==0)result.down=1;
	else{
		int d=gcd(abs(result.up),result.down);
		result.up/=d;
		result.down/=d;
	}
	return result;
}
Fraction add(Fraction f1,Fraction f2){
	Fraction result;
	result.up=f1.up*f2.down+f2.up*f1.down;
	result.down=f1.down*f2.down;
	return reduction(result);
}
Fraction minu(Fraction f1,Fraction f2){
	f2.up=-f2.up;
	return add(f1,f2);
}
Fraction multi(Fraction f1,Fraction f2){
	Fraction result;
	result.up=f1.up*f2.up;
	result.down=f1.down*f2.down;
	return reduction(result);
}
Fraction divide(Fraction f1,Fraction f2){
	swap(f2.up,f2.down);
	return multi(f1,f2);
}
```
