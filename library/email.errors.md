# `email.errors`: 异常和缺陷类¶

**源代码:** [Lib/email/errors.py](https://github.com/python/cpython/tree/3.12/Lib/email/errors.py)

* * *

以下异常类在 `email.errors` 模块中定义：

_exception _email.errors.MessageError¶

    

~~~
这是 [`email`](email.md#module-email "email: Package supporting the parsing, manipulating, and generating email messages.") 包可以引发的所有异常的基类。 它源自标准异常 [`Exception`](exceptions.md#Exception "Exception") 类，这个类没有定义其他方法。

_exception _email.errors.MessageParseError¶
~~~
    

~~~
这是由 [`Parser`](email.parser.md#email.parser.Parser "email.parser.Parser") 类引发的异常的基类。它派生自 `MessageError`。 [`headerregistry`](email.headerregistry.md#module-email.headerregistry "email.headerregistry: Automatic Parsing of headers based on the field name") 使用的解析器也在内部使用这个类。

_exception _email.errors.HeaderParseError¶
~~~
    

~~~
在解析消息的 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 标头时，某些错误条件下会触发，此类派生自 `MessageParseError`。 如果在调用方法时内容类型未知，则 [`set_boundary()`](email.message.md#email.message.EmailMessage.set_boundary "email.message.EmailMessage.set_boundary") 方法将引发此错误。 当尝试创建一个看起来包含嵌入式标头的标头时 [`Header`](email.header.md#email.header.Header "email.header.Header") 可能会针对某些 base64 解码错误引发此错误（也就是说，应该是一个 没有前导空格并且看起来像标题的延续行）。

_exception _email.errors.BoundaryError¶
~~~
    

~~~
已弃用和不再使用的。

_exception _email.errors.MultipartConversionError¶
~~~
    

~~~
