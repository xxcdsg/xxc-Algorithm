## [A - 单调栈](https://vjudge.net.cn/problem/洛谷-P5788)

> 求每个位置后面第一个比它大的数的位置

##### 思路：

* 我们如果暴力找，那么每次都是向后找，直到找到比它大的数
* 那么如果找到一个小的数，那么我们至少要找比这个数大的数，这样才可能比我们要找的位置的数大
* 也就是我们可以维护一个单调递增的序列，每次在这个单调递增序列中找即可

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
	stack<int> st;
	vector<int> a(n + 1),f(n + 1);
	a[0] = inf;
	st.push(0);
	for(int i = 1;i <= n;i++) cin >> a[i];
	for(int i = n;i >= 1;i--){
		while(!st.empty() && a[st.top()] <= a[i]) st.pop();
		f[i] = st.top();
		st.push(i);
	}
	for(int i = 1;i <= n;i++) cout << f[i] << ' ';
	cout << '\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [B - Bad Hair Day S](https://vjudge.net.cn/problem/洛谷-P2866)

> 假设每个人只能看到比它高的人，并且不能被挡住
>
> 问所有人能看到的人的数量之和
>
> 人排成一串

##### 思路：

* 同上，只是变成了记录数量

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
	stack<int> st;
	vector<int> h(n + 1);
	for(int i = 1;i <= n;i++) cin >> h[i];
	reverse(h.begin() + 1,h.end());
	ll ans = 0;
	for(int i = n;i >= 1;i--){
		while(!st.empty() && st.top() <= h[i]){
			st.pop();
		}
		ans += st.size();
		st.push(h[i]);
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

## [C - Maximum Xor Secondary](https://vjudge.net.cn/problem/CodeForces-280B)

> 一个序列中的最大的数与次大的数的异或和为这个序列的幸运数字
>
> 问一个序列的所有连续子序列的幸运数字最大值为多少
>
> 给出的序列所有数都不相同

##### 思路：

* 我们枚举出所有连续区间中最大的数与次大的组合，即可得到最大的组合
* 我们假设枚举到一个位置，我们要求后面区间的最大值，要求这个最大值小于枚举的数，那么就得到了所有前面大，后面小的组合
* 然后在将这个数添加进入序列中之前，我们再将栈顶与这个数组合，这时，最大的数就是后面栈顶元素，次大的数为枚举到的数
* 这样就将所有可能性考虑到了

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
	vector<int> s(n + 1);
	stack<int> st;
	int ans = 0;
	for(int i = 1;i <= n;i++){
		cin >> s[i];
		while(!st.empty() && st.top() < s[i]){
			ans = max(ans,s[i]^st.top());
			st.pop();
		}
		if(!st.empty()){
			ans = max(ans,s[i]^st.top());
		}
		st.push(s[i]);
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

## [D - Max GEQ Sum](https://vjudge.net.cn/problem/CodeForces-1691D)

> 求对于所有连续子序列来说，其最大值是否都比总和大

##### 思路：

* 暴力一点的做法 $O(nlog_2n)$ 大常数
* 我们先用两轮单调栈来得出每个数作为最大值是，其可取到的区间范围（也就是左端点与右端点）
* 然后计算前缀和并将其放入可查询区间最大值与最小值的线段树中
* 那么我们在枚举每个作为最大值时，为了让总和最大，那么后面的前缀和要取最大值，前面的取最小值，差分，再与这个最大值比较即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define int long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

#define ll long long
#define lson (x << 1)
#define rson (x << 1 | 1)
const int MAXN = 5e5 + 10;
ll a[MAXN];
class tree{
	public:
	ll ma[MAXN*4];//代表的最大值
	ll mi[MAXN*4];//代表的最小值
	ll l[MAXN*4],r[MAXN*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			ma[x] = a[_l];
			mi[x] = a[_l - 1];
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
		ma[x] = max(ma[lson],ma[rson]);
		mi[x] = min(mi[lson],mi[rson]);
	}
	ll findma(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return ma[x];
		else
		{
			int mid = (l[x] + r[x]) / 2;
			ll ma = -INF;
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
			int mid = (l[x] + r[x]) / 2;
			ll mi = INF;
			if(_l <= mid)
				mi = findmi(_l,_r,lson);
			if(_r >= mid + 1)
				mi = min(mi,findmi(_l,_r,rson));
			if(_l == 0) mi = min(mi,(ll)0);
			update(x);
			return mi;
		}
	}
}tr;

void test(){
	int n;cin >> n;
	vector<int> aa(n + 1);
	vector<int> l(n + 1),r(n + 1);
	stack<int> st;
	for(int i = 1;i <= n;i++){
		cin >> aa[i];
		while(!st.empty() && aa[st.top()] <= aa[i]) st.pop();
		if(st.empty()) l[i] = 1;
		else l[i] = st.top() + 1;
		st.push(i);
	}
	while(!st.empty()) st.pop();
	for(int i = n;i >= 1;i--){
		while(!st.empty() && aa[st.top()] <= aa[i]) st.pop();
		if(st.empty()) r[i] = n;
		else r[i] = st.top() - 1;
		st.push(i);
	}
	//得到每个数作为最大值时的左右最大范围

	vector<ll> sum(n + 1);
	for(int i = 1;i <= n;i++){
		sum[i] = sum[i - 1] + aa[i];
		a[i] = sum[i];
	}
	tr.build(1,n,1);
	bool f = 1;
	for(int i = 1;i <= n;i++){
		if(tr.findma(i,r[i],1) - tr.findmi(l[i],i,1) > aa[i]) f = 0;
	}
	if(f) cout << "Yes\n";
	else cout << "No\n";
}

signed main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

* 另一种 $O(n)$ 的做法
* 如果这个数为这个区间最大的数，那么它左边取数，右边取数，排除其自身，取的最大值一定要小于 $0$ 才能让题目条件成立
* 而两边取数，实际上我们可以简化为取一边的数，因为如果两边取数大于 $0$ ，说明我们某一边取数肯定大于 $0$
* 那么分两边就跑两次不同方向的单调栈
* 然后我们假设我们放入单调栈的元素向单调栈拓展的反方向取数，取不到大于 $0$ 的和
* 那么对于下一个要放入单调栈的数来说，它肯定要取大的，不会取不大于 $0$ 的和的部分，因此我们只要考虑单调栈退栈时栈顶元素到新增元素位置(不包括新增元素位置)内的和是否大于 $0$ 即可
* 这样就能将复杂度降为 $O(n)$

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
	vector<int> a(n + 1);
	vector<ll> sum(n + 1);
	stack<int> st;
	a[0] = 0;
	bool f = 1;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		sum[i] = sum[i - 1] + a[i];
		while(!st.empty() && a[st.top()] <= a[i]){
			if(sum[i - 1] - sum[st.top() - 1] > 0) f = 0;
			st.pop();
		}
		st.push(i);
	}
	while(!st.empty()) st.pop();
	for(int i = n;i >= 1;i--){
		while(!st.empty() && a[st.top()] <= a[i]){
			if(sum[i] - sum[st.top()] < 0) f = 0;
			st.pop();
		}
		st.push(i);
	}
	if(f) cout << "Yes\n";
	else cout << "No\n";
}

signed main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## [E - Second Sum](https://vjudge.net.cn/problem/AtCoder-abc140_e)

> 给一个长度为 $n$ 的序列 $a$ ，问所有长度大于等于 $2$ 的连续子序列的次大值的和

##### 思路：

* 我们假设一个位置为次大值，那么我们如果要找出其作为一个区间的次大值，那么我们就是要找其两边拓展出去的第一次出现的比其大的位置与第二次
* 第一次可以用单调栈，第二次用 $st$ 即可

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

class ST{
public:
	vector<vector<int>> st;
	vector<vector<int>> to;
	const int MAXlg = 20;
	ST(int n,vector<int> &a){
		st = vector<vector<int>>(n + 2,vector<int>(MAXlg + 1));
		to = vector<vector<int>>(n + 2,vector<int>(MAXlg + 1));
		for(int i = 1;i <= n;i++){
			to[i][0] = i + 1;
		}
		to[n][0] = n + 1;
		for(int j = 0;j <= MAXlg;j++)
			to[n + 1][j] = n + 1;
		for(int i = n;i >= 1;i--){
			st[i][0] = a[i];
			for(int j = 1;j <= MAXlg;j++){
				st[i][j] = max(st[i][j - 1],st[to[i][j - 1]][j - 1]);
				to[i][j] = to[to[i][j - 1]][j - 1];
			}
		}
	}
	int ask(int l,int j){
		return st[l][j];
	}
};

void test(){
	int n;cin >> n;
	vector<int> p(n + 1);
	for(int i = 1;i <= n;i++) cin >> p[i];
	vector<int> l(n + 1),r(n + 1),_l(n + 1),_r(n + 1);
	ST stlist = ST(n,p);
	stack<int> st;
	for(int i = n;i >= 1;i--){
		while(!st.empty() && p[st.top()] < p[i]) st.pop();
		if(st.empty()) r[i] = n + 1;
		else r[i] = st.top();
		if(r[i] == n + 1) _r[i] = n + 1;
		else{
			_r[i] = r[i] + 1;
			for(int j = 20;j >= 0;j--){
				if(stlist.ask(_r[i],j) < p[i]) _r[i] = stlist.to[_r[i]][j];
			}
		}
		st.push(i);
	}

	reverse(p.begin() + 1,p.end());
	ST stlist2 = ST(n,p);
	while(!st.empty()) st.pop();
	for(int i = n;i >= 1;i--){
		while(!st.empty() && p[st.top()] < p[i]) st.pop();
		if(st.empty()) l[i] = n + 1;
		else l[i] = st.top();
		if(l[i] == n + 1) _l[i] = n + 1;
		else{
			_l[i] = l[i] + 1;
			for(int j = 20;j >= 0;j--){
				if(stlist2.ask(_l[i],j) < p[i]) _l[i] = stlist2.to[_l[i]][j];
			}
		}
		st.push(i);
	}

	for(int i = 1;i <= n;i++){
		l[i] = n + 1 - l[i];
		_l[i] = n + 1 - _l[i];
	}
	reverse(l.begin() + 1,l.end());
	reverse(_l.begin() + 1,_l.end());
	reverse(p.begin() + 1,p.end());

	ll ans = 0;
	for(int i = 1;i <= n;i++){
		ans += (((ll)_r[i] - r[i]) * (i - l[i]) + ((ll)r[i] - i) * (l[i] - _l[i])) * p[i];
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

## [F - Skyscrapers (hard version)](https://vjudge.net.cn/problem/CodeForces-1313C2)

> 给出每个位置的值最多能到多少 $m_i$ 
>
> 并且不存在 $i<j<k$ 使得 $a_j>a_i<a_k$
>
> 问整个序列的总和最大为多少，当取到最大时，序列为何

##### 思路：

* 将题意转化一下，不存在 $i<j<k$ 使得 $a_j>a_i<a_k$ ，也就是 $i<j<k$ ，$a_j>min\set{a_i,a_k}$，也就是说，越中间越大
* 那么这个序列的特点就是先递增，再递减
* 我们假设出中间最大的位置，向着两边递推，那么我们就有了 $O(n^2)$ 的做法
* 我们将递推分成两边，我们能否 $O(n)$ 递推出一边的值呢
* 我们想加入 $m_i$ 时，前面大于 $m_i$ 的位置都会最多到 $m_i$ ，直到遇到比 $m_i$ 小的位置，这个位置可以通过单调栈找出
* 然后假设那个位置的答案为 $ans[l]$ ，那么新的答案就是 $ans[l]+(i-l)*m_i$
* 同理右边也可以这么得出
* 复杂度 $O(n)$

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

void test(){
	int n;cin >> n;
	vector<int> a(n + 2);
	vector<int> l(n + 1),r(n + 1);
	vector<ll> ansl(n + 2),ansr(n + 2);
	stack<int> st;
	st.push(0);
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		while(!st.empty() && a[st.top()] > a[i]) st.pop();
		ansl[i] = ansl[st.top()] + (i - st.top()) * a[i];
		st.push(i);
	}
	while(!st.empty()) st.pop();
	st.push(n + 1);
	for(int i = n;i >= 1;i--){
		while(!st.empty() && a[st.top()] > a[i]) st.pop();
		ansr[i] = ansr[st.top()] + (st.top() - i) * a[i];
		st.push(i);
	}
	ll ans = 0;
	int id = 1;
	for(int i = 1;i <= n;i++){
		if(ans < ansl[i] + ansr[i] - a[i]){
			ans = ansl[i] + ansr[i] - a[i];
			id = i;
		}
	}
	int ma = a[id];
	for(int i = id;i >= 1;i--){
		ma = min(a[i],ma);
		a[i] = ma;
	}
	ma = a[id];
	for(int i = id;i <= n;i++){
		ma = min(a[i],ma);
		a[i] = ma;
	}
	// cout << ans << '\n';
	for(int i = 1;i <= n;i++) cout << a[i] << ' ';
}

signed main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [G - Discrete Centrifugal Jumps](https://vjudge.net.cn/problem/CodeForces-1407D)

> 有 $n$ 个高度 $h_i$
>
> 初始在位置 $1$ 要到 $n$
>
> 如果 $i+1=j$ ，或者 $h_i,h_j$ 比中间的都大或者都小，那么 $i$ 可以一步跳到 $j$
>
> 问最小步数

##### 思路：

* 我们想单调栈每次退队或加入队列， $pair(st.top(),i)$ 代表的是什么
* 实际上代表的是这个区间， $st.top(),i$ 为这个区间的最大值（或者最小值），如果是退队那么 $i$ 是最大值，如果是加队，就是次大值
* 那么我们将这两种跳转方法加上 $i+1=j$ 的跳转方式 $dp$ 即可

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
	vector<int> h(n + 1),ans(n + 1);
	vector<vector<int>> l1(n + 1),l2(n + 1);
	stack<int> st;
	st.push(0);
	h[0] = inf;
	for(int i = 1;i <= n;i++){
		cin >> h[i];
		int pre = -1;
		while(!st.empty() && h[st.top()] < h[i]){
			if(pre != h[st.top()])
				l1[i].push_back(st.top());
			pre = h[st.top()];
			st.pop();
		}
		l1[i].push_back(st.top());
		st.push(i);
	}
	while(!st.empty()) st.pop();
	st.push(0);
	h[0] = 0;
	for(int i = 1;i <= n;i++){
		int pre = -1;
		while(!st.empty() && h[st.top()] > h[i]){
			if(pre != h[st.top()])
				l2[i].push_back(st.top());
			pre = h[st.top()];
			st.pop();
		}
		l2[i].push_back(st.top());
		st.push(i);
	}
	ans[1] = 0;
	for(int i = 2;i <= n;i++){
		ans[i] = ans[i - 1] + 1;
		for(auto x : l1[i]){
			if(x != 0) ans[i] = min(ans[x] + 1,ans[i]);
		}
		for(auto x : l2[i]){
			if(x != 0) ans[i] = min(ans[x] + 1,ans[i]);
		}
	}
	cout << ans[n] << '\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [H - Skyline Photo](https://vjudge.net.cn/problem/CodeForces-1483C)

> 一个建筑有高度 $h_i$ 与美感 $b_i$ 两个值
>
> 我们可以将连续的建筑拍成一张照片，这张照片的美感就是最矮的建筑的美感
>
> 要求使得所有建筑都属于且仅属于一张照片
>
> 问美感之和最大值为多少

##### 思路：

* 我们设 $dp_i$ 代表 $[1,i]$ 能得到的最大美感
* 那么我们有 $dp$ 转移方程 $dp_i=\max\set{dp_j+b_k|h_k=\min\set{h_{j+1},h_{j+2}...h_i},1\leq j< i}$
* 我们考虑在 $i$ 位置， $b_k$ 的取值
* 如果我们取的 $j$ 的位置之后到 $i$ 还没有遇到比 $h_i$ 小的 $h$ ，那么我们就取最新的 $h_i$ ，那么我们用单调栈就可以得到这个范围 $[l,i-1]$ ，这个范围内求 $dp_j$ 的最大值，加上 $b_k$ 即可
* 否则，我们的 $b_k$ 肯定是取不到的，那么我们直接将前面的 $dp_l$ 继承过来，意思就是本来是以 $l$ 结尾，将这个结尾拓展到 $i$ 位置，因为 $i$ 位置高度比 $l$ 高，所以取的数还是前面的

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
const int MAXN = 5e5 + 10;
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
			ll ma = -INF;
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
	tr.build(1,n,1);
	vector<ll> h(n + 1);
	vector<ll> b(n + 1);
	vector<ll> ans(n + 1);
	stack<int> st;
	st.push(0);
	h[0] = -inf;
	ll mi = inf,mii = -inf;
	for(int i = 1;i <= n;i++) cin >> h[i];
	for(int i = 1;i <= n;i++){
		cin >> b[i];
		if(mi > h[i]){
			mi = h[i];
			mii = b[i];
		}
		ans[i] = mii;
		while(!st.empty() && h[st.top()] > h[i]){
			st.pop();
		}
		if(i != 1)
			ans[i] = max(tr.findma(max(st.top(),(ll)1),i-1,1) + b[i],ans[i]);
		if(st.top() != 0)
			ans[i] = max(ans[i],ans[st.top()]);
		tr.turn(i,i,1,ans[i]);
		st.push(i);
	}
	cout << ans[n] << '\n';
}

signed main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [I - Permutation recovery](https://vjudge.net.cn/problem/CodeForces-1158C)

> 要求一个排列
>
> 已知这个排列部分位置比它大的在它后面的最近位置的数的位置 $next_i$
>
> 未知为 $next_i=-1$
>
> 给出该排列的其中一个可能性，或者输出不可能

##### 思路：

* 如果我们将 $(i,next_i)$ 作为一个区间，那么有一个结论，即如果区间非端点相交，那么该排列不存在
* 利用单调栈的性质，我们发现 $(i,next_i)$ 就是 $(i,st.top())$ ，因此就是 $(次大值位置，最大值位置)$
* 假设区间相加 $(l_1,r_1),(l_2,r_2),l_1<l_2<r_1<r_2$ ，那么有 $a[r_1]>a[l_1]>a[l_2],a[r_2]>a[l_2]>a[r_1]$
* 但是区间 $l_1,r_1$ 的最大与次大值为 $a[l_1],a[r_1]$ ，内部的 $a[l_2]$ 应该小于 $a[l_1]$ ，因此矛盾
* 那么怎么判断区间是否不相交呢
* 我们给区间排序，根据左端点把右端点加入线段树，询问就问左区间在 $[l+1,r-1]$ 内的 $r$ 的最大值是否大于 $r$ ，如果有，说明有区间相交
* 然后是构造，我们发现对 $next_i = -1$ 的，我们将其 $next_i=i+1$ ，排列是否存在是不会改变的
* 因为这个区间长度为 $2$ ，无论如何都是端点相交
* 我们想， $a[next_i]>a[i]$ ，因此我们先让后面的数赋大的数，具体的，给 $next_i$ 较大的赋值，因为它们能跑的比较远，中间的数肯定比它们小
* 然后对于 $next_i$ 相同的位置来说，肯定是前面大后面小，否则就不能直接跳到 $next_i$ 了

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
	vector<ll> sum,ma,mi,ladd,lturn,l,r;
	vector<bool> fadd,fturn;
		
//	ll sum[MAXN*4];//代表的和
//	ll ma[MAXN*4];//代表的最大值
//	ll mi[MAXN*4];//代表的最小值
//	bool fadd[MAXN*4];//是否启用标签
//	ll ladd[MAXN*4];//加法标签
//	bool fturn[MAXN*4];
//	ll lturn[MAXN*4];//变换标签
//	ll l[MAXN*4],r[MAXN*4];
	void init(int n){
		sum = vector<ll>(n*4 + 10,0);
		ma = vector<ll>(n*4 + 10,0);
		mi = vector<ll>(n*4 + 10,0);
		ladd = vector<ll>(n*4 + 10,0);
		lturn = vector<ll>(n*4 + 10,0);
		l = vector<ll>(n*4 + 10,0);
		r = vector<ll>(n*4 + 10,0);
		fadd = vector<bool>(n*4 + 10,0);
		fturn = vector<bool>(n*4 + 10,0);
	}
	void build(int _l,int _r,int x)
	{
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
	vector<pair<int,int>> nex(n + 1);
	vector<pair<int,int>> tem;
	for(int i = 1;i <= n;i++){
		cin >> nex[i].first;
		if(nex[i].first == -1) nex[i].first = i + 1;
		tem.push_back(make_pair(i,nex[i].first));
		nex[i].first = -nex[i].first;
		nex[i].second = i;
	}
	bool f = 1;
	tr.init(n);
	for(int i = 0;i < n;i++){
		a[tem[i].first] = max(a[tem[i].first],tem[i].second);
	}
	tr.build(1,n,1);
	for(int i = 0;i < n;i++){
		if(tem[i].first == tem[i].second - 1) continue;
		else{
			if(tr.findma(tem[i].first + 1,tem[i].second - 1,1) > tem[i].second) f = 0;
		}
	}

	if(f){
		int ptop = n;
		sort(nex.begin() + 1,nex.end());
		vector<int> ans(n + 1);
		for(int i = 1;i <= n;i++){
			ans[nex[i].second] = ptop;
			ptop--;
		}
		for(int i = 1;i <= n;i++) cout << ans[i] << ' ';
		cout << '\n';
	}else{
		cout << -1 << '\n';
	}
	for(int i = 1;i <= n;i++) a[i] = 0;
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## [J - 滑动窗口 /【模板】单调队列](https://vjudge.net.cn/problem/洛谷-P1886)

* 板

## [K - 求m区间内的最小值](https://vjudge.net.cn/problem/洛谷-P1440)

* 同

## [L - 质量检测](https://vjudge.net.cn/problem/洛谷-P2251)

* 单调队列典题

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

void test(){
	int n,m;cin >> n >> m;
	deque<pair<int,int>> dqu;
	vector<int> a(n + 1);
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		while(!dqu.empty() && dqu.front().first <= i - m) dqu.pop_front();
		while(!dqu.empty() && dqu.back().second > a[i]) dqu.pop_back();
		dqu.push_back(make_pair(i,a[i]));
		if(i >= m)
			cout << dqu.front().second << '\n';
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

## [M - Mowing the Lawn G](https://vjudge.net.cn/problem/洛谷-P2627)

> 不能连续取超过 $k$ 个元素，求总和的最大值

##### 思路：

* 转化一下，变成不能连续不取 $k$ 个元素，也就是一个位置如果不取，那么前一个不取的位置必须在 $i-k$ 范围内
* 那么问题就变成了每个位置只能从 $[i-k,i]$ 转移，求总和的最小值
* 而求区间最小值，我们用单调队列即可

```cpp
// xxc2
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;
#define int long long

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

void test(){
	int n,k;cin >> n >> k;
	k++;
	deque<pair<int,int>> deq;
	vector<int> a(n + 1);
	int ans = 0,sum = 0;
	for(int i = -k;i <= 0;i++){
		deq.push_back(make_pair(i,0));
	}
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		sum += a[i];
		while(!deq.empty() && deq.front().first < i - k) deq.pop_front();
		int no = deq.front().second + a[i];
		while(!deq.empty() && deq.front().first <= i - k) deq.pop_front();
		while(!deq.empty() && deq.back().second > no) deq.pop_back();
		deq.push_back(make_pair(i,no));
	}
	cout << sum - deq.front().second << '\n';
}

signed main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

* 总结详见[单调栈与单调队列](C:\Users\Administrator\Desktop\markdown\c++\算法\数据结构\单调栈与单调队列.md)
