---
layout: post
title: Android Shape Stroke部分重叠问题
category: 技术
tags: Android
keywords: 
description: 
---

#### 问题描述

最近想实现一个带背景色的圆环  
期望的整个圆的半径为64x64dp  
stroke宽为10dp  
代码如下  

background.xml
```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
       android:shape="oval"
       android:useLevel="false">
  <solid android:color="#FF0000FF"/>
  <stroke
    android:width="10dp"
    android:color="#29000000"/>
  <size
    android:width="64dp"
    android:height="64dp"/>
</shape>

```  

```  
<ImageView
  android:id="@+id/img"
  android:layout_width="64dp"
  android:layout_height="64dp"
  android:background="@drawable/background"
  android:layout_centerInParent="true"/>
```

实现效果却是这样  
![1](/images/2018-1130-01.png)  
如果你stroke的颜色是不透明的效果是对的  
但是如果stroke颜色是带有透明度的话 就像现在 可以看到圆环和填充圆有一部分重叠了  
这样就不是我们想要的效果了  


#### 原因

为啥会这样呢 顺着代码 一步步可以找到 `<shape>` 这个标签最终绘制时的实现是 `GradientDrawable.java`里的 `draw()` 方法  
![2](/images/2018-1130-02.png)  
再看下这个rect的区域范围是多少 rect是在 `draw()` 里的 `ensureValidRect()` 里初始化的
```
@Override
public void draw(Canvas canvas) {
    if (!ensureValidRect()) {
        // nothing to draw
        return;
    }
    ...
}   
```  
![3](/images/2018-1130-03.png)  
可以知道rect的实际范围是黄色区域那部分  
![4](/images/2018-1130-04.png)


