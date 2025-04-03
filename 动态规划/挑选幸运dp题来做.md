## [D. Sorting By Multiplication](https://codeforces.com/contest/1861/problem/D)

### *1800

### 题意：给一个正整数序列a，你可以挑选任意区间乘上任意整数（可以是负数），问使得a严格升序的最小操作次数

### 思路：左边全是负数，右边全是正数，每遇到一个不满足严格升序的数，计数+1，如果左边有负区间，计数+1

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 2e5 + 10;

int a[N];

int l[N],r[N];

void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++) cin >> a[i];
	l[1] = r[n] = 0;
	for(int i = 2;i <= n;i++){
		l[i] = l[i - 1];
		if(a[i - 1] <= a[i])
			l[i]++;
	}
	for(int i = n - 1;i >= 1;i--){
		r[i] = r[i + 1];
		if(a[i + 1] <= a[i])
			r[i]++;
	}
	int ans = r[1];
	for(int i = 2;i <= n;i++){
		ans = min(ans,l[i - 1] + r[i] + 1);
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

## [E. Speedrun](https://codeforces.com/contest/1863/problem/E)

### *2100

### 题意：有依赖的图，要完成所有节点，给出每个节点在循环中可完成的时间，每个节点只能在循环中的特定时间完成，问从完成第一个点到完成最后一个点的最小时间

### 思路：依赖图要完成所有节点，最少需要的时间为最长路，那么反向遍历可以求出从所有终点到起点的最长路，在从小到大枚举向后移动k所需要的时间即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 2e5 + 10;
const ll INF = 1e18;

int k;

ll ans[N],h[N],in[N],ot[N];

vector<int> ed[N];

void dfs(int x){
	for(int y : ed[x]){
		int w = h[x] - h[y];
		if(w < 0) w += k;
		if(x == 0) w = 0;
		ans[y] = max(ans[y],ans[x] + w);
		in[y]--;
		if(in[y] == 0)
			dfs(y);
	}
}

struct point{
	int h,i;
	bool operator<(const point & other)const{
		return h < other.h;
	}
}p[N];

void test(){
	int n,m;cin >> n >> m >> k;
	for(int i = 0;i <= n;i++){
		ans[i] = 0;
		ot[i] = 0;
		ed[i].clear();
	}
	for(int i = 1;i <= n;i++){
		cin >> h[i];
	}
	for(int i = 1;i <= m;i++){
		int a,b;cin >> a >> b;
		ed[b].push_back(a);
		in[a]++;
		ot[b]++;
	}
	int ptop = 0;
	for(int i = 1;i <= n;i++){
		if(in[i] == 0){
			ed[0].push_back(i);
			in[i] = 1;
		}
		if(ot[i] == 0){
			p[++ptop] = {h[i],i};
		}
	}
	dfs(0);
	ll mx = 0,res = 0;
	sort(p + 1,p + 1 + ptop);
	for(int i = 1;i <= ptop;i++){
		mx = max(h[p[i].i] + ans[p[i].i],mx);
	}
	for(int i = 1;i <= ptop;i++){
		if(i == 1) res = mx - h[p[i].i];
		else res = min(mx - h[p[i].i],res);
		mx = max(mx,h[p[i].i] + ans[p[i].i] + k);
	}
	cout << res << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## [D. Trees and Segments](https://codeforces.com/contest/1858/problem/D)

### 题意：给一个01序列，最多能翻转k次，问对于每一个$1 \leq a \leq n$来说，$al_0+l_1$的最大值为多少，其中$l_0,l_1$代表连续0与连续1的最大长度

### 思路：用$O(n^2)$来枚举连续的1的区间，那么用掉的翻转次数就时区间内0的数量，用前缀和来维护即可

### 因为取了1的区间，那么考虑在这个条件下0的最大长度为多少，那么要么在这个区间前面，要么在后面，那么从前与从后dp得出最大长度即可

### 用$ldp[i][k]$带表从左边开始到i，至多翻转k次，0的最大长度

### 因为最大长度的区间是连续的，因此我们先得出从i点向前一直翻0至多翻转k次的最大长度，再用二维最大前缀和维护即可得出$ldp$，同理得出$rdp$

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 3e3 + 10;

bool f[N];

ll l[N][N],r[N][N];//以这个点为结尾，翻转k个最长能向前延长多少1

ll ans[N];//在l0为i时l1的最大值

void init(int n){
	for(int i = 0;i <= n + 1;i++){
		f[i] = 0;
		ans[i] = 0;
		for(int j = 0;j <= n + 1;j++)
			l[i][j] = r[i][j] = 0;
	}
}

void test(){
	int n,k;cin >> n >> k;

	init(n);

	string s;cin >> s;
	for(int i = 0;i < n;i++) f[i + 1] = s[i] - '0';

	for(int i = 1;i <= n;i++){
		if(f[i]){
			for(int j = 0;j <= k;j++)
				l[i][j] = l[i - 1][j] + 1;
		}
		else{
			l[i][0] = 0;
			for(int j = 1;j <= k;j++)
				l[i][j] = l[i - 1][j - 1] + 1;
		}
	}

	for(int i = 1;i <= n;i++){
		for(int j = 0;j <= k;j++){
			if(j == 0) l[i][j] = max(l[i - 1][j],l[i][j]);
			else l[i][j] = max(l[i][j],max(l[i - 1][j],l[i][j - 1]));
		}
	}

	for(int i = n;i >= 1;i--){
		if(f[i]){
			for(int j = 0;j <= k;j++)
				r[i][j] = r[i + 1][j] + 1;
		}
		else{
			r[i][0] = 0;
			for(int j = 1;j <= k;j++)
				r[i][j] = r[i + 1][j - 1] + 1;
		}
	}

	for(int i = n;i >= 1;i--){
		for(int j = 0;j <= k;j++){
			if(j == 0) r[i][j] = max(r[i + 1][j],r[i][j]);
			else r[i][j] = max(r[i][j],max(r[i + 1][j],r[i][j - 1]));
		}
	}

	int lim = 0;

	for(int i = 1;i <= n;i++){
		int num = 0;
		for(int j = i;j <= n;j++){
			if(f[j]) num++;
			if(num > k) break;
			lim = max(lim,j - i + 1);
			ans[j - i + 1] = max(ans[j - i + 1],max(l[i - 1][k - num],r[j + 1][k - num]));
		}
	}

	for(int i = 1;i <= n;i++){
		ll ma = l[n][k];
		for(int j = 1;j <= lim;j++){
			ma = max(ma,i * j + ans[j]);
		}
		cout << ma << ' ';
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

## [E1. PermuTree (easy version)](https://codeforces.com/contest/1856/problem/E1)

### 题意：n点树，给每个节点排序，问$a_u \leq a_{lca(u,v)} \leq a_v$的对数最大值为

### 思路：无论子树怎么排序，父节点都可以在不改变子树内部排序关系的情况下插入任意位置，那么枚举父节点在子树中的排序位置，然后考虑多个子树即可，因为枚举到$(sz[x]-sz[y])*sz[y]$，复杂度为$O(n^2)$

```c++
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 5e3 + 10;

vector<int> son[N];

int sz[N];

int dp[N][N];
int tem[N];

void dfs(int x){
	sz[x] = 1;
	dp[x][0] = 0;
	for(auto y : son[x]){
		dfs(y);
		for(int i = 0;i <= sz[x] + sz[y];i++) tem[i] = 0;
		for(int i = 0;i <= sz[x] - 1;i++){
			int l = sz[x] - 1 - i,r = i;
			for(int j = 0;j <= sz[y];j++){
				tem[i + j] = max(dp[x][i] + dp[y][0] + j * l + (sz[y] - j) * r,tem[i + j]);
			}
		}
		for(int i = 0;i <= sz[x] + sz[y] - 1;i++)
			dp[x][i] = tem[i];
		sz[x] += sz[y];
	}
	for(int i = sz[x] - 1;i >= 0;i--) dp[x][i] = max(dp[x][i],dp[x][i + 1]);
}

void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++){
		int x;cin >> x;
		son[x].push_back(i);
	}
	dfs(1);
	cout << dp[1][0] << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```
