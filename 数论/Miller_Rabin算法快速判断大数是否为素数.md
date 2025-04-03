# Miller_Rabin算法快速判断大数是否为素数

## 并不是绝对，这只是一种判断大概率为素数的方法

## 首先根据费马小定理有：$a^{p-1}=1\pmod p(a不为p的倍数且p不是素数)$

## 又因为$p$为素奇数，所以$p-1$为偶数，表示为$p-1=2^dm$

## 所以有$a^{p-1}-1=0\pmod p$

## $a^{2^dm}-1=0\pmod p$

## $(a^{2^{d-1}m}-1)(a^{2^{d-1}m}+1)=0\pmod p$

## 从而有$a^{2^{d-1}m}=1\pmod p||a^{2^{d-1}}=p-1\pmod p$

## 以此类推有$a^m,a^{2m},a^{2^2m}...a^{p-1}$

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long 
ll qpow(ll x,ll y,ll mod){
	__int128 ans = 1,xx = x;
	while(y){
		if(y&1) ans=(ans*xx)%mod;
		xx = (xx*xx) % mod;
		y>>=1;
	}
	return ans;
}
bool f(ll x){
	if(x == 2) return 1;
	if(x < 2 || x % 2 == 0) return 0;
	ll tem = x-1,d = 0;
	while(tem % 2 == 0){
		tem/=2;
		d++;
	}
	bool ff = 1;
	for(int i = 1;i <= 20;i++){
		ll a = rand()%(x-1) + 1;
		__int128 xx = qpow(a,tem,x);
		if(xx == 1 || xx == x - 1) continue;
		int j;
		for(j = 1;j <= d;j++){
			xx = (xx*xx)%x;
			if(xx == x - 1)
				break;
		}
		if(j == d + 1)
			ff = 0;
	}
	return ff;
}
int main()
{
	ll x;
	while(~scanf("%lld",&x)){
		if(f(x))
			cout << 'Y' << endl;
		else
			cout << 'N' << endl;
	}
}
```

