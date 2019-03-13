---
layout: post
title: PAT甲级考试最后一题总结
tags:
- DSA
categories: algorithm
description: 笔记
---


# 1004 Counting Leaves[树遍历]
给定一颗树，问每一层各有多少叶子结点。

采用邻接表的方式存储该树，用一个标志数组表示该层结束最方便。如果节点不多，并且序号连续固定（本题），可以用一个level数组来表示，记录每个节点的层次。
采用DFS或BFS的方法均可，DFS如果深度不大优先选取，因为代码较简洁。

```cpp
#define MAX 105
vector<vector<int> > grid(MAX);//存储树
vector<int> level(MAX,0);//节点的层数，0节点层数为0；
vector<int> levNum(MAX,0);//该层叶子结点数目
int maxLev;
#define _for(i,a,b) for(int i=(a);i<(b);i++)
int getint(){
	int x;scanf("%d",&x);return x;
}
void DFS(int id,int lev){
	if(grid[id].size()==0){
		levNum[level[id]]++;
	}else{
		if(lev+1>maxLev)maxLev=lev+1;
		_for(i,0,grid[id].size()){
			DFS(grid[id][i],level[grid[id][i]]=lev+1);
		}
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	int n,m;
	scanf("%d %d",&n,&m);
	int id,k;//后继个数、临时变量
	_for(i,0,m){
		scanf("%d %d",&id,&k);
		if(k!=0){
			_for(j,0,k){
				grid[id].push_back(getint());
			}
		}
	}
	//DFS
	maxLev=1;level[1]=1;
	DFS(1,1);
	printf("%d",levNum[1]);
	_for(i,2,maxLev+1)
		printf(" %d",levNum[i]);
	return 0;
}
```

# 1014 Waiting in Line[模拟]
某银行有N个窗口，每个窗口前可以排M个人。现在有K个客户需要服务，每位客户的服务时长已知。假设所有客户在8:00按客户编号次序排在黄线外，每个窗口的人数少于M时，黄线外的客户就去补齐。给出Q个查询，输出查询客户的办理结束时间。

模拟，用数组记录每个客户的办理开始时间，用分钟记录，如果该客户的开始时间在17:00及以后，则不予办理。

易错点：Note that since the bank is closed everyday after 17:00, for those customers who cannot be served before 17:00, you must output Sorry instead.这句话含义不清楚啊，导致三个算例没过，be servered原来指的是开始服务的时间，很容易当做是结束服务的时间，从而导致出错.其实用脑子想象也应该不是结束服务的时间，总不能服务一半吧

```cpp
//这个代码看起来太繁杂了，用优先级队列看起来更好。但是本题中只有20个窗口，每次选择最小窗口时，直接遍历两次更加简单。本代码中给每个窗口包含一个队列，采用了静态数组队列的方法，其实不如用stl的队列，则写起来更加简洁。

int main(){
	//freopen("d:\\input.txt","r",stdin);
	int N,M,K,Q;
	scanf("%d %d %d %d",&N,&M,&K,&Q);
	vector<int> process(K+1);//process[i]是id=i的顾客办理业务所花时间
	vector<int> time(K+1);//time[i]是id=i的顾客办理完业务的时间，其实记录开始时间更好，就不用下面的vis数组了
	vector<bool> vis(K+1,false);//vis[i]表示id=i的顾客可以被服务
	_for(i,1,K+1)scanf("%d",&process[i]);
	vector<vector<int> > myq(N,vector<int>(1005,0)); //黄线以内的队列,为了记录历史队列信息，用数组模拟队
	vector<int> front(N,0),back(N,0);//记录各个队列的队首和队尾，[front,back)，队空是front==back,队中元素个数为back-front

	//初始化，所有黄线内队列入满
	int id=1;//1号客户准备入队
	while(back[0]-front[0]<M&&id<=K){
		_for(i,0,N){
			//printf("i=%d front=%d back=%d\n",i,front[i],back[i]);
			if(back[i]-front[i]<M&&id<=K){
				//一旦入队，办理业务的时间也就确定了
				int tmp=back[i]==0?0:time[myq[i][back[i]-1]];
				if(tmp<(17-8)*60)vis[id]=true;
				time[id]=tmp+process[id];
				//printf("time[%d]=%d\n",id,time[id]);
				myq[i][back[i]++]=id;
				//printf("i=%d id=%d\n",i,id);
				id++;
			}
		}
	}
	while(id<=K){//说明黄线外队列还有人，需要模拟出队
		//遍历队首，让最早办完的人出队
		int minque=0;
		_for(i,1,N){
			if(time[myq[i][front[i]]]<time[myq[minque][front[minque]]])minque=i;
		}
		front[minque]++;
		//printf("minque=%d\n",minque);
		int tmp=back[minque]==0?0:time[myq[minque][back[minque]-1]];
		if(tmp<(17-8)*60)vis[id]=true;
		time[id]=tmp+process[id];
		//printf("time[%d]=%d\n",id,time[id]);
		myq[minque][back[minque]++]=id;
		id++;
	}
	_for(i,0,Q){
		scanf("%d",&id);
		if(vis[id]){
			printf("%02d:%02d\n",time[id]/60+8,time[id]%60);
		}else printf("Sorry\n");
	}
	return 0;
}
```



# 1018 Public Bike Management[最短路径]
城市里有一些公共自行车站，每个车站的自行车最大容量为一个偶数Cmax，且如果一个车站中自行车的数量恰好是Cmax/2，那额称该车站处于完美状态。而如果一个车站是满的或空的，那么控制中心PBMC就会携带或从路上手机一定数量的自行车前往该车站，以使该问题车站都达到完美状态。现在给出Cmax，车站数目N（不含控制中心），问题车站编号Sp，无向边数M及边权，求一条从PBMC到达问题车站的最短路径，输出需要从PBMC携带的自行车数目、最短路径、到达问题车站后需要带回的自行车数目。如果路径有多条，选择从PBMC带回的自行车数目最少的，如果仍然有多条，那么选择最后从问题车站带回的自行车数目最少的。注意：沿途所有车站的调整过程必须在前往问题车站的过程中就调整完毕，带回时不再调整。

Dijkstra记录所有的最短路径，然后DFS找出符合条件的最短路径。

```cpp
struct node{int x,len;node(int xx,int ll):x(xx),len(ll){}};
vector<vector<node> > grid(MAX);//节点标号为1-N,从0出发
vector<vector<int> > path(MAX);
vector<int> vec,minpath;//记录中间路径,最短路径
int minwei=inf,minremain=inf;
int Cmax,N,Sp,M;//含义与题目同
vector<int> dist(MAX,inf),vis(MAX,false),weight(MAX);

void Dijkstra(){//x顶点到其他定点的单源最短路径
	int id,len,n;
	_for(k,0,N+1){
		id=-1;len=inf;
		//找到未发现的定点中的最近顶点，加入已发现集合
		_for(i,0,N+1){
			if(!vis[i]&&dist[i]<len){
				len=dist[i];
				id=i;
			}
		}
		if(id==-1)return;
		//从该定点更新状态数组
		vis[id]=true;
		n=grid[id].size();
		int nextid;
		_for(i,0,n){
			nextid=grid[id][i].x;len=grid[id][i].len;//分别表示顶点和长度
			if(!vis[nextid]){
				if(dist[nextid]>dist[id]+len){
					dist[nextid]=dist[id]+len;
					path[nextid].clear();
					path[nextid].push_back(id);
				}else if(dist[nextid]==dist[id]+len){
					path[nextid].push_back(id);
				}
			}
		}
	}
}
//遍历path向量，在最短路径中寻找
//这里每个节点需要两个标记，到达该节点需要带来的数目，从该节点需要带走的数目
//两者可能均不为0，比如前两个节点为-5，10，从0节点出发需要带5个，而从2节点返回时带10个，返回到0节点也要带上这10个
void DFS(int id,int wei,int remain){//Wei表示在id节点需要带来的数目，remain表示在id节点需要带走的数目
	if(id==0){
		if(wei<minwei){
			minwei=wei;
			minremain=remain;
			minpath=vec;
		}else if(wei==minwei&&remain<minremain){
			minremain=remain;
			minpath=vec;
		}
		return;
	}
	_for(i,0,path[id].size()){
		int next=path[id][i];
		vec.push_back(next);
		if(weight[next]>=0){
			if(weight[next]>=wei)
				DFS(next,0,remain+weight[next]-wei);
			else
				DFS(next,wei-weight[next],remain);
		}else{
			DFS(next,wei-weight[next],remain);
		}
		vec.pop_back();
	}
}

int main(){
    //freopen("d:\\input.txt","r",stdin);
    scanf("%d %d %d %d",&Cmax,&N,&Sp,&M);
	_for(i,1,N+1){
		weight[i]=getint()-Cmax/2;//输入时直接减去，后面可根据其正负判断
	}
    int c1,c2,len;
    for(int i=1;i<=M;i++){
        scanf("%d %d %d",&c1,&c2,&len);
        grid[c1].push_back(node(c2,len));
        grid[c2].push_back(node(c1,len));
    }
	//从0节点计算单源最短路径
	dist[0]=0;
	Dijkstra();
	//测试中居然还存在Sp节点不是问题节点的情况，所以，初始化进入DFS要分此两种情况
	if(weight[Sp]<0)
		DFS(Sp,-weight[Sp],0);
	else
		DFS(Sp,0,weight[Sp]);
	printf("%d ",minwei);
	for(int i=minpath.size()-1;i>=0;i--){
		printf("%d->",minpath[i]);
	}
	printf("%d ",Sp);
	printf("%d\n",minremain);
    return 0;
}
```

# 1022
# 1026 Table Tennis[模拟]
有K张乒乓球桌（编号1-K）于8:00-21:00开放，每对球员到达时总是选择当前空闲的最小球桌进行训练，且训练时长超过2h的会被强制压缩为2h，如果到达的时候没有球桌空闲，则排队等待。使问题复杂的地方在于，这K张球桌里面有M张是VIP球桌，如果存在VIP球桌空闲，且排队等待队列中存在VIP球员，那么等待队列汇总的第一对VIP球员将前往编号最小的VIP球桌进行训练；如果等待队列中没有VIP球员，那么VIP球桌被当做普通球桌处理；如果当前没有VIP球桌空闲，那么VIP球员将被当做普通球员处理。

输出每对球员的到达时间，结束时间和等待时间。

如果不存在VIP球桌，这个题目就和1014一模一样了，相当于1014中的窗口队列大小为1。因为存在VIP球桌，对其单独处理即可。

先按照到达时间给球桌初始化，然后遍历球桌找到结束时间最早的球桌进行处理，然后判断该球桌是否是VIP以及队列中是否有VIP球员。


```cpp
struct person {
    int arrive, start, time;
    bool vip;
}tempperson;

struct tablenode {
    int end = 8 * 3600, num;
    bool vip;
};

bool cmp1(person a, person b) {    return a.arrive < b.arrive;}
bool cmp2(person a, person b) {    return a.start < b.start;}

vector<person> player;
vector<tablenode> table;

void alloctable(int personid, int tableid) {
    if(player[personid].arrive <= table[tableid].end)
        player[personid].start = table[tableid].end;
    else
        player[personid].start = player[personid].arrive;
    table[tableid].end = player[personid].start + player[personid].time;
    table[tableid].num++;
}

//该函数可以简单的表示出当前队列中是否有VIP存在
int findnextvip(int vipid) {
    vipid++;
    while(vipid < player.size() && player[vipid].vip == false) vipid++;
    return vipid;
}

int main() {
    int n, k, m, viptable;
    scanf("%d", &n);
    for(int i = 0; i < n; i++) {
        int h, m, s, temptime, flag;
        scanf("%d:%d:%d %d %d", &h, &m, &s, &temptime, &flag);
        tempperson.arrive = h * 3600 + m * 60 + s;
        tempperson.start = 21 * 3600;
        if(tempperson.arrive >= 21 * 3600) continue;
        tempperson.time = temptime <= 120 ? temptime * 60 : 7200;
        tempperson.vip = ((flag == 1) ? true : false);
        player.push_back(tempperson);
    }
    scanf("%d%d", &k, &m);
    table.resize(k + 1);
    for(int i = 0; i < m; i++) {
        scanf("%d", &viptable);
        table[viptable].vip = true;
    }
    sort(player.begin(), player.end(), cmp1);
    int i = 0, vipid = -1;
    vipid = findnextvip(vipid);
    while(i < player.size()) {
        int index = -1, minendtime = 999999999;
        for(int j = 1; j <= k; j++) {
            if(table[j].end < minendtime) {
                minendtime = table[j].end;
                index = j;
            }
        }
        if(table[index].end >= 21 * 3600) break;
        if(player[i].vip == true && i < vipid) {
            i++;
            continue;
        }
        if(table[index].vip == true) {
            if(player[i].vip == true) {
                alloctable(i, index);
                if(vipid == i) vipid = findnextvip(vipid);
                i++;
            } else {
                if(vipid < player.size() && player[vipid].arrive <= table[index].end) {
                    alloctable(vipid, index);
                    vipid = findnextvip(vipid);
                } else {
                    alloctable(i, index);
                    i++;
                }
            }
        } else {
            if(player[i].vip == false) {
                alloctable(i, index);
                i++;
            } else {
                int vipindex = -1, minvipendtime = 999999999;
                for(int j = 1; j <= k; j++) {
                    if(table[j].vip == true && table[j].end < minvipendtime) {
                        minvipendtime = table[j].end;
                        vipindex = j;
                    }
                }
                if(vipindex != -1 && player[i].arrive >= table[vipindex].end) {
                    alloctable(i, vipindex);
                    if(vipid == i) vipid = findnextvip(vipid);
                    i++;
                } else {
                    alloctable(i, index);
                    if(vipid == i) vipid = findnextvip(vipid);
                    i++;
                }
            }
        }
    }
    sort(player.begin(), player.end(), cmp2);
    for(i = 0; i < player.size() && player[i].start < 21 * 3600; i++) {
        printf("%02d:%02d:%02d ", player[i].arrive / 3600, player[i].arrive % 3600 / 60, player[i].arrive % 60);
        printf("%02d:%02d:%02d ", player[i].start / 3600, player[i].start % 3600 / 60, player[i].start % 60);
        printf("%.0f\n", round((player[i].start - player[i].arrive) / 60.0));
    }
    for(int i = 1; i <= k; i++) {
        if(i != 1) printf(" ");
        printf("%d", table[i].num);
    }
    return 0;
}
```

# 1030 Travel Plan[最短路径]
有N个城市（编号0~N-1）,M条道路（无向边），并给出M条道路的距离属性与花费属性。现在给定起点S与终点D，求从起点到终点的最短路径、最短距离及花费。注意如果有多条最短路径，则选择花费最小的那条。

典型的Dijkstra+第二边权的问题，保存另一个数组记录到该点的花费即可。

```cpp
struct node{
     int r,dist,cost;//可到达的下一个顶点和边权
     node(int rr,int dd,int cc):r(rr),dist(dd),cost(cc){}
};

int main(){
    //freopen("D:\\input.txt","r",stdin);
    int n,m,c1,c2;
    scanf("%d %d %d %d",&n,&m,&c1,&c2);
    vector<int> w(n,0);//所有前驱节点的第二维度和
    vector<bool> vis(n,false);
    vector<int> path(n,-1);
    vector<int> dist(n,inf);
    vector<vector<node>> grid(n);
    vector<int> num(n,0);//记录最短路径的数目

    int t1,t2,t3,t4;
    for(int i=0;i<m;i++){
        scanf("%d %d %d %d",&t1,&t2,&t3,&t4);
        grid[t1].push_back(node(t2,t3,t4));
        grid[t2].push_back(node(t1,t3,t4));
    }
    dist[c1]=0;num[c1]=1;
    for(int i=0;i<n;i++){//每次循环，添加进一个节点，由于令dist[c1]为0，而其他均为inf，首先进入的节点必为c1
        int id=-1,dis=inf;//下一个最近节点的id和距离
        for(int j=0;j<n;j++){
            if(!vis[j]&&dist[j]<dis){dis=dist[j];id=j;}
        }
        if(id==-1)return 0;//说明非连通图
        vis[id]=true;
        for(int j=0;j<grid[id].size();j++){
            int r=grid[id][j].r,d=grid[id][j].dist,c=grid[id][j].cost;
            if(!vis[r]&&d!=inf){

                if(dist[id]+d<dist[r]){
                    path[r]=id;
                    dist[r]=dist[id]+d;
                    w[r]=w[id]+c;
                    num[r]=num[id];
                }else if(dist[id]+d==dist[r]){
                    if(w[r]>w[id]+c){
                        w[r]=w[id]+c;
                        path[r]=id;
                    }
                    num[r]+=num[id];//找到相同长度的线路
                }
            }
        }
    }
    printpath(c2,path);
    printf("%d %d",dist[c2],w[c2]);
    return 0;
}
```

# 1034 Head of a Gang[BFS图遍历]
给出若干人的通话长度（视为无向边），按照这些通话将他们分为若干个组。每个组的总边权设为该组内的所有通话的长度之和，而每个人的点权设为该人参与的通话长度之和。现在给定一个阈值K，且只要一个组的总边权超过K，并满足成员人数超过2，则将该组视为“Gang”，而该组内点权最大的人视为头目。要求输出Gang的个数，并按照头目姓名字典序从小到大的顺序输出每个Gang的头目姓名和成员人数。

BFS遍历获得连通块的个数以及总边权和头目、成员人数。名字与节点的对应关系map，最后答案要求按照字母序，显然用map

```cpp
int main(){
    //freopen("D:\\input.txt","r",stdin);
    int N,K;
    scanf("%d %d",&N,&K);
    //定义邻接表的最简单方法，最多有2*N个节点
    vector<int> grid[2*N];//这个写法就是邻接表的另外一种表达
    vector<int> weight(2*N,0);

    //名字与节点序号的对应关系用map和向量实现
    map<string,int> str2num;int hashrank=0;//记录成员的个数
    vector<string> num2str;
    //记录答案
    map<string,int> ans;

    string name1,name2;
    int time;
    //初始化
    for(int i=0;i<N;i++){
        cin>>name1>>name2>>time;//这里用cin也是没问题的
        //下面写为getID更好
        if(str2num.count(name1)==0){
            str2num[name1]=hashrank++;num2str.push_back(name1);
        }
        if(str2num.count(name2)==0){str2num[name2]=hashrank++;num2str.push_back(name2);}
        int t1=str2num[name1],t2=str2num[name2];
        grid[t1].push_back(t2);weight[t1]+=time;
        grid[t2].push_back(t1);weight[t2]+=time;
   }
    vector<bool> finded(hashrank,false);
    //BFS图遍历，在图遍历的过程中记录权值最大的点、成员个数和通话时间总和
    //和一般的BFS不同之处就是就加入了对于图中节点的处理
    int gangnum=0;//满足题意的连通域总数
    int sub_sum,sub_id,sub_p;//分别记录连通域的权值\最大值的id\成员个数
    for(int i=0;i<hashrank;i++){
        if(!finded[i]){//发现新连通域
            queue<int> q;//DFS队列
            q.push(i);
            finded[i]=true;sub_sum=weight[i];sub_id=i;sub_p=1;
            int cur;
            while(!q.empty()){
                cur=q.front();q.pop();
                for(int j=0;j<grid[cur].size();j++){
                    if(!finded[grid[cur][j]]){
                        finded[grid[cur][j]]=true;
                        sub_sum+=weight[grid[cur][j]];
                        if(weight[grid[cur][j]]>weight[sub_id])sub_id=grid[cur][j];
                        sub_p++;
                        q.push(grid[cur][j]);
                    }
                }
            }
            //cout<<num2str[i]<<" "<<weight[i]<<" "<<sub_p<<" "<<sub_sum<<endl;
            if(sub_p>2&&sub_sum/2>K){
                gangnum++;//连通域个数
                ans[num2str[sub_id]]=sub_p;
            }
        }
    }
    printf("%d\n",gangnum);
    for(map<string,int>::iterator it=ans.begin();it!=ans.end();it++){
        cout<<it->first<<" "<<it->second<<endl;
    }
    return 0;
}
```

# 1038
# 1045 Favorite Color Stripe[动态规划]
给出m中颜色作为主人公EVA喜欢的颜色以及顺序，然后给出一串长度为L的颜色序列。现在要除掉这个序列中EVA不喜欢的颜色，然后求剩余序列的一个子序列，使得这个子序列表示的颜色顺序符合EVA喜欢的颜色的顺序，且为所有满足这个条件的子序列中长度最长的子序列。

先将eva喜欢的颜色{2,3,1,5,6}映射为{0,1,2,3,4},可以在读入颜色序列的时候将不存在喜欢颜色中的不存入待处理序列。然后按照最长不下降子序列的方法处理就好。

```cpp
int main(){
     //freopen("d:\\CodeBlockSpace\\input.txt","r",stdin);
     int n,m,l;
     scanf("%d",&n);
     scanf("%d",&m);
     int pri[201];memset(pri,0,201*sizeof(int));
     //设置排序大小，不在喜欢列表中的元素为0
     int t;
     for(int i=0;i<m;i++){scanf("%d",&t);pri[t]=i+1;}
     scanf("%d",&l);
     int* num=new int[l];
     for(int i=0;i<l;i++)scanf("%d",num+i);

     //dp[i]表示以i结尾的最长不降子序列的长度
     int dp[l];
     int i=0;
     while(num[i]==0)dp[i]=0;
     dp[i]=1;
     int _max=0;
     while(i<l){
         if(pri[num[i]]==0)dp[i]=0;
         else{
             dp[i]=1;
             for(int j=0;j<i;j++){
                 if(pri[num[i]]>=pri[num[j]])dp[i]=max(dp[i],dp[j]+1);
             }
         }
        _max=max(_max,dp[i]);
        i++;
     }
     printf("%d",_max);
     return 0;
 }
//只将合法元素插入到序列中
	//freopen("D:\\input.txt","r",stdin);
	int N,M,maxsum=1,t;
	vector<int> hashtable(205,0),v,dp(10005);//dp[i]表示以i为后缀的最长不下降子序列的长度
	cin>>N;
	map<int,int> mmap;
	cin>>M;
	_for(i,0,M){cin>>t;hashtable[t]=i+1;}
	cin>>M;
	//剔除掉不喜欢的元素，然后按照最大递增子序列来做最方便
	_for(i,0,M)	{cin>>t;if(hashtable[t]!=0)v.push_back(hashtable[t]);}
	if(v.size()==0){printf("0");return 0;}
	dp[0]=1;
	_for(i,1,v.size()){
		dp[i]=1;//这里一定要赋初值1，否则会错误很多
		_for(j,0,i){
			if(v[i]>=v[j])dp[i]=max(dp[i],dp[j]+1);
		}
		maxsum=max(dp[i],maxsum);
	}
	printf("%d\n",maxsum);

```

# 1049


# 1053 Path of Equal Weight[树遍历]
给定一颗树和每个节点的权值，求所有从根节点到叶子节点的路径，使得每条路径上节点的权值之和等于给定的常数S。

同1004中树的存储方式，采用邻接表即可。因为题目需要存储路径，所以用DFS回溯，可以用数组存储路径，用push和pop的方法或者采用全局节点秩的方法。

注意，本题对于结果有字典序的要求，在DFS之前，将所有节点的孩子数组从小到大排列，然后DFS即可。

```cpp
#define MAX 105
vector<vector<int>> ans;//记录答案

//直接在这里定义节点数组，对于内部的孩子节点vector,可调用sort对其进行排序
struct node{
    int w;//权值
    vector<int> child;
}Node[MAX];

void DFS(int cur,vector<int>& sta,int key){
    if(Node[cur].child.size()==0){
        if(Node[cur].w==key){
            sta.push_back(Node[cur].w);
            ans.push_back(sta);
            sta.pop_back();
        }
        return;
    }
    sta.push_back(Node[cur].w);
    for(int i=0;i<Node[cur].child.size();i++){
        if(key>=Node[cur].w)DFS(Node[cur].child[i],sta,key-Node[cur].w);
    }
    sta.pop_back();
}

bool cmp(int a,int b){return Node[a].w>Node[b].w;}

int main()
{
    //freopen("d:\\input.txt","r",stdin);
    int N,M,Key;
    scanf("%d %d %d",&N,&M,&Key);
    for(int i=0;i<N;i++)scanf("%d",&(Node[i].w));
    int r,m,t;
    while(scanf("%d",&r)!=EOF){
        scanf("%d",&m);
        for(int i=0;i<m;i++){
            scanf("%d",&t);
            Node[r].child.push_back(t);
        }
        //因为这里是按照节点权值大小进行排序，所以要引入自定义排序函数cmp
        sort(Node[r].child.begin(),Node[r].child.end(),cmp);
    }
    int root=0;//根据题意，0节点为根节点

    vector<int> ch;//用向量模拟栈，进行深度优先搜索
    DFS(root,ch,Key);

    for(int i=0;i<ans.size();i++){
        for(int j=0;j<ans[i].size()-1;j++)
            printf("%d ",ans[i][j]);
        printf("%d\n",ans[i][ans[i].size()-1]);
    }
    return 0;
}
```

# 1057 Stack[树状数组]
给出一个栈的入栈、出栈的过程，并随时通过PeekMedian要求输出栈中中位数。

注意到输入栈的数字均为正整数且<=10^5，所以可以用树状数组来表示每个数字的个数。当PeekMedian命令时，因为树状数组存储的是各个数字的个数，用二分法找到getSum()>=N/2的最小数即可。

```cpp
const int MAXN=100005;
int A[MAXN],C[MAXN];
#define lowbit(i) (i&(-i))

//A[x]加上v
void updata(int x,int v){
	if(v==0)return;
	for(int i=x;i<MAXN;i+=lowbit(i)){
		C[i]+=v;
	}
}

//计算A[1]-A[x]的和
int getsum(int x){
	int sum=0;
	for(int i=x;i>0;i-=lowbit(i)){
		sum+=C[i];
	}
	return sum;
}

stack<int> s;

int getmid(){
	int k=(s.size()+1)/2;
	//求getsum(i)>=k的最小值
	int lo=1,hi=MAXN;
	while(lo<hi){//[lo,hi)
		int mid=(lo+hi)/2;
		if(getsum(mid)>=k){
			hi=mid;
		}else{
			lo=mid+1;
		}
	}
	return lo;
}
int main(){
    //freopen("d:\\input.txt","r",stdin);
	int N,t;
	cin>>N;
	char op[15];
	int top=0;
	_for(i,0,N){
		scanf("%s",op);
		switch(op[1]){
		case 'u':
			scanf("%d",&t);
			//A[t]++;
			updata(t,1);
			s.push(t);
			top++;
			break;
		case 'o':
			if(s.empty())printf("Invalid\n");
			else {
				t=s.top();
				//A[t]--;
				updata(t,-1);
				s.pop();
				printf("%d\n",t);
			}
			break;
		case 'e':
			if(s.empty())printf("Invalid\n");
			else{
				t=getmid();
				printf("%d\n",t);
			}
			break;
		}
	}
	return 0;
}
```

# 1064
# 1068 Find More Conis[01背包]
有N枚硬币，给出每枚硬币的价值，现在要用这些硬币去支付价值为M的东西，问是否可以找到这样的方案，是的选择用来支付的硬币的价值正好是M。多种方式时选择字典序小的。

先将硬币按照价值 *逆序* 排序，然后用01背包的思路即可。需要注意的是，因为要输出选择的路径，所以要记录下每次状态转移选取的策略。

01背包中用dp[i][v]表示容量是v的背包中在前i件物品中选择的最大的价值。将背包的价值和重量等同，那么，dp[N][M]==M表示有解，否则无解。状态转移方程是`dp[i][v]=max{dp[i-1][v],dp[i][v-w[i]]+c[i]}`。开一个二维数组,choice[i][v]=0表示不选择i物品，choice[i][v]=1表示选择i物品，即分别对应状态转移方程的两种情况。相等时，说明可以选择更小的元素，即选当前元素。当然，这个选择的前提是硬币的价值逆序排列。

```cpp
#define MAXN 10004
#define MAXM 102
int dp[MAXN][MAXM];
bool choice[MAXN][MAXM];
int N,M;
vector<int> v(MAXN),ans;

int main(){
    //freopen("d:\\input.txt","r",stdin);
	cin>>N>>M;

	_for(i,1,N+1){
		scanf("%d",&v[i]);
	}
	sort(v.begin()+1,v.begin()+N+1,greater<int>() );
	_for(i,0,N+1)dp[i][0]=0;
	_for(j,0,M+1)dp[0][j]=0;
	_for(i,1,N+1){
		_for(j,1,M+1){
			if(j<v[i]){
				dp[i][j]=dp[i-1][j];
				choice[i][j]=false;
			}else{
				if(dp[i-1][j-v[i]]+v[i]>=dp[i-1][j]){
					choice[i][j]=true;
					dp[i][j]=dp[i-1][j-v[i]]+v[i];
				}else{
					choice[i][j]=false;
					dp[i][j]=dp[i-1][j];
				}
			}
		}
	}
	if(dp[N][M]!=M)printf("No Solution\n");
	else{
		int i=N,j=M;
		while(i>0&&j>0){
			if(choice[i][j]){
				ans.push_back(v[i]);
				j=j-v[i];i=i-1;
			}else{
				i--;
			}
		}
		printf("%d",ans[0]);
		_for(i,1,ans.size())printf(" %d",ans[i]);
		printf("\n");
	}
	return 0;
}
```

# 1072 Gas Station[最短路径]
有N所居民房、M个加油站待建点以及K条无向边。现在要从M个加油站待建点中选择一个来建造加油站，使得该加油站距离最近的居民房尽可能远，且必须保证所有的房子与该加油站的距离不超过给定的服务范围DS。现在给出N\M\K\DS，以及K条无向边的端点及边权，输出应当选择的加油站编号、与该加油站最近的居民房的距离、该加油站距离所有居民房的平均距离。如果有多个最近距离相等的解，那么选择平均距离最小的，如果平均距离也相同，则选择编号最小的。

对于每一个加油站，用Dijkstra求其与各个居民房的最短路径，然后根据题目条件判断取答案。

```cpp
struct node{
	int c;
	int len;
	node(int cc,int l):c(cc),len(l){}
};
int char2int(char* c,int n){
	int id=0,lo=0,st=0;
	if(c[0]=='G'){lo=1;st=n;}
	while(c[lo]!='\0'){
		id=id*10+c[lo]-'0';
		lo++;
	}
	return id+st;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    int n,m,K,D;
    scanf("%d %d %d %d",&n,&m,&K,&D);
	char s1[5],s2[5];
	vector<vector<node>> grid(n+m+1);
	int c1,c2,t;
	//这里的格式转换错了，因为k的取值范围是1-1000，应写成函数递归处理得到其值
	for(int i=0;i<K;i++){
		scanf("%s %s %d",s1,s2,&t);
		c1=char2int(s1,n);
		c2=char2int(s2,n);
		grid[c1].push_back(node(c2,t));
		grid[c2].push_back(node(c1,t));
	}
	//单元最短路径：迪杰斯特拉算法
	vector<bool> vis(n+m+1,false);
	vector<int> dist(n+m+1,inf);
	//vector<int> path(n+m+1,-1);
	vector<int> mindist;
	int solution=0;//记录可行方案数
	int length=inf,minid,lengthsub=0,minsum=inf;//所有方案中最短路径及其序号,及离加气站最近的距离
	int mxlen,minlen,sumlen;//一个方案中的最长路径和最短路径、路径和
	for(int i=n+1;i<=n+m;i++){//依次查询m个加油站到各个城市的最短路径是否符合题意，
		fill(vis.begin(),vis.end(),false);
		fill(dist.begin(),dist.end(),inf);
		dist[i]=0;
		for(int j=0;j<n+m;j++){
			int id=-1,mind=inf;//最小节点
			for(int k=1;k<=n+m;k++){
				if(!vis[k]&&dist[k]!=inf){
					if(dist[k]<mind){mind=dist[k];id=k;}
				}
			}
			if(id==-1){goto loop;}
			vis[id]=true;
			for(int k=0;k<grid[id].size();k++){
				int c=grid[id][k].c,len=grid[id][k].len;
				if(!vis[c]&&dist[c]>dist[id]+len){
					dist[c]=dist[id]+len;
				}
			}
		}
		mxlen=0;minlen=inf;sumlen=0;
		for(int j=1;j<=n;j++){
			sumlen+=dist[j];
			if(dist[j]>mxlen)mxlen=dist[j];
			if(dist[j]<minlen)minlen=dist[j];
		}

		if(mxlen<=D){
			solution++;
			if(lengthsub<minlen){length=mxlen;minid=i;lengthsub=minlen;mindist=dist;minsum=sumlen;}
			else if(lengthsub==minlen&&minsum>sumlen){minid=i;lengthsub=minlen;mindist=dist;minsum=sumlen;}
		}
		loop:;
	}
	if(solution==0)printf("No Solution");
	else{
		printf("G%d\n",minid-n);
		printf("%d.0 %.1f",lengthsub,(float)minsum/n);
	}
	return 0;
}
```

# 1076 ForWard on Weibo[BFS图遍历]
在微博中，每个用户都可能被其他用户关注。而当该用户发布一条信息时，他的关注者就可以看到这条信息并且选择是否转发它，且转发的信息也可以被转发者再次转发，但同一用户最多只转发该信息一次（信息的发布者不能转发该信息）。现在给出N个用户的关注情况以及一个转发层数上限L，并给出最初发布消息的用户编号，求在转发层上限内消息最多会被多少用户转发。

有转发层数限制，用BFS模板即可。注意到，当需要统计层次时，用广度优先搜索最佳，在每层末尾加标志元素以判断层数。也可以给每个节点增加层数变量，用一个数组即可实现，BFS的过程中设置层数，如果层数超限，就结束BFS

```cpp
int main(){
    //freopen("D:\\input.txt","r",stdin);
    int N,L;
    scanf("%d %d",&N,&L);
     //定义邻接表
    vector<vector<int>> grid(N+1);
    vector<bool> finded(N+1,false);
    int m,t;
    for(int i=1;i<=N;i++){
        scanf("%d",&m);
        for(int j=0;j<m;j++){
            scanf("%d",&t);
            grid[t].push_back(i);
        }
    }
    int K;scanf("%d",&K);//共K次查询
    for(int i=0;i<K;i++){
        int cur;scanf("%d",&cur);
        for(int j=0;j<N+1;j++)finded[j]=false;
        queue<int> q;//BFS队列
        int sum=0;//记录答案
        int level=0;
        q.push(cur);finded[cur]=true;
        q.push(0);//以0作为各层之间的标志位
        while(!q.empty()){
            cur=q.front();q.pop();
            if(cur==0){
                if(++level==L)break;
                q.push(0);
            }else{
                for(int k=0;k<grid[cur].size();k++){
                    if(!finded[grid[cur][k]]){
                        finded[grid[cur][k]]=true;
                        q.push(grid[cur][k]);
                        sum++;
                    }
                }
            }
        }
		printf("%d\n",sum);
    }
    return 0;
}
```

# 1080
# 1087 All Roads Lead To Rome[最短路径]
有N个城市，M条无向边。现在需要从某个给定的城市出发（除了起始城市外，其他每个城市都有一个幸福值），前往名为ROM的城市。给出每条边所需要的花费，求从起始城市出发，到达城市ROM所需要的最少花费，并输出最少花费的路径。如果这样的路径有多条，则选择路径上城市的幸福值之和最大的那条。如果路径仍然不唯一，则选择路径上城市的平均幸福值最大的那条。

最短路径+第二点权+第三路径上节点个数


方法一：标志数组

```cpp
int N,K,M;//记录最短路径的条数
struct node{
	int id,len;
	node(int i,int l):id(i),len(l){}
};

//下面对ID的处理很常用
map<string,int> ID;
vector<string> name;
int getID(string& str){
	if(ID.count(str))return ID[str];
	name.push_back(str);
	return ID[str]=name.size()-1;
}

vector<int> dist(MAXN,INF),weight(MAXN),w1(MAXN,0),w2(MAXN,INF),path(MAXN,-1),num(MAXN,0);//第一权值是获得的快感更多，第二权值是经过的城市更少
vector<bool> vis(MAXN,false);
vector<vector<node> > grid(MAXN);
void Dijkstra(int s){
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
			int next=grid[minid][i].id,len=grid[minid][i].len;
			if(vis[next])continue;
			if(dist[next]>dist[minid]+len){
				num[next]=num[minid];
				dist[next]=dist[minid]+len;
				w1[next]=w1[minid]+weight[next];
				w2[next]=w2[minid]+1;
				path[next]=minid;
			}else if(dist[next]==dist[minid]+len){
				num[next]+=num[minid];
				if(w1[next]<w1[minid]+weight[next]){
					w1[next]=w1[minid]+weight[next];
					w2[next]=w2[minid]+1;
					path[next]=minid;
				}else if(w1[next]==w1[minid]+weight[next]&&w2[next]>w2[minid]+1){
					w2[next]=w2[minid]+1;
					path[next]=minid;
				}
			}
		}
	}
}

int main() {
	//freopen("d:\\input.txt","r",stdin);
	string str1,str2;
	cin>>N>>K>>str1;
	int start=getID(str1),t;
	_for(i,1,N){
		cin>>str1>>t;
		weight[getID(str1)]=t;
	}
	_for(i,0,K){
		cin>>str1>>str2>>t;
		int c1=ID[str1],c2=ID[str2];
		grid[c1].push_back(node(c2,t));
		grid[c2].push_back(node(c1,t));
	}
	dist[start]=0;w1[start]=0;w2[start]=0;num[start]=1;
	Dijkstra(start);
	str1="ROM";
	int end=getID(str1);
	printf("%d %d %d %d\n",num[end],dist[end],w1[end],w1[end]/w2[end]);
	cout<<name[start];
	stack<int> ans;
	while(path[end]!=-1){
		ans.push(end);
		end=path[end];
	}
	while(!ans.empty()){
		cout<<"->"<<name[ans.top()];
		ans.pop();
	}
	cout<<endl;
	return 0;
}
```

方法二：DFS+Dijkstra如此复杂的判断条件，采用增加标志数组的方法容易出错。

```cpp
map<string,int> ID;
vector<string> name;
int getID(const string& s){
	if(ID.count(s)!=0)return ID[s];
	name.push_back(s);
	return ID[s]=name.size()-1;
}

struct node{
	int id,len;
	node(int id,int len):id(id),len(len){	}
};

int N,M,w;
vector<int> weight(MAXN);

vector<vector<node> > grid(MAXN);
vector<vector<int> > path(MAXN);//该节点的后续节点

vector<int> dist(MAXN,inf);
vector<bool> vis(MAXN,false);

vector<int> ans,tmp;
int cnt=0,ansHappiness=0;

void Dijkstra(){
	dist[0]=0;

	while(1){
		int minid=-1,t=inf;
		_for(i,0,N){
			if(!vis[i]&&dist[i]<t){
				minid=i;t=dist[i];
			}
		}
		if(minid==-1)break;
		vis[minid]=true;		
		_for(i,0,grid[minid].size()){
			int next=grid[minid][i].id,len=grid[minid][i].len;
			if(!vis[next]){
				if(dist[next]>dist[minid]+len){
					dist[next]=dist[minid]+len;
					path[next].clear();
					path[next].push_back(minid);
				}else if(dist[next]==dist[minid]+len){
					path[next].push_back(minid);
				}
			}
		}
	}
}

void DFS(int end){
	if(path[end].size()==0){
		cnt++;
		int tmpHappiness=0;
		_for(i,0,tmp.size())tmpHappiness+=weight[tmp[i]];
		if(tmpHappiness>ansHappiness){
			ans=tmp;ansHappiness=tmpHappiness;
		}else if(tmpHappiness==ansHappiness&&tmp.size()<ans.size()){
			ans=tmp;
		}
		return;
	}
	_for(i,0,path[end].size()){
		tmp.push_back(path[end][i]);
		DFS(path[end][i]);
		tmp.pop_back();
	}
}

int main(){
//	freopen("d:\\input.txt","r",stdin);

	string s;
	cin>>N>>M>>s;
	getID(s);//出发点的ID为0
	_for(i,1,N){
		cin>>s>>w;
		weight[getID(s)]=w;
	} 	
	string s1,s2;
	int len;
	_for(i,0,M){
		cin>>s1>>s2>>len;
		int c1=getID(s1),c2=getID(s2);
		grid[c1].push_back(node(c2,len));
		grid[c2].push_back(node(c1,len));
	}

	int end=getID("ROM");

	Dijkstra();
	tmp.push_back(end);
	DFS(end);

	printf("%d %d %d %d\n",cnt,dist[end],ansHappiness,ansHappiness/(ans.size()-1));
	for(int i=ans.size()-1;i>0;i--){
		cout<<name[ans[i]]<<"->";
	}
	cout<<"ROM";
	return 0;
}
```

# 1091
# 1095
# 1099
# 1103
# 1107 Social Clusters[并查集]
有N个人，每个人喜欢若干项活动，如果两个人有任意一个活动相同，那么就称他们处于同一个社交网络。求N个人总共形成了多少个社交网络。



# 1111
# 1115
# 1119
# 1123
# 1127
# 1131
# 1135
# 1139
# 1143
# 1147
# 1151
