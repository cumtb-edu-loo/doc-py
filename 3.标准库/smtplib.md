# `smtplib` \--- SMTP 协议客户端¶

**源代码：** [Lib/smtplib.py](https://github.com/python/cpython/tree/3.12/Lib/smtplib.py)

* * *

`smtplib` 模块定义了一个 SMTP 客户端会话对象，该对象可将邮件发送到互联网上任何带有 SMTP 或 ESMTP 监听程序的计算机。 关于 SMTP 和 ESMTP 操作的更多细节请参阅 [**RFC 821**](https://datatracker.ietf.org/doc/html/rfc821.md) (简单邮件传输协议) 和 [**RFC 1869**](https://datatracker.ietf.org/doc/html/rfc1869.md) (SMTP 服务扩展)。

[可用性](3.标准库/intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](3.标准库/intro.md#wasm-availability) 了解详情。

_class _smtplib.SMTP( _host=''_ , _port=0_ , _local_hostname=None_ , [ _timeout_ , ] _source_address=None_ )¶

    

~~~
`SMTP` 实例是对 SMTP 连接的封装。 它提供了支持全部 SMTP 和 ESMTP 操作的方法。 如果给出了可选的 _host_ 和 _port_ 形参，则会在初始化期间调用 SMTP `connect()` 方法并附带这些形参。 如果指定了 _local_hostname_ ，它将在 HELO/EHLO 命令中被用作本地主机的 FQDN。 在其他情况下，会使用 [`socket.getfqdn()`](socket.md#socket.getfqdn "socket.getfqdn") 来找到本地主机名。 如果 `connect()` 调用返回了表示成功代码以外的任何信息，则会引发 `SMTPConnectError`。 可选的 _timeout_ 形参指定了阻塞操作如连接尝试的超时秒数（如果未指定，则将使用全局默认超时设置）。 如果达到超时限制，将会引发 [`TimeoutError`](exceptions.md#TimeoutError "TimeoutError")。 可选的 _source_address_ 形参允许在有多张网卡的计算机中绑定到某些特定的源地址，和/或绑定到某个特定的源 TCP 端口。 它接受一个 2 元组 `(host, port)` 作为在连接之前要绑定为其源地址的套接字。 如果省略 (或者如果 _host_ 或 _port_ 分别为 `''` 和/或 `0`) 则将使用 OS 的默认行为。

正常使用时，只需要初始化或 connect 方法，`sendmail()` 方法，再加上 `SMTP.quit()` 方法即可。下文包括了一个示例。

`SMTP` 类支持 [`with`](../reference/compound_stmts.md#with) 语句。当这样使用时，`with` 语句一退出就会自动发出 SMTP `QUIT` 命令。例如:
~~~
    
    
~~~shell
>>> from smtplib import SMTP
>>> with SMTP("domain.org") as smtp:
...     smtp.noop()
...
(250, b'Ok')
>>>
~~~

引发一个 [审计事件](3.标准库/sys.md#auditing) `smtplib.send`，附带参数 `self`, `data`。

在 3.3 版本发生变更: 添加了对 [`with`](compound_stmts.md#with) 语句的支持。

在 3.3 版本发生变更: 添加了 _source_address_ 参数。

在 3.5 版本加入: 现在已支持 SMTPUTF8 扩展 ([ **RFC 6531**](https://datatracker.ietf.org/doc/html/rfc6531.md))。

在 3.9 版本发生变更: 如果 _timeout_ 参数设置为 0，创建非阻塞套接字时，它将引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError") 来阻止该操作。

_class _smtplib.SMTP_SSL( _host=''_ , _port=0_ , _local_hostname=None_ , _*_ , [ _timeout_ , ] _context=None_ , _source_address=None_ )¶

    

~~~
`SMTP_SSL` 实例与 `SMTP` 实例的行为完全相同。在开始连接就需要 SSL，且 `starttls()` 不适合的情况下，应该使用 `SMTP_SSL`。如果未指定 _host_ ，则使用 localhost。如果 _port_ 为 0，则使用标准 SMTP-over-SSL 端口（465）。可选参数 _local_hostname_ 、 _timeout_ 和 _source_address_ 的含义与 `SMTP` 类中的相同。可选参数 _context_ 是一个 [`SSLContext`](ssl.md#ssl.SSLContext "ssl.SSLContext") 对象，可以从多个方面配置安全连接。请阅读 [安全考量](ssl.md#ssl-security) 以获取最佳实践。

在 3.3 版本发生变更: 增加了 _context_ 。

在 3.3 版本发生变更: 添加了 _source_address_ 参数。

在 3.4 版本发生变更: 该类现在支持使用 [`ssl.SSLContext.check_hostname`](ssl.md#ssl.SSLContext.check_hostname "ssl.SSLContext.check_hostname") 和 _服务器名称提示_ (参见 [`ssl.HAS_SNI`](ssl.md#ssl.HAS_SNI "ssl.HAS_SNI")) 进行主机名检测。

在 3.9 版本发生变更: 如果 _timeout_ 形参被设为零，则它将引发 [`ValueError`](exceptions.md#ValueError "ValueError") 来阻止创建非阻塞的套接字

在 3.12 版本发生变更: 已弃用的 _keyfile_ 和 _certfile_ 形参已被移除。

_class _smtplib.LMTP( _host=''_ , _port=LMTP_PORT_ , _local_hostname=None_ , _source_address=None_ [, _timeout_ ])¶
~~~
    

~~~
LMTP 协议与 ESMTP 非常相似，它很大程度上基于标准 SMTP 客户端。 将 Unix 套接字用于 LMTP 是很常见的，因此 `connect()` 方法必须支持它以及常规的 host:port 服务器。 可选参数 _local_hostname_ 和 _source_address_ 的含义与 `SMTP` 类中的相同。 要指定 Unix 套接字，你必须使用绝对路径作为 _host_ ，即以 '/' 开头。

支持使用常规的 SMTP 机制来进行认证。 当使用 Unix 套接字时，LMTP 通常不支持或要求任何认证，但你的情况可能会有所不同。

在 3.9 版本发生变更: 添加了可选的 _timeout_ 形参。

同样地定义了一组精心选择的异常:

_exception _smtplib.SMTPException¶
~~~
    

~~~
[`OSError`](exceptions.md#OSError "OSError") 的子类，它是本模块提供的所有其他异常的基类。

在 3.4 版本发生变更: SMTPException 已成为 [`OSError`](exceptions.md#OSError "OSError") 的子类

_exception _smtplib.SMTPServerDisconnected¶
~~~
    

~~~
当服务器意外断开连接，或在 `SMTP` 实例连接到服务器之前尝试使用它时将引发此异常。

_exception _smtplib.SMTPResponseException¶
~~~
    

~~~
包括 SMTP 错误代码的所有异常的基类。 这些异常会在 SMTP 服务器返回错误代码时在实例中生成。 错误代码存放在错误的 `smtp_code` 属性中，并且 `smtp_error` 属性会被设为错误消息。

_exception _smtplib.SMTPSenderRefused¶
~~~
    

~~~
发送方地址被拒绝。 除了在所有 `SMTPResponseException` 异常上设置的属性，还会将 'sender' 设为代表拒绝方 SMTP 服务器的字符串。

_exception _smtplib.SMTPRecipientsRefused¶
~~~
    

~~~
所有接收方地址被拒绝。 每个接收方的错误可通过属性 `recipients` 来访问，该属性是一个字典，其元素顺序与 `SMTP.sendmail()` 所返回的一致。

_exception _smtplib.SMTPDataError¶
~~~
    

~~~
SMTP 服务器拒绝接收消息数据。

_exception _smtplib.SMTPConnectError¶
~~~
    

~~~
在建立与服务器的连接期间发生了错误。

_exception _smtplib.SMTPHeloError¶
~~~
    

~~~
服务器拒绝了我们的 `HELO` 消息。

_exception _smtplib.SMTPNotSupportedError¶
~~~
    

~~~
尝试的命令或选项不被服务器所支持。

在 3.5 版本加入.

_exception _smtplib.SMTPAuthenticationError¶
~~~
    

~~~
SMTP 认证出现问题。 最大的可能是服务器不接受所提供的用户名/密码组合。

参见

[**RFC 821**](https://datatracker.ietf.org/doc/html/rfc821.md) \- 简单邮件传输协议
~~~
    

~~~
SMTP 的协议定义。 该文件涵盖了 SMTP 的模型、操作程序和协议细节。

[**RFC 1869**](https://datatracker.ietf.org/doc/html/rfc1869.md) \- SMTP 服务扩展
~~~
    

~~~
定义了 SMTP 的 ESMTP 扩展。 这描述了一个用新命令扩展 SMTP 的框架，支持动态发现服务器所提供的命令，并定义了一些额外的命令。

## SMTP 对象¶

一个 `SMTP` 实例拥有以下方法：

SMTP.set_debuglevel( _level_ )¶
~~~
    

~~~
设置调试输出级别。 如果 _level_ 的值为 1 或 `True` ，就会产生连接的调试信息，以及所有发送和接收服务器的信息。 如果 _level_ 的值为 2 ，则这些信息会被加上时间戳。

在 3.5 版本发生变更: 添调试级别 2 。

SMTP.docmd( _cmd_ , _args =''_)¶
~~~
    

~~~
向服务器发送一条命令 _cmd_ 。 可选的参数 _args_ 被简单地串联到命令中，用一个空格隔开。

这将返回一个由数字响应代码和实际响应行组成的2元组（多行响应被连接成一个长行）。

在正常操作中，应该没有必要明确地调用这个方法。它被用来实现其他方法，对于测试私有扩展可能很有用。

如果在等待回复的过程中，与服务器的连接丢失， `SMTPServerDisconnected` 将被触发。

SMTP.connect( _host ='localhost'_, _port =0_)¶
~~~
    

~~~
连接到某个主机的某个端口。默认是连接到 localhost 的标准 SMTP 端口（25）上。如果主机名以冒号 (`':'`) 结尾，后跟数字，则该后缀将被删除，且数字将视作要使用的端口号。如果在实例化时指定了 host，则构造函数会自动调用本方法。返回包含响应码和响应消息的 2 元组，它们由服务器在其连接响应中发送。

触发一个 [auditing event](sys.md#auditing) `smtplib.connect`，其参数为 `self` ， `host` ， `port` 。

SMTP.helo( _name =''_)¶
~~~
    

~~~
使用 `HELO` 向 SMTP 服务器表明自己的身份。 hostname 参数默认为本地主机的完全合格域名。服务器返回的消息被存储为对象的 `helo_resp` 属性。

在正常操作中，应该没有必要明确调用这个方法。它将在必要时被 `sendmail()` 隐式调用。

SMTP.ehlo( _name =''_)¶
~~~
    

~~~
使用 `EHLO` 向 ESMTP 服务器表明自己的身份。 hostname 参数默认为本地主机的完全合格域名。 检查 ESMTP 选项的响应，并存储它们供 `has_extn()` 使用。同时设置几个信息属性：服务器返回的消息被存储为 `ehlo_resp` 属性， `does_esmtp` 根据服务器是否支持 ESMTP 被设置为 `True` 或 `False` ，而 `esmtp_features` 将是一个字典，包含这个服务器支持的 SMTP 服务扩展的名称，以及它们的参数（如果有）。

除非你想在发送邮件前使用 `has_extn()` ，否则应该没有必要明确调用这个方法。 它将在必要时被 `sendmail()` 隐式调用。

SMTP.ehlo_or_helo_if_needed()¶
~~~
    

~~~
如果这个会话中没有先前的 `EHLO` 或 `HELO` 命令，该方法会调用 `ehlo()` 和/或 `helo()` 。它首先尝试 ESMTP `EHLO` 。

`SMTPHeloError`
~~~
    

~~~
服务器没有正确回复 `HELO` 问候。

SMTP.has_extn( _name_ )¶
~~~
    

~~~
如果 _name_ 在服务器返回的 SMTP 服务扩展集合中，返回 [`True`](constants.md#True "True") ，否则为 [`False`](constants.md#False "False") 。大小写被忽略。

SMTP.verify( _address_ )¶
~~~
    

~~~
使用 SMTP `VRFY` 检查此服务器上的某个地址是否有效。 如果用户地址有效则返回一个由代码 250 和完整 [**RFC 822**](https://datatracker.ietf.org/doc/html/rfc822.md) 地址（包括人名）组成的元组。 否则返回 400 或更大的 SMTP 错误代码以及一个错误字符串。

备注

许多网站都禁用 SMTP `VRFY` 以阻止垃圾邮件。

SMTP.login( _user_ , _password_ , _*_ , _initial_response_ok =True_)¶
~~~
    

~~~
登录到一个需要认证的 SMTP 服务器。 参数是用于认证的用户名和密码。 如果会话在之前没有执行过 `EHLO` 或 `HELO` 命令，此方法会先尝试 ESMTP `EHLO`。 如果认证成功则此方法将正常返回，否则可能引发以下异常:

`SMTPHeloError`
~~~
    

~~~
服务器没有正确回复 `HELO` 问候。

`SMTPAuthenticationError`
~~~
    

~~~
服务器不接受所提供的用户名/密码组合。

`SMTPNotSupportedError`
~~~
    

~~~
服务器不支持 `AUTH` 命令。

`SMTPException`
~~~
    

~~~
未找到适当的认证方法。

`smtplib` 所支持的每种认证方法只要被服务器声明支持就会被依次尝试。 请参阅 `auth()` 获取受支持的认证方法列表。 _initial_response_ok_ 会被传递给 `auth()`。

可选的关键字参数 _initial_response_ok_ 对于支持它的认证方法，是否可以与 `AUTH` 命令一起发送 [**RFC 4954**](https://datatracker.ietf.org/doc/html/rfc4954.md) 中所规定的“初始响应”，而不是要求回复/响应。

在 3.5 版本发生变更: 可能会引发 `SMTPNotSupportedError`，并添加 _initial_response_ok_ 形参。

SMTP.auth( _mechanism_ , _authobject_ , _*_ , _initial_response_ok =True_)¶
~~~
    

~~~
为指定的认证机制 _mechanism_ 发送 `SMTP` `AUTH` 命令，并通过 _authobject_ 处理回复响应。

_mechanism_ 指定要使用何种认证机制作为 `AUTH` 命令的参数；可用的值是在 `esmtp_features` 的 `auth` 元素中列出的内容。

_authobject_ 必须为接受一个可选的单独参数的可调用对象:
~~~
    
    
~~~
data = authobject(challenge=None)
~~~

如果可选的关键字参数 _initial_response_ok_ 为真值，则将先不带参数地调用 `authobject()`。 它可以返回 [**RFC 4954**](https://datatracker.ietf.org/doc/html/rfc4954.md) "初始响应" ASCII `str`，其内容将被编码并使用下述的 `AUTH` 命令来发送。 如果 `authobject()` 不支持初始响应（例如由于要求一个回复），它应当将 `None` 作为附带 `challenge=None` 调用的返回值。 如果 _initial_response_ok_ 为假值，则 `authobject()` 将不会附带 `None` 被首先调用。

如果初始响应检测返回了 `None`，或者如果 _initial_response_ok_ 为假值，则将调用 `authobject()` 来处理服务器的回复响应；它所传递的 _challenge_ 参数将为一个 `bytes`。 它应当返回用 base64 进行编码的 ASCII `str` _data_ 并发送给服务器。

`SMTP` 类提供的 `authobjects` 针对 `CRAM-MD5`, `PLAIN` 和 `LOGIN` 等机制；它们的名称分别是 `SMTP.auth_cram_md5`, `SMTP.auth_plain` 和 `SMTP.auth_login`。 它们都要求将 `user` 和 `password` 这两个 `SMTP` 实例属性设为适当的值。

用户代码通常不需要直接调用 `auth`，而是调用 `login()` 方法，它将按上述顺序依次尝试上述每一种机制。 `auth` 被公开以便辅助实现 `smtplib` 没有（或尚未）直接支持的认证方法。

在 3.5 版本加入.

SMTP.starttls( _*_ , _context =None_)¶

    

~~~
将 SMTP 连接设为 TLS (传输层安全) 模式。 后续的所有 SMTP 命令都将被加密。 你应当随即再次调用 `ehlo()`。

如果提供了 _keyfile_ 和 _certfile_ ，它们会被用来创建 [`ssl.SSLContext`](ssl.md#ssl.SSLContext "ssl.SSLContext")。

可选的 _context_ 形参是一个 [`ssl.SSLContext`](ssl.md#ssl.SSLContext "ssl.SSLContext") 对象；它是使用密钥文件和证书的替代方式，如果指定了该形参则 _keyfile_ 和 _certfile_ 都应为 `None`。

如果这个会话中没有先前的 `EHLO` or `HELO` 命令，该方法会首先尝试 ESMTP `EHLO`。

在 3.12 版本发生变更: 已弃用的 _keyfile_ 和 _certfile_ 形参已被移除。

`SMTPHeloError`
~~~
    

~~~
服务器没有正确回复 `HELO` 问候。

`SMTPNotSupportedError`
~~~
    

~~~
服务器不支持 STARTTLS 扩展。

[`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")
~~~
    

~~~
SSL/TLS 支持在你的 Python 解释器上不可用。

在 3.3 版本发生变更: 增加了 _context_ 。

在 3.4 版本发生变更: 此方法现在支持使用 `SSLContext.check_hostname` 和 _服务器名称指示符_ (参见 [`HAS_SNI`](ssl.md#ssl.HAS_SNI "ssl.HAS_SNI")) 进行主机名检测。

在 3.5 版本发生变更: 因缺少 STARTTLS 支持而引发的错误现在是 `SMTPNotSupportedError` 子类而不是 `SMTPException` 基类。

SMTP.sendmail( _from_addr_ , _to_addrs_ , _msg_ , _mail_options =()_, _rcpt_options =()_)¶
~~~
    

~~~
发送邮件。必要参数是一个 [**RFC 822**](https://datatracker.ietf.org/doc/html/rfc822.md) 发件地址字符串，一个 [**RFC 822**](https://datatracker.ietf.org/doc/html/rfc822.md) 收件地址字符串列表（裸字符串将被视为含有 1 个地址的列表），以及一个消息字符串。调用者可以将 ESMTP 选项列表（如 `8bitmime`）作为 _mail_options_ 传入，用于 `MAIL FROM` 命令。需要与所有 `RCPT` 命令一起使用的 ESMTP 选项（如 `DSN` 命令）可以作为 _rcpt_options_ 传入。（如果需要对不同的收件人使用不同的 ESMTP 选项，则必须使用底层的方法来发送消息，如 `mail()`, `rcpt()` 和 `data()`。）

备注

_from_addr_ 和 _to_addrs_ 形参被用来构造传输代理所使用的消息封包。 `sendmail` 不会以任何方式修改消息标头。

_msg_ 可以是一个包含 ASCII 范围内字符的字符串，或是一个字节串。 字符串会使用 ascii 编解码器编码为字节串，并且单独的 `\r` 和 `\n` 字符会被转换为 `\r\n` 字符序列。 字节串则不会被修改。

如果在此之前本会话没有执行过 `EHLO` 或 `HELO` 命令，此方法会先尝试 ESMTP `EHLO`。 如果服务器执行了 ESMTP，消息大小和每个指定的选项将被传递给它（如果指定的选项属于服务器声明的特性集）。 如果 `EHLO` 失败，则将尝试 `HELO` 并屏蔽 ESMTP 选项。

如果邮件被至少一个接收方接受则此方法将正常返回。 在其他情况下它将引发异常。 也就是说，如果此方法没有引发异常，则应当会有人收到你的邮件。 如果此方法没有引发异常，它将返回一个字典，其中的条目对应每个拒绝的接收方。 每个条目均包含由服务器发送的 SMTP 错误代码和相应错误消息所组成的元组。

如果 `SMTPUTF8` 包括在 _mail_options_ 中，并且被服务器所支持，则 _from_addr_ 和 _to_addrs_ 可能包含非 ASCII 字符。

此方法可能引发以下异常:

`SMTPRecipientsRefused`
~~~
    

~~~
所有收件人都被拒绝。 无人收到邮件。 该异常的 `recipients` 属性是一个字典，其中有被拒绝收件人的信息（类似于至少有一个收件人接受邮件时所返回的信息）。

`SMTPHeloError`
~~~
    

~~~
服务器没有正确回复 `HELO` 问候。

`SMTPSenderRefused`
~~~
    

~~~
服务器不接受 _from_addr_ 。

`SMTPDataError`
~~~
    

~~~
服务器回复了一个意外的错误代码（而不是拒绝收件人）。

`SMTPNotSupportedError`
~~~
    

~~~
在 _mail_options_ 中给出了 `SMTPUTF8` 但是不被服务器所支持。

除非另有说明，即使在引发异常之后连接仍将被打开。

在 3.2 版本发生变更: _msg_ 可以为字节串。

在 3.5 版本发生变更: 增加了 `SMTPUTF8` 支持，并且如果指定了 `SMTPUTF8` 但是不被服务器所支持则可能会引发 `SMTPNotSupportedError`。

SMTP.send_message( _msg_ , _from_addr =None_, _to_addrs =None_, _mail_options =()_, _rcpt_options =()_)¶
~~~
    

~~~
本方法是一种快捷方法，用于带着消息调用 `sendmail()`，消息由 [`email.message.Message`](email.compat32-message.md#email.message.Message "email.message.Message") 对象表示。参数的含义与 `sendmail()` 中的相同，除了 _msg_ ，它是一个 `Message` 对象。

如果 _from_addr_ 为 `None` 或 _to_addrs_ 为 `None` ，那么 `send_message` 将根据 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md)，从 _msg_ 头部提取地址填充下列参数：如果头部存在 _Sender_ 字段，则用它填充 _from_addr_ ，不存在则用 _From_ 字段填充 _from_addr_ 。 _to_addrs_ 组合了 _msg_ 中的 _To_ , _Cc_ 和 _Bcc_ 字段的值（字段存在的情况下）。如果一组 _Resent-*_ 头部恰好出现在 message 中，那么就忽略常规的头部，改用 _Resent-*_ 头部。如果 message 包含多组 _Resent-*_ 头部，则引发 [`ValueError`](exceptions.md#ValueError "ValueError")，因为无法明确检测出哪一组 _Resent-_ 头部是最新的。

`send_message` 使用 [`BytesGenerator`](email.generator.md#email.generator.BytesGenerator "email.generator.BytesGenerator") 来序列化 _msg_ ，且将 `\r\n` 作为 _linesep_ ，并调用 `sendmail()` 来传输序列化后的结果。无论 _from_addr_ 和 _to_addrs_ 的值为何，`send_message` 都不会传输 _msg_ 中可能出现的 _Bcc_ 或 _Resent-Bcc_ 头部。如果 _from_addr_ 和 _to_addrs_ 中的某个地址包含非 ASCII 字符，且服务器没有声明支持 `SMTPUTF8`，则引发 `SMTPNotSupported` 错误。如果服务器支持，则 `Message` 将按新克隆的 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 进行序列化，其中的 [`utf8`](email.policy.md#email.policy.EmailPolicy.utf8 "email.policy.EmailPolicy.utf8") 属性被设置为 `True`，且 `SMTPUTF8` 和 `BODY=8BITMIME` 被添加到 _mail_options_ 中。

在 3.2 版本加入.

在 3.5 版本加入: 支持国际化地址 (`SMTPUTF8`)。

SMTP.quit()¶
~~~
    

~~~
终结 SMTP 会话并关闭连接。 返回 SMTP `QUIT` 命令的结果。

与标准 SMTP/ESMTP 命令 `HELP`, `RSET`, `NOOP`, `MAIL`, `RCPT` 和 `DATA` 对应的低层级方法也是受支持的。 通常不需要直接调用这些方法，因此它们没有被写入本文档。 相关细节请参看模块代码。

## SMTP 示例¶

这个例子提示用户输入消息封包所需的地址 ('To' 和 'From' 地址)，以及所要封包的消息。 请注意包括在消息中的标头必须包括在输入的消息中；这个例子不对 [**RFC 822**](https://datatracker.ietf.org/doc/html/rfc822.md) 标头进行任何处理。 特别地，'To' 和 'From' 地址必须显式地包括在消息标头中。
~~~
    
    
~~~
import smtplib

def prompt(prompt):
    return input(prompt).strip()

fromaddr = prompt("From: ")
toaddrs  = prompt("To: ").split()
print("Enter message, end with ^D (Unix) or ^Z (Windows):")

# Add the From: and To: headers at the start!
msg = ("From: %s\r\nTo: %s\r\n\r\n"
       % (fromaddr, ", ".join(toaddrs)))
while True:
    try:
        line = input()
    except EOFError:
        break
    if not line:
        break
    msg = msg + line

print("Message length is", len(msg))

server = smtplib.SMTP('localhost')
server.set_debuglevel(1)
server.sendmail(fromaddr, toaddrs, msg)
server.quit()
~~~

备注

通常，你将需要使用 [`email`](email.md#module-email "email: Package supporting the parsing, manipulating, and generating email messages.") 包的特性来构造电子邮件消息，然后你可以通过 `send_message()` 来发送它，参见 [email: 示例](email.examples.md#email-examples)。

