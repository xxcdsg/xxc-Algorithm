## dfs搜索，转移$sz[x] * sz[y](sz[x]不包括sz[y])$复杂度为$O(n^2)$

* 对于每个点来说，在该点被遍历的$sz[x]与sz[y]$不相交，在相交，也就是$x = lca(y,z)$的点被遍历，那么所有点总被转移遍历的数量就为$n^2$
* [M. Monster Hunter](https://codeforces.com/gym/102992/problem/M)
* 题意：只有打败父节点的怪物才能打倒子节点的怪物，打败该节点的怪物的花费为所有直接子节点的价值与该点的价值，问取掉0~n个节点后所需要的最小总需要花费
* 思路：每加入一个子树转移方程，只枚举到子树的大小与排除子树大小的主树大小，由于要根据自身来更新自己，先创建一个暂存数组建立，枚举完子树后再放回dp数组中

$$
dp[0][x][i + j] = min(dp[0][x][i] + min(dp[0][y][j],dp[1][y][j]))\\
dp[1][x][i + j] = min(dp[1][x][i] + min(dp[0][y][j],dp[1][y][j] + v[y]))
$$

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 2e3 + 10;
const ll INF = 1e18;

vector<int> son[N];

ll dp[2][N][N],tem[2][N];
int v[N],sz[N];

void dfs(int x){
	sz[x] = 1;
	dp[1][x][1] = v[x];
	dp[0][x][0] = 0;
	for(int y : son[x]){
		dfs(y);
		for(int i = 0;i <= sz[x] + sz[y];i++)
			tem[0][i] = tem[1][i] = INF;
		for(int i = 0;i <= sz[x];i++)
			for(int j = 0;j <= sz[y];j++){
				tem[0][i + j] = min(tem[0][i + j],dp[0][x][i] + min(dp[1][y][j],dp[0][y][j]));
				tem[1][i + j] = min(tem[1][i + j],dp[1][x][i] + min(dp[1][y][j] + v[y],dp[0][y][j]));
			}
		for(int i = 0;i <= sz[x] + sz[y];i++){
			dp[0][x][i] = tem[0][i];
			dp[1][x][i] = tem[1][i];
		}
		sz[x] += sz[y];
	}
}

void init(int n){
	for(int i = 1;i <= n;i++){
		for(int j = 0;j <= n;j++)
			dp[0][i][j] = dp[1][i][j] = INF;
		son[i].clear();
	}
}

void test(){
	int n;cin >> n;
	init(n);
	for(int i = 2;i <= n;i++){
		int x;cin >> x;
		son[x].push_back(i);
	}
	for(int i = 1;i <= n;i++) cin >> v[i];
	dfs(1);
	for(int i = n;i >= 0;i--) cout << min(dp[0][1][i],dp[1][1][i]) << ' ';
	cout << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

