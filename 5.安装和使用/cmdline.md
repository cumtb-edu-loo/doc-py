# 1\. 命令行与环境¶

为获取各种设置信息，CPython 解析器会扫描命令行与环境。

**CPython 实现细节：** 其他实现的命令行方案可能会有所不同。 详见 [其他实现](4.语言参考/introduction.md#implementations)。

## 1.1. 命令行¶

调用 Python 时，可以指定下列任意选项：

    
    
~~~
python [-bBdEhiIOqsSuvVWx?] [-c command | -m module-name | script | - ] [args]
~~~

最常见的用例是启动时执行脚本：

    
    
~~~
python myscript.py
~~~

### 1.1.1. 接口选项¶

解释器接口类似于 UNIX shell，但提供了额外的调用方法:

  * 用连接到 tty 设备的标准输入调用时，会提示输入并执行命令，输入 EOF （文件结束符，UNIX 中按 ``Ctrl`-`D``，Windows 中按 ``Ctrl`-`Z,` `Enter``）时终止。

  * 用文件名参数或以标准输入文件调用时，读取，并执行该脚本文件。

  * 用目录名参数调用时，从该目录读取、执行适当名称的脚本。

  * 用 `-c command` 调用时，执行 _command_ 表示的 Python 语句。 _command_ 可以包含用换行符分隔的多条语句。注意，前导空白字符在 Python 语句中非常重要！

  * 用 `-m module-name` 调用时，在 Python 模块路径中查找指定的模块，并将其作为脚本执行。

非交互模式下，先解析全部输入，再执行。

接口选项会终结解释器读入的选项列表，所有后续参数都在 [`sys.argv`](3.标准库/sys.md#sys.argv "sys.argv") 里 -- 注意，首个元素，即下标为零的元素（`sys.argv[0]`）是表示程序来源的字符串。

-c <command>¶
    

执行 _command_ 中的 Python 代码。 _command_ 可以是一条语句，也可以是用换行符分隔的多条语句，其中，前导空白字符与普通模块代码中的作用一样。

使用此选项时，[`sys.argv`](3.标准库/sys.md#sys.argv "sys.argv") 的首个元素为 `"-c"`，并会把当前目录加入至 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 开头（让该目录中的模块作为顶层模块导入）。

使用 `command` 参数会引发 [审计事件](3.标准库/sys.md#auditing) `cpython.run_command` 。

-m <module-name>¶
    

在 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 中搜索指定模块，并以 [`__main__`](__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") 模块执行其内容。

该参数是 _模块名_ ，请勿输入文件扩展名（`.py`）。模块名应为有效的绝对 Python 模块名，但本实现对此不作强制要求（例如，允许使用含连字符 `-` 的名称）。

包名称（包括命名空间包）也允许使用。使用包名称而不是普通模块名时，解释器把 `<pkg>.__main__` 作为主模块执行。此行为特意被设计为与作为脚本参数传递给解释器的目录和 zip 文件的处理方式类似。

备注

此选项不适用于内置模块和以 C 编写的扩展模块，因为它们并没有对应的 Python 模块文件。 但是它仍然适用于预编译的模块，即使没有可用的初始源文件。

如果给出此选项，[`sys.argv`](3.标准库/sys.md#sys.argv "sys.argv") 的首个元素将为模块文件的完整路径 (在定位模块文件期间，首个元素将设为 `"-m"`)。 与 `-c` 选项一样，当前目录将被加入 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 的开头。

`-I` 选项可用来在隔离模式下运行脚本，此模式中 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 既不包含当前目录也不包含用户的 site-packages 目录。 所有 `PYTHON*` 环境变量也会被忽略。

许多标准库模块都包含在执行时，以脚本方式调用的代码。例如 [`timeit`](timeit.md#module-timeit "timeit: Measure the execution time of small code snippets.") 模块：

    
    
~~~
python -m timeit -s "setup here" "benchmarked code here"
python -m timeit -h # for details
~~~

使用 `module-name` 参数会引发 [审计事件](3.标准库/sys.md#auditing) `cpython.run_module` 。

参见

[`runpy.run_module()`](runpy.md#runpy.run_module "runpy.run_module")

    

~~~
Python 代码可以直接使用的等效功能

[**PEP 338**](https://peps.python.org/pep-0338/) \-- 将模块作为脚本执行

在 3.1 版本发生变更: 提供包名称来运行 `__main__` 子模块。

在 3.4 版本发生变更: 同样支持命名空间包

-
~~~

从标准输入 ([`sys.stdin`](3.标准库/sys.md#sys.stdin "sys.stdin")) 读取命令。标准输入为终端时，使用 `-i`。

使用此选项时，[`sys.argv`](3.标准库/sys.md#sys.argv "sys.argv") 的第一个元素是 `"-"`， 同时，把当前目录加入 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 开头。

没有参数时，会触发 [审计事件](3.标准库/sys.md#auditing) `cpython.run_stdin` 。

<script>

    

~~~
执行 _script_ 中的 Python 代码，该参数应为（绝对或相对）文件系统路径，指向 Python 文件、包含 `__main__.py` 文件的目录，或包含 `__main__.py` 文件的 zip 文件。

给出此选项时，[`sys.argv`](../library/sys.md#sys.argv "sys.argv") 的第一个元素就是在命令行中指定的脚本名称。

如果脚本名称直接指向 Python 文件，则把该文件所在目录加入 [`sys.path`](../library/sys.md#sys.path "sys.path") 的开头，并且把该文件当作 [`__main__`](../library/__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") 模块来执行。

如果脚本名称指向目录或 zip 文件，则把脚本名加入 [`sys.path`](../library/sys.md#sys.path "sys.path") 的开头，并把该位置中的 `__main__.py` 文件当作 [`__main__`](../library/__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") 模块来执行。

`-I` 选项以隔离模式运行脚本，此模式中，[`sys.path`](../library/sys.md#sys.path "sys.path") 既不包含脚本目录，也不包含用户的 site-packages 目录，还会忽略所有 `PYTHON*` 环境变量。

使用 `filename` 参数会引发 [审计事件](../library/sys.md#auditing) `cpython.run_file` 。

参见

[`runpy.run_path()`](../library/runpy.md#runpy.run_path "runpy.run_path")
~~~
    

~~~
Python 代码可以直接使用的等效功能

未给出接口选项时，使用 `-i`，`sys.argv[0]` 为空字符串 (`""`)，并把当前目录加至 [`sys.path`](../library/sys.md#sys.path "sys.path") 的开头。 此外，如果系统支持，还能自动启用 tab 补全和历史编辑（参见 [Readline 配置](../library/site.md#rlcompleter-config)）。

参见

[调用解释器](../tutorial/interpreter.md#tut-invoking)

在 3.4 版本发生变更: 自动启用 tab 补全和历史编辑。

### 1.1.2. 通用选项¶

-?¶
-h¶
\--help¶
~~~
    

~~~
打印所有命令行选项及对应环境变量的简短描述然后退出。

\--help-env¶
~~~
    

~~~
打印 Python 专属环境变量的简短描述然后退出。

在 3.11 版本加入.

\--help-xoptions¶
~~~
    

~~~
打印实现专属 `-X` 选项的简短描述然后退出。

在 3.11 版本加入.

\--help-all¶
~~~
    

~~~
打印完整使用信息然后退出。

在 3.11 版本加入.

-V¶
\--version¶
~~~
    

~~~
输出 Python 版本号并退出。示例如下：
~~~
    
    
~~~
Python 3.8.0b2+
~~~

输入两次 `V` 选项时，输出更多构建信息，例如：

    
    
~~~
Python 3.8.0b2+ (3.8:0c076caaa8, Apr 20 2019, 21:55:00)
[GCC 6.2.0 20161005]
~~~

在 3.6 版本加入: `-VV` 选项。

### 1.1.3. 其他选项¶

-b¶
    

用 [`str`](../library/stdtypes.md#str "str") 与 [`bytes`](../library/stdtypes.md#bytes "bytes") 或 [`bytearray`](../library/stdtypes.md#bytearray "bytearray") 对比， 或对比 [`bytes`](../library/stdtypes.md#bytes "bytes") 与 [`int`](../library/functions.md#int "int") 时，会发出警告。重复给出该选项（`-bb`）时会报错。

在 3.5 版本发生变更: 影响 [`bytes`](../library/stdtypes.md#bytes "bytes") 与 [`int`](../library/functions.md#int "int") 的比较。

-B¶
    

给出此选项时，Python 不在导入源模块时写入 `.pyc` 文件。另请参阅 `PYTHONDONTWRITEBYTECODE`。

\--check-hash-based-pycs default|always|never¶

    

~~~
控制基于哈希值的 `.pyc` 文件的验证行为。 参见 [已缓存字节码的失效](../reference/import.md#pyc-invalidation)。 当设为 `default` 时，已选定和未选定的基于哈希值的字节码缓存文件将根据其默认语义进行验证。 当设为 `always` 时，所有基于哈希值的 `.pyc` 文件，不论是已选定还是未选定的都将根据其对应的源文件进行验证。 当设为 `never` 时，基于哈希值的 `.pyc` 文件将不会根据其对应的源文件进行验证。

基于时间戳的 `.pyc` 文件的语义不会受此选项影响。

-d¶
~~~

启用解析器调试输出（仅供专家查看）。 另请参见 `PYTHONDEBUG` 环境变量。

此选项需要 [Python 的调试构建版](configure.md#debug-build)，否则它将被忽略。

-E¶
    

忽略所有 `PYTHON*` 环境变量，例如，已设置的 `PYTHONPATH` 和 `PYTHONHOME`。

另请参阅 `-P` 和 `-I` (隔离) 选项。

-i¶
    

脚本是第一个参数，或使用 `-c` 时，即便 [`sys.stdin`](../library/sys.md#sys.stdin "sys.stdin") 不是终端，执行脚本或命令后，也会进入交互模式。不读取 `PYTHONSTARTUP` 文件。

本选项用于，脚本触发异常时，检查全局变量或堆栈回溯。 详见 `PYTHONINSPECT`。

-I¶
    

以隔离模式运行 Python。 这还将应用 `-E`, `-P` 和 `-s` 选项。

在隔离模式下 [`sys.path`](../library/sys.md#sys.path "sys.path") 既不包含脚本所在目录也不包含用户的 site-packages 目录。 所有 `PYTHON*` 环境变量也都会被忽略。 还可以施加更进一步的限制以防止用户注入恶意代码。

在 3.4 版本加入.

-O¶
    

移除 assert 语句以及任何以 [`__debug__`](../library/constants.md#debug__ "__debug__") 的值作为条件的代码。 通过在 `.pyc` 扩展名之前添加 `.opt-1` 来扩充已编译文件 ([bytecode](../glossary.md#term-bytecode)) 的文件名 (参见 [**PEP 488**](https://peps.python.org/pep-0488/))。 另请参阅 `PYTHONOPTIMIZE`。

在 3.5 版本发生变更: 依据 [**PEP 488**](https://peps.python.org/pep-0488/) 修改 `.pyc` 文件名。

-OO¶
    

在启用 `-O` 的同时丢弃文档字符串。 通过在 `.pyc` 扩展名之前添加 `.opt-2` 来扩展已编译文件 ([bytecode](../glossary.md#term-bytecode)) 的文件名 (参见 [**PEP 488**](https://peps.python.org/pep-0488/))。

在 3.5 版本发生变更: 依据 [**PEP 488**](https://peps.python.org/pep-0488/) 修改 `.pyc` 文件名。

-P¶
    

不要将具有潜在不安全性的路径附加到 [`sys.path`](../library/sys.md#sys.path "sys.path"):

  * `python -m module` 命令行: 不要附加当前工作目录。

  * `python script.py` 命令行: 不要附加脚本所在目录。 如果是一个符号链接，则会解析符号链接。

  * `python -c code` 和 `python` (REPL) 命令行: 不要附加空字符串，这表示当前工作目录。

另请参阅 `PYTHONSAFEPATH` 环境变量，以及 `-E` 和 `-I` (隔离) 选项。

在 3.11 版本加入.

-q¶
    

即使在交互模式下也不显示版权和版本信息。

在 3.2 版本加入.

-R¶
    

开启哈希随机化。 此选项权 `PYTHONHASHSEED` 环境变量设置为 `0` 时起作用，因为哈希随机化是默认启用的。

在Python的早期版本中，此选项启用哈希随机化，将 str 和 bytes 的对象 `__hash__()` 的值 "加盐" 为不可预测的随机值。虽然它们在单个Python进程中保持不变，但是在重复调用的Python进程之间它们是不可预测的。

哈希随机化旨在针对由精心选择的输入引起的拒绝服务攻击提供防护，这种输入利用了构造 dict 在最坏情况下的性能即 O(n2) 复杂度。 请参阅 <http://ocert.org/advisories/ocert-2011-003.md> 了解详情。

`PYTHONHASHSEED` 允许你为哈希种子密码设置一个固定值。

在 3.7 版本发生变更: 此选项不会再被忽略。

在 3.2.3 版本加入.

-s¶
    

不要将 [`用户 site-packages 目录`](../library/site.md#site.USER_SITE "site.USER_SITE") 添加到 [`sys.path`](../library/sys.md#sys.path "sys.path")。

参见

[**PEP 370**](https://peps.python.org/pep-0370/) \-- 分用户的 site-packages 目录

-S¶
    

禁用 [`site`](../library/site.md#module-site "site: Module responsible for site-specific configuration.") 的导入及其所附带的基于站点对 [`sys.path`](../library/sys.md#sys.path "sys.path") 的操作。 如果 [`site`](../library/site.md#module-site "site: Module responsible for site-specific configuration.") 会在稍后被显式地导入也会禁用这些操作 (如果你希望触发它们则应调用 [`site.main()`](../library/site.md#site.main "site.main"))。

-u¶
    

强制 stdout 和 stderr 流不使用缓冲。 此选项对 stdin 流无影响。

另请参阅 `PYTHONUNBUFFERED`。

在 3.7 版本发生变更: stdout 和 stderr 流在文本层现在不使用缓冲。

-v¶
    

每次在初始化模块时会打印一条信息，显示被加载的地方（文件名或内置模块名）。当给出两个v（ `-vv` ）时，搜索模块时会为每个文件打印一条信息。退出时模块清理的信息也会给出来。

在 3.10 版本发生变更: 由 [`site`](../library/site.md#module-site "site: Module responsible for site-specific configuration.") 模块可以得到将要处理的站点路径和 `.pth` 文件。

参阅 `PYTHONVERBOSE` 。

-W arg¶
    

警告信息的控制。Python 的警告机制默认将警告信息打印到 [`sys.stderr`](../library/sys.md#sys.stderr "sys.stderr")。

最简单的设置是将某个特定操作无条件地应用于进程所发出所有警告 (即使是在默认情况下会忽略的那些警告):

    
    
~~~
-Wdefault  # Warn once per call location
-Werror    # Convert to exceptions
-Walways   # Warn every time
-Wmodule   # Warn once per calling module
-Wonce     # Warn once per Python process
-Wignore   # Never warn
~~~

action 名可以根据需要进行缩写，解释器将会解析为合适的名称。例如，`-Wi` 与 `-Wignore` 相同。

完整的参数如下：

    
    
~~~
action:message:category:module:lineno
~~~

空字段匹配所有值；尾部的空字段可以省略。例如，`-W ignore::DeprecationWarning` 将忽略所有的 DeprecationWarning 警告。

_action_ 字段如上所述，但只适用于匹配其余字段的警告。

_message_ 字段必须与整个警告信息相匹配；不区分大小写。

_category_ 字段与警告类别相匹配（`DeprecationWarning` 等）。必须是个类名；检测消息的实际警告类别是否为指定类别的子类。

_module_ 字段匹配的是（完整限定）模块名称；这种匹配是大小写敏感的。

_lineno_ 字段匹配行号，其中 0 匹配所有行号，相当于省略了行号。

可以给出多个 `-W` 选项；当某条警告信息匹配上多个选项时，将执行最后一个匹配项的操作。非法 `-W` 选项将被忽略（不过，在触发第一条警告时，会打印出一条无效选项的警告信息）。

警告信息还可以用 `PYTHONWARNINGS` 环境变量来控制，也可以在 Python 程序中用 [`warnings`](../library/warnings.md#module-warnings "warnings: Issue warning messages and control their disposition.") 模块进行控制。例如， [`warnings.filterwarnings()`](../library/warnings.md#warnings.filterwarnings "warnings.filterwarnings") 函数可对警告信息使用正则表达式。

请参阅 [警告过滤器](../library/warnings.md#warning-filter) 和 [警告过滤器的介绍](../library/warnings.md#describing-warning-filters) 了解更多细节。

-x¶
    

跳过源中第一行，以允许使用非 Unix 形式的 `#!cmd`。 这适用于 DOS 专属的破解操作。

-X¶
    

保留用于各种具体实现专属的选项。 CPython 目前定义了下列可用的值：

  * `-X faulthandler` 将启用 [`faulthandler`](../library/faulthandler.md#module-faulthandler "faulthandler: Dump the Python traceback.")。 另请参阅 `PYTHONFAULTHANDLER`。

  * `-X showrefcount` 可在程序结束时或在交互式解释器每条语句后，输出总的引用计数和使用的内存块数。这只适用于 [调试版本](configure.md#debug-build)。

  * `-X tracemalloc` 使用 [`tracemalloc`](../library/tracemalloc.md#module-tracemalloc "tracemalloc: Trace memory allocations.") 模块启动对 Python 内存分配的跟踪。 在默认情况下，只有最近的帧会保存在跟踪的回溯信息中。 使用 `-X tracemalloc=NFRAME` 来启动限定回溯 _NFRAME_ 帧的跟踪。 请参阅 [`tracemalloc.start()`](../library/tracemalloc.md#tracemalloc.start "tracemalloc.start") 和 `PYTHONTRACEMALLOC` 了解详情。

  * `-X int_max_str_digits` 将配置 [整数字符串转换长度限制](../library/stdtypes.md#int-max-str-digits)。 另请参阅 `PYTHONINTMAXSTRDIGITS`。

  * `-X importtime` 显示每次导入耗费的时间。 它会显示模块名称，累计时间（包括嵌套的导入）和自身时间（排除嵌套的导入）。 请注意它的输出在多线程应用程序中可能会出错。 典型用法如 `python3 -X importtime -c 'import asyncio'`。 另请参阅 `PYTHONPROFILEIMPORTTIME`。

  * `-X dev`: 启用 [Python 开发模式](../library/devmode.md#devmode)，引入在默认情况下启用会导致开销过大的运行时检查。

  * `-X utf8` 启用 [Python UTF-8 模式](../library/os.md#utf8-mode)。 `-X utf8=0` 将显式地禁用 [Python UTF-8 模式](../library/os.md#utf8-mode) (即使在该模式应该会自动激活时也是如此)。 另请参阅 `PYTHONUTF8`。

  * `-X pycache_prefix=PATH` 允许将 `.pyc` 文件写入以给定目录为根的并行树，而不是代码树。另见 `PYTHONPYCACHEPREFIX` 。

  * `-X warn_default_encoding` issues a [`EncodingWarning`](../library/exceptions.md#EncodingWarning "EncodingWarning") when the locale-specific default encoding is used for opening files. See also `PYTHONWARNDEFAULTENCODING`.

  * `-X no_debug_ranges` 会禁用在代码对象中包括将额外位置信息（结束行、开始列偏移量和结束列偏移量）映射到每条指令的映射表。 这在需要较小的代码对象和 pyc 文件时很有用处并可在解释器显示回溯时屏蔽额外的视觉位置提示。 另请参阅 `PYTHONNODEBUGRANGES`。

  * `-X frozen_modules` 会决定被冻结的模块是否要被导入机制所忽略。 值为 "on" 表示它们将被导入而 "off" 表示它们将被忽略。 如果这是已安装的 Python (正常情况) 则默认为 "on"。 如果是尚在开发中 (从源代码树运行) 则默认为 "off"。 请注意已冻结的 "importlib_bootstrap" 和 "importlib_bootstrap_external" 模块将总是会被使用，即使该旗标被设为 "off"。

  * `-X perf` 会启用对 Linux `perf` 性能分析器的支持。 当提供了此选项时，`perf` 性能分析器将能够报告 Python 调用。 此选项仅在某些平台上可用而在当前系统不支持的情况下将不做任何事。 默认值为 "off"。 另请参阅 `PYTHONPERFSUPPORT` 和 [Python 对 Linux perf 性能分析器的支持](../howto/perf_profiling.md#perf-profiling)。

它还允许传入任意值并通过 [`sys._xoptions`](../library/sys.md#sys._xoptions "sys._xoptions") 字典来提取这些值。

在 3.2 版本发生变更: 增加了 `-X` 选项。

在 3.3 版本加入: `-X faulthandler` 选项。

在 3.4 版本加入: `-X showrefcount` 与 `-X tracemalloc` 选项。

在 3.6 版本加入: `-X showalloccount` 选项。

在 3.7 版本加入: `-X importtime`, `-X dev` 与 `-X utf8` 选项。

在 3.8 版本加入: `-X pycache_prefix` 选项。 `-X dev` 选项现在在 [`io.IOBase`](../library/io.md#io.IOBase "io.IOBase") 析构函数中记录 `close()` 异常。

在 3.9 版本发生变更: 使用 `-X dev` 选项，在字符串编码和解码操作时检查 _encoding_ 和 _errors_ 参数。

The `-X showalloccount` 选项已被移除。

在 3.10 版本加入: `-X warn_default_encoding` 选项。

从 3.9 版起不建议使用，已在 3.10 版中移除: The `-X oldparser` option.

在 3.11 版本加入: `-X no_debug_ranges` 选项。

在 3.11 版本加入: `-X frozen_modules` 选项。

在 3.11 版本加入: `-X int_max_str_digits` 选项。

在 3.12 版本加入: `-X perf` 选项。option.

### 1.1.4. 不应当使用的选项¶

-J¶
    

保留给 [Jython](https://www.jython.org/) 使用。

## 1.2. 环境变量¶

这些环境变量会影响 Python 的行为，它们是在命令行开关之前被处理的，但 -E 或 -I 除外。 根据约定，当存在冲突时命令行开关会覆盖环境变量的设置。

PYTHONHOME¶

    

~~~
更改标准 Python 库的位置。 默认情况下库是在 `_prefix_ /lib/python _version_` 和 `_exec_prefix_ /lib/python _version_` 中搜索，其中 `_prefix_` 和 `_exec_prefix_` 是由安装位置确定的目录，默认都位于 `/usr/local`。

当 `PYTHONHOME` 被设为单个目录时，它的值会同时替代 `_prefix_` 和 `_exec_prefix_`。 要为两者指定不同的值，请将 `PYTHONHOME` 设为 `_prefix_ : _exec_prefix_`。

PYTHONPATH¶
~~~
    

~~~
增加模块文件默认搜索路径。 所用格式与终端的 `PATH` 相同：一个或多个由 [`os.pathsep`](../library/os.md#os.pathsep "os.pathsep") 分隔的目录路径名称（例如 Unix 上用冒号而在 Windows 上用分号）。 默认忽略不存在的目录。

除了普通目录之外，单个 `PYTHONPATH` 条目可以引用包含纯Python模块的zip文件（源代码或编译形式）。无法从zip文件导入扩展模块。

默认索引路径依赖于安装路径，但通常都是以 `_prefix_ /lib/python _version_` 开始 (参见上文中的 `PYTHONHOME`)。 它 _总是_ 会被添加到 `PYTHONPATH`。

有一个附加目录将被插入到索引路径的 `PYTHONPATH` 之前，正如上文中 接口选项 所描述的。 搜索路径可以在 Python 程序内作为变量 [`sys.path`](../library/sys.md#sys.path "sys.path") 来进行操作。

PYTHONSAFEPATH¶
~~~
    

~~~
如果这被设为一个非空字符串，请不要将具有潜在不安全性的路径附加到 [`sys.path`](../library/sys.md#sys.path "sys.path"): 参见 `-P` 选项了解详情。

在 3.11 版本加入.

PYTHONPLATLIBDIR¶
~~~
    

~~~
如果它被设为非空字符串，则会覆盖 [`sys.platlibdir`](../library/sys.md#sys.platlibdir "sys.platlibdir") 值。

在 3.9 版本加入.

PYTHONSTARTUP¶
~~~
    

~~~
这如果是一个可读文件的名称，该文件中的 Python 命令会在交互模式的首个提示符显示之前被执行。 该文件会在与交互式命令执行所在的同一命名空间中被执行，因此其中所定义或导入的对象可以在交互式会话中无限制地使用。 你还可以在这个文件中修改提示符 [`sys.ps1`](../library/sys.md#sys.ps1 "sys.ps1") 和 [`sys.ps2`](../library/sys.md#sys.ps2 "sys.ps2") 以及钩子 [`sys.__interactivehook__`](../library/sys.md#sys.__interactivehook__ "sys.__interactivehook__")。

使用 `filename` 参数会引发 [审计事件](../library/sys.md#auditing) `cpython.run_startup` 。

PYTHONOPTIMIZE¶
~~~
    

~~~
这如果被设为一个非空字符串，它就相当于指定 `-O` 选项。 如果设为一个整数，则它就相当于多次指定 `-O`。

PYTHONBREAKPOINT¶
~~~
    

~~~
此变量如果被设定，它会使用加点号的路径标记一个可调用对象。 包含该可调用对象的模块将被导入，随后该可调用对象将由 [`sys.breakpointhook()`](../library/sys.md#sys.breakpointhook "sys.breakpointhook") 的默认实现来运行，后者自身将由内置的 [`breakpoint()`](../library/functions.md#breakpoint "breakpoint") 来调用。 如果未设定，或设定为空字符串，则它相当于值 "pdb.set_trace"。 将此变量设为字符串 "0" 会导致 [`sys.breakpointhook()`](../library/sys.md#sys.breakpointhook "sys.breakpointhook") 的默认实现不做任何事而直接返回。

在 3.7 版本加入.

PYTHONDEBUG¶
~~~
    

~~~
此变量如果被设为一个非空字符串，它就相当于指定 `-d` 选项。 如果设为一个整数，则它就相当于多次指定 `-d`。

此环境变量需要 [Python 的调试构建版](configure.md#debug-build)，否则它将被忽略。

PYTHONINSPECT¶
~~~
    

~~~
此变量如果被设为一个非空字符串，它就相当于指定 `-i` 选项。

此变量也可由 Python 代码使用 [`os.environ`](../library/os.md#os.environ "os.environ") 来修改以在程序终结时强制检查模式。

PYTHONUNBUFFERED¶
~~~
    

~~~
此变量如果被设为一个非空字符串，它就相当于指定 `-u` 选项。

PYTHONVERBOSE¶
~~~
    

~~~
此变量如果被设为一个非空字符串，它就相当于指定 `-v` 选项。 如果设为一个整数，则它就相当于多次指定 `-v`。

PYTHONCASEOK¶
~~~
    

~~~
如果设置了此变量，Python 将忽略 [`import`](../reference/simple_stmts.md#import) 语句中的大小写。 这仅在 Windows 和 macOS 上有效。

PYTHONDONTWRITEBYTECODE¶
~~~
    

~~~
此变量如果被设为一个非空字符串，Python 将不会尝试在导入源模块时写入 `.pyc` 文件。 这相当于指定 `-B` 选项。

PYTHONPYCACHEPREFIX¶
~~~
    

~~~
如果设置了此选项，Python将在镜像目录树中的此路径中写入 `.pyc` 文件，而不是源树中的 `__pycache__` 目录中。这相当于指定 `-X` `pycache_prefix=PATH` 选项。

在 3.8 版本加入.

PYTHONHASHSEED¶
~~~
    

~~~
如果此变量未设置或设为 `random`，将使用一个随机值作为 str 和 bytes 对象哈希运算的种子。

如果 `PYTHONHASHSEED` 被设为一个整数值，它将被作为固定的种子数用来生成哈希随机化所涵盖的类型的 hash() 结果。

它的目的是允许可复现的哈希运算，例如用于解释器本身的自我检测，或允许一组 python 进程共享哈希值。

该整数必须为一个 [0,4294967295] 范围内的十进制数。 指定数值 0 将禁用哈希随机化。

在 3.2.3 版本加入.

PYTHONINTMAXSTRDIGITS¶
~~~
    

~~~
如果将此变量设为一个整数，它会被用来配置解释器的全局 [整数字符串转换长度限制](../library/stdtypes.md#int-max-str-digits)。

在 3.11 版本加入.

PYTHONIOENCODING¶
~~~
    

~~~
如果此变量在运行解释器之前被设置，它会覆盖通过 `encodingname:errorhandler` 语法设置的 stdin/stdout/stderr 所用编码。 `encodingname` 和 `:errorhandler` 部分都是可选项，与在 [`str.encode()`](../library/stdtypes.md#str.encode "str.encode") 中的含义相同。

对于 stderr，`:errorhandler` 部分会被忽略；处理程序将总是为 `'backslashreplace'`。

在 3.4 版本发生变更: “encodingname” 部分现在是可选的。

在 3.6 版本发生变更: 在 Windows 上，对于交互式控制台缓冲区会忽略此变量所指定的编码，除非还指定了 `PYTHONLEGACYWINDOWSSTDIO`。 通过标准流重定向的文件和管道则不受其影响。

PYTHONNOUSERSITE¶
~~~
    

~~~
如果设置了此变量，Python 将不会把 [`用户 site-packages 目录`](../library/site.md#site.USER_SITE "site.USER_SITE") 添加到 [`sys.path`](../library/sys.md#sys.path "sys.path")。

参见

[**PEP 370**](https://peps.python.org/pep-0370/) \-- 分用户的 site-packages 目录

PYTHONUSERBASE¶
~~~
    

~~~
定义 [`用户基准目录`](../library/site.md#site.USER_BASE "site.USER_BASE")，它将被用来计算 [`用户 site-packages 目录`](../library/site.md#site.USER_SITE "site.USER_SITE") 的路径以及 `python -m pip install --user` 的安装路径。

参见

[**PEP 370**](https://peps.python.org/pep-0370/) \-- 分用户的 site-packages 目录

PYTHONEXECUTABLE¶
~~~
    

~~~
如果设置了此环境变量，则 `sys.argv[0]` 将被设为此变量的值而不是通过 C 运行时所获得的值。 这仅在 macOS 上起作用。

PYTHONWARNINGS¶
~~~
    

~~~
此变量等价于 `-W` 选项。 如果被设为一个以逗号分隔的字符串，它就相当于多次指定 `-W`，列表中后出现的过滤器优先级会高于列表中先出现的。

最简单的设置是将某个特定操作无条件地应用于进程所发出所有警告 (即使是在默认情况下会忽略的那些警告):
~~~
    
    
~~~
PYTHONWARNINGS=default  # Warn once per call location
PYTHONWARNINGS=error    # Convert to exceptions
PYTHONWARNINGS=always   # Warn every time
PYTHONWARNINGS=module   # Warn once per calling module
PYTHONWARNINGS=once     # Warn once per Python process
PYTHONWARNINGS=ignore   # Never warn
~~~

请参阅 [警告过滤器](../library/warnings.md#warning-filter) 和 [警告过滤器的介绍](../library/warnings.md#describing-warning-filters) 了解更多细节。

PYTHONFAULTHANDLER¶

    

~~~
如果此环境变量被设为一个非空字符串，[`faulthandler.enable()`](../library/faulthandler.md#faulthandler.enable "faulthandler.enable") 会在启动时被调用：为 `SIGSEGV`, `SIGFPE`, `SIGABRT`, `SIGBUS` 和 `SIGILL` 等信号安装一个处理句柄以转储 Python 回溯信息。 此变量等价于 `-X` `faulthandler` 选项。

在 3.3 版本加入.

PYTHONTRACEMALLOC¶
~~~
    

~~~
如果此环境变量被设为一个非空字符串，则会使用 [`tracemalloc`](../library/tracemalloc.md#module-tracemalloc "tracemalloc: Trace memory allocations.") 模块启动对 Python 内存分配的跟踪。 该变量的值是保存在跟踪的回溯信息中的最大帧数。 例如， `PYTHONTRACEMALLOC=1` 只保存最近的帧。 请参阅 [`tracemalloc.start()`](../library/tracemalloc.md#tracemalloc.start "tracemalloc.start") 函数了解更多信息。 这等价于设置 `-X` `tracemalloc` 选项。

在 3.4 版本加入.

PYTHONPROFILEIMPORTTIME¶
~~~
    

~~~
如果此环境变量被设为一个非空字符串，Python 将会显示每次导入药费了多长时间。 这等价于设置 `-X` `importtime` 选项。

在 3.7 版本加入.

PYTHONASYNCIODEBUG¶
~~~
    

~~~
如果此变量被设为一个非空字符串，则会启用 [`asyncio`](../library/asyncio.md#module-asyncio "asyncio: Asynchronous I/O.") 模块的 [调试模式](../library/asyncio-dev.md#asyncio-debug-mode)。

在 3.4 版本加入.

PYTHONMALLOC¶
~~~
    

~~~
设置 Python 内存分配器和/或安装调试钩子。

设置 Python 所使用的内存分配器族群：

  * `default`: 使用 [默认内存分配器](../c-api/memory.md#default-memory-allocators)。

  * `malloc`: 对所有域 ([`PYMEM_DOMAIN_RAW`](../c-api/memory.md#c.PYMEM_DOMAIN_RAW "PYMEM_DOMAIN_RAW"), [`PYMEM_DOMAIN_MEM`](../c-api/memory.md#c.PYMEM_DOMAIN_MEM "PYMEM_DOMAIN_MEM"), [`PYMEM_DOMAIN_OBJ`](../c-api/memory.md#c.PYMEM_DOMAIN_OBJ "PYMEM_DOMAIN_OBJ")) 使用 C 库的 `malloc()` 函数。

  * `pymalloc`: 对 [`PYMEM_DOMAIN_MEM`](../c-api/memory.md#c.PYMEM_DOMAIN_MEM "PYMEM_DOMAIN_MEM") 和 [`PYMEM_DOMAIN_OBJ`](../c-api/memory.md#c.PYMEM_DOMAIN_OBJ "PYMEM_DOMAIN_OBJ") 域使用 [pymalloc 分配器](../c-api/memory.md#pymalloc) 而对 [`PYMEM_DOMAIN_RAW`](../c-api/memory.md#c.PYMEM_DOMAIN_RAW "PYMEM_DOMAIN_RAW") 域使用 `malloc()` 函数。

安装 [调试钩子](../c-api/memory.md#pymem-debug-hooks) ：

  * `debug`: 在 [默认内存分配器](../c-api/memory.md#default-memory-allocators) 之上安装调试钩子。

  * `malloc_debug`: 与 `malloc` 相同但还会安装调试钩子。

  * `pymalloc_debug`: 与 `pymalloc` 相同但还会安装调试钩子。

在 3.7 版本发生变更: 增加了 `"default"` 分配器。

在 3.6 版本加入.

PYTHONMALLOCSTATS¶
~~~
    

~~~
如果设为一个非空字符串，Python 将在每次创建新的 pymalloc 对象区域以及在关闭时打印 [pymalloc 内存分配器](../c-api/memory.md#pymalloc) 的统计信息。

如果 `PYTHONMALLOC` 环境变量被用来强制开启 C 库的 `malloc()` 分配器，或者如果 Python 的配置不支持 `pymalloc`，则此变量将被忽略。

在 3.6 版本发生变更: 此变量现在也可以被用于在发布模式下编译的 Python。 如果它被设置为一个空字符串则将没有任何效果。

PYTHONLEGACYWINDOWSFSENCODING¶
~~~
    

~~~
如果设为非空字符串，默认的 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 模式将恢复到 3.6 版本之前的值 “mbcs”和“replace”。 否则，将采用新的默认值“utf-8”和“surrogatepass”。

这也可以在运行时通过 [`sys._enablelegacywindowsfsencoding()`](../library/sys.md#sys._enablelegacywindowsfsencoding "sys._enablelegacywindowsfsencoding") 来启用。

[可用性](../library/intro.md#availability): Windows。

在 3.6 版本加入: 更多详情请参阅 [**PEP 529**](https://peps.python.org/pep-0529/)。

PYTHONLEGACYWINDOWSSTDIO¶
~~~
    

~~~
如果设为一个非空字符串，则不使用新的控制台读取器和写入器。 这意味着 Unicode 字符将根据活动控制台的代码页进行编码，而不是使用 utf-8。

如果标准流被重定向（到文件或管道）而不是指向控制台缓冲区则该变量会被忽略。

[可用性](../library/intro.md#availability): Windows。

在 3.6 版本加入.

PYTHONCOERCECLOCALE¶
~~~
    

~~~
如果值设为 `0`，将导致主 Python 命令行应用跳过将传统的基于 ASCII 的 C 与 POSIX 区域设置强制转换为更强大的基于 UTF-8 的替代方案。

如果此变量 _未被_ 设置（或被设为 `0` 以外的值），则覆盖环境变量的 `LC_ALL` 区域选项也不会被设置，并且报告给 `LC_CTYPE` 类别的当前区域选项或者为默认的 `C` 区域，或者为显式指明的基于 ASCII 的 `POSIX` 区域，然后 Python CLI 将在加载解释器运行时之前尝试为 `LC_CTYPE` 类别按指定的顺序配置下列区域选项：

  * `C.UTF-8`

  * `C.utf8`

  * `UTF-8`

如果成功设置了以上区域类别中的一个，则初始化 Python 运行时之前也将在当前进程环境中相应地设置 `LC_CTYPE` 环境变量。 这会确保除了解释器本身和运行于同一进程中的其他可感知区域选项的组件 (例如 GNU `readline` 库) 之外，还能在子进程 (无论这些进程是否在运行 Python 解释器) 以及在查询环境而非当前 C 区域的操作 (例如 Python 自己的 [`locale.getdefaultlocale()`](../library/locale.md#locale.getdefaultlocale "locale.getdefaultlocale")) 中看到更新的设置。

(显式地或通过上述的隐式区域强制转换) 配置其中一个区域选项将自动为 [`sys.stdin`](../library/sys.md#sys.stdin "sys.stdin") 和 [`sys.stdout`](../library/sys.md#sys.stdout "sys.stdout") 启用 `surrogateescape` [错误处理句柄](../library/codecs.md#error-handlers) ([`sys.stderr`](../library/sys.md#sys.stderr "sys.stderr") 会继续使用 `backslashreplace` 如同在任何其他区域选项中一样)。 这种流处理行为可以按通常方式使用 `PYTHONIOENCODING` 来覆盖。

出于调试目的，如果激活了区域强制转换，或者如果当 Python 运行时被初始化时某个 _应该_ 触发强制转换的区域选项仍处于激活状态则设置 `PYTHONCOERCECLOCALE=warn` 将导致 Python 在 `stderr` 上发出警告消息。

还要注意，即使在区域转换转换被禁用，或者在其无法找到合适的目标区域时，默认 `PYTHONUTF8` 仍将在传统的基于 ASCII 的区域中被激活。 必须同时禁用这两项特性以强制解释器使用 `ASCII` 而不是 `UTF-8` 作为系统接口。

[可用性](../library/intro.md#availability): Unix。

在 3.7 版本加入: 请参阅 [**PEP 538**](https://peps.python.org/pep-0538/) 了解详情。

PYTHONDEVMODE¶
~~~
    

~~~
如果此环境变量被设为一个非空字符串，则会启用 [Python 开发模式](../library/devmode.md#devmode)，引入在默认情况下启用扩展会导致开销过大的额外运行时检查。 这等价于设置 `-X` `dev` 选项。

在 3.7 版本加入.

PYTHONUTF8¶
~~~
    

~~~
如果设为 `1` ，将会启用 [Python UTF-8 模式](../library/os.md#utf8-mode)。

若设为 `0` ，则会禁用 [Python UTF-8 模式](../library/os.md#utf8-mode) 。

设置任何其他非空字符串会在解释器初始化期间导致错误。

在 3.7 版本加入.

PYTHONWARNDEFAULTENCODING¶
~~~
    

~~~
如果该环境变量设为一个非空字符串，则在采用某地区默认编码时，将会引发一条 [`EncodingWarning`](../library/exceptions.md#EncodingWarning "EncodingWarning") 。

请参阅 [选择性的 EncodingWarning](../library/io.md#io-encoding-warning) 来了解详情。

在 3.10 版本加入.

PYTHONNODEBUGRANGES¶
~~~
    

~~~
如果设置了此变量，它会禁用在代码对象中包括将额外位置信息（结束行、开始列偏移量和结束列偏移量）映射到每条指令的映射表。 这在需要较小的代码对象和 pyc 文件时很有用处并可在解释器显示回溯时屏蔽额外的视觉位置提示。

在 3.11 版本加入.

PYTHONPERFSUPPORT¶
~~~
    

~~~
如果此变量被设为非零值，它将启用对 Linux `perf` 分析器的支持以便 Python 调用能被它检测到。

如果设为 `0`，则禁用 Linux `perf` 性能分析器支持。

另请参阅 `-X perf` 命令行选项和 [Python 对 Linux perf 性能分析器的支持](../howto/perf_profiling.md#perf-profiling)。

在 3.12 版本加入.

### 1.2.1. 调试模式变量¶

PYTHONDUMPREFS¶
~~~
    

~~~
如果设置，Python在关闭解释器，及转储对象和引用计数后仍将保持活动。

需用 [`--with-trace-refs`](configure.md#cmdoption-with-trace-refs) 编译选项来配置 Python。

PYTHONDUMPREFSFILE=FILENAME¶
~~~
    

~~~
