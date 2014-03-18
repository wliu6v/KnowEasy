Visual Studio
==================================

##	关于Visual Studio 2010 中的 ipch 与 .sdf 文件夹

[Visual Studio 2010工程目录下的ipch文件夹和.sdf文件](http://www.cnblogs.com/web100/archive/2012/12/21/vs2010-ipch-sdf.html)

可以通过某些设置将 ipch 与 .sdf 文件放在单独的文件夹中，避免占用项目文件夹的空间。设置方法如下：

-	进入 Tools->Options->Text Editor->C/C++->Advanced，找到 Fallback Location 的属性组
-	将"Always Use Fallback Location"设置为 true。
-	将"Do Not Warn If Fallback Location Used" 设置为 true。
-	将"Fallback Location" 设置为固定路径，可以定期进行清理，释放空间。
-	删除解决方案目录下的 sdf 文件和 ipch 目录。


---
  
Bash
==================================================

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

##	获取自身路径

`dirname "$0"`


Windows CMD
=================================================


[批处理命令之精确获取操作系统版本信息](http://blog.csdn.net/clever101/article/details/8453378)

[CMD For 用法](http://club.topsage.com/thread-597580-1-1.html)


---

[XP远程桌面mstsc和带参数的mstsc /console](http://nic.upc.edu.cn/s/2/t/20/03/98/info920.htm)


##	获取CMD文件自身路径

可使用 `%~dp0` 。

`%~` 表示使用参数扩展语法。
`dp` 表示扩展之后的参数为只包含盘符和路径的形式，不包括文件名。
`0` 表示第0个参数，也就是 .bat 文件自身

##	cd到其他驱动器

使用 `/d` 参数

`cd /d D:/blabla`



FTP 命令
=====================================================

	cd %~dp0
	ftp -s:publishftp.txt
	timeout 1

publishftp.txt 内容

	open ftp.xxxx.net
	YOUR_USERNAME
	YOUR_PASSWORD
	cd Share/ftp_directory
	lcd ./publish
	send english.html
	send 中文名.exe
	bye

直接通过 CMD 中的一行命令进行 ftp 的登录等操作较难实现，比较好的方法是将 ftp 需要进行的操作都放在同一个文本文件中，然后在命令行中使用此文本文件作为参数之一。

需要注意的是，该 ftp 命令文件最好是使用 ascii 编码，避免中文部分在 cmd 中出现乱码。（如 cmd 默认编码不为 ascii 码则需进行其他设置。不知跟 FTP 服务器的编码方式有无关系，需进一步进行确认） 