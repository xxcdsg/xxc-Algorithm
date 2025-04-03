* 一维最大值 $ST$ 表

```cpp
class ST{
public:
	static int MAX_lg;
	static vector<int> lg;//求最高位
	static void get_lg(int n){
		lg = vector<int>(n + 1,0);
		for(int i = 2;i <= n;i++){
			lg[i] = lg[i / 2] + 1;
		}
		MAX_lg = lg[n];
	}

	vector<vector<int>> st;
	vector<vector<int>> to;
	void init(int n,vector<int> &a){
		st = vector<vector<int>>(n + 1,vector<int>(MAX_lg + 1));
		for(int i = 1;i <= n;i++) st[i][0] = a[i];
		for(int j = 1;j <= MAX_lg;j++)
			for(int i = 1;i + (1 << j) - 1 <= n;i++)
				st[i][j] = Max(st[i + (1 << (j - 1))][j - 1],st[i][j - 1]);
	}
	int query(int l,int r){
		int len = r - l + 1;
		int _2 = lg[len];
		return Max(st[l][_2],st[r - (1 << _2) + 1][_2]);
	}
};
int ST::MAX_lg = 0;
vector<int> ST::lg = vector<int>(0);
```

* 二维最大值最小值 $ST$ 表

```cpp
const int N = 250 + 10;
const int MAX_lg = 10;
class ST2{
public:
	static vector<int> lg;//求最高位
	static void get_lg(){
		lg = vector<int>(N,0);
		for(int i = 2;i < N;i++){
			lg[i] = lg[i / 2] + 1;
		}
	}

	int st[N][N][MAX_lg][MAX_lg];
	void init(int n,int m,vector<vector<int>> a){
		for(int i = 1;i <= n;i++)
			for(int j = 1;j <= m;j++)
				st[i][j][0][0] = a[i][j];
		for(int ii = 0;ii <= MAX_lg;ii++){
			for(int jj = 0;jj <= MAX_lg;jj++){
				if(ii == jj && ii == 0) continue;
				for(int i = 1;i + (1 << ii) - 1 <= n;i++){
					for(int j = 1;j + (1 << jj) - 1 <= m;j++){
						if(ii != 0){
							st[i][j][ii][jj] = max(st[i + (1 << (ii - 1))][j][ii - 1][jj],st[i][j][ii - 1][jj]);
						}
						if(jj != 0){
							st[i][j][ii][jj] = max({st[i][j][ii][jj],st[i][j + (1 << (jj - 1))][ii][jj - 1],st[i][j][ii][jj - 1]});
						}
					}
				}
			}
		}
	}

	int query(int l,int r,int d,int u){
		int lenx = r - l + 1,lgx = lg[lenx];
		int leny = u - d + 1,lgy = lg[leny];
		return max({st[l][d][lgx][lgy],st[r - (1 << lgx) + 1][d][lgx][lgy],st[l][u - (1 << lgy) + 1][lgx][lgy],st[r - (1 << lgx) + 1][u - (1 << lgy) + 1][lgx][lgy]});
	}


	int st2[N][N][MAX_lg][MAX_lg];
	void init2(int n,int m,vector<vector<int>> a){
		for(int i = 1;i <= n;i++)
			for(int j = 1;j <= m;j++)
				st2[i][j][0][0] = a[i][j];
		for(int ii = 0;ii <= MAX_lg;ii++){
			for(int jj = 0;jj <= MAX_lg;jj++){
				if(ii == jj && ii == 0) continue;
				for(int i = 1;i + (1 << ii) - 1 <= n;i++){
					for(int j = 1;j + (1 << jj) - 1 <= m;j++){
						if(ii != 0){
							st[i][j][ii][jj] = min(st2[i + (1 << (ii - 1))][j][ii - 1][jj],st2[i][j][ii - 1][jj]);
						}
						if(jj != 0){
							st[i][j][ii][jj] = min({st2[i][j][ii][jj],st2[i][j + (1 << (jj - 1))][ii][jj - 1],st2[i][j][ii][jj - 1]});
						}
					}
				}
			}
		}
	}

	int query2(int l,int r,int d,int u){
		int lenx = r - l + 1,lgx = lg[lenx];
		int leny = u - d + 1,lgy = lg[leny];
		return min({st2[l][d][lgx][lgy],st2[r - (1 << lgx) + 1][d][lgx][lgy],st2[l][u - (1 << lgy) + 1][lgx][lgy],st2[r - (1 << lgx) + 1][u - (1 << lgy) + 1][lgx][lgy]});
	}
}st;
vector<int> ST2::lg = vector<int>(0);
```

