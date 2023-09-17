# `email.mime`: 从头创建电子邮件和 MIME 对象¶

**源代码:** [Lib/email/mime/](https://github.com/python/cpython/tree/3.12/Lib/email/mime/)

* * *

此模块是旧版 (`Compat32`) 电子邮件 API 的组成部分。 它的功能在新版 API 中被 [`contentmanager`](email.contentmanager.md#module-email.contentmanager "email.contentmanager: Storing and Retrieving Content from MIME Parts") 部分替代，但在某些应用中这些类仍可能有用，即使是在非旧版代码中。

通常，你是通过传递一个文件或一些文本到解析器来获得消息对象结构体的，解析器会解析文本并返回根消息对象。 不过你也可以从头开始构建一个完整的消息结构体，甚至是手动构建单独的 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 对象。 实际上，你也可以接受一个现有的结构体并添加新的 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 对象并移动它们。 这为切片和分割 MIME 消息提供了非常方便的接口。

你可以通过创建 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 实例并手动添加附件和所有适当的标头来创建一个新的对象结构体。 不过对于 MIME 消息来说，[`email`](email.md#module-email "email: Package supporting the parsing, manipulating, and generating email messages.") 包提供了一些便捷子类来让事情变得更容易。

这些类列示如下:

_class _email.mime.base.MIMEBase( __maintype_ , __subtype_ , _*_ , _policy =compat32_, _** _params_)¶

    

~~~
模块: `email.mime.base`

这是 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 的所有 MIME 专属子类。 通常你不会创建专门的 `MIMEBase` 实例，尽管你可以这样做。 `MIMEBase` 主要被提供用来作为更具体的 MIME 感知子类的便捷基类。

__maintype_ 是 _Content-Type_ 的主类型 (例如 _text_ 或 _image_ )，而 __subtype_ 是 _Content-Type_ 的次类型 (例如 _plain_ 或 _gif_ )。 __params_ 是一个形参键/值字典并会被直接传递给 [`Message.add_header`](email.compat32-message.md#email.message.Message.add_header "email.message.Message.add_header")。

如果指定了 _policy_ (默认为 [`compat32`](email.policy.md#email.policy.Compat32 "email.policy.Compat32") 策略)，它将被传递给 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message")。

`MIMEBase` 类总是会添加一个 _Content-Type_ 标头 (基于 __maintype_ , __subtype_ 和 __params_ )，以及一个 _MIME-Version_ 标头 (总是设为 `1.0`)。

在 3.6 版本发生变更: 添加了 _policy_ 仅限关键字形参。

_class _email.mime.nonmultipart.MIMENonMultipart¶
~~~
    

~~~
模块: `email.mime.nonmultipart`

`MIMEBase` 的子类，这是用于非 _multipart_ MIME 消息的中间基类。 这个类的主要目标是避免使用 [`attach()`](email.compat32-message.md#email.message.Message.attach "email.message.Message.attach") 方法，该方法仅对 _multipart_ 消息有意义。 如果 [`attach()`](email.compat32-message.md#email.message.Message.attach "email.message.Message.attach") 被调用，则会引发 [`MultipartConversionError`](email.errors.md#email.errors.MultipartConversionError "email.errors.MultipartConversionError") 异常。

_class _email.mime.multipart.MIMEMultipart( __subtype ='mixed'_, _boundary =None_, __subparts =None_, _*_ , _policy =compat32_, _** _params_)¶
~~~
    

~~~
模块: `email.mime.multipart`

`MIMEBase` 的子类，这是用于 _multipart_ MIME 消息的中间基类。 可选的 __subtype_ 默认为 _mixed_ ，但可被用来指定消息的子类型。 将会在消息对象中添加一个 _multipart/_subtype_ 的 _Content-Type_ 标头。 并还将添加一个 _MIME-Version_ 标头。

可选的 _boundary_ 是多部分边界字符串。 当为 `None` (默认值) 时，则会在必要时（例如当消息被序列化时）计算边界。

__subparts_ 是载荷初始子部分的序列。 此序列必须可以被转换为列表。 你总是可以使用 [`Message.attach`](email.compat32-message.md#email.message.Message.attach "email.message.Message.attach") 方法将新的子部分附加到消息中。

可选的 _policy_ 参数默认为 [`compat32`](email.policy.md#email.policy.Compat32 "email.policy.Compat32")。

用于 _Content-Type_ 标头的附加形参会从关键字参数中获取，或者传入到 __params_ 参数，该参数是一个关键字的字典。

在 3.6 版本发生变更: 添加了 _policy_ 仅限关键字形参。

_class _email.mime.application.MIMEApplication( __data_ , __subtype ='octet-stream'_, __encoder =email.encoders.encode_base64_, _*_ , _policy =compat32_, _** _params_)¶
~~~
    

~~~
模块: `email.mime.application`

`MIMENonMultipart` 的子类，`MIMEApplication` 类被用来表示主类型为 _application_ 的 MIME 消息。 __data_ 为包含原始应用程序数据的字节串。 可选的 __subtype_ 指定 MIME 子类型并默认为 _octet-stream_ 。

可选的 __encoder_ 是一个可调用对象（即函数），它将执行实际的数据编码以便传输。 这个可调用对象接受一个参数，该参数是 `MIMEApplication` 的实例。 它应当使用 [`get_payload()`](email.compat32-message.md#email.message.Message.get_payload "email.message.Message.get_payload") 和 [`set_payload()`](email.compat32-message.md#email.message.Message.set_payload "email.message.Message.set_payload") 来将载荷改为已编码形式。 它还应根据需要将任何 _Content-Transfer-Encoding_ 或其他标头添加到消息对象中。 默认编码格式为 base64。 请参阅 [`email.encoders`](email.encoders.md#module-email.encoders "email.encoders: Encoders for email message payloads.") 模块来查看内置编码器列表。

可选的 _policy_ 参数默认为 [`compat32`](email.policy.md#email.policy.Compat32 "email.policy.Compat32")。

__params_ 会被直接传递给基类的构造器。

在 3.6 版本发生变更: 添加了 _policy_ 仅限关键字形参。

_class _email.mime.audio.MIMEAudio( __audiodata_ , __subtype =None_, __encoder =email.encoders.encode_base64_, _*_ , _policy =compat32_, _** _params_)¶
~~~
    

~~~
模块: `email.mime.audio`

`MIMENonMultipart` 的子类，`MIMEAudio` 类被用来创建主类型为 _audio_ 的 MIME 消息。 __audiodata_ 是包含原始音频数据的字节串。 如果此数据可作为 au, wav, aiff 或 aifc 来解码，则其子类型将被自动包括在 _Content-Type_ 标头中。 在其他情况下你可以通过 __subtype_ 参数显式地指定音频子类型。 如果无法猜测出次类型并且未给出 __subtype_ ，则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

可选的 __encoder_ 是一个可调用对象（即函数），它将执行实际的音频数据编码以便传输。 这个可调用对象接受一个参数，该参数是 `MIMEAudio` 的实例。 它应当使用 [`get_payload()`](email.compat32-message.md#email.message.Message.get_payload "email.message.Message.get_payload") 和 [`set_payload()`](email.compat32-message.md#email.message.Message.set_payload "email.message.Message.set_payload") 来将载荷改为已编码形式。 它还应根据需要将任何 _Content-Transfer-Encoding_ 或其他标头添加到消息对象中。 默认编码格式为 base64。 请参阅 [`email.encoders`](email.encoders.md#module-email.encoders "email.encoders: Encoders for email message payloads.") 模块来查看内置编码器列表。

可选的 _policy_ 参数默认为 [`compat32`](email.policy.md#email.policy.Compat32 "email.policy.Compat32")。

__params_ 会被直接传递给基类的构造器。

在 3.6 版本发生变更: 添加了 _policy_ 仅限关键字形参。

_class _email.mime.image.MIMEImage( __imagedata_ , __subtype =None_, __encoder =email.encoders.encode_base64_, _*_ , _policy =compat32_, _** _params_)¶
~~~
    

~~~
模块: `email.mime.image`

`MIMENonMultipart` 的子类，`MIMEImage` 类被用来创建主类型为 _image_ 的 MIME 消息对象。 __imagedata_ 是包含原始图像数据的字节串。 如果此数据类型可以被检测（将尝试 jpeg, png, gif, tiff, rgb, pbm, pgm, ppm, rast, xbm, bmp, webp 和 exr 类型），则其子类型将被自动包括在 _Content-Type_ 标头中。 在其他情况下你可以通过 __subtype_ 参数显式地指定图像子类型。 如果无法猜测出次类型并且未给出 __subtype_ ，则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

可选的 __encoder_ 是一个可调用对象（即函数），它将执行实际的图像数据编码以便传输。 这个可调用对象接受一个参数，该参数是 `MIMEImage` 的实例。 它应当使用 [`get_payload()`](email.compat32-message.md#email.message.Message.get_payload "email.message.Message.get_payload") 和 [`set_payload()`](email.compat32-message.md#email.message.Message.set_payload "email.message.Message.set_payload") 来将载荷改为已编码形式。 它还应根据需要将任何 _Content-Transfer-Encoding_ 或其他标头添加到消息对象中。 默认编码格式为 base64。 请参阅 [`email.encoders`](email.encoders.md#module-email.encoders "email.encoders: Encoders for email message payloads.") 模块来查看内置编码器列表。

可选的 _policy_ 参数默认为 [`compat32`](email.policy.md#email.policy.Compat32 "email.policy.Compat32")。

__params_ 会被直接传递给 `MIMEBase` 构造器。

在 3.6 版本发生变更: 添加了 _policy_ 仅限关键字形参。

_class _email.mime.message.MIMEMessage( __msg_ , __subtype ='rfc822'_, _*_ , _policy =compat32_)¶
~~~
    

~~~
模块: `email.mime.message`

`MIMENonMultipart` 的子类，`MIMEMessage` 类被用来创建主类型为 _message_ 的 MIME 对象。 __msg_ 将被用作载荷，并且必须为 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 类（或其子类）的实例，否则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

可选的 __subtype_ 设置消息的子类型；它的默认值为 _rfc822_ 。

可选的 _policy_ 参数默认为 [`compat32`](email.policy.md#email.policy.Compat32 "email.policy.Compat32")。

在 3.6 版本发生变更: 添加了 _policy_ 仅限关键字形参。

_class _email.mime.text.MIMEText( __text_ , __subtype ='plain'_, __charset =None_, _*_ , _policy =compat32_)¶
~~~
    

~~~
