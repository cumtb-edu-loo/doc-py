# `socketserver` \--- 用于网络服务器的框架¶

**源代码:** [Lib/socketserver.py](https://github.com/python/cpython/tree/3.12/Lib/socketserver.py)

* * *

`socketserver` 模块简化了编写网络服务器的任务。

[可用性](intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

该模块具有四个基础实体服务器类:

_class _socketserver.TCPServer( _server_address_ , _RequestHandlerClass_ , _bind_and_activate =True_)¶

    

~~~
该类使用互联网 TCP 协议，它可以提供客户端与服务器之间的连续数据流。 如果 _bind_and_activate_ 为真值，该类的构造器会自动尝试发起调用 `server_bind()` 和 `server_activate()`。 其他形参会被传递给 `BaseServer` 基类。

_class _socketserver.UDPServer( _server_address_ , _RequestHandlerClass_ , _bind_and_activate =True_)¶
~~~
    

~~~
该类使用数据包，即一系列离散的信息分包，它们可能会无序地到达或在传输中丢失。 该类的形参与 `TCPServer` 的相同。

_class _socketserver.UnixStreamServer( _server_address_ , _RequestHandlerClass_ , _bind_and_activate =True_)¶

_class _socketserver.UnixDatagramServer( _server_address_ , _RequestHandlerClass_ , _bind_and_activate =True_)¶
~~~
    

~~~
这两个更常用的类与 TCP 和 UDP 类相似，但使用 Unix 域套接字；它们在非 Unix 系统平台上不可用。 它们的形参与 `TCPServer` 的相同。

这四个类会 _同步地_ 处理请求；每个请求必须完成才能开始下一个请求。 这就不适用于每个请求要耗费很长时间来完成的情况，或者因为它需要大量的计算，又或者它返回了大量的数据而客户端处理起来很缓慢。 解决方案是创建单独的进程或线程来处理每个请求；`ForkingMixIn` 和 `ThreadingMixIn` 混合类可以被用于支持异步行为。

创建一个服务器需要分几个步骤进行。 首先，你必须通过子类化 `BaseRequestHandler` 类并重载其 `handle()` 方法来创建一个请求处理句柄类；这个方法将处理传入的请求。 其次，你必须实例化某个服务器类，将服务器地址和请求处理句柄类传给它。 建议在 [`with`](../reference/compound_stmts.md#with) 语句中使用该服务器。 然后再调用服务器对象的 `handle_request()` 或 `serve_forever()` 方法来处理一个或多个请求。 最后，调用 `server_close()` 来关闭套接字（除非你使用了 `with` 语句）。

当从 `ThreadingMixIn` 继承线程连接行为时，你应当显式地声明你希望在突然关机时你的线程采取何种行为。 `ThreadingMixIn` 类定义了一个属性 _daemon_threads_ ，它指明服务器是否应当等待线程终止。 如果你希望线程能自主行动你应当显式地设置这个旗标；默认值为 [`False`](constants.md#False "False")，表示 Python 将不会在 `ThreadingMixIn` 所创建的所有线程都退出之前退出。

服务器类具有同样的外部方法和属性，无论它们使用哪种网络协议。

## 服务器创建的说明¶

在继承图中有五个类，其中四个代表四种类型的同步服务器:
~~~
    
    
~~~
+------------+
| BaseServer |
+------------+
      |
      v
+-----------+        +------------------+
| TCPServer |------->| UnixStreamServer |
+-----------+        +------------------+
      |
      v
+-----------+        +--------------------+
| UDPServer |------->| UnixDatagramServer |
+-----------+        +--------------------+
~~~

请注意 `UnixDatagramServer` 是派生自 `UDPServer`，而不是派生自 `UnixStreamServer` \--- IP 和 Unix 流服务器的唯一区别地址族。

_class _socketserver.ForkingMixIn¶

_class _socketserver.ThreadingMixIn¶

    

~~~
每种服务器类型的分叉和线程版本都可以使用这些混合类来创建。 例如，`ThreadingUDPServer` 的创建方式如下:
~~~
    
    
~~~
class ThreadingUDPServer(ThreadingMixIn, UDPServer):
    pass
~~~

混合类先出现，因为它重载了 `UDPServer` 中定义的一个方法。 设置各种属性也会改变下层服务器机制的行为。

`ForkingMixIn` 和下文提及的分叉类仅在支持 [`fork()`](os.md#os.fork "os.fork") 的 POSIX 系统平台上可用。

`socketserver.ForkingMixIn.server_close()` 会等待直到所有子进程完成，除非 `socketserver.ForkingMixIn.block_on_close` 属性为假值。

`socketserver.ThreadingMixIn.server_close()` 会等待直到所有非守护类线程完成，除非 `socketserver.ThreadingMixIn.block_on_close` 属性为假值。 请将 `ThreadingMixIn.daemon_threads` 设为 `True` 来使用守护类线程以便不等待线完成。

在 3.7 版本发生变更: `socketserver.ForkingMixIn.server_close()` 和 `socketserver.ThreadingMixIn.server_close()` 现在会等待直到所有子进程和非守护类线程完成。 请新增一个 `socketserver.ForkingMixIn.block_on_close` 类属性来选择 3.7 版之前的行为。

_class _socketserver.ForkingTCPServer¶

_class _socketserver.ForkingUDPServer¶

_class _socketserver.ThreadingTCPServer¶

_class _socketserver.ThreadingUDPServer¶

_class _socketserver.ForkingUnixStreamServer¶

_class _socketserver.ForkingUnixDatagramServer¶

_class _socketserver.ThreadingUnixStreamServer¶

_class _socketserver.ThreadingUnixDatagramServer¶

    

~~~
这些类都是使用混合类来预定义的。

在 3.12 版本加入: 增加了 `ForkingUnixStreamServer` 和 `ForkingUnixDatagramServer` 类。

要实现一个服务，你必须从 `BaseRequestHandler` 派生一个类并重定义其 `handle()` 方法。 然后你可以通过组合某种服务器类型与你的请求处理句柄类来运行各种版本的服务。 请求处理句柄类对于数据报和流服务必须是不相同的。 这可以通过使用处理句柄子类 `StreamRequestHandler` 或 `DatagramRequestHandler` 来隐藏。

当然，你仍然需要动点脑筋！ 举例来说，如果服务包含可能被不同请求所修改的内存状态则使用分叉服务器是没有意义的，因为在子进程中的修改将永远不会触及保存在父进程中的初始状态并传递到各个子进程。 在这种情况下，你可以使用线程服务器，但你可能必须使用锁来保护共享数据的一致性。

另一方面，如果你是在编写一个所有数据保存在外部（例如文件系统）的 HTTP 服务器，同步类实际上将在正在处理某个请求的时候“失聪” -- 如果某个客户端在接收它所请求的所有数据时很缓慢这可能会是非常长的时间。 这时线程或分叉服务器会更为适用。

在某些情况下，合适的做法是同步地处理请求的一部分，但根据请求数据在分叉的子进程中完成处理。 这可以通过使用一个同步服务器并在请求处理句柄类 `handle()` 中进行显式分叉来实现。

另一种可以在既不支持线程也不支持 [`fork()`](os.md#os.fork "os.fork") 的环境（或者对于本服务来说这两者开销过大或不适用）中处理多个同时请求的方式是维护一个显式的部分完成的请求表并使用 [`selectors`](selectors.md#module-selectors "selectors: High-level I/O multiplexing.") 来决定接下来要处理哪个请求（或者是否要处理一个新传入的请求）。 这对于流式服务来说特别重要，因为每个客户端可能会连接很长的时间（如果不能使用线程或子进程）。

## Server 对象¶

_class _socketserver.BaseServer( _server_address_ , _RequestHandlerClass_ )¶
~~~
    

~~~
这是本模块中所有 Server 对象的超类。 它定义了下文给出的接口，但没有实现大部分的方法，它们应在子类中实现。 两个形参存储在对应的 `server_address` 和 `RequestHandlerClass` 属性中。

fileno()¶
~~~
    

~~~
返回服务器正在监听的套接字的以整数表示的文件描述符。 此函数最常被传递给 [`selectors`](selectors.md#module-selectors "selectors: High-level I/O multiplexing.")，以允许在同一进程中监控多个服务器。

handle_request()¶
~~~
    

~~~
处理单个请求。 此函数会依次调用下列方法: `get_request()`, `verify_request()` 和 `process_request()`。 如果用户提供的处理句柄类的 `handle()` 方法引发了异常，则将调用服务器的 `handle_error()` 方法。 如果在 `timeout` 秒内未接收到请求，将会调用 `handle_timeout()` 并将返回 `handle_request()`。

serve_forever( _poll_interval =0.5_)¶
~~~
    

~~~
对请求进行处理直至收到显式的 `shutdown()` 请求。 每隔 _poll_interval_ 秒对 shutdown 进行轮询。 忽略 `timeout` 属性。 它还会调用 `service_actions()`，这可被子类或混合类用来提供某个给定服务的专属操作。 例如，`ForkingMixIn` 类使用 `service_actions()` 来清理僵尸子进程。

在 3.3 版本发生变更: 将 `service_actions` 调用添加到 `serve_forever` 方法。

service_actions()¶
~~~
    

~~~
此方法会在 the `serve_forever()` 循环中被调用。 此方法可被子类或混合类所重载以执行某个给定服务的专属操作，例如清理操作。

在 3.3 版本加入.

shutdown()¶
~~~
    

~~~
通知 `serve_forever()` 循环停止并等待它完成。 `shutdown()` 必须在 `serve_forever()` 运行于不同线程时被调用否则它将发生死锁。

server_close()¶
~~~
    

~~~
清理服务器。 此方法可被重载。

address_family¶
~~~
    

~~~
服务器套接字所属的协议族。 常见的例子有 [`socket.AF_INET`](socket.md#socket.AF_INET "socket.AF_INET") 和 [`socket.AF_UNIX`](socket.md#socket.AF_UNIX "socket.AF_UNIX")。

RequestHandlerClass¶
~~~
    

~~~
用户提供的请求处理句柄类；将为每个请求创建该类的实例。

server_address¶
~~~
    

~~~
服务器所监听的地址。 地址的格式因具体协议族而不同；请参阅 [`socket`](socket.md#module-socket "socket: Low-level networking interface.") 模块的文档了解详情。 对于互联网协议，这将是一个元组，其中包含一个表示地址的字符串，和一个表示端口号的整数，例如: `('127.0.0.1', 80)`。

socket¶
~~~
    

~~~
将由服务器用于监听入站请求的套接字对象。

服务器类支持下列类变量:

allow_reuse_address¶
~~~
    

~~~
服务器是否要允许地址的重用。 默认值为 [`False`](constants.md#False "False")，并可在子类中设置以改变策略。

request_queue_size¶
~~~
    

~~~
请求队列的长度。 如果处理单个请求要花费很长的时间，则当服务器正忙时到达的任何请求都会被加入队列，最多加入 `request_queue_size` 个请求。 一旦队列被加满，来自客户端的更多请求将收到 "Connection denied" 错误。 默认值为 5，但可在子类中重载。

socket_type¶
~~~
    

~~~
服务器使用的套接字类型；常见的有 [`socket.SOCK_STREAM`](socket.md#socket.SOCK_STREAM "socket.SOCK_STREAM") 和 [`socket.SOCK_DGRAM`](socket.md#socket.SOCK_DGRAM "socket.SOCK_DGRAM") 这两个值。

timeout¶
~~~
    

~~~
超时限制，以秒数表示，或者如果不限制超时则为 [`None`](constants.md#None "None")。 如果在超时限制期间没有收到 `handle_request()`，则会调用 `handle_timeout()` 方法。

有多个服务器方法可被服务器基类的子类例如 `TCPServer` 所重载；这些方法对服务器对象的外部用户来说并无用处。

finish_request( _request_ , _client_address_ )¶
~~~
    

~~~
通过实例化 `RequestHandlerClass` 并调用其 `handle()` 方法来实际处理请求。

get_request()¶
~~~
    

~~~
必须接受来自套接字的请求，并返回一个 2 元组，其中包含用来与客户端通信的 _new_ 套接字对象，以及客户端的地址。

handle_error( _request_ , _client_address_ )¶
~~~
    

~~~
此函数会在 `RequestHandlerClass` 实例的 `handle()` 方法引发异常时被调用。 默认行为是将回溯信息打印到标准错误并继续处理其他请求。

在 3.6 版本发生变更: 现在只针对派生自 [`Exception`](exceptions.md#Exception "Exception") 类的异常调用此方法。

handle_timeout()¶
~~~
    

~~~
此函数会在 `timeout` 属性被设为 [`None`](constants.md#None "None") 以外的值并且在超出时限之后仍未收到请求时被调用。 分叉服务器的默认行为是收集任何已退出的子进程状态，而在线程服务器中此方法则不做任何操作。

process_request( _request_ , _client_address_ )¶
~~~
    

~~~
调用 `finish_request()` 来创建 `RequestHandlerClass` 的实例。 如果需要，此函数可创建一个新的进程或线程来处理请求；`ForkingMixIn` 和 `ThreadingMixIn` 类能完成此任务。

server_activate()¶
~~~
    

~~~
由服务器的构造器调用以激活服务器。 TCP 服务器的默认行为只是在服务器的套接字上发起调用 [`listen()`](socket.md#socket.socket.listen "socket.socket.listen")。 可以被重载。

server_bind()¶
~~~
    

~~~
由服务器的构造器调用以将套接字绑定到所需的地址。 可以被重载。

verify_request( _request_ , _client_address_ )¶
~~~
    

~~~
必须返回一个布尔值；如果值为 [`True`](constants.md#True "True")，请求将被处理。 而如果值为 [`False`](constants.md#False "False")，请求将被拒绝。 此函数可被重载以实现服务器的访问控制。 默认实现总是返回 [`True`](constants.md#True "True")。

在 3.6 版本发生变更: 添加了对 [context manager](../glossary.md#term-context-manager) 协议的支持。 退出上下文管理器与调用 `server_close()` 等效。

## 请求处理句柄对象¶

_class _socketserver.BaseRequestHandler¶
~~~
    

~~~
这是所有请求处理句柄对象的超类。 它定义了下文列出的接口。 一个实体请求处理句柄子类必须定义新的 `handle()` 方法，并可重载任何其他方法。 对于每个请求都会创建一个新的子类的实例。

setup()¶
~~~
    

~~~
会在 `handle()` 方法之前被调用以执行任何必要的初始化操作。 默认实现不执行任何操作。

handle()¶
~~~
    

~~~
此函数必须执行为请求提供服务所需的全部操作。 默认实现不执行任何操作。 它有几个可用的实例属性；请求为 `self.request`；客户端地址为 `self.client_address`；服务器实例为 `self.server`，如果它需要访问特定服务器信息的话。

针对数据报或流服务的 `self.request` 类型是不同的。 对于流服务，`self.request` 是一个套接字对象；对于数据报服务，`self.request` 是一对字符串与套接字。

finish()¶
~~~
    

~~~
在 `handle()` 方法之后调用以执行任何需要的清理操作。 默认实现不执行任何操作。 如果 `setup()` 引发了异常，此函数将不会被调用。

_class _socketserver.StreamRequestHandler¶

_class _socketserver.DatagramRequestHandler¶
~~~
    

~~~
这些 `BaseRequestHandler` 子类重载了 `setup()` 和 `finish()` 方法，并提供了 `self.rfile` 和 `self.wfile` 属性。 `self.rfile` 和 `self.wfile` 属性可以被分别读取或写入，以便获取请求数据或将数据返回给客户端。 `rfile` 属性支持 [`io.BufferedIOBase`](io.md#io.BufferedIOBase "io.BufferedIOBase") 可读接口，而 `wfile` 属性支持 `io.BufferedIOBase` 可写接口。

在 3.6 版本发生变更: `StreamRequestHandler.wfile` 也支持 [`io.BufferedIOBase`](io.md#io.BufferedIOBase "io.BufferedIOBase") 可写接口。

## 例子¶

### `socketserver.TCPServer` 示例¶

以下是服务端:
~~~
    
    
~~~
import socketserver

class MyTCPHandler(socketserver.BaseRequestHandler):
    """
    The request handler class for our server.

    It is instantiated once per connection to the server, and must
    override the handle() method to implement communication to the
    client.
    """

    def handle(self):
        # self.request is the TCP socket connected to the client
        self.data = self.request.recv(1024).strip()
        print("{} wrote:".format(self.client_address[0]))
        print(self.data)
        # just send back the same data, but upper-cased
        self.request.sendall(self.data.upper())

if __name__ == "__main__":
    HOST, PORT = "localhost", 9999

    # Create the server, binding to localhost on port 9999
    with socketserver.TCPServer((HOST, PORT), MyTCPHandler) as server:
        # Activate the server; this will keep running until you
        # interrupt the program with Ctrl-C
        server.serve_forever()
~~~

一个使用流（通过提供标准文件接口来简化通信的文件类对象）的替代请求处理句柄类:

    
    
~~~
class MyTCPHandler(socketserver.StreamRequestHandler):

    def handle(self):
        # self.rfile is a file-like object created by the handler;
        # we can now use e.g. readline() instead of raw recv() calls
        self.data = self.rfile.readline().strip()
        print("{} wrote:".format(self.client_address[0]))
        print(self.data)
        # Likewise, self.wfile is a file-like object used to write back
        # to the client
        self.wfile.write(self.data.upper())
~~~

区别在于第二个处理句柄的 `readline()` 调用将多次调用 `recv()` 直至遇到一个换行符，而第一个处理句柄的单次 `recv()` 调用只是返回在一次 `sendall()` 调用中由客户端发送的内容。

以下是客户端:

    
    
~~~
import socket
import sys

HOST, PORT = "localhost", 9999
data = " ".join(sys.argv[1:])

# Create a socket (SOCK_STREAM means a TCP socket)
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as sock:
    # Connect to server and send data
    sock.connect((HOST, PORT))
    sock.sendall(bytes(data + "\n", "utf-8"))

    # Receive data from the server and shut down
    received = str(sock.recv(1024), "utf-8")

print("Sent:     {}".format(data))
print("Received: {}".format(received))
~~~

这个示例程序的输出应该是像这样的:

服务器:

    
    
~~~
$ python TCPServer.py
127.0.0.1 wrote:
b'hello world with TCP'
127.0.0.1 wrote:
b'python is nice'
~~~

客户端:

    
    
~~~
$ python TCPClient.py hello world with TCP
Sent:     hello world with TCP
Received: HELLO WORLD WITH TCP
$ python TCPClient.py python is nice
Sent:     python is nice
Received: PYTHON IS NICE
~~~

### `socketserver.UDPServer` 示例¶

以下是服务端:

    
    
~~~
import socketserver

class MyUDPHandler(socketserver.BaseRequestHandler):
    """
    This class works similar to the TCP handler class, except that
    self.request consists of a pair of data and client socket, and since
    there is no connection the client address must be given explicitly
    when sending data back via sendto().
    """

    def handle(self):
        data = self.request[0].strip()
        socket = self.request[1]
        print("{} wrote:".format(self.client_address[0]))
        print(data)
        socket.sendto(data.upper(), self.client_address)

if __name__ == "__main__":
    HOST, PORT = "localhost", 9999
    with socketserver.UDPServer((HOST, PORT), MyUDPHandler) as server:
        server.serve_forever()
~~~

以下是客户端:

    
    
~~~
import socket
import sys

HOST, PORT = "localhost", 9999
data = " ".join(sys.argv[1:])

# SOCK_DGRAM is the socket type to use for UDP sockets
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# As you can see, there is no connect() call; UDP has no connections.
# Instead, data is directly sent to the recipient via sendto().
sock.sendto(bytes(data + "\n", "utf-8"), (HOST, PORT))
received = str(sock.recv(1024), "utf-8")

print("Sent:     {}".format(data))
print("Received: {}".format(received))
~~~

这个示例程序的输出应该是与 TCP 服务器示例相一致的。

### 异步混合类¶

要构建异步处理句柄，请使用 `ThreadingMixIn` 和 `ForkingMixIn` 类。

`ThreadingMixIn` 类的示例:

    
    
~~~
import socket
import threading
import socketserver

class ThreadedTCPRequestHandler(socketserver.BaseRequestHandler):

    def handle(self):
        data = str(self.request.recv(1024), 'ascii')
        cur_thread = threading.current_thread()
        response = bytes("{}: {}".format(cur_thread.name, data), 'ascii')
        self.request.sendall(response)

class ThreadedTCPServer(socketserver.ThreadingMixIn, socketserver.TCPServer):
    pass

def client(ip, port, message):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as sock:
        sock.connect((ip, port))
        sock.sendall(bytes(message, 'ascii'))
        response = str(sock.recv(1024), 'ascii')
        print("Received: {}".format(response))

if __name__ == "__main__":
    # Port 0 means to select an arbitrary unused port
    HOST, PORT = "localhost", 0

    server = ThreadedTCPServer((HOST, PORT), ThreadedTCPRequestHandler)
    with server:
        ip, port = server.server_address

        # Start a thread with the server -- that thread will then start one
        # more thread for each request
        server_thread = threading.Thread(target=server.serve_forever)
        # Exit the server thread when the main thread terminates
        server_thread.daemon = True
        server_thread.start()
        print("Server loop running in thread:", server_thread.name)

        client(ip, port, "Hello World 1")
        client(ip, port, "Hello World 2")
        client(ip, port, "Hello World 3")

        server.shutdown()
~~~

这个示例程序的输出应该是像这样的:

    
    
~~~
$ python ThreadedTCPServer.py
Server loop running in thread: Thread-1
Received: Thread-2: Hello World 1
Received: Thread-3: Hello World 2
Received: Thread-4: Hello World 3
~~~

`ForkingMixIn` 类的使用方式是相同的，区别在于服务器将为每个请求产生一个新的进程。 仅在支持 [`fork()`](os.md#os.fork "os.fork") 的 POSIX 系统平台上可用。

