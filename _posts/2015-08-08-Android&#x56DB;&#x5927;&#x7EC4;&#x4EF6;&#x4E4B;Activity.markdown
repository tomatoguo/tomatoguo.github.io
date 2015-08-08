---
layout: post
title:  "Android四大组件之Activity"
author: 郭永辉
date:   2015-08-08 20:58:50
categories: android
---


### 一、Activity简介

Activity是Android应用程序中最常见也是最重要的组件之一，Activity提供了用户与应用程序进行交互的界面。每个Activity都有自己的窗口，在这个窗口中Activity绘制自己的用户界面，通常Activity的窗口是全屏的，但也有一些小于屏幕并且浮动于其他窗口之上。


### 二、Activity生命周期

![activity_lifecycle.png](https://ooo.0o0.ooo/2015/08/08/55c608bdb1a12.png "activity_lifecycle.png")

当用户进入或离开一个Android应用程序时，Activity通过调用不同的生命周期方法在上图所示的生命周期中不断进行转换。在一个Activity从启动到销毁的过程中，这些生命周期方法被调用的顺序一般是：<code>onCreate()</code> -> <code>onStart()</code> -> <code>onResume()</code> -> <code>onPause()</code> -> <code>onStop()</code> -> <code>onDestroy()</code>

下面对这些生命周期方法进行详细介绍：

<code>onCreate()</code>

当Activity被初次创建时调用该生命周期方法，在<code>onCreate()</code>方法中，我们应该完成一些初始化工作，例如创建用户界面、初始化数据等，同时，该方法向我们提供了一个存储有Activity的一些状态及数据的Bundle（如果该Bundle存在的话），我们可以利用Bundle进行一些初始化工作。

<code>onStart()</code>

在<code>onCreate()</code>或<code>onStart()</code>方法之后被调用，调用时机为当activity变得对用户可见时。

<code>onResume()</code>

当Activity可以和用户进行交互时调用该方法，调用此方法后，当前Activity会处于Activity栈的栈顶。

<code>onPause()</code>

当系统要开始恢复其他Activity时调用此方法，该方法通常用来将未保存的变更保存到持久化数据中、停止动画和其他占用cpu的事务。此方法的实现需要占用尽量少的时间，因为直到<code>onPause()</code>返回后另一个Activity才会被系统恢复。