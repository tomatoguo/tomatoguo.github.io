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

在应用程序运行的过程中，Activity通过调用不同的生命周期方法在上图所示的生命周期中不断进行转换。在一个Activity从启动到销毁的过程中，这些生命周期方法被调用的顺序是：onCreate() -> onStart() -> onResume() -> onPause() -> onStop() -> onDestroy()

由上图可知，当Activity被初次创建时，onCreate()方法被调用，在此方法中需要完成创建用户界面、初始化数据等工作，同时，该方法有一个参数Bundle savedInstanceState，这个参数是已保存的Activity实例的状态，我们可以利用该参数完成一些初始化工作，例如屏幕旋转会导致当前Activity的实例被销毁然后一个新的Activity实例被创建，如果我们需要保留旋转前Activity的一些状态或数据并在新的Activity实例中使用就会用到onCreate()方法的Bundle savedInstanceState参数。

#### 栗子 —— 屏幕旋转过程中Activity的生命周期以及savedInstanceState的使用
	
	public class MainActivity extends Activity {

	    ...

	    private int mCounter = 0;

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);

	        Log.d(TAG, "onCreate() called.");

	        if(savedInstanceState != null) {
	            mCounter = savedInstanceState.getInt(EXTRA_COUNTER, -1);
	        }

	        Log.d(TAG, "counter = " + mCounter);
	    }

	    ...

	    @Override
	    public void onSaveInstanceState(Bundle outState) {
	        super.onSaveInstanceState(outState);

	        Log.d(TAG, "onSaveInstanceState() called.");

	        mCounter++;

	        outState.putInt(EXTRA_COUNTER, mCounter);
	    }
	}

![saved-instance-state.png](https://ooo.0o0.ooo/2015/08/09/55c74da2915bb.png "saved-instance-state.png")

在MainActivity中，我们维持了一个全局变量mCounter赋初值0，并在onCreate()方法中打印mCounter的值。在onSavedInstanceState()方法中将mCounter加一并存入savedInstanceState中。从logcat的调试信息中我们可以看到，未旋转前mCounter的值为0，当屏幕进行旋转时，在onPause()和onStop()方法之间，onSavedInstanceState()方法被调用用于保存当前Activity实例的状态，在旋转后新的Activity实例被创建时，savedInstanceState参数不为空，因此旋转后新创建的Activity实例中mCounter被赋予savedInstanceState中储存的相应的值即1。通过该栗子我们了解到当屏幕发生旋转时Activity的生命周期变化以及如何使用onCreate()方法中的Bundle savedInstanceState参数。

#### 栗子结束 —— 屏幕旋转过程中Activity的生命周期以及savedInstanceState的使用

紧随onCreate()方法之后被调用的是onStart()，在调用此方法后Activity的状态由不可见变为可见。但此时处于Started状态的Activity还无法与用户进行交互，需在调用onResume()方法后，用户才可以与Activity进行交互，此时Activity的状态变为Running并显示在屏幕上，同时当前Activity处于Activity栈的栈顶。

在调用以上三个生命周期方法后，Activity已经具有了用户界面、完成了数据的初始化并且可以和用户进行交互了，接下来用户的相应操作会使得不同的生命周期方法被调用。当用户按下回退键时，被调用的生命周期方法及其顺序是：onPause() -> onStop() -> onDestroy()。当用户按下home键或切换至其他应用时，被调用的生命周期方法及其顺序是：onPause() -> onStop()。当onPause()方法被调用后，Activity的状态变为部分可见，值得注意的是，如果在退出Activity前需要进行相关数据的保存以及资源的释放等工作，那么需要在onPause()方法中进行相关实现，这是因为从图中我们可以看出，在onPause()方法被调用之后，如果有其他更高优先级的应用程序需要占用内存而此时内存不足的话，那么当前应用程序所在的进程会被杀死，因而onPause()方法后的onStop()以及onDestroy()方法都不会被执行，这就是我们需要在onPause()方法中进行相关处理的原因。如果在调用onPause()方法后用户立即回到该Activity，那么之后onResume()方法会被调用，Activity变为Running状态。

如果Activity的onStop()方法被调用，那么Activity会变为不可见的状态，但并非不可用，接下来如果用户切换回该Activity，则onRestart()方法被调用，Activity恢复运行。或者，Activity被销毁，Activity销毁的原因有两种：主动调用finish()或系统回收。系统为了节省空间有时会回收处于后台的Activity，在调用了onStop()方法后，处于后台的Activity所在的进程随时都有可能被系统杀死以达到节省空间的目的，当用户的操作使得Activity可能会达到这一状态（随时可能被系统杀死，例如用户按下home键、锁屏等操作）时，onSavedInstanceState()方法会被调用，在这个方法中可以对Activity中的一些数据及状态进行保存。同时，通过logcat的调试信息我们可以看到，按下回退键时onSavedInstanceState()方法并不会调用，此时Activity由用户主动销毁，因此我们可以说，当Activity非用户主动销毁时，onSavedInstanceState()方法会被调用。