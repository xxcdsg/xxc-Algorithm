# [能力提升综合题单Part3 搜索](https://www.luogu.com.cn/training/9376)

## 1.DFS

### [P1219 [USACO1.5] 八皇后 Checker Challenge](https://www.luogu.com.cn/problem/P1219)

* 可以说是十足的典题了

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 15;

bool use[N],i_juse[N*2],i__juse[N*2];

int ans[N];

int le = 3;

int n;

int dfs(int i){
	if(i == n + 1){
		if(le-- > 0){
			for(int j = 1;j <= n;j++)
				cout << ans[j] << ' ';
			cout << endl;
		}
		return 1;
	}
	int sum = 0;
	for(int j = 1;j <= n;j++){
		if(!use[j] && !i_juse[i - j + N] && !i__juse[i + j]){
			use[j] = 1;
			i_juse[i - j + N] = 1;
			i__juse[i + j] = 1;
			ans[i] = j;
			sum += dfs(i + 1);
			use[j] = 0;
			i__juse[i + j] = 0;
			i_juse[i - j + N] = 0;
		}
	}
	return sum;
}

void test(){
	cin >> n;
	cout << dfs(1) << endl;
}

int main(){
    IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

### [P1019 [NOIP2000 提高组] 单词接龙](https://www.luogu.com.cn/problem/P1019)

* 主要难点是预处理字符串的连接
* dfs过程中不连接，只记录长度即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 21;

vector<pair<int,int>> ed[N];

string s[N];

int no;

int ma = 0;

int len[N];

int use[N];

void dfs(int i){
	ma = max(ma,no);
	for(auto [k,j]:ed[i]){
		if(use[j] < 2){
			use[j]++;
			no += len[j] - k;
			dfs(j);
			use[j]--;
			no -= len[j] - k;
		}
	}
}

void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++){
		cin >> s[i];
		len[i] = s[i].size();
		for(int j = 1;j <= i;j++){
			int _len = min(len[i],len[j]);
			_len--;
			for(int k = 1;k <= _len;k++){//长度
				bool f = 1;
				for(int _i = 0,_j = len[j] - k;f && _i < k;_i++,_j++){
					if(s[i][_i] != s[j][_j]){
						f = 0;
					}
				}
				if(f){
					ed[j].push_back({k,i});
					break;
				}
			}
			for(int k = 1;k <= _len;k++){
				bool f = 1;
				for(int _j = 0,_i = len[i] - k;f && _j < k;_j++,_i++){
					if(s[i][_i] != s[j][_j]){
						f = 0;
					}
				}
				if(f){
					ed[i].push_back({k,j});
					break;
				}
			}
		}
	}
	char c;cin >> c;
	for(int i = 1;i <= n;i++){
		if(s[i][0] == c){
			use[i]++;
			no = len[i];
			dfs(i);
		}
	}
	cout << ma << endl;
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

### [P5194 [USACO05DEC] Scales S](https://www.luogu.com.cn/problem/P5194)

* 首先这最小是一个斐波那契数列，因此要到2^30只要45个数
* 但2^45的数量级还是太大了，需要优化
* 因为这个数变大的速度很大，因此前缀和相对于后面的数较小，因此可看前缀和能否直接加入，实现剪枝

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 1e3 + 10;

ll m[N];

ll no = 0;
ll ma = 0;
ll n,c;

ll pre[N];

void dfs(int i){
	if(i == 0){
		ma = max(ma,no);
		return;
	}
	if(pre[i] + no <= ma) return;
	else if(pre[i] + no <= c){
		ma = max(ma,no + pre[i]);
		return;
	}
	dfs(i - 1);
	if(no + m[i] <= c){
		no += m[i];
		dfs(i - 1);
		no -= m[i];
	}
}

void test(){
	cin >> n >> c;
	int r = 0;
	for(int i = 1;i <= n;i++){
		pre[i] = pre[i - 1];
		cin >> m[i];
		pre[i] += m[i];
		if(m[i] < c) r = i;
	}
	dfs(r);
	cout << ma << endl;
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

### [P5440 【XR-2】奇迹](https://www.luogu.com.cn/problem/P5440)

* 我的筛子没特判1，emmm，给我搞半天
* 加一些剪枝之类的，就很简单，考常识，闰年什么的

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 1e8 + 100,M = 1e7 + 10;

bool vis[N];
int ans[M];
int idx;

int r[8] = {9,9,9,9,1,9,3,9};
double _10[8] = {1e7,1e6,1e5,1e4,1e3,1e2,10,1};
int month[13] = {-1,31,28,31,30,31,30,31,31,30,31,30,31};

string s;

bool ok(int x){
	if(vis[x] == 1) return 0;
	if(x / 10000 == 0) return 0;
	else{
		int y = x / 10000;
		int m = (x / 100) % 100;
		int d = x % 100;
		if(m == 2){
			if((y % 4 == 0 && y % 100 != 0) || y % 400 == 0){
				if(d <= 29) return 1;
			}else return d <= 28;
		}else{
			return d <= month[m];
		}
	}
}

int ask(int x,int no){
	int sum = 0;
	if(x == -1){
		return ok(no);
	}
	if(x == 5){
		if(vis[no]) return 0;
		if(no == 0 || no > 31) return 0;
	}
	if(x == 3){
		if(vis[no]) return 0;
		if(no / 100 == 0 || no / 100 > 12) return 0;
	}
	if(s[x] == '-'){
		for(int j = 0;j <= r[x];j++){
			sum += ask(x - 1,no + j * _10[x]);
		}
	}else{
		return ask(x - 1,no + (s[x] - '0') * _10[x]);
	}
	return sum;
}

void test(){
	cin >> s;
	cout << ask(7,0) << endl;
}

void f(){
	vis[1] = 1;
	for(int i = 2;i <= N - 10;i++){
		if(!vis[i])
			ans[++idx] = i;
		for(int j = 1;j <= idx && ans[j]*i < N - 10;j++){
			vis[ans[j]*i] = 1;
			if(i % ans[j] == 0)
				break;
		}
	}
}

int main(){
	f();
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

### [P1378 油滴扩展](https://www.luogu.com.cn/problem/P1378)

* $A^6_6=720$，怎么做都行，注意特判点被覆盖导致半径为负数的情况即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 7;
const double pi = acos(-1);

pair<int,int> p[N];

double dis[N][N],r[N],ans,ma = 0;

bool use[N];

int n,ge[N];

void dfs(int x){
	if(x == n + 1){
		double de = 0;
		for(int i = 1;i <= n;i++){
			if(r[i] > 0)
				de += pi * (r[i] * r[i]);
		}
		ma = max(ma,de);
		return;
	}
	for(int i = 1;i <= n;i++){
		if(!use[i]){
			double pre = r[i];
			for(int j = 1;j < x;j++)
				r[i] = min(dis[i][ge[j]] - r[ge[j]],r[i]);
			ge[x] = i;
			use[i] = 1;	
			dfs(x + 1);
			use[i] = 0;
			r[i] = pre;
		}
	}
}

void test(){
	cin >> n;
	int x1,x2,y1,y2;cin >> x1 >> y1 >> x2 >> y2;
	if(x1 > x2) swap(x1,x2);
	if(y1 > y2) swap(y1,y2);
	ans = (x2 - x1) * (y2 - y1);
	for(int i = 1;i <= n;i++){
		cin >> p[i].first >> p[i].second;
		r[i] = min(min(p[i].first - x1,x2 - p[i].first),min(p[i].second - y1,y2 - p[i].second));
		for(int j = 1;j < i;j++){
			dis[i][j] = dis[j][i] = sqrt(pow((p[i].first - p[j].first),2) + pow(p[i].second - p[j].second,2));
		}
	}
	dfs(1);
	cout << (int)(ans - ma + 0.5) << endl;
}

int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## 2.BFS

### [P1162 填涂颜色](https://www.luogu.com.cn/problem/P1162)

* 感觉好久没打了，有点手生
* 用两个函数，一个找，一个填即可，这样写是dfs的写法

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define endl '\n'
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 31;

int mvx[] = {0,0,1,-1};
int mvy[] = {1,-1,0,0};

int a[N][N];
bool vis[N][N];
int n;

int find(int x,int y){
	vis[x][y] = 1;
	bool _f = 0;
	for(int k = 0;k < 4;k++){
		int xx = x + mvx[k],yy = y + mvy[k];
		if(xx <= 0 || xx > n || yy <= 0 || yy > n)
			_f = 1;
		else if(a[xx][yy] == 0 && !vis[xx][yy])
			_f |= find(xx,yy); 
	}
	return _f;
}

void fill(int x,int y,int aim){
	a[x][y] = aim;
	for(int k = 0;k < 4;k++){
		int xx = x + mvx[k],yy = y + mvy[k];
		if(xx <= 0 || xx > n || yy <= 0 || yy > n || a[xx][yy] != 0) continue;
		fill(xx,yy,aim);
	}
}

void test(){
	cin >> n;
	for(int i = 1;i <= n;i++)
		for(int j = 1;j <= n;j++)
			cin >> a[i][j];
	for(int i = 2;i < n;i++)
		for(int j = 2;j < n;j++)
			if(a[i][j] == 0 && !vis[i][j]){
				if(find(i,j) == 0)
					fill(i,j,2);
			}
	for(int i = 1;i <= n;i++){
		for(int j = 1;j <= n;j++){
			cout << a[i][j] << ' ';
		}
		cout << endl;
	}
}

int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [P1443 马的遍历](https://www.luogu.com.cn/problem/P1443)

* 这告诉我们每次加入队列时就要标记，让后面的不能再加入一遍

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define endl '\n'
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 410;

int mvx[] = {1,1,-1,-1,2,2,-2,-2};
int mvy[] = {2,-2,2,-2,1,-1,1,-1};

int ans[N][N];

void test(){
	int n,m,x,y;cin >> n >> m >> x >> y;
	ans[x][y] = 1;
	queue<tuple<int,int,int>> qu;
	qu.push({x,y,1});
	int num = 0;
	while(!qu.empty()){
		num++;
		auto [xx,yy,t] = qu.front();
		qu.pop();
		for(int k = 0;k < 8;k++){
			int _x = xx + mvx[k],_y = yy + mvy[k];
			if(_x <= 0 || _x > n || _y <= 0 || _y > m || ans[_x][_y]) continue;
			ans[_x][_y] = t + 1;
			qu.push({_x,_y,t + 1});
		}
	}
	for(int i = 1;i <= n;i++){
		for(int j = 1;j <= m;j++)
			cout << ans[i][j] - 1 << '\t';
		cout << endl;
	}
}

int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [P3956 棋盘](https://www.luogu.com.cn/problem/P3956)

* 都是练手题呢
* 用队列来写bfs呢

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 1e2 + 10,inf = 1e9;

int a[N][N];
int ans[N][N][3];

int mvx[] = {0,0,1,-1};
int mvy[] = {1,-1,0,0};

void test(){
	int m,n;cin >> m >> n;
	for(int i = 1;i <= n;i++){
		int x,y;cin >> x >> y;
		cin >> a[x][y];
		a[x][y]++;
	}
	ans[1][1][a[1][1]] = 1;
	queue<tuple<int,int,int>> qu;
	qu.push({1,1,a[1][1]});
	while(!qu.empty()){
		auto [x,y,t] = qu.front();
		int no = ans[x][y][t];
		qu.pop();
		for(int i = 0;i < 4;i++){
			int _x = x + mvx[i],_y = y + mvy[i];
			if(!(_x > 0 && _x <= m && _y > 0 && _y <= m)) continue;
			if(a[_x][_y]){
				if(a[_x][_y] == t){
					if(ans[_x][_y][a[_x][_y]] == 0 || ans[_x][_y][a[_x][_y]] > no){
						qu.push({_x,_y,a[_x][_y]});
						ans[_x][_y][a[_x][_y]] = no;
					}
				}else{
					if(ans[_x][_y][a[_x][_y]] == 0 || ans[_x][_y][a[_x][_y]] > no + 1){
						qu.push({_x,_y,a[_x][_y]});
						ans[_x][_y][a[_x][_y]] = no + 1;
					}
				}
			}else if(a[x][y]){
				if(ans[_x][_y][t] == 0 || ans[_x][_y][t] > no + 2){
					qu.push({_x,_y,t});
					ans[_x][_y][t] = no + 2;
				}
			}
		}
	}
	int x = min((ans[m][m][1] == 0) ? inf : ans[m][m][1],(ans[m][m][2] == 0) ? inf : ans[m][m][2]);
	cout << ((x == inf) ? -1 : x - 1) << endl;
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [P1032 字串变换](https://www.luogu.com.cn/problem/P1032)

* 经典恶心题，考验对字符串函数的使用

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

map<string,int> mp;

queue<string> qu;

pair<string,string> pa[10];

void test(){
	string a,b;cin >> a >> b;
	qu.push(a);
	string l,r;
	int ptop = 0;
	while(cin >> l >> r){
		pa[++ptop] = {l,r};
	}
	int ans = -1;
	while(!qu.empty()){
		string s = qu.front();
		qu.pop();
		if(s == b) {
			ans = mp[s];
			break;
		}else if(mp[s] == 10) break;
		else{
			for(int i = 1;i <= ptop;i++){
				l = pa[i].first,r = pa[i].second;
				int tem = 0,len = s.size(),llen = l.size();
				while(tem < len){
					int k = s.substr(tem).find(l);
					if(k == -1) break;
					k += tem;
					string ne = s.substr(0,k) + r + s.substr(k + llen);
					if(mp[ne] == 0){
						qu.push(ne);
						mp[ne] = mp[s] + 1;
					}
					tem = k + 1;
				}
			}
		}
	}
	while(!qu.empty()){
		if(qu.front() == b){
			ans = 10;
			break;
		}
		qu.pop();
	}
	if(ans == -1) cout << "NO ANSWER!" << endl;
	else cout << ans << endl;
}

int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## 3.记忆化搜索

### [P1514 [NOIP2010 提高组] 引水入城](https://www.luogu.com.cn/problem/P1514)

* 从大到小流动到下面，那么它一定无法切割。
* 然后用反向树状数组维护最小值，区间转移。（有点麻烦啊）

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 5e2 + 10;

struct point{
	int l,r,x;
}p[N];//记录最大的位置与范围

bool able[N];
bool vis[N][N];
int a[N][N];

int n,m;
int no;

void find(int x){
	memset(vis,0,sizeof(vis));
	queue<pair<int,int>> qu;
	qu.push({1,x});
	vis[1][x] = 1;
	while(!qu.empty()){
		auto [x,y] = qu.front();
		qu.pop();
		if(x != 1 && a[x][y] > a[x - 1][y] && !vis[x - 1][y]){
			vis[x - 1][y] = 1;
			qu.push({x - 1,y});
		}
		if(y != 1 && a[x][y] > a[x][y - 1] && !vis[x][y - 1]){
			vis[x][y - 1] = 1;
			qu.push({x,y - 1});
		}
		if(x != n && a[x][y] > a[x + 1][y] && !vis[x + 1][y]){
			vis[x + 1][y] = 1;
			qu.push({x + 1,y});
		}else if(x == n){
			able[y] = 1;
		}
		if(y != m && a[x][y] > a[x][y + 1] && !vis[x][y + 1]){
			vis[x][y + 1] = 1;
			qu.push({x,y + 1});
		}
	}
	p[no].x = 0;
	for(int i = 1;i <= m;i++){
		if(!p[no].x && vis[n][i]){
			p[no].l = i;
			p[no].x = 1;
		}
		if(p[no].x && vis[n][i]){
			p[no].r = i;
		}
	}
}

int tr[N];
int lowbit(int x){
	return x & -x;
}
void updata(int x,int y){
	for(;x <= m;x += lowbit(x))
		tr[x] = min(tr[x],y);
}
int queu(int x){
	int mi = 0x3f3f3f3f;
	for(;x;x -= lowbit(x))
		mi = min(mi,tr[x]);
	return mi;
}

void test(){
	cin >> n >> m;
	int ptop = 1;
	p[ptop].l = 1,p[ptop].x = -1;
	for(int i = 1;i <= m;i++){//预处理第一行
		cin >> a[1][i];
		if(a[1][i] > a[1][p[ptop].x] && (i == p[ptop].l || a[1][i] > a[1][i - 1])){//变大
			p[ptop].x = i;
		}else if(a[1][i - 1] > a[1][i]){//变小
			p[ptop].r = i;
		}else{
			ptop++;
			p[ptop].l = i;
			p[ptop].r = i;
			p[ptop].x = i;
		}
	}
	if(n == 1) cout << 1 << endl << ptop << endl;
	else{
		for(int i = 2;i <= n;i++){
			for(int j = 1;j <= m;j++)
				cin >> a[i][j];
		}
		for(int i = 1;i <= ptop;i++){
			no = i;
			find(p[i].x);
		}
		int ans = 0;
		for(int i = 1;i <= m;i++){
			if(!able[i]) ans++;
		}
		if(ans) cout << 0 << endl << ans << endl;
		else{
			memset(tr,0x3f,sizeof(tr));
			for(int i = 1;i <= ptop;i++){
				if(p[i].x)//有范围
				{
					int tem = queu(m + 1 - (p[i].l - 1));
					if(p[i].l == 1) tem = 0;
					tem++;
					updata(m + 1 - p[i].r,tem);
				}
			}
			cout << 1 << endl << queu(1) << endl;
		}
	}
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

### [P1535 游荡的奶牛](https://www.luogu.com.cn/problem/P1535)

* 15步，4个方向，分一半双向搜索最多为$4^8=65536$

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 1e2 + 10;

int mvi[] = {1,-1,0,0};
int mvj[] = {0,0,1,-1};

bool able[N][N];

int n,m;

queue<pair<int,int>> qu;

void find(pair<int,int> be,int t){
	qu.push(be);
	while(t--){
		int num = qu.size();
		while(num--){
			auto [x,y] = qu.front();
			qu.pop();
			for(int i = 0;i < 4;i++){
				int _x = x + mvi[i];
				int _y = y + mvj[i];
				if(_x <= 0 || x > n || _y <= 0 || _y > m || !able[_x][_y])
					continue;
				qu.push({_x,_y});
			}
		}
	}
}

void test(){
	int t;cin >> n >> m >> t;
	for(int i = 1;i <= n;i++){
		string s;cin >> s;
		for(int j = 0;j < m;j++){
			if(s[j] == '.') able[i][j + 1] = 1;
		}
	}
	int si,sj,ei,ej;cin >> si >> sj >> ei >> ej;
	int l = t / 2,r = t - t / 2;
	map<pair<int,int>,int> mp;
	find((pair<int,int>){ei,ej},r);
	while(!qu.empty()){
		mp[qu.front()]++;
		qu.pop();
	}
	find((pair<int,int>){si,sj},l);
	int ans = 0;
	while(!qu.empty()){
		ans += mp[qu.front()];
		qu.pop();
	}
	cout << ans << endl;
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

### [P1434 [SHOI2002\]滑雪](https://www.luogu.com.cn/problem/P1434)

* 不看报错信息是吧，没返回值是吧

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 1e2 + 10;

int a[N][N];

int ma[N][N] = {0};

int mvi[] = {1,-1,0,0},mvj[] = {0,0,1,-1};

int r,c;

int find(int x,int y){
	if(ma[x][y]) return ma[x][y];
	else{
		ma[x][y] = 1;
		for(int i = 0;i < 4;i++){
			int _x = x + mvi[i],_y = y + mvj[i];
			if(_x > r || _x <= 0 || _y > c || _y <= 0 || a[x][y] <= a[_x][_y]) continue;
			ma[x][y] = max(find(_x,_y) + 1,ma[x][y]);
		}
	}
	return ma[x][y];
}

void test(){
	cin >> r >> c;
	for(int i = 1;i <= r;i++)
		for(int j = 1;j <= c;j++)
			cin >> a[i][j];
	int ans = 0;
	for(int i = 1;i <= r;i++)
		for(int j = 1;j <= c;j++){
			ans = max(ans,find(i,j));
		}
	cout << ans << endl;
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

### [P3953 逛公园](https://www.luogu.com.cn/problem/P3953)

* 记录到这个点差最短路最多k有多少合法线路，记忆化搜索
* 建立反向边记录每个的点到终点的最短路
* 好像不需要正向搜索emmmm
* 无限要找0环，并且这个环上的点还得能从起点取到（还是需要正向搜索的），并且能去到终点，并且经过这个环的最短路还要在范围内
* 这个0环要记录所有0边，然后找环，因为只要取环上的任意点，因此枚举找环标记即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 1e5 + 10,K = 51,M = 2e5 + 10;

int n,m,k,p;

int sdis[N];
int edis[N];
bool able[N][2];
bool use[N];

ll ans[N][K];//从这个点到终点,走k,有多少合法线路
bool vis[N][K];

vector<pair<int,int>> ed[N][2];
vector<int> _0[N];

struct point{
	int u,dis;
	bool operator <(const point &other)const{
		return other.dis < dis;
	}
};

priority_queue<point> qu;

void bfs(int *dis,int s,int op){
	memset(use,0,sizeof(use));
	qu.push({s,0});
	while(!qu.empty()){
		auto [u,d] = qu.top();
		able[u][op] = 1;
		qu.pop();
		if(use[u]) continue;
		use[u] = 1;
		dis[u] = d;
		use[u] = 1;
		for(auto [v,l]:ed[u][op]){
			if(use[v]) continue;
			qu.push({v,l + d});
		}
	}
}

void find(int x,int k){
	if(vis[x][k]) return;
	else{
		int an = 0;
		for(auto [v,d] : ed[x][0]){
			if(!able[v][1]) continue;
			if(d + edis[v] <= edis[x] + k){
				find(v,(edis[x] + k) - (d + edis[v]));
				an = (an + ans[v][(edis[x] + k) - (d + edis[v])]) % p;
			}
		}
		if(x == n) an++;
		ans[x][k] = an;
		vis[x][k] = 1;
	}
}

bool find_0(){
	vector<int> us(n + 1,0);
	for(int i = 1;i <= n;i++){
		if(!us[i])//没走过
			if(sdis[i] + edis[i] <= sdis[n] + k){//路径可经过它
				queue<int> qu;
				qu.push(i);
				while(!qu.empty()){
					int x = qu.front();
					qu.pop();
					us[x] = i;
					for(auto y:_0[x]){
						if(us[y] == i && able[y][0] && able[y][1]) return 0;
						if(us[y] == 0) qu.push(y);
					}
				}
			}
	}
	return 1;
}

void test(){
	cin >> n >> m >> k >> p;
	for(int i = 1;i <= n;i++){
		ed[i][0].clear();
		ed[i][1].clear();
		_0[i].clear();
		able[i][0] = able[i][1] = 0;
		memset(ans[i],0,sizeof(ans[i]));
		memset(vis[i],0,sizeof(vis[i]));
	}
	for(int i = 1;i <= m;i++){
		int u,v,d;cin >> u >> v >> d;
		ed[u][0].push_back({v,d});
		ed[v][1].push_back({u,d});
		if(d == 0)
			_0[u].push_back(v);
	}
	bfs(sdis,1,0);
	bfs(edis,n,1);
	bool f = find_0();
	if(f){
		find(1,k);
		cout << ans[1][k] << endl;
	}else{
		cout << -1 << endl;
	}
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## 4.剪枝

### [P1120 小木棍 ［数据加强版］](https://www.luogu.com.cn/problem/P1120)

