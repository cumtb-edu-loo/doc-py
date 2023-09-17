# 初始化，终结和线程¶

请参阅 [Python 初始化配置](init_config.md#init-config) 。

## 在Python初始化之前¶

在一个植入了 Python 的应用程序中，`Py_Initialize()` 函数必须在任何其他 Python/C API 函数之前被调用；例外的只有个别函数和 全局配置变量。

在初始化Python之前，可以安全地调用以下函数：

  * 配置函数：

    * [`PyImport_AppendInittab()`](10.C%20API接口/import.md#c.PyImport_AppendInittab "PyImport_AppendInittab")

    * [`PyImport_ExtendInittab()`](10.C%20API接口/import.md#c.PyImport_ExtendInittab "PyImport_ExtendInittab")

    * `PyInitFrozenExtensions()`

    * [`PyMem_SetAllocator()`](memory.md#c.PyMem_SetAllocator "PyMem_SetAllocator")

    * [`PyMem_SetupDebugHooks()`](memory.md#c.PyMem_SetupDebugHooks "PyMem_SetupDebugHooks")

    * [`PyObject_SetArenaAllocator()`](memory.md#c.PyObject_SetArenaAllocator "PyObject_SetArenaAllocator")

    * [`PySys_ResetWarnOptions()`](10.C%20API接口/sys.md#c.PySys_ResetWarnOptions "PySys_ResetWarnOptions")

  * 信息函数：

    * `Py_IsInitialized()`

    * [`PyMem_GetAllocator()`](memory.md#c.PyMem_GetAllocator "PyMem_GetAllocator")

    * [`PyObject_GetArenaAllocator()`](memory.md#c.PyObject_GetArenaAllocator "PyObject_GetArenaAllocator")

    * `Py_GetBuildInfo()`

    * `Py_GetCompiler()`

    * `Py_GetCopyright()`

    * `Py_GetPlatform()`

    * `Py_GetVersion()`

  * 工具

    * [`Py_DecodeLocale()`](10.C%20API接口/sys.md#c.Py_DecodeLocale "Py_DecodeLocale")

  * 内存分配器：

    * [`PyMem_RawMalloc()`](memory.md#c.PyMem_RawMalloc "PyMem_RawMalloc")

    * [`PyMem_RawRealloc()`](memory.md#c.PyMem_RawRealloc "PyMem_RawRealloc")

    * [`PyMem_RawCalloc()`](memory.md#c.PyMem_RawCalloc "PyMem_RawCalloc")

    * [`PyMem_RawFree()`](memory.md#c.PyMem_RawFree "PyMem_RawFree")

备注

The following functions **should not be called** before `Py_Initialize()`: [`Py_EncodeLocale()`](10.C%20API接口/sys.md#c.Py_EncodeLocale "Py_EncodeLocale"), `Py_GetPath()`, `Py_GetPrefix()`, `Py_GetExecPrefix()`, `Py_GetProgramFullPath()`, `Py_GetPythonHome()`, and `Py_GetProgramName()`.

## 全局配置变量¶

Python 有负责控制全局配置中不同特性和选项的变量。这些标志默认被 [命令行选项](cmdline.md#using-on-interface-options)。

当一个选项设置一个旗标时，该旗标的值将是设置选项的次数。 例如，`-b` 会将 `Py_BytesWarningFlag` 设为 1 而 `-bb` 会将 `Py_BytesWarningFlag` 设为 2.

int Py_BytesWarningFlag¶  

    

~~~
此 API 仅为向下兼容而保留：应当改为设置 [`PyConfig.bytes_warning`](init_config.md#c.PyConfig.bytes_warning "PyConfig.bytes_warning")，参见 [Python 初始化配置](init_config.md#init-config)。

当将 [`bytes`](../library/stdtypes.md#bytes "bytes") 或 [`bytearray`](../library/stdtypes.md#bytearray "bytearray") 与 [`str`](../library/stdtypes.md#str "str") 比较或者将 [`bytes`](../library/stdtypes.md#bytes "bytes") 与 [`int`](../library/functions.md#int "int") 比较时发出警告。 如果大于等于 `2` 则报错。

由 [`-b`](../using/cmdline.md#cmdoption-b) 选项设置。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_DebugFlag¶  
~~~
    

~~~
此 API 仅为向下兼容而保留：应当改为设置 [`PyConfig.parser_debug`](init_config.md#c.PyConfig.parser_debug "PyConfig.parser_debug")，参见 [Python 初始化配置](init_config.md#init-config)。

开启解析器调试输出（限专家使用，依赖于编译选项）。

由 [`-d`](../using/cmdline.md#cmdoption-d) 选项和 [`PYTHONDEBUG`](../using/cmdline.md#envvar-PYTHONDEBUG) 环境变量设置。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_DontWriteBytecodeFlag¶  
~~~
    

~~~
此 API 仅为向下兼容而保留：应当改为设置 [`PyConfig.write_bytecode`](init_config.md#c.PyConfig.write_bytecode "PyConfig.write_bytecode")，参见 [Python 初始化配置](init_config.md#init-config)。

如果设置为非零, Python 不会在导入源代码时尝试写入 `.pyc` 文件

由 [`-B`](../using/cmdline.md#cmdoption-B) 选项和 [`PYTHONDONTWRITEBYTECODE`](../using/cmdline.md#envvar-PYTHONDONTWRITEBYTECODE) 环境变量设置。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_FrozenFlag¶  
~~~
    

~~~
此 API 仅为向下兼容而保留：应当改为设置 [`PyConfig.pathconfig_warnings`](init_config.md#c.PyConfig.pathconfig_warnings "PyConfig.pathconfig_warnings")，参见 [Python 初始化配置](init_config.md#init-config)。

当在 `Py_GetPath()` 中计算模块搜索路径时屏蔽错误消息。

由 `_freeze_importlib` 和 `frozenmain` 程序使用的私有旗标。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_HashRandomizationFlag¶  
~~~
    

~~~
此 API 仅为向下兼容而保留：应当改为设置 [`PyConfig.hash_seed`](init_config.md#c.PyConfig.hash_seed "PyConfig.hash_seed") 和 [`PyConfig.use_hash_seed`](init_config.md#c.PyConfig.use_hash_seed "PyConfig.use_hash_seed")，参见 [Python 初始化配置](init_config.md#init-config)。

如果 [`PYTHONHASHSEED`](../using/cmdline.md#envvar-PYTHONHASHSEED) 环境变量被设为非空字符串则设为 `1`。

如果该旗标为非零值，则读取 [`PYTHONHASHSEED`](../using/cmdline.md#envvar-PYTHONHASHSEED) 环境变量来初始化加密哈希种子。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_IgnoreEnvironmentFlag¶  
~~~
    

~~~
此 API 仅为向下兼容而保留：应当改为设置 [`PyConfig.use_environment`](init_config.md#c.PyConfig.use_environment "PyConfig.use_environment")，参见 [Python 初始化配置](init_config.md#init-config)。

忽略所有 `PYTHON*` 环境变量，例如可能设置的 [`PYTHONPATH`](../using/cmdline.md#envvar-PYTHONPATH) 和 [`PYTHONHOME`](../using/cmdline.md#envvar-PYTHONHOME)。

由 [`-E`](../using/cmdline.md#cmdoption-E) 和 [`-I`](../using/cmdline.md#cmdoption-I) 选项设置。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_InspectFlag¶  
~~~
    

~~~
此 API 被保留用于向下兼容：应当改为采用设置 [`PyConfig.inspect`](init_config.md#c.PyConfig.inspect "PyConfig.inspect")，参见 [Python 初始化配置](init_config.md#init-config)。

当将脚本作为第一个参数传入或是使用了 [`-c`](../using/cmdline.md#cmdoption-c) 选项时，则会在执行该脚本或命令后进入交互模式，即使在 [`sys.stdin`](../library/sys.md#sys.stdin "sys.stdin") 并非一个终端时也是如此。

由 [`-i`](../using/cmdline.md#cmdoption-i) 选项和 [`PYTHONINSPECT`](../using/cmdline.md#envvar-PYTHONINSPECT) 环境变量设置。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_InteractiveFlag¶  
~~~
    

~~~
此 API 被保留用于向下兼容：应当改为采用设置 [`PyConfig.interactive`](init_config.md#c.PyConfig.interactive "PyConfig.interactive")，参见 [Python 初始化配置](init_config.md#init-config)。

由 [`-i`](../using/cmdline.md#cmdoption-i) 选项设置。

自 3.12 版本弃用.

int Py_IsolatedFlag¶  
~~~
    

~~~
此 API 被保留用于向下兼容：应当改为设置 [`PyConfig.isolated`](init_config.md#c.PyConfig.isolated "PyConfig.isolated")，参见 [Python 初始化配置](init_config.md#init-config)。

以隔离模式运行 Python. 在隔离模式下 [`sys.path`](../library/sys.md#sys.path "sys.path") 将不包含脚本的目录或用户的 site-packages 目录。

由 [`-I`](../using/cmdline.md#cmdoption-I) 选项设置。

在 3.4 版本加入.

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_LegacyWindowsFSEncodingFlag¶  
~~~
    

~~~
此 API 被保留用于向下兼容：应当改为设置 [`PyPreConfig.legacy_windows_fs_encoding`](init_config.md#c.PyPreConfig.legacy_windows_fs_encoding "PyPreConfig.legacy_windows_fs_encoding")，参见 [Python 初始化配置](init_config.md#init-config)。

如果该旗标为非零值，则使用 `mbcs` 编码和``replace`` 错误处理句柄，而不是 UTF-8 编码和 `surrogatepass` 错误处理句柄作用 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler)。

如果 [`PYTHONLEGACYWINDOWSFSENCODING`](../using/cmdline.md#envvar-PYTHONLEGACYWINDOWSFSENCODING) 环境变量被设为非空字符串则设为 `1`。

更多详情请参阅 [**PEP 529**](https://peps.python.org/pep-0529/)。

[可用性](../library/intro.md#availability): Windows。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_LegacyWindowsStdioFlag¶  
~~~
    

~~~
此 API 被保留用于向下兼容：应当改为设置 [`PyConfig.legacy_windows_stdio`](init_config.md#c.PyConfig.legacy_windows_stdio "PyConfig.legacy_windows_stdio")，参见 [Python 初始化配置](init_config.md#init-config)。

如果该旗标为非零值，则会使用 [`io.FileIO`](../library/io.md#io.FileIO "io.FileIO") 而不是 `io._WindowsConsoleIO` 作为 [`sys`](../library/sys.md#module-sys "sys: Access system-specific parameters and functions.") 标准流。

如果 [`PYTHONLEGACYWINDOWSSTDIO`](../using/cmdline.md#envvar-PYTHONLEGACYWINDOWSSTDIO) 环境变量被设为非空字符串则设为 `1`。

有关更多详细信息，请参阅 [**PEP 528**](https://peps.python.org/pep-0528/)。

[可用性](../library/intro.md#availability): Windows。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_NoSiteFlag¶  
~~~
    

~~~
此 API 被保留用于向下兼容：应当改为设置 [`PyConfig.site_import`](init_config.md#c.PyConfig.site_import "PyConfig.site_import")，参见 [Python 初始化配置](init_config.md#init-config)。

禁用 [`site`](../library/site.md#module-site "site: Module responsible for site-specific configuration.") 的导入及其所附带的基于站点对 [`sys.path`](../library/sys.md#sys.path "sys.path") 的操作。 如果 [`site`](../library/site.md#module-site "site: Module responsible for site-specific configuration.") 会在稍后被显式地导入也会禁用这些操作 (如果你希望触发它们则应调用 [`site.main()`](../library/site.md#site.main "site.main"))。

由 [`-S`](../using/cmdline.md#cmdoption-S) 选项设置。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_NoUserSiteDirectory¶  
~~~
    

~~~
此 API 被保留用于向下兼容：应当改为设置 [`PyConfig.user_site_directory`](init_config.md#c.PyConfig.user_site_directory "PyConfig.user_site_directory")，参见 [Python 初始化配置](init_config.md#init-config)。

不要将 [`用户 site-packages 目录`](../library/site.md#site.USER_SITE "site.USER_SITE") 添加到 [`sys.path`](../library/sys.md#sys.path "sys.path")。

由 [`-s`](../using/cmdline.md#cmdoption-s) 和 [`-I`](../using/cmdline.md#cmdoption-I) 选项以及 [`PYTHONNOUSERSITE`](../using/cmdline.md#envvar-PYTHONNOUSERSITE) 环境变量设置。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_OptimizeFlag¶  
~~~
    

~~~
此 API 被保留用于向下兼容：应当改为 [`PyConfig.optimization_level`](init_config.md#c.PyConfig.optimization_level "PyConfig.optimization_level")，参见 [Python 初始化配置](init_config.md#init-config)。

由 [`-O`](../using/cmdline.md#cmdoption-O) 选项和 [`PYTHONOPTIMIZE`](../using/cmdline.md#envvar-PYTHONOPTIMIZE) 环境变量设置。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_QuietFlag¶  
~~~
    

~~~
此 API 被保留用于向下兼容：应当改为设置 [`PyConfig.quiet`](init_config.md#c.PyConfig.quiet "PyConfig.quiet")，参见 [Python 初始化配置](init_config.md#init-config)。

即使在交互模式下也不显示版权和版本信息。

由 [`-q`](../using/cmdline.md#cmdoption-q) 选项设置。

在 3.2 版本加入.

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_UnbufferedStdioFlag¶  
~~~
    

~~~
此 API 被保留用于向下兼容：应当改为设置 [`PyConfig.buffered_stdio`](init_config.md#c.PyConfig.buffered_stdio "PyConfig.buffered_stdio")，参见 [Python 初始化配置](init_config.md#init-config)。

强制 stdout 和 stderr 流不带缓冲。

由 [`-u`](../using/cmdline.md#cmdoption-u) 选项和 [`PYTHONUNBUFFERED`](../using/cmdline.md#envvar-PYTHONUNBUFFERED) 环境变量设置。

从 3.12 版起不建议使用，将在 3.14 版中移除.

int Py_VerboseFlag¶  
~~~
    

~~~
此 API 被保留用于向下兼容：应当改为设置 [`PyConfig.verbose`](init_config.md#c.PyConfig.verbose "PyConfig.verbose")，参见 [Python 初始化配置](init_config.md#init-config)。

每次初始化模块时打印一条消息，显示加载模块的位置（文件名或内置模块）。 如果大于或等于 `2`，则为搜索模块时检查的每个文件打印一条消息。 此外还会在退出时提供模块清理信息。

由 [`-v`](../using/cmdline.md#cmdoption-1) 选项和 [`PYTHONVERBOSE`](../using/cmdline.md#envvar-PYTHONVERBOSE) 环境变量设置。

从 3.12 版起不建议使用，将在 3.14 版中移除.

## 初始化和最终化解释器¶

void Py_Initialize()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

初始化 Python 解释器。 在嵌入 Python 的应用程序中，它应当在使用任何其他 Python/C API 函数之前被调用；请参阅 在 Python 初始化之前 了解少数的例外情况。

This initializes the table of loaded modules (`sys.modules`), and creates the fundamental modules [`builtins`](../library/builtins.md#module-builtins "builtins: The module that provides the built-in namespace."), [`__main__`](../library/__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") and [`sys`](../library/sys.md#module-sys "sys: Access system-specific parameters and functions."). It also initializes the module search path (`sys.path`). It does not set `sys.argv`; use the new [`PyConfig`](init_config.md#c.PyConfig "PyConfig") API of the [Python Initialization Configuration](init_config.md#init-config) for that. This is a no-op when called for a second time (without calling `Py_FinalizeEx()` first). There is no return value; it is a fatal error if the initialization fails.

使用 [`Py_InitializeFromConfig()`](init_config.md#c.Py_InitializeFromConfig "Py_InitializeFromConfig") 函数自定义 [Python 初始化配置](init_config.md#init-config)。

备注

在 Windows 上，将控制台模式从 `O_TEXT` 改为 `O_BINARY`，这还将影响使用 C 运行时的非 Python 的控制台使用。

void Py_InitializeEx(int initsigs)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果 _initsigs_ 为 `1` 则该函数的工作方式与 `Py_Initialize()` 类似。 如果 _initsigs_ 为 `0`，它将跳过信号处理句柄的初始化注册，这在嵌入 Python 时可能会很有用处。

使用 [`Py_InitializeFromConfig()`](init_config.md#c.Py_InitializeFromConfig "Py_InitializeFromConfig") 函数自定义 [Python 初始化配置](init_config.md#init-config)。

int Py_IsInitialized()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果 Python 解释器已初始化，则返回真值（非零）；否则返回假值（零）。 在调用 `Py_FinalizeEx()` 之后，此函数将返回假值直到 `Py_Initialize()` 再次被调用。

int Py_IsFinalizing()¶  
~~~
    

~~~
Return true (non-zero) if the main Python interpreter is [shutting down](../glossary.md#term-interpreter-shutdown). Return false (zero) otherwise.

在 3.13 版本加入.

int Py_FinalizeEx()¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.6._

撤销 `Py_Initialize()` 所做的所有初始化操作和后续对 Python/C API 函数的使用，并销毁自上次调用 `Py_Initialize()` 以来创建但尚未销毁的所有子解释器（参见下文 `Py_NewInterpreter()` 一节)。 在理想情况下，这会释放 Python 解释器分配的所有内存。 当第二次调用时（在未再次调用 `Py_Initialize()` 的情况下），这将不执行任何操作。 正常情况下返回值是 `0`。 如果在最终化（刷新缓冲数据）过程中出现错误，则返回 `-1`。

提供此函数的原因有很多。嵌入应用程序可能希望重新启动Python，而不必重新启动应用程序本身。从动态可加载库（或DLL）加载Python解释器的应用程序可能希望在卸载DLL之前释放Python分配的所有内存。在搜索应用程序内存泄漏的过程中，开发人员可能希望在退出应用程序之前释放Python分配的所有内存。

**程序问题和注意事项:** 模块和模块中对象的销毁是按随机顺序进行的；这可能导致依赖于其他对象（甚至函数）或模块的析构器（即 [`__del__()`](../reference/datamodel.md#object.__del__ "object.__del__") 方法）出错。 Python 所加载的动态加载扩展模块不会被卸载。 Python 解释器所分配的少量内存可能不会被释放（如果发现内存泄漏，请报告问题）。 对象间循环引用所占用的内存不会被释放。 扩展模块所分配的某些内存可能不会被释放。 如果某些扩展的初始化例程被调用多次它们可能无法正常工作；如果应用程序多次调用了 `Py_Initialize()` 和 `Py_FinalizeEx()` 就可能发生这种情况。

引发一个 [审计事件](../library/sys.md#auditing) `cpython._PySys_ClearAuditHooks`，不附带任何参数。

在 3.6 版本加入.

void Py_Finalize()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是一个不考虑返回值的 `Py_FinalizeEx()` 的向下兼容版本。

## 进程级参数¶

wchar_t *Py_GetProgramName()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return the program name set with [`PyConfig.program_name`](init_config.md#c.PyConfig.program_name "PyConfig.program_name"), or the default. The returned string points into static storage; the caller should not modify its value.

此函数不应在 `Py_Initialize()` 之前被调用，否则将返回 `NULL`。

在 3.10 版本发生变更: 现在如果它在 `Py_Initialize()` 之前被调用将返回 `NULL`。

从 3.13 版起不建议使用，将在 3.15 版中移除: Get [`sys.executable`](../library/sys.md#sys.executable "sys.executable") instead.

wchar_t *Py_GetPrefix()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return the _prefix_ for installed platform-independent files. This is derived through a number of complicated rules from the program name set with [`PyConfig.program_name`](init_config.md#c.PyConfig.program_name "PyConfig.program_name") and some environment variables; for example, if the program name is `'/usr/local/bin/python'`, the prefix is `'/usr/local'`. The returned string points into static storage; the caller should not modify its value. This corresponds to the **prefix** variable in the top-level `Makefile` and the [`--prefix`](../using/configure.md#cmdoption-prefix) argument to the **configure** script at build time. The value is available to Python code as `sys.prefix`. It is only useful on Unix. See also the next function.

此函数不应在 `Py_Initialize()` 之前被调用，否则将返回 `NULL`。

在 3.10 版本发生变更: 现在如果它在 `Py_Initialize()` 之前被调用将返回 `NULL`。

从 3.13 版起不建议使用，将在 3.15 版中移除: Get [`sys.prefix`](../library/sys.md#sys.prefix "sys.prefix") instead.

wchar_t *Py_GetExecPrefix()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return the _exec-prefix_ for installed platform- _dependent_ files. This is derived through a number of complicated rules from the program name set with [`PyConfig.program_name`](init_config.md#c.PyConfig.program_name "PyConfig.program_name") and some environment variables; for example, if the program name is `'/usr/local/bin/python'`, the exec-prefix is `'/usr/local'`. The returned string points into static storage; the caller should not modify its value. This corresponds to the **exec_prefix** variable in the top-level `Makefile` and the `--exec-prefix` argument to the **configure** script at build time. The value is available to Python code as `sys.exec_prefix`. It is only useful on Unix.

Background: The exec-prefix differs from the prefix when platform dependent files (such as executables and shared libraries) are installed in a different directory tree. In a typical installation, platform dependent files may be installed in the `/usr/local/plat` subtree while platform independent may be installed in `/usr/local`.

Generally speaking, a platform is a combination of hardware and software families, e.g. Sparc machines running the Solaris 2.x operating system are considered the same platform, but Intel machines running Solaris 2.x are another platform, and Intel machines running Linux are yet another platform. Different major revisions of the same operating system generally also form different platforms. Non-Unix operating systems are a different story; the installation strategies on those systems are so different that the prefix and exec-prefix are meaningless, and set to the empty string. Note that compiled Python bytecode files are platform independent (but not independent from the Python version by which they were compiled!).

System administrators will know how to configure the **mount** or **automount** programs to share `/usr/local` between platforms while having `/usr/local/plat` be a different filesystem for each platform.

此函数不应在 `Py_Initialize()` 之前被调用，否则将返回 `NULL`。

在 3.10 版本发生变更: 现在如果它在 `Py_Initialize()` 之前被调用将返回 `NULL`。

从 3.13 版起不建议使用，将在 3.15 版中移除: Get [`sys.exec_prefix`](../library/sys.md#sys.exec_prefix "sys.exec_prefix") instead.

wchar_t *Py_GetProgramFullPath()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return the full program name of the Python executable; this is computed as a side-effect of deriving the default module search path from the program name (set by [`PyConfig.program_name`](init_config.md#c.PyConfig.program_name "PyConfig.program_name")). The returned string points into static storage; the caller should not modify its value. The value is available to Python code as `sys.executable`.

此函数不应在 `Py_Initialize()` 之前被调用，否则将返回 `NULL`。

在 3.10 版本发生变更: 现在如果它在 `Py_Initialize()` 之前被调用将返回 `NULL`。

从 3.13 版起不建议使用，将在 3.15 版中移除: Get [`sys.executable`](../library/sys.md#sys.executable "sys.executable") instead.

wchar_t *Py_GetPath()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return the default module search path; this is computed from the program name (set by [`PyConfig.program_name`](init_config.md#c.PyConfig.program_name "PyConfig.program_name")) and some environment variables. The returned string consists of a series of directory names separated by a platform dependent delimiter character. The delimiter character is `':'` on Unix and macOS, `';'` on Windows. The returned string points into static storage; the caller should not modify its value. The list [`sys.path`](../library/sys.md#sys.path "sys.path") is initialized with this value on interpreter startup; it can be (and usually is) modified later to change the search path for loading modules.

此函数不应在 `Py_Initialize()` 之前被调用，否则将返回 `NULL`。

在 3.10 版本发生变更: 现在如果它在 `Py_Initialize()` 之前被调用将返回 `NULL`。

从 3.13 版起不建议使用，将在 3.15 版中移除: Get [`sys.path`](../library/sys.md#sys.path "sys.path") instead.

const char *Py_GetVersion()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return the version of this Python interpreter. This is a string that looks something like
~~~
    
    
~~~
"3.0a5+ (py3k:63103M, May 12 2008, 00:53:55) \n[GCC 4.2.3]"
~~~

The first word (up to the first space character) is the current Python version; the first characters are the major and minor version separated by a period. The returned string points into static storage; the caller should not modify its value. The value is available to Python code as [`sys.version`](3.标准库/sys.md#sys.version "sys.version").

See also the [`Py_Version`](apiabiversion.md#c.Py_Version "Py_Version") constant.

const char *Py_GetPlatform()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return the platform identifier for the current platform. On Unix, this is formed from the "official" name of the operating system, converted to lower case, followed by the major revision number; e.g., for Solaris 2.x, which is also known as SunOS 5.x, the value is `'sunos5'`. On macOS, it is `'darwin'`. On Windows, it is `'win'`. The returned string points into static storage; the caller should not modify its value. The value is available to Python code as `sys.platform`.

const char *Py_GetCopyright()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return the official copyright string for the current Python version, for example

`'Copyright 1991-1995 Stichting Mathematisch Centrum, Amsterdam'`

返回的字符串指向静态存储；调用者不应修改其值。 Python 代码可通过 `sys.copyright` 获取该值。

const char *Py_GetCompiler()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回用于编译当前 Python 版本的编译器指令，为带方括号的形式，例如:

    
    
~~~
"[GCC 2.7.2.2]"
~~~

返回的字符串指向静态存储；调用者不应修改其值。 Python 代码可以从变量 `sys.version` 中获取该值。

const char *Py_GetBuildInfo()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回有关当前Python解释器实例的序列号和构建日期和时间的信息，例如：

    
    
~~~
"#67, Aug  1 1997, 22:34:28"
~~~

返回的字符串指向静态存储；调用者不应修改其值。 Python 代码可以从变量 `sys.version` 中获取该值。

wchar_t *Py_GetPythonHome()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return the default "home", that is, the value set by [`PyConfig.home`](init_config.md#c.PyConfig.home "PyConfig.home"), or the value of the [`PYTHONHOME`](cmdline.md#envvar-PYTHONHOME) environment variable if it is set.

此函数不应在 `Py_Initialize()` 之前被调用，否则将返回 `NULL`。

在 3.10 版本发生变更: 现在如果它在 `Py_Initialize()` 之前被调用将返回 `NULL`。

从 3.13 版起不建议使用，将在 3.15 版中移除: Get [`PyConfig.home`](init_config.md#c.PyConfig.home "PyConfig.home") or [`PYTHONHOME`](cmdline.md#envvar-PYTHONHOME) environment variable instead.

## 线程状态和全局解释器锁¶

The Python interpreter is not fully thread-safe. In order to support multi-threaded Python programs, there's a global lock, called the [global interpreter lock](../glossary.md#term-global-interpreter-lock) or [GIL](../glossary.md#term-GIL), that must be held by the current thread before it can safely access Python objects. Without the lock, even the simplest operations could cause problems in a multi-threaded program: for example, when two threads simultaneously increment the reference count of the same object, the reference count could end up being incremented only once instead of twice.

Therefore, the rule exists that only the thread that has acquired the [GIL](../glossary.md#term-GIL) may operate on Python objects or call Python/C API functions. In order to emulate concurrency of execution, the interpreter regularly tries to switch threads (see [`sys.setswitchinterval()`](3.标准库/sys.md#sys.setswitchinterval "sys.setswitchinterval")). The lock is also released around potentially blocking I/O operations like reading or writing a file, so that other Python threads can run in the meantime.

The Python interpreter keeps some thread-specific bookkeeping information inside a data structure called `PyThreadState`. There's also one global variable pointing to the current `PyThreadState`: it can be retrieved using `PyThreadState_Get()`.

### 从扩展扩展代码中释放 GIL¶

大多数操作 [GIL](../glossary.md#term-GIL) 的扩展代码具有以下简单结构：

    
    
~~~
Save the thread state in a local variable.
Release the global interpreter lock.
... Do some blocking I/O operation ...
Reacquire the global interpreter lock.
Restore the thread state from the local variable.
~~~

这是如此常用因此增加了一对宏来简化它:

    
    
~~~
Py_BEGIN_ALLOW_THREADS
... Do some blocking I/O operation ...
Py_END_ALLOW_THREADS
~~~

`Py_BEGIN_ALLOW_THREADS` 宏将打开一个新块并声明一个隐藏的局部变量；`Py_END_ALLOW_THREADS` 宏将关闭这个块。

上面的代码块可扩展为下面的代码:

    
    
~~~
PyThreadState *_save;

_save = PyEval_SaveThread();
... Do some blocking I/O operation ...
PyEval_RestoreThread(_save);
~~~

Here is how these functions work: the global interpreter lock is used to protect the pointer to the current thread state. When releasing the lock and saving the thread state, the current thread state pointer must be retrieved before the lock is released (since another thread could immediately acquire the lock and store its own thread state in the global variable). Conversely, when acquiring the lock and restoring the thread state, the lock must be acquired before storing the thread state pointer.

备注

Calling system I/O functions is the most common use case for releasing the GIL, but it can also be useful before calling long-running computations which don't need access to Python objects, such as compression or cryptographic functions operating over memory buffers. For example, the standard [`zlib`](zlib.md#module-zlib "zlib: Low-level interface to compression and decompression routines compatible with gzip.") and [`hashlib`](hashlib.md#module-hashlib "hashlib: Secure hash and message digest algorithms.") modules release the GIL when compressing or hashing data.

### 非Python创建的线程¶

When threads are created using the dedicated Python APIs (such as the [`threading`](threading.md#module-threading "threading: Thread-based parallelism.") module), a thread state is automatically associated to them and the code showed above is therefore correct. However, when threads are created from C (for example by a third-party library with its own thread management), they don't hold the GIL, nor is there a thread state structure for them.

If you need to call Python code from these threads (often this will be part of a callback API provided by the aforementioned third-party library), you must first register these threads with the interpreter by creating a thread state data structure, then acquiring the GIL, and finally storing their thread state pointer, before you can start using the Python/C API. When you are done, you should reset the thread state pointer, release the GIL, and finally free the thread state data structure.

The `PyGILState_Ensure()` and `PyGILState_Release()` functions do all of the above automatically. The typical idiom for calling into Python from a C thread is:

    
    
~~~
PyGILState_STATE gstate;
gstate = PyGILState_Ensure();

/* Perform Python actions here. */
result = CallSomeFunction();
/* evaluate result or handle exception */

/* Release the thread. No Python API allowed beyond this point. */
PyGILState_Release(gstate);
~~~

Note that the `PyGILState_*` functions assume there is only one global interpreter (created automatically by `Py_Initialize()`). Python supports the creation of additional interpreters (using `Py_NewInterpreter()`), but mixing multiple interpreters and the `PyGILState_*` API is unsupported.

### Cautions about fork()¶

Another important thing to note about threads is their behaviour in the face of the C `fork()` call. On most systems with `fork()`, after a process forks only the thread that issued the fork will exist. This has a concrete impact both on how locks must be handled and on all stored state in CPython's runtime.

The fact that only the "current" thread remains means any locks held by other threads will never be released. Python solves this for [`os.fork()`](os.md#os.fork "os.fork") by acquiring the locks it uses internally before the fork, and releasing them afterwards. In addition, it resets any [锁对象](threading.md#lock-objects) in the child. When extending or embedding Python, there is no way to inform Python of additional (non-Python) locks that need to be acquired before or reset after a fork. OS facilities such as `pthread_atfork()` would need to be used to accomplish the same thing. Additionally, when extending or embedding Python, calling `fork()` directly rather than through [`os.fork()`](os.md#os.fork "os.fork") (and returning to or calling into Python) may result in a deadlock by one of Python's internal locks being held by a thread that is defunct after the fork. [`PyOS_AfterFork_Child()`](10.C%20API接口/sys.md#c.PyOS_AfterFork_Child "PyOS_AfterFork_Child") tries to reset the necessary locks, but is not always able to.

The fact that all other threads go away also means that CPython's runtime state there must be cleaned up properly, which [`os.fork()`](os.md#os.fork "os.fork") does. This means finalizing all other `PyThreadState` objects belonging to the current interpreter and all other `PyInterpreterState` objects. Due to this and the special nature of the "main" interpreter, `fork()` should only be called in that interpreter's "main" thread, where the CPython global runtime was originally initialized. The only exception is if `exec()` will be called immediately after.

### 高阶 API¶

These are the most commonly used types and functions when writing C extension code, or when embedding the Python interpreter:

type PyInterpreterState¶  

    _Part of the[ Limited API](stable.md#stable) (as an opaque struct)._

This data structure represents the state shared by a number of cooperating threads. Threads belonging to the same interpreter share their module administration and a few other internal items. There are no public members in this structure.

Threads belonging to different interpreters initially share nothing, except process state like available memory, open file descriptors and such. The global interpreter lock is also shared by all threads, regardless of to which interpreter they belong.

type PyThreadState¶  

    _Part of the[ Limited API](stable.md#stable) (as an opaque struct)._

This data structure represents the state of a single thread. The only public data member is:

PyInterpreterState *interp¶  

    

~~~
该线程的解释器状态。

PyThreadState *PyEval_SaveThread()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Release the global interpreter lock (if it has been created) and reset the thread state to `NULL`, returning the previous thread state (which is not `NULL`). If the lock has been created, the current thread must have acquired it.

void PyEval_RestoreThread(PyThreadState *tstate)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Acquire the global interpreter lock (if it has been created) and set the thread state to _tstate_ , which must not be `NULL`. If the lock has been created, the current thread must not have acquired it, otherwise deadlock ensues.

备注

Calling this function from a thread when the runtime is finalizing will terminate the thread, even if the thread was not created by Python. You can use `Py_IsFinalizing()` or [`sys.is_finalizing()`](../library/sys.md#sys.is_finalizing "sys.is_finalizing") to check if the interpreter is in process of being finalized before calling this function to avoid unwanted termination.

PyThreadState *PyThreadState_Get()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return the current thread state. The global interpreter lock must be held. When the current thread state is `NULL`, this issues a fatal error (so that the caller needn't check for `NULL`).

PyThreadState *PyThreadState_Swap(PyThreadState *tstate)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Swap the current thread state with the thread state given by the argument _tstate_ , which may be `NULL`. The global interpreter lock must be held and is not released.

The following functions use thread-local storage, and are not compatible with sub-interpreters:

PyGILState_STATE PyGILState_Ensure()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Ensure that the current thread is ready to call the Python C API regardless of the current state of Python, or of the global interpreter lock. This may be called as many times as desired by a thread as long as each call is matched with a call to `PyGILState_Release()`. In general, other thread-related APIs may be used between `PyGILState_Ensure()` and `PyGILState_Release()` calls as long as the thread state is restored to its previous state before the Release(). For example, normal usage of the `Py_BEGIN_ALLOW_THREADS` and `Py_END_ALLOW_THREADS` macros is acceptable.

The return value is an opaque "handle" to the thread state when `PyGILState_Ensure()` was called, and must be passed to `PyGILState_Release()` to ensure Python is left in the same state. Even though recursive calls are allowed, these handles _cannot_ be shared - each unique call to `PyGILState_Ensure()` must save the handle for its call to `PyGILState_Release()`.

When the function returns, the current thread will hold the GIL and be able to call arbitrary Python code. Failure is a fatal error.

备注

Calling this function from a thread when the runtime is finalizing will terminate the thread, even if the thread was not created by Python. You can use `Py_IsFinalizing()` or [`sys.is_finalizing()`](../library/sys.md#sys.is_finalizing "sys.is_finalizing") to check if the interpreter is in process of being finalized before calling this function to avoid unwanted termination.

void PyGILState_Release(PyGILState_STATE)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Release any resources previously acquired. After this call, Python's state will be the same as it was prior to the corresponding `PyGILState_Ensure()` call (but generally this state will be unknown to the caller, hence the use of the GILState API).

Every call to `PyGILState_Ensure()` must be matched by a call to `PyGILState_Release()` on the same thread.

PyThreadState *PyGILState_GetThisThreadState()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Get the current thread state for this thread. May return `NULL` if no GILState API has been used on the current thread. Note that the main thread always has such a thread-state, even if no auto-thread-state call has been made on the main thread. This is mainly a helper/diagnostic function.

int PyGILState_Check()¶  
~~~
    

~~~
Return `1` if the current thread is holding the GIL and `0` otherwise. This function can be called from any thread at any time. Only if it has had its Python thread state initialized and currently is holding the GIL will it return `1`. This is mainly a helper/diagnostic function. It can be useful for example in callback contexts or memory allocation functions when knowing that the GIL is locked can allow the caller to perform sensitive actions or otherwise behave differently.

在 3.4 版本加入.

The following macros are normally used without a trailing semicolon; look for example usage in the Python source distribution.

Py_BEGIN_ALLOW_THREADS¶  

    _Part of the[ Stable ABI](stable.md#stable)._

This macro expands to `{ PyThreadState *_save; _save = PyEval_SaveThread();`. Note that it contains an opening brace; it must be matched with a following `Py_END_ALLOW_THREADS` macro. See above for further discussion of this macro.

Py_END_ALLOW_THREADS¶  

    _Part of the[ Stable ABI](stable.md#stable)._

此宏扩展为 `PyEval_RestoreThread(_save); }`。 注意它包含一个右花括号；它必须与之前的 `Py_BEGIN_ALLOW_THREADS` 宏匹配。 请参阅上文以进一步讨论此宏。

Py_BLOCK_THREADS¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这个宏扩展为 `PyEval_RestoreThread(_save);`: 它等价于没有关闭花括号的 `Py_END_ALLOW_THREADS`。

Py_UNBLOCK_THREADS¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这个宏扩展为 `_save = PyEval_SaveThread();`: 它等价于没有开始花括号和变量声明的 `Py_BEGIN_ALLOW_THREADS`。

### 底层级 API¶

All of the following functions must be called after `Py_Initialize()`.

在 3.7 版本发生变更: `Py_Initialize()` now initializes the [GIL](../glossary.md#term-GIL).

PyInterpreterState *PyInterpreterState_New()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Create a new interpreter state object. The global interpreter lock need not be held, but may be held if it is necessary to serialize calls to this function.

Raises an [auditing event](../library/sys.md#auditing) `cpython.PyInterpreterState_New` with no arguments.

void PyInterpreterState_Clear(PyInterpreterState *interp)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Reset all information in an interpreter state object. The global interpreter lock must be held.

Raises an [auditing event](../library/sys.md#auditing) `cpython.PyInterpreterState_Clear` with no arguments.

void PyInterpreterState_Delete(PyInterpreterState *interp)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Destroy an interpreter state object. The global interpreter lock need not be held. The interpreter state must have been reset with a previous call to `PyInterpreterState_Clear()`.

PyThreadState *PyThreadState_New(PyInterpreterState *interp)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

创建属于给定解释器对象的新线程状态对象。全局解释器锁不需要保持，但如果需要序列化对此函数的调用，则可以保持。

void PyThreadState_Clear(PyThreadState *tstate)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Reset all information in a thread state object. The global interpreter lock must be held.

在 3.9 版本发生变更: This function now calls the `PyThreadState.on_delete` callback. Previously, that happened in `PyThreadState_Delete()`.

void PyThreadState_Delete(PyThreadState *tstate)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Destroy a thread state object. The global interpreter lock need not be held. The thread state must have been reset with a previous call to `PyThreadState_Clear()`.

void PyThreadState_DeleteCurrent(void)¶  
~~~
    

~~~
Destroy the current thread state and release the global interpreter lock. Like `PyThreadState_Delete()`, the global interpreter lock need not be held. The thread state must have been reset with a previous call to `PyThreadState_Clear()`.

[PyFrameObject](frame.md#c.PyFrameObject "PyFrameObject") *PyThreadState_GetFrame(PyThreadState *tstate)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

Get the current frame of the Python thread state _tstate_.

Return a [strong reference](../glossary.md#term-strong-reference). Return `NULL` if no frame is currently executing.

See also [`PyEval_GetFrame()`](reflection.md#c.PyEval_GetFrame "PyEval_GetFrame").

_tstate_ must not be `NULL`.

在 3.9 版本加入.

uint64_t PyThreadState_GetID(PyThreadState *tstate)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

Get the unique thread state identifier of the Python thread state _tstate_.

_tstate_ must not be `NULL`.

在 3.9 版本加入.

PyInterpreterState *PyThreadState_GetInterpreter(PyThreadState *tstate)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

Get the interpreter of the Python thread state _tstate_.

_tstate_ must not be `NULL`.

在 3.9 版本加入.

void PyThreadState_EnterTracing(PyThreadState *tstate)¶  
~~~
    

~~~
Suspend tracing and profiling in the Python thread state _tstate_.

Resume them using the `PyThreadState_LeaveTracing()` function.

在 3.11 版本加入.

void PyThreadState_LeaveTracing(PyThreadState *tstate)¶  
~~~
    

~~~
Resume tracing and profiling in the Python thread state _tstate_ suspended by the `PyThreadState_EnterTracing()` function.

See also `PyEval_SetTrace()` and `PyEval_SetProfile()` functions.

在 3.11 版本加入.

PyInterpreterState *PyInterpreterState_Get(void)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.9._

获取当前解释器。

Issue a fatal error if there no current Python thread state or no current interpreter. It cannot return NULL.

呼叫者必须持有GIL。

在 3.9 版本加入.

int64_t PyInterpreterState_GetID(PyInterpreterState *interp)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Return the interpreter's unique ID. If there was any error in doing so then `-1` is returned and an error is set.

呼叫者必须持有GIL。

在 3.7 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyInterpreterState_GetDict(PyInterpreterState *interp)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.8._

Return a dictionary in which interpreter-specific data may be stored. If this function returns `NULL` then no exception has been raised and the caller should assume no interpreter-specific dict is available.

This is not a replacement for [`PyModule_GetState()`](module.md#c.PyModule_GetState "PyModule_GetState"), which extensions should use to store interpreter-specific state information.

在 3.8 版本加入.

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*_PyFrameEvalFunction)(PyThreadState *tstate, [_PyInterpreterFrame](frame.md#c._PyInterpreterFrame "_PyInterpreterFrame") *frame, int throwflag)¶  
~~~
    

~~~
帧评估函数的类型

The _throwflag_ parameter is used by the `throw()` method of generators: if non-zero, handle the current exception.

在 3.9 版本发生变更: 此函数现在可接受一个 _tstate_ 形参。

在 3.11 版本发生变更: The _frame_ parameter changed from `PyFrameObject*` to `_PyInterpreterFrame*`.

_PyFrameEvalFunction _PyInterpreterState_GetEvalFrameFunc(PyInterpreterState *interp)¶  
~~~
    

~~~
Get the frame evaluation function.

See the [**PEP 523**](https://peps.python.org/pep-0523/) "Adding a frame evaluation API to CPython".

在 3.9 版本加入.

void _PyInterpreterState_SetEvalFrameFunc(PyInterpreterState *interp, _PyFrameEvalFunction eval_frame)¶  
~~~
    

~~~
Set the frame evaluation function.

See the [**PEP 523**](https://peps.python.org/pep-0523/) "Adding a frame evaluation API to CPython".

在 3.9 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyThreadState_GetDict()¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Return a dictionary in which extensions can store thread-specific state information. Each extension should use a unique key to use to store state in the dictionary. It is okay to call this function when no current thread state is available. If this function returns `NULL`, no exception has been raised and the caller should assume no current thread state is available.

int PyThreadState_SetAsyncExc(unsigned long id, [PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Asynchronously raise an exception in a thread. The _id_ argument is the thread id of the target thread; _exc_ is the exception object to be raised. This function does not steal any references to _exc_. To prevent naive misuse, you must write your own C extension to call this. Must be called with the GIL held. Returns the number of thread states modified; this is normally one, but will be zero if the thread id isn't found. If _exc_ is `NULL`, the pending exception (if any) for the thread is cleared. This raises no exceptions.

在 3.7 版本发生变更: The type of the _id_ parameter changed from long to unsigned long.

void PyEval_AcquireThread(PyThreadState *tstate)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Acquire the global interpreter lock and set the current thread state to _tstate_ , which must not be `NULL`. The lock must have been created earlier. If this thread already has the lock, deadlock ensues.

备注

Calling this function from a thread when the runtime is finalizing will terminate the thread, even if the thread was not created by Python. You can use `Py_IsFinalizing()` or [`sys.is_finalizing()`](../library/sys.md#sys.is_finalizing "sys.is_finalizing") to check if the interpreter is in process of being finalized before calling this function to avoid unwanted termination.

在 3.8 版本发生变更: Updated to be consistent with `PyEval_RestoreThread()`, `Py_END_ALLOW_THREADS()`, and `PyGILState_Ensure()`, and terminate the current thread if called while the interpreter is finalizing.

`PyEval_RestoreThread()` is a higher-level function which is always available (even when threads have not been initialized).

void PyEval_ReleaseThread(PyThreadState *tstate)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Reset the current thread state to `NULL` and release the global interpreter lock. The lock must have been created earlier and must be held by the current thread. The _tstate_ argument, which must not be `NULL`, is only used to check that it represents the current thread state --- if it isn't, a fatal error is reported.

`PyEval_SaveThread()` is a higher-level function which is always available (even when threads have not been initialized).

## 子解释器支持¶

While in most uses, you will only embed a single Python interpreter, there are cases where you need to create several independent interpreters in the same process and perhaps even in the same thread. Sub-interpreters allow you to do that.

The "main" interpreter is the first one created when the runtime initializes. It is usually the only Python interpreter in a process. Unlike sub-interpreters, the main interpreter has unique process-global responsibilities like signal handling. It is also responsible for execution during runtime initialization and is usually the active interpreter during runtime finalization. The `PyInterpreterState_Main()` function returns a pointer to its state.

You can switch between sub-interpreters using the `PyThreadState_Swap()` function. You can create and destroy them using the following functions:

type PyInterpreterConfig¶  
~~~
    

~~~
Structure containing most parameters to configure a sub-interpreter. Its values are used only in `Py_NewInterpreterFromConfig()` and never modified by the runtime.

在 3.12 版本加入.

结构体字段:

int use_main_obmalloc¶  
~~~
    

~~~
If this is `0` then the sub-interpreter will use its own "object" allocator state. Otherwise it will use (share) the main interpreter's.

If this is `0` then `check_multi_interp_extensions` must be `1` (non-zero). If this is `1` then `gil` must not be `PyInterpreterConfig_OWN_GIL`.

int allow_fork¶  
~~~
    

~~~
If this is `0` then the runtime will not support forking the process in any thread where the sub-interpreter is currently active. Otherwise fork is unrestricted.

Note that the [`subprocess`](../library/subprocess.md#module-subprocess "subprocess: Subprocess management.") module still works when fork is disallowed.

int allow_exec¶  
~~~
    

~~~
If this is `0` then the runtime will not support replacing the current process via exec (e.g. [`os.execv()`](../library/os.md#os.execv "os.execv")) in any thread where the sub-interpreter is currently active. Otherwise exec is unrestricted.

Note that the [`subprocess`](../library/subprocess.md#module-subprocess "subprocess: Subprocess management.") module still works when exec is disallowed.

int allow_threads¶  
~~~
    

~~~
If this is `0` then the sub-interpreter's [`threading`](../library/threading.md#module-threading "threading: Thread-based parallelism.") module won't create threads. Otherwise threads are allowed.

int allow_daemon_threads¶  
~~~
    

~~~
If this is `0` then the sub-interpreter's [`threading`](../library/threading.md#module-threading "threading: Thread-based parallelism.") module won't create daemon threads. Otherwise daemon threads are allowed (as long as `allow_threads` is non-zero).

int check_multi_interp_extensions¶  
~~~
    

~~~
If this is `0` then all extension modules may be imported, including legacy (single-phase init) modules, in any thread where the sub-interpreter is currently active. Otherwise only multi-phase init extension modules (see [**PEP 489**](https://peps.python.org/pep-0489/)) may be imported. (Also see [`Py_mod_multiple_interpreters`](module.md#c.Py_mod_multiple_interpreters "Py_mod_multiple_interpreters").)

This must be `1` (non-zero) if `use_main_obmalloc` is `0`.

int gil¶  
~~~
    

~~~
This determines the operation of the GIL for the sub-interpreter. It may be one of the following:

PyInterpreterConfig_DEFAULT_GIL¶  
~~~
    

~~~
Use the default selection (`PyInterpreterConfig_SHARED_GIL`).

PyInterpreterConfig_SHARED_GIL¶  
~~~
    

~~~
Use (share) the main interpreter's GIL.

PyInterpreterConfig_OWN_GIL¶  
~~~
    

~~~
Use the sub-interpreter's own GIL.

If this is `PyInterpreterConfig_OWN_GIL` then `PyInterpreterConfig.use_main_obmalloc` must be `0`.

[PyStatus](init_config.md#c.PyStatus "PyStatus") Py_NewInterpreterFromConfig(PyThreadState **tstate_p, const PyInterpreterConfig *config)¶  
~~~
    

~~~
Create a new sub-interpreter. This is an (almost) totally separate environment for the execution of Python code. In particular, the new interpreter has separate, independent versions of all imported modules, including the fundamental modules [`builtins`](../library/builtins.md#module-builtins "builtins: The module that provides the built-in namespace."), [`__main__`](../library/__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") and [`sys`](../library/sys.md#module-sys "sys: Access system-specific parameters and functions."). The table of loaded modules (`sys.modules`) and the module search path (`sys.path`) are also separate. The new environment has no `sys.argv` variable. It has new standard I/O stream file objects `sys.stdin`, `sys.stdout` and `sys.stderr` (however these refer to the same underlying file descriptors).

The given _config_ controls the options with which the interpreter is initialized.

Upon success, _tstate_p_ will be set to the first thread state created in the new sub-interpreter. This thread state is made in the current thread state. Note that no actual thread is created; see the discussion of thread states below. If creation of the new interpreter is unsuccessful, _tstate_p_ is set to `NULL`; no exception is set since the exception state is stored in the current thread state and there may not be a current thread state.

Like all other Python/C API functions, the global interpreter lock must be held before calling this function and is still held when it returns. Likewise a current thread state must be set on entry. On success, the returned thread state will be set as current. If the sub-interpreter is created with its own GIL then the GIL of the calling interpreter will be released. When the function returns, the new interpreter's GIL will be held by the current thread and the previously interpreter's GIL will remain released here.

在 3.12 版本加入.

Sub-interpreters are most effective when isolated from each other, with certain functionality restricted:
~~~
    
    
~~~
PyInterpreterConfig config = {
    .use_main_obmalloc = 0,
    .allow_fork = 0,
    .allow_exec = 0,
    .allow_threads = 1,
    .allow_daemon_threads = 0,
    .check_multi_interp_extensions = 1,
    .gil = PyInterpreterConfig_OWN_GIL,
};
PyThreadState *tstate = Py_NewInterpreterFromConfig(&config);
~~~

Note that the config is used only briefly and does not get modified. During initialization the config's values are converted into various `PyInterpreterState` values. A read-only copy of the config may be stored internally on the `PyInterpreterState`.

Extension modules are shared between (sub-)interpreters as follows:

  * For modules using multi-phase initialization, e.g. [`PyModule_FromDefAndSpec()`](module.md#c.PyModule_FromDefAndSpec "PyModule_FromDefAndSpec"), a separate module object is created and initialized for each interpreter. Only C-level static and global variables are shared between these module objects.

  * For modules using single-phase initialization, e.g. [`PyModule_Create()`](module.md#c.PyModule_Create "PyModule_Create"), the first time a particular extension is imported, it is initialized normally, and a (shallow) copy of its module's dictionary is squirreled away. When the same extension is imported by another (sub-)interpreter, a new module is initialized and filled with the contents of this copy; the extension's `init` function is not called. Objects in the module's dictionary thus end up shared across (sub-)interpreters, which might cause unwanted behavior (see Bugs and caveats below).

Note that this is different from what happens when an extension is imported after the interpreter has been completely re-initialized by calling `Py_FinalizeEx()` and `Py_Initialize()`; in that case, the extension's `initmodule` function _is_ called again. As with multi-phase initialization, this means that only C-level static and global variables are shared between these modules.

PyThreadState *Py_NewInterpreter(void)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Create a new sub-interpreter. This is essentially just a wrapper around `Py_NewInterpreterFromConfig()` with a config that preserves the existing behavior. The result is an unisolated sub-interpreter that shares the main interpreter's GIL, allows fork/exec, allows daemon threads, and allows single-phase init modules.

void Py_EndInterpreter(PyThreadState *tstate)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Destroy the (sub-)interpreter represented by the given thread state. The given thread state must be the current thread state. See the discussion of thread states below. When the call returns, the current thread state is `NULL`. All thread states associated with this interpreter are destroyed. The global interpreter lock used by the target interpreter must be held before calling this function. No GIL is held when it returns.

`Py_FinalizeEx()` will destroy all sub-interpreters that haven't been explicitly destroyed at that point.

### A Per-Interpreter GIL¶

Using `Py_NewInterpreterFromConfig()` you can create a sub-interpreter that is completely isolated from other interpreters, including having its own GIL. The most important benefit of this isolation is that such an interpreter can execute Python code without being blocked by other interpreters or blocking any others. Thus a single Python process can truly take advantage of multiple CPU cores when running Python code. The isolation also encourages a different approach to concurrency than that of just using threads. (See [**PEP 554**](https://peps.python.org/pep-0554/).)

Using an isolated interpreter requires vigilance in preserving that isolation. That especially means not sharing any objects or mutable state without guarantees about thread-safety. Even objects that are otherwise immutable (e.g. `None`, `(1, 5)`) can't normally be shared because of the refcount. One simple but less-efficient approach around this is to use a global lock around all use of some state (or object). Alternately, effectively immutable objects (like integers or strings) can be made safe in spite of their refcounts by making them "immortal". In fact, this has been done for the builtin singletons, small integers, and a number of other builtin objects.

If you preserve isolation then you will have access to proper multi-core computing without the complications that come with free-threading. Failure to preserve isolation will expose you to the full consequences of free-threading, including races and hard-to-debug crashes.

Aside from that, one of the main challenges of using multiple isolated interpreters is how to communicate between them safely (not break isolation) and efficiently. The runtime and stdlib do not provide any standard approach to this yet. A future stdlib module would help mitigate the effort of preserving isolation and expose effective tools for communicating (and sharing) data between interpreters.

在 3.12 版本加入.

### 错误和警告¶

Because sub-interpreters (and the main interpreter) are part of the same process, the insulation between them isn't perfect --- for example, using low-level file operations like [`os.close()`](os.md#os.close "os.close") they can (accidentally or maliciously) affect each other's open files. Because of the way extensions are shared between (sub-)interpreters, some extensions may not work properly; this is especially likely when using single-phase initialization or (static) global variables. It is possible to insert objects created in one sub-interpreter into a namespace of another (sub-)interpreter; this should be avoided if possible.

Special care should be taken to avoid sharing user-defined functions, methods, instances or classes between sub-interpreters, since import operations executed by such objects may affect the wrong (sub-)interpreter's dictionary of loaded modules. It is equally important to avoid sharing objects from which the above are reachable.

Also note that combining this functionality with `PyGILState_*` APIs is delicate, because these APIs assume a bijection between Python thread states and OS-level threads, an assumption broken by the presence of sub-interpreters. It is highly recommended that you don't switch sub-interpreters between a pair of matching `PyGILState_Ensure()` and `PyGILState_Release()` calls. Furthermore, extensions (such as [`ctypes`](ctypes.md#module-ctypes "ctypes: A foreign function library for Python.")) using these APIs to allow calling of Python code from non-Python created threads will probably be broken when using sub-interpreters.

## 异步通知¶

A mechanism is provided to make asynchronous notifications to the main interpreter thread. These notifications take the form of a function pointer and a void pointer argument.

int Py_AddPendingCall(int (*func)(void*), void *arg)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Schedule a function to be called from the main interpreter thread. On success, `0` is returned and _func_ is queued for being called in the main thread. On failure, `-1` is returned without setting any exception.

When successfully queued, _func_ will be _eventually_ called from the main interpreter thread with the argument _arg_. It will be called asynchronously with respect to normally running Python code, but with both these conditions met:

  * on a [bytecode](../glossary.md#term-bytecode) boundary;

  * with the main thread holding the [global interpreter lock](../glossary.md#term-global-interpreter-lock) ( _func_ can therefore use the full C API).

_func_ must return `0` on success, or `-1` on failure with an exception set. _func_ won't be interrupted to perform another asynchronous notification recursively, but it can still be interrupted to switch threads if the global interpreter lock is released.

This function doesn't need a current thread state to run, and it doesn't need the global interpreter lock.

To call this function in a subinterpreter, the caller must hold the GIL. Otherwise, the function _func_ can be scheduled to be called from the wrong interpreter.

警告

This is a low-level function, only useful for very special cases. There is no guarantee that _func_ will be called as quick as possible. If the main thread is busy executing a system call, _func_ won't be called before the system call returns. This function is generally **not** suitable for calling Python code from arbitrary C threads. Instead, use the PyGILState API.

在 3.9 版本发生变更: If this function is called in a subinterpreter, the function _func_ is now scheduled to be called from the subinterpreter, rather than being called from the main interpreter. Each subinterpreter now has its own list of scheduled calls.

在 3.1 版本加入.

## 分析和跟踪¶

The Python interpreter provides some low-level support for attaching profiling and execution tracing facilities. These are used for profiling, debugging, and coverage analysis tools.

This C interface allows the profiling or tracing code to avoid the overhead of calling through Python-level callable objects, making a direct C function call instead. The essential attributes of the facility have not changed; the interface allows trace functions to be installed per-thread, and the basic events reported to the trace function are the same as had been reported to the Python-level trace functions in previous versions.

typedef int (*Py_tracefunc)([PyObject](structures.md#c.PyObject "PyObject") *obj, [PyFrameObject](frame.md#c.PyFrameObject "PyFrameObject") *frame, int what, [PyObject](structures.md#c.PyObject "PyObject") *arg)¶  

    

~~~
The type of the trace function registered using `PyEval_SetProfile()` and `PyEval_SetTrace()`. The first parameter is the object passed to the registration function as _obj_ , _frame_ is the frame object to which the event pertains, _what_ is one of the constants `PyTrace_CALL`, `PyTrace_EXCEPTION`, `PyTrace_LINE`, `PyTrace_RETURN`, `PyTrace_C_CALL`, `PyTrace_C_EXCEPTION`, `PyTrace_C_RETURN`, or `PyTrace_OPCODE`, and _arg_ depends on the value of _what_ :

_what_ 的值

|

_arg_ 的含义  
  
---|---  
  
`PyTrace_CALL`

|

总是 [`Py_None`](none.md#c.Py_None "Py_None").  
  
`PyTrace_EXCEPTION`

|

[`sys.exc_info()`](../library/sys.md#sys.exc_info "sys.exc_info") 返回的异常信息。  
  
`PyTrace_LINE`

|

总是 [`Py_None`](none.md#c.Py_None "Py_None").  
  
`PyTrace_RETURN`

|

返回给调用方的值，或者如果是由异常导致的则返回 `NULL`。  
  
`PyTrace_C_CALL`

|

正在调用函数对象。  
  
`PyTrace_C_EXCEPTION`

|

正在调用函数对象。  
  
`PyTrace_C_RETURN`

|

正在调用函数对象。  
  
`PyTrace_OPCODE`

|

总是 [`Py_None`](none.md#c.Py_None "Py_None").  
  
int PyTrace_CALL¶  
~~~
    

~~~
The value of the _what_ parameter to a `Py_tracefunc` function when a new call to a function or method is being reported, or a new entry into a generator. Note that the creation of the iterator for a generator function is not reported as there is no control transfer to the Python bytecode in the corresponding frame.

int PyTrace_EXCEPTION¶  
~~~
    

~~~
The value of the _what_ parameter to a `Py_tracefunc` function when an exception has been raised. The callback function is called with this value for _what_ when after any bytecode is processed after which the exception becomes set within the frame being executed. The effect of this is that as exception propagation causes the Python stack to unwind, the callback is called upon return to each frame as the exception propagates. Only trace functions receives these events; they are not needed by the profiler.

int PyTrace_LINE¶  
~~~
    

~~~
The value passed as the _what_ parameter to a `Py_tracefunc` function (but not a profiling function) when a line-number event is being reported. It may be disabled for a frame by setting `f_trace_lines` to _0_ on that frame.

int PyTrace_RETURN¶  
~~~
    

~~~
The value for the _what_ parameter to `Py_tracefunc` functions when a call is about to return.

int PyTrace_C_CALL¶  
~~~
    

~~~
The value for the _what_ parameter to `Py_tracefunc` functions when a C function is about to be called.

int PyTrace_C_EXCEPTION¶  
~~~
    

~~~
The value for the _what_ parameter to `Py_tracefunc` functions when a C function has raised an exception.

int PyTrace_C_RETURN¶  
~~~
    

~~~
The value for the _what_ parameter to `Py_tracefunc` functions when a C function has returned.

int PyTrace_OPCODE¶  
~~~
    

~~~
The value for the _what_ parameter to `Py_tracefunc` functions (but not profiling functions) when a new opcode is about to be executed. This event is not emitted by default: it must be explicitly requested by setting `f_trace_opcodes` to _1_ on the frame.

void PyEval_SetProfile(Py_tracefunc func, [PyObject](structures.md#c.PyObject "PyObject") *obj)¶  
~~~
    

~~~
Set the profiler function to _func_. The _obj_ parameter is passed to the function as its first parameter, and may be any Python object, or `NULL`. If the profile function needs to maintain state, using a different value for _obj_ for each thread provides a convenient and thread-safe place to store it. The profile function is called for all monitored events except `PyTrace_LINE` `PyTrace_OPCODE` and `PyTrace_EXCEPTION`.

See also the [`sys.setprofile()`](../library/sys.md#sys.setprofile "sys.setprofile") function.

The caller must hold the [GIL](../glossary.md#term-GIL).

void PyEval_SetProfileAllThreads(Py_tracefunc func, [PyObject](structures.md#c.PyObject "PyObject") *obj)¶  
~~~
    

~~~
Like `PyEval_SetProfile()` but sets the profile function in all running threads belonging to the current interpreter instead of the setting it only on the current thread.

The caller must hold the [GIL](../glossary.md#term-GIL).

As `PyEval_SetProfile()`, this function ignores any exceptions raised while setting the profile functions in all threads.

在 3.12 版本加入.

void PyEval_SetTrace(Py_tracefunc func, [PyObject](structures.md#c.PyObject "PyObject") *obj)¶  
~~~
    

~~~
Set the tracing function to _func_. This is similar to `PyEval_SetProfile()`, except the tracing function does receive line-number events and per-opcode events, but does not receive any event related to C function objects being called. Any trace function registered using `PyEval_SetTrace()` will not receive `PyTrace_C_CALL`, `PyTrace_C_EXCEPTION` or `PyTrace_C_RETURN` as a value for the _what_ parameter.

See also the [`sys.settrace()`](../library/sys.md#sys.settrace "sys.settrace") function.

The caller must hold the [GIL](../glossary.md#term-GIL).

void PyEval_SetTraceAllThreads(Py_tracefunc func, [PyObject](structures.md#c.PyObject "PyObject") *obj)¶  
~~~
    

~~~
Like `PyEval_SetTrace()` but sets the tracing function in all running threads belonging to the current interpreter instead of the setting it only on the current thread.

The caller must hold the [GIL](../glossary.md#term-GIL).

As `PyEval_SetTrace()`, this function ignores any exceptions raised while setting the trace functions in all threads.

在 3.12 版本加入.

## 高级调试器支持¶

These functions are only intended to be used by advanced debugging tools.

PyInterpreterState *PyInterpreterState_Head()¶  
~~~
    

~~~
Return the interpreter state object at the head of the list of all such objects.

PyInterpreterState *PyInterpreterState_Main()¶  
~~~
    

~~~
返回主解释器状态对象。

PyInterpreterState *PyInterpreterState_Next(PyInterpreterState *interp)¶  
~~~
    

~~~
Return the next interpreter state object after _interp_ from the list of all such objects.

PyThreadState *PyInterpreterState_ThreadHead(PyInterpreterState *interp)¶  
~~~
    

~~~
Return the pointer to the first `PyThreadState` object in the list of threads associated with the interpreter _interp_.

PyThreadState *PyThreadState_Next(PyThreadState *tstate)¶  
~~~
    

~~~
Return the next thread state object after _tstate_ from the list of all such objects belonging to the same `PyInterpreterState` object.

## 线程本地存储支持¶

The Python interpreter provides low-level support for thread-local storage (TLS) which wraps the underlying native TLS implementation to support the Python-level thread local storage API ([`threading.local`](../library/threading.md#threading.local "threading.local")). The CPython C level APIs are similar to those offered by pthreads and Windows: use a thread key and functions to associate a void* value per thread.

The GIL does _not_ need to be held when calling these functions; they supply their own locking.

Note that `Python.h` does not include the declaration of the TLS APIs, you need to include `pythread.h` to use thread-local storage.

备注

None of these API functions handle memory management on behalf of the void* values. You need to allocate and deallocate them yourself. If the void* values happen to be [PyObject](structures.md#c.PyObject "PyObject")*, these functions don't do refcount operations on them either.

### Thread Specific Storage (TSS) API¶

TSS API is introduced to supersede the use of the existing TLS API within the CPython interpreter. This API uses a new type `Py_tss_t` instead of int to represent thread keys.

在 3.7 版本加入.

参见

"A New C-API for Thread-Local Storage in CPython" ([ **PEP 539**](https://peps.python.org/pep-0539/))

type Py_tss_t¶  
~~~
    

~~~
This data structure represents the state of a thread key, the definition of which may depend on the underlying TLS implementation, and it has an internal field representing the key's initialization state. There are no public members in this structure.

When [Py_LIMITED_API](stable.md#stable) is not defined, static allocation of this type by `Py_tss_NEEDS_INIT` is allowed.

Py_tss_NEEDS_INIT¶  
~~~
    

~~~
