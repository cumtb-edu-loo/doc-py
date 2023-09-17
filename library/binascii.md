# `binascii` \--- 二进制和 ASCII 码互转¶

* * *

The `binascii` module contains a number of methods to convert between binary and various ASCII-encoded binary representations. Normally, you will not use these functions directly but use wrapper modules like [`base64`](base64.md#module-base64 "base64: RFC 4648: Base16, Base32, Base64 Data Encodings; Base85 and Ascii85") instead. The `binascii` module contains low-level functions written in C for greater speed that are used by the higher-level modules.

备注

`a2b_*` 函数接受只含有 ASCII 码的Unicode 字符串。其他函数只接受 [字节类对象](../glossary.md#term-bytes-like-object) （例如 [`bytes`](stdtypes.md#bytes "bytes")，[`bytearray`](stdtypes.md#bytearray "bytearray") 和其他支持缓冲区协议的对象）。

在 3.3 版本发生变更: ASCII-only unicode strings are now accepted by the `a2b_*` functions.

`binascii` 模块定义了以下函数：

binascii.a2b_uu( _string_ )¶

    

~~~
将单行 uu 编码数据转换成二进制数据并返回。uu 编码每行的数据通常包含45 个（二进制）字节，最后一行除外。每行数据后面可能跟有空格。

binascii.b2a_uu( _data_ , _*_ , _backtick =False_)¶
~~~
    

~~~
将二进制数据转换为 ASCII 编码字符，返回值是转换后的行数据，包括换行符。 _data_ 的长度最多为45。如果 _backtick_ 为ture，则零由 `'`'` 而不是空格表示。

在 3.7 版本发生变更: 增加 _backtick_ 形参。

binascii.a2b_base64( _string_ , _/_ , _*_ , _strict_mode =False_)¶
~~~
    

~~~
将 base64 数据块转换成二进制并以二进制数据形式返回。一次可以传递多行数据。

如果 _strict_mode_ 为真值，则将只转换有效的 base64 数据。 无效的 base64 数据将会引发 `binascii.Error`。

有效的 base64:
~~~
    

~~~
  * 遵循 [**RFC 3548**](https://datatracker.ietf.org/doc/html/rfc3548.md)。

  * 仅包含来自 base64 字符表的字符。

  * 不包含填充后的额外数据（包括冗余填充、换行符等）。

  * 不以填充符打头。

在 3.11 版本发生变更: 增加了 _strict_mode_ 形参。

binascii.b2a_base64( _data_ , _*_ , _newline =True_)¶
~~~
    

~~~
将二进制数据转换为一行用 base64 编码的ASCII字符串。返回值是转换后的行数据，如果 _newline_ 为true，则返回值包括换行符。该函数的输出符合：rfc：3548。

在 3.6 版本发生变更: 增加 _newline_ 形参。

binascii.a2b_qp( _data_ , _header =False_)¶
~~~
    

~~~
将一个引号可打印的数据块转换成二进制数据并返回。一次可以转换多行。如果可选参数 _header_ 存在且为true，则数据中的下划线将被解码成空格。

binascii.b2a_qp( _data_ , _quotetabs =False_, _istext =True_, _header =False_)¶
~~~
    

~~~
将二进制数据转换为一行或多行带引号可打印编码的ASCII字符串。返回值是转换后的行数据。如果可选参数 _quotetabs_ 存在且为真值，则对所有制表符和空格进行编码。如果可选参数 _istext_ 存在且为真值，则不对新行进行编码，但将对尾随空格进行编码。如果可选参数 _header_ 存在且为true，则空格将被编码为下划线 [**RFC 1522**](https://datatracker.ietf.org/doc/html/rfc1522.md)。如果可选参数 _header_ 存在且为假值，则也会对换行符进行编码;不进行换行转换编码可能会破坏二进制数据流。

binascii.crc_hqx( _data_ , _value_ )¶
~~~
    

~~~
以 _value_ 作为初始 CRC 计算 _data_ 的16位 CRC 值，返回其结果。这里使用 CRC-CCITT 生成多项式 _x_ 16 \+ _x_ 12 \+ _x_ 5 \+ 1 ，通常表示为0x1021。该 CRC 被用于 binhex4 格式。

binascii.crc32( _data_ [, _value_ ])¶
~~~
    

~~~
计算 CRC-32，即 _data_ 的无符号 32 位校验和，初始 CRC 值为 _value_ 。 默认的初始 CRC 值为零。 该算法与 ZIP 文件校验和算法一致。 由于该算法被设计用作校验和算法，因此不适合用作通用哈希算法。 使用方式如下:
~~~
    
    
~~~
print(binascii.crc32(b"hello world"))
# Or, in two pieces:
crc = binascii.crc32(b"hello")
crc = binascii.crc32(b" world", crc)
print('crc32 = {:#010x}'.format(crc))
~~~

在 3.0 版本发生变更: 结果将总是不带符号的。

binascii.b2a_hex( _data_ [, _sep_ [, _bytes_per_sep=1_ ]])¶

binascii.hexlify( _data_ [, _sep_ [, _bytes_per_sep=1_ ]])¶

    

~~~
返回二进制数据 _data_ 的十六进制表示形式。 _data_ 的每个字节都被转换为相应的2位十六进制表示形式。因此返回的字节对象的长度是 _data_ 的两倍。

使用：[`bytes.hex()`](stdtypes.md#bytes.hex "bytes.hex") 方法也可以方便地实现相似的功能（但仅返回文本字符串）。

如果指定了 _sep_ ，它必须为单字符 str 或 bytes 对象。 它将被插入每个 _bytes_per_sep_ 输入字节之后。 分隔符位置默认从输出的右端开始计数，如果你希望从左端开始计数，请提供一个负的 _bytes_per_sep_ 值。
~~~
    
    
~~~shell
>>> import binascii
>>> binascii.b2a_hex(b'\xb9\x01\xef')
b'b901ef'
>>> binascii.hexlify(b'\xb9\x01\xef', '-')
b'b9-01-ef'
>>> binascii.b2a_hex(b'\xb9\x01\xef', b'_', 2)
b'b9_01ef'
>>> binascii.b2a_hex(b'\xb9\x01\xef', b' ', -2)
b'b901 ef'
~~~

在 3.8 版本发生变更: 添加了 _sep_ 和 _bytes_per_sep_ 形参。

binascii.a2b_hex( _hexstr_ )¶

binascii.unhexlify( _hexstr_ )¶

    

~~~
返回由十六进制字符串 _hexstr_ 表示的二进制数据。此函数功能与 `b2a_hex()` 相反。 _hexstr_ 必须包含偶数个十六进制数字（可以是大写或小写），否则会引发 `Error` 异常。

使用：[`bytes.fromhex()`](stdtypes.md#bytes.fromhex "bytes.fromhex") 类方法也实现相似的功能（仅接受文本字符串参数，不限制其中的空白字符）。

_exception _binascii.Error¶
~~~
    

~~~
通常是因为编程错误引发的异常。

_exception _binascii.Incomplete¶
~~~
    

~~~
数据不完整引发的异常。通常不是编程错误导致的，可以通过读取更多的数据并再次尝试来处理该异常。

参见

模块 [`base64`](base64.md#module-base64 "base64: RFC 4648: Base16, Base32, Base64 Data Encodings; Base85 and Ascii85")
~~~
    

~~~
支持在16，32，64，85进制中进行符合 RFC 协议的 base64 样式编码。

模块 [`quopri`](quopri.md#module-quopri "quopri: Encode and decode files using the MIME quoted-printable encoding.")
~~~
    

~~~
