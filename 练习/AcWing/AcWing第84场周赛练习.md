# AcWing第84场周赛练习
## 1.[最大数量](https://www.acwing.com/problem/content/4791/)
* 求值相等的元素的最大数量，水
```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
pair<int,int> ti[MAXN];
int main()
{
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
	{
		cin >> ti[i].first >> ti[i].second;
	}
	sort(ti + 1,ti + 1 + n);
	int ma = 1;
	int tem = 1;
	for(int i = 2;i <= n;i++)
	{
		if(ti[i] == ti[i - 1])
		ma = max(++tem,ma);
		else
		tem = 1;
	}
	cout << ma;
}
```
## 2.[前缀和序列](https://www.acwing.com/problem/content/4792/)
* 求一个区间内的和，典型前缀和与和差题
```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 1e5 + 10;
ll a[MAXN] = {0},b[MAXN] = {0};
int main()
{
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
	{
		cin >> a[i];
		b[i] = a[i];
		a[i] += a[i - 1];
	}
	sort(b + 1,b + 1 + n);
	for(int i = 1;i <= n;i++)
	b[i] += b[i - 1];
	int m;cin >> m;
	for(int i = 1;i <= m;i++)
	{
		int type,l,r;cin >> type >> l >> r;
		if(type == 1)
		cout << a[r] - a[l - 1] << endl;
		else
		cout << b[r] - b[l - 1] << endl;
	}
}
```
## 3.[买可乐](https://www.acwing.com/problem/content/description/4793/)
* 模拟题，水
```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int c,d,n,m,k;cin >> c >> d >> n >> m >> k;
	if(k >= n * m)
	cout << 0;
	else
	{
		int need = n * m - k;
		int output = 0;
		if(c/n<d)
		{
			while(need >= n)
			{
				need -= n;
				output += c;
			}
			output += min(need * d,c);
			cout << output;
		}
		else
		cout << need * d;
	}
}
```