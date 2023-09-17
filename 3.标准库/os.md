# `os` \--- 多种操作系统接口¶

**源代码：** [Lib/os.py](https://github.com/python/cpython/tree/3.12/Lib/os.py)

* * *

本模块提供了一种使用与操作系统相关的功能的便捷式途径。 如果你只是想读写一个文件，请参阅 [`open()`](functions.md#open "open")，如果你想操作文件路径，请参阅 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 模块，如果你想读取通过命令行给出的所有文件中的所有行，请参阅 [`fileinput`](fileinput.md#module-fileinput "fileinput: Loop over standard input or a list of files.") 模块。 为了创建临时文件和目录，请参阅 [`tempfile`](tempfile.md#module-tempfile "tempfile: Generate temporary files and directories.") 模块，对于高级文件和目录处理，请参阅 [`shutil`](shutil.md#module-shutil "shutil: High-level file operations, including copying.") 模块。

关于这些函数的适用性的说明：

  * Python中所有依赖于操作系统的内置模块的设计都是这样，只要不同的操作系统某一相同的功能可用，它就使用相同的接口。例如，函数 `os.stat(path)` 以相同的格式返回关于 _path_ 的状态信息（该格式源于 POSIX 接口）。

  * 特定于某一操作系统的扩展通过操作 `os` 模块也是可用的，但是使用它们当然是对可移植性的一种威胁。

  * 所有接受路径或文件名的函数都同时支持字节串和字符串对象，并在返回路径或文件名时使用相应类型的对象作为结果。

  * 在 VxWorks 系统上，os.popen, os.fork, os.execv 和 os.spawn*p* 都未支持。

  * 在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上，`os` 模块有很大一部分不可用或行为不同。 API 中有关进程 (如 `fork()`, `execve()`)、信号 (如 `kill()`, `wait()`) 和资源 (如 `nice()`) 的部分均不可用。 其他部分如 `getuid()` 和 `getpid()` 则是模拟或空代码段。

备注

如果使用无效或无法访问的文件名与路径，或者其他类型正确但操作系统不接受的参数，此模块的所有函数都抛出 [`OSError`](3.标准库/exceptions.md#OSError "OSError") （或者它的子类）。

_exception _os.error¶

    

~~~
内建的 [`OSError`](exceptions.md#OSError "OSError") 异常的一个别名。

os.name¶
~~~
    

~~~
导入的依赖特定操作系统的模块的名称。以下名称目前已注册: `'posix'`, `'nt'`, `'java'`.

参见

[`sys.platform`](sys.md#sys.platform "sys.platform") 具有更细的粒度。 `os.uname()` 将给出基于不同系统的版本信息。

[`platform`](platform.md#module-platform "platform: Retrieves as much platform identifying data as possible.") 模块对系统的标识有更详细的检查。

## 文件名，命令行参数，以及环境变量。¶

在 Python 中，使用字符串类型表示文件名、命令行参数和环境变量。 在某些系统上，在将这些字符串传递给操作系统之前，必须将这些字符串解码为字节。 Python 使用 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 来执行此转换（请参阅 [`sys.getfilesystemencoding()`](sys.md#sys.getfilesystemencoding "sys.getfilesystemencoding") ）。

[filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 是在 Python 启动时通过 [`PyConfig_Read()`](../c-api/init_config.md#c.PyConfig_Read "PyConfig_Read") 函数来配置的：请参阅 [`PyConfig`](../c-api/init_config.md#c.PyConfig "PyConfig") 的 [`filesystem_encoding`](../c-api/init_config.md#c.PyConfig.filesystem_encoding "PyConfig.filesystem_encoding") 和 [`filesystem_errors`](../c-api/init_config.md#c.PyConfig.filesystem_errors "PyConfig.filesystem_errors") 等成员。

在 3.1 版本发生变更: 在某些系统上，使用文件系统编码进行转换可能会失败。 在这种情况下，Python 会使用 [代理转义编码错误处理器](codecs.md#surrogateescape)，这意味着在解码时，不可解码的字节被 Unicode 字符 U+DCxx 替换，并且这些字节在编码时再次转换为原始字节。

[文件系统编码器](../glossary.md#term-filesystem-encoding-and-error-handler) 必须保证能成功解码所有 128 以内的字节。如果不能保证，API 函数可能触发 [`UnicodeError`](exceptions.md#UnicodeError "UnicodeError") 。

另请参见 [locale encoding](../glossary.md#term-locale-encoding)。

## Python UTF-8 模式¶

在 3.7 版本加入: 有关更多详细信息，请参阅 [**PEP 540**](https://peps.python.org/pep-0540/) 。

Python UTF-8 模式会忽略 [locale encoding](../glossary.md#term-locale-encoding) 并强制使用 UTF-8 编码。

  * 用 UTF-8 作为 [文件系统编码](../glossary.md#term-filesystem-encoding-and-error-handler)。

  * [`sys.getfilesystemencoding()`](sys.md#sys.getfilesystemencoding "sys.getfilesystemencoding") 返回 `'utf-8'`。

  * [`locale.getpreferredencoding()`](locale.md#locale.getpreferredencoding "locale.getpreferredencoding") 返回 `'utf-8'` ( _do_setlocale_ 参数不起作用)。

  * [`sys.stdin`](sys.md#sys.stdin "sys.stdin"), [`sys.stdout`](sys.md#sys.stdout "sys.stdout") 和 [`sys.stderr`](sys.md#sys.stderr "sys.stderr") 都将 UTF-8 用作它们的文本编码，并且为 [`sys.stdin`](sys.md#sys.stdin "sys.stdin") 和 [`sys.stdout`](sys.md#sys.stdout "sys.stdout") 启用 `surrogateescape` [错误处理句柄](codecs.md#error-handlers) ([`sys.stderr`](sys.md#sys.stderr "sys.stderr") 会继续使用 `backslashreplace` 如同在默认的局部感知模式下一样)

  * 在 Unix 上，`os.device_encoding()` 返回 `'utf-8'` 而不是设备的编码格式。

请注意 UTF-8 模式下的标准流设置可以被 [`PYTHONIOENCODING`](../using/cmdline.md#envvar-PYTHONIOENCODING) 所覆盖（在默认的区域感知模式下也同样如此）。

作为低层级 API 发生改变的结果，其他高层级 API 也会表现出不同的默认行为：

  * 命令行参数，环境变量和文件名会使用 UTF-8 编码来解码为文本。

  * `os.fsdecode()` 和 `os.fsencode()` 会使用 UTF-8 编码。

  * [`open()`](functions.md#open "open"), [`io.open()`](io.md#io.open "io.open") 和 [`codecs.open()`](codecs.md#codecs.open "codecs.open") 默认会使用 UTF-8 编码。 但是，它们默认仍将使用严格错误处理句柄，因此试图在文本模式下打开二进制文件将可能引发异常，而不是生成无意义的数据。

如果在 Python 启动时 LC_CTYPE 区域设为 `C` 或 `POSIX` ，则启用 Python UTF-8 模式 (参见 [`PyConfig_Read()`](../c-api/init_config.md#c.PyConfig_Read "PyConfig_Read") 函数)。

它可以通过命令行选项 [`-X utf8`](../using/cmdline.md#cmdoption-X) 和环境变量 [`PYTHONUTF8`](../using/cmdline.md#envvar-PYTHONUTF8)，来启用或禁用。

如果没有设置 [`PYTHONUTF8`](../using/cmdline.md#envvar-PYTHONUTF8) 环境变量，那么解释器默认使用当前的地区设置， _除非_ 当前地区识别为基于 ASCII 的传统地区（如 [`PYTHONCOERCECLOCALE`](../using/cmdline.md#envvar-PYTHONCOERCECLOCALE) 所述），并且 locale coercion 被禁用或失败。在这种传统地区，除非显式指明不要如此，解释器将默认启用 UTF-8 模式。

Python UTF-8 模式只能在 Python 启动时启用。其值可以从 [`sys.flags.utf8_mode`](sys.md#sys.flags "sys.flags") 读取。

另请参阅 [在 Windows 中的 UTF-8 模式](../using/windows.md#win-utf8-mode) 和 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler)。

参见

[**PEP 686**](https://peps.python.org/pep-0686/)
~~~
    

~~~
Python 3.15 将把 Python UTF-8 模式 设为默认值。

## 进程参数¶

这些函数和数据项提供了操作当前进程和用户的信息。

os.ctermid()¶
~~~
    

~~~
返回与进程控制终端对应的文件名。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.environ¶
~~~
    

~~~
一个 [mapping](../glossary.md#term-mapping) 对象，其中键值是代表进程环境的字符串。 例如，`environ['HOME']` 是你的主目录（在某些平台上）的路径名，相当于 C 中的 `getenv("HOME")`。

这个映射是在第一次导入 `os` 模块时捕获的，通常作为 Python 启动时处理 `site.py` 的一部分。除了通过直接修改 `os.environ` 之外，在此之后对环境所做的更改不会反映在 `os.environ` 中。

该映射除了可以用于查询环境外，还能用于修改环境。当该映射被修改时，将自动调用 `putenv()`。

在Unix系统上，键和值会使用 [`sys.getfilesystemencoding()`](sys.md#sys.getfilesystemencoding "sys.getfilesystemencoding") 和 `'surrogateescape'` 的错误处理。如果你想使用其他的编码，使用 `environb`。

在 Windows 上，这些键会被转换为大写形式。 这也会在获取、设备或删除条目时被应用。 例如，`environ['monty'] = 'python'` 会将键 `'MONTY'` 映射到值 `'python'`。

备注

直接调用 `putenv()` 并不会影响 `os.environ` ，所以推荐直接修改 `os.environ` 。

备注

在某些平台上，包括 FreeBSD 和 macOS 等，设置 `environ` 可能会导致内存泄漏。 请参阅有关 `putenv()` 的系统文档。

可以删除映射中的元素来删除对应的环境变量。当从 `os.environ` 删除元素时，以及调用 `pop()` 或 `clear()` 之一时，将自动调用 `unsetenv()`。

在 3.9 版本发生变更: 已更新并支持了 [**PEP 584**](https://peps.python.org/pep-0584/) 的合并 (`|`) 和更新 (`|=`) 运算符。

os.environb¶
~~~
    

~~~
`environ` 的字节版本：一个 [mapping](../glossary.md#term-mapping) 对象，其中键值都是 [`bytes`](stdtypes.md#bytes "bytes") 对象，代表进程环境。 `environ` 和 `environb` 是同步的（修改 `environb` 会更新 `environ`，反之亦然）。

`environb` 仅在 `supports_bytes_environ` 为 `True` 时可用。

在 3.2 版本加入.

在 3.9 版本发生变更: 已更新并支持了 [**PEP 584**](https://peps.python.org/pep-0584/) 的合并 (`|`) 和更新 (`|=`) 运算符。

os.chdir( _path_ )

os.fchdir( _fd_ )

os.getcwd()
~~~
    

~~~
以上函数请参阅 文件和目录 。

os.fsencode( _filename_ )¶
~~~
    

~~~
将 [类似路径形式的](../glossary.md#term-path-like-object) _filename_ 编码为 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler)；原样返回 [`bytes`](stdtypes.md#bytes "bytes")。

`fsdecode()` 是此函数的逆向函数。

在 3.2 版本加入.

在 3.6 版本发生变更: 增加对实现了 `os.PathLike` 接口的对象的支持。

os.fsdecode( _filename_ )¶
~~~
    

~~~
根据 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 来解码 [类似路径形式的](../glossary.md#term-path-like-object) _filename_ ；原样返回 [`str`](stdtypes.md#str "str")。

`fsencode()` 是此函数的逆向函数。

在 3.2 版本加入.

在 3.6 版本发生变更: 增加对实现了 `os.PathLike` 接口的对象的支持。

os.fspath( _path_ )¶
~~~
    

~~~
返回路径的文件系统表示。

如果传入的是 [`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 类型的字符串，将原样返回。否则 `__fspath__()` 将被调用，如果得到的是一个 [`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 类型的对象，那就返回这个值。其他所有情况则会抛出 [`TypeError`](exceptions.md#TypeError "TypeError") 异常。

在 3.6 版本加入.

_class _os.PathLike¶
~~~
    

~~~
某些对象用于表示文件系统中的路径（如 [`pathlib.PurePath`](pathlib.md#pathlib.PurePath "pathlib.PurePath") 对象），本类是这些对象的 [抽象基类](../glossary.md#term-abstract-base-class)。

在 3.6 版本加入.

_abstractmethod ___fspath__()¶
~~~
    

~~~
返回当前对象的文件系统表示。

这个方法只应该返回一个 [`str`](stdtypes.md#str "str") 字符串或 [`bytes`](stdtypes.md#bytes "bytes") 字节串，请优先选择 [`str`](stdtypes.md#str "str") 字符串。

os.getenv( _key_ , _default =None_)¶
~~~
    

~~~
如果环境变量 _key_ 存在则将其值作为字符串返回，如果不存在则返回 _default_ 。 _key_ 是一个字符串。 请注意由于 `getenv()` 使用了 `os.environ`，因此 `getenv()` 的映射同样也会在导入时被捕获，并且该函数可能无法反映未来的环境变化。

在Unix系统上，键和值会使用 [`sys.getfilesystemencoding()`](sys.md#sys.getfilesystemencoding "sys.getfilesystemencoding") 和 `'surrogateescape'` 错误处理进行解码。如果你想使用其他的编码，使用 `os.getenvb()`。

[可用性](intro.md#availability): Unix, Windows。

os.getenvb( _key_ , _default =None_)¶
~~~
    

~~~
如果环境变量 _key_ 存在则将其值作为字节串返回，如果不存在则返回 _default_ 。 _key_ 必须为字节串。 请注意由于 `getenvb()` 使用了 `os.environb`，因此 `getenvb()` 的映射同样也会在导入时被捕获，并且该函数可能无法反映未来的环境变化。

`getenvb()` 仅在 `supports_bytes_environ` 为 `True` 时可用。

[可用性](intro.md#availability): Unix。

在 3.2 版本加入.

os.get_exec_path( _env =None_)¶
~~~
    

~~~
返回将用于搜索可执行文件的目录列表，与在外壳程序中启动一个进程时相似。指定的 _env_ 应为用于搜索 PATH 的环境变量字典。默认情况下，当 _env_ 为 `None` 时，将会使用 `environ` 。

在 3.2 版本加入.

os.getegid()¶
~~~
    

~~~
返回当前进程的有效组ID。对应当前进程执行文件的 "set id" 位。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.geteuid()¶
~~~
    

~~~
返回当前进程的有效用户ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.getgid()¶
~~~
    

~~~
返回当前进程的实际组ID。

[可用性](intro.md#availability): Unix。

该函数在 Emscripten 和 WASI 上将为空代码段，请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

os.getgrouplist( _user_ , _group_ , _/_ )¶
~~~
    

~~~
返回 _user_ 所属的组 ID 列表。 如果 _group_ 不在该列表中，它将被包括在内；通常， _group_ 将会被指定为来自 _user_ 的密码记录文件的组 ID 字段，因为在其他情况下该组 ID 有可能会被略去。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入.

os.getgroups()¶
~~~
    

~~~
返回当前进程关联的附加组ID列表

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

备注

在 macOS 上，`getgroups()` 的行为与其他 Unix 平台有所不同。 如果 Python 解释器是以 `10.5` 或更早版本作为部署目标的，则 `getgroups()` 会返回与当前用户进程相关联的有效组 ID 列表；该列表受限于系统预定义的条目数量，通常为 16，并且在适当的权限下还可通过调用 `setgroups()` 来修改。 如果所用的部署目标版本大于 `10.5`，则 `getgroups()` 会返回与进程的有效用户 ID 相关联的当前组访问列表；组访问列表可能会在进程的生命周期之内发生改变，它不会受对 `setgroups()` 的调用影响，且其长度也不会被限制为 16。 部署目标值 `MACOSX_DEPLOYMENT_TARGET` 可以通过 [`sysconfig.get_config_var()`](sysconfig.md#sysconfig.get_config_var "sysconfig.get_config_var") 来获取。

os.getlogin()¶
~~~
    

~~~
返回通过控制终端进程进行登录的用户名。在多数情况下，使用 [`getpass.getuser()`](getpass.md#getpass.getuser "getpass.getuser") 会更有效，因为后者会通过检查环境变量 `LOGNAME` 或 `USERNAME` 来查找用户，再由 `pwd.getpwuid(os.getuid())[0]` 来获取当前用户 ID 的登录名。

[可用性](intro.md#availability): Unix, Windows, 非 Emscripten, 非 WASI。

os.getpgid( _pid_ )¶
~~~
    

~~~
根据进程id _pid_ 返回进程的组 ID 列表。如果 _pid_ 为 0，则返回当前进程的进程组 ID 列表

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.getpgrp()¶
~~~
    

~~~
返回当时进程组的ID

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.getpid()¶
~~~
    

~~~
返回当前进程ID

该函数在 Emscripten 和 WASI 上将为空代码段，请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

os.getppid()¶
~~~
    

~~~
返回父进程ID。当父进程已经结束，在Unix中返回的ID是初始进程(1)中的一个，在Windows中仍然是同一个进程ID，该进程ID有可能已经被进行进程所占用。

[可用性](intro.md#availability): Unix, Windows, 非 Emscripten, 非 WASI。

在 3.2 版本发生变更: 添加WIndows的支持。

os.getpriority( _which_ , _who_ )¶
~~~
    

~~~
获取程序调度优先级。 _which_ 参数值可以是 `PRIO_PROCESS`，`PRIO_PGRP`，或 `PRIO_USER` 中的一个， _who_ 是相对于 _which_ (`PRIO_PROCESS` 的进程标识符，`PRIO_PGRP` 的进程组标识符和 `PRIO_USER` 的用户ID)。当 _who_ 为 0 时（分别）表示调用的进程，调用进程的进程组或调用进程所属的真实用户 ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入.

os.PRIO_PROCESS¶

os.PRIO_PGRP¶

os.PRIO_USER¶
~~~
    

~~~
函数 `getpriority()` 和 `setpriority()` 的参数。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入.

os.PRIO_DARWIN_THREAD¶

os.PRIO_DARWIN_PROCESS¶

os.PRIO_DARWIN_BG¶

os.PRIO_DARWIN_NONUI¶
~~~
    

~~~
函数 `getpriority()` 和 `setpriority()` 的参数。

[可用性](intro.md#availability): macOS

在 3.12 版本加入.

os.getresuid()¶
~~~
    

~~~
返回一个由 (ruid, euid, suid) 所组成的元组，分别表示当前进程的真实用户ID，有效用户ID和暂存用户ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.2 版本加入.

os.getresgid()¶
~~~
    

~~~
返回一个由 (rgid, egid, sgid) 所组成的元组，分别表示当前进程的真实组ID，有效组ID和暂存组ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.2 版本加入.

os.getuid()¶
~~~
    

~~~
返回当前进程的真实用户ID。

[可用性](intro.md#availability): Unix。

该函数在 Emscripten 和 WASI 上将为空代码段，请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

os.initgroups( _username_ , _gid_ , _/_ )¶
~~~
    

~~~
调用系统 initgroups()，使用指定用户所在的所有值来初始化组访问列表，包括指定的组ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.2 版本加入.

os.putenv( _key_ , _value_ , _/_ )¶
~~~
    

~~~
将名为 _key_ 的环境变量值设置为 _value_ 。该变量名修改会影响由 `os.system()`， `popen()` ，`fork()` 和 `execv()` 发起的子进程。

对 `os.environ` 中的项目的赋值会自动转化为对 `putenv()` 的相应调用；然而，对 `putenv()` 的调用并不更新 `os.environ` ，所以实际上最好是赋值到 `os.environ` 的项目。这也适用于 `getenv()` 和 `getenvb()` ，它们分别使用 `os.environ` 和 `os.environb` 在它们的实现中。

备注

在某些平台上，包括 FreeBSD 和 macOS 等，设置 `environ` 可能会导致内存泄漏。 请参阅有关 `putenv()` 的系统文档。

引发一个 [审计事件](sys.md#auditing) `os.putenv`，附带参数 `key`, `value`。

在 3.9 版本发生变更: 该函数现在总是可用。

os.setegid( _egid_ , _/_ )¶
~~~
    

~~~
设置当前进程的有效组ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.seteuid( _euid_ , _/_ )¶
~~~
    

~~~
设置当前进程的有效用户ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.setgid( _gid_ , _/_ )¶
~~~
    

~~~
设置当前进程的组ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.setgroups( _groups_ , _/_ )¶
~~~
    

~~~
将 _group_ 参数值设置为与当进程相关联的附加组ID列表。 _group_ 参数必须为一个序列，每个元素应为每个组的数字ID。该操作通常只适用于超级用户。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

备注

在 macOS 中， _groups_ 的长度不能超过系统定义的最大有效组 ID 数量，通常为 16。 对于未返回与调用 setgroups() 产生的相同组列表的情况，请参阅 `getgroups()` 的文档。

os.setns( _fd_ , _nstype =0_)¶
~~~
    

~~~
将当前线程与 Linux 命名空间重新关联。 详情参见 _[setns(2)](https://manpages.debian.org/setns\(2\))_ 和 _[namespaces(7)](https://manpages.debian.org/namespaces\(7\))_ 手册页面。

如果 _fd_ 是指向一个 `/proc/ _pid_ /ns/` 链接，`setns()` 会将调用线程与该链接所关联的命名空间重新关联起来，并且 _nstype_ 可以设为某个 CLONE_NEW* 常量 以便对操作施加约束 (`0` 表示没有任何约束)。

Since Linux 5.8, _fd_ may refer to a PID file descriptor obtained from `pidfd_open()`. In this case, `setns()` reassociates the calling thread into one or more of the same namespaces as the thread referred to by _fd_. This is subject to any constraints imposed by _nstype_ , which is a bit mask combining one or more of the CLONE_NEW* constants, e.g. `setns(fd, os.CLONE_NEWUTS | os.CLONE_NEWPID)`. The caller's memberships in unspecified namespaces are left unchanged.

_fd_ 可以是任何带有 [`fileno()`](io.md#io.IOBase.fileno "io.IOBase.fileno") 方法的对象，或是一个原始文件描述符。

此示例将线程与 `init` 进程的网络命名空间进行了重新关联:
~~~
    
    
~~~
fd = os.open("/proc/1/ns/net", os.O_RDONLY)
os.setns(fd, os.CLONE_NEWNET)
os.close(fd)
~~~

[可用性](3.标准库/intro.md#availability): Linux >= 3.0 且 glibc >= 2.14。

在 3.12 版本加入.

参见

`unshare()` 函数。

os.setpgrp()¶

    

~~~
Call the system call `setpgrp()` or `setpgrp(0, 0)` depending on which version is implemented (if any). See the Unix manual for the semantics.

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.setpgid( _pid_ , _pgrp_ , _/_ )¶
~~~
    

~~~
Call the system call `setpgid()` to set the process group id of the process with id _pid_ to the process group with id _pgrp_. See the Unix manual for the semantics.

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.setpriority( _which_ , _who_ , _priority_ )¶
~~~
    

~~~
设置程序调度优先级。 _which_ 的值为 `PRIO_PROCESS`, `PRIO_PGRP` 或 `PRIO_USER` 之一，而 _who_ 会相对于 _which_ (`PRIO_PROCESS` 的进程标识符, `PRIO_PGRP` 的进程组标识符和 `PRIO_USER` 的用户 ID) 被解析。 _who_ 值为零 (分别) 表示调用进程，调用进程的进程组或调用进程的真实用户 ID。 _priority_ 是范围在 -20 至 19 的值。 默认优先级为 0；较小的优先级数值会更优先被调度。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入.

os.setregid( _rgid_ , _egid_ , _/_ )¶
~~~
    

~~~
设置当前进程的真实和有效组ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.setresgid( _rgid_ , _egid_ , _sgid_ , _/_ )¶
~~~
    

~~~
设置当前进程的真实，有效和暂存组ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.2 版本加入.

os.setresuid( _ruid_ , _euid_ , _suid_ , _/_ )¶
~~~
    

~~~
设置当前进程的真实，有效和暂存用户ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.2 版本加入.

os.setreuid( _ruid_ , _euid_ , _/_ )¶
~~~
    

~~~
设置当前进程的真实和有效用户ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.getsid( _pid_ , _/_ )¶
~~~
    

~~~
调用系统调用 `getsid()`。 其语义请参见 Unix 手册。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.setsid()¶
~~~
    

~~~
调用系统调用 `setsid()`。 其语义请参见 Unix 手册。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.setuid( _uid_ , _/_ )¶
~~~
    

~~~
设置当前进程的用户ID。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.strerror( _code_ , _/_ )¶
~~~
    

~~~
Return the error message corresponding to the error code in _code_. On platforms where `strerror()` returns `NULL` when given an unknown error number, [`ValueError`](exceptions.md#ValueError "ValueError") is raised.

os.supports_bytes_environ¶
~~~
    

~~~
如果操作系统上原生环境类型是字节型则为 `True` (例如在 Windows 上为 `False`)。

在 3.2 版本加入.

os.umask( _mask_ , _/_ )¶
~~~
    

~~~
设定当前数值掩码并返回之前的掩码。

该函数在 Emscripten 和 WASI 上将为空代码段，请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

os.uname()¶
~~~
    

~~~
返回当前操作系统的识别信息。返回值是一个有5个属性的对象：

  * `sysname` \- 操作系统名

  * `nodename` \- 机器在网络上的名称（需要先设定）

  * `release` \- 操作系统发行信息

  * `version` \- 操作系统版本信息

  * `machine` \- 硬件标识符

为了向后兼容，该对象也是可迭代的，像是一个按照 `sysname`，`nodename`，`release`，`version`，和 `machine` 顺序组成的元组。

有些系统会将 `nodename` 截短为 8 个字符或截短至前缀部分；获取主机名的一个更好方式是 [`socket.gethostname()`](socket.md#socket.gethostname "socket.gethostname") 或甚至可以用 `socket.gethostbyaddr(socket.gethostname())`。

[可用性](intro.md#availability): Unix。

在 3.3 版本发生变更: 返回结果的类型由元组变成一个类似元组的对象，同时具有命名的属性。

os.unsetenv( _key_ , _/_ )¶
~~~
    

~~~
取消设置（删除）名为 _key_ 的环境变量。变量名的改变会影响由 `os.system()`，`popen()`，`fork()` 和 `execv()` 触发的子进程。

删除 `os.environ` 中的项目会自动转化为对 `unsetenv()` 的相应调用；然而，对 `unsetenv()` 的调用并不更新 `os.environ` ，所以实际上最好是删除 `os.environ` 的项目。

引发一个 [审计事件](sys.md#auditing) `os.unsetenv`，附带参数 `key`。

在 3.9 版本发生变更: 该函数现在总是可用，并且在 Windows 上也可用。

os.unshare( _flags_ )¶
~~~
    

~~~
Disassociate parts of the process execution context, and move them into a newly created namespace. See the _[unshare(2)](https://manpages.debian.org/unshare\(2\))_ man page for more details. The _flags_ argument is a bit mask, combining zero or more of the CLONE_* constants, that specifies which parts of the execution context should be unshared from their existing associations and moved to a new namespace. If the _flags_ argument is `0`, no changes are made to the calling process's execution context.

[Availability](intro.md#availability): Linux >= 2.6.16.

在 3.12 版本加入.

参见

`setns()` 函数。

`unshare()` 函数的旗标，如果实现支持。 访问 Linux 手册中的 _[unshare(2)](https://manpages.debian.org/unshare\(2\))_ 以获取关于实际影响和可用性的信息。

os.CLONE_FILES¶

os.CLONE_FS¶

os.CLONE_NEWCGROUP¶

os.CLONE_NEWIPC¶

os.CLONE_NEWNET¶

os.CLONE_NEWNS¶

os.CLONE_NEWPID¶

os.CLONE_NEWTIME¶

os.CLONE_NEWUSER¶

os.CLONE_NEWUTS¶

os.CLONE_SIGHAND¶

os.CLONE_SYSVSEM¶

os.CLONE_THREAD¶

os.CLONE_VM¶
~~~
    

~~~
## 创建文件对象¶

这些函数创建新的 [file objects](../glossary.md#term-file-object) 。（参见 `open()` 以获取打开文件描述符的相关信息。）

os.fdopen( _fd_ , _* args_, _** kwargs_)¶
~~~
    

~~~
返回打开文件描述符 _fd_ 对应文件的对象。类似内建 [`open()`](functions.md#open "open") 函数，二者接受同样的参数。不同之处在于 `fdopen()` 第一个参数应该为整数。

## 文件描述符操作¶

这些函数对文件描述符所引用的 I/O 流进行操作。

文件描述符是一些小的整数，对应于当前进程所打开的文件。例如，标准输入的文件描述符通常是0，标准输出是1，标准错误是2。之后被进程打开的文件的文件描述符会被依次指定为3，4，5等。“文件描述符”这个词有点误导性，在 Unix 平台中套接字和管道也被文件描述符所引用。

当需要时，可以用 [`fileno()`](io.md#io.IOBase.fileno "io.IOBase.fileno") 可以获得 [file object](../glossary.md#term-file-object) 所对应的文件描述符。需要注意的是，直接使用文件描述符会绕过文件对象的方法，会忽略如数据内部缓冲等情况。

os.close( _fd_ )¶
~~~
    

~~~
关闭文件描述符 _fd_ 。

备注

该功能适用于低级 I/O 操作，必须用于 `os.open()` 或 `pipe()` 返回的文件描述符。若要关闭由内建函数 [`open()`](functions.md#open "open")、`popen()` 或 `fdopen()` 返回的 "文件对象"，则应使用其相应的 [`close()`](io.md#io.IOBase.close "io.IOBase.close") 方法。

os.closerange( _fd_low_ , _fd_high_ , _/_ )¶
~~~
    

~~~
关闭从 _fd_low_ （包括）到 _fd_high_ （排除）间的文件描述符，并忽略错误。类似（但快于）:
~~~
    
    
~~~
for fd in range(fd_low, fd_high):
    try:
        os.close(fd)
    except OSError:
        pass
~~~

os.copy_file_range( _src_ , _dst_ , _count_ , _offset_src =None_, _offset_dst =None_)¶

    

~~~
Copy _count_ bytes from file descriptor _src_ , starting from offset _offset_src_ , to file descriptor _dst_ , starting from offset _offset_dst_. If _offset_src_ is None, then _src_ is read from the current position; respectively for _offset_dst_.

In Linux kernel older than 5.3, the files pointed by _src_ and _dst_ must reside in the same filesystem, otherwise an [`OSError`](exceptions.md#OSError "OSError") is raised with [`errno`](exceptions.md#OSError.errno "OSError.errno") set to [`errno.EXDEV`](errno.md#errno.EXDEV "errno.EXDEV").

This copy is done without the additional cost of transferring data from the kernel to user space and then back into the kernel. Additionally, some filesystems could implement extra optimizations, such as the use of reflinks (i.e., two or more inodes that share pointers to the same copy-on-write disk blocks; supported file systems include btrfs and XFS) and server-side copy (in the case of NFS).

The function copies bytes between two file descriptors. Text options, like the encoding and the line ending, are ignored.

返回值是复制的字节的数目。这可能低于需求的数目。

备注

On Linux, `os.copy_file_range()` should not be used for copying a range of a pseudo file from a special filesystem like procfs and sysfs. It will always copy no bytes and return 0 as if the file was empty because of a known Linux kernel issue.

[可用性](intro.md#availability): Linux >= 4.5 且 glibc >= 2.27。

在 3.8 版本加入.

os.device_encoding( _fd_ )¶
~~~
    

~~~
如果连接到终端，则返回一个与 _fd_ 关联的设备描述字符，否则返回 [`None`](constants.md#None "None")。

在 Unix 上，如果启用了 Python UTF-8 模式，则返回 `'UTF-8'` 而不是设备的编码格式。

在 3.10 版本发生变更: 在 Unix 上，该函数现在实现了 Python UTF-8 模式。

os.dup( _fd_ , _/_ )¶
~~~
    

~~~
返回一个文件描述符 _fd_ 的副本。该文件描述符的副本是 不可继承的。

在 Windows 中，当复制一个标准流（0: stdin, 1: stdout, 2: stderr）时，新的文件描述符是 可继承的。

[可用性](intro.md#availability): 非 WASI。

在 3.4 版本发生变更: 新的文件描述符现在是不可继承的。

os.dup2( _fd_ , _fd2_ , _inheritable =True_)¶
~~~
    

~~~
把文件描述符 _fd_ 复制为 _fd2_ ，必要时先关闭后者。返回 _fd2_ 。新的文件描述符默认是 可继承的，除非在 _inheritable_ 为 `False` 时，是不可继承的。

[可用性](intro.md#availability): 非 WASI。

在 3.4 版本发生变更: 添加可选参数 _inheritable_ 。

在 3.7 版本发生变更: 成功时返回 _fd2_ ，以过去的版本中，总是返回 `None`。

os.fchmod( _fd_ , _mode_ )¶
~~~
    

~~~
将 _fd_ 指定文件的权限状态修改为 _mode_ 。可以参考 `chmod()` 中列出 _mode_ 的可用值。从Python 3.3开始，这相当于 `os.chmod(fd, mode)`。

引发一个 [审计事件](sys.md#auditing) `os.chmod`，附带参数 `path`、`mode`、`dir_fd`。

[可用性](intro.md#availability): Unix。

该函数 Emscripten 和 WASI 将受到限制，请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

os.fchown( _fd_ , _uid_ , _gid_ )¶
~~~
    

~~~
分别将 _fd_ 指定文件的所有者和组 ID 修改为 _uid_ 和 _gid_ 的值。若不想变更其中的某个 ID，可将相应值设为 -1。参考 `chown()`。从 Python 3.3 开始，这相当于 `os.chown(fd, uid, gid)`。

引发一个 [审计事件](sys.md#auditing) `os.chown`，附带参数 `path`、`uid`、`gid`、`dir_fd`。

[可用性](intro.md#availability): Unix。

该函数 Emscripten 和 WASI 将受到限制，请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

os.fdatasync( _fd_ )¶
~~~
    

~~~
强制将文件描述符 _fd_ 指定文件写入磁盘。不强制更新元数据。

[可用性](intro.md#availability): Unix。

备注

该功能在 MacOS 中不可用。

os.fpathconf( _fd_ , _name_ , _/_ )¶
~~~
    

~~~
返回与打开的文件有关的系统配置信息。 _name_ 指定要查找的配置名称，它可以是字符串，是一个系统已定义的名称，这些名称定义在不同标准（POSIX.1，Unix 95，Unix 98 等）中。一些平台还定义了额外的其他名称。当前操作系统已定义的名称在 `pathconf_names` 字典中给出。对于未包含在该映射中的配置名称，也可以传递一个整数作为 _name_ 。

如果 _name_ 是一个字符串且不是已定义的名称，将抛出 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。如果当前系统不支持 _name_ 指定的配置名称，即使该名称存在于 `pathconf_names`，也会抛出 [`OSError`](exceptions.md#OSError "OSError") 异常，错误码为 [`errno.EINVAL`](errno.md#errno.EINVAL "errno.EINVAL")。

从 Python 3.3 起，此功能等价于 `os.pathconf(fd, name)`。

[可用性](intro.md#availability): Unix。

os.fstat( _fd_ )¶
~~~
    

~~~
获取文件描述符 _fd_ 的状态. 返回一个 `stat_result` 对象。

从 Python 3.3 起，此功能等价于 `os.stat(fd)`。

参见

`stat()` 函数。

os.fstatvfs( _fd_ , _/_ )¶
~~~
    

~~~
返回文件系统的信息，该文件系统是文件描述符 _fd_ 指向的文件所在的文件系统，与 `statvfs()` 一样。从 Python 3.3 开始，它等效于 `os.statvfs(fd)`。

[可用性](intro.md#availability): Unix。

os.fsync( _fd_ )¶
~~~
    

~~~
Force write of file with filedescriptor _fd_ to disk. On Unix, this calls the native `fsync()` function; on Windows, the MS `_commit()` function.

如果要写入的是缓冲区内的 Python [文件对象](../glossary.md#term-file-object) _f_ ，请先执行 `f.flush()`，然后执行 `os.fsync(f.fileno())`，以确保与 _f_ 关联的所有内部缓冲区都写入磁盘。

[可用性](intro.md#availability): Unix, Windows。

os.ftruncate( _fd_ , _length_ , _/_ )¶
~~~
    

~~~
截断文件描述符 _fd_ 指向的文件，以使其最大为 _length_ 字节。从 Python 3.3 开始，它等效于 `os.truncate(fd, length)`。

引发一个 [审计事件](sys.md#auditing) `os.truncate`，附带参数 `fd`, `length`。

[可用性](intro.md#availability): Unix, Windows。

在 3.5 版本发生变更: 添加了 Windows 支持

os.get_blocking( _fd_ , _/_ )¶
~~~
    

~~~
获取文件描述符的阻塞模式：如果设置了 `O_NONBLOCK` 标志位，返回 `False`，如果该标志位被清除，返回 `True`。

参见 `set_blocking()` 和 [`socket.socket.setblocking()`](socket.md#socket.socket.setblocking "socket.socket.setblocking")。

[可用性](intro.md#availability): Unix, Windows。

该函数 Emscripten 和 WASI 将受到限制，请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

On Windows, this function is limited to pipes.

在 3.5 版本加入.

在 3.12 版本发生变更: 增加了在Windows上对于管道的支持。

os.isatty( _fd_ , _/_ )¶
~~~
    

~~~
如果文件描述符 _fd_ 打开且已连接至 tty 设备（或类 tty 设备），返回 `True`，否则返回 `False`。

os.lockf( _fd_ , _cmd_ , _len_ , _/_ )¶
~~~
    

~~~
在打开的文件描述符上，使用、测试或删除 POSIX 锁。 _fd_ 是一个打开的文件描述符。 _cmd_ 指定要进行的操作，它们是 `F_LOCK`、`F_TLOCK`、`F_ULOCK` 或 `F_TEST` 中的一个。 _len_ 指定哪部分文件需要锁定。

引发一个 [审计事件](sys.md#auditing) `os.lockf`，附带参数 `fd`、`cmd`、`len`。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

os.F_LOCK¶

os.F_TLOCK¶

os.F_ULOCK¶

os.F_TEST¶
~~~
    

~~~
标志位，用于指定 `lockf()` 进行哪一种操作。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

os.login_tty( _fd_ , _/_ )¶
~~~
    

~~~
准备 tty 设置 fd 为新登录会话的文件描述符。 设置调用方进程为会话主进程；设置该 tty 为主控 tty，调用方进程使用其 stdin, stdout 和 stderr；关闭 fd。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.11 版本加入.

os.lseek( _fd_ , _pos_ , _whence_ , _/_ )¶
~~~
    

~~~
将文件描述符 _fd_ 的当前位置设为位置 _pos_ ，经 _whence_ 修正，并返回相对于文件开头的以字节为单位的新位置。 _whence_ 的有效值为：

  * `SEEK_SET` 或 `0` \-- 相对于文件开头设置 _pos_

  * `SEEK_CUR` 或 `1` \-- 相对于当前文件位置设置 _pos_

  * `SEEK_END` 或 `2` \-- 相对于文件末尾设置 _pos_

  * `SEEK_HOLE` \-- 将 _pos_ 设置为相对于 _pos_ 的下一个数据位置

  * `SEEK_DATA` \-- set _pos_ to the next data hole, relative to _pos_

在 3.3 版本发生变更: Add support for `SEEK_HOLE` and `SEEK_DATA`.

os.SEEK_SET¶

os.SEEK_CUR¶

os.SEEK_END¶
~~~
    

~~~
Parameters to the `lseek()` function and the [`seek()`](io.md#io.IOBase.seek "io.IOBase.seek") method on [file-like objects](../glossary.md#term-file-object), for whence to adjust the file position indicator.

`SEEK_SET`
~~~
    

~~~
Adjust the file position relative to the beginning of the file.

`SEEK_CUR`
~~~
    

~~~
Adjust the file position relative to the current file position.

`SEEK_END`
~~~
    

~~~
Adjust the file position relative to the end of the file.

Their values are 0, 1, and 2, respectively.

os.SEEK_HOLE¶

os.SEEK_DATA¶
~~~
    

~~~
Parameters to the `lseek()` function and the [`seek()`](io.md#io.IOBase.seek "io.IOBase.seek") method on [file-like objects](../glossary.md#term-file-object), for seeking file data and holes on sparsely allocated files.

`SEEK_DATA`
~~~
    

~~~
Adjust the file offset to the next location containing data, relative to the seek position.

`SEEK_HOLE`
~~~
    

~~~
Adjust the file offset to the next location containing a hole, relative to the seek position. A hole is defined as a sequence of zeros.

备注

These operations only make sense for filesystems that support them.

[Availability](intro.md#availability): Linux >= 3.1, macOS, Unix

在 3.3 版本加入.

os.open( _path_ , _flags_ , _mode =0o777_, _*_ , _dir_fd =None_)¶
~~~
    

~~~
打开文件 _path_ ，根据 _flags_ 设置各种标志位，并根据 _mode_ 设置其权限状态。当计算 _mode_ 时，会首先根据当前 umask 值将部分权限去除。本方法返回新文件的描述符。新的文件描述符是 不可继承 的。

有关 flag 和 mode 取值的说明，请参见 C 运行时文档。标志位常量（如 `O_RDONLY` 和 `O_WRONLY`）在 `os` 模块中定义。特别地，在 Windows 上需要添加 `O_BINARY` 才能以二进制模式打开文件。

本函数带有 _dir_fd_ 参数，支持 基于目录描述符的相对路径。

`open` 附带参数 `path`、`mode`、`flags` 会引发 [审计事件](sys.md#auditing)。

在 3.4 版本发生变更: 新的文件描述符现在是不可继承的。

备注

本函数适用于底层的 I/O。常规用途请使用内置函数 [`open()`](functions.md#open "open")，该函数的 `read()` 和 `write()` 方法（及其他方法）会返回 [文件对象](../glossary.md#term-file-object)。要将文件描述符包装在文件对象中，请使用 `fdopen()`。

在 3.3 版本加入: _dir_fd_ 参数。

在 3.5 版本发生变更: 如果系统调用被中断，但信号处理程序没有触发异常，此函数现在会重试系统调用，而不是触发 [`InterruptedError`](exceptions.md#InterruptedError "InterruptedError") 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

以下常量是 `open()` 函数 _flags_ 参数的选项。可以用按位或运算符 `|` 将它们组合使用。部分常量并非在所有平台上都可用。有关其可用性和用法的说明，请参阅 _[open(2)](https://manpages.debian.org/open\(2\))_ 手册（Unix 上）或 [MSDN](https://msdn.microsoft.com/en-us/library/z0kc8e3z.aspx) （Windows 上）。

os.O_RDONLY¶

os.O_WRONLY¶

os.O_RDWR¶

os.O_APPEND¶

os.O_CREAT¶

os.O_EXCL¶

os.O_TRUNC¶
~~~
    

~~~
上述常量在 Unix 和 Windows 上均可用。

os.O_DSYNC¶

os.O_RSYNC¶

os.O_SYNC¶

os.O_NDELAY¶

os.O_NONBLOCK¶

os.O_NOCTTY¶

os.O_CLOEXEC¶
~~~
    

~~~
这个常数仅在 Unix 系统中可用。

在 3.3 版本发生变更: 增加 `O_CLOEXEC` 常量。

os.O_BINARY¶

os.O_NOINHERIT¶

os.O_SHORT_LIVED¶

os.O_TEMPORARY¶

os.O_RANDOM¶

os.O_SEQUENTIAL¶

os.O_TEXT¶
~~~
    

~~~
这个常数仅在 Windows 系统中可用。

os.O_EVTONLY¶

os.O_FSYNC¶

os.O_SYMLINK¶

os.O_NOFOLLOW_ANY¶
~~~
    

~~~
以上常量仅适用于 macOS。

在 3.10 版本发生变更: 加入 `O_EVTONLY` 、 `O_FSYNC` 、 `O_SYMLINK` 和 `O_NOFOLLOW_ANY` 常量。

os.O_ASYNC¶

os.O_DIRECT¶

os.O_DIRECTORY¶

os.O_NOFOLLOW¶

os.O_NOATIME¶

os.O_PATH¶

os.O_TMPFILE¶

os.O_SHLOCK¶

os.O_EXLOCK¶
~~~
    

~~~
上述常量是扩展常量，如果 C 库未定义它们，则不存在。

在 3.4 版本发生变更: 在支持的系统上增加 `O_PATH`。增加 `O_TMPFILE`，仅在 Linux Kernel 3.11 或更高版本可用。

os.openpty()¶
~~~
    

~~~
打开一对新的伪终端，返回一对文件描述符 `（主，从）`，分别为 pty 和 tty。新的文件描述符是 不可继承 的。对于（稍微）轻量一些的方法，请使用 [`pty`](pty.md#module-pty "pty: Pseudo-Terminal Handling for Unix. \(Unix\)") 模块。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.4 版本发生变更: 新的文件描述符不再可继承。

os.pipe()¶
~~~
    

~~~
创建一个管道，返回一对分别用于读取和写入的文件描述符 `(r, w)`。新的文件描述符是 不可继承 的。

[可用性](intro.md#availability): Unix, Windows。

在 3.4 版本发生变更: 新的文件描述符不再可继承。

os.pipe2( _flags_ , _/_ )¶
~~~
    

~~~
创建带有 _flags_ 标志位的管道。可通过对以下一个或多个值进行“或”运算来构造这些 _flags_ ：`O_NONBLOCK`、`O_CLOEXEC`。返回一对分别用于读取和写入的文件描述符 `(r, w)`。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入.

os.posix_fallocate( _fd_ , _offset_ , _len_ , _/_ )¶
~~~
    

~~~
确保为 _fd_ 指向的文件分配了足够的磁盘空间，该空间从偏移量 _offset_ 开始，到 _len_ 字节为止。

[可用性](intro.md#availability): Unix, 非 Emscripten。

在 3.3 版本加入.

os.posix_fadvise( _fd_ , _offset_ , _len_ , _advice_ , _/_ )¶
~~~
    

~~~
声明即将以特定模式访问数据，使内核可以提前进行优化。数据范围是从 _fd_ 所指向文件的 _offset_ 开始，持续 _len_ 个字节。 _advice_ 的取值是如下之一：`POSIX_FADV_NORMAL`, `POSIX_FADV_SEQUENTIAL`, `POSIX_FADV_RANDOM`, `POSIX_FADV_NOREUSE`, `POSIX_FADV_WILLNEED` 或 `POSIX_FADV_DONTNEED`。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

os.POSIX_FADV_NORMAL¶

os.POSIX_FADV_SEQUENTIAL¶

os.POSIX_FADV_RANDOM¶

os.POSIX_FADV_NOREUSE¶

os.POSIX_FADV_WILLNEED¶

os.POSIX_FADV_DONTNEED¶
~~~
    

~~~
用于 `posix_fadvise()` 的 _advice_ 参数的标志位，指定可能使用的访问模式。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

os.pread( _fd_ , _n_ , _offset_ , _/_ )¶
~~~
    

~~~
从文件描述符 _fd_ 所指向文件的偏移位置 _offset_ 开始，读取至多 _n_ 个字节，而保持文件偏移量不变。

返回所读取字节的字节串 (bytestring)。如果到达了 _fd_ 指向的文件末尾，则返回空字节对象。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

os.preadv( _fd_ , _buffers_ , _offset_ , _flags =0_, _/_ )¶
~~~
    

~~~
从文件描述符 _fd_ 所指向文件的偏移位置 _offset_ 开始，将数据读取至可变 [字节类对象](../glossary.md#term-bytes-like-object) 缓冲区 _buffers_ 中，保持文件偏移量不变。将数据依次存放到每个缓冲区中，填满一个后继续存放到序列中的下一个缓冲区，来保存其余数据。

flags 参数可以由零个或多个标志位进行按位或运算来得到：

  * `RWF_HIPRI`

  * `RWF_NOWAIT`

返回实际读取的字节总数，该总数可以小于所有对象的总容量。

操作系统可能对允许使用的缓冲区数量有限制（使用 `sysconf()` 获取 `'SC_IOV_MAX'` 值）。

本方法结合了 `os.readv()` 和 `os.pread()` 的功能。

[可用性](intro.md#availability): Linux >= 2.6.30, FreeBSD >= 6.0, OpenBSD >= 2.7, AIX >= 7.1。

使用旗标需要 Linux >= 4.6。

在 3.7 版本加入.

os.RWF_NOWAIT¶
~~~
    

~~~
不要等待无法立即获得的数据。如果指定了此标志，那么当需要从后备存储器中读取数据，或等待文件锁时，系统调用将立即返回。

If some data was successfully read, it will return the number of bytes read. If no bytes were read, it will return `-1` and set errno to [`errno.EAGAIN`](errno.md#errno.EAGAIN "errno.EAGAIN").

[可用性](intro.md#availability): Linux >= 4.14。

在 3.7 版本加入.

os.RWF_HIPRI¶
~~~
    

~~~
高优先级读/写。允许基于块的文件系统对设备进行轮询，这样可以降低延迟，但可能会占用更多资源。

目前在 Linux 上，此功能仅在使用 `O_DIRECT` 标志打开的文件描述符上可用。

[可用性](intro.md#availability): Linux >= 4.6。

在 3.7 版本加入.

os.pwrite( _fd_ , _str_ , _offset_ , _/_ )¶
~~~
    

~~~
将 _str_ 中的字节串 (bytestring) 写入文件描述符 _fd_ 的偏移位置 _offset_ 处，保持文件偏移量不变。

返回实际写入的字节数。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

os.pwritev( _fd_ , _buffers_ , _offset_ , _flags =0_, _/_ )¶
~~~
    

~~~
将缓冲区 _buffers_ 的内容写入文件描述符 _fd_ 的偏移位置 _offset_ 处，保持文件偏移量不变。缓冲区 _buffers_ 必须是由 [字节类对象](../glossary.md#term-bytes-like-object) 组成的序列。缓冲区以数组顺序处理。先写入第一个缓冲区的全部内容，再写入第二个缓冲区，照此继续。

flags 参数可以由零个或多个标志位进行按位或运算来得到：

  * `RWF_DSYNC`

  * `RWF_SYNC`

  * `RWF_APPEND`

返回实际写入的字节总数。

操作系统可能对允许使用的缓冲区数量有限制（使用 `sysconf()` 获取 `'SC_IOV_MAX'` 值）。

本方法结合了 `os.writev()` 和 `os.pwrite()` 的功能。

[可用性](intro.md#availability): Linux >= 2.6.30, FreeBSD >= 6.0, OpenBSD >= 2.7, AIX >= 7.1。

使用旗标需要 Linux >= 4.6。

在 3.7 版本加入.

os.RWF_DSYNC¶
~~~
    

~~~
提供预写功能，等效于带 `O_DSYNC` 标志的 `os.open()` 。本标志只作用于通过系统调用写入的数据。

[可用性](intro.md#availability)： Linux >= 4.7。

在 3.7 版本加入.

os.RWF_SYNC¶
~~~
    

~~~
提供预写功能，等效于带 `O_SYNC` 标志的 `os.open()` 。本标志只作用于通过系统调用写入的数据。

[可用性](intro.md#availability)： Linux >= 4.7。

在 3.7 版本加入.

os.RWF_APPEND¶
~~~
    

~~~
提供预写功能，等效于带 `O_APPEND` 标志的 `os.open()` 。本标志只对 `os.pwritev()` 有意义，只作用于通过系统调用写入的数据。参数 _offset_ 对写入操作无效；数据总是会添加到文件的末尾。但如果 _offset_ 参数为 `-1`，则会刷新当前文件的 _offset_ 。

[可用性](intro.md#availability): Linux >= 4.16。

在 3.10 版本加入.

os.read( _fd_ , _n_ , _/_ )¶
~~~
    

~~~
从文件描述符 _fd_ 中读取至多 _n_ 个字节。

返回所读取字节的字节串 (bytestring)。如果到达了 _fd_ 指向的文件末尾，则返回空字节对象。

备注

该功能适用于低级 I/O 操作，必须用于 `os.open()` 或 `pipe()` 返回的文件描述符。若要读取由内建函数 [`open()`](functions.md#open "open")、`popen()`、`fdopen()` 或 [`sys.stdin`](sys.md#sys.stdin "sys.stdin") 返回的 "文件对象"，则应使用其相应的 `read()` 或 `readline()` 方法。

在 3.5 版本发生变更: 如果系统调用被中断，但信号处理程序没有触发异常，此函数现在会重试系统调用，而不是触发 [`InterruptedError`](exceptions.md#InterruptedError "InterruptedError") 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

os.sendfile( _out_fd_ , _in_fd_ , _offset_ , _count_ )¶

os.sendfile( _out_fd_ , _in_fd_ , _offset_ , _count_ , _headers =()_, _trailers =()_, _flags =0_)
~~~
    

~~~
将文件描述符 _in_fd_ 中的 _count_ 字节复制到文件描述符 _out_fd_ 的偏移位置 _offset_ 处。返回复制的字节数，如果到达 EOF，返回 `0`。

定义了 `sendfile()` 的所有平台均支持第一种函数用法。

在 Linux 上，将 _offset_ 设置为 `None`，则从 _in_fd_ 的当前位置开始读取，并更新 _in_fd_ 的位置。

第二种情况可以被用于 macOS 和 FreeBSD，其中 _headers_ 和 _trailers_ 是任意的缓冲区序列，它们会在写入来自 _in_fd_ 的数据之前被写入。 它的返回内容与第一种情况相同。

在 macOS 和 FreeBSD 上，传入 `0` 值作为 _count_ 将指定持续发送直至到达 _in_fd_ 的末尾。

所有平台都支持将套接字作为 _out_fd_ 文件描述符，有些平台也支持其他类型（如常规文件或管道）。

跨平台应用程序不应使用 _headers_ 、 _trailers_ 和 _flags_ 参数。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

备注

有关 `sendfile()` 的高级封装，参见 [`socket.socket.sendfile()`](socket.md#socket.socket.sendfile "socket.socket.sendfile")。

在 3.3 版本加入.

在 3.9 版本发生变更: _out_ 和 _in_ 参数被重命名为 _out_fd_ 和 _in_fd_ 。

os.SF_NODISKIO¶

os.SF_MNOWAIT¶

os.SF_SYNC¶
~~~
    

~~~
`sendfile()` 函数的参数（假设当前实现支持这些参数）。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入.

os.SF_NOCACHE¶
~~~
    

~~~
传给 `sendfile()` 函数的形参，如果具体实现支持的话。 数据不会缓存在虚拟内存中并将随即被释放。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.11 版本加入.

os.set_blocking( _fd_ , _blocking_ , _/_ )¶
~~~
    

~~~
设置指定文件描述符的阻塞模式：如果 blocking 为 `False`，则为该描述符设置 `O_NONBLOCK` 标志位，反之则清除该标志位。

参见 `get_blocking()` 和 [`socket.socket.setblocking()`](socket.md#socket.socket.setblocking "socket.socket.setblocking")。

[可用性](intro.md#availability): Unix, Windows。

该函数 Emscripten 和 WASI 将受到限制，请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

On Windows, this function is limited to pipes.

在 3.5 版本加入.

在 3.12 版本发生变更: 增加了在Windows上对于管道的支持。

os.splice( _src_ , _dst_ , _count_ , _offset_src =None_, _offset_dst =None_)¶
~~~
    

~~~
Transfer _count_ bytes from file descriptor _src_ , starting from offset _offset_src_ , to file descriptor _dst_ , starting from offset _offset_dst_. At least one of the file descriptors must refer to a pipe. If _offset_src_ is None, then _src_ is read from the current position; respectively for _offset_dst_. The offset associated to the file descriptor that refers to a pipe must be `None`. The files pointed by _src_ and _dst_ must reside in the same filesystem, otherwise an [`OSError`](exceptions.md#OSError "OSError") is raised with [`errno`](exceptions.md#OSError.errno "OSError.errno") set to [`errno.EXDEV`](errno.md#errno.EXDEV "errno.EXDEV").

此复制的完成没有额外的从内核到用户空间再回到内核的数据转移花费。另外，一些文件系统可能实现额外的优化。完成复制就如同打开两个二进制文件一样。

调用成功后，返回拼接到管道的字节数或从管道拼接出来的字节数。返回值为 0 意味着输入结束。如果 _src_ 指向一个管道，则意味着没有数据需要传输，而且由于没有写入程序连到管道的写入端，所以将不会阻塞。

[可用性](intro.md#availability): Linux >= 2.6.17 且 glibc >= 2.5

在 3.10 版本加入.

os.SPLICE_F_MOVE¶

os.SPLICE_F_NONBLOCK¶

os.SPLICE_F_MORE¶
~~~
    

~~~
在 3.10 版本加入.

os.readv( _fd_ , _buffers_ , _/_ )¶
~~~
    

~~~
从文件描述符 _fd_ 将数据读取至多个可变的 [字节类对象](../glossary.md#term-bytes-like-object) 缓冲区 _buffers_ 中。将数据依次存放到每个缓冲区中，填满一个后继续存放到序列中的下一个缓冲区，来保存其余数据。

返回实际读取的字节总数，该总数可以小于所有对象的总容量。

操作系统可能对允许使用的缓冲区数量有限制（使用 `sysconf()` 获取 `'SC_IOV_MAX'` 值）。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

os.tcgetpgrp( _fd_ , _/_ )¶
~~~
    

~~~
返回与 _fd_ 指定的终端相关联的进程组（ _fd_ 是由 `os.open()` 返回的已打开的文件描述符）。

[可用性](intro.md#availability): Unix, 非 WASI。

os.tcsetpgrp( _fd_ , _pg_ , _/_ )¶
~~~
    

~~~
设置与 _fd_ 指定的终端相关联的进程组为 _pg*（*fd_ 是由 `os.open()` 返回的已打开的文件描述符）。

[可用性](intro.md#availability): Unix, 非 WASI。

os.ttyname( _fd_ , _/_ )¶
~~~
    

~~~
返回一个字符串，该字符串表示与文件描述符 _fd_ 关联的终端。如果 _fd_ 没有与终端关联，则抛出异常。

[可用性](intro.md#availability): Unix。

os.write( _fd_ , _str_ , _/_ )¶
~~~
    

~~~
将 _str_ 中的字节串 (bytestring) 写入文件描述符 _fd_ 。

返回实际写入的字节数。

备注

该功能适用于低级 I/O 操作，必须用于 `os.open()` 或 `pipe()` 返回的文件描述符。若要写入由内建函数 [`open()`](functions.md#open "open")、`popen()`、`fdopen()`、[`sys.stdout`](sys.md#sys.stdout "sys.stdout") 或 [`sys.stderr`](sys.md#sys.stderr "sys.stderr") 返回的 "文件对象"，则应使用其相应的 `write()` 方法。

在 3.5 版本发生变更: 如果系统调用被中断，但信号处理程序没有触发异常，此函数现在会重试系统调用，而不是触发 [`InterruptedError`](exceptions.md#InterruptedError "InterruptedError") 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

os.writev( _fd_ , _buffers_ , _/_ )¶
~~~
    

~~~
将缓冲区 _buffers_ 的内容写入文件描述符 _fd_ 。缓冲区 _buffers_ 必须是由 [字节类对象](../glossary.md#term-bytes-like-object) 组成的序列。缓冲区以数组顺序处理。先写入第一个缓冲区的全部内容，再写入第二个缓冲区，照此继续。

返回实际写入的字节总数。

操作系统可能对允许使用的缓冲区数量有限制（使用 `sysconf()` 获取 `'SC_IOV_MAX'` 值）。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

### 查询终端的尺寸¶

在 3.3 版本加入.

os.get_terminal_size( _fd =STDOUT_FILENO_, _/_ )¶
~~~
    

~~~
返回终端窗口的尺寸，格式为 `(columns, lines)`，它是类型为 `terminal_size` 的元组。

可选参数 `fd` （默认为 `STDOUT_FILENO` 或标准输出）指定应查询的文件描述符。

如果文件描述符未连接到终端，则抛出 [`OSError`](exceptions.md#OSError "OSError") 异常。

[`shutil.get_terminal_size()`](shutil.md#shutil.get_terminal_size "shutil.get_terminal_size") 是供常规使用的高阶函数，`os.get_terminal_size` 是其底层的实现。

[可用性](intro.md#availability): Unix, Windows。

_class _os.terminal_size¶
~~~
    

~~~
元组的子类，存储终端窗口尺寸 `(columns, lines)`。

columns¶
~~~
    

~~~
终端窗口的宽度，单位为字符。

lines¶
~~~
    

~~~
终端窗口的高度，单位为字符。

### 文件描述符的继承¶

在 3.4 版本加入.

每个文件描述符都有一个 "inheritable"（可继承）标志位，该标志位控制了文件描述符是否可以由子进程继承。从 Python 3.4 开始，由 Python 创建的文件描述符默认是不可继承的。

在 UNIX 上，执行新程序时，不可继承的文件描述符在子进程中是关闭的，其他文件描述符将被继承。

在 Windows 上，不可继承的句柄和文件描述符在子进程中是关闭的，但标准流（文件描述符 0、1 和 2 即标准输入、标准输出和标准错误）是始终继承的。如果使用 `spawn*` 函数，所有可继承的句柄和文件描述符都将被继承。如果使用 [`subprocess`](subprocess.md#module-subprocess "subprocess: Subprocess management.") 模块，将关闭除标准流以外的所有文件描述符，并且仅当 _close_fds_ 参数为 `False` 时才继承可继承的句柄。

在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上，文件描述符无法被修改。

os.get_inheritable( _fd_ , _/_ )¶
~~~
    

~~~
获取指定文件描述符的“可继承”标志位（为布尔值）。

os.set_inheritable( _fd_ , _inheritable_ , _/_ )¶
~~~
    

~~~
设置指定文件描述符的“可继承”标志位。

os.get_handle_inheritable( _handle_ , _/_ )¶
~~~
    

~~~
获取指定句柄的“可继承”标志位（为布尔值）。

[可用性](intro.md#availability): Windows。

os.set_handle_inheritable( _handle_ , _inheritable_ , _/_ )¶
~~~
    

~~~
设置指定句柄的“可继承”标志位。

[可用性](intro.md#availability): Windows。

## 文件和目录¶

在某些 Unix 平台上，许多函数支持以下一项或多项功能：

  * **指定文件描述符为参数：** 通常在 `os` 模块中提供给函数的 _path_ 参数必须是表示文件路径的字符串，但是，某些函数现在可以接受其 _path_ 参数为打开文件描述符，该函数将对描述符指向的文件进行操作。（对于 POSIX 系统，Python 将调用以 `f` 开头的函数变体（如调用 `fchdir` 而不是 `chdir`）。）

可以用 `os.supports_fd` 检查某个函数在你的平台上是否支持将 _path_ 参数指定为文件描述符。如果不支持，使用该功能将抛出 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") 异常。

如果该函数还支持 _dir_fd_ 或 _follow_symlinks_ 参数，那么用文件描述符作为 _path_ 后就不能再指定上述参数了。

  * **基于目录描述符的相对路径：** 如果 _dir_fd_ 不是 `None`，它就应该是一个指向目录的文件描述符，这时待操作的 path 应该是相对路径，相对路径是相对于前述目录的。如果 path 是绝对路径，则 _dir_fd_ 将被忽略。（对于 POSIX 系统，Python 将调用该函数的变体，变体以 `at` 结尾，可能以 `f` 开头（如调用 `faccessat` 而不是 `access`）。

可以用 `os.supports_dir_fd` 检查某个函数在你的平台上是否支持 _dir_fd_ 。如果不支持，使用该功能将抛出 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") 异常。

  * **不跟踪符号链接：** 如果 _follow_symlinks_ 为 `False`，并且待操作路径的最后一个元素是符号链接，则该函数将在符号链接本身而不是链接所指向的文件上操作。（对于 POSIX 系统，Python 将调用该函数的 `l...` 变体。）

可以用 `os.supports_follow_symlinks` 检查某个函数在你的平台上是否支持 _follow_symlinks_ 。如果不支持，使用该功能将抛出 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") 异常。

os.access( _path_ , _mode_ , _*_ , _dir_fd =None_, _effective_ids =False_, _follow_symlinks =True_)¶
~~~
    

~~~
使用 实际用户ID/用户组ID 测试对 _path_ 的访问。请注意，大多数测试操作将使用 有效用户ID/用户组ID，因此可以在 suid/sgid 环境中运用此例程，来测试调用用户是否具有对 _path_ 的指定访问权限。 _mode_ 为 `F_OK` 时用于测试 _path_ 是否存在，也可以对 `R_OK`、`W_OK` 和 `X_OK` 中的一个或多个进行“或”运算来测试指定权限。允许访问则返回 [`True`](constants.md#True "True")，否则返回 [`False`](constants.md#False "False")。更多信息请参见 Unix 手册页 _[access(2)](https://manpages.debian.org/access\(2\))_ 。

本函数支持指定 基于目录描述符的相对路径 和 不跟踪符号链接。

如果 _effective_ids_ 为 `True`，`access()` 将使用 有效用户ID/用户组ID 而非 实际用户ID/用户组ID 进行访问检查。您的平台可能不支持 _effective_ids_ ，您可以使用 `os.supports_effective_ids` 检查它是否可用。如果不可用，使用它时会抛出 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") 异常。

备注

使用 `access()` 来检查用户是否具有某项权限（如打开文件的权限），然后再使用 [`open()`](functions.md#open "open") 打开文件，这样做存在一个安全漏洞，因为用户可能会在检查和打开文件之间的时间里做其他操作。推荐使用 [EAFP](../glossary.md#term-EAFP) 技术。如:
~~~
    
    
~~~
if os.access("myfile", os.R_OK):
    with open("myfile") as fp:
        return fp.read()
return "some default data"
~~~

最好写成:

    
    
~~~
try:
    fp = open("myfile")
except PermissionError:
    return "some default data"
else:
    with fp:
        return fp.read()
~~~

备注

即使 `access()` 指示 I/O 操作会成功，但实际操作仍可能失败，尤其是对网络文件系统的操作，其权限语义可能超出常规的 POSIX 权限位模型。

在 3.3 版本发生变更: 添加 _dir_fd_ 、 _effective_ids_ 和 _follow_symlinks_ 参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.F_OK¶

os.R_OK¶

os.W_OK¶

os.X_OK¶

    

~~~
作为 `access()` 的 _mode_ 参数的可选值，分别测试 _path_ 的存在性、可读性、可写性和可执行性。

os.chdir( _path_ )¶
~~~
    

~~~
将当前工作目录更改为 _path_ 。

本函数支持 指定文件描述符为参数。其中，描述符必须指向打开的目录，不能是打开的文件。

本函数可以抛出 [`OSError`](exceptions.md#OSError "OSError") 及其子类的异常，如 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError")、[`PermissionError`](exceptions.md#PermissionError "PermissionError") 和 [`NotADirectoryError`](exceptions.md#NotADirectoryError "NotADirectoryError") 异常。

引发一个 [审计事件](sys.md#auditing) `os.chdir`，附带参数 `path`。

在 3.3 版本加入: 在某些平台上新增支持将 _path_ 参数指定为文件描述符。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.chflags( _path_ , _flags_ , _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
将 _path_ 的 flags 设置为其他由数字表示的 _flags_ 。 _flags_ 可以用以下值按位或组合起来（以下值在 [`stat`](stat.md#module-stat "stat: Utilities for interpreting the results of os.stat\(\), os.lstat\(\) and os.fstat\(\).") 模块中定义）：

  * [`stat.UF_NODUMP`](stat.md#stat.UF_NODUMP "stat.UF_NODUMP")

  * [`stat.UF_IMMUTABLE`](stat.md#stat.UF_IMMUTABLE "stat.UF_IMMUTABLE")

  * [`stat.UF_APPEND`](stat.md#stat.UF_APPEND "stat.UF_APPEND")

  * [`stat.UF_OPAQUE`](stat.md#stat.UF_OPAQUE "stat.UF_OPAQUE")

  * [`stat.UF_NOUNLINK`](stat.md#stat.UF_NOUNLINK "stat.UF_NOUNLINK")

  * [`stat.UF_COMPRESSED`](stat.md#stat.UF_COMPRESSED "stat.UF_COMPRESSED")

  * [`stat.UF_HIDDEN`](stat.md#stat.UF_HIDDEN "stat.UF_HIDDEN")

  * [`stat.SF_ARCHIVED`](stat.md#stat.SF_ARCHIVED "stat.SF_ARCHIVED")

  * [`stat.SF_IMMUTABLE`](stat.md#stat.SF_IMMUTABLE "stat.SF_IMMUTABLE")

  * [`stat.SF_APPEND`](stat.md#stat.SF_APPEND "stat.SF_APPEND")

  * [`stat.SF_NOUNLINK`](stat.md#stat.SF_NOUNLINK "stat.SF_NOUNLINK")

  * [`stat.SF_SNAPSHOT`](stat.md#stat.SF_SNAPSHOT "stat.SF_SNAPSHOT")

本函数支持 不跟踪符号链接。

引发一个 [审计事件](sys.md#auditing) `os.chflags`，附带参数 `path`、`flags`。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入: _follow_symlinks_ 参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.chmod( _path_ , _mode_ , _*_ , _dir_fd =None_, _follow_symlinks =True_)¶
~~~
    

~~~
将 _path_ 的 mode 更改为其他由数字表示的 _mode_ 。 _mode_ 可以用以下值之一，也可以将它们按位或组合起来（以下值在 [`stat`](stat.md#module-stat "stat: Utilities for interpreting the results of os.stat\(\), os.lstat\(\) and os.fstat\(\).") 模块中定义）：

  * [`stat.S_ISUID`](stat.md#stat.S_ISUID "stat.S_ISUID")

  * [`stat.S_ISGID`](stat.md#stat.S_ISGID "stat.S_ISGID")

  * [`stat.S_ENFMT`](stat.md#stat.S_ENFMT "stat.S_ENFMT")

  * [`stat.S_ISVTX`](stat.md#stat.S_ISVTX "stat.S_ISVTX")

  * [`stat.S_IREAD`](stat.md#stat.S_IREAD "stat.S_IREAD")

  * [`stat.S_IWRITE`](stat.md#stat.S_IWRITE "stat.S_IWRITE")

  * [`stat.S_IEXEC`](stat.md#stat.S_IEXEC "stat.S_IEXEC")

  * [`stat.S_IRWXU`](stat.md#stat.S_IRWXU "stat.S_IRWXU")

  * [`stat.S_IRUSR`](stat.md#stat.S_IRUSR "stat.S_IRUSR")

  * [`stat.S_IWUSR`](stat.md#stat.S_IWUSR "stat.S_IWUSR")

  * [`stat.S_IXUSR`](stat.md#stat.S_IXUSR "stat.S_IXUSR")

  * [`stat.S_IRWXG`](stat.md#stat.S_IRWXG "stat.S_IRWXG")

  * [`stat.S_IRGRP`](stat.md#stat.S_IRGRP "stat.S_IRGRP")

  * [`stat.S_IWGRP`](stat.md#stat.S_IWGRP "stat.S_IWGRP")

  * [`stat.S_IXGRP`](stat.md#stat.S_IXGRP "stat.S_IXGRP")

  * [`stat.S_IRWXO`](stat.md#stat.S_IRWXO "stat.S_IRWXO")

  * [`stat.S_IROTH`](stat.md#stat.S_IROTH "stat.S_IROTH")

  * [`stat.S_IWOTH`](stat.md#stat.S_IWOTH "stat.S_IWOTH")

  * [`stat.S_IXOTH`](stat.md#stat.S_IXOTH "stat.S_IXOTH")

本函数支持 指定文件描述符、指定基于目录描述符的相对路径 和 不跟踪符号链接。

备注

尽管 Windows 支持 `chmod()`，但只能用它设置文件的只读标志（`stat.S_IWRITE` 和 `stat.S_IREAD` 常量或对应的整数值）。所有其他标志位都会被忽略。

该函数 Emscripten 和 WASI 将受到限制，请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

引发一个 [审计事件](sys.md#auditing) `os.chmod`，附带参数 `path`、`mode`、`dir_fd`。

在 3.3 版本加入: 添加了指定 _path_ 为文件描述符的支持，以及 _dir_fd_ 和 _follow_symlinks_ 参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.chown( _path_ , _uid_ , _gid_ , _*_ , _dir_fd =None_, _follow_symlinks =True_)¶
~~~
    

~~~
将 _path_ 的用户和组 ID 分别修改为数字形式的 _uid_ 和 _gid_ 。若要使其中某个 ID 保持不变，请将其置为 -1。

本函数支持 指定文件描述符、指定基于目录描述符的相对路径 和 不跟踪符号链接。

参见更高阶的函数 [`shutil.chown()`](shutil.md#shutil.chown "shutil.chown")，除了数字 ID 之外，它也接受名称。

引发一个 [审计事件](sys.md#auditing) `os.chown`，附带参数 `path`、`uid`、`gid`、`dir_fd`。

[可用性](intro.md#availability): Unix。

该函数 Emscripten 和 WASI 将受到限制，请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

在 3.3 版本加入: 添加了指定 _path_ 为文件描述符的支持，以及 _dir_fd_ 和 _follow_symlinks_ 参数。

在 3.6 版本发生变更: 支持 [类路径对象](../glossary.md#term-path-like-object)。

os.chroot( _path_ )¶
~~~
    

~~~
将当前进程的根目录更改为 _path_ 。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.fchdir( _fd_ )¶
~~~
    

~~~
将当前工作目录更改为文件描述符 _fd_ 指向的目录。fd 必须指向打开的目录而非文件。从 Python 3.3 开始，它等效于 `os.chdir(fd)`。

引发一个 [审计事件](sys.md#auditing) `os.chdir`，附带参数 `path`。

[可用性](intro.md#availability): Unix。

os.getcwd()¶
~~~
    

~~~
返回表示当前工作目录的字符串。

os.getcwdb()¶
~~~
    

~~~
返回表示当前工作目录的字节串 (bytestring)。

在 3.8 版本发生变更: 在 Windows 上，本函数现在会使用 UTF-8 编码格式而不是 ANSI 代码页：请参看 [**PEP 529**](https://peps.python.org/pep-0529/) 了解具体原因。 该函数在 Windows 上不再被弃用。

os.lchflags( _path_ , _flags_ )¶
~~~
    

~~~
将 _path_ 的 flags 设置为其他由数字表示的 _flags_ ，与 `chflags()` 类似，但不跟踪符号链接。从 Python 3.3 开始，它等效于 `os.chflags(path, flags, follow_symlinks=False)`。

引发一个 [审计事件](sys.md#auditing) `os.chflags`，附带参数 `path`、`flags`。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.lchmod( _path_ , _mode_ )¶
~~~
    

~~~
将 _path_ 的权限状态修改为 _mode_ 。如果 path 是符号链接，则影响符号链接本身而非链接目标。可以参考 `chmod()` 中列出 _mode_ 的可用值。从 Python 3.3 开始，它等效于 `os.chmod(path, mode, follow_symlinks=False)`。

引发一个 [审计事件](sys.md#auditing) `os.chmod`，附带参数 `path`、`mode`、`dir_fd`。

[可用性](intro.md#availability): Unix。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.lchown( _path_ , _uid_ , _gid_ )¶
~~~
    

~~~
将 _path_ 的用户和组 ID 分别修改为数字形式的 _uid_ 和 _gid_ ，本函数不跟踪符号链接。从 Python 3.3 开始，它等效于 `os.chown(path, uid, gid, follow_symlinks=False)`。

引发一个 [审计事件](sys.md#auditing) `os.chown`，附带参数 `path`、`uid`、`gid`、`dir_fd`。

[可用性](intro.md#availability): Unix。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.link( _src_ , _dst_ , _*_ , _src_dir_fd =None_, _dst_dir_fd =None_, _follow_symlinks =True_)¶
~~~
    

~~~
创建一个指向 _src_ 的硬链接，名为 _dst_ 。

本函数支持将 _src_dir_fd_ 和 _dst_dir_fd_ 中的一个或两个指定为 基于目录描述符的相对路径，支持 不跟踪符号链接。

引发一个 [审计事件](sys.md#auditing) `os.link` 附带参数 `src`、`dst`、`src_dir_fd`、`dst_dir_fd`。

[可用性](intro.md#availability): Unix, Windows, 非 Emscripten。

在 3.2 版本发生变更: 添加了对 Windows 的支持。

在 3.3 版本加入: 添加 _src_dir_fd_ 、 _dst_dir_fd_ 和 _follow_symlinks_ 参数。

在 3.6 版本发生变更: 接受一个 [类路径对象](../glossary.md#term-path-like-object) 作为 _src_ 和 _dst_ 。

os.listdir( _path ='.'_)¶
~~~
    

~~~
返回一个包含由 _path_ 指定目录中条目名称组成的列表。 该列表按任意顺序排列，并且不包括特殊条目 `'.'` 和 `'..'`，即使它们存在于目录中。 如果有文件在调用此函数期间在被移除或添加到目录中，是否要包括该文件的名称并没有规定。

_path_ 可以是 [类路径对象](../glossary.md#term-path-like-object)。如果 _path_ 是（直接传入或通过 `PathLike` 接口间接传入） `bytes` 类型，则返回的文件名也将是 `bytes` 类型，其他情况下是 `str` 类型。

本函数也支持 指定文件描述符为参数，其中描述符必须指向目录。

引发一个 [审计事件](sys.md#auditing) `os.listdir`，附带参数 `path`。

备注

要将 `str` 类型的文件名编码为 `bytes`，请使用 `fsencode()`。

参见

`scandir()` 函数返回目录内文件名的同时，也返回文件属性信息，它在某些具体情况下能提供更好的性能。

在 3.2 版本发生变更: _path_ 变为可选参数。

在 3.3 版本加入: 新增支持将 _path_ 参数指定为打开的文件描述符。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.listdrives()¶
~~~
    

~~~
返回一个包括Windows系统上驱动名称的列表。

A drive name typically looks like `'C:\\'`. Not every drive name will be associated with a volume, and some may be inaccessible for a variety of reasons, including permissions, network connectivity or missing media. This function does not test for access.

May raise [`OSError`](exceptions.md#OSError "OSError") if an error occurs collecting the drive names.

Raises an [auditing event](sys.md#auditing) `os.listdrives` with no arguments.

[Availability](intro.md#availability): Windows

在 3.12 版本加入.

os.listmounts( _volume_ )¶
~~~
    

~~~
Return a list containing the mount points for a volume on a Windows system.

_volume_ must be represented as a GUID path, like those returned by `os.listvolumes()`. Volumes may be mounted in multiple locations or not at all. In the latter case, the list will be empty. Mount points that are not associated with a volume will not be returned by this function.

The mount points return by this function will be absolute paths, and may be longer than the drive name.

Raises [`OSError`](exceptions.md#OSError "OSError") if the volume is not recognized or if an error occurs collecting the paths.

Raises an [auditing event](sys.md#auditing) `os.listmounts` with argument `volume`.

[Availability](intro.md#availability): Windows

在 3.12 版本加入.

os.listvolumes()¶
~~~
    

~~~
Return a list containing the volumes in the system.

Volumes are typically represented as a GUID path that looks like `\\?\Volume{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}\`. Files can usually be accessed through a GUID path, permissions allowing. However, users are generally not familiar with them, and so the recommended use of this function is to retrieve mount points using `os.listmounts()`.

如果在收集卷时发生错误则可能引发 [`OSError`](exceptions.md#OSError "OSError")。

引发一个 [审计事件](sys.md#auditing) `os.listvolumes`，不附带任何参数。

[Availability](intro.md#availability): Windows

在 3.12 版本加入.

os.lstat( _path_ , _*_ , _dir_fd =None_)¶
~~~
    

~~~
Perform the equivalent of an `lstat()` system call on the given path. Similar to `stat()`, but does not follow symbolic links. Return a `stat_result` object.

在不支持符号链接的平台上，本函数是 `stat()` 的别名。

从 Python 3.3 起，此功能等价于 `os.stat(path, dir_fd=dir_fd, follow_symlinks=False)`。

本函数支持 基于目录描述符的相对路径。

参见

`stat()` 函数。

在 3.2 版本发生变更: 添加对 Windows 6.0 (Vista) 符号链接的支持。

在 3.3 版本发生变更: 添加了 _dir_fd_ 参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.8 版本发生变更: 目前在 Windows 上，遇到表示另一个路径的重解析点（即名称代理，包括符号链接和目录结点），本函数将打开它。其他种类的重解析点由 `stat()` 交由操作系统解析。

os.mkdir( _path_ , _mode =0o777_, _*_ , _dir_fd =None_)¶
~~~
    

~~~
创建一个名为 _path_ 的目录，应用以数字表示的权限模式 _mode_ 。

如果目录已经存在， [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError") 会被提出。如果路径中的父目录不存在，则会引发 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError") 。

某些系统会忽略 _mode_ 。如果没有忽略它，那么将首先从它中减去当前的 umask 值。如果除最后 9 位（即 _mode_ 八进制的最后 3 位）之外，还设置了其他位，则其他位的含义取决于各个平台。在某些平台上，它们会被忽略，应显式调用 `chmod()` 进行设置。

本函数支持 基于目录描述符的相对路径。

如果需要创建临时目录，请参阅 [`tempfile`](tempfile.md#module-tempfile "tempfile: Generate temporary files and directories.") 模块中的 [`tempfile.mkdtemp()`](tempfile.md#tempfile.mkdtemp "tempfile.mkdtemp") 函数。

引发一个 [审计事件](sys.md#auditing) `os.mkdir`，附带参数 `path`、`mode`、`dir_fd`。

在 3.3 版本加入: _dir_fd_ 参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.makedirs( _name_ , _mode =0o777_, _exist_ok =False_)¶
~~~
    

~~~
递归目录创建函数。与 `mkdir()` 类似，但会自动创建到达最后一级目录所需要的中间目录。

_mode_ 形参会被传递给 `mkdir()` 用来创建最后一级目录；请参阅 mkdir() 的说明 了解其解读方式。 要设置任何新建父目录的权限你可以在发起调用 `makedirs()` 之前设置掩码。 现有父目录的文件权限不会被更改。

如果 _exist_ok_ 为 `False` (默认值)，则如果目标目录已存在将会引发 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError")。

备注

如果要创建的路径元素包含 `pardir` (如 UNIX 系统中的 "..") `makedirs()` 将无法明确目标。

本函数能正确处理 UNC 路径。

引发一个 [审计事件](sys.md#auditing) `os.mkdir`，附带参数 `path`、`mode`、`dir_fd`。

在 3.2 版本加入: _exist_ok_ 参数。

在 3.4.1 版本发生变更: 在 Python 3.4.1 以前，如果 _exist_ok_ 为 `True`，且目录已存在，且 _mode_ 与现有目录的权限不匹配，`makedirs()` 仍会抛出错误。由于无法安全地实现此行为，因此在 Python 3.4.1 中将该行为删除。请参阅 [bpo-21082](https://bugs.python.org/issue?@action=redirect&bpo=21082)。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.7 版本发生变更: _mode_ 参数不会再影响新创建的中间目录的文件权限位。

os.mkfifo( _path_ , _mode =0o666_, _*_ , _dir_fd =None_)¶
~~~
    

~~~
创建一个名为 _path_ 的 FIFO（命名管道，一种先进先出队列），具有以数字表示的权限状态 _mode_ 。将从 mode 中首先减去当前的 umask 值。

本函数支持 基于目录描述符的相对路径。

FIFO 是可以像常规文件一样访问的管道。FIFO 如果没有被删除（如使用 `os.unlink()`），会一直存在。通常，FIFO 用作“客户端”和“服务器”进程之间的汇合点：服务器打开 FIFO 进行读取，而客户端打开 FIFO 进行写入。请注意，`mkfifo()` 不会打开 FIFO --- 它只是创建汇合点。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入: _dir_fd_ 参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.mknod( _path_ , _mode =0o600_, _device =0_, _*_ , _dir_fd =None_)¶
~~~
    

~~~
创建一个名为 _path_ 的文件系统节点（文件，设备专用文件或命名管道）。 _mode_ 指定权限和节点类型，方法是将权限与下列节点类型 `stat.S_IFREG`、`stat.S_IFCHR`、`stat.S_IFBLK` 和 `stat.S_IFIFO` 之一（按位或）组合（这些常量可以在 [`stat`](stat.md#module-stat "stat: Utilities for interpreting the results of os.stat\(\), os.lstat\(\) and os.fstat\(\).") 模块中找到）。对于 `stat.S_IFCHR` 和 `stat.S_IFBLK`， _device_ 参数指定了新创建的设备专用文件（可能会用到 `os.makedev()`），否则该参数将被忽略。

本函数支持 基于目录描述符的相对路径。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入: _dir_fd_ 参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.major( _device_ , _/_ )¶
~~~
    

~~~
Extract the device major number from a raw device number (usually the `st_dev` or `st_rdev` field from `stat`).

os.minor( _device_ , _/_ )¶
~~~
    

~~~
Extract the device minor number from a raw device number (usually the `st_dev` or `st_rdev` field from `stat`).

os.makedev( _major_ , _minor_ , _/_ )¶
~~~
    

~~~
将主设备号和次设备号组合成原始设备号。

os.pathconf( _path_ , _name_ )¶
~~~
    

~~~
返回所给名称的文件有关的系统配置信息。 _name_ 指定要查找的配置名称，它可以是字符串，是一个系统已定义的名称，这些名称定义在不同标准（POSIX.1，Unix 95，Unix 98 等）中。一些平台还定义了额外的其他名称。当前操作系统已定义的名称在 `pathconf_names` 字典中给出。对于未包含在该映射中的配置名称，也可以传递一个整数作为 _name_ 。

如果 _name_ 是一个字符串且不是已定义的名称，将抛出 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。如果当前系统不支持 _name_ 指定的配置名称，即使该名称存在于 `pathconf_names`，也会抛出 [`OSError`](exceptions.md#OSError "OSError") 异常，错误码为 [`errno.EINVAL`](errno.md#errno.EINVAL "errno.EINVAL")。

本函数支持 指定文件描述符为参数。

[可用性](intro.md#availability): Unix。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.pathconf_names¶
~~~
    

~~~
字典，表示映射关系，为 `pathconf()` 和 `fpathconf()` 可接受名称与操作系统为这些名称定义的整数值之间的映射。这可用于判断系统已定义了哪些名称。

[可用性](intro.md#availability): Unix。

os.readlink( _path_ , _*_ , _dir_fd =None_)¶
~~~
    

~~~
返回一个字符串，为符号链接指向的实际路径。其结果可以是绝对或相对路径。如果是相对路径，则可用 `os.path.join(os.path.dirname(path), result)` 转换为绝对路径。

如果 _path_ 是字符串对象（直接传入或通过 `PathLike` 接口间接传入），则结果也将是字符串对象，且此类调用可能会引发 UnicodeDecodeError。如果 _path_ 是字节对象（直接传入或间接传入），则结果将会是字节对象。

本函数支持 基于目录描述符的相对路径。

当尝试解析的路径可能含有链接时，请改用 [`realpath()`](os.path.md#os.path.realpath "os.path.realpath") 以正确处理递归和平台差异。

[可用性](intro.md#availability): Unix, Windows。

在 3.2 版本发生变更: 添加对 Windows 6.0 (Vista) 符号链接的支持。

在 3.3 版本加入: _dir_fd_ 参数。

在 3.6 版本发生变更: 在 Unix 上可以接受一个 [类路径对象](../glossary.md#term-path-like-object)。

在 3.8 版本发生变更: 在 Windows 上接受 [类路径对象](../glossary.md#term-path-like-object) 和字节对象。

在 3.8 版本发生变更: 增加了对目录链接的支持，且返回值改为了“替换路径”的形式（通常带有 `\\?\` 前缀），而不是先前那样返回可选的 "print name" 字段。

os.remove( _path_ , _*_ , _dir_fd =None_)¶
~~~
    

~~~
移除（删除）文件 _path_ 。 如果 _path_ 是目录，则会引发 [`OSError`](exceptions.md#OSError "OSError")。 请使用 `rmdir()` 来移除目录。 如果文件不存在，则会引发 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError")。

本函数支持 基于目录描述符的相对路径。

在 Windows 上，尝试删除正在使用的文件会抛出异常。而在 Unix 上，虽然该文件的条目会被删除，但分配给文件的存储空间仍然不可用，直到原始文件不再使用为止。

本函数在语义上与 `unlink()` 相同。

引发一个 [审计事件](sys.md#auditing) `os.remove`，附带参数 `path`、`dir_fd`。

在 3.3 版本加入: _dir_fd_ 参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.removedirs( _name_ )¶
~~~
    

~~~
递归删除目录。工作方式类似于 `rmdir()`，不同之处在于，如果成功删除了末尾一级目录，`removedirs()` 会尝试依次删除 _path_ 中提到的每个父目录，直到抛出错误为止（但该错误会被忽略，因为这通常表示父目录不是空目录）。例如，`os.removedirs('foo/bar/baz')` 将首先删除目录 `'foo/bar/baz'`，然后如果 `'foo/bar'` 和 `'foo'` 为空，则继续删除它们。如果无法成功删除末尾一级目录，则抛出 [`OSError`](exceptions.md#OSError "OSError") 异常。

引发一个 [审计事件](sys.md#auditing) `os.remove`，附带参数 `path`、`dir_fd`。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.rename( _src_ , _dst_ , _*_ , _src_dir_fd =None_, _dst_dir_fd =None_)¶
~~~
    

~~~
将文件或目录 _src_ 重命名为 _dst_ 。如果 _dst_ 已存在，则下列情况下将会操作失败，并抛出 [`OSError`](exceptions.md#OSError "OSError") 的子类：

在 Windows 上，如果 _dst_ 存在则总是会引发 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError")。 如果 _src_ 和 _dst_ 是在不同的文件系统上则此操作可能会失败。 请使用 [`shutil.move()`](shutil.md#shutil.move "shutil.move") 以支持移动到不同的文件系统。

在 Unix 上，如果 _src_ 是文件而 _dst_ 是目录，将抛出 [`IsADirectoryError`](exceptions.md#IsADirectoryError "IsADirectoryError") 异常，反之则抛出 [`NotADirectoryError`](exceptions.md#NotADirectoryError "NotADirectoryError") 异常。如果两者都是目录且 _dst_ 为空，则 _dst_ 将被静默替换。如果 _dst_ 是非空目录，则抛出 [`OSError`](exceptions.md#OSError "OSError") 异常。如果两者都是文件，则在用户具有权限的情况下，将对 _dst_ 进行静默替换。如果 _src_ 和 _dst_ 在不同的文件系统上，则本操作在某些 Unix 分支上可能会失败。如果成功，重命名操作将是一个原子操作（这是 POSIX 的要求）。

本函数支持将 _src_dir_fd_ 和 _dst_dir_fd_ 中的一个或两个指定为 基于目录描述符的相对路径。

如果需要在不同平台上都能替换目标，请使用 `replace()`。

引发一个 [审计事件](sys.md#auditing) `os.rename` 附带参数 `src`、`dst`、`src_dir_fd`、`dst_dir_fd`。

在 3.3 版本加入: _src_dir_fd_ 和 _dst_dir_fd_ 参数。

在 3.6 版本发生变更: 接受一个 [类路径对象](../glossary.md#term-path-like-object) 作为 _src_ 和 _dst_ 。

os.renames( _old_ , _new_ )¶
~~~
    

~~~
递归重命名目录或文件。工作方式类似 `rename()`，除了会首先创建新路径所需的中间目录。重命名后，将调用 `removedirs()` 删除旧路径中不需要的目录。

备注

如果用户没有权限删除末级的目录或文件，则本函数可能会无法建立新的目录结构。

引发一个 [审计事件](sys.md#auditing) `os.rename` 附带参数 `src`、`dst`、`src_dir_fd`、`dst_dir_fd`。

在 3.6 版本发生变更: 接受一个 [类路径对象](../glossary.md#term-path-like-object) 作为 _old_ 和 _new_ 。

os.replace( _src_ , _dst_ , _*_ , _src_dir_fd =None_, _dst_dir_fd =None_)¶
~~~
    

~~~
将文件或目录 _src_ 重命名为 _dst_ 。如果 _dst_ 是非空目录，将抛出 [`OSError`](exceptions.md#OSError "OSError") 异常。如果 _dst_ 已存在且为文件，则在用户具有权限的情况下，将对其进行静默替换。如果 _src_ 和 _dst_ 在不同的文件系统上，本操作可能会失败。如果成功，重命名操作将是一个原子操作（这是 POSIX 的要求）。

本函数支持将 _src_dir_fd_ 和 _dst_dir_fd_ 中的一个或两个指定为 基于目录描述符的相对路径。

引发一个 [审计事件](sys.md#auditing) `os.rename` 附带参数 `src`、`dst`、`src_dir_fd`、`dst_dir_fd`。

在 3.3 版本加入.

在 3.6 版本发生变更: 接受一个 [类路径对象](../glossary.md#term-path-like-object) 作为 _src_ 和 _dst_ 。

os.rmdir( _path_ , _*_ , _dir_fd =None_)¶
~~~
    

~~~
移除（删除）目录 _path_ 。 如果目录不存在或不为空，则会分别引发 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError") 或 [`OSError`](exceptions.md#OSError "OSError")。 要移除整个目录树，可以使用 [`shutil.rmtree()`](shutil.md#shutil.rmtree "shutil.rmtree")。

本函数支持 基于目录描述符的相对路径。

引发一个 [审计事件](sys.md#auditing) `os.rmdir`，附带参数 `path`、`dir_fd`。

在 3.3 版本加入: _dir_fd_ 参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.scandir( _path ='.'_)¶
~~~
    

~~~
返回一个 `os.DirEntry` 对象的迭代器，它们对应于由 _path_ 指定目录中的条目。 这些条目会以任意顺序生成，并且不包括特殊条目 `'.'` 和 `'..'`。 如果有文件在迭代器创建之后在目录中被移除或添加，是否要包括该文件对应的条目并没有规定。

如果需要文件类型或文件属性信息，使用 `scandir()` 代替 `listdir()` 可以大大提高这部分代码的性能，因为如果操作系统在扫描目录时返回的是 `os.DirEntry` 对象，则该对象包含了这些信息。所有 `os.DirEntry` 的方法都可能执行一次系统调用，但是 `is_dir()` 和 `is_file()` 通常只在有符号链接时才执行一次系统调用。`os.DirEntry.stat()` 在 Unix 上始终需要一次系统调用，而在 Windows 上只在有符号链接时才需要。

_path_ 可以是 [类路径对象](../glossary.md#term-path-like-object)。如果 _path_ 是（直接传入或通过 `PathLike` 接口间接传入的） `bytes` 类型，那么每个 `os.DirEntry` 的 `name` 和 `path` 属性将是 `bytes` 类型，其他情况下是 `str` 类型。

本函数也支持 指定文件描述符为参数，其中描述符必须指向目录。

引发一个 [审计事件](sys.md#auditing) `os.scandir`，附带参数 `path`。

`scandir()` 迭代器支持 [上下文管理](../glossary.md#term-context-manager) 协议，并具有以下方法：

scandir.close()¶
~~~
    

~~~
关闭迭代器并释放占用的资源。

当迭代器迭代完毕，或垃圾回收，或迭代过程出错时，将自动调用本方法。但仍建议显式调用它或使用 [`with`](../reference/compound_stmts.md#with) 语句。

在 3.6 版本加入.

下面的例子演示了 `scandir()` 的简单用法，用来显示给定 _path_ 中所有不以 `'.'` 开头的文件（不包括目录）。`entry.is_file()` 通常不会增加一次额外的系统调用:
~~~
    
    
~~~
with os.scandir(path) as it:
    for entry in it:
        if not entry.name.startswith('.') and entry.is_file():
            print(entry.name)
~~~

备注

在基于 Unix 的系统上，`scandir()` 使用系统的 [opendir()](https://pubs.opengroup.org/onlinepubs/009695399/functions/opendir.md) 和 [readdir()](https://pubs.opengroup.org/onlinepubs/009695399/functions/readdir_r.md) 函数。 在 Windows 上，它使用 Win32 [FindFirstFileW](https://msdn.microsoft.com/en-us/library/windows/desktop/aa364418\(v=vs.85\).aspx) 和 [FindNextFileW](https://msdn.microsoft.com/en-us/library/windows/desktop/aa364428\(v=vs.85\).aspx) 函数。

在 3.5 版本加入.

在 3.6 版本加入: 添加了对 [上下文管理](../glossary.md#term-context-manager) 协议和 `close()` 方法的支持。如果 `scandir()` 迭代器没有迭代完毕且没有显式关闭，其析构函数将发出 [`ResourceWarning`](3.标准库/exceptions.md#ResourceWarning "ResourceWarning") 警告。

本函数接受一个 [类路径对象](../glossary.md#term-path-like-object)。

在 3.7 版本发生变更: 在 Unix 上新增支持 指定文件描述符为参数。

_class _os.DirEntry¶

    

~~~
由 `scandir()` 生成的对象，用于显示目录内某个条目的文件路径和其他文件属性。

`scandir()` 将在不进行额外系统调用的情况下，提供尽可能多的此类信息。每次进行 `stat()` 或 `lstat()` 系统调用时，`os.DirEntry` 对象会将结果缓存下来。

`os.DirEntry` 实例不适合存储在长期存在的数据结构中，如果你知道文件元数据已更改，或者自调用 `scandir()` 以来已经经过了很长时间，请调用 `os.stat(entry.path)` 来获取最新信息。

因为 `os.DirEntry` 方法可以进行系统调用，所以它也可能抛出 [`OSError`](exceptions.md#OSError "OSError") 异常。如需精确定位错误，可以逐个调用 `os.DirEntry` 中的方法来捕获 [`OSError`](exceptions.md#OSError "OSError")，并适当处理。

为了能直接用作 [类路径对象](../glossary.md#term-path-like-object)，`os.DirEntry` 实现了 `PathLike` 接口。

`os.DirEntry` 实例所包含的属性和方法如下：

name¶
~~~
    

~~~
本条目的基本文件名，是根据 `scandir()` 的 _path_ 参数得出的相对路径。

如果 `scandir()` 的 _path_ 参数是 `bytes` 类型，则 `name` 属性也是 `bytes` 类型，否则为 `str`。使用 `fsdecode()` 解码 byte 类型的文件名。

path¶
~~~
    

~~~
本条目的完整路径：等效于 `os.path.join(scandir_path, entry.name)`，其中 _scandir_path_ 就是 `scandir()` 的 _path_ 参数。仅当 `scandir()` 的 _path_ 参数为绝对路径时，本路径才是绝对路径。如果 `scandir()` 的 _path_ 参数是 文件描述符，则 [`path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 属性与上述 `name` 属性相同。

如果 `scandir()` 的 _path_ 参数是 `bytes` 类型，则 [`path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 属性也是 `bytes` 类型，否则为 `str`。使用 `fsdecode()` 解码 byte 类型的文件名。

inode()¶
~~~
    

~~~
返回本条目的索引节点号 (inode number)。

这一结果是缓存在 `os.DirEntry` 对象中的，请调用 `os.stat(entry.path, follow_symlinks=False).st_ino` 来获取最新信息。

一开始没有缓存时，在 Windows 上需要一次系统调用，但在 Unix 上不需要。

is_dir( _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
如果本条目是目录，或是指向目录的符号链接，则返回 `True`。如果本条目是文件，或指向任何其他类型的文件，或该目录不再存在，则返回 `False`。

如果 _follow_symlinks_ 是 `False`，那么仅当本条目为目录时返回 `True` （不跟踪符号链接），如果本条目是任何类型的文件，或该文件不再存在，则返回 `False`。

这一结果是缓存在 `os.DirEntry` 对象中的，且 _follow_symlinks_ 为 `True` 和 `False` 时的缓存是分开的。请调用 `os.stat()` 和 [`stat.S_ISDIR()`](stat.md#stat.S_ISDIR "stat.S_ISDIR") 来获取最新信息。

一开始没有缓存时，大多数情况下不需要系统调用。特别是对于非符号链接，Windows 和 Unix 都不需要系统调用，除非某些 Unix 文件系统（如网络文件系统）返回了 `dirent.d_type == DT_UNKNOWN`。如果本条目是符号链接，则需要一次系统调用来跟踪它（除非 _follow_symlinks_ 为 `False`）。

本方法可能抛出 [`OSError`](exceptions.md#OSError "OSError") 异常，如 [`PermissionError`](exceptions.md#PermissionError "PermissionError") 异常，但 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError") 异常会被内部捕获且不会抛出。

is_file( _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
如果本条目是文件，或是指向文件的符号链接，则返回 `True`。如果本条目是目录，或指向目录，或指向其他非文件条目，或该文件不再存在，则返回 `False`。

如果 _follow_symlinks_ 是 `False`，那么仅当本条目为文件时返回 `True` （不跟踪符号链接），如果本条目是目录或其他非文件条目，或该文件不再存在，则返回 `False`。

这一结果是缓存在 `os.DirEntry` 对象中的。缓存、系统调用、异常抛出都与 `is_dir()` 一致。

is_symlink()¶
~~~
    

~~~
如果本条目是符号链接（即使是断开的链接），返回 `True`。如果是目录或任何类型的文件，或本条目不再存在，返回 `False`。

这一结果是缓存在 `os.DirEntry` 对象中的，请调用 [`os.path.islink()`](os.path.md#os.path.islink "os.path.islink") 来获取最新信息。

一开始没有缓存时，大多数情况下不需要系统调用。其实 Windows 和 Unix 都不需要系统调用，除非某些 Unix 文件系统（如网络文件系统）返回了 `dirent.d_type == DT_UNKNOWN`。

本方法可能抛出 [`OSError`](exceptions.md#OSError "OSError") 异常，如 [`PermissionError`](exceptions.md#PermissionError "PermissionError") 异常，但 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError") 异常会被内部捕获且不会抛出。

is_junction()¶
~~~
    

~~~
Return `True` if this entry is a junction (even if broken); return `False` if the entry points to a regular directory, any kind of file, a symlink, or if it doesn't exist anymore.

The result is cached on the `os.DirEntry` object. Call [`os.path.isjunction()`](os.path.md#os.path.isjunction "os.path.isjunction") to fetch up-to-date information.

在 3.12 版本加入.

stat( _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
返回本条目对应的 `stat_result` 对象。本方法默认会跟踪符号链接，要获取符号链接本身的 stat，请添加 `follow_symlinks=False` 参数。

在 Unix 上，本方法需要一次系统调用。在 Windows 上，仅在 _follow_symlinks_ 为 `True` 且该条目是一个重解析点（如符号链接或目录结点）时，才需要一次系统调用。

在 Windows 上，`stat_result` 的 `st_ino`、`st_dev` 和 `st_nlink` 属性总是为零。请调用 `os.stat()` 以获得这些属性。

这一结果是缓存在 `os.DirEntry` 对象中的，且 _follow_symlinks_ 为 `True` 和 `False` 时的缓存是分开的。请调用 `os.stat()` 来获取最新信息。

Note that there is a nice correspondence between several attributes and methods of `os.DirEntry` and of [`pathlib.Path`](pathlib.md#pathlib.Path "pathlib.Path"). In particular, the `name` attribute has the same meaning, as do the `is_dir()`, `is_file()`, `is_symlink()`, `is_junction()`, and `stat()` methods.

在 3.5 版本加入.

在 3.6 版本发生变更: 添加了对 `PathLike` 接口的支持。在 Windows 上添加了对 [`bytes`](stdtypes.md#bytes "bytes") 类型路径的支持。

在 3.12 版本发生变更: The `st_ctime` attribute of a stat result is deprecated on Windows. The file creation time is properly available as `st_birthtime`, and in the future `st_ctime` may be changed to return zero or the metadata change time, if available.

os.stat( _path_ , _*_ , _dir_fd =None_, _follow_symlinks =True_)¶
~~~
    

~~~
获取文件或文件描述符的状态。在所给路径上执行等效于 `stat()` 系统调用的操作。 _path_ 可以是字符串类型，或（直接传入或通过 `PathLike` 接口间接传入的） bytes 类型，或打开的文件描述符。返回一个 `stat_result` 对象。

本函数默认会跟踪符号链接，要获取符号链接本身的 stat，请添加 `follow_symlinks=False` 参数，或使用 `lstat()`。

本函数支持 指定文件描述符为参数 和 不跟踪符号链接。

在 Windows 上，传入 `follow_symlinks=False` 将禁用所有名称代理重解析点，其中包括符号链接和目录结点。其他类型的重解析点将直接打开，比如不像链接的或系统无法跟踪的重解析点。当多个链接形成一个链时，本方法可能会返回原始链接的 stat，无法完整遍历到非链接的对象。在这种情况下，要获取最终路径的 stat，请使用 [`os.path.realpath()`](os.path.md#os.path.realpath "os.path.realpath") 函数尽可能地解析路径，并在解析结果上调用 `lstat()`。这不适用于空链接或交接点，否则会抛出异常。

示例:
~~~
    
    
~~~shell
>>> import os
>>> statinfo = os.stat('somefile.txt')
>>> statinfo
os.stat_result(st_mode=33188, st_ino=7876932, st_dev=234881026,
st_nlink=1, st_uid=501, st_gid=501, st_size=264, st_atime=1297230295,
st_mtime=1297230027, st_ctime=1297230027)
>>> statinfo.st_size
264
~~~

参见

`fstat()` 和 `lstat()` 函数。

在 3.3 版本加入: 增加 _dir_fd_ 和 _follow_symlinks_ 参数，可指定文件描述符代替路径。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.8 版本发生变更: 在 Windows 上，本方法将跟踪系统能解析的所有重解析点，并且传入 `follow_symlinks=False` 会停止跟踪所有名称代理重解析点。现在，如果操作系统遇到无法跟踪的重解析点， _stat_ 将返回原始路径的信息，就像已指定 `follow_symlinks=False` 一样，而不会抛出异常。

_class _os.stat_result¶

    

~~~
Object whose attributes correspond roughly to the members of the `stat` structure. It is used for the result of `os.stat()`, `os.fstat()` and `os.lstat()`.

属性：

st_mode¶
~~~
    

~~~
文件模式：包括文件类型和文件模式位（即权限位）。

st_ino¶
~~~
    

~~~
与平台有关，但如果不为零，则根据 `st_dev` 值唯一地标识文件。通常：

  * 在 Unix 上该值表示索引节点号 (inode number)。

  * 在 Windows 上该值表示 [文件索引号](https://msdn.microsoft.com/en-us/library/aa363788) 。

st_dev¶
~~~
    

~~~
该文件所在设备的标识符。

st_nlink¶
~~~
    

~~~
硬链接的数量。

st_uid¶
~~~
    

~~~
文件所有者的用户 ID。

st_gid¶
~~~
    

~~~
文件所有者的用户组 ID。

st_size¶
~~~
    

~~~
文件大小（以字节为单位），文件可以是常规文件或符号链接。符号链接的大小是它包含的路径的长度，不包括末尾的空字节。

时间戳：

st_atime¶
~~~
    

~~~
最近的访问时间，以秒为单位。

st_mtime¶
~~~
    

~~~
最近的修改时间，以秒为单位。

st_ctime¶
~~~
    

~~~
以秒数表示的元数据最近更改的时间。

在 3.12 版本发生变更: `st_ctime` is deprecated on Windows. Use `st_birthtime` for the file creation time. In the future, `st_ctime` will contain the time of the most recent metadata change, as for other platforms.

st_atime_ns¶
~~~
    

~~~
最近的访问时间，以纳秒表示，为整数。

st_mtime_ns¶
~~~
    

~~~
最近的修改时间，以纳秒表示，为整数。

st_ctime_ns¶
~~~
    

~~~
Time of most recent metadata change expressed in nanoseconds as an integer.

在 3.12 版本发生变更: `st_ctime_ns` is deprecated on Windows. Use `st_birthtime_ns` for the file creation time. In the future, `st_ctime` will contain the time of the most recent metadata change, as for other platforms.

st_birthtime¶
~~~
    

~~~
Time of file creation expressed in seconds. This attribute is not always available, and may raise [`AttributeError`](exceptions.md#AttributeError "AttributeError").

在 3.12 版本发生变更: `st_birthtime` is now available on Windows.

st_birthtime_ns¶
~~~
    

~~~
Time of file creation expressed in nanoseconds as an integer. This attribute is not always available, and may raise [`AttributeError`](exceptions.md#AttributeError "AttributeError").

在 3.12 版本加入.

备注

The exact meaning and resolution of the `st_atime`, `st_mtime`, `st_ctime` and `st_birthtime` attributes depend on the operating system and the file system. For example, on Windows systems using the FAT32 file systems, `st_mtime` has 2-second resolution, and `st_atime` has only 1-day resolution. See your operating system documentation for details.

Similarly, although `st_atime_ns`, `st_mtime_ns`, `st_ctime_ns` and `st_birthtime_ns` are always expressed in nanoseconds, many systems do not provide nanosecond precision. On systems that do provide nanosecond precision, the floating-point object used to store `st_atime`, `st_mtime`, `st_ctime` and `st_birthtime` cannot preserve all of it, and as such will be slightly inexact. If you need the exact timestamps you should always use `st_atime_ns`, `st_mtime_ns`, `st_ctime_ns` and `st_birthtime_ns`.

在某些 Unix 系统上（如 Linux 上），以下属性可能也可用：

st_blocks¶
~~~
    

~~~
为文件分配的字节块数，每块 512 字节。文件是稀疏文件时，它可能小于 `st_size`/512。

st_blksize¶
~~~
    

~~~
“首选的” 块大小，用于提高文件系统 I/O 效率。写入文件时块大小太小可能会导致读取-修改-重写效率低下。

st_rdev¶
~~~
    

~~~
设备类型（如果是 inode 设备）。

st_flags¶
~~~
    

~~~
用户定义的文件标志位。

在其他 Unix 系统上（如 FreeBSD 上），以下属性可能可用（但仅当 root 使用它们时才被填充）：

st_gen¶
~~~
    

~~~
文件生成号。

在 Solaris 及其衍生版本上，以下属性可能也可用：

st_fstype¶
~~~
    

~~~
文件所在文件系统的类型的唯一标识，为字符串。

在 macOS 系统上，以下属性可能也可用：

st_rsize¶
~~~
    

~~~
文件的实际大小。

st_creator¶
~~~
    

~~~
文件的创建者。

st_type¶
~~~
    

~~~
文件类型。

在 Windows 系统上，以下属性也可用：

st_file_attributes¶
~~~
    

~~~
Windows file attributes: `dwFileAttributes` member of the `BY_HANDLE_FILE_INFORMATION` structure returned by `GetFileInformationByHandle()`. See the `FILE_ATTRIBUTE_* <stat.FILE_ATTRIBUTE_ARCHIVE>` constants in the [`stat`](stat.md#module-stat "stat: Utilities for interpreting the results of os.stat\(\), os.lstat\(\) and os.fstat\(\).") module.

st_reparse_tag¶
~~~
    

~~~
When `st_file_attributes` has the [`FILE_ATTRIBUTE_REPARSE_POINT`](stat.md#stat.FILE_ATTRIBUTE_REPARSE_POINT "stat.FILE_ATTRIBUTE_REPARSE_POINT") set, this field contains the tag identifying the type of reparse point. See the [`IO_REPARSE_TAG_*`](stat.md#stat.IO_REPARSE_TAG_SYMLINK "stat.IO_REPARSE_TAG_SYMLINK") constants in the [`stat`](stat.md#module-stat "stat: Utilities for interpreting the results of os.stat\(\), os.lstat\(\) and os.fstat\(\).") module.

The standard module [`stat`](stat.md#module-stat "stat: Utilities for interpreting the results of os.stat\(\), os.lstat\(\) and os.fstat\(\).") defines functions and constants that are useful for extracting information from a `stat` structure. (On Windows, some items are filled with dummy values.)

For backward compatibility, a `stat_result` instance is also accessible as a tuple of at least 10 integers giving the most important (and portable) members of the `stat` structure, in the order `st_mode`, `st_ino`, `st_dev`, `st_nlink`, `st_uid`, `st_gid`, `st_size`, `st_atime`, `st_mtime`, `st_ctime`. More items may be added at the end by some implementations. For compatibility with older Python versions, accessing `stat_result` as a tuple always returns integers.

在 3.3 版本加入: 添加了 `st_atime_ns`、`st_mtime_ns` 和 `st_ctime_ns` 成员。

在 3.5 版本加入: 在 Windows 上添加了 `st_file_attributes` 成员。

在 3.5 版本发生变更: 在 Windows 上，如果可用，会返回文件索引作为 `st_ino` 的值。

在 3.7 版本加入: 在 Solaris 及其衍生版本上添加了 `st_fstype` 成员。

在 3.8 版本加入: 在 Windows 上添加了 `st_reparse_tag` 成员。

在 3.8 版本发生变更: 在 Windows 上，`st_mode` 成员现在可以根据需要将特殊文件标识为 `S_IFCHR`、`S_IFIFO` 或 `S_IFBLK`。

在 3.12 版本发生变更: On Windows, `st_ctime` is deprecated. Eventually, it will contain the last metadata change time, for consistency with other platforms, but for now still contains creation time. Use `st_birthtime` for the creation time.

在 3.12 版本发生变更: On Windows, `st_ino` may now be up to 128 bits, depending on the file system. Previously it would not be above 64 bits, and larger file identifiers would be arbitrarily packed.

在 3.12 版本发生变更: On Windows, `st_rdev` no longer returns a value. Previously it would contain the same as `st_dev`, which was incorrect.

在 3.12 版本加入: Added the `st_birthtime` member on Windows.

os.statvfs( _path_ )¶
~~~
    

~~~
Perform a `statvfs()` system call on the given path. The return value is an object whose attributes describe the filesystem on the given path, and correspond to the members of the `statvfs` structure, namely: `f_bsize`, `f_frsize`, `f_blocks`, `f_bfree`, `f_bavail`, `f_files`, `f_ffree`, `f_favail`, `f_flag`, `f_namemax`, `f_fsid`.

为 `f_flag` 属性位定义了两个模块级常量：如果存在 `ST_RDONLY` 位，则文件系统以只读挂载；如果存在 `ST_NOSUID` 位，则文件系统禁用或不支持 setuid/setgid 位。

为基于 GNU/glibc 的系统还定义了额外的模块级常量。它们是 `ST_NODEV` （禁止访问设备专用文件），`ST_NOEXEC` （禁止执行程序），`ST_SYNCHRONOUS` （写入后立即同步），`ST_MANDLOCK` （允许文件系统上的强制锁定），`ST_WRITE` （写入文件/目录/符号链接），`ST_APPEND` （仅追加文件），`ST_IMMUTABLE` （不可变文件），`ST_NOATIME` （不更新访问时间），`ST_NODIRATIME` （不更新目录访问时间），`ST_RELATIME` （相对于 mtime/ctime 更新访问时间）。

本函数支持 指定文件描述符为参数。

[可用性](intro.md#availability): Unix。

在 3.2 版本发生变更: 添加了 `ST_RDONLY` 和 `ST_NOSUID` 常量。

在 3.3 版本加入: 新增支持将 _path_ 参数指定为打开的文件描述符。

在 3.4 版本发生变更: 添加了 `ST_NODEV`、`ST_NOEXEC`、`ST_SYNCHRONOUS`、`ST_MANDLOCK`、`ST_WRITE`、`ST_APPEND`、`ST_IMMUTABLE`、`ST_NOATIME`、`ST_NODIRATIME` 和 `ST_RELATIME` 常量。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.7 版本加入: 添加了 `f_fsid`。

os.supports_dir_fd¶
~~~
    

~~~
一个 [`set`](stdtypes.md#set "set") 对象，指示 `os` 模块中的哪些函数接受一个打开的文件描述符作为 _dir_fd_ 参数。不同平台提供的功能不同，且 Python 用于实现 _dir_fd_ 参数的底层函数并非在 Python 支持的所有平台上都可用。考虑到一致性，支持 _dir_fd_ 的函数始终允许指定描述符，但如果在底层不支持时调用了该函数，则会抛出异常。（在所有平台上始终支持将 _dir_fd_ 指定为 `None`。）

要检查某个函数是否接受打开的文件描述符作为 _dir_fd_ 参数，请在 `supports_dir_fd` 前使用 `in` 运算符。例如，如果 `os.stat()` 在当前平台上接受打开的文件描述符作为 _dir_fd_ 参数，则此表达式的计算结果为 `True`:
~~~
    
    
~~~
os.stat in os.supports_dir_fd
~~~

目前 _dir_fd_ 参数仅在 Unix 平台上有效，在 Windows 上均无效。

在 3.3 版本加入.

os.supports_effective_ids¶

    

~~~
一个 [`set`](stdtypes.md#set "set") 对象，指示 `os.access()` 是否允许在当前平台上将其 _effective_ids_ 参数指定为 `True`。（所有平台都支持将 _effective_ids_ 指定为 `False`。）如果当前平台支持，则集合将包含 `os.access()`，否则集合为空。

如果当前平台上的 `os.access()` 支持 `effective_ids=True`，则此表达式的计算结果为 `True`:
~~~
    
    
~~~
os.access in os.supports_effective_ids
~~~

目前仅 Unix 平台支持 _effective_ids_ ，Windows 不支持。

在 3.3 版本加入.

os.supports_fd¶

    

~~~
一个 [`set`](stdtypes.md#set "set") 对象，指示在当前平台上 `os` 模块中的哪些函数接受一个打开的文件描述符作为 _path_ 参数。不同平台提供的功能不同，且 Python 所使用到的底层函数（用于实现接受描述符作为 _path_ ）并非在 Python 支持的所有平台上都可用。

要判断某个函数是否接受打开的文件描述符作为 _path_ 参数，请在 `supports_fd` 前使用 `in` 运算符。例如，如果 `os.chdir()` 在当前平台上接受打开的文件描述符作为 _path_ 参数，则此表达式的计算结果为 `True`:
~~~
    
    
~~~
os.chdir in os.supports_fd
~~~

在 3.3 版本加入.

os.supports_follow_symlinks¶

    

~~~
一个 [`set`](stdtypes.md#set "set") 对象，指示在当前平台上 `os` 模块中的哪些函数的 _follow_symlinks_ 参数可指定为 `False`。不同平台提供的功能不同，且 Python 用于实现 _follow_symlinks_ 的底层函数并非在 Python 支持的所有平台上都可用。考虑到一致性，支持 _follow_symlinks_ 的函数始终允许将其指定为 `False`，但如果在底层不支持时调用了该函数，则会抛出异常。（在所有平台上始终支持将 _follow_symlinks_ 指定为 `True`。）

要检查某个函数的 _follow_symlinks_ 参数是否可以指定为 `False`，请在 `supports_follow_symlinks` 前使用 `in` 运算符。例如，如果在当前平台上调用 `os.stat()` 时可以指定 `follow_symlinks=False`，则此表达式的计算结果为 `True`:
~~~
    
    
~~~
os.stat in os.supports_follow_symlinks
~~~

在 3.3 版本加入.

os.symlink( _src_ , _dst_ , _target_is_directory =False_, _*_ , _dir_fd =None_)¶

    

~~~
创建一个指向 _src_ 的符号链接，名为 _dst_ 。

在 Windows 上，符号链接可以表示文件或目录两种类型，并且不会动态改变类型。如果目标存在，则新建链接的类型将与目标一致。否则，如果 _target_is_directory_ 为 `True`，则符号链接将创建为目录链接，为 `False` （默认）将创建为文件链接。在非 Windows 平台上， _target_is_directory_ 被忽略。

本函数支持 基于目录描述符的相对路径。

备注

在 Windows 10 或更高版本上，如果启用了开发人员模式，非特权帐户可以创建符号链接。如果开发人员模式不可用/未启用，则需要 _SeCreateSymbolicLinkPrivilege_ 权限，或者该进程必须以管理员身份运行。

当本函数由非特权账户调用时，抛出 [`OSError`](exceptions.md#OSError "OSError") 异常。

引发一个 [审计事件](sys.md#auditing) `os.symlink`，附带参数 `src`、`dst`、`dir_fd`。

[可用性](intro.md#availability): Unix, Windows。

该函数 Emscripten 和 WASI 将受到限制，请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

在 3.2 版本发生变更: 添加对 Windows 6.0 (Vista) 符号链接的支持。

在 3.3 版本加入: 添加了 _dir_fd_ 参数，现在在非 Windows 平台上允许 _target_is_directory_ 参数。

在 3.6 版本发生变更: 接受一个 [类路径对象](../glossary.md#term-path-like-object) 作为 _src_ 和 _dst_ 。

在 3.8 版本发生变更: 针对启用了开发人员模式的 Windows，添加了非特权账户创建符号链接的支持。

os.sync()¶
~~~
    

~~~
强制将所有内容写入磁盘。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

os.truncate( _path_ , _length_ )¶
~~~
    

~~~
截断 _path_ 对应的文件，以使其最大为 _length_ 字节。

本函数支持 指定文件描述符为参数。

引发一个 [审计事件](sys.md#auditing) `os.truncate`，附带参数 `path`, `length`。

[可用性](intro.md#availability): Unix, Windows。

在 3.3 版本加入.

在 3.5 版本发生变更: 添加了 Windows 支持

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.unlink( _path_ , _*_ , _dir_fd =None_)¶
~~~
    

~~~
移除（删除）文件 _path_ 。该函数在语义上与 `remove()` 相同，`unlink` 是其传统的 Unix 名称。请参阅 `remove()` 的文档以获取更多信息。

引发一个 [审计事件](sys.md#auditing) `os.remove`，附带参数 `path`、`dir_fd`。

在 3.3 版本加入: _dir_fd_ 参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.utime( _path_ , _times=None_ , _*_ , [ _ns_ , ] _dir_fd=None_ , _follow_symlinks=True_ )¶
~~~
    

~~~
设置文件 _path_ 的访问时间和修改时间。

`utime()` 有 _times_ 和 _ns_ 两个可选参数，它们指定了设置给 _path_ 的时间，用法如下：

  * 如果指定 _ns_ ，它必须是一个 `(atime_ns, mtime_ns)` 形式的二元组，其中每个成员都是一个表示纳秒的整数。

  * 如果 _times_ 不为 `None`，则它必须是 `(atime, mtime)` 形式的二元组，其中每个成员都是一个表示秒的 int 或 float。

  * 如果 _times_ 为 `None` 且未指定 _ns_ ，则相当于指定 `ns=(atime_ns, mtime_ns)`，其中两个时间均为当前时间。

同时为 _times_ 和 _ns_ 指定元组会出错。

请注意你在此处设置的确切时间可能不会被后续的 `stat()` 调用所返回，具体取决于你的操作系统记录访问和修改时间的分辨率；请参阅 `stat()`。 保留准确时间的最佳方式是使用来自于将 _ns_ 形参设为 `utime()` 的 `os.stat()` 结果对象的 _st_atime_ns_ 和 _st_mtime_ns_ 字段。

本函数支持 指定文件描述符、指定基于目录描述符的相对路径 和 不跟踪符号链接。

引发一个 [审计事件](sys.md#auditing) `os.utime`，附带参数 `path`、`times`、`ns`、`dir_fd`。

在 3.3 版本加入: 新增支持将 _path_ 参数指定为打开的文件描述符，以及支持 _dir_fd_ 、 _follow_symlinks_ 和 _ns_ 参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.walk( _top_ , _topdown =True_, _onerror =None_, _followlinks =False_)¶
~~~
    

~~~
生成目录树中的文件名，方式是按上->下或下->上顺序浏览目录树。对于以 _top_ 为根的目录树中的每个目录（包括 _top_ 本身），它都会生成一个三元组 `(dirpath, dirnames, filenames)`。

_dirpath_ 是一个字符串，表示目录的路径。 _dirnames_ 是由 _dirpath_ 中的子目录名称组成的列表 (包括指向目录的符号链接，不包括 `'.'` 和 `'..'`)。 _filenames_ 是由 _dirpath_ 中非目录文件名称组成的列表。 请注意列表中的名称不包含路径部分。 要获取 _dirpath_ 中文件或目录的完整路径 (以 _top_ 打头，请执行 `os.path.join(dirpath, name)`。 列表是否排序取决于具体文件系统。 如果有文件在列表生成期间被移除或添加到 _dirpath_ ，是否要包括该文件的名称并没有规定。

如果可选参数 _topdown_ 为 `True` 或未指定，则在所有子目录的三元组之前生成父目录的三元组（目录是自上而下生成的）。如果 _topdown_ 为 `False`，则在所有子目录的三元组生成之后再生成父目录的三元组（目录是自下而上生成的）。无论 _topdown_ 为何值，在生成目录及其子目录的元组之前，都将检索全部子目录列表。

当 _topdown_ 为 `True` 时，调用者可以就地修改 _dirnames_ 列表（也许用到了 [`del`](../reference/simple_stmts.md#del) 或切片），而 `walk()` 将仅仅递归到仍保留在 _dirnames_ 中的子目录内。这可用于减少搜索、加入特定的访问顺序，甚至可在继续 `walk()` 之前告知 `walk()` 由调用者新建或重命名的目录的信息。当 _topdown_ 为 `False` 时，修改 _dirnames_ 对 walk 的行为没有影响，因为在自下而上模式中， _dirnames_ 中的目录是在 _dirpath_ 本身之前生成的。

默认将忽略 `scandir()` 调用中的错误。如果指定了可选参数 _onerror_ ，它应该是一个函数。出错时它会被调用，参数是一个 [`OSError`](exceptions.md#OSError "OSError") 实例。它可以报告错误然后继续遍历，或者抛出异常然后中止遍历。注意，可以从异常对象的 `filename` 属性中获取出错的文件名。

`walk()` 默认不会递归进指向目录的符号链接。可以在支持符号链接的系统上将 _followlinks_ 设置为 `True`，以访问符号链接指向的目录。

备注

注意，如果链接指向自身的父目录，则将 _followlinks_ 设置为 `True` 可能导致无限递归。`walk()` 不会记录它已经访问过的目录。

备注

如果传入的是相对路径，请不要在恢复 `walk()` 之间更改当前工作目录。`walk()` 不会更改当前目录，并假定其调用者也不会更改当前目录。

下面的示例遍历起始目录内所有子目录，打印每个目录内的文件占用的字节数，CVS 子目录不会被遍历:
~~~
    
    
~~~
import os
from os.path import join, getsize
for root, dirs, files in os.walk('python/Lib/email'):
    print(root, "consumes", end=" ")
    print(sum(getsize(join(root, name)) for name in files), end=" ")
    print("bytes in", len(files), "non-directory files")
    if 'CVS' in dirs:
        dirs.remove('CVS')  # don't visit CVS directories
~~~

在下一个示例（[`shutil.rmtree()`](shutil.md#shutil.rmtree "shutil.rmtree") 的简单实现）中，必须使树自下而上遍历，因为 `rmdir()` 只允许在目录为空时删除目录:

    
    
~~~
# Delete everything reachable from the directory named in "top",
# assuming there are no symbolic links.
# CAUTION:  This is dangerous!  For example, if top == '/', it
# could delete all your disk files.
import os
for root, dirs, files in os.walk(top, topdown=False):
    for name in files:
        os.remove(os.path.join(root, name))
    for name in dirs:
        os.rmdir(os.path.join(root, name))
~~~

引发一个 [审计事件](3.标准库/sys.md#auditing) `os.walk`，附带参数 `top`, `topdown`, `onerror`, `followlinks`。

在 3.5 版本发生变更: 现在，本函数调用的是 `os.scandir()` 而不是 `os.listdir()`，从而减少了调用 `os.stat()` 的次数而变得更快。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.fwalk( _top ='.'_, _topdown =True_, _onerror =None_, _*_ , _follow_symlinks =False_, _dir_fd =None_)¶

    

~~~
本方法的行为与 `walk()` 完全一样，除了它产生的是 4 元组 `(dirpath, dirnames, filenames, dirfd)`，并且它支持 `dir_fd`。

_dirpath_ 、 _dirnames_ 和 _filenames_ 与 `walk()` 输出的相同， _dirfd_ 是指向目录 _dirpath_ 的文件描述符。

本函数始终支持 基于目录描述符的相对路径 和 不跟踪符号链接。但是请注意，与其他函数不同，`fwalk()` 的 _follow_symlinks_ 的默认值为 `False`。

备注

由于 `fwalk()` 会生成文件描述符，而它们仅在下一个迭代步骤前有效，因此如果要将描述符保留更久，则应复制它们（比如使用 `dup()`）。

下面的示例遍历起始目录内所有子目录，打印每个目录内的文件占用的字节数，CVS 子目录不会被遍历:
~~~
    
    
~~~
import os
for root, dirs, files, rootfd in os.fwalk('python/Lib/email'):
    print(root, "consumes", end="")
    print(sum([os.stat(name, dir_fd=rootfd).st_size for name in files]),
          end="")
    print("bytes in", len(files), "non-directory files")
    if 'CVS' in dirs:
        dirs.remove('CVS')  # don't visit CVS directories
~~~

在下一个示例中，必须使树自下而上遍历，因为 `rmdir()` 只允许在目录为空时删除目录:

    
    
~~~
# Delete everything reachable from the directory named in "top",
# assuming there are no symbolic links.
# CAUTION:  This is dangerous!  For example, if top == '/', it
# could delete all your disk files.
import os
for root, dirs, files, rootfd in os.fwalk(top, topdown=False):
    for name in files:
        os.unlink(name, dir_fd=rootfd)
    for name in dirs:
        os.rmdir(name, dir_fd=rootfd)
~~~

引发一个 [审计事件](3.标准库/sys.md#auditing) `os.fwalk`，附带参数 `top`, `topdown`, `onerror`, `follow_symlinks`, `dir_fd`。

[可用性](3.标准库/intro.md#availability): Unix。

在 3.3 版本加入.

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.7 版本发生变更: 添加了对 [`bytes`](stdtypes.md#bytes "bytes") 类型路径的支持。

os.memfd_create( _name_ [, _flags=os.MFD_CLOEXEC_ ])¶

    

~~~
创建一个匿名文件，返回指向该文件的文件描述符。 _flags_ 必须是系统上可用的 `os.MFD_*` 常量之一（或将它们按位“或”组合起来）。新文件描述符默认是 不可继承的。

_name_ 提供的名称会被用作文件名，并且 `/proc/self/fd/` 目录中相应符号链接的目标将显示为该名称。显示的名称始终以 `memfd:` 为前缀，并且仅用于调试目的。名称不会影响文件描述符的行为，因此多个文件可以有相同的名称，不会有副作用。

[可用性](intro.md#availability): Linux >= 3.17 且 glibc >= 2.27。

在 3.8 版本加入.

os.MFD_CLOEXEC¶

os.MFD_ALLOW_SEALING¶

os.MFD_HUGETLB¶

os.MFD_HUGE_SHIFT¶

os.MFD_HUGE_MASK¶

os.MFD_HUGE_64KB¶

os.MFD_HUGE_512KB¶

os.MFD_HUGE_1MB¶

os.MFD_HUGE_2MB¶

os.MFD_HUGE_8MB¶

os.MFD_HUGE_16MB¶

os.MFD_HUGE_32MB¶

os.MFD_HUGE_256MB¶

os.MFD_HUGE_512MB¶

os.MFD_HUGE_1GB¶

os.MFD_HUGE_2GB¶

os.MFD_HUGE_16GB¶
~~~
    

~~~
以上标志位可以传递给 `memfd_create()`。

[可用性](intro.md#availability): Linux >= 3.17 且 glibc >= 2.27

`MFD_HUGE*` 旗标仅从 Linux 4.14 开始可用。

在 3.8 版本加入.

os.eventfd( _initval_ [, _flags=os.EFD_CLOEXEC_ ])¶
~~~
    

~~~
创建并返回一个事件文件描述符。此文件描述符支持缓冲区大小为 8 的原生 `read()` 和 `write()` 操作、[`select()`](select.md#select.select "select.select") 、[`poll()`](select.md#select.poll "select.poll") 等类似操作。更多信息请参阅 man 文档 _[eventfd(2)](https://manpages.debian.org/eventfd\(2\))_ 。默认情况下，新的文件描述符是 non-inheritable。

_initval_ 是事件计数器的初始值。初始值必须是一个 32 位无符号整数。请注意，虽然事件计数器是一个无符号的 64 位整数，其最大值为 2 64-2，但初始值仍被限制为 32 位无符号整数。

_flags_ 可由 `EFD_CLOEXEC` 、 `EFD_NONBLOCK` 和 `EFD_SEMAPHORE` 组合而成。

如果设置了 `EFD_SEMAPHORE`，并且事件计数器非零，那么 `eventfd_read()` 将返回 1 并将计数器递减 1。

如果未设置 `EFD_SEMAPHORE`，并且事件计数器非零，那么 `eventfd_read()` 返回当前的事件计数器值，并将计数器重置为零。

如果事件计数器为 0，并且未设置 `EFD_NONBLOCK`，那么 `eventfd_read()` 会阻塞。

`eventfd_write()` 会递增事件计数器。如果写操作会让计数器的增量大于264-2，则写入会被阻止。

示例:
~~~
    
    
~~~
import os

# semaphore with start value '1'
fd = os.eventfd(1, os.EFD_SEMAPHORE | os.EFC_CLOEXEC)
try:
    # acquire semaphore
    v = os.eventfd_read(fd)
    try:
        do_work()
    finally:
        # release semaphore
        os.eventfd_write(fd, v)
finally:
    os.close(fd)
~~~

[可用性](3.标准库/intro.md#availability): Linux >= 2.6.27 且 glibc >= 2.8

在 3.10 版本加入.

os.eventfd_read( _fd_ )¶

    

~~~
从一个 `eventfd()` 文件描述符中读取数据，并返回一个 64 位无符号整数。该函数不会校验 _fd_ 是否为 `eventfd()`。

[可用性](intro.md#availability): Linux >= 2.6.27

在 3.10 版本加入.

os.eventfd_write( _fd_ , _value_ )¶
~~~
    

~~~
向一个 `eventfd()` 文件描述符加入数据。 _value_ 必须是一个 64 位无符号整数。本函数不会校验 _fd_ 是否为 `eventfd()`。

[可用性](intro.md#availability): Linux >= 2.6.27

在 3.10 版本加入.

os.EFD_CLOEXEC¶
~~~
    

~~~
为新的 `eventfd()` 文件描述符设置 close-on-exec 标志。

[可用性](intro.md#availability): Linux >= 2.6.27

在 3.10 版本加入.

os.EFD_NONBLOCK¶
~~~
    

~~~
为新的 `eventfd()` 文件描述符设置 `O_NONBLOCK` 状态标志。

[可用性](intro.md#availability): Linux >= 2.6.27

在 3.10 版本加入.

os.EFD_SEMAPHORE¶
~~~
    

~~~
为读取 `eventfd()` 文件描述符的操作提供类似信号量的控制。在读取时，内部计数器递减 1。

[可用性](intro.md#availability): Linux >= 2.6.30

在 3.10 版本加入.

### Linux 扩展属性¶

在 3.3 版本加入.

这些函数仅在 Linux 上可用。

os.getxattr( _path_ , _attribute_ , _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
返回 _path_ 的扩展文件系统属性 _attribute_ 的值。 _attribute_ 可以是 bytes 或 str （直接传入或通过 `PathLike` 接口间接传入）。如果是 str，则使用文件系统编码来编码字符串。

本函数支持 指定文件描述符为参数 和 不跟踪符号链接。

引发一个 [审计事件](sys.md#auditing) `os.getxattr`，附带参数 `path`、`attribute`。

在 3.6 版本发生变更: 接受一个 [类路径对象](../glossary.md#term-path-like-object) 作为 _path_ 和 _attribute_ 。

os.listxattr( _path =None_, _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
返回一个列表，包含 _path_ 的所有扩展文件系统属性。列表中的属性都表示为字符串，它们是根据文件系统编码解码出来的。如果 _path_ 为 `None`，则 `listxattr()` 将检查当前目录。

本函数支持 指定文件描述符为参数 和 不跟踪符号链接。

引发一个 [审计事件](sys.md#auditing) `os.listxattr`，附带参数 `path`。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.removexattr( _path_ , _attribute_ , _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
移除 _path_ 中的扩展文件系统属性 _attribute_ 。 _attribute_ 应为字节串或字符串类型（通过 `PathLike` 接口直接或间接得到）。 若为字符串类型，则用 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 进行编码。

本函数支持 指定文件描述符为参数 和 不跟踪符号链接。

引发一个 [审计事件](sys.md#auditing) `os.removexattr`，附带参数 `path`、`attribute`。

在 3.6 版本发生变更: 接受一个 [类路径对象](../glossary.md#term-path-like-object) 作为 _path_ 和 _attribute_ 。

os.setxattr( _path_ , _attribute_ , _value_ , _flags =0_, _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
将 _path_ 的文件系统扩展属性 _attribute_ 设为 _value_ 。 _attribute_ 必须是一个字节串或字符串，不含 NUL（通过 `PathLike` 接口直接或间接得到）。 若为字符串，将用 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 进行编码。 _flags_ 可以是 `XATTR_REPLACE` 或 `XATTR_CREATE`。 如果给出 `XATTR_REPLACE` 而属性不存在，则会触发 `ENODATA`。 如果给出了 `XATTR_CREATE` 而属性已存在，则不会创建属性并将触发 `EEXISTS`。

本函数支持 指定文件描述符为参数 和 不跟踪符号链接。

备注

Linux kernel 2.6.39 以下版本的一个 bug 导致在某些文件系统上，flags 参数会被忽略。

引发一个 [审计事件](sys.md#auditing) `os.setxattr`，附带参数 `path`、`attribute`、`value`、`flags`。

在 3.6 版本发生变更: 接受一个 [类路径对象](../glossary.md#term-path-like-object) 作为 _path_ 和 _attribute_ 。

os.XATTR_SIZE_MAX¶
~~~
    

~~~
一条扩展属性的值的最大大小。在当前的 Linux 上是 64 KiB。

os.XATTR_CREATE¶
~~~
    

~~~
这是 `setxattr()` 的 flags 参数的可取值，它表示该操作必须创建一个属性。

os.XATTR_REPLACE¶
~~~
    

~~~
这是 `setxattr()` 的 flags 参数的可取值，它表示该操作必须替换现有属性。

## 进程管理¶

下列函数可用于创建和管理进程。

所有 `exec*` 函数都接受一个参数列表，用来给新程序加载到它的进程中。在所有情况下，传递给新程序的第一个参数是程序本身的名称，而不是用户在命令行上输入的参数。对于 C 程序员来说，这就是传递给 `main()` 函数的 `argv[0]`。例如，`os.execv('/bin/echo', ['foo', 'bar'])` 只会在标准输出上打印 `bar`，而 `foo` 会被忽略。

os.abort()¶
~~~
    

~~~
发送 `SIGABRT` 信号到当前进程。在 Unix 上，默认行为是生成一个核心转储。在 Windows 上，该进程立即返回退出代码 `3`。请注意，使用 [`signal.signal()`](signal.md#signal.signal "signal.signal") 可以为 `SIGABRT` 注册 Python 信号处理程序，而调用本函数将不会调用按前述方法注册的程序。

os.add_dll_directory( _path_ )¶
~~~
    

~~~
将路径添加到 DLL 搜索路径。

当需要解析扩展模块的依赖时（扩展模块本身通过 [`sys.path`](sys.md#sys.path "sys.path") 解析），会使用该搜索路径，[`ctypes`](ctypes.md#module-ctypes "ctypes: A foreign function library for Python.") 也会使用该搜索路径。

要移除目录，可以在返回的对象上调用 **close()** ，也可以在 [`with`](../reference/compound_stmts.md#with) 语句内使用本方法。

参阅 [Microsoft 文档](https://msdn.microsoft.com/44228cf2-6306-466c-8f16-f513cd3ba8b5) 获取如何加载 DLL 的信息。

引发一个 [审计事件](sys.md#auditing) `os.add_dll_directory`，附带参数 `path`。

[可用性](intro.md#availability): Windows。

在 3.8 版本加入: 早期版本的 CPython 解析 DLL 时用的是当前进程的默认行为。这会导致不一致，比如不是每次都会去搜索 `PATH` 和当前工作目录，且系统函数（如 `AddDllDirectory` ）失效。

在 3.8 中，DLL 的两种主要加载方式现在可以显式覆盖进程的行为，以确保一致性。请参阅 [移植说明](../whatsnew/3.8.md#bpo-36085-whatsnew) 了解如何更新你的库。

os.execl( _path_ , _arg0_ , _arg1_ , _..._ )¶

os.execle( _path_ , _arg0_ , _arg1_ , _..._ , _env_ )¶

os.execlp( _file_ , _arg0_ , _arg1_ , _..._ )¶

os.execlpe( _file_ , _arg0_ , _arg1_ , _..._ , _env_ )¶

os.execv( _path_ , _args_ )¶

os.execve( _path_ , _args_ , _env_ )¶

os.execvp( _file_ , _args_ )¶

os.execvpe( _file_ , _args_ , _env_ )¶
~~~
    

~~~
这些函数都将执行一个新程序，以替换当前进程。它们没有返回值。在 Unix 上，新程序会加载到当前进程中，且进程号与调用者相同。过程中的错误会被报告为 [`OSError`](exceptions.md#OSError "OSError") 异常。

当前进程会被立即替换。打开的文件对象和描述符都不会刷新，因此如果这些文件上可能缓冲了数据，则应在调用 `exec*` 函数之前使用 `sys.stdout.flush()` 或 `os.fsync()` 刷新它们。

`exec*` 函数的 "l" 和 "v" 变体不同在于命令行参数的传递方式。如果在编码时固定了参数数量，则 "l" 变体可能是最方便的，各参数作为 `execl*()` 函数的附加参数传入即可。当参数数量可变时，"v" 变体更方便，参数以列表或元组的形式作为 _args_ 参数传递。在这两种情况下，子进程的第一个参数都应该是即将运行的命令名称，但这不是强制性的。

The variants which include a "p" near the end (`execlp()`, `execlpe()`, `execvp()`, and `execvpe()`) will use the `PATH` environment variable to locate the program _file_. When the environment is being replaced (using one of the `exec*e` variants, discussed in the next paragraph), the new environment is used as the source of the `PATH` variable. The other variants, `execl()`, `execle()`, `execv()`, and `execve()`, will not use the `PATH` variable to locate the executable; _path_ must contain an appropriate absolute or relative path. Relative paths must include at least one slash, even on Windows, as plain names will not be resolved.

对于 `execle()`、`execlpe()`、`execve()` 和 `execvpe()` （都以 "e" 结尾）， _env_ 参数是一个映射，用于定义新进程的环境变量（代替当前进程的环境变量）。而函数 `execl()`、`execlp()`、`execv()` 和 `execvp()` 会将当前进程的环境变量过继给新进程。

某些平台上的 `execve()` 可以将 _path_ 指定为打开的文件描述符。当前平台可能不支持此功能，可以使用 `os.supports_fd` 检查它是否支持。如果不可用，则使用它会抛出 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") 异常。

引发一个 [审计事件](sys.md#auditing) `os.exec`，附带参数 `path`、`args`、`env`。

[可用性](intro.md#availability): Unix, Windows, 非 Emscripten, 非 WASI。

在 3.3 版本加入: 新增支持将 `execve()` 的 _path_ 参数指定为打开的文件描述符。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os._exit( _n_ )¶
~~~
    

~~~
以状态码 _n_ 退出进程，不会调用清理处理程序，不会刷新 stdio，等等。

备注

退出的标准方式是使用 [`sys.exit(n)`](sys.md#sys.exit "sys.exit")。 `_exit()` 通常只应在 `fork()` 所生成的子进程中使用。

以下是已定义的退出代码，可以用于 `_exit()`，尽管它们不是必需的。这些退出代码通常用于 Python 编写的系统程序，例如邮件服务器的外部命令传递程序。

备注

其中部分退出代码在部分 Unix 平台上可能不可用，因为平台间存在差异。如果底层平台定义了这些常量，那上层也会定义。

os.EX_OK¶
~~~
    

~~~
表示没有发生错误的退出码。 在某些平台上可能会从 `EXIT_SUCCESS` 定义的值中选取。 通常其值为零。

[可用性](intro.md#availability): Unix, Windows。

os.EX_USAGE¶
~~~
    

~~~
退出代码，表示命令使用不正确，如给出的参数数量有误。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_DATAERR¶
~~~
    

~~~
退出代码，表示输入数据不正确。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_NOINPUT¶
~~~
    

~~~
退出代码，表示某个输入文件不存在或不可读。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_NOUSER¶
~~~
    

~~~
退出代码，表示指定的用户不存在。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_NOHOST¶
~~~
    

~~~
退出代码，表示指定的主机不存在。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_UNAVAILABLE¶
~~~
    

~~~
退出代码，表示所需的服务不可用。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_SOFTWARE¶
~~~
    

~~~
退出代码，表示检测到内部软件错误。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_OSERR¶
~~~
    

~~~
退出代码，表示检测到操作系统错误，例如无法 fork 或创建管道。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_OSFILE¶
~~~
    

~~~
退出代码，表示某些系统文件不存在、无法打开或发生其他错误。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_CANTCREAT¶
~~~
    

~~~
退出代码，表示无法创建用户指定的输出文件。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_IOERR¶
~~~
    

~~~
退出代码，表示对某些文件进行读写时发生错误。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_TEMPFAIL¶
~~~
    

~~~
退出代码，表示发生了暂时性故障。它可能并非意味着真正的错误，例如在可重试的情况下无法建立网络连接。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_PROTOCOL¶
~~~
    

~~~
退出代码，表示协议交换是非法的、无效的或无法解读的。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_NOPERM¶
~~~
    

~~~
退出代码，表示没有足够的权限执行该操作（但不适用于文件系统问题）。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_CONFIG¶
~~~
    

~~~
退出代码，表示发生某种配置错误。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.EX_NOTFOUND¶
~~~
    

~~~
退出代码，表示的内容类似于“找不到条目”。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.fork()¶
~~~
    

~~~
Fork 出一个子进程。在子进程中返回 `0`，在父进程中返回子进程的进程号。如果发生错误，则抛出 [`OSError`](exceptions.md#OSError "OSError") 异常。

注意，当从线程中使用 `fork()` 时，某些平台（包括 FreeBSD <= 6.3 和 Cygwin）存在已知问题。

引发一个 [审计事件](sys.md#auditing) `os.fork`，没有附带参数。

在 3.8 版本发生变更: 不再支持在子解释器中调用 `fork()` （将抛出 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 异常）。

警告

有关 SSL 模块与 fork() 结合的应用，请参阅 [`ssl`](ssl.md#module-ssl "ssl: TLS/SSL wrapper for socket objects")。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.forkpty()¶
~~~
    

~~~
Fork 出一个子进程，使用新的伪终端作为子进程的控制终端。返回一对 `(pid, fd)`，其中 _pid_ 在子进程中为 `0`，这是父进程中新子进程的进程号，而 _fd_ 是伪终端主设备的文件描述符。对于更便于移植的方法，请使用 [`pty`](pty.md#module-pty "pty: Pseudo-Terminal Handling for Unix. \(Unix\)") 模块。如果发生错误，则抛出 [`OSError`](exceptions.md#OSError "OSError") 异常。

引发一个 [审计事件](sys.md#auditing) `os.forkpty`，没有附带参数。

在 3.8 版本发生变更: 不再支持在子解释器中调用 `forkpty()` （将抛出 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 异常）。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.kill( _pid_ , _sig_ , _/_ )¶
~~~
    

~~~
将信号 _sig_ 发送至进程 _pid_ 。特定平台上可用的信号常量定义在 [`signal`](signal.md#module-signal "signal: Set handlers for asynchronous events.") 模块中。

Windows: The [`signal.CTRL_C_EVENT`](signal.md#signal.CTRL_C_EVENT "signal.CTRL_C_EVENT") and [`signal.CTRL_BREAK_EVENT`](signal.md#signal.CTRL_BREAK_EVENT "signal.CTRL_BREAK_EVENT") signals are special signals which can only be sent to console processes which share a common console window, e.g., some subprocesses. Any other value for _sig_ will cause the process to be unconditionally killed by the TerminateProcess API, and the exit code will be set to _sig_. The Windows version of `kill()` additionally takes process handles to be killed.

另请参阅 [`signal.pthread_kill()`](signal.md#signal.pthread_kill "signal.pthread_kill")。

引发一个 [审计事件](sys.md#auditing) `os.kill`，附带参数 `pid`、`sig`。

[可用性](intro.md#availability): Unix, Windows, 非 Emscripten, 非 WASI。

在 3.2 版本加入: Windows 支持。

os.killpg( _pgid_ , _sig_ , _/_ )¶
~~~
    

~~~
将信号 _sig_ 发送给进程组 _pgid_ 。

引发一个 [审计事件](sys.md#auditing) `os.killpg`，附带参数 `pgid`、`sig`。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.nice( _increment_ , _/_ )¶
~~~
    

~~~
将进程的优先级（nice 值）增加 _increment_ ，返回新的 nice 值。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.pidfd_open( _pid_ , _flags =0_)¶
~~~
    

~~~
Return a file descriptor referring to the process _pid_ with _flags_ set. This descriptor can be used to perform process management without races and signals.

更多详细信息请参阅 _[pidfd_open(2)](https://manpages.debian.org/pidfd_open\(2\))_ 手册页。

[可用性](intro.md#availability): Linux >= 5.3

在 3.9 版本加入.

os.PIDFD_NONBLOCK¶
~~~
    

~~~
This flag indicates that the file descriptor will be non-blocking. If the process referred to by the file descriptor has not yet terminated, then an attempt to wait on the file descriptor using _[waitid(2)](https://manpages.debian.org/waitid\(2\))_ will immediately return the error [`EAGAIN`](errno.md#errno.EAGAIN "errno.EAGAIN") rather than blocking.

[Availability](intro.md#availability): Linux >= 5.10

在 3.12 版本加入.

os.plock( _op_ , _/_ )¶
~~~
    

~~~
将程序段锁定到内存中。 _op_ 的值（定义在 `<sys/lock.h>` 中）决定了哪些段被锁定。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.popen( _cmd_ , _mode ='r'_, _buffering =-1_)¶
~~~
    

~~~
打开一个通往或接受命令 _cmd_ 的管道。 返回值是连接到该管道的已打开文件对象，它可读取还是可写入取决于其 _mode_ 是 `'r'` (默认) 还是 `'w'`。 _buffering_ 参数与内置 [`open()`](functions.md#open "open") 函数相应的参数含义相同。 返回的文件对象只能读写文本字符串而不是字节串。

如果子进程成功退出，则 `close` 方法返回 [`None`](constants.md#None "None")。如果发生错误，则返回子进程的返回码。在 POSIX 系统上，如果返回码为正，则它就是进程返回值左移一个字节后的值。如果返回码为负，则进程是被信号终止的，返回码取反后就是该信号。（例如，如果子进程被终止，则返回值可能是 `- signal.SIGKILL`。）在 Windows 系统上，返回值包含子进程的返回码（有符号整数）。

在 Unix 上，`waitstatus_to_exitcode()` 可以将 `close` 方法的返回值（即退出状态，不能是 `None`）转换为退出码。在 Windows 上，`close` 方法的结果直接就是退出码（或 `None` ）。

本方法是使用 [`subprocess.Popen`](subprocess.md#subprocess.Popen "subprocess.Popen") 实现的，如需更强大的方法来管理和沟通子进程，请参阅该类的文档。

[可用性](intro.md#availability): 非 Emscripten，非 WASI。

备注

Python UTF-8 模型 影响 _cmd_ 和管道内容所使用的编码格式。

`popen()` 是针对 [`subprocess.Popen`](subprocess.md#subprocess.Popen "subprocess.Popen") 的简单包装器。 请使用 [`subprocess.Popen`](subprocess.md#subprocess.Popen "subprocess.Popen") 或 [`subprocess.run()`](subprocess.md#subprocess.run "subprocess.run") 来控制编码格式等选项。

os.posix_spawn( _path_ , _argv_ , _env_ , _*_ , _file_actions =None_, _setpgroup =None_, _resetids =False_, _setsid =False_, _setsigmask =()_, _setsigdef =()_, _scheduler =None_)¶
~~~
    

~~~
Wraps the `posix_spawn()` C library API for use from Python.

大多数用户应使用 [`subprocess.run()`](subprocess.md#subprocess.run "subprocess.run") 代替 `posix_spawn()`。

仅位置参数 (Positional-only arguments) _path_ 、 _args_ 和 _env_ 与 `execve()` 中的类似。

_path_ 形参是可执行文件的路径， _path_ 中应当包含目录。 使用 `posix_spawnp()` 可传入不带目录的可执行文件。

_file_actions_ 参数可以是由元组组成的序列，序列描述了对子进程中指定文件描述符采取的操作，这些操作会在 C 库实现的 `fork()` 和 `exec()` 步骤间完成。每个元组的第一个元素必须是下面列出的三个类型指示符之一，用于描述元组剩余的元素：

os.POSIX_SPAWN_OPEN¶
~~~
    

~~~
(`os.POSIX_SPAWN_OPEN`, _fd_ , _path_ , _flags_ , _mode_ )

执行 `os.dup2(os.open(path, flags, mode), fd)`。

os.POSIX_SPAWN_CLOSE¶
~~~
    

~~~
(`os.POSIX_SPAWN_CLOSE`, _fd_ )

执行 `os.close(fd)`。

os.POSIX_SPAWN_DUP2¶
~~~
    

~~~
(`os.POSIX_SPAWN_DUP2`, _fd_ , _new_fd_ )

执行 `os.dup2(fd, new_fd)`。

These tuples correspond to the C library `posix_spawn_file_actions_addopen()`, `posix_spawn_file_actions_addclose()`, and `posix_spawn_file_actions_adddup2()` API calls used to prepare for the `posix_spawn()` call itself.

The _setpgroup_ argument will set the process group of the child to the value specified. If the value specified is 0, the child's process group ID will be made the same as its process ID. If the value of _setpgroup_ is not set, the child will inherit the parent's process group ID. This argument corresponds to the C library `POSIX_SPAWN_SETPGROUP` flag.

If the _resetids_ argument is `True` it will reset the effective UID and GID of the child to the real UID and GID of the parent process. If the argument is `False`, then the child retains the effective UID and GID of the parent. In either case, if the set-user-ID and set-group-ID permission bits are enabled on the executable file, their effect will override the setting of the effective UID and GID. This argument corresponds to the C library `POSIX_SPAWN_RESETIDS` flag.

If the _setsid_ argument is `True`, it will create a new session ID for `posix_spawn`. _setsid_ requires `POSIX_SPAWN_SETSID` or `POSIX_SPAWN_SETSID_NP` flag. Otherwise, [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") is raised.

The _setsigmask_ argument will set the signal mask to the signal set specified. If the parameter is not used, then the child inherits the parent's signal mask. This argument corresponds to the C library `POSIX_SPAWN_SETSIGMASK` flag.

The _sigdef_ argument will reset the disposition of all signals in the set specified. This argument corresponds to the C library `POSIX_SPAWN_SETSIGDEF` flag.

The _scheduler_ argument must be a tuple containing the (optional) scheduler policy and an instance of `sched_param` with the scheduler parameters. A value of `None` in the place of the scheduler policy indicates that is not being provided. This argument is a combination of the C library `POSIX_SPAWN_SETSCHEDPARAM` and `POSIX_SPAWN_SETSCHEDULER` flags.

引发一个 [审计事件](sys.md#auditing) `os.posix_spawn`，附带参数 `path`、`argv`、`env`。

在 3.8 版本加入.

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.posix_spawnp( _path_ , _argv_ , _env_ , _*_ , _file_actions =None_, _setpgroup =None_, _resetids =False_, _setsid =False_, _setsigmask =()_, _setsigdef =()_, _scheduler =None_)¶
~~~
    

~~~
Wraps the `posix_spawnp()` C library API for use from Python.

与 `posix_spawn()` 相似，但是系统会在 `PATH` 环境变量指定的目录列表中搜索可执行文件 _executable_ （与 `execvp(3)` 相同）。

引发一个 [审计事件](sys.md#auditing) `os.posix_spawn`，附带参数 `path`、`argv`、`env`。

在 3.8 版本加入.

[可用性](intro.md#availability): POSIX，非 Emscripten，非 WASI。

参见 `posix_spawn()` 文档。

os.register_at_fork( _*_ , _before =None_, _after_in_parent =None_, _after_in_child =None_)¶
~~~
    

~~~
注册可调用对象，在使用 `os.fork()` 或类似的进程克隆 API 派生新的子进程时，这些对象会运行。参数是可选的，且为仅关键字 (Keyword-only) 参数。每个参数指定一个不同的调用点。

  * _before_ 是一个函数，在 fork 子进程前调用。

  * _after_in_parent_ 是一个函数，在 fork 子进程后从父进程调用。

  * _after_in_child_ 是一个函数，从子进程中调用。

只有希望控制权回到 Python 解释器时，才进行这些调用。典型的 [`子进程`](subprocess.md#module-subprocess "subprocess: Subprocess management.") 启动时不会触发它们，因为子进程不会重新进入解释器。

在注册的函数中，用于 fork 前运行的函数将按与注册相反的顺序调用。用于 fork 后（从父进程或子进程）运行的函数按注册顺序调用。

注意，第三方 C 代码的 `fork()` 调用可能不会调用这些函数，除非它显式调用了 [`PyOS_BeforeFork()`](../c-api/sys.md#c.PyOS_BeforeFork "PyOS_BeforeFork")、[`PyOS_AfterFork_Parent()`](../c-api/sys.md#c.PyOS_AfterFork_Parent "PyOS_AfterFork_Parent") 和 [`PyOS_AfterFork_Child()`](../c-api/sys.md#c.PyOS_AfterFork_Child "PyOS_AfterFork_Child")。

函数注册后无法注销。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.7 版本加入.

os.spawnl( _mode_ , _path_ , _..._ )¶

os.spawnle( _mode_ , _path_ , _..._ , _env_ )¶

os.spawnlp( _mode_ , _file_ , _..._ )¶

os.spawnlpe( _mode_ , _file_ , _..._ , _env_ )¶

os.spawnv( _mode_ , _path_ , _args_ )¶

os.spawnve( _mode_ , _path_ , _args_ , _env_ )¶

os.spawnvp( _mode_ , _file_ , _args_ )¶

os.spawnvpe( _mode_ , _file_ , _args_ , _env_ )¶
~~~
    

~~~
在新进程中执行程序 _path_ 。

（注意，[`subprocess`](subprocess.md#module-subprocess "subprocess: Subprocess management.") 模块提供了更强大的工具来生成新进程并跟踪执行结果，使用该模块比使用这些函数更好。尤其应当检查 [使用 subprocess 模块替换旧函数](subprocess.md#subprocess-replacements) 部分。）

_mode_ 为 `P_NOWAIT` 时，本函数返回新进程的进程号。 _mode_ 为 `P_WAIT` 时，如果进程正常退出，返回退出代码，如果被终止，返回 `-signal`，其中 _signal_ 是终止进程的信号。在 Windows 上，进程号实际上是进程句柄，因此可以与 `waitpid()` 函数一起使用。

注意在 VxWorks 上，新进程被终止时，本函数不会返回 `-signal`，而是会抛出 OSError 异常。

`spawn*` 函数的 "l" 和 "v" 变体不同在于命令行参数的传递方式。如果在编码时固定了参数数量，则 "l" 变体可能是最方便的，各参数作为 `spawnl*()` 函数的附加参数传入即可。当参数数量可变时，"v" 变体更方便，参数以列表或元组的形式作为 _args_ 参数传递。在这两种情况下，子进程的第一个参数都必须是即将运行的命令名称。

结尾包含第二个 "p" 的变体（`spawnlp()`、`spawnlpe()`、`spawnvp()` 和 `spawnvpe()`）将使用 `PATH` 环境变量来查找程序 _file_ 。当环境被替换时（使用下一段讨论的 `spawn*e` 变体之一），`PATH` 变量将来自于新环境。其他变体 `spawnl()`、`spawnle()`、`spawnv()` 和 `spawnve()` 不使用 `PATH` 变量来查找程序，因此 _path_ 必须包含正确的绝对或相对路径。

对于 `spawnle()`、`spawnlpe()`、`spawnve()` 和 `spawnvpe()` （都以 "e" 结尾）， _env_ 参数是一个映射，用于定义新进程的环境变量（代替当前进程的环境变量）。而函数 `spawnl()`、`spawnlp()`、`spawnv()` 和 `spawnvp()` 会将当前进程的环境变量过继给新进程。注意， _env_ 字典中的键和值必须是字符串。无效的键或值将导致函数出错，返回值为 `127`。

例如，以下对 `spawnlp()` 和 `spawnvpe()` 的调用是等效的:
~~~
    
    
~~~
import os
os.spawnlp(os.P_WAIT, 'cp', 'cp', 'index.md', '/dev/null')

L = ['cp', 'index.md', '/dev/null']
os.spawnvpe(os.P_WAIT, 'cp', L, os.environ)
~~~

引发一个 [审计事件](3.标准库/sys.md#auditing) `os.spawn`，附带参数 `mode`、`path`、`args`、`env`。

[可用性](3.标准库/intro.md#availability): Unix, Windows, 非 Emscripten, 非 WASI。

`spawnlp()`, `spawnlpe()`, `spawnvp()` 和 `spawnvpe()` 在 Windows 上不可用。 `spawnle()` 和 `spawnve()` 在 Windows 上不是线程安全的；我们建议你用 [`subprocess`](subprocess.md#module-subprocess "subprocess: Subprocess management.") 模块来代替。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

os.P_NOWAIT¶

os.P_NOWAITO¶

    

~~~
`spawn*` 系列函数的 _mode_ 参数的可取值。如果给出这些值中的任何一个，则 `spawn*()` 函数将在创建新进程后立即返回，且返回值为进程号。

[可用性](intro.md#availability): Unix, Windows。

os.P_WAIT¶
~~~
    

~~~
`spawn*` 系列函数的 _mode_ 参数的可取值。如果将 _mode_ 指定为该值，则 `spawn*()` 函数将在新进程运行完毕后返回，运行成功则返回进程的退出代码，被信号终止则返回 `-signal`。

[可用性](intro.md#availability): Unix, Windows。

os.P_DETACH¶

os.P_OVERLAY¶
~~~
    

~~~
`spawn*` 系列函数的 _mode_ 参数的可取值。它们比上面列出的值可移植性差。`P_DETACH` 与 `P_NOWAIT` 相似，但是新进程会与父进程的控制台脱离。使用 `P_OVERLAY` 则会替换当前进程，`spawn*` 函数将不会返回。

[可用性](intro.md#availability): Windows。

os.startfile( _path_ [, _operation_ ][, _arguments_ ][, _cwd_ ][, _show_cmd_ ])¶
~~~
    

~~~
使用已关联的应用程序打开文件。

When _operation_ is not specified, this acts like double-clicking the file in Windows Explorer, or giving the file name as an argument to the **start** command from the interactive command shell: the file is opened with whatever application (if any) its extension is associated.

When another _operation_ is given, it must be a "command verb" that specifies what should be done with the file. Common verbs documented by Microsoft are `'open'`, `'print'` and `'edit'` (to be used on files) as well as `'explore'` and `'find'` (to be used on directories).

在启动某个应用程序时， _arguments_ 将作为一个字符串传入。若是打开某个文档，此参数可能没什么效果。

默认工作目录是继承而来的，但可以通过 _cwd_ 参数进行覆盖。且应为绝对路径。相对路径 _path_ 将据此参数进行解析。

Use _show_cmd_ to override the default window style. Whether this has any effect will depend on the application being launched. Values are integers as supported by the Win32 `ShellExecute()` function.

在关联的应用程序启动后，`startfile()` 就会立即返回。 没有提供等待应用程序关闭的选项，也没有办法获得应用程序的退出状态。 _path_ 形参是基于当前目录或 _cwd_ 的相对路径。 如果要使用绝对路径，请确保第一个字符不为斜杠 (`'/'`) 。 请用 [`pathlib`](pathlib.md#module-pathlib "pathlib: Object-oriented filesystem paths") 或 [`os.path.normpath()`](os.path.md#os.path.normpath "os.path.normpath") 函数来保证路径已按照 Win32 的要求进行了正确的编码。

To reduce interpreter startup overhead, the Win32 `ShellExecute()` function is not resolved until this function is first called. If the function cannot be resolved, [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") will be raised.

引发一个 [审计事件](sys.md#auditing) `os.startfile`，附带参数 `path`、`operation`。

引发一条 [审计事件](sys.md#auditing) `os.startfile/2`，附带参数为 `path`、`operation`、`arguments`、`cwd`、`show_cmd`。

[可用性](intro.md#availability): Windows。

在 3.10 版本发生变更: 加入了 _arguments_ 、 _cwd_ 和 _show_cmd_ 参数，以及 `os.startfile/2` 审计事件。

os.system( _command_ )¶
~~~
    

~~~
在子外壳程序中执行此命令（一个字符串）。 这是通过调用标准 C 函数 `system()` 来实现的，并受到同样的限制。 对 [`sys.stdin`](sys.md#sys.stdin "sys.stdin") 的更改等不会反映在所执行命令的环境中。 如果 _command_ 生成了任何输出，它将被发送到解释器的标准输出流。 C 标准没有指明这个 C 函数返回值的含义，因此这个 Python 函数的返回值取决于具体系统。

在 Unix 上，返回值为进程的退出状态，以针对 `wait()` 而指定的格式进行编码。

在 Windows 上，返回值是运行 _command_ 后系统 Shell 返回的值。该 Shell 由 Windows 环境变量 `COMSPEC`: 给出：通常是 **cmd.exe** ，它会返回命令的退出状态。在使用非原生 Shell 的系统上，请查阅 Shell 的文档。

[`subprocess`](subprocess.md#module-subprocess "subprocess: Subprocess management.") 模块提供了更强大的工具来生成新进程并跟踪执行结果，使用该模块比使用本函数更好。参阅 [`subprocess`](subprocess.md#module-subprocess "subprocess: Subprocess management.") 文档中的 [使用 subprocess 模块替换旧函数](subprocess.md#subprocess-replacements) 部分以获取有用的帮助。

在 Unix 上，`waitstatus_to_exitcode()` 可以将返回值（即退出状态）转换为退出码。在 Windows 上，返回值就是退出码。

引发一个 [审计事件](sys.md#auditing) `os.system`，附带参数 `command`。

[可用性](intro.md#availability): Unix, Windows, 非 Emscripten, 非 WASI。

os.times()¶
~~~
    

~~~
返回当前的全局进程时间。返回值是一个有 5 个属性的对象：

  * `user` \- 用户时间

  * `system` \- 系统时间

  * `children_user` \- 所有子进程的用户时间

  * `children_system` \- 所有子进程的系统时间

  * `elapsed` \- 从过去的固定时间点起，经过的真实时间

为了向后兼容，该对象的行为也类似于五元组，按照 `user`，`system`，`children_user`，`children_system` 和 `elapsed` 顺序组成。

在 Unix 上请参阅 Unix 手册页 _[times(2)](https://manpages.debian.org/times\(2\))_ 和 [times(3)](https://man.freebsd.org/cgi/man.cgi?time\(3\)) 而在 Windows 上请参阅 [GetProcessTimes MSDN](https://docs.microsoft.com/windows/win32/api/processthreadsapi/nf-processthreadsapi-getprocesstimes)。 在 Windows 上，只有 `user` 和 `system` 是已知的；其他属性均为零。

[可用性](intro.md#availability): Unix, Windows。

在 3.3 版本发生变更: 返回结果的类型由元组变成一个类似元组的对象，同时具有命名的属性。

os.wait()¶
~~~
    

~~~
等待子进程执行完毕，返回一个元组，包含其 pid 和退出状态指示：一个 16 位数字，其低字节是终止该进程的信号编号，高字节是退出状态码（信号编号为零的情况下），如果生成了核心文件，则低字节的高位会置位。

如果不存在可被等待的子进程，则将引发 [`ChildProcessError`](exceptions.md#ChildProcessError "ChildProcessError")。

可以使用 `waitstatus_to_exitcode()` 来将退出状态转换为退出码。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

参见

下面列出的其他 `wait*()` 函数可被用于等待特定子进程完成并具有更多的选项。 `waitpid()` 是其中唯一在 Windows 上也可用的。

os.waitid( _idtype_ , _id_ , _options_ , _/_ )¶
~~~
    

~~~
等待一个子进程完成。

_idtype_ 可以为 `P_PID`, `P_PGID`, `P_ALL` 或 (在 Linux 上) `P_PIDFD`。 对于 _id_ 的解读由它来决定；请参阅它们各自的说明。

_options_ 是多个旗标的 OR 组合。 要求至少有 `WEXITED`, `WSTOPPED` 或 `WCONTINUED` 中的一个；`WNOHANG` 和 `WNOWAIT` 是附加的可选旗标。

The return value is an object representing the data contained in the `siginfo_t` structure with the following attributes:

  * `si_pid` (进程 ID)

  * `si_uid` (子进程的实际用户 ID)

  * `si_signo` (always [`SIGCHLD`](signal.md#signal.SIGCHLD "signal.SIGCHLD"))

  * `si_status` (退出状态或信号编号，具体取决于 `si_code`)

  * `si_code` (可能的值参见 `CLD_EXITED`)

如果指定了 `WNOHANG` 而在所请求的状态中没有匹配的子进程，则将返回 `None`。 在其他情况下，如果没有匹配的子进程可被等待，则将引发 [`ChildProcessError`](exceptions.md#ChildProcessError "ChildProcessError")。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入.

os.waitpid( _pid_ , _options_ , _/_ )¶
~~~
    

~~~
本函数的细节在 Unix 和 Windows 上有不同之处。

在 Unix 上：等待进程号为 _pid_ 的子进程执行完毕，返回一个元组，内含其进程 ID 和退出状态指示（编码与 `wait()` 相同）。调用的语义受整数 _options_ 的影响，常规操作下该值应为 `0`。

如果 _pid_ 大于 `0`，则 `waitpid()` 会获取该指定进程的状态信息。如果 _pid_ 为 `0`，则获取当前进程所在进程组中的所有子进程的状态。如果 _pid_ 为 `-1`，则获取当前进程的子进程状态。如果 _pid_ 小于 `-1`，则获取进程组 `-pid` （ _pid_ 的绝对值）中所有进程的状态。

_options_ 是多个旗标的 OR 组合。 如果它包含了 `WNOHANG` 并且在所请求的状态中没有匹配的子进程，则将返回 `(0, 0)`。 在其他情况下，如果没有匹配的子进程可以被等待，则将引发 [`ChildProcessError`](exceptions.md#ChildProcessError "ChildProcessError")。 其他的可用选项还有 `WUNTRACED` 和 `WCONTINUED`。

在 Windows 上：等待句柄为 _pid_ 的进程执行完毕，返回一个元组，内含 _pid_ 以及左移 8 位后的退出状态码（移位简化了跨平台使用本函数）。小于或等于 `0` 的 _pid_ 在 Windows 上没有特殊含义，且会抛出异常。整数值 _options_ 无效。 _pid_ 可以指向任何 ID 已知的进程，不一定是子进程。调用 `spawn*` 函数时传入 `P_NOWAIT` 将返回合适的进程句柄。

可以使用 `waitstatus_to_exitcode()` 来将退出状态转换为退出码。

[可用性](intro.md#availability): Unix, Windows, 非 Emscripten, 非 WASI。

在 3.5 版本发生变更: 如果系统调用被中断，但信号处理程序没有触发异常，此函数现在会重试系统调用，而不是触发 [`InterruptedError`](exceptions.md#InterruptedError "InterruptedError") 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

os.wait3( _options_ )¶
~~~
    

~~~
与 `waitpid()` 类似，区别在于没有给出进程 id 参数并且返回一个 3 元组，其中包括子进程的 id、退出状态指示以及资源使用信息。 请参阅 [`resource.getrusage()`](resource.md#resource.getrusage "resource.getrusage") 了解有关资源使用信息的详情。 _options_ 参数与提供给 `waitpid()` 和 `wait4()` 的相同。

可以使用 `waitstatus_to_exitcode()` 来将退出状态转换为退出码。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.wait4( _pid_ , _options_ )¶
~~~
    

~~~
与 `waitpid()` 类似，区别在于它是返回一个 3 元组，其中包括子进程的 id、退出状态指示以及资源使用信息。 请参阅 [`resource.getrusage()`](resource.md#resource.getrusage "resource.getrusage") 了解有关资源使用信息的详情。 `wait4()` 的参数与提供给 `waitpid()` 的相同。

可以使用 `waitstatus_to_exitcode()` 来将退出状态转换为退出码。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.P_PID¶

os.P_PGID¶

os.P_ALL¶

os.P_PIDFD¶
~~~
    

~~~
这些是 `waitid()` 中 _idtype_ 可取的值。 它们会影响 _id_ 的解读方式:

  * `P_PID` \- 等待 PID 为 _id_ 的子进程。

  * `P_PGID` \- 等待进程组 ID 为 _id_ 的任何子进程。

  * `P_ALL` \- 等待任何子进程； _id_ 会被忽略。

  * `P_PIDFD` \- 等待以文件描述符 _id_ 作为标识的子进程（使用a process file descriptor created with `pidfd_open()` 创建的进程文件描述符）。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

备注

`P_PIDFD` 仅在 Linux >= 5.4 时可用。

在 3.3 版本加入.

在 3.9 版本加入: `P_PIDFD` 常量。

os.WCONTINUED¶
~~~
    

~~~
如果子进程自它们上次被报告之后从作业控制停止位置继续执行，则 `waitpid()`, `wait3()`, `wait4()` 和 `waitid()` 的这个 _选项_ 旗标将导致子进程被报告。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.WEXITED¶
~~~
    

~~~
`waitid()` 的这个 _选项_ 旗标将导致已终结的子进程被报告。

其他 `wait*` 函数总是会报告已终结的子进程，所以此选项对它们不可用。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入.

os.WSTOPPED¶
~~~
    

~~~
这个 `waitid()` 的 _选项_ 旗标将导致被信号发送所停止的子进程被报告。

这个选项对于其他 `wait*` 函数不可用。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入.

os.WUNTRACED¶
~~~
    

~~~
如果子进程自它们上次被停止之后再次被停止但它们的当前状态还未被报告，则 `waitpid()`, `wait3()` 和 `wait4()` 的这个 _选项_ 旗标也将导致子进程被报告。

这个选项对于 `waitid()` 不可用。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.WNOHANG¶
~~~
    

~~~
如果没有任何子进程状态是立即可用的，则这个 _选项_ 旗标将导致 `waitpid()`, `wait3()`, `wait4()` 和 `waitid()` 立即返回。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.WNOWAIT¶
~~~
    

~~~
这个 _选项_ 旗标将导致 `waitid()` 以可等待的状态离开子进程，这样后续的 `wait*()` 调用可被用来再次获取子进程状态信息。

这个选项对于其他 `wait*` 函数不可用。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.CLD_EXITED¶

os.CLD_KILLED¶

os.CLD_DUMPED¶

os.CLD_TRAPPED¶

os.CLD_STOPPED¶

os.CLD_CONTINUED¶
~~~
    

~~~
这些是由 `waitid()` 所返回的结果中 `si_code` 可能的取值。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

在 3.3 版本加入.

在 3.9 版本发生变更: 添加了 `CLD_KILLED` 和 `CLD_STOPPED` 值。

os.waitstatus_to_exitcode( _status_ )¶
~~~
    

~~~
将等待状态转换为退出码。

在 Unix 上：

  * 如果进程正常退出（当 `WIFEXITED(status)` 为真值），则返回进程退出状态 (返回 `WEXITSTATUS(status)`): 结果值大于等于 0。

  * 如果进程被信号终止（当 `WIFSIGNALED(status)` 为真值），则返回 `-signum` 其中 _signum_ 为导致进程终止的信号数值 (返回 `-WTERMSIG(status)`): 结果值小于 0。

  * 否则将抛出 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。

在 Windows 上，返回 _status_ 右移 8 位的结果。

在 Unix 上，如果进程正被追踪或 `waitpid()` 附带 `WUNTRACED` 选项被调用，则调用者必须先检查 `WIFSTOPPED(status)` 是否为真值。 如果 `WIFSTOPPED(status)` 为真值则此函数不可被调用。

参见

`WIFEXITED()`, `WEXITSTATUS()`, `WIFSIGNALED()`, `WTERMSIG()`, `WIFSTOPPED()`, `WSTOPSIG()` 函数。

[可用性](intro.md#availability): Unix, Windows, 非 Emscripten, 非 WASI。

在 3.9 版本加入.

下列函数采用进程状态码作为参数，状态码由 `system()`、`wait()` 或 `waitpid()` 返回。它们可用于确定进程上发生的操作。

os.WCOREDUMP( _status_ , _/_ )¶
~~~
    

~~~
如果为该进程生成了核心转储，返回 `True`，否则返回 `False`。

此函数应当仅在 `WIFSIGNALED()` 为真值时使用。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.WIFCONTINUED( _status_ )¶
~~~
    

~~~
Return `True` if a stopped child has been resumed by delivery of [`SIGCONT`](signal.md#signal.SIGCONT "signal.SIGCONT") (if the process has been continued from a job control stop), otherwise return `False`.

参见 `WCONTINUED` 选项。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.WIFSTOPPED( _status_ )¶
~~~
    

~~~
如果进程是通过传送一个信号来停止的则返回 `True`，否则返回 `False`。

`WIFSTOPPED()` 只有在当 `waitpid()` 调用是通过使用 `WUNTRACED` 选项来完成或者当该进程正被追踪时 (参见 _[ptrace(2)](https://manpages.debian.org/ptrace\(2\))_ ) 才返回 `True`。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.WIFSIGNALED( _status_ )¶
~~~
    

~~~
如果进程是通过一个信号来终止的则返回 `True` ，否则返回 `False`。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.WIFEXITED( _status_ )¶
~~~
    

~~~
如果进程正常终止退出则返回 `True`，也就是说通过调用 `exit()` 或 `_exit()`，或者通过从 `main()` 返回；在其他情况下则返回 `False`。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.WEXITSTATUS( _status_ )¶
~~~
    

~~~
返回进程退出状态。

此函数应当仅在 `WIFEXITED()` 为真值时使用。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.WSTOPSIG( _status_ )¶
~~~
    

~~~
返回导致进程停止的信号。

此函数应当仅在 `WIFSTOPPED()` 为真值时使用。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

os.WTERMSIG( _status_ )¶
~~~
    

~~~
返回导致进程终止的信号的编号。

此函数应当仅在 `WIFSIGNALED()` 为真值时使用。

[可用性](intro.md#availability): Unix, 非 Emscripten, 非 WASI。

## 调度器接口¶

这些函数控制操作系统如何为进程分配 CPU 时间。 它们仅在某些 Unix 平台上可用。 更多细节信息请查阅你所用 Unix 的指南页面。

在 3.3 版本加入.

以下调度策略如果被操作系统支持就会对外公开。

os.SCHED_OTHER¶
~~~
    

~~~
默认调度策略。

os.SCHED_BATCH¶
~~~
    

~~~
用于 CPU 密集型进程的调度策略，它会尽量为计算机中的其余任务保留交互性。

os.SCHED_IDLE¶
~~~
    

~~~
用于极低优先级的后台任务的调度策略。

os.SCHED_SPORADIC¶
~~~
    

~~~
用于偶发型服务程序的调度策略。

os.SCHED_FIFO¶
~~~
    

~~~
先进先出的调度策略。

os.SCHED_RR¶
~~~
    

~~~
循环式的调度策略。

os.SCHED_RESET_ON_FORK¶
~~~
    

~~~
此旗标可与任何其他调度策略进行 OR 运算。 当带有此旗标的进程设置分叉时，其子进程的调度策略和优先级会被重置为默认值。

_class _os.sched_param( _sched_priority_ )¶
~~~
    

~~~
这个类表示在 `sched_setparam()`, `sched_setscheduler()` 和 `sched_getparam()` 中使用的可修改调度形参。 它属于不可变对象。

目前它只有一个可能的形参：

sched_priority¶
~~~
    

~~~
一个调度策略的调度优先级。

os.sched_get_priority_min( _policy_ )¶
~~~
    

~~~
获取 _policy_ 的最低优先级数值。 _policy_ 是以上调度策略常量之一。

os.sched_get_priority_max( _policy_ )¶
~~~
    

~~~
获取 _policy_ 的最高优先级数值。 _policy_ 是以上调度策略常量之一。

os.sched_setscheduler( _pid_ , _policy_ , _param_ , _/_ )¶
~~~
    

~~~
设置 PID 为 _pid_ 的进程的调度策略。 _pid_ 为 0 指的是调用本方法的进程。 _policy_ 是以上调度策略常量之一。 _param_ 是一个 `sched_param` 实例。

os.sched_getscheduler( _pid_ , _/_ )¶
~~~
    

~~~
返回 PID 为 _pid_ 的进程的调度策略。 _pid_ 为 0 指的是调用本方法的进程。返回的结果是以上调度策略常量之一。

os.sched_setparam( _pid_ , _param_ , _/_ )¶
~~~
    

~~~
设置 PID 为 _pid_ 的进程的调度参数。 _pid_ 为 0 表示调用方过程。 _param_ 是一个 `sched_param` 实例。

os.sched_getparam( _pid_ , _/_ )¶
~~~
    

~~~
返回 PID 为 _pid_ 的进程的调度参数为一个 `sched_param` 实例。 _pid_ 为 0 指的是调用本方法的进程。

os.sched_rr_get_interval( _pid_ , _/_ )¶
~~~
    

~~~
返回 PID 为 _pid_ 的进程在时间片轮转调度下的时间片长度（单位为秒）。 _pid_ 为 0 指的是调用本方法的进程。

os.sched_yield()¶
~~~
    

~~~
自愿放弃 CPU。

os.sched_setaffinity( _pid_ , _mask_ , _/_ )¶
~~~
    

~~~
将 PID 为 _pid_ 的进程（为零则为当前进程）限制到一组 CPU 上。 _mask_ 是整数的可迭代对象，表示应将进程限制在其中的一组 CPU。

os.sched_getaffinity( _pid_ , _/_ )¶
~~~
    

~~~
返回 PID 为 _pid_ 的进程（为零则为当前进程）被限制到的那一组 CPU。

## 其他系统信息¶

os.confstr( _name_ , _/_ )¶
~~~
    

~~~
返回字符串格式的系统配置信息。 _name_ 指定要查找的配置名称，它可以是字符串，是一个系统已定义的名称，这些名称定义在不同标准（POSIX，Unix 95，Unix 98 等）中。一些平台还定义了额外的其他名称。当前操作系统已定义的名称在 `confstr_names` 字典的键中给出。对于未包含在该映射中的配置名称，也可以传递一个整数作为 _name_ 。

如果 _name_ 指定的配置值未定义，返回 `None`。

如果 _name_ 是一个字符串且不是已定义的名称，将抛出 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。如果当前系统不支持 _name_ 指定的配置名称，即使该名称存在于 `confstr_names`，也会抛出 [`OSError`](exceptions.md#OSError "OSError") 异常，错误码为 [`errno.EINVAL`](errno.md#errno.EINVAL "errno.EINVAL")。

[可用性](intro.md#availability): Unix。

os.confstr_names¶
~~~
    

~~~
字典，表示映射关系，为 `confstr()` 可接受名称与操作系统为这些名称定义的整数值之间的映射。这可用于判断系统已定义了哪些名称。

[可用性](intro.md#availability): Unix。

os.cpu_count()¶
~~~
    

~~~
返回系统的 CPU 数量。不确定则返回 `None`。

该数量不同于当前进程可以使用的CPU数量。可用的CPU数量可以由 `len(os.sched_getaffinity(0))` 方法获得。

在 3.4 版本加入.

os.getloadavg()¶
~~~
    

~~~
返回系统运行队列中最近 1、5 和 15 分钟内的平均进程数。无法获得平均负载则抛出 [`OSError`](exceptions.md#OSError "OSError") 异常。

[可用性](intro.md#availability): Unix。

os.sysconf( _name_ , _/_ )¶
~~~
    

~~~
返回整数格式的系统配置信息。如果 _name_ 指定的配置值未定义，返回 `-1`。对 `confstr()` 的 _name_ 参数的注释在此处也适用。当前已知的配置名称在 `sysconf_names` 字典中提供。

[可用性](intro.md#availability): Unix。

os.sysconf_names¶
~~~
    

~~~
字典，表示映射关系，为 `sysconf()` 可接受名称与操作系统为这些名称定义的整数值之间的映射。这可用于判断系统已定义了哪些名称。

[可用性](intro.md#availability): Unix。

在 3.11 版本发生变更: 添加 `'SC_MINSIGSTKSZ'` 名称。

以下数据值用于支持对路径本身的操作。所有平台都有定义。

对路径的高级操作在 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 模块中定义。

os.curdir¶
~~~
    

~~~
操作系统用来表示当前目录的常量字符串。在 Windows 和 POSIX 上是 `'.'`。在 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 中也可用。

os.pardir¶
~~~
    

~~~
操作系统用来表示父目录的常量字符串。在 Windows 和 POSIX 上是 `'..'`。在 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 中也可用。

os.sep¶
~~~
    

~~~
操作系统用来分隔路径不同部分的字符。在 POSIX 上是 `'/'`，在 Windows 上是是 `'\\'`。注意，仅了解它不足以能解析或连接路径，请使用 [`os.path.split()`](os.path.md#os.path.split "os.path.split") 和 [`os.path.join()`](os.path.md#os.path.join "os.path.join")，但它有时是有用的。在 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 中也可用。

os.altsep¶
~~~
    

~~~
操作系统用来分隔路径不同部分的替代字符。如果仅存在一个分隔符，则为 `None`。在 `sep` 是反斜杠的 Windows 系统上，该值被设为 `'/'`。在 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 中也可用。

os.extsep¶
~~~
    

~~~
分隔基本文件名与扩展名的字符，如 `os.py` 中的 `'.'`。在 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 中也可用。

os.pathsep¶
~~~
    

~~~
操作系统通常用于分隔搜索路径（如 `PATH`）中不同部分的字符，如 POSIX 上是 `':'`，Windows 上是 `';'`。在 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 中也可用。

os.defpath¶
~~~
    

~~~
在环境变量没有 `'PATH'` 键的情况下，`exec*p*` and `spawn*p*` 使用的默认搜索路径。在 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 中也可用。

os.linesep¶
~~~
    

~~~
当前平台用于分隔（或终止）行的字符串。它可以是单个字符，如 POSIX 上是 `'\n'`，也可以是多个字符，如 Windows 上是 `'\r\n'`。在写入以文本模式（默认模式）打开的文件时，请不要使用 _os.linesep_ 作为行终止符，请在所有平台上都使用一个 `'\n'` 代替。

os.devnull¶
~~~
    

~~~
空设备的文件路径。如 POSIX 上为 `'/dev/null'`，Windows 上为 `'nul'`。在 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 中也可用。

os.RTLD_LAZY¶

os.RTLD_NOW¶

os.RTLD_GLOBAL¶

os.RTLD_LOCAL¶

os.RTLD_NODELETE¶

os.RTLD_NOLOAD¶

os.RTLD_DEEPBIND¶
~~~
    

~~~
[`setdlopenflags()`](sys.md#sys.setdlopenflags "sys.setdlopenflags") 和 [`getdlopenflags()`](sys.md#sys.getdlopenflags "sys.getdlopenflags") 函数所使用的标志。请参阅 Unix 手册页 _[dlopen(3)](https://manpages.debian.org/dlopen\(3\))_ 获取不同标志的含义。

在 3.3 版本加入.

## 随机数¶

os.getrandom( _size_ , _flags =0_)¶
~~~
    

~~~
获得最多为 _size_ 的随机字节。本函数返回的字节数可能少于请求的字节数。

这些字节可用于为用户空间的随机数生成器提供种子，或用于加密目的。

`getrandom()` 依赖于从设备驱动程序和其他环境噪声源收集的熵。不必要地读取大量数据将对使用 `/dev/random` 和 `/dev/urandom` 设备的其他用户产生负面影响。

The flags argument is a bit mask that can contain zero or more of the following values ORed together: `os.GRND_RANDOM` and `GRND_NONBLOCK`.

另请参阅 [Linux getrandom() 手册页](https://man7.org/linux/man-pages/man2/getrandom.2.md)。

[可用性](intro.md#availability): Linux >= 3.17。

在 3.6 版本加入.

os.urandom( _size_ , _/_ )¶
~~~
    

~~~
返回大小为 _size_ 的字节串，它是适合加密使用的随机字节。

本函数从系统指定的随机源获取随机字节。对于加密应用程序，返回的数据应有足够的不可预测性，尽管其确切的品质取决于操作系统的实现。

在 Linux 上，如果 `getrandom()` 系统调用可用，它将以阻塞模式使用：阻塞直到系统的 urandom 熵池初始化完毕（内核收集了 128 位熵）。原理请参阅 [**PEP 524**](https://peps.python.org/pep-0524/)。在 Linux 上，`getrandom()` 可以以非阻塞模式（使用 `GRND_NONBLOCK` 标志）获取随机字节，或者轮询直到系统的 urandom 熵池初始化完毕。

在类 Unix 系统上，随机字节是从 `/dev/urandom` 设备读取的。如果 `/dev/urandom` 设备不可用或不可读，则抛出 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") 异常。

在 Windows 上，它将使用 `BCryptGenRandom()`。

参见

[`secrets`](secrets.md#module-secrets "secrets: Generate secure random numbers for managing secrets.") 模块提供了更高级的功能。所在平台会提供随机数生成器，有关其易于使用的接口，请参阅 [`random.SystemRandom`](random.md#random.SystemRandom "random.SystemRandom")。

在 3.6.0 版本发生变更: 在 Linux 上，`getrandom()` 现在以阻塞模式使用，以提高安全性。

在 3.5.2 版本发生变更: 在 Linux 上，如果 `getrandom()` 系统调用阻塞（urandom 熵池尚未初始化完毕），则退回一步读取 `/dev/urandom`。

在 3.5 版本发生变更: 在 Linux 3.17 和更高版本上，现在使用 `getrandom()` 系统调用（如果可用）。在 OpenBSD 5.6 和更高版本上，现在使用 `getentropy()` C 函数。这些函数避免了使用内部文件描述符。

在 3.11 版本发生变更: 在 Windows 上，将使用 `BCryptGenRandom()` 而不是已被弃用的 `CryptGenRandom()`。

os.GRND_NONBLOCK¶
~~~
    

~~~
默认情况下，从 `/dev/random` 读取时，如果没有可用的随机字节，则 `getrandom()` 会阻塞；从 `/dev/urandom` 读取时，如果熵池尚未初始化，则会阻塞。

如果设置了 `GRND_NONBLOCK` 标志，则这些情况下 `getrandom()` 不会阻塞，而是立即抛出 [`BlockingIOError`](exceptions.md#BlockingIOError "BlockingIOError") 异常。

在 3.6 版本加入.

os.GRND_RANDOM¶
~~~
    

~~~
