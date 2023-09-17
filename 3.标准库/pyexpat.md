# `xml.parsers.expat` \--- 使用 Expat 的快速 XML 解析¶

* * *

警告

`pyexpat` 模块对于恶意构建的数据是不安全的。 如果你需要解析不受信任或未经身份验证的数据，请参阅 [XML 漏洞](xml.md#xml-vulnerabilities)。

`xml.parsers.expat` 模块是针对 Expat 非验证 XML 解析器的 Python 接口。 此模块提供了一个扩展类型 `xmlparser`，它代表一个 XML 解析器的当前状态。 在创建一个 `xmlparser` 对象之后，该对象的各个属性可被设置为相应的处理句柄函数。 随后当将一个 XML 文档送入解析器时，就会为该 XML 文档中的字符数据和标记调用处理句柄函数。

此模块使用 `pyexpat` 模块来提供对 Expat 解析器的访问。 直接使用 `pyexpat` 模块的方式已被弃用。

此模块提供了一个异常和一个类型对象:

_exception _xml.parsers.expat.ExpatError¶

    

~~~
此异常会在 Expat 报错时被引发。 请参阅 ExpatError 异常 一节了解有关解读 Expat 错误的更多信息。

_exception _xml.parsers.expat.error¶
~~~
    

~~~
`ExpatError` 的别名。

xml.parsers.expat.XMLParserType¶
~~~
    

~~~
来自 `ParserCreate()` 函数的返回值的类型。

`xml.parsers.expat` 模块包含两个函数:

xml.parsers.expat.ErrorString( _errno_ )¶
~~~
    

~~~
返回给定错误号 _errno_ 的解释性字符串。

xml.parsers.expat.ParserCreate( _encoding =None_, _namespace_separator =None_)¶
~~~
    

~~~
创建并返回一个新的 `xmlparser` 对象。 如果指定了 _encoding_ ，它必须为指定 XML 数据所使用的编码格式名称的字符串。 Expat 支持的编码格式没有 Python 那样多，而且它的编码格式库也不能被扩展；它支持 UTF-8, UTF-16, ISO-8859-1 (Latin1) 和 ASCII。 如果给出了 _encoding_ [1] 则它将覆盖隐式或显式指定的文档编码格式。

可以选择让 Expat 为你做 XML 命名空间处理，这是通过提供 _namespace_separator_ 值来启用的。 该值必须是一个单字符的字符串；如果字符串的长度不合法则将引发 [`ValueError`](exceptions.md#ValueError "ValueError") (`None` 被视为等同于省略)。 当命名空间处理被启用时，属于特定命名空间的元素类型名称和属性名称将被展开。 传递给The element name passed to the 元素处理句柄 `StartElementHandler` 和 `EndElementHandler` 的元素名称将为命名空间 URI，命名空间分隔符和名称的本地部分的拼接。 如果命名空间分隔符是一个零字节 (`chr(0)`) 则命名空间 URI 和本地部分将被直接拼接而不带任何分隔符。

举例来说，如果 _namespace_separator_ 被设为空格符 (`' '`) 并对以下文档进行解析:
~~~
    
    
~~~
<?xml version="1.0"?>
<root xmlns    = "http://default-namespace.org/"
      xmlns:py = "http://www.python.org/ns/">
  <py:elem1 />
  <elem2 xmlns="" />
</root>
~~~

`StartElementHandler` 将为每个元素获取以下字符串:

    
    
~~~
http://default-namespace.org/ root
http://www.python.org/ns/ elem1
elem2
~~~

由于 `pyexpat` 所使用的 `Expat` 库的限制，被返回的 `xmlparser` 实例只能被用来解析单个 XML 文档。 请为每个文档调用 `ParserCreate` 来提供单独的解析器实例。

参见

[The Expat XML Parser](http://www.libexpat.org/)

    

~~~
Expat 项目的主页。

## XMLParser对象¶

`xmlparser` 对象具有以下方法:

xmlparser.Parse( _data_ [, _isfinal_ ])¶
~~~
    

~~~
解析字符串 _data_ 的内容，调用适当的处理函数来处理解析后的数据。 在对此方法的最后一次调用时 _isfinal_ 必须为真值；它允许以片段形式解析单个文件，而不是提交多个文件。 _data_ 在任何时候都可以为空字符串。

xmlparser.ParseFile( _file_ )¶
~~~
    

~~~
解析从对象 _file_ 读取的 XML 数据。 _file_ 仅需提供 `read(nbytes)` 方法，当没有更多数据可读时将返回空字符串。

xmlparser.SetBase( _base_ )¶
~~~
    

~~~
设置要用于解析声明中的系统标识符的相对 URI 的基准。 解析相对标识符的任务会留给应用程序进行：这个值将作为 _base_ 参数传递给 `ExternalEntityRefHandler()`, `NotationDeclHandler()` 和 `UnparsedEntityDeclHandler()` 函数。

xmlparser.GetBase()¶
~~~
    

~~~
返回包含之前调用 `SetBase()` 所设置的基准位置的字符串，或者如果未调用 `SetBase()` 则返回 `None`。

xmlparser.GetInputContext()¶
~~~
    

~~~
将生成当前事件的输入数据以字符串形式返回。 数据为包含文本的实体的编码格式。 如果被调用时未激活事件处理句柄，则返回值将为 `None`。

xmlparser.ExternalEntityParserCreate( _context_ [, _encoding_ ])¶
~~~
    

~~~
创建一个“子”解析器，可被用来解析由父解析器解析的内容所引用的外部解析实体。 _context_ 形参应当是传递给 `ExternalEntityRefHandler()` 处理函数的字符串，具体如下所述。 子解析器创建时 `ordered_attributes` 和 `specified_attributes` 会被设为此解析器的值。

xmlparser.SetParamEntityParsing( _flag_ )¶
~~~
    

~~~
控制参数实体（包括外部 DTD 子集）的解析。 可能的 _flag_ 值有 `XML_PARAM_ENTITY_PARSING_NEVER`, `XML_PARAM_ENTITY_PARSING_UNLESS_STANDALONE` 和 `XML_PARAM_ENTITY_PARSING_ALWAYS`。 如果该旗标设置成功则返回真值。

xmlparser.UseForeignDTD([ _flag_ ])¶
~~~
    

~~~
调用时将 _flag_ 设为真值（默认）将导致 Expat 调用 `ExternalEntityRefHandler` 时将所有参数设为 [`None`](constants.md#None "None") 以允许加载替代的 DTD。 如果文档不包含文档类型声明，`ExternalEntityRefHandler` 仍然会被调用，但 `StartDoctypeDeclHandler` 和 `EndDoctypeDeclHandler` 将不会被调用。

为 _flag_ 传入假值将将撤消之前传入真值的调用，除此之外没有其他影响。

此方法只能在调用 `Parse()` 或 `ParseFile()` 方法之前被调用；在已调用过这两个方法之后调用它会导致引发 `ExpatError` 且 [`code`](code.md#module-code "code: Facilities to implement read-eval-print loops.") 属性被设为 `errors.codes[errors.XML_ERROR_CANT_CHANGE_FEATURE_ONCE_PARSING]`。

`xmlparser` 对象具有下列属性:

xmlparser.buffer_size¶
~~~
    

~~~
当 `buffer_text` 为真值时所使用的缓冲区大小。 可以通过将此属性赋一个新的整数值来设置一个新的缓冲区大小。 当大小发生改变时，缓冲区将被刷新。

xmlparser.buffer_text¶
~~~
    

~~~
将此属性设为真值会使得 `xmlparser` 对象缓冲 Expat 所返回的文本内容以尽可能地避免多次调用 `CharacterDataHandler()` 回调。 这可以显著地提升性能，因为 Expat 通常会将字符数据在每个行结束的位置上进行分块。 此属性默认为假值，但可以在任何时候被更改。

xmlparser.buffer_used¶
~~~
    

~~~
当 `buffer_text` 被启用时，缓冲区中存储的字节数。 这些字节数据表示以 UTF-8 编码的文本。 当 `buffer_text` 为假值时此属性没有任何实际意义。

xmlparser.ordered_attributes¶
~~~
    

~~~
将该属性设为非零整数会使得各个属性被报告为列表而非字典。 各个属性会按照在文档文本中的出现顺序显示。 对于每个属性，将显示两个列表条目：属性名和属性值。 （该模块的较旧版本也使用了此格式。） 默认情况下，该属性为假值；它可以在任何时候被更改。

xmlparser.specified_attributes¶
~~~
    

~~~
如果设为非零整数，解析器将只报告在文档实例中指明的属性而不报告来自属性声明的属性。 设置此属性的应用程序需要特别小心地使用从声明中获得的附加信息以符合 XML 处理程序的行为标准。 默认情况下，该属性为假值；它可以在任何时候被更改。

下列属性包含与 `xmlparser` 对象遇到的最近发生的错误有关联的值，并且一旦对 `Parse()` 或 `ParseFile()` 的调用引发了 `xml.parsers.expat.ExpatError` 异常就将只包含正确的值。

xmlparser.ErrorByteIndex¶
~~~
    

~~~
错误发生位置的字节索引号。

xmlparser.ErrorCode¶
~~~
    

~~~
指明问题的的数字代码。 该值可被传给 `ErrorString()` 函数，或是与在 `errors` 对象中定义的常量之一进行比较。

xmlparser.ErrorColumnNumber¶
~~~
    

~~~
错误发生位置的列号。

xmlparser.ErrorLineNumber¶
~~~
    

~~~
错误发生位置的行号。

下列属性包含 `xmlparser` 对象中关联到当前解析位置的值。 在回调报告解析事件期间它们将指示生成事件的字符序列的第一个字符的位置。 当在回调的外部被调用时，所指示的位置将恰好位于最后的解析事件之后（无论是否存在关联的回调）。

xmlparser.CurrentByteIndex¶
~~~
    

~~~
解析器输入的当前字节索引号。

xmlparser.CurrentColumnNumber¶
~~~
    

~~~
解析器输入的当前列号。

xmlparser.CurrentLineNumber¶
~~~
    

~~~
解析器输入的当前行号。

可被设置的处理句柄列表。 要在一个 `xmlparser` 对象 _o_ 上设置处理句柄，请使用 `o.handlername = func`。 _handlername_ 必须从下面的列表中获取，而 _func_ 必须为接受正确数量参数的可调用对象。 所有参数均为字符串，除非另外指明。

xmlparser.XmlDeclHandler( _version_ , _encoding_ , _standalone_ )¶
~~~
    

~~~
当解析 XML 声明时被调用。 XML 声明是 XML 建议适用版本、文档文本的编码格式，以及可选的“独立”声明的（可选）声明。 _version_ 和 _encoding_ 将为字符串，而 _standalone_ 在文档被声明为独立时将为 `1`，在文档被声明为非独立时将为 `0`，或者在 standalone 短语被省略时则为 `-1`。 这仅适用于 Expat 的 1.95.0 或更新版本。

xmlparser.StartDoctypeDeclHandler( _doctypeName_ , _systemId_ , _publicId_ , _has_internal_subset_ )¶
~~~
    

~~~
当 Expat 开始解析文档类型声明 (`<!DOCTYPE ...`) 时被调用。 _doctypeName_ 会完全按所显示的被提供。 _systemId_ 和 _publicId_ 形参给出所指定的系统和公有标识符，如果被省略则为 `None`。 如果文档包含内部文档声明子集则 _has_internal_subset_ 将为真值。 这要求 Expat 1.2 或更新的版本。

xmlparser.EndDoctypeDeclHandler()¶
~~~
    

~~~
当 Expat 完成解析文档类型声明时被调用。 这要求 Expat 1.2 或更新版本。

xmlparser.ElementDeclHandler( _name_ , _model_ )¶
~~~
    

~~~
为每个元素类型声明调用一次。 _name_ 为元素类型名称，而 _model_ 为内容模型的表示形式。

xmlparser.AttlistDeclHandler( _elname_ , _attname_ , _type_ , _default_ , _required_ )¶
~~~
    

~~~
为一个元素类型的每个已声明属性执行调用。 如果一个属性列表声明声明了三个属性，这个处理句柄会被调用三次，每个属性一次。 _elname_ 是声明所适用的元素的名称而 _attname_ 是已声明的属性的名称。 属性类型是作为 _type_ 传入的字符串；可能的值有 `'CDATA'`, `'ID'`, `'IDREF'`, ... _default_ 给出了当属性未被文档实例所指明时该属性的默认值，或是为 `None`，如果没有默认值 (`#IMPLIED` 值) 的话。 如果属性必须在文档实例中给出，则 _required_ 将为真值。 这要求 Expat 1.95.0 或更新的版本。

xmlparser.StartElementHandler( _name_ , _attributes_ )¶
~~~
    

~~~
在每个元素开始时调用。 _name_ 是包含元素名称的字符串，而 _attributes_ 是元素的属性。 如果 `ordered_attributes` 为真值，则属性为列表形式 (完整描述参见 `ordered_attributes`)。 否则为将名称映射到值的字典。

xmlparser.EndElementHandler( _name_ )¶
~~~
    

~~~
在每个元素结束时调用。

xmlparser.ProcessingInstructionHandler( _target_ , _data_ )¶
~~~
    

~~~
在每次处理指令时调用。

xmlparser.CharacterDataHandler( _data_ )¶
~~~
    

~~~
针对字符数据调用。 此方法将被用于普通字符数据、CDATA 标记内容以及可忽略的空白符。 需要区分这几种情况的应用程序可以使用 `StartCdataSectionHandler`, `EndCdataSectionHandler` 和 `ElementDeclHandler` 回调来收集必要的信息。

xmlparser.UnparsedEntityDeclHandler( _entityName_ , _base_ , _systemId_ , _publicId_ , _notationName_ )¶
~~~
    

~~~
针对未解析（NDATA）实体声明调用。 此方法仅存在于 Expat 库的 1. 2 版；对于更新的版本，请改用 `EntityDeclHandler`。 （下层 Expat 库中的对应函数已被声明为过时。）

xmlparser.EntityDeclHandler( _entityName_ , _is_parameter_entity_ , _value_ , _base_ , _systemId_ , _publicId_ , _notationName_ )¶
~~~
    

~~~
针对所有实体声明被调用。 对于形参和内部实体， _value_ 将为给出实体的声明内容的字符串；对于外部实体将为 `None`。 _notationName_ 形参对于已解析实体将为 `None`，对于未解析实体则为标注的名称。 如果实体为形参实体则 _is_parameter_entity_ 将为真值而如果为普通实体则为假值（大多数应用程序只需要关注普通实体）。 此方法仅从 1.95.0 版 Expat 库开始才可用。

xmlparser.NotationDeclHandler( _notationName_ , _base_ , _systemId_ , _publicId_ )¶
~~~
    

~~~
针对标注声明被调用。 _notationName_ , _base_ , _systemId_ 和 _publicId_ 如果给出则均应为字符串。 如果省略公有标识符，则 _publicId_ 将为 `None`。

xmlparser.StartNamespaceDeclHandler( _prefix_ , _uri_ )¶
~~~
    

~~~
当一个元素包含命名空间声明时被调用。 命名空间声明会在为声明所在的元素调用 `StartElementHandler` 之前被处理。

xmlparser.EndNamespaceDeclHandler( _prefix_ )¶
~~~
    

~~~
当到达包含命名空间声明的元素的关闭标记时被调用。 此方法会按照调用 `StartNamespaceDeclHandler` 以指明每个命名空间作用域的开始的逆顺序为元素上的每个命名空间声明调用一次。 对这个处理句柄的调用是在相应的 `EndElementHandler` 之后针对元素的结束而进行的。

xmlparser.CommentHandler( _data_ )¶
~~~
    

~~~
针对注释被调用。 _data_ 是注释的文本，不包括开头的 `'<!-``-'` 和末尾的 `'-``->'`。

xmlparser.StartCdataSectionHandler()¶
~~~
    

~~~
在一个 CDATA 节的开头被调用。 需要此方法和 `EndCdataSectionHandler` 以便能够标识 CDATA 节的语法开始和结束。

xmlparser.EndCdataSectionHandler()¶
~~~
    

~~~
在一个 CDATA 节的末尾被调用。

xmlparser.DefaultHandler( _data_ )¶
~~~
    

~~~
针对 XML 文档中没有指定适用处理句柄的任何字符被调用。 这包括了所有属于可被报告的结构的一部分，但未提供处理句柄的字符。

xmlparser.DefaultHandlerExpand( _data_ )¶
~~~
    

~~~
这与 `DefaultHandler()` 相同，但不会抑制内部实体的扩展。 实体引用将不会被传递给默认处理句柄。

xmlparser.NotStandaloneHandler()¶
~~~
    

~~~
当 XML 文档未被声明为独立文档时被调用。 这种情况发生在出现外部子集或对参数实体的引用，但 XML 声明没有在 XML 声明中将 standalone 设为 `yes` 的时候。 如果这个处理句柄返回 `0`，那么解析器将引发 `XML_ERROR_NOT_STANDALONE` 错误。 如果这个处理句柄没有被设置，那么解析器就不会为这个条件引发任何异常。

xmlparser.ExternalEntityRefHandler( _context_ , _base_ , _systemId_ , _publicId_ )¶
~~~
    

~~~
为对外部实体的引用执行调用。 _base_ 为当前的基准，由之前对 `SetBase()` 的调用设置。 公有和系统标识符 _systemId_ 和 _publicId_ 如果给出则圴为字符串；如果公有标识符未给出，则 _publicId_ 将为 `None`。 _context_ 是仅根据以下说明来使用的不透明值。

对于要解析的外部实体，这个处理句柄必须被实现。 它负责使用 `ExternalEntityParserCreate(context)` 来创建子解析器，通过适当的回调将其初始化，并对实体进行解析。 这个处理句柄应当返回一个整数；如果它返回 `0`，则解析器将引发 `XML_ERROR_EXTERNAL_ENTITY_HANDLING` 错误，否则解析将会继续。

如果未提供这个处理句柄，外部实体会由 `DefaultHandler` 回调来报告，如果提供了该回调的话。

## ExpatError 异常¶

`ExpatError` 异常包含几个有趣的属性:

ExpatError.code¶
~~~
    

~~~
Expat 对于指定错误的内部错误号。 `errors.messages` 字典会将这些错误号映射到 Expat 的错误消息。 例如:
~~~
    
    
~~~
from xml.parsers.expat import ParserCreate, ExpatError, errors

p = ParserCreate()
try:
    p.Parse(some_xml_document)
except ExpatError as err:
    print("Error:", errors.messages[err.code])
~~~

`errors` 模块也提供了一些错误消息常量和一个将这些消息映射回错误码的字典 `codes`，参见下文。

ExpatError.lineno¶

    

~~~
检测到错误所在的行号。 首行的行号为 `1`。

ExpatError.offset¶
~~~
    

~~~
错误发生在行中的字符偏移量。 首列的列号为 `0`。

## 示例¶

以下程序定义了三个处理句柄，会简单地打印出它们的参数。:
~~~
    
    
~~~
import xml.parsers.expat

# 3 handler functions
def start_element(name, attrs):
    print('Start element:', name, attrs)
def end_element(name):
    print('End element:', name)
def char_data(data):
    print('Character data:', repr(data))

p = xml.parsers.expat.ParserCreate()

p.StartElementHandler = start_element
p.EndElementHandler = end_element
p.CharacterDataHandler = char_data

p.Parse("""<?xml version="1.0"?>
<parent id="top"><child1 name="paul">Text goes here</child1>
<child2 name="fred">More text</child2>
</parent>""", 1)
~~~

来自这个程序的输出是:

    
    
~~~
Start element: parent {'id': 'top'}
Start element: child1 {'name': 'paul'}
Character data: 'Text goes here'
End element: child1
Character data: '\n'
Start element: child2 {'name': 'fred'}
Character data: 'More text'
End element: child2
Character data: '\n'
End element: parent
~~~

## 内容模型描述¶

内容模型是使用嵌套的元组来描述的。 每个元素包含四个值：类型、限定符、名称和一个子元组。 子元组就是附加的内容模型描述。

前两个字段的值是在 `xml.parsers.expat.model` 模块中定义的常量。 这些常量可分为两组：模型类型组和限定符组。

模型类型组中的常量有:

xml.parsers.expat.model.XML_CTYPE_ANY

    

~~~
模型名称所指定的元素被声明为具有 `ANY` 内容模型。

xml.parsers.expat.model.XML_CTYPE_CHOICE
~~~
    

~~~
命名元素允许从几个选项中选择；这被用于 `(A | B | C)` 形式的内容模型。

xml.parsers.expat.model.XML_CTYPE_EMPTY
~~~
    

~~~
被声明为 `EMPTY` 的元素具有此模型类型。

xml.parsers.expat.model.XML_CTYPE_MIXED
~~~
    

~~~
xml.parsers.expat.model.XML_CTYPE_NAME
~~~
    

~~~
xml.parsers.expat.model.XML_CTYPE_SEQ
~~~
    

~~~
代表彼此相连的一系列模型的模型用此模型类型来指明。 这被用于 `(A, B, C)` 形式的模型。

限定符组中的常量有:

xml.parsers.expat.model.XML_CQUANT_NONE
~~~
    

~~~
未给出限定符，这样它可以只出现一次，例如 `A`。

xml.parsers.expat.model.XML_CQUANT_OPT
~~~
    

~~~
模型是可选的：它可以出现一次或完全不出现，例如 `A?`。

xml.parsers.expat.model.XML_CQUANT_PLUS
~~~
    

~~~
模型必须出现一次或多次 (例如 `A+`)。

xml.parsers.expat.model.XML_CQUANT_REP
~~~
    

~~~
模型必须出现零次或多次，例如 `A*`。

## Expat 错误常量¶

下列常量是在 `xml.parsers.expat.errors` 模块中提供的。 这些常量在有错误发生时解读被引发的 `ExpatError` 异常对象的某些属性时很有用处。 出于保持向下兼容性的理由，这些常量的值是错误 _消息_ 而不是数字形式的错误 _代码_ ，为此你可以将它的 [`code`](code.md#module-code "code: Facilities to implement read-eval-print loops.") 属性和 `errors.codes[errors.XML_ERROR_ _CONSTANT_NAME_ ]` 进行比较。

`errors` 模块具有以下属性:

xml.parsers.expat.errors.codes¶
~~~
    

~~~
将字符串描述映射到其错误代码的字典。

在 3.2 版本加入.

xml.parsers.expat.errors.messages¶
~~~
    

~~~
将数字形式的错误代码映射到其字符串描述的字典。

在 3.2 版本加入.

xml.parsers.expat.errors.XML_ERROR_ASYNC_ENTITY¶
~~~
    

~~~
xml.parsers.expat.errors.XML_ERROR_ATTRIBUTE_EXTERNAL_ENTITY_REF¶
~~~
    

~~~
属性值中指向一个外部实体而非内部实体的实体引用。

xml.parsers.expat.errors.XML_ERROR_BAD_CHAR_REF¶
~~~
    

~~~
指向一个在 XML 不合法的字符的字符引用 (例如，字符 `0` 或 '`&#0;`')。

xml.parsers.expat.errors.XML_ERROR_BINARY_ENTITY_REF¶
~~~
    

~~~
指向一个使用标注声明，因而无法被解析的实体的实体引用。

xml.parsers.expat.errors.XML_ERROR_DUPLICATE_ATTRIBUTE¶
~~~
    

~~~
一个属性在一个开始标记中被使用超过一次。

xml.parsers.expat.errors.XML_ERROR_INCORRECT_ENCODING¶
~~~
    

~~~
xml.parsers.expat.errors.XML_ERROR_INVALID_TOKEN¶
~~~
    

~~~
当一个输入字节无法被正确分配给一个字符时引发；例如，在 UTF-8 输入流中的 NUL 字节 (值为 `0`)。

xml.parsers.expat.errors.XML_ERROR_JUNK_AFTER_DOC_ELEMENT¶
~~~
    

~~~
在文档元素之后出现空白符以外的内容。

xml.parsers.expat.errors.XML_ERROR_MISPLACED_XML_PI¶
~~~
    

~~~
在输入数据开始位置以外的地方发现 XML 声明。

xml.parsers.expat.errors.XML_ERROR_NO_ELEMENTS¶
~~~
    

~~~
文档不包含任何元素（XML 要求所有文档都包含恰好一个最高层级元素）。

xml.parsers.expat.errors.XML_ERROR_NO_MEMORY¶
~~~
    

~~~
Expat 无法在内部分配内存。

xml.parsers.expat.errors.XML_ERROR_PARAM_ENTITY_REF¶
~~~
    

~~~
在不被允许的位置发现一个参数实体引用。

xml.parsers.expat.errors.XML_ERROR_PARTIAL_CHAR¶
~~~
    

~~~
在输入中发出一个不完整的字符。

xml.parsers.expat.errors.XML_ERROR_RECURSIVE_ENTITY_REF¶
~~~
    

~~~
一个实体引用包含了对同一实体的另一个引用；可能是通过不同的名称，并可能是间接的引用。

xml.parsers.expat.errors.XML_ERROR_SYNTAX¶
~~~
    

~~~
遇到了某个未指明的语法错误。

xml.parsers.expat.errors.XML_ERROR_TAG_MISMATCH¶
~~~
    

~~~
一个结束标记不能匹配到最内层的未关闭开始标记。

xml.parsers.expat.errors.XML_ERROR_UNCLOSED_TOKEN¶
~~~
    

~~~
某些记号（例如开始标记）在流结束或遇到下一个记号之前还未关闭。

xml.parsers.expat.errors.XML_ERROR_UNDEFINED_ENTITY¶
~~~
    

~~~
对一个未定义的实体进行了引用。

xml.parsers.expat.errors.XML_ERROR_UNKNOWN_ENCODING¶
~~~
    

~~~
文档编码格式不被 Expat 所支持。

xml.parsers.expat.errors.XML_ERROR_UNCLOSED_CDATA_SECTION¶
~~~
    

~~~
一个 CDATA 标记节还未关闭。

xml.parsers.expat.errors.XML_ERROR_EXTERNAL_ENTITY_HANDLING¶
~~~
    

~~~
xml.parsers.expat.errors.XML_ERROR_NOT_STANDALONE¶
~~~
    

~~~
解析器确定文档不是“独立的”但它却在 XML 声明中声明自己是独立的，并且 `NotStandaloneHandler` 被设置为返回 `0`。

xml.parsers.expat.errors.XML_ERROR_UNEXPECTED_STATE¶
~~~
    

~~~
xml.parsers.expat.errors.XML_ERROR_ENTITY_DECLARED_IN_PE¶
~~~
    

~~~
xml.parsers.expat.errors.XML_ERROR_FEATURE_REQUIRES_XML_DTD¶
~~~
    

~~~
请求了一个需要已编译 DTD 支持的操作，但 Expat 被配置为不带 DTD 支持。 此错误应当绝对不会被 `xml.parsers.expat` 模块的标准构建版本所报告。

xml.parsers.expat.errors.XML_ERROR_CANT_CHANGE_FEATURE_ONCE_PARSING¶
~~~
    

~~~
在解析开始之后请求一个只能在解析开始之前执行的行为改变。 此错误（目前）只能由 `UseForeignDTD()` 所引发。

xml.parsers.expat.errors.XML_ERROR_UNBOUND_PREFIX¶
~~~
    

~~~
当命名空间处理被启用时发现一个未声明的前缀。

xml.parsers.expat.errors.XML_ERROR_UNDECLARING_PREFIX¶
~~~
    

~~~
文档试图移除与某个前缀相关联的命名空间声明。

xml.parsers.expat.errors.XML_ERROR_INCOMPLETE_PE¶
~~~
    

~~~
一个参数实体包含不完整的标记。

xml.parsers.expat.errors.XML_ERROR_XML_DECL¶
~~~
    

~~~
文档完全未包含任何文档元素。

xml.parsers.expat.errors.XML_ERROR_TEXT_DECL¶
~~~
    

~~~
解析一个外部实体中的文本声明时出现错误。

xml.parsers.expat.errors.XML_ERROR_PUBLICID¶
~~~
    

~~~
在公有 id 中发现不被允许的字符。

xml.parsers.expat.errors.XML_ERROR_SUSPENDED¶
~~~
    

~~~
在挂起的解析器上请求执行操作，但未获得允许。 这包括提供额外输入或停止解析器的尝试。

xml.parsers.expat.errors.XML_ERROR_NOT_SUSPENDED¶
~~~
    

~~~
在解析器未被挂起的时候执行恢复解析器的尝试。

xml.parsers.expat.errors.XML_ERROR_ABORTED¶
~~~
    

~~~
此错误不应当被报告给 Python 应用程序。

xml.parsers.expat.errors.XML_ERROR_FINISHED¶
~~~
    

~~~
在一个已经完成解析输入的解析器上请求执行操作，但未获得允许。 这包括提供额外输入或停止解析器的尝试。

xml.parsers.expat.errors.XML_ERROR_SUSPEND_PE¶
~~~
    

~~~
xml.parsers.expat.errors.XML_ERROR_RESERVED_PREFIX_XML¶
~~~
    

~~~
有人试图撤销保留的命名空间前缀 `xml` 或将其绑定到另一个命名空间 URI。

xml.parsers.expat.errors.XML_ERROR_RESERVED_PREFIX_XMLNS¶
~~~
    

~~~
有人试图声明或撤销保留的命名空间前缀 `xmlns`。

xml.parsers.expat.errors.XML_ERROR_RESERVED_NAMESPACE_URI¶
~~~
    

~~~
有人试图将一个保留的命名空间前缀 `xml` 和 `xmlns` 的 URI 绑定到另一个命名空间前缀。

xml.parsers.expat.errors.XML_ERROR_INVALID_ARGUMENT¶
~~~
    

~~~
此错误不应当被报告给 Python 应用程序。

xml.parsers.expat.errors.XML_ERROR_NO_BUFFER¶
~~~
    

~~~
此错误不应当被报告给 Python 应用程序。

xml.parsers.expat.errors.XML_ERROR_AMPLIFICATION_LIMIT_BREACH¶
~~~
    

~~~
