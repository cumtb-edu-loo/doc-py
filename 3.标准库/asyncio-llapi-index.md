# 低层级 API 索引¶

本页列出所有低层级的 asyncio API。

## 获取事件循环¶

[`asyncio.get_running_loop()`](asyncio-eventloop.md#asyncio.get_running_loop "asyncio.get_running_loop")

|

获取当前运行的事件循环 **首选** 函数。  
  
---|---  
  
[`asyncio.get_event_loop()`](asyncio-eventloop.md#asyncio.get_event_loop "asyncio.get_event_loop")

|

获取一个事件循环实例（正在运行的事件循环或通过当前策略确定的当前事件循环）。  
  
[`asyncio.set_event_loop()`](asyncio-eventloop.md#asyncio.set_event_loop "asyncio.set_event_loop")

|

通过当前策略将事件循环设置当前事件循环。  
  
[`asyncio.new_event_loop()`](asyncio-eventloop.md#asyncio.new_event_loop "asyncio.new_event_loop")

|

创建一个新的事件循环。  
  
例子

  * [使用asyncio.get_running_loop()](asyncio-future.md#asyncio-example-future)。

## 事件循环方法集¶

另请参阅有关 [事件循环方法集](asyncio-eventloop.md#asyncio-event-loop-methods) 的主文档章节。

生命周期

[`loop.run_until_complete()`](asyncio-eventloop.md#asyncio.loop.run_until_complete "asyncio.loop.run_until_complete")

|

运行一个期程/任务/可等待对象直到完成。  
  
---|---  
  
[`loop.run_forever()`](asyncio-eventloop.md#asyncio.loop.run_forever "asyncio.loop.run_forever")

|

一直运行事件循环。  
  
[`loop.stop()`](asyncio-eventloop.md#asyncio.loop.stop "asyncio.loop.stop")

|

停止事件循环。  
  
[`loop.close()`](asyncio-eventloop.md#asyncio.loop.close "asyncio.loop.close")

|

关闭事件循环。  
  
[`loop.is_running()`](asyncio-eventloop.md#asyncio.loop.is_running "asyncio.loop.is_running")

|

返回 `True` ， 如果事件循环正在运行。  
  
[`loop.is_closed()`](asyncio-eventloop.md#asyncio.loop.is_closed "asyncio.loop.is_closed")

|

返回 `True` ，如果事件循环已经被关闭 。  
  
`await` [`loop.shutdown_asyncgens()`](asyncio-eventloop.md#asyncio.loop.shutdown_asyncgens "asyncio.loop.shutdown_asyncgens")

|

关闭异步生成器。  
  
调试

[`loop.set_debug()`](asyncio-eventloop.md#asyncio.loop.set_debug "asyncio.loop.set_debug")

|

开启或禁用调试模式。  
  
---|---  
  
[`loop.get_debug()`](asyncio-eventloop.md#asyncio.loop.get_debug "asyncio.loop.get_debug")

|

获取当前测试模式。  
  
调度回调函数

[`loop.call_soon()`](asyncio-eventloop.md#asyncio.loop.call_soon "asyncio.loop.call_soon")

|

尽快调用回调。  
  
---|---  
  
[`loop.call_soon_threadsafe()`](asyncio-eventloop.md#asyncio.loop.call_soon_threadsafe "asyncio.loop.call_soon_threadsafe")

|

[`loop.call_soon()`](asyncio-eventloop.md#asyncio.loop.call_soon "asyncio.loop.call_soon") 方法线程安全的变体。  
  
[`loop.call_later()`](asyncio-eventloop.md#asyncio.loop.call_later "asyncio.loop.call_later")

|

在给定时间 _之后_ 调用回调函数。  
  
[`loop.call_at()`](asyncio-eventloop.md#asyncio.loop.call_at "asyncio.loop.call_at")

|

在 _指定_ 时间调用回调函数。  
  
线程/进程池

`await` [`loop.run_in_executor()`](asyncio-eventloop.md#asyncio.loop.run_in_executor "asyncio.loop.run_in_executor")

|

在 [`concurrent.futures`](concurrent.futures.md#module-concurrent.futures "concurrent.futures: Execute computations concurrently using threads or processes.") 执行器中运行一个独占CPU或其它阻塞函数。  
  
---|---  
  
[`loop.set_default_executor()`](asyncio-eventloop.md#asyncio.loop.set_default_executor "asyncio.loop.set_default_executor")

|

设置 [`loop.run_in_executor()`](asyncio-eventloop.md#asyncio.loop.run_in_executor "asyncio.loop.run_in_executor") 默认执行器。  
  
任务与期程

[`loop.create_future()`](asyncio-eventloop.md#asyncio.loop.create_future "asyncio.loop.create_future")

|

创建一个 [`Future`](asyncio-future.md#asyncio.Future "asyncio.Future") 对象。  
  
---|---  
  
[`loop.create_task()`](asyncio-eventloop.md#asyncio.loop.create_task "asyncio.loop.create_task")

|

将协程当作 [`Task`](asyncio-task.md#asyncio.Task "asyncio.Task") 一样调度。  
  
[`loop.set_task_factory()`](asyncio-eventloop.md#asyncio.loop.set_task_factory "asyncio.loop.set_task_factory")

|

设置 [`loop.create_task()`](asyncio-eventloop.md#asyncio.loop.create_task "asyncio.loop.create_task") 使用的工厂，它将用来创建 [`Tasks`](asyncio-task.md#asyncio.Task "asyncio.Task") 。  
  
[`loop.get_task_factory()`](asyncio-eventloop.md#asyncio.loop.get_task_factory "asyncio.loop.get_task_factory")

|

获取 [`loop.create_task()`](asyncio-eventloop.md#asyncio.loop.create_task "asyncio.loop.create_task") 使用的工厂，它用来创建 [`Tasks`](asyncio-task.md#asyncio.Task "asyncio.Task") 。  
  
DNS

`await` [`loop.getaddrinfo()`](asyncio-eventloop.md#asyncio.loop.getaddrinfo "asyncio.loop.getaddrinfo")

|

异步版的 [`socket.getaddrinfo()`](socket.md#socket.getaddrinfo "socket.getaddrinfo") 。  
  
---|---  
  
`await` [`loop.getnameinfo()`](asyncio-eventloop.md#asyncio.loop.getnameinfo "asyncio.loop.getnameinfo")

|

异步版的 [`socket.getnameinfo()`](socket.md#socket.getnameinfo "socket.getnameinfo") 。  
  
网络和IPC

`await` [`loop.create_connection()`](asyncio-eventloop.md#asyncio.loop.create_connection "asyncio.loop.create_connection")

|

打开一个TCP链接。  
  
---|---  
  
`await` [`loop.create_server()`](asyncio-eventloop.md#asyncio.loop.create_server "asyncio.loop.create_server")

|

创建一个TCP服务。  
  
`await` [`loop.create_unix_connection()`](asyncio-eventloop.md#asyncio.loop.create_unix_connection "asyncio.loop.create_unix_connection")

|

打开一个Unix socket连接。  
  
`await` [`loop.create_unix_server()`](asyncio-eventloop.md#asyncio.loop.create_unix_server "asyncio.loop.create_unix_server")

|

创建一个Unix socket服务。  
  
`await` [`loop.connect_accepted_socket()`](asyncio-eventloop.md#asyncio.loop.connect_accepted_socket "asyncio.loop.connect_accepted_socket")

|

将 [`socket`](socket.md#socket.socket "socket.socket") 包装成 `(transport, protocol)` 对。  
  
`await` [`loop.create_datagram_endpoint()`](asyncio-eventloop.md#asyncio.loop.create_datagram_endpoint "asyncio.loop.create_datagram_endpoint")

|

打开一个数据报(UDP)连接。  
  
`await` [`loop.sendfile()`](asyncio-eventloop.md#asyncio.loop.sendfile "asyncio.loop.sendfile")

|

通过传输通道发送一个文件。  
  
`await` [`loop.start_tls()`](asyncio-eventloop.md#asyncio.loop.start_tls "asyncio.loop.start_tls")

|

将一个已建立的链接升级到TLS。  
  
`await` [`loop.connect_read_pipe()`](asyncio-eventloop.md#asyncio.loop.connect_read_pipe "asyncio.loop.connect_read_pipe")

|

将管道读取端包装成 `(transport, protocol)` 对。  
  
`await` [`loop.connect_write_pipe()`](asyncio-eventloop.md#asyncio.loop.connect_write_pipe "asyncio.loop.connect_write_pipe")

|

将管道写入端包装成 `(transport, protocol)` 对。  
  
套接字

`await` [`loop.sock_recv()`](asyncio-eventloop.md#asyncio.loop.sock_recv "asyncio.loop.sock_recv")

|

从 [`socket`](socket.md#socket.socket "socket.socket") 接收数据。  
  
---|---  
  
`await` [`loop.sock_recv_into()`](asyncio-eventloop.md#asyncio.loop.sock_recv_into "asyncio.loop.sock_recv_into")

|

从 [`socket`](socket.md#socket.socket "socket.socket") 接收数据到一个缓冲区中。  
  
`await` [`loop.sock_recvfrom()`](asyncio-eventloop.md#asyncio.loop.sock_recvfrom "asyncio.loop.sock_recvfrom")

|

从 [`socket`](socket.md#socket.socket "socket.socket") 接收数据报。  
  
`await` [`loop.sock_recvfrom_into()`](asyncio-eventloop.md#asyncio.loop.sock_recvfrom_into "asyncio.loop.sock_recvfrom_into")

|

从 [`socket`](socket.md#socket.socket "socket.socket") 接收数据报并放入缓冲区。  
  
`await` [`loop.sock_sendall()`](asyncio-eventloop.md#asyncio.loop.sock_sendall "asyncio.loop.sock_sendall")

|

发送数据到 [`socket`](socket.md#socket.socket "socket.socket") 。  
  
`await` [`loop.sock_sendto()`](asyncio-eventloop.md#asyncio.loop.sock_sendto "asyncio.loop.sock_sendto")

|

通过 [`socket`](socket.md#socket.socket "socket.socket") 向给定的地址发送数据报。  
  
`await` [`loop.sock_connect()`](asyncio-eventloop.md#asyncio.loop.sock_connect "asyncio.loop.sock_connect")

|

链接 `await` [`loop.sock_connect()`](asyncio-eventloop.md#asyncio.loop.sock_connect "asyncio.loop.sock_connect") 。  
  
`await` [`loop.sock_accept()`](asyncio-eventloop.md#asyncio.loop.sock_accept "asyncio.loop.sock_accept")

|

接受一个 [`socket`](socket.md#socket.socket "socket.socket") 链接。  
  
`await` [`loop.sock_sendfile()`](asyncio-eventloop.md#asyncio.loop.sock_sendfile "asyncio.loop.sock_sendfile")

|

利用 [`socket`](socket.md#socket.socket "socket.socket") 发送一个文件。  
  
[`loop.add_reader()`](asyncio-eventloop.md#asyncio.loop.add_reader "asyncio.loop.add_reader")

|

开始对一个文件描述符的可读性的监视。  
  
[`loop.remove_reader()`](asyncio-eventloop.md#asyncio.loop.remove_reader "asyncio.loop.remove_reader")

|

停止对一个文件描述符的可读性的监视。  
  
[`loop.add_writer()`](asyncio-eventloop.md#asyncio.loop.add_writer "asyncio.loop.add_writer")

|

开始对一个文件描述符的可写性的监视。  
  
[`loop.remove_writer()`](asyncio-eventloop.md#asyncio.loop.remove_writer "asyncio.loop.remove_writer")

|

停止对一个文件描述符的可写性的监视。  
  
Unix信号

[`loop.add_signal_handler()`](asyncio-eventloop.md#asyncio.loop.add_signal_handler "asyncio.loop.add_signal_handler")

|

给 [`signal`](signal.md#module-signal "signal: Set handlers for asynchronous events.") 添加一个处理回调函数。  
  
---|---  
  
[`loop.remove_signal_handler()`](asyncio-eventloop.md#asyncio.loop.remove_signal_handler "asyncio.loop.remove_signal_handler")

|

删除 [`signal`](signal.md#module-signal "signal: Set handlers for asynchronous events.") 的处理回调函数。  
  
子进程集

[`loop.subprocess_exec()`](asyncio-eventloop.md#asyncio.loop.subprocess_exec "asyncio.loop.subprocess_exec")

|

衍生一个子进程  
  
---|---  
  
[`loop.subprocess_shell()`](asyncio-eventloop.md#asyncio.loop.subprocess_shell "asyncio.loop.subprocess_shell")

|

从终端命令衍生一个子进程。  
  
错误处理

[`loop.call_exception_handler()`](asyncio-eventloop.md#asyncio.loop.call_exception_handler "asyncio.loop.call_exception_handler")

|

调用异常处理器。  
  
---|---  
  
[`loop.set_exception_handler()`](asyncio-eventloop.md#asyncio.loop.set_exception_handler "asyncio.loop.set_exception_handler")

|

设置一个新的异常处理器。  
  
[`loop.get_exception_handler()`](asyncio-eventloop.md#asyncio.loop.get_exception_handler "asyncio.loop.get_exception_handler")

|

获取当前异常处理器。  
  
[`loop.default_exception_handler()`](asyncio-eventloop.md#asyncio.loop.default_exception_handler "asyncio.loop.default_exception_handler")

|

默认异常处理器实现。  
  
例子

  * [使用 asyncio.new_event_loop() 和 loop.run_forever()](asyncio-eventloop.md#asyncio-example-lowlevel-helloworld).

  * [使用 loop.call_later()](asyncio-eventloop.md#asyncio-example-call-later).

  * 使用 `loop.create_connection()` 实现 [echo客户端](asyncio-protocol.md#asyncio-example-tcp-echo-client-protocol).

  * 使用 `loop.create_connection()` 去 [链接socket](asyncio-protocol.md#asyncio-example-create-connection).

  * [使用add_reader()监听FD(文件描述符)的读取事件](asyncio-eventloop.md#asyncio-example-watch-fd).

  * [使用loop.add_signal_handler()](asyncio-eventloop.md#asyncio-example-unix-signals).

  * [使用loop.add_signal_handler()](asyncio-protocol.md#asyncio-example-subprocess-proto)。

## 传输¶

所有传输都实现以下方法:

[`transport.close()`](asyncio-protocol.md#asyncio.BaseTransport.close "asyncio.BaseTransport.close")

|

关闭传输。  
  
---|---  
  
[`transport.is_closing()`](asyncio-protocol.md#asyncio.BaseTransport.is_closing "asyncio.BaseTransport.is_closing")

|

返回 `True` ，如果传输正在关闭或已经关闭。  
  
[`transport.get_extra_info()`](asyncio-protocol.md#asyncio.BaseTransport.get_extra_info "asyncio.BaseTransport.get_extra_info")

|

请求传输的相关信息。  
  
[`transport.set_protocol()`](asyncio-protocol.md#asyncio.BaseTransport.set_protocol "asyncio.BaseTransport.set_protocol")

|

设置一个新协议。  
  
[`transport.get_protocol()`](asyncio-protocol.md#asyncio.BaseTransport.get_protocol "asyncio.BaseTransport.get_protocol")

|

返回当前协议。  
  
传输可以接收数据(TCP和Unix链接，管道等)。它通过 [`loop.create_connection()`](asyncio-eventloop.md#asyncio.loop.create_connection "asyncio.loop.create_connection"), [`loop.create_unix_connection()`](asyncio-eventloop.md#asyncio.loop.create_unix_connection "asyncio.loop.create_unix_connection"), [`loop.connect_read_pipe()`](asyncio-eventloop.md#asyncio.loop.connect_read_pipe "asyncio.loop.connect_read_pipe") 等方法返回。

读取传输

[`transport.is_reading()`](asyncio-protocol.md#asyncio.ReadTransport.is_reading "asyncio.ReadTransport.is_reading")

|

返回 `True` ，如果传输正在接收。  
  
---|---  
  
[`transport.pause_reading()`](asyncio-protocol.md#asyncio.ReadTransport.pause_reading "asyncio.ReadTransport.pause_reading")

|

暂停接收。  
  
[`transport.resume_reading()`](asyncio-protocol.md#asyncio.ReadTransport.resume_reading "asyncio.ReadTransport.resume_reading")

|

继续接收。  
  
传输可以发送数据(TCP和Unix链接，管道等)。它通过 [`loop.create_connection()`](asyncio-eventloop.md#asyncio.loop.create_connection "asyncio.loop.create_connection"), [`loop.create_unix_connection()`](asyncio-eventloop.md#asyncio.loop.create_unix_connection "asyncio.loop.create_unix_connection"), [`loop.connect_write_pipe()`](asyncio-eventloop.md#asyncio.loop.connect_write_pipe "asyncio.loop.connect_write_pipe") 等方法返回。

写入传输

[`transport.write()`](asyncio-protocol.md#asyncio.WriteTransport.write "asyncio.WriteTransport.write")

|

向传输写入数据。  
  
---|---  
  
[`transport.write()`](asyncio-protocol.md#asyncio.WriteTransport.write "asyncio.WriteTransport.write")

|

向传输写入缓冲。  
  
[`transport.can_write_eof()`](asyncio-protocol.md#asyncio.WriteTransport.can_write_eof "asyncio.WriteTransport.can_write_eof")

|

返回 [`True`](constants.md#True "True") ，如果传输支持发送 EOF。  
  
[`transport.write_eof()`](asyncio-protocol.md#asyncio.WriteTransport.write_eof "asyncio.WriteTransport.write_eof")

|

在冲洗已缓冲的数据后关闭传输和发送EOF。  
  
[`transport.abort()`](asyncio-protocol.md#asyncio.WriteTransport.abort "asyncio.WriteTransport.abort")

|

立即关闭传输。  
  
[`transport.get_write_buffer_size()`](asyncio-protocol.md#asyncio.WriteTransport.get_write_buffer_size "asyncio.WriteTransport.get_write_buffer_size")

|

返回当前输出缓冲区的大小。  
  
[`transport.get_write_buffer_limits()`](asyncio-protocol.md#asyncio.WriteTransport.get_write_buffer_limits "asyncio.WriteTransport.get_write_buffer_limits")

|

返回写入流控制的高位标记位和低位标记位。  
  
[`transport.set_write_buffer_limits()`](asyncio-protocol.md#asyncio.WriteTransport.set_write_buffer_limits "asyncio.WriteTransport.set_write_buffer_limits")

|

设置新的写入流控制的高位标记位和低位标记位。  
  
由 [`loop.create_datagram_endpoint()`](asyncio-eventloop.md#asyncio.loop.create_datagram_endpoint "asyncio.loop.create_datagram_endpoint") 返回的传输:

数据报传输

[`transport.sendto()`](asyncio-protocol.md#asyncio.DatagramTransport.sendto "asyncio.DatagramTransport.sendto")

|

发送数据到远程链接端。  
  
---|---  
  
[`transport.abort()`](asyncio-protocol.md#asyncio.DatagramTransport.abort "asyncio.DatagramTransport.abort")

|

立即关闭传输。  
  
基于子进程的底层抽象传输，它由 [`loop.subprocess_exec()`](asyncio-eventloop.md#asyncio.loop.subprocess_exec "asyncio.loop.subprocess_exec") 和 [`loop.subprocess_shell()`](asyncio-eventloop.md#asyncio.loop.subprocess_shell "asyncio.loop.subprocess_shell") 返回:

子进程传输

[`transport.get_pid()`](asyncio-protocol.md#asyncio.SubprocessTransport.get_pid "asyncio.SubprocessTransport.get_pid")

|

返回子进程的进程ID。  
  
---|---  
  
[`transport.get_pipe_transport()`](asyncio-protocol.md#asyncio.SubprocessTransport.get_pipe_transport "asyncio.SubprocessTransport.get_pipe_transport")

|

返回请求通信管道 ( _stdin_ , _stdout_ , 或 _stderr_ )的传输。  
  
[`transport.get_returncode()`](asyncio-protocol.md#asyncio.SubprocessTransport.get_returncode "asyncio.SubprocessTransport.get_returncode")

|

返回子进程的返回代号。  
  
[`transport.kill()`](asyncio-protocol.md#asyncio.SubprocessTransport.kill "asyncio.SubprocessTransport.kill")

|

杀死子进程。  
  
[`transport.send_signal()`](asyncio-protocol.md#asyncio.SubprocessTransport.send_signal "asyncio.SubprocessTransport.send_signal")

|

发送一个信号到子进程。  
  
[`transport.terminate()`](asyncio-protocol.md#asyncio.SubprocessTransport.terminate "asyncio.SubprocessTransport.terminate")

|

停止子进程。  
  
[`transport.close()`](asyncio-protocol.md#asyncio.SubprocessTransport.close "asyncio.SubprocessTransport.close")

|

杀死子进程并关闭所有管道。  
  
## 协议¶

协议类可以由下面 **回调方法** 实现：

`callback` [`connection_made()`](asyncio-protocol.md#asyncio.BaseProtocol.connection_made "asyncio.BaseProtocol.connection_made")

|

连接建立时被调用。  
  
---|---  
  
`callback` [`connection_lost()`](asyncio-protocol.md#asyncio.BaseProtocol.connection_lost "asyncio.BaseProtocol.connection_lost")

|

连接丢失或关闭时将被调用。  
  
`callback` [`pause_writing()`](asyncio-protocol.md#asyncio.BaseProtocol.pause_writing "asyncio.BaseProtocol.pause_writing")

|

传输的缓冲区超过高位标记位时被调用。  
  
`callback` [`resume_writing()`](asyncio-protocol.md#asyncio.BaseProtocol.resume_writing "asyncio.BaseProtocol.resume_writing")

|

传输的缓冲区传送到低位标记位时被调用。  
  
流协议 (TCP, Unix 套接字, 管道)

`callback` [`data_received()`](asyncio-protocol.md#asyncio.Protocol.data_received "asyncio.Protocol.data_received")

|

接收到数据时被调用。  
  
---|---  
  
`callback` [`eof_received()`](asyncio-protocol.md#asyncio.Protocol.eof_received "asyncio.Protocol.eof_received")

|

接收到EOF时被调用。  
  
缓冲流协议

`callback` [`get_buffer()`](asyncio-protocol.md#asyncio.BufferedProtocol.get_buffer "asyncio.BufferedProtocol.get_buffer")

|

调用后会分配新的接收缓冲区。  
  
---|---  
  
`callback` [`buffer_updated()`](asyncio-protocol.md#asyncio.BufferedProtocol.buffer_updated "asyncio.BufferedProtocol.buffer_updated")

|

用接收的数据更新缓冲区时被调用。  
  
`callback` [`eof_received()`](asyncio-protocol.md#asyncio.BufferedProtocol.eof_received "asyncio.BufferedProtocol.eof_received")

|

接收到EOF时被调用。  
  
数据报协议

`callback` [`datagram_received()`](asyncio-protocol.md#asyncio.DatagramProtocol.datagram_received "asyncio.DatagramProtocol.datagram_received")

|

接收到数据报时被调用。  
  
---|---  
  
`callback` [`error_received()`](asyncio-protocol.md#asyncio.DatagramProtocol.error_received "asyncio.DatagramProtocol.error_received")

|

前一个发送或接收操作引发 [`OSError`](3.标准库/exceptions.md#OSError "OSError") 时被调用。  
  
子进程协议

`callback` [`pipe_data_received()`](asyncio-protocol.md#asyncio.SubprocessProtocol.pipe_data_received "asyncio.SubprocessProtocol.pipe_data_received")

|

子进程向 _stdout_ 或 _stderr_ 管道写入数据时被调用。  
  
---|---  
  
`callback` [`pipe_connection_lost()`](asyncio-protocol.md#asyncio.SubprocessProtocol.pipe_connection_lost "asyncio.SubprocessProtocol.pipe_connection_lost")

|

与子进程通信的其中一个管道关闭时被调用。  
  
`callback` [`process_exited()`](asyncio-protocol.md#asyncio.SubprocessProtocol.process_exited "asyncio.SubprocessProtocol.process_exited")

|

子进程退出时被调用。  
  
## 事件循环策略¶

策略是改变 [`asyncio.get_event_loop()`](asyncio-eventloop.md#asyncio.get_event_loop "asyncio.get_event_loop") 这类函数行为的一个底层机制。更多细节可以查阅 [策略部分](asyncio-policy.md#asyncio-policies)。

访问策略

[`asyncio.get_event_loop_policy()`](asyncio-policy.md#asyncio.get_event_loop_policy "asyncio.get_event_loop_policy")

|

返回当前进程域的策略。  
  
---|---  
  
[`asyncio.set_event_loop_policy()`](asyncio-policy.md#asyncio.set_event_loop_policy "asyncio.set_event_loop_policy")

|

设置一个新的进程域策略。  
  
[`AbstractEventLoopPolicy`](asyncio-policy.md#asyncio.AbstractEventLoopPolicy "asyncio.AbstractEventLoopPolicy")

|

策略对象的基类。  
  
