身份证支付终端 IDCardPay
======================

2013.12.18

主要功能模块
----------------

1.	读取身份证信息
2.	利用 json 处理信息
3.	通过 http 向后台收发信息
4.	wpf + metro 界面


##	1. 读取身份证信息

读证使用 samclient (rc2readerlibrary.dll)

要读取身份证信息，需在 wpf 程序中调用 C 语言编写的 DLL。这存在一些问题。

wpf 程序为托管代码，也就是由公共语言运行库环境（而不是直接由操作系统）执行的代码。而 C DLL 通常是非托管代码。因此较难相互调用。

###	解决方案一

[看上去尚未验证的一篇文章](http://blog.csdn.net/jarvischu/article/details/6634185)

添加命名空间 `System.Runtime.InteropServices`，然后使用 DLLImport等方式导入库。对于C#中没有的类型使用IntPtr。

###	解决方案二

C# 调用 C DLL 比较麻烦，因此可以考虑直接调用exe，通过标准输入输出流进行数据交互。这样做的话，不管是什么语言，只要能够使用标准输入输出流都可以相互调用了。

C EXE 中的标准输出自然就是 printf 了。

	Process p = new Process();
	p.StartInfo.FileName = "RC2Exe";
	p.StartInfo.Arguments = "";
	p.StartInfo.UseShellExecute = false;
	p.StartInfo.CreateNoWindow = true;
	p.StartInfo.RedirectStandardInput = true;
	p.StartInfo.RedirectStandardOutput = true;
	p.Start();
	p.WaitForExit(10000);
	StreamReader sr = p.StandardOutput;
	String output = sr.ReadToEnd();
	p.Close();
	p = null;


###	2. json

nuget -> Json.NET

###	3. http 收发信息
 

###	4. wpf + metro 界面

nuget -> MahApps.Metro


Reference
----------------


[如何通过代码修改wpf窗口的title](http://book.51cto.com/art/200809/91353.htm)