# 最短路径之Bellman-ford算法

#### 1.简单介绍一下Bellman-ford算法的基本思路：

Bellman-ford解决的是单源最短路径问题，边的权值可以为负值，并且可以判断存在负权环的存在。

如果存在负权环，最多循环V-1遍（因为V个节点情况下，最多V-1条边就可以把所有的节点串联起来），对于每层循环里面，就对所有的边进行松弛。最多进行v-1，就能把所有的边松弛完毕。

如果V-1遍松弛完成之后，仍然存在可以松弛的边，那么肯定就存在负权环，此时返回false退出就可以了

仍然采用链表前向星存图

```
bool bellman_fold(int s){
	for(int i = 0;i < 100;i++){
		dist[i] = inf;
	}
	dist[s] = 0;
	for(int i = 1;i < n;i++){
		for(int u = 1;u <= n;u++){
			for(int k = head[u];k!=-1;k = edge[k].ne){
				int v = edge[k].v;
				if(dist[u]!=inf&&dist[v]>dist[u]+edge[k].dis){
					dist[v] = dist[u]+edge[k].dis; 
				}
			}
		}
	}
	for(int u = 1;u <= n;u++){
		for(int k = head[u];k!=-1;k=edge[k].ne){
			int v = edge[k].v;
			if(dist[v]>dist[u]+edge[k].dis){
				return false;
			}
		}
	}
	return true;
}
```

在对所有的边进行松弛的情况下，我的代码还可以进行优化，因为我把每条边都存了两遍。

解决方法就是：存图的时候，直接进行存边（结构体里面包括两个端点和权值w），这样的话直接遍历所有的边就可以了。估计好一点点。

下面就是全部的代码：

```
#include <iostream>
#include <string>
#include <string.h>
#include <algorithm>
using namespace std;
const int maxn = 1e3;
const int inf = 0x3f3f3f3f;
int n,m;//n表示点数，m表示边数 
struct Edge{
	int v;
	int ne;
	int dis;
};
struct Edge edge[maxn];
int head[maxn];
int tot;
void init(){
	for(int i = 0;i < 100;i++){
		edge[i].ne = -1;
	}
	tot = 1;
	for(int i = 0;i<100;i++){
		head[i] = -1;
	}
}
void add(int u,int v,int dis){
	edge[tot].v = v;
	edge[tot].ne = head[u];
	edge[tot].dis = dis;
	head[u] = tot++;
}
int dist[maxn];
bool bellman_fold(int s){
	for(int i = 0;i < 100;i++){
		dist[i] = inf;
	}
	dist[s] = 0;
	for(int i = 1;i < n;i++){
		for(int u = 1;u <= n;u++){
			for(int k = head[u];k!=-1;k = edge[k].ne){
				int v = edge[k].v;
				if(dist[u]!=inf&&dist[v]>dist[u]+edge[k].dis){
					dist[v] = dist[u]+edge[k].dis; 
				}
			}
		}
	}
	for(int u = 1;u <= n;u++){
		for(int k = head[u];k!=-1;k=edge[k].ne){
			int v = edge[k].v;
			if(dist[v]>dist[u]+edge[k].dis){
				return false;
			}
		}
	}
	return true;
}
int main(){
	init();
	cin >> n >>m;
	for(int i = 1;i <= m;i++){
		int s,e,dis;
		cin >> s >> e >> dis;
		add(s,e,dis);
		add(e,s,dis);
	}
	if(bellman_fold(1)){
		for(int i = 1;i <= n;i++){
			cout << dist[i] << endl;
		}
	}
	else
	{
		cout << "存在负权环路" << endl;
	}
	return 0;
} 
```

