---
layout: post
title: "Binary-Segment-Tree-Heap?"
date: 2017-10-26
excerpt: "线段树大法好啊"
tags: [Data Structure]
comment: true
---
# Binary-Segment-Tree-Heap

我们简称它为Beap。

## 是什么

我不敢称之为一种新的数据结构,因为它是基于zkw线段树的。

这里首先附上另外两篇与这个东西有关的Blog

[Dijkstra的奇异优化 https://dormantbs.github.io/Dijkstra/](https://dormantbs.github.io/Dijkstra/)

[SGT排序 https://dormantbs.github.io/SGT-sort/](https://dormantbs.github.io/SGT-sort/)

仔细看上面两篇文章我们发现都是用线段树代替了堆的作用。

于是我们得出一个结论。

线段树可以当堆用！！

## 实现

这里以小根堆为例

### 初始化?
初始用一个数组来做容器,初始值为无穷大,并建立一颗线段树维护最小值的编号。
```cpp
int leaf,tree[N<<2];
int a[N];
int check_min(int i,int j){
  return a[i]<a[j]?i:j;
}
void init(){
  memset(a,0x7fffffff,sizeof(a));
  for(leaf=1;leaf<=n;leaf<<=1);--leaf;
  for(int i=1;i<=n;++i) tree[i+leaf]=i;
  for(int i=leaf;i;--i) tree[i]=check_min(tree[i<<1],tree[i<<1|1]);
}
```

### 插入/修改元素?
即单点修改
```cpp
int tot;//用于记录当前元素插入到的位置
int cnt;//记录当前堆中有多少元素
void change(int x,int y){
  a[x]=y,x+=leaf,x=x>>1;
  while(x) tree[x]=check_min(tree[x<<1],tree[x<<1|1]);
}
void push(int x){
  change(++tot,x);
  ++cnt;
}
```
### 取堆顶?
tree[1] 所指向的值
```cpp
int top(){
  return cnt?a[tree[1]]:-1;
}
```

### 删除?
把tree[1]指向的值删去即可(change它为inf)
```cpp
void pop(){
  change(tree[1],0x7fffffff);
  --cnt;
}
```

### 合并两个堆?
首先你要会线段树的合并,在此不再探讨。

### 时空复杂度分析
初始化: $$O(n)$$

插入/修改元素: $$O(\log n)$$

取堆顶: $$O(1)$$

删除: $$O(\log n)$$

合并: $$O(\log n)$$

空间:约 $$O(5*n)$$

### 优点
好写,常数小。效率也还不错的。

至于为什么插入元素部分还要写一个修改,具体可以去看看Dijkstra的博客,用这个东西维护一串固定的值时是可以精确修改值的。

### 缺点
显然可以看出,它最大的缺点在于空间,一个元素被删除后就会空下一个为正无穷的位置,并且不太好回收利用,因此这一点该如何优化还有待探究。

但是它在维护一组固定的值,如最短路的dist数组,就能很好的避免这一个问题啊。
