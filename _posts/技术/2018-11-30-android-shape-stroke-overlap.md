---
layout: post
title: Android Shape Stroke部分重叠问题
category: 技术
tags: Android
keywords: 
description: 
---

## 问题描述

最近想实现一个带背景色的圆环  
期望的整个圆的半径为64x64dp  
stroke宽为10dp  
代码如下  

background.xml
```java
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

```java
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


## 原因

为啥会这样呢 顺着代码 一步步可以找到 `<shape>` 这个标签最终绘制时的实现是 `GradientDrawable.java`里的 `draw()` 方法  
![2](/images/2018-1130-02.png)  
再看下这个rect的区域范围是多少 rect是在 `draw()` 里的 `ensureValidRect()` 里初始化的
```java
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
这就引出了另外一个问题  
```java
canvas.drawOval(mRect, mFillPaint);
canvas.drawOval(mRect, mStrokePaint);

```  
上面这段代码的实际绘制区域是哪里？  
可以看出如果paint的style是FILL 那么是我们期望的效果  
而如果paint的style是STROKE 从结果看似乎它是以矩形框的为中间线 里外分别画了一半  
这也就解释了为啥这里要乘0.5f  好吧这样在颜色是不透明的时候是没问题的  
但是当stroke颜色有透明度的时候 重叠部分的就会显现出来  
```java
if (mStrokePaint != null) {
    inset = mStrokePaint.getStrokeWidth() * 0.5f;
}
mRect.set(bounds.left + inset, bounds.top + inset,
	bounds.right - inset, bounds.bottom - inset);
```
好吧 不是很理解STROKE为什么是这样的规则 正常不应该全部都绘制那个框里面吗  

所以当我们自己实现自定义View用到paint的STROKE模式时 需要注意paint的绘制范围  
很可能一不注意就只显示了一半的stroke width  

## 解决方法  
如果paint的stroke这样的绘制规则不更改  
那似乎GradientDrawable里的draw方法里这样写是不合理的  

当shape有stroke时 一个可能的写法应该是这样  
```java
Rect fillRect = new RectF();
int inset = mStrokePaint.getStrokeWidth;
fillRect.set(bounds.left + inset, bounds.top + inset,
	bounds.right - inset, bounds.bottom - inset);
canvas.drawOval(fillRect, mFillPaint);

Rect strokeRect = new RectF();
int inset = mStrokePaint.getStrokeWidth * 0.5f;
fillRect.set(bounds.left + inset, bounds.top + inset,
	bounds.right - inset, bounds.bottom - inset);
canvas.drawOval(fillRect, mStrokePaint);

```  

但是在改不了源码的情况下 要实现期望的效果 一种改法是这样 使用 `<layer-list>`  
```java
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item
    android:bottom="10dp"
    android:left="10dp"
    android:right="10dp"
    android:top="10dp">
    <shape android:shape="oval">
      <solid android:color="#FF0000FF"/>
    </shape>
  </item>

  <item>
    <shape android:shape="oval">
      <stroke
        android:width="10dp"
        android:color="#29000000"/>
    </shape>
  </item>
</layer-list>
```  
效果  
![5](/images/2018-1130-05.png)


