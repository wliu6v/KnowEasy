SVN
===============================

环境：  
Visual SVN Server + Windows Server 2003  
TortoiseSVN + Windows 7


##	SVN 日志

SVN库的有些设置可以通过 hook 来实现。每个版本库下面都有一个 hooks 目录，其中存在着许多 hook 模板。可以通过修改模板以达到修改设置的目的（应将 .tmpl 改为 .bat ）。

[SVN修改日志的方法](http://blog.csdn.net/starnight_cbj/article/details/5904654)

###	强制写日志

文件名:pre-commit-log.bat

	@echo off
	setlocal
	set REPOS=%1
	set TXN=%2
	rem check that logmessage contains at least 5 characters
	svnlook log "%REPOS%" -t "%TXN%" | findstr ".........." > nul
	if %errorlevel% gtr 0 goto err
	exit 0
	:err
	echo Empty log message not allowed. Commit aborted! 1>&2
	exit 1

###	修改历史日志

文件名:pre-revprop-change.bat
	
	REM SVN pre-revprop-change hook allows edit of logmessages from TSVN
	
	setlocal
	set REPOS=%1
	set REV=%2
	set USER=%3
	set PROPNAME=%4
	set ACTION=%5
	
	if not "%ACTION%"=="M" goto refuse
	if not "%PROPNAME%"=="svn:log" goto refuse
	goto OK
	
	:refuse
	echo Cann't set %PROPNAME%/%ACTION%, only svn:log is allowed 1>&2
	endlocal
	exit 1
	
	:OK
	endlocal
	exit 0 


http://www.subversion.org.cn

RCS (Revision Control System), 修订控制系统。

CVS (Concurrent Versions System)，协作版本系统/并发版本系统。

SVN (Apache Subversion)。

#	Subversion 基本概念

##	基本架构

!
[](http://www.subversion.org.cn/svnbook/nightly/images/ch01dia1.png)

##	copy-modify-merge Model 

拷贝-修改-合并 方案。

!
[copy-modify-merge Model](http://www.subversion.org.cn/svnbook/nightly/images/ch02dia5.png)

##	SVN 库状态

-	未修改且是当前的
	-	update null / commit null
-	本地已修改且是当前的
	-	update null / commit 
-	未修改且不是当前的
	-	update / commit null
-	本地已修改且不是最新的
	-	can't commit / update first

只有当本地的文件是最新的时候，才能正常的进行提交。如果服务器上存在“你没见过的”更新内容，那么就需要先 update 然后才能进行 commit 。


#	Subversion 基本使用

##	SVN 命令

SVN 命令经常存在选项。短选项有一个连字符和一个字母；长选项有两个连字符和一串字母(或数字)。每个选项都有长选项，同时有个别选项有短选项。

##	基本的工作周期

1.	update work coy
	-	svn update
2.	Make changes
	-	svn add
	-	svn delete
	-	svn copy
	-	svn move
3.	Examine your changes.
	-	svn status	// 查看对每个文件做出的修改
	-	svn diff	// 检查修改的详细信息
4.	Possibly undo some changes
	-	svn revert
5.	Resolve conflicts (merge other's changes)
	-	svn update
	-	svn resolve
6.	Commit your changes
	-	svn commit


##	svn status (svn st) 查看本地修改概况

当所有的文件都处于 A、C、D、M、U、G 六种状态之一时，就可以进行提交了。

-	A = Add = 添加
-	C = Create = 创建
-	D = 删除
-	M = 修改
-	U = 本地没有修改
-	G = 合并，表示本地已经修改过，与版本库没有重叠的地方。
-	! = 文件丢失了，可能需要 svn delete 手动删除。
-	? = 出现了多的文件， 可能需要 svn add 将其加入版本控制。

可以通过 ` svn status -v` 命令获取新旧版本号差异。

通过 `svn status -u -v` 可以获知哪些文件已经过期，需要先进行 update 进行更新。过期的文件将会以 * 标记

##	svn diff

通过 `svn diff` 参数可以将两次修改的内容显示出来。

##	svn revert 取消本地的更改

可以取消某个更改。比如可以 `svn revert README` 。

##	解决冲突 (合并别人的修改)

好复杂- = 

使用 `svn st -u` 检查是否有文件需要先进行 update。 如果有，进行 svn update， 然后可以使用 p 指令，将修改前后的文件都获取到。然后手动将其进行合并，之后使用 `svn resolve --accept working "filename"` 命令清理多余的文件然后取消 conflict 状态。

##	svn log 查看log

通过 `svn log` 命令可以查看log。

	svn log -r 5:19    # shows logs 5 through 19 in chronological order

	svn log -r 19:5    # shows logs 5 through 19 in reverse order

	svn log -r 8       # shows log for revision 8

通过 `svn -v` 可以查看 path 的更改。

可以通过 `svn log -r 2 http://svn.collab.net/repos/svn` 这样获取该版本库的日志。

##	svn diff	比较

`svn diff` 将本地文件与缓存在 .svn 中的拷贝

`svn diff -r 3` 将本地文件与指定版本进行比较

`svn diff -r 3 rules.txt` 比较指定文件

`svn diff -r 2:3 rules.txt` 比较两个不同版本库


##	svn cleanup

如何使用？

不要将工作拷贝锁与Subversion用户使用并发版本控制的“锁定-修改-解锁”模型创建的锁混淆；


##	修订版本关键字

-	HEAD, 版本库中最新的版本
-	BASE, 
-	COMMITTED, 项目最近修改的版本，与 BASE 相同或更早。
-	PREV, 一个项目最后修改版本之前的版本。可以认为是 COMMITED-1

##	svn ignore

通过 svn ignore 将指定文件或者文件夹忽略。

可以使用 `--no-ignore` 参数忽略这个忽略列表。


SVN 分支
==================================================

首先需要知道， svn 内部并没有内在的分支概念，只有拷贝。当拷贝一个目录后，这个结果目录就是一个“分支”，只是因为你给了他这样一个含义而已。包括标签也是如此，只是一个版本目录的copy，只是copy之后不进行任何操作，他就算是一个tag了。

场景：

假设SVN项目名为 svnTest ，且此目录下有三个目录：

-	branches
-	tags
-	trunk

首先，进入 svnTest 目录。通过 svn copy 命令将 svnTest/trunk copy到 svnTest/tags/blabla 下。

然后，再次使用 svn copy  命令将 svnTest/trunk copy到 svnTest/branches/blabla 下。

之后对代码进行编辑可以在 branches 的相应标签中进行了。对分支的内容进行修改并及时 commit 。待工作完成后便可以进行 merge 操作，将该分支的内容 merge 到主干，这样服务器上的主干就是从分支合并过去的，是最新的了。然后回到 trunk 目录下并 update，可将本地的主干也更新成最新的。 



SVN
==================================================

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

SVN Relocate
==================================================

[如何进行 svn relocate](http://www.cnblogs.com/fengwei/archive/2012/09/20/2694854.html)

当 SVN 服务器进行迁移后，客户端应对之前存在的库进行 relocate 命令。

	svn switch --relocate oldPath newPath


SVN Ignore List 
==================================================

##	XCode

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

---

New Version

	#########################
	# .gitignore file for Xcode4 / OS X Source projects
	#
	# Version 2.1
	# For latest version, see: http://stackoverflow.com/questions/49478/git-ignore-file-for-xcode-projects
	#
	# 2013 updates:
	# - fixed the broken "save personal Schemes"
	# - added line-by-line explanations for EVERYTHING (some were missing)
	#
	# NB: if you are storing "built" products, this WILL NOT WORK,
	# and you should use a different .gitignore (or none at all)
	# This file is for SOURCE projects, where there are many extra
	# files that we want to exclude
	#
	#########################
	
	#####
	# OS X temporary files that should never be committed
	#
	# c.f. http://www.westwind.com/reference/os-x/invisibles.html
	
	.DS_Store
	
	# c.f. http://www.westwind.com/reference/os-x/invisibles.html
	
	.Trashes
	
	# c.f. http://www.westwind.com/reference/os-x/invisibles.html
	
	*.swp
	
	# *.lock - this is used and abused by many editors for many different things.
	#    For the main ones I use (e.g. Eclipse), it should be excluded 
	#    from source-control, but YMMV
	
	*.lock
	
	#
	# profile - REMOVED temporarily (on double-checking, this seems incorrect; I can't find it in OS X docs?)
	#profile
	
	
	####
	# Xcode temporary files that should never be committed
	# 
	# NB: NIB/XIB files still exist even on Storyboard projects, so we want this...
	
	*~.nib
	
	
	####
	# Xcode build files -
	#
	# NB: slash on the end, so we only remove the FOLDER, not any files that were badly named "DerivedData"
	
	DerivedData/
	
	# NB: slash on the end, so we only remove the FOLDER, not any files that were badly named "build"
	
	build/
	
	
	#####
	# Xcode private settings (window sizes, bookmarks, breakpoints, custom executables, smart groups)
	#
	# This is complicated:
	#
	# SOMETIMES you need to put this file in version control.
	# Apple designed it poorly - if you use "custom executables", they are
	#  saved in this file.
	# 99% of projects do NOT use those, so they do NOT want to version control this file.
	#  ..but if you're in the 1%, comment out the line "*.pbxuser"
	
	# .pbxuser: http://lists.apple.com/archives/xcode-users/2004/Jan/msg00193.html
	
	*.pbxuser
	
	# .mode1v3: http://lists.apple.com/archives/xcode-users/2007/Oct/msg00465.html
	
	*.mode1v3
	
	# .mode2v3: http://lists.apple.com/archives/xcode-users/2007/Oct/msg00465.html
	
	*.mode2v3
	
	# .perspectivev3: http://stackoverflow.com/questions/5223297/xcode-projects-what-is-a-perspectivev3-file
	
	*.perspectivev3
	
	#    NB: also, whitelist the default ones, some projects need to use these
	!default.pbxuser
	!default.mode1v3
	!default.mode2v3
	!default.perspectivev3
	
	
	####
	# Xcode 4 - semi-personal settings
	#
	#
	# OPTION 1: ---------------------------------
	#     throw away ALL personal settings (including custom schemes!
	#     - unless they are "shared")
	#
	# NB: this is exclusive with OPTION 2 below
	xcuserdata
	
	# OPTION 2: ---------------------------------
	#     get rid of ALL personal settings, but KEEP SOME OF THEM
	#     - NB: you must manually uncomment the bits you want to keep
	#
	# NB: this *requires* git v1.8.2 or above; you may need to upgrade to latest OS X,
	#    or manually install git over the top of the OS X version
	# NB: this is exclusive with OPTION 1 above
	#
	#xcuserdata/**/*
	
	#     (requires option 2 above): Personal Schemes
	#
	#!xcuserdata/**/xcschemes/*
	
	####
	# XCode 4 workspaces - more detailed
	#
	# Workspaces are important! They are a core feature of Xcode - don't exclude them :)
	#
	# Workspace layout is quite spammy. For reference:
	#
	# /(root)/
	#   /(project-name).xcodeproj/
	#     project.pbxproj
	#     /project.xcworkspace/
	#       contents.xcworkspacedata
	#       /xcuserdata/
	#         /(your name)/xcuserdatad/
	#           UserInterfaceState.xcuserstate
	#     /xcsshareddata/
	#       /xcschemes/
	#         (shared scheme name).xcscheme
	#     /xcuserdata/
	#       /(your name)/xcuserdatad/
	#         (private scheme).xcscheme
	#         xcschememanagement.plist
	#
	#
	
	####
	# Xcode 4 - Deprecated classes
	#
	# Allegedly, if you manually "deprecate" your classes, they get moved here.
	#
	# We're using source-control, so this is a "feature" that we do not want!
	
	*.moved-aside
	
	####
	# UNKNOWN: recommended by others, but I can't discover what these files are
	#
	# ...none. Everything is now explained.

