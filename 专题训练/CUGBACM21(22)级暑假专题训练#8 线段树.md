## [A - 敌兵布阵](https://vjudge.net.cn/problem/HDU-1166)

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;
#define lson (x << 1)
#define rson (x << 1 | 1)

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

const int MAXN = 5e4 + 10;
int a[MAXN];
class tree{
	public:
	ll sum[MAXN*4];//代表的和
	ll ma[MAXN*4];//代表的最大值
	ll mi[MAXN*4];//代表的最小值
	bool fadd[MAXN*4];//是否启用标签
	ll ladd[MAXN*4];//加法标签
	bool fturn[MAXN*4];
	ll lturn[MAXN*4];//变换标签
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		fadd[x] = fturn[x] = ladd[x] = lturn[x] = 0;
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = a[_l];
			ma[x] = a[_l];
			mi[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void add(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			ladd[x] += v;
			fadd[x] = 1;
			ma[x] += v;
			mi[x] += v;
			sum[x] += v*(r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				add(_l,_r,lson,v);
			if(_r >= mid + 1)
				add(_l,_r,rson,v);
			update(x);
		}
	}
	void turn(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			lturn[x] = v;
			fturn[x] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
			ma[x] = v;
			mi[x] = v;
			sum[x] = v * (r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				turn(_l,_r,lson,v);
			if(_r >= mid + 1)
				turn(_l,_r,rson,v);
			update(x);
		}
	}
	void update(int x){
		ma[x] = max(ma[lson],ma[rson]);
		mi[x] = min(mi[lson],mi[rson]);
		sum[x] = sum[lson] + sum[rson];
	}
	void down(int x){
		if(fturn[x])
		{
			fturn[lson] = fturn[rson] = 1;
			lturn[lson] = lturn[rson] = lturn[x];
			ma[lson] = ma[rson] = lturn[x];
			mi[lson] = mi[rson] = lturn[x];
			sum[lson] = lturn[x]*(r[lson]-l[lson]+1);
			sum[rson] = lturn[x]*(r[rson]-l[rson]+1);
			fadd[lson] = fadd[rson] = 0;
			fturn[x] = 0;
		}
		if(fadd[x])
		{
			fadd[lson] = fadd[rson] = 1;
			ma[lson] += ladd[x];
			ma[rson] += ladd[x];
			mi[lson] += ladd[x];
			mi[rson] += ladd[x];
			sum[lson] += ladd[x]*(r[lson]-l[lson]+1);
			sum[rson] += ladd[x]*(r[rson]-l[rson]+1);
			ladd[lson] += ladd[x];
			ladd[rson] += ladd[x];
			fadd[lson] = fadd[rson] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
		}
	}
	ll findma(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return ma[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll ma = 0;
			if(_l <= mid)
				ma = findma(_l,_r,lson);
			if(_r >= mid + 1)
				ma = max(ma,findma(_l,_r,rson));
			update(x);
			return ma;
		}
	}
	ll findmi(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return mi[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll mi = 1e9 + 10;
			if(_l <= mid)
				mi = findmi(_l,_r,lson);
			if(_r >= mid + 1)
				mi = min(mi,findmi(_l,_r,rson));
			update(x);
			return mi;
		}
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum += findsum(_l,_r,lson);
			if(_r >= mid + 1)
				sum += findsum(_l,_r,rson);
			return sum;
			update(x);
		}
	}
}tr;

int ptop = 0;

void test(){
	ptop++;
	cout << "Case " << ptop << ": \n";
	int n;cin >> n;
	for(int i = 1;i <= n;i++) cin >> a[i];
	tr.build(1,n,1);
	while(1){
		string s;cin >> s;
		if(s[0] == 'Q'){
			int l,r;cin >> l >> r;
			cout << tr.findsum(l,r,1) << '\n';
		}else if(s[0] == 'A'){
			int l,r;cin >> l >> r;
			tr.add(l,l,1,r);
		}else if(s[0] == 'S'){
			int l,r;cin >> l >> r;
			tr.add(l,l,1,-r);
		}else if(s[0] == 'E') break;
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

## [B - I Hate It](https://vjudge.net.cn/problem/HDU-1754)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 2e5 + 10;
int a[MAXN];
class tree{
	public:
	ll sum[MAXN*4];//代表的和
	ll ma[MAXN*4];//代表的最大值
	ll mi[MAXN*4];//代表的最小值
	bool fadd[MAXN*4];//是否启用标签
	ll ladd[MAXN*4];//加法标签
	bool fturn[MAXN*4];
	ll lturn[MAXN*4];//变换标签
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		fadd[x] = fturn[x] = ladd[x] = lturn[x] = 0;
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = a[_l];
			ma[x] = a[_l];
			mi[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void add(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			ladd[x] += v;
			fadd[x] = 1;
			ma[x] += v;
			mi[x] += v;
			sum[x] += v*(r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				add(_l,_r,lson,v);
			if(_r >= mid + 1)
				add(_l,_r,rson,v);
			update(x);
		}
	}
	void turn(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			lturn[x] = v;
			fturn[x] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
			ma[x] = v;
			mi[x] = v;
			sum[x] = v * (r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				turn(_l,_r,lson,v);
			if(_r >= mid + 1)
				turn(_l,_r,rson,v);
			update(x);
		}
	}
	void update(int x){
		ma[x] = max(ma[lson],ma[rson]);
		mi[x] = min(mi[lson],mi[rson]);
		sum[x] = sum[lson] + sum[rson];
	}
	void down(int x){
		if(fturn[x])
		{
			fturn[lson] = fturn[rson] = 1;
			lturn[lson] = lturn[rson] = lturn[x];
			ma[lson] = ma[rson] = lturn[x];
			mi[lson] = mi[rson] = lturn[x];
			sum[lson] = lturn[x]*(r[lson]-l[lson]+1);
			sum[rson] = lturn[x]*(r[rson]-l[rson]+1);
			fadd[lson] = fadd[rson] = 0;
			fturn[x] = 0;
		}
		if(fadd[x])
		{
			fadd[lson] = fadd[rson] = 1;
			ma[lson] += ladd[x];
			ma[rson] += ladd[x];
			mi[lson] += ladd[x];
			mi[rson] += ladd[x];
			sum[lson] += ladd[x]*(r[lson]-l[lson]+1);
			sum[rson] += ladd[x]*(r[rson]-l[rson]+1);
			ladd[lson] += ladd[x];
			ladd[rson] += ladd[x];
			fadd[lson] = fadd[rson] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
		}
	}
	ll findma(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return ma[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll ma = 0;
			if(_l <= mid)
				ma = findma(_l,_r,lson);
			if(_r >= mid + 1)
				ma = max(ma,findma(_l,_r,rson));
			update(x);
			return ma;
		}
	}
	ll findmi(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return mi[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll mi = 1e9 + 10;
			if(_l <= mid)
				mi = findmi(_l,_r,lson);
			if(_r >= mid + 1)
				mi = min(mi,findmi(_l,_r,rson));
			update(x);
			return mi;
		}
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum += findsum(_l,_r,lson);
			if(_r >= mid + 1)
				sum += findsum(_l,_r,rson);
			return sum;
			update(x);
		}
	}
}tr;

void test(){
	int n,m;//cin >> n >> m;
	while(cin >> n >> m){
		for(int i = 1;i <= n;i++) cin >> a[i];
		tr.build(1,n,1);
		while(m--){
			char c;cin >> c;
			int a,b;cin >> a >> b;
			if(c == 'Q'){
				cout << tr.findma(a,b,1) << '\n';
			}else{
				tr.turn(a,a,1,b);
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

## [C - Mayor's posters](https://vjudge.net.cn/problem/OpenJ_Bailian-2528)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 2e4 + 10;
int a[MAXN];
class tree{
	public:
	ll sum[MAXN*4];//代表的和
	ll ma[MAXN*4];//代表的最大值
	ll mi[MAXN*4];//代表的最小值
	bool fadd[MAXN*4];//是否启用标签
	ll ladd[MAXN*4];//加法标签
	bool fturn[MAXN*4];
	ll lturn[MAXN*4];//变换标签
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		fadd[x] = fturn[x] = ladd[x] = lturn[x] = 0;
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = a[_l];
			ma[x] = a[_l];
			mi[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void add(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			ladd[x] += v;
			fadd[x] = 1;
			ma[x] += v;
			mi[x] += v;
			sum[x] += v*(r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				add(_l,_r,lson,v);
			if(_r >= mid + 1)
				add(_l,_r,rson,v);
			update(x);
		}
	}
	void turn(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			lturn[x] = v;
			fturn[x] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
			ma[x] = v;
			mi[x] = v;
			sum[x] = v * (r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				turn(_l,_r,lson,v);
			if(_r >= mid + 1)
				turn(_l,_r,rson,v);
			update(x);
		}
	}
	void update(int x){
		ma[x] = max(ma[lson],ma[rson]);
		mi[x] = min(mi[lson],mi[rson]);
		sum[x] = sum[lson] + sum[rson];
	}
	void down(int x){
		if(fturn[x])
		{
			fturn[lson] = fturn[rson] = 1;
			lturn[lson] = lturn[rson] = lturn[x];
			ma[lson] = ma[rson] = lturn[x];
			mi[lson] = mi[rson] = lturn[x];
			sum[lson] = lturn[x]*(r[lson]-l[lson]+1);
			sum[rson] = lturn[x]*(r[rson]-l[rson]+1);
			fadd[lson] = fadd[rson] = 0;
			fturn[x] = 0;
		}
		if(fadd[x])
		{
			fadd[lson] = fadd[rson] = 1;
			ma[lson] += ladd[x];
			ma[rson] += ladd[x];
			mi[lson] += ladd[x];
			mi[rson] += ladd[x];
			sum[lson] += ladd[x]*(r[lson]-l[lson]+1);
			sum[rson] += ladd[x]*(r[rson]-l[rson]+1);
			ladd[lson] += ladd[x];
			ladd[rson] += ladd[x];
			fadd[lson] = fadd[rson] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
		}
	}
	ll findma(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return ma[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll ma = 0;
			if(_l <= mid)
				ma = findma(_l,_r,lson);
			if(_r >= mid + 1)
				ma = max(ma,findma(_l,_r,rson));
			update(x);
			return ma;
		}
	}
	ll findmi(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return mi[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll mi = 1e9 + 10;
			if(_l <= mid)
				mi = findmi(_l,_r,lson);
			if(_r >= mid + 1)
				mi = min(mi,findmi(_l,_r,rson));
			update(x);
			return mi;
		}
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum += findsum(_l,_r,lson);
			if(_r >= mid + 1)
				sum += findsum(_l,_r,rson);
			return sum;
			update(x);
		}
	}
}tr;

void test(){
	int n;cin >> n;
	map<int,int> mp;
	vector<pair<int,int>> pa(n + 1);
	for(int i = 1;i <= n;i++){
		cin >> pa[i].first >> pa[i].second;
		mp[pa[i].first] = 1;
		mp[pa[i].second] = 1;
	}
	int ptop = 1;
	for(pair<int,int> pa : mp){
		int x = pa.first,y = pa.second;
		mp[x] = ptop;
		ptop++;
	}
	for(int i = 1;i <= n;i++){
		pa[i].first = mp[pa[i].first];
		pa[i].second = mp[pa[i].second];
	}
	tr.build(1,2e4,1);
	ptop = 1;
	for(int i = 1;i <= n;i++){
		if(pa[i].first != 0){
			tr.turn(pa[i].first,pa[i].second,1,ptop);
			ptop++;
		}
	}
	set<int> se;
	for(int i = 1;i <= 2e4;i++){
		int tem = tr.findsum(i,i,1);
		if(tem != 0){
			se.insert(tem);
		}
	}
	cout << se.size() << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```



## [D - Just a Hook](https://vjudge.net.cn/problem/HDU-1698)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 1e5 + 10;
int a[MAXN];
class tree{
	public:
	ll sum[MAXN*4];//代表的和
	ll ma[MAXN*4];//代表的最大值
	ll mi[MAXN*4];//代表的最小值
	bool fadd[MAXN*4];//是否启用标签
	ll ladd[MAXN*4];//加法标签
	bool fturn[MAXN*4];
	ll lturn[MAXN*4];//变换标签
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		fadd[x] = fturn[x] = ladd[x] = lturn[x] = 0;
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = a[_l];
			ma[x] = a[_l];
			mi[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void add(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			ladd[x] += v;
			fadd[x] = 1;
			ma[x] += v;
			mi[x] += v;
			sum[x] += v*(r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				add(_l,_r,lson,v);
			if(_r >= mid + 1)
				add(_l,_r,rson,v);
			update(x);
		}
	}
	void turn(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			lturn[x] = v;
			fturn[x] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
			ma[x] = v;
			mi[x] = v;
			sum[x] = v * (r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				turn(_l,_r,lson,v);
			if(_r >= mid + 1)
				turn(_l,_r,rson,v);
			update(x);
		}
	}
	void update(int x){
		ma[x] = max(ma[lson],ma[rson]);
		mi[x] = min(mi[lson],mi[rson]);
		sum[x] = sum[lson] + sum[rson];
	}
	void down(int x){
		if(fturn[x])
		{
			fturn[lson] = fturn[rson] = 1;
			lturn[lson] = lturn[rson] = lturn[x];
			ma[lson] = ma[rson] = lturn[x];
			mi[lson] = mi[rson] = lturn[x];
			sum[lson] = lturn[x]*(r[lson]-l[lson]+1);
			sum[rson] = lturn[x]*(r[rson]-l[rson]+1);
			fadd[lson] = fadd[rson] = 0;
			fturn[x] = 0;
		}
		if(fadd[x])
		{
			fadd[lson] = fadd[rson] = 1;
			ma[lson] += ladd[x];
			ma[rson] += ladd[x];
			mi[lson] += ladd[x];
			mi[rson] += ladd[x];
			sum[lson] += ladd[x]*(r[lson]-l[lson]+1);
			sum[rson] += ladd[x]*(r[rson]-l[rson]+1);
			ladd[lson] += ladd[x];
			ladd[rson] += ladd[x];
			fadd[lson] = fadd[rson] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
		}
	}
	ll findma(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return ma[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll ma = 0;
			if(_l <= mid)
				ma = findma(_l,_r,lson);
			if(_r >= mid + 1)
				ma = max(ma,findma(_l,_r,rson));
			update(x);
			return ma;
		}
	}
	ll findmi(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return mi[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll mi = 1e9 + 10;
			if(_l <= mid)
				mi = findmi(_l,_r,lson);
			if(_r >= mid + 1)
				mi = min(mi,findmi(_l,_r,rson));
			update(x);
			return mi;
		}
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum += findsum(_l,_r,lson);
			if(_r >= mid + 1)
				sum += findsum(_l,_r,rson);
			return sum;
			update(x);
		}
	}
}tr;

int ptop = 0;

void test(){
	ptop++;
	cout << "Case " << ptop << ": The total value of the hook is ";
	int n,q;cin >> n >> q;
	for(int i = 1;i <= n;i++) a[i] = 1;
	tr.build(1,n,1);
	while(q--){
		int l,r,z;cin >> l >> r >> z;
		tr.turn(l,r,1,z);
	}
	cout << tr.findsum(1,n,1) << '.';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```



## [E - A Simple Problem with Integers](https://vjudge.net.cn/problem/OpenJ_Bailian-3243)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 1e5 + 10;
int a[MAXN];
class tree{
	public:
	ll sum[MAXN*4];//代表的和
	ll ma[MAXN*4];//代表的最大值
	ll mi[MAXN*4];//代表的最小值
	bool fadd[MAXN*4];//是否启用标签
	ll ladd[MAXN*4];//加法标签
	bool fturn[MAXN*4];
	ll lturn[MAXN*4];//变换标签
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		fadd[x] = fturn[x] = ladd[x] = lturn[x] = 0;
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = a[_l];
			ma[x] = a[_l];
			mi[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void add(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			ladd[x] += v;
			fadd[x] = 1;
			ma[x] += v;
			mi[x] += v;
			sum[x] += v*(r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				add(_l,_r,lson,v);
			if(_r >= mid + 1)
				add(_l,_r,rson,v);
			update(x);
		}
	}
	void turn(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			lturn[x] = v;
			fturn[x] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
			ma[x] = v;
			mi[x] = v;
			sum[x] = v * (r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				turn(_l,_r,lson,v);
			if(_r >= mid + 1)
				turn(_l,_r,rson,v);
			update(x);
		}
	}
	void update(int x){
		ma[x] = max(ma[lson],ma[rson]);
		mi[x] = min(mi[lson],mi[rson]);
		sum[x] = sum[lson] + sum[rson];
	}
	void down(int x){
		if(fturn[x])
		{
			fturn[lson] = fturn[rson] = 1;
			lturn[lson] = lturn[rson] = lturn[x];
			ma[lson] = ma[rson] = lturn[x];
			mi[lson] = mi[rson] = lturn[x];
			sum[lson] = lturn[x]*(r[lson]-l[lson]+1);
			sum[rson] = lturn[x]*(r[rson]-l[rson]+1);
			fadd[lson] = fadd[rson] = 0;
			fturn[x] = 0;
		}
		if(fadd[x])
		{
			fadd[lson] = fadd[rson] = 1;
			ma[lson] += ladd[x];
			ma[rson] += ladd[x];
			mi[lson] += ladd[x];
			mi[rson] += ladd[x];
			sum[lson] += ladd[x]*(r[lson]-l[lson]+1);
			sum[rson] += ladd[x]*(r[rson]-l[rson]+1);
			ladd[lson] += ladd[x];
			ladd[rson] += ladd[x];
			fadd[lson] = fadd[rson] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
		}
	}
	ll findma(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return ma[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll ma = 0;
			if(_l <= mid)
				ma = findma(_l,_r,lson);
			if(_r >= mid + 1)
				ma = max(ma,findma(_l,_r,rson));
			update(x);
			return ma;
		}
	}
	ll findmi(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return mi[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll mi = 1e9 + 10;
			if(_l <= mid)
				mi = findmi(_l,_r,lson);
			if(_r >= mid + 1)
				mi = min(mi,findmi(_l,_r,rson));
			update(x);
			return mi;
		}
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum += findsum(_l,_r,lson);
			if(_r >= mid + 1)
				sum += findsum(_l,_r,rson);
			return sum;
			update(x);
		}
	}
}tr;

void test(){
	int n,q;cin >> n >> q;
	for(int i = 1;i <= n;i++) cin >> a[i];
	tr.build(1,n,1);
	while(q--){
		char c;cin >> c;
		int l,r;cin >> l >> r;
		if(c == 'Q'){
			cout << tr.findsum(l,r,1) << '\n';
		}else{
			int cc;cin >> cc;
			tr.add(l,r,1,cc);
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



## [F - Can you answer these queries?](https://vjudge.net.cn/problem/HDU-4027)

```cpp
// xxc2
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 1e5 + 10;
ll a[MAXN];
class tree{
	public:
	ll sum[MAXN*4];//代表的和
	bool fadd[MAXN*4];//是否启用标签
	ll ladd[MAXN*4];//加法标签
	bool fturn[MAXN*4];
	ll lturn[MAXN*4];//变换标签
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		fadd[x] = fturn[x] = ladd[x] = lturn[x] = 0;
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void add(int _l,int _r,int x,int v){
		if(ladd[x] >= 6) return;//6次必定变1
		if(_l <= l[x] && _r >= r[x])
		{
			down(x);
		}
		else
		{
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				add(_l,_r,lson,v);
			if(_r >= mid + 1)
				add(_l,_r,rson,v);
			update(x);
		}
	}

	void down(int x){
		if(ladd[x] >= 6) return;
		ladd[x]++;
		if(l[x] == r[x]){
			a[l[x]] = (ll)sqrt(a[l[x]]*1.0);
			sum[x] = a[l[x]];
		}else{
			down(lson);
			down(rson);
			update(x);
		}
	}

	void update(int x){
		sum[x] = sum[lson] + sum[rson];
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum += findsum(_l,_r,lson);
			if(_r >= mid + 1)
				sum += findsum(_l,_r,rson);
			return sum;
		}
	}
}tr;

int ptop = 0;

void test(){
	int n;
	while(cin >> n){
		ptop++;
		cout << "Case #" << ptop << ":\n";
		for(int i = 1;i <= n;i++) cin >> a[i];
		tr.build(1,n,1);
		int q;cin >> q;
		while(q--){
			int t,l,r;cin >> t >> l >> r;
			if(l > r) swap(l,r);
			if(t == 0){
				tr.add(l,r,1,1);
			}else if(t == 1){
				cout << tr.findsum(l,r,1) << '\n';
			}
		}
		cout << '\n';
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



## [G - Pillars](https://vjudge.net.cn/problem/CodeForces-474E)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 1e5 + 10;
int a[MAXN];
class tree{
	public:
	ll sum[MAXN*4];//代表的和
	ll ma[MAXN*4];//代表的最大值
	ll mi[MAXN*4];//代表的最小值
	int id[MAXN*4];
	bool fadd[MAXN*4];//是否启用标签
	ll ladd[MAXN*4];//加法标签
	bool fturn[MAXN*4];
	ll lturn[MAXN*4];//变换标签
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		fadd[x] = fturn[x] = ladd[x] = lturn[x] = 0;
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			id[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void add(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			ladd[x] += v;
			fadd[x] = 1;
			ma[x] += v;
			mi[x] += v;
			sum[x] += v*(r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				add(_l,_r,lson,v);
			if(_r >= mid + 1)
				add(_l,_r,rson,v);
			update(x);
		}
	}
	void turn(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			lturn[x] = v;
			fturn[x] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
			ma[x] = v;
			mi[x] = v;
			sum[x] = v * (r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				turn(_l,_r,lson,v);
			if(_r >= mid + 1)
				turn(_l,_r,rson,v);
			update(x);
		}
	}
	void update(int x){
		if(ma[lson] > ma[rson]){
			id[x] = id[lson];
		}else{
			id[x] = id[rson];
		}
		ma[x] = max(ma[lson],ma[rson]);
		mi[x] = min(mi[lson],mi[rson]);
		sum[x] = sum[lson] + sum[rson];
	}
	void down(int x){
		if(fturn[x])
		{
			fturn[lson] = fturn[rson] = 1;
			lturn[lson] = lturn[rson] = lturn[x];
			ma[lson] = ma[rson] = lturn[x];
			mi[lson] = mi[rson] = lturn[x];
			sum[lson] = lturn[x]*(r[lson]-l[lson]+1);
			sum[rson] = lturn[x]*(r[rson]-l[rson]+1);
			fadd[lson] = fadd[rson] = 0;
			fturn[x] = 0;
		}
	}
	pair<ll,int> findma(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return make_pair(ma[x],id[x]);
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			pair<ll,int> ma = {0,0};
			if(_l <= mid)
				ma = findma(_l,_r,lson);
			if(_r >= mid + 1)
				ma = max(ma,findma(_l,_r,rson));
			update(x);
			return ma;
		}
	}
	ll findmi(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return mi[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll mi = 1e9 + 10;
			if(_l <= mid)
				mi = findmi(_l,_r,lson);
			if(_r >= mid + 1)
				mi = min(mi,findmi(_l,_r,rson));
			update(x);
			return mi;
		}
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum += findsum(_l,_r,lson);
			if(_r >= mid + 1)
				sum += findsum(_l,_r,rson);
			return sum;
			update(x);
		}
	}
}tr;

int ptop = 0;
int fa[MAXN];

void test(){
	int n;
	ll d;cin >> n >> d;
	vector<ll> h(n + 1);
	vector<pair<ll,int>> hh(n + 1);
	vector<ll> sort_h(n + 1);
	vector<int> pos(n + 1);
	for(int i = 1;i <= n;i++){
		cin >> h[i];
		hh[i].first = h[i];
		hh[i].second = i;
		sort_h[i] = h[i];
	}
	sort(sort_h.begin() + 1,sort_h.end());
	sort(hh.begin() + 1,hh.end());
	for(int i = 1;i <= n;i++){
		a[i] = hh[i].second;
		pos[a[i]] = i;
	}
	int ptop = 1;
	vector<pair<int,int>> pa(n + 1);
	tr.build(1,n,1);
	for(int i = 1;i <= n;i++){
		int itl = lower_bound(sort_h.begin() + 1,sort_h.end(),h[i] - d + 1) - sort_h.begin();
		itl--;
		auto itr = lower_bound(sort_h.begin() + 1,sort_h.end(),h[i] + d) - sort_h.begin();
		pair<ll,int> ma = {0,0};
		if(itl > 0)
			ma = max(ma,tr.findma(1,itl,1));
		if(itr <= n)
			ma = max(ma,tr.findma(itr,n,1));
		fa[i] = ma.second;
		tr.turn(pos[i],pos[i],1,ma.first + 1);
	}
	auto [ans,x] = tr.findma(1,n,1);
	cout << ans << '\n';
	stack<int> st;
	st.push(x);
	ans--;
	while(ans--){
		x = fa[x];
		st.push(x);
	}
	while(!st.empty()){
		cout << st.top() << ' ';
		st.pop();
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



## [H - 小白逛公园](https://vjudge.net.cn/problem/洛谷-P4513)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 5e5 + 10;
int a[MAXN];
class tree{
	public:
	ll lma[MAXN*4],rma[MAXN*4];//代表的和
	ll sum[MAXN*4];
	ll ma[MAXN*4];//代表的最大值
	bool fturn[MAXN*4];
	ll lturn[MAXN*4];//变换标签
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = lma[x] = rma[x] = ma[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void turn(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			sum[x] = lma[x] = rma[x] = ma[x] = v;
		}
		else
		{
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				turn(_l,_r,lson,v);
			if(_r >= mid + 1)
				turn(_l,_r,rson,v);
			update(x);
		}
	}
	void update(int x){
		lma[x] = max(lma[lson],sum[lson] + lma[rson]);
		rma[x] = max(rma[rson],sum[rson] + rma[lson]);
		ma[x] = max({ma[lson],ma[rson],lma[x],rma[x],rma[lson]+lma[rson]});
		sum[x] = sum[lson] + sum[rson];
	}
	tuple<int,int,int,int> findma(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x]){
			return make_tuple(lma[x],ma[x],rma[x],sum[x]);
		}
		else
		{
			int mid = (l[x] + r[x]) / 2;
			tuple<int,int,int,int> res;
			auto &[_lma,_ma,_rma,_sum] = res;
			_lma = -inf;
			_ma = -inf;
			_rma = -inf;
			if(_l > mid)
				return findma(_l,_r,rson);
			if(_r < mid + 1)
				return findma(_l,_r,lson);
			int llma = -inf,lma = -inf,lrma = -inf,lsum = 0,rlma = -inf,rma = -inf,rrma = -inf,rsum = 0;
			tie(llma,lma,lrma,lsum) = findma(_l,_r,lson);
			tie(rlma,rma,rrma,rsum) = findma(_l,_r,rson);	
			_lma = max(llma,lsum + rlma);
			_rma = max(rrma,rsum + lrma);
			_ma = max({lma,rma,_lma,_rma,lrma+rlma});
			_sum = lsum + rsum;
			return res;
		}
	}
}tr;

void test(){
	int n,m;cin >> n >> m;
	for(int i = 1;i <= n;i++) cin >> a[i];
	tr.build(1,n,1);
	for(int i = 1;i <= m;i++){
		int k;cin >> k;
		if(k == 1){
			int a,b;cin >> a >> b;
			if(a > b) swap(a,b);
			auto [x1,x2,x3,x4] = tr.findma(a,b,1);
			cout << x2 << '\n';
		}else{
			int p,s;cin >> p >> s;
			tr.turn(p,p,1,s);
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



## [I - 序列](https://vjudge.net.cn/problem/洛谷-P7492)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 1e5 + 10;
int a[MAXN];
class tree{
	public:
	ll lma[MAXN*4],rma[MAXN*4];//代表的和
	ll sum[MAXN*4];
	ll ma[MAXN*4];//代表的最大值
	int tem[MAXN*4];
	bool fturn[MAXN*4];
	ll lturn[MAXN*4];//变换标签
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = lma[x] = rma[x] = ma[x] = a[_l];
			ma[x] = max(ma[x],(ll)0);
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void turn(int _l,int _r,int x,int v){
		if((tem[x] | v) == tem[x]){
			return;
		}
		if(_l <= l[x] && _r >= r[x])
		{
			tem[x] |= v;
			if(l[x] == r[x]){
				lma[x] = rma[x] = ma[x] = sum[x] = sum[x] | v;
				ma[x] = max(ma[x],(ll)0);
			}else{
				turn(l[lson],r[lson],lson,v);
				turn(l[rson],r[rson],rson,v);
				update(x);
			}
		}
		else
		{
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				turn(_l,_r,lson,v);
			if(_r >= mid + 1)
				turn(_l,_r,rson,v);
			update(x);
		}
	}
	void update(int x){
		lma[x] = max(lma[lson],sum[lson] + lma[rson]);
		rma[x] = max(rma[rson],sum[rson] + rma[lson]);
		ma[x] = max({ma[lson],ma[rson],rma[lson]+lma[rson]});
		ma[x] = max(ma[x],(ll)0);
		sum[x] = sum[lson] + sum[rson];
	}
	tuple<ll,ll,ll,ll> findma(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x]){
			return make_tuple(lma[x],ma[x],rma[x],sum[x]);
		}
		else
		{
			int mid = (l[x] + r[x]) / 2;
			tuple<ll,ll,ll,ll> res;
			auto &[_lma,_ma,_rma,_sum] = res;
			_lma = 0;
			_ma = 0;
			_rma = 0;
			if(_l > mid)
				return findma(_l,_r,rson);
			if(_r < mid + 1)
				return findma(_l,_r,lson);
			ll llma = 0,lma = 0,lrma = 0,lsum = 0,rlma = 0,rma = 0,rrma = 0,rsum = 0;
			tie(llma,lma,lrma,lsum) = findma(_l,_r,lson);
			tie(rlma,rma,rrma,rsum) = findma(_l,_r,rson);	
			_lma = max(llma,lsum + rlma);
			_rma = max(rrma,rsum + lrma);
			_ma = max({lma,rma,lrma+rlma});
			_sum = lsum + rsum;
			return res;
		}
	}
}tr;

void test(){
	int n,m;cin >> n >> m;
	for(int i = 1;i <= n;i++) cin >> a[i];
	tr.build(1,n,1);
	for(int i = 1;i <= m;i++){
		int k;cin >> k;
		if(k == 1){
			int a,b;cin >> a >> b;
			if(a > b) swap(a,b);
			auto [x1,x2,x3,x4] = tr.findma(a,b,1);
			cout << x2 << '\n';
		}else{
			int l,r,k;cin >> l >> r >> k;
			tr.turn(l,r,1,k);
		}
	}
}

signed main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```



## [J - SUM and REPLACE](https://vjudge.net.cn/problem/CodeForces-920F)

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;
#define Max(x,y) (x < y ? y : x)

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 3e5 + 10,MAXA = 1e6 + 10;
int a[MAXN];

int d[MAXA];

bool f[MAXA];
int ptop = 0;

void init(){
	for(int i = 1;i <= 1e6;i++)
		for(int j = i;j <= 1e6;j += i)
			d[j]++;
}

class tree{
	public:
	ll sum[MAXN*4];//代表的和
	int ma[MAXN*4];
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			ma[x] = sum[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void add(int _l,int _r,int x){
		if(ma[x] <= 2) return;

		if(l[x] == r[x]){
			sum[x] = d[sum[x]];
			ma[x] = sum[x];
		}
		else
		{
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				add(_l,_r,lson);
			if(_r >= mid + 1)
				add(_l,_r,rson);
			update(x);
		}
	}
	void update(int x){
		ma[x] = Max(ma[lson],ma[rson]);
		sum[x] = sum[lson] + sum[rson];
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum += findsum(_l,_r,lson);
			if(_r >= mid + 1)
				sum += findsum(_l,_r,rson);
			return sum;
		}
	}
}tr;


void test(){
	int n,m;cin >> n >> m;
	init();
	for(int i = 1;i <= n;i++) cin >> a[i];
	tr.build(1,n,1);
	while(m--){
		int op;cin >> op;
		int l,r;cin >> l >> r;
		if(op == 1){
			tr.add(l,r,1);
		}else{
			cout << tr.findsum(l,r,1) << '\n';
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



## [K - The Child and Sequence](https://vjudge.net.cn/problem/CodeForces-438D)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 1e5 + 10;
int a[MAXN];
class tree{
	public:
	ll sum[MAXN*4];//代表的和
	ll ma[MAXN*4];//代表的最大值
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = a[_l];
			ma[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void set(int k,int x,int v){
		if(l[x] == r[x]){
			sum[x] = ma[x] = v;
		}else{
			int mid = (l[x] + r[x]) / 2;
			if(k <= mid) set(k,lson,v);
			else set(k,rson,v);
			update(x);
		}
	}
	void turn(int _l,int _r,int x,int v){
		if(ma[x] < v) return;
		if(_l <= l[x] && _r >= r[x])
		{
			if(l[x] == r[x]){
				sum[x] = ma[x] = sum[x] % v;
			}else{
				turn(_l,_r,lson,v);
				turn(_l,_r,rson,v);
				update(x);
			}
		}
		else
		{
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				turn(_l,_r,lson,v);
			if(_r >= mid + 1)
				turn(_l,_r,rson,v);
			update(x);
		}
	}
	void update(int x){
		ma[x] = max(ma[lson],ma[rson]);
		sum[x] = sum[lson] + sum[rson];
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum += findsum(_l,_r,lson);
			if(_r >= mid + 1)
				sum += findsum(_l,_r,rson);
			return sum;
			update(x);
		}
	}
}tr;

void test(){
	int n,m;cin >> n >> m;
	for(int i = 1;i <= n;i++) cin >> a[i];
	tr.build(1,n,1);
	while(m--){
		int op;cin >> op;
		if(op == 1){
			int l,r;cin >> l >> r;
			cout << tr.findsum(l,r,1) << '\n';
		}else if(op == 2){
			int l,r;cin >> l >> r;
			int x;cin >> x;
			tr.turn(l,r,1,x);
		}else{
			int k,x;cin >> k >> x;
			tr.set(k,1,x);
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



## [L - Subarray Sorting](https://vjudge.net.cn/problem/CodeForces-1187D)

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
#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 3e5 + 10;
int a[MAXN];
int id[MAXN];
class tree{
	public:
	pair<ll,int> ma[MAXN*4];//代表的最大值
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			ma[x].first = a[_l];
			ma[x].second = _l;
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void turn(int pos,int x,int v){
		if(l[x] == r[x]){
			ma[x].first = v;
			ma[x].second = -1;
		}else{
			int mid = (l[x] + r[x]) / 2;
			if(pos > mid) turn(pos,rson,v);
			else turn(pos,lson,v);
			update(x);
		}
	}
	void update(int x){
		ma[x] = max(ma[lson],ma[rson]);
	}
	int find(int x,int v){
		if(l[x] == r[x] && ma[x].first != v){
			return -1;
		}
		if(ma[x].first < v){
			return -1;
		}
		if(ma[x].first == v){
			return ma[x].second;
		}else{
			if(ma[rson].first >= v){
				return find(rson,v);
			}else{
				return find(lson,v);
			}
		}
	}
}tr;

void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++) cin >> a[i];
	tr.build(1,n,1);
	vector<int> b(n + 1);
	for(int i = 1;i <= n;i++) cin >> b[i];
	for(int i = n;i >= 1;i--){
		int pos = tr.find(1,b[i]);
		if(pos == -1){
			cout << "No\n";
			return;
		}else{
			tr.turn(pos,1,0);
		}
	}
	cout << "Yes\n";
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```



## [M - Sereja and Brackets](https://vjudge.net.cn/problem/CodeForces-380C)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 1e6 + 10;
int a[MAXN];
class tree{
	public:
	ll sum[MAXN*4];//代表的和
	ll lsum[MAXN*4];
	ll rsum[MAXN*4];
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = 0;
			if(a[_l] == 1) lsum[x] = 1;
			else rsum[x] = 1;
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void update(int x){
		int mi = min(lsum[lson],rsum[rson]);
		sum[x] = sum[lson] + sum[rson] + mi;
		lsum[x] = lsum[lson] + lsum[rson] - mi;
		rsum[x] = rsum[lson] + rsum[rson] - mi;
	}
	tuple<int,int,int> findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return make_tuple(sum[x],lsum[x],rsum[x]);
		else
		{
			int mid = (l[x] + r[x]) / 2;
			tuple<int,int,int> res;
			auto &[_sum,_lsum,_rsum] = res;

			if(_l > mid)
				return findsum(_l,_r,rson);
			if(_r < mid + 1)
				return findsum(_l,_r,lson);

			auto [l_sum,l_lsum,l_rsum] = findsum(_l,_r,lson);
			auto [r_sum,r_lsum,r_rsum] = findsum(_l,_r,rson);

			int mi = min(l_lsum,r_rsum);

			_sum = mi + l_sum + r_sum;
			_lsum = l_lsum + r_lsum - mi;
			_rsum = l_rsum + r_rsum - mi;

			return res;
		}
	}
}tr;

void test(){
	string s;cin >> s;
	int n = s.size();
	for(int i = 0;i < n;i++){
		if(s[i] == '(') a[i + 1] = 1;
		else a[i + 1] = 0;
	}
	tr.build(1,n,1);
	int q;cin >> q;
	while(q--){
		int l,r;cin >> l >> r;
		cout << get<0>(tr.findsum(l,r,1)) * 2 << '\n';
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

## [N - Rmq Problem / mex](https://vjudge.net.cn/problem/洛谷-P4137)

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

int a[N];

int root[N];//每个树的根

int ptop = 1;

struct Persistable_Segment_Tree{
	int lson[N*32],rson[N*32];
	int mi[N*32];
	void build(int l,int r,int x){//建初始树
		if(l == r) return;
		else{
			int mid = (l + r) >> 1;
			lson[x] = ++ptop;
			rson[x] = ++ptop;
			build(l,mid,lson[x]);
			build(mid + 1,r,rson[x]);
		}
	}
	void updata(int x){
		mi[x] = min(mi[lson[x]],mi[rson[x]]);
	}
	void ins(int l,int r,int no,int &update,int x,int i){//插入值,l,r现在处理的区间,现在的节点,updata
		update = ++ptop,lson[ptop] = lson[no],rson[ptop] = rson[no];//向前更新,copy下面的树
		if(l == r) mi[ptop] = x;
		else{
			int mid = (l + r) >> 1;
			if(i > mid) ins(mid + 1,r,rson[no],rson[ptop],x,i);
			else ins(l,mid,lson[no],lson[ptop],x,i);
			updata(update);
		}
	}
	int query(int l,int r,int x,int _l){
		if(l == r) return l;
		else{
			int mid = (l + r) >> 1;
			if(mi[lson[x]] < _l) return query(l,mid,lson[x],_l);
			else return query(mid + 1,r,rson[x],_l);
		}
	}
}tr;

void test(){
	int n,m;cin >> n >> m;
	vector<int> ve(n + 1);
	tr.build(1,2e5 + 1,1);
	root[0] = 1;
	for(int i = 1;i <= n;i++){
		cin >> ve[i];
		ve[i]++;
		tr.ins(1,2e5 + 1,root[i - 1],root[i],i,ve[i]);
	}
	for(int i = 1;i <= m;i++){
		int l,r;cin >> l >> r;
		cout << tr.query(1,2e5 + 1,root[r],l) - 1 << '\n';
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

## [O - KUR-Couriers](https://vjudge.net.cn/problem/洛谷-P3567)

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 5e5 + 10;

int a[N];

int root[N];

int ptop = 1;

struct Persistable_Segment_Tree{
	int lson[N*32] = {0},rson[N*32] = {0},sum[N*32] = {0};
	void build(int l,int r,int x){
		if(l == r) return;
		else{
			int mid = (l + r) >> 1;
			lson[x] = ++ptop;
			rson[x] = ++ptop;
			build(l,mid,lson[x]);
			build(mid + 1,r,rson[x]);
		}
	}
	void ins(int l,int r,int no,int val){
		ptop++;
		sum[ptop] = sum[no] + 1;
		if(l != r){
			int mid = (l + r) >> 1;
			if(val > mid) rson[ptop] = ptop + 1,lson[ptop] = lson[no],ins(mid + 1,r,rson[no],val);
			else lson[ptop] = ptop + 1,rson[ptop] = rson[no],ins(l,mid,lson[no],val);
		}
	}
	int query(int preroot,int sufroot,int num,int l,int r){
		if(l == r) return l;
		else{
			int mid = (l + r) >> 1;
			if(sum[lson[sufroot]] - sum[lson[preroot]] > num) return query(lson[preroot],lson[sufroot],num,l,mid);
			else if(sum[rson[sufroot]] - sum[rson[preroot]] > num) return query(rson[preroot],rson[sufroot],num,mid + 1,r);
			else return 0;
		}
	}
}tr;

void test(){
	int n,m;cin >> n >> m;
	for(int i = 1;i <= n;i++) cin >> a[i];
	root[0] = 1;
	tr.build(1,n,1);
	for(int i = 1;i <= n;i++){
		root[i] = ptop + 1;
		tr.ins(1,n,root[i - 1],a[i]);
	}
	for(int i = 1;i <= m;i++){
		int l,r;cin >> l >> r;
		cout << tr.query(root[l - 1],root[r],(r - l + 1) / 2,1,n) << '\n';
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

## [P - Cut and Stick](https://vjudge.net.cn/problem/CodeForces-1514D)

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


const int N = 3e5 + 10;

int a[N];

int root[N];//每个树的根

int ptop = 1;

struct Persistable_Segment_Tree{
	int lson[N*32],rson[N*32];
	int sum[N*32];
	void build(int l,int r,int x){//建初始树
		if(l == r) return;
		else{
			int mid = (l + r) >> 1;
			lson[x] = ++ptop;
			rson[x] = ++ptop;
			build(l,mid,lson[x]);
			build(mid + 1,r,rson[x]);
		}
	}
	void updata(int x){
		sum[x] = sum[lson[x]] + sum[rson[x]];
	}
	void ins(int l,int r,int no,int &update,int x){//插入值,l,r现在处理的区间,现在的节点,updata
		update = ++ptop,lson[ptop] = lson[no],rson[ptop] = rson[no];//向前更新,copy下面的树
		if(l == r) sum[ptop] = ++a[x];
		else{
			int mid = (l + r) >> 1;
			if(x > mid) ins(mid + 1,r,rson[no],rson[ptop],x);
			else ins(l,mid,lson[no],lson[ptop],x);
			updata(update);
		}
	}
	int query(int l,int r,int lx,int rx,int ned){
		if(l == r) return sum[rx] - sum[lx];
		else{
			int mid = (l + r) >> 1;
			if(sum[lson[rx]] - sum[lson[lx]] > ned){
				return query(l,mid,lson[lx],lson[rx],ned);
			}
			if(sum[rson[rx]] - sum[rson[lx]] > ned){
				return query(mid + 1,r,rson[lx],rson[rx],ned);
			}
			return 0;
		}
	}
}tr;

void test(){
	int n,m;cin >> n >> m;
	vector<int> ve(n + 1);
	tr.build(1,3e5 + 1,1);
	root[0] = 1;
	for(int i = 1;i <= n;i++){
		cin >> ve[i];
		tr.ins(1,3e5 + 1,root[i - 1],root[i],ve[i]);
	}
	while(m--){
		int l,r;cin >> l >> r;
		int ct = tr.query(1,3e5 + 1,root[l - 1],root[r],(r - l + 2) / 2);
		cout << 1 + max(ct - (r - l + 1 - ct + 1),0) << '\n';
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

## [Q - Bash and a Tough Math Puzzle](https://vjudge.net.cn/problem/CodeForces-914D)

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

int gcd(int x,int y){
	if(y == 0) return x;
	else return gcd(y,x%y);
}

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 1e6 + 10;
int a[MAXN];
int cnt = 0;
class tree{
	public:
	ll gc[MAXN*4];
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			gc[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void turn(int pos,int x,int v){
		if(l[x] == r[x]) gc[x] = v;
		else
		{
			int mid = (l[x] + r[x]) / 2;
			if(pos <= mid)
				turn(pos,lson,v);
			if(pos >= mid + 1)
				turn(pos,rson,v);
			update(x);
		}
	}
	void update(int x){
		gc[x] = gcd(gc[lson],gc[rson]);
	}
	void findgc(int _l,int _r,int x,int gcc)
	{
		if(cnt >= 2) return;
		if(gc[x] % gcc == 0) return;
		if(l[x] == r[x]) cnt++;
		int mid = (l[x] + r[x]) / 2;
		
		if(_l > mid){
			findgc(_l,_r,rson,gcc);
			return;
		}
		if(_r < mid + 1){
			findgc(_l,_r,lson,gcc);
			return;
		}
		findgc(_l,_r,lson,gcc);
		findgc(_l,_r,rson,gcc);
	}
}tr;

void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++) cin >> a[i];
	tr.build(1,n,1);
	int q;cin >> q;
	while(q--){
		int op;cin >> op;
		if(op == 1){
			int l,r,x;cin >> l >> r >> x;
			cnt = 0;
			tr.findgc(l,r,1,x);
			if(cnt >= 2) cout << "NO\n";
			else cout << "YES\n";
		}else{
			int i,y;cin >> i >> y;
			tr.turn(i,1,y);
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

## [R - Non-Decreasing Dilemma](https://vjudge.net.cn/problem/CodeForces-1567E)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 2e5 + 10;
int a[MAXN];
class tree{
	public:
	ll sum[MAXN*4];
	pair<int,ll> lsum[MAXN*4],rsum[MAXN*4];
	bool f[MAXN*4];
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			f[x] = 1;
			lsum[x].first = a[_l];
			lsum[x].second = 1;
			rsum[x].first = a[_l];
			rsum[x].second = 1;
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void turn(int pos,int x,int v){
		if(l[x] == r[x])
		{
			f[x] = 1;
			lsum[x].first = v;
			lsum[x].second = 1;
			rsum[x].first = v;
			rsum[x].second = 1;
		}
		else
		{
			int mid = (l[x] + r[x]) / 2;
			if(pos <= mid)
				turn(pos,lson,v);
			if(pos >= mid + 1)
				turn(pos,rson,v);
			update(x);
		}
	}

	tuple<ll,pair<int,ll>,pair<int,ll>,bool> update(tuple<ll,pair<int,ll>,pair<int,ll>,bool> l,tuple<ll,pair<int,ll>,pair<int,ll>,bool> r){
		auto [_lsum,_llsum,_lrsum,_lf] = l;
		auto [_rsum,_rlsum,_rrsum,_rf] = r;
		tuple<ll,pair<int,ll>,pair<int,ll>,bool> res;
		auto&[__sum,__lsum,__rsum,__f] = res;
		if(_lf && _rf && _lrsum.first <= _rlsum.first){
			__lsum.first = _llsum.first;
			__rsum.first = _rrsum.first;
			__lsum.second = __rsum.second = _llsum.second + _rrsum.second;
			__sum = 0;
			__f = 1;
		}else{
			__f = 0;
			__lsum = _llsum;
			__rsum = _rrsum;
			__sum = _lsum + _rsum;
			if(_lf && _rf);
			else if(_lf){
				if(_lrsum.first <= _rlsum.first)//可连接
					__lsum.second += _rlsum.second;
				else
					__sum += _rlsum.second * (_rlsum.second + 1) / 2;
			}else if(_rf){
				if(_lrsum.first <= _rlsum.first)//可连接
					__rsum.second += _lrsum.second;
				else
					__sum += _lrsum.second * (_lrsum.second + 1) / 2;
			}else{
				if(_lrsum.first <= _rlsum.first){
					ll len = _rlsum.second + _lrsum.second;
					__sum += len * (len + 1) / 2;
				}else{
					ll len = _rlsum.second;
					__sum += len * (len + 1) / 2;
					len = _lrsum.second;
					__sum += len * (len + 1) / 2;
				}
			}
		}
		return res;
	}

	void update(int x){
		if(f[lson] && f[rson] && rsum[lson].first <= lsum[rson].first){
			lsum[x].first = lsum[lson].first;
			rsum[x].first = rsum[rson].first;
			lsum[x].second = rsum[x].second = lsum[lson].second + rsum[rson].second;
			sum[x] = 0;
			f[x] = 1;
		}else{
			f[x] = 0;
			lsum[x] = lsum[lson];
			rsum[x] = rsum[rson];
			sum[x] = sum[lson] + sum[rson];
			if(f[lson] && f[rson]);
			else if(f[lson]){
				if(rsum[lson].first <= lsum[rson].first)//可连接
					lsum[x].second += lsum[rson].second;
				else
					sum[x] += lsum[rson].second * (lsum[rson].second + 1) / 2;
			}else if(f[rson]){
				if(rsum[lson].first <= lsum[rson].first)//可连接
					rsum[x].second += rsum[lson].second;
				else
					sum[x] += rsum[lson].second * (rsum[lson].second + 1) / 2;
			}else{
				if(rsum[lson].first <= lsum[rson].first){
					ll len = lsum[rson].second + rsum[lson].second;
					sum[x] += len * (len + 1) / 2;
				}else{
					ll len = lsum[rson].second;
					sum[x] += len * (len + 1) / 2;
					len = rsum[lson].second;
					sum[x] += len * (len + 1) / 2;
				}
			}
		}
	}
	tuple<ll,pair<int,ll>,pair<int,ll>,bool> findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return make_tuple(sum[x],lsum[x],rsum[x],f[x]);
		else
		{
			int mid = (l[x] + r[x]) / 2;
			if(_l > mid)
				return findsum(_l,_r,rson);
			if(_r < mid + 1)
				return findsum(_l,_r,lson);
			return update(findsum(_l,_r,lson),findsum(_l,_r,rson));
		}
	}
}tr;

void test(){
	int n,q;cin >> n >> q;
	for(int i = 1;i <= n;i++) cin >> a[i];
	tr.build(1,n,1);
	while(q--){
		int t;cin >> t;
		if(t == 1){
			int x,y;cin >> x >> y;
			tr.turn(x,1,y);
		}else{
			int l,r;cin >> l >> r;
			auto [sum,lpa,rpa,f] = tr.findsum(l,r,1);
			if(f){
				sum += lpa.second * (lpa.second + 1) / 2;
			}else{
				sum += lpa.second * (lpa.second + 1) / 2 + rpa.second * (rpa.second + 1) / 2;
			}
			cout << sum << '\n';
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

## [S - Closest Equals](https://vjudge.net.cn/problem/CodeForces-522D)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 5e5 + 10;
int a[MAXN];
class tree{
	public:
	ll mi[MAXN*4];//代表的最小值
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			mi[x] = inf;
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void turn(int pos,int x,int v){
		if(l[x] == r[x])
		{
			mi[x] = min((ll)v,mi[x]);
		}
		else
		{
			int mid = (l[x] + r[x]) / 2;
			if(pos <= mid)
				turn(pos,lson,v);
			if(pos >= mid + 1)
				turn(pos,rson,v);
			update(x);
		}
	}
	void update(int x){
		mi[x] = min(mi[lson],mi[rson]);
	}
	ll findmi(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return mi[x];
		else
		{
			int mid = (l[x] + r[x]) / 2;
			ll mi = inf;
			if(_l <= mid)
				mi = findmi(_l,_r,lson);
			if(_r >= mid + 1)
				mi = min(mi,findmi(_l,_r,rson));
			update(x);
			return mi;
		}
	}
}tr;

void test(){
	unordered_map<int,int> mp;
	int n,m;cin >> n >> m;
	vector<int> ve(n + 1);
	vector<int> to(n + 1);
	for(int i = 1;i <= n;i++){
		cin >> ve[i];
	}
	for(int i = n;i >= 1;i--){
		to[i] = mp[ve[i]];
		mp[ve[i]] = i;
	}
	tr.build(1,n,1);
	vector<pair<pair<int,int>,int>> ask(m + 1);
	for(int i = 1;i <= m;i++){
		auto &[pa,ii] = ask[i];
		cin >> pa.first >> pa.second;
		ii = i;
	}
	sort(ask.begin() + 1,ask.end());
	int ptop = m;
	vector<int> ans(m + 1);
	for(int i = n;i >= 1;i--){
		if(to[i] != 0){
			tr.turn(to[i],1,to[i] - i);
		}
		while(ptop >= 1){
			auto [pa,ii] = ask[ptop];
			if(pa.first != i) break;
			ans[ii] = tr.findmi(1,pa.second,1);
			if(ans[ii] == inf) ans[ii] = -1;
			ptop--;
		}
	}
	for(int i = 1;i <= m;i++) cout << ans[i] << '\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [T - Mishka and Interesting sum](https://vjudge.net.cn/problem/CodeForces-703D)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 1e6 + 10;
int a[MAXN];
class tree{
	public:
	ll sum[MAXN*4];//代表的和
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void turn(int pos,int x,int v){
		if(l[x] == r[x])
		{
			sum[x] = v;
		}
		else
		{
			int mid = (l[x] + r[x]) / 2;
			if(pos <= mid)
				turn(pos,lson,v);
			if(pos >= mid + 1)
				turn(pos,rson,v);
			update(x);
		}
	}
	void update(int x){
		sum[x] = sum[lson] ^ sum[rson];
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum ^= findsum(_l,_r,lson);
			if(_r >= mid + 1)
				sum ^= findsum(_l,_r,rson);
			return sum;
			update(x);
		}
	}
}tr;

void test(){
	int n;cin >> n;
	vector<int> aa(n + 1);
	vector<int> pre(n + 1);
	for(int i = 1;i <= n;i++){
		cin >> aa[i];
		pre[i] = pre[i - 1] ^ aa[i];
	}
	tr.build(1,n,1);
	int m;cin >> m;
	vector<tuple<int,int,int>> ask(m + 1);
	for(int i = 1;i <= m;i++){
		auto &[l,r,ii] = ask[i];
		cin >> l >> r;
		ii = i;
	}
	sort(ask.begin(),ask.end());
	unordered_map<int,int> mp;
	int suf = n + 1;
	vector<int> ans(m + 1);
	for(int i = m;i >= 1;i--){
		auto [l,r,ii] = ask[i];
		while(suf != l){
			suf--;
			if(mp[aa[suf]] != 0){
				tr.turn(mp[aa[suf]],1,0);
			}
			mp[aa[suf]] = suf;
			tr.turn(mp[aa[suf]],1,aa[suf]);
		}
		ans[ii] = tr.findsum(l,r,1) ^ pre[r] ^ pre[l - 1];
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

## [U - Intervals](https://vjudge.net.cn/problem/OpenJ_Bailian-1201)

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

#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 5e4 + 10;
int a[MAXN];
class tree{
	public:
	ll mi[MAXN*4];//代表的最小值
	ll l[MAXN*4],r[MAXN*4];
	bool fadd[MAXN*4];//是否启用标签
	ll ladd[MAXN*4];//加法标签
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			mi[x] = a[_l];
			fadd[x] = ladd[x] = 0;
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void turn(int pos,int x,int v){
		if(l[x] == r[x])
		{
			fadd[x] = 0;
			ladd[x] = 0;
			mi[x] = v;
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(pos <= mid)
				turn(pos,lson,v);
			if(pos >= mid + 1)
				turn(pos,rson,v);
			update(x);
		}
	}
	void add(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			ladd[x] += v;
			fadd[x] = 1;
			mi[x] += v;
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				add(_l,_r,lson,v);
			if(_r >= mid + 1)
				add(_l,_r,rson,v);
			update(x);
		}
	}
	void down(int x){
		if(fadd[x])
		{
			fadd[lson] = fadd[rson] = 1;
			mi[lson] += ladd[x];
			mi[rson] += ladd[x];
			ladd[lson] += ladd[x];
			ladd[rson] += ladd[x];
			fadd[x] = 0;
			ladd[x] = 0;
		}
	}
	void update(int x){
		mi[x] = min(mi[lson],mi[rson]);
	}
	ll findmi(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return mi[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll mi = 1e9 + 10;
			if(_l <= mid)
				mi = findmi(_l,_r,lson);
			if(_r >= mid + 1)
				mi = min(mi,findmi(_l,_r,rson));
			update(x);
			return mi;
		}
	}
}tr;

bool cmp(tuple<int,int,int,int> a,tuple<int,int,int,int> b){
	return get<1>(a) < get<1>(b);
}

void test(){
	for(int i = 1;i <= 5e4 + 10;i++)
		a[i] = inf;
	tr.build(1,5e4 + 10,1);
	int n;cin >> n;
	vector<tuple<int,int,int,int>> ask(n + 1);
	vector<tuple<int,int,int,int>> ask2;
	for(int i = 1;i <= n;i++){
		// auto &[l,r,ned,ii] = ask[i];
		int l,r,ned,ii;
		cin >> l >> r >> ned;
		l++;
		r++;
		ii = i;
		ask[i] = make_tuple(l,r,ned,ii);
	}
	sort(ask.begin() + 1,ask.end());
	ask2 = ask;
	sort(ask2.begin() + 1,ask2.end(),cmp);
	int ans = 0;
	int ptop1 = 1,ptop2 = 1;
	for(int i = 1;i <= 5e4 + 10;i++){//l为加入条件,r为退出条件,r-ned+1为值
		while(ptop1 <= n){
			int l,r,ned,ii;
			tie(l,r,ned,ii) = ask[ptop1];
			// auto [l,r,ned,ii] = ask[ptop1];
			if(l == i){
				ptop1++;
				tr.turn(ii,1,r-ned+1);
			}
			else
				break;
		}
		while(ptop2 <= n){
			int l,r,ned,ii;
			tie(l,r,ned,ii) = ask2[ptop2];
			if(r < i){
				ptop2++;
				tr.turn(ii,1,inf);
			}else{
				break;
			}
		}
		if(tr.findmi(1,5e4 + 10,1) == i){
			ans++;
			tr.add(1,5e4 + 10,1,1);
		}
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

## [V - Petya and Array](https://vjudge.net.cn/problem/CodeForces-1042D)

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

void test(){
	cin >> n;
	n++;
	vector<ll> a(n + 1);
	vector<ll> ask(n + 1);
	ll t;
	cin >> t;
	a[1] = 0;
	for(int i = 2;i <= n;i++){
		cin >> a[i];
		a[i] += a[i - 1];
	}
	//find a[i] - a[j] < t
	//find a[j] > a[i] - t
	vector<ll> tem = a;
	sort(tem.begin() + 1,tem.end());
	ll ans = 0;
	add(lower_bound(tem.begin() + 1,tem.end(),0) - tem.begin(),1);
	for(int i = 2;i <= n;i++){
		ll aim = a[i] - t;
		aim = upper_bound(tem.begin() + 1,tem.end(),aim) - tem.begin();
		ans += query(n) - query(aim - 1);
		add(lower_bound(tem.begin() + 1,tem.end(),a[i]) - tem.begin(),1);
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

## [W - Subsegments](https://vjudge.net.cn/problem/CodeForces-69E)

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
	int n,k;cin >> n >> k;
	unordered_map<int,int> mp;
	set<int> se;
	vector<int> a(n + 1);
	for(int i = 1;i <= k - 1;i++){
		int x;cin >> x;
		mp[x]++;
		a[i] = x;
		if(mp[x] == 1) se.insert(-x);
		if(mp[x] == 2) se.erase(-x);
	}
	for(int i = k;i <= n;i++){
		int x;cin >> x;
		mp[x]++;
		a[i] = x;
		if(mp[x] == 1) se.insert(-x);
		if(mp[x] == 2) se.erase(-x);
		if(se.empty()) cout << "Nothing\n";
		else{
			cout << -(*se.begin()) << '\n';
		}
		x = a[i - k + 1];
		mp[x]--;
		if(mp[x] == 1) se.insert(-x);
		if(mp[x] == 0) se.erase(-x);
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

## [X - Legacy](https://vjudge.net.cn/problem/CodeForces-786B)

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

int ptop = 0;
const int MAXN = 1e5 + 10;
ll dis[MAXN * 8];
bool vis[MAXN * 8];

vector<pair<int,int>> ed[MAXN * 8];

struct pos{
	int l,r;
	int lson,rson;
	int i;
	ll dis;
	bool operator<(const pos& other)const{
		return dis > other.dis;
	}
}p[MAXN * 8];

priority_queue<pos> pqu;

int uproot,downroot;

void build_uptree(int l,int r,int x){
	dis[x] = INF;
	p[x].dis = INF;
	p[x].l = l;
	p[x].r = r;
	if(l == r){
		p[x].lson = p[x].rson = -1;
		return;
	}
	int mid = (l + r) / 2;
	p[x].lson = ++ptop;
	build_uptree(l,mid,p[x].lson);
	p[x].rson = ++ptop;
	build_uptree(mid + 1,r,p[x].rson);
	ed[p[x].lson].push_back(make_pair(x,0));
	ed[p[x].rson].push_back(make_pair(x,0));
}

void build_downtree(int l,int r,int x){
	dis[x] = INF;
	p[x].dis = INF;
	p[x].l = l;
	p[x].r = r;
	if(l == r){
		p[x].lson = p[x].rson = -1;
		return;
	}
	int mid = (l + r) / 2;
	p[x].lson = ++ptop;
	build_downtree(l,mid,p[x].lson);
	p[x].rson = ++ptop;
	build_downtree(mid + 1,r,p[x].rson);
	ed[x].push_back(make_pair(p[x].lson,0));
	ed[x].push_back(make_pair(p[x].rson,0));
}

int onep[MAXN];

void build_up_down_link(int up,int dp){
	if(p[up].l != p[up].r){
		build_up_down_link(p[up].lson,p[dp].lson);
		build_up_down_link(p[up].rson,p[dp].rson);
	}else{
		ed[dp].push_back(make_pair(up,0));
		onep[p[dp].l] = dp;
	}
}

void build_u_to_d_link(int u,int l,int r,int w,int x){
	if(p[x].l >= l && p[x].r <= r){
		ed[u].push_back(make_pair(x,w));
	}else{
		int mid = (p[x].l + p[x].r) / 2;
		if(l <= mid){
			build_u_to_d_link(u,l,r,w,p[x].lson);
		}
		if(r >= mid + 1){
			build_u_to_d_link(u,l,r,w,p[x].rson);
		}
	}
}

void build_d_to_u_link(int v,int l,int r,int w,int x){
	if(p[x].l >= l && p[x].r <= r){
		ed[x].push_back(make_pair(v,w));
	}else{
		int mid = (p[x].l + p[x].r) / 2;
		if(l <= mid){
			build_d_to_u_link(v,l,r,w,p[x].lson);
		}
		if(r >= mid + 1){
			build_d_to_u_link(v,l,r,w,p[x].rson);
		}
	}
}

void test(){
	int n,q,s;cin >> n >> q >> s;
	uproot = ++ptop;
	build_uptree(1,n,ptop);
	downroot = ++ptop;
	build_downtree(1,n,ptop);
	build_up_down_link(uproot,downroot);

	while(q--){
		int t;cin >> t;
		if(t == 1){
			int x,y,w;cin >> x >> y >> w;
			ed[onep[x]].push_back(make_pair(onep[y],w));
		}else if(t == 2){
			int u,l,r,w;cin >> u >> l >> r >> w;
			build_u_to_d_link(onep[u],l,r,w,downroot);
		}else{
			int u,l,r,w;cin >> u >> l >> r >> w;
			build_d_to_u_link(onep[u],l,r,w,uproot);
		}
	}

	s = onep[s];
	pos tem;
	tem.i = s;
	tem.dis = 0;
	pqu.push(tem);
	while(!pqu.empty()){
		tem = pqu.top();
		ll nodis = tem.dis;
		int x = tem.i;
		pqu.pop();
		if(vis[x]) continue;
		vis[x] = 1;
		dis[x] = tem.dis;
		for(auto [y,w] : ed[x]){
			if(vis[y]) continue;
			tem.i = y;
			tem.dis = nodis + w;
			if(dis[y] > tem.dis)
			dis[y] = tem.dis;
			pqu.push(tem);
		}
	}
	for(int i = 1;i <= n;i++){
		ll ddis = dis[onep[i]];
		if(ddis == INF) cout << -1 << ' ';
		else cout << ddis << ' ';
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