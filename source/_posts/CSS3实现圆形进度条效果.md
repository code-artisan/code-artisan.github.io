---
title: 'CSS3实现圆形进度条效果'
date: 2017-08-04 15:54:13
tags: [CSS3]
published: true
hideInList: false
feature: http://wx3.sinaimg.cn/square/a5bab661ly1fi7qa45yhyj206a066mx5.jpg
---
今天早上在群里看到有同学问如何用 CSS3 实现以下效果？

<!-- more -->

![设计效果图](http://wx3.sinaimg.cn/square/a5bab661ly1fi7qa45yhyj206a066mx5.jpg)

一开始我觉得 CSS3 搞不出来这种效果的吧，主要是白色透明那个地方，后来灵机一动想到了渐变至透明色（不考虑兼容性的情况）应该是可以做出来的。如果你对 CSS3 比较熟悉的话，那么你应该对渐变效果 `linear-gradient` 也不陌生，不了解的同学可以去查阅 [文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/linear-gradient)。

首先我们对这个结构进行一下拆分，看下图所示：

![结构拆分](http://wx4.sinaimg.cn/square/a5bab661ly1fi7qcqtglaj20bu0b2aa6.jpg)

有的同学一看到这张图就已经知道该怎么做了，对，就是用 border-radius + 遮罩。先用 border-radius 做一个圆出来，再加上线性渐变色，中间部分用伪类也做一个圆，定位过去盖上，于是就有了外面的空心圆，即下图所示：

![圆](http://wx4.sinaimg.cn/square/a5bab661ly1fi7qj2gg76j206i06ojrh.jpg)

代码如下：
```css
.box1 {
  width: 100px;
  height: 100px;
  position: relative;
  border-radius: 50%;
  background-image: linear-gradient(
    60deg,
    #f79533,
    #f37055,
    #ef4e7b,
    #a166ab,
    #5073b8,
    #1098ad,
    #07b39b,
    #6fba82);
  background-image: linear-gradient(
    60deg,
    #f79533,
    #f37055,
    #ef4e7b,
    #a166ab,
    #5073b8,
    #1098ad,
    #07b39b,
    #6fba82);
}
.box1:after {
  width: 90px;
  height: 90px;
  position: absolute;
  top: 5px;
  left: 5px;
  background-color: #fff;
  content: ' ';
  border-radius: 50%;
}
.box1:before {
  width: 50px;
  height: 50px;
  position: absolute;
  top: 50px;
  left: 50px;
  content: ' ';
  background-image: -webkit-linear-gradient(right top, white 60%, transparent);
  background-image:linear-gradient(right top, white 60%, transparent);
}

```

接下来就是最重要的一步了，用线性渐变画出遮罩层，直接上代码吧：
```
.box1:before {
  width: 50px;
  height: 50px;
  position: absolute;
  top: 50px;
  left: 50px;
  content: ' ';
  background-image: -webkit-linear-gradient(right top, white 60%, transparent);
  background-image:linear-gradient(right top, white 60%, transparent);
}
```

这样一来我们就实现了上面的效果，完整代码如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <style>
    .box1 {
      width: 100px;
      height: 100px;
      position: relative;
      border-radius: 50%;
      background-image: linear-gradient(
        60deg,
        #f79533,
        #f37055,
        #ef4e7b,
        #a166ab,
        #5073b8,
        #1098ad,
        #07b39b,
        #6fba82);
      background-image: linear-gradient(
        60deg,
        #f79533,
        #f37055,
        #ef4e7b,
        #a166ab,
        #5073b8,
        #1098ad,
        #07b39b,
        #6fba82);
    }
    .box1:after {
      width: 90px;
      height: 90px;
      position: absolute;
      top: 5px;
      left: 5px;
      background-color: #fff;
      content: ' ';
      border-radius: 50%;
    }
    .box1:before {
      width: 50px;
      height: 50px;
      position: absolute;
      top: 50px;
      left: 50px;
      content: ' ';
      background-image: -webkit-linear-gradient(right top, white 60%, transparent);
      background-image:linear-gradient(right top, white 60%, transparent);
    }
  </style>
</head>
<body>
  <div class="box1">
  </div>
</body>
</html>
```

最终效果图：
![最终效果图](http://wx2.sinaimg.cn/square/a5bab661ly1fi7qtlp5eij206s06mt8r.jpg)

<script async src="//jsfiddle.net/code_artisan/m7ydzj7y/embed/html,css,result/dark/"></script>