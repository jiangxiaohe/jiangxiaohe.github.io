---
layout: post
title: 算法笔记
tags:
- DSA
categories: algorithm
description: 内容包括《算法笔记》一书的重点，以及其他算法的总结。
---

# PAT考试：

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
17. 特别注意输入的多样性，如果有一个算例不过，很可能是输入的特殊值自己没有考虑到，比如输入1
18. 考前再熟悉下STL库，把第六章内容再巩固

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

[TOC]

# 2输入输出

* 补充：重载输入输出流操作符

```cpp
ostream& operator<<(ostream& out,vector<int>& vec){
	out<<vec[0];
	_for(i,1,vec.size())out<<" "<<vec[i];
	out<<endl;
	return out;
}
```

* stringstream将一行数据一个一个的输入到string中

```cpp
getline(cin,strline);
stringstream ss(strline);
string str;
ss>>str;
```

* sscanf、sprintf用法

比如：char a[100]="123";sscanf(a,"%d",&n);

再比如：int n=123;char a[100];sprintf(a,"%s\n",n);

当然了，也可以用于格式化文件读写，比如：`FILE* fp=fopen("d:\\codeBlockSpace\\output.txt","w"); for(int i=0;i<10;i++)fprintf(fp,"%d %d\n",i,i+3);`

将一个文件的内容复制到另一个文件，while(scanf("%s",a)!=EOF)这时会将空格和回车省去不复制。也可以用fscanf(fp,"%s",a)

* scanf\printf用法

```cpp
%m.nf    %md    %-md    %0md    %lld    %lf
scanf("%lld",&a);printf("%lld",a);
用printf输出string用str.c_str()即可
输入整行
char a[100];getline(a);
string a;getline(cin,str1);
```

* getchar、putchar、gets、puts用法


* getchar()和putchar(c)用来输入输出单个字符
* gets(&a)用来输入一行字符串，gets识别换行符作为结束标志，因此scanf输入一个完整数组后，应该先用getchar吸收整数后的换行符在使用gets。
* puts(&a)输出数组信息并自动添加一个换行

* C中在标准C语言中，getline函数是不存在的。下面是一个简单的实现方式：

```
int getline_(char s[],int lim){
    int c,i;
    i=0;
    while((c=getchar())!=EOF&&c!='\n'&&i<lim-1)
        s[i++]=c;
    s[i]='\0';
    return i;
}
```

* 判断输入输出结束

```cpp
注意，scanf的返回值是读取到的变量的个数
string a, b; char c; while (cin >> a >> b >> c){}
while (scanf("%s %s %c", &a, &b, &c) != EOF){}
```

* 重定向

```
freopen("d:\\input.txt","r",stdin);
freopen("d:\\output.txt","w",stdout);
```

* 直接从文件读数据

```
//C
#include"stdio.h"
FILE* fp=fopen("D:\\test.txt","r");
char buffer[50];
fgets(buffer,50,fp);
sscanf(buffer,"%d %d",&m,&n);
fgets(buffer,50,fp);
sscanf(buffer,"%d %d",num1+i,num2+i);
fclose(fp);

//C++
#include <fstream>
#include <iostream>
#include <sstream>
ifstream fp("D:\\data.txt");
string buffer;
stringstream ss;
getline(fp,buffer);ss<<buffer;ss>>n;cout<<n<<endl;
getline(fp,buffer);
cout<<buffer<<endl;
```

# 3小技巧&基础知识

* 四种基本数据类型

```cpp
整形int	longlong	%d	%lld	%md  %0md	%.md
浮点型float\double  %f   %lf
字符型char
布尔型bool
```



* 用order数组记录结构体秩并移动
存储结构体数组并且涉及到数组的移动删除操作时，如果结构体空间较大，则用order[]数据记录各元素的位置。即用vector<node>存储数组，用vector[order[i]]按顺序访问数组，这样移动和删除操作都是对int类型进行处理了。
* 判断是否能被2整除时，可以直接用位运算&1



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
* 运用sort函数进行排序时，要编写cmp函数。比如对学生排序，如果成绩不同，那么分数高的排在前面，否则，将姓名字典序小的排在前面。

```cpp
bool cmp(Student a,Student b){
	if(a.score != b.score)return a.score>b.score;
	else return strcmp(a.name,b.name)<0;//注意这里strcmp函数
}
```

# 4.2 散列
* 做题套路：基本考察点就是用256数组存储各个字符出现的次数，判断各个字符是否有效。用set去重，用map计数，基本就这三种套路
1. 数据不大时，直接把输入的数当做数组下标来对这个数据的性质进行统计，是很实用的以空间换时间的方法
2. hash开放定址法：线性试探法试探H(key)+1。平方试探法试探H(key)+1,H(key)-1,H(key)+4,H(key)-4。注意有些题目说明了平方试探发只试探一边。封闭定址法有：链地址法。
3. 字符串hash：A-Z映射为0-25,一个字符串看做26进制数。注意如果前三位字母，后一位数字，可以把各位的权值分别设置为26、26、26、10，即非标准的进制法。BCD4，可以看做前三位的731和后面的4拼接，即7314.
4. 如果把进制设置为10^7级别的素数（比如10000019），mod设置为10^9的素数（比如1000000007），实践证明，很难发生冲突。

* 散列函数

1. 除余法`hash(key)=key mod M`
2. MAD法：除余法中0的hash值始终为0，不管hashtable多大，而且相邻关键码的散列值相同，随机性不强。改进为`hash(key)=(a*key+b) mod M`

* 冲突解决方案

无论散列函数设计多么巧妙，冲突是不可避免的。

封闭定址  开散列

1. 每一个词条的key值不变
2. 多槽位法：即在每一个桶都细分为固定更小的槽位，每一个组槽位数固定，用向量或链表实现
3. 独立连法：和多槽位的不同就是向量中存放的是列表的指针，所有冲突的词条都在该列表上
4. 公共溢出区法：将冲突的词条放入另一个存放冲突的公共缓冲区

封闭定址  闭散列

1. 每一个词条的key值可变，仅仅依靠基本的散列表结构，就地排解冲突
2. 线性试探(linear probing)：冲突的时候就找后面没词条的位置放。注意这里面有一个惰性删除(即标记为之前存过元素)，以防止其后面的冲突元素在查找的时候无法被访问
3. 平方试探(quadratic probing)：按照如下规则确定第j次试探的位置：`(hash(key)+j^2) mod M`
	- 有的地方说平方试探是两个方向，即`(hash(key)-j^2) mod M`，看题目是要清楚，看看是哪个方向，还是两个方向交错
	- 单向试探时，若M为质数，且装填因子小于50%，则平方试探必然可以找到空桶
	- 可以证明：如果j从0-(M-1)都无法找到位置，那么，就不可能找到位置，即循环节为M。此结论易证明。


# 4.3 递归
1. 全排列。其实这个更是DFS的应用，应熟记此方法。
2. **n皇后**。相比邓老师书中用试探回溯法，自己模拟栈的情况，然后不断的试探+回溯，不如类比为全排列（根据每行每列只能放一个数，用数组下标表示行，数组中的元素表示列，或者相反，即把每一个全排列和每一种n皇后的分布对应起来），然后验证该排列是否满足n皇后的对角线不相等即可。全排列的实现和DFS法一致，步进递归。当然，对于n皇后的特殊性，比如前三个元素已经选定但是冲突时，就应该避免继续递归而即使break掉。这个用函数递归系统栈实现的方法和邓老师书中自建栈实现的方法原理是一样的，但是自建栈明显要繁琐很多，也不好调试。所以，DFS回溯法解n皇后更佳。

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
9. fib数列也可以用快速幂解法，在O(logn)的时间内求出F(n)，关键是推导出两项与两项之间的矩阵形式。


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

# 5.2 常用math函数
* fabs(double x)
* floor(double x)向下取整
* ceil(double x)向上取整
* pow(double r,double p)返回r^p
* sqrt(double x)
* log(double x)返回double型变量的以自然对数为底的对数
* sin(double x)、cos(double x)、tan(double x)
* pi=acos(-1)
* asin(double x)、acos(double x)、atan(double x)
* round(double x)四舍五入、返回类型也是double

# 5.3 日期处理

```cpp
int mon[2][13]={0,31,28,31,30,31,30,31,31,30,31,30,31,\
				0,31,29,31,30,31,30,31,31,30,31,30,31};
#define is_p(x) ((x%400==0||(x%100!=0&&x%4==0))?1:0)
struct today{
	int year,month,day,week;
	today(int y,int m,int d,int w):year(y),month(m),day(d),week(w){}
	void operator++(){
		day++;
		week++;
		if(week==8)week=1;
		int flag=is_p(year);
		if(day==mon[flag][month]+1){
			day=1;
			month++;
			//cout<<month<<" "<<day<<endl;
			if(month==13){
				month=1;year++;
			}
		}
	}
};
```

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

# 5.5 质数、质因子分解
0. 套路：大量质数时素数筛法，少量时用is_prime函数
1. is_prime函数O(n^1/2)或者用数组记录过程结果的筛素数法O(nloglogn)。
2. 对于质因子分解，首先看因子分解，如果一个数存在除1和它本身之外的其他因子，那么必定是在sqrt(n)两侧成对出现的。对于质因子而言，可以得到，要么所有的质因子都小于sqrt(n)，要么只有一个质因子大于sqrt(n)，所以，质因子分解可以先判断2-sqrt(n)之间的所有质数是否是其质因子，最后再判断除去这些质因子后是否为1，否的话说明这是一个比sqrt(n)大的质因子。

```cpp
bool is_prime(int n){
	if(n<=1)return false;
	for(long long i=2;i*i<=n;i++){
		if(n%i==0)return false;
	}
	return true;
}
```

# 5.6 高精度
0. 套路：写的多了，问题就少了
1. 实现大整数类，包括运算：高精度加减法、高精度与低精度的乘除法。

```cpp
struct bign{
	int d[1000],len;
	bign():len(0){}
	bign(char* str,int n):len(n){
		for(int i=n-1;i>=0;i--)
			d[n-i-1]=str[i]-'0';
	}
};
int cmp(bign& f1,bign& f2){
	if(f1.len!=f2.len){
		if(f1.len>f2.len)return 1;
		else return -1;
	}else{
		for(int i=f1.len-1;i>=0;i--){
			if(f1[i]!=f2[i]){
				if(f1[i]>f2[2])return 1;
				else return -1;
			}
		}
		return 0;
	}
}
//加法
bign add(bign& f1,bign& f2){
	bign result;
	int carry=0;
	for(int i=0;i<a.len||i<b.len;i++){
		int tmp=a[i]+b[i]+carry;
		result[result.len++]=tmp%10;
		carry=tmp/10;
	}
	if(carry!=0)result[result.len++]=carry;
	return result;
}
//减法a-b，从低位到高位，要借位
bign sub(bign a,bign b){
	bign c;
	for(int i=0;i<a.len || i<b.len;i++){
		if(a.d[i]<b.d[i]){//如果不够减
			a.d[i+1]--;
			a.d[i]+=10;
		}
		c.d[c.len++]=a.d[i]-b.d[i];
	}
	while(c.len-1>=1&&c.d[c.len-1]==0){
		c.len--;
	}
	return c;
}
//高精度与低精度的乘法
bign multi(bign a,int b){
	bign c;
	int carry=0;
	for(int i=0;i<a.len;i++){
		int temp=a.d[i] * b+carry;
		c.d[c.len++]=temp%10;
		carry=temp/10;
	}
	while(carry!=0){
		c.d[c.len++]=carry%10;
		carry/=10;
	}
	return c;
}
//高精度与低精度的除法
bign divide(bign a,int b,int& r){//r为余数，这里为引用
	bign c;
	c.len=a.len;
	for(int i=a.len-1;i>=0;i--){
		r=r*10+a.d[i];//和上一位遗留的余数组合
		if(r<b)c.d[i]=0;//不够除，该位为0
		else{
			c.d[i]=r/b;//商
			r=r%b;//获得新的余数
		}
	}
	while(c.len-1>=1&&c.d[c.len-1]==0){
		c.len--;
	}
	return c;
}
```

# java大数

```java
//创建大数类
import java.math.BigDecimal;
import java.math.BigInteger;
import java.util.Scanner;

Scanner cin=new Scanner(System.in);
BigInteger num1=new BigInteger("12345");
BigInteger num2=cin.nextBigInteger();
BigDecimal num3=new BigDecimal("123.45");
BigDecimal num4=cin.nextBigDecimal();

//BigInterger整数
import java.math.BigInteger;
public class Main {
	public static void main(String[] args) {
		BigInteger num1=new BigInteger("12345");
		BigInteger num2=new BigInteger("45"); //加法
		System.out.println(num1.add(num2)); //减法
		System.out.println(num1.subtract(num2)); //乘法
		System.out.println(num1.multiply(num2)); //除法(相除取整)
		System.out.println(num1.divide(num2)); //取余
		System.out.println(num1.mod(num2)); //最大公约数GCD
		System.out.println(num1.gcd(num2)); //取绝对值
		System.out.println(num1.abs()); //取反
		System.out.println(num1.negate()); //取最大值
		System.out.println(num1.max(num2)); //取最小值
		System.out.println(num1.min(num2)); //是否相等
		System.out.println(num1.equals(num2));
	}
}
//BigDecimal(浮点数)
import java.math.BigDecimal;
public class Main {
	public static void main(String[] args) {
		BigDecimal num1=new BigDecimal("123.45");
		BigDecimal num2=new BigDecimal("4.5"); //加法
		System.out.println(num1.add(num2)); //减法
		System.out.println(num1.subtract(num2)); //乘法
		System.out.println(num1.multiply(num2)); //除法（在divide的时候就设置好要精确的小数位数和舍入模式）
		System.out.println(num1.divide(num2,10,BigDecimal.ROUND_HALF_DOWN)); //取绝对值
		System.out.println(num1.abs()); //取反 System.out.println(num1.negate()); //取最大值
		System.out.println(num1.max(num2)); //取最小值
		System.out.println(num1.min(num2)); //是否相等
		System.out.println(num1.equals(num2)); //判断大小( > 返回1, < 返回-1)
		System.out.println(num2.compareTo(num1));
	}
}
```



# 5.7 扩展欧几里得算法
* ax+by=gcd(a,b).如何求得x,y。边界条件，b=0时x=1,y=0;根据辗转相除法递推可得递推公式x1=y2;y1=x2-(a/b) * y2;

```cpp
int exGcd(int a,int b,int& x,int& y){
	if(b==0){
		x=1;y=0;
		return a;
	}
	int g=exGcd(b,a%b,x,y);
	int t=x;
	x=y;
	y=t-(a/b) * y;
	return g;
}
```
* 方程ax+by=c的整数解，直接用扩展欧几里得算法，因为ax+by=gcd已知
* 同余式ax=c(mod m)求解，即ax+my=c求解，有解的条件是c%gcd(x,m)==0
* 逆元的求解。a,b,m>0,且ab=1(mod m)，则称a,b互为模m的逆元
* 常用取模1e9+7
	- 1. 1000000007是一个质数
	- int32位的最大值为2147483647，所以对于int32位来说1000000007足够大
	- int64位的最大值为2^63-1，对于1000000007来说它的平方不会在int64中溢出
	- 所以在大数相乘的时候，因为(a∗b)%c=((a%c)∗(b%c))%c，所以相乘时两边都对1000000007取模，再保存在int64里面不会溢出

# 5.8 组合数
* 第一个问题：**求n!有多少个质因子P**
* 比如10！中有2的质因子个数可表示为：有因子2的个数为10/2，有因子2^2的个数为10/2^2，有因子2^3的个数为10/2^3
可以递归求解，也可迭代求解。
* 即n!中有 $\frac{n}{p}+\frac{n}{p^2}+\frac{n}{p^3}+……$ 个质因子p,其中除法均为向下取整

```cpp
//迭代
int cal(int n,int p){
	int ans=0;
	while(n>0){
		ans+=n/p;
		n/=p;
	}
	return ans;
}
//递归
int cal(int n,int p){
	if(n<p)return 0;
	else return n/p+cal(n/p,p);
}
```

* 第二个问题：**组合数的计算**
$C_m^n=\frac{m!}{n!(m-n)!}$
* 方法一就是通过定义直接计算，但是因为阶乘相当庞大，此种方法计算组合数能接受的数据范围很小。
* 方法二通过递归公式计算 $C_m^n=C_{m-1}^n+C_{m-1}^{n-1}$
* 方法三通过定义变形来计算$C_m^n=\frac{(m-n+1)(m-n+2)……(m-n+n)}{n!}$，应注意到分子分母均为n项，先计算(m-n+1)/1，乘以(m-b+2)/2，不断乘以(m-n+i).

* 第三个问题：**如何计算$C_n^m\%P$**
* 掌握第一种方法即可，在组合数的计算的递归公式法上改进

```cpp
int res[1010][1010]={0};
//递归方法
int C(int n,int m,int p){
	if(m==0||m==n)return 1;//C(n,0)=C(n,n)=1
	if(res[n][m]!=0)return return res[n][m];
	return res[n][m]=(C(n-1,m)+C(n-1,m-1))%p;
}
//递推方法
void CalC(){
	for(int i=0;i<=n;i++){
		res[i][0]=res[i][i]=1;//初始化边界条件
	}
	for(int i=2;i<=n;i++){
		for(int j=0;j<=i/2;j++){
			res[i][j]=(res[i-1][j]+res[i-1][j-1])%p;
			res[i][i-j]=res[i][j];
		}
	}
}
```

# 5.9 错误重排问题
用A、B、C、D………表示写着n位友人名字的信封，a、b、c、d………表示n份相应的信，把n份信装错的总数记为D(n)，那么n-1份信封装错的总数就是D(n-1)。
现在，假设这样一种情况，把a错装进B中，那么对于信b有以下两种分法：
1. b装入A中，这样剩下的（n-2）份信和信封A、B，和信a、b就没有任何关系了，所以这时候装错的可能性总共有D(n-2)。
2. b不一定装入A中，那么就有可能装入A、C、D等其余除B之外的信封了，这时总共就是(n-1)种装错的可能性了。
所以对于信b来说，总共有D(n-2)+D(n-1)种装错的可能性。所以最后除a之外还有（n-1）封信，所以最终的关系式如下：
`D(n)＝(n－1)*[D(n－1)＋D(n－2)]`

# 5.10 斐波那契数列
想出递推公式最重要

# 5.11 约瑟夫问题

n个人围成一个圈，每个人分别标注为1、2、...、n，要求从1号从1开始报数，报到k的人出圈，接着下一个人又从1开始报数，如此循环，直到只剩最后一个人时，该人即为胜利者。例如当n=10,k=4时，依次出列的人分别为4、8、2、7、3、10，9、1、6、5，则5号位置的人为胜利者。给定n个人，请你编程计算出最后胜利者标号数。

约瑟夫问题，创新解法虽然分析复杂，但是代码却十分简洁明了，时间O(n)，空间O(1)，这就是数学的魅力。

主要思想：n个人第一个删除的人的秩为`(k-1)%n`，假如我们已经知道了n-1个人时，最后胜利者的编号为x，利用映射关系逆推，就可以得出n个人时，胜利者的编号为(x+k)%n（要注意的是这里是按照映射后的序号进行的）。可以总结为：
$$f(n,k)= ( f(n-1,k)+k )\%n  $$

```cpp
int LastRemaining_Solution(int n, int m){
    if(n<1||m<1)return -1;
    int last=0;
    for(int i=2;i<=n;i++)
        last=(last+m)%i;
    return last;
}
```

# 6.1 set
* set只能通过iterator访问
* 除vector和string之外的STL容器都不支持*(it+i)的访问方式，只能用迭代器枚举的方式
* find(value)返回对应值为value的迭代器
* erase(it)需要结合find函数，比如st.erase(find(st.find());
* erase(value)删除特定值
* erase(first,last)删除区间[first,last)
* set<int,greater<int> >默认是less<int>
* set中自定义元素排列顺序：待排序元素重载小于运算符
* 如果不需要对set进行排序，建议使用速度更快的unordered_set，内部采用散列实现。

# 6.2 string
* 可用用< <=等各种比较符，比较的依据是字典序
* erase(it)
* erase(first,last)
* **erase(pos,len)**
* insert(pos,string)在pos位置插入字符串string
* insert(it,it2,it3)    it为原字符串的带插入位置，it2、it3为带插入字符串的首尾迭代器
* substr(pos,len)
* string::npos是一个常数，值为-1，但由于是unsigned类型，为unsigned的最大值
* str.find(str2),如果str2是str的子串，返回其在str中第一次出现的位置，如果不是子串，返回npos
* str.find(str2,pos)，从str的pos位置开始匹配
* str.replace(pos,len,str2)把str从pos号位开始、长度为len的子串替代为str2
* str.replace(first,last,str2)把str的迭代器[first,last)范围的子串替换为str2

# 6.3 map
* 如果是字符到整形的映射，必须用string，不能用char数组
* map的第三个参数可以用greater<int> ,包含头文件<functional>
* find(key)返回键值key的迭代器
* erase(it)
* erase(key)
* erase(first,last)
* 常见用途：
	- 建立各种类型之间的映射
	- 判断大整数或者其他类是是否存在的题目，把map当bool数组用
	- 字符串和字符串的映射

# 6.4 queue
* 只能通过front()来访问队首元素，back()来访问队尾元素
* push()入队
* pop()出队
* empty()检测队空


# 6.5 priority_queue
* 不能用front()和back()函数，只能用top()访问队首元素
* **优先级设置**
* priority_queue<int,vector<int>,less<int> > q;注意最后两个>之间有一个空格。
* 易错点：less表示数字大的优先级高，greater<int> 表示数字小的优先级高。**优先级队列默认的就是优先级高的放在队首，优先级队列的这个函数和sort中的cmp起到的作用是相反的**
* 结构体内部要重载小于号

```cpp
//结构体内部重载<运算符
struct fruit{
	string name;int price;
	friend bool operator<(fruit f1,fruit f2){
		return f1.price<f2.price;//这里top是价格高的
	}
};
//函数对象
struct cmp{
	bool operator()(fruit f1,fruit f2){
		return f1.price<f2.price;
	}
};
```

* friend关键字不能省，当结构体类型较大时，参数用(const fruit& f1,const fruit& f2)
* 优先级队列这里的第三个参数只能是函数对象，不能是函数指针。（sort支持函数对象和函数指针两者）


# 6.6 pair
* 添加map头文件最方便
* pair<string,string> p;
* oair<string,int> p("haha",5);
* make_pair("haha",5);
* 比较时先以first的大小为标准，first相等时采取判别second的大小


# 6.7 algorithm
* max() min() abs() swap()
* reverse(it_1,it_2)，对[it_1,it_2)的元素进行反转
* next_permutation(it_1,it_2)
* sort支持函数对象和函数指针两者,STL中，只有vector、string、deque可以用sort，set、map本身有序，不用sort。list有自带的sort函数
* sort(it1,it2,cmp)cmp为函数指针和函数对象均可
* fill(it1,it2,e)
* lower_bound(first,last,val)第一个大于等于val的元素的位置，upper_bound第一个大于val的元素的位置


# 6.8 map set拓展
* unordered_map用散列代替红黑树，只处理映射而不处理键值排序，速度比map快很多
* unordered_set同理
* multimap一个键值可以对应多个值

```cpp
/*
	在multimap中，同一个键关联的元素必然相邻存放。基于这个事实，就可以将某个键对应的值一一输出。
	1、使用find和count函数。count函数求出某个键出现的次数，
	find函数返回一个迭代器，指向第一个拥有正在查找的键的实例。
	2、使用lower_bound(key)和upper_bound(key)
      lower_bound(key)返回一个迭代器，指向键不小于k的第一个元素
      upper_bound(key)返回一个迭代器，指向键不大于k的第一个元素
	3、使用equat_range(key)
      返回一个迭代器的pair对象，first成员等价于lower_bound(key)，second成员等价于upper_bound(key)
*/
int main()
{
    multimap<string,int> m_map;
    string s("中国"),s1("美国");
    m_map.insert(make_pair(s,50));
    m_map.insert(make_pair(s,55));
    m_map.insert(make_pair(s,60));
    m_map.insert(make_pair(s1,30));
    m_map.insert(make_pair(s1,20));
    m_map.insert(make_pair(s1,10));
    //方式1
    int k;
    multimap<string,int>::iterator m;
    m = m_map.find(s);
    for(k = 0;k != m_map.count(s);k++,m++)
        cout<<m->first<<"--"<<m->second<<endl;
    //方式2
    multimap<string,int>::iterator beg,end;
    beg = m_map.lower_bound(s1);
    end = m_map.upper_bound(s1);
    for(m = beg;m != end;m++)
        cout<<m->first<<"--"<<m->second<<endl;
    //方式3
    beg = m_map.equal_range(s).first;
    end = m_map.equal_range(s).second;
    for(m = beg;m != end;m++)
        cout<<m->first<<"--"<<m->second<<endl;
    return 0;
}
```

# 7.1 栈
* 计算逆波兰表达式，先将中缀表达式转化为后缀表达式，然后用栈计算后缀表达式的值(注意设置各运算符优先级)


# 7.2 队列


# 7.3 链表
* 套路：链表题目一般输入为`Addr Key Next`三元组，地址到下一个节点的映射或者用数组（优先选用，如果地址仅五位数表示的话），或者用map映射。
* 注意，题目往往要求地址是五位数，注意用%05d


# 8.1 DFS


DFS是一种枚举所有完整路径来遍历所有情况的搜索方法。最佳实现方法：递归。当然也可以自建栈代替递归，但是往往较麻烦，递归相当于调用系统栈，本质一样。

其中一类DFS问题的解法：给定一个序列，枚举这个序列所有的子序列（可以不连续）。

* 例1：01背包问题的DFS解法
对于每一个物品，DFS有两个分支，遍历所有的分支。当选择物品的综合超过背包容量，说明该路径走入了死胡同，则需要回到最近的一个岔道口。核心代码如下。

注意到每件物品都有两种选择，故时间复杂度是O(2^n)，优化的点有，在sumW+W[index]小于V时再进入迭代

```cpp
void DFS(int index,int sumW,int sumC){
	if(index==n){已经完成物品选择的处理}
	DFS(index+1,SumW,sumC);//不选第index件物品
	DFS(index+1,SumW+W[index],sumC+C[index]);//选第index件物品
}
```

* 例2：N个整数中选择K个数的和等于X的所有方案，冲突的话选择其平方和最大的一个。用向量记录添加进方案当中的元素，进入DFS下一步的时候将元素入栈。


```cpp
void DFS(int index,int nowK,int sum,int sumSqu){
	if(nowk==k&&sum==x){
		if(sumSqu>maxsumSqu){
			maxsumSqu=sumSqu;
			ans=tmp;
		}
		return;
	}
	if(index==n||nowk>k||sum>x)return;

	//在DFS递归前和递归后分别设置状态和取消状态，是回溯的典型特征。
	//选择index元素
	tmp.push_back(A[index]);
	DFS(index+1,nowk+1,sum+A[index],sumSqu+A[index]*A[index]);
	tmp.pop_back();
	//不选index

	DFS(index+1,nowk,sum,sumSqu);

}
```
* DFS应用之全排列

阿哈算法：将问题形象化为降1-n编号的扑克牌放入n个盒子中，用book[]数组记录该数字已经放过。

```cpp
#define maxn 100
int n;
vector<int> book(maxn,0),vec(maxn);
//step表示现在放第step个盒子，n表示全排列总数
void DFS(int step){
    if(step>n)return;
    for(int i=1;i<=n;i++){
        if(!book[i]){
            book[i]=1;
            vec[step]=i;
            DFS(step+1);
            book[i]=0;//DFS关键一步，收回刚放的牌
        }
    }
}

DFS的伪代码如下：
dfs(int step){
    判断边界
    尝试每一种可能{
        标记数组记录该次选择
        DFS(step+1)
        取消标记数组的选择
    }
    返回
}
```

* DFS和BFS配合vis数组，都可以用来求解连通域的个数

* DFS回溯法解n皇后

邓书改进迭代版本，原版见cppSTL。

```cpp
 /*书中写的虽然代码短，但是不好理解是如何实现的，我根据自己的思路写的这个，思路很清晰
 	感觉没必要为了少几行代码将思路搞得很不一般，就像归并排序一样，末尾有元素剩余单独处理就好
	循环不变性：初始进入循环的是有效元素，每次根据在栈中有没有找到相同元素判断是否可以入栈
	入栈情况：
		当栈满时，意味着找到了一种结果，记录并输出。然后y不变，x++，当然，如果x也到了最大值，就要回溯（此时的回溯必定可以找到有元素的x非最大值，因为此前的元素各不相同）
		栈不满时，y++,x=0
	不入栈的情况：
		x没达到最大值，x++即可
		x达到了最大值，回溯，回溯到的元素不是第一行最大值时，x++即可，否则，退出循环。这是循环退出的唯一条件
 */
#include<iostream>
#include"string.h"
using namespace std;
//试探回溯法解八皇后问题
struct Queen{
	int x,y;
 	Queen(int xx=0,int yy=0):x(xx),y(yy){}
 	Queen(Queen& b){x=b.x;y=b.y;}
 	bool operator==(Queen b){
 		return x==b.x||y==b.y||x+y==b.x+b.y||x-y==b.x-b.y;
 	}
};

int find(Queen* solu,Queen& q,int lo,int hi){//查找范围[lo,hi),找不到返回hi
	for(int i=lo;i<hi;i++){
         if(solu[i]==q)return i;
    }
    return hi;
}
void placeQueens(int N){
 	Queen* solu=new Queen[N];int top=0;
 	//用数组模拟栈,入栈solu[top++]=q;出栈q=solu[--top];
 	int nsolu=0;//记录满足条件的答案的数量
 	Queen q(0,0);
 	solu[top++]=q;q.y++;
 	//不断的试探回溯,忒休斯的绳索是栈，入栈是进一步，出栈是退一步
 	while(1){
         //循环不变性：初始进入循环的q是有效的
         //首先判断q应该前进（入栈）还是后退（出栈）
    	int r=find(solu,q,0,top);
        if(r==top){//前进
        	solu[top++]=q;
            //判断是否满足题意，确保下一次循环内的元素满足条件
            if(top==N){//得到满足题意的一个结果
            	nsolu++;
                for(int i=0;i<N;i++)printf("%d ",solu[i].x);
                printf("\n");
                //得到下一个有效点，回溯，以进入下一次循环
                while(q.x==N-1&&top>0)q=solu[--top];
                q.x++;
            }else{
                q.y++;q.x=0;
            }
        }else{
            if(q.x==N-1){//说明此种情况不符合条件，应回溯
                while(q.x==N-1&&top>0)q=solu[--top];
                if(top==0&&q.x==N-1)break;
                else q.x++;
            }else{
                q.x++;
            }
        }
	}
 	cout<<nsolu;
}
int main(){
    //freopen("d:\\CodeBlockSpace\\input.txt","r",stdin);
    placeQueens(8);
    return 0;
}
```

递归版本
```cpp
class Solution {
public:
    vector<vector<int> > ans;
    vector<vector<string> > strans;
    vector<int> tmp;
    vector<bool> vis;
    bool conflicted(vector<int>& tmp,int x){
        for(int i=0;i<tmp.size();i++){
            if(x==tmp[i])return false;
            if(tmp.size()+x==i+tmp[i])return false;//斜对角线
            if(tmp.size()-x==i-tmp[i])return false;//正对角线
        }
        return true;
    }
    void solve(int step,int n){
        if(step==n){
            ans.push_back(tmp);
            //cout<<ans[0][0]<<endl;
            return;
        }
        for(int i=0;i<n;i++){
            if(!vis[i]){
                if(conflicted(tmp,i)){
                    vis[i]=true;
                    tmp.push_back(i);
                    solve(step+1,n);
                    tmp.pop_back();
                    vis[i]=false;
                }
            }
        }
    }
    vector<vector<string>> solveNQueens(int n) {
        vis.resize(n,false);
        fill(vis.begin(),vis.end(),false);
        int step=0;
        solve(step,n);
        for(int i=0;i<ans.size();i++){
            vector<string> strtmp;
            for(int j=0;j<ans[i].size();j++){
                string s;
                for(int k=0;k<n;k++){
                    if(ans[i][j]==k)s+="Q";
                    else s+=".";
                }
                strtmp.push_back(s);
            }
            strans.push_back(strtmp);
        }
        return strans;
    }
};

//思路相同，但是相对更加简单点的方法
//8皇后问题
#define N 8
vector<int> v(N,0);//表示每一行中皇后的位置
int cnt=0;
bool conflict(int step){
	_for(i,0,step){
		if(v[i]==v[step]||v[i]-i==v[step]-step||v[i]+i==v[step]+step)
			return true;
	}
	return false;
}
void DFS(int step){
	if(step==N){
		_for(i,0,N){
			printf("%d ",v[i]);
		}
		printf("\n");
		cnt++;
		return;
	}
	_for(i,0,N){
		v[step]=i;
		if(!conflict(step))
			DFS(step+1);
	}
}
int main(int argc, char** argv) {
	int step=0;
	DFS(step);
	cout<<cnt<<endl;
	return 0;
}
```


# 8.2 BFS

相比DFS用递归系统栈实现，BFS用自建队列实现。

* BFS的模板

```cpp
void BFS(int s){
	queue<int> q;
	q.push(s);
	while(!q.empty()){
		取出队首元素top
		访问队首元素top
		将队首元素出队
		将top的下一层未入队的节点入队，并标记为已入队
	}
}
```

* 用BFS求m*n矩阵中连通域的个数。（注意，这里可以用x[4],y[4]枚举四个方向）
* 迷宫中求S到T的最少步数，用BFS，并且记录节点深度（S深度为0，然后每一层深度是上一层+1）
* 队中元素时是结构体的注意事项，如果直接在队列汇中存储结构体，那么，对队列中的副本的修改不会影响原元素。这就是说，如果需要对队列中的元素进行修改，最好在队列中存储元素的编号而非元素本身，如果数组的话则是下标。
* BFS求二叉树的层次遍历
* BFS求二叉树的最小深度
* BFS判断是否是满二叉树，将空节点也入队，如果出队的是空节点，而此时已经出队了n个元素，则是满二叉树
* m*n图中的最短路径问题，都可以用BFS来最方便实现


# 9 树与二叉树
* 二叉树构建
* DFS递归遍历，BFS层次遍历
* 二叉树的静态实现适合于不喜欢用指针的同学。其定义、查找、插入、遍历等均可以静态实现

```cpp
struct node{
	int val;
	int lc,rc;
}Node[MAXN];//节点数组，MAXN为节点上限
```

* BST的插入操作
* 删除操作：将该节点的值与其前驱或者后继的值交换，然后再进行删除。

# 10 图算法专题
见专题总结

# 11 动态规划
见专题总结

# 12 字符串
见专题总结

# 13 树状数组

* `lowbit(x)=x&(-x)`
* 背景：给定一个整数序列，有两种操作：计算前K项的和，对某一项加。如果采用普通的数组，则计算前K项和的复杂度过高，如果采用sum数组，则给某一项加的复杂度过高。
* 采用树状数组可以使两种操作的复杂度均为log(n)
* 应熟记树状数组的图形记忆。数组的下标从1开始，不包括0.
* **//C[i]的覆盖长度是lowbit(i)，以i为结尾。**

```cpp
#define lowbit(i) (i&(-i))

int  A[100005];
long long C[100005];

//A[x]加上v
void updata(int x,int v){
	if(v==0)return;
	for(int i=x;i<n+1;i+=lowbit(i)){
		C[i]+=v;
	}
}

//计算A[1]-A[x]的和
long long getsum(int x){
	long long sum=0;
	for(int i=x;i>0;i-=lowbit(i)){
		sum+=C[i];
	}
	return sum;
}
```

典型应用1：单点更新，区间查询
* 给定一个有N个正整数的序列A(A<=10^5,A[i]<=10^5) ,对序列中的每个数，求出序列中它左边比它小的数的个数。

思路：
* 用hash[x]记录整数x当前出现的次数。从左到右遍历序列，对于每一个x，使hash[x]加一。对于序列中左侧比自身小的数，即为hash[0]+hash[1]+……+hash[x-1]，即单元素加操作和前k项和操作，直接用树状数组实现即可。

举一反三：
* 如果求序列中左侧比自身大的数字的个数，即求hash[x+1]+hash[x+2]+……+hash[n]，即getSum(N)-getSum(x)。
* 如果没有给出A[i]的取值范围，要采用离散化技巧。
* 先排序，设置结构体{val,pos},即把原始数据的大小和位置记录下来，后按照大小排序。即把所有可能的数字映射到1-n，然后用之前的hash数组即可。

典型应用2：区间更新，单点查询
* 设计函数getSum(x)，返回A[x]
* 设计函数update(x,v)，将A[1]-A[x]的每个数都加上一个数v

思路：
* 修改树状数组的定义，宽度不变，仍为lowbit(x)，但是C[i]不再表示这段宽度内的综合，而是表示这段区间的每个数当前被加了多少。

# 14 主元素问题
一个序列中有没有一个元素超过总数的50%？

方法一：当数组元素之间可以有序时，找中位数，它是主元素的唯一候选，用插入排序类似的第K大问题求解。第K大问题的时间复杂度是O(N)

方法二：摩尔投票算法。元素数目相同时删除前序即可，最后剩下的一个元素就是主元素。

方法一没有考虑到题目中数组的特性：数组中有个数字出现的次数超过了数组长度的一半。也就是说，有个数字出现的次数比其他所有数字出现次数的和还要多。因此我们可以考虑在遍历数组的时候保存两个值：一个是数组中的一个数字，一个是次数。当我们遍历到下一个数字的时候，如果下一个数字和我们之前保存的数字相同，则次数加1。如果下一个数字和我们之前保存的数字不同，则次数减1。如果次数为零，我们需要保存下一个数字，并把次数设为1。由于我们要找的数字出现的次数比其他所有数字出现的次数之和还要多，那么要找的数字肯定是最后一次把次数设为1时对应的数字。

不限定时间复杂度的话，很多人会先排序，再遍历的方法来做。不限定空间复杂度的话，很多人会用hash表来做。那么，有了这两个限定，就只能用摩尔投票算法了。

摩尔投票算法：时间复杂度O(n)，空间复杂度O(1)

```cpp
//leetcode169
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int ans=nums[0],cnt=1;
        for(int i=1;i<nums.size();i++){
            if(cnt==0){ans=nums[i];cnt++;}
            else if(nums[i]==ans)cnt++;
            else cnt--;     
        }
        return ans;
    }
};
```

扩展：问题升级为选取大于等于n/3的数，简单分析可知，大于n/3的数最多有两个。（反证法轻轻松松即可证明），采取和169一样的摩尔投票算法，只不过，这次保留两个元素出现的次数。另外一个区别是这个题目没有保证此众数一定存在，所以，在得到两个候选众数之后，需要再次遍历一遍验证。

```cpp
//LeetCode229
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        vector<int> res;
        int m = 0, n = 0, cm = 0, cn = 0;
        for (auto &a : nums) {
            if (a == m) ++cm;
            else if (a ==n) ++cn;
            else if (cm == 0) {m = a, cm = 1;}
            else if (cn == 0) {n = a, cn = 1;}
            else --cm, --cn;
        }
        cm = cn = 0;
        for (auto &a : nums) {
            if (a == m) ++cm;
            else if (a == n) ++cn;
        }
        if (cm > nums.size() / 3) res.push_back(m);
        if (cn > nums.size() / 3) res.push_back(n);
        return res;
    }
};
```

# 15 全排列

* DFS解一般全排列问题
## 可重复元素的全排列问题
和DFS不同元素的方法是一样的，只不过怎加了判断元素个数是否用完
	- 不过，这个方法太繁琐，重复计算各个各个元素的数目，其中的`!i||P[i]!=P[i-1]`就是除去了重复元素而已，要理解P是包含重复元素的已排序序列，这个就很容易理解了，还是不如一个数组表示数目，一个数组表示元素数量。

```cpp
int n,m;//元素总的个数、不同元素的数目
vector<int> cntP,P,ans;//分别表示元素的值和数量
void permutation(int step){
	if(step==n){
		printvec(ans);
		return;
	}else{
		for(int i=0;i<P.size();i++){
			if(cntP[i]>0){
				cntP[i]--;
				ans[step]=P[i];
				permutation(step+1);
				cntP[i]++;
			}
		}
	}
}
int main() {
	freopen("d:\\input.txt","r",stdin);
	//freopen("d:\\output.txt","w",stdout);
	cin>>n>>m;
	cntP.resize(m);P.resize(m);ans.resize(n);
	_for(i,0,m)	cin>>P[i];
	_for(i,0,m)	cin>>cntP[i];
	permutation(0);
	return 0;
}
```

## 求下一个排列的字典序法

[由当前序列找到下一个序](https://blog.csdn.net/cpfeed/article/details/7376132?utm_source=blogxgwz3)

设P是1～n的一个全排列:`p=p1p2......pn=p1p2......pj-1pjpj+1......pk-1pkpk+1......pn`

* 从排列的右端开始，找出第一个比右边数字小的数字的序号j（j从左端开始计算），即 `j=max{i|pi<pi+1}`
* 在pj的右边的数字中，找出所有比pj大的数中最小的数字pk，即 `k=max{i|pi>pj}`（右边的数从右至左是递增的，因此k是所有大于pj的数字中序号最大者）
* 对换pi，pk
* 再将`pj+1......pk-1pkpk+1......pn`倒转得到排列`p'=p1p2.....pj-1pjpn.....pk+1pkpk-1.....pj+1`，这就是排列p的下一个排列。

## 康拓展开和逆康托展开
采用了康托展开的排序方法，顺序最小，逆序最大，康托展开是字符串全排列到自然数的一个双射，通常用于构建哈希表时空间压缩

* 首先从最尾端开始往前寻找两个相邻元素，令第一元素为i,第二元素为i+1,且满足v[i]<v[i+1]。
* 找到这样一组相邻元素后，再从最尾端开始往前检验，找出第一个大于v[i]的元素，令为j，将i,j元素对调swap(v[i],v[j])。
* 再将i+1(包括)及之后的所有元素颠倒(reverse)排序。
[思路参考](https://blog.csdn.net/c18219227162/article/details/50301513)
[康托展开](https://blog.csdn.net/wbin233/article/details/72998375)

```cpp
bool next_permutation(vector<int>& nums,int lo,int hi){
    int i=hi-2,j=hi-1;
    //step1
    while(i>=lo){
        if(nums[i]<nums[i+1])break;
        i--;
    }
    if(i<lo)return false;
    //step2
    for(j=hi-1;j>i;j--){
        if(nums[j]>nums[i])break;
    }
    swap(nums[i],nums[j]);
    //step3
    reverse(nums.begin()+i+1,nums.end());
    return true;
}
vector<vector<int>> permuteUnique(vector<int>& nums) {
    vector<vector<int>> ans;
    int n=nums.size();
    if(n==0)return ans;
    sort(nums.begin(),nums.end());
    do{
        ans.push_back(nums);
    }while(next_permutation(nums,0,n));
    return ans;
}
```

# 16 并查集

```cpp
//路径优化：
int Find(int x) { return fa[x]==x?x:fa[x]=Find(fa[x]);}
//合并
void union(int x,int y){
	int fx=find(x),fy=find(y);
	fa[fx]=fy;
}
//判断是否同一子集
bool same(int x,int y){
	return find[x]==find[y];
}
```
