# [CUGBACM22级春季学期训练#2](https://vjudge.net/contest/545463#status/xxcdsg/-/0/)

## [A - Middle  Letter](https://vjudge.net/problem/AtCoder-abc266_a)

* 题意：输出最中间的字符
* 思路：水

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	string str;cin >> str;
	cout << str[(str.size() - 1) / 2];
}
```

## [B - Modulo Number](https://vjudge.net/problem/AtCoder-abc266_b)

* 题意：输入$n$，求$x$使$n-x$为$998244353$的倍数（x在$[0,998244353-1]$间）

* 思路：$n-x=0\pmod{998244353}$
* $x=n\pmod{998244353}$
* 因为n有可能是负数，所以n求余后还要加上998244353再求余

```c++
#include<bits/stdc++.h>
using namespace std;
const int p = 998244353;
#define ll long long
int main()
{
	ll n;cin >> n;
	cout << (n % p + p) % p;
}
```

## [C - Convex Quadrilateral](https://vjudge.net/problem/AtCoder-abc266_c)

* 题意：判断四边形凹凸
* 思路：如果是凸图形，相对的两个点一定在另外两个连线的两边
* 设直线为$ax+b=y$，$a=\frac{y_1-y_2}{x_1-x_2}$

* 那么$b=y_1-ax_1$

* 特判如果$x1=x2$，那么直线就为$x=x_1$

```c++
#include<bits/stdc++.h>
using namespace std;
double a[5][3];
bool f(int x,int y,int xx,int yy)
{
	double aa,bb = 1,cc;
	if(a[xx][1] == a[yy][1])
	{
		bb = 0;
		aa = 1;
		cc = -a[xx][1];
	}
	else
	{
		aa = (a[yy][2] - a[xx][2])/(a[xx][1] - a[yy][1]);
		cc = -a[xx][2] - aa*a[xx][1];
	}
	double ans1 = aa*a[x][1] + bb*a[x][2] + cc,ans2 = aa*a[y][1] + bb*a[y][2] + cc;
	if(ans1*ans2 < 0)
	return 1;
	else
	return 0;
}
int main()
{
	for(int i = 1;i <= 4;i++)
	for(int j = 1;j <= 2;j++)
	cin >> a[i][j];
	if(f(1,3,2,4) && f(2,4,1,3))
		cout << "Yes";
	else
		cout << "No";
}
```

## [D - Snuke Panic (1D)](https://vjudge.net/problem/AtCoder-abc266_d)

* 题意：一个人从洞0，时间为0开始，每一段时间可以移动到相邻的洞，给出洞口出现蛇的时间和洞口和价值，求这个人能得到的最大价值
* 思路：动态规划，每个时间每个洞口最大能得到的价值只和上一个时间三个洞口（或两个）有关，再加上这个时间洞口的蛇的价值
* 注意初始化除了洞0的最大价值为0，其他为极小值

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
#define INF -1000000000000000000
#define ll long long
ll t[MAXN],x[MAXN],a[MAXN];
ll dp[MAXN][5];
int main()
{
	int n;cin >> n;
	ll mat = 0;
	t[0] = -1;
	dp[0][1] = dp[0][2] = dp[0][3] = dp[0][4] = INF;
	for(int i = 1;i <= n;i++)
	{
		cin >> t[i] >> x[i] >> a[i];
		mat = max(t[i],mat);
	}
	int ptop = 1;
	ll ans = 0;
	for(int i = 1;i <= mat;i++)
	{
		dp[i][0] = max(dp[i - 1][0],dp[i - 1][1]);
		for(int j = 1;j <= 3;j++)
		{
			ll ma = INF;
			for(int k = j - 1;k <= j + 1;k++)
				ma = max(ma,dp[i - 1][k]);
			dp[i][j] = ma;
		}
		dp[i][4] = max(dp[i - 1][3],dp[i - 1][4]);
		while(ptop <= n && i == t[ptop])
		{
			dp[i][x[ptop]] += a[ptop];
			ptop++;
		}
	}
	for(int i = 0;i <= 4;i++)
		ans = max(ans,dp[mat][i]);
	cout << ans;
}
```

## [E - Throwing the Die](https://vjudge.net/problem/AtCoder-abc266_e)

* 题意：有丢n次骰子的机会，每丢因此都可以停止并确定这个值，求最高期望
* 思路：如果继续丢的期望更高，就取这个期望，否则取这个较大的数，从后往前直到第一次

```c++
#include<bits/stdc++.h>
using namespace std;
double dp[101];
int main()
{
	int n;cin >> n;
	dp[1] = 3.5;
	for(int i = 2;i <= n;i++)
	{
		if(dp[i - 1] >= 5)
		{
			dp[i] = (6 + 5 * dp[i - 1]) / 6;
		}
		else if(dp[i - 1] >= 4)
		{
			dp[i] = (5 + 6 + 4 * dp[i - 1]) / 6;
		}
		else
		{
			dp[i] = (4 + 5 + 6 + 3 * dp[i - 1]) / 6;
		}
	}
	printf("%.6lf",dp[n]);
}
```

## [F - Well-defined Path Queries on a Namori](https://vjudge.net/problem/AtCoder-abc266_f)

* 题意：n点n边的无向简单图，求到任意两点的简单路径是否唯一
* 思路：详见基环树

## [G - Yet Another RGB Sequence](https://vjudge.net/problem/AtCoder-abc266_g)

* 题意：给出RGB三种元素的数量r、g、b，并给出RG串的数量k，求一共有多少中可能性
* 思路：确定R和B的位置$\frac{(r+b)!}{r!b!}$然后将k个G放在r个R的后面$C(r,k)$，然后其他$g-k$的G就只能放在最前面，G的后面，B的后面了$1+k+b$，也就是$g-k$个小球放入$1+k+b$个盒子，允许盒子为空的问题，详见球盒问题，也就是$C(b+g,g-k-1)$答案就为$\frac{(r+b)!}{r!b!}C(r,k)C(b+g,g-k-1)$

```c++
#include<bits/stdc++.h>
using namespace std;
const int p = 998244353;
const int MAXN = 3e6 + 10;
#define ll long long
ll ny[MAXN];
void pre_ny()
{
    ny[1] = 1;
    for(int i = 2;i <= MAXN - 1;i++){
        ny[i] = (ll)(p - p / i)*ny[p % i] % p;//p - p / i是为了防止负数 
    }
}
ll jc(ll x)
{
	ll ans = 1;
	for(ll i = 1;i <= x;i++)
		ans = (ans * i) % p;
	return ans;
}
ll jcny(ll x)
{
	ll ans = 1;
	for(ll i = 1;i <= x;i++)
		ans = (ans * ny[i]) % p;
	return ans;
}
ll C(ll n,ll m)
{
	ll ans = 1;
	for(int i = 1;i <= n;i++)
	ans = (ans * i) % p;
	for(int i = 1;i <= m;i++)
	ans = (ans * ny[i]) % p;
	for(int i = 1;i <= n - m;i++)
	ans = (ans * ny[i]) % p;
	return ans;
}
int main()
{
	pre_ny();
	int r,g,b,k;cin >> r >> g >> b >> k;
	int M = k + b + 1,N = g - k;
	cout << ((((((jc(r+b)*jcny(r)) % p)*jcny(b)) % p )*C(r,k) % p)*C(N+M-1,M-1)) % p;
}
```

