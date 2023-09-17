# `runpy` ——查找并执行 Python 模块¶

**源代码：** [Lib/runpy.py](https://github.com/python/cpython/tree/3.12/Lib/runpy.py)

* * *

`runpy` 模块用于找到并运行 Python 的模块，而无需首先导入。主要用于实现 [`-m`](1.%20命令行与环境.md#cmdoption-m) 命令行开关，以允许用 Python 模块命名空间而不是文件系统来定位脚本。

请注意，这 _并非_ 一个沙盒模块——所有代码都在当前进程中运行，所有副作用（如其他模块对导入操作进行了缓存）在函数返回后都会留存。

此外，在 `runpy` 函数返回后，任何由已执行代码定义的函数和类都不能保证正确工作。如果某使用场景不能接收此限制，那么选用 [`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.") 可能更合适些。

`runpy` 模块提供两个函数：

runpy.run_module( _mod_name_ , _init_globals =None_, _run_name =None_, _alter_sys =False_)¶

    

~~~
执行给定模块的代码，并返回结果模块的 globals 字典。 该模块的代码首先会用标准的导入机制去查找定位 (详情请参阅 [**PEP 302**](https://peps.python.org/pep-0302/))，然后在全新的模块命名空间中运行。

_mod_name_ 参数应当是一个绝对模块名。 如果模块名指向一个包而非普通模块，则会导入这个包然后执行这个包中的 [`__main__`](__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") 子模块再返回模块全局字典。

可选的字典参数 _init_globals_ 可用来在代码执行前预填充模块的 globals 字典。给出的字典参数不会被修改。如果字典中定义了以下任意一个特殊全局变量，这些定义都会被 `run_module()` 覆盖。

The special global variables `__name__`, `__spec__`, `__file__`, `__cached__`, `__loader__` and `__package__` are set in the globals dictionary before the module code is executed (Note that this is a minimal set of variables - other variables may be set implicitly as an interpreter implementation detail).

若可选参数 `__name__` 不为 [`None`](constants.md#None "None") 则设为 _run_name_ ，若此名称的模块是一个包则设为 `mod_name + '.__main__'`，否则设为 _mod_name_ 参数。

`__spec__` 将设为合适的 _实际_ 导入模块（也就是说，`__spec__.name` 一定是 _mod_name_ 或 `mod_name + '.__main__`，而不是 _run_name_ ）。

`__file__` 、`__cached__`、 `__loader__` 和 `__package__` 根据模块规格进行 [常规设置](../reference/import.md#import-mod-attrs)

如果给出了参数 _alter_sys_ 并且值为 [`True`](constants.md#True "True")，那么 `sys.argv[0]` 将被更新为 `__file__` 的值，`sys.modules[__name__]` 将被更新为临时模块对象。在函数返回前， `sys.argv[0]` 和 `sys.modules[__name__]` 将会复原。

请注意对 [`sys`](sys.md#module-sys "sys: Access system-specific parameters and functions.") 的这种操作不是线程安全的。 其他线程可能会看到部分初始化的模块，以及更改后的参数列表。 建议当从线程中的代码调用此函数时不要使用 `sys` 模块。

参见

[`-m`](../using/cmdline.md#cmdoption-m) 选项由命令行提供相同功能。

在 3.1 版本发生变更: 增加了通过查找 [`__main__`](__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") 子模块来执行包的功能。

在 3.2 版本发生变更: 加入了 `__cached__` 全局变量（参见 [**PEP 3147**](https://peps.python.org/pep-3147/) ）。

在 3.4 版本发生变更: 充分利用 [**PEP 451**](https://peps.python.org/pep-0451/) 加入的模块规格功能。使得以这种方式运行的模块能够正确设置 `__cached__`，并确保真正的模块名称总是可以通过 `__spec__.name` 的形式访问。

在 3.12 版本发生变更: `__cached__`, `__loader__` 和 `__package__` 的设置已被弃用。 替代设置参见 [`ModuleSpec`](importlib.md#importlib.machinery.ModuleSpec "importlib.machinery.ModuleSpec")。

runpy.run_path( _path_name_ , _init_globals =None_, _run_name =None_)¶
~~~
    

~~~
执行指定文件系统位置上的代码并返回结果模块的 globals 字典。 与提供给 CPython 命令行的脚本名称一样，所提供的路径可以指向 Python 源文件、编译后的字节码文件或包含 [`__main__`](__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") 模块的有效 [`sys.path`](sys.md#sys.path "sys.path") 条目（例如一个包含最高层级 `__main__.py` 文件的 zip 文件）。

对于简单的脚本而言，只需在新的模块命名空间中执行指定的代码即可。 对于一个有效的 [`sys.path`](sys.md#sys.path "sys.path") 条目（通常是一个 zip 文件或目录），首先会将该条目添加到 `sys.path` 的开头。 然后函数会使用更新后的路径查找并执行 [`__main__`](__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") 模块。 请注意如果在指定的位置上没有 `__main__` 模块那么在发起调用位于 `sys.path` 中其他位置上的现有条目时也不会受到特殊保护。

利用可选的字典参数 _init_globals_ ，可在代码执行前预填模块的 globals 字典。给出的字典参数不会被修改。如果给出的字典中定义了下列特殊全局变量，这些定义均会被 `run_module()` 覆盖。

The special global variables `__name__`, `__spec__`, `__file__`, `__cached__`, `__loader__` and `__package__` are set in the globals dictionary before the module code is executed (Note that this is a minimal set of variables - other variables may be set implicitly as an interpreter implementation detail).

如果该可选参数不为 [`None`](constants.md#None "None")，则 `__name__` 被设为 _run_name_ ，否则为 `'<run_path>'`。

如果提供的路径直接引用了一个脚本文件（无论是源码文件还是预编译的字节码），那么 `__file__` 将设为给出的路径，而 `__spec__`、`__cached__`、`__loader__` 和 `__package__` 都将设为 [`None`](constants.md#None "None")。

如果给出的路径是对有效 [`sys.path`](sys.md#sys.path "sys.path") 条目的引用，那么 `__spec__` 将为导入的 [`__main__`](__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") 模块进行正确设置 (也就是说，`__spec__.name` 将总是为 `__main__`)。 `__file__`, `__cached__`, `__loader__` 和 `__package__` 将依据模块规格说明 [正常设置](../reference/import.md#import-mod-attrs)。

[`sys`](sys.md#module-sys "sys: Access system-specific parameters and functions.") 模块也进行了多处发动。 首先，[`sys.path`](sys.md#sys.path "sys.path") 可能做上文所述的修改。 `sys.argv[0]` 会使用 `path_name` 的值进行更新而 `sys.modules[__name__]` 会使用对应于正在被执行的模块的临时模块对象进行更新。 在函数返回之前对 [`sys`](sys.md#module-sys "sys: Access system-specific parameters and functions.") 中条目的所有修改都会被复原。

请注意，与 `run_module()` 不同，对 [`sys`](sys.md#module-sys "sys: Access system-specific parameters and functions.") 的修改在本函数中不是可选项，因为这些调整对于允许执行 [`sys.path`](sys.md#sys.path "sys.path") 条目来说是至关重要的。 由于线程安全限制仍然适用，在线程代码中使用该函数应当使用导入锁进行序列化，或是委托给单独的进程。

参见

[接口选项](../using/cmdline.md#using-on-interface-options) 用于在命令行上实现同等功能（`python path/to/script`）。

在 3.2 版本加入.

在 3.4 版本发生变更: 进行更新以便利用 [**PEP 451**](https://peps.python.org/pep-0451/) 加入的模块规格特性。 这允许在 `__main__` 是从有效的 [`sys.path`](sys.md#sys.path "sys.path") 条目导入而不是直接执行的情况下能够正确地设置 `__cached__`。

在 3.12 版本发生变更: `__cached__`, `__loader__` 和 `__package__` 已被弃用。

参见

[**PEP 338**](https://peps.python.org/pep-0338/) \-- 将模块作为脚本执行
~~~
    

~~~
PEP 由 Nick Coghlan 撰写并实现。

[**PEP 366**](https://peps.python.org/pep-0366/) ——主模块的显式相对导入
~~~
    

~~~
PEP 由 Nick Coghlan 撰写并实现。

[**PEP 451**](https://peps.python.org/pep-0451/) —— 导入系统采用的 ModuleSpec 类型
~~~
    

~~~
