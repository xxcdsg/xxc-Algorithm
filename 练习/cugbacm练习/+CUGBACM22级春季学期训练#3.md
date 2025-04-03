# [CUGBACM22级春季学期训练#3](https://vjudge.net/contest/545887)

## [A - Division?](https://vjudge.net/problem/CodeForces-1669A)

* 题意：水
* 思路：水

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int x;cin >> x;
		cout << "Division ";
		if(x >= 1900)
			cout << 1;
		else if(x >= 1600)
			cout << 2;
		else if(x >= 1400)
			cout << 3;
		else
			cout << 4;
		cout << endl;
	}
}
```

## [B - Triple](https://vjudge.net/problem/CodeForces-1669B)

* 题意：出现三次，小范围，用桶存，水
* 思路：水

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e5 + 10;
int num[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		bool f = false;
		memset(num,0,sizeof(num));
		for(int i = 1;i <= n;i++)
		{
			int x;cin >> x;
			num[x]++;
			if(num[x] >= 3)
			f = true;
		}
		if(f)
		{
			for(int i = 1;i <= n;i++)
				if(num[i] >= 3)
				{
					cout << i;
					break;
				}
		}
		else
		cout << -1;
		cout << endl;
	}
}
```

## [C - Odd/Even Increments](https://vjudge.net/problem/CodeForces-1669C)

* 题意：一次操作可以给所有奇数位的数加1或给所有偶数位的数加1，问能否让所有数都变为奇数

* 思路：如果奇数位有奇有偶，那么加1会让偶变奇，让奇变偶，不成立，只有同奇同偶才能成立，偶数位同理

```c++
#include<bits/stdc++.h>
using namespace std;
bool f[5] = {0};
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;
		cin >> n;
		int i = 1;
		int x;
		memset(f,0,sizeof(f));
		while(i <= n)
		{
			cin >> x;
			if((i % 2) == 0)
			{
				if((x % 2) == 0)
					f[1] = 1;
				else
					f[2] = 1;
			}
			else
			{
				if((x % 2) == 0)
					f[3] = 1;
				else
					f[4] = 1;
			}
			i++;
		}
		if((f[1] && f[2]) || (f[3] && f[4]))
		cout << "NO" << endl;
		else
		cout << "YES" << endl;
	}
}
```

## [D - Colorful Stamp](https://vjudge.net/problem/CodeForces-1669D)

* 题意：一开始都是wwwwww........的序列，每一次都可以选择相邻的两个元素填上RB或BR，现给出结果，判断是否成立
* 思路：1.如果没有色块，成立
* 2.如果有色块，那么它的长度必须大于等于2，并且包含R和B（缺一不可）

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		string str;cin >> str;
		bool f[3] = {0};
		bool ff = 1;
		int i = 0;
		while(i < n && str[i] == 'W')
		{
			i++;
		}
		for(;i < n;i++)
		{
			while(i < n && str[i] == 'W')
			{
				i++;
			}
			f[1] = f[2] = 0;
			while(i < n && str[i] != 'W')
			{
				if(str[i] == 'R')
				f[1] = 1;
				else if(str[i] == 'B')
				f[2] = 1;
				else
				break;
				i++;
			}
			if(!(f[1] && f[2]))
			ff = 0;
			while(i + 1 < n && str[i + 1] == 'W')
			{
				i++;
			}
		}
		if(ff)
		cout << "YES" << endl;
		else
		cout << "NO" << endl;
	}
}
```

## [E - 2-Letter Strings](https://vjudge.net/problem/CodeForces-1669E)

* 题意：给一堆两位字符串，问有一个字符不一样的字符串对的对数
* 思路：容斥原理，一个字符串不一样的数量 = 所有字符串 - 第一个字符串一样 - 第二个字符串一样 + 完全一样

![E - 2-Letter Strings](C:\Users\Administrator\Desktop\markdown\E - 2-Letter Strings.png)

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
ll num[26][26];
ll nnum[2][26];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		memset(num,0,sizeof(num));
		memset(nnum,0,sizeof(nnum));
		ll sum = 0;
		ll ans = 0;
		for(int i = 1;i <= n;i++)
		{
			string str;cin >> str;
			ans += nnum[1][str[1] - 'a'] + nnum[0][str[0] - 'a'] - 2 * num[str[0] - 'a'][str[1] - 'a'];
			num[str[0] - 'a'][str[1] - 'a']++;
			nnum[1][str[1] - 'a']++;
			nnum[0][str[0] - 'a']++;
			sum++;
		}
		cout << ans << endl;
	}
}
```

## [F - Eating Candies](https://vjudge.net/problem/CodeForces-1669F)

* 题意：一个从左取数，一个从右取数，求取数和相等时的最大值（不能取同一个数和跳过）
* 思路：双指针

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e5 + 10;
int w[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		for(int i = 1;i <= n;i++)
		{
			cin >> w[i];
		}
		int l = 0,r = n + 1,num = 0;
		long long ma = 0,ll = 0,rr = 0;
		while(l < r)
		{
			if(ll == rr)
			{
				ll += w[++l];
				rr += w[--r];
				ma = num;
				num += 2;
			}
			else if(ll > rr)
			{
				rr += w[--r];
				num++;
			}
			else
			{
				ll += w[++l];
				num++;
			}
		}
		cout << ma << endl;
	}
}
```

## [G - Fall Down](https://vjudge.net/problem/CodeForces-1669G)

* 题意：给出一个包含石头，空气，固定石头的图，石头会往下掉直到碰到底端或者碰到其他石头，固定石头不会往下掉，求这张图的最后形式
* 思路：从下向上遍历，记录最低位置，如果有石头，就把这个石头放在最低位置并且最低位置提高一格，如果是固定石头就让最低位置为这个位置的+1

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 51;
int h[51];
char c[51][51];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n,m;cin >> n >> m;
		for(int i = 1;i <= m;i++)
		h[i] = n + 1;
		for(int i = 1;i <= n;i++)
		for(int j = 1;j <= m;j++)
		{
			cin >> c[i][j];
		}
		for(int i = n;i >= 1;i--)
		for(int j = 1;j <= m;j++)
		{
			if(c[i][j] == '*')
			{
				c[i][j] = '.';
				c[--h[j]][j] = '*';
			}
			else if(c[i][j] == '.')
			{
				continue;
			}
			else
			{
				h[j] = i;
			}
		}
		for(int i = 1;i <= n;i++)
		{
			for(int j = 1;j <= m;j++)
			{
				cout << c[i][j];
			}
			cout << endl;
		}
	}
}
```

## [H - Maximal AND](https://vjudge.net/problem/CodeForces-1669H)

* 题意：有k次让$a_i$的二进制某一位置为1的机会，求$a_1\&a_2\&a_3......\&a_n$的最大值
* 思路：尽量让较高位都为1

```c++
#include<bits/stdc++.h>
using namespace std;
int num[32];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n,m;cin >> n >> m;
		memset(num,0,sizeof(num));
		for(int i = 1;i <= n;i++)
		{
			int x;cin >> x;
			int ptop = 0;
			while(x)
			{
				if(x % 2) num[ptop]++;
				x /= 2;
				ptop++;
			}
		}
		int ans = 0;
		for(int i = 30;i >= 0;i--)
		{
			if(n - num[i] <= m)
			{
				m -= (n - num[i]);
				ans += (1 << i);
			}
		}
		cout << ans << endl;
	}
}
```

