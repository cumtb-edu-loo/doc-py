# `fcntl` —— 系统调用 `fcntl` 和 `ioctl`¶

* * *

本模块基于文件描述符来进行文件控制和 I/O 控制。它是 Unix 系统调用 `fcntl()` 和 `ioctl()` 的接口。关于这些调用的完整描述，请参阅 Unix 手册的 _[fcntl(2)](https://manpages.debian.org/fcntl\(2\))_ 和 _[ioctl(2)](https://manpages.debian.org/ioctl\(2\))_ 页面。

[可用性](intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

本模块的所有函数都接受文件描述符 _fd_ 作为第一个参数。可以是一个整数形式的文件描述符，比如 `sys.stdin.fileno()` 的返回结果，或为 [`io.IOBase`](io.md#io.IOBase "io.IOBase") 对象，比如 `sys.stdin` 提供一个 [`fileno()`](io.md#io.IOBase.fileno "io.IOBase.fileno")，可返回一个真正的文件描述符。

在 3.3 版本发生变更: 本模块的操作以前触发的是 [`IOError`](exceptions.md#IOError "IOError")，现在则会触发 [`OSError`](exceptions.md#OSError "OSError")。

在 3.8 版本发生变更: fcntl 模块现在有了 `F_ADD_SEALS` 、`F_GET_SEALS` 和 `F_SEAL_*` 常量，用于文件描述符 [`os.memfd_create()`](os.md#os.memfd_create "os.memfd_create") 的封装。

在 3.9 版本发生变更: 在 macOS 上，fcntl 模块提供了 `F_GETPATH` 常量，从文件描述符获取文件的路径。 在 Linux(>=3.15) 上，fcntl 模块提供了 `F_OFD_GETLK`, `F_OFD_SETLK` 和 `F_OFD_SETLKW` 常量，它们将在处理打开文件描述锁时被使用。

在 3.10 版本发生变更: 在 Linux 2.6.11 以上版本中，fcntl 模块提供了 `F_GETPIPE_SZ` 和 `F_SETPIPE_SZ` 常量，分别用于检查和修改管道的大小。

在 3.11 版本发生变更: 在 FreeBSD 上，fcntl 模块会暴露 `F_DUP2FD` 和 `F_DUP2FD_CLOEXEC` 常量，它们允许复制文件描述符，后者还额外设置了 `FD_CLOEXEC` 旗标。

在 3.12 版本发生变更: 在 Linux >= 4.5 上，`fcntl` 模块将公开 `FICLONE` 和 `FICLONERANGE` 常量，这允许在某些系统上（例如 btrfs, OCFS2, 和 XFS）通过将一个文件引用链接到另一个文件来共享某些数据。 此行为通常被称为“写入时拷贝”。

这个模块定义了以下函数：

fcntl.fcntl( _fd_ , _cmd_ , _arg =0_)¶

    

~~~
对文件描述符 _fd_ 执行 _cmd_ 操作（能够提供 [`fileno()`](io.md#io.IOBase.fileno "io.IOBase.fileno") 方法的文件对象也可以接受）。 _cmd_ 可用的值与操作系统有关，在 `fcntl` 模块中可作为常量使用，名称与相关 C 语言头文件中的一样。参数 _arg_ 可以是整数或 [`bytes`](stdtypes.md#bytes "bytes") 对象。若为整数值，则本函数的返回值是 C 语言 `fcntl()` 调用的整数返回值。若为字节串，则其代表一个二进制结构，比如由 [`struct.pack()`](struct.md#struct.pack "struct.pack") 创建的数据。该二进制数据将被复制到一个缓冲区，缓冲区地址传给 C 调用 `fcntl()`。调用成功后的返回值位于缓冲区内，转换为一个 [`bytes`](stdtypes.md#bytes "bytes") 对象。返回的对象长度将与 _arg_ 参数的长度相同。上限为 1024 字节。如果操作系统在缓冲区中返回的信息大于 1024 字节，很可能导致内存段冲突，或更为不易察觉的数据错误。

如果 `fcntl()` 调用失败，会触发 [`OSError`](exceptions.md#OSError "OSError") 。

引发一条 [auditing 事件](sys.md#auditing) `fcntl.fcntl`，参数为 `fd` 、`cmd`、`arg`。

fcntl.ioctl( _fd_ , _request_ , _arg =0_, _mutate_flag =True_)¶
~~~
    

~~~
本函数与 `fcntl()` 函数相同，只是参数的处理更加复杂。

_request_ 参数的上限是 32位。[`termios`](termios.md#module-termios "termios: POSIX style tty control. \(Unix\)") 模块中包含了可用作 _request_ 参数其他常量，名称与相关 C 头文件中定义的相同。

参数 _arg_ 可为整数、支持只读缓冲区接口的对象（如 [`bytes`](stdtypes.md#bytes "bytes") ）或支持读写缓冲区接口的对象（如 [`bytearray`](stdtypes.md#bytearray "bytearray") ）。

除了最后一种情况，其他情况下的行为都与 `fcntl()` 函数一样。

如果传入的是个可变缓冲区，那么行为就由 _mutate_flag_ 参数决定。

如果 _mutate_flag_ 为 False，缓冲区的可变性将被忽略，行为与只读缓冲区一样，只是没有了上述 1024 字节的上限——只要传入的缓冲区能容纳操作系统放入的数据即可。

如果 _mutate_flag_ 为 True（默认值），那么缓冲区（实际上）会传给底层的 系统调用 `ioctl()` ，其返回代码则会回传给调用它的 Python，而缓冲区的新数据则反映了 `ioctl()` 的运行结果。这里做了一点简化，因为若是给出的缓冲区少于 1024 字节，首先会被复制到一个 1024 字节长的静态缓冲区再传给 `ioctl()` ，然后把结果复制回给出的缓冲区去。

如果 `ioctl()` 调用失败，则会触发 [`OSError`](exceptions.md#OSError "OSError") 异常。

举个例子：
~~~
    
    
~~~shell
>>> import array, fcntl, struct, termios, os
>>> os.getpgrp()
13341
>>> struct.unpack('h', fcntl.ioctl(0, termios.TIOCGPGRP, "  "))[0]
13341
>>> buf = array.array('h', [0])
>>> fcntl.ioctl(0, termios.TIOCGPGRP, buf, 1)
0
>>> buf
array('h', [13341])
~~~

触发一条 [auditing 事件](sys.md#auditing) `fcntl.ioctl`，参数为 `fd` 、`request` 、`arg`。

fcntl.flock( _fd_ , _operation_ )¶

    

~~~
在文件描述符 _fd_ 上执行加锁操作 _operation_ (也接受能提供 [`fileno()`](io.md#io.IOBase.fileno "io.IOBase.fileno") 方法的文件对象)。 详见 Unix 手册 _[flock(2)](https://manpages.debian.org/flock\(2\))_ 。 (在某些系统中，此函数是用 `fcntl()` 模拟出来的。)

如果 `flock()` 调用失败，就会触发 [`OSError`](exceptions.md#OSError "OSError") 异常。

触发一条 [审计事件](sys.md#auditing) `fcntl.flock`，参数为 `fd` 、`operation`。

fcntl.lockf( _fd_ , _cmd_ , _len =0_, _start =0_, _whence =0_)¶
~~~
    

~~~
本质上是对 `fcntl()` 加锁调用的封装。 _fd_ 是要加解锁的文件描述符（也接受能提供 [`fileno()`](io.md#io.IOBase.fileno "io.IOBase.fileno") 方法的文件对象）， _cmd_ 是以下值之一：

  * `LOCK_UN` ——解锁

  * `LOCK_SH` —— 获取一个共享锁

  * `LOCK_EX` —— 获取一个独占锁

如果 _cmd_ 为 `LOCK_SH` 或 `LOCK_EX`，则还可以与 `LOCK_NB` 进行按位或运算，以避免在获取锁时出现阻塞。 如果用了 `LOCK_NB`，无法获取锁时将触发 [`OSError`](exceptions.md#OSError "OSError")，此异常的 _errno_ 属性将被设为 `EACCES` 或 `EAGAIN` (视操作系统而定；为了保证可移植性，请检查这两个值)。 至少在某些系统上，只有当文件描述符指向需要写入而打开的文件时，才可以使用 `LOCK_EX`。

_len_ 是要锁定的字节数， _start_ 是自 _whence_ 开始锁定的字节偏移量， _whence_ 与 [`io.IOBase.seek()`](io.md#io.IOBase.seek "io.IOBase.seek") 的定义一样。

  * `0` \-- 相对于文件开头 ([`os.SEEK_SET`](os.md#os.SEEK_SET "os.SEEK_SET"))

  * `1` \-- 相对于当前缓冲区位置 ([`os.SEEK_CUR`](os.md#os.SEEK_CUR "os.SEEK_CUR"))

  * `2` \-- 相对于文件末尾 ([`os.SEEK_END`](os.md#os.SEEK_END "os.SEEK_END"))

_start_ 的默认值为 0，表示从文件起始位置开始。 _len_ 的默认值是 0，表示加锁至文件末尾。 _whence_ 的默认值也是 0。

触发一条 [审计事件](sys.md#auditing) `fcntl.lockf`，参数为 `fd` 、 `cmd`、 `len`、 `start`、 `whence`。

示例（都是运行于符合 SVR4 的系统）：
~~~
    
    
~~~
import struct, fcntl, os

f = open(...)
rv = fcntl.fcntl(f, fcntl.F_SETFL, os.O_NDELAY)

lockdata = struct.pack('hhllhh', fcntl.F_WRLCK, 0, 0, 0, 0, 0)
rv = fcntl.fcntl(f, fcntl.F_SETLKW, lockdata)
~~~

注意，在第一个例子中，返回值变量 _rv_ 将存有整数；在第二个例子中，该变量中将存有一个 [`bytes`](stdtypes.md#bytes "bytes") 对象。 _lockdata_ 变量的结构布局视系统而定——因此采用 `flock()` 调用可能会更好。

参见

模块 [`os`](os.md#module-os "os: Miscellaneous operating system interfaces.")

    

~~~
