### 杂谈：懒得想F和G2了，F太长了，看了G1过的人多一点就做它了，G2也没想出来，之后看看题解

# [Codeforces Round 878 (Div. 3)](https://codeforces.com/contest/1840)

## A. Cipher Shifer

## 题意：初始字符序列，在每个字符后加任意不同于本身的字符，再在最后加上那个字符，现给出处理后的序列，问初始序列为什么

## 思路：水，从头或者尾开始找一样的就可以了

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
void test(){
	int n;cin >> n;
	string s;cin >> s;
	string ans = "";
	ans = ans + s[0];
	char no = s[0];
	for(int i = 1;i < n;i++){
		if(s[i] == no){
			i++;
			if(i < n)
				ans = ans + s[i];
			no = s[i];
		}
	}
	cout << ans << endl;
}
#undef int
int main(){
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## B. Binary Cafe

## 题意：用$2^i$来配数，不能超过$n$，$0\leq i\leq k-1$

## 思路：如果没有n的限制，那么根据二进制可以配出$0到2^k-1$的所有数，所以只要比较$2^k-1$与$n$的大小，取小的加1即可，由于$k$可以到1000，因此不能直接用二进制，用循环即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
void test(){
	int n,k;cin >> n >> k;
	int no = 1;
	while(no - 1 <= n && k--) no *= 2;
	if(no - 1 <= n) cout << no << endl;
	else cout << n + 1 << endl;
}
#undef int
int main(){
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## C. Ski Resort

## 题意：给出一个序列，取至少连续的k个数，其中的最大值不超过q，问有几种取法

## 思路：那些超过q的数将连续的断开，对于完整的一段，长度如果为n，那么至少连续取k个数的方法数就为$(n-k+1)+(n-k)+(n-k-1)+...+1$，也就是$[(n-k+2)*(n-k+1)]/2$

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int a[N];
void test(){
	int n,k,q;cin >> n >> k >> q;
	int no = 0;
	int ans = 0;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		if(a[i] <= q)
			no++;
		else{
			if(no - k + 1 > 0)
				ans += max((int)0,(no - k + 2)*(no - k + 1)/2);
			no = 0;
		}
	}
	if(no - k + 1 > 0)
		ans += max((int)0,(no - k + 2)*(no - k + 1)/2);
	cout << ans << endl;
}
#undef int
int main(){
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## D. Wooden Toy Festival

## 题意：取三个数，让所有数取的$|x-y|$的最大值最小

## 思路：取三个数也就是将所有数分成三分，每个数对应一个区域，这个区域肯定是连续的，因此先sort

## 然后枚举最后的区间，然后用二分找中间区间的最适合位置

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int a[N];
void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
		cin >> a[i];
	sort(a + 1,a + 1 + n);
	int mi = 1e9 + 10;
	if(n <= 3)
		cout << 0 << endl;
	else{
		int r = 2;
		for(;r <= n - 1;r++){
			int rr = (a[n] - a[r + 1] + 1) / 2;
			int x = (a[r] - a[2] + 1) / 2;
			int _l = 1,_r = r;
			while(_l <= _r){
				int _mid = (_l + _r) / 2;
				int _ll = (a[_mid - 1] - a[1] + 1) / 2;
				int _rr = (a[r] - a[_mid] + 1) / 2;
				x = min(max(_ll,_rr),x);
				if(_ll > _rr)
					_r = _mid - 1;
				else
					_l = _mid + 1;
			}
			x = max(x,rr);
			mi = min(x,mi);
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

## E. Character Blocking

## 题意：给出两个长度相同的字符串，有三个操作，1交换：交换指定两个位置的字符，2阻塞：将上下同一位置的字符串阻塞t时间，3询问：询问两个字符串未阻塞位置是否完全相同

## 思路：记录完全相同的数量(不考虑阻塞)与阻塞的数量，维护，询问的时候看两个数量相加是否等于长度即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int nu[N];
void test(){
	string s[2];cin >> s[0] >> s[1];
	queue<pair<int,int>> qu;
	int t,q;cin >> t >> q;
	int no = 0;
	int num = 0,block = 0;//一样的数量,阻塞的数量
	int n = s[0].size();
	for(int i = 1;i <= n;i++)
		nu[i] = 0;
	s[0] = ' ' + s[0];
	s[1] = ' ' + s[1];
	for(int i = 1;i <= n;i++)
		if(s[0][i] == s[1][i])
			num++;
	while(q--){
		no++;
		if(!qu.empty() && qu.front().first + t <= no){
			nu[qu.front().second]--;
			if(!nu[qu.front().second]){
				block--;
				if(s[0][qu.front().second] == s[1][qu.front().second])
					num++;	
			}
			qu.pop();
		}
		int op;cin >> op;
		if(op == 1){
			int pos;cin >> pos;
			if(s[0][pos] == s[1][pos])
				num--;
			qu.push({no,pos});
			if(!nu[pos])
				block++;
			nu[pos]++;
		}else if(op == 2){
			int aim1,pos1,aim2,pos2;
			cin >> aim1 >> pos1 >> aim2 >> pos2;
			aim1--;
			aim2--;
			if(s[aim1][pos1] == s[1 - aim1][pos1])
				num--;
			if(s[aim2][pos2] == s[1 - aim2][pos2])
				num--;
			swap(s[aim1][pos1],s[aim2][pos2]);
			if(s[aim1][pos1] == s[1 - aim1][pos1])
				num++;
			if(s[aim2][pos2] == s[1 - aim2][pos2])
				num++;
		}else{
			if(num + block == n)
				cout << "Yes" << endl;
			else
				cout << "No" << endl;
		}
	}
}
#undef int
int main()
{
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## G1. In Search of Truth (Easy Version)

## 题意：一个环，每个节点的数不同，你可以顺时针走k，或者逆时针走k，在2023次内得出环的长度n$(1\leq n\leq 1e6)$

## 思路：如果能配出$1到1e6$的所有数，那么只要看那个数相同，那么长度就是那个数

## 999次得出$0到999$，然后一直加1000即可，那么即可覆盖$1到1e6$

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 1e6 + 10;
int turn_out[N];
void test(){
	for(int i = 1;i < N;i ++ )
		turn_out[i] = -1;
	int x;cin >> x;
	turn_out[x] = 0;
	int no = 0;
	for(int i = 1;i <= 999;i++){
		cout << '+' << ' ' << 1 << endl;
		no++;
		cin >> x;
		if(turn_out[x] != -1){
			cout << '!' << ' ' << no << endl;
			return;
		}
		turn_out[x] = no;
	}
	for(int i = 1;i <= 1000;i++){
		cout << '+' << ' ' << 1000 << endl;
		no += 1000;
		cin >> x;
		if(turn_out[x] != -1){
			cout << '!' << ' ' << no - turn_out[x] << endl;
			return;
		}
		turn_out[x] = no;
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

