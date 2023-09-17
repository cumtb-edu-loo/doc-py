# 用 asyncio 开发¶

异步编程与传统的“顺序”编程不同。

本页列出常见的错误和陷阱，并解释如何避免它们。

## Debug 模式¶

默认情况下，asyncio以生产模式运行。为了简化开发，asyncio还有一种*debug 模式* 。

有几种方法可以启用异步调试模式:

  * 将 [`PYTHONASYNCIODEBUG`](../using/cmdline.md#envvar-PYTHONASYNCIODEBUG) 环境变量设置为 `1` 。

  * 使用 [Python 开发模式](devmode.md#devmode)。

  * 将 `debug=True` 传递给 [`asyncio.run()`](asyncio-runner.md#asyncio.run "asyncio.run") 。

  * 调用 [`loop.set_debug()`](asyncio-eventloop.md#asyncio.loop.set_debug "asyncio.loop.set_debug") 。

除了启用调试模式外，还要考虑:

  * 将 asyncio logger 的级别设为 [`logging.DEBUG`](logging.md#logging.DEBUG "logging.DEBUG")，例如下面的代码片段可以在应用程序启动时运行:
    
        logging.basicConfig(level=logging.DEBUG)
    

  * 配置 [`warnings`](warnings.md#module-warnings "warnings: Issue warning messages and control their disposition.") 模块以显示 [`ResourceWarning`](exceptions.md#ResourceWarning "ResourceWarning") 警告。一种方法是使用 [`-W`](../using/cmdline.md#cmdoption-W) `default` 命令行选项。

启用调试模式时:

  * asyncio 检查 未被等待的协程 并记录他们；这将消除“被遗忘的等待”问题。

  * 许多非线程安全的异步 APIs (例如 [`loop.call_soon()`](asyncio-eventloop.md#asyncio.loop.call_soon "asyncio.loop.call_soon") 和 [`loop.call_at()`](asyncio-eventloop.md#asyncio.loop.call_at "asyncio.loop.call_at") 方法)，如果从错误的线程调用，则会引发异常。

  * 如果执行I/O操作花费的时间太长，则记录I/O选择器的执行时间。

  * 执行时间超过 100 毫秒的回调会被写入日志。 `loop.slow_callback_duration` 属性可用于设置以秒为单位的将被视为“缓慢”的最小执行持续时间。

## 并发性和多线程¶

事件循环在线程中运行(通常是主线程)，并在其线程中执行所有回调和任务。当一个任务在事件循环中运行时，没有其他任务可以在同一个线程中运行。当一个任务执行一个 `await` 表达式时，正在运行的任务被挂起，事件循环执行下一个任务。

要调度来自另一 OS 线程的 [callback](../glossary.md#term-callback)，应该使用 [`loop.call_soon_threadsafe()`](asyncio-eventloop.md#asyncio.loop.call_soon_threadsafe "asyncio.loop.call_soon_threadsafe") 方法。 例如:

    
    
~~~
loop.call_soon_threadsafe(callback, *args)
~~~

几乎所有异步对象都不是线程安全的，这通常不是问题，除非在任务或回调函数之外有代码可以使用它们。如果需要这样的代码来调用低级异步API，应该使用 [`loop.call_soon_threadsafe()`](asyncio-eventloop.md#asyncio.loop.call_soon_threadsafe "asyncio.loop.call_soon_threadsafe") 方法，例如:

    
    
~~~
loop.call_soon_threadsafe(fut.cancel)
~~~

要从不同的OS线程调度一个协程对象，应该使用 [`run_coroutine_threadsafe()`](asyncio-task.md#asyncio.run_coroutine_threadsafe "asyncio.run_coroutine_threadsafe") 函数。它返回一个 [`concurrent.futures.Future`](concurrent.futures.md#concurrent.futures.Future "concurrent.futures.Future") 。查询结果:

    
    
~~~
async def coro_func():
     return await asyncio.sleep(1, 42)

# Later in another OS thread:

future = asyncio.run_coroutine_threadsafe(coro_func(), loop)
# Wait for the result:
result = future.result()
~~~

为了能处理信号事件循环必须在主线程中运行。

方法 [`loop.run_in_executor()`](asyncio-eventloop.md#asyncio.loop.run_in_executor "asyncio.loop.run_in_executor") 可以和 [`concurrent.futures.ThreadPoolExecutor`](concurrent.futures.md#concurrent.futures.ThreadPoolExecutor "concurrent.futures.ThreadPoolExecutor") 一起使用，用于在一个不同的操作系统线程中执行阻塞代码，并避免阻塞运行事件循环的那个操作系统线程。

目前没有办法直接从另一个进程（如使用 [`multiprocessing`](multiprocessing.md#module-multiprocessing "multiprocessing: Process-based parallelism.") 启动的进程）安排协程或回调。 [事件循环方法集](asyncio-eventloop.md#asyncio-event-loop-methods) 小节列出了一些可以从管道读取并监视文件描述符而不会阻塞事件循环的 API。 此外，asyncio 的 [子进程](asyncio-subprocess.md#asyncio-subprocess) API 提供了一种启动进程并在事件循环中与其通信的办法。 最后，之前提到的 [`loop.run_in_executor()`](asyncio-eventloop.md#asyncio.loop.run_in_executor "asyncio.loop.run_in_executor") 方法也可以配合 [`concurrent.futures.ProcessPoolExecutor`](concurrent.futures.md#concurrent.futures.ProcessPoolExecutor "concurrent.futures.ProcessPoolExecutor") 使用以在另一个进程中执行代码。

## 运行阻塞的代码¶

不应该直接调用阻塞( CPU 绑定)代码。例如，如果一个函数执行1秒的 CPU 密集型计算，那么所有并发异步任务和 IO 操作都将延迟1秒。

可以用执行器在不同的线程甚至不同的进程中运行任务，以避免使用事件循环阻塞 OS 线程。 请参阅 [`loop.run_in_executor()`](asyncio-eventloop.md#asyncio.loop.run_in_executor "asyncio.loop.run_in_executor") 方法了解详情。

## 日志记录¶

asyncio使用 [`logging`](logging.md#module-logging "logging: Flexible event logging system for applications.") 模块，所有日志记录都是通过 `"asyncio"` logger执行的。

默认的日志级别是 [`logging.INFO`](logging.md#logging.INFO "logging.INFO")，它可以被方便地调整:

    
    
~~~
logging.getLogger("asyncio").setLevel(logging.WARNING)
~~~

网络日志会阻塞事件循环。 建议使用一个单独进程来处理日志或者使用非阻塞式 IO。 例如，可以参阅 [处理日志 handler 的阻塞](../howto/logging-cookbook.md#blocking-handlers)。

## 检测 never-awaited 协同程序¶

当协程函数被调用而不是被等待时 (即执行 `coro()` 而不是 `await coro()`) 或者协程没有通过 [`asyncio.create_task()`](asyncio-task.md#asyncio.create_task "asyncio.create_task") 被排入计划日程，asyncio 将会发出一条 [`RuntimeWarning`](exceptions.md#RuntimeWarning "RuntimeWarning"):

    
    
~~~
import asyncio

async def test():
    print("never scheduled")

async def main():
    test()

asyncio.run(main())
~~~

输出:

    
    
~~~
test.py:7: RuntimeWarning: coroutine 'test' was never awaited
  test()
~~~

调试模式的输出:

    
    
~~~
test.py:7: RuntimeWarning: coroutine 'test' was never awaited
Coroutine created at (most recent call last)
  File "../t.py", line 9, in <module>
    asyncio.run(main(), debug=True)

  < .. >

  File "../t.py", line 7, in main
    test()
  test()
~~~

通常的修复方法是等待协程或者调用 [`asyncio.create_task()`](asyncio-task.md#asyncio.create_task "asyncio.create_task") 函数:

    
    
~~~
async def main():
    await test()
~~~

## 检测就再也没异常¶

如果调用 [`Future.set_exception()`](asyncio-future.md#asyncio.Future.set_exception "asyncio.Future.set_exception") ，但不等待 Future 对象，将异常传播到用户代码。在这种情况下，当 Future 对象被垃圾收集时，asyncio将发出一条日志消息。

未处理异常的例子:

    
    
~~~
import asyncio

async def bug():
    raise Exception("not consumed")

async def main():
    asyncio.create_task(bug())

asyncio.run(main())
~~~

输出:

    
    
~~~
Task exception was never retrieved
future: <Task finished coro=<bug() done, defined at test.py:3>
  exception=Exception('not consumed')>

Traceback (most recent call last):
  File "test.py", line 4, in bug
    raise Exception("not consumed")
Exception: not consumed
~~~

激活调试模式 以获取任务创建处的跟踪信息:

    
    
~~~
asyncio.run(main(), debug=True)
~~~

调试模式的输出:

    
    
~~~
Task exception was never retrieved
future: <Task finished coro=<bug() done, defined at test.py:3>
    exception=Exception('not consumed') created at asyncio/tasks.py:321>

source_traceback: Object created at (most recent call last):
  File "../t.py", line 9, in <module>
    asyncio.run(main(), debug=True)

< .. >

Traceback (most recent call last):
  File "../t.py", line 4, in bug
    raise Exception("not consumed")
Exception: not consumed
~~~

