# 文件对象¶

这些 API 是对内置文件对象的 Python 2 C API 的最小化模拟，它过去依赖于 C 标准库的带缓冲 I/O (FILE*) 支持。 在 Python 3 中，文件和流使用新的 [`io`](../library/io.md#module-io "io: Core tools for working with streams.") 模块，该萨凡纳的操作系统的低层级无缓冲 I/O 之上定义了几个层。 下面介绍的函数是针对这些新 API 的便捷 C 包装器，主要用于解释器的内部错误报告；建议第三方代码改为访问 [`io`](../library/io.md#module-io "io: Core tools for working with streams.") API。

[PyObject](structures.md#c.PyObject "PyObject") *PyFile_FromFd(int fd, const char *name, const char *mode, int buffering, const char *encoding, const char *errors, const char *newline, int closefd)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

根据已打开文件 _fd_ 的文件描述符创建一个 Python 文件对象。 参数 _name_ , _encoding_ , _errors_ 和 _newline_ 可以为 `NULL` 表示使用默认值； _buffering_ 可以为 _-1_ 表示使用默认值。 _name_ 会被忽略仅保留用于向下兼容。 失败时返回 `NULL`。 有关参数的更全面描述，请参阅 [`io.open()`](../library/io.md#io.open "io.open") 函数的文档。

警告

由于Python流具有自己的缓冲层，因此将它们与 OS 级文件描述符混合会产生各种问题（例如数据的意外排序）。

在 3.2 版本发生变更: 忽略 _name_ 属性。

int PyObject_AsFileDescriptor([PyObject](structures.md#c.PyObject "PyObject") *p)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将与 _p_ 关联的文件描述符作为 int 返回。 如果对象是整数，则返回其值。 如果不是，则如果对象存在 [`fileno()`](../library/io.md#io.IOBase.fileno "io.IOBase.fileno") 方法则调用该方法；该方法必须返回一个整数，它将作为文件描述符的值返回。 失败时将设置异常并返回 `-1`。

[PyObject](structures.md#c.PyObject "PyObject") *PyFile_GetLine([PyObject](structures.md#c.PyObject "PyObject") *p, int n)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

等价于 `p.readline([n])` ，这个函数从对象 _p_ 中读取一行。 _p_ 可以是文件对象或具有 [`readline()`](../library/io.md#io.IOBase.readline "io.IOBase.readline") 方法的任何对象。 如果 _n_ 是 `0` ，则无论该行的长度如何，都会读取一行。 如果 _n_ 大于 `0`，则从文件中读取不超过 _n_ 个字节；可以返回行的一部分。 在这两种情况下，如果立即到达文件末尾，则返回空字符串。 但是，如果 _n_ 小于 `0` ，则无论长度如何都会读取一行，但是如果立即到达文件末尾，则引发 [`EOFError`](../library/exceptions.md#EOFError "EOFError")。

int PyFile_SetOpenCodeHook(Py_OpenCodeHookFunction handler)¶  

    

~~~
