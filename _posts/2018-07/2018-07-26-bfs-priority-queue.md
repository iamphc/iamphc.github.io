---
layout: post
title: "BFS & 迪克斯特拉 & 优先队列"
subtitle: "BFS &amp; dixtra &amp; priority queue"
date: 2018-07-26 21:23:45
author: "PhcPro"
catalog: true
header-style: text
tags:
  - 算法
  - 搜索
  - 数据结构
  - 队列
--- 

## 问题引入

>从引入一个问题开始：
>给出一个无向图/加权图，计算单源点到任意点的最短路长。

>解决思路（分类）：
>- 各个点到邻近点距离都相同：纯BFS或迪克斯特拉
>- 存在某一点到临近点距离不同:
>   - 迪克斯特拉（BFS+贪心+张弛, 优化方法：堆排序:O(E+nlog2 n)）
>   - SPFA（没有张弛操作，时间复杂度：O(VE)）

## 深入细节

### BFS不适用的情况
BFS不适用的情况：加权图的某点到临近点距离不相同。（试试优先队列）

### 迪克斯特拉不适用的情况
迪克斯特拉不适用的情况：不适用于含有负权边的加权图---->导致死循环（无限张弛）

### 优先队列
优先队列的特点：自动排序

- 优先队列：
头文件：<queue>

- 定义优先队列：
priority_queue<T> name;

- 基本操作：
name.empty();
name.top();//头元素
name.push();
name.pop();

### 优先队列的小于号重载
```c++
struct node {
  int a,..;
  //举例：比如a小的排在前面
  bool operator < (const node & n) const {    
    if(a>=n.a) return true;
    else return false;
  }
};
```

### 其他
重载小于号的操作也与结构体有关，bool operator < 和优先队列相似。set定义也可以用结构体。