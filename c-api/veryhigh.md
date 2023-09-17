# 极高层级 API¶

本章节的函数将允许你执行在文件或缓冲区中提供的 Python 源代码，但它们将不允许你在更细节化的方式与解释器进行交互。

这些函数中有几个可以接受特定的语法前缀符号作为形参。 可用的前缀符号有 `Py_eval_input`, `Py_file_input` 和 `Py_single_input`。 这些符号会在接受它们作为形参的函数文档中加以说明。

还要注意这些函数中有几个可以接受 FILE* 形参。 有一个需要小心处理的特别问题是针对不同 C 库的 `FILE` 结构体可能是不相同且不兼容的。 （至少是）在 Windows 中，动态链接的扩展实际上有可能会使用不同的库，所以应当特别注意只有在确定这些函数是由 Python 运行时所使用的相同的库创建的情况下才将 FILE* 形参传给它们。

int Py_Main(int argc, wchar_t **argv)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

针对标准解释器的主程序。 嵌入了 Python 的程序将可使用此程序。 所提供的 _argc_ 和 _argv_ 形参应当与传给 C 程序的 `main()` 函数的形参相同（将根据用户的语言区域转换为）。 一个重要的注意事项是参数列表可能会被修改（但参数列表中字符串所指向的内容不会被修改）。 如果解释器正常退出（即未引发异常）则返回值将为 `0`，如果解释器因引发异常而退出则返回 `1`，或者如果形参列表不能表示有效的 Python 命令行则返回 `2`。

请注意如果引发了一个在其他场合下未处理的 [`SystemExit`](../library/exceptions.md#SystemExit "SystemExit")，此函数将不会返回 `1`，而是退出进程，只要 [`PyConfig.inspect`](init_config.md#c.PyConfig.inspect "PyConfig.inspect") 为零就会这样。

int Py_BytesMain(int argc, char **argv)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.8._

类似于 `Py_Main()` 但 _argv_ 是一个包含字节串的数组。

在 3.8 版本加入.

int PyRun_AnyFile(FILE *fp, const char *filename)¶  

    

~~~
这是针对下面 `PyRun_AnyFileExFlags()` 的简化版接口，将 _closeit_ 设为 `0` 而将 _flags_ 设为 `NULL`。

int PyRun_AnyFileFlags(FILE *fp, const char *filename, PyCompilerFlags *flags)¶  
~~~
    

~~~
这是针对下面 `PyRun_AnyFileExFlags()` 的简化版接口，将 _closeit_ 参数设为 `0`。

int PyRun_AnyFileEx(FILE *fp, const char *filename, int closeit)¶  
~~~
    

~~~
这是针对下面 `PyRun_AnyFileExFlags()` 的简化版接口，将 _flags_ 参数设为 `NULL`。

int PyRun_AnyFileExFlags(FILE *fp, const char *filename, int closeit, PyCompilerFlags *flags)¶  
~~~
    

~~~
如果 _fp_ 指向一个关联到交互设备（控制台或终端输入或 Unix 伪终端）的文件，则返回 `PyRun_InteractiveLoop()` 的值，否则返回 `PyRun_SimpleFile()` 的结果。 _filename_ 会使用文件系统的编码格式 ([`sys.getfilesystemencoding()`](../library/sys.md#sys.getfilesystemencoding "sys.getfilesystemencoding")) 来解码。 如果 _filename_ 为 `NULL`，此函数会使用 `"???"` 作为文件名。 如果 _closeit_ 为真值，文件会在 `PyRun_SimpleFileExFlags()` 返回之前被关闭。

int PyRun_SimpleString(const char *command)¶  
~~~
    

~~~
这是针对下面 `PyRun_SimpleStringFlags()` 的简化版接口，将 `PyCompilerFlags`* 参数设为 `NULL`。

int PyRun_SimpleStringFlags(const char *command, PyCompilerFlags *flags)¶  
~~~
    

~~~
根据 _flags_ 参数，在 [`__main__`](../library/__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") 模块中执行 Python 源代码。 如果 [`__main__`](../library/__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") 尚不存在，它将被创建。 成功时返回 `0`，如果引发异常则返回 `-1`。 如果发生错误，则将无法获得异常信息。 对于 _flags_ 的含义，请参阅下文。

请注意如果引发了一个在其他场合下未处理的 [`SystemExit`](../library/exceptions.md#SystemExit "SystemExit")，此函数将不会返回 `-1`，而是退出进程，只要 [`PyConfig.inspect`](init_config.md#c.PyConfig.inspect "PyConfig.inspect") 为零就会这样。

int PyRun_SimpleFile(FILE *fp, const char *filename)¶  
~~~
    

~~~
这是针对下面 `PyRun_SimpleFileExFlags()` 的简化版接口，将 _closeit_ 设为 `0` 而将 _flags_ 设为 `NULL`。

int PyRun_SimpleFileEx(FILE *fp, const char *filename, int closeit)¶  
~~~
    

~~~
这是针对下面 `PyRun_SimpleFileExFlags()` 的简化版接口，将 _flags_ 设为 `NULL`。

int PyRun_SimpleFileExFlags(FILE *fp, const char *filename, int closeit, PyCompilerFlags *flags)¶  
~~~
    

~~~
类似于 `PyRun_SimpleStringFlags()`，但 Python 源代码是从 _fp_ 读取而不是一个内存中的字符串。 _filename_ 应为文件名，它将使用 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 来解码。 如果 _closeit_ 为真值，则文件将在 `PyRun_SimpleFileExFlags()` 返回之前被关闭。

备注

在 Windows 上， _fp_ 应当以二进制模式打开 (即 `fopen(filename, "rb")`)。 否则，Python 可能无法正确地处理使用 LF 行结束符的脚本文件。

int PyRun_InteractiveOne(FILE *fp, const char *filename)¶  
~~~
    

~~~
这是针对下面 `PyRun_InteractiveOneFlags()` 的简化版接口，将 _flags_ 设为 `NULL`。

int PyRun_InteractiveOneFlags(FILE *fp, const char *filename, PyCompilerFlags *flags)¶  
~~~
    

~~~
根据 _flags_ 参数读取并执行来自与交互设备相关联的文件的一条语句。 用户将得到使用 `sys.ps1` 和 `sys.ps2` 的提示。 _filename_ 将使用 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 来解码。

当输入被成功执行时返回 `0`，如果引发异常则返回 `-1`，或者如果存在解析错误则返回来自作为 Python 的组成部分发布的 `errcode.h` 包括文件的错误代码。 （请注意 `errcode.h` 并未被 `Python.h` 所包括，因此如果需要则必须专门地包括。）

int PyRun_InteractiveLoop(FILE *fp, const char *filename)¶  
~~~
    

~~~
这是针对下面 `PyRun_InteractiveLoopFlags()` 的简化版接口，将 _flags_ 设为 `NULL`。

int PyRun_InteractiveLoopFlags(FILE *fp, const char *filename, PyCompilerFlags *flags)¶  
~~~
    

~~~
读取并执行来自与交互设备相关联的语句直至到达 EOF。 用户将得到使用 `sys.ps1` 和 `sys.ps2` 的提示。 _filename_ 将使用 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 来解码。 当位于 EOF 时将返回 `0`，或者当失败时将返回一个负数。

int (*PyOS_InputHook)(void)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

可以被设为指向一个原型为 `int func(void)` 的函数。 该函数将在Python 的解释器提示符即将空闲并等待用户从终端输入时被调用。 返回值会被忽略。 重载这个钩子可被用来将解释器的提示符集成到其他事件循环中，就像 Python 码中 `Modules/_tkinter.c` 所做的那样。

在 3.12 版本发生变更: 此函数只能被 [主解释器](init.md#sub-interpreter-support) 调用。

char *(*PyOS_ReadlineFunctionPointer)(FILE*, FILE*, const char*)¶  
~~~
    

~~~
可以被设为指向一个原型为 `char *func(FILE *stdin, FILE *stdout, char *prompt)` 的函数，重载被用来读取解释器提示符的一行输入的默认函数。 该函数被预期为如果字符串 _prompt_ 不为 `NULL` 就输出它，然后从所提供的标准输入文件读取一行输入，并返回结果字符串。 例如，[`readline`](../library/readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 模块将这个钩子设置为提供行编辑和 tab 键补全等功能。

结果必须是一个由 [`PyMem_RawMalloc()`](memory.md#c.PyMem_RawMalloc "PyMem_RawMalloc") 或 [`PyMem_RawRealloc()`](memory.md#c.PyMem_RawRealloc "PyMem_RawRealloc") 分配的字符串，或者如果发生错误则为 `NULL`。

在 3.4 版本发生变更: 结果必须由 [`PyMem_RawMalloc()`](memory.md#c.PyMem_RawMalloc "PyMem_RawMalloc") 或 [`PyMem_RawRealloc()`](memory.md#c.PyMem_RawRealloc "PyMem_RawRealloc") 分配，而不是由 [`PyMem_Malloc()`](memory.md#c.PyMem_Malloc "PyMem_Malloc") 或 [`PyMem_Realloc()`](memory.md#c.PyMem_Realloc "PyMem_Realloc") 分配。

在 3.12 版本发生变更: 此函数只能被 [主解释器](init.md#sub-interpreter-support) 调用。

[PyObject](structures.md#c.PyObject "PyObject") *PyRun_String(const char *str, int start, [PyObject](structures.md#c.PyObject "PyObject") *globals, [PyObject](structures.md#c.PyObject "PyObject") *locals)¶  

    _返回值：新的引用。_

这是针对下面 `PyRun_StringFlags()` 的简化版接口，将 _flags_ 设为 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyRun_StringFlags(const char *str, int start, [PyObject](structures.md#c.PyObject "PyObject") *globals, [PyObject](structures.md#c.PyObject "PyObject") *locals, PyCompilerFlags *flags)¶  

    _返回值：新的引用。_

在由对象 _globals_ 和 _locals_ 指定的上下文中执行来自 _str_ 的 Python 源代码，并使用以 _flags_ 指定的编译器旗标。 _globals_ 必须是一个字典； _locals_ 可以是任何实现了映射协议的对象。 形参 _start_ 指定了应当被用来解析源代码的起始形符。

返回将代码作为 Python 对象执行的结果，或者如果引发了异常则返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyRun_File(FILE *fp, const char *filename, int start, [PyObject](structures.md#c.PyObject "PyObject") *globals, [PyObject](structures.md#c.PyObject "PyObject") *locals)¶  

    _返回值：新的引用。_

这是针对下面 `PyRun_FileExFlags()` 的简化版接口，将 _closeit_ 设为 `0` 并将 _flags_ 设为 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyRun_FileEx(FILE *fp, const char *filename, int start, [PyObject](structures.md#c.PyObject "PyObject") *globals, [PyObject](structures.md#c.PyObject "PyObject") *locals, int closeit)¶  

    _返回值：新的引用。_

这是针对下面 `PyRun_FileExFlags()` 的简化版接口，将 _flags_ 设为 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyRun_FileFlags(FILE *fp, const char *filename, int start, [PyObject](structures.md#c.PyObject "PyObject") *globals, [PyObject](structures.md#c.PyObject "PyObject") *locals, PyCompilerFlags *flags)¶  

    _返回值：新的引用。_

这是针对下面 `PyRun_FileExFlags()` 的简化版接口，将 _closeit_ 设为 `0`。

[PyObject](structures.md#c.PyObject "PyObject") *PyRun_FileExFlags(FILE *fp, const char *filename, int start, [PyObject](structures.md#c.PyObject "PyObject") *globals, [PyObject](structures.md#c.PyObject "PyObject") *locals, int closeit, PyCompilerFlags *flags)¶  

    _返回值：新的引用。_

类似于 `PyRun_StringFlags()`，但 Python 源代码是从 _fp_ 读取而不是一个内存中的字符串。 _filename_ 应为文件名，它将使用 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 来解码。 如果 _closeit_ 为真值，则文件将在 `PyRun_FileExFlags()` 返回之前被关闭。

[PyObject](structures.md#c.PyObject "PyObject") *Py_CompileString(const char *str, const char *filename, int start)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

这是针对下面 `Py_CompileStringFlags()` 的简化版接口，将 _flags_ 设为 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *Py_CompileStringFlags(const char *str, const char *filename, int start, PyCompilerFlags *flags)¶  

    _返回值：新的引用。_

这是针对下面 `Py_CompileStringExFlags()` 的简化版接口，将 _optimize_ 设为 `-1`。

[PyObject](structures.md#c.PyObject "PyObject") *Py_CompileStringObject(const char *str, [PyObject](structures.md#c.PyObject "PyObject") *filename, int start, PyCompilerFlags *flags, int optimize)¶  

    _返回值：新的引用。_

解析并编译 _str_ 中的 Python 源代码，返回结果代码对象。 开始形符由 _start_ 给出；这可被用来约束可被编译的代码并且应当为 `Py_eval_input`, `Py_file_input` 或 `Py_single_input`。 由 _filename_ 指定的文件名会被用来构造代码对象并可能出现在回溯信息或 [`SyntaxError`](../library/exceptions.md#SyntaxError "SyntaxError") 异常消息中。 如果代码无法被解析或编译则此函数将返回 `NULL`。

整数 _optimize_ 指定编译器的优化级别；值 `-1` 将选择与 [`-O`](../using/cmdline.md#cmdoption-O) 选项相同的解释器优化级别。 显式级别为 `0` (无优化；`__debug__` 为真值)、`1` (断言被移除，`__debug__` 为假值) 或 `2` (文档字符串也被移除)。

在 3.4 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *Py_CompileStringExFlags(const char *str, const char *filename, int start, PyCompilerFlags *flags, int optimize)¶  

    _返回值：新的引用。_

与 `Py_CompileStringObject()` 类似，但 _filename_ 是以 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 解码出的字节串。

在 3.2 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyEval_EvalCode([PyObject](structures.md#c.PyObject "PyObject") *co, [PyObject](structures.md#c.PyObject "PyObject") *globals, [PyObject](structures.md#c.PyObject "PyObject") *locals)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

这是针对 `PyEval_EvalCodeEx()` 的简化版接口，只附带代码对象，以及全局和局部变量。 其他参数均设为 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyEval_EvalCodeEx([PyObject](structures.md#c.PyObject "PyObject") *co, [PyObject](structures.md#c.PyObject "PyObject") *globals, [PyObject](structures.md#c.PyObject "PyObject") *locals, [PyObject](structures.md#c.PyObject "PyObject") *const *args, int argcount, [PyObject](structures.md#c.PyObject "PyObject") *const *kws, int kwcount, [PyObject](structures.md#c.PyObject "PyObject") *const *defs, int defcount, [PyObject](structures.md#c.PyObject "PyObject") *kwdefs, [PyObject](structures.md#c.PyObject "PyObject") *closure)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

对一个预编译的代码对象求值，为其求值给出特定的环境。 此环境由全局变量的字典，局部变量映射对象，参数、关键字和默认值的数组，[仅限关键字](../glossary.md#keyword-only-parameter) 参数的默认值的字典和单元的封闭元组构成。

[PyObject](structures.md#c.PyObject "PyObject") *PyEval_EvalFrame([PyFrameObject](frame.md#c.PyFrameObject "PyFrameObject") *f)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

对一个执行帧求值。 这是针对 `PyEval_EvalFrameEx()` 的简化版接口，用于保持向下兼容性。

[PyObject](structures.md#c.PyObject "PyObject") *PyEval_EvalFrameEx([PyFrameObject](frame.md#c.PyFrameObject "PyFrameObject") *f, int throwflag)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

这是 Python 解释运行不带修饰的主函数。 与执行帧 _f_ 相关联的代码对象将被执行，解释字节码并根据需要执行调用。 额外的 _throwflag_ 形参基本可以被忽略 —— 如果为真值，则会导致立即抛出一个异常；这会被用于生成器对象的 [`throw()`](../reference/expressions.md#generator.throw "generator.throw") 方法。

在 3.4 版本发生变更: 该函数现在包含一个调试断言，用以确保不会静默地丢弃活动的异常。

int PyEval_MergeCompilerFlags(PyCompilerFlags *cf)¶  
~~~
    

~~~
此函数会修改当前求值帧的旗标，并在成功时返回真值，失败时返回假值。

int Py_eval_input¶  
~~~
    

~~~
Python 语法中用于孤立表达式的起始符号；配合 `Py_CompileString()` 使用。

int Py_file_input¶  
~~~
    

~~~
Python 语法中用于从文件或其他源读取语句序列的起始符号；配合 `Py_CompileString()` 使用。 这是在编译任意长的 Python 源代码时要使用的符号。

int Py_single_input¶  
~~~
    

~~~
Python 语法中用于单独语句的起始符号；配合 `Py_CompileString()` 使用。 这是用于交互式解释器循环的符号。

struct PyCompilerFlags¶  
~~~
    

~~~
这是用来存放编译器旗标的结构体。 对于代码仅被编译的情况，它将作为 `int flags` 传入，而对于代码要被执行的情况，它将作为 `PyCompilerFlags *flags` 传入。 在这种情况下，`from __future__ import` 可以修改 _flags_ 。

只要 `PyCompilerFlags *flags` 是 `NULL`，`cf_flags` 就会被视为等同于 `0`，而由于 `from __future__ import` 而产生的任何修改都会被丢弃。

int cf_flags¶  
~~~
    

~~~
编译器旗标。

int cf_feature_version¶  
~~~
    

~~~
_cf_feature_version_ 是 Python 的小版本号。 它应当被初始化为 `PY_MINOR_VERSION`。

该字段默认会被忽略，当且仅当在 `cf_flags` 中设置了 `PyCF_ONLY_AST` 旗标时它才会被使用。

在 3.8 版本发生变更: 增加了 _cf_feature_version_ 字段。

int CO_FUTURE_DIVISION¶  
~~~
    

~~~
