---
title: '小程序跨端—在小程序里面跑 react app'
date: 2021-05-25 14:52:02
tags: [小程序]
published: true
hideInList: false
feature:
---

大家好，我是来自杭州端点科技的前端工程师，我目前在产品研发部门负责跨端框架 Octopus（已适配 iOS、Android、H5、微信、支付宝、钉钉、京东、字节，其他小程序平台逐步适配中）和跨端组件库，该部门包含的产品有：Trantor（Low code 研发平台）、装修、跨端框架。如果你对我们的工作内容有浓厚的兴趣，请投简历至邮箱：yiqiang.jyq@alibaba-inc.com。

## 前言
​
在开始之前我们先来聊一聊关于微信小程序的发展历史：2016 年 1 月 11 日，微信之父张小龙进行了公开亮相；2016 年 9 月 21 日，微信小程序进入了正式开启内测阶段；2017 年 1 月 9 号微信小程序终于正式发布了第一个版本，我也因此成为了第一批接触和开发微信小程序的开发人员；2017 年 12 月 28 日微信小程序加入小游戏程；2018 年 7 月 13 日微信 App 为小程序添加单独的入口……

随着时间的推移，微信小程序逐步稳定，也已经取得巨大的成功，吸引了越来越多的厂商加入这个游戏，开始纷纷打造自己的小程序平台，且都参考了微信小程序的设计。也正式因为小程序的成功才有了今天的内容，否则当业务需要适配 Web、iOS、Android 的时候就要负出高昂的人力和时间成本，这时候只编写一套代码就能够适配到多端的能力就显得极为需要（write once, run anywhere）。
​
> Tips：以下所有提到的小程序均指微信小程序。

## 为什么要选择 react?

为什么是 React 而不是 Vue，两方面原因吧：1、对 React 熟，学习成本更低；2、之前我们已经有基于 React Naitve 的三端（iOS、Android、Web）统一方案，且有很多相对成熟的配套工具/库。以上两点原因后者占到比重更大，那么选择 `React` 就成了一个必然的结果。
​
## 为什么不用现有的方案？

目前市面上已经有一些不错的、基于 React 思想开发的框架，如：TaroJS、Remax、RaxJS，而他们的方案基本都是基于 Web 的标准，现在我们把视线拉回到上面我说的两点原因，由此可以得出两种结果：1、选一个适合自己的现有方案，然后自己改；2、从零开始自己写一个。显然，我们选的是后者。
​
## 如何将 react 运行到小程序中?

对 React 稍有研究的小伙伴都知道 React 是有一个单独的渲染器，即：[react-reconciler](https://www.npmjs.com/package/react-reconciler)。有了这个线索，似乎事情开始变得简单了，只需要维护一下 `VNode` 的增删改再使用小程序的模板能力（下文着重讲一下，这里先不展开来讲）进行动态渲染貌似就有希望，因此我们可以画出大致的流程图，编译时的内容本篇不做讲解。
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1621836703719-f31d2a81-b380-4fe2-9eea-a7c2d3992301.png#clientId=ufb1045e9-e7ec-4&from=paste&height=220&id=uf9a02f68&margin=%5Bobject%20Object%5D&name=image.png&originHeight=440&originWidth=2970&originalType=binary&size=95809&status=done&style=none&taskId=u7bbaf28c-10aa-452b-9894-930db73523b&width=1485)
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1621849815989-60a8c724-78f1-4b91-a5ce-a2abe627e208.png#clientId=ufb1045e9-e7ec-4&from=paste&height=626&id=ufeeabf14&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1252&originWidth=1602&originalType=binary&size=293737&status=done&style=none&taskId=u5850ab77-94a6-4fcc-a8c2-8773178a2df&width=801)

### react-reconciler 渲染器
​
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1621836772080-98ef0a16-8780-4089-a377-ea3713c358c0.png#clientId=ufb1045e9-e7ec-4&from=paste&height=223&id=uaaea4603&margin=%5Bobject%20Object%5D&name=image.png&originHeight=446&originWidth=2978&originalType=binary&size=119280&status=done&style=none&taskId=uf846d49d-6429-49a0-9379-dd935501053&width=1489)
​

现在我们回到 react-reconciler 这个包，来看看大概是怎么用的，更多细节请翻阅官方文档和源码。

```typescript
const HostConfig = {
  // 添加子节点
  appendChild(parent: VNode, child: VNode) {
    parent.appendChild(child)
  },
  // 添加初始节点
  appendInitialChild: (parent: VNode, child: VNode) => {
    parent.appendChild(child)
  },
  // 在某一节点之前插入节点
  insertBefore(parent: VNode, child: VNode, beforeChild: VNode) {
    parent.insertBefore(child, beforeChild)
  },
  // 删除某一个节点
  removeChild(parent: VNode, child: VNode) {
    parent.removeChild(child)
  },
  // ...
};
```

剩下的问题就是如何把 VNode 转成 JSON 以及通过调用 setData 和调用模板进行页面的拼装完成渲染。这两个问题其实都不难。先来看第一个问题：如何把 VNode 转成 JSON？
​
### VNode 转 JSON

![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1621836812224-bad0501b-6114-4629-afd0-136d434d5ad7.png#clientId=ufb1045e9-e7ec-4&from=paste&height=224&id=u15ffd8f5&margin=%5Bobject%20Object%5D&name=image.png&originHeight=448&originWidth=2976&originalType=binary&size=111970&status=done&style=none&taskId=ueb4631b0-9a0b-414c-933f-fb456a2c238&width=1488)


我们需要为 VNode 写一个 `toJSON` 的方法：
```javascript
/**
 * 提取部分 node 信息,
 * 作为 page data 内的数据
 */
function toRawNode(node: VNode): VNodeJson {
  if (node.type === TYPE_TEXT) {
    return {
      id: node.id, // VNode 的 ID
      te: node.type, // VNode 类型，如：View、Text
      tt: node.text, // 文本内容
    };
  }

  return {
    te: node.type, // VNode 类型，如：View、Text
    cn: [], // VNode 的子节点
    id: node.props.id ?? node.id, // VNode 的 ID
		// ... 其他属性
  }
}

class VNode extends PureNode {
  // ...

	toJSON = (): any => {
    return {
      ...toRawNode(this),
      cn: this.childs.map((item) => item.toJSON()),
    };
  }

  // ...
}

```

通过调用 VNode 的 `toJSON` 方法，我们将可以得到类似以下的数据结构：

```js
{
	root: {
  	cn: [{
    	te: 'view',
      id: 't3',
      cn: [{
      	te: 'text',
        id: 't2',
        cn: [{
        	te: 'plain-text',
          id: 't1',
          tt: 'Hello Terminus.'
        }]
      }]
    }]
  }
}
```

### setData 和调用模板

![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1621836958282-ac5717f0-2e6e-4e78-abf9-db2bd37de8ae.png#clientId=ufb1045e9-e7ec-4&from=paste&height=234&id=u5abc5d86&margin=%5Bobject%20Object%5D&name=image.png&originHeight=468&originWidth=2974&originalType=binary&size=133743&status=done&style=none&taskId=u909f0466-f0e0-42b9-a112-8af4d89bfbe&width=1487)

我们先来了解这两个知识点：`setData` 和 `模板`。setData 是小程序提供的 `API`，也是逻辑层和视图层通信的桥梁。
​
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1621849163763-e9a8da7e-85c2-4357-8791-c1bff0b0110a.png#clientId=ufb1045e9-e7ec-4&from=paste&height=532&id=u6e84d1af&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1064&originWidth=1500&originalType=binary&size=204446&status=done&style=none&taskId=u3f438f08-abc7-4b8d-9b73-08e2c6751f3&width=750)
​

`模板`其主要用途用于定义代码片段，可以在不同的地方调用。声明一个模板：

```xml
<template name="msgItem">
  <view>
    <text> {{index}}: {{msg}} </text>
    <text> Time: {{time}} </text>
  </view>
</template>
```

使用 `is` 属性，声明需要使用的模板，然后将模板所需要的 data 传入，如：

```xml
<template is="msgItem" data="{{...item}}"/>
```

```javascript
Page({
  data: {
    item: {
      index: 0,
      msg: 'this is a template',
      time: '2016-09-15'
    }
  }
})
```

你可以把所有的模板都放到同一个文件中维护，在这里我们统一放到 base.wxml 文件中。

base.wxml

```xml
<wxs src='./helper.wxs' module="helper" />

<template name="OCTOPUS_BASE_TEMPLATE">
  <block wx:for="{{root.cn}}" wx:key="id">
    <template is="OCTOPUS_1_CONTAINER" data="{{i: item, ancestor: ''}}" />
  </block>
</template>

<template name="oc_button">
    <button id="{{helper.v(i['id'])}}" class="{{helper.v(i['class'],i['c1'])}}" bindtap="eh" style="{{helper.v(i['c2'])}}" size="{{helper.v(i['c48'])}}" type="{{helper.v(i['c47'])}}" plain="{{helper.v(i['c49'])}}" disabled="{{helper.v(i['c50'])}}" loading="{{helper.v(i['c51'])}}" form-type="{{helper.v(i['c52'])}}" open-type="{{helper.v(i['c53'])}}" hover-class="{{helper.v(i['c9'])}}" hover-stop-propagation="{{helper.v(i['c10'])}}" hover-start-time="{{helper.v(i['c6'])}}" hover-stay-time="{{helper.v(i['c7'])}}" lang="{{helper.v(i['lang'])}}" session-from="{{helper.v(i['sessionFrom'])}}" send-message-title="{{helper.v(i['sendMessageTitle'])}}" send-message-path="{{helper.v(i['sendMessagePath'])}}" send-message-img="{{helper.v(i['sendMessageImg'])}}" app-parameter="{{helper.v(i['appParameter'])}}" show-message-card="{{helper.v(i['showMessageCard'])}}" bindgetuserinfo="eh" bindcontact="eh" bindgetphonenumber="eh" binderror="eh" bindopensetting="eh" bindlaunchapp="eh" public-id="{{helper.v(i['publicId'])}}">
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
  <template is="{{helper.tid(i.te, ancestor, i.id)}}" data="{{i: i, ancestor: ancestor + ',' + i.te, tid: 2 }}" />
</template>

...
```

再来看调用 setData 和调用模板进行页面的拼装完成渲染这个过程。
前半部分：调用 setData 我们需要手动维护一下页面的实例，然后调用当前页面实例的 setData。

```javascript
...

const start = new Date().getTime();

this.context.setData(payload, () => {
  if (process.env.NODE_ENV !== 'production') {
    console.log('Set data time:', new Date().getTime() - start, 'ms', payload);
  }
});
```
​
后半部分：在页面的视图文件中调用对应的模板进行页面的拼装完成渲染。

```xml
<import src="/base.wxml" />

<template is="OCTOPUS_BASE_TEMPLATE" data="{{root: root}}" />
```

流程示意图：
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1621849033796-43016733-cb65-4369-b131-d748a76313e1.png#clientId=ufb1045e9-e7ec-4&from=paste&height=329&id=uc4dabda1&margin=%5Bobject%20Object%5D&name=image.png&originHeight=658&originWidth=1832&originalType=binary&size=65847&status=done&style=none&taskId=ud9f63c64-c6f8-4553-9f1b-26c19f992da&width=916)

渲染结果：
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1621835203320-0ddf2a17-c83e-4c0c-99ab-1e5b29dada25.png#clientId=ufb1045e9-e7ec-4&from=paste&height=1050&id=ub5e7e5a1&margin=%5Bobject%20Object%5D&name=image.png&originHeight=2100&originWidth=3360&originalType=binary&size=1832292&status=done&style=none&taskId=u17b4a3e2-0858-4161-96ab-6c59676285d&width=1680)

## 示例

我们来看个例子：

```jsx
import React from 'react';
import { View,  Text } from 'react-native';
import './index.module.less';

export default function basicComponents() {
  return (
    <View style={{ height: '100%', alignItems: 'center', justifyContent: 'center' }}>
      <Text style={{ fontSize: 30 }}>Hello Terminus.</Text>
    </View>
  );
}
```

最后的运行效果：

> 模板代码展示

![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1621822768186-69532717-840b-4d10-856c-4e767c673600.png#clientId=u542e46aa-e0ab-4&from=paste&height=988&id=ubdb56808&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1976&originWidth=3300&originalType=binary&size=2146809&status=done&style=none&taskId=u484bea35-bb44-4416-ab66-27b06ee0afa&width=1650)

> 编译后的 JS 和当前页面实例展示

![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1621837805030-c10b9065-5352-41f9-93e5-17dcd27bf5f3.png#clientId=ufb1045e9-e7ec-4&from=paste&height=1050&id=u64451570&margin=%5Bobject%20Object%5D&name=image.png&originHeight=2100&originWidth=3360&originalType=binary&size=2093377&status=done&style=none&taskId=u9f140d8e-99f9-4c96-8860-d2102199cca&width=1680)

## 总结

到这里，我们已经详细地介绍了如何把 React 代码运行到小程序中：React reconciler 渲染器 + 单独维护 VNode，然后调用 VNode 的 toJSON 方法转成 JSON 对象，再调用当前页面实例的 setData 把 VNode tree 传到视图层，视图层根据 VNode tree 递归调用对应的模板进行渲染。
​
## 最后

打造一个好用的跨端框架是非常具有挑战性和创新的事情，其难度堪比古人上青天 <img src="https://intranetproxy.alipay.com/skylark/lark/0/2021/png/116584/1621837638991-a05c8d5f-efde-41c7-b05b-702cb6d98bb3.png#clientId=ufb1045e9-e7ec-4&from=paste&height=16&id=ucdc929ea&name=image.png&originHeight=64&originWidth=64&originalType=binary&size=3692&status=done&style=none&taskId=u365c01dd-cb6a-446c-ab6e-d90f4f0ad40&width=16" width="20" height="20"/> ，但古人有云：路漫漫其修远兮，吾将上下而求索《屈原·离骚》。道路且长，行则将至《荀子·修身》。

非常感谢大家的阅读和支持，本篇的内容就到这里，后面有时间会再出几期关于跨端框架的内容，如果你有任何好的建议或疑问欢迎在评论区留言，我们下期见。
​