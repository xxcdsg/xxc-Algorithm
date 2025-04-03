## A - A-B 数对

## 题意：找$A-B=C$的对数，已知$C$，那么就是找对于每个数就是找$C+B$的数量

## 思路：二分找位置最大的$C+B$与位置最小的$C+B$，枚举每个$B$即可

## 用map可能也行？

### map

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int a[N];
map<int,int> mp;
void test(){
	int n,c;cin >> n >> c;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		mp[a[i]]++;
	}
	int ans = 0;
	for(int i = 1;i <= n;i++)
		ans += mp[a[i] + c];
	cout << ans << endl;
}
signed main()
{
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

### 二分

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int a[N];
void test(){
	int n,c;cin >> n >> c;
	for(int i = 1;i <= n;i++)
		cin >> a[i];
	sort(a + 1,a + 1 + n);
	int ans = 0,num;
	for(int i = 1;i <= n;i++){
		int aim = c + a[i];
		int l = lower_bound(a + 1,a + 1 + n,aim) - a;
		int r = upper_bound(a + 1,a + 1 + n,aim) - a - 1;
		if(l != n + 1 && aim == a[l]){
			num = r - l + 1;
			ans += num;
		}
	}
	cout << ans << endl;
}
#undef int
int main()
{
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## B - 烦恼的高考志愿

## 题意：找最近的，水

## 思路：水

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAX = 110000;
long long b[MAX],a[MAX],sum;
int n,m;
void find(int x)
{
	int begin = 1,end = m;
	while(begin <= end)
	{
		int mid = (begin + end) / 2;
		if(x > b[mid])
		begin = mid + 1;
		else
		end = mid - 1;
	}
	if(end >= 1 && begin <= m)
	sum += min(abs(b[end] - x),abs(b[begin] - x));
	else if(end < 1)
	sum += abs(b[begin] - x);
	else
	sum += abs(b[end] - x);
}
int main()
{
	cin >> m >> n;
	for(int i = 1;i <= m;i++)
	cin >> b[i];
	for(int i = 1;i <= n;i++)
	cin >> a[i];
	sort(b + 1,b + m + 1);
	for(int i = 1;i <= n;i++)
	find(a[i]);
	cout << sum;
}
```

## C - 银行贷款

## 题意：给出借款，每个月偿还的钱数，偿还月数，问月利率

## 思路：二分答案

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
int w,ww,m;
bool pd(double x){
	double tem = w;
	for(int i = 1;i <= m;i++){
		tem *= (1 + x / 100);
		tem -= ww;
	}
	return tem >= 0;
}
void test(){
	cin >> w >> ww >> m;
	double begin = 0,end = 500;
	while((end - begin) > 0.01){
		double mid = (end + begin) / 2;
		if(pd(mid)) end = mid;
		else begin = mid;
	}
	printf("%.1lf",begin);
}
signed main()
{
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## D - 数列分段 Section II

## 题意：数列分n段，其中的最大和的最小值

## 思路：和青蛙过河一个思路，二分答案，贪心判断

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e5 + 10;
int n,m;
int a[N];
bool pd(int x){
	int no = 0,num = 1;
	for(int i = 1;i <= n;i++){
		if(no + a[i] > x){
			num++;
			no = a[i];
			if(a[i] > x)
				return 0;
		}else{
			no += a[i];
		}
	}
	return num <= m;
}
void test(){
	cin >> n >> m;
	for(int i = 1;i <= n;i++)
		cin >> a[i];
	int l = 1,r = 1e9;
	while(l <= r){
		int mid = (l + r) / 2;
		if(pd(mid)){
			r = mid - 1;
		}else
			l = mid + 1;
	}
	cout << l << endl;
}
signed main()
{
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## E - 丢瓶盖

## 同上，懂得都懂

```c++
#include<bits/stdc++.h>
using namespace std;//
#define int long long
const int N = 1e5 + 10;
int dis[N];
int a,b;
bool check(int x){
	int no = dis[1],num = 1;
	for(int i = 2;i <= a;i++){
		if(dis[i] - no >= x){
			no = dis[i];
			num++;
		}
	}
	if(num < b)
		return 0;
	else	
		return 1;
}
void test(){
	cin >> a >> b;
	for(int i = 1;i <= a;i++){
		cin >> dis[i];
	}
	sort(dis + 1,dis + 1 + a);
	int l = 1,r = 1e9;
	while(l <= r){
		int mid = (l + r) / 2;
		if(check(mid)){
			l = mid + 1;
		}else{
			r = mid - 1;
		}
	}
	cout << r << endl;
}
#undef int
int main()
{
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## F - Almost Increasing Subsequence

## 题意：不包含$a_i\geq a_{i+1} \geq a_{i+2}$,有q次询问，每次询问$[l,r]$内最多取几个满足条件

## 思路：取两个，第三个如果与前两个满足上面的关系就用它替换前一个，这样从前往后做一遍后，有一些为开始点，即满足$a_i>a_{i-1}$，如果从开始点开始，那么就是前缀和之差+1

## 如果不是从开始点开始，并且下一个点也为第二个点，那么在新的取法中这两个点都可以取，因此为前缀和之差+2

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
pair<int,int> dp[N];
void test(){
	int n,q;cin >> n >> q;
	pair<int,int> pa = {0,0};
	dp[1].first = 1;
	dp[1].second = 1;
	cin >> pa.first;
	for(int i = 2;i <= n;i++){
		int x;cin >> x;
		if(pa.second){
			if(pa.second >= x){
				dp[i].first = dp[i - 1].first;
				dp[i].second = 2;
				pa.second = x;
			}else{
				pa.second = 0;
				pa.first = x;
				dp[i].first = dp[i - 1].first + 1;
				dp[i].second = 1;
			}
		}else{
			dp[i].first = dp[i - 1].first + 1;
			if(pa.first >= x){
				pa.second = x;
				dp[i].second = 2;
			}else{
				pa.first = x;
				dp[i].second = 1;
			}
		}
	}
	while(q--){
		int x,y;cin >> x >> y;
		if(dp[x].second == dp[x + 1].second && x != y && dp[x].second == 2)
			cout << dp[y].first - dp[x].first + 2 << endl;
		else
			cout << dp[y].first - dp[x].first + 1 << endl;
	}
}
signed main()
{
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## G - Living Sequence

## 题意：第k个没有4的数字

## 思路：转换为9进制

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
void f(ll k){
	if(k == 0) return;
	else{
		f(k/9);
		int t = k % 9;
		if(k%9 >= 4)
			cout << k%9 + 1;
		else
			cout << k%9;
	}
}
int main()
{
	int t;cin >> t;
	while(t--){
		ll k;cin >> k;
		f(k);
		cout << endl;
	}
}
```

## H - Climbing the Tree

## 题意：树高为h，但你一开始不知道，有两类数据，1给出n天明天早上向上爬a，晚上向下滑动b才到树顶，如果不矛盾就接受该数据，否则忽略

## 2给出a、b，问需要几天才能到树顶，如果不确定输出-1

## 思路：每次的1数据，均可得到一个最高可能高度与最低可能高度，如果与原本的有交集就取，否则不取

## 对于2数据，针对最高可能高度与最第可能高度求天数，如果相同就说明确定

## 具体求法：最高为$(n-1)(a-b)+a$，最低为$max(0,(n-2)(a-b)+a+1)$

## 天数为$ceil((h-a)/(a-b))+1$

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
int main()
{
	int t;cin >> t;
	while(t--){
		int q;cin >> q;
		ll mi = 0,ma = 1e18;
		for(int i = 1;i <= q;i++){
			int op;cin >> op;
			if(op == 1){
				ll a,b,n;cin >> a >> b >> n;
				ll maa = a + (a-b)*(n-1);
				ll mii = a + (a-b)*(n-2) + 1;
				if(n == 1)
					mii = 0;
				if(max(mii,mi) <= min(maa,ma))
				{
					ma = min(maa,ma);
					mi = max(mii,mi);
					cout << 1 << ' ';
				}
				else
					cout << 0 << ' ';
			}
			else{
				ll a,b;cin >> a >> b;
				ll n1 = max(1.0,ceil((ma - b)/(1.0*a - b)));
				ll n2 = max(ceil((mi - b)/(1.0*a - b)),1.0);
				if(n1 == n2)
					cout << n1 << ' ';
				else
					cout << -1 << ' ';
			}
		}
		cout << endl;
	}
}
```

## I - Thwarting Demonstrations

## 题意：~~题目很怪，模板很帅~~，k天，每天派出$[l,r]$的人出勤，每次均会派$[l,r]$之和最大的出去，派过一次的区间不能再派，问派出的最小的区间和的最大值为多少

## 思路：转换为求第k大的区间和

## 离散化，二分，树状数组，前缀和

## 对于特定的数据，要看它是第几大的就把比它更大的找出来即可，也就是找出所有$pre[i]-pre[j]>x,(i<j)$，从前往后找，然后树状数组加入即可

## 随便写了一个类

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
#define lowbit(x) (x&-x)
#define pb(x) push_back(x)
const int N = 1e5 + 10;

class K_range{
	vector<int> pre,tem;
	int tr[N],l = -1e18,r = 1e18,n,k,a[N],aa[N],nn;
	void add(int x){while(x <= nn) tr[x]++,x += lowbit(x);}
	int ask(int x){
		int ans = 0;
		while(x > 0) ans += tr[x],x -= lowbit(x);
		return ans;
	}
	bool check(int x){
		int ans = 0;
		memset(tr,0,sizeof(tr));
		for(int i = 1;i <= n;i++){
			if(aa[i] > x) ans++;
			int ra = upper_bound(tem.begin(),tem.end(),aa[i] - x) - tem.begin();
			if(aa[i] - x == tem[ra - 1])
				ans += ask(ra - 1);
			else
				ans += ask(ra);
			add(a[i]);
		}
		return ans >= k;//排名，不成立,需要让x变大
	}
	void Discretization(int& n,vector<int>& ve,int* data,int* ra){//长度，离散化数组，数据，排名
		sort(ve.begin(),ve.end());
		ve.erase(unique(ve.begin(),ve.end()),ve.end());
		for(int i = 1;i <= n;i++) ra[i] = lower_bound(ve.begin(),ve.end(),data[i]) - ve.begin() + 1;
		n = ve.size();
	}
	public:
	int _main(){
		cin >> n >> k;
		for(int i = 1;i <= n;i++){
			cin >> a[i];
			a[i] += a[i - 1];
			aa[i] = a[i];
			tem.push_back(a[i]);
		}
		nn = n;
		Discretization(nn,tem,aa,a);
		int an;
		while(l <= r){
			int mid = (l + r) / 2;
			if(check(mid)) l = mid + 1;
			else{//成立，但要更小
				r = mid - 1;
				an = mid;
			}
		}
		return an;
	}
};

signed main()
{
	static K_range p;
	cout << p._main() << endl;
}
```

## J - Complete The Graph

## 题意：n个点，给出从s到t的最短路长度为L，然后给出m条边，有一些不知道长度，问一种可能的图

## 思路：先将已经有长度的边连上，如果已经得出最短路为L了，那么就让其他不知道的边为INF即可

## 如果得出比L小说明不成立

## 否则一个一个加不知道长度的边，令那些边的长度为1，如果将最短路更新得比L小，说明我们加的那条边加小了，加上L-x即可，然后退出

## 如果将所有边都假设为1都不行，那肯定就不成立

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e3 + 10,M = 1e4 + 10,INF = 1e18;
int n,m,l,s,t;
pair<pair<int,int>,int> _ed[M];
vector<pair<int,int>> ed[N];
bool use[N] = {0};
struct point{
        int u,dis;
        bool operator <(const point &other)const{
            return dis > other.dis;
        }
    };
int dij(int u,int v){
	priority_queue<point> pqu;
	memset(use,0,sizeof(use));
	pqu.push({s,0});
	while(!pqu.empty()){
		int u = pqu.top().u,dis = pqu.top().dis;
		pqu.pop();
		if(u == t)
			return dis;
		if(use[u])
			continue;
		use[u] = 1;
		for(auto e:ed[u]){
			if(use[e.first])
				continue;
			pqu.push({e.first,e.second + dis});
		}
	}
	return -1;
}
void test(){
	cin >> n >> m >> l >> s >> t;
	for(int i = 1;i <= m;i++){
		int u,v,len;
		cin >> u >> v >> len;
		_ed[i] = {{u,v},len};
		if(len != 0){
			ed[u].push_back({v,len});
			ed[v].push_back({u,len});
		}
	}
	bool f = 0;
	int len = dij();
	if(len == l)
		f = 1;
	if(len != -1 && len < l)
		cout << "NO" << endl;
	else{
		for(int i = 1;i <= m && !f;i++){
			int u = _ed[i].first.first,v = _ed[i].first.second;
			len = _ed[i].second;
			if(len != 0)
				continue;
			_ed[i] = {{u,v},1};
			ed[u].push_back({v,1});
			ed[v].push_back({u,1});
			int llen = dij();
			if(llen != -1 && llen <= l){
				_ed[i] = {{u,v},l - llen + 1};
				f = 1;
				break;
			}
		}
		if(f){
			cout << "YES" << endl;
			for(int i = 1;i <= m;i++){
				int u,v;
				u = _ed[i].first.first,v = _ed[i].first.second;
				len = _ed[i].second;
				if(len == 0)
					len = INF;
				cout << u << ' ' << v << ' ' << len << endl;
			}
		}else
			cout << "NO" << endl;
	}
}
signed main()
{
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## L - Red-Blue Operations (Hard Version)

## 题意：一开始全是红球，n个球，k次操作，每次操作可以让红球变成蓝球并加上i，或者让蓝球变成红球然后减去i

## 思路：就是让最后的n个都为+，如果剩下奇数个就是n-1个，剩下的除以二为-1的数量，计算最小即可

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;

int dp[N],a[N];

void test(){
	int n,q;cin >> n >> q;
	for(int i = 1;i <= n;i++) cin >> a[i];
	sort(a + 1,a + 1 + n);
	dp[0] = 1e18;
	a[n + 1] = 1e18;
	for(int i = 1;i <= n;i++){
		if(i != n)
			dp[i] = min(dp[i - 1],min(a[i] + 1,a[i + 1]) + n - i);
		else
			dp[i] = min(dp[i - 1],min(a[i] + 1,a[i + 1]) + n - i);
	}
	int sum = 0;
	for(int i = 1;i <= n;i++)
		sum += i + a[i];
	for(int i = 1;i <= q;i++){
		int x;cin >> x;
		if(x <= n)
			cout << dp[x] + x - n << ' ';
		else{
			
			int all,mi;
			if((x - n) % 2 == 0){
				all = n*(x-n) - (x-n)/2 + sum;
				mi = dp[n]+x-n;
			}else{sum--;
				all = (n-1)*(x-n) - (x-n + 1) / 2 + sum;
				mi = min(dp[n - 1] + x - n,a[n]);
				sum++;
			}
			if(mi*n <= all)
				cout << mi << ' ';
			else
				cout << (int)(floor((1.0*all) / n)) << ' ';
		}
	}
}
signed main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```
