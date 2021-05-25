---
title: 'JS 中 slice, substr, substring 的区别'
date: 2019-06-26 14:02:04
tags: [javascript]
published: true
hideInList: false
feature: 
---

首先，他们都接收两个参数，slice和substring接收的是起始位置和结束位置(不包括结束位置)，而substr接收的则是起始位置和所要返回的字符串长度。直接看下面例子：

<!-- more -->

```js
var test = 'hello world';

alert(test.slice(4,7));             //o w
alert(test.substring(4,7));         //o w
alert(test.substr(4,7));            //o world
```

这里有个需要注意的地方就是：substring是以两个参数中较小一个作为起始位置，较大的参数作为结束位置。
如：
```js
alert(test.substring(7,4));          //o w
```

接着，当接收的参数是负数时，slice会将它字符串的长度与对应的负数相加，结果作为参数；substr则仅仅是将第一个参数与字符串长度相加后的结果作为第一个参数；substring则干脆将负参数都直接转换为0。测试代码如下：

```js
var test = 'hello world';

alert(test.slice(-3));         //rld
alert(test.substring(-3));     //hello world
alert(test.substr(-3));        //rld
alert(test.slice(3,-4));       //lo w
alert(test.substring(3,-4));   //hel
alert(test.substr(3,-4));      //空字符串
```

注意：IE对substr接收负值的处理有错，它会返回原始字符串。

> 原文地址：http://www.cnblogs.com/littledu/archive/2011/04/18/2019475.html