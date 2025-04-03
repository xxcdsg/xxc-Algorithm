## [A - 平面最近点对（加强版）](https://vjudge.net/problem/洛谷-P1429)

### [参考点距题](C:\Users\Administrator\Desktop\markdown\计算几何\点距题.md)

## [B - 三维偏序（陌上花开）](https://vjudge.net/problem/洛谷-P3810)

### 题意：求$x[i]\leq x[j],y[i]\leq y[j],z[i]\leq z[j]$的对数

### 思路：一维排序，二维分治，三维树状数组

### 具体的，对一维排序后在中间切两半，那么左右两边的一维就有序了，对这两边先分治后，就只剩下两边对应的对数了

### 然后我们两边对y排序，用双指针保持y的有序，在树状数组中加入第三维数据（只加前面部分的），加上后面部分查询的

### 由于是大于等于，因此考虑完全相同的将它们变成一个点，记录数量

~~重新写了半天没考虑完全相同，血压上来就不写了~~

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define lowbit(x) (x&-x)

const int N = 2e5 + 10;
int n,k;
int tr[N];

void add(int x,int y){
	for(;x <= n;x += lowbit(x)) tr[x] += y;
}
int query(int x){
	int ans = 0;
	for(;x;x -= lowbit(x)) ans += tr[x];
	return ans;
}

struct point{
	int x,y,z;
	int i;
}p[N];
int ans[N],num[N];
bool cmpx(point other,point a){
	if(a.x == other.x){
		if(a.y == other.y)
			return a.z > other.z;
		return a.y > other.z;
	}
	return a.x > other.x;
}
bool cmpy(point other,point a){
	if(a.y == other.y)
		return a.z > other.z;
	return a.y > other.y;
}
void cdq(int l,int r){
	int mid = (l + r) >> 1;
	if(l == r) return;
	cdq(l,mid);
	cdq(mid + 1,r);
	sort(p + l,p + mid + 1,cmpy);
	sort(p + mid + 1,p + r + 1,cmpy);
	int _l = l;
	queue<int> qu;
	for(int i = mid + 1;i <= r;i++){
		while(_l <= mid && p[_l].y <= p[i].y){
			qu.push(p[_l].z);
			add(p[_l].z,1);
			_l++;
		}
		ans[p[i].i] += query(p[i].z);
	}
	while(!qu.empty()){
		add(qu.front(),-1);
		qu.pop();
	}
}
int main()
{
	cin >> n >> k;
	for(int i = 1;i <= n;i++){
		cin >> p[i].x >> p[i].y >> p[i].z;
		p[i].i = i;
	}
	sort(p + 1,p + 1 + n,cmpx);
	cdq(1,n);
	for(int i = 1;i <= n;i++)
		num[ans[i]]++;
	for(int i = 0;i < n;i++)
		cout << num[i] << endl;
}
```

## [C - 火星商店问题](https://vjudge.net/problem/洛谷-P4585)

~~这位更是重量级~~

### 题意：1~n位置，有val固定的值与之后会添加的有日期限制的值，$0\ s\ v$代表给s位置v值，$1\ l\ r\ x\ d$，代表问在$[l,r]$位置范围内，日期在d内与x异或最大可得出的值为

### 思路：对于日期限定的异或问题用可持久化字典树即可，然后区间我们又想到线段树，因此本题就是一个线段树上套可持久化字典树

[可持久化字典树](C:\Users\Administrator\Desktop\markdown\数据结构\高级数据结构\可持久化trie树.md)

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 1e8 + 10,INF = 1e9 + 10;

int n,m,idx;

struct tire{
	int tr[N][2],max_id[N];
	
	void add(int p,int x,int d){
		for(int i = 23;i >= 0;i--){
			int v = x >> i & 1;//第i + 1位为什么
			if(!tr[p][v])
				tr[p][v] = ++idx;
			p = tr[p][v];
			max_id[p] = max(max_id[p],d);
		}
	}
	
	int ask(int C,int p,int d){
		int ans = 0;
		for(int i = 23;i >= 0;i--){
			int v = C >> i & 1;//这一位是什么
			if(max_id[tr[p][v ^ 1]] >= d)//在范围内可以跳转
				p = tr[p][v ^ 1] , ans += (1 << i);
			else
				p = tr[p][v];//否则就选差的
		}
		return ans;
	}
} T;

void insert(int l,int r,int s,int v,int d,int p){
	T.add(p,v,d);
	if(l == r)
		return;
	int mid = (l + r) >> 1;
	if(mid >= s)
		insert(l,mid,s,v,d,p << 1);
	else
		insert(mid + 1,r,s,v,d,p << 1 | 1);
}

int query(int l,int r,int ml,int mr,int x,int d,int p){
	int ans = 0;
	if(l >= ml && r <= mr)
		return T.ask(x,p,d);
	else{
		int mid = (l + r) >> 1;
		if(ml <= mid)
			ans = max(ans,query(l,mid,ml,mr,x,d,p << 1));
		if(mr >= mid + 1)
			ans = max(ans,query(mid + 1,r,ml,mr,x,d,p << 1 | 1));
	}
	return ans;
}

int main(){
	cin >> n >> m;
	idx = n*4 + 10;
	for(int i = 1;i <= n;i++){
		int x;cin >> x;
		insert(1,n,i,x,INF,1);
	}
	int day = 0;
	for(int i = 1;i <= m;i++){
		int op;cin >> op;
		if(op){
			int l,r,x,d;cin >> l >> r >> x >> d;
			cout << query(1,n,l,r,x,max(day - d + 1,0),1) << endl;
		}else{
			day++;
			int s,v;cin >> s >> v;
			insert(1,n,s,v,day,1);
		}
	}
}
```

## [D - Painting Fence](https://vjudge.net/problem/CodeForces-448C)

### 题意：给出每一列的栅栏高度，可以横着或者竖着涂连续一行或一列区域（也就是说不能断开），问最少把所有区域涂满的步数

### 思路：横着将最矮的栅栏都涂掉后就分成了两个部分，分治的将两边都涂完的最小值相加就是这种方法的最小值了，在与不横着涂，只竖着图比较

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int N = 5e3 + 10;
int a[N],b[N];
int f(int l,int r){
	if(l > r)
		return 0;
	if(l == r)
		return 1;
	int mi = 1e9;
	for(int i = l;i <= r;i++)
		mi = min(mi,a[i]);
	int ans = mi;
	int begin = l;
	for(int i = l;i <= r;i++){
		a[i] -= mi;
		if(i == r){
			if(a[i] == 0)
				ans += f(begin,i - 1);
			else
				ans += f(begin,i);
		}
		else if(a[i] == 0){
			if(begin != i){
				ans += f(begin,i - 1);
			}
			begin = i + 1;
		}
	}
	return min(ans,r - l + 1);
}
int main()
{
	int n;cin >> n;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		b[i] = a[i];
	}
	sort(b + 1,b + 1 + n);
	cout << f(1,n) << endl;
}
```

## [E - Optimal Insertion](https://vjudge.net/problem/CodeForces-1601C)

### 题意：将b序列插入a中得到最少的逆序对

### 思路：首先b序列必须先有序，先证明一下

### $b_i,a_j,a_{j+1},a_{j+2}...a_{k},b_{i+1}$像这样的序列，对于$b_i$左边与$b_{i+1}$右边来说，交换两者顺序并不会改变它们对于这两个数的逆序对个数

### 那么对于$b_i$与$b_{i+1}$本身来说，有序当然没有逆序对

### 对于中间的数来说，也当然在前面的数小，后面的数大，所产生的逆序对更少

### 然后怎么插入呢？只要找最优插入位置即可，但最优插入位置较多，会TLE，那么我们就分治，每次都插入中间的数，那么就分成两边，比它的的数只能插在它的后面，小的只能插在前面，只要每次递归范围就小了很多大概$O(nlog_2m)$的复杂度，比$O(n^2)$小多了

### 最后整合为c序列离散化+树状数组求逆序对即可

```cpp
#include<bits/stdc++.h>
using namespace std;

#define lowbit(x) (x&-x)
#define ll long long

const int N = 1e6 + 10,INF = 1e9 + 10;

int a[N],b[N],c[N*2],aim[N],ra[N*2],tr[N*2];
vector<int> tem;
int nn;
int n,m;

void add(int x,int y){
	for(;x <= n + m;x += lowbit(x)) tr[x] += y;
}
int query(int x){
	int ans = 0;
	for(;x;x -= lowbit(x)) ans += tr[x];
	return ans;
}

void Discretization(int& n,vector<int>& ve,int* data,int* ra){//长度，离散化数组，数据，排名
	sort(ve.begin(),ve.end());
	ve.erase(unique(ve.begin(),ve.end()),ve.end());
	for(int i = 1;i <= n;i++) ra[i] = lower_bound(ve.begin(),ve.end(),data[i]) - ve.begin() + 1;
	n = ve.size();
}

pair<int,int> num[N];

void f(int l,int r,int ml,int mr){
	if(l > r)
		return;
	int mid = (l + r) / 2;
	int x = b[mid];//要插入的数
	int mi = ml;
	num[ml].first = 0;
	num[mr].second = 0;
	for(int i = ml + 1;i <= mr;i++){
		num[i].first = num[i - 1].first;
		if(x < a[i - 1])
			num[i].first++;
	}
	for(int i = mr - 1;i >= ml;i--){
		num[i].second = num[i + 1].second;
		if(x > a[i])
			num[i].second++;
	}
	for(int i = ml;i <= mr;i++){
		if(num[i].first + num[i].second < num[mi].first + num[mi].second)
			mi = i;
	}
	aim[mid] = mi;
	f(mid + 1,r,mi,mr);
	f(l,mid - 1,ml,mi);
}

void test(){
	cin >> n >> m;
	//memset(aim,0,sizeof(aim));
	for(int i = 0;i <= n + m;i++)
		tr[i] = 0;
	for(int i = 1;i <= n;i++) cin >> a[i];
	for(int i = 1;i <= m;i++) cin >> b[i];
	sort(b + 1,b + 1 + m);
	f(1,m,1,n + 1);
	int j = 1,k = 0;
	for(int i = 1;i <= n;i++){
		while(aim[j] == i && j <= m){
			c[++k] = b[j];
			j++;
		}
		c[++k] = a[i];
	}
	while(j <= m){
		c[++k] = b[j];
		j++;
	}
	vector<int> tem;
	for(int i = 1;i <= n + m;i++)
		tem.push_back(c[i]);
	nn = n + m;
	Discretization(nn,tem,c,ra);
	ll ans = 0;
	for(int i = n + m;i >= 1;i--){
		add(ra[i],1);
		ans += query(ra[i] - 1);
	}
	cout << ans << endl;
}

int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## [F - Berland Elections](https://vjudge.net/problem/CodeForces-847F)

~~真是又臭又长~~

### 题意：m个人给n个人投票，其中a个人已经投完票了，有k个人能胜利，问每个人的胜利情况

### 1.一定能胜利 2.有可能胜利 3.一定不可能胜利

### 对于同票的，先到该票数的排名靠前

### 0票一定不能胜利，因此有可能少于k人胜利

### 思路：数量范围较小，先暴力将a个人的票投了，然后进行判断，emmm

```cpp
#include<bits/stdc++.h>
using namespace std;
     
#define lowbit(x) (x&-x)
#define ll long long
     
const int N = 101;
     
int ra[N],num[N],st[N];
     
void test(){
   	int n,k,m,a;cin >> n >> k >> m >> a;
   	for(int i = 1;i <= n;i++){
   		st[i] = i;
   		ra[i] = i;
   	}
   	for(int i = 1;i <= a;i++){
   		int x;cin >> x;
   		num[ra[x]]++;
   		while(ra[x] >= 2 && num[ra[x] - 1] < num[ra[x]]){
   			int l = st[ra[x] - 1],r = st[ra[x]];
    		swap(num[ra[x] - 1],num[ra[x]]);
    		swap(st[ra[x]],st[ra[x] - 1]);
    		ra[l]++;
    		ra[r]--;
    	}
    }
    for(int i = 1;i <= n;i++){
    	if(num[ra[i]] == 0){//数量为0的话，就要特殊考虑剩下的没投票的人也是0的情况
    		if(m - a == 0)//0人是不能的
    			cout << 3 << ' ';
    		else if(n == 1)//如果只有一个人那么就可以
    			cout << 1 << ' ';
    		else if(m - a > num[k])//剩下的人都投票给它
    			cout << 2 << ' ';
    		else
    			cout << 3 << ' ';
		}else{
			if(m - a == 0){//没人有票了,那么就不存在有可能胜利的情况
				if(ra[i] > k)
					cout << 3 << ' ';
				else
					cout << 1 << ' ';
			}else{
				if(num[ra[i]] + m - a <= num[k])//全票给它也不够
					cout << 3 << ' ';
				else{//否则就要判断它是一定会胜利或者可能胜利
					int need = 0;//使它后面的人超过它需要的最少票数
					for(int j = ra[i] + 1;j <= min(k + 1,n);j++){//枚举后面的人加上比他大1的数量
						need += num[ra[i]] - num[j] + 1;
					}
					if(need > m - a || n == k)
						cout << 1 << ' ';
					else
						cout << 2 << ' ';
				}
			}
		}
    }
}
int main()
{
   	ios::sync_with_stdio(false);//写了using namespace std;
   	int t = 1;//cin >> t;
   	while(t--){
   		test();
   	}
}
```

## [G - 寒假作业](https://vjudge.net/problem/洛谷-P2717)

### 题意：给定一个长度为 $n$ 的正整数序列 $a_i$，求出有多少个**连续**子序列的平均值不小于 $k$

### 思路：先求个前缀和，那么题意就转换为找$pre[j]-pre[i]\geq k×(j-i)$的对数，如果我们再让所有数减去$k$，那么更简化为了$pre[j]-pre[i]\geq 0$，也就是找$pre[j]\geq pre[i]$的对数，用分治与树状数组均可

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int N = 1e5 + 10;
int tem[N],a[N];
ll f(int l,int r){
	if(l == r){
		if(a[l] >= 0)
			return 1;
		else return 0;
	}
	ll ans = 0,begin = l;
	int mid = (l + r) / 2;
	ans += f(l,mid);
	ans += f(mid + 1,r);
	tem[mid + 1] = a[mid + 1];
	for(int i = mid + 2;i <= r;i++)	
		tem[i] = tem[i - 1] + a[i];
	tem[mid] = a[mid];
	for(int i = mid - 1;i >= l;i--)
		tem[i] = tem[i + 1] + a[i];
	sort(tem + l,tem + mid + 1);
	sort(tem + mid + 1,tem + r + 1);
	ll j = mid + 1;
	for(int i = mid + 1;i <= r;i++){
		while(tem[j - 1] + tem[i] >= 0 && j > l)
			j--;
		ans += mid - j + 1;
	}
	return ans;
}
int main()
{
	int n,k;cin >> n >> k;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		a[i] -= k;
	}
	cout << f(1,n) << endl;
}
```

## [H - 点分治1](https://vjudge.net/problem/洛谷-P3806)

### 淀粉汁模板题

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 2e4 + 10,M = 1e2 + 10;
const int INF = 1e7 + 10;

bool vis[N],able[M];
int n,m,zx,idx;
int zs[N],max_zs[N],ask[M];
int d[N],dis[N];
bool judge[INF];

struct edge{
	int v,w;
	edge* nex;
}ed[N*2];
int ptop;
edge* head[N];

void add(int u,int v,int w){
	ed[ptop].v = v;
	ed[ptop].w = w;
	ed[ptop].nex = head[u];
	head[u] = &ed[ptop];
	ptop++;
}

void get(){
	cin >> n >> m;
	for(int i = 1;i <= n - 1;i++){
		int u,v,w;cin >> u >> v >> w;
		add(u,v,w);
		add(v,u,w);
	}
	for(int i = 1;i <= m;i++)
		cin >> ask[i];
}

void get_zx(int u,int fa,int all){
	zs[u] = 1;
	max_zs[u] = 0;
	for(edge* p = head[u];p != NULL;p = p -> nex){
		int v = p -> v;
		if(vis[v] || v == fa) continue;
		get_zx(v,u,all);
		zs[u] += zs[v];
		max_zs[u] = max(max_zs[u],zs[v]);
	}
	max_zs[u] = max(all - zs[u],max_zs[u]); 
	if(max_zs[u] < max_zs[zx])
		zx = u;
}

void get_dis(int u,int fa,int len){
	d[++idx] = len;
	for(edge* p = head[u];p != NULL;p = p -> nex){
		int v = p -> v,w = p -> w;
		if(vis[v] || v == fa) continue;
		get_dis(v,u,len + w);
	}
}

void calc(int x){
	judge[0] = 1;
	stack<int> st;
	for(edge* p = head[x];p != NULL;p = p -> nex){//所有子树
		int v = p -> v,w = p -> w;
		if(vis[v]) continue;
		idx = 0;
		get_dis(v,x,w);//这个子树上的所有距离
		for(int i = 1;i <= idx;i++){
			for(int j = 1;j <= m;j++){
				if(ask[j] - d[i] >= 0 && ask[j] - d[i] < INF && judge[ask[j] - d[i]])
					able[j] = 1;
			}
		}
		for(int i = 1;i <= idx;i++)//处理完成，放入judge
		{
			if(d[i] <= INF){
				judge[d[i]] = 1;
				st.push(d[i]);
			}
		}
	}
	while(!st.empty()){//清空judge
		judge[st.top()] = 0;
		st.pop();
	}
}

void divid(int x){
	calc(x);//计算经过x的
	vis[x] = 1;
	for(edge* p = head[x];p != NULL;p = p -> nex){
		int v = p -> v;
		if(vis[v]) continue;
		zx = 0;
		get_zx(v,x,zs[v]);
		divid(zx);
	}
}

int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	get();
	zx = 0;
	max_zs[0] = INF;
	get_zx(1,0,n);
	divid(zx);
	for(int i = 1;i <= m;i++){
		if(able[i])
			cout << "AYE" << endl;
		else
			cout << "NAY" << endl;
	}
}
```

## [I - 纸箱堆叠](https://vjudge.net/problem/黑暗爆炸-2253)

### 题意：n个箱子，长宽高分别为$(a^{3i-2}\mod p,a^{3i-1}\mod p,a^{3i}\mod p)$，当箱子的最短边，次短边，长边均严格小于另一个箱子时该箱子可以放在它里面，问最多能塞几个箱子

### 思路：三维偏序求最长序列长度，那么我们记录的就不是对数而是对应箱子作为最大的箱子时所能塞入的最多箱子数量，然后先处理前面的，再处理后面的，那么我们的树状数组要存的就是最大值，用max取代+=即可

```cpp
#include<bits/stdc++.h>//
using namespace std;

#define ll long long
#define lowbit(x) (x&-x)

const int N = 5e4 + 10;

ll a,p,n;

struct data{
	ll x,y,z,cnt;
}da[N];

ll tr[N*3];
void add(int x,ll y){
	for(;x <= n*3;x += lowbit(x)) tr[x] = max(y,tr[x]);
}
void _set(int x,int y){
	for(;x <= n*3;x += lowbit(x)) tr[x] = y;
}
ll query(int x){
	ll ans = 0;
	for(;x;x -= lowbit(x)) ans = max(tr[x],ans);
	return ans;
}

bool cmp_x(data a,data b){
	if(a.x == b.x){
		if(a.y == b.y)
			return a.z < b.z;
		return a.y < b.y;
	}
	return a.x < b.x;
}

bool cmp_y(data a,data b){
	if(a.y == b.y)
		return a.z < b.z;
	return a.y < b.y;
}

void cdq(int l,int r){
	da[l].cnt = max(da[l].cnt,(ll)1);
	if(da[l].x == da[r].x)
		return;
	int mid = (l + r) >> 1;
	while(da[mid - 1].x == da[mid].x) mid--;
	if(mid < l)
		return;
	//开始更新
	cdq(l,mid);
	sort(da + l,da + 1 + mid,cmp_y);//两边都根据y排序
	sort(da + mid + 1,da + r + 1,cmp_y);
	int _l = l;
	stack<int> st;
	for(int i = mid + 1;i <= r;i++){
		while(da[_l].y < da[i].y && _l <= mid){
			add(da[_l].z,da[_l].cnt);
			st.push(da[_l].z);
			_l++;
		}
		da[i].cnt = max(query(da[i].z - 1) + 1,da[i].cnt);
	}
	while(!st.empty()){
		_set(st.top(),0);
		st.pop();
	}
	sort(da + mid + 1,da + r + 1,cmp_x);
	cdq(mid + 1,r);
}

void Discretization(vector<int>& ve){//长度，离散化数组，数据，排名
	sort(ve.begin(),ve.end());
	ve.erase(unique(ve.begin(),ve.end()),ve.end());
}

vector<int> tt;

int main(){
	cin >> a >> p >> n;
	ll x = a,y = (a*a) % p,z = (y * a) % p;
	ll tem = z;
	for(int i = 1;i <= n;i++){
		if(x > y) swap(x,y);
		if(x > z) swap(x,z);
		if(y > z) swap(y,z);
		da[i] = {x,y,z,0};
		x = (x*tem) % p;
		y = (y*tem) % p;
		z = (z*tem) % p;
		tt.push_back(x);
		tt.push_back(y);
		tt.push_back(z);
	}
	Discretization(tt);
	for(int i = 1;i <= n;i++){
		da[i].x = lower_bound(tt.begin(),tt.end(),da[i].x) - tt.begin() + 1;
		da[i].y = lower_bound(tt.begin(),tt.end(),da[i].y) - tt.begin() + 1;
		da[i].z = lower_bound(tt.begin(),tt.end(),da[i].z) - tt.begin() + 1;
	}
	sort(da + 1,da + 1 + n,cmp_x);
	cdq(1,n);
	ll ma = 1;
	for(int i = 1;i <= n;i++)
		ma = max(ma,da[i].cnt);
	cout << ma << endl;
}
```

## [J - Raid](https://vjudge.net/problem/OpenJ_Bailian-3658)

### 题意：从A点集中找与B点集中的点的最短距离

~~水~~

```cpp
#include<bits/stdc++.h>
using namespace std;

#define ll long long
#define db double

const int N = 1e3 + 10;

db ddis(db x1,db y1,db x2,db y2){
	return sqrt(pow(x1-x2,2) + pow(y1-y2,2));
}

struct point{
	db x,y;
}p[N];

int main(){
	int t;cin >> t;
	while(t--){
		int n;cin >> n;
		for(int i = 1;i <= n;i++)
			cin >> p[i].x >> p[i].y;
		db ma = 2e18;
		for(int i = 1;i <= n;i++){
			db x,y;cin >> x >> y;
			for(int j = 1;j <= n;j++)
				ma = min(ma,ddis(p[j].x,p[j].y,x,y));
		}
		printf("%.3lf\n",ma);
	}
}
```

## [M - Evil Straw Warts Live](https://vjudge.net/problem/OpenJ_Bailian-1854)

### 题意：最少将字符串变成回文字符串的相邻交换次数

### 思路：两边同时向中间一个一个对字符，如果一样说明不用换，否则向中间找与左边字符或者右边字符一样的最近字符

```cpp
#include<bits/stdc++.h>
using namespace std;

int ans = 0;

void solve(string s){
	int n = s.size();
	for(int i = 0;i < n / 2;i++){
		if(s[i] == s[n - i - 1])
			continue;
		else{
			int l = i + 1,r = n - i - 2;
			bool f = 0;
			while(r > i){
				if(s[r] == s[i]){
					for(int j = r;j < n - i - 1;j++){
						swap(s[j],s[j + 1]);
						ans++;
					}
					f = 1;
					break;
				}
				if(s[l] == s[n - i - 1]){
					for(int j = l;j > i;j--){
						swap(s[j],s[j - 1]);
						ans++;
					}
					f = 1;
					break;
				}
				r--;
				l++;
			}
			if(!f){
				ans = -1;
				return;
			}
		}
	}
}
int main(){
	int t;cin >> t;
	while(t--){
		string s;cin >> s;
		ans = 0;
		solve(s);
		if(ans == -1)
			cout << "Impossible" << endl;
		else
			cout << ans << endl;
	}
}
```

