## 求最大异或和

给定一个非负整数序列 $\{a\}$，初始长度为 $N$。  

有 $M$ 个操作，有以下两种操作类型：  

1. `A x`：添加操作，表示在序列末尾添加一个数 $x$，序列的长度 $N$ 加 $1$。  
2. `Q l r x`：询问操作，你需要找到一个位置 $p$，满足 $l \le p \le r$，使得：$a[p] \oplus a[p+1] \oplus ... \oplus a[N] \oplus x$ 最大，输出最大值。

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 6e5 + 10,M = N * 30;//log(1e7) = 23.25

int n,m;
int s[N];
int tr[M][2],max_id[M];//树，可到的最大id(就是这个是以第几个为基的)
int root[N],idx;//不存内容的根节点，现在的最大id

void insert(int k,int p,int q){//现在的版本，上面完全相同的最大的树对应节点，现在的节点
	max_id[q] = k;
	for(int i = 23;i >= 0;i--){
		int v = s[k] >> i & 1;//第i + 1位为什么
		if(p) tr[q][v ^ 1] = tr[p][v ^ 1];//如果前颗树不是0，就连上前一颗树的对应不同的位
		tr[q][v] = ++idx;//正常给这个位置加上一个新节点
		max_id[tr[q][v]] = k;//新节点
		q = tr[q][v],p = tr[p][v];//跳转到新节点与相同最近节点
	}
}

int query(int l,int r,int C){
	int p = root[r];//从r开始找
	for(int i = 23;i >= 0;i--){
		int v = C >> i & 1;//这一位要什么
		if(max_id[tr[p][v ^ 1]] >= l)//在范围内可以跳转
			p = tr[p][v ^ 1];
		else
			p = tr[p][v];//否则就选差的
	}
	return C ^ s[max_id[p]];
}

int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	cin >> n >> m;
	
	s[0] = 0;
	max_id[0] = -1;//排除l-1为0可能会跳到0节点的可能性
	root[0] = ++idx;
	
	insert(0,0,root[0]);
	
	for(int i = 1;i <= n;i++){
		int x;cin >> x;
		s[i] = s[i - 1] ^ x;
		root[i] = ++idx;
		insert(i,root[i - 1],root[i]);
	}
	
	int l,r,x;
	while(m--){
		char c;cin >> c;
		if(c == 'A'){
			cin >> x;
			n++;
			root[n] = ++idx;
			s[n] = s[n - 1] ^ x;
			insert(n,root[n - 1],root[n]);
		}else{
			cin >> l >> r >> x;
			cout << query(l - 1,r - 1,x ^ s[n]) << endl;
		}
	}
}
```

