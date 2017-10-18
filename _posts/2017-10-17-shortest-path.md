---
layout: post
title: "最短路总结"
date: 2017-10-17
excerpt: "穿越空间的限制，走最短的路找到你"
tags: [Algorithm, Graph]
comments: true
---
## 最短路总结
### 1.最短路
u->v之间的最短路满足以下限制：
对任意k ∈ G(V,E) 有 dist u,v <= dis u,k + dis k,j
#### 关键操作-松弛
```cpp
void relax(int i,int j,int k){
  if(dis[k]>dis[i]+j)
    dis[k]=dis[i]+j;
}
```
### 2.多源点多汇点最短路
#### Floyd
```cpp
void Floyd(){
  for(int k=1;k<=n;++k)
    for(int i=1;i<=n;++i)
      for(int j=1;j<=n;++j)
        if(dis[i][j]>dis[i][k]+dis[k][j])
          dis[i][j]=dis[i][k]+dis[k][j];
}
```
复杂度 O(V^3)
可处理负环
##### 拓展
把所有边存成负的可以作为最长路。
### 3.单源点多汇点最短路
#### Bellman-Ford
```cpp
void Bellman_ford(int s){
  memset(dis,inf,sizeof(dis));
  for(int i=1;i<n;++i)
    for(int j=1;j<=m;++j)
      relax(Edge[j].u,Edge[j].w,Edge[j].v);
}
```
复杂度O(V*E)
可处理负环(判断是否还能松弛)
#### SPFA
```cpp
queue<int> q;
void SPFA(int s){
  memset(dis,inf,sizeof(dis));
  q.push(s);
  vis[s]=1;
  while(!q.empty()){
    const int u=q.front();
    q.pop();
    vis[u]=0;
    for(int e=pre[u];e;e=nx[e]){
      const int v=to[e];
      if(dis[v]>dis[u]+w[e]){
        dis[v]=dis[u]+w[e];
        if(!vis[v]) q.push(v),vis[v]=1;
      }
    }
  }
}
```
复杂度O(k*E)
可处理负环(一个点入队次数超过n次)
加上SLF和LLL优化后k很小
#### Dijikstra
```cpp
void Dijikstra(int s){
  memset(dis,inf,sizeof(dis));
  dis[s]=0;
  for(int i=1;i<=n;++i){
    int maxs=inf,u=0;
    for(int j=1;j<=n;++j)
      if(!vis[j]&&dis[j]<maxs)
        u=j,maxs=dis[j];
    vis[u]=1;
    for(int e=pre[u];e;e=nx[e]){
      const int v=to[e];
      if(!vis[v]&&dis[v]>dis[u]+w[e]) dis[v]=dis[u]+w[e];
    }
  }
}
```
复杂度O(V^2+E)
不可处理负环
可用heap,线段树等优化到O((V+E)logV),O(VlogV+E)
### 4.多源点单汇点最短路
存逆图跑单源最短路即可。
### 5.DAG最短/最长链
```cpp
//对于一个DAG可以得知它的拓扑序，从而通过DP解决问题
queue<int> q;
int DAG_shortest_path(){
  memset(dis,inf,sizeof(dis));
  for(int i=1;i<=n;++i)
    if(degree[i]==0) q.push(i);
  while(!q.empty()){
    const int u=q.front();
    q.pop();
    topo[++topo[0]]=u;
    for(int e=pre[u];e;e=nx[e]){
      const int v=to[e];
      --degree[v];
      if(degree[v]==0) q.push(v);
    }
  }
  ans=inf;
  for(int i=1;i<=topo[0];++i){
    const int u=topo[i];
    for(int e=pre[u];e;e=nx[e]){
      const int v=to[e];
      dis[v]=min(dis[v],dis[u]+w[e]);
    }
    ans=min(ans,dis[u]);
  }
  return ans;
}
```
复杂度O(V+E)
### 6.最短路算法的选择
#### 对于稀疏图
首选自然是SPFA，好打而且跑得快，而且几乎没有考试丧心病狂到卡SPFA。
如果实在不放心，就选择priority_queue+Dijikstra吧。
#### 对于稠密图
如果点数很小而且要求任意两个点之间的距离,那么就Floyd吧
否则首选是Dijikstra+priority_queue,至于为什么不选择朴素Dijikstra，因为它比较长。
#### 对于有负权的图
SPFA
#### 对于DAG且不要求源点与汇点
DAG最短/最长链
#### 总之
SPFA和priority_queue Dijikstra比较实用，一般会这两个就行了。
最好写的是Floyd。
DAG最短/最长链在某些题目中很重要。
Bellman-Ford没卵用。
