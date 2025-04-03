# [CUGBACM22级春季学期训练#6](https://vjudge.net/contest/547120)

## [A - 最长公共子序列](https://vjudge.net/problem/洛谷-P1439)

### 题意：不重复的1~n的两个排列，求最长子序列

### 思路：按第一个的出现顺序排个引索，对第二个序列根据引索求最长严格递增子序列即可

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
int a[MAXN];
int c[MAXN];
int main()
{
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
	{
		int x;cin >> x;
		a[x] = i;
	}
	int ptop = 0;
	cin >> c[++ptop];
	for(int i = 2;i <= n;i++)
	{
		int x;cin >> x;
		if(a[c[ptop]] < a[x])
			c[++ptop] = x;
		else
		{
			int pos = ptop - 1;
			while(a[c[pos]] >= a[x])
				pos--;
			c[pos + 1] = x;
		}
	}
	cout << ptop << endl;
}
```

## [B - 最近公共祖先（LCA）](https://vjudge.net/problem/洛谷-P3379)

### 重点：记录深度，一个节点的$2^n$的祖先节点等价于一个节点的$2^{n-1}$祖先的$2^{n-1}$的祖先

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAX = 511111;
struct ss{
	int val;
	int nex;
}p[MAX << 1];
int head[MAX],po;
int depth[MAX],fa[MAX][30],lg[MAX];
void input(int x,int y)
{
	p[++po].val = y;
	p[po].nex = head[x];
	head[x] = po;
}
void dfs(int son,int dad)
{
	fa[son][0] = dad;
	depth[son] = depth[dad] + 1;
	for(int i = 1;i <= lg[depth[son]];++i)
		fa[son][i] = fa[fa[son][i - 1]][i - 1];
	for(int i = head[son];i;i = p[i].nex)
		if(p[i].val != dad)
			dfs(p[i].val,son);
}
int find(int x,int y)
{
	if(depth[x] < depth[y])
		swap(x,y);
	while(depth[x] > depth[y])
		x = fa[x][lg[depth[x] - depth[y]] - 1];
	if(x == y)
		return x;
	for(int i = lg[depth[x]] - 1;i >= 0;i--)
		if(fa[x][i] != fa[y][i])
			x = fa[x][i],y = fa[y][i];
	return fa[x][0];
}
int main()
{
	int n,m,s;
	scanf("%d %d %d",&n,&m,&s);
	for(int i = 1;i <= n - 1;i++)
	{
		int x,y;
		scanf("%d %d",&x,&y);
		input(x,y);
		input(y,x);
	}
	for(int i = 1;i <= n;i++)
		lg[i] = lg[i - 1] + (1 << lg[i - 1]== i);
	dfs(s,0);
	for(int i = 1;i <= m;i++)
	{
		int x,y;
		scanf("%d %d",&x,&y);
		printf("%d\n",find(x,y));
	}
}
```

## [C - KMP字符串匹配](https://vjudge.net/problem/洛谷-P3375)

```c++
void fnex(string str)
{
    int nex[0] = ptop = -1;
    int i = 0;
    int end = str.size();
    while(i < end)
    {
        if(ptop == -1 || str[i] == str[ptop])
            nex[++i] = ++ptop;
        else
            ptop = nex[ptop];
    }
}
```

## [D - Likes](https://vjudge.net/problem/CodeForces-1802A)

### 题意：正数代表第i个人点赞，负数代表第i个人取消点赞，问时间t最大和最小的点赞数

### 思路：全点赞最后取消最大，一个人点赞后就取消最小，那么我们只要记录点赞次数和取消点赞次数即可

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int t;cin >> t;
	while(t--){
		int n;cin >> n;
		int down = 0,num = 0;
		for(int i = 1;i <= n;i++)
		{
			int x;cin >> x;
			if(x < 0)
				down++;
			else
				num++;
		}
		for(int i = 1;i <= num;i++)
			cout << i << ' ';
		for(int i = num - 1;i >= num - down;i--)
			cout << i << ' ';
		cout << endl;
		for(int i = 1;i <= down;i++)
			cout << 1 << ' ' << 0 << ' ';
		for(int i = 1;i <= num - down;i++)
			cout << i << ' ';
		cout << endl;
	}
}
```

## [E - Settlement of Guinea Pigs](https://vjudge.net/problem/CodeForces-1802B)

### 题意：1代表买了一只不知道性别的豚鼠，2代表来了个医生来判断所有豚鼠的性别，一个笼子能放同性别的最多两只豚鼠，问一个序列最少需要的笼子个数

### 思路：在医生来之前，有几只豚鼠就要买几个笼子来存，探明之后考虑最坏情况，奇数就让多余的豚鼠在多余的笼子里，也就是(n+1)/2，偶数就让雌雄都是奇数个，数量就为(n-2)/2+2，合并一下就是n/2+1

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		int num = 0,ans = 0,temans = 0;
		for(int i = 1;i <= n;i++)
		{
			int op;cin >> op;
			if(op == 1)
			{
				temans++,num++;
				ans = max(temans,ans);
			}
			else
			{
				if(num != 0)
				temans = num / 2 + 1;
				ans = max(temans,ans);
			}
		}
		cout << ans << endl;
	}
}
```

## [F - Two Towers](https://vjudge.net/problem/CodeForces-1795A)

### 题意：两个塔有红方块和蓝方块组成，只能从塔顶拿方块到另外的塔顶，问能否让两个塔的颜色相同的方块不相邻

### 思路：头和头相连遍历，如果有没有相同颜色相邻或只有一个那就可以分成两个塔，否则就不行

```c++
#include<bits/stdc++.h>
using namespace std;
string str1,str2;
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n,m;cin >> n >> m;
		cin >> str1 >> str2;
		int num = 0;
		for(int i = 1;i < n;i++)
			if(str1[i - 1] == str1[i])
				num++;
		if(str1[n - 1] == str2[m - 1]) num++;
		for(int i = 1;i < m;i++)
			if(str2[i - 1] == str2[i])
				num++;
		if(num >= 2)
			cout << "NO" << endl;
		else
			cout << "YES" << endl;
	}
}
```
