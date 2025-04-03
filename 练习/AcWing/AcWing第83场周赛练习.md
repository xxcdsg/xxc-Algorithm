# AcWing第83场周赛练习
## 1.[奇偶](https://www.acwing.com/problem/content/4788/)
* 统计不同字符出现次数并判断奇偶
```c++
#include<bits/stdc++.h>
using namespace std;
bool use[26] = {0};
int main()
{
	string str;cin >> str;
	int num = 0;
	for(int i = 0;i < str.size();i++)
	if(use[str[i]-'a'] == 0)
	num++,use[str[i]-'a']=1;
	if(num&1)
	cout << "odd";
	else
	cout << "even";
}
```
## 2.[闯关](https://www.acwing.com/problem/content/4789/)
* 贪心，先将所以非特殊关卡的关统计，然后从分数比较高的开始，这样就能实现最多的增长
```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
ll a[101],b[101];
int main()
{
	int n,m;cin >> n >> m;
	ll sum = 0;
	for(int i = 1;i <= n;i++)
	{
		cin >> a[i];
		sum += a[i];
	}
	for(int i = 1;i <= m;i++)
	{
		int t;cin >> t;
		b[i] = a[t];
		sum -= b[i];
	}
	sort(b + 1,b + 1 + m);
	for(int i = m;i >= 1;i--)
	{
		if(sum >= b[i])
		sum*=2;
		else
		sum += b[i];
	}
	cout << sum;
}
```
## 3.[构造序列](https://www.acwing.com/problem/content/4790/)
* n<<1就是我们要输出的对数，m是目标
* 如果n<<1比m大，那么序列价值一定就大于m，输出-1
* 如果n为1,只有m=0是才能随便输出一个数，否则就输出-1
```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long

int main()
{
	int n,m;cin >> n >> m;
	bool flag = false;
	if(n%2)
	flag = true;
	n >>= 1;
	if(n == 0 && m == 0 && flag)
	cout << 1;
	else if(n == 0 && m != 0)
	cout << -1;
	else if(n > m)
	cout << -1;
	else
	{
		int x = m + 1 - n;
		cout << x << ' ' << 2 * x << ' ';
		x *= 2;
		for(int i = 2;i <= n;i++)
		cout << ++x << ' ' << ++x << ' ';
		if(flag)
		cout << ++x;
	}
}
```