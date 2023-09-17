# Python初始化配置¶

在 3.8 版本加入.

Python 可以使用 `Py_InitializeFromConfig()` 和 `PyConfig` 结构体来初始化。 它可以使用 `Py_PreInitialize()` 和 `PyPreConfig` 结构体来预初始化。

有两种配置方式：

  * Python 配置 可被用于构建一个定制的 Python，其行为与常规 Python 类似。 例如，环境变量和命令行参数可被用于配置 Python。

  * 隔离配置 可被用于将 Python 嵌入到应用程序。 它将 Python 与系统隔离开来。 例如，环境变量将被忽略，LC_CTYPE 语言区域设置保持不变并且不会注册任何信号处理句柄。

`Py_RunMain()` 函数可被用来编写定制的 Python 程序。

参见 [Initialization, Finalization, and Threads](init.md#initialization).

参见

[**PEP 587**](https://peps.python.org/pep-0587/) "Python 初始化配置".

## 示例¶

定制的 Python 的示例总是会以隔离模式运行:

    
    
~~~
int main(int argc, char **argv)
{
    PyStatus status;

    PyConfig config;
    PyConfig_InitPythonConfig(&config);
    config.isolated = 1;

    /* Decode command line arguments.
       Implicitly preinitialize Python (in isolated mode). */
    status = PyConfig_SetBytesArgv(&config, argc, argv);
    if (PyStatus_Exception(status)) {
        goto exception;
    }

    status = Py_InitializeFromConfig(&config);
    if (PyStatus_Exception(status)) {
        goto exception;
    }
    PyConfig_Clear(&config);

    return Py_RunMain();

exception:
    PyConfig_Clear(&config);
    if (PyStatus_IsExit(status)) {
        return status.exitcode;
    }
    /* Display the error message and exit the process with
       non-zero exit code */
    Py_ExitStatusException(status);
}
~~~

## PyWideStringList¶

type PyWideStringList¶  

    

~~~
由 `wchar_t*` 字符串组成的列表。

如果 _length_ 为非零值，则 _items_ 必须不为 `NULL` 并且所有字符串均必须不为 `NULL`。

方法

PyStatus PyWideStringList_Append(PyWideStringList *list, const wchar_t *item)¶  
~~~
    

~~~
将 _item_ 添加到 _list_ 。

Python 必须被预初始化以便调用此函数。

PyStatus PyWideStringList_Insert(PyWideStringList *list, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") index, const wchar_t *item)¶  
~~~
    

~~~
将 _item_ 插入到 _list_ 的 _index_ 位置上。

如果 _index_ 大于等于 _list_ 的长度，则将 _item_ 添加到 _list_ 。

_index_ 必须大于等于 `0`。

Python 必须被预初始化以便调用此函数。

结构体字段:

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") length¶  
~~~
    

~~~
List 长度。

wchar_t **items¶  
~~~
    

~~~
列表项目。

## PyStatus¶

type PyStatus¶  
~~~
    

~~~
存储初始函数状态：成功、错误或退出的结构体。

对于错误，它可以存储造成错误的 C 函数的名称。

结构体字段:

int exitcode¶  
~~~
    

~~~
退出码。 传给 `exit()` 的参数。

const char *err_msg¶  
~~~
    

~~~
错误信息

const char *func¶  
~~~
    

~~~
造成错误的函数的名称，可以为 `NULL`。

创建状态的函数:

PyStatus PyStatus_Ok(void)¶  
~~~
    

~~~
完成。

PyStatus PyStatus_Error(const char *err_msg)¶  
~~~
    

~~~
带消息的初始化错误。

_err_msg_ 不可为 `NULL`。

PyStatus PyStatus_NoMemory(void)¶  
~~~
    

~~~
内存分配失败（内存不足）。

PyStatus PyStatus_Exit(int exitcode)¶  
~~~
    

~~~
以指定的退出代码退出 Python。

处理状态的函数:

int PyStatus_Exception(PyStatus status)¶  
~~~
    

~~~
状态为错误还是退出？如为真值，则异常必须被处理；例如通过调用 `Py_ExitStatusException()`。

int PyStatus_IsError(PyStatus status)¶  
~~~
    

~~~
结果错误吗？

int PyStatus_IsExit(PyStatus status)¶  
~~~
    

~~~
结果是否退出？

void Py_ExitStatusException(PyStatus status)¶  
~~~
    

~~~
如果 _status_ 是一个退出码则调用 `exit(exitcode)`。如果 _status_ 是一个错误码则打印错误消息并设置一个非零退出码再退出。 必须在 `PyStatus_Exception(status)` 为非零值时才能被调用。

备注

在内部，Python 将使用设置 `PyStatus.func` 的宏，而创建状态的函数则会将 `func` 设为 `NULL`。

示例:
~~~
    
    
~~~
PyStatus alloc(void **ptr, size_t size)
{
    *ptr = PyMem_RawMalloc(size);
    if (*ptr == NULL) {
        return PyStatus_NoMemory();
    }
    return PyStatus_Ok();
}

int main(int argc, char **argv)
{
    void *ptr;
    PyStatus status = alloc(&ptr, 16);
    if (PyStatus_Exception(status)) {
        Py_ExitStatusException(status);
    }
    PyMem_Free(ptr);
    return 0;
}
~~~

## PyPreConfig¶

type PyPreConfig¶  

    

~~~
用于预初始化 Python 的结构体。

用于初始化预先配置的函数:

void PyPreConfig_InitPythonConfig(PyPreConfig *preconfig)¶  
~~~
    

~~~
通过 Python 配置 来初始化预先配置。

void PyPreConfig_InitIsolatedConfig(PyPreConfig *preconfig)¶  
~~~
    

~~~
通过 隔离配置 来初始化预先配置。

结构体字段:

int allocator¶  
~~~
    

~~~
Python 内存分配器名称:

  * `PYMEM_ALLOCATOR_NOT_SET` (`0`): 不改变内存分配器 (使用默认)。

  * `PYMEM_ALLOCATOR_DEFAULT` (`1`): [默认内存分配器](memory.md#default-memory-allocators)。

  * `PYMEM_ALLOCATOR_DEBUG` (`2`): [默认内存分配器](memory.md#default-memory-allocators) 附带 [调试钩子](memory.md#pymem-debug-hooks)。

  * `PYMEM_ALLOCATOR_MALLOC` (`3`): 使用 C 库的 `malloc()`。

  * `PYMEM_ALLOCATOR_MALLOC_DEBUG` (`4`): 强制使用 `malloc()` 附带 [调试钩子](memory.md#pymem-debug-hooks)。

  * `PYMEM_ALLOCATOR_PYMALLOC` (`5`): [Python pymalloc 内存分配器](memory.md#pymalloc)。

  * `PYMEM_ALLOCATOR_PYMALLOC_DEBUG` (`6`): [Python pymalloc 内存分配器](memory.md#pymalloc) 附带 [调试钩子](memory.md#pymem-debug-hooks)。

如果 Python 是 [`使用 --without-pymalloc 进行配置`](../using/configure.md#cmdoption-without-pymalloc) 则 `PYMEM_ALLOCATOR_PYMALLOC` 和 `PYMEM_ALLOCATOR_PYMALLOC_DEBUG` 将不被支持。

参见 [Memory Management](memory.md#memory).

默认值: `PYMEM_ALLOCATOR_NOT_SET`。

int configure_locale¶  
~~~
    

~~~
将 LC_CTYPE 语言区域设为用户选择的语言区域。

如果等于 `0`，则将 `coerce_c_locale` 和 `coerce_c_locale_warn` 的成员设为 `0`。

参见 [locale encoding](../glossary.md#term-locale-encoding)。

默认值: 在 Python 配置中为 `1`，在隔离配置中为 `0`。

int coerce_c_locale¶  
~~~
    

~~~
如果等于 `2`，强制转换 C 语言区域。

如果等于 `1`，则读取 LC_CTYPE 语言区域来确定其是否应当被强制转换。

参见 [locale encoding](../glossary.md#term-locale-encoding)。

默认值: 在 Python 配置中为 `-1`，在隔离配置中为 `0`。

int coerce_c_locale_warn¶  
~~~
    

~~~
如为非零值，则会在 C 语言区域被强制转换时发出警告。

默认值: 在 Python 配置中为 `-1`，在隔离配置中为 `0`。

int dev_mode¶  
~~~
    

~~~
[Python 开发模式](../library/devmode.md#devmode): 参见 `PyConfig.dev_mode`。

默认值: 在 Python 模式中为 `-1`，在隔离模式中为 `0`。

int isolated¶  
~~~
    

~~~
隔离模式：参见 `PyConfig.isolated`。

默认值: 在 Python 模式中为 `0`，在隔离模式中为 `1`。

int legacy_windows_fs_encoding¶  
~~~
    

~~~
如为非零值:

  * 设置 `PyPreConfig.utf8_mode` 为 `0`,

  * 设置 `PyConfig.filesystem_encoding` 为 `"mbcs"`,

  * 设置 `PyConfig.filesystem_errors` 为 `"replace"`.

初始化来自 [`PYTHONLEGACYWINDOWSFSENCODING`](../using/cmdline.md#envvar-PYTHONLEGACYWINDOWSFSENCODING) 的环境变量值。

仅在 Windows 上可用。 `#ifdef MS_WINDOWS` 宏可被用于 Windows 专属的代码。

默认值: `0`.

int parse_argv¶  
~~~
    

~~~
如为非零值，`Py_PreInitializeFromArgs()` 和 `Py_PreInitializeFromBytesArgs()` 将以与常规 Python 解析命令行参数的相同方式解析其 `argv` 参数：参见 [命令行参数](../using/cmdline.md#using-on-cmdline)。

默认值: 在 Python 配置中为 `1`，在隔离配置中为 `0`。

int use_environment¶  
~~~
    

~~~
使用 [环境变量](../using/cmdline.md#using-on-envvars)? 参见 `PyConfig.use_environment`。

默认值: 在 Python 配置中为 `1` 而在隔离配置中为 `0`。

int utf8_mode¶  
~~~
    

~~~
如为非零值，则启用 [Python UTF-8 模式](../library/os.md#utf8-mode)。

通过 [`-X utf8`](../using/cmdline.md#cmdoption-X) 命令行选项和 [`PYTHONUTF8`](../using/cmdline.md#envvar-PYTHONUTF8) 环境变量设为 `0` 或 `1`。

如果 `LC_CTYPE` 语言区域为 `C` 或 `POSIX` 也会被设为 `1`。

默认值: 在 Python 配置中为 `-1` 而在隔离配置中为 `0`。

## 使用 PyPreConfig 预初始化 Python¶

Python 的预初始化:

  * 设置 Python 内存分配器 (`PyPreConfig.allocator`)

  * 配置 LC_CTYPE 语言区域 ([locale encoding](../glossary.md#term-locale-encoding))

  * 设置 [Python UTF-8 模式](../library/os.md#utf8-mode) (`PyPreConfig.utf8_mode`)

当前的预配置 (`PyPreConfig` 类型) 保存在 `_PyRuntime.preconfig` 中。

用于预初始化 Python 的函数：

PyStatus Py_PreInitialize(const PyPreConfig *preconfig)¶  
~~~
    

~~~
根据 _preconfig_ 预配置来预初始化 Python。

_preconfig_ 不可为 `NULL`。

PyStatus Py_PreInitializeFromBytesArgs(const PyPreConfig *preconfig, int argc, char *const *argv)¶  
~~~
    

~~~
根据 _preconfig_ 预配置来预初始化 Python。

如果 _preconfig_ 的 `parse_argv` 为非零值则解析 _argv_ 命令行参数（字节串）。

_preconfig_ 不可为 `NULL`。

PyStatus Py_PreInitializeFromArgs(const PyPreConfig *preconfig, int argc, wchar_t *const *argv)¶  
~~~
    

~~~
根据 _preconfig_ 预配置来预初始化 Python。

如果 _preconfig_ 的 `parse_argv` 为非零值则解析 _argv_ 命令行参数（宽字符串）。

_preconfig_ 不可为 `NULL`。

调用方要负责使用 `PyStatus_Exception()` 和 `Py_ExitStatusException()` 来处理异常（错误或退出）。

对于 Python 配置 (`PyPreConfig_InitPythonConfig()`)，如果 Python 是用命令行参数初始化的，那么在预初始化 Python 时也必须传递命令行参数，因为它们会对编码格式等预配置产生影响。 例如，[`-X utf8`](../using/cmdline.md#cmdoption-X) 命令行选项将启用 [Python UTF-8 模式](../library/os.md#utf8-mode)。

`PyMem_SetAllocator()` 可在 `Py_PreInitialize()` 之后、`Py_InitializeFromConfig()` 之前被调用以安装自定义的内存分配器。 如果 `PyPreConfig.allocator` 被设为 `PYMEM_ALLOCATOR_NOT_SET` 则可在 `Py_PreInitialize()` 之前被调用。

像 [`PyMem_RawMalloc()`](memory.md#c.PyMem_RawMalloc "PyMem_RawMalloc") 这样的 Python 内存分配函数不能在 Python 预初始化之前使用，而直接调用 `malloc()` 和 `free()` 则始终会是安全的。 [`Py_DecodeLocale()`](sys.md#c.Py_DecodeLocale "Py_DecodeLocale") 不能在 Python 预初始化之前被调用。

使用预初始化来启用 [Python UTF-8 模式](../library/os.md#utf8-mode) 的例子:
~~~
    
    
~~~
PyStatus status;
PyPreConfig preconfig;
PyPreConfig_InitPythonConfig(&preconfig);

preconfig.utf8_mode = 1;

status = Py_PreInitialize(&preconfig);
if (PyStatus_Exception(status)) {
    Py_ExitStatusException(status);
}

/* at this point, Python speaks UTF-8 */

Py_Initialize();
/* ... use Python API here ... */
Py_Finalize();
~~~

## PyConfig¶

type PyConfig¶  

    

~~~
包含了大部分用于配置 Python 的形参的结构体。

在完成后，必须使用 `PyConfig_Clear()` 函数来释放配置内存。

结构体方法:

void PyConfig_InitPythonConfig(PyConfig *config)¶  
~~~
    

~~~
通过 Python 配置 来初始化配置。

void PyConfig_InitIsolatedConfig(PyConfig *config)¶  
~~~
    

~~~
通过 隔离配置 来初始化配置。

PyStatus PyConfig_SetString(PyConfig *config, wchar_t *const *config_str, const wchar_t *str)¶  
~~~
    

~~~
将宽字符串 _str_ 拷贝至 `*config_str`。

在必要时 预初始化 Python。

PyStatus PyConfig_SetBytesString(PyConfig *config, wchar_t *const *config_str, const char *str)¶  
~~~
    

~~~
使用 [`Py_DecodeLocale()`](sys.md#c.Py_DecodeLocale "Py_DecodeLocale") 对 _str_ 进行解码并将结果设置到 `*config_str`。

在必要时 预初始化 Python。

PyStatus PyConfig_SetArgv(PyConfig *config, int argc, wchar_t *const *argv)¶  
~~~
    

~~~
根据宽字符串列表 _argv_ 设置命令行参数 ( _config_ 的 `argv` 成员)。

在必要时 预初始化 Python。

PyStatus PyConfig_SetBytesArgv(PyConfig *config, int argc, char *const *argv)¶  
~~~
    

~~~
根据字节串列表 _argv_ 设置命令行参数 ( _config_ 的 `argv` 成员)。 使用 [`Py_DecodeLocale()`](sys.md#c.Py_DecodeLocale "Py_DecodeLocale") 对字节串进行解码。

在必要时 预初始化 Python。

PyStatus PyConfig_SetWideStringList(PyConfig *config, PyWideStringList *list, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") length, wchar_t **items)¶  
~~~
    

~~~
将宽字符串列表 _list_ 设置为 _length_ 和 _items_ 。

在必要时 预初始化 Python。

PyStatus PyConfig_Read(PyConfig *config)¶  
~~~
    

~~~
读取所有 Python 配置。

已经初始化的字段会保持不变。

调用此函数时不再计算或修改用于 路径配置 的字段，如 Python 3.11 那样。

`PyConfig_Read()` 函数只解析 `PyConfig.argv` 参数一次：在参数解析完成后，`PyConfig.parse_argv` 将被设为 `2`。 由于 Python 参数是从 `PyConfig.argv` 中剥离的，因此解析参数两次会将应用程序选项解析为 Python 选项。

在必要时 预初始化 Python。

在 3.10 版本发生变更: `PyConfig.argv` 参数现在只会被解析一次，在参数解析完成后，`PyConfig.parse_argv` 将被设为 `2`，只有当 `PyConfig.parse_argv` 等于 `1` 时才会解析参数。

在 3.11 版本发生变更: `PyConfig_Read()` 不会再计算所有路径，因此在 Python 路径配置 下列出的字段可能不会再更新直到 `Py_InitializeFromConfig()` 被调用。

void PyConfig_Clear(PyConfig *config)¶  
~~~
    

~~~
释放配置内存

如有必要大多数 `PyConfig` 方法将会 预初始化 Python。 在这种情况下，Python 预初始化配置 (`PyPreConfig`) 将以 `PyConfig` 为基础。 如果要调整与 `PyPreConfig` 相同的配置字段，它们必须在调用 `PyConfig` 方法之前被设置：

  * `PyConfig.dev_mode`

  * `PyConfig.isolated`

  * `PyConfig.parse_argv`

  * `PyConfig.use_environment`

此外，如果使用了 `PyConfig_SetArgv()` 或 `PyConfig_SetBytesArgv()`，则必须在调用其他方法之前调用该方法，因为预初始化配置取决于命令行参数（如果 `parse_argv` 为非零值）。

这些方法的调用者要负责使用 `PyStatus_Exception()` 和 `Py_ExitStatusException()` 来处理异常（错误或退出）。

结构体字段:

PyWideStringList argv¶  
~~~
    

~~~
Set [`sys.argv`](../library/sys.md#sys.argv "sys.argv") command line arguments based on `argv`. These parameters are similar to those passed to the program's `main()` function with the difference that the first entry should refer to the script file to be executed rather than the executable hosting the Python interpreter. If there isn't a script that will be run, the first entry in `argv` can be an empty string.

将 `parse_argv` 设为 `1` 将以与普通 Python 解析 Python 命令行参数相同的方式解析 `argv` 再从 `argv` 中剥离 Python 参数。

如果 `argv` 为空，则会添加一个空字符串以确保 [`sys.argv`](../library/sys.md#sys.argv "sys.argv") 始终存在并且永远不为空。

默认值: `NULL`.

另请参阅 `orig_argv` 成员。

int safe_path¶  
~~~
    

~~~
如果等于零，`Py_RunMain()` 会在启动时向 [`sys.path`](../library/sys.md#sys.path "sys.path") 开头添加一个可能不安全的路径:

  * 如果 `argv[0]` 等于 `L"-m"` (`python -m module`)，则添加当前工作目录。

  * 如果是运行脚本 (`python script.py`)，则添加脚本的目录。 如果是符号链接，则会解析符号链接。

  * 在其他情况下 (`python -c code` 和 `python`)，将添加一个空字符串，这表示当前工作目录。

通过 [`-P`](../using/cmdline.md#cmdoption-P) 命令行选项和 [`PYTHONSAFEPATH`](../using/cmdline.md#envvar-PYTHONSAFEPATH) 环境变量设置为 `1`。

默认值：Python 配置中为 `0`，隔离配置中为 `1`。

在 3.11 版本加入.

wchar_t *base_exec_prefix¶  
~~~
    

~~~
[`sys.base_exec_prefix`](../library/sys.md#sys.base_exec_prefix "sys.base_exec_prefix").

默认值: `NULL`.

Python 路径配置 的一部分。

See also `PyConfig.exec_prefix`.

wchar_t *base_executable¶  
~~~
    

~~~
Python 基础可执行文件: `sys._base_executable`。

由 `__PYVENV_LAUNCHER__` 环境变量设置。

如为 `NULL` 则从 `PyConfig.executable` 设置。

默认值: `NULL`.

Python 路径配置 的一部分。

See also `PyConfig.executable`.

wchar_t *base_prefix¶  
~~~
    

~~~
[`sys.base_prefix`](../library/sys.md#sys.base_prefix "sys.base_prefix").

默认值: `NULL`.

Python 路径配置 的一部分。

See also `PyConfig.prefix`.

int buffered_stdio¶  
~~~
    

~~~
如果等于 `0` 且 `configure_c_stdio` 为非零值，则禁用 C 数据流 stdout 和 stderr 的缓冲。

通过 [`-u`](../using/cmdline.md#cmdoption-u) 命令行选项和 [`PYTHONUNBUFFERED`](../using/cmdline.md#envvar-PYTHONUNBUFFERED) 环境变量设置为 `0`。

stdin 始终以缓冲模式打开。

默认值: `1`.

int bytes_warning¶  
~~~
    

~~~
如果等于 `1`，则在将 [`bytes`](../library/stdtypes.md#bytes "bytes") 或 [`bytearray`](../library/stdtypes.md#bytearray "bytearray") 与 [`str`](../library/stdtypes.md#str "str") 进行比较，或将 [`bytes`](../library/stdtypes.md#bytes "bytes") 与 [`int`](../library/functions.md#int "int") 进行比较时发出警告。

如果大于等于 `2`，则在这些情况下引发 [`BytesWarning`](../library/exceptions.md#BytesWarning "BytesWarning") 异常。

由 [`-b`](../using/cmdline.md#cmdoption-b) 命令行选项执行递增。

默认值: `0`.

int warn_default_encoding¶  
~~~
    

~~~
如为非零值，则在 [`io.TextIOWrapper`](../library/io.md#io.TextIOWrapper "io.TextIOWrapper") 使用默认编码格式时发出 [`EncodingWarning`](../library/exceptions.md#EncodingWarning "EncodingWarning") 警告。 详情请参阅 [选择性的 EncodingWarning](../library/io.md#io-encoding-warning)。

默认值: `0`.

在 3.10 版本加入.

int code_debug_ranges¶  
~~~
    

~~~
如果等于 `0`，则禁用在代码对象中包括末尾行和列映射。 并且禁用在特定错误位置打印回溯标记。

通过 [`PYTHONNODEBUGRANGES`](../using/cmdline.md#envvar-PYTHONNODEBUGRANGES) 环境变量和 [`-X no_debug_ranges`](../using/cmdline.md#cmdoption-X) 命令行选项设置为 `0`。

默认值: `1`.

在 3.11 版本加入.

wchar_t *check_hash_pycs_mode¶  
~~~
    

~~~
控制基于哈希值的 `.pyc` 文件的验证行为: [`--check-hash-based-pycs`](../using/cmdline.md#cmdoption-check-hash-based-pycs) 命令行选项的值。

有效的值：

  * `L"always"`: 无论 'check_source' 旗标的值是什么都会对源文件进行哈希验证。

  * `L"never"`: 假定基于哈希值的 pyc 始终是有效的。

  * `L"default"`: 基于哈希值的 pyc 中的 'check_source' 旗标确定是否验证无效。

默认值： `L"default"`。

参见 [**PEP 552**](https://peps.python.org/pep-0552/) "Deterministic pycs"。

int configure_c_stdio¶  
~~~
    

~~~
如为非零值，则配置 C 标准流:

  * 在 Windows 中，在 stdin, stdout 和 stderr 上设置二进制模式 (`O_BINARY`)。

  * 如果 `buffered_stdio` 等于零，则禁用 stdin, stdout 和 stderr 流的缓冲。

  * 如果 `interactive` 为非零值，则启用 stdin 和 stdout 上的流缓冲（Windows 中仅限 stdout）。

默认值: 在 Python 配置中为 `1`，在隔离配置中为 `0`。

int dev_mode¶  
~~~
    

~~~
如果为非零值，则启用 [Python 开发模式](../library/devmode.md#devmode)。

通过 [`-X dev`](../using/cmdline.md#cmdoption-X) 选项和 [`PYTHONDEVMODE`](../using/cmdline.md#envvar-PYTHONDEVMODE) 环境变量设置为 `1`。

默认值: 在 Python 模式中为 `-1`，在隔离模式中为 `0`。

int dump_refs¶  
~~~
    

~~~
转储 Python 引用？

如果为非零值，则转储所有在退出时仍存活的对象。

由 [`PYTHONDUMPREFS`](../using/cmdline.md#envvar-PYTHONDUMPREFS) 环境变量设置为 `1`。

需要定义了 `Py_TRACE_REFS` 宏的特殊 Python 编译版：参见 [`configure --with-trace-refs 选项`](../using/configure.md#cmdoption-with-trace-refs)。

默认值: `0`.

wchar_t *exec_prefix¶  
~~~
    

~~~
安装依赖于平台的 Python 文件的站点专属目录前缀: [`sys.exec_prefix`](../library/sys.md#sys.exec_prefix "sys.exec_prefix")。

默认值: `NULL`.

Python 路径配置 的一部分。

See also `PyConfig.base_exec_prefix`.

wchar_t *executable¶  
~~~
    

~~~
Python 解释器可执行二进制文件的绝对路径: [`sys.executable`](../library/sys.md#sys.executable "sys.executable")。

默认值: `NULL`.

Python 路径配置 的一部分。

See also `PyConfig.base_executable`.

int faulthandler¶  
~~~
    

~~~
启用 faulthandler？

如果为非零值，则在启动时调用 [`faulthandler.enable()`](../library/faulthandler.md#faulthandler.enable "faulthandler.enable")。

通过 [`-X faulthandler`](../using/cmdline.md#cmdoption-X) 和 [`PYTHONFAULTHANDLER`](../using/cmdline.md#envvar-PYTHONFAULTHANDLER) 环境变量设为 `1`。

默认值: 在 Python 模式中为 `-1`，在隔离模式中为 `0`。

wchar_t *filesystem_encoding¶  
~~~
    

~~~
[文件系统编码格式](../glossary.md#term-filesystem-encoding-and-error-handler): [`sys.getfilesystemencoding()`](../library/sys.md#sys.getfilesystemencoding "sys.getfilesystemencoding")。

在 macOS, Android 和 VxWorks 上：默认使用 `"utf-8"`。

在 Windows 上：默认使用 `"utf-8"`，或者如果 `PyPreConfig` 的 `legacy_windows_fs_encoding` 为非零值则使用 `"mbcs"`。

在其他平台上的默认编码格式：

  * 如果 `PyPreConfig.utf8_mode` 为非零值则使用 `"utf-8"`。

  * 如果 Python 检测到 `nl_langinfo(CODESET)` 声明为 ASCII 编码格式，而 `mbstowcs()` 是从其他的编码格式解码（通常为 Latin1）则使用 `"ascii"`。

  * 如果 `nl_langinfo(CODESET)` 返回空字符串则使用 `"utf-8"`。

  * 在其他情况下，使用 [locale encoding](../glossary.md#term-locale-encoding): `nl_langinfo(CODESET)` 的结果。

在 Python 启动时，编码格式名称会规范化为 Python 编解码器名称。 例如，`"ANSI_X3.4-1968"` 将被替换为 `"ascii"`。

参见 `filesystem_errors` 的成员。

wchar_t *filesystem_errors¶  
~~~
    

~~~
[文件系统错误处理句柄](../glossary.md#term-filesystem-encoding-and-error-handler): [`sys.getfilesystemencodeerrors()`](../library/sys.md#sys.getfilesystemencodeerrors "sys.getfilesystemencodeerrors")。

在 Windows 上：默认使用 `"surrogatepass"`，或者如果 `PyPreConfig` 的 `legacy_windows_fs_encoding` 为非零值则使用 `"replace"`。

在其他平台上：默认使用 `"surrogateescape"`。

支持的错误处理句柄：

  * `"strict"`

  * `"surrogateescape"`

  * `"surrogatepass"` (仅支持 UTF-8 编码格式)

参见 `filesystem_encoding` 的成员。

unsigned long hash_seed¶  
~~~
    

~~~
int use_hash_seed¶  
~~~
    

~~~
随机化的哈希函数种子。

如果 `use_hash_seed` 为零，则在 Python 启动时随机选择一个种子，并忽略 `hash_seed`。

由 [`PYTHONHASHSEED`](../using/cmdline.md#envvar-PYTHONHASHSEED) 环境变量设置。

默认的 _use_hash_seed_ 值：在 Python 模式下为 `-1`，在隔离模式下为 `0`。

wchar_t *home¶  
~~~
    

~~~
Set the default Python "home" directory, that is, the location of the standard Python libraries (see [`PYTHONHOME`](../using/cmdline.md#envvar-PYTHONHOME)).

由 [`PYTHONHOME`](../using/cmdline.md#envvar-PYTHONHOME) 环境变量设置。

默认值: `NULL`.

Python 路径配置 输入的一部分。

int import_time¶  
~~~
    

~~~
如为非零值，则对导入时间执行性能分析。

通过 [`-X importtime`](../using/cmdline.md#cmdoption-X) 选项和 [`PYTHONPROFILEIMPORTTIME`](../using/cmdline.md#envvar-PYTHONPROFILEIMPORTTIME) 环境变量设置为 `1`。

默认值: `0`.

int inspect¶  
~~~
    

~~~
在执行脚本或命令之后进入交互模式。

如果大于 `0` ，则启用检查：当脚本作为第一个参数传入或使用了 -c 选项时，在执行脚本或命令后进入交互模式，即使在 [`sys.stdin`](../library/sys.md#sys.stdin "sys.stdin") 看来并非一个终端时也是如此。

通过 [`-i`](../using/cmdline.md#cmdoption-i) 命令行选项执行递增。 如果 [`PYTHONINSPECT`](../using/cmdline.md#envvar-PYTHONINSPECT) 环境变量为非空值则设为 `1`。

默认值: `0`.

int install_signal_handlers¶  
~~~
    

~~~
安装 Python 信号处理句柄？

默认值：在 Python 模式下为 `1`，在隔离模式下为 `0`。

int interactive¶  
~~~
    

~~~
如果大于 `0`，则启用交互模式（REPL）。

由 [`-i`](../using/cmdline.md#cmdoption-i) 命令行选项执行递增。

默认值: `0`.

int int_max_str_digits¶  
~~~
    

~~~
配置 [整数字符串转换长度限制](../library/stdtypes.md#int-max-str-digits)。 初始值为 `-1` 表示该值将从命令行或环境获取否则默认为 4300 ([`sys.int_info.default_max_str_digits`](../library/sys.md#sys.int_info.default_max_str_digits "sys.int_info.default_max_str_digits"))。 值为 `0` 表示禁用限制。 大于 0 但小于 640 ([`sys.int_info.str_digits_check_threshold`](../library/sys.md#sys.int_info.str_digits_check_threshold "sys.int_info.str_digits_check_threshold")) 的值将不被支持并会产生错误。

通过 [`-X int_max_str_digits`](../using/cmdline.md#cmdoption-X) 命令行旗标或 [`PYTHONINTMAXSTRDIGITS`](../using/cmdline.md#envvar-PYTHONINTMAXSTRDIGITS) 环境变量配置。

默认值：在 Python 模式下为 `-1` 。 在孤立模式下为 4300 ([`sys.int_info.default_max_str_digits`](../library/sys.md#sys.int_info.default_max_str_digits "sys.int_info.default_max_str_digits"))。

在 3.12 版本加入.

int isolated¶  
~~~
    

~~~
如果大于 `0` ，则启用隔离模式：

  * 将 `safe_path` 设为 `1`: 在 Python 启动时将不在 [`sys.path`](../library/sys.md#sys.path "sys.path") 前添加有潜在不安全性的路径，如当前目录、脚本所在目录或空字符串。

  * 将 `use_environment` 设为 `0`: 忽略 `PYTHON` 环境变量。

  * 将 `user_site_directory` 设为 `0`: 不要将用户级站点目录添加到 [`sys.path`](../library/sys.md#sys.path "sys.path")。

  * Python REPL 将不导入 [`readline`](../library/readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 也不在交互提示符中启用默认的 readline 配置。

通过 [`-I`](../using/cmdline.md#cmdoption-I) 命令行选项设置为 `1`。

默认值: 在 Python 模式中为 `0`，在隔离模式中为 `1`。

另请参阅 隔离配置 和 `PyPreConfig.isolated`。

int legacy_windows_stdio¶  
~~~
    

~~~
如为非零值，则使用 [`io.FileIO`](../library/io.md#io.FileIO "io.FileIO") 代替 `io._WindowsConsoleIO` 作为 [`sys.stdin`](../library/sys.md#sys.stdin "sys.stdin")、[`sys.stdout`](../library/sys.md#sys.stdout "sys.stdout") 和 [`sys.stderr`](../library/sys.md#sys.stderr "sys.stderr")。

如果 [`PYTHONLEGACYWINDOWSSTDIO`](../using/cmdline.md#envvar-PYTHONLEGACYWINDOWSSTDIO) 环境变量被设为非空字符串则设为 `1`。

仅在 Windows 上可用。 `#ifdef MS_WINDOWS` 宏可被用于 Windows 专属的代码。

默认值: `0`.

另请参阅 [**PEP 528**](https://peps.python.org/pep-0528/) (将 Windows 控制台编码格式更改为 UTF-8)。

int malloc_stats¶  
~~~
    

~~~
如为非零值，则在退出时转储 [Python pymalloc 内存分配器](memory.md#pymalloc) 的统计数据。

由 [`PYTHONMALLOCSTATS`](../using/cmdline.md#envvar-PYTHONMALLOCSTATS) 环境变量设置为 `1`。

如果 Python 是 [`使用 --without-pymalloc 选项进行配置`](../using/configure.md#cmdoption-without-pymalloc) 则该选项将被忽略。

默认值: `0`.

wchar_t *platlibdir¶  
~~~
    

~~~
平台库目录名称: [`sys.platlibdir`](../library/sys.md#sys.platlibdir "sys.platlibdir")。

由 [`PYTHONPLATLIBDIR`](../using/cmdline.md#envvar-PYTHONPLATLIBDIR) 环境变量设置。

默认值：由 [`configure --with-platlibdir 选项`](../using/configure.md#cmdoption-with-platlibdir) 设置的 `PLATLIBDIR` 宏的值 (默认值: `"lib"`，在 Windows 上则为 `"DLLs"`)。

Python 路径配置 输入的一部分。

在 3.9 版本加入.

在 3.11 版本发生变更: 目前在 Windows 系统中该宏被用于定位标准库扩展模块，通常位于 `DLLs` 下。 不过，出于兼容性考虑，请注意在任何非标准布局包括树内构建和虚拟环境中，该值都将被忽略。

wchar_t *pythonpath_env¶  
~~~
    

~~~
模块搜索路径 ([`sys.path`](../library/sys.md#sys.path "sys.path")) 为一个用 `DELIM` ([`os.pathsep`](../library/os.md#os.pathsep "os.pathsep")) 分隔的字符串。

由 [`PYTHONPATH`](../using/cmdline.md#envvar-PYTHONPATH) 环境变量设置。

默认值: `NULL`.

Python 路径配置 输入的一部分。

PyWideStringList module_search_paths¶  
~~~
    

~~~
int module_search_paths_set¶  
~~~
    

~~~
模块搜索路径: [`sys.path`](../library/sys.md#sys.path "sys.path")。

如果 `module_search_paths_set` 等于 `0`，`Py_InitializeFromConfig()` 将替代 `module_search_paths` 并将 `module_search_paths_set` 设为 `1`。

默认值：空列表 (`module_search_paths`) 和 `0` (`module_search_paths_set`)。

Python 路径配置 的一部分。

int optimization_level¶  
~~~
    

~~~
编译优化级别：

  * `0`: Peephole 优化器，将 `__debug__` 设为 `True`。

  * `1`: 0 级，移除断言，将 `__debug__` 设为 `False`。

  * `2`: 1 级，去除文档字符串。

通过 [`-O`](../using/cmdline.md#cmdoption-O) 命令行选项递增。 设置为 [`PYTHONOPTIMIZE`](../using/cmdline.md#envvar-PYTHONOPTIMIZE) 环境变量值。

默认值: `0`.

PyWideStringList orig_argv¶  
~~~
    

~~~
传给 Python 可执行程序的原始命令行参数列表: [`sys.orig_argv`](../library/sys.md#sys.orig_argv "sys.orig_argv")。

如果 `orig_argv` 列表为空并且 `argv` 不是一个只包含空字符串的列表，`PyConfig_Read()` 将在修改 `argv` 之前把 `argv` 拷贝至 `orig_argv` (如果 `parse_argv` 不为空)。

另请参阅 `argv` 成员和 `Py_GetArgcArgv()` 函数。

默认值：空列表。

在 3.10 版本加入.

int parse_argv¶  
~~~
    

~~~
解析命令行参数？

如果等于 `1`，则以与常规 Python 解析 [命令行参数](../using/cmdline.md#using-on-cmdline) 相同的方式解析 `argv`，并从 `argv` 中剥离 Python 参数。

`PyConfig_Read()` 函数只解析 `PyConfig.argv` 参数一次：在参数解析完成后，`PyConfig.parse_argv` 将被设为 `2`。 由于 Python 参数是从 `PyConfig.argv` 中剥离的，因此解析参数两次会将应用程序选项解析为 Python 选项。

默认值：在 Python 模式下为 `1`，在隔离模式下为 `0`。

在 3.10 版本发生变更: 现在只有当 `PyConfig.parse_argv` 等于 `1` 时才会解析 `PyConfig.argv` 参数。

int parser_debug¶  
~~~
    

~~~
解析器调试模式。 如果大于 `0`，则打开解析器调试输出（仅针对专家，取决于编译选项）。

通过 [`-d`](../using/cmdline.md#cmdoption-d) 命令行选项递增。 设置为 [`PYTHONDEBUG`](../using/cmdline.md#envvar-PYTHONDEBUG) 环境变量值。

需要 [Python 调试编译版](../using/configure.md#debug-build) (必须定义 `Py_DEBUG` 宏)。

默认值: `0`.

int pathconfig_warnings¶  
~~~
    

~~~
如为非零值，则允许计算路径配置以将警告记录到 `stderr` 中。 如果等于 `0`，则抑制这些警告。

默认值：在 Python 模式下为 `1`，在隔离模式下为 `0`。

Python 路径配置 输入的一部分。

在 3.11 版本发生变更: 现在也适用于 Windows。

wchar_t *prefix¶  
~~~
    

~~~
安装依赖于平台的 Python 文件的站点专属目录前缀: [`sys.prefix`](../library/sys.md#sys.prefix "sys.prefix")。

默认值: `NULL`.

Python 路径配置 的一部分。

See also `PyConfig.base_prefix`.

wchar_t *program_name¶  
~~~
    

~~~
用于初始化 `executable` 和在 Python 初始化期间早期错误消息中使用的程序名称。

  * 在 macOS 上，如果设置了 [`PYTHONEXECUTABLE`](../using/cmdline.md#envvar-PYTHONEXECUTABLE) 环境变量则会使用它。

  * 如果定义了 `WITH_NEXT_FRAMEWORK` 宏，当设置了 `__PYVENV_LAUNCHER__` 环境变量时将会使用它。

  * 如果 `argv` 的 `argv[0]` 可用并且不为空值则会使用它。

  * 否则，在 Windows 上将使用 `L"python"`，在其他平台上将使用 `L"python3"`。

默认值: `NULL`.

Python 路径配置 输入的一部分。

wchar_t *pycache_prefix¶  
~~~
    

~~~
缓存 `.pyc` 文件被写入到的目录: [`sys.pycache_prefix`](../library/sys.md#sys.pycache_prefix "sys.pycache_prefix")。

通过 [`-X pycache_prefix=PATH`](../using/cmdline.md#cmdoption-X) 命令行选项和 [`PYTHONPYCACHEPREFIX`](../using/cmdline.md#envvar-PYTHONPYCACHEPREFIX) 环境变量设置。

如果为 `NULL`，则 [`sys.pycache_prefix`](../library/sys.md#sys.pycache_prefix "sys.pycache_prefix") 将被设为 `None`。

默认值: `NULL`.

int quiet¶  
~~~
    

~~~
安静模式。 如果大于 `0`，则在交互模式下启动 Python 时不显示版权和版本。

由 [`-q`](../using/cmdline.md#cmdoption-q) 命令行选项执行递增。

默认值: `0`.

wchar_t *run_command¶  
~~~
    

~~~
[`-c`](../using/cmdline.md#cmdoption-c) 命令行选项的值。

由 `Py_RunMain()` 使用。

默认值: `NULL`.

wchar_t *run_filename¶  
~~~
    

~~~
通过命令行传入的文件名：不包含 [`-c`](../using/cmdline.md#cmdoption-c) 或 [`-m`](../using/cmdline.md#cmdoption-m) 的附加命令行参数。 它会被 `Py_RunMain()` 函数使用。

例如，对于命令行 `python3 script.py arg` 它将被设为 `script.py`。

另请参阅 `PyConfig.skip_source_first_line` 选项。

默认值: `NULL`.

wchar_t *run_module¶  
~~~
    

~~~
[`-m`](../using/cmdline.md#cmdoption-m) 命令行选项的值。

由 `Py_RunMain()` 使用。

默认值: `NULL`.

int show_ref_count¶  
~~~
    

~~~
在退出时显示总引用计数（不包括永生对象）？

通过 [`-X showrefcount`](../using/cmdline.md#cmdoption-X) 命令行选项设置为 `1`。

需要 [Python 调试编译版](../using/configure.md#debug-build) (必须定义 `Py_REF_DEBUG` 宏)。

默认值: `0`.

int site_import¶  
~~~
    

~~~
在启动时导入 [`site`](../library/site.md#module-site "site: Module responsible for site-specific configuration.") 模块？

如果等于零，则禁用模块站点的导入以及由此产生的与站点相关的 [`sys.path`](../library/sys.md#sys.path "sys.path") 操作。

如果以后显式地导入 [`site`](../library/site.md#module-site "site: Module responsible for site-specific configuration.") 模块也要禁用这些操作（如果你希望触发这些操作，请调用 [`site.main()`](../library/site.md#site.main "site.main") 函数）。

通过 [`-S`](../using/cmdline.md#cmdoption-S) 命令行选项设置为 `0`。

[`sys.flags.no_site`](../library/sys.md#sys.flags "sys.flags") 会被设为 `site_import` 取反后的值。

默认值: `1`.

int skip_source_first_line¶  
~~~
    

~~~
如为非零值，则跳过 `PyConfig.run_filename` 源的第一行。

它将允许使用非 Unix 形式的 `#!cmd`。 这是针对 DOS 专属的破解操作。

通过 [`-x`](../using/cmdline.md#cmdoption-x) 命令行选项设置为 `1`。

默认值: `0`.

wchar_t *stdio_encoding¶  
~~~
    

~~~
wchar_t *stdio_errors¶  
~~~
    

~~~
[`sys.stdin`](../library/sys.md#sys.stdin "sys.stdin")、[`sys.stdout`](../library/sys.md#sys.stdout "sys.stdout") 和 [`sys.stderr`](../library/sys.md#sys.stderr "sys.stderr") 的编码格式和编码格式错误（但 [`sys.stderr`](../library/sys.md#sys.stderr "sys.stderr") 将始终使用 `"backslashreplace"` 错误处理句柄）。

如果 [`PYTHONIOENCODING`](../using/cmdline.md#envvar-PYTHONIOENCODING) 环境变量非空则会使用它。

默认编码格式：

  * 如果 `PyPreConfig.utf8_mode` 为非零值则使用 `"UTF-8"`。

  * 在其他情况下，使用 [locale encoding](../glossary.md#term-locale-encoding)。

默认错误处理句柄：

  * 在 Windows 上：使用 `"surrogateescape"`。

  * 如果 `PyPreConfig.utf8_mode` 为非零值，或者如果 LC_CTYPE 语言区域为 "C" 或 "POSIX" 则使用 `"surrogateescape"`。

  * 在其他情况下则使用 `"strict"`。

See also `PyConfig.legacy_windows_stdio`.

int tracemalloc¶  
~~~
    

~~~
启用 tracemalloc？

如果为非零值，则在启动时调用 [`tracemalloc.start()`](../library/tracemalloc.md#tracemalloc.start "tracemalloc.start")。

通过 [`-X tracemalloc=N`](../using/cmdline.md#cmdoption-X) 命令行选项和 [`PYTHONTRACEMALLOC`](../using/cmdline.md#envvar-PYTHONTRACEMALLOC) 环境变量设置。

默认值: 在 Python 模式中为 `-1`，在隔离模式中为 `0`。

int perf_profiling¶  
~~~
    

~~~
启用与 perf 性能分析器兼容的模式？

如果为非零值，则初始化 perf trampoline。 更多信息参见 [Python 对 Linux perf 性能分析器的支持](../howto/perf_profiling.md#perf-profiling)。

通过 [`-X perf`](../using/cmdline.md#cmdoption-X) 命令行选项和 [`PYTHONPERFSUPPORT`](../using/cmdline.md#envvar-PYTHONPERFSUPPORT) 环境变量设置。

默认值: `-1`。

在 3.12 版本加入.

int use_environment¶  
~~~
    

~~~
使用 [环境变量](../using/cmdline.md#using-on-envvars)？

如果等于零，则忽略 [环境变量](../using/cmdline.md#using-on-envvars)。

由 [`-E`](../using/cmdline.md#cmdoption-E) 环境变量设置为 `0`。

默认值: 在 Python 配置中为 `1` 而在隔离配置中为 `0`。

int user_site_directory¶  
~~~
    

~~~
如果为非零值，则将用户站点目录添加到 [`sys.path`](../library/sys.md#sys.path "sys.path")。

通过 [`-s`](../using/cmdline.md#cmdoption-s) 和 [`-I`](../using/cmdline.md#cmdoption-I) 命令行选项设置为 `0`。

由 [`PYTHONNOUSERSITE`](../using/cmdline.md#envvar-PYTHONNOUSERSITE) 环境变量设置为 `0`。

默认值：在 Python 模式下为 `1`，在隔离模式下为 `0`。

int verbose¶  
~~~
    

~~~
详细模式。 如果大于 `0`，则每次导入模块时都会打印一条消息，显示加载模块的位置（文件名或内置模块）。

如果大于等于 `2`，则为搜索模块时每个被检查的文件打印一条消息。 还在退出时提供关于模块清理的信息。

由 [`-v`](../using/cmdline.md#cmdoption-1) 命令行选项执行递增。

通过 [`PYTHONVERBOSE`](../using/cmdline.md#envvar-PYTHONVERBOSE) 环境变量值设置。

默认值: `0`.

PyWideStringList warnoptions¶  
~~~
    

~~~
[`warnings`](../library/warnings.md#module-warnings "warnings: Issue warning messages and control their disposition.") 模块用于构建警告过滤器的选项，优先级从低到高: [`sys.warnoptions`](../library/sys.md#sys.warnoptions "sys.warnoptions")。

[`warnings`](../library/warnings.md#module-warnings "warnings: Issue warning messages and control their disposition.") 模块以相反的顺序添加 [`sys.warnoptions`](../library/sys.md#sys.warnoptions "sys.warnoptions"): 最后一个 `PyConfig.warnoptions` 条目将成为 `warnings.filters` 的第一个条目并将最先被检查（最高优先级）。

[`-W`](../using/cmdline.md#cmdoption-W) 命令行选项会将其值添加到 `warnoptions` 中，它可以被多次使用。

[`PYTHONWARNINGS`](../using/cmdline.md#envvar-PYTHONWARNINGS) 环境变量也可被用于添加警告选项。 可以指定多个选项，并以逗号 (`,`) 分隔。

默认值：空列表。

int write_bytecode¶  
~~~
    

~~~
如果等于 `0`，Python 将不会尝试在导入源模块时写入 `.pyc` 文件。

通过 [`-B`](../using/cmdline.md#cmdoption-B) 命令行选项和 [`PYTHONDONTWRITEBYTECODE`](../using/cmdline.md#envvar-PYTHONDONTWRITEBYTECODE) 环境变量设置为 `0`。

[`sys.dont_write_bytecode`](../library/sys.md#sys.dont_write_bytecode "sys.dont_write_bytecode") 会被初始化为 `write_bytecode` 取反后的值。

默认值: `1`.

PyWideStringList xoptions¶  
~~~
    

~~~
[`-X`](../using/cmdline.md#cmdoption-X) 命令行选项的值: [`sys._xoptions`](../library/sys.md#sys._xoptions "sys._xoptions")。

默认值：空列表。

如果 `parse_argv` 为非零值，则 `argv` 参数将以与常规 Python 解析 [命令行参数](../using/cmdline.md#using-on-cmdline) 相同的方式被解析，并从 `argv` 中剥离 Python 参数。

`xoptions` 选项将会被解析以设置其他选项：参见 [`-X`](../using/cmdline.md#cmdoption-X) 命令行选项。

在 3.9 版本发生变更: `show_alloc_count` 字段已被移除。

## 使用 PyConfig 初始化¶

用于初始化 Python 的函数：

PyStatus Py_InitializeFromConfig(const PyConfig *config)¶  
~~~
    

~~~
根据 _config_ 配置来初始化 Python。

调用方要负责使用 `PyStatus_Exception()` 和 `Py_ExitStatusException()` 来处理异常（错误或退出）。

如果使用了 [`PyImport_FrozenModules()`](import.md#c.PyImport_FrozenModules "PyImport_FrozenModules")、[`PyImport_AppendInittab()`](import.md#c.PyImport_AppendInittab "PyImport_AppendInittab") 或 [`PyImport_ExtendInittab()`](import.md#c.PyImport_ExtendInittab "PyImport_ExtendInittab")，则必须在 Python 预初始化之后、Python 初始化之前设置或调用它们。 如果 Python 被多次初始化，则必须在每次初始化 Python 之前调用 [`PyImport_AppendInittab()`](import.md#c.PyImport_AppendInittab "PyImport_AppendInittab") 或 [`PyImport_ExtendInittab()`](import.md#c.PyImport_ExtendInittab "PyImport_ExtendInittab")。

当前的配置 (`PyConfig` 类型) 保存在 `PyInterpreterState.config` 中。

设置程序名称的示例:
~~~
    
    
~~~
void init_python(void)
{
    PyStatus status;

    PyConfig config;
    PyConfig_InitPythonConfig(&config);

    /* Set the program name. Implicitly preinitialize Python. */
    status = PyConfig_SetString(&config, &config.program_name,
                                L"/path/to/my_program");
    if (PyStatus_Exception(status)) {
        goto exception;
    }

    status = Py_InitializeFromConfig(&config);
    if (PyStatus_Exception(status)) {
        goto exception;
    }
    PyConfig_Clear(&config);
    return;

exception:
    PyConfig_Clear(&config);
    Py_ExitStatusException(status);
}
~~~

更完整的示例会修改默认配置，读取配置，然后覆盖某些参数。 请注意自 3.11 版开始，许多参数在初始化之前不会被计算，因此无法从配置结构体中读取值。在调用初始化之前设置的任何值都将不会被初始化操作改变:

    
    
~~~
PyStatus init_python(const char *program_name)
{
    PyStatus status;

    PyConfig config;
    PyConfig_InitPythonConfig(&config);

    /* Set the program name before reading the configuration
       (decode byte string from the locale encoding).

       Implicitly preinitialize Python. */
    status = PyConfig_SetBytesString(&config, &config.program_name,
                                     program_name);
    if (PyStatus_Exception(status)) {
        goto done;
    }

    /* Read all configuration at once */
    status = PyConfig_Read(&config);
    if (PyStatus_Exception(status)) {
        goto done;
    }

    /* Specify sys.path explicitly */
    /* If you want to modify the default set of paths, finish
       initialization first and then use PySys_GetObject("path") */
    config.module_search_paths_set = 1;
    status = PyWideStringList_Append(&config.module_search_paths,
                                     L"/path/to/stdlib");
    if (PyStatus_Exception(status)) {
        goto done;
    }
    status = PyWideStringList_Append(&config.module_search_paths,
                                     L"/path/to/more/modules");
    if (PyStatus_Exception(status)) {
        goto done;
    }

    /* Override executable computed by PyConfig_Read() */
    status = PyConfig_SetString(&config, &config.executable,
                                L"/path/to/my_executable");
    if (PyStatus_Exception(status)) {
        goto done;
    }

    status = Py_InitializeFromConfig(&config);

done:
    PyConfig_Clear(&config);
    return status;
}
~~~

## 隔离配置¶

`PyPreConfig_InitIsolatedConfig()` 和 `PyConfig_InitIsolatedConfig()` 函数会创建一个配置来将 Python 与系统隔离开来。 例如，将 Python 嵌入到某个应用程序。

该配置将忽略全局配置变量、环境变量、命令行参数 (`PyConfig.argv` 将不会被解析) 和用户站点目录。 C 标准流 (例如 `stdout`) 和 LC_CTYPE 语言区域将保持不变。 信号处理句柄将不会被安装。

该配置仍然会使用配置文件来确定未被指明的路径。 请确保指定了 `PyConfig.home` 以避免计算默认的路径配置。

## Python 配置¶

`PyPreConfig_InitPythonConfig()` 和 `PyConfig_InitPythonConfig()` 函数会创建一个配置来构建一个行为与常规 Python 相同的自定义 Python。

环境变量和命令行参数将被用于配置 Python，而全局配置变量将被忽略。

此函数将根据 LC_CTYPE 语言区域、[`PYTHONUTF8`](../using/cmdline.md#envvar-PYTHONUTF8) 和 [`PYTHONCOERCECLOCALE`](../using/cmdline.md#envvar-PYTHONCOERCECLOCALE) 环境变量启用 C 语言区域强制转换 ([ **PEP 538**](https://peps.python.org/pep-0538/)) 和 [Python UTF-8 模式](../library/os.md#utf8-mode) ([ **PEP 540**](https://peps.python.org/pep-0540/))。

## Python 路径配置¶

`PyConfig` 包含多个用于路径配置的字段：

  * 路径配置输入：

    * `PyConfig.home`

    * `PyConfig.platlibdir`

    * `PyConfig.pathconfig_warnings`

    * `PyConfig.program_name`

    * `PyConfig.pythonpath_env`

    * 当前工作目录：用于获取绝对路径

    * `PATH` 环境变量用于获取程序的完整路径 (来自 `PyConfig.program_name`)

    * `__PYVENV_LAUNCHER__` 环境变量

    * （仅限 Windows only） 注册表 HKEY_CURRENT_USER 和 HKEY_LOCAL_MACHINE 的 "SoftwarePythonPythonCoreX.YPythonPath" 项下的应用程序目录（其中 X.Y 为 Python 版本）。

  * 路径配置输出字段：

    * `PyConfig.base_exec_prefix`

    * `PyConfig.base_executable`

    * `PyConfig.base_prefix`

    * `PyConfig.exec_prefix`

    * `PyConfig.executable`

    * `PyConfig.module_search_paths_set`, `PyConfig.module_search_paths`

    * `PyConfig.prefix`

如果至少有一个“输出字段”未被设置，Python 就会计算路径配置来填充未设置的字段。 如果 `module_search_paths_set` 等于 `0`，则 `module_search_paths` 将被覆盖并且 `module_search_paths_set` 将被设置为 `1`。

通过显式地设置上述所有路径配置输出字段可以完全忽略计算默认路径配置的函数。 即使字符串不为空也会被视为已设置。 如果 `module_search_paths_set` 被设为 `1` 则 `module_search_paths` 会被视为已设置。 在这种情况下，`module_search_paths` 将不加修改地被使用。

将 `pathconfig_warnings` 设为 `0` 以便在计算路径配置时抑制警告（仅限 Unix，Windows 不会记录任何警告）。

如果 `base_prefix` 或 `base_exec_prefix` 字段未设置，它们将分别从 `prefix` 和 `exec_prefix` 继承其值。

`Py_RunMain()` 和 [`Py_Main()`](veryhigh.md#c.Py_Main "Py_Main") 将修改 [`sys.path`](../library/sys.md#sys.path "sys.path"):

  * 如果 `run_filename` 已设置并且是一个包含 `__main__.py` 脚本的目录，则会将 `run_filename` 添加到 [`sys.path`](../library/sys.md#sys.path "sys.path") 的开头。

  * 如果 `isolated` 为零：

    * 如果设置了 `run_module`，则将当前目录添加到 [`sys.path`](../library/sys.md#sys.path "sys.path") 的开头。 如果无法读取当前目录则不执行任何操作。

    * 如果设置了 `run_filename`，则将文件名的目录添加到 [`sys.path`](../library/sys.md#sys.path "sys.path") 的开头。

    * 在其他情况下，则将一个空字符串添加到 [`sys.path`](../library/sys.md#sys.path "sys.path") 的开头。

如果 `site_import` 为非零值，则 [`sys.path`](../library/sys.md#sys.path "sys.path") 可通过 [`site`](../library/site.md#module-site "site: Module responsible for site-specific configuration.") 模块修改。 如果 `user_site_directory` 为非零值且用户的 site-package 目录存在，则 [`site`](../library/site.md#module-site "site: Module responsible for site-specific configuration.") 模块会将用户的 site-package 目录附加到 [`sys.path`](../library/sys.md#sys.path "sys.path")。

路径配置会使用以下配置文件：

  * `pyvenv.cfg`

  * `._pth` 文件 (例如: `python._pth`)

  * `pybuilddir.txt` (仅Unix)

如果存在 `._pth` 文件:

  * 将 `isolated` 设为 `1`。

  * 将 `use_environment` 设为 `0`。

  * 将 `site_import` 设为 `0`。

  * 将 `safe_path` 设为 `1`。

The `__PYVENV_LAUNCHER__` environment variable is used to set `PyConfig.base_executable`.

## Py_RunMain()¶

int Py_RunMain(void)¶  

    

~~~
执行在命令行或配置中指定的命令 (`PyConfig.run_command`)、脚本 (`PyConfig.run_filename`) 或模块 (`PyConfig.run_module`)。

在默认情况下如果使用了 [`-i`](../using/cmdline.md#cmdoption-i) 选项，则运行 REPL。

最后，终结化 Python 并返回一个可传递给 `exit()` 函数的退出状态。

请参阅 Python 配置 查看一个使用 `Py_RunMain()` 在隔离模式下始终运行自定义 Python 的示例。

## Py_GetArgcArgv()¶

void Py_GetArgcArgv(int *argc, wchar_t ***argv)¶  
~~~
    

~~~
在 Python 修改原始命令行参数之前，获取这些参数。

另请参阅 `PyConfig.orig_argv` 成员。

## 多阶段初始化私有暂定 API¶

本节介绍的私有暂定 API 引入了多阶段初始化，它是 [**PEP 432**](https://peps.python.org/pep-0432/) 的核心特性：

  * “核心”初始化阶段，“最小化的基本 Python”：

    * 内置类型；

    * 内置异常；

    * 内置和已冻结模块；

    * [`sys`](../library/sys.md#module-sys "sys: Access system-specific parameters and functions.") 模块仅部分初始化（例如: [`sys.path`](../library/sys.md#sys.path "sys.path") 尚不存在）。

  * "主要"初始化阶段，Python 被完全初始化：

    * 安装并配置 [`importlib`](../library/importlib.md#module-importlib "importlib: The implementation of the import machinery.")；

    * 应用 路径配置；

    * 安装信号处理句柄；

    * 完成 [`sys`](../library/sys.md#module-sys "sys: Access system-specific parameters and functions.") 模块初始化 (例如：创建 [`sys.stdout`](../library/sys.md#sys.stdout "sys.stdout") 和 [`sys.path`](../library/sys.md#sys.path "sys.path"))；

    * 启用 [`faulthandler`](../library/faulthandler.md#module-faulthandler "faulthandler: Dump the Python traceback.") 和 [`tracemalloc`](../library/tracemalloc.md#module-tracemalloc "tracemalloc: Trace memory allocations.") 等可选功能；

    * 导入 [`site`](../library/site.md#module-site "site: Module responsible for site-specific configuration.") 模块；

    * 等等.

私有临时API：

  * `PyConfig._init_main`: 如果设为 `0`，`Py_InitializeFromConfig()` 将在“核心”初始化阶段停止。

PyStatus _Py_InitializeMain(void)¶  
~~~
    

~~~
进入“主要”初始化阶段，完成 Python 初始化。

在“核心”阶段不会导入任何模块，也不会配置 `importlib` 模块: 路径配置 只会在“主要”阶段期间应用。 这可能允许在 Python 中定制 Python 以覆盖或微调 路径配置，也可能会安装自定义的 [`sys.meta_path`](../library/sys.md#sys.meta_path "sys.meta_path") 导入器或导入钩子等等。

在核心阶段之后主要阶段之前，将有可能在 Python 中计算 路径配置，这是 [**PEP 432**](https://peps.python.org/pep-0432/) 的动机之一。

“核心”阶段并没有完整的定义：在这一阶段什么应该可用什么不应该可用都尚未被指明。 该 API 被标记为私有和暂定的：也就是说该 API 可以随时被修改甚至被移除直到设计出适用的公共 API。

在“核心”和“主要”初始化阶段之间运行 Python 代码的示例:
~~~
    
    
~~~
void init_python(void)
{
    PyStatus status;

    PyConfig config;
    PyConfig_InitPythonConfig(&config);
    config._init_main = 0;

    /* ... customize 'config' configuration ... */

    status = Py_InitializeFromConfig(&config);
    PyConfig_Clear(&config);
    if (PyStatus_Exception(status)) {
        Py_ExitStatusException(status);
    }

    /* Use sys.stderr because sys.stdout is only created
       by _Py_InitializeMain() */
    int res = PyRun_SimpleString(
        "import sys; "
        "print('Run Python code before _Py_InitializeMain', "
               "file=sys.stderr)");
    if (res < 0) {
        exit(1);
    }

    /* ... put more configuration code here ... */

    status = _Py_InitializeMain();
    if (PyStatus_Exception(status)) {
        Py_ExitStatusException(status);
    }
}
~~~

