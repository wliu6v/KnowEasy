#	Jni

## reference

[JAVA基础之理解JNI原理](http://www.cnblogs.com/mandroid/archive/2011/06/15/2081093.html)

[android.mk 详解](http://www.cnblogs.com/vistal/archive/2013/05/13/3076103.html)

[JNI中java类型与C/C++类型对应关系](http://www.cnblogs.com/nicholas_f/archive/2010/11/30/1892124.html)

[可能会碰到的问题](http://www.cnblogs.com/yejiurui/archive/2013/03/29/2989297.html)

[使用动态加载](http://lingavin.com/blog/2013/03/05/jni-technology)

##	基本步骤

-	JNI 接口设计
-	使用 C \ C++ 实现
-	生成 .so 文件
-	调用

论如何编译 reader-jni
====================

'memcpy' error
--------------------------------------------------

使用 ndk-build 命令编译 reader 库的过程中，可能会碰到如下错误：

	error: 'memcpy' was not declared in this scope

只要为该 cpp 文件添加 `#include <string.h>` 即可解决该问题。

PS. 为了避免每个 cpp 都要添加一遍这句话，我在 "kt.h" 文件中添加了此条语句。


在C语言中调用 Android 的 Log
--------------------------------------------------

	#include <android/log.h>
	
	#define LOG_TAG		"KTReaderJni"

	#ifdef	NEED_LOG
	#define	LOGI(...)	__android_log_print(ANDROID_LOG_INFO, LOG_TAG, __VA_ARGS__)
	#else
	#define	LOGI(...)
	#endif

如果需要其他类型的LOG，则修改 `ANDROID_LOG_INFO` 。

C语言中三个点 `...` 表示可变参数，调用时使用 `__VA_ARGS__` 。


在 Android.mk 中添加宏定义
--------------------------------------------------

若要添加宏　HHH , 则要设定 CFLAGS 为 -DHHH， 如下：

	LOCAL_CFLAGS := -DHHH

宏名称前面加 -D 才可以，不能省略 D 。



如何令程序能够访问 TrustFly
--------------------------------------------------

__必要条件：手机能够获取Root权限__

1.	下载 Android 手机上能够使用的命令行程序。推荐 Better Terminal Emulator Pro (BTEP, 超级终端)。
2.	通过 OTG 将 TrustFly 连接至手机，运行 BTEP ，先运行 `cd /dev` ，再运行 `ll`  , 在列表中找到 ttyACM0 这一项（根据设备型号不同，也有可能是 ttyUSB0），查看其权限是否为 crw-rw-rw。若不是则进行下一步
3.	通过 `su` 命令使 BTEP 获取 Root 权限（需要手机允许进行该操作），然后执行 `chmod 666 /dev/ttyACM0` ，之后再次检查ttyACM0 的权限，若是 `crw-rw-rw` 即可。
4.	可以将 `chmod 666 /dev/ttyACM0` 这条语句写在代码中并通过特定方法执行，从而保证每次运行程序时都能获取 Root 权限。


NDK 中 jstring \ char* \ wchar_t* 互相转换
---------------------------------------------------
[NDK 中 jstring \ char* \ wchar_t* 互相转换](http://blog.chinaunix.net/uid-24103300-id-188799.html)

[方法二](http://stackoverflow.com/questions/4181934/jni-converting-jstring-to-char)


在 Android.mk 文件中批量加入 cpp 文件
---------------------------------------------------
[编写Android.mk中的LOCAL_SRC_FILES的终极技巧](http://blog.ready4go.com/blog/2013/05/20/write-local-src-files-in-android-dot-mk-ultimate-skills/)

