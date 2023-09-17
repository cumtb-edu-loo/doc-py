# `threading` \--- 基于线程的并行¶

**源代码:** [Lib/threading.py](https://github.com/python/cpython/tree/3.12/Lib/threading.py)

* * *

这个模块在低层级的 [`_thread`](_thread.md#module-_thread "_thread: Low-level threading API.") 模块之上构造了高层级的线程接口。

在 3.7 版本发生变更: 这个模块曾经为可选项，但现在总是可用。

参见

[`concurrent.futures.ThreadPoolExecutor`](concurrent.futures.md#concurrent.futures.ThreadPoolExecutor "concurrent.futures.ThreadPoolExecutor") 提供了一个高层级接口用来向后台线程推送任务而不会阻塞调用方线程的执行，同时仍然能够在需要时获取任务的结果。

[`queue`](queue.md#module-queue "queue: A synchronized queue class.") 提供了一个线程安全的接口用来在运行中的线程之间交换数据。

[`asyncio`](asyncio.md#module-asyncio "asyncio: Asynchronous I/O.") 提供了一个替代方式用来实现任务层级的并发而不要求使用多个操作系统线程。

备注

在 Python 2.x 系列中，此模块包含有某些方法和函数 `camelCase` 形式的名称。 它们在 Python 3.10 中已弃用，但为了与 Python 2.5 及更旧版本的兼容性而仍受到支持。

**CPython 实现细节：** 在 CPython 中，由于存在 [全局解释器锁](../glossary.md#term-global-interpreter-lock)，同一时刻只有一个线程可以执行 Python 代码（虽然某些性能导向的库可能会去除此限制）。 如果你想让你的应用更好地利用多核心计算机的计算资源，推荐你使用 [`multiprocessing`](multiprocessing.md#module-multiprocessing "multiprocessing: Process-based parallelism.") 或 [`concurrent.futures.ProcessPoolExecutor`](concurrent.futures.md#concurrent.futures.ProcessPoolExecutor "concurrent.futures.ProcessPoolExecutor")。 但是，如果你想要同时运行多个 I/O 密集型任务，则多线程仍然是一个合适的模型。

[可用性](3.标准库/intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](3.标准库/intro.md#wasm-availability) 了解详情。

这个模块定义了以下函数：

threading.active_count()¶

    

~~~
返回当前存活的 `Thread` 对象的数量。 返回值与 `enumerate()` 所返回的列表长度一致。

函数 `activeCount` 是此函数的已弃用别名。

threading.current_thread()¶
~~~
    

~~~
返回当前对应调用者的控制线程的 `Thread` 对象。如果调用者的控制线程不是利用 `threading` 创建，会返回一个功能受限的虚拟线程对象。

函数 `currentThread` 是此函数的已弃用别名。

threading.excepthook( _args_ , _/_ )¶
~~~
    

~~~
处理由 `Thread.run()` 引发的未捕获异常。

_args_ 参数具有以下属性:

  * _exc_type_ : 异常类型

  * _exc_value_ : 异常值，可以是 `None`.

  * _exc_traceback_ : 异常回溯，可以是 `None`.

  * _thread_ : 引发异常的线程，可以为 `None`。

如果 _exc_type_ 为 [`SystemExit`](exceptions.md#SystemExit "SystemExit")，则异常会被静默地忽略。 在其他情况下，异常将被打印到 [`sys.stderr`](sys.md#sys.stderr "sys.stderr")。

如果此函数引发了异常，则会调用 [`sys.excepthook()`](sys.md#sys.excepthook "sys.excepthook") 来处理它。

`threading.excepthook()` 可以被重载以控制由 `Thread.run()` 引发的未捕获异常的处理方式。

使用定制钩子存放 _exc_value_ 可能会创建引用循环。 它应当在不再需要异常时被显式地清空以打破引用循环。

如果一个对象正在被销毁，那么使用自定义的钩子储存 _thread_ 可能会将其复活。请在自定义钩子生效后避免储存 _thread_ ，以避免对象的复活。

参见

[`sys.excepthook()`](sys.md#sys.excepthook "sys.excepthook") 处理未捕获的异常。

在 3.8 版本加入.

threading.__excepthook__¶
~~~
    

~~~
保存 `threading.excepthook()` 的原始值。 它被保存以便在原始值碰巧被已损坏或替代对象所替换的情况下可被恢复。

在 3.10 版本加入.

threading.get_ident()¶
~~~
    

~~~
返回当前线程的 “线程标识符”。它是一个非零的整数。它的值没有直接含义，主要是用作 magic cookie，比如作为含有线程相关数据的字典的索引。线程标识符可能会在线程退出，新线程创建时被复用。

在 3.3 版本加入.

threading.get_native_id()¶
~~~
    

~~~
返回内核分配给当前线程的原生集成线程 ID。 这是一个非负整数。 它的值可被用来在整个系统中唯一地标识这个特定线程（直到线程终结，在那之后该值可能会被 OS 回收再利用）。

[可用性](intro.md#availability): Windows, FreeBSD, Linux, macOS, OpenBSD, NetBSD, AIX, DragonFlyBSD。

在 3.8 版本加入.

threading.enumerate()¶
~~~
    

~~~
返回当前所有存活的 `Thread` 对象的列表。 该列表包括守护线程以及 `current_thread()` 创建的空线程。 它不包括已终结的和尚未开始的线程。 但是，主线程将总是结果的一部分，即使是在已终结的时候。

threading.main_thread()¶
~~~
    

~~~
返回主 `Thread` 对象。一般情况下，主线程是Python解释器开始时创建的线程。

在 3.4 版本加入.

threading.settrace( _func_ )¶
~~~
    

~~~
为所有 `threading` 模块开始的线程设置追踪函数。在每个线程的 `run()` 方法被调用前， _func_ 会被传递给 [`sys.settrace()`](sys.md#sys.settrace "sys.settrace") 。

threading.settrace_all_threads( _func_ )¶
~~~
    

~~~
为从 `threading` 模块启动的所有线程和当前正在执行的所有 Python 线程设置追踪函数。

_func_ 将为每个线程传递给 [`sys.settrace()`](sys.md#sys.settrace "sys.settrace")，在其 `run()` 方法被调用之前。

在 3.12 版本加入.

threading.gettrace()¶
~~~
    

~~~
返回由 `settrace()` 设置的跟踪函数。

在 3.10 版本加入.

threading.setprofile( _func_ )¶
~~~
    

~~~
为所有 `threading` 模块开始的线程设置性能测试函数。在每个线程的 `run()` 方法被调用前， _func_ 会被传递给 [`sys.setprofile()`](sys.md#sys.setprofile "sys.setprofile") 。

threading.setprofile_all_threads( _func_ )¶
~~~
    

~~~
为从 `threading` 模块启动的所有线程和当前正在执行的所有 Python 线程设置性能分析函数。

_func_ 将为每个线程传递给 [`sys.setprofile()`](sys.md#sys.setprofile "sys.setprofile")，在其 `run()` 方法被调用之前。

在 3.12 版本加入.

threading.getprofile()¶
~~~
    

~~~
返回由 `setprofile()` 设置的性能分析函数。

在 3.10 版本加入.

threading.stack_size([ _size_ ])¶
~~~
    

~~~
返回创建线程时使用的堆栈大小。可选参数 _size_ 指定之后新建的线程的堆栈大小，而且一定要是0（根据平台或者默认配置）或者最小是32,768(32KiB)的一个正整数。如果 _size_ 没有指定，默认是0。如果不支持改变线程堆栈大小，会抛出 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 错误。如果指定的堆栈大小不合法，会抛出 [`ValueError`](exceptions.md#ValueError "ValueError") 错误并且不会修改堆栈大小。32KiB是当前最小的能保证解释器有足够堆栈空间的堆栈大小。需要注意的是部分平台对于堆栈大小会有特定的限制，例如要求大于32KiB的堆栈大小或者需要根据系统内存页面的整数倍进行分配 - 应当查阅平台文档有关详细信息（4KiB页面比较普遍，在没有更具体信息的情况下，建议的方法是使用4096的倍数作为堆栈大小）。

[可用性](intro.md#availability): Windows, pthreads。

带有 POSIX 线程支持的 Unix 平台。

这个模块同时定义了以下常量：

threading.TIMEOUT_MAX¶
~~~
    

~~~
阻塞函数（ `Lock.acquire()`, `RLock.acquire()`, `Condition.wait()`, ...）中形参 _timeout_ 允许的最大值。传入超过这个值的 timeout 会抛出 [`OverflowError`](exceptions.md#OverflowError "OverflowError") 异常。

在 3.2 版本加入.

这个模块定义了许多类，详见以下部分。

该模块的设计基于 Java的线程模型。 但是，在Java里面，锁和条件变量是每个对象的基础特性，而在Python里面，这些被独立成了单独的对象。 Python 的 `Thread` 类只是 Java 的 Thread 类的一个子集；目前还没有优先级，没有线程组，线程还不能被销毁、停止、暂停、恢复或中断。 Java 的 Thread 类的静态方法在实现时会映射为模块级函数。

下述方法的执行都是原子性的。

## 线程本地数据¶

线程本地数据是特定线程的数据。管理线程本地数据，只需要创建一个 `local` （或者一个子类型）的实例并在实例中储存属性：
~~~
    
    
~~~
mydata = threading.local()
mydata.x = 1
~~~

在不同的线程中，实例的值会不同。

_class _threading.local¶

    

~~~
一个代表线程本地数据的类。

更多相关细节和大量示例，请参阅 `_threading_local` 模块的文档字符串: [Lib/_threading_local.py](https://github.com/python/cpython/tree/3.12/Lib/_threading_local.py)。

## 线程对象¶

`Thread` 类代表一个在独立控制线程中运行的活动。 指定活动有两种方式：向构造器传递一个可调用对象，或在子类中重载 `run()` 方法。 其他方法不应在子类中重载（除了构造器）。 换句话说， _只能_ 重载这个类的 `__init__()` 和 `run()` 方法。

当线程对象一旦被创建，其活动必须通过调用线程的 `start()` 方法开始。 这会在独立的控制线程中发起调用 `run()` 方法。

一旦线程活动开始，该线程会被认为是 '存活的' 。当它的 `run()` 方法终结了（不管是正常的还是抛出未被处理的异常），就不是'存活的'。 `is_alive()` 方法用于检查线程是否存活。

其他线程可以调用一个线程的 `join()` 方法。这会阻塞调用该方法的线程，直到被调用 `join()` 方法的线程终结。

线程有名字。名字可以传递给构造函数，也可以通过 `name` 属性读取或者修改。

如果 `run()` 方法引发了异常，则会调用 `threading.excepthook()` 来处理它。 在默认情况下，`threading.excepthook()` 会静默地忽略 [`SystemExit`](exceptions.md#SystemExit "SystemExit")。

一个线程可以被标记成一个“守护线程”。 这个标识的意义是，当剩下的线程都是守护线程时，整个 Python 程序将会退出。 初始值继承于创建线程。 这个标识可以通过 `daemon` 特征属性或者 _daemon_ 构造器参数来设置。

备注

守护线程在程序关闭时会突然关闭。他们的资源（例如已经打开的文档，数据库事务等等）可能没有被正确释放。如果你想你的线程正常停止，设置他们成为非守护模式并且使用合适的信号机制，例如： `Event`。

有个 "主线程" 对象；这对应Python程序里面初始的控制线程。它不是一个守护线程。

创建“虚拟线程对象”是有可能的。 它们是与“外部线程”相对应 的线程对象，是在 threading 模块之外启动的控制线程，例如直接来自 C 代码。 虚拟线程对象的功能是受限的；它们总是会被视为处于激活和守护状态，且无法被 合并。 它们绝不会被删除，因为检测外部线程的终结是不可能做到的。

_class _threading.Thread( _group =None_, _target =None_, _name =None_, _args =()_, _kwargs ={}_, _*_ , _daemon =None_)¶
~~~
    

~~~
应当始终使用关键字参数调用此构造函数。 参数如下：

_group_ 应为 `None`；保留给将来实现 `ThreadGroup` 类的扩展使用。

_target_ 是用于 `run()` 方法调用的可调用对象。默认是 `None`，表示不需要调用任何方法。

_name_ 是线程名称。 在默认情况下，会以 "Thread- _N_ " 的形式构造唯一名称，其中 _N_ 为一个较小的十进制数值，或是 "Thread- _N_ (target)" 的形式，其中 "target" 为 `target.__name__`，如果指定了 _target_ 参数的话。

_args_ 是用于发起调用目标函数的参数列表或元组。 默认为 `()`。

_kwargs_ 是用于调用目标函数的关键字参数字典。默认是 `{}`。

如果不是 `None`， _daemon_ 参数将显式地设置该线程是否为守护模式。 如果是 `None` (默认值)，线程将继承当前线程的守护模式属性。

如果子类型重载了构造函数，它一定要确保在做任何事前，先发起调用基类构造器(`Thread.__init__()`)。

在 3.10 版本发生变更: 使用 _target_ 名称，如果 _name_ 参数被省略的话。

在 3.3 版本发生变更: 加入 _daemon_ 参数。

start()¶
~~~
    

~~~
开始线程活动。

它在一个线程里最多只能被调用一次。 它安排对象的 `run()` 方法在一个独立的控制线程中被调用。

如果同一个线程对象中调用这个方法的次数大于一次，会抛出 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 。

run()¶
~~~
    

~~~
代表线程活动的方法。

你可以在子类型里重载这个方法。 标准的 `run()` 方法会对作为 _target_ 参数传递给该对象构造器的可调用对象（如果存在）发起调用，并附带从 _args_ 和 _kwargs_ 参数分别获取的位置和关键字参数。

使用列表或元组作为传给 `Thread` 的 _args_ 参数可以达成同样的效果。

示例：
~~~
    
    
~~~shell
>>> from threading import Thread
>>> t = Thread(target=print, args=[1])
>>> t.run()
1
>>> t = Thread(target=print, args=(1,))
>>> t.run()
1
~~~

join( _timeout =None_)¶

    

~~~
等待，直到线程终结。这会阻塞调用这个方法的线程，直到被调用 `join()` 的线程终结 -- 不管是正常终结还是抛出未处理异常 -- 或者直到发生超时，超时选项是可选的。

当 _timeout_ 参数存在而且不是 `None` 时，它应该是一个用于指定操作超时的以秒为单位的浮点数或者分数。因为 `join()` 总是返回 `None` ，所以你一定要在 `join()` 后调用 `is_alive()` 才能判断是否发生超时 -- 如果线程仍然存活，则 `join()` 超时。

当 _timeout_ 参数不存在或者是 `None` ，这个操作会阻塞直到线程终结。

一个线程可以被合并多次。

如果尝试加入当前线程会导致死锁， `join()` 会引起 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 异常。如果尝试 `join()` 一个尚未开始的线程，也会抛出相同的异常。

name¶
~~~
    

~~~
只用于识别的字符串。它没有语义。多个线程可以赋予相同的名称。 初始名称由构造函数设置。

getName()¶

setName()¶
~~~
    

~~~
已被弃用的 `name` 的取值/设值 API；请改为直接以特征属性方式使用它。

自 3.10 版本弃用.

ident¶
~~~
    

~~~
这个线程的 '线程标识符'，如果线程尚未开始则为 `None` 。这是个非零整数。参见 `get_ident()` 函数。当一个线程退出而另外一个线程被创建，线程标识符会被复用。即使线程退出后，仍可得到标识符。

native_id¶
~~~
    

~~~
此线程的线程 ID (`TID`)，由 OS (内核) 分配。 这是一个非负整数，或者如果线程还未启动则为 `None`。 请参阅 `get_native_id()` 函数。 这个值可被用来在全系统范围内唯一地标识这个特定线程 (直到线程终结，在那之后该值可能会被 OS 回收再利用)。

备注

类似于进程 ID，线程 ID 的有效期（全系统范围内保证唯一）将从线程被创建开始直到线程被终结。

[可用性](intro.md#availability): Windows, FreeBSD, Linux, macOS, OpenBSD, NetBSD, AIX, DragonFlyBSD。

在 3.8 版本加入.

is_alive()¶
~~~
    

~~~
返回线程是否存活。

当 `run()` 方法刚开始直到 `run()` 方法刚结束，这个方法返回 `True` 。模块函数 `enumerate()` 返回包含所有存活线程的列表。

daemon¶
~~~
    

~~~
一个布尔值，表示这个线程是否是一个守护线程（`True`）或不是（`False`）。 这个值必须在调用 `start()` 之前设置，否则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 。它的初始值继承自创建线程；主线程不是一个守护线程，因此所有在主线程中创建的线程默认为 `daemon` = `False`。

当没有存活的非守护线程时，整个Python程序才会退出。

isDaemon()¶

setDaemon()¶
~~~
    

~~~
已被弃用的 `daemon` 的取值/设值 API；请改为直接以特征属性方式使用它。

自 3.10 版本弃用.

## 锁对象¶

原始锁是一个在锁定时不属于特定线程的同步基元组件。在Python中，它是能用的最低级的同步基元组件，由 [`_thread`](_thread.md#module-_thread "_thread: Low-level threading API.") 扩展模块直接实现。

原始锁处于 "锁定" 或者 "非锁定" 两种状态之一。它被创建时为非锁定状态。它有两个基本方法， `acquire()` 和 `release()` 。当状态为非锁定时， `acquire()` 将状态改为 锁定 并立即返回。当状态是锁定时， `acquire()` 将阻塞至其他线程调用 `release()` 将其改为非锁定状态，然后 `acquire()` 调用重置其为锁定状态并返回。 `release()` 只在锁定状态下调用； 它将状态改为非锁定并立即返回。如果尝试释放一个非锁定的锁，则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 异常。

锁同样支持 上下文管理协议。

当多个线程在 `acquire()` 等待状态转变为未锁定被阻塞，然后 `release()` 重置状态为未锁定时，只有一个线程能继续执行；至于哪个等待线程继续执行没有定义，并且会根据实现而不同。

所有方法的执行都是原子性的。

_class _threading.Lock¶
~~~
    

~~~
实现原始锁对象的类。一旦一个线程获得一个锁，会阻塞随后尝试获得锁的线程，直到它被释放；任何线程都可以释放它。

需要注意的是 `Lock` 其实是一个工厂函数，返回平台支持的具体锁类中最有效的版本的实例。

acquire( _blocking =True_, _timeout =-1_)¶
~~~
    

~~~
可以阻塞或非阻塞地获得锁。

当调用时参数 _blocking_ 设置为 `True` （缺省值），阻塞直到锁被释放，然后将锁锁定并返回 `True` 。

在参数 _blocking_ 被设置为 `False` 的情况下调用，将不会发生阻塞。如果调用时 _blocking_ 设为 `True` 会阻塞，并立即返回 `False` ；否则，将锁锁定并返回 `True`。

当参数 _timeout_ 使用设置为正值的浮点数调用时，最多阻塞 _timeout_ 指定的秒数，在此期间锁不能被获取。设置 _timeout_ 参数为 `-1` specifies an unbounded wait. It is forbidden to specify a _timeout_ when _blocking_ is `False` 。

如果成功获得锁，则返回 `True`，否则返回 `False` (例如发生 _超时_ 的时候)。

在 3.2 版本发生变更: 新的 _timeout_ 形参。

在 3.2 版本发生变更: 现在如果底层线程实现支持，则可以通过POSIX上的信号中断锁的获取。

release()¶
~~~
    

~~~
释放一个锁。这个方法可以在任何线程中调用，不单指获得锁的线程。

当锁被锁定，将它重置为未锁定，并返回。如果其他线程正在等待这个锁解锁而被阻塞，只允许其中一个允许。

当在未锁定的锁上发起调用时，会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

没有返回值。

locked()¶
~~~
    

~~~
当锁被获取时，返回 `True`。

## 递归锁对象¶

重入锁是一个可以被同一个线程多次获取的同步基元组件。在内部，它在基元锁的锁定/非锁定状态上附加了 "所属线程" 和 "递归等级" 的概念。在锁定状态下，某些线程拥有锁 ； 在非锁定状态下， 没有线程拥有它。

若要锁定锁，线程调用其 `acquire()` 方法；一旦线程拥有了锁，方法将返回。若要解锁，线程调用 `release()` 方法。 `acquire()`/`release()` 对可以嵌套；只有最终 `release()` (最外面一对的 `release()` ) 将锁解开，才能让其他线程继续处理 `acquire()` 阻塞。

递归锁也支持 上下文管理协议。

_class _threading.RLock¶
~~~
    

~~~
此类实现了重入锁对象。重入锁必须由获取它的线程释放。一旦线程获得了重入锁，同一个线程再次获取它将不阻塞；线程必须在每次获取它时释放一次。

需要注意的是 `RLock` 其实是一个工厂函数，返回平台支持的具体递归锁类中最有效的版本的实例。

acquire( _blocking =True_, _timeout =-1_)¶
~~~
    

~~~
可以阻塞或非阻塞地获得锁。

当无参数调用时： 如果这个线程已经拥有锁，递归级别增加一，并立即返回。否则，如果其他线程拥有该锁，则阻塞至该锁解锁。一旦锁被解锁(不属于任何线程)，则抢夺所有权，设置递归等级为一，并返回。如果多个线程被阻塞，等待锁被解锁，一次只有一个线程能抢到锁的所有权。在这种情况下，没有返回值。

当调用时，将 _blocking_ 参数设置为 `True`，所做的事情与调用时没有参数时相同，并返回 `True`。

当 _blocking_ 参数设置为 `False``时调用，不会阻塞。如果调用时没有参数而且会阻塞，立即返回``False`；否则，做与没有参数的调用相同的事情，并返回 `True`。

当浮点数参数 _timeout_ 设置为正值时调用，最多阻断 _timeout_ 指定的秒数，如果期间锁不能被获取。 如果锁已被获取返回 `True`，如果超时已过，则返回 `False`。

在 3.2 版本发生变更: 新的 _timeout_ 形参。

release()¶
~~~
    

~~~
释放锁，自减递归等级。如果减到零，则将锁重置为非锁定状态(不被任何线程拥有)，并且，如果其他线程正被阻塞着等待锁被解锁，则仅允许其中一个线程继续。如果自减后，递归等级仍然不是零，则锁保持锁定，仍由调用线程拥有。

只有当前线程拥有锁才能调用这个方法。如果锁被释放后调用这个方法，会引起 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 异常。

没有返回值。

## 条件对象¶

条件变量总是与某种类型的锁对象相关联，锁对象可以通过传入获得，或者在缺省的情况下自动创建。当多个条件变量需要共享同一个锁时，传入一个锁很有用。锁是条件对象的一部分，你不必单独地跟踪它。

条件变量遵循 上下文管理协议 ：使用 `with` 语句会在它包围的代码块内获取关联的锁。 `acquire()` 和 `release()` 方法也能调用关联锁的相关方法。

其它方法必须在持有关联的锁的情况下调用。 `wait()` 方法释放锁，然后阻塞直到其它线程调用 `notify()` 方法或 `notify_all()` 方法唤醒它。一旦被唤醒， `wait()` 方法重新获取锁并返回。它也可以指定超时时间。

The `notify()` method wakes up one of the threads waiting for the condition variable, if any are waiting. The `notify_all()` method wakes up all threads waiting for the condition variable.

注意： `notify()` 方法和 `notify_all()` 方法并不会释放锁，这意味着被唤醒的线程不会立即从它们的 `wait()` 方法调用中返回，而是会在调用了 `notify()` 方法或 `notify_all()` 方法的线程最终放弃了锁的所有权后返回。

使用条件变量的典型编程风格是将锁用于同步某些共享状态的权限，那些对状态的某些特定改变感兴趣的线程，它们重复调用 `wait()` 方法，直到看到所期望的改变发生；而对于修改状态的线程，它们将当前状态改变为可能是等待者所期待的新状态后，调用 `notify()` 方法或者 `notify_all()` 方法。例如，下面的代码是一个通用的无限缓冲区容量的生产者-消费者情形：
~~~
    
    
~~~
# Consume one item
with cv:
    while not an_item_is_available():
        cv.wait()
    get_an_available_item()

# Produce one item
with cv:
    make_an_item_available()
    cv.notify()
~~~

使用 `while` 循环检查所要求的条件成立与否是有必要的，因为 `wait()` 方法可能要经过不确定长度的时间后才会返回，而此时导致 `notify()` 方法调用的那个条件可能已经不再成立。这是多线程编程所固有的问题。 `wait_for()` 方法可自动化条件检查，并简化超时计算。

    
    
~~~
# Consume an item
with cv:
    cv.wait_for(an_item_is_available)
    get_an_available_item()
~~~

选择 `notify()` 还是 `notify_all()` ，取决于一次状态改变是只能被一个还是能被多个等待线程所用。例如在一个典型的生产者-消费者情形中，添加一个项目到缓冲区只需唤醒一个消费者线程。

_class _threading.Condition( _lock =None_)¶

    

~~~
实现条件变量对象的类。一个条件变量对象允许一个或多个线程在被其它线程所通知之前进行等待。

如果给出了非 `None` 的 _lock_ 参数，则它必须为 `Lock` 或者 `RLock` 对象，并且它将被用作底层锁。否则，将会创建新的 `RLock` 对象，并将其用作底层锁。

在 3.3 版本发生变更: 从工厂函数变为类。

acquire( _* args_)¶
~~~
    

~~~
请求底层锁。此方法调用底层锁的相应方法，返回值是底层锁相应方法的返回值。

release()¶
~~~
    

~~~
释放底层锁。此方法调用底层锁的相应方法。没有返回值。

wait( _timeout =None_)¶
~~~
    

~~~
等待直到被通知或发生超时。如果线程在调用此方法时没有获得锁，将会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 异常。

这个方法释放底层锁，然后阻塞，直到在另外一个线程中调用同一个条件变量的 `notify()` 或 `notify_all()` 唤醒它，或者直到可选的超时发生。一旦被唤醒或者超时，它重新获得锁并返回。

当提供了 _timeout_ 参数且不是 `None` 时，它应该是一个浮点数，代表操作的超时时间，以秒为单位（可以为小数）。

当底层锁是个 `RLock` ，不会使用它的 `release()` 方法释放锁，因为当它被递归多次获取时，实际上可能无法解锁。相反，使用了 `RLock` 类的内部接口，即使多次递归获取它也能解锁它。 然后，在重新获取锁时，使用另一个内部接口来恢复递归级别。

返回 `True` ，除非提供的 _timeout_ 过期，这种情况下返回 `False`。

在 3.2 版本发生变更: 很明显，方法总是返回 `None`。

wait_for( _predicate_ , _timeout =None_)¶
~~~
    

~~~
等待，直到条件计算为真。 _predicate_ 应该是一个可调用对象而且它的返回值可被解释为一个布尔值。可以提供 _timeout_ 参数给出最大等待时间。

这个实用方法会重复地调用 `wait()` 直到满足判断式或者发生超时。返回值是判断式最后一个返回值，而且如果方法发生超时会返回 `False` 。

忽略超时功能，调用此方法大致相当于编写:
~~~
    
    
~~~
while not predicate():
    cv.wait()
~~~

因此，规则同样适用于 `wait()` ：锁必须在被调用时保持获取，并在返回时重新获取。 随着锁定执行判断式。

在 3.2 版本加入.

notify( _n =1_)¶

    

~~~
默认唤醒一个等待这个条件的线程。如果调用线程在没有获得锁的情况下调用这个方法，会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 异常。

这个方法唤醒最多 _n_ 个正在等待这个条件变量的线程；如果没有线程在等待，这是一个空操作。

当前实现中，如果至少有 _n_ 个线程正在等待，准确唤醒 _n_ 个线程。但是依赖这个行为并不安全。未来，优化的实现有时会唤醒超过 _n_ 个线程。

注意：被唤醒的线程并没有真正恢复到它调用的 `wait()` ，直到它可以重新获得锁。 因为 `notify()` 不释放锁，其调用者才应该这样做。

notify_all()¶
~~~
    

~~~
唤醒所有正在等待这个条件的线程。这个方法行为与 `notify()` 相似，但并不只唤醒单一线程，而是唤醒所有等待线程。如果调用线程在调用这个方法时没有获得锁，会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 异常。

`notifyAll` 方法是此方法的已弃用别名。

## 信号量对象¶

这是计算机科学史上最古老的同步原语之一，早期的荷兰科学家 Edsger W. Dijkstra 发明了它。（他使用名称 `P()` 和 `V()` 而不是 `acquire()` 和 `release()` ）。

一个信号量管理一个内部计数器，该计数器因 `acquire()` 方法的调用而递减，因 `release()` 方法的调用而递增。 计数器的值永远不会小于零；当 `acquire()` 方法发现计数器为零时，将会阻塞，直到其它线程调用 `release()` 方法。

信号量对象也支持 上下文管理协议 。

_class _threading.Semaphore( _value =1_)¶
~~~
    

~~~
该类实现信号量对象。信号量对象管理一个原子性的计数器，代表 `release()` 方法的调用次数减去 `acquire()` 的调用次数再加上一个初始值。如果需要， `acquire()` 方法将会阻塞直到可以返回而不会使得计数器变成负数。在没有显式给出 _value_ 的值时，默认为1。

可选参数 _value_ 赋予内部计数器初始值，默认值为 `1` 。如果 _value_ 被赋予小于0的值，将会引发 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。

在 3.3 版本发生变更: 从工厂函数变为类。

acquire( _blocking =True_, _timeout =None_)¶
~~~
    

~~~
获取一个信号量。

在不带参数的情况下调用时：

  * 如果在进入时内部计数器的值大于零，则将其减一并立即返回 `True`。

  * 如果在进入时内部计数器的值为零，则将会阻塞直到被对 `release()` 的调用唤醒。 一旦被唤醒（并且计数器的值大于 0），则将计数器减 1 并返回 `True`。 每次对 `release()` 的调用将只唤醒一个线程。 线程被唤醒的次序是不可确定的。

当 _blocking_ 设置为 `False` 时调用，不会阻塞。 如果没有参数的调用会阻塞，立即返回 `False`；否则，做与无参数调用相同的事情时返回 `True`。

当发起调用时如果 _timeout_ 不为 `None`，则它将阻塞最多 _timeout_ 秒。 请求在此时段时未能成功完成获取则将返回 `False`。 在其他情况下返回 `True`。

在 3.2 版本发生变更: 新的 _timeout_ 形参。

release( _n =1_)¶
~~~
    

~~~
释放一个信号量，将内部计数器的值增加 _n_ 。 当进入时值为零且有其他线程正在等待它再次变为大于零时，则唤醒那 _n_ 个线程。

在 3.9 版本发生变更: 增加了 _n_ 形参以一次性释放多个等待线程。

_class _threading.BoundedSemaphore( _value =1_)¶
~~~
    

~~~
该类实现有界信号量。有界信号量通过检查以确保它当前的值不会超过初始值。如果超过了初始值，将会引发 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。在大多情况下，信号量用于保护数量有限的资源。如果信号量被释放的次数过多，则表明出现了错误。没有指定时， _value_ 的值默认为1。

在 3.3 版本发生变更: 从工厂函数变为类。

### `Semaphore` 例子¶

信号量通常用于保护数量有限的资源，例如数据库服务器。在资源数量固定的任何情况下，都应该使用有界信号量。在生成任何工作线程前，应该在主线程中初始化信号量。
~~~
    
    
~~~
maxconnections = 5
# ...
pool_sema = BoundedSemaphore(value=maxconnections)
~~~

工作线程生成后，当需要连接服务器时，这些线程将调用信号量的 acquire 和 release 方法：

    
    
~~~
with pool_sema:
    conn = connectdb()
    try:
        # ... use connection ...
    finally:
        conn.close()
~~~

使用有界信号量能减少这种编程错误：信号量的释放次数多于其请求次数。

## 事件对象¶

这是线程之间通信的最简单机制之一：一个线程发出事件信号，而其他线程等待该信号。

一个事件对象管理一个内部标识，调用 `set()` 方法可将其设置为 true ，调用 `clear()` 方法可将其设置为 false ，调用 `wait()` 方法将进入阻塞直到标识为 true 。

_class _threading.Event¶

    

~~~
实现事件对象的类。事件对象管理一个内部标识，调用 `set()` 方法可将其设置为true。调用 `clear()` 方法可将其设置为 false 。调用 `wait()` 方法将进入阻塞直到标识为true。这个标识初始时为 false 。

在 3.3 版本发生变更: 从工厂函数变为类。

is_set()¶
~~~
    

~~~
当且仅当内部标识为 true 时返回 `True` 。

`isSet` 方法是此方法的已弃用别名。

set()¶
~~~
    

~~~
将内部标识设置为 true 。所有正在等待这个事件的线程将被唤醒。当标识为 true 时，调用 `wait()` 方法的线程不会被被阻塞。

clear()¶
~~~
    

~~~
将内部标识设置为 false 。之后调用 `wait()` 方法的线程将会被阻塞，直到调用 `set()` 方法将内部标识再次设置为 true 。

wait( _timeout =None_)¶
~~~
    

~~~
阻塞线程直到内部变量为 true 。如果调用时内部标识为 true，将立即返回。否则将阻塞线程，直到调用 `set()` 方法将标识设置为 true 或者发生可选的超时。

当提供了timeout参数且不是 `None` 时，它应该是一个浮点数，代表操作的超时时间，以秒为单位（可以为小数）。

当且仅当内部旗标在等待调用之前或者等待开始之后被设为真值时此方法将返回 `True`，也就是说，它将总是返回 `True` 除非设定了超时且操作发生了超时。

在 3.1 版本发生变更: 很明显，方法总是返回 `None`。

## 定时器对象¶

此类表示一个操作应该在等待一定的时间之后运行 --- 相当于一个定时器。 `Timer` 类是 `Thread` 类的子类，因此可以像一个自定义线程一样工作。

与线程一样，定时器也是通过调用其 `Timer.start` 方法来启动的。 定时器可以通过调用 `cancel()` 方法来停止（在其动作开始之前）。 定时器在执行其行动之前要等待的时间间隔可能与用户指定的时间间隔不完全相同。

例如：
~~~
    
    
~~~python
def hello():
    print("hello, world")

t = Timer(30.0, hello)
t.start()  # after 30 seconds, "hello, world" will be printed
~~~

_class _threading.Timer( _interval_ , _function_ , _args =None_, _kwargs =None_)¶

    

~~~
创建一个定时器，在经过 _interval_ 秒的间隔事件后，将会用参数 _args_ 和关键字参数 _kwargs_ 调用 _function_ 。如果 _args_ 为 `None` （默认值），则会使用一个空列表。如果 _kwargs_ 为 `None` （默认值），则会使用一个空字典。

在 3.3 版本发生变更: 从工厂函数变为类。

cancel()¶
~~~
    

~~~
停止定时器并取消执行计时器将要执行的操作。仅当计时器仍处于等待状态时有效。

## 栅栏对象¶

在 3.2 版本加入.

栅栏类提供一个简单的同步原语，用于应对固定数量的线程需要彼此相互等待的情况。线程调用 `wait()` 方法后将阻塞，直到所有线程都调用了 `wait()` 方法。此时所有线程将被同时释放。

栅栏对象可以被多次使用，但进程的数量不能改变。

这是一个使用简便的方法实现客户端进程与服务端进程同步的例子：
~~~
    
    
~~~
b = Barrier(2, timeout=5)

def server():
    start_server()
    b.wait()
    while True:
        connection = accept_connection()
        process_server_connection(connection)

def client():
    b.wait()
    while True:
        connection = make_connection()
        process_client_connection(connection)
~~~

_class _threading.Barrier( _parties_ , _action =None_, _timeout =None_)¶

    

~~~
创建一个需要 _parties_ 个线程的栅栏对象。如果提供了可调用的 _action_ 参数，它会在所有线程被释放时在其中一个线程中自动调用。 _timeout_ 是默认的超时时间，如果没有在 `wait()` 方法中指定超时时间的话。

wait( _timeout =None_)¶
~~~
    

~~~
冲出栅栏。当栅栏中所有线程都已经调用了这个函数，它们将同时被释放。如果提供了 _timeout_ 参数，这里的 _timeout_ 参数优先于创建栅栏对象时提供的 _timeout_ 参数。

函数返回值是一个整数，取值范围在0到 _parties_ \-- 1，在每个线程中的返回值不相同。可用于从所有线程中选择唯一的一个线程执行一些特别的工作。例如：
~~~
    
    
~~~
i = barrier.wait()
if i == 0:
    # Only one thread needs to print this
    print("passed the barrier")
~~~

如果创建栅栏对象时在构造函数中提供了 _action_ 参数，它将在其中一个线程释放前被调用。如果此调用引发了异常，栅栏对象将进入损坏态。

如果发生了超时，栅栏对象将进入破损态。

如果栅栏对象进入破损态，或重置栅栏时仍有线程等待释放，将会引发 `BrokenBarrierError` 异常。

reset()¶

    

~~~
重置栅栏为默认的初始态。如果栅栏中仍有线程等待释放，这些线程将会收到 `BrokenBarrierError` 异常。

请注意使用此函数时，如果存在状态未知的其他线程，则可能需要执行外部同步。 如果栅栏已损坏则最好将其废弃并新建一个。

abort()¶
~~~
    

~~~
使栅栏处于损坏状态。 这将导致任何现有和未来对 `wait()` 的调用失败并引发 `BrokenBarrierError`。 例如可以在需要中止某个线程时使用此方法，以避免应用程序的死锁。

更好的方式是：创建栅栏时提供一个合理的超时时间，来自动避免某个线程出错。

parties¶
~~~
    

~~~
冲出栅栏所需要的线程数量。

n_waiting¶
~~~
    

~~~
当前时刻正在栅栏中阻塞的线程数量。

broken¶
~~~
    

~~~
一个布尔值，值为 `True` 表明栅栏为破损态。

_exception _threading.BrokenBarrierError¶
~~~
    

~~~
异常类，是 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 异常的子类，在 `Barrier` 对象重置时仍有线程阻塞时和对象进入破损态时被引发。

## 在 `with` 语句中使用锁、条件和信号量¶

本模块提供的所有具有 `acquire` 和 `release` 方法的对象都可用作 [`with`](../reference/compound_stmts.md#with) 语句的上下文管理器。 进入语句块时将调用 `acquire` 方法，退出语句块时将调用 `release` 方法。 因此，下面的代码段:
~~~
    
    
~~~
with some_lock:
    # do something...
~~~

相当于:

    
    
~~~
some_lock.acquire()
try:
    # do something...
finally:
    some_lock.release()
~~~

现在 `Lock` 、 `RLock` 、 `Condition` 、 `Semaphore` 和 `BoundedSemaphore` 对象可以用作 [`with`](8.%20复合语句.md#with) 语句的上下文管理器。

