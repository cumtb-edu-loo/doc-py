# 异常¶

**源代码:** [Lib/asyncio/exceptions.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/exceptions.py)

* * *

_exception _asyncio.TimeoutError¶

    

~~~
[`TimeoutError`](exceptions.md#TimeoutError "TimeoutError") 的一个已被弃用的别名，会在操作超出了给定的时限时引发。

在 3.11 版本发生变更: 这个类是 [`TimeoutError`](exceptions.md#TimeoutError "TimeoutError") 的别名。

_exception _asyncio.CancelledError¶
~~~
    

~~~
该操作已被取消。

取消asyncio任务时，可以捕获此异常以执行自定义操作。在几乎所有情况下，都必须重新引发异常。

在 3.8 版本发生变更: `CancelledError` 现在是 [`BaseException`](exceptions.md#BaseException "BaseException") 的子类而不是 [`Exception`](exceptions.md#Exception "Exception") 的子类。

_exception _asyncio.InvalidStateError¶
~~~
    

~~~
[`Task`](asyncio-task.md#asyncio.Task "asyncio.Task") 或 [`Future`](asyncio-future.md#asyncio.Future "asyncio.Future") 的内部状态无效。

在为已设置结果值的未来对象设置结果值等情况下，可以引发此问题。

_exception _asyncio.SendfileNotAvailableError¶
~~~
    

~~~
"sendfile" 系统调用不适用于给定的套接字或文件类型。

子类 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 。

_exception _asyncio.IncompleteReadError¶
~~~
    

~~~
请求的读取操作未完全完成。

由 [asyncio stream APIs](asyncio-stream.md#asyncio-streams) 提出

此异常是 [`EOFError`](exceptions.md#EOFError "EOFError") 的子类。

expected¶
~~~
    

~~~
预期字节的总数（ [`int`](functions.md#int "int") ）。

partial¶
~~~
    

~~~
到达流结束之前读取的 [`bytes`](stdtypes.md#bytes "bytes") 字符串。

_exception _asyncio.LimitOverrunError¶
~~~
    

~~~
在查找分隔符时达到缓冲区大小限制。

由 [asyncio stream APIs](asyncio-stream.md#asyncio-streams) 提出

consumed¶
~~~
    

~~~
