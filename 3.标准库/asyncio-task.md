# 协程与任务¶

本节将简述用于协程与任务的高层级 API。

  * 协程

  * 可等待对象

  * 创建任务

  * 任务取消

  * 任务组

  * 休眠

  * 并发运行任务

  * 主动任务工厂

  * 屏蔽取消操作

  * 超时

  * 简单等待

  * 在线程中运行

  * 跨线程调度

  * 内省

  * Task 对象

## 协程¶

_源码：_ [Lib/asyncio/coroutines.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/coroutines.py)

* * *

通过 async/await 语法来声明 [协程](../glossary.md#term-coroutine) 是编写 asyncio 应用的推荐方式。 例如，以下代码段会打印 "hello"，等待 1 秒，再打印 "world":

    
    
~~~shell
>>> import asyncio

>>> async def main():
...     print('hello')
...     await asyncio.sleep(1)
...     print('world')

>>> asyncio.run(main())
hello
world
~~~

注意：简单地调用一个协程并不会使其被调度执行

    
    
~~~shell
>>> main()
<coroutine object main at 0x1053bb7c8>
~~~

要实际运行一个协程，asyncio 提供了以下几种机制:

  * [`asyncio.run()`](asyncio-runner.md#asyncio.run "asyncio.run") 函数用来运行最高层级的入口点 "main()" 函数 (参见上面的示例。)

  * 等待一个协程。以下代码段会在等待 1 秒后打印 "hello"，然后 _再次_ 等待 2 秒后打印 "world":
    
        import asyncio
    import time
    
    async def say_after(delay, what):
        await asyncio.sleep(delay)
        print(what)
    
    async def main():
        print(f"started at {time.strftime('%X')}")
    
        await say_after(1, 'hello')
        await say_after(2, 'world')
    
        print(f"finished at {time.strftime('%X')}")
    
    asyncio.run(main())
    

预期的输出:

    
        started at 17:13:52
    hello
    world
    finished at 17:13:55
    

  * `asyncio.create_task()` 函数用来并发运行作为 asyncio `任务` 的多个协程。

让我们修改以上示例， _并发_ 运行两个 `say_after` 协程:

    
        async def main():
        task1 = asyncio.create_task(
            say_after(1, 'hello'))
    
        task2 = asyncio.create_task(
            say_after(2, 'world'))
    
        print(f"started at {time.strftime('%X')}")
    
        # Wait until both tasks are completed (should take
        # around 2 seconds.)
        await task1
        await task2
    
        print(f"finished at {time.strftime('%X')}")
    

注意，预期的输出显示代码段的运行时间比之前快了 1 秒:

    
        started at 17:14:32
    hello
    world
    finished at 17:14:34
    

  * `asyncio.TaskGroup` 类提供了 `create_task()` 的更现代化的替代。 使用此 API，之前的例子将变为:
    
        async def main():
        async with asyncio.TaskGroup() as tg:
            task1 = tg.create_task(
                say_after(1, 'hello'))
    
            task2 = tg.create_task(
                say_after(2, 'world'))
    
            print(f"started at {time.strftime('%X')}")
    
        # The await is implicit when the context manager exits.
    
        print(f"finished at {time.strftime('%X')}")
    

用时和输出结果应当与之前的版本相同。

在 3.11 版本加入: `asyncio.TaskGroup`。

## 可等待对象¶

如果一个对象可以在 [`await`](expressions.md#await) 语句中使用，那么它就是 **可等待** 对象。许多 asyncio API 都被设计为接受可等待对象。

_可等待_ 对象有三种主要类型: **协程** , **任务** 和 **Future**.

协程

Python 协程属于 _可等待_ 对象，因此可以在其他协程中被等待:

    
    
~~~
import asyncio

async def nested():
    return 42

async def main():
    # Nothing happens if we just call "nested()".
    # A coroutine object is created but not awaited,
    # so it *won't run at all*.
    nested()

    # Let's do it differently now and await it:
    print(await nested())  # will print "42".

asyncio.run(main())
~~~

重要

在本文档中 "协程" 可用来表示两个紧密关联的概念:

  * _协程函数_ : 定义形式为 [`async def`](compound_stmts.md#async-def) 的函数;

  * _协程对象_ : 调用 _协程函数_ 所返回的对象。

任务

_任务_ 被用来“并行的”调度协程

当一个协程通过 `asyncio.create_task()` 等函数被封装为一个 _任务_ ，该协程会被自动调度执行:

    
    
~~~
import asyncio

async def nested():
    return 42

async def main():
    # Schedule nested() to run soon concurrently
    # with "main()".
    task = asyncio.create_task(nested())

    # "task" can now be used to cancel "nested()", or
    # can simply be awaited to wait until it is complete:
    await task

asyncio.run(main())
~~~

Futures

[`Future`](asyncio-future.md#asyncio.Future "asyncio.Future") 是一种特殊的 **低层级** 可等待对象，表示一个异步操作的 **最终结果** 。

当一个 Future 对象 _被等待_ ，这意味着协程将保持等待直到该 Future 对象在其他地方操作完毕。

在 asyncio 中需要 Future 对象以便允许通过 async/await 使用基于回调的代码。

通常情况下 **没有必要** 在应用层级的代码中创建 Future 对象。

Future 对象有时会由库和某些 asyncio API 暴露给用户，用作可等待对象:

    
    
~~~
async def main():
    await function_that_returns_a_future_object()

    # this is also valid:
    await asyncio.gather(
        function_that_returns_a_future_object(),
        some_python_coroutine()
    )
~~~

一个很好的返回对象的低层级函数的示例是 [`loop.run_in_executor()`](asyncio-eventloop.md#asyncio.loop.run_in_executor "asyncio.loop.run_in_executor")。

## 创建任务¶

**源码：** [Lib/asyncio/tasks.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/tasks.py)

* * *

asyncio.create_task( _coro_ , _*_ , _name =None_, _context =None_)¶

    

~~~
将 _coro_ 协程 封装为一个 `Task` 并调度其执行。返回 Task 对象。

_name_ 不为 `None`，它将使用 `Task.set_name()` 来设为任务的名称。

可选的 _context_ 参数允许指定自定义的 [`contextvars.Context`](contextvars.md#contextvars.Context "contextvars.Context") 供 _coro_ 运行。 当未提供 _context_ 时将创建当前上下文的副本。

该任务会在 [`get_running_loop()`](asyncio-eventloop.md#asyncio.get_running_loop "asyncio.get_running_loop") 返回的循环中执行，如果当前线程没有在运行的循环则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

备注

`asyncio.TaskGroup.create_task()` 是一个平衡了结构化并发的新选择；它允许等待一组相关任务并具有极强的安全保证。

重要

保存一个指向此函数的结果的引用，以避免任务在执行过程中消失。 事件循环将只保留对任务的弱引用。 未在其他地方被引用的任务可能在任何时候被作为垃圾回收，即使是在它被完成之前。 如果需要可靠的“发射后不用管”后台任务，请将它们放到一个多项集中:
~~~
    
    
~~~
background_tasks = set()

for i in range(10):
    task = asyncio.create_task(some_coro(param=i))

    # Add task to the set. This creates a strong reference.
    background_tasks.add(task)

    # To prevent keeping references to finished tasks forever,
    # make each task remove its own reference from the set after
    # completion:
    task.add_done_callback(background_tasks.discard)
~~~

在 3.7 版本加入.

在 3.8 版本发生变更: 添加了 _name_ 形参。

在 3.11 版本发生变更: 增加了 _context_ 形参。

## 任务取消¶

任务可以便捷和安全地取消。 当任务被取消时，[`asyncio.CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 将在遇到机会时在任务中被引发。

推荐协程使用 `try/finally` 代码块来可靠地执行清理逻辑。 对于 [`asyncio.CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 被显式捕获的情况，它通常应当在清理完成时被传播。 [`asyncio.CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 会直接子类化 [`BaseException`](3.标准库/exceptions.md#BaseException "BaseException") 因此大多数代码都不需要关心这一点。

启用结构化并发的 asyncio 组件，如 `asyncio.TaskGroup` 和 `asyncio.timeout()`，在内部是使用撤销操作来实现的因而在协程屏蔽了 [`asyncio.CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 时可能无法正常工作。 类似地，用户代码通常也不应调用 `uncancel`。 但是，在确实想要屏蔽 [`asyncio.CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 的情况下，则还有必要调用 `uncancel()` 来完全移除撤销状态。

## 任务组¶

任务组合并了一套用于等待分组中所有任务完成的方便可靠方式的任务创建 API。

_class _asyncio.TaskGroup¶

    

~~~
持有一个任务分组的 [异步上下文管理器](../reference/datamodel.md#async-context-managers)。 可以使用 `create_task()` 将任务添加到分组中。 当该上下文管理器退出时所有任务都将被等待。

在 3.11 版本加入.

create_task( _coro_ , _*_ , _name =None_, _context =None_)¶
~~~
    

~~~
在该任务组中创建一个任务。 其签名与 `asyncio.create_task()` 的相匹配。

示例:
~~~
    
    
~~~
async def main():
    async with asyncio.TaskGroup() as tg:
        task1 = tg.create_task(some_coro(...))
        task2 = tg.create_task(another_coro(...))
    print(f"Both tasks have completed now: {task1.result()}, {task2.result()}")
~~~

`async with` 语句将等待分组中的所有任务结束。 在等待期间，仍可将新任务添加到分组中 (例如，通过将 `tg` 传入某个协程并在该协程中调用 `tg.create_task()`)。 一旦最后的任务完成并退出 `async with` 代码块，将无法再向分组添加新任务。

当首次有任何属于分组的任务因 [`asyncio.CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 以外的异常而失败时，分组中的剩余任务将被取消。 在此之后将无法添加更多任务到该分组中。 在这种情况下，如果 `async with` 语句体仍然为激活状态（即 [`__aexit__()`](datamodel.md#object.__aexit__ "object.__aexit__") 尚未被调用），则直接包含 `async with` 语句的任务也会被取消。 结果 [`asyncio.CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 将中断一个 `await`，但它将不会跳出包含的 `async with` 语句。

一旦所有任务被完成，如果有任何任务因 [`asyncio.CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 以外的异常而失败，这些异常会被组合在 [`ExceptionGroup`](3.标准库/exceptions.md#ExceptionGroup "ExceptionGroup") 或 [`BaseExceptionGroup`](3.标准库/exceptions.md#BaseExceptionGroup "BaseExceptionGroup") 中（选择其中较适合的一个；参见其文档）并将随后引发。

两个基础异常会被特别对待：如果有任何任务因 [`KeyboardInterrupt`](3.标准库/exceptions.md#KeyboardInterrupt "KeyboardInterrupt") 或 [`SystemExit`](3.标准库/exceptions.md#SystemExit "SystemExit") 而失败，任务分组仍然会取消剩余的任务并等待它们，但随后初始 [`KeyboardInterrupt`](3.标准库/exceptions.md#KeyboardInterrupt "KeyboardInterrupt") 或 [`SystemExit`](3.标准库/exceptions.md#SystemExit "SystemExit") 而不是 [`ExceptionGroup`](3.标准库/exceptions.md#ExceptionGroup "ExceptionGroup") 或 [`BaseExceptionGroup`](3.标准库/exceptions.md#BaseExceptionGroup "BaseExceptionGroup") 会被重新引发。

如果 `async with` 语句体因异常而退出（这样将调用 [`__aexit__()`](datamodel.md#object.__aexit__ "object.__aexit__") 并附带一个异常），此种情况会与有任务失败时一样对待：剩余任务将被取消然后被等待，而非取消类异常会被加入到一个异常分组并被引发。 传入到 [`__aexit__()`](datamodel.md#object.__aexit__ "object.__aexit__") 的异常，除了 [`asyncio.CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 以外，也都会被包括在该异常分组中。 同样的特殊对待也适用于上一段所说的 [`KeyboardInterrupt`](3.标准库/exceptions.md#KeyboardInterrupt "KeyboardInterrupt") 和 [`SystemExit`](3.标准库/exceptions.md#SystemExit "SystemExit")。

## 休眠¶

_coroutine _asyncio.sleep( _delay_ , _result =None_)¶

    

~~~
阻塞 _delay_ 指定的秒数。

如果指定了 _result_ ，则当协程完成时将其返回给调用者。

`sleep()` 总是会挂起当前任务，以允许其他任务运行。

将 delay 设为 0 将提供一个经优化的路径以允许其他任务运行。 这可供长期间运行的函数使用以避免在函数调用的全过程中阻塞事件循环。

以下协程示例运行 5 秒，每秒显示一次当前日期:
~~~
    
    
~~~
import asyncio
import datetime

async def display_date():
    loop = asyncio.get_running_loop()
    end_time = loop.time() + 5.0
    while True:
        print(datetime.datetime.now())
        if (loop.time() + 1.0) >= end_time:
            break
        await asyncio.sleep(1)

asyncio.run(display_date())
~~~

在 3.10 版本发生变更: 移除了 _loop_ 形参。

在 3.13 版本发生变更: Raises [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError") if _delay_ is [`nan`](math.md#math.nan "math.nan").

## 并发运行任务¶

_awaitable _asyncio.gather( _* aws_, _return_exceptions =False_)¶

    

~~~
_并发_ 运行 _aws_ 序列中的 可等待对象。

如果 _aws_ 中的某个可等待对象为协程，它将自动被作为一个任务调度。

如果所有可等待对象都成功完成，结果将是一个由所有返回值聚合而成的列表。结果值的顺序与 _aws_ 中可等待对象的顺序一致。

如果 _return_exceptions_ 为 `False` (默认)，所引发的首个异常会立即传播给等待 `gather()` 的任务。 _aws_ 序列中的其他可等待对象 **不会被取消** 并将继续运行。

如果 _return_exceptions_ 为 `True`，异常会和成功的结果一样处理，并聚合至结果列表。

如果 `gather()` _被取消_ ，所有被提交 (尚未完成) 的可等待对象也会 _被取消_ 。

如果 _aws_ 序列中的任一 Task 或 Future 对象 _被取消_ ，它将被当作引发了 [`CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 一样处理 -- 在此情况下 `gather()` 调用 **不会** 被取消。这是为了防止一个已提交的 Task/Future 被取消导致其他 Tasks/Future 也被取消。

备注

一个创建然后并发地运行任务等待它们完成的新选择是 `asyncio.TaskGroup`。 _TaskGroup_ 提供了针对调度嵌套子任务的比 _gather_ 更强的安全保证：如果一个任务（或子任务，即由一个任务调度的任务）引发了异常， _TaskGroup_ 将取消剩余的已排期任务）。

示例:
~~~
    
    
~~~
import asyncio

async def factorial(name, number):
    f = 1
    for i in range(2, number + 1):
        print(f"Task {name}: Compute factorial({number}), currently i={i}...")
        await asyncio.sleep(1)
        f *= i
    print(f"Task {name}: factorial({number}) = {f}")
    return f

async def main():
    # Schedule three calls *concurrently*:
    L = await asyncio.gather(
        factorial("A", 2),
        factorial("B", 3),
        factorial("C", 4),
    )
    print(L)

asyncio.run(main())

# Expected output:
#
#     Task A: Compute factorial(2), currently i=2...
#     Task B: Compute factorial(3), currently i=2...
#     Task C: Compute factorial(4), currently i=2...
#     Task A: factorial(2) = 2
#     Task B: Compute factorial(3), currently i=3...
#     Task C: Compute factorial(4), currently i=3...
#     Task B: factorial(3) = 6
#     Task C: Compute factorial(4), currently i=4...
#     Task C: factorial(4) = 24
#     [2, 6, 24]
~~~

备注

如果 _return_exceptions_ 为 False，则在 gather() 被标记为已完成后取消它将不会取消任何已提交的可等待对象。 例如，在将一个异常传播给调用者之后，gather 可被标记为已完成，因此，在从 gather 捕获一个（由可等待对象所引发的）异常之后调用 `gather.cancel()` 将不会取消任何其他可等待对象。

在 3.7 版本发生变更: 如果 _gather_ 本身被取消，则无论 _return_exceptions_ 取值为何，消息都会被传播。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

自 3.10 版本弃用: 如果未提供位置参数或者并非所有位置参数均为 Future 类对象并且没有正在运行的事件循环则会发出弃用警告。

## 主动任务工厂¶

asyncio.eager_task_factory( _loop_ , _coro_ , _*_ , _name =None_, _context =None_)¶

    

~~~
用于主动任务执行的任务工厂

当使用这个工厂函数时 (通过 [`loop.set_task_factory(asyncio.eager_task_factory)`](asyncio-eventloop.md#asyncio.loop.set_task_factory "asyncio.loop.set_task_factory"))，协程将在 `Task` 构造期间同步地开始执行。 任务仅会在它们阻塞时被加入事件循环上的计划任务。 这可以达成性能提升因为对同步完成的协程来说可以避免循环调度的开销。

此特性会带来好处的一个常见例子是应用了缓存或记忆功能以便在可能的情况避免实际 I/O 的协程。

备注

协程是立即执行是一项语言改变。 如果协程返回或引发异常，其任务将不会被加入事件循环上的计划任务。 如果协程执行发生阻塞，其任务将被加入事件循环上的计划任务。 这项改变可能会向现有应用程序引入行为变化。 例如，应用程序的任务执行顺序可能会发生改变。

在 3.12 版本加入.

asyncio.create_eager_task_factory( _custom_task_constructor_ )¶
~~~
    

~~~
创建一个主动型任务工厂，类似于 `eager_task_factory()`，在创建新任务时使用所提供的 _custom_task_constructor_ 而不是默认的 `Task`。

_custom_task_constructor_ 必须是一个 _可调用对象_ ，其签名与 `Task.__init__` 的签名相匹配。 该可调用对象必须返回一个兼容 `asyncio.Task` 的对象。

此函数返回一个 _可调用对象_ ，将通过 [`loop.set_task_factory(factory)`](asyncio-eventloop.md#asyncio.loop.set_task_factory "asyncio.loop.set_task_factory")) 被用作一个事件循环的任务工厂。

在 3.12 版本加入.

## 屏蔽取消操作¶

_awaitable _asyncio.shield( _aw_ )¶
~~~
    

~~~
保护一个 可等待对象 防止其被 `取消`。

如果 _aw_ 是一个协程，它将自动被作为任务调度。

以下语句:
~~~
    
    
~~~
task = asyncio.create_task(something())
res = await shield(task)
~~~

相当于:

    
    
~~~
res = await shield(something())
~~~

_不同之处_ 在于如果包含它的协程被取消，在 `something()` 中运行的任务不会被取消。从 `something()` 的角度看来，取消操作并没有发生。然而其调用者已被取消，因此 "await" 表达式仍然会引发 [`CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError")。

如果通过其他方式取消 `something()` (例如在其内部操作) 则 `shield()` 也会取消。

如果希望完全忽略取消操作 (不推荐) 则 `shield()` 函数需要配合一个 try/except 代码段，如下所示:

    
    
~~~
task = asyncio.create_task(something())
try:
    res = await shield(task)
except CancelledError:
    res = None
~~~

重要

保存一个传给此函数的任务的引用，以避免任务在执行过程中消失。 事件循环将只保留对任务的弱引用。 未在其他地方被引用的任务可能在任何时候被作为垃圾回收，即使是在它被完成之前。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

自 3.10 版本弃用: 如果 _aw_ 不是 Future 类对象并且没有正在运行的事件循环则会发出弃用警告。

## 超时¶

asyncio.timeout( _delay_ )¶

    

~~~
返回一个可被用于限制等待某个操作所耗费时间的 [异步上下文管理器](../reference/datamodel.md#async-context-managers)。

_delay_ 可以为 `None`，或是一个表示等待秒数的浮点数/整数。 如果 _delay_ 为 `None`，将不会应用时间限制；如果当创建上下文管理器时无法确定延时则此设置将很适用。

在两种情况下，该上下文管理器都可以在创建之后使用 `Timeout.reschedule()` 来重新安排计划。

示例:
~~~
    
    
~~~
async def main():
    async with asyncio.timeout(10):
        await long_running_task()
~~~

如果 `long_running_task` 耗费 10 秒以上完成，该上下文管理器将取消当前任务并在内部处理所引发的 [`asyncio.CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError")，将其转化为可被捕获和处理的 [`TimeoutError`](3.标准库/exceptions.md#TimeoutError "TimeoutError")。

备注

`asyncio.timeout()` 上下文管理器负责将 [`asyncio.CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 转化为 [`TimeoutError`](3.标准库/exceptions.md#TimeoutError "TimeoutError")，这意味着 [`TimeoutError`](3.标准库/exceptions.md#TimeoutError "TimeoutError") 只能在该上下文管理器 _之外_ 被捕获。

捕获 [`TimeoutError`](3.标准库/exceptions.md#TimeoutError "TimeoutError") 的示例:

    
    
~~~
async def main():
    try:
        async with asyncio.timeout(10):
            await long_running_task()
    except TimeoutError:
        print("The long operation timed out, but we've handled it.")

    print("This statement will run regardless.")
~~~

`asyncio.timeout()` 所产生的上下文管理器可以被重新调整到不同的终止点并执行检查。

_class _asyncio.Timeout( _when_ )¶

    

~~~
一个用于撤销已过期协程的 [异步内容管理器](../reference/datamodel.md#async-context-managers)。

`when` 应当是一个指明上下文将要过期的绝对时间，由事件循环的时钟来计时。

  * 如果 `when` 为 `None`，则超时将永远不会被触发。

  * 如果 `when < loop.time()`，则超时将在事件循环的下一次迭代中被触发。

> when() -> [float](functions.md#float "float") | [None](constants.md#None "None")¶
>  
>
> 返回当前终止点，或者如果未设置当前终止点则返回 `None`。
>
> reschedule( _when : [float](functions.md#float "float") | [None](constants.md#None "None")_)¶
>  
>
> 重新安排超时。
>
> expired() -> [bool](functions.md#bool "bool")¶
>  
>
> 返回上下文管理器是否已超出时限（过期）。

示例:
~~~
    
    
~~~
async def main():
    try:
        # We do not know the timeout when starting, so we pass ``None``.
        async with asyncio.timeout(None) as cm:
            # We know the timeout now, so we reschedule it.
            new_deadline = get_running_loop().time() + 10
            cm.reschedule(new_deadline)

            await long_running_task()
    except TimeoutError:
        pass

    if cm.expired():
        print("Looks like we haven't finished on time.")
~~~

超时上下文管理器可以被安全地嵌套。

在 3.11 版本加入.

asyncio.timeout_at( _when_ )¶

    

~~~
类似于 `asyncio.timeout()`，不同之处在于 _when_ 是停止等待的绝对时间，或者为 `None`。

示例:
~~~
    
    
~~~
async def main():
    loop = get_running_loop()
    deadline = loop.time() + 20
    try:
        async with asyncio.timeout_at(deadline):
            await long_running_task()
    except TimeoutError:
        print("The long operation timed out, but we've handled it.")

    print("This statement will run regardless.")
~~~

在 3.11 版本加入.

_coroutine _asyncio.wait_for( _aw_ , _timeout_ )¶

    

~~~
等待 _aw_ 可等待对象 完成，指定 timeout 秒数后超时。

如果 _aw_ 是一个协程，它将自动被作为任务调度。

_timeout_ 可以为 `None`，也可以为 float 或 int 型数值表示的等待秒数。如果 _timeout_ 为 `None`，则等待直到完成。

如果发生超时，将取消任务并引发 [`TimeoutError`](exceptions.md#TimeoutError "TimeoutError")。

要避免任务 `取消`，可以加上 `shield()`。

此函数将等待直到 Future 确实被取消，所以总等待时间可能超过 _timeout_ 。 如果在取消期间发生了异常，异常将会被传播。

如果等待被取消，则 _aw_ 指定的对象也会被取消。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

示例:
~~~
    
    
~~~
async def eternity():
    # Sleep for one hour
    await asyncio.sleep(3600)
    print('yay!')

async def main():
    # Wait for at most 1 second
    try:
        await asyncio.wait_for(eternity(), timeout=1.0)
    except TimeoutError:
        print('timeout!')

asyncio.run(main())

# Expected output:
#
#     timeout!
~~~

在 3.7 版本发生变更: 当 _aw_ 由于超时被取消时，`wait_for` 会等待 _aw_ 被取消。 在之前版本中，它会立即引发 [`TimeoutError`](3.标准库/exceptions.md#TimeoutError "TimeoutError")。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

## 简单等待¶

_coroutine _asyncio.wait( _aws_ , _*_ , _timeout =None_, _return_when =ALL_COMPLETED_)¶

    

~~~
并发地运行 _aws_ 可迭代对象中的 [`Future`](asyncio-future.md#asyncio.Future "asyncio.Future") 和 `Task` 实例并进入阻塞状态直到满足 _return_when_ 所指定的条件。

_aws_ 可迭代对象必须不为空。

返回两个 Task/Future 集合: `(done, pending)`。

用法：
~~~
    
    
~~~
done, pending = await asyncio.wait(aws)
~~~

如指定 _timeout_ (float 或 int 类型) 则它将被用于控制返回之前等待的最长秒数。

请注意此函数不会引发 [`TimeoutError`](3.标准库/exceptions.md#TimeoutError "TimeoutError")。 当超时发生时尚未完成的 Future 或 Task 会在设定的秒数后被直接返回。

_return_when_ 指定此函数应在何时返回。它必须为以下常数之一:

常量

|

描述  
  
---|---  
  
`FIRST_COMPLETED`

|

函数将在任意可等待对象结束或取消时返回。  
  
`FIRST_EXCEPTION`

|

函数将在任意可等待对象因引发异常而结束时返回。当没有引发任何异常时它就相当于 `ALL_COMPLETED`。  
  
`ALL_COMPLETED`

|

函数将在所有可等待对象结束或取消时返回。  
  
与 `wait_for()` 不同，`wait()` 在超时发生时不会取消可等待对象。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

在 3.11 版本发生变更: 直接向 `wait()` 传入协程对象的方式已被弃用。

在 3.12 版本发生变更: 增加了对产生任务的生成器的支持。

asyncio.as_completed( _aws_ , _*_ , _timeout =None_)¶

    

~~~
并发地运行 _aws_ 可迭代对象中的 可等待对象。 返回一个协程的迭代器。 所返回的每个协程可被等待以从剩余的可等待对象的可迭代对象中获得最早的下一个结果。

如果在所有 Future 对象完成之前发生超时则将引发 [`TimeoutError`](exceptions.md#TimeoutError "TimeoutError")。

示例:
~~~
    
    
~~~
for coro in as_completed(aws):
    earliest_result = await coro
    # ...
~~~

在 3.10 版本发生变更: 移除了 _loop_ 形参。

自 3.10 版本弃用: 如果 _aws_ 可迭代对象中的可等待对象不全为 Future 类对象并且没有正在运行的事件循环则会发出弃用警告。

在 3.12 版本发生变更: 增加了对产生任务的生成器的支持。

## 在线程中运行¶

_coroutine _asyncio.to_thread( _func_ , _/_ , _* args_, _** kwargs_)¶

    

~~~
在不同的线程中异步地运行函数 _func_ 。

向此函数提供的任何 *args 和 **kwargs 会被直接传给 _func_ 。 并且，当前 [`contextvars.Context`](contextvars.md#contextvars.Context "contextvars.Context") 会被传播，允许在不同的线程中访问来自事件循环的上下文变量。

返回一个可被等待以获取 _func_ 的最终结果的协程。

这个协程函数主要是用于执行在其他情况下会阻塞事件循环的 IO 密集型函数/方法。 例如:
~~~
    
    
~~~python
def blocking_io():
    print(f"start blocking_io at {time.strftime('%X')}")
    # Note that time.sleep() can be replaced with any blocking
    # IO-bound operation, such as file operations.
    time.sleep(1)
    print(f"blocking_io complete at {time.strftime('%X')}")

async def main():
    print(f"started main at {time.strftime('%X')}")

    await asyncio.gather(
        asyncio.to_thread(blocking_io),
        asyncio.sleep(1))

    print(f"finished main at {time.strftime('%X')}")
~~~
    
    asyncio.run(main())
    
    # Expected output:
    #
    # started main at 19:50:53
    # start blocking_io at 19:50:53
    # blocking_io complete at 19:50:54
    # finished main at 19:50:54
    

在任何协程中直接调用 `blocking_io()` 将会在调用期间阻塞事件循环，导致额外的 1 秒运行时间。 但是，通过改用 `asyncio.to_thread()`，我们可以在单独的线程中运行它从而不会阻塞事件循环。

备注

由于 [GIL](../glossary.md#term-GIL) 的存在，`asyncio.to_thread()` 通常只能被用来将 IO 密集型函数变为非阻塞的。 但是，对于会释放 GIL 的扩展模块或无此限制的替代性 Python 实现来说，`asyncio.to_thread()` 也可被用于 CPU 密集型函数。

在 3.9 版本加入.

## 跨线程调度¶

asyncio.run_coroutine_threadsafe( _coro_ , _loop_ )¶

    

~~~
向指定事件循环提交一个协程。（线程安全）

返回一个 [`concurrent.futures.Future`](concurrent.futures.md#concurrent.futures.Future "concurrent.futures.Future") 以等待来自其他 OS 线程的结果。

此函数应该从另一个 OS 线程中调用，而非事件循环运行所在线程。示例:
~~~
    
    
~~~
# Create a coroutine
coro = asyncio.sleep(1, result=3)

# Submit the coroutine to a given loop
future = asyncio.run_coroutine_threadsafe(coro, loop)

# Wait for the result with an optional timeout argument
assert future.result(timeout) == 3
~~~

如果在协程内产生了异常，将会通知返回的 Future 对象。它也可被用来取消事件循环中的任务:

    
    
~~~
try:
    result = future.result(timeout)
except TimeoutError:
    print('The coroutine took too long, cancelling the task...')
    future.cancel()
except Exception as exc:
    print(f'The coroutine raised an exception: {exc!r}')
else:
    print(f'The coroutine returned: {result!r}')
~~~

参见 [concurrency and multithreading](asyncio-dev.md#asyncio-multithreading) 部分的文档。

不同于其他 asyncio 函数，此函数要求显式地传入 _loop_ 参数。

在 3.5.1 版本加入.

## 内省¶

asyncio.current_task( _loop =None_)¶

    

~~~
返回当前运行的 `Task` 实例，如果没有正在运行的任务则返回 `None`。

如果 _loop_ 为 `None` 则会使用 [`get_running_loop()`](asyncio-eventloop.md#asyncio.get_running_loop "asyncio.get_running_loop") 获取当前事件循环。

在 3.7 版本加入.

asyncio.all_tasks( _loop =None_)¶
~~~
    

~~~
返回事件循环所运行的未完成的 `Task` 对象的集合。

如果 _loop_ 为 `None`，则会使用 [`get_running_loop()`](asyncio-eventloop.md#asyncio.get_running_loop "asyncio.get_running_loop") 获取当前事件循环。

在 3.7 版本加入.

asyncio.iscoroutine( _obj_ )¶
~~~
    

~~~
如果 _obj_ 是一个协程对象则返回 `True`。

在 3.4 版本加入.

## Task 对象¶

_class _asyncio.Task( _coro_ , _*_ , _loop =None_, _name =None_, _context =None_, _eager_start =False_)¶
~~~
    

~~~
一个与 [`Future 类似`](asyncio-future.md#asyncio.Future "asyncio.Future") 的对象，可运行 Python 协程。非线程安全。

Task 对象被用来在事件循环中运行协程。如果一个协程在等待一个 Future 对象，Task 对象会挂起该协程的执行并等待该 Future 对象完成。当该 Future 对象 _完成_ ，被打包的协程将恢复执行。

事件循环使用协同日程调度: 一个事件循环每次运行一个 Task 对象。而一个 Task 对象会等待一个 Future 对象完成，该事件循环会运行其他 Task、回调或执行 IO 操作。

使用高层级的 `asyncio.create_task()` 函数来创建 Task 对象，也可用低层级的 [`loop.create_task()`](asyncio-eventloop.md#asyncio.loop.create_task "asyncio.loop.create_task") 或 [`ensure_future()`](asyncio-future.md#asyncio.ensure_future "asyncio.ensure_future") 函数。不建议手动实例化 Task 对象。

要取消一个正在运行的 Task 对象可使用 `cancel()` 方法。调用此方法将使该 Task 对象抛出一个 [`CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 异常给打包的协程。如果取消期间一个协程正在等待一个 Future 对象，该 Future 对象也将被取消。

`cancelled()` 可被用来检测 Task 对象是否被取消。如果打包的协程没有抑制 [`CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 异常并且确实被取消，该方法将返回 `True`。

`asyncio.Task` 从 [`Future`](asyncio-future.md#asyncio.Future "asyncio.Future") 继承了其除 [`Future.set_result()`](asyncio-future.md#asyncio.Future.set_result "asyncio.Future.set_result") 和 [`Future.set_exception()`](asyncio-future.md#asyncio.Future.set_exception "asyncio.Future.set_exception") 以外的所有 API。

可选的仅限关键字参数 _context_ 允许指定自定义的 [`contextvars.Context`](contextvars.md#contextvars.Context "contextvars.Context") 供 _coro_ 运行。 如果未提供 _context_ ，Task 将拷贝当前上下文并随后在拷贝的上下文中运行其协程。

可选的仅限关键字参数 _eager_start_ 允许在任务创建时主动开始 `asyncio.Task` 的执行。 如果设为 `True` 并且事件循环正在运行，任务将立即开始执行协程，直到该协程第一次阻塞。 如果协程未发生阻塞即返回或引发异常，任务将主动结果并将跳过向事件循环添加计划任务。

在 3.7 版本发生变更: 加入对 [`contextvars`](contextvars.md#module-contextvars "contextvars: Context Variables") 模块的支持。

在 3.8 版本发生变更: 添加了 _name_ 形参。

自 3.10 版本弃用: 如果未指定 _loop_ 并且没有正在运行的事件循环则会发出弃用警告。

在 3.11 版本发生变更: 增加了 _context_ 形参。

在 3.12 版本发生变更: 增加了 _eager_start_ 形参。

done()¶
~~~
    

~~~
如果 Task 对象 _已完成_ 则返回 `True`。

当 Task 所封包的协程返回一个值、引发一个异常或 Task 本身被取消时，则会被认为 _已完成_ 。

result()¶
~~~
    

~~~
返回 Task 的结果。

如果 Task 对象 _已完成_ ，其封包的协程的结果会被返回 (或者当协程引发异常时，该异常会被重新引发。)

如果 Task 对象 _被取消_ ，此方法会引发一个 [`CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 异常。

如果 Task 对象的结果还不可用，此方法会引发一个 [`InvalidStateError`](asyncio-exceptions.md#asyncio.InvalidStateError "asyncio.InvalidStateError") 异常。

exception()¶
~~~
    

~~~
返回 Task 对象的异常。

如果所封包的协程引发了一个异常，该异常将被返回。如果所封包的协程正常返回则该方法将返回 `None`。

如果 Task 对象 _被取消_ ，此方法会引发一个 [`CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 异常。

如果 Task 对象尚未 _完成_ ，此方法将引发一个 [`InvalidStateError`](asyncio-exceptions.md#asyncio.InvalidStateError "asyncio.InvalidStateError") 异常。

add_done_callback( _callback_ , _*_ , _context =None_)¶
~~~
    

~~~
添加一个回调，将在 Task 对象 _完成_ 时被运行。

此方法应该仅在低层级的基于回调的代码中使用。

要了解更多细节请查看 [`Future.add_done_callback()`](asyncio-future.md#asyncio.Future.add_done_callback "asyncio.Future.add_done_callback") 的文档。

remove_done_callback( _callback_ )¶
~~~
    

~~~
从回调列表中移除 _callback_ 。

此方法应该仅在低层级的基于回调的代码中使用。

要了解更多细节请查看 [`Future.remove_done_callback()`](asyncio-future.md#asyncio.Future.remove_done_callback "asyncio.Future.remove_done_callback") 的文档。

get_stack( _*_ , _limit =None_)¶
~~~
    

~~~
返回此 Task 对象的栈框架列表。

如果所封包的协程未完成，这将返回其挂起所在的栈。如果协程已成功完成或被取消，这将返回一个空列表。如果协程被一个异常终止，这将返回回溯框架列表。

框架总是从按从旧到新排序。

每个被挂起的协程只返回一个栈框架。

可选的 _limit_ 参数指定返回框架的数量上限；默认返回所有框架。返回列表的顺序要看是返回一个栈还是一个回溯：栈返回最新的框架，回溯返回最旧的框架。(这与 traceback 模块的行为保持一致。)

print_stack( _*_ , _limit =None_, _file =None_)¶
~~~
    

~~~
打印此 Task 对象的栈或回溯。

此方法产生的输出类似于 traceback 模块通过 `get_stack()` 所获取的框架。

_limit_ 参数会直接传递给 `get_stack()`。

_file_ 参数是输出所写入的 I/O 流；在默认情况下输出会写入到 [`sys.stdout`](sys.md#sys.stdout "sys.stdout")。

get_coro()¶
~~~
    

~~~
返回由 `Task` 包装的协程对象。

备注

这对于已经主动完成的任务将返回 `None`。 参见 主动任务工厂。

在 3.8 版本加入.

在 3.12 版本发生变更: 新增加的主动任务执行意味着结果可能为 `None`。

get_context()¶
~~~
    

~~~
返回关联到该任务的 [`contextvars.Context`](contextvars.md#contextvars.Context "contextvars.Context") 对象。

在 3.12 版本加入.

get_name()¶
~~~
    

~~~
返回 Task 的名称。

如果没有一个 Task 名称被显式地赋值，默认的 asyncio Task 实现会在实例化期间生成一个默认名称。

在 3.8 版本加入.

set_name( _value_ )¶
~~~
    

~~~
设置 Task 的名称。

_value_ 参数可以为任意对象，它随后会被转换为字符串。

在默认的 Task 实现中，名称将在任务对象的 [`repr()`](functions.md#repr "repr") 输出中可见。

在 3.8 版本加入.

cancel( _msg =None_)¶
~~~
    

~~~
请求取消 Task 对象。

这将安排在下一轮事件循环中抛出一个 [`CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 异常给被封包的协程。

协程随后将有机会进行清理甚至通过 [`try`](../reference/compound_stmts.md#try) ... ... `except CancelledError` ... [`finally`](../reference/compound_stmts.md#finally) 代码块抑制异常来拒绝请求。 因此，不同于 [`Future.cancel()`](asyncio-future.md#asyncio.Future.cancel "asyncio.Future.cancel"), `Task.cancel()` 不保证 Task 会被取消，虽然完全抑制撤销并不常见也很不建议这样做。 但是如果协程决定要抑制撤销，那么它需要额外调用 `Task.uncancel()` 来捕获异常。

在 3.9 版本发生变更: 增加了 _msg_ 形参。

在 3.11 版本发生变更: `msg` 形参将从被取消的任务传播到其等待方。

以下示例演示了协程是如何侦听取消请求的:
~~~
    
    
~~~
async def cancel_me():
    print('cancel_me(): before sleep')

    try:
        # Wait for 1 hour
        await asyncio.sleep(3600)
    except asyncio.CancelledError:
        print('cancel_me(): cancel sleep')
        raise
    finally:
        print('cancel_me(): after sleep')

async def main():
    # Create a "cancel_me" Task
    task = asyncio.create_task(cancel_me())

    # Wait for 1 second
    await asyncio.sleep(1)

    task.cancel()
    try:
        await task
    except asyncio.CancelledError:
        print("main(): cancel_me is cancelled now")

asyncio.run(main())

# Expected output:
#
#     cancel_me(): before sleep
#     cancel_me(): cancel sleep
#     cancel_me(): after sleep
#     main(): cancel_me is cancelled now
~~~

cancelled()¶

    

~~~
如果 Task 对象 _被取消_ 则返回 `True`。

当使用 `cancel()` 发出取消请求时 Task 会被 _取消_ ，其封包的协程将传播被抛入的 [`CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 异常。

uncancel()¶
~~~
    

~~~
递减对此任务的取消请求计数。

返回剩余的取消请求数量。

请注意一理被取消的任务执行完成，继续调用 `uncancel()` 将是低效的。

在 3.11 版本加入.

此方法是供 asyncio 内部使用而不应被最终用户代码所使用。 特别地，在一个 Task 成功地保持未取消状态的时候使用，这可以允许结构化的并发元素如 任务组 和 `asyncio.timeout()` 继续运行，将取消操作隔离在相应的结构化代码块中。 例如:
~~~
    
    
~~~
async def make_request_with_timeout():
    try:
        async with asyncio.timeout(1):
            # Structured block affected by the timeout:
            await make_request()
            await make_another_request()
    except TimeoutError:
        log("There was a timeout")
    # Outer code not affected by the timeout:
    await unrelated_code()
~~~

带有 `make_request()` 和 `make_another_request()` 的代码块可能因超时而被取消，而 `unrelated_code()` 应当在超时的情况下继续运行。 这是通过 `uncancel()` 实现的。 `TaskGroup` 上下文管理器也会以类似的方式来使用 `uncancel()`。

如果最终用户代码出于某种原因通过捕获 [`CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError") 抑制撤销操作，那么它需要调用此方法来移除撤销状态。

cancelling()¶

    

~~~
