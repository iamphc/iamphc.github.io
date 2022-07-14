---
layout: post
title: "TypeScript 手册，章节2"
subtitle: "类型收拢(narrowing)"
date: 2022-07-14 16:00:00
author: "PhcPro"
catalog: false
header-style: text
tags:
  - typescript
  - 翻译
--- 

### typeof 类型保护

typeof 的结果：
- number
- string
- boolean
- symbol
- undefined
- bigint
- function
- object

```ts
function test(arg: boolean | number | null) {
    if (null && typeof arg === boolean) {}
    else if (typeof arg === number) {}
    else {}
}
```

### 真值

真值转为false，其他为true
- false
- NaN
- undefined
- null
- ""
- 0n

```ts
function test(a: number | boolean, b: number | boolean) {
    if (a === b) {}
    else {}
}
```

### 相等比较

switch-case / 严格相等 / 严格不等 / 相等 / 不等

### in 操作符

value in x.
- value: 可选属性
- x: 联合类型

```ts
type People { swim(): void }
type Fish { fly(): void }

function move(animal: People | Fish) {
    if ("swim" in animal) {}
    if ("fly" in animal) {}
}
```

### instanceof 操作符

target instanceof X.
```ts
function logValue(x: Date | string) {
    if(x instanceof Date) {}
    else {}
}
```

### 三元运算符

```ts
const value = Math.romdom() > 0.5 ?  10 : 'xxx';
```

### 类型谓词

parameterName is Type.

### 可辨识联合

- 混合定义情况下，使用属性判断（if/switch）+非空断言（xxx!）:表示该属性必定存在
```ts
interface Shape {
    kind: 'circle' | 'square';
    radius?: number;
    sideLength?: number;
}

function getArea(shape: Shape) {
    if (shape.kind === 'circle') {
        return Math.PI * shape.radius! ** 2;
    }
}
```

- 精确定义情况下，使用属性判断（if/switch）
```ts
interface Circle {
    kind: 'circle';
    radius: number;
}
interface Square {
    kind: 'square';
    sideLength: number;
}
type Shape = Circle | Square;
function getArea(shape: Shape) {
    if (shape.kind === 'circle') {
        return Math.PI * shape.radius ** 2;
    }
}
```

### never

>never类型可分配给所有类型，但除了never以外其他类型不可分配给never。
>never可用于switch的default分支。但必须保证default的类型是不存在的