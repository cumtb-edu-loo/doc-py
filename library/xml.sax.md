# `xml.sax` \--- 支持 SAX2 解析器¶

**源代码:** [Lib/xml/sax/__init__.py](https://github.com/python/cpython/tree/3.12/Lib/xml/sax/__init__.py)

* * *

`xml.sax` 包提供多个模块，它们在 Python 上实现了用于 XML (SAX) 接口的简单 API。 这个包本身为 SAX API 用户提供了一些最常用的 SAX 异常和便捷函数。

警告

`xml.sax` 模块对于恶意构建的数据是不安全的。 如果你需要解析不受信任或未经身份验证的数据，请参阅 [XML 漏洞](xml.md#xml-vulnerabilities)。

在 3.7.1 版本发生变更: SAX 解析器默认不会再处理通用外部实体以便提升安全性。 在此之前，解析器会创建网络连接来获取远程文件或是从 DTD 和实体文件系统中加载本地文件。 此特性可通过在解析器对象上调用 [`setFeature()`](xml.sax.reader.md#xml.sax.xmlreader.XMLReader.setFeature "xml.sax.xmlreader.XMLReader.setFeature") 对象并传入参数 [`feature_external_ges`](xml.sax.handler.md#xml.sax.handler.feature_external_ges "xml.sax.handler.feature_external_ges") 来重新启用。

可用的便捷函数如下所列:

xml.sax.make_parser( _parser_list =[]_)¶

    

~~~
创建并返回一个 SAX [`XMLReader`](xml.sax.reader.md#xml.sax.xmlreader.XMLReader "xml.sax.xmlreader.XMLReader") 对象。 将返回第一个被找到的解析器。 如果提供了 _parser_list_ ，它必须为一个包含字符串的可迭代对象，这些字符串指定了具有名为 `create_parser()` 函数的模块。 在 _parser_list_ 中列出的模块将在默认解析器列表中的模块之前被使用。

在 3.8 版本发生变更: _parser_list_ 参数可以是任意可迭代对象，而不一定是列表。

xml.sax.parse( _filename_or_stream_ , _handler_ , _error_handler =handler.ErrorHandler()_)¶
~~~
    

~~~
创建一个 SAX 解析器并用它来解析文档。 用于传入文档的 _filename_or_stream_ 可以是一个文件名或文件对象。 _handler_ 形参必须是一个 SAX [`ContentHandler`](xml.sax.handler.md#xml.sax.handler.ContentHandler "xml.sax.handler.ContentHandler") 实例。 如果给出了 _error_handler_ ，则它必须是一个 SAX [`ErrorHandler`](xml.sax.handler.md#xml.sax.handler.ErrorHandler "xml.sax.handler.ErrorHandler") 实例；如果省略，则对于任何错误都将引发 `SAXParseException`。 此函数没有返回值；所有操作必须由传入的 _handler_ 来完成。

xml.sax.parseString( _string_ , _handler_ , _error_handler =handler.ErrorHandler()_)¶
~~~
    

~~~
类似于 `parse()`，但解析对象是作为形参传入的缓冲区 _string_ 。 _string_ 必须为 [`str`](stdtypes.md#str "str") 实例或者 [bytes-like object](../glossary.md#term-bytes-like-object)。

在 3.5 版本发生变更: 增加了对 [`str`](stdtypes.md#str "str") 实例的支持。

典型的 SAX 应用程序会使用三种对象：读取器、处理句柄和输入源。 “读取器”在此上下文中与解析器同义，即某个从输入源读取字节或字符，并产生事件序列的代码段。 事件随后将被分发给处理句柄对象，即由读取器发起调用处理句柄上的某个方法。 因此 SAX 应用程序必须获取一个读取器对象，创建或打开输入源，创建处理句柄，并一起连接到这些对象。 作为准备工作的最后一步，将调用读取器来解析输入内容。 在解析过程中，会根据来自输入数据的结构化和语义化事件来调用处理句柄对象上的方法。

就这些对象而言，只有接口部分是需要关注的；它们通常不是由应用程序本身来实例化。 由于 Python 没有显式的接口标记法，它们的正式引入形式是类，但应用程序可能会使用并非从已提供的类继承而来的实现。 [`InputSource`](xml.sax.reader.md#xml.sax.xmlreader.InputSource "xml.sax.xmlreader.InputSource"), [`Locator`](xml.sax.reader.md#xml.sax.xmlreader.Locator "xml.sax.xmlreader.Locator"), `Attributes`, `AttributesNS` 以及 [`XMLReader`](xml.sax.reader.md#xml.sax.xmlreader.XMLReader "xml.sax.xmlreader.XMLReader") 接口是在 [`xml.sax.xmlreader`](xml.sax.reader.md#module-xml.sax.xmlreader "xml.sax.xmlreader: Interface which SAX-compliant XML parsers must implement.") 模块中定义的。 处理句柄接口是在 [`xml.sax.handler`](xml.sax.handler.md#module-xml.sax.handler "xml.sax.handler: Base classes for SAX event handlers.") 中定义的。 为了方便起见，[`InputSource`](xml.sax.reader.md#xml.sax.xmlreader.InputSource "xml.sax.xmlreader.InputSource") (它往往会被直接实例化) 和处理句柄类也可以从 `xml.sax` 获得。 这些接口的描述见下文。

除了这些类，`xml.sax` 还提供了如下异常类。

_exception _xml.sax.SAXException( _msg_ , _exception =None_)¶
~~~
    

~~~
封装某个 XML 错误或警告。 这个类可以包含来自 XML 解析器或应用程序的基本错误或警告信息：它可以被子类化以提供额外的功能或是添加本地化信息。 请注意虽然在 [`ErrorHandler`](xml.sax.handler.md#xml.sax.handler.ErrorHandler "xml.sax.handler.ErrorHandler") 接口中定义的处理句柄可以接收该异常的实例，但是并不要求实际引发该异常 --- 它也可以被用作信息的容器。

当实例化时， _msg_ 应当是适合人类阅读的错误描述。 如果给出了可选的 _exception_ 形参，它应当为 `None` 或者解析代码所捕获的异常并会被作为信息传递出去。

这是其他 SAX 异常类的基类。

_exception _xml.sax.SAXParseException( _msg_ , _exception_ , _locator_ )¶
~~~
    

~~~
`SAXException` 的子类，针对解析错误引发。 这个类的实例会被传递给 SAX [`ErrorHandler`](xml.sax.handler.md#xml.sax.handler.ErrorHandler "xml.sax.handler.ErrorHandler") 接口的方法来提供关于解析错误的信息。 这个类支持 SAX [`Locator`](xml.sax.reader.md#xml.sax.xmlreader.Locator "xml.sax.xmlreader.Locator") 接口以及 `SAXException` 接口。

_exception _xml.sax.SAXNotRecognizedException( _msg_ , _exception =None_)¶
~~~
    

~~~
`SAXException` 的子类，当 SAX [`XMLReader`](xml.sax.reader.md#xml.sax.xmlreader.XMLReader "xml.sax.xmlreader.XMLReader") 遇到不可识别的特性或属性时引发。 SAX 应用程序和扩展可能会出于类似目的而使用这个类。

_exception _xml.sax.SAXNotSupportedException( _msg_ , _exception =None_)¶
~~~
    

~~~
`SAXException` 的子类，当 SAX [`XMLReader`](xml.sax.reader.md#xml.sax.xmlreader.XMLReader "xml.sax.xmlreader.XMLReader") 被要求启用某个不受支持的特性，或者将某个属性设为具体实现不支持的值时引发。 SAX 应用程序和扩展可能会出于类似目的而使用这个类。

参见

[SAX: The Simple API for XML](http://www.saxproject.org/)
~~~
    

~~~
这个网站是 SAX API 定义的焦点。 它提供了一个 Java 实现以及在线文档。 还包括其他实现的链接和历史信息。

[`xml.sax.handler`](xml.sax.handler.md#module-xml.sax.handler "xml.sax.handler: Base classes for SAX event handlers.") 模块
~~~
    

~~~
应用程序所提供对象的接口定义。

[`xml.sax.saxutils`](xml.sax.utils.md#module-xml.sax.saxutils "xml.sax.saxutils: Convenience functions and classes for use with SAX.") 模块
~~~
    

~~~
可在 SAX 应用程序中使用的便捷函数。

[`xml.sax.xmlreader`](xml.sax.reader.md#module-xml.sax.xmlreader "xml.sax.xmlreader: Interface which SAX-compliant XML parsers must implement.") 模块
~~~
    

~~~
解析器所提供对象的接口定义。

## SAXException 对象¶

`SAXException` 异常类支持下列方法:

SAXException.getMessage()¶
~~~
    

~~~
返回描述错误条件的适合人类阅读的消息。

SAXException.getException()¶
~~~
    

~~~
