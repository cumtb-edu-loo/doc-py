# 调用协议¶

CPython 支持两种不同的调用协议： _tp_call_ 和矢量调用。

## _tp_call_ 协议¶

设置 [`tp_call`](typeobj.md#c.PyTypeObject.tp_call "PyTypeObject.tp_call") 的类的实例都是可调用的。 槽位的签名为:

    
    
~~~
PyObject *tp_call(PyObject *callable, PyObject *args, PyObject *kwargs);
~~~

一个调用是用一个元组表示位置参数，用一个dict表示关键字参数，类似于Python代码中的 `callable(*args, **kwargs)`。 _args*必须是非空的（如果没有参数，会使用一个空元组），但如果没有关键字参数，*kwargs*可以是*NULL_ 。

这个约定不仅被*tp_call*使用： [`tp_new`](typeobj.md#c.PyTypeObject.tp_new "PyTypeObject.tp_new") 和 [`tp_init`](typeobj.md#c.PyTypeObject.tp_init "PyTypeObject.tp_init") 也这样传递参数。

要调用一个对象，请使用 `PyObject_Call()` 或者其他的 调用 API。

## Vectorcall 协议¶

在 3.9 版本加入.

vectorcall 协议是在 [**PEP 590**](https://peps.python.org/pep-0590/) 被引入的，它是使调用函数更加有效的附加协议。

作为经验法则，如果可调用程序支持 vectorcall，CPython 会更倾向于内联调用。 然而，这并不是一个硬性规定。 此外，一些第三方扩展直接使用 _tp_call_ (而不是使用 `PyObject_Call()`)。 因此，一个支持 vectorcall 的类也必须实现 [`tp_call`](typeobj.md#c.PyTypeObject.tp_call "PyTypeObject.tp_call")。 此外，无论使用哪种协议，可调对象的行为都必须是相同的。 推荐的方法是将 [`tp_call`](typeobj.md#c.PyTypeObject.tp_call "PyTypeObject.tp_call") 设置为 `PyVectorcall_Call()`。值得一提的是:

警告

一个支持 Vectorcall 的类 **必须** 也实现具有相同语义的 [`tp_call`](typeobj.md#c.PyTypeObject.tp_call "PyTypeObject.tp_call")。

在 3.12 版本发生变更: 现在 [`Py_TPFLAGS_HAVE_VECTORCALL`](typeobj.md#c.Py_TPFLAGS_HAVE_VECTORCALL "Py_TPFLAGS_HAVE_VECTORCALL") 旗标在类的 [`__call__()`](3.%20数据模型.md#object.__call__ "object.__call__") 方法被重新赋值时将会从类中移除。 （这将仅在内部设置 [`tp_call`](typeobj.md#c.PyTypeObject.tp_call "PyTypeObject.tp_call")，因此可能使其行为不同于 vectorcall 函数。） 在更早的 Python 版本中，vectorcall 应当仅被用于 [`不可变对象`](typeobj.md#c.Py_TPFLAGS_IMMUTABLETYPE "Py_TPFLAGS_IMMUTABLETYPE") 或静态类型。

如果一个类的vectorcall比*tp_call*慢，就不应该实现vectorcall。例如，如果被调用者需要将参数转换为args 元组和kwargs dict，那么实现vectorcall就没有意义。

类可以通过启用 [`Py_TPFLAGS_HAVE_VECTORCALL`](typeobj.md#c.Py_TPFLAGS_HAVE_VECTORCALL "Py_TPFLAGS_HAVE_VECTORCALL") 旗标并将 [`tp_vectorcall_offset`](typeobj.md#c.PyTypeObject.tp_vectorcall_offset "PyTypeObject.tp_vectorcall_offset") 设为对象结构体中 _vectorcallfunc_ 出现位置偏移量来实现 vectorcall 协议。 这是一个指向具有以下签名的函数的指针:

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*vectorcallfunc)([PyObject](structures.md#c.PyObject "PyObject") *callable, [PyObject](structures.md#c.PyObject "PyObject") *const *args, size_t nargsf, [PyObject](structures.md#c.PyObject "PyObject") *kwnames)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

  * _callable_ 是指被调用的对象。

  * _args_ 是一个C语言数组，由位置参数和后面的
    

关键字参数的值。如果没有参数，这个值可以是 _NULL_ 。

  * _nargsf_ 是位置参数的数量加上可能的
    

`PY_VECTORCALL_ARGUMENTS_OFFSET` 旗标。 要从 _nargsf_ 获得位置参数的实际数量，请使用 `PyVectorcall_NARGS()`。

  * _kwnames_ 是一包含所有关键字名称的元组。
    

换句话说，就是 kwargs 字典的键。 这些名字必须是字符串 (`str` 或其子类的实例)，并且它们必须是唯一的。 如果没有关键字参数，那么 _kwnames_ 可以用 _NULL_ 代替。

PY_VECTORCALL_ARGUMENTS_OFFSET¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

如果在 vectorcall 的 _nargsf_ 参数中设置了此标志，则允许被调用者临时更改 `args[-1]` 的值。换句话说， _args_ 指向分配向量中的参数 1（不是 0 ）。被调用方必须在返回之前还原 `args[-1]` 的值。

对于 `PyObject_VectorcallMethod()` ，这个标志的改变意味着 `args[0]` 可能改变了。

只要调用方能以低代价（不额外分配内存）这样做，就推荐使用 `PY_VECTORCALL_ARGUMENTS_OFFSET`。 这样做将允许诸如绑定方法之类的可调用对象非常高效地执行前向调用（这种调用将包括一个加在开头的 _self_ 参数）。

要调用一个实现了 vectorcall 的对象，请使用某个 call API 函数，就像其他可调对象一样。 `PyObject_Vectorcall()` 通常是最有效的。

### 递归控制¶

在使用 _tp_call_ 时，被调用者不必担心 [递归](10.C%20API接口/exceptions.md#recursion): CPython 对于使用 _tp_call_ 进行的调用会使用 [`Py_EnterRecursiveCall()`](10.C%20API接口/exceptions.md#c.Py_EnterRecursiveCall "Py_EnterRecursiveCall") 和 [`Py_LeaveRecursiveCall()`](10.C%20API接口/exceptions.md#c.Py_LeaveRecursiveCall "Py_LeaveRecursiveCall")。

为保证效率，这不适用于使用 vectorcall 的调用：被调用方在需要时应当使用 _Py_EnterRecursiveCall_ 和 _Py_LeaveRecursiveCall_ 。

### Vectorcall 支持 API¶

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") PyVectorcall_NARGS(size_t nargsf)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

给定一个 vectorcall _nargsf_ 实参，返回参数的实际数量。 目前等同于:

    
    
~~~
(Py_ssize_t)(nargsf & ~PY_VECTORCALL_ARGUMENTS_OFFSET)
~~~

然而，应使用 `PyVectorcall_NARGS` 函数以便将来扩展。

在 3.8 版本加入.

vectorcallfunc PyVectorcall_Function([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    

~~~
如果*op*不支持vectorcall协议（要么是因为类型不支持，要么是因为具体实例不支持），返回*NULL*。否则，返回存储在*op*中的vectorcall函数指针。这个函数从不触发异常。

这在检查 _op_ 是否支持 vectorcall 时最有用处，可以通过检查 `PyVectorcall_Function(op) != NULL` 来实现。

在 3.9 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyVectorcall_Call([PyObject](structures.md#c.PyObject "PyObject") *callable, [PyObject](structures.md#c.PyObject "PyObject") *tuple, [PyObject](structures.md#c.PyObject "PyObject") *dict)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

调用*可调对象*的 `vectorcallfunc`，其位置参数和关键字参数分别以元组和dict形式给出。

这是一个专用函数，用于放入 [`tp_call`](typeobj.md#c.PyTypeObject.tp_call "PyTypeObject.tp_call") 槽位或是用于 `tp_call` 的实现。 它不会检查 [`Py_TPFLAGS_HAVE_VECTORCALL`](typeobj.md#c.Py_TPFLAGS_HAVE_VECTORCALL "Py_TPFLAGS_HAVE_VECTORCALL") 旗标并且它也不会回退到 `tp_call`。

在 3.8 版本加入.

## 调用对象的 API¶

有多个函数可被用来调用 Python 对象。 各个函数会将其参数转换为被调用对象所支持的惯例 – 可以是 _tp_call_ 或 vectorcall。 为了尽可能少地进行转换，请选择一个适合你所拥有的数据格式的函数。

下表总结了可用的功能； 请参阅各个文档以了解详细信息。

函数

|

可调用对象（Callable）

|

args

|

kwargs  
  
---|---|---|---  
  
`PyObject_Call()`

|

`PyObject *`

|

元组

|

dict/`NULL`  
  
`PyObject_CallNoArgs()`

|

`PyObject *`

|

\---

|

\---  
  
`PyObject_CallOneArg()`

|

`PyObject *`

|

1个对象

|

\---  
  
`PyObject_CallObject()`

|

`PyObject *`

|

元组/`NULL`

|

\---  
  
`PyObject_CallFunction()`

|

`PyObject *`

|

format

|

\---  
  
`PyObject_CallMethod()`

|

对象 + `char*`

|

format

|

\---  
  
`PyObject_CallFunctionObjArgs()`

|

`PyObject *`

|

可变参数

|

\---  
  
`PyObject_CallMethodObjArgs()`

|

对象 + 名称

|

可变参数

|

\---  
  
`PyObject_CallMethodNoArgs()`

|

对象 + 名称

|

\---

|

\---  
  
`PyObject_CallMethodOneArg()`

|

对象 + 名称

|

1个对象

|

\---  
  
`PyObject_Vectorcall()`

|

`PyObject *`

|

vectorcall

|

vectorcall  
  
`PyObject_VectorcallDict()`

|

`PyObject *`

|

vectorcall

|

dict/`NULL`  
  
`PyObject_VectorcallMethod()`

|

参数 + 名称

|

vectorcall

|

vectorcall  
  
[PyObject](structures.md#c.PyObject "PyObject") *PyObject_Call([PyObject](structures.md#c.PyObject "PyObject") *callable, [PyObject](structures.md#c.PyObject "PyObject") *args, [PyObject](structures.md#c.PyObject "PyObject") *kwargs)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

调用一个可调用的 Python 对象 _callable_ ，附带由元组 _args_ 所给出的参数，以及由字典 _kwargs_ 所给出的关键字参数。

_args_ 必须不为 _NULL_ ；如果不想要参数请使用一个空元组。 如果不想要关键字参数，则 _kwargs_ 可以为 _NULL_ 。

成功时返回结果，在失败时抛出一个异常并返回 _NULL_ 。

这等价于 Python 表达式 `callable(*args, **kwargs)`。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_CallNoArgs([PyObject](structures.md#c.PyObject "PyObject") *callable)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

调用一个可调用的 Python 对象 _callable_ 并不附带任何参数。 这是不带参数调用 Python 可调用对象的最有效方式。

成功时返回结果，在失败时抛出一个异常并返回 _NULL_ 。

在 3.9 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_CallOneArg([PyObject](structures.md#c.PyObject "PyObject") *callable, [PyObject](structures.md#c.PyObject "PyObject") *arg)¶  
~~~
    

~~~
调用一个可调用的 Python 对象 _callable_ 并附带恰好 1 个位置参数 _arg_ 而没有关键字参数。

成功时返回结果，在失败时抛出一个异常并返回 _NULL_ 。

在 3.9 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_CallObject([PyObject](structures.md#c.PyObject "PyObject") *callable, [PyObject](structures.md#c.PyObject "PyObject") *args)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

调用一个可调用的 Python 对象 _callable_ ，附带由元组 _args_ 所给出的参数。 如果不想要传入参数，则 _args_ 可以为 _NULL_ 。

成功时返回结果，在失败时抛出一个异常并返回 _NULL_ 。

这等价于 Python 表达式 `callable(*args)`。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_CallFunction([PyObject](structures.md#c.PyObject "PyObject") *callable, const char *format, ...)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

调用一个可调用的 Python 对象 _callable_ ，附带可变数量的 C 参数。 这些 C 参数使用 [`Py_BuildValue()`](arg.md#c.Py_BuildValue "Py_BuildValue") 风格的格式字符串来描述。 format 可以为 _NULL_ ，表示没有提供任何参数。

成功时返回结果，在失败时抛出一个异常并返回 _NULL_ 。

这等价于 Python 表达式 `callable(*args)`。

请注意如果你只传入 [PyObject](structures.md#c.PyObject "PyObject")* 参数，则 `PyObject_CallFunctionObjArgs()` 是更快速的选择。

在 3.4 版本发生变更: 这个 _format_ 类型已从 `char *` 更改。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_CallMethod([PyObject](structures.md#c.PyObject "PyObject") *obj, const char *name, const char *format, ...)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

调用 _obj_ 对象中名为 _name_ 的方法并附带可变数量的 C 参数。 这些 C 参数由 [`Py_BuildValue()`](arg.md#c.Py_BuildValue "Py_BuildValue") 格式字符串来描述并应当生成一个元组。

格式可以为 _NULL_ ，表示未提供任何参数。

成功时返回结果，在失败时抛出一个异常并返回 _NULL_ 。

这和Python表达式 `obj.name(arg1, arg2, ...)` 是一样的。

请注意如果你只传入 [PyObject](structures.md#c.PyObject "PyObject")* 参数，则 `PyObject_CallMethodObjArgs()` 是更快速的选择。

在 3.4 版本发生变更: The types of _name_ and _format_ were changed from `char *`.

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_CallFunctionObjArgs([PyObject](structures.md#c.PyObject "PyObject") *callable, ...)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

调用一个 Python 可调用对象 _callable_ ，附带可变数量的 [PyObject](structures.md#c.PyObject "PyObject")* 参数。 这些参数是以 _NULL_ 之后可变数量的形参的形式提供的。

成功时返回结果，在失败时抛出一个异常并返回 _NULL_ 。

这和Python表达式 `callable(arg1, arg2, ...)` 是一样的。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_CallMethodObjArgs([PyObject](structures.md#c.PyObject "PyObject") *obj, [PyObject](structures.md#c.PyObject "PyObject") *name, ...)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

调用 Python 对象 _obj_ 中的一个访问，其中方法名称由 _name_ 中的 Python 字符串对象给出。 它将附带可变数量的 [PyObject](structures.md#c.PyObject "PyObject")* 参数被调用。 这些参数是以 _NULL_ 之后可变数量的形参的形式提供的。

成功时返回结果，在失败时抛出一个异常并返回 _NULL_ 。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_CallMethodNoArgs([PyObject](structures.md#c.PyObject "PyObject") *obj, [PyObject](structures.md#c.PyObject "PyObject") *name)¶  
~~~
    

~~~
调用 Python 对象 _obj_ 中的一个方法并不附带任何参数，其中方法名称由 _name_ 中的 Python 字符串对象给出。

成功时返回结果，在失败时抛出一个异常并返回 _NULL_ 。

在 3.9 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_CallMethodOneArg([PyObject](structures.md#c.PyObject "PyObject") *obj, [PyObject](structures.md#c.PyObject "PyObject") *name, [PyObject](structures.md#c.PyObject "PyObject") *arg)¶  
~~~
    

~~~
调用 Python 对象 _obj_ 中的一个方法并附带单个位置参数 _arg_ ，其中方法名称由 _name_ 中的 Python 字符串对象给出。

成功时返回结果，在失败时抛出一个异常并返回 _NULL_ 。

在 3.9 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_Vectorcall([PyObject](structures.md#c.PyObject "PyObject") *callable, [PyObject](structures.md#c.PyObject "PyObject") *const *args, size_t nargsf, [PyObject](structures.md#c.PyObject "PyObject") *kwnames)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

调用一个可调用的 Python 对象 _callable_ 。 附带的参数与 `vectorcallfunc` 相同。 如果 _callable_ 支持 vectorcall，则它会直接调用存放在 _callable_ 中的 vectorcall 函数。

成功时返回结果，在失败时抛出一个异常并返回 _NULL_ 。

在 3.9 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_VectorcallDict([PyObject](structures.md#c.PyObject "PyObject") *callable, [PyObject](structures.md#c.PyObject "PyObject") *const *args, size_t nargsf, [PyObject](structures.md#c.PyObject "PyObject") *kwdict)¶  
~~~
    

~~~
