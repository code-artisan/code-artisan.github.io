---
title: '使用 BatteryManager 来查看电池状态'
date: 2016-07-22 12:05:02
tags: [HTML5]
published: true
hideInList: false
feature: 
---

大家都知道HTML5中新增了不少有意思的API，比如消息通知(Notification)、定位(Geolocation)、音乐、视频。。。在去年，我利用一些API写了一个简单的音乐可视化的小玩意，演示地址：[http://blog.codeartisan.name/music-visualizer/](http://blog.codeartisan.name/music-visualizer/)。今天我们来看看`BatteryManager`这个API，看看它能干些什么。

<!-- more -->

根据 `mozilla` 社区提供的文档，我们可以看到关于 `BatteryManager` 的简介：`BatteryManager` 接口提供方法获取系统电量。navigator.getBattery 方法返回一个promise对象，该promise将提供一个`BatteryManager`接口，你可以从 `Battery Status API` 查询到相关信息。关看不练假把式，干技术的，怎么能只看不动手呢？来个例子玩玩吧！


```js
navigator.getBattery().then((response) => {
  console.log(response);

  response.onchargingchange = function (event) {
    console.log(response.charging);
  };

  response.ondischargingchange = function (event) {
    console.log(response.dischargingTime);
  };

  response.ondischargingtimechange = function (event) {
    console.log(response.dischargingTime);
  };

  response.onlevelchange = function () {
    console.log(response.level);
  }
});
```

你可以拷贝以上的代码片段，粘贴到 console 面板中执行，看看都有什么信息返回。我这里的返回信息见下图：

<div class="text-center">![0d5644e7-6f60-463c-ad48-8d6849bc77c4](https://cloud.githubusercontent.com/assets/5195218/17047695/f57140c2-5012-11e6-86f2-a214c1af8c9c.png)
  ![image](https://cloud.githubusercontent.com/assets/5195218/17048238/fd5e60fe-5016-11e6-9ce1-b6cdb15fe43d.png)
  ![image](https://cloud.githubusercontent.com/assets/5195218/17048250/14e49658-5017-11e6-97d1-7f584d03f998.png)
</div>


其中 `charging` 是指当前电池是否正在充电，`chargingTime` 代表距离充满电还需要多少时间（注意：单位是秒），如果为0则充电完毕。`dischargingTime` 代表距离电池耗尽且挂起需要多少秒，如果电池是充电状态，返回的则是Infinity，`level` 代表电量的放大等级，这个值在 0.0 到 1.0 之间，剩下几个为事件回调函数。

### 属性：
- BatteryManager.charging（只读） 一个布尔值，说明当前电池是否正在充电。
- BatteryManager.chargingTime （只读） 一个数字，代表距离充电完毕还需多少秒，如果为0则充电完毕。
- BatteryManager.dischargingTime （只读） 一个数字，代表距离电池耗电至空且挂起需要多少秒。
- BatteryManager.level （只读） 一个数字，代表电量的放大等级，这个值在 0.0 至 1.0 之间。


### 事件：
- BatteryManager.onchargingchange 当充电池充电时且电量发生改变时会被触发。
- BatteryManager.onchargingtimechange 当充电池充电时且所需充电时间发生改变时会被触发。
- BatteryManager.ondischargingtimechange 当电池放电（即非充电状态）且距离电池耗尽时间发生改变时会被触发。
- BatteryManager.onlevelchange 当电量放大等级发生改变时会被触发。

### 兼容：
<div class="text-center">![image](https://cloud.githubusercontent.com/assets/5195218/17048149/43265eee-5016-11e6-9264-a2c983ae70d6.png)
![image](https://cloud.githubusercontent.com/assets/5195218/17048163/5e20d634-5016-11e6-8669-300c8aa12e85.png)
</div>

### 扩展阅读：
1、[https://developer.mozilla.org/zh-CN/docs/Web/API/BatteryManager](https://developer.mozilla.org/zh-CN/docs/Web/API/BatteryManager)
2、[https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/getBattery](https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/getBattery)