使用 Visual Studio 2010 开发 C# Metro Style 应用
---------------------------

主要步骤
=======

1.	[下载并配置 NuGet 插件](http://www.cnblogs.com/Dlonghow/archive/2012/03/16/2399993.html)
2.	[MahApps.Metro](http://www.cnblogs.com/dingli/archive/2012/05/29/2524172.html)


---

1.	VS2010 —— 工具 —— 扩展管理器 —— 联机库 —— nuget
2.	管理 nuget 程序包（项目上点右键） —— MahApps.Metro
3.	新建一个 WPF， Windows Presentation Foundation 项目，然后 blabla
4.	[官方文档 Quick Start](http://mahapps.com/MahApps.Metro/guides/quick-start.html)


---

Quick Start

1.	MainWindow.xaml
	-	将 Window 标签替换为 Controls:MetroWindow 标签
	-	添加 ```xmlns:Controls="clr-namespace:MahApps.Metro.Controls;assembly=MahApps.Metro"```
2.	MainWindow.xaml.cs
	-	添加命名空间 `MahApp.Metro` 和 `MahApp.Metro.Control`
	-	去掉 MainWindow 的基类
3.	App.xaml
	-	添加以下内容
	
			<Application.Resources>
				<ResourceDictionary>
					<ResourceDictionary.MergedDictionaries>
						<ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Controls.xaml" />
						<ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Fonts.xaml" />
						<ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Colors.xaml" />
						<ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Accents/Blue.xaml" />
						<ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Accents/BaseLight.xaml" />
					</ResourceDictionary.MergedDictionaries>
				</ResourceDictionary>
			</Application.Resources>


--------

##	论如何在 WPF 应用中使用 WindowsForm 的方法

项目那里找到“引用”，然后右键“添加引用”，找到 .NET 中的 System.Windows.Forms

----

##	在 Wpf 应用中用 enter 实现 tab 的效果

[http://madprops.org/blog/enter-to-tab-in-wpf/](http://madprops.org/blog/enter-to-tab-in-wpf/)

[http://www.cnblogs.com/zhengwen/archive/2011/03/16/1986191.html](http://www.cnblogs.com/zhengwen/archive/2011/03/16/1986191.html)

	if (e.Key == Key.Enter)
    {
        var uie = e.OriginalSource as UIElement;
        e.Handled = true;
        uie.MoveFocus(new TraversalRequest(FocusNavigationDirection.Next));
    }


---

##	在 WPF 应用中调用外部程序（如CMD）

添加命名空间

	using System.Diagnostics;

创建一个 process

	Process process = new Process();

以执行 cmd 命令为例，通常有两种方法。其一是通过 cmd 后面加参数执行语句。

	process.StartInfo.FileName = "cmd";				// 要调用的外部程序名称
    process.StartInfo.Arguments = "/c 日报提交.bat";	// 调用时所添加的参数
    process.StartInfo.UseShellExecute = false;		// 是否启动 shell 程序执行
    process.StartInfo.CreateNoWindow = true;		// 是否要显示被调用程序的窗口
    process.Start();								// 开始执行
	process.WaitForExit(5000);						// 等待执行结束

另外一种是重定向被调用窗口的标准输入输出流，然后以 C# 的方式写入内容，并读出结果

	process.StartInfo.FileName = "cmd";				// 要调用的外部程序名称
    process.StartInfo.UseShellExecute = false;		// 是否启动 shell 程序执行
    process.StartInfo.CreateNoWindow = true;		// 是否要显示被调用程序的窗口
    process.StartInfo.RedirectStandardInput = true;	// 重定向标准输入流
    process.StartInfo.RedirectStandardOutput = true;// 重定向标准输出流
	process.StandardInput.WriteLine("start 日报提交.bat");
	process.Start();								// 开始执行
	process.WaitForExit(5000);						// 等待执行结束
    process.Close();
    process = null;
    process.OutputDataReceived += new DataReceivedEventHandler(Process_OutputDataReceived);
    System.IO.StreamReader sr = process.StandardOutput;
    String output = sr.ReadToEnd();
    return output;

用此方法时，通过重定向所输出的内容可能需要自己新建一个方法进行输出，即那个 Process_OouputDataReceived()

	private static void Process_OutputDataReceived(object sender, DataReceivedEventArgs e)
	{
		String s = e.Data;
		Console.WriteLine(s);
	}


但实际上使用的时候，第二种方法总是还多多少少有些问题，还是第一种比较靠谱。

---
