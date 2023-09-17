# 对象协议¶

[PyObject](structures.md#c.PyObject "PyObject") *Py_NotImplemented¶  

    

~~~
`NotImplemented` 单例，用于标记某个操作没有针对给定类型组合的实现。

Py_RETURN_NOTIMPLEMENTED¶  
~~~
    

~~~
正确处理从 C 语言函数中返回 `Py_NotImplemented` 的问题（即新建一个指向 NotImplemented 的 [strong reference](../glossary.md#term-strong-reference) 并返回它）。

int PyObject_Print([PyObject](structures.md#c.PyObject "PyObject") *o, FILE *fp, int flags)¶  
~~~
    

~~~
打印对象 _o_ 到文件 _fp_ 。 出错时返回 `-1`。 flags 参数被用于启用特定的打印选项。 目前唯一支持的选项是 `Py_PRINT_RAW`；如果给出该选项，则将写入对象的 [`str()`](../library/stdtypes.md#str "str") 而不是 [`repr()`](../library/functions.md#repr "repr")。

int PyObject_HasAttr([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *attr_name)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果 _o_ 带有属性 _attr_name_ ，则返回 `1`，否则返回 `0`。这相当于 Python 表达式 `hasattr(o, attr_name)`。 此函数总是成功。

备注

Exceptions that occur when this calls [`__getattr__()`](../reference/datamodel.md#object.__getattr__ "object.__getattr__") and [`__getattribute__()`](../reference/datamodel.md#object.__getattribute__ "object.__getattribute__") methods are silently ignored. For proper error handling, use `PyObject_GetOptionalAttr()` or `PyObject_GetAttr()` instead.

int PyObject_HasAttrString([PyObject](structures.md#c.PyObject "PyObject") *o, const char *attr_name)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这与 `PyObject_HasAttr()` 相同，但 _attr_name_ 被指定为 const char* UTF-8 编码的字节串，而不是 [PyObject](structures.md#c.PyObject "PyObject")*。

备注

Exceptions that occur when this calls [`__getattr__()`](../reference/datamodel.md#object.__getattr__ "object.__getattr__") and [`__getattribute__()`](../reference/datamodel.md#object.__getattribute__ "object.__getattribute__") methods or while creating the temporary [`str`](../library/stdtypes.md#str "str") object are silently ignored. For proper error handling, use `PyObject_GetOptionalAttrString()` or `PyObject_GetAttrString()` instead.

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_GetAttr([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *attr_name)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

从对象 _o_ 中读取名为 _attr_name_ 的属性。成功返回属性值，失败则返回 `NULL`。 这相当于 Python 表达式 `o.attr_name`。

If the missing attribute should not be treated as a failure, you can use `PyObject_GetOptionalAttr()` instead.

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_GetAttrString([PyObject](structures.md#c.PyObject "PyObject") *o, const char *attr_name)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

这与 `PyObject_GetAttr()` 相同，但 _attr_name_ 被指定为 const char* UTF-8 编码的字节串，而不是 [PyObject](structures.md#c.PyObject "PyObject")*。

If the missing attribute should not be treated as a failure, you can use `PyObject_GetOptionalAttrString()` instead.

int PyObject_GetOptionalAttr([PyObject](structures.md#c.PyObject "PyObject") *obj, [PyObject](structures.md#c.PyObject "PyObject") *attr_name, [PyObject](structures.md#c.PyObject "PyObject") **result);¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.13._

Variant of `PyObject_GetAttr()` which doesn't raise [`AttributeError`](../library/exceptions.md#AttributeError "AttributeError") if the attribute is not found.

If the attribute is found, return `1` and set _*result_ to a new [strong reference](../glossary.md#term-strong-reference) to the attribute. If the attribute is not found, return `0` and set _*result_ to `NULL`; the [`AttributeError`](../library/exceptions.md#AttributeError "AttributeError") is silenced. If an error other than [`AttributeError`](../library/exceptions.md#AttributeError "AttributeError") is raised, return `-1` and set _*result_ to `NULL`.

在 3.13 版本加入.

int PyObject_GetOptionalAttrString([PyObject](structures.md#c.PyObject "PyObject") *obj, const char *attr_name, [PyObject](structures.md#c.PyObject "PyObject") **result);¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.13._

This is the same as `PyObject_GetOptionalAttr()`, but _attr_name_ is specified as a const char* UTF-8 encoded bytes string, rather than a [PyObject](structures.md#c.PyObject "PyObject")*.

在 3.13 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_GenericGetAttr([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *name)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

通用的属性获取函数，用于放入类型对象的 `tp_getattro` 槽中。它在类的字典中（位于对象的 MRO 中）查找某个描述符，并在对象的 [`__dict__`](../library/stdtypes.md#object.__dict__ "object.__dict__") 中查找某个属性。正如 [实现描述器](../reference/datamodel.md#descriptors) 所述，数据描述符优先于实例属性，而非数据描述符则不优先。失败则会触发 [`AttributeError`](../library/exceptions.md#AttributeError "AttributeError") 。

int PyObject_SetAttr([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *attr_name, [PyObject](structures.md#c.PyObject "PyObject") *v)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将对象 _o_ 中名为 _attr_name_ 的属性值设为 _v_ 。失败时引发异常并返回 `-1`；成功时返 回 `0` 。这相当于 Python 语句 `o.attr_name = v`。

如果 _v_ 为 `NULL`，该属性将被删除。 此行为已被弃用而应改用 `PyObject_DelAttr()`，但目前还没有移除它的计划。

int PyObject_SetAttrString([PyObject](structures.md#c.PyObject "PyObject") *o, const char *attr_name, [PyObject](structures.md#c.PyObject "PyObject") *v)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这与 `PyObject_SetAttr()` 相同，但 _attr_name_ 被指定为 const char* UTF-8 编码的字节串，而不是 [PyObject](structures.md#c.PyObject "PyObject")*。

如果 _v_ 为 `NULL`，该属性将被删除，但是此功能已被弃用而应改用 `PyObject_DelAttrString()`。

int PyObject_GenericSetAttr([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *name, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

通用的属性设置和删除函数，用于放入类型对象的 [`tp_setattro`](typeobj.md#c.PyTypeObject.tp_setattro "PyTypeObject.tp_setattro") 槽。它在类的字典中（位于对象的MRO中）查找数据描述器，如果找到，则将比在实例字典中设置或删除属性优先执行。否则，该属性将在对象的 [`__dict__`](../library/stdtypes.md#object.__dict__ "object.__dict__") 中设置或删除。如果成功将返回 `0`，否则将引发 [`AttributeError`](../library/exceptions.md#AttributeError "AttributeError") 并返回 `-1`。

int PyObject_DelAttr([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *attr_name)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.13._

删除对象 _o_ 中名为 _attr_name_ 的属性。失败时返回 `-1`。这相当于 Python 语句 `del o.attr_name`。

int PyObject_DelAttrString([PyObject](structures.md#c.PyObject "PyObject") *o, const char *attr_name)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.13._

这与 `PyObject_DelAttr()` 相同，但 _attr_name_ 被指定为 const char* UTF-8 编码的字节串，而不是 [PyObject](structures.md#c.PyObject "PyObject")*。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_GenericGetDict([PyObject](structures.md#c.PyObject "PyObject") *o, void *context)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

`__dict__` 描述符的获取函数的一种通用实现。必要时会创建该字典。

此函数还可能会被调用以获取对象 _o_ 的 [`__dict__`](../library/stdtypes.md#object.__dict__ "object.__dict__")。 当调用它时可传入 `NULL` 作为 _context_ 。 由于此函数可能需要为字典分配内存，所以在访问对象上的属性时调用 `PyObject_GetAttr()` 可能会更为高效。

当失败时，将返回 `NULL` 并设置一个异常。

在 3.3 版本加入.

int PyObject_GenericSetDict([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *value, void *context)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

`__dict__` 描述符设置函数的一种通用实现。这里不允许删除该字典。

在 3.3 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") **_PyObject_GetDictPtr([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  
~~~
    

~~~
返回一个指向对象 _obj_ 的 [`__dict__`](../library/stdtypes.md#object.__dict__ "object.__dict__") 的指针。 如果不存在 `__dict__`，则返回 `NULL` 并且不设置异常。

此函数可能需要为字典分配内存，所以在访问对象上的属性时调用 `PyObject_GetAttr()` 可能会更为高效。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_RichCompare([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2, int opid)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

使用由 _opid_ 指定的操作来比较 _o1_ 和 _o2_ 的值，操作必须为 [`Py_LT`](typeobj.md#c.Py_LT "Py_LT"), [`Py_LE`](typeobj.md#c.Py_LE "Py_LE"), [`Py_EQ`](typeobj.md#c.Py_EQ "Py_EQ"), [`Py_NE`](typeobj.md#c.Py_NE "Py_NE"), [`Py_GT`](typeobj.md#c.Py_GT "Py_GT") 或 [`Py_GE`](typeobj.md#c.Py_GE "Py_GE") 中的一个，分别对应于 `<`, `<=`, `==`, `!=`, `>` 或 `>=`。 这等价于 Python 表达式 `o1 op o2`，其中 `op` 是与 _opid_ 对应的运算符。 成功时返回比较结果值，失败时返回 `NULL`。

int PyObject_RichCompareBool([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2, int opid)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

使用由 _opid_ 指定的操作来比较 _o1_ 和 _o2_ 的值，操作必须为 [`Py_LT`](typeobj.md#c.Py_LT "Py_LT"), [`Py_LE`](typeobj.md#c.Py_LE "Py_LE"), [`Py_EQ`](typeobj.md#c.Py_EQ "Py_EQ"), [`Py_NE`](typeobj.md#c.Py_NE "Py_NE"), [`Py_GT`](typeobj.md#c.Py_GT "Py_GT") 或 [`Py_GE`](typeobj.md#c.Py_GE "Py_GE") 中的一个，分别对应于 `<`, `<=`, `==`, `!=`, `>` 或 `>=`。 失败时返回 `-1`，结果为假值时返回 `0`，否则返回 `1`。 这等价于 Python 表达式 `o1 op o2`，其中 `op` 是与 _opid_ 对应的运算符。

备注

如果 _o1_ 和 _o2_ 是同一个对象，`PyObject_RichCompareBool()` 将总是为 [`Py_EQ`](typeobj.md#c.Py_EQ "Py_EQ") 返回 `1` 并为 [`Py_NE`](typeobj.md#c.Py_NE "Py_NE") 返回 `0`。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_Format([PyObject](structures.md#c.PyObject "PyObject") *obj, [PyObject](structures.md#c.PyObject "PyObject") *format_spec)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

格式 _obj_ 使用 _format_spec_ 。 这等价于 Python 表达式 `format(obj, format_spec)`。

_format_spec_ 可以为 `NULL`。 在此情况下调用将等价于 `format(obj)`。 成功时返回已格式化的字符串，失败时返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_Repr([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

计算对象 _o_ 的字符串形式。 成功时返回字符串，失败时返回 `NULL`。 这相当于 Python 表达式 `repr(o)`。 由内置函数 [`repr()`](../library/functions.md#repr "repr") 调用。

在 3.4 版本发生变更: 该函数现在包含一个调试断言，用以确保不会静默地丢弃活动的异常。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_ASCII([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

与 `PyObject_Repr()` 一样，计算对象 _o_ 的字符串形式，但在 `PyObject_Repr()` 返回的字符串中用 `\x`、`\u` 或 `\U` 转义非 ASCII 字符。这将生成一个类似于 Python 2 中由 `PyObject_Repr()` 返回的字符串。由内置函数 [`ascii()`](../library/functions.md#ascii "ascii") 调用。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_Str([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

计算对象 _o_ 的字符串形式。 成功时返回字符串，失败时返回 `NULL`。 这相当于 Python 表达式 `str(o)`。由内置函数 [`str()`](../library/stdtypes.md#str "str") 调用，因此也由 [`print()`](../library/functions.md#print "print") 函数调用。

在 3.4 版本发生变更: 该函数现在包含一个调试断言，用以确保不会静默地丢弃活动的异常。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_Bytes([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

计算对象 _o_ 的字节形式。失败时返回 `NULL`，成功时返回一个字节串对象。这相当于 _o_ 不是整数时的 Python 表达式 `bytes(o)` 。与 `bytes(o)` 不同的是，当 _o_ 是整数而不是初始为 0 的字节串对象时，会触发 TypeError。

int PyObject_IsSubclass([PyObject](structures.md#c.PyObject "PyObject") *derived, [PyObject](structures.md#c.PyObject "PyObject") *cls)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果 _derived_ 类与 _cls_ 类相同或为其派生类，则返回 `1`，否则返回 `0`。 如果出错则返回 `-1`。

如果 _cls_ 是元组，则会对 _cls_ 进行逐项检测。如果至少有一次检测返回 `1`，结果将为 `1`，否则将是 `0`。

正如 [**PEP 3119**](https://peps.python.org/pep-3119/) 所述，如果 _cls_ 带有 [`__subclasscheck__()`](../reference/datamodel.md#class.__subclasscheck__ "class.__subclasscheck__") 方法，将会被调用以确定子类的状态。 否则，如果 _derived_ 是个直接或间接子类，即包含在 `cls.__mro__` 中，那么它就是 _cls_ 的一个子类。

通常只有类对象（即 [`type`](../library/functions.md#type "type") 或派生类的实例）才被视为类。 但是，对象可以通过设置 [`__bases__`](../library/stdtypes.md#class.__bases__ "class.__bases__") 属性（必须是基类的元组）来覆盖这一点。

int PyObject_IsInstance([PyObject](structures.md#c.PyObject "PyObject") *inst, [PyObject](structures.md#c.PyObject "PyObject") *cls)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果 _inst_ 是 _cls_ 类或其子类的实例，则返回 `1`，如果不是则返回 `0`。 如果出错则返回 `-1` 并设置一个异常。

如果 _cls_ 是元组，则会对 _cls_ 进行逐项检测。如果至少有一次检测返回 `1`，结果将为 `1`，否则将是 `0`。

正如 [**PEP 3119**](https://peps.python.org/pep-3119/) 所述，如果 _cls_ 带有 [`__subclasscheck__()`](../reference/datamodel.md#class.__subclasscheck__ "class.__subclasscheck__") 方法，将会被调用以确定子类的状态。 否则，如果 _derived_ 是 _cls_ 的子类，那么它就是 _cls_ 的一个实例。

实例 _inst_ 可以通过 [`__class__`](../library/stdtypes.md#instance.__class__ "instance.__class__") 属性来覆盖其所属的类。

对象 _cls_ 可以通过设置 [`__bases__`](../library/stdtypes.md#class.__bases__ "class.__bases__") 属性（该属性必须是基类的元组）来覆盖其是否会被视为类，及其有哪些基类。

Py_hash_t PyObject_Hash([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

计算并返回对象的哈希值 _o_ 。 失败时返回 `-1`。这相当于 Python 表达式 `hash(o)`。

在 3.2 版本发生变更: 现在的返回类型是 Py_hash_t。 这是一个大小与 [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 相同的有符号整数。

Py_hash_t PyObject_HashNotImplemented([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

设置一个 [`TypeError`](../library/exceptions.md#TypeError "TypeError") 来指明 `type(o)` 不是 [hashable](../glossary.md#term-hashable) 并返回 `-1`。 此函数在存储于 `tp_hash` 槽位内时会获得特别对待，允许某个类型显式地向解释器指明它是不可哈希对象。

int PyObject_IsTrue([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果对象 _o_ 被认为是 true，则返回 `1`，否则返回 `0`。这相当于 Python 表达式 `not not o`。 失败则返回 `-1`。

int PyObject_Not([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果对象 _o_ 被认为是 true，则返回 `1`，否则返回 `0`。这相当于 Python 表达式 `not not o`。 失败则返回 `-1`。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_Type([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

当 _o_ 不为 `NULL` 时，返回一个与对象 _o_ 的类型相对应的类型对象。 当失败时，将引发 [`SystemError`](../library/exceptions.md#SystemError "SystemError") 并返回 `NULL`。 这等同于 Python 表达式 `type(o)`。 该函数会新建一个指向返回值的 [strong reference](../glossary.md#term-strong-reference)。 实际上没有多少理由使用此函数来替代 [`Py_TYPE()`](structures.md#c.Py_TYPE "Py_TYPE") 函数，后者将返回一个 [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject")* 类型的指针，除非是需要一个新的 [strong reference](../glossary.md#term-strong-reference)。

int PyObject_TypeCheck([PyObject](structures.md#c.PyObject "PyObject") *o, [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type)¶  
~~~
    

~~~
如果对象 _o_ 是 _type_ 类型或其子类型，则返回非零，否则返回 `0`。两个参数都必须非 `NULL`。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyObject_Size([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyObject_Length([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回对象 _o_ 的长度。 如果对象 _o_ 支持序列和映射协议，则返回序列长度。 出错时返回 `-1`。这等同于 Python 表达式 `len(o)`。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyObject_LengthHint([PyObject](structures.md#c.PyObject "PyObject") *o, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") defaultvalue)¶  
~~~
    

~~~
返回对象 _o_ 的估计长度。首先尝试返回实际长度，然后用 [`__length_hint__()`](../reference/datamodel.md#object.__length_hint__ "object.__length_hint__") 进行估计，最后返回默认值。出错时返回 `-1`。这等同于 Python 表达式 `operator.length_hint(o, defaultvalue)`。

在 3.4 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_GetItem([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *key)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回对象 _key_ 对应的 _o_ 元素，或在失败时返回 `NULL`。这等同于 Python 表达式 `o[key]`。

int PyObject_SetItem([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *key, [PyObject](structures.md#c.PyObject "PyObject") *v)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将对象 _key_ 映射到值 _v_ 。 失败时引发异常并返回 `-1`；成功时返回 `0`。 这相当于 Python 语句 `o[key] = v`。该函数 _不会_ 偷取 _v_ 的引用计数。

int PyObject_DelItem([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *key)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

从对象 _o_ 中移除对象 _key_ 的映射。失败时返回 `-1`。 这相当于 Python 语句 `del o[key]`。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_Dir([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

相当于 Python 表达式 `dir(o)`，返回一个（可能为空）适合对象参数的字符串列表，如果出错则返回 `NULL`。 如果参数为 `NULL`，类似 Python 的 `dir()`，则返回当前 locals 的名字；这时如果没有活动的执行框架，则返回 `NULL`，但 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 将返回 false。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_GetIter([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

等同于 Python 表达式 `iter(o)`。为对象参数返回一个新的迭代器，如果该对象已经是一个迭代器，则返回对象本身。如果对象不能被迭代，会引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError") ，并返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_GetAIter([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

等同于 Python 表达式 `aiter(o)`。接受一个 `AsyncIterable` 对象，并为其返回一个 `AsyncIterator`。通常返回的是一个新迭代器，但如果参数是一个 `AsyncIterator`，将返回其自身。如果该对象不能被迭代，会引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError")，并返回 `NULL`。

在 3.10 版本加入.

void *PyObject_GetTypeData([PyObject](structures.md#c.PyObject "PyObject") *o, [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *cls)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

获取一个指向为 _cls_ 保留的子类专属数据的指针。

对象 _o_ 必须为 _cls_ 的实例，而 _cls_ 必须使用负的 [`PyType_Spec.basicsize`](type.md#c.PyType_Spec.basicsize "PyType_Spec.basicsize") 来创建。 Python 不会检查这一点。

发生错误时，将设置异常并返回 `NULL`。

在 3.12 版本加入.

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyType_GetTypeDataSize([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *cls)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

返回为 _cls_ 保留的实例内存空间大小，即 `PyObject_GetTypeData()` 所返回的内存大小。

这可能会大于使用 [`-PyType_Spec.basicsize`](type.md#c.PyType_Spec.basicsize "PyType_Spec.basicsize") 请求到的大小；可以安全地使用这个更大的值 (例如通过 `memset()`)。

类型 _cls_ **必须** 使用负的 [`PyType_Spec.basicsize`](type.md#c.PyType_Spec.basicsize "PyType_Spec.basicsize") 来创建。 Python 不会检查这一点。

当失败时，将设置异常并返回一个负值。

在 3.12 版本加入.

void *PyObject_GetItemData([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
