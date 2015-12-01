---
layout: post
title: Android-回调机制
category: 技术
tags: Android
keywords: 
description: 
---

#Android-回调机制
在练手项目中遇到这样的需求：当USB拔出后需要重新扫描数据，更新数据库，在数据库更新完成后，需要通知前台activity重新查询数据库进行显示  
- 首先想到想到的是 发送一个自定义的广播通知activity  
实现的时候发现 通过动态注册Broadcast时，如：  
``` java
IntentFilter filter = new IntentFilter("SCAN_USB_SUCC");
UsbSuccReceiver receiver = new UsbSuccReceiver();
registerReceiver(receiver, filter);
```
注册的同时要指定处理该broadcast的类，而为了能更新UI，BroadcastReceiver就需要写成activity的内部类的形式，为私有的不能被外部访问到，若声明为public类型，语法没错，但运行时会出错（一个Java源文件中最多只能有一个public类）。而该注册代码位于主Activity中，动态注册很难实现。  
若是在AndroidManifest.xml文件中配置，如：  
``` xml
<receiver android:name=".UsbSuccReceiver">
	<intent-filter>
		<action android:name="SCAN_USB_SUCC"/>
	</intent-filter>
</receiver>
```
`.UsbSuccReceiver` 也必须是public类型才可以  
- 其次又想到了用 `sendMessage()` 发送个消息，用`Handler`来处理，但大多数也都是写到了一个类里面的，不同的类之间就想到可不可以把 `handler`声明为静态变量，但是处理Message消息时又不能调用非静态方法，这个方法也不行。  

唉，小白基础太差，就只能绞尽脑计的想各种逻辑上的东西，太高深的机制现在也不懂都不会知道有那么个方法。还好同组的还有那么个稍微知道点儿东西的---回调机制。

**回调函数**的使用情景：客户程序C调用服务程序S中的某个函数A，然后S又在某个时候反过来调用C中的某个函数B，对于C来说，这个B便叫做回调函数。例如Win32下的窗口过程函数就是一个典型的回调函数。一般说来，C不会自己调用B，C提供B的目的就是让S来调用它，而且是C不得不提供。由于S并不知道C提供的B姓甚名谁，所以S会约定B的接口规范（函数原型），然后由C提前通过S的一个函数R告诉S自己将要使用B函数，这个过程称为回调函数的注册，R称为注册函数。Web Service以及Java的RMI都用到回调机制，可以访问远程服务器程序。（参考：[Android回调机制][1] )  

android中好多地方都用到了回调机制，典型的就是各种控件的监听设置：
``` java
//定义接口
public interface OnClickListenr{
	public void onClick(Button b);
}
//定义Button
public class Button{
	OnClickListener mListener;
	public void click(){
		mListener.onClick(this);
	}
	public void setOnClickListener(OnClickListener listener){
		mListener = listener;
	}
}
//将接口对象OnClickListener赋给Button的接口成员
public class Activity{
	public Activity(){}
	public static void main(String[] args){
		Button mButton = new Button();
		mButton.setOnClickListener(new OnClickListener(){
			@Override
			public void onClick(Button b){
				System.out.println("clicked");
			}
		});
		mButton.click();  //模拟点击事件
	}

}

```
通过这种机制就能很好的解决上述问题，实际实现时，由于回调方法只能监听一个类，而实际情况是每次扫描时就会new一个ScanTask()出来，当时我想的是定义一个静态类型的ScanTask，就可以对其“监听"了；想法是这样，不过那边已经通过两个回调将三个类给联系起来了，我的话可能根本就不会想到还可以这样实现，还是知识不够。看不到那点，  
首先定义了两个接口：  
``` java
public interface UsbStateChangeListener{
	public void onUsbStateChange();
}

public interface ScanDoneListener{
	public void onScanDone();
}  
```  
然后在UsbBroadcastReceiver类中：  
``` java
public class UsbBroadcastReceiver extends BroadcastReceiver{
	...
	UsbStateChangeListener mStateChangeListener;
	
	public void setOnUsbStateChange(UsbStateChangeListener listener){
		mStateChangeListener = listener;
	}
	//USB设备状态改变时
	mStateChangedListener.onUsbStateChanged();
	...
}  
```  
在ScanTask类中  
``` java
public class ScanTask{
	...
	ScanDoneListener mScanDoneListener;

	public void setOnScanDoneListener(ScanDoneListener listener){
		mScanDoneListener = listener;
	}
	//当扫描完成时
	mScanDoneListener.onScanDone();
	
	...
}  
```





[1]: http://www.cnblogs.com/vtianyun/archive/2012/06/19/2555427.html
 
