# 流¶

**源码:** [Lib/asyncio/streams.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/streams.py)

* * *

流是用于处理网络连接的支持 async/await 的高层级原语。 流允许发送和接收数据，而不需要使用回调或低级协议和传输。

下面是一个使用 asyncio streams 编写的 TCP echo 客户端示例:

    
    
~~~
import asyncio

async def tcp_echo_client(message):
    reader, writer = await asyncio.open_connection(
        '127.0.0.1', 8888)

    print(f'Send: {message!r}')
    writer.write(message.encode())
    await writer.drain()

    data = await reader.read(100)
    print(f'Received: {data.decode()!r}')

    print('Close the connection')
    writer.close()
    await writer.wait_closed()

asyncio.run(tcp_echo_client('Hello World!'))
~~~

参见下面的 Examples 部分。

Stream 函数

下面的高级 asyncio 函数可以用来创建和处理流:

_coroutine _asyncio.open_connection( _host =None_, _port =None_, _*_ , _limit =None_, _ssl =None_, _family =0_, _proto =0_, _flags =0_, _sock =None_, _local_addr =None_, _server_hostname =None_, _ssl_handshake_timeout =None_, _ssl_shutdown_timeout =None_, _happy_eyeballs_delay =None_, _interleave =None_)¶

    

~~~
建立网络连接并返回一对 `(reader, writer)` 对象。

返回的 _reader_ 和 _writer_ 对象是 `StreamReader` 和 `StreamWriter` 类的实例。

_limit_ 确定返回的 `StreamReader` 实例使用的缓冲区大小限制。默认情况下， _limit_ 设置为 64 KiB 。

其余的参数直接传递到 [`loop.create_connection()`](asyncio-eventloop.md#asyncio.loop.create_connection "asyncio.loop.create_connection") 。

备注

_sock_ 参数可将套接字的所有权转给所创建的 `StreamWriter`。 要关闭该套接字，请调用其 `close()` 方法。

在 3.7 版本发生变更: 添加了 _ssl_handshake_timeout_ 形参。

在 3.8 版本加入: 增加了 _happy_eyeballs_delay_ 和 _interleave_ 形参。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

在 3.11 版本发生变更: 添加了 _ssl_shutdown_timeout_ 形参。

_coroutine _asyncio.start_server( _client_connected_cb_ , _host =None_, _port =None_, _*_ , _limit =None_, _family =socket.AF_UNSPEC_, _flags =socket.AI_PASSIVE_, _sock =None_, _backlog =100_, _ssl =None_, _reuse_address =None_, _reuse_port =None_, _ssl_handshake_timeout =None_, _ssl_shutdown_timeout =None_, _start_serving =True_)¶
~~~
    

~~~
启动套接字服务。

当一个新的客户端连接被建立时，回调函数 _client_connected_cb_ 会被调用。该函数会接收到一对参数 `(reader, writer)` ，reader是类 `StreamReader` 的实例，而writer是类 `StreamWriter` 的实例。

_client_connected_cb_ 即可以是普通的可调用对象也可以是一个 [协程函数](asyncio-task.md#coroutine); 如果它是一个协程函数，它将自动作为 [`Task`](asyncio-task.md#asyncio.Task "asyncio.Task") 被调度。

_limit_ 确定返回的 `StreamReader` 实例使用的缓冲区大小限制。默认情况下， _limit_ 设置为 64 KiB 。

余下的参数将会直接传递给 [`loop.create_server()`](asyncio-eventloop.md#asyncio.loop.create_server "asyncio.loop.create_server").

备注

_sock_ 参数可将套接字的所有权转给所创建的服务器。 要关闭该套接字，请调用服务器的 [`close()`](asyncio-eventloop.md#asyncio.Server.close "asyncio.Server.close") 方法。

在 3.7 版本发生变更: 增加了 _ssl_handshake_timeout_ 和 _start_serving_ 形参。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

在 3.11 版本发生变更: 添加了 _ssl_shutdown_timeout_ 形参。

Unix 套接字

_coroutine _asyncio.open_unix_connection( _path =None_, _*_ , _limit =None_, _ssl =None_, _sock =None_, _server_hostname =None_, _ssl_handshake_timeout =None_, _ssl_shutdown_timeout =None_)¶
~~~
    

~~~
> 建立一个 Unix 套接字连接并返回 `(reader, writer)` 这对返回值。
>
> 与 `open_connection()` 相似，但是是在 Unix 套接字上的操作。
>
> 请看文档 [`loop.create_unix_connection()`](asyncio-eventloop.md#asyncio.loop.create_unix_connection "asyncio.loop.create_unix_connection").
>
> 备注
>
> _sock_ 参数可将套接字的所有权转给所创建的 `StreamWriter`。 要关闭该套接字，请调用其 `close()` 方法。
>
> [可用性](intro.md#availability): Unix。
>
> 在 3.7 版本发生变更: 增加了 _ssl_handshake_timeout_ 形参。 现在 _path_ 形参可以是一个 [path-like object](../glossary.md#term-path-like-object)
>
> 在 3.10 版本发生变更: 移除了 _loop_ 形参。

在 3.11 版本发生变更: 添加了 _ssl_shutdown_timeout_ 形参。

_coroutine _asyncio.start_unix_server( _client_connected_cb_ , _path =None_, _*_ , _limit =None_, _sock =None_, _backlog =100_, _ssl =None_, _ssl_handshake_timeout =None_, _ssl_shutdown_timeout =None_, _start_serving =True_)¶
~~~
    

~~~
启动一个 Unix 套接字服务。

与 `start_server()` 相似，但是是在 Unix 套接字上的操作。

请看文档 [`loop.create_unix_server()`](asyncio-eventloop.md#asyncio.loop.create_unix_server "asyncio.loop.create_unix_server").

备注

_sock_ 参数可将套接字的所有权转给所创建的服务器。 要关闭该套接字，请调用服务器的 [`close()`](asyncio-eventloop.md#asyncio.Server.close "asyncio.Server.close") 方法。

[可用性](intro.md#availability): Unix。

在 3.7 版本发生变更: 增加了 _ssl_handshake_timeout_ 和 _start_serving_ 形参。 现在 _path_ 形参可以是一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

在 3.11 版本发生变更: 添加了 _ssl_shutdown_timeout_ 形参。

## StreamReader¶

_class _asyncio.StreamReader¶
~~~
    

~~~
代表一个提供从 IO 流读取数据的 API 的读取器。 作为一个 [asynchronous iterable](../glossary.md#term-asynchronous-iterable)，该对象支持 [`async for`](../reference/compound_stmts.md#async-for) 语句。

不推荐直接实例化 _StreamReader_ 对象，建议使用 `open_connection()` 和 `start_server()` 来获取 _StreamReader_ 实例。

_coroutine _read( _n =-1_)¶
~~~
    

~~~
从流读取至多 _n_ 个字节。

如果未提供 _n_ 或是设为 `-1`，则一直读取到 EOF，然后返回所读取的全部 [`bytes`](stdtypes.md#bytes "bytes")。 如果收到 EOF 并且内部缓冲区为空，则返回一个空 `bytes` 对象。object.

如果 _n_ 为 `0`，则立即返回一个空 `bytes` 对象。

如果 _n_ 为正值，则一旦内部缓冲区有至少 1 个字节可用就返回至多 _n_ 个可用的 `bytes`。 如果在读取任何字节之前收到 EOF，则返回一个空 `bytes` 对象。

_coroutine _readline()¶
~~~
    

~~~
读取一行，其中“行”指的是以 `\n` 结尾的字节序列。

如果读到EOF而没有找到 `\n` ，该方法返回部分读取的数据。

如果读到EOF，且内部缓冲区为空，则返回一个空的 `bytes` 对象。

_coroutine _readexactly( _n_ )¶
~~~
    

~~~
精确读取 _n_ 个 bytes，不会超过也不能少于。

如果在读取完 _n_ 个byte之前读取到EOF，则会引发 [`IncompleteReadError`](asyncio-exceptions.md#asyncio.IncompleteReadError "asyncio.IncompleteReadError") 异常。使用 [`IncompleteReadError.partial`](asyncio-exceptions.md#asyncio.IncompleteReadError.partial "asyncio.IncompleteReadError.partial") 属性来获取到达流结束之前读取的 bytes 字符串。

_coroutine _readuntil( _separator =b'\n'_)¶
~~~
    

~~~
从流中读取数据直至遇到 _separator_

成功后，数据和指定的separator将从内部缓冲区中删除(或者说被消费掉)。返回的数据将包括在末尾的指定separator。

如果读取的数据量超过了配置的流限制，将引发 [`LimitOverrunError`](asyncio-exceptions.md#asyncio.LimitOverrunError "asyncio.LimitOverrunError") 异常，数据将留在内部缓冲区中并可以再次读取。

如果在找到完整的separator之前到达EOF，则会引发 [`IncompleteReadError`](asyncio-exceptions.md#asyncio.IncompleteReadError "asyncio.IncompleteReadError") 异常，并重置内部缓冲区。 [`IncompleteReadError.partial`](asyncio-exceptions.md#asyncio.IncompleteReadError.partial "asyncio.IncompleteReadError.partial") 属性可能包含指定separator的一部分。

在 3.5.2 版本加入.

at_eof()¶
~~~
    

~~~
如果缓冲区为空并且 `feed_eof()` 被调用，则返回 `True` 。

## StreamWriter¶

_class _asyncio.StreamWriter¶
~~~
    

~~~
这个类表示一个写入器对象，该对象提供api以便于写数据至IO流中。

不建议直接实例化 _StreamWriter_ ；而应改用 `open_connection()` 和 `start_server()`。

write( _data_ )¶
~~~
    

~~~
此方法会尝试立即将 _data_ 写入到下层的套接字。 如果写入失败，数据会被排入内部写缓冲队列直到可以被发送。

此方法应当与 `drain()` 方法一起使用:
~~~
    
    
~~~
stream.write(data)
await stream.drain()
~~~

writelines( _data_ )¶

    

~~~
此方法会立即尝试将一个字节串列表（或任何可迭代对象）写入到下层的套接字。 如果写入失败，数据会被排入内部写缓冲队列直到可以被发送。

此方法应当与 `drain()` 方法一起使用:
~~~
    
    
~~~
stream.writelines(lines)
await stream.drain()
~~~

close()¶

    

~~~
此方法会关闭流以及下层的套接字。

此方法应当于 `wait_closed()` 方法一起使用，但这并非强制要求:
~~~
    
    
~~~
stream.close()
await stream.wait_closed()
~~~

can_write_eof()¶

    

~~~
如果下层的传输支持 `write_eof()` 方法则返回 `True`，否则返回 `False`。

write_eof()¶
~~~
    

~~~
在已缓冲的写入数据被刷新后关闭流的写入端。

transport¶
~~~
    

~~~
返回下层的 asyncio 传输。

get_extra_info( _name_ , _default =None_)¶
~~~
    

~~~
访问可选的传输信息；详情参见 [`BaseTransport.get_extra_info()`](asyncio-protocol.md#asyncio.BaseTransport.get_extra_info "asyncio.BaseTransport.get_extra_info")。

_coroutine _drain()¶
~~~
    

~~~
等待直到可以适当地恢复写入到流。 示例:
~~~
    
    
~~~
writer.write(data)
await writer.drain()
~~~

这是一个与下层的 IO 写缓冲区进行交互的流程控制方法。 当缓冲区大小达到最高水位（最大上限）时， _drain()_ 会阻塞直到缓冲区大小减少至最低水位以便恢复写入。 当没有要等待的数据时，`drain()` 会立即返回。

_coroutine _start_tls( _sslcontext_ , _\\*_ , _server_hostname=None_ , _ssl_handshake_timeout=None_ , _ssl_shutdown_timeout=None_ )¶

    

~~~
将现有基于流的连接升级到 TLS。

参数：

  * _sslcontext_ ：一个已经配置好的 [`SSLContext`](ssl.md#ssl.SSLContext "ssl.SSLContext") 实例。

  * _server_hostname_ ：设置或者覆盖目标服务器证书中相对应的主机名。

  * _ssl_handshake_timeout_ 是在放弃连接之前要等待 TLS 握手完成的秒数。 如为 `None` (默认值) 则使用 `60.0`。

  * _ssl_shutdown_timeout_ 是在放弃连接之前要等待 SSL 关闭完成的秒数。 如为 `None` (默认值) 则使用 `30.0`。

在 3.11 版本加入.

在 3.12 版本发生变更: 添加了 _ssl_shutdown_timeout_ 形参。

is_closing()¶
~~~
    

~~~
如果流已被关闭或正在被关闭则返回 `True`。

在 3.7 版本加入.

_coroutine _wait_closed()¶
~~~
    

~~~
等待直到流被关闭。

应当在 `close()` 之后调用以等待直到下层连接被关闭，确保所有数据在退出程序之前已刷新。

在 3.7 版本加入.

## 例子¶

### 使用流的 TCP 回显客户端¶

使用 `asyncio.open_connection()` 函数的 TCP 回显客户端:
~~~
    
    
~~~
import asyncio

async def tcp_echo_client(message):
    reader, writer = await asyncio.open_connection(
        '127.0.0.1', 8888)

    print(f'Send: {message!r}')
    writer.write(message.encode())
    await writer.drain()

    data = await reader.read(100)
    print(f'Received: {data.decode()!r}')

    print('Close the connection')
    writer.close()
    await writer.wait_closed()

asyncio.run(tcp_echo_client('Hello World!'))
~~~

参见

使用低层级 [`loop.create_connection()`](asyncio-eventloop.md#asyncio.loop.create_connection "asyncio.loop.create_connection") 方法的 [TCP 回显客户端协议](asyncio-protocol.md#asyncio-example-tcp-echo-client-protocol) 示例。

### 使用流的 TCP 回显服务器¶

TCP 回显服务器使用 `asyncio.start_server()` 函数:

    
    
~~~
import asyncio

async def handle_echo(reader, writer):
    data = await reader.read(100)
    message = data.decode()
    addr = writer.get_extra_info('peername')

    print(f"Received {message!r} from {addr!r}")

    print(f"Send: {message!r}")
    writer.write(data)
    await writer.drain()

    print("Close the connection")
    writer.close()
    await writer.wait_closed()

async def main():
    server = await asyncio.start_server(
        handle_echo, '127.0.0.1', 8888)

    addrs = ', '.join(str(sock.getsockname()) for sock in server.sockets)
    print(f'Serving on {addrs}')

    async with server:
        await server.serve_forever()

asyncio.run(main())
~~~

参见

使用 [`loop.create_server()`](asyncio-eventloop.md#asyncio.loop.create_server "asyncio.loop.create_server") 方法的 [TCP 回显服务器协议](asyncio-protocol.md#asyncio-example-tcp-echo-server-protocol) 示例。

### 获取 HTTP 标头¶

查询命令行传入 URL 的 HTTP 标头的简单示例:

    
    
~~~
import asyncio
import urllib.parse
import sys

async def print_http_headers(url):
    url = urllib.parse.urlsplit(url)
    if url.scheme == 'https':
        reader, writer = await asyncio.open_connection(
            url.hostname, 443, ssl=True)
    else:
        reader, writer = await asyncio.open_connection(
            url.hostname, 80)

    query = (
        f"HEAD {url.path or '/'} HTTP/1.0\r\n"
        f"Host: {url.hostname}\r\n"
        f"\r\n"
    )

    writer.write(query.encode('latin-1'))
    while True:
        line = await reader.readline()
        if not line:
            break

        line = line.decode('latin1').rstrip()
        if line:
            print(f'HTTP header> {line}')

    # Ignore the body, close the socket
    writer.close()
    await writer.wait_closed()

url = sys.argv[1]
asyncio.run(print_http_headers(url))
~~~

用法：

    
    
~~~
python example.py http://example.com/path/page.md
~~~

或使用 HTTPS:

    
    
~~~
python example.py https://example.com/path/page.md
~~~

### 注册一个打开的套接字以等待使用流的数据¶

使用 `open_connection()` 函数实现等待直到套接字接收到数据的协程:

    
    
~~~
import asyncio
import socket

async def wait_for_data():
    # Get a reference to the current event loop because
    # we want to access low-level APIs.
    loop = asyncio.get_running_loop()

    # Create a pair of connected sockets.
    rsock, wsock = socket.socketpair()

    # Register the open socket to wait for data.
    reader, writer = await asyncio.open_connection(sock=rsock)

    # Simulate the reception of data from the network
    loop.call_soon(wsock.send, 'abc'.encode())

    # Wait for data
    data = await reader.read(100)

    # Got data, we are done: close the socket
    print("Received:", data.decode())
    writer.close()
    await writer.wait_closed()

    # Close the second socket
    wsock.close()

asyncio.run(wait_for_data())
~~~

参见

使用低层级协议以及 [`loop.create_connection()`](asyncio-eventloop.md#asyncio.loop.create_connection "asyncio.loop.create_connection") 方法的 [注册一个打开的套接字以等待使用协议的数据](asyncio-protocol.md#asyncio-example-create-connection) 示例。

使用低层级的 [`loop.add_reader()`](asyncio-eventloop.md#asyncio.loop.add_reader "asyncio.loop.add_reader") 方法来监视文件描述符的 [监视文件描述符以读取事件](asyncio-eventloop.md#asyncio-example-watch-fd) 示例。

