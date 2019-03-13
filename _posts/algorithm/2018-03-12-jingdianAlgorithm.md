---
layout: post
title: 算法竞赛入门经典
tags:
- DSA
categories: algorithm
description: 笔记
---


# 第一部分 程序设计入门

# 第一、二章 学习建议

1. 学会模仿，适当的不求甚解
2. 不拘一格的用伪代码来思考和描述算法
3. 不要忘记测试，一个看上去正确的程序也可能存在错误
4. 在观察无法找出错误的时候，优先用输出中间结果来找错
5. 适当的用do{}while();而不需要把必须执行一次的循环多写一遍
6. 输入输出的重定向很好用，也方便进行结果对比，存入标准答案用diff命令对比
7. 重定向的部分写入`#ifdef #endif`之间
8. 将重要的测试输出代码调试好后注释掉而非删除
9. 如果禁用输入输出则使用`fopen,fscanf,fprintf`
10. OJ常见的错误类型，正确是accept，AC
	1. wrong answer,WA
	2. presentation error,PE
	3. time limit exceeded,TLE
	4. Runtime error,re
11. 学扎实前11章的内容，需要特别关注每章后面的“小结”部分，要注意到：理解一个题解和自己独立推导出来所有细节还是不一样的，所有在看完一个难题的题解后最好把它做两遍：第一遍是刚看完题目后趁热打铁，一遍是等忘掉题解后自己从头推导一遍。


# 第三章 数组和字符串

1. 数组用全局变量，容量选大点，`#define maxn 105`，浪费了5，但是更保险
2. int a[maxn],b[maxn];将数组a赋值k个元素到数组b可用`memset(b,a,sizeof(int)*k)`，全部复制可以写的简单些，改为sizeof(a)即可
3. 例3-3蛇形填数：代码值得学习，非常，判断也很明了

```cpp
#define manx 20
int a[maxn][maxn]
int main(){
	int n,x,y,tot=0;//total缩写
	scanf("%d",&n);
	memset(a,0,sizeof(a));
	tot=a[x=0][y=n-1]=1;//注意这里连续赋值的写法，非常简洁
	while(tot<n*n){
		while(x+1<n&&!a[x+1][y])a[++x][y]=++tot;//向下
		while(y-1>=0&&!a[x][y-1])a[x][--y]=++tot;//左
		while(x-1>=0&&!a[x-1][y])a[--x][y]=+=tot;//上
		while(y+1<n&&!a[x][y+1])a[x][++y]=++tot;//右
	}
}
```

4. cctype中定义的isalpha\isdigit\isprint可以判断字符的属性，toupper\tolower可以转换大小写
5. 不同操作系统的回车换行符是不一致的，用fgets、getchar应避免写和操作系统相关的程序。
6. C语言并不进制程序读写非法内存，例如声明char s[100]，完全可以赋值s[1000]='a'，甚至-Wall也不会警告。
7. fgets(buf,maxn,fin)，从文件中读取不超过maxn个字符，其中buf的大小是maxn。而gets并没有指明读取的字符数，引发的潜在的问题就是gets不停的往s中存储数据，而不管是不是存的下。正因如此，C++11已经废除了gets函数

# 第四章 函数与递归

* 建议把谓词命名为is_xxx的形式，返回int值，0表示false,1表示true
* 函数调用栈保存了该函数的返回地址和局部变量，这样自然的保证了不同函数间的局部变量互不相干
* 数组作为函数参数时，int a[]和int* a的作用是一样的，均不能对函数参数机型sizeof(a)运算，得到的只是指针类型的宽度，而应该传入指针，并且传入数组的宽度，当然也可以从第二位传入，如int &a[1]
* 函数作为函数参数，用的最多的就是sort函数传入cmp参数，这里cmp是函数名，即函数指针，函数的入口地址。
* 由于使用了调用栈，c语言自然支持递归，并且调用自己和调用其他函数没有本质区别，都是建立栈帧，传递参数并修改当前代码行
* 段错误和段溢出，size命令可以得到可执行文件各个段的大小，包括正文段（存储指令）、数据段（存储已初始化的全局变量）、BSS段（用来存储未赋值的全局变量所需的空间）。
* 运行时程序才常见堆栈段，里面存放着调用栈，保存着函数的调用关系和局部变量，所以，如果发生段溢出，可能是递归深度的问题，当然也可能是局部变量太大的原因，所以建议把大数组写到main函数外面。
* 不要返回局部变量的指针，因为局部变量存储在系统栈中，函数调用解释自动释放该内存，如果要返回新变量的指针，应该用malloc动态内存分配。
* 浮点数误差：判断浮点数相等可以用相差为1e-6来判断


# 第五章 C++与STL入门
从这一章开始，例题都要自己做下，OJ要AC
* C++中声明数组时，可以用const声明的常数，C++中这种写法更推荐，而不用#define声明常数
* 引用是别名，虽然功能上比指针弱，但是减少了出错的可能，提高了代码可读性。
* lower_bound返回大于或等于e的最小数，upper_bound
* 测试时常用assert
* 大整数类、高精度算法
```cpp
class Bign
```

# 第六章 数据结构基础
# 第七章 暴力求解法

## 7.3 自己生成
### 7.3.1 增量构造法

```cpp
int n;//元素总的个数、不同元素的数目
vector<int> P,R;//P表示元素的值,R是元素的秩的集合
void print_subset(int cur){
	_for(i,0,R.size())printf("%d ",P[R[i]]);
	printf("\n");
	int s=cur?R.back()+1:0;//这里用到了定序的技巧，避免了同一个集合枚举两次
	_for(i,s,n){
		R.push_back(i);
		print_subset(cur+1);
		R.pop_back();
	}
}
int main(){
	freopen("d:\\input.txt","r",stdin);
	cin>>n;
	P.resize(n);
	_for(i,0,n)cin>>P[i];
	print_subset(0);
	return 0;
}
```

### 7.3.2 位向量法

```cpp
void print_subset(int cur){
	if(cur==n){输出序列；返回；}//当B[i]==1时表示该元素在集合当中
	B[cur]=1;//选当前元素
	print_subset(cur+1);
	B[cur]=2;//不选当前元素
	print_subset(cur+1);
}
```

### 7.3.3 二进制法

     集合与二进制数的一一对应关系
* A&B A|B A^B 分别对应集合的交、并、对称差。
```cpp
void print_subset(int n,int s){
	_for(i,0,n)
		if(s&1<<i)printf("%d ",i);
	printf("\n");
}
_for(i,0,1<<n)print_subset(n,i);
```

# 第八章
