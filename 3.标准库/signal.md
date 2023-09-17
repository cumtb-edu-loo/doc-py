# `signal` \--- 设置异步事件处理程序¶

**源代码:** [Lib/signal.py](https://github.com/python/cpython/tree/3.12/Lib/signal.py)

* * *

该模块提供了在 Python 中使用信号处理程序的机制。

## 一般规则¶

`signal.signal()` 函数允许定义在接收到信号时执行的自定义处理程序。少量的默认处理程序已经设置： `SIGPIPE` 被忽略（因此管道和套接字上的写入错误可以报告为普通的 Python 异常）以及如果父进程没有更改 `SIGINT` ，则其会被翻译成 [`KeyboardInterrupt`](3.标准库/exceptions.md#KeyboardInterrupt "KeyboardInterrupt") 异常。

一旦设置，特定信号的处理程序将保持安装，直到它被显式重置（ Python 模拟 BSD 样式接口而不管底层实现），但 `SIGCHLD` 的处理程序除外，它遵循底层实现。

在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上，信号是通过模拟实现的因而其行为有所不同。 某些函数和信号在这些平台上将不可用。

### 执行 Python 信号处理程序¶

Python 信号处理程序不会在低级（ C ）信号处理程序中执行。相反，低级信号处理程序设置一个标志，告诉 [virtual machine](../glossary.md#term-virtual-machine) 稍后执行相应的 Python 信号处理程序（例如在下一个 [bytecode](../glossary.md#term-bytecode) 指令）。这会导致：

  * 捕获同步错误是没有意义的，例如 `SIGFPE` 或 `SIGSEGV` ，它们是由 C 代码中的无效操作引起的。Python 将从信号处理程序返回到 C 代码，这可能会再次引发相同的信号，导致 Python 显然的挂起。 从Python 3.3开始，你可以使用 [`faulthandler`](faulthandler.md#module-faulthandler "faulthandler: Dump the Python traceback.") 模块来报告同步错误。

  * 纯 C 中实现的长时间运行的计算（例如在大量文本上的正则表达式匹配）可以在任意时间内不间断地运行，而不管接收到任何信号。计算完成后将调用 Python 信号处理程序。

  * 如果处理句柄引发了异常，它将在主线程中“凭空”被引发。 请参阅 下面的注释 讨论相关细节。

### 信号与线程¶

Python 信号处理程序总是会在主 Python 主解释器的主线程中执行，即使信号是在另一个线程中接收的。 这意味着信号不能被用作线程间通信的手段。 你可以改用 [`threading`](threading.md#module-threading "threading: Thread-based parallelism.") 模块中的同步原语。

此外，只有主解释器的主线程才被允许设置新的信号处理程序。

## 模块内容¶

在 3.5 版本发生变更: 下面列出的信号 (SIG*), 处理句柄 (`SIG_DFL`, `SIG_IGN`) 和信号掩码 (`SIG_BLOCK`, `SIG_UNBLOCK`, `SIG_SETMASK`) 相关的常量会被转成 [`enums`](3.标准库/enum.md#enum.IntEnum "enum.IntEnum") (分别为 `Signals`, `Handlers` 和 `Sigmasks`)。 `getsignal()`, `pthread_sigmask()`, `sigpending()` 和 `sigwait()` 函数将以 `Signals` 对象形式返回人类可读的 [`enums`](3.标准库/enum.md#enum.IntEnum "enum.IntEnum")。

signal 模块定义了三个枚举:

_class _signal.Signals¶

    

~~~
[`enum.IntEnum`](enum.md#enum.IntEnum "enum.IntEnum") 是 SIG* 常量和 CTRL_* 常量的多项集。

在 3.5 版本加入.

_class _signal.Handlers¶
~~~
    

~~~
[`enum.IntEnum`](enum.md#enum.IntEnum "enum.IntEnum") 是常量 `SIG_DFL` 和 `SIG_IGN` 的多项集。

在 3.5 版本加入.

_class _signal.Sigmasks¶
~~~
    

~~~
[`enum.IntEnum`](enum.md#enum.IntEnum "enum.IntEnum") 是常量 `SIG_BLOCK`, `SIG_UNBLOCK` 和 `SIG_SETMASK` 的多项集。

[可用性](intro.md#availability): Unix。

请参阅手册页面 _[sigprocmask(2)](https://manpages.debian.org/sigprocmask\(2\))_ 和 _[pthread_sigmask(3)](https://manpages.debian.org/pthread_sigmask\(3\))_ 了解更多信息。

在 3.5 版本加入.

在 `signal` 模块中定义的变量是：

signal.SIG_DFL¶
~~~
    

~~~
这是两种标准信号处理选项之一；它只会执行信号的默认函数。 例如，在大多数系统上，对于 `SIGQUIT` 的默认操作是转储核心并退出，而对于 `SIGCHLD` 的默认操作是简单地忽略它。

signal.SIG_IGN¶
~~~
    

~~~
这是另一个标准信号处理程序，它将简单地忽略给定的信号。

signal.SIGABRT¶
~~~
    

~~~
来自 _[abort(3)](https://manpages.debian.org/abort\(3\))_ 的中止信号。

signal.SIGALRM¶
~~~
    

~~~
来自 _[alarm(2)](https://manpages.debian.org/alarm\(2\))_ 的计时器信号。

[可用性](intro.md#availability): Unix。

signal.SIGBREAK¶
~~~
    

~~~
来自键盘的中断 (CTRL + BREAK)。

[可用性](intro.md#availability): Windows。

signal.SIGBUS¶
~~~
    

~~~
总线错误 (非法的内存访问)。

[可用性](intro.md#availability): Unix。

signal.SIGCHLD¶
~~~
    

~~~
子进程被停止或终结。

[可用性](intro.md#availability): Unix。

signal.SIGCLD¶
~~~
    

~~~
`SIGCHLD` 的别名。

signal.SIGCONT¶
~~~
    

~~~
如果进程当前已停止则继续执行它

[可用性](intro.md#availability): Unix。

signal.SIGFPE¶
~~~
    

~~~
浮点异常。 例如除以零。

参见

当除法或求余运算的第二个参数为零时会引发 [`ZeroDivisionError`](exceptions.md#ZeroDivisionError "ZeroDivisionError") 。

signal.SIGHUP¶
~~~
    

~~~
在控制终端上检测到挂起或控制进程的终止。

[可用性](intro.md#availability): Unix。

signal.SIGILL¶
~~~
    

~~~
非法指令。

signal.SIGINT¶
~~~
    

~~~
来自键盘的中断 (CTRL + C)。

默认的动作是引发 [`KeyboardInterrupt`](exceptions.md#KeyboardInterrupt "KeyboardInterrupt")。

signal.SIGKILL¶
~~~
    

~~~
终止信号。

它不能被捕获、阻塞或忽略。

[可用性](intro.md#availability): Unix。

signal.SIGPIPE¶
~~~
    

~~~
损坏的管道：写入到没有读取器的管道。

默认的动作是忽略此信号。

[可用性](intro.md#availability): Unix。

signal.SIGSEGV¶
~~~
    

~~~
段错误：无效的内存引用。

signal.SIGSTKFLT¶
~~~
    

~~~
> 协处理器上的栈错误。 Linux 内核不会引发此信号：它只能在用户空间中被引发。

[可用性](intro.md#availability): Linux。

在信号可用的架构上。 参见手册页面 _[signal(7)](https://manpages.debian.org/signal\(7\))_ 了解更多信息。

在 3.11 版本加入.

signal.SIGTERM¶
~~~
    

~~~
终结信号。

signal.SIGUSR1¶
~~~
    

~~~
用户自定义信号 1。

[可用性](intro.md#availability): Unix。

signal.SIGUSR2¶
~~~
    

~~~
用户自定义信号 2。

[可用性](intro.md#availability): Unix。

signal.SIGWINCH¶
~~~
    

~~~
窗口调整大小信号。

[可用性](intro.md#availability): Unix。

SIG*
~~~
    

~~~
所有信号编号都是符号化定义的。 例如，挂起信号被定义为 `signal.SIGHUP`；变量的名称与 C 程序中使用的名称相同，具体见 `<signal.h>`。 '`signal()`' 的 Unix 手册页面列出了现有的信号 (在某些系统上这是 _[signal(2)](https://manpages.debian.org/signal\(2\))_ ，在其他系统中此列表则是在 _[signal(7)](https://manpages.debian.org/signal\(7\))_ 中)。 请注意并非所有系统都会定义相同的信号名称集；只有系统所定义的名称才会由此模块来定义。

signal.CTRL_C_EVENT¶
~~~
    

~~~
对应于 ``Ctrl`+`C`` 击键事件的信号。此信号只能用于 [`os.kill()`](os.md#os.kill "os.kill") 。

[可用性](intro.md#availability): Windows。

在 3.2 版本加入.

signal.CTRL_BREAK_EVENT¶
~~~
    

~~~
对应于 ``Ctrl`+`Break`` 击键事件的信号。此信号只能用于 [`os.kill()`](os.md#os.kill "os.kill") 。

[可用性](intro.md#availability): Windows。

在 3.2 版本加入.

signal.NSIG¶
~~~
    

~~~
比最高的信号编号值多一。 请使用 `valid_signals()` 来获取有效的信号编号。

signal.ITIMER_REAL¶
~~~
    

~~~
实时递减间隔计时器，并在到期时发送 `SIGALRM` 。

signal.ITIMER_VIRTUAL¶
~~~
    

~~~
仅在进程执行时递减间隔计时器，并在到期时发送 SIGVTALRM 。

signal.ITIMER_PROF¶
~~~
    

~~~
当进程执行时以及当系统替进程执行时都会减小间隔计时器。 这个计时器与 ITIMER_VIRTUAL 相配结，通常被用于分析应用程序在用户和内核空间中花费的时间。 SIGPROF 会在超期时被发送。

signal.SIG_BLOCK¶
~~~
    

~~~
`pthread_sigmask()` 的 _how_ 形参的一个可能的值，表明信号将会被阻塞。

在 3.3 版本加入.

signal.SIG_UNBLOCK¶
~~~
    

~~~
`pthread_sigmask()` 的 _how_ 形参的是个可能的值，表明信号将被解除阻塞。

在 3.3 版本加入.

signal.SIG_SETMASK¶
~~~
    

~~~
`pthread_sigmask()` 的 _how_ 形参的一个可能的值，表明信号掩码将要被替换。

在 3.3 版本加入.

`signal` 模块定义了一个异常:

_exception _signal.ItimerError¶
~~~
    

~~~
作为来自下层 `setitimer()` 或 `getitimer()` 实现错误的信号被引发。 如果将无效的定时器或负的时间值传给 `setitimer()` 就导致这个错误。 此错误是 [`OSError`](exceptions.md#OSError "OSError") 的子类型。

在 3.3 版本加入: 此错误是 [`IOError`](exceptions.md#IOError "IOError") 的子类型，现在则是 [`OSError`](exceptions.md#OSError "OSError") 的别名。

`signal` 模块定义了以下函数:

signal.alarm( _time_ )¶
~~~
    

~~~
如果 _time_ 值非零，则此函数将要求将一个 `SIGALRM` 信号在 _time_ 秒内发往进程。 任何在之前排入计划的警报都会被取消（在任何时刻都只能有一个警报被排入计划）。 后续的返回值将是任何之前设置的警报被传入之前的秒数。 如果 _time_ 值为零，则不会将任何警报排入计划，并且任何已排入计划的警报都会被取消。 如果返回值为零，则目前没有任何警报被排入计划。

[可用性](intro.md#availability): Unix。

请参阅手册页面 _[alarm(2)](https://manpages.debian.org/alarm\(2\))_ 了解更多信息。

signal.getsignal( _signalnum_ )¶
~~~
    

~~~
返回当前用于信号 _signalnum_ 的信号处理程序。 返回值可以是一个 Python 可调用对象，或是特殊值 `signal.SIG_IGN`, `signal.SIG_DFL` 或 [`None`](constants.md#None "None") 之一。 在这里，`signal.SIG_IGN` 表示信号在之前被忽略，`signal.SIG_DFL` 表示之前在使用默认的信号处理方式，而 `None` 表示之前的信号处理程序未由 Python 安装。

signal.strsignal( _signalnum_ )¶
~~~
    

~~~
返回信号 _signalnum_ 的描述信息，例如 "Interrupt" 对应 `SIGINT`。 如果 _signalnum_ 没有描述信息则返回 [`None`](constants.md#None "None")。 如果 _signalnum_ 无效则引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

在 3.8 版本加入.

signal.valid_signals()¶
~~~
    

~~~
返回本平台上的有效信号编号集。 这可能会少于 `range(1, NSIG)`，如果某些信号被系统保留作为内部使用的话。

在 3.8 版本加入.

signal.pause()¶
~~~
    

~~~
使进程休眠直至接收到一个信号；然后将会调用适当的处理程序。 返回空值。

[可用性](intro.md#availability): Unix。

请参阅手册页面 _[signal(2)](https://manpages.debian.org/signal\(2\))_ 了解更多信息。

另请参阅 `sigwait()`, `sigwaitinfo()`, `sigtimedwait()` 和 `sigpending()`。

signal.raise_signal( _signum_ )¶
~~~
    

~~~
向调用方进程发送一个信号。 返回空值。

在 3.8 版本加入.

signal.pidfd_send_signal( _pidfd_ , _sig_ , _siginfo =None_, _flags =0_)¶
~~~
    

~~~
发送信号 _sig_ 到文件描述符 _pidfd_ 所指向的进程。 Python 目前不支持 _siginfo_ 形参；它必须为 `None`。 提供 _flags_ 参数是为了将来扩展；当前未定义旗标值。

更多信息请参阅 _[pidfd_send_signal(2)](https://manpages.debian.org/pidfd_send_signal\(2\))_ 手册页面。

[可用性](intro.md#availability): Linux >= 5.1

在 3.9 版本加入.

signal.pthread_kill( _thread_id_ , _signalnum_ )¶
~~~
    

~~~
将信号 _signalnum_ 发送至与调用者在同一进程中另一线程 _thread_id_ 。 目标线程可被用于执行任何代码（Python或其它）。 但是，如果目标线程是在执行 Python 解释器，则 Python 信号处理程序将 由主解释器的主线程来执行。 因此，将信号发送给特定 Python 线程的唯一作用在于强制让一个正在运行的系统调用失败并抛出 [`InterruptedError`](exceptions.md#InterruptedError "InterruptedError")。

使用 [`threading.get_ident()`](threading.md#threading.get_ident "threading.get_ident") 或 [`threading.Thread`](threading.md#threading.Thread "threading.Thread") 对象的 [`ident`](threading.md#threading.Thread.ident "threading.Thread.ident") 属性为 _thread_id_ 获取合适的值。

如果 _signalnum_ 为 0，则不会发送信号，但仍然会执行错误检测；这可被用来检测目标线程是否仍在运行。

引发一个 [审计事件](sys.md#auditing) `signal.pthread_kill`，附带参数 `thread_id`, `signalnum`。

[可用性](intro.md#availability): Unix。

请参阅手册页面 _[pthread_kill(3)](https://manpages.debian.org/pthread_kill\(3\))_ 了解更多信息。

另请参阅 [`os.kill()`](os.md#os.kill "os.kill")。

在 3.3 版本加入.

signal.pthread_sigmask( _how_ , _mask_ )¶
~~~
    

~~~
获取和/或修改调用方线程的信号掩码。 信号掩码是一组传送过程目前为调用者而阻塞的信号集。 返回旧的信号掩码作为一组信号。

该调用的行为取决于 _how_ 的值，具体见下。

  * `SIG_BLOCK`: 被阻塞信号集是当前集与 _mask_ 参数的并集。

  * `SIG_UNBLOCK`: _mask_ 中的信号会从当前已阻塞信号集中被移除。 允许尝试取消对一个非阻塞信号的阻塞。

  * `SIG_SETMASK`: 已阻塞信号集会被设为 _mask_ 参数的值。

_mask_ 是一个信号编号集合 (例如 {`signal.SIGINT`, `signal.SIGTERM`})。 请使用 `valid_signals()` 表示包含所有信号的完全掩码。

例如，`signal.pthread_sigmask(signal.SIG_BLOCK, [])` 会读取调用方线程的信号掩码。

`SIGKILL` 和 `SIGSTOP` 不能被阻塞。

[可用性](intro.md#availability): Unix。

请参阅手册页面 _[sigprocmask(2)](https://manpages.debian.org/sigprocmask\(2\))_ 和 _[pthread_sigmask(3)](https://manpages.debian.org/pthread_sigmask\(3\))_ 了解更多信息。

另请参阅 `pause()`, `sigpending()` 和 `sigwait()`。

在 3.3 版本加入.

signal.setitimer( _which_ , _seconds_ , _interval =0.0_)¶
~~~
    

~~~
设置由 _which_ 指明的给定间隔计时器 (`signal.ITIMER_REAL`, `signal.ITIMER_VIRTUAL` 或 `signal.ITIMER_PROF` 之一) 在 _seconds_ 秒 (接受浮点数值，为与 `alarm()` 之差) 之后开始并在每 _interval_ 秒间隔时 (如果 _interval_ 不为零) 启动。 由 _which_ 指明的间隔计时器可通过将 _seconds_ 设为零来清空。

当一个间隔计时器启动时，会有信号发送至进程。 所发送的具体信号取决于所使用的计时器；`signal.ITIMER_REAL` 将发送 `SIGALRM`, `signal.ITIMER_VIRTUAL` 将发送 `SIGVTALRM`, 而 `signal.ITIMER_PROF` 将发送 `SIGPROF`.

原有的值会以元组: (delay, interval) 的形式被返回。

尝试传入无效的计时器将导致 `ItimerError`。

[可用性](intro.md#availability): Unix。

signal.getitimer( _which_ )¶
~~~
    

~~~
返回由 _which_ 指明的给定间隔计时器当前的值。

[可用性](intro.md#availability): Unix。

signal.set_wakeup_fd( _fd_ , _*_ , _warn_on_full_buffer =True_)¶
~~~
    

~~~
将唤醒文件描述符设为 _fd_ 。 当接收到信号时，会将信号编号以单个字节的形式写入 fd。 这可被其它库用来唤醒一次 poll 或 select 调用，以允许该信号被完全地处理。

原有的唤醒 fd 会被返回（或者如果未启用文件描述符唤醒则返回 -1）。 如果 _fd_ 为 -1，文件描述符唤醒会被禁用。 如果不为 -1，则 _fd_ 必须为非阻塞型。 需要由库来负责在重新调用 poll 或 select 之前从 _fd_ 移除任何字节数据。

当启用线程用时，此函数只能从 主解释器的主线程 被调用；尝试从另一线程调用它将导致 [`ValueError`](exceptions.md#ValueError "ValueError") 异常被引发。

使用此函数有两种通常的方式。 在两种方式下，当有信号到达时你都是用 fd 来唤醒，但之后它们在确定达到的一个或多个信号 _which_ 时存在差异。

在第一种方式下，我们从 fd 的缓冲区读取数据，这些字节值会给你信号编号。 这种方式很简单，但在少数情况下会发生问题：通常 fd 将有缓冲区空间大小限制，如果信号到达得太多且太快，缓冲区可能会爆满，有些信号可能丢失。 如果你使用此方式，则你应当设置 `warn_on_full_buffer=True`，当信号丢失时这至少能将警告消息打印到 stderr。

在第二种方式下，我们 _只会_ 将唤醒 fd 用于唤醒，而忽略实际的字节值。 在此情况下，我们所关心的只有 fd 的缓冲区为空还是不为空；爆满的缓冲区完全不会导致问题。 如果你使用此方式，则你应当设置 `warn_on_full_buffer=False`，这样你的用户就不会被虚假的警告消息所迷惑。

在 3.5 版本发生变更: 在 Windows 上，此函数现在也支持套接字处理。

在 3.7 版本发生变更: 添加了 `warn_on_full_buffer` 形参。

signal.siginterrupt( _signalnum_ , _flag_ )¶
~~~
    

~~~
更改系统调用重启行为：如果 _flag_ 为 [`False`](constants.md#False "False")，系统调用将在被信号 _signalnum_ 中断时重启，否则系统调用将被中断。 返回空值。

[可用性](intro.md#availability): Unix。

请参阅手册页面 _[siginterrupt(3)](https://manpages.debian.org/siginterrupt\(3\))_ 了解更多信息。

请注意使用 `signal()` 安装信号处理句柄将会通过隐式地调用 `siginterrupt()` 并为给定信号的 _flag_ 设置真值来将重启行为重置为可中断的。

signal.signal( _signalnum_ , _handler_ )¶
~~~
    

~~~
将信号 _signalnum_ 的处理程序设为函数 _handler_ 。 _handler_ 可以为接受两个参数（见下）的 Python 可调用对象，或者为特殊值 `signal.SIG_IGN` 或 `signal.SIG_DFL` 之一。 之前的信号处理程序将被返回（参见上文 `getsignal()` 的描述）。 （更多信息请参阅 Unix 手册页面 _[signal(2)](https://manpages.debian.org/signal\(2\))_ 。）

当启用线程用时，此函数只能从 主解释器的主线程 被调用；尝试从另一线程调用它将导致 [`ValueError`](exceptions.md#ValueError "ValueError") 异常被引发。

_handler_ 将附带两个参数调用：信号编号和当前堆栈帧 (`None` 或一个帧对象；有关帧对象的描述请参阅 [类型层级结构描述](../reference/datamodel.md#frame-objects) 或者参阅 [`inspect`](inspect.md#module-inspect "inspect: Extract information and source code from live objects.") 模块中的属性描述)。

在 Windows 上，`signal()` 调用只能附带 `SIGABRT`, `SIGFPE`, `SIGILL`, `SIGINT`, `SIGSEGV`, `SIGTERM` 或 `SIGBREAK`。 任何其他值都将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 请注意不是所有系统都定义了同样的信号名称集合；如果一个信号名称未被定义为 `SIG*` 模块层级常量则将引发 [`AttributeError`](exceptions.md#AttributeError "AttributeError")。

signal.sigpending()¶
~~~
    

~~~
检查正在等待传送给调用方线程的信号集合（即在阻塞期间被引发的信号）。 返回正在等待的信号集合。

[可用性](intro.md#availability): Unix。

请参阅手册页面 _[sigpending(2)](https://manpages.debian.org/sigpending\(2\))_ 了解更多信息。

另请参阅 `pause()`, `pthread_sigmask()` 和 `sigwait()`。

在 3.3 版本加入.

signal.sigwait( _sigset_ )¶
~~~
    

~~~
挂起调用方线程的执行直到信号集合 _sigset_ 中指定的信号之一被传送。 此函数会接受该信号（将其从等待信号列表中移除），并返回信号编号。

[可用性](intro.md#availability): Unix。

请参阅手册页面 _[sigwait(3)](https://manpages.debian.org/sigwait\(3\))_ 了解更多信息。

另请参阅 `pause()`, `pthread_sigmask()`, `sigpending()`, `sigwaitinfo()` 和 `sigtimedwait()`。

在 3.3 版本加入.

signal.sigwaitinfo( _sigset_ )¶
~~~
    

~~~
挂起调用方线程的执行直到信号集合 _sigset_ 中指定的信号之一被传送。 此函数会接受该信号并将其从等待信号列表中移除。 如果 _sigset_ 中的信号之一已经在等待调用方线程，此函数将立即返回并附带有关该信号的信息。 被传送信号的信号处理程序不会被调用。 如果该函数被某个不在 _sigset_ 中的信号中断则会引发 [`InterruptedError`](exceptions.md#InterruptedError "InterruptedError")。

返回值是一个代表 `siginfo_t` 结构体所包含数据的对象，具体为: `si_signo`, `si_code`, `si_errno`, `si_pid`, `si_uid`, `si_status`, `si_band`。

[可用性](intro.md#availability): Unix。

请参阅手册页面 _[sigwaitinfo(2)](https://manpages.debian.org/sigwaitinfo\(2\))_ 了解更多信息。

另请参阅 `pause()`, `sigwait()` 和 `sigtimedwait()`。

在 3.3 版本加入.

在 3.5 版本发生变更: 当被某个 不在 _sigset_ 中的信号中断时本函数将进行重试并且信号处理程序不会引发异常（请参阅 [**PEP 475**](https://peps.python.org/pep-0475/) 了解其理由）。

signal.sigtimedwait( _sigset_ , _timeout_ )¶
~~~
    

~~~
与 `sigwaitinfo()` 类似，但会接受一个额外的 _timeout_ 参数来指定超时限制。 如果将 _timeout_ 指定为 `0`，则会执行轮询。 如果发生超时则返回 [`None`](constants.md#None "None")。

[可用性](intro.md#availability): Unix。

请参阅手册页面 _[sigtimedwait(2)](https://manpages.debian.org/sigtimedwait\(2\))_ 了解更多信息。

另请参阅 `pause()`, `sigwait()` 和 `sigwaitinfo()`。

在 3.3 版本加入.

在 3.5 版本发生变更: 现在当此函数被某个不在 _sigset_ 中的信号中断时将以计算出的 _timeout_ 进行重试并且信号处理程序不会引发异常（请参阅 [**PEP 475**](https://peps.python.org/pep-0475/) 了解其理由）。

## 例子¶

这是一个最小示例程序。 它使用 `alarm()` 函数来限制等待打开一个文件所花费的时间；这在文件为无法开启的串行设备时会很有用处，此情况通常会导致 [`os.open()`](os.md#os.open "os.open") 无限期地挂起。 解决办法是在打开文件之前设置 5 秒钟的 alarm；如果操作耗时过长，将会发送 alarm 信号，并且处理程序会引发一个异常。
~~~
    
    
~~~
import signal, os

def handler(signum, frame):
    signame = signal.Signals(signum).name
    print(f'Signal handler called with signal {signame} ({signum})')
    raise OSError("Couldn't open device!")

# Set the signal handler and a 5-second alarm
signal.signal(signal.SIGALRM, handler)
signal.alarm(5)

# This open() may hang indefinitely
fd = os.open('/dev/ttyS0', os.O_RDWR)

signal.alarm(0)          # Disable the alarm
~~~

## 对于 SIGPIPE 的说明¶

将你的程序用管道输出到工具例如 _[head(1)](https://manpages.debian.org/head\(1\))_ 将会导致 `SIGPIPE` 信号在其标准输出的接收方提前关闭时被发送到你的进程。 这将引发一个异常例如 `BrokenPipeError: [Errno 32] Broken pipe`。 要处理这种情况，请对你的入口点进行包装以捕获此异常，如下所示:

    
    
~~~
import os
import sys

def main():
    try:
        # simulate large output (your code replaces this loop)
        for x in range(10000):
            print("y")
        # flush output here to force SIGPIPE to be triggered
        # while inside this try block.
        sys.stdout.flush()
    except BrokenPipeError:
        # Python flushes standard streams on exit; redirect remaining output
        # to devnull to avoid another BrokenPipeError at shutdown
        devnull = os.open(os.devnull, os.O_WRONLY)
        os.dup2(devnull, sys.stdout.fileno())
        sys.exit(1)  # Python exits with error code 1 on EPIPE

if __name__ == '__main__':
    main()
~~~

请不要将 `SIGPIPE` 的处置方式设为 `SIG_DFL` 以避免 [`BrokenPipeError`](3.标准库/exceptions.md#BrokenPipeError "BrokenPipeError")。 这样做还会在你的程序所写入的任何套接字连接中断时导致你的程序异常退出。

## 有关信号处理句柄和异常的注释¶

如果一个信号处理句柄引发了异常，该异常将被传播到主线程并可能在任何 [bytecode](../glossary.md#term-bytecode) 指令之后被引发，在执行期间的任何时候都可能出现 [`KeyboardInterrupt`](3.标准库/exceptions.md#KeyboardInterrupt "KeyboardInterrupt")。 大多数 Python 代码，包括标准库的代码都不能对此进行健壮性处理，因此 [`KeyboardInterrupt`](3.标准库/exceptions.md#KeyboardInterrupt "KeyboardInterrupt") (或由信号处理句柄所导致的任何其他异常) 可能会在极少数情况下使程序处于非预期的状态。

为了展示这个问题，请考虑以下代码:

    
    
~~~
class SpamContext:
    def __init__(self):
        self.lock = threading.Lock()

    def __enter__(self):
        # If KeyboardInterrupt occurs here, everything is fine
        self.lock.acquire()
        # If KeyboardInterrupt occurs here, __exit__ will not be called
        ...
        # KeyboardInterrupt could occur just before the function returns

    def __exit__(self, exc_type, exc_val, exc_tb):
        ...
        self.lock.release()
~~~

对于许多程序，特别是那些在遇到 [`KeyboardInterrupt`](3.标准库/exceptions.md#KeyboardInterrupt "KeyboardInterrupt") 只需直接退出的程序来说，这不是个问题，但是高复杂度或要求高可靠性的应用程序则应当避免由于信号处理句柄引发异常。 他们还应当避免将捕获 [`KeyboardInterrupt`](3.标准库/exceptions.md#KeyboardInterrupt "KeyboardInterrupt") 作为程序关闭的优雅方式。 相反地，他们应当安装自己的 `SIGINT` 处理句柄。 下面是一个避免了 [`KeyboardInterrupt`](3.标准库/exceptions.md#KeyboardInterrupt "KeyboardInterrupt") 的 HTTP 服务器示例:

    
    
~~~
import signal
import socket
from selectors import DefaultSelector, EVENT_READ
from http.server import HTTPServer, SimpleHTTPRequestHandler

interrupt_read, interrupt_write = socket.socketpair()

def handler(signum, frame):
    print('Signal handler called with signal', signum)
    interrupt_write.send(b'\0')
signal.signal(signal.SIGINT, handler)

def serve_forever(httpd):
    sel = DefaultSelector()
    sel.register(interrupt_read, EVENT_READ)
    sel.register(httpd, EVENT_READ)

    while True:
        for key, _ in sel.select():
            if key.fileobj == interrupt_read:
                interrupt_read.recv(1)
                return
            if key.fileobj == httpd:
                httpd.handle_request()

print("Serving on port 8000")
httpd = HTTPServer(('', 8000), SimpleHTTPRequestHandler)
serve_forever(httpd)
print("Shutdown...")
~~~

