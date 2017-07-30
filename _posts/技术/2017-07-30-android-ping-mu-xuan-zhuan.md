---
layout: post
title: Android屏幕旋转
category: 技术
tags: Android
keywords: 
description: 
--- 
  
>我看见自己写下的心情 把自己放在卑微的后头                                                             
---
﻿

[toc]

### 1. 常用的几种属性
- unspecified
    + 默认值 由系统来判断显示方向 这时当用户打开自动旋转的设置后 activity才会在用户旋转设备的时候进行横竖屏切换

- landscape
    + 横屏显示 宽比高要长
- portrait
    + 竖屏显示 高比宽要长
- sensor
    + 由物理传感器来决定横竖屏 只要用户旋转设备 屏幕就会横竖屏切换 而不管自动旋转设置有没有打开
- nosensor
    + 忽略物理传感器 这样就不会随着用户旋转设备而更改了
- behind
    + 和该activity下面的那个activity方向一致（在activity堆栈中的） 这个按我的理解是假如栈顶activity设置为behind的属性 这时如果它下面那个activity是横屏的 那么这个activity也就是横屏的 如果它下面那个activity是竖屏的 那么这个activity也就是竖屏的 可实际实验发现并不是我想的这样 所以目前还不太理解这个属性该怎么用

### 2. AndroidManifest.xml中设置

```
android:screenOrientation="xxx"
```

### 3. 代码中设置

```
activity.setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_SENSOR);
```

### 4. 避免屏幕旋转造成activity重建问题
屏幕方向（orientation）是一个Configuration，其他Configuraion还有fontScale、keyboardHidden、locale等
在AndroidManifest.xml里设置
```
android:configChanges="orientation|screenSize"
```
这样当屏幕方向发生改变时
activity不会重建
相应的Activity的`onConfigurationChanged(Configuration newConfig)`会被调用
可以重写此方法来对不同屏幕方向时做不同处理
```
@Override
public void onConfigurationChanged(Configuration newConfig) {
    Log.v(TAG, "onconfig " + newConfig.toString());
    if (newConfig.orientation == Configuration.ORIENTATION_LANDSCAPE) {
        Toast.makeText(this, "横屏模式", Toast.LENGTH_SHORT).show();
    } else if (newConfig.orientation == Configuration.ORIENTATION_PORTRAIT) {
        Toast.makeText(this, "竖屏模式", Toast.LENGTH_SHORT).show();
    }
    super.onConfigurationChanged(newConfig);
}

```


### 5. 对于透明的activity
透明的activity设置unspecified属性后 没有效果 不会跟随系统改变屏幕状态
而是跟随它底下的那个不透明activity的屏幕状态

比如当前activity栈 从栈顶到栈底 依次为 ActivityB、ActivityA
B为透明activity ，A不透明
这种情况对B设置相应的屏幕旋转属性（除了sensor）是没用的，它是跟随A的属性来变化的
当A为unspecified属性时，B也就是unspecified; 
当A为portrait时，B也就是portrait

有一种属性时例外的，sensor
当B设置为sensor时 这时B会响应屏幕旋转，而不管A的属性如何
因为此时A的属性也会变成和B一样了，即也会变成sensor的

### 6. 实际使用
有这么个需求
在用户打开系统自动转屏开关后，
小窗视频播放切换到全屏播放（只是该视频View充满屏幕，并未开启新的acitvity）时要求支持横竖屏播放
该小窗视频所在activity会有是透明的情况，而且该activity只支持竖屏 没有对横屏做适配

所以需要通过代码动态的设置screenOrientation属性
即当点击大窗播放后 使activity为unspecified属性，当变回小窗时再切换为portrait属性，并且这个过程中activity要避免发生销毁重建
但是对于透明的activity设置unspecified没有用啊，怎么办
倒可以设置为sensor属性来响应屏幕旋转，但是这样就不会受用户是否打开了自动旋转屏幕来控制了，咋办？

解决方法是，获取一下用户是否打开了自动旋转屏幕...
```
int flag = Settings.System.getInt(context.getContentResolver(), Settings.System.ACCELEROMETER_ROTATION, 0);
// 打开了自动旋转屏幕
if (flag == 1) {
    // 这里设置SCREEN_ORIENTATION_UNSPECIFIED无法达到需求
    // 透明的activity设置unspecified属性后 不会跟随系统改变屏幕状态
  // 而是跟随它底下的那个activity的屏幕状态
  //
    activity.setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_SENSOR);
}
```





