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

在应用程序运行的过程中，Activity通过调用不同的生命周期方法在上图所示的生命周期中不断进行转换。在一个Activity从启动到销毁的过程中，这些生命周期方法被调用的顺序是：<code>onCreate()</code> -> <code>onStart()</code> -> <code>onResume()</code> -> <code>onPause()</code> -> <code>onStop()</code> -> <code>onDestroy()</code>

由上图可知，当Activity被初次创建时，<code>onCreate()</code>方法被调用，在此方法中需要完成创建用户界面、初始化数据等任务，同时，该方法的参数<code>Bundle savedInstanceState</code>为已保存的Activity实例的状态，我们可以利用该参数完成一些初始化工作，例如屏幕旋转会导致当前Activity的实例被销毁然后一个新的Activity实例被创建，如果我们需要保留旋转前Activity的一些状态或数据并在新的Activity实例中使用就会用到<code>onCreate()</code>方法的<code>Bundle savedInstanceState</code>参数，在后面会进行详细说明。

紧随<code>onCreate()</code>方法之后被调用的是<code>onStart()</code>，在调用此方法后Activity的状态由不可见变为可见。但此时处于Started状态的Activity还无法与用户进行交互，需在调用<code>onResume()</code>方法后，用户才可以与Activity进行交互，此时Activity的状态变为Running并显示在屏幕上，同时当前Activity处于Activity栈的栈顶。

在调用以上三个生命周期方法后，Activity已经具有了用户界面、完成了数据的初始化并且可以和用户进行交互了，接下来用户的相应操作会使得不同的生命周期方法被调用。当用户按下回退键时，被调用的生命周期方法及其顺序是：<code>onPause()</code> -> <code>onStop()</code> -> <code>onDestroy()</code>。当用户按下home键或切换至其他应用时，被调用的生命周期方法及其顺序是：<code>onPause()</code> -> <code>onStop()</code>。当<code>onPause()</code>方法被调用后，Activity的状态变为部分可见，值得注意的是，如果在退出Activity前需要进行相关数据的保存以及资源的释放等工作，那么需要在<code>onPause()</code>方法中进行相关实现，这是因为从图中我们可以看出，在<code>onPause()</code>方法被调用之后，如果有其他更高优先级的应用程序需要占用内存而此时内存不足的话，那么当前应用程序所在的进程会被杀死，因而<code>onPause()</code>方法后的<code>onStop()</code>以及<code>onDestroy()</code>方法都不会被执行，这就是我们需要在<code>onPause()</code>方法中进行相关处理的原因。