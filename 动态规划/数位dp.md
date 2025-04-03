* 要求统计满足一定条件的数的数量
* 这些条件经过转换可以使用数位的思想去理解和判断
* 输入会提供一个数字区间统计
* 一般用$ans[0,r]-ans[0,l-1]$来统计$r$到$l$内的答案
* 记忆化搜索pre0，limit，pos：前导0，限制，

## [P2602 [ZJOI2010] 数字计数](https://www.luogu.com.cn/problem/P2602)

* 计算$[l,r]$内所有数$[0,9]$出现的次数

* 真麻烦呢
* 希望能理清思路

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

ll sum[13];//前面位确定，后面的特定数出现次数
ll _10[13];

void pre(){
	_10[0] = 1;
	sum[1] = 1;
	_10[1] = 10;
	for(int i = 2;i <= 12;i++){
		sum[i] = sum[i - 1] * 10 + _10[i - 1];
		_10[i] = _10[i - 1] * 10;
	}
}

int p[13];

ll ans[10];
ll anstem[10];

ll val[14];

void get(ll x){
	memset(ans,0,sizeof(ans));
	int cnt = 0;
	while(x){
		p[++cnt] = x % 10;
		x /= 10;
	}
	val[cnt + 1] = 0;
	for(int j = 1;j <= cnt;j++) val[j] = val[j - 1] + p[j]*_10[j - 1];
	for(int i = cnt;i >= 1;i--){
		//全0
		if(i != cnt){
			for(int j = 0;j <= 9;j++){
				ans[j] += sum[i - 1] * 9;
				if(j != 0)
					ans[j] += _10[i - 1];
			}
		}
		//放小于p[i]的数
		if(p[i] >= 2){
			for(int j = 0;j <= 9;j++){
				ans[j] += (p[i] - 1) * sum[i - 1];
			}
		}
		for(int j = 1;j < p[i];j++){
			ans[j] += _10[i - 1];
		}
		if(p[i] != 0 && i != cnt){
			for(int j = 0;j <= 9;j++)
				ans[j] += sum[i - 1];
			ans[0] += _10[i - 1];
		}
		//放p[i]
		ans[p[i]] += val[i - 1] + 1;
	}
}

void test(){
	ll l,r;cin >> l >> r;
	get(l - 1);
	for(int i = 0;i <= 9;i++) anstem[i] = ans[i];
	get(r);
	for(int i = 0;i <= 9;i++) cout << ans[i] - anstem[i] << ' ';
}

int main(){
	IOS
	pre();
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [不要62](https://vjudge.net/problem/HDU-2089)

* $[l,r]$内不含4与连续的62的数的数量
* 记忆化搜索法，状态量有前面一个是否为6，是否有前导0，前面是否与x相同

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

int dp[2][2][2][8];

int p[8];

int cnt = 0;

int num[11] = {0,0,1,2,3,3,4,4,5,6,7};

int asknum(bool f,int x){
	int ans = x;
	if(x > 4) ans--;
	if(f && x > 2) ans--;
	if(x > 6) ans--;
	return ans;
}

int dfs(bool _6,bool pre0,bool limit,int pos){
	if(pos == 0) return 1;
	if(dp[_6][pre0][limit][pos]) return dp[_6][pre0][limit][pos];
	int ans = 0;
	if(pos == cnt){//第1位
		ans += dfs(0,1,0,pos - 1);//放0
		ans += dfs(0,0,0,pos - 1) * num[p[pos]];//放小于p[pos]但不等于6与4的数
		if(p[pos] == 6) ans += dfs(1,0,1,pos - 1);
		else if(p[pos] != 4) ans += dfs(0,0,1,pos - 1);
		if(p[pos] > 6) ans += dfs(1,0,0,pos - 1);
	}else if(limit){
		ans += dfs(0,0,0,pos - 1) * asknum(_6,p[pos]);
		if(p[pos] > 6) ans += dfs(1,0,0,pos - 1);
		if(p[pos] == 4 || (_6 && p[pos] == 2));
		else if(p[pos] == 6) ans += dfs(1,0,1,pos - 1); 
		else ans += dfs(0,0,1,pos - 1);
	}else if(pre0){
		ans += dfs(0,1,0,pos - 1);
		ans += dfs(0,0,0,pos - 1) * 7;
		ans += dfs(1,0,0,pos - 1);
	}else{
		ans += dfs(0,0,0,pos - 1) * asknum(_6,10);
		ans += dfs(1,0,0,pos - 1);
	}
	return ans;
}

int f_p(int x){
	memset(dp,0,sizeof(dp));
	cnt = 0;
	while(x){
		p[++cnt] = x % 10;
		x /= 10;
	}
	return dfs(0,0,0,cnt);
}

void test(){
	int n,m;
	while((cin >> n >> m),n){
		int ansn = f_p(n - 1);
		int ansm = f_p(m);
		// debug(ansn);
		// debug(ansm);
		cout << ansm - ansn << '\n';
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

## [#10165. 「一本通 5.3 例 3」Windy 数](https://loj.ac/p/10165)

### 题意：求不含前导零且相邻两个数字之差至少为 2 的正整数

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

int p[11];

int dp[2][2][11][11];//pos位置前面填pre数的数量

int cnt;

int dfs(bool pre0,bool limit,int pos,int pre){
	if(pos == 0) return 1;
	if(dp[pre0][limit][pos][pre])  return dp[pre0][limit][pos][pre];
	int ans = 0;
	if(pos == cnt){
		ans += dfs(1,0,pos - 1,0);
		ans += dfs(0,1,pos - 1,p[pos]);
		for(int i = 1;i < p[pos];i++)
			ans += dfs(0,0,pos - 1,i);
	}else if(pre0){
		ans += dfs(1,0,pos - 1,0);
		for(int i = 1;i <= 9;i++)
			ans += dfs(0,0,pos - 1,i);
	}else if(limit){
		if(abs(p[pos] - pre) >= 2) ans += dfs(0,1,pos - 1,p[pos]);
		for(int i = 0;i < p[pos];i++){
			if(abs(i - pre) >= 2) ans += dfs(0,0,pos - 1,i);
		}
	}else{
		for(int i = 0;i <= 9;i++)
			if(abs(i - pre) >= 2)
				ans += dfs(0,0,pos - 1,i);
	}
	return dp[pre0][limit][pos][pre] = ans;
}

int ask(int x){
	cnt = 0;
	memset(dp,0,sizeof(dp));
	while(x){
		p[++cnt] = x % 10;
		x /= 10;
	}
	return dfs(1,1,cnt,0);
}

void test(){
	int a,b;cin >> a >> b;
	cout << ask(b) - ask(a - 1) << endl;
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [P3311 数数](https://www.luogu.com.cn/problem/P3311)

* 求不包含m种序列，小于等于n的数的数量
* $dp[pre0][limit][x][pos]$记忆化搜索，$x$代表在tire树中的位置，pos代表在n中的位置
* 预处理出每个位置是否包含m种序列，然后记忆化搜索即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int mod = 1e9 + 7;

const int N = 1.5e3 + 10;

int ptop = 0;

bool f[2][2][1510][1220];
ll dp[2][2][1510][1220];//pre0,limit,x,pos

struct c_tree{
	int tr[26];
	int nex;
	bool f;
}p[N];

void add(string s){
	int pos = 0;
	for(auto c : s){
		if(p[pos].tr[c - '0'] == 0){
			p[pos].tr[c - '0'] = ++ptop;
		}
		pos = p[pos].tr[c - '0'];
	}
	p[pos].f = 1;
}

bool _f[1550];//判断这个点是否为标记点
int nex[1550][26];

void kmp_tr(){
	queue<int> qu;
	for(int i = 0;i < 26;i++){
		if(p[0].tr[i]) qu.push(p[0].tr[i]);
	}
	while(!qu.empty()){
		int x = qu.front();
		qu.pop();
		for(int i = 0;i < 26;i++){
			if(p[x].tr[i]){
				p[p[x].tr[i]].nex = p[p[x].nex].tr[i];
				qu.push(p[x].tr[i]);
			}else
				p[x].tr[i] = p[p[x].nex].tr[i];
		}
	}
	for(int i = 1;i <= ptop;i++){
		int x = i;
		while(x){
			if(p[x].f) {_f[i] = 1;break;}
			else x = p[x].nex;
		}
	}
}

string s;

int cnt;

ll dfs(bool pre0,bool limit,int x,int pos){
	if(_f[x]) return 0;
	if(pos == 0) return 1;
	if(f[pre0][limit][x][pos]) return dp[pre0][limit][x][pos];
	ll ans = 0;
	if(pos == cnt){//第1位
		ans += dfs(1,0,0,pos - 1);
		ans += dfs(0,1,p[x].tr[s[pos]],pos - 1);
		for(int i = 1;i < s[pos];i++) ans += dfs(0,0,p[x].tr[i],pos - 1);
	}else if(pre0){
		ans += dfs(1,0,0,pos - 1);
		for(int i = 1;i <= 9;i++) ans += dfs(0,0,p[x].tr[i],pos - 1);
	}else if(limit){
		ans += dfs(0,1,p[x].tr[s[pos]],pos - 1);
		for(int i = 0;i < s[pos];i++) ans += dfs(0,0,p[x].tr[i],pos - 1);
	}else{
		for(int i = 0;i <= 9;i++) ans += dfs(0,0,p[x].tr[i],pos - 1);
	}
	f[pre0][limit][x][pos] = 1;
	return dp[pre0][limit][x][pos] = ans % mod;
}

ll get(){
	reverse(s.begin(),s.end());
	cnt = s.size();
	s = ' ' + s;
	for(int i = 1;i <= cnt;i++) s[i] -= '0';
	return dfs(0,0,0,cnt);
}

void test(){
	cin >> s;
	int m;cin >> m;
	while(m--){
		string ms;cin >> ms;
		add(ms);
	}
	kmp_tr();
	cout << get() - 1 << endl;
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

