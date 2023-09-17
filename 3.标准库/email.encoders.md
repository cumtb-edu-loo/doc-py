# `email.encoders`: 编码器¶

**源代码:** [Lib/email/encoders.py](https://github.com/python/cpython/tree/3.12/Lib/email/encoders.py)

* * *

此模块是旧版 (`Compat32`) email API 的组成部分。 在新版 API 中将由 [`set_content()`](email.message.md#email.message.EmailMessage.set_content "email.message.EmailMessage.set_content") 方法的 _cte_ 形参提供该功能。

此模块在 Python 3 中已弃用。 这里提供的函数不应被显式地调用，因为 [`MIMEText`](email.mime.md#email.mime.text.MIMEText "email.mime.text.MIMEText") 类会在类实例化期间使用 __subtype_ 和 __charset_ 值来设置内容类型和 CTE 标头。

本节中的其余文本是此模块的原始文档。

当创建全新的 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 对象时，你经常需要对载荷编码以便通过兼容的邮件服务器进行传输。 对于包含二进制数据的 _image/*_ 和 _text/*_ 类型的消息来说尤其如此。

[`email`](email.md#module-email "email: Package supporting the parsing, manipulating, and generating email messages.") 包在其 `encoders` 模块中提供了一些方便的编码器。 这些编码器实际上由 [`MIMEAudio`](email.mime.md#email.mime.audio.MIMEAudio "email.mime.audio.MIMEAudio") 和 [`MIMEImage`](email.mime.md#email.mime.image.MIMEImage "email.mime.image.MIMEImage") 类构造器使用以提供默认编码格式。 所有编码器函数都只接受一个参数，即要编码的消息对象。 它们通常会提取有效截荷，对其进行编码，并将载荷重置为新近编码的值。 它们还应当相应地设置 _Content-Transfer-Encoding_ 标头。

请注意，这些函数对于多段消息没有意义。 它们必须应用到各个单独的段上面，而不是整体。如果直接传递一个多段类型的消息，会产生一个 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError") 错误。

下面是提供的编码函数：

email.encoders.encode_quopri( _msg_ )¶

    

~~~
将有效数据编码为经转换的可打印形式，并将 _Content-Transfer-Encoding_ 标头设置为 `quoted-printable` [1]。 当大多数实际的数据是普通的可打印数据但包含少量不可打印的字符时，这是一个很好的编码。

email.encoders.encode_base64( _msg_ )¶
~~~
    

~~~
将有效载荷编码为 base64 形式，并将 _Content-Transfer-Encoding_ 标头设为 `base64`。 当你的载荷主要包含不可打印数据时这是一种很好用的编码格式，因为它比 quoted-printable 更紧凑。 base64 编码格式的缺点是它会使文本变成人类不可读的形式。

email.encoders.encode_7or8bit( _msg_ )¶
~~~
    

~~~
此函数并不实际改变消息的有效载荷，但它会基于载荷数据将 _Content-Transfer-Encoding_ 标头相应地设为 `7bit` 或 `8bit`。

email.encoders.encode_noop( _msg_ )¶
~~~
    

~~~
