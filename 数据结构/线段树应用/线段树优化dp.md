* 其实也没什么好说的，可能一个位置，只能从前面位置满足特定条件转移而来，而这个特定条件可能指代的是一个范围，那么我们就可以将这个特定条件对应的值作为线段树的位置，那么转移就是找特定条件对应的范围的最大或者最小值来转移
* 比如只能从前面高度大于该点的位置转移，那么就将高度作为线段树的位置，然后边放，边维护线段树即可

## [E. Pillars](https://codeforces.com/problemset/problem/474/E)

> 长度为 $n$ 的序列，只有当 $i<j\ AND\ |h_i-h_j|\geq d$ 时，才能从 $i$ 位置跳到 $j$ 位置
>
> 问最长的跳跃序列

* 那么该题的特定条件就是高度大于等于或者小于等于 $h_j+d$ ， $h_j-d$ 
* 因为问了路径，因此同时要维护最大值对应的点