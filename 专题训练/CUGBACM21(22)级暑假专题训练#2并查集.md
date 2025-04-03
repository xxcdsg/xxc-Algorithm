## A-并查集

* 来个通用板子

```cpp
class Dsu{
public:
    vector<int> a;
    int sz;

    void init(){for(int i = 1;i <= sz;i++) a[i] = i;}
    void init(int n){for(int i = 1;i <= n;i++) a[i] = i;}

    Dsu(int n){a = vector<int>(n + 1); sz = n; init();}

    int size(){return sz;}

    int find(int x){ return x == a[x] ? x : a[x] = find(a[x]);}

    int merge(int x,int y){return a[find(x)] = find(y);}

    bool same_set(int x,int y){return find(x) == find(y);}
};
```

## [B - Equals](https://vjudge.net.cn/problem/AtCoder-arc097_b)

>给出多个 $(x_i,y_i)$ 使得 $p_{x_i}与p_{x_j}$ 可相互交换
>
>问最多能使得多少个 $p_i=i$

##### 思路：

* 因为是排列，因此不会出现抢同一个位置的情况，那么我们只要考虑 $p_i$ 能否到位置 $i$ 即可
* 那么我们用并查集得到 $i$ 能互通的集合，然后遍历 $p_i$ 是否在这个集合中即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

const int N = 1e5 + 10;
set<int> se[N];
class Dsu{
public:
    vector<int> a;
    int sz;

    void init(){for(int i = 1;i <= sz;i++) a[i] = i;}
    void init(int n){for(int i = 1;i <= n;i++) a[i] = i;}

    Dsu(int n){a = vector<int>(n + 1); sz = n; init();}

    int size(){return sz;}

    int find(int x){ return x == a[x] ? x : a[x] = find(a[x]);}

    int merge(int x,int y){return a[find(x)] = find(y);}

    bool same_set(int x,int y){return find(x) == find(y);}
};

void test(){
	int n,m;cin >> n >> m;
	Dsu dsu = Dsu(n);
	vector<int> p(n + 1);
	for(int i = 1;i <= n;i++){
		cin >> p[i];
	}
	for(int i = 1;i <= m;i++){
		int u,v;cin >> u >> v;
		dsu.merge(u,v);
	}
	int ans = 0;
	for(int i = 1;i <= n;i++){
		se[dsu.find(i)].insert(i);
	}
	for(int i = 1;i <= n;i++){
		if(se[dsu.find(i)].count(p[i])) ans++;
	}
	cout << ans << '\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [C - Peaceful Rooks](https://vjudge.net.cn/problem/CodeForces-1411C)

> $m$ 个石头在 $(n,n)$ 的棋盘上 $(m<n)$ ，希望将其移动到主对角线上
>
>一次移动操作可以横着或者竖着移动一个石头任意距离
>
>任意时刻均不能使得任意两颗石头连线平行于坐标系（移动可以穿过）
>
>问最小移动次数

##### 思路：

* 我们假设 $x_i=y_j或者y_i=x_j$ 为 $(i,j)$ 无向边，那么一个点最多两条边出去，如果它们不能形成环，那么说明我们可以采用拓扑序的方式使得所有经过的点都一步放在对角线上
* 因为一个点最多两条边，那么判环可以用并查集来判断

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

class Dsu{
public:
    vector<int> a;
    int sz;

    void init(){for(int i = 1;i <= sz;i++) a[i] = i;}
    void init(int n){for(int i = 1;i <= n;i++) a[i] = i;}

    Dsu(int n){a = vector<int>(n + 1); sz = n; init();}

    int size(){return sz;}

    int find(int x){ return x == a[x] ? x : a[x] = find(a[x]);}

    void merge(int x,int y){a[find(x)] = find(y);}

    bool same_set(int x,int y){return find(x) == find(y);}
};

const int N = 1e5 + 10;

int x[N],y[N];

void test(){
	int n,m;cin >> n >> m;
	for(int i = 1;i <= n;i++){
		x[i] = y[i] = 0;
	}
	Dsu dsu = Dsu(m + 1);
	vector<pair<int,int>> pa(n + 1);
	int ans = m;
	for(int i = 1;i <= m;i++){
		cin >> pa[i].first >> pa[i].second;
		if(pa[i].first == pa[i].second) ans--;
		if(x[pa[i].second]){
			dsu.merge(x[pa[i].second],i);
		}
		if(y[pa[i].first]){
			if(dsu.same_set(y[pa[i].first],i)) ans++;
			else dsu.merge(y[pa[i].first],i);
		}
		x[pa[i].first] = i;
		y[pa[i].second] = i;
	}
	cout << ans << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## [D - Edge Split](https://vjudge.net.cn/problem/CodeForces-1726D)

> 给出 $n$ 顶点， $m$ 条边的无向图， $n - 1 \leq m \leq n+2$ 
>
> 将其边染色成红色或者蓝色，对红色的图求联通分量 $c_1$ 与对蓝色的图求连通分量 $c_2$
>
> 问 $c_1+c_2$ 取最小值时的可能染色方案，输出其一即可

##### 思路：

* 边数给的很少，假设是 $m=n-1$ ，那么就是一颗树，每条边能消除的连通分量最多是一个，那么树的每条边正好是能消除一个连通分量，因此可以
* 当 $n-1\leq m \leq n+1$ 时，生成一颗树剩下的边的数量在 $[0,2]$ 内，此时另外一张图不会形成环（因为没有重边与自环）
* 那么同样每条边能消除一个连通分量，直接输出即可
* 只有当 $m=n+2$ 时，我们考虑剩下的三条边形成环时
* 我们取树上三个点的一条任意边替换，只要这条边不会与剩下两条边再次形成环

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

const int N = 2e5 + 10;

vector<pair<int,int>> ed[N];

int fa[N];
bool vis[N];
int ans[N];

void find_tree(int s){
	vis[s] = 1;
	stack<int> st;
	st.push(s);
	while(!st.empty()){
		int x = st.top();
		st.pop();
		for(auto [y,id] : ed[x]){
			if(!vis[y]){
				vis[y] = 1;
				fa[y] = x;
				st.push(y);
				ans[id] = 1;
			}
		}
	}
}

void test(){
	int n,m;cin >> n >> m;
	for(int i = 1;i <= m;i++){
		ans[i] = 0;
	}
	for(int i = 1;i <= n;i++){
		ed[i].clear();
		vis[i] = 0;
	}
	vector<pair<int,int>> pa(m + 1);
	for(int i = 1;i <= m;i++){
		auto& [u,v] = pa[i];
		cin >> u >> v;
		ed[u].push_back(make_pair(v,i));
		ed[v].push_back(make_pair(u,i));
	}
	find_tree(1);
	set<int> se;
	for(int i = 1;i <= m;i++){
		if(ans[i] == 0){
			se.insert(pa[i].first);
			se.insert(pa[i].second);
		}
	}
	if(m == n + 2 && (int)se.size() == 3){//另外一个为环
		int p;
		for(auto x : se){
			if(!se.count(fa[x])){
				p = x;
				break;
			}
		}
		bool f = 1;
		for(auto [y,id] : ed[p]){
			if(ans[id] == 0 && f){
				ans[id] = 1;
				f = 0;
			}else if(pa[id].first == fa[p] || pa[id].second == fa[p]){
				ans[id] = 0;
			}
		}
	}
	for(int i = 1;i <= m;i++)
		cout << ans[i];
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

## [E - Dense Subsequence](https://vjudge.net.cn/problem/CodeForces-724D)

>给出一个字符串，从中选取一些位置的字符
>
>每 $m$ 个连续字符必须至少被选取一个
>
>将选取的字符提取出来，任意排列之后的字典序最小，问其是

##### 思路：

* 假设选取的字符最大的为 $c$ ，那么比其更小的字符肯定都要选上
* 如果能够不取到最大的字符 $c$ ，那么肯定是不取更优
* 选取的字符最大的为 $c$ 要尽量少取
* 满足以上三点取字符即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

bool f(const string &s,char c,int m){
	int n = s.size();
	int no = 0;
	for(int i = 0;i < n;i++){
		if(s[i] > c) no++;
		else{
			no = 0;
		}
		if(no >= m) return 0;
	}
	return 1;
}

void test(){
	int m;cin >> m;
	string s;cin >> s;
	char c;
	for(c = 'a';c <= 'z';c++){
		if(f(s,c,m)){
			break;
		}
	}
	string ans;
	int n = s.size();
	int pre = -1,nex = -1;
	for(int i = 0;i < n;i++){
		if(s[i] < c){
			ans.push_back(s[i]);
			pre = i;
		}
		else if(s[i] == c){
			nex = i;
		}
		if(i - pre >= m){
			pre = nex;
			ans.push_back(c);
		}
	}
	sort(ans.begin(),ans.end());
	cout << ans;
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [F - String Reconstruction](https://vjudge.net.cn/problem/CodeForces-827A)

> 给出字符串部分区域的起点与其字符串内容，问该字符串最小字典序为
>
> 保证不冲突

##### 思路：

* 那么我们用前缀和维护能跑的最远的那个表示，如果有位置没有表示就赋值为 $'a'$

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

const int N = 2e6 + 10;

void test(){
	int n;cin >> n;
	vector<string> sarr(n + 1);
	vector<int> sz(n + 1);
	string ans;
	vector<pair<int,int>> to(N);
	int r = 0;//最远
	for(int i = 1;i <= n;i++){
		cin >> sarr[i];
		sz[i] = sarr[i].size();
		int m;cin >> m;
		for(int j = 1;j <= m;j++){
			int x;cin >> x;
			r = max(r,x + sz[i] - 1);
			if(to[x].first + sz[to[x].second] - 1 < x + sz[i] - 1){
				to[x].first = x;
				to[x].second = i;
			}
		}
	}
	pair<int,int> no = to[0];
	for(int i = 1;i <= r;i++){
		if(to[i].first + sz[to[i].second] - 1 > no.first + sz[no.second] - 1) no = to[i];
		if(sz[no.second] + no.first <= i) no = {0,0};
		if(no.second == 0){
			ans.push_back('a');
		}else{
			ans.push_back(sarr[no.second][i - no.first]);
		}
	}
	cout << ans;
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [G - Minimum Array](https://vjudge.net.cn/problem/CodeForces-1157E)

>  $c_i=(a_i+b_i)\%n$ ，已知数组 $a,b$ ，将 $b$ 的顺序任意调换，问 $c$ 字典序最小为

##### 思路：

* 问如果我们只是要让 $c_1$ 最小，那么我们就要选 $[n-a_1,n-1],[0,n-a_1-1]$ 最靠近区间左边的数
* 对每个位置都求右边并去除，我们用 $multiset$ 维护即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

void test(){
	multiset<int> se;
	int n;cin >> n;
	vector<int> a(n + 1);
	for(int i = 1;i <= n;i++){
		cin >> a[i];
	}
	for(int i = 1;i <= n;i++){
		int x;cin >> x;
		se.insert(x);
	}
	for(int i = 1;i <= n;i++){
		int aim = n - a[i];
		auto it = se.lower_bound(aim);
		if(it != se.end()){
			cout << (a[i] + *it) % n << ' ';
		}else{
			it = se.begin();
			cout << (a[i] + *se.begin()) % n << ' ';
		}
		se.erase(it);
	}
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [H - Vessels](https://vjudge.net.cn/problem/CodeForces-371D)

> 一个水塔，上面有 $n$ 个容器，第 $i$ 个容器溢出的水会流到 $i + 1$
>
> 每次给第 $k_i$ 位置倒 $x_i$ 的水，或者询问 $k_i$ 位置的水量

##### 思路：

* 当一个位置的水满时，它就不会改变了，我们要维护每个位置倒水最终会到的容器
* 又因为下面要到的位置改变会同时改变上面的要到的位置，我们就维护一个 $find$ 只往下找的并查集即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

const int N = 2e5 + 10;

int fa[N];
int find(int x){
	if(fa[x] == x) return x;
	else return fa[x] = find(fa[x]);
}
int a[N];

void test(){
	int n,m;cin >> n;
	for(int i = 1;i <= n;i++) fa[i] = i;
	vector<int> v(n + 1);
	for(int i = 1;i <= n;i++){
		cin >> v[i];
	}
	cin >> m;
	for(int i = 1;i <= m;i++){
		int op;cin >> op;
		if(op == 2){
			int p;cin >> p;
			cout << a[p] << '\n';
		}else{
			int p,x;cin >> p >> x;
			p = find(p);
			while(p && x){
				int use = min(x,v[p] - a[p]);
				x -= use;
				a[p] += use;
				if(a[p] == v[p]){
					p = fa[p] = find(p + 1);
				}
			}
		}
	}
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [I - 降水](https://vjudge.net.cn/problem/洛谷-P5930)

> 典，二维积水，问最多能积多少水

##### 思路：

* 1.spfa

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

int mvx[] = {0,0,-1,1},mvy[] = {-1,1,0,0};

void test(){
	int n,m;cin >> n >> m;
	vector<vector<int>> a(n + 1,vector<int>(m + 1)),h(n + 1,vector<int>(m + 1)),in(n + 1,vector<int>(m + 1));
	queue<pair<int,int>> qu;
	for(int i = 1;i <= n;i++)
		for(int j = 1;j <= m;j++){
			cin >> a[i][j];
			h[i][j] = inf;
			if(i == 1 || j == 1 || i == n || j == m){
				if(!in[i][j])
					qu.push(make_pair(i,j));
				h[i][j] = a[i][j];
				in[i][j] = 1;
			}
		}
	while(!qu.empty()){
		int i = qu.front().first,j = qu.front().second;
		qu.pop();
		in[i][j] = 0;
		int tem = max(h[i][j],a[i][j]);
		for(int k = 0;k < 4;k++){
			int _i = i + mvx[k],_j = j + mvy[k];
			if(_i >= 1 && _i <= n && _j >= 1 && _j <= m){
				if(h[_i][_j] > tem){
					h[_i][_j] = max(tem,a[_i][_j]);
					if(!in[_i][_j]){
						in[_i][_j] = 1;
						qu.push(make_pair(_i,_j));
					}
				}
			}
		}
	}
	ll ans = 0;
	// cout << '\n';
	for(int i = 1;i <= n;i++){
		for(int j = 1;j <= m;j++){
			ans += h[i][j] - a[i][j];
			// cout << h[i][j] << ' ';
		}
		// cout << '\n';
	}
		
	cout << ans << '\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

* 2.并查集，我们从小到大遍历高度，然后将高点与低点相连，如果集合没有连到最外边，那么就按该集合的大小来+1
* 我才不敲

## [J - Restructuring Company](https://vjudge.net.cn/problem/CodeForces-566D)

>  $n$ 个人分别属于 $n$ 个部门，每次操作
>
>  $op=1$ 合并 $x,y$ 人属于的部门
>
>  $op=2$ 合并 $[x,y]$ 属于的部门
>
>  $op=3$ 询问 $x,y$ 是否属于同一个部门

##### 思路：

* 主要是操作二可能会覆盖多次大区间
* 那么我们只要将那些已经向后一步合并位置维护另外一个并查集即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

class Dsu{
public:
    vector<int> a;
    int sz;

    void init(){for(int i = 1;i <= sz;i++) a[i] = i;}
    void init(int n){for(int i = 1;i <= n;i++) a[i] = i;}

    Dsu(int n){a = vector<int>(n + 1); sz = n; init();}

    int size(){return sz;}

    int find(int x){ return x == a[x] ? x : a[x] = find(a[x]);}

    int merge(int x,int y){return a[find(x)] = find(y);}

    bool same_set(int x,int y){return find(x) == find(y);}
};

void test(){
	int n,q;cin >> n >> q;
	Dsu dsu = Dsu(n);
	Dsu nex = Dsu(n);
	while(q--){
		int op,x,y;cin >> op >> x >> y;
		if(x > y) swap(x,y);
		if(op == 1){
			dsu.merge(x,y);
		}else if(op == 2){
			while(x < y){
				dsu.merge(x,x + 1);
				nex.merge(x,x + 1);
				x = nex.find(x);
			}
		}else{
			if(dsu.same_set(x,y)) cout << "YES\n";
			else cout << "NO\n";
		}
	}
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [K - Range Update Point Query](https://vjudge.net.cn/problem/CodeForces-1791F)

> 区间进行取每位数求和的操作，问某个位置的数为多少

##### 思路：

* 树状数组维护每个点的操作次数
* 因为这种操作不会进行很多次，因此暴力直到其**小于** $10$ 即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

const int N = 2e5 + 10;

int tr[N];
int n;
int lowbit(int x){
	return x&-x;
}
void add(int x,int y){
	for(;x <= n;x += lowbit(x)) tr[x] += y;
}
int query(int x){
	int res = 0;
	for(;x;x -= lowbit(x)) res += tr[x];
	return res;
}

int f(int x,int y){
	while(y && x >= 10){
		int res = 0;
		while(x){
			res += x % 10;
			x /= 10;
		}
		x = res;
		y--;
	}
	return x;
}

void test(){
	int q;cin >> n >> q;
	for(int i = 1;i <= n;i++) tr[i] = 0;
	vector<int> a(n + 1);
	for(int i = 1;i <= n;i++) cin >> a[i];
	while(q--){
		int op;cin >> op;
		if(op == 1){
			int l,r;cin >> l >> r;
			add(l,1);
			add(r + 1,-1);
		}else{
			int x;cin >> x;
			cout << f(a[x],query(x)) << '\n';
		}
	}
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## [L - Til the Cows Come Home](https://vjudge.net.cn/problem/OpenJ_Bailian-2387)

> 最短路

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

vector<vector<pair<int,int>>> ed;
vector<bool> vis;

struct pos
{
	int u;
	int dis;
	bool operator<(const pos &other)const{
		return dis > other.dis;
	}	
};

priority_queue<pos> qu;

int find(int n){
	qu.push({1,0});
	while(!qu.empty()){
		auto [x,dis] = qu.top();
		if(x == n) return dis;
		qu.pop();
		if(vis[x]) continue;
		vis[x] = 1;
		for(auto [y,d] : ed[x]){
			if(vis[y]) continue;
			qu.push({y,dis + d});
		}
	}
}

void test(){
	int m,n;cin >> m >> n;
	vis = vector<bool>(n + 1);
	ed = vector<vector<pair<int,int>>>(n + 1);
	for(int i = 1;i <= m;i++){
		int u,v,d;cin >> u >> v >> d;
		ed[u].push_back(make_pair(v,d));
		ed[v].push_back(make_pair(u,d));
	}
	cout << find(1);
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [M - Frogger](https://vjudge.net.cn/problem/OpenJ_Bailian-2253)

> 从点 $1$ 到点 $2$ 路径的最大权的最小值

##### 思路：

* 二分或者最小生成树
* 最小生成树

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

class Dsu{
public:
    vector<int> a;
    int sz;

    void init(){for(int i = 1;i <= sz;i++) a[i] = i;}
    void init(int n){for(int i = 1;i <= n;i++) a[i] = i;}

    Dsu(int n){a = vector<int>(n + 1); sz = n; init();}
    Dsu(){}

    int size(){return sz;}

    int find(int x){ return x == a[x] ? x : a[x] = find(a[x]);}

    int merge(int x,int y){return a[find(x)] = find(y);}

    bool same_set(int x,int y){return find(x) == find(y);}
};

Dsu dsu;

double f(pair<int,int> a,pair<int,int> b){
	return sqrt((a.first - b.first)*(a.first - b.first) + (a.second - b.second)*(a.second - b.second));
}

void test(){
	int ptop = 0;
	while(1){
		ptop++;
		int n;cin >> n;
		if(n == 0) return;
		dsu = Dsu(n);
		vector<pair<int,int>> p(n + 1);
		vector<tuple<double,int,int>> ve;
		for(int i = 1;i <= n;i++){
			cin >> p[i].first >> p[i].second;
			for(int j = 1;j < i;j++){
				tuple<double,int,int> res;
				auto &[d,x,y] = res;
				x = i,y = j,d = f(p[i],p[j]);
				ve.push_back(res);
			}
		}
		sort(ve.begin(),ve.end());
		double ans = 0;
		for(auto [d,x,y] : ve){
			dsu.merge(x,y);
			if(dsu.same_set(1,2)){
				ans = d;
				break;
			}
		}
		cout<<"Scenario #"<<ptop<<endl;
		cout<<"Frog Distance = "<<fixed<<setprecision(3)<<ans<<endl<<endl;
	}
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

* 二分

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

double f(pair<int,int> a,pair<int,int> b){
	return sqrt((a.first - b.first)*(a.first - b.first) + (a.second - b.second)*(a.second - b.second));
}

vector<vector<double>> ed;
vector<bool> vis;

int n;

bool dfs(double x){
	queue<int> qu;
	qu.push(1);
	vis[1] = 1;
	while(!qu.empty()){
		int no = qu.front();
		if(no == 2){
			return 1;
		}
		qu.pop();
		for(int i = 1;i <= n;i++){
			if(!vis[i] && ed[i][no] <= x){
				qu.push(i);
				vis[i] = 1;
			}
		}
	}
	return 0;
}

void test(){
	int ptop = 0;
	while(1){
		ptop++;
		cin >> n;
		if(n == 0) return;
		vector<pair<int,int>> p(n + 1);
		ed = vector<vector<double>>(n + 1,vector<double>(n + 1));
		for(int i = 1;i <= n;i++){
			cin >> p[i].first >> p[i].second;
			for(int j = 1;j < i;j++){
				ed[i][j] = ed[j][i] = f(p[i],p[j]);
			}
		}
		double l = 0,r = 2000;
		while(l + 1e-5 < r){
			vis = vector<bool>(n + 1);
			double mid = (l + r) / 2;
			if(dfs(mid)) r = mid;
			else l = mid;
		}
		cout << "Scenario #" << ptop << '\n';
		cout << "Frog Distance = ";
		printf("%.3lf\n\n",r);
	}
}

int main(){
	// IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [N - Currency Exchange](https://vjudge.net.cn/problem/OpenJ_Bailian-1860)

> 搜索看是否能增加

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

const int N = 1e2 + 10;

vector<vector<tuple<int,double,double>>> ex;

int num[N];
double val[N];

void test(){
	int n,m,s;cin >> n >> m >> s;
	double v;cin >> v;
	ex = vector<vector<tuple<int,double,double>>>(n + 1);
	for(int i = 1;i <= m;i++){
		int u,v;
		double ra,ca,rb,cb;
		cin >> u >> v >> ra >> ca >> rb >> cb;
		tuple<int,double,double> tem;
		auto &[to,r,c] = tem;
		to = v;
		r = ra;
		c = ca;
		ex[u].push_back(tem);
		to = u;
		r = rb;
		c = cb;
		ex[v].push_back(tem);
	}
	queue<pair<int,double>> qu;
	qu.push(make_pair(s,v));
	val[s] = v;
	while(!qu.empty()){
		auto [x,va] = qu.front();
		num[x]++;
		qu.pop();
		if(x == s && num[x] == 2){
			cout << "YES\n";
			return;
		}
		for(auto [y,r,c] : ex[x]){
			double ne = r * (va - c);
			if(val[y] < ne){
				val[y] = ne;
				num[y]++;
				if(num[y] >= n){
					cout << "YES\n";
					return;
				}
				qu.push(make_pair(y,ne));
			}
		}
	}
	cout << "NO\n";
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [O - MPI Maelstrom](https://vjudge.net.cn/problem/OpenJ_Bailian-1502)

> 单源最短路

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

inline ll read() {
    ll X=0,w=1; int c=getchar();
    while ((c<'0'||c>'9') && c!='x') { if (c=='-') w=-1;c=getchar(); }
    while (c>='0'&&c<='9' || c == 'x') {X=(X<<3)+(X<<1)+(c^48);if(c == 'x') X=inf;c=getchar();}
    return X*w;
}
inline void write(ll x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}

vector<vector<pair<int,int>>> ed;

const int N = 1e2 + 10;

struct pos{
	int dis,p;
	bool operator <(const pos &x)const{
		return dis > x.dis;
	}//重载运算符
};
priority_queue<pos> qu;//存点
int dis[N];
bool vis[N];

int bfs(int s){
	qu.push({0,s});
	int ans = 0;
	dis[s] = 0;
	while(!qu.empty()){
		auto [ds,x] = qu.top();
		qu.pop();
		if(vis[x]) continue;
		ans = max(ds,ans);
		vis[x] = 1;
		for(auto [y,d] : ed[x]){
			if(vis[y]) continue;
			if(dis[y] > d + ds){
				dis[y] = d + ds;
				qu.push({d + ds,y});
			}
		}
	}
	return ans;
}

void test(){
	int n = read();
	for(int i = 1;i <= n;i++) dis[i] = inf;
	ed = vector<vector<pair<int,int>>>(n + 1);
	for(int i = 1;i < n;i++){
		for(int j = 1;j <= i;j++){
			int u = i + 1,v = j;
			int val = read();
			ed[u].push_back(make_pair(v,val));
			ed[v].push_back(make_pair(u,val));
		}
	}
	write(bfs(1));
}

signed main(){
	// IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

