# AcWing��83��������ϰ
## 1.[��ż](https://www.acwing.com/problem/content/4788/)
* ͳ�Ʋ�ͬ�ַ����ִ������ж���ż
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
## 2.[����](https://www.acwing.com/problem/content/4789/)
* ̰�ģ��Ƚ����Է�����ؿ��Ĺ�ͳ�ƣ�Ȼ��ӷ����ȽϸߵĿ�ʼ����������ʵ����������
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
## 3.[��������](https://www.acwing.com/problem/content/4790/)
* n<<1��������Ҫ����Ķ�����m��Ŀ��
* ���n<<1��m����ô���м�ֵһ���ʹ���m�����-1
* ���nΪ1,ֻ��m=0�ǲ���������һ��������������-1
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