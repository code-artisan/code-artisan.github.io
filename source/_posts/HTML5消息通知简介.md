---
title: 'HTML5消息通知简介'
date: 2016-03-28 10:36:13
tags: [HTML5,消息通知]
published: true
hideInList: false
feature: 
---

在HTML5中，加入不少新的API，比如地理定位、消息通知、WebSQL等等。。。今天要讲的是`Notification（消息通知）`，废话不多说，直接上手吧。

<!-- more -->

## 使用
  如果你所开发的项目中需要使用这些新的API，那么肯定就要注意浏览器的兼容性，况且这还只是个草案，API的变更也会比较大。浏览器的兼容性处理我就不说了，大家应该都知道怎么处理，我记得在两年前，我用它的时候还是这么用的：
```js
window.webkitNotifications.checkPermission();
// 该方法返回0, 1, 2三个值，0代表PERMISSION_ALLOWED,即’允许’；1代表PERMISSION_NOT_ALLOWED，即不允许；2代表PERMISSION_DENIED，即拒绝

window.webkitNotifications.requestPermission();
// 调用该方法将会在浏览器的信息栏弹出一个是否允许桌面通知的提醒，该方法只能由用户主动事件触发，如click 或 mouse over，也就是说你不能在document.ready里面直接调用该方法。

window.webkitNotifications.createNotification('icon-url','title', 'body');
```
而现在，完全不同了，现在你需要这么使用：

```js
// 首先需要用户授权，只有用户授予权限之后才能顺利的通知用户，否则无法通知
window.Notification.requestPermission(function (state) {
  // state 分为3个状态：granted为允许，default默认不允许，denied为拒绝
  if (state === 'granted') {
    var notification = new Notification('Here is header', {
      body: 'I am message body',
      icon: 'https://www.google.co.jp/images/branding/product/ico/googleg_lodp.ico'
    });
  }
});
```

执行完上面的代码（授予权限），你应该就可以看到下面这个画面（如果你是用的苹果电脑并且当前处于全屏模式下是看不到，需要切换到桌面才行）：
![image](https://cloud.githubusercontent.com/assets/5195218/14070229/77c25fb8-f4d6-11e5-80bb-1ce846697da1.png)

## 属性
- notification.title:   只读，第一个参数
- notification.dir:     只读，执行上下文
- notification.lang:    只读，语言设置
- notification.body:    只读，消息主体
- notification.tag:     只读
- notification.icon:    只读，左边的图标
- notification.data:    只读，看起来像是附加数据传输
- notification.silent:  只读，估计是静音推送吧，现在还不支持添加声音
- notification.requireInteraction: 只读

## 事件
作为HTML5的API，它的功能远远不止上面我所介绍的那么点，它还有事件：`Notification.onclick` 响应用户点击消息的行为，你可以利用它来进行一些有意思的操作，比如点击后进行一个动画，或者是跳转到某个特性的页面。`Notification.onerror` 消息通知出错时触发。

## 静态方法
其实在上面的使用介绍中，我们已经使用过了它的静态方法：`Notification.requestPermission`，这个静态方法用于请求权限，上面也有说明过了。`Notification.close`方法是用于清除现有的消息，消息通知虽然牛逼，但是同时通知太多了也不好，用户会感觉到烦。

![image](https://cloud.githubusercontent.com/assets/5195218/14070245/b3958e48-f4d6-11e5-8efa-c1c568f6c976.png)

所以，我们可以用它来实现单一消息通知，在推送消息前把上一个消息或者是上一堆消息关闭掉，然后再进行新的消息推送。这样一来，用户才能够专心的去阅读消息。。。

具体文档请参阅W3C文档：[https://developer.mozilla.org/en-US/docs/Web/API/notification](https://developer.mozilla.org/en-US/docs/Web/API/notification)

> 如需纠正此文，请一一列举并发送邮件至 `codes.artisan@gmail.com`，谢谢~~

## 参考文献
- [https://developer.mozilla.org/en-US/docs/Web/API/notification](https://developer.mozilla.org/en-US/docs/Web/API/notification)
