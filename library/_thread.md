# `_thread` \--- 底层多线程 API¶

* * *

该模块提供了操作多个线程（也被称为 _轻量级进程_ 或 _任务_ ）的底层原语 —— 多个控制线程共享全局数据空间。为了处理同步问题，也提供了简单的锁机制（也称为 _互斥锁_ 或 _二进制信号_ ）。[`threading`](threading.md#module-threading "threading: Thread-based parallelism.") 模块基于该模块提供了更易用的高级多线程 API。

在 3.7 版本发生变更: 这个模块曾经为可选项，但现在总是可用。

这个模块定义了以下常量和函数：

_exception __thread.error¶

    

~~~
发生线程相关错误时抛出。

在 3.3 版本发生变更: 现在是内建异常 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 的别名。

_thread.LockType¶
~~~
    

~~~
锁对象的类型。

_thread.start_new_thread( _function_ , _args_ [, _kwargs_ ])¶
~~~
    

~~~
开启一个新线程并返回其标识。 线程执行函数 _function_ 并附带参数列表 _args_ (必须是元组)。 可选的 _kwargs_ 参数指定一个关键字参数字典。

当函数返回时，线程会静默地退出。

当函数因某个未处理异常而终结时，[`sys.unraisablehook()`](sys.md#sys.unraisablehook "sys.unraisablehook") 会被调用以处理异常。 钩子参数的 _object_ 属性为 _function_ 。 在默认情况下，会打印堆栈回溯然后该线程将退出（但其他线程会继续运行）。

当函数引发 [`SystemExit`](exceptions.md#SystemExit "SystemExit") 异常时，它会被静默地忽略。

引发一个 [审计事件](sys.md#auditing) `_thread.start_new_thread`，附带参数 `function`, `args`, `kwargs`。

在 3.8 版本发生变更: 现在会使用 [`sys.unraisablehook()`](sys.md#sys.unraisablehook "sys.unraisablehook") 来处理未处理的异常。

_thread.interrupt_main( _signum =signal.SIGINT_, _/_ )¶
~~~
    

~~~
模拟一个信号到达主线程的效果。 线程可使用此函数来打断主线程，虽然并不保证打断将立即发生。

如果给出 _signum_ ，则表示要模拟的信号的编号。 如果未给出 _signum_ ，则将模拟 [`signal.SIGINT`](signal.md#signal.SIGINT "signal.SIGINT")。

如果给出的信号未被 Python 处理 (它被设为 [`signal.SIG_DFL`](signal.md#signal.SIG_DFL "signal.SIG_DFL") 或 [`signal.SIG_IGN`](signal.md#signal.SIG_IGN "signal.SIG_IGN"))，则此函数将不做任何操作。

在 3.10 版本发生变更: 添加了 _signum_ 参数来定制信号的编号。

备注

这并不会发出对应的信号而是将一个调用排入关联处理句柄的计划任务（如果句柄存在的话）。 如果你想要真的发出信号，请使用 [`signal.raise_signal()`](signal.md#signal.raise_signal "signal.raise_signal")。

_thread.exit()¶
~~~
    

~~~
抛出 [`SystemExit`](exceptions.md#SystemExit "SystemExit") 异常。如果没有捕获的话，这个异常会使线程退出。

_thread.allocate_lock()¶
~~~
    

~~~
返回一个新的锁对象。锁中的方法在后面描述。初始情况下锁处于解锁状态。

_thread.get_ident()¶
~~~
    

~~~
返回当前线程的 “线程标识符”。它是一个非零的整数。它的值没有直接含义，主要是用作 magic cookie，比如作为含有线程相关数据的字典的索引。线程标识符可能会在线程退出，新线程创建时被复用。

_thread.get_native_id()¶
~~~
    

~~~
返回内核分配给当前线程的原生集成线程 ID。 这是一个非负整数。 它的值可被用来在整个系统中唯一地标识这个特定线程（直到线程终结，在那之后该值可能会被 OS 回收再利用）。

[可用性](intro.md#availability): Windows, FreeBSD, Linux, macOS, OpenBSD, NetBSD, AIX, DragonFlyBSD。

在 3.8 版本加入.

_thread.stack_size([ _size_ ])¶
~~~
    

~~~
返回创建线程时使用的堆栈大小。可选参数 _size_ 指定之后新建的线程的堆栈大小，而且一定要是0（根据平台或者默认配置）或者最小是32,768(32KiB)的一个正整数。如果 _size_ 没有指定，默认是0。如果不支持改变线程堆栈大小，会抛出 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 错误。如果指定的堆栈大小不合法，会抛出 [`ValueError`](exceptions.md#ValueError "ValueError") 错误并且不会修改堆栈大小。32KiB是当前最小的能保证解释器有足够堆栈空间的堆栈大小。需要注意的是部分平台对于堆栈大小会有特定的限制，例如要求大于32KiB的堆栈大小或者需要根据系统内存页面的整数倍进行分配 - 应当查阅平台文档有关详细信息（4KiB页面比较普遍，在没有更具体信息的情况下，建议的方法是使用4096的倍数作为堆栈大小）。

[可用性](intro.md#availability): Windows, pthreads。

带有 POSIX 线程支持的 Unix 平台。

_thread.TIMEOUT_MAX¶
~~~
    

~~~
[`Lock.acquire`](threading.md#threading.Lock.acquire "threading.Lock.acquire") 的 _timeout_ 形参所允许的最大值。 指定大于该值的 timeout 将引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError")。

在 3.2 版本加入.

锁对象有以下方法：

lock.acquire( _blocking =True_, _timeout =-1_)¶
~~~
    

~~~
没有任何可选参数时，该方法无条件申请获得锁，有必要的话会等待其他线程释放锁（同时只有一个线程能获得锁 —— 这正是锁存在的原因）。

如果提供了 _blocking_ 参数，具体的行为将取决于它的值：如果它为 False，则只在能够立即获取到锁而无需等待时才会获取，而如果它为 True，则会与上面一样无条件地获取锁。

如果提供了浮点数形式的 _timeout_ 参数且为正值，它将指明在返回之前的最大等待秒数。 负的 _timeout_ 参数值表示无限期的等待。 如果 _blocking_ 为 False 则你不能指定 _timeout_ 。

如果成功获取到所会返回 `True`，否则返回 `False`。

在 3.2 版本发生变更: 新的 _timeout_ 形参。

在 3.2 版本发生变更: 现在获取锁的操作可以被 POSIX 信号中断。

lock.release()¶
~~~
    

~~~
释放锁。锁必须已经被获取过，但不一定是同一个线程获取的。

lock.locked()¶
~~~
    

~~~
返回锁的状态：如果已被某个线程获取，返回 `True`，否则返回 `False`。

除了这些方法之外，锁对象也可以通过 [`with`](../reference/compound_stmts.md#with) 语句使用，例如：
~~~
    
    
~~~
import _thread

a_lock = _thread.allocate_lock()

with a_lock:
    print("a_lock is locked while this executes")
~~~

**注意事项：**

>   * 线程与中断奇怪地交互：[`KeyboardInterrupt`](exceptions.md#KeyboardInterrupt "KeyboardInterrupt") 异常可能会被任意一个线程捕获。（如果 [`signal`](signal.md#module-signal "signal: Set handlers for asynchronous events.") 模块可用的话，中断总是会进入主线程。）

  * 调用 [`sys.exit()`](sys.md#sys.exit "sys.exit") 或是抛出 [`SystemExit`](exceptions.md#SystemExit "SystemExit") 异常等效于调用 `_thread.exit()`。

  * 不可能中断锁上的 [`acquire()`](threading.md#threading.Lock.acquire "threading.Lock.acquire") 方法 --- [`KeyboardInterrupt`](exceptions.md#KeyboardInterrupt "KeyboardInterrupt") 异常将在获取锁之后发生。

  * 当主线程退出时，由系统决定其他线程是否存活。在大多数系统中，这些线程会直接被杀掉，不会执行 [`try`](../reference/compound_stmts.md#try) ... [`finally`](../reference/compound_stmts.md#finally) 语句，也不会执行对象析构函数。

  * 当主线程退出时，不会进行正常的清理工作（除非使用了 [`try`](../reference/compound_stmts.md#try) ... [`finally`](../reference/compound_stmts.md#finally) 语句），标准 I/O 文件也不会刷新。

