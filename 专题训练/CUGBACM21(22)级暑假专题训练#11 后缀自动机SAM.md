## [A - 后缀自动机（SAM）](https://vjudge.net.cn/problem/洛谷-P3804)

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
struct pos{
	ll len,link;
	map<char,int> next;
}st[N*2];
int ptop,last;
void sam_init(){
	st[0].len = 0;
	st[0].link = -1;
	ptop++;
	last = 0;
}

int cnt[N*2];

void sam_extend(char c){
	int cur = ptop++;
	st[cur].len = st[last].len + 1;
	int p = last;
	while(p != -1 && !st[p].next.count(c)){//不存在向后走c的
		st[p].next[c] = cur;
		p = st[p].link;
	}
	if(p == -1){
		st[cur].link = 0;
	}else{
		int q = st[p].next[c];
		if(st[p].len + 1 == st[q].len){
			st[cur].link = q;
		}else{
			int clone = ptop++;
			st[clone] = st[q];
			st[clone].len = st[p].len + 1;
			while(p != -1 && st[p].next[c] == q){
				st[p].next[c] = clone;
				p = st[p].link;
			}
			st[q].link = st[cur].link = clone;
		}
	}
	last = cur;
	cnt[cur] = 1;
}

void test(){
	string s;cin >> s;
	sam_init();
	int n = s.size();
	for(int i = 0;i < n;i++){
		sam_extend(s[i]);
	}
	ll ans = 0;
	vector<int> in(ptop);
	for(int i = ptop - 1;i >= 1;i--){
		in[st[i].link]++;
	}
	queue<int> qu;
	for(int i = ptop - 1;i >= 1;i--){
		if(in[i] == 0) qu.push(i);
	}
	while(1){
		int x = qu.front();
		qu.pop();
		if(x == 0) break;
		if(cnt[x] > 1)
			ans = max(ans,(ll)cnt[x] * st[x].len);
		in[st[x].link]--;
		if(in[st[x].link] == 0) qu.push(st[x].link);
		cnt[st[x].link] += cnt[x];
	}
	cout << ans << '\n';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [B - 最长公共子串](https://vjudge.net.cn/problem/LibreOJ-171)

