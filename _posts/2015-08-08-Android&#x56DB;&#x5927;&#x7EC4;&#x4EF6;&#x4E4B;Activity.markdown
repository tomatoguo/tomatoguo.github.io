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

在应用程序运行的过程中，Activity通过调用不同的生命周期方法在上图所示的生命周期中不断进行转换。在一个Activity从启动到销毁的过程中，这些生命周期方法被调用的顺序一般是：<code>onCreate()</code> -> <code>onStart()</code> -> <code>onResume()</code> -> <code>onPause()</code> -> <code>onStop()</code> -> <code>onDestroy()</code>

由上图可知，当Activity被初次创建时，<code>onCreate()</code>方法被调用，在此方法中需要完成创建用户界面、初始化数据等任务，同时，该方法的参数<code>Bundle savedInstanceState</code>为已保存的Activity实例的状态，我们可以利用该参数完成一些初始化工作，例如屏幕旋转会导致当前Activity的实例被销毁然后一个新的Activity实例被创建，如果我们需要保留旋转前Activity的一些状态或数据并在新的Activity实例中使用就会用到<code>onCreate()</code>方法的<code>Bundle savedInstanceState</code>参数，在后面会进行详细说明。

紧随<code>onCreate()</code>方法之后被调用的是<code>onStart()</code>，在调用此方法后Activity的状态由不可见变为可见。但此时处于Started状态的Activity还无法与用户进行交互，需在调用<code>onResume()</code>方法后，用户才可以与Activity进行交互，此时Activity的状态变为Running并显示在屏幕上，同时处于Activity栈的栈顶。