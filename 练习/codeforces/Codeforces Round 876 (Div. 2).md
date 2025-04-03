## 杂谈：

### 应付期末考试，连着两三周都没正经打代码，先来6把codeforces练练手，结果直接就把第一场全写出来了（太棒了）

### 不过第二题就读假题结果用了45分钟，都和后面的题用时差不多了

## 正经的

# [Codeforces Round 876 (Div. 2)](https://codeforces.com/contest/1839)

## A. The Good Array

## 题意：构造长度为n，只由1与0构成的数列，对于每一个i来说都有:前面i个里至少有$\lceil\frac{i}{k}\rceil$个1，同理后面i个里也至少有$\lceil\frac{i}{k}\rceil$个1

## 思路：假设只有一个限制条件，很容易想到结果就为每k个放一个，那么如果要两边都满足，就要考虑最右边要不要多放一个

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
void test(){
	int n,k;cin >> n >> k;
	if(n % k == 0){
		if(k == 1)
			cout << n << endl;
		else
			cout << n / k + 1 << endl;
	}
	else if(n % k == 1)
		cout << n / k + 1 << endl;
	else
		cout << n / k + 2 << endl;
}
#undef int long long
int main()
{
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## B. Lamps

~~别读假题，别读假题~~

## 题意：灯有3种状态：开启，关闭，损坏。灯有两个值，一个为a[i]，一个为b[i]，开灯会得到b[i]的价值，假设开启的灯的数量为x，那么所有$a[i]\leq x$的灯都会损坏，然后损坏的灯不计入开启，问最大能得到多少的价值

## 思路：首先，为了选最多的灯，我们应该从a[i]较小的灯开始选起，然后选到a[i]-1时，我们发现只剩下a[i]的灯亮着，因此对于a[i]的灯来说最多选a[i]个，取前a[i]即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
pair<int,int> pa[N];
int n;
bool cmp(pair<int,int> a,pair<int,int> b)
{
	if(a.first == b.first)
	return a.second > b.second;
	return a.first < b.first;
}
void test(){
	cin >> n;
	for(int i = 1;i <= n;i++)
		cin >> pa[i].first >> pa[i].second;
	sort(pa + 1,pa + 1 + n,cmp);
	int ans = 0;
	int x = 0;
	for(int i = 1;i <= n;i++){
		if(pa[i].first == pa[i - 1].first){
			if(pa[i].first != x){
				ans += pa[i].second;
				x++;
			}
		}else{
			x = 1;
			ans += pa[i].second;
		}
	}
	cout << ans << endl;
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

## C. Insert Zero and Invert Prefix

## 题意：一次操作可以插入一个0，让前面的数从1变0，从0变1，问如何得出所给的数列

## 思路：一次操作不影响后面的数，因此我们可以从后往前处理，然后以0为尾，有几个1就放几个，然后用最后的0来翻转它们，以此类推。因此如果最后的数不是0就说明这个数列不可行

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
bool b[N];
void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
		cin >> b[i];
	if(b[n] == 1)
		cout << "NO" << endl;
	else{
		cout << "YES" << endl;
		int i = n;
		while(i > 0){
			int num = 0;
			while(i - 1 > 0 && b[i - 1] == 1){
				cout << 0 << ' ';
				num++;
				i--;
			}
			i--;
			cout << num << ' ';
		}
		cout << endl;
	}
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

## D. Ball Sorting

## 题意：两种操作：放0球与将0球旁边的球放在任意位置，问给出1~n个0球，将所给数列从1~n排列（排除0球），操作2的数量最小值为多少

## 思路：一个0球可以处理它所在的一片连续区域的错误摆放的球，那么题意就可转换为最多给出1~n个错误区间，问最少的数量

## dp思路：第一个参数代表区间数量，第二个代表非错误最大的数（只有比这个数大的才能做为正确区间，否则一定为错误区间），存最小的数量

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 501;
int dp[N][N];//空多少连续位，最后一个数是多少,空的最小数量
int a[N];
int n;
void init(){
	for(int i = 0;i <= n;i++)
	for(int j = 0;j <= n;j++)
	dp[i][j] = n;
}
void test(){
	cin >> n;
	init();
	for(int i = 1;i <= n;i++)
		cin >> a[i];
	dp[0][0] = 0;
	for(int i = 1;i <= n;i++){//一位一位更新
		for(int j = 0;j <= i;j++)//空连续位
		{
			for(int k = 0;k < a[i];k++)//可以连上的
			{
				if(dp[j][k] == -1)//不存在
					continue;
				//分两种情况，连或者不连，连的情况还分直接连与间隔连
				if(a[i - 1] == k)//如果前一个数就是k，那么可以直接连
					dp[j][a[i]] = min(dp[j][k],dp[j][a[i]]);
				else//否则让空的加一
					dp[j + 1][a[i]] = min(dp[j][k],dp[j + 1][a[i]]);
				//不连
				dp[j][k]++;
			}
			for(int k = a[i] + 1;k <= n;k++)//不能连上的
			{
				if(dp[j][k] == -1)
					continue;
				dp[j][k]++;
			}
		}
	}
	int mi = n;
	if(dp[0][a[n]] == 0)
		mi = 0;
	for(int i = 1;i <= n;i++){
		if(dp[i][a[n]] != -1)
			mi = min(mi,dp[i][a[n]]);
		for(int j = 0;j <= n;j++){
			if(j != a[n] && dp[i - 1][j] != -1)
				mi = min(dp[i - 1][j],mi);
		}
		cout << mi << ' ';
	}
	cout << endl;
}
#undef int
int main()
{
	int t;cin >> t;
	while(t--){
		test();
	}
}
```

## E. Decreasing Game

~~一个范围搞一小时~~

## 题意：博弈，第一个人取i，其中$a[i]\neq0$，第二个人取j，其中$a[j]\neq0$，然后让它们均减去$min(a[i],a[j])$，问谁胜利，并给出必胜办法交互

## 思路：如果这些数可以被分为等和子集，那么第一个人取a子集中的数，那么第二个人取b子集中的数就可以一直保证这两个子集和相等，从而第二个人胜利，否则就为第一个人胜利

## 分解子集考虑使用dp，参数表示具体和为多少，存到这个值所使用的a[i]的i，那么划分等子集时就可以从sum/2反向递推即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 301;
int a[N];
pair<int,int> pre[N*N + 10];
bool type[N];
void test(){
	int n;cin >> n;
	int sum = 0;
	for(int i = 1;i <= N*N - 1;i++)
		pre[i].first = -1;
	pre[0].first = 0;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		for(int j = N*N;j >= a[i];j--){
			if(pre[j - a[i]].first != -1 && pre[j - a[i]].second != i && pre[j].first == -1){
				pre[j].first = j - a[i];
				pre[j].second = i;
			}
		}
		sum += a[i];
	}
	if(sum % 2 == 1 || pre[sum / 2].first == -1){
		cout << "First" << endl;
		int no = 1;
		while(1){
			while(a[no] == 0)
				no++;
			cout << no << endl;
			int xx;cin >> xx;
			if(xx == 0)
				break;
			int mi = min(a[xx],a[no]);
			a[xx] -= mi;
			a[no] -= mi;
		}
	}
	else{
		cout << "Second" << endl;
		int no = sum / 2;
		int all = 0;
		while(no != 0){
			type[pre[no].second] = 1;
			all += a[pre[no].second];
			no = pre[no].first;
		}
		if(all != sum / 2)
			while(1);
		while(1){
			int x;cin >> x;
			if(x == 0)
				break;
			int i;
			for(i = 1;i <= n;i++){
				if(a[i] && i != x && type[i] == !type[x]){
					cout << i << endl;
					int mi = min(a[i],a[x]);
					a[i] -= mi;
					a[x] -= mi;
					break;
				}
			}
			if(i == n + 1)
				return;
		}
	}
}
#undef int
int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

