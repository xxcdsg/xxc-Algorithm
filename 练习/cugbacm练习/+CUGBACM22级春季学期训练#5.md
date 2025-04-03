# [CUGBACM22级春季学期训练#5](https://vjudge.net/contest/546973#overview)

## [A - 扫描线](https://vjudge.net/problem/洛谷-P5490)

### 详见扫描线

### 将y离散化，并转换为区间，用线段树存，分别记录被覆盖次数和被覆盖长度，果然被覆盖次数存在说明完全被覆盖，如果不存在就求左右两子节点的覆盖长度然后相加

## [B - 线性筛素数](https://vjudge.net/problem/洛谷-P3383)

### 读素数，把后面关于这个的倍数排除

```c++
#include<bits/stdc++.h>
using namespace std;//new
inline int read() {
    int X=0,w=1; char c=getchar();
    while (c<'0'||c>'9') { if (c=='-') w=-1; c=getchar(); }
    while (c>='0'&&c<='9') X=(X<<3)+(X<<1)+c-'0',c=getchar();
    return X*w;
}
inline void write(int x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}
const int MAXN = 1e8 + 10,MAXM = 1e7 + 10;
bool prime[MAXN] = {0};
int ve[MAXM];
int main()
{
	int n = read(),q = read();
	int nn = sqrt(n);
	for(int i = 2;i <= nn;i++)
	{
		if(prime[i])
		continue;
		else
		{
			int tem = i * 2;
			while(tem <= n)
			{
				prime[tem] = 1;
				tem += i;
			}
		}
	}
	int ptop = 0;
	for(int i = 2;i <= n;i++)
	{
		if(prime[i])
		continue;
		ve[ptop++] = i;
	}
	for(int i = 1;i <= q;i++)
	{
		int tem = read();
		write(ve[tem - 1]);
		putchar('\n');
	}
}
```

## [C - 树状数组 2](https://vjudge.net/problem/洛谷-P3368)

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 5e6 + 10;//new
inline long long read() {
    long long X=0,w=1; char c=getchar();
    while (c<'0'||c>'9') { if (c=='-') w=-1; c=getchar(); }
    while (c>='0'&&c<='9') X=(X<<3)+(X<<1)+c-'0',c=getchar();
    return X*w;
}
inline void write(long long x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}
int lowbit(int x)
{
	return (x&-x);
}
int a[MAXN] = {0};
int n;
void build_tree()
{
    int pre = 0;
	for(int i = 1;i <= n - 1;i++)
	{
	    int x = read();
		a[i] +=  x - pre;
		pre = x;
		a[i + lowbit(i)] += a[i];
	}
	a[n] += read() - pre;
}
void add(int x,int y,int k)
{
	a[x] += k;
	while((x += lowbit(x)) <= n)a[x] += k;
	y++;
	a[y] -= k;
	while((y += lowbit(y)) <= n)a[y] -= k;
}
long long find(int x)
{
	long long output = 0;
	while(x)
	{
		output += a[x];
		x -= lowbit(x);
	}
	return output;
}
int main()
{
	n = read();
	int m;m = read();
	build_tree();
	for(int i = 1;i <= m;i++)
	{
		int op = read(),x = read();
		if(op&1)
		{
			int y = read(),k = read();
			add(x,y,k);
		}
		else
		{
			write(find(x));
			putchar('\n');
		}
	}
}
```

## [D - AND Sorting](https://vjudge.net/problem/CodeForces-1682B)

### 题意：$p_i\&p_j=X$可以交换，求让$0到n-1$递增排列需要的$X$的最大值

### 思路：$X=p_{k1}\&p_{k2}\&......p_{kt}(p_k为参与交换的)$如果要$X$最大，就要让参与交换的尽量小，也就是说让那些不在正确位置的$\&$即可

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e5 + 10;//new
int x;
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		int ans = 0;
		for(int i = 0;i <= 25;i++)
			ans |= (1 << i);
		for(int i = 0;i <= n - 1;i++)
		{
			cin >> x;
			if(x != i)
				ans &= x;
		}
		cout << ans << endl;
	}
}
```

## [E - LIS or Reverse LIS?](https://vjudge.net/problem/CodeForces-1682C)

### 题意：可以重新安排数列的顺序，求严格递增子序列和翻转过来的严格递增子序列的最小值的最大值

### 思路：一半递增，一半递减，考虑有三个相同数的情况

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e5 + 10;//new
int a[MAXN],b[MAXN];
int main()
{
	int t;cin >> t;
	while(t--)
	{
		int n;cin >> n;
		int ptop = 0;
		for(int i = 1;i <= n;i++)
			cin >> a[i];
		sort(a + 1,a + 1 + n);
		int num = 2;
		for(int i = 3;i <= n;i++)
			if(a[i] != a[i - 2])
				num++;
		cout << (num + 1) / 2<< endl;
	}
}
```

