## [A - 剪花布条](https://vjudge.net.cn/problem/HDU-2087)

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

class KMP{
public:
	static vector<int> fnex(string &s){
		int n = s.size();
		vector<int> nex(n);
		int ptop = -1,i = 0;
		nex[0] = -1;
		while(i < n){
			if(ptop == -1 || s[i] == s[ptop]) nex[++i] = ++ptop;
			else ptop = nex[ptop];
		}
		return nex;
	}
	static int fcnt(string &s,string &ss,vector<int> &nex){
		int n = s.size(),m = ss.size();
		int pos = 0,cnt = 0;
		for(int i = 0;i < n;){
			if(pos == m) cnt++,pos = 0;
			else if(pos == -1 || s[i] == ss[pos]) i++,pos++;
			else pos = nex[pos];
		}
		if(pos == m) cnt++;
		return cnt;
	}
}kmp;

void test(){
	string a,b;
	while(1){
		cin >> a;
		if(a == "#") break;
		cin >> b;
		a = a;
		b = b;
		vector<int> nex = kmp.fnex(b);
		cout << kmp.fcnt(a,b,nex) << '\n';
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

## [B - Cyclic Nacklace](https://vjudge.net.cn/problem/HDU-3746)

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

string s;

class KMP{
public:
    static vector<int> fnex(){
        int n = s.size();
        vector<int> nex(n + 1);
        int ptop = -1,i = 0;
        nex[0] = -1;
        while(i < n){
            if(ptop == -1 || s[i] == s[ptop]) nex[++i] = ++ptop;
            else ptop = nex[ptop];
        }
        return nex;
    }
    static int fcnt(string &s,string &ss,vector<int> &nex){
        int n = s.size(),m = ss.size();
        int pos = 0,cnt = 0;
        for(int i = 0;i < n;){
            if(pos == m) cnt++,pos = nex[pos];
            else if(pos == -1 || s[i] == ss[pos]) i++,pos++;
            else pos = nex[pos];
        }
        if(pos == m) cnt++;
        return cnt;
    }
}kmp;

void test(){
    cin >> s;
    vector<int> nex = kmp.fnex();
    int n = s.size();
    int ans = n - nex[n];
    if(n % ans == 0 && ans != n) ans = 0;
    else ans = ans - n % ans;
    cout << ans << endl;
}

int main(){
    IOS
    int t = 1;cin >> t;
    while(t--){
        test();
    }
}
```

## [C - manacher 算法](https://vjudge.net.cn/problem/洛谷-P3805)

```cpp
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1.1e7 + 10;
char c[MAXN*2];
int len[MAXN*2],mid = 1,ma = 0;
int main()
{
	int ptop = 0;
	c[0] = '*';
	char cc;
	while(scanf("%c",&cc) != EOF)
	{
		if(cc == '0')
		break;
		c[++ptop] = '#';
		c[++ptop] = cc;
	}
	c[++ptop] = '#';
	c[++ptop] = '?';
	for(int i = 1;i <= ptop - 1;i++)
	{
		if(mid + len[mid] > i) len[i] = min(len[mid * 2 - i],mid + len[mid] - i);
		while(c[len[i] + i + 1] == c[i - len[i] - 1]) len[i]++;
		if(i + len[i] > mid + len[mid]) mid = i;
		ma = max(len[i],ma);
	}
	cout << ma;
}
```

## [D - 字符串哈希](https://vjudge.net.cn/problem/洛谷-P3370)

```cpp
#include<bits/stdc++.h>
using namespace std;

const int mod = 1e9 + 7;
using ll = long long;
ll qpow(ll x,ll y){
	x %= mod;
	ll ans = 1;
	while(y){
		if(y & 1) ans = ans * x % mod;
		x = x * x % mod;
		y >>= 1;
	}
	return ans;
}
class _ll{//自动取模类型
public:
	long long x;
	_ll():x(0){;}

	_ll(const int &other){x = other;}
	_ll(const ll &other):x(other){x = other;}
	_ll(const _ll &other){x = other.x;}

	inline void _mod(){x %= mod;}
	inline void add_mod(){x = (x % mod + mod) % mod;}

	_ll operator=(const int &other){x = other;return *this;}
	_ll operator=(const ll &other){x = other;return *this;}
	_ll operator=(const _ll &other){x = other.x;return *this;}

	_ll operator+(const int &other){return (_ll)((x + other) % mod);}
	_ll operator+(const ll &other){return (_ll)((x + other) % mod);}
	_ll operator+(const _ll &other){return (_ll)((x + other.x) % mod);}

	_ll operator-(const int &other){return (_ll)(((x - other) % mod + mod) % mod);}
	_ll operator-(const ll &other){return (_ll)(((x - other) % mod + mod) % mod);}
	_ll operator-(const _ll &other){return (_ll)(((x - other.x) % mod + mod) % mod);}

	_ll operator*(const int &other){return (_ll)((x * other) % mod);}
	_ll operator*(const ll &other){return (_ll)((x * other) % mod);}
	_ll operator*(const _ll &other){return (_ll)((x * other.x) % mod);}

	_ll operator/(const int &other){return (_ll)(x * qpow(other,mod - 2) % mod);}
	_ll operator/(const ll &other){return (_ll)(x * qpow(other,mod - 2) % mod);}
	_ll operator/(const _ll &other){return (_ll)(x * qpow(other.x,mod - 2) % mod);}
	
	    
    bool operator <(const _ll &other)const{return x < other.x;}
};

const int Base = 127,N = 1600;
class string_hash{
public:
	static _ll pow_base[N];
	static void init(int n){
		pow_base[0] = 1;
		for(int i = 1;i <= n;i++)
			pow_base[i] = pow_base[i] * Base;
	}
	vector<_ll> hash;
	string s;
	void get_hash(int n){
		hash.resize(n + 1);
		for(int i = 0;i < n;i++){
			hash[i + 1] = hash[i] * Base + (int)s[i];
		}
	}
	_ll get_hash_range(int l,int r){
		return hash[r + 1] - hash[l] * pow_base[r - l + 1];
	}
}ss[10001];
_ll string_hash::pow_base[N] = {0};//静态成员变量要初始化，否则无法使用

int main(){
	int n;cin >> n;
	string_hash::init(1501);
	set<_ll> se;
	for(int i = 1;i <= n;i++){
		cin >> ss[i].s;
		int len = ss[i].s.size();
		ss[i].get_hash(len);
		se.insert(ss[i].get_hash_range(0,len - 1));
	}
	cout << se.size() << '\n';
}
```

## [E - 单词背诵](https://vjudge.net.cn/problem/洛谷-P1381)

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
	int n;cin >> n;
	map<string,int> mp;
	int ptop = 0;
	for(int i = 1;i <= n;i++){
		string s;cin >> s;
		if(!mp.count(s)) mp[s] = ++ptop;
	}
	int num = 0;
	map<int,int> ct;
	ct[0] = 2;
	int m;cin >> m;
	queue<int> qu;
	int ans = 0;
	for(int i = 1;i <= m;i++){
		string s;cin >> s;
		ct[mp[s]]++;
		qu.push(mp[s]);
		if(ct[mp[s]] == 1){
			num++;
			ans = (int)qu.size() + 1;
		}
		while(!qu.empty() && (ct[qu.front()] != 1 || (qu.front() == 0))){
			ct[qu.front()]--;
			qu.pop();
		}

		ans = min(ans,(int)qu.size());
	}
	cout << num << '\n';
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

## [F - 背单词](https://vjudge.net.cn/problem/洛谷-P3294)

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
const int M = 6e5 + 10;

vector<vector<int>> ed;
vector<int> fa;
vector<int> sz;
vector<int> ans;
vector<int> pos;


class TIRE{
public:
	vector<vector<int>> tr;
	vector<int> e;
	int ptop = 0;
	void init(int n){
		tr = vector<vector<int>>(M + 1,vector<int>(26));
		e = vector<int>(M + 1);
		fa = vector<int>(n + 1);
		ed = vector<vector<int>>(n + 1);
		sz = vector<int>(n + 1);
		pos = vector<int>(n + 1);
//		ans = vector<int>(n + 1);
	}
	void add(int i,string s){
		int begin = 0;
		for(auto c : s){
			if(!tr[begin][c - 'a']) tr[begin][c - 'a'] = ++ptop;
			begin = tr[begin][c - 'a'];
			if(e[begin]) fa[i] = e[begin];
		}
		e[begin] = i;
		ed[fa[i]].push_back(i);
		ed[i].push_back(fa[i]);
	}
}trie;

void dfs(int x){
	sz[x] = 1;
	for(auto y : ed[x]){
		if(y == fa[x]) continue;
		dfs(y);
		sz[x] += sz[y];
	}
}
void dfs2(int x){
	ans.push_back(x);
	vector<pair<int,int>> ve;
	for(auto y : ed[x]){
		if(y == fa[x]) continue;
		ve.push_back(make_pair(sz[y],y));
	}
	sort(ve.begin(),ve.end());
	for(auto [x,y] : ve){
		dfs2(y);
	}
}

void test(){
	int n;cin >> n;
	trie.init(n);
//	ans.push_back(0);
	vector<tuple<int,int,string>> ve(n + 1);
	for(int i = 1;i <= n;i++){
		auto &[len,j,s] = ve[i];
		cin >> s;
		reverse(s.begin(),s.end());
		j = i;
		len = s.size();
	}
	sort(ve.begin(),ve.end());
	for(int i = 1;i <= n;i++){
		auto [len,j,s] = ve[i];
		trie.add(j,s);
	}
	dfs(0);
	dfs2(0);
	for(int i = 1;i <= n;i++){
		pos[ans[i]] = i;
	}
	ll sum = 0;
	for(int i = 1;i <= n;i++){
		sum += pos[i] - pos[fa[i]];
	}
	cout << sum << '\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}	
```

## [G - KMP字符串匹配](https://vjudge.net.cn/problem/洛谷-P3375)

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 1e6 + 10;
int nex[N];

void fnex(string s){
	nex[0] = -1;
	int n = s.size(),ptop = -1,i = 0;
	while(i < n){
		if(ptop == -1 || s[i] == s[ptop])
			nex[++i] = ++ptop;
		else
			ptop = nex[ptop];
	}
}

int main(){
	string s1,s2;cin >> s1 >> s2;
	fnex(s2);
	int n = s1.size(),m = s2.size();
	int i = 0,pos = 0;
	while(i < n){
		if(pos == m){
			cout << i - m + 1 << endl;
			pos = nex[pos];
		}else if(pos == -1 || s2[pos] == s1[i])
			pos++,i++;
		else
			pos = nex[pos];
	}
	if(pos == m)
	    cout << i - m + 1 << endl;
	for(int i = 1;i <= m;i++)
		cout << nex[i] << ' ';
}
```

## [H - Radio Transmission 无线传输](https://vjudge.net.cn/problem/洛谷-P4391)

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

class KMP{
public:
    static vector<int> fnex(string &s){
        int n = s.size();
        vector<int> nex(n + 1);
        int ptop = -1,i = 0;
        nex[0] = -1;
        while(i < n){
            if(ptop == -1 || s[i] == s[ptop]) nex[++i] = ++ptop;
            else ptop = nex[ptop];
        }
        return nex;
    }
    static int fcnt(string &s,string &ss,vector<int> &nex){
        int n = s.size(),m = ss.size();
        int pos = 0,cnt = 0;
        for(int i = 0;i < n;){
            if(pos == m) cnt++,pos = nex[pos];
            else if(pos == -1 || s[i] == ss[pos]) i++,pos++;
            else pos = nex[pos];
        }
        if(pos == m) cnt++;
        return cnt;
    }
}kmp;

void test(){
	int n;cin >> n;
    string s;cin >> s;
    vector<int> nex = kmp.fnex(s);
    int ans = n - nex[n];
    cout << ans << endl;
}

int main(){
    IOS
    int t = 1;//cin >> t;
    while(t--){
        test();
    }
}
```

## [I - 动物园](https://vjudge.net.cn/problem/洛谷-P2375)

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
const int mod = 1e9 + 7;

vector<int> ans;
vector<int> rans;

class KMP{
public:
    static vector<int> fnex(string &s){
        int n = s.size();
        vector<int> nex(n + 1);
        int ptop = -1,i = 0;
        nex[0] = -1;
        while(i < n){
            if((ptop == -1 || s[i] == s[ptop]) && ((ptop + 1) * 2 - 1 <= i)) nex[++i] = ++ptop;
            else ptop = nex[ptop];
        } 
   	   	return nex;
    }
    static vector<int> rnex(string &s){
        int n = s.size();
        vector<int> nex(n + 1);
        int ptop = -1,i = 0;
        nex[0] = -1;
        while(i < n){
            if((ptop == -1 || s[i] == s[ptop])){
            	nex[++i] = ++ptop;
            	rans[i] = rans[ptop] + 1;
            }
            else ptop = nex[ptop];
        }
        return nex;
    }
    static int fcnt(string &s,string &ss,vector<int> &nex){
        int n = s.size(),m = ss.size();
        int pos = 0,cnt = 0;
        for(int i = 0;i < n;){
            if(pos == m) cnt++,pos = nex[pos];
            else if(pos == -1 || s[i] == ss[pos]) i++,pos++;
            else pos = nex[pos];
        }
        if(pos == m) cnt++;
        return cnt;
    }
}kmp;

void test(){
	// int n;cin >> n;
    string s;cin >> s;
    int n = s.size();
    ans = vector<int>(n + 1);
    rans = vector<int>(n + 1);
    kmp.rnex(s);
    vector<int> nex = kmp.fnex(s);
    ll sum = 1;
    for(int i = 1;i <= n;i++){
    	sum = (sum * (rans[nex[i]] + 1)) % mod;
    }	
    cout << sum << '\n';
}

int main(){
    IOS
    int t = 1;cin >> t;
    while(t--){
        test();
    }
}
```

## [J - AC 自动机（简单版）](https://vjudge.net.cn/problem/洛谷-P3808)

## [K - AC 自动机（二次加强版）](https://vjudge.net.cn/problem/洛谷-P5357)

* 直接搓个板子

```cpp
class KMP_TRIE{
public:
	int ptop = 0;
	int n = 0;
	vector<vector<int>> trie;
	vector<int> cnt,nex,dfs_order,ccnt;
	vector<vector<int>> id;

	void init(){
		add_one();
		ptop = 0;
	}

	void add_one(){
		trie.push_back(vector<int>(26));
		nex.push_back(0);
		cnt.push_back(0);
		id.push_back(vector<int>(0));
	}

	void add(string s){
		n++;
		int end = s.size();
		int x = 0;
		for(int i = 0;i < end;i++){
			int t = s[i] - 'a';
			if(!trie[x][t]){
				trie[x][t] = ++ptop;
				add_one();
			}
			x = trie[x][t];
		}
		cnt[x]++;
		id[x].push_back(n);
	}

	void kmp(){
		queue<int> qu;
		dfs_order.push_back(0);
		for(int i = 0;i < 26;i++){
			if(trie[0][i]) qu.push(trie[0][i]);
		}
		while(!qu.empty()){
			int x = qu.front();
			dfs_order.push_back(x);
			qu.pop();
			for(int i = 0;i < 26;i++){
				if(trie[x][i]){
					nex[trie[x][i]] = trie[nex[x]][i];
					qu.push(trie[x][i]);
				}else{
					trie[x][i] = trie[nex[x]][i];
				}
			}
		}
		reverse(dfs_order.begin(),dfs_order.end());
		ccnt = vector<int>(ptop + 1);
		for(auto x : dfs_order){
			ccnt[x] = ccnt[nex[x]] + cnt[x];
		}
	}


	int find_one_count_type(string s){//统计s中包含模式串的数量
		int n = s.size();
		vector<int> vis = vector<int>(ptop + 1);
		int no = 0;
		int ans = 0;
		for(int i = 0;i < n;i++){
			no = trie[no][s[i] - 'a'];
			vis[no] = 1;
		}
		for(int i = 0;i <= ptop;i++){
			if(vis[dfs_order[i]]){
				vis[nex[dfs_order[i]]] = 1;
				ans += cnt[dfs_order[i]];
			}
		}
		return ans;
	}


	vector<int> num;
	void find_one_count_num(string s){//统计一个字符串内出现的所有模式字符串的数量
		int n = s.size();
		if(num.size() == 0) num = vector<int>(ptop + 1);
		int x = 0;
		for(int i = 0;i < n;i++){
			x = trie[x][s[i] - 'a'];
			num[x]++;
		}
	}

	vector<int> count_num(){
		vector<int> ans(n + 1);
		for(int i = 0;i <= ptop;i++){
			for(auto _i : id[dfs_order[i]]){
				ans[_i] = num[dfs_order[i]];
			}
			num[nex[dfs_order[i]]] += num[dfs_order[i]];
		}
		return ans;
	}

}kmp_trie;
```

## [L - 阿狸的打字机](https://vjudge.net.cn/problem/洛谷-P2414)

*  $AC$ 自动机 $nex$ 数组建树，记录 $dfs$ 序
* 然后按深度搜索
* 按 $dfs$ 序加入树状数组，求子树内的元素数量

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

const int M = 1e6 + 10;

class KMP_TRIE{
public:
	int ptop = 0;
	int n = 0;
	vector<vector<int>> trie;
	vector<vector<int>> retrie;
	vector<int> cnt,nex,fa;
	vector<vector<int>> id;

	void init(){
		add_one();
		ptop = 0;
	}

	void add_one(){
		trie.push_back(vector<int>(26));
		nex.push_back(0);
		cnt.push_back(0);
		id.push_back(vector<int>(0));
		fa.push_back(0);
	}

	int add(string s){
		n++;
		int end = s.size();
		int x = 0;
		for(int i = 0;i < end;i++){
			int t = s[i] - 'a';
			if(!trie[x][t]){
				trie[x][t] = ++ptop;
				add_one();
				fa[ptop] = x;
			}
			x = trie[x][t];
		}
		cnt[x]++;
		id[x].push_back(n);
		return x;//返回endpos
	}

	int move_next(int pos,char c){
		int t = c - 'a';
		if(!trie[pos][t]){
			trie[pos][t] = ++ptop;
			add_one();
			fa[ptop] = pos;
		}
		pos = trie[pos][t];
		return pos;
	}

	void kmp(){
		retrie = trie;
		queue<int> qu;
		for(int i = 0;i < 26;i++){
			if(trie[0][i]) qu.push(trie[0][i]);
		}
		while(!qu.empty()){
			int x = qu.front();
			qu.pop();
			for(int i = 0;i < 26;i++){
				if(trie[x][i]){
					nex[trie[x][i]] = trie[nex[x]][i];
					qu.push(trie[x][i]);
				}else{
					trie[x][i] = trie[nex[x]][i];
				}
			}
		}
	}

}kmp_trie;

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

BIT<int> bit;
vector<int> ans;

vector<vector<int>> son;
vector<int> dfs,dfs_order,rdfs;

int ptop = -1;
void get_dfs(int x){
	dfs[x] = ++ptop;
	dfs_order[ptop] = x;
	for(auto y : son[x]){
		get_dfs(y);
	}
	rdfs[x] = ptop;
}

vector<vector<pair<int,int>>> ask;

bool cmp(tuple<int,int,int> a,tuple<int,int,int> b){
	return dfs[get<0>(a)] < dfs[get<0>(b)];
}

int sum = 0;
int ptop2 = 0;
void dfs2(int x){
	if(dfs[x] != 0)
		bit.add(dfs[x],1);

	for(auto [y,i] : ask[x]){
		ans[i] = bit.query(dfs[y],rdfs[y]);
		ptop2++;
	}

	for(int i = 0;i < 26;i++){
		if(kmp_trie.retrie[x][i])
			dfs2(kmp_trie.retrie[x][i]);
	}
	// for(auto y : son[x]){
	// 	dfs2(y);
	// }
	if(dfs[x] != 0)
		bit.add(dfs[x],-1);
}

void test(){
	kmp_trie.init();
	string s;cin >> s;
	int nopos = 0;
	vector<int> endpos(1,0);
	for(auto c : s){
		if(c == 'P'){
			endpos.push_back(nopos);
		}else if(c == 'B'){
			nopos = kmp_trie.fa[nopos];
		}else{
			nopos = kmp_trie.move_next(nopos,c);
		}
	}

	kmp_trie.kmp();

	//用nex建树
	son = vector<vector<int>>(kmp_trie.ptop + 1);
	for(int i = 1;i <= kmp_trie.ptop;i++){
		son[kmp_trie.nex[i]].push_back(i);
	}
	//得到dfs序
	dfs = vector<int>(kmp_trie.ptop + 1);
	rdfs = vector<int>(kmp_trie.ptop + 1);
	dfs_order = vector<int>(kmp_trie.ptop + 1);
	get_dfs(0);

	int m;cin >> m;
	sum = m;
	
	ans = vector<int>(m + 1);

	ask = vector<vector<pair<int,int>>>(kmp_trie.ptop + 1);

	for(int i = 1;i <= m;i++){
		int x,y;cin >> x >> y;
		x = endpos[x],y = endpos[y];
		ask[y].push_back(make_pair(x,i));
	}

	bit = BIT<int>(kmp_trie.ptop + 1);
	dfs2(0);

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

## [M - 单词](https://vjudge.net.cn/problem/洛谷-P3966)

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long

const int N = 200 + 10,M = 1e6 + 10;

string s[N],t;

int cnt[M],ccnt[M],ma,ans;
int trie[M][26];
int nex[M];
int ptop;
int aans[M];
int tem[M];
map<string,int> mp;
map<int,string> mpp;

void add_trie(string str)
{
	int end = str.size();
	int x = 0;
	for(int i = 0;i < end;i++)
	{
		int t = str[i] - 'a';
		if(!trie[x][t])
			trie[x][t] = ++ptop;
		x = trie[x][t];	
	}
	cnt[x] = 1;//这里是算结尾的数量，如果求前缀可以把这个放上面去
	mp[str] = x;
	// mpp[x] = str;
}

vector<int> ve;
bool vis[M];
void dfs(){
	int x = 0;
	queue<int> qu;
	qu.push(0);
	vis[0] = 1;
	while(!qu.empty()){
		x = qu.front();
		qu.pop();
		ve.push_back(x);
		for(int i = 0;i < 26;i++){
			if(!vis[trie[x][i]]){
				qu.push(trie[x][i]);
				vis[trie[x][i]] = 1;
			}
		}
	}
	reverse(ve.begin(),ve.end());
}

void kmp_trie()
{
	queue<int> qu;
	for(int i = 0;i < 26;i++)
	{
		if(trie[0][i])//存在
			qu.push(trie[0][i]);
	}
	while(!qu.empty())
	{
		int x = qu.front();
		qu.pop();
		for(int i = 0;i < 26;i++)
		{
			if(trie[x][i])//节点存在
			{
				nex[trie[x][i]] = trie[nex[x]][i];//核心1,这个节点指向父节点的指向的节点的同子节点
				qu.push(trie[x][i]);
			}
			else
				trie[x][i] = trie[nex[x]][i];//核心2,这个节点不存在就让它等于父节点的指向的子节点
		}
	}
}

void find(string str)
{
	int end = str.size();
	int x = 0;
	for(int i = 0;i < end;i++)
	{
		x = trie[x][str[i] - 'a'];
		int xx = x;
		tem[xx]++;
		// while(xx != 0)
		// {
		// 	if(cnt[xx]){
		// 		aans[xx] += cnt[xx];
		// 		// mp[mpp[xx]] += cnt[xx];
		// 	}
		// 	xx = nex[xx];
		// }
	}
}

void init(){
	memset(cnt,0,sizeof(cnt));
	memset(trie,0,sizeof(trie));
	memset(nex,0,sizeof(nex));
	mp.clear();
	mpp.clear();
	ptop = 0;
	ma = 0;
	ans = 0;
}

void test(){
	int n;cin >> n;
		for(int i = 1;i <= n;i++){
			cin >> s[i];
			add_trie(s[i]);
		}
		kmp_trie();
		for(int i = 1;i <= n;i++)
			find(s[i]);
		dfs();
		for(auto x : ve){
			aans[x] += tem[x] * cnt[x];
			tem[nex[x]] += tem[x];
		}
		for(int i = 1;i <= n;i++)
			cout << aans[mp[s[i]]] << '\n';
}

signed main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [N - L 语言](https://vjudge.net.cn/problem/洛谷-P2292)

* 我们假设 $i$ 位置可以作为结尾，那么 $i+j$ 可以作为结尾为后面 $j$ 长的字符串为出现的字符串
* 我们在 $ac$ 自动机上给长度为 $j$ 的字符串结尾标记上 $2^j$
* 然后对于所有 $ac$ 自动机的 $nex$ 路径，我们都要取一个 $|$ 来代表这个位置结尾，有可能的 $j$ 的长度
* 这样我们就得出了所有位置可向前转移的长度信息
* 然后对于一个给出的字符串 $t$ ，我们可以得到其在 $ac$ 自动机的路径与该路径的标记序列 $tt$
* 我们想一下可以如何转移
* 如果 $i$ 位置 $dp_i\&1并且tt_{i+j}>>j\&1$ ，那么其可以继承 $2^j$
* 但是如果每个位置都这样转移，复杂度为 $O(20m|t|+n|s|)$

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

vector<int> t;//如果该位置为结尾，那么结尾的长度应该为多少才符合条件
class KMP_TRIE{
public:
	int ptop = 0;
	int n = 0;
	vector<vector<int>> trie;
	vector<int> cnt,nex,dfs_order,fa;
	vector<vector<int>> id;

	void init(){
		add_one();
		ptop = 0;
	}

	void add_one(){
		trie.push_back(vector<int>(26));
		nex.push_back(0);
		id.push_back(vector<int>(0));
		t.push_back(0);
		fa.push_back(0);
	}

	void add(string s){
		n++;
		int end = s.size();
		int x = 0;
		for(int i = 0;i < end;i++){
			int t = s[i] - 'a';
			if(!trie[x][t]){
				add_one();
				trie[x][t] = ++ptop;
				fa[ptop] = x;
			}
			x = trie[x][t];
		}
		t[x] |= 1 << s.size();
	}

	void kmp(){
		queue<int> qu;
		dfs_order.push_back(0);
		for(int i = 0;i < 26;i++){
			if(trie[0][i]) qu.push(trie[0][i]);
		}
		while(!qu.empty()){
			int x = qu.front();
			dfs_order.push_back(x);
			qu.pop();
			for(int i = 0;i < 26;i++){
				if(trie[x][i]){
					nex[trie[x][i]] = trie[nex[x]][i];
					qu.push(trie[x][i]);
				}else{
					trie[x][i] = trie[nex[x]][i];
				}
			}
		}
		for(auto x : dfs_order){
			t[x] |= t[nex[x]];
		}
	}

}kmp_trie;
void test(){
	int n,m;cin >> n >> m;
	kmp_trie.init();
	for(int i = 1;i <= n;i++){
		string s;cin >> s;
		kmp_trie.add(s);
	}
	kmp_trie.kmp();
	for(int i = 1;i <= m;i++){
		string s;cin >> s;
		int len = s.size();
		s = ' ' + s;
		vector<int> tt(len + 1);
		tt[0] = 0;
		int no = 0;
		for(int j = 1;j <= len;j++){
			no = kmp_trie.trie[no][s[j] - 'a'];
			tt[j] = t[no];
		}
		vector<int> ans(len + 1);//一个位置,其前面长度为j可转移
		ans[0] = 1;
		for(int j = 0;j <= len;j++){
			if(j != 0)
				ans[j] = (ans[j - 1] >> 1);
			if(ans[j] & 1){
				for(int k = 1;k <= 20 && k + j <= len;k++){
					if((tt[k + j] >> k) & 1)
						ans[j] |= 1 << k;
				}
			}
		}
		for(int i = len;i >= 0;i--){
			if(ans[i] & 1){
				cout << i << '\n';
				break;
			}
		}
	}
}

int main(){
	IOS
	int t = 1;//	cin >> t;
	while(t--){
		test();
	}
}
```

* 如何优化？
* 我们得到 $ttt_i=|\sum tt_{i+j}>>j\&1?2^j:0$
* 如果我们反过来想，从后往前跑， $dp_i\&1$ 为结束的起点，那么 $dp_i\&1\to dp_i|=tt_i$ 就很正常
* 但是这样我们得到的就是从往前的长位置了
* 因此我们要将所有给出的字符串翻转即可
* 然后从前往后遍历也可以翻转 $tt$ 数组

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

vector<int> t;//如果该位置为结尾，那么结尾的长度应该为多少才符合条件
class KMP_TRIE{
public:
	int ptop = 0;
	int n = 0;
	vector<vector<int>> trie;
	vector<int> cnt,nex,dfs_order,fa;
	vector<vector<int>> id;

	void init(){
		add_one();
		ptop = 0;
	}

	void add_one(){
		trie.push_back(vector<int>(26));
		nex.push_back(0);
		id.push_back(vector<int>(0));
		t.push_back(0);
		fa.push_back(0);
	}

	void add(string s){
		n++;
		int end = s.size();
		int x = 0;
		for(int i = 0;i < end;i++){
			int t = s[i] - 'a';
			if(!trie[x][t]){
				add_one();
				trie[x][t] = ++ptop;
				fa[ptop] = x;
			}
			x = trie[x][t];
		}
		t[x] |= 1 << s.size();
	}

	void kmp(){
		queue<int> qu;
		dfs_order.push_back(0);
		for(int i = 0;i < 26;i++){
			if(trie[0][i]) qu.push(trie[0][i]);
		}
		while(!qu.empty()){
			int x = qu.front();
			dfs_order.push_back(x);
			qu.pop();
			for(int i = 0;i < 26;i++){
				if(trie[x][i]){
					nex[trie[x][i]] = trie[nex[x]][i];
					qu.push(trie[x][i]);
				}else{
					trie[x][i] = trie[nex[x]][i];
				}
			}
		}
		//reverse(dfs_order.begin(),dfs_order.end());
		for(auto x : dfs_order){
			t[x] |= t[nex[x]];
		}
	}

}kmp_trie;
void test(){
	int n,m;cin >> n >> m;
	kmp_trie.init();
	for(int i = 1;i <= n;i++){
		string s;cin >> s;
		reverse(s.begin(),s.end());
		kmp_trie.add(s);
	}
	kmp_trie.kmp();
	// int m;cin >> m;
	for(int i = 1;i <= m;i++){
		string s;cin >> s;
		reverse(s.begin(),s.end());
		int len = s.size();
		s = ' ' + s;
		vector<int> tt(len + 1);
		tt[0] = 0;
		int no = 0;
		for(int j = 1;j <= len;j++){
			no = kmp_trie.trie[no][s[j] - 'a'];
			tt[j] = t[no] >> 1;
		}
		vector<int> ans(len + 1);//一个位置,其前面长度为j可转移
		ans[0] = 1;
		reverse(tt.begin(),tt.end());
		for(int j = 1;j <= len;j++){//后面位置可作为结尾
			ans[j] = (ans[j - 1] >> 1) | (ans[j - 1] & 1 ? tt[j - 1] : 0);
		}
		for(int i = len;i >= 0;i--){
			if(ans[i] & 1){
				cout << i << '\n';
				break;
			}
		}
	}
}

int main(){
	IOS
	int t = 1;//	cin >> t;
	while(t--){
		test();
	}
}
```

## [O - Xor sum](https://vjudge.net.cn/problem/HDU-6955)

* 纯纯sb

```cpp
// xxc
// 有病吧，这题，输入就算用三重优化也要800ms,用scanf也是，必须用cin.exceptions(ios::badbit | ios::failbit)
// 解题的复杂度贡献时间又有 700ms左右，你做什么sb
// 还限时1000ms，你读的了吗
// 是不是把所有test总和时间设置为1000ms了？
// 逆天
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);cin.exceptions(ios::badbit | ios::failbit);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

int ptop = 0;

const int N = 3e6 + 10;
const int MAX_lg = 30;
int trie[N][MAX_lg];
int ma[N];
int a[N];

inline void add(int &x,int &i){
	// return;
	int no = 0;
	for(int k = 30;k >= 0;k--){
		int t = (x >> k) & 1;
		if(!trie[no][t]){
			trie[no][t] = ++ptop;
			trie[ptop][0] = trie[ptop][1] = 0;
		}
		no = trie[no][t];
		ma[no] = i;
	}
}

inline int find_ma(const int &x,const int &k,int range){
	// return 0;
	int no = 0;
	int ans = -inf;
	for(int i = 30;i >= 0;i--){
		int t1 = (x >> i) & 1,t2 = (k >> i) & 1;

		if(t1 && !t2 && trie[no][0]) ans = max(ans,ma[trie[no][0]]);
		if(!t1 && !t2 && trie[no][1]) ans = max(ans,ma[trie[no][1]]);
		range = max(ans,range);
		
		no = trie[no][t1 != t2];
		if(no == 0 || ma[no] < range) break;
	}
	return max(ans,ma[no]);
}

inline void init(){
	ptop = 0;
}

void test(){
	int n,k;cin >> n >> k;
	// scanf("%d %d",&n,&k);
	ptop = 0;
	int tem = 0;
	init();
	add(tem,tem);
	int minlen = inf,l = 0;
	for(int i = 1;i <= n;i++){
		// scanf("%d",&a[i]);
		cin >> a[i];
		a[i] ^= a[i - 1];
		
		ma[0] = -inf;
		int _l = find_ma(a[i],k,i - minlen);
		if(minlen > i - _l){
			minlen = i - _l;
			l = _l;
		}
		
		add(a[i],i);
	}
	if(minlen == inf) printf("-1\n");
	else printf("%d %d\n",l + 1,l + minlen);
}

int main(){
	ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);cin.exceptions(ios::badbit | ios::failbit);
	// IOS
	int t = 1;cin >> t;
	// scanf("%d",&t);
	while(t--){
		test();
	}
}
```

## [P - Xor Sum](https://vjudge.net.cn/problem/HDU-4825)

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

const int N = 3e7 + 10,MAX_lg = 31;

int trie[N][2];
int ptop = 0;

void add(int x){
	int no = 0;
	for(int i = MAX_lg;i >= 0;i--){
		int t = (x >> i) & 1;
		if(!trie[no][t]){
			trie[no][t] = ++ptop;
			trie[ptop][0] = trie[ptop][1] = 0;
		}
		no = trie[no][t];
	}
}

int find(int x){
	int no = 0,ans = 0;
	for(int i = MAX_lg;i >= 0;i--){
		int t = (x >> i) & 1;
		t ^= 1;
		if(trie[no][t]){
			ans += 1 << i;
			no = trie[no][t];
		}else{
			no = trie[no][t^1];
		}
	}
	return ans;
}

void test(){
	int t;cin >> t;
	for(int i = 1;i <= t;i++){
		cout << "Case#" << i << ":\n";
		trie[0][0] = trie[0][1] = 0;
		ptop = 0;
		int n,m;cin >> n >> m;
		for(int i = 1;i <= n;i++){
			int x;cin >> x;
			add(x);
		}
		for(int i = 1;i <= m;i++){
			int x;cin >> x;
			cout << (find(x) ^ x) << '\n';
		}
	}
}

int main(){
	ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);cin.exceptions(ios::badbit | ios::failbit);
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [Q - Random Student ID](https://vjudge.net.cn/problem/AtCoder-abc268_g)

* 我们只考虑两个字符串之间的贡献，其只有两种可能性，在前面与在后面
* 在前面贡献 $1$ ，在后面贡献 $0$ ，如果可以在前在后贡献 $\frac{1}{2}$
* 根据前缀关系可得出一定在前面的与一定在后面的字符串数量

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

const int mod = 998244353;
const int N = 5e5 + 10;

int trie[N][26];
int pre[N],nex[N];
int cnt[N],ed[N];
int ptop = 0;

void add(string &s){
	int no = 0;
	int n = s.size();
	for(int i = 0;i < n;i++){
		if(!trie[no][s[i] - 'a']){
			trie[no][s[i] - 'a'] = ++ptop;
		}
		no = trie[no][s[i] - 'a'];
		cnt[no]++;
	}
	ed[no]++;
}

void get(int id,string &s){
	int no = 0;
	int n = s.size();
	for(int i = 0;i < n;i++){
		pre[id] += ed[no];//比它短
		no = trie[no][s[i] - 'a'];
		cnt[no]++;
	}
	nex[id] = cnt[no] - 1;//比它长
}

ll qpow(int x,int y){
	ll res = 1;
	while(y){
		if(y & 1) res = (x * res) % mod;
		y >>= 1;
		x = (x*x) % mod;
	}
	return res;
}

void test(){
	int n;cin >> n;
	vector<string> ve(n + 1);
	for(int i = 1;i <= n;i++){
		cin >> ve[i];
		add(ve[i]);
	}
	for(int i = 1;i <= n;i++){
		get(i,ve[i]);
		cout << (pre[i] + 1 + (1 + pre[i] + n - nex[i]) * qpow(2,mod - 2) % mod) % mod << '\n';
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

## [R - Period](https://vjudge.net.cn/problem/OpenJ_Bailian-1961)

* 一个位置要分解为最多相同的子字符串
* 那么我们看它的跳转，如果其跳转将后面的一段字符串丢掉，而丢掉的长度与前面能分解的字符串长度一样，说明前面与后面都可以由这个字符串分解而来的
* 我们再考虑为什么直接这样就可以得到最多的分解方法
* 首先，如果跳转的是与分解方法的，那么它一定会跳转到最多的，因为可以 $nex$ 就是最近的
* 其次，如果跳转的不是分解方法，但是存在分解方法，我们会发现其会相交，从而得到更小的分解方法，因此分解方法一定会是跳转位置

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

string s;

class KMP{
public:
    static vector<int> fnex(){
        int n = s.size();
        vector<int> nex(n + 1);
        int ptop = -1,i = 0;
        nex[0] = -1;
        while(i < n){
            if(ptop == -1 || s[i] == s[ptop]) nex[++i] = ++ptop;
            else ptop = nex[ptop];
        }
        return nex;
    }
    static int fcnt(string &s,string &ss,vector<int> &nex){
        int n = s.size(),m = ss.size();
        int pos = 0,cnt = 0;
        for(int i = 0;i < n;){
            if(pos == m) cnt++,pos = nex[pos];
            else if(pos == -1 || s[i] == ss[pos]) i++,pos++;
            else pos = nex[pos];
        }
        if(pos == m) cnt++;
        return cnt;
    }
}kmp;

void test(){
	int n;
	int ptop = 0;
	while(1){
		ptop++;
		// cout << "Test case #" << ptop << '\n';

		int n;cin >> n;
		if(n == 0) break;
		cout << "Test case #" << ptop << '\n';
		
		// if(n == 0) break;
	    cin >> s;
	    vector<int> nex = kmp.fnex();

	    vector<int> ans(n + 1);

	    for(int i = 1;i <= n;i++){
	    	ans[i] = ans[nex[i]] + 1;
	    	if(i != (i - nex[i]) * ans[i]) ans[i] = 1;
	    	if(ans[i] > 1) cout << i << ' ' << ans[i] << '\n';
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

