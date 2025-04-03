## [Codeforces Round 856 (Div. 2)](https://codeforces.com/contest/1794)

### D. Counting Factorizations

* 题意：一个数可以表示成$m=p_1^{e_1}p_2^{e_2}p_3^{e_3}...p_k^{e_k}$，其中$p_1<p_2<...<p_k$并且p均为质数，现给出由p序列和e序列组成的长度为2n的序列，问它能代表多少个数
* 思路：首先p序列都是由素数组成并且不重复，所以可以用dp背包来考虑序列中的数取不取作为素数
* 然后p和e有一个组合关系，即$A_n^n$，但是还要考虑e是否有重复，有重复就要除上$A_{num}^{num}$

* 因为最后都要乘上$A_n^n$，所以我们设初始值就为$dp[0][0]=A_n^n$

* 对于每一个数，如果有重复就除上$A_{num}^{num}$（肯定是用逆元）再加上
* 如果是素数就还考虑取这个数的情况

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 2023,MAXA = 1e6 + 10,mod = 998244353;
vector<int> v(2*MAXN);
vector<bool> prime(MAXA,1);
vector<ll> jc(MAXA),jcny(MAXA);
ll fmx(ll x){
	ll ans = 1,y = mod - 2;
	while(y){
		if(y&1) ans = (ans*x) % mod;
		y >>= 1;
		x = (x*x)%mod;
	}
	return ans;
}
void pre_jcny(){
	jc[0] = 1;
	jcny[0] = 1;
	for(int i = 1;i <= MAXA - 10;i++){
		jc[i] = (jc[i-1]*i) % mod;
		jcny[i] = (jcny[i-1]*fmx(i)) % mod;
	}
}
void pre_prime(){
	prime[1] = 0;
	for(int i = 2;i <= MAXA - 10;i++)
	{
		if(prime[i]){
			int ptop = i*2;
			while(ptop <= MAXA - 10){
				prime[ptop] = 0;
				ptop += i;
			}
		}
	}
}
ll dp[2][MAXN];//现在攒了这个数量的质数的可能性
int main()
{
	int n;cin >> n;
	pre_prime();
	pre_jcny();
	for(int i = 1;i <= 2*n;i++){
		cin >> v[i];
	}
	sort(v.begin() + 1,v.begin() + 1 + 2*n);
	int op = 0;
	dp[0][0] = jc[n];
	for(int i = 1;i <= 2*n;i++){
		for(int i = 0;i <= n;i++)
			dp[1 - op][i] = 0;
		int num = 1;
		while(i < 2*n && v[i] == v[i + 1])//统计数量
		{
			num++;
			i++;
		}
		if(prime[v[i]]){//可以作为质数
			for(int j = 1;j <= n;j++)
				dp[1 - op][j] = (dp[1 - op][j] + dp[op][j - 1]*jcny[num-1]) % mod;
			for(int j = 0;j <= n;j++)
				dp[1 - op][j] = (dp[1 - op][j] + dp[op][j]*jcny[num]) % mod;//这个数量可能会重复
		}
		else{//不可作为质数
			for(int j = 0;j <= n;j++)
				dp[1 - op][j] = (dp[1 - op][j] + dp[op][j]*jcny[num]) % mod;//这个数量可能会重复
		}
		op = 1 - op;
	}
	cout << dp[op][n];
}
```

