---
layout: post
title: "欧几里得算法（辗转相除法）"
subtitle: "Euclidean algorithm (rolling Division)"
date: 2018-03-18 22:01:24
author: "PhcPro"
catalog: false
header-style: text
tags:
  - 数论
  - 算法
--- 

欧几里得算法的目的是求出两个整数的最大公约数。
在程序中一般用gcd表示。

```c++
#include <iostream>
#include <map>
#include <string>
#include <stdio.h>
using namespace std;

int gcd(int a,int b){
  while(b>0){
    int ans=a%b;
    a=b;
    b=ans;
  }
  return a;
}

int main(){
  int a,b;
  while(scanf("%d%d",&a,&b)){
    printf("%d\n",gcd(a,b));
  }
  return 0;
}
```