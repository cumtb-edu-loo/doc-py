# 扩展¶

[`asyncio`](asyncio.md#module-asyncio "asyncio: Asynchronous I/O.") 扩展的主要方向是编写自定义的 _事件循环_ 类。 asyncio 具有可以被用来简化此任务的辅助工具。

备注

第三方应当小心谨慎地重用现有的异步代码，新的 Python 版本可以自由地打破 API 的 _内部_ 部分的向下兼容性。

## 编写自定义事件循环¶

[`asyncio.AbstractEventLoop`](asyncio-eventloop.md#asyncio.AbstractEventLoop "asyncio.AbstractEventLoop") 声明了大量的方法。 从头开始全部实现它们将是一件烦琐的工作。

一个事件循环可以通过从 `asyncio.BaseEventLoop` 继承来自动地获得许多常用方法的实现。

相应地，继承者应当实现多个在 `asyncio.BaseEventLoop` 中已声明但未实现的 _私有_ 方法。

例如，`loop.create_connection()` 会检查参数，解析 DNS 地址，并调用应当由继承方类来实现的 `loop._make_socket_transport()`。 `_make_socket_transport()` 方法未被写入文档并被视为 _内部_ API。

## Future 和 Task 私有构造器¶

[`asyncio.Future`](asyncio-future.md#asyncio.Future "asyncio.Future") 和 [`asyncio.Task`](asyncio-task.md#asyncio.Task "asyncio.Task") 不应该被直接实例化，请使用对应的 [`loop.create_future()`](asyncio-eventloop.md#asyncio.loop.create_future "asyncio.loop.create_future"), [`loop.create_task()`](asyncio-eventloop.md#asyncio.loop.create_task "asyncio.loop.create_task") 或 [`asyncio.create_task()`](asyncio-task.md#asyncio.create_task "asyncio.create_task") 工厂函数。

但是，第三方 _事件循环_ 可能会 _重用_ 内置的 Future 和 Task 实现以自动获得复杂且高度优化的代码。

出于这个目的，下面列出了相应的 _私有_ 构造器:

Future.__init__( _*_ , _loop =None_)¶

    

~~~
创建一个内置的 Future 实例。

_loop_ 是一个可选的事件循环实例。

Task.__init__( _coro_ , _*_ , _loop =None_, _name =None_, _context =None_)¶
~~~
    

~~~
创建一个内置的 Task 实例。

_loop_ 是一个可选的事件循环实例。 其余参数会在 [`loop.create_task()`](asyncio-eventloop.md#asyncio.loop.create_task "asyncio.loop.create_task") 说明中加以描述。

在 3.11 版本发生变更: 添加了 _context_ 参数。

## Task 生命周期支持¶

第三方任务实现应当调用下列函数以使任务对 [`asyncio.all_tasks()`](asyncio-task.md#asyncio.all_tasks "asyncio.all_tasks") 和 [`asyncio.current_task()`](asyncio-task.md#asyncio.current_task "asyncio.current_task") 可见:

asyncio._register_task( _task_ )¶
~~~
    

~~~
注册一个新的 _task_ 并由 _asyncio_ 管理。

调用来自任务构造器的函数。

asyncio._unregister_task( _task_ )¶
~~~
    

~~~
从 _asyncio_ 内置结构体中注销 _task_ 。

此函数应当在任务将要结束时被调用。

asyncio._enter_task( _loop_ , _task_ )¶
~~~
    

~~~
将当前任务切换为 _task_ 参数。

在执行嵌入的 _coroutine_ ([`coroutine.send()`](../reference/datamodel.md#coroutine.send "coroutine.send") 或 [`coroutine.throw()`](../reference/datamodel.md#coroutine.throw "coroutine.throw")) 的一部分之前调用此函数。

asyncio._leave_task( _loop_ , _task_ )¶
~~~
    

~~~
