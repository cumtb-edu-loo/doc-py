# 整数型对象¶

所有整数都实现为长度任意的长整数对象。

在出错时，大多数 `PyLong_As*` API 都会返回 `(return type)-1`，这与数字无法区分开。请采用 [`PyErr_Occurred()`](10.C%20API接口/exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 来加以区分。

type PyLongObject¶  

    _Part of the[ Limited API](stable.md#stable) (as an opaque struct)._

表示 Python 整数对象的 [`PyObject`](structures.md#c.PyObject "PyObject") 子类型。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyLong_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的实例表示 Python 的整数类型。与 Python 语言中的 [`int`](functions.md#int "int") 相同。

int PyLong_Check([PyObject](structures.md#c.PyObject "PyObject") *p)¶  

    

~~~
如果参数是 `PyLongObject` 或 `PyLongObject` 的子类型，则返回 True。该函数一定能够执行成功。

int PyLong_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果其参数属于 `PyLongObject`，但不是 `PyLongObject` 的子类型则返回真值。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PyLong_FromLong(long v)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

由 _v_ 返回一个新的 `PyLongObject` 对象，失败时返回 `NULL` 。

当前的实现维护着一个整数对象数组，包含 `-5` 和 `256` 之间的所有整数对象。 若创建一个位于该区间的 int 时，实际得到的将是对已有对象的引用。

[PyObject](structures.md#c.PyObject "PyObject") *PyLong_FromUnsignedLong(unsigned long v)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

基于 C unsigned long 返回一个新的 `PyLongObject` 对象，失败时返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyLong_FromSsize_t([Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") v)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

由 C [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 返回一个新的 `PyLongObject` 对象，失败时返回 `NULL` 。

[PyObject](structures.md#c.PyObject "PyObject") *PyLong_FromSize_t(size_t v)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

由 C `size_t` 返回一个新的 `PyLongObject` 对象，失败则返回 `NULL` 。

[PyObject](structures.md#c.PyObject "PyObject") *PyLong_FromLongLong(long long v)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

基于 C long long 返回一个新的 `PyLongObject`，失败时返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyLong_FromUnsignedLongLong(unsigned long long v)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

基于 C unsigned long long 返回一个新的 `PyLongObject` 对象，失败时返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyLong_FromDouble(double v)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

由 _v_ 的整数部分返回一个新的 `PyLongObject` 对象，失败则返回 `NULL` 。

[PyObject](structures.md#c.PyObject "PyObject") *PyLong_FromString(const char *str, char **pend, int base)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

根据 _str_ 字符串值返回一个新的 `PyLongObject`，它将根据 _base_ 指定的基数来解读，或是在失败时返回 `NULL`。 如果 _pend_ 不为 `NULL`，则在成功时 _*pend_ 将指向 _str_ 中末尾而在出错时将指向第一个无法处理的字符。 如果 _base_ 为 `0`，则 _str_ 将使用 [整数字面值](../reference/lexical_analysis.md#integers) 定义来解读；在此情况下，非零十进制数以零开头将会引发 [`ValueError`](../library/exceptions.md#ValueError "ValueError")。 如果 _base_ 不为 `0`，则必须在 `2` 和 `36`，包括这两个值。 开头和末尾的空格以及基数标示符之后和数码之间的单下划线将被忽略。 如果没有数码或 _str_ 中数码和末尾空格之后不以 NULL 结束，则将引发 [`ValueError`](../library/exceptions.md#ValueError "ValueError")。

参见

Python 方法 [`int.to_bytes()`](../library/stdtypes.md#int.to_bytes "int.to_bytes") 和 [`int.from_bytes()`](../library/stdtypes.md#int.from_bytes "int.from_bytes") 用于 `PyLongObject` 到/从字节数组之间以 `256` 为基数进行转换。 你可以使用 [`PyObject_CallMethod()`](call.md#c.PyObject_CallMethod "PyObject_CallMethod") 从 C 调用它们。

[PyObject](structures.md#c.PyObject "PyObject") *PyLong_FromUnicodeObject([PyObject](structures.md#c.PyObject "PyObject") *u, int base)¶  

    _返回值：新的引用。_

将字符串 _u_ 中的 Unicode 数字序列转换为 Python 整数值。

在 3.3 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyLong_FromVoidPtr(void *p)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

从指针 _p_ 创建一个 Python 整数。可以使用 `PyLong_AsVoidPtr()` 返回的指针值。

long PyLong_AsLong([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _obj_ 的 C long 表示形式。 如果 _obj_ 不是 `PyLongObject` 的实例，则会先调用其 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__") 方法（如果存在）将其转换为 `PyLongObject`。

如果 _obj_ 的值超出了 long 的取值范围则会引发 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError")。

出错则返回 `-1` 。请用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 找出具体问题。

在 3.8 版本发生变更: 如果可能将使用 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__")。

在 3.10 版本发生变更: 此函数将不再使用 [`__int__()`](../reference/datamodel.md#object.__int__ "object.__int__")。

int PyLong_AsInt([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.13._

Similar to `PyLong_AsLong()`, but store the result in a C int instead of a C long.

在 3.13 版本加入.

long PyLong_AsLongAndOverflow([PyObject](structures.md#c.PyObject "PyObject") *obj, int *overflow)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _obj_ 的 C long 表示形式。 如果 _obj_ 不是 `PyLongObject` 的实例，则会先调用其 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__") 方法（如果存在）将其转换为 `PyLongObject`。

如果 _obj_ 的值大于 `LONG_MAX` 或小于 `LONG_MIN`，则会把 _*overflow_ 分别置为 `1` 或 `-1`，并返回 `-1`；否则，将 _*overflow_ 置为 `0`。 如果发生其他异常则按常规把 _*overflow_ 置为 `0` 并返回 `-1`。

出错则返回 `-1` 。请用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 找出具体问题。

在 3.8 版本发生变更: 如果可能将使用 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__")。

在 3.10 版本发生变更: 此函数将不再使用 [`__int__()`](../reference/datamodel.md#object.__int__ "object.__int__")。

long long PyLong_AsLongLong([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _obj_ 的 C long long 表示形式。 如果 _obj_ 不是 `PyLongObject` 的实例，则会先调用其 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__") 方法（如果存在）将其转换为 `PyLongObject`。

如果 _obj_ 值超出 long long 的取值范围则会引发 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError")。

出错则返回 `-1` 。请用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 找出具体问题。

在 3.8 版本发生变更: 如果可能将使用 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__")。

在 3.10 版本发生变更: 此函数将不再使用 [`__int__()`](../reference/datamodel.md#object.__int__ "object.__int__")。

long long PyLong_AsLongLongAndOverflow([PyObject](structures.md#c.PyObject "PyObject") *obj, int *overflow)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _obj_ 的 C long long 表示形式。 如果 _obj_ 不是 `PyLongObject` 的实例，则会先调用其 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__") 方法（如果存在）将其转换为 `PyLongObject`。

如果 _obj_ 的值大于 `LLONG_MAX` 或小于 `LLONG_MIN`，则会把 _*overflow_ 分别置为 `1` 或 `-1`，并返回 `-1`；否则，将 _*overflow_ 置为 `0`。 如果发生其他异常则按常规把 _*overflow_ 置为 `0` 并返回 `-1`。

出错则返回 `-1` 。请用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 找出具体问题。

在 3.2 版本加入.

在 3.8 版本发生变更: 如果可能将使用 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__")。

在 3.10 版本发生变更: 此函数将不再使用 [`__int__()`](../reference/datamodel.md#object.__int__ "object.__int__")。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyLong_AsSsize_t([PyObject](structures.md#c.PyObject "PyObject") *pylong)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _pylong_ 的 C 语言 [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 形式。 _pylong_ 必须是 `PyLongObject` 的实例。

如果 _pylong_ 的值超出了 [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 的取值范围则会引发 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError")。

出错则返回 `-1` 。请用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 找出具体问题。

unsigned long PyLong_AsUnsignedLong([PyObject](structures.md#c.PyObject "PyObject") *pylong)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _pylong_ 的 C unsigned long 表示形式。 _pylong_ 必须是 `PyLongObject` 的实例。

如果 _pylong_ 的值超出了 unsigned long 的取值范围则会引发 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError")。

出错时返回 `(unsigned long)-1` ，请利用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 辨别具体问题。

size_t PyLong_AsSize_t([PyObject](structures.md#c.PyObject "PyObject") *pylong)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _pylong_ 的 C 语言 `size_t` 形式。 _pylong_ 必须是 `PyLongObject` 的实例。

如果 _pylong_ 的值超出了 `size_t` 的取值范围则会引发 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError")。

出错时返回 `(size_t)-1` ，请利用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 辨别具体问题。

unsigned long long PyLong_AsUnsignedLongLong([PyObject](structures.md#c.PyObject "PyObject") *pylong)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _pylong_ 的 C unsigned long long 表示形式。 _pylong_ 必须是 `PyLongObject` 的实例。

如果 _pylong_ 的值超出 unsigned long long 的取值范围则会引发 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError")。

出错时返回 `(unsigned long long)-1`，请利用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 辨别具体问题。

在 3.1 版本发生变更: 现在 _pylong_ 为负值会触发 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError")，而不是 [`TypeError`](../library/exceptions.md#TypeError "TypeError")。

unsigned long PyLong_AsUnsignedLongMask([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _obj_ 的 C unsigned long 表示形式。 如果 _obj_ 不是 `PyLongObject` 的实例，则会先调用其 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__") 方法（如果存在）将其转换为 `PyLongObject`。

如果 _obj_ 的值超出了 unsigned long 的取值范围，则返回该值对 `ULONG_MAX + 1` 求模的余数。

出错时返回 `(unsigned long)-1`，请利用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 辨别具体问题。

在 3.8 版本发生变更: 如果可能将使用 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__")。

在 3.10 版本发生变更: 此函数将不再使用 [`__int__()`](../reference/datamodel.md#object.__int__ "object.__int__")。

unsigned long long PyLong_AsUnsignedLongLongMask([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _obj_ 的 C unsigned long long 表示形式。 如果 _obj_ 不是 `PyLongObject` 的实例，则会先调用其 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__") 方法（如果存在）将其转换为 `PyLongObject`。

如果 _obj_ 的值超出了 unsigned long long 的取值范围，则返回该值对 `ULLONG_MAX + 1` 求模的余数。

出错时返回 `(unsigned long long)-1`，请利用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 辨别具体问题。

在 3.8 版本发生变更: 如果可能将使用 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__")。

在 3.10 版本发生变更: 此函数将不再使用 [`__int__()`](../reference/datamodel.md#object.__int__ "object.__int__")。

double PyLong_AsDouble([PyObject](structures.md#c.PyObject "PyObject") *pylong)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _pylong_ 的 C double 表示形式。 _pylong_ 必须是 `PyLongObject` 的实例。

如果 _pylong_ 的值超出了 double 的取值范围则会引发 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError")。

出错时返回 `-1.0` ，请利用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 辨别具体问题。

void *PyLong_AsVoidPtr([PyObject](structures.md#c.PyObject "PyObject") *pylong)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将一个 Python 整数 _pylong_ 转换为 C void 指针。 如果 _pylong_ 无法被转换，则将引发 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError")。 这只是为了保证将通过 `PyLong_FromVoidPtr()` 创建的值产生一个可用的 void 指针。

出错时返回 `NULL`，请利用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 辨别具体问题。

int PyUnstable_Long_IsCompact(const PyLongObject *op)¶  
~~~
    

~~~
_This is[ Unstable API](stable.md#unstable-c-api). It may change without warning in minor releases._

如果 _op_ 为紧凑形式则返回 1，否则返回 0。

此函数使得显著影响性能的关键代码可以实现小整数的“快速路径”。 对于紧凑形式的值使用 `PyUnstable_Long_CompactValue()`；对于其他值则回退为 `PyLong_As*` 函数或者 [`调用`](call.md#c.PyObject_CallMethod "PyObject_CallMethod") [`int.to_bytes()`](../library/stdtypes.md#int.to_bytes "int.to_bytes")。

此项加速对于大多数用户来说是可以忽略的。

具体有哪些值会被视为紧凑形式属于实现细节并可能发生改变。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyUnstable_Long_CompactValue(const PyLongObject *op)¶  
~~~
    

~~~
