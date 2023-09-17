# `xml.sax.handler` \--- SAX 处理句柄的基类¶

**源代码:** [Lib/xml/sax/handler.py](https://github.com/python/cpython/tree/3.12/Lib/xml/sax/handler.py)

* * *

SAX API 定义了五种处理句柄：内容句柄、DTD 句柄、错误句柄、实体解析器以及词法句柄。 应用程序通常只需要实现他们感兴趣的事件对应的接口；他们可以在单个对象或多个对象中实现这些接口。 处理句柄的实现应当继承自 `xml.sax.handler` 模块所提供的基类，以便所有方法都能获得默认的实现。

_class _xml.sax.handler.ContentHandler¶

    

~~~
这是 SAX 中的主回调接口，也是对应用程序来说最重要的一个接口。 此接口中事件的顺序反映了文档中信息的顺序。

_class _xml.sax.handler.DTDHandler¶
~~~
    

~~~
处理 DTD 事件。

这个接口仅指定了基本解析（未解析的实体和属性）所需的那些 DTD 事件。

_class _xml.sax.handler.EntityResolver¶
~~~
    

~~~
用于解析实体的基本接口。 如果你创建了实现此接口的对象，然后用你的解析器注册该对象，该解析器将调用你的对象中的方法来解析所有外部实体。

_class _xml.sax.handler.ErrorHandler¶
~~~
    

~~~
解析器用来向应用程序表示错误和警告的接口。 这个对象的方法控制错误是要立即转换为异常还是以某种其他该来来处理。

_class _xml.sax.handler.LexicalHandler¶
~~~
    

~~~
解析器用来代表低频度事件的接口，这些事件可能是许多应用程序都不感兴趣的。

除了这些类，`xml.sax.handler` 还提供了表示特性和属性名称的符号常量。

xml.sax.handler.feature_namespaces¶
~~~
    

~~~
值: `"http://xml.org/sax/features/namespaces"`

true: 执行命名空间处理。

false: 可选择不执行命名空间处理 (这意味着 namespace-prefixes; default)。

access: (解析) 只读; (不解析) 读/写

xml.sax.handler.feature_namespace_prefixes¶
~~~
    

~~~
值: `"http://xml.org/sax/features/namespace-prefixes"`

true: 报告原始的带前缀名称和用于命名空间声明的属性。

false: 不报告用于命名空间声明的属性，可选择不报告原始的带前缀名称（默认）。

access: (解析) 只读; (不解析) 读/写

xml.sax.handler.feature_string_interning¶
~~~
    

~~~
值: `"http://xml.org/sax/features/string-interning"`

true: 所有元素名称、前缀、属性名称、命名空间 URI 以及本地名称都使用内置的 intern 函数进行内化。

false: 名称不要求被内化，但也可以被内化（默认）。

access: (解析) 只读; (不解析) 读/写

xml.sax.handler.feature_validation¶
~~~
    

~~~
值: `"http://xml.org/sax/features/validation"`

true: 报告所有的验证错误（包括 external-general-entities 和 external-parameter-entities）。

false: 不报告验证错误。

access: (解析) 只读; (不解析) 读/写

xml.sax.handler.feature_external_ges¶
~~~
    

~~~
值: `"http://xml.org/sax/features/external-general-entities"`

true: 包括所有的外部通用（文本）实体。

false: 不包括外部通用实体。

access: (解析) 只读; (不解析) 读/写

xml.sax.handler.feature_external_pes¶
~~~
    

~~~
值: `"http://xml.org/sax/features/external-parameter-entities"`

true: 包括所有的外部参数实体，也包括外部 DTD 子集。

false: 不包括任何外部参数实体，也不包括外部 DTD 子集。

access: (解析) 只读; (不解析) 读/写

xml.sax.handler.all_features¶
~~~
    

~~~
全部特性列表。

xml.sax.handler.property_lexical_handler¶
~~~
    

~~~
值: `"http://xml.org/sax/properties/lexical-handler"`

数据类型: xml.sax.handler.LexicalHandler (在 Python 2 中不受支持)

描述: 可选的扩展处理句柄，用于注释等词法事件。

访问: 读/写

xml.sax.handler.property_declaration_handler¶
~~~
    

~~~
值: `"http://xml.org/sax/properties/declaration-handler"`

数据类型: xml.sax.sax2lib.DeclHandler (在 Python 2 中不受支持)

描述: 可选的扩展处理句柄，用于标注和未解析实体以外的 DTD 相关事件。

访问: 读/写

xml.sax.handler.property_dom_node¶
~~~
    

~~~
值: `"http://xml.org/sax/properties/dom-node"`

数据类型: org.w3c.dom.Node (在 Python 2 中不受支持)

描述: 在解析时，如果这是一个 DOM 迭代器则为当前被访问的 DOM 节点；不在解析时，则将根 DOM 节点用于迭代。

access: (解析) 只读; (不解析) 读/写

xml.sax.handler.property_xml_string¶
~~~
    

~~~
值: `"http://xml.org/sax/properties/xml-string"`

数据类型: Bytes

描述: 作为当前事件来源的字符串字面值。

访问: 只读

xml.sax.handler.all_properties¶
~~~
    

~~~
已知属性名称列表。

## ContentHandler 对象¶

用户应当子类化 `ContentHandler` 来支持他们的应用程序。 以下方法会由解析器在输入文档的适当事件上调用:

ContentHandler.setDocumentLocator( _locator_ )¶
~~~
    

~~~
由解析器调用来给予应用程序一个定位器以确定文档事件来自何处。

强烈建议（虽然不是绝对的要求） SAX 解析器提供一个定位器：如果提供的话，它必须在发起调用 DocumentHandler 接口的任何其他方法之前通过发起调用此方法来提供定位器。

定位器允许应用程序确定任何文档相关事件的结束位置，即使解析器没有报告错误。 通常，应用程序将使用这些信息来报告它自己的错误（例如未匹配到应用程序业务规则的字符内容）。 定位器所返回的信息可能不足以与搜索引擎配合使用。

请注意定位器只有在发起调用此接口中的事件时才会返回正确的信息。 应用程序不应试图在其他任何时刻使用它。

ContentHandler.startDocument()¶
~~~
    

~~~
接收一个文档开始的通知。

SAX 解析器将只发起调用这个方法一次，并且会在调用这个接口或 DTDHandler 中的任何其他方法之前 (`setDocumentLocator()` 除外)。

ContentHandler.endDocument()¶
~~~
    

~~~
接收一个文档结束的通知。

SAX 解析器将只发起调用这个方法一次，并且它将是在解析过程中最后发起调用的方法。 解析器在（因不可恢复的错误）放弃解析或到达输入的终点之前不应发起调用这个方法。

ContentHandler.startPrefixMapping( _prefix_ , _uri_ )¶
~~~
    

~~~
开始一个前缀 URI 命名空间映射的范围。

来自此事件的信息对于一般命名空间处理来说是不必要的：当 `feature_namespaces` 特性被启用时（默认）SAX XML 读取器将自动为元素和属性名称替换前缀。

但是也存在一些情况，当应用程序需要在字符数据或属性值中使用前缀，而它们无法被安全地自动扩展；`startPrefixMapping()` 和 `endPrefixMapping()` 事件会向应用程序提供信息以便在这些上下文内部扩展前缀，如果有必要的话。

请注意 `startPrefixMapping()` 和 `endPrefixMapping()` 事件并不保证能够相对彼此被正确地嵌套：所有 `startPrefixMapping()` 事件都将在对应的 `startElement()` 事件之前发生，而所有 `endPrefixMapping()` 事件都将在对应的 `endElement()` 事件之后发生，但它们的并不保证一致。

ContentHandler.endPrefixMapping( _prefix_ )¶
~~~
    

~~~
结束一个前缀 URI 映射的范围。

请参看 `startPrefixMapping()` 了解详情。 此事件将总是会在对应的 `endElement()` 事件之后发生，但 `endPrefixMapping()` 事件的顺序则并没有保证。

ContentHandler.startElement( _name_ , _attrs_ )¶
~~~
    

~~~
在非命令空间模式下指示一个元素的开始。

_name_ 形参包含字符串形式的元素类型原始 XML 1.0 名称而 _attrs_ 形参存放包含元素属性的 `Attributes` 接口对象 (参见 [Attributes 接口](xml.sax.reader.md#attributes-objects))。 作为 _attrs_ 传入的对象可能被解析器所重用；维持一个对它的引用不是保持属性副本的可靠方式。 要保持这些属性的一个副本，请使用 _attrs_ 对象的 [`copy()`](copy.md#module-copy "copy: Shallow and deep copy operations.") 方法。

ContentHandler.endElement( _name_ )¶
~~~
    

~~~
在非命名空间模式下指示一个元素的结束。

_name_ 形参包含元素类型的名称，与 `startElement()` 事件的一样。

ContentHandler.startElementNS( _name_ , _qname_ , _attrs_ )¶
~~~
    

~~~
在命名空间模式下指示一个元素的开始。

_name_ 形参包含以 `(uri, localname)` 元组表示的元素类型名称， _qname_ 形参包含源文档中使用的原始 XML 1.0 名称，而 _attrs_ 形参存放包含元素属性的 `AttributesNS` 接口实例 (参见 [AttributesNS 接口](xml.sax.reader.md#attributes-ns-objects))。 如果没有命名空间被关联到元素，则 _name_ 的 _uri_ 部分将为 `None`。 作为 _attrs_ 传入的对象可能被解析器所重用；维持一个对它的引用不是保持属性副本的可靠方式。 要保持这些属性的一个副本，请使用 _attrs_ 对象的 [`copy()`](copy.md#module-copy "copy: Shallow and deep copy operations.") 方法。

解析器可将 _qname_ 形参设为 `None`，除非 `feature_namespace_prefixes` 特性已被激活。

ContentHandler.endElementNS( _name_ , _qname_ )¶
~~~
    

~~~
在命名空间模式下指示一个元素的结束。

_name_ 形参包含元素类型的名称，与 `startElementNS()` 方法的一样， _qname_ 形参也是类似的。

ContentHandler.characters( _content_ )¶
~~~
    

~~~
接收字符数据的通知。

解析器将调用此方法来报告每一个字符数据分块。 SAX 解析器可以将所有连续字符数据返回为一个单独分块，或者将其拆成几个分块；但是，在任意单个事件中的所有字符都必须来自同一个外部实体以便定位器提供有用的信息。

_content_ 可以是一个字符串或字节串实例；`expat` 读取器模块总是会产生字符串。

备注

Python XML 特别关注小组所提供的早期 SAX 1 接口针对此方法使用了一个更类似于 Java 的接口。 由于 Python 所使用的大多数解析器都没有利用老式的接口，因而选择了更简单的签名来替代它。 要将旧代码转换为新接口，请使用 _content_ 而不要通过旧的 _offset_ 和 _length_ 形参来对内容进行切片。

ContentHandler.ignorableWhitespace( _whitespace_ )¶
~~~
    

~~~
接收元素内容中可忽略空白符的通知。

验证解析器必须使用此方法来报告每个可忽略的空白符分块（参见 W3C XML 1.0 建议第 2.10 节）：非验证解析器如果能够解析并使用内容模型的话也可以使用此方法。

SAX 解析器可以将所有连续字符数据返回为一个单独分块，或者将其拆成几个分块；但是，在任意单个事件中的所有字符都必须来自同一个外部实体以便定位器提供有用的信息。

ContentHandler.processingInstruction( _target_ , _data_ )¶
~~~
    

~~~
接受一条处理指令的通知。

解析器将为已找到的每条处理指令发起调用该方法一次：请注意处理指令可能出现在主文档元素之前或之后。

SAX 解析器绝不应当使用此方法来报告 XML 声明（XML 1.0 第 2.8 节）或文本声明（XML 1.0 第 4.3.1 节）。

ContentHandler.skippedEntity( _name_ )¶
~~~
    

~~~
接收一个已跳过实体的通知。

解析器将为每个已跳过实体发起调用此方法一次。 非验证处理程序可能会跳过未看到声明的实体（例如，由于实体是在一个外部because, for example, the entity was declared in an external DTD 子集中声明的）。 所有处理程序都可以跳过外部实体，具体取决于 `feature_external_ges` 和 `feature_external_pes` 属性的值。

## DTDHandler 对象¶

`DTDHandler` 实例提供了下列方法:

DTDHandler.notationDecl( _name_ , _publicId_ , _systemId_ )¶
~~~
    

~~~
处理标注声明事件。

DTDHandler.unparsedEntityDecl( _name_ , _publicId_ , _systemId_ , _ndata_ )¶
~~~
    

~~~
处理未解析的实体声明事件。

## EntityResolver 对象¶

EntityResolver.resolveEntity( _publicId_ , _systemId_ )¶
~~~
    

~~~
求解一个实体的系统标识符并返回一个字符串形式的系统标识符作为读取源，或是一个 InputSource 作为读取源。 默认的实现会返回 _systemId_ 。

## ErrorHandler 对象¶

带有这个接口的对象被用于接收来自 [`XMLReader`](xml.sax.reader.md#xml.sax.xmlreader.XMLReader "xml.sax.xmlreader.XMLReader") 的错误和警告信息。 如果你创建了一个实现此接口的对象，然后用你的 [`XMLReader`](xml.sax.reader.md#xml.sax.xmlreader.XMLReader "xml.sax.xmlreader.XMLReader") 注册这个对象，则解析器将调用你的对象中的这个方法来报告所有的警告和错误。 有三个可用的错误级别：警告、（或许）可恢复的错误和不可恢复的错误。 所有方法都接受 [`SAXParseException`](xml.sax.md#xml.sax.SAXParseException "xml.sax.SAXParseException") 作为唯一的形参。 错误和警告可以通过引发所传入的异常对象来转换为异常。

ErrorHandler.error( _exception_ )¶
~~~
    

~~~
当解析器遇到一个可恢复的错误时调用。 如果此方法没有引发异常，则解析可能会继续，但是应用程序不能预期获得更多的文档信息。 允许解析器继续可能会允许在输入文档中发现额外的错误。

ErrorHandler.fatalError( _exception_ )¶
~~~
    

~~~
当解析器遇到一个不可恢复的错误时调用；在此方法返回时解析应当终止。

ErrorHandler.warning( _exception_ )¶
~~~
    

~~~
当解析器向应用程序提供次要警告信息时调用。 在此方法返回时解析应当继续，并且文档信息将继续被传递给应用程序。 在此方法中引发异常将导致解析结束。

## LexicalHandler 对象¶

可选的词法事件 SAX2 处理句柄。

这个处理句柄被用来获取一个 XML 文档的相关词法信息。 词法信息包括描述所使用的文档编码格式和嵌入文档中的 XML 注释，以及 DTD 和任何 CDATA 部分的节边界。 词法处理句柄的使用方式与内容处理句柄相同。

通过使用带有属性标识符 `'http://xml.org/sax/properties/lexical-handler'` 的 setProperty 方法来设置一个 XMLReader 的 LexicalHandler。

LexicalHandler.comment( _content_ )¶
~~~
    

~~~
报告在文档中任何地方（包括 DTD 和文档元素以外）的注释。

LexicalHandler.startDTD( _name_ , _public_id_ , _system_id_ )¶
~~~
    

~~~
如果文档有关联的 DTD 则报告 DTD 声明的开始。

LexicalHandler.endDTD()¶
~~~
    

~~~
报告 DTD 声明的结束。

LexicalHandler.startCDATA()¶
~~~
    

~~~
报告 CDATA 标记部分的开始。

CDATA 标记部分的内容将通过字符处理句柄来报告。

LexicalHandler.endCDATA()¶
~~~
    

~~~
