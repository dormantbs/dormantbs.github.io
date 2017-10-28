---
layout: post
title: "字符串的匹配"
date: 2017-10-28
excerpt: "字符的海洋中的寻觅"
tags: [Algorithm, String]
commets: true
---

# 字符串的匹配

## 基本概念

### 文本串
我们所寻找的场所

### 模式串
我们要找的东西

### 我们要干什么
看模式串在文本穿出现的次数/位置

## 怎么做

### 1.朴素字符串匹配
暴力地一位一位匹配,看是否完全相等。

```cpp
int *Simple_string_match(char *text,char *pattern){
  for(int i=0;i<strlen(text);++i)
    if(text[i]==pattern[0]){
      bool flag=1;
      for(int j=0;j<strlen(pattern);++j)
        if(pattern[j]!=text[i+j]) flag=0;
      if(flag) pos[++pos[0]]=i;
    }
  if(!pos[0]) return NULL;
  return pos;
}
```

复杂度$$O(L1*L2)$$

### 2.KMP算法

我们先来分析一下我们要从哪些方面入手优化吧。

我们先抛弃计算机的思维来考虑一下我们现实生活中是怎么"匹配字符串的"。

比如说你在上一节语文课,你的老师让你从"他是一个很寂寞的人" 中找"寂寞" 这个词,你会一个一个去看吗?

显然不会,你发现你的视野中已经找过的位置没有这个词了,那么自然会往后面跳很大一截。

这便可以看出朴素字符串一个很大的不足了。

当你在第i位匹配失败的时候,你的前i-1位是已经匹配过了的,它们自然不会再匹配成功了,再一位一位地跳很浪费时间啊。

那么我们应该怎么跳呢?

假设我们在O中寻找f,通过当前已知的信息知道可以前移k位，考虑移动前 f的[k,i-1] 为 A,移动后 f的[0,i-k-1] 为 B，那么有以下规律:

1. A 是 f 的一个前缀
2. B 是 fail处的子串的一个后缀
3. A=B

所以前移k位,一定要满足:长度为i-k-1的前缀A和后缀B相同。

因此我们就引出了KMP算法的核心内容---fail 指针。

我们称决定模式串怎么移动的东西叫做fail指针。

由以上分析可知,对于KMP算法,我们需要求的fail指针为最长的前缀等于后缀。

考虑如何来求他。

假设我们已经知道了next[i] 要求 next[i+1]

显然,若s[i]=s[next[i]] 那么 next[i+1]=next[i]+1

否则,我们将长度为next[i]的字符串继续分割,即next[next[i]],如果相等,就等于它+1，然后继续迭代,直到为0。

```cpp
int *get_next(char *s){
  int len=strlen(s),j=0;
  int *next=new int[len+1];
  next[0]=next[1]=0;
  for(int i=1;i<len;++i){
    while(j>0&&s[i]!=s[j]) j=next[j];
    if(s[i]==s[j]) ++j;
    next[i+1]=j;
  }
  return next;
}
```

好,有了next数组,我们来搞匹配!
```cpp
int *KMP_match(char *text,char *pattern){
  int *next=new int[strlen(pattern)];
  next=get_next(pattern);
  int j=0;
  for(int i=0;i<strlen(text);++i){
    while(j>0&&text[i]!=pattern[j]) j=next[j];
    if(text[i]==pattern[j]) ++j;
    if(j==strlen(pattern)){
        pos[++pos[0]]=i-j;
        j=next[j];
    }
  }
  if(!pos[0]) return NULL;
  return pos;
}
```
复杂度$$O(L1+L2)$$

### 3.Sunday字符串匹配
这是一个90后算法。

和KMP不同的是,它的fail指针关注的是主串中参加匹配的最末位字符的下一位字符。

规则如下:

1.如果该字符没有在模式串中出现则直接跳过,即移动长度=模式串长度+1。

2.否则,其移动位数=模式串长度-该字符最右出现的位置

偏移表(shift)求法:(如果这里看不到就看程序脑补吧,本来这里是有公式的qwq)

$$
  shift_w =
  \begin{cases}
  m - max \{i \lt m \mid P_i = w \}, & \text{if $w$ is in $P_{0...m-1}$} \\
  m + 1 & \text{otherwise}
  \end{cases}
$$

```cpp
int *Sunday_match(char *text,char *pattern){
  for(int i=0;i<N;++i) shift=m+1;
  for(int i=0;i<strlen(pattern);++i) shift[pattern[i]]=m-i;
  int i=0,j;
  while(i<=strlen(text)-strlen(pattern)){
    j=0;
    while(text[i+j]==pattern[j]){
      ++j;
      if(j>=m) pos[++pos[0]]=i;
    }
    i+=shift[text[i+m]];
  }
  if(!pos[0]) return NULL;
  return pos;
}
```
复杂度

最坏$$O(L1*L2)$$

平均$$O(L1)$$
