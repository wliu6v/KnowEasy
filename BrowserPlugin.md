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

##	3.	制作数字证书

##	4.	网页检查插件并自动安装插件