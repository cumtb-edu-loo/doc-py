# `py_compile` \--- 编译 Python 源文件¶

**源代码:** [Lib/py_compile.py](https://github.com/python/cpython/tree/3.12/Lib/py_compile.py)

* * *

`py_compile` 模块提供了用来从源文件生成字节码的函数和另一个用于当模块源文件作为脚本被调用时的函数。

虽然不太常用，但这个函数在安装共享模块时还是很有用的，特别是当一些用户可能没有权限在包含源代码的目录中写字节码缓存文件时。

_exception _py_compile.PyCompileError¶

    

~~~
当编译文件过程中发生错误时，抛出的异常。

py_compile.compile( _file_ , _cfile =None_, _dfile =None_, _doraise =False_, _optimize =-1_, _invalidation_mode =PycInvalidationMode.TIMESTAMP_, _quiet =0_)¶
~~~
    

~~~
将源文件编译成字节码并写入字节码缓存文件。 源代码将从名为 _file_ 的文件中加载。 字节码会被写入到 _cfile_ ，它默认为 [**PEP 3147**](https://peps.python.org/pep-3147/)/[ **PEP 488**](https://peps.python.org/pep-0488/) 路径，以 `.pyc` 结尾。 举例来说，如果 _file_ 为 `/foo/bar/baz.py` 则对于 Python 3.2 _cfile_ 将默认为 `/foo/bar/__pycache__/baz.cpython-32.pyc`。 如果指定了 _dfile_ ，则将用它而不是 _file_ 作为在异常回溯中获取并显示的源文件的名称。 如果 _doraise_ 为真值，则当编译 _file_ 遇到错误时将引发 `PyCompileError`。 如果 _doraise_ 为（默认的）假值，则会将错误字符串写入到 `sys.stderr`，但不会引发异常。 此函数返回已编译字节码文件的路径，即 _cfile_ 所使用的值。

_doraise_ 和 _quiet_ 参数确定在编译文件时如何处理错误。 如果 _quiet_ 为 0 或 1，并且 _doraise_ 为假值，则会启用默认行为：写入错误信息到 `sys.stderr`，并且函数将返回 `None` 而非一个路径。 如果 _doraise_ 为真值，则将改为引发 `PyCompileError`。 但是如果 _quiet_ 为 2，则不会写入消息，并且 _doraise_ 也不会有效果。

如果 _cfile_ 所表示（显式指定或计算得出）的路径为符号链接或非常规文件，则将引发 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError")。 此行为是用来警告如果允许写入编译后字节码文件到这些路径则导入操作将会把它们转为常规文件。 这是使用文件重命名来将最终编译后字节码文件放置到位以防止并发文件写入问题的导入操作的附带效果。

_optimize_ 控制优化级别并会被传给内置的 [`compile()`](functions.md#compile "compile") 函数。 默认值 `-1` 表示选择当前解释器的优化级别。

_invalidation_mode_ 应当是 `PycInvalidationMode` 枚举的成员，它控制在运行时如何让已生成的字节码缓存失效。 如果设置了 `SOURCE_DATE_EPOCH` 环境变量则默认值为 `PycInvalidationMode.CHECKED_HASH`，否则默认值为 `PycInvalidationMode.TIMESTAMP`。

在 3.2 版本发生变更: 将 _cfile_ 的默认值改成与 [**PEP 3147**](https://peps.python.org/pep-3147/) 兼容。 之前的默认值是 _file_ \+ `'c'` (如果启用优化则为 `'o'`)。 同时也添加了 _optimize_ 形参。

在 3.4 版本发生变更: 将代码更改为使用 [`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.") 执行字节码缓存文件写入。 这意味着文件创建/写入的语义现在与 [`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.") 所做的相匹配，例如权限、写入和移动语义等等。 同时也添加了当 _cfile_ 为符号链接或非常规文件时引发 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError") 的预警设置。

在 3.7 版本发生变更: _invalidation_mode_ 形参是根据 [**PEP 552**](https://peps.python.org/pep-0552/) 的描述添加的。 如果设置了 `SOURCE_DATE_EPOCH` 环境变量， _invalidation_mode_ 将被强制设为 `PycInvalidationMode.CHECKED_HASH`。

在 3.7.2 版本发生变更: `SOURCE_DATE_EPOCH` 环境变量不会再覆盖 _invalidation_mode_ 参数的值，而改为确定其默认值。

在 3.8 版本发生变更: 增加了 _quiet_ 形参。

_class _py_compile.PycInvalidationMode¶
~~~
    

~~~
一个由可用方法组成的枚举，解释器可以用来确定字节码文件是否与源文件保持一致。 `.pyc` 文件在其标头中指明了所需的失效模式。 请参阅 [已缓存字节码的失效](../reference/import.md#pyc-invalidation) 了解有关 Python 在运行时如何让 `.pyc` 文件失效的更多信息。

在 3.7 版本加入.

TIMESTAMP¶
~~~
    

~~~
`.pyc` 文件包括时间戳和源文件的大小，Python 将在运行时将其与源文件的元数据进行比较以确定 `.pyc` 文件是否需要重新生成。

CHECKED_HASH¶
~~~
    

~~~
`.pyc` 文件包括源文件内容的哈希值，Python 将在运行时将其与源文件内容进行比较以确定 `.pyc` 文件是否需要重新生成。

UNCHECKED_HASH¶
~~~
    

~~~
类似于 `CHECKED_HASH`，`.pyc` 文件包括源文件内容的哈希值。 但是，Python 将在运行时假定 `.pyc` 文件是最新的而完全不会将 `.pyc` 与源文件进行验证。

此选项适用于 `.pycs` 由 Python 以外的某个系统例如构建系统来确保最新的情况。

## 命令行接口¶

这个模块可作为脚本发起调用以编译多个源文件。 在 _filenames_ 中指定的文件会被编译并将结果字节码以普通方式进行缓存。 这个程序不会搜索目录结构来定位源文件；它只编译显式指定的文件。 如果某个文件无法被编译则退出状态为非零值。

<file> ... <fileN>¶

-¶
~~~

位置参数是要编译的文件。 如果 `-` 是唯一的形参，则文件列表将从标准输入获取。

-q, \--quiet¶
    

屏蔽错误输出。

在 3.2 版本发生变更: 添加了对 `-` 的支持。

在 3.10 版本发生变更: 添加了对 `-q` 的支持。

参见

模块 [`compileall`](compileall.md#module-compileall "compileall: Tools for byte-compiling all Python source files in a directory tree.")

    

~~~
