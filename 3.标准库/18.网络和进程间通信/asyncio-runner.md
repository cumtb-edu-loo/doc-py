# 运行器¶

**源代码:** [Lib/asyncio/runners.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/runners.py)

本节将简述用于运行异步代码的高层级异步原语。

它们构建于 [事件循环](asyncio-eventloop.md#asyncio-event-loop) 之上，其目标是简化针对常见通用场景的异步代码的用法。

  * 运行 asyncio 程序

  * 运行器上下文管理器

  * 处理键盘中断

## 运行 asyncio 程序¶

asyncio.run( _coro_ , _*_ , _debug =None_, _loop_factory =None_)¶

    

~~~
执行 [coroutine](../glossary.md#term-coroutine) _coro_ 并返回结果。

此函数会运行传入的协程，负责管理 asyncio 事件循环， _终结异步生成器_ ，并关闭执行器。

当有其他 asyncio 事件循环在同一线程中运行时，此函数不能被调用。

如果 _debug_ 为 `True`，事件循环将运行于调试模式。 `False` 将显式地禁用调试模式。 使用 `None` 将沿用全局 [Debug 模式](asyncio-dev.md#asyncio-debug-mode) 设置。

如果 _loop_factory_ 不为 `None`，它将被用来创建新的事件循环；, it is used to create a new event loop; otherwise [`asyncio.new_event_loop()`](asyncio-eventloop.md#asyncio.new_event_loop "asyncio.new_event_loop") is used. The loop is closed at the end. This function should be used as a main entry point for asyncio programs, and should ideally only be called once. It is recommended to use _loop_factory_ to configure the event loop instead of policies.

执行器的关闭有 5 分钟的超时限制。 如果执行器未在时限之内结束，将发出警告消息并关闭执行器。

示例：
~~~
    
    
~~~
async def main():
    await asyncio.sleep(1)
    print('hello')

asyncio.run(main())
~~~

在 3.7 版本加入.

在 3.9 版本发生变更: 更新为使用 [`loop.shutdown_default_executor()`](asyncio-eventloop.md#asyncio.loop.shutdown_default_executor "asyncio.loop.shutdown_default_executor")。

在 3.10 版本发生变更: 默认情况下 _debug_ 为 `None` 即沿用全局调试模式设置。

在 3.12 版本发生变更: 增加了 _loop_factory_ 形参。

## 运行器上下文管理器¶

_class _asyncio.Runner( _*_ , _debug =None_, _loop_factory =None_)¶

    

~~~
对在相同上下文中 _多个_ 异步函数调用进行简化的上下文管理器。

有时多个最高层级异步函数应当在同一个 [事件循环](asyncio-eventloop.md#asyncio-event-loop) 和 [`contextvars.Context`](contextvars.md#contextvars.Context "contextvars.Context") 中被调用。

如果 _debug_ 为 `True`，事件循环将运行于调试模式。 `False` 将显式地禁用调试模式。 使用 `None` 将沿用全局 [Debug 模式](asyncio-dev.md#asyncio-debug-mode) 设置。

_loop_factory_ 可被用来重载循环的创建。 _loop_factory_ 要负责将所创建的循环设置为当前事件循环。 在默认情况下如果 _loop_factory_ 为 `None` 则会使用 [`asyncio.new_event_loop()`](asyncio-eventloop.md#asyncio.new_event_loop "asyncio.new_event_loop") 并通过 [`asyncio.set_event_loop()`](asyncio-eventloop.md#asyncio.set_event_loop "asyncio.set_event_loop") 将其设置为当前事件循环。

基本上，`asyncio.run()` 示例可以通过运行器的使用来重写:
~~~
    
    
~~~
async def main():
    await asyncio.sleep(1)
    print('hello')

with asyncio.Runner() as runner:
    runner.run(main())
~~~

在 3.11 版本加入.

run( _coro_ , _*_ , _context =None_)¶

    

~~~
在嵌入的循环中运行一个 [协程](../glossary.md#term-coroutine) _coro_ 。

返回协程的结果或者引发其异常。

可选的仅限关键字参数 _context_ 允许指定一个自定义 [`contextvars.Context`](contextvars.md#contextvars.Context "contextvars.Context") 用作 _coro_ 运行所在的上下文。 如果为 `None` 则会使用运行器的默认上下文。

当有其他 asyncio 事件循环在同一线程中运行时，此函数不能被调用。

close()¶
~~~
    

~~~
关闭运行器。

最终化异步生成器，停止默认执行器，关闭事件循环并释放嵌入的 [`contextvars.Context`](contextvars.md#contextvars.Context "contextvars.Context")。

get_loop()¶
~~~
    

~~~
