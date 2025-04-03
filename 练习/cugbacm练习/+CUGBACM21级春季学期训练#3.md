# [CUGBACM21级春季学期训练#3](https://vjudge.net/contest/545891#problem/F)

## [A - Image](https://vjudge.net/problem/CodeForces-1721A)

* 题意：把2*2的色块涂成同一种元素，求最少次数
* 思路：色彩数量减1

```c++
#include<bits/stdc++.h>
using namespace std;
int num[26];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		string str1,str2;cin >> str1 >> str2;
		for(int i = 0;i < 26;i++)
		num[i] = 0;
		num[str1[0] - 'a']++;
		num[str1[1] - 'a']++;
		num[str2[0] - 'a']++;
		num[str2[1] - 'a']++;
		sort(num , num + 26);
		if(num[25] == 1)
		cout << 3;
		else if(num[25] == 2 && num[24] == 2)
		cout << 1;
		else if(num[25] == 3)
		cout << 1;
		else if(num[25] == 4)
		cout << 0;
		else
		cout << 2;
		cout << endl;
	}
}
```

## [B - Deadly Laser](https://vjudge.net/problem/CodeForces-1721B)

* 题意：从(1，1)走到(n，m)，在$(s_x,s_y)$有一个激光器，机器人不能进入$|s_x-x|+|s_y-y|\leq k$的区域，如果可以到就输出最短长度，否则输出-1

  思路：![B - Deadly Laser_1](C:\Users\Administrator\Desktop\markdown\B - Deadly Laser_1.png)

* 如果可以走到，那么最短一定是$n+m-2$
* 如果不能走到，那么就有这四种情况

![B - Deadly Laser_2](C:\Users\Administrator\Desktop\markdown\B - Deadly Laser_2.png)

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n,m,x,y,d;cin >> n >> m >> x >> y >> d;
		bool f[5] = {0};
		if(x - 1 <= d)
		f[1] = 1;
		if(n - x <= d)
		f[2] = 1;
		if(y - 1 <= d)
		f[3] = 1;
		if(m - y <= d)
		f[4] = 1;
		if((f[1] && f[2]) || (f[3] && f[4]) || (f[1] && f[3]) || (f[2] && f[4]))
		cout << -1 << endl;
		else
		cout << n + m - 2 << endl;
	}
}
```

## [C - Min-Max Array Transformation](https://vjudge.net/problem/CodeForces-1721C)

* 题意：给出a序列和b序列，b序列为a序列加上d序列再排序得出，问$a_i$对应的$d_i$的最大值和最小值
* 思路：最理想的最小值就是第一个大于$a_i$的$b_i$减$a_i$，最大值就是最大的$b_i$减$a_i$
* 但是，有一些情况会不成立，比如
* $a:10\ \ 20\ \ 30\ \ 40\ \ $
* $b:22\ \ 33\ \ 33\ \ 55\ \ $
* 在其中，40必须和55相对，因为40不能和22、33、33对应，这就意味着10、20、30就不能和55对应
* 在这我们就使用拓展法~~随便取的名字~~，我们想要取到后面的数就意味着前面的数被其他数取了，在我们之前的不用考虑，已经一一对应好了，只要考虑我们正对的数，比如10正对的是22，这个22必须被10后面的数取，也就是20，成立，所以10就可以取20对应的33，但是它不能取55，因为如果取55，那么33就必须被后面的数取，但是33对应的数的下一个数是40，取不到33，所以55不能给10
* 也就是是要取那个数，就要让它对应的数大于那个数的前一个数
* 最小值无所谓，取第一个大于它的$b_i$减就好

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e5 + 10;
int a[MAXN],b[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		for(int i = 1;i <= n;i++)
		cin >> a[i];
		for(int i = 1;i <= n;i++)
		cin >> b[i];
		int p = 1,q = 1;
		while(p <= n)
		{
			if(a[p] <= b[q])
			{
				cout << b[q] - a[p] << ' ';
				p++;
			}
			else
			q++;
		}
		cout << endl;
		int maq = 1;
		for(int i = 1;i <= n;i++)
		{
			maq = max(maq,i);
			maq = min(maq,n);
			while(maq < n && a[maq + 1] <= b[maq])
			maq++;
			cout << b[maq] - a[i] << ' ';
		}
		cout << endl;
	}
}
```

## [D - Maximum AND](https://vjudge.net/problem/CodeForces-1721D)

* 题意：两个数组任意对应异或，然后与求和，求最大值
* 思路：分块思想
* 首先，从高位遍历到低位，如果a的0和b的1数量相等，记录这一位并开始分组
* 把a为0的和b为1的分一组，另外的分一组（a为1，b为0）
* 继续下一位，不过这次就有两个组要处理，要两个组都能分才能标记这一位并再次分组
* 因为vector爆了，所以我们的分组要更巧妙
* 元素数量不变，都是n个，那么我们将元素染色分组，排序分开即可
* 如何让颜色不重复？分组会裂成两份，那么我们让颜色*2 另一种 * 2 + 1即可(就是把前面的位反着记录)

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 1e5 + 10;
pair<int,ll> a[MAXN],b[MAXN];
pair<int,ll> aa[MAXN],bb[MAXN];
bool able[31];
bool cmp(pair<int,ll> x,pair<int,ll> y)
{
	return x.second < y.second;
}
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		for(int i = 0;i <= 30;i++)
		able[i] = 1;
		int ans = 0;
		for(int i = 1;i <= n;i++)
		{
			cin >> a[i].first;
			a[i].second = 0;
		}
		for(int i = 1;i <= n;i++)
		{
			cin >> b[i].first;
			b[i].second = 0;
		}
		for(int j = 29;j >= 0;j--)
		{
			int aim = (1 << j);
			for(int i = 1;i <= n;i++)
			{
				aa[i].first = a[i].first;
				bb[i].first = b[i].first;
				if(a[i].first & aim)
				{
					aa[i].second = a[i].second*2 + 1;
				}
				else
				{
					aa[i].second = a[i].second*2;
				}
				if(b[i].first & aim)
				{
					bb[i].second = b[i].second*2;
				}
				else
				{
					bb[i].second = b[i].second*2 + 1;
				}
			}
			bool f = true;
			sort(aa + 1,aa + 1 + n,cmp);
			sort(bb + 1,bb + 1 + n,cmp);
			for(int i = 1;i <= n && f;i++)
				if(aa[i].second != bb[i].second)
					f = false;
			if(f)
			{
				for(int i = 1;i <= n;i++)
				{
					a[i] = aa[i];
					b[i] = bb[i];
				}
			}
			else
			able[j] = 0;
		}
		for(int i = 0;i <= 29;i++)
		{
			if(able[i])
				ans += (1 << i);
		}
		cout << ans << endl;
	}
}
```

## [E - Prove Him Wrong](https://vjudge.net/problem/CodeForces-1651B)

* 题意：让所有组合$(i,j)$的$|a_i-a_j|*2>=a_i+a_j$
* 思路：令$a_i>a_j$
* 那么$2*a_i \geq a_i+3a_j$
* $a_i\geq3a_j$
* 所以令$a_1=1$每个数都*3即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		bool f = true;
		ll x = 1;
		for(int i = 2;i <= n;i++)
		{
			x = x*3;
			if(x > 1e9)
			f = false;
		}
		if(f)
		{
			cout << "YES" << endl;
			x = 1;
			cout << 1 << ' ';
			for(int i = 2;i <= n;i++)
			{
				x = x*3;
				cout << x << ' ';
			}
			cout << endl;
		}
		else
		cout << "NO" << endl;
	}
}
```

## [F - Fault-tolerant Network](https://vjudge.net/problem/CodeForces-1651C)

* 题意：上下互不相连，内部相邻的相连，连接需要的价值为节点值的差的绝对值，要求把这个网络连接满足破坏任意一个节点依旧能让所有节点全连接
* 思路：假设破坏上面的某个节点，那么这次破坏就将上面分成了两个部分，要让它们完全相连就必须在上面的左边和右边都连下面
* 那么我们可以发现，如果破坏$a_2$节点的话，$a_1$节点就必须向下连接，同理破坏$a_{n-1}$，$a_n$节点就必须向下连接，并且连接这两个节点后，无论破坏上面哪个节点，左右部分都可以连接到下面
* 同理$b_1$、$b_n$节点也要连接到上面
* 此时又出现了一个问题，是这四个节点互相连比较节省还是取其他节点连接比较节省，不过这只要枚举比较一下就可以了

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 2e5 + 10;
ll a[MAXN],b[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		for(int i = 1;i <= n;i++)
		cin >> a[i];
		for(int i = 1;i <= n;i++)
		cin >> b[i];
		ll ans = 0;
		ll mi[4];
		for(int i = 0;i < 4;i++)
		mi[i] = 1e9;
		for(int i = 1;i <= n;i++)
		mi[0] = min(mi[0],abs(a[1]-b[i]));
		for(int i = 1;i <= n;i++)
		mi[1] = min(mi[1],abs(a[n]-b[i]));
		for(int i = 1;i <= n;i++)
		mi[2] = min(mi[2],abs(b[1]-a[i]));
		for(int i = 1;i <= n;i++)
		mi[3] = min(mi[3],abs(b[n]-a[i]));
		ans = mi[0] + mi[1] + mi[2] + mi[3];
		ans = min(ans,mi[0]+mi[2]+abs(a[n]-b[n]));
		ans = min(ans,mi[0]+mi[3]+abs(a[n]-b[1]));
		ans = min(ans,mi[1]+mi[2]+abs(a[1]-b[n]));
		ans = min(ans,mi[1]+mi[3]+abs(a[1]-b[1]));
		ans = min(ans,abs(a[1]-b[1])+abs(a[n]-b[n]));
		ans = min(ans,abs(a[1]-b[n])+abs(a[n]-b[1]));
		cout << ans << endl;
	}
}
```

