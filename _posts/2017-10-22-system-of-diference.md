---
layout: post
title: "差分约束系统"
date: 2017-10-22
excerpt: "图的奇异用法"
tags: [Algorithm, Graph]
comments: true
---
# 差分约束系统
下列文字摘自百度百科

如果一个系统由n个变量和m个约束条件组成，形成m个形如

$$a_i-a_j\leq k (i,j\in[1,n],k为常数)$$

的不等式，则称其为差分约束系统。亦即，差分约束系统是求解一组关于特殊不等式组的方法。

## 它和图论的关系
观察上面的这个不等式，我们将它移项，即:

$$a_i\leq a_j+k$$

对比最短路的三角形不等式:

$$dist_v\leq dist_u + w $$

它们一摸一样。

因此对于这个限制条件，我们可以将其转化成由 j 向 i 连一条长为k的边。

对于所有限制条件，可以全部转化成小于等于号跑最短路，也可以全部转化成大于号跑最长路。

然后对于由无解的情况，就转换为判负/正环的问题。

细节上，建出来的图大多数是不联通的，我们需要一个超级源点S连n条权值为0的边，这样显然不会影响答案的对吧。

对于小于/大于号，就通过权值-/+1(整数型)或者-/+eps(实数型)即可

## 常用模板
这个时候SPFA的作用就出来了

### bfs_spfa
```cpp
inline void spfa(int s){
    memset(dis,-0x3f,sizeof(dis));
    q.push(s),vis[s]=1,dis[s]=0;
    while(!q.empty()){
        const int u=q.front();
        q.pop();
        vis[u]=0;
        for(int e=pre[u];e;e=nx[e])
            if(dis[to[e]]<dis[u]+w[e]){
                dis[to[e]]=dis[u]+w[e];
                if(!vis[to[e]]) q.push(to[e]),vis[to[e]]=1;
            }
    }
}
```

### dfs_spfa(用于判负环很快)
```cpp
bool spfa(int u){
    vis[u]=1;
    for(register int e=pre[u];e;e=nx[e]){
        const int v=to[e];
        if(dis[v]>dis[u]+w[e]){
            dis[v]=dis[u]+w[e];
            if(vis[v]) return 0;
            if(!spfa(v)) return 0;
        }
    }
    vis[u]=0;
    return 1;
}
```

## 一些题目

### 1.种树
[题意&&数据范围](https://www.luogu.org/problem/show?pid=1250)

大概这可以算是一道入门题吧。

设 $$S_i$$为前i家种树的前缀和，则依题意有

$$S_E-S_{B-1}\geq T$$

$$S_i-S_{i-1}\geq 0$$

$$S_i-S_{i-1}\leq 1$$

因此直接连边跑就好了

```cpp
#ifndef X_CPP
#define X_CPP
#include<cstdio>
#include<cstring>
#include<queue>
#define Files "work"
using namespace std;
#undef read
#undef write
#ifdef Files
#undef redir
#define redir(name) freopen(name".in","r",stdin),freopen(name".out","w",stdout)
extern inline char gc(){
    static char buf[1<<17],*p1=buf,*p2=buf;
    return p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<17,stdin),p1==p2)?EOF:*p1++;
}
#else
#undef gc
#define gc getchar
#endif
template <class T>
extern inline void read(T&n){
    int sign=1;register char ch=gc();
    for(n=0;(ch<'0'||ch>'9')&&ch!='-';ch=gc());
    for(ch=='-'?ch=gc(),sign=-1:0;ch>='0'&&ch<='9';ch=gc()) n=(n<<1)+(n<<3)+ch-'0';
    n*=sign;
}
#ifdef Files
namespace out {
    char buf[1<<17],*p1=buf,*p2=buf+(1<<17);
}
using namespace out;
extern inline void pc(register char ch) {
    *(p1++)=ch,p1==p2?fwrite(buf,1,p1-buf,stdout),p1=buf:0;
}
#else
#undef pc
#define pc putchar
#endif
template <class T>
extern inline void write(T val) {
    if(val<0) pc('-'),val=-val;
    if(!val) pc('0');
    register int num=0;
    char ch[24];
    while(val) ch[++num]=val%10+'0',val/=10;
    while(num) pc(ch[num--]);
}
#ifndef _STL_ALGOBASE_H
#undef max
#undef min
template <class T> inline T max(const T a,const T b){return a>b?a:b;}
template <class T> inline T min(const T a,const T b){return a<b?a:b;}
#endif
template <class T> inline void ckmax(T&a,const T b){a<b?a=b:0;}
template <class T> inline void ckmin(T&a,const T b){a>b?a=b:0;}
const int N=30010,M=1000010;
int pre[N],nx[M],to[M],cnt,w[M],n,m;
inline void add(int u,int v,int c){
    nx[++cnt]=pre[u],pre[u]=cnt,to[cnt]=v,w[cnt]=c;
}
queue<int> q;
bool vis[N];
int dis[N];
inline void spfa(int s){
    memset(dis,-0x3f,sizeof(dis));
    q.push(s),vis[s]=1,dis[s]=0;
    while(!q.empty()){
        const int u=q.front();
        q.pop();
        vis[u]=0;
        for(int e=pre[u];e;e=nx[e])
            if(dis[to[e]]<dis[u]+w[e]){
                dis[to[e]]=dis[u]+w[e];
                if(!vis[to[e]]) q.push(to[e]),vis[to[e]]=1;
            }

    }
}
int main(){
#ifdef Files
    if(fopen(Files".in","r")) redir(Files);
#endif
    read(n),read(m);
    for(register int i=1,u,v,c;i<=m;++i) read(u),read(v),read(c),add(u-1,v,c);
    for(register int i=1;i<=n;++i) add(i-1,i,0),add(i,i-1,-1);
    spfa(0);
    write(dis[n]);
#ifdef Files
    fwrite(buf,1,p1-buf,stdout);
#endif
    return 0;
}
#endif
```

### 2.小k的农场
[题意&&数据范围](https://www.luogu.org/problem/show?pid=1993)

依然根据题意直接得到条件

设$$D_i$$为第i个农场种植的作物，则对于每个条件分别有:

$$D_a-D_b\geq D_c$$

$$D_a-D_b\leq D_c$$

$$D_a=D_b$$

对于相等的边直接一条权值为0的双向边即可

```cpp
#ifndef X_CPP
#define X_CPP
#include<cstdio>
#include<cstring>
#include<queue>
#define Files "work"
using namespace std;
#undef read
#undef write
#ifdef Files
#undef redir
#define redir(name) freopen(name".in","r",stdin),freopen(name".out","w",stdout)
extern inline char gc(){
    static char buf[1<<17],*p1=buf,*p2=buf;
    return p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<17,stdin),p1==p2)?EOF:*p1++;
}
#else
#undef gc
#define gc getchar
#endif
template <class T>
extern inline void read(T&n){
    int sign=1;register char ch=gc();
    for(n=0;(ch<'0'||ch>'9')&&ch!='-';ch=gc());
    for(ch=='-'?ch=gc(),sign=-1:0;ch>='0'&&ch<='9';ch=gc()) n=(n<<1)+(n<<3)+ch-'0';
    n*=sign;
}
#ifdef Files
namespace out {
    char buf[1<<17],*p1=buf,*p2=buf+(1<<17);
}
using namespace out;
extern inline void pc(register char ch) {
    *(p1++)=ch,p1==p2?fwrite(buf,1,p1-buf,stdout),p1=buf:0;
}
#else
#undef pc
#define pc putchar
#endif
template <class T>
extern inline void write(T val) {
    if(val<0) pc('-'),val=-val;
    if(!val) pc('0');
    register int num=0;
    char ch[24];
    while(val) ch[++num]=val%10+'0',val/=10;
    while(num) pc(ch[num--]);
}
#ifndef _STL_ALGOBASE_H
#undef max
#undef min
template <class T> inline T max(const T a,const T b){return a>b?a:b;}
template <class T> inline T min(const T a,const T b){return a<b?a:b;}
#endif
template <class T> inline void ckmax(T&a,const T b){a<b?a=b:0;}
template <class T> inline void ckmin(T&a,const T b){a>b?a=b:0;}
const int N=30010,M=1000010;
int pre[N],nx[M],to[M],cnt,w[M],n,m;
inline void add(int u,int v,int c){
    nx[++cnt]=pre[u],pre[u]=cnt,to[cnt]=v,w[cnt]=c;
}
bool vis[N];
int dis[N];
bool spfa(int u){
    vis[u]=1;
    for(register int e=pre[u];e;e=nx[e]){
        const int v=to[e];
        if(dis[v]>dis[u]+w[e]){
            dis[v]=dis[u]+w[e];
            if(vis[v]) return 0;
            if(!spfa(v)) return 0;
        }
    }
    vis[u]=0;
    return 1;
}
int main(){
#ifdef Files
    if(fopen(Files".in","r")) redir(Files);
#endif
    read(n),read(m);
    for(register int i=1,op,u,v,c;i<=m;++i){
        read(op);
        if(op==1) read(u),read(v),read(c),add(u,v,-c);
        else if(op==2) read(u),read(v),read(c),add(v,u,c);
        else read(u),read(v),add(u,v,0),add(v,u,0);
    }
    for(register int i=1;i<=n;++i) add(0,i,0);
    memset(dis,0x3f,sizeof(*dis)*(n+1));
    dis[0]=0;
    puts(spfa(0)?"Yes":"No");
#ifdef Files
    fwrite(buf,1,p1-buf,stdout);
#endif
    return 0;
}
#endif
```

### 3.狡猾的商人
[题意&&数据范围](https://www.luogu.org/problem/show?pid=2294)

思想和第一题有一点像，考虑用前缀和来解决问题，限制条件:

$$ G_t-G_{s-1}=v$$

判断有无负环即可。

好像也可以用并查集来做啊。

```cpp
#ifndef X_CPP
#define X_CPP
#include<cstdio>
#include<cstring>
#include<queue>
#define Files "work"
using namespace std;
#undef read
#undef write
#ifdef Files
#undef redir
#define redir(name) freopen(name".in","r",stdin),freopen(name".out","w",stdout)
extern inline char gc(){
    static char buf[1<<17],*p1=buf,*p2=buf;
    return p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<17,stdin),p1==p2)?EOF:*p1++;
}
#else
#undef gc
#define gc getchar
#endif
template <class T>
extern inline void read(T&n){
    int sign=1;register char ch=gc();
    for(n=0;(ch<'0'||ch>'9')&&ch!='-';ch=gc());
    for(ch=='-'?ch=gc(),sign=-1:0;ch>='0'&&ch<='9';ch=gc()) n=(n<<1)+(n<<3)+ch-'0';
    n*=sign;
}
#ifdef Files
namespace out {
    char buf[1<<17],*p1=buf,*p2=buf+(1<<17);
}
using namespace out;
extern inline void pc(register char ch) {
    *(p1++)=ch,p1==p2?fwrite(buf,1,p1-buf,stdout),p1=buf:0;
}
#else
#undef pc
#define pc putchar
#endif
template <class T>
extern inline void write(T val) {
    if(val<0) pc('-'),val=-val;
    if(!val) pc('0');
    register int num=0;
    char ch[24];
    while(val) ch[++num]=val%10+'0',val/=10;
    while(num) pc(ch[num--]);
}
#ifndef _STL_ALGOBASE_H
#undef max
#undef min
template <class T> inline T max(const T a,const T b){return a>b?a:b;}
template <class T> inline T min(const T a,const T b){return a<b?a:b;}
#endif
template <class T> inline void ckmax(T&a,const T b){a<b?a=b:0;}
template <class T> inline void ckmin(T&a,const T b){a>b?a=b:0;}
const int N=105,M=10010;
int pre[N],nx[M],to[M],cnt,w[M],n,m;
inline void add(int u,int v,int c){
    nx[++cnt]=pre[u],pre[u]=cnt,to[cnt]=v,w[cnt]=c;
}
bool vis[N];
int dis[N];
bool spfa(int u){
    vis[u]=1;
    for(register int e=pre[u];e;e=nx[e]){
        const int v=to[e];
        if(dis[v]>dis[u]+w[e]){
            dis[v]=dis[u]+w[e];
            if(vis[v]) return 0;
            if(!spfa(v)) return 0;
        }
    }
    vis[u]=0;
    return 1;
}
int main(){
#ifdef Files
    if(fopen(Files".in","r")) redir(Files);
#endif
    register int T;
    read(T);
    while(T--){
        read(n),read(m);
        cnt=0;
        memset(pre,0,sizeof(pre));
        for(register int i=1,u,v,c;i<=m;++i){
             read(u),read(v),read(c);
             add(v,u-1,-c),add(u-1,v,c);
        }
        for(register int i=1;i<=n;++i) add(n+1,i,0);
        memset(vis,0,sizeof(*vis)*(n+1));
        memset(dis,0x3f,sizeof(*dis)*(n+1));
        dis[n+1]=0;
        puts(spfa(n+1)?"true":"false");
    }
#ifdef Files
    fwrite(buf,1,p1-buf,stdout);
#endif
    return 0;
}
#endif
```

### 4.糖果
[题意&&数据范围](https://www.luogu.org/problem/show?pid=3275)

限制条件

$$A=B$$

$$ A \leq B-1 $$

$$A\geq B$$

$$ A \geq B+1 $$

$$A\leq B$$

依然直接跑就好了

```cpp
#ifndef X_CPP
#define X_CPP
#include<cstdio>
#include<cstring>
#include<queue>
#define Files "work"
using namespace std;
#undef read
#undef write
#ifdef Files
#undef redir
#define redir(name) freopen(name".in","r",stdin),freopen(name".out","w",stdout)
extern inline char gc(){
    static char buf[1<<17],*p1=buf,*p2=buf;
    return p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<17,stdin),p1==p2)?EOF:*p1++;
}
#else
#undef gc
#define gc getchar
#endif
template <class T>
extern inline void read(T&n){
    int sign=1;register char ch=gc();
    for(n=0;(ch<'0'||ch>'9')&&ch!='-';ch=gc());
    for(ch=='-'?ch=gc(),sign=-1:0;ch>='0'&&ch<='9';ch=gc()) n=(n<<1)+(n<<3)+ch-'0';
    n*=sign;
}
#ifdef Files
namespace out {
    char buf[1<<17],*p1=buf,*p2=buf+(1<<17);
}
using namespace out;
extern inline void pc(register char ch) {
    *(p1++)=ch,p1==p2?fwrite(buf,1,p1-buf,stdout),p1=buf:0;
}
#else
#undef pc
#define pc putchar
#endif
template <class T>
extern inline void write(T val) {
    if(val<0) pc('-'),val=-val;
    if(!val) pc('0');
    register int num=0;
    char ch[24];
    while(val) ch[++num]=val%10+'0',val/=10;
    while(num) pc(ch[num--]);
}
#ifndef _STL_ALGOBASE_H
#undef max
#undef min
template <class T> inline T max(const T a,const T b){return a>b?a:b;}
template <class T> inline T min(const T a,const T b){return a<b?a:b;}
#endif
template <class T> inline void ckmax(T&a,const T b){a<b?a=b:0;}
template <class T> inline void ckmin(T&a,const T b){a>b?a=b:0;}
const int N=100010,M=1000010;
template<class T,int V_size,int E_size>
class Graph{
    public:
        struct Edge{
             T val;
             int link;
             Edge *nx;
             Edge(void):val(0),link(0),nx(0){}
        }_ed[E_size+1],*e_cnt,*pre[V_size+1];
        inline void init(){
            e_cnt=_ed;
        }
        inline void add(int u,int v){
            e_cnt->nx=pre[u];
            e_cnt->link=v;
            pre[u]=e_cnt++;
        }
        inline void add(int u,int v,T c){
            e_cnt->nx=pre[u];
            e_cnt->link=v;
            e_cnt->val=c;
            pre[u]=e_cnt++;
        }
        inline Edge *head(int u){
            return this->pre[u];
        }
};
Graph<int,N,M> g;
bool vis[N];

long long dis[N];
bool spfa(const int u){
    vis[u]=1;
    for(Graph<int,N,M>::Edge *e=g.head(u);e!=NULL;e=e->nx){
        const int v=e->link;
        if(dis[v]>dis[u]+e->val){
            dis[v]=dis[u]+e->val;
            if(vis[v])return 0;
            if(!spfa(v)) return 0;
        }
    }
    vis[u]=0;
    return 1;
}

int main(){
#ifdef Files
    if(fopen(Files".in","r")) redir(Files);
#endif
    int n,m;
    read(n),read(m);
    g.init();
    for(register int i=1,p,u,v;i<=m;++i){
        read(p),read(u),read(v);
        if(p==1) g.add(u,v,0),g.add(v,u,0);
        else if(p==2){
            if(u==v) return puts("-1"),0;
            g.add(u,v,-1);
        }else if(p==3) g.add(v,u,0);
        else if(p==4){
            if(u==v) return puts("-1"),0;
            g.add(v,u,-1);
        }
        else g.add(u,v,0);
    }
    memset(dis,0x3f,sizeof(*dis)*(n+1));
    for(register int i=n;i;--i) g.add(0,i,-1);
    dis[0]=0;
    if(!spfa(0)) puts("-1");
    else{
        long long ans=0;
        for(register int i=1;i<=n;++i) ans-=dis[i];
        printf("%lld",ans);
    }
#ifdef Files
    fwrite(buf,1,p1-buf,stdout);
#endif
    return 0;
}
#endif
```

## 总结
上面列出了一些不算很难的题目，可以发现这个东西算法并不难，难点在于建模，有一种网络流的感觉呢。

建模的话，还是要多做题来理解吧。
