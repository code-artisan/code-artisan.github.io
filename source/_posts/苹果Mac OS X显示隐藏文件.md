---
title: '苹果Mac OS X显示隐藏文件'
date: 2017-08-11 15:25:26
tags: [mac]
published: true
hideInList: false
feature: 
---

苹果Mac OS X显示隐藏文件的方法有很多种，这里介绍一种最简单的：

<!-- more -->

	OS X（10.6~10.8）系统使用如下命令：
```bash
defaults write com.apple.Finder AppleShowAllFiles Yes && killall Finder //显示隐藏文件
defaults write com.apple.Finder AppleShowAllFiles No && killall Finder //不显示隐藏文件
	```

	OS X 10.9 Mavericks 之后的版本使用如下命令：
```bash
defaults write com.apple.finder AppleShowAllFiles Yes && killall Finder //显示隐藏文件
defaults write com.apple.finder AppleShowAllFiles No && killall Finder //不显示隐藏文件
```

注意：OS X 10.9 Mavericks 需要将 `Finder` 修改成 `finder` 才行，第一个字母换成小写的 `f`