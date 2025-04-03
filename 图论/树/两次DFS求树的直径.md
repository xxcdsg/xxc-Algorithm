# 两次DFS求树的直径
* 第一次DFS随便找一个点，然后找离他最远的点，便是直径的一个端点
* 找到一个端点后，再进行一次DFS便能找到树的直径了
[最远距离](https://www.acwing.com/problem/content/4802/)
```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
struct edge{
	int u,v;
	edge* nex;
}ed[MAXN * 2];
edge* head[MAXN];
int ptop = 0;
void addedge(int u,int v)
{
	edge* tem = head[u];
	ed[ptop].u = u,ed[ptop].v = v;
	head[u] = &ed[ptop];
	ed[ptop].nex = tem;
	ptop++;
}
int find_far()
{
	bool use[MAXN] = {0};
	use[1] = 1;
	queue<int> qu;
	qu.push(1);
	int aim;
	while(!qu.empty())
	{
		edge* t = head[qu.front()];
		qu.pop();
		while(t != NULL)
		{
			if(!use[t -> v])
			{
				qu.push(t -> v);
				aim = t -> v;
				use[t -> v] = 1;
			}
			t = t -> nex;
		}
	}
	return aim;
}
int find_dis()
{
	bool use[MAXN] = {0};
	int dp[MAXN] = {0};
	int begin = find_far();
	use[begin] = begin;
	queue<int> qu;
	qu.push(begin);
	int ma = 0;
	while(!qu.empty())
	{
		int x = qu.front();
		edge* t = head[qu.front()];
		qu.pop();
		while(t != NULL)
		{
			if(!use[t -> v])
			{
				qu.push(t -> v);
				ma = dp[t -> v] = dp[x] + 1;
				use[t -> v] = 1;
			}
			t = t -> nex;
		}
	}
	return ma;
}
int main()
{
	int n,m;cin >> n >> m;
	while(m--)
	{
		int u,v;cin >> u >> v;
		addedge(u,v);
		addedge(v,u);
	}
	cout << find_dis() << endl;
}
```