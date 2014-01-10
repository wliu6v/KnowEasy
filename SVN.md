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