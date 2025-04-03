# [CUGBACM21级春季学期训练#2](https://vjudge.net/contest/545491)

## [A - Make It Round](https://vjudge.net/problem/CodeForces-1759D)

* 题意：n * (1~m)，求后缀0最多的时候最大值为多少，如果没有0就输出n * m

* 思路：如果n * x1有一个0，n * x2有两个0，那么x2一定是x1的整数倍，所以每次取到0就去掉，记录这次的次数，之后就加这个次数，直到不能加为止

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
int main()
{
	int t;cin >> t;
	while(t--)
	{
		ll n,m;cin >> n >> m;
		int num = 0,now = 1,x,tem = 1;
		while(n%10 == 0)
		{
			num++;
			n/=10;
		}
		x = n;
		while(m - tem > 0)
		{
			if(n%10 == 0)
			{
				num++;
				n/=10;
				x=n;
				tem = now;
			}
			else
			{
				n+=x;
				now+=tem;
				m-=tem;
			}
		}
		cout << n;
		while(num--)
		cout << 0;
		cout << endl;
	}
}
```

## [B - The Humanoid](https://vjudge.net/problem/CodeForces-1759E)

* 题意：可以吃到值小于现在值的机器人，并获得$\lfloor\frac{v}{2}\rfloor$的值，还有令自身值乘2的两次机会和乘3的一次机会，求最大值
* 思路：从小到大排序，先吃再乘能乘到最多的数，但是无法确定乘的顺序，所以枚举3种乘的顺序，取最大值即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 2e5 + 10;
const int MAXH = 1e6 + 10;
ll dp[2][3][MAXN];
ll a[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		ll n,h;cin >> n >> h;
		ll hh = h;
		for(int i = 1;i <= n;i++)
			cin >> a[i];
		sort(a + 1,a + 1 + n);
		ll t1 = 2,t2 = 1,num = 0;
		for(int i = 1;i <= n;i++)
		{
			if(h <= a[i])
			{
				if(t1)
				h*=2,t1--;
				else if(t2)
				h*=3,t2--;
				else
				break;
				i--;
			}
			else
			{
				h += a[i] / 2;
				num++;
			}
		}
		h = hh;
		ll nnum = 0;
		t1 = 2,t2 = 1;
		for(int i = 1;i <= n;i++)
		{
			if(h <= a[i])
			{
				if(t2)
				h*=3,t2--;
				else if(t1)
				h*=2,t1--;
				else
				break;
				i--;
			}
			else
			{
				h += a[i] / 2;
				nnum++;
			}
		}
		num = max(nnum,num);
		h = hh;
		nnum = 0;
		t1 = 2,t2 = 1;
		for(int i = 1;i <= n;i++)
		{
			if(h <= a[i])
			{
				if(t1 == 1 && t2)
				h*=3,t2--;
				else if(t1)
				h*=2,t1--;
				else
				break;
				i--;
			}
			else
			{
				h += a[i] / 2;
				nnum++;
			}
		}
		cout << max(nnum,num) << endl;
	}
}
```

## [C - All Possible Digits](https://vjudge.net/problem/CodeForces-1759F)

* 题意：给出一个p进制的数，记录每个+1，问出现所有0~p-1的最少+1次数
* 思路：首先最大为加上p-1，因为那时所有的数都在第一位出现过了，然后从p-1枚举到1，分两种情况，如果个位数加上这个枚举的数大于p，就要考虑进位前有没有出现和进位后有没有出现，并且如果进位前没有出现，那么必须要进位，没进位的就不用考虑，如果都出现了就考虑没进位的，特殊的，枚举刚好为p时，我们考虑的是没进位的，因为加上这个数才进位

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 101;
#define ll long long
ll a[MAXN],b[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n,p;cin >> n >> p;
		for(int i = 1;i <= n;i++)
		{
			cin >> a[i];
			b[i] = a[i];
		}
		ll begin = a[n];
		b[n] = p;
		int ptop = n;
		b[0] = 0;
		while(b[ptop] == p)
		{
			b[ptop] = 0;
			b[--ptop]++;
		}
		int be = 1;
		if(ptop == 0)
		b[ptop] = 1,be = 0;
		ll i;
		bool f,ff = false,fff = true;
		for(i = p - 1;i >= 1;i--)
		{
			f = false;
			ff = false;
			if(i + begin > p)
			{
				for(int j = be;j <= n;j++)
					if(b[j] == (i + begin) % p)
						f = true;
				for(int j = 1;j <= n - 1;j++)
					if(a[j] == (i + begin) % p)
					{
						f = true;
						ff = true;
					}
				if(!ff)
					fff = false;//在进位最前找不到
			}
			else if(fff)
			{
				for(int j = 1;j <= n - 1;j++)
					if(a[j] == (i + begin) % p)
						f = true;
			}
			if(!f)
			{
				break;
			}
		}
		cout << i << endl;
	}
}
```



## [D - Restore the Permutation](https://vjudge.net/problem/CodeForces-1759G)

* 题意：给每个$b_i$配一个数，让这个序列字典序最小，1~n都要出现且仅出现一次
* 思路：从后往前配，每次都找最大的，这样留给前面就最小了

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e5 + 10;
bool ma[MAXN];
bool use[MAXN];
int b[MAXN];
int ans[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		int r = n;//指针
		memset(use,0,sizeof(use));
		memset(ma,0,sizeof(ma));
		bool f = 1;
		for(int i = 1;i <= n/2;i++)
		{
			cin >> b[i];
			if(use[b[i]])
				f = 0;
			use[b[i]] = 1;
		}
		for(int i = n/2;i >= 1;i--)
		{
			while(use[r])
				r--;
			int rr = min(b[i] - 1,r);
			while(use[rr])
				rr--;
			if(rr <= 0)
				f = 0;
			use[rr] = 1;
			ans[i] = rr;
		}
		if(f)
		{
			for(int i = 1;i <= n/2;i++)
			{
				cout << ans[i] << ' ' << b[i] << ' ';
			}
		}
		else
			cout << -1;
		cout << endl;
	}
}
```



## [E - Two Permutations](https://vjudge.net/problem/CodeForces-1761A)

* 题意：两个长度相等为n的数组(1~n不重复)，最长前缀为a，最长后最为b，判断是否存在
* 思路：不存在就是前后缀相连但不重合，也就是要么a=b=n，要么a+b<n-1，因为如果前后缀中间就隔着一个数，那么那个数一定是一样的

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n,a,b;cin >> n >> a >> b;
		if(a == b && b == n)
		cout << "Yes" << endl;
		else if(n - a - b >= 2)
		cout << "Yes" << endl;
		else
		cout << "No" << endl;
	}
}
```



## [F - Elimination of a Ring](https://vjudge.net/problem/CodeForces-1761B)

* 题意：给出一个环，相邻的数如果相等就会消除到一个数，每次可以消除一个数，求最多能操作的次数
* 思路：最多操作此时，也就意味着因为相等消除的数最少，如果只有一种数那么答案就为1
* 如果有两种数，那么就可以简化为101010这种形式，无论是取0还是取1都可以简化为1010，所以答案为n/2+1
* 如果有三种数，以第三个数为边，就可以删去另外两个数直到那两个数只剩1个，再以那一个为边，就可以删去所以数，所以答案为n，大于三种数同理

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 101;
int a[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		int num = 0;
		a[1] = 0;
		for(int i = 1;i <= n;i++)
		{
			int x;cin >> x;
			if(a[num] == x || (i == n && a[1] == x))
			continue;
			else
			a[++num] = x;
		}
		int t[MAXN],nnum = 0;
		for(int i = 1;i <= num;i++)
		{
			bool flag = true;
			for(int j = 1;j <= nnum;j++)
			if(a[i] == t[j])
			flag = false;
			if(flag)
			t[++nnum] = a[i];
		}
		if(nnum == 2)
		{
			num = num / 2 + 1;
		}
		cout << num << endl;
	}
}
```



## [G - Set Construction](https://vjudge.net/problem/CodeForces-1761C)

* 题意：n个集合有子集关系，最大的集合包括1~n个数，构造出不重复的满足题意的集合
* 思路：拓扑排序，一个一个加元素，因为只有n个集合，所以不会比n个元素多

```c++
#include<bits/stdc++.h>
using namespace std;
bool dad[101][101];
pair<int,int> pa[101];
bool cmp(pair<int,int> a,pair<int,int> b)
{
	return a.second > b.second;
}
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		vector<int> ve[101];
		for(int i = 1;i <= n;i++)
			pa[i] = make_pair(i,0);
		for(int i = 0;i < n;i++)
		{
			string str;cin >> str;
			for(int j = 0;j < n;j++)
			{
				dad[i + 1][j + 1] = str[j] - '0';
				if(dad[i+1][j+1])
				pa[i+1].second++;
			}
		}
		sort(pa + 1,pa + 1 + n,cmp);
		for(int i = 1;i <= n;i++)
		{
			ve[pa[i].first].push_back(i);
			for(int j = 1;j <= n;j++)
				if(dad[pa[i].first][j])
					ve[j].push_back(i);
		}
		for(int i = 1;i <= n;i++)
		{
			int end = ve[i].size();
			cout << end << ' ';
			for(int j = 0;j < end;j++)
				cout << ve[i][j] << ' ';
			cout << endl;
		}
	}
}
```

