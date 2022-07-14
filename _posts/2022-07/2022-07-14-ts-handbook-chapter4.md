---
layout: post
title: "TypeScript 手册，章节4"
subtitle: "TS中的对象"
date: 2022-07-14 16:00:00
author: "PhcPro"
catalog: false
header-style: text
tags:
  - typescript
  - 翻译
--- 

### 向函数传递参数

```ts
// 方法1：字面量
function abc(arg: {name: string, age: number}) {}
// 方法2：接口
interface ABC {name: string, age: number}
function abc(arg: ABC) {}
// 方法3：类型别名
type ABC = {name: string, age: number}
function abc(arg: ABC) {}
```

### 属性修饰符

#### 可选属性

>解构赋值无法编写类型注释，冒号右侧在 js 中是属性别名

```ts
interface Point {
    x: number;
    y: number;
    z?: number;
}
// 自动推断 z 属性
function getPoint(point: Point) {
    let px = point.x;
    let py = point.y;
    let pz = point.z;
}
// 三元运算符
function getPoint(point: Point) {
    ...
    let pz = point.z === undefined ? 0 : point.z;
}
// 结构赋值默认值
function getPoint({x, y, z = 0}: Point) {
    let px = x;
    let py = y;
    let pz = z;
}
```

#### 只读属性 [readonly]

>***readonly 修饰的属性本身不可被覆盖***
>***readonly 修饰的是对象，对象内部可以修改***
```ts
interface SomeType {
    readonly prop: string;
}
function doSomething(obj: SomeType) {
    obj.prop = 123; // error
}
```

#### 只读属性别名 [readonly]

>具有相同定义但是具有readonly修饰符的类型别名，可以包含没有 readonly 的类型别名。

```ts
interface Point { name: string }
interface ReadonlyPoint { name: string }
let writeablePoint: Point = { name: 'xxx' }
let readonlyPoint: ReadonlyPoint = writeablePoint;

writeablePoint.name = 'yyy'
readonlyPoint.name = 'zzz' //error
```

#### 索引签名

>***索引签名类型不能是泛型***
>***索引签名类型只能是string或number***
>***索引签名强制所有属性类型统一***

之所以索取签名强制所有属性类型统一，是因为访问类型的这些属性可以通过索引签名访问，类型不统一会矛盾。即 obj.prop = obj['prop']

```ts
interface StringArray<T> { 
    [index: number | string]: T; 
}
interface StringArray<T> {
    [prop: T]: string; // error
}
interface StringArray<T> {
    [prop: number | string]: T;
    a: T;
    b: T;
    c: number;  //error
}
```

#### 索引签名 [readonly]

```ts
interface StringArray<T> { 
    readonly [index: number | string]: T; 
}
const array: StringArray = ['1','2','3'];
array[1] = 'xxx';   // error
```

### 扩展类型 [extends]

```ts
// 扩展一个
interface BasicAddress {}
interface ExtendAddress extends BasicAddress {}
// 扩展多个
interface BasicAddress1 {}
interface BasicAddress2 {}
interface ExtendAddress extends BasicAddress1, BasicAddress2 {}
```

### 交叉类型 [&运算符]

```ts
interface A {}
interface B {}
type UnionType = A & B;
function getABC(type: A & B) {}
```

### 通用对象类型

>通过泛型实现通用类型。
>最佳实践：泛型代替重载
```ts
// 通用接口
interface Prop<T> {
    prop: T;
}
// 通用类型别名
type Type<T> = {
    prop: T;
}
// 泛型代替重载
function test<T>(a: T): T[] {}
```

### Array
>Array关键字表示一种泛型类型。以下是代码形式：
```ts
interface Array<T> {
    length: number;
    pop(): T | undefined;
    push(...items: T[]): number;
    // 其他数组实例方法...
}
```

>其他类似类型：Map<K,V>、 Set<T>、 Promise<T>
>Array 具有构造函数

#### Array类型
```ts
function abc(arg: Array<string>) {}
```

#### ReadonlyArray类型
>ReadonlyArray用于描述不应更改的数组
>没有相应的构造函数
>简写语法：readonly T[]
>Readonly T[] 和 T[]写法不可相互赋值
```ts
function abc(arg: ReadonlyArray<string>) {}
```

### 元组类型 [Tuple]
>元组类型是另一种类型Array，它确切地知道它包含多少个元素，以及它在特定位置包含哪些类型。
>元组可以具有可选属性，且可选属性只能出现在末尾，会影响length

```ts
// 写法一
type ABC = [string, number];

// 写法二
interface StrNumPair {
    length: 2;
    0: string;
    1: number;
    // 实例方法
    slice(start?: number, end?: number): Array[string | number];
}
// 写法三：具有剩余元素的元组
type ABC = [string, number, ...boolean[]];
type ABC = [string, ...boolean[], number];
type ABC = [...boolena[], string, number];
```