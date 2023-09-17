# `plistlib` \--- 生成与解析 Apple `.plist` 文件¶

**源代码:** [Lib/plistlib.py](https://github.com/python/cpython/tree/3.12/Lib/plistlib.py)

* * *

此模块提供了可读写 Apple "property list" 文件的接口，它主要用于 macOS 和 iOS 系统。 此模块同时支持二进制和 XML plist 文件。

property list (`.plist`) 文件格式是一种简单的序列化格式，它支持一些基本对象类型，例如字典、列表、数字和字符串等。 通常使用一个字典作为最高层级对象。

要写入和解析 plist 文件，请使用 `dump()` 和 `load()` 函数。

要以字节串对象形式操作 plist 数据，请使用 `dumps()` 和 `loads()`。

值可以为字符串、整数、浮点数、布尔值、元组、列表、字典（但只允许用字符串作为键）、[`bytes`](stdtypes.md#bytes "bytes")、[`bytearray`](stdtypes.md#bytearray "bytearray") 或 [`datetime.datetime`](datetime.md#datetime.datetime "datetime.datetime") 对象。

在 3.4 版本发生变更: 新版 API，旧版 API 已被弃用。 添加了对二进制 plist 格式的支持。

在 3.8 版本发生变更: 添加了在二进制 plist 中读写 `UID` 令牌的支持，例如用于 NSKeyedArchiver 和 NSKeyedUnarchiver。

在 3.9 版本发生变更: 旧 API 已被移除。

参见

[PList 指南页面](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/PropertyLists/)

    

~~~
针对该文件格式的 Apple 文档。

这个模块定义了以下函数：

plistlib.load( _fp_ , _*_ , _fmt =None_, _dict_type =dict_)¶
~~~
    

~~~
读取 plist 文件。 _fp_ 应当可读并且为二进制文件对象。 返回已解包的根对象（通常是一个字典）。

_fmt_ 为文件的格式，有效的值如下:

  * [`None`](constants.md#None "None"): 自动检测文件格式

  * `FMT_XML`: XML 文件格式

  * `FMT_BINARY`: 二进制 plist 格式

_dict_type_ 为字典用来从 plist 文件读取的类型。

`FMT_XML` 格式的 XML 数据 会使用来自 [`xml.parsers.expat`](pyexpat.md#module-xml.parsers.expat "xml.parsers.expat: An interface to the Expat non-validating XML parser.") 的 Expat 解析器 -- 请参阅其文档了解错误格式 XML 可能引发的异常。 未知元素将被 plist 解析器直接略过。

当文件无法被解析时二进制格式的解析器将引发 `InvalidFileException`。

在 3.4 版本加入.

plistlib.loads( _data_ , _*_ , _fmt =None_, _dict_type =dict_)¶
~~~
    

~~~
从一个 bytes 对象加载 plist。 参阅 `load()` 获取相应关键字参数的说明。

在 3.4 版本加入.

plistlib.dump( _value_ , _fp_ , _*_ , _fmt =FMT_XML_, _sort_keys =True_, _skipkeys =False_)¶
~~~
    

~~~
将 _value_ 写入 plist 文件。 _Fp_ 应当可写并且为二进制文件对象。

_fmt_ 参数指定 plist 文件的格式，可以是以下值之一:

  * `FMT_XML`: XML 格式的 plist 文件

  * `FMT_BINARY`: 二进制格式的 plist 文件

当 _sort_keys_ 为真值（默认）时字典的键将经过排序再写入 plist，否则将按字典的迭代顺序写入。

当 _skipkeys_ 为假值（默认）时该函数将在字典的键不为字符串时引发 [`TypeError`](exceptions.md#TypeError "TypeError")，否则将跳过这样的键。

如果对象是不受支持的类型或者是包含不受支持类型的对象的容器则将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

对于无法在（二进制）plist 文件中表示的整数值，将会引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError")。

在 3.4 版本加入.

plistlib.dumps( _value_ , _*_ , _fmt =FMT_XML_, _sort_keys =True_, _skipkeys =False_)¶
~~~
    

~~~
将 _value_ 以 plist 格式字节串对象的形式返回。 参阅 `dump()` 的文档获取此函数的关键字参数的说明。

在 3.4 版本加入.

可以使用以下的类:

_class _plistlib.UID( _data_ )¶
~~~
    

~~~
包装一个 [`int`](functions.md#int "int")。 该类将在读取或写入 NSKeyedArchiver 编码的数据时被使用，其中包含 UID（参见 PList 指南）。

它具有一个属性 `data`，可以被用来提取 UID 的 int 值。 `data` 的取值范围必须为 `0 <= data < 2**64`。

在 3.8 版本加入.

可以使用以下的常量:

plistlib.FMT_XML¶
~~~
    

~~~
用于 plist 文件的 XML 格式。

在 3.4 版本加入.

plistlib.FMT_BINARY¶
~~~
    

~~~
用于 plist 文件的二进制格式。

在 3.4 版本加入.

## 例子¶

生成一个 plist:
~~~
    
    
~~~
import datetime
import plistlib

pl = dict(
    aString = "Doodah",
    aList = ["A", "B", 12, 32.1, [1, 2, 3]],
    aFloat = 0.1,
    anInt = 728,
    aDict = dict(
        anotherString = "<hello & hi there!>",
        aThirdString = "M\xe4ssig, Ma\xdf",
        aTrueValue = True,
        aFalseValue = False,
    ),
    someData = b"<binary gunk>",
    someMoreData = b"<lots of binary gunk>" * 10,
    aDate = datetime.datetime.now()
)
print(plistlib.dumps(pl).decode())
~~~

解析一个 plist:

    
    
~~~
import plistlib

plist = b"""<plist version="1.0">
<dict>
    <key>foo</key>
    <string>bar</string>
</dict>
</plist>"""
pl = plistlib.loads(plist)
print(pl["foo"])
~~~

