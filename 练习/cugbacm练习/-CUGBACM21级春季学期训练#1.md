# [CUGBACM21级春季学期训练#1](https://vjudge.net/contest/545193)

## [A - camel Case](https://vjudge.net/problem/AtCoder-abc291_a)

* 题意：找大写字母在第几个
* 思路：水

```c++
#include<iostream>
#include<string>
using namespace std;
int main()
{
	string str;cin >> str;
	int end = str.size();
	for(int i = 0;i < end;i++)
		if(str[i] >= 'A' && str[i] <= 'Z')
			cout << i + 1;
}
```

## [B - Trimmed Mean](https://vjudge.net/problem/AtCoder-abc291_b)

* 题意：给出5N个数，求中间3N数的平均数
* 思路：排序，水

```c++
#include<iostream>
#include<stdio.h>
#include<algorithm>
using namespace std;
const int MAXN = 1e3 + 10;
int x[MAXN];
int main()
{
	int n;cin >> n;
	for(int i = 1;i <= 5 * n;i++)
	cin >> x[i];
	double sum = 0;
	sort(x + 1,x + 1 + 5*n);
	for(int i = n + 1;i <= 4 * n;i++)
		sum += x[i];
	printf("%.6lf",sum / (3 * n));
}
```

## [C - LRUD Instructions 2](https://vjudge.net/problem/AtCoder-abc291_c)

* 题意：判断是否走到重复位置
* 思路：用map和pair存，判断重复(本质就是哈希表)

```c++
#include<iostream>
#include<stdio.h>
#include<algorithm>
#include<string>
#include<map>
using namespace std;
const int MAXN = 4e5 + 10;
map<pair<int,int>,bool> mp;
int main()
{
	int n;cin >> n;
	string str;cin >> str;
	int x = 0,y = 0;
	mp[make_pair(0,0)] = 1;
	int i;
	for(i = 0;i < n;i++)
	{
		if(str[i] == 'R')
			x++;
		else if(str[i] == 'L')
			x--;
		else if(str[i] == 'U')
			y++;
		else if(str[i] == 'D')
			y--;
		if(mp[make_pair(x,y)] == 1)
		{
			break;
		}
		else
		mp[make_pair(x,y)] = 1;
	}
	if(i == n)
		cout << "No";
	else
		cout << "Yes";
}
```

## [D - Flip Cards](https://vjudge.net/problem/AtCoder-abc291_d)

* 题意：n张卡片，每张卡牌印着两个数字，问让每张相邻的数字都不相同的翻法
* 思路：记录确定翻每张卡片的可能性数量，通过上一张的可能性数量递推，动态规划

```c++
#include<bits/stdc++.h>
using namespace std;
const int p = 998244353,MAXN = 2e5 + 10;
#define ll long long
ll a[MAXN],b[MAXN];
ll dp[2][MAXN];
int main()
{
	int n;cin >> n;
	cin >> a[1] >> b[1];
	dp[0][1] = dp[1][1] = 1;
	for(int i = 2;i <= n;i++)
	{
		cin >> a[i] >> b[i];
		dp[0][i] = dp[1][i] = (dp[0][i - 1] + dp[1][i - 1]) % p;
		if(a[i] == a[i - 1])
			dp[0][i] = (dp[0][i] - dp[0][i - 1] + p) % p;
		if(a[i] == b[i - 1])
			dp[0][i] = (dp[0][i] - dp[1][i - 1] + p) % p;
		if(b[i] == a[i - 1])
			dp[1][i] = (dp[1][i] - dp[0][i - 1] + p) % p;
		if(b[i] == b[i - 1])
			dp[1][i] = (dp[1][i] - dp[1][i - 1] + p) % p;
	}
	cout << (dp[0][n] + dp[1][n]) % p;
}
```

## [E - Find Permutation](https://vjudge.net/problem/AtCoder-abc291_e)

* 题意：数组由1~n构成，知道第$A_{X_i}<A_{X_j}$，确定这个数组，如果无法确定就输出NO
* 思路：拓扑排序，如果没有比这个数大的，就说明这个数是最大的，然后将有关它的所有关系删去，也就是去掉这个数，下一个没有比本身大的数就是第二大的，以此类推，如果出现了两个没有比自身大的就说明无法确定

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXM = 2e5 + 10,MAXN = 2e5 + 10;
bool use[MAXN];
struct a{
	int y;
	a* nex;
}p[MAXM];
a* head[MAXN];
int ptop = 0;
int ans[MAXN];
void add(int x,int y)
{
	p[ptop].y = y;
	p[ptop].nex = head[x];
	head[x] = &p[ptop];
	ptop++;
}
int innum[MAXN],outnum[MAXN];
int main()
{
	int n,m;cin >> n >> m;
	for(int i = 1;i <= m;i++)
	{
		int x,y;cin >> x >> y;
		add(y,x);
		innum[x]++,outnum[y]++;
	}
	queue<int> qu;
	for(int i = 1;i <= n;i++)
		if(innum[i] == 0)
			qu.push(i);
	int now = n;
	bool flag = true;
	while(!qu.empty())
	{
		int x = qu.front();qu.pop();
		if(qu.size() != 0)
			flag = false;
		if(use[x])
			continue;
		ans[x] = now--;
		use[x] = 1;
		a* pp = head[x];
		while(pp != NULL)
		{
			int y = pp -> y;
			innum[y]--;
			if(innum[y] == 0)
				qu.push(y);
			pp = pp -> nex;
		}
	}
	if(now == 0 && flag)
	{
		cout << "Yes" << endl;
		for(int i = 1;i <= n;i++)
			cout << ans[i] << ' ';
	}
	else
		cout << "No" << endl; 
}
```

## [F - Teleporter and Closed off](https://vjudge.net/problem/AtCoder-abc291_f)

* 题意：给出所有城市能否到后面m个城市，确定不经过i城市，从1到n的最短走法
* 思路：计算每个城市从前往后的最短走法和从后往前的最短走法，然后分别枚举第i个城市前的城市和第i个城市后的城市，如果两个城市能走，求取最小值

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
bool able[MAXN][11];
int rdp[MAXN],ldp[MAXN];//反着走,正着走
bool ruse[MAXN],luse[MAXN];
int main()
{
	int n,m;cin >> n >> m;
	if(m == 1)
	{
		for(int i = 2;i <= n - 1;i++)
			cout << -1 << ' ';
		return 0;
	}
	for(int i = 1;i <= n;i++)
		ldp[i] = rdp[i] = MAXN;
	rdp[n] = ldp[1] = 0;
	ruse[n] = luse[1] = 1;
	for(int i = 1;i <= n;i++)
	{
		string str;cin >> str;
		for(int j = 0;j < m;j++)
			able[i][j + 1] = str[j] - '0';
		for(int t = 1;t <= m && i - t > 0;t++)
			if(luse[i - t] && able[i - t][t])
			{
				ldp[i] = min(ldp[i],ldp[i - t] + 1);
				luse[i] = 1;
			}
	}
	for(int i = n;i >= 1;i--)
	{
		for(int t = 1;t <= m && i + t <= n;t++)
			if(ruse[i + t] && able[i][t])
			{
				ruse[i] = 1;
				rdp[i] = min(rdp[i],rdp[i + t] + 1);
			}
	}
	for(int i = 2;i <= n - 1;i++)
	{
		int ans = MAXN;
		for(int j = 1;j <= m - 1 && i - j >= 1;j++)
		{
			for(int k = 1;j + k <= m && i + k <= n;k++)
				if(able[i - j][j + k])
					ans = min(ans,ldp[i - j] + rdp[i + k]);
		}
		if(ans == MAXN)
			cout << -1 << ' ';
		else
			cout << ans + 1 << ' ';
	}
}
```

## [G - OR Sum](https://vjudge.net/problem/AtCoder-abc291_g)

* 前置知识：快速傅里叶变换，卷积，循环卷积，数论变换

* 题意：
* 思路：