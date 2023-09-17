# `queue` \--- 一个同步的队列类¶

**源代码:** [Lib/queue.py](https://github.com/python/cpython/tree/3.12/Lib/queue.py)

* * *

`queue` 模块实现了多生产者、多消费者队列。这特别适用于消息必须安全地在多线程间交换的线程编程。模块中的 `Queue` 类实现了所有所需的锁定语义。

本模块实现了三种类型的队列，它们的区别仅仅是条目的提取顺序。 在 FIFO 队列中，先添加的任务会先被提取。 在 LIFO 队列中，最近添加的条目会先被提取 (类似于一个栈)。 在优先级队列中，条目将保持已排序状态 (使用 [`heapq`](heapq.md#module-heapq "heapq: Heap queue algorithm \(a.k.a. priority queue\).") 模块) 并且值最小的条目会先被提取。

在内部，这三个类型的队列使用锁来临时阻塞竞争线程；然而，它们并未被设计用于线程的重入性处理。

此外，模块实现了一个 "简单的" FIFO 队列类型， `SimpleQueue` ，这个特殊实现为小功能在交换中提供额外的保障。

`queue` 模块定义了下列类和异常：

_class _queue.Queue( _maxsize =0_)¶

    

~~~
Constructor for a FIFO queue. _maxsize_ is an integer that sets the upperbound limit on the number of items that can be placed in the queue. Insertion will block once this size has been reached, until queue items are consumed. If _maxsize_ is less than or equal to zero, the queue size is infinite.

_class _queue.LifoQueue( _maxsize =0_)¶
~~~
    

~~~
LIFO 队列构造函数。 _maxsize_ 是个整数，用于设置可以放入队列中的项目数的上限。当达到这个大小的时候，插入操作将阻塞至队列中的项目被消费掉。如果 _maxsize_ 小于等于零，队列尺寸为无限大。

_class _queue.PriorityQueue( _maxsize =0_)¶
~~~
    

~~~
优先级队列构造函数。 _maxsize_ 是个整数，用于设置可以放入队列中的项目数的上限。当达到这个大小的时候，插入操作将阻塞至队列中的项目被消费掉。如果 _maxsize_ 小于等于零，队列尺寸为无限大。

值最小的条目会先被提取 (值最小的条目是由the lowest valued entry is the one that would be returned by `min(entries)` 返回的)。 条目的典型模式是如下形式的元组: `(priority_number, data)`。

如果 _data_ 元素没有可比性，数据将被包装在一个类中，忽略数据值，仅仅比较优先级数字 ：
~~~
    
    
~~~
from dataclasses import dataclass, field
from typing import Any

@dataclass(order=True)
class PrioritizedItem:
    priority: int
    item: Any=field(compare=False)
~~~

_class _queue.SimpleQueue¶

    

~~~
无界的 FIFO 队列构造函数。简单的队列，缺少任务跟踪等高级功能。

在 3.7 版本加入.

_exception _queue.Empty¶
~~~
    

~~~
对空的 `Queue` 对象，调用非阻塞的 `get()` (or `get_nowait()`) 时，引发的异常。

_exception _queue.Full¶
~~~
    

~~~
对满的 `Queue` 对象，调用非阻塞的 `put()` (or `put_nowait()`) 时，引发的异常。

## Queue对象¶

队列对象 (`Queue`, `LifoQueue`, 或者 `PriorityQueue`) 提供下列描述的公共方法。

Queue.qsize()¶
~~~
    

~~~
返回队列的大致大小。注意，qsize() > 0 不保证后续的 get() 不被阻塞，qsize() < maxsize 也不保证 put() 不被阻塞。

Queue.empty()¶
~~~
    

~~~
如果队列为空，返回 `True` ，否则返回 `False` 。如果 empty() 返回 `True` ，不保证后续调用的 put() 不被阻塞。类似的，如果 empty() 返回 `False` ，也不保证后续调用的 get() 不被阻塞。

Queue.full()¶
~~~
    

~~~
如果队列是满的返回 `True` ，否则返回 `False` 。如果 full() 返回 `True` 不保证后续调用的 get() 不被阻塞。类似的，如果 full() 返回 `False` 也不保证后续调用的 put() 不被阻塞。

Queue.put( _item_ , _block =True_, _timeout =None_)¶
~~~
    

~~~
将 _item_ 加入队列。 如果可选参数 _block_ 为真值并且 _timeout_ 为 `None` (默认值)，则会在必要时阻塞直到有空闲槽位可用。 如为 _timeout_ 为正数，则将阻塞最多 _timeout_ 秒并会在没有可用的空闲槽位时引发 `Full` 异常。 在其他情况下 ( _block_ 为假值)，则如果空闲槽位立即可用则将条目加入队列，否则将引发 `Full` 异常 ( _timeout_ 在此情况下将被忽略)。

Queue.put_nowait( _item_ )¶
~~~
    

~~~
相当于 `put(item, block=False)`。

Queue.get( _block =True_, _timeout =None_)¶
~~~
    

~~~
从队列中移除并返回一个项目。如果可选参数 _block_ 是 true 并且 _timeout_ 是 `None` (默认值)，则在必要时阻塞至项目可得到。如果 _timeout_ 是个正数，将最多阻塞 _timeout_ 秒，如果在这段时间内项目不能得到，将引发 `Empty` 异常。反之 ( _block_ 是 false) , 如果一个项目立即可得到，则返回一个项目，否则引发 `Empty` 异常 (这种情况下， _timeout_ 将被忽略)。

POSIX 系统上在 3.0 之前，以及在 Windows 上的所有版本中，如果 _block_ 为真值并且 _timeout_ 为 `None`，此操作将进入在底层锁上的不可中断的等待。 这意味着不会发生任何异常，特别是 SIGINT 将不会触发 [`KeyboardInterrupt`](exceptions.md#KeyboardInterrupt "KeyboardInterrupt")。

Queue.get_nowait()¶
~~~
    

~~~
相当于 `get(False)` 。

提供了两个方法，用于支持跟踪 排队的任务 是否 被守护的消费者线程 完整的处理。

Queue.task_done()¶
~~~
    

~~~
表示前面排队的任务已经被完成。被队列的消费者线程使用。每个 `get()` 被用于获取一个任务， 后续调用 `task_done()` 告诉队列，该任务的处理已经完成。

如果 `join()` 当前正在阻塞，在所有条目都被处理后，将解除阻塞(意味着每个 `put()` 进队列的条目的 `task_done()` 都被收到)。

如果被调用的次数多于放入队列中的项目数量，将引发 [`ValueError`](exceptions.md#ValueError "ValueError") 异常 。

Queue.join()¶
~~~
    

~~~
阻塞至队列中所有的元素都被接收和处理完毕。

当一个条目被添加到队列的时候未完成任务的计数将会增加。 每当一个消费者线程调用 `task_done()` 来表明该条目已被提取且其上的所有工作已完成时未完成计数将会减少。 当未完成计数降为零时，`join()` 将解除阻塞。

如何等待排队的任务被完成的示例：
~~~
    
    
~~~
import threading
import queue

q = queue.Queue()

def worker():
    while True:
        item = q.get()
        print(f'Working on {item}')
        print(f'Finished {item}')
        q.task_done()

# Turn-on the worker thread.
threading.Thread(target=worker, daemon=True).start()

# Send thirty task requests to the worker.
for item in range(30):
    q.put(item)

# Block until all tasks are done.
q.join()
print('All work completed')
~~~

## SimpleQueue 对象¶

`SimpleQueue` 对象提供下列描述的公共方法。

SimpleQueue.qsize()¶

    

~~~
返回队列的大致大小。注意，qsize() > 0 不保证后续的 get() 不被阻塞。

SimpleQueue.empty()¶
~~~
    

~~~
如果队列为空则返回 `True`，否则返回 `False`。 如果 empty() 返回 `False` 则不保证后续对 get() 的调用将不会阻塞。

SimpleQueue.put( _item_ , _block =True_, _timeout =None_)¶
~~~
    

~~~
将 _item_ 放入队列。此方法永不阻塞，始终成功（除了潜在的低级错误，例如内存分配失败）。可选参数 _block_ 和 _timeout_ 仅仅是为了保持 `Queue.put()` 的兼容性而提供，其值被忽略。

**CPython 实现细节：** 此方法具有一个可重入的 C 实现。 也就是说，一个 `put()` 或 `get()` 调用可以被同一线程中的另一个 `put()` 调用打断而不会发生死锁或破坏队列内部的状态。 这使得它适用于析构器如 `__del__` 方法或 [`weakref`](weakref.md#module-weakref "weakref: Support for weak references and weak dictionaries.") 回调。

SimpleQueue.put_nowait( _item_ )¶
~~~
    

~~~
相当于 `put(item, block=False)`，为保持与 `Queue.put_nowait()` 的兼容性而提供。

SimpleQueue.get( _block =True_, _timeout =None_)¶
~~~
    

~~~
从队列中移除并返回一个项目。如果可选参数 _block_ 是 true 并且 _timeout_ 是 `None` (默认值)，则在必要时阻塞至项目可得到。如果 _timeout_ 是个正数，将最多阻塞 _timeout_ 秒，如果在这段时间内项目不能得到，将引发 `Empty` 异常。反之 ( _block_ 是 false) , 如果一个项目立即可得到，则返回一个项目，否则引发 `Empty` 异常 (这种情况下， _timeout_ 将被忽略)。

SimpleQueue.get_nowait()¶
~~~
    

~~~
相当于 `get(False)` 。

参见

类 [`multiprocessing.Queue`](multiprocessing.md#multiprocessing.Queue "multiprocessing.Queue")
~~~
    

~~~
