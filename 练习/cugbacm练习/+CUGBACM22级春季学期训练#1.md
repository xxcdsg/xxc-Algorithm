# [CUGBACM22级春季学期训练#1](https://vjudge.net/contest/545174)

## [A - 并查集](https://vjudge.net/contest/545174#problem/A)

* 并查集模板题

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e4 + 10;
int a[MAXN];
void pre(int n)
{
	for(int i = 1;i <= n;i++)
	a[i] = i;
}
int find(int x)
{
	if(a[x] == x) return a[x];
	else return a[x] = find(a[x]);
}
int main()
{
	int n,m;cin >> n >> m;
	pre(n);
	for(int i = 1;i <= m;i++)
	{
		int op,x,y;cin >> op >> x >> y;
		if(op == 1)
		{
			a[find(x)] = find(y);
		}
		else
		{
			if(find(x) == find(y))
			cout << "Y" << endl;
			else
			cout << "N" << endl;
		}
	}
}
```

## [B - manacher 算法](https://vjudge.net/contest/545174#problem/B)

* 马拉车模板题，找最长回文串
* 思路：每个字符间插入一个间隔符，使所有的回文串长度变为奇数，从左到右遍历，将左边对应的回文串长度对称过来，如果大于范围就判断能否扩展范围

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2.3e7;
char c[MAXN];
int llen[MAXN] = {0};
int main()
{
	int len = 1,ans = 0;
	char cc;
	c[0] = '*',c[1] = '#';
	while(scanf("%c",&cc) != EOF)
	{
		c[++len] = cc,c[++len] = '#';
		if(cc < 'a' || cc > 'z')
		break;
	}
	c[0] = '*';//阻挡符
	for(int t = 1,r = 0,mid = 0;t <= len;t++)
	{
		if(t <= r) llen[t] = min(llen[(mid << 1)-t],r-t+1);//范围对称过来，但是不超过范围
		while(c[t-llen[t]] == c[t+llen[t]]) llen[t]++;
		if(llen[t]+t>r) r = llen[t]+t-1,mid=t;//拓展范围
		if(llen[t]>ans) ans=llen[t];
	}
	cout << ans - 1;
}
```

## [C - 乘法逆元](https://vjudge.net/contest/545174#problem/C)

* 考察逆元，可以用线性逆元
* 线性：

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 3e6 + 10;
ll ny[MAXN];
int main()
{
	int n,p;cin >> n >> p;
	ny[1] = 1;cout << 1 << endl;
	for(ll i = 2;i <= n;i++)
	{
		ny[i] = (p - p / i) * ny[p % i] % p;
		printf("%lld\n",ny[i]);
	}
}
```

## [D - Fedya and Array](https://vjudge.net/contest/545174#problem/D)

* 题意：每个相邻数字相差一，给出极大值之和和极小值之和，构造一个最短的环
* 思路：

| 0    |      | 0    |      | 0    |
| ---- | ---- | ---- | ---- | ---- |
|      | 1    |      | 1    |      |
|      |      |      |      |      |

* 等价于

| 0    |      |      |      | 0    |
| ---- | ---- | ---- | ---- | ---- |
|      | 1    |      | 1    |      |
|      |      | 2    |      |      |

* 可见无论多少个折，它们的结果是不变的
* 因此最简单的构造法便为最大值-->最小值-->最大值-1

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	int t;cin >> t;
	while(t--)
	{
		ll x,y;cin >> x >> y;
		cout << (abs(x - y) * 2) << endl;
		ll xx = x,yy = y;
		for(;xx >= yy;xx--)
		cout << xx << ' ';
		xx = x - 1,yy = y + 1;
		for(;xx >= yy;yy++)
		cout << yy << ' ';
		cout << endl;
	}
}
```

## [E - Dora and Search](https://vjudge.net/contest/545174#problem/E)

* 题意：给出一个数列，找出一个区间让它的两个端点都不是区间的最值
* 思路：四个指针，双个数组，复制给定的数组并排序，两组指针都指向开头和结尾，如果给定数组的指针对于的值和排过序的数组的指针对于的值相等，说明是最值，就要缩小范围

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e5 + 10;
int a[MAXN];
int b[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		for(int i = 1;i <= n;i++)
		{
			cin >> a[i];
			b[i] = a[i];
		}
		sort(b + 1,b + 1 + n);
		int l = 1,r = n,ll = 1,rr = r;
		bool flag = false;;
		while(l < n)
		{
			if(a[l] == b[ll])
			{
				ll++,l++;
			}
			else if(a[l] == b[rr])
			{
				l++,rr--;
			}
			else if(a[r] == b[ll])
			{
				ll++,r--;
			}
			else if(a[r] == b[rr])
			{
				r--,rr--;
			}
			else
			{
				flag = true;
				break;
			}
		}
		if(flag)
			cout << l << ' ' << r << endl;
		else
			cout << -1 << endl;
	}
}
```

