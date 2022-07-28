---
layout: post
title: "TypeScript 手册，章节1(第1部分)"
subtitle: "TS基础：实用的内置类型（全局可用）"
date: 2022-07-14 16:00:00
author: "PhcPro"
catalog: false
header-style: text
tags:
  - typescript
  - 翻译
--- 

>前言：ts中文手册真的垃圾

### 类型别名

#### 可选与必选：Partical<T>

构造一个类型，TYPE的所有属性都设置为可选。返回一个表示给定类型的所有子集的类型。
```ts
type Partical<T> = {
    [P in keyof T]?: T[P] | undefined;
}
```

#### 可选与必选：Required<T>

构造一个类型，TYPE的所有属性都必须有。Partical的反义词。
```ts
type Required<T> = {
    [P in keyof T]-?: T[P];
}
```

#### 只读：Readonly<T>

构造一个类型，TYPE的所有属性都为只读。
```ts
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
}
```

#### 保留与排除：Pick<T,K>

构造一个类型，从T中选择一组K（字符串文字或字符串文字的联合）作为键值，作为T的子集。
```ts
type Pick<T,K extends keyof T> = {
    [P in K]: T[P];
}
```

#### 保留与排除：Omit<T,K>

构造一个类型，从T中排除一些K(字符串文字或字符串文字的联合),剩下的作为键值,作为T的子集。Pick的反义词。
```ts
type Omit<T,K extends string | number | symbol> = {
    [P in Exclude<keyof T,K>]: T[P];
}
```

### 联合类型

#### 保留与排除：Exclude<UnionType,ExcludeMembers>

构造一个类型，从UnionType中排除ExcludeMembers得到剩下的成员。
```ts
type Exclude<T,U> = T extends U ? never : T;
```

#### 保留与排除：Exact<T,U>

构造一个类型，从T中选择有U类型得到剩下的成员。Exclude的反义词。
```ts
type Exact<T,U> = T extends U ? T : never;
```

#### 非空：NonNullable<T>

构造一个类型，排除T中的null或undefined，得到剩下的类型。
```ts
type NonNullable<T> = T extends null | undefined ? never : T;
```

### 类型映射

#### Record<K,T>

构造一个对象类型，键值为K，值为T。用于将一种类型映射到另一种类型。
```ts
type Record<K extends string | number | symbol,T> = {
    [P in K]: T;
}
```

### 函数

#### 入参：Parameters<T>

构造一个类型，得到T函数类型的参数形成的元组类型。
```ts
type Parameters<T extends (...args: any) => any> =  T extends (...args: infer P) => any ? P : never;
```
```ts
type A = Parameters<any>; // unknown[]
type B = Parameters<never>; // never
type B = Parameters<string>; // -- error --
```

#### 返回：ReturnType<T>

- 构造一个类型，得到T函数类型的返回类型
- 具有多个调用签名类型进行推断（eg：函数重载），采用最后一个签名.
```ts
type ReturnType<T extends (...arg: any) => any> = T extends (...arg: any) => infer R => R : never; 
```
```ts
type A = ReturnType<any> // any
type B = ReturnType<never> // never
```

### 构造函数

#### 入参：ConstructorParameters<T>

- 构造一个类型，得到T类型中的构造函数的参数形成的元组类型。
- 具有多个构造函数，采用最后一个。
```ts
type ConstructorParameters<T extends abstract new (...arg: infer) => any> = T extends abstract new (...arg: infer P) => any ? P : never;
```

#### 实例：InstanceType<T>

构造一个类型，得到T构造函数类型的实例的类型。
```ts
type InstanceType<T extends abstract new (...arg: any) => any> = T extends abstract new (...arg: any) => infer R ? R : never;
```
```ts
type A = InstanceType<any>  // any
type B = InstanceType<never>    // never
```

### this

#### 得到：ThisParameterType<T>

构造一个类型，得到T类型的this类型。如果没有this，得到unknown。
```ts
type ThisParameterType<T> = T extends (this: infer U, ...arg: any) => any ? U : unknown;
```

#### 排除：OmitThisParameter<T>

构造一个类型，得到T类型的没有this的类型。
```ts
type OmitThisParameter<T> = unknown ThisParameterType<T> ? T : T extends (...arg: infer A) => infer R ? (...arg: A) => R : T;
```

#### 仅做标注：ThisType<T>

不会返回任何类型，用作对this类型的上下文标注。
```ts
interface ThisType<T> {}
```

### 字符串操作类型

#### Uppercase<S>

将字符串所有字符转为大写

#### Lowwercase<S>

将字符串所有字符转为小写

#### Capitalize<S>

将字符串单词的第一个字符转为大写

#### Uncapitalize<S>

将字符串单词的第一个字符转为小写