#	Android

##	概述

Dalvik 虚拟机

-	ADB，Android Debug Bridge
-	DDMS，Dalvik Debug Monitor Service，Dalvik 调试监控服务
-	AAPT，Android Asset Packaging Tool，Android 资源打包工具
-	DX，将 .class 文件编译为 .dex 文件，从而可以在 Android Dalvik 虚拟机中运行。

###	Android 平台架构

-	应用程序
-	应用程序框架
-	Android 程序库
-	Android 运行库
-	Linux 内核

###	Android 应用程序组件

-	Activity
	-	通常，一个 Activity 就是一个单独的屏幕。每一个 Activity 都被实现为一个独立的类，并且继承于 Activity 这个基类。一个应用一般会包含着许多个屏幕，每个屏幕就是一个 Activity。
	-	通过调用 startActivity() 方法可以从一个屏幕导航到另一个屏幕，打开 Activity 的条件被封装在 Intent 中。
	-	每次切换屏幕时，旧的屏幕都将会暂停并保存在历史堆栈中。
-	Service
	-	长生命周期的、没有用户界面的程序，比如一个正在播放音乐的媒体播放器。
	-	通过 Context.startService() 启动一个 Service，从而在后台保持音乐的播放。
	-	可以通过 Context.bindService() 连接到一个 Service 上。
-	Broadcast Receiver
	-	系统广播
-	Content Provider
	-	实现不同组件之间数据的共享。
-	View
	-	Android 的图形界面可以分为三层，由底至上依次是 Activity、Window、Views。
	-	View 分为 View 和 ViewGroup
	-	View 指基本的控件，如按钮、菜单等。
	-	ViewGroup 指布局空间，用来控制界面中的控件是如何布局摆放的。
-	Intent
	-	是不同组件之间相互导航的纽带。