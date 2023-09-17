# `http.server` \--- HTTP 服务器¶

**源代码：** [Lib/http/server.py](https://github.com/python/cpython/tree/3.12/Lib/http/server.py)

* * *

这个模块定义了用于实现 HTTP 服务器的类。

警告

`http.server` 不推荐用于生产环境。它仅仅实现了 basic security checks 的要求。

[可用性](intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

`HTTPServer` 是 [`socketserver.TCPServer`](socketserver.md#socketserver.TCPServer "socketserver.TCPServer") 的一个子类。它会创建和侦听 HTTP 套接字，并将请求分发给处理程序。创建和运行 HTTP 服务器的代码类似如下所示：

    
    
~~~python
def run(server_class=HTTPServer, handler_class=BaseHTTPRequestHandler):
    server_address = ('', 8000)
    httpd = server_class(server_address, handler_class)
    httpd.serve_forever()
~~~

_class _http.server.HTTPServer( _server_address_ , _RequestHandlerClass_ )¶

    

~~~
该类基于 [`TCPServer`](socketserver.md#socketserver.TCPServer "socketserver.TCPServer") 类，并在实例变量 `server_name` 和 `server_port` 中保存 HTTP 服务器地址。处理程序可通过实例变量 `server` 访问 HTTP 服务器。

_class _http.server.ThreadingHTTPServer( _server_address_ , _RequestHandlerClass_ )¶
~~~
    

~~~
该类相似于 HTTPServer ，只是会利用 [`ThreadingMixIn`](socketserver.md#socketserver.ThreadingMixIn "socketserver.ThreadingMixIn") 对请求进行多线程处理。当需要对 Web 浏览器预先打开套接字进行处理时，这就很有用，这时 `HTTPServer` 会一直等待请求。

在 3.7 版本加入.

实例化 `HTTPServer` 和 `ThreadingHTTPServer` 时，必须给出一个 _RequestHandlerClass_ ，本模块提供了该对象的三种变体：

_class _http.server.BaseHTTPRequestHandler( _request_ , _client_address_ , _server_ )¶
~~~
    

~~~
这个类用于处理到达服务器的 HTTP 请求。 它本身无法响应任何实际的 HTTP 请求；它必须被子类化以处理每个请求方法（例如 GET 或 POST）。 `BaseHTTPRequestHandler` 提供了许多供子类使用的类和实例变量以及方法。

这个处理程序将解析请求和标头，然后调用特定请求类型对应的方法。 方法名称将根据请求来构造。 例如，对于请求方法 `SPAM`，将不带参数地调用 `do_SPAM()` 方法。 所有相关信息会被保存在该处理程序的实际变量中。 子类不需要重载或扩展 `__init__()` 方法。

`BaseHTTPRequestHandler` 具有下列实例变量:

client_address¶
~~~
    

~~~
包含 `(host, port)` 形式的指向客户端地址的元组。

server¶
~~~
    

~~~
包含服务器实例。

close_connection¶
~~~
    

~~~
应当在 `handle_one_request()` 返回之前设定的布尔值，指明是否要期待另一个请求，还是应当关闭连接。

requestline¶
~~~
    

~~~
包含 HTTP 请求行的字符串表示。 末尾的 CRLF 会被去除。 该属性应当由 `handle_one_request()` 来设定。 如果无有效请求行被处理，则它应当被设为空字符串。

command¶
~~~
    

~~~
包含具体的命令（请求类型）。 例如 `'GET'`。

path¶
~~~
    

~~~
包含请求路径。如果URL的查询部分存在， `path` 会包含这个查询部分。使用 [**RFC 3986**](https://datatracker.ietf.org/doc/html/rfc3986.md) 的术语来说，在这里， `path` 包含 `hier-part` 和 `query` 。

request_version¶
~~~
    

~~~
包含请求的版本字符串。 例如 `'HTTP/1.0'`。

headers¶
~~~
    

~~~
存放由 `MessageClass` 类变量所指定的类的实例。 该实例会解析并管理 HTTP 请求中的标头。 [`http.client`](http.client.md#module-http.client "http.client: HTTP and HTTPS protocol client \(requires sockets\).") 中的 [`parse_headers()`](http.client.md#http.client.parse_headers "http.client.parse_headers") 函数将被用来解析标头并且它需要 HTTP 请求提供一个有效的 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 风格的标头。

rfile¶
~~~
    

~~~
一个 [`io.BufferedIOBase`](io.md#io.BufferedIOBase "io.BufferedIOBase") 输入流，准备从可选的输入数据的开头进行读取。

wfile¶
~~~
    

~~~
包含用于写入响应并发回给客户端的输出流。 在写入流时必须正确遵守 HTTP 协议以便成功地实现与 HTTP 客户端的互操作。

在 3.6 版本发生变更: 这是一个 [`io.BufferedIOBase`](io.md#io.BufferedIOBase "io.BufferedIOBase") 流。

`BaseHTTPRequestHandler` 具有下列属性：

server_version¶
~~~
    

~~~
指定服务器软件版本。 你可能会想要重载该属性。 该属性的格式为多个以空格分隔的字符串，其中每个字符串的形式为 name[/version]。 例如 `'BaseHTTP/0.2'`。

sys_version¶
~~~
    

~~~
包含 Python 系统版本，采用 `version_string` 方法和 `server_version` 类变量所支持的形式。 例如 `'Python/1.4'`。

error_message_format¶
~~~
    

~~~
指定应当被 `send_error()` 方法用来构建发给客户端的错误响应的格式字符串。 该字符串应使用来自 `responses` 的变量根据传给 `send_error()` 的状态码来填充默认值。

error_content_type¶
~~~
    

~~~
指定发送给客户端的错误响应的 Content-Type HTTP 标头。 默认值为 `'text/html'`。

protocol_version¶
~~~
    

~~~
指定服务器所符合的 HTTP 版本。 它会在响应中发送以便让客户端知道服务器对于未来请求的通信能力。 如果设置为 `'HTTP/1.1'`，服务器将允许 HTTP 持久连接；但是，你的服务器 _必须_ 在所有对客户端的响应中包括一个准确的 `Content-Length` 标头 (使用 `send_header()`)。 为了保持向下兼容性，该设置默认为 `'HTTP/1.0'`。

MessageClass¶
~~~
    

~~~
指定一个 [`email.message.Message`](email.compat32-message.md#email.message.Message "email.message.Message") 这样的类来解析 HTTP 标头。 通常该属性不会被重载，其默认值为 `http.client.HTTPMessage`。

responses¶
~~~
    

~~~
该属性包含一个整数错误代码与由短消息和长消息组成的二元组的映射。 例如，`{code: (shortmessage, longmessage)}`。 _shortmessage_ 通常是作为消息响应中的 _message_ 键，而 _longmessage_ 则是作为 _explain_ 键。 该属性会被 `send_response_only()` 和 `send_error()` 方法所使用。

`BaseHTTPRequestHandler` 实例具有下列方法:

handle()¶
~~~
    

~~~
调用 `handle_one_request()` 一次（或者如果启用了永久连接则为多次）来处理传入的 HTTP 请求。 你应该完全不需要重载它；而是要实现适当的 `do_*()` 方法。

handle_one_request()¶
~~~
    

~~~
此方法将解析并将请求分配给适当的 `do_*()` 方法。 你应该完全不需要重载它。

handle_expect_100()¶
~~~
    

~~~
当一个符合 HTTP/1.1 标准的服务器接收到一个 `Expect: 100-continue` 请求标头时它会以一个 `100 Continue` 加 `200 OK` 标头作为响应。 如果服务器不希望客户端继续则可以通过重载来引发一个错误。 例如服务器可以选择发送 `417 Expectation Failed` 作为响应标头并 `return False`。

在 3.2 版本加入.

send_error( _code_ , _message =None_, _explain =None_)¶
~~~
    

~~~
发送并记录回复给客户端的完整错误信息。 数字形式的 _code_ 指明 HTTP 错误代码，可选的 _message_ 为简短的易于人类阅读的错误描述。 _explain_ 参数可被用于提供更详细的错误信息；它将使用 `error_message_format` 属性来进行格式化并在一组完整的标头之后作为响应体被发送。 `responses` 属性存放了 _message_ 和 _explain_ 的默认值，它们将在未提供时被使用；对于未知代码两者的默认值均为字符串 `???`。 如果方法为 HEAD 或响应代码是下列值之一则响应体将为空: `1xx`, `204 No Content`, `205 Reset Content`, `304 Not Modified`。

在 3.4 版本发生变更: 错误响应包括一个 Content-Length 标头。 增加了 _explain_ 参数。

send_response( _code_ , _message =None_)¶
~~~
    

~~~
将一个响应标头添加到标头缓冲区并记录被接受的请求。 HTTP 响应行会被写入到内部缓冲区，后面是 _Server_ 和 _Date_ 标头。 这两个标头的值将分别通过 `version_string()` 和 `date_time_string()` 方法获取。 如果服务器不打算使用 `send_header()` 方法发送任何其他标头，则 `send_response()` 后面应该跟一个 `end_headers()` 调用。

在 3.3 版本发生变更: 标头会被存储到内部缓冲区并且需要显式地调用 `end_headers()`。

send_header( _keyword_ , _value_ )¶
~~~
    

~~~
将 HTTP 标头添加到内部缓冲区，它将在 `end_headers()` 或 `flush_headers()` 被发起调用时写入输出流。 _keyword_ 应当指定标头关键字，并以 _value_ 指定其值。 请注意，在 send_header 调用结束之后，必须调用 `end_headers()` 以便完成操作。

在 3.2 版本发生变更: 标头将被存入内部缓冲区。

send_response_only( _code_ , _message =None_)¶
~~~
    

~~~
只发送响应标头，用于当 `100 Continue` 响应被服务器发送给客户端的场合。 标头不会被缓冲而是直接发送到输出流。 如果未指定 _message_ ，则会发送与响应 _code_ 相对应的 HTTP 消息。

在 3.2 版本加入.

end_headers()¶
~~~
    

~~~
将一个空行（指明响应中 HTTP 标头的结束）添加到标头缓冲区并调用 `flush_headers()`。

在 3.2 版本发生变更: 已缓冲的标头会被写入到输出流。

flush_headers()¶
~~~
    

~~~
最终将标头发送到输出流并清空内部标头缓冲区。

在 3.3 版本加入.

log_request( _code ='-'_, _size ='-'_)¶
~~~
    

~~~
记录一次被接受（成功）的请求。 _code_ 应当指定与请求相关联的 HTTP 代码。 如果请求的大小可用，则它应当作为 _size_ 形参传入。

log_error( _..._ )¶
~~~
    

~~~
当请求无法完成时记录一次错误。 默认情况下，它会将消息传给 `log_message()`，因此它接受同样的参数 ( _format_ 和一些额外的值)。

log_message( _format_ , _..._ )¶
~~~
    

~~~
将任意一条消息记录到 `sys.stderr`。 此方法通常会被重载以创建自定义的错误日志记录机制。 _format_ 参数是标准 printf 风格的格式字符串，其中会将传给 `log_message()` 的额外参数用作格式化操作的输入。 每条消息日志记录的开头都会加上客户端 IP 地址和当前日期时间。

version_string()¶
~~~
    

~~~
返回服务器软件的版本字符串。 该值为 `server_version` 与 `sys_version` 属性的组合。

date_time_string( _timestamp =None_)¶
~~~
    

~~~
返回由 _timestamp_ 所给定的日期和时间（参数应为 `None` 或为 [`time.time()`](time.md#time.time "time.time") 所返回的格式），格式化为一个消息标头。 如果省略 _timestamp_ ，则会使用当前日期和时间。

结果看起来像 `'Sun, 06 Nov 1994 08:49:37 GMT'`。

log_date_time_string()¶
~~~
    

~~~
返回当前的日期和时间，为日志格式化

address_string()¶
~~~
    

~~~
返回客户端的地址

在 3.3 版本发生变更: 在之前版本中，会执行一次名称查找。 为了避免名称解析的时延，现在将总是返回 IP 地址。

_class _http.server.SimpleHTTPRequestHandler( _request_ , _client_address_ , _server_ , _directory =None_)¶
~~~
    

~~~
这个类会为目录 _directory_ 及以下的文件提供发布服务，或者如果未提供 _directory_ 则为当前目录，直接将目录结构映射到 HTTP 请求。

在 3.7 版本加入: _directory_ 形参。

在 3.9 版本发生变更: _directory_ 形参接受一个 [path-like object](../glossary.md#term-path-like-object)。

诸如解析请求之类的大量工作都是由基类 `BaseHTTPRequestHandler` 完成的。本类实现了 `do_GET()` 和 `do_HEAD()` 函数。

以下是 `SimpleHTTPRequestHandler` 的类属性。

server_version¶
~~~
    

~~~
这会是 `"SimpleHTTP/" + __version__`，其中 `__version__` 定义于模块级别。

extensions_map¶
~~~
    

~~~
将后缀映射为 MIME 类型的字典，其中包含了覆盖系统默认值的自定义映射关系。不区分大小写，因此字典键只应为小写值。

在 3.9 版本发生变更: 此字典不再填充默认的系统映射，而只包含覆盖值。

`SimpleHTTPRequestHandler` 类定义了以下方法：

do_HEAD()¶
~~~
    

~~~
本方法为 `'HEAD'` 请求提供服务：它将发送等同于 `GET` 请求的头文件。关于合法头部信息的更完整解释，请参阅 `do_GET()` 方法。

do_GET()¶
~~~
    

~~~
通过将请求解释为相对于当前工作目录的路径，将请求映射到某个本地文件。

如果请求被映射到目录，则会依次检查该目录是否存在 `index.md` 或 `index.htm` 文件。若存在则返回文件内容；否则会调用 `list_directory()` 方法生成目录列表。本方法将利用 [`os.listdir()`](os.md#os.listdir "os.listdir") 扫描目录，如果 [`listdir()`](os.md#os.listdir "os.listdir") 失败，则返回 `404` 出错应答。

如果请求被映射到文件，则会打开该文件。 打开文件时的任何 [`OSError`](exceptions.md#OSError "OSError") 异常都会被映射为 `404`, `'File not found'` 错误。 如果请求中带有 `'If-Modified-Since'` 标头，而在此时间点之后文件未作修改，则会发送 `304`, `'Not Modified'` 的响应。 否则会调用 `guess_type()` 方法猜测内容的类型，该方法会反过来用到 _extensions_map_ 变量，并返回文件内容。

将会输出 `'Content-type:'` 头部信息，带上猜出的内容类型，然后是 `'Content-Length:'` 头部信息，带有文件的大小，以及 `'Last-Modified:'` 头部信息，带有文件的修改时间。

后面是一个空行，标志着头部信息的结束，然后输出文件的内容。如果文件的 MIME 类型以 `text/` 开头，文件将以文本模式打开；否则将使用二进制模式。

示例用法请见在 [Lib/http/server.py](https://github.com/python/cpython/tree/3.12/Lib/http/server.py) 中 `test` 函数的实现。

在 3.7 版本发生变更: 为 `'If-Modified-Since'` 头部信息提供支持。

`SimpleHTTPRequestHandler` 类的用法可如下所示，以便创建一个非常简单的 Web 服务，为相对于当前目录的文件提供服务：
~~~
    
    
~~~
import http.server
import socketserver

PORT = 8000

Handler = http.server.SimpleHTTPRequestHandler

with socketserver.TCPServer(("", PORT), Handler) as httpd:
    print("serving at port", PORT)
    httpd.serve_forever()
~~~

`SimpleHTTPRequestHandler` 也可以被子类化以便增强其行为，例如通过重载类属性 `index_pages` 以使用不同的 index 文件名。

`http.server` 也可以使用解释器的 [`-m`](../using/cmdline.md#cmdoption-m) 参数直接调用。 与前面的例子类似，这将提供相对于当前目录的文件:

    
    
~~~
python -m http.server
~~~

服务器默认监听端口为8000。可以通过传递所需的端口号作为参数来覆盖默认值:

    
    
~~~
python -m http.server 9000
~~~

默认情况下，服务器将自己绑定到所有接口。 选项 -b/--bind 指定了一个特定的地址，它应该与之绑定。 IPv4 和 IPv6 地址都被支持。例如，下面的命令使服务器只绑定到 localhost:

    
    
~~~
python -m http.server --bind 127.0.0.1
~~~

在 3.4 版本加入: 引入了 `--bind` 参数。

在 3.8 版本加入: 为了支持 IPv6 改进了 `--bind` 参数。

默认情况下，服务器使用当前目录。选项 `-d/--directory` 指定了一个它应该提供文件的目录。例如，下面的命令使用一个特定的目录:

    
    
~~~
python -m http.server --directory /tmp/
~~~

在 3.7 版本加入: `--directory` 参数被引入。

在默认情况下，服务器将遵循 HTTP/1.0 标准。 选项 `-p/--protocol` 指定了服务器所符合的 HTTP 版本。 例如，以下命令将运行一个符合 HTTP/1.1 标准的服务器:

    
    
~~~
python -m http.server --protocol HTTP/1.1
~~~

在 3.11 版本加入: 引入了 `--protocol` 参数。

_class _http.server.CGIHTTPRequestHandler( _request_ , _client_address_ , _server_ )¶

    

~~~
该类可为当前及以下目录中的文件或输出 CGI 脚本提供服务。注意，把 HTTP 分层结构映射到本地目录结构，这与 `SimpleHTTPRequestHandler` 完全一样。

备注

由 `CGIHTTPRequestHandler` 类运行的 CGI 脚本不能进行重定向操作（HTTP 代码302），因为在执行 CGI 脚本之前会发送代码 200（接下来就输出脚本）。这样状态码就冲突了。

然而，如果这个类猜测它是一个 CGI 脚本，那么就会运行该 CGI 脚本，而不是作为文件提供出去。 只会识别基于目录的 CGI —— 另有一种常用的服务器设置，即标识 CGI 脚本是通过特殊的扩展名。

如果请求指向 `cgi_directories` 以下的路径，`do_GET()` 和 `do_HEAD()` 函数已作修改，不是给出文件，而是运行 CGI 脚本并输出结果。

`CGIHTTPRequestHandler` 定义了以下数据成员：

cgi_directories¶
~~~
    

~~~
默认为 `['/cgi-bin', '/htbin']`，视作 CGI 脚本所在目录。

`CGIHTTPRequestHandler` 定义了以下方法：

do_POST()¶
~~~
    

~~~
本方法服务于 `'POST'` 请求，仅用于 CGI 脚本。如果试图向非 CGI 网址发送 POST 请求，则会输出错误 501：Can only POST to CGI scripts"。

请注意，为了保证安全性，CGI 脚本将以用户 nobody 的 UID 运行。CGI 脚本运行错误将被转换为错误 403。

从 3.13 版起不建议使用，将在 3.15 版中移除: `CGIHTTPRequestHandler` is being removed in 3.15. CGI has not been considered a good way to do things for well over a decade. This code has been unmaintained for a while now and sees very little practical use. Retaining it could lead to further security considerations.

通过在命令行传入 `--cgi` 参数，可以启用 `CGIHTTPRequestHandler` ：
~~~
    
    
~~~
python -m http.server --cgi
~~~

从 3.13 版起不建议使用，将在 3.15 版中移除: `http.server` command line `--cgi` support is being removed because `CGIHTTPRequestHandler` is being removed.

## 安全考量¶

当处理请求时，`SimpleHTTPRequestHandler` 会解析符号链接，这有可能使得指定文件夹以外的文件被暴露。

较早版本的 Python 不会擦除从 `python -m http.server` 或默认的 `BaseHTTPRequestHandler` `.log_message` 实现发送到 stderr 的日志消息中的控制字符。 这可以允许连接到你的服务器的远程客户端向你的终端发送邪恶的控制代码。

在 3.12 版本加入: 控制字符会在 stderr 日志中被擦除。

