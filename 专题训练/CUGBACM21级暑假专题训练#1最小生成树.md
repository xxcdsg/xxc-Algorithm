# CUGBACM21级暑假专题训练#1最小生成树

## [A - Jungle Roads](https://vjudge.net/problem/OpenJ_Bailian-1251)

## 题意：将字符作为节点，求最小生成树

## 懂得都懂，算复习一下最小生成树怎么求

## *1.kruskal算法：*每次取最短的边，如果两个端点不在同一集合就加入最小生成树，否则不加入

```cpp
int ans;
struct edge{
    int u,v,len;
    bool operator <(const edge &ohter)const{
        return len < other.len;
    }
}
vector<pair<int,int>> _ed[N];//存生成树
void kruskal(){
    sort(ed + 1,ed + 1 + m);
    for(int i = 1;i <= m;i++){
        if(find(ed[i].u) != find(ed[i].v)){
            pre[find(ed[i].u)] = find(ed[i].v);
            ans += ed[i].len;
            _ed[ed[i].u].push_back({ed[i].v,ed[i].len});
        }
    }
}
```

## *2.pirm算法：*创一个优先队列，从一个点开始，加入它所有的边，取最小的，如果相连的那个点还没被访问过，就加入，然后加入新点相连的边，直到所有节点都被访问过

### ~~还是kruskal算法比较好写~~

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 28;
bool use[N];
struct edge{
	int len,u,v;
	bool operator <(const edge &other)const{
		return len < other.len;
	}
}ed[N*N];
int ptop = 0,sum = 0;
int a[N];
void init(int n){
	ptop = 0;
	for(int i = 1;i <= n;i++)
		a[i] = i;
}
int find(int x){
	if(a[x] == x) return a[x];
	else return a[x] = find(a[x]);
}
void kru(){
	sort(ed + 1,ed + 1 + ptop);
	for(int i = 1;i <= ptop;i++){
		if(find(ed[i].u) != find(ed[i].v)){
			sum += ed[i].len;
			a[find(ed[i].u)] = find(ed[i].v);
		}
	}
}
void test(){
	int n;cin >> n;
	while(n != 0){
		init(n);
		for(int i = 1;i <= n - 1;i++){
			char c,cc;cin >> c;
			int m;cin >> m;
			for(int j = 1;j <= m;j++){
				int x;cin >> cc >> x;
				ed[++ptop] = {x,c-'A'+1,cc-'A'+1};
			}
		}
		sum = 0;
		kru();
		cout << sum << endl;
		cin >> n;
	}
	
}
#undef int
int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [B - Networking](https://vjudge.net/problem/OpenJ_Bailian-1287)

## 题意：emmmm，懂得都懂，基本不用改

## [C - Building a Space Station](https://vjudge.net/problem/OpenJ_Bailian-2031)

## 题意：给出节点坐标和覆盖半径，问连接所有节点需要的最短长度

## 思路：用$O(n^2)$算所有节点连接需要的长度，然后进行最小生成树即可

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 101;
bool use[N];
struct edge{
	double len;
	int u,v;
	bool operator <(const edge &other)const{
		return len < other.len;
	}
}ed[N*N];
int ptop = 0;
double sum = 0;
int a[N];
void init(int n){
	ptop = 0;
	for(int i = 1;i <= n;i++)
		a[i] = i;
}
int find(int x){
	if(a[x] == x) return a[x];
	else return a[x] = find(a[x]);
}
void kru(){
	sort(ed + 1,ed + 1 + ptop);
	for(int i = 1;i <= ptop;i++){
		if(find(ed[i].u) != find(ed[i].v)){
			if(ed[i].len > 0)
				sum += ed[i].len;
			a[find(ed[i].u)] = find(ed[i].v);
		}
	}
}
double x[N],y[N],z[N],r[N];
double dis(int i,int j){
	return sqrt(pow(x[i]-x[j],2)+pow(y[i]-y[j],2)+pow(z[i]-z[j],2));
}
void test(){
	int n;cin >> n;
	while(n != 0){	
		init(n);
		for(int i = 1;i <= n;i++)
			cin >> x[i] >> y[i] >> z[i] >> r[i];
		sum = 0;
		for(int i = 1;i <= n;i++)
			for(int j = i + 1;j <= n;j++)
				ed[++ptop] = {dis(i,j) - r[i] - r[j],i,j};
		kru();
		printf("%.3lf\n",sum);
		cin >> n;
	}
	
}
#undef int
int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [D - Constructing Roads](https://vjudge.net/problem/OpenJ_Bailian-2421)

## 题意：其中有一些节点已经被连接

## 并查集提前加入即可

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 101;
bool use[N];
struct edge{
	int len;
	int u,v;
	bool operator <(const edge &other)const{
		return len < other.len;
	}
}ed[N*N];
int ptop = 0;
int sum = 0;
int a[N];
void init(int n){
	ptop = 0;
	for(int i = 1;i <= n;i++)
		a[i] = i;
}
int find(int x){
	if(a[x] == x) return a[x];
	else return a[x] = find(a[x]);
}
void kru(){
	sort(ed + 1,ed + 1 + ptop);
	for(int i = 1;i <= ptop;i++){
		if(find(ed[i].u) != find(ed[i].v)){
			if(ed[i].len > 0)
				sum += ed[i].len;
			a[find(ed[i].u)] = find(ed[i].v);
		}
	}
}
void test(){
	int n;cin >> n;
	init(n);
	for(int i = 1;i <= n;i++)
	for(int j = 1;j <= n;j++){
		int x;cin >> x;
		if(i != j){
			ed[++ptop] = {x,i,j};
		}
	}
	int q;cin >> q;
	for(int i = 1;i <= q;i++){
		int u,v;cin >> u >> v;
		a[find(u)] = find(v);
	}
	kru();
	cout << sum << endl;
}
#undef int
int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [E - Truck History](https://vjudge.net/problem/OpenJ_Bailian-1789)

~~怎么读入影响结果啊，一个一个读就过了~~

## 题意：字符串之间的差异为长度，问让所有字符串相连的最短长度

## 思路：计算每对字符串之间的距离，然后最短生成树即可

```cpp
#include <iostream>
#include <vector>
#include <memory.h>
#include <string>
#include <algorithm>
using namespace std;
#define int long long
const int N = 2e3 + 10;
struct edge{
	int len;
	int u,v;
	bool operator <(const edge &other)const{
		return len < other.len;
	}
}ed[N*N];
int ptop = 0;//边数
int sum = 0;//总边长
int a[N];//并查集
void init(int n){
	ptop = 0;//初始化数量
	for(int i = 1;i <= n;i++)
		a[i] = i;//初始化并查集
}
int find(int x){
	if(a[x] == x) return a[x];
	else return a[x] = find(a[x]);
}
void kru(){
	sort(ed + 1,ed + 1 + ptop);//根据长度排序
	for(int i = 1;i <= ptop;i++){
		if(find(ed[i].u) != find(ed[i].v)){//如果不在同一并查集
			sum += ed[i].len;//加上这条边
			a[find(ed[i].u)] = find(ed[i].v);//并查集连上
		}
	}
}
char c[N][10];//存字符串
void test(){
	int n;cin >> n;
	while(n != 0){
		init(n);
		for(int i = 1;i <= n;i++)
			for(int j = 0;j < 7;j++)
				cin >> c[i][j];
		for(int i = 1;i <= n;i++)
		for(int j = i + 1;j <= n;j++){
			int dis = 0;
			for(int k = 0;k < 7;k++)
				if(c[i][k] != c[j][k])
					dis++;
			ptop++;
			ed[ptop].len = dis;
			ed[ptop].u = i;
			ed[ptop].v = j;
		}
		sum = 0;
		kru();
		printf("The highest possible quality is 1/%d.\n",sum);
		cin >> n;
	}
}
#undef int
int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}//
```

## [F - Arctic Network](https://vjudge.net/problem/OpenJ_Bailian-2349)

## 题意：可以给s个节点与卫星相连，其他根据坐标距离相连，问最短需要的建设路径

## 思路：每次加边都是减少一个独立集合，而$s$个节点也是减去$s-1$个集合，因此只要做$min(n-s,0)$次kruskal即可

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 5e2 + 10;
struct edge{
	double len;
	int u,v;
	bool operator <(const edge &other)const{
		return len < other.len;
	}
}ed[N*N];
int ptop = 0;//边数
int sum = 0;//总边长
int a[N];//并查集
void init(int n){
	ptop = 0;//初始化数量
	for(int i = 1;i <= n;i++)
		a[i] = i;//初始化并查集
}
int find(int x){
	if(a[x] == x) return a[x];
	else return a[x] = find(a[x]);
}
int s,n;
double ma = 0;
void kru(){
	sort(ed + 1,ed + 1 + ptop);//根据长度排序
	int cnt = 0;
	for(int i = 1;i <= ptop;i++){
		if(cnt + s >= n)
			break;
		if(find(ed[i].u) != find(ed[i].v)){//如果不在同一并查集
			cnt++;
			ma = ed[i].len;
			a[find(ed[i].u)] = find(ed[i].v);//并查集连上
		}
	}
}
int x[N],y[N];
double dis(int i,int j){
	return sqrt(pow(x[i]-x[j],2) + pow(y[i]-y[j],2));
}
void test(){
	cin >> s >> n;
	init(n);
	for(int i = 1;i <= n;i++)
		cin >> x[i] >> y[i];
	for(int i = 1;i <= n;i++)
	for(int j = i + 1;j <= n;j++){
		ed[++ptop] = {dis(i,j),i,j};
	}
	ma = 0;
	kru();
	printf("%.2lf\n",ma);
}
#undef int
int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}//
```

## [G - Highways](https://vjudge.net/problem/OpenJ_Bailian-1751)

## 题意：同D，有一些边已经被连接，节点间距离用坐标求即可

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 7.5e2 + 10;
struct edge{
	double len;
	int u,v;
	bool operator <(const edge &other)const{
		return len < other.len;
	}
}ed[N*N];
int ptop = 0;//边数
double sum = 0;//总边长
int a[N];//并查集
void init(int n){
	ptop = 0;//初始化数量
	for(int i = 1;i <= n;i++)
		a[i] = i;//初始化并查集
}
int find(int x){
	if(a[x] == x) return a[x];
	else return a[x] = find(a[x]);
}
void kru(){
	sort(ed + 1,ed + 1 + ptop);//根据长度排序
	for(int i = 1;i <= ptop;i++){
		if(find(ed[i].u) != find(ed[i].v)){//如果不在同一并查集
			cout << ed[i].u << ' ' << ed[i].v << endl;
			sum += ed[i].len;
			a[find(ed[i].u)] = find(ed[i].v);//并查集连上
		}
	}
}
int x[N],y[N];
double dis(int i,int j){
	return sqrt(pow(x[i]-x[j],2) + pow(y[i]-y[j],2));
}
void test(){
	int n;
	while(cin >> n){
		init(n);
		for(int i = 1;i <= n;i++)
			cin >> x[i] >> y[i];
		for(int i = 1;i <= n;i++)
		for(int j = i + 1;j <= n;j++){
			ed[++ptop] = {dis(i,j),i,j};
		}
		int m;cin >> m;
		for(int i = 1;i <= m;i++){
			int u,v;cin >> u >> v;
			a[find(u)] = find(v);
		}
		sum = 0;
		kru();
	}
}
#undef int
int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [H - Agri-Net](https://vjudge.net/problem/OpenJ_Bailian-1258)

## 题意：水

## [I - The Unique MST](https://vjudge.net/problem/OpenJ_Bailian-1679)

## 题意：看最小生成树是否唯一

## 思路：根据kruskal算法，对于相同长度的边它们的优先级是相同的，如果它们本来可以连接，但因为加入了相同长度的边后不能连接就说明这个图不唯一，用一个队列存储可以连接的相同长度的边，然后一个一个加入，如果有一个不能加入就说明最小生成树不唯一

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e2 + 10;
struct edge{
	int len;
	int u,v;
	bool operator <(const edge &other)const{
		return len < other.len;
	}
}ed[N*N];
int ptop = 0;//边数
int sum = 0;//总边长
int a[N];//并查集
void init(int n){
	ptop = 0;//初始化数量
	for(int i = 1;i <= n;i++)
		a[i] = i;//初始化并查集
}
int find(int x){
	if(a[x] == x) return a[x];
	else return a[x] = find(a[x]);
}
bool kru(){
	bool f = 0;
	sort(ed + 1,ed + 1 + ptop);//根据长度排序
	int no;//现在的长度
	for(int i = 1;i <= ptop;i++){
		no = ed[i].len;
		queue<edge> qu;
		while(i <= ptop && ed[i].len == no){
			if(find(ed[i].u) != find(ed[i].v))
				qu.push(ed[i]);
			i++;
		}
		i--;
		while(!qu.empty()){
			edge no = qu.front();
			qu.pop();
			if(find(no.u) != find(no.v)){
				a[find(no.u)] = find(no.v);
				sum += no.len;
			}else
				f = 1;
		}
	}
	return f;
}
void test(){
	int n,m;cin >> n >> m;
	init(n);
	for(int i = 1;i <= m;i++){
		int u,v,dis;cin >> u >> v >> dis;
		ed[++ptop] = {dis,u,v};
	}
	sum = 0;
	if(kru())
		cout << "Not Unique!" << endl;
	else
		cout << sum << endl;
}
#undef int
int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## [J - 还是畅通工程](https://vjudge.net/problem/HDU-1233)

## 题意：懂得都懂，正常最小生成树

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e2 + 10;
struct edge{
	int len;
	int u,v;
	bool operator <(const edge &other)const{
		return len < other.len;
	}
}ed[N*N];
int ptop = 0;//边数
int sum = 0;//总边长
int a[N];//并查集
void init(int n){
	ptop = 0;//初始化数量
	for(int i = 1;i <= n;i++)
		a[i] = i;//初始化并查集
}
int find(int x){
	if(a[x] == x) return a[x];
	else return a[x] = find(a[x]);
}
void kru(){
	sort(ed + 1,ed + 1 + ptop);//根据长度排序
	for(int i = 1;i <= ptop;i++){
		if(find(ed[i].u) != find(ed[i].v)){
			a[find(ed[i].v)] = find(ed[i].u);
			sum += ed[i].len;
		}
	}
}
void test(){
	int n;cin >> n;
	while(n != 0){
		init(n);
		for(int i = 1;i <= n*(n - 1) / 2;i++){
			int u,v;cin >> u >> v;
			int dis;cin >> dis;
			ed[++ptop] = {dis,u,v};
		}
		sum = 0;
		kru();
		cout << sum << endl;
		cin >> n;
	}
}
#undef int
int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [L - 畅通工程再续](https://vjudge.net/problem/HDU-1875)

## 题意：长度小于10或者大于1000的边不能建，问能否完全连通，最小为多少

## 加入判断边，结束后判断是否完全相连

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e2 + 10;
struct edge{
	double len;
	int u,v;
	bool operator <(const edge &other)const{
		return len < other.len;
	}
}ed[N*N];
int ptop = 0;//边数
double sum = 0;//总边长
int a[N];//并查集
void init(int n){
	ptop = 0;//初始化数量
	for(int i = 1;i <= n;i++)
		a[i] = i;//初始化并查集
}
int find(int x){
	if(a[x] == x) return a[x];
	else return a[x] = find(a[x]);
}
void kru(){
	sort(ed + 1,ed + 1 + ptop);//根据长度排序
	for(int i = 1;i <= ptop;i++){
		if(find(ed[i].u) != find(ed[i].v)){
			a[find(ed[i].v)] = find(ed[i].u);
			sum += ed[i].len;
		}
	}
}
int x[N],y[N];
double dis(int i,int j){
	return sqrt(pow(x[i] - x[j],2) + pow(y[i] - y[j],2));
}
void test(){
	int n;cin >> n;
	init(n);
	for(int i = 1;i <= n;i++)
		cin >> x[i] >> y[i];
	for(int i = 1;i <= n;i++)
		for(int j = i + 1;j <= n;j++){
			double _dis = dis(i,j);
			if(_dis >= 10 && _dis <= 1000)
				ed[++ptop]= {_dis,i,j};
		}	
	sum = 0;
	kru();
	bool f = 1;
	for(int i = 2;i <= n;i++)
		if(find(i) != find(i - 1))
			f = 0;
	if(f)
		printf("%.1lf\n",sum*100);
	else
		cout << "oh!" << endl;
}
#undef int
int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

