# `quopri` \--- 编码与解码经过 MIME 转码的可打印数据¶

**源代码:** [Lib/quopri.py](https://github.com/python/cpython/tree/3.12/Lib/quopri.py)

* * *

此模块会执行转换后可打印的传输编码与解码，具体定义见 [**RFC 1521**](https://datatracker.ietf.org/doc/html/rfc1521.md): "MIME (Multipurpose Internet Mail Extensions) Part One: Mechanisms for Specifying and Describing the Format of Internet Message Bodies"。 转换后可打印的编码格式被设计用于只包含相对较少的不可打印字符的数据；如果存在大量这样的字符，通过 [`base64`](base64.md#module-base64 "base64: RFC 4648: Base16, Base32, Base64 Data Encodings; Base85 and Ascii85") 模块所提供的 base64 编码方案会更为紧凑，例如当发送图片文件时。

quopri.decode( _input_ , _output_ , _header =False_)¶

    

~~~
解码 _input_ 文件的内容并将已解码二进制数据结果写入 _output_ 文件。 _input_ 和 _output_ 必须为 [二进制文件对象](../glossary.md#term-file-object)。 如果提供了可选参数 _header_ 且为真值，下划线将被解码为空格。 此函数可用于解码“Q”编码的头数据，具体描述见 [**RFC 1522**](https://datatracker.ietf.org/doc/html/rfc1522.md): "MIME (Multipurpose Internet Mail Extensions) Part Two: Message Header Extensions for Non-ASCII Text"。

quopri.encode( _input_ , _output_ , _quotetabs_ , _header =False_)¶
~~~
    

~~~
编码 _input_ 文件的内容并将转换后可打印的数据结果写入 _output_ 文件。 _input_ 和 _output_ 必须为 [二进制文件对象](../glossary.md#term-file-object). _quotetabs_ 是一个非可选的旗标，它控制是否要编码内嵌的空格与制表符；当为真值时将编码此类内嵌空白符，当为假值时则保持原样不进行编码。 请注意出现在行尾的空格与制表符总是会被编码，具体描述见 [**RFC 1521**](https://datatracker.ietf.org/doc/html/rfc1521.md)。 _header_ 旗标控制空格符是否要编码为下划线，具体描述见 [**RFC 1522**](https://datatracker.ietf.org/doc/html/rfc1522.md)。

quopri.decodestring( _s_ , _header =False_)¶
~~~
    

~~~
类似 `decode()`，区别在于它接受一个源 [`bytes`](stdtypes.md#bytes "bytes") 并返回对应的已解码 [`bytes`](stdtypes.md#bytes "bytes")。

quopri.encodestring( _s_ , _quotetabs =False_, _header =False_)¶
~~~
    

~~~
类型 `encode()`，区别在于它接受一个源 [`bytes`](stdtypes.md#bytes "bytes") 并返回对应的已编码 [`bytes`](stdtypes.md#bytes "bytes")。 在默认情况下，它会发送 `False` 值给 `encode()` 函数的 _quotetabs_ 形参。

参见

模块 [`base64`](base64.md#module-base64 "base64: RFC 4648: Base16, Base32, Base64 Data Encodings; Base85 and Ascii85")
~~~
    

~~~
