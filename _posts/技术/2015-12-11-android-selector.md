---
layout: post
title: Android-Selector背景选择器
category: 技术
tags: Android
keywords: 
description: 
---  
###Android-Selector背景选择器  
- ListView  实现了焦点item的背景变化，边框

``` xml
<ListView
	android:id="@+id/id_listview_species"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:listSelector="@drawable/listview_change"
</ListView>
```
drawable下的listview_change.xml文件  

``` xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
	<item android:state_focused="true">
        <shape>
            <solid android:color="#B0E0E6" />
            <stroke 
	            android:width="6dp" 
	            android:color="#7CFC00" />
	        <corners 
				android:bottomLeftRadius="5dp"
				android:bottomRightRadius="5dp" 
				android:topLeftRadius="5dp" 
				android:topRightRadius="5dp" />
        </shape>
    </item>

    <item>
        <shape>
            <solid 
	            android:color="#B0E0E6" />
            <corners 
	            android:bottomLeftRadius="5dp" 
	            android:bottomRightRadius="5dp" 
	            android:topLeftRadius="5dp" 
	            android:topRightRadius="5dp" />
        </shape>
    </item>
</selector>
```

**效果**  
默认状态  
![](http://7xkxii.com1.z0.glb.clouddn.com/20151209list_default.jpg?imageMogr2/thumbnail/!50p)

获得焦点时的  
![](http://7xkxii.com1.z0.glb.clouddn.com/20151209list_fouc.jpg?imageMogr2/thumbnail/!50p)



- `<selector>`下的`<item>`一般有这么几个状态：  
 + android:state_selected   选中  
 + android:state_foucuse    获得焦点  
 + android:state_pressed    点击  



`<shape>`有这些个子标签：  

- `<solid>` 填充 设置填充的颜色  

- `<corners>` 圆角 设置圆角半径  

- `<stroke>` 描边 设置边框宽度 颜色  

- `<size>`大小  

- `<padding>`间隔  

- `<gradient>`渐变



参考:[android shape的使用] [1]

[1]: http://www.cnblogs.com/cyanfei/archive/2012/07/27/2612023.html
