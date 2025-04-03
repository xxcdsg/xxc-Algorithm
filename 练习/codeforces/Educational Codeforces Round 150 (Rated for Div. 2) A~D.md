### E之后也应该能做，之后再补

# [Educational Codeforces Round 150 (Rated for Div. 2)](https://codeforces.com/contest/1841)

## A. Game with Board

## 题意：~~（什么鬼题）~~,取至少两个相同的数全加起来，谁不能取谁赢，一开始有n个1

## 思路：果然n很大，那么分出n-2与2，取n-2的和，那么剩下两个1，就能保证胜利，如果，在4之内，无论怎么取都会失败

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
void test(){
	int n;cin >> n;
	if(n <= 4)
		cout << "Bob" << endl;
	else
		cout << "Alice" << endl;
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

## B. Keep it Beautiful

## 题意：让前面任意个放到后面，然后保证数列不递减，一个一个接数，如果接入能保证操作后依旧不递减，就接入

## 思路：正常来说，如果这个数比最后的数大，那么可以接上，如果有数放到后面，那么这个数还要比最前面的数小

## 如果这个数虽然不比最后的数大，但比第一个数小，那么如果还没让前面的数向后放的情况下，可以放所有数都向后放

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;
int a[N];
void test(){
	int q;cin >> q;
	if(q == 1){
		cout << 1 << endl;
		return;
	}
	bool f = 0;
	pair<int,int> pa;
	cin >> pa.first >> pa.second;
	if(pa.second < pa.first)
		f = 1;
	string ans = "11";
	for(int i = 3;i <= q;i++){
		int x;cin >> x;
		if(!f){
			if(x >= pa.second){
				pa.second = x;
				ans += '1';
			}else if(x <= pa.first){
				pa.second = x;
				ans += '1';
				f = 1;
			}else
				ans += '0';
		}else{
			if(x >= pa.second && x <= pa.first){
				pa.second = x;
				ans += '1';
			}else
				ans += '0';
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

## C. Ranom Numbers

## 题意：ABCDE分别代表1，10，100，1000，10000，如果右边有比这个位置元素大的元素，那么这个元素取负，否则取正，问在只改变一次任意元素的情况下的最大取值

## 思路：无论怎么变，右边的数不变，那么考虑左边的，左边有一些数已经取了负数，记录那些数，然后不计入它们的数量，仅记录不知道是正的还是负的元素的数量，如果枚举每个位的5种取法，同时考虑右边的最大元素，两个取最大，这样就得到了最大元素，对左边不确定的元素取正负，就得到左边的数，然后加上这个位置的数与右边确定的数即可

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e5 + 10;;
int r[N];
int rma[N];
int num[5];
void init(int n){
	for(int i = 0;i < 5;i++)
		num[i] = 0;
	for(int i = 0;i <= n + 1;i++){
		r[i] = rma[i] = 0;
	}
}
void test(){
	string s;cin >> s;
	int n = s.size();
	init(n);
	s = ' ' + s;
	int ma = 0;
	for(int i = n;i >= 1;i--){
		int x = s[i] - 'A';
		if(x >= ma){
			ma = x;
			r[i] = r[i + 1] + pow(10,x);
		}else{
			r[i] = r[i + 1] - pow(10,x);
		}
		rma[i] = ma;
	}
	ma = -1e9;
	int ll = 0;
	for(int i = 1;i <= n;i++){
		for(int j = 0;j < 5;j++){
			int maa = j;
			int tem = 0;
			if(maa < rma[i + 1]){
				tem -= pow(10,maa);
				maa = rma[i + 1];
			}else{
				tem += pow(10,maa);
			}
			for(int jj = 0;jj < maa;jj++)
				tem -= num[jj] * pow(10,jj);
			for(int jj = maa;jj < 5;jj++)
				tem += num[jj] * pow(10,jj);
			ma = max(ma,tem + ll + r[i + 1]);
		}
		int x = s[i] - 'A';
		num[x]++;
		for(int j = 0;j < x;j++){
			ll -= num[j]*pow(10,j);
			num[j] = 0;
		}
	}
	cout << ma << endl;
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

## D. Pairs of Segments

## 题意：两个线段相交并且不与其他线段相交可以留下，问最少去掉的数量

## 思路：对右边界排序，将影响降到最小，从小到大枚举右边界，直到找到相交的，留下的数量+2，然后限制左边界最小到这个右边界，继续枚举即可

~~(这怎么有2000分)~~

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N = 2e3 + 10;
pair<int,int> pa[N];
bool cmp(pair<int,int> a,pair<int,int> b){
	return a.second < b.second;
}
void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
		cin >> pa[i].first >> pa[i].second;
	sort(pa + 1,pa + 1 + n,cmp);
	int r = -1;
	int no = 2;
	int _l = pa[1].first,_r = pa[1].second;
	int left = 0;
	while(no <= n){
		if(pa[no].first <= r);
		else if(pa[no].first <= _r){
			left+=2;
			r = pa[no].second;
			no++;
			while(pa[no].first <= r && no <= n)
				no++;
			if(no > n)
				break;
			_l = pa[no].first;
			_r = pa[no].second;
		}else{
			_r = pa[no].second;
		}
		no++;
	}
	cout << n - left << endl;
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

