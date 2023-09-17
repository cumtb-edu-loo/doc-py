# `ftplib` \--- FTP 协议客户端¶

**源代码：** [Lib/ftplib.py](https://github.com/python/cpython/tree/3.12/Lib/ftplib.py)

* * *

本模块定义了 `FTP` 类和一些相关项目。 `FTP` 类实现了 FTP 协议的客户端。 你可以用这个类来编写执行各种自动化 FTP 任务的 Python 程序，例如镜像其他 FTP 服务器等。 它还被 [`urllib.request`](urllib.request.md#module-urllib.request "urllib.request: Extensible library for opening URLs.") 模块用来处理使用 FTP 的 URL。 有关 FTP (文件传输协议) 的更多信息，请参阅 [**RFC 959**](https://datatracker.ietf.org/doc/html/rfc959.md)。

默认编码为 UTF-8，遵循 [**RFC 2640**](https://datatracker.ietf.org/doc/html/rfc2640.md)。

[可用性](3.标准库/intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](3.标准库/intro.md#wasm-availability) 了解详情。

以下是使用 `ftplib` 模块的会话示例:

    
    
~~~shell
>>> from ftplib import FTP
>>> ftp = FTP('ftp.us.debian.org')  # connect to host, default port
>>> ftp.login()                     # user anonymous, passwd anonymous@
'230 Login successful.'
>>> ftp.cwd('debian')               # change into "debian" directory
'250 Directory successfully changed.'
>>> ftp.retrlines('LIST')           # list directory contents
-rw-rw-r--    1 1176     1176         1063 Jun 15 10:18 README
...
drwxr-sr-x    5 1176     1176         4096 Dec 19  2000 pool
drwxr-sr-x    4 1176     1176         4096 Nov 17  2008 project
drwxr-xr-x    3 1176     1176         4096 Oct 10  2012 tools
'226 Directory send OK.'
>>> with open('README', 'wb') as fp:
>>>     ftp.retrbinary('RETR README', fp.write)
'226 Transfer complete.'
>>> ftp.quit()
'221 Goodbye.'
~~~

这个模块定义了以下内容：

_class _ftplib.FTP( _host =''_, _user =''_, _passwd =''_, _acct =''_, _timeout =None_, _source_address =None_, _*_ , _encoding ='utf-8'_)¶

    

~~~
返回一个 `FTP` 类的新实例。当传入 _host_ 时，将调用 `connect(host)` 方法。当传入 _user_ 时，将额外调用 `login(user, passwd, acct)` 方法（其中 _passwd_ 和 _acct_ 若没有传入则默认为空字符串）。可选参数 _timeout_ 指定阻塞操作（如连接尝试）的超时（以秒为单位，如果未指定超时，将使用全局默认超时设置）。 _source_address_ 是一个 2 元组 `(host, port)`，套接字在连接前绑定它，作为其源地址。 _encoding_ 参数指定目录和文件名的编码。

`FTP` 类支持 [`with`](../reference/compound_stmts.md#with) 语句，例如：
~~~
    
    
~~~shell
>>> from ftplib import FTP
>>> with FTP("ftp1.at.proftpd.org") as ftp:
...     ftp.login()
...     ftp.dir()
... 
'230 Anonymous login ok, restrictions apply.'
dr-xr-xr-x   9 ftp      ftp           154 May  6 10:43 .
dr-xr-xr-x   9 ftp      ftp           154 May  6 10:43 ..
dr-xr-xr-x   5 ftp      ftp          4096 May  6 10:43 CentOS
dr-xr-xr-x   3 ftp      ftp            18 Jul 10  2008 Fedora
>>>
~~~

在 3.2 版本发生变更: 添加了对 [`with`](compound_stmts.md#with) 语句的支持。

在 3.3 版本发生变更: 添加了 _source_address_ 参数。

在 3.9 版本发生变更: 如果 _timeout_ 参数设置为 0，创建非阻塞套接字时，它将引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError") 来阻止该操作。添加了 _encoding_ 参数，且为了遵循 [**RFC 2640**](https://datatracker.ietf.org/doc/html/rfc2640.md)，该参数默认值从 Latin-1 改为了 UTF-8。

FTP_TLS(host='', user='', passwd='', acct='', *, context=None,

timeout=None, source_address=None, encoding='utf-8')

    

~~~
一个 `FTP` 的子类，它为 FTP 添加了 TLS 支持，如 [**RFC 4217**](https://datatracker.ietf.org/doc/html/rfc4217.md) 所述。它将像通常一样连接到 21 端口，暗中保护在身份验证前的 FTP 控制连接。而保护数据连接需要用户明确调用 `prot_p()` 方法。 _context_ 是一个 [`ssl.SSLContext`](ssl.md#ssl.SSLContext "ssl.SSLContext") 对象，该对象可以将 SSL 配置选项、证书和私钥打包放入一个单独的（可以长久存在的）结构中。请阅读 [安全考量](ssl.md#ssl-security) 以获取最佳实践。

在 3.2 版本加入.

在 3.3 版本发生变更: 添加了 _source_address_ 参数。

在 3.4 版本发生变更: 该类现在支持使用 [`ssl.SSLContext.check_hostname`](ssl.md#ssl.SSLContext.check_hostname "ssl.SSLContext.check_hostname") 和 _服务器名称提示_ (参见 [`ssl.HAS_SNI`](ssl.md#ssl.HAS_SNI "ssl.HAS_SNI")) 进行主机名检测。

在 3.9 版本发生变更: 如果 _timeout_ 参数设置为 0，创建非阻塞套接字时，它将引发 [`ValueError`](exceptions.md#ValueError "ValueError") 来阻止该操作。添加了 _encoding_ 参数，且为了遵循 [**RFC 2640**](https://datatracker.ietf.org/doc/html/rfc2640.md)，该参数默认值从 Latin-1 改为了 UTF-8。

在 3.12 版本发生变更: 已弃用的 _keyfile_ 和 _certfile_ 形参已被移除。

以下是使用 `FTP_TLS` 类的会话示例:
~~~
    
    
~~~shell
>>> ftps = FTP_TLS('ftp.pureftpd.org')
>>> ftps.login()
'230 Anonymous user logged in'
>>> ftps.prot_p()
'200 Data protection level set to "private"'
>>> ftps.nlst()
['6jack', 'OpenBSD', 'antilink', 'blogbench', 'bsdcam', 'clockspeed', 'djbdns-jedi', 'docs', 'eaccelerator-jedi', 'favicon.ico', 'francotone', 'fugu', 'ignore', 'libpuzzle', 'metalog', 'minidentd', 'misc', 'mysql-udf-global-user-variables', 'php-jenkins-hash', 'php-skein-hash', 'php-webdav', 'phpaudit', 'phpbench', 'pincaster', 'ping', 'posto', 'pub', 'public', 'public_keys', 'pure-ftpd', 'qscan', 'qtc', 'sharedance', 'skycache', 'sound', 'tmp', 'ucarp']
~~~

_exception _ftplib.error_reply¶

    

~~~
从服务器收到意外答复时，将引发本异常。

_exception _ftplib.error_temp¶
~~~
    

~~~
收到表示临时错误的错误代码（响应代码在 400--499 范围内）时，将引发本异常。

_exception _ftplib.error_perm¶
~~~
    

~~~
收到表示永久性错误的错误代码（响应代码在 500--599 范围内）时，将引发本异常。

_exception _ftplib.error_proto¶
~~~
    

~~~
从服务器收到不符合 FTP 响应规范的答复，比如以数字 1--5 开头时，将引发本异常。

ftplib.all_errors¶
~~~
    

~~~
所有异常的集合（一个元组），由于 FTP 连接出现问题（并非调用者的编码错误），`FTP` 实例的方法可能会引发这些异常。该集合包括上面列出的四个异常以及 [`OSError`](exceptions.md#OSError "OSError") 和 [`EOFError`](exceptions.md#EOFError "EOFError")。

参见

[`netrc`](netrc.md#module-netrc "netrc: Loading of .netrc files.") 模块
~~~
    

~~~
`.netrc` 文件格式解析器。FTP 客户端在响应用户之前，通常使用 `.netrc` 文件来加载用户认证信息。

## FTP 对象¶

一些方法可以按照两种方式来使用：一种处理文本文件，另一种处理二进制文件。方法名称与相应的命令相同，文本版中命令后面跟着 `lines`，二进制版中命令后面跟着 `binary`。

`FTP` 实例具有下列方法:

FTP.set_debuglevel( _level_ )¶
~~~
    

~~~
设置实例的调试级别，它控制着调试信息的数量。默认值 `0` 不产生调试信息。值 `1` 产生中等数量的调试信息，通常每个请求产生一行。大于或等于 `2` 的值产生的调试信息最多，FTP 控制连接上发送和接收的每一行都将被记录下来。

FTP.connect( _host =''_, _port =0_, _timeout =None_, _source_address =None_)¶
~~~
    

~~~
连接到给定的主机和端口。默认端口号由 FTP 协议规范规定，为 `21`。偶尔才需要指定其他端口号。每个实例只应调用一次本函数，如果在创建实例时就传入了 host，则根本不应调用它。所有其他方法只能在建立连接后使用。可选参数 _timeout_ 指定连接尝试的超时（以秒为单位）。如果没有传入 _timeout_ ，将使用全局默认超时设置。 _source_address_ 是一个 2 元组 `(host, port)`，套接字在连接前绑定它，作为其源地址。

引发一个 [审计事件](sys.md#auditing) `ftplib.connect`，附带参数 `self`, `host`, `port`。

在 3.3 版本发生变更: 添加了 _source_address_ 参数。

FTP.getwelcome()¶
~~~
    

~~~
返回服务器发送的欢迎消息，作为连接开始的回复。（该消息有时包含与用户有关的免责声明或帮助信息。）

FTP.login( _user ='anonymous'_, _passwd =''_, _acct =''_)¶
~~~
    

~~~
以 _user_ 的身份登录。 _passwd_ 和 _acct_ 是可选参数，默认为空字符串。如果没有指定 _user_ ，则默认为 `'anonymous'`。如果 _user_ 为 `'anonymous'`，那么默认的 _passwd_ 是 `'anonymous@'`。连接建立后，每个实例只应调用一次本函数；如果在创建实例时传入了 host 和 user，则完全不应该调用本函数。在客户端登录后，才允许执行大多数 FTP 命令。 _acct_ 参数提供记账信息 ("accounting information")；仅少数系统实现了该特性。

FTP.abort()¶
~~~
    

~~~
中止正在进行的文件传输。本方法并不总是有效，但值得一试。

FTP.sendcmd( _cmd_ )¶
~~~
    

~~~
将一条简单的命令字符串发送到服务器，返回响应的字符串。

引发一个 [审计事件](sys.md#auditing) `ftplib.sendcmd`，附带参数 `self`, `cmd`。

FTP.voidcmd( _cmd_ )¶
~~~
    

~~~
将一条简单的命令字符串发送到服务器，并处理响应内容。如果收到的响应代码表示的是成功（代码范围 200--299），则不返回任何内容。否则将引发 `error_reply`。

引发一个 [审计事件](sys.md#auditing) `ftplib.sendcmd`，附带参数 `self`, `cmd`。

FTP.retrbinary( _cmd_ , _callback_ , _blocksize =8192_, _rest =None_)¶
~~~
    

~~~
以二进制传输模式下载文件。 _cmd_ 应为恰当的 `RETR` 命令：`'RETR 文件名'`。 _callback_ 函数会在收到每个数据块时调用，传入的参数是表示数据块的一个字节。为执行实际传输，创建了底层套接字对象，可选参数 _blocksize_ 指定了读取该对象时的最大块大小（这也是传入 _callback_ 的数据块的最大大小）。已经选择了合理的默认值。 _rest_ 的含义与 `transfercmd()` 方法中的含义相同。

FTP.retrlines( _cmd_ , _callback =None_)¶
~~~
    

~~~
按照初始化时的 _encoding_ 参数指定的编码，获取文件或目录列表。 _cmd_ 应是恰当的 `RETR` 命令（参阅 `retrbinary()`），也可以是诸如 `LIST` 或 `NLST` 之类的命令（通常就只是字符串 `'LIST'`）。`LIST` 获取文件列表以及那些文件的信息。`NLST` 获取文件名称列表。 _callback_ 函数会在每一行都调用，参数就是包含一行的字符串，删除了尾部的 CRLF。默认的 _callback_ 会把行打印到 `sys.stdout`。

FTP.set_pasv( _val_ )¶
~~~
    

~~~
如果 _val_ 为 true，则打开“被动”模式，否则禁用被动模式。默认下被动模式是打开的。

FTP.storbinary( _cmd_ , _fp_ , _blocksize =8192_, _callback =None_, _rest =None_)¶
~~~
    

~~~
以二进制传输模式存储文件。 _cmd_ 应为恰当的 `STOR` 命令: `"STOR filename"`。 _fp_ 是一个 [文件对象](../glossary.md#term-file-object) (以二进制模式打开)，将使用它的 `read()` 方法读取它，用于提供要存储的数据，直到遇到 EOF，读取时的块大小为 _blocksize_ 。 参数 _blocksize_ 的默认值为 8192。 可选参数 _callback_ 是单参数函数，在每个数据块发送后都会以该数据块作为参数来调用它。 _rest_ 的含义与 `transfercmd()` 方法中的含义相同。

在 3.2 版本发生变更: 添加了 _rest_ 参数。

FTP.storlines( _cmd_ , _fp_ , _callback =None_)¶
~~~
    

~~~
以文本行模式存储文件。 _cmd_ 应为恰当的 `STOR` 命令 (请参阅 `storbinary()`)。 _fp_ 是一个 [文件对象](../glossary.md#term-file-object) (以二进制模式打开)，将使用它的 [`readline()`](io.md#io.IOBase.readline "io.IOBase.readline") 方法读取它的每一行，用于提供要存储的数据，直到遇到 EOF。 可选参数 _callback_ 是单参数函数，在每行发送后都会以该行作为参数来调用它。

FTP.transfercmd( _cmd_ , _rest =None_)¶
~~~
    

~~~
在 FTP 数据连接上开始传输数据。如果传输处于活动状态，传输命令由 _cmd_ 指定，需发送 `EPRT` 或 `PORT` 命令，然后接受连接 (accept)。如果服务器是被动服务器，需发送 `EPSV` 或 `PASV` 命令，连接到服务器 (connect)，然后启动传输命令。两种方式都将返回用于连接的套接字。

如果传入了可选参数 _rest_ ，则一条 `REST` 命令会被发送到服务器，并以 _rest_ 作为参数。 _rest_ 通常表示请求文件中的字节偏移量，它告诉服务器重新开始发送文件的字节，从请求的偏移量处开始，跳过起始字节。但是请注意，`transfercmd()` 方法会将 _rest_ 转换为字符串，但是不检查字符串的内容，转换用的编码是在初始化时指定的 _encoding_ 参数。如果服务器无法识别 `REST` 命令，将引发 `error_reply` 异常。如果发生这种情况，只需不带 _rest_ 参数调用 `transfercmd()`。

FTP.ntransfercmd( _cmd_ , _rest =None_)¶
~~~
    

~~~
类似于 `transfercmd()`，但返回一个元组，包括数据连接和数据的预计大小。如果预计大小无法计算，则返回的预计大小为 `None`。 _cmd_ 和 _rest_ 的含义与 `transfercmd()` 中的相同。

FTP.mlsd( _path =''_, _facts =[]_)¶
~~~
    

~~~
使用 `MLSD` 命令以标准格式列出目录内容 ([ **RFC 3659**](https://datatracker.ietf.org/doc/html/rfc3659.md))。如果省略 _path_ 则使用当前目录。 _facts_ 是字符串列表，表示所需的信息类型（如 `["type", "size", "perm"]`）。返回一个生成器对象，每个在 path 中找到的文件都将在该对象中生成两个元素的元组。第一个元素是文件名，第二个元素是该文件的 facts 的字典。该字典的内容受 _facts_ 参数限制，但不能保证服务器会返回所有请求的 facts。

在 3.3 版本加入.

FTP.nlst( _argument_ [, _..._ ])¶
~~~
    

~~~
返回一个文件名列表，文件名由 `NLST` 命令返回。可选参数 _argument_ 是待列出的目录（默认为当前服务器目录）。可以使用多个参数，将非标准选项传递给 `NLST` 命令。

备注

如果目标服务器支持相关命令，那么 `mlsd()` 提供的 API 更好。

FTP.dir( _argument_ [, _..._ ])¶
~~~
    

~~~
生成目录列表，即 `LIST` 命令所返回的结果，并将其打印到标准输出。可选参数 _argument_ 是待列出的目录（默认为当前服务器目录）。可以使用多个参数，将非标准选项传递给 `LIST` 命令。如果最后一个参数是一个函数，它将被用作 _callback_ 函数，与 `retrlines()` 中的相同，默认将打印到 `sys.stdout`。本方法返回 `None`。

备注

如果目标服务器支持相关命令，那么 `mlsd()` 提供的 API 更好。

FTP.rename( _fromname_ , _toname_ )¶
~~~
    

~~~
将服务器上的文件 _fromname_ 重命名为 _toname_ 。

FTP.delete( _filename_ )¶
~~~
    

~~~
将服务器上名为 _filename_ 的文件删除。如果删除成功，返回响应文本，如果删除失败，在权限错误时引发 `error_perm`，在其他错误时引发 `error_reply`。

FTP.cwd( _pathname_ )¶
~~~
    

~~~
设置服务器端的当前目录。

FTP.mkd( _pathname_ )¶
~~~
    

~~~
在服务器上创建一个新目录。

FTP.pwd()¶
~~~
    

~~~
返回服务器上当前目录的路径。

FTP.rmd( _dirname_ )¶
~~~
    

~~~
将服务器上名为 _dirname_ 的目录删除。

FTP.size( _filename_ )¶
~~~
    

~~~
请求服务器上名为 _filename_ 的文件大小。成功后以整数返回文件大小，未成功则返回 `None`。注意，`SIZE` 不是标准命令，但通常许多服务器的实现都支持该命令。

FTP.quit()¶
~~~
    

~~~
向服务器发送 `QUIT` 命令并关闭连接。 这是关闭一个连接的“礼貌”方式，但是如果服务器对 `QUIT` 命令的响应带有错误消息则这会引发一个异常。 这意味着对 `close()` 方法的调用，它将使得 `FTP` 实例对后继调用无效（见下文）。

FTP.close()¶
~~~
    

~~~
单方面关闭连接。 这不该被应用于已经关闭的连接，例如成功调用 `quit()` 之后的连接。 在此调用之后 `FTP` 实例不应被继续使用（在调用 `close()` 或 `quit()` 之后你不能通过再次发起调用 `login()` 方法重新打开连接）。

## FTP_TLS 对象¶

`FTP_TLS` 类继承自 `FTP`，它定义了下述其他对象：

FTP_TLS.ssl_version¶
~~~
    

~~~
要使用的 SSL 版本 (默认为 [`ssl.PROTOCOL_SSLv23`](ssl.md#ssl.PROTOCOL_SSLv23 "ssl.PROTOCOL_SSLv23"))。

FTP_TLS.auth()¶
~~~
    

~~~
通过使用 TLS 或 SSL 来设置一个安全控制连接，具体取决于 `ssl_version` 属性是如何设置的。

在 3.4 版本发生变更: 该方法现在支持使用 [`ssl.SSLContext.check_hostname`](ssl.md#ssl.SSLContext.check_hostname "ssl.SSLContext.check_hostname") 和 _服务器名称提示_ (参见 [`ssl.HAS_SNI`](ssl.md#ssl.HAS_SNI "ssl.HAS_SNI")) 进行主机名称检测。

FTP_TLS.ccc()¶
~~~
    

~~~
将控制通道回复为纯文本。 这适用于发挥知道如何使用非安全 FTP 处理 NAT 而无需打开固定端口的防火墙的优势。

在 3.3 版本加入.

FTP_TLS.prot_p()¶
~~~
    

~~~
设置加密数据连接。

FTP_TLS.prot_c()¶
~~~
    

~~~
