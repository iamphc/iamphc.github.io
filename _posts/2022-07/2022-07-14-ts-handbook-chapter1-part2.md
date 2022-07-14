---
layout: post
title: "TypeScript 手册，章节1(第2部分)"
subtitle: "TS基础：declare——声明的几种使用场景"
date: 2022-07-14 16:00:00
author: "PhcPro"
catalog: false
header-style: text
tags:
  - typescript
  - 翻译
--- 

### 声明重载函数
```ts
declare function get(n: number): void;
declare function get(s: string): void;
```

### 声明类
```ts
declare class Abc {
    constructor(s: string);
    abc: string;
    show(): void;
}
```

### 声明全局变量
```ts
declare var abc: number;
declare let abc: string;    // 块作用域
declare const abc: boolean; // readonly
```

### 声明全局函数
```ts
declare function get(abc: string): void;
```

### 声明（可嵌套）命名空间
>声明命名空间

```ts
declare namespace ABC {
    interface IP1 { [s: string]: string; }
    interface IP2 { [n: number]: number; }
    interface IP3<T> { [t: string]: T; }
}
```

>还可以声明嵌套的命名空间

```ts
declare namespace ABC.test {
    interface P1 { a(): string; }
}
```

### 声明具有属性的对象
```ts
declare namespace MyObj {
    function get<T>(n: T): T;
    let prop: number | string;
}
```