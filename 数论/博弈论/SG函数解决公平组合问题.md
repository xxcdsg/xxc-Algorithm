# SG函数解决公平组合问题

## 公平组合问题：

- 游戏有两个人参与，二者轮流做出决策，双方均知道游戏的完整信息；
- 任意一个游戏者在某一确定状态可以作出的决策集合只与当前的状态有关，而与游戏者无关（如象棋就为非公平组合游戏，因为你不能操作对手的棋子，那么你可以操作的集合就与对手不同）；
- 游戏中的同一个状态不可能多次抵达，游戏以玩家无法行动为结束，且游戏一定会在有限步后以非平局结束。

## SG值：

* 定义SG值为不属于可到集合的SG值的最小非负整数
* 定义必败状态SG为0
* 那么必败状态(SG值 = 0)必定不能到必败状态
* 必胜状态(SG值 ！= 0)必定能到必败状态(SG = 0)(因为可到集合SG值的最小值不为0)

## SG值定理

* 若有多个这样的公平组合游戏，那么求它们的异或值，就是它的SG值，为0说明必败，否则必胜
* 如果一个公平组合游戏可以分解为多个，就求它们的异或值。

## 或者情况与分离情况

* 如果可以转移到A或B状态，那么直接取A，B状态的SG值即可
* 如果是可以转移到A与B共存状态，那么取A^B即可

## 例题

### 1.暴力型

[Cutting Game](https://vjudge.net/problem/OpenJ_Bailian-2311)

### 每次切一刀，第一个切出1*1的方块的胜利

### $2\leq n,m \leq 200$

### 有1时必胜，设SG值为1，其他递推即可

```c++
#include<bits/stdc++.h>
using namespace std;
int sg[201][201];
int main()
{
	for(int i = 1;i <= 200;i++){
		sg[i][1] = sg[1][i] = 1;
	}
	for(int i = 1;i <= 200;i++)
	for(int j = 1;j <= 200;j++){
		set<int> se;
		int _sg = 0;
		for(int _i = 2;_i <= i - 2;_i++){
			se.insert(sg[_i][j]^sg[i-_i][j]);
		}
		for(int _j = 2;_j <= j - 2;_j++){
			se.insert(sg[i][_j]^sg[i][j-_j]);
		}
		for(auto x:se){
			if(_sg == x){
				_sg++;
			}else
				break;
		}
		sg[i][j] = _sg;
	}
	int w,h;
	while(cin >> w >> h){
		if(sg[w][h])
			cout << "WIN" << endl;
		else
			cout << "LOSE" << endl;
	}
}
```

### 2.打表找规律型

[G. Integer Game](https://codeforces.com/gym/103860/problem/G)

### 题意：给n个集合$[l_i,r_i]$和p，每次选一个集合和一个x，要求$x*p \geq r_i$

### 思路：打个表求SG函数先

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int sg[1001];
	int p = 3,l = 1;
	for(int r = 1;r <= 100;r++){
		set<int> se;
		for(int x = r;x*p >= r;x--){
			if(x <= l)
				se.insert(0);
			else
				se.insert(sg[x-1]);
		}
		int _sg = 0;
		for(auto x:se){
			if(_sg == x)
				_sg++;
			else
				break;
		}
		cout << (sg[r] = _sg) << ' ';
	}
}
```

### 得出规律：前$ql-l+1$个从$1$到$ql-l+1$,然后接上一个0，然后是长度为$p$的循环递增节，如果不是p的整数倍，那么正常递增，否则所有后面的p的整数倍的形成和原序列相同的序列（用递归求）

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
ll get_sg(ll l,ll r,ll p){
	ll num = r - l + 1;
	if(num <= p*l - l + 1)
		return num;
	else if(num == p*l - l + 2)
		return 0;
	else{
		num -= p*l - l + 2;
		if(num % p == 0)
			return get_sg(l,l+num/p-1,p);
		else
			return (num/p)*(p - 1) + (num % p) + p*l - l + 1;
	}
}
int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	int t;cin >> t;
	while(t--){
		ll nim = 0;
		int n;
		ll p;cin >> n >> p;
		for(int i = 1;i <= n;i++){
			ll l,r;cin >> l >> r;
			nim ^= get_sg(l,r,p);
		}
		if(nim == 0)
			cout << "Second" << endl;
		else
			cout << "First" << endl;
	}
}
```

### [A Funny Game](https://vjudge.net/contest/567667#problem/F)

~~做过看到这题我就想笑~~

#### 题意：一个环，每次可以取1~2连续的节点，取了点后就断开了环或者线，不能取的败

#### 思路：一个环比较难直接求sg函数，但一条线可以分解为两条线，因此sg函数比较好求，而环也可以转换为环长-1与环长-2的线的问题，那么只要求环长-1与环长-2即可

#### 打个表先

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 1e3 + 10;
int sg[N];
int main(){
	sg[0] = 0;cout << 0 << ' ';
	for(int i = 1;i <= 1e3;i++){
		bool vis[N] = {0};
		for(int j = 1;j <= i;j++){
			vis[sg[i - j]^sg[j - 1]] = 1;
			if(j > 1)
				vis[sg[i - j]^sg[j - 2]] = 1;
		}
		int _sg = 0;
		sg[i] = 0;
		while(vis[_sg]){
			sg[i] = ++_sg;
		}
		cout << sg[i] << ' ';
	}
}
```

![打表](C:\Users\Administrator\Desktop\markdown\打表.PNG)

#### 笑了，除了0必败外其他均必胜，也就是说作为环除了$n\leq2$的情况先手能胜利，其他情况先手均败

```cpp
#include<iostream>
using namespace std;
int main(){
	int n;
	cin >> n;
	while(n != 0){
		if(n <= 2)
			cout << "Alice" << endl;
		else
			cout << "Bob" << endl;
		cin >> n;
	}
}
```

#### 那么我们来正常想一下，先手将一个环变成了一条线，那么后手在中间取1或者2，一定有方法将它分成完全相同的两份，那么后手只要模仿先手的操作，那么后手一定是取完所有点的人，因此除了$n=1,2$的情况，其他情况后手均必胜

~~想笑~~

### [G - A Chess Game](https://vjudge.net/contest/567667#problem/G)

#### 题意：N个位置，相互连接形成一个有向拓扑图，在这些位置上放棋子，每个人可以移动棋子一步，谁先不能移动就输

####  思路：深度为0，SG值就为0，~~反推即可~~，~~然后爆时间了~~，用dfs正向推，记忆化搜索即可，然后棋子放哪就取它的SG值异或即可

```cpp
#include<iostream>
#include<stdio.h>
#include<set>
#include<queue>
#include<vector>
using namespace std;
const int N = 1e3 + 10;
int sg[N];
vector<int> ed[N];

int ask(int x){
	if(sg[x] != -1) return sg[x];
	int sz = ed[x].size();
	bool use[N] = {0};
	for(int i = 0;i < sz;i++){
		use[ask(ed[x][i])] = 1;
	}
	sg[x] = 0;
	int _sg = 0;
	while(1){
		if(use[_sg])
			sg[x] = ++_sg;
		else
			break;
	}
	return sg[x];
}

int main(){
	int n;
	while(~scanf("%d",&n)){
		queue<int> qu;
		for(int i = 0;i < n;i++){
			int num;scanf("%d",&num);
			ed[i].clear();
			for(int j = 1;j <= num;j++){
				int x;scanf("%d",&x);
				ed[i].push_back(x);
			}
		}
		int m;
		memset(sg,-1,sizeof(sg));
		while(scanf("%d",&m),m){
			int nim = 0;
			for(int i = 1;i <= m;i++){
				int x;scanf("%d",&x);
				nim^=ask(x);
			}
			if(nim)
				puts("WIN");
			else
				puts("LOSE");
		}
	}
}
```

### [H - S-Nim](https://vjudge.net/contest/567667#problem/H)

#### 题意：每次只能取S集合内数字的nim游戏

#### 思路：总数最大为10000，集合内数100，暴力求SG就$10^6$，直接求即可

```cpp
#include<iostream>
#include<stdio.h>
#include<set>
#include<queue>
#include<vector>
using namespace std;
const int N = 1e4 + 10,M = 1e2 + 10;
int sg[N],s[M];
int n,m;

void getnim(){
	for(int i = 1;i <= 1e4;i++){
		bool vis[M] = {0};
		for(int j = 1;j <= m;j++)
			if(i - s[j]>= 0)
				vis[sg[i - s[j]]] = 1;
		int _sg = 0;
		sg[i] = 0;
		while(1){
			if(vis[_sg])
				sg[i] = ++_sg;
			else
				break;
		}
	}
}

int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	cin >> m;
	while(m != 0){
		for(int i = 1;i <= m;i++)
			cin >> s[i];
		getnim();
		cin >> n;
		while(n--){
			int t;cin >> t;
			int nim = 0;
			for(int i = 1;i <= t;i++){
				int x;cin >> x;
				nim ^= sg[x];
			}
			if(nim)
				cout << 'W';
			else
				cout << 'L';
		}
		cout << endl;
		cin >> m;
	}
}
```

