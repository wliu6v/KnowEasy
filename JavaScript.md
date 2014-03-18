##	JavaScript 的实现

JavaScript 的核心 ECMAScript 描述了该语言的语法和基本对象；

DOM 描述了处理网页内容的方法和接口；

BOM 描述了与浏览器进行交互的方法和接口。

##	ECMAScript

[ek - ma - script]

ECMAScript 可以为不同种类的宿主环境提供核心的脚本编程能力，因此核心的脚本语言是与任何特定的宿主环境分开进行规定的。

ECMAScriipt 描述了 语法、类型、语句、关键字、保留字、运算符、对象 等。

ECMAScript 仅仅是描述，定义了脚本语言的所有属性、方法和对象。其他语言可以实现 ECMAScript 来作为功能的基准。



##	DOM

Document Object Model， 文件对象模型。

DOM 可以以一种独立于语言和平台的方式访问和修改一个文档的内容与结构。

DOM 主要依靠于文档中的节点。

### 节点

根据 DOM， HTML 文档中的每个成分都是一个节点。

-	文档节点：整个文档
-	元素节点：每个 HTML 标签
-	文本节点：包含在 HTML 元素中的文本
-	属性节点：HTML 属性
-	注释节点：注释

节点之间的关系：父节点，子节点，同辈，后代，先辈。。。

通过 getElementById 与 getElementByTagName 查找并访问节点。

通过 parentNode、firstChild 及 lastChild 可在文档中的不同节点间访问。

##	BOM

Broswer Object Model, 浏览器对象模型。

通过 BOM 可以访问和操控浏览器窗口。比如：

-	移动窗口
-	更改状态栏文本
-	弹出新窗口
-	支持 Cookies
-	ActiveX

BOM 在 js 应用中没有相关标准，每个浏览器都有其自己对 BOM 的实现方式。

BOM 主要处理浏览器与框架。

## XML 和 DTD

Document Type Definition， 文档类型定义。

使用一系列的合法元素来定义文档结构

[参考一](http://blog.sina.com.cn/s/blog_53a62e21010000rt.html)

[参考二](http://blog.csdn.net/hj_545/article/details/6408244)

