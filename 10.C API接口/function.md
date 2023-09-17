# Function 对象¶

有一些特定于 Python 函数的函数。

type PyFunctionObject¶  

    

~~~
用于函数的 C 结构体。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyFunction_Type¶  
~~~
    

~~~
这是一个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 实例并表示 Python 函数类型。 它作为 `types.FunctionType` 向 Python 程序员公开。

int PyFunction_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果 _o_ 是一个函数对象 (类型为 `PyFunction_Type`) 则返回真值。 形参必须不为 `NULL`。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PyFunction_New([PyObject](structures.md#c.PyObject "PyObject") *code, [PyObject](structures.md#c.PyObject "PyObject") *globals)¶  

    _返回值：新的引用。_

返回与代码对象 _code_ 关联的新函数对象。 _globals_ 必须是一个字典，该函数可以访问全局变量。

函数的文档字符串和名称是从代码对象中提取的。 ___module___ 是从 _globals_ 中提取的。 参数 defaults, annotations 和 closure 被设为 `NULL`。 ___qualname___ 被设为与代码对象的 `co_qualname` 字段相同的值。

[PyObject](structures.md#c.PyObject "PyObject") *PyFunction_NewWithQualName([PyObject](structures.md#c.PyObject "PyObject") *code, [PyObject](structures.md#c.PyObject "PyObject") *globals, [PyObject](structures.md#c.PyObject "PyObject") *qualname)¶  

    _返回值：新的引用。_

类似 `PyFunction_New()`，但还允许设置函数对象的 `__qualname__` 属性。 _qualname_ 应当是一个 unicode 对象或 `NULL`；如果是 `NULL`，则 `__qualname__` 属性会被设为与代码对象的 `co_qualname` 字段相同的值。

在 3.3 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyFunction_GetCode([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    _返回值：借入的引用。_

返回与函数对象 _op_ 关联的代码对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyFunction_GetGlobals([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    _返回值：借入的引用。_

返回与函数对象*op*相关联的全局字典。

[PyObject](structures.md#c.PyObject "PyObject") *PyFunction_GetModule([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    _返回值：借入的引用。_

向函数对象 _op_ 的 ___module___ 属性返回一个 [borrowed reference](../glossary.md#term-borrowed-reference)。 该值可以为 _NULL_ 。

这通常为一个包含模块名称的字符串，但可以通过 Python 代码设为任何其他对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyFunction_GetDefaults([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    _返回值：借入的引用。_

返回函数对象 _op_ 的参数默认值。 这可以是一个参数元组或 `NULL`。

int PyFunction_SetDefaults([PyObject](structures.md#c.PyObject "PyObject") *op, [PyObject](structures.md#c.PyObject "PyObject") *defaults)¶  
~~~
    

~~~
为函数对象 _op_ 设置参数默认值。 _defaults_ 必须为 `Py_None` 或一个元组。

失败时引发 [`SystemError`](../library/exceptions.md#SystemError "SystemError") 异常并返回 `-1` 。

void PyFunction_SetVectorcall(PyFunctionObject *func, [vectorcallfunc](call.md#c.vectorcallfunc "vectorcallfunc") vectorcall)¶  
~~~
    

~~~
设置给定函数对象 _func_ 的 vectorcall 字段。

警告：使用此 API 的扩展必须保留未修改的（默认） vectorcall 函数的行为！

在 3.12 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyFunction_GetClosure([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    _返回值：借入的引用。_

返回关联到函数对象 _op_ 的闭包。 这可以是 `NULL` 或 cell 对象的元组。

int PyFunction_SetClosure([PyObject](structures.md#c.PyObject "PyObject") *op, [PyObject](structures.md#c.PyObject "PyObject") *closure)¶  
~~~
    

~~~
设置关联到函数对象 _op_ 的闭包。 _closure_ 必须为 `Py_None` 或 cell 对象的元组。

失败时引发 [`SystemError`](../library/exceptions.md#SystemError "SystemError") 异常并返回 `-1` 。

[PyObject](structures.md#c.PyObject "PyObject") *PyFunction_GetAnnotations([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    _返回值：借入的引用。_

返回函数对象 _op_ 的标注。 这可以是一个可变字典或 `NULL`。

int PyFunction_SetAnnotations([PyObject](structures.md#c.PyObject "PyObject") *op, [PyObject](structures.md#c.PyObject "PyObject") *annotations)¶  
~~~
    

~~~
设置函数对象 _op_ 的标注。 _annotations_ 必须为一个字典或 `Py_None`。

失败时引发 [`SystemError`](../library/exceptions.md#SystemError "SystemError") 异常并返回 `-1` 。

int PyFunction_AddWatcher(PyFunction_WatchCallback callback)¶  
~~~
    

~~~
注册 _callback_ 作为当前解释器的函数监视器。 返回一个可被传给 `PyFunction_ClearWatcher()` 的 ID。 如果出现错误（比如没有足够的可用监视器 ID），则返回 `-1` 并设置一个异常。

在 3.12 版本加入.

int PyFunction_ClearWatcher(int watcher_id)¶  
~~~
    

~~~
清空当前解释器在之前从Clear watcher identified by previously returned from `PyFunction_AddWatcher()` 返回的由 _watcher_id_ 所标识的监视器。 成功时返回 `0`，或者出错时（比如当给定的 _watcher_id_ 未被注册）返回 `-1` 并设置一个异常。

在 3.12 版本加入.

type PyFunction_WatchEvent¶  
~~~
    

~~~
由以下可能的函数监视器事件组成的枚举: - `PyFunction_EVENT_CREATE` \- `PyFunction_EVENT_DESTROY` \- `PyFunction_EVENT_MODIFY_CODE` \- `PyFunction_EVENT_MODIFY_DEFAULTS` \- `PyFunction_EVENT_MODIFY_KWDEFAULTS`

在 3.12 版本加入.

typedef int (*PyFunction_WatchCallback)(PyFunction_WatchEvent event, PyFunctionObject *func, [PyObject](structures.md#c.PyObject "PyObject") *new_value)¶  
~~~
    

~~~
