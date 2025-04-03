# [CUGBACM21级春季学期训练#6](https://vjudge.net/contest/547121#overview)

## [A - Coprime](https://vjudge.net/problem/CodeForces-1742D)

### 题意：从序列中找最大的两个互质数的和

### 思路：~~给薄纱了~~因为a范围在1~1000之间，所以我们先预处理所以的组合判断是否互质，然后从大到小枚举这个数字是否出现复杂度为$10^6t$

```c++
#include<bits/stdc++.h>
using namespace std;
int gcd(int x,int y)
{
	if(y == 0) return x;
	else return gcd(y,x%y);
}
const int MAXN = 2e5 + 10;
bool able[1001][1001];
void pre()
{
	for(int i = 1;i <= 1000;i++)
	for(int j = 1;j <= i;j++)
	able[i][j] = able[j][i] = (gcd(i,j) == 1);
}
int main()
{
	int t;cin >> t;
	pre();
	while(t--)
	{
		int n;cin >> n;
		int a[1001] = {0};
		int ma = 0;
		for(int i = 1;i <= n;i++)
		{
			int x;
			cin >> x;
			a[x] = i;
		}
		for(int i = 1;i <= 1000;i++)
		for(int j = 1;j <= 1000;j++)
		if(able[i][j] && a[i] && a[j] && i != j)
		ma = max(a[i] + a[j],ma);
		ma = max(2*a[1],ma);
		if(ma == 0)
		cout << -1 << endl;
		else
		cout << ma << endl;
		
	}
}
```

## [B - Scuza](https://vjudge.net/problem/CodeForces-1742E)

### 题意：给出一串阶梯的高度，然后给出最大能走的阶梯高度，求对于每个阶梯高度对应能走到的最大高度

### 思路：新建一个数组存前缀和，也就是对应的高度，在新建一个数组存最大出现的阶梯，在最大阶梯用二分中找最大能走的阶梯高度，输出对应的高度

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 2e5 + 10;
int ma[MAXN];
ll sum[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n,q;cin >> n >> q;
		memset(ma,0,sizeof(ma));
		memset(sum,0,sizeof(sum));
		for(int i = 1;i <= n;i++)
		{
			int x;cin >> x;
			ma[i] = max(ma[i - 1],x);
			sum[i] = sum[i - 1] + x;
		}
		while(q--)
		{
			int x;cin >> x;
			int pos = upper_bound(ma + 1,ma + 1 + n,x) - ma - 1;
			cout << sum[pos] << ' ';
		}
		cout << endl;
	}
}
```

## [C - Smaller](https://vjudge.net/problem/CodeForces-1742F)

### 题意：两个字符串p，q一开始都是"a"，然后给这两个字符串加入字符并重新排序，问能否让字符串p的字典序小于q

### 思路：尽量让字符串p小，字符串q大，也就是说q从'z'向前找，p从'a'向后找，因为'a'一开始都有，所以只要q除了'a'还有其他字符就能让其字典序小于q，否则就要让p的'a'的数量大于q，并且q不存在'a'以外的字符

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
ll a[26],b[26],s[26];
bool f()
{
	int l = 0,r = 25;
	while(b[r] == 0)
		r--;
	if(r > l)
	return 1;
	else
	{
		l = 25;
		while(a[l] == 0)
		l--;
		if(l != 0)
		return 0;
		else
		return a[0] < b[0];
	}
}
int main()
{
	int t;cin >> t;
	while(t--)
	{
		memset(a,0,sizeof(a));
		memset(b,0,sizeof(b));
		a[0] = b[0] = 1;
		int q;cin >> q;
		while(q--)
		{
			memset(s,0,sizeof(s));
			int d,k;cin >> d >> k;
			string str;cin >> str;
			int end = str.size();
			for(int i = 0;i < end;i++)
				s[str[i] - 'a']++;
			if(d == 1)
				for(int i = 0;i < 26;i++)
					a[i] += s[i]*k;
			else
				for(int i = 0;i < 26;i++)
					b[i] += s[i]*k;
			if(f())
				cout << "YES" << endl;
			else
				cout << "NO" << endl;
		}
	}
}
```

## [D - Orray](https://vjudge.net/problem/CodeForces-1742G)

### 题意：重新给a排序使$b_i=a_1|a_2|...a_i$对应的b序列字典序最大

### 思路：找最大的数，然后将这个数对应的二进制全部取消，再找最大的数，直到最大的数为0，此时将剩下的所有数输出

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e5 + 10;
int a[MAXN];
bool use[MAXN];
pair<int,int> b[MAXN];//new
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		memset(use,0,sizeof(use));
		for(int i = 1;i <= n;i++)
		{
			cin >> a[i];
			b[i].first = a[i];
			b[i].second = i;
		}
		sort(b + 1,b + 1 + n);
		while(b[n].first)
		{
			cout << a[b[n].second] << ' ';
			use[b[n].second] = 1;
			int t = 0;
			t = ~b[n].first;
			for(int i = 1;i <= n;i++)
				b[i].first &= t;
			sort(b + 1,b + 1 + n);
		}
		for(int i = 1;i <= n;i++)
			if(!use[i])
				cout << a[i] << ' ';
		cout << endl;
	}
}
```

## [E - Divisible Numbers (easy version)](https://vjudge.net/problem/CodeForces-1744E1)

## [F - Divisible Numbers (hard version)](https://vjudge.net/problem/CodeForces-1744E2)

### 题意：从$a<x≤c,b<y≤d$，找一个$xy\%ab=0$

### 思路：将$ab$分解为两个因子相乘，令$x$为一个因子，$y$为另外一个，在范围内找，而$ab$的因子就为$a$的因子乘上$b$的因子，所以分解$a和b$的因子再枚举组合即可

```c++
#include<bits/stdc++.h>
using namespace std;

#define ll long long

ll gcd(ll x,ll y)
{
	if(y == 0) return x;
	else return gcd(y,x % y);
}

int main()
{
	int t;cin >> t;
	while(t--)
	{
		ll a,b,c,d;cin >> a >> b >> c >> d;
		ll aim = a*b;
		vector<ll> vea,veb;
		ll sqr = sqrt(a);
		for(int i = 1;i <= sqr;i++)
			if(a % i == 0)
			{
				vea.push_back(i);
				vea.push_back(a/i);
			}
		sqr = sqrt(b);
		for(int i = 1;i <= sqr;i++)
			if(b % i == 0)
			{
				veb.push_back(i);
				veb.push_back(b/i);
			}
		bool f = 1;
		ll enda = vea.size(),endb = veb.size(),s;
		for(int i = 0;i < enda && f;i++)
		for(int j = 0;j < endb && f;j++)
		{
			ll ss = vea[i]*veb[j];
			if(a/ss != c/ss)
			s = aim/vea[i]/veb[j];
			else
			continue;
			if(b/s != d/s)
			{
				cout << (c/ss)*ss << ' ' << (d/s)*s << endl;
				f = 0;
			}
		}
		if(f)
			cout << -1 << ' ' << - 1 << endl;
	}
}
```

## [G - MEX vs MED](https://vjudge.net/problem/CodeForces-1744F)

### 题意：mex代表序列中最小的没出现的自然数，med代表排$\lfloor(|s|+1)/2\rfloor$，求mex大于med对应的$(l,r)$对数

### 思路：我们要得到$0,1...mid-1,mid,mid+1...i,i+k,...$这样的序列，所以可得到这样的结论：取0可以取长度为0的序列，取0，1可以取长度最长为2的序列，取0，1，2