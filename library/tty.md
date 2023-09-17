# `tty` \--- 终端控制功能¶

**Source code:** [Lib/tty.py](https://github.com/python/cpython/tree/3.12/Lib/tty.py)

* * *

`tty` 模块定义了将 tty 放入 cbreak 和 raw 模式的函数。

因为它需要 [`termios`](termios.md#module-termios "termios: POSIX style tty control. \(Unix\)") 模块，所以只能在 Unix 上运行。

`tty` 模块定义了以下函数：

tty.cfmakeraw( _mode_ )¶

    

~~~
操作 tty 属性列表 _mode_ ，它是一个与 [`termios.tcgetattr()`](termios.md#termios.tcgetattr "termios.tcgetattr") 的返回值类似的列表，将其转换为原始模式 tty 的属性列表。

在 3.12 版本加入.

tty.cfmakecbreak( _mode_ )¶
~~~
    

~~~
操作 tty 属性列表 _mode_ ，它是一个与 [`termios.tcgetattr()`](termios.md#termios.tcgetattr "termios.tcgetattr") 的返回值类似的列表，将其转换为 cbreak 模式的 tty 的属性列表。

在 3.12 版本加入.

tty.setraw( _fd_ , _when =termios.TCSAFLUSH_)¶
~~~
    

~~~
将文件描述符 _fd_ 的模式改为 raw。 如果 _when_ 被省略，它将默认为 `termios.TCSAFLUSH`，并将被传给 [`termios.tcsetattr()`](termios.md#termios.tcsetattr "termios.tcsetattr")。 [`termios.tcgetattr()`](termios.md#termios.tcgetattr "termios.tcgetattr") 的返回值在将 _fd_ 设为 raw 模式前会被保存；该值将被返回。

tty.setcbreak( _fd_ , _when =termios.TCSAFLUSH_)¶
~~~
    

~~~
将文件描述符 _fd_ 的模式改为 cbreak。 如果 _when_ 被省略，它将默认为 `termios.TCSAFLUSH`，并将被传给 [`termios.tcsetattr()`](termios.md#termios.tcsetattr "termios.tcsetattr")。 [`termios.tcgetattr()`](termios.md#termios.tcgetattr "termios.tcgetattr") 的返回值在将 _fd_ 设为 cbreak 模式前会被保存；该值将被返回。

参见

模块 [`termios`](termios.md#module-termios "termios: POSIX style tty control. \(Unix\)")
~~~
    

~~~
