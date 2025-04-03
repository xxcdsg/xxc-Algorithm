## [E. Wonderful Tree!](https://codeforces.com/contest/1987/problem/E)

> tags : 动态规划 ， 树

### 题意：

> 每次可给一个点 $a_i$ 增加 $1$
>
> 问使得整棵树满足 $a_v\leq \sum a_u,u为v的直接子节点$ ，需要的最少操作次数

### 思路：

* 我们称 $\sum a_u - a_v$ 的余量，也就是这个点 $v$ 至多加上该值，能不改变树下方的满足题目条件
* 当一个点的余量为负数时，我们就需要让子节点 $+1$ 直到其余量到 $0$
* 一个点 $+1$ ，会使得该点余量 $-1$ ，父节点余量 $+1$
* 那么这就相当于传递了一个 $+1$ 到上面去，操作次数就是传递的距离
* 每当我们处理完子树后，子树的所有点的余量都是正数或者 $0$ ，那么我们只要整合为一条链即可，因为可以直接传递上去
* 同时记录距离该点最近的到树底的距离，很明显，树底的点 $+1$ 不需要考虑下方，因此可以直接传递

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;
#define int long long

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

vector<vector<int>> ed;
int n;
vector<int> a;
vector<int> fa;
vector<int> dep;
void dfs(int x,int dad){
	fa[x] = dad;
	int mi = inf;
	bool f = 0;
	for(auto y : ed[x]){
		if(fa[x] == y) continue;
		f = 1;
		a[x] -= a[y];
		dfs(y,x);
		mi = min(dep[y],mi);
	}
	dep[x] = mi + 1;
	if(!f) dep[x] = 0;
//	a[x] = -a[x];
}

ll ans = 0;

vector<vector<int>> dp;

void dfs2(int x){
	dp[x] = vector<int>(dep[x] + 1);
	vector<int> temdp(dep[x] + 1);
	for(auto y : ed[x]){
		if(y == fa[x]) continue;
		dfs2(y);
		for(int j = 0;j <= dep[x] - 1;j++){
			dp[x][j + 1] += dp[y][j];
		}
	}
	if(a[x] < 0){
		dp[x][0] = -a[x];
	}else{
		for(int j = 0;j <= dep[x] - 1;j++){
			int tem = min(a[x],dp[x][j]);
			dp[x][j] -= tem;
			ans += tem * j;
			a[x] -= tem;
		}
		if(a[x] > 0){
			ans += dep[x] * a[x];
		}
	}
}

void test(){
	int n;
	cin >> n;
	a = vector<int>(n + 1);
	for(int i = 1;i <= n;i++) cin >> a[i];
	ed = vector<vector<int>>(n + 1);
	for(int i = 2;i <= n;i++){
		int v;cin >> v;
		ed[i].push_back(v);
		ed[v].push_back(i);
	}
	fa = vector<int>(n + 1);
	dep = vector<int>(n + 1,inf);
	dfs(1,1);
	dp = vector<vector<int>>(n + 1);
	ans = 0;
	dfs2(1);
	cout << ans << '\n';
}

signed main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

