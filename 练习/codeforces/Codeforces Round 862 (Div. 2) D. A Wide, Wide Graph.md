# [Codeforces Round 862 (Div. 2) D. A Wide, Wide Graph](https://codeforces.com/contest/1805/problem/D)

## 题意：给出一个树，给出k，重新构造图，连接点u,v如果u,v距离至少为k，问这个k依次从1到n所产生的图的连通块的数量

## 思路：如果u,v距离为k，那么u,v点（相对于）之外的点都相互连接，形成一个大的连通块，从而可以推得这个点在图中有距离为k的点，那么这个点一定在这个大的连通块里，否则就为独立点，所以我们就要求出所有点到所有点的最大距离，所以我们只要在直径的两端都做以此dfs求深度，取最大值即可

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
struct edge{
	int v;
	edge* nex;
}ed[MAXN*2];
edge* head[MAXN];
int ptop = 0;
int ans[MAXN];
void add(int u,int v){
	ed[ptop].v = v;
	ed[ptop].nex = head[u];
	head[u] = &ed[ptop];
	ptop++;
}
struct pos{
	int x,dep;
}po;
bool use[MAXN];
void bfs()
{
	queue<pos> qu;
	qu.push({1,0});
	int y,x = 1,yy;//最后一个找到的
	while(!qu.empty()){
		x = qu.front().x;
		qu.pop();
		if(use[x])
			continue;
		yy = x;
		use[x] = 1;
		edge* p = head[x];
		while(p != NULL){
			y = p -> v;
			p = p -> nex;
			qu.push({y,0});
		}
	}
	memset(use,0,sizeof(use));
	int dep,ddep;
	qu.push({yy,0});
	while(!qu.empty()){
		int x = qu.front().x;
		dep = qu.front().dep + 1;
		qu.pop();
		if(use[x])
			continue;
		ddep = dep - 1;
		use[x] = 1;
		edge* p = head[x];
		while(p != NULL){
			y = p -> v;
			p = p -> nex;
			qu.push({y,dep});
		}
	}
	int l = ddep;
	memset(use,0,sizeof(use));
	qu.push({yy,0});
	while(!qu.empty()){
		int x = qu.front().x;
		dep = qu.front().dep;
		qu.pop();
		if(use[x])
			continue;
		yy = x;
		ans[x] = max(l-dep,dep);
		use[x] = 1;
		edge* p = head[x];
		while(p != NULL){
			y = p -> v;
			p = p -> nex;
			qu.push({y,dep + 1});
		}
	}
	memset(use,0,sizeof(use));
	qu.push({yy,0});
	while(!qu.empty()){
		int x = qu.front().x;
		dep = qu.front().dep;
		qu.pop();
		if(use[x])
			continue;
		ans[x] = max(max(l-dep,dep),ans[x]);
		use[x] = 1;
		edge* p = head[x];
		while(p != NULL){
			y = p -> v;
			p = p -> nex;
			qu.push({y,dep + 1});
		}
	}
}
int main()
{
	int n;cin >> n;
	for(int i = 1;i <= n - 1;i++){
		int u,v;cin >> u >> v;
		add(u,v);
		add(v,u);
	}
	bfs();
	map<int,int> mp;
	for(int i = 1;i <= n;i++)
		mp[ans[i] + 1]++;
	int tem = 0;
	for(int i = 1;i <= n;i++){
		tem += mp[i];
		if(tem + 1 <= n)
			cout << tem + 1 << ' ';
		else
			cout << n << ' ';
	}
}
```

