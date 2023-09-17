# 3\. 配置 Python¶

## 3.1. 构建要求¶

Features and minimum versions required to build CPython:

  * [C11](https://en.cppreference.com/w/c/11) 编译器。 [可选的 C11 特性](https://en.wikipedia.org/wiki/C11_\(C_standard_revision\)#Optional_features) 不是必须的。

  * 在 Windows 上，需要 Microsoft Visual Studio 2017 或更新版本。

  * 对 [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754) 浮点数和 [浮点 Not-a-Number (NaN)](https://en.wikipedia.org/wiki/NaN#Floating_point) 的支持。

  * 对线程的支持。

  * OpenSSL 1.1.1 is the minimum version and OpenSSL 3.0.9 is the recommended minimum version for the [`ssl`](ssl.md#module-ssl "ssl: TLS/SSL wrapper for socket objects") and [`hashlib`](hashlib.md#module-hashlib "hashlib: Secure hash and message digest algorithms.") extension modules.

  * SQLite 3.15.2 for the [`sqlite3`](sqlite3.md#module-sqlite3 "sqlite3: A DB-API 2.0 implementation using SQLite 3.x.") extension module.

  * Tcl/Tk 8.5.12 for the [`tkinter`](tkinter.md#module-tkinter "tkinter: Interface to Tcl/Tk for graphical user interfaces") module.

  * Autoconf 2.71 and aclocal 1.16.4 are required to regenerate the `configure` script.

在 3.13: 版本发生变更: Autoconf 2.71, aclocal 1.16.4 and SQLite 3.15.2 are now required.

在 3.11 版本发生变更: C11 compiler, IEEE 754 and NaN support are now required. On Windows, Visual Studio 2017 or later is required. Tcl/Tk version 8.5.12 is now required for the [`tkinter`](tkinter.md#module-tkinter "tkinter: Interface to Tcl/Tk for graphical user interfaces") module.

在 3.10 版本发生变更: OpenSSL 1.1.1 is now required. Require SQLite 3.7.15.

在 3.7 版本发生变更: 现在要求线程支持和 OpenSSL 1.0.2。

在 3.6 版本发生变更: 现在要求选定的 C99 特性，如 `<stdint.h>` 和 `static inline` 函数。

在 3.5 版本发生变更: On Windows, Visual Studio 2015 or later is now required. Tcl/Tk version 8.4 is now required.

在 3.1 版本发生变更: Tcl/Tk version 8.3.1 is now required.

另请参阅 [**PEP 7**](https://peps.python.org/pep-0007/) "Style Guide for C Code" 和 [**PEP 11**](https://peps.python.org/pep-0011/) "CPython platform support"。

## 3.2. 已生成的文件¶

为了减少构建依赖性，Python 源代码包含多个已生成的文件。 重新生成所有已生成文件的命令如下:

    
    
~~~
make regen-all
make regen-stdlib-module-names
make regen-limited-abi
make regen-configure
~~~

`Makefile.pre.in` 文件记录了已生成的文件、它们的输入以及用于重新生成它们的工具。 搜索 `regen-*` make target。

The `make regen-configure` command runs [tiran/cpython_autoconf](https://github.com/tiran/cpython_autoconf) container for reproducible build; see container `entry.sh` script. The container is optional, the following command can be run locally, the generated files depend on autoconf and aclocal versions:

    
    
~~~
autoreconf -ivf -Werror
~~~

## 3.3. 配置选项¶

List all `configure` script options using:

    
    
~~~
./configure --help
~~~

参阅 Python 源代码中的 `Misc/SpecialBuilds.txt` 。

### 3.3.1. 通用选项¶

\--enable-loadable-sqlite-extensions¶

    

~~~
支持 `_sqlite` 扩展模块中的可加载扩展（默认为否）。

参见 [`sqlite3.Connection.enable_load_extension()`](../library/sqlite3.md#sqlite3.Connection.enable_load_extension "sqlite3.Connection.enable_load_extension") 方法的 [`sqlite3`](../library/sqlite3.md#module-sqlite3 "sqlite3: A DB-API 2.0 implementation using SQLite 3.x.") 模块。

在 3.6 版本加入.

\--disable-ipv6¶
~~~
    

~~~
禁用 IPv6 支持（若开启支持则默认启用），见 [`socket`](../library/socket.md#module-socket "socket: Low-level networking interface.") 模块。

\--enable-big-digits=[15|30]¶
~~~
    

~~~
定义 Python [`int`](../library/functions.md#int "int") 数字的比特大小：15或30比特

在默认情况下，数位大小为 30。

定义 `PYLONG_BITS_IN_DIGIT` 为 `15` 或 `30`。

参见 [`sys.int_info.bits_per_digit`](../library/sys.md#sys.int_info "sys.int_info") 。

\--with-suffix=SUFFIX¶
~~~
    

~~~
将 Python 的可执行文件后缀设置为 _SUFFIX_ 。

在 Windows 和 macOS 上默认后缀为 `.exe` (可执行文件为 `python.exe`)，在 Emscripten 节点上为 `.js`，在 Emscripten 浏览器上为 `.md`，在 WASI 上为 `.wasm`，而在其他平台上为一个空字符串 (可执行文件为 `python`)。

在 3.11 版本发生变更: 在 WASM 平台上默认后缀为 `.js`, `.md` 或 `.wasm` 之一。

\--with-tzpath=<list of absolute paths separated by pathsep>¶
~~~
    

~~~
Select the default time zone search path for为 [`zoneinfo.TZPATH`](../library/zoneinfo.md#zoneinfo.TZPATH "zoneinfo.TZPATH") 选择默认的时区搜索路径。 参见 [`zoneinfo`](../library/zoneinfo.md#module-zoneinfo "zoneinfo: IANA time zone support") 模块的 [编译时配置](../library/zoneinfo.md#zoneinfo-data-compile-time-config)。

默认：`/usr/share/zoneinfo:/usr/lib/zoneinfo:/usr/share/lib/zoneinfo:/etc/zoneinfo`

参阅 [`os.pathsep`](../library/os.md#os.pathsep "os.pathsep") 路径分隔符。

在 3.9 版本加入.

\--without-decimal-contextvar¶
~~~
    

~~~
编译 `_decimal` 扩展模块时使用线程本地上下文，而不是协程本地上下文（默认），参见 [`decimal`](../library/decimal.md#module-decimal "decimal: Implementation of the General Decimal Arithmetic  Specification.") 模块。

参见 [`decimal.HAVE_CONTEXTVAR`](../library/decimal.md#decimal.HAVE_CONTEXTVAR "decimal.HAVE_CONTEXTVAR") 和 [`contextvars`](../library/contextvars.md#module-contextvars "contextvars: Context Variables") 模块。

在 3.9 版本加入.

\--with-dbmliborder=<list of backend names>¶
~~~
    

~~~
覆盖 [`dbm`](../library/dbm.md#module-dbm "dbm: Interfaces to various Unix "database" formats.") 模块的 db 后端检查顺序。

合法值是用冒号（`:`）分隔的字符串，包含后端名称。

  * `ndbm` ；

  * `gdbm` ；

  * `bdb` 。

\--without-c-locale-coercion¶
~~~
    

~~~
禁用 C 语言对 UTF-8 的强制要求（默认为启用）。

不定义 `PY_COERCE_C_LOCALE` 宏。

参阅 [`PYTHONCOERCECLOCALE`](cmdline.md#envvar-PYTHONCOERCECLOCALE) 和 [**PEP 538**](https://peps.python.org/pep-0538/)。

\--without-freelists¶
~~~
    

~~~
禁用除空元组单例以外的所有自由列表。Disable all freelists except the empty tuple singleton.

在 3.11 版本加入.

\--with-platlibdir=DIRNAME¶
~~~
    

~~~
Python 库目录名（默认为 `lib`）。

Fedora 和 SuSE 在64 位平台用 `lib64` 。

参阅 [`sys.platlibdir`](../library/sys.md#sys.platlibdir "sys.platlibdir") 。

在 3.9 版本加入.

\--with-wheel-pkg-dir=PATH¶
~~~
    

~~~
[`ensurepip`](../library/ensurepip.md#module-ensurepip "ensurepip: Bootstrapping the "pip" installer into an existing Python installation or virtual environment.") 模块用到的 wheel 包目录（默认为无）。

某些 Linux 发行版的打包策略建议不要捆绑依赖关系。如 Fedora 在 `/usr/share/python-wheels/` 目录下安装 wheel 包，而不安装 `ensurepip._bundled` 包。

在 3.10 版本加入.

\--with-pkg-config=[check|yes|no]¶
~~~
    

~~~
配置是否应当使用 **pkg-config** 来检测生成依赖。

  * `check` (默认值): **pkg-config** 为可选项

  * `yes`: **pkg-config** 为必选项。

  * `no`: 配置不使用 **pkg-config** 即使其存在

在 3.11 版本加入.

\--enable-pystats¶
~~~
    

~~~
Turn on internal Python performance statistics gathering.

By default, statistics gathering is off. Use `python3 -X pystats` command or set `PYTHONSTATS=1` environment variable to turn on statistics gathering at Python startup.

At Python exit, dump statistics if statistics gathering was on and not cleared.

效果如下：

  * Add [`-X pystats`](cmdline.md#cmdoption-X) command line option.

  * Add `PYTHONSTATS` environment variable.

  * Define the `Py_STATS` macro.

  * Add functions to the [`sys`](../library/sys.md#module-sys "sys: Access system-specific parameters and functions.") module:

    * `sys._stats_on()`: Turns on statistics gathering.

    * `sys._stats_off()`: Turns off statistics gathering.

    * `sys._stats_clear()`: Clears the statistics.

    * `sys._stats_dump()`: Dump statistics to file, and clears the statistics.

The statistics will be dumped to a arbitrary (probably unique) file in `/tmp/py_stats/` (Unix) or `C:\temp\py_stats\` (Windows). If that directory does not exist, results will be printed on stderr.

使用 `Tools/scripts/summarize_stats.py` 来读取统计数据。

Statistics:

  * Opcode:

    * Specialization: success, failure, hit, deferred, miss, deopt, failures;

    * Execution count;

    * Pair count.

  * Call:

    * Inlined Python calls;

    * PyEval calls;

    * Frames pushed;

    * Frame object created;

    * Eval calls: vector, generator, legacy, function VECTORCALL, build class, slot, function "ex", API, method.

  * Object:

    * incref and decref;

    * interpreter incref and decref;

    * allocations: all, 512 bytes, 4 kiB, big;

    * free;

    * to/from free lists;

    * dictionary materialized/dematerialized;

    * type cache;

    * optimization attemps;

    * optimization traces created/executed;

    * uops executed.

  * Garbage collector:

    * Garbage collections;

    * Objects visited;

    * Objects collected.

在 3.11 版本加入.

\--disable-gil¶
~~~
    

~~~
Enables **experimental** support for running Python without the [global interpreter lock](../glossary.md#term-global-interpreter-lock) (GIL).

See [**PEP 703**](https://peps.python.org/pep-0703/) "Making the Global Interpreter Lock Optional in CPython".

在 3.13 版本加入.

### 3.3.2. WebAssembly 选项。¶

\--with-emscripten-target=[browser|node]¶
~~~
    

~~~
为 `wasm32-emscripten` 设置生成风格。

  * `browser` (默认值): 预加载最小 stdlib，默认 MEMFS。

  * `node`: NODERAWFS 和 pthread 支持。

在 3.11 版本加入.

\--enable-wasm-dynamic-linking¶
~~~
    

~~~
为 WASM 启用动态链接支持。

动态链接启用 `dlopen`。 可执行文件的大小将由于限制死代码清理和附加特性而增加。

在 3.11 版本加入.

\--enable-wasm-pthreads¶
~~~
    

~~~
为 WASM 启用 pthreads 支持。

在 3.11 版本加入.

### 3.3.3. 安装时的选项¶

\--prefix=PREFIX¶
~~~
    

~~~
在 PREFIX 中安装架构无关的文件。 在 Unix 上，它默认为 `/usr/local`。

该值可在运行时使用 [`sys.prefix`](../library/sys.md#sys.prefix "sys.prefix") 获取。

作为示例，用户可以使用 `--prefix="$HOME/.local/"` 在其家目录中安装 Python。

\--exec-prefix=EPREFIX¶
~~~
    

~~~
在 EPREFIX 中安装架构无关的文件，默认为 `--prefix`。

该值可在运行时使用 [`sys.exec_prefix`](../library/sys.md#sys.exec_prefix "sys.exec_prefix") 获取。

\--disable-test-modules¶
~~~
    

~~~
不编译和安装 test 模块，如 [`test`](../library/test.md#module-test "test: Regression tests package containing the testing suite for Python.") 包或 `_testcapi` 扩展模块（默认会编译并安装）。

在 3.10 版本加入.

\--with-ensurepip=[upgrade|install|no]¶
~~~
    

~~~
选择 Python 安装时运行的 [`ensurepip`](../library/ensurepip.md#module-ensurepip "ensurepip: Bootstrapping the "pip" installer into an existing Python installation or virtual environment.") 命令。

  * `upgrade` （默认）：运行 `python -m ensurepip --altinstall --upgrade` 命令。

  * `install` ：运行 `python -m ensurepip --altinstall` 命令。

  * `no` ：不运行 ensurepip。

在 3.6 版本加入.

### 3.3.4. 性能选项¶

为获得最佳性能推荐使用 `--enable-optimizations --with-lto` (PGO + LTO) 来配置 Python。 试验性的 `--enable-bolt` 旗标也可被用来提升性能。

\--enable-optimizations¶
~~~
    

~~~
用 `PROFILE_TASK` 启用以配置文件主导的优化（PGO）（默认为禁用）。

C 编译器 Clang 需要用到 `llvm-profdata` 程序进行 PGO。在 macOS 上，GCC 也需要用到它：在 macOS 上 GCC 只是 Clang 的别名而已。

如果使用 `--enable-shared` 和 GCC ，还可以禁用 libpython 中的语义插值：在编译器和链接器的标志中加入 `-fno-semantic-interposition` 。

在 3.6 版本加入.

在 3.10 版本发生变更: 在 GCC 上使用 `-fno-semantic-interposition` 。

PROFILE_TASK¶
~~~
    

~~~
Makefile 用到的环境变量：PGO 用到的 Python 命令行参数。

默认为：`-m test --pgo --timeout=$(TESTTIMEOUT)` 。

在 3.8 版本加入.

\--with-lto=[full|thin|no|yes]¶
~~~
    

~~~
在编译过程中启用链接时间优化（LTO）（默认为禁用）。

LTO 时 C 编译器 Clang 需要用到 `llvm-ar` 参数（在 macOS 则为 `ar`），以及支持 LTO 的链接器（`ld.gold` 或 `lld`）。

在 3.6 版本加入.

在 3.11 版本加入: 要使用 ThinLTO 特性，请在 Clang 上使用 `--with-lto=thin`。

在 3.12 版本发生变更: 如果编译器支持将使用 ThinLTO 旗标作为 Clang 上的默认优化策略。

\--enable-bolt¶
~~~
    

~~~
允许启用 [BOLT 链接后二进制优化器](https://github.com/llvm/llvm-project/tree/main/bolt) (默认为禁用)。

BOLT 是 LLVM 项目的一部分但并不总是包括在其二进制分发包中。 该旗标要求 `llvm-bolt` 和 `merge-fdata` 可用。

BOLT 仍然是一个相当新的项目因此目前该旗标应当被视为是试验性的。 因为此工具是作用于机器码所以其成功依赖于构建环境 + 其他优化配置参数 + CPU 架构的组合，并且并非所有组合都受到支持。 已知 LLVM 16 之前的 BOLT 版本在某些场景下会使得 BOLT 发生崩溃。 强烈建议使用 LLVM 16 或更新版本进行 BOLT 优化。

`BOLT_INSTRUMENT_FLAGS` 和 `BOLT_APPLY_FLAGS` **configure** 变量可被定义为覆盖 **llvm-bolt** 的默认参数集合来分别指示和将 BOLT 数据应用于二进制代码中。

在 3.12 版本加入.

\--with-computed-gotos¶
~~~
    

~~~
在求值环节启用 goto 计数（在支持的编译器上默认启用）。

\--without-pymalloc¶
~~~
    

~~~
禁用特定的 Python 内存分配器 [pymalloc](../c-api/memory.md#pymalloc) （默认为启用）。

参见环境变量 [`PYTHONMALLOC`](cmdline.md#envvar-PYTHONMALLOC) 。

\--without-doc-strings¶
~~~
    

~~~
禁用静态文档字符串以减少内存占用（默认启用）。Python 中定义的文档字符串不受影响。

不定义 `PY_COERCE_C_LOCALE` 宏。

参阅宏 `PyDoc_STRVAR()` 。

\--enable-profiling¶
~~~
    

~~~
用 `gprof` 启用 C 语言级的代码评估（默认为禁用）。

\--with-strict-overflow¶
~~~
    

~~~
将 `-fstrict-overflow` 添加到 C 编译器旗标 (在默认情况下我们将添加 `-fno-strict-overflow` 来代替)。

### 3.3.5. Python 调试级编译¶

调试版本 Python 是指带有 `--with-pydebug` 参数的编译。

调试版本的效果：

  * 默认显示所有警告：在 [`warnings`](../library/warnings.md#module-warnings "warnings: Issue warning messages and control their disposition.") 模块中，默认警告过滤器的列表是空的。

  * 在 [`sys.abiflags`](../library/sys.md#sys.abiflags "sys.abiflags") 中加入 `d` 标记。

  * 加入 `sys.gettotalrefcount()` 函数。

  * 命令行参数加入 [`-X showrefcount`](cmdline.md#cmdoption-X) 。

  * 添加 [`-d`](cmdline.md#cmdoption-d) 命令行选项和 [`PYTHONDEBUG`](cmdline.md#envvar-PYTHONDEBUG) 环境变量用于调试解析器。

  * 添加对 `__lltrace__` 变量的支持：如果定义了该变量则会在字节码求值循环中启用低层级追踪。

  * 安装 [内存分配调试钩子](../c-api/memory.md#default-memory-allocators) ，以便检测缓冲区溢出和其他内存错误。

  * 定义宏 `Py_DEBUG` 和 `Py_REF_DEBUG` 。

  * 增加运行时检查：针对由 `#ifdef Py_DEBUG` 和 `#endif` 所包裹的代码。 启用 `assert(...)` 和 `_PyObject_ASSERT(...)` 断言：不设置 `NDEBUG` 宏（另请参阅 `--with-assertions` 配置选项）。 主要的运行时检查有:

    * 增加了对函数参数的合理性检查。

    * 创建 Unicode 和 int 对象时，内存按某种模式进行了填充，用于检测是否使用了未初始化的对象。

    * 确保有能力清除或替换当前异常的函数在调用时不会引发异常。

    * 检查内存释放器函数是否不改变当前异常。

    * 垃圾收集器（[`gc.collect()`](../library/gc.md#gc.collect "gc.collect") 函数）对对象的一致性进行一些基本检查。

    * 从较宽类型转换到较窄类型时，`Py_SAFE_DOWNCAST()` 宏会检查整数下溢和上溢的情况。

参见 [Python 开发模式](../library/devmode.md#devmode) 和配置参数 `--with-trace-refs` 。

在 3.8 版本发生变更: Release builds and debug builds are now ABI compatible: defining the `Py_DEBUG` macro no longer implies the `Py_TRACE_REFS` macro (see the `--with-trace-refs` option).

### 3.3.6. 调试选项¶

\--with-pydebug¶
~~~
    

~~~
在调试模式下编译 Python: 定义宏 `Py_DEBUG` (默认为禁用)。

\--with-trace-refs¶
~~~
    

~~~
为了调试而启用引用的跟踪（默认为禁用）。

效果如下：

  * 定义 `Py_TRACE_REFS` 宏。

  * 加入 `sys.getobjects()` 函数。

  * 环境变量加入 [`PYTHONDUMPREFS`](cmdline.md#envvar-PYTHONDUMPREFS) 。

The [`PYTHONDUMPREFS`](cmdline.md#envvar-PYTHONDUMPREFS) environment variable can be used to dump objects and reference counts still alive at Python exit.

[Statically allocated objects](../c-api/typeobj.md#static-types) are not traced.

在 3.13 版本发生变更: This build is now ABI compatible with release build and debug build.

在 3.8 版本加入.

\--with-assertions¶
~~~
    

~~~
编译时启用 C 断言：`assert(...);` 和 `_PyObject_ASSERT(...);` （默认不启用）。

如果设置此参数，则在 `OPT` 编译器变量中不定义 `NDEBUG` 宏。

参阅 `--with-pydebug` 选项（调试编译模式），它也可以启用断言。

在 3.6 版本加入.

\--with-valgrind¶
~~~
    

~~~
启用 Valgrind （默认禁用）。

\--with-dtrace¶
~~~
    

~~~
启用 DTrace（默认禁用）。

参阅 [用 DTrace 和 SystemTap 测试 CPython](../howto/instrumentation.md#instrumentation)。

在 3.6 版本加入.

\--with-address-sanitizer¶
~~~
    

~~~
启用 AddressSanitizer 内存错误检测 `asan`，（默认为禁用）。

在 3.6 版本加入.

\--with-memory-sanitizer¶
~~~
    

~~~
启用 MemorySanitizer 内存错误检测 `msan`，（默认为禁用）。

在 3.6 版本加入.

\--with-undefined-behavior-sanitizer¶
~~~
    

~~~
启用 undefinedBehaviorSanitizer 未定义行为检测 `ubsan`，（默认为禁用）。

在 3.6 版本加入.

### 3.3.7. 链接器选项¶

\--enable-shared¶
~~~
    

~~~
启用共享 Python 库 `libpython` 的编译（默认为禁用）。

\--without-static-libpython¶
~~~
    

~~~
不编译 `libpythonMAJOR.MINOR.a`，也不安装 `python.o` (默认会编译并安装)。

在 3.10 版本加入.

### 3.3.8. 库选项¶

\--with-libs='lib1 ...'¶
~~~
    

~~~
链接附加库（默认不会）。

\--with-system-expat¶
~~~
    

~~~
用已安装的 `expat` 库编译 `pyexpat` 模块（默认为否）。

\--with-system-libmpdec¶
~~~
    

~~~
用已安装的 `mpdec` 库编译 `_decimal` 扩展模块，参见 [`decimal`](../library/decimal.md#module-decimal "decimal: Implementation of the General Decimal Arithmetic  Specification.") 模块（默认为否）。

在 3.3 版本加入.

\--with-readline=editline¶
~~~
    

~~~
用 `editline` 库作为 [`readline`](../library/readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 模块的后端。

定义 `WITH_EDITLINE` 宏。

在 3.10 版本加入.

\--without-readline¶
~~~
    

~~~
不编译 [`readline`](../library/readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 模块（默认会）。

不定义 `HAVE_LIBREADLINE` 宏。

在 3.10 版本加入.

\--with-libm=STRING¶
~~~
    

~~~
将 `libm` 数学库覆盖为 _STRING_ (默认情况视系统而定)。

\--with-libc=STRING¶
~~~
    

~~~
将 `libc` C 库覆盖为 _STRING_ (默认情况视系统而定)。

\--with-openssl=DIR¶
~~~
    

~~~
OpenSSL 的根目录。

在 3.7 版本加入.

\--with-openssl-rpath=[no|auto|DIR]¶
~~~
    

~~~
设置 OpenSSL 库的运行时库目录（rpath）。

  * `no` (默认): 不设置 rpath。

  * `auto`：根据 `--with-openssl` 和 `pkg-config` 自动检测 rpath。

  * _DIR_ ：直接设置 rpath。

在 3.10 版本加入.

### 3.3.9. 安全性选项¶

\--with-hash-algorithm=[fnv|siphash13|siphash24]¶
~~~
    

~~~
选择 `Python/pyhash.c` 采用的哈希算法。

  * `siphash13` (默认值);

  * `siphash24`;

  * `fnv`.

在 3.4 版本加入.

在 3.11 版本加入: 增加了 `siphash13` 并且是新的默认值。

\--with-builtin-hashlib-hashes=md5,sha1,sha256,sha512,sha3,blake2¶
~~~
    

~~~
内置哈希模块：

  * `md5`。

  * `sha1`。

  * `sha256`。

  * `sha512`。

  * `sha3` (带 shake)。

  * `blake2`。

在 3.9 版本加入.

\--with-ssl-default-suites=[python|openssl|STRING]¶
~~~
    

~~~
覆盖 OpenSSL 默认的密码套件字符串。

  * `python` (默认值): 采用 Python 推荐选择。

  * `openssl`：保留 OpenSSL 默认值不动。

  * _STRING_ ：采用自定义字符串。

参见 [`ssl`](../library/ssl.md#module-ssl "ssl: TLS/SSL wrapper for socket objects") 模块。

在 3.7 版本加入.

在 3.10 版本发生变更: 设置 `python` 和 _STRING_ 也会把 TLS 1.2 设为最低版本的协议。

### 3.3.10. macOS 选项¶

参见 `Mac/README.rst` 。

\--enable-universalsdk¶
~~~
    

~~~
\--enable-universalsdk=SDKDIR¶
~~~
    

~~~
创建通用的二进制版本。 _SDKDIR_ 指定应采用的 macOS SDK （默认为否）。

\--enable-framework¶
~~~
    

~~~
\--enable-framework=INSTALLDIR¶
~~~
    

~~~
创建 Python.framework ，而不是传统的 Unix 安装版。可选参数 _INSTALLDIR_ 指定了安装路径（(默认为否）。

\--with-universal-archs=ARCH¶
~~~
    

~~~
指定应创建何种通用二进制文件。该选项仅当设置了 `--enable-universalsdk` 时才有效。

可选项：

  * `universal2`。

  * `32-bit`。

  * `64-bit`。

  * `3-way`。

  * `intel`。

  * `intel-32`。

  * `intel-64`。

  * `all`。

\--with-framework-name=FRAMEWORK¶
~~~
    

~~~
为 macOS 中的 python 框架指定名称，仅当设置了 `--enable-framework` 时有效（默认：`Python`）。

### 3.3.11. 交叉编译选项¶

交叉编译，或称交叉构建，可被用于为不同的 CPU 架构或平台构建 Python。 交叉编译需要一个针对构建平台的 Python 解释器。 构建的 Python 版本必须与交叉编译的主机 Python 版本相匹配。

\--build=BUILD¶
~~~
    

~~~
用于在 BUILD 上执行构建的配置，通常由 **config.guess** 通过推测得到。

\--host=HOST¶
~~~
    

~~~
交叉编译以构建在 HOST (目标平台) 上运行的程序

\--with-build-python=path/to/python¶
~~~
    

~~~
针对交叉编译构建 `python` 二进制文件的路径

在 3.11 版本加入.

CONFIG_SITE=file¶
~~~
    

~~~
指向一个带有配置重载的的文件的环境变量。

示例 _config.site_ 文件:
~~~
    
    
~~~
# config.site-aarch64
ac_cv_buggy_getaddrinfo=no
ac_cv_file__dev_ptmx=yes
ac_cv_file__dev_ptc=no
~~~

交叉编译示例:

    
    
~~~
CONFIG_SITE=config.site-aarch64 ../configure \
    --build=x86_64-pc-linux-gnu \
    --host=aarch64-unknown-linux-gnu \
    --with-build-python=../x86_64/python
~~~

## 3.4. Python 构建系统¶

### 3.4.1. 构建系统的主要文件¶

  * `configure.ac` => `configure`;

  * `Makefile.pre.in` => `Makefile` (由 `configure` 创建);

  * `pyconfig.h` (由 `configure` 创建);

  * `Modules/Setup`: 由Makefile 使用 `Module/makesetup` shell 脚本构建的 C 扩展;

### 3.4.2. 主要构建步骤¶

  * C文件（ `.c` ）是作为对象文件（ `.o` ）构建的。

  * 一个静态库 `libpython` （ `.a` ）是由对象文件创建的。

  * `python.o` 和静态库 `libpython` 被链接到最终程序 `python` 中。

  * C 扩展是由 Makefile 构建的 (参见 `Modules/Setup`)。

### 3.4.3. 主要 Makefile 目标¶

  * `make` ：用标准库构建Python。

  * `make platform:` ：构建 `python` 程序，但不构建标准库扩展模块。

  * `make profile-opt` ：使用 Profile Guided Optimization (PGO) 构建 Python 。你可以使用 configure 的 `--enable-optimizations` 选项来使其成为 `make` 命令的默认目标（ `make all` 或只是 `make` ）。

  * `make buildbottest` ：构建 Python 并运行 Python 测试套件，与 buildbots 测试 Python 的方式相同。设置变量 `TESTTIMEOUT` （单位：秒）来改变测试超时时间（默认为 1200 即 20 分钟）。

  * `make install`：构建并安装 Python 。

  * `make regen-all` ：重新生成（几乎）所有生成的文件； `make regen-stdlib-module-names` 和 `autoconf` 必须对其余生成的文件单独运行。

  * `make clean` ：移除构建的文件。

  * `make distclean` ：与 `make clean` 相同，但也删除由配置脚本创建的文件。

### 3.4.4. C 扩展¶

一些 C 扩展是作为内置模块构建的，比如模块 `sys` 。它们是在定义了宏 `Py_BUILD_CORE_BUILTIN` 的情况下构建的。内置模块没有 `__file__` 属性:

    
    
~~~shell
>>> import sys
>>> sys
<module 'sys' (built-in)>
>>> sys.__file__
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: module 'sys' has no attribute '__file__'
~~~

其他 C 扩展是作为动态库来构建的，如 `_asyncio` 模块。 它们将在定义了 `Py_BUILD_CORE_MODULE` 宏的情况下构建。 在 Linux x86-64 上的例子:

    
    
~~~shell
>>> import _asyncio
>>> _asyncio
<module '_asyncio' from '/usr/lib64/python3.9/lib-dynload/_asyncio.cpython-39-x86_64-linux-gnu.so'>
>>> _asyncio.__file__
'/usr/lib64/python3.9/lib-dynload/_asyncio.cpython-39-x86_64-linux-gnu.so'
~~~

`Modules/Setup` 用于生成 Makefile 目标，以构建 C 扩展。在文件的开头， C 被构建为内置模块。在标记 `*shared*` 之后定义的扩展被构建为动态库。

The `PyAPI_FUNC()`, `PyAPI_DATA()` and [`PyMODINIT_FUNC`](10.C%20API接口/intro.md#c.PyMODINIT_FUNC "PyMODINIT_FUNC") macros of `Include/exports.h` are defined differently depending if the `Py_BUILD_CORE_MODULE` macro is defined:

  * 如果 `Py_BUILD_CORE_MODULE` 定义了，使用 `Py_EXPORTED_SYMBOL` 。

  * 否则使用 `Py_IMPORTED_SYMBOL` 。

如果宏 `Py_BUILD_CORE_BUILTIN` 被错误地用在作为共享库构建的 C 扩展上，它的函数 `PyInit_xxx()` 就不会被导出，导致导入时出现 [`ImportError`](3.标准库/exceptions.md#ImportError "ImportError") 。

## 3.5. 编译器和链接器的标志¶

脚本 `./configure` 和环境变量设置的选项，并被 `Makefile` 使用。

### 3.5.1. 预处理器的标志¶

CONFIGURE_CPPFLAGS¶

    

~~~
变量 `CPPFLAGS` 的值被传递给 `./configure` 脚本。

在 3.6 版本加入.

CPPFLAGS¶
~~~
    

~~~
（ Objective ） C/C++ 预处理器标志，例如，使用 `-I<include dir>` 如果你的头文件在一个非标准的目录 `<include dir>` 中 、

`CPPFLAGS` 和 `LDFLAGS` 都需要包含 shell 的值以便能够使用环境变量中指定的目录构建扩展模块。

BASECPPFLAGS¶
~~~
    

~~~
在 3.4 版本加入.

PY_CPPFLAGS¶
~~~
    

~~~
为构建解释器对象文件增加了额外的预处理器标志。

默认为： `$(BASECPPFLAGS) -I. -I$(srcdir)/Include $(CONFIGURE_CPPFLAGS) $(CPPFLAGS)` 。

在 3.2 版本加入.

### 3.5.2. 编译器标志¶

CC¶
~~~
    

~~~
C 编译器指令。

例如： `gcc -pthread` 。

CXX¶
~~~
    

~~~
C++ 编译器指令。

例如： `g++ -pthread` 。

CFLAGS¶
~~~
    

~~~
C 编译器标志。

CFLAGS_NODIST¶
~~~
    

~~~
`CFLAGS_NODIST` 用于构建解释器和 stdlib C 扩展。 一旦装好 Python 则当某个编译器旗标 _不应_ 成为 `CFLAGS` 的一部分时将可使用它 ([gh-65320](https://github.com/python/cpython/issues/65320))。

特别地，`CFLAGS` 不应当包含:

  * 编译器旗标 `-I` (用于为包括文件设置搜索路径)。 `-I` 旗标将按从左到右的顺序处理，并且 `CFLAGS` 中的任何旗标都将优先于 user- 和 package- 层级所提供的 `-I` 旗标。

  * 加固旗标如 `-Werror` 因为分发版无法控制由用户安装的包是否符合这样的高标准。

在 3.5 版本加入.

COMPILEALL_OPTS¶
~~~
    

~~~
当在 `make install` 中构建 PYC 文件时传给 [`compileall`](../library/compileall.md#module-compileall "compileall: Tools for byte-compiling all Python source files in a directory tree.") 命令行的选项。 默认值: `-j0`。

在 3.12 版本加入.

EXTRA_CFLAGS¶
~~~
    

~~~
而外的 C 编译器指令。

CONFIGURE_CFLAGS¶
~~~
    

~~~
变量 `CFLAGS` 的值传递给 `./configure` 脚本。

在 3.2 版本加入.

CONFIGURE_CFLAGS_NODIST¶
~~~
    

~~~
变量 `CFLAGS_NODIST` 的值传递给 `./configure` 脚本。

在 3.5 版本加入.

BASECFLAGS¶
~~~
    

~~~
基础编译器标志。

OPT¶
~~~
    

~~~
优化标志。

CFLAGS_ALIASING¶
~~~
    

~~~
严格或不严格的别名标志，用于编译 `Python/dtoa.c` 、

在 3.7 版本加入.

CCSHARED¶
~~~
    

~~~
用于构建共享库的编译器标志。

例如， `-fPIC` 在 Linux 和 BSD 上使用。

CFLAGSFORSHARED¶
~~~
    

~~~
为构建解释器对象文件增加了额外的 C 标志。

，默认为： `$(CCSHARED)` ，当 `--enable-shared` 被使用时，则为空字符串

PY_CFLAGS¶
~~~
    

~~~
默认为： `$(BASECFLAGS) $(OPT) $(CONFIGURE_CFLAGS) $(CFLAGS) $(EXTRA_CFLAGS)` 。

PY_CFLAGS_NODIST¶
~~~
    

~~~
默认为： `$(CONFIGURE_CFLAGS_NODIST) $(CFLAGS_NODIST) -I$(srcdir)/Include/internal` 。

在 3.5 版本加入.

PY_STDMODULE_CFLAGS¶
~~~
    

~~~
用于构建解释器对象文件的 C 标志。

默认为： `$(PY_CFLAGS) $(PY_CFLAGS_NODIST) $(PY_CPPFLAGS) $(CFLAGSFORSHARED)`。

在 3.7 版本加入.

PY_CORE_CFLAGS¶
~~~
    

~~~
默认为 `$(PY_STDMODULE_CFLAGS) -DPy_BUILD_CORE` 。

在 3.2 版本加入.

PY_BUILTIN_MODULE_CFLAGS¶
~~~
    

~~~
编译器标志，将标准库的扩展模块作为内置模块来构建，如 [`posix`](../library/posix.md#module-posix "posix: The most common POSIX system calls \(normally used via module os\). \(Unix\)") 模块

默认为： `$(PY_STDMODULE_CFLAGS) -DPy_BUILD_CORE_BUILTIN` 。

在 3.8 版本加入.

PURIFY¶
~~~
    

~~~
Purify 命令。 Purify 是一个内存调试程序。

默认为：空字符串（不使用）。

### 3.5.3. 链接器标志位¶

LINKCC¶
~~~
    

~~~
用于构建如 `python` 和 `_testembed` 的程序的链接器命令。

默认值: `$(PURIFY) $(CC)`。

CONFIGURE_LDFLAGS¶
~~~
    

~~~
变量 `LDFLAGS` 的值被传递给 `./configure` 脚本。

避免指定 `CFLAGS` ， `LDFLAGS` 等，这样用户就可以在命令行上使用它们来追加这些值，而不用触碰到预设的值。

在 3.2 版本加入.

LDFLAGS_NODIST¶
~~~
    

~~~
`LDFLAGS_NODIST` 的使用方式与 `CFLAGS_NODIST` 相同。 一旦装好 Python 则当某个链接器旗标 _不应_ 成为 `LDFLAGS` 的一部分时将可使用它 ([gh-65320](https://github.com/python/cpython/issues/65320))。

特别地，`LDFLAGS` 不应当包含:

  * 编译器旗标 `-L` (用于为库设置搜索路径)。 `-L` 旗标将按从左到右的顺序处理，并且 `LDFLAGS` 中的任何旗标都将优先于 user- 和 package 层级所提供的 `-L` 旗标。

CONFIGURE_LDFLAGS_NODIST¶
~~~
    

~~~
变量 `LDFLAGS_NODIST` 的值传递给 `./configure` 脚本。

在 3.8 版本加入.

LDFLAGS¶
~~~
    

~~~
链接器标志，例如，如果你的库在一个非标准的目录 `<lib dir>` 中，则使用 `-L<lib dir>` 。

`CPPFLAGS` 和 `LDFLAGS` 都需要包含 shell 的值以便能够使用环境变量中指定的目录构建扩展模块。

LIBS¶
~~~
    

~~~
链接器标志，在链接 Python 可执行文件时将库传递给链接器。

例如： `-lrt` 。

LDSHARED¶
~~~
    

~~~
构建一个共享库的命令。

默认为： `@LDSHARED@ $(PY_LDFLAGS)` 。

BLDSHARED¶
~~~
    

~~~
构建共享库 `libpython` 的命令。

默认为： `@BLDSHARED@ $(PY_CORE_LDFLAGS)` 。

PY_LDFLAGS¶
~~~
    

~~~
默认为： `$(CONFIGURE_LDFLAGS) $(LDFLAGS)` 。

PY_LDFLAGS_NODIST¶
~~~
    

~~~
默认为： `$(CONFIGURE_LDFLAGS_NODIST) $(LDFLAGS_NODIST)` 。

在 3.8 版本加入.

PY_CORE_LDFLAGS¶
~~~
    

~~~
