# 同步原语¶

**源代码:** [Lib/asyncio/locks.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/locks.py)

* * *

asyncio 同步原语被设计为与 [`threading`](threading.md#module-threading "threading: Thread-based parallelism.") 模块的类似，但有两个关键注意事项:

  * asyncio 原语不是线程安全的，因此它们不应被用于 OS 线程同步 (而应当使用 [`threading`](threading.md#module-threading "threading: Thread-based parallelism."))；

  * 这些同步原语的方法不接受 _timeout_ 参数；请使用 [`asyncio.wait_for()`](asyncio-task.md#asyncio.wait_for "asyncio.wait_for") 函数来执行带有超时的操作。

asyncio 具有下列基本同步原语:

  * `Lock`

  * `Event`

  * `Condition`

  * `Semaphore`

  * `BoundedSemaphore`

  * `Barrier`

* * *

## Lock¶

_class _asyncio.Lock¶

    

~~~
实现一个用于 asyncio 任务的互斥锁。 非线程安全。

asyncio 锁可被用来保证对共享资源的独占访问。

使用 Lock 的推荐方式是通过 [`async with`](../reference/compound_stmts.md#async-with) 语句:
~~~
    
    
~~~
lock = asyncio.Lock()

# ... later
async with lock:
    # access shared state
~~~

这等价于:

    
    
~~~
lock = asyncio.Lock()

# ... later
await lock.acquire()
try:
    # access shared state
finally:
    lock.release()
~~~

在 3.10 版本发生变更: 移除了 _loop_ 形参。

_coroutine _acquire()¶

    

~~~
获取锁。

此方法会等待直至锁为 _unlocked_ ，将其设为 _locked_ 并返回 `True`。

当有一个以上的协程在 `acquire()` 中被阻塞则会等待解锁，最终只有一个协程会被执行。

锁的获取是 _公平的_ : 被执行的协程将是第一个开始等待锁的协程。

release()¶
~~~
    

~~~
释放锁。

当锁为 _locked_ 时，将其设为 _unlocked_ 并返回。

如果锁为 _unlocked_ ，则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

locked()¶
~~~
    

~~~
如果锁为 _locked_ 则返回 `True`。

## Event¶

_class _asyncio.Event¶
~~~
    

~~~
事件对象。 该对象不是线程安全的。

asyncio 事件可被用来通知多个 asyncio 任务已经有事件发生。

Event 对象会管理一个内部旗标，可通过 `set()` 方法将其设为 _true_ 并通过 `clear()` 方法将其重设为 _false_ 。 `wait()` 方法会阻塞直至该旗标被设为 _true_ 。 该旗标初始时会被设为 _false_ 。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

示例:
~~~
    
    
~~~
async def waiter(event):
    print('waiting for it ...')
    await event.wait()
    print('... got it!')

async def main():
    # Create an Event object.
    event = asyncio.Event()

    # Spawn a Task to wait until 'event' is set.
    waiter_task = asyncio.create_task(waiter(event))

    # Sleep for 1 second and set the event.
    await asyncio.sleep(1)
    event.set()

    # Wait until the waiter task is finished.
    await waiter_task

asyncio.run(main())
~~~

_coroutine _wait()¶

    

~~~
等待直至事件被设置。

如果事件已被设置，则立即返回 `True`。 否则将阻塞直至另一个任务调用 `set()`。

set()¶
~~~
    

~~~
设置事件。

所有等待事件被设置的任务将被立即唤醒。

clear()¶
~~~
    

~~~
清空（取消设置）事件。

通过 `wait()` 进行等待的任务现在将会阻塞直至 `set()` 方法被再次调用。

is_set()¶
~~~
    

~~~
如果事件已被设置则返回 `True`。

## Condition¶

_class _asyncio.Condition( _lock =None_)¶
~~~
    

~~~
条件对象。 该对象不是线程安全的。

asyncio 条件原语可被任务用于等待某个事件发生，然后获取对共享资源的独占访问。

在本质上，Condition 对象合并了 `Event` 和 `Lock` 的功能。 多个 Condition 对象有可能共享一个 Lock，这允许关注于共享资源的特定状态的不同任务实现对共享资源的协同独占访问。

可选的 _lock_ 参数必须为 `Lock` 对象或 `None`。 在后一种情况下会自动创建一个新的 Lock 对象。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

使用 Condition 的推荐方式是通过 [`async with`](../reference/compound_stmts.md#async-with) 语句:
~~~
    
    
~~~
cond = asyncio.Condition()

# ... later
async with cond:
    await cond.wait()
~~~

这等价于:

    
    
~~~
cond = asyncio.Condition()

# ... later
await cond.acquire()
try:
    await cond.wait()
finally:
    cond.release()
~~~

_coroutine _acquire()¶

    

~~~
获取下层的锁。

此方法会等待直至下层的锁为 _unlocked_ ，将其设为 _locked_ 并返回 returns `True`。

notify( _n =1_)¶
~~~
    

~~~
唤醒最多 _n_ 个正在等待此条件的任务（默认为 1 个）。 如果没有任务正在等待则此方法为空操作。

锁必须在此方法被调用前被获取并在随后被快速释放。 如果通过一个 _unlocked_ 锁调用则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

locked()¶
~~~
    

~~~
如果下层的锁已被获取则返回 `True`。

notify_all()¶
~~~
    

~~~
唤醒所有正在等待此条件的任务。

此方法的行为类似于 `notify()`，但会唤醒所有正在等待的任务。

锁必须在此方法被调用前被获取并在随后被快速释放。 如果通过一个 _unlocked_ 锁调用则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

release()¶
~~~
    

~~~
释放下层的锁。

当在未锁定的锁上发起调用时，会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

_coroutine _wait()¶
~~~
    

~~~
等待直至收到通知。

当此方法被调用时如果调用方任务未获得锁，则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

这个方法会释放下层的锁，然后保持阻塞直到被 `notify()` 或 `notify_all()` 调用所唤醒。 一旦被唤醒，Condition 会重新获取它的锁并且此方法将返回 `True`。

_coroutine _wait_for( _predicate_ )¶
~~~
    

~~~
等待直到目标值变为 _true_ 。

目标必须为一个可调用对象，其结果将被解读为一个布尔值。 最终的值将为返回值。

## Semaphore¶

_class _asyncio.Semaphore( _value =1_)¶
~~~
    

~~~
信号量对象。 该对象不是线程安全的。

信号量会管理一个内部计数器，该计数器会随每次 `acquire()` 调用递减并随每次 `release()` 调用递增。 计数器的值永远不会降到零以下；当 `acquire()` 发现其值为零时，它将保持阻塞直到有某个任务调用了 `release()`。

可选的 _value_ 参数用来为内部计数器赋初始值 (默认值为 `1`)。 如果给定的值小于 `0` 则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

使用 Semaphore 的推荐方式是通过 [`async with`](../reference/compound_stmts.md#async-with) 语句。:
~~~
    
    
~~~
sem = asyncio.Semaphore(10)

# ... later
async with sem:
    # work with shared resource
~~~

这等价于:

    
    
~~~
sem = asyncio.Semaphore(10)

# ... later
await sem.acquire()
try:
    # work with shared resource
finally:
    sem.release()
~~~

_coroutine _acquire()¶

    

~~~
获取一个信号量。

如果内部计数器的值大于零，则将其减一并立即返回 `True`。 如果其值为零，则会等待直到 `release()` 并调用并返回 `True`。

locked()¶
~~~
    

~~~
如果信号量对象无法被立即获取则返回 `True`。

release()¶
~~~
    

~~~
释放一个信号量对象，将内部计数器的值加一。 可以唤醒一个正在等待获取信号量对象的任务。

不同于 `BoundedSemaphore`，`Semaphore` 允许执行的 `release()` 调用多于 `acquire()` 调用。

## BoundedSemaphore¶

_class _asyncio.BoundedSemaphore( _value =1_)¶
~~~
    

~~~
绑定的信号量对象。 该对象不是线程安全的。

BoundedSemaphore 是特殊版本的 `Semaphore`，如果在 `release()` 中内部计数器值增加到初始 _value_ 以上它将引发一个 [`ValueError`](exceptions.md#ValueError "ValueError")。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

## Barrier¶

_class _asyncio.Barrier( _parties_ )¶
~~~
    

~~~
屏障对象。 该对象不是线程安全的。

屏障是一个允许阻塞直到 _parties_ 个任务在其上等待的简单同步原语。 任务可以在 `wait()` 方法上等待并将被阻塞直到有指定数量的任务在 `wait()` 上等待。 在那时所有正在等待的任务将同时撤销阻塞。

[`async with`](../reference/compound_stmts.md#async-with) 可以被用作在 `wait()` 上等待的替代。

屏障可被重复使用任意次数。

示例:
~~~
    
    
~~~
async def example_barrier():
   # barrier with 3 parties
   b = asyncio.Barrier(3)

   # create 2 new waiting tasks
   asyncio.create_task(b.wait())
   asyncio.create_task(b.wait())

   await asyncio.sleep(0)
   print(b)

   # The third .wait() call passes the barrier
   await b.wait()
   print(b)
   print("barrier passed")

   await asyncio.sleep(0)
   print(b)

asyncio.run(example_barrier())
~~~

该示例的结果为:

    
    
~~~
<asyncio.locks.Barrier object at 0x... [filling, waiters:2/3]>
<asyncio.locks.Barrier object at 0x... [draining, waiters:0/3]>
barrier passed
<asyncio.locks.Barrier object at 0x... [filling, waiters:0/3]>
~~~

在 3.11 版本加入.

_coroutine _wait()¶

    

~~~
穿过屏障。 当屏障汇集的所有任务都调用了此函数时，它们将被同时撤销阻塞。

当该屏障中等待或阻塞的任务被取消时，此任务将退出屏障而屏障将保持相同状态。 如果屏障的状态为 "正在填充"，则等待的任务数量将减 1。

返回值是一个 0 到 `parties-1` 之间的整数，对于每个任务来说各不相同。 这可被用来选择一个任务以执行某些特别的操作。 例如:
~~~
    
    
~~~
...
async with barrier as position:
   if position == 0:
      # Only one task prints this
      print('End of *draining phase*')
~~~

如果屏障在有任务在等待时已被破坏或重置则此方法可能会引发 `BrokenBarrierError`。 如果有任务被取消则它可能会引发 [`CancelledError`](asyncio-exceptions.md#asyncio.CancelledError "asyncio.CancelledError")。

_coroutine _reset()¶

    

~~~
将屏障返回为默认的空白状态。 任何正在其上等待的任务将会接收到 `BrokenBarrierError` 异常。

如果屏障已被破坏则最好的是让其保持原状并创建一个新的屏障。

_coroutine _abort()¶
~~~
    

~~~
使屏障处于已破坏状态。 这会导致任何现有和未来对 [`wait()`](asyncio-task.md#asyncio.wait "asyncio.wait") 的调用失败并引发 `BrokenBarrierError`。 例如可以在需要中止某个任务时使用此方法，以避免任务无限等待。

parties¶
~~~
    

~~~
请求穿过该屏障的任务的数量。

n_waiting¶
~~~
    

~~~
当执行填充时正在屏障中等待的任务的数量。

broken¶
~~~
    

~~~
一个布尔值，值为 `True` 表明栅栏为破损态。

_exception _asyncio.BrokenBarrierError¶
~~~
    

~~~
