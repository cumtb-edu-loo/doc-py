# 传输和协议¶

前言

传输和协议会被像 [`loop.create_connection()`](asyncio-eventloop.md#asyncio.loop.create_connection "asyncio.loop.create_connection") 这类 **底层** 事件循环接口使用。它们使用基于回调的编程风格支持网络或IPC协议（如HTTP）的高性能实现。

基本上，传输和协议应只在库和框架上使用，而不应该在高层的异步应用中使用它们。

本文档包含 Transports 和 Protocols 。

概述

在最顶层，传输只关心 **怎样** 传送字节内容，而协议决定传送 **哪些** 字节内容(还要在一定程度上考虑何时)。

也可以这样说：从传输的角度来看，传输是套接字(或类似的I/O终端)的抽象，而协议是应用程序的抽象。

换另一种说法，传输和协议一起定义网络I/0和进程间I/O的抽象接口。

传输对象和协议对象总是一对一关系：协议调用传输方法来发送数据，而传输在接收到数据时调用协议方法传递数据。

大部分面向连接的事件循环方法(如 [`loop.create_connection()`](asyncio-eventloop.md#asyncio.loop.create_connection "asyncio.loop.create_connection") ) 通常接受 _protocol_factory_ 参数为接收到的链接创建 _协议_ 对象，并用 _传输_ 对象来表示。这些方法一般会返回 `(transport, protocol)` 元组。

目录

本文档包含下列小节:

  * 传输 部分记载异步IO `BaseTransport` 、 `ReadTransport` 、 `WriteTransport` 、 `Transport` 、 `DatagramTransport` 和 `SubprocessTransport` 等类。

  * The Protocols section documents asyncio `BaseProtocol`, `Protocol`, `BufferedProtocol`, `DatagramProtocol`, and `SubprocessProtocol` classes.

  * 例子 部分展示怎样使用传输、协议和底层事件循环接口。

## 传输¶

**源码:** [Lib/asyncio/transports.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/transports.py)

* * *

传输属于 [`asyncio`](asyncio.md#module-asyncio "asyncio: Asynchronous I/O.") 模块中的类，用来抽象各种通信通道。

传输对象总是由 [异步IO事件循环](asyncio-eventloop.md#asyncio-event-loop) 实例化。

异步IO实现TCP、UDP、SSL和子进程管道的传输。传输上可用的方法由传输的类型决定。

传输类属于 [线程不安全](asyncio-dev.md#asyncio-multithreading) 。

### 传输层级¶

_class _asyncio.BaseTransport¶

    

~~~
所有传输的基类。包含所有异步IO传输共用的方法。

_class _asyncio.WriteTransport( _BaseTransport_ )¶
~~~
    

~~~
只写链接的基础传输。

_WriteTransport_ 类的实例由 [`loop.connect_write_pipe()`](asyncio-eventloop.md#asyncio.loop.connect_write_pipe "asyncio.loop.connect_write_pipe") 事件循环方法返回，也被子进程相关的方法如 [`loop.subprocess_exec()`](asyncio-eventloop.md#asyncio.loop.subprocess_exec "asyncio.loop.subprocess_exec") 使用。

_class _asyncio.ReadTransport( _BaseTransport_ )¶
~~~
    

~~~
只读链接的基础传输。

_ReadTransport_ 类的实例由 [`loop.connect_read_pipe()`](asyncio-eventloop.md#asyncio.loop.connect_read_pipe "asyncio.loop.connect_read_pipe") 事件循环方法返回，也被子进程相关的方法如 [`loop.subprocess_exec()`](asyncio-eventloop.md#asyncio.loop.subprocess_exec "asyncio.loop.subprocess_exec") 使用。

_class _asyncio.Transport( _WriteTransport_ , _ReadTransport_ )¶
~~~
    

~~~
接口代表一个双向传输，如TCP链接。

用户不用直接实例化传输；调用一个功能函数，给它传递协议工厂和其它需要的信息就可以创建传输和协议。

_传输_ 类实例由如 [`loop.create_connection()`](asyncio-eventloop.md#asyncio.loop.create_connection "asyncio.loop.create_connection") 、 [`loop.create_unix_connection()`](asyncio-eventloop.md#asyncio.loop.create_unix_connection "asyncio.loop.create_unix_connection") 、 [`loop.create_server()`](asyncio-eventloop.md#asyncio.loop.create_server "asyncio.loop.create_server") 、 [`loop.sendfile()`](asyncio-eventloop.md#asyncio.loop.sendfile "asyncio.loop.sendfile") 等这类事件循环方法使用或返回。

_class _asyncio.DatagramTransport( _BaseTransport_ )¶
~~~
    

~~~
数据报(UDP)传输链接。

_DatagramTransport_ 类实例由事件循环方法 [`loop.create_datagram_endpoint()`](asyncio-eventloop.md#asyncio.loop.create_datagram_endpoint "asyncio.loop.create_datagram_endpoint") 返回。

_class _asyncio.SubprocessTransport( _BaseTransport_ )¶
~~~
    

~~~
表示父进程和子进程之间连接的抽象。

_SubprocessTransport_ 类的实例由事件循环方法 [`loop.subprocess_shell()`](asyncio-eventloop.md#asyncio.loop.subprocess_shell "asyncio.loop.subprocess_shell") 和 [`loop.subprocess_exec()`](asyncio-eventloop.md#asyncio.loop.subprocess_exec "asyncio.loop.subprocess_exec") 返回。

### 基础传输¶

BaseTransport.close()¶
~~~
    

~~~
关闭传输。

如果传输具有外发数据缓冲区，已缓存的数据将被异步地发送。 之后将不会再接收更多数据。 在所有已缓存的数据被发送之后，协议的 `protocol.connection_lost()` 方法将被调用并以 [`None`](constants.md#None "None") 作为其参数。 在传输关闭后它就不应再被使用。

BaseTransport.is_closing()¶
~~~
    

~~~
返回 `True` ，如果传输正在关闭或已经关闭。

BaseTransport.get_extra_info( _name_ , _default =None_)¶
~~~
    

~~~
返回 传输或它使用的相关资源信息。

_name_ 是表示要获取传输特定信息的字符串。

_default_ 是在信息不可用或传输不支持第三方事件循环实现或当前平台查询时返回的值。

例如下面代码尝试获取传输相关套接字对象:
~~~
    
    
~~~
sock = transport.get_extra_info('socket')
if sock is not None:
    print(sock.getsockopt(...))
~~~

传输可查询信息类别:

  * 套接字:

    * `'peername'`: 套接字链接时的远端地址，[`socket.socket.getpeername()`](socket.md#socket.socket.getpeername "socket.socket.getpeername") 方法的结果 (出错时为 `None` )

    * `'socket'`: [`socket.socket`](socket.md#socket.socket "socket.socket") 实例

    * `'sockname'`: 套接字本地地址， [`socket.socket.getsockname()`](socket.md#socket.socket.getsockname "socket.socket.getsockname") 方法的结果

  * SSL套接字

    * `'compression'`: 用字符串指定压缩算法，或者链接没有压缩时为 `None` ；[`ssl.SSLSocket.compression()`](ssl.md#ssl.SSLSocket.compression "ssl.SSLSocket.compression") 的结果。

    * `'cipher'`: 一个三值的元组，包含使用密码的名称，定义使用的SSL协议的版本，使用的加密位数。 [`ssl.SSLSocket.cipher()`](ssl.md#ssl.SSLSocket.cipher "ssl.SSLSocket.cipher") 的结果。

    * `'peercert'`: 远端凭证； [`ssl.SSLSocket.getpeercert()`](ssl.md#ssl.SSLSocket.getpeercert "ssl.SSLSocket.getpeercert") 结果。

    * `'sslcontext'`: [`ssl.SSLContext`](ssl.md#ssl.SSLContext "ssl.SSLContext") 实例

    * `'ssl_object'`: [`ssl.SSLObject`](ssl.md#ssl.SSLObject "ssl.SSLObject") 或 [`ssl.SSLSocket`](ssl.md#ssl.SSLSocket "ssl.SSLSocket") 实例

  * 管道：

    * `'pipe'`: 管道对象

  * 子进程：

    * `'subprocess'`: [`subprocess.Popen`](subprocess.md#subprocess.Popen "subprocess.Popen") 实例

BaseTransport.set_protocol( _protocol_ )¶

    

~~~
设置一个新协议。

只有两种协议都写明支持切换才能完成切换协议。

BaseTransport.get_protocol()¶
~~~
    

~~~
返回当前协议。

### 只读传输¶

ReadTransport.is_reading()¶
~~~
    

~~~
如果传输接收到新数据时返回 `True` 。

在 3.7 版本加入.

ReadTransport.pause_reading()¶
~~~
    

~~~
暂停传输的接收端。`protocol.data_received()` 方法将不会收到数据，除非 `resume_reading()` 被调用。

在 3.7 版本发生变更: 这个方法幂等的， 它可以在传输已经暂停或关闭时调用。

ReadTransport.resume_reading()¶
~~~
    

~~~
恢复接收端。如果有数据可读取时，协议方法 `protocol.data_received()` 将再次被调用。

在 3.7 版本发生变更: 这个方法幂等的， 它可以在传输已经准备好读取数据时调用。

### 只写传输¶

WriteTransport.abort()¶
~~~
    

~~~
立即关闭传输，不会等待已提交的操作处理完毕。已缓存的数据将会丢失。不会接收更多数据。 最终 [`None`](constants.md#None "None") 将作为协议的 `protocol.connection_lost()` 方法的参数被调用。

WriteTransport.can_write_eof()¶
~~~
    

~~~
如果传输支持 `write_eof()` 返回 [`True`](constants.md#True "True") 否则返回 [`False`](constants.md#False "False") 。

WriteTransport.get_write_buffer_size()¶
~~~
    

~~~
返回传输使用输出缓冲区的当前大小。

WriteTransport.get_write_buffer_limits()¶
~~~
    

~~~
获取写入流控制 _high_ 和 _low_ 高低标记位。返回元组 `(low, high)` ， _low_ 和 _high_ 为正字节数。

使用 `set_write_buffer_limits()` 设置限制。

在 3.4.2 版本加入.

WriteTransport.set_write_buffer_limits( _high =None_, _low =None_)¶
~~~
    

~~~
设置写入流控制 _high_ 和 _low_ 高低标记位。

这两个值（以字节数表示）控制何时调用协议的 `protocol.pause_writing()` 和 `protocol.resume_writing()` 方法。 如果指明，则低水位必须小于或等于高水位。 _high_ 和 _low_ 都不能为负值。

`pause_writing()` 会在缓冲区尺寸大于或等于 _high_ 值时被调用。 如果写入已经被暂停，`resume_writing()` 会在缓冲区尺寸小于或等于 _low_ 值时被调用。

默认值是实现专属的。 如果只给出了高水位值，则低水位值默认为一个小于或等于高水位值的实现传属值。 将 _high_ 设为零会强制将 _low_ 也设为零，并使得 `pause_writing()` 在缓冲区变为非空的任何时刻被调用。 将 _low_ 设为零会使得 `resume_writing()` 在缓冲区为空时只被调用一次。 对于上下限都使用零值通常是不够优化的，因为它减少了并发执行 I/O 和计算的机会。

可使用 `get_write_buffer_limits()` 来获取上下限值。

WriteTransport.write( _data_ )¶
~~~
    

~~~
将一些 _data_ 字节串写入传输。

此方法不会阻塞；它会缓冲数据并安排其被异步地发出。

WriteTransport.writelines( _list_of_data_ )¶
~~~
    

~~~
将数据字节串的列表（或任意可迭代对象）写入传输。 这在功能上等价于在可迭代对象产生的每个元素上调用 `write()`，但其实现可能更为高效。

WriteTransport.write_eof()¶
~~~
    

~~~
在刷新所有已缓冲数据之后关闭传输的写入端。 数据仍可以被接收。

如果传输（例如 SSL）不支持半关闭的连接，此方法会引发 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError")。

### 数据报传输¶

DatagramTransport.sendto( _data_ , _addr =None_)¶
~~~
    

~~~
将 _data_ 字节串发送到 _addr_ (基于传输的目标地址) 所给定的远端对等方。 如果 _addr_ 为 [`None`](constants.md#None "None")，则将数据发送到传输创建时给定的目标地址。

此方法不会阻塞；它会缓冲数据并安排其被异步地发出。

DatagramTransport.abort()¶
~~~
    

~~~
立即关闭传输，不会等待已提交的操作执行完毕。 已缓存的数据将会丢失。 不会接收更多的数据。 协议的 `protocol.connection_lost()` 方法最终将附带 [`None`](constants.md#None "None") 作为参数被调用。

### 子进程传输¶

SubprocessTransport.get_pid()¶
~~~
    

~~~
将子进程的进程 ID 以整数形式返回。

SubprocessTransport.get_pipe_transport( _fd_ )¶
~~~
    

~~~
返回对应于整数文件描述符 _fd_ 的通信管道的传输:

  * `0`: 标准输入 ( _stdin_ ) 的可读流式传输，如果子进程创建时未设置 `stdin=PIPE` 则为 [`None`](constants.md#None "None")

  * `1`: 标准输出 ( _stdout_ ) 的可写流式传输，如果子进程创建时未设置 `stdout=PIPE` 则为 [`None`](constants.md#None "None")

  * `2`: 标准错误 ( _stderr_ ) 的可写流式传输，如果子进程创建时未设置 `stderr=PIPE` 则为 [`None`](constants.md#None "None")

  * 其他 _fd_ : [`None`](constants.md#None "None")

SubprocessTransport.get_returncode()¶
~~~
    

~~~
返回整数形式的进程返回码，或者如果还未返回则为 [`None`](constants.md#None "None")，这类似于 [`subprocess.Popen.returncode`](subprocess.md#subprocess.Popen.returncode "subprocess.Popen.returncode") 属性。

SubprocessTransport.kill()¶
~~~
    

~~~
杀死子进程。

在 POSIX 系统中，函数会发送 SIGKILL 到子进程。 在 Windows 中，此方法是 `terminate()` 的别名。

另请参见 [`subprocess.Popen.kill()`](subprocess.md#subprocess.Popen.kill "subprocess.Popen.kill")。

SubprocessTransport.send_signal( _signal_ )¶
~~~
    

~~~
发送 _signal_ 编号到子进程，与 [`subprocess.Popen.send_signal()`](subprocess.md#subprocess.Popen.send_signal "subprocess.Popen.send_signal") 一样。

SubprocessTransport.terminate()¶
~~~
    

~~~
停止子进程。

在 POSIX 系统中，此方法会发送 SIGTERM 到子进程。 在 Windows 中，则会调用 Windows API 函数 TerminateProcess() 来停止子进程。

另请参见 [`subprocess.Popen.terminate()`](subprocess.md#subprocess.Popen.terminate "subprocess.Popen.terminate")。

SubprocessTransport.close()¶
~~~
    

~~~
通过调用 `kill()` 方法来杀死子进程。

如果子进程尚未返回，并关闭 _stdin_ , _stdout_ 和 _stderr_ 管道的传输。

## 协议¶

**源码:** [Lib/asyncio/protocols.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/protocols.py)

* * *

asyncio 提供了一组抽象基类，它们应当被用于实现网络协议。 这些类被设计为与 传输 配合使用。

抽象基础协议类的子类可以实现其中的部分或全部方法。 所有这些方法都是回调：它们由传输或特定事件调用，例如当数据被接收的时候。 基础协议方法应当由相应的传输来调用。

### 基础协议¶

_class _asyncio.BaseProtocol¶
~~~
    

~~~
带有所有协议的共享方法的基础协议。

_class _asyncio.Protocol( _BaseProtocol_ )¶
~~~
    

~~~
用于实现流式协议（TCP, Unix 套接字等等）的基类。

_class _asyncio.BufferedProtocol( _BaseProtocol_ )¶
~~~
    

~~~
用于实现可对接收缓冲区进行手动控制的流式协议的基类。

_class _asyncio.DatagramProtocol( _BaseProtocol_ )¶
~~~
    

~~~
用于实现数据报（UDP）协议的基类。

_class _asyncio.SubprocessProtocol( _BaseProtocol_ )¶
~~~
    

~~~
用于实现与子进程通信（单向管道）的协议的基类。

### 基础协议¶

所有 asyncio 协议均可实现基础协议回调。

连接回调

连接回调会在所有协议上被调用，每个成功的连接将恰好调用一次。 所有其他协议回调只能在以下两个方法之间被调用。

BaseProtocol.connection_made( _transport_ )¶
~~~
    

~~~
连接建立时被调用。

_transport_ 参数是代表连接的传输。 此协议负责将引用保存至对应的传输。

BaseProtocol.connection_lost( _exc_ )¶
~~~
    

~~~
连接丢失或关闭时将被调用。

方法的参数是一个异常对象或为 [`None`](constants.md#None "None")。 后者意味着收到了常规的 EOF，或者连接被连接的一端取消或关闭。

流程控制回调

流程控制回调可由传输来调用以暂停或恢复协议所执行的写入操作。

请查看 `set_write_buffer_limits()` 方法的文档了解详情。

BaseProtocol.pause_writing()¶
~~~
    

~~~
当传输的缓冲区升至高水位以上时将被调用。

BaseProtocol.resume_writing()¶
~~~
    

~~~
当传输的缓冲区降到低水位以下时将被调用。

如果缓冲区大小等于高水位值，则 `pause_writing()` 不会被调用：缓冲区大小必须要高于该值。

相反地，`resume_writing()` 会在缓冲区大小等于或小于低水位值时被调用。 这些结束条件对于当两个水位取零值时也能确保符合预期的行为是很重要的。

### 流式协议¶

事件方法，例如 [`loop.create_server()`](asyncio-eventloop.md#asyncio.loop.create_server "asyncio.loop.create_server"), [`loop.create_unix_server()`](asyncio-eventloop.md#asyncio.loop.create_unix_server "asyncio.loop.create_unix_server"), [`loop.create_connection()`](asyncio-eventloop.md#asyncio.loop.create_connection "asyncio.loop.create_connection"), [`loop.create_unix_connection()`](asyncio-eventloop.md#asyncio.loop.create_unix_connection "asyncio.loop.create_unix_connection"), [`loop.connect_accepted_socket()`](asyncio-eventloop.md#asyncio.loop.connect_accepted_socket "asyncio.loop.connect_accepted_socket"), [`loop.connect_read_pipe()`](asyncio-eventloop.md#asyncio.loop.connect_read_pipe "asyncio.loop.connect_read_pipe") 和 [`loop.connect_write_pipe()`](asyncio-eventloop.md#asyncio.loop.connect_write_pipe "asyncio.loop.connect_write_pipe") 都接受返回流式协议的工厂。

Protocol.data_received( _data_ )¶
~~~
    

~~~
当收到数据时被调用。 _data_ 为包含入站数据的非空字节串对象。

数据是否会被缓冲、分块或重组取决于具体传输。 通常，你不应依赖于特定的语义而应使你的解析具有通用性和灵活性。 但是，数据总是要以正确的顺序被接收。

此方法在连接打开期间可以被调用任意次数。

但是，`protocol.eof_received()` 最多只会被调用一次。 一旦 `eof_received()` 被调用，`data_received()` 就不会再被调用。

Protocol.eof_received()¶
~~~
    

~~~
当发出信号的另一端不再继续发送数据时（例如通过调用 `transport.write_eof()`，如果另一端也使用 asyncio 的话）被调用。

此方法可能返回假值 (包括 `None`)，在此情况下传输将会自行关闭。 相反地，如果此方法返回真值，将以所用的协议来确定是否要关闭传输。 由于默认实现是返回 `None`，因此它会隐式地关闭连接。

某些传输，包括 SSL 在内，并不支持半关闭的连接，在此情况下从该方法返回真值将导致连接被关闭。

状态机：
~~~
    
    
~~~
start -> connection_made
    [-> data_received]*
    [-> eof_received]?
-> connection_lost -> end
~~~

### 缓冲流协议¶

在 3.7 版本加入.

带缓冲的协议可与任何支持 流式协议 的事件循环方法配合使用。

`BufferedProtocol` 实现允许显式手动分配和控制接收缓冲区。 随后事件循环可以使用协议提供的缓冲区来避免不必要的数据复制。 这对于接收大量数据的协议来说会有明显的性能提升。 复杂的协议实现能显著地减少缓冲区分配的数量。

以下回调是在 `BufferedProtocol` 实例上被调用的:

BufferedProtocol.get_buffer( _sizehint_ )¶

    

~~~
调用后会分配新的接收缓冲区。

_sizehint_ 是推荐的返回缓冲区最小尺寸。 返回小于或大于 _sizehint_ 推荐尺寸的缓冲区也是可接受的。 当设为 -1 时，缓冲区尺寸可以是任意的。 返回尺寸为零的缓冲区则是错误的。

`get_buffer()` 必须返回一个实现了 [缓冲区协议](../c-api/buffer.md#bufferobjects) 的对象。

BufferedProtocol.buffer_updated( _nbytes_ )¶
~~~
    

~~~
用接收的数据更新缓冲区时被调用。

_nbytes_ 是被写入到缓冲区的字节总数。

BufferedProtocol.eof_received()¶
~~~
    

~~~
请查看 `protocol.eof_received()` 方法的文档。

在连接期间 `get_buffer()` 可以被调用任意次数。 但是，`protocol.eof_received()` 最多只能被调用一次，如果被调用，则在此之后 `get_buffer()` 和 `buffer_updated()` 不能再被调用。

状态机：
~~~
    
    
~~~
start -> connection_made
    [-> get_buffer
        [-> buffer_updated]?
    ]*
    [-> eof_received]?
-> connection_lost -> end
~~~

### 数据报协议¶

数据报协议实例应当由传递给 [`loop.create_datagram_endpoint()`](asyncio-eventloop.md#asyncio.loop.create_datagram_endpoint "asyncio.loop.create_datagram_endpoint") 方法的协议工厂来构造。

DatagramProtocol.datagram_received( _data_ , _addr_ )¶

    

~~~
当接收到数据报时被调用。 _data_ 是包含传入数据的字节串对象。 _addr_ 是发送数据的对等端地址；实际的格式取决于具体传输。

DatagramProtocol.error_received( _exc_ )¶
~~~
    

~~~
当前一个发送或接收操作引发 [`OSError`](exceptions.md#OSError "OSError") 时被调用。 _exc_ 是 [`OSError`](exceptions.md#OSError "OSError") 的实例。

此方法会在当传输（例如UDP）检测到无法将数据报传给接收方等极少数情况下被调用。 而在大多数情况下，无法送达的数据报将被静默地丢弃。

备注

在 BSD 系统（macOS, FreeBSD 等等）上，数据报协议不支持流控制，因为没有可靠的方式来检测因写入多过包所导致的发送失败。

套接字总是显示为 'ready' 且多余的包会被丢弃。 有一定的可能性会引发 [`OSError`](exceptions.md#OSError "OSError") 并设置 `errno` 为 [`errno.ENOBUFS`](errno.md#errno.ENOBUFS "errno.ENOBUFS")；如果此异常被引发，它将被报告给 `DatagramProtocol.error_received()`，在其他情况下则会被忽略。

### 子进程协议¶

子进程协议实例应当由传递给 [`loop.subprocess_exec()`](asyncio-eventloop.md#asyncio.loop.subprocess_exec "asyncio.loop.subprocess_exec") 和 [`loop.subprocess_shell()`](asyncio-eventloop.md#asyncio.loop.subprocess_shell "asyncio.loop.subprocess_shell") 方法的协议工厂函数来构造。

SubprocessProtocol.pipe_data_received( _fd_ , _data_ )¶
~~~
    

~~~
当子进程向其 stdout 或 stderr 管道写入数据时被调用。

_fd_ 是以整数表示的管道文件描述符。

_data_ 是包含已接收数据的非空字节串对象。

SubprocessProtocol.pipe_connection_lost( _fd_ , _exc_ )¶
~~~
    

~~~
与子进程通信的其中一个管道关闭时被调用。

_fd_ 以整数表示的已关闭文件描述符。

SubprocessProtocol.process_exited()¶
~~~
    

~~~
子进程退出时被调用。

## 例子¶

### TCP 回显服务器¶

使用 [`loop.create_server()`](asyncio-eventloop.md#asyncio.loop.create_server "asyncio.loop.create_server") 方法创建 TCP 回显服务器，发回已接收的数据，并关闭连接:
~~~
    
    
~~~
import asyncio
~~~
    
    class EchoServerProtocol(asyncio.Protocol):
        def connection_made(self, transport):
            peername = transport.get_extra_info('peername')
            print('Connection from {}'.format(peername))
            self.transport = transport
    
        def data_received(self, data):
            message = data.decode()
            print('Data received: {!r}'.format(message))
    
            print('Send: {!r}'.format(message))
            self.transport.write(data)
    
            print('Close the client socket')
            self.transport.close()
    
    
    async def main():
        # Get a reference to the event loop as we plan to use
        # low-level APIs.
        loop = asyncio.get_running_loop()
    
        server = await loop.create_server(
            EchoServerProtocol,
            '127.0.0.1', 8888)
    
        async with server:
            await server.serve_forever()
    
    
    asyncio.run(main())
    

参见

[使用流的 TCP 回显服务器](asyncio-stream.md#asyncio-tcp-echo-server-streams) 示例，使用了高层级的 [`asyncio.start_server()`](asyncio-stream.md#asyncio.start_server "asyncio.start_server") 函数。

### TCP 回显客户端¶

使用 [`loop.create_connection()`](asyncio-eventloop.md#asyncio.loop.create_connection "asyncio.loop.create_connection") 方法的 TCP 回显客户端，发送数据并等待，直到连接被关闭:

    
    
~~~
import asyncio
~~~
    
    class EchoClientProtocol(asyncio.Protocol):
        def __init__(self, message, on_con_lost):
            self.message = message
            self.on_con_lost = on_con_lost
    
        def connection_made(self, transport):
            transport.write(self.message.encode())
            print('Data sent: {!r}'.format(self.message))
    
        def data_received(self, data):
            print('Data received: {!r}'.format(data.decode()))
    
        def connection_lost(self, exc):
            print('The server closed the connection')
            self.on_con_lost.set_result(True)
    
    
    async def main():
        # Get a reference to the event loop as we plan to use
        # low-level APIs.
        loop = asyncio.get_running_loop()
    
        on_con_lost = loop.create_future()
        message = 'Hello World!'
    
        transport, protocol = await loop.create_connection(
            lambda: EchoClientProtocol(message, on_con_lost),
            '127.0.0.1', 8888)
    
        # Wait until the protocol signals that the connection
        # is lost and close the transport.
        try:
            await on_con_lost
        finally:
            transport.close()
    
    
    asyncio.run(main())
    

参见

[使用流的 TCP 回显客户端](asyncio-stream.md#asyncio-tcp-echo-client-streams) 示例，使用了高层级的 [`asyncio.open_connection()`](asyncio-stream.md#asyncio.open_connection "asyncio.open_connection") 函数。

### UDP 回显服务器¶

使用 [`loop.create_datagram_endpoint()`](asyncio-eventloop.md#asyncio.loop.create_datagram_endpoint "asyncio.loop.create_datagram_endpoint") 方法的 UDP 回显服务器，发回已接收的数据:

    
    
~~~
import asyncio
~~~
    
    class EchoServerProtocol:
        def connection_made(self, transport):
            self.transport = transport
    
        def datagram_received(self, data, addr):
            message = data.decode()
            print('Received %r from %s' % (message, addr))
            print('Send %r to %s' % (message, addr))
            self.transport.sendto(data, addr)
    
    
    async def main():
        print("Starting UDP server")
    
        # Get a reference to the event loop as we plan to use
        # low-level APIs.
        loop = asyncio.get_running_loop()
    
        # One protocol instance will be created to serve all
        # client requests.
        transport, protocol = await loop.create_datagram_endpoint(
            EchoServerProtocol,
            local_addr=('127.0.0.1', 9999))
    
        try:
            await asyncio.sleep(3600)  # Serve for 1 hour.
        finally:
            transport.close()
    
    
    asyncio.run(main())
    

### UDP 回显客户端¶

使用 [`loop.create_datagram_endpoint()`](asyncio-eventloop.md#asyncio.loop.create_datagram_endpoint "asyncio.loop.create_datagram_endpoint") 方法的 UDP 回显客户端，发送数据并在收到回应时关闭传输:

    
    
~~~
import asyncio
~~~
    
    class EchoClientProtocol:
        def __init__(self, message, on_con_lost):
            self.message = message
            self.on_con_lost = on_con_lost
            self.transport = None
    
        def connection_made(self, transport):
            self.transport = transport
            print('Send:', self.message)
            self.transport.sendto(self.message.encode())
    
        def datagram_received(self, data, addr):
            print("Received:", data.decode())
    
            print("Close the socket")
            self.transport.close()
    
        def error_received(self, exc):
            print('Error received:', exc)
    
        def connection_lost(self, exc):
            print("Connection closed")
            self.on_con_lost.set_result(True)
    
    
    async def main():
        # Get a reference to the event loop as we plan to use
        # low-level APIs.
        loop = asyncio.get_running_loop()
    
        on_con_lost = loop.create_future()
        message = "Hello World!"
    
        transport, protocol = await loop.create_datagram_endpoint(
            lambda: EchoClientProtocol(message, on_con_lost),
            remote_addr=('127.0.0.1', 9999))
    
        try:
            await on_con_lost
        finally:
            transport.close()
    
    
    asyncio.run(main())
    

### 链接已存在的套接字¶

附带一个协议使用 [`loop.create_connection()`](asyncio-eventloop.md#asyncio.loop.create_connection "asyncio.loop.create_connection") 方法，等待直到套接字接收数据:

    
    
~~~
import asyncio
import socket
~~~
    
    class MyProtocol(asyncio.Protocol):
    
        def __init__(self, on_con_lost):
            self.transport = None
            self.on_con_lost = on_con_lost
    
        def connection_made(self, transport):
            self.transport = transport
    
        def data_received(self, data):
            print("Received:", data.decode())
    
            # We are done: close the transport;
            # connection_lost() will be called automatically.
            self.transport.close()
    
        def connection_lost(self, exc):
            # The socket has been closed
            self.on_con_lost.set_result(True)
    
    
    async def main():
        # Get a reference to the event loop as we plan to use
        # low-level APIs.
        loop = asyncio.get_running_loop()
        on_con_lost = loop.create_future()
    
        # Create a pair of connected sockets
        rsock, wsock = socket.socketpair()
    
        # Register the socket to wait for data.
        transport, protocol = await loop.create_connection(
            lambda: MyProtocol(on_con_lost), sock=rsock)
    
        # Simulate the reception of data from the network.
        loop.call_soon(wsock.send, 'abc'.encode())
    
        try:
            await protocol.on_con_lost
        finally:
            transport.close()
            wsock.close()
    
    asyncio.run(main())
    

参见

使用低层级的 [`loop.add_reader()`](asyncio-eventloop.md#asyncio.loop.add_reader "asyncio.loop.add_reader") 方法来注册一个 FD 的 [监视文件描述符以读取事件](asyncio-eventloop.md#asyncio-example-watch-fd) 示例。

使用在协程中通过 [`open_connection()`](asyncio-stream.md#asyncio.open_connection "asyncio.open_connection") 函数创建的高层级流的 [注册一个打开的套接字以等待使用流的数据](asyncio-stream.md#asyncio-example-create-connection-streams) 示例。

### loop.subprocess_exec() 与 SubprocessProtocol¶

一个使用子进程协议来获取子进程的输出并等待子进程退出的示例。

这个子进程是由 [`loop.subprocess_exec()`](asyncio-eventloop.md#asyncio.loop.subprocess_exec "asyncio.loop.subprocess_exec") 方法创建的:

    
    
~~~
import asyncio
import sys

class DateProtocol(asyncio.SubprocessProtocol):
    def __init__(self, exit_future):
        self.exit_future = exit_future
        self.output = bytearray()

    def pipe_data_received(self, fd, data):
        self.output.extend(data)

    def process_exited(self):
        self.exit_future.set_result(True)

async def get_date():
    # Get a reference to the event loop as we plan to use
    # low-level APIs.
    loop = asyncio.get_running_loop()

    code = 'import datetime; print(datetime.datetime.now())'
    exit_future = asyncio.Future(loop=loop)

    # Create the subprocess controlled by DateProtocol;
    # redirect the standard output into a pipe.
    transport, protocol = await loop.subprocess_exec(
        lambda: DateProtocol(exit_future),
        sys.executable, '-c', code,
        stdin=None, stderr=None)

    # Wait for the subprocess exit using the process_exited()
    # method of the protocol.
    await exit_future

    # Close the stdout pipe.
    transport.close()

    # Read the output which was collected by the
    # pipe_data_received() method of the protocol.
    data = bytes(protocol.output)
    return data.decode('ascii').rstrip()

date = asyncio.run(get_date())
print(f"Current date: {date}")
~~~

另请参阅使用高层级 API 编写的 [相同示例](asyncio-subprocess.md#asyncio-example-create-subprocess-exec)。

