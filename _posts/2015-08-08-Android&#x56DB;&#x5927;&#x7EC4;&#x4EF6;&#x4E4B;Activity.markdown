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

![activity-basic-lifecycle.png](https://ooo.0o0.ooo/2015/08/08/55c5fd9f11748.png "activity-basic-lifecycle.png")

当用户进入或离开一个Android应用程序时，Activity通过调用不同的生命周期方法在上图所示的生命周期中不断进行转换。在一个Activity从启动到销毁的过程中，这些生命周期方法被调用的顺序一般是：<code>onCreate()</code> -> <code>onStart()</code> -> <code>onResume()</code> -> <code>onPause()</code> -> <code>onStop()</code> -> <code>onDestroy()</code>