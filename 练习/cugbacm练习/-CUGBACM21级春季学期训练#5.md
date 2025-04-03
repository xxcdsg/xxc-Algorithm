# [CUGBACM21级春季学期训练#5](https://vjudge.net/contest/546819#overview)

## [A - Playing with GCD](https://vjudge.net/problem/CodeForces-1736B)

### 题意：给出序列a，判断是否有序列b使得$a_i=gcd(b_i,b_{i+1})$

### 思路：这意味着$b_i和b_{i+1}$是$a_i$的整数倍，要使它们的最小公倍数为$a_i$只要找互质的数即可，这很好找

### 考虑隔项的关系，$b_i和b_{i+1}$是$a_i$的整数倍，$b_{i+2}和b_{i+3}$是$a_{i+2}$的整数倍，并且$a_{i+1}=gcd(b_{i+1},b_{i+2})$，所以要求$a_{i+1}是gcd(a_i,a_{i+2})$的整数倍

```c++
#include<bits/stdc++.h>
using namespace std;
int gcd(int x,int y)
{
	if(y == 0) return x;
	else return gcd(y,x%y);
}
const int MAXN = 1e5 + 10;
int a[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		for(int i = 1;i <= n;i++)
			cin >> a[i];
		bool f = true;
		for(int i = 3;i <= n && f;i++)
			if(a[i - 1] % gcd(a[i - 2],a[i]) != 0)
				f = false;
		if(f)
			cout << "YES" << endl;
		else
			cout << "NO" << endl;
	}
}
```

## [B - Good Subarrays (Hard Version)](https://vjudge.net/problem/CodeForces-1736C2)

## [C - Equal Binary Subsequences](https://vjudge.net/problem/CodeForces-1736D)

## [D - Masha and a Beautiful Tree](https://vjudge.net/problem/CodeForces-1741D)

### 题意：给出一个二叉树的底端，可以交换任意节点的左右子树，问需要几次才能使所以节点从小到大递增

### 思路：递归

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 262145;
int a[MAXN];
int b[MAXN];
bool flag = true;
int num = 0;
void turn(int l,int r)
{
	for(int i = l;i <= (l + r) / 2;i++)
		b[i] = a[i];
	int mid = (l + r) / 2;
	int nu = (r - l + 1) / 2;
	for(int i = 1;i <= nu;i++)
		a[l + i - 1] = a[mid + i];
	for(int i = 1;i <= nu;i++)
		a[mid + i] = b[l + i - 1];
}
void f(int l,int r)
{
	if(r == l)
	return;
	if(r == l + 1)
	{
		if(abs(a[r] - a[l]) != 1)
			flag = false;
		else if(a[r] == a[l] + 1) return;
		else
		turn(l,r),num++;
	}
	else
	{
		int mid = (l + r) / 2;
		f(l,mid);
		f(mid + 1,r);
		int lma = max(a[l],a[mid]);
		int rma = max(a[r],a[mid + 1]);
		int lmi = a[l] + a[mid] - lma;
		int rmi = a[r] + a[mid + 1] - rma;
		if(lma < rmi) return;
		else if(lmi > rma)
		turn(l,r),num++;
		else
		flag = false;
	}
}
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int m;cin >> m;
		for(int i = 1;i <= m;i++)
			cin >> a[i];
		int n = 0;
		while((1 << n) != m)
		n++;
		flag = true;
		num = 0;
		f(1,m);
		if(flag)
			cout << num << endl;
		else
			cout << -1 << endl;
	}
}
```



## [E - Sending a Sequence Over the Network](https://vjudge.net/problem/CodeForces-1741E)

### 题意：一个数组由许多小数组组成，每个小数组有这样的特点，它最前面的数字或最后面的数字代表这个数组的其他数的数量，问这个数组是否可以分解为这样的小数组

### 思路：动态规划记录结尾，每次读取一个数字就向前访问这个数量的位置后直接访问这个位置是否为结尾

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e5 + 10;
bool use[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		memset(use,0,sizeof(use));use[0] = 1;
		for(int i = 1;i <= n;i++)
		{
			int x;
			cin >> x;
			if(use[i - 1])
				if(i + x <= n)
					use[i + x] = 1;//向后
			if(i - x - 1 >= 0 && use[i - x - 1])
				use[i] = 1;//向前
		}
		if(use[n])
			cout << "YES" << endl;
		else
			cout << "NO" << endl;
	}
}
```



## [F - Multi-Colored Segments](https://vjudge.net/problem/CodeForces-1741F)

## [G - Kirill and Company](https://vjudge.net/problem/CodeForces-1741G)