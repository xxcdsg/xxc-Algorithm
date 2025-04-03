## [F. Railguns](https://codeforces.com/contest/1840/problem/F)

## 其实也就是一个模拟题

## 题意：一开始在$(0,0)$，要去到$(n,m)$，每一回合在$(i,j)$可以走到$(i+1,j)$或走到$(i,j+1)$或者不动，同时在特定回合会对特定的行和列进行射击，因此在那个回合结束不能在特定的行和列，问最小需要的回合数到终点

## 思路：由于$n*m\leq 1e4$，直接考虑暴力dp~~（一开始看成$n、m\leq 1e4$）~~

```C++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int R = 1e2 + 10;
struct shot{
	int t,d,coord;
}s[R];
bool cmp(shot a,shot b){
	return a.t < b.t;
}
void test(){
	int n,m;cin >> n >> m;
	vector<vector<vector<int>>> dp(2,vector<vector<int>>(n + 1,vector<int> (m + 1,0)));
	int r;cin >> r;
	for(int i = 1;i <= r;i++)
		cin >> s[i].t >> s[i].d >> s[i].coord;
	sort(s + 1,s + 1 + r,cmp);
	if(s[1].t > n + m){
		cout << n + m << endl;
		return;
	}
	dp[0][0][0] = 1;
	int op = 0;
	int no = 0;
	int ptop = 1;
	while(!dp[op][n][m]){
		no++;
		bool f = 0;
		for(int i = 0;i <= n;i++)
		for(int j = 0;j <= m;j++){
			if(!dp[op][i][j])
				continue;
			f = 1;
			if(i != n)
				dp[1 - op][i + 1][j] = 1;
			if(j != m)
				dp[1 - op][i][j + 1] = 1;
			dp[1 - op][i][j] = 1;
			dp[op][i][j] = 0;
		}
		if(!f)
			break;
		while(ptop <= r && no == s[ptop].t){
			if(s[ptop].d == 1){
				for(int j = 0;j <= m;j++)
					dp[1 - op][s[ptop].coord][j] = 0;
			}
			else{
				for(int i = 0;i <= n;i++)
					dp[1 - op][i][s[ptop].coord] = 0;
			}
			ptop++;
		}
		op = 1 - op;
	}
	if(dp[op][n][m])
		cout << no << endl;
	else
		cout << -1 << endl;
}
#undef int
int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```