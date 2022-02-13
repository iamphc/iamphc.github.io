---
layout: post
title: "字典树的细节"
subtitle: "Dictionary tree details"
date: 2018-08-18 01:58:00
author: "PhcPro"
catalog: true
header-style: text
tags:
  - 数据结构
  - 树
  - 字符串
--- 

## 简述字典树
>字典树的背景
[字典树](https://baike.baidu.com/item/%E5%AD%97%E5%85%B8%E6%A0%91/9825209)

>数据结构
- 数组
- 结构体（常见）

>主要函数
- 插入函数（参数：字符串）
- 查找函数（参数：字符串）

>优势
空间换时间

>时空复杂度
- 时间复杂度：O(Max(s))。max(s)是单词的最大长度
- 空间复杂度：O(n*len)。n是单词数量，len表示平均单词长度

## 数据结构
```c++
struct node{
  int cnt;
  node *nxt[ALTN];//字母表的大小，取决于题目要求
  node() {//构造函数 
    for(int i=0; i<26; ++i) {
      nxt[i]=NULL;//初始化为空
    }
    cnt=0;//标记符
  }
};
```

## 插入函数 
```c++
void ins(char *s) {
  node *now=root;//根节点
  int len=strlen(s);//字符长度
  for(int i=0; i<len; ++i) {//遍历 
    int c=s[i]-'a';//简化
    if(now->nxt[c]!=NULL) {//判断是否要建立新的子节点
      now=now->nxt[c];
      now->cnt++;
    }
    else {
      node *tmp=new node();
      tmp->cnt=1;
      now->nxt[c]=tmp;
      now=now->nxt[c];
    }
  }
}
```

## 查找函数
以CSU1115为例
```c++
void fid(char *s) {
  node * now = root;//根节点
  int len=strlen(s);
  for(int i=0;i<len;++i) {
    int c=s[i]-'a';
    now = now->nxt[c];
    ans++;
    if(now->cnt==1) {
      return;
    }
  }
}
```

## 清空链表
```c++
void dele(node *tmp) {
  if(tmp==NULL) {
    return;
  }
  for(int i=0;i<ALTN;++i) {
    if(tmp->next[i]!=NULL) {
      dele(tmp->next[i]);
    }
  }
  delete tmp;
  return;
}
```

## 一些解释
1. 什么是根节点：
>根节点是整颗字典树的最上层的节点，他没有任何字符，也不存在标记。
2. 根节点在哪里定义？
>可以定义为全局变量。eg: node *root。
>根节点这样定义了以后，如果直接运行的话，就会报错。
>这是因为我们虽然是定义了根节点，但是他的内存是硬件随机分配的，所以他的指针是不固定的，俗称”野指针“。
>因此，我们要为他分配内存空间：
>这要在main() 函数内部进行:
>node *root = new node();//利用构造函数+内存分配=避免问题

3. 关于标记符，因题目而异。注意审题。
4. 推荐使用数组写法，使用指针容易报错。ORZ