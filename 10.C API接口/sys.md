# 操作系统实用工具¶

[PyObject](structures.md#c.PyObject "PyObject") *PyOS_FSPath([PyObject](structures.md#c.PyObject "PyObject") *path)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.6._

返回 _path_ 在文件系统中的表示形式。 如果该对象是一个 [`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 对象，则返回一个新的 [strong reference](../glossary.md#term-strong-reference)。 如果对象实现了 [`os.PathLike`](os.md#os.PathLike "os.PathLike") 接口，则只要它是一个 [`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 对象就将返回 [`__fspath__()`](os.md#os.PathLike.__fspath__ "os.PathLike.__fspath__")。 在其他情况下将引发 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError") 并返回 `NULL`。

在 3.6 版本加入.

int Py_FdIsInteractive(FILE *fp, const char *filename)¶  

    

~~~
如果名称为 _filename_ 的标准Return true (nonzero) if the standard I/O 文件 _fp_ 被确认为可交互的则返回真（非零）值。 所有 `isatty(fileno(fp))` 为真值的文件都属于这种情况。 如果 [`PyConfig.interactive`](init_config.md#c.PyConfig.interactive "PyConfig.interactive") 为非零值，此函数在 _filename_ 指针为 `NULL` 或者其名称等于字符串 `'<stdin>'` 或 `'???'` 之一时也将返回真值。

此函数不可在 Python 被初始化之前调用。

void PyOS_BeforeFork()¶  

    _Part of the[ Stable ABI](stable.md#stable) on platforms with fork() since version 3.7._

在进程分叉之前准备某些内部状态的函数。 此函数应当在调用 `fork()` 或者任何类似的克隆当前进程的函数之前被调用。 只适用于定义了 `fork()` 的系统。

警告

C `fork()` 调用应当只在 ["main" 线程](init.md#fork-and-threads) (位于 ["main" 解释器](init.md#sub-interpreter-support)) 中进行。 对于 `PyOS_BeforeFork()` 来说也是如此。

在 3.7 版本加入.

void PyOS_AfterFork_Parent()¶  

    _Part of the[ Stable ABI](stable.md#stable) on platforms with fork() since version 3.7._

在进程分叉之后更新某些内部状态的函数。 此函数应当在调用 `fork()` 或任何类似的克隆当前进程的函数之后被调用，无论进程克隆是否成功。 只适用于定义了 `fork()` 的系统。

警告

C `fork()` 调用应当只在 ["main" 线程](init.md#fork-and-threads) (位于 ["main" 解释器](init.md#sub-interpreter-support)) 中进行。 对于 `PyOS_AfterFork_Parent()` 来说也是如此。

在 3.7 版本加入.

void PyOS_AfterFork_Child()¶  

    _Part of the[ Stable ABI](stable.md#stable) on platforms with fork() since version 3.7._

在进程分叉之后更新内部解释器状态的函数。 此函数必须在调用 `fork()` 或任何类似的克隆当前进程的函数之后在子进程中被调用，如果该进程有机会回调到 Python 解释器的话。 只适用于定义了 `fork()` 的系统。

警告

C `fork()` 调用应当只在 ["main" 线程](init.md#fork-and-threads) (位于 ["main" 解释器](init.md#sub-interpreter-support)) 中进行。 对于 `PyOS_AfterFork_Child()` 来说也是如此。

在 3.7 版本加入.

参见

[`os.register_at_fork()`](../library/os.md#os.register_at_fork "os.register_at_fork") 允许注册可被 `PyOS_BeforeFork()`, `PyOS_AfterFork_Parent()` 和 `PyOS_AfterFork_Child()` 调用的自定义 Python 函数。

void PyOS_AfterFork()¶  

    _Part of the[ Stable ABI](stable.md#stable) on platforms with fork()._

在进程分叉之后更新某些内部状态的函数；如果要继续使用 Python 解释器则此函数应当在新进程中被调用。 如果已将一个新的可执行文件载入到新进程中，则不需要调用此函数。

自 3.7 版本弃用: 此函数已被 `PyOS_AfterFork_Child()` 取代。

int PyOS_CheckStack()¶  

    _Part of the[ Stable ABI](stable.md#stable) on platforms with USE_STACKCHECK since version 3.7._

当解释器耗尽栈空间时返回真值。 这是一个可靠的检查，但仅在已定义 `USE_STACKCHECK` 时可用（目前是在使用 Microsoft Visual C++ 编译器的特定 Windows 版本上）。 `USE_STACKCHECK` 将被自动定义；你绝不应该在你自己的代码中改变此定义。will be defined automatically; you should never change the definition in your own code.

PyOS_sighandler_t PyOS_getsig(int i)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回信号 _i_ 的当前信号处理句柄。 这是一个对 `sigaction()` 或 `signal()` 的简单包装器。 请勿直接调用这两个函数！ `PyOS_sighandler_t` 是对应于 void (*)(int) 的类型定义别名。

PyOS_sighandler_t PyOS_setsig(int i, PyOS_sighandler_t h)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将信号 _i_ 的信号处理句柄设为 _h_ ；返回旧的信号处理句柄。 这是一个对 `sigaction()` 或 `signal()` 的简单包装器。 请勿直接调用这两个函数！ `PyOS_sighandler_t` 是对应于 void (*)(int) 的类型定义别名。

wchar_t *Py_DecodeLocale(const char *arg, size_t *size)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

警告

此函数不应当被直接调用：请使用 [`PyConfig`](init_config.md#c.PyConfig "PyConfig") API 以及可确保 [对 Python 进行预初始化](init_config.md#c-preinit) 的 [`PyConfig_SetBytesString()`](init_config.md#c.PyConfig_SetBytesString "PyConfig_SetBytesString") 函数。

此函数不可在This function must not be called before [对 Python 进行预初始化](init_config.md#c-preinit) 之前被调用以便正确地配置 LC_CTYPE 语言区域：请参阅 [`Py_PreInitialize()`](init_config.md#c.Py_PreInitialize "Py_PreInitialize") 函数。

使用 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 来解码一个字节串。 如果错误处理句柄为 [surrogateescape 错误处理句柄](../library/codecs.md#surrogateescape)，则不可解码的字节将被解码为 U+DC80..U+DCFF 范围内的字符；而如果一个字节序列可被解码为代理字符，则其中的字节会使用 surrogateescape 错误处理句柄来转义而不是解码它们。

返回一个指向新分配的由宽字符组成的字符串的指针，使用 [`PyMem_RawFree()`](memory.md#c.PyMem_RawFree "PyMem_RawFree") 来释放内存。 如果 size 不为 `NULL`，则将排除了 null 字符的宽字符数量写入到 `*size`

在解码错误或内存分配错误时返回 `NULL`。 如果 _size_ 不为 `NULL`，则 `*size` 将在内存错误时设为 `(size_t)-1` 或在解码错误时设为 `(size_t)-2`。

[filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 是由 [`PyConfig_Read()`](init_config.md#c.PyConfig_Read "PyConfig_Read") 来选择的: 参见 [`PyConfig`](init_config.md#c.PyConfig "PyConfig") 的 [`filesystem_encoding`](init_config.md#c.PyConfig.filesystem_encoding "PyConfig.filesystem_encoding") 和 [`filesystem_errors`](init_config.md#c.PyConfig.filesystem_errors "PyConfig.filesystem_errors") 等成员。

解码错误绝对不应当发生，除非 C 库有程序缺陷。

请使用 `Py_EncodeLocale()` 函数来将字符串编码回字节串。

参见

[`PyUnicode_DecodeFSDefaultAndSize()`](unicode.md#c.PyUnicode_DecodeFSDefaultAndSize "PyUnicode_DecodeFSDefaultAndSize") 和 [`PyUnicode_DecodeLocaleAndSize()`](unicode.md#c.PyUnicode_DecodeLocaleAndSize "PyUnicode_DecodeLocaleAndSize") 函数。

在 3.5 版本加入.

在 3.7 版本发生变更: 现在此函数在 [Python UTF-8 模式](../library/os.md#utf8-mode) 下将使用 UTF-8 编码格式。

在 3.8 版本发生变更: 现在如果在 Windows 上 [`PyPreConfig.legacy_windows_fs_encoding`](init_config.md#c.PyPreConfig.legacy_windows_fs_encoding "PyPreConfig.legacy_windows_fs_encoding") 为零则此函数将使用 UTF-8 编码格式；

char *Py_EncodeLocale(const wchar_t *text, size_t *error_pos)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

将一个由宽字符组成的字符串编码为 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler)。 如果错误处理句柄为 [surrogateescape 错误处理句柄](../library/codecs.md#surrogateescape)，则在 U+DC80..U+DCFF 范围内的代理字符会被转换为字节值 0x80..0xFF。

返回一个指向新分配的字节串的指针，使用 [`PyMem_Free()`](memory.md#c.PyMem_Free "PyMem_Free") 来释放内存。 当发生编码错误或内存分配错误时返回 `NULL`。

如果 error_pos 不为 `NULL`，则成功时会将 `*error_pos` 设为 `(size_t)-1`，或是在发生编码错误时设为无效字符的索引号。

[filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 是由 [`PyConfig_Read()`](init_config.md#c.PyConfig_Read "PyConfig_Read") 来选择的: 参见 [`PyConfig`](init_config.md#c.PyConfig "PyConfig") 的 [`filesystem_encoding`](init_config.md#c.PyConfig.filesystem_encoding "PyConfig.filesystem_encoding") 和 [`filesystem_errors`](init_config.md#c.PyConfig.filesystem_errors "PyConfig.filesystem_errors") 等成员。

请使用 `Py_DecodeLocale()` 函数来将字节串解码回由宽字符组成的字符串。

警告

此函数不可在This function must not be called before [对 Python 进行预初始化](init_config.md#c-preinit) 之前被调用以便正确地配置 LC_CTYPE 语言区域：请参阅 [`Py_PreInitialize()`](init_config.md#c.Py_PreInitialize "Py_PreInitialize") 函数。

参见

[`PyUnicode_EncodeFSDefault()`](unicode.md#c.PyUnicode_EncodeFSDefault "PyUnicode_EncodeFSDefault") 和 [`PyUnicode_EncodeLocale()`](unicode.md#c.PyUnicode_EncodeLocale "PyUnicode_EncodeLocale") 函数。

在 3.5 版本加入.

在 3.7 版本发生变更: 现在此函数在 [Python UTF-8 模式](../library/os.md#utf8-mode) 下将使用 UTF-8 编码格式。

在 3.8 版本发生变更: 现在如果在 Windows 上 [`PyPreConfig.legacy_windows_fs_encoding`](init_config.md#c.PyPreConfig.legacy_windows_fs_encoding "PyPreConfig.legacy_windows_fs_encoding") 为零则此函数将使用 UTF-8 编码格式。

# 系统功能¶

这些是使来自 [`sys`](../library/sys.md#module-sys "sys: Access system-specific parameters and functions.") 模块的功能可以让 C 代码访问的工具函数。 它们都可用于当前解释器线程的 [`sys`](../library/sys.md#module-sys "sys: Access system-specific parameters and functions.") 模块的字典，该字典包含在内部线程状态结构体中。

[PyObject](structures.md#c.PyObject "PyObject") *PySys_GetObject(const char *name)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回来自 [`sys`](../library/sys.md#module-sys "sys: Access system-specific parameters and functions.") 模块的对象 _name_ 或者如果它不存在则返回 `NULL`，并且不会设置异常。

int PySys_SetObject(const char *name, [PyObject](structures.md#c.PyObject "PyObject") *v)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将 [`sys`](../library/sys.md#module-sys "sys: Access system-specific parameters and functions.") 模块中的 _name_ 设为 _v_ 除非 _v_ 为 `NULL`，在此情况下 _name_ 将从 sys 模块中被删除。 成功时返回 `0`，发生错误时返回 `-1`。

void PySys_ResetWarnOptions()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将 [`sys.warnoptions`](../library/sys.md#sys.warnoptions "sys.warnoptions") 重置为空列表。 此函数可在 [`Py_Initialize()`](init.md#c.Py_Initialize "Py_Initialize") 之前被调用。

从 3.13 版起不建议使用，将在 3.15 版中移除: Clear [`sys.warnoptions`](../library/sys.md#sys.warnoptions "sys.warnoptions") and `warnings.filters` instead.

void PySys_WriteStdout(const char *format, ...)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将以 _format_ 描述的输出字符串写入到 [`sys.stdout`](../library/sys.md#sys.stdout "sys.stdout")。 不会引发任何异常，即使发生了截断（见下文）。

_format_ 应当将已格式化的输出字符串的总大小限制在 1000 字节以下 -- 超过 1000 字节后，输出字符串会被截断。 特别地，这意味着不应出现不受限制的 "%s" 格式；它们应当使用 "%.<N>s" 来限制，其中 <N> 是一个经计算使得 <N> 与其他已格式化文本的最大尺寸之和不会超过 1000 字节的十进制数字。 还要注意 "%f"，它可能为非常大的数字打印出数以百计的数位。

如果发生了错误，[`sys.stdout`](../library/sys.md#sys.stdout "sys.stdout") 会被清空，已格式化的消息将被写入到真正的 (C 层级) _stdout_ 。

void PySys_WriteStderr(const char *format, ...)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

类似 `PySys_WriteStdout()`，但改为写入到 [`sys.stderr`](../library/sys.md#sys.stderr "sys.stderr") 或 _stderr_ 。

void PySys_FormatStdout(const char *format, ...)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

类似 PySys_WriteStdout() 的函数将会使用 [`PyUnicode_FromFormatV()`](unicode.md#c.PyUnicode_FromFormatV "PyUnicode_FromFormatV") 来格式化消息并且不会将消息截短至任意长度。

在 3.2 版本加入.

void PySys_FormatStderr(const char *format, ...)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

类似 `PySys_FormatStdout()`，但改为写入到 [`sys.stderr`](../library/sys.md#sys.stderr "sys.stderr") 或 _stderr_ 。

在 3.2 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PySys_GetXOptions()¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

返回当前 [`-X`](../using/cmdline.md#cmdoption-X) 选项的字典，类似于 [`sys._xoptions`](../library/sys.md#sys._xoptions "sys._xoptions")。 发生错误时，将返回 `NULL` 并设置一个异常。

在 3.2 版本加入.

int PySys_Audit(const char *event, const char *format, ...)¶  
~~~
    

~~~
引发一个审计事件并附带任何激活的钩子。 成功时返回零值或在失败时返回非零值并设置一个异常。

如果已添加了任何钩子，则将使用 _format_ 和其他参数来构造一个用入传入的元组。 除 `N` 以外，在 [`Py_BuildValue()`](arg.md#c.Py_BuildValue "Py_BuildValue") 中使用的格式字符均可使用。 如果构建的值不是一个元组，它将被添加到一个单元素元组中。 （格式选项 `N` 会消耗一个引用，但是由于没有办法知道此函数的参数是否将被消耗，因此使用它可能导致引用泄漏。）

请注意 `#` 格式字符应当总是被当作 [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 来处理，无论是否定义了 `PY_SSIZE_T_CLEAN`。

[`sys.audit()`](../library/sys.md#sys.audit "sys.audit") 会执行与来自 Python 代码的函数相同的操作。

在 3.8 版本加入.

在 3.8.2 版本发生变更: 要求 [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 用于 `#` 格式字符。 在此之前，会引发一个不可避免的弃用警告。

int PySys_AddAuditHook(Py_AuditHookFunction hook, void *userData)¶  
~~~
    

~~~
