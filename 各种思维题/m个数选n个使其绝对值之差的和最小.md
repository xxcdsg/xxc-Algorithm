### [D. Very Different Array](https://codeforces.com/contest/1921/problem/D)

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

void test(){
    int n,m;cin >> n >> m;
    vector<ll> ve1(n + 1),ve2(m + 1);
    for(int i = 1;i <= n;i++) cin >> ve1[i];
    for(int i = 1;i <= m;i++) cin >> ve2[i];
    ll ans = 0;
    sort(ve1.begin() + 1,ve1.end());
    sort(ve2.begin() + 1,ve2.end());
    ll maans = 0;
    for(int i = 1;i <= n;i++) ans += ve2[m - i + 1] - ve1[i];
    maans = ans;
    for(int i = 1;i <= n;i++){
        ans += 2 * ve1[n - i + 1];
        ans -= ve2[m - n + i];
        ans -= ve2[i];
        maans = max(maans,ans);
    }
    cout << maans << '\n';
}

int main(){
    IOS
    int t = 1;cin >> t;
    while(t--){
        test();
    }
}
```

