# `email.header`: 国际化标头¶

**源代码:** [Lib/email/header.py](https://github.com/python/cpython/tree/3.12/Lib/email/header.py)

* * *

此模块是旧式 (`Compat32`) email API 的一部分。 在当前的 API 中标头的编码和解码是由 [`EmailMessage`](email.message.md#email.message.EmailMessage "email.message.EmailMessage") 类的字典型 API 来透明地处理的。 除了在旧有代码中使用，此模块在需要完全控制当编码标头时所使用的字符集时也很有用处。

本节中的其余文本是此模块的原始文档。

[**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 是描述电子邮件消息格式的基础标准。 它派生自更早的 [**RFC 822**](https://datatracker.ietf.org/doc/html/rfc822.md) 标准，该标准在大多数电子邮件仅由 ASCII 字符组成时已被广泛使用。 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 所描述的规范假定电子邮件都只包含 7 位 ASCII 字符。

当然，随着电子邮件在全球部署，它已经变得国际化了，例如电子邮件消息中现在可以使用特定语言的专属字符集。 这个基础标准仍然要求电子邮件消息只使用 7 位 ASCII 字符来进行传输，为此编写了大量 RFC 来描述如何将包含非 ASCII 字符的电子邮件编码为符合 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 的格式。 这些 RFC 包括 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md), [**RFC 2046**](https://datatracker.ietf.org/doc/html/rfc2046.md), [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.md) 和 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md)。 [`email`](email.md#module-email "email: Package supporting the parsing, manipulating, and generating email messages.") 包在其 `email.header` 和 [`email.charset`](email.charset.md#module-email.charset "email.charset: Character Sets") 模块中支持了这些标准。

如果你想在你的电子邮件标头中包括非 ASCII 字符，比如说是在 _Subject_ 或 _To_ 字段中，你应当使用 `Header` 类并将 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 对象中的字段赋值为 `Header` 的实例而不是使用字符串作为字段值。 请从 `email.header` 模块导入 `Header` 类。 例如:

    
    
~~~shell
>>> from email.message import Message
>>> from email.header import Header
>>> msg = Message()
>>> h = Header('p\xf6stal', 'iso-8859-1')
>>> msg['Subject'] = h
>>> msg.as_string()
'Subject: =?iso-8859-1?q?p=F6stal?=\n\n'
~~~

是否注意到这里我们是如何希望 _Subject_ 字段包含非 ASCII 字符的？ 我们通过创建一个 `Header` 实例并传入字节串编码所用的字符集来做到这一点。 当后续的 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 实例被展平时， _Subject_ 字段会正确地按 [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.md) 来编码。 可感知 MIME 的电子邮件阅读器将会使用嵌入的 ISO-8859-1 字符来显示此标头。

以下是 `Header` 类描述:

_class _email.header.Header( _s =None_, _charset =None_, _maxlinelen =None_, _header_name =None_, _continuation_ws =' '_, _errors ='strict'_)¶

    

~~~
创建符合 MIME 要求的标头，其中可包含不同字符集的字符串。

可选的 _s_ 是初始标头值。 如果为 `None` (默认值)，则表示初始标头值未设置。 你可以在稍后使用 `append()` 方法调用向标头添加新值。 _s_ 可以是 [`bytes`](stdtypes.md#bytes "bytes") 或 [`str`](stdtypes.md#str "str") 的实例，注意参阅 `append()` 文档了解相关语义。

可选的 _charset_ 用于两种目的：它的含义与 `append()` 方法的 _charset_ 参数相同。 它还会为所有省略了 _charset_ 参数的后续 `append()` 调用设置默认字符集。 如果 _charset_ 在构造器中未提供（默认设置），则会将 `us-ascii` 字符集用作 _s_ 的初始字符集以及后续 `append()` 调用的默认字符集。

通过 _maxlinelen_ 可以显式指定最大行长度。 要将第一行拆分为更短的值 (以适应未被包括在to account for the field header which isn't included in _s_ 中的字段标头，例如 _Subject_ )，则将字段名称作为 _header_name_ 传入。 _maxlinelen_ 默认值为 76，而 _header_name_ 默认值为 `None`，表示不考虑拆分超长标头的第一行。

可选的 _continuation_ws_ 必须为符合 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 的折叠用空白符，通常是空格符或硬制表符。 这个字符将被加缀至连续行的开头。 _continuation_ws_ 默认为一个空格符。

可选的 _errors_ 会被直接传递给 `append()` 方法。

append( _s_ , _charset =None_, _errors ='strict'_)¶
~~~
    

~~~
将字符串 _s_ 添加到 MIME 标头。

如果给出可选的 _charset_ ，它应当是一个 [`Charset`](email.charset.md#email.charset.Charset "email.charset.Charset") 实例 (参见 [`email.charset`](email.charset.md#module-email.charset "email.charset: Character Sets")) 或字符集名称，该参数将被转换为一个 [`Charset`](email.charset.md#email.charset.Charset "email.charset.Charset") 实例。 如果为 `None` (默认值) 则表示会使用构造器中给出的 _charset_ 。

_s_ 可以是 [`bytes`](stdtypes.md#bytes "bytes") 或 [`str`](stdtypes.md#str "str") 的实例。 如果它是 [`bytes`](stdtypes.md#bytes "bytes") 的实例，则 _charset_ 为该字节串的编码格式，如果字节串无法用该字符集来解码则将引发 [`UnicodeError`](exceptions.md#UnicodeError "UnicodeError")。

如果 _s_ 是 [`str`](stdtypes.md#str "str") 的实例，则 _charset_ 是用来指定字符串中字符字符集的提示。

在这两种情况下，当使用 [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.md) 规则产生符合 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 的标头时，将使用指定字符集的输出编解码器来编码字符串。 如果字符串无法使用该输出编解码器来编码，则将引发 UnicodeError。

可选的 _errors_ 会在 _s_ 为字节串时被作为 errors 参数传递给 decode 调用。

encode( _splitchars =';, \t'_, _maxlinelen =None_, _linesep ='\n'_)¶
~~~
    

~~~
将消息标头编码为符合 RFC 的格式，可能会对过长的行采取折行并将非 ASCII 部分以 base64 或 quoted-printable 编码格式进行封装。

可选的 _splitchars_ 是一个字符串，其中包含应在正常的标头折行处理期间由拆分算法赋予额外权重的字符。 这是对于 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 中 '更高层级语法拆分' 的很粗略的支持：在拆分期间会首选在 splitchar 之前的拆分点，字符的优先级是基于它们在字符串中的出现顺序。 字符串中可包含空格和制表符以指明当其他拆分字符未在被拆分行中出现时是否要将某个字符作为优先于另一个字符的首选拆分点。 拆分字符不会影响以 [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.md) 编码的行。

如果给出 _maxlinelen_ ，它将覆盖实例的最大行长度值。

_linesep_ 指定用来分隔已折叠标头行的字符。 它默认为 Python 应用程序代码中最常用的值 (`\n`)，但也可以指定为 `\r\n` 以便产生带有符合 RFC 的行分隔符的标头。

在 3.2 版本发生变更: 增加了 _linesep_ 参数。

`Header` 类还提供了一些方法以支持标准运算符和内置函数。

__str__()¶
~~~
    

~~~
以字符串形式返回 `Header` 的近似表示，使用不受限制的行长度。 所有部分都会使用指定编码格式转换为 unicode 并适当地连接起来。 任何带有 `'unknown-8bit'` 字符集的部分都会使用 `'replace'` 错误处理程序解码为 ASCII。

在 3.2 版本发生变更: 增加对 `'unknown-8bit'` 字符集的处理。

__eq__( _other_ )¶
~~~
    

~~~
这个方法允许你对两个 `Header` 实例进行相等比较。

__ne__( _other_ )¶
~~~
    

~~~
这个方法允许你对两个 `Header` 实例进行不等比较。

`email.header` 模块还提供了下列便捷函数。

email.header.decode_header( _header_ )¶
~~~
    

~~~
在不转换字符集的情况下对消息标头值进行解码。 _header_ 为标头值。

这个函数返回一个 `(decoded_string, charset)` 对的列表，其中包含标头的每个已解码部分。 对于标头的未编码部分 _charset_ 为 `None`，在其他情况下则为一个包含已编码字符串中所指定字符集名称的小写字符串。

以下是为示例代码:
~~~
    
    
~~~shell
>>> from email.header import decode_header
>>> decode_header('=?iso-8859-1?q?p=F6stal?=')
[(b'p\xf6stal', 'iso-8859-1')]
~~~

email.header.make_header( _decoded_seq_ , _maxlinelen =None_, _header_name =None_, _continuation_ws =' '_)¶

    

~~~
