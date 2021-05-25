---
title: 'JSONP 实现跨域的原理【转载】'
date: 2016-04-12 10:14:08
tags: [JSONP,前端,JavaScript]
published: true
hideInList: false
feature: 
---

JSON(JavaScript Object Notation)和JSONP(JSON with Padding)虽然只有一个字母的差别，但其实他们根本不是一回事儿：JSON是一种数据交换格式，而JSONP是一种依靠开发人员的聪明才智创造出的一种非官方跨域数据交互协议。

<!-- more -->

JSONP是怎么产生的：
1、一个众所周知的问题，Ajax直接请求普通文件存在跨域无权限访问的问题，甭管你是静态页面、动态网页、web服务、WCF，只要是跨域请求，一律不准；

2、不过我们又发现，Web页面上调用js文件时则不受是否跨域的影响（不仅如此，我们还发现凡是拥有”src”这个属性的标签都拥有跨域的能力；

3、现在我们在jsonp.html页面定义一个函数，然后在远程remote.js中传入数据进行调用。
jsonp.html页面代码如下：
```js
  var localHandler = function(data){
      alert('我是本地函数，可以被跨域的 remote.js 文件调用，远程 js 带来的数据是：' + data.result);
  };
```
remote.js文件代码如下：
```js
  localHandler({"result":"我是远程js带来的数据"});
```
运行之后查看结果，页面成功弹出提示窗口，显示本地函数被跨域的远程js调用成功，并且还接收到了远程js带来的数据。很欣喜，跨域远程获取数据的目的基本实现了，但是又一个问题出现了，我怎么让远程js知道它应该调用的本地函数叫什么名字呢？毕竟是jsonp的服务者都要面对很多服务对象，而这些服务对象各自的本地函数都不相同啊？我们接着往下看。

4、聪明的开发者很容易想到，只要服务端提供的js脚本是动态生成的就行了呗，这样调用者可以传一个参数过去告诉服务端“我想要一段调用XXX函数的js代码，请你返回给我”，于是服务器就可以按照客户端的需求来生成js脚本并响应了。
看jsonp.html页面的代码：
```js
  // 得到航班信息查询结果后的回调函数
  var flightHandler = function(data){
      alert('你查询的航班结果是：票价 ' + data.price + ' 元，' + '余票 ' + data.tickets + ' 张。');
  };

  // 提供jsonp服务的url地址（不管是什么类型的地址，最终生成的返回值都是一段javascript代码）
  var url = "http://flightQuery.com/jsonp/flightResult.aspx?code=CA1998&callback=flightHandler";    // 创建script标签，设置其属性
  var script = document.createElement('script');
  script.setAttribute('src', url);    // 把script标签加入head，此时调用开始    document.getElementsByTagName('head')[0].appendChild(script);
```
这次的代码变化比较大，不再直接把远程js文件写死，而是编码实现动态查询，而这也正是jsonp客户端实现的核心部分，本例中的重点也就在于如何完成jsonp调用的全过程。
我们看到调用的url中传递了一个code参数，告诉服务器我要查的是CA1998次航班的信息，而callback参数则告诉服务器，我的本地回调函数叫做flightHandler，所以请把查询结果传入这个函数中进行调用。
OK，服务器很聪明，这个叫做flightResult.aspx的页面生成了一段这样的代码提供给jsonp.html（服务端的实现这里就不演示了，与你选用的语言无关，说到底就是拼接字符串）：
```js
  flightHandler({
      "code": "CA1998",
      "price": 1780,
      "tickets": 5
  });
````
我们看到，传递给flightHandler函数的是一个json，它描述了航班的基本信息。运行一下页面，成功弹出提示窗口，jsonp的执行全过程顺利完成！

5、到这里为止的话，相信你已经能够理解jsonp的客户端实现原理了吧？剩下的就是如何把代码封装一下，以便于与用户界面交互，从而实现多次和重复调用。
什么？你用的是jQuery，想知道jQuery如何实现jsonp调用？好吧，那我就好人做到底，再给你一段jQuery使用jsonp的代码（我们依然沿用上面那个航班信息查询的例子，假定返回jsonp结果不变）：
```js
  jQuery(document).ready(function(){
    $.ajax({
      type: "get",
      async: false,
      url: "http://flightQuery.com/jsonp/flightResult.aspx?code=CA1998",
      dataType: "jsonp",
      jsonp: "callback", //传递给请求处理程序或页面的，用以获得jsonp回调函数名的参数名(一般默认为:callback)                      jsonpCallback:"flightHandler",//自定义的jsonp回调函数名称，默认为jQuery自动生成的随机函数名，也可以写"?"，jQuery会自动为你处理数据             success: function(json){
         alert('您查询到航班信息：票价： ' + json.price + ' 元，余票： ' + json.tickets + ' 张。');
      },
      error: function(){
         alert('fail');
      }
    });
  });
```
是不是有点奇怪？为什么我这次没有写flightHandler这个函数呢？而且竟然也运行成功了！哈哈，这就是jQuery的功劳了，jquery在处理jsonp类型的ajax时（还是忍不住吐槽，虽然jquery也把jsonp归入了ajax，但其实它们真的不是一回事儿），自动帮你生成回调函数并把数据取出来供success属性方法来调用，是不是很爽呀？

这里针对 ajax 与 jsonp 的异同再做一些补充说明：
1、ajax 和 jsonp 这两种技术在调用方式上“看起来”很像，目的也一样，都是请求一个 url，然后把服务器返回的数据进行处理，因此 jquery 和 ext 等框架都把 jsonp 作为 ajax 的一种形式进行了封装；
2、但 ajax 和 jsonp 其实本质上是不同的东西。ajax 的核心是通过 XmlHttpRequest 获取非本页内容，而jsonp的核心则是动态添加

本文转自聚合数据
