# `base64` \--- Base16, Base32, Base64, Base85 数据编码¶

**源代码：** [Lib/base64.py](https://github.com/python/cpython/tree/3.12/Lib/base64.py)

* * *

此模块提供了将二进制数据编码为可打印的 ASCII 字符以及将这种编码格式解码回二进制数据的函数。 它为 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.md) 所定义的 Base16, Base32 和 Base64 算法及已成为事实标准的 Ascii85 和 Base85 编码格式提供了编码和解码函数。

[**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.md) 中的编码格式适用于编码二进制数据使得它能安全地通过电子邮件发送、用作 URL 的一部分，或者包括在 HTTP POST 请求之中。 此编码格式算法与 **uuencode** 程序并不相同。

此模块提供了两个接口。 较新的接口支持将 [字节类对象](../glossary.md#term-bytes-like-object) 编码为 ASCII [`bytes`](stdtypes.md#bytes "bytes")，以及将 [字节类对象](../glossary.md#term-bytes-like-object) 或包含 ASCII 的字符串解码为 [`bytes`](stdtypes.md#bytes "bytes")。 在 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.md) 中定义的几种 base-64 字母表（普通的以及 URL 和文件系统安全的）都受到支持。

旧的接口不提供从字符串的解码操作，但提供了操作 [文件对象](../glossary.md#term-file-object) 的编码和解码函数。旧接口只支持标准的 Base64 字母表，并且按照 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md) 的规范每 76 个字符增加一个换行符。注意：如果你需要支持 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md)，那么使用 [`email`](email.md#module-email "email: Package supporting the parsing, manipulating, and generating email messages.") 模块可能更加合适。

在 3.3 版本发生变更: 新的接口提供的解码函数现在已经支持只包含 ASCII 的 Unicode 字符串。

在 3.4 版本发生变更: 所有 [类字节对象](../glossary.md#term-bytes-like-object) 现在已经被所有编码和解码函数接受。添加了对 Ascii85/Base85 的支持。

新的接口提供：

base64.b64encode( _s_ , _altchars =None_)¶

    

~~~
对 [bytes-like object](../glossary.md#term-bytes-like-object) _s_ 进行 Base64 编码，并返回编码后的 [`bytes`](stdtypes.md#bytes "bytes")。

可选项 _altchars_ 必须是一个长度为 2 的 [bytes-like object](../glossary.md#term-bytes-like-object)，它指定了用于替换 `+` 和 `/` 的字符表。 这允许应用程序生成对 URL 或文件系统安全的 Base64 字符串。 默认值为 `None`，即使用标准 Base64 字符表。

如果 _altchars_ 的长度不为 2 则可以断言或引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 如果 _altchars_ 不是 [bytes-like object](../glossary.md#term-bytes-like-object) 则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

base64.b64decode( _s_ , _altchars =None_, _validate =False_)¶
~~~
    

~~~
解码 Base64 编码过的 [bytes-like object](../glossary.md#term-bytes-like-object) 或 ASCII 字符串 _s_ 并返回解码过的 [`bytes`](stdtypes.md#bytes "bytes")。

可选项 _altchars_ 必须是一个长度为 2 的 [bytes-like object](../glossary.md#term-bytes-like-object) 或 ASCII 字符串，它指定了用于替换 `+` 和 `/` 的字符表。

如果 _s_ 被不正确地填写，一个 [`binascii.Error`](binascii.md#binascii.Error "binascii.Error") 错误将被抛出。

如果 _validate_ 值为 `False` （默认情况），则在填充检查前，将丢弃既不在标准 base-64 字母表之中也不在备用字母表中的字符。如果 _validate_ 为 `True`，这些非 base64 字符将导致 [`binascii.Error`](binascii.md#binascii.Error "binascii.Error")。

有关严格 base64 检查的详情，请参阅 [`binascii.a2b_base64()`](binascii.md#binascii.a2b_base64 "binascii.a2b_base64")

如果 _altchars_ 不为 2 则可以断言设定或引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

base64.standard_b64encode( _s_ )¶
~~~
    

~~~
编码 [bytes-like object](../glossary.md#term-bytes-like-object) _s_ ，使用标准 Base64 字母表并返回编码过的 [`bytes`](stdtypes.md#bytes "bytes")。

base64.standard_b64decode( _s_ )¶
~~~
    

~~~
解码 [bytes-like object](../glossary.md#term-bytes-like-object) 或 ASCII 字符串 _s_ ，使用标准 Base64 字母表并返回编码过的 [`bytes`](stdtypes.md#bytes "bytes")。

base64.urlsafe_b64encode( _s_ )¶
~~~
    

~~~
编码 [bytes-like object](../glossary.md#term-bytes-like-object) _s_ ，使用 URL 与文件系统安全的字母表，使用 `-` 以及 `_` 代替标准 Base64 字母表中的 `+` 和 `/`。返回编码过的 [`bytes`](stdtypes.md#bytes "bytes")。结果中可能包含 `=`。

base64.urlsafe_b64decode( _s_ )¶
~~~
    

~~~
解码 [bytes-like object](../glossary.md#term-bytes-like-object) 或 ASCII 字符串 _s_ ，使用 URL 与文件系统安全的字母表，使用 `-` 以及 `_` 代替标准 Base64 字母表中的 `+` 和 `/`。返回解码过的 [`bytes`](stdtypes.md#bytes "bytes")

base64.b32encode( _s_ )¶
~~~
    

~~~
用 Base32 编码 [bytes-like object](../glossary.md#term-bytes-like-object) _s_ 并返回编码过的 [`bytes`](stdtypes.md#bytes "bytes")

base64.b32decode( _s_ , _casefold =False_, _map01 =None_)¶
~~~
    

~~~
解码 Base32 编码过的 [bytes-like object](../glossary.md#term-bytes-like-object) 或 ASCII 字符串 _s_ 并返回解码过的 [`bytes`](stdtypes.md#bytes "bytes")。

可选的 _casefold_ 是一个指定小写字幕是否可接受为输入的标志。为了安全考虑，默认值为 `False`。

[**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.md) 允许可以选择将数码 0 (zero) 映射为字母 O (oh)，并可以选择将数码 1 (one) 映射为字母 I (eye) 或字母 L (el)。 可选参数 _map01_ 在不为 `None` 时，指定数码 1 应当映射为哪个字母 (当 _map01_ 不为 `None` 时，数码 0 总是被映射为字母 O)。 出于安全考虑其默认值为 `None`，因而在输入中不允许 0 和 1。

如果 _s_ 被错误地填写或输入中存在字母表之外的字符，将抛出 [`binascii.Error`](binascii.md#binascii.Error "binascii.Error")。

base64.b32hexencode( _s_ )¶
~~~
    

~~~
类似于 `b32encode()` 但是使用 Extended Hex Alphabet，如 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.md) 所定义。

在 3.10 版本加入.

base64.b32hexdecode( _s_ , _casefold =False_)¶
~~~
    

~~~
类似于 `b32decode()` 但是使用 Extended Hex Alphabet，如 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.md) 所定义。

这个版本不允许数字 0（零）与字母 O（oh）和数字 1（一）与字母 I（eye）或字母 L （el）的映射，所有这些字符都包含在扩展的十六进制字母表中，不能互换。

在 3.10 版本加入.

base64.b16encode( _s_ )¶
~~~
    

~~~
用 Base16 编码 [bytes-like object](../glossary.md#term-bytes-like-object) _s_ 并返回编码过的 [`bytes`](stdtypes.md#bytes "bytes")

base64.b16decode( _s_ , _casefold =False_)¶
~~~
    

~~~
解码 Base16 编码过的 [bytes-like object](../glossary.md#term-bytes-like-object) 或 ASCII 字符串 _s_ 并返回解码过的 [`bytes`](stdtypes.md#bytes "bytes")。

可选的 _casefold_ 是一个指定小写字幕是否可接受为输入的标志。为了安全考虑，默认值为 `False`。

如果 _s_ 被错误地填写或输入中存在字母表之外的字符，将抛出 [`binascii.Error`](binascii.md#binascii.Error "binascii.Error")。

base64.a85encode( _b_ , _*_ , _foldspaces =False_, _wrapcol =0_, _pad =False_, _adobe =False_)¶
~~~
    

~~~
用 Ascii85 编码 [bytes-like object](../glossary.md#term-bytes-like-object) _s_ 并返回编码过的 [`bytes`](stdtypes.md#bytes "bytes")

_foldspaces_ 是一个可选的标志，使用特殊的短序列 'y' 代替 'btoa' 提供的 4 个连续空格 (ASCII 0x20)。这个特性不被 "标准" Ascii85 编码支持。

_wrapcol_ 控制了输出是否包含换行符 (`b'\n'`). 如果该值非零, 则每一行只有该值所限制的字符长度.

_pad_ 控制在编码之前输入是否填充为4的倍数。请注意，`btoa` 实现总是填充。

_adobe_ 控制编码后的字节序列是否要加上 `<~` 和 `~>`，这是 Adobe 实现所使用的。

在 3.4 版本加入.

base64.a85decode( _b_ , _*_ , _foldspaces =False_, _adobe =False_, _ignorechars =b' \t\n\r\x0b'_)¶
~~~
    

~~~
解码 Ascii85 编码过的 [bytes-like object](../glossary.md#term-bytes-like-object) 或 ASCII 字符串 _s_ 并返回解码过的 [`bytes`](stdtypes.md#bytes "bytes")。

_foldspaces_ 旗标指明是否应接受 'y' 短序列作为 4 个连续空格 (ASCII 0x20) 的快捷方式。 此特性不被 "标准" Ascii85 编码格式所支持。

_adobe_ 控制输入序列是否为 Adobe Ascii85 格式 (即附加 <~ 和 ~>)。

_ignorechars_ 应当是一个 [bytes-like object](../glossary.md#term-bytes-like-object) 或 ASCII 字符串，其中包含要从输入中忽略的字符。 这应当只包含空白字符，并且默认包含 ASCII 中所有的空白字符。

在 3.4 版本加入.

base64.b85encode( _b_ , _pad =False_)¶
~~~
    

~~~
用 base85（如 git 风格的二进制 diff 数据所用格式）编码 [bytes-like object](../glossary.md#term-bytes-like-object) _b_ 并返回编码后的 [`bytes`](stdtypes.md#bytes "bytes")。

如果 _pad_ 为真值，输入将以 `b'\0'` 填充以使其编码前长度为 4 字节的倍数。

在 3.4 版本加入.

base64.b85decode( _b_ )¶
~~~
    

~~~
解码 base85 编码过的 [bytes-like object](../glossary.md#term-bytes-like-object) 或 ASCII 字符串 _b_ 并返回解码过的 [`bytes`](stdtypes.md#bytes "bytes")。 如有必要，填充会被隐式地移除。

在 3.4 版本加入.

旧式接口:

base64.decode( _input_ , _output_ )¶
~~~
    

~~~
解码二进制 _input_ 文件的内容并将结果二进制数据写入 _output_ 文件。 _input_ 和 _output_ 必须为 [文件对象](../glossary.md#term-file-object). _input_ 将被读取直至 `input.readline()` 返回空字节串对象。

base64.decodebytes( _s_ )¶
~~~
    

~~~
解码 [bytes-like object](../glossary.md#term-bytes-like-object) _s_ ，该对象必须包含一行或多行 base64 编码的数据，并返回已解码的 [`bytes`](stdtypes.md#bytes "bytes")。

在 3.1 版本加入.

base64.encode( _input_ , _output_ )¶
~~~
    

~~~
编码二进制 _input_ 文件的内容并将经 base64 编码的数据写入 _output_ 文件。 _input_ 和 _output_ 必须为 [文件对象](../glossary.md#term-file-object)。 _input_ 将被读取直到 `input.read()` 返回空字节串对象。 `encode()` 会在每输出 76 个字节之后插入一个换行符 (`b'\n'`)，并会确保输出总是以换行符来结束，如 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md) (MIME) 所规定的那样。

base64.encodebytes( _s_ )¶
~~~
    

~~~
编码 [bytes-like object](../glossary.md#term-bytes-like-object) _s_ ，其中可以包含任意二进制数据，并返回包含经 base64 编码数据的 [`bytes`](stdtypes.md#bytes "bytes")，每输出 76 个字节之后将带一个换行符 (`b'\n'`)，并会确保在末尾也有一个换行符，如 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md) (MIME) 所规定的那样。

在 3.1 版本加入.

此模块的一个使用示例:
~~~
    
    
~~~shell
>>> import base64
>>> encoded = base64.b64encode(b'data to be encoded')
>>> encoded
b'ZGF0YSB0byBiZSBlbmNvZGVk'
>>> data = base64.b64decode(encoded)
>>> data
b'data to be encoded'
~~~

## 安全考量¶

在 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.md) 中新增了安全事项部分（第 12 节）；对于要部署到生产环境的任何代码都建议充分考虑此安全事项部分。

参见

模块 [`binascii`](binascii.md#module-binascii "binascii: Tools for converting between binary and various ASCII-encoded binary representations.")

    

~~~
支持模块，包含ASCII到二进制和二进制到ASCII转换。

[**RFC 1521**](https://datatracker.ietf.org/doc/html/rfc1521.md) \- MIME (Multipurpose Internet Mail Extensions) 第一部分：规定并描述因特网消息体的格式的机制。
~~~
    

~~~
