# `termios` \--- POSIX 风格的 tty 控制¶

* * *

此模块提供了针对tty I/O 控制的 POSIX 调用的接口。 有关此类调用的完整描述，请参阅 _[termios(3)](https://manpages.debian.org/termios\(3\))_ Unix 指南页。 它仅在当安装时配置了支持 POSIX _termios_ 风格的 tty I/O 控制的 Unix 版本上可用。

此模块中的所有函数均接受一个文件描述符 _fd_ 作为第一个参数。 这可以是一个整数形式的文件描述符，例如 `sys.stdin.fileno()` 所返回的对象，或是一个 [file object](../glossary.md#term-file-object)，例如 `sys.stdin` 本身。

这个模块还定义了与此处所提供的函数一起使用的所有必要的常量；这些常量与它们在 C 中的对应常量同名。 请参考你的系统文档了解有关如何使用这些终端控制接口的更多信息。

这个模块定义了以下函数：

termios.tcgetattr( _fd_ )¶

    

~~~
对于文件描述符 _fd_ 返回一个包含 tty 属性的列表，形式如下: `[iflag, oflag, cflag, lflag, ispeed, ospeed, cc]`，其中 _cc_ 为一个包含 tty 特殊字符的列表（每一项都是长度为 1 的字符串，索引号为 `VMIN` 和 `VTIME` 的项除外，这些字段如有定义则应为整数）。 对旗标和速度以及 _cc_ 数组中索引的解读必须使用在 `termios` 模块中定义的符号常量来完成。

termios.tcsetattr( _fd_ , _when_ , _attributes_ )¶
~~~
    

~~~
根据 _attributes_ 列表设置文件描述符 _fd_ 的 tty 属性，该列表即 `tcgetattr()` 所返回的对象。 _when_ 参数确定何时改变属性: `TCSANOW` 表示立即改变，`TCSADRAIN` 表示在传输所有队列输出后再改变，或 `TCSAFLUSH` 表示在传输所有队列输出并丢失所有队列输入后再改变。

termios.tcsendbreak( _fd_ , _duration_ )¶
~~~
    

~~~
在文件描述符 _fd_ 上发送一个中断。 _duration_ 为零表示发送时长为 0.25--0.5 秒的中断； _duration_ 非零值的含义取决于具体系统。

termios.tcdrain( _fd_ )¶
~~~
    

~~~
进入等待状态直到写入文件描述符 _fd_ 的所有输出都传送完毕。

termios.tcflush( _fd_ , _queue_ )¶
~~~
    

~~~
在文件描述符 _fd_ 上丢弃队列数据。 _queue_ 选择器指定哪个队列: `TCIFLUSH` 表示输入队列，`TCOFLUSH` 表示输出队列，或 `TCIOFLUSH` 表示两个队列同时。

termios.tcflow( _fd_ , _action_ )¶
~~~
    

~~~
在文件描述符 _fd_ 上挂起一战恢复输入或输出。 _action_ 参数可以为 `TCOOFF` 表示挂起输出，`TCOON` 表示重启输出，`TCIOFF` 表示挂起输入，或 `TCION` 表示重启输入。

termios.tcgetwinsize( _fd_ )¶
~~~
    

~~~
返回一个包含文件描述符 _fd_ 的 tty 窗口大小的元组 `(ws_row, ws_col)`。 需要 `termios.TIOCGWINSZ` 或 `termios.TIOCGSIZE`。

在 3.11 版本加入.

termios.tcsetwinsize( _fd_ , _winsize_ )¶
~~~
    

~~~
将文件描述符 _fd_ 的 tty 窗口大小设置为 _winsize_ ，这是一个包含两项的元组 `(ws_row, ws_col)`，如 `tcgetwinsize()` 所返回的一样。 要求至少定义了 (`termios.TIOCGWINSZ`, `termios.TIOCSWINSZ`); (`termios.TIOCGSIZE`, `termios.TIOCSSIZE`) 对之一。

在 3.11 版本加入.

参见

模块 [`tty`](tty.md#module-tty "tty: Utility functions that perform common terminal control operations. \(Unix\)")
~~~
    

~~~
针对常用终端控制操作的便捷函数。

## 示例¶

这个函数可提示输入密码并且关闭回显。 请注意其采取的技巧是使用一个单独的 `tcgetattr()` 调用和一个 [`try`](../reference/compound_stmts.md#try) ... [`finally`](../reference/compound_stmts.md#finally) 语句来确保旧的 tty 属性无论在何种情况下都会被原样保存:
~~~
    
    
~~~python
def getpass(prompt="Password: "):
    import termios, sys
    fd = sys.stdin.fileno()
    old = termios.tcgetattr(fd)
    new = termios.tcgetattr(fd)
    new[3] = new[3] & ~termios.ECHO          # lflags
    try:
        termios.tcsetattr(fd, termios.TCSADRAIN, new)
        passwd = input(prompt)
    finally:
        termios.tcsetattr(fd, termios.TCSADRAIN, old)
    return passwd
~~~

