# `email.generator`: 生成 MIME 文档¶

**源代码:** [Lib/email/generator.py](https://github.com/python/cpython/tree/3.12/Lib/email/generator.py)

* * *

One of the most common tasks is to generate the flat (serialized) version of the email message represented by a message object structure. You will need to do this if you want to send your message via [`smtplib.SMTP.sendmail()`](smtplib.md#smtplib.SMTP.sendmail "smtplib.SMTP.sendmail"), or print the message on the console. Taking a message object structure and producing a serialized representation is the job of the generator classes.

与 [`email.parser`](email.parser.md#module-email.parser "email.parser: Parse flat text email messages to produce a message object structure.") 模块一样，你并不会受限于已捆绑生成器的功能；你可以自己从头写一个。 不过，已捆绑生成器知道如何以符合标准的方式来生成大多数电子邮件，应该能够很好地处理 MIME 和非 MIME 电子邮件消息，并且被设计为面向字节的解析和生成操作是互逆的，它假定两者都使用同样的非转换型 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages")。 也就是说，通过 [`BytesParser`](email.parser.md#email.parser.BytesParser "email.parser.BytesParser") 类来解析序列化字节流然后再使用 `BytesGenerator` 来重新生成序列化字节流应当得到与输入相同的结果 [1]。 （而另一方面，在由程序所构造的 [`EmailMessage`](email.message.md#email.message.EmailMessage "email.message.EmailMessage") 上使用生成器可能导致对默认填入的 [`EmailMessage`](email.message.md#email.message.EmailMessage "email.message.EmailMessage") 对象的改变。。）

可以使用 `Generator` 类将消息扁平化为文本（而非二进制数据）的序列化表示形式，但是由于 Unicode 无法直接表示二进制数据，因此消息有必要被转换为仅包含 ASCII 字符的数据，这将使用标准电子邮件 RFC 内容传输编码格式技术来编码电子邮件消息以便通过非 “8 比特位兼容”的信道来传输。

为了适应 SMIME 签名消息的可重现处理过程，`Generator` 禁用了针对 `multipart/signed` 类型的消息部分及所有子部分的标头折叠。

_class _email.generator.BytesGenerator( _outfp_ , _mangle_from_ =None_, _maxheaderlen =None_, _*_ , _policy =None_)¶

    

~~~
返回一个 `BytesGenerator` 对象，该对象将把提供给 `flatten()` 方法的任何消息或者提供给 `write()` 方法的任何经过代理转义编码的文本写入到 [file-like object](../glossary.md#term-file-like-object) _outfp_ 。 _outfp_ 必须支持接受二进制数据的 `write` 方法。

如果可选的 _mangle_from__ 为 `True`，则会将一个 `>` 字符放到消息体中恰好以字符串 `"From "` 打头，即开头文本为 `From` 加一个空格的任何行的前面。 _mangle_from__ 默认为 _policy_ 的 [`mangle_from_`](email.policy.md#email.policy.Policy.mangle_from_ "email.policy.Policy.mangle_from_") 设置值 (对于 [`compat32`](email.policy.md#email.policy.compat32 "email.policy.compat32") 策略为 `True` 而对于所有其他策略则为 `False`)。 _mangle_from__ 被设计为在当消息以 Unix mbox 格式存储时使用 (参见 [`mailbox`](mailbox.md#module-mailbox "mailbox: Manipulate mailboxes in various formats") 和 [WHY THE CONTENT-LENGTH FORMAT IS BAD](https://www.jwz.org/doc/content-length.md))。

如果 _maxheaderlen_ 不为 `None`，则重新折叠任何长于 _maxheaderlen_ 的标头行，或者如果为 `0`，则不重新包装任何标头。 如果 _manheaderlen_ 为 `None` (默认值)，则根据 _policy_ 设置包装标头和其他消息行。

如果指定了 _policy_ ，则使用该策略来控制消息的生成。 如果 _policy_ 为 `None` (默认值)，则使用与传递给 `flatten` 的 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 或 [`EmailMessage`](email.message.md#email.message.EmailMessage "email.message.EmailMessage") 对象相关联的策略来控制消息的生成。 请参阅 [`email.policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 了解有关 _policy_ 所控制内容的详情。

在 3.2 版本加入.

在 3.3 版本发生变更: 添加了 _policy_ 关键字。

在 3.6 版本发生变更: _mangle_from__ 和 _maxheaderlen_ 形参的默认行为是遵循策略。

flatten( _msg_ , _unixfrom =False_, _linesep =None_)¶
~~~
    

~~~
将将以 _msg_ 为根的消息对象结构体的文本表示形式打印到创建 `BytesGenerator` 实例时指定的输出文件。

如果 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 选项 [`cte_type`](email.policy.md#email.policy.Policy.cte_type "email.policy.Policy.cte_type") 为 `8bit` (默认值)，则会将未被修改的原始已解析消息中的任何标头拷贝到输出，其中会重新生成与原始数据相同的高比特位组字节数据，并保留具有它们的任何消息体部分的非 ASCII _Content-Transfer-Encoding_ 。 如果 `cte_type` 为 `7bit`，则会根据需要使用兼容 ASCII 的 _Content-Transfer-Encoding_ 来转换高比特位组字节数据。 也就是说，将具有非 ASCII _Content-Transfer-Encoding_ ( _Content-Transfer-Encoding: 8bit_ ) 的部分转换为兼容 ASCII 的 _Content-Transfer-Encoding_ ，并使用 MIME `unknown-8bit` 字符集来编码标头中不符合 RFC 的非 ASCII 字节数据，以使其符合 RFC。

如果 _unixfrom_ 为 `True`，则会在根消息对象的第一个 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 标头之前打印 Unix mailbox 格式 (参见 [`mailbox`](mailbox.md#module-mailbox "mailbox: Manipulate mailboxes in various formats")) 所使用的封包标头分隔符。 如果根对象没有封包标头，则会创建一个标准标头。 默认值为 `False`。 请注意对于子部分来说，不会打印任何封包标头。

如果 _linesep_ 不为 `None`，则会将其用作扁平化消息的所有行之间的分隔符。 如果 _linesep_ 为 `None` (默认值)，则使用在 _policy_ 中指定的值。

clone( _fp_ )¶
~~~
    

~~~
返回此 `BytesGenerator` 实例的独立克隆，具有完全相同的选项设置，而 _fp_ 为新的 _outfp_ 。

write( _s_ )¶
~~~
    

~~~
使用 `ASCII` 编解码器和 `surrogateescape` 错误处理程序编码 _s_ ，并将其传递给传入到 `BytesGenerator` 的构造器的 _outfp_ 的 _write_ 方法 。

作为一个便捷工具，[`EmailMessage`](email.message.md#email.message.EmailMessage "email.message.EmailMessage") 提供了 [`as_bytes()`](email.message.md#email.message.EmailMessage.as_bytes "email.message.EmailMessage.as_bytes") 和 `bytes(aMessage)` (即 [`__bytes__()`](email.message.md#email.message.EmailMessage.__bytes__ "email.message.EmailMessage.__bytes__")) 等方法，它们简单地生成一个消息对象的序列化二进制表示形式。 更多细节请参阅 [`email.message`](email.message.md#module-email.message "email.message: The base class representing email messages.")。

因为字符串无法表示二进制数据，`Generator` 类必须将任何消息中扁平化的任何二进制数据转换为兼容 ASCII 的格式，具体将其转换为兼容 ASCII 的 _Content-Transfer_Encoding_ 。 使用电子邮件 RFC 的术语，你可以将其视作 `Generator` 序列化为不 "支持 8 比特" 的 I/O 流。 换句话说，大部分应用程序将需要使用 `BytesGenerator`，而非 `Generator`。

_class _email.generator.Generator( _outfp_ , _mangle_from_ =None_, _maxheaderlen =None_, _*_ , _policy =None_)¶
~~~
    

~~~
返回一个 `Generator`，它将把提供给 `flatten()` 方法的任何消息，或者提供给 `write()` 方法的任何文本写入到 [file-like object](../glossary.md#term-file-like-object) _outfp_ 。 _outfp_ 必须支持接受字符串数据的 `write` 方法。

如果可选的 _mangle_from__ 为 `True`，则会将一个 `>` 字符放到消息体中恰好以字符串 `"From "` 打头，即开头文本为 `From` 加一个空格的任何行的前面。 _mangle_from__ 默认为 _policy_ 的 [`mangle_from_`](email.policy.md#email.policy.Policy.mangle_from_ "email.policy.Policy.mangle_from_") 设置值 (对于 [`compat32`](email.policy.md#email.policy.compat32 "email.policy.compat32") 策略为 `True` 而对于所有其他策略则为 `False`)。 _mangle_from__ 被设计为在当消息以 Unix mbox 格式存储时使用 (参见 [`mailbox`](mailbox.md#module-mailbox "mailbox: Manipulate mailboxes in various formats") 和 [WHY THE CONTENT-LENGTH FORMAT IS BAD](https://www.jwz.org/doc/content-length.md))。

如果 _maxheaderlen_ 不为 `None`，则重新折叠任何长于 _maxheaderlen_ 的标头行，或者如果为 `0`，则不重新包装任何标头。 如果 _manheaderlen_ 为 `None` (默认值)，则根据 _policy_ 设置包装标头和其他消息行。

如果指定了 _policy_ ，则使用该策略来控制消息的生成。 如果 _policy_ 为 `None` (默认值)，则使用与传递给 `flatten` 的 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 或 [`EmailMessage`](email.message.md#email.message.EmailMessage "email.message.EmailMessage") 对象相关联的策略来控制消息的生成。 请参阅 [`email.policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 了解有关 _policy_ 所控制内容的详情。

在 3.3 版本发生变更: 添加了 _policy_ 关键字。

在 3.6 版本发生变更: _mangle_from__ 和 _maxheaderlen_ 形参的默认行为是遵循策略。

flatten( _msg_ , _unixfrom =False_, _linesep =None_)¶
~~~
    

~~~
将以 _msg_ 为根的消息对象结构体的文本表示形式打印到当 `Generator` 实例被创建时所指定的输出文件。

如果 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 选项 [`cte_type`](email.policy.md#email.policy.Policy.cte_type "email.policy.Policy.cte_type") 为 `8bit`，则视同选项被设为 `7bit` 来生成消息。 （这是必需的，因为字符串无法表示非 ASCII 字节数据。） 将使用兼容 ASCII 的 _Content-Transfer-Encoding_ 按需转换任何具有高比特位组的字节数据。 也就是说，将具有非 ASCII _Content-Transfer-Encoding_ ( _Content-Transfer-Encoding: 8bit_ ) 的部分转换为兼容 ASCII 的 _Content-Transfer-Encoding_ ，并使用 MIME `unknown-8bit` 字符集来编码标头中不符合 RFC 的非 ASCII 字节数据，以使其符合 RFC。

如果 _unixfrom_ 为 `True`，则会在根消息对象的第一个 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 标头之前打印 Unix mailbox 格式 (参见 [`mailbox`](mailbox.md#module-mailbox "mailbox: Manipulate mailboxes in various formats")) 所使用的封包标头分隔符。 如果根对象没有封包标头，则会创建一个标准标头。 默认值为 `False`。 请注意对于子部分来说，不会打印任何封包标头。

如果 _linesep_ 不为 `None`，则会将其用作扁平化消息的所有行之间的分隔符。 如果 _linesep_ 为 `None` (默认值)，则使用在 _policy_ 中指定的值。

在 3.2 版本发生变更: 添加了对重编码 `8bit` 消息体的支持，以及 _linesep_ 参数。

clone( _fp_ )¶
~~~
    

~~~
返回此 `Generator` 实例的独立克隆，具有完全相同的选项设置，而 _fp_ 为新的 _outfp_ 。

write( _s_ )¶
~~~
    

~~~
将 _s_ 写入到传给 `Generator` 的构造器的 _outfp_ 的 _write_ 方法。 这足够为 `Generator` 实际提供可用于 [`print()`](functions.md#print "print") 函数的文件类 API。

作为一个便捷工具，[`EmailMessage`](email.message.md#email.message.EmailMessage "email.message.EmailMessage") 提供了 [`as_string()`](email.message.md#email.message.EmailMessage.as_string "email.message.EmailMessage.as_string") 和 `str(aMessage)` (即 [`__str__()`](email.message.md#email.message.EmailMessage.__str__ "email.message.EmailMessage.__str__")) 等方法，它们简单地生成一个消息对象的已格式化字符串表示形式。 更多细节请参阅 [`email.message`](email.message.md#module-email.message "email.message: The base class representing email messages.")。

`email.generator` 模块还提供了一个派生类 `DecodedGenerator`，它类似于 `Generator` 基类，不同之处在于非 _text_ 部分不会被序列化，而是被表示为 基于模板并填写了有关该部分的信息的字符串输出流的形式。

_class _email.generator.DecodedGenerator( _outfp_ , _mangle_from_ =None_, _maxheaderlen =None_, _fmt =None_, _*_ , _policy =None_)¶
~~~
    

~~~
