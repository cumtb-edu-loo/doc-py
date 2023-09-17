# `compileall` \--- 字节编译 Python 库¶

**源代码:** [Lib/compileall.py](https://github.com/python/cpython/tree/3.12/Lib/compileall.py)

* * *

这个模块提供了一些工具函数来支持安装 Python 库。 这些函数可以编译一个目录树中的 Python 源文件。 这个模块可被用来在安装库时创建缓存的字节码文件，这使得它们对于没有库目录写入权限的用户来说也是可用的。

[可用性](intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

## 使用命令行¶

此模块可以作为脚本运行 (使用 **python -m compileall** ) 来编译 Python 源代码。

directory ...¶

file ...¶

    

~~~
位置参数是要编译的文件或包含源文件的目录，目录将被递归地遍历。 如果没有给出参数，则其行为如同使用了命令行 `-l <directories from sys.path>`。

-l¶
~~~

不要递归到子目录，只编译直接包含在指明或隐含的目录中的源代码文件。

-f¶
    

强制重新构建即使时间戳是最新的。

-q¶
    

不要打印已编译文件的列表。 如果传入一次，则错误消息仍将被打印。 如果传入两次 (`-qq`)，所有输出都会被屏蔽。

-d destdir¶
    

要附加到每个被编译文件的路径之前的目录。 这将出现在编译时回溯信息中，并且还会被编译到字节码文件中，届时它将在字节码文件被执行而源文件已不存在的情况下被用于回溯和其他消息。

-s strip_prefix¶
    

-p prepend_prefix¶
    

移除 (`-s`) 或添加 (`-p`) 记录在 `.pyc` 文件中的给定路径前缀。 不可与 `-d` 一同使用。

-x regex¶
    

regex 会被用于搜索每个要执行编译的文件的完整路径，而如果 regex 产生了一个匹配，则相应文件会被跳过。

-i list¶
    

读取文件 `list` 并将其包含的每一行添加到要编译的文件和目录列表中。 如果 `list` 为 `-`，则从 `stdin` 读取行。

-b¶
    

将字节码写入到它们的传统位置和名称，这可能会覆盖由另一版本的 Python 所创建的字节码文件。 默认是将文件写入到它们的 [**PEP 3147**](https://peps.python.org/pep-3147/) 位置和名称，这允许来自多个版本的 Python 字节码文件共存。

-r¶
    

控制子目录的最大递归层级。 如果给出此选项，则 `-l` 选项将不会被考虑。 **python -m compileall <directory> -r 0** 等价于 **python -m compileall <directory> -l**。

-j N¶
    

使用 _N_ 个工作者来编译给定目录中的文件。 如果使用 `0`，则将使用 [`os.cpu_count()`](os.md#os.cpu_count "os.cpu_count") 的结果。

\--invalidation-mode [timestamp|checked-hash|unchecked-hash]¶

    

~~~
控制生成的字节码文件在运行时的失效规则。 值为 `timestamp`，意味着将生成嵌入了源时间戳和大小的 `.pyc` 文件。 `checked-hash` 和 `unchecked-hash` 等值将导致生成基于哈希的 pyc。 基于哈希的 pyc 嵌入了源文件内容的哈希值而不是时间戳。 请参阅 [已缓存字节码的失效](../reference/import.md#pyc-invalidation) 了解有关 Python 在运行时如何验证字节码缓存文件的更多信息。 如果未设置 `SOURCE_DATE_EPOCH` 环境变量则默认值为 `timestamp`，而如果设置了 `SOURCE_DATE_EPOCH` 则为 `checked-hash`。

-o level¶
~~~

使用给定的优化级别进行编译。 可以多次使用来一次性地针对多个级别进行编译 (例如，`compileall -o 1 -o 2`)。

-e dir¶
    

忽略指向给定目录之外的符号链接。

\--hardlink-dupes¶

    

~~~
如果两个不同优化级别的 `.pyc` 文件具有相同的内容，则使用硬链接来合并重复的文件。

在 3.2 版本发生变更: 增加了 `-i`, `-b` 和 `-h` 选项。

在 3.5 版本发生变更: 增加了 `-j`, `-r` 和 `-qq` 选项。 `-q` 选项改为多级别值。 `-b` 将总是产生以 `.pyc` 为后缀的字节码文件，而不是 `.pyo`。

在 3.7 版本发生变更: 增加了 `--invalidation-mode` 选项。

在 3.9 版本发生变更: 增加了 `-s`, `-p`, `-e` 和 `--hardlink-dupes` 选项。 将默认的递归限制从 10 提高到 [`sys.getrecursionlimit()`](sys.md#sys.getrecursionlimit "sys.getrecursionlimit")。 增加允许多次指定 `-o` 选项。

没有可以控制 [`compile()`](functions.md#compile "compile") 函数所使用的优化级别的命令行选项，因为 Python 解释器本身已经提供了该选项: **python -O -m compileall** 。

类似地，[`compile()`](functions.md#compile "compile") 函数会遵循 [`sys.pycache_prefix`](sys.md#sys.pycache_prefix "sys.pycache_prefix") 设置。 所生成的字节码缓存将仅在 [`compile()`](functions.md#compile "compile") 附带与将在运行时使用的相同 [`sys.pycache_prefix`](sys.md#sys.pycache_prefix "sys.pycache_prefix") 时可用（如果存在该设置）。

## 公有函数¶

compileall.compile_dir( _dir_ , _maxlevels =sys.getrecursionlimit()_, _ddir =None_, _force =False_, _rx =None_, _quiet =0_, _legacy =False_, _optimize =-1_, _workers =1_, _invalidation_mode =None_, _*_ , _stripdir =None_, _prependdir =None_, _limit_sl_dest =None_, _hardlink_dupes =False_)¶
~~~
    

~~~
递归地深入名为 _dir_ 的目录树，在途中编译所有 `.py` 文件。 如果所有文件都编译成功则返回真值，否则返回假值。

_maxlevels_ 形参被用来限制递归深度；它默认为 `sys.getrecursionlimit()`。

如果给出了 _ddir_ ，它会被附加到每个被编译的文件的路径之前以便在编译时回溯中使用，同时还会被编译到字节码文件中，届时它将在字节码文件被执行而源文件已不存在的情况下被用于回溯和其他消息中。

如果 _force_ 为真值，则即使时间戳为最新模块也会被重新编译。

如果给出了 _rx_ ，则它的 `search` 方法会在准备编译的每个文件的完整路径上被调用，并且如果它返回真值，则该文件会被跳过。 这可被用来排除与一个正则表达式相匹配的文件，正则表达式将以 [re.Pattern](re.md#re-objects) 对象的形式给出。

如果 _quiet_ 为 `False` 或 `0` (默认值)，则文件名和其他信息将被打印到标准输出。 如果设为 `1`，则只打印错误。 如果设为 `2`，则屏蔽所有输出。

如果 _legacy_ 为真值，则将字节码文件写入到它们的传统位置和名称，这可能会覆盖由另一版本的 Python 所创建的字节码文件。 默认是将文件写入到它们的 [**PEP 3147**](https://peps.python.org/pep-3147/) 位置和名称，这允许来自多个版本的 Python 字节码文件共存。

_optimize_ 指明编译器的优化级别。 它会被传给内置 [`compile()`](functions.md#compile "compile") 函数。 还接受一个优化层别列表这将在单次调用中多次编译一个 `.py` 文件。

参数 _workers_ 指明要使用多少个工作进程来并行编译文件。 默认设置不使用多个工作进程。 如果平台不能使用多个工作进程而又给出了 _workers_ 参数，则将回退为使用顺序编译。 如果 _workers_ 为 0，则会使用系统的核心数量。 如果 _workers_ 小于 `0`，则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

_invalidation_mode_ 应为 [`py_compile.PycInvalidationMode`](py_compile.md#py_compile.PycInvalidationMode "py_compile.PycInvalidationMode") 枚举的成员之一并将控制所生成的 pyc 在运行时以何种方式验证是否失效。

_stripdir_ , _prependdir_ 和 _limit_sl_dest_ 参数分别对应上述的 `-s`, `-p` 和 `-e` 选项。 它们可以被指定为 `str` 或 [`os.PathLike`](os.md#os.PathLike "os.PathLike")。

如果 _hardlink_dupes_ 为真值且两个使用不同优化级别的 `.pyc` 文件具有相同的内容，则会使用硬链接来合并重复的文件。

在 3.2 版本发生变更: 增加了 _legacy_ 和 _optimize_ 形参。

在 3.5 版本发生变更: 增加了 _workers_ 形参。

在 3.5 版本发生变更: _quiet_ 形参已改为多级别值。

在 3.5 版本发生变更: _legacy_ 形参将只写入 `.pyc` 文件而非 `.pyo` 文件，无论 _optimize_ 的值是什么。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.7 版本发生变更: 增加了 _invalidation_mode_ 形参。

在 3.7.2 版本发生变更: _invalidation_mode_ 形参的默认值更新为 None。

在 3.8 版本发生变更: 将 _workers_ 设为 0 现在将会选择最优核心数量。

在 3.9 版本发生变更: 增加了 _stripdir_ , _prependdir_ , _limit_sl_dest_ 和 _hardlink_dupes_ 参数。 _maxlevels_ 的默认值从 `10` 改为 `sys.getrecursionlimit()`

compileall.compile_file( _fullname_ , _ddir =None_, _force =False_, _rx =None_, _quiet =0_, _legacy =False_, _optimize =-1_, _invalidation_mode =None_, _*_ , _stripdir =None_, _prependdir =None_, _limit_sl_dest =None_, _hardlink_dupes =False_)¶
~~~
    

~~~
编译路径为 _fullname_ 的文件。 如果文件编译成功则返回真值，否则返回假值。

如果给出了 _ddir_ ，它会被附加到被编译的文件的路径之前以便在编译时回溯中使用，同时还会被编译到字节码文件中，届时它将在字节码文件被执行而源文件已不存在的情况下被用于回溯和其他消息中。

如果给出了 _rx_ ，则会向它的 `search` 方法传入准备编译的文件的完整路径，并且如果它返回真值，则不编译该文件并返回 `True`。 这可被用来排除与一个正则表达式相匹配的文件，正则表达式将以 [re.Pattern](re.md#re-objects) 对象的形式给出。

如果 _quiet_ 为 `False` 或 `0` (默认值)，则文件名和其他信息将被打印到标准输出。 如果设为 `1`，则只打印错误。 如果设为 `2`，则屏蔽所有输出。

如果 _legacy_ 为真值，则将字节码文件写入到它们的传统位置和名称，这可能会覆盖由另一版本的 Python 所创建的字节码文件。 默认是将文件写入到它们的 [**PEP 3147**](https://peps.python.org/pep-3147/) 位置和名称，这允许来自多个版本的 Python 字节码文件共存。

_optimize_ 指明编译器的优化级别。 它会被传给内置 [`compile()`](functions.md#compile "compile") 函数。 还接受一个优化层别列表这将在单次调用中多次编译一个 `.py` 文件。

_invalidation_mode_ 应为 [`py_compile.PycInvalidationMode`](py_compile.md#py_compile.PycInvalidationMode "py_compile.PycInvalidationMode") 枚举的成员之一并将控制所生成的 pyc 在运行时以何种方式验证是否失效。

_stripdir_ , _prependdir_ 和 _limit_sl_dest_ 参数分别对应上述的 `-s`, `-p` 和 `-e` 选项。 它们可以被指定为 `str` 或 [`os.PathLike`](os.md#os.PathLike "os.PathLike")。

如果 _hardlink_dupes_ 为真值且两个使用不同优化级别的 `.pyc` 文件具有相同的内容，则会使用硬链接来合并重复的文件。

在 3.2 版本加入.

在 3.5 版本发生变更: _quiet_ 形参已改为多级别值。

在 3.5 版本发生变更: _legacy_ 形参将只写入 `.pyc` 文件而非 `.pyo` 文件，无论 _optimize_ 的值是什么。

在 3.7 版本发生变更: 增加了 _invalidation_mode_ 形参。

在 3.7.2 版本发生变更: _invalidation_mode_ 形参的默认值更新为 None。

在 3.9 版本发生变更: 增加了 _stripdir_ , _prependdir_ , _limit_sl_dest_ 和 _hardlink_dupes_ 参数。

compileall.compile_path( _skip_curdir =True_, _maxlevels =0_, _force =False_, _quiet =0_, _legacy =False_, _optimize =-1_, _invalidation_mode =None_)¶
~~~
    

~~~
将在 `sys.path` 中找到的所有 `.py` 文件编译为字节码。 如果所有文件编译成功则返回真值，否则返回假值。

如果 _skip_curdir_ 为真值（默认），则当前目录不会被包括在搜索中。 所有其他形参将被传递给 `compile_dir()` 函数。 请注意不同于其他编译函数，`maxlevels` 默认为 `0`。

在 3.2 版本发生变更: 增加了 _legacy_ 和 _optimize_ 形参。

在 3.5 版本发生变更: _quiet_ 形参已改为多级别值。

在 3.5 版本发生变更: _legacy_ 形参将只写入 `.pyc` 文件而非 `.pyo` 文件，无论 _optimize_ 的值是什么。

在 3.7 版本发生变更: 增加了 _invalidation_mode_ 形参。

在 3.7.2 版本发生变更: _invalidation_mode_ 形参的默认值更新为 None。

强制重新编译 `Lib/` 子目录及其所有子目录下的全部 `.py` 文件:
~~~
    
    
~~~
import compileall

compileall.compile_dir('Lib/', force=True)

# Perform same compilation, excluding files in .svn directories.
import re
compileall.compile_dir('Lib/', rx=re.compile(r'[/\\][.]svn'), force=True)

# pathlib.Path objects can also be used.
import pathlib
compileall.compile_dir(pathlib.Path('Lib/'), force=True)
~~~

参见

模块 [`py_compile`](py_compile.md#module-py_compile "py_compile: Generate byte-code files from Python source files.")

    

~~~
