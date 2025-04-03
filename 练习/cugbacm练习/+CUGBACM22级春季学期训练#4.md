# [CUGBACM22级春季学期训练#4A](https://vjudge.net/contest/546461)

## [A - 单源最短路径（弱化版）](https://vjudge.net/problem/洛谷-P3371)

### 详见单源最短路专题

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e4 + 10,MAXM = 5e5 + 10;
struct edge{
	int v,val;
	edge* nex;
}ed[MAXM];
edge* head[MAXN];
int ptop = 0;
void add(int x,int y,int val)
{
	ed[ptop].v = y,ed[ptop].val = val;
	ed[ptop].nex = head[x];
	head[x] = &ed[ptop];
	ptop++;
}
bool use[MAXN];
struct pos{
	int dis,p;
	bool operator <(const pos &x)const{
		return dis > x.dis;
	}//重载运算符
};
priority_queue<pos> a;//存点
int dis[MAXN];
int main()
{
	int n,m,s;cin >> n >> m >> s;
	a.push({0,s});
	for(int i = 1;i <= n;i++)
		dis[i] = ((long long)1 << 31) - 1;
	for(int i = 1;i <= m;i++)
	{
		int x,y,v;cin >> x >> y >> v;
		add(x,y,v);
	}
	while(!a.empty())
	{
		int x = a.top().p,predis = a.top().dis;
		a.pop();
		if(use[x])
			continue;
		dis[x] = predis;
		use[x] = 1;
		edge* p = head[x];
		while(p != NULL)
		{
			int y = p -> v,val = p -> val;
			a.push({predis+val,y});
			p = p -> nex;
		}
	}
	for(int i = 1;i <= n;i++)
	cout << dis[i] << ' ';
}
```

## [B - 单源最短路径（标准版）](https://vjudge.net/problem/洛谷-P4779)

### 同上

## [C - 线段树 1](https://vjudge.net/problem/洛谷-P3372)

### ~~最烦线段树了，~~详见线段树专题

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
int n,m;
long long a[MAXN];
#define ll long long 
inline ll read() {
    ll X=0,w=1; char c=getchar();
    while (c<'0'||c>'9') { if (c=='-') w=-1; c=getchar(); }
    while (c>='0'&&c<='9') X=(X<<3)+(X<<1)+c-'0',c=getchar();
    return X*w;
}
inline void write(ll x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}
struct segment_tree{
	long long v,lazy;
	int l,r;
}st[MAXN * 4];//四倍空间，记录区间范围，lazy_tag和值
void build_tree(int x,int L,int R)
{
	st[x].l = L,st[x].r = R;
	if(L == R)//找到底部
	st[x].v = a[L];
	else//没找到底部，分解
	{
		int mid = (L + R) / 2;
		build_tree((x<<1),L,mid);
		build_tree((x<<1) + 1,mid + 1,R);
		st[x].v = st[(x<<1)].v + st[(x<<1) + 1].v;
	}
}
void push_down(int x)
{
	if(st[x].lazy == 0)
	return;
	st[x].v += st[x].lazy * (st[x].r - st[x].l + 1);
	st[(x<<1)].lazy += st[x].lazy;
	st[(x<<1) + 1].lazy += st[x].lazy;
	st[x].lazy = 0;
}
long long find(int x,int L,int R)
{
	long long sum = 0;
	if(st[x].l == L && st[x].r == R)//包含整个区间
		return st[x].v + st[x].lazy * (R - L + 1);
	else
	{
		push_down(x);//把lazy推给下面区间
		int mid = (st[x].l + st[x].r) / 2;
		if(L <= mid)
		sum += find((x<<1),L,min(R,mid));
		if(R >= mid + 1)
		sum += find((x<<1) + 1,max(L,mid + 1),R);
		return sum;
	}
}
void add(int x,int L,int R,int va)
{
	if(st[x].l == L && st[x].r == R)//包含整个区间
		st[x].lazy += va;
	else
	{
		st[x].v += (R - L + 1) * va;
		int mid = (st[x].l + st[x].r) / 2;
		if(L <= mid)
		add((x<<1),L,min(R,mid),va);
		if(R >= mid + 1)
		add((x<<1) + 1,max(L,mid + 1),R,va);
	}
}
int main()
{
	n = read(),m = read();
	for(int i = 1;i <= n;i++)
		a[i] = read();
	build_tree(1,1,n);
	for(int i = 1;i <= m;i++)
	{
		int type = read(),x = read(),y = read();
		if(type&1)
		{
			int k = read();
			add(1,x,y,k);
		}
		else
		{
			write(find(1,x,y));
			putchar('\n');
		}
	}
}
```

## [D - 线段树 2](https://vjudge.net/problem/洛谷-P3373)

### 同上

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
int n,m,p;
long long a[MAXN];
#define ll long long 
inline ll read() {
    ll X=0,w=1; char c=getchar();
    while (c<'0'||c>'9') { if (c=='-') w=-1; c=getchar(); }
    while (c>='0'&&c<='9') X=(X<<3)+(X<<1)+c-'0',c=getchar();
    return X*w;
}
inline void write(ll x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}
struct segment_tree{
	long long v,lazy,mul;
	int l,r;
}st[MAXN * 4];//四倍空间，记录区间范围，lazy_tag和值
void build_tree(int x,int L,int R)
{
	st[x].l = L,st[x].r = R;
	if(L == R)//找到底部
	st[x].v = a[L];
	else//没找到底部，分解
	{
		int mid = (L + R) / 2;
		build_tree((x<<1),L,mid);
		build_tree((x<<1) + 1,mid + 1,R);
		st[x].v = st[(x<<1)].v + st[(x<<1) + 1].v;
	}
}
void push_down(int x)
{
	st[(x<<1)].v = (st[(x<<1)].v * st[x].mul + st[x].lazy * (st[(x<<1)].r - st[(x<<1)].l + 1)) % p;
	st[(x<<1) + 1].v = (st[(x<<1) + 1].v * st[x].mul + st[x].lazy * (st[(x<<1) + 1].r - st[(x<<1) + 1].l + 1)) % p;
	st[(x<<1)].lazy = (st[(x<<1)].lazy * st[x].mul + st[x].lazy) % p;
	st[(x<<1) + 1].lazy = (st[(x<<1) + 1].lazy * st[x].mul + st[x].lazy) % p;
	st[(x<<1)].mul = (st[(x<<1)].mul * st[x].mul) % p;
	st[(x<<1) + 1].mul = (st[(x<<1) + 1].mul * st[x].mul) % p;
	st[x].lazy = 0;
	st[x].mul = 1;
}
long long find(int x,int L,int R)
{
	long long sum = 0;
	if(st[x].l == L && st[x].r == R)//包含整个区间
		return st[x].v;
	else
	{
		push_down(x);//把lazy推给下面区间
		int mid = (st[x].l + st[x].r) / 2;
		if(L <= mid)
		sum = (sum + find((x<<1),L,min(R,mid))) % p;
		if(R >= mid + 1)
		sum = (sum + find((x<<1) + 1,max(L,mid + 1),R)) % p;
		return sum;
	}
}
void add(int x,int L,int R,int va)
{
	if(st[x].l == L && st[x].r == R)//包含整个区间
	{
		st[x].lazy = (st[x].lazy + va) % p;
		st[x].v = (st[x].v + va * (R - L + 1)) % p;
	}
	else
	{
		push_down(x);
		int mid = (st[x].l + st[x].r) / 2;
		if(L <= mid)
		add((x<<1),L,min(R,mid),va);
		if(R >= mid + 1)
		add((x<<1) + 1,max(L,mid + 1),R,va);
		st[x].v = st[(x<<1)].v + st[(x<<1) + 1].v;
	}
}
void mul(int x,int L,int R,int va)//乘法
{
	if(st[x].l == L && st[x].r == R)//包含整个区间
	{
		st[x].mul = (st[x].mul * va) % p;
		st[x].lazy = (st[x].lazy * va) % p;
		st[x].v = (st[x].v * va) % p;
	}
	else
	{
		push_down(x);
		int mid = (st[x].l + st[x].r) / 2;
		if(L <= mid)
		mul((x<<1),L,min(R,mid),va);
		if(R >= mid + 1)
		mul((x<<1) + 1,max(L,mid + 1),R,va);
		st[x].v = st[(x<<1)].v + st[(x<<1) + 1].v;
	}
}
int main()
{
	n = read(),m = read(),p = read();
	for(int i = 1;i <= n;i++)
		a[i] = read();
	for(int i = 1;i <= 4*n;i++)
		st[i].mul = 1;
	build_tree(1,1,n);
	for(int i = 1;i <= m;i++)
	{
		int type = read(),x = read(),y = read();
		if(type == 1)
		{
			int k = read();
			mul(1,x,y,k);
		}
		else if(type == 2)
		{
			int k = read();
			add(1,x,y,k);
		}
		else
		{
			write(find(1,x,y));
			putchar('\n');
		}
	}
}
```

## [E - Recent Actions](https://vjudge.net/problem/CodeForces-1799A)

### 题意：一开始你的历史记录为$1,2,3,4......n$，每次加入一个大于n的数，或者将这个大于n的数提到最前面，问一开始的1到n这些数什么时候离开范围，如果没有离开就输出-1

### 思路：记录新加的数是否用过，如果没用过就要把1~n最后一个数踢出去，不然不变

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 5e4 + 10;
int num[MAXN];
bool use[MAXN*2];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n,m;cin >> n >> m;
		memset(num,-1,sizeof(num));
		memset(use,0,sizeof(use));
		int ptop = n;
		for(int i = 1;i <= m;i++)
		{
			int x;cin >> x;
			if(use[x])
				continue;
			use[x] = 1;
			if(ptop != 0)
				num[ptop--] = i;
		}
		for(int i = 1;i <= n;i++)
			cout << num[i] << ' ';
		cout << endl;
	}
}
```

## [F - Beppa and SwerChat](https://vjudge.net/problem/CodeForces-1776H)

### 题意：一条序列，如果一个人上线就把这个人的序号拉到最前面，现给出前后序列，问在这期间最少上线人数

### 思路：找最少上线人数即找最多没上线人数，上线的会被提到最前面，那么最后面的就都是没变的，并且没上线的话，顺序就不变，所以就在后序列中一个一个在前序列向前找数即可

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
int a[MAXN],b[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		for(int i = 1;i <= n;i++)
			cin >> a[i];
		int ptop = n;
		for(int i = 1;i <= n;i++)
			cin >> b[i];
		int num = n;
		int pptop = n;
		while(pptop)
		{
			if(a[pptop] == b[ptop])
			{
				num--;
				ptop--;
			}
			pptop--;
		}
		cout << num << endl;
	}
}
```

