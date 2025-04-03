# K-D Tree

## 主要思想：将空间分割为许多份，方便查询内部的点，优先查询比较可能的区间，再查询可能性较低的区间，排除较多不可能区间即内部点，实现更少的比较

### [KdTree-KNN算法可视化](https://www.bilibili.com/video/BV1Ag411a7i4/?spm_id_from=333.337.search-card.all.click)

## 具体实现：

### 前置函数：nth_element

### 作用：取第k小的数放在k位置，让前面的都小于等于它，后面的都比它大于等于它，但乱序

### 复杂度：$O(n)$

### 那么在我们的K-D Tree中就可以用该函数将节点分割而不用sort($O(nlogn)$)

### *1.存点:*需要k维的数据，读入，还有距离函数（具体问题具体分析），存n个点与选择进行的点 

```cpp
struct point{
	double x[2];//展示的为二维
	void read(){
		cin >> x[0] >> x[1];//读入
	}
	friend double dis(const point &a,const point &b){//距离函数
		return _pow(a.x[0] - b.x[0]) + _pow(a.x[1] - b.x[1]);
	}
}p[N],no;
```

### *2.cmp函数：*以什么维度来排序

```cpp
int D;
inline bool cmp(const point &a,const point &b){//排序
	if(a.x[D] == b.x[D])
		return a.x[D^1] < b.x[D^1];
	return a.x[D] < b.x[D];
}
```

### *3.树节点：*左右儿子节点，维度范围

```cpp
struct KD_tree{
	double mx[2],mi[2];//二维的范围
	int l,r;//左右子节点
}tr[N];
```

### *4.更新函数：*子节点更新范围后要扩大父节点的范围

```cpp
void updata(int fa,int son){//给父节点更新
	chmax(tr[fa].mx[0],tr[son].mx[0]);
	chmax(tr[fa].mx[1],tr[son].mx[1]);
	chmin(tr[fa].mi[0],tr[son].mi[0]);
	chmin(tr[fa].mi[1],tr[son].mi[1]);
}
void updata(int fa,point &a){//赋初值
	tr[fa].mx[0] = tr[fa].mi[0] = a.x[0];
	tr[fa].mx[1] = tr[fa].mi[1] = a.x[1];
}
```

### *5.建树*

```cpp
int build(int l,int r){
	int mid = (l + r) >> 1;
	nth_element(p + l,p + mid,p + r + 1,cmp);//确定中间点
	D ^= 1;//换维度
	updata(mid,p[mid]);//给树范围先定在对应的点
	if(mid > l) updata(mid,tr[mid].l = build(l,mid - 1));
	if(mid < r) updata(mid,tr[mid].r = build(mid + 1,r));
	return mid;
}
```

### *6.估值*

```cpp
double f_v(int x){//现在的点到指定范围的最小距离(也可以改成最大)
	if(x == 0)
		return INF;
	double x0 = 0;
	if(no.x[0] > tr[x].mx[0]) x0 = no.x[0] - tr[x].mx[0];
	if(no.x[0] < tr[x].mi[0]) x0 = tr[x].mi[0] - no.x[0];
	x0 = _pow(x0);
	if(no.x[1] > tr[x].mx[1]) return _pow(tr[x].mx[1] - no.x[1]) + x0;
	if(no.x[1] < tr[x].mi[1]) return _pow(tr[x].mi[1] - no.x[1]) + x0;
	return x0;
}
```

### *7.询问*

```cpp
void query(int x){
	chmin(ans,dis(no,p[x]));
	double vl = f_v(tr[x].l),vr = f_v(tr[x].r);
	if(vl < vr){//先搜最可能的
		if(vl < ans){
			query(tr[x].l);
			if(vr < ans)
				query(tr[x].r);
		}
	}else{
		if(vr < ans){
			query(tr[x].r);
			if(vl < ans)
				query(tr[x].l);
		}
	}
}
```

## 例题：

### [P1429 平面最近点对（加强版）](https://www.luogu.com.cn/problem/P1429)

### [P7883 平面最近点对（加强加强版）](https://www.luogu.com.cn/problem/P7883)

### [P6247 [SDOI2012] 最近最远点对](https://www.luogu.com.cn/problem/P6247)

### ~~[P1452 [USACO03FALL] Beauty Contest G /【模板】旋转卡壳](https://www.luogu.com.cn/problem/P1452)~~

### 对于这些最近最远点均可用该方法来做

### 最近为query1，ans1，f_v1；最远为query2，ans2，f_v2；问平方就将double define为long long然后将%.4lf改成%lld即可，问距离就反过来该回去

```cpp
#include<bits/stdc++.h>
using namespace std;
#define double long long
const int N = 4e5 + 10;
const double INF = 1e18;

int n;

inline double _pow(double x){
	return x*x;
}
void chmin(double &a,const double &b){
	a = a < b ? a : b;
}
void chmax(double &a,const double &b){
	a = a > b ? a : b;
}

struct point{
	double x[2];
	void read(){
		cin >> x[0] >> x[1];
	}
	friend double dis(const point &a,const point &b){
		return _pow(a.x[0] - b.x[0]) + _pow(a.x[1] - b.x[1]);
	}
}p[N],no;

int D;
inline bool cmp(const point &a,const point &b){
	if(a.x[D] == b.x[D])
		return a.x[D^1] < b.x[D^1];
	return a.x[D] < b.x[D];
}

struct KD_tree{
	double mx[2],mi[2];
	int l,r;
}tr[N];
void updata(int fa,int son){
	chmax(tr[fa].mx[0],tr[son].mx[0]);
	chmax(tr[fa].mx[1],tr[son].mx[1]);
	chmin(tr[fa].mi[0],tr[son].mi[0]);
	chmin(tr[fa].mi[1],tr[son].mi[1]);
}
void updata(int fa,point &a){
	tr[fa].mx[0] = tr[fa].mi[0] = a.x[0];
	tr[fa].mx[1] = tr[fa].mi[1] = a.x[1];
}

int build(int l,int r){
	int mid = (l + r) >> 1;
	nth_element(p + l,p + mid,p + r + 1,cmp);
	D ^= 1;
	updata(mid,p[mid]);//给树范围先定在对应的点
	if(mid > l) updata(mid,tr[mid].l = build(l,mid - 1));
	if(mid < r) updata(mid,tr[mid].r = build(mid + 1,r));
	return mid;
}

double f_v1(int x){
	if(x == 0)
		return INF;
	double x0 = 0;
	if(no.x[0] > tr[x].mx[0]) x0 = no.x[0] - tr[x].mx[0];
	if(no.x[0] < tr[x].mi[0]) x0 = tr[x].mi[0] - no.x[0];
	x0 = _pow(x0);
	if(no.x[1] > tr[x].mx[1]) return _pow(tr[x].mx[1] - no.x[1]) + x0;
	if(no.x[1] < tr[x].mi[1]) return _pow(tr[x].mi[1] - no.x[1]) + x0;
	return x0;
}

double ans1 = INF,ans2 = 0;
void query1(int x){
	chmin(ans1,dis(no,p[x]));
	double vl = f_v1(tr[x].l),vr = f_v1(tr[x].r);
	if(vl < vr){
		if(vl < ans1){
			query1(tr[x].l);
			if(vr < ans1)
				query1(tr[x].r);
		}
	}else{
		if(vr < ans1){
			query1(tr[x].r);
			if(vl < ans1)
				query1(tr[x].l);
		}
	}
}

double f_v2(int x){
	if(x == 0)
		return 0;
	return max(_pow(tr[x].mx[0] - no.x[0]),
				_pow(tr[x].mi[0] - no.x[0])) + 
			max(_pow(tr[x].mx[1] - no.x[1]),
				_pow(tr[x].mi[1] - no.x[1]));
}

void query2(int x){
	chmax(ans2,dis(no,p[x]));
	double vl = f_v2(tr[x].l),vr = f_v2(tr[x].r);
	if(vl > vr){
		if(vl > ans2){
			query2(tr[x].l);
			if(vr > ans2)
				query2(tr[x].r);
		}
	}else{
		if(vr > ans2){
			query2(tr[x].r);
			if(vl > ans2)
				query2(tr[x].l);
		}
	}
}

int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	cin >> n;
	for(int i = 1;i <= n;i++)
		p[i].read();
	int root = build(1,n);
	for(int i = 2;i <= n;i++){
		no = p[i];
		p[i] = p[i - 1];
		query2(root);
		p[i] = no;
	}
	printf("%lld",ans2);
}
```

### [P4357 [CQOI2016] K 远点对](https://www.luogu.com.cn/problem/P4357)

#### 虽然正解为旋转卡壳做$k/2$，每次做都去掉那两个距离最大的点并且加入与其他点的距离，并且K-D tree被最后一个hack数据卡了，但在没有毒瘤数据的情况下复杂度还是比较优秀的

#### 思路：先在小根堆优先队列内放k个0，然后每次查找都访问堆顶的元素，如果比堆顶元素大说明有可能是前k大的数，将堆顶pop掉，然后加入这个数，否则肯定在k名开外，不管它即可

#### 本来是2k个元素，将交换两点也考虑到，但是这样常数就比较大，因此优化为k个元素并且不考虑已经完全搜索过的点，并且因为是从前往后搜索的，每当最右边的访问都被访问过就说明它不可能有贡献了，就可以return了，不过即使这样优化+$O_2$，让前两个hack数据到$200ms$以内都无法通过最后一个hack数据，不过这样在正常题目中也够用了吧

```cpp
#include<bits/stdc++.h>
using namespace std;
#define double long long
#define ll long long
const int N = 1e5 + 10;
const double INF = 1e18;

int n,k;

inline double _pow(double x){
	return x*x;
}
void chmin(double &a,const double &b){
	a = a < b ? a : b;
}
void chmax(double &a,const double &b){
	a = a > b ? a : b;
}

struct point{
	double x[2];
	void read(){
		cin >> x[0] >> x[1];
	}
	friend double dis(const point &a,const point &b){
		return _pow(a.x[0] - b.x[0]) + _pow(a.x[1] - b.x[1]);
	}
}p[N],no;

int D;
inline bool cmp(const point &a,const point &b){
	if(a.x[D] == b.x[D])
		return a.x[D^1] < b.x[D^1];
	return a.x[D] < b.x[D];
}

struct KD_tree{
	double mx[2],mi[2];
	int l,r;
	int rr;
}tr[N];
void updata(int fa,int son){
	chmax(tr[fa].mx[0],tr[son].mx[0]);
	chmax(tr[fa].mx[1],tr[son].mx[1]);
	chmin(tr[fa].mi[0],tr[son].mi[0]);
	chmin(tr[fa].mi[1],tr[son].mi[1]);
}
void updata(int fa,point &a){
	tr[fa].mx[0] = tr[fa].mi[0] = a.x[0];
	tr[fa].mx[1] = tr[fa].mi[1] = a.x[1];
}

int build(int l,int r){
	int mid = (l + r) >> 1;
	tr[mid].rr = r;
	nth_element(p + l,p + mid,p + r + 1,cmp);
	D ^= 1;
	updata(mid,p[mid]);//给树范围先定在对应的点
	if(mid > l) updata(mid,tr[mid].l = build(l,mid - 1));
	if(mid < r) updata(mid,tr[mid].r = build(mid + 1,r));
	return mid;
}

/*double f_v1(int x){
	if(x == 0)
		return INF;
	double x0 = 0;
	if(no.x[0] > tr[x].mx[0]) x0 = no.x[0] - tr[x].mx[0];
	if(no.x[0] < tr[x].mi[0]) x0 = tr[x].mi[0] - no.x[0];
	x0 = _pow(x0);
	if(no.x[1] > tr[x].mx[1]) return _pow(tr[x].mx[1] - no.x[1]) + x0;
	if(no.x[1] < tr[x].mi[1]) return _pow(tr[x].mi[1] - no.x[1]) + x0;
	return x0;
}

double ans1 = INF,ans2 = 0;
void query1(int x){
	chmin(ans1,dis(no,p[x]));
	double vl = f_v1(tr[x].l),vr = f_v1(tr[x].r);
	if(vl < vr){
		if(vl < ans1){
			query1(tr[x].l);
			if(vr < ans1)
				query1(tr[x].r);
		}
	}else{
		if(vr < ans1){
			query1(tr[x].r);
			if(vl < ans1)
				query1(tr[x].l);
		}
	}
}*/

priority_queue<ll,vector<ll>,greater<ll> > qu;

inline double f_v2(int x){
	if(x == 0)
		return 0;
	return max(_pow(tr[x].mx[0] - no.x[0]),
				_pow(tr[x].mi[0] - no.x[0])) + 
			max(_pow(tr[x].mx[1] - no.x[1]),
				_pow(tr[x].mi[1] - no.x[1]));
}

bool use[N],unable[N];

inline void query2(int x){
	if(use[tr[x].rr]) return;
	if(!use[x]){
		ll tem = dis(no,p[x]);
		if(tem > qu.top()){
			qu.pop();
			qu.push(tem);
		}
	}
	double vl = f_v2(tr[x].l),vr = f_v2(tr[x].r);
	if(vl > vr){
		if(vl > qu.top()){
			query2(tr[x].l);
			if(vr > qu.top())
				query2(tr[x].r);
		}
	}else{
		if(vr > qu.top()){
			query2(tr[x].r);
			if(vl > qu.top())
				query2(tr[x].l);
		}
	}
}

int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	cin >> n >> k;
	for(int i = 1;i <= n;i++)
		p[i].read();
	for(int i = 1;i <= k;i++)
		qu.push(0);
	int root = build(1,n);
	no = p[1];
	p[1] = p[2];
	query2(root);
	p[1] = no;
	use[1] = 1;
	for(int i = 2;i <= n;i++){
		no = p[i];
		p[i] = p[i - 1];
		query2(root);
		p[i] = no;
		use[i] = 1;
	}
	printf("%lld",qu.top());
}
```

## 偏序问题

#### 本来我研究K-D Tree就是为了解决逆天的四维偏序问题，~~那么不好好干它怎么可以呢~~

#### ~~好像还是cdq套cdq比较快~~

#### 先来个简单的三维偏序

### [P3810 【模板】三维偏序（陌上花开）](https://www.luogu.com.cn/problem/P3810)
