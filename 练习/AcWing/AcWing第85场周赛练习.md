# AcWing��85������
## 1.[������](https://www.acwing.com/activity/content/problem/content/7917/)
* ͳ���������жϽ����û�Ѷ�
```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int n;cin >> n;
	int x,y;
	int a[3] = {0};
	for(int i = 1;i <= n;i++)
	{
		int t,x,y;cin >> t >> x >> y;
		a[t] += x - y;
	}
	for(int i = 1;i <= 2;i++)
	if(a[i] >= 0)
	cout << "LIVE" << endl;
	else
	cout << "DEAD" << endl;
}
```
## 2.[����ֵ](https://www.acwing.com/activity/content/problem/content/7918/)
* ģ���⣬ֻҪ��������ĸȫ���ں�����ܵõ����ֵ
```c++
#include<bits/stdc++.h>
using namespace std;

#define turn(x) (x-'a')

int main()
{
	string str;cin >> str;
	int k;cin >> k;
	int a[26];
	int ma = 0;
	for(int i = 0;i < 26;i++)
	{
		cin >> a[i];
		ma = max(a[i],ma);
	}
	int output = 0;
	for(int i = 1;i <= str.size();i++)
	output += a[turn(str[i - 1])] * i;
	for(int i = 1;i <= k;i++)
	output += ma * (i + str.size());
	cout << output;
}
```
## 3.[Σ�ճ̶�](https://www.acwing.com/activity/content/problem/content/7919/)
* ÿ����һ�ֻ�ѧ���ʣ�����������ܹ���֮ǰ�����Թ��е�һ�ֻ�������ʷ�����Ӧ
* ���һ�Ѷ����ܹ�ͨ����Ӧ��ϵ��������ôֻҪȡһ�����ʣ���һ�����ʶ��������ܵ�2����Σ�նȼӳ�
* �����ת�����󲢲鼯��Ԫ������
```c++
#include<bits/stdc++.h>
using namespace std;

const int MAXN = 51;
int a[MAXN];
int b[MAXN] = {0};
int n,m;

int find(int x)
{
	if(a[x] == x)
	return x;
	else
	return a[x] = find(a[x]);
}

void reload()
{
	for(int i = 1;i <= n;i++)
	a[i] = i;
}

int main()
{
	cin >> n >> m;
	reload();
	for(int i = 1;i <= m;i++)
	{
		int x,y;cin >> x >> y;
		a[find(x)] = find(y);
	}
	long long sum = 1;
	for(int i = 1;i <= n;i++)
	{
		b[find(i)]++;
	}
	for(int i = 1;i <= n;i++)
	{
		sum *= ((long long)1 << (b[find(i)] - 1));
		b[find(i)] = 1;
	}
	cout << sum;
}
```