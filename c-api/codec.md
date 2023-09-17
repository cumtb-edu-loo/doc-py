# 编解码器注册与支持功能¶

int PyCodec_Register([PyObject](structures.md#c.PyObject "PyObject") *search_function)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

注册一个新的编解码器搜索函数。

作为其附带影响，如果 `encodings` 包尚未加载，则会尝试加载它，以确保它在搜索函数列表中始终排在第一位。

int PyCodec_Unregister([PyObject](structures.md#c.PyObject "PyObject") *search_function)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

注销一个编解码器搜索函数并清空注册表缓存。 如果指定搜索函数未被注册，则不做任何操作。 成功时返回 0。 出错时引发一个异常并返回 -1。

在 3.10 版本加入.

int PyCodec_KnownEncoding(const char *encoding)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

根据注册的给定 _encoding_ 的编解码器是否已存在而返回 `1` 或 `0`。此函数总能成功。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_Encode([PyObject](structures.md#c.PyObject "PyObject") *object, const char *encoding, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

泛型编解码器基本编码 API。

_object_ 使用由 _errors_ 所定义的错误处理方法传递给定 _encoding_ 的编码器函数。 _errors_ 可以为 `NULL` 表示使用为编码器所定义的默认方法。 如果找不到编码器则会引发 [`LookupError`](../library/exceptions.md#LookupError "LookupError")。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_Decode([PyObject](structures.md#c.PyObject "PyObject") *object, const char *encoding, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

泛型编解码器基本解码 API。

_object_ 使用由 _errors_ 所定义的错误处理方法传递给定 _encoding_ 的解码器函数。 _errors_ 可以为 `NULL` 表示使用为编解码器所定义的默认方法。 如果找不到编解码器则会引发 [`LookupError`](../library/exceptions.md#LookupError "LookupError")。

## Codec 查找API¶

在下列函数中， _encoding_ 字符串会被查找并转换为小写字母形式，这使得通过此机制查找编码格式实际上对大小写不敏感。 如果未找到任何编解码器，则将设置 [`KeyError`](../library/exceptions.md#KeyError "KeyError") 并返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_Encoder(const char *encoding)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

为给定的 _encoding_ 获取一个编码器函数。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_Decoder(const char *encoding)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

为给定的 _encoding_ 获取一个解码器函数。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_IncrementalEncoder(const char *encoding, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

为给定的 _encoding_ 获取一个 [`IncrementalEncoder`](../library/codecs.md#codecs.IncrementalEncoder "codecs.IncrementalEncoder") 对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_IncrementalDecoder(const char *encoding, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

为给定的 _encoding_ 获取一个 [`IncrementalDecoder`](../library/codecs.md#codecs.IncrementalDecoder "codecs.IncrementalDecoder") 对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_StreamReader(const char *encoding, [PyObject](structures.md#c.PyObject "PyObject") *stream, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

为给定的 _encoding_ 获取一个 [`StreamReader`](../library/codecs.md#codecs.StreamReader "codecs.StreamReader") 工厂函数。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_StreamWriter(const char *encoding, [PyObject](structures.md#c.PyObject "PyObject") *stream, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

为给定的 _encoding_ 获取一个 [`StreamWriter`](../library/codecs.md#codecs.StreamWriter "codecs.StreamWriter") 工厂函数。

## 用于Unicode编码错误处理程序的注册表API¶

int PyCodec_RegisterError(const char *name, [PyObject](structures.md#c.PyObject "PyObject") *error)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

在给定的 _name_ 之下注册错误处理回调函数 _error_ 。 该回调函数将在一个编解码器遇到无法编码的字符/无法解码的字节数据并且 _name_ 被指定为 encode/decode 函数调用的 error 形参时由该编解码器来调用。

该回调函数会接受一个 [`UnicodeEncodeError`](../library/exceptions.md#UnicodeEncodeError "UnicodeEncodeError"), [`UnicodeDecodeError`](../library/exceptions.md#UnicodeDecodeError "UnicodeDecodeError") 或 [`UnicodeTranslateError`](../library/exceptions.md#UnicodeTranslateError "UnicodeTranslateError") 的实例作为单独参数，其中包含关于有问题字符或字节序列及其在原始序列的偏移量信息（请参阅 [Unicode 异常对象](exceptions.md#unicodeexceptions) 了解提取此信息的函数详情）。 该回调函数必须引发给定的异常，或者返回一个包含有问题序列及相应替换序列的二元组，以及一个表示偏移量的整数，该整数指明应在什么位置上恢复编码/解码操作。

成功则返回 `0`，失败则返回 `-1`。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_LookupError(const char *name)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

查找在 _name_ 之下注册的错误处理回调函数。 作为特例还可以传入 `NULL`，在此情况下将返回针对 "strict" 的错误处理回调函数。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_StrictErrors([PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable)._

引发 _exc_ 作为异常。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_IgnoreErrors([PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

忽略 unicode 错误，跳过错误的输入。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_ReplaceErrors([PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

使用 `?` 或 `U+FFFD` 替换 unicode 编码错误。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_XMLCharRefReplaceErrors([PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

使用 XML 字符引用替换 unicode 编码错误。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_BackslashReplaceErrors([PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

使用反斜杠转义符 (`\x`, `\u` 和 `\U`) 替换 unicode 编码错误。

[PyObject](structures.md#c.PyObject "PyObject") *PyCodec_NameReplaceErrors([PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

使用 `\N{...}` 转义符替换 unicode 编码错误。

在 3.5 版本加入.

