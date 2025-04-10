### [Blossom](https://codeforces.com/contest/2084/problem/E)

* 给一个缺元素的排列 $a$ 
* 求其所有可能的排列的子段的MEX之和
* $1\leq n\leq 5000$

### 思路

* 由于其可以枚举子段，因此我们将所有子段提取出来，假如为 $(i,j)$ ，其中缺少的项数为 $c_1$
* 假如 $(i,j)$ 在某个排列下MEX至少可以取到 $k$ ，称为 $(i,j,k)$ ，其对应的排列数为 $num_{i,j,k}$ ，那么该题的答案可以表示为 $\sum num_{i,j,k}$
* 为了取到 $k$ ，中间缺少的数的数量假设为 $c_2$ ，序列中原本就有的范围假设为 $(L,R)$ ，那么就能得到 $num_{i,j,k}=C_{c_1}^{c_2}A_{c_2}^{c_2}A_{al-c_2}^{al-c_2}$ 其中 $al$ 代表总的缺少的数量
* 并且要求 $i\leq L\leq R\leq j$
* 假设枚举 $k$ ，那么我们发现 $num_{i,j,k}$ 只受 $c_2$ 影响，并且 $0\leq c_2 \leq n$ ，可以直接 $n^2$ 遍历
* 那么我们只需要取消掉非法的 $num_{i,j,k}$ 即可，由于区间拓展最多也是 $n^2$ 因此总复杂度为 $O(n^2)$

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;
#define int long long

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;
const int mod = 1e9 + 7;
const int N = 5e3 + 10;
ll A[N],_A[N];
ll qpow(ll x,ll y){
    ll res = 1;
    while(y){
        if(y&1) res = (res*x) % mod;
        x = (x*x) % mod;
        y >>= 1;
    }
    return res;
}
void pre(){
    A[0] = _A[0] = 1;
    for(int i = 1;i <= N - 10;i++){
        A[i] = i * A[i - 1] % mod;
        _A[i] = qpow(A[i],mod - 2);
    }
}
ll C(ll n,ll m){
    if(m > n) return 0;
    return (A[n]*_A[m]%mod)*_A[n-m]%mod;
}

int _num[N];

int cnum(int x,int y){
    if(x > y) swap(x,y);
    return _num[y] - _num[x - 1];
}

void test(){
    int n;cin >> n;
    vector<int> ve(n + 1);
    vector<int> pos(n + 1,0);
    int sumep = 0;
    for(int i = 1;i <= n;i++){
        cin >> ve[i];
        if(ve[i] != -1){
            pos[ve[i]] = i;
        }else{
            sumep++;
        }
        _num[i] = sumep;
    }
    vector<int> al(n + 1);
    for(int i = 1;i <= n;i++){
        int num = 0;
        for(int j = i;j <= n;j++){
            if(ve[j] == -1) num++;
            al[num]++;
        }
    }
    vector<vector<bool>> f(n + 1,vector<bool>(n + 1,0));
    int L = -1,R = -1;
    ll ans = 0;
    int ned = 0;
    for(int i = 0;i < n;i++){
        if(pos[i]){
            if(L == -1){
                L = R = pos[i];
                for(int j = 1;j < L;j++){
                    int num = 0;
                    for(int k = j;k < L;k++){
                        f[j][k] = f[k][j] = 1;
                        if(ve[k] == -1) num++;
                        al[num]--;
                    }
                }
                
                for(int j = R + 1;j <= n;j++){
                    int num = 0;
                    for(int k = j;k <= n;k++){
                        f[j][k] = f[k][j] = 1;
                        if(ve[k] == -1) num++;
                        al[num]--;
                    }
                }
            }else{
                if(pos[i] > R){
                    for(int j = R;j < pos[i];j++){
                        for(int k = 1;k <= n;k++){
                        	if(L != j || k <= j){
	                        	if(!f[j][k]){
	                                f[j][k] = f[k][j] = 1;
	                                al[cnum(j,k)]--;
	                            }
							}
                        }
                    }
                    R = pos[i];
                }else if(pos[i] < L){
                    for(int j = L;j > pos[i];j--){
                        for(int k = 1;k <= n;k++){
                        	if(j != R || k >= j){
	                        	if(!f[j][k]){
	                                f[j][k] = f[k][j] = 1;
	                                al[cnum(j,k)]--;
	                            }
							}
                        }
                    }
                    L = pos[i];
                }
            }
        }else{
            ned++;
        }
        for(int i = ned;i <= n;i++){
            ans += ((al[i] * A[sumep - ned] % mod) * A[ned] % mod) * C(i,ned) % mod;
            ans %= mod;
        }

    }
    cout << ans << '\n';
}

signed main(){
    IOS
    pre();
    int t = 1;cin >> t;
    while(t--){
        test();
    }
}
```

