---
title: 'Less 与 Sass 的对比'
date: 2016-03-17 16:33:30
tags: [前端,CSS]
published: true
hideInList: false
feature: 
---

目前，较为出名的三大CSS预处理器：`less`、`sass`、`stylus`，相信大家都不陌生，很多项目都使用预处理器来编译CSS样式，比如 `bootstrap`。在 `bootstrap` `4.x` 版本之前，都是使用 `less` 预处理器，但是在`4.x`版本之后，`bootstrap` 已经把 `less` 换成了 `sass`。。。

<!-- more -->

#### 疑问：
既然有三款预处理器，那么哪一款预处理器更适合于我们呢？他们之间有什么差异呢？以下是我个人整理的特性对比表格，若有错误的地方，请发邮件至`codes.artisan@gmail.com`指出。

#### 特性对比：

| 特性 | less | sass |
|------| ---- | ----- |
| 学习成本 | 低 | 略高 |
| 变量 `*` | 支持，以 `@` 符号开头 | 支持，以 `$` 符号开头 |
| mixin `*` | 支持并以混合模式使用 | 支持（语法限定较为严格） |
| 嵌套规则 `*` | 支持 | 支持 |
| 运算 `*` | 支持 | 支持 |
| 逻辑判断 | 支持但不直观 [docs](http://less.bootcss.com/features/#mixin-guards-feature-guard-comparison-operators) | 支持且比较直观 [docs](http://sass.bootcss.com/docs/sass-reference/#-if-) |
| 循环 | 支持但不直观 [docs](http://less.bootcss.com/features/#loops-feature) | 支持且比较直观 [docs](http://sass.bootcss.com/docs/sass-reference/#each-directive) |
| 默认变量 | 支持但比较麻烦 [docs](http://less.bootcss.com/features/#variables-feature-default-variables) | 支持且简单易用 [docs](http://sass-lang.com/documentation/file.SASS_REFERENCE.html) |
| 继承 | 支持 | 支持 |
| 变量作用域 | 全局 | 全局和局部 [Understanding Variable Scope in Sass](http://webdesign.tutsplus.com/articles/understanding-variable-scope-in-sass--cms-23498) |
| 自定义函数 | 需要借助插件 [less-plugin-functions](https://www.npmjs.com/package/less-plugin-functions) | 支持 [docs](http://sass-lang.com/documentation/Sass/Script/Functions.html#adding_custom_functions)

以上的表格仅为一些较大的差异，具体细节请查阅文档： [less](http://lesscss.org/) [sass](http://sass-lang.com/) 。

结语：通过上面的表格对比，可以得出结论，`less` 学习成本低，但是在使用一些特性的时候，并不是那么直观（相对于编程中常用的语法来说：`if` `else` `each` 等），而 `sass` 学习成本相对于 `less` 来说学习成本更高一些，但是它的语法更直观、更贴近于现有的编程语言。

#### 参考文献：
- [less中文网](http://www.lesscss.cn/)
- [sass中文网](http://www.sasschina.com/guide/)
- [为您详细比较三个 CSS 预处理器（框架）：Sass、LESS 和 Stylus](http://www.oschina.net/question/12_44255)
- [Less vs Sass? 是时候选择Sass了【译】](http://www.html-js.com/article/The-Gothic-Revival--Less-the-vs-Sass-Is-selected-when-the-Sass)
- [CSS 的预处理程序（Sass、LESS、Stylus 等）分别都有哪些优缺点？](https://www.zhihu.com/question/20300388)
