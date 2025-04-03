## Z函数（拓展kmp）

### 定义：

* 对应长度为$n$的字符串$s$。定义函数$z[i]$表示$s$和$s[i,n-1]$的最长公共前缀$(LCP)$
* $z[0] = 0$

### 实现方法

* 保存右端点最靠右的区间$[l,r]$，如果新的要计算的$z[i]$的$i$在$[l,r]$区间内，那么就可以根据$[i,r]$和$[i-l,r-l]$相同得出$z[i-l] \leq z[i](z[i]+i<n)$
* 因为r只会被拓展到字符串的长度，因此复杂度为$O(n)$

### 两个字符串

* 求两个字符串同理，不过要继承的不是自己的$z$函数，而是匹配字符串的$z$函数

### 匹配单字串

* 构造$s = p + ? + t$然后统计$z[i]=|p|$的数量就是$t$中$p$出现的次数了

### 本质不同的字串数

### [P5410 【模板】扩展 KMP/exKMP（Z 函数）](https://www.luogu.com.cn/problem/P5410)

* 求b的z函数$get\_z()$
* 求a的每个后缀对b的最长公共前缀$get\_ab\_z()$

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

void get_z(string &s,int n,vector<int>& z){
	z[0] = 0;
	for(int i = 1,l = 0,r = 0;i < n;i++){
		z[i] = 0;
		if(r > i){
			z[i] = min(z[i - l],r - i + 1);
		}
		while(i + z[i] < n && s[i + z[i]] == s[z[i]]) z[i]++;
		if(i + z[i] > r) l = i,r = i + z[i] - 1;
	}
}

void get_ab_z(string &a,string &b,int n1,int n2,vector<int>& z,vector<int> &bz){
	for(int i = 0,l = -1,r = -1;i < n1;i++){
		z[i] = 0;
		if(r >= i){
			z[i] = min(bz[i - l],r - i + 1);
		}
		while(i + z[i] < n1 && z[i] < n2 && a[i + z[i]] == b[z[i]]) z[i]++;
		if(i + z[i] > r) l = i,r = i + z[i] - 1;
	}
}

void test(){
	string a,b;cin >> a >> b;
	int n = b.size();
	ll ans1 = 0;
	vector<int> z(n);
	get_z(b,b.size(),z);
	z[0] = n;
	for(ll i = 0;i < n;i++){
		ans1 ^= (i + 1) * (z[i] + 1);
	}
	cout << ans1 << '\n';
	vector<int> za_b((int)a.size());
	get_ab_z(a,b,a.size(),b.size(),za_b,z);
	n = a.size();
	ans1 = 0;
	for(ll i = 0;i < n;i++){
		ans1 ^= (i + 1) * (za_b[i] + 1);
	}
	cout << ans1 << '\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

### [B. Password](https://codeforces.com/problemset/problem/126/B)

#### 题意：

* 求最长的字串，既是前缀又是后缀，并且不在最前面与最后面

#### 思路：

* 正着跑一遍z函数，反着在跑一遍，然后用单调队列记录离得最远的正反z函数即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

void get_z(string &s,int n,vector<int>& z){
	z[0] = 0;
	for(int i = 1,l = 0,r = 0;i < n;i++){
		z[i] = 0;
		if(r > i){
			z[i] = min(z[i - l],r - i + 1);
		}
		while(i + z[i] < n && s[i + z[i]] == s[z[i]]) z[i]++;
		if(i + z[i] > r) l = i,r = i + z[i] - 1;
	}
}

void test(){
	string s;cin >> s;
	int n = s.size();
	vector<int> zpre(n),zsuf(n);
	get_z(s,n,zpre);
	reverse(s.begin(),s.end());
	get_z(s,n,zsuf);
	reverse(zsuf.begin(),zsuf.end());
	int l = 0,len = 0;

	queue<pair<int,int>> qu;

	for(int i = n - 1;i >= 1;i--){
		while(!qu.empty() && qu.front().first - qu.front().second + 1 > i) qu.pop();
		if(zsuf[i]) qu.push(make_pair(i,zsuf[i]));
		if(!qu.empty()){
			if(len < qu.front().first - i + 1 && qu.front().first - i + 1 <= zpre[i]){
				l = i;
				len = min(qu.front().first - i + 1,zpre[i]);
			}
		}
	}
	queue<pair<int,int>> qqu;
	for(int i = 1;i <= n - 1;i++){
		while(!qqu.empty() && qqu.front().first + qqu.front().second - 1 < i) qqu.pop();
		if(zpre[i]) qqu.push(make_pair(i,zpre[i]));
		if(!qqu.empty()){
			if(len < i - qqu.front().first + 1 && zsuf[i] >= i - qqu.front().first + 1){
				l = qqu.front().first;
				len = i - qqu.front().first + 1;
			}
		}
	}

	reverse(s.begin(),s.end());

	if(len == 0) cout << "Just a legend\n";
	else{
		cout << s.substr(l,len) << '\n';
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

