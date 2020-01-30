---
layout: post
title: 图算法总结
tags:
- DSA
categories: algorithm
description: 总结图算法，包括
---
# 图算法
1. 图的定义、表示、存储
2. 图的遍历
    1. DFS
    2. BFS
3. 最短路径
    1. Dijkstra单源最短路径
    2. bellman-Ford负向边单源最短路径算法
    3. SPFA算法
    4. Floyd全源最短路径算法
4. 最小生成树
    1. prim算法
    2. kruskal算法
5. 拓扑排序
6. 关键路径
7. DAG最长路
8. 图的割点
9. 图的割边
10. 二分图最大匹配
11. 欧拉图、欧拉路径
12. 图中最小环(Floyd、BFS两种方法)
13. 强连通分量tarjan算法
14. 最大流算法

# 1 图的定义、表示、存储

# 2 图的遍历

## 课本重要知识点
重要概念：
* 对于顶点，有三种状态:undiscovered  discovered  visited
* 对于边，有五种状态:undetermined(未确定)  tree(支撑树边)  cross(跨边)  forward(祖先指向后代)  backward(被后代指向的祖先)
* 入度、出度
* 顶点访问的时间标签：DTime(发现时间，即状态置为discovered的时间)  FTime(结束访问时间，即状态置为visited的时间)
* 这里记录了被发现和访问完成的时刻，对应的时间区间[dtime(v),ftime(v)]称为该顶点的活跃期。实际上，两个顶点是否存在祖先/后代的关系，完全取决于二者的活跃期是否包含（不可能相交而不包含）

```cpp
void DFS(int v,int& clock){
    dtime(v)=++clock;statue(v)=discovered;
    for(int u=firstNbr(v);-1<u;u=nextNbr(v,u)){
        switch(statue(u)){//根据顶点的状态来分别处理##重要
            case undiscovered：//意味着支撑树可以在这里扩展，状态为tree
                type(v,u)=tree;parent(u)=v;DFS(u,clock);break;
            case discovered://已经发现但是还未访问，说明是其祖先，状态为backward
                type(v,u)=backward;break;
            case visited://已经访问，应比较生命期来判断是否有血缘关系，有的话该边状态为forward，否则是cross
                type(v,u)=dtime(v)<dtime(u)?forawrd:cross;break;
        }
    }
    statue(v)=visited;ftime(v)=++clock;//至此，v访问完毕
}
```

* 与广度优先搜索有关的边的状态：tree  cross
* 与深度优先搜索有关的边的状态：tree backword forward

# 3 最短路径

## 0 单源最短路径的最优子结构

* 最优子结构是动态规划和贪心是否适用的一个标记。
* 迪杰斯特拉算法是一个贪心算法，弗洛伊德算法是一个动态规划算法。
* 最短路径的最优子结构表现在：最短路径的子路径也是最短路径

## 1 迪杰斯特拉算法

* 实际上是贪心算法，将所有的点分为两个集合，一个是已经确定最短路径的，一个是还没有的，维护dist[]数组，每次从没有确定最短路径的点的集合中找到距离最短的点加入集合
* 可以看到，**如果存在负权边，贪心就不可用**
* 复杂度计算：最外层循环n次（每次加入确定点一个），内层显示遍历dist数组找最小值O(n)，然后对找到的点的边进行松弛操作，所有复杂度为  $n*(n+E_i)=n^2+E$
* 堆优化：如果内存查找函数采用堆存储，则查找时间变为了logn，则时间复杂度为$nlogn+E$
* 用邻接表存储图，两层循环，复杂度是O(n^2)

* 重点：set、path、dist数组的含义以及维护
* set[]存放该节点是否已经确定最短路径(确定为1，否则为0)
* path[i]存放要想到达i节点，需要到达的上一个节点
* dist[i]存放v0到达第i个定点的最短路径
* 对于三种特殊情况：统计第二边权，统计点权，统计最短路径的个数，应分别维护另一个数组，并且根据相等和小于的情况进行不同的处理

```cpp
//Dijkstra+DFS

const int MAXN=100;
const int INF=0x7fffffff;
vector<int> dist(MAXN,INF);
vector<bool> vis(MAXN,false);
vector<vector<int> > grid(MAXN),path(MAXN);
dist[0]=0;
void dijkstra(){
    while(1){
        int minid=-1,mindist=INF;
        _for(i,0,N){
            if(!vis[i]&&dist[i]<mindist){
                minid=i;mindist=dist[i];
            }
        }
        if(minid==-1)break;
        vis[minid]=true;
        _for(i,0,grid[minid].size()){
            if(vis[grid[minid][i]])continue;
            int ni=grid[minid][i].id,len=grid[minid][i].len;
            if(dist[ni]>dist[minid]+len){
                dist[ni]=dist[minid]+len;
                path[ni].clear();
                path[ni].push_back(minid);
            }else if(dist[ni]==dist[minid]+len){
                path[ni].push_back(minid);
            }
        }
    }  
}
```

* 堆优化

```cpp
const int MAXN=10005;
#define inf 0x7fffffff
typedef long long LL;
#define _for(i,lo,hi) for(int i=(lo);i<(hi);i++)
int N,M;

struct edge{
	int id,len;
	edge(int i,int l):id(i),len(l){}
	bool operator<(const edge& b)const{return len>b.len;}//重要
};
vector<int> dist(MAXN,inf);
vector<bool> vis(MAXN,false);
vector<edge> G[MAXN];
vector<int> path(MAXN,-1);

void Dijkstra(){
	priority_queue<edge> pq;
    dist[0]=0;
	pq.push(edge(0,0));
	while(!pq.empty()){
		edge e=pq.top();pq.pop();
		int nc=e.id;
		if(vis[nc])continue;
		vis[nc]=true;
		_for(i,0,G[nc].size()){
			int nx=G[nc][i].id,len=G[nc][i].len;
			if(vis[nx])continue;
			if(dist[nx]>dist[nc]+len){
				dist[nx]=dist[nc]+len;
				pq.push(edge(nx,dist[nx]));
			}
		}
	}
}
```

## 2 bellman-ford算法（贝尔曼福特），只要五行的代码：

对于负权值边的讨论：首先看我们对于最短路径的定义：
$$D_{ij}=min\sum{(D_{ik}+D_{kj})}$$
可以看出来，及时存在负权值边，这个定义也是没有问题的，$D_{ij}$ 也是存在最小值的，但是，如果存在负权值回路，这个定义就有问题了。

最短路径是否存在回路：如果有正权或零权回路，则必有删除此正权回路的路径

* 单源最短路径，可以存在负权边，可以检测负权回路是否存在，复杂度O(VE)
* 松弛操作:
$$D_{0i}+E_{ij}<D_{0j}$$

* 循环n-1次，每次对所有的边进行松弛操作。可以看到，这个过程实际上包含了迪杰斯特拉的全部过程。也是将所有的点分为两个集合，确定和未确定最短距离的，那么，每一轮的松弛操作，两个集合的跨边上的边都可以经过松弛操作而使新的点加入到确定点的集合当中（虽然这里没有标记，但是已经确定最短路径的点在新的松弛操作过程中是不会发生变化的）
* 算法相比较迪杰斯特拉，复杂度到了O(VE)，但有个好处是可以计算负权边和负权回路。
* 但是，bellman-ford算法存在很多不必要的运算和冗余运算，改进的方法就是spfa算法

```cpp
dist[0]=0;
//对所有的边进行n-1次循环
_for(i,0,n-1){
    bool flag=true;
    //邻接表存储图，对所有边进行一次松弛操作
    _for(j,0,n){
        _for(k,0,grid[j].size()){
            if(dist[k]>dist[j]+grid[j][k].len){
                dist[k]=dist[j]+grid[j][k].len;flag=false;
            }
        }
    }
    if(flag)break;//如果该轮没有松弛，则提前退出循环
}
//再循环一次，如果还可以优化，就是存在负权回路
bool flag=true;
_for(j,0,n){
    _for(k,0,grid[j].size()){
        if(dist[k]>dist[j]+grid[j][k].len){
            flag=false;break;
        }
    }
}
```

## 3 SPFA(shortest path faster algorithm)算法

* SPFA就是bellman-ford的队列优化
* 算法的主要思路就是改进bellman-Ford中无意义的操作，可以看到，对BF算法中每轮对边的松弛操作$E_{ij}$ 如果要起作用，必然在上一轮操作中改变了$D_{i}$ 或者$D_{j}$ ,否则，这一轮对于边$E_{ij}$ 的松弛就是无用功。
* 所以，我们建立一个队列，每次将队首元素取出，对其各边进行松弛操作，将改变了节点最短距离的节点入队，将为改变节点值的节点不入队，这样操作直至队列为空（当存在负权回路时队列不会空），判断有负权值环存在：设置一个num数组，记录各个节点入队的次数，如果一个节点入队达到n次，则必然存在负权值回路。如果实现知道不存在负权值回路，则不必设置该数组

* 复杂度期望O(kE)，k为常数，一般不超过2，此算法大部分很高效，经常好过迪杰斯特拉算法。
* 这里要注意，设置inqueue数组判断该节点是否已在队列，防止重复入队。当然了，松弛操作并不因该节点在队列中而不进行，松弛操作对所有边都进行，只不过，如果其值改变了，不必重复入队

```cpp
vector<int> dist(n,inf),num(n,0);
vector<bool> inque(n,false);
dist[0]=0;
queue<int> q;
q.push(0);
inque[0]=true;
num[0]=1;
while(!q.empty()){
    int cur=q.front();q.pop();inque[cur]=false;
    _for(i,0,grid[cur].size()){
        int next=grid[cur][i];
        if(dist[next]>dist[cur]+grid[cur][i].len){
            dist[next]=dist[cur]+grid[cur][i].len;
            if(!inque[next]){
                q.push(next);
                inque[next]=true;
                num[next]++;//next点的入队次数加一
                if(num[next]>=n)return false;//存在可达负环
            }
        }
    }
}
```


## 4 floyd算法（弗洛伊德）

* 全源最短路径，是一个动态规划算法。三轮循环，$O(n^3)$
* 图中任意一对定点之间的最短路径
可以存在负边，但是不能有权值为负的回路

* 是一种动态规划算法
* 用邻接矩阵存储，三层循环，复杂度是O(n^3)
* 维护两个二维n*n数组，dist[i][j]表示从顶点i到达顶点j的最小距离，path[i][j]表示从i到达j需要到达的下一个顶点，初始值为-1，表示i到j直接可达。比如path[1][3]=2表示从1到3要经过2，然后看dist[2][3]=-1，表示从2可以直接到3。

三重循环的次序不能颠倒：

```cpp
for k-0：n
    for i-0:n
        for j-0:n
            if dist[i][j]>dist[i][k]+dist[k][j]
k表示中间节点,i,k表示两端的节点
```

弗洛伊德算法很好写，但是怎么证明其正确性呢？这个算法本质上是一个动态规划算法，具体的证明看《算法导论》
$$d_{ij}^k$$
定义为i,j定点之间，所有中间节点都在顶点(1,2,...,k)中的最短路径.

则可以推出递推公式
$$d_{ij}^k=min({d_{ij}^{k-1},d_{ik}^{k-1}+d_{kj}^{k-1}})$$
其递归基为
$$d_{ij}^0=w_{ij}$$
因为没有中间节点存在

# 4 最小生成树

无向图最小生成树三个性质
* 无环，边数=顶点数-1
* 对于给定的无向图，最小生成树不唯一，但是，其边权和唯一
* 算法题目时为了测评容易，一般会给定根节点

* prime算法和kruskal算法都是贪心算法，只不过一个是点贪心，一个是边贪心
* 由复杂度分析可知：如果是稠密图，用`prime`更优，如果是稀疏图，kruskal更优

## prime算法（普里姆）
* 和迪杰斯特拉算法几乎一样，只不过用集合来代替起点s，dist数组定义为未在集合当中的节点距离集合的最短距离，其余算法的流程完全相同
* prime算法近适用于无向图。在有向图中，有可能存在这样一种情况：两个节点之间来和回的权重不一样.
* 复杂度：和迪杰斯特拉相同，为O(nlogn+E)（经过堆优化）

```cpp
dist[s]=0;
vis[s]=true;
_for(i,1,n){//n-1次循环
    int minid=-1;mindist=inf;
    _for(j,0,N){
        if(!vis[j]&&dist[j]<mindist){
            minid=j;mindist=dist[j];
        }
    }
    if(minid==-1)return false;
    _for(j,0,grid[i].size()){
        int next=grid[i][j].id;
        if(!vis[next]&&dist[j]>grid[i][j].len)
            dist[j]=grid[i][j].len;
    }
}
```

* 堆优化

```cpp
const int MAXN=10005;
#define inf 0x7fffffff
typedef long long LL;
#define _for(i,lo,hi) for(int i=(lo);i<(hi);i++)
int N,M;

struct edge{
	int id,len;
	edge(int i,int l):id(i),len(l){}
	bool operator<(const edge& b)const{return len>b.len;}//重要
};
vector<int> w(MAXN,inf);//到该点的边的长度
vector<bool> vis(MAXN,false);
vector<edge> G[MAXN];

void Prime(){
	priority_queue<edge> pq;
    w[0]=0;
	pq.push(edge(0,0));
	while(!pq.empty()){
		edge e=pq.top();pq.pop();
		int nc=e.id;
		if(vis[nc])continue;
		vis[nc]=true;
		_for(i,0,G[nc].size()){
			int nx=G[nc][i].id,len=G[nc][i].len;
			if(vis[nx])continue;
			if(w[nx]>len){
				w[nx]=len;
				pq.push(edge(nx,w[nx]));
			}
		}
	}
}
```

## kruskal算法（克鲁斯卡尔）

* 边贪心算法，将所有的边建堆，每次取出权值最小的边，如果其两个端点在不同的**连通域**中，则将其包含在最小生成树中。是否在同一连通域可以用并查集来解决
* 复杂度：主要来源在于边排序O(ElogE)，然后检测两个端点是否在同一连通域，
* 对边排序，然后从小到大遍历一遍即可。遍历过程中判断该边的两个顶点是否属于同一个并查集，不属于的话将该边加入到最小生成树，将两个顶点合并。
* 也是仅适用于无向图
* 总结，稠密图用prim，稀疏图用kruskal

```cpp
struct edge{
    int len,c1,c2;
    bool operator<(dege& b){
        return len<b.len;
    }
};
vector<edge> grid;
int kruskal(int n,int m){//顶点数，边数
    int ans=0,edge_num=0;
    _for(i,0,m){
        int f1=findroot(grid[i].c1),f2=findroot(grid[i].c2);
        if(f1!=f2){
            fa[f1]=f2;
            ans+=grid[i].len;
            edge_num++;
        }
    }
    if(edge_num!=n-1)return -1;
    else return ans;
}
```

# 5 拓扑排序
1. 定义一个队列，将图中入度为零的节点入队，并将节点标记
2. 每出队一个节点，将与其相连的节点的入度减一，将入度为零的节点标记并入队
3. 指导队列为空，但是，如果队列为空时，还有节点未标记，则存在环

用DFS也可以实现拓扑排序，而且算法更加简洁，如果只需要求拓扑排序序列或者逆拓扑排序序列的时候，这种方法更加简洁明了

```cpp
//拓扑排序,每次让入度为0的节点入队，而不是让队列中节点的后继节点入队
vector<int> vec(x);
int lo=0,hi=0;//[lo,hi)队列
_for(i,0,x){
	if(!inD[i]){vec[hi++]=i;}
}
while(lo<hi){
	int c=vec[lo++];
	_for(i,0,post[c].size()){
		int next=post[c][i].id;
		inD[next]--;
		if(inD[next]==0)vec[hi++]=next;
	}
}
```

# 6 关键路径

## AOE网关键路径，即有向无环图DAG最长路径

* 用e[r]/l[r]表示该边的最早开始时间和最晚开始时间
* 用ve[i]/vl[i]表示该点的最早开始时间和最晚开始时间
* e[r]最早开始时间=max{直接前驱活动的最早开始时间-该前驱活动的时间}
* l[r]最晚开始时间=min{直接后继的最晚开始时间+该后继活动的时间}
* 所以，可以求vl[r]/ve[r]来间接求出e[r]/l[r]
* ve[i]/vl[i]可以分别通过拓扑排序和逆拓扑排序得到

```cpp
//拓扑排序,每次让入度为0的节点入队，而不是让队列中节点的后继节点入队
vector<int> vec(x);
int lo=0,hi=0;//[lo,hi)队列
_for(i,0,x){
	if(!inD[i]){vec[hi++]=i;}
}
while(lo<hi){
	int c=vec[lo++];
	_for(i,0,post[c].size()){
		int next=post[c][i].id;
		inD[next]--;
		if(inD[next]==0)vec[hi++]=next;
	}
}
//根据拓扑排序序列计算最早开始时间
vector<int> A(x,0);//队首节点的最早开始时间为0
_for(i,0,x){
	int c=vec[i];
	_for(j,0,post[c].size()){
		int next=post[c][j].id,len=post[c][j].len;
		A[next]=max(A[next],A[c]+len);
	}
}
//根据逆拓扑排序时间计算最晚开始时间
//其实这里不需要用pre存前驱节点，直接根据每个节点的后继节点也可以更新当前节点的最晚开始时间
vector<int> B(x,A[vec[x-1]]);//汇点的最晚开始时间等于最早开始时间
_for(i,0,x){
	int c=vec[x-1-i];
	_for(j,0,pre[c].size()){
		int next=pre[c][j].id,len=pre[c][j].len;
		B[next]=min(B[next],B[c]-len);
	}
}
```

## 最长路径

* 最长简单路径（无环）的求法：若均为正权边，将其乘以-1，然后用spfa或bellman-Ford算法求出最小值，然后乘以-1即为答案。
* 有向无环图图的最长路径求法可以直接用前面的关键路径法


# 7 DAG最长路

* 问题1：求整个DAG中的最长路径。

方法一：逆拓扑排序

* 那么，如果顶点i的所有后继节点的最长路径已知，则可求出dist[i]，关键是逆拓扑排序（其实就是拓扑排序的定点从栈里取出来的顺序）

关键：
1. map和数组建立char与int的对应关系，即ID函数
2. vector模拟队列，下标模拟进队出队，inDegree数组记录并更新入度，入度为0入队
3. 上述vector即为拓扑排序，然后逆拓扑排序将其从后向前取出即可
4. 逆拓扑排序时更新dist和path数组，dist[i]表示i顶点出发的最长路径，path[i]表示i顶点的后继顶点

方法二：动态规划

建立dp[i]数组，表示从顶点i出发能获得的最长路径，这样所有dp[i]的最大值就是DAG最长路。

详见动态规划专题。

* 问题2：固定终点，求DAG的最长路径

# 8 双联通域分解

割点也叫关节点，它的删除将导致连通域增加。不含任何关节点的图称为双联通图。任一无向图都可以视作由若干个极大的双联通子图组合而成，这样的每一个子图都称为原图的一个双联通域。

基于DFS可以高效的实现双联通域分解。DFS树中的叶节点不可能是关节点，DFS树根节点若至少拥有两个分支，则必是一个关节点。

分辨关节点：如果节点C的移除导致其某一课真子树与其真祖先无法联通，则C必为关节点。反之，若C的所有真子树都能与C的某一真祖先联通，则C不可能是关节点。

在原无向图的DFS树中，C的真子树只可能通过后向边与C的真祖先联通。因此，只要在DFS搜索过程记录并更新各顶点v所能（经由后向边）联通的最高祖先（highest connected ancestor,HCA）hca[v]，即可及时认定关节点，并报告对应的双连通域。

由于处理的是无向图，故DFS搜索在顶点v的孩子u出返回之后，通过比较hca[v]与dtime[v]的大小，即可判断v是否是关节点。这里将闲置的ftime[] 用做hca[] 。故若hca[u]>=dtime[v]，则说明u及其后代无法通过后向边与v的真祖先联通，故v为关节点。既然栈S存有搜索过的顶点，则该关节点相对应的双联通域内的顶点，此时都应集中存放在S顶部，故可依次弹出这些顶点。v本身必然最后弹出，作为多个连通域的连接枢纽，它应重新入栈。

反之，若hca[u] < dtime[v]，说明u可经过后向边连通至v的真祖先。果真如此，则这一性质对v同样适用，故有必要将hca[v]，更新为hca[v]和hca[u]之间的更小者。

当然，没遇到一条后向边(v,u)，也将及时地将hca[v]，更新为hca[v]与dtime[u]之间的更小者，以保证hca[v]能够始终记录顶点v可经后向边向上联通的最高祖先。

```cpp
void bcc(int s){//基于DFS的BCC分解算法
    int clock=0;
    int v=s;
    stack<int> S;
    do{
        if(status(v)==UNDISCOVERED){//发现为发现的顶点（新联通分量）
            BCC(v,clock,S);//即从该定点出发启动一次BCC
            S.pop();//遍历返回后，弹出栈中最后一个顶点：当前连通域的起点。

        }
    }while(s!=(v=(++v%n)));
}
#define hca(x) (fTime(x))

void BCC(int v,int& clock,stack<int>& S){
    hca(v)=dTime(v)=++clock;
    status(v)=DISCOVERED;
    S.push(v);//顶点v被发现并入栈
    for(int i=0;i<grid[v].size();i++){
        int u=grid[v][i];
        switch(status(u)){
            case UNDISCOVERED:
                parent(u)=v;status(v,u)=TREE;BCC(u,clock,S);//从定点u深入
                if(hca(u)<dTime(v)){//遍历返回后，若发现u(通过后向边)可指向v的真祖先
                    hca(v)=min(hca(v),hca(u));                    
                }else{
                    while(v!=S.pop());//依次弹出当前BCC的节点，亦可根据实际需求转存至其他结构
                    S.push(v);//最后一个顶点(关节点)重新入栈-总计至多两次
                }
                break;
            case DISCOVERED:
                status(v,u)=BACKWARD;
                if(u!=parent(v))hca(v)=min(hca(v),dTime(u));//更新hca[v]越小越好
                break;
            default:
                status(v,u)=(dTime(v)<dTime(u))?FORWARD:CROSS;
                break;
        }
    }
    status(v)=VISITED;//对v的访问结束
}
```

# 9 二分图最大匹配

二分图又称作二部图，是图论中的一种特殊模型。 设G=(V,E)是一个无向图，如果顶点V可分割为两个互不相交的子集(A,B)，并且图中的每条边（i，j）所关联的两个顶点i和j分别属于这两个不同的顶点集(i in A,j in B)，则称图G为一个二分图。

给定一个二分图G，在G的一个子图M中，M的边集{E}中的任意两条边都不依附于同一个顶点，则称M是一个匹配。

极大匹配(Maximal Matching)是指在当前已完成的匹配下,无法再通过增加未完成匹配的边的方式来增加匹配的边数。最大匹配(maximum matching)是所有极大匹配当中边数最大的一个匹配。选择这样的边数最大的子集称为图的最大匹配问题。

匈牙利算法的本质是DFS。
[CSDN趣写算法](https://blog.csdn.net/sunny_hun/article/details/80627351)

```cpp
bool find(int x){
	int i,j;
	for (j=1;j<=m;j++){    //扫描每个妹子
		if (line[x][j]==true && used[j]==false)      
		//如果有暧昧并且还没有标记过(这里标记的意思是这次查找曾试图改变过该妹子的归属问题，但是没有成功，所以就不用瞎费工夫了）
		{
			used[j]=1;
			if (girl[j]==0 || find(girl[j])) {
				//名花无主或者能腾出个位置来，这里使用递归
				girl[j]=x;
				return true;
			}
		}
	}
	return false;
}
//在主程序我们这样做：每一步相当于我们上面描述的一二三四中的一步
for (i=1;i<=n;i++){
	memset(used,0,sizeof(used));    //这个在每一步中清空
	if find(i) all+=1;
}
```

# 10 欧拉图、欧拉路径

1. 定义
* 欧拉通路 (Euler tour)——通过图中每条边一次且仅一次，并且过每一顶点的通路。
* 欧拉回路 (Euler circuit)——通过图中每条边一次且仅一次，并且过每一顶点的回路。
* 欧拉图——存在欧拉回路的图。
2. 无向图是否具有欧拉通路或回路的判定
* G有欧拉通路的充分必要条件为：G 连通，G中只有两个奇度顶点(它们分别是欧拉通路的两个端点)。
* G有欧拉回路(G为欧拉图)：G连通，G中均为偶度顶点。
3. 有向图是否具有欧拉通路或回路的判定
* D有欧拉通路：D连通，除两个顶点外，其余顶点的入度均等于出度，这两个特殊的顶点中，一个顶点的入度比出度大1，另一个顶点的入度比出度小1。
* D有欧拉回路(D为欧拉图)：D连通，D中所有顶点的入度等于出度。

欧拉路径 Hierholzer算法（逐步插入回路法），即DFS+回溯,O(n+E)

```cpp
/*Hierholzer算法自动寻找欧拉回路，在找不到欧拉回路的情况下会找到欧拉路径。前提是得给它指定好起点。
算法流程（无向图）：
1. 判断奇点数。奇点数若为0则任意指定起点，奇点数若为2则指定起点为奇点。
2. 开始递归函数Hierholzer(x):
　　循环寻找与x相连的边(x,u):
　　　　删除(x,u)
　　　　删除(u,x)
　　　　Hierholzer(u);
　　将x插入答案队列之中；【注意，这里必须是先遍历，最后再插入节点，也称回溯】
3. 倒序输出答案队列

因为要删除边，所以用领接表存储图结构，而且要保证删除边在O(1)内完成*/
const int MAXN = 1005;

int G[MAXN][MAXN];//存图
int cnt[MAXN];//存每个点度的奇偶性
int N,M;//点个数，边条数
stack<int> S;//存路径

void dfs(int u){
    for(int v=1; v<=N; v++)
        if(G[u][v]){
            G[u][v]-=1;
            G[v][u]-=1;
            dfs(v);
            //不用恢复边！
        }
    S.push(u);//出栈时记录
}

inline void Print(){//输出路径
	if(!S.empty()){
	    printf("%d",S.top());
	    S.pop();
	}
	while(!S.empty()){
	    printf(" %d",S.top());
	    S.pop();
	}
	printf("\n");
}

inline void init(){
	memset(cnt,0,sizeof cnt);
	memset(G,0,sizeof G);
}

int main(){
    while(scanf("%d %d",&N,&M) == 2){
    	init();
    	int u,v;
	    for(int i=1 ; i<=M ; ++i){
	        scanf("%d %d",&u,&v);
	        G[u][v] += 1;
	        G[v][u] += 1;
	        cnt[u] ^= 1;//利用了异或运算，0表示度为偶数，1表示度为奇数。
	        cnt[v] ^= 1;
	    }
	    for(u=1; u<=N ; ++u){//注意判断图是否从1点开始
	        if(cnt[u]) break;
	    }
	    if(u == N+1) dfs(1);//都为偶节点，从随便一个开始都行
	    else dfs(u);//从奇节点开始
	    Print();
	}

    return 0;
}
```

4. 混合图。混合图也就是无向图与有向图的混合，即图中的边既有有向边也有无向边。
5. 混合图欧拉回路

* 混合图欧拉回路用的是网络流。

把该图的无向边随便定向，计算每个点的入度和出度。如果有某个点出入度之差为奇数，那么肯定不存在欧拉回路。因为欧拉回路要求每点入度 = 出度，也就是总度数为偶数，存在奇数度点必不能有欧拉回路。

现在每个点入度和出度之差均为偶数。将这个偶数除以2，得x。即是说，对于每一个点，只要将x条边反向（入>出就是变入，出>入就是变出），就能保证出 = 入。如果每个点都是出 = 入，那么很明显，该图就存在欧拉回路。
现 在的问题就变成了：该改变哪些边，可以让每个点出 = 入？构造网络流模型。有向边不能改变方向，直接删掉。开始已定向的无向边，定的是什么向，就把网络构建成什么样，边长容量上限1。另新建s和t。对于入 > 出的点u，连接边(u, t)、容量为x，对于出 > 入的点v，连接边(s, v)，容量为x（注意对不同的点x不同。当初由于不小心，在这里错了好几次）。之后，察看是否有满流的分配。有就是能有欧拉回路，没有就是没有。查看流值 分配，将所有流量非 0（上限是1，流值不是0就是1）的边反向，就能得到每点入度 = 出度的欧拉图。
由于是满流，所以每个入 > 出的点，都有x条边进来，将这些进来的边反向，OK，入 = 出了。对于出 > 入的点亦然。那么，没和s、t连接的点怎么办？和s连接的条件是出 > 入，和t连接的条件是入 > 出，那么这个既没和s也没和t连接的点，自然早在开始就已经满足入 = 出了。那么在网络流过程中，这些点属于“中间点”。我们知道中间点流量不允许有累积的，这样，进去多少就出来多少，反向之后，自然仍保持平衡。
所以，就这样，混合图欧拉回路问题，解了。

# 11 网络流


# 12 图中最小环
1. Floyd方法

[有向图无向图具体解法](https://wenku.baidu.com/view/d1031265657d27284b73f242336c1eb91a373384.html)

题目：vijos1046 观光旅游

* 对于有向图，可以令dp[i][i]初始化为inf，然后运行一次Floyd，最后查看dp[i][i]的最小值，即为最小环。（包括了A-B-A这种两点环路）
* 如果求的是最小环的路径长度，令dp[i][j]对应项初始化为相应路径长度，如果求的是最小环上的节点数，只需要dp[i][j]对应项初始化为1即可。
* 对于无向图，环中至少是三个
$$d_{ij}^k$$
定义为i,j定点之间，所有中间节点都在顶点(1,2,...,k)中的最短路径

```cpp
//map表示邻接矩阵，dist是dp数组
int floyd()
{
	int mincircle = INF;
	int Dist[n+1][n+1];
	for (int i = 1; i <= n; i++)
	for (int j = 1; j <= n; j++)
	{
		Dist[i][j] = map[i][j];
	}
	for(int k = 1; k <= n; k++)
	{
		for(int i = 1; i < k; i++)
		for(int j = i+1; j < k; j++)
		{
			mincircle = min(mincircle,Dist[i][j]+map[j][k]+map[k][i]);
		}
		for(int i = 1; i <= n; i++)
		for(int j = 1; j <= n; j++)
		{
			Dist[i][j] = min(Dist[i][j],Dist[i][k] + Dist[k][j]);
		}
	}
	return mincircle;
}
```

2. BFS法

[参考CSDN](https://blog.csdn.net/kk303/article/details/16917465)
* BFS从一点i开始，找包括i顶点在内的最小环，注意最先找到的环不一定是最小的环，同一层次上的点还是有先后搜索顺序的。依次从所有节点出发一遍，就可以得到最小环。记录各个BFS的层次，如果该层遍历的时候发现已有顶点且不是其上层顶点，即找到环，然后该层遍历结束后可得包括i顶点在内的最小环。
* 用BFS的方法,思路就是形成了环,则必然是搜索树上有了前向或者平行边..枚举每个点位根..做BFS..按照遍历的顺序给每个点标号..当找到一个已经标号的边..则知道形成了环..距离为dis[u]+dis[v]-1...但是这种方法只能求这种边权值都为1的最小环...加些条件就很容易出错了..而Floyd的方法适用范围更广

# 13 强连通分量tarjan算法

tarjan是一种快速求解图中所有的割点和割边的方法，仅通过一次DFS。

为了使这颗搜索树在遇到强连通分量的节点的时候能顺利进行。每个点都有两个参数。
1，DFN［］作为这个点搜索的次序编号（时间戳），简单来说就是 第几个被搜索到的。％每个点的时间戳都不一样％。
2，LOW［］作为每个点在这颗树中的，最小的子树的根，每次保证最小，like它的父亲结点的时间戳这种感觉。如果它自己的LOW［］最小，那这个点就应该从新分配，变成这个强连通分量子树的根节点。
ps：每次找到一个新点，这个点LOW［］＝DFN［］。

而为了存储整个强连通分量，这里挑选的容器是，堆栈。每次一个新节点出现，就进站，如果这个点有 出度 就继续往下找。直到找到底，每次返回上来都看一看子节点与这个节点的LOW值，谁小就取谁，保证最小的子树根。如果找到DFN［］＝＝LOW［］就说明这个节点是这个强连通分量的根节点（毕竟这个LOW［］值是这个强连通分量里最小的。）最后找到强连通分量的节点后，就将这个栈里，比此节点后进来的节点全部出栈，它们就组成一个全新的强连通分量。

伪代码如下：

```cpp
tarjan(u){
　　DFN[u]=Low[u]=++Index // 为节点u设定次序编号和Low初值
　　Stack.push(u)   // 将节点u压入栈中
　　for each (u, v) in E // 枚举每一条边
　　　　if (v is not visted) // 如果节点v未被访问过
　　　　　　　　tarjan(v) // 继续向下找
　　　　　　　　Low[u] = min(Low[u], Low[v])
　　　　else if (v in S) // 如果节点u还在栈内
　　　　　　　　Low[u] = min(Low[u], DFN[v])
　　if (DFN[u] == Low[u]) // 如果节点u是强连通分量的根
    　　repeat v = S.pop  // 将v退栈，为该强连通分量中一个顶点
    　　print v
　　until (u== v)
}
```

# 14. 网络流算法

实例：一个自来水管道输送系统，起点S，终点T，途中经过的管道都有一个最大的容量，问从S到T的最大水流量是多少？

## 最大流Ford-Fulkerson算法

首先定义增广路径：从S到T的一条简单路径（包括正向边和逆向边），如果路径上的边满足：正向边f(u,v)< c(u,v)，逆向边f(u,v)>0，则称该路径为一条增光路径。

[图解](https://wenku.baidu.com/view/7ed3c241a8956bec0975e32b.html)

定理：可行流为最大流，当且仅当不存在关于f的增广路径。

Ford-Fulkerson算法：
1. 如果存在增广路径，就找出一条增广路径（DFS、BFS）
2. 然后沿该条路径进行更新流量
