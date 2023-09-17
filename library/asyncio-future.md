# Futures¶

**源代码:** [Lib/asyncio/futures.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/futures.py), [Lib/asyncio/base_futures.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/base_futures.py)

* * *

_Future_ 对象用来链接 **底层回调式代码** 和高层异步/等待式代码。

## Future 函数¶

asyncio.isfuture( _obj_ )¶

    

~~~
如果 _obj_ 为下面任意对象，返回 `True`：

  * 一个 `asyncio.Future` 类的实例，

  * 一个 [`asyncio.Task`](asyncio-task.md#asyncio.Task "asyncio.Task") 类的实例，

  * 带有 `_asyncio_future_blocking` 属性的类似 Future 的对象。

在 3.5 版本加入.

asyncio.ensure_future( _obj_ , _*_ , _loop =None_)¶
~~~
    

~~~
返回：

  * _obj_ 参数会是保持原样，如果 _obj_ 是 `Future`、 [`Task`](asyncio-task.md#asyncio.Task "asyncio.Task") 或 类似 Future 的对象( `isfuture()` 用于测试。)

  * 封装了 _obj_ 的 [`Task`](asyncio-task.md#asyncio.Task "asyncio.Task") 对象，如果 _obj_ 是一个协程 (使用 [`iscoroutine()`](asyncio-task.md#asyncio.iscoroutine "asyncio.iscoroutine") 进行检测)；在此情况下该协程将通过 `ensure_future()` 加入执行计划。

  * 等待 _obj_ 的 [`Task`](asyncio-task.md#asyncio.Task "asyncio.Task") 对象，如果 _obj_ 是一个可等待对象( [`inspect.isawaitable()`](inspect.md#inspect.isawaitable "inspect.isawaitable") 用于测试)

如果 _obj_ 不是上述对象会引发一个 [`TypeError`](exceptions.md#TypeError "TypeError") 异常。

重要

查看 [`create_task()`](asyncio-task.md#asyncio.create_task "asyncio.create_task") 函数，它是创建新任务的首选途径。

保存一个指向此函数的结果的引用，以避免任务在执行期间消失。

在 3.5.1 版本发生变更: 这个函数接受任意 [awaitable](../glossary.md#term-awaitable) 对象。

自 3.10 版本弃用: 如果 _obj_ 不是 Future 类对象同时未指定 _loop_ 并且没有正在运行的事件循环则会发出弃用警告。

asyncio.wrap_future( _future_ , _*_ , _loop =None_)¶
~~~
    

~~~
将一个 [`concurrent.futures.Future`](concurrent.futures.md#concurrent.futures.Future "concurrent.futures.Future") 对象封装到 `asyncio.Future` 对象中。

自 3.10 版本弃用: 如果 _future_ 不是 Future 类对象同时未指定 _loop_ 并且没有正在运行的事件循环则会发出弃用警告。

## Future 对象¶

_class _asyncio.Future( _*_ , _loop =None_)¶
~~~
    

~~~
一个 Future 代表一个异步运算的最终结果。线程不安全。

Future 是一个 [awaitable](../glossary.md#term-awaitable) 对象。 协程可以等待 Future 对象直到它们有结果或设置了异常，或者直到它们被取消。 一个 Future 可被等待多次并且结果相同。

通常 Future 用于支持底层回调式代码(例如在协议实现中使用asyncio [transports](asyncio-protocol.md#asyncio-transports-protocols)) 与高层异步/等待式代码交互。

经验告诉我们永远不要面向用户的接口暴露 Future 对象，同时建议使用 [`loop.create_future()`](asyncio-eventloop.md#asyncio.loop.create_future "asyncio.loop.create_future") 来创建 Future 对象。这种方法可以让 Future 对象使用其它的事件循环实现，它可以注入自己的优化实现。

在 3.7 版本发生变更: 加入对 [`contextvars`](contextvars.md#module-contextvars "contextvars: Context Variables") 模块的支持。

自 3.10 版本弃用: 如果未指定 _loop_ 并且没有正在运行的事件循环则会发出弃用警告。

result()¶
~~~
    

~~~
返回 Future 的结果。

如果 Future 状态为 _完成_ ，并由 `set_result()` 方法设置一个结果，则返回这个结果。

如果 Future 状态为 _完成_ ，并由 `set_exception()` 方法设置一个异常，那么这个方法会引发异常。

如果 Future 已 _取消_ ，方法会引发一个 [`CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 异常。

如果 Future 的结果还不可用，此方法会引发一个 [`InvalidStateError`](asyncio-exceptions.md#asyncio.InvalidStateError "asyncio.InvalidStateError") 异常。

set_result( _result_ )¶
~~~
    

~~~
将 Future 标记为 _完成_ 并设置结果。

如果 Future 已经 _完成_ 则抛出一个 [`InvalidStateError`](asyncio-exceptions.md#asyncio.InvalidStateError "asyncio.InvalidStateError") 错误。

set_exception( _exception_ )¶
~~~
    

~~~
将 Future 标记为 _完成_ 并设置一个异常。

如果 Future 已经 _完成_ 则抛出一个 [`InvalidStateError`](asyncio-exceptions.md#asyncio.InvalidStateError "asyncio.InvalidStateError") 错误。

done()¶
~~~
    

~~~
如果 Future 为已 _完成_ 则返回 `True` 。

如果 Future 为 _取消_ 或调用 `set_result()` 设置了结果或调用 `set_exception()` 设置了异常，那么它就是 _完成_ 。

cancelled()¶
~~~
    

~~~
如果 Future 已 _取消_ 则返回 `True`

这个方法通常在设置结果或异常前用来检查 Future 是否已 _取消_ 。
~~~
    
    
~~~
if not fut.cancelled():
    fut.set_result(42)
~~~

add_done_callback( _callback_ , _*_ , _context =None_)¶

    

~~~
添加一个在 Future _完成_ 时运行的回调函数。

调用 _callback_ 时，Future 对象是它的唯一参数。

如果调用这个方法时 Future 已经 _完成_ ，回调函数会被 [`loop.call_soon()`](asyncio-eventloop.md#asyncio.loop.call_soon "asyncio.loop.call_soon") 调度。

可选键值类的参数 _context_ 允许 _callback_ 运行在一个指定的自定义 [`contextvars.Context`](contextvars.md#contextvars.Context "contextvars.Context") 对象中。如果没有提供 _context_ ，则使用当前上下文。

可以用 [`functools.partial()`](functools.md#functools.partial "functools.partial") 给回调函数传递参数，例如:
~~~
    
    
~~~
# Call 'print("Future:", fut)' when "fut" is done.
fut.add_done_callback(
    functools.partial(print, "Future:"))
~~~

在 3.7 版本发生变更: 加入键值类形参 _context_ 。请参阅 [**PEP 567**](https://peps.python.org/pep-0567/) 查看更多细节。

remove_done_callback( _callback_ )¶

    

~~~
从回调列表中移除 _callback_ 。

返回被移除的回调函数的数量，通常为1，除非一个回调函数被添加多次。

cancel( _msg =None_)¶
~~~
    

~~~
取消 Future 并调度回调函数。

如果 Future 已经 _完成_ 或 _取消_ ，返回 `False` 。否则将 Future 状态改为 _取消_ 并在调度回调函数后返回 `True` 。

在 3.9 版本发生变更: 增加了 _msg_ 形参。

exception()¶
~~~
    

~~~
返回 Future 已设置的异常。

只有 Future 在 _完成_ 时才返回异常（或者 `None` ，如果没有设置异常）。

如果 Future 已 _取消_ ，方法会引发一个 [`CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 异常。

如果 Future 还没 _完成_ ，这个方法会引发一个 [`InvalidStateError`](asyncio-exceptions.md#asyncio.InvalidStateError "asyncio.InvalidStateError") 异常。

get_loop()¶
~~~
    

~~~
返回 Future 对象已绑定的事件循环。

在 3.7 版本加入.

这个例子创建一个 Future 对象，创建和调度一个异步任务去设置 Future 结果，然后等待其结果:
~~~
    
    
~~~
async def set_after(fut, delay, value):
    # Sleep for *delay* seconds.
    await asyncio.sleep(delay)

    # Set *value* as a result of *fut* Future.
    fut.set_result(value)

async def main():
    # Get the current event loop.
    loop = asyncio.get_running_loop()

    # Create a new Future object.
    fut = loop.create_future()

    # Run "set_after()" coroutine in a parallel Task.
    # We are using the low-level "loop.create_task()" API here because
    # we already have a reference to the event loop at hand.
    # Otherwise we could have just used "asyncio.create_task()".
    loop.create_task(
        set_after(fut, 1, '... world'))

    print('hello ...')

    # Wait until *fut* has a result (1 second) and print it.
    print(await fut)

asyncio.run(main())
~~~

重要

该 Future 对象是为了模仿 [`concurrent.futures.Future`](concurrent.futures.md#concurrent.futures.Future "concurrent.futures.Future") 类。主要差异包含：

  * 与 asyncio 的 Future 不同，[`concurrent.futures.Future`](concurrent.futures.md#concurrent.futures.Future "concurrent.futures.Future") 实例不是可等待对象。

  * `asyncio.Future.result()` 和 `asyncio.Future.exception()` 不接受 _timeout_ 参数。

  * Future 没有 _完成_ 时 `asyncio.Future.result()` 和 `asyncio.Future.exception()` 抛出一个 [`InvalidStateError`](asyncio-exceptions.md#asyncio.InvalidStateError "asyncio.InvalidStateError") 异常。

  * 使用 `asyncio.Future.add_done_callback()` 注册的回调函数不会立即调用，而是被 [`loop.call_soon()`](asyncio-eventloop.md#asyncio.loop.call_soon "asyncio.loop.call_soon") 调度。

  * asyncio Future 不能兼容 [`concurrent.futures.wait()`](concurrent.futures.md#concurrent.futures.wait "concurrent.futures.wait") 和 [`concurrent.futures.as_completed()`](concurrent.futures.md#concurrent.futures.as_completed "concurrent.futures.as_completed") 函数。

  * `asyncio.Future.cancel()` 接受一个可选的 `msg` 参数，但 [`concurrent.futures.Future.cancel()`](concurrent.futures.md#concurrent.futures.Future.cancel "concurrent.futures.Future.cancel") 无此参数。

