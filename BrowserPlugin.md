#	ActiveX 控件的自动安装

1.	编写 inf 文件
2.	打包成 cab 文件
3.	制作数字证书
4.	网页检查插件并自动安装插件

##	1. 编写 INF 文件

在编写 ActiveX控件时，找到（或加入）这样的语句：

	IMPLEMENT_OLECREATE_EX(CActiveXProject, "ActiveXProject.YourProject.1",
	0x7585D848, 0x1BD6, 0x4F8C, 0xA3, 0x2C, 0x70, 0xE0, 0xA5, 0xC0, 0xF2, 0xF7)

这里的 `ActiveXProject.YourProject.1` 可以用于页面检测插件是否存在。而那串十六进制数值就是这个 ActiveX 的 CLSID，此值可以理解为 "7585D848-1BD6-4F8C-A32C-70E0A5C0F2F7" （虽然我也不知道为什么是这样）。

有了 CLSID， 就可以开始编写 INF 文件了。

首先将编译生成的 yourproject.ocx 文件与它所依赖的其他文件放在同一个文件夹中，然后在那个文件夹下建立一个与 ocx 文件同名的 inf 文件， 如 YourProject.inf 。然后修改其内容如下所示：

	[version]
	signature="$CHICAGO$"
	AdvancedINF=2.0
	
	[Add.Code]
	yourproject.ocx=yourproject.ocx
	dll1.dll=dll1.dll
	dll2.dll=dll2.dll
	
	[yourproject.ocx]
	file=thiscab    
	clsid={7585D848-1BD6-4F8C-A32C-70E0A5C0F2F7}
	FileVersion=1,0,0,0
	RegisterServer=yes
	DestDir=10
	
	[dll1.dll]
	file=thiscab
	FileVersion=1,0,0,0
	DestDir=10
	
	[dll2.dll]
	file=thiscab
	FileVersion=1,0,0,0
	DestDir=10

[version] 字段通常保持不变，用于支持特定版本的 windows 系统。  
[Add.Code] 字段需要列出 yourproject.ocx 与其依赖的所有文件。左边是文件名，右边是安装后的文件名（大概吧= =）。通常为了方便维护，将其保持为一致即可。  
再下面就是每个文件的具体描述了。`file=thiscab` 指明了加载该文件的位置。thiscab 就是当前目录。如果要对不同的机型加载不同的文件，也可以在这里进行设定。 `clsid` 就是上文所得到的 CLSID 了。 `FileVersion` 是版本号，这里的版本号会用在页面中。用于识别插件是否已被更新。`DestDir` 表示这些文件将会被安装在哪个目录下。10 代表了 Windows 目录。如果是 11 便是 System32 目录（64 位系统下会有所不同）。

编写完成后保存。现在控件所在目录应包括如下文件：

-	ActiveX控件文件，即 yourproject.ocx
-	控件所依赖的文件，即 dll1.dll, dll2.dll
-	inf 文件， 即 yourproject.inf

##	2.	打包成 cab 文件

使用 Windows 自带的 iExpress 工具进行打包。在“运行”中输入 `iexpress` 即可运行。

![](https://raw.github.com/wliu6v/KnowEasy/master/img/BrowsePlugin_01.png)


打包时选择第三项

![](https://raw.github.com/wliu6v/KnowEasy/master/img/BrowsePlugin_02.png)


将上一步中所有的文件 （ActiveX控件文件，控件所依赖的文件，inf 文件) 都添加进来

![](https://raw.github.com/wliu6v/KnowEasy/master/img/BrowsePlugin_03.png)

输入要生成的 cab 文件名。同时要勾选 "Store files using Long File Name inside Package" 选项。文件名需要符合 8,3 规则，就是说文件名应该不能长于8位，扩展名不能长于3位（大概如此吧，我猜的）。如果文件名需要长于八位，可以在成功生成 cab 文件之后直接进行重命名。

![](https://raw.github.com/wliu6v/KnowEasy/master/img/BrowsePlugin_04.png)

下一步，选择 "Don't save"

![](https://raw.github.com/wliu6v/KnowEasy/master/img/BrowsePlugin_05.png)

之后就可以成功生成 cab 文件了。

实际上，这时候已经可以在本地使页面自动加载控件了。但如果要将控件放在网上，让页面能够自动进行下载，那么就要为其制作一个数字证书。

##	3.	制作数字证书

首先下载数字签名工具包 [http://files.cnblogs.com/babyt/SignTool.rar](http://files.cnblogs.com/babyt/SignTool.rar)

将下载之后的文件夹添加到 path 路径中，然后在任意目录建立证书与私钥文件。打开 cmd， 输入

	makecert /sv "project.PVK" /r /n "CN=Kingtrust.net, O=liuwei" project.cer

就可以生成私钥文件与数字证书了。这期间需要创建私钥密码并输入多次。需要牢牢记住该密码。

打开证书文件，安装证书，将其装到 "受信任的根证书颁发机构" 中，然后运行 "signcode.exe" 工具。

-	要经过数字签名的文件选择刚才制作的 cab 文件
-	签名选项选择 “自定义”
-	"从文件选择...", 选择刚才生成的证书文件 .cer
-	选择私钥文件 .pvk 。其他选项保持默认即可
-	签名算法默认
-	其他证书默认
-	数据描述随意
-	时间戳保持默认，不需要添加。

之后如果提示成功即完成了对该 CAB 文件的签名。在浏览器中安装之前还需要先安装数字证书，用户需要将数字证书添加到受信任的根证书颁发机构。

##	4.	网页检查插件并自动安装插件

页面首先要判断浏览器类型

	var Sys = {};
	var ua = navigator.userAgent.toLowerCase();
	var s;
	(s = ua.match(/(msie\s|trident.*rv:)([\w.]+)/)) ? Sys.ie = s[1] :
	(s = ua.match(/firefox\/([\d.]+)/)) ? Sys.firefox = s[1] :
	(s = ua.match(/chrome\/([\d.]+)/)) ? Sys.chrome = s[1] :
	(s = ua.match(/opera.([\d.]+)/)) ? Sys.opera = s[1] :
	(s = ua.match(/version\/([\d.]+).*safari/)) ? Sys.safari = s[1] : 0;

如果是 IE 浏览器，则加载 ActiveX控件

	if (Sys.ie) {
		document.write('<object ID="plugin" WIDTH=0 HEIGHT=0 CLASSID="CLSID:7585D848-1BD6-4F8C-A32C-70E0A5C0F2F7" CODEBASE="http://yourproject.kingtrust.net/yourproject.cab#version=1,0,0,0"><PARAM NAME="_Version" VALUE="65536"><PARAM NAME="_ExtentX" VALUE="2646"><PARAM NAME="_ExtentY" VALUE="1323"><PARAM NAME="_StockProps" VALUE="0"></object><br />');
	} 

要注意将替换 CLASSID 中的 CLSID 替换为实际的值。CODEBASE 属性就指明了当前页面所需要的CAB应该从何处获取以及应使用哪个版本。

要判断控件是否存在。有两种方法：

	if (Sys.ie) {
		var plugin = document.getElementById("plugin");
		if (plugin.object == null) {
			alert("插件未安装");		
		}
	}

或者

	if (Sys.ie) {
		var plugin = null;
		try {
			plugin = new ActiveXObject("ActiveXProject.Project.1");
		} catch (e) {
			alert("插件未安装");
		}
	}

第二种方式的优点在于无需在页面上放置插件即可进行判断。需要将ActiveXObject 内的字符串替换为上文中提到的内容。



##	5.	尾巴

卸载安装的 cab 文件，可编写一个 bat 文件，然后右键“以管理员权限运行”

	@echo off
	del "%windir%\yourproject.inf"
	del "%windir%\yourproject.ocx"
	del "%windir%\dll1.dll"
	del "%windir%\dll2.dll"


#	多线程

##	进程与线程

进程是表示资源分配的基本单位，线程是系统中并发执行的单位。

##	pthread

通过 `#include <pthread.h>` 添加多线程相关的头文件。

使用 g++ 编译时添加参数 `-lpthread` 添加对应的库。



