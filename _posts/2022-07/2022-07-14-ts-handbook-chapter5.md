---
layout: post
title: "TypeScript 手册，章节5"
subtitle: "TS中的复合类型——几种创建方式"
date: 2022-07-14 16:00:00
author: "PhcPro"
catalog: false
header-style: text
tags:
  - typescript
  - 翻译
--- 

### 泛型

#### 泛型类型
函数使用泛型的写法
```ts
// 函数使用泛型的几个写法
function identity<Type>(arg: Type): Type {
    return arg;
}
declare type Input = {
    key: string;
    value: any;
}
// 写法1：
let myIdentity1: (arg: string) => string = identity;
// 写法2：
let myIdentity2: <Input>(arg: Input) => Input = identity;
// 写法3：字面量写法【我认为非常重要的写法】
let myIdentity3: { <TypeName>(arg: TypeName): TypeName } = identity;
```
接口使用泛型的写法
```ts
// 接口泛型写法1：
interface GenericIdentityFn1 {
    <TypeName>(arg: TypeName): TypeName;
}
// 接口泛型写法2：
interface GenericIdentityFn2<TypeName> {
    (arg: TypeName): TypeName;
}
// 接口泛型+函数使用泛型的写法4：
let myIdentity4: GenericIdentityFn = identity;
```

#### 泛型类
```ts
class Student<TypeName> {}
const student = new Student<string>();
```

#### 通用约束(extends 解决)
通用类型可能不是string或者数组类型，那么读取length就可能出错。使用extends可以解决。如下
```ts
// 写法1：继承具有length属性的接口
interface LengthInterface { length: number; }
function abc<Type extends LengthInterface>(arg: Type): Type {
    return arg.length;
}
// 写法2：继承具有length的类型别名
declare type LengthName = { length: number; }
function abc<Type extends LengthName>(arg: Type): Type {
    return arg.length;
}
```

#### 泛型约束中使用类型参数
```ts
function abc<Type, Key extends keyof Type>(arg: Type, key: Key): void {}
```

#### 泛型中使用类类型
```ts
function create<Type>(c: { new(): Type }): Type {
    return new c();
}
```
一个用于混入mixins的例子
```ts
class TestA {}
class TestB {}
class ABC {}

class ABC_1 extends ABC {}
class ABC_2 extends ABC {}

function foo<Type extends ABC>(arg: new () => Type): Type {
    return new arg();
}
```

### keyof 运算符
keyof可以生成字符串（和/或）数字（联合）类型。
需要注意的是，js对象始终将键转化为字符串。
```ts
declare type Test1 = { [n: number]: any }
type A = keyof Test1 // number

declare type Test2 = { [n: string]: any }
type B = keyof Test2 // string | number
```

### typeof 运算符【有限制的使用】
>js和ts分别有typeof概念
- js的typeof用于产生原始类型。
- ts的typeof用于产生定义类型。
```ts
type Abc = (arg: unknown) => boolean;
type K = ReturnType<Abc>;   // correct

function Abc() {}
type K = ReturnType<Abc>    // error
type K = ReturnType<typeof Abc> // correct
```
>ts的typeof的使用限制【ts的typoef与js的typeof不同点】
ts的typeof不可用于【生成】【函数执行】的结果。**也就是说typeof 不认识函数执行。**

### 索引访问类型

#### 场景1：通过索引查找特定属性
```ts
type Person = { a: number; b: string; }
type ABC = Person['a']; // => type ABC = number;
```

#### 场景2：索引类型+联合类型
```ts
type Person = { a: number; b: string; }
type ABC = Person['a' | 'b']; // number | string
type ABC = Person[keyof Person]; // number | string
type Temp = 'a' | 'b';
type ABC = Person[Temp]; // number | string
```

#### 场景3：索引类型+数组
```ts
const array = [
    { name: 'abc', age: 10 },
    { name: 'test', age: 12 }
]
type Person = typeof array[number];
type person = typeof array[number]['age'];
```

#### 场景4：const陷阱
```ts
type Person = { name: string; age: number; }
const t = 'age';
type P = Person[t]; // error, t是值，不是类型
type P = Person[typeof t];  // correct
```

### 条件类型
>条件类型的形式：
>SomeType **extends** OtherType ? TypeA : TypeB;

#### 场景1：优化函数重载
这个场景下，通过泛型+条件类型，处理实现函数重载时，每添加一个类型，（实现的?）代码将指数型增长。
```ts
interface I1 {}
interface I2 {}
// 重载1
function get(id: number): I1;
// 重载2
function get(str: string): I2;
// 重载3
function get(idOrStr: number|string): I1 | I2;
// 函数实现
function get(idOrStr: number|string): I1 | I2 {
    throw "xxx";
}

// 类型别名（条件类型+泛型）
type I1OrI2<T extends number | string> = T extends number ? I1 : I2;
// 函数实现
function get<T extends number | string>(idOrStr: T): I1OrI2 {
    throw "xxx";
} 
```

#### 场景2：代替约束
在这个场景下，条件类型可以充分发挥其优势，可以在没有约束的情况下进行类型判断。
```ts
// 有约束：
type MessageOf<T extends {message: unknown}> = T['message'];
// 条件类型（无约束）：
type MessageOf<T> = T extends {message: unknown} ? T['message'] : never;
```

#### 场景3：推断
在这个场景下，利用条件类型+infer关键字实现推断。
>**infer关键字，可以引入一个泛型类型变量**
>**需要注意的是：infer关键字仅在条件类型中可用**
```ts
type Flatten<T> = T extends Array<infer R> ? R : T;
```

#### 场景4：联合类型陷阱
在这个场景下，条件类型+联合类型有时会产生非预期的结果。如下
>关键点：条件类型中，在extends前后使用方括号包裹

```ts
// 合法，非预期：string[] | number[]
type ToArray<T> = T extends any ? T[] : never;
type StrArrOrNumArr = ToArray<string | number>;

// 合法，预期：(string | number)[]
type ToArray<T> = [T] extends [any] ? T[] : never;
type StrArrOrNumArr = ToArray<string | number>;
```

### 映射类型

映射类型，基于“索引访问类型”。
>所谓映射类型：指的是将类型A通过类型B（一个映射类型）映射为一个新的类型C；
>在正常情况下（没有过滤/重映射等）其中，类型C的属性名同类型A，属性值类型同类型B；

```ts
type MapType<T> = {
    [prop in keyof T]: boolean; // 我认为重要的写法
}
type A = {
    get: <T>(arg: T) => T;
    set: () => void;
}
type MapA = MapType<A>;
// 映射结果：
// MapA = { get: boolean; set: boolean; }
```

#### 映射修饰符
>基于**readonly**和 **？** 关键字（修饰符），ts提供了两个修饰符：+ / -，用于增加/删除它们。
>**需要注意的是**：修饰符影将会响映射结果。此外，最终的映射结果（如果不添加任何修饰符），默认都添加了（+修饰符）。

修饰符对readonly的映射结果影响：
```ts
type MapType<T> = {
    -readonly [prop in keyof T]: T[prop];
}
type ABC = {
    readonly id: number;
    readonly name: string;
}
type MapABC = MapType<ABC>;
// 映射结果：
// MapABC = { id: number; set: string; }
```

修饰符对？的映射结果影响：
```ts
type MapType<T> = {
    [prop in keyof T]-?: T[prop];
}
type ABC = {
    id?: number;
    name?: string;
}
type MapABC = MapType<ABC>;
// 映射结果：
// MapABC = { id: number; set: string; }
```
#### 重映射关键字：as
版本要求：ts4.1+
>可以这样理解as关键字。在普通映射下，属性名因为一一映射，故保持不变。as关键字可以对映射后的属性名再次映射（包含过滤，重命名等操作）。如下

```ts
// as+模板字符串：重命名
// 映射结果:
// {getName: () => string; getId: () => number;}
type ABC<T> = {
    [prop in keyof T as `get${Capitalize<string&prop>}`]: 
    () => T[prop];
}
interface Person {
    name: string;
    id: number;
}
type MyPerson = ABC<Person>;

// as+Exclude内置类型：过滤键
// 映射结果：
// {name: string;}
type ABC<T> = {
    [prop in keyof T as Exclude<prop, 'kind'>]: T[prop];
}
interface Person {
    name: string;
    kind: boolean;
}
type MyPerson = ABC<Person>;
```

#### 映射类型+联合类型（复合类型）
映射类型可以结合包括原始类型、复合类型的联合类型。如下
```ts
type SquareEvent = { 
    kind: 'square';
    x: number;
    y: number;
}
type CircleEvent = { 
    kind: 'circle';
    radius: number;
}
type MapType<T extends {kind: string}> = {
    [E in keyof T as E['kind']]: (event: E) => void;
}
type Conifg = EventConfig<SquareEvent | CircleEvent>;
```
#### 映射类型+条件类型
```ts
type DBFields = {
    id: {format: 'abc'};
    name: {type: string, pii: true};
}
type MapExact<T> = {
    [prop in keyof T]: T[prop] extends {pii: true} ? true : false; 
}
```

### 模板文字类型

#### 场景1：文字的排列组合
```ts
type StrA = 'a' | 'b' | 'c' | 'd' | 'e';
type StrB = '0' | '1' | '2' | '3' | '4';
type StrC = 'A' | 'B' | 'C' | 'D' | 'E';

// 组合
type CombineStr = `${StrA}_${StrB}_${StrC}_id`;
// 排列
type CombineStr = `${StrA | StrB | StrC}_id`
```