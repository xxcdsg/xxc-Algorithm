# 2-SAT

* 对于二元条件并且，元素只有两个可能，如果非A，那么就可以推出B，同理非B就可以推出A，那么就可以建图，不可能成立的情况就是A与非A形成了一个环，用tarjan找环，然后tarjan给出的color就是拓扑逆序，那么为了防止A推得非A，那么只要输出color小的可能性就是拓扑后面的位置

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 1e6 + 10;

vector<int> ed[N*2];

stack<int> st;
bool in[N*2];//是否在栈内
int dfs[N*2],low[N*2];
int ptop = 0;
int num = 0;//记录强连通分量的数量
int c[N*2];
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
			c[y] = num;
			y = st.top();
			st.pop();
			in[y] = 0;
		}while(y != x);
		c[x] = num;
	}
}
int n,m;

int main(){
	cin >> n >> m;
	for(int k = 1;k <= m;k++){
		int i,a,j,b;cin >> i >> a >> j >> b;
		if(a) i = 2*n - i + 1;
		if(b) j = 2*n - j + 1;
		i = 2*n - i + 1;
		ed[i].push_back(j);
		i = 2*n - i + 1;
		j = 2*n - j + 1;
		ed[j].push_back(i);
	}
	bool f = 1;
	for(int i = 1;i <= n*2;i++) {
		if(!dfs[i]) {
			tarjan(i);
		}
	}
	for(int i = 1;i <= n;i++){
		if(c[i] == c[2*n + 1 - i]) f = 0;
	}
	if(!f) cout << "IMPOSSIBLE" << '\n';
	else{
		cout << "POSSIBLE" << '\n';
		for(int i = 1;i <= n;i++)
			cout <<	(c[2*n - i + 1] < c[i]) << ' ';
	}
}
```



