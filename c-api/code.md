# 代码对象¶

代码对象是 CPython 实现的低层级细节。 每个代表一块尚未绑定到函数中的可执行代码。

type PyCodeObject¶  

    

~~~
用于描述代码对象的对象的 C 结构。 此类型字段可随时更改。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyCode_Type¶  
~~~
    

~~~
这是一个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 实例，其表示Python的 [`code`](../library/code.md#module-code "code: Facilities to implement read-eval-print loops.") 类型。

int PyCode_Check([PyObject](structures.md#c.PyObject "PyObject") *co)¶  
~~~
    

~~~
如果 _co_ 是一个 [`code`](../library/code.md#module-code "code: Facilities to implement read-eval-print loops.") 对象则返回真值。 此函数总是会成功执行。

int PyCode_GetNumFree(PyCodeObject *co)¶  
~~~
    

~~~
返回 _co_ 中的自由变量数。

PyCodeObject *PyUnstable_Code_New(int argcount, int kwonlyargcount, int nlocals, int stacksize, int flags, [PyObject](structures.md#c.PyObject "PyObject") *code, [PyObject](structures.md#c.PyObject "PyObject") *consts, [PyObject](structures.md#c.PyObject "PyObject") *names, [PyObject](structures.md#c.PyObject "PyObject") *varnames, [PyObject](structures.md#c.PyObject "PyObject") *freevars, [PyObject](structures.md#c.PyObject "PyObject") *cellvars, [PyObject](structures.md#c.PyObject "PyObject") *filename, [PyObject](structures.md#c.PyObject "PyObject") *name, [PyObject](structures.md#c.PyObject "PyObject") *qualname, int firstlineno, [PyObject](structures.md#c.PyObject "PyObject") *linetable, [PyObject](structures.md#c.PyObject "PyObject") *exceptiontable)¶  
~~~
    

~~~
_This is[ Unstable API](stable.md#unstable-c-api). It may change without warning in minor releases._

返回一个新的代码对象。 如果你需要一个用空代码对象来创建帧，请改用 `PyCode_NewEmpty()`。

由于字节码的定义经常变化，可以直接调用 `PyUnstable_Code_New()` 来绑定某个确定的 Python 版本。

此函数的许多参数以复杂的方式相互依赖，这意味着参数值的细微改变可能导致不正确的执行或 VM 崩溃。 使用此函数需要极度小心。

在 3.11 版本发生变更: 添加了 `qualname` 和 `exceptiontable` 形参。

在 3.12 版本发生变更: 由 `PyCode_New` 更名而来，是 [不稳定 C API](stable.md#unstable-c-api) 的一部分。 旧名称已被弃用，但在签名再次更改之前仍然可用。

PyCodeObject *PyUnstable_Code_NewWithPosOnlyArgs(int argcount, int posonlyargcount, int kwonlyargcount, int nlocals, int stacksize, int flags, [PyObject](structures.md#c.PyObject "PyObject") *code, [PyObject](structures.md#c.PyObject "PyObject") *consts, [PyObject](structures.md#c.PyObject "PyObject") *names, [PyObject](structures.md#c.PyObject "PyObject") *varnames, [PyObject](structures.md#c.PyObject "PyObject") *freevars, [PyObject](structures.md#c.PyObject "PyObject") *cellvars, [PyObject](structures.md#c.PyObject "PyObject") *filename, [PyObject](structures.md#c.PyObject "PyObject") *name, [PyObject](structures.md#c.PyObject "PyObject") *qualname, int firstlineno, [PyObject](structures.md#c.PyObject "PyObject") *linetable, [PyObject](structures.md#c.PyObject "PyObject") *exceptiontable)¶  
~~~
    

~~~
_This is[ Unstable API](stable.md#unstable-c-api). It may change without warning in minor releases._

与 `PyUnstable_Code_New()` 类似，但额外增加了一个针对仅限位置参数的 "posonlyargcount"。 适用于 `PyUnstable_Code_New` 的适用事项同样适用于这个函数。

在 3.8 版本加入: 作为 `PyCode_NewWithPosOnlyArgs`

在 3.11 版本发生变更: 增加了 `qualname` 和 `exceptiontable` 形参。

在 3.12 版本发生变更: 重命名为 `PyUnstable_Code_NewWithPosOnlyArgs`。 旧名称已被弃用，但在签名再次更改之前将保持可用。

PyCodeObject *PyCode_NewEmpty(const char *filename, const char *funcname, int firstlineno)¶  

    _返回值：新的引用。_

返回一个具有指定用户名、函数名和首行行号的空代码对象。 结果代码对象如果被执行则将引发一个 `Exception`。

int PyCode_Addr2Line(PyCodeObject *co, int byte_offset)¶  
~~~
    

~~~
返回在 `byte_offset` 位置或之前以及之后发生的指令的行号。 如果你只需要一个帧的行号，请改用 [`PyFrame_GetLineNumber()`](frame.md#c.PyFrame_GetLineNumber "PyFrame_GetLineNumber")。

要高效地对代码对象中的行号进行迭代，请使用 [在 PEP 626 中描述的 API](https://peps.python.org/pep-0626/#out-of-process-debuggers-and-profilers)。

int PyCode_Addr2Location([PyObject](structures.md#c.PyObject "PyObject") *co, int byte_offset, int *start_line, int *start_column, int *end_line, int *end_column)¶  
~~~
    

~~~
将传入的 `int` 指针设为 `byte_offset` 处的指令的源代码行编号和列编号。 当没有任何特定元素的信息时则将值设为 `0`。

如果函数执行成功则返回 `1` 否则返回 0。

在 3.11 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyCode_GetCode(PyCodeObject *co)¶  
~~~
    

~~~
等价于 Python 代码 `getattr(co, 'co_code')`。 返回一个指向表示代码对象中的字节码的 [`PyBytesObject`](bytes.md#c.PyBytesObject "PyBytesObject") 的强引用。 当出错时，将返回 `NULL` 并引发一个异常。

这个 `PyBytesObject` 可以由解释器按需创建并且不必代表 CPython 所实际执行的字节码。 此函数的主要用途是调试器和性能分析工具。

在 3.11 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyCode_GetVarnames(PyCodeObject *co)¶  
~~~
    

~~~
等价于 Python 代码 `getattr(co, 'co_varnames')`。 返回一个指向包含局部变量名称的 [`PyTupleObject`](tuple.md#c.PyTupleObject "PyTupleObject") 的新引用。 当出错时，将返回 `NULL` 并引发一个异常。

在 3.11 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyCode_GetCellvars(PyCodeObject *co)¶  
~~~
    

~~~
等价于 Python 代码 `getattr(co, 'co_cellvars')`。 返回一个包含被嵌套的函数所引用的局部变量名称的 [`PyTupleObject`](tuple.md#c.PyTupleObject "PyTupleObject") 的新引用。 当出错时，将返回 `NULL` 并引发一个异常。

在 3.11 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyCode_GetFreevars(PyCodeObject *co)¶  
~~~
    

~~~
等价于 Python 代码 `getattr(co, 'co_freevars')`。 返回一个指向包含自由变量名称的 [`PyTupleObject`](tuple.md#c.PyTupleObject "PyTupleObject") 的新引用。 当出错时，将返回 `NULL` 并引发一个异常。

在 3.11 版本加入.

int PyCode_AddWatcher(PyCode_WatchCallback callback)¶  
~~~
    

~~~
注册 _callback_ 作为当前解释器的代码对象监视器。 返回一个可被传给 `PyCode_ClearWatcher()` 的 ID。 如果出现错误（例如没有足够的可用监视器 ID），则返回 `-1` 并设置一个异常。

在 3.12 版本加入.

int PyCode_ClearWatcher(int watcher_id)¶  
~~~
    

~~~
清除之前从 `PyCode_AddWatcher()` 返回的当前解释器中由 _watcher_id_ 所标识的监视器。 成功时返回 `0`，或者出错时（例如当给定的 _watcher_id_ 未被注册）返回 `-1` 并设置异常。

在 3.12 版本加入.

type PyCodeEvent¶  
~~~
    

~~~
由可能的代码对象监视器事件组成的枚举: - `PY_CODE_EVENT_CREATE` \- `PY_CODE_EVENT_DESTROY`

在 3.12 版本加入.

typedef int (*PyCode_WatchCallback)(PyCodeEvent event, PyCodeObject *co)¶  
~~~
    

~~~
代码对象监视器回调函数的类型。

如果 _event_ 为 `PY_CODE_EVENT_CREATE` ，则回调会在 co 完全初始化后被发起调用。 否则，回调会在 _co_ 执行销毁之前被发起调用，这样就可以检查 _co_ 之前的状态。

如果 _event_ 为 `PY_CODE_EVENT_DESTROY`，则在回调中接受一个即将被销毁的代码对象的引用将使其重生，并阻止其在此时被释放。 当重生的对象以后再被销毁时，任何在当时已激活的监视器回调将再次被调用。

本 API 的用户不应依赖内部运行时的实现细节。 这类细节可能包括但不限于创建和销毁代码对象的确切顺序和时间。 虽然这些细节的变化可能会导致监视器可观察到的差异（包括回调是否被发起调用），但不会改变正在执行的 Python 代码的语义。

如果该回调设置了一个异常，则它必须返回 `-1`；此异常将作为不可引发的异常使用 [`PyErr_WriteUnraisable()`](exceptions.md#c.PyErr_WriteUnraisable "PyErr_WriteUnraisable") 打印出来。 在其他情况下它应当返回 `0`。

在进入回调时可能已经设置了尚未处理的异常。 在此情况下，回调应当返回 `0` 并仍然设置同样的异常。 这意味着该回调可能不会调用任何其他可设置异常的 API 除非它先保存并清空异常状态，并在返回之前恢复它。

在 3.12 版本加入.

# 附加信息¶

为了支持对帧求值的低层级扩展，如外部即时编译器等，可以在代码对象上附加任意的额外数据。

这些函数是不稳定 C API 层的一部分：该功能是 CPython 的实现细节，此 API 可能随时改变而不发出弃用警告。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyUnstable_Eval_RequestCodeExtraIndex([freefunc](typeobj.md#c.freefunc "freefunc") free)¶  
~~~
    

~~~
_This is[ Unstable API](stable.md#unstable-c-api). It may change without warning in minor releases._

返回一个新的不透明索引值用于向代码对象添加数据。

通常情况下（对于每个解释器）你只需调用该函数一次然后将调用结果与 `PyCode_GetExtra` 和 `PyCode_SetExtra` 一起使用以操作单个代码对象上的数据。

如果 _free_ 没有不为 `NULL`: 当代码对象被释放时， _free_ 将在存储于新索引下的非 `NULL` 数据上被调用。 当存储 [`PyObject`](structures.md#c.PyObject "PyObject") 时使用 [`Py_DecRef()`](refcounting.md#c.Py_DecRef "Py_DecRef")。

在 3.6 版本加入: 作为 `_PyEval_RequestCodeExtraIndex`

在 3.12 版本发生变更: 重命名为 `PyUnstable_Eval_RequestCodeExtraIndex`。 旧的私有名称已被弃用，但在 API 更改之前仍将可用。

int PyUnstable_Code_GetExtra([PyObject](structures.md#c.PyObject "PyObject") *code, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") index, void **extra)¶  
~~~
    

~~~
_This is[ Unstable API](stable.md#unstable-c-api). It may change without warning in minor releases._

将 _extra_ 设为存储在给定索引下的额外数据。 成功时将返回 0。 失败时将设置一个异常并返回 -1。

如果未在索引下设置数据，则将 _extra_ 设为 `NULL` 并返回 0 而不设置异常。

在 3.6 版本加入: 作为 `_PyCode_GetExtra`

在 3.12 版本发生变更: 重命名为 `PyUnstable_Code_GetExtra`。 旧的私有名称已被弃用，但在 API 更改之前仍将可用。

int PyUnstable_Code_SetExtra([PyObject](structures.md#c.PyObject "PyObject") *code, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") index, void *extra)¶  
~~~
    

~~~
