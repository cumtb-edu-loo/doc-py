# `http.client` \--- HTTP 协议客户端¶

**源代码：** [Lib/http/client.py](https://github.com/python/cpython/tree/3.12/Lib/http/client.py)

* * *

这个模块定义了实现 HTTP 和 HTTPS 协议客户端的类。 它通常不直接使用 --- 模块 [`urllib.request`](urllib.request.md#module-urllib.request "urllib.request: Extensible library for opening URLs.") 会用它来处理使用 HTTP 和 HTTPS 的 URL。

参见

对于更高层级的 HTTP 客户端接口，建议使用 [Requests 包](https://requests.readthedocs.io/en/latest/)。

备注

HTTPS 支持仅在编译 Python 时启用了 SSL 支持的情况下（通过 [`ssl`](ssl.md#module-ssl "ssl: TLS/SSL wrapper for socket objects") 模块）可用。

[可用性](3.标准库/intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](3.标准库/intro.md#wasm-availability) 了解详情。

该模块支持以下类：

_class _http.client.HTTPConnection( _host_ , _port=None_ , [ _timeout_ , ] _source_address=None_ , _blocksize=8192_ )¶

    

~~~
`HTTPConnection` 的实例代表与 HTTP 服务器的一个连接事务。 它在实例化时应当传入一个主机和可选的端口号。 若未传入端口号，则如果主机字符串的形式为 `host:port` 则会从中提取端口，否则将使用默认的 HTTP 端口（80）。 如果给出了可选的 _timeout_ 形参，则阻塞操作（如连接尝试）将在指定的秒数之后超时（如果未给出，则使用全局默认超时设置）。 可选的 _source_address_ 形参可以是一个 (host, port) 元组，用作进行 HTTP 连接的源地址。 可选的 _blocksize_ 形参以字节为单位设置缓冲区的大小，用来发送文件类消息体。

举个例子，以下调用都是创建连接到同一主机和端口的服务器的实例：
~~~
    
    
~~~shell
>>> h1 = http.client.HTTPConnection('www.python.org')
>>> h2 = http.client.HTTPConnection('www.python.org:80')
>>> h3 = http.client.HTTPConnection('www.python.org', 80)
>>> h4 = http.client.HTTPConnection('www.python.org', 80, timeout=10)
~~~

在 3.2 版本发生变更: 添加了*source_address* 参数

在 3.4 版本发生变更: 移除了 _strict_ 形参。 不再支持 HTTP 0.9 风格的“简单响应”。

在 3.7 版本发生变更: 添加了 _blocksize_ 参数。

_class _http.client.HTTPSConnection( _host_ , _port=None_ , _*_ , [ _timeout_ , ] _source_address=None_ , _context=None_ , _blocksize=8192_ )¶

    

~~~
`HTTPConnection` 的子类，使用 SSL 与安全服务器进行通信。 默认端口为 `443`。 如果指定了 _context_ ，它必须为一个描述 SSL 各选项的 [`ssl.SSLContext`](ssl.md#ssl.SSLContext "ssl.SSLContext") 实例。

请参阅 [安全考量](ssl.md#ssl-security) 了解有关最佳实践的更多信息。

在 3.2 版本发生变更: 添加了 _source_address_ , _context_ 和 _check_hostname_ 。

在 3.2 版本发生变更: 这个类现在会在可能的情况下（即当 [`ssl.HAS_SNI`](ssl.md#ssl.HAS_SNI "ssl.HAS_SNI") 为真值时）支持 HTTPS 虚拟主机。

在 3.4 版本发生变更: 删除了 _strict_ 参数，不再支持 HTTP 0.9 风格的“简单响应”。

在 3.4.3 版本发生变更: 目前这个类在默认情况下会执行所有必要的证书和主机检查。 要回复到先前的非验证行为，可以将 `ssl._create_unverified_context()` 传递给 _context_ 参数。

在 3.8 版本发生变更: 该类现在对于默认的 _context_ 或在传入 _cert_file_ 并附带自定义 _context_ 时会启用 TLS 1.3 [`ssl.SSLContext.post_handshake_auth`](ssl.md#ssl.SSLContext.post_handshake_auth "ssl.SSLContext.post_handshake_auth")。

在 3.10 版本发生变更: 现在，若未给出 _context_ ，则会发送一个带有协议指示器 `http/1.1` 的ALPN 扩展。自定义 _context_ 应用 `set_alpn_protocol()` 设置 ALPN 协议。

在 3.12 版本发生变更: 已弃用的 _key_file_ , _cert_file_ 和 _check_hostname_ 形参已被移除。

_class _http.client.HTTPResponse( _sock_ , _debuglevel =0_, _method =None_, _url =None_)¶
~~~
    

~~~
在成功连接后返回类的实例，而不是由用户直接实例化。

在 3.4 版本发生变更: 删除了 _strict_ 参数，不再支持HTTP 0.9 风格的“简单响应”。

这个模块定义了以下函数：

http.client.parse_headers( _fp_ )¶
~~~
    

~~~
从文件指针 _fp_ 中解析头部信息，该文件代表 HTTP 请求/响应。该文件必须是 `BufferedIOBase` reader 对象（即不是文本），并且必须提供符合 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 格式的头部。

该函数返回 `http.client.HTTPMessage` 的实例，带有头部各个字段，但不带正文数据（与 `HTTPResponse.msg` 和 [`http.server.BaseHTTPRequestHandler.headers`](http.server.md#http.server.BaseHTTPRequestHandler.headers "http.server.BaseHTTPRequestHandler.headers") 一样）。返回之后，文件指针 _fp_ 已为读取 HTTP 正文做好准备了。

备注

`parse_headers()` 不会解析 HTTP 消息的开始行；只会解析各 `Name: value` 行。文件必须为读取这些字段做好准备，所以在调用该函数之前，第一行应该已经被读取过了。

下列异常可以适当地被引发:

_exception _http.client.HTTPException¶
~~~
    

~~~
此模块中其他异常的基类。 它是 [`Exception`](exceptions.md#Exception "Exception") 的一个子类。

_exception _http.client.NotConnected¶
~~~
    

~~~
`HTTPException` 的一个子类。

_exception _http.client.InvalidURL¶
~~~
    

~~~
`HTTPException` 的一个子类，如果给出了一个非数字或为空值的端口就会被引发。

_exception _http.client.UnknownProtocol¶
~~~
    

~~~
`HTTPException` 的一个子类。

_exception _http.client.UnknownTransferEncoding¶
~~~
    

~~~
`HTTPException` 的一个子类。

_exception _http.client.UnimplementedFileMode¶
~~~
    

~~~
`HTTPException` 的一个子类。

_exception _http.client.IncompleteRead¶
~~~
    

~~~
`HTTPException` 的一个子类。

_exception _http.client.ImproperConnectionState¶
~~~
    

~~~
`HTTPException` 的一个子类。

_exception _http.client.CannotSendRequest¶
~~~
    

~~~
`ImproperConnectionState` 的一个子类。

_exception _http.client.CannotSendHeader¶
~~~
    

~~~
`ImproperConnectionState` 的一个子类。

_exception _http.client.ResponseNotReady¶
~~~
    

~~~
`ImproperConnectionState` 的一个子类。

_exception _http.client.BadStatusLine¶
~~~
    

~~~
`HTTPException` 的一个子类。 如果服务器反馈了一个我们不理解的 HTTP 状态码就会被引发。

_exception _http.client.LineTooLong¶
~~~
    

~~~
`HTTPException` 的一个子类。 如果在 HTTP 协议中从服务器接收到过长的行就会被引发。

_exception _http.client.RemoteDisconnected¶
~~~
    

~~~
[`ConnectionResetError`](exceptions.md#ConnectionResetError "ConnectionResetError") 和 `BadStatusLine` 的一个子类。 当尝试读取响应时的结果是未从连接读取到数据时由 `HTTPConnection.getresponse()` 引发，表明远端已关闭连接。

在 3.5 版本加入: 在此之前引发的异常为 `BadStatusLine``('')`。

此模块中定义的常量为：

http.client.HTTP_PORT¶
~~~
    

~~~
HTTP 协议默认的端口号 (总是 `80`)。

http.client.HTTPS_PORT¶
~~~
    

~~~
HTTPS 协议默认的端口号 (总是 `443`)。

http.client.responses¶
~~~
    

~~~
这个字典把 HTTP 1.1 状态码映射到 W3C 名称。

例如：`http.client.responses[http.client.NOT_FOUND]` 是 `'NOT FOUND` （未发现）。

本模块中可用的 HTTP 状态码常量可以参见 [HTTP 状态码](http.md#http-status-codes) 。

## HTTPConnection 对象¶

`HTTPConnection` 实例拥有以下方法：

HTTPConnection.request( _method_ , _url_ , _body =None_, _headers ={}_, _*_ , _encode_chunked =False_)¶
~~~
    

~~~
这将使用 HTTP 请求方法 _method_ 和请求 URI _url_ 将服务器发送一个请求。 所提供的 _url_ 必须是符合 [**RFC 2616 §5.1.2**](https://datatracker.ietf.org/doc/html/rfc2616.md#section-5.1.2) 规范的绝对路径（除非是连接到一个 HTTP 代理服务器或者使用 `OPTIONS` 或 `CONNECT` 方法）。

如果给定 _body_ ，那么给定的数据会在信息头完成之后发送。它可能是一个 `字符串`，一个 [bytes-like object](../glossary.md#term-bytes-like-object)，一个打开的 [file object](../glossary.md#term-file-object)，或者 [`bytes`](stdtypes.md#bytes "bytes") 迭代器。如果 _body_ 是字符串，它会按 HTTP 默认的 ISO-8859-1 编码。如果是一个字节类对象，它会按原样发送。如果是 [file object](../glossary.md#term-file-object)，文件的内容会被发送，这个文件对象应该至少支持 `read()` 方法。如果这个文件对象是一个 [`io.TextIOBase`](io.md#io.TextIOBase "io.TextIOBase") 实例，由 `read()` 方法返回的数据会按 ISO-8859-1 编码，否则由 `read()` 方法返回的数据会按原样发送。如果 _body_ 是一个迭代器，迭代器中的元素会被发送，直到迭代器耗尽。

_headers_ 参数应为由要与请求一同发送的额外 HTTP 标头组成的映射。 必须提供一个 [**主机标头**](https://datatracker.ietf.org/doc/html/rfc2616.md#section-14.23) 以符合 [**RFC 2616 §5.1.2**](https://datatracker.ietf.org/doc/html/rfc2616.md#section-5.1.2) 规范（除非是连接到一个 HTTP 代理服务器或者使用 `OPTIONS` 或 `CONNECT` 方法）。

如果 _headers_ 既不包含 Content-Length 也没有 Transfer-Encoding，但存在请求正文，那么这些头字段中的一个会自动设定。如果 _body_ 是 `None`，那么对于要求正文的方法 (`PUT`，`POST`，和 `PATCH`)，Content-Length 头会被设为 `0`。如果 _body_ 是字符串或者类似字节的对象，并且也不是 [文件](../glossary.md#term-file-object)，Content-Length 头会设为正文的长度。任何其他类型的 _body_ （一般是文件或迭代器）会按块编码，这时会自动设定 Transfer-Encoding 头以代替 Content-Length。

在 _headers_ 中指定 Transfer-Encoding 时， _encode_chunked_ 是唯一相关的参数。如果 _encode_chunked_ 为 `False`，HTTPConnection 对象会假定所有的编码都由调用代码处理。如果为 `True`，正文会按块编码。

例如，要对 `https://docs.python.org/3/` 执行一个 `GET` 请求:
~~~
    
    
~~~shell
>>> import http.client
>>> host = "docs.python.org"
>>> conn = http.client.HTTPSConnection(host)
>>> conn.request("GET", "/3/", headers={"Host": host})
>>> response = conn.getresponse()
>>> print(response.status, response.reason)
200 OK
~~~

备注

HTTP 协议在 1.1 版中添加了块传输编码。除非明确知道 HTTP 服务器可以处理 HTTP 1.1，调用者要么必须指定 Content-Length，要么必须传入 [`str`](stdtypes.md#str "str") 或字节类对象，注意该对象不能是表达 body 的文件。

在 3.2 版本加入: _body_ 现在可以是可迭代对象了。

在 3.6 版本发生变更: 如果 Content-Length 和 Transfer-Encoding 都没有在 _headers_ 中设置，文件和可迭代的 _body_ 对象现在会按块编码。添加了 _encode_chunked_ 参数。不会尝试去确定文件对象的 Content-Length。

HTTPConnection.getresponse()¶

    

~~~
应当在发送一个请求从服务器获取响应时被调用。 返回一个 `HTTPResponse` 的实例。

备注

请注意你必须在读取了整个响应之后才能向服务器发送新的请求。

在 3.5 版本发生变更: 如果引发了 [`ConnectionError`](exceptions.md#ConnectionError "ConnectionError") 或其子类， `HTTPConnection` 对象将在发送新的请求时准备好重新连接。

HTTPConnection.set_debuglevel( _level_ )¶
~~~
    

~~~
设置调试等级。 默认的调试等级为 `0`，意味着不会打印调试输出。 任何大于 `0` 的值将使得所有当前定义的调试输出被打印到 stdout。 `debuglevel` 会被传给任何新创建的 `HTTPResponse` 对象。

在 3.1 版本加入.

HTTPConnection.set_tunnel( _host_ , _port =None_, _headers =None_)¶
~~~
    

~~~
为 HTTP 连接隧道设置主机和端口。 这将允许通过代理服务器运行连接。

_host_ 和 _port_ 参数指明隧道连接的端点（即 CONNECT 请求所包含的地址，而 _不是_ 代理服务器的地址）。

_headers_ 参数应为一个随 CONNECT 请求发送的额外 HTTP 标头的映射。

在 HTTP/1.1 被用于 HTTP CONNECT 隧道请求时，[根据相应的 RFC](https://datatracker.ietf.org/doc/html/rfc7231#section-4.3.6)，必须提供一个 HTTP `Host:` 标头，以匹配作为 CONNECT 请求的目标提供的请求目标 authority-form。 如果未通过 headers 参数提供 HTTP `Host:` 标头，则会自动生成并传送一个标头。

例如，要通过一个运行于本机 8080 端口的 HTTPS 代理服务器隧道，我们应当向 `HTTPSConnection` 构造器传入代理的地址，并将我们最终想要访问的主机地址传给 `set_tunnel()` 方法:
~~~
    
    
~~~shell
>>> import http.client
>>> conn = http.client.HTTPSConnection("localhost", 8080)
>>> conn.set_tunnel("www.python.org")
>>> conn.request("HEAD","/index.md")
~~~

在 3.2 版本加入.

在 3.12 版本发生变更: HTTP CONNECT 隧道请求使用 HTTP/1.1 协议，它是从 HTTP/1.0 协议升级而来。 `Host:` HTTP 标头是 HTTP/1.1 所必需的，因此如果未在 headers 参数中提供则会自动生成并传送一个标头。

HTTPConnection.get_proxy_response_headers()¶

    

~~~
返回一个由从代理服务器接收的响应标头映射到 CONNECT 请求的字典。

如果未发送 CONNECT 请求，该方法将返回 `None`。

在 3.12 版本加入.

HTTPConnection.connect()¶
~~~
    

~~~
当对象被创建后连接到指定的服务器。 默认情况下，如果客户端还未建立连接，此函数会在发送请求时自动被调用。

触发 [音频事件](sys.md#auditing) `http.client.connect` ，带有 `self` 、`host` 、`port` 参数。

HTTPConnection.close()¶
~~~
    

~~~
关闭到服务器的连接。

HTTPConnection.blocksize¶
~~~
    

~~~
用于发送文件类消息体的缓冲区大小。

在 3.7 版本加入.

作为对使用上述 `request()` 方法的替代同，你也可以通过使用下面的四个函数，分步骤发送请的请求。

HTTPConnection.putrequest( _method_ , _url_ , _skip_host =False_, _skip_accept_encoding =False_)¶
~~~
    

~~~
应为连接服务器之后首先调用的函数。将向服务器发送一行数据，包含 _method_ 字符串、 _url_ 字符串和 HTTP 版本（`HTTP/1.1`）。若要禁止自动发送 `Host:` 或 `Accept-Encoding:` 头部信息（比如需要接受其他编码格式的内容），请将 _skip_host_ 或 _skip_accept_encoding_ 设为非 False 值。

HTTPConnection.putheader( _header_ , _argument_ [, _..._ ])¶
~~~
    

~~~
向服务器发送一个 [**RFC 822**](https://datatracker.ietf.org/doc/html/rfc822.md) 格式的头部。将向服务器发送一行由头、冒号和空格以及第一个参数组成的数据。 如果还给出了其他参数，将在后续行中发送，每行由一个制表符和一个参数组成。

HTTPConnection.endheaders( _message_body =None_, _*_ , _encode_chunked =False_)¶
~~~
    

~~~
向服务器发送一个空行，表示头部文件结束。可选的 _message_body_ 参数可用于传入一个与请求相关的消息体。

如果 _encode_chunked_ 为 `True`，则对 _message_body_ 的每次迭代结果将依照 [**RFC 7230**](https://datatracker.ietf.org/doc/html/rfc7230.md) 3.3.1 节的规范进行分块编码。数据如何编码取决于 _message_body_ 的类型。 如果 _message_body_ 实现了 [buffer 接口](../c-api/buffer.md#bufferobjects)，编码将生成一个数据块。如果 _message_body_ 是 [`collections.abc.Iterable`](collections.abc.md#collections.abc.Iterable "collections.abc.Iterable")，则 _message_body_ 的每次迭代都会产生一个块。 如果 _message_body_ 为 [file object](../glossary.md#term-file-object)，那么每次调用 `.read()` 都会产生一个数据块。在 _message_body_ 结束后，本方法立即会自动标记分块编码数据的结束。

备注

由于分块编码的规范要求，迭代器本身产生的空块将被分块编码器忽略。这是为了避免目标服务器因错误编码而过早终止对请求的读取。

在 3.6 版本加入: 支持分块编码。加入了 _encode_chunked_ 参数。

HTTPConnection.send( _data_ )¶
~~~
    

~~~
发送数据到服务器。本函数只应在调用 `endheaders()` 方法之后且调用 `getresponse()` 之前直接调用。

触发 [音频事件](sys.md#auditing) `http.client.send`，参数为 `self`、`data`。

## HTTPResponse 对象¶

`HTTPResponse` 实例封装了来自服务器的 HTTP 响应。通过它可以访问请求头和响应体。响应是可迭代对象，可在 with 语句中使用。

在 3.5 版本发生变更: 现在已实现了 [`io.BufferedIOBase`](io.md#io.BufferedIOBase "io.BufferedIOBase") 接口，并且支持所有的读取操作。

HTTPResponse.read([ _amt_ ])¶
~~~
    

~~~
读取并返回响应体，或后续 _amt_ 个字节。

HTTPResponse.readinto( _b_ )¶
~~~
    

~~~
读取响应体的后续 len(b) 个字节到缓冲区 _b_ 。返回读取的字节数。

在 3.3 版本加入.

HTTPResponse.getheader( _name_ , _default =None_)¶
~~~
    

~~~
返回标头 _name_ 的值，或者如果没有匹配 _name_ 的标头则返回 _default_ 。 如果名为 _name_ 的标头不止一个，则返回以 ', ' 连接的所有值。 如果 _default_ 是任何不为单个字符串的可迭代对象，则其元素同样会以逗号连接的形式返回。

HTTPResponse.getheaders()¶
~~~
    

~~~
返回 (header, value) 元组构成的列表。

HTTPResponse.fileno()¶
~~~
    

~~~
返回底层套接字的 `fileno`。

HTTPResponse.msg¶
~~~
    

~~~
包含响应头的 `http.client.HTTPMessage` 实例。`http.client.HTTPMessage` 是 [`email.message`](email.message.md#module-email.message "email.message: The base class representing email messages.") 的子类。

HTTPResponse.version¶
~~~
    

~~~
服务器采用的 HTTP 协议版本。10 代表 HTTP/1.0，11 代表 HTTP/1.1。

HTTPResponse.url¶
~~~
    

~~~
已读取资源的 URL，通常用于确定是否进行了重定向。

HTTPResponse.headers¶
~~~
    

~~~
响应的头部信息，形式为 [`email.message.EmailMessage`](email.message.md#email.message.EmailMessage "email.message.EmailMessage") 的实例。

HTTPResponse.status¶
~~~
    

~~~
由服务器返回的状态码。

HTTPResponse.reason¶
~~~
    

~~~
服务器返回的原因短语。

HTTPResponse.debuglevel¶
~~~
    

~~~
一个调试钩子。如果 `debuglevel` 大于零，状态信息将在读取和解析响应数据时打印输出到 stdout。

HTTPResponse.closed¶
~~~
    

~~~
如果流被关闭，则为 `True`。

HTTPResponse.geturl()¶
~~~
    

~~~
自 3.9 版本弃用: 已弃用，建议用 `url`。

HTTPResponse.info()¶
~~~
    

~~~
自 3.9 版本弃用: 已弃用，建议用 `headers`。

HTTPResponse.getcode()¶
~~~
    

~~~
自 3.9 版本弃用: 已弃用，建议用 `status` 。

## 例子¶

下面是使用 `GET` 方法的会话示例：
~~~
    
    
~~~shell
>>> import http.client
>>> conn = http.client.HTTPSConnection("www.python.org")
>>> conn.request("GET", "/")
>>> r1 = conn.getresponse()
>>> print(r1.status, r1.reason)
200 OK
>>> data1 = r1.read()  # This will return entire content.
>>> # The following example demonstrates reading data in chunks.
>>> conn.request("GET", "/")
>>> r1 = conn.getresponse()
>>> while chunk := r1.read(200):
...     print(repr(chunk))
b'<!doctype html>\n<!--[if"...
...
>>> # Example of an invalid request
>>> conn = http.client.HTTPSConnection("docs.python.org")
>>> conn.request("GET", "/parrot.spam")
>>> r2 = conn.getresponse()
>>> print(r2.status, r2.reason)
404 Not Found
>>> data2 = r2.read()
>>> conn.close()
~~~

以下是使用 `HEAD` 方法的会话示例。 请注意，`HEAD` 方法从不返回任何数据。

    
    
~~~shell
>>> import http.client
>>> conn = http.client.HTTPSConnection("www.python.org")
>>> conn.request("HEAD", "/")
>>> res = conn.getresponse()
>>> print(res.status, res.reason)
200 OK
>>> data = res.read()
>>> print(len(data))
0
>>> data == b''
True
~~~

下面是一个使用 `POST` 方法的会话示例:

    
    
~~~shell
>>> import http.client, urllib.parse
>>> params = urllib.parse.urlencode({'@number': 12524, '@type': 'issue', '@action': 'show'})
>>> headers = {"Content-type": "application/x-www-form-urlencoded",
...            "Accept": "text/plain"}
>>> conn = http.client.HTTPConnection("bugs.python.org")
>>> conn.request("POST", "", params, headers)
>>> response = conn.getresponse()
>>> print(response.status, response.reason)
302 Found
>>> data = response.read()
>>> data
b'Redirecting to <a href="https://bugs.python.org/issue12524">https://bugs.python.org/issue12524</a>'
>>> conn.close()
~~~

客户端 HTTP `PUT` 请求与 `POST` 请求非常相似。 区别仅在于服务器端 HTTP 服务器将允许通过 `PUT` 请求创建资源。 应该注意自定义的 HTTP 方法也可以在 [`urllib.request.Request`](urllib.request.md#urllib.request.Request "urllib.request.Request") 中通过设置适当的方法属性来进行处理。 下面是一个使用 `PUT` 方法的会话示例:

    
    
~~~shell
>>> # This creates an HTTP request
>>> # with the content of BODY as the enclosed representation
>>> # for the resource http://localhost:8080/file
...
>>> import http.client
>>> BODY = "***filecontents***"
>>> conn = http.client.HTTPConnection("localhost", 8080)
>>> conn.request("PUT", "/file", BODY)
>>> response = conn.getresponse()
>>> print(response.status, response.reason)
200, OK
~~~

## HTTPMessage 对象¶

`http.client.HTTPMessage` 的实例存有 HTTP 响应的头部信息。利用 [`email.message.Message`](email.compat32-message.md#email.message.Message "email.message.Message") 类实现。

