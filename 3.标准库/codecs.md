# `codecs` \--- 编解码器注册和相关基类¶

**源代码：** [Lib/codecs.py](https://github.com/python/cpython/tree/3.12/Lib/codecs.py)

* * *

这个模块定义了标准 Python 编解码器（编码器和解码器）的基类并提供对内部 Python 编解码器注册表的访问，该注册表负责管理编解码器和错误处理的查找过程。 大多数标准编解码器都属于 [文本编码格式](../glossary.md#term-text-encoding)，它们可将文本编码为字节串（以及将字节串解码为文本），但也提供了一些将文本编码为文本，以及将字节串编码为字节串的编解码器。 自定义编解码器可以在任意类型间进行编码和解码，但某些模块特性被限制为仅适用于 [文本编码格式](../glossary.md#term-text-encoding) 或将数据编码为 [`bytes`](stdtypes.md#bytes "bytes") 的编解码器。

该模块定义了以下用于使用任何编解码器进行编码和解码的函数:

codecs.encode( _obj_ , _encoding ='utf-8'_, _errors ='strict'_)¶

    

~~~
使用为 _encoding_ 注册的编解码器对 _obj_ 进行编码。

可以给定 _Errors_ 以设置所需要的错误处理方案。 默认的错误处理方案 `'strict'` 表示编码错误将引发 [`ValueError`](exceptions.md#ValueError "ValueError") (或更特定编解码器相关的子类，例如 [`UnicodeEncodeError`](exceptions.md#UnicodeEncodeError "UnicodeEncodeError"))。 请参阅 编解码器基类 了解有关编解码器错误处理的更多信息。

codecs.decode( _obj_ , _encoding ='utf-8'_, _errors ='strict'_)¶
~~~
    

~~~
使用为 _encoding_ 注册的编解码器对 _obj_ 进行解码。

可以给定 _Errors_ 以设置所需要的错误处理方案。 默认的错误处理方案 `'strict'` 表示编码错误将引发 [`ValueError`](exceptions.md#ValueError "ValueError") (或更特定编解码器相关的子类，例如 [`UnicodeDecodeError`](exceptions.md#UnicodeDecodeError "UnicodeDecodeError"))。 请参阅 编解码器基类 了解有关编解码器错误处理的更多信息。

每种编解码器的完整细节也可以直接查找获取：

codecs.lookup( _encoding_ )¶
~~~
    

~~~
在 Python 编解码器注册表中查找编解码器信息，并返回一个 `CodecInfo` 对象，其定义见下文。

首先将会在注册表缓存中查找编码，如果未找到，则会扫描注册的搜索函数列表。 如果没有找到 `CodecInfo` 对象，则将引发 [`LookupError`](exceptions.md#LookupError "LookupError")。 否则，`CodecInfo` 对象将被存入缓存并返回给调用者。

_class _codecs.CodecInfo( _encode_ , _decode_ , _streamreader =None_, _streamwriter =None_, _incrementalencoder =None_, _incrementaldecoder =None_, _name =None_)¶
~~~
    

~~~
查找编解码器注册表所得到的编解码器细节信息。 构造器参数将保存为同名的属性：

name¶
~~~
    

~~~
编码名称

encode¶

decode¶
~~~
    

~~~
无状态的编码和解码函数。 它们必须是具有与 Codec 的 `encode()` 和 `decode()` 方法相同接口的函数或方法 (参见 Codec 接口)。 这些函数或方法应当工作于无状态的模式。

incrementalencoder¶

incrementaldecoder¶
~~~
    

~~~
增量式的编码器和解码器类或工厂函数。 这些函数必须分别提供由基类 `IncrementalEncoder` 和 `IncrementalDecoder` 所定义的接口。 增量式编解码器可以保持状态。

streamwriter¶

streamreader¶
~~~
    

~~~
流式写入器和读取器类或工厂函数。 这些函数必须分别提供由基类 `StreamWriter` 和 `StreamReader` 所定义的接口。 流式编解码器可以保持状态。

为了简化对各种编解码器组件的访问，本模块提供了以下附加函数，它们使用 `lookup()` 来执行编解码器查找：

codecs.getencoder( _encoding_ )¶
~~~
    

~~~
查找给定编码的编解码器并返回其编码器函数。

在编码无法找到时将引发 [`LookupError`](exceptions.md#LookupError "LookupError")。

codecs.getdecoder( _encoding_ )¶
~~~
    

~~~
查找给定编码的编解码器并返回其解码器函数。

在编码无法找到时将引发 [`LookupError`](exceptions.md#LookupError "LookupError")。

codecs.getincrementalencoder( _encoding_ )¶
~~~
    

~~~
查找给定编码的编解码器并返回其增量式编码器类或工厂函数。

在编码无法找到或编解码器不支持增量式编码器时将引发 [`LookupError`](exceptions.md#LookupError "LookupError")。

codecs.getincrementaldecoder( _encoding_ )¶
~~~
    

~~~
查找给定编码的编解码器并返回其增量式解码器类或工厂函数。

在编码无法找到或编解码器不支持增量式解码器时将引发 [`LookupError`](exceptions.md#LookupError "LookupError")。

codecs.getreader( _encoding_ )¶
~~~
    

~~~
查找给定编码的编解码器并返回其 `StreamReader` 类或工厂函数。

在编码无法找到时将引发 [`LookupError`](exceptions.md#LookupError "LookupError")。

codecs.getwriter( _encoding_ )¶
~~~
    

~~~
查找给定编码的编解码器并返回其 `StreamWriter` 类或工厂函数。

在编码无法找到时将引发 [`LookupError`](exceptions.md#LookupError "LookupError")。

自定义编解码器的启用是通过注册适当的编解码器搜索函数：

codecs.register( _search_function_ )¶
~~~
    

~~~
注册一个编解码器搜索函数。 搜索函数预期接收一个参数，即全部以小写字母表示的编码格式名称，其中中连字符和空格会被转换为下划线，并返回一个 `CodecInfo` 对象。 在搜索函数无法找到给定编码格式的情况下，它应当返回 `None`。

在 3.9 版本发生变更: 连字符和空格会被转换为下划线。

codecs.unregister( _search_function_ )¶
~~~
    

~~~
注销一个编解码器搜索函数并清空注册表缓存。 如果指定搜索函数未被注册，则不做任何操作。

在 3.10 版本加入.

虽然内置的 [`open()`](functions.md#open "open") 和相关联的 [`io`](io.md#module-io "io: Core tools for working with streams.") 模块是操作已编码文本文件的推荐方式，但本模块也提供了额外的工具函数和类，允许在操作二进制文件时使用更多种类的编解码器：

codecs.open( _filename_ , _mode ='r'_, _encoding =None_, _errors ='strict'_, _buffering =-1_)¶
~~~
    

~~~
使用给定的 _mode_ 打开已编码的文件并返回一个 `StreamReaderWriter` 的实例，提供透明的编码/解码。 默认的文件模式为 `'r'`，表示以读取模式打开文件。

备注

如果 _encoding_ 不为 `None`，则下层的已编码文件总是以二进制模式打开。 在读取和写入时不会自动执行 `'\n'` 的转换。 _mode_ 参数可以是内置 [`open()`](functions.md#open "open") 函数所接受的任意二进制模式；`'b'` 会被自动添加。

_encoding_ 指定文件所要使用的编码格式。 允许任何编码为字节串或从字节串解码的编码格式，而文件方法所支持的数据类型则取决于所使用的编解码器。

可以指定 _errors_ 来定义错误处理方案。 默认值 `'strict'` 表示在出现编码错误时引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

_buffering_ 的含义与内置 [`open()`](functions.md#open "open") 函数中的相同。 默认值 -1 表示将使用默认的缓冲区大小。

在 3.11 版本发生变更: `'U'` 模式已被移除。

codecs.EncodedFile( _file_ , _data_encoding_ , _file_encoding =None_, _errors ='strict'_)¶
~~~
    

~~~
返回一个 `StreamRecoder` 实例，它提供了 _file_ 的透明转码包装版本。 当包装版本被关闭时原始文件也会被关闭。

写入已包装文件的数据会根据给定的 _data_encoding_ 解码，然后以使用 _file_encoding_ 的字节形式写入原始文件。 从原始文件读取的字节串将根据 _file_encoding_ 解码，其结果将使用 _data_encoding_ 进行编码。

如果 _file_encoding_ 未给定，则默认为 _data_encoding_ 。

可以指定 _errors_ 来定义错误处理方案。 默认值 `'strict'` 表示在出现编码错误时引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

codecs.iterencode( _iterator_ , _encoding_ , _errors ='strict'_, _** kwargs_)¶
~~~
    

~~~
使用增量式编码器通过迭代来编码由 _iterator_ 所提供的输入。 此函数属于 [generator](../glossary.md#term-generator)。 _errors_ 参数（以及任何其他关键字参数）会被传递给增量式编码器。

此函数要求编解码器接受 [`str`](stdtypes.md#str "str") 对象形式的文本进行编码。 因此它不支持字节到字节的编码器，例如 `base64_codec`。

codecs.iterdecode( _iterator_ , _encoding_ , _errors ='strict'_, _** kwargs_)¶
~~~
    

~~~
使用增量式解码器通过迭代来解码由 _iterator_ 所提供的输入。 此函数属于 [generator](../glossary.md#term-generator)。 _errors_ 参数（以及任何其他关键字参数）会被传递给增量式解码器。

此函数要求编解码器接受 [`bytes`](stdtypes.md#bytes "bytes") 对象进行解码。 因此它不支持文本到文本的编码器，例如 `rot_13`，但是 `rot_13` 可以通过同样效果的 `iterencode()` 来使用。

本模块还提供了以下常量，适用于读取和写入依赖于平台的文件：

codecs.BOM¶

codecs.BOM_BE¶

codecs.BOM_LE¶

codecs.BOM_UTF8¶

codecs.BOM_UTF16¶

codecs.BOM_UTF16_BE¶

codecs.BOM_UTF16_LE¶

codecs.BOM_UTF32¶

codecs.BOM_UTF32_BE¶

codecs.BOM_UTF32_LE¶
~~~
    

~~~
这些常量定义了多种字节序列，即一些编码格式的 Unicode 字节顺序标记（BOM）。 它们在 UTF-16 和 UTF-32 数据流中被用以指明所使用的字节顺序，并在 UTF-8 中被用作 Unicode 签名。 `BOM_UTF16` 是 `BOM_UTF16_BE` 或 `BOM_UTF16_LE`，具体取决于平台的本机字节顺序，`BOM` 是 `BOM_UTF16` 的别名, `BOM_LE` 是 `BOM_UTF16_LE` 的别名，`BOM_BE` 是 `BOM_UTF16_BE` 的别名。 其他序列则表示 UTF-8 和 UTF-32 编码格式中的 BOM。

## 编解码器基类¶

`codecs` 模块定义了一系列基类用来定义配合编解码器对象进行工作的接口，并且也可用作定制编解码器实现的基础。

每种编解码器必须定义四个接口以便用作 Python 中的编解码器：无状态编码器、无状态解码器、流读取器和流写入器。 流读取器和写入器通常会重用无状态编码器/解码器来实现文件协议。 编解码器作者还需要定义编解码器将如何处理编码和解码错误。

### 错误处理方案¶

为了简化和标准化错误处理，编解码器可以通过接受 _errors_ 字符串参数来实现不同的错误处理方案:
~~~
    
    
~~~shell
>>> 'German ß, ♬'.encode(encoding='ascii', errors='backslashreplace')
b'German \\xdf, \\u266c'
>>> 'German ß, ♬'.encode(encoding='ascii', errors='xmlcharrefreplace')
b'German &#223;, &#9836;'
~~~

以下错误处理句柄可以用于所有的 Python 标准编码 编解码器:

值

|

含意  
  
---|---  
  
`'strict'`

|

引发 [`UnicodeError`](3.标准库/exceptions.md#UnicodeError "UnicodeError") (或其子类)，这是默认的方案。 在 `strict_errors()` 中实现。  
  
`'ignore'`

|

忽略错误格式的数据并且不加进一步通知就继续执行。 在 `ignore_errors()` 中实现。  
  
`'replace'`

|

用一个替代标记来替换。 在编码时，使用 `?` (ASCII 字符)。 在解码时，使用 `�` (U+FFFD，官方的 REPLACEMENT CHARACTER)。 在 `replace_errors()` 中实现。  
  
`'backslashreplace'`

|

用反斜杠转义序列来替换。 在编码时，使用格式为 `\xhh` `\uxxxx` `\Uxxxxxxxx` 的 Unicode 码位十六进制表示形式。 在解码时，使用格式为 `\xhh` 的字节值十六进制表示形式。 在 `backslashreplace_errors()` 中实现。  
  
`'surrogateescape'`

|

在解码时，将字节替换为 `U+DC80` 至 `U+DCFF` 范围内的单个代理代码。 当在编码数据时使用 `'surrogateescape'` 错误处理方案时，此代理将被转换回相同的字节。 （请参阅 [**PEP 383**](https://peps.python.org/pep-0383/) 了解详情。）  
  
下列错误处理句柄仅在编码时适用（在 [文本编码格式](../glossary.md#term-text-encoding) 类别以内）:

值

|

含意  
  
---|---  
  
`'xmlcharrefreplace'`

|

用 XML/HTML 数字字符引用来替换，即格式为 `&#num;` 的 Unicode 码位十进制表达形式。 在 `xmlcharrefreplace_errors()` 中实现。  
  
`'namereplace'`

|

用 `\N{...}` 转义序列来替换，出现在花括号中的是来自 Unicode 字符数据库的 Name 属性。 在 `namereplace_errors()` 中实现。  
  
此外，以下错误处理方案被专门用于指定的编解码器：

值

|

编解码器

|

含意  
  
---|---|---  
  
`'surrogatepass'`

|

utf-8, utf-16, utf-32, utf-16-be, utf-16-le, utf-32-be, utf-32-le

|

允许将代理码位 (`U+D800` \- `U+DFFF`) 作为正常码位来编码和解码。 否则这些编解码器会将 [`str`](stdtypes.md#str "str") 中出现的代理码位视为错误。  
  
在 3.1 版本加入: `'surrogateescape'` 和 `'surrogatepass'` 错误处理方案。

在 3.4 版本发生变更: `'surrogatepass'` 错误处理器现在可适用于 utf-16* 和 utf-32* 编解码器。

在 3.5 版本加入: `'namereplace'` 错误处理方案。

在 3.5 版本发生变更: `'backslashreplace'` 错误处理器现在可适用于解码和转码。

允许的值集合可以通过注册新命名的错误处理方案来扩展：

codecs.register_error( _name_ , _error_handler_ )¶

    

~~~
在名称 _name_ 之下注册错误处理函数 _error_handler_ 。 当 _name_ 被指定为错误形参时， _error_handler_ 参数所指定的对象将在编码和解码期间发生错误的情况下被调用，

对于编码操作，将会调用 _error_handler_ 并传入一个 [`UnicodeEncodeError`](exceptions.md#UnicodeEncodeError "UnicodeEncodeError") 实例，其中包含有关错误位置的信息。 错误处理程序必须引发此异常或别的异常，或者也可以返回一个元组，其中包含输入的不可编码部分的替换对象，以及应当继续进行编码的位置。 替换对象可以为 [`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 类型。 如果替换对象为字节串，编码器将简单地将其复制到输出缓冲区。 如果替换对象为字符串，编码器将对替换对象进行编码。 对原始输入的编码操作会在指定位置继续进行。 负的位置值将被视为相对于输入字符串的末尾。 如果结果位置超出范围则将引发 [`IndexError`](exceptions.md#IndexError "IndexError")。

解码和转换的做法很相似，不同之处在于将把 [`UnicodeDecodeError`](exceptions.md#UnicodeDecodeError "UnicodeDecodeError") 或 [`UnicodeTranslateError`](exceptions.md#UnicodeTranslateError "UnicodeTranslateError") 传给处理程序，并且来自错误处理程序的替换对象将被直接放入输出。

之前注册的错误处理方案（包括标准错误处理方案）可通过名称进行查找：

codecs.lookup_error( _name_ )¶
~~~
    

~~~
返回之前在名称 _name_ 之下注册的错误处理方案。

在处理方案无法找到时将引发 [`LookupError`](exceptions.md#LookupError "LookupError")。

以下标准错误处理方案也可通过模块层级函数的方式来使用：

codecs.strict_errors( _exception_ )¶
~~~
    

~~~
实现了 `'strict'` 错误处理。

每个编码或解码错误都将引发 [`UnicodeError`](exceptions.md#UnicodeError "UnicodeError")。

codecs.ignore_errors( _exception_ )¶
~~~
    

~~~
实现了 `'ignore'` 错误处理。

错误格式的数据会被忽略；编码或解码将继续执行而不再通知。

codecs.replace_errors( _exception_ )¶
~~~
    

~~~
实现了 `'replace'` 错误处理。

替换 `?` (ASCII 字符) 表示编码错误或者 `�` (U+FFFD，官方的 REPLACEMENT CHARACTER) 表示解码错误。

codecs.backslashreplace_errors( _exception_ )¶
~~~
    

~~~
实现了 `'backslashreplace'` 错误处理。

错误格式的数据会用反斜杠转义序列来替换。 在编码时，使用格式为 `\xhh` `\uxxxx` `\Uxxxxxxxx` 的 Unicode 码位十六进制表示形式。 在解码时，使用格式为 `\xhh` 的字节值十六进制表示形式。

在 3.5 版本发生变更: 适用于解码和转码。

codecs.xmlcharrefreplace_errors( _exception_ )¶
~~~
    

~~~
实现 `'xmlcharrefreplace'` 错误处理（仅限 [text encoding](../glossary.md#term-text-encoding) 范围内的编码操作）。

不可编码的字符会被替换为适当的 XML/HTML 数值字符引用，即格式为 `&#num;` 的十进制形式 Unicode 码位。

codecs.namereplace_errors( _exception_ )¶
~~~
    

~~~
实现 `'namereplace'` 错误处理（仅限 [text encoding](../glossary.md#term-text-encoding) 范围内的编码操作）。

不可编码的字符会被替换为 `\N{...}` 转义序列。 出现在花括号内的字符集合是来自于 Unicode 字符数据库的 Name 属性。 例如，德语小写字母 `'ß'` 将被转换为字符序列 `\N{LATIN SMALL LETTER SHARP S}`。

在 3.5 版本加入.

### 无状态的编码和解码¶

基本 `Codec` 类定义了这些方法，同时还定义了无状态编码器和解码器的函数接口：

Codec.encode( _input_ , _errors ='strict'_)¶
~~~
    

~~~
编码 _input_ 对象并返回一个元组 (输出对象, 消耗长度)。 例如，[text encoding](../glossary.md#term-text-encoding) 会使用特定的字符集编码格式 (例如 `cp1252` 或 `iso-8859-1`) 将字符串转换为字节串对象。

_errors_ 参数定义了要应用的错误处理方案。 默认为 `'strict'` 处理方案。

此方法不一定会在 `Codec` 实例中保存状态。 可使用必须保存状态的 `StreamWriter` 作为编解码器以便高效地进行编码。

编码器必须能够处理零长度的输入并在此情况下返回输出对象类型的空对象。

Codec.decode( _input_ , _errors ='strict'_)¶
~~~
    

~~~
解码 _input_ 对象并返回一个元组 (输出对象, 消耗长度)。 例如，[text encoding](../glossary.md#term-text-encoding) 的解码操作会使用特定的字符集编码格式将字节串对象转换为字符串对象。

对于文本编码格式和字节到字节编解码器， _input_ 必须为一个字节串对象或提供了只读缓冲区接口的对象 -- 例如，缓冲区对象和映射到内存的文件。

_errors_ 参数定义了要应用的错误处理方案。 默认为 `'strict'` 处理方案。

此方法不一定会在 `Codec` 实例中保存状态。 可使用必须保存状态的 `StreamReader` 作为编解码器以便高效地进行解码。

解码器必须能够处理零长度的输入并在此情况下返回输出对象类型的空对象。

### 增量式的编码和解码¶

`IncrementalEncoder` 和 `IncrementalDecoder` 类提供了增量式编码和解码的基本接口。 对输入的编码/解码不是通过对无状态编码器/解码器的一次调用，而是通过对增量式编码器/解码器的 `encode()`/`decode()` 方法的多次调用。 增量式编码器/解码器会在方法调用期间跟踪编码/解码过程。

调用 `encode()`/`decode()` 方法后的全部输出相当于将所有通过无状态编码器/解码器进行编码/解码的单个输入连接在一起所得到的输出。

#### IncrementalEncoder 对象¶

`IncrementalEncoder` 类用来对一个输入进行分步编码。 它定义了以下方法，每个增量式编码器都必须定义这些方法以便与 Python 编解码器注册表相兼容。

_class _codecs.IncrementalEncoder( _errors ='strict'_)¶
~~~
    

~~~
`IncrementalEncoder` 实例的构造器。

所有增量式编码器必须提供此构造器接口。 它们可以自由地添加额外的关键字参数，但只有在这里定义的参数才会被 Python 编解码器注册表所使用。

`IncrementalEncoder` 可以通过提供 _errors_ 关键字参数来实现不同的错误处理方案。 可用的值请参阅 错误处理方案。

_errors_ 参数将被赋值给一个同名的属性。 通过对此属性赋值就可以在 `IncrementalEncoder` 对象的生命期内在不同的错误处理策略之间进行切换。

encode( _object_ , _final =False_)¶
~~~
    

~~~
编码 _object_ (会将编码器的当前状态纳入考虑) 并返回已编码的结果对象。 如果这是对 `encode()` 的最终调用则 _final_ 必须为真值（默认为假值）。

reset()¶
~~~
    

~~~
将编码器重置为初始状态。 输出将被丢弃：调用 `.encode(object, final=True)`，在必要时传入一个空字节串或字符串，重置编码器并得到输出。

getstate()¶
~~~
    

~~~
返回编码器的当前状态，该值必须为一个整数。 实现应当确保 `0` 是最常见的状态。 （比整数更复杂的状态表示可以通过编组/选择状态并将结果字符串的字节数据编码为整数来转换为一个整数值）。

setstate( _state_ )¶
~~~
    

~~~
将编码器的状态设为 _state_ 。 _state_ 必须为 `getstate()` 所返回的一个编码器状态。

#### IncrementalDecoder 对象¶

`IncrementalDecoder` 类用来对一个输入进行分步解码。 它定义了以下方法，每个增量式解码器都必须定义这些方法以便与 Python 编解码器注册表相兼容。

_class _codecs.IncrementalDecoder( _errors ='strict'_)¶
~~~
    

~~~
`IncrementalDecoder` 实例的构造器。

所有增量式解码器必须提供此构造器接口。 它们可以自由地添加额外的关键字参数，但只有在这里定义的参数才会被 Python 编解码器注册表所使用。

`IncrementalDecoder` 可以通过提供 _errors_ 关键字参数来实现不同的错误处理方案。 可用的值请参阅 错误处理方案。

_errors_ 参数将被赋值给一个同名的属性。 通过对此属性赋值就可以在 `IncrementalDecoder` 对象的生命期内在不同的错误处理策略之间进行切换。

decode( _object_ , _final =False_)¶
~~~
    

~~~
解码 _object_ (会将解码器的当前状态纳入考虑) 并返回已解码的结果对象。 如果这是对 `decode()` 的最终调用则 _final_ 必须为真值（默认为假值）。 如果 _final_ 为真值则解码器必须对输入进行完全解码并且必须 刷新所有缓冲区。 如果这无法做到（例如由于在输入结束时字节串序列不完整）则它必须像在无状态的情况下那样初始化错误处理（这可能引发一个异常）。

reset()¶
~~~
    

~~~
将解码器重置为初始状态。

getstate()¶
~~~
    

~~~
返回解码器的当前状态。 这必须为一个二元组，第一项必须是包含尚未解码的输入的缓冲区。 第二项必须为一个整数，可以表示附加状态信息。 （实现应当确保 `0` 是最常见的附加状态信息。） 如果此附加状态信息为 `0` 则必须可以将解码器设为没有已缓冲输入并且以 `0` 作为附加状态信息，以便将先前已缓冲的输入馈送到解码器使其返回到先前的状态而不产生任何输出。 （比整数更复杂的附加状态信息可以通过编组/选择状态信息并将结果字符串的字节数据编码为整数来转换为一个整数值。）

setstate( _state_ )¶
~~~
    

~~~
将解码器的状态设为 _state_ 。 _state_ 必须为 `getstate()` 所返回的一个解码器状态。

### 流式的编码和解码¶

`StreamWriter` 和 `StreamReader` 类提供了一些泛用工作接口，可被用来非常方便地实现新的编码格式子模块。 请参阅 `encodings.utf_8` 中的示例了解如何做到这一点。

#### StreamWriter 对象¶

`StreamWriter` 类是 `Codec` 的子类，它定义了以下方法，每个流式写入器都必须定义这些方法以便与 Python 编解码器注册表相兼容。

_class _codecs.StreamWriter( _stream_ , _errors ='strict'_)¶
~~~
    

~~~
`StreamWriter` 实例的构造器。

所有流式写入器必须提供此构造器接口。 它们可以自由地添加额外的关键字参数，但只有在这里定义的参数才会被 Python 编解码器注册表所使用。

_stream_ 参数必须为一个基于特定编解码器打开用于写入文本或二进制数据的文件类对象。

`StreamWriter` 可以通过提供 _errors_ 关键字参数来实现不同的错误处理方案。 请参阅 错误处理方案 了解下层的流式编解码器可支持的标准错误处理方案。

_errors_ 参数将被赋值给一个同名的属性。 通过对此属性赋值就可以在 `StreamWriter` 对象的生命期内在不同的错误处理策略之间进行切换。

write( _object_ )¶
~~~
    

~~~
将编码后的对象内容写入到流。

writelines( _list_ )¶
~~~
    

~~~
将拼接后的字符串可迭代对象写入到流（可能通过重用 `write()` 方法）。 无限长或非常大的可迭代对象不受支持。 标准的字节到字节编解码器不支持此方法。

reset()¶
~~~
    

~~~
重置用于保持内部状态的编解码器缓冲区。

调用此方法应当确保在干净的状态下放入输出数据，以允许直接添加新的干净数据而无须重新扫描整个流来恢复状态。

除了上述的方法，`StreamWriter` 还必须继承来自下层流的所有其他方法和属性。

#### StreamReader 对象¶

`StreamReader` 类是 `Codec` 的子类，它定义了以下方法，每个流式读取器都必须定义这些方法以便与 Python 编解码器注册表相兼容。

_class _codecs.StreamReader( _stream_ , _errors ='strict'_)¶
~~~
    

~~~
`StreamReader` 实例的构造器。

所有流式读取器必须提供此构造器接口。 它们可以自由地添加额外的关键字参数，但只有在这里定义的参数才会被 Python 编解码器注册表所使用。

_stream_ 参数必须为一个基于特定编解码器打开用于读取文本或二进制数据的文件类对象。

`StreamReader` 可以通过提供 _errors_ 关键字参数来实现不同的错误处理方案。 请参阅 错误处理方案 了解下层的流式编解码器可支持的标准错误处理方案。

_errors_ 参数将被赋值给一个同名的属性。 通过对此属性赋值就可以在 `StreamReader` 对象的生命期内在不同的错误处理策略之间进行切换。

_errors_ 参数所允许的值集合可以使用 `register_error()` 来扩展。

read( _size =-1_, _chars =-1_, _firstline =False_)¶
~~~
    

~~~
解码来自流的数据并返回结果对象。

_chars_ 参数指明要返回的解码后码位或字节数量。 `read()` 方法绝不会返回超出请求数量的数据，但如果可用数量不足，它可能返回少于请求数量的数据。

_size_ 参数指明要读取并解码的已编码字节或码位的最大数量近似值。 解码器可以适当地修改此设置。 默认值 -1 表示尽可能多地读取并解码。 此形参的目的是防止一次性解码过于巨大的文件。

_firstline_ 旗标指明如果在后续行发生解码错误，则仅返回第一行就足够了。

此方法应当使用“贪婪”读取策略，这意味着它应当在编码格式定义和给定大小所允许的情况下尽可能多地读取数据，例如，如果在流上存在可选的编码结束或状态标记，这些内容也应当被读取。

readline( _size =None_, _keepends =True_)¶
~~~
    

~~~
从输入流读取一行并返回解码后的数据。

如果给定了 _size_ ，则将其作为 size 参数传递给流的 `read()` 方法。

如果 _keepends_ 为假值，则行结束符将从返回的行中去除。

readlines( _sizehint =None_, _keepends =True_)¶
~~~
    

~~~
从输入流读取所有行并将其作为一个行列表返回。

行结束符会使用编解码器的 `decode()` 方法来实现，并且如果 _keepends_ 为真值则会将其包含在列表条目中。

如果给定了 _sizehint_ ，则将其作为 _size_ 参数传递给流的 `read()` 方法。

reset()¶
~~~
    

~~~
重置用于保持内部状态的编解码器缓冲区。

请注意不应当对流进行重定位。 使用此方法的主要目的是为了能够从解码错误中恢复。

除了上述的方法，`StreamReader` 还必须继承来自下层流的所有其他方法和属性。

#### StreamReaderWriter 对象¶

`StreamReaderWriter` 是一个方便的类，允许对同时工作于读取和写入模式的流进行包装。

其设计使得开发者可以使用 `lookup()` 函数所返回的工厂函数来构造实例。

_class _codecs.StreamReaderWriter( _stream_ , _Reader_ , _Writer_ , _errors ='strict'_)¶
~~~
    

~~~
创建一个 `StreamReaderWriter` 实例。 _stream_ 必须为一个文件类对象。 _Reader_ 和 _Writer_ 必须为分别提供了 `StreamReader` 和 `StreamWriter` 接口的工厂函数或类。 错误处理通过与流式读取器和写入器所定义的相同方式来完成。

`StreamReaderWriter` 实例定义了 `StreamReader` 和 `StreamWriter` 类的组合接口。 它们还继承了来自下层流的所有其他方法和属性。

#### StreamRecoder 对象¶

`StreamRecoder` 将数据从一种编码格式转换为另一种，这对于处理不同编码环境的情况有时会很有用。

其设计使得开发者可以使用 `lookup()` 函数所返回的工厂函数来构造实例。

_class _codecs.StreamRecoder( _stream_ , _encode_ , _decode_ , _Reader_ , _Writer_ , _errors ='strict'_)¶
~~~
    

~~~
创建一个实现了双向转换的 `StreamRecoder` 实例: _encode_ 和 _decode_ 工作于前端 — 对代码可见的数据调用 `read()` 和 `write()`，而 _Reader_ 和 _Writer_ 工作于后端 — _stream_ 中的数据。

你可以使用这些对象来进行透明转码，例如从 Latin-1 转为 UTF-8 以及反向转换。

_stream_ 参数必须为一个文件类对象。

_encode_ 和 _decode_ 参数必须遵循 `Codec` 接口。 _Reader_ 和 _Writer_ 必须为分别提供了 `StreamReader` 和 `StreamWriter` 接口对象的工厂函数或类。

错误处理通过与流式读取器和写入器所定义的相同方式来完成。

`StreamRecoder` 实例定义了 `StreamReader` 和 `StreamWriter` 类的组合接口。 它们还继承了来自下层流的所有其他方法和属性。

## 编码格式与 Unicode¶

字符串在系统内部存储为 `U+0000`\--`U+10FFFF` 范围内的码位序列。 （请参阅 [**PEP 393**](https://peps.python.org/pep-0393/) 了解有关实现的详情。） 一旦字符串对象要在 CPU 和内存以外使用，字节的大小端顺序和字节数组的存储方式就成为一个影响因素。 如同使用其他编解码器一样，将字符串序列化为字节序列被称为 _编码_ ，而从字节序列重建字符串被称为 _解码_ 。 存在许多不同的文本序列化编解码器，它们被统称为 [文本编码格式](../glossary.md#term-text-encoding)。

最简单的文本编码格式 (称为 `'latin-1'` 或 `'iso-8859-1'`) 将码位 0--255 映射为字节值 `0x0`\--`0xff`，这意味着包含 `U+00FF` 以上码位的字符串对象无法使用此编解码器进行编码。 这样做将引发 [`UnicodeEncodeError`](exceptions.md#UnicodeEncodeError "UnicodeEncodeError")，其形式类似下面这样（不过详细的错误信息可能会有所不同）: `UnicodeEncodeError: 'latin-1' codec can't encode character '\u1234' in position 3: ordinal not in range(256)`。

还有另外一组编码格式（所谓的字符映射编码）会选择全部 Unicode 码位的不同子集并设定如何将这些码位映射为字节值 `0x0`\--`0xff`。 要查看这是如何实现的，只需简单地打开相应源码例如 `encodings/cp1252.py` (这是一个主要在 Windows 上使用的编码格式)。 其中会有一个包含 256 个字符的字符串常量，指明每个字符所映射的字节值。

所有这些编码格式只能对 Unicode 所定义的 1114112 个码位中的 256 个进行编码。 一种能够存储每个 Unicode 码位的简单而直接的办法就是将每个友们存储为四个连续的字节。 存在两种不同的可能性：以大端序存储或以小端序存储。 这两种编码格式分别被称为 `UTF-32-BE` 和 `UTF-32-LE`。 他们共有的缺点可以举例说明：如果你在一台小端序的机器上使用 `UTF-32-BE` 则你必须在编码和解码时翻转字节。 `UTF-32` 避免了这个问题：字节的排列将总是使用自然端序。 当这些字节被具有不同端序的 CPU 读取时，则必须进行字节翻转。 为了能够检测 `UTF-16` 或 `UTF-32` 字节序列的大小端序，可以使用所谓的 BOM ("字节顺序标记")。 这对应于 Unicode 字符 `U+FEFF`。 此字符可被添加到每个 `UTF-16` 或 `UTF-32` 字节序列的开头。 此字符的字节翻转版本 (`0xFFFE`) 是一个不可出现于 Unicode 文本中的非法字符。 因此当发现一个 `UTF-16` 或 `UTF-32` 字节序列的首个字符是 `U+FFFE` 时，就必须在解码时进行字节翻转。 不幸的是字符 `U+FEFF` 还有第二个含义 `ZERO WIDTH NO-BREAK SPACE`: 即宽度为零并且不允许用来拆分单词的字符。 它可以被用来为语言分析算法提供提示。 在 Unicode 4.0 中使用 `U+FEFF` 表示 `ZERO WIDTH NO-BREAK SPACE` 已被弃用 (改用 `U+2060` (`WORD JOINER`) 负责此任务)。 然而 Unicode 软件仍然必须能够处理 `U+FEFF` 的两个含义：作为 BOM 它被用来确定已编码字节的存储布局，并在字节序列被解码为字符串后将其去除；作为 `ZERO WIDTH NO-BREAK SPACE` 它是一个普通字符，将像其他字符一样被解码。

还有另一种编码格式能够对所有 Unicode 字符进行编码：UTF-8。 UTF-8 是一种 8 位编码，这意味着在 UTF-8 中没有字节顺序问题。 UTF-8 字节序列中的每个字节由两部分组成：标志位（最重要的位）和内容位。 标志位是由零至四个值为 `1` 的二进制位加一个值为 `0` 的二进制位构成的序列。 Unicode 字符会按以下形式进行编码（其中 x 为内容位，当拼接为一体时将给出对应的 Unicode 字符）:

范围

|

编码  
  
---|---  
  
`U-00000000` ... `U-0000007F`

|

0xxxxxxx  
  
`U-00000080` ... `U-000007FF`

|

110xxxxx 10xxxxxx  
  
`U-00000800` ... `U-0000FFFF`

|

1110xxxx 10xxxxxx 10xxxxxx  
  
`U-00010000` ... `U-0010FFFF`

|

11110xxx 10xxxxxx 10xxxxxx 10xxxxxx  
  
Unicode 字符最不重要的一个位就是最右侧的二进制位 x。

由于 UTF-8 是一种 8 位编码格式，因此 BOM 是不必要的，并且已编码字符串中的任何 `U+FEFF` 字符（即使是作为第一个字符）都会被视为是 `ZERO WIDTH NO-BREAK SPACE`。

在没有外部信息的情况下将不可能毫无疑义地确定一个字符串使用了何种编码格式。 每种字符映射编码格式都可以解码任意的随机字节序列。 然而对 UTF-8 来说这却是不可能的，因为 UTF-8 字节序列具有不允许任意字节序列的特别结构。 为了提升 UTF-8 编码格式检测的可靠性，Microsoft 发明了一种 UTF-8 的变体形式 (Python 称之为 `"utf-8-sig"`) 专门用于其 Notepad 程序：在任何 Unicode 字节被写入文件之前，会先写入一个 UTF-8 编码的 BOM (它看起来是这样一个字节序列: `0xef`, `0xbb`, `0xbf`)。 由于任何字符映射编码后的文件都不大可能以这些字节值开头 (例如它们会映射为

> LATIN SMALL LETTER I WITH DIAERESIS
>
> RIGHT-POINTING DOUBLE ANGLE QUOTATION MARK
>
> INVERTED QUESTION MARK

对于 iso-8859-1 编码格式来说），这提升了根据字节序列来正确猜测 `utf-8-sig` 编码格式的成功率。 所以在这里 BOM 的作用并不是帮助确定生成字节序列所使用的字节顺序，而是作为帮助猜测编码格式的记号。 在进行编码时 utf-8-sig 编解码器将把 `0xef`, `0xbb`, `0xbf` 作为头三个字节写入文件。 在进行解码时 `utf-8-sig` 将跳过这三个字节，如果它们作为文件的头三个字节出现的话。 在 UTF-8 中并不推荐使用 BOM，通常应当避免它们的出现。

## 标准编码¶

Python 自带了许多内置的编解码器，它们的实现或者是通过 C 函数，或者是通过映射表。 以下表格是按名称排序的编解码器列表，并提供了一些常见别名以及编码格式通常针对的语言。 别名和语言列表都不是详尽无遗的。 请注意仅有大小写区别或使用连字符替代下划线的拼写形式也都是有效的别名；因此，`'utf-8'` 是 `'utf_8'` 编解码器的有效别名。

**CPython 实现细节：** 有些常见编码格式可以绕过编解码器查找机制来提升性能。 这些优化机会对于 CPython 来说仅能通过一组有限的别名（大小写不敏感）来识别：utf-8, utf8, latin-1, latin1, iso-8859-1, iso8859-1, mbcs (Windows 专属), ascii, us-ascii, utf-16, utf16, utf-32, utf32, 也包括使用下划线替代连字符的的形式。 使用这些编码格式的其他别名可能会导致更慢的执行速度。

在 3.6 版本发生变更: 可识别针对 us-ascii 的优化机会。

许多字符集都支持相同的语言。 它们在个别字符（例如是否支持 EURO SIGN 等）以及给字符所分配的码位方面存在差异。 特别是对于欧洲语言来说，通常存在以下几种变体：

  * 某个 ISO 8859 编码集

  * 某个 Microsoft Windows 编码页，通常是派生自某个 8859 编码集，但会用附加的图形字符来替换控制字符。

  * 某个 IBM EBCDIC 编码页

  * 某个 IBM PC 编码页，通常会兼容 ASCII

编码

|

别名

|

语言  
  
---|---|---  
  
ascii

|

646, us-ascii

|

英语  
  
big5

|

big5-tw, csbig5

|

繁体中文  
  
big5hkscs

|

big5-hkscs, hkscs

|

繁体中文  
  
cp037

|

IBM037, IBM039

|

英语  
  
cp273

|

273, IBM273, csIBM273

|

德语

在 3.4 版本加入.  
  
cp424

|

EBCDIC-CP-HE, IBM424

|

希伯来语  
  
cp437

|

437, IBM437

|

英语  
  
cp500

|

EBCDIC-CP-BE, EBCDIC-CP-CH, IBM500

|

西欧  
  
cp720

|  |

阿拉伯语  
  
cp737

|  |

希腊语  
  
cp775

|

IBM775

|

波罗的海语言  
  
cp850

|

850, IBM850

|

西欧  
  
cp852

|

852, IBM852

|

中欧和东欧  
  
cp855

|

855, IBM855

|

保加利亚语，白俄罗斯语，马其顿语，俄语，塞尔维亚语  
  
cp856

|  |

希伯来语  
  
cp857

|

857, IBM857

|

土耳其语  
  
cp858

|

858, IBM858

|

西欧  
  
cp860

|

860, IBM860

|

葡萄牙语  
  
cp861

|

861, CP-IS, IBM861

|

冰岛语  
  
cp862

|

862, IBM862

|

希伯来语  
  
cp863

|

863, IBM863

|

加拿大语  
  
cp864

|

IBM864

|

阿拉伯语  
  
cp865

|

865, IBM865

|

丹麦语/挪威语  
  
cp866

|

866, IBM866

|

俄语  
  
cp869

|

869, CP-GR, IBM869

|

希腊语  
  
cp874

|  |

泰语  
  
cp875

|  |

希腊语  
  
cp932

|

932, ms932, mskanji, ms-kanji

|

日语  
  
cp949

|

949, ms949, uhc

|

韩语  
  
cp950

|

950, ms950

|

繁体中文  
  
cp1006

|  |

乌尔都语  
  
cp1026

|

ibm1026

|

土耳其语  
  
cp1125

|

1125, ibm1125, cp866u, ruscii

|

乌克兰语

在 3.4 版本加入.  
  
cp1140

|

ibm1140

|

西欧  
  
cp1250

|

windows-1250

|

中欧和东欧  
  
cp1251

|

windows-1251

|

保加利亚语，白俄罗斯语，马其顿语，俄语，塞尔维亚语  
  
cp1252

|

windows-1252

|

西欧  
  
cp1253

|

windows-1253

|

希腊语  
  
cp1254

|

windows-1254

|

土耳其语  
  
cp1255

|

windows-1255

|

希伯来语  
  
cp1256

|

windows-1256

|

阿拉伯语  
  
cp1257

|

windows-1257

|

波罗的海语言  
  
cp1258

|

windows-1258

|

越南语  
  
euc_jp

|

eucjp, ujis, u-jis

|

日语  
  
euc_jis_2004

|

jisx0213, eucjis2004

|

日语  
  
euc_jisx0213

|

eucjisx0213

|

日语  
  
euc_kr

|

euckr, korean, ksc5601, ks_c-5601, ks_c-5601-1987, ksx1001, ks_x-1001

|

韩语  
  
gb2312

|

chinese, csiso58gb231280, euc-cn, euccn, eucgb2312-cn, gb2312-1980, gb2312-80, iso-ir-58

|

简体中文  
  
gbk

|

936, cp936, ms936

|

统一汉语  
  
gb18030

|

gb18030-2000

|

统一汉语  
  
hz

|

hzgb, hz-gb, hz-gb-2312

|

简体中文  
  
iso2022_jp

|

csiso2022jp, iso2022jp, iso-2022-jp

|

日语  
  
iso2022_jp_1

|

iso2022jp-1, iso-2022-jp-1

|

日语  
  
iso2022_jp_2

|

iso2022jp-2, iso-2022-jp-2

|

日语，韩语，简体中文，西欧，希腊语  
  
iso2022_jp_2004

|

iso2022jp-2004, iso-2022-jp-2004

|

日语  
  
iso2022_jp_3

|

iso2022jp-3, iso-2022-jp-3

|

日语  
  
iso2022_jp_ext

|

iso2022jp-ext, iso-2022-jp-ext

|

日语  
  
iso2022_kr

|

csiso2022kr, iso2022kr, iso-2022-kr

|

韩语  
  
latin_1

|

iso-8859-1, iso8859-1, 8859, cp819, latin, latin1, L1

|

西欧  
  
iso8859_2

|

iso-8859-2, latin2, L2

|

中欧和东欧  
  
iso8859_3

|

iso-8859-3, latin3, L3

|

世界语，马耳他语  
  
iso8859_4

|

iso-8859-4, latin4, L4

|

波罗的海语言  
  
iso8859_5

|

iso-8859-5, cyrillic

|

保加利亚语，白俄罗斯语，马其顿语，俄语，塞尔维亚语  
  
iso8859_6

|

iso-8859-6, arabic

|

阿拉伯语  
  
iso8859_7

|

iso-8859-7, greek, greek8

|

希腊语  
  
iso8859_8

|

iso-8859-8, hebrew

|

希伯来语  
  
iso8859_9

|

iso-8859-9, latin5, L5

|

土耳其语  
  
iso8859_10

|

iso-8859-10, latin6, L6

|

北欧语言  
  
iso8859_11

|

iso-8859-11, thai

|

泰语  
  
iso8859_13

|

iso-8859-13, latin7, L7

|

波罗的海语言  
  
iso8859_14

|

iso-8859-14, latin8, L8

|

凯尔特语  
  
iso8859_15

|

iso-8859-15, latin9, L9

|

西欧  
  
iso8859_16

|

iso-8859-16, latin10, L10

|

东南欧  
  
johab

|

cp1361, ms1361

|

韩语  
  
koi8_r

|  |

俄语  
  
koi8_t

|  |

塔吉克

在 3.5 版本加入.  
  
koi8_u

|  |

乌克兰语  
  
kz1048

|

kz_1048, strk1048_2002, rk1048

|

哈萨克语

在 3.5 版本加入.  
  
mac_cyrillic

|

maccyrillic

|

保加利亚语，白俄罗斯语，马其顿语，俄语，塞尔维亚语  
  
mac_greek

|

macgreek

|

希腊语  
  
mac_iceland

|

maciceland

|

冰岛语  
  
mac_latin2

|

maclatin2, maccentraleurope, mac_centeuro

|

中欧和东欧  
  
mac_roman

|

macroman, macintosh

|

西欧  
  
mac_turkish

|

macturkish

|

土耳其语  
  
ptcp154

|

csptcp154, pt154, cp154, cyrillic-asian

|

哈萨克语  
  
shift_jis

|

csshiftjis, shiftjis, sjis, s_jis

|

日语  
  
shift_jis_2004

|

shiftjis2004, sjis_2004, sjis2004

|

日语  
  
shift_jisx0213

|

shiftjisx0213, sjisx0213, s_jisx0213

|

日语  
  
utf_32

|

U32, utf32

|

所有语言  
  
utf_32_be

|

UTF-32BE

|

所有语言  
  
utf_32_le

|

UTF-32LE

|

所有语言  
  
utf_16

|

U16, utf16

|

所有语言  
  
utf_16_be

|

UTF-16BE

|

所有语言  
  
utf_16_le

|

UTF-16LE

|

所有语言  
  
utf_7

|

U7, unicode-1-1-utf-7

|

所有语言  
  
utf_8

|

U8, UTF, utf8, cp65001

|

所有语言  
  
utf_8_sig

|  |

所有语言  
  
在 3.4 版本发生变更: utf-16* 和 utf-32* 编码器将不再允许编码代理码位 (`U+D800`\--`U+DFFF`)。 utf-32* 解码器将不再解码与代理码位相对应的字节序列。

在 3.8 版本发生变更: `cp65001` 现在是 `utf_8` 的一个别名。

## Python 专属的编码格式¶

有一些预定义编解码器是 Python 专属的，因此它们在 Python 之外没有意义。 这些编解码器按其所预期的输入和输出类型在下表中列出（请注意虽然文本编码是编解码器最常见的使用场景，但下层的编解码器架构支持任意数据转换而不仅是文本编码）。 对于非对称编解码器，该列描述的含义是编码方向。

### 文字编码¶

以下编解码器提供了 [`str`](stdtypes.md#str "str") 到 [`bytes`](stdtypes.md#bytes "bytes") 的编码和 [bytes-like object](../glossary.md#term-bytes-like-object) 到 [`str`](stdtypes.md#str "str") 的解码，类似于 Unicode 文本编码。

编码

|

别名

|

含意  
  
---|---|---  
  
idna

|  |

实现 [**RFC 3490**](https://datatracker.ietf.org/doc/html/rfc3490.md)，另请参阅 `encodings.idna` 。仅支持 `errors='strict'` 。  
  
mbcs

|

ansi, dbcs

|

Windows 专属：根据 ANSI 代码页（CP_ACP）对操作数进行编码。  
  
oem

|  |

Windows 专属：根据 OEM 代码页（CP_OEMCP）对操作数进行编码。

在 3.6 版本加入.  
  
palmos

|  |

PalmOS 3.5 的编码格式  
  
punycode

|  |

实现 [**RFC 3492**](https://datatracker.ietf.org/doc/html/rfc3492.md)。 不支持有状态编解码器。  
  
raw_unicode_escape

|  |

Latin-1 编码格式附带对其他码位以 `\uXXXX` 和 `\UXXXXXXXX` 进行编码。 现有反斜杠不会以任何方式转义。 它被用于 Python 的 pickle 协议。  
  
undefined

|  |

所有转换都将引发异常，甚至对空字符串也不例外。 错误处理方案会被忽略。  
  
unicode_escape

|  |

适合用于以 ASCII 编码的 Python 源代码中的 Unicode 字面值内容的编码格式，但引号不会被转义。 对 Latin-1 源代码进行解码。 请注意 Python 源代码实际上默认使用 UTF-8。  
  
在 3.8 版本发生变更: "unicode_internal" 编解码器已被移除。

### 二进制转换¶

以下编解码器提供了二进制转换: [bytes-like object](../glossary.md#term-bytes-like-object) 到 [`bytes`](stdtypes.md#bytes "bytes") 的映射。 它们不被 [`bytes.decode()`](stdtypes.md#bytes.decode "bytes.decode") 所支持（该方法只生成 [`str`](stdtypes.md#str "str") 类型的输出）。

编码

|

别名

|

含意

|

编码器/解码器  
  
---|---|---|---  
  
base64_codec [1]

|

base64, base_64

|

将操作数转换为多行 MIME base64 (结果总是包含一个末尾的 `'\n'`)

在 3.4 版本发生变更: 接受任意 [bytes-like object](../glossary.md#term-bytes-like-object) 作为输入用于编码和解码

|

[`base64.encodebytes()`](base64.md#base64.encodebytes "base64.encodebytes") / [`base64.decodebytes()`](base64.md#base64.decodebytes "base64.decodebytes")  
  
bz2_codec

|

bz2

|

使用bz2压缩操作数

|

[`bz2.compress()`](bz2.md#bz2.compress "bz2.compress") / [`bz2.decompress()`](bz2.md#bz2.decompress "bz2.decompress")  
  
hex_codec

|

hex

|

将操作数转换为十六进制表示，每个字节有两位数

|

[`binascii.b2a_hex()`](binascii.md#binascii.b2a_hex "binascii.b2a_hex") / [`binascii.a2b_hex()`](binascii.md#binascii.a2b_hex "binascii.a2b_hex")  
  
quopri_codec

|

quopri, quotedprintable, quoted_printable

|

将操作数转换为 MIME 带引号的可打印数据

|

[`quopri.encode()`](quopri.md#quopri.encode "quopri.encode") 且 `quotetabs=True` / [`quopri.decode()`](quopri.md#quopri.decode "quopri.decode")  
  
uu_codec

|

uu

|

使用uuencode转换操作数

|

`uu.encode()` / `uu.decode()`  
  
zlib_codec

|

zip, zlib

|

使用gzip压缩操作数

|

[`zlib.compress()`](zlib.md#zlib.compress "zlib.compress") / [`zlib.decompress()`](zlib.md#zlib.decompress "zlib.decompress")  
  
[1]

除了 [字节类对象](../glossary.md#term-bytes-like-object)，`'base64_codec'` 也接受仅包含 ASCII 的 [`str`](stdtypes.md#str "str") 实例用于解码

在 3.2 版本加入: 恢复二进制转换。

在 3.4 版本发生变更: 恢复二进制转换的别名。

### 文字转换¶

以下编解码器提供了文本转换: [`str`](stdtypes.md#str "str") 到 [`str`](stdtypes.md#str "str") 的映射。 它不被 [`str.encode()`](stdtypes.md#str.encode "str.encode") 所支持（该方法只生成 [`bytes`](stdtypes.md#bytes "bytes") 类型的输出）。

编码

|

别名

|

含意  
  
---|---|---  
  
rot_13

|

rot13

|

返回操作数的凯撒密码加密结果  
  
在 3.2 版本加入: 恢复 `rot_13` 文本转换。

在 3.4 版本发生变更: 恢复 `rot13` 别名。

## `encodings.idna` \--- 应用程序中的国际化域名¶

此模块实现了 [**RFC 3490**](https://datatracker.ietf.org/doc/html/rfc3490.md) (应用程序中的国际化域名) 和 [**RFC 3492**](https://datatracker.ietf.org/doc/html/rfc3492.md) (Nameprep: 用于国际化域名 (IDN) 的 Stringprep 配置文件)。 它是在 `punycode` 编码格式和 [`stringprep`](stringprep.md#module-stringprep "stringprep: String preparation, as per RFC 3453") 的基础上构建的。

如果你需要来自 [**RFC 5891**](https://datatracker.ietf.org/doc/html/rfc5891.md) 和 [**RFC 5895**](https://datatracker.ietf.org/doc/html/rfc5895.md) 的 IDNA 2008 标准，请使用第三方 [idna 模块](https://pypi.org/project/idna/)。

这些 RFC 共同定义了一个在域名中支持非 ASCII 字符的协议。 一个包含非 ASCII 字符的域名 (例如 `www.Alliancefrançaise.nu`) 会被转换为兼容 ASCII 的编码格式 (简称 ACE，例如 `www.xn--alliancefranaise-npb.nu`)。 随后此域名的 ACE 形式可以用于所有由于特定协议而不允许使用任意字符的场合，例如 DNS 查询，HTTP _Host_ 字段等等。 此转换是在应用中进行的；如有可能将对用户可见：应用应当透明地将 Unicode 域名标签转换为线上的 IDNA，并在 ACE 标签被呈现给用户之前将其转换回 Unicode。

Python 以多种方式支持这种转换: `idna` 编解码器执行 Unicode 和 ACE 之间的转换，基于在 [**section 3.1 of RFC 3490**](https://datatracker.ietf.org/doc/html/rfc3490.md#section-3.1) 中定义的分隔字符将输入字符串拆分为标签，再根据需要将每个标签转换为 ACE，相反地又会基于 `.` 分隔符将输入字节串拆分为标签，再将找到的任何 ACE 标签转换为 Unicode。 此外，[`socket`](socket.md#module-socket "socket: Low-level networking interface.") 模块可透明地将 Unicode 主机名转换为 ACE，以便应用在将它们传给 socket 模块时无须自行转换主机名。 除此之外，许多包含以主机名作为函数参数的模块例如 [`http.client`](http.client.md#module-http.client "http.client: HTTP and HTTPS protocol client \(requires sockets\).") 和 [`ftplib`](ftplib.md#module-ftplib "ftplib: FTP protocol client \(requires sockets\).") 都接受 Unicode 主机名（并且 [`http.client`](http.client.md#module-http.client "http.client: HTTP and HTTPS protocol client \(requires sockets\).") 也会在 _Host_ 字段中透明地发送 IDNA 主机名，如果它需要发送该字段的话）。

当从线路接收主机名时（例如反向名称查找），到 Unicode 的转换不会自动被执行：希望向用户提供此种主机名的应用应当将它们解码为 Unicode。

`encodings.idna` 模块还实现了 nameprep 过程，该过程会对主机名执行特定的规范化操作，以实现国际域名的大小写不敏感特性与合并相似的字符。 如果有需要可以直接使用 nameprep 函数。

encodings.idna.nameprep( _label_ )¶
~~~
    

~~~
返回 _label_ 经过名称处理操作的版本。 该实现目前基于查询字符串，因此 `AllowUnassigned` 为真值。

encodings.idna.ToASCII( _label_ )¶
~~~
    

~~~
将标签转换为 ASCII，规则定义见 [**RFC 3490**](https://datatracker.ietf.org/doc/html/rfc3490.md)。 `UseSTD3ASCIIRules` 预设为假值。

encodings.idna.ToUnicode( _label_ )¶
~~~
    

~~~
