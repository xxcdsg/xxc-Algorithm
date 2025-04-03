## 单点修改，子树区间求和

### [DFS 序 1](https://loj.ac/p/144)

* 用dfs排序并求区域，这样每个子树都是一个连续的区域，用树状数组维护即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 1e6 + 10;
int n,m,r;
vector<int> edge[MAXN];
ll v[MAXN],tr[MAXN],_dfs[MAXN],pre[MAXN];
pair<ll,ll> range[MAXN];
int ptop = 0;
void dfs(int dad,int son){
	range[son].first = ptop + 1;
	for(auto v:edge[son]){
		if(v != dad){
			dfs(son,v);
		}
	}
	pre[++ptop] = son;
	_dfs[son] = ptop;
	range[son].second = ptop;
}
ll lowbit(ll x){
	return x&-x;
}
void build_tree(ll n){
	for(int i = 1;i <= n;i++){
		ll x = v[pre[i]];
		for(int j = i;j <= n;j+=lowbit(j)) tr[j] += x;
	}
}
void add(ll x,ll aim){
	for(;aim <= n;aim += lowbit(aim)) tr[aim] += x;
}
ll find(ll aim){
	ll ans = 0;
	for(;aim >= 1;aim -= lowbit(aim)) ans += tr[aim];
	return ans;
}
int main()
{
	cin >> n >> m >> r;
	for(int i = 1;i <= n;i++)	cin >> v[i];
	for(int i = 1;i <= n - 1;i++){
		int x,y;cin >> x >> y;
		edge[x].push_back(y);
		edge[y].push_back(x);
	}
	dfs(0,r);
	build_tree(n);
	for(int i = 1;i <= m;i++){
		int op;cin >> op;
		if(op == 1){
			int a,x;cin >> a >> x;
			add(x,_dfs[a]);
		}
		else{
			int a;cin >> a;
			cout << find(range[a].second) - find(range[a].first - 1) << endl;
		}
	}
}
```



