# 高层级 API 索引¶

这个页面列举了所有能用于 async/wait 的高层级asyncio API 集。

## 任务¶

运行异步程序，创建Task对象，等待多件事运行超时的公共集。

[`run()`](asyncio-runner.md#asyncio.run "asyncio.run")

|

创建事件循环，运行一个协程，关闭事件循环。  
  
---|---  
  
[`Runner`](asyncio-runner.md#asyncio.Runner "asyncio.Runner")

|

一个能够简化多次异步函数调用操作的上下文管理器。  
  
[`Task`](asyncio-task.md#asyncio.Task "asyncio.Task")

|

Task对象  
  
[`TaskGroup`](asyncio-task.md#asyncio.TaskGroup "asyncio.TaskGroup")

|

持有一组任务的上下文管理器。 它提供了一种等待分组中所有任务完成的方便可靠的方式。  
  
[`create_task()`](asyncio-task.md#asyncio.create_task "asyncio.create_task")

|

启动一个异步 Task，然后将其返回。  
  
[`current_task()`](asyncio-task.md#asyncio.current_task "asyncio.current_task")

|

返回当前Task对象  
  
[`all_tasks()`](asyncio-task.md#asyncio.all_tasks "asyncio.all_tasks")

|

返回一个事件循环的所有尚未完成的任务。  
  
`await` [`sleep()`](asyncio-task.md#asyncio.sleep "asyncio.sleep")

|

休眠几秒。  
  
`await` [`gather()`](asyncio-task.md#asyncio.gather "asyncio.gather")

|

并发执行所有事件的调度和等待。  
  
`await` [`wait_for()`](asyncio-task.md#asyncio.wait_for "asyncio.wait_for")

|

有超时控制的运行。  
  
`await` [`shield()`](asyncio-task.md#asyncio.shield "asyncio.shield")

|

屏蔽取消操作  
  
`await` [`wait()`](asyncio-task.md#asyncio.wait "asyncio.wait")

|

完成情况的监控器  
  
[`timeout()`](asyncio-task.md#asyncio.timeout "asyncio.timeout")

|

设置超时运行。 在 `wait_for` 不适合的情况下会很有用。  
  
[`to_thread()`](asyncio-task.md#asyncio.to_thread "asyncio.to_thread")

|

在不同的 OS 线程中异步地运行一个函数。  
  
[`run_coroutine_threadsafe()`](asyncio-task.md#asyncio.run_coroutine_threadsafe "asyncio.run_coroutine_threadsafe")

|

从其他OS线程中调度一个协程。  
  
`for in` [`as_completed()`](asyncio-task.md#asyncio.as_completed "asyncio.as_completed")

|

用 `for` 循环监控完成情况。  
  
例子

  * [使用 asyncio.gather() 并行运行](asyncio-task.md#asyncio-example-gather).

  * [使用 asyncio.wait_for() 强制超时](asyncio-task.md#asyncio-example-waitfor).

  * [撤销协程](asyncio-task.md#asyncio-example-task-cancel).

  * [asyncio.sleep() 的用法](asyncio-task.md#asyncio-example-sleep).

  * 请主要参阅 [协程与任务文档](asyncio-task.md#coroutine).

## 队列集¶

队列集被用于多个异步Task对象的运行调度，实现连接池以及发布/订阅模式。

[`Queue`](asyncio-queue.md#asyncio.Queue "asyncio.Queue")

|

先进先出队列  
  
---|---  
  
[`PriorityQueue`](asyncio-queue.md#asyncio.PriorityQueue "asyncio.PriorityQueue")

|

优先级队列。  
  
[`LifoQueue`](asyncio-queue.md#asyncio.LifoQueue "asyncio.LifoQueue")

|

后进先出队列。  
  
例子

  * [使用 asyncio.Queue 在多个并发任务间分配工作量](asyncio-queue.md#asyncio-example-queue-dist).

  * 请参阅 [队列集文档](asyncio-queue.md#asyncio-queues).

## 子进程集¶

用于生成子进程和运行shell命令的工具包。

`await` [`create_subprocess_exec()`](asyncio-subprocess.md#asyncio.create_subprocess_exec "asyncio.create_subprocess_exec")

|

创建一个子进程。  
  
---|---  
  
`await` [`create_subprocess_shell()`](asyncio-subprocess.md#asyncio.create_subprocess_shell "asyncio.create_subprocess_shell")

|

运行一个shell命令。  
  
例子

  * [执行一个shell命令](asyncio-subprocess.md#asyncio-example-subprocess-shell).

  * 请参阅 [子进程 APIs](asyncio-subprocess.md#asyncio-subprocess) 相关文档.

## 流¶

用于网络IO处理的高级API集。

`await` [`open_connection()`](asyncio-stream.md#asyncio.open_connection "asyncio.open_connection")

|

建立一个TCP连接。  
  
---|---  
  
`await` [`open_unix_connection()`](asyncio-stream.md#asyncio.open_unix_connection "asyncio.open_unix_connection")

|

建立一个Unix socket连接。  
  
`await` [`start_server()`](asyncio-stream.md#asyncio.start_server "asyncio.start_server")

|

启动TCP服务。  
  
`await` [`start_unix_server()`](asyncio-stream.md#asyncio.start_unix_server "asyncio.start_unix_server")

|

启动一个 Unix 套接字服务。  
  
[`StreamReader`](asyncio-stream.md#asyncio.StreamReader "asyncio.StreamReader")

|

接收网络数据的高级async/await对象。  
  
[`StreamWriter`](asyncio-stream.md#asyncio.StreamWriter "asyncio.StreamWriter")

|

发送网络数据的高级async/await对象。  
  
例子

  * [TCP 客户端样例](asyncio-stream.md#asyncio-example-stream).

  * 请参阅 [streams APIs](asyncio-stream.md#asyncio-streams) 文档。

## 同步¶

能被用于Task对象集的，类似线程的同步基元组件。

[`Lock`](asyncio-sync.md#asyncio.Lock "asyncio.Lock")

|

互斥锁。  
  
---|---  
  
[`Event`](asyncio-sync.md#asyncio.Event "asyncio.Event")

|

事件对象。  
  
[`Condition`](asyncio-sync.md#asyncio.Condition "asyncio.Condition")

|

条件对象  
  
[`Semaphore`](asyncio-sync.md#asyncio.Semaphore "asyncio.Semaphore")

|

信号量  
  
[`BoundedSemaphore`](asyncio-sync.md#asyncio.BoundedSemaphore "asyncio.BoundedSemaphore")

|

有界的信号量。  
  
[`Barrier`](asyncio-sync.md#asyncio.Barrier "asyncio.Barrier")

|

一个 Barrier 对象。  
  
例子

  * [asyncio.Event 的用法](asyncio-sync.md#asyncio-example-sync-event).

  * [使用 asyncio.Barrier](asyncio-sync.md#asyncio-example-barrier)。

  * 请参阅asyncio文档 [synchronization primitives](asyncio-sync.md#asyncio-sync).

## 异常¶

[`asyncio.CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError")

|

当一个Task对象被取消的时候被引发。请参阅 [`Task.cancel()`](asyncio-task.md#asyncio.Task.cancel "asyncio.Task.cancel")。  
  
---|---  
  
[`asyncio.BrokenBarrierError`](asyncio-sync.md#asyncio.BrokenBarrierError "asyncio.BrokenBarrierError")

|

当一个 Barrier 对象被破坏时引发。 另请参阅 [`Barrier.wait()`](asyncio-sync.md#asyncio.Barrier.wait "asyncio.Barrier.wait")。  
  
例子

  * [在取消请求发生的运行代码中如何处理CancelledError异常](asyncio-task.md#asyncio-example-task-cancel).

  * 请参阅完整的 [asyncio 专用异常](asyncio-exceptions.md#asyncio-exceptions) 列表.

