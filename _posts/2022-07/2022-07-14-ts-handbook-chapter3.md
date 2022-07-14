---
layout: post
title: "TypeScript 手册，章节3"
subtitle: "TS中的函数"
date: 2022-07-14 16:00:00
author: "PhcPro"
catalog: false
header-style: text
tags:
  - typescript
  - 翻译
--- 

### 基础定义

```ts
function abc(fn: (a: string) => void) {}
```

### type

#### type命名 [type alias]

```ts
type Fn = (a: string) => void;
function abc (fn: Fn) {}
```

#### type命名 [call signatures] 

```ts
type Fn = {
    desc: string;
    (a: number): void;
}
function abc(fn: Fn) {
    console.log(fn.desc + fn(123))
}
```

#### type命名 [construct signatures]

```ts
type Fn = {
    new (s: string): void;
}
function abc(fn: Fn) {
    return new Fn('sss');
}
```

### 泛型

#### 泛型 [自动推断]

当函数参数和返回值类型相同时，如何定义？

```ts
function abc<T>(arr: T[]): T | undefined {
    return arr[0];
}
abc();  
// 自动推断 T 为 undefined
abc([1,2,3]) 
// 自动推断 T 为 number
abc(['a','b', 'c']) 
// 自动推断 T 为 string
```

#### 泛型 [extends 约束]

```ts
function abc<T extends { length: number }>(a: T, b: T): T {
    if (a.length > b.length) {
        return a;
    }
    return b;
}
abc([1,2,3], [0,1])
abc('abc', 'xx')
```

#### 泛型 [extends 约束 + 子类型]

```ts
function abc<T extends {length: number}>(obj: T, num: number): T {
    if (obj.length > num) {
        return obj;
    }
    return {length: num}; // error
}
```

>以上代码会报错，因为T的子类型有length属性，但T的完整类型可能还有其他属性。

#### 泛型 [指定参数类型]

```ts
function abc<T>(arr1: T[], arr2: T[]): T[] {
    return arr1.concat(arr2);
}
abc([1,2,3], ['xxxx']) // error
abc<string | number>([1,2,3], ['xxxx'])
```

#### 泛型 [编写指南]

- 减少泛型约束（少用extends）
- 减少泛型数量
- 只有当泛型在函数出现多次时，才要使用泛型（只出现一次，不要用了）

### 函数重载（假重载/声明重载）

>通过编写重载的函数签名+一个函数实现，实现函数重载。我个人关于ts重载的看法是
```ts
function abc(a: number): void;
// 重载签名 1
function abc(a: number, b: string): Date;
// 重载签名 2
function abc(a: number, b?: string): void | Date | number {
    if (b !== undefined) {
        return new Date(b);
    }
    if (a !== undefined) {
        a += 123;
    }
    return '123';
}
// 函数实现
```

### this

#### this [自动推断]

```ts
const user = {
    id: 123,
    test: function() {
        this.id = 456;
    }
}
```

#### this [作为函数参数]

```ts
interface User {
  id: number;
  admin: boolean;
}
interface DB {
  filterUsers(filter: (this: User) => boolean): User[];
}
declare const getDB: () => DB;

const db = getDB();
// 此处不允许使用箭头函数
const admins = db.filterUsers(function (this: User) {
  return this.admin;
});
```

### 其他类型

#### 类型 [void]

- 函数无 return
- 函数 return 无显示值
- 与 js 不同的是，ts 直接return不是返回undefiend

#### 类型 [object, 非 Object]

>指任何不是原始值(string, number, bigint, boolean, symbol, null)的值

#### 类型 [unknown]

>unknown 同 any 一样，可以代表任何类型。
>unknown 与 any 不同的是，unknown 更加安全，其类型在任何操作下都非法。

#### 类型 [never]

- 函数终止或者抛出错误
- 联合类型以外的情况（if/switch）

#### 类型 [function]

>同 js 一样，具有 call，apply，bind等属性

### 剩余参数

#### 剩余形参 [parameters]

>ts 中，剩余形参的类型隐式为 any[] 而非 any
>ts 中，剩余形参的类型注释必须是 Array<T> 或 T<>

```ts
function abc(a: number, ...b: number[]): void;
abc(10, 1, 2, 3, 4)
```

#### 函数的剩余实参 [arguments]

>ts 会认为数组是可变的（即使使用 const 进行定义）。
>使用 as const 后，ts 会认为数组不变。

```ts
const args = [1,2] as const; // as const 是重点
const angle = Math.atan2(...args);
```

### 函数的解构赋值

```ts
// 写法一
function abc({a,b,c}: {a: number, b: number, c: number}) {}
// 写法二
type ABC = {a: number, b: number, c: number}
function abc({a,b,c}: ABC) {}
```

### 函数的可赋值性 [待定]

[参考网址](https://www.typescriptlang.org/docs/handbook/2/functions.html#assignability-of-functions)

### 最佳实践

#### 回调函数不使用可选参数

>回调函数不要编写’可选参数‘
```ts
// bad
function abc(a: any[], callback: (val: any, index?: number) => void) {
    return a.map(callback(val))
}
abc([1,2,3], (v,i) => console.log(v, i))    // error
```

```ts
// good
function abc(a: any[], callback: (val: any, index: number) => void) {
    return a.map(callback(val))
}
abc([1,2,3], (v,i) => console.log(v, i))
```

#### 联合类型代替重载

```ts
// bad: 重载
function abc(arg: string): void;
function abc(arg: number): void;
function abc(arg: string | number): void {
    if (typeof arg === 'string') {}
    if (typeof arg === 'number') {}
    return;
}
```
```ts
// good: 联合类型
function abc(arg: string | number) {}
```