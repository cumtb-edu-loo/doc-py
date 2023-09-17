# Python 开发模式¶

在 3.7 版本加入.

开发模式下的 Python 加入了额外的运行时检查，由于开销太大，并非默认启用的。如果代码能够正确执行，默认的调试级别足矣，不应再提高了；仅当觉察到问题时再提升警告触发的级别。

使用 [`-X dev`](1.%20命令行与环境.md#cmdoption-X) 命令行参数或将环境变量 [`PYTHONDEVMODE`](1.%20命令行与环境.md#envvar-PYTHONDEVMODE) 置为 `1` ，可以启用开发模式。

另请参考 [Python debug build](3.%20配置%20Python.md#debug-build) 。

## Python 开发模式的效果¶

启用 Python 开发模式后的效果，与以下命令类似，不过还有下面的额外效果：

    
    
~~~
PYTHONMALLOC=debug PYTHONASYNCIODEBUG=1 python -W default -X faulthandler
~~~

Python 开发模式的效果：

  * 加入 `default` [warning filter](warnings.md#describing-warning-filters) 。下述警告信息将会显示出来：

    * [`DeprecationWarning`](3.标准库/exceptions.md#DeprecationWarning "DeprecationWarning")

    * [`ImportWarning`](3.标准库/exceptions.md#ImportWarning "ImportWarning")

    * [`PendingDeprecationWarning`](3.标准库/exceptions.md#PendingDeprecationWarning "PendingDeprecationWarning")

    * [`ResourceWarning`](3.标准库/exceptions.md#ResourceWarning "ResourceWarning")

通常上述警告是由默认的 [warning filters](warnings.md#describing-warning-filters) 负责处理的。

效果类似于采用了 [`-W default`](1.%20命令行与环境.md#cmdoption-W) 命令行参数。

使用命令行参数 [`-W error`](1.%20命令行与环境.md#cmdoption-W) 或将环境变量 [`PYTHONWARNINGS`](1.%20命令行与环境.md#envvar-PYTHONWARNINGS) 设为 `error`，可将警告视为错误。

  * 在内存分配程序中安装调试钩子，用以查看：

    * 缓冲区下溢

    * 缓冲区上溢

    * 内存分配 API 冲突

    * 不安全的 GIL 调用

参见 C 函数 [`PyMem_SetupDebugHooks()`](memory.md#c.PyMem_SetupDebugHooks "PyMem_SetupDebugHooks") 。

效果如同将环境变量 [`PYTHONMALLOC`](1.%20命令行与环境.md#envvar-PYTHONMALLOC) 设为 `debug`。

若要启用 Python 开发模式，却又不要在内存分配程序中安装调试钩子，请将 环境变量 [`PYTHONMALLOC`](1.%20命令行与环境.md#envvar-PYTHONMALLOC) 设为 `default`。

  * 在启动 Python 时调用 [`faulthandler.enable()`](faulthandler.md#faulthandler.enable "faulthandler.enable") ，会安装 `SIGSEGV` 、 `SIGFPE` 、 `SIGABRT` 、 `SIGBUS` 和 `SIGILL` 信号的处理程序，以便在程序崩溃时将 Python 跟踪信息转储下来。

其行为如同使用了 [`-X faulthandler`](1.%20命令行与环境.md#cmdoption-X) 命令行选项或将 [`PYTHONFAULTHANDLER`](1.%20命令行与环境.md#envvar-PYTHONFAULTHANDLER) 环境变量设为 `1`。

  * 启用 [asyncio debug mode](asyncio-dev.md#asyncio-debug-mode)。比如 [`asyncio`](asyncio.md#module-asyncio "asyncio: Asynchronous I/O.") 会检查没有等待的协程并记录下来。

效果如同将环境变量 [`PYTHONASYNCIODEBUG`](1.%20命令行与环境.md#envvar-PYTHONASYNCIODEBUG) 设为 `1`。

  * 检查字符串编码和解码函数的 _encoding_ 和 _errors_ 参数。例如： [`open()`](functions.md#open "open") 、 [`str.encode()`](stdtypes.md#str.encode "str.encode") 和 [`bytes.decode()`](stdtypes.md#bytes.decode "bytes.decode")。

为了获得最佳性能，默认只会在第一次编码/解码错误时才会检查 _errors_ 参数，有时 _encoding_ 参数为空字符串时还会被忽略。

  * [`io.IOBase`](io.md#io.IOBase "io.IOBase") 的析构函数会记录 `close()` 触发的异常。

  * 将 [`sys.flags`](3.标准库/sys.md#sys.flags "sys.flags") 的 [`dev_mode`](3.标准库/sys.md#sys.flags.dev_mode "sys.flags.dev_mode") 属性设为 `True`。

Python 开发模式下，默认不会启用 [`tracemalloc`](tracemalloc.md#module-tracemalloc "tracemalloc: Trace memory allocations.") 模块，因为其性能和内存开销太大。启用 [`tracemalloc`](tracemalloc.md#module-tracemalloc "tracemalloc: Trace memory allocations.") 模块后，能够提供有关错误来源的一些额外信息。例如，[`ResourceWarning`](3.标准库/exceptions.md#ResourceWarning "ResourceWarning") 记录了资源分配的跟踪信息，而缓冲区溢出错误记录了内存块分配的跟踪信息。

Python 开发模式不会阻止命令行参数 [`-O`](1.%20命令行与环境.md#cmdoption-O) 删除 [`assert`](7.%20简单语句.md#assert) 语句，也不会阻止将 [`__debug__`](constants.md#debug__ "__debug__") 设为 `False`。

Python 开发模式只能在 Python 启动时启用。其参数值可从 [`sys.flags.dev_mode`](3.标准库/sys.md#sys.flags "sys.flags") 读取。

在 3.8 版本发生变更: 现在， [`io.IOBase`](io.md#io.IOBase "io.IOBase") 的析构函数会记录 `close()` 触发的异常。

在 3.9 版本发生变更: 现在，字符串编码和解码操作时会检查 _encoding_ 和 _errors_ 参数。

## ResourceWarning 示例¶

以下示例将统计由命令行指定的文本文件的行数：

    
    
~~~
import sys

def main():
    fp = open(sys.argv[1])
    nlines = len(fp.readlines())
    print(nlines)
    # The file is closed implicitly

if __name__ == "__main__":
    main()
~~~

上述代码没有显式关闭文件。默认情况下，Python 不会触发任何警告。下面用 README.txt 文件测试下，有 269 行：

    
    
~~~
$ python script.py README.txt
269
~~~

启用 Python 开发模式后，则会显示一条 [`ResourceWarning`](3.标准库/exceptions.md#ResourceWarning "ResourceWarning") 警告：

    
    
~~~
$ python -X dev script.py README.txt
269
script.py:10: ResourceWarning: unclosed file <_io.TextIOWrapper name='README.rst' mode='r' encoding='UTF-8'>
  main()
ResourceWarning: Enable tracemalloc to get the object allocation traceback
~~~

启用 [`tracemalloc`](tracemalloc.md#module-tracemalloc "tracemalloc: Trace memory allocations.") 后，则还会显示打开文件的那行代码：

    
    
~~~
$ python -X dev -X tracemalloc=5 script.py README.rst
269
script.py:10: ResourceWarning: unclosed file <_io.TextIOWrapper name='README.rst' mode='r' encoding='UTF-8'>
  main()
Object allocated at (most recent call last):
  File "script.py", lineno 10
    main()
  File "script.py", lineno 4
    fp = open(sys.argv[1])
~~~

修正方案就是显式关闭文件。下面用上下文管理器作为示例：

    
    
~~~python
def main():
    # Close the file explicitly when exiting the with block
    with open(sys.argv[1]) as fp:
        nlines = len(fp.readlines())
    print(nlines)
~~~

未能显式关闭资源，会让资源打开时长远超预期；在退出 Python 时可能会导致严重问题。这在 CPython 中比较糟糕，但在 PyPy 中会更糟。显式关闭资源能让应用程序更加稳定可靠。

## 文件描述符错误示例¶

显示自身的第一行代码：

    
    
~~~
import os

def main():
    fp = open(__file__)
    firstline = fp.readline()
    print(firstline.rstrip())
    os.close(fp.fileno())
    # The file is closed implicitly

main()
~~~

默认情况下，Python 不会触发任何警告：

    
    
~~~
$ python script.py
import os
~~~

在 Python 开发模式下，会在析构文件对象时显示 [`ResourceWarning`](3.标准库/exceptions.md#ResourceWarning "ResourceWarning") 并记录 “Bad file descriptor” 错误。

    
    
~~~
$ python -X dev script.py
import os
script.py:10: ResourceWarning: unclosed file <_io.TextIOWrapper name='script.py' mode='r' encoding='UTF-8'>
  main()
ResourceWarning: Enable tracemalloc to get the object allocation traceback
Exception ignored in: <_io.TextIOWrapper name='script.py' mode='r' encoding='UTF-8'>
Traceback (most recent call last):
  File "script.py", line 10, in <module>
    main()
OSError: [Errno 9] Bad file descriptor
~~~

`os.close(fp.fileno())` 会关闭文件描述符。当文件对象析构函数试图再次关闭文件描述符时会失败，并触发 `Bad file descriptor` 错误。每个文件描述符只允许关闭一次。在最坏的情况下，关闭两次会导致程序崩溃（示例可参见 [bpo-18748](https://bugs.python.org/issue?@action=redirect&bpo=18748) ）。

修正方案是删除 `os.close(fp.fileno())` 这一行，或者打开文件时带上 `closefd=False` 参数。

