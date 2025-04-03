## Splay

* 也就是平衡树，维护二叉搜索树，使其插入与删除后，深度保持在 $O(log_2n)$ 水平

##### 维护结构

*  $fa[x]$ 父节点
*  $lson[x],rson[x]$ 左右子节点
*  $val[x]$ 权值
*  $cnt[x]$ 出现次数
*  $sz[x]$ 子树大小

##### 基本操作

*  $update(x)$ 更新 $size$

```cpp
void update(int x){
    sz[x] = sz[lson[x]] + sz[rson[x]] + cnt[x];
}
```

*  $get(x)$ 得到该节点是否为父节点的左子节点

```cpp
bool get(int x){
    return lson[fa[x]] == x;
}
```

*  $clear(x)$ 销毁该节点

```cpp
void clear(int x){
    lson[x] = rson[x] = fa[x] = val[x] = cnt[x] = sz[x] = 0;
}
```

### 旋转

* 左旋：把右节点提上来
* 右旋：把左节点提上来

![splay-rotate](C:\Users\Administrator\Desktop\markdown\c++\算法\数据结构\高级数据结构\splay-rotate.svg)

* 无论左旋，还是右旋，上来的节点其中一个子点会传递给子节点的另外一个位置
* 因此我们用异或来表示左节点与右节点更好

*  改成 $son[x][2]$
* 那么将一个点提取
* 考虑变化的值，首先提取的点，其为左节点，那么其右节点就会变成父节点，即父节点会变成其反类型
* 原本的父节点的父节点会变成提取的点的父节点
* 父节点的父节点会变成提取点

```cpp
void rotate(int x){
    int _type = get(x),_fa = fa[x],_son = son[x][_type^1];//得到该类型
    son[_fa][_type] = _son;
    fa[_son] = _fa;
    son[x][_type^1] = _fa;
    fa[x] = fa[_fa];
    fa[_fa] = x;
    update(x);
    update(_fa);
}
```

### Splay 操作

* 将一个点强制旋转到根节点
* 分三种
* 如果父节点就是根节点，那么只转该节点

![splay-zig](C:\Users\Administrator\Desktop\markdown\c++\算法\数据结构\高级数据结构\splay-zig.svg)

* 如果该节点与父节点为同一类型的节点，那么先转父节点，再转该节点

![splay-zig-zig](C:\Users\Administrator\Desktop\markdown\c++\算法\数据结构\高级数据结构\splay-zig-zig.svg)

* 如果该节点与父节点不为同一类型的节点，那么转两次该节点

![splay-zig-zag](C:\Users\Administrator\Desktop\markdown\c++\算法\数据结构\高级数据结构\splay-zig-zag.svg)

```cpp
void Splay(int x){
    while(fa[x]){
        if(fa[fa[x]]) rotate(get(x) == get(fa[x]) ? fa[x] : x);
        rotate(x);
    }
}
```

### 插入

* 如果树为空，那么设该点为根
* 如果该点权值与插入值相同，那么更新该点数值，并且将该点提取到树根

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

const int N = 1e5 + 10;
struct Splay
{
	int root,tot,fa[N],son[N][2],val[N],cnt[N],sz[N];
	void maintain(int x) {sz[x] = sz[son[x][0]] + sz[son[x][1]] + cnt[x];}

	bool get(int x) {return x == son[fa[x]][1];}

	void clear(int x){son[x][0] = son[x][1] = cnt[x] = sz[x] = val[x] = fa[x] = 0;}

	void rotate(int x){
		int y = fa[x],z = fa[y],chk = get(x);
		son[y][chk] = son[x][chk ^ 1];
		if(son[x][chk ^ 1]) fa[son[x][chk ^ 1]] = y;
		son[x][chk ^ 1] = y;
		fa[y] = x;
		fa[x] = z;
		if(z) son[z][y == son[z][1]] = x;
		maintain(y);
		maintain(x);
	}

	void splay(int x){
		for(int f = fa[x]; f = fa[x],f;rotate(x))
			if(fa[f]) rotate(get(x) == get(f) ? f : x);
		root = x;
	}

	void ins(int k){
		if(!root){
			val[++tot] = k;
			cnt[tot]++;
			root = tot;
			maintain(root);
			return;
		}
		int cur = root,f = 0;
		while(1){
			if(val[cur] == k){
				cnt[cur]++;
				maintain(cur);
				maintain(f);
				splay(cur);
				break;
			}
			f = cur;
			cur = son[cur][val[cur] < k];
			if(!cur){
				val[++tot] = k;
				cnt[tot]++;
				fa[tot] = f;
				son[f][val[f] < k] = tot;
				maintain(tot);
				maintain(f);
				splay(tot);
				break;
			}
		}
	}

	int rk(int k){
		int res = 0,cur = root;
		while(1){
       		if(!cur) return res + 1;
			if(k < val[cur]){
				cur = son[cur][0];
			}else{
				res += sz[son[cur][0]];
				if(k == val[cur]){
					splay(cur);
					return res + 1;
				}
				res += cnt[cur];
				cur = son[cur][1];
			}
		}
	}

	int kth(int k){
		int cur = root;
		while(1){
			if(son[cur][0] && k <= sz[son[cur][0]]){
				cur = son[cur][0];
			}else{
				k -= cnt[cur] + sz[son[cur][0]];
				if(k <= 0){
					splay(cur);
					return val[cur];
				}
				cur = son[cur][1];
			}
		}
	}

	int pre(){
		int cur = son[root][0];
		if(!cur) return cur;
		while(son[cur][1]) cur = son[cur][1];
		splay(cur);
		return cur;
	}

	int nxt(){
		int cur = son[root][1];
		if(!cur) return cur;
		while(son[cur][0]) cur = son[cur][0];
		splay(cur);
		return cur;
	}

	void del(int k){
		rk(k);
		if(cnt[root] > 1){
			cnt[root]--;
			maintain(root);
			return;
		}
		if(!son[root][0] && !son[root][1]){
			clear(root);
			root = 0;
			return;
		}
		if(!son[root][0]){
			int cur = root;
			root = son[root][1];
			fa[root] = 0;
			clear(cur);
			return;
		}
		if(!son[root][1]){
			int cur = root;
			root = son[root][0];
			fa[root] = 0;
			clear(cur);
			return;
		}
		int cur = root;
		int x = pre();
		fa[son[cur][1]] = x;
		son[x][1] = son[cur][1];
		clear(cur);
		maintain(root);
	}
}tr;

void test(){
	int q;cin >> q;
	while(q--){
		int op,x;cin >> op >> x;
		if(op == 1) tr.ins(x);
		else if(op == 2) tr.del(x);
		else if(op == 3) cout << tr.rk(x) << '\n';
		else if(op == 4) cout << tr.kth(x) << '\n';
		else if(op == 5){
			tr.ins(x);
			cout << tr.val[tr.pre()] << '\n';
			tr.del(x);
		}
		else{
			tr.ins(x);
			cout << tr.val[tr.nxt()] << '\n';
			tr.del(x);
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

