# [Codeforces Round 877 (Div. 2)](https://codeforces.com/contest/1838)

## A. Blackboard List

## 题意：给两个数，两个差的绝对值为新数，现给出最后的结果，问一开始的两个数的其中一个为什么。

## 思路：首先负数无法通过绝对值得出，因此如果有负数就选负数，否则就全为正数，那么最大的数就无法通过相减取绝对值得出

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e2 + 10;
int a[N];
void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
	}
	sort(a + 1,a + 1 + n);
	if(a[1] < 0)
		cout << a[1] << endl;
	else
		cout << a[n] << endl;
}
#undef int
int main()
{
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## B. Minimize Permutation Subarrays

## 题意：给出一个排列，可以交换两个数的位置一次，令子序列为排列的最小数量，给出交换方法

## 思路：将所有数包括，即原本的排列为至少的排列，那么就要让这个排列为唯一子序列可成立，即将其他排列可能性排除（1这个排列本身就成立）

## 先排除如果要为除了1与全部的排列，那么就要2，所以我们只要让它取到1与2时同时包括上n即可，即我们让n在1与2之间即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int p[N];
void test(){
	int n;cin >> n;
	int aim_1,aim_2,aim_n;
	for(int i = 1;i <= n;i++){
		cin >> p[i];
		if(p[i] == 1)
			aim_1 = i;
		else if(p[i] == 2)
			aim_2 = i;
		else if(p[i] == n)
			aim_n = i;
	}
	if((aim_1 - aim_n)*(aim_n - aim_2) > 0)
		cout << 1 << ' ' << 1 << endl;
	else if((aim_1 - aim_2) * (aim_2 - aim_n) > 0)
		cout << aim_2 << ' ' << aim_n << endl;
	else
		cout << aim_1 << ' ' << aim_n << endl;
}
#undef int
int main()
{
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## C. No Prime Differences

## 题意：n*m的矩阵放上$1到nm$的数，相邻的数差的绝对值不为质数

## 思路：首先1不是质数，那么首先想到递增摆放，那么横向的问题就解决了

## 针对竖向，如果正常放，那么它们的差就为m，如果m为质数，那么就成立，否则就要隔着放置，针对奇数和偶数分别防止，只要让它们的差为m的整数倍

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e3 + 10;
bool not_prime(int x){
	for(int i = 2;i*i <= x;i++)
		if(x % i == 0)
			return 0;
	return 1;
}
int ans[N][N];
void test(){
	int n,m;cin >> n >> m;
	int begin = 1;
	for(int i = 1;i <= n / 2;i++){
		for(int j = 1;j <= m;j++)
			ans[i][j] = j + begin * m;
		begin += 2;
	}
	begin = 0;
	for(int i = 1;i <= n - n / 2;i++){
		for(int j = 1;j <= m;j++)
			ans[n/2 + i][j] = j + begin * m;
		begin += 2;
	}
	for(int i = 1;i <= n;i++){
		for(int j = 1;j <= m;j++)
			cout << ans[i][j] << ' ';
		cout << endl;
	}	
}
#undef int
int main()
{
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## D. Bracket Walk

## 题意：给出一个由左括号与右括号组成的序列，一开始在最左边，最后要走到最右边，每一问都改变一个位置的括号方向,问能否有一种移动方法得到一个括号序列

## 思路：如果有$(($，那么就可以得到任意偶数倍的$($，同理$))$也可以得到任意偶数倍的$($

## 那么如果$($与$)$的数量的奇偶性不相同，那么无论怎么走，奇偶性都不能相同，因此恒不相等，那么只要总数为偶数，分出来的奇偶性就一定相同。同理奇数一定不成立

## 然后从前面开始分析，如果一开始有$)$，那么肯定不成立，如果一开始为$(($，那么左边的左括号就一定不会不够，如果为$()$，就跳过

## 分析一下就可得最左边的连续相同的一定要是$(($或者不存在

## 因为要么是可以忽略的$()$，要么连续相同，那么如果先出现了$))$，那么左括号一定少一个，从左边就不成立

## 同理最右边的续相同的一定要是$))$

## 用set维护即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
bool a[N];
set<int> se;
void test(){
	int n,q;cin >> n >> q;
	string s;cin >> s;
	for(int i = 0;i < n;i++){
		if(s[i] == ')')
			a[i + 1] = 1;
		if(i != 0 && a[i] == a[i + 1])
			se.insert(i);
	}
	if(n%2 == 1){
		while(q--){
			int x;cin >> x;
			cout << "NO" << endl;
		}
	}else{
		while(q--){
			int x;cin >> x;
			if(x != 1 && a[x] == a[x - 1])
				se.erase(se.find(x - 1));
			if(x != n && a[x] == a[x + 1])
				se.erase(se.find(x));
			a[x] = 1 - a[x];
			if(x != 1 && a[x] == a[x - 1])
				se.insert(x - 1);
			if(x != n && a[x] == a[x + 1])
				se.insert(x);
			if(!se.empty() && a[1] == 0 && a[n] == 1 && (a[*se.begin()] != 0 || a[*(--se.end())] != 1))
				cout << "NO" << endl;
			else if(a[1] == 1 || a[n] == 0)
				cout << "NO" << endl;
			else 
				cout << "YES" << endl;
		}
	}
}
#undef int
int main()
{
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

