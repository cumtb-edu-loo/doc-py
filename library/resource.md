# `resource` \--- 资源使用信息¶

* * *

该模块提供了测量和控制程序所利用的系统资源的基本机制。

[可用性](intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

符号常量被用来指定特定的系统资源，并要求获得关于当前进程或其子进程的使用信息。

当系统调用失败时，会触发一个 [`OSError`](exceptions.md#OSError "OSError") 。

_exception _resource.error¶

    

~~~
一个被弃用的 [`OSError`](exceptions.md#OSError "OSError") 的别名。

在 3.3 版本发生变更: 根据 [**PEP 3151**](https://peps.python.org/pep-3151/)，这个类是 [`OSError`](exceptions.md#OSError "OSError") 的别名。

## 资源限制¶

资源的使用可以通过下面描述的 `setrlimit()` 函数来限制。每个资源都被一对限制所控制：一个软限制和一个硬限制。软限制是当前的限制，并且可以由一个进程随着时间的推移而降低或提高。软限制永远不能超过硬限制。硬限制可以降低到大于软限制的任何数值，但不能提高。（只有拥有超级用户有效UID的进程才能提高硬限制。)

可以被限制的具体资源取决于系统。它们在 man _[getrlimit(2)](https://manpages.debian.org/getrlimit\(2\))_ 中描述。 下面列出的资源在底层操作系统支持的情况下被支持；那些不能被操作系统检查或控制的资源在本模块中没有为这些平台定义。

resource.RLIM_INFINITY¶
~~~
    

~~~
用来表示无限资源的极限的常数。

resource.getrlimit( _resource_ )¶
~~~
    

~~~
返回一个包含 _resource_ 当前软限制和硬限制的元组。如果指定了一个无效的资源，则触发 [`ValueError`](exceptions.md#ValueError "ValueError") ，如果底层系统调用意外失败，则引发 `error` 。

resource.setrlimit( _resource_ , _limits_ )¶
~~~
    

~~~
设置 _resource_ 的新的消耗极限。参数 _limits_ 必须是一个由两个整数组成的元组 `(soft, hard)` ，描述了新的限制。 `RLIM_INFINITY` 的值可以用来请求一个无限的限制。

如果指定了一个无效的资源，如果新的软限制超过了硬限制，或者如果一个进程试图提高它的硬限制，将触发 [`ValueError`](exceptions.md#ValueError "ValueError") 。当资源的硬限制或系统限制不是无限时，指定一个 `RLIM_INFINITY` 的限制将导致 [`ValueError`](exceptions.md#ValueError "ValueError") 。 一个有效 UID 为超级用户的进程可以请求任何有效的限制值，包括无限，但如果请求的限制超过了系统规定的限制，则仍然会产生 [`ValueError`](exceptions.md#ValueError "ValueError") 。

如果底层系统调用失败， `setrlimit` 也可能触发 `error` 。

VxWorks只支持设置 `RLIMIT_NOFILE` 。

触发一个 [auditing event](sys.md#auditing) `resource.setrlimit` 使用参数 `resource` ， `limits` 。

resource.prlimit( _pid_ , _resource_ [, _limits_ ])¶
~~~
    

~~~
将 `setrlimit()` 和 `getrlimit()` 合并为一个函数，支持获取和设置任意进程的资源限制。如果 _pid_ 为0，那么该调用适用于当前进程。 _resource_ 和 _limits_ 的含义与 `setrlimit()` 相同，只是 _limits_ 是可选的。

当 _limits_ 没有给出时，该函数返回进程 _pid_ 的 _resource_ 限制。当 _limits_ 被给定时，进程的 _resource_ 限制被设置，并返回以前的资源限制。

当 _pid_ 找不到时，触发 [`ProcessLookupError`](exceptions.md#ProcessLookupError "ProcessLookupError") ；当用户没有进程的 `CAP_SYS_RESOURCE` 时，触发 [`PermissionError`](exceptions.md#PermissionError "PermissionError") 。

触发一个 [auditing event](sys.md#auditing) `resource.prlimit` 带有参数 `pid` ， `resource` ， `limits` 。

[可用性](intro.md#availability): Linux >= 2.6.36 且 glibc >= 2.13。

在 3.4 版本加入.

这些符号定义了资源的消耗可以通过下面描述的 `setrlimit()` 和 `getrlimit()` 函数来控制。这些符号的值正是 C 程序所使用的常数。

Unix man 页面 _[getrlimit(2)](https://manpages.debian.org/getrlimit\(2\))_ 列出了可用的资源。注意，并非所有系统都使用相同的符号或相同的值来表示相同的资源。本模块并不试图掩盖平台的差异——没有为某一平台定义的符号在该平台上将无法从本模块中获得。

resource.RLIMIT_CORE¶
~~~
    

~~~
当前进程可以创建的核心文件的最大大小（以字节为单位）。如果需要更大的核心文件来包含整个进程的镜像，这可能会导致创建一个部分核心文件。

resource.RLIMIT_CPU¶
~~~
    

~~~
一个进程可以使用的最大处理器时间（以秒为单位）。如果超过了这个限制，一个 `SIGXCPU` 信号将被发送给进程。（参见 [`signal`](signal.md#module-signal "signal: Set handlers for asynchronous events.") 模块文档，了解如何捕捉这个信号并做一些有用的事情，例如，将打开的文件刷新到磁盘上）。

resource.RLIMIT_FSIZE¶
~~~
    

~~~
进程可能创建的文件的最大大小。

resource.RLIMIT_DATA¶
~~~
    

~~~
进程的堆的最大大小（以字节为单位）。

resource.RLIMIT_STACK¶
~~~
    

~~~
当前进程的调用堆栈的最大大小（字节）。 这只影响到多线程进程中主线程的堆栈。

resource.RLIMIT_RSS¶
~~~
    

~~~
应该提供给进程的最大常驻内存大小。

resource.RLIMIT_NPROC¶
~~~
    

~~~
当前进程可能创建的最大进程数。

resource.RLIMIT_NOFILE¶
~~~
    

~~~
当前进程打开的文件描述符的最大数量。

resource.RLIMIT_OFILE¶
~~~
    

~~~
BSD 对 `RLIMIT_NOFILE` 的命名。

resource.RLIMIT_MEMLOCK¶
~~~
    

~~~
可能被锁定在内存中的最大地址空间。

resource.RLIMIT_VMEM¶
~~~
    

~~~
进程可能占用的最大映射内存区域。

resource.RLIMIT_AS¶
~~~
    

~~~
进程可能占用的地址空间的最大区域（以字节为单位）。

resource.RLIMIT_MSGQUEUE¶
~~~
    

~~~
可分配给 POSIX 消息队列的字节数。

[可用性](intro.md#availability): Linux >= 2.6.8。

在 3.4 版本加入.

resource.RLIMIT_NICE¶
~~~
    

~~~
进程的 Nice 级别的上限（计算为 20 - rlim_cur ）。

[可用性](intro.md#availability): Linux >= 2.6.12。

在 3.4 版本加入.

resource.RLIMIT_RTPRIO¶
~~~
    

~~~
实时优先级的上限。

[可用性](intro.md#availability): Linux >= 2.6.12。

在 3.4 版本加入.

resource.RLIMIT_RTTIME¶
~~~
    

~~~
在实时调度下，一个进程在不进行阻塞性系统调用的情况下，可以花费的 CPU 时间限制（以微秒计）。

[可用性](intro.md#availability)： Linux >= 2.6.25。

在 3.4 版本加入.

resource.RLIMIT_SIGPENDING¶
~~~
    

~~~
进程可能排队的信号数量。

[可用性](intro.md#availability): Linux >= 2.6.8。

在 3.4 版本加入.

resource.RLIMIT_SBSIZE¶
~~~
    

~~~
这个用户使用的套接字缓冲区的最大大小（字节数）。这限制了这个用户在任何时候都可以持有的网络内存数量，因此也限制了 mbufs 的数量。

[可用性](intro.md#availability): FreeBSD。

在 3.4 版本加入.

resource.RLIMIT_SWAP¶
~~~
    

~~~
这个用户 ID 的所有进程可能保留或使用的交换空间的大小上限（以字节数表示）。 此限制只有在 vm.overcommit sysctl 的 1 号比特位被设置时才会生效。 请参阅 [tuning(7)](https://man.freebsd.org/cgi/man.cgi?query=tuning&sektion=7) 获取该 sysctl 的完整描述。

[可用性](intro.md#availability): FreeBSD。

在 3.4 版本加入.

resource.RLIMIT_NPTS¶
~~~
    

~~~
该用户 ID 创建的伪终端的最大数量。

[可用性](intro.md#availability): FreeBSD。

在 3.4 版本加入.

resource.RLIMIT_KQUEUES¶
~~~
    

~~~
这个用户 ID 被允许创建的最大 kqueue 数量。

[可用性](intro.md#availability): FreeBSD >= 11。

在 3.10 版本加入.

## 资源用量¶

这些函数被用来检索资源使用信息。

resource.getrusage( _who_ )¶
~~~
    

~~~
这个函数返回一个对象，描述当前进程或其子进程所消耗的资源，由 _who_ 参数指定。 _who_ 参数应该使用下面描述的 `RUSAGE_*` 常数之一来指定。

一个简单的示例：
~~~
    
    
~~~
from resource import *
import time

# a non CPU-bound task
time.sleep(3)
print(getrusage(RUSAGE_SELF))

# a CPU-bound task
for i in range(10 ** 8):
   _ = 1 + 1
print(getrusage(RUSAGE_SELF))
~~~

返回值的字段分别描述了某一特定系统资源的使用情况，例如，在用户模式下运行的时间或进程从主内存中换出的次数。有些值取决于内部的时钟周期，例如进程使用的内存量。

为了向后兼容，返回值也可以作为一个 16 个元素的元组来访问。

返回值中的 `ru_utime` 和 `ru_stime` 字段是浮点值，分别代表在用户模式下执行的时间和在系统模式下执行的时间。其余的值是整数。关于这些值的详细信息，请查阅 _[getrusage(2)](https://manpages.debian.org/getrusage\(2\))_ man page 。这里介绍一个简短的摘要。

索引

|

字段

|

资源  
  
---|---|---  
  
`0`

|

`ru_utime`

|

用户模式下的时间（浮点数秒）  
  
`1`

|

`ru_stime`

|

系统模式下的时间（浮点数秒）  
  
`2`

|

`ru_maxrss`

|

最大的常驻内存大小  
  
`3`

|

`ru_ixrss`

|

共享内存大小  
  
`4`

|

`ru_idrss`

|

未共享的内存大小  
  
`5`

|

`ru_isrss`

|

未共享的堆栈大小  
  
`6`

|

`ru_minflt`

|

不需要 I/O 的页面故障数  
  
`7`

|

`ru_majflt`

|

需要 I/O 的页面故障数  
  
`8`

|

`ru_nswap`

|

swap out 的数量  
  
`9`

|

`ru_inblock`

|

块输入操作数  
  
`10`

|

`ru_oublock`

|

块输出操作数  
  
`11`

|

`ru_msgsnd`

|

发送消息数  
  
`12`

|

`ru_msgrcv`

|

收到消息数  
  
`13`

|

`ru_nsignals`

|

收到信号数  
  
`14`

|

`ru_nvcsw`

|

主动上下文切换  
  
`15`

|

`ru_nivcsw`

|

被动上下文切换  
  
如果指定了一个无效的 _who_ 参数，这个函数将触发一个 [`ValueError`](exceptions.md#ValueError "ValueError") 。在特殊情况下，它也可能触发 `error` 异常。

resource.getpagesize()¶

    

~~~
返回一个系统页面的字节数。（这不需要和硬件页的大小相同）。

下面的 `RUSAGE_*` 符号被传递给 `getrusage()` 函数，以指定应该为哪些进程提供信息。

resource.RUSAGE_SELF¶
~~~
    

~~~
传递给 `getrusage()` 以请求调用进程消耗的资源，这是进程中所有线程使用的资源总和。

resource.RUSAGE_CHILDREN¶
~~~
    

~~~
传递给 `getrusage()` 以请求被终止和等待的调用进程的子进程所消耗的资源。

resource.RUSAGE_BOTH¶
~~~
    

~~~
传递给 `getrusage()` 以请求当前进程和子进程所消耗的资源。并非所有系统都能使用。

resource.RUSAGE_THREAD¶
~~~
    

~~~
