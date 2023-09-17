# `xml.dom` \--- 文档对象模型 API¶

**源代码:** [Lib/xml/dom/__init__.py](https://github.com/python/cpython/tree/3.12/Lib/xml/dom/__init__.py)

* * *

文档对象模型“DOM”是一个来自万维网联盟（W3C）的跨语言 API，用于访问和修改 XML 文档。 DOM 的实现将 XML 文档以树结构表示，或者允许客户端代码从头构建这样的结构。 然后它会通过一组提供通用接口的对象赋予对结构的访问权。

DOM 特别适用于进行随机访问的应用。 SAX 仅允许你每次查看文档的一小部分。 如果你正在查看一个 SAX 元素，你将不能访问其他元素。 如果你正在查看一个文本节点，你将不能访问包含它的元素。 当你编写一个 SAX 应用时，你需要在你自己的代码的某个地方记住你的程序在文档中的位置。 SAX 不会帮你做这件事。 并且，如果你想要在 XML 文档中向前查看，你是绝对办不到的。

有些应用程序在不能访问树的事件驱动模型中是根本无法编写的。 当然你可以在 SAX 事件中自行构建某种树，但是 DOM 可以使你避免编写这样的代码。 DOM 是针对 XML 数据的标准树表示形式。

文档对象模型是由 W3C 分阶段定义的，在其术语中称为“层级”。 Python 中该 API 的映射大致是基于 DOM 第 2 层级的建议。

DOM 应用程序通常从将某些 XML 解析为 DOM 开始。 此操作如何实现完全未被 DOM 第 1 层级所涉及，而第 2 层级也只提供了有限的改进：有一个 `DOMImplementation` 对象类，它提供对 `Document` 创建方法的访问，但却没有办法以不依赖具体实现的方式访问 XML 读取器/解析器/文档创建器。 也没有当不存在 `Document` 对象的情况下访问这些方法的定义良好的方式。 在 Python 中，每个 DOM 实现将提供一个函数 `getDOMImplementation()`。 DOM 第 3 层级增加了一个载入/存储规格说明，它定义了与读取器的接口，但这在 Python 标准库中尚不可用。

一旦你得到了 DOM 文档对象，你就可以通过 XML 文档的属性和方法访问它的各个部分。 这些属性定义在 DOM 规格说明当中；参考指南的这一部分描述了 Python 对此规格说明的解读。

W3C 提供的规格说明定义了适用于 Java, ECMAScript 和 OMG IDL 的 DOM API。 这里定义的 Python 映射很大程度上是基于此规格说明的 IDL 版本，但并不要求严格映射（但具体实现可以自由地支持对 IDL 的严格映射）。 请参阅 一致性 一节查看有关映射要求的详细讨论。

参见

[文档对象模型 (DOM) 第 2 层级规格说明](https://www.w3.org/TR/2000/REC-DOM-Level-2-Core-20001113/)

    

~~~
被 Python DOM API 作为基础的 W3C 建议。

[文档对象模型 (DOM) 第 1 层级规格说明](https://www.w3.org/TR/REC-DOM-Level-1/)
~~~
    

~~~
被 [`xml.dom.minidom`](xml.dom.minidom.md#module-xml.dom.minidom "xml.dom.minidom: Minimal Document Object Model \(DOM\) implementation.") 所支持的 W3C 针对 DOM 的建议。

[Python 语言映射规格说明](https://www.omg.org/spec/PYTH/1.2/PDF)
~~~
    

~~~
此文档指明了从 OMG IDL 到 Python 的映射。

## 模块内容¶

`xml.dom` 包含下列函数:

xml.dom.registerDOMImplementation( _name_ , _factory_ )¶
~~~
    

~~~
注册 _factory_ 函数并使用名称 _name_ 。 该工厂函数应当返回一个实现了 `DOMImplementation` 接口的对象。 该工厂函数可每次都返回相同对象，或每次调用都返回新的对象，视具体实现的要求而定（例如该实现是否支持某些定制功能）。

xml.dom.getDOMImplementation( _name =None_, _features =()_)¶
~~~
    

~~~
返回一个适当的 DOM 实现。 _name_ 是通用名称、DOM 实现的模块名称或者 `None`。 如果它不为 `None`，则会导入相应模块并在导入成功时返回一个 `DOMImplementation` 对象。 如果没有给出名称，并且如果设置了 `PYTHON_DOM` 环境变量，此变量会被用来查找相应的实现。

如果未给出 name，此函数会检查可用的实现来查找具有所需特性集的一个。 如果找不到任何实现，则会引发 [`ImportError`](exceptions.md#ImportError "ImportError")。 features 集必须是包含 `(feature, version)` 对的序列，它会被传给可用的 `DOMImplementation` 对象上的 `hasFeature()` 方法。

还提供了一些便捷常量:

xml.dom.EMPTY_NAMESPACE¶
~~~
    

~~~
该值用于指明没有命名空间被关联到 DOM 中的某个节点。 它通常被作为某个节点的 `namespaceURI`，或者被用作某个命名空间专属方法的 _namespaceURI_ 参数。

xml.dom.XML_NAMESPACE¶
~~~
    

~~~
关联到保留前缀 `xml` 的命名空间 URI，如 [XML 中的命名空间](https://www.w3.org/TR/REC-xml-names/) （第 4 节） 所定义的。

xml.dom.XMLNS_NAMESPACE¶
~~~
    

~~~
命名空间声明的命名空间 URI，如 [文档对象模型 (DOM) 第 2 层级核心规格说明](https://www.w3.org/TR/DOM-Level-2-Core/core.md) (第 1.1.8节) 所定义的。

xml.dom.XHTML_NAMESPACE¶
~~~
    

~~~
XHTML 命名空间的 URI，如 [XHTML 1.0: 扩展超文本标记语言](https://www.w3.org/TR/xhtml1/) (第 3.1.1 节) 所定义的。

此外，`xml.dom` 还包含一个基本 `Node` 类和一些 DOM 异常类。 此模块提供的 `Node` 类未实现 DOM 规格描述所定义的任何方法和属性；实际的 DOM 实现必须提供它们。 提供 `Node` 类作为此模块的一部分并没有提供用于实际的 `Node` 对象的 `nodeType` 属性的常量；它们是位于类内而不是位于模块层级以符合 DOM 规格描述。

## DOM 中的对象¶

DOM 的权威文档是来自 W3C 的 DOM 规格描述。

请注意，DOM 属性也可以作为节点而不是简单的字符串进行操作。 然而，必须这样做的情况相当少见，所以这种用法还没有被写入文档。

接口

|

部件

|

目的  
  
---|---|---  
  
`DOMImplementation`

|

DOMImplementation 对象

|

底层实现的接口。  
  
`Node`

|

节点对象

|

文档中大多数对象的基本接口。  
  
`NodeList`

|

节点列表对象

|

节点序列的接口。  
  
`DocumentType`

|

文档类型对象

|

有关处理文档所需声明的信息。  
  
`Document`

|

Document 对象

|

表示整个文档的对象。  
  
`Element`

|

元素对象

|

文档层次结构中的元素节点。  
  
`Attr`

|

Attr 对象

|

元素节点上的属性值节点。  
  
`Comment`

|

注释对象

|

源文档中注释的表示形式。  
  
`Text`

|

Text 和 CDATASection 对象

|

包含文档中文本内容的节点。  
  
`ProcessingInstruction`

|

ProcessingInstruction 对象

|

处理指令表示形式。  
  
描述在 Python 中使用 DOM 定义的异常的小节。

### DOMImplementation 对象¶

`DOMImplementation` 接口提供了一种让应用程序确定他们所使用的 DOM 中某一特性可用性的方式。 DOM 第 2 级还添加了使用 `DOMImplementation` 来创建新的 `Document` 和 `DocumentType` 对象的能力。

DOMImplementation.hasFeature( _feature_ , _version_ )¶
~~~
    

~~~
如果字符串对 _feature_ 和 _version_ 所标识的特性已被实现则返回 `True`。

DOMImplementation.createDocument( _namespaceUri_ , _qualifiedName_ , _doctype_ )¶
~~~
    

~~~
返回一个新的 `Document` 对象 (DOM 的根节点)，包含一个具有给定 _namespaceUri_ 和 _qualifiedName_ 的下级 `Element` 对象。 _doctype_ 必须为由 `createDocumentType()` 创建的 `DocumentType` 对象，或者为 `None`。 在 Python DOM API 中，前两个参数也可为 `None` 以表示不要创建任何下级 `Element`。

DOMImplementation.createDocumentType( _qualifiedName_ , _publicId_ , _systemId_ )¶
~~~
    

~~~
返回一个新的封装了给定 _qualifiedName_ , _publicId_ 和 _systemId_ 字符串的 `DocumentType` 对象，它表示包含在 XML 文档类型声明中的信息。

### 节点对象¶

XML 文档的所有组成部分都是 `Node` 的子类。

Node.nodeType¶
~~~
    

~~~
一个代表节点类型的整数。 类型符号常量在 `Node` 对象上: `ELEMENT_NODE`, `ATTRIBUTE_NODE`, `TEXT_NODE`, `CDATA_SECTION_NODE`, `ENTITY_NODE`, `PROCESSING_INSTRUCTION_NODE`, `COMMENT_NODE`, `DOCUMENT_NODE`, `DOCUMENT_TYPE_NODE`, `NOTATION_NODE`。 这是个只读属性。

Node.parentNode¶
~~~
    

~~~
当前节点的上级，或者对于文档节点则为 `None`。 该值总是一个 `Node` 对象或者 `None`。 对于 `Element` 节点，这将为上级元素，但对于根元素例外，在此情况下它将为 `Document` 对象。 对于 `Attr` 节点，它将总是为 `None`。 这是个只读属性。

Node.attributes¶
~~~
    

~~~
属性对象的 `NamedNodeMap`。 这仅对元素才有实际值；其它对象会为该属性提供 `None` 值。 这是个只读属性。

Node.previousSibling¶
~~~
    

~~~
在此节点之前具有相同上级的相邻节点。 例如结束标记紧接在在 _self_ 元素的开始标记之前的元素。 当然，XML 文档并非只是由元素组成，因此之前相邻节点可以是文本、注释或者其他内容。 如果此节点是上级的第一个子节点，则该属性将为 `None`。 这是一个只读属性。

Node.nextSibling¶
~~~
    

~~~
在此节点之后具有相同上级的相邻节点。 另请参见 `previousSibling`。 如果此节点是上级的最后一个子节点，则该属性将为 `None`。 这是一个只读属性。

Node.childNodes¶
~~~
    

~~~
包含在此节点中的节点列表。 这是一个只读属性。

Node.firstChild¶
~~~
    

~~~
节点的第一个下级，如果有的话，否则为 `None`。 这是个只读属性。

Node.lastChild¶
~~~
    

~~~
节点的最后一个下级，如果有的话，否则为 `None`。 这是个只读属性。

Node.localName¶
~~~
    

~~~
`tagName` 在冒号之后的部分，如果有冒号的话，否则为整个 `tagName`。 该值为一个字符串。

Node.prefix¶
~~~
    

~~~
`tagName` 在冒号之前的部分，如果有冒号的话，否则为空字符串。 该值为一个字符串或者为 `None`。

Node.namespaceURI¶
~~~
    

~~~
关联到元素名称的命名空间。 这将是一个字符串或为 `None`。 这是个只读属性。

Node.nodeName¶
~~~
    

~~~
这对于每种节点类型具有不同的含义；请查看 DOM 规格说明来了解详情。 你总是可以从其他特征属性例如元素的 `tagName` 特征属性或属性的 `name` 特征属性获取你能从这里获取的信息。 对于所有节点类型，这个属性的值都将是一个字符串或为 `None`。 这是一个只读属性。

Node.nodeValue¶
~~~
    

~~~
这对于每种节点类型具有不同的含义；请查看 DOM 规格说明来了解详情。 具体情况与 `nodeName` 的类似。 该值是一个字符串或为 `None`。

Node.hasAttributes()¶
~~~
    

~~~
如果该节点具有任何属性则返回 `True`。

Node.hasChildNodes()¶
~~~
    

~~~
如果该节点具有任何子节点则返回 `True`。

Node.isSameNode( _other_ )¶
~~~
    

~~~
如果 _other_ 指向的节点就是此节点则返回 `True`。 这对于使用了任何代理架构的 DOM 实现来说特别有用（因为多个对象可能指向相同节点）。

备注

这是基于已提议的 DOM 第 3 等级 API，目前尚处于“起草”阶段，但这个特定接口看来并不存在争议。 来自 W3C 的修改将不会影响 Python DOM 接口中的这个方法（不过针对它的任何新 W3C API 也将受到支持）。

Node.appendChild( _newChild_ )¶
~~~
    

~~~
在子节点列表末尾添加一个新的子节点，返回 _newChild_ 。 如果节点已存在于树结构中，它将先被移除。

Node.insertBefore( _newChild_ , _refChild_ )¶
~~~
    

~~~
在现有的子节点之前插入一个新的子节点。 它必须属于 _refChild_ 是这个节点的子节点的情况；如果不是，则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 _newChild_ 会被返回。 如果 _refChild_ 为 `None`，它会将 _newChild_ 插入到子节点列表的末尾。

Node.removeChild( _oldChild_ )¶
~~~
    

~~~
移除一个子节点。 _oldChild_ 必须是这个节点的子节点；如果不是，则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 成功时 _oldChild_ 会被返回。 如果 _oldChild_ 将不再被继续使用，则将调用它的 `unlink()` 方法。

Node.replaceChild( _newChild_ , _oldChild_ )¶
~~~
    

~~~
将一个现有节点替换为新的节点。 这必须属于 _oldChild_ 是该节点的子节点的情况；如果不是，则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

Node.normalize()¶
~~~
    

~~~
合并相邻的文本节点以便将所有文本段存储为单个 `Text` 实例。 这可以简化许多应用程序处理来自 DOM 树文本的操作。

Node.cloneNode( _deep_ )¶
~~~
    

~~~
克隆此节点。 设置 _deep_ 表示也克隆所有子节点。 此方法将返回克隆的节点。

### 节点列表对象¶

`NodeList` 代表一个节点列表。 在 DOM 核心建议中这些对象有两种使用方式: 由 `Element` 对象提供作为其子节点列表，以及由 `Node` 的 `getElementsByTagName()` 和 `getElementsByTagNameNS()` 方法通过此接口返回对象来表示查询结果。

DOM 第 2 层级建议为这些对象定义一个方法和一个属性:

NodeList.item( _i_ )¶
~~~
    

~~~
从序列中返回第 _i_ 项，如果序列不为空的话，否则返回 `None`。 索引号 _i_ 不允许小于零或大于等于序列的长度。

NodeList.length¶
~~~
    

~~~
序列中的节点数量。

此外，Python DOM 接口还要求提供一些额外支持来允许将 `NodeList` 对象用作 Python 序列。 所有 `NodeList` 实现都必须包括对 [`__len__()`](../reference/datamodel.md#object.__len__ "object.__len__") 和 [`__getitem__()`](../reference/datamodel.md#object.__getitem__ "object.__getitem__") 的支持；这样 `NodeList` 就允许使用 [`for`](../reference/compound_stmts.md#for) 语句进行迭代并能正确地支持 [`len()`](functions.md#len "len") 内置函数。

如果一个 DOM 实现支持文档的修改，则 `NodeList` 实现还必须支持 [`__setitem__()`](../reference/datamodel.md#object.__setitem__ "object.__setitem__") 和 [`__delitem__()`](../reference/datamodel.md#object.__delitem__ "object.__delitem__") 方法。

### 文档类型对象¶

有关一个文档所声明的标注和实体的信息（包括解析器所使用并能提供信息的外部子集）可以从 `DocumentType` 对象获取。 文档的 `DocumentType` 可从 `Document` 对象的 `doctype` 属性中获取；如果一个文档没有 `DOCTYPE` 声明，则该文档的 `doctype` 属性将被设为 `None` 而非此接口的一个实例。

`DocumentType` 是 `Node` 是专门化，并增加了下列属性:

DocumentType.publicId¶
~~~
    

~~~
文档类型定义的外部子集的公有标识。 这将为一个字符串或者为 `None`。

DocumentType.systemId¶
~~~
    

~~~
文档类型定义的外部子集的系统标识。 这将为一个字符串形式的 URI，或者为 `None`。

DocumentType.internalSubset¶
~~~
    

~~~
一个给出来自文档的完整内部子集的字符串。 这不包括子集外面的圆括号。 如果文档没有内部子集，则应为 `None`。

DocumentType.name¶
~~~
    

~~~
`DOCTYPE` 声明中给出的根元素名称，如果有的话。

DocumentType.entities¶
~~~
    

~~~
这是给出外部实体定义的 `NamedNodeMap`。 对于多次定义的实体名称，则只提供第一次的定义（其他的会按照 XML 建议被忽略）。 这可能为 `None`，如果解析器未提供此信息，或者如果未定义任何实体的话。

DocumentType.notations¶
~~~
    

~~~
这是给出标注定义的 `NamedNodeMap`。 对于多次定义的标注，则只提供第一次的定义（其他的会按照 XML 建议被忽略）。 这可能为 `None`，如果解析器未提供此信息，或者如果未定义任何标注的话。

### Document 对象¶

`Document` 代表一个完整的 XML 文档，包括其组成元素、属性、处理指令和注释等。 请记住它会继承来自 `Node` 的属性。

Document.documentElement¶
~~~
    

~~~
文档唯一的根元素。

Document.createElement( _tagName_ )¶
~~~
    

~~~
创建并返回一个新的元素节点。 当元素被创建时不会被插入到文档中。 你需要通过某个其他方法例如 `insertBefore()` 或 `appendChild()` 来显示地插入它。

Document.createElementNS( _namespaceURI_ , _tagName_ )¶
~~~
    

~~~
创建并返回一个新的带有命名空间的元素。 _tagName_ 可以带有前缀。 当元素被创建时不会被插入到文档中。 你需要通过某个其他方法例如 `insertBefore()` 或 `appendChild()` 来显式地插入它。

Document.createTextNode( _data_ )¶
~~~
    

~~~
创建并返回一个包含作为形参被传入的数据的文本节点。 与其他创建方法一样，此方法不会将节点插入到树中。

Document.createComment( _data_ )¶
~~~
    

~~~
创建并返回一个包含作为形参被传入的数据的注释节点。 与其他创建方法一样，此方法不会将节点插入到树中。

Document.createProcessingInstruction( _target_ , _data_ )¶
~~~
    

~~~
创建并返回一个包含作为形参被传入的 _target_ 和 _data_ 的处理指令节点。 与其他创建方法一样，此方法不会将节点插入到树中。

Document.createAttribute( _name_ )¶
~~~
    

~~~
创建并返回一个属性节点。 此方法不会将属性节点关联到任何特定的元素。 你必须在正确的 `Element` 对象上使用 `setAttributeNode()` 来使用新创建的属性实例。

Document.createAttributeNS( _namespaceURI_ , _qualifiedName_ )¶
~~~
    

~~~
创建并返回一个带有命名空间的属性节点。 _tagName_ 可以带有前缀。 此方法不会将属性节点关联到任何特定的元素。 你必须在正确的 `Element` 对象上使用 `setAttributeNode()` 来使用新创建的属性实例。

Document.getElementsByTagName( _tagName_ )¶
~~~
    

~~~
搜索全部具有特定元素类型名称的后继元素（直接下级、下级的下级等等）。

Document.getElementsByTagNameNS( _namespaceURI_ , _localName_ )¶
~~~
    

~~~
搜索全部具有特定命名空间 URI 和 localname 的后继元素（直接下级、下级的下级等等）。 localname 是命名空间在前缀之后的部分。

### 元素对象¶

`Element` 是 `Node` 的子类，因此会继承该类的全部属性。

Element.tagName¶
~~~
    

~~~
元素类型名称。 在使用命名空间的文档中它可能包含冒号。 该值是一个字符串。

Element.getElementsByTagName( _tagName_ )¶
~~~
    

~~~
与 `Document` 类中的对应方法相同。

Element.getElementsByTagNameNS( _namespaceURI_ , _localName_ )¶
~~~
    

~~~
与 `Document` 类中的对应方法相同。

Element.hasAttribute( _name_ )¶
~~~
    

~~~
如果元素带有名称为 _name_ 的属性则返回 `True`。

Element.hasAttributeNS( _namespaceURI_ , _localName_ )¶
~~~
    

~~~
如果元素带有名称为 _namespaceURI_ 加 _localName_ 的属性则返回 `True`。

Element.getAttribute( _name_ )¶
~~~
    

~~~
将名称为 _name_ 的属性的值作为字符串返回。 如果指定属性不存在，则返回空字符串，就像该属性没有对应的值一样。

Element.getAttributeNode( _attrname_ )¶
~~~
    

~~~
返回名称为 _attrname_ 的属性对应的 `Attr` 节点。

Element.getAttributeNS( _namespaceURI_ , _localName_ )¶
~~~
    

~~~
将名称为 _namespaceURI_ 加 _localName_ 的属性的值作为字符串返回。 如果指定属性不存在，则返回空字符串，就像该属性没有对应的值一样。

Element.getAttributeNodeNS( _namespaceURI_ , _localName_ )¶
~~~
    

~~~
将给定 _namespaceURI_ 加 _localName_ 的属性的值作为节点返回。

Element.removeAttribute( _name_ )¶
~~~
    

~~~
移除指定名称的节点。 如果没有匹配的属性，则会引发 `NotFoundErr`。

Element.removeAttributeNode( _oldAttr_ )¶
~~~
    

~~~
从属性列表中移除并返回 _oldAttr_ ，如果该属性存在的话。 如果 _oldAttr_ 不存在，则会引发 `NotFoundErr`。

Element.removeAttributeNS( _namespaceURI_ , _localName_ )¶
~~~
    

~~~
移除指定名称的属性。 请注意它是使用 localName 而不是 qname。 如果没有匹配的属性也不会引发异常。

Element.setAttribute( _name_ , _value_ )¶
~~~
    

~~~
将属性值设为指定的字符串。

Element.setAttributeNode( _newAttr_ )¶
~~~
    

~~~
将一个新的属性节点添加到元素，当匹配到 `name` 属性时如有必要会替换现有的属性。 如果发生了替换，将返回原有属性节点。 如果 _newAttr_ 已经被使用，则会引发 `InuseAttributeErr`。

Element.setAttributeNodeNS( _newAttr_ )¶
~~~
    

~~~
将一个新的属性节点添加到元素，当匹配到 `namespaceURI` 和 `localName` 属性时如有必要会替换现有的属性。 如果发生了替换，将返回原有属性节点。 如果 _newAttr_ 已经被使用，则会引发 `InuseAttributeErr`。

Element.setAttributeNS( _namespaceURI_ , _qname_ , _value_ )¶
~~~
    

~~~
将属性值设为 _namespaceURI_ 和 _qname_ 所给出的字符串。 请注意 qname 是整个属性名称。 这与上面的方法不同。

### Attr 对象¶

`Attr` 继承自 `Node`，因此会继承其全部属性。

Attr.name¶
~~~
    

~~~
属性名称。 在使用命名空间的文档中可能会包括冒号。

Attr.localName¶
~~~
    

~~~
名称在冒号之后的部分，如果有的话，否则为完整名称。 这是个只读属性。

Attr.prefix¶
~~~
    

~~~
名称在冒号之前的部分，如果有冒号的话，否则为空字符串。

Attr.value¶
~~~
    

~~~
属性的文本值。 这与 `nodeValue` 属性同义。

### NamedNodeMap 对象¶

`NamedNodeMap` _不是_ 继承自 `Node`。

NamedNodeMap.length¶
~~~
    

~~~
属性列表的长度。

NamedNodeMap.item( _index_ )¶
~~~
    

~~~
返回特定带有索引号的属性。 获取属性的顺序是强制规定的，但在 DOM 的生命期内会保持一致。 其中每一项均为属性节点。 可使用 `value` 属性获取其值。

还有一些试验性方法给予这个类更多的映射行为。 你可以使用这些方法或者使用 `Element` 对象上标准化的 `getAttribute*()` 方法族。

### 注释对象¶

`Comment` 代表 XML 文档中的注释。 它是 `Node` 的子类，但不能拥有下级节点。

Comment.data¶
~~~
    

~~~
注释的内容是一个字符串。 该属性包含在开头 `<!-``-` 和末尾 `-``->` 之间的所有字符，但不包括这两个符号。

### Text 和 CDATASection 对象¶

`Text` 接口代表 XML 文档中的文本。 如果解析器和 DOM 实现支持 DOM 的 XML 扩展，则包裹在 CDATA 标记的节中的部分会被存储到 `CDATASection` 对象中。 这两个接口很相似，但是提供了不同的 `nodeType` 属性值。

这些接口扩展了 `Node` 接口。 它们不能拥有下级节点。

Text.data¶
~~~
    

~~~
字符串形式的文本节点内容。

备注

`CDATASection` 节点的使用并不表示该节点代表一个完整的 CDATA 标记节，只是表示该节点的内容是 CDATA 节的一部分。 单个 CDATA 节可以由文档树中的多个节点来表示。 没有什么办法能确定两个相邻的 `CDATASection` 节点是否代表不同的 CDATA 标记节。

### ProcessingInstruction 对象¶

代表 XML 文档中的处理指令。 它继承自 `Node` 接口并且不能拥有下级节点。

ProcessingInstruction.target¶
~~~
    

~~~
到第一个空格符为止的处理指令内容。 这是个只读属性。

ProcessingInstruction.data¶
~~~
    

~~~
在第一个空格符之后的处理指令内容。

### 异常¶

DOM 第 2 层级推荐定义一个异常 `DOMException`，以及多个变量用来允许应用程序确定发生了何种错误。 `DOMException` 实例带有 [`code`](code.md#module-code "code: Facilities to implement read-eval-print loops.") 属性用来提供特定异常所对应的值。

Python DOM 接口提供了一些常量，但还扩展了异常集以使 DOM 所定义的每个异常代码都存在特定的异常。 接口的具体实现必须引发正确的特定异常，它们各自带有正确的 [`code`](code.md#module-code "code: Facilities to implement read-eval-print loops.") 属性值。

_exception _xml.dom.DOMException¶
~~~
    

~~~
所有特定 DOM 异常所使用的异常基类。 该异常类不可被直接实例化。

_exception _xml.dom.DomstringSizeErr¶
~~~
    

~~~
当指定范围的文本不能适配一个字符串时被引发。 此异常在 Python DOM 实现中尚不可用，但可从不是以 Python 编写的 DOM 实现中接收。

_exception _xml.dom.HierarchyRequestErr¶
~~~
    

~~~
当尝试插入一个节点但该节点类型不被允许时被引发。

_exception _xml.dom.IndexSizeErr¶
~~~
    

~~~
当一个方法的索引或大小参数为负值或超出允许的值范围时被引发。

_exception _xml.dom.InuseAttributeErr¶
~~~
    

~~~
当尝试插入一个 `Attr` 节点但该节点已存在于文档中的某处时被引发。

_exception _xml.dom.InvalidAccessErr¶
~~~
    

~~~
当某个参数或操作在底层对象中不受支持时被引发。

_exception _xml.dom.InvalidCharacterErr¶
~~~
    

~~~
当某个字符串参数包含的字符在使用它的上下文中不被 XML 1.0 标准建议所允许时引发。 例如，尝试创建一个元素类型名称中带有空格的 `Element` 节点将导致此错误被引发。

_exception _xml.dom.InvalidModificationErr¶
~~~
    

~~~
当尝试修改某个节点的类型时被引发。

_exception _xml.dom.InvalidStateErr¶
~~~
    

~~~
当尝试使用未定义或不再可用的的对象时被引发。

_exception _xml.dom.NamespaceErr¶
~~~
    

~~~
如果试图以 [XML 中的命名空间](https://www.w3.org/TR/REC-xml-names/) 建议所不允许的方式修改任何对象，则会引发此异常。

_exception _xml.dom.NotFoundErr¶
~~~
    

~~~
当某个节点不存在于被引用的上下文中时引发的异常。 例如，`NamedNodeMap.removeNamedItem()` 将在所传入的节点不在于于映射中时引发此异常。

_exception _xml.dom.NotSupportedErr¶
~~~
    

~~~
当具体实现不支持所请求的对象类型或操作时被引发。

_exception _xml.dom.NoDataAllowedErr¶
~~~
    

~~~
当为某个不支持数据 的节点指定数据时被引发。

_exception _xml.dom.NoModificationAllowedErr¶
~~~
    

~~~
当尝试修改某个不允许修改的对象（例如只读节点）时被引发。

_exception _xml.dom.SyntaxErr¶
~~~
    

~~~
当指定了无效或非法的字符串时被引发。

_exception _xml.dom.WrongDocumentErr¶
~~~
    

~~~
当将某个节点插入非其当前所属的另一个文档，并且具体实现不支持从一个文档向一个文档迁移节点时被引发。

DOM 建议映射中针对上述异常而定义的异常代码如下表所示:

常量

|

异常  
  
---|---  
  
`DOMSTRING_SIZE_ERR`

|

`DomstringSizeErr`  
  
`HIERARCHY_REQUEST_ERR`

|

`HierarchyRequestErr`  
  
`INDEX_SIZE_ERR`

|

`IndexSizeErr`  
  
`INUSE_ATTRIBUTE_ERR`

|

`InuseAttributeErr`  
  
`INVALID_ACCESS_ERR`

|

`InvalidAccessErr`  
  
`INVALID_CHARACTER_ERR`

|

`InvalidCharacterErr`  
  
`INVALID_MODIFICATION_ERR`

|

`InvalidModificationErr`  
  
`INVALID_STATE_ERR`

|

`InvalidStateErr`  
  
`NAMESPACE_ERR`

|

`NamespaceErr`  
  
`NOT_FOUND_ERR`

|

`NotFoundErr`  
  
`NOT_SUPPORTED_ERR`

|

`NotSupportedErr`  
  
`NO_DATA_ALLOWED_ERR`

|

`NoDataAllowedErr`  
  
`NO_MODIFICATION_ALLOWED_ERR`

|

`NoModificationAllowedErr`  
  
`SYNTAX_ERR`

|

`SyntaxErr`  
  
`WRONG_DOCUMENT_ERR`

|

`WrongDocumentErr`  
  
## 一致性¶

本节描述了 Python DOM API、W3C DOM 建议以及 Python 的 OMG IDL 映射之间的一致性要求和关系。

### 类型映射¶

将根据下表，将DOM规范中使用的IDL类型映射为Python类型。

IDL 类型

|

Python 类型  
  
---|---  
  
`boolean`

|

`bool` 或 `int`  
  
`int`

|

`int`  
  
`long int`

|

`int`  
  
`unsigned int`

|

`int`  
  
`DOMString`

|

`str` 或 `bytes`  
  
`null`

|

`None`  
  
### 访问器方法¶

从 OMG IDL 到 Python 的映射以类似于 Java 映射的方式定义了针对 IDL `attribute` 声明的访问器函数。 映射以下 IDL 声明
~~~
    
    
~~~
readonly attribute string someValue;
         attribute string anotherValue;
~~~

会产生三个访问器函数: `someValue` 的 "get" 方法 (`_get_someValue()`)，以及 `anotherValue` 的 "get" 和 "set" 方法 (`_get_anotherValue()` 和 `_set_anotherValue()`)。 特别地，该映射不要求 IDL 属性像普通 Python 属性那样可访问: `object.someValue` _并非_ 必须可用，并可能引发 [`AttributeError`](3.标准库/exceptions.md#AttributeError "AttributeError")。

但是，Python DOM API 则 _确实_ 要求普通属性访问可用。 这意味着由 Python IDL 解译器生成的典型代理有可能会不可用，如果 DOM 对象是通过 CORBA 来访问则在客户端可能需要有包装对象。 虽然这确实要求为 CORBA DOM 客户端进行额外的考虑，但具有从 Python 通过 CORBA 使用 DOM 经验的实现并不会认为这是个问题。 已经声明了 `readonly` 的属性不必在所有 DOM 实现中限制写入访问。

在 Python DOM API 中，访问器函数不是必须的。 如果提供，则它们应当采用由 Python IDL 映射所定义的形式，但这些方法会被认为不必要，因为这些属性可以从 Python 直接访问。 永远都不要为 `readonly` 属性提供 "set" 访问器。

IDL 定义没有完全体现 W3C DOM API 的要求，如特定对象的概念，又如 `getElementsByTagName()` 的返回值为 "live" 等。 Python DOM API 并不强制具体实现执行这些要求。

