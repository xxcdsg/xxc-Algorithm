# STL的使用
## vector动态数组
### 头文件
```c++
#include<vector>
```
### 定义
* 定义一个空的一维动态数组
```c++
vector<type> name;
```
* 定义一个空的二维动态数组
```c++
vector<vector<type>> name;
```
* 定义一个自带数据的动态数组
```c++
vector<int> a{1,2,3,4,5};
```
* 定义一个初始化的数组
```c++
vector<int> a(n,0);//长度为n，初始值为0
```
* 复制数组
```c++
vector<type> b(a);//定义了一个b动态数组，其内容和a一样
```
* 比较复杂的形式
```c++
vector<vector<int>> a(n + 1, vector<int>(m + 1, 0));//二维数组，一维长度为n+1,二维长度为m+1
```
### 方法函数
|函数|作用|
|---|---|
|ve.pop_back()|删除最后一个数据|
|ve.push_back(x)|在尾部加一个数据x|
|ve.size()|返回数据个数|
|ve.clear()|清空数组|
|ve.resize(n,v)|改变数组大小并赋值为v|
|**ve.insert(it,x)**|往迭代器it的位置插入x|
|**ve.erase(first,last)**|删除[first,last) 迭代器|
|ve.begin()|返回ve[0]的迭代器|
|ve.end()|返回最后一个元素后一个的迭代器|
|ve.empty()|判断是否为空|
### 常用方法函数的使用方法
* 删除[ve[first],ve[end]]
```c++
ve.erase(ve.begin() + first,ve.begin() + end + 1);
```
* 将x插入到ve的ve[aim]位置
```c++
ve.insert(ve.begin() + aim,x);
```
## stack栈
### 头文件
```c++
#include<stack>
```
### 定义
```c++
stack<type> sta;
```
### 方法函数
|代码|作用|
|-|-|
|sta.push(x)|在栈顶放入x|
|sta.pop()|删除栈顶元素|
|sta.top()|返回栈顶元素|
|sta.empty()|判断是否为空|
|sta.size()|返回元素数量|
## queue队列
### 头文件
```c++
#include<queue>
```
### 定义
```c++
queue<type> qu;
```
### 方法函数
|代码|作用|
|-|-|
|qu.front()|返回首元素|
|qu.back()|返回尾元素|
|qu.push(x)|在尾部添加一个元素x|
|qu.pop()|删除首元素|
|qu.size()|返回元素个数|
|qu.empty()|判断是否为空|
## deque双端队列
### 头文件
```c++
#include<deque>
```
### 定义
```c++
deque<type> deq;
```
### 方法函数
|函数|作用|
|-|-|
|deq.push_back(x)/deq.push_front(x)|将x添加在队列后/前|
|deq.pop_back()/deq.pop_front()|删除尾元素/首元素|
|deq.back()/deq.front()|返回尾元素/首元素|
|deq.empty()|判断是否为空|
|deq.size()|返回数量|
|deq.clear()|清空双端队列|
## map映射
### 头文件
```c++
#include<map>
```
### 定义
```c++
map<type1,type2> mp
```
### 方法函数
|函数|作用|
|-|-|
|mp.find(key)|返回键为key的迭代器O(logN),不存在返回mp.end()|
|mp.erase(it)|删除迭代器对应的键和值|
|mp.erase(key)|根据键删除键和对应的值|
|mp.erase(first,last)|删除迭代器[first,last)的元素|
|mp.size()|返回元素个数|
|mp.clear()|清空|
|mp.empty()|检查是否为空|
|mp.begin()/end()/rbegin()/rend()|头迭代器，尾迭代器，反向头迭代器，反向尾迭代器|
|mp.count(key)|查找这个键对应元素是否存在|
|mp.lower_bound(key)|返回一个迭代器指向键值>=key的第一个元素|
|mp.upper_bound(key)|返回一个迭代器指向键值>key的第一个元素|
### 存储方式
* map利用红黑树存储，按照键值由小到大排
### 遍历
* 利用迭代器进行遍历
* 正向，根据键值从小到大
```c++
auto it = mp.begin();
while(it != mp.end())
{
	cout << it -> first << ' ' << it -> second << endl;
	it++;
}
```
* 反向，根据键值从大到小 
```c++
auto it = mp.rbegin();
while(it != mp.rend())
{
	cout << it -> first << ' ' << it -> second << endl;
	it++;
}
```
* 利用mp.lower_bound(key)和mp.upper_bound(key)便可以实现遍历相对于key值的所有元素
```c++
auto it = mp.lower_bound(key);
while(it != mp.end())
{
	cout << it -> first << ' ' << it -> second << endl;
	it++;
}
```
### 其他
* 要访问key，为了不增加元素数量导致效率变低，要通过mp.count(key)来判断键值是否存在
```c++
if(mp.count(key))
	cout << key << ' ' << mp[key]
```
* 加速访问的unordered_map
* 利用哈希表加速访问
## set集合
### 头文件
```c++
#include<set>
```
### 定义
```c++
set<type> se;
```
### 方法函数
|代码|作用|
|-|-|
|se.insert(x)|插入一个元素|
|se.clear()|清空|
|se.empty()|检查是否为空|
|se.erase(iterator)|删除迭代器对应的元素|
|se.erase(first,last)|删除[first,lase)的元素|
|se.erase(key_value)|删除key_value元素|
|se.begin()/end()/rbegin()/rend()|迭代器|
|se.find(key_value)|返回该元素对应迭代器，没找到返回se.end()|
|se.lower_bound(key_value)|返回第一个>=key元素的迭代器|
|se.upper_bound(key_value)|返回第一个>key元素的迭代器|
* set内元素默认从小到大排序
## pair二元组
### 头文件
```c++
#include<pair>
```
### 定义
```c++
pair<type1,type2> pa;
```
### 访问
```
cout << pa.first << ' ' << pa.second;
```
## string字符串
### 头文件
```cpp
#include<string>
```
### 定义
```cpp
string str;//空

string str1("123");//复制
string str2(str1);//同理复制

string str3("123456",0,3);//0代表第一个字符位置,3代表个数
string str4(str2,1,4);//同上

string str5("123456",5);//取5个字符

string str6("")
```
### 
