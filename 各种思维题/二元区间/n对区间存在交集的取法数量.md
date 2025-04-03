## [Non-Puzzle: Segment Pair](https://ac.nowcoder.com/acm/contest/57363/I)

### 题意：n对区间，有$2^n$种取法，问能够有交集的取法

### 思路：把所有区间分别根据左端点与右端点排序，枚举左端点为交集的左起点，排除区间右端点在这个左端点左边的，记录0、1、2的数量，如果添加左端点并让选择变成2，说明即可选左，又可选右，因此排除即可

```cpp
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
const int N = 5e5 + 10,mod = 1e9 + 7,inv2 = 500000004;

int num[N];

ll qpow(ll x,ll y){
    ll ans = 1;
    while(y){
        if(y&1) ans = (ans * x) % mod;
        y >>= 1;
        x = (x*x) % mod;
    }
    return ans;
}

struct point{
    int l,r;
    int op;
    int id;
}p[N*4];

bool cmp1(point a,point b){
    return a.l < b.l;
}

bool cmp2(point aa,point bb){
    return aa.r < bb.r;
}

int main(){
    int n;cin >> n;
    ll ans = 0;
    for(int i = 1;i <= n;i++){
        cin >> p[i*2-1].l >> p[i*2-1].r >> p[i*2].l >> p[i*2].r;
        p[i*2-1].op = 1;
        p[i*2].op = 0;
        p[i*2-1].id = p[i*2].id = i;
        p[n*2 + i*2 - 1] = p[i*2 - 1];
        p[n*2 + i*2] = p[i*2];
    }
    int num0 = n;
    int num2 = 0;
    int r = 2*n + 1;
    sort(p + 1,p + 1 + 2*n,cmp1);
    sort(p + 1 + 2*n,p + 1 + 4*n,cmp2);
    for(int i = 1;i <= 2*n;i++){
        int j = i;
        map<int,bool> mp;
        int _num2 = 0;
        bool f = 0,ff = 0;
        while(r <= 4*n && p[r].r < p[i].l){
            num[p[r].id]--;
            if(num[p[r].id] == 1) num2--;
            else num0++;
            r++;
        }
        while(j <= 2*n && p[j].l == p[i].l){
            if(mp[p[j].id]) f = 1;
            mp[p[j].id] = 1;
            num[p[j].id]++;
            if(num[p[j].id] == 1) num0--,ff = 1;
            else num2++,_num2++;
            j++;
        }
        if(num0 == 0){
            ans = (ans + qpow(2,num2)) % mod;
            if(!f){
            	if(!ff) ans = (ans - qpow(2,num2 - _num2) + mod) % mod;//减去该点不为左端点的选法
			}
        }
        i = j - 1;
    }
    cout << ans << endl;
}
```

