---
layout: post
title: Android-Handler使用简介
category: 技术
tags: Android
keywords: 
description: 
---  
## Handler类
- 主要作用
 + 在新启动线程中发送消息
 + 在主线程中获取、处理消息


### 与Handler一起工作的几个组件

- **Message**: Handler接收和处理的消息对象

-  **Looper**:每个线程只能拥有一个Looper。它的loop方法负责读取MessageQueue中的消息，读到信息之后就把消息交给发送该消息的Handler进行处理。

-  **MessageQueue**: 消息队列，采用先进先出的方式来管理Message。程序初始化Looper对象时会创建一个与之关联的MessageQueue。

Looper的构造器源码：


```java
private Looper(){
	mQueue = new MessageQueue();
	mRun = true;
	mThread = Thread.currentThread();
}
```

为保证当前线程中有Looper对象，可分两种情况：

- 主UI线程中，系统已经初始化了一个Looper对象，程序直接创建Handler即可

- 自己启动的子线程中，必须自己创建一个Looper对象，并启动它。创建Looper对象调用它的prepare()方法即可。然后通过Looper的静态loop()方法来启动它。


### 在自定义线程中使用Hanlder

- 调用Looper的prepare()方法为当前线程创建Looper对象，创建Looper对象时，它的构造器会创建与之配套的MessageQueue 

- 有了Looper之后，创建Handler子类的实例，重写handlerMessage()方法，该方法负责处理来自于其他线程的消息。

- 调用Looper的loop()方法启动Looper。

示例代码：

```java
class CalThread extends Thread{
	public Handler mHandler;
	public void run(){
		Looper.prepare();
		mHandler = new Handler(){
			@Override
			public void handleMessage(Message msg){
				//...do something	
			}
		};
		Looper.loop();
	}
}
```
