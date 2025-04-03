## [A - ST 表](https://vjudge.net.cn/problem/洛谷-P3865)

```cpp
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
const int MAXM = 2e6 + 10;
inline int read()
{
	int x=0,f=1;char ch=getchar();
	while (ch<'0'||ch>'9'){if (ch=='-') f=-1;ch=getchar();}
	while (ch>='0'&&ch<='9'){x=x*10+ch-48;ch=getchar();}
	return x*f;
}
inline void write(int x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}

int a[MAXN],b[MAXN][20];
int logn[MAXN] = {0};
void log_2(int n)
{
	for(int i = 2;i <= n;i++)
	 logn[i] = logn[i/2] + 1;
}
void build_ST(int n)//建表
{
	log_2(n);
	for(int i = 1;i <= n;i++)
		b[i][0] = a[i];//区间大小为1就是本身
	for(int j = 1;(1 << j) <= n;j++)//区间大小
		for(int i = 1;i <= n - (1 << j) + 1;i++)//枚举起点
			b[i][j] = max(b[i][j - 1],b[i + (1 << (j - 1))][j - 1]);
}
int find(int l,int r)
{
	int aim = r - l + 1;
	int t = logn[aim];
	return max(b[l][t],b[r-(1<<t)+1][t]);
}
int main()
{
	int n = read(),m = read();
	for(int i = 1;i <= n;i++)
	a[i] = read();
	build_ST(n);
	for(int i = 1;i <= m;i++)
	{
		int l = read(),r = read();
		write(find(l,r));
		putchar('\n');
	}
}
```

## [B - 数列区间最大值](https://vjudge.net.cn/problem/LibreOJ-10119)

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

const int N = 1e5 + 10,MAX_lg = 21;
int n;
int a[N];
class ST{
public:
	int st[N][MAX_lg];
	int to[N][MAX_lg];
	void build_sum(){
		for(int i = 1;i <= n;i++) st[i][0] = a[i],to[i][0] = i + 1;
		to[n][0] = n;
		for(int i = n;i >= 1;i--){
			for(int j = 1;j < MAX_lg;j++){
				to[i][j] = to[to[i][j - 1]][j - 1];
				st[i][j] = st[i][j - 1] + st[to[i][j - 1]][j - 1];
			}
		}
	}
	void build_ma(){
		for(int i = 1;i <= n;i++) st[i][0] = a[i],to[i][0] = i + 1;
		to[n][0] = n;
		for(int i = n;i >= 1;i--){
			for(int j = 1;j < MAX_lg;j++){
				to[i][j] = to[to[i][j - 1]][j - 1];
				st[i][j] = max(st[i][j - 1],st[to[i][j - 1]][j - 1]);
			}
		}
	}
	int query(int l,int r){
		int ans = 0;
		for(int i = MAX_lg - 1;i >= 0;i--){
			if(to[l][i] <= r + 1){
				ans = max(ans,st[l][i]);
				l = to[l][i];
			}
		}
		
		return ans;
	}
}st;

void test(){
	int m;cin >> n >> m;
	for(int i = 1;i <= n;i++) cin >> a[i];
	st.build_ma();
	while(m--){
		int x,y;cin >> x >> y;
		cout << st.query(x,y) << '\n';
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

## [C - Cornfields](https://vjudge.net.cn/problem/OpenJ_Bailian-2019)

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
    int n,b,k;cin >> n >> b >> k;
    vector<vector<int>> ma(n + 1,vector<int>(n + 1,0));
    vector<vector<int>> mi(n + 1,vector<int>(n + 1,inf));
    vector<vector<int>> a(n + 1,vector<int>(n + 1));
    for(int i = 1;i <= n;i++)
        for(int j = 1;j <= n;j++){
            cin >> a[i][j];
            mi[i][j] = ma[i][j] = a[i][j];
        }
    for(int i = 1;i < b;i++){
        for(int j = n;j >= 1;j--){
            for(int k = n;k >= 1;k--){
                ma[j][k] = max({ma[j - 1][k],ma[j][k - 1],a[j][k],a[max(j - i,0)][max(k - i,0)]});
                mi[j][k] = min({mi[j - 1][k],mi[j][k - 1],a[j][k],a[max(j - i,0)][max(k - i,0)]});
            }
        }
    }
    while(k--){
        int i,j;
        cin >> i >> j;
        cout << ma[i + b - 1][j + b - 1] - mi[i + b - 1][j + b - 1] << '\n';
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

## [D - Check Corners](https://vjudge.net.cn/problem/HDU-2888)

```cpp
#include<iostream>
#include<cmath>
#include<stdio.h>
#include<algorithm>
using namespace std;
const int N = 301;
int ma[N][N][9][9];

int n,m;

void build_st(){
    int a = (int)(log(n)/log(2)),b = (int)(log(m)/log(2));
    for(int i = 0;i <= a;i++)
    for(int j = 0;j <= b;j++){
        if(i==0&&j==0) continue;
        for(int _i = 1;_i - 1 + (1 << i) <= n;_i++)
        for(int _j = 1;_j - 1 + (1 << j) <= m;_j++){
            if(i)
                ma[_i][_j][i][j] = max(ma[_i][_j][i - 1][j],ma[_i + (1 << (i - 1))][_j][i - 1][j]);
            else
                ma[_i][_j][i][j] = max(ma[_i][_j][i][j - 1],ma[_i][_j + (1 << (j - 1))][i][j - 1]);
        }
    }
}

int query(int x1,int y1,int x2,int y2){
    int a = (int)(log(x2 - x1 + 1)/log(2)),b = (int)(log(y2 - y1 + 1)/log(2));
    int maa = ma[x1][y1][a][b];
    maa = max(ma[x2 - (1 << a) + 1][y1][a][b],maa);
    maa = max(ma[x2 - (1 << a) + 1][y2 - (1 << b) + 1][a][b],maa);
    maa = max(ma[x1][y2 - (1 << b) + 1][a][b],maa);
    return maa;
}

int main()
{
    while(~scanf("%d%d",&n,&m)){
        for(int i = 1;i <= n;i++)
        for(int j = 1;j <= m;j++)
            scanf("%d",&ma[i][j][0][0]);
        build_st();
        int q;
        scanf("%d",&q);
        while(q--){
            int x1,y1,x2,y2;
            scanf("%d%d%d%d",&x1,&y1,&x2,&y2);
            int maa = query(x1,y1,x2,y2);
            printf("%d ",maa);
            if(ma[x1][y1][0][0] == maa || ma[x1][y2][0][0] == maa ||ma[x2][y1][0][0] == maa ||ma[x2][y2][0][0] == maa)
                puts("yes");
            else
                puts("no");
        }
    }
}
```

