# AcWing第85场周赛
## 1.[死或生](https://www.acwing.com/activity/content/problem/content/7917/)
* 统计数量，判断结果，没难度
```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int n;cin >> n;
	int x,y;
	int a[3] = {0};
	for(int i = 1;i <= n;i++)
	{
		int t,x,y;cin >> t >> x >> y;
		a[t] += x - y;
	}
	for(int i = 1;i <= 2;i++)
	if(a[i] >= 0)
	cout << "LIVE" << endl;
	else
	cout << "DEAD" << endl;
}
```
## 2.[最大价值](https://www.acwing.com/activity/content/problem/content/7918/)
* 模拟题，只要让最大的字母全排在后面就能得到最大值
```c++
#include<bits/stdc++.h>
using namespace std;

#define turn(x) (x-'a')

int main()
{
	string str;cin >> str;
	int k;cin >> k;
	int a[26];
	int ma = 0;
	for(int i = 0;i < 26;i++)
	{
		cin >> a[i];
		ma = max(a[i],ma);
	}
	int output = 0;
	for(int i = 1;i <= str.size();i++)
	output += a[turn(str[i - 1])] * i;
	for(int i = 1;i <= k;i++)
	output += ma * (i + str.size());
	cout << output;
}
```
## 3.[危险程度](https://www.acwing.com/activity/content/problem/content/7919/)
* 每倒入一种化学物质，如果该物质能够与之前倒入试管中的一种或多种物质发生反应
* 如果一堆东西能够通过反应联系起来，那么只要取一个物质，这一堆物质都可以享受到2倍的危险度加成
* 这题就转换成求并查集的元素数量
```c++
#include<bits/stdc++.h>
using namespace std;

const int MAXN = 51;
int a[MAXN];
int b[MAXN] = {0};
int n,m;

int find(int x)
{
	if(a[x] == x)
	return x;
	else
	return a[x] = find(a[x]);
}

void reload()
{
	for(int i = 1;i <= n;i++)
	a[i] = i;
}

int main()
{
	cin >> n >> m;
	reload();
	for(int i = 1;i <= m;i++)
	{
		int x,y;cin >> x >> y;
		a[find(x)] = find(y);
	}
	long long sum = 1;
	for(int i = 1;i <= n;i++)
	{
		b[find(i)]++;
	}
	for(int i = 1;i <= n;i++)
	{
		sum *= ((long long)1 << (b[find(i)] - 1));
		b[find(i)] = 1;
	}
	cout << sum;
}
```