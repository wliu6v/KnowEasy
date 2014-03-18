ActiveX
=================================================

[参考教程 - 浏览器插件之 ActiveX 开发](http://www.cnblogs.com/qguohog/archive/2013/01/22/2871805.html)


##	如何实现一个最基本的 ActiveX 控件

1. 新建 MFC Active X 控件并通过类视图为其添加一个方法。（教程第一章节）
2. 修改资源文件。（第二章第4节）
3. 获取其 GUID ,用于页面加载插件。
4. 编写测试页面并进行测试。

Advance ： 为其添加安全声明。（第二章节）

附： 测试页面
	
	<html>
	<head>
	<meta http-equiv="content-type" content="text/html;charset=utf-8" />
	<title>My ActiveX Test</title>
	<script type="text/javascript" charset="utf-8">
	
		function doAdd() {
			var plugin = document.getElementById("plugin");
			alert( plugin.AddFun(2, 3) );
		}
		
	</script>
	</head>
	<body>
	
	<object ID="plugin" WIDTH=0 HEIGHT=0 CLASSID="CLSID:108F0856-73FC-4A55-B9FC-9D1D997FCAA4"><PARAM NAME="_Version" VALUE="65536"><PARAM NAME="_ExtentX" VALUE="2646"><PARAM NAME="_ExtentY" VALUE="1323"><PARAM NAME="_StockProps" VALUE="0"></object><br />
	
	<button onclick="doAdd()">add</button>
	
	</body>
	</html>


----------------

##	为 ActiveX 增加一个属性

为 COleControl 的子类（如 CSamclientCtrl，SamclientCtrl.h）建立一个 protected 修饰的属性，同时为该属性创建一个消息映射函数。如：

	protected:
		CString m_strDeviceSN;
		afx_msg void OnDeviceSNChanged();

实现 OnDeviceSNChanged 方法。如：

	void CSamclientCtrl::OnDeviceSNChanged() {
		SetModifiedFlag();
	}

为该属性定义调度标识符（dispid.h）。如：

	#define DISPID_PROPERTY_DEVICESN 100L

在为 COleControl 子类增加调度映射。如：（SamclientCtrl.cpp）

	BEGIN_DISPATCH_MAP(CSamclientCtrl, COleControl)
		//{{AFX_DISPATCH_MAP(CSamclientCtrl)
		DISP_PROPERTY_NOTIFY_ID(CSamclientCtrl, "DeviceSN", DISPID_PROPERTY_DEVICESN, m_strDeviceSN, OnDeviceSNChanged, VT_BSTR)
		//}}AFX_DISPATCH_MAP
	END_DISPATCH_MAP()

定义 OCX 的调度接口（samclient.idl）。

	dispinterface _Dsamclient
	{
		properties:
			[id(DISPID_PROPERTY_DEVICESN)]
			LPTSTR DeviceSN;
	};

部分属性的类型对应（Unicode 环境）：

LPTSTR 	—— VT_BSTR
INT		—— VT_I4

----------------

##	为 ActiveX 增加一个方法

为 COleControl 的子类（如 CSamclientCtrl, SamclientCtrl.h）建立一个 protected 修饰的方法。如：

	protected:
		afx_msg DWORD GetDeviceSN();

实现该方法。如：

	DWORD CSamclientCtrl::GetDeviceSN() {
		// ...
	}

为该方法定义调度标识符（dispid.h）。如：

	#define DISPID_FUNCTION_GETDEVICESN 17L

在为 COleControl 子类增加调度映射。如：（SamclientCtrl.cpp）

	BEGIN_DISPATCH_MAP(CSamclientCtrl, COleControl)
		//{{AFX_DISPATCH_MAP(CSamclientCtrl)
		DISP_FUNCTION_ID(CSamclientCtrl, "GetDeviceSN", DISPID_FUNCTION_GETDEVICESN, GetDeviceSN, VT_I4, VTS_NONE)
		//}}AFX_DISPATCH_MAP
	END_DISPATCH_MAP()

定义 OCX 的调度接口（samclient.idl）。

	dispinterface _Dsamclient
	{
		methods:
			[id(DISPID_FUNCTION_GETDEVICESN)]
			DWORD GetDeviceSN();
	};

部分返回值与参数的类型对应（Unicode 环境）：

DWORD	—— 

----------------

##	运行浏览器通过 new ActiveXObject 的方式加载插件

我们可以通过在 xxxCtrl 中的 `IMPLEMENT_OLECREATE_EX` 一项中找到该 ActiveX 控件的名称，然后在 JavaScript 代码中通过 new ActiveXObject 进行加载。但是通常直接这样加载了之后并不能正常的使用控件中的方法与属性。

解决方法很简单，只需要实现 COleCtrl 中的方法 IsInvokeAllowed, 将其返回值设为 true 即可。

	BOOL CXxxxxCtrl::IsInvokeAllowed(DISPID dispid) {
		return true;
	}

----------------

##	AcitveX 中的多线程

[参考教程：ActiveX异步回调JavaScript](http://blog.csdn.net/tingsking18/article/details/4278619)


1. 在 xxxCtrl.cpp 中添加头文件 <process.h> 与 <MsHTML.h>
2. 为控件类添加以下属性 
	-	callbackFunction，类型为 CString （BSTR）。网页部分应该将需要回调的函数名称赋值给它。应该注意的是，赋值的是函数名称，是个字符串。
	-	param，类型为 SHORT。作为传给回调函数的参数。此处仅为举例，并不一定是此类型。
3. 为控件类添加以下方法
	-	invoke 方法。返回值 void，参数 SHORT 类型。用于被网页所直接调用。开启一个新线程运行异步函数。
	-	OnXXXThread 方法。返回值 LONG，参数为 WPARAM(ULONG) 与 LPARAM (LONG)。用于异步处理结束之后进行调用。
4.	在 xxxCtrl.h 中添加以下属性
5.	在 xxxCtrl.cpp 中添加以下方法
	
