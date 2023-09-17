# `xml.sax.saxutils` \--- SAX 工具集¶

**源代码:** [Lib/xml/sax/saxutils.py](https://github.com/python/cpython/tree/3.12/Lib/xml/sax/saxutils.py)

* * *

`xml.sax.saxutils` 模块包含一些在创建 SAX 应用程序时十分有用的类和函数，它们可以被直接使用，或者是作为基类使用。

xml.sax.saxutils.escape( _data_ , _entities ={}_)¶

    

~~~
对数据字符串中的 `'&'`, `'<'` 和 `'>'` 进行转义。

你可以通过传入一个字典作为可选的 _entities_ 形参来对其他字符串数据进行转义。 字典的键和值必须为字符串；每个键将被替换为其所对应的值。 字符 `'&'`, `'<'` 和 `'>'` 总是会被转义，即使提供了 _entities_ 。

备注

此函数应当仅用于对无法直接在 XML 中使用的字符进行转义。 请不要将此函数用作通用的字符串转换函数。

xml.sax.saxutils.unescape( _data_ , _entities ={}_)¶
~~~
    

~~~
对字符串数据中的 `'&amp;'`, `'&lt;'` 和 `'&gt;'` 进行反转义。

你可以通过传入一个字典作为可选的 _entities_ 形参来对其他数据字符串进行转义。 字典的键和值必须都为字符串；每个键将被替换为所对应的值。 `'&amp'`, `'&lt;'` 和 `'&gt;'` 将总是保持不被转义，即使提供了 _entities_ 。

xml.sax.saxutils.quoteattr( _data_ , _entities ={}_)¶
~~~
    

~~~
类似于 `escape()`，但还会对 _data_ 进行处理以将其用作属性值。 返回值是 _data_ 加上任何额外要求的替换的带引号版本。`quoteattr()` 将基于 _data_ 的内容选择一个引号字符，以尽量避免在字符串中编码任何引号字符。 如果单双引号字符在 _data_ 中都存在，则双引号字符将被编码并且 _data_ 将使用双引号来标记。 结果字符串可被直接用作属性值:
~~~
    
    
~~~shell
>>> print("<element attr=%s>" % quoteattr("ab ' cd \" ef"))
<element attr="ab ' cd &quot; ef">
~~~

此函数适用于为 HTML 或任何使用引用实体语法的 SGML 生成属性值。

_class _xml.sax.saxutils.XMLGenerator( _out =None_, _encoding ='iso-8859-1'_, _short_empty_elements =False_)¶

    

~~~
这个类通过将 SAX 事件写回到 XML 文档来实现 [`ContentHandler`](xml.sax.handler.md#xml.sax.handler.ContentHandler "xml.sax.handler.ContentHandler") 接口。 换句话说，使用 `XMLGenerator` 作为内容处理程序将重新产生所解析的原始文档。 _out_ 应当为一个文件类对象，它默认将为 _sys.stdout_ 。 _encoding_ 为输出流的编码格式，它默认将为 `'iso-8859-1'`。 _short_empty_elements_ 控制不包含内容的元素的格式化：如为 `False` (默认值) 则它们会以开始/结束标记对的形式被发送，如果设为 `True` 则它们会以单个自结束标记的形式被发送。

在 3.2 版本加入: _short_empty_elements_ 形参。

_class _xml.sax.saxutils.XMLFilterBase( _base_ )¶
~~~
    

~~~
这个类被设计用来分隔 [`XMLReader`](xml.sax.reader.md#xml.sax.xmlreader.XMLReader "xml.sax.xmlreader.XMLReader") 和客户端应用的事件处理程序。 在默认情况下，它除了将请求传送给读取器并将事件传送给处理程序之外什么都不做，但其子类可以重载特定的方法以在传送它们的时候修改事件流或配置请求。

xml.sax.saxutils.prepare_input_source( _source_ , _base =''_)¶
~~~
    

~~~
