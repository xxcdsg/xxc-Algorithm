## [E1. Doremy's Drying Plan (Easy Version)](https://codeforces.com/contest/1890/problem/E1)

### 题意：n个区间，取消两个区间使得最多元素不被任意区间包括

### 思路：两个区间，分为相离与相交，先利用前缀和得出每个点被区间选中的次数，那么相离区间的答案就是整个区域的0的数量加上选中的两个区间内所有1的数量

### 再考虑相交的情况，对于每个2点来说，其中一个区间一定是所有左端点小于等于该点，右端点最大的区间，那么只要对根据左端点排序，然后只取右端点最大的点与枚举的点考虑相交即可，当然要考虑不相交的情况。

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;
#define lowbit(x) (x&-x)

const int N = 2e5 + 10;

int n;

pair<int,int> pa[N];

struct range{
	int l,r,i;
	bool operator<(const range &other)const{
		return l < other.l;
	}
}ra[N];

int ans1[N],pre[N],num1[N],num2[N];

void test(){
	int m,k;cin >> n >> m >> k;
	int res0 = 0;
	for(int i = 1;i <= n;i++) pre[i] = num2[i] = num1[i] = 0;
	for(int i = 1;i <= m;i++){
		cin >> pa[i].first >> pa[i].second;
		ra[i] = {pa[i].first,pa[i].second,i};
		pre[pa[i].first]++;
		pre[pa[i].second + 1]--;
	}
	
	sort(ra + 1,ra + 1 + m);

	for(int i = 1;i <= n;i++){
		pre[i] += pre[i - 1];
		if(pre[i] == 0) res0++;//统计本没有区间覆盖的数量
	}
	
	for(int i = 1;i <= n;i++){
		if(pre[i] == 1) num1[i] = 1;
		if(pre[i] == 2) num2[i] = 1;
		num1[i] += num1[i - 1];
		num2[i] += num2[i - 1];
	}
	
	for(int i = 1;i <= m;i++) ans1[i] = num1[pa[i].second] - num1[pa[i].first - 1];//统计每个区间内1的数量
	
	int ans = 0;

	int mai = 1;
	for(int i = 2;i <= m;i++){
		int id1 = ra[mai].i,id2 = ra[i].i;
		int tem[4] = {pa[id1].first,pa[id1].second,pa[id2].first,pa[id2].second};
		sort(tem,tem + 4);
		if(pa[id1].second >= pa[id2].first)
			ans = max(ans,ans1[id1] + ans1[id2] + num2[tem[2]] - num2[tem[1] - 1]);
		if(ra[mai].r < ra[i].r) mai = i;
	}

	sort(ans1 + 1,ans1 + 1 + m);

	ans = max(ans,ans1[m] + ans1[m - 1]);

	cout << ans + res0 << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

