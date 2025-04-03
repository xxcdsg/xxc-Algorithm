# 最近公共祖先(LCA)

## 主要思路：一个节点的$2^n$的祖先是那个节点$2^{n-1}$的祖先的$2^{n-1}$的祖先

## 也就是说$f[x][n]=f[f[x][n-1]][n-1]$ 

## 还要计算$log_2$的值，记录深度

```c++
#include<bits/stdc++.h>
using namespace std;

const int MAXN = 5e5 + 10;

int fa[MAXN][30],lg[MAXN],dp[MAXN];

vector<int> ve[MAXN];//记录连接

void dfs(int son,int dad)
{
	fa[son][0] = dad;//第一个祖先
	dp[son] = dp[dad] + 1;
	for(int i = 1;i <= 29;i++) fa[son][i] = fa[fa[son][i - 1]][i - 1];
	int end = ve[son].size();
	for(int i = 0;i < end;i++)
		if(ve[son][i] != dad)
			dfs(ve[son][i],son);
}

void flg(int n) {
	for(int i = 1;i <= n;i++) lg[i] = lg[i - 1] + (i == (2 << lg[i - 1]));
}

int find(int x,int y)
{
	if(dp[x] < dp[y])
		swap(x,y);
	while(dp[x] != dp[y]) x = fa[x][lg[dp[x]-dp[y]]];
	if(x == y) return x;
	for(int i = 29;i >= 0;i--) if(fa[x][i] != fa[y][i]) {x = fa[x][i],y = fa[y][i];}
	return fa[x][0];
}

int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	int n,m,s;cin >> n >> m >> s;
	flg(n);
	for(int i = 1;i <= n - 1;i++)
	{
		int x,y;cin >> x >> y;
		ve[x].push_back(y);
		ve[y].push_back(x);
	}
	dfs(s,s);
	for(int i = 1;i <= m;i++)
	{
		int x,y;cin >> x >> y;
		cout << find(x,y) << endl;
	}
}
```

* 来个当板子

```cpp
class LCA_TREE{
public:
	vector<vector<int>> ed;
	vector<vector<int>> fa;
	vector<int> dep;
	int sz;
	int MAX_lg = 5;
	LCA_TREE(vector<vector<int>>& eed,int n){
		ed = eed;
		sz = n;
		fa = vector<vector<int>>(n + 1,vector<int>(MAX_lg + 1));
		dep = vector<int>(n + 1);
		dfs(1,1);
	}
	void dfs(int x,int dad){
		fa[x][0] = dad;
		dep[x] = dep[dad] + 1;
		for(int i = 1;i <= MAX_lg;i++){
			fa[x][i] = fa[fa[x][i - 1]][i - 1];
		}
	}
	int find(int u,int v){
		if(dep[u] < dep[v]) swap(u,v);
		for(int i = MAX_lg;i >= 0;i--){
			if(dep[fa[u][i]] >= dep[v]) u = fa[u][i];
		}
		for(int i = MAX_lg;i >= 0;i--){
			if(fa[u][i] != fa[v][i]){
				u = fa[u][i];
				v = fa[v][i];
			}
		}
		if(u != v) return fa[u][0];
		else return u;
	}
	bool in(int u,int x,int v){
		int ux = find(u,x),vx = find(v,x),uv = find(u,v);
		int dis1 = dep[u] + dep[x] - dep[ux] * 2;
		int dis2 = dep[v] + dep[x] - dep[vx] * 2;
		int dis3 = dep[u] + dep[v] - dep[uv] * 2;
		return dis1 + dis2 == dis3;
	}
};
```

