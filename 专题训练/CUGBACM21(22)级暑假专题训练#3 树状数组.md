* 来个通用板子

```cpp
template<class T>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T> tr;
	int sz;
	BIT(int n){tr = vector<T>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,int y){add(l,y);add(r+1,-y);}

	void set_max(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T query(int x){T res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T query(int l,int r){return query(r) - query(l - 1);}
};

class BIT_2{//双树状数组实现区间加减与区间求和
public:
	BIT<ll> tr1,tr2;

	int sz;

	BIT_2(int n){tr1.set_size(n);tr2.set_size(n);sz = n;}

	void apply(const vector<int> &v){
		tr1.apply_d(v);
		for(int i = 1;i <= sz;i++){
			int d = v[i] - v[i - 1];
			tr2.add(i,d * (i - 1));
		}
	}

	void add(int l,int r,int x){
		//d[l] += x d[r + 1] -= x
		tr1.add(l,x);
		tr2.add(l,x * (l - 1));
		tr1.add(r + 1,- x);
		tr2.add(r + 1,- x * r);
	}

	ll query(int x){
		return x*tr1.query(x) - tr2.query(x);
	}

	ll query(int l,int r){
		return query(r) - query(l - 1);
	}
};
```

## [A - 树状数组 1](https://vjudge.net.cn/problem/洛谷-P3374)

* 板子启动

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

//板子

void test(){
	int n,m;cin >> n >> m;
	BIT<int> bit = BIT<int>(n);
	vector<int> a(n + 1);
	for(int i = 1;i <= n;i++) cin >> a[i];
	bit.apply_a(a);
	for(int i = 1;i <= m;i++){
		int op,x,k;cin >> op >> x >> k;
		if(op == 1){
			bit.add(x,k);
		}else{
			cout << bit.query(x,k) << '\n';
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

## [B - Infinite Inversions](https://vjudge.net.cn/problem/CodeForces-540E)

> 无限序列交换某些位置求逆序对

##### 思路：

* 正常较小序列我们求逆序对方式为 $\sum_{1\leq i \leq n}\sum_{1\leq j\leq i}(a[i]<a[j]?1:0)$
* 也就是我们对每一个位置求一次前面有多少数比它大
* 可以用树状数组来计算
* 对于本题，考虑移动的位置，使用离散化可以直接得出相互之间的逆序对
* 移动位置对非移动位置，我们假设该位置为 $pos$ ，值为 $val$ ，离散化的值为 $pos'$ 与 $val'$
* 那么我们就要考虑在前面有多少的值大于 $val'$
* 如果 $val'>pos'$ ，那么前面没有移动的值肯定比 $val$ 小
* 如果 $val'<pos'$ ，那么我们就要看在 $[val',pos']$ 有多少个没有移动的位置，我们用另外一个树状数组维护
* 然后我们现在考虑非移动位置
* 首先非移动位置对非移动位置肯定没有逆序对
* 那么我们只要考虑非移动位置对移动位置的逆序对
* 我们容易发现，如果两个非移动位置相邻，那么它们两个对移动位置的逆序对是相同的
* 那么我们就可以根据离散化的位置来分出区间来得到非移动位置对移动位置的逆序对再乘上长度
* 具体的，我们考虑该区间前面的为 $pos,pos'$ ，那么我们就是要求前面有多少数大于 $pos'$ ，因为区间连续，因此不存在有数在该区间中使得逆序对数量不同

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

template<class T>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T> tr;
	int sz;
	BIT(int n){tr = vector<T>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,int y){add(l,y);add(r+1,-y);}

	void set_max(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T query(int x){T res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T query(int l,int r){return query(r) - query(l - 1);}
};

class BIT_2{//双树状数组实现区间加减与区间求和
public:
	BIT<ll> tr1,tr2;

	int sz;

	BIT_2(int n){tr1.set_size(n);tr2.set_size(n);sz = n;}

	void apply(const vector<int> &v){
		tr1.apply_d(v);
		for(int i = 1;i <= sz;i++){
			int d = v[i] - v[i - 1];
			tr2.add(i,d * (i - 1));
		}
	}

	void add(int l,int r,int x){
		//d[l] += x d[r + 1] -= x
		tr1.add(l,x);
		tr2.add(l,x * (l - 1));
		tr1.add(r + 1,- x);
		tr2.add(r + 1,- x * r);
	}

	ll query(int x){
		return x*tr1.query(x) - tr2.query(x);
	}

	ll query(int l,int r){
		return query(r) - query(l - 1);
	}
};

void test(){
	int n;cin >> n;
	unordered_map<int,int> mp;
	for(int i = 1;i <= n;i++){
		int x,y;cin >> x >> y;
		if(!mp.count(x)) mp[x] = x;
		if(!mp.count(y)) mp[y] = y;
		swap(mp[x],mp[y]);
	}
	vector<pair<int,int>> ve;
	for(auto [x,y] : mp){
		ve.push_back(make_pair(x,y));
	}
	sort(ve.begin(),ve.end());
	int ptop = 0;
	vector<int> arr;
	vector<int> pos;
	arr.push_back(0);
	pos.push_back(0);
	unordered_map<int,int> mmp;
	for(auto [x,y] : ve){//离散化，按位置排序
		mmp[x] = ++ptop;
		pos.push_back(x);
		arr.push_back(y);
	}
	BIT<int> bitx(ptop),bity(ptop);
	ll ans = 0;
	for(int i = 1;i <= ptop;i++){
		if(i != 1){//统计中间的
			int len = pos[i] - pos[i - 1] - 1;
			ans += (ll)len * bity.query(mmp[pos[i - 1]] + 1,ptop);
		}
		//统计该点
		ans += bity.query(mmp[arr[i]] + 1,ptop);
		if(arr[i] < pos[i]){
			ans += pos[i] - arr[i] - bitx.query(mmp[arr[i]],mmp[pos[i]]);
		}
		bitx.add(mmp[pos[i]],1);
		bity.add(mmp[arr[i]],1);
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

* 优化
* 因为不移动区间内不会产生移动，那么我们可以将其也看作一个数，将其与 $pos'$ 合并即可
* 那么我们只需要一个树状数组维护即可

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;

#define lowbit(x) (x&-x)
#define ll long long
#define int long long

ll tr[N*2],n,_data[N*2],nn;
pair<ll,ll> pa[N];
vector<ll> tem;

void add(ll x,ll y){
	for(;x <= nn;x += lowbit(x)) tr[x] += y;
}
ll query(ll x){
	ll ans = 0;
	for(;x;x -= lowbit(x)) ans += tr[x];
	return ans;
}

void Discretization(int& nn,vector<int>& ve,pair<int,int>* _data){//长度，离散化数组，数据，排名
	sort(ve.begin(),ve.end());
	ve.erase(unique(ve.begin(),ve.end()),ve.end());
	for(int i = 1;i <= nn;i++){
	 	_data[i].first = lower_bound(ve.begin(),ve.end(),_data[i].first) - ve.begin() + 1;
	 	_data[i].second = lower_bound(ve.begin(),ve.end(),_data[i].second) - ve.begin() + 1;
	}
	nn = ve.size();
}

void init(){
	for(int i = 1;i <= nn;i++)
		_data[i] = i;
	for(int i = 1;i <= n;i++){
		swap(_data[pa[i].first],_data[pa[i].second]);
	}
}

signed main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	cin >> n;
	
	for(int i = 1;i <= n;i++){
		cin >> pa[i].first >> pa[i].second;
		tem.push_back(pa[i].first);
		tem.push_back(pa[i].second);
	}
	nn = n;
	Discretization(nn,tem,pa);
	init();
	ll ans = 0;
	add(nn - _data[1] + 1,1);
	for(int i = 2;i <= nn;i++){
		ans += query(nn - i + 1) * (tem[i - 1] - tem[i - 2] - 1);
		add(nn - i + 1,tem[i - 1] - tem[i - 2] - 1);
		ans += query(nn - _data[i]);
		add(nn - _data[i] + 1,1);
	}
	cout << ans << endl;
}
```

## [C - HH的项链](https://vjudge.net.cn/problem/洛谷-P1972)

> 离线区间求种类

##### 思路：

* 主要是考虑离线
* 如果 $r=n$ 我们都将最后的数标记为 $1$ 统计 $1$ 的数量，那么答案就出来的
* 那么我们动态移动 $r$ ，将 $r$ 小的先算，并且维护所有的 $1$ ，用树状数组维护

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

template<class T>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T> tr;
	int sz;
	BIT(int n){tr = vector<T>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,int y){add(l,y);add(r+1,-y);}

	void set_max(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T query(int x){T res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T query(int l,int r){return query(r) - query(l - 1);}
};

const int N = 1e6 + 10;

void test(){
	int n;cin >> n;
	vector<int> a(n + 1);
	for(int i = 1;i <= n;i++) cin >> a[i];
	vector<tuple<int,int,int>> ve;
	int m;cin >> m;
	for(int i = 1;i <= m;i++){
		tuple<int,int,int> res;
		auto &[r,l,id] = res;
		cin >> l >> r;
		id = i;	
		ve.push_back(res);
	}
	sort(ve.begin(),ve.end());
	BIT<int> bit = BIT<int>(N);
	vector<int> pre(N),ans(m + 1);
	int _r = 0;
	for(int i = 0;i < m;i++){
		auto [r,l,id] = ve[i];
		while(_r < r){
			_r++;
			if(pre[a[_r]]) bit.add(pre[a[_r]],-1);
			bit.add(_r,1);
			pre[a[_r]] = _r;
		}
		ans[id] = bit.query(l,r);
	}
	for(int i = 1;i <= m;i++)
		cout << ans[i] << '\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [D - Range Set Query](https://vjudge.net.cn/problem/AtCoder-abc174_f)

* 同上

## [E - MooFest G 加强版](https://vjudge.net.cn/problem/洛谷-P5094)

> 求 $\sum_{i=1}^n\sum_{j=i+1}^n\max(v_i,v_j)|x_i-x_j|$

##### 思路：

* 其中一个乘积为 $max(v_i,v_j)$ ，那么我们先按 $v_i$ 来排序，那么其中一个乘积就固定了
* 然后对于 $|x_i-x_j|$ 就分成大于 $x_i$ 与小于 $x_i$ 的
* 我们用树状数组很容易知道小于 $x_i$ 的数目与值的总和

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

template<class T>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T> tr;
	int sz;
	BIT(int n){tr = vector<T>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,int y){add(l,y);add(r+1,-y);}

	void set_max(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T query(int x){T res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T query(int l,int r){return query(r) - query(l - 1);}
};

const int N = 5e4 + 10;

void test(){
	int n;cin >> n;

	vector<pair<int,int>> ve(n + 1);
	for(int i = 1;i <= n;i++)
		cin >> ve[i].first >> ve[i].second;
	sort(ve.begin(),ve.end());
	BIT<ll> bit = BIT<ll>(N);
	BIT<ll> num = BIT<ll>(N);
	ll sum = 0,ans = 0;
	for(int i = 1;i <= n;i++){
		ll pre = bit.query(ve[i].second);
		ll prenum = num.query(ve[i].second);

		ans += (prenum * ve[i].second - 2 * pre + sum - (i - prenum - 1) * ve[i].second) * ve[i].first;

		sum += ve[i].second;

		bit.add(ve[i].second,ve[i].second);
		num.add(ve[i].second,1);
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

## [F - Enemy is weak](https://vjudge.net.cn/problem/CodeForces-61E)

> 求三元组 $(i,j,k)$ 满足 $i<j<k,a_i>a_j>a_k$ 的数量

##### 思路：

* 维护 $[1,j-1]和[j+1,n]$ 两个树状数组，并且要离散化

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

template<class T>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T> tr;
	int sz;
	BIT(int n){tr = vector<T>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,int y){add(l,y);add(r+1,-y);}

	void set_max(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T query(int x){T res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T query(int l,int r){return query(r) - query(l - 1);}
};

void test(){
	int n;cin >> n;
	vector<int> ve(n + 1);
	vector<int> cp(n + 1);
	for(int i = 1;i <= n;i++){
		cin >> ve[i];
		cp[i] = ve[i];
	}
	sort(cp.begin() + 1,cp.end());
	for(int i = 1;i <= n;i++){
		ve[i] = lower_bound(cp.begin() + 1,cp.end(),ve[i]) - cp.begin();
	}
	BIT<int> bitl = BIT<int>(n);
	BIT<int> bitr = BIT<int>(n);
	ll ans = 0;
	for(int i = 3;i <= n;i++){
		bitr.add(ve[i],1);
	}
	bitl.add(ve[1],1);
	for(int i = 2;i < n;i++){
		ans += (ll)bitl.query(ve[i] + 1,n) * bitr.query(ve[i] - 1);
		bitl.add(ve[i],1);
		bitr.add(ve[i + 1],-1);
	}
	cout << ans <<'\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [G - Nested Segments](https://vjudge.net.cn/problem/CodeForces-652D)

>给出一些线段，问每个线段包含的线段

##### 思路：

* 求二元组 $(i,j)$ 满足 $a_i>a_j,b_i<b_j$ 的数量
* 那么按 $a_i$ 排序，就是求 $b_i<b_j$ 的数量了

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

template<class T>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T> tr;
	int sz;
	BIT(int n){tr = vector<T>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,int y){add(l,y);add(r+1,-y);}

	void set_max(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T query(int x){T res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T query(int l,int r){return query(r) - query(l - 1);}
};

void test(){
	int n;cin >> n;
	vector<tuple<int,int,int>> ve(n + 1);
	vector<int> R;
	R.push_back(-1);
	for(int i = 1;i <= n;i++){
		auto &[l,r,id] = ve[i];
		id = i;
		cin >> l >> r;
		R.push_back(r);
	}

	sort(R.begin() + 1,R.end());
	for(int i = 1;i <= n;i++){
		auto &[l,r,id] = ve[i];
		r = lower_bound(R.begin() + 1,R.end(),r) - R.begin();
	}

	sort(ve.begin() + 1,ve.end());
	BIT<int> bit = BIT<int>(n);
	vector<int> ans(n + 1);
	for(int i = n;i >= 1;i--){
		auto [l,r,id] = ve[i];
		ans[id] = bit.query(r);
		bit.add(r,1);
	}
	for(int i = 1;i <= n;i++)
		cout << ans[i] << '\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [H - Subsequences](https://vjudge.net.cn/problem/CodeForces-597C)

> 求 $n$ 个元素的序列的 $k+1$ 长度的递增子序列

##### 思路：

*  $dp[i][k]=\sum_{j< i,a[j]<a[i]} dp[j][k-1]$
* 维护 $k$ 个树状数组即可

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

template<class T1,class T2>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T2> tr;
	int sz;
	BIT(int n){tr = vector<T1>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<T2> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<T2> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T1>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,T2 y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,T2 y){add(l,y);add(r+1,-y);}

	void set_max(int x,T2 y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,T2 y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T1 query(int x){T1 res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T1 query(int l,int r){return query(r) - query(l - 1);}
};

void test(){
	int n,k;cin >> n >> k;
	vector<int> a(n + 1);
	//维护k个树状数组
	for(int i = 1;i <= n;i++) cin >> a[i];
	vector<BIT<ll,ll>> bits = vector<BIT<ll,ll>>(k + 1,BIT<ll,ll>(n));
	for(int i = 1;i <= n;i++){
		for(int j = k;j >= 1;j--){
			bits[j].add(a[i],bits[j - 1].query(a[i] - 1));
		}
		bits[0].add(a[i],1);
	}
	cout << bits[k].query(n);
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [I - Matrix](https://vjudge.net.cn/problem/OpenJ_Bailian-2155)

> 二维矩形增减，单点查询

##### 思路：

* 创建 $n$ 个树状数组

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

template<class T>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T> tr;
	int sz;
	BIT(int n){tr = vector<T>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,int y){add(l,y);add(r+1,-y);}

	void set_max(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T query(int x){T res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T query(int l,int r){return query(r) - query(l - 1);}
};

void test(){
	int n,t;cin >> n >> t;
	vector<BIT<int>> bits = vector<BIT<int>>(n + 1,BIT<int>(n));
	while(t--){
		char c;cin >> c;
		if(c == 'C'){
			int x1,y1,x2,y2;
			cin >> x1 >> y1 >> x2 >> y2;
			for(int i = y1;i <= y2;i++){
				bits[i].add(x1,x2,1);
			}
		}else{
			int x,y;cin >> x >> y;
			cout << bits[y].query(x) % 2 << '\n';
		}
	}
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

## [J - 能被3除尽的数之和](https://vjudge.net.cn/problem/OpenJ_Bailian-2886)

* ?

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;
int main(){
	long long n;
	while(cin >> n){ cout << ((n / 3 * 3) + 3) * (n / 3 * 3) / 6 << '\n';}
}
```

## [K - Insertion Sort](https://vjudge.net.cn/problem/CodeForces-362C)

> 贪心交换次数最小化，仅可交换一次两个数的位置

##### 思路：

* 贪心交换次数即逆序对个数
* 逆序对个数用树状数组即可计算
* 交换之后交换次数最小化就是让逆序对的个数减少最多
* 只有交换的两个数之间的逆序对会改变，改变的数量根据原本的逆序对的数量变成长度减去
* 我们假设枚举交换的后面那个数，再向前枚举另外一个数，那么再用一个树状数组即可知道两个数对于的逆序对的个数

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

template<class T>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T> tr;
	int sz;
	BIT(int n){tr = vector<T>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,int y){add(l,y);add(r+1,-y);}

	void set_max(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T query(int x){T res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T query(int l,int r){return query(r) - query(l - 1);}
};

void test(){
	int n;cin >> n;
	vector<int> a(n + 1);
	int sum = 0;
	BIT<int> bit = BIT<int>(n);
	pair<int,int> ans;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		a[i] = n - a[i];

		BIT<int> bit2 = BIT<int>(n);
		for(int j = i - 1;j >= 1;j--){
			int be = 1;
			if(a[i] > a[j]){
				be += (2 * bit2.query(a[i]) - (i - j - 1)) - (2 * bit2.query(a[j]) - (i - j - 1));
				if(be > ans.first){
					ans.second = 1;
					ans.first = be;
				}else if(be == ans.first){
					ans.second++;
				}
			}
			bit2.add(a[j],1);
		}

		sum += bit.query(a[i]);
		bit.add(a[i],1);
	}
	cout << sum - ans.first << ' ' << ans.second << '\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

* 实际上我们考虑了该区间对应端点在区间中排序位置
* 那么实际上可以之间用数组来维护
* 首先对于后面那个数来说，这个区间的后端点不动，前端点拓展，因此可以之间枚举出来
* 对于前面那个数来说，外循环又是从前往后枚举，也可以在途中维护

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

template<class T>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T> tr;
	int sz;
	BIT(int n){tr = vector<T>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,int y){add(l,y);add(r+1,-y);}

	void set_max(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T query(int x){T res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T query(int l,int r){return query(r) - query(l - 1);}
};

void test(){
	int n;cin >> n;
	vector<int> a(n + 1);
	int sum = 0;
	BIT<int> bit = BIT<int>(n);
	pair<int,int> ans;

	vector<int> num(n + 1);

	for(int i = 1;i <= n;i++){
		cin >> a[i];
		a[i] = n - a[i];

		int tem = 0;
		for(int j = i - 1;j >= 1;j--){
			int be = 1;
			if(a[i] > a[j]){
				be += (2 * num[j]) - (i - j - 1) - (2 * tem - (i - j - 1));
				if(be > ans.first){
					ans.second = 1;
					ans.first = be;
				}else if(be == ans.first){
					ans.second++;
				}
				num[j]++;
			}else{
				tem++;
			}
		}

		sum += bit.query(a[i]);
		bit.add(a[i],1);
	}
	cout << sum - ans.first << ' ' << ans.second << '\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [L - 敌兵布阵](https://vjudge.net.cn/problem/HDU-1166)

* 板

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

template<class T>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T> tr;
	int sz;
	BIT(int n){tr = vector<T>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,int y){add(l,y);add(r+1,-y);}

	void set_max(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T query(int x){T res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T query(int l,int r){return query(r) - query(l - 1);}
};

void test(){
	int t;cin >> t;
	for(int i = 1;i <= t;i++){
		cout << "Case " << i << ":\n";
		int n;cin >> n;
		BIT<int> bit = BIT<int>(n);
		for(int i = 1;i <= n;i++){
			int x;cin >> x;
			bit.add(i,x);
		}
		string s;
		while(cin >> s){
			if(s[0] == 'E'){
				break;
			}else if(s[0] == 'Q'){
				int i,j;cin >> i >> j;
				cout << bit.query(i,j) << '\n';
			}else if(s[0] == 'S'){
				int i,j;cin >> i >> j;
				bit.add(i,-j);
			}else{
				int i,j;cin >> i >> j;
				bit.add(i,j);
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

## [M - Ping pong](https://vjudge.net.cn/problem/HDU-2492)

* 同三元组题目，加一个反向条件

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

template<class T>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T> tr;
	int sz;
	BIT(int n){tr = vector<T>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,int y){add(l,y);add(r+1,-y);}

	void set_max(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T query(int x){T res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T query(int l,int r){return query(r) - query(l - 1);}
};

void test(){
	int n;cin >> n;
	vector<int> ve(n + 1);
	vector<int> cp(n + 1);
	for(int i = 1;i <= n;i++){
		cin >> ve[i];
		cp[i] = ve[i];
	}
	sort(cp.begin() + 1,cp.end());
	for(int i = 1;i <= n;i++){
		ve[i] = lower_bound(cp.begin() + 1,cp.end(),ve[i]) - cp.begin();
	}
	BIT<int> bitl = BIT<int>(n);
	BIT<int> bitr = BIT<int>(n);
	ll ans = 0;
	for(int i = 3;i <= n;i++){
		bitr.add(ve[i],1);
	}
	bitl.add(ve[1],1);
	for(int i = 2;i < n;i++){
		ans += (ll)bitl.query(ve[i] + 1,n) * bitr.query(ve[i] - 1);
		ans += (ll)bitl.query(ve[i] - 1) * bitr.query(ve[i] + 1,n);
		bitl.add(ve[i],1);
		bitr.add(ve[i + 1],-1);
	}
	cout << ans <<'\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## [N - Stars](https://vjudge.net.cn/problem/HDU-1541)

* 题目描述怎么不说这是个多case的题我日

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

template<class T>
class BIT{//普通树状数组实现单点加减,前缀和查询,区间查询 | 区间加减,某点位置查询 | 单点修改,前缀最大最小值查询
public:	
	const inline int lowbit(int x){return x&-x;}

	vector<T> tr;
	int sz;
	BIT(int n){tr = vector<T>(n + 1);sz = n;}
	BIT(){}

	void apply_a(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]);}

	void apply_d(const vector<int> &v){for(int i = 1;i <= sz;i++) add(i,v[i]-v[i-1]);}

	void set_size(int n){tr = vector<T>(n + 1);sz = n;}
	
	void init(){for(int i = 1;i <= sz;i++) tr[i] = 0;}

	void add(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] += y;}

	void add(int l,int r,int y){add(l,y);add(r+1,-y);}

	void set_max(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = max(y,tr[x]);}

	void set_min(int x,int y){for(;x <= sz;x += lowbit(x)) tr[x] = min(y,tr[x]);}

	T query(int x){T res = 0;for(;x;x -= lowbit(x)) res += tr[x];return res;}

	T query(int l,int r){return query(r) - query(l - 1);}
};

const int N = 4e4 + 10;

void test(){
	int n;
	while(cin >> n){
		vector<pair<int,int>> p(n + 1);
		for(int i = 1;i <= n;i++){
			cin >> p[i].first >> p[i].second;
			p[i].first++;
		}
		vector<int> ans(n + 1);
		BIT<int> bit = BIT<int>(N);
		for(int i = 1;i <= n;i++){
			ans[bit.query(p[i].first) + 1]++;
			bit.add(p[i].first,1);
		}
		for(int i = 1;i <= n;i++){
			cout << ans[i] << '\n';
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

