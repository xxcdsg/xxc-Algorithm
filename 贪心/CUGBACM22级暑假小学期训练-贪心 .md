# [CUGBACM22级暑假小学期训练-贪心](https://vjudge.net/contest/565189)

## A - 种树

## 题意：给一个环，取m个节点，不能取相邻节点，令所取节点的权值之和最大化

## 思路:~~dp~~，用到了最大流中建立反向边的思想使得这个贪心能够“反悔”

## 用堆存贪心选择，我们当我们选取一个最优选择时，同时会增加与减少一些选择，那些新的选择再次加入，继续贪心。

## 当我们贪心选取一个节点时，那么标记它的相邻节点，但是这样选不一定是最优的选法

## 如果我们只选取那个节点的左节点，那么相对于该节点选取时更劣(因为假设了右节点不取，所以取左节点的影响范围更大)

## 同理只取右节点也比选中间差

## 那么只有一种情况可取：两边节点均取

## 因此当选取一个节点时，那么就还要考虑不取这个节点而取两边的取法，即让这个节点变成一个包括两边不包括本身的节点，更新这个节点的左右端点，更新左右端点与这个点相连

## 那么如何证明这样取是最大的呢？

## 那么只要证明任意连续两个取最大比任何取法都大即可

## 由于我们的取法可以取消点取两边，因此所有可能的取法我们均能考虑，因此续两个取最大一定是最大取法，因此可得贪心解即为最优解

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
struct pos{
	int x,id,l,r;
	bool operator <(const pos &other)const{
		return other.x > x;
	}
}p[N];
bool vis[N];
priority_queue<pos> du;//存点
void test(){
	int n,m;cin >> n >> m;
	for(int i = 1;i <= n;i++){
		p[i].l = i - 1;
		p[i].r = i + 1;
		if(i == 1)
			p[i].l = n;
		if(i == n)
			p[i].r = 1;
		cin >> p[i].x;
		p[i].id = i;
		du.push(p[i]);
	}
	if(m > n / 2)
		cout << "Error!" << endl;
	else{
		int ans = 0;
		while(m--){
			while(vis[du.top().id])
				du.pop();
			int no = du.top().id;
			ans += p[no].x;
			du.pop();
			vis[p[no].l] = 1;
			vis[p[no].r] = 1;
			p[no].x = p[p[no].l].x + p[p[no].r].x - p[no].x;
			p[no].l = p[p[no].l].l;
			p[no].r = p[p[no].r].r;
			p[p[no].l].r = no;
			p[p[no].r].l = no;
			du.push(p[no]);
		}
		cout << ans << endl;
	}
}
#undef int
int main()
{
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## B - 国王游戏

## 题意：每个人左手和右手均有一个数，每个人获得的钱为前面所有人的左手数乘积除以那个人右手的数，问其中最大钱数的最小值为多少

## 思路：考虑两个人的排序，假设前面左手的乘积为$x$，那么如果是1、2这样排，那么钱数分别为$x/r_1$、$(x*l_1)/r_2$，如果是2、1的话就是$x/r_2$、$(x*l_2)/r_1$

## 同除去$x$有$\frac{1}{r_1}，\frac{l_1}{r_2}，\frac{1}{r_2}，\frac{l_2}{r_1}$，那么只要比较$\frac{l_1}{r_2}，\frac{l_2}{r_1}$即可，要想不交换，就要保证$l_1r_1\leq l_2r_2$，因此以此排序计算即可

## 不过这个数据范围较大，需要用高精度来计算

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1100;
pair<int,int> a[N];
vector<int> tem,sum,ma;
void next()//高精度进位
{
	int i = 0;
	while(i < sum.size())
	{
		if(sum[i] >= 10)
			if(i == sum.size() - 1)
			{
				sum.push_back(sum[i] / 10);
				sum[i] %= 10;
			}
			else
			{
				sum[i + 1] += sum[i] / 10;
				sum[i] %= 10;
			}
		i++;
	}
}
void  mult(int i)//高精度乘法
{
	for(int j = 0;j < sum.size();j++)
	sum[j] *= i;
	next();
}
void divi(int i)//高精度除法
{
	int tt = 0;//记录数据
	for(int j = tem.size() - 1;j >= 0;j--)//从前往后
	{
		tt *= 10;
		tt += tem[j];
		tem[j] = tt / i;
		tt %= i;
	}
	for(int j = tem.size() - 1;j >= 1;j--)//去0
	if(tem[j] == 0)
	tem.pop_back();
	else
	break;
	if(ma.size() > tem.size());//选最大值
		else if(ma.size() == tem.size())
		for(int i = ma.size() - 1;i >= 0;i--)
		{
			if(tem[i] > ma[i])
			{
				ma = tem;break;
			}
			else if(ma[i] < tem[i])
			break;
		}
		else
		ma = tem;
}
bool cmp(pair<int,int> aa,pair<int,int> bb){
	return aa.first*aa.second < bb.second*bb.first;
}
void test(){
	int n;cin >> n;
	int x = 1,te;
	cin >> x >> te;
	sum.push_back(x);
	for(int i =1;i <= n;i++){
		cin >> a[i].first >> a[i].second;
	}
	sort(a + 1,a + 1 + n,cmp);
	for(int i = 1;i <= n;i++){
		tem = sum;
		divi(a[i].second);
		mult(a[i].first);
	}
	int nn = ma.size();
	for(int i = nn - 1;i >= 0;i--){
		cout << ma[i];
	}
	if(nn == 0)
		cout << 0 << endl;
}
#undef int
int main()
{
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## C - Demonstration

## 题意：~~又臭又长~~，k天里，反对派可以每天向政府申请任意地块$i$（之前没选过的），如果这个地块不是最差（离得最远的，最大的）地块，那么政府就会尝试拒绝，即在这个地块上进行活动，而活动需要$a_i$的钱，如果钱不够就接受，问能选到的最好区块。

## 思路：贪心，先申请k-1大的区块，然后从小到大看能否申请到，如果这个区块为k-1大的区块，那么加上第k大的区块看是否大于b，否则加上这个区块的$a_i$看看是否大于b，取第一个成立的

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e5 + 10;
int a[N];
pair<int,int> b[N];
bool use[N];
void test(){
	int n,k,_b;cin >> n >> k >> _b;
	int sum = 0;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		sum += a[i];
		b[i].first = a[i];
		b[i].second = i;
	}
	if(_b > sum - a[n])
		cout << n << endl;
	else if(k >= n - 1){
		bool f = 0;
		cout << 1 << endl;
	}else{
		sort(b + 1,b + n,[&]
		(pair<int,int> x,pair<int,int> y){return x.first > y.first;});
		sum = 0;
		for(int i = 1;i <= k - 1;i++){
			sum += b[i].first;
			use[b[i].second] = 1;
		}
		int left = b[k].first;
		bool f = 0;
		for(int i = 1;i <= n - 1;i++){
			if(use[i]){
				if(left + sum > _b){
					f = 1;
					cout << i << endl;
					break;
				}
			}else{
				if(sum + a[i] > _b){
					f = 1;
					cout << i << endl;
					break;
				}
			}
		}
		if(!f)
			cout << n << endl;
	}
}
#undef int
int main(){
	test();
}
```

## D - Winnie-the-Pooh and honey

## 题意：最多吃三次，一次吃k，没到k不吃，问剩下多少

## 思路：水，取$max(a[i]-3k,a[i]\ \%\ k)$即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 111;
int a[N];
void test(){
	freopen("input.txt","r",stdin);
	freopen("output.txt","w",stdout);
	int n,k;cin >> n >> k;
	int ans = 0;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		ans += max(a[i] % k,a[i] - k*3);
	}
	cout << ans << endl;
}
#undef int
int main(){
	test();
}
```

## E - 合并果子 / [USACO06NOV] Fence Repair G

## 题意：合并两堆果子需要$a+b$的体力，问将所有果子合并的最小需要体力

## 思路：很容易想到贪心做法为每次选最小的两堆果子合并，但是为什么这种做法是之前的呢？

## 假设有一颗该做法的树，较大的数深度一定较浅，这样才能乘最小次数

## 因此有$a<b$时必定有$h(a)\leq h(b)$，否则交换一定更小

## 因此取最小的两个就是取位于树最底层的两个

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e4 + 10;
priority_queue<int,vector<int>,greater<int>> qu;
void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++){
		int x;cin >> x;
		qu.push(x);
	}
	int ans = 0;
	while(qu.size() != 1){
		int ne = qu.top();
		qu.pop();
		ne += qu.top();
		qu.pop();
		qu.push(ne);
		ans += ne;
	}
	cout << ans << endl;
}
#undef int
int main(){
	test();
}
```

## F - Set or Decrease

## 题意：一次操作可以用一个数取代一个数或者让一个数-1，问让和小于k的最小的操作次数

## 思路：首先如果本来本来和就小于k那么就为0

## 然后考虑变$0到n-1$个数为最小值需要让最小值为多少，得到最小的操作次数

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int a[N];
void test(){
	int n,k;cin >> n >> k;
	int sum = 0;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		sum += a[i];
	}
	int mi;
	sort(a + 1,a + 1 + n);
	if(sum <= k)
		cout << 0 << endl;
	else{
		mi = n - 1 + max((int)0,a[1] - k/n);//全变
		sum = 0;
		for(int i = 2;i <= n;i++){
			sum += a[i];
			int left = n - i + 1;
			mi = min(mi,left - 1 + max((int)0,(int)(a[1] - floor((k - sum*1.0) / left))));
		}
		cout << mi << endl;
	}
}
#undef int
int main(){
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## G - Till I Collapse

~~2500分，感觉有点难，有时间再补~~

## H - Unfair Poll

## 题意：行$1,2,3,4,...,n-1,n,n-1,...4,3,2,1,2,3,4......$，列从1到m，一共取k次，问最小取的位置的数量与最大的，还要指定位置的

## 思路：周期，特殊处理行1与行n

## 周期为$T=(2n-2)*m,n!=1$，对正常的来说用k除去周期再乘2，然后考虑从左边与从右边是否有来加1

## 对于1与n来说，一周期只加1，然后考虑最后是否有到那里即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 101;
int sum[N][N];
void test(){
	int n,m,k,x,y;cin >> n >> m >> k >> x >> y;
	if(n == 1){
		int mi = k / m,ma = k / m + (k%m == 0?0:1);
		int no = k / m + (k % m >= y?1:0);
		cout << ma << ' ' << mi << ' ' << no << endl;
		return;
	}
	int T = (n*2 - 2)*m;//周期
	int ma = 0,mi = 1e18;
	for(int i = 1;i <= n;i++)
	for(int j = 1;j <= m;j++){
		if(i == 1){
			sum[i][j] = k/T;
			int l = j;
			if(k % T >= l)
				sum[i][j]++;
			ma = max(ma,sum[i][j]);
			mi = min(mi,sum[i][j]);
		}else if(i == n){
			sum[i][j] = k/T;
			int r = (n - 1)*m + j;
			if(k % T >= r)
				sum[i][j]++;
			ma = max(ma,sum[i][j]);
			mi = min(mi,sum[i][j]);
		}else{
			sum[i][j] = (k/T)*2;
			int l = (i - 1) * m + j;
			int r = (n + n - i - 1) * m + j;
			if(k % T >= l)
				sum[i][j]++;
			if(k % T >= r)
				sum[i][j]++;
			ma = max(ma,sum[i][j]);
			mi = min(mi,sum[i][j]);
		}
	}
	cout << ma << ' ' << mi << ' ' << sum[x][y] << endl;
}
#undef int
int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## I - Allowance

## 题意：给出$N,(1 <= N <= 20)$ 种类型钱，每种钱的面值与数量分别为$v_i与b_i$，每个月要拿出至少k元来支付，问最多取几周

## 思路：如果面值大于k，那么一张对应一周，否则就要凑k元

## ~~不理解~~，从大到小取，如果正好取到k那么就采用该方法直到不足以使用该方法，否则在从小到大多取直到超过k，就得到了一个近似k的取法

```c++
#include<iostream>//
#include<algorithm>
using namespace std;
#define int long long
const int N = 25;
pair<int,int> pa[N];
int use[N];
void test(){
	int n,c;cin >> n >> c;
	for(int i = 1;i <= n;i++)
		cin >> pa[i].first >> pa[i].second;
	sort(pa + 1,pa + 1 + n);
	int ans = 0;
	for(int i = n;i >= 1;i--){
		if(pa[i].first < c)
			break;
		ans += pa[i].second;
		pa[i].second = 0;
		n--;
	}
	while(1){
		int cc = c;
		for(int i = n;i >= 1 && c > 0;i--){
			use[i] = min(cc/pa[i].first,pa[i].second);
			cc -= use[i]*pa[i].first;
		}
		bool f = 0;
		if(cc != 0){
			f = 0;
			for(int i = 1;i <= n;i++){
				while(pa[i].second > use[i]){
					use[i]++;
					cc -= pa[i].first;
					if(cc <= 0){
						f = 1;
						break;
					}
				}
				if(f)
					break;
			}
		}
		if(!f && cc != 0)
			break;
		int mi = 0x7f7f7f7f;
		for(int i = 1;i <= n;i++){
			if(use[i])
				mi = min(mi,pa[i].second / use[i]);
		}
		ans += mi;
		for(int i = 1;i <= n;i++)
			pa[i].second -= use[i] * mi;
	}
	cout << ans << endl;
}
#undef int
int main(){
	int t = 1;//cin >> t;
	while(t--)
		test();
}
```

## J - Radar Installation

## 题意：在x轴上放最少的 范围一定的雷达覆盖所有的岛屿

## 以x轴为圆心化最少的大小一定的圆覆盖所有点

## 思路：将点根据圆的半径化为范围，根据左端点排序，贪心取（能和之前的取到并集就取），直到取完

```c++
#include<iostream>//
#include<algorithm>
#include<math.h>
using namespace std;
#define int long long
const int N = 1e3 + 10;
pair<double,double> pa[N];
int use[N];
void test(){
	int n,d;
	cin >> n >> d;
	int ti = 0;
	while(n != 0){
		ti++;
		bool f = 1;
		for(int i = 1;i <= n;i++){
			int x,y;
			cin >> x >> y;
			if(y > d){
				f = 0;
				continue;
			}
			double miss = sqrt((double)d*d-y*y);
			pa[i].first = x - miss;
			pa[i].second = x + miss;
		}
		sort(pa + 1,pa + 1 + n);
		int num = 0;
		if(f){
			for(int i = 1;i <= n;i++){
				num++;
				double r = pa[i].second;
				while(i <= n && pa[i].first <= r){
					i++;
					r = min(pa[i].second,r);
				}
				i--;
			}
		}
		cout << "Case " << ti << ": ";
		if(!f)
			cout << -1 << endl;
		else
			cout << num << endl;
		cin >> n >> d;
	}
}
#undef int
int main(){
	int t = 1;//cin >> t;
	while(t--)
		test();
}
```

## K - Circle Through Three Points

## 三角形外心公式为

> ```c++
> x=((y2-y1)*(y3*y3-y1*y1+x3*x3-x1*x1)-(y3-y1)*(y2*y2-y1*y1+x2*x2-x1*x1))/(2.0*((x3-x1)*(y2-y1)-(x2-x1)*(y3-y1)));
> y=((x2-x1)*(x3*x3-x1*x1+y3*y3-y1*y1)-(x3-x1)*(x2*x2-x1*x1+y2*y2-y1*y1))/(2.0*((y3-y1)*(x2-x1)-(y2-y1)*(x3-x1)));
> ```

```c++
#include<iostream>
#include<algorithm>
#include<math.h>
#include<stdio.h>
using namespace std;
double _abs(double x){
	return x<0?-x:x;
}
double dis(double x1,double y1,double x2,double y2){
	return sqrt(pow(x1-x2,2)+pow(y1-y2,2));
}
void test(){
	double  x1,x2,x3,y1,y2,y3; 
	while(cin >> x1 >> y1 >> x2 >> y2 >> x3 >> y3){
	    double x = 0; 
	    double y = 0; 
	    x=((y2-y1)*(y3*y3-y1*y1+x3*x3-x1*x1)-(y3-y1)*(y2*y2-y1*y1+x2*x2-x1*x1))/(2.0*((x3-x1)*(y2-y1)-(x2-x1)*(y3-y1)));
	    y=((x2-x1)*(x3*x3-x1*x1+y3*y3-y1*y1)-(x3-x1)*(x2*x2-x1*x1+y2*y2-y1*y1))/(2.0*((y3-y1)*(x2-x1)-(y2-y1)*(x3-x1)));
	    double r = dis(x,y,x1,y1);
		printf("(x %c %.3lf)^2 + (y %c %.3lf)^2 = %.3lf^2\n",(-x < 0?'-':'+'),_abs(x),
		-y < 0?'-':'+',_abs(y),r);
		double c = -r*r + x*x + y*y;
		printf("x^2 + y^2 %c %.3lfx %c %.3lfy %c %.3lf = 0\n\n",(-x < 0?'-':'+'),2*_abs(x),
		(-y < 0?'-':'+'),2*_abs(y),c < 0 ? '-':'+',_abs(c));
	}
}
int main(){
	int t = 1;//cin >> t;
	while(t--)
		test();
}
```

## L - Y2K Accounting Bug

## 题意：每个月如果亏损就亏损d，否则就盈利s，所有连续的五个月均亏损，问全年能否盈利，最大利益多少

## 思路：贪心盈利月份，假设5个月最多两个月盈利，那么就应该是$110001100011$，因此全年盈利月份数就为$ma*2+min(2,ma)$，其中ma为5个月最多盈利月份，用循环计算

```c++
#include<iostream>
using namespace std;
int main(){
	long long s,d;
	while(cin >> s >> d){
		int i;
		for(i = 1;i <= 5;i++){
			if(d*i > s*(5 - i)){
				break;
			}
		}
		i = 5 - i;
		int t = i * 2 + min(2,i);
		if(s*t > d*(12 - t))
			cout << s*t - d*(12 - t) << endl;
		else
			cout << "Deficit" << endl;
	}
}
```