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

由上图可知，当Activity被初次创建时，onCreate()方法被调用，在此方法中需要完成创建用户界面、初始化数据等工作。

紧随onCreate()方法之后被调用的是onStart()，在调用此方法后Activity的状态由不可见变为可见。但此时处于Started状态的Activity还无法与用户进行交互，需在调用onResume()方法后，用户才可以与Activity进行交互，此时Activity的状态变为Running并显示在屏幕上，同时当前Activity处于Activity栈的栈顶。

在调用以上三个生命周期方法后，Activity已经具有了用户界面、完成了数据的初始化并且可以和用户进行交互了，接下来用户的相应操作会使得不同的生命周期方法被调用。当用户按下回退键时，被调用的生命周期方法及其顺序是：onPause() -> onStop() -> onDestroy()。当用户按下home键或切换至其他应用时，被调用的生命周期方法及其顺序是：onPause() -> onStop()。当onPause()方法被调用后，Activity的状态变为部分可见，值得注意的是，如果在退出Activity前需要进行相关数据的保存以及资源的释放等工作，那么需要在onPause()方法中进行相关实现，这是因为从图中我们可以看出，在onPause()方法被调用之后，如果有其他更高优先级的应用程序需要占用内存而此时内存不足的话，那么当前应用程序所在的进程会被杀死，因而onPause()方法后的onStop()以及onDestroy()方法都不会被执行，这就是我们需要在onPause()方法中进行相关处理的原因。如果在调用onPause()方法后用户立即回到该Activity，那么之后onResume()方法会被调用，Activity变为Running状态。

如果Activity的onStop()方法被调用，那么Activity会变为不可见的状态，但并非不可用，接下来如果用户切换回该Activity，则onRestart()方法被调用，Activity恢复运行。或者，Activity被销毁，Activity销毁的原因有两种：主动调用finish()或系统回收。

最后是onDestroy()方法，调用此方法后，Activity被销毁。

以上是Activity的基本生命周期。

### 三、保存Activity运行状态

移动设备的状态的改变（例如屏幕方向发生变化等）有时导致Activity被销毁重建，因此我们经常需要保存Activity运行状态，一般我们会用到onSavedInstanceState()。

#### 栗子 —— 屏幕旋转过程中onSavedInstanceState()方法的使用

保存以及取出Activity运行状态的代码

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

在MainActivity中，有一个全局变量mCounter，其初值为0，并在onCreate()方法中打印mCounter的值。在onSavedInstanceState()方法中将mCounter加一并存入savedInstanceState中。从logcat的调试信息中我们可以看到，未旋转前mCounter的值为0，当屏幕进行旋转时，在onPause()和onStop()方法之间，onSavedInstanceState()方法被调用用于保存当前Activity的运行状态，在旋转后新的Activity实例被创建时，savedInstanceState参数不为空，因此旋转后新创建的Activity实例中mCounter被赋予savedInstanceState中储存的相应的值即1。通过onSavedInstanceState()方法的调用，将旋转前Activity的运行状态保存了下来。

#### 栗子结束 —— 屏幕旋转过程中onSavedInstanceState()方法的使用

#### 栗子 —— onSavedInstanceState()方法调用的时机

Activity运行时点击home键的生命周期

![lifecycle_when_press_home.png](https://ooo.0o0.ooo/2015/08/10/55c80d9966f48.png "lifecycle_when_press_home.png")

Activity运行时旋转屏幕的生命周期

![lifecycle_when_rotate_screen.png](https://ooo.0o0.ooo/2015/08/10/55c80e96e55a9.png "lifecycle_when_rotate_screen.png")

Activity运行时点击back键的生命周期

![lifecycle_when_press_back.png](https://ooo.0o0.ooo/2015/08/10/55c80f0b5ca23.png "lifecycle_when_press_back.png")

从上面的图片中可以看出，在Activity运行时，当我们点击home键或者是进行旋转屏幕等操作时，onSavedInstanceState()方法会被调用，然而当用户点击back键时，onSavedInstanceState()方法并不会被调用。

当Activity的onStop()方法被调用处于后台但未被销毁时时，有时系统为了节省空间，会将处于后台的Activity销毁回收，出现了非用户主动销毁Activity的情况，在这种情况下，系统会在销毁Activity前调用onSavedInstanceState()方法为用户保存Activity运行状态，而当用户按下back键时，即用户主动销毁Activity，此时onSavedInstanceState()不会被调用。因此，可以说当Activity存在非用户主动销毁的可能时，onSavedInstanceState()方法会被调用以保存Activity的运行状态。

#### 栗子结束 —— onSavedInstanceState()方法调用的时机

### 四、Activity的启动

Activity可通过Intent启动，通过Intent启动Activity有两种不用的方式，分别是显式和隐式。

#### 栗子 —— Activity的显式启动与隐式启动

显式启动与隐式启动Activity的代码

	public class MainActivity extends Activity implements View.OnClickListener {

	    ...

	    @Override
	    public void onClick(View v) {
	        int id = v.getId();

	        switch (id) {
	            case R.id.start_another_activity_explicitly:
	                Log.d(TAG, "In MainActivity, start another activity explicitly.");

	                Intent explicitIntent = new Intent(MainActivity.this, AnotherActivity.class);

	                startActivity(explicitIntent);
	                break;
	            case R.id.start_another_activity_implicitly:
	                Log.d(TAG, "In MainActivity, start another activity implicitly.");

	                Intent implicitIntent = new Intent(AnotherActivity.

	                implicitIntent.setAction(AnotherActivity.ACTION_ANOTHER_ACTIVITY);
	                implicitIntent.addCategory(AnotherActivity.CATEGORY_ANOTHER_ACTIVITY);
	                //implicitIntent.addCategory(Intent.CATEGORY_DEFAULT);

	                startActivity(implicitIntent);
	                break;

	            default:
	                break;
	        }
	    }

	    ...

	}

AndroidManifest.xml配置文件

	<activity
	    android:name=".AnotherActivity"
	    android:label="@string/app_name">
	    <intent-filter>
	        <action android:name="com.guoyonghui.activitylifecycle.ACTION_ANOTHER_ACTIVITY"/>

	        <category android:name="com.guoyonghui.activitylifecycle.CATEGORY_ANOTHER_ACTIVITY"/>
	        <category android:name="android.intent.category.DEFAULT"/>
	    </intent-filter>
	</activity>

#### 栗子结束 —— Activity的显式启动与隐式启动

在上面的代码中，我们分别通过显示和隐式两种方式来启动同一个Activity。

在显示启动Activity的过程中，我们只需要实例化一个Intent，并指定实现要启动的Activity的类所属包的上下文以及要启动的Activity的Class对象即可，在本例中MainActivity与AnotherActivity处于同一个包下，因此使用MainActivity.this即可。

而隐式启动Activity的过程就相对复杂一些，以下为一个隐式Intent的主要组成部分：

*	要执行的操作，即ACTION。
*	可选类别，即CATEGORY。
*	要访问的数据位置，通常为URI。
*	设计操作的数据类型，如text/html/或audio/mpeg3等。

我们需要在AndroidManifest.xml文件中为我们需要启动的Activity设置Intent过滤器，在本例中我们只需设置action和category标签，值得说明的是，对于需要隐式启动的Activity我们必须添加DEFAULT类别（唯一的例外是LAUNCHER类别），否则在隐式启动Activity的过程中会产生无法找到相应Activity的错误。

在隐式启动Activity的代码中，我们需要实例化一个Intent，并指定我们在AndroidManifest.xml中设置的Intent过滤器中相应的内容，其中隐式Intent会默认被添加CATEGORY_DEFAULT，因此我们可以手动写出添加CATEGORY_DEFAULT的代码也可以不写，也正是因为CATEGORY_DEFAULT是隐式Intent所默认的类别，因此如果在Intent过滤器中我们没有指定DEFAULT类别就会造成隐式Intent匹配失败。

以下为执行结果，在下面的Logcat调试信息中我们也可以了解从一个Activity中启动另一个Activity的过程中两个Activity的生命周期。

![lifecycle-when-start-another-activity.png](https://ooo.0o0.ooo/2015/08/10/55c8acfe8eb5e.png "lifecycle-when-start-another-activity.png")

[Source Code](https://github.com/tomatoguo/Activity-Lifecycle)