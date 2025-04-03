# bitset

## 定义：

```c++
bitset<n> b; //n位0
bitset<n> b(u); //unsigned long型u的一个副本(从右到左)
bitset<n> b(s); //01型字符串转换
```

## 函数：

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

## 效率：内部用unsigned long long 来实现，因此效率为O(N/64)