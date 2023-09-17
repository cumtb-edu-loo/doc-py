# 平台支持¶

[`asyncio`](asyncio.md#module-asyncio "asyncio: Asynchronous I/O.") 模块被设计为可移植的,但由于平台的底层架构和功能，一些平台存在细微的差异和限制。

## 所有平台¶

  * [`loop.add_reader()`](asyncio-eventloop.md#asyncio.loop.add_reader "asyncio.loop.add_reader") 和 [`loop.add_writer()`](asyncio-eventloop.md#asyncio.loop.add_writer "asyncio.loop.add_writer") 不能用来监视文件I/O。

## Windows¶

**源代码:** [Lib/asyncio/proactor_events.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/proactor_events.py), [Lib/asyncio/windows_events.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/windows_events.py), [Lib/asyncio/windows_utils.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/windows_utils.py)

* * *

在 3.8 版本发生变更: 在 Windows 上，[`ProactorEventLoop`](asyncio-eventloop.md#asyncio.ProactorEventLoop "asyncio.ProactorEventLoop") 现在是默认的事件循环。

Windows上的所有事件循环都不支持以下方法:

  * 不支持 [`loop.create_unix_connection()`](asyncio-eventloop.md#asyncio.loop.create_unix_connection "asyncio.loop.create_unix_connection") 和 [`loop.create_unix_server()`](asyncio-eventloop.md#asyncio.loop.create_unix_server "asyncio.loop.create_unix_server")。 [`socket.AF_UNIX`](socket.md#socket.AF_UNIX "socket.AF_UNIX") 套接字族是 Unix 专属的。

  * 不支持 [`loop.add_signal_handler()`](asyncio-eventloop.md#asyncio.loop.add_signal_handler "asyncio.loop.add_signal_handler") 和 [`loop.remove_signal_handler()`](asyncio-eventloop.md#asyncio.loop.remove_signal_handler "asyncio.loop.remove_signal_handler") 。

[`SelectorEventLoop`](asyncio-eventloop.md#asyncio.SelectorEventLoop "asyncio.SelectorEventLoop") 有下列限制:

  * [`SelectSelector`](selectors.md#selectors.SelectSelector "selectors.SelectSelector") 只被用于等待套接字事件：它支持套接字且最多支持512个套接字。

  * [`loop.add_reader()`](asyncio-eventloop.md#asyncio.loop.add_reader "asyncio.loop.add_reader") 和 [`loop.add_writer()`](asyncio-eventloop.md#asyncio.loop.add_writer "asyncio.loop.add_writer") 只接受套接字处理回调函数(如管道、文件描述符等都不支持)。

  * 因为不支持管道，所以 [`loop.connect_read_pipe()`](asyncio-eventloop.md#asyncio.loop.connect_read_pipe "asyncio.loop.connect_read_pipe") 和 [`loop.connect_write_pipe()`](asyncio-eventloop.md#asyncio.loop.connect_write_pipe "asyncio.loop.connect_write_pipe") 方法没有实现。

  * 不支持 [Subprocesses](asyncio-subprocess.md#asyncio-subprocess) ，也就是 [`loop.subprocess_exec()`](asyncio-eventloop.md#asyncio.loop.subprocess_exec "asyncio.loop.subprocess_exec") 和 [`loop.subprocess_shell()`](asyncio-eventloop.md#asyncio.loop.subprocess_shell "asyncio.loop.subprocess_shell") 方法没有实现。

[`ProactorEventLoop`](asyncio-eventloop.md#asyncio.ProactorEventLoop "asyncio.ProactorEventLoop") 有下列限制:

  * 不支持 [`loop.add_reader()`](asyncio-eventloop.md#asyncio.loop.add_reader "asyncio.loop.add_reader") 和 [`loop.add_writer()`](asyncio-eventloop.md#asyncio.loop.add_writer "asyncio.loop.add_writer") 方法。

通常 Windows 上单调时钟的分辨率约为 15.6 毫秒。 最佳分辨率是 0.5 毫秒。 分辨率依赖于具体的硬件 ([HPET](https://en.wikipedia.org/wiki/High_Precision_Event_Timer) 的可用性) 和 Windows 的设置。

### Windows的子进程支持¶

在 Windows 上，默认的事件循环 [`ProactorEventLoop`](asyncio-eventloop.md#asyncio.ProactorEventLoop "asyncio.ProactorEventLoop") 支持子进程，而 [`SelectorEventLoop`](asyncio-eventloop.md#asyncio.SelectorEventLoop "asyncio.SelectorEventLoop") 则不支持。

也不支持 [`policy.set_child_watcher()`](asyncio-policy.md#asyncio.AbstractEventLoopPolicy.set_child_watcher "asyncio.AbstractEventLoopPolicy.set_child_watcher") 函数，[`ProactorEventLoop`](asyncio-eventloop.md#asyncio.ProactorEventLoop "asyncio.ProactorEventLoop") 有不同的机制来监视子进程。

## macOS¶

完整支持流行的macOS版本。

macOS <= 10.8

在 macOS 10.6, 10.7 和 10.8 上，默认的事件循环使用 [`selectors.KqueueSelector`](selectors.md#selectors.KqueueSelector "selectors.KqueueSelector")，在这些版本上它并不支持字符设备。 可以手工配置 [`SelectorEventLoop`](asyncio-eventloop.md#asyncio.SelectorEventLoop "asyncio.SelectorEventLoop") 来使用 [`SelectSelector`](selectors.md#selectors.SelectSelector "selectors.SelectSelector") 或 [`PollSelector`](selectors.md#selectors.PollSelector "selectors.PollSelector") 以在这些较老版本的 macOS 上支持字符设备。 例如:

    
    
~~~
import asyncio
import selectors

selector = selectors.SelectSelector()
loop = asyncio.SelectorEventLoop(selector)
asyncio.set_event_loop(loop)
~~~

