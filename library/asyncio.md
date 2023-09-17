# `asyncio` \--- 异步 I/O¶

* * *

Hello World!

    
    
~~~
import asyncio

async def main():
    print('Hello ...')
    await asyncio.sleep(1)
    print('... World!')

asyncio.run(main())
~~~

asyncio 是用来编写 **并发** 代码的库，使用 **async/await** 语法。

asyncio 被用作多个提供高性能 Python 异步框架的基础，包括网络和网站服务，数据库连接库，分布式任务队列等等。

asyncio 往往是构建 IO 密集型和高层级 **结构化** 网络代码的最佳选择。

asyncio 提供一组 **高层级** API 用于:

  * 并发地 [运行 Python 协程](asyncio-task.md#coroutine) 并对其执行过程实现完全控制;

  * 执行 [网络 IO 和 IPC](asyncio-stream.md#asyncio-streams);

  * 控制 [子进程](asyncio-subprocess.md#asyncio-subprocess);

  * 通过 [队列](asyncio-queue.md#asyncio-queues) 实现分布式任务;

  * [同步](asyncio-sync.md#asyncio-sync) 并发代码;

此外，还有一些 **低层级** API 以支持 _库和框架的开发者_ 实现:

  * 创建和管理 [事件循环](asyncio-eventloop.md#asyncio-event-loop)，以提供异步 API 用于 [`网络化`](asyncio-eventloop.md#asyncio.loop.create_server "asyncio.loop.create_server"), 运行 [`子进程`](asyncio-eventloop.md#asyncio.loop.subprocess_exec "asyncio.loop.subprocess_exec")，处理 [`OS 信号`](asyncio-eventloop.md#asyncio.loop.add_signal_handler "asyncio.loop.add_signal_handler") 等等;

  * 使用 [transports](asyncio-protocol.md#asyncio-transports-protocols) 实现高效率协议;

  * 通过 async/await 语法 [桥接](asyncio-future.md#asyncio-futures) 基于回调的库和代码。

你可以在 REPL 中尝试使用 `asyncio` 并发上下文:

    
    
~~~
$ python -m asyncio
asyncio REPL ...
Use "await" directly instead of "asyncio.run()".
Type "help", "copyright", "credits" or "license" for more information.
>>> import asyncio
>>> await asyncio.sleep(10, result='hello')
'hello'
~~~

[可用性](intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

参考

高层级 API

  * [运行器](asyncio-runner.md)
  * [协程与任务](asyncio-task.md)
  * [流](asyncio-stream.md)
  * [同步原语](asyncio-sync.md)
  * [子进程集](asyncio-subprocess.md)
  * [队列集](asyncio-queue.md)
  * [异常](asyncio-exceptions.md)

低层级 API

  * [事件循环](asyncio-eventloop.md)
  * [Futures](asyncio-future.md)
  * [传输和协议](asyncio-protocol.md)
  * [策略](asyncio-policy.md)
  * [平台支持](asyncio-platforms.md)
  * [扩展](asyncio-extending.md)

指南与教程

  * [高层级 API 索引](asyncio-api-index.md)
  * [低层级 API 索引](asyncio-llapi-index.md)
  * [用 asyncio 开发](asyncio-dev.md)

备注

asyncio 的源代码可以在 [Lib/asyncio/](https://github.com/python/cpython/tree/3.12/Lib/asyncio/) 中找到。

