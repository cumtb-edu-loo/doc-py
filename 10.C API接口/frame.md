# 帧对象¶

type PyFrameObject¶  

    _Part of the[ Limited API](stable.md#stable) (as an opaque struct)._

用于描述帧对象的对象C结构体。

此结构体中无公有成员。

在 3.11 版本发生变更: 此结构体的成员已从公有 C API 中移除。 请参阅 [What's New entry](3.11.md#pyframeobject-3-11-hiding) 了解详情。

可以使用函数 [`PyEval_GetFrame()`](reflection.md#c.PyEval_GetFrame "PyEval_GetFrame") 与 [`PyThreadState_GetFrame()`](init.md#c.PyThreadState_GetFrame "PyThreadState_GetFrame") 去获取一个帧对象。

可参考：[Reflection 1](reflection.md#reflection)

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyFrame_Type¶  

    

~~~
帧对象的类型。 它与 Python 层中的 [`types.FrameType`](../library/types.md#types.FrameType "types.FrameType") 是同一对象。

在 3.11 版本发生变更: 在之前版本中，此类型仅在包括 `<frameobject.h>` 之后可用。

int PyFrame_Check([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  
~~~
    

~~~
如果 _obj_ 是一个帧对象则返回非零值。

在 3.11 版本发生变更: 在之前版本中，只函数仅在包括 `<frameobject.h>` 之后可用。

PyFrameObject *PyFrame_GetBack(PyFrameObject *frame)¶  
~~~
    

~~~
获取 _frame_ 为下一个外部帧。

参考返回一个 [strong reference](../glossary.md#term-strong-reference)，或者如果 _frame_ 没有外部帧则返回 `NULL`。

在 3.9 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyFrame_GetBuiltins(PyFrameObject *frame)¶  
~~~
    

~~~
获取 _frame_ 的 `f_builtins` 属性。

返回一个 [strong reference](../glossary.md#term-strong-reference)。 此结果不可为 `NULL`。

在 3.11 版本加入.

[PyCodeObject](code.md#c.PyCodeObject "PyCodeObject") *PyFrame_GetCode(PyFrameObject *frame)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

获取 _frame_ 的代码。

返回一个 [strong reference](../glossary.md#term-strong-reference)。

结果（帧代码）不可为 `NULL`。

在 3.9 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyFrame_GetGenerator(PyFrameObject *frame)¶  
~~~
    

~~~
获取拥有该帧的生成器、协程或异步生成器，或者如果该帧不被某个生成器所拥有则为 `NULL`。 不会引发异常，即使其返回值为 `NULL`。

返回一个 [strong reference](../glossary.md#term-strong-reference)，或者 `NULL`。

在 3.11 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyFrame_GetGlobals(PyFrameObject *frame)¶  
~~~
    

~~~
获取 _frame_ 的 `f_globals` 属性。

返回一个 [strong reference](../glossary.md#term-strong-reference)。 此结果不可为 `NULL`。

在 3.11 版本加入.

int PyFrame_GetLasti(PyFrameObject *frame)¶  
~~~
    

~~~
获取 _frame_ 的 `f_lasti` 属性。

如果 `frame.f_lasti` 为 `None` 则返回 -1。

在 3.11 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyFrame_GetVar(PyFrameObject *frame, [PyObject](structures.md#c.PyObject "PyObject") *name)¶  
~~~
    

~~~
获取 _frame_ 的变量 _name_ 。

  * 成功时返回一个指向变量值的 [strong reference](../glossary.md#term-strong-reference)。

  * 引发 [`NameError`](../library/exceptions.md#NameError "NameError") 并返回 `NULL` 如果该变量不存在。

  * 引发异常并返回``NULL``错误。

_name_ 必须是 [`str`](../library/stdtypes.md#str "str") 类型的。

在 3.12 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyFrame_GetVarString(PyFrameObject *frame, const char *name)¶  
~~~
    

~~~
和 `PyFrame_GetVar()` 相似，但该变量名是一个使用 UTF-8 编码的 C 字符串。

在 3.12 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyFrame_GetLocals(PyFrameObject *frame)¶  
~~~
    

~~~
获取 _frame_ 的 `f_locals` 属性 ([`dict`](../library/stdtypes.md#dict "dict"))。

返回一个 [strong reference](../glossary.md#term-strong-reference)。

在 3.11 版本加入.

int PyFrame_GetLineNumber(PyFrameObject *frame)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

返回 _frame_ 当前正在执行的行号。

## 内部帧¶

除非使用:pep:523，否则你不会需要它。

struct _PyInterpreterFrame¶  
~~~
    

~~~
解释器的内部帧表示。

在 3.11 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnstable_InterpreterFrame_GetCode(struct _PyInterpreterFrame *frame);¶  
~~~
    

~~~
_This is[ Unstable API](stable.md#unstable-c-api). It may change without warning in minor releases._

> 返回一个指向帧的代码对象的 [strong reference](../glossary.md#term-strong-reference)。

在 3.12 版本加入.

int PyUnstable_InterpreterFrame_GetLasti(struct _PyInterpreterFrame *frame);¶  
~~~
    

~~~
_This is[ Unstable API](stable.md#unstable-c-api). It may change without warning in minor releases._

将字节偏移量返回到最后执行的指令中。

在 3.12 版本加入.

int PyUnstable_InterpreterFrame_GetLine(struct _PyInterpreterFrame *frame);¶  
~~~
    

~~~
