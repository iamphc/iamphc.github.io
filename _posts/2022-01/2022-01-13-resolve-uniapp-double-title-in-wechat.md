---
layout: post
title: "uniapp H5端在微信webview内单标题显示保留顶部导航栏的优雅解决方案"
subtitle: "Uniapp H5 terminal displays a single title in wechat WebView, an elegant solution that retains the top navigation bar"
date: 2022-01-13 13:01:00
author: "PhcPro"
catalog: false
header-style: text
tags:
  - uniapp
--- 

## 问题
uniapp H5端在微信webview内双标题

## 目标/呈现结果
单一标题显示，显示微信webview标题，并且顶部导航栏存在，包含返回按钮

## 解决方案
```html
// template.h5.html
<head>
  <script>
    window.onload = function() {
      const isWechat = /MicroMessenger/i.test(window.navigator.userAgent); 
      if (!isWechat) {
        const wrapper = document.querySelector('.user-agent-wrapper');
        wrapper.classList.remove('user-agent-wrapper');
      }
    }
  </script>
</head>
<body>
  <div class="user-agent-wrapper">
    <div id="app"></div>
  </div> 
</body>

```