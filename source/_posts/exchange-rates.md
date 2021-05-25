---
title: '实时采集货币汇率并上传至物联网数据平台'
date: 2016-09-20 20:39:47
tags: [物联网]
published: true
hideInList: false
feature: 
---

如果某天你打算出国了，需要将人民币兑换成美元或者是欧元，你肯定希望汇率是尽可能的低吧，汇率越低，能换到的钱也就越多。也许你运气好，一下就碰到了汇率低的时候，也许你不经意间错过了汇率低的时候，那岂不是太可惜了？那我们能不能写个脚本，实时查询汇率，当低于指定的值时发送邮件或是短信及时通知我？答应是肯定可以的。

<!-- more -->

### # 原料

首先，我们需要准备以下原料：
- [聚合数据平台](https://www.juhe.cn/)帐号一个；
- [Smart Sensor 物联网数据平台](https://www.smart-sensor.link/auth/sign-up)（以下简称数据平台） 帐号一个；
- 有效的邮箱地址一个；
- 装有 Linux 系统的电脑一台。

其实原理很简单，用 shell 写一个脚本（写好之后放到 `crontab` 里面去，定期执行），先获取当前货币的兑换汇率，然后判断是否小于指定的值，当小于指定的值时，触发某个动作，比如发邮件或短信通知你。在这里我是选择用自己的平台来做，方便推广嘛（广告在此），最主要的是方便实时查看汇率和操作方便，当然你也可以自己搞一个发邮件或发短信的类。

我们来看看 shell 脚本中的代码：
```bash
#!/bin/bash

timestamp=$(date '+%Y-%m-%d %H:%M:%S')
millisecond=$(date +%s)

response=$(curl 'http://op.juhe.cn/onebox/exchange/currency?from=CAD&to=CNY&key=:key') # 替换为你自己的KEY

reason=$(echo $response | /usr/local/Cellar/jq/1.5_1/bin/jq '.reason') # 替换为你本机 jq 脚本的绝对地址
result=$(echo $response | /usr/local/Cellar/jq/1.5_1/bin/jq '.result[0].exchange') # 替换为你本机 jq 脚本的绝对地址

if test $reason='successed';
  then
    # 注意替换掉 :key :gateway_id :sensor_id 这 3 个参数
    response=$(curl -d 'key=:key&value='$result 'http://api.smart-sensor.link/v1/gateway/:gateway_id/sensor/:sensor_id/append')

    echo "当前汇率为："$result"%"
else
    echo "额。。。汇率查询失败~~~"
fi

echo "Millisecond: "$millisecond", Timestamp: "$timestamp", Response: "$response >> response.log # 写入到日志
```

需要注意的是，脚本中使用了 `jq` 这个工具，需要额外安装的，如果你不怕麻烦，你可以自己手动处理 JSON 数据。`jq` 官网：[https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)，自己对着文档安装吧，没什么难度，我就不啰嗦了。安装好之后，记得把 jq 脚本的绝对地址替换掉。

然后登录到数据平台进行添加网关、传感器、策略等信息，具体操作步骤如下图所示。

添加网关：
<div class="text-center"><img src="http://o8ciju3cb.bkt.clouddn.com/BF26D631-9CE8-4DFC-8C59-9FAB882E760A.png?e=1493720917&token=wJr8a_CVH-3dirDXIb1Vm09q-fdVPkuA0X1IpEI2:O3IJ7sJ2Xjx_8fIeaKOlh0h2vFc" /></div>

添加传感器：
<div class="text-center"><img src="http://o8ciju3cb.bkt.clouddn.com/A5F4C129-D0B0-4FF8-8E76-D83034738F55.png?e=1493720917&token=wJr8a_CVH-3dirDXIb1Vm09q-fdVPkuA0X1IpEI2:iUQIG20ICe1jNRIhw-31TAoDbM8" /></div>

添加策略：
<div class="text-center"><img src="http://o8ciju3cb.bkt.clouddn.com/DE552A62-4F49-46BF-9C40-A1960F15312C.png?e=1493720917&token=wJr8a_CVH-3dirDXIb1Vm09q-fdVPkuA0X1IpEI2:fwVwPA_iPZnnzRJqX8zsdzko9eY" /></div>

最后把脚本加入到 `crontab` 里面，让其定期执行，我设置的是 1 分钟执行一次：

```bash
*/1 * * * * /absolute/path/to/shell-script

# crontab 格式如下：
* * * * * /path/to/shell-script #分 时 日 月 周 /path/to/shell-script。
```

不会 `crontab` 的，自己谷歌咯。。。如果一切顺利的话，等一分钟后，脚本所在的目录就会多出一个 response.log 文件。当采集到的汇率小于指定的阀值时，数据平台会就发送邮件给你了~~。

效果预览：
<div class="text-center"><img src="http://o8ciju3cb.bkt.clouddn.com/2E25B8A3-610C-441A-879F-1F4FF8431782.png?e=1493720917&token=wJr8a_CVH-3dirDXIb1Vm09q-fdVPkuA0X1IpEI2:T83YM9IN1r2BUqvsvQJ8aOGghP0" />
<img src="http://o8ciju3cb.bkt.clouddn.com/C2B4DF01-7BC0-4E04-8EDC-9D2E1E00F575.png?e=1493720917&token=wJr8a_CVH-3dirDXIb1Vm09q-fdVPkuA0X1IpEI2:vyPR-2uHm45Aszc35lHVSeWMJKo" /></div>

PS：我这里监控的是加币。

如果你在使用数据平台时发现了 Bug 或是有好的建议，欢迎邮件至 report@smart-sensor.link 或是提交 issue 到 [https://github.com/code-artisan/www.smart-sensor.link/issues](https://github.com/code-artisan/www.smart-sensor.link/issues)，谢谢！

结束！！！
