# 计算几何

### 叉乘的应用

* 三角形面积$2S=|\left[\begin{matrix}x_2-x_1&y_2-y_1\\x_3-x_1&y_3-y_1\end{matrix}\right]|$

* 叉乘，如果是正数说明第二个向量在第一个向量的顺时针方向，否则为逆时针

### 几何板子

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define db double

const double DINF = 1e18,eps = 1e-8,Pi = acos(-1);

//角度弧度转换
db R_to_D(db rad){return 180/Pi*rad;}
db D_to_R(db D){return Pi/180*D;}

struct Point{
	double x,y;
	Point(double x = 0,double y = 0):x(x),y(y){}
	void read(){
		cin >> x >> y;
	}
};

//点与向量表达相同
typedef Point Vector;

//点与向量的操作
Vector operator + (Vector A,Vector B) {return Vector(A.x + B.x,A.y + B.y);}
Vector operator - (Vector A,Vector B) {return Vector(A.x - B.x,A.y - B.y);}
Vector operator * (Vector A,double p) {return Vector(A.x*p , A.y*p);}
Vector operator / (Vector A,double p) {return Vector(A.x/p , A.y/p);}

//常引用减少数据复制量，又保证数据不更改
bool operator < (const Point& a,const Point& b){return a.x < b.x || (a.x == b.x && a.y < b.y);}

//极坐标系中的角度
//double atan2(double y,double x)
double Polar_angle(Vector A){return atan2(A.y,A.x);}

//三态函数，判断正负0
int sgn(db x){
	if(fabs(x) < eps) return 0;
	if(x < 0) return -1;
	return 1;
}

bool operator == (const Point& A,const Point& B){return !sgn(A.x-B.x) && !sgn(A.y-B.y);}

//判断double型用sgn(a-b) == 0，sgn(a-b)>0这样写

//点积
double Dot(Vector A,Vector B){return A.x * B.x + A.y * B.y;}
//叉积
double Cross(Vector A,Vector B){return A.x * B.y - B.x * A.y;}
//向量BC是否在AB的逆时针方向
bool ToLeftTest(Point A,Point B,Point C){return Cross(B - A,C - B);}
//取模
double Length(Vector A){return sqrt(Dot(A,A));}
//计算两向量夹角(弧度制)
double Angle(Vector A,Vector B){return acos(Dot(A,B)/Length(A)/Length(B));}
//计算三点，两向量的平行四边形有向面积AB,BC
double Area2(Point A,Point B,Point C){return Cross(B - A,C - A);}
//单位法向量，逆时针旋转90度,长度不能为0
Vector Normal_Vector(Vector A){
	double L = Length(A);
	return Vector(-A.y / L, A.x / L);
}
//单位向量
Vector Format(const Vector &A){
	double L = Length(A);
	return A/L;
}

//逆时针旋转θ角度
//顺时针(xcos(θ)-ysin(θ),-xsin(θ)+ycos(θ))
//弧度制
Vector Rotate(Vector A,db rad){
	return Vector(A.x * cos(rad) - A.y * sin(rad),A.x * sin(rad) + A.y * cos(rad));
}
//点A绕着p点逆时针旋转rad
//变向量，旋转，加起点
Point rotate(const Point& A,const Point& p,const db rad){
	Point v = A - p;
	return (Rotate(v,rad) + p);
}

//直线可用一个起点与方向向量表示
struct Line{
	Vector v;
	Point p;
	Line(Vector v,Point p):v(v),p(p){}
	Line(){}
	void read(){
		p.read(),v.read();
		v = v - p;
	}
	Point get_point_in_line(double t){
		return v + (p - v)*t;
	}
};

//点与直线关系C与AB关系
//1在左侧，-1在右侧
int realtion(Point A,Point B,Point C){
	int c = sgn(Cross((B - A),(C - A)));
	if(c < 0) return 1;
	else if(c > 0) return -1;
	return 0;
}

//计算两直线交点，调用前要保证Cross(v,w)!=0,即不平行
Point Get_line_intersection(Point P,Vector v,Point Q,Vector w){
	Vector u = P - Q;
	double t = Cross(w,u) / Cross(v,w);
	return P + v * t;
}
//计算点到直线的距离
double Distance_point_to_line(Point P,Point A,Point B){
	Vector v1 = B - A,v2 = P - A;
	return fabs(Cross(v1,v2) / Length(v1)); 
}

//点到线段的距离
double Distance_point_to_segment(Point P,Point A,Point B){
	if(A == B) return Length(P - A);//线段两点重合
	Vector v1 = B - A,v2 = P - A,v3 = P - B;
	if(sgn(Dot(v1,v2)) < 0) return Length(v2);
	if(sgn(Dot(v1,v3)) > 0) return Length(v3);
	return fabs(Cross(v1,v2)/Length(v1));
}

//求点在直线上的投影点
Point Get_line_projection(Point P,Point A,Point B){
	Vector v = B - A;
	return A + Format(v) * (Dot(v,P - A) / Length(v));
}

//计算点到直线的对称点
Point Symmetry_point(Point p,Point A,Point B){
	return p + (Get_line_projection(p,A,B) - p)*2;
}

//判断点是否在线段上
bool On_segment(Point P,Point A,Point B){
	return sgn(Cross(A-P,B-P)) == 0 && sgn(Dot(A-P,B-P)) < 0;
}

//快速排斥实验+跨立实验
bool Segment_proper_intersection(Point A,Point B,Point C,Point D){
	//快速排斥
	if((A.x > B.x ? A.x :B.x) < (C.x > D.x ? D.x : C.x)) return 0;
	if((A.y > B.y ? A.y :B.y) < (C.y > D.y ? D.y : C.y)) return 0;
	if((C.x > D.x ? C.x :D.x) < (A.x > B.x ? B.x : A.x)) return 0;
	if((C.y > D.y ? C.y :D.y) < (A.y > B.y ? B.y : A.y)) return 0;
	//跨立实验
	db c1 = Cross(B - A,C - A),c2 = Cross(B - A,D - A);//CD在AB的...
	db c3 = Cross(D - C,A - C),c4 = Cross(D - C,B - C);//AB在CD的...
	return sgn(c1*c2) < 0 && sgn(c3*c4) < 0;//<为严格相交，<=为端点也可以
}
//两向量是否平行
bool parallel(Vector v,Vector w){
	return sgn(Dot(v,w)) == 0;
}
//0平行，1重合，2相交
int Line_line(Line L1,Line L2){
	if(parallel(L1.v,L2.v))
	return On_segment(L1.p + L1.v,L2.p,L2.p + L2.v);
	return 2;
}

//三角形重心
Point Triangle_gravity_center(Point A,Point B,Point C){
	return (A + B + C) / 3;
}
//三角形外心
Point Triangle_Circum_center(Point A,Point B,Point C){
	db x1 = A.x,x2 = B.x,x3 = C.x,y1 = A.y,y2 = B.y,y3 = C.y;
	db x=((y2-y1)*(y3*y3-y1*y1+x3*x3-x1*x1)-(y3-y1)*(y2*y2-y1*y1+x2*x2-x1*x1))/(2.0*((x3-x1)*(y2-y1)-(x2-x1)*(y3-y1)));
	db y=((x2-x1)*(x3*x3-x1*x1+y3*y3-y1*y1)-(x3-x1)*(x2*x2-x1*x1+y2*y2-y1*y1))/(2.0*((y3-y1)*(x2-x1)-(y2-y1)*(x3-x1)));
	return Point(x,y);
}
//三角形垂心,垂心定理：外心O，重心G，垂心H共线并且OG：GH=1：2,因此2/3PO+1/3PH=PG，因此PH=3PG-2PO
Point Triangle_ortho_center(Point A,Point B,Point C){
	return Triangle_gravity_center(A,B,C)*3 - Triangle_Circum_center(A,B,C)*2;
}
//内心(ax1+bx2+cx3/a+b+c,ay1+by2+cy3/a+b+c)
Point Triangle_internal_center(Point A,Point B,Point C){
	db a = Length(B - C),b = Length(C - A),c = Length(A - B);
	db x = (a*A.x + b*B.x + c*C.x) / (a + b + c);
	db y = (a*A.y + b*B.y + c*C.y) / (a + b + c);
	return Point(x,y);
}

//判断点在三角形的位置4顶点，3边，2外，1内
int In_triangle(Point A,Point B,Point C,Point P){
	if(A == P || B == P || C == P)
		return 4;
	else if(On_segment(P,A,B) || On_segment(P,A,C) || On_segment(P,C,B))
		return 3;
	else{
		Vector v1 = B - A , v2 = C - A , v3 = P - A;
		if(sgn(Cross(v1,v3))*sgn(Cross(v1,v3)) == 1) return 2;
		v1 = A - B , v2 = C - B , v3 = P - B;
		if(sgn(Cross(v1,v3))*sgn(Cross(v1,v3)) == 1) return 2;
		return 1;
	}
}
//判断点在多边形Polygon与点P的关系0在外，1在内，2在边上
//射线法，画一条平行与x轴的射线，看交点的数量，如果是奇数说明在里面，否则在外面
//多边形Polygon第一个点与最后一个点一样
//还有二分法O（logn）的复杂度，但懒得搞
int In_polygon(const vector<Point>& Polygon,const Point& P){
	int n = Polygon.size();
	bool f = 0;
	for(int i = 1;i < n;i++){
		Point A = Polygon[i],B = Polygon[i - 1];
		if(On_segment(P,A,B)) return 2;
		//先看是否在y内,将y=P.y带入两点式算出x，然后减去P.x看是否大于0
		if(sgn(A.y - P.y)>0 != sgn(B.y - P.y)>0 && sgn(((P.y - A.y)/(A.y - B.y))*(A.x - B.x) + A.x - P.x) > 0)
			f = !f;
	}
	return f;
}
//多边形重心，用三角形面积求精度更高
Point Polygon_gravity_center(const vector<Point>& Polygon){
	Point P = Polygon[0];
	db sumarea = 0;
	Point ans(0,0);
	int n = Polygon.size();
	for(int i = 1;i < n - 1;i++){
		db area = Area2(P,Polygon[i],Polygon[i+1])/2;
		ans = ans + (P+Polygon[i]+Polygon[i+1])*area;
		sumarea += area;
	}
	return ans / sumarea / 3;
}
//凸包
int Convex_hull(Point* p,int n,Point* ch){
	sort(p,p + n);
	int m = 0;
	for(int i = 0;i < n;i++){
		while(m > 1 && Cross(ch[m - 1] - ch[m - 2],p[i] - ch[m - 2]) <= 0) m--;
		ch[m++] = p[i];
	}
	int k = m;
	for(int i = n - 2;i >= 0;i--){
		while(m > k && Cross(ch[m - 1] - ch[m - 2],p[i] - ch[m - 2]) <= 0) m--;
		ch[m++] = p[i];
	}
	if(n > 1) m--;
	return m;
}
//旋转卡壳
db Rotating_calipers(Point *p,int num){
	int op = 1;
	db ans = 0;
	for(int i = 0;i < num;i++){
		while(Cross((p[i] - p[op]) , (p[i + 1] - p[i])) < Cross((p[i] - p[op + 1]) , (p[i + 1] - p[i])))
			op = (op + 1) % num;
		ans = max(ans,max(Length(p[i] - p[op]),Length(p[i + 1] - p[op])));
	}
	return ans;
}
//判定凸多边形(看凸包点数即可)

//圆
struct Circle{
	Point p;
	db r;
	Circle(Point p = Point(),db r = 0):p(p),r(r){}
	Point get_point(double rad){//通过圆心角求坐标
		return Point(p.x + cos(rad) * r,p.y + sin(rad) * r);
	}
	void read(){
		p.read();
		cin >> r;
	}
};
//求圆与直线交点
//通过距离，勾股定理,投影算的，可能精度有点低？
int Get_line_circle_intersection(Line L,Circle C,pair<Point,Point> &pa){
	db d = Distance_point_to_line(C.p,L.p,L.p + L.v);
	if(sgn(d - C.r) > 0) return 0;//相离
	else if(sgn(d - C.r) == 0){//相切
		pa.first = Get_line_projection(C.p,L.p,L.p + L.v);
		return 1;
	}else{//相交
		Point P = Get_line_projection(C.p,L.p,L.p + L.v);
		d = sqrt(C.r*C.r - d*d);
		Vector v = Format(L.v);
		pa = {P - v*d,P + v*d};
		return 2;
	}
}
//求两圆交点，-1重合
int Get_circle_circle_intersection(Circle C1,Circle C2,pair<Point,Point> &pa){
	db d = Length(C1.p - C2.p);
	if(sgn(d) == 0){//同心
		if(sgn(C1.r - C2.r) == 0) return -1;
		else return 0;
	}
	if(sgn(d - C1.r - C2.r) > 0) return 0;//相离
	if(sgn(fabs(C1.r - C2.r) - d) > 0) return 0;//内部
	
}

```

### 点距题

#### 求平面内$2≤n≤200000$的最近点对距离

#### *1.分治*

#### 按$x$排序，分两边，算出两边的最短距离，根据这个最短距离取$[mid-mi,mid+mi]$的范围内的两边的点，然后再根据$y$排序，计算每个点与它$y$差值在$mi$之间的点

#### 那么对于每个点来说都对应一个$2mi×mi$矩形范围内的点，又由于距离最小为$mi$，因此点是有限的(最多6个)，复杂度就可控

#### 只对x排序一次即可，之后对y进行归并排序

#### 由于对y排序会打乱x的排序，因此在递归分治之前记录mid的x值

```cpp
#include<bits/stdc++.h>
using namespace std;

#define ll long long

const int N = 4e5 + 10;

int n,idx;

struct point{
	ll x,y;
}p[N],q[N],tem[N];

bool cmp_x(point a,point b){
	return a.x < b.x;
}
bool cmp_y(point a,point b){
	return a.y < b.y;
}

ll mi;

ll _pow(ll x){
	return x*x;
}

ll dis(point a,point b){
	return _pow(a.x-b.x) + _pow(a.y-b.y);
}

void _sort(int l,int r){
	int idx = 0,mid = (l + r) >> 1;
	int _l = l,_r = mid + 1;
	while(_l <= mid && _r <= r){
		if(p[_l].y < p[_r].y)
			tem[l + idx++] = p[_l++];
		else
			tem[l + idx++] = p[_r++];
	}
	while(_l <= mid)
		tem[l + idx++] = p[_l++];
	while(_r <= r)
		tem[l + idx++] = p[_r++];
	for(int i = l;i <= r;i++)
		p[i] = tem[i];
}

void cdq(int l,int r){
	if(l == r) return;
	int mid = (l + r) >> 1;
	ll pmid = p[mid].x;
	cdq(l,mid);
	cdq(mid+1,r);
	idx = 0;
	
	_sort(l,r);
	for(int i = l;i <= r;i++)
		if(_pow(p[i].x - pmid) < mi)
			q[++idx] = p[i];
	
	//sort(q + 1,q + 1 + idx,cmp_y);
	
	for(int i = 1;i <= idx;i++){
		for(int j = i + 1;j <= idx;j++){
			if(_pow(q[j].y - q[i].y) > mi)
				break;
			mi = min(mi,dis(q[i],q[j]));
		}
	}
}

int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	cin >> n;
	for(int i = 1;i <= n;i++)
		cin >> p[i].x >> p[i].y;
	mi = 1e18 + 10;
	sort(p + 1,p + n + 1,cmp_x);
	cdq(1,n);
	cout << mi << endl;
}
```

#### *2.K-D-TREE*

### 凸包

* 定义：对于确定的点集，其中的部分点构成的多边形能包含所有点，这个部分点构成的集合叫凸包

#### 1.安德鲁算法(Andrew’s Algorithm)

##### (1)将点根据x从小到大排序，如果x相同就根据y升序排列

##### (2)(建立上边)按x从小到大加入序列，如果加入后不是凸包，那么就将前面的删除

##### (3)(建立下边)反着加入即可

* 那么怎么判断加入后是否为凸包?
* 对于上边来说，它必须向下偏，也就是连边向量必须向顺时针方向旋转，利用叉乘即可判断是否向顺时针方向旋转，也就是相对于之前的向量向顺时针方向旋转，即新向量于之前的向量叉积为负数，所以只要大于0就要删掉栈顶的元素

* 例题[圈奶牛Fencing the Cows /【模板】二维凸包](https://www.luogu.com.cn/problem/P2742)

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll double
const int MAXN = 1e6;
pair<double,double> p[MAXN];
pair<double,double> st[MAXN];
ll xc(ll x1,ll y1,ll x2,ll y2){
	return x1*y2-x2*y1;
}
double s(ll x1,ll y1,ll x2,ll y2,ll x3,ll y3){
	ll _x1 = x2 - x1,_x2 = x3 - x1,_y1 = y2 - y1,_y2 = y3 - y1;
	return abs(0.5*xc(_x1,_y1,_x2,_y2));
}
double l(ll x1,ll y1,ll x2,ll y2){
	return sqrt((x1-x2)*(x1-x2)+(y1-y2)*(y1-y2));
}
bool cmp(pair<ll,ll> a,pair<ll,ll> b){
	if(a.first != b.first)
		return a.first < b.first;
	else
		return a.second < b.second;
}
int main()
{
	int n;cin >> n;
	for(int i = 0;i < n;i++)
		cin >> p[i].first >> p[i].second;
	sort(p,p+n,cmp);
	int ptop = -1;
	for(int i = 0;i < n;i++){
		if(ptop < 1){
			st[++ptop] = p[i];
		}else{
			while(ptop >= 1){
				ll t = xc(st[ptop].first-st[ptop-1].first,st[ptop].second-st[ptop-1].second,p[i].first-st[ptop-1].first,p[i].second-st[ptop-1].second);
				if(t < 0)
					ptop--;
				else
					break;
			}
			st[++ptop] = p[i];
		}
	}
	for(int i = n - 1;i >= 0;i--){
		if(ptop < 1){
			st[++ptop] = p[i];
		}else{
			while(ptop >= 1){
				ll t = xc(st[ptop].first-st[ptop-1].first,st[ptop].second-st[ptop-1].second,p[i].first-st[ptop-1].first,p[i].second-st[ptop-1].second);
				if(t < 0)
					ptop--;
				else
					break;
			}
			st[++ptop] = p[i];
		}
	}
	double ans = 0;
	for(int i = 1;i <= ptop;i++){
		ans += l(st[i].first,st[i].second,st[i - 1].first,st[i - 1].second);
	}
	printf("%.2lf",ans);
}
```

# 数论

## 康托展开与逆康托展开

* n个不同元素的全排列中, 比当前排列组合小的个数
* 求第k大的排列

```cpp
//小数的O(n^2)康托展开
int fac[13];//阶乘
void init(){
    fac[1] = 1;
    for(int i = 2;i <= 12;i++)
        fac[i] = fac[i - 1] * i;
}
int cantor(int *a,int n){//数组位置、数量
    int ans = 0;
    for(int i = 0;i < n;i++){
        int smaller = 0; //后面比它小的数
        for(int j = i + 1;j < n;j++){
            if(a[i] > a[j])
                smaller++;
        }
        ans += fac[n - i - 1] * smaller; //把小的提上来，后面全排列
    }
    return ans;
}

//较小的数的O(n^2)康托展开
#define ll long long
const int N = 1e4 + 10,p = 998244353;
ll fac[N];
void init(){
    fac[1] = 1;
    for(int i = 2;i <= N - 10;i++)
        fac[i] = (fac[i - 1] * i) % p;
}
ll cantor(int *a,int n){
    ll ans = 0;
    for(int i = 0;i < n;i++){
        ll smaller = 0;
        for(int j = i + 1;j < n;j++){
            if(a[i] > a[j])
                smaller++;
        }
        ans = (ans + fac[n - i - 1] * smaller) % p;
    }
    return ans;
}

//数状数组优化的O(nlogn)康托展开
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define lowbit(x) (x&-x)
const int N = 1e6 + 10,p = 998244353;
int n,a[N];
ll fac[N],tr[N];
void init(){
    fac[1] = 1;
    for(int i = 2;i <= N - 10;i++)
        fac[i] = (fac[i - 1] * i) % p;
}
void add(int x,int num){
    for(;x <= n;x += lowbit(x)) tr[x] += num;
}
int query(int x){
    int ans = 0;
    for(;x >= 1;x -= lowbit(x)) ans += tr[x];
    return ans;
}
ll cantor(int *a){
    ll ans = 0;
    for(int i = 0;i < n;i++)
        add(a[i],1);
   	for(int i = 0;i < n;i++){
        add(a[i],-1);
        ans = (ans + fac[n - i - 1] * query(a[i])) % p;
    }
    return ans;
}
int main(){
	init();
	cin >> n;
	for(int i = 0;i < n;i++)
		cin >> a[i];
	cout << cantor(a) + 1;
}

//O(n)逆康托展开
#define ll long long
const int N = 11;
ll fac[N];
void init(){
    fac[0] = fac[1] = 1;
    for(int i = 2;i < N;i++)
        fac[i] = fac[i - 1] * i;
}
vector<int> ans;
void decantor(int x,int n){
    x--;//变成小于它的排列有几个
    vector<int> rest;//可选的数
    for(int i = 1;i <= n;i++)
        rest.push_back(i);
    for(int i = n;i >= 1;i--){
        int t = x / fac[i - 1];//选第几个
        x %= fac[i - 1];//还剩多少
        ans.push_back(rest[t]);
        rest.erase(rest.begin + t);
    }
}
```

## 组合数与卢卡斯定理

```cpp
//逆元、阶乘逆元、阶乘计算组合数   预处理O(3n) p为质数时
#define ll long long
const int N = 1e7 + 10,p = 1e9 + 7;
ll ny[N],jcny[N],jc[N];
ll fmx(ll a,ll b){
    ll ans = 1;
    while(b){
        if(b&1) ans = (ans * a) % p;
        a = (a*a) % p;
        b >>= 1;
    }
    return ans;
}
void init(){
    ny[0] = jc[0] = jcny[0] = 1;
    for(int i = 1;i <= N - 10;i++){
        ny[i] = fmx(i,p - 2);
        jcny[i] = ny[i] * jcny[i - 1] % p;
        jc[i] = jc[i - 1] * i % p;
    }
}
ll C(ll n,ll m){
	if(n < m) return 0;
    return (((jc[n]*jcny[n-m]) % p) * jcny[m]) % p;
}

//卢卡斯定理 p为质数时并且无法预处理时
//C(n,m)%p=(C(n/p,m/p)*C(n%p,m%p))%p;
ll jc[N];
ll p;
ll C(ll n,ll m){
	if(m >n) return 0;
    return ((((jc[n]*fmx(jc[n-m],p-2))) % p) * fmx(jc[m],p-2)) % p;
}
ll Lucas(ll n,ll m){
	if(!m) return 1;
	return C(n%p,m%p)*Lucas(n/p,m/p)%p;
}

//拓展卢卡斯定理,p不为质数
//将p分解为质数,用中国剩余定理合并
#define ll long long

const int N = 1e6 + 10;

ll qpow(ll x,ll y,ll p){
	ll ans = 1;
	while(y){
		if(y&1) ans = (ans * x) % p;
		x = (x * x) % p;
		y >>= 1;
	}
	return ans;
}

void exgcd(ll a,ll b,ll &x,ll &y)
{
	if(b == 0)
	{
		x = 1;
		y = 0;
		return;
	}
	exgcd(b,a%b,y,x);
	y-=a/b*x;
	return;
}

ll inv(ll a,ll p){
	ll x,y;
	exgcd(a,p,x,y);
	x = ((x % p) + p) % p;
	return x;
}

ll p[N],a[N];
int idx = 0;

ll fac(ll n,ll p,ll pk){
	if(!n) return 1;
	ll ans = 1;
	for(ll i = 1;i < pk;i++){//单循环节，pk求余因此为一循环节
		if(i % p) ans = (ans * i) % pk;
	}
	ans = qpow(ans,n/pk,pk);//有几个循环节
	for(ll i = 1;i <= n % pk;i++){//多的部分
		if(i % p) ans = (ans * i) % pk;
	}
	//为什么要继续n/p?
	//如10!的2的倍数有2、4、6、8、10，数量为5个即10/2，将它们除以2后为1、2、3、4、5
	//其中还有为2的倍数2、4，所以还要继续将它们除完，也就是5/2=2
	return ans * fac(n/p,p,pk) % pk;
}

ll C(ll n,ll m,ll p,ll pk){//把p都去掉
	if(n < m) return 0;
	ll f1 = fac(n,p,pk) , f2 = fac(m,p,pk) ,f3 = fac(n - m,p,pk),cnt = 0;
	ll t1 = n,t2 = m,t3 = n - m;
	//同理计算n/p直到为0,这样才能将p完全去除
	while(t1) cnt += (t1 /= p);
	while(t2) cnt -= (t2 /= p);
	while(t3) cnt -= (t3 /= p);
	return ((((f1*inv(f2,pk)) % pk) * inv(f3,pk)) % pk) * qpow(p,cnt,pk) % pk;
}

ll CRT(int cnt)
{
    ll n = 1,ans = 0;
    for(int i = 1;i <= cnt;i++) n*=p[i];
    for(int i = 1;i <= cnt;i++)
    {
        ll m = n / p[i],mm,t;
        exgcd(m,p[i],mm,t);
        ans = (ans + a[i]*mm*m%n)%n;
    }
    return (ans%n + n) % n;
}

ll ex_lucas(ll n,ll m,ll pp){
	for(int i = 2;pp != 1;i++){//分解p
		if(pp % i) continue;
		idx++;//数量+1
		ll tem = 1;//存p^k
		p[idx] = i;
		while(pp % i == 0) pp /= i,tem *= i;
		a[idx] = C(n,m,p[idx],tem);p[idx] = tem;
	}
	return CRT(idx);
}
```

## 斯特林数

### 第二类斯特林数(最常用的斯特林数)

### 用于表示将n个不同的元素分成m个集合的方案数，记作$S(n,m)$

### 推导：分成两种情况

* 1.由n - 1个不同的元素分成m - 1个集合，那么就将第n个元素放在第m个集合即可
* 2.由n - 1个不同的元素分成m个集合，那么将第n个元素随便放在哪个集合均可
* 因为这种分法确定了第n个元素是否独占一个集合，所以没有重复
* $S(n,m)=m*S(n-1,m)+S(n-1,m-1)$
* 利用上式就就可以设计出时间与空间复杂度均为$O(nm)$的斯特林数预处理

## 排列组合：球盒问题

### 有n个球和m个盒子，根据是否允许为空，球是否相同，盒子是否相同分成8种情况

### 1.允许为空、球不相同、盒子不相同

* 给每个球选择它的盒子，所以结果为$m^{n}$

### 2.不允许为空、球不相同、盒子不相同

* 假设盒子相同，这个问题就转换为将n个元素分成m个集合，然后对盒子全排列即$m!$，所以结果就为$m!S(n,m)$

### 3.允许为空、球相同、盒子不相同

* 假设不允许为空，并且球数为n+m 个，那么结果为$C(n+m-1,m-1)$，再给所有的盒子取出一个球，结果不变，所以为$C(n+m-1,m-1)$

### 4.不允许为空、球相同、盒子不相同

* 插板法，n个球就有n-1个空，插入m-1个板子，所以结果为$C(n-1,m-1)$

### 5.允许为空、球不同、盒子相同

* 第6问的求和，$S(n,1)$ + $S(n,2)$ + ... + $S(n,m)$

### 6.不允许为空、球不同、盒子相同

* 就是n个元素分为m个集合，$S(n,m)$

### 7.允许为空、球相同、盒子相同

* 分解，有空盒和没有的情况
* 有空盒时，与去掉一个空盒的情况数量相同
* 没有时，转换为问题8
* 又根据问题8可以转换为7，得出结果为$ans(n,m)=ans(n,m-1)+ans(n-m,m)$
* 为了简化，n小于m时，直接令$ans(n,m)=ans(n,n)$省去不必要的计算

### 8.不允许为空、球相同、盒子相同

* 所以盒子里的球全减少1个，转换为问题7，$ans8(n,m)=ans7(n-m,m)$

## 多步容斥与二项式反演

### 多步容斥

* 对于$n$个集合$A_1,A_2,...,A_n$来说有

$$
|A_1\cup A_2...\cup A_n|=\sum_{1\leq i \leq n}|A_i| - \sum_{1\leq i < j \leq n}|A_i \cap A_j|...(-1)^{n-1}|A_1 \cap A_2 ...\cap A_n|
$$

* 考虑全集与补集有

$$
\begin{align}
&|A_1^c\cap A_2^c  ...\cap A_n^c|\\=& |S| - |A_1\cup A_2...\cup A_n| \\
=& |S| - \sum_{1\leq i \leq n}|A_i| + \sum_{1\leq i < j \leq n}|A_i \cap A_j|...(-1)^{n}|A_1 \cap A_2 ...\cap A_n|
\end{align}
$$

* 对所有集合进行补集计算后有

$$
\begin{align}
&|A_1\cap A_2  ...\cap A_n|\\
=& |S| - |A_1^c\cup A_2^c...\cup A_n^c| \\
=& |S| - \sum_{1\leq i \leq n}|A_i^c| + \sum_{1\leq i < j \leq n}|A_i^c \cap A_j^c|...(-1)^{n}|A_1^c \cap A_2^c ...\cap A_n^c|
\end{align}
$$

### 二项式反演

* 令集合的交集大小只与集合数量有关有关
* 记$f(n)$为$n$个补集的交集大小，$g(n)$为$n$个原集的交集大小
* 根据补集与原集的性质有

$$
\begin{align}
f(i)&=|S|-C^1_ng(1)+C^2_ng(2)+...(-1)^{n}C^n_ng(n)\\
&=\sum_{0\leq i \leq n}(-1)^{i}C_{n}^{i}g(i)\\
g(i)&=|S|-C^1_nf(1)+C^2_nf(2)+...(-1)^{n}C^n_nf(n)\\
&=\sum_{0\leq i \leq n}(-1)^{i}C_{n}^{i}f(i)
\end{align}
$$

#### 形式2

* 一共有$n$个元素，设$f(k)$为钦定$k$个元素的方法数，$g(k)$为恰好选$k$个元素的方法数

$$
\begin{align}
f(k)=\sum_{i=k}^{n}C_i^kg(i),g(k)=\sum_{i=k}^n(-1)^{i-k}C_i^kf(i)
\end{align}
$$

* 何为钦定，就是先选择的集合会被看作是不同元素，即使最后选择出的集合相同，也就是说对于${1,2,3}$元素，如果我们钦定${1,2}$与${1,3}$那么${1,2,3}$就会被重复计数

#### 形式1

* $f(k)$是$k$个性质中，至少满足$m$个，至多满足$k$个性质的个数
* $g(k)$是$k$个性质中，恰好满足$k$个性质的个数

$$
f(k)=\sum_{i=m}^{k}C_{k}^{i}g(i),g(k)=\sum_{i=m}^k(-1)^{k-i}C^i_kf(i)
$$

## 扩展欧几里得

### 作用：求$ax+by=gcd(a,b)$的可行解

* 设$x',y'$满足$bx'+(a\ mod\ b)y'=gcd(b,a\ mod\ b)$

* 又$a\ mod\ b=a-\lfloor\frac{a}{b}\rfloor *b$，$gcd(a,b)=gcd(b,a\ mod\ b)$

* 可得$ax+by=bx'+(a-\lfloor\frac{a}{b}\rfloor *b)y'$

* 即$ax+by=ay'+b(x'- \lfloor\frac{a}{b}\rfloor *b*y')$
* 即$x=y',y=x'- \lfloor\frac{a}{b}\rfloor *b*y'$
* 最后b肯定为0，此时$ax=a$所以赋值$x=1,y=0$再递归回去

```c++
void exgcd(ll a,ll b,ll &x,ll &y)
{
	if(b == 0)
	{
		x = 1;
		y = 0;
		return;
	}
	exgcd(b,a%b,y,x);
	y-=a/b*x;
	return;
}
```

## 中国剩余定理(计算特殊同余方程组)

$$
\begin{cases}
x\equiv{a_1}\pmod{n_1} \\
x\equiv{a_2}\pmod{n_2} \\
\vdots\\
x\equiv{a_k}\pmod{n_k}
\end{cases}
$$

### 过程：

* 1.计算所有模数的积n
* 2.计算$m_i=\frac{n}{n_i}$
* 3.计算$m_i^{-1}\pmod{n_i}$
* 4.计算$c_i=m_im_i^{-1}$（不取模）
* 5.$x$=求和$c_i*a_i$

```c++
//所有余数互素时
int ci[MAXN],ni[MAXN];
ll CRT(int cnt)
{
    ll n = 1,ans = 0;
    for(int i = 1;i <= cnt;i++) n*=ni[i];
    for(int i = 1;i <= cnt;i++)
    {
        ll m = n / ni[i],mm,t;
        exgcd(m,ni[i],mm,t);
        ans = (ans + ci[i]*mm*m%n)%n;
    }
    return (ans%n + n) % n;
}

//不一定互素时
ll gc;
void exgcd(ll a,ll b,ll &x,ll &y)
{
	if(b == 0)
	{
		x = 1;
		y = 0;
		gc = a;
		return;
	}
	exgcd(b,a%b,y,x);
	y-=a/b*x;
	return;
}
ll ci[N],ni[N];//ci = T
bool flag;
ll exCRT(int n){
    flag = 1;
	ll lcm = ni[1],x = ci[1];
	for(int i = 2;i <= n;i++)
	{
		ll a = ni[i],aa = ci[i];
		ll t1,t2;
		exgcd(lcm,a,t1,t2);
		if((aa-x)%gc != 0){
		    flag = 0;
		    return -1;
		}
		t1 = t1*(aa-x)/gc;
		x = (t1*lcm+x);
		lcm = lcm*a/gc;
		x = (x%lcm+lcm)%lcm;
	}
	return x;
}
```

## 拓展中国剩余定理

* 中国剩余定理考虑了所有数互质的情况，那么如果不互质，结果为多少呢？

$$
\begin{cases}
x\equiv{a_1}\pmod{n_1} \\
x\equiv{a_2}\pmod{n_2} \\
\end{cases}
$$

* 解方程

$$
\begin{cases}
x={a_1}+{n_1}*{t_1} \\
x={a_2}+{n_2}*{t_2} \\
\end{cases}
$$

* $$a_1-a_2=n_1*t_1-n_2*t_2$$
* 如果$a_1-a_2$为$gcd(n_1,n_2)$的整数倍，则有解，通过拓展欧几里得求解，然后乘上倍数，在返回带回原来的式子，得到$x\equiv{a_1+n_1*t_1}\pmod{lcm(n_1,n_2)}$

```c++
ll a1 = a[1],n1 = n[1];
bool flag = true;
for(int i = 2;i <= m;i++)
{
	ll a2 = a[i],n2 = n[i],t1,t2;
    exgcd(n1,n2,t1,t2);
    if((a1-a2)%gc!=0)
        flag = false;
    t1 = t1*(a1-a2)/gc;
    x = (n1*t1+x);
    n1 = n1/gc*n2;
    t1 = (t1%n1+n1)%n1;
}
```

* 可以#define ll __int128以防爆

## Miller_Rabin算法快速判断大数是否为素数

### 并不是绝对，这只是一种判断大概率为素数的方法

### 首先根据费马小定理有：$a^{p-1}=1\pmod p(a不为p的倍数且p不是素数)$

### 又因为$p$为素奇数，所以$p-1$为偶数，表示为$p-1=2^dm$

### 所以有$a^{p-1}-1=0\pmod p$

## $a^{2^dm}-1=0\pmod p$

## $(a^{2^{d-1}m}-1)(a^{2^{d-1}m}+1)=0\pmod p$

### 从而有$a^{2^{d-1}m}=1\pmod p||a^{2^{d-1}}=p-1\pmod p$

### 以此类推有$a^m,a^{2m},a^{2^2m}...a^{p-1}$

```c++
#include <cstdio>
#include <cmath>
#include <cstdlib>
#include <iostream>
using namespace std;
#define ll long long

ll _abs(ll x){
	return x < 0 ? -x : x;
}

inline ll mul(ll x,ll y,ll p){
	ll z=(ld)x/p*y;
	ll res=(ull)x*y-(ull)z*p;
	return (res+p)%p;
}
// ll 表示 long long
// ld 表示 long double
// ull 表示 unsigned long long
// 一种自动溢出的数据类型（存满了就会自动变为0）

inline ll qpow(ll x,ll y,ll mod){
	ll sum = 1;
    while( y ){
        if( y&1 )
            sum = mul(x,sum,mod);
        x = mul(x,x,mod);
        y >>= 1;
    }
    return sum;
}

inline bool Miller_rabin(ll x){
    if( x == 2 || x == 3 || x == 5 || x == 7 )
        return 1;
    if( x < 2 || x % 2 == 0 )
        return 0;
    ll u, p, xx;
    int k = 0;
    u = x-1;
    while( u%2 == 0 ){
        k++;
        u >>= 1;
    }
    for(int i = 1; i < 11; i ++){
        xx = rand()%(x-1)+1;
        xx = qpow(xx, u, x);
        p = xx;
        for(int j = 1; j <= k; j ++){
            xx = mul(xx, xx, x);
            if( p != 1 && p != x-1 && xx == 1 )
                return 0;
            p = xx;
        }
        if( xx != 1 )
            return 0;
    }
    return 1;
}

inline ll gcd(ll a,ll b){
	if(b == 0) return a;
	else return gcd(b,a%b);
}

ll ans = 1e18;

inline ll pollard_rho(ll n, int c)
{
    ll i = 1, k = 2, x, y, d;
    x = rand() % n;
    y = x;
    while(true)
    {
        i++;
        x = (mul(x, x, n) + c) % n;
        d = gcd(_abs(y - x), n);
        if(d > 1 && d < n) return d;
        if(y == x) return n;
        if(i == k)
        {
            y = x;
            k <<= 1;
        }
    }
}
inline void get_small(ll n)
{
    if(n == 1) return;
    if(Miller_rabin(n))
    {
        if(n < ans) ans = n;
        return;
    }
    ll p = n;
    while( p >= n )
        p = pollard_rho(p, rand()%(n-1)+1);
    get_small(p);
    get_small(n / p);
}

int main(){
	ll n,t;
	scanf("%lld", &t);
    while( t-- ){
        scanf("%lld",&n);
        ans = 0x3f3f3f3f3f3f3f3f;
        get_small(n);
        if( n != ans )
            printf("%lld\n", ans);
        else printf("Prime\n");
    }
    return 0;
}
```

## 等比数列求和

```cpp
//p为质数时
ll sum_mul(ll a1,ll n,ll q)//q > 1时
{
	return ((((qpow(q,n) + p - 1) % p) * qpow(q - 1,p - 2) % p) * a1) % p;
}
//p不为质数时
ll sum_mul(ll a1,ll n,ll q){
	if(n == 1)
		return a1;
    if(n % 2 == 1)
        return (a1 + sum_mul((a1 * q) % p,n - 1,q)) % p;
 	else
        return ((qpow(q,n/2) + 1) * sum_mul(a1,n/2,q)) % p;
}
```

## O(1)快速乘

```cpp
#define ll long long
#define ld long double
#define ull unsigned long long
inline ll mul(ll x,ll y,ll p){
	ll z=(ld)x/p*y;
	ll res=(ull)x*y-(ull)z*p;
	return (res+p)%p;
}
```

## 线性逆元

* 首先1的逆元为1
* $a^{-1}\equiv-\lfloor{\frac{p}{a}}\rfloor(p\  mod\ a)^{-1}$

```c++
ll ny[MAXN];
void pre_ny()
{
    ny[1] = 1;
    for(int i = 2;i <= n;i++){
        ny[i] = (ll)(p - p / i)*ny[p % i] % p;//p - p / i是为了防止负数 
    }
}
```

## 欧拉函数

### 定义：表示的是小于等于n和n互质的数的个数。记作$\varphi(n)$

### 思路：先假设答案为n，分解质因数，如果这个质因数为x，那么这个答案就变成了$(x-1)/x$倍（因为少了质因数那一部分$1/x$）

* 代码：

```c++
ll f_ol(ll x)
{
	ll ans = x;
	for(int i = 2;i*i <= x;i++)
	{
		if(x%i==0)
		{
			ans = ans/i*(i-1);
			while(x%i==0) x/=i;
		}
	}
	if(x>1) ans = ans/x*(x-1);
	return ans;
}
```

## 拉格朗日插值定理-已知n+1个点求n次多项式在特定点的值

* 利用n+1个点$(x_i,y_i)$确定n次多项式在$k$的取值

* $f(k)=\sum_{i=0}^ny_i\prod_{j\neq i}\frac{k-x_i}{x_i-x_j}$

* 对于这个式子来说，当带入$x_i$时，除了第$i$项的累乘为1，其他因为均存在$k-x_i$这一项因此为$0$，因此对于$x_i$来说答案就是$y_i$，因此对于这$n+1$个点来说均能被这个式子表示，因此该n次多项式确定为$f(k)=\sum_{i=0}^ny_i\prod_{j\neq i}\frac{x-x_i}{x_i-x_j}$（它刚好是n次多项式）

```cpp
const int p = 998244353,N = 2e3 + 10;
struct point{
	ll x,y;
}P[N];
ll lagrange(ll n,ll k){//n个点,值为k
	ll ans = 0;
    for(int i = 1;i <= n;i++){
    	ll mul = P[i].y;
    	for(int j = 1;j <= n;j++){
    		if(i != j){
    			mul = ((mul * qpow(P[i].x - P[j].x + p,p - 2) % p) * (k - P[j].x + p) % p);
			}
		}
		ans = (ans + mul) % p;
	}
	return ans;
}
```

## 快速计算多项式相乘系数 FFT快速傅里叶变换

### 正常求两个多项式乘积

$$
A(x)=\sum_{i=0}^{n}{A_ix^i},B(x)=\sum_{i=0}^{n}{B_ix^i}
\\
C(x)=\sum_{i=0}^{n}\sum_{j=0}^nA_iB_jx^{i+j}
$$

### 复杂度为$O(n^2)$

### 分别设n+1个在A与B上的点

### 根据高斯消元法，n+1个不同的点一定能确定一个唯一的n次多项式

$$
A(x)=\{(x_0,A(x_0)),(x_1,A(x_1)),(x_2,A(x_2))...(x_n,A(x_n))\}\\
B(x)=\{(x_0,B(x_0)),(x_1,B(x_1)),(x_2,B(x_2))...(x_n,B(x_n))\}\\
$$

### 横坐标相同的点相乘

$$
C(x)=\{(x_0,A(x_0)B(x_0)),(x_1,A(x_1)B(x_1))...(x_n,A(x_n)B(x_n))\}
$$

### 那么就有两个问题：

#### *1.怎么快速得到n+1个点*

#### *2.怎么将点值表达法变回系数*

### 如果$A(x)$是一个偶函数，那么$A(x)=A(-x)$，因此带入一个值也就得到了两个点

### 同理如果$A(x)$是一个奇函数，那么有$A(x)=-A(-x)$，也可以带一个点得到两个点

### 因此可以将$A(x)$分成奇函数部分与偶函数部分

$$
P(x)=a_0+a_1x+a_2x^2+...+a_nx^n\\
P(x)=(a_0+a_2x^2+a_4x^4+...)+x(a_1+a_3x^2+a_5x^4+...)\\
P(x)=P_e(x^2)+xP_o(x^2)\\
P(x_i)=P_e(x_i^2)+x_iP_o(x_i^2)\\
P(-x_i)=P_e(x_i^2)-x_iP_o(x_i^2)
$$

### 然后如果将$P_e(x),P_o(x)$继续分解

### 但我们的$P_e(x),P_o(x)$是通过$P(x)=P_e(x^2)+xP_o(x^2)$得来的，因此取相反数也无法让它们一一符号相反

### 那么我们取复数呢？

$$
i^2=(-i)^2=-1\\
1^2=(-1)^2=1\\
因此有\\
i,-i,1,-1:\ x_1,-x_1,x_2,-x_2\\
-1,1:x_1^2,x_2^2\\
1:x_1^4
\\可解决n\leq4的问题
$$

### 那么更大的呢？

### 我们令$w=e^{\frac{2\pi i}{n}}=cos(\frac{2\pi}{n})+isin(\frac{2\pi}{n})$，我们可以得到它的性质正好可以解决该问题

![捕获](C:\Users\Administrator\Desktop\markdown\快速傅里叶变换.PNG)

#### 反过来呢？

#### 将$w=e^{-\frac{2\pi i}{n}}$，然后给每个数都乘上$\frac{1}{n}$即可

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 2e6 + 10;

typedef complex<double> cp;
const double pie = acos(-1);

int rev[N*2];
cp a[N*2],b[N*2];

void init(int k)
{
    int len=1<<k;
	for(int i=0;i<len;i++)
	rev[i]=(rev[i>>1]>>1)|((i&1)<<(k-1));
}
void fft(cp *a,int n,int flag){ 
    for(int i=0;i<n;i++)
	  if(i<rev[i])swap(a[i],a[rev[i]]);
	for(int h=1;h<n;h*=2)
	{
		cp wn=exp(cp(0,flag*pie/h));
		for(int j=0;j<n;j+=h*2)
		{
			cp w(1,0);
			for(int k=j;k<j+h;k++)
			{
			    cp x=a[k];
			    cp y=w*a[k+h];
		        a[k]=x+y;
		        a[k+h]=x-y;
		        w*=wn;
			}
		 }
	 }
	 if(flag==-1)
	 for(int i=0;i<n;i++)
     a[i]/=n;
}

int main(){
	int n,m;cin >> n >> m;
	n++;
	m++;
	for(int i = 0;i < n;i++)
		cin >> a[i];
	for(int j = 0;j < m;j++)
		cin >> b[j];
	int s = 2,nn = 1;
	while(s <= n + m) nn++,s <<= 1;
	init(nn);
	fft(a,s,1);
	fft(b,s,1);
	for(int i = 0;i < s;i++)
		a[i] *= b[i];
	fft(a,s,-1);
	for(int i = 0;i < n + m - 1;i++)
		cout << (int)(a[i].real() + 0.5) << ' ';
}
```

## 大数乘法

```cpp
typedef complex<double> cp;
const int N = 4e6 + 10;
const double pie = acos(-1);
cp a[N],b[N];
int rev[N],ans[N];
void init(int k){
	int len = 1 << k;
	for(int i = 0;i < len;i++){
		rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (k - 1));
	}
}
void FFT(cp *a,int n,int f){
	for(int i = 0;i < n;i++){
		if(i < rev[i]) swap(a[i],a[rev[i]]);
	}
	for(int h = 1;h < n;h *= 2){
		cp wn = exp(cp(0,f*pie/h));
		for(int j = 0;j < n;j += h*2){
			cp w(1,0);
			for(int k = j;k < j + h;k++){
				cp x = a[k];
				cp y = w*a[k + h];
				a[k] = x + y;
				a[k + h] = x - y;
				w *= wn;
			}
		}
	}
	if(f == -1)
		for(int i = 0;i < n;i++)
			a[i] /= n;
}
int mul(string &s1,string &s2,int *ans){
	int len_1 = s1.size(),len_2 = s2.size();
	for(int i=0;i<len_1;i++){
    	a[i]=(double)(s1[len_1-i-1]-'0');
	}
	for(int i=0;i<len_2;i++){
		b[i]=(double)(s2[len_2-i-1]-'0');
	}
	int k = 1,s = 2;
 	while(s <= len_1 + len_2)k++,s<<=1;
	init(k);
	FFT(a,s,1);
	FFT(b,s,1);
	for(int i = 0;i < s;i++){
		a[i] *= b[i];
	}
	FFT(a,s,-1);
	for(int i=0;i<s;i++)
    {
		ans[i] += (int)(a[i].real()+0.5);
		ans[i+1] += ans[i] / 10;
		ans[i] %= 10;
	}
	while(!ans[s] && s != 0) s--;
	for(int i = s;i > s/2;i--)
		swap(ans[i],ans[s - i]);
	return s;
}
int main(){
	string s1,s2;cin >> s1 >> s2;
	int s = mul(s1,s2,ans);
	for(int i = 0;i <= s;i++)
		cout << ans[i];
}
```

## NTT

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 2e6 + 10,p = 998244353,G = 3,Gi = 332748118;

#define ll long long
inline ll qpow(ll x,ll y){ll ans = 1;while(y){if(y&1) ans = ans*x%p;x = x*x%p;y >>= 1;}return ans;}

int rev[N*2];
ll a[N*2],b[N*2];
ll inv;

void init(int k)
{
    int len=1<<k;
	for(int i=0;i<len;i++)
	rev[i]=(rev[i>>1]>>1)|((i&1)<<(k-1));
}
void NTT(ll *a,int n,int f){
	for(int i = 0;i < n;i++){
		if(i < rev[i]) swap(a[i],a[rev[i]]);
	}
	for(int h = 1;h < n;h *= 2){
		ll Wn = qpow( f == 1 ? G : Gi , (p - 1) / (h << 1));
		for(int j = 0;j < n;j += h*2){
			ll w = 1;
			for(int k = j;k < j + h;k++,w = (w * Wn) % p){
				ll x = a[k];
				ll y = w*a[k + h] % p;
				a[k] = (x + y) % p;
				a[k + h] = (x - y + p) % p;
			}
		}
	}
	if(f == -1)
	for(int i = 0;i < n;i++)
        a[i] = a[i]*inv%p;
}

int main(){
	int n,m;cin >> n >> m;
	n++;
	m++;
	for(int i = 0;i < n;i++)
		cin >> a[i];
	for(int j = 0;j < m;j++)
		cin >> b[j];
	int s = 2,nn = 1;
	while(s <= n + m) nn++,s <<= 1;
	init(nn);
	NTT(a,s,1);
	NTT(b,s,1);
	for(int i = 0;i < s;i++)
		a[i] = b[i]*a[i] % p;
	inv = qpow(s,p-2);
	NTT(a,s,-1);
	ll inv = qpow(s,p-2);
	for(int i = 0;i < n + m - 1;i++)
		cout << a[i] % p << ' ';
}
```

## NTT版大数乘法

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int N = 4e6 + 10,p = 998244353,G = 3,Gi = 332748118;
ll a[N],b[N];
int rev[N],ans[N];
inline ll qpow(ll x,ll y){ll ans = 1;while(y){if(y&1) ans = ans*x%p;x = x*x%p;y >>= 1;}return ans;}
void init(int k){
	int len = 1 << k;
	for(int i = 0;i < len;i++){
		rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (k - 1));
	}
}
void NTT(ll *a,int n,int f){
	for(int i = 0;i < n;i++){
		if(i < rev[i]) swap(a[i],a[rev[i]]);
	}
	for(int h = 1;h < n;h *= 2){
		ll Wn = qpow( f == 1 ? G : Gi , (p - 1) / (h << 1));
		for(int j = 0;j < n;j += h*2){
			ll w = 1;
			for(int k = j;k < j + h;k++,w = (w * Wn) % p){
				ll x = a[k];
				ll y = w*a[k + h] % p;
				a[k] = (x + y) % p;
				a[k + h] = (x - y + p) % p;
			}
		}
	}
}
int mul(string &s1,string &s2,int *ans){
	int len_1 = s1.size(),len_2 = s2.size();
	for(int i=0;i<len_1;i++){
    	a[i]=(s1[len_1-i-1]-'0');
	}
	for(int i=0;i<len_2;i++){
		b[i]=(s2[len_2-i-1]-'0');
	}
	int k = 1,s = 2;
 	while(s <= len_1 + len_2)k++,s<<=1;
	init(k);
	NTT(a,s,1);
	NTT(b,s,1);
	for(int i = 0;i < s;i++){
		a[i] = a[i] * b[i] % p;
	}
	NTT(a,s,-1);
	ll tem = qpow(s,p-2);
	for(int i = 0;i < s;i++){
		ans[i] += a[i]*tem % p;
		ans[i + 1] += ans[i] / 10;
		ans[i] %= 10;
	}
	while(!ans[s] && s != 0) s--;
	for(int i = s;i > s/2;i--)
		swap(ans[i],ans[s - i]);
	return s;
}

int main(){
	string s1,s2;cin >> s1 >> s2;
	int s = mul(s1,s2,ans);
	for(int i = 0;i <= s;i++)
		cout << ans[i];
}
```

## FWT（与，或，异或卷积）

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 18,p = 998244353;

#define ll long long

inline ll qpow(ll x,ll y){ll ans = 1;while(y){if(y&1) ans = ans*x%p;x = x*x%p;y >>= 1;}return ans;}

ll a[1<<N],b[1<<N];
ll A[1<<N],B[1<<N];

int n;
inline void OR(ll *f, int x = 1) {
	for (int o = 2, k = 1; o <= n; o <<= 1, k <<= 1)
		for (int i = 0; i < n; i += o)
			for (int j = 0; j < k; ++ j)
				f[i + j + k] = (f[i + j + k] + f[i + j] * x) % p;
}
inline void AND(ll *f, int x = 1) {
	for (int o = 2, k = 1; o <= n; o <<= 1, k <<= 1)
		for (int i = 0; i < n; i += o)
			for (int j = 0; j < k; ++ j)
				f[i+j] = (f[i+j] + f[i+j+k] * x) % p;
}
inline void XOR(ll *f, int x = 1) {
	for (int o = 2, k = 1; o <= n; o <<= 1, k <<= 1)
		for (int i = 0; i < n; i += o)
			for (int j = 0; j < k; ++ j)
				f[i + j] = (f[i + j] + f[i + j + k]) % p,
				f[i + j + k] = (f[i + j] - f[i + j + k] - f[i + j + k] + 2*p) % p,
				f[i + j] = f[i + j] * x % p, f[i+j+k] = f[i+j+k] * x % p;
}

void mul(){
	for(int i = 0;i < n;i++) a[i]=a[i]*b[i]%p;
}
void out(){
	for(int i = 0;i < n;i++){
		cout << a[i] << ' ';
		a[i] = A[i];
		b[i] = B[i];
	}
	cout << endl;
}

int main(){
	cin >> n;n = 1<<n;
	for(int i = 0;i < n;i++){
		cin >> a[i];A[i] = a[i];
	}
	for(int i = 0;i < n;i++){
		cin >> b[i];B[i] = b[i];
	}
	OR(a,1);OR(b,1);mul();OR(a,p - 1);out();
	AND(a,1);AND(b,1);mul();AND(a,p - 1);out();
	XOR(a,1);XOR(b,1);mul();XOR(a,qpow(2,p-2));out();
}
```

## 生成函数

### 普通生成函数(适用于限制元素取法的计数)

#### 形如$f(x)=\sum_{i=0}^{∞,n}a_ix^i$，根据次数代表出现次数，将所有条件化为生成函数，累乘，得到结果的生成函数，再通过$\frac{1}{(1-x)^n}=\sum ^{∞}_{i=0}C_{n+i-1}^{n-1}x^i$转换，取$x^i$的系数就是取$i$个元素的所有取法

#### 例题：食物

* 在许多不同种类的食物中选出n个，每种食物的限制如下：

* 承德汉堡：偶数个

* 可乐：0 个或 1 个

* 鸡腿：0 个，1 个或 2 个

* 蜜桃多：奇数个

* 鸡块：4 的倍数个

* 包子：0 个，1 个，2 个或 3 个

* 土豆片炒肉：不超过一个。

* 面包：3 的倍数个

* 每种食物都是以「个」为单位，只要总数加起来是n就算一种方案。对于给出的n你需要计算出方案数，对10007取模。

#### 得出每个食物的生成函数分别为

* $1+x^2+x^4+...=\frac{1}{1-x^2}$
* $1+x=\frac{1-x^2}{1-x}$
* $1+x+x^2=\frac{1-x^3}{1-x}$
* $x+x^3+x^5+...=\frac{x}{1-x^2}$
* $1+x^4+x^8+...=\frac{1}{1-x^4}$
* $1+x+x^2+x^3=\frac{1-x^4}{1-x}$
* $1+x=\frac{1-x^2}{1-x}$
* $1+x^3+x^6+...=\frac{1}{1-x^3}$

#### 累乘可得

* $f(x)=\frac{x}{(1-x)^4}$

#### 再根据$\frac{1}{(1-x)^n}=\sum ^{∞}_{i=0}C_{n+i-1}^{n-1}x^i$可得

* $f(x)=\frac{x}{(1-x)^4}=\sum_{i=0}^∞C_{3+i}^3x^{i+1}$
* 令$i+1=n$即可得$ans=C_{2+n}^3$

## 矩阵快速幂与快速斐波那契数列

已知$f(n)=af(n-1)+bf(b-2)$，因为有两项所以我们构造一个$2*2$的矩阵使得
$$
\begin{bmatrix}f(n-1)&f(n-2)\end{bmatrix}*
\begin{bmatrix}
t1&t2\\
t3&t4
\end{bmatrix}
=\begin{bmatrix}f(n)&f(n-1)\end{bmatrix}
$$
根据矩阵乘法规律
$$
f(n)=t1*f(n-1)+t3*f(n-2)\\
f(n-1)=t2*f(n-1)+t4*f(n-2)
$$
推得$t1=a,t2=1,t3=b,t4=0$

即
$$
\begin{bmatrix}f(n-1)&f(n-2)\end{bmatrix}*
\begin{bmatrix}
a&1\\
b&0
\end{bmatrix}
=\begin{bmatrix}f(n)&f(n-1)\end{bmatrix}
$$
所以有
$$
\begin{bmatrix}f(2)&f(1)\end{bmatrix}*
\begin{bmatrix}
a&1\\
b&0
\end{bmatrix}^{n-2}
=\begin{bmatrix}f(n)&f(n-1)\end{bmatrix}
$$
同理斐波那契数列为
$$
\begin{bmatrix}1&1\end{bmatrix}*
\begin{bmatrix}
1&1\\
1&0
\end{bmatrix}^{n-2}
=\begin{bmatrix}f(n)&f(n-1)\end{bmatrix}
$$
矩阵乘法：

```c++
#define ll long long
const int N = 3,mod = 1e9 + 7;
struct jz{
    ll a[N][N];
    jz(){memset(a,0,sizeof(a));}//构造
    jz(bool f){memset(a,0,sizeof(a));//单位矩阵
    for(int i = 1;i <= 2;i++)
        a[i][i] = 1;
    }//构造单位矩阵
    jz operator*(const jz &other)const{
        jz ans;
        for(int i = 1;i <= 2;i++)
            for(int j = 1;j <= 2;j++)
                for(int k = 1;k <= 2;k++)
                    ans.a[i][j] = (ans.a[i][j] + a[i][k]*other.a[k][j]) % mod;
       	return ans;
    }
}
```

单位矩阵：

从左上角到右下角的对角线上的元素均为1。除此以外全都为0。根据单位矩阵的特点，任何矩阵与单位矩阵相乘都等于本身

矩阵快速幂：

```c++
jz jzfpow(jz a,ll n){
	jz ans(true);
    while(n){
        if(n&1) ans = (ans*a);
        n >>= 1;
        a=a*a;
    }
    return ans;
}
```

```cpp
const ll N = 101,mod = 1e9 + 7;
int n;
struct jz{
    ll a[N][N];
    jz(){memset(a,0,sizeof(a));}//构造
    jz(bool f){memset(a,0,sizeof(a));//单位矩阵
    for(int i = 1;i <= n;i++)
        a[i][i] = 1;
    }//构造单位矩阵
    jz operator*(const jz &other)const{
        jz ans;
        for(int i = 1;i <= n;i++)
            for(int j = 1;j <= n;j++)
                for(int k = 1;k <= n;k++)
                    ans.a[i][j] = (ans.a[i][j] + (a[i][k]*other.a[k][j]) % mod) % mod;
       	return ans;
    }
};
jz jzfpow(jz a,ll n){
	jz ans(true);
    while(n){
        if(n&1) ans = (ans*a);
        n >>= 1;
        a=a*a;
    }
    return ans;
}
```

## 卡特兰数

### n对括号匹配，n个数入栈出栈

### 递推：$h(n)=h(n-1)*(4n-2)/(n+1)$

### 解：$h(n)=C(2n,n)/(n+1)$

## $h(n)=C(2n,n)-C(2n,n-1)$

## 二分图博弈

### 定义：

* 两人博弈
* 两类状态点
* 每次转移从A状态转移到B状态
* 不可转移到已经访问过的状态点
* 无法转移者判负

### 典题：

* 两个人轮流移动无向图上的一个点，已经游览过的点不能再次访问，询问是先手还是后手胜利问题。

### 结论：

* 如果起点是二分图最大匹配中一定包含的点，那么先手必胜；反正就是后手必胜。

## 博弈论入门

* 必败情况为P，必胜情况为N，我们要得出N一定有方法能转换到P，P任意操作都会到N

### 1.巴什博弈

* 两个顶尖聪明的人在玩游戏，有一堆n个石子，每次每个人能取$[1,m]$个石子，不能拿的人输，请问先手与后手谁必败？

* 1~m个石子，先手必胜
* 反推m+1个石子只能到1~m，所以必败
* 反推m+2~2*m+1一定能到m+1，所以必胜
* 综上所述，当石子数为m+1的倍数时必败，否则必胜

### 2.尼姆博弈

* 两个顶尖聪明的人在玩游戏，有n堆石子，第$i$堆有$a_i$个，每人每次能从一堆石子中取任意多个石子但不能不取，不能拿的人输，请问先手与后手谁必胜？

* 最后所有数都为0，所以异或和为0
* 反推一步，将一个数改变，那么异或和一定不为0
* 也就是说先手的人保持自己的异或和为0就能获得最后的胜利，如果本来就是0就输
* 那么一定有保持自己异或和为0的取法吗？
* 假设异或和为x，那么一定有一个数在这个位上为1，让它和x异或，它一定会变小，因为x最高位变成了0，无论后面是多少都会变小。
* 结论：异或和为0后手赢，反之先手赢

### nimk游戏

* 如果将游戏改成每人每次可以从$[1,d]$堆中各取任意多个石子呢？
* 每个二进制上为1的个数%(1 + d) = 0为必败情况
* 以下为反推过程

* 全0为P
* 对于P状态，每次操作$[1,d]$个数，对于操作的最高位来说也就是减少$[1,d]$次，无法变回%(1 + d) = 0
* 因此P任意操作都会到N得证
* 对于N状态，从高位处理到低位，如果高位有从1到0的，那么下面的位无论取0还是1都无所谓
* 对于最大位，取多余的1
* 对于下面的位，假设有n个高位从1到0，也就是说有n个可以任意换0或换1，有a个1和b个0，那么它的贡献就可以为$[-a,b]$，如果n到了d，这个范围就包括了$[1,d]$
* 如果n没到d，那就可以取外面的1，照样可以让这个范围覆盖$[1,d]$，并且将这个范围扩大
* 因此N有方法到P得证
* 综上所述每个二进制上为1的个数%(1 + d) = 0为必败情况，否则必胜

### 3.威佐夫博弈

* 两个顶尖聪明的人在玩游戏，有2堆石子，每人每次可以拿走任意一堆中任意数量的石子或在两堆石子中拿走相同数量的石子，不能拿的人输，请问先手与后手谁必胜？
* 结论：$(⌊\frac{1+\sqrt5}2|y-x|⌋,⌊\frac{3+\sqrt5}2|y-x|⌋)$必败，否则必胜，利用Betty定理~~不会，再说~~

#### 扩展威佐夫博弈

* 要求取的数的绝对值之差$|x-y|<d$，或者只取一个
* 结论：$(⌊\frac{2-d+\sqrt{d^2+4}}2k⌋,⌊\frac{2+d+\sqrt{d^2+4}}2k⌋)$，$y=x+dk$必败~~背结论呗~~

### 4.斐波那契博弈

* 两个顶尖聪明的人在玩游戏，有一堆石子，先手第一次可以拿任意多个但不能全拿走也不能不拿，之后每个人最少拿一个，最多拿前一个人两倍那么多个，谁取到最后一个谁就能获胜，请问先手后手谁必胜？

* 结论：为斐波那契数时必败，否则必败

* 数学归纳法证明：斐波那契数列时，先手必败

* 当n等于2时显然先手必败

* 假设$n=f[i](i <= k)$的先手必败，现证明$n=f[k+1]=f[k]+f[k-1]$是否先手必败

* 因为$f[k+1]-f[k-1]=f[k]=f[k-1]+f[k-2]<f[k-1]*2$所以先手不能取大于$f[k-1]$的数

* 那么根据$n=f[k-1]$时先手必败，将会是后手取完$f[k-1]$所以问题就转换为$n=f[k]$时先手必败

* 再证明：不是斐波那契数列时，先手必胜
* 首先根据齐肯多夫定理：**任何整数可以分解成若干个不连续的斐波那契数之和**，可以将n分解成a+b+c+...
* 取最小的斐波那契数a，因为不连续，所以b>2*a，这就转换为面对斐波那契数b先手必败，直到把所有的数取完

### 5.阶梯博弈

* 两个绝顶聪明的人在玩游戏，有n堆石子，每次每人可以取走第i堆(i>1)任意数量的石子并将它们放到第i−1堆，或者直接取走第一堆的任意数量石子，不能操作的人输，请问先手能否必胜？

* 转换为奇数位的尼姆博弈
* 从偶数位下放到奇数位，下一个人可以再将这些多的下放到下一个偶数位直到丢掉，所以对于后手来说，丢偶数位没有用
* 如果只丢奇数位，就变成了n/2堆石头，尼姆博弈

## SG函数解决公平组合问题

### 公平组合问题：

- 游戏有两个人参与，二者轮流做出决策，双方均知道游戏的完整信息；
- 任意一个游戏者在某一确定状态可以作出的决策集合只与当前的状态有关，而与游戏者无关（如象棋就为非公平组合游戏，因为你不能操作对手的棋子，那么你可以操作的集合就与对手不同）；
- 游戏中的同一个状态不可能多次抵达，游戏以玩家无法行动为结束，且游戏一定会在有限步后以非平局结束。

### SG值：

* 定义SG值为不属于可到集合的SG值的最小非负整数
* 定义必败状态SG为0
* 那么必败状态(SG值 = 0)必定不能到必败状态
* 必胜状态(SG值 ！= 0)必定能到必败状态(SG = 0)(因为可到集合SG值的最小值不为0)

### SG值定理

* 若有多个这样的公平组合游戏，那么求它们的异或值，就是它的SG值，为0说明必败，否则必胜
* 如果一个公平组合游戏可以分解为多个，就求它们的异或值。

### 或者情况与分离情况

* 如果可以转移到A或B状态，那么直接取A，B状态的SG值即可
* 如果是可以转移到A与B共存状态，那么取A^B即可

### 例题

### 1.暴力型

[Cutting Game](https://vjudge.net/problem/OpenJ_Bailian-2311)

### 每次切一刀，第一个切出1*1的方块的胜利

### $2\leq n,m \leq 200$

### 有1时必胜，设SG值为1，其他递推即可

```c++
#include<bits/stdc++.h>
using namespace std;
int sg[201][201];
int main()
{
	for(int i = 1;i <= 200;i++){
		sg[i][1] = sg[1][i] = 1;
	}
	for(int i = 1;i <= 200;i++)
	for(int j = 1;j <= 200;j++){
		set<int> se;
		int _sg = 0;
		for(int _i = 2;_i <= i - 2;_i++){
			se.insert(sg[_i][j]^sg[i-_i][j]);
		}
		for(int _j = 2;_j <= j - 2;_j++){
			se.insert(sg[i][_j]^sg[i][j-_j]);
		}
		for(auto x:se){
			if(_sg == x){
				_sg++;
			}else
				break;
		}
		sg[i][j] = _sg;
	}
	int w,h;
	while(cin >> w >> h){
		if(sg[w][h])
			cout << "WIN" << endl;
		else
			cout << "LOSE" << endl;
	}
}
```

### 2.打表找规律型

[G. Integer Game](https://codeforces.com/gym/103860/problem/G)

### 题意：给n个集合$[l_i,r_i]$和p，每次选一个集合和一个x，要求$x*p \geq r_i$

### 思路：打个表求SG函数先

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int sg[1001];
	int p = 3,l = 1;
	for(int r = 1;r <= 100;r++){
		set<int> se;
		for(int x = r;x*p >= r;x--){
			if(x <= l)
				se.insert(0);
			else
				se.insert(sg[x-1]);
		}
		int _sg = 0;
		for(auto x:se){
			if(_sg == x)
				_sg++;
			else
				break;
		}
		cout << (sg[r] = _sg) << ' ';
	}
}
```

### 得出规律：前$ql-l+1$个从$1$到$ql-l+1$,然后接上一个0，然后是长度为$p$的循环递增节，如果不是p的整数倍，那么正常递增，否则所有后面的p的整数倍的形成和原序列相同的序列（用递归求）

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
ll get_sg(ll l,ll r,ll p){
	ll num = r - l + 1;
	if(num <= p*l - l + 1)
		return num;
	else if(num == p*l - l + 2)
		return 0;
	else{
		num -= p*l - l + 2;
		if(num % p == 0)
			return get_sg(l,l+num/p-1,p);
		else
			return (num/p)*(p - 1) + (num % p) + p*l - l + 1;
	}
}
int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	int t;cin >> t;
	while(t--){
		ll nim = 0;
		int n;
		ll p;cin >> n >> p;
		for(int i = 1;i <= n;i++){
			ll l,r;cin >> l >> r;
			nim ^= get_sg(l,r,p);
		}
		if(nim == 0)
			cout << "Second" << endl;
		else
			cout << "First" << endl;
	}
}
```

### [A Funny Game](https://vjudge.net/contest/567667#problem/F)

~~做过看到这题我就想笑~~

#### 题意：一个环，每次可以取1~2连续的节点，取了点后就断开了环或者线，不能取的败

#### 思路：一个环比较难直接求sg函数，但一条线可以分解为两条线，因此sg函数比较好求，而环也可以转换为环长-1与环长-2的线的问题，那么只要求环长-1与环长-2即可

#### 打个表先

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 1e3 + 10;
int sg[N];
int main(){
	sg[0] = 0;cout << 0 << ' ';
	for(int i = 1;i <= 1e3;i++){
		bool vis[N] = {0};
		for(int j = 1;j <= i;j++){
			vis[sg[i - j]^sg[j - 1]] = 1;
			if(j > 1)
				vis[sg[i - j]^sg[j - 2]] = 1;
		}
		int _sg = 0;
		sg[i] = 0;
		while(vis[_sg]){
			sg[i] = ++_sg;
		}
		cout << sg[i] << ' ';
	}
}
```

![打表](C:\Users\Administrator\Desktop\markdown\打表.PNG)

#### 笑了，除了0必败外其他均必胜，也就是说作为环除了$n\leq2$的情况先手能胜利，其他情况先手均败

```cpp
#include<iostream>
using namespace std;
int main(){
	int n;
	cin >> n;
	while(n != 0){
		if(n <= 2)
			cout << "Alice" << endl;
		else
			cout << "Bob" << endl;
		cin >> n;
	}
}
```

#### 那么我们来正常想一下，先手将一个环变成了一条线，那么后手在中间取1或者2，一定有方法将它分成完全相同的两份，那么后手只要模仿先手的操作，那么后手一定是取完所有点的人，因此除了$n=1,2$的情况，其他情况后手均必胜

~~想笑~~

### [G - A Chess Game](https://vjudge.net/contest/567667#problem/G)

#### 题意：N个位置，相互连接形成一个有向拓扑图，在这些位置上放棋子，每个人可以移动棋子一步，谁先不能移动就输

####  思路：深度为0，SG值就为0，~~反推即可~~，~~然后爆时间了~~，用dfs正向推，记忆化搜索即可，然后棋子放哪就取它的SG值异或即可

```cpp
#include<iostream>
#include<stdio.h>
#include<set>
#include<queue>
#include<vector>
using namespace std;
const int N = 1e3 + 10;
int sg[N];
vector<int> ed[N];

int ask(int x){
	if(sg[x] != -1) return sg[x];
	int sz = ed[x].size();
	bool use[N] = {0};
	for(int i = 0;i < sz;i++){
		use[ask(ed[x][i])] = 1;
	}
	sg[x] = 0;
	int _sg = 0;
	while(1){
		if(use[_sg])
			sg[x] = ++_sg;
		else
			break;
	}
	return sg[x];
}

int main(){
	int n;
	while(~scanf("%d",&n)){
		queue<int> qu;
		for(int i = 0;i < n;i++){
			int num;scanf("%d",&num);
			ed[i].clear();
			for(int j = 1;j <= num;j++){
				int x;scanf("%d",&x);
				ed[i].push_back(x);
			}
		}
		int m;
		memset(sg,-1,sizeof(sg));
		while(scanf("%d",&m),m){
			int nim = 0;
			for(int i = 1;i <= m;i++){
				int x;scanf("%d",&x);
				nim^=ask(x);
			}
			if(nim)
				puts("WIN");
			else
				puts("LOSE");
		}
	}
}
```

### [H - S-Nim](https://vjudge.net/contest/567667#problem/H)

#### 题意：每次只能取S集合内数字的nim游戏

#### 思路：总数最大为10000，集合内数100，暴力求SG就$10^6$，直接求即可

```cpp
#include<iostream>
#include<stdio.h>
#include<set>
#include<queue>
#include<vector>
using namespace std;
const int N = 1e4 + 10,M = 1e2 + 10;
int sg[N],s[M];
int n,m;

void getnim(){
	for(int i = 1;i <= 1e4;i++){
		bool vis[M] = {0};
		for(int j = 1;j <= m;j++)
			if(i - s[j]>= 0)
				vis[sg[i - s[j]]] = 1;
		int _sg = 0;
		sg[i] = 0;
		while(1){
			if(vis[_sg])
				sg[i] = ++_sg;
			else
				break;
		}
	}
}

int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	cin >> m;
	while(m != 0){
		for(int i = 1;i <= m;i++)
			cin >> s[i];
		getnim();
		cin >> n;
		while(n--){
			int t;cin >> t;
			int nim = 0;
			for(int i = 1;i <= t;i++){
				int x;cin >> x;
				nim ^= sg[x];
			}
			if(nim)
				cout << 'W';
			else
				cout << 'L';
		}
		cout << endl;
		cin >> m;
	}
}
```

# 图论

## 树的直径

* 用一次bfs找端点，再用一次bfs找直径

* [A - Roads in the North](https://vjudge.net/problem/POJ-2631)

```c++
#include<queue>
#include<iostream>
using namespace std;
const int MAXN = 1e4 + 10;
struct edge{
	int v,dis;
	edge* nex;
}ed[MAXN * 2];
edge* head[MAXN];
int ptop = 0,ans = 0;
bool use[MAXN];
struct pos{
	int x;
	int dis;
	bool operator <(const pos &x)const{
		return dis > x.dis;
	}//重载运算符
};
priority_queue<pos> qu;
int bfs(int u){
	pos tem;
	tem.x = u,tem.dis = 0;
	qu.push(tem);
	int x;
	while(!qu.empty()){
		x = qu.top().x;
		int pre = qu.top().dis;
		ans = pre;
		qu.pop();
		edge* p = head[x];
		while(p != NULL){
			int v = p -> v,dis = p -> dis;
			p = p -> nex;
			if(!use[v]){
				use[v] = 1;
				pos tem;
				tem.x = v,tem.dis = dis + pre;
				qu.push(tem);
			}
		}
	}
	return x;
}
void add(int u,int v,int dis){
	ed[ptop].v = v;
	ed[ptop].dis = dis;
	ed[ptop].nex = head[u];
	head[u] = &ed[ptop];
	ptop++;
}
int main()
{
	int u,v,dis;
	while(~scanf("%d %d %d",&u,&v,&dis)){
		add(u,v,dis);
		add(v,u,dis);
	}
	use[u] = 1;
	u = bfs(u);
	memset(use,0,sizeof(use));
	use[u] = 1;
	bfs(u);
	cout << ans << endl;
}
```

## 树的重心

* 重心的性质：去掉后，剩下的连接部分的最大数量最小
* 也就是子树最大值最小，求出所有节点的子树的最大值即可
* 用dfs可以找到所有节点的子节点子树，但是不知道父节点方向的子树，因为知道点的数量为n，所以只要用n减去所有子节点子树数量之和再减1就能得出父节点的子树数量

```c++
#include<iostream>
#include<algorithm>
using namespace std;
const int MAXN = 5e4 + 10;
struct edge{
	int v;
	edge* nex;
}ed[MAXN * 2];
edge* head[MAXN];
int ptop = 0;
void add(int u,int v){
	ed[ptop].v = v;
	ed[ptop].nex = head[u];
	head[u] = &ed[ptop];
	ptop++;
}
int sz[MAXN];
pair<int,int> ans[MAXN];
bool use[MAXN];
int n;
void find(int dad,int x){
	edge* p = head[x];
	sz[x] = 1;
	int ma = 0;
	while(p != NULL){
		int v = p -> v;
		p = p -> nex;
		if(v == dad)
			continue;
		if(!use[v])
			find(x,v);
		use[v] = 1;
		sz[x] += sz[v];
		ma = max(sz[v],ma);
	}
	ans[x].first = max(n-sz[x],ma);
	ans[x].second = x;
}
bool cmp(pair<int,int> a,pair<int,int> b){
		if(a.first == b.first)
			return a.second < b.second;
		else
			return a.first < b.first;
	}
int main()
{
	scanf("%d",&n);
	for(int i = 1;i <= n - 1;i++){
		int u,v;
		scanf("%d%d",&u,&v);
		add(u,v);
		add(v,u);
	}
	find(0,1);
	sort(ans + 1,ans + 1 + n,cmp);
	int mi = ans[1].first;
	for(int i = 1;i <= n;i++)
		if(mi == ans[i].first)
			printf("%d ",ans[i].second);
	return 0;
}
```

## 用贪心和树上dp解决[树的最小支配集，最小点覆盖与最大独立集](https://www.cnblogs.com/i-love-acm/p/3558238.html)问题

* 最小支配集：对于图$G=(V,E)$来说，选最少的点组成一个集合使得所有点都与集合中的点有边相连

* 最小点覆盖：对于图$G=(V,E)$来说，选最少的点组成一个集合使得所有边都与集合中的点相连
* 最大独立集：对于图$G=(V,E)$来说，选最多的点组成一个集合使得任意集合中的两点不相连

* 贪心法：用dfs给所有节点反向排序，这样就能实现对节点处理时所有子树都被处理过，从而确定贪心的正确性
* DFS确定顺序

```c++
void dfs(int _dad,int x){
    edge* p = head[x];
    while(p != NULL){
        int v = p -> v;
        p = p -> nex;
        if(use[v])
            continue;
       	use[v] = 1;
        dfs(x,v);
    }
    dad[x] = _dad;
    a[++t] = x;
}
```

* 对于最小支配集，贪心方案为从子树开始，如果这个节点没有被标记并且父节点没有被选，为了对后面产生最好的影响，选取父节点，如果标记自己，父节点，父节点的父节点，因为已经用DFS确定过顺序，所以只要考虑父节点即可，子节点已经被标记过

```c++
void greedy(int n){
    for(int i = 1;i <= n;i++){
        int x = a[i];
        if(bj[x] || xz[dad[x]])//已经被标记过或者父节点被选中
            continue;
        else{
            ans++;
            xz[dad[x]] = 1;
            bj[x] = 1;
            bj[dad[x]] = 1;
            bj[dad[dad[x]]] = 1;
        }
    }
}
```

* 对于最小点覆盖，相对于最小支配集，也是要选中父节点来对后面产生最好影响，每次轮到的节点，只有父节点方向的边，所以只要看父节点是否被选中即可，对于根节点，因为没有父节点所以要特判

```c++
void greedy(int n){
    for(int i = 1;i <= n;i++){
        int x = a[i];
        if(xz[x] || xz[dad[x]] || x == dad[x])
            continue;
        else{
            ans++;
            xz[dad[x]] = 1;
        }
    }
}
```

* 对于最大独立集，为了对后面产生最好影响，当这个点没有被标记时就应该直接选中这个点，每次选中这个点时标记自己和父节点

```c++
void greedy(int n){
    for(int i = 1;i <= n;i++){
        int x = a[i];
        if(bj[x])
            continue;
        else{
            ans++;
            bj[dad[x]] = 1;
        }
    }
}
```

* 动态规划法：同贪心法用DFS处理完子树再处理主树

## 树哈希：判断树的同构

### [P5043 【模板】树同构（[BJOI2015]树的同构）](https://www.luogu.com.cn/problem/P5043)

* 拓扑排序，从外到内哈希，在最后一层对每个点都当作根，取最大的哈希作为这棵树的哈希值

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

typedef unsigned long long ull;

const ull mask = std::chrono::steady_clock::now().time_since_epoch().count();

const int N = 51;

ull shift(ull x){
	x ^= mask;
	x ^= x << 13;
	x ^= x >> 7;
	x ^= x << 17;
	x ^= mask;
	return x;
}

int in[N];
ull res_hash[N];
ull res_ans[N];

vector<int> ed[N];

int nn;

map<ull,int> mp;

int get_mahash(int no){
	queue<int> qu;
	for(int i = 1;i <= nn;i++){
		if(in[i] == 1) qu.push(i); 
	}
	ull ma = -1;
	while(!qu.empty()){
		int sz = qu.size();
		nn -= sz;
		ull ma = 0;
		queue<int> tem;
		while(sz--){
			int x = qu.front();
			qu.pop();
			tem.push(x);
			in[x] = -1;
			for(auto y : ed[x]){
				in[y]--;
				if(in[y] == 1){
					qu.push(y);
					in[y] = -3;
				}else if(in[y] == -1){
					res_hash[x] += res_hash[y];
				}
			}
		}
		if(qu.size() == 0)//最中间的那些点就是tem里面的点
		{
			while(!tem.empty()){
				int x = tem.front();
				tem.pop();
				ull has = res_hash[x];
				for(auto y : ed[x]){
					if(in[y] != -3) has += res_hash[y];
				}
				has = shift(has);
				ma = max(ma,has);
			}
			res_ans[no] = ma;
			if(mp[ma] == 0) mp[ma] = no;
		}else{
			while(!tem.empty()){
				int x = tem.front();
				in[x] = 0;
				res_hash[x] = shift(res_hash[x]);
				tem.pop();
			}
		}
	}
}

void test(){
	int n;cin >> n;
	for(int j = 1;j <= n;j++){
		cin >> nn;
		for(int i = 1;i <= nn;i++){
			res_hash[i] = 1;
			in[i] = 0;
			ed[i].clear();
		}
		for(int i = 1;i <= nn;i++){
			int fa;cin >> fa;
			if(fa != 0){
				in[fa]++;
				in[i]++;
				ed[fa].push_back(i);
				ed[i].push_back(fa);
			}
		}
		get_mahash(j);
	}
	for(int i = 1;i <= n;i++){
		cout << mp[res_ans[i]] << '\n';
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

## 点分治

### 处理树上问题，将问题范围分为穿过树根的与子树内部

### 对于子树内部采用递归处理，对穿过树根具体题目具体分析

### 为了让递归层数尽量少，先要找重心

### 找重心：

### 重心为所有子树的最大值最小的节点

### 用$sz[i]$记录点$i$作为父亲子树时的子树数量，每次从父节点到$i$节点时初始化为$1$

### 用$max\_sz[i]$记录节点最大的子树数量，递归处理完所有子树后，用$all-sz[i]$更新$max\_sz[i]$即可得到$i$节点的最大子树节点数量，再更新$zx$即可

### 分治：找重心，计算经过重心的可能性，遍历以重心为根的子树，对那些子树找重心继续递归

### [模板点分治1](https://www.luogu.com.cn/problem/P3806)

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 2e4 + 10,M = 1e2 + 10;
const int INF = 1e7 + 10;

bool vis[N],able[M];
int n,m,zx,idx;
int zs[N],max_zs[N],ask[M];
int d[N],dis[N];
bool judge[INF];

struct edge{
	int v,w;
	edge* nex;
}ed[N*2];
int ptop;
edge* head[N];

void add(int u,int v,int w){
	ed[ptop].v = v;
	ed[ptop].w = w;
	ed[ptop].nex = head[u];
	head[u] = &ed[ptop];
	ptop++;
}

void get(){
	cin >> n >> m;
	for(int i = 1;i <= n - 1;i++){
		int u,v,w;cin >> u >> v >> w;
		add(u,v,w);
		add(v,u,w);
	}
	for(int i = 1;i <= m;i++)
		cin >> ask[i];
}

void get_zx(int u,int fa,int all){
	zs[u] = 1;
	max_zs[u] = 0;
	for(edge* p = head[u];p != NULL;p = p -> nex){
		int v = p -> v;
		if(vis[v] || v == fa) continue;
		get_zx(v,u,all);
		zs[u] += zs[v];
		max_zs[u] = max(max_zs[u],zs[v]);
	}
	max_zs[u] = max(all - zs[u],max_zs[u]); 
	if(max_zs[u] < max_zs[zx])
		zx = u;
}

void get_dis(int u,int fa,int len){
	d[++idx] = len;
	for(edge* p = head[u];p != NULL;p = p -> nex){
		int v = p -> v,w = p -> w;
		if(vis[v] || v == fa) continue;
		get_dis(v,u,len + w);
	}
}

void calc(int x){
	judge[0] = 1;
	stack<int> st;
	for(edge* p = head[x];p != NULL;p = p -> nex){//所有子树
		int v = p -> v,w = p -> w;
		if(vis[v]) continue;
		idx = 0;
		get_dis(v,x,w);//这个子树上的所有距离
		for(int i = 1;i <= idx;i++){
			for(int j = 1;j <= m;j++){
				if(ask[j] - d[i] >= 0 && ask[j] - d[i] < INF && judge[ask[j] - d[i]])
					able[j] = 1;
			}
		}
		for(int i = 1;i <= idx;i++)//处理完成，放入judge
		{
			if(d[i] <= INF){
				judge[d[i]] = 1;
				st.push(d[i]);
			}
		}
	}
	while(!st.empty()){//清空judge
		judge[st.top()] = 0;
		st.pop();
	}
}

void divid(int x){
	calc(x);//计算经过x的
	vis[x] = 1;
	for(edge* p = head[x];p != NULL;p = p -> nex){
		int v = p -> v;
		if(vis[v]) continue;
		zx = 0;
		get_zx(v,x,zs[v]);
		divid(zx);
	}
}

int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	get();
	zx = 0;
	max_zs[0] = INF;
	get_zx(1,0,n);
	divid(zx);
	for(int i = 1;i <= m;i++){
		if(able[i])
			cout << "AYE" << endl;
		else
			cout << "NAY" << endl;
	}
}
```

### [聪聪可可](https://www.luogu.com.cn/problem/P2634)

### 找所有3倍数的连接（包括本身，ab与ba不同）

### 正常分治，calc时记录0,1,2的数量即可

```cpp
#include<bits/stdc++.h>
using namespace std;

#define ll long long

const int N = 2e4 + 10,INF = 1e9 + 10;

ll gcd(ll a,ll b){
	if(b == 0) return a;
	else return gcd(b,a%b);
}

ll n;
bool vis[N];
int zs[N],max_zs[N],zx;

struct edge{
	int v,w;
	edge* nex;
}ed[N*2];
int ptop;
edge* head[N];
void add(int u,int v,int w){
	ed[ptop].v = v;
	ed[ptop].nex = head[u];
	ed[ptop].w = w;
	head[u] = &ed[ptop++];
}

void get_zx(int u,int fa,int all){
	zs[u] = 1;
	max_zs[u] = 0;
	for(edge* p = head[u];p != NULL;p = p -> nex){
		int v = p -> v;
		if(vis[v] || v == fa) continue;
		get_zx(v,u,all);
		zs[u] += zs[v];
		max_zs[u] = max(max_zs[u],zs[v]);
	}
	max_zs[u] = max(max_zs[u],all - zs[u]);
	if(max_zs[zx] > max_zs[u])
		zx = u;
}

ll num[3],d[3],ans;

void get_dis(int u,int fa,int len){
	d[len]++;
	for(edge* p = head[u];p != NULL;p = p -> nex){
		int v = p -> v,w = p -> w;
		if(vis[v] || v == fa) continue;
		get_dis(v,u,(len + w) % 3);
	}
}

void calc(int u){
	num[1] = num[2] = 0;
	num[0] = 1;
	for(edge* p = head[u];p != NULL;p = p -> nex){
		int v = p -> v,w = p -> w;
		if(vis[v]) continue;
		d[0] = d[1] = d[2] = 0;
		get_dis(v,u,w % 3);
		for(int i = 0;i < 3;i++)
			ans += num[(3 - i) % 3]*d[i];
		for(int i = 0;i < 3;i++)
			num[i] += d[i];
	}
}

void divid(int u){
	calc(u);
	vis[u] = 1;
	for(edge* p = head[u];p != NULL;p = p -> nex){
		int v = p -> v;
		if(vis[v]) continue;
		zx = 0;
		get_zx(v,u,zs[v]);
		divid(zx);
	}
}

void get(){
	cin >> n;
	for(int i = 1;i <= n - 1;i++){
		int u,v,w;cin >> u >> v >> w;
		w %= 3;
		add(u,v,w);
		add(v,u,w);
	}
}

int main(){
	max_zs[0] = INF;
	get();
	zx = 0;
	get_zx(1,0,n);
	divid(zx);
	ll all = n*n;
	ans = ans * 2 + n;
	ll gc = gcd(ans,all);
	cout << ans / gc << '/' << all / gc << endl;
}
```

### [P4178 Tree](https://www.luogu.com.cn/problem/P4178)

### 找树上距离小于k的点对

### 用树状数组维护

```cpp
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define lowbit(x) (x&-x)

const int N = 4e4 + 10;

int tr[N / 2];

int k,n;

int num_0 = 0;

void add(int x,int y){
	if(x == 0) num_0 += y;
	for(;x <= k;x += lowbit(x)) tr[x] += y;
}
int query(int x){
	if(x == 0) return num_0;
	int res = 0;
	for(;x;x -= lowbit(x)) res += tr[x];
	return res;
}

struct edge{
	int v,w,nex;
}ed[N*2];
int head[N];
int ptop = 0;
void add(int u,int v,int w){
	ptop++;
	ed[ptop].v = v;
	ed[ptop].w = w;
	ed[ptop].nex = head[u];
	head[u] = ptop;
}

int fa[N],sz[N];
bool vis[N];

int zx,misz;
void get_zx(int u,int dad,int all){
	if(u == dad){
		misz = all;
		zx = u;
	}
	sz[u] = 1;
	int x = 0;
	for(int p = head[u];p;p = ed[p].nex){
		if(ed[p].v == dad) continue;
		sz[u] += sz[ed[p].v];
		x = max(x,sz[ed[p].v]);
	}
	x = max(all - sz[u],x);
	if(x < misz) zx = u;
}

int idx;
int d[N];

void get_dis(int u,int fa,int len){
	d[++idx] = len;
	for(int p = head[u];p;p = ed[p].nex){
		if(ed[p].v == fa || vis[ed[p].v]) continue;
		get_dis(ed[p].v,u,len + ed[p].w);
	}
}

int ans = 0;

void calc(int x){
	stack<int> st;
	for(int p = head[x];p;p = ed[p].nex){
		if(vis[ed[p].v]) continue;
		idx = 0;
		get_dis(ed[p].v,x,ed[p].w);
		for(int i = 1;i <= idx;i++){
			if(d[i] <= k) ans += query(k - d[i]);
		}
		for(int i = 1;i <= idx;i++) if(d[i] <= k) add(d[i],1),st.push(d[i]),ans++;
	}
	while(!st.empty()){
		add(st.top(),-1);
		st.pop();
	}
}

void divid(int x){
	calc(x);
	vis[x] = 1;
	for(int p = head[x];p;p = ed[p].nex){
		if(vis[ed[p].v]) continue;
		zx = 0;
		get_zx(ed[p].v,ed[p].v,sz[ed[p].v]);
		divid(zx);
	}
}

int main(){
	IOS
	cin >> n;
	for(int i = 1;i < n;i++){
		int u,v,w;cin >> u >> v >> w;
		add(u,v,w);
		add(v,u,w);
	}
	cin >> k;
	get_zx(1,1,n);
	divid(zx);
	cout << ans << endl;
}
```

## 虚树

### 对于树上dp来说，如果每次询问所涉及的点较少，那么树上有许多点都是没有必要遍历的，那么我们就要建一颗新的只跟所涉及的点有关的树来进行树上dp即可大大降低复杂度

### 假设新树的点为关键点，那么我们就可以得出以下结论

* 询问所涉及的点肯定为关键点
* 关键点间的lca也为关键点
* 所建的新树与原树的祖先后代关系相同

### 关于复杂度证明，可得到虚树点的总数不超过所涉及的点的两倍（意会一下）

### 那么通过dfs序与lca来建虚树即可

### 步骤：

* 1.建原树，dfs得出倍增父节点，dfs序，dep
* 2.给所涉及的点根据dfs序来排序
* 用栈来建虚树
* 3.将点1先放入栈中
* 4.记录lc为要放入的点与栈顶元素的最近公共祖先
* 5.从栈顶开始删去深度比lc大的点，并连上相邻的点
* 6.如果lc不在栈内，加入栈中，并连上本次循环中最后一个踢掉的点
* 7.直到所有点放入虚树中
* 8.根据虚树dp
* 注意初始化、清空等等

### [P2495 [SDOI2011] 消耗战](https://www.luogu.com.cn/problem/P2495)

#### 题意：可以花费一定代价使得边中断，问使得点1无法到达任意一个标记点的最小代价

#### 思路：记录所有点向上跳$2^k$所经历的最小边，建立虚树，询问是就看虚树两点间的最小边来进行dp即可，用$dp[i]$代表这个点与下面被标记的点不相连的最小代价，那么如果子树那个点被标记，那么直接加该点到子树的最小边，否则加上子树点的dp与该点到子树的最小边的最小值

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 2.5e5 + 10,inf = 1e9 + 10;
const ll INF = 1e18 + 10;

struct point{
	int fa[20],dfs,dep;
	int w[20];//向上跳2^i的最短边
}p[N];

struct edge{//原树与虚树
	int w;
	int v,nex;
}ed[N*2],ved[N*2];
int head[N],vhead[N];
int ptop = 0,vptop = 0;
void add(int u,int v,int w){
	ptop++;
	ed[ptop].v = v;
	ed[ptop].w = w;
	ed[ptop].nex = head[u];
	head[u] = ptop;
}
void vadd(int u,int v){
	vptop++;
	ved[vptop].v = v;
	ved[vptop].nex = vhead[u];
	vhead[u] = vptop;
}

int _dfs = 0;
void dfs(int x,int dad){
	p[x].dep = p[dad].dep + 1;
	p[x].dfs = ++_dfs;
	p[x].fa[0] = dad;
	for(int i = 1;i <= 19;i++) p[x].fa[i] = p[p[x].fa[i - 1]].fa[i - 1];
	for(int i = 1;i <= 19;i++) p[x].w[i] = min(p[p[x].fa[i - 1]].w[i - 1],p[x].w[i - 1]);
	for(int por = head[x];por;por = ed[por].nex){
		if(ed[por].v == dad) continue;
		p[ed[por].v].w[0] = ed[por].w;
		dfs(ed[por].v,x);
	}
}

int lca(int x,int y){
	if(p[x].dep > p[y].dep) swap(x,y);
	for(int i = 19;i >= 0;i--){
		if(p[p[y].fa[i]].dep >= p[x].dep) y = p[y].fa[i];
	}
	if(x == y) return x;
	for(int i = 19;i >= 0;i--){
		if(p[x].fa[i] != p[y].fa[i]){
			x = p[x].fa[i];
			y = p[y].fa[i];
		}
	}
	return p[x].fa[0];
}

ll w(int x,int y){//x与y竖直链上的最短边
	if(p[x].dep > p[y].dep) swap(x,y);
	int ans = inf;
	for(int i = 19;i >= 0;i--){
		if(p[p[y].fa[i]].dep >= p[x].dep){
			ans = min(ans,p[y].w[i]);
			y = p[y].fa[i];
		}
	}
	return ans;
}

bool cmp(int i,int j){
	return p[i].dfs < p[j].dfs;
}

int h[N];

int k;

int st[N];

int pptop = 0;
int use[N];
void build_vtr(){//造虚树
	sort(h + 1,h + 1 + k,cmp);
	vptop = 0;
	pptop = ptop = 1;
	use[1] = st[1] = 1;
	for(int i = 1;i <= k;i++){
		int lc = lca(h[i],st[ptop]);
		while(p[lc].dep < p[st[ptop]].dep){
			ptop--;
			if(p[lc].dep < p[st[ptop]].dep)
				vadd(st[ptop],st[ptop + 1]);
			else
				vadd(lc,st[ptop + 1]);
		}
		if(lc != st[ptop])
			st[++ptop] = use[++pptop] = lc;
		use[++pptop] = st[++ptop] = h[i];
	}
	while(--ptop){
		vadd(st[ptop],st[ptop + 1]);
	}
}

ll dp[N];
bool ned[N];
void dfs_vtr(int x){
	for(int por = vhead[x];por;por = ved[por].nex){
		dfs_vtr(ved[por].v);
		if(ned[ved[por].v]){
			dp[x] = dp[x] + w(x,ved[por].v);
			ned[ved[por].v] = 0;
		}
		else
			dp[x] = dp[x] + min(dp[ved[por].v],w(x,ved[por].v));
	}
	vhead[x] = 0;
}

void init(int n){
	for(int i = 0;i <= 19;i++) p[1].w[i] = inf;
}

void test(){
	int n;cin >> n;
	init(n);
	for(int i = 1;i < n;i++){
		int u,v,w;cin >> u >> v >> w;
		add(u,v,w);
		add(v,u,w);
	}
	dfs(1,1);
	int m;cin >> m;
	for(int i = 1;i <= m;i++){
		cin >> k;
		for(int i = 1;i <= k;i++){
			cin >> h[i];
			ned[h[i]] = 1;//标记关键点
		}
		build_vtr();
		dfs_vtr(1);
		cout << dp[1] << endl;
		for(int i = 1;i <= pptop;i++) dp[use[i]] = 0;
	}
}

int main(){
//	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## 树上启发式合并

### 复杂度$O(nlog_2n)$考虑所有子树

### 继承大子树，取消小子树

### 步骤：

* 1.找重儿子，确认dfs序，记录sz，记录rk
* 2.遍历轻子，统计答案，但不继承cnt（dfs下去）
* 3.跑重子，统计答案，继承cnt（dfs下去）
* 4.遍历轻子继承cnt（dfs序遍历）

### [U41492 树上数颜色](https://www.luogu.com.cn/problem/U41492)

### 题意：问每个子树的颜色数

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 1e5 + 10;

vector<int> ed[N];

int rk[N];

struct point{
    int cnt,c,dfs,sz,son;
}p[N];

int total = 0;

void add(int x){
	cnt[c[x]]++;
	if(cnt[c[x]] == 1) total++;
}
void del(int x){
	cnt[c[x]]--;
	if(cnt[c[x]] == 0) total--;
}

int _dfs = 0;
void dfs0(int x,int dad){
	sz[x] = 1;
	ldfs[x] = ++_dfs;
	rk[_dfs] = x;
	int mai = 0;
	for(auto y : ed[x]){
		if(y == dad) continue;
		dfs0(y,x);
		if(sz[mai] < sz[y]) mai = y;
		sz[x] += sz[y];
	}
	son[x] = mai;
}

int ans[N];

void dfs1(int x,int dad,bool flag){
	for(auto y : ed[x]){
		if(y != son[x] && y != dad)
			dfs1(y,x,0);
	}
	if(son[x])
		dfs1(son[x],x,1);
	for(auto y : ed[x]){
		if(y != son[x] && y != dad){
			for(int i = ldfs[y];i <= ldfs[y] + sz[y] - 1;i++){
				add(rk[i]);
			}
		}
	}
	add(x);
	ans[x] = total;
	if(!flag){
		for(int i = ldfs[x];i <= ldfs[x] + sz[x] - 1;i++){
			del(rk[i]);
		}
	}
}

void test(){
	int n;cin >> n;
	for(int i = 1;i < n;i++){
		int u,v;cin >> u >> v;
		ed[u].push_back(v);
		ed[v].push_back(u);
	}
	for(int i = 1;i <= n;i++) cin >> c[i];
	dfs0(1,0);
	dfs1(1,0,0);
	int m;cin >> m;
	for(int i = 1;i <= m;i++){
		int x;cin >> x;
		cout << ans[x] << endl;
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

### [E. Lomsat gelral](https://codeforces.com/problemset/problem/600/E)

### 题意：问每个子树中没有比该种颜色节点数量更多的颜色数量

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 1e5 + 10;

vector<int> ed[N];

int cnt[N];
int num[N];//记录cnt为i的数量
ll sum[N];
int ma = 0;

void add(int x){
	if(cnt[x] == ma) ma++;
	sum[cnt[x]] -= x;
	num[cnt[x]]--;
	cnt[x]++;
	num[cnt[x]]++;
	sum[cnt[x]] += x;
}
void del(int x){
	if(cnt[x] == ma && num[cnt[x]] == 1) ma--;
	sum[cnt[x]] -= x;
	num[cnt[x]]--;
	cnt[x]--;
	num[cnt[x]]++;
	sum[cnt[x]] += x;
}

int rk[N];

struct point{
	int sz,dfs,son,c;
}p[N];

int _dfs = 0;

void dfs0(int x,int dad){
	p[x].dfs = ++_dfs;
	rk[_dfs] = x;
	p[x].sz = 1;
	int mai = 0;
	for(auto y : ed[x]){
		if(y == dad) continue;
		dfs0(y,x);
		p[x].sz += p[y].sz;
		if(p[mai].sz < p[y].sz) mai = y;
	}
	p[x].son = mai;
}

ll ans[N];

void dfs1(int x,int dad,bool f){
	for(auto y : ed[x]){
		if(y != dad && y != p[x].son)
			dfs1(y,x,0);
	}
	if(p[x].son)
		dfs1(p[x].son,x,1);
	for(auto y : ed[x]){
		if(y != dad && y != p[x].son){
			for(int j = p[y].dfs;j <= p[y].dfs + p[y].sz - 1;j++)
				add(p[rk[j]].c);
		}
	}
	add(p[x].c);
	ans[x] = sum[ma];
	if(!f){
		for(int i = p[x].dfs;i <= p[x].dfs + p[x].sz - 1;i++){
			del(p[rk[i]].c);
		}
	}
}

void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++) cin >> p[i].c;
	for(int i = 1;i < n;i++){
		int x,y;cin >> x >> y;
		ed[x].push_back(y);
		ed[y].push_back(x);
	}
	dfs0(1,0);
	dfs1(1,0,0);
	for(int i = 1;i <= n;i++)
		cout << ans[i] << ' ';
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## 支配树

### 定义：

* 对于一个单一起点有向图而言，如果去掉了点x，就无法到达y，那么称x为y的支配点

* 在y的支配点集中，如果x被除自己以外的点支配，那么称x为y的最近支配点，记作$x=idom[y]$
* 构造树$e[idom[i],i](i\neq root)$称为支配树

### 有向无环图(DAG)支配树建立方法：

* 一个点的$idom$为能到它的所有点的共同最近公共祖先，因此用拓扑排序让点x遍历时所有能到达该点x的y均遍历过
* 建图，记录每个点的入度
* 拓扑排序
* 遍历u能到的所有v
* 如果v是第一次访问，那么令它的$idom[v]$为$u$，否则$idom[v]=lca(idom[v],u)$
* 如果v的入度变成0，给支配树加入边$add(idom[v],v)$，记录$fa[v] [0]=idom[v]$，维护$lca$，将$v$加入队列中
* 复杂度：$O(nlog_2n+mlog_2m)$
* 例题[P2597 [ZJOI2012] 灾难](https://www.luogu.com.cn/problem/P2597)

```cpp
#include<bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);

const int N = 65535,M = 1e7 + 10;

int fa[N][20],n,in[N],dep[N],idom[N],ans[N];
bool vis[N];

struct edge{
	int v,to;
}ed[M],ne[M];
int head[N],nhead[N];
int ptop = 0;
void add(int u,int v){
	ptop++;
	ed[ptop].v = v;
	ed[ptop].to = head[u];
	head[u] = ptop;
}

void addne(int u,int v){
	ptop++;
	ne[ptop].v = v;
	ne[ptop].to = nhead[u];
	nhead[u] = ptop;
}

int lca(int x,int y){
	if(dep[x] < dep[y]) swap(x,y);
	for(int i = 19;i >= 0;i--) if(dep[fa[x][i]] >= dep[y]) x = fa[x][i];
	if(x == y) return x;
	for(int i = 19;i >= 0;i--) if(fa[x][i] != fa[y][i]) x = fa[x][i],y = fa[y][i];
	return fa[x][0];
}

void tp(){
	ptop = 0;
	queue<int> qu;
	for(int i = 1;i <= n;i++){
		if(in[i] == 0){
			qu.push(i);
			idom[i] = 0;
			fa[i][0] = 0;//超级源点
			addne(0,i);
			dep[i] = 1;
		}	
	}
	while(!qu.empty()){
		int u = qu.front();qu.pop();
		for(int p = head[u];p;p = ed[p].to){
			int v = ed[p].v;
			in[v]--;
			if(!vis[v])
				idom[v] = u;
			vis[v] = 1;
			idom[v] = lca(idom[v],u);
			if(in[v] == 0){
				addne(idom[v],v);
				fa[v][0] = idom[v];
				for(int i = 1;i < 20;i++)
					fa[v][i] = fa[fa[v][i - 1]][i - 1];
				dep[v] = dep[idom[v]] + 1;
				qu.push(v);
			}
		}
	}
}



void dfs(int x){
	ans[x] = 1;
	for(int p = nhead[x];p;p = ne[p].to){
		int v = ne[p].v;
		dfs(v);
		ans[x] += ans[v];
	}
}

int main(){
	IOS
	cin >> n;
	for(int i = 1;i <= n;i++){
		int x;cin >> x;
		while(x != 0){
			add(x,i);
			in[i]++;
			cin >> x;
		}
	}
	tp();
	dfs(0);
	for(int i = 1;i <= n;i++)
		cout << ans[i] - 1 << endl;
}
```

### 有向有环图的支配树：

* 求dfs序，同时构造dfs树，记录每个节点dfs时的父节点
* 求sdom，从dfs大的开始，除原点，枚举反向边，如果dfs序小采用，否则采用祖先(mn)的sdom的dfs小的，用带权并查集维护，注意维护时要先find(fa[x])再维护
* 根据dfs树与sdom构造的DAG图求支配树
* 统计答案
* [P5180 【模板】支配树](https://www.luogu.com.cn/problem/P5180)

```cpp
//#pragma GCC optimize(2)
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define debug(x) cout << #x << '=' << x << endl;

inline int Min(int x,int y){return x > y ? y : x;}
inline int Max(int x,int y){return x > y ? x : y;}

const int N = 3e5 + 10,M = 4e5 + 10;

int dfs[N],sdom[N],idom[N],dep[N],fa[N][20],rdfs[N],in[N],dad[N],anc[N],mn[N],ans[N];
int n,m;
bool vis[N];
struct {
	int v,to;
}e[3][M];
int ptop[3];
int h[3][N];

void init(){
	for(int i = 1;i <= n;i++)
		sdom[i] = dad[i] = mn[i] = i;
}

void add(int u,int v,int t){
	ptop[t]++;
	e[t][ptop[t]].to = h[t][u];
	e[t][ptop[t]].v = v;
	h[t][u] = ptop[t];
}

int _dfs;
void dfs1(int x){//求出dfs序
	dfs[x] = ++_dfs;
	rdfs[_dfs] = x;
	for(int p = h[0][x];p;p = e[0][p].to){
		int y = e[0][p].v;
		if(!dfs[y]){
			anc[y] = x;
			add(x,y,2);
			in[y]++;
			dfs1(y);
		}
	}
}

int find(int x){//带权并查集
	if(x == dad[x]) return x;
	int tem = dad[x];dad[x] = find(dad[x]);
	if(dfs[sdom[mn[x]]] > dfs[sdom[mn[tem]]]) mn[x] = mn[tem];
	return dad[x];
}

int lca(int x,int y){
	if(dep[x] < dep[y]) swap(x,y);
	for(int i = 19;i >= 0;i--) if(dep[fa[x][i]] >= dep[y]) x = fa[x][i];
	if(x == y) return x;
	for(int i = 19;i >= 0;i--) if(fa[x][i] != fa[y][i]) x = fa[x][i],y = fa[y][i];
	return fa[x][0];
}

void getsdom(){
	for(int i = _dfs;i >= 2;i--){
		int x = rdfs[i],res = n;
		for(int p = h[1][x];p;p = e[1][p].to){
			int y = e[1][p].v;
			if(!dfs[y]) continue;
			find(y);
			if(dfs[y] < dfs[x]) res = Min(res,dfs[y]);
			else res = Min(res,dfs[sdom[mn[y]]]);
		}
		sdom[x] = rdfs[res];
		add(sdom[x],x,2);
		in[x]++;
		dad[x] = anc[x];
	}
}

void tp(){
	memset(h[0],0,sizeof(h[0]));
	ptop[0] = 0;
	queue<int> qu;
	for(int i = 1;i <= n;i++){
		if(in[i] == 0){
			idom[i] = 0;
			dep[i] = 1;
			fa[i][0] = 0;
			qu.push(i);
			add(0,i,0);
		}
	}
	while(!qu.empty()){
		int u = qu.front();qu.pop();
		for(int p = h[2][u];p;p = e[2][p].to){
			int v = e[2][p].v;
			if(!vis[v]) idom[v] = u;
			idom[v] = lca(idom[v],u);
			vis[v] = 1;
			in[v]--;
			if(in[v] == 0){
				qu.push(v);
				fa[v][0] = idom[v];
				for(int i = 1;i < 20;i++) fa[v][i] = fa[fa[v][i - 1]][i - 1];
				dep[v] = dep[idom[v]] + 1;
				add(idom[v],v,0);
			}
		}
	}
}

void dfs2(int x){
	ans[x] = 1;
	for(int p = h[0][x];p;p = e[0][p].to){
		int y = e[0][p].v;
		dfs2(y);
		ans[x] += ans[y];
	}
}

void Lengauer_Tarjan(){
	init();
	dfs1(1);
	getsdom();
	tp();
	dfs2(0);
}

int main(){
	IOS
	cin >> n >> m;
//	for(int i = 1;i <= n;i++){
//		int x;cin >> x;
//		while(x){
//			add(x,i,2);
//			in[i]++;
//			add(i,x,1);
//			cin >> x;
//		}
//	}
	for(int i = 1,i <= m;i++){
		int u,v;cin >> u >> v;
		add(u,v,0);
		add(v,u,1);
	}
	Lengauer_Tarjan();
	for(int i = 1;i <= n;i++)
		cout << ans[i] << ' ';
}
```

## 换根

### 用于解决树上假设每个点均为根的问题

### 思路：跑两遍dfs，第一遍假设一个节点为根，第二遍根据上一遍跑的尝试计算父节点的贡献并得出所有节点为根的情况

### 第二遍dfs一般思路为：如果为根节点，那么记录，否则将父节点纳入考虑中

### 在遍历所有子节点前记录现在的数据，要遍历那个子节点就取消该子节点的数据对该数据的影响再继续dfs，然后还原到原来的数据

### [P3478 [POI2008] STA-Station](https://www.luogu.com.cn/problem/P3478)

#### 题意：以一个节点为根使得所有节点深度之和最大

#### 思路：维护以该节点为子节点的节点数量与总深度

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);

const int N = 1e6 + 10;

vector<int> ed[N];

ll cnt[N],sumdep[N];

void dfs1(int son,int dad){
	cnt[son] = 1;
	for(auto v:ed[son]){
		if(v == dad) continue;
		dfs1(v,son);
		cnt[son] += cnt[v];
		sumdep[son] += cnt[v] + sumdep[v];
	}
}

pair<ll,ll> ans;

void dfs2(int son,int dad){
	if(son == 1){
		ans = {sumdep[1],1};
	}else{
		sumdep[son] += cnt[dad] + sumdep[dad];
		cnt[son] += cnt[dad];
		if(sumdep[son] > ans.first)
			ans = {sumdep[son],son};
	}
	ll rescnt = cnt[son],ressum = sumdep[son];
	for(auto v:ed[son]){
		if(v == dad) continue;
		cnt[son] -= cnt[v];
		sumdep[son] -= cnt[v] + sumdep[v];
		dfs2(v,son);
		cnt[son] = rescnt;
		sumdep[son] = ressum;
	}
}

int main(){
	IOS
	int n;cin >> n;
	for(int i = 1;i < n;i++){
		int u,v;cin >> u >> v;
		ed[u].push_back(v);
		ed[v].push_back(u);
	}
	dfs1(1,1);
	dfs2(1,1);
	cout << ans.second << endl;
}
```

## [P2986 [USACO10MAR] Great Cow Gathering G](https://www.luogu.com.cn/problem/P2986)

#### 题意：n个牛棚，n-1条路，每个牛棚有$C_i$只牛，不方便的程度为$\sum _{i-1}^nC_idis_i$，$dis_i$为根到该点的距离，问最小的不方便值为多少

#### 思路：维护牛的数量，总不方便值

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);

const int N = 1e5 + 10;
ll cnt[N],sum[N],c[N];

vector<pair<int,int>> ed[N];

void dfs1(int son,int dad){
	cnt[son] += c[son];
	for(auto pa:ed[son]){
		int v = pa.first,dis = pa.second;
		if(v == dad) continue;
		dfs1(v,son);
		cnt[son] += cnt[v];
		sum[son] += sum[v] + cnt[v] * dis;
	}
}

ll ans;

void dfs2(int son,int dad,ll dis){
	if(son == 1){
		ans = sum[son];
	}else{
		cnt[son] += cnt[dad];
		sum[son] += cnt[dad] * dis + sum[dad];
		ans = min(sum[son],ans);
	}
	ll rescnt = cnt[son],ressum = sum[son];
	for(auto pa:ed[son]){
		int v = pa.first,_dis = pa.second;
		if(v == dad) continue;
		cnt[son] -= cnt[v];
		sum[son] -= cnt[v] * _dis + sum[v];
		dfs2(v,son,_dis);
		cnt[son] = rescnt;
		sum[son] = ressum;
	}
}

int main(){
	IOS
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
		cin >> c[i];
	for(int i = 1;i < n;i++){
		int a,b,l;cin >> a >> b >> l;
		ed[a].push_back({b,l});
		ed[b].push_back({a,l});
	}
	dfs1(1,1);
	dfs2(1,1,0);
	cout << ans << endl;
}
```

### [E. Tree Painting](https://codeforces.com/problemset/problem/1187/E)

#### 题意：给定一棵n个点的树 初始全是白点

#### 要求你做n步操作，每一次选定一个与一个黑点相隔一条边的白点，将它染成黑点，然后获得该白点被染色前所在的白色联通块大小的权值。

#### 第一次操作可以任意选点。

#### 求可获得的最大权值

#### 思路：维护数量与答案

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);

const int N = 2e5 + 10;
ll cnt[N],sum[N];
vector<int> ed[N];

ll ans;
void dfs1(int son,int dad){
	cnt[son] = 1;
	for(auto v:ed[son]){
		if(v == dad) continue;
		dfs1(v,son);
		cnt[son] += cnt[v];
		sum[son] += cnt[v] + sum[v];
	}
}

void dfs2(int son,int dad){
	if(son == 1){
		ans = sum[1];
	}else{
		sum[son] += cnt[dad] + sum[dad];
		cnt[son] += cnt[dad];
		ans = max(sum[son],ans);
	}
	ll ressum = sum[son],rescnt = cnt[son];
	for(auto v:ed[son]){
		if(v == dad) continue;
		sum[son] -= sum[v] + cnt[v];
		cnt[son] -= cnt[v];
		dfs2(v,son);
		sum[son] = ressum;
		cnt[son] = rescnt;
	}
}

int main(){
	IOS
	int n;cin >> n;
	for(int i = 1;i < n;i++){
		int u,v;cin >> u >> v;
		ed[u].push_back(v);
		ed[v].push_back(u);
	}
	dfs1(1,1);
	dfs2(1,1);
	cout << ans + n << endl;
}
```

## 二叉树的读取顺序

### 前序：父节点-->左节点-->右节点

### 中序：左节点-->父节点-->右节点

### 后序：左节点-->右节点-->父节点

### 如何通过两种排序得出第三种排序

* 分治，先找父节点(只可能为一个)，通过这个判断左节点和右节点
* 已知中序和后续，求前序

```c++
#include<bits/stdc++.h>
using namespace std;
string mid,back;
void f(int mid_l,int mid_r,int back_l,int back_r)
{
    int i;
    if(mid_l>mid_r)
        return;
    for(i = 0;i <= 8;i++)
        if(mid[mid_l+i] == back[back_r])
            break;
    cout << mid[mid_l+i];
    f(mid_l,mid_l+i-1,back_l,back_l+i-1);
    f(mid_l+i+1,mid_r,back_l+i,back_r-1);
}
int main()
{
	cin >> mid >> back;
	f(0,mid.size() - 1,0,mid.size() - 1);
}
```

* 已知先序和后序，求中序

```c++
    void f(int x_l,int x_r,int y_l,int y_r,vector<int>& pre,vector<int>& suf,vector<int>& output)
        {
            if(x_l > x_r)
                return;
            else if(x_l==x_r)
                output.push_back(pre[x_l]);
            else
            {
                int num = 1;
                while(pre[x_l+1] != suf[y_l+num-1])
                    num++;
                f(x_l+1,x_l+num,y_l,y_l+num-1,pre,suf,output);
                output.push_back(pre[x_l]);
                f(x_l+num+1,x_r,y_l+num,y_r-1,pre,suf,output);
            }
        }
    vector<int> solve(int n, vector<int>& pre, vector<int>& suf) {
        // write code here
        vector<int> output;
        f(0,n-1,0,n-1,pre,suf,output);
        return output;
    }
```

* 对于已知中序来说，知道父节点就可以知道左右节点的数量，然后进行递归
* 对于不知道中序来说，要通过子节点的父节点位置在最前面和最后面进行判断

## 最小生成树

### 定义

* 边权和最小的生成树

### Kruskal 算法

* 让边从小到大排序，如果不在同一集合，就加入

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 5e3 + 10,MAXM = 2e5 + 10;
int n,m;
int a[MAXN];
int find(int x)
{
	if(a[x] == x) return x;
	else return a[x] = find(a[x]);
}
struct edge{
	int u,v,val;
}ed[MAXM];
bool cmp(edge a,edge b)
{
	return a.val < b.val;
}
int sum;
void pre()
{
	for(int i = 1;i <= n;i++)
	a[i] = i;
}
void Kruskal()
{
	pre();
	sum = 0;
	for(int i = 1;i <= m;i++)
	{
		if(find(ed[i].u) != find(ed[i].v))
		{
			sum += ed[i].val;
			a[find(ed[i].u)] = find(ed[i].v);
		}
	}
}
int main()
{
	cin >> n >> m;
	for(int i = 1;i <= m;i++)
	{
		cin >> ed[i].u >> ed[i].v >> ed[i].val;
	}
	sort(ed + 1,ed + 1 + m,cmp);
	Kruskal();
	bool flag = true;
	for(int i = 2;i <= n;i++)
	if(find(i) != find(i - 1))
	flag = false;
	if(flag)
	cout << sum;
	else
	cout << "orz";
}
```

### Prim算法

* 搜索，对查到的边排序，取最小的边，加入

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 5e3 + 10,MAXM = 2e5 + 10;
int n,m;
struct edge{
	int v,val;
	edge* nex;
}ed[MAXM*2];
edge* head[MAXN];
int ptop = 0;
void add_edge(int u,int v,int val)
{
	ed[ptop].v = v,ed[ptop].val = val;
	ed[ptop].nex = head[u];
	head[u] = &ed[ptop];
	ptop++;
}
int sum = 0;
struct cmp{
	bool operator()(const pair<int,int>& a,const pair<int,int>& b)const{
		return a.second > b.second;
	}
};
priority_queue<pair<int,int>,vector<pair<int,int>>,cmp> pq;
bool use[MAXN];
int num = 0;
void Prim()
{
	pq.push({1,0});//距离点1距离为0
	while(!pq.empty() && num < n)
	{
		int x = pq.top().first,val = pq.top().second;
		pq.pop();
		if(use[x]) continue;
		use[x] = 1;//标记
		num++;
		sum += val;
		edge* p = head[x];
		while(p != NULL)
		{
			int y = p -> v,val = p -> val;
			pq.push({y,val});
			p = p -> nex;
		}
	}
}
int main()
{
	cin >> n >> m;
	for(int i = 1;i <= m;i++)
	{
		int x,y,val;cin >> x >> y >> val;
		add_edge(x,y,val);
		add_edge(y,x,val);
	}
	Prim();
	if(num != n)
	cout << "orz";
	else
	cout << sum;
}
```

## 次小生成树

### 定义：边权之和大于最小生成树边权之和的生成树中最小的一个

### 思路：枚举所有未连接的边连上，那么一定会出现一个环，再去掉环上最大的边（如果与新加的边等大就要去次小边），这个最小值就为次小生成树的值

### 朴素求法：先用kruskal求出最小生成树，然后从每个点开始找到其他点的最大边与次大边，然后枚举所有边

### 复杂度$O(n^2+m)$

### LAC优化：就是只讨论一棵树，对每个点记录向上跳$2^i$经过的最大边与次大边

### 注意LAC的i=0时的特殊处理

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long
#define PII pair<int,int>
#define fi first
#define se second
const int N = 1e5 + 10,M = 3e5 + 10;
int n,m,sum;

vector<PII> ed[N];//新图
struct edge{int u,v,len;
bool operator<(const edge &x)const{return len < x.len;}};
edge _ed[M];//旧图
int pre[N];//并查集
bool use[M];//边使用
void init(){for(int i = 1;i <= n;i++) pre[i] = i;}//并查集初始化
int find(int x){if(pre[x] == x) return pre[x];else return pre[x] = find(pre[x]);}
void kruskal(){
	init();
	sort(_ed + 1,_ed + 1 + m);
	for(int i = 1;i <= m;i++){
		if(find(_ed[i].u) != find(_ed[i].v)){
			use[i] = 1;
			sum += _ed[i].len;
			pre[find(_ed[i].u)] = find(_ed[i].v);
			ed[_ed[i].u].push_back({_ed[i].v,_ed[i].len});
			ed[_ed[i].v].push_back({_ed[i].u,_ed[i].len});
		}
	}
}

int dep[N],fa[N][21] = {0};
PII ma[N][21];
void bfs(){
	queue<int> qu;
	qu.push(1);
	dep[1] = 1;
	while(!qu.empty()){
		int u = qu.front();
		qu.pop();
		for(auto e:ed[u]){
			int v = e.fi,len = e.se;
			if(dep[v]) continue;
			qu.push(v);
			dep[v] = dep[u] + 1;
			fa[v][0] = u;
			ma[v][0].fi = len,ma[v][0].se = -1;
			for(int i = 1;i <= 20;i++){
				int faa = fa[v][i - 1];
				fa[v][i] = fa[faa][i - 1];
				ma[v][i].fi = max(ma[v][i-1].fi,ma[faa][i-1].fi);
				if(ma[v][i-1].fi == ma[faa][i-1].fi) ma[v][i].se = max(ma[v][i-1].se,ma[faa][i-1].se);
				else ma[v][i].se = min(ma[v][i-1].fi,ma[faa][i-1].fi);
			}
		}
	}
}

int d[N];
int lca(int x,int y,int w){
	int idx = 0;
	if(dep[x] < dep[y]) swap(x,y);//让x深度更深
	for(int i = 20;i >= 0;i--){//到同一深度
		if(dep[fa[x][i]] >= dep[y]){
			d[++idx] = ma[x][i].fi;
			d[++idx] = ma[x][i].se;
			x = fa[x][i];
		}
	}
	if(x != y){
		for(int i = 20;i >= 0;i--){
			if(fa[x][i] != fa[y][i] || i == 0){
				d[++idx] = ma[x][i].fi;
				d[++idx] = ma[x][i].se;
				d[++idx] = ma[y][i].fi;
				d[++idx] = ma[y][i].se;
				x = fa[x][i];
				y = fa[y][i];
				if(i == 0 && x != y)
				    i++;
			}
		}
	}
	int ma = -1;
	for(int i = 1;i <= idx;i++)
		if(d[i] != w && d[i] > ma) ma = d[i];
	if(ma == -1)
		return 1e18;
	return - ma + w;
}

signed main(){
	cin >> n >> m;
	for(int i = 1;i <= m;i++){
		int x,y,z;cin >> x >> y >> z;
		_ed[i] = {x,y,z};
	}
	kruskal();
	bfs();
	int ans = 1e18;
	for(int i = 1;i <= m;i++){
		if(use[i]) continue;
		ans = min(ans,sum + lca(_ed[i].u,_ed[i].v,_ed[i].len));
	}
	cout << ans << endl;
}
```

## 基环树

### 简单无向图有n个点n-1条边，那么它们会连成一条直线

### n个点n条边，相对多一条边，有且仅有一个环

### 可以利用拓扑排序找这个环

### 例题：[F - Well-defined Path Queries on a Namori](https://atcoder.jp/contests/abc266/tasks/abc266_f?lang=en)

#### 题意：基环树上判断两点之间是否路径唯一

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e5 + 10,MAXM = 2e5 + 10;
int n,q;
struct edge{
	int v;
	edge* nex;
}ed[MAXM * 2];
edge* head[MAXN];
int ptop = 0;
void add(int u,int v)
{
	ed[ptop].v = v;
	ed[ptop].nex = head[u];
	head[u] = &ed[ptop];
	ptop++;
}
int du[MAXN];
bool unable[MAXN];
int co = 1;//颜色
int color[MAXN];
void tp()//拓扑排序,找环
{
	queue<int> qu;
	for(int i = 1;i <= n;i++)
		if(du[i] == 1)
			qu.push(i);
	while(!qu.empty())
	{
		int u = qu.front();qu.pop();
		unable[u] = 1;
		edge* p = head[u];
		while(p != NULL)
		{
			int v = p -> v;
			du[v]--;
			if(du[v] == 1)
				qu.push(v);
			p = p -> nex;
		}
	}
}
bool use[MAXN];
void dfs(int c,int p)//染色
{
	if(use[p] || !unable[p])
		return;
	use[p] = 1;
	color[p] = c;
	edge* pp = head[p];
	while(pp != NULL)
	{
		int v = pp -> v;
		dfs(c,v);
		pp = pp -> nex;
	}
}
int main()
{
	cin >> n;
	for(int i = 1;i <= n;i++)
	{
		int u,v;cin >> u >> v;
		du[u]++,du[v]++;
		add(u,v);
		add(v,u);
	}
	tp();
	for(int i = 1;i <= n;i++)
	{
		if(!unable[i])
		{
			color[i] = co;
			edge* p = head[i];
			while(p != NULL)
			{
				int v = p -> v;
				dfs(co,v);
				p = p -> nex;
			}
			co++;
		}
	}
	cin >> q;
	while(q--)
	{
		int u,v;cin >> u >> v;
		if(color[u] == color[v])
			cout << "Yes" << endl;
		else
			cout << "No" << endl;
	}
}
```

## 差分约束

### 对于形如：

$$
\begin{cases}
x_{c_{1}}-x_{c_{1}'}\leq y_1 \\
x_{c_{2}}-x_{c_{2}'}\leq y_2 \\
...\\
x_{c_{n}}-x_{c_{n}'}\leq y_n \\
\end{cases}
$$

### 对于单个式子而言$x_{c_{1}}-x_{c_{1}'}\leq y_1$，可转换为$x_{c_{1}}\leq x_{c_{1}'}+y_1$，而在图论中的点与点的距离也可以表示成这种样子，也就是点$c_1'$到$c_1$有一条权值为$y_1$的单向边

### 建图，用SPFA判断是否有负环即可判断是否有解，并且最后的dis就是一个可行解

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define fo(i,n) for(int i = 1;i <= n;i++)
#define debug(i,x) cout << "case" << (i) << ":" << x << endl;
#define lson(x) (x << 1)
#define rson(x) (x << 1 | 1)
const int inf = 1e9;
const int MAXN = 5e3 + 10;
ll num[MAXN],dis[MAXN];
bool use[MAXN];
vector<pair<int,int>> ed[MAXN];
int n,m;
void init(){
	memset(dis,1e9,sizeof(dis));
}
bool spfa(){
	dis[1] = 0;
	queue<int> qu;
	fo(i,n){
		qu.push(i);
		use[i] = 1;
		num[i]++;
	}
	while(!qu.empty()){
		int u = qu.front();
		use[u] = 0;
		qu.pop();
		for(auto pa:ed[u]){
			int v = pa.first,ddis = pa.second;
			if(ddis + dis[u] < dis[v]){
				dis[v] = ddis + dis[u];
				if(!use[v]){
					num[v]++;
					use[v] = 1;
					qu.push(v);
					if(num[u] >= n)//出现负环
						return 0;
				}
			}
		}
	}
	return 1;
}
int main()
{
	cin >> n >> m;
	init();
	for(int i = 1;i <= m;i++){
		int u,v,dis;cin >> u >> v >> dis;
		ed[v].push_back({u,dis});
	}
	bool f = 1;
	if(!spfa()){
		f = 0;
	}
	if(f)
		fo(i,n)
			cout << dis[i] << ' ';
	else
		cout << "NO" << endl;
}
```

## 负环

* SPFA算法求最短路，会在负环死循环，根据正常图的点最多经历n - 1轮松弛可知只要一个点经过了多余n - 1轮就可以说明这个图有负环

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2e3 + 10,MAXM = 3e3 + 10,INF = 0x3f3f3f3f;
struct edge{
	int v,val;
	edge* nex;
}ed[MAXM * 2];
edge* head[MAXN];
int ptop = 0;
void add_edge(int u,int v,int val)
{
	ed[ptop].v = v,ed[ptop].val = val;
	ed[ptop].nex = head[u];
	head[u] = &ed[ptop];
	ptop++;
}
int num[MAXN];
int dis[MAXN];
int n,m;
bool spfa()
{
	queue<int> qu;
	qu.push(1);
	dis[1] = 0;
	while(!qu.empty())
	{
		int x = qu.front();
		qu.pop();
		if(num[x] >= n)
		return true;
		num[x]++;
		edge* p = head[x];
		while(p != NULL)
		{
			int y = p -> v,val = p -> val;
			if(val + dis[x] < dis[y])
			{
				dis[y] = val + dis[x];
				qu.push(y);
			}
			p = p -> nex;
		}
	}
	return false;
}
void pre()
{
	for(int i = 1;i <= n;i++)
	{
		head[i] = NULL;
		num[i] = 0;
		dis[i] = INF;
	}
	ptop = 0;
}
int main()
{
	int t;cin >> t;
	while(t--)
	{
		cin >> n >> m;
		pre();
		for(int i = 1;i <= m;i++)
		{
			int u,v,val;cin >> u >> v >> val;
			if(val >= 0)
			add_edge(v,u,val);
			add_edge(u,v,val);
		}
		if(spfa())
		cout << "YES" << endl;
		else
		cout << "NO" << endl;
	}
}
```

## 最大流问题

### 给出起点、终点、边最大能传递的值，问从起点到终点最多能传多少

* 阻塞流：不能再给终点增加值的流（最大流就是一种阻塞流）
* 传统算法：新建一个剩余量的图，找路径、减去最小值、删路径，重复直到为阻塞流（不一定为最优解）

### Ford-Fulkerson算法(复杂度O(fm)，没什么用，过不了模板题)

### 相对于传统算法，它加入了一步建立反向边的步骤使得它可以"反悔"

### 1.找路径

### 2.减去最小值并建立反向的最小值边

### 3.重复

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
ll ans = 0;
const int MAXN = 201,MAXM = 5e3 + 10;
struct edge{
	ll v,val,i;
	edge* nex;
}ed[MAXM*2];
int ptop;
edge* head[MAXN];
void add(int u,int v,ll val){
	ed[ptop].i = ptop;
	ed[ptop].v = v;
	ed[ptop].nex = head[u];
	ed[ptop].val = val;
	head[u] = &ed[ptop];
	ptop++;
	ed[ptop].v = u;
	ed[ptop].i = ptop;
	ed[ptop].nex = head[v];
	ed[ptop].val = 0;
	head[v] = &ed[ptop];
	ptop++;
}
int s,t;
int ask[MAXM*2],use[MAXN];
bool find(int u){
	if(u == t)
		return 1;
	edge* p = head[u];
	for(edge* p = head[u];p != NULL;p = p -> nex){
		int v = p -> v;
		if(use[v] || p -> val == 0)
			continue;
		else{
			use[v] = 1;
			ask[++ptop] = p -> i;
			if(find(v))	return 1;
			ptop--;
		}	
	}
	return 0;
}
bool bfs(){
	ptop = 0;
	memset(use,0,sizeof(use));
	use[s] = 1;
	if(!find(s))
		return 0;
	ll mi = (ll)1 << 50;
	for(int i = 1;i <= ptop;i++){
		mi = min(mi,ed[ask[i]].val);
	}
	for(int i = 1;i <= ptop;i++){
		ed[ask[i]].val -= mi;
		ed[ask[i]^1].val += mi;
	}
	ans += mi;
	return 1;
}
int main()
{
	int n,m;cin >> n >> m >> s >> t;
	for(int i = 1;i <= m;i++){
		ll u,v,val;cin >> u >> v >> val;
		add(u,v,val);
	}
	while(bfs());
	cout << ans;
}
```

### Edmonds-Karp算法(用最短路找边，时间复杂度为O($m^2n$))(SAP算法)

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
ll ans = 0;
const int MAXN = 201,MAXM = 5e3 + 10;
struct edge{
	ll u,v,val,i;
	edge* nex;
}ed[MAXM*2];
int ptop = 2;
edge* head[MAXN];
void add(int u,int v,ll val){
	ed[ptop].i = ptop;
	ed[ptop].v = v;
	ed[ptop].u = u;
	ed[ptop].nex = head[u];
	ed[ptop].val = val;
	head[u] = &ed[ptop];
	ptop++;
	ed[ptop].v = u;
	ed[ptop].u = v;
	ed[ptop].i = ptop;
	ed[ptop].nex = head[v];
	ed[ptop].val = 0;
	head[v] = &ed[ptop];
	ptop++;
}
int s,t;
int pre[MAXN*2],use[MAXN];
bool find(){
	queue<int> qu;qu.push(s);
	while(!qu.empty()){
		int x = qu.front();
		qu.pop();
		for(edge* p = head[x];p != NULL;p = p -> nex){
			int v = p -> v;
			if(use[v] || p -> val == 0)
				continue;
			pre[v] = p -> i;
			use[v] = 1;
			if(v == t)
				return 1;
			qu.push(v);
		}
	}
	return 0;
}
bool bfs(){
	ptop = 0;
	memset(use,0,sizeof(use));
	use[s] = 1;
	if(!find())
		return 0;
	ll mi = (ll)1 << 50;
	for(int i = pre[t];i != 0;i = pre[ed[i].u]){
		mi = min(mi,ed[i].val);
	}
	for(int i = pre[t];i != 0;i = pre[ed[i].u]){
		ed[i].val -= mi;
		ed[i^1].val += mi;
	}
	ans += mi;
	return 1;
}
int main()
{
	int n,m;cin >> n >> m >> s >> t;
	for(int i = 1;i <= m;i++){
		ll u,v,val;cin >> u >> v >> val;
		add(u,v,val);
	}
	while(bfs());
	cout << ans;
}
```

### Dinic算法

* level-up图：就是分层图，起点开始，终点结束，中间分层
* Edmonds-Karp算法每次只解决一条边，效率较低，Dinic算法通过level-up图算一个图的阻塞流，效率更高
* BFS找level-up图思路：记录深度，最后看终点有没有被标记深度即可
* DFS找阻塞流：每次只找深度加1的点，深入DFS并传递边的最小值，返回分配的流，让这条边减去返回值，继续找，直到找完或者没有流可以继续了

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define inf 0x3f3f3f3f
const int MAXN = 201,MAXM = 5e3 + 10;
struct edge{
	int v;
	ll w;
	edge* nex;
	int i;
}ed[MAXM*2];
edge* head[MAXN];
int ptop = 0;
void add(int u,int v,ll w){
	ed[ptop].w = w;
	ed[ptop].v = v;
	ed[ptop].nex = head[u];
	head[u] = &ed[ptop];
	ed[ptop].i = ptop;
	ptop++;
	ed[ptop].w = 0;
	ed[ptop].v = u;
	ed[ptop].nex = head[v];
	head[v] = &ed[ptop];//构造反向边
	ed[ptop].i = ptop;
	ptop++;
}
int n,m,s,t;
int d[MAXN];//记录深度
bool bfs(){//构造levelup图
	queue<int> qu;
	memset(d,-1,sizeof(d));
	d[s] = 0;
	qu.push(s);//放入起点
	while(!qu.empty()){
		int u = qu.front();
		qu.pop();
		edge* p = head[u];
		while(p != NULL){
			int v = p -> v;
			if(p -> w && d[v] == -1){//存在且还没加入图
				d[v] = d[u] + 1;
				qu.push(v);
			}
			p = p -> nex;
		}
	}
	if(d[t] == -1)//到不了终点
		return 0;
	else
		return 1;
}
ll dfs(int u,ll flow){
	if(u == t)
		return flow;
	edge* p = head[u];
	ll use = 0;
	while(p != NULL){
		int v = p -> v;
		if(d[v] == d[u] + 1 && p -> w){
			ll tem = dfs(v,min(flow,p -> w));
			flow -= tem;
			ed[p -> i].w -= tem;
			ed[(p -> i) ^ 1].w += tem;
			use += tem;
			if(flow == 0)
				break;
		}
		p = p -> nex;
	}
	if(use == 0) d[u] = -1;
	return use;
}
ll dinic(){
	ll ans = 0;
	while(bfs()){
		ans += dfs(s,inf);
	}
	return ans;
}
int main()
{
	cin >> n >> m >> s >> t;
	for(int i = 1;i <= m;i++){
		int u,v,w;cin >> u >> v >> w;
		add(u,v,w);
	}
	cout << dinic();
}
```

### ISAP算法

### 最小割等价于最大流问题

* 因为所有流都要经过割，因此所有流都大于等于割，那么最大的流就等价于最小的割
* 随便用一个最大流算法处理图，从起点出发将所有找到的点加入S集合，另外的为T集合，这样就找到最小割了(不唯一)

### EK(SPFA版)费用流

* 费用流：最大流中费用最小的
* EK算法每次都找一条可行路径，那么只要一直找费用最小的可行路径即可

* 每次都找一条费用最小的路径，反向边的费用是正向边的负权值

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 5e4 + 10,M = 5e5 + 10,INF = 0x7f7f7f7f;
int n,m,s,t,ans1,ans2;

struct edge{
	int v,flow,cost,i;
	edge* nex;
}ed[M];
int ptop = 0;
edge* head[N];
void add(int u,int v,int flow,int cost){
	ed[ptop].v = v;
	ed[ptop].flow = flow;
	ed[ptop].cost = cost;
	ed[ptop].i = ptop;
	ed[ptop].nex = head[u];
	head[u] = &ed[ptop];
	ptop++;
	ed[ptop].v = u;
	ed[ptop].flow = 0;
	ed[ptop].cost = -cost;//反向路径费用为负
	ed[ptop].i = ptop;
	ed[ptop].nex = head[v];
	head[v] = &ed[ptop];
	ptop++;
}

int pre[M],newcost[N],Flow[N];
bool vis[N];

inline bool spfa(){
	queue<int> qu;qu.push(s);vis[s] = 1;
	memset(Flow,0,sizeof(Flow));Flow[s] = INF;//起点流量无限
	memset(newcost,INF,sizeof(newcost));newcost[s] = 0;//起点费用为0
	memset(pre,0,sizeof(pre));
	while(!qu.empty()){
		int u = qu.front();
		qu.pop();
		vis[u] = 0;
		for(auto* p = head[u];p != NULL;p = p -> nex){
			int v = p -> v;
			if(p -> flow > 0 && newcost[v] > newcost[u] + p -> cost){//是否能更新这个点
				newcost[v] = newcost[u] + p -> cost;//更新点的费用
				Flow[v] = min(Flow[u],p -> flow);//更新流量
				pre[v] = p -> i;//记录这条边
				if(!vis[v]){vis[v] = 1,qu.push(v);}
			}
		}
	}
	return newcost[t] != INF;
}

void EK(){
	while(spfa()){
		ans1 += Flow[t],ans2 += newcost[t]*Flow[t];
		int u = t;
		while(u != s){
			int k = pre[u];
			ed[k].flow -= Flow[t];
			ed[k^1].flow += Flow[t];
			u = ed[k^1].v;
		}
	}
}

int main(){
	cin >> n >> m >> s >> t;
	for(int i = 1;i <= m;i++){
		int u,v,flow,cost;
		cin >> u >> v >> flow >> cost;
		add(u,v,flow,cost);
	}
	EK();
	cout << ans1 << ' ' << ans2 << endl;
}
```

## tarjan算法（求强连通分量）（缩点）

### 强连通：两个点相互可达

### 强连通分量：集合中的点两两可达

### 思路：记录自己的时间戳dfs与能到达的最小时间戳low，先dfs搜索完自己能到达的点，如果更新后的最小时间戳low与己的时间戳dfs相等说明自己就是那个强连通分量顶点，如果不相等说明它可以到达更小的时间戳，那么在回溯到那个点时再处理

### 题目：

### 1.计算强连通分量数量

### [迷宫城堡](https://acm.hdu.edu.cn/showproblem.php?pid=1269)

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 3e5 + 10;
stack<int> st;
bool in[N];//是否在栈内
int dfs[N],low[N];
vector<int> ed[N];
int ptop = 0;
int num = 0;//记录强连通分量的数量
void tarjan(int x){
	dfs[x] = low[x] = ++ptop;
	st.push(x);
	in[x] = 1;
	for(auto y : ed[x]){
		if(!dfs[y])
			tarjan(y);
        else if(in[y])
			low[x] = min(low[x],low[y]);
	}
	if(low[x] == dfs[x]){
		int y;
		num++;
		do{
			y = st.top();
			st.pop();
			in[y] = 0;
		}while(y != x);
	}
}
int n,m;
void init(){
	num = ptop = 0;
	for(int i = 1;i <= n;i++){
		in[i] = dfs[i] = low[i] = 0;
		ed[i].clear();
	}
}
int main()
{
	cin >> n >> m;
	while(n != 0){
		init();
		for(int i = 1;i <= m;i++){
			int u,v;cin >> u >> v;
			ed[u].push_back(v);
		}
		for(int i = 1;i <= n;i++)
			if(!dfs[i])
				tarjan(i);
		if(num == 1)
			cout << "Yes" << endl;
		else
			cout << "No" << endl;
		cin >> n >> m;
	}
}
```

### 2.变成强连通图的最小连接数

### [Proving Equivalences](https://acm.hdu.edu.cn/showproblem.php?pid=2767)

### 思路：先用tarjan缩点，然后看新的图，如果要让新的图变成强连通图，就要让每个点都有入有出，如果原图是强连通图，那么新加一点也要让其依旧为强连通图，那么只要随便连一条边进入原图，再从原图拉一条边出来，因为原图是强连通图，所以所有点两两相通，也就包括从原图连入与连出的点，因此对于强连通图来说，只要每个点都有入有出即可变为强连通图

### 但是如果强连通分量彼此分立那就不行，因此先用tarjan将强连通分量整合即可

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 3e5 + 10;
stack<int> st;
bool in[N];//是否在栈内
int dfs[N],low[N];
int _in[N],_out[N];
vector<int> ed[N];
int belong[N];//缩点
int ptop = 0;
int num = 0;//记录强连通分量的数量
void tarjan(int x){
	dfs[x] = low[x] = ++ptop;
	st.push(x);
	in[x] = 1;
	for(auto y : ed[x]){
		if(!dfs[y]){
			tarjan(y);
			low[x] = min(low[x],low[y]);
		}
		else if(in[y])
			low[x] = min(low[x],dfs[y]);
	}
	if(low[x] == dfs[x]){
		int y;
		num++;
		do{
			y = st.top();
			st.pop();
			in[y] = 0;
			belong[y] = num;
		}while(y != x);
	}
}
int n,m;
void init(){
	num = ptop = 0;
	for(int i = 1;i <= n;i++){
		dfs[i] = low[i] = 0;
		_in[i] = _out[i] = 0;
		ed[i].clear();
	}
}
int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	int t;cin >> t;
	while(t--){
		cin >> n >> m;
		init();
		for(int i = 1;i <= m;i++){
			int u,v;cin >> u >> v;
			ed[u].push_back(v);
		}
		for(int i = 1;i <= n;i++)
			if(!dfs[i])
				tarjan(i);
		if(num == 1){
			cout << 0 << endl;
			continue;
		}
		for(int i = 1;i <= n;i++){
			for(auto x:ed[i]){
				if(belong[x] != belong[i]){
					_in[belong[x]]++;
					_out[belong[i]]++;
				}
			}
		}
		int __in = 0,__out = 0;
		for(int i = 1;i <= num;i++){
			if(_in[i] == 0)
				__in++;
			if(_out[i] == 0)
				__out++;
		}
		cout << max(__in,__out) << endl;
	}
}
```

### 3.让所有点都能流到需要的最小费用

### [Summer Holiday](https://acm.hdu.edu.cn/showproblem.php?pid=1827)

### 题意：联系第$i$人需要$a_i$的费用，那些人有些又可以联系其他人，问最小的费用联系到所有人

### 思路：先缩点，然后看入边，如果入边为0说明需要联系，在这个集合里找最小加上即可

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 3e5 + 10;
stack<int> st;
bool in[N];//是否在栈内
int dfs[N],low[N];
vector<int> ed[N];
int belong[N];//缩点
int ptop = 0;
int num = 0;//记录强连通分量的数量
void tarjan(int x){
	dfs[x] = low[x] = ++ptop;
	st.push(x);
	in[x] = 1;
	for(auto y : ed[x]){
		if(!dfs[y]){
			tarjan(y);
			low[x] = min(low[x],low[y]);
		}
		else if(in[y])
			low[x] = min(low[x],dfs[y]);
	}
	if(low[x] == dfs[x]){
		int y;
		num++;
		do{
			y = st.top();
			st.pop();
			in[y] = 0;
			belong[y] = num;
		}while(y != x);
	}
}
int n,m;
int mi[N];
int _in[N];
void init(){
	num = ptop = 0;
	for(int i = 1;i <= n;i++){
		dfs[i] = low[i] = 0;
		_in[i] = 0;
		ed[i].clear();
		mi[i] = 1e9;
	}
}
int a[N];
int main()
{
	ios::sync_with_stdio(false);//写了using namespace std;
	while(cin >> n >> m){
		init();
		for(int i = 1;i <= n;i++)
			cin >> a[i];
		for(int i = 1;i <= m;i++){
			int u,v;cin >> u >> v;
			ed[u].push_back(v);
		}
		for(int i = 1;i <= n;i++)
			if(!dfs[i])
				tarjan(i);
		for(int i = 1;i <= n;i++){
			for(auto x : ed[i]){
				if(belong[x] != belong[i]){
					_in[belong[x]]++;
				}
			}
		}
		for(int i = 1;i <= n;i++)
			mi[belong[i]] = min(a[i],mi[belong[i]]);
		int ans = 0,nnum = 0;
		for(int i = 1;i <= num;i++){
			if(_in[i] == 0){
				ans += mi[i];
				nnum++;
			}
		}
		cout << nnum << ' ' << ans << endl;
	}
}
```

### 4.割点

#### 其实割点不需要tarjan的栈之类的，用dfs就能解决，参考了tarjan的时间戳的思想

#### 定义：去掉该点后使得原本该点所在的树不连通

#### 思路：我们从一个点开始搜索，将它当作根，如果它搜索了两个即以上不相连的子树，那么说明这个点为割点，如果是被搜索到的点，说明上面为一连通部分，那么只要有一个子树的最小能到达时间戳大于等于该节点，说明子树被这个点与上面分开，那么这个点就为割点

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 3e5 + 10;
stack<int> st;
int dfs[N],low[N];
vector<int> ed[N];
bool is[N];
int ptop = 0;
int cnt = 0;
int root;
void tarjan(int x){
	dfs[x] = low[x] = ++ptop;
	st.push(x);
	int son = 0;
	for(auto y : ed[x]){
		if(!dfs[y]){
			son++;
			tarjan(y);low[x] = min(low[x],low[y]);
			if(low[y] >= dfs[x] && x != root){
				cnt += !is[x];
				is[x] = 1;
			}
		}else
			low[x] = min(low[x],dfs[y]);
	}
	if(son >= 2 && root == x){
		cnt += !is[x];
		is[x] = 1;
	}
}
int n,m;
int main()
{
	cin >> n >> m;
	for(int i = 1;i <= m;i++){
		int u,v;cin >> u >> v;
		ed[u].push_back(v);
		ed[v].push_back(u);
	}
	for(int i = 1;i <= n;i++){
		if(!dfs[i]){
			root = i;
			tarjan(i);
		}
	}
	cout << cnt << endl;
	for(int i = 1;i <= n;i++)
		if(is[i])
			cout << i << ' ';
}
```

## 二分图（二部图）

### 概念：可分为两个集合，集合内的点无边相连的图

### 判定：染色法

```c++
int col[MAXN];
vector<int> ed[MAXN];
bool bfs(){
	col[1] = 1;
	queue<int> qu;
	while(!qu.empty()){
		int u = qu.front();
		qu.pop();
		for(auto v:ed[u]){
			if(!col[v]){
				col[v] = 2 - col[u];
				qu.push(v);
			}else if(col[v] == col[u])
				return 0;
		}
	}
	return 1;
}
```

## 无权二部图最大匹配问题

* 匹配：每个节点都只连一条边的边集合

### 转换为最大流问题

* 设置一个起点，连接集合S的所有点，权值为1，设置一个终点，连接集合T的所有点，权值为1，求最大匹配等价于求起点到终点的最大流

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e3 + 20,MAXE = 5e4 + MAXN;
#define inf 0x3f3f3f3f
struct edge{
	int v,w;
	int i;
	edge* nex;
}ed[MAXE*2];
edge* head[MAXN];
int ptop = 0;
void add(int u,int v){
	ed[ptop].v = v;
	ed[ptop].w = 1;
	ed[ptop].nex = head[u];
	head[u] = &ed[ptop];
	ed[ptop].i = ptop;
	ptop++;
	ed[ptop].v = u;
	ed[ptop].w = 0;
	ed[ptop].nex = head[v];
	head[v] = &ed[ptop];
	ed[ptop].i = ptop;
	ptop++;
}
int d[MAXN];
int s = 0,t;
bool bfs(){
	queue<int> qu;
	qu.push(s);
	memset(d,-1,sizeof(d));
	d[s] = 0;
	while(!qu.empty()){
		int u = qu.front();
		qu.pop();
		edge* p = head[u];
		while(p != NULL){
			int v = p -> v;
			if(p -> w && d[v] == -1){
				d[v] = d[u] + 1;
				qu.push(v);
			}
			p = p -> nex;
		}
	}
	if(d[t] == -1)
		return 0;
	else
		return 1;
}
int dfs(int u,int flow){
	edge* p = head[u];
	if(u == t)
		return flow;
	int use = 0;
	while(p != NULL){
		int v = p -> v;
		if(d[v] == d[u] + 1 && p -> w){
			int tem = dfs(v,min(flow,p -> w));
			use += tem;
			flow -= tem;
			ed[p -> i].w -= tem;
			ed[(p -> i)^1].w += tem;
			if(flow == 0)
				break;
		}
		p = p -> nex;
	}
	if(use == 0)
		d[u] = -1;
	return use;
}
int dinic(){
	int ans = 0;
	while(bfs()){
		ans += dfs(s,inf);
	}
	return ans;
}
int main()
{
	int n,m,e;cin >> n >> m >> e;
	for(int i = 1;i <= n;i++)
		add(s,i);
	t = n + m + 1;
	for(int i = 1 + n;i < t;i++)
		add(i,t);
	for(int i = 1;i <= e;i++)
	{
		int u,v;cin >> u >> v;
		v += n;
		add(u,v);
	}
	cout << dinic();
}
```

## 2-SAT

* 对于二元条件并且，元素只有两个可能，如果非A，那么就可以推出B，同理非B就可以推出A，那么就可以建图，不可能成立的情况就是A与非A形成了一个环，用tarjan找环，然后tarjan给出的color就是拓扑逆序，那么为了防止A推得非A，那么只要输出color小的可能性就是拓扑后面的位置

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 1e6 + 10;

vector<int> ed[N*2];

stack<int> st;
bool in[N*2];//是否在栈内
int dfs[N*2],low[N*2];
int ptop = 0;
int num = 0;//记录强连通分量的数量
int c[N*2];
void tarjan(int x){
	dfs[x] = low[x] = ++ptop;
	st.push(x);
	in[x] = 1;
	for(auto y : ed[x]){
		if(!dfs[y])
			tarjan(y);
		else if(in[y])
			low[x] = min(low[x],low[y]);
	}
	if(low[x] == dfs[x]){
		int y;
		num++;
		do{
			c[y] = num;
			y = st.top();
			st.pop();
			in[y] = 0;
		}while(y != x);
		c[x] = num;
	}
}
int n,m;

int main(){
	cin >> n >> m;
	for(int k = 1;k <= m;k++){
		int i,a,j,b;cin >> i >> a >> j >> b;
		if(a) i = 2*n - i + 1;
		if(b) j = 2*n - j + 1;
		i = 2*n - i + 1;
		ed[i].push_back(j);
		i = 2*n - i + 1;
		j = 2*n - j + 1;
		ed[j].push_back(i);
	}
	bool f = 1;
	for(int i = 1;i <= n*2;i++) {
		if(!dfs[i]) {
			tarjan(i);
		}
	}
	for(int i = 1;i <= n;i++){
		if(c[i] == c[2*n + 1 - i]) f = 0;
	}
	if(!f) cout << "IMPOSSIBLE" << '\n';
	else{
		cout << "POSSIBLE" << '\n';
		for(int i = 1;i <= n;i++)
			cout <<	(c[2*n - i + 1] < c[i]) << ' ';
	}
}
```



# 动态规划

![闫氏DP分析法](C:\Users\Administrator\Desktop\markdown\闫氏DP分析法.png)

## 1.数字三角形模型

### 1.1只取一条最大路或最小路

- [1015. 摘花生](https://www.acwing.com/problem/content/1017/)

- 只能向下或向右走，求最大能取多少花生

- 记录每个点最大能取到多少花生，从左上一直推到右下角

- $dp[i][j]=max(dp[i-1][j],dp[i][j-1])+a[i][j]$

- 复杂度：$O(tn^2)$

  ```c++
  #include<bits/stdc++.h>
  using namespace std;
  const int MAXN = 101;
  int dp[MAXN][MAXN];
  int a[MAXN][MAXN];
  int main()
  {
  	int t;cin >> t;
  	while(t--)
  	{
  		memset(dp,0,sizeof(dp));
  		int r,c;cin >> r >> c;
  		for(int i = 1;i <= r;i++)
  		for(int j = 1;j <= c;j++)
  			cin >> a[i][j];
  		dp[1][1] = a[1][1];
  		for(int i = 3;i <= r + c;i++)//从小到大枚举步数
  		{
  			for(int _i = 1;_i <= r;_i++)
  			{
  				int _j = i - _i;
  				if(_j <= 0 || _j > c)
  					continue;
  				else
  				{
  					if(_i != 1)
  						dp[_i][_j] = dp[_i - 1][_j];
  					if(_j != 1)
  						dp[_i][_j] = max(dp[_i][_j],dp[_i][_j - 1]);
  					dp[_i][_j] += a[_i][_j];
  				}
  			}
  		}
  		cout << dp[r][c] << endl;
  	}
  }
  ```

  

- [1018. 最低通行费](https://www.acwing.com/problem/content/1020/)

- 同上，只是取最小值

- $dp[i][j]=min(dp[i-1][j],dp[i][j-1])+a[i][j]$

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e2 + 10;
int dp[MAXN][MAXN];
int main()
{
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
	for(int j = 1;j <= n;j++)
	cin >> dp[i][j];
	for(int i = 3;i <= n*2;i++)
	{
		for(int _i = 1;_i <= n;_i++)
		{
			int _j = i - _i;
			if(_j <= 0 || _j > n)
				continue;
			int mi = 1e9 + 10;
			if(_j - 1 > 0)
				mi = min(mi,dp[_i][_j - 1]);
			if(_i - 1 > 0)
				mi = min(mi,dp[_i - 1][_j]);
			dp[_i][_j] += mi;
		}
	}
	cout << dp[n][n];
}
```



### 1.2取两条路，可重叠

- [1027.方格取数](https://www.acwing.com/problem/content/description/1029/)
- 取两条路得到最大值，我们就让两条路同时走，如果走到同一个格子就加一次
- 复杂度：$O(n^3)$

```c++
#include<bits/stdc++.h>
using namespace std;
int dp[11][11][22];
int a[11][11];
int main()
{
	int n;cin >> n;
	int i,j,v;cin >> i >> j >> v;
	while(i != 0)
	{
		a[i][j] = v;
		cin >> i >> j >> v;
	}
	dp[1][1][2] = a[1][1];
	for(int i = 3;i <= n*2;i++)//枚举两条路的步数
	{
		for(int i1 = 1;i1 <= n;i1++)//枚举第一条路线的i
		{
			int j1 = i - i1;
			if(j1 <= 0 || j1 > n)
				continue;
			for(int i2 = 1;i2 <= n;i2++)//枚举第二条
			{
				int j2 = i - i2;
				if(j2 <= 0 || j2 > n)
					continue;
				int w = a[i1][j1] + a[i2][j2];
				if(i1 == i2)//走到同一个格子
					w -= a[i1][j1];
				int ma = 0;
				if(i1 - 1 > 0 && i2 - 1 > 0)
					ma = dp[i1 - 1][i2 - 1][i - 1];
				if(i1 - 1 > 0 && j2 - 1 > 0)
					ma = max(ma,dp[i1 - 1][i2][i - 1]);
				if(j1 - 1 > 0 && i2 - 1 > 0)
					ma = max(ma,dp[i1][i2 - 1][i - 1]);
				if(j1 - 1 > 0 && j2 - 1 > 0)
					ma = max(ma,dp[i1][i2][i - 1]);
				dp[i1][i2][i] = ma + w;
			}
		}
	}
	cout << dp[n][n][n*2];
}
```

### 1.3取两条路，不可重叠

- [275. 传纸条](https://www.acwing.com/problem/content/277/)
- 那就默认让一条路的$i1$大于$i2$即可，这样两条路就没有相交点
- 复杂度：$O(n^3)$

```c++
#include<bits/stdc++.h>
using namespace std;//假设第一条路径就是高于第二条路径
int a[51][51];
int dp[51][51][102];
int main()
{
	int m,n;cin >> m >> n;
	for(int i = 1;i <= m;i++)
	for(int j = 1;j <= n;j++)
		cin >> a[i][j];
	for(int i = 1;i <= m + n;i++)//枚举步数
	{
		for(int i1 = 1;i1 <= m;i1++)
		{
			int j1 = i - i1;
			if(j1 <= 0 || j1 > n)
				continue;
			for(int i2 = 1;i2 < i1;i2++)
			{
				int j2 = i - i2;
				if(j2 <= 0 || j2 > n)
					continue;
				int ma = 0;
				if(i1 - 1 > 0 && i2 - 1 > 0)
					ma = dp[i1 - 1][i2 - 1][i - 1];
				if(i1 - 1 > 0 && j2 - 1 > 0)
					ma = max(ma,dp[i1 - 1][i2][i - 1]);
				if(j1 - 1 > 0 && i2 - 1 > 0)
					ma = max(ma,dp[i1][i2 - 1][i - 1]);
				if(j1 - 1 > 0 && j2 - 1 > 0)
					ma = max(ma,dp[i1][i2][i - 1]);
				dp[i1][i2][i] = ma + a[i1][j1] + a[i2][j2];
			}
		}
	}
	cout << dp[m][m - 1][n + m - 1];
}
```

## 2.最长上升子序列模型

 ### 2.1求最长长度

* [1017. 怪盗基德的滑翔翼](https://www.acwing.com/problem/content/1019/)

* 1.贪心法：如果大于最后一个数，就直接加入，否则找第一个大于它的数替换，这样就实现了让后面有潜力的子序列能替换掉最大串

* 复杂度：$O(nlog_2n)$

```c++
#include<bits/stdc++.h>
using namespace std;
int a[101],b[101],c[101];
int main()
{
	int k;cin >> k;
	while(k--)
	{
		int n;cin >> n;
		int ptop = 1;
		cin >> a[1];
		c[1] = a[1];
		for(int i = 2;i <= n;i++)
		{
			int x;cin >> c[i];
			x = c[i];
			if(a[ptop] < x)
				a[++ptop] = x;
			else
				a[lower_bound(a + 1,a + 1 + ptop,x) - a] = x;
		}
		int pptop = 1;
		b[1] = c[n];
		for(int i = n - 1;i >= 1;i--)
		{
			int x = c[i];
			if(b[pptop] < x)
				b[++pptop] = x;
			else
				b[lower_bound(b + 1,b + 1 + pptop,x) - b] = x;
		}
		cout << max(ptop,pptop) << endl;
	}
}
```

* 2.动态规划：存这个位置最长的串，从前往后枚举，每次都向前找最长的能插上的位置然后加1
* 复杂度：$O(n^2)$

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 101;
int a[MAXN],dp[MAXN];
int main()
{
	int k;cin >> k;
	while(k--)
	{
		int n;cin >> n;
		memset(dp,0,sizeof(dp));
		int ma = 0;
		a[0] = 10001;
		for(int i = 1;i <= n;i++)
			cin >> a[i];
		for(int i = 1;i <= n;i++)
		for(int j = 0;j < i;j++)
		if(a[j] > a[i])
		{
			dp[i] = max(dp[j] + 1,dp[i]);
			ma = max(dp[i],ma);
		}
		memset(dp,0,sizeof(dp));
		a[n + 1] = 10001;
		for(int i = n;i >= 1;i--)
		for(int j = i + 1;j <= n + 1;j++)
		if(a[j] > a[i])
		{
			dp[i] = max(dp[j] + 1,dp[i]);
			ma = max(dp[i],ma);
		}
		cout << ma << endl;
	}
}
```

### 2.2求最长山形

* [1014. 登山](https://www.acwing.com/problem/content/1016/)
* [482. 合唱队形](https://www.acwing.com/problem/content/484/)

* 从前往后和从后往前都求一遍，求最大和

* 贪心法：复杂度$O(nlog_{2}n)$

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e3 + 10;
int a[MAXN];
int b[MAXN],ans[MAXN];
int main()
{
	int n;cin >> n;
	int ptop = 1;
	cin >> a[1];
	b[1] = a[1];
	ans[1] = 1;
	for(int i = 2;i <= n;i++)
	{
		cin >> a[i];
		int x = a[i];
		if(x > b[ptop])
			b[++ptop] = x;
		else
			b[lower_bound(b + 1,b + 1 + ptop,x) - b] = x;
		ans[i] = ptop;
	}
	ptop = 1;
	b[1] = a[n];
	int ma = ans[n];
	for(int i = n - 1;i >= 1;i--)
	{
		int x = a[i];
		if(x > b[ptop])
			b[++ptop] = x;
		else
			b[lower_bound(b + 1,b + 1 + ptop,x) - b] = x;
		ma = max(ptop + ans[i] - 1,ma);
	}
	cout << ma;
}
```

### 2.3引索型最长子序列

* [1012. 友好城市](https://www.acwing.com/problem/content/1014/)

* $a_i$和$b_i$都要递增

* 对$a_i$排序，对排序后的$b_i$做最长子序列

* 复杂度：$O(nlog_2n)$

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 5e3 + 10;
pair<int,int> pa[MAXN];
int b[MAXN];
int main()
{
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
		cin >> pa[i].first >> pa[i].second;
	sort(pa + 1,pa + 1 + n);
	b[1] = pa[1].second;
	int ptop = 1;
	for(int i = 2;i <= n;i++)
		if(pa[i].second > b[ptop])
			b[++ptop] = pa[i].second;
		else
			b[lower_bound(b + 1,b + 1 + ptop,pa[i].second) - b] = pa[i].second;
	cout << ptop;
}
```

### 2.4最少递增序列

* [1010. 拦截导弹](https://www.acwing.com/problem/content/1012/)

* 给一个序列，将其分成多个单调递增序列，求最少数量

* 记录每个序列的最大值，枚举放下，如果放不下就增加一个序列来放

* 复杂度：$O(nlog_2n)$

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e3 + 10;
int a[MAXN],b[MAXN],c[MAXN];
int main()
{
	int ptop = 0;
	int x,ma = 0;
	while(scanf("%d",&x) != EOF)
	{
		a[++ptop] = x;
	}
	int pptop = 1;c[1] = b[1] = a[ptop];
	int ans = 1;
	for(int i = ptop - 1;i >= 1;i--)
	{
		int x = a[i];
		int aim = lower_bound(c + 1,c + 1 + ans,x,[&](int a,int b){
			return a > b;
		}) - c;
		if(aim == ans + 1)
			c[++ans] = x;
		else
			c[aim] = x;
		if(b[pptop] <= x)
		{
			b[++pptop] = x;
		}
		else
		{
			b[upper_bound(b + 1,b + 1 + pptop,x) - b] = x;
		}
	}
	cout << pptop << endl << ans << endl;
}
```

### 2.5求最大的上升子序列

* [1016. 最大上升子序列和](https://www.acwing.com/problem/content/1018/)
* 动态规划暴力，找前面最大的可以放在后面的，就是把记录的长度换成总和，没有贪心做法
* 复杂度：$O(n^2)$

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e3 + 10,MAX = 1e4 + 10;
int a[MAXN];
int dp[MAX];
int main()
{
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
	{
		int x;cin >> x;
		int ma = 0;
		for(int i = 1;i < x;i++)
		{
			ma = max(ma,dp[i]);
		}
		dp[x] = ma + x;
	}
	int ma = 0;
	for(int i = 1;i <= MAX - 1;i++)
		ma = max(ma,dp[i]);
	cout << ma << endl;
}
```

### 2.6最少分割单调子序列

* [187. 导弹防御系统](https://www.acwing.com/problem/content/189/)

* 爆搜

* 枚举每个位置是加入递增还是递减，然后深度优先搜索，得出一个答案，回溯然后用这个答案剪枝

* 复杂度：$O(n2^n)$

```c++
#include<bits/stdc++.h>
using namespace std;
int a[51];
int ans = 999;
int ma[52];
int mi[52];
int n;
void dfs(int i,int ptop1,int ptop2){
	if(ptop1 + ptop2 >= ans) return;
	if(i == n + 1) ans = ptop1 + ptop2;
	else{
		int j;
		for(j = 1;j <= ptop1;j++)
			if(a[i] > ma[j])
				break;
		int pre = ma[j];
		ma[j] = a[i];
		if(j == ptop1 + 1)
			dfs(i + 1,ptop1 + 1,ptop2);
		else
			dfs(i + 1,ptop1,ptop2);
		ma[j] = pre;
		for(j = 1;j <= ptop2;j++)
			if(a[i] < mi[j])
				break;
		pre = mi[j];
		mi[j] = a[i];
		if(j == ptop2 + 1)
			dfs(i + 1,ptop1,ptop2 + 1);
		else
			dfs(i + 1,ptop1,ptop2);
		mi[j] = pre;
	}
}
int main()
{
	cin >> n;
	while(n != 0){
		ans = 999;
		for(int i = 1;i <= n;i++)
			cin >> a[i];
		dfs(1,0,0);
		cout << ans << endl;
		cin >> n;
	}
}
```

### 2.7最长公共上升子序列

* 如果$a[i]=b[j]$，那么如果要接上这个，就要找前面最长的还小于它们的公共上升子序列，用$f[i][j]$来表示以$b[j]$结尾的最长公共上升子序列，要找的就是$f_{max}[i'][j'](b[j']<a[j]\&\&j'<j)$

```c++
for(int i = 1;i <= n;i++)
for(int j = 1;j <= n;j++)
{
    if(a[i] == b[j]){
        int ma = 1;
        for(int k = 1;k < j;k++)
            if(a[i] > b[j])
            	ma = max(dp[i][k] + 1,ma);
        dp[i][j] = ma;
    }
}
```

* 三重循环会爆时间，因此要简化，我们可以看到$j$是递增的，而$k$循环是找$j$之前的东西，所以我们只要在$j$循环内找即可，推一个$j$找一次

```c++
for(int i = 1;i <= n;i++)
{
    int ma = 1;
    for(int j = 1;j <= n;j++)
    {
        if(a[i] == b[j]) dp[i][j] = ma;
        else if(a[i] > b[j]) ma = max(dp[i][j] + 1,ma);
    }
}
```

* 综上所述
* 复杂度：$O(n^2)$

```c++
#include<bits/stdc++.h>
using namespace std;
int dp[3001][3001];
int a[3001],b[3001];
int main()
{
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
		cin >> a[i];
	for(int i = 1;i <= n;i++)
		cin >> b[i];
	for(int i = 1;i <= n;i++)
	{
		int ma = 1;
		for(int j = 1;j <= n;j++)//从前往后,尝试加入a[i]
		{
			dp[i][j] = dp[i - 1][j];
			if(a[i] == b[j])//尝试加入
			dp[i][j] = max(dp[i][j],ma);
			else if(a[i] > b[j])
			ma = max(dp[i][j] + 1,ma);
		}
	}
	int ma = 0;
	for(int i = 1;i <= n;i++)
		ma = max(dp[n][i],ma);
	cout << ma << endl;
}
```

### addtion 2.8最长下降子序列及计不同序列数量

* [G - 低价购买](https://vjudge.net/contest/567292#problem/G)

* 用dp来做最长下降子序列，每次得到一个数就向前找比它大的然后尝试接上
* 计数时有可能重复计数，那么我们取一个数是如果前面有相同的数，那么就把那个数抛弃掉，因为对后面的数来说接前面的数还是这个数都一样并且接后面的那个相同的数更有可能更长

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 5e3 + 10;

pair<int,int> dp[N];
int a[N];

int main()
{
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
		cin >> a[i];
	int ma = 0;
	for(int i = 1;i <= n;i++){
		dp[i] = {1,1};
		for(int j = i - 1;j >= 1;j--){
			if(a[i] < a[j]){
				if(dp[j].first + 1 > dp[i].first){
					dp[i].first = dp[j].first + 1;
					dp[i].second = dp[j].second;
				}else if(dp[j].first + 1 == dp[i].first)
					dp[i].second += dp[j].second;
			}else if(a[i] == a[j])
				dp[j] = {0,0};
		}
		ma = max(ma,dp[i].first);
	}
	int cnt = 0;
	for(int i = 1;i <= n;i++)
		if(ma == dp[i].first)
			cnt += dp[i].second;
	cout << ma << ' ' << cnt << endl;
}
```



## 3.背包模型

### 3.1 01背包问题

* [423. 采药](https://www.acwing.com/problem/content/425/)

* 枚举物品，每次尝试放入物品，如果放入后状态更好就替换，否则不换$dp[i][j]=max(dp[i-1][j - t]+v,dp[i-1][j])$

* 如果从后往前更新，就不会产生一个物品取两次的情况了，所以有$dp[j]=max(dp[j-t]+v,dp[j])$
* 时间复杂度：$O(tm)$
* 开$dp[][]$

```c++
#include<bits/stdc++.h>
using namespace std;
int dp[101][1001];
int main()
{
	int t,m;cin >> t >> m;
	for(int i = 1;i <= m;i++){
		
		int tt,v;cin >> tt >> v;
		for(int j = 1;j <= t;j++)
			if(j - tt >= 0)
				dp[i][j] = max(dp[i - 1][j - tt] + v,dp[i - 1][j]);
			else
				dp[i][j] = dp[i - 1][j];
	}
	cout << dp[m][t];
}
```

* 开$dp[]$

```c++
#include<bits/stdc++.h>
using namespace std;
int dp[1001];
int main()
{
		int t,m;cin >> t >> m;
		for(int i = 1;i <= m;i++){
			int tt,v;cin >> tt >> v;
			for(int j = t;j >= tt;j--)
				dp[j] = max(dp[j - tt] + v,dp[j]);
		}
		cout << dp[t] << endl;
}
```

* [1024. 装箱问题](https://www.acwing.com/problem/content/1026/)

* 只有已经被标记过的体积才能再填充减去再标记，最后找最小标记即可

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXV = 2e4 + 10;
bool dp[MAXV];
int main()
{
	int mi = MAXV;
	int v,n;cin >> v >> n;
	dp[v] = 1;
	for(int i = 1;i <= n;i++){
		int vv;cin >> vv;
		for(int j = vv;j <= v;j++)
			if(dp[j])
			{
				dp[j - vv] = 1;
				mi = min(j - vv,mi);
			}
	}
	cout << mi;
}
```

### 3.2二重01背包问题

* [1022. 宠物小精灵之收服](https://www.acwing.com/problem/content/1024/)

* 两个限制条件，就是加一重循环来更新，用两个数值代表现在状态
* $dp[i][j]=max(dp[i][j],dp[i-t_1][j-t_2]+v)$
* 时间复杂度：$O(nmk)$

```c++
#include<bits/stdc++.h>
using namespace std;
pair<int,int> ans;
int dp[1001][501];
int main()
{
	int n,m,k;cin >> n >> m >> k;
	ans.first = 0,ans.second = m;
	for(int i = 1;i <= k;i++){
		int nu,at;cin >> nu >> at;
		for(int _i = 0;_i <= n - nu;_i++)
		for(int _j = 1;_j <= m - at;_j++)
		{
			dp[_i][_j] = max(dp[_i][_j],dp[_i + nu][_j + at] + 1);
			if(dp[_i][_j] > ans.first)
				ans = {dp[_i][_j],_i};
			else if(dp[_i][_j] == ans.first)
				ans.second = max(_j,ans.second);
		}
	}
	cout << ans.first << ' ' << ans.second;
}
```

### 3.3 01背包状态出现次数

* [278. 数字组合](https://www.acwing.com/problem/content/280/)

* 设初始状态出现次数为1，更新背包时把更新前的出现次数加在更新后状态的出现次数

```c++
#include<bits/stdc++.h>
using namespace std;
int dp[10001];//出现次数
int main()
{
	int n,m;cin >> n >> m;
	dp[0] = 1;
	for(int i = 1;i <= n;i++){
		int x;cin >> x;
		for(int i = m;i >= x;i--)
			dp[i] += dp[i - x];
	}
	cout << dp[m];
}
```

### 3.4完全背包求方案数

* [1023. 买书](https://www.acwing.com/problem/content/description/1025/)

* 从前往后更新背包，这样就是完全背包，因为更新背包是更新后面，可以继续更新

```c++
#include<bits/stdc++.h>
using namespace std;
int dp[1001];//出现此数的数量
int num[] = {10,20,50,100};
int main()
{
	int n;cin >> n;
	dp[0] = 1;
	for(int i = 0;i < 4;i++)
	    for(int j = num[i];j <= n;j++)
	        dp[j] += dp[j - num[i]];
	cout << dp[n];
}
```

* [1021. 货币系统](https://www.acwing.com/problem/content/1023/) 同理，记得开long long

### 3.5最少的数来组合出所有数 

* [532. 货币系统](https://www.acwing.com/problem/content/534/)

* 从小的开始，如果这个被标记过，就说明已经被其他数给表示了，否则就加入，进行一次完全背包

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 101,MAXM = 2.5e4;
bool dp[MAXM];
int a[MAXN];
int main()
{
	int t;cin >> t;
	while(t--){
		int n;cin >> n;
		memset(dp,0,sizeof(dp));
		dp[0] = 1;
		for(int i = 1;i <= n;i++)
			cin >> a[i];
		sort(a + 1,a + 1 + n);
		int ans = 0;
		for(int i = 1;i <= n;i++){
			if(dp[a[i]])
				continue;
			ans++;
			for(int j = 0;j <= a[n] - a[i];j++)
				if(dp[j])
					dp[j + a[i]] = 1;
		}
		cout << ans << endl;
	}
}
```

### 3.6单调队列+滑动窗口+滚动数组 优化多重完全背包

* [6. 多重背包问题 III](https://www.acwing.com/problem/content/6/)

* 现加入s个价值为w，体积为v的物体，那么能更新位置x的有

$$
dp[i][x]=max(dp[i-1][x],dp[i-1][x-v]+w,dp[i-1][x-2v]+2w...,dp[i-1][x-sv]+sw)
$$

* 那么更新位置x+v有

$$
dp[i][x+v]=max(dp[i-1][x+v],dp[i-1][x]+w,dp[i-1][x-v]+w...,dp[i-1][x-(s-1)v]+(s-1)w)
$$

* 可以见到形式十分相似，我们可以使用滑动窗口来维护这个式子，利用单调队列来求出这个的最大值
* 枚举所有v的余数即可

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXV = 2e4 + 10;
int dp[2][MAXV];
pair<int,int> deq[MAXV];
int main()
{
	int n,op = 0,V;cin >> n >> V;
	for(int i = 1;i <= n;i++){
		int v,w,s;cin >> v >> w >> s;
		
		for(int j = 0;j < v;j++)//枚举余数
		{
			int top = 1,end = 1;
			deq[1] = {0,0};
			for(int k = 1;k*v + j <= V;k++){
				while(top <= end && deq[end].first + (k - deq[end].second)*w < dp[1 - op][k*v + j])
					end--;
				deq[++end] = {dp[1 - op][k*v + j],k};
				if(k - deq[top].second > s)
					top++;
				dp[op][k*v + j] = deq[top].first + (k - deq[top].second)*w;
			}
		}
		op = 1 - op;
	}
	cout << dp[1 - op][V];
}
```

### 3.7简单的混合背包

* [7. 混合背包问题](https://www.acwing.com/problem/content/7/)

* 如果是一次，就从后往前进行一次
* 如果是完全就从前往后更新
* 如果是多重就判断给的大小是否超过背包大小，如果超过就继续完全背包，否则就执行s次01背包

```c++
#include<bits/stdc++.h>
using namespace std;
int dp[1001];
int main()
{
	int n,V;cin >> n >> V;
	for(int i = 1;i <= n;i++){
		int v,w,s;cin >> v >> w >> s;
		if(s == 0 || v*s >= V){
			for(int k = v;k <= V;k++)
				dp[k] = max(dp[k],dp[k - v] + w);
		}
		else
		{
			s = max(s,1);
			for(int j = 1;j <= s;j++)
				for(int k = V;k >= v;k--)
					dp[k] = max(dp[k],dp[k - v] + w);
		}
	}
	cout << dp[V];
}
```

### 3.8二维费用背包

* [8. 二维费用的背包问题](https://www.acwing.com/problem/content/8/)

* 就是加了1维，没什么差别

```c++
#include<bits/stdc++.h>
using namespace std;
int dp[101][101];
int main()
{
	int n,V,M;cin >> n >> V >> M;
	for(int i = 1;i <= n;i++){
		int v,m,w;cin >> v >> m >> w;
		for(int j = V;j >= v;j--)
		for(int k = M;k >= m;k--)
			dp[j][k] = max(dp[j][k],dp[j - v][k - m] + w);
	}
	cout << dp[V][M];
}
```

### 3.9满足条件或更好的最值背包

* [1020. 潜水员](https://www.acwing.com/problem/content/description/1022/)
* 每次找最优能更新此位置的位置

```c++
#include<bits/stdc++.h>
using namespace std;
int dp[80][22];
int main()
{
	int m,n;cin >> m >> n;
	int t;cin >> t;
	for(int i = 0;i <= m;i++)
	for(int j = 0;j <= n;j++)
		dp[j][i] = 1e9 + 10;
	dp[0][0] = 0;
	for(int i = 1;i <= t;i++){
		int a,b,c;cin >> a >> b >> c;
		for(int j = n;j >= 0;j--)
		for(int k = m;k >= 0;k--)
		{
			int aimj = max(0,j-b),aimk = max(0,k-a);
			dp[j][k] = min(dp[j][k],dp[aimj][aimk] + c);
		}
	}
	cout << dp[n][m];
}
```

### 3.10输出任意一种合法方案

* [1013. 机器分配](https://www.acwing.com/problem/content/1015/)

* 反向搜索，不用担心找不到，因为是一层一层推的，所以所有点都可以找到方案

```c++
#include<bits/stdc++.h>
using namespace std;
int dp[11][16];//给剩下的数量，执行了几个
int a[16][16];
void dfs(int n,int m){
	if(n == 1)
	{
		cout << 1 << ' ' << m << endl;
		return;
	}
	for(int i = 0;i <= m;i++){
		if(dp[n][m] == a[n][i] + dp[n - 1][m-i]){
			dfs(n-1,m-i);
			cout << n << ' ' << i << endl;
			return;
		}
	}
}
int main()
{
	int n,m;cin >> n >> m;
	for(int i = 1;i <= n;i++){
		for(int k = 0;k <= m;k++)
			dp[i][k] = dp[i - 1][k];
		for(int j = 1;j <= m;j++){
			cin >> a[i][j];
			for(int k = j;k <= m;k++)
				dp[i][k] = max(dp[i][k],dp[i - 1][k - j] + a[i][j]);
		}
	}
	cout << dp[n][m] << endl;
	dfs(n,m);
}
```

### 3.11有依赖的背包问题

* [10.有依赖的背包问题](https://www.acwing.com/problem/content/description/10/)

* 先处理子节点，再处理父节点![有依赖的背包问题](C:\Users\Administrator\Desktop\markdown\有依赖的背包问题.png)

```c++
#include<bits/stdc++.h>
using namespace std;
vector<int> son[101];
int dad[101];
int n,V,root;
int v[101],w[101];
int dp[2][101][101];
void dfs(int i){
	int op = 0;
	for(int j = v[i];j <= V;j++)
		dp[op][i][j] = dp[1 - op][i][j] = w[i];
	for(auto _i:son[i]){//更新所有子节点
		dfs(_i);
		op = 1 - op;
		for(int j = v[i];j <= V;j++)
			for(int k = 0;j - k >= v[i];k++)
				dp[op][i][j] = max(dp[op][i][j],max(dp[1-op][_i][k],dp[op][_i][k]) + dp[1-op][i][j - k]);
	}
}
int main()
{
	cin >> n >> V;
	for(int i = 1;i <= n;i++){
		cin >> v[i] >> w[i] >> dad[i];
		if(dad[i] == -1){
			root = i;
			continue;
		}
		son[dad[i]].push_back(i);
	}
	dfs(root);
	cout << max(dp[0][root][V],dp[1][root][V]);
}
```

* 分组背包就是一组内只能取一种状态，所以我们记录更新前的背包，对这组数据用更新前的背包更新新背包，这样就不会使数据重复更新，一般用滚动数组实现

### 3.12背包问题求方案数

* [11. 背包问题求方案数](https://www.acwing.com/problem/content/description/11/)

* 更新就替换方案数，相同就加上，最后统计时有可能体积不同但是最大值一样

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int mod = 1e9 + 7;
const int MAXV = 1e3 + 10;
bool able[MAXV];
pair<ll,ll> dp[MAXV];
int main()
{
	able[0] = 1;
	dp[0].second = 1;
	int n,v;cin >> n >> v;
	for(int i = 1;i <= n;i++){
		int _v,_w;cin >> _v >> _w;
		for(int j = v;j >= _v;j--){
			if(able[j - _v])
			{
				if(dp[j - _v].first + _w > dp[j].first)
				{
					dp[j].first = dp[j - _v].first + _w;
					dp[j].second = dp[j - _v].second;
					able[j] = 1;
				}
				else if(dp[j - _v].first + _w == dp[j].first){
					dp[j].second = (dp[j].second + dp[j - _v].second) % mod;
				}
			}
		}
	}
	ll num = 0;
	ll ma = 0;
	for(int i = v;i >= 0;i--){
		if(able[i]){
			if(dp[i].first > ma){
				ma = dp[i].first;
				num = dp[i].second;
			}
			else if(dp[i].first == ma){
				num = (num + dp[i].second) % mod;
			}
		}
	}
	cout << num << endl;
}
```

### 3.13背包问题求具体方案

* [12. 背包问题求具体方案](https://www.acwing.com/problem/content/12/)

* 要字典树最小的具体方案，那么较小的必须先判断能否取到最大的方案，那么就要让它最后更新背包，标记这个体积下能否用它更新背包，说明可以取

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e3 + 10;
int dp[MAXN][MAXN];
int v[MAXN],w[MAXN];
bool able[MAXN][MAXN];
int main()
{
	int n,V;cin >> n >> V;
	for(int i = 1;i <= n;i++)
	{
		cin >> v[i] >> w[i];
	}
	for(int i = n;i >= 1;i--){
		for(int j = 1;j <= V;j++){
			dp[i][j] = dp[i + 1][j];//先不取
			if(j >= v[i] && dp[i][j] <= dp[i + 1][j - v[i]] + w[i])
			{
				able[i][j] = 1;
				dp[i][j] = dp[i + 1][j - v[i]] + w[i];
			}
		}
	}
	int now = V;
	for(int i = 1;i <= n;i++){
		if(able[i][now])//能取
		{
			cout << i << ' ';
			now -= v[i];
		}
	}
}
```

### 3.14贪心确定顺序后背包

* [734. 能量石](https://www.acwing.com/problem/content/description/736/)

* 假设最优解为$p_1,p_2,p_3...$那么无论如何交换最优解的顺序都不能让答案变大，所以我们可以用贪心先确定这个顺序，然后用背包确定哪些取，那些不取

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 101,MAXS = 1e4;
struct rock{
	int s,e,l;
}r[MAXN];
bool cmp(rock a,rock b){
	return a.e + b.e - b.l*a.s > b.e + a.e - b.s*a.l;
}
int dp[MAXS];
int main()
{
	int t;cin >> t;
	int ptop=1;
	while(t--){
		memset(dp,0,sizeof(dp));
		int n;cin >> n;
		for(int i = 1;i <= n;i++){
			cin >> r[i].s >> r[i].e >> r[i].l;
		}
		sort(r + 1,r + 1 + n,cmp);
		int ma = 0;
		for(int i = 1;i <= n;i++){
			for(int j = MAXS - 1;j >= r[i].s;j--){
				dp[j] = max(dp[j],dp[j - r[i].s] + r[i].e - r[i].l*(j - r[i].s));
				ma = max(dp[j],ma);
			}
		}
		cout << "Case #" << ptop << ": " << ma << endl;
		ptop++;
	}
}
```

## 4.状态机

* 将状态转换为特定的数字，根据状态之间的转换来确定

* [1049. 大盗阿福](https://www.acwing.com/problem/content/1051/)

* 设偷这个店的状态为1，不偷为0，就有状态转换：不偷可以由偷和不偷转移，偷只能由不偷转移
* $dp[i][0]=max(dp[i-1][1],dp[i-1][0])$
* $dp[i][1]=dp[i-1][0]+w$

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
int dp[MAXN][2];
int main(){
	int t;cin >> t;
	while(t--){
		memset(dp,0,sizeof(dp));
		int n;cin >> n;
		for(int i = 1;i <= n;i++){
			int v;cin >> v;
			dp[i][1] = dp[i - 1][0] + v;
			dp[i][0] = max(dp[i - 1][0],dp[i - 1][1]);
		}
		cout << max(dp[n][0],dp[n][1]) << endl;
	}
}
```

* [1057. 股票买卖 IV](https://www.acwing.com/problem/content/1059/)

* 假设持有股票为1，没有为0，就有$dp[第几个][交易了几轮][是否有股票]$
* $dp[i][j][0]=max(dp[i-1][j-1][1]+v,dp[i-1][j][0])$
* $dp[i][j][1]=max(dp[i-1][j][0]-v,dp[i-1][j][1])$

* 还能用滚动数组优化

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
int dp[2][101][2];
int main()
{
	int n,k;cin >> n >> k;
	int op = 0;
	for(int i = 0;i <= 1;i++)
	for(int j = 0;j <= k;j++)
	dp[0][j][i] = dp[1][j][i] = -1e9;
	for(int j = 0;j <= k;j++)
		dp[0][j][0] = dp[0][j][1] = 0;//初始状态
	int ma = 0;
	for(int i = 1;i <= n;i++){
		int v;cin >> v;
		for(int j = k - 1;j >= 0;j--){
			dp[0][j + 1][1 - op] = max(dp[0][j + 1][op],dp[1][j][op] + v);
			ma = max(dp[0][j + 1][1 - op],ma);
			dp[1][j][1 - op] = max(dp[1][j][op],dp[0][j][op] - v);
		}
		op = 1 - op;
	}
	cout << ma << endl;
}
```

* [1058. 股票买卖 V](https://www.acwing.com/problem/content/1060/)

* 多了一个冷却状态2
* $dp[i][j][2]=dp[i-1][j][1]$

* $dp[i][j][1]=max(dp[i-1][j-1][0]+w,dp[i-1][j][1])$
* $dp[i][j][0]=max(dp[i-1][j][0],dp[i-1][j][2])$

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
int dp[3][2];//0无,1有,2冷却
int main()
{
	int n,op = 0;cin >> n;
	dp[1][1] = dp[1][0] = dp[2][1] = dp[2][0] = -1e9;//不存在
	for(int i = 1;i <= n;i++){
		int x;cin >> x;
		dp[0][op] = max(dp[2][1 - op],dp[0][1 - op]);
		dp[1][op] = max(dp[0][1 - op] - x,dp[1][1 - op]);
		dp[2][op] = dp[1][1 - op] + x;
		op = 1 - op;
	}
	cout << max(dp[0][1 - op],dp[2][1 - op]);
}
```

## 其他

### 最大k块连续序列之和

* [A - Max Sum Plus Plus](https://vjudge.net/contest/567292#problem/A)

* 令$dp[2][N]$，第一个参量表示是否取了上一个数，第二个参量表示是否取了多少块，本身代表和

```cpp
#include<bits/stdc++.h>//
using namespace std;

#define ll long long 
inline int read() {
    int X=0,w=1; int c=getchar();
    while (c<'0'||c>'9') { if (c=='-') w=-1; c=getchar(); }
    while (c>='0'&&c<='9') X=(X<<3)+(X<<1)+(c^48),c=getchar();
    return X*w;
}
inline void write(ll x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}

const int N = 1e6 + 10,inf = 1e9 + 10;
int dp[N];
int ma[N];
int a[N];

int main(){
	int m,n;
	while(~scanf("%d %d",&m,&n)){
		for(int i = 1;i <= n;i++)
			a[i] = read();
		memset(dp,0,sizeof(dp));dp[0] = -inf;
		memset(ma,0,sizeof(ma));
		int maa;
		for(int i = 1;i <= m;i++){
			maa = -inf;
			for(int j = 1;j <= n;j++){
				dp[j] = max(dp[j - 1] + a[j],ma[j - 1] + a[j]);
				ma[j - 1] = maa;
				maa = max(dp[j],maa);
			}
		}
		printf("%d\n",maa);
	}
}
```

### 单调栈求最大可行矩形

* 二维[H - 棋盘制作](https://vjudge.net/contest/567292#problem/H)
* 一维[Largest Rectangle in a Histogram  ](https://www.luogu.com.cn/problem/SP1805)

* 对于一个大矩形中有一些点不可行，然后要求最大可行矩形问题，用单调栈解决
* 栈内元素单调递增，如果遇到小的数，说明栈顶的元素无法向右继续延伸了，用现在的位置减去它的左边界就是最大的宽度
* 同时距离去掉的最左边边界，这就是新元素的左边界了（去掉的都比新元素大）

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int N = 1e5 + 10;
ll w[N],a[N];
int main(){
    ios::sync_with_stdio(false);//写了using namespace std;
    ll n;cin >> n;
    while(n){
        stack<pair<ll,ll>> st;
        ll ma = 0;
        for(int i = 1;i <= n;i++){
            ll x;cin >> x;
            ll l = i;
            while(!st.empty() && x < st.top().first){
                ma = max(ma,(i - st.top().second)*st.top().first);
                l = min(st.top().second,l);
                st.pop();
            }
            st.push({x,l});
        }
        while(!st.empty()){
            ma = max(ma,(n + 1 - st.top().second)*st.top().first);
            st.pop();
        }
        cout << ma << endl;
        cin >> n;
    }
}
```

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
int n,m;
const int N = 2e3 + 10;
bool mp[N][N];
int dp[2][N][N];
int a[N];
signed main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	cin >> n >> m;
	for(int i = 1;i <= n;i++)
	for(int j = 1;j <= m;j++){
		cin >> mp[i][j];
		mp[i][j] ^= ((i + j) % 2);
	}
	int ma1 = 0,ma2 = 0;
	bool f = 1;
	for(int i = 1;i <= n;i++){
		for(int j = 1;j <= m;j++){
			if(mp[i][j])
				dp[1][i][j] = dp[1][i - 1][j] + 1;
			else
				dp[0][i][j] = dp[0][i - 1][j] + 1;
		}
		stack<pair<int,int>> st;
		for(int j = 1;j <= m;j++){
			int l = j;
			while(!st.empty() && dp[1][i][j] < st.top().first){
				int wid = j - st.top().second;
				int h = st.top().first;
				ma1 = max(min(h,wid)*min(h,wid),ma1);
				ma2 = max(h*wid,ma2);
				l = st.top().second;
				st.pop();
			}
			st.push({dp[1][i][j],l});
		}
		while(!st.empty()){
			int wid = m + 1 - st.top().second;
			int h = st.top().first;
			ma1 = max(min(h,wid)*min(h,wid),ma1);
			ma2 = max(h*wid,ma2);
			st.pop();
		}
		for(int j = 1;j <= m;j++){
			int l = j;
			while(!st.empty() && dp[0][i][j] < st.top().first){
				int wid = j - st.top().second;
				int h = st.top().first;
				ma1 = max(min(h,wid)*min(h,wid),ma1);
				ma2 = max(h*wid,ma2);
				
				l = st.top().second;
				st.pop();
			}
			st.push({dp[0][i][j],l});
		}
		while(!st.empty()){
			int wid = m + 1 - st.top().second;
			int h = st.top().first;
			ma1 = max(min(h,wid)*min(h,wid),ma1);
			ma2 = max(h*wid,ma2);
			st.pop();
		}
	}
	cout << ma1 << endl << ma2 << endl;
}
```

### 最长相同子序列

* [L - Common Subsequence](https://vjudge.net/contest/567292#problem/L)
* $dp[i][j]$第一个字符串枚举到那里，第二个，记录长度

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 1e3 + 10;

int dp[N][N];

int main()
{
	string s1,s2;
	while(cin >> s1 >> s2){
		int n = s1.size(),m = s2.size();
		memset(dp,0,sizeof(dp));
		s1 = ' ' + s1,s2 = ' ' + s2;
		for(int j = 1;j <= n;j++)
			for(int k = 1;k <= m;k++){
				if(s1[j] == s2[k]){
					dp[j][k] = dp[j - 1][k - 1] + 1;
				}else{
					dp[j][k] = max(dp[j - 1][k],dp[j][k - 1]);
				}
			}
		cout << dp[n][m] << endl;
	}
	
}
```

## 数位dp

* 要求统计满足一定条件的数的数量
* 这些条件经过转换可以使用数位的思想去理解和判断
* 输入会提供一个数字区间统计
* 一般用$ans[0,r]-ans[0,l-1]$来统计$r$到$l$内的答案
* 记忆化搜索pre0，limit，pos：前导0，限制，

### [P2602 [ZJOI2010] 数字计数](https://www.luogu.com.cn/problem/P2602)

* 计算$[l,r]$内所有数$[0,9]$出现的次数

* 真麻烦呢
* 希望能理清思路

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

ll sum[13];//前面位确定，后面的特定数出现次数
ll _10[13];

void pre(){
	_10[0] = 1;
	sum[1] = 1;
	_10[1] = 10;
	for(int i = 2;i <= 12;i++){
		sum[i] = sum[i - 1] * 10 + _10[i - 1];
		_10[i] = _10[i - 1] * 10;
	}
}

int p[13];

ll ans[10];
ll anstem[10];

ll val[14];

void get(ll x){
	memset(ans,0,sizeof(ans));
	int cnt = 0;
	while(x){
		p[++cnt] = x % 10;
		x /= 10;
	}
	val[cnt + 1] = 0;
	for(int j = 1;j <= cnt;j++) val[j] = val[j - 1] + p[j]*_10[j - 1];
	for(int i = cnt;i >= 1;i--){
		//全0
		if(i != cnt){
			for(int j = 0;j <= 9;j++){
				ans[j] += sum[i - 1] * 9;
				if(j != 0)
					ans[j] += _10[i - 1];
			}
		}
		//放小于p[i]的数
		if(p[i] >= 2){
			for(int j = 0;j <= 9;j++){
				ans[j] += (p[i] - 1) * sum[i - 1];
			}
		}
		for(int j = 1;j < p[i];j++){
			ans[j] += _10[i - 1];
		}
		if(p[i] != 0 && i != cnt){
			for(int j = 0;j <= 9;j++)
				ans[j] += sum[i - 1];
			ans[0] += _10[i - 1];
		}
		//放p[i]
		ans[p[i]] += val[i - 1] + 1;
	}
}

void test(){
	ll l,r;cin >> l >> r;
	get(l - 1);
	for(int i = 0;i <= 9;i++) anstem[i] = ans[i];
	get(r);
	for(int i = 0;i <= 9;i++) cout << ans[i] - anstem[i] << ' ';
}

int main(){
	IOS
	pre();
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

### [不要62](https://vjudge.net/problem/HDU-2089)

* $[l,r]$内不含4与连续的62的数的数量
* 记忆化搜索法，状态量有前面一个是否为6，是否有前导0，前面是否与x相同

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

int dp[2][2][2][8];

int p[8];

int cnt = 0;

int num[11] = {0,0,1,2,3,3,4,4,5,6,7};

int asknum(bool f,int x){
	int ans = x;
	if(x > 4) ans--;
	if(f && x > 2) ans--;
	if(x > 6) ans--;
	return ans;
}

int dfs(bool _6,bool pre0,bool limit,int pos){
	if(pos == 0) return 1;
	if(dp[_6][pre0][limit][pos]) return dp[_6][pre0][limit][pos];
	int ans = 0;
	if(pos == cnt){//第1位
		ans += dfs(0,1,0,pos - 1);//放0
		ans += dfs(0,0,0,pos - 1) * num[p[pos]];//放小于p[pos]但不等于6与4的数
		if(p[pos] == 6) ans += dfs(1,0,1,pos - 1);
		else if(p[pos] != 4) ans += dfs(0,0,1,pos - 1);
		if(p[pos] > 6) ans += dfs(1,0,0,pos - 1);
	}else if(limit){
		ans += dfs(0,0,0,pos - 1) * asknum(_6,p[pos]);
		if(p[pos] > 6) ans += dfs(1,0,0,pos - 1);
		if(p[pos] == 4 || (_6 && p[pos] == 2));
		else if(p[pos] == 6) ans += dfs(1,0,1,pos - 1); 
		else ans += dfs(0,0,1,pos - 1);
	}else if(pre0){
		ans += dfs(0,1,0,pos - 1);
		ans += dfs(0,0,0,pos - 1) * 7;
		ans += dfs(1,0,0,pos - 1);
	}else{
		ans += dfs(0,0,0,pos - 1) * asknum(_6,10);
		ans += dfs(1,0,0,pos - 1);
	}
	return ans;
}

int f_p(int x){
	memset(dp,0,sizeof(dp));
	cnt = 0;
	while(x){
		p[++cnt] = x % 10;
		x /= 10;
	}
	return dfs(0,0,0,cnt);
}

void test(){
	int n,m;
	while((cin >> n >> m),n){
		int ansn = f_p(n - 1);
		int ansm = f_p(m);
		// debug(ansn);
		// debug(ansm);
		cout << ansm - ansn << '\n';
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

### [#10165. 「一本通 5.3 例 3」Windy 数](https://loj.ac/p/10165)

### 题意：求不含前导零且相邻两个数字之差至少为 2 的正整数

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

int p[11];

int dp[2][2][11][11];//pos位置前面填pre数的数量

int cnt;

int dfs(bool pre0,bool limit,int pos,int pre){
	if(pos == 0) return 1;
	if(dp[pre0][limit][pos][pre])  return dp[pre0][limit][pos][pre];
	int ans = 0;
	if(pos == cnt){
		ans += dfs(1,0,pos - 1,0);
		ans += dfs(0,1,pos - 1,p[pos]);
		for(int i = 1;i < p[pos];i++)
			ans += dfs(0,0,pos - 1,i);
	}else if(pre0){
		ans += dfs(1,0,pos - 1,0);
		for(int i = 1;i <= 9;i++)
			ans += dfs(0,0,pos - 1,i);
	}else if(limit){
		if(abs(p[pos] - pre) >= 2) ans += dfs(0,1,pos - 1,p[pos]);
		for(int i = 0;i < p[pos];i++){
			if(abs(i - pre) >= 2) ans += dfs(0,0,pos - 1,i);
		}
	}else{
		for(int i = 0;i <= 9;i++)
			if(abs(i - pre) >= 2)
				ans += dfs(0,0,pos - 1,i);
	}
	return dp[pre0][limit][pos][pre] = ans;
}

int ask(int x){
	cnt = 0;
	memset(dp,0,sizeof(dp));
	while(x){
		p[++cnt] = x % 10;
		x /= 10;
	}
	return dfs(1,1,cnt,0);
}

void test(){
	int a,b;cin >> a >> b;
	cout << ask(b) - ask(a - 1) << endl;
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

### [P3311 数数](https://www.luogu.com.cn/problem/P3311)

* 求不包含m种序列，小于等于n的数的数量
* $dp[pre0][limit][x][pos]$记忆化搜索，$x$代表在tire树中的位置，pos代表在n中的位置
* 预处理出每个位置是否包含m种序列，然后记忆化搜索即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int mod = 1e9 + 7;

const int N = 1.5e3 + 10;

int ptop = 0;

bool f[2][2][1510][1220];
ll dp[2][2][1510][1220];//pre0,limit,x,pos

struct c_tree{
	int tr[26];
	int nex;
	bool f;
}p[N];

void add(string s){
	int pos = 0;
	for(auto c : s){
		if(p[pos].tr[c - '0'] == 0){
			p[pos].tr[c - '0'] = ++ptop;
		}
		pos = p[pos].tr[c - '0'];
	}
	p[pos].f = 1;
}

bool _f[1550];//判断这个点是否为标记点
int nex[1550][26];

void kmp_tr(){
	queue<int> qu;
	for(int i = 0;i < 26;i++){
		if(p[0].tr[i]) qu.push(p[0].tr[i]);
	}
	while(!qu.empty()){
		int x = qu.front();
		qu.pop();
		for(int i = 0;i < 26;i++){
			if(p[x].tr[i]){
				p[p[x].tr[i]].nex = p[p[x].nex].tr[i];
				qu.push(p[x].tr[i]);
			}else
				p[x].tr[i] = p[p[x].nex].tr[i];
		}
	}
	for(int i = 1;i <= ptop;i++){
		int x = i;
		while(x){
			if(p[x].f) {_f[i] = 1;break;}
			else x = p[x].nex;
		}
	}
}

string s;

int cnt;

ll dfs(bool pre0,bool limit,int x,int pos){
	if(_f[x]) return 0;
	if(pos == 0) return 1;
	if(f[pre0][limit][x][pos]) return dp[pre0][limit][x][pos];
	ll ans = 0;
	if(pos == cnt){//第1位
		ans += dfs(1,0,0,pos - 1);
		ans += dfs(0,1,p[x].tr[s[pos]],pos - 1);
		for(int i = 1;i < s[pos];i++) ans += dfs(0,0,p[x].tr[i],pos - 1);
	}else if(pre0){
		ans += dfs(1,0,0,pos - 1);
		for(int i = 1;i <= 9;i++) ans += dfs(0,0,p[x].tr[i],pos - 1);
	}else if(limit){
		ans += dfs(0,1,p[x].tr[s[pos]],pos - 1);
		for(int i = 0;i < s[pos];i++) ans += dfs(0,0,p[x].tr[i],pos - 1);
	}else{
		for(int i = 0;i <= 9;i++) ans += dfs(0,0,p[x].tr[i],pos - 1);
	}
	f[pre0][limit][x][pos] = 1;
	return dp[pre0][limit][x][pos] = ans % mod;
}

ll get(){
	reverse(s.begin(),s.end());
	cnt = s.size();
	s = ' ' + s;
	for(int i = 1;i <= cnt;i++) s[i] -= '0';
	return dfs(0,0,0,cnt);
}

void test(){
	cin >> s;
	int m;cin >> m;
	while(m--){
		string ms;cin >> ms;
		add(ms);
	}
	kmp_tr();
	cout << get() - 1 << endl;
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

### dp + 容斥原理

* #### [P1450 [HAOI2008] 硬币购物](https://www.luogu.com.cn/problem/P1450) 

#### 题意：给4种不同硬币，每次给出这四种硬币的数量与要凑的数s，问有多少种凑法

#### 思路：如果不考虑四种硬币限制，那么直接对四种硬币完全背包即可，然后输出$dp[s]$即可

#### 那么如果考虑那么我们就要减去一种硬币超过的情况再加上两种硬币超过的情况....，那么怎么考虑超过的情况呢

#### 如果给了$d[i]$个硬币，那么我们取$d[i] + 1$就可以，那么对应的计数为$dp[s-(d[i] + 1)*c[i]]$，用容斥原理枚举$2^4$即可

~~很酷~~

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int S = 1e5 + 10;
int c[5],d[5],s;
ll dp[S];
int main()
{
	dp[0] = 1;
	for(int i = 1;i <= 4;i++){
		cin >> c[i];
		for(int j = c[i];j <= S - 10;j++){
			dp[j] += dp[j - c[i]];
		}
	}
	int n;cin >> n;
	while(n--){
		for(int i = 1;i <= 4;i++)
			cin >> d[i];
		cin >> s;
		ll ans = 0;
		for(int i = 0;i < (1 << 4);i++){
			int u = s,f = 1;
			for(int j = 1;j <= 4;j++){
				
				if(i & (1 << (j - 1))){
					f = -f;
					u -= (d[j] + 1) * c[j];
				}
			}
			if(u >= 0)
				ans += f*dp[u];
		}
		cout << ans << endl;
	}
}
```

### dp计数，集合取数得特定和

* [**F - #(subset sum = K) with Add and Erase**](https://atcoder.jp/contests/abc321/tasks/abc321_f)

#### 如果要求的目标方案较小，那么只要计数到目标方案即可，对于取消而言也是先取消前面的，只有已经取消的才能取消选择的，那么只有dp[0]是本来就没有被选择的，因此取消也是从前往后到目标方案

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int mod = 998244353,N = 5e3 + 10;

int dp[N];

void test(){
	int q,k;
	cin >> q >> k;
	dp[0] = 1;
	while(q--){
		char op;cin >> op;
		int x;cin >> x;
		if(op == '+'){
			for(int i = 5000;i >= x;i--){
				dp[i] = (dp[i] + dp[i - x]) % mod;
			}
		}else{
			for(int i = x;i <= 5000;i++){
				dp[i] = (dp[i] - dp[i - x] + mod) % mod;
			}
		}
		cout << dp[k] << endl;
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

## 从一道轮廓线状压dp得出枚举操作次序与状态顺序的dp区别

## [Mondriaan's Dream](https://vjudge.net/problem/HDU-1400)

* 问题描述：你可以横着放$1*2$的方块，或者竖着放$2*1$的方块，问填满n\*m的区域的方案数
* 用$dp[i][j]$描述第$i$行填满后第$i+1$的状态
* 从第$i$位转移到第$i+1$位，先假设全是竖着放的，那么有转移方程$dp[i][j] = dp[i - 1][sum - 1 - j]$
* 枚举位数$k$，如果$j$的第$k$位与第$k+1$位都是1，那么说明这个也可以换成横着放的，因此有$dp[i][j]=dp[i][j \oplus f(k)]$
* 如果先枚举下一位状态，再枚举取消的位数，那么就有可能状态A从B转移到C与从D转移到状态C导致计数重复，因此要先枚举操作，即取消的位数，再枚举状态，才不会计数重复

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define sum (1 << w)
#define f(x) (1 << (x))
#define ll long long

const int N = 12;

ll dp[N][1 << N];

void test(){
	int h,w;
	while(cin >> h >> w,h != 0){
		memset(dp,0,sizeof(dp));
		dp[0][0] = 1;//第0行填完,第一行什么都没有为初始状态
		for(int i = 1;i <= h;i++){//填这一行
			for(int s = sum;s >= 0;s--) dp[i][s] += dp[i - 1][sum - 1 - s];
			for(int j = 1;j <= w;j++){
				for(int s = sum;s >= 0;s--){
					if((s >> (j - 1)) & 1){//这一位有
						if((s >> j) & 1) dp[i][s ^ f(j - 1) ^ f(j)] += dp[i][s];
						//下一位也有,那么可以转移为取消的
						//因为是一位一位取消的，就不会出现取消先取消前面再取消后面与先取消后面再取消前面导致重复的情况
					}
				}
			}
		}
		cout << dp[h][0] << '\n';
	}
}

int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

* 该题的特征就是对应每个状态来说，它要转移到的状态较多，很难在一次循环中得到，并且它中途会经过不同的状态再回到同一个状态，那么我们枚举不同的转移操作就可以限制操作的顺序从而去除重复的计数
* 1.难以确定每个状态的所有转移目标状态
* 2.一个状态可以通过不同路径到达同一状态（但不能重复计数）
* 3.对于相同的初始状态与目标状态，中间的转移过程可以交换，不影响目标状态
* 那么就可以限制转移过程的顺序来dp

# 数据结构

## 树链剖分

### 结合树与线段树，实现树链$log_2n$的操作复杂度

### 步骤

* 建树，建点记录：size，fa，son，dfs，top，dep
* 第一步搜索得出size，fa，son（size最大的子树），dep
* 第二步搜索得出dfs与top，建立根据son的树链
* 第三步根据dfs序将树点数据放入线段树数据中，建立线段树

### [P3384 【模板】重链剖分/树链剖分](https://www.luogu.com.cn/problem/P3384)

```cpp
#include<bits/stdc++.h>
using namespace std;

#define ll long long
#define lson (x << 1)
#define rson (x << 1 | 1)

const int N = 1e5 + 10;

int n,m,r,_p;

struct edge{
	int v,to;
}ed[N*2];
int ptop = 0;
int head[N];
void add(int u,int v){
	ptop++;
	ed[ptop].v = v;
	ed[ptop].to = head[u];
	head[u] = ptop;
}

struct point{
	int fa,son,siz,dfs,top,dep;
}p[N];

int rnk[N];

void dfs1(int u,int dad){
	p[u].dep = p[dad].dep + 1;
	p[u].fa = dad;
	p[u].siz = 1;
	int ma = 0;
	for(int it = head[u];it;it = ed[it].to){
		if(ed[it].v == dad) continue;
		dfs1(ed[it].v,u);
		p[u].siz += p[ed[it].v].siz;
		if(p[ma].siz < p[ed[it].v].siz) ma = ed[it].v;
	}
	p[u].son = ma;//最大的子树计为重子
}

int _dfs = 0;

void dfs2(int u){
	p[u].dfs = ++_dfs;
	rnk[_dfs] = u;//dfs序
	if(p[u].son){//存在重子就继续延长链
		p[p[u].son].top = p[u].top;
		dfs2(p[u].son);//先跑重子那个子树
	}
	for(int it = head[u];it;it = ed[it].to){
		if(ed[it].v == p[u].fa || ed[it].v == p[u].son) continue;
		p[ed[it].v].top = ed[it].v;//作为新的链top
		dfs2(ed[it].v);
	}
}

ll a[N];
class tree{
	public:
	ll sum[N*4];//代表的和
	ll ma[N*4];//代表的最大值
	ll mi[N*4];//代表的最小值
	bool fadd[N*4];//是否启用标签
	ll ladd[N*4];//加法标签
	bool fturn[N*4];
	ll lturn[N*4];//变换标签
	ll l[N*4],r[N*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = a[_l];
			ma[x] = a[_l];
			mi[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void add(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			ladd[x] += v;
			fadd[x] = 1;
			ma[x] += v;
			mi[x] += v;
			sum[x] += v*(r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				add(_l,_r,lson,v);
			if(_r >= mid + 1)
				add(_l,_r,rson,v);
			update(x);
		}
	}
	void turn(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			lturn[x] = v;
			fturn[x] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
			ma[x] = v;
			mi[x] = v;
			sum[x] = v * (r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				turn(_l,_r,lson,v);
			if(_r >= mid + 1)
				turn(_l,_r,rson,v);
			update(x);
		}
	}
	void update(int x){
		ma[x] = max(ma[lson],ma[rson]);
		mi[x] = min(mi[lson],mi[rson]);
		sum[x] = sum[lson] + sum[rson];
	}
	void down(int x){
		if(fturn[x])
		{
			fturn[lson] = fturn[rson] = 1;
			lturn[lson] = lturn[rson] = lturn[x];
			ma[lson] = ma[rson] = lturn[x];
			mi[lson] = mi[rson] = lturn[x];
			sum[lson] = lturn[x]*(r[lson]-l[lson]+1);
			sum[rson] = lturn[x]*(r[rson]-l[rson]+1);
			fadd[lson] = fadd[rson] = 0;
			fturn[x] = 0;
		}
		if(fadd[x])
		{
			fadd[lson] = fadd[rson] = 1;
			ma[lson] += ladd[x];
			ma[rson] += ladd[x];
			mi[lson] += ladd[x];
			mi[rson] += ladd[x];
			sum[lson] += ladd[x]*(r[lson]-l[lson]+1);
			sum[rson] += ladd[x]*(r[rson]-l[rson]+1);
			ladd[lson] += ladd[x];
			ladd[rson] += ladd[x];
			fadd[lson] = fadd[rson] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
		}
	}
	ll findma(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return ma[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll ma = 0;
			if(_l <= mid)
				ma = findma(_l,_r,lson);
			if(_r >= mid + 1)
				ma = max(ma,findma(_l,_r,rson));
			update(x);
			return ma;
		}
	}
	ll findmi(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return mi[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll mi = 1e9 + 10;
			if(_l <= mid)
				mi = findmi(_l,_r,lson);
			if(_r >= mid + 1)
				mi = min(mi,findmi(_l,_r,rson));
			update(x);
			return mi;
		}
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum += findsum(_l,_r,lson);
			if(_r >= mid + 1)
				sum += findsum(_l,_r,rson);
			return sum;
			update(x);
		}
	}
}tr;

int b[N];

int main(){
	cin >> n >> m >> r >> _p;
	for(int i = 1;i <= n;i++) cin >> b[i];
	for(int i = 1;i < n;i++){
		int u,v;cin >> u >> v;
		add(u,v);
		add(v,u);
	}
	dfs1(r,0);
	dfs2(r);
	for(int i = 1;i <= n;i++) a[p[i].dfs] = b[i];
	tr.build(1,n,1);
	for(int i = 1;i <= m;i++){
		int op;cin >> op;
		if(op == 1){
			int x,y,z;cin >> x >> y >> z;
			while(1){
				if(p[p[x].top].dep < p[p[y].top].dep) swap(x,y);
				if(p[x].top == p[y].top){
					if(p[x].dep > p[y].dep) swap(x,y);
					tr.add(p[x].dfs,p[y].dfs,1,z);
					break;
				}else{
					tr.add(p[p[x].top].dfs,p[x].dfs,1,z);
					x = p[p[x].top].fa;
				}
			}
		}else if(op == 2){
			int x,y;cin >> x >> y;
			ll ans = 0;
			while(1){
				if(p[p[x].top].dep < p[p[y].top].dep) swap(x,y);
				if(p[x].top == p[y].top){
					if(p[x].dep > p[y].dep) swap(x,y);
					ans += tr.findsum(p[x].dfs,p[y].dfs,1);
					break;
				}else{
					ans += tr.findsum(p[p[x].top].dfs,p[x].dfs,1);
					x = p[p[x].top].fa;
				}
			}
			cout << ans << endl;
		}else if(op == 3){
			int x,z;cin >> x >> z;
			tr.add(p[x].dfs,p[x].dfs + p[x].siz - 1,1,z);
		}else if(op == 4){
			int x;cin >> x;
			cout << tr.findsum(p[x].dfs,p[x].dfs + p[x].siz - 1,1) << endl;
		}
	}
}
```

### 取模版

```cpp
#include<bits/stdc++.h>
using namespace std;

#define ll long long
#define int long long
#define lson (x << 1)
#define rson (x << 1 | 1)

const int N = 1e5 + 10;

int n,m,r,_p;

struct edge{
	int v,to;
}ed[N*2];
int ptop = 0;
int head[N];
void add(int u,int v){
	ptop++;
	ed[ptop].v = v;
	ed[ptop].to = head[u];
	head[u] = ptop;
}

struct point{
	int fa,son,siz,dfs,top,dep;
}p[N];

int rnk[N];

void dfs1(int u,int dad){
	p[u].dep = p[dad].dep + 1;
	p[u].fa = dad;
	p[u].siz = 1;
	int ma = 0;
	for(int it = head[u];it;it = ed[it].to){
		if(ed[it].v == dad) continue;
		dfs1(ed[it].v,u);
		p[u].siz += p[ed[it].v].siz;
		if(p[ma].siz < p[ed[it].v].siz) ma = ed[it].v;
	}
	p[u].son = ma;
}

int _dfs = 0;

void dfs2(int u){
	p[u].dfs = ++_dfs;
	rnk[_dfs] = u;
	if(p[u].son){
		p[p[u].son].top = p[u].top;
		dfs2(p[u].son);
	}
	for(int it = head[u];it;it = ed[it].to){
		if(ed[it].v == p[u].fa || ed[it].v == p[u].son) continue;
		p[ed[it].v].top = ed[it].v;
		dfs2(ed[it].v);
	}
}

ll a[N];
class tree{
	public:
	ll sum[N*4];//代表的和
	ll ma[N*4];//代表的最大值
	ll mi[N*4];//代表的最小值
	bool fadd[N*4];//是否启用标签
	ll ladd[N*4];//加法标签
	bool fturn[N*4];
	ll lturn[N*4];//变换标签
	ll l[N*4],r[N*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = a[_l];
			ma[x] = a[_l];
			mi[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void add(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			ladd[x] += v;
			fadd[x] = 1;
			ma[x] += v;
			mi[x] += v;
			sum[x] = (sum[x] + v*(r[x] - l[x] + 1)) % _p;
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				add(_l,_r,lson,v);
			if(_r >= mid + 1)
				add(_l,_r,rson,v);
			update(x);
		}
	}
	void turn(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			lturn[x] = v;
			fturn[x] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
			ma[x] = v;
			mi[x] = v;
			sum[x] = v * (r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				turn(_l,_r,lson,v);
			if(_r >= mid + 1)
				turn(_l,_r,rson,v);
			update(x);
		}
	}
	void update(int x){
		ma[x] = max(ma[lson],ma[rson]);
		mi[x] = min(mi[lson],mi[rson]);
		sum[x] = (sum[lson] + sum[rson]) % _p;
	}
	void down(int x){
		if(fturn[x])
		{
			fturn[lson] = fturn[rson] = 1;
			lturn[lson] = lturn[rson] = lturn[x];
			ma[lson] = ma[rson] = lturn[x];
			mi[lson] = mi[rson] = lturn[x];
			sum[lson] = lturn[x]*(r[lson]-l[lson]+1) % _p;
			sum[rson] = lturn[x]*(r[rson]-l[rson]+1) % _p;
			fadd[lson] = fadd[rson] = 0;
			fturn[x] = 0;
		}
		if(fadd[x])
		{
			fadd[lson] = fadd[rson] = 1;
			ma[lson] += ladd[x];
			ma[rson] += ladd[x];
			mi[lson] += ladd[x];
			mi[rson] += ladd[x];
			sum[lson] = (sum[lson] + ladd[x]*(r[lson]-l[lson]+1)) % _p;
			sum[rson] = (sum[rson] + ladd[x]*(r[rson]-l[rson]+1)) % _p;
			ladd[lson] = (ladd[lson] + ladd[x]) % _p;
			ladd[rson] = (ladd[rson] + ladd[x]) % _p;
			fadd[lson] = fadd[rson] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
		}
	}
	ll findma(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return ma[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll ma = 0;
			if(_l <= mid)
				ma = findma(_l,_r,lson);
			if(_r >= mid + 1)
				ma = max(ma,findma(_l,_r,rson));
			update(x);
			return ma;
		}
	}
	ll findmi(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return mi[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll mi = 1e9 + 10;
			if(_l <= mid)
				mi = findmi(_l,_r,lson);
			if(_r >= mid + 1)
				mi = min(mi,findmi(_l,_r,rson));
			update(x);
			return mi;
		}
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum = (sum + findsum(_l,_r,lson)) % _p;
			if(_r >= mid + 1)
				sum = (sum + findsum(_l,_r,rson)) % _p;
			return sum;
			update(x);
		}
	}
}tr;

int b[N];

signed main(){
	cin >> n >> m >> r >> _p;
	for(int i = 1;i <= n;i++) cin >> b[i];
	for(int i = 1;i < n;i++){
		int u,v;cin >> u >> v;
		add(u,v);
		add(v,u);
	}
	dfs1(r,0);
	dfs2(r);
	for(int i = 1;i <= n;i++) a[p[i].dfs] = b[i];
	tr.build(1,n,1);
	for(int i = 1;i <= m;i++){
		int op;cin >> op;
		if(op == 1){
			int x,y,z;cin >> x >> y >> z;
			while(1){
				if(p[p[x].top].dep < p[p[y].top].dep) swap(x,y);
				if(p[x].top == p[y].top){
					if(p[x].dep > p[y].dep) swap(x,y);
					tr.add(p[x].dfs,p[y].dfs,1,z);
					break;
				}else{
					tr.add(p[p[x].top].dfs,p[x].dfs,1,z);
					x = p[p[x].top].fa;
				}
			}
		}else if(op == 2){
			int x,y;cin >> x >> y;
			ll ans = 0;
			while(1){
				if(p[p[x].top].dep < p[p[y].top].dep) swap(x,y);
				if(p[x].top == p[y].top){
					if(p[x].dep > p[y].dep) swap(x,y);
					ans = (ans + tr.findsum(p[x].dfs,p[y].dfs,1)) % _p;
					break;
				}else{
					ans = (ans + tr.findsum(p[p[x].top].dfs,p[x].dfs,1)) % _p;
					x = p[p[x].top].fa;
				}
			}
			cout << ans << endl;
		}else if(op == 3){
			int x,z;cin >> x >> z;
			tr.add(p[x].dfs,p[x].dfs + p[x].siz - 1,1,z);
		}else if(op == 4){
			int x;cin >> x;
			cout << tr.findsum(p[x].dfs,p[x].dfs + p[x].siz - 1,1) % _p << endl;
		}
	}
}
```

### P2146 [NOI2015] [软件包管理器](https://www.luogu.com.cn/problem/P2146)

### 题意：除了0号安装包，其他安装包均会依赖另一个安装包（树形结构），q步操作，可能删除或者安装某个安装包，问每个操作会改变多少安装包的安装状态

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;
#define lson x << 1
#define rson x << 1 | 1

const int N = 1e5 + 10;

struct edge{
	int v,nex;
}ed[N*2];
int ptop = 0;
int head[N];
void add(int u,int v){
	ptop++;
	ed[ptop].v = v;
	ed[ptop].nex = head[u];
	head[u] = ptop;
}

struct point{
	int son,fa,sz,top,dfs,dep,rdfs;
}p[N];

void dfs1(int x){//fa,son,dep,sz
	p[x].sz = 1;
	int mason = -1;
	for(auto _p = head[x];_p;_p = ed[_p].nex){
		int v = ed[_p].v;
		p[v].dep = p[x].dep + 1;
		dfs1(v);
		p[x].sz += p[v].sz;
		if(mason == -1 || p[mason].sz < p[v].sz){
			mason = v;
		}
	}
	if(mason == -1) mason = 0;
	p[x].son = mason;
}

int _dfs = 0;
void dfs2(int x){//top,dfs,dson
	p[x].dfs = ++_dfs;
	if(p[x].son){
		p[p[x].son].top = p[x].top;
		dfs2(p[x].son);
	}
	for(int _p = head[x];_p;_p = ed[_p].nex){
		int v = ed[_p].v;
		if(v == p[x].son) continue;
		p[v].top = v;
		dfs2(v);
	}
	p[x].rdfs = _dfs;
}

int a[N] = {0};

class tree{
	public:
	ll sum[N*4];//代表的和
	ll ma[N*4];//代表的最大值
	ll mi[N*4];//代表的最小值
	bool fadd[N*4];//是否启用标签
	ll ladd[N*4];//加法标签
	bool fturn[N*4];
	ll lturn[N*4];//变换标签
	ll l[N*4],r[N*4];
	void build(int _l,int _r,int x)
	{
		l[x] = _l,r[x] = _r;
		if(_l == _r)
		{
			sum[x] = a[_l];
			ma[x] = a[_l];
			mi[x] = a[_l];
		}
		else
		{
			int mid = (_l+_r)/2;
			build(_l,mid,lson);
			build(mid + 1,_r,rson);
			update(x);
		}
	}
	void add(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			ladd[x] += v;
			fadd[x] = 1;
			ma[x] += v;
			mi[x] += v;
			sum[x] += v*(r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				add(_l,_r,lson,v);
			if(_r >= mid + 1)
				add(_l,_r,rson,v);
			update(x);
		}
	}
	void turn(int _l,int _r,int x,int v){
		if(_l <= l[x] && _r >= r[x])
		{
			lturn[x] = v;
			fturn[x] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
			ma[x] = v;
			mi[x] = v;
			sum[x] = v * (r[x] - l[x] + 1);
		}
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			if(_l <= mid)
				turn(_l,_r,lson,v);
			if(_r >= mid + 1)
				turn(_l,_r,rson,v);
			update(x);
		}
	}
	void update(int x){
		ma[x] = max(ma[lson],ma[rson]);
		mi[x] = min(mi[lson],mi[rson]);
		sum[x] = sum[lson] + sum[rson];
	}
	void down(int x){
		if(fturn[x])
		{
			fturn[lson] = fturn[rson] = 1;
			lturn[lson] = lturn[rson] = lturn[x];
			ma[lson] = ma[rson] = lturn[x];
			mi[lson] = mi[rson] = lturn[x];
			sum[lson] = lturn[x]*(r[lson]-l[lson]+1);
			sum[rson] = lturn[x]*(r[rson]-l[rson]+1);
			fadd[lson] = fadd[rson] = 0;
			fturn[x] = 0;
		}
		if(fadd[x])
		{
			fadd[lson] = fadd[rson] = 1;
			ma[lson] += ladd[x];
			ma[rson] += ladd[x];
			mi[lson] += ladd[x];
			mi[rson] += ladd[x];
			sum[lson] += ladd[x]*(r[lson]-l[lson]+1);
			sum[rson] += ladd[x]*(r[rson]-l[rson]+1);
			ladd[lson] += ladd[x];
			ladd[rson] += ladd[x];
			fadd[lson] = fadd[rson] = 1;
			fadd[x] = 0;
			ladd[x] = 0;
		}
	}
	ll findma(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return ma[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll ma = 0;
			if(_l <= mid)
				ma = findma(_l,_r,lson);
			if(_r >= mid + 1)
				ma = max(ma,findma(_l,_r,rson));
			update(x);
			return ma;
		}
	}
	ll findmi(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return mi[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll mi = 1e9 + 10;
			if(_l <= mid)
				mi = findmi(_l,_r,lson);
			if(_r >= mid + 1)
				mi = min(mi,findmi(_l,_r,rson));
			update(x);
			return mi;
		}
	}
	ll findsum(int _l,int _r,int x)
	{
		if(_l <= l[x] && _r >= r[x])
			return sum[x];
		else
		{
			down(x);
			int mid = (l[x] + r[x]) / 2;
			ll sum = 0;
			if(_l <= mid)
				sum += findsum(_l,_r,lson);
			if(_r >= mid + 1)
				sum += findsum(_l,_r,rson);
			return sum;
			update(x);
		}
	}
}tr;

void test(){
	int n;cin >> n;
	for(int i = 2;i <= n;i++){
		int x;cin >> x;
		x++;
		add(x,i);
		p[i].fa = x;
	}
	p[1].top = 1;
	p[1].fa = 0;
	dfs1(1);
	dfs2(1);
	tr.build(1,n,1);
	int q;cin >> q;
	while(q--){
		string s;cin >> s;
		int x;cin >> x;
		x++;
		if(s[0] == 'i'){
			int ans = 0;
			do{
				int y = p[x].top;
				int l = p[y].dfs,r = p[x].dfs;
				ans += r - l + 1 - tr.findsum(l,r,1);
				tr.turn(l,r,1,1);
				x = p[y].fa;
			}while(x);
			cout << ans << endl;
		}else{
			cout << tr.findsum(p[x].dfs,p[x].rdfs,1) << endl;
			tr.turn(p[x].dfs,p[x].rdfs,1,0);
		}
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

## 利用带权并查集解决集合矛盾问题

### [P2024 [NOI2001] 食物链](https://www.luogu.com.cn/problem/P2024)

* 记录该节点的父节点相对自己的类型，一个集合将所有与它有关系的全记录下来，合并集合就是看是否矛盾，通过取余代换

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 5e4 + 10;

int fa[N],type[N];

void init(int n){
	for(int i = 1;i <= n;i++)
		fa[i] = i;
}

int find(int x){
	if(fa[x] == x) return x;
	int gfa = find(fa[x]);
	type[x] = (type[x] + type[fa[x]]) % 3;
	return fa[x] = gfa;
}

int main(){
	int n,k;cin >> n >> k;
	int ans = 0;
	init(n);
	for(int i = 1;i <= k;i++){
		int op,x,y;cin >> op >> x >> y;
		if(x > n || y > n) ans++;
		else if(op == 1){
			int fx = find(x),fy = find(y);
			if(fx == fy && type[x] != type[y])
				ans++;
			else{
				fa[fx] = fy;
				type[fx] = (3 + type[y] - type[x]) % 3;
			}
		}else{
			int fx = find(x),fy = find(y);
			if(x == y) ans++;
			else if(fx == fy && type[x] != (type[y] + 1) % 3){
				ans++;
			}else{
				fa[fx] = fy;
				type[fx] = (4 + type[y] - type[x]) % 3;
			}
		}
	}
	cout << ans << endl;
}
```

## K-D Tree

### 主要思想：将空间分割为许多份，方便查询内部的点，优先查询比较可能的区间，再查询可能性较低的区间，排除较多不可能区间即内部点，实现更少的比较

### 具体实现：

### 前置函数：nth_element

### 作用：取第k小的数放在k位置，让前面的都小于等于它，后面的都比它大于等于它，但乱序

### 复杂度：$O(n)$

### 那么在我们的K-D Tree中就可以用该函数将节点分割而不用sort($O(nlogn)$)

### *1.存点:*需要k维的数据，读入，还有距离函数（具体问题具体分析），存n个点与选择进行的点 

```cpp
struct point{
	double x[2];//展示的为二维
	void read(){
		cin >> x[0] >> x[1];//读入
	}
	friend double dis(const point &a,const point &b){//距离函数
		return _pow(a.x[0] - b.x[0]) + _pow(a.x[1] - b.x[1]);
	}
}p[N],no;
```

### *2.cmp函数：*以什么维度来排序

```cpp
int D;
inline bool cmp(const point &a,const point &b){//排序
	if(a.x[D] == b.x[D])
		return a.x[D^1] < b.x[D^1];
	return a.x[D] < b.x[D];
}
```

### *3.树节点：*左右儿子节点，维度范围

```cpp
struct KD_tree{
	double mx[2],mi[2];//二维的范围
	int l,r;//左右子节点
}tr[N];
```

### *4.更新函数：*子节点更新范围后要扩大父节点的范围

```cpp
void updata(int fa,int son){//给父节点更新
	chmax(tr[fa].mx[0],tr[son].mx[0]);
	chmax(tr[fa].mx[1],tr[son].mx[1]);
	chmin(tr[fa].mi[0],tr[son].mi[0]);
	chmin(tr[fa].mi[1],tr[son].mi[1]);
}
void updata(int fa,point &a){//赋初值
	tr[fa].mx[0] = tr[fa].mi[0] = a.x[0];
	tr[fa].mx[1] = tr[fa].mi[1] = a.x[1];
}
```

### *5.建树*

```cpp
int build(int l,int r){
	int mid = (l + r) >> 1;
	nth_element(p + l,p + mid,p + r + 1,cmp);//确定中间点
	D ^= 1;//换维度
	updata(mid,p[mid]);//给树范围先定在对应的点
	if(mid > l) updata(mid,tr[mid].l = build(l,mid - 1));
	if(mid < r) updata(mid,tr[mid].r = build(mid + 1,r));
	return mid;
}
```

### *6.估值*

```cpp
double f_v(int x){//现在的点到指定范围的最小距离(也可以改成最大)
	if(x == 0)
		return INF;
	double x0 = 0;
	if(no.x[0] > tr[x].mx[0]) x0 = no.x[0] - tr[x].mx[0];
	if(no.x[0] < tr[x].mi[0]) x0 = tr[x].mi[0] - no.x[0];
	x0 = _pow(x0);
	if(no.x[1] > tr[x].mx[1]) return _pow(tr[x].mx[1] - no.x[1]) + x0;
	if(no.x[1] < tr[x].mi[1]) return _pow(tr[x].mi[1] - no.x[1]) + x0;
	return x0;
}
```

### *7.询问*

```cpp
void query(int x){
	chmin(ans,dis(no,p[x]));
	double vl = f_v(tr[x].l),vr = f_v(tr[x].r);
	if(vl < vr){//先搜最可能的
		if(vl < ans){
			query(tr[x].l);
			if(vr < ans)
				query(tr[x].r);
		}
	}else{
		if(vr < ans){
			query(tr[x].r);
			if(vl < ans)
				query(tr[x].l);
		}
	}
}
```

## 例题：

### [P1429 平面最近点对（加强版）](https://www.luogu.com.cn/problem/P1429)

### [P7883 平面最近点对（加强加强版）](https://www.luogu.com.cn/problem/P7883)

### [P6247 [SDOI2012] 最近最远点对](https://www.luogu.com.cn/problem/P6247)

### ~~[P1452 [USACO03FALL] Beauty Contest G /【模板】旋转卡壳](https://www.luogu.com.cn/problem/P1452)~~

### 对于这些最近最远点均可用该方法来做

### 最近为query1，ans1，f_v1；最远为query2，ans2，f_v2；问平方就将double define为long long然后将%.4lf改成%lld即可，问距离就反过来该回去

```cpp
#include<bits/stdc++.h>
using namespace std;
#define double long long
const int N = 4e5 + 10;
const double INF = 1e18;

int n;

inline double _pow(double x){
	return x*x;
}
void chmin(double &a,const double &b){
	a = a < b ? a : b;
}
void chmax(double &a,const double &b){
	a = a > b ? a : b;
}

struct point{
	double x[2];
	void read(){
		cin >> x[0] >> x[1];
	}
	friend double dis(const point &a,const point &b){
		return _pow(a.x[0] - b.x[0]) + _pow(a.x[1] - b.x[1]);
	}
}p[N],no;

int D;
inline bool cmp(const point &a,const point &b){
	if(a.x[D] == b.x[D])
		return a.x[D^1] < b.x[D^1];
	return a.x[D] < b.x[D];
}

struct KD_tree{
	double mx[2],mi[2];
	int l,r;
}tr[N];
void updata(int fa,int son){
	chmax(tr[fa].mx[0],tr[son].mx[0]);
	chmax(tr[fa].mx[1],tr[son].mx[1]);
	chmin(tr[fa].mi[0],tr[son].mi[0]);
	chmin(tr[fa].mi[1],tr[son].mi[1]);
}
void updata(int fa,point &a){
	tr[fa].mx[0] = tr[fa].mi[0] = a.x[0];
	tr[fa].mx[1] = tr[fa].mi[1] = a.x[1];
}

int build(int l,int r){
	int mid = (l + r) >> 1;
	nth_element(p + l,p + mid,p + r + 1,cmp);
	D ^= 1;
	updata(mid,p[mid]);//给树范围先定在对应的点
	if(mid > l) updata(mid,tr[mid].l = build(l,mid - 1));
	if(mid < r) updata(mid,tr[mid].r = build(mid + 1,r));
	return mid;
}

double f_v1(int x){
	if(x == 0)
		return INF;
	double x0 = 0;
	if(no.x[0] > tr[x].mx[0]) x0 = no.x[0] - tr[x].mx[0];
	if(no.x[0] < tr[x].mi[0]) x0 = tr[x].mi[0] - no.x[0];
	x0 = _pow(x0);
	if(no.x[1] > tr[x].mx[1]) return _pow(tr[x].mx[1] - no.x[1]) + x0;
	if(no.x[1] < tr[x].mi[1]) return _pow(tr[x].mi[1] - no.x[1]) + x0;
	return x0;
}

double ans1 = INF,ans2 = 0;
void query1(int x){
	chmin(ans1,dis(no,p[x]));
	double vl = f_v1(tr[x].l),vr = f_v1(tr[x].r);
	if(vl < vr){
		if(vl < ans1){
			query1(tr[x].l);
			if(vr < ans1)
				query1(tr[x].r);
		}
	}else{
		if(vr < ans1){
			query1(tr[x].r);
			if(vl < ans1)
				query1(tr[x].l);
		}
	}
}

double f_v2(int x){
	if(x == 0)
		return 0;
	return max(_pow(tr[x].mx[0] - no.x[0]),
				_pow(tr[x].mi[0] - no.x[0])) + 
			max(_pow(tr[x].mx[1] - no.x[1]),
				_pow(tr[x].mi[1] - no.x[1]));
}

void query2(int x){
	chmax(ans2,dis(no,p[x]));
	double vl = f_v2(tr[x].l),vr = f_v2(tr[x].r);
	if(vl > vr){
		if(vl > ans2){
			query2(tr[x].l);
			if(vr > ans2)
				query2(tr[x].r);
		}
	}else{
		if(vr > ans2){
			query2(tr[x].r);
			if(vl > ans1)
				query2(tr[x].l);
		}
	}
}

int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	cin >> n;
	for(int i = 1;i <= n;i++)
		p[i].read();
	int root = build(1,n);
	for(int i = 2;i <= n;i++){
		no = p[i];
		p[i] = p[i - 1];
		query2(root);
		p[i] = no;
	}
	printf("%lld",ans2);
}
```

### [P4357 [CQOI2016] K 远点对](https://www.luogu.com.cn/problem/P4357)

#### 虽然正解为旋转卡壳做$k/2$，每次做都去掉那两个距离最大的点并且加入与其他点的距离，并且K-D tree被最后一个hack数据卡了，但在没有毒瘤数据的情况下复杂度还是比较优秀的

#### 思路：先在小根堆优先队列内放k个0，然后每次查找都访问堆顶的元素，如果比堆顶元素大说明有可能是前k大的数，将堆顶pop掉，然后加入这个数，否则肯定在k名开外，不管它即可

#### 本来是2k个元素，将交换两点也考虑到，但是这样常数就比较大，因此优化为k个元素并且不考虑已经完全搜索过的点，并且因为是从前往后搜索的，每当最右边的访问都被访问过就说明它不可能有贡献了，就可以return了，不过即使这样优化+$O_2$，让前两个hack数据到$200ms$以内都无法通过最后一个hack数据，不过这样在正常题目中也够用了吧

```cpp
#include<bits/stdc++.h>
using namespace std;
#define double long long
#define ll long long
const int N = 1e5 + 10;
const double INF = 1e18;

int n,k;

inline double _pow(double x){
	return x*x;
}
void chmin(double &a,const double &b){
	a = a < b ? a : b;
}
void chmax(double &a,const double &b){
	a = a > b ? a : b;
}

struct point{
	double x[2];
	void read(){
		cin >> x[0] >> x[1];
	}
	friend double dis(const point &a,const point &b){
		return _pow(a.x[0] - b.x[0]) + _pow(a.x[1] - b.x[1]);
	}
}p[N],no;

int D;
inline bool cmp(const point &a,const point &b){
	if(a.x[D] == b.x[D])
		return a.x[D^1] < b.x[D^1];
	return a.x[D] < b.x[D];
}

struct KD_tree{
	double mx[2],mi[2];
	int l,r;
	int rr;
}tr[N];
void updata(int fa,int son){
	chmax(tr[fa].mx[0],tr[son].mx[0]);
	chmax(tr[fa].mx[1],tr[son].mx[1]);
	chmin(tr[fa].mi[0],tr[son].mi[0]);
	chmin(tr[fa].mi[1],tr[son].mi[1]);
}
void updata(int fa,point &a){
	tr[fa].mx[0] = tr[fa].mi[0] = a.x[0];
	tr[fa].mx[1] = tr[fa].mi[1] = a.x[1];
}

int build(int l,int r){
	int mid = (l + r) >> 1;
	tr[mid].rr = r;
	nth_element(p + l,p + mid,p + r + 1,cmp);
	D ^= 1;
	updata(mid,p[mid]);//给树范围先定在对应的点
	if(mid > l) updata(mid,tr[mid].l = build(l,mid - 1));
	if(mid < r) updata(mid,tr[mid].r = build(mid + 1,r));
	return mid;
}

/*double f_v1(int x){
	if(x == 0)
		return INF;
	double x0 = 0;
	if(no.x[0] > tr[x].mx[0]) x0 = no.x[0] - tr[x].mx[0];
	if(no.x[0] < tr[x].mi[0]) x0 = tr[x].mi[0] - no.x[0];
	x0 = _pow(x0);
	if(no.x[1] > tr[x].mx[1]) return _pow(tr[x].mx[1] - no.x[1]) + x0;
	if(no.x[1] < tr[x].mi[1]) return _pow(tr[x].mi[1] - no.x[1]) + x0;
	return x0;
}

double ans1 = INF,ans2 = 0;
void query1(int x){
	chmin(ans1,dis(no,p[x]));
	double vl = f_v1(tr[x].l),vr = f_v1(tr[x].r);
	if(vl < vr){
		if(vl < ans1){
			query1(tr[x].l);
			if(vr < ans1)
				query1(tr[x].r);
		}
	}else{
		if(vr < ans1){
			query1(tr[x].r);
			if(vl < ans1)
				query1(tr[x].l);
		}
	}
}*/

priority_queue<ll,vector<ll>,greater<ll> > qu;

inline double f_v2(int x){
	if(x == 0)
		return 0;
	return max(_pow(tr[x].mx[0] - no.x[0]),
				_pow(tr[x].mi[0] - no.x[0])) + 
			max(_pow(tr[x].mx[1] - no.x[1]),
				_pow(tr[x].mi[1] - no.x[1]));
}

bool use[N],unable[N];

inline void query2(int x){
	if(use[tr[x].rr]) return;
	if(!use[x]){
		ll tem = dis(no,p[x]);
		if(tem > qu.top()){
			qu.pop();
			qu.push(tem);
		}
	}
	double vl = f_v2(tr[x].l),vr = f_v2(tr[x].r);
	if(vl > vr){
		if(vl > qu.top()){
			query2(tr[x].l);
			if(vr > qu.top())
				query2(tr[x].r);
		}
	}else{
		if(vr > qu.top()){
			query2(tr[x].r);
			if(vl > qu.top())
				query2(tr[x].l);
		}
	}
}

int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	cin >> n >> k;
	for(int i = 1;i <= n;i++)
		p[i].read();
	for(int i = 1;i <= k;i++)
		qu.push(0);
	int root = build(1,n);
	no = p[1];
	p[1] = p[2];
	query2(root);
	p[1] = no;
	use[1] = 1;
	for(int i = 2;i <= n;i++){
		no = p[i];
		p[i] = p[i - 1];
		query2(root);
		p[i] = no;
		use[i] = 1;
	}
	printf("%lld",qu.top());
}
```

## 偏序问题

#### 本来我研究K-D Tree就是为了解决逆天的四维偏序问题，~~那么不好好干它怎么可以呢~~

#### ~~好像还是cdq套cdq比较快~~

#### 先来个简单的三维偏序

### [P3810 【模板】三维偏序（陌上花开）](https://www.luogu.com.cn/problem/P3810)

## 可持久化trie树

### 求最大异或和

给定一个非负整数序列 $\{a\}$，初始长度为 $N$。  

有 $M$ 个操作，有以下两种操作类型：  

1. `A x`：添加操作，表示在序列末尾添加一个数 $x$，序列的长度 $N$ 加 $1$。  
2. `Q l r x`：询问操作，你需要找到一个位置 $p$，满足 $l \le p \le r$，使得：$a[p] \oplus a[p+1] \oplus ... \oplus a[N] \oplus x$ 最大，输出最大值。

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 6e5 + 10,M = N * 30;//log(1e7) = 23.25

int n,m;
int s[N];
int tr[M][2],max_id[M];//树，可到的最大id(就是这个是以第几个为基的)
int root[N],idx;//不存内容的根节点，现在的最大id

void insert(int k,int p,int q){//现在的版本，上面完全相同的最大的树对应节点，现在的节点
	max_id[q] = k;
	for(int i = 23;i >= 0;i--){
		int v = s[k] >> i & 1;//第i + 1位为什么
		if(p) tr[q][v ^ 1] = tr[p][v ^ 1];//如果前颗树不是0，就连上前一颗树的对应不同的位
		tr[q][v] = ++idx;//正常给这个位置加上一个新节点
		max_id[tr[q][v]] = k;//新节点
		q = tr[q][v],p = tr[p][v];//跳转到新节点与相同最近节点
	}
}

int query(int l,int r,int C){
	int p = root[r];//从r开始找
	for(int i = 23;i >= 0;i--){
		int v = C >> i & 1;//这一位要什么
		if(max_id[tr[p][v ^ 1]] >= l)//在范围内可以跳转
			p = tr[p][v ^ 1];
		else
			p = tr[p][v];//否则就选差的
	}
	return C ^ s[max_id[p]];
}

int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	cin >> n >> m;
	
	s[0] = 0;
	max_id[0] = -1;//排除l-1为0可能会跳到0节点的可能性
	root[0] = ++idx;
	
	insert(0,0,root[0]);
	
	for(int i = 1;i <= n;i++){
		int x;cin >> x;
		s[i] = s[i - 1] ^ x;
		root[i] = ++idx;
		insert(i,root[i - 1],root[i]);
	}
	
	int l,r,x;
	while(m--){
		char c;cin >> c;
		if(c == 'A'){
			cin >> x;
			n++;
			root[n] = ++idx;
			s[n] = s[n - 1] ^ x;
			insert(n,root[n - 1],root[n]);
		}else{
			cin >> l >> r >> x;
			cout << query(l - 1,r - 1,x ^ s[n]) << endl;
		}
	}
}
```

## 树状数组实现区间加减与求和~~(不用写线段树啦)~~

### 用差分可以快速实现区间的快速加减，那么如何用差分求区间和呢？

### 求区间和可以转换为求前缀和相减，也就转换为差分求前缀和

* $a_1+a_2+a_3+...+a_n=d_1+(d_1+d_2)+(d_1+d_2+d_3)+...+(d_1+d_2+d_3+...+d_n)$
* $a_1+a_2+a_3+...+a_n=nd_1+(n-1)d_2+(n-2)d_3+...+d_n$

### 但是每个区间的n互不相同，所以要将它转换为有关i的数

* $a_1+a_2+a_3+...+a_n=nd_1+nd_2+nd_3+...+nd_n-(0d_1+1d_2+2d_3+...(n-1)d_n)$

### 因此就转换为了n倍的差分前缀和减去$(i-1)d_i$的前缀和

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 1e6 + 10;
ll n,q;
ll v[MAXN];
int lowbit(ll x)
{
	return x&-x;
}
ll tr_1[MAXN],tr_2[MAXN];
ll ask(ll tr[],ll x){
	ll ans = 0;
	for(;x >= 1;x -= lowbit(x)) ans += tr[x];
	return ans;
}
void add(ll tr[],ll i,ll x){
	for(;i <= n;i += lowbit(i)) tr[i] += x;
}
int main()
{
	cin >> n >> q;
	ll pre = 0;
	for(int i = 1;i <= n;i++){
		cin >> v[i];
		v[i] -= pre;
		pre += v[i];
		add(tr_1,i,v[i]);
		add(tr_2,i,v[i]*(i - 1));
	}
	for(int i = 1;i <= q;i++){
		int op;cin >> op;
		if(op == 1){
			ll l,r,x;cin >> l >> r >> x;
			add(tr_1,l,x);
			add(tr_1,r+1,-x);
			add(tr_2,l,x*(l-1));
			add(tr_2,r+1,-x*r);
		}else{
			ll l,r;cin >> l >> r;
			cout << r*ask(tr_1,r) - ask(tr_2,r) - ((l - 1)*ask(tr_1,(l - 1)) - ask(tr_2,(l - 1))) << endl;
		}
	}
}
```

## 线性基（不小心被删了，只剩下板子了）

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int MN = 63;
ll a[MN + 1];

bool f0 = 0;//有重复
bool feasy = 0;//已经使得线性基最简

void ins(ll x){
	for(int i = MN;~i;i--){
		if(x&(1ll << i)){
			if(!a[i]){
				a[i] = x;
				return;
			}else
				x ^= a[i];
		}
	}
	f0 = 1;
}

ll qkmin(ll k){
	if(f0) k--;
	ll ans = 0;
	if(!feasy){
		for(int i = MN;~i;i--){
			if(!a[i]) continue;
			for(int j = i - 1;~j;j--){
				if(!a[j]) continue;
				if((a[i] >> j) & 1) a[i] ^= a[j];
			}
		}
		feasy = 1;
	}
	for(int i = 0;i <= MN;i++){
		if(a[i] && k){
			if((k & 1) ^ ((ans >> i) & 1)){
				ans ^= a[i];
			}
			k >>= 1;
		}
	}
	if(k) return -1;
	else return ans;
}

ll qmin(){
	if(f0) return 0;
	else{
		for(int i = MN;~i;i--){
			if(a[i]) return a[i];
		}
	}
}

ll qmax(){
	ll x = 0;
	for(int i = MN;~i;i--){
		if(!((x >> i)&1)) x ^= a[i];
	}
	return x;
}

void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++){
		ll x;cin >> x;
		ins(x);
	}
	cout << qmax() << endl;
}

int main(){
	IOS
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## bitset

### 定义：

```c++
bitset<n> b; //n位0
bitset<n> b(u); //unsigned long型u的一个副本(从右到左)
bitset<n> b(s); //01型字符串转换
```

### 函数：

```c++
b.any(); //是否存在二进制为1的位
b.none(); //是否不存在二进制为1的位
b.count(); //二进制为1的个数
b.size(); //位个数
b[pos]; //访问pos位置的二进制位
b.set(); //全置为1
b.reset(); //全置为0
b.flip(); //全取反
```

### 效率：内部用unsigned long long 来实现，因此效率为O(N/64)

# 字符串

## 输入

```c++
char c[100];
cin.getline(c,100);//可以读空格
string s;
getline(cin,s);
```

## sscanf

```c++
cin >> s;
//s = "2023:2:7"
sscanf(s,"%d:%d:%d",&y,&m,&d);
```

## Manachar算法

* 如果一个回文串左边有一个小回文串，那么右边就有一个对应的字符串
* 记录最大的拓展范围，如果现在在判断的中心在范围之内，那么就可以把左边的长度对称给右边（小于范围）
* 加入#是为了让每个回文串都有回文中心
* [【模板】manacher 算法](https://www.luogu.com.cn/problem/solution/P3805)

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 2.3e7;
char c[MAXN];
int llen[MAXN] = {0};
int main()
{
	int len = 1,ans = 0;
	char cc;
	c[0] = '*',c[1] = '#';
	while(scanf("%c",&cc) != EOF)
	{
		c[++len] = cc,c[++len] = '#';
		if(cc < 'a' || cc > 'z')
		break;
	}
	c[0] = '*';//阻挡符
	for(int t = 1,r = 0,mid = 0;t <= len;t++)
	{
		if(t <= r) llen[t] = min(llen[(mid << 1)-t],r-t+1);//范围对称过来，但是不超过范围
		while(c[t-llen[t]] == c[t+llen[t]]) llen[t]++;
		if(llen[t]+t>r) r = llen[t]+t-1,mid=t;//拓展范围
		if(llen[t]>ans) ans=llen[t];
	}
	cout << ans - 1;
}
```

## KMP查询子字符串

* 预处理匹配不上那匹配前面的哪个字符

```c++
void fnex(string str)
{
	int len = str.size();//长度
	int ptop = -1,i = 0;
	nex[0] = -1;
	while(i < len)
	{
		if(ptop == -1 || str[ptop] == str[i])
			nex[++i] = ++ptop;
		else
			ptop = nex[ptop];
	}
}
```

```cpp
int fcnt(string &s,string &ss){//统计ss在s中的数量
    fnex(ss);
    int n = s.size(),m = ss.size();
    int pos = 0,cnt = 0;
    for(int i = 0;i < n;){
        if(pos == m) cnt++,pos = nex[pos];
        else if(pos == -1 || s[i] == ss[pos]) i++,pos++;
        else pos = nex[pos];
    }
    if(pos == m) cnt++;
    return cnt;
}
```



## trie树

```c++
void add_trie(string str)
{
	int end = str.size();
	int x = 0;
	for(int i = 0;i < end;i++)
	{
		int t = str[i] - 'a';
		if(!trie[x][t])
			trie[x][t] = ++ptop;
		x = trie[x][t];
	}
	cnt[x]++;//这里是算结尾的数量，如果求前缀可以把这个放上面去
}
```

## AC自动机

```c++
void kmp_trie()
{
	queue<int> qu;
	for(int i = 0;i < 26;i++)
	{
		if(trie[0][i])//存在
			qu.push(trie[0][i]);
	}
	while(!qu.empty())
	{
		int x = qu.front();
		qu.pop();
		for(int i = 0;i < 26;i++)
		{
			if(trie[x][i])//节点存在
			{
				nex[trie[x][i]] = trie[nex[x]][i];//核心1,这个节点指向父节点的指向的节点的同子节点
				qu.push(trie[x][i]);
			}
			else
				trie[x][i] = trie[nex[x]][i];//核心2,这个节点不存在就让它等于父节点的指向的子节点
		}
	}
}

int find(string str)
{
	int end = str.size();
	int x = 0,num = 0;
	for(int i = 0;i < end;i++)
	{
		x = trie[x][str[i] - 'a'];
		int xx = x;
		while(cnt[xx] != -1)
		{
			num += cnt[xx];
			cnt[xx] = -1;
			xx = nex[xx];
		}
	}
	return num;
}
```

## [AC自动机简单版](https://www.luogu.com.cn/problem/P3808)

### 问有多少个模式串在文本串中出现过(将cnt在结尾+1，而非前缀的在每个位置+1)

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 150 + 10,M = 1e4 + 10;

string s[N],t;

int cnt[M],ccnt[M],ma,ans;
int trie[M][26];
int nex[M];
int ptop;
map<string,int> mp;
map<int,string> mpp;

void add_trie(string str)
{
	int end = str.size();
	int x = 0;
	for(int i = 0;i < end;i++)
	{
		int t = str[i] - 'a';
		if(!trie[x][t])
			trie[x][t] = ++ptop;
		x = trie[x][t];
	}
	cnt[x] = 1;//这里是算结尾的数量，如果求前缀可以把这个放上面去
	mpp[x] = str;
}

void kmp_trie()
{
	queue<int> qu;
	for(int i = 0;i < 26;i++)
	{
		if(trie[0][i])//存在
			qu.push(trie[0][i]);
	}
	while(!qu.empty())
	{
		int x = qu.front();
		qu.pop();
		for(int i = 0;i < 26;i++)
		{
			if(trie[x][i])//节点存在
			{
				nex[trie[x][i]] = trie[nex[x]][i];//核心1,这个节点指向父节点的指向的节点的同子节点
				qu.push(trie[x][i]);
			}
			else
			trie[x][i] = trie[nex[x]][i];//核心2,这个节点不存在就让它等于父节点的指向的子节点
		}
	}
}

void find(string str)
{
	int end = str.size();
	int x = 0,num = 0;
	for(int i = 0;i < end;i++)
	{
		x = trie[x][str[i] - 'a'];
		int xx = x;
		while(xx != 0)
		{
			if(cnt[xx]){
				mp[mpp[xx]]++;
				if(mp[mpp[xx]] > ma){
					ans = 1;
					ma = mp[mpp[xx]]; 
				}else if(mp[mpp[xx]] == ma){
					ans++;
				}
			}
			xx = nex[xx];
		}
	}
}

void init(){
	memset(cnt,0,sizeof(cnt));
	memset(trie,0,sizeof(trie));
	memset(nex,0,sizeof(nex));
	mp.clear();
	mpp.clear();
	ptop = 0;
	ma = 0;
	ans = 0;
}

void test(){
	int n;cin >> n;
	while(n != 0){
		init();
		for(int i = 1;i <= n;i++){
			cin >> s[i];
			add_trie(s[i]);
		}
		kmp_trie();
		cin >> t;
		find(t);
		cout << ans << endl;
		for(int i = 1;i <= n;i++){
			if(mp[s[i]] == ma)
				cout << s[i] << endl;
		}
		cin >> n;
	}
}

int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [AC自动机加强版](https://www.luogu.com.cn/problem/P3796)

### 问在文本串中最多出现次数的格式串的出现次数，并输出所有最大次数的格式串

### 用一个字典树的节点记录对应的是哪个字符串的结尾，到这个节点就让对应字符串的数量加1

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 151,M = 1e5 + 10;

string s[N],t;

int cnt[M],ccnt[M],ma,ans;
int trie[M][26];
int nex[M];
int ptop;
map<int,int> mp;

void add_trie(string str,int id)
{
	int end = str.size();
	int x = 0;
	for(int i = 0;i < end;i++)
	{
		int t = str[i] - 'a';
		if(!trie[x][t])
			trie[x][t] = ++ptop;
		x = trie[x][t];
	}
	cnt[x] = 1;//这里是算结尾的数量，如果求前缀可以把这个放上面去
	mp[x] = id;
}

void kmp_trie()
{
	queue<int> qu;
	for(int i = 0;i < 26;i++)
	{
		if(trie[0][i])//存在
			qu.push(trie[0][i]);
	}
	while(!qu.empty())
	{
		int x = qu.front();
		qu.pop();
		for(int i = 0;i < 26;i++)
		{
			if(trie[x][i])//节点存在
			{
				nex[trie[x][i]] = trie[nex[x]][i];//核心1,这个节点指向父节点的指向的节点的同子节点
				qu.push(trie[x][i]);
			}
			else
			trie[x][i] = trie[nex[x]][i];//核心2,这个节点不存在就让它等于父节点的指向的子节点
		}
	}
}

void find(string str)
{
	int end = str.size();
	int x = 0,num = 0;
	for(int i = 0;i < end;i++)
	{
		x = trie[x][str[i] - 'a'];
		int xx = x;
		while(xx != 0)
		{
			if(cnt[xx]){
				ccnt[mp[xx]]++;
				if(ccnt[mp[xx]] > ma){
					ma = ccnt[mp[xx]];
					ans = 1;
				}else{
					ans++;
				}
			}
			xx = nex[xx];
		}
	}
}

void init(){
	memset(cnt,0,sizeof(cnt));
	memset(trie,0,sizeof(trie));
	memset(nex,0,sizeof(nex));
	memset(ccnt,0,sizeof(ccnt));
	mp.clear();
	ptop = 0;
	ma = 0;
	ans = 0;
}

void test(){
	int n;cin >> n;
	while(n != 0){
		init();
		for(int i = 1;i <= n;i++){
			cin >> s[i];
			add_trie(s[i],i);
		}
		kmp_trie();
		cin >> t;
		find(t);
		cout << ma << endl;
		for(int i = 1;i <= n;i++){
			if(ccnt[i] == ma)
				cout << s[i] << endl;
		}
		cin >> n;
	}
}

int main(){
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

## [AC 自动机(二次加强版)](https://www.luogu.com.cn/problem/P5357)

### 根据nex数组的树，采用拓扑排序优化

```cpp
while(xx != 0)
{
	if(cnt[xx]){
		ccnt[mp[xx]]++;
		if(ccnt[mp[xx]] > ma){
			ma = ccnt[mp[xx]];
			ans = 1;
		}else{
			ans++;
        }
	}	
    xx = nex[xx];
}
```

### 这个部分，将xx这个位置的标记+1，然后拓扑即可

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 2e5 + 10,M = 2e6 + 10;

string s[N],t;

int ccnt[M],ma,ans,bj[M],in[M];
bool cnt[M];
vector<int> ve[M];
int trie[M][26];
int nex[M];
int ptop;
map<int,int> mp;

void add_trie(string str,int id)
{
	int end = str.size();
	int x = 0;
	for(int i = 0;i < end;i++)
	{
		int t = str[i] - 'a';
		if(!trie[x][t])
			trie[x][t] = ++ptop;
		x = trie[x][t];
	}
	cnt[x] = 1;//这个点有东西
	ve[x].push_back(id);
}

void kmp_trie()
{
	queue<int> qu;
	for(int i = 0;i < 26;i++)
	{
		if(trie[0][i])//存在
			qu.push(trie[0][i]);
	}
	while(!qu.empty())
	{
		int x = qu.front();
		qu.pop();
		for(int i = 0;i < 26;i++)
		{
			if(trie[x][i])//节点存在
			{
				nex[trie[x][i]] = trie[nex[x]][i];//核心1,这个节点指向父节点的指向的节点的同子节点
				in[nex[trie[x][i]]]++;
				qu.push(trie[x][i]);
			}
			else
			trie[x][i] = trie[nex[x]][i];//核心2,这个节点不存在就让它等于父节点的指向的子节点
		}
	}
}

void find(string str)
{
	int end = str.size();
	int x = 0;
	for(int i = 0;i < end;i++)
	{
		x = trie[x][str[i] - 'a'];
		ma = max(x,ma);
		int xx = x;
		if(xx != 0)
		{
			bj[xx]++;
		}
	}
	queue<int> qu;
	for(int i = ptop;i >= 0;i--){
		if(in[i] == 0)
			qu.push(i);
	}
	while(!qu.empty()){
		int x = qu.front();
		qu.pop();
		in[nex[x]]--;
		if(in[nex[x]] == 0)
			qu.push(nex[x]);
		if(bj[x]){
			bj[nex[x]] += bj[x];
			for(auto id:ve[x]){
				ccnt[id] += bj[x];
			}
		}
	}
}

void test(){
	int n;cin >> n;
		for(int i = 1;i <= n;i++){
			cin >> s[i];
			add_trie(s[i],i);
		}
		kmp_trie();
		cin >> t;
		find(t);
		for(int i = 1;i <= n;i++){
			cout << ccnt[i] << endl;
		}
}

int main(){
	ios::sync_with_stdio(false);//写了using namespace std;
	int t = 1;//cin >> t;
	while(t--){
		test();
	}
}
```

# 其他

## cdq套cdq解决n维偏序问题

~~cdq大法好~~

~~还是没怎么搞懂~~

### 先把题目放着：

二维偏序：[P1908 逆序对](https://www.luogu.com.cn/problem/P1908)

三维偏序：[P3810 【模板】三维偏序（陌上花开）](https://www.luogu.com.cn/problem/P3810)

四维偏序~~罪恶之源~~：[stars](http://acm.hdu.edu.cn/showproblem.php?pid=5126)

### 先从简单的开始，二维偏序cdq做法

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

### 然后三维

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

### 然后按着这思路继续四维

## 二分、三分、双指针

### 二分查找

### 概念

* 又名折半查找，针对有序排列，每次找中间元素并判断，根据判断结果从而排除一半的元素，查找效率非常快O(logN)
* 必须针对有序排列

### 代码-找第一个大于等于x的数-数组从小到大排序

```c++
int left = 1,right = n;
while(left < right)
{
	int mid = (left + right) / 2;
	if(x >= a[mid])
		right = mid - 1;
	else
		left = mid + 1;
}
if(left <= n)
cout << a[left];
else
cout << -1;//不存在
```

### 二分答案

### 概念

* 对答案进行二分查找

### 适用条件

* 将不确定的答案带入题目可以得出正确答案的相对大小
* 答案越大越满足题目条件，而题目求最小值或反过来的情况
* 让最大值最小或让最小值最大
* 详见二分答案专题

### 三分

### 例子

* 给定一个 N 次函数和范围 [l, r]，求出使函数在 [l, x] 上单调递增且在 [x, r] 上单调递减的唯一的 x 的值。
  ![三分](C:\Users\Administrator\Desktop\markdown\三分.png)

### 代码

```c++
double x;
while(r - l > mis)
{
	x = (r + l) / 2;
	double rmid = (r + x) / 2,lmid = (l + x) / 2;
	if(f(rmid) > f(x))
		r = rmid;
	if(f(lmid) > f(x))
		l = lmid;
}
```

### 双指针与尺取法

### 双指针前进方向相反：典型二分法

### 双指针前进方向相同：典型尺取法

#### 尺取法典型题目：从一个序列中找出连续且和大于等于m的最小子序列，输出其长度

* 代码

```c++
int l = r = 1,sum = a[1];
while(r <= n)
{
	if(sum >= m)
	{
		while(sum - a[l + 1] >= m)//移动左指针直到不满足条件之前
		l++,sum -= a[l];
		mi = min(sum,mi);//更新答案
		sum -= a[l];//使其不满足条件
	}
	r++;
	sum += a[r];//不满足条件，移动右指针
}
```

## 离散化

### 定义

* 将数组转换为排序序号

### 前置知识

* unique(begin,end)对这个数组进行去重并返回第一个重复元素的地址
* lower_bound(begin,end,x);找第一个>=x的地址

### 离散化方法1

* **复制数组t**
* **对t进行排序**
* **对t进行去重并计算不重复元素数量**
* **在t中找a[i]的位置并存入b[i]**

### 代码

```c++
void Discretization(int& n,vector<int>& ve,int* data,int* ra){//长度，离散化数组，数据，排名
	sort(ve.begin(),ve.end());
	ve.erase(unique(ve.begin(),ve.end()),ve.end());
	for(int i = 1;i <= n;i++) ra[i] = lower_bound(ve.begin(),ve.end(),data[i]) - ve.begin() + 1;
	n = ve.size();
}
```

## 怪东西

### 计算程序运行时间

* 当然可以跑到OI网站上调试程序，但是也可以用一些方法来调试程序
* clock()函数：返回从程序运行到调用clock函数所经过的时间（单位毫秒）(数据类型long)

```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){
	int ans = 0;
	long start = clock();
	for(int i = 1;i <= 100000;i++){
		for(int j = 1;j <= 10000;j++){
			if(i^j) ans++;
		}
	}
	cout << (clock() - start) << ' ' << ans << endl;
}
```

* 这样就可以得到各个程序段所运行的时间，从而锁定哪一段程序运行时间较长，复杂度较高从而修改，比如

```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){
	int ans = 0;
	long start = clock();
	for(int i = 1;i <= 10000;i++){
		for(int j = 1;j <= 10000;j++){
			if(i^j) ans++;
		}
	}
	cout << (clock() - start) << ' ' << ans << endl;
	start = clock();
	for(int i = 1;i <= 50000;i++){
		for(int j = 1;j <= 10000;j++){
			if(i^j) ans++;
		}
	}
	cout << (clock() - start) << ' ' << ans << endl;
	start = clock();
	for(int i = 1;i <= 100000;i++){
		for(int j = 1;j <= 10000;j++){
			if(i^j) ans++;
		}
	}
	cout << (clock() - start) << ' ' << ans << endl;
}
```

### O2优化

* 一般如果使用该优化也只使用到O2优化，当然如果要使用O3也只要修改里面的数字即可
* 在程序的开头放上

```cpp
#pragma GCC optimize(2)
```

* 比如上面的程序，运行效率将会提高4倍

### 输入输出优化

* 一般使用cin与cout都会使用关流优化，也是最常用的优化

```cpp
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
```

* 在main函数中放入IOS即可
* 使用scanf与printf与关流优化的cin与cout效率基本相同

* 更快的read与write函数

```cpp
inline int read() {
    int X=0,w=1; int c=getchar();
    while (c<'0'||c>'9') { if (c=='-') w=-1; c=getchar(); }
    while (c>='0'&&c<='9') X=(X<<3)+(X<<1)+(c^48),c=getchar();
    return X*w;
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
```

### __int128

* 不想写高精度或者在运算过程中出现超过了$10^{18}$的情况，那么在一定程度上就能用__int128来解决($2^{128}$大概$10^{38}$数量级)
* 它无法使用正常方法输入输出，因此使用快读模板输入输出

```cpp
inline __int128 read() {
    __int128 X=0,w=1; int c=getchar();
    while (c<'0'||c>'9') { if (c=='-') w=-1; c=getchar(); }
    while (c>='0'&&c<='9') X=(X<<3)+(X<<1)+(c^48),c=getchar();
    return X*w;
}
inline void write(__int128 x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}
```

### max,min函数

* 自带的max，min函数常数较大，可以自己用三元运算符优化

```cpp
#define Max(x,y) (x < y ? y : x)
#define Min(x,y) (x < y ? x : y)
```

* 快5倍左右吧

### 遍历pair动态数组

```cpp
vector<pair<int,int>> ve(n);
for(auto &[x,y]:ve)
    cin >> x >> y;
for(auto [x,y]:ve)
    cout << x << ' ' << y << endl;
```

* 当然除了int，其他奇怪的类型也可以

```cpp
vector<pair<int,string>> ve(n,{0,"aa"});
for(auto &[x,s]:ve) cin >> x >> s;
```

### tuple使用

* pair的拓展，（可存任意个）
* 结构体的简易替代品

```cpp
//定义
tuple<int,int,int,int> tu;
//访问
get<0>(tu);//不支持变量
//我爱auto与[]表达式
auto &[a,b,c,d] = tu;
cin >> a >> b >> c >> d;
auto [e,f,g,h] = tu;
cout << e << f << g << h;
```

### template

* 你猜猜这是什么类型
* 拿快速幂举例，如果x,y与p的类型不同

```cpp
template<typename T1,typename T2>
T2 qpow(T1 x,T1 y,T2 p){T2 ans = 1;while(y){if(y&1) ans = ans*x%p;x = x*x%p;y >>= 1;}return ans;}
```

* 就可以这样写
* 这样就能是函数模板适用更多情况

* 套快读函数

```cpp
template<typename T1>
inline T1 read() {
    T1 X=0,w=1; int c=getchar();
    while (c<'0'||c>'9') { if (c=='-') w=-1; c=getchar(); }
    while (c>='0'&&c<='9') X=(X<<3)+(X<<1)+(c^48),c=getchar();
    return X*w;
}
inline void write(T1 x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}
```

### 随机化！

* 我做不出来怎么办
* 随机化！（乱搞！）
* （加上上面的clock函数就能实现卡时随机化暴力，狠狠乱搞）
* 生成随机数

```cpp
srand(seed);//根据seed生成伪随机函数
rand();//返回[0,2^15)的随机数
(rand() << 15 | rand())//更大的随机数
```

* ### shuffle

* 从所有可能的排列中等概率选取一个

```cpp
shuffle(a + 1,a + 1 + n);
```

* 还有最重量级的模拟退火算法，也可以结合随机数指定启点，加clock函数卡时，乱搞

#### 绝对值优化与取模优化（没测出来差别不知道为什么）

#### 循环中++i比i++稍微快一点（也没测出来）

### register修饰

```cpp
for(register int i = 1;i <= _al;i++)
```

### 常用质数

```cpp
//1e9 + 7
//1e18 + 3
```

## 快读

```cpp
#define ll long long 
inline ll read() {
    ll X=0,w=1; int c=getchar();
    while (c<'0'||c>'9') { if (c=='-') w=-1; c=getchar(); }
    while (c>='0'&&c<='9') X=(X<<3)+(X<<1)+(c^48),c=getchar();
    return X*w;
}
inline void write(ll x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}
```

## 莫队算法

### 对于一大堆区间处理，我们可以对左区间分块，那么左区间的只要在$\sqrt{n}$内跳，右区间仍然是$n$，总复杂度为$O((n+\sqrt{n})\sqrt{n})$

### 例题：[P2709 小B的询问](https://www.luogu.com.cn/problem/P2709)

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int MAXN = 5e4 + 10;
int k[MAXN],a[MAXN];
pair<int,int> lr[MAXN];
int block;
struct range{
	int l,r,i;
}ra[MAXN];
ll an[MAXN];
int main()
{
	int n,m,kk;cin >> n >> m >> kk;
	block = sqrt(kk);
	for(int i = 1;i <= n;i++)
		cin >> a[i];
	for(int i = 1;i <= m;i++)
	{
		cin >> ra[i].l >> ra[i].r;
		ra[i].i = i;
	}
	sort(ra + 1,ra + 1 + m,[&](range a,range b){
		if(a.l / block == b.l / block)
			return a.r < b.r;
		else
			return a.l < b.l;
	});
	int l = 1,r = 1;
	ll ans = 1;
	k[a[1]] = 1;
	for(int i = 1;i <= m;i++)
	{
		int _l = ra[i].l,_r = ra[i].r;
		while(l < _l)//向右缩小
		{
			k[a[l]]--;
			ans -= k[a[l]]*2 + 1;
			l++;
		}
		while(l > _l)//向左扩大
		{
			l--;
			k[a[l]]++;
			ans += k[a[l]]*2 - 1;
		}
		while(r < _r)//向右扩大
		{
			r++;
			k[a[r]]++;
			ans += k[a[r]]*2 - 1;
		}
		while(r > _r)//向左缩小
		{
			k[a[r]]--;
			ans -= k[a[r]]*2 + 1;
			r--;
		}
		an[ra[i].i] = ans;
	}
	for(int i = 1;i <= m;i++)
		cout << an[i] << endl;
}
```

## 扫描线

### 先将y离散化，用离散化的y构造线段树，对所有线根据x排序，一个一个加入或减去线段

### 将点变成点后的区间处理

### 分别记录覆盖长度和覆盖次数

```c++
#include<bits/stdc++.h>
using namespace std;
#define lson (x << 1)
#define rson (x << 1 | 1)
#define ll long long

const int MAXN = 2e5 + 10;
int n;
ll val[MAXN * 2],tem[MAXN * 2],cnt;//离散化

struct edge{
	ll x,yh,yl;
	bool f;//记录是否为起始边
}ed[MAXN * 2];//记录边

bool cmp(edge a,edge b)//对边根据x排序
{
	return a.x < b.x;
}

struct tree{
	ll len,sum;//长度,覆盖次数
	ll l,r;
}tr[MAXN *2 *4];

void build(int l,int r,int x)
{
	tr[x].l = l,tr[x].r = r;
	if(l == r)
	return;
	else
	{
		int mid = (l + r) / 2;
		build(l,mid,lson);
		build(mid + 1,r,rson);
	}
}

void update(int x)//更新
{
	if(tr[x].sum) tr[x].len = val[tr[x].r + 1] - val[tr[x].l];//有覆盖说明整个区间都有,+1是为了让它一定为一个区间而不是一个点
	else tr[x].len = tr[lson].len + tr[rson].len;//没有整个覆盖的直线，那就只能看下面的覆盖长度
}

void add(int l,int r,int x,int v)
{
	if(tr[x].l >= l && tr[x].r <= r)//能覆盖
	{
		tr[x].sum += v;
		update(x);
	}
	else//不能覆盖
	{
		int mid = (tr[x].l + tr[x].r) / 2;
		if(l <= mid)
			add(l,r,lson,v);
		if(r >= mid + 1)
			add(l,r,rson,v);
		update(x);
	}
}

int main()
{
	cin >> n;
	for(int i = 1;i <= n;i++)
	{
		ll x1,x2,y1,y2;
		cin >> x1 >> y1 >> x2 >> y2;
		ed[i*2 - 1].x = x1;
		ed[i*2].x = x2;
		ed[i*2].yh = ed[i*2 - 1].yh = y2;
		ed[i*2].yl = ed[i*2 - 1].yl = y1;
		ed[i*2 - 1].f = 1;
		tem[i * 2 - 1] = y1;
		tem[i * 2] = y2;
	}
	sort(tem + 1,tem + 1 + 2 * n);
	cnt = unique(tem + 1,tem + 1 + 2*n) - tem - 1;
	for(int i = 1;i <= 2*n;i++)
	{
		int pos;
		pos = lower_bound(tem + 1,tem + 1 + cnt,ed[i].yh) - tem;
		val[pos] = ed[i].yh;
		ed[i].yh = pos;
		pos = lower_bound(tem + 1,tem + 1 + cnt,ed[i].yl) - tem;
		val[pos] = ed[i].yl;
		ed[i].yl = pos;
	}
	val[cnt + 1] = val[cnt];
	build(1,cnt,1);
	sort(ed + 1,ed + 1 + 2*n,cmp);
	ll ans = 0;
	for(int i = 1;i <= 2*n - 1;i++)
	{
		if(ed[i].f) add(ed[i].yl,ed[i].yh - 1,1,1);//-1是为了让点变成点后的区间
		else add(ed[i].yl,ed[i].yh - 1,1,-1);
		ans += (ed[i + 1].x - ed[i].x) * tr[1].len;
	}
	cout << ans << endl;
	return 0;
}
```

## 龟速乘

### 求$a*b\pmod p$的值，但是$a$和$b$的值都非常大($10^{18}$)那么正常用乘法就会爆long long，要想其他方法

### 类快速幂思想，就是把乘法换成加法

```c++
ll qadd(ll a,ll b,ll p)
{
    ll ans = 0;
    while(b)
    {
        if(b&1) ans = (ans + a) %p;
        a = (a + a) % p;
        b>>=1;
    }
    return ans;
}
```

## 数列分块入门

### [数列分块入门 1](https://loj.ac/p/6277)

### 目标

* 区间加法
* 单点查值

### 做法

#### 建立区块

* 定义区块大小
* 记录区块起点和终点
* 标记各个数属于哪个区块

```c++
int n,par;
int belong[MAXN];//属于哪个区块
int l[MAXPAR],r[MAXPAR];//记录区间的起点，终点
void build()
{
	par = sqrt(n);//确定区块大小
	int cnt = n / par;
	int ptop = 1;
	for(int i = 1;i <= cnt;i++)
	{
		l[i] = ptop;//确定左边界
		for(int j = 1;j <= par;j++)
			belong[ptop++] = i;
		r[i] = ptop - 1;//确定右边界
	}
	while(ptop <= n)//处理多余元素
	belong[ptop++] = cnt;
	r[cnt] = n;//重置左边界
}
```

#### 区块加法

* 只要重点考虑边界的情况
* 中间的区块用一个数组记录操作

```c++
int part[MAXPAR];
void add(int ll,int rr,int c)
{
	if(belong[ll] == belong[rr])//如果在同一区块，暴力加法
		for(int i = ll;i <= rr;i++)
			a[i] += c;
	else
	{
		for(int i = ll;i <= r[belong[ll]];i++)//边界处理
			a[i] += c;
		for(int i = l[belong[rr]];i <= rr;i++)
			a[i] += c;
		for(int i = belong[ll] + 1;i <= belong[rr] - 1;i++)//区间加法
			part[i] += c;
	}
}
```

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 5e4 + 10;
const int MAXPAR = 250;
int n,par;
int a[MAXN];
int belong[MAXN];//属于哪个区块
int l[MAXPAR],r[MAXPAR];//记录区间的起点，终点
void build()
{
	par = sqrt(n);//确定区块大小
	int cnt = n / par;
	int ptop = 1;
	for(int i = 1;i <= cnt;i++)
	{	
		l[i] = ptop;//确定左边界
		for(int j = 1;j <= par;j++)
			belong[ptop++] = i;
		r[i] = ptop - 1;//确定右边界
	}
}
int part[MAXPAR];
void add(int ll,int rr,int c)
{
	if(belong[ll] == belong[rr])//如果在同一区块，暴力加法
		for(int i = ll;i <= rr;i++)
			a[i] += c;
	else
	{
		for(int i = ll;i <= r[belong[ll]];i++)//边界处理
			a[i] += c;
		for(int i = l[belong[rr]];i <= rr;i++)
			a[i] += c;
		for(int i = belong[ll] + 1;i <= belong[rr] - 1;i++)//区间加法
			part[i] += c;
	}
}

int main()
{
	cin >> n;build();//读取并建立区间
	for(int i = 1;i <= n;i++)
		cin >> a[i];
	for(int i = 1;i <= n;i++)
	{
		int opt,ll,rr,c;
		cin >> opt >> ll >> rr >> c;
		if(opt)
			cout << a[rr] + part[belong[rr]] << endl;
		else
			add(ll,rr,c);
	}
}
```

### [数列分块入门 2](https://loj.ac/p/6278)

### 目标

* 区间加法
* 统计区间内相较特定数的大小数量

### 做法

* 用pair<int,int> 来存数量和原位置
* 被包含的区间进行排序，用lower_bound找数量
* 没被包含的区间进行暴力统计
* 初始化进行区间内的排序
* 加法时对

#### 统计

```c++
int ff(int l,int r,int x)//因为pair不能用lower_bound,所以手写一个二分
{
	int begin = l;
	while(l <= r)
	{
		int mid = (l+r)/2;
		if(a[mid].first >= x)//相等时往前找，找第一个大于等于x的值的位置
		r = mid - 1;
		else
		l = mid + 1;
	}
	return l - begin;
}
long long find(int left,int right,int x)
{
	int ll = belong[left],rr = belong[right];
	int num = 0;
	if(ll == rr)
	{
		for(int i = l[ll];i <= r[ll];i++)//暴力统计不完整的块
		if(a[i].second >= left && a[i].second <= right && a[i].first < x - part[ll])
		num++;
	}
	else
	{
		for(int i = ll + 1;i <= rr - 1;i++)//二分查找完整的块
		{
			num += ff(l[i],r[i],x - part[i]);
		}
		for(int i = l[ll];i <= r[ll];i++)//暴力统计不完整的块
		if(a[i].second >= left && a[i].second <= right && a[i].first < x - part[ll])
		num++;
		for(int i = l[rr];i <= r[rr];i++)//暴力统计不完整的块
		if(a[i].second >= left && a[i].second <= right && a[i].first < x - part[rr])
		num++;
	}
	return num;
}
```

#### 对加法的修改

```c++
void add(int left,int right,int k)
{
	int ll = belong[left],rr = belong[right];
	if(ll == rr)//同一个区间,暴力加法
	{
		for(int i = l[ll];i <= r[ll];i++)
		if(a[i].second >= left && a[i].second <= right)
		a[i].first += k;
		sort(a + l[ll],a + r[ll] + 1);//破坏了块的大小，因此重新排序
	}
	else
	{
		for(int i = ll + 1;i <= rr - 1;i++)//完整的块
		part[i] += k;
		for(int i = l[ll];i <= r[ll];i++)
		if(a[i].second >= left && a[i].second <= right)
		a[i].first += k;
		for(int i = l[rr];i <= r[rr];i++)
		if(a[i].second >= left && a[i].second <= right)
		a[i].first += k;
		sort(a + l[ll],a + r[ll] + 1);//重新排序不完整的块
		sort(a + l[rr],a + r[rr] + 1);
	}
}
```

#### 对初始化的修改

```c++
void build()
{
	par = sqrt(n);//块的大小
	int cnt = n/par;//块的数量
	int ptop = 1;//要处理的元素
	for(int i = 1;i <= cnt;i++)
	{
		l[i] = ptop;//确定左边界
		for(int j = 1;j <= par;j++)
		belong[ptop++] = i;
		r[i] = ptop - 1;//确定右边界
		sort(a + l[i],a + r[i] + 1);
	}
	while(ptop <= n)//处理多余元素
	belong[ptop++] = cnt;
	r[cnt] = n;//重置左边界
	sort(a + l[cnt],a + r[cnt] + 1);//重新排序最后一个区间
}
```

#### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
inline long long read() {
    long long X=0,w=1; char c=getchar();
    while (c<'0'||c>'9') { if (c=='-') w=-1; c=getchar(); }
    while (c>='0'&&c<='9') X=(X<<3)+(X<<1)+c-'0',c=getchar();
    return X*w;
}
inline void write(long long x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}
const int MAXN = 5e4 + 10;
const int MAXPAR = 230;//最多分出230个块
pair<int,int> a[MAXN];
int n,par;
int l[MAXPAR],r[MAXPAR],belong[MAXN],part[MAXPAR];
int ff(int l,int r,int x)
{
	int begin = l;
	while(l <= r)
	{
		int mid = (l+r)/2;
		if(a[mid].first >= x)
		r = mid - 1;
		else
		l = mid + 1;
	}
	return l - begin;
}
bool cmp(pair<int,int> a,pair<int,int> b)
{
	return a.first < b.first;
}
void build()
{
	par = sqrt(n);
	int cnt = n/par;
	int ptop = 1;
	for(int i = 1;i <= cnt;i++)
	{
		l[i] = ptop;
		for(int j = 1;j <= par;j++)
		belong[ptop++] = i;
		r[i] = ptop - 1;
		sort(a + l[i],a + r[i] + 1,cmp);
	}
	while(ptop <= n)
	belong[ptop++] = cnt;
	r[cnt] = n;
	sort(a + l[cnt],a + r[cnt] + 1,cmp);
}
void add(int left,int right,int k)
{
	int ll = belong[left],rr = belong[right];
	if(ll == rr)//同一个区间,暴力加法
	{
		for(int i = l[ll];i <= r[ll];i++)
		if(a[i].second >= left && a[i].second <= right)
		a[i].first += k;
		sort(a + l[ll],a + r[ll] + 1,cmp);
	}
	else
	{
		for(int i = ll + 1;i <= rr - 1;i++)
		part[i] += k;
		for(int i = l[ll];i <= r[ll];i++)
		if(a[i].second >= left && a[i].second <= right)
		a[i].first += k;
		for(int i = l[rr];i <= r[rr];i++)
		if(a[i].second >= left && a[i].second <= right)
		a[i].first += k;
		sort(a + l[ll],a + r[ll] + 1,cmp);
		sort(a + l[rr],a + r[rr] + 1,cmp);
	}
}
long long find(int left,int right,int x)
{
	int ll = belong[left],rr = belong[right];
	int num = 0;
	if(ll == rr)
	{
		for(int i = l[ll];i <= r[ll];i++)
		if(a[i].second >= left && a[i].second <= right && a[i].first < x - part[ll])
		num++;
	}
	else
	{
		for(int i = ll + 1;i <= rr - 1;i++)
		{
			num += ff(l[i],r[i],x - part[i]);
		}
		for(int i = l[ll];i <= r[ll];i++)
		if(a[i].second >= left && a[i].second <= right && a[i].first < x - part[ll])
		num++;
		for(int i = l[rr];i <= r[rr];i++)
		if(a[i].second >= left && a[i].second <= right && a[i].first < x - part[rr])
		num++;
	}
	return num;
}
int main()
{
	cin >> n;
	for(int i = 1;i <= n;i++)
	a[i].first = read(),a[i].second = i;
	build();
	for(int i = 1;i <= n;i++)
	{
		int op = read(),ll = read(),rr = read(),c = read();
		if(op)
		{
			write(find(ll,rr,c*c));
			putchar('\n');
		}
		else
			add(ll,rr,c);
	}
}
```

### [数列分块入门 3](https://loj.ac/p/6279)

### 目标

* 区间加法
* 查询区间某个值的前驱

### 做法

* 对于大区间，分成小区间，找出每个区间的那个值的前驱，再整合
* 对于单个完整区间使用二分查找

#### 对查找的修改

```c++
int ff(int l,int r,int x)
{
	int begin = l;
	while(l <= r)
	{
		int mid = (l+r)/2;
		if(a[mid].first >= x)
		r = mid - 1;
		else
		l = mid + 1;
	}
	if(l == begin)//区间内所有数都大于等于x，也就是说不存在x的前驱
	return -1;
	else//第一个大于等于x的值的前一个就是x的前驱
	return a[l - 1].first + part[belong[l]];
}
long long find(int left,int right,int x)
{
	int ll = belong[left],rr = belong[right];
	int ans = -1;
	if(ll == rr)
	{
		for(int i = l[ll];i <= r[ll];i++)
		if(a[i].second >= left && a[i].second <= right && a[i].first < x - part[ll] && a[i].first + part[ll] > ans)
		ans = a[i].first + part[ll];
	}
	else
	{
		for(int i = ll + 1;i <= rr - 1;i++)
		{
			int t = ff(l[i],r[i],x-part[i]);
			if(t > ans)
			ans = t;
		}
		for(int i = l[ll];i <= r[ll];i++)
		if(a[i].second >= left && a[i].second <= right && a[i].first < x - part[ll] && a[i].first + part[ll] > ans)
		ans = a[i].first + part[ll];
		for(int i = l[rr];i <= r[rr];i++)
		if(a[i].second >= left && a[i].second <= right && a[i].first < x - part[rr] && a[i].first + part[rr] > ans)
		ans = a[i].first + part[rr];
	}
	return ans;
}
```

### [数列分块入门 4](https://loj.ac/p/6280)

### 目标

* 区间加法
* 区间求和

### 做法

* 记录每个区间的和

#### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
inline long long read() {
    long long X=0,w=1; char c=getchar();
    while (c<'0'||c>'9') { if (c=='-') w=-1; c=getchar(); }
    while (c>='0'&&c<='9') X=(X<<3)+(X<<1)+c-'0',c=getchar();
    return X*w;
}
inline void write(long long x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}
const int MAXN = 5e4 + 10;
const int MAXPAR = 230;//最多分出230个块
int a[MAXN],l[MAXPAR],r[MAXPAR],belong[MAXN],part[MAXPAR] = {0};
long long sum[MAXPAR] = {0};
int n,par;
void build()
{
	par = sqrt(n);
	int cnt = n / par;
	int ptop = 1;
	for(int i = 1;i <= cnt;i++)
	{
		l[i] = ptop;
		for(int j = 1;j <= par;j++)
		sum[i] += a[ptop],belong[ptop++] = i;
		r[i] = ptop - 1;
	}
	while(ptop <= n)
	sum[cnt] += a[ptop],belong[ptop++] = cnt;
	r[cnt] = n;
}
void add(int x,int y,int c)
{
	int ll = belong[x],rr = belong[y];
	if(ll == rr)
	{
		for(int i = x;i <= y;i++)
		a[i] += c,sum[ll] += c;
	}
	else
	{
		for(int i = ll + 1;i <= rr - 1;i++)
		part[i] += c,sum[i] += c*par;
		for(int i = x;i <= r[ll];i++)
		a[i] += c,sum[ll] += c;
		for(int i = l[rr];i <= y;i++)
		a[i] += c,sum[rr] += c;
	}
}
long long find(int x,int y,int c)
{
	int ll = belong[x],rr = belong[y];
	long long ans = 0;
	if(ll == rr)
	{
		for(int i = x;i <= y;i++)
			ans += a[i] + part[ll];
	}
	else
	{
		for(int i = ll + 1;i <= rr - 1;i++)
			ans = (ans + sum[i]) % c;
		for(int i = x;i <= r[ll];i++)
			ans += a[i] + part[ll];
		for(int i = l[rr];i <= y;i++)
			ans += a[i] + part[rr];
	}
	return ans % c;
}
int main()
{
	n = read();
	for(int i = 1;i <= n;i++)
		a[i] = read();
	build();
	for(int i = 1;i <= n;i++)
	{
		int op = read(),x = read(),y = read(),c = read();
		if(op)
		{
			write(find(x,y,c + 1));
			putchar('\n');
		}
		else
		add(x,y,c);
	}
}
```

### [数列分块入门 5](https://loj.ac/p/6281)

### 目标

* 区间开方
* 区间求和

### 做法

* 2^31开5次二次方也会变成1，所以我们开6倍的空间来存所有数开0、1、2、3、4、5的值和它们的和

#### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long 
inline ll read() {
    ll X=0,w=1; char c=getchar();
    while (c<'0'||c>'9') { if (c=='-') w=-1; c=getchar(); }
    while (c>='0'&&c<='9') X=(X<<3)+(X<<1)+c-'0',c=getchar();
    return X*w;
}
inline void write(ll x)
{
    if(x<0){
    	putchar('-');
		x=-x;
	}
    if(x>9) 
		write(x/10);
    putchar(x%10+'0');
}
const int MAXN = 5e4 + 10;
const int MAXPAR = 250;
int n,par,belong[MAXN],l[MAXPAR],r[MAXPAR],a[6][MAXN],sum[6][MAXPAR];
int part[MAXPAR] = {0};
void build()
{
	par = sqrt(n);
	int cnt = n/par;
	int ptop = 1;
	for(int i = 1;i <= cnt;i++)
	{
		l[i] = ptop;
		for(int j = 1;j <= par;j++)
		{
			for(int k = 0;k <= 5;k++)
			sum[k][i] += a[k][ptop];
			belong[ptop++] = i;
		}
		r[i] = ptop - 1;
	}
	while(ptop <= n)
	{
		for(int k = 0;k <= 5;k++)
		sum[k][cnt] += a[k][ptop];
		belong[ptop++] = cnt;
	}
	r[cnt] = n;
}
void down(int x,int y)
{
	int be = belong[x];
	for(int j = x;j <= y;j++)
	{
		for(int i = 0;i <= 4;i++)
		{
			sum[i][be] -= a[i][j] - a[i + 1][j];
			a[i][j] = a[i + 1][j];
		}
	}
}
void add(int x,int y)
{
	int lb = belong[x],rb = belong[y];
	if(lb == rb)
	{
		down(x,y);
	}
	else
	{
		down(x,r[lb]);
		down(l[rb],y);
		for(int i = lb + 1;i <= rb - 1;i++)
		part[i]++;
	}
}
int find(int x,int y)
{
	int lb = belong[x],rb = belong[y];
	int ans = 0;
	if(lb == rb)
	{
		int t = min(5,part[lb]);
		for(int i = x;i <= y;i++)
		ans += a[t][i];
	}
	else
	{
		for(int i = lb + 1;i <= rb - 1;i++)
			ans += sum[min(5,part[i])][i];
		int t = min(5,part[lb]);
		for(int i = x;i <= r[lb];i++)
		ans += a[t][i];
		t = min(5,part[rb]);
		for(int i = l[rb];i <= y;i++)
		ans += a[t][i];
	}
	return ans;
}
int main()
{
	cin >> n;
	for(int i = 1;i <= n;i++)
	{
		a[0][i] = read();
		for(int j = 1;j <= 5;j++)
		a[j][i] = sqrt(a[j - 1][i]);
	}
	build();
	for(int i = 1;i <= n;i++)
	{
		int op = read(),x = read(),y = read(),c = read();
		if(op)
		{
			write(find(x,y));
			putchar('\n');
		}
		else
			add(x,y);
	}
}
```