---
layout: post
title: "react 源码系列（一）"
subtitle: "通过自己写useState, useEffect, useMemo实现react细粒度更新"
date: 2023-06-14 11:42:00
author: "PhcPro"
catalog: false
header-style: text
tags:
  - react源码
  - hooks
  - react
  - 细粒度更新
--- 

### react中的细粒度更新

### 当前effect环境
```js
const effectStack = []
```

### state订阅依赖+effect与state建立依赖关系
```js
function subscribe(effect, subs) {
    effect.deps.add(subs)
    subs.add(effect)
}
```

### 重新收集依赖
```js
function cleanup(effect) {
    for (let subs of effect.deps) {
        subs.remove(effect)
    }
    effect.deps.clear()
}
```

### 实现useState
```js
function useState(value) {
    const subs = new Set()
    const getter = () => {
        const curEffect = effectStack[effectStack.length - 1]
        if (curEffect) {
            subscribe(curEffect, subs)
        }
        return value
    }
    const setter = (newValue) => {
        value = newValue
        for (let effect of subs) {
            effect.execute()
        }
    }
    return [getter, settter]
}
```

### 实现useEffect
```js
function useEffect(callback) {
    const execute = () => {
        cleanup(effect)
        effectStack.push(effect)
        try {
            callback()
        } finally {
            effectStack.pop()
        }
    }
    const effect = {
        execute,
        deps: new Set()
    }
    execute()
}
```

### 实现useMemo
```js
```

### react为什么不用细粒度更新