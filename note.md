# SVN

## 不需要提交的文件

	.DS_Store
	build/
	*.pbxuser
	!default.pbxuser
	*.mode1v3
	!default.mode1v3
	*.mode2v3
	!default.mode2v3
	*.perspectivev3
	!default.perspectivev3
	*.xcworkspace
	!default.xcworkspace
	xcuserdata
	profile
	*.moved-aside
	DerivedData
	.idea/ 
	
在 Mac 下，可以修改 SVN 的全局设置使其在客户端永远的忽略这些文件。  
SVN 的配置文件通常是 `~/.subversion/config` 。我们可以修改 `global-ignores` 一项使其在以后忽略这些文件。如下：

	# global-ignores = *.o *.lo *.la *.al .libs *.so *.so.[0-9]* *.a *.pyc *.pyo
	#   *.rej *~ #*# .#* .*.swp .DS_Store UserInterfaceState.xcuserstate 
	#   build/ *.pbxuser !default.pbxuser *.mode1v3 !default.mode1v3 *.mode2v3
	#   !default.mode2v3 *.perspectivev3 !default.perspectivev3 *.xcworkspace
	#   !default.xcworkspace xcuserdate profile *.moved-aside DerivedData .idea/
	

##	清理SVN中的所有.svn文件夹

	find . -type d -name ".svn" -exec rm -rf {} \;
	
-	find : 查找命令
-	.:在当前目录搜索
-	-type d :类型为目录
-	-name ".svn" :名称
-	-exec:应执行的命令
-	rm -rf: bash中的删除文件夹命令。rm只能删除文件，要删除文件夹就要加上-rf参数。


---
  
#	Bash

http://blog.csdn.net/wooin/article/details/580772

## 一个简单的 Hello World 程序

最简单的例子

	#!/bin/bash
	# This is a very simple example
	echo Hello World	

-	第一行的 #! 是说明这个文件的类型的，有点类似于windows底下的后缀名。linux系统根据 #! 及该字符串后面的信息确定文件类型。此例子中说明该文件是个 Bash 程序，需要由 /bin 目录下的 bash 程序来解释执行。
-	\# 开头的内容是 Bash 程序的注释。除非是 #! 这样的组合，否则 # 后面的内容总被认为是注释。
-	echo 可以将后面的字符串输出到标准输出中。

我们可以通过 `$bash hello` 这样的命令运行它，也可以把hello改成一个可执行文件，然后直接运行。


