# 子进程集¶

**源代码:** [Lib/asyncio/subprocess.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/subprocess.py), [Lib/asyncio/base_subprocess.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/base_subprocess.py)

* * *

本节介绍了用于创建和管理子进程的高层级 async/await asyncio API。

下面的例子演示了如何用 asyncio 运行一个 shell 命令并获取其结果:

    
    
~~~
import asyncio

async def run(cmd):
    proc = await asyncio.create_subprocess_shell(
        cmd,
        stdout=asyncio.subprocess.PIPE,
        stderr=asyncio.subprocess.PIPE)

    stdout, stderr = await proc.communicate()

    print(f'[{cmd!r} exited with {proc.returncode}]')
    if stdout:
        print(f'[stdout]\n{stdout.decode()}')
    if stderr:
        print(f'[stderr]\n{stderr.decode()}')

asyncio.run(run('ls /zzz'))
~~~

将打印:

    
    
~~~
['ls /zzz' exited with 1]
[stderr]
ls: /zzz: No such file or directory
~~~

由于所有 asyncio 子进程函数都是异步的并且 asyncio 提供了许多工具用来配合这些函数使用，因此并行地执行和监视多个子进程十分容易。 要修改上面的例子来同时运行多个命令确实是非常简单的:

    
    
~~~
async def main():
    await asyncio.gather(
        run('ls /zzz'),
        run('sleep 1; echo "hello"'))

asyncio.run(main())
~~~

另请参阅 Examples 小节。

## 创建子进程¶

_coroutine _asyncio.create_subprocess_exec( _program_ , _* args_, _stdin =None_, _stdout =None_, _stderr =None_, _limit =None_, _** kwds_)¶

    

~~~
创建一个子进程。

_limit_ 参数为 `Process.stdout` 和 `Process.stderr` 设置 [`StreamReader`](asyncio-stream.md#asyncio.StreamReader "asyncio.StreamReader") 包装器的缓冲区上限（如果将 [`subprocess.PIPE`](subprocess.md#subprocess.PIPE "subprocess.PIPE") 传给 _stdout_ 和 _stderr_ 参数）。

返回一个 `Process` 实例。

有关其他形参的说明请查阅 [`loop.subprocess_exec()`](asyncio-eventloop.md#asyncio.loop.subprocess_exec "asyncio.loop.subprocess_exec") 的文档。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

_coroutine _asyncio.create_subprocess_shell( _cmd_ , _stdin =None_, _stdout =None_, _stderr =None_, _limit =None_, _** kwds_)¶
~~~
    

~~~
运行 _cmd_ shell 命令。

_limit_ 参数为 `Process.stdout` 和 `Process.stderr` 设置 [`StreamReader`](asyncio-stream.md#asyncio.StreamReader "asyncio.StreamReader") 包装器的缓冲区上限（如果将 [`subprocess.PIPE`](subprocess.md#subprocess.PIPE "subprocess.PIPE") 传给 _stdout_ 和 _stderr_ 参数）。

返回一个 `Process` 实例。

有关其他形参的说明请查阅 [`loop.subprocess_shell()`](asyncio-eventloop.md#asyncio.loop.subprocess_shell "asyncio.loop.subprocess_shell") 的文档。

重要

应用程序要负责确保正确地转义所有空白字符和特殊字符以防止 [shell 注入](https://en.wikipedia.org/wiki/Shell_injection#Shell_injection) 漏洞。 [`shlex.quote()`](shlex.md#shlex.quote "shlex.quote") 函数可以被用来正确地转义字符串中可以被用来构造 shell 命令的空白字符和特殊 shell 字符。

在 3.10 版本发生变更: 移除了 _loop_ 形参。

备注

如果使用了 [`ProactorEventLoop`](asyncio-eventloop.md#asyncio.ProactorEventLoop "asyncio.ProactorEventLoop") 则子进程将在 Windows 中可用。 详情参见 [Windows 上的子进程支持](asyncio-platforms.md#asyncio-windows-subprocess)。

参见

asyncio 还有下列 _低层级_ API 可配合子进程使用: [`loop.subprocess_exec()`](asyncio-eventloop.md#asyncio.loop.subprocess_exec "asyncio.loop.subprocess_exec"), [`loop.subprocess_shell()`](asyncio-eventloop.md#asyncio.loop.subprocess_shell "asyncio.loop.subprocess_shell"), [`loop.connect_read_pipe()`](asyncio-eventloop.md#asyncio.loop.connect_read_pipe "asyncio.loop.connect_read_pipe"), [`loop.connect_write_pipe()`](asyncio-eventloop.md#asyncio.loop.connect_write_pipe "asyncio.loop.connect_write_pipe") 以及 [子进程传输](asyncio-protocol.md#asyncio-subprocess-transports) 和 [子进程协议](asyncio-protocol.md#asyncio-subprocess-protocols)。

## 常量¶

asyncio.subprocess.PIPE¶
~~~
    

~~~
可以被传递给 _stdin_ , _stdout_ 或 _stderr_ 形参。

如果 _PIPE_ 被传递给 _stdin_ 参数，则 `Process.stdin` 属性将会指向一个 `StreamWriter` 实例。

如果 _PIPE_ 被传递给 _stdout_ 或 _stderr_ 参数，则 `Process.stdout` 和 `Process.stderr` 属性将会指向 `StreamReader` 实例。

asyncio.subprocess.STDOUT¶
~~~
    

~~~
可以用作 _stderr_ 参数的特殊值，表示标准错误应当被重定向到标准输出。

asyncio.subprocess.DEVNULL¶
~~~
    

~~~
可以用作 _stdin_ , _stdout_ 或 _stderr_ 参数来处理创建函数的特殊值。 它表示将为相应的子进程流使用特殊文件 [`os.devnull`](os.md#os.devnull "os.devnull")。

## 与子进程交互¶

`create_subprocess_exec()` 和 `create_subprocess_shell()` 函数都返回 _Process_ 类的实例。 _Process_ 是一个高层级包装器，它允许与子进程通信并监视其完成情况。

_class _asyncio.subprocess.Process¶
~~~
    

~~~
一个用于包装 `create_subprocess_exec()` and `create_subprocess_shell()` 函数创建的 OS 进程的对象。

这个类被设计为具有与 [`subprocess.Popen`](subprocess.md#subprocess.Popen "subprocess.Popen") 类相似的 API，但两者有一些重要的差异:

  * 不同于 Popen，Process 实例没有与 [`poll()`](subprocess.md#subprocess.Popen.poll "subprocess.Popen.poll") 方法等价的方法；

  * `communicate()` 和 `wait()` 方法没有 _timeout_ 形参：请使用 [`wait_for()`](asyncio-task.md#asyncio.wait_for "asyncio.wait_for") 函数；

  * `Process.wait()` 方法是异步的，而 [`subprocess.Popen.wait()`](subprocess.md#subprocess.Popen.wait "subprocess.Popen.wait") 方法则被实现为阻塞型忙循环；

  * _universal_newlines_ 形参不被支持。

这个类不是线程安全的（[not thread safe](asyncio-dev.md#asyncio-multithreading)）。

请参阅 子进程和线程 部分。

_coroutine _wait()¶
~~~
    

~~~
等待子进程终结。

设置并返回 `returncode` 属性。

备注

当使用 `stdout=PIPE` 或 `stderr=PIPE` 并且子进程产生了足以阻塞 OS 管道缓冲区等待接收更多的数据的输出时，此方法会发生死锁。 当使用管道时请使用 `communicate()` 方法来避免这种情况。

_coroutine _communicate( _input =None_)¶
~~~
    

~~~
与进程交互:

  1. 发送数据到 _stdin_ (如果 _input_ 不为 `None`)；

  2. 关闭 _stdin_ ;

  3. 从 _stdout_ 和 _stderr_ 读取数据，直至到达 EOF；

  4. 等待进程终结。

可选的 _input_ 参数为将被发送到子进程的数据 ([`bytes`](stdtypes.md#bytes "bytes") 对象)。

返回一个元组 `(stdout_data, stderr_data)`。

如果在将 _input_ 写入到 _stdin_ 时引发了 [`BrokenPipeError`](exceptions.md#BrokenPipeError "BrokenPipeError") 或 [`ConnectionResetError`](exceptions.md#ConnectionResetError "ConnectionResetError") 异常，异常会被忽略。 此条件会在进程先于所有数据被写入到 _stdin_ 之前退出时发生。

如果想要将数据发送到进程的 _stdin_ ，则创建进程时必须使用 `stdin=PIPE`。 类似地，要在结果元组中获得任何不为 `None` 的值，则创建进程时必须使用 `stdout=PIPE` 和/或 `stderr=PIPE` 参数。

注意，数据读取在内存中是带缓冲的，因此如果数据量过大或不受则不要使用此方法。

在 3.12 版本发生变更: _stdin_ 在 input=None 时也会被关闭。

send_signal( _signal_ )¶
~~~
    

~~~
将信号 _signal_ 发送给子进程。

备注

在 Windows 上，`SIGTERM` 是 `terminate()` 的别名。 `CTRL_C_EVENT` 和 `CTRL_BREAK_EVENT` 可被发送给创建时设置了 _creationflags_ 形参且其中包括 `CREATE_NEW_PROCESS_GROUP` 的进程。

terminate()¶
~~~
    

~~~
停止子进程。

在 POSIX 系统上此方法会发送 [`signal.SIGTERM`](signal.md#signal.SIGTERM "signal.SIGTERM") 给子进程。

在 Windows 上会调用 Win32 API 函数 `TerminateProcess()` 以停止子进程。

kill()¶
~~~
    

~~~
杀掉子进程。

在 POSIX 系统中此方法会发送 `SIGKILL` 给子进程。

在 Windows 上此方法是 `terminate()` 的别名。

stdin¶
~~~
    

~~~
标准输入流 (`StreamWriter`) 或者如果进程创建时设置了 `stdin=None` 则为 `None`。

stdout¶
~~~
    

~~~
标准输出流 (`StreamReader`) 或者如果进程创建时设置了 `stdout=None` 则为 `None`。

stderr¶
~~~
    

~~~
标准错误流 (`StreamReader`) 或者如果进程创建时设置了 `stderr=None` 则为 `None`。

警告

使用 `communicate()` 方法而非 `process.stdin.write()`, `await process.stdout.read()` 或 `await process.stderr.read()`。 这可以避免由于流暂停读取或写入并阻塞子进程而导致的死锁。

pid¶
~~~
    

~~~
进程标识号（PID）。

注意对于由Note that for processes created by the `create_subprocess_shell()` 函数所创建的进程，这个属性将是所生成的 shell 的 PID。

returncode¶
~~~
    

~~~
当进程退出时返回其代号。

`None` 值表示进程尚未终止。

一个负值 `-N` 表示子进程被信号 `N` 中断 (仅 POSIX).

### 子进程和线程¶

标准 asyncio 事件循环默认支持从不同线程中运行子进程。

在 Windows 上子进程（默认）只由 [`ProactorEventLoop`](asyncio-eventloop.md#asyncio.ProactorEventLoop "asyncio.ProactorEventLoop") 提供，[`SelectorEventLoop`](asyncio-eventloop.md#asyncio.SelectorEventLoop "asyncio.SelectorEventLoop") 没有子进程支持。

在 UNIX 上会使用 _child watchers_ 来让子进程结束等待，详情请参阅 [进程监视器](asyncio-policy.md#asyncio-watchers)。

在 3.8 版本发生变更: UNIX 对于从不同线程中无限制地生成子进程会切换为使用 [`ThreadedChildWatcher`](asyncio-policy.md#asyncio.ThreadedChildWatcher "asyncio.ThreadedChildWatcher")。

使用 _不活动的_ 当前子监视器生成子进程将引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

请注意其他的事件循环实现可能有其本身的限制；请查看它们各自的文档。

参见

[asyncio 中的并发和多线程](asyncio-dev.md#asyncio-multithreading) 章节。

### 例子¶

一个使用 `Process` 类来控制子进程并用 [`StreamReader`](asyncio-stream.md#asyncio.StreamReader "asyncio.StreamReader") 类来从其标准输出读取信息的示例。

这个子进程是由 `create_subprocess_exec()` 函数创建的:
~~~
    
    
~~~
import asyncio
import sys

async def get_date():
    code = 'import datetime; print(datetime.datetime.now())'

    # Create the subprocess; redirect the standard output
    # into a pipe.
    proc = await asyncio.create_subprocess_exec(
        sys.executable, '-c', code,
        stdout=asyncio.subprocess.PIPE)

    # Read one line of output.
    data = await proc.stdout.readline()
    line = data.decode('ascii').rstrip()

    # Wait for the subprocess exit.
    await proc.wait()
    return line

date = asyncio.run(get_date())
print(f"Current date: {date}")
~~~

另请参阅使用低层级 API 编写的 [相同示例](asyncio-protocol.md#asyncio-example-subprocess-proto)。

