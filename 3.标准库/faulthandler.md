# `faulthandler` —— 转储 Python 的跟踪信息¶

在 3.3 版本加入.

* * *

当故障、超时或收到用户信号时，利用本模块内的函数可转储 Python 跟踪信息。调用 `faulthandler.enable()` 可安装 `SIGSEGV` 、 `SIGFPE` 、 `SIGABRT` 、 `SIGBUS` 和 `SIGILL` 信号的故障处理程序。通过设置 [`PYTHONFAULTHANDLER`](cmdline.md#envvar-PYTHONFAULTHANDLER) 环境变量或 [`-X`](cmdline.md#cmdoption-X) `faulthandler` 命令行参数，还可以在启动时开启这些设置。

故障处理程序与操作系统的故障处理程序兼容，比如 Apport 或 Windows 故障处理程序等。如果 `sigaltstack()` 函数可用，本模块将为信号处理程序使用备用堆栈。这样即便堆栈溢出也能转储跟踪信息。

故障处理程序将在灾难性场合调用，因此只能使用信号安全的函数（比如不能在堆上分配内存）。由于这一限制，与正常的 Python 跟踪相比，转储量是最小的。

  * 只支持 ASCII 码。编码时会用到 `backslashreplace` 错误处理程序。

  * 每个字符串限制在 500 个字符以内。

  * 只会显式文件名、函数名和行号。（不显示源代码）

  * 上限是 100 页内存帧和 100 个线程。

  * 反序排列：最近的调用最先显示。

默认情况下，Python 的跟踪信息会写入 [`sys.stderr`](3.标准库/sys.md#sys.stderr "sys.stderr")。为了能看到跟踪信息，应用程序必须运行于终端中。日志文件也可以传给 `faulthandler.enable()`。

本模块是用 C 语言实现的，所以才能在崩溃或 Python 死锁时转储跟踪信息。

在 Python 启动时， [Python 开发模式](devmode.md#devmode) 会调用 `faulthandler.enable()`。

参见

模块 [`pdb`](pdb.md#module-pdb "pdb: The Python debugger for interactive interpreters.")

    

~~~
用于 Python 程序的交互式源代码调试器。

模块 [`traceback`](traceback.md#module-traceback "traceback: Print or retrieve a stack traceback.")
~~~
    

~~~
提取、格式化和打印 Python 程序的栈回溯信息的标准接口。

## 转储跟踪信息¶

faulthandler.dump_traceback( _file =sys.stderr_, _all_threads =True_)¶
~~~
    

~~~
将所有线程的跟踪数据转储到 _file_ 中。如果 _all_threads_ 为 `False`，则只转储当前线程。

参见

[`traceback.print_tb()`](traceback.md#traceback.print_tb "traceback.print_tb")，可被用于打印回溯对象。

在 3.5 版本发生变更: 增加了向本函数传入文件描述符的支持。

## 故障处理程序的状态¶

faulthandler.enable( _file =sys.stderr_, _all_threads =True_)¶
~~~
    

~~~
启用故障处理程序：为 `SIGSEGV` 、 `SIGFPE` 、 `SIGABRT` 、 `SIGBUS` 和 `SIGILL` 信号安装处理程序，以转储 Python 跟踪信息。如果 _all_threads_ 为 `True`，则会为每个运行中的线程生成跟踪信息。否则只转储当前线程。

_file_ 必须保持打开状态，直至停用故障处理程序为止：参见 文件描述符相关话题。

在 3.5 版本发生变更: 增加了向本函数传入文件描述符的支持。

在 3.6 版本发生变更: 在 Windows 系统中，同时会安装一个 Windows 异常处理程序。

在 3.10 版本发生变更: 现在如果 _all_threads_ 为 True，则转储信息会包含垃圾收集器是否正在运行。

faulthandler.disable()¶
~~~
    

~~~
停用故障处理程序：卸载由 `enable()` 安装的信号处理程序。

faulthandler.is_enabled()¶
~~~
    

~~~
检查故障处理程序是否被启用。

## 一定时间后转储跟踪数据。¶

faulthandler.dump_traceback_later( _timeout_ , _repeat =False_, _file =sys.stderr_, _exit =False_)¶
~~~
    

~~~
在 _timeout_ 秒超时后，转储所有线程的跟踪信息，如果 _repeat_ 为 `True`，则每隔 _timeout_ 秒转储一次。如果 _exit_ 为 `True`，则在转储跟踪信息后调用 `_exit()`，参数 status=1。请注意， `_exit()` 会立即关闭进程，这意味着不做任何清理工作，如刷新文件缓冲区等。如果调用两次函数，则新的调用将取代之前的参数，超时时间也会重置。计时器的精度为亚秒级。

_file_ 必须保持打开状态，直至跟踪信息转储完毕，或调用了 `cancel_dump_traceback_later()` ：参见 文件描述符相关话题。

本函数用一个看门狗线程实现。

在 3.7 版本发生变更: 该函数现在总是可用。

在 3.5 版本发生变更: 增加了向本函数传入文件描述符的支持。

faulthandler.cancel_dump_traceback_later()¶
~~~
    

~~~
取消 `dump_traceback_later()` 的最后一次调用。

## 转储用户信号的跟踪信息。¶

faulthandler.register( _signum_ , _file =sys.stderr_, _all_threads =True_, _chain =False_)¶
~~~
    

~~~
注册一个用户信号：为 _signum_ 信号安装一个处理程序，将所有线程或当前线程（ _all_threads_ 为 `False` 时）的跟踪信息转储到 _file_ 中。如果 chain 为 `True`，则调用上一层处理程序。

_file_ 必须保持打开状态，直至该信号被 `unregister()` 注销：参见 文件描述符相关话题。

Windows 中不可用。

在 3.5 版本发生变更: 增加了向本函数传入文件描述符的支持。

faulthandler.unregister( _signum_ )¶
~~~
    

~~~
注销一个用户信号：卸载由 `register()` 安装的 _signum_ 信号处理程序。如果信号已注册，返回 `True`，否则返回 `False`。

Windows 中不可用。

## 文件描述符相关话题¶

`enable()` 、 `dump_traceback_later()` 和 `register()` 保留其 _file_ 参数给出的文件描述符。 如果文件关闭，文件描述符将被一个新文件重新使用；或者用 [`os.dup2()`](os.md#os.dup2 "os.dup2") 替换了文件描述符，则跟踪信息将被写入另一个文件。 每次文件被替换时，都会再次调用这些函数。

## 示例¶

在 Linux 中启用和停用内存段故障的默认处理程序：
~~~
    
    
~~~
$ python -c "import ctypes; ctypes.string_at(0)"
Segmentation fault

$ python -q -X faulthandler
>>> import ctypes
>>> ctypes.string_at(0)
Fatal Python error: Segmentation fault

Current thread 0x00007fb899f39700 (most recent call first):
  File "/home/python/cpython/Lib/ctypes/__init__.py", line 486 in string_at
  File "<stdin>", line 1 in <module>
Segmentation fault
~~~

