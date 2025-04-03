# [D -  Three Days Ago](https://vjudge.net/problem/AtCoder-abc295_d)

* 题意：找序列中出现数字的次数都为偶数的连续子序列数量
* 思路：前缀和得出次数，然后找数字出现次数奇偶性相同的对数，因为要所有的数都要满足奇偶性相同的条件，也就是要满足10个01条件都相同，容易想到用二进制来表示状态，然后排序求相同状态数量组合一下就能得出答案

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 5e5 + 10;//
int a[MAXN];
int main()
{
	string str;cin >> str;
	int end = str.size();
	int now = 0;
	for(int i = 0;i < end;i++)
	{
		int x = str[i] - '0';
		now ^= (1 << x);
		a[i] = now;
	}
	a[end] = 0;
	sort(a,a + 1 + end);
	long long num = 1,ans = 0;
	for(int i = 1;i <= end;i++)
	{
		if(a[i] == a[i - 1])
			num++;
		else
			num = 1;
		ans += num - 1;
	}
	cout << ans << endl;
}
```

# [G - Minimum Reachable City](https://vjudge.net/problem/AtCoder-abc295_g)

* 题意：给出一个只会从后往前的有向图，每次可以根据激活从u到v的路，或者询问x能到的最小位置
* 思路：向前找，会把所有路上的全部更新，所以需要一个函数传递最后找到的值给前面，如果分别找后面的路和前面的路，必须将前后联系起来，使用并查集来存就可以在更新前面路时将后面的答案也同步更新（因为都是向前找，并且向前只有一个方向），~~然后就爆时间了，恭喜你，最后一个aftercontest_01.txt过不了~~，在更新完这条路时，这条路就可以压缩为一个点，进行路径压缩即可

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e5 + 10;
int a[MAXN];
int b[MAXN];
int find(int x)
{
	if(x == b[x]) return b[x];
	else return b[x] = find(b[x]);
}
void update(int u,int v){
	if(find(u) != find(v)){
		b[u] = find(v);
		update(a[u],v);
		a[u] = find(v);//路径压缩
	}
}
int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	int n;cin >> n;
	for(int i = 1;i <= n - 1;i++)
	{
		cin >> a[i + 1];
		b[i] = i;
	}
	b[n] = n;
	int q;cin >> q;
	for(int i = 1;i <= q;i++)
	{
		int op;cin >> op;
		if(op == 1)
		{
			int u,v;cin >> u >> v;
			update(u,v);
		}
		else
		{
			int x;cin >> x;
			cout << find(x) << endl;
		}
	}
}
```



