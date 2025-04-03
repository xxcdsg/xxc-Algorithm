## [A - Max Sum Plus Plus](https://vjudge.net.cn/problem/HDU-1024)

>  $n$ 个数，取 $m$ 个区间使得和最大，区间不相交

##### 思路：

*  $m$ 不大，那么我们跑 $m$ 次，每一次从上一次的 $dp$ 值出发， 设 $dp[i]$ 为该次至少取了 $k$ 次区间，结尾为 $i$ 的最大值
* 那么有 $dp$ 转移方程 $dp[i]=\max\set{dp[j]+a[i],k-1\leq j\leq i-1}(未更新)$ 与 $dp[i]=max\set{dp[i],dp[i-1]+a[i]}(更新后)$

* 如果从前往后 $dp$ ，那么为了取到未更新的最大值，我们要存一个中间变量

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
    int n,m;
    while(cin >> m >> n){
        vector<int> dp(n + 1),a(n + 1);
        for(int i = 1;i <= n;i++) cin >> a[i];
        for(int i = 0;i <= n;i++) dp[i] = -inf;
        dp[0] = 0;
        int ma = 0;
        for(int j = 1;j <= m;j++){
            int pre = dp[j - 1];
            for(int i = j;i <= n;i++){
                int tpre = max(pre,dp[i]);
                dp[i] = -inf;
                dp[i] = max(dp[i],dp[i - 1] + a[i]);
                dp[i] = max(dp[i],pre + a[i]);
                pre = tpre;
            }
            dp[j - 1] = -inf;
        }
        ma = -inf;
        for(int i = m;i <= n;i++){
            ma = max(ma,dp[i]);
        }
        cout << ma << '\n';
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

## [B - Ignatius and the Princess IV](https://vjudge.net.cn/problem/HDU-1029)

> 找出序列中出现次数至少为 $(n+1)/2$ 的数

##### 思路：

* 经典 $tick$ ，一个数出现的次数比其他数出现次数之和都多，要找出这个数，我们想让它和所有其他数抵消之后，就这个数会剩下，因此可以用栈模拟， $O(n)$ 即可解决

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
	int n;
	while(cin >> n){
		stack<int> st;
		for(int i = 1;i <= n;i++){
			int x;cin >> x;
			if(st.empty() || st.top() == x){
				st.push(x);
			}else{
				st.pop();
			}
		}
		cout << st.top() << '\n';
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

## [C - Monkey and Banana](https://vjudge.net.cn/problem/HDU-1069)

> 供应无限的长方体，问叠放的最高高度
>
> 要求下方的长方体所放方向的长与宽均严格大于上方的长方体

##### 思路：

* 一个长方体有六种摆放方法
* 我们离散化长度
*  $dp[i][j]$ 表示最高的方块的底为长宽离散化之后对应的最高高度
* 我们从小到大枚举 $dp$ ，枚举所有方块

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

struct pos{
	int x,y,z;
};

void test(){
	int n;
	int ptop = 0;
	while(cin >> n){
		if(n == 0) break;
		ptop++;
		cout << "Case " << ptop << ": maximum height = ";

		vector<int> a(n + 1);
		vector<pos> ve;
		vector<int> tem;
		set<int> se;
		for(int i = 1;i <= n;i++){
			pos res;cin >> res.x >> res.y >> res.z;
			se.insert(res.x);
			se.insert(res.y);
			se.insert(res.z);
			ve.push_back(res);
			swap(res.x,res.y);
			ve.push_back(res);
			swap(res.x,res.z);
			ve.push_back(res);
			swap(res.y,res.z);
			ve.push_back(res);
			swap(res.x,res.y);
			ve.push_back(res);
			swap(res.x,res.z);
			ve.push_back(res);
		}
		int sz = se.size();
		vector<int> mp;
		mp.push_back(0);
		for(auto x : se){
			tem.push_back(x);
			mp.push_back(x);
		}
		for(pos & block : ve){
			block.x = lower_bound(tem.begin(),tem.end(),block.x) - tem.begin() + 1;
			block.y = lower_bound(tem.begin(),tem.end(),block.y) - tem.begin() + 1;
			block.z = lower_bound(tem.begin(),tem.end(),block.z) - tem.begin() + 1;
		}
		vector<vector<int>> dp(sz + 1,vector<int>(sz + 1));

		for(int i = 0;i <= sz;i++){
			for(int j = 0;j <= sz;j++){
				for(pos block : ve){
					int x = block.x,y = block.y,z = block.z;
					if(x > i && y > j) dp[x][y] = max(dp[x][y],dp[i][j] + mp[z]);	
				}
			}
		}
		int ans = 0;
		for(int i = 1;i <= sz;i++)
			for(int j = 1;j <= sz;j++)
				ans = max(ans,dp[i][j]);
		cout << ans << '\n';
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

## [D - Doing Homework](https://vjudge.net.cn/problem/HDU-1074)

> 每个科目都有截止时间与耗时
>
> 截止时间之后也可以做，但是每个科目每拖一天都会扣一分
>
> 问最少的扣分

##### 思路：

*  $n$ 较小，使用状压 $dp$
* 设二进制位为 $1$ 为该状态已经做了某个科目
* 那么我们有 $dp$ 转移方程 $dp[i + (1 << k)] = dp[i]+use$
* 其中 $use$ 代表花费时间中途会扣除的分，我们枚举所有的科目即可得出

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

struct pos{
	string name;
	int begin,us;
};

void test(){
	int n;cin >> n;
	vector<pos> ve(n);
	vector<pair<int,int>> pre(1 << n);
	for(int i = 0;i < n;i++){
		cin >> ve[i].name >> ve[i].begin >> ve[i].us;
	}
	vector<int> dp(1 << n,inf);
	vector<int> ct(1 << n);
	dp[0] = 0;
	for(int i = 0;i < (1 << n);i++){
		int no = 0;
		for(int kk = 0;kk < n;kk++){
			if((i >> kk) & 1){
				no += ve[kk].us;
			}
		}
		for(int k = 0;k < n;k++){
			if(((i >> k) & 1) == 0){//没取到
				//dp[i] -> dp[i + (1 << k)]
				int ed = no + ve[k].us;
				int ad = 0;
				for(int j = 0;j < n;j++){
					if(((i >> j) & 1) == 0){
						ad += max(min(ed - ve[j].begin,ed - no),(ll)0);
					}
				}
				if(dp[i + (1 << k)] > dp[i] + ad){
					dp[i + (1 << k)] = dp[i] + ad;
					pre[i + (1 << k)] = {i,k};
				}
			}
		}
	}
	cout << dp[(1 << n) - 1] << '\n';
	int no = (1 << n) - 1;
	stack<string> st;
	while(no){
		st.push(ve[pre[no].second].name);
		no = pre[no].first;
	}
	while(!st.empty()){
		cout << st.top() << '\n';
		st.pop();	
	}
}

signed main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## [E - Super Jumping! Jumping! Jumping!](https://vjudge.net.cn/problem/HDU-1087)

>  单调递增子序列的和的最大值

##### 思路：

*  $O(n^2)$ 记录每个点结束的最大值，然后 $dp$

* 应该没有 $O(nlog_2n)$ 的做法

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
	int n;
	while(cin >> n){
		if(n == 0) break;
		vector<int> a(n + 1);
		for(int i = 1;i <= n;i++) cin >> a[i];
		//O(n^2)
		vector<ll> dp(n + 1);
		for(int i = 1;i <= n;i++){
			ll ma = 0;
			for(int j = 1;j <= i;j++){
				if(a[i] > a[j]){
					ma = max(ma,dp[j]);
				}
			}
			dp[i] = ma + a[i];
		}
		ll ma = 0;
		for(int i = 1;i <= n;i++) ma = max(ma,dp[i]);
		cout << ma << '\n';
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

## [F - Piggy-Bank](https://vjudge.net.cn/problem/HDU-1114)

> 给货币，凑重量，问最小价值

##### 思路：

* 纯纯的枚举 $dp$

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

struct account{
	int w,v;
};

void test(){
	int e,f;cin >> e >> f;
	int ed = f - e;
	vector<int> dp(ed + 1,inf);
	int n;cin >> n;
	vector<account> ve(n + 1);
	for(int i = 1;i <= n;i++){
		cin >> ve[i].v >> ve[i].w;
	}
	dp[0] = 0;
	for(int i = 1;i <= ed;i++){
		for(int j = 1;j <= n;j++){
			if(i >= ve[j].w){
				dp[i] = min(dp[i],dp[i - ve[j].w] + ve[j].v);
			}
		}
	}
	if(dp[ed] == inf) cout << "This is impossible.\n";
	else{
		cout << "The minimum amount of money in the piggy-bank is " << dp[ed] << ".\n";
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

## [G - 免费馅饼](https://vjudge.net.cn/problem/HDU-1176)

>  $11$ 个位置，每次可以移动到相邻位置
>
>  给出某些时刻与位置，如果那个时刻在那个位置，则加一分
>
>  问分数最大值为多少

##### 思路：

* 位置数少，已知转移到相邻位置，直接 $dp$

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

const int T = 1e5 + 10;

void test(){
    int n;
    while(cin >> n){
        if(n == 0) break;
        vector<vector<int>> dp(T,vector<int>(11,-inf));
        dp[0][5] = 0;
        vector<pair<int,int>> ve(n + 1);
        int ma = 0;
        for(int i = 1;i <= n;i++){
            cin >> ve[i].second >> ve[i].first;
            ma = max(ve[i].first,ma);
        }
        sort(ve.begin(),ve.end());
        int l = 1;
        for(int i = 1;i <= ma;i++){
            for(int j = 0;j <= 10;j++){
                dp[i][j] = dp[i - 1][j];
                if(j != 0) dp[i][j] = max(dp[i][j],dp[i - 1][j - 1]);
                if(j != 10) dp[i][j] = max(dp[i][j],dp[i - 1][j + 1]);
            }
            for(int j = 0;j <= 10;j++){
                while(l <= n && ve[l].first == i && ve[l].second == j){
                    dp[i][j]++;
                    l++;
                }
            }
        }
        int ans = 0;
        for(int i = 0;i <= 10;i++)
            ans = max(ans,dp[ma][i]);
        cout << ans << '\n';
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

## [H - Tickets](https://vjudge.net.cn/problem/HDU-1260)

> 给出取每个东西的花费与连续取两个的花费，问最小花费

##### 思路：

* 水

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

void fcout(int x){
	if(x < 10) cout << '0';
	cout << x;
}

void test(){
	int n;cin >> n;
	vector<int> dp(n + 1,inf),a(n + 1),b(n + 1);
	for(int i = 1;i <= n;i++) cin >> a[i];
	for(int i = 1;i <= n - 1;i++) cin >> b[i];
	dp[0] = 0;
	dp[1] = a[1];
	for(int i = 2;i <= n;i++){
		dp[i] = min({dp[i],dp[i - 1] + a[i],dp[i - 2] + b[i - 1]});
	}
	int h = 8,m = 0,s = dp[n];
	m += s / 60;
	s %= 60;
	h += m / 60;
	m %= 60;
	bool f = 1;
	if(h > 12){
		f = 0;
		h -= 12;
	}
	fcout(h);
	cout << ':';
	fcout(m);
	cout << ':';
	fcout(s);
	cout << ' ';
	if(f) cout << "am\n";
	else cout << "pm\n";
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## [I - 最少拦截系统](https://vjudge.net.cn/problem/HDU-1257)

> 分解为最少递增子序列

##### 思路：

* 答案是不是和最长上升子序列的长度一样啊

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
	int n;
	while(cin >> n){
		vector<int> a;
		for(int i = 1;i <= n;i++){
			int x;cin >> x;
			if(a.empty() || (*a.rbegin()) < x){
				a.push_back(x);
			}else{
				*lower_bound(a.begin(),a.end(),x) = x;
			}
		}
		cout << a.size() << '\n';
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

* 分析一下
* 假设我们现在已经有了一些序列
* 那么我们现在又拿了一个数
* 那么我们最好的方法就是将他放在第一个比他的的位置后面
* 不存在则新增序列
* 那么这种做法正好是最长上升子序列的做法
* 最后每个数都是子序列的最后选的数

## [J - FatMouse's Speed](https://vjudge.net.cn/problem/HDU-1160)

> 求最长序列使得对应一个序列递增，另外一个递减

##### 思路：

* 那么就是给一个排序之后求另外一个的最长递增子序列
*  $dp$ 很好做

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
	vector<tuple<int,int,int>> ve;
	int x,y,ptop = 0;
	while(cin >> x >> y){
		ptop++;
		ve.push_back(make_tuple(x,y,ptop));
	}
	sort(ve.begin(),ve.end());
	int n = ve.size();
	vector<int> dp(n + 1);
	vector<int> pre(n + 1);
	int maid = 1,ma = 1;
	for(int i = 0;i < n;i++){
		dp[i] = 1;
		for(int j = 0;j < i;j++){
			auto [xi,yi,idi] = ve[i];
			auto [xj,yj,idj] = ve[j];
			if(xi > xj && yi < yj){
				if(dp[j] + 1 > dp[i]){
					dp[i] = dp[j] + 1;
					pre[idi] = idj;
					if(dp[i] > ma){
						ma = dp[i];
						maid = idi;
					}
				}
			}
		}
	}
	stack<int> st;
	while(maid){
		st.push(maid);
		maid = pre[maid];
	}
	cout << ma << '\n';
	while(!st.empty()){
		cout << st.top() << '\n';
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

* 问题是 $nlog_2n$ 的二分法
* 要排除相同情况的干扰
* 第一个参数相同，我们可以让第二个参数排序从小到大排
* 我不理解为什么过不了

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

bool cmp(tuple<int,int,int> a,tuple<int,int,int> b){
	auto [x,y,z] = a;
	auto [xx,yy,zz] = b;
	return x < xx && y < yy;
}

void test(){
	vector<tuple<int,int,int>> pa;
	int x,y;
	int ptop = 0;
	while(cin >> x >> y){
		ptop++;
		swap(x,y);
		x = -x;
		pa.push_back(make_tuple(x,y,-ptop));
	}
	sort(pa.begin(),pa.end());
	for(auto &[x,y,id] : pa){
		id = -id;
	}
	vector<tuple<int,int,int>> a;
	vector<int> pre(pa.size() + 1);
	for(auto item : pa){
		if(a.empty() || cmp((*a.rbegin()),item)){
			if(a.empty()) pre[get<2>(item)] = 0;
			else pre[get<2>(item)] = get<2>(*a.rbegin());
			a.push_back(item);
		}else{
			auto it = lower_bound(a.begin(),a.end(),item,cmp);
			pre[get<2>(item)] = pre[get<2>(*it)];
			*it = item;
		}
	}
	stack<int> st;
	int no = get<2>(*a.rbegin());
	while(no){
		st.push(no);
		no = pre[no];
	}
	cout << a.size() << '\n';
	while(!st.empty()){
		cout << st.top() << '\n';
		st.pop();
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

## [K - Common Subsequence](https://vjudge.net.cn/problem/OpenJ_Bailian-1458)

> 最长相同子序列

##### 思路：

*  $O(nm)的dp$

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
	string s,t;
	while(cin >> s >> t){
		int n = s.size(),m = t.size();;
		s = ' ' + s;
		t = ' ' + t;
		vector<vector<int>> dp(n + 1,vector<int>(m + 1,-inf));
		dp[1][0] = dp[0][1] = dp[0][0] = 0;
		for(int i = 1;i <= n;i++){
			for(int j = 1;j <= m;j++){
				dp[i][j] = max({dp[i - 1][j],dp[i][j - 1],dp[i][j]});
				if(s[i] == t[j])
					dp[i][j] = max(dp[i][j],dp[i - 1][j - 1] + 1);
			}
		}
		cout << dp[n][m] << '\n';
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

## [L - Longest Ordered Subsequence](https://vjudge.net.cn/problem/OpenJ_Bailian-2533)

* 重复， $LIS$

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;
int main(){
	int n;cin >> n;vector<int> a;
	for(int i = 1;i <= n;i++){
		int x;cin >> x;
		if(a.empty() || (*a.rbegin()) < x){
			a.push_back(x);
		}else{
			*lower_bound(a.begin(),a.end(),x) = x;
		}
	}
	cout << a.size() << '\n';
}
```

## [M - Jury Compromise](https://vjudge.net.cn/problem/OpenJ_Bailian-1015)

> 每个人可以给同时给两方打分，你需要在 $n$ 个人中选取 $m$ 个人，使得其分差最小，如果有分差相同的方案，则输出分数总和最大的方案

##### 思路：

*  $dp$ 拔河问题

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
	int n,m;
	int ptop = 0;
	while(cin >> n >> m){
		ptop++;
		if(n == 0 && m == 0) break;
		cout << "Jury #" << ptop << '\n';
		vector<tuple<int,int,int>> l,r;
		for(int i = 1;i <= n;i++){
			tuple<int,int,int> tem;
			auto &[base,ad,id] = tem;
			id = i;
			cin >> base >> ad;
			if(base < ad){
				ad = ad - base;
				l.push_back(tem);
			}
			else{
				swap(ad,base);
				ad = ad - base;
				r.push_back(tem);
			}
		}

		vector<vector<pair<int,vector<int>>>> dpl(401,vector<pair<int,vector<int>>>(m + 1,make_pair(-1,vector<int>(0))));//base,id序列
		vector<vector<pair<int,vector<int>>>> dpr(401,vector<pair<int,vector<int>>>(m + 1,make_pair(-1,vector<int>(0))));

		sort(dpl.begin(),dpl.end());
		sort(dpr.begin(),dpr.end());
		
		dpl[0][0].first = dpr[0][0].first = 0;
		
//		for(int i = 0;i <= 400;i++)
//			dpl[i][0].first = 0;
//		for(int i = 0;i <= 400;i++)
//			dpr[i][0].first = 0;
		for(auto &[base,ad,id] : l){
			for(int j = 400;j >= ad;j--){
				for(int k = m;k >= 1;k--){
					auto &[_base,_ve] = dpl[j - ad][k - 1];
					auto &[nbase,nve] = dpl[j][k];
					if(_base != -1 && _base + base > nbase){
						nbase = _base + base;
						nve = _ve;
						nve.push_back(id);
					}
				}
			}
		}
				
			
		for(auto &[base,ad,id] : r){
			for(int j = 400;j >= ad;j--){
				for(int k = m;k >= 1;k--){
					auto &[_base,_ve] = dpr[j - ad][k - 1];
					auto &[nbase,nve] = dpr[j][k];
					if(_base != -1 && _base + base > nbase){
						nbase = _base + base;
						nve = _ve;
						nve.push_back(id);
					}
				}
			}
		}
		
		
		tuple<int,int,int,int,int,int> ans;
		auto &[midis,masum,lad,rad,luse,ruse] = ans;
		midis = inf;

		for(int _luse = 0;_luse <= m;_luse++){
			int _ruse = m - _luse;
			for(int _lad = 0;_lad <= 400;_lad++){
				if(dpl[_lad][_luse].first == -1) continue;
				for(int _rad = 0;_rad <= 400;_rad++){
					if(dpr[_rad][_ruse].first == -1) continue;
					if(abs(_lad - _rad) < midis || (abs(_lad - _rad) == midis && _lad + _rad + (dpl[_lad][_luse].first + dpr[_rad][_ruse].first) * 2 > masum)){
						midis = abs(_lad - _rad);
						masum = _lad + _rad + (dpl[_lad][_luse].first + dpr[_rad][_ruse].first) * 2;
						// debug(get<0>(dpl[_lad][_luse]))
						// debug(get<0>(dpl[_rad][_ruse]))
						lad = _lad;
						rad = _rad;
						luse = _luse;
						ruse = _ruse;
					}
				}
			}
		}

		cout << "Best jury has value " << (masum - lad - rad) / 2 + rad << " for prosecution and value " << (masum - lad - rad) / 2 + lad << " for defence:\n";

		vector<int> ansid;

		vector<int> lidlist = dpl[lad][luse].second;
		vector<int> ridlist = dpr[rad][ruse].second;

		for(auto x : lidlist) ansid.push_back(x);
		for(auto x : ridlist) ansid.push_back(x);

		sort(ansid.begin(),ansid.end());

		for(auto x : ansid) cout << ' ' << x;
		cout << "\n\n";
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

## [N - FatMouse and Cheese](https://vjudge.net.cn/problem/HDU-1078)

> 每个位置有吃的
>
> 每次只能横向或者纵向移动最多 $k$ 格
>
> 每次吃的东西要比上一次多
>
> 问最多能吃多少东西
>
> 初始位置为 $(0,0)$ ，并且已经吃了 $(0,0)$ 位置的东西

##### 思路：

* 我们按东西大小排序，每次转移只能是从食物小的图转移到食物大的图
* 注意食物相同的我们要同时更新
* 不同时更新好像也行？
* 因为只要保证小的比大的先更新即可，转移的时候还会判断真的是否能转移

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
	int n,k;
	while(cin >> n >> k){;
		vector<vector<int>> a = vector<vector<int>>(n + 1,vector<int>(n + 1));
		vector<vector<int>> dp = vector<vector<int>>(n + 1,vector<int>(n + 1,-inf));
		vector<tuple<int,int,int>> ve;
		if(n == -1 && k == -1) break;
		for(int i = 1;i <= n;i++)
			for(int j = 1;j <= n;j++){
				cin >> a[i][j];
				tuple<int,int,int> res;
				auto &[val,x,y] = res;
				val = a[i][j];
				x = i;
				y = j;
				ve.push_back(res);
			}
		sort(ve.begin(),ve.end());
		int nn = ve.size();
		dp[1][1] = a[1][1];
		for(int i = 0;i < nn;i++){
			auto [val,x,y] = ve[i];
			for(int ii = x - k;ii <= x + k;ii++){
				if(ii <= 0 || ii > n || ii == x) continue;
				if(dp[x][y] < dp[ii][y] + a[x][y] && a[x][y] > a[ii][y]){
					dp[x][y] = max(dp[ii][y] + a[x][y],dp[x][y]);
				}
			}
			for(int jj = y - k;jj <= y + k;jj++){
				if(jj <= 0 || jj > n || jj == y) continue;
				if(dp[x][y] < dp[x][jj] + a[x][y] && a[x][y] > a[x][jj]){
					dp[x][y] = max(dp[x][jj] + a[x][y],dp[x][y]);
				}
			}
		}
		int ans = 0;
		for(int i = 1;i <= n;i++)
			for(int j = 1;j <= n;j++)
				ans = max(ans,dp[i][j]);
		cout << ans << '\n';
	}
}

int main(){
//	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

