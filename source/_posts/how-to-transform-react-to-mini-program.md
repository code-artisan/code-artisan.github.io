---
title: '小程序跨端—浅谈小程序代码编译过程'
date: 2021-08-03
tags: [小程序]
published: true
hideInList: false
feature:
---


![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1627891436116-f9365e12-9225-4db1-8d8a-ac100376b5ae.png#clientId=u25c33056-b4b5-4&from=paste&height=370&id=u0f0f9163&margin=%5Bobject%20Object%5D&name=image.png&originHeight=740&originWidth=2430&originalType=binary&ratio=1&size=179047&status=done&style=none&taskId=u9a086512-4921-492f-9054-ede7592c158&width=1215)
## 前言

> 在阅读本篇文章之前，你需要先掌握 Webpack、Babel 的插件开发且熟悉 AST。


最近几年随着前端技术不段发展和更新，催生出很多优秀的前端框架，如：Vue、React。在享受到了 Vue、React 带来的红利之后，现在的大部分前端工程都会采用 Vue/React 来开发，包括最近几年出现的各种跨端框架也是如此。但享受红利势必是要付出一些代价的，其中最为典型且最重要的就是编译过程。虽然现在已经有很多成熟的方案供我们选择，但是我们不能一直停留在使用层面，所谓『技多不压身』。本篇将会简单剖析从 React 代码到小程序可运行代码的编译过程。


在开始之前，我们先看一下编译前和编译后的目录结构。
​

编译前：![carbon.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1627901557556-6af7ef33-d05f-472a-9b8d-af81488e8266.png#clientId=u25c33056-b4b5-4&from=paste&height=312&id=wMu02&margin=%5Bobject%20Object%5D&name=carbon.png&originHeight=624&originWidth=1772&originalType=binary&ratio=1&size=59053&status=done&style=none&taskId=u29ab8e25-6a51-42c9-80aa-ab6bfc3ad91&width=886)
编译后：
![carbon (1).png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1627901568494-9f49eafa-ef5d-427c-86f6-b02556d3db16.png#clientId=u25c33056-b4b5-4&from=paste&height=438&id=u3ac952e9&margin=%5Bobject%20Object%5D&name=carbon%20%281%29.png&originHeight=876&originWidth=1772&originalType=binary&ratio=1&size=125012&status=done&style=none&taskId=u8942a158-66c3-4fce-82f6-4683fe2e156&width=886)
写过小程序的同学应该能发现编译后的目录结构跟小程序的标准目录结构基本相同。
​

## 现状
假设我们现在已经有了一个相对完善的运行时框架，它的使用方式看起来大概是以下这样。
​

路由声明：
```jsx
// src/index.mp.tsx

import React from 'react';
import { Router, Route, TabRouter } from 'third-party/framework';
import Home from './pages/home/index';
import Video from './pages/video/index';

class Index extends React.Component {
	render() {
  	return (
      <>
        <Router>
          <TabRouter text="Home">
            <Route name='Home' component={Home} />
          </TabRouter>
          <TabRouter text="Video">
            <Route name='Video' component={Video} />
          </TabRouter>
        </Router>
        {this.props.children}
       </>
    );
  }
}

export default Index;
```
业务代码：
```jsx
// src/pages/home/index.jsx

import React from 'react';
import { View, Text } from 'react-native';

export default () => {
  return (
    <View style={{ ... }}>
      <Text style={{ ... }}>Hello terminus.</Text>
    </View>
  );
}
```
以上只是实例代码，在实际业务场景中页面数量更多且复杂度更高，虽然这些不会成为影响本篇内容的因素。
​

## 目标
要想将以上业务代码在小程序端正常运行和渲染，则必须要经过编译和转换。从 JSX 到小程序代码的编译过程大致如下图所示：
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1627891632475-32a8e00b-6071-47c7-ac88-639ba1c514bd.png#clientId=u25c33056-b4b5-4&from=paste&height=845&id=u16d4ff15&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1690&originWidth=1732&originalType=binary&ratio=1&size=460675&status=done&style=none&taskId=u270fb02c-eb45-4de1-a403-b4ec214891b&width=866)
其中最核心、最复杂的部分莫过于各种各样的 Webpack 插件，每个插件都有各自的分工，比如：拷贝静态资源、模板生成、虚拟模块的管理等等。


## 解析路由
解析路由是构建过程的首要任务，只有拿到业务中的路由信息后才能知道有哪些页面需要被注册，就像原生小程序的 app.json 中的 `pages`、`tabBar.list` 字段一样需要先将路由信息注册到小程序中。
​

解析路由的核心逻辑是对源码进行 `AST`（抽象语法树） 操作。我们再回过头来看一下路由的声明方式：
```jsx
import { Router, Route, TabRouter } from 'third-party/framework';
import Home from './pages/home/index';
import Video from './pages/video/index';

class Index extends React.Component {
	render() {
  	return (
      <>
        <Router>
          <TabRouter text="Home">
            <Route name='Home' component={Home} />
          </TabRouter>
          <TabRouter text="Video">
            <Route name='Video' component={Video} />
          </TabRouter>
        </Router>
        {this.props.children}
       </>
    );
  }
}

export default Index;
```
再来看一下小程序的 app.json 配置示例（以微信小程序为例）：
```json
{
  "pages": [
    "pages/home/index",
    "pages/video/index"
  ],
  "tabBar": {
    "list": [
      {
        "pagePath": "pages/home/index",
        "text": "Home"
      },
      {
        "pagePath": "pages/video/index",
        "text": "Video"
      }
    ]
  }
}

```
细心观察似乎发现这两者潜藏着一些关系：

- pages 中的项和 pagePath 的值是 component 对应的 value；
- tabBar 项的 text 字段的值是 TabRouter text 属性的值；

![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1627893305833-055b190e-a66c-479c-bbb4-76af8e90d244.png#clientId=u25c33056-b4b5-4&from=paste&height=513&id=u16510870&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1026&originWidth=2526&originalType=binary&ratio=1&size=792133&status=done&style=none&taskId=u4ac8930c-0da8-4f9a-829b-68397883044&width=1263)
在理清楚逻辑后就可以写代码解析路由了，由于本步骤所涉及到的代码篇幅较长，这里只放伪代码用于参考。
​

我们先写一个用于解析路由的 babel 插件：
```typescript
// plugins/babel-router-plugin.ts

export default {
  pre() {
    // 每次解析前清空之前的结果，避免路由重复。
  },
  post() {
    // 解析完后把结果存储起来，后面需要用到。
  },
  visitor: {
    ImportDeclaration({ node: { specifiers, source } }) {
      // 把引用的路径存起来，用于后续生成文件时使用。
    },
    ReturnStatement(paths) {
      // AST 词法分析，TabRouter 代表 TabBar，Route 代表普通路由、Subpackage 代表分包。
    },
  },
}

```
封装一个解析路由的辅助方法，在其内部调用上面的插件：
```typescript
// utils/routes.ts

import path from 'path';
import * as babel from '@babel/core';
import jetpack from 'fs-jetpack';

/**
 * 解析项目的路由文件，获取路由信息。
 * @param base workspace
 * @param entry 路由文件路径
 * @param plugins babel plugins
 */
export const analysis = (base: string, entry: string, plugins?: any | any[]) => {
  const options = {
    plugins: [].concat(plugins),
    presets: [
      [
        '@babel/preset-typescript',
        {
          ...
        }
      ]
    ],
  };

  return new Promise((resolve, reject) => {
    try {
      const filepath = path.resolve(base, entry);
  		const content = jetpack.read(filepath, 'utf8');

      resolve(babel.transform(content, options).code);
    } catch (error) {
      reject(error);
    }
  });
}

```
有了上面的插件和辅助方法，调用它们就能拿到对应的路由信息了。
```typescript
import { analysis } from './utils/routes';
import BabelRouterPlugin from './plugins/babel-router-plugin';

analysis(base, 'src/index.mp.tsx', BabelRouterPlugin).then(() => {
	// ...
});
```
最终我们拿到的路由信息的结构看起来像是这样：
```json
{
  "pages": [
    "pages/home/index",
    "pages/video/index"
  ],
  "tabBar": {
    "list": [
      {
        "pagePath": "pages/home/index",
        "text": "Home"
      },
      {
        "pagePath": "pages/video/index",
        "text": "Video"
      }
    ]
  }
}
```
看起来跟上文举例用的小程序的 `app.json` 内容一模一样，实际上也是这样。在拿到路由信息后我们需要将这些信息存放起来，后续的过程中会用到。


## 动态生成 & 添加虚拟模块
> 关于虚拟模块的定义：

> Webpack Virtual Modules is a plugin that allows for dynamical generation of in-memory virtual modules for JavaScript builds created with webpack. When virtual module is created all the parent virtual dirs that lead to the module filename are created too. This plugin supports watch mode meaning any write to a virtual module is seen by webpack as if a real file stored on disk has changed.
> 摘自：webpack-virtual-modules readme.md。

​

上文简单翻译过来的意思：`Webpack Virtual Modules` 是一个插件，它允许为用 Webpack 创建的 JavaScript 构建动态生成内存中的`虚拟模块`。创建虚拟模块时，也会创建模块文件名的所有父虚拟目录。它支持监视模式，这意味着任何对虚拟模块的写入都会被 webpack 看到，就像是存储在磁盘上的真实文件被修改了一样。


在开始这一步之前我们需要先了解一下能让一个小程序跑起来的几个要素：
1、必备的 `app.json`、`app.js` 以及页面文件。
2、必须调用 `App` 来注册小程序、必须调用 `Page` 注册页面。
3、页面路由信息必须在 `app.json` 文件中声明。


其中第二个关键要素是本节点相关的，在实际项目中我们是不希望业务侧的同学去写跟小程序直接关联的代码，所以我们必须得经过 AST 操作修改源代码然后借助 `Webpack Virtual Modules` 生成对应的虚拟文件并监听其内容的修改重新打包。


App：
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1627902933935-a17c6fc0-8043-4085-a105-e1b5aa521449.png#clientId=u25c33056-b4b5-4&from=paste&height=557&id=ud8a888bf&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1114&originWidth=3194&originalType=binary&ratio=1&size=1296585&status=done&style=none&taskId=uafb672ef-128e-4313-bc24-345c2620ac2&width=1597)
> App 这块有个细节需要注意：在 AST 操作要把引入页面的 import 语句删除掉，不然会存在业务代码重复打包的问题。Page 则不存在这个问题。

​

Page：
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1627902954158-373bc451-3407-4552-a493-83c9dfe82a34.png#clientId=u25c33056-b4b5-4&from=paste&height=380&id=u82243739&margin=%5Bobject%20Object%5D&name=image.png&originHeight=760&originWidth=2716&originalType=binary&ratio=1&size=636654&status=done&style=none&taskId=u0a4fb27b-c5cb-475a-b826-74f158fc25a&width=1358)


在这一步我们需要用到之前拿到的路由信息，然后把所有的路由组合成一份新的数据遍历对每个路由对应的文件做 AST、Webpack Virtual Modules 操作，最终动态加入到 Webpack 的 Entry 中。
## 生成模板文件
到目前为止，整个编译的过程已经介绍了一大半，最后一步需要生成页面的模板文件，即：`index.wxml`，该文件是**必须**存在，否则小程序会报错且无法渲染该页面的视图。
​

> 由于我们的方案是基于小程序的 template 来实现（动态渲染方案） ，所以需要把所使用到的组件模板都提前声明好，在这里我们把这些模板统一放在 base.wxml 文件中，最后在各个页面中调用即可。



pages/home/index.wxml
```xml
<!-- pages/home/index.wxml -->

<import src="/base.wxml" /> <!-- 公共模板文件 -->

<template is="OCTOPUS_BASE_TEMPLATE" data="{{root: root}}" />
```
base.wxml
```xml
<!-- helper.wxs 文件内置了一些简单的辅助函数 -->
<wxs src='./helper.wxs' module="helper" />

<!-- 根模板，调用该模板可 -->
<template name="OCTOPUS_BASE_TEMPLATE">
  <!-- root 为一棵完整的 VNode tree -->
  <block wx:for="{{root.cn}}" wx:key="id">
    <template is="OCTOPUS_1_CONTAINER" data="{{i: item, ancestor: ''}}" />
  </block>
</template>

<template name="oc_button">
  <button id="{{helper.v(i['id'])}}" bindtap="eh" ...>
    <block wx:for="{{i.cn}}" wx:key="id">
      <template is="{{'OCTOPUS_' + (tid + 1) + '_CONTAINER'}}" data="{{i: item, ancestor: ancestor + ',' + i.typ, tid: tid + 1 }}" />
    </block>
  </button>
</template>

...

<template name="OCTOPUS_1_CONTAINER" data="{{i: i}}">
  <template is="{{helper.tid(i.te, ancestor, i.id)}}" data="{{i: i, ancestor: ancestor + ',' + i.te, tid: 1 }}" />
</template>

<template name="OCTOPUS_2_CONTAINER" data="{{i: i}}">
  <template is="{{helper.tid(i.te, ancestor, i.id)}}" data="{{i: i, ancestor: ancestor + ',' + i.te, tid: 1 }}" />
</template>

...
```
​

生成公共模板的步骤你需要：

1. 列举一份完整的小程序组件和组件属性的数据；
2. 一个适合你的模板引擎；
3. 在 webpack 的插件中调用模板引擎编译出对应的模板内容并生成到指定的位置。

​

有了上面的模板文件后，就具备了渲染页面的能力。在进到页面的时候会调用 `OCTOPUS_BASE_TEMPLATE` 模板，根据页面的 `虚拟 DOM 树` 递归去找对应的组件模板，最终渲染成正确的视图。
​

## 开始启动编译
经过上面的流程拆解，我们可以将以上的流程封装成以下几个插件：

- WebpackEntryProcesserPlugin --> 动态生成 & 添加虚拟模块章节，用于处理源代码。
- WebpackTemplateGeneatorPlugin --> 生成模板文件章节，用于生成页面和公共模板文件。

​

你也可以继续封装其他的插件，如：处理自定义组件的、自动分析已使用的组件用于生成更干净的公共模板文件，或者是其他用途的插件。
​

在封装好需要的插件后，就可以唤起 `webpack` 来编译代码了。
```javascript
import webpack from 'webpack';
import { analysis } from './utils/routes';
import BabelRouterPlugin from './plugins/babel-router-plugin';
import WebpackEntryProcesserPlugin from './plugins/webpack-entry-processer-plugin';
import WebpackTemplateGeneatorPlugin from './plugins/webpack-template-generator-plugin';

const getWebpackConfig = (...args) => {
	return {
  	...,
    plugins: [
	    new WebpackEntryProcesserPlugin(),
    	new WebpackTemplateGeneatorPlugin(),
      ...
    ],
  };
};

const build = async ({ target, debug, watch, base, zip, compress, progress }) => {
	analysis(base, 'src/index.mp.tsx', BabelRouterPlugin).then(() => {
    const compiler = webpack(getWebpackConfig({ ... }));

    if (watch === true) {
      compiler.watch({
        // 注意：监听文件时应忽略虚拟模块，否则会陷入死循环
        ignored: [
          '**/**.virtual.ts',
          ...
        ],
      }, () => {
        // Skip...
      });
    } else {
      // Skip...
    }
  });
});

```


## 小结
通过该篇文章我们大概了解了将 `React 代码` 编译成 `小程序代码` 的过程，也是整个编译过程的核心逻辑：

1. 解析出业务代码中的路由，暂存起来；
2. 根据路由信息在对应的路径做 `AST` 的操作修改代码并生成新的虚拟模块，然后动态加入到 Webpack 的 Entry 中；
3. 生成对应的页面模板文件和公共模板文件。



除此之外还简单介绍了一下动态渲染的方案：因小程序的限制无法操作 DOM，只能通过 `template` 进行循环调用，另外还需要将使用到的组件模板提前声明好，最终在页面的模板文件中调用对应的模板完成页面的渲染。


如果你觉得以上内容有错误或者有改进和疑问的地方，欢迎在评论区留言，我们会持续改进我们的方案，感谢阅读~~
