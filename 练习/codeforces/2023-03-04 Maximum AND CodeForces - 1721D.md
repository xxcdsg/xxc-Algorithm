# 2023-03-04 Maximum AND [CodeForces - 1721D](https://codeforces.com/problemset/problem/1721/D)

## 题意：两个数组任意排列对应异或得出新数组，然后对新数组按位和，求结果的最大值

### 思路：首先要确定比较高的位置是否能取到1，这就意味着两个数组内那一位的10数量一一对应，我们可以让一个数组取1的数的tag * 2+1，让取0的数tag * 2，另一个相反，这样就完成了0和1的分组，然后根据tag排序，如果对应的tag不相等就说明没有一一对应，不对数组进行更新并标记这个位取不到，否则更新数组，对下一位进行判断

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 1e5 + 10;
pair<int,ll> a[MAXN],b[MAXN];
pair<int,ll> aa[MAXN],bb[MAXN];
bool able[31];
bool cmp(pair<int,ll> x,pair<int,ll> y)
{
	return x.second < y.second;
}
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		for(int i = 0;i <= 30;i++)
		able[i] = 1;
		int ans = 0;
		for(int i = 1;i <= n;i++)
		{
			cin >> a[i].first;
			a[i].second = 0;
		}
		for(int i = 1;i <= n;i++)
		{
			cin >> b[i].first;
			b[i].second = 0;
		}
		for(int j = 29;j >= 0;j--)
		{
			int aim = (1 << j);
			for(int i = 1;i <= n;i++)
			{
				aa[i].first = a[i].first;
				bb[i].first = b[i].first;
				if(a[i].first & aim)
				{
					aa[i].second = a[i].second*2 + 1;
				}
				else
				{
					aa[i].second = a[i].second*2;
				}
				if(b[i].first & aim)
				{
					bb[i].second = b[i].second*2;
				}
				else
				{
					bb[i].second = b[i].second*2 + 1;
				}
			}
			bool f = true;
			sort(aa + 1,aa + 1 + n,cmp);
			sort(bb + 1,bb + 1 + n,cmp);
			for(int i = 1;i <= n && f;i++)
				if(aa[i].second != bb[i].second)
					f = false;
			if(f)
			{
				for(int i = 1;i <= n;i++)
				{
					a[i] = aa[i];
					b[i] = bb[i];
				}
			}
			else
			able[j] = 0;
		}
		for(int i = 0;i <= 29;i++)
		{
			if(able[i])
				ans += (1 << i);
		}
		cout << ans << endl;
	}
}
```

