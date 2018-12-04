# 最短路径之dijkstra

#### 1.首先介绍一下简单思路：

准备S集合{a,b....}和U集合{d,e,f...}，S集合用来装已知到源s的最短路径的点，U集合用来装未知到源s的最短路径的点。

刚开始的时候S集合里面只有源s，因为现在已知的到源s 的最短路径的点只有源s，其最短路径为0。

每次都是从U集合中挑选一个点来加入S集合，规则是：U集合中的该点到源s的路径最短，并且从U集合中删除，然后对该点和该点相连的边进行松弛（所谓的松弛，假设该点为v，已知u与源s的最短路径，并且u->v,如果dist[v]>dist[u]+w(u,v)，那么对dist[v]进行更新，使他始终维持到源s的最短路径）.....不断进行下去，直到什么时候呢？对了，直到U集合中的元素全部到达S集合中，那么完成了。

#### 2.这个过程中哪些地方可以优化呢？首先选择存图的时候，可以使用邻接表来进行存储图，这种是最好的。但是这里使用链表前向星来进行存图，首先来学习一下什么时候链表前向星？

链表前向星就是使用结构体来存储每条边，对每条边进行编号，找到 时候直接利用边的编号来进行查找。

需要两个东西：

1.结构体数组edge，来存边（包括边的长度w，边的端点v，下条边的编号）

2.head[i]表示以i点为起点的第一条边的编号

```
struct Edge
{
	int v; 
	int dis;
	int ne;
};//结构体存储边
void init(){//初始化操作，head初始化为-1，表示下一个编号为NULL
	for(int i = 0;i < 100;i++){
		edge[i].ne = -1;
	}
	tot = 1;
	for(int i = 0;i < 100;i++){
		head[i] = -1;
	}
}
void add(int u,int v,int dis){//添加边操作
	edge[tot].v = v;//编号为tot的边的端点为v
	edge[tot].ne = head[u];//编号为tot的边的下个编号为head[u]
	edge[tot].dis = dis;//编号为tot距离为dis
	head[u]=tot++;//head[u]表示这条边编号，并且tot加一。采用的是头插法，每次插在第一条边那里
}
```

另外一个可以优化的地方就是查找U集合中离源s最近的点，这里可以使用优先队列进行优化。

现在来回忆一下优先队列，c++中有专门来实现好的priority_queue

```

```

还需要注意的一点是：

当某个点从U集合中移到S集合中的时候，并且对该点进行松弛完成的时候，记住要标记一下该点加入其中了，避免重复加入。这里只需要使用book数组进行标记一下就可以了。



最后放上自己调试了模板（注意有点问题：当多组输入的时候，没有进行相应的初始化操作）

```
#include <iostream>
#include <stdio.h>
#include <string>
#include <string.h>
#include <algorithm>
#include <queue>
using namespace std;
struct node{
	int key;
	int value;
	node(int key,int value){
		this->key = key;
		this->value = value;
	}
	bool operator < (const node &other) const {
		return value>other.value;
	}
};
priority_queue<node>q;
struct Edge
{
	int v; 
	int dis;
	int ne;
};
const int inf = 0x3f3f3f3f;
int dist[100];
struct Edge edge[100];
int head[100];
int tot = 0;
void init(){
	for(int i = 0;i < 100;i++){
		edge[i].ne = -1;
	}
	tot = 1;
	for(int i = 0;i < 100;i++){
		head[i] = -1;
	}
}
void add(int u,int v,int dis){
	edge[tot].v = v;
	edge[tot].ne = head[u];
	edge[tot].dis = dis;
	head[u]=tot++;
}
int book[100];
void dijkstra(int s){
	for(int i = 0;i < 100;i++){
		dist[i] = inf;
	}
	dist[s] = 0;
//	for(int i = head[s];i!=-1;i=edge[i].ne){
//		dist[edge[i].v] = edge[i].dis;
//	}
	q.push(node(s,0));
	while(!q.empty()){
		node n = q.top();
		q.pop();
		int v = n.key;
		int w = n.value;
		book[v] = 1;
		for(int i = head[v];i!=-1;i=edge[i].ne){
			int vv = edge[i].v;
			if(!book[vv]&&dist[vv]>dist[v]+edge[i].dis){
				dist[vv] = dist[v]+edge[i].dis;
				q.push(node(vv,dist[vv]));
			}
		}
	}
}
int main(){
	init();
	int nn,mm;
	cin >> nn >> mm;
	for(int i = 1;i <= mm;i++){
		int s,e,dis;
		cin >> s >> e >> dis;
		add(s,e,dis);
		add(e,s,dis);
	}
	dijkstra(1);
	for(int i = 1;i <= nn;i++){
		cout << dist[i] << endl;
	}
	return 0;
}
```

