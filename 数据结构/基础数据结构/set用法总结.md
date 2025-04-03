# set用法总结

## 不重复的高速插入的升序队列

### 1.insert()

* 插入一个元素

```c++
se.insert(x);
```

### 2.find()

* 返回对应元素的迭代器

```c++
set<int>::iterator it = se.find(x);
cout << *it;
```

### 3.erase()

* 删除单个元素

```c++
se.erase(x);//删除对应的值
se.erase(it);//删除迭代器对应的值
se.erase(it1,it2);//删除[it1,it2)范围内的值
```

### 4.size()

* 返回元素个数

### 5.clear()

* 清空

### 6.lower_bound()

* 返回第一个大于等于它数对应的的迭代器

```c++
set<int>::iterator it = se.lower_bound(x);
```

### 7.upper_bound()

* 返回第一个大于它数对应的迭代器

```c++
set<int>::iterator it = se.upper_bound(x);
```

