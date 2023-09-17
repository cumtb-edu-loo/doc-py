# `multiprocessing.shared_memory` \--- 可跨进程直接访问的共享内存¶

**源代码:** [Lib/multiprocessing/shared_memory.py](https://github.com/python/cpython/tree/3.12/Lib/multiprocessing/shared_memory.py)

在 3.8 版本加入.

* * *

该模块提供了一个 `SharedMemory` 类，用于分配和管理多核或对称多处理器（SMP）机器上进程间的共享内存。为了协助管理不同进程间的共享内存生命周期，`multiprocessing.managers` 模块也提供了一个 [`BaseManager`](multiprocessing.md#multiprocessing.managers.BaseManager "multiprocessing.managers.BaseManager") 的子类： `SharedMemoryManager`。

本模块中，共享内存是指 "System V 类型" 的共享内存块（虽然可能和它实现方式不完全一致）而不是 “分布式共享内存”。这种类型的的共享内存允许不同进程读写一片公共（或者共享）的易失性存储区域。一般来说，进程被限制只能访问属于自己进程空间的内存，但是共享内存允许跨进程共享数据，从而避免通过进程间发送消息的形式传递数据。相比通过磁盘、套接字或者其他要求序列化、反序列化和复制数据的共享形式，直接通过内存共享数据拥有更出色性能。

_class _multiprocessing.shared_memory.SharedMemory( _name =None_, _create =False_, _size =0_)¶

    

~~~
创建一个新的共享内存块或者连接到一片已经存在的共享内存块。每个共享内存块都被指定了一个全局唯一的名称。通过这种方式，进程可以使用一个特定的名字创建共享内存区块，然后其他进程使用同样的名字连接到这个共享内存块。

作为一种跨进程共享数据的方式，共享内存块的寿命可能超过创建它的原始进程。一个共享内存块可能同时被多个进程使用，当一个进程不再需要访问这个共享内存块的时候，应该调用 `close()` 方法。当一个共享内存块不被任何进程使用的时候，应该调用 `unlink()` 方法以执行必要的清理。

_name_ 是共享内存的唯一名称，字符串类型。如果创建一个新共享内存块的时候，名称指定为 `None` (默认值)，将会随机产生一个新名称。

_create_ 指定创建一个新的共享内存块 (`True`) 还是连接到已存在的共享内存块 (`False`) 。

如果是新创建共享内存块则 _size_ 用于指定块的大小为多少字节。由于某些平台是以内存页大小为最小单位来分配内存的，最终得到的内存块大小可能大于或等于要求的大小。如果是连接到已经存在的共享内存块， `size` 参数会被忽略。

close()¶
~~~
    

~~~
关闭实例对于共享内存的访问连接。所有实例确认自己不再需要使用共享内存的时候都应该调用 `close()` ，以保证必要的资源清理。调用 `close()` 并不会销毁共享内存区域。

unlink()¶
~~~
    

~~~
请求销毁底层的共享内存块。 为了执行必要的资源清理，在所有使用这个共享内存块的进程中，`unlink()` 应该调用一次（且只能调用一次）。 发出此销毁请求后，共享内存块可能会、也可能不会立即销毁，且此行为在不同操作系统之间可能不同。 调用 `unlink()` 后再尝试访问其中的数据可能导致内存错误。 注意：最后一个关闭共享内存访问权限的进程可以以任意顺序调用 `unlink()` 和 `close()`。

buf¶
~~~
    

~~~
共享内存块内容的 memoryview 。

name¶
~~~
    

~~~
共享内存块的唯一标识，只读属性。

size¶
~~~
    

~~~
共享内存块的字节大小，只读属性。

以下示例展示了 `SharedMemory` 底层的用法:
~~~
    
    
~~~shell
>>> from multiprocessing import shared_memory
>>> shm_a = shared_memory.SharedMemory(create=True, size=10)
>>> type(shm_a.buf)
<class 'memoryview'>
>>> buffer = shm_a.buf
>>> len(buffer)
10
>>> buffer[:4] = bytearray([22, 33, 44, 55])  # Modify multiple at once
>>> buffer[4] = 100                           # Modify single byte at a time
>>> # Attach to an existing shared memory block
>>> shm_b = shared_memory.SharedMemory(shm_a.name)
>>> import array
>>> array.array('b', shm_b.buf[:5])  # Copy the data into a new array.array
array('b', [22, 33, 44, 55, 100])
>>> shm_b.buf[:5] = b'howdy'  # Modify via shm_b using bytes
>>> bytes(shm_a.buf[:5])      # Access via shm_a
b'howdy'
>>> shm_b.close()   # Close each SharedMemory instance
>>> shm_a.close()
>>> shm_a.unlink()  # Call unlink only once to release the shared memory
~~~

下面的例子展示了 `SharedMemory` 类结合 [NumPy 数组](https://numpy.org/) 的实际应用，从两个独立的 Python shell 访问同一个 `numpy.ndarray`:

    
    
~~~shell
>>> # In the first Python interactive shell
>>> import numpy as np
>>> a = np.array([1, 1, 2, 3, 5, 8])  # Start with an existing NumPy array
>>> from multiprocessing import shared_memory
>>> shm = shared_memory.SharedMemory(create=True, size=a.nbytes)
>>> # Now create a NumPy array backed by shared memory
>>> b = np.ndarray(a.shape, dtype=a.dtype, buffer=shm.buf)
>>> b[:] = a[:]  # Copy the original data into shared memory
>>> b
array([1, 1, 2, 3, 5, 8])
>>> type(b)
<class 'numpy.ndarray'>
>>> type(a)
<class 'numpy.ndarray'>
>>> shm.name  # We did not specify a name so one was chosen for us
'psm_21467_46075'

>>> # In either the same shell or a new Python shell on the same machine
>>> import numpy as np
>>> from multiprocessing import shared_memory
>>> # Attach to the existing shared memory block
>>> existing_shm = shared_memory.SharedMemory(name='psm_21467_46075')
>>> # Note that a.shape is (6,) and a.dtype is np.int64 in this example
>>> c = np.ndarray((6,), dtype=np.int64, buffer=existing_shm.buf)
>>> c
array([1, 1, 2, 3, 5, 8])
>>> c[-1] = 888
>>> c
array([  1,   1,   2,   3,   5, 888])

>>> # Back in the first Python interactive shell, b reflects this change
>>> b
array([  1,   1,   2,   3,   5, 888])

>>> # Clean up from within the second Python shell
>>> del c  # Unnecessary; merely emphasizing the array is no longer used
>>> existing_shm.close()

>>> # Clean up from within the first Python shell
>>> del b  # Unnecessary; merely emphasizing the array is no longer used
>>> shm.close()
>>> shm.unlink()  # Free and release the shared memory block at the very end
~~~

_class _multiprocessing.managers.SharedMemoryManager([ _address_ [, _authkey_ ]])¶

    

~~~
[`BaseManager`](multiprocessing.md#multiprocessing.managers.BaseManager "multiprocessing.managers.BaseManager") 的子类，可用于管理跨进程的共享内存块。

调用 `SharedMemoryManager` 实例上的 [`start()`](multiprocessing.md#multiprocessing.managers.BaseManager.start "multiprocessing.managers.BaseManager.start") 方法会启动一个新进程。这个新进程的唯一目的就是管理所有由它创建的共享内存块的生命周期。想要释放此进程管理的所有共享内存块，可以调用实例的 [`shutdown()`](multiprocessing.md#multiprocessing.managers.BaseManager.shutdown "multiprocessing.managers.BaseManager.shutdown") 方法。这会触发执行它管理的所有 `SharedMemory` 对象的 `SharedMemory.unlink()` 方法，然后停止这个进程。通过 `SharedMemoryManager` 创建 `SharedMemory` 实例，我们可以避免手动跟踪和释放共享内存资源。

这个类提供了创建和返回 `SharedMemory` 实例的方法，以及以共享内存为基础创建一个列表类对象 (`ShareableList`) 的方法。

有关继承的可选输入参数 _address_ 和 _authkey_ 以及他们如何用于从进程连接已经存在的 `SharedMemoryManager` 服务，参见 [`multiprocessing.managers.BaseManager`](multiprocessing.md#multiprocessing.managers.BaseManager "multiprocessing.managers.BaseManager") 。

SharedMemory( _size_ )¶
~~~
    

~~~
使用 `size` 参数，创建一个新的指定字节大小的 `SharedMemory` 对象并返回。

ShareableList( _sequence_ )¶
~~~
    

~~~
创建并返回一个新的 `ShareableList` 对象，通过输入参数 `sequence` 初始化。

下面的案例展示了 `SharedMemoryManager` 的基本机制:
~~~
    
    
~~~shell
>>> from multiprocessing.managers import SharedMemoryManager
>>> smm = SharedMemoryManager()
>>> smm.start()  # Start the process that manages the shared memory blocks
>>> sl = smm.ShareableList(range(4))
>>> sl
ShareableList([0, 1, 2, 3], name='psm_6572_7512')
>>> raw_shm = smm.SharedMemory(size=128)
>>> another_sl = smm.ShareableList('alpha')
>>> another_sl
ShareableList(['a', 'l', 'p', 'h', 'a'], name='psm_6572_12221')
>>> smm.shutdown()  # Calls unlink() on sl, raw_shm, and another_sl
~~~

以下案例展示了 `SharedMemoryManager` 对象的一种可能更方便的使用方式，通过 [`with`](8.%20复合语句.md#with) 语句来保证所有共享内存块在使用完后被释放。

    
    
~~~shell
>>> with SharedMemoryManager() as smm:
...     sl = smm.ShareableList(range(2000))
...     # Divide the work among two processes, storing partial results in sl
...     p1 = Process(target=do_work, args=(sl, 0, 1000))
...     p2 = Process(target=do_work, args=(sl, 1000, 2000))
...     p1.start()
...     p2.start()  # A multiprocessing.Pool might be more efficient
...     p1.join()
...     p2.join()   # Wait for all work to complete in both processes
...     total_result = sum(sl)  # Consolidate the partial results now in sl
~~~

在 [`with`](8.%20复合语句.md#with) 语句中使用 `SharedMemoryManager` 对象的时候，使用这个管理器创建的共享内存块会在 [`with`](8.%20复合语句.md#with) 语句代码块结束后被释放。

_class _multiprocessing.shared_memory.ShareableList( _sequence=None_ , _\\*_ , _name=None_ )¶

    

~~~
提供了一个类似于可变列表的对象，其中存储的所有值都存储在一个共享内存块中。 这限制了可存储的值只能是 `int` (带符号的 64 位), `float`, `bool`, `str` (当以 utf-8 编码时每个值小于 10M 字节), `bytes` (每个值小于 10M 字节) 和 `None` 内置数据类型。 它与 `list` 内置类型的另一个显著区别在于这些列表不能改变其总长度 (例如不能追加、插入等)，也不支持通过切片动态创建新的 `ShareableList` 实例。

_sequence_ 会被用来为一个新的 `ShareableList` 填充值。 设为 `None` 则会基于唯一的共享内存名称关联到已经存在的 `ShareableList`。

_name_ 是所请求的共享内存的唯一名称，与 `SharedMemory` 的定义中所描述的一致。 当关联到现有的 `ShareableList` 时，则指明其共享内存块的唯一名称并将 `sequence` 设为 `None`。

备注

[`bytes`](stdtypes.md#bytes "bytes") 和 [`str`](stdtypes.md#str "str") 值存在一个已知问题。 如果这些值以 `\x00` 空字节或字符结尾，那么在按索引从 `ShareableList` 抓取这些值时它们可能会被 _静默地截去_ 。 这种 `.rstrip(b'\x00')` 行为被认为是一个错误并可能在未来消失。 参见 [gh-106939](https://github.com/python/cpython/issues/106939)。

对于某些应用来说在右侧截去尾部空值会造成问题，要绕过此问题可以在存储这样的值时总是无条件地在其末尾附加一个额外的非 0 字节并在获取时无条件地移除它:
~~~
    
    
~~~shell
>>> from multiprocessing import shared_memory
>>> nul_bug_demo = shared_memory.ShareableList(['?\x00', b'\x03\x02\x01\x00\x00\x00'])
>>> nul_bug_demo[0]
'?'
>>> nul_bug_demo[1]
b'\x03\x02\x01'
>>> nul_bug_demo.shm.unlink()
>>> padded = shared_memory.ShareableList(['?\x00\x07', b'\x03\x02\x01\x00\x00\x00\x07'])
>>> padded[0][:-1]
'?\x00'
>>> padded[1][:-1]
b'\x03\x02\x01\x00\x00\x00'
>>> padded.shm.unlink()
~~~

count( _value_ )¶

    

~~~
返回 `value` 出现的次数。

index( _value_ )¶
~~~
    

~~~
返回 `value` 首次出现的位置，如果 `value` 不存在, 则抛出 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。

format¶
~~~
    

~~~
包含由所有当前存储值所使用的 [`struct`](struct.md#module-struct "struct: Interpret bytes as packed binary data.") 打包格式的只读属性。

shm¶
~~~
    

~~~
存储了值的 `SharedMemory` 实例。

下面的例子演示了 `ShareableList` 实例的基本用法:
~~~
    
    
~~~shell
>>> from multiprocessing import shared_memory
>>> a = shared_memory.ShareableList(['howdy', b'HoWdY', -273.154, 100, None, True, 42])
>>> [ type(entry) for entry in a ]
[<class 'str'>, <class 'bytes'>, <class 'float'>, <class 'int'>, <class 'NoneType'>, <class 'bool'>, <class 'int'>]
>>> a[2]
-273.154
>>> a[2] = -78.5
>>> a[2]
-78.5
>>> a[2] = 'dry ice'  # Changing data types is supported as well
>>> a[2]
'dry ice'
>>> a[2] = 'larger than previously allocated storage space'
Traceback (most recent call last):
  ...
ValueError: exceeds available storage for existing str
>>> a[2]
'dry ice'
>>> len(a)
7
>>> a.index(42)
6
>>> a.count(b'howdy')
0
>>> a.count(b'HoWdY')
1
>>> a.shm.close()
>>> a.shm.unlink()
>>> del a  # Use of a ShareableList after call to unlink() is unsupported
~~~

下面的例子演示了一个、两个或多个进程如何通过提供下层的共享内存块名称来访问同一个 `ShareableList`:

    
    
~~~shell
>>> b = shared_memory.ShareableList(range(5))         # In a first process
>>> c = shared_memory.ShareableList(name=b.shm.name)  # In a second process
>>> c
ShareableList([0, 1, 2, 3, 4], name='...')
>>> c[-1] = -999
>>> b[-1]
-999
>>> b.shm.close()
>>> c.shm.close()
>>> c.shm.unlink()
~~~

下面的例子显示 `ShareableList` (以及下层的 `SharedMemory`) 对象可以在必要时被封存和解封。 请注意，它将仍然为同一个共享对象。 出现这种情况是因为被反序列化的对象具有相同的唯一名称并使用这个相同的名称附加到现有的对象上（如果对象仍然保持存活）:

    
    
~~~shell
>>> import pickle
>>> from multiprocessing import shared_memory
>>> sl = shared_memory.ShareableList(range(10))
>>> list(sl)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
~~~
    
    
~~~shell
>>> deserialized_sl = pickle.loads(pickle.dumps(sl))
>>> list(deserialized_sl)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
~~~
    
    
~~~shell
>>> sl[0] = -1
>>> deserialized_sl[1] = -2
>>> list(sl)
[-1, -2, 2, 3, 4, 5, 6, 7, 8, 9]
>>> list(deserialized_sl)
[-1, -2, 2, 3, 4, 5, 6, 7, 8, 9]
~~~
    
    
~~~shell
>>> sl.shm.close()
>>> sl.shm.unlink()
~~~

