## 后缀数组

* 我们令后缀 $i$ 为 $s[i...n]$ ，并编号 
*  $sa[i]$ 代表排序后第 $i$ 小的后缀的编号
*  $rk[i]$ 代表后缀 $i$ 的排名
* 那么我们有 $sa[rk[i]] = rk[sa[i]]=i$

### 做法：

##### 1.$O(n^2log_2n)$ 做法：和你爆了

* 暴力提取所有后缀字符串并用 $sort$ 排序

##### 2. $O(n(log_2n)^2)$ 做法

* 采用倍增的想法，首先我们给所有字符串按长度为 $1$ 来排序，然后长度为 $2$ 的排序就是先按第一个长度为 $1$ 的先排序，然后按后面的长度为 $1$ 的排序，同理长度为 $4$ 的就是按两个长度为 $2$ 的排序，这样循环 $log_2n$ 轮即可

```cpp
const int N = 1e6 + 10;
int sa[N],rk[N << 1],oldrk[N << 1];
void get_sa(string& s){
	int len = s.size();
	s = ' ' + s;
	for(int i = 1;i <= len;i++){
		sa[i] = i;
		rk[i] = s[i];
	}
	for(int w = 1;w < len;w <<= 1){//现在的长度
		sort(sa + 1,sa + len + 1,[&](int x,int y){
			return rk[x] == rk[y] ? rk[x + w] < rk[y + w] : rk[x] < rk[y];
		});
		memcpy(oldrk,rk,sizeof(int)*(len + 1));
		for(int p = 0,i = 1;i <= len;i++){
			if(oldrk[sa[i]] == oldrk[sa[i - 1]] && oldrk[sa[i] + w] == oldrk[sa[i - 1] + w]){
				rk[sa[i]] = p;
			}else{
				rk[sa[i]] = ++p;
			}
		}
	}
}
```

##### 3. $O(nlog_2n)$ 做法

* 那么这就相当于 $log_2n$ 个双关键词排序，值域为 $127(轮次1),n$ ，那么采用基数排序LSD，复杂度为 $O(nlog_2n)$

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

const int N = 1e6 + 10,K = 1e2 + 10,W = 1e6 + 10;

int n,sa[N],rk[N << 1],oldrk[N << 1],id[N],cnt[N];

void counting_sort(int w){
	memset(cnt, 0,sizeof(int) * (n + 1));
    memcpy(id, sa,sizeof(int) * (n + 1));
    for(int i = 1;i <= n;i++) ++cnt[rk[id[i] + w]];
    for(int i = 1;i <= n;i++) cnt[i] += cnt[i - 1];
    for(int i = n;i >= 1;i--) sa[cnt[rk[id[i] + w]]--] = id[i];
}

void test(){
	string s;cin >> s;
	n = s.size();
	s = ' ' + s;
	int m = 127;
	for(int i = 1;i <= n;i++) ++cnt[rk[i] = s[i]];
	for(int i = 1;i <= m;i++) cnt[i] += cnt[i - 1];
	for(int i = n;i >= 1;i--) sa[cnt[rk[i]]--] = i;
	memcpy(oldrk,rk,sizeof(int) * (n + 1));
	for(int p = 0,i = 1;i <= n;i++){
		if(oldrk[sa[i]] == oldrk[sa[i - 1]]){
			rk[sa[i]] = p;
		}else{
			rk[sa[i]] = ++p;
		}
	}

	for(int w = 1;w < n;w <<= 1){
		counting_sort(w);//先按+w关键词排序
		counting_sort(0);//再按原本的w来排序
		memcpy(oldrk,rk,sizeof(int) * (n + 1));
		for(int p = 0,i = 1;i <= n;i++){
			if(oldrk[sa[i]] == oldrk[sa[i - 1]] && oldrk[sa[i] + w] == oldrk[sa[i - 1] + w]){
				rk[sa[i]] = p;
			}else{
				rk[sa[i]] = ++p;
			}
		}
	}
	for(int i = 1;i <= n;i++) cout << sa[i] << ' ';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

* 开始常数优化

###### 第二关键词无需计数排序

* 对于 $+w$ 原本就有的字符串，原 $+w$ 排序的顺序就是新的顺序
* 对于 $+w$ 后为空的字符串，只需要按前面 $w$ 的排序即可，因此将将它们直接不管顺序全放到前面即可

```cpp
int p = 0;
for (int i = n; i > n - w; i--) id[++p] = i;
for (int i = 1; i <= n; ++i) {
	if (sa[i] > w) id[++p] = sa[i] - w;
}
```

###### 减少非连续内存访问

* 在 $counting\_sort$ 中，我们连续两次使用了 $rk[id[i] + w]$ ，那么第二次可以将使用上次记录的 $key[i]=rk[id[i]+w]$

```cpp
for(int i = 1;i <= n;i++) ++cnt[key[i] = rk[id[i] + w]];
for(int i = 1;i <= n;i++) cnt[i] += cnt[i - 1];
for(int i = n;i >= 1;i--) sa[cnt[key[i]]--] = id[i];
```

* 替换 $oldrk[sa[i]] == oldrk[sa[i - 1]] \&\& oldrk[sa[i] + w] == oldrk[sa[i - 1] + w]$

```cpp
bool cmp(int x,int y,int w){
	return oldrk[x] == oldrk[y] && oldrk[x + w] == oldrk[y + w];
}
```

###### 如果排名都不相同，那么就没必要继续排序了

```cpp
if(p == n)
	break;
```

##### 解法

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

const int N = 1e6 + 10,K = 1e2 + 10,W = 1e6 + 10;

int n,sa[N],rk[N << 1],oldrk[N << 1],id[N],cnt[N],key[N];

void counting_sort(int w){
	memset(cnt, 0,sizeof(int) * (n + 1));
    // memcpy(id, sa,sizeof(int) * (n + 1));
    for(int i = 1;i <= n;i++) ++cnt[key[i] = rk[id[i] + w]];
    for(int i = 1;i <= n;i++) cnt[i] += cnt[i - 1];
    for(int i = n;i >= 1;i--) sa[cnt[key[i]]--] = id[i];
}

bool cmp(int x,int y,int w){
	return oldrk[x] == oldrk[y] && oldrk[x + w] == oldrk[y + w];
}

void test(){
	string s;cin >> s;
	n = s.size();
	s = ' ' + s;
	int m = 127;
	for(int i = 1;i <= n;i++) ++cnt[rk[i] = s[i]];
	for(int i = 1;i <= m;i++) cnt[i] += cnt[i - 1];
	for(int i = n;i >= 1;i--) sa[cnt[rk[i]]--] = i;
	memcpy(oldrk,rk,sizeof(int) * (n + 1));
	for(int p = 0,i = 1;i <= n;i++){
		if(oldrk[sa[i]] == oldrk[sa[i - 1]]){
			rk[sa[i]] = p;
		}else{
			rk[sa[i]] = ++p;
		}
	}

	for(int w = 1;w < n;w <<= 1){
		//counting_sort(w);
		int p = 0;
		for (int i = n; i > n - w; i--) id[++p] = i;
		for (int i = 1; i <= n; ++i) {
			if (sa[i] > w) id[++p] = sa[i] - w;
		}

		counting_sort(0);
		memcpy(oldrk,rk,sizeof(int) * (n + 1));
		p = 0;
		for(int i = 1;i <= n;i++){
			if(cmp(sa[i],sa[i - 1],w)){
				rk[sa[i]] = p;
			}else{
				rk[sa[i]] = ++p;
			}
		}
		if(p == n)
			break;
	}
	for(int i = 1;i <= n;i++) cout << sa[i] << ' ';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

```cpp
const int N = 1e6 + 10;
int n,sa[N],rk[N << 1],oldrk[N << 1],id[N],cnt[N],key[N];
void counting_sort(int w){
	memset(cnt, 0,sizeof(int) * (n + 1));
    // memcpy(id, sa,sizeof(int) * (n + 1));
    for(int i = 1;i <= n;i++) ++cnt[key[i] = rk[id[i] + w]];
    for(int i = 1;i <= n;i++) cnt[i] += cnt[i - 1];
    for(int i = n;i >= 1;i--) sa[cnt[key[i]]--] = id[i];
}
bool cmp(int x,int y,int w){
	return oldrk[x] == oldrk[y] && oldrk[x + w] == oldrk[y + w];
}
void get_sa(string& s){
	n = s.size();
	s = ' ' + s;
	int m = 127;
	for(int i = 1;i <= n;i++) ++cnt[rk[i] = s[i]];
	for(int i = 1;i <= m;i++) cnt[i] += cnt[i - 1];
	for(int i = n;i >= 1;i--) sa[cnt[rk[i]]--] = i;
	memcpy(oldrk,rk,sizeof(int) * (n + 1));
	for(int p = 0,i = 1;i <= n;i++){
		if(oldrk[sa[i]] == oldrk[sa[i - 1]]){
			rk[sa[i]] = p;
		}else{
			rk[sa[i]] = ++p;
		}
	}

	for(int w = 1;w < n;w <<= 1){
		//counting_sort(w);
		int p = 0;
		for (int i = n; i > n - w; i--) id[++p] = i;
		for (int i = 1; i <= n; ++i) {
			if (sa[i] > w) id[++p] = sa[i] - w;
		}

		counting_sort(0);
		memcpy(oldrk,rk,sizeof(int) * (n + 1));
		p = 0;
		for(int i = 1;i <= n;i++){
			if(cmp(sa[i],sa[i - 1],w)){
				rk[sa[i]] = p;
			}else{
				rk[sa[i]] = ++p;
			}
		}
		if(p == n)
			break;
	}
}
```

##### O(n) 的我不会

### 应用

### 1.寻找最小的循环移动位置

##### [P4051 [JSOI2007] 字符加密](https://www.luogu.com.cn/problem/P4051)

##### 题意：

* 给一个字符串循环移动，并排序，问最后的字符连起来的字符串是什么

##### 思路：

* 循环移动，那么我们想到给复制字符串 $ss$
* 然后我们求后缀数组，那么 $ss$ 的前 $n$ 个对应的后缀字符串就包含了所有循环移动

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

const int N = 2e5 + 10;
int n,sa[N],rk[N << 1],oldrk[N << 1],id[N],cnt[N],key[N];
void counting_sort(int w){
	memset(cnt, 0,sizeof(int) * (n + 1));
    // memcpy(id, sa,sizeof(int) * (n + 1));
    for(int i = 1;i <= n;i++) ++cnt[key[i] = rk[id[i] + w]];
    for(int i = 1;i <= n;i++) cnt[i] += cnt[i - 1];
    for(int i = n;i >= 1;i--) sa[cnt[key[i]]--] = id[i];
}
bool cmp(int x,int y,int w){
	return oldrk[x] == oldrk[y] && oldrk[x + w] == oldrk[y + w];
}
void get_sa(string& s){
	n = s.size();
	s = ' ' + s;
	int m = 127;
	for(int i = 1;i <= n;i++) ++cnt[rk[i] = s[i]];
	for(int i = 1;i <= m;i++) cnt[i] += cnt[i - 1];
	for(int i = n;i >= 1;i--) sa[cnt[rk[i]]--] = i;
	memcpy(oldrk,rk,sizeof(int) * (n + 1));
	for(int p = 0,i = 1;i <= n;i++){
		if(oldrk[sa[i]] == oldrk[sa[i - 1]]){
			rk[sa[i]] = p;
		}else{
			rk[sa[i]] = ++p;
		}
	}

	for(int w = 1;w < n;w <<= 1){
		//counting_sort(w);
		int p = 0;
		for (int i = n; i > n - w; i--) id[++p] = i;
		for (int i = 1; i <= n; ++i) {
			if (sa[i] > w) id[++p] = sa[i] - w;
		}

		counting_sort(0);
		memcpy(oldrk,rk,sizeof(int) * (n + 1));
		p = 0;
		for(int i = 1;i <= n;i++){
			if(cmp(sa[i],sa[i - 1],w)){
				rk[sa[i]] = p;
			}else{
				rk[sa[i]] = ++p;
			}
		}
		if(p == n)
			break;
	}
}

void test(){
	string s;cin >> s;
	s = s + s;
	get_sa(s);
	for(int i = 1;i <= n;i++){
		if(sa[i] <= n / 2){
			cout << s[sa[i] + n / 2 - 1];
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

### 2.在字符串中找子串

* 在线在主串 $T$ 中找模式串 $S$ ，$T$ 不变
* 我们先求出 $T$ 的后缀数组，在后缀数组中二分找 $S$
* 每次比较复杂度为 $|S|$ ，最多找 $log_2|T|$ ，复杂度为$O((|T|+q|S|)log_2T)$

### 3.字符串首尾取字符最小化字典序

##### [P2870 [USACO07DEC] Best Cow Line G](https://www.luogu.com.cn/problem/P2870)

* 很容易可以想到，我们左右取字符要看那边的字典序小取那边
* 普通后缀数组可得出前面字符的大小排序，那么我们再添加一个相反字符串求后缀数组即可得到两者的比较

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

const int N = 1e6 + 10;
int n,sa[N],rk[N << 1],oldrk[N << 1],id[N],cnt[N],key[N];
void counting_sort(int w){
	memset(cnt, 0,sizeof(int) * (n + 1));
    // memcpy(id, sa,sizeof(int) * (n + 1));
    for(int i = 1;i <= n;i++) ++cnt[key[i] = rk[id[i] + w]];
    for(int i = 1;i <= n;i++) cnt[i] += cnt[i - 1];
    for(int i = n;i >= 1;i--) sa[cnt[key[i]]--] = id[i];
}
bool cmp(int x,int y,int w){
	return oldrk[x] == oldrk[y] && oldrk[x + w] == oldrk[y + w];
}
void get_sa(string& s){
	n = s.size();
	s = ' ' + s;
	int m = 127;
	for(int i = 1;i <= n;i++) ++cnt[rk[i] = s[i]];
	for(int i = 1;i <= m;i++) cnt[i] += cnt[i - 1];
	for(int i = n;i >= 1;i--) sa[cnt[rk[i]]--] = i;
	memcpy(oldrk,rk,sizeof(int) * (n + 1));
	for(int p = 0,i = 1;i <= n;i++){
		if(oldrk[sa[i]] == oldrk[sa[i - 1]]){
			rk[sa[i]] = p;
		}else{
			rk[sa[i]] = ++p;
		}
	}

	for(int w = 1;w < n;w <<= 1){
		//counting_sort(w);
		int p = 0;
		for (int i = n; i > n - w; i--) id[++p] = i;
		for (int i = 1; i <= n; ++i) {
			if (sa[i] > w) id[++p] = sa[i] - w;
		}

		counting_sort(0);
		memcpy(oldrk,rk,sizeof(int) * (n + 1));
		p = 0;
		for(int i = 1;i <= n;i++){
			if(cmp(sa[i],sa[i - 1],w)){
				rk[sa[i]] = p;
			}else{
				rk[sa[i]] = ++p;
			}
		}
		if(p == n)
			break;
	}
}

void test(){
	int nn;cin >> nn;
	string s;
	for(int i = 1;i <= n;i++){
		char c;cin >> c;
		s.push_back(c);
	}
	string ss = s;
	reverse(s.begin(),s.end());
	s = ss + '|' + s;
	get_sa(s);

	int l = 1,r = n;
	string ans;
	while(l <= r){
		if(rk[l] < rk[2*n + 2 - r]){
			ans.push_back(s[l++]);
		}else{
			ans.push_back(s[r--]);
		}
	}
	int no = 0;
	for(char c : s){
		cout << c;
		no++;
		if(no % 80 == 0) cout << '\n';
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

### height数组

##### LCP(最长公共前缀)

##### height数组定义

*  $height[i]=lcp(sa[i],sa[i-1])$
* 也就是排名为 $i$ 的后缀与排名为 $i-1$ 的后缀的最长公共前缀

##### 引理

*  $height[rk[i]] \geq height[rk[i-1]]-1$

当 $height[rk[i-1]]-1 \leq 1$ 一定成立

当 $height[rk[i-1]]-1 > 1$

也就是 $lcp(sa[rk[i-1]],sa[rk[i-1]-1])>1$

我们假设后缀 $sa[rk[i-1]]=aAD,sa[rk[i-1]-1]=aAB$

我们就有 $|D|=height[rk[i-1]]-1,D>B$

因为 $sa[rk[i-1]]$ 就是后缀 $i-1$ ，因此后缀 $i$ 就可以表示为 $AD$

同时肯定存在一个后缀他为 $AB$

假如 $AD$ 与 $AB$ 的排名不相接，那么中间的也肯定是以 $A$ 开头，那么 $height[i]$ 就至少为 $|A|=height[rk[i-1]]-1$

* 好酷的证明，但是一想也就那么回事
* 那么 $O(n)$ 处理 $height$ 数组就是

```cpp
for(int i = 1,k = 0;i <= n;i++){
    if(rk[i] == 0) continue; // height[0] 默认为0
    if(k) k--;
    while(s[i + k] == s[sa[rk[i] - 1] + k]) k++;
    height[rk[i]] = k;
}
```

##### 两子串最长公共前缀

*  $lcp(sa[i],sa[j])=min\{height[i+1...j]\}$
* 很好理解，假设相同最高位为 $x$ ，那么在改变 $x+1$ 是，当时的 $height$ 肯定会等于 $x$ ，同时因为前面位相同，肯定不会小于 $x$

##### 比较一个字符串的两个子串的大小关系

* 先根据 $lcp(sa[i],sa[j])=min\{height[i+1...j]\}$ 看是否到最大长度，如果没到，那么后缀排名就是两个子串排名，否则看长度

##### 不同子串数目

* $\frac{n(n+1)}{2}-\sum_{i=2}^nheight[i]$
* 意会一下， $height[i]$ 代表前面的子串存在长度小于等于 $height[i]$ 的子串了，因此去掉

##### 出现至少 $k$ 次的子串的最大长度

##### [P2852 [USACO06DEC] Milk Patterns G](https://www.luogu.com.cn/problem/P2852)

* 二分哈希也能做
*  $height$ 数组的话就是求连续 $k-1$ 的长度内最大的最小元素，用双端队列求区间最小值即可

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

const int N = 2e4 + 10;
int n,sa[N],rk[N << 1],oldrk[N << 1],id[N],cnt[N],key[N];
void counting_sort(int w){
	memset(cnt, 0,sizeof(int) * (n + 1));
    // memcpy(id, sa,sizeof(int) * (n + 1));
    for(int i = 1;i <= n;i++) ++cnt[key[i] = rk[id[i] + w]];
    for(int i = 1;i <= n;i++) cnt[i] += cnt[i - 1];
    for(int i = n;i >= 1;i--) sa[cnt[key[i]]--] = id[i];
}
bool cmp(int x,int y,int w){
	return oldrk[x] == oldrk[y] && oldrk[x + w] == oldrk[y + w];
}
void get_sa(string &s){
	n = s.size();
	s = ' ' + s;
	int m = 127;
	for(int i = 1;i <= n;i++) ++cnt[rk[i] = s[i]];
	for(int i = 1;i <= m;i++) cnt[i] += cnt[i - 1];
	for(int i = n;i >= 1;i--) sa[cnt[rk[i]]--] = i;
	memcpy(oldrk,rk,sizeof(int) * (n + 1));
	for(int p = 0,i = 1;i <= n;i++){
		if(oldrk[sa[i]] == oldrk[sa[i - 1]]){
			rk[sa[i]] = p;
		}else{
			rk[sa[i]] = ++p;
		}
	}

	for(int w = 1;w < n;w <<= 1){
		//counting_sort(w);
		int p = 0;
		for (int i = n; i > n - w; i--) id[++p] = i;
		for (int i = 1; i <= n; ++i) {
			if (sa[i] > w) id[++p] = sa[i] - w;
		}

		counting_sort(0);
		memcpy(oldrk,rk,sizeof(int) * (n + 1));
		p = 0;
		for(int i = 1;i <= n;i++){
			if(cmp(sa[i],sa[i - 1],w)){
				rk[sa[i]] = p;
			}else{
				rk[sa[i]] = ++p;
			}
		}
		if(p == n)
			break;
	}
}

int height[N];

void get_height(int n,const string &s){
	for(int i = 1,k = 0;i <= n;i++){
	    if(rk[i] == 0) continue; // height[0] 默认为0
	    if(k) k--;
	    while(s[i + k] == s[sa[rk[i] - 1] + k]) k++;
	    height[rk[i]] = k;
	}
}

void test(){
	int n,k;cin >> n >> k;
	string s;
	for(int i = 1;i <= n;i++){
		int x;cin >> x;
		s.push_back(x);
	}
	if(k == 1){
		cout << n << '\n';
	}else{
		get_sa(s);
		get_height(n,s);
		deque<pair<int,int>> qu;
		int ans = 0;
		k--;
		for(int i = 1;i <= k;i++){
			while(!qu.empty() && qu.front().first > height[i]){
				qu.pop_front();
			}
			qu.push_front({height[i],i});
		}
		ans = qu.back().first;
		for(int i = k + 1;i <= n;i++){
			while(!qu.empty() && qu.front().first > height[i]){
				qu.pop_front();
			}
			qu.push_front({height[i],i});
			while(qu.back().second <= i - k)
				qu.pop_back();
			ans = max(ans,qu.back().first);
		}
		cout << ans << '\n';
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

