## 划分树

* 按中位数分开，点内部数按原顺序排序

#### 建树

* 我们容易发现，对于一个节点来说，内部的数与排完序后对于区间的数是完全相同的，就顺序是按原顺序而非完全排序的顺序
* 那么我们很容易知道对于一个区间来说，其中位数就是节点对应区间在排完序的序列中的中位数
* 那么建树就很容易了

```cpp
const int N = 1e5 + 10;

int sorted[N],a[N];
int partition_tree[20][N],num[20][N];

void build(int dep,int l,int r){
	if(l == r) return;
	int mid = (l + r) >> 1;
	int samel = 1;
	for(int i = mid - 1;i >= l;i--,samel++)
		if(sorted[i] != sorted[mid]) break;
	for(int i = l,lpos = l,rpos = mid + 1;i <= r;i++){
		num[dep][i] = (i == l ? 0 : num[dep][i - 1]);
		if(partition_tree[dep][i] < sorted[mid] || (partition_tree[dep][i] == sorted[mid] && (samel--) > 0)){
			partition_tree[dep + 1][lpos++] = partition_tree[dep][i];
			num[dep][i]++;
		}else{
			partition_tree[dep + 1][rpos++] = partition_tree[dep][i];
		}
	}
	build(dep + 1,l,mid);
	build(dep + 1,mid + 1,r);
}
```

* 用 $num$ 来记录对于这个节点来说，跑到这个位置左节点已经分配了多少数，分别后面查询

![1674973-20190820220808519-490271564](C:\Users\Administrator\Desktop\markdown\c++\算法\数据结构\高级数据结构\1674973-20190820220808519-490271564.png)

#### 查询

* 用 $L,R$ 代表节点代表的区间
* 用 $l,r$ 代表查询区间
* 用 $k$ 代表第 $k$ 小的数
* 首先我们考虑，是进入左节点，还是右节点
* 如果 $l,r$ 内的数，进入左节点的数量大于等于 $k$ ，那就说明第 $k$ 小的数在左节点，因为右节点的数都大于左节点
* 即 $num[dep][r]-dep[dep][l-1](l=L为0)>=k$
* 那么 $L,R$ 的转移简单，就是取 $(L+R)/2=mid$ 分区间
* 那么问题就是 $l,r$ 的转移
* 我们考虑到从上面的树转移到下面，其内部顺序不变，那么我们只要知道大区间的左右端点跑到下面端点的位置即可
* 那么第一个跑到左端点的位置就是 $L+num[dep][l - 1](l=L为0)$
* 最后一个就是看区间内的数量，即 $L+num[dep][r]-1$
* 同理，跑右节点也是这样，只是减去了 $l,r$ 进入左节点的数量
*  $l,r$ 区间转移就是
* 首先都是从 $mid+1$ 为起点
* 考虑 $L,l-1$ 进入右节点的数量，即为 $l-L-num1$ ，就是区间数量总数减去进入左节点的数量
* 同理考虑进入右节点的总数，那么就是进入左节点的总数减去区间大小， $r-L-num[dep][r]$
* 两个再加入 $mid+1$ 即为新的 $l,r$

```cpp
int query(int dep,int L,int R,int l,int r,int k){
	if(L == R) return partition_tree[dep][L];
	int mid = (L + R) >> 1;
	int num1 = (L == l ? 0 : num[dep][l - 1]);
	int num2 = num[dep][r] - num1;
	if(num2 >= k) return query(dep + 1,L,mid,num1 + L,L + num[dep][r] - 1,k);
	else return query(dep + 1,mid + 1,R,l - L - num1 + mid + 1,mid + r - L - num[dep][r] + 1,k - num2);
}
```

#### [Kth number](https://acm.hdu.edu.cn/showproblem.php?pid=2665)

* 静态序列，求区间 $k$ 小值

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

const int N = 1e5 + 10;

int sorted[N],a[N];
int partition_tree[20][N],num[20][N];

void build(int dep,int l,int r){
	if(l == r) return;
	int mid = (l + r) >> 1;
	int samel = 1;
	for(int i = mid - 1;i >= l;i--,samel++)
		if(sorted[i] != sorted[mid]) break;
	for(int i = l,lpos = l,rpos = mid + 1;i <= r;i++){
		num[dep][i] = (i == l ? 0 : num[dep][i - 1]);
		if(partition_tree[dep][i] < sorted[mid] || (partition_tree[dep][i] == sorted[mid] && (samel--) > 0)){
			partition_tree[dep + 1][lpos++] = partition_tree[dep][i];
			num[dep][i]++;
		}else{
			partition_tree[dep + 1][rpos++] = partition_tree[dep][i];
		}
	}
	build(dep + 1,l,mid);
	build(dep + 1,mid + 1,r);
}

int query(int dep,int L,int R,int l,int r,int k){
	if(L == R) return partition_tree[dep][L];
	int mid = (L + R) >> 1;
	int num1 = (L == l ? 0 : num[dep][l - 1]);
	int num2 = num[dep][r] - num1;
	if(num2 >= k) return query(dep + 1,L,mid,num1 + L,L + num[dep][r] - 1,k);
	else return query(dep + 1,mid + 1,R,l - L - num1 + mid + 1,mid + r - L - num[dep][r] + 1,k - num2);
}

void test(){
	int n,m;cin >> n >> m;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		partition_tree[0][i] = sorted[i] = a[i];
	}
	sort(sorted + 1,sorted + 1 + n);
	build(0,1,n);
	for(int i = 1;i <= m;i++){
		int l,r,k;cin >> l >> r >> k;
		cout << query(0,1,n,l,r,k) << '\n';
	}
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

#### [P3157 [CQOI2011] 动态逆序对  ](https://www.luogu.com.cn/problem/P3157)