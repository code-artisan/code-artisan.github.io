---
title: 'Firefox 下 flex 元素 overflow 失效的原因和修复'
date: 2017-01-03 15:37:05
tags: [前端,CSS3]
published: true
hideInList: false
feature: 
---

使用 flex 布局时，有些需要滚动显示全部内容的元素在 Firefox 下却不能滚动，其原因在于 overflow 失效。

<!-- more -->

原因：
在 Firefox 下，flex 元素默认将其最小尺寸设置为其子元素的尺寸，这意味着父元素永远能显示全部子元素，即使这样导致整个页面超过了屏幕。这还 overflow 个鬼嘛！。因此我们需要覆盖默认的最小尺寸。

解决方法：
给该元素添加 `min-height: 0` 或者 `min-width: 0`，取决于你的滚动方向，如果无效，尝试给其父元素添加该 style，以此类推。

Firefox 的这一设定可以参见MDN：
[https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes)

为了确保弹性项目有合理的默认最小尺寸，使用 `min-width: auto` 与 `min-height: auto`。对于弹性项目，属性值 auto 使项目的最小宽 / 高在计算中不会小于其内容的实际宽 / 高，这样可以保证项目渲染得足够大以容纳其内容。更多细节请参考 min-width 与 min-height 。

然而这一条内容已经在英文文档被删除，中文也加了删除线，估计是准备改了，不过目前Firefox并未更新掉这一问题。
Chrome 等其它主流浏览器貌似没有类似问题，因为它们并未实现这个规则。

Flex 布局相当好使，但是这些浏览器厂商老毛病又犯了，都想自己搞一套规范，好烦。

css3 - firefox overflow-y not working with nested flexbox - Stack Overflow — I have designed a 100% width 100% height layout with css3 flexbox, which works both on IE11 (and probably on IE10 if emulation of IE11 is correct). —— almon123・ stackoverflow.com

使用 CSS 弹性框 - CSS — CSS3 弹性框( Flexible Box 或 Flexbox )，是一种当页面需要适应不同的屏幕大小以及设备类型时确保元素拥有恰当排布行为的 布局方式 。

> 转自：http://www.jianshu.com/p/de06328eaa44