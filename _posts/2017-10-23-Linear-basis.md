---
layout: post
title: "线性基"
date: 2017-10-23
excerpt: "初探线性代数"
tags: [Algorithm,Linear Algebra]
comments: true
---

# 线性基

## 基
下列文字摘自百度百科

基：在线性代数中，基（也称为基底）是描述、刻画向量空间的基本工具。向量空间的基是它的一个特殊的子集，基的元素称为基向量。向量空间中任意一个元素，都可以唯一地表示成基向量的线性组合。如果基中元素个数有限，就称向量空间为有限维向量空间，将元素的个数称作向量空间的维数。

## 线性基
线性基是一种特殊的基，它通常会在异或运算中出现，它的意义是：通过原集合S的某一个最小子集S1使得S1内元素相互异或得到的值域与原集合S相互异或得到的值域相同。

### 性质
1.线性基能相互异或能得到原集合中所有数相互异或的值。

2.线性基是满足性质1的最小集合。

3.线性基没有异或和为0的子集。

### 操作
集合异或最大/最小值，加入元素，删除元素，第k大，两个线性基合并。

### 模板
因为还不是很理解透彻就直接上模板吧qwq
```cpp
struct LB{
    long long a[61],b[61];
    LB(){
        memset(a,0,sizeof(a));
        memset(b,0,sizeof(b));
    }
    inline bool ins(long long _val){//插入元素
        for(register int i=60;i>=0;--i)
            if(_val&(1LL<<i)){
                if(!a[i]){
                    a[i]=_val;
                    break;
                }
                _val^=a[i];
            }
        return _val>0;
    }
    inline long long query_max(){//最大值
        long long res=0;
        for(register int i=60;i>=0;--i)
            if((res^a[i])>res) res^=a[i];
        return res;
    }
    inline long long query_min(){//最小值
        for(register int i=0;i<=60;++i)
            if(a[i]) return a[i];
        return 0;
    }
    inline void rebuild(){//第k大前置操作
        for(register int i=60;i>=0;--i)
            for(register int j=i-1;j>=0;--j)
                if(a[i]&(1LL<<j)) a[i]^=a[j];
        for(register int i=0;i<=60;++i)
            if(a[i]) b[++b[0]]=a[i];
    }
    inline long long kth_query(long long k){//第k大
        long long res=0;
        if(k>=(1LL<<b[0])) return -1;
        for(register int i=60;i>=0;--i)
            if(k&(1LL<<i)) res^=b[i];
        return res;
    }
};
LB merge(LB l1,LB l2){//合并两个线性基
    LB res=l1;
    for(register int i=60;i>=0;--i)
        if(l2.a[i]) l1.ins(l2.a[i]);
    return res;
}
```

### 题目

[题意&&数据范围](http://www.lydsy.com/JudgeOnline/problem.php?id=2115)

我们做一遍DFS可以得出1到任意一个节点的距离，同时也可以预处理出所有环的异或值。

通过异或运算的性质，我们不难得出只要将一条路径与任意多个环异或起来，我们就可以得到一条1到n的路径。

因此问题就变成了，给定一堆数字，求它们的异或最大值。

上线性基模板即可w

```cpp
#include<cstdio>
#include<cstring>
#define min(a,b) (a<b?a:b)
#define max(a,b) (a>b?a:b)
//#define Files "data"
using namespace std;
#ifdef Files
#define redir(name) freopen(Files".in","r",stdin),freopen("test.out","w",stdout);
inline char gc(){
    static char buf[1<<17],*p1=buf,*p2=buf;
    return p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<17,stdin),p1==p2)?EOF:*p1++;
}
#else
#define gc getchar
#endif
template <class T>
inline void read(T&n){
    int sign=1;register char ch=gc();
    for(n=0;(ch<'0'||ch>'9')&&ch!='-';ch=gc());
    for(ch=='-'?ch=gc(),sign=-1:0;ch>='0'&&ch<='9';ch=gc())(n*=10)+=ch-'0';
    n*=sign;
}
const int maxn=50000+10;
const int maxm=200000+10;
int pre[maxn],nx[maxm],to[maxm],cnt,n,m;
long long w[maxm];
inline void add(int u,int v,long long c){
    to[++cnt]=v,nx[cnt]=pre[u],pre[u]=cnt,w[cnt]=c;
}
bool vis[maxn];
long long s[maxn],res[maxm];
inline void dfs(int u){
    vis[u]=1;
    for(register int e=pre[u];e;e=nx[e]){
        if(vis[to[e]])res[++res[0]]=s[u]^s[to[e]]^w[e];
        else s[to[e]]=s[u]^w[e],dfs(to[e]);
    }
}
long long a[63];
int main(){
#ifdef Files
    if(fopen(Files".in","r")) redir(Files);
#endif
    int u,v;
    long long c;
    read(n),read(m);
    for(register int i=1;i<=m;++i)
        read(u),read(v),read(c),add(u,v,c),add(v,u,c);
    dfs(1);
    register long long ans=s[n];
    for(register int i=1;i<=res[0];++i)
        for(register int j=62;j>=0;--j)
            if(res[i]>>j){
                if(!a[j]){
                    a[j]=res[i];
                    break;
                }
                res[i]^=a[j];
            }
    for(register int i=62;i>=0;--i)
        if((ans^a[i])>ans) ans=ans^a[i];
    printf("%lld",ans);
    return 0;
}
```

## Summary
线性代数真的难。
