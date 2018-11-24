---
layout: post
title: macOS升级 10.14Mojave后Android Studio ADB Connction Error问题
category: 技术
tags: Android
keywords: 
description: 
---


想使用深色模式 最近把两台Mac都升级到10.14Mojave后 在打开Android Studio后都出现了
ADB Connection Error 
Unable to establish a connection to adb的问题
![1](/images/2018-1125-01.png)

首先去Google了下 发现没有搜到类似的问题 莫非是这个问题过于简单都自行解决了？

搜索无果就看它的出错提示吧

点击Help | Show Log in Explorer看下idea.log这个文件
![2](/images/2018-1125-02.png)

localhost为啥会是192.168.0.104呢
然后打开hosts发现里面是空的 加上一行

```
127.0.0.1  localhost
```
然后就可以了




