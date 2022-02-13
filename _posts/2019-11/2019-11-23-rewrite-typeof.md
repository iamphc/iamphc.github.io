---
layout: post
title: "重写 typeof 方法"
subtitle: "Rewrite tyeof"
date: 2019-11-23 15:01:00
author: "PhcPro"
catalog: false
header-style: text
tags:
  - ES5
--- 

```js
function type(target) {
  const template = {
    "[object Array]": "array",
    "[object Object]": "object",
    "[object Number]": "number object",
    "[object String]": "string object",
    "[object Boolean]": "boolean object",
  }
  if (target === null) {
    return "null";
  }
  if (typeof target === "object") {
    const str = Object.prototype.toString.call(target);
    return template[str];
  }
  return typeof target;
}
```