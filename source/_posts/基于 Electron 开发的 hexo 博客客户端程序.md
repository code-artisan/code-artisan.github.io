---
title: '基于 Electron 开发的 hexo 博客客户端程序'
date: 2017-08-16 11:57:15
tags: [electron,react.js]
published: true
hideInList: false
feature: 
---

因工作需要，接触到了 `Electron` 平台，相信不少同学应该都听说过或者是使用过吧，大名鼎鼎的 `VS Code`、`Atom` 编辑器就是基于 `Electron` 开发的，官网地址：[https://electron.atom.io/docs/](https://electron.atom.io/docs/)。 

<!-- more -->

鄙人一直都在用 `hexo` 这个博客系统，确实挺好用，但是前不久听说 `wordpress` 官方推出了客户端，然鹅 hexo 并没有客户端，于是就写了一个 hexo 客户端应用程序，注意：如果要使用该软件的话，必须要有 `Node.JS` 和 `Git` 环境，并软链到可执行环境中（先用 which 命令查看脚本位置）。

```bash
$ which node
// output
$ which hexo
// output
$ ln -s /path/to/node /usr/local/bin/node  # *inx
$ ln -s /path/to/hexo /usr/local/bin/hexo  # *inx
```

使用技术如下：`React`、`React Router`、`Element UI`、`Webpack`，运行效果图如下：

![软件首页](http://wx1.sinaimg.cn/mw1024/a5bab661ly1fgv02bcqiij218g0xcwh4.jpg)

![编辑文章](http://wx3.sinaimg.cn/mw1024/a5bab661ly1fgv02bhb4fj218g0xcdp9.jpg)

![发布博客](http://wx1.sinaimg.cn/mw1024/a5bab661ly1filfpchne9j218g0xc41i.jpg)

![关于](http://wx1.sinaimg.cn/mw1024/a5bab661ly1filfn679unj20fq09qwet.jpg)

![设置](http://wx4.sinaimg.cn/mw1024/a5bab661ly1filfn69rwyj20m80b40tl.jpg)

Github 地址：https://github.com/code-artisan/hexo-client 

注意：该软件功能还在完善中，如果你发现了 bug 或者有好的建议请提 issue，如果不喜欢请忽略，禁止对作者和支持者进行人身攻击。谢谢！