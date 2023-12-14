---
title: '在 MacBook 上使用 Safari 调试移动端页面'
date: 2017-01-04 10:18:12
tags: []
published: true
hideInList: false
feature: 
---

移动终端和现代浏览器的用户数量越来越多，某些不喜欢守规矩的浏览器总喜欢搞一些奇奇怪怪的东西出来。但是！我们是前端工程师，趁这个机会就来说说如何用 `Safari` 来调试移动端页面。估计会有人会问，为啥不用 `weinre` 调试？兄弟，别拆台啊，我承认 `weinre` 确实好用。废话不多说，直接上干货吧！

<!-- more -->

## 设置
- 【移动终端】在你的移动设备中开启功能： 设置 → Safari → 高级 → Web 检查器 → 开；
![IOS 设备开启 Web 检查器](http://ww4.sinaimg.cn/mw690/a5bab661jw1fbeepnmqdmj20hs0vk76a.jpg)

- 【OS X】Safari → 偏好设置 → 高级 → 在菜单栏中显示 "开发" 菜单；

![在菜单栏中显示 “开发” 菜单](http://ww1.sinaimg.cn/mw1024/a5bab661jw1fbeefurf25j210w0swdmd.jpg)

## 启动
打开 Safari，将 USB 数据线接入 IOS 移动终端，并在该设备上开启 Safari 并打开你想要调试的页面；
![连接到移动终端](http://ww1.sinaimg.cn/mw690/a5bab661jw1fbees1iu49j20vi10gn4f.jpg)

点击 OS X 的 Safari 菜单中的 "开发"，可以看到有 IOS 设备的名称，选择你想要调试的页面就可以顺利的调试了！

DOM 结构及对应样式：
![DOM 结构及对应样式](http://ww3.sinaimg.cn/mw690/a5bab661jw1fbefjnuaauj21jk1047lt.jpg)

移动终端截图：
![移动终端截图](http://ww2.sinaimg.cn/mw690/a5bab661jw1fbeff5rubaj20hs0vkq8u.jpg)

时间线：
![时间线](http://ww4.sinaimg.cn/mw690/a5bab661jw1fbefjn7g6aj21jk104dnu.jpg)

网络请求：
![网络请求](http://ww3.sinaimg.cn/mw690/a5bab661jw1fbefjmeeasj21jk1044h3.jpg)

在这个调试工具中，你可以做各种调试工作，比如：修改 HTML、CSS、JavaScript，查看 DOM 元素、网络请求等操作！
