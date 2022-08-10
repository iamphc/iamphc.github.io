---
layout: post
title: "ant-design 源码粗略分析"
subtitle: "调色板算法的更迭"
date: 2022-08-10 11:48:00
author: "PhcPro"
catalog: false
header-style: text
tags:
  - ant-design
  - ui库
  - 源码分析
--- 

### 概述
antd到目前为止，共设计了3种调色板算法。

### 第一版
>- 特点：简单粗暴，颜色过渡不自然
>- 主色：5号色
>- 参考文档：[tint/shade](https://css-tricks.com/snippets/sass/tint-shade-functions/)

```sass
@tint ($color, $percentage) {
    @return mix(white, $color, $percentage);
}
@shade ($color, $percentage) {
    @return mix(black, $color, $percentage);
}
```

### 第二版
>- 特点：采用HSL色彩模型+贝塞尔曲线+tinyColor库。过渡效果好，但是算法复杂，可读性差。颜色选取需要设计师更加准确的选择
>- 主色：6号色
>- 浅色：与白色混合
>- 深色：从HSL对应的冷色/暖色进行混合

```js
// 核心代码
var primaryEasing = colorEasing(0.6);
this.colorPalette = function(color, index) {
  var currentEasing = colorEasing(index * 0.1);
  // return light colors after tint
  if (index <= 6) {
    return tinycolor.mix(
      '#ffffff',
      color,
      currentEasing * 100 / primaryEasing
    ).toHexString();
  }
  return tinycolor.mix(
    getShadeColor(color),
    color,
    (1 - (currentEasing - primaryEasing) / (1 - primaryEasing)) * 100
  ).toHexString();
};
```

```js
var warmDark = 0.5;     // warm color darken radio
var warmRotate = -26;  // warm color rotate degree
var coldDark = 0.55;     // cold color darken radio
var coldRotate = 10;   // cold color rotate degree
// 暖色，则旋转 HSL 色轮，使颜色更暖
if (shadeColor.toRgb().r > shadeColor.toRgb().b) {
  return shadeColor.darken(shadeColor.toHsl().l * warmDark * 100).spin(warmRotate).toHexString();
}
// 冷色，则旋转 HSL 色轮，使颜色更冷
return shadeColor.darken(shadeColor.toHsl().l * coldDark * 100).spin(coldRotate).toHexString();
```

### 第三版
>- 特色：采用HSV色彩模型+tinyColor，不使用贝塞尔曲线。代码复杂度降低。过渡平滑。颜色选取需要设计师更加准确的选择
>- 主色：6号色

```js
function main(color, index) {
    var isLight = index <= 6;
    var hsv = tinycolor(color).toHsv();
    var i = isLight ? lightColorCount + 1 - index : index - lightColorCount - 1;
    // i 为index与6的相对距离
    console.log(hsv)
    return tinycolor({
        h: getHue(hsv, i, isLight),
        s: getSaturation(hsv, i, isLight),
        v: getValue(hsv, i, isLight),
    }).toHexString();
};
```