# 异常处理¶

本章描述的函数将让你处理和触发 Python 异常。 了解一些 Python 异常处理的基础知识是很重要的。 它的工作原理有点像 POSIX `errno` 变量: (每个线程) 有一个最近发生的错误的全局指示器。 大多数 C API 函数在成功执行时将不理会它。 大多数 C API 函数也会返回一个错误指示器，如果它们应当返回一个指针则会返回 `NULL`，或者如果它们应当返回一个整数则会返回 `-1` (例外情况: `PyArg_*` 函数返回 `1` 表示成功而 `0` 表示失败)。

具体地说，错误指示器由三个对象指针组成：异常的类型，异常的值，和回溯对象。如果没有错误被设置，这些指针都可以是 `NULL` （尽管一些组合使禁止的，例如，如果异常类型是 `NULL`，你不能有一个非 `NULL` 的回溯）。

当一个函数由于它调用的某个函数失败而必须失败时，通常不会设置错误指示器；它调用的那个函数已经设置了它。而它负责处理错误和清理异常，或在清除其拥有的所有资源后返回（如对象应用或内存分配）。如果不准备处理异常，则 _不_ 应该正常地继续。如果是由于一个错误返回，那么一定要向调用者表明已经设置了错误。如果错误没有得到处理或小心传播，对 Python/C API的其它调用可能不会有预期的行为，并且可能会以某种神秘的方式失败。

备注

错误指示器 **不是** [`sys.exc_info()`](3.标准库/sys.md#sys.exc_info "sys.exc_info") 的执行结果。前者对应尚未捕获的异常（异常还在传播），而后者在捕获异常后返回这个异常（异常已经停止传播）。

## 打印和清理¶

void PyErr_Clear()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

清除错误指示器。如果没有设置错误指示器，则不会有作用。

void PyErr_PrintEx(int set_sys_last_vars)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将标准回溯打印到 `sys.stderr` 并清除错误指示器。 **除非** 错误是 `SystemExit`，这种情况下不会打印回溯进程，且会退出 Python 进程，并显示 `SystemExit` 实例指定的错误代码。

只有在错误指示器被设置时才需要调用这个函数，否则这会导致错误！

如果 _set_sys_last_vars_ 为非零值，则变量 [`sys.last_exc`](3.标准库/sys.md#sys.last_exc "sys.last_exc") 将被设为要打印的异常。 出于向下兼容性考虑，已弃用的变量 [`sys.last_type`](3.标准库/sys.md#sys.last_type "sys.last_type"), [`sys.last_value`](3.标准库/sys.md#sys.last_value "sys.last_value") 和 [`sys.last_traceback`](3.标准库/sys.md#sys.last_traceback "sys.last_traceback") 也会被分别设为该异常的类型, 值和回溯。

在 3.12 版本发生变更: 增加了对 [`sys.last_exc`](3.标准库/sys.md#sys.last_exc "sys.last_exc") 的设置。

void PyErr_Print()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

`PyErr_PrintEx(1)` 的别名。

void PyErr_WriteUnraisable([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

使用当前异常和 _obj_ 参数调用 [`sys.unraisablehook()`](3.标准库/sys.md#sys.unraisablehook "sys.unraisablehook")。

当异常已被设置但解释器不可能实际引发该异常时，这个工具函数会向 `sys.stderr` 打印一条警告消息。 例如，当异常发生在 [`__del__()`](3.%20数据模型.md#object.__del__ "object.__del__") 方法中时就会使用该函数。

该函数使用单个参数 _obj_ 进行调用，该参数标识发生不可触发异常的上下文。如果可能， _obj_ 的报告将打印在警告消息中。

调用此函数时必须设置一个异常。

void PyErr_DisplayException([PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

将 `exc` 的标准回溯显示打印到 `sys.stderr`，包括链式异常和注释。

在 3.12 版本加入.

## 抛出异常¶

这些函数可帮助你设置当前线程的错误指示器。为了方便起见，一些函数将始终返回 `NULL` 指针，以便用于 `return` 语句。

void PyErr_SetString([PyObject](structures.md#c.PyObject "PyObject") *type, const char *message)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是设置错误指示器最常用的方式。 第一个参数指定异常类型；它通常为某个标准异常，例如 `PyExc_RuntimeError`。 你无需为其创建新的 [strong reference](../glossary.md#term-strong-reference) (例如使用 [`Py_INCREF()`](refcounting.md#c.Py_INCREF "Py_INCREF"))。 第二个参数是一条错误消息；它是用 `'utf-8'` 解码的。

void PyErr_SetObject([PyObject](structures.md#c.PyObject "PyObject") *type, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

此函数类似于 `PyErr_SetString()`，但是允许你为异常的“值”指定任意一个 Python 对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_Format([PyObject](structures.md#c.PyObject "PyObject") *exception, const char *format, ...)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable)._

这个函数设置了一个错误指示器并且返回了 `NULL`， _exception_ 应当是一个 Python 中的异常类。 _format_ 和随后的形参会帮助格式化这个错误的信息；它们与 [`PyUnicode_FromFormat()`](10.C%20API接口/unicode.md#c.PyUnicode_FromFormat "PyUnicode_FromFormat") 有着相同的含义和值。 _format_ 是一个ASCII编码的字符串。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_FormatV([PyObject](structures.md#c.PyObject "PyObject") *exception, const char *format, va_list vargs)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.5._

和 `PyErr_Format()` 相同，但它接受一个 `va_list` 类型的参数而不是可变数量的参数集。

在 3.5 版本加入.

void PyErr_SetNone([PyObject](structures.md#c.PyObject "PyObject") *type)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是 `PyErr_SetObject(type, Py_None)` 的简写。

int PyErr_BadArgument()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是 `PyErr_SetString(PyExc_TypeError, message)` 的简写，其中 _message_ 指出使用了非法参数调用内置操作。它主要用于内部使用。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_NoMemory()¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable)._

这是 `PyErr_SetNone(PyExc_MemoryError)` 的简写；它返回 `NULL` ，以便当内存耗尽时，对象分配函数可以写 `return PyErr_NoMemory();` 。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_SetFromErrno([PyObject](structures.md#c.PyObject "PyObject") *type)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable)._

这是一个便捷函数，当在 C 库函数返回错误并设置 C 变量 `errno` 时它会引发一个异常。 它构造了一个元组对象，其第一项是整数值 `errno` 而第二项是对应的错误信息（从 `strerror()` 获取），然后调用 `PyErr_SetObject(type, object)`。 在 Unix 上，当 `errno` 的值为 `EINTR` 时，表示有一个中断的系统调用，这将会调用 `PyErr_CheckSignals()`，如果它设置了错误指示符，则让其保持该设置。 该函数总是返回 `NULL`，因此当系统调用返回错误时该系统调用的包装函数可以写入 `return PyErr_SetFromErrno(type);`。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_SetFromErrnoWithFilenameObject([PyObject](structures.md#c.PyObject "PyObject") *type, [PyObject](structures.md#c.PyObject "PyObject") *filenameObject)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable)._

与 `PyErr_SetFromErrno()` 类似，但如果 _filenameObject_ 不为 `NULL`，它将作为第三个参数传递给 _type_ 的构造函数。 在 [`OSError`](3.标准库/exceptions.md#OSError "OSError") 异常的情况下，它将被用于定义异常实例的 `filename` 属性。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_SetFromErrnoWithFilenameObjects([PyObject](structures.md#c.PyObject "PyObject") *type, [PyObject](structures.md#c.PyObject "PyObject") *filenameObject, [PyObject](structures.md#c.PyObject "PyObject") *filenameObject2)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

类似于 `PyErr_SetFromErrnoWithFilenameObject()` ，但接受第二个 filename 对象，用于当一个接受两个 filename 的函数失败时触发错误。

在 3.4 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_SetFromErrnoWithFilename([PyObject](structures.md#c.PyObject "PyObject") *type, const char *filename)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable)._

类似于 `PyErr_SetFromErrnoWithFilenameObject()`，但文件名以 C 字符串形式给出。 _filename_ 是用 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 解码的。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_SetFromWindowsErr(int ierr)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable) on Windows since version 3.7._

这是一个用于引发 [`WindowsError`](3.标准库/exceptions.md#WindowsError "WindowsError") 的便捷函数。 如果在调用传传入的 _ierr_ 值为 `0`，则会改用对 `GetLastError()` 的调用所返回的错误代码。 它将调用 Win32 函数 `FormatMessage()` 来获取 _ierr_ 或 `GetLastError()` 所给出的错误代码的 Windows 描述，然后构造一个元组对象，其第一项为 _ierr_ 值而第二项为相应的错误消息（从 `FormatMessage()` 获取），然后调用 `PyErr_SetObject(PyExc_WindowsError, object)`。 该函数将总是返回 `NULL`。

[可用性](3.标准库/intro.md#availability): Windows。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_SetExcFromWindowsErr([PyObject](structures.md#c.PyObject "PyObject") *type, int ierr)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable) on Windows since version 3.7._

类似于 `PyErr_SetFromWindowsErr()` ，额外的参数指定要触发的异常类型。

[可用性](3.标准库/intro.md#availability): Windows。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_SetFromWindowsErrWithFilename(int ierr, const char *filename)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable) on Windows since version 3.7._

与 `PyErr_SetFromWindowsErr()` 类似，额外的不同点是如果 _filename_ 不为 `NULL` ，则会使用文件系统编码格式 ([`os.fsdecode()`](os.md#os.fsdecode "os.fsdecode")) 进行解码并作为第三个参数传递给 [`OSError`](3.标准库/exceptions.md#OSError "OSError") 的构造器用于定义异常实例的 `filename` 属性。

[可用性](3.标准库/intro.md#availability): Windows。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_SetExcFromWindowsErrWithFilenameObject([PyObject](structures.md#c.PyObject "PyObject") *type, int ierr, [PyObject](structures.md#c.PyObject "PyObject") *filename)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable) on Windows since version 3.7._

与 `PyErr_SetExcFromWindowsErr()` 类似，额外的不同点是如果 _filename_ 不为 `NULL`，它将作为第三个参数传递给 [`OSError`](3.标准库/exceptions.md#OSError "OSError") 的构造器用于定义异常实例的 `filename` 属性。

[可用性](3.标准库/intro.md#availability): Windows。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_SetExcFromWindowsErrWithFilenameObjects([PyObject](structures.md#c.PyObject "PyObject") *type, int ierr, [PyObject](structures.md#c.PyObject "PyObject") *filename, [PyObject](structures.md#c.PyObject "PyObject") *filename2)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable) on Windows since version 3.7._

类似于 `PyErr_SetExcFromWindowsErrWithFilenameObject()` ，但是接受第二个 filename 对象。

[可用性](3.标准库/intro.md#availability): Windows。

在 3.4 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_SetExcFromWindowsErrWithFilename([PyObject](structures.md#c.PyObject "PyObject") *type, int ierr, const char *filename)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable) on Windows since version 3.7._

类似于 `PyErr_SetFromWindowsErrWithFilename()` ，额外参数指定要触发的异常类型。

[可用性](3.标准库/intro.md#availability): Windows。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_SetImportError([PyObject](structures.md#c.PyObject "PyObject") *msg, [PyObject](structures.md#c.PyObject "PyObject") *name, [PyObject](structures.md#c.PyObject "PyObject") *path)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

这是触发 [`ImportError`](3.标准库/exceptions.md#ImportError "ImportError") 的便捷函数。 _msg_ 将被设为异常的消息字符串。 _name_ 和 _path_ ，（都可以为 `NULL` ），将用来被设置 [`ImportError`](3.标准库/exceptions.md#ImportError "ImportError") 对应的属性 `name` 和 `path`。

在 3.3 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_SetImportErrorSubclass([PyObject](structures.md#c.PyObject "PyObject") *exception, [PyObject](structures.md#c.PyObject "PyObject") *msg, [PyObject](structures.md#c.PyObject "PyObject") *name, [PyObject](structures.md#c.PyObject "PyObject") *path)¶  

    _返回值：总是为 NULL。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.6._

和 `PyErr_SetImportError()` 很类似，但这个函数允许指定一个 [`ImportError`](3.标准库/exceptions.md#ImportError "ImportError") 的子类来触发。

在 3.6 版本加入.

void PyErr_SyntaxLocationObject([PyObject](structures.md#c.PyObject "PyObject") *filename, int lineno, int col_offset)¶  

    

~~~
设置当前异常的文件，行和偏移信息。如果当前异常不是 [`SyntaxError`](../library/exceptions.md#SyntaxError "SyntaxError") ，则它设置额外的属性，使异常打印子系统认为异常是 [`SyntaxError`](../library/exceptions.md#SyntaxError "SyntaxError")。

在 3.4 版本加入.

void PyErr_SyntaxLocationEx(const char *filename, int lineno, int col_offset)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

类似于 `PyErr_SyntaxLocationObject()`，但 _filename_ 是用 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 解码的字节串。

在 3.2 版本加入.

void PyErr_SyntaxLocation(const char *filename, int lineno)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

类似于 `PyErr_SyntaxLocationEx()`，但省略了 _col_offset_ parameter 形参。

void PyErr_BadInternalCall()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是 `PyErr_SetString(PyExc_SystemError, message)` 的缩写，其中 _message_ 表示使用了非法参数调用内部操作（例如，Python/C API 函数）。它主要用于内部使用。

## 发出警告¶

这些函数可以从 C 代码中发出警告。它们仿照了由 Python 模块 [`warnings`](../library/warnings.md#module-warnings "warnings: Issue warning messages and control their disposition.") 导出的那些函数。它们通常向 _sys.stderr_ 打印一条警告信息；当然，用户也有可能已经指定将警告转换为错误，在这种情况下，它们将触发异常。也有可能由于警告机制出现问题，使得函数触发异常。如果没有触发异常，返回值为 `0` ；如果触发异常，返回值为 `-1`。（无法确定是否实际打印了警告信息，也无法确定异常触发的原因。这是故意为之）。如果触发了异常，调用者应该进行正常的异常处理（例如，[`Py_DECREF()`](refcounting.md#c.Py_DECREF "Py_DECREF") 持有引用并返回一个错误值）。

int PyErr_WarnEx([PyObject](structures.md#c.PyObject "PyObject") *category, const char *message, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") stack_level)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

发出一个警告信息。参数 _category_ 是一个警告类别（见下面）或 `NULL` ； _message_ 是一个 UTF-8 编码的字符串。 _stack_level_ 是一个给出栈帧数量的正数；警告将从该栈帧中当前正在执行的代码行发出。 _stack_level_ 为 1 的是调用 `PyErr_WarnEx()` 的函数，2 是在此之上的函数，以此类推。

警告类别必须是 `PyExc_Warning` 的子类， `PyExc_Warning` 是 `PyExc_Exception` 的子类；默认警告类别是 `PyExc_RuntimeWarning` 。标准 Python 警告类别作为全局变量可用，所有其名称见 标准警告类别 。

有关警告控制的信息，参见模块文档 [`warnings`](../library/warnings.md#module-warnings "warnings: Issue warning messages and control their disposition.") 和命令行文档中的 [`-W`](../using/cmdline.md#cmdoption-W) 选项。没有用于警告控制的 C API。

int PyErr_WarnExplicitObject([PyObject](structures.md#c.PyObject "PyObject") *category, [PyObject](structures.md#c.PyObject "PyObject") *message, [PyObject](structures.md#c.PyObject "PyObject") *filename, int lineno, [PyObject](structures.md#c.PyObject "PyObject") *module, [PyObject](structures.md#c.PyObject "PyObject") *registry)¶  
~~~
    

~~~
发出一个对所有警告属性进行显式控制的警告消息。 这是位于 Python 函数 [`warnings.warn_explicit()`](../library/warnings.md#warnings.warn_explicit "warnings.warn_explicit") 外层的直接包装；请查看其文档了解详情。 _module_ 和 _registry_ 参数可被设为 `NULL` 以得到相关文档所描述的默认效果。

在 3.4 版本加入.

int PyErr_WarnExplicit([PyObject](structures.md#c.PyObject "PyObject") *category, const char *message, const char *filename, int lineno, const char *module, [PyObject](structures.md#c.PyObject "PyObject") *registry)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

类似于 `PyErr_WarnExplicitObject()` 不过 _message_ 和 _module_ 是 UTF-8 编码的字符串，而 _filename_ 是由 [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) 解码的。

int PyErr_WarnFormat([PyObject](structures.md#c.PyObject "PyObject") *category, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") stack_level, const char *format, ...)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

类似于 `PyErr_WarnEx()` 的函数，但使用 [`PyUnicode_FromFormat()`](unicode.md#c.PyUnicode_FromFormat "PyUnicode_FromFormat") 来格式化警告消息。 _format_ 是使用 ASCII 编码的字符串。

在 3.2 版本加入.

int PyErr_ResourceWarning([PyObject](structures.md#c.PyObject "PyObject") *source, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") stack_level, const char *format, ...)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.6._

类似于 `PyErr_WarnFormat()` 的函数，但 _category_ 是 [`ResourceWarning`](../library/exceptions.md#ResourceWarning "ResourceWarning") 并且它会将 _source_ 传给 `warnings.WarningMessage()`。

在 3.6 版本加入.

## 查询错误指示器¶

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_Occurred()¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

测试是否设置了错误指示器。 如已设置，则返回异常 _type_ (传给对某个 `PyErr_Set*` 函数或 `PyErr_Restore()` 的最后一次调用的第一个参数)。 如未设置，则返回 `NULL`。 你并不会拥有对返回值的引用，因此你不需要对它执行 [`Py_DECREF()`](refcounting.md#c.Py_DECREF "Py_DECREF")。

呼叫者必须持有GIL。

备注

不要将返回值与特定的异常进行比较；请改为使用 `PyErr_ExceptionMatches()`，如下所示。 （比较很容易失败因为对于类异常来说，异常可能是一个实例而不是类，或者它可能是预期的异常的一个子类。）

int PyErr_ExceptionMatches([PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

等价于 `PyErr_GivenExceptionMatches(PyErr_Occurred(), exc)`。 此函数应当只在实际设置了异常时才被调用；如果没有任何异常被引发则将发生非法内存访问。

int PyErr_GivenExceptionMatches([PyObject](structures.md#c.PyObject "PyObject") *given, [PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果 _given_ 异常与 _exc_ 中的异常类型相匹配则返回真值。 如果 _exc_ 是一个类对象，则当 _given_ 是一个子类的实例时也将返回真值。 如果 _exc_ 是一个元组，则该元组（以及递归的子元组）中的所有异常类型都将被搜索进行匹配。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_GetRaisedException(void)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

返回当前被引发的异常，同时清除错误指示器。

此函数会被需要捕获异常的代码，或需要临时保存和恢复错误指示器的代码所使用。

例如：
~~~
    
    
~~~
{
   PyObject *exc = PyErr_GetRaisedException();

   /* ... code that might produce other errors ... */

   PyErr_SetRaisedException(exc);
}
~~~

参见

`PyErr_GetHandledException()`，保存当前正在处理的异常。

在 3.12 版本加入.

void PyErr_SetRaisedException([PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

将 _exc_ 设为当前被引发的异常，如果已设置则清空现有的异常。

警告

此调用将偷取一个对 _exc_ 的引用，它必须是一个有效的异常。

在 3.12 版本加入.

void PyErr_Fetch([PyObject](structures.md#c.PyObject "PyObject") **ptype, [PyObject](structures.md#c.PyObject "PyObject") **pvalue, [PyObject](structures.md#c.PyObject "PyObject") **ptraceback)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

自 3.12 版本弃用: 使用 `PyErr_GetRaisedException()` 代替。

将错误指示符提取到三个变量中并传递其地址。 如果未设置错误指示符，则将三个变量都设为 `NULL`。 如果已设置，则将其清除并且你将得到对所提取的每个对象的引用。 值和回溯对象可以为 `NULL` 即使类型对象不为空。

备注

此函数通常只被需要捕获异常或临时保存和恢复错误指示符的旧式代码所使用。

例如：

    
    
~~~
{
   PyObject *type, *value, *traceback;
   PyErr_Fetch(&type, &value, &traceback);

   /* ... code that might produce other errors ... */

   PyErr_Restore(type, value, traceback);
}
~~~

void PyErr_Restore([PyObject](structures.md#c.PyObject "PyObject") *type, [PyObject](structures.md#c.PyObject "PyObject") *value, [PyObject](structures.md#c.PyObject "PyObject") *traceback)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

自 3.12 版本弃用: 请改用 `PyErr_SetRaisedException()`。

根据 _type_ , _value_ 和 _traceback_ 这三个对象设置错误指示符，如果已设置了错误指示符则先清除它。如果三个对象均为 `NULL`，则清除错误指示符。 请不要传入 `NULL` 类型和非 `NULL` 的值或回溯。 异常类型应当是一个类。 请不要传入无效的异常类型或值。 （违反这些规则将导致微妙的后继问题。） 此调用会带走对每个对象的引用：你必须在调用之前拥有对每个对象的引用并且在调用之后你将不再拥有这些引用。 （如果你不理解这一点，就不要使用此函数。 勿谓言之不预。）

备注

此函数通常只被需要临时保存和恢复错误指示符的旧代码所使用。 请使用 `PyErr_Fetch()` 来保存当前的错误指示符。

void PyErr_NormalizeException([PyObject](structures.md#c.PyObject "PyObject") **exc, [PyObject](structures.md#c.PyObject "PyObject") **val, [PyObject](structures.md#c.PyObject "PyObject") **tb)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

自 3.12 版本弃用: 请改用 `PyErr_GetRaisedException()`，以避免任何可能的去正规化。

在特定情况下，下面 `PyErr_Fetch()` 所返回的值可以是“非正规化的”，即 `*exc` 是一个类对象而 `*val` 不是同一个类的实例。 在这种情况下此函数可以被用来实例化类。 如果值已经是正规化的，则不做任何操作。 实现这种延迟正规化是为了提升性能。

备注

此函数 _不会_ 显式地在异常值上设置 `__traceback__` 属性。 如果想要适当地设置回溯，还需要以下附加代码片段:

    
    
~~~
if (tb != NULL) {
  PyException_SetTraceback(val, tb);
}
~~~

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_GetHandledException(void)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

提取激活的异常实例，就如 [`sys.exception()`](3.标准库/sys.md#sys.exception "sys.exception") 所返回的一样。 这是指一个 _已被捕获_ 的异常，而不是刚被引发的异常。 返回一个指向该异常的新引用或者 `NULL`。 不会修改解释器的异常状态。 Does not modify the interpreter's exception state.

备注

此函数通常不会被需要处理异常的代码所使用。 它可被使用的场合是当代码需要临时保存并恢复异常状态的时候。 请使用 `PyErr_SetHandledException()` 来恢复或清除异常状态。

在 3.11 版本加入.

void PyErr_SetHandledException([PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

设置激活的异常，就是从 `sys.exception()` 所获得的。 这是指一个 _已被捕获_ 的异常，而不是刚被引发的异常。 要清空异常状态，请传入 `NULL`。

备注

此函数通常不会被需要处理异常的代码所使用。 它被使用的场合是在代码需要临时保存并恢复异常状态的时候。 请使用 `PyErr_GetHandledException()` 来获取异常状态。

在 3.11 版本加入.

void PyErr_GetExcInfo([PyObject](structures.md#c.PyObject "PyObject") **ptype, [PyObject](structures.md#c.PyObject "PyObject") **pvalue, [PyObject](structures.md#c.PyObject "PyObject") **ptraceback)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

提取旧式的异常信息表示形式，就是从 [`sys.exc_info()`](3.标准库/sys.md#sys.exc_info "sys.exc_info") 所获得的。 这是指一个 _已被捕获_ 的异常，而不是刚被引发的异常。 返回分别指向三个对象的新引用，其中任何一个都可以为 `NULL`。 不会修改异常信息的状态。 此函数是为了向下兼容而保留的。 更推荐使用 `PyErr_GetHandledException()`。

备注

此函数通常不会被需要处理异常的代码所使用。 它被使用的场合是在代码需要临时保存并恢复异常状态的时候。 请使用 `PyErr_SetExcInfo()` 来恢复或清除异常状态。

在 3.3 版本加入.

void PyErr_SetExcInfo([PyObject](structures.md#c.PyObject "PyObject") *type, [PyObject](structures.md#c.PyObject "PyObject") *value, [PyObject](structures.md#c.PyObject "PyObject") *traceback)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

设置异常信息，就是从 `sys.exc_info()` 所获得的，这是指一个 _已被捕获_ 的异常，而不是刚被引发的异常。 此函数会偷取对参数的引用。 要清空异常状态，请为所有三个参数传入 `NULL`。 此函数是为了向下兼容而保留的。 更推荐使用 `PyErr_SetHandledException()`。

备注

此函数通常不会被需要处理异常的代码所使用。 它被使用的场合是在代码需要临时保存并恢复异常状态的情况。 请使用 `PyErr_GetExcInfo()` 来读取异常状态。

在 3.3 版本加入.

在 3.11 版本发生变更: `type` 和 `traceback` 参数已不再被使用并且可以为 NULL。 解释器现在会根据异常实例（即 `value` 参数）来推断出它们。 此函数仍然会偷取对所有三个参数的引用。

## 信号处理¶

int PyErr_CheckSignals()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这个函数与Python的信号处理交互。

如果在主 Python 解释器下从主线程调用该函数，它将检查是否向进程发送了信号，如果是，则发起调用相应的信号处理句柄。 如果支持 [`signal`](signal.md#module-signal "signal: Set handlers for asynchronous events.") 模块，则可以发起调用以 Python 编写的信号处理句柄。

该函数会尝试处理所有待处理信号，然后返回 `0`。 但是，如果 Python 信号处理句柄引发了异常，则设置错误指示符并且函数将立即返回 `-1` (这样其他待处理信号可能还没有被处理：它们将在下次发起调用 `PyErr_CheckSignals()` 时被处理)。

如果函数从非主线程调用，或在非主Python解释器下调用，则它不执行任何操作并返回0。

这个函数可以由希望被用户请求(例如按Ctrl-C)中断的长时间运行的C代码调用。

备注

针对 `SIGINT` 的默认 Python 信号处理句柄会引发 [`KeyboardInterrupt`](3.标准库/exceptions.md#KeyboardInterrupt "KeyboardInterrupt") 异常。

void PyErr_SetInterrupt()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

模拟一个 `SIGINT` 信号到达的效果。 这等价于 `PyErr_SetInterruptEx(SIGINT)`。

备注

此函数是异步信号安全的。 它可以不带 [GIL](../glossary.md#term-GIL) 并由 C 信号处理句柄来调用。

int PyErr_SetInterruptEx(int signum)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

模拟一个信号到达的效果。 当下次 `PyErr_CheckSignals()` 被调用时，将会调用针对指定的信号编号的 Python 信号处理句柄。

此函数可由自行设置信号处理，并希望 Python 信号处理句柄会在请求中断时（例如当用户按下 Ctrl-C 来中断操作时）按照预期被发起调用的 C 代码来调用。

如果给定的信号不是由 Python 来处理的 (即被设为 [`signal.SIG_DFL`](signal.md#signal.SIG_DFL "signal.SIG_DFL") 或 [`signal.SIG_IGN`](signal.md#signal.SIG_IGN "signal.SIG_IGN"))，它将会被忽略。

如果 _signum_ 在被允许的信号编号范围之外，将返回 `-1`。 在其他情况下，则返回 `0`。 错误指示符绝不会被此函数所修改。

备注

此函数是异步信号安全的。 它可以不带 [GIL](../glossary.md#term-GIL) 并由 C 信号处理句柄来调用。

在 3.10 版本加入.

int PySignal_SetWakeupFd(int fd)¶  

    

~~~
这个工具函数指定了一个每当收到信号时将被作为以单个字节的形式写入信号编号的目标的文件描述符。 _fd_ 必须是非阻塞的。 它将返回前一个这样的文件描述符。

设置值 `-1` 将禁用该特性；这是初始状态。 这等价于 Python 中的 [`signal.set_wakeup_fd()`](../library/signal.md#signal.set_wakeup_fd "signal.set_wakeup_fd")，但是没有任何错误检查。 _fd_ 应当是一个有效的文件描述符。 此函数应当只从主线程来调用。

在 3.5 版本发生变更: 在 Windows 上，此函数现在也支持套接字处理。

## Exception 类¶

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_NewException(const char *name, [PyObject](structures.md#c.PyObject "PyObject") *base, [PyObject](structures.md#c.PyObject "PyObject") *dict)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

这个工具函数会创建并返回一个新的异常类。 _name_ 参数必须为新异常的名称，是 `module.classname` 形式的 C 字符串。 _base_ 和 _dict_ 参数通常为 `NULL`。 这将创建一个派生自 [`Exception`](../library/exceptions.md#Exception "Exception") 的类对象（在 C 中可以通过 `PyExc_Exception` 访问）。

新类的 `__module__` 属性将被设为 _name_ 参数的前半部分（最后一个点号之前），而类名将被设为后半部分（最后一个点号之后）。 _base_ 参数可被用来指定替代基类；它可以是一个类或是一个由类组成的元组。 _dict_ 参数可被用来指定一个由类变量和方法组成的字典。

[PyObject](structures.md#c.PyObject "PyObject") *PyErr_NewExceptionWithDoc(const char *name, const char *doc, [PyObject](structures.md#c.PyObject "PyObject") *base, [PyObject](structures.md#c.PyObject "PyObject") *dict)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

和 `PyErr_NewException()` 一样，除了可以轻松地给新的异常类一个文档字符串：如果 _doc_ 属性非空，它将用作异常类的文档字符串。

在 3.2 版本加入.

## 异常对象¶

[PyObject](structures.md#c.PyObject "PyObject") *PyException_GetTraceback([PyObject](structures.md#c.PyObject "PyObject") *ex)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

将与异常相关联的回溯作为一个新引用返回，可以通过 `__traceback__` 在 Python 中访问。 如果没有已关联的回溯，则返回 `NULL`。

int PyException_SetTraceback([PyObject](structures.md#c.PyObject "PyObject") *ex, [PyObject](structures.md#c.PyObject "PyObject") *tb)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将异常关联的回溯设置为 _tb_ 。使用 `Py_None` 清除它。

[PyObject](structures.md#c.PyObject "PyObject") *PyException_GetContext([PyObject](structures.md#c.PyObject "PyObject") *ex)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

将与异常相关联的上下文（在处理 _ex_ 的过程中引发的另一个异常实例）作为一个新引用返回，可以通过 `__context__` 在 Python 中访问。 如果没有已关联的上下文，则返回 `NULL`。

void PyException_SetContext([PyObject](structures.md#c.PyObject "PyObject") *ex, [PyObject](structures.md#c.PyObject "PyObject") *ctx)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将与异常相关联的上下文设置为 _ctx_ 。 使用 `NULL` 来清空它。 没有用来确保 _ctx_ 是一个异常实例的类型检查。 这将窃取一个指向 _ctx_ 的引用。

[PyObject](structures.md#c.PyObject "PyObject") *PyException_GetCause([PyObject](structures.md#c.PyObject "PyObject") *ex)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

将与异常相关联的原因（一个异常实例，或为 `None`，由 `raise ... from ...` 设置）作为一个新引用返回，可在从 Python 通过 `__cause__` 来访问。

void PyException_SetCause([PyObject](structures.md#c.PyObject "PyObject") *ex, [PyObject](structures.md#c.PyObject "PyObject") *cause)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将与异常相关联的原因设为 _cause_ 。 使用 `NULL` 来清空它。 不存在类型检查用来确保 _cause_ 是一个异常实例或为 `None`。 这个偷取一个指向 _cause_ 的引用。

`__suppress_context__` 会被此函数隐式地设为 `True`。

[PyObject](structures.md#c.PyObject "PyObject") *PyException_GetArgs([PyObject](structures.md#c.PyObject "PyObject") *ex)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

返回异常 _ex_ 的 [`args`](../library/exceptions.md#BaseException.args "BaseException.args")。

void PyException_SetArgs([PyObject](structures.md#c.PyObject "PyObject") *ex, [PyObject](structures.md#c.PyObject "PyObject") *args)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

将异常 _ex_ 的 [`args`](../library/exceptions.md#BaseException.args "BaseException.args") 设为 _args_ 。

[PyObject](structures.md#c.PyObject "PyObject") *PyUnstable_Exc_PrepReraiseStar([PyObject](structures.md#c.PyObject "PyObject") *orig, [PyObject](structures.md#c.PyObject "PyObject") *excs)¶  
~~~
    

~~~
