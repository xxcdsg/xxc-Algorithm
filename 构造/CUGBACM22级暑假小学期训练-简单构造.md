# [CUGBACM22级暑假小学期训练-简单构造](https://vjudge.net/contest/564951)

~~这Typora打到9000多字就开始卡了，难绷~~

## A - Gardener and the Capybaras (hard version)

## 题意：给一个字符串，将它分成三个字符串，让中间的字典序最小或者最大

## 思路：如果让它小，那么a是最小的，因此如果s[1]为a，那么指定它为中间的字符串即可

## 如果让它大，就尽量大，取最多即可，即如果s[1]为b，那么让中间的字符串取到n-2即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
void test(){
	string s;cin >> s;
	if(s[1] == 'a'){
		cout << s[0] << ' ' << s[1] << ' ';
		int n = s.size();
		for(int i = 2;i < n;i++)
			cout << s[i];
		cout << endl;
	}else if(s[1] == 'b'){
		cout << s[0] << ' ';
		int n = s.size();
		for(int i = 1;i < n - 1;i++)
			cout << s[i];
		cout << ' ';
		cout << s[n - 1] << endl;
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

## B - XOR = Average

~~做过，忘了，那怎么办~~

## 题意：构造n个数使得$a_1\oplus a_2\oplus a_3 ... a_n = \frac{a_1+a_2+a_3+...a_n}{n}$

## 思路：如果n为奇数，那么相同的数异或就为本身

## 如果为偶数，那么我们就想一个$x+y=2z，x\oplus y=z$的组合，易得有$x=1,y=3,z=2$，因此只要取一个1与一个3，然后都是2即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
void test(){
	int n;cin >> n;
	if(n % 2 == 1){
		for(int i = 1;i <= n;i++)
			cout << 1 << ' ';
	}
	else{
		cout << 1 << ' ' << 3 << ' ';
		for(int i = 3;i <= n;i++){
			cout << 2 << ' ';
		}
	}
	cout << endl;
}
#undef int
int main(){
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## C - LuoTianyi and the Table

~~也做过~~

## 题意： 填n * m个数到n * m的矩阵中去，让它所有点的二维前缀最大值减最小值的和最大

## 最大化$\sum_{i=1}^{n}{\sum}_{j=1}^{m}(max-min)$

## 思路：尽量让最大的和最小的相减，因此有四种放法，最大与最小与次小的两种，与最小与最大与次大的两种，枚举即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e6;
int b[N];
void test(){
	int t;cin >> t;
	while(t--){
		int n,m;cin >> n >> m;
		for(int i = 1;i <= n*m;i++)
			cin >> b[i];
		sort(b + 1,b + 1 + n*m);
		int ma = max(n,m),mi = min(n,m);
		cout << (ma - 1)*mi*(b[n*m]-b[1]) + (mi - 1)*max(b[n*m] - b[2],b[n*m - 1] - b[1]) << endl;
	}
}
#undef int
int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## D - Playing with GCD

## 题意：$b_i=gcd(p_i,p_{i+1})$，给出b序列，问p序列是否存在

## 思路：$b_i=gcd(p_i,p_{i+1})$，$b_{i+1}=gcd(p_{i+1},p_{i+2})$，$b_{i+2}=gcd(p_{i+2},p_{i+3})$,也就是$p_{i+1}=k_1b_i,p_{i+2}=k_2b_{i+2}$，因此有$b_{i+1}=gcd(k_1b_i,k_2b_{i+2})$，因此不可能出现$b_{i+1}\%gcd(b_i,b_{i+2})!=0$

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e5 + 10;
int a[N];
int gcd(int x,int y){
	if(y == 0) return x;
	else return gcd(y,x%y);
}
void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
	}
	bool f = 1;
	for(int i = 2;i <= n - 1;i++)
		if(a[i] % gcd(a[i - 1],a[i + 1]) != 0)
			f = 0;
	if(f)
		cout << "YES" << endl;
	else
		cout << "NO" << endl;
}
#undef int
int main(){
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## E - Elemental Decompress

## 题意：$a_i=max(p_i,q_i)$，给出a序列，问能否构造出p与q序列

## 思路：如果出现三次，那么肯定不可能

## 如果出现两次，那么这两次分开摆放

## 如果出现一次，可以是自己对自己

## 如果没出现，那么要向出现两次的对应

## 为了更好对应，从大到小枚举，那么后面枚举到的数肯定能和前面的对应上

## 如果没法对应，什么也不可能

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int num[N];
pair<int,int> a[N];
int p[N],q[N];
void test(){
	int n;cin >> n;
	queue<pair<int,int>> qu;
	for(int i = 1;i <= n;i++)
		num[i] = 0;
	for(int i = 1;i <= n;i++){
		cin >> a[i].first;a[i].second = i;
		num[a[i].first]++;
	}
	sort(a + 1,a + 1 + n);
	bool f = 1;
	int left = 0;
	for(int i = n;i >= 1;i--){
		if(num[i] >= 3)
			f = 0;
		else if(num[i] == 0)
			left -= 2;
		else if(num[i] == 2)
			left += 2;
		if(left < 0)
			f = 0;
	}
	if(f){
		cout << "Yes" << endl;
		int j = n;
		for(int i = n;i >= 1;i--){
			if(a[j].first == i){//放
				if(a[j - 1].first != i)
					p[a[j].second] = q[a[j].second] = i;
				else{
					qu.push({1,a[j].second});
					p[a[j].second] = i;
					j--;
					qu.push({2,a[j].second});
					q[a[j].second] = i;
				}
				j--;
			}else{//填
				pair<int,int> aim_1 = qu.front();
				qu.pop();
				pair<int,int> aim_2 = qu.front();
				qu.pop();
				q[aim_1.second] = i;
				p[aim_2.second] = i;
			}
		}
		for(int i = 1;i <= n;i++)
			cout << p[i] << ' ';
		cout << endl;
		for(int i = 1;i <= n;i++)
			cout << q[i] << ' ';
		cout << endl;
	}
	else{
		cout << "No" << endl;
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

## F - Vladik and fractions

## 题意：$\frac{2}{n}=\frac{1}{x}+\frac{1}{y}+\frac{1}{z}$，寻找x，y，z

## 思路：简化问题，令x=n，那么找$\frac{1}{n}=\frac{1}{x}+\frac{1}{y}$，易得$x=n+1,y=n(n+1)$，那么只要$n!=1$就可以

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
void test(){
	int n;cin >> n;
	if(n == 1)
		cout << -1 << endl;
	else
		cout << n << ' ' << n + 1 << ' ' << n*n + n << endl;
}
#undef int
int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## G - Permutation Operations

## 题意：操作n次，每次选一个数$a_i$，然后让所有序号大于等于$a_i$的数加$i$，使得序列变成一个递增序列

## 思路：让后面的数变大i，那么找对应的i，让它的后面都加i，那么后面的所有数都肯定大于那个数

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int a[N];
int aim[N];
void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		aim[a[i]] = i;
	}
	for(int i = 1;i <= n;i++){
		if(aim[i] == n)
			cout << 1 << ' ';
		else
			cout << aim[i] + 1 << ' ';
	}
	cout << endl;
}
#undef int
int main(){
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## H - Smaller

## 题意：一次操作可以给s字符串接上k个字符串x，或者给t字符串接上k个字符串x，每次操作后询问能否重新排序s与t使得s<t

## 一开始s与t均为"a"

## 思路：由于可以重新排序s与t，因此只要记录对应字母的数量即可

## 让s最小，让t最大

## 如果t存在比a大的字母，那么它一定可以比s大

## 否则，看s是否有比a大的字母，那么它一定比全a大

## 否则比较它们两个的a的数量

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int num[2][27];
bool check(){
	for(int i = 1;i < 26;i++)
		if(num[1][i])
			return 1;
	for(int i = 1;i < 26;i++)
		if(num[0][i])
			return 0;
	if(num[0][0] < num[1][0])
		return 1;
	return 0;
}
void test(){
	int q;cin >> q;
	for(int i = 0;i < 26;i++){
		num[0][i] = num[1][i] = 0;
	}
	num[0][0] = num[1][0] = 1;
	for(int i = 1;i <= q;i++){
		int op,k;cin >> op >> k;
		string s;cin >> s;
		int n = s.size();
		for(int j = 0;j < n;j++)		
			num[op -  1][s[j] - 'a'] += k;
		if(!check()){
			cout << "NO" << endl;
		}else
			cout << "YES" << endl;
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

## I - Search in Parallel

~~经典~~

## 题意： 两个机器人，搜索一个球需要时间分别为$s_1,s_2$，每个机器人必须按照所给的列表搜索，对每个球来说都要从头开始，给出所有球需要的搜索次数，构造两个列表使得总搜索时间最短

## 思路：贪心，让最多次搜索的最先搜索，记录两个机器人需要的时间，取小的放入最多搜索的球，直到所有球被放入即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int r[N];
queue<int> qu1,qu2;
pair<int,int> pa[N];
void test(){
	int n,s1,s2;cin >> n >> s1 >> s2;
	for(int i = 1;i <= n;i++){
		cin >> pa[i].first;
		pa[i].second = i;
	}
	sort(pa + 1,pa + 1 + n);
	int l = s1,r = s2;
	for(int i = n;i >= 1;i--){
		if(l < r){
			qu1.push(pa[i].second);
			l += s1;
		}else{
			qu2.push(pa[i].second);
			r += s2;
		}
	}
	cout << qu1.size() << ' ';
	while(!qu1.empty()){
		cout << qu1.front() << ' ';
		qu1.pop();
	}
	cout << endl;
	cout << qu2.size() << ' ';
	while(!qu2.empty()){
		cout << qu2.front() << ' ';
		qu2.pop();
	}
	cout << endl;
}
#undef int
int main(){
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## J - Flipper

## 题意：给一个排列，选l,r，让中间的子字符串翻转，让前缀与后缀交换，问字典序最大为多少

## 思路：分类讨论，如果能让n到开头的话，那么就肯定取这类方法

## 如果n不在开头和结尾的话，那么它想到开头只能是后缀变前缀，因此r确定，枚举l即可

## 如果n在结尾，那么它有两种方法到开头，翻转或枚举l

## 如果n在开头，那么就让n-1到开头，用上面的方法再做一遍即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e3 + 10;
int p[N];
int ans[N];
int ne[N];
int n;
void turn(int l,int r){
	int ptop = 0;
	for(int i = r + 1;i <= n;i++)
		ne[++ptop] = p[i];
	for(int i = r;i >= l;i--)
		ne[++ptop] = p[i];
	for(int i = 1;i < l;i++)
		ne[++ptop] = p[i];
}
bool bj(){
	for(int i = 1;i <= n;i++)
		if(ne[i] > ans[i])
			return 1;
		else if(ne[i] < ans[i])
			return 0;
	return 0;
}
void change(){
	for(int i = 1;i <= n;i++)
		ans[i] = ne[i];
}
void test(){
	cin >> n;
	int aim_n,aim_nn;
	for(int i = 1;i <= n;i++){
		cin >> p[i];
		if(p[i] == n)
			aim_n = i;
		else if(p[i] == n - 1)
			aim_nn = i;
 	}
	if(aim_n == 1 && n != 1){
		turn(1,aim_nn);
		change();
		turn(aim_nn,aim_nn);
		if(bj())
			change();
		for(int l = 1;l <= aim_nn - 1;l++){
			turn(l,aim_nn - 1);
			if(bj())
				change();
		}
	}
	else{
		turn(1,aim_n);
		change();
		turn(aim_n,aim_n);
		if(bj())
			change();
		for(int l = 1;l <= aim_n - 1;l++){
			turn(l,aim_n - 1);
			if(bj())
				change();
		}
	}
	for(int i = 1;i <= n;i++)
		cout << ans[i] << ' ';
	cout << endl;
}
#undef int long long
int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## K - Li Hua and Chess

## 题意：n*m的棋盘，棋子在上面的一点，棋子可以横着，竖着，斜着走，可以询问3次到点(x,y)需要的步数，求棋子位置

## 思路：返回的为x，y的差值的最大值，因此先询问（1，1），得到最大坐标-1，假设它为x，或者y，询问(x,1)，如果得到的数小说明假设成功，否则它的反例成功，再问(1,x)即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e3 + 10;
void test(){
	int n,m,x;cin >> n >> m;
	cout << '?' << ' ' << 1 << ' ' << 1 << endl;
	cin >> x;
	x++;
	if(x > n){
		cout << '?' << ' ' << 1 << ' ' << x << endl;
		int y;cin >> y;
		y++;
		cout << '!' << ' ' << y << ' ' << x << endl;
	}else{
		cout << '?' << ' ' << x << ' ' << 1 << endl;
		int y;cin >> y;
		y++;
		if(y == x){
			cout << '?' << ' ' << 1 << ' ' << x << endl;
			cin >> y;
			y++;
			cout << '!' << ' ' << y << ' ' << x << endl;
		}else
			cout << '!' << ' ' << x << ' ' << y << endl;
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

## L - Umka and a Long Flight

## 题意：给一个边长分别为F[n+1]与F[n]的矩形（F[n]代表第n个斐波那契数），然后涂去其中的一个点，问能否将这个矩形分解成n+1个以斐波那契数为边的正方形

## 思路：$F[n+1]f[n]-f[n]^2=f[n]f[n-1]$，因此问题就变成了一个递归问题，在切正方形时，这个点只能在一边的空白处，如果两者均不在就说明不成立

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e3 + 10;
int fb[N];
void init(){
	fb[0] = fb[1] = 1;
	for(int i = 2;i <= 44;i++)
		fb[i] = fb[i - 1] + fb[i - 2];
}
bool f(int n,int x,int y){
	if(n == 1)
		return 1;
		if(x > fb[n])
			return f(n-1,y,x - fb[n]);
		else if(x <= fb[n - 1])
			return f(n-1,y,x);
		else
			return 0;
	}
void test(){
	int n,x,y;cin >> n >> x >> y;
	if(f(n,y,x))
		cout << "YES" << endl;
	else
		cout << "NO" << endl;
}
#undef int
int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	init();
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## M - Shinju and the Lost Permutation

## 题意：$b_i=max_{1\leq x\leq i}p[x]$，$c_i=dif(b),p向左循环移动i$，给出c序列，问p是否存在

## 思路：当p为1，说明一开始就是最大的，然后移动，每次最多+1，但是可以任意最多掉到2

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e5 + 10;
int c[N];
void test(){
	int n;cin >> n;
	bool f = 1;
	int aim = -1;
	for(int i = 1;i <= n;i++){
		cin >> c[i];
		if(c[i] == 1){
			if(aim == -1)
				aim = i;
			else
				f = 0;
		}
	}
	int no = 1;
	for(int i = aim + 1;i <= n;i++){
		if(c[i] > no + 1)
			f = 0;
		no = c[i];
	}
	for(int i = 1;i < aim;i++){
		if(c[i] > no + 1)
			f = 0;
		no = c[i];
	}
	if(f)
		cout << "YES" << endl;
	else
		cout << "NO" << endl;
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

## N - Restore the Permutation

## 题意：$b_i=max(p_{2i-1},p_{2i})$，p为一个排列，问能否成立，其字典序最小为多少

## 思路：如果从前往后放小的，那么有可能缺少小的给$b_i$，因此反过来从后往前放大的，如果大的放在后面，每次尽量取大的，那么给前面的就越小，同时如果没有能够给$b_i$的数，那只能说明这个排列不成立，因为每次选最大就最大化了前面的可选择范围

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int b[N];
int p[N*2];
bool use[N];
set<int> se;
void test(){
	int n;cin >> n;
	bool f = 1;
	se.clear();
	for(int i = 1;i <= n;i++){
		use[i] = 0;
		se.insert(i);
	}
	for(int i = 1;i <= n / 2;i++){
		cin >> b[i];
		p[i*2] = b[i];
		
		if(use[b[i]])
			f = 0;
		use[b[i]] = 1;
		if(f)
			se.erase(b[i]);
	}
	for(int i = n/2;i >= 1 && f;i--){
		auto pp = se.lower_bound(b[i]);
		if(pp == se.begin())
			f = 0;
		else{
			pp--;
			p[i*2 - 1] = *pp;
			se.erase(pp);
		}
	}
	if(f)
		for(int i = 1;i <= n;i++)
			cout << p[i] << ' ';
	else
		cout << -1;
	cout << endl;
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

## O - Maximal GCD

## 题意：构造k个严格递增的数，使得它们的和为n，并且最大公约数最大

## 思路：最大公约数为gc的k个严格递增的数为$gc,2gc,3gc...$,它们的和为$\frac{k(k+1)gc}{2}$，因此gc必须小于$\frac{2n}{k(k+1)}$，并且gc还必须是n的一个因子，因此开方来求满足以上条件的最大因子即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e5 + 10;
void test(){
	int n,k;cin >> n >> k;
	//if(k == 1)
		//cout << n << endl;
	if(k > 1e6 || k*(k+1)/2 > n)
		cout << -1 << endl;
	else{
		int gc = n / (k*(k + 1) / 2);
		int tem = sqrt(n);
		int ma = 1;
		for(int i = 1;i <= tem;i++)
			if(n % i == 0){
				if(i <= gc)
					ma = max(ma,i);
				if(n/i <= gc)
					ma = max(n/i,ma);
			}
		for(int i = 1;i <= k - 1;i++){
			n -= ma*i;
			cout << ma*i << ' ';
		}
		cout << n << ' ';
		cout << endl;
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

