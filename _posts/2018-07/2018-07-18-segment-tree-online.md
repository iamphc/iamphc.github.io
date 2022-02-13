---
layout: post
title: "线段树（在线）整理"
subtitle: "Line segment tree (online) sorting"
date: 2018-07-18 00:37:17
author: "PhcPro"
catalog: false
header-style: text
tags:
  - 数据结构
  - 树
--- 

>线段树就是一种组织数据的结构，它分为了一维数组和结构体数组的方式。

>一维数组的优点：函数写着简单；
>缺点：时间长；

>结构体数组优点：时间短
>缺点：耗费空间；

---

线段树的几个基本：（以结构体为例）,​以[hdu1754](http://acm.hdu.edu.cn/showproblem.php?pid=1754)为例

​
1.建立结构体：

```c++
struct tree{
  int l,r,v;
  int tag;
};
```

2.建立线段树：
```c++
void build(int c,int l,int r){
  t[c].l=l,t[c].r=r,t[c].tag=0;
  if(l==r)
  {
      t[c].v=a[l];//事先保存数组a；
      return;    
  }
  int mid=(l+r)/2;
  build(c<<1,l,mid);
  build(c<<1|1,mid+1,r);
  t[c].v=max(t[c<<1].v,t[c<<1|1].v);
}
```

3.单点更新：
```c++
void update(int c,int pod ,int v)
{
    if(t[c].l==t[c].r)    {t[c].v=v;reutn;}
    int mid=(t[c].l+t[c].r)/2;
    if(pos<=mid)    update(c<<1,pos,v);
    else if(pos>mid)    update(c<<1|1,pos,v);
    t[c].v=max(t[c<<1].v,t[c<<1|1].v);
}
```

4.区间查询：
```c++
int query(int c,int l,int r)
{
  if(l<=t[c].l&&t[c].r<=r)    return t[c].v;
  int mid=(t[c].l+t[c].r)/2;
  if(l<=mid)    query(c<<1,l,r);
  else if(r>mid)    query(c<<1|1,l,r);
  else    {int a=quert(c<<1,l,mid);int b=query(c<<1|1,mid+1,r);return max(a,v);}    
}
```

5.区间更新（比如区间集体加上某个值）：
```c++
void update_(int c,int L,int R,int v)
{
  if(R<t[c].l||t[c].r<L)    return;
  if(L==t[c].l&&t[c].r==R)    {t[c].v=t[c].v+v;t[c].tag=v;return;}
  if(t[c].tag)
  {
      t[c<<1].v+=t[c].tag;
      t[c<<1|1].v+=t[c].tag;
      t[c].v=0;
  }
  int mid=(t[c].l+t[c].r)/2;
  if(L>mid)    {update_(c<<1|1,L,R,v);}
  else if(R<=mid)    {update_(c<<1,L,R,v);}
  else    {update_(c<<1,L,mid,v);update(c<<1,mid+1,R,v);}
}
```

6.离线操作要等我想起来再说orz

---

心得：
>线段树就是把一组数据给有限二分，每一个节点维护对应它的左到右的某一个属性值，子节点的变更会影响到他的父节点的值，以此类推。
>中心思想就在于线段树能够将查询分层两个方向，左和右，层层往下，如果查询的范围和当前的范围没有交集，就会忽略，从而大大加快了查询的速度，把时间复杂度从n简化到log2n。
>我觉得在线操作的最难理解的就是区间更新：
>所谓区间更新，最重要的一个准备工作就是要设置一个标志，这个标志是说上一次的更新操作的更新的量是多少。
>一般的话，如果是0，那么就不往下传递，否则，就往下传递给子节点。
>这个标志的作用也在于传递上一次的更新值，这样就不用把整个线段树的所有节点都更新一遍，节约了时间。