* 试一下新的IDE

# [KEYENCE Programming Contest 2023 Summer（AtCoder Beginner Contest 315）](https://atcoder.jp/contests/abc315)

## [A - tcdr](https://atcoder.jp/contests/abc315/tasks/abc315_a)

### 题意：将字符串去除a`, `e`, `i`, `o`, `u

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
	string s;cin >> s;
	int n = s.size();
	for(int i = 0;i < n;i++)
		if(s[i] != 'a' && s[i] != 'e' && s[i] != 'i' && s[i] != 'o' && s[i] != 'u')
			cout << s[i];
	return 0;
}
```

## [B-The Middle Day](https://atcoder.jp/contests/abc315/tasks/abc315_b)

### 题意：给出每个月的天数，求最中间的那天是几月几日

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 1e2 + 10;
int month[N];
int main(){
	int m;cin >> m;
	int al = 0;
	for(int i = 1;i <= m;i++){
		cin >> month[i];
		al += month[i];
	}
	int mid = (al + 1) / 2;
	for(int i = 1;i <= m;i++){
		if(mid - month[i] <= 0){
			cout << i << ' ' << mid << endl;
			break;
		}
		mid -= month[i];
	}
	return 0;
}
```

## [C-Flavors](https://atcoder.jp/contests/abc315/tasks/abc315_c)

### 题意：N个物品，有风味与美味度两个量，选两个（不能选一样的），如果风味相同那么得分为较大的美味度+较小的美味度/2，否则为两个美味度相加

### 思路：分两种情况，相同与不同，如果不同就是选两个最大的，相同就是选相同最大较大的美味度+较小的美味度/2，那么对于每个风味记录最大的美味度与次大的美味度即可

```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n;cin >> n;
	map<int,pair<int,int>> mp;
	for(int i = 1;i <= n;i++){
		int a,b;cin >> a >> b;
		if(b > mp[a].first) mp[a].second = mp[a].first,mp[a].first = b;
		else if(b > mp[a].second) mp[a].second = b;
	}
	int x = 0,y = 0,tem = 0;
	for(auto [a,pa]:mp){
		int b = pa.first,c = pa.second;
		tem = max(tem,b + c / 2);
		if(b > x) y = x,x = b;
		else if(b > y) y = b;
	}
	cout << max(x + y,tem) << endl;
}
```

## [D-Magical Cookies](https://atcoder.jp/contests/abc315/tasks/abc315_d)

### 题意：h*w的矩阵，每轮如果一行上元素数量大于2并且完全相同就标记，列上也是，然后去除所有标记的元素，问最后还剩多少元素

### 思路：元素种类只有26种，可以枚举，可知如果行被选了，那么列就不需要访问它，同理列也是，因此用set维护还剩下的行与列来优化，用num记录行列上特定元素剩下的数量，可知复杂度不超过h*w，因为每个元素不会被重复访问

```cpp
#include<bits/stdc++.h>
using namespace std;

#define ll long long
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);

const int N = 2e3 + 10;

int num[2][N][26];

char c[N][N];

set<int> se[2];

int main(){
	int h,w;cin >> h >> w;
	for(int i = 1;i <= h;i++) se[0].insert(i);
	for(int i = 1;i <= w;i++) se[1].insert(i);
	for(int i = 1;i <= h;i++){
		for(int j = 1;j <= w;j++){
			cin >> c[i][j];
			c[i][j] -= 'a';
			num[0][i][c[i][j]]++;
			num[1][j][c[i][j]]++;
		}
		getchar();
	}
	int ans = h*w;
	queue<int> qu[2];
	while(1){
		for(auto i:se[0]){
			bool f = 0;
			int ff = 0;
			for(int j = 0;j < 26;j++){
				if(num[0][i][j]) ff++;
				if(num[0][i][j] >= 2) f = 1;
			}
			if(f && ff == 1) qu[0].push(i);
		}
		for(auto i:se[1]){
			bool f = 0;
			int ff = 0;
			for(int j = 0;j < 26;j++){
				if(num[1][i][j]) ff++;
				if(num[1][i][j] >= 2) f = 1;
			}
			if(f && ff == 1) qu[1].push(i);
		}
		if(qu[0].empty() && qu[1].empty()) break;
		while(!qu[0].empty()){
			int x = qu[0].front();qu[0].pop();
			se[0].erase(x);
			for(auto i:se[1]){
				num[0][x][c[x][i]]--;
				num[1][i][c[x][i]]--;
				ans--;
			}
		}
		while(!qu[1].empty()){
			int x = qu[1].front();qu[1].pop();
			se[1].erase(x);
			for(auto i:se[0]){
				num[0][i][c[i][x]]--;
				num[1][x][c[h][x]]--;
				ans--;
			}
		}
	}
	cout << ans << endl;
}
```

## [E-Prerequisites](https://atcoder.jp/contests/abc315/tasks/abc315_e)

### 题意：读每本书之前有前置的书必须要读，问要读书1最少需要读的书的其中一种读法

### 思路：图论，建图，dfs搜索

```cpp
#include<bits/stdc++.h>
using namespace std;

#define ll long long
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);

const int N = 2e5 + 10;

bool vis[N];

vector<int> ed[N];

queue<int> ans;

void dfs(int x){
	for(auto y:ed[x]){
		if(!vis[y]){
			dfs(y);
			vis[y] = 1;
		}
	}
	ans.push(x);
}

int main(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++){
		int c;
		cin >> c;
		while(c--){
			int x;cin >> x;
			ed[i].push_back(x);
		}
	}
	dfs(1);
	while(!ans.empty()){
		if(ans.front() != 1)
		cout << ans.front() << ' ';
		ans.pop();
	}
}
```

