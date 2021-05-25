---
title: 'OOCSS 和 SMACSS 以及其他 CSS 规范'
date: 2017-08-15 17:53:20
tags: []
published: true
hideInList: false
feature: 
---

真心觉得写出 CSS 并不难，但是要写出可被维护的 CSS 比其他程式语言都还难。所幸已经有许多大师级的人物，提出许多设计模式和思维，藉由站在巨人的肩膀上可以让事情事半功倍。这篇文章就来说说 OOCSS、SMACSS 和撰写 CSS 时应该注意的规范。

（本文的例子用的是 SCSS 语法）

<!-- more -->

### OOCSS
OOCSS 不是什么新技术，只是一种撰写 CSS 的设计模式，或者可以说是一种「道德规范」，大致上我觉得重点只有两个：

- [减少对HTML 结构的依赖](http://blog.chh.tw/posts/oocss-smacss-and-css-guidelines/#html-dependency)
- [增加CSS class 重复性的使用](http://blog.chh.tw/posts/oocss-smacss-and-css-guidelines/#reuse-classes)

减少对HTML 结构的依赖
```html
<nav class="nav--main">
  <ul>
    <li><a>.........</a></li>
    <li><a>.........</a></li>
    <li><a>.........</a></li>
  </ul>
</nav>
```

一般的导航栏写法，结构应该会像上面的HTML范例一样，如果要对那些`<a>` 标签定义样式，CSS的写法可能写成 `.nav--main ul li a {}`，这种写法先不管效能上的问题，可以看出来过度地依赖元素标签的结构，有可能之后HTML结构改变，这个CSS就必须跟着重构，造成维护上多余的成本。

若从这个例子来考量，原则上 `<a>` 都一定会接在 `<li>` 标签的后面，一个`<li>` 只会有一个 `<a>`，通常不会独立存在，那就可以写成 `.nav--main a {}`，会是比较好的写法，甚至是直接给 `<a>` 加上 `class nav--main_item`。后者是 OOCSS 所提倡的用法。

这样的写法，一来效能理论上比较好（我没办法验证），二来层次比较单纯。

### 增加 CSS class 的重复使用
在 OOCSS 的观念中，强调重复使用 class，而应该避免使用 id 作为 CSS 的选择器。这种想法就是像 [OOP](https://en.wikipedia.org/wiki/Object-oriented_programming) 尽量抽离重复的程式码，例如以下这个例子，这是两种按钮的 CSS 样式属性：

```css
.button {
  display: inline-block;
  padding: 6px 12px;
  color: hsla(0, 100%, 100%, 1);
  &.button-default { background: hsla(180, 1%, 28%, 1); }
  &.button-primary { background: hsla(208, 56%, 53%, 1); }
}
```

上面的 CSS 将两种不同样式的 button，抽离出重复的部份，并且定义在同个 class 上。因此，要使用这样的样式，HTML 的写法可能长这个样子：
```html
<a class="button button-default"></a>
<a class="button button-primary"></a>
```

先用 `button` 宣告此为一个按钮的样式，再用 `button-default` 或`button-primary` 作为按钮底色的区别。这么做可以维护成本变得比较低，例如：想要改网站上所有按钮的大小，就只要修改 `.button` 的 `padding` 而已。

### SMACSS
我对SMACSS的理解还不是很深入，或许把 [Scalable and Modular Architecture for CSS](http://smacss.com/) 看完后会有更深一曾的理解。目前对 SMACSS 的概念仅限于它对 CSS 不同的业务逻辑所做的划分方式：

但我认为原本的设计不是很妥当，因此我自己做了一些改良：
- [Base](http://blog.chh.tw/posts/oocss-smacss-and-css-guidelines/#base)
- [Layout](http://blog.chh.tw/posts/oocss-smacss-and-css-guidelines/#layout)
- [Module](http://blog.chh.tw/posts/oocss-smacss-and-css-guidelines/#module)
- [Partial](http://blog.chh.tw/posts/oocss-smacss-and-css-guidelines/#partial)
- [State](http://blog.chh.tw/posts/oocss-smacss-and-css-guidelines/#state)
- [Theme](http://blog.chh.tw/posts/oocss-smacss-and-css-guidelines/#theme)

### Base
Base 就是设定标签元素的预设值，例如浏览器的 reset 可以写在这里，如果用的是 Compass，只要 `@include global-reset` 即可。这里只会对标签元素本身做设定，不会出现任何 class 或 id，但是可以有属性选择器或是伪类：
```css
html {}
input[type=text] {}
a:hover {}
```

### Layout

Layout 是指整个网站的「大架构」的外观，而非 `.button` 这种小元件的 class。网站通常会有一些主要的大区块，可能是 header 或 footer，Layout 就是用来定义这些「大架构」的 CSS。如果有做 Responsive Web Design 或是用 Grid System，也是把规则写在 Layout 这里。

以下这是个范例：
```css
#header { margin: 30px 0; }
#articles-wrapper { ......; }
.sidebar {
  &.sidebar--right { ......; }
  &.sidebar-left { ......; }
}
```
通常只有一个选择器，一个id、或一个class。

### Module
原本的 SMACSS 对 Module 的设计我觉得不是很好，所以我硬是将 Module 拆分出一个 [Partial](http://blog.chh.tw/posts/oocss-smacss-and-css-guidelines/#partial)。

这里的 Module 顾名思义，就是可以在其他地方被重复使用，如果要找更明确的例子，我想就像 Twitter Bootstrap 的 [Components](http://getbootstrap.com/2.3.2/components.html) 一样，或者像前面OOCSS 所举例的 .button 这种会被重复使用的元件模组。

模组不需要用任何的 prefix，因为 Module 就是设计来可以重复应用在不同的 page 上。

### Partial
Partial 跟 Latout 不同，也跟 Module 不同，他比 Layout 的范围小，可能是 header 底下的某个子元素。他不像 Module，他是特定单一领域下特别的设定。

```css
.nav--main {
  a { ......; }
}
```
通常会将 Partial 的名称加在子 class 作为 prefix，例如 `.nav--main` 底下的 `.nav--main_item`。至于为什么要用这么奇怪的命名方式？这等等在 [CSS 规范](http://blog.chh.tw/posts/oocss-smacss-and-css-guidelines/#bem) 部分会说明介绍。

### State
State 负责定义元素不同的状态下，所呈现的样式。但是并非指一个元素的 `:hover` 或 `:active` 下的状态。举例来说，一个导航栏分页，目前所在页面的分页需要加上 `.active` 的属性表示目前位置是在这个分页，HTML会长这样：
```html
<nav class="nav--main">
  <ul>
    <li><a>.........</a></li>
    <li class="active"><a>.........</a></li>
    <li><a>.........</a></li>
  </ul>
</nav>
```
因此可以替.nav--main增加.active这样的子class：
```css
.nav--main {
  // others…;
  .active {
    background: darken($background-color, 16%);
  }
}
```
有时候为了让阅读更贴近语义，会用比较友善的命名方式，以此段的范例来说，`.is-active` 就比 `.active` 来得好读。


### Theme
Theme 是画面上所有「主视觉」的定义，例如 `border-color`、`background-image` 或是 `font-family` 等相关的 Typography 设定。为什么说是「主视觉」？因为有些元件模组仍然是留在 Module 去定义，Theme 就像 Layout 一样负责「大架构」上的视觉样式。

### CSS 规范
这里整理的是我觉得一定要知道的，其他还有很多规范可以转到文末的参考资源连结，那篇文章有介绍更多的细节。

### BEM
BEM 即 Block、Element、Modifier 的缩写，这是一种 class 的命名技巧。如果整个 project 只有自己一个人做，那当然是不太可能出现 class 重复的问题，但是如果同时多个 F2E 一起写同个部分的 CSS，就很容易出现共用 class 的问题，因此有了 BEM 这样的命名技巧。

将 Block 区块作为起始开头，像前面 SMACSS 介绍的 Partial 就可以拿来作为 Block 的 prefix 名称；Element 则是 Block 下的元素；Modifier 则是这个元素的属性。

不同 Block 和 Element 用 `__` 两个底线区隔开来，不同的 Modifier 则用 `--` 两个 dash 区隔。至于 `-` 一个 dash 则表示这个 class 不依赖任何 Block 或 Element，是个独立的存在，例如：`.page-container` 或 `.article-wrapper`。

这里有个范例：
```css
.sidebar {
  .sidebar--left__section {
    .sidebar--left__section--header {}
    .sidebar--left__section--footer {}
  }
}
```

###Javascript Hook
透过 CSS class 来作为 Javascript 选取 DOM 节点的方式，就是 Javascript Hook。用 jQuery 可以常常看到这样的写法：`$('.nav--main a')`，可是当 CSS 跟 Javascript 搅在一起反而造成两边维护上的不便，当改了 CSS 时 Javascript 也要跟着改。

所以用 HTML 的属性去选取 DOM 节点会更好，如果非要用 CSS 的 class 那也可以多写一个 js- 的 prefix，以表示这个节点有被 Javascript 使用，例如：
```html
<li class="nav--main__item  js-nav--main__item"><a>.........</a></li>
<li class="nav--main__item  js-nav--main__item"><a>.........</a></li>
<li class="nav--main__item  js-nav--main__item"><a>.........</a></li>
```
PS. HTML 里两个 class 之间用两个空格，会比一个空格看起来好阅读。

### 合理的选择器

class 无所谓是否语意化的问题；你应该关注它们是否合理，不要刻意强调 class 名称要符合语意，而要注重使用的合理性与未来性。
有时候为了表示更明确，在使用 CSS 的选择器时，要表示某的 class 是搭配某个标签元素使用，会写成这样：
```css
ol.breadcrumb{}
p.intro{}
ul.image-thumbs{}
```
但是上面这个写法效能不是很好，同样的目的但可以减少多余的修饰，试试改用下面这种写法，将标签名称用注解标示起来，维护上有相同的效果，但是浏览器处理的速度会比较快：
```css
/*ol*/.breadcrumb{}
/*p*/.intro{}
/*ul*/.image-thumbs{}
```
### 参考资源
- [撰写可管理、可维护的CSS 高阶技巧](https://github.com/doggy8088/CSS-Guidelines)

> 转自：http://blog.chh.tw/posts/oocss-smacss-and-css-guidelines/