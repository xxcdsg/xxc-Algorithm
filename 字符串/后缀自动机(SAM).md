## 后缀自动机（SAM）

### 定义

> SAM是一张有向无环图。节点被称为状态，边为状态间的转移
>
> 有唯一一个起点 $t_0$
>
> 每个转移代表一个字母
>
> 存在一个或多个终止状态，从初始状态出发到终止状态，路径上所有转移连接起来一定是字符串 $s$ 的一个后缀

### 与子串的联系

> 从 $t_0$ 开始的任意路径都对应一个子串
>
> 到达某个状态的路径可能不止一条

### 构造

##### 结束位置 $endpos$

> 我们设 $endpos(t)$ 为子串 $t$ 在 $s$ 中的所有结束位置
>
> 我们将所有 $s$ 的子串 $t$ 根据集合 $endpos(t)$ 分成若干等价类
>
> 然后假设 $SAM$ 中的每个状态对应一个等价类

* 根据 $endpos$ 的值，我们可以推

>  $p1$ .若 $endpos(u)=endpos(w),|u|\leq|w|$ ，则有 $u$ 为 $w$ 的后缀，并且每次 $u$ 出现，必定是以 $w$ 后缀的形式出现的

* 显然，因为你要让两个字符串都在同一个位置结束，那么小的肯定是后缀，并且因为只在 $u$ 后缀出现，因此 $u$ 结束肯定是 $w$ 的结束

>$$
>p2.
>\begin{cases}
>endpos(w) \subseteq endpos(u),u为w的后缀\\
>endpos(w) \bigcap endpos(u)=\empty,其他
>\end{cases}
>$$

* 这也很显然，首先后缀的结束位置肯定是包含更长的子串，但是不一定只对应一个子串，因此是包含
* 如果不是后缀关系，那么肯定不会在同一个位置结束

> $p3$ .考虑 $endpos$ 等价类内部，按长度排序，那么短的字符串均为长的字符串的真后缀

* 引用 $p1$ ，内部的子串肯定是以后缀形式出现的

##### 后缀连接 $link$

* 考虑 $SAM$ 中某个状态 $v$ ，对应一个具有相同 $endpos$ 的等价类，我们假设 $w$ 为最长的一个，那么内部其他字符串均为其后缀。
* 那么 $w$ 的较长的某几个后缀包含于该等价类，而其他较小的后缀（至少一个空字符串）被包含于其他的等价类
* 我们将 $v$ 的后缀连接到包含其他较小的后缀中最长后缀的等价类状态 上
* 我们假设空字符串对应的等价类状态为 $t_0$ ，$endpos(t_0)=\set{-1,0,...,|S|-1}$ ，那么我们有

>  $p4$. 所有后缀连接构成一颗根节点为 $t_0$ 的树

* 很好证明，因为后缀变小，因此集合只会一直增大，最终到 $t_0$

>  $p5$. 通过 $endpos$ 集合构造的树（每个子节点的 $endpos$ 为父节点的 子集）与通过后缀连接 $link$ 构造的树相同

* 就是相同的呀，根据其定义不就易得
* 左图为 $SAM$ ，右图为后缀路径建立的树

![SA_suffix_links](C:\Users\Administrator\Desktop\markdown\c++\算法\字符串\SA_suffix_links.svg)

##### 小结

* 子串根据结束位置 $endpos$ 分成多个等价类
*  $SAM$ 由初始状态 $t_0$ 与每个 $endpos$ 的等价类对应的状态组成
* 每个状态 $v$ 有一个或多个子串匹配，记最长串为 $longest(v)$ 最短串为 $shortest(v)$ ，其长度分别为 $len(v)$ $minlen(v)$ ，状态 $v$ 对应的字符串长度覆盖区间 $[minlen(v),len[v]]$
*  $v$ 的后缀连接为连接到 $longest(v)$ 的 $minlen(v)-1$ 后缀对应状态，从 $t_0$ 反向出发可建成一颗树，这棵树也表示 $endpos$ 的包含关系

##### 算法

* 该算法为在线算法

* 我们可以逐个加入字符串中的字符，并且在每一步中对应地维护 $SAM$

* 其实我们就是要同时维护 $link$ 与 $next$

  > 初始化 $t_0,len=0,link=-1$ 
  >
  >  $last$ 为添加字符 $c$ 之前字符串对应的状态，初始化为 $0$
  >
  > 创建新状态 $cur$，将 $len(cur)$ 赋值为 $len(last)+1$ (创建新状态，即新的整个字符串)
  >
  > 然后从 $last$ 开始，遍历 $link$ ，如果有到某个点的字符 $c$ 转移，否则添加到 $cur$ 的转移 $next$ ，我们就停下，标记这个状态为 $p$
  >
  > 如果没找到 $p$ 就到了 $t_0$ 我们就将 $link(cur)$ 赋值为 $0$ 
  >
  > 找到 $p$ 可通过字符 $c$ 转移到 $q$
  >
  > 如果 $len(p)+1=len(q)$ ，那么我们将 $link(cur)$ 赋值为 $q$
  >
  > 否则我们克隆 $q$ ，令 $len(clone)=len(p)+1$
  >
  > 然后让 $link[cur]=link[q]=clone$
  >
  > 然后从 $p$ 往回走，将所有本来从 $p$ 到 $q$ 状态的转移都重定向到 $clone$
  >
  > 最后令 $last=cur$

##### 证明

* 证鸡毛，我会就有鬼了
*  $last$ 即整个字符串的状态，它所记录的是所有前缀(用 $link$ 连接的链表，并且用 $endpos$ 等价类压缩)
*  $cur$ 先存上 $len(last)$ 也就是最新的前缀，也就是最长的字符串，也就是整个后缀
* 然后遍历 $last$ 的 $link$ 是为了继承原本的所有数据
* 如果没有字符 $c$ 转移，直接连接，因为向前走是通过 $link$ ，$link$ 所连的状态都是后缀，那么就可以向前连接
* 那么有 $c$ 转移是什么情况呢
* 对应较小的后缀，有可能前面也出现了，前面就有可能进行了 $c$ 的转移
* 对应状态集为 $p$ 跳转为 $q$
* 如果 $q$ 是正常从 $p$ 跳转， $len(p)+1=len(q)$ 也就是 $longest(p)+c=longest(q)$
* 那么就刚好满足后缀连接的定义，即小后缀在前面出现，因此 $link(cur)=q$
* 否则， $longest(q)$ 就不一定我们的后缀了

```cpp
const int N = 1e5 + 10;
struct pos{
	int len,link;
	map<char,int> next;
}st[N*2];
int ptop,last;
void sam_init(){
	st[0].len = 0;
	st[0].link = -1;
	ptop++;
	last = 0;
}
void sam_extend(char c){
	int cur = ptop++;
	st[cur].len = st[last].len + 1;
	int p = last;
	while(p != -1 && !st[p].next.count(c)){//不存在向后走c的
		st[p].next[c] = cur;
		p = st[p].link;
	}
	if(p == -1){
		st[cur].link = 0;
	}else{
		int q = st[p].next[c];
		if(st[p].len + 1 == st[q].len){
			st[cur].link = q;
		}else{
			int clone = ptop++;
			st[clone] = st[q];
			st[clone].len = st[p].len + 1;
			while(p != -1 && st[p].next[c] == q){
				st[p].next[c] = clone;
				p = st[p].link;
			}
			st[q].link = st[cur].link = clone;
		}
	}
	last = cur;
}
```

