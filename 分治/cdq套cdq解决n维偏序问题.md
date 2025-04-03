~~cdq大法好~~

~~还是没怎么搞懂~~

### 先把题目放着：

二维偏序：[P1908 逆序对](https://www.luogu.com.cn/problem/P1908)

三维偏序：[P3810 【模板】三维偏序（陌上花开）](https://www.luogu.com.cn/problem/P3810)

四维偏序~~罪恶之源~~：[stars](http://acm.hdu.edu.cn/showproblem.php?pid=5126)

## 先从简单的开始，二维偏序cdq做法

### 先对一维排序，然后分成两半，然后递归处理左右两边，然后左右两边内部就处理好了，那么再处理两边的，因为两边处理完后都是对第二维排完序的，因此要整体排序的话就用并归排序即可，如果是在前半部分取的数，那么答案就要更新已经加入到序列中的后半部分的数量，这样就把所有的都处理到了

* 逆序对

```cpp
#include<bits/stdc++.h>
using namespace std;

#define ll long long

const int N = 5e5 + 10;
ll a[N],b[N],ans = 0;

void cdq(int l,int r){
	if(l == r) return;
	int mid = (l + r) >> 1;
	cdq(l,mid);
	cdq(mid + 1,r);
	ll l1 = l,l2 = mid + 1,len = l;
	while(l1 <= mid && l2 <= r){
		if(a[l1] <= a[l2]) b[len++] = a[l1++],ans += l2 - mid - 1;
		else b[len++] = a[l2++];
	}
	while(l1 <= mid) b[len++] = a[l1++],ans += l2 - mid - 1;
	while(l2 <= r) b[len++] = a[l2++];
	for(int i = l;i <= r;i++) a[i] = b[i];
}

int main(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
		cin >> a[i];
	cdq(1,n);
	cout << ans << endl;
}
```

## 然后三维

### 先去重，记录数量，然后对第一维排序

### 现在开始分治，取中间，递归处理两边(那么就认为两边内部的已经完全处理好了，只要处理两边对应的即可)

```cpp
cdq1(int l,int r){
    if(l == r) return;
    int mid = (l + r) >> 1;
    cdq1(l,mid);
    cdq1(mid + 1,r);
```

### 然后归并排序，因为分治了左边与右边，因此就认为它们第二维已经排完序了

### 我们要记录它的一维是在左边还是右边，这样在排完二维时才能不失去第一维的数据，也就是说我们只要右边对应左边的数量，因此要记录它是在那边

```cpp
	int l1 = l,l2 = mid + 1,len = l;
	while(l1 <= mid && l2 <= r){
		if(p[l1].x[1] <= p[l2].x[1]) tem[len] = p[l1++] , tem[len++].op = 0;
		else tem[len] = p[l2++] , tem[len++].op = 1;
	}
	while(l1 <= mid) tem[len] = p[l1++] , tem[len++].op = 0;
	while(l2 <= r) tem[len] = p[l2++] , tem[len++].op = 1;
```

### 在第二层递归之前，我们不能破坏这个已经对第二维排好的序列，因此我们要新建一个序列来处理下面的分治问题，来记录第三维排好的数据

```cpp
	for(int i = l;i <= r;i++) p[i] = tem[i],tem[i].id = i;
	cdq2(l,r);
}
```

### 然后第二层cdq2已经有了第一维是左边还是右边的数据，并且第二维已经被排好了，那么我们就来写cdq2（要放在cdq1前面，因为它要调用）

### 我们继续递归，分两边，然后得到两段对第三维已经排好的序列，并且内部已经被处理好了

```cpp
void cdq2(int l,int r){//二维已经排好并且一维已经记录
	if(l == r) return;
	int mid = (l + r) >> 1;
	cdq2(l,mid);
	cdq2(mid + 1,r);
```

### 然后归并并且记录数据，被记录的数据应该为一维在左边的，并且现在在前面（二维），然后得到数据的为一维在右边，并且现在在后面的数据

```cpp
	int l1 = l,l2 = mid + 1,len = l,sum = 0;
	while(l1 <= mid && l2 <= r){
		if(tem[l1].x[2] <= tem[l2].x[2]){
			if(tem[l1].op == 0) sum += tem[l1].cnt;//二维在前面，并且一维也在前面，那么它就被记录了(贡献)
			p2[len++] = tem[l1++];
		}else{
			if(tem[l2].op == 1) p[tem[l2].id].val += sum;//一维在后面，并且二维也在后面，那么它就得到了数据
			p2[len++] = tem[l2++];
		}
	}
	while(l1 <= mid){
		if(tem[l1].op == 0) sum += tem[l1].cnt;
		p2[len++] = tem[l1++];
	}
	while(l2 <= r){
		if(tem[l2].op == 1) p[tem[l2].id].val += sum;
		p2[len++] = tem[l2++];
	}
```

### 最后把排好的放回tem即可

```cpp
	for(int i = l;i <= r;i++) tem[i] = p2[i];
}
```

### AC代码

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 2e5 + 10;

int n,k,ptop,cnt[N],ans[N];

struct point{
	int x[3],id,cnt,op,val;
	bool operator <(const point &other){
		if(x[0] == other.x[0]){
			if(x[1] == other.x[1])
				return x[2] < other.x[2];
			return x[1] < other.x[1];
		}
		return x[0] < other.x[0];
	}
	bool operator ==(const point &other){
		if(x[0] != other.x[0] || x[1] != other.x[1] || x[2] != other.x[2])
			return 0;
		return 1;
	}
	void read(){
		cin >> x[0] >> x[1] >> x[2];
	}
}p[N],p2[N],tem[N];

void cdq2(int l,int r){//二维已经排好并且一维已经记录
	if(l == r) return;
	int mid = (l + r) >> 1;
	cdq2(l,mid);
	cdq2(mid + 1,r);
	int l1 = l,l2 = mid + 1,len = l,sum = 0;
	while(l1 <= mid && l2 <= r){
		if(tem[l1].x[2] <= tem[l2].x[2]){
			if(tem[l1].op == 0) sum += tem[l1].cnt;//二维在前面，并且一维也在前面，
			p2[len++] = tem[l1++];
		}else{
			if(tem[l2].op == 1) p[tem[l2].id].val += sum;
			p2[len++] = tem[l2++];
		}
	}
	while(l1 <= mid){
		if(tem[l1].op == 0) sum += tem[l1].cnt;
		p2[len++] = tem[l1++];
	}
	while(l2 <= r){
		if(tem[l2].op == 1) p[tem[l2].id].val += sum;
		p2[len++] = tem[l2++];
	}
	for(int i = l;i <= r;i++) tem[i] = p2[i];
}

void cdq1(int l,int r){//一维已经排好，现在排二维，记录一维是在左边还是右边op
	if(l == r) return;
	int mid = (l + r) >> 1;
	cdq1(l,mid);
	cdq1(mid + 1,r);
	int l1 = l,l2 = mid + 1,len = l;
	while(l1 <= mid && l2 <= r){
		if(p[l1].x[1] <= p[l2].x[1]) tem[len] = p[l1++] , tem[len++].op = 0;
		else tem[len] = p[l2++] , tem[len++].op = 1;
	}
	while(l1 <= mid) tem[len] = p[l1++] , tem[len++].op = 0;
	while(l2 <= r) tem[len] = p[l2++] , tem[len++].op = 1;
	for(int i = l;i <= r;i++) p[i] = tem[i],tem[i].id = i;
	cdq2(l,r);
}

int main(){
	cin >> n >> k;
	for(int i = 1;i <= n;i++)
		p[i].read();
	sort(p + 1,p + 1 + n);
	ptop = 0;
	for(int i = 1;i <= n;i++){
		if(p[i] == p[i - 1]) tem[ptop].cnt++;
		else tem[++ptop] = p[i],tem[ptop].cnt = 1;
	}
	for(int i = 1;i <= ptop;i++)
		p[i] = tem[i];
	cdq1(1,ptop);
	for(int i = 1;i <= ptop;i++){
		ans[p[i].cnt - 1 + p[i].val] += p[i].cnt;
	}
	for(int i = 0;i < n;i++)
		cout << ans[i] << endl;
}
```

## 然后按着这思路继续四维

