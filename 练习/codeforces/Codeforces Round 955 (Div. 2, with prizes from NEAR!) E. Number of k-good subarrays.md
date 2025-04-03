## E. Number of k-good subarrays

> 我们称一个数 $x$ 的二进制位为 $1$ 的数量为 $bit(x)$
>
> 称一个数 $bit(x)\leq k$ 为 $k-good$
>
> 对于一个 $[0,n-1]$ 数组来说，内部有多少个全部由 $k-good$ 数组成的子序列

##### 思路：

* 二进制位逐步分解 $dp$
* 令 $dp[i][k]$ 为 $[1,2^i]$ 在 $k-good$ 的限制下的 ${ans,l,r,f}$ 
* 其中 $ans$ 代表非边缘满足条件区间的贡献
*  $l$ 代表左边多余区间大小
*  $r$ 代表右边多余区间大小
*  $f$ 代表左右区间是否为同一个区间
* 那么我们可以重载加法，使得这个加法为两个区间合并
* 为得到 $dp[i][k]$ 我们可以先得出 $2^i-1$ 然后加 $1$ 
* 也就是 $dp[i][k]=\sum_{1\leq j\leq i,k}{dp[i-j][k-j]}$

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

ll qpow(ll x,ll y){
	ll res = 1;
	while(y){
		if(y & 1) res = (res * x) % mod;
		y >>= 1;
		x = (x * x) % mod;
	}
	return res;
}

ll _2;

ll ct(ll x){
	return (((((x % mod) * ((x - 1) % mod)) % mod) * _2 % mod) + x) % mod;
}

struct pos{
	ll ans,l,r;
	bool f;

	ll ask_ans(){
		ans += (ct(l) + ct(r)) % mod;
		ans %= mod;
		return ans;
	}

	friend pos operator +(const pos &re,const pos &other);
};

pos operator +(const pos &re,const pos &other){
	pos res = {0,0,0,0};
	if(re.f && other.f){
		res.l = res.r = re.l + other.r;
		res.f = 1;
	}else{
		res.l = re.l;
		res.r = other.r;
		if(re.f){
			res.l += other.l;
		}else if(other.f){
			res.r += re.r;
		}else{
			res.ans += ct(re.r + other.l);
		}
	}
	return res;
}

bool vis[61][61];
pos dp[61][61];//dp[i][k] 2^i count_bit(x) <= k {ans,l,r,f}
//先不计入0

pos Ct(int i,int k){
	if(k <= 0){
		return {0,0,0,0};
	}
	if(vis[i][k]) return dp[i][k];
	if(k > i){
		return {0,(ll)1 << i,(ll)1 << i,1};//完整区间
	}else{
		pos res = {0,0,0,0};
		//先配成111111111
		for(int j = i - 1;j >= 0;j--){
			res = res + Ct(j,k - (i - 1 - j));
		}
		//然后+1
		res = res + (pos){0,1,1,1};
		vis[i][k] = 1;
		return res;
	}
}
void init(){
	_2 = qpow(2,mod - 2);
	for(int k = 1;k <= 60;k++){
		for(int i = 0;i <= 60;i++){
			dp[i][k] = Ct(i,k);
			vis[i][k] = 1;
		}
	}
}

void test(){
	ll n;
	int k;
	cin >> n >> k;

	pos cur = {0,1,1,1};

	for(int i = 60;i >= 0;i--){
		if((n >> i) & 1){
			cur = cur + Ct(i,k);
			k--;
		}
	}
	cout << cur.ask_ans() << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

* 用自动取模类

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define debug(x) cout << #x << ' ' << x << endl;

using ll = long long;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;
const int mod = 1e9 + 7;

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

const int mod = 1e9 + 7;
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
	
	_ll operator+=(const int &other){return *this = (_ll)((x + other) % mod);}
	_ll operator+=(const ll &other){return *this = (_ll)((x + other) % mod);}
	_ll operator+=(const _ll &other){return *this = (_ll)((x + other.x) % mod);}

	_ll operator-(const int &other){return (_ll)(((x - other) % mod + mod) % mod);}
	_ll operator-(const ll &other){return (_ll)(((x - other) % mod + mod) % mod);}
	_ll operator-(const _ll &other){return (_ll)(((x - other.x) % mod + mod) % mod);}

	_ll operator*(const int &other){return (_ll)((x * other) % mod);}
	_ll operator*(const ll &other){return (_ll)((x * other) % mod);}
	_ll operator*(const _ll &other){return (_ll)((x * other.x) % mod);}

	_ll operator/(const int &other){return (_ll)(x * qpow(other,mod - 2) % mod);}
	_ll operator/(const ll &other){return (_ll)(x * qpow(other,mod - 2) % mod);}
	_ll operator/(const _ll &other){return (_ll)(x * qpow(other.x,mod - 2) % mod);}
    
    bool operator <(const _ll &other)const{return x < other.x;} //能用set了

    friend _ll operator+(const _ll &re,const _ll &other);
};
_ll operator+(const _ll &re,const _ll &other){return (_ll)((re.x + other.x) % mod);}

_ll _2;

_ll ct(_ll x){
	return x * (x - 1) * _2 + x;
}

struct pos{
	_ll ans,l,r;
	bool f;

	_ll ask_ans(){
		if(f){
			ans = ct(l);
			return ans;
		}
		ans += ct(l) + ct(r);
		return ans;
	}

	friend pos operator +(const pos &re,const pos &other);
};

pos operator +(const pos &re,const pos &other){
	pos res = {0,0,0,0};
	res.ans = re.ans + other.ans;
	if(re.f && other.f){
		res.l = res.r = re.l + other.r;
		res.f = 1;
	}else{
		res.l = re.l;
		res.r = other.r;
		if(re.f){
			res.l += other.l;
		}else if(other.f){
			res.r += re.r;
		}else{
			res.ans += ct(re.r + other.l);
		}
	}
	return res;
}

bool vis[61][61];
pos dp[61][61];//dp[i][k] 2^i count_bit(x) <= k {ans,l,r,f}
//先不计入0

pos Ct(int i,int k){
	if(k <= 0){
		return {0,0,0,0};
	}
	if(vis[i][k]) return dp[i][k];
	if(k > i){
		return dp[i][k] = {0,(ll)1 << i,(ll)1 << i,1};//完整区间
	}else{
		pos res = {0,0,0,1};
		//先配成111111111
		for(int j = i - 1;j >= 0;j--){
			res = res + Ct(j,k - (i - 1 - j));
		}
		//然后+1
		res = res + (pos){0,1,1,1};
		vis[i][k] = 1;
		return dp[i][k] = res;
	}
}
void init(){
	_2 = qpow(2,mod - 2);
	for(int k = 1;k <= 60;k++){
		for(int i = 0;i <= 60;i++){
			dp[i][k] = Ct(i,k);
			vis[i][k] = 1;
		}
	}
}

void test(){
	ll n;
	int k;
	cin >> n >> k;
	n--;

	pos cur = {0,1,1,1};

	for(int i = 60;i >= 0;i--){
		if((n >> i) & 1){
			cur = cur + Ct(i,k);
			k--;
		}
	}
	cout << cur.ask_ans().x << '\n';
}

int main(){
	IOS
	init();
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```



