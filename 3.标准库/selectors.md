# `selectors` \--- 高级 I/O 复用库¶

在 3.4 版本加入.

**源码:** [Lib/selectors.py](https://github.com/python/cpython/tree/3.12/Lib/selectors.py)

* * *

## 概述¶

此模块允许高层级且高效率的 I/O 复用，它建立在 [`select`](select.md#module-select "select: Wait for I/O completion on multiple streams.") 模块原型的基础之上。 推荐用户改用此模块，除非他们希望对所使用的 OS 层级原型进行精确控制。

它定义了一个 `BaseSelector` 抽象基类，以及多个实际的实现 (`KqueueSelector`, `EpollSelector`...)，它们可被用于在多个文件对象上等待 I/O 就绪通知。 在下文中，"文件对象" 是指任何具有 `fileno()` 方法的对象，或是一个原始文件描述符。 参见 [file object](../glossary.md#term-file-object)。

`DefaultSelector` 是一个指向当前平台上可用的最高效实现的别名：这应为大多数用户的默认选择。

备注

受支持的文件对象类型取决于具体平台：在 Windows 上，支持套接字但不支持管道，而在 Unix 上两者均受支持（某些其他类型也可能受支持，例如 fifo 或特殊文件设备等）。

参见

[`select`](select.md#module-select "select: Wait for I/O completion on multiple streams.")

    

~~~
低层级的 I/O 多路复用模块。

[可用性](intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

## 类¶

类的层次结构:
~~~
    
    
~~~
BaseSelector
+-- SelectSelector
+-- PollSelector
+-- EpollSelector
+-- DevpollSelector
+-- KqueueSelector
~~~

下文中， _events_ 一个位掩码，指明哪些 I/O 事件要在给定的文件对象上执行等待。 它可以是以下模块级常量的组合:

> 常量
>
> |
>
> 含意  
>  
> ---|---  
>  
> selectors.EVENT_READ¶
>  
> |
>
> 可读  
>  
> selectors.EVENT_WRITE¶
>  
> |
>
> 可写  
  
_class _selectors.SelectorKey¶

    

~~~
`SelectorKey` 是一个 [`namedtuple`](collections.md#collections.namedtuple "collections.namedtuple")，用来将文件对象关联到其下层的文件描述符、选定事件掩码和附加数据等。 它会被某些 `BaseSelector` 方法返回。

fileobj¶
~~~
    

~~~
已注册的文件对象。

fd¶
~~~
    

~~~
下层的文件描述符。

events¶
~~~
    

~~~
必须在此文件对象上被等待的事件。

data¶
~~~
    

~~~
可选的关联到此文件对象的不透明数据：例如，这可被用来存储各个客户端的会话 ID。

_class _selectors.BaseSelector¶
~~~
    

~~~
一个 `BaseSelector`，用来在多个文件对象上等待 I/O 事件就绪。 它支持文件流注册、注销，以及在这些流上等待 I/O 事件的方法。 它是一个抽象基类，因此不能被实例化。 请改用 `DefaultSelector`，或者 `SelectSelector`, `KqueueSelector` 等。 如果你想要指明使用某个实现，并且你的平台支持它的话。 `BaseSelector` 及其具体实现支持 [context manager](../glossary.md#term-context-manager) 协议。

_abstractmethod _register( _fileobj_ , _events_ , _data =None_)¶
~~~
    

~~~
注册一个用于选择的文件对象，在其上监视 I/O 事件。

_fileobj_ 是要监视的文件对象。 它可以是整数形式的文件描述符或者具有 `fileno()` 方法的对象。 _events_ 是要监视的事件的位掩码。 _data_ 是一个不透明对象。

这将返回一个新的 `SelectorKey` 实例，或在出现无效事件掩码或文件描述符时引发 [`ValueError`](exceptions.md#ValueError "ValueError")，或在文件对象已被注册时引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

_abstractmethod _unregister( _fileobj_ )¶
~~~
    

~~~
注销对一个文件对象的选择，移除对它的监视。 在文件对象被关闭之前应当先将其注销。

_fileobj_ 必须是之前已注册的文件对象。

这将返回已关联的 `SelectorKey` 实例，或者如果 _fileobj_ 未注册则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。 It will raise [`ValueError`](exceptions.md#ValueError "ValueError") 如果 _fileobj_ 无效（例如它没有 `fileno()` 方法或其 `fileno()` 方法返回无效值）。

modify( _fileobj_ , _events_ , _data =None_)¶
~~~
    

~~~
更改已注册文件对象所监视的事件或所附带的数据。

这等价于 `BaseSelector.unregister(fileobj)` 加 `BaseSelector.register(fileobj, events, data)`，区别在于它可以被更高效地实现。

这将返回一个新的 `SelectorKey` 实例，或在出现无效事件掩码或文件描述符时引发 [`ValueError`](exceptions.md#ValueError "ValueError")，或在文件对象未被注册时引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

_abstractmethod _select( _timeout =None_)¶
~~~
    

~~~
等待直到有已注册的文件对象就绪，或是超过时限。

如果 `timeout > 0`，这指定以秒数表示的最大等待时间。 如果 `timeout <= 0`，调用将不会阻塞，并将报告当前就绪的文件对象。 如果 _timeout_ 为 `None`，调用将阻塞直到某个被监视的文件对象就绪。

这将返回由 `(key, events)` 元组构成的列表，每项各表示一个就绪的文件对象。

_key_ 是对应于就绪文件对象的 `SelectorKey` 实例。 _events_ 是在此文件对象上等待的事件位掩码。

备注

如果当前进程收到一个信号，此方法可在任何文件对象就绪之前或超出时限时返回：在此情况下，将返回一个空列表。

在 3.5 版本发生变更: 现在当被某个信号中断时，如果信号处理程序没有引发异常，选择器会用重新计算的超时值进行重试（请查看 [**PEP 475**](https://peps.python.org/pep-0475/) 其理由），而不是在超时之前返回空的事件列表。

close()¶
~~~
    

~~~
关闭选择器。

必须调用这个方法以确保下层资源会被释放。 选择器被关闭后将不可再使用。

get_key( _fileobj_ )¶
~~~
    

~~~
返回关联到某个已注册文件对象的键。

此方法将返回关联到文件对象的 `SelectorKey` 实例，或在文件对象未注册时引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

_abstractmethod _get_map()¶
~~~
    

~~~
返回从文件对象到选择器键的映射。

这将返回一个将已注册文件对象映射到与其相关联的 `SelectorKey` 实例的 [`Mapping`](collections.abc.md#collections.abc.Mapping "collections.abc.Mapping") 实例。

_class _selectors.DefaultSelector¶
~~~
    

~~~
默认的选择器类，使用当前平台上可用的最高效实现。 这应为大多数用户的默认选择。

_class _selectors.SelectSelector¶
~~~
    

~~~
基于 [`select.select()`](select.md#select.select "select.select") 的选择器。

_class _selectors.PollSelector¶
~~~
    

~~~
基于 [`select.poll()`](select.md#select.poll "select.poll") 的选择器。

_class _selectors.EpollSelector¶
~~~
    

~~~
基于 [`select.epoll()`](select.md#select.epoll "select.epoll") 的选择器。

fileno()¶
~~~
    

~~~
此方法将返回由下层 [`select.epoll()`](select.md#select.epoll "select.epoll") 对象所使用的文件描述符。

_class _selectors.DevpollSelector¶
~~~
    

~~~
基于 [`select.devpoll()`](select.md#select.devpoll "select.devpoll") 的选择器。

fileno()¶
~~~
    

~~~
此方法将返回由下层 [`select.devpoll()`](select.md#select.devpoll "select.devpoll") 对象所使用的文件描述符。

在 3.5 版本加入.

_class _selectors.KqueueSelector¶
~~~
    

~~~
基于 [`select.kqueue()`](select.md#select.kqueue "select.kqueue") 的选择器。

fileno()¶
~~~
    

~~~
此方法将返回由下层 [`select.kqueue()`](select.md#select.kqueue "select.kqueue") 对象所使用的文件描述符。

## 例子¶

下面是一个简单的回显服务器实现:
~~~
    
    
~~~
import selectors
import socket

sel = selectors.DefaultSelector()

def accept(sock, mask):
    conn, addr = sock.accept()  # Should be ready
    print('accepted', conn, 'from', addr)
    conn.setblocking(False)
    sel.register(conn, selectors.EVENT_READ, read)

def read(conn, mask):
    data = conn.recv(1000)  # Should be ready
    if data:
        print('echoing', repr(data), 'to', conn)
        conn.send(data)  # Hope it won't block
    else:
        print('closing', conn)
        sel.unregister(conn)
        conn.close()

sock = socket.socket()
sock.bind(('localhost', 1234))
sock.listen(100)
sock.setblocking(False)
sel.register(sock, selectors.EVENT_READ, accept)

while True:
    events = sel.select()
    for key, mask in events:
        callback = key.data
        callback(key.fileobj, mask)
~~~

