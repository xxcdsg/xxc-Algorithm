# tarjan算法（求强连通分量）（缩点）

## 强连通：两个点相互可达

## 强连通分量：集合中的点两两可达

## 思路：记录自己的时间戳dfs与能到达的最小时间戳low，先dfs搜索完自己能到达的点，如果更新后的最小时间戳low与己的时间戳dfs相等说明自己就是那个强连通分量顶点，如果不相等说明它可以到达更小的时间戳，那么在回溯到那个点时再处理

### 题目：

### 1.计算强连通分量数量

### [迷宫城堡](https://acm.hdu.edu.cn/showproblem.php?pid=1269)

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 3e5 + 10;
stack<int> st;
bool in[N];//是否在栈内
int dfs[N],low[N];
vector<int> ed[N];
int ptop = 0;
int num = 0;//记录强连通分量的数量
void tarjan(int x){
	dfs[x] = low[x] = ++ptop;
	st.push(x);
	in[x] = 1;
	for(auto y : ed[x]){
		if(!dfs[y])
			tarjan(y);
        else if(in[y])
			low[x] = min(low[x],low[y]);
	}
	if(low[x] == dfs[x]){
		int y;
		num++;
		do{
			y = st.top();
			st.pop();
			in[y] = 0;
		}while(y != x);
	}
}
int n,m;
void init(){
	num = ptop = 0;
	for(int i = 1;i <= n;i++){
		in[i] = dfs[i] = low[i] = 0;
		ed[i].clear();
	}
}
int main()
{
	cin >> n >> m;
	while(n != 0){
		init();
		for(int i = 1;i <= m;i++){
			int u,v;cin >> u >> v;
			ed[u].push_back(v);
		}
		for(int i = 1;i <= n;i++)
			if(!dfs[i])
				tarjan(i);
		if(num == 1)
			cout << "Yes" << endl;
		else
			cout << "No" << endl;
		cin >> n >> m;
	}
}
```

### 2.变成强连通图的最小连接数

### [Proving Equivalences](https://acm.hdu.edu.cn/showproblem.php?pid=2767)

### 思路：先用tarjan缩点，然后看新的图，如果要让新的图变成强连通图，就要让每个点都有入有出，如果原图是强连通图，那么新加一点也要让其依旧为强连通图，那么只要随便连一条边进入原图，再从原图拉一条边出来，因为原图是强连通图，所以所有点两两相通，也就包括从原图连入与连出的点，因此对于强连通图来说，只要每个点都有入有出即可变为强连通图

### 但是如果强连通分量彼此分立那就不行，因此先用tarjan将强连通分量整合即可

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 3e5 + 10;
stack<int> st;
bool in[N];//是否在栈内
int dfs[N],low[N];
int _in[N],_out[N];
vector<int> ed[N];
int belong[N];//缩点
int ptop = 0;
int num = 0;//记录强连通分量的数量
void tarjan(int x){
	dfs[x] = low[x] = ++ptop;
	st.push(x);
	in[x] = 1;
	for(auto y : ed[x]){
		if(!dfs[y]){
			tarjan(y);
			low[x] = min(low[x],low[y]);
		}
		else if(in[y])
			low[x] = min(low[x],dfs[y]);
	}
	if(low[x] == dfs[x]){
		int y;
		num++;
		do{
			y = st.top();
			st.pop();
			in[y] = 0;
			belong[y] = num;
		}while(y != x);
	}
}
int n,m;
void init(){
	num = ptop = 0;
	for(int i = 1;i <= n;i++){
		dfs[i] = low[i] = 0;
		_in[i] = _out[i] = 0;
		ed[i].clear();
	}
}
int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	int t;cin >> t;
	while(t--){
		cin >> n >> m;
		init();
		for(int i = 1;i <= m;i++){
			int u,v;cin >> u >> v;
			ed[u].push_back(v);
		}
		for(int i = 1;i <= n;i++)
			if(!dfs[i])
				tarjan(i);
		if(num == 1){
			cout << 0 << endl;
			continue;
		}
		for(int i = 1;i <= n;i++){
			for(auto x:ed[i]){
				if(belong[x] != belong[i]){
					_in[belong[x]]++;
					_out[belong[i]]++;
				}
			}
		}
		int __in = 0,__out = 0;
		for(int i = 1;i <= num;i++){
			if(_in[i] == 0)
				__in++;
			if(_out[i] == 0)
				__out++;
		}
		cout << max(__in,__out) << endl;
	}
}
```

### 3.让所有点都能流到需要的最小费用

### [Summer Holiday](https://acm.hdu.edu.cn/showproblem.php?pid=1827)

### 题意：联系第$i$人需要$a_i$的费用，那些人有些又可以联系其他人，问最小的费用联系到所有人

### 思路：先缩点，然后看入边，如果入边为0说明需要联系，在这个集合里找最小加上即可

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 3e5 + 10;
stack<int> st;
bool in[N];//是否在栈内
int dfs[N],low[N];
vector<int> ed[N];
int belong[N];//缩点
int ptop = 0;
int num = 0;//记录强连通分量的数量
void tarjan(int x){
	dfs[x] = low[x] = ++ptop;
	st.push(x);
	in[x] = 1;
	for(auto y : ed[x]){
		if(!dfs[y]){
			tarjan(y);
			low[x] = min(low[x],low[y]);
		}
		else if(in[y])
			low[x] = min(low[x],dfs[y]);
	}
	if(low[x] == dfs[x]){
		int y;
		num++;
		do{
			y = st.top();
			st.pop();
			in[y] = 0;
			belong[y] = num;
		}while(y != x);
	}
}
int n,m;
int mi[N];
int _in[N];
void init(){
	num = ptop = 0;
	for(int i = 1;i <= n;i++){
		dfs[i] = low[i] = 0;
		_in[i] = 0;
		ed[i].clear();
		mi[i] = 1e9;
	}
}
int a[N];
int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	while(cin >> n >> m){
		init();
		for(int i = 1;i <= n;i++)
			cin >> a[i];
		for(int i = 1;i <= m;i++){
			int u,v;cin >> u >> v;
			ed[u].push_back(v);
		}
		for(int i = 1;i <= n;i++)
			if(!dfs[i])
				tarjan(i);
		for(int i = 1;i <= n;i++){
			for(auto x : ed[i]){
				if(belong[x] != belong[i]){
					_in[belong[x]]++;
				}
			}
		}
		for(int i = 1;i <= n;i++)
			mi[belong[i]] = min(a[i],mi[belong[i]]);
		int ans = 0,nnum = 0;
		for(int i = 1;i <= num;i++){
			if(_in[i] == 0){
				ans += mi[i];
				nnum++;
			}
		}
		cout << nnum << ' ' << ans << endl;
	}
}
```

### 4.割点

#### 其实割点不需要tarjan的栈之类的，用dfs就能解决，参考了tarjan的时间戳的思想

#### 定义：去掉该点后使得原本该点所在的树不连通

#### 思路：我们从一个点开始搜索，将它当作根，如果它搜索了两个即以上不相连的子树，那么说明这个点为割点，如果是被搜索到的点，说明上面为一连通部分，那么只要有一个子树的最小能到达时间戳大于等于该节点，说明子树被这个点与上面分开，那么这个点就为割点

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 3e5 + 10;
stack<int> st;
int dfs[N],low[N];
vector<int> ed[N];
bool is[N];
int ptop = 0;
int cnt = 0;
int root;
void tarjan(int x){
	dfs[x] = low[x] = ++ptop;
	st.push(x);
	int son = 0;
	for(auto y : ed[x]){
		if(!dfs[y]){
			son++;
			tarjan(y);low[x] = min(low[x],low[y]);
			if(low[y] >= dfs[x] && x != root){
				cnt += !is[x];
				is[x] = 1;
			}
		}else
			low[x] = min(low[x],dfs[y]);
	}
	if(son >= 2 && root == x){
		cnt += !is[x];
		is[x] = 1;
	}
}
int n,m;
int main()
{
	cin >> n >> m;
	for(int i = 1;i <= m;i++){
		int u,v;cin >> u >> v;
		ed[u].push_back(v);
		ed[v].push_back(u);
	}
	for(int i = 1;i <= n;i++){
		if(!dfs[i]){
			root = i;
			tarjan(i);
		}
	}
	cout << cnt << endl;
	for(int i = 1;i <= n;i++)
		if(is[i])
			cout << i << ' ';
}
```

