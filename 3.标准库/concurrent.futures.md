# `concurrent.futures` \--- 启动并行任务¶

在 3.2 版本加入.

**源码:** [Lib/concurrent/futures/thread.py](https://github.com/python/cpython/tree/3.12/Lib/concurrent/futures/thread.py) 和 [Lib/concurrent/futures/process.py](https://github.com/python/cpython/tree/3.12/Lib/concurrent/futures/process.py)

* * *

`concurrent.futures` 模块提供异步执行可调用对象高层接口。

异步执行可以由 `ThreadPoolExecutor` 使用线程或由 `ProcessPoolExecutor` 使用单独的进程来实现。 两者都是实现抽象类 `Executor` 定义的接口。

[可用性](3.标准库/intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](3.标准库/intro.md#wasm-availability) 了解详情。

## Executor 对象¶

_class _concurrent.futures.Executor¶

    

~~~
抽象类提供异步执行调用方法。要通过它的子类调用，而不是直接调用。

> submit( _fn_ , _/_ , _* args_, _** kwargs_)¶
>  
>
> 调度可调用对象 _fn_ ，以 `fn(*args, **kwargs)` 方式执行并返回一个代表该可调用对象的执行的 `Future` 对象。
>  
>  
>     with ThreadPoolExecutor(max_workers=1) as executor:
>         future = executor.submit(pow, 323, 1235)
>         print(future.result())
>  
>
> map( _func_ , _* iterables_, _timeout =None_, _chunksize =1_)¶
>  
>
> 类似于 [`map(func, *iterables)`](functions.md#map "map") 函数，除了以下两点：
>
>   * _iterables_ 是立即执行而不是延迟执行的；
>
>   * _func_ 是异步执行的，对 _func_ 的多个调用可以并发执行。
>
>

>
> 如果 [`__next__()`](stdtypes.md#iterator.__next__ "iterator.__next__") 被调用且从对 `Executor.map()` 原始调用 _timeout_ 秒之后其结果还不可用则已返回的迭代器将引发 [`TimeoutError`](exceptions.md#TimeoutError "TimeoutError")。 _timeout_ 可以是整数或浮点数。 如果 _timeout_ 未指定或为 `None`，则不限制等待时间。
>
> 如果 _func_ 调用引发一个异常，当从迭代器中取回它的值时这个异常将被引发。
>
> 使用 `ProcessPoolExecutor` 时，这个方法会将 _iterables_ 分割任务块并作为独立的任务并提交到执行池中。这些块的大概数量可以由 _chunksize_ 指定正整数设置。 对很长的迭代器来说，使用大的 _chunksize_ 值比默认值 1 能显著地提高性能。 _chunksize_ 对 `ThreadPoolExecutor` 没有效果。
>
> 在 3.5 版本发生变更: 加入 _chunksize_ 参数。
>
> shutdown( _wait =True_, _*_ , _cancel_futures =False_)¶
>  
>
> 当待执行的 future 对象完成执行后向执行者发送信号，它就会释放正在使用的任何资源。 在关闭后调用 `Executor.submit()` 和 `Executor.map()` 将会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。
>
> 如果 _wait_ 为 `True` 则此方法只有在所有待执行的 future 对象完成执行且释放已分配的资源后才会返回。 如果 _wait_ 为 `False`，方法立即返回，所有待执行的 future 对象完成执行后会释放已分配的资源。 不管 _wait_ 的值是什么，整个 Python 程序将等到所有待执行的 future 对象完成执行后才退出。
>
> 如果 _cancel_futures_ 为 `True`，此方法将取消所有执行器还未开始运行的挂起的 Future。无论 _cancel_futures_ 的值是什么，任何已完成或正在运行的 Future 都不会被取消。
>
> 如果 _cancel_futures_ 和 _wait_ 均为 `True`，则执行器已开始运行的所有 Future 将在此方法返回之前完成。 其余的 Future 会被取消。
>
> 如果使用 [`with`](../reference/compound_stmts.md#with) 语句，你就可以避免显式调用这个方法，它将会停止 `Executor` (就好像 `Executor.shutdown()` 调用时 _wait_ 设为 `True` 一样等待):
>  
>  
>     import shutil
>     with ThreadPoolExecutor(max_workers=4) as e:
>         e.submit(shutil.copy, 'src1.txt', 'dest1.txt')
>         e.submit(shutil.copy, 'src2.txt', 'dest2.txt')
>         e.submit(shutil.copy, 'src3.txt', 'dest3.txt')
>         e.submit(shutil.copy, 'src4.txt', 'dest4.txt')
>  
>
> 在 3.9 版本发生变更: 增加了 _cancel_futures_ 。

## ThreadPoolExecutor¶

`ThreadPoolExecutor` 是 `Executor` 的子类，它使用线程池来异步执行调用。

当可调用对象已关联了一个 `Future` 然后在等待另一个 `Future` 的结果时就会导致死锁情况。例如:
~~~
    
    
~~~
import time
def wait_on_b():
    time.sleep(5)
    print(b.result())  # b will never complete because it is waiting on a.
    return 5

def wait_on_a():
    time.sleep(5)
    print(a.result())  # a will never complete because it is waiting on b.
    return 6
~~~
    
    executor = ThreadPoolExecutor(max_workers=2)
    a = executor.submit(wait_on_b)
    b = executor.submit(wait_on_a)
    

与:

    
    
~~~python
def wait_on_future():
    f = executor.submit(pow, 5, 2)
    # This will never complete because there is only one worker thread and
    # it is executing this function.
    print(f.result())

executor = ThreadPoolExecutor(max_workers=1)
executor.submit(wait_on_future)
~~~

_class _concurrent.futures.ThreadPoolExecutor( _max_workers =None_, _thread_name_prefix =''_, _initializer =None_, _initargs =()_)¶

    

~~~
`Executor` 子类使用最多 _max_workers_ 个线程的线程池来异步执行调用。

所有排入 `ThreadPoolExecutor` 的队列的线程将在解释器退出之前被合并。 请注意执行此操作的退出处理句柄会在在任何使用 `atexit` 添加的退出处理句柄 _之前_ 被执行。 这意味着主线程中的异常必须被捕获和处理以便向线程发出信号使其能够优雅地退出。 由于这个原理，建议不要将 `ThreadPoolExecutor` 用于长期运行的任务。

_initializer_ 是在每个工作者线程开始处调用的一个可选可调用对象。 _initargs_ 是传递给初始化器的元组参数。任何向池提交更多工作的尝试， _initializer_ 都将引发一个异常，当前所有等待的工作都会引发一个 `BrokenThreadPool`。

在 3.5 版本发生变更: 如果 _max_workers_ 为 `None` 或没有指定，将默认为机器处理器的个数，假如 `ThreadPoolExecutor` 侧重于I/O操作而不是CPU运算，那么可以乘以 `5` ，同时工作线程的数量可以比 `ProcessPoolExecutor` 的数量高。

在 3.6 版本加入: 添加 _thread_name_prefix_ 参数允许用户控制由线程池创建的 [`threading.Thread`](threading.md#threading.Thread "threading.Thread") 工作线程名称以方便调试。

在 3.7 版本发生变更: 加入 _initializer_ 和*initargs* 参数。

在 3.8 版本发生变更: _max_workers_ 的默认值已改为 `min(32, os.cpu_count() + 4)`。 这个默认值会保留至少 5 个工作线程用于 I/O 密集型任务。 对于那些释放了 GIL 的 CPU 密集型任务，它最多会使用 32 个 CPU 核心。这样能够避免在多核机器上不知不觉地使用大量资源。

现在 ThreadPoolExecutor 在启动 _max_workers_ 个工作线程之前也会重用空闲的工作线程。

### ThreadPoolExecutor 例子¶
~~~
    
    
~~~
import concurrent.futures
import urllib.request

URLS = ['http://www.foxnews.com/',
        'http://www.cnn.com/',
        'http://europe.wsj.com/',
        'http://www.bbc.co.uk/',
        'http://nonexistant-subdomain.python.org/']

# Retrieve a single page and report the URL and contents
def load_url(url, timeout):
    with urllib.request.urlopen(url, timeout=timeout) as conn:
        return conn.read()

# We can use a with statement to ensure threads are cleaned up promptly
with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
    # Start the load operations and mark each future with its URL
    future_to_url = {executor.submit(load_url, url, 60): url for url in URLS}
    for future in concurrent.futures.as_completed(future_to_url):
        url = future_to_url[future]
        try:
            data = future.result()
        except Exception as exc:
            print('%r generated an exception: %s' % (url, exc))
        else:
            print('%r page is %d bytes' % (url, len(data)))
~~~

## ProcessPoolExecutor¶

`ProcessPoolExecutor` 类是 `Executor` 的子类，它使用进程池来异步地执行调用。 `ProcessPoolExecutor` 会使用 [`multiprocessing`](multiprocessing.md#module-multiprocessing "multiprocessing: Process-based parallelism.") 模块，这允许它绕过 [全局解释器锁](../glossary.md#term-global-interpreter-lock) 但也意味着只可以处理和返回可封存的对象。

`__main__` 模块必须可以被工作者子进程导入。这意味着 `ProcessPoolExecutor` 不可以工作在交互式解释器中。

从可调用对象中调用 `Executor` 或 `Future` 的方法提交给 `ProcessPoolExecutor` 会导致死锁。

_class _concurrent.futures.ProcessPoolExecutor( _max_workers =None_, _mp_context =None_, _initializer =None_, _initargs =()_, _max_tasks_per_child =None_)¶

    

~~~
异步地执行调用的 `Executor` 子类使用最多 _max_workers_ 个进程的进程池。 如果 _max_workers_ 为 `None` 或未给出，它将默认为机器的处理器个数。 如果 _max_workers_ 小于等于 `0`，则将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 在 Windows 上， _max_workers_ 必须小于等于 `61`。 如果不是这样则将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 如果 _max_workers_ 为 `None`，则选择的默认值最多为 `61`，即使存在更多的处理器。 _mp_context_ 可以是一个 [`multiprocessing`](multiprocessing.md#module-multiprocessing "multiprocessing: Process-based parallelism.") 上下文或是 `None`。 它将被用来启动工作进程。 如果 _mp_context_ 为 `None` 或未给出，则将使用默认的 [`multiprocessing`](multiprocessing.md#module-multiprocessing "multiprocessing: Process-based parallelism.") 上下文。 参见 [上下文和启动方法](multiprocessing.md#multiprocessing-start-methods)。

_initializer_ 是一个可选的可调用对象，它会在每个工作进程启动时被调用； _initargs_ 是传给 initializer 的参数元组。 如果 _initializer_ 引发了异常，则所有当前在等待的任务以及任何向进程池提交更多任务的尝试都将引发 `BrokenProcessPool`。

_max_tasks_per_child_ 是指定单个进程在其退出并替换为新工作进程之前可以执行的最大任务数量的可选参数。 在默认情况下 _max_tasks_per_child_ 为 `None` 表示工作进程将存活与进程池一样长的时间。 当指定了最大数量时，则如果不存在 _mp_context_ 形参则将默认使用 "spawn" 多进程启动方法。 此特性不能兼容 "fork" 启动方法。

在 3.3 版本发生变更: 如果其中一个工作进程被突然终止，`BrokenProcessPool` 就会马上触发。 可预计的行为没有定义，但执行器上的操作或它的 future 对象会被冻结或死锁。

在 3.7 版本发生变更: 添加 _mp_context_ 参数允许用户控制由进程池创建给工作者进程的开始方法 。

加入 _initializer_ 和*initargs* 参数。

备注

在 Python 3.14 中默认的 [`multiprocessing`](multiprocessing.md#module-multiprocessing "multiprocessing: Process-based parallelism.") 启动方法 (参见 [上下文和启动方法](multiprocessing.md#multiprocessing-start-methods)) 将改为不再使用 _fork_ 。 需要为其 `ProcessPoolExecutor` 使用 _fork_ 的代码应当通过传入 `mp_context=multiprocessing.get_context("fork")` 形参来显式地指明这一点。

在 3.11 版本发生变更: 增加了 _max_tasks_per_child_ 参数以允许用户控制进程池中工作进程的生命期。

### ProcessPoolExecutor 例子¶
~~~
    
    
~~~
import concurrent.futures
import math

PRIMES = [
    112272535095293,
    112582705942171,
    112272535095293,
    115280095190773,
    115797848077099,
    1099726899285419]

def is_prime(n):
    if n < 2:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False

    sqrt_n = int(math.floor(math.sqrt(n)))
    for i in range(3, sqrt_n + 1, 2):
        if n % i == 0:
            return False
    return True

def main():
    with concurrent.futures.ProcessPoolExecutor() as executor:
        for number, prime in zip(PRIMES, executor.map(is_prime, PRIMES)):
            print('%d is prime: %s' % (number, prime))

if __name__ == '__main__':
    main()
~~~

## Future 对象¶

`Future` 类将可调用对象封装为异步执行。`Future` 实例由 `Executor.submit()` 创建。

_class _concurrent.futures.Future¶

    

~~~
将可调用对象封装为异步执行。`Future` 实例由 `Executor.submit()` 创建，除非测试，不应直接创建。

> cancel()¶
>  
>
> 尝试取消调用。 如果调用正在执行或已结束运行不能被取消则该方法将返回 `False`，否则调用会被取消并且该方法将返回 `True`。
>
> cancelled()¶
>  
>
> 如果调用成功取消返回 `True`。
>
> running()¶
>  
>
> 如果调用正在执行而且不能被取消那么返回 `True` 。
>
> done()¶
>  
>
> 如果调用已被取消或正常结束那么返回 `True`。
>
> result( _timeout =None_)¶
>  
>
> 返回调用所返回的值。 如果调用尚未完成则此方法将等待至多 _timeout_ 秒。 如果调用在 _timeout_ 秒内仍未完成，则将引发 [`TimeoutError`](exceptions.md#TimeoutError "TimeoutError")。 _timeout_ 可以为整数或浮点数。 如果 _timeout_ 未指定或为 `None`，则不限制等待时间。
>
> 如果 future 在完成前被取消则 `CancelledError` 将被触发。
>
> 如果调用引发了一个异常，这个方法也会引发同样的异常。
>
> exception( _timeout =None_)¶
>  
>
> 返回调用所引发的异常。 如果调用尚未完成则此方法将等待至多 _timeout_ 秒。 如果调用在 _timeout_ 秒内仍未完成，则将引发 [`TimeoutError`](exceptions.md#TimeoutError "TimeoutError")。 _timeout_ 可以为整数或浮点数。 如果 _timeout_ 未指定或为 `None`，则不限制等待时间。
>
> 如果 future 在完成前被取消则 `CancelledError` 将被触发。
>
> 如果调用正常完成那么返回 `None`。
>
> add_done_callback( _fn_ )¶
>  
>
> 附加可调用 _fn_ 到 future 对象。当 future 对象被取消或完成运行时，将会调用 _fn_ ，而这个 future 对象将作为它唯一的参数。
>
> 加入的可调用对象总被属于添加它们的进程中的线程按加入的顺序调用。如果可调用对象引发一个 [`Exception`](exceptions.md#Exception "Exception") 子类，它会被记录下来并被忽略掉。如果可调用对象引发一个 [`BaseException`](exceptions.md#BaseException "BaseException") 子类，这个行为没有定义。
>
> 如果 future 对象已经完成或已取消， _fn_ 会被立即调用。

下面这些 `Future` 方法用于单元测试和 `Executor` 实现。

> set_running_or_notify_cancel()¶
>  
>
> 这个方法只可以在执行关联 `Future` 工作之前由 `Executor` 实现调用或由单测试调用。
>
> 如果此方法返回 `False` 则 `Future` 已被取消，即 `Future.cancel()` 已被调用并返回 `True`。 任何等待 `Future` 完成 (即通过 `as_completed()` 或 `wait()`) 的线程将被唤醒。
>
> 如果此方法返回 `True` 则 `Future` 没有被取消并已被置为正在运行的状态，即对 `Future.running()` 的调用将返回 `True`。
>
> 这个方法只可以被调用一次并且不能在调用 `Future.set_result()` 或 `Future.set_exception()` 之后再调用。
>
> set_result( _result_ )¶
>  
>
> 设置将 `Future` 关联工作的结果给 _result_ 。
>
> 这个方法只可以由 `Executor` 实现和单元测试使用。
>
> 在 3.8 版本发生变更: 如果 `Future` 已经完成则此方法会引发 `concurrent.futures.InvalidStateError`。
>
> set_exception( _exception_ )¶
>  
>
> 设置 `Future` 关联工作的结果给 [`Exception`](exceptions.md#Exception "Exception") _exception_ 。
>
> 这个方法只可以由 `Executor` 实现和单元测试使用。
>
> 在 3.8 版本发生变更: 如果 `Future` 已经完成则此方法会引发 `concurrent.futures.InvalidStateError`。

## 模块函数¶

concurrent.futures.wait( _fs_ , _timeout =None_, _return_when =ALL_COMPLETED_)¶
~~~
    

~~~
等待由 _fs_ 指定的 `Future` 实例（可能由不同的 `Executor` 实例创建）完成。 重复传给 _fs_ 的 future 会被移除并将只返回一次。 返回一个由集合组成的具名 2 元组。 第一个集合的名称为 `done`，包含在等待完成之前已完成的 future（包括正常结束或被取消的 future）。 第二个集合的名称为 `not_done`，包含未完成的 future（包括挂起的或正在运行的 future）。

_timeout_ 可以用来控制返回前最大的等待秒数。 _timeout_ 可以为 int 或 float 类型。 如果 _timeout_ 未指定或为 `None` ，则不限制等待时间。

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
  
concurrent.futures.as_completed( _fs_ , _timeout =None_)¶
~~~
    

~~~
返回一个包含 _fs_ 所给出的 `Future` 实例（可能由不同的 `Executor` 实例创建）的迭代器，这些实例会在完成时产生 future 对象（包括正常结束或被取消的 future 对象）。 任何由 _fs_ 给出的重复的 future 对象将只被返回一次。 任何在 `as_completed()` 被调用之前完成的 future 对象将优先被产生。 如果 [`__next__()`](stdtypes.md#iterator.__next__ "iterator.__next__") 被调用并且在对 `as_completed()` 的原始调用 _timeout_ 秒之后其结果仍不可用则所返回的迭代器将引发 [`TimeoutError`](exceptions.md#TimeoutError "TimeoutError")。 _timeout_ 可以为整数或浮点数。 如果 _timeout_ 未指定或为 `None`，则不限制等待时间。

参见

[**PEP 3148**](https://peps.python.org/pep-3148/) \-- future 对象 - 异步执行指令。
~~~
    

~~~
该提案描述了Python标准库中包含的这个特性。

## Exception 类¶

_exception _concurrent.futures.CancelledError¶
~~~
    

~~~
future 对象被取消时会触发。

_exception _concurrent.futures.TimeoutError¶
~~~
    

~~~
[`TimeoutError`](exceptions.md#TimeoutError "TimeoutError") 的一个已被弃用的别名，会在 future 操作超出了给定的时限时被引发。

在 3.11 版本发生变更: 这个类是 [`TimeoutError`](exceptions.md#TimeoutError "TimeoutError") 的别名。

_exception _concurrent.futures.BrokenExecutor¶
~~~
    

~~~
当执行器被某些原因中断而且不能用来提交或执行新任务时就会被引发派生于 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 的异常类。

在 3.7 版本加入.

_exception _concurrent.futures.InvalidStateError¶
~~~
    

~~~
当某个操作在一个当前状态所不允许的 future 上执行时将被引发。

在 3.8 版本加入.

_exception _concurrent.futures.thread.BrokenThreadPool¶
~~~
    

~~~
当 `ThreadPoolExecutor` 中的其中一个工作者初始化失败时会引发派生于 `BrokenExecutor` 的异常类。

在 3.7 版本加入.

_exception _concurrent.futures.process.BrokenProcessPool¶
~~~
    

~~~
