## LA 求树上点的 $K$ 级祖先

* 无优化普通倍增法，预处理 $O(nlog_2n)$，查询 $O(log_2n)$

```cpp
const int N = 5e5 + 10;
struct LA{
	vector<int> ed[N];
	int fa[N][20];
	void dfs(int x,int dad){
		fa[x][0] = dad;
		dep[x] = dep[dad] + 1;
		for(int i = 1;i < 20;i++)
			fa[x][i] = fa[fa[x][i - 1]][i - 1];
		for(int y : ed[x]){
			if(y == dad) continue;
			dfs(y,x);
		}
	}
	int find(int x,int k){
		int cnt = 0;
		while(k){
			if(k & 1) x = fa[x][cnt];
			cnt++;
			k >>= 1;
		}
		return x;
	}
}tr;
```

* 我们加入 $lg$ 数组，然后根据最长链的性质来优化，预处理 $O(nlog_2n)$ ，查询 $O(1)$
* 我们第一次跳 $t = 2^{\lfloor log_2k \rfloor}$ ，那么我们有 $t>k-t$ 因为这是二进制的最高位，同时有 $lstson[belong[x]].size\geq t$ ，因为我们至少跳了 $t$ ，如果我们要求的点在 $belong[x]$ 与第一次跳转点 $y$ 之间，那么直接在 $belong[x]$ 的链中找即可，如果在 $belong[x]$ 的上面，那么我们也知道它向上不可能跳多余原链长，那么我们在创建时同时创建向上的，相同长度的链即可

```cpp
const int N = 5e5 + 10;
struct LA{
	int dep[N],madep[N],bson[N],belong[N],fa[N][20],lg2[N];
	vector<int> lst[N],upfa[N],ed[N];//最长链

	void dfs1(int x,int dad){
		fa[x][0] = dad;
		madep[x] = dep[x] = dep[dad] + 1;
		for(int i = 1;i <= lg2[dep[x]];i++)
			fa[x][i] = fa[fa[x][i - 1]][i - 1];
		for(int y : ed[x]){
			if(y == dad) continue;
			dfs1(y,x);
			if(madep[y] > madep[x]){
				madep[x] = madep[y];
				bson[x] = y;
			}
		}
	}

	void dfs2(int x,int dad,int no){//现在的点，父亲，现在在更新的链顶的下标
		belong[x] = no;
		for(int y : ed[x]){
			if(y == dad) continue;
			if(y == bson[x]){
				dfs2(y,x,no);
			}else{
				dfs2(y,x,y);
			}
		}
		if(x == no){
			for(int i = 1,tem = x;i <= madep[x] - dep[x] + 1;i++){
				upfa[x].push_back(tem);
				tem = fa[tem][0];
			}
			for(int i = 1,tem = x;i <= madep[x] - dep[x] + 1;i++){
				lst[x].push_back(tem);
				tem = bson[tem];
			}
		}
	}

	void init(){
		lg2[1] = 0;
		for(int i = 2;i <= N - 10;i++)
			lg2[i] = lg2[i >> 1] + 1;
	}

	int find(int x,int k){
		if(k == 0) return x;
		x = fa[x][lg2[k]];
		k -= 1 << lg2[k];
		int up = belong[x];
		if(dep[x] - dep[up] >= k){
			return lst[up][dep[x] - dep[up] - k];
		}else{
			return upfa[up][k - (dep[x] - dep[up])];
		}
	}
}tr;
```

