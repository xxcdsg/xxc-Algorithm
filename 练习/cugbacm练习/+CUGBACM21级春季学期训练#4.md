# [CUGBACM21级春季学期训练#4](https://vjudge.net/contest/546466#problem/A)

## [A - Two Elevators](https://vjudge.net/problem/CodeForces-1729A)

### 题意：两个电梯，一个在a，另外一个在b并必须经过c，求那一个更快

### 思路：在a所需要的时间为$|a-1|$，另外一个需要的时间为$|b-c|+|c-1|$

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int a,b,c;cin >> a >> b >> c;
		if(a - 1 < abs(b-c) + abs(c-1))
			cout << 1;
		else if(a - 1 == abs(b-c) + abs(c-1))
			cout << 3;
		else
			cout << 2;
		cout << endl;
	}
}
```

## [B - Decode String](https://vjudge.net/problem/CodeForces-1729B)

### 题意：数字转字符，如果小于10就正常，如果大于10会在这个数字后面加一个0

### 思路：看这个数字后第两个是否为0，如果是就说明这个数是要大于10读取，但是，如果这个数字后第三数为0说明这第二个是第一个的后导0，说明这个数是单个的

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int q;cin >> q;
	while(q--)
	{
		int n;cin >> n;
		string str;cin >> str;
		for(int i = 0;i < n;i++)
		{
			if(i + 2 < n && str[i + 2] == '0' && !(i + 3 < n && str[i + 3] == '0'))
			{
				int t = (str[i] - '0') * 10 + (str[i + 1] - '0');
				cout << (char)(t + 'a' - 1);
				i += 2;
			}
			else
			{
				int t = (str[i] - '0');
				cout << (char)(t + 'a' - 1);
			}
		}
		cout << endl;
	}
}
```

## [C - Jumping on Tiles](https://vjudge.net/problem/CodeForces-1729C)

### 题意：要从字符串的最前面跳到最后面，每次跳跃需要的费用为字符之差的绝对值，问最少费用情况下最多次跳跃的跳法

### 思路：在第一个字符和最后一个字符的序号中间的字符按顺序跳即可，用一个容器记录所有相同的字符的位置

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int t;cin >> t;
	while(t--)
	{
		string str;cin >> str;
		vector<int> ve[26];
		int end = str.size();
		int be = str[0] - 'a',en = str[end - 1] - 'a';
		for(int i = 0;i < end;i++)
			ve[str[i] - 'a'].push_back(i + 1);
		int m = 0;
		for(int i = be;i <= en;i++)
			m += ve[i].size();
		if(be != en)
			for(int i = be;i >= en;i--)
				m += ve[i].size();
		cout << abs(be - en) << ' ' << m << endl;
		for(int i = be;i <= en;i++)
		{
			int t = ve[i].size();
			for(int j = 0;j < t;j++)
				cout << ve[i][j] << ' ';
		}
		if(be != en)
			for(int i = be;i >= en;i--)
			{
				int t = ve[i].size();
				for(int j = 0;j < t;j++)
					cout << ve[i][j] << ' ';
			}
		cout << endl;
	}
}
```

## [D - Friends and the Restaurant](https://vjudge.net/problem/CodeForces-1729D)

### 题意：每个人有费用和可承担费用，把这些人分组，使得组的可承担费用大于等于费用，最多可以分出多少组（至少两个人）

### 思路：先用可承担费用减去费用，算出贡献，再按贡献排序

### 一个正贡献的人，如果请一个负贡献的人，这样最好，如果请两个，不如请一个，这样分的组比较小，最后组数更多。

### 正贡献的人尽量和小的组，不会浪费，导致后面正的人请不起小的

### 从最大的开始组起，让剩下的选择更多

### 如果负的没了，就让剩下的正数和0组

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
int a[MAXN],x[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		for(int i = 1;i <= n;i++)
			cin >> a[i];
		for(int i = 1;i <= n;i++)
		{
			cin >> x[i];
			x[i] -= a[i];
		}
		sort(x + 1,x + 1 + n);
		int l = 1,num = 0;
		for(int i = n;i > 0 && a[i] >= 0;i--)
		{
			while(x[l] < 0)
			{
				if(x[l] + x[i] >= 0)
					break;
				l++;
			}
			if(x[l] < 0)
				num++,l++;
			else if(i - 1 > 0 && x[i] + x[i - 1] >= 0 && i - 1 >= l)
				num++,i--;
		}
		cout << num << endl;
	}
}
```

## [E - Guess the Cycle Size](https://vjudge.net/problem/CodeForces-1729E)

### 题意：一个环，序号从1~n，每次可以问x点和y点间的随机一条边的长度，如果不存在则输出-1，问x和y多次结果不变，问n是多少，(x,y)和(y,x)不同，最多可以问50次

### 思路：n最大为$10^{18}$，如果用二分最多到$10^{15}$量级

###  (x，y)如果和(y，x)不同，说明是不同的两条边，长度为n=l1+l2

### 我们问25次不同的(x，y)和(y，x)就有$(2^{25}-1)/2^{25}$的概率能让它不一样，让不一样的相加就为结果了

### 选1为x，y递增，如果为-1，说明结果为y-1

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
int a[MAXN],x[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		for(int i = 1;i <= n;i++)
			cin >> a[i];
		for(int i = 1;i <= n;i++)
		{
			cin >> x[i];
			x[i] -= a[i];
		}
		sort(x + 1,x + 1 + n);
		int l = 1,num = 0;
		for(int i = n;i > 0 && a[i] >= 0;i--)
		{
			while(x[l] < 0)
			{
				if(x[l] + x[i] >= 0)
					break;
				l++;
			}
			if(x[l] < 0)
				num++,l++;
			else if(i - 1 > 0 && x[i] + x[i - 1] >= 0 && i - 1 >= l)
				num++,i--;
		}
		cout << num << endl;
	}
}
```

## [F - Kirei and the Linear Function](https://vjudge.net/problem/CodeForces-1729F)

### 题意：给由（0~9）组成的字符串，$v(l,r)$代表由$str[l]+str[l+1]...str[r]$子字符串代表的数，现给出l，r，k，我们要输出$L_1$，$L_2$满足$(v(L_1,L_1+w−1)⋅v(l_i,r_i)+v(L_2,L_2+w−1) )\%9=k$，最小化$L_1$，再最小化$L_2$（$L_1!=L_2$）

### 思路：首先一个数$10⋅x\%9=(9⋅(x\%9))\%9+x\%9=x\%9$

###  也就是说一个数对9取余就等于所有位置上的数相加再对9取余

### 那么$v(l_i,r_i)\%9$就可以用前缀和来解决

### 预处理所有$v(L_1,L_1+w−1)\%9$对应前两个位置

### 枚举$v(L_1,L_1+w−1)\%9$的值，算出$v(L_2,L_2+w−1)\%9$的值

### 看是否能更新

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e5 + 10;
int a[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		string str;cin >> str;
		int end = str.size();
		for(int i = 0;i < end;i++)//前缀和
			a[i + 1] = (a[i] + str[i] - '0') % 9;
		int w,m,n = end;cin >> w >> m;
		int num[9] = {0},now = 0;//记录数量和集齐的数量
		vector<int> ve[9];//记录起始位置
		for(int i = 1;i <= n - w + 1;i++)
		{
			int x = (a[i + w - 1] - a[i - 1] + 9) % 9;
			num[x]++;
			if(num[x] == 2)//满了
			{
				now++;
			}
			if(num[x] <= 2)//没满，继续加
				ve[x].push_back(i);
			if(now == 9)//全满了，退出
				break;
		}
		for(int i = 1;i <= m;i++)
		{
			int l,r,aim;cin >> l >> r >> aim;
			int x = (a[r] - a[l - 1] + 9) % 9;
			pair<int,int> ans = {n+1,n+1};
			for(int j = 0;j < 9;j++)//枚举l1和l2
			{
				if(num[j])
				for(int k = 0;k < 9;k++)
				{
					if((j*x+k)%9 == aim && (j == k && num[j] >= 2 || j != k && num[k]))//相等要两个，不相等要一个
					{
						if(j == k && (ans.first > ve[j][0] || ans.first == ve[j][0] && ans.second > ve[j][1]))
							ans = {ve[j][0],ve[j][1]};
						else if(ans.first > ve[j][0] || ans.first == ve[j][0] && ans.second > ve[k][0])
							ans = {ve[j][0],ve[k][0]};
					}
				}
			}
			if(ans.first == n+1)
				cout << -1 << ' ' << -1 << endl;
			else
				cout << ans.first << ' ' << ans.second << endl; 
		}
	}
}
```

## [G - Cut Substrings](https://vjudge.net/problem/CodeForces-1729G)

### 题意：大字符串和小字符串，移除大字符串中的小字符串直到小字符串消失，求最小需要去除次数

### 思路：利用kmp记录所有小字符串在大字符串里的出现，考虑小字符串会相互影响，所以取这个位置会使这个位置前s_2和后s_2-1的内的位置都不需要取

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 501,p = 1e9 + 7;
string s,t;
int nex[MAXN];
void fnex()
{
	int end = t.size();
	nex[0] = -1;
	int ptop = -1;
	int i = 0;
	while(i < end)
	{
		if(ptop == -1 || t[i] == t[ptop])
			nex[++i] = ++ptop;
		else
			ptop = nex[ptop];
	}
}
int main()
{
	int q;cin >> q;
	while(q--)
	{
		cin >> s >> t;
		fnex();
		int aim = t.size();
		int end = s.size();
		int i = 0,ptop = 0;
		vector<int> ve;
		while(i < end)
		{
			if(ptop == -1 || s[i] == t[ptop])
			{
				i++,ptop++;
				if(ptop == aim)
				{
					ve.push_back(i);
					ptop = nex[ptop];
				}
			}
			else
				ptop = nex[ptop];
		}
		pair<int,ll> dp[501];
		int ed = ve.size();
		int j = 0,k = 0;
		for(int i = 0;i < ed;i++)
		{
			if(ve[i] - ve[0] < aim)//如果取这个可以不取前面，那么这个的最小step为1
				dp[i].first  = 1,dp[i].second = 1;
			else
			{
				while(ve[i] - ve[j] >= aim)//找第一个在范围内的，它的前一个就是前面的一个必须在前一个的范围内
					j++;//j在范围内，j-1就不在范围内
				while(ve[j - 1] - ve[k] >= aim)//第一个能包含必须包含的
					k++;
				dp[i].first = dp[k].first + 1;//最小step肯定是取最前面的
				for(int ii = k;ii < i;ii++)//遍历前一个
				{
					if(dp[ii].first + 1 == dp[i].first && ve[i] - ve[ii] >= aim)
						dp[i].second = (dp[i].second + dp[ii].second) % p;
				}
			}
		}
		int mi = 999;//记录最小的能全覆盖的step
		for(int i = ed - 1;i >= 0;i--)
		{
			if(ve[ed - 1] - ve[i] < aim)
				mi = min(mi,dp[i].first);
		}
		ll sum = 0;
		for(int i = ed - 1;i >= 0;i--)//对所有最小能覆盖的累计
		{
			if(ve[ed - 1] - ve[i] < aim && dp[i].first == mi)
				sum = (sum + dp[i].second) % p;
		}
		if(mi == 999)
		mi = 0,sum = 1;
		cout << mi << ' ' << sum << endl;
	}
}
```

