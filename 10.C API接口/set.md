# 集合对象¶

这一节详细介绍了针对 [`set`](stdtypes.md#set "set") 和 [`frozenset`](stdtypes.md#frozenset "frozenset") 对象的公共 API。 任何未在下面列出的功能最好是使用抽象对象协议 (包括 [`PyObject_CallMethod()`](call.md#c.PyObject_CallMethod "PyObject_CallMethod"), [`PyObject_RichCompareBool()`](object.md#c.PyObject_RichCompareBool "PyObject_RichCompareBool"), [`PyObject_Hash()`](object.md#c.PyObject_Hash "PyObject_Hash"), [`PyObject_Repr()`](object.md#c.PyObject_Repr "PyObject_Repr"), [`PyObject_IsTrue()`](object.md#c.PyObject_IsTrue "PyObject_IsTrue"), [`PyObject_Print()`](object.md#c.PyObject_Print "PyObject_Print") 以及 [`PyObject_GetIter()`](object.md#c.PyObject_GetIter "PyObject_GetIter")) 或者抽象数字协议 (包括 [`PyNumber_And()`](number.md#c.PyNumber_And "PyNumber_And"), [`PyNumber_Subtract()`](number.md#c.PyNumber_Subtract "PyNumber_Subtract"), [`PyNumber_Or()`](number.md#c.PyNumber_Or "PyNumber_Or"), [`PyNumber_Xor()`](number.md#c.PyNumber_Xor "PyNumber_Xor"), [`PyNumber_InPlaceAnd()`](number.md#c.PyNumber_InPlaceAnd "PyNumber_InPlaceAnd"), [`PyNumber_InPlaceSubtract()`](number.md#c.PyNumber_InPlaceSubtract "PyNumber_InPlaceSubtract"), [`PyNumber_InPlaceOr()`](number.md#c.PyNumber_InPlaceOr "PyNumber_InPlaceOr") 以及 [`PyNumber_InPlaceXor()`](number.md#c.PyNumber_InPlaceXor "PyNumber_InPlaceXor"))。

type PySetObject¶  

    

~~~
这个 [`PyObject`](structures.md#c.PyObject "PyObject") 的子类型被用来保存 [`set`](../library/stdtypes.md#set "set") 和 [`frozenset`](../library/stdtypes.md#frozenset "frozenset") 对象的内部数据。 它类似于 [`PyDictObject`](dict.md#c.PyDictObject "PyDictObject") 的地方在于对小尺寸集合来说它是固定大小的（很像元组的存储方式），而对于中等和大尺寸集合来说它将指向单独的可变大小的内存块（很像列表的存储方式）。 此结构体的字段不应被视为公有并且可能发生改变。 所有访问都应当通过已写入文档的 API 来进行而不可通过直接操纵结构体中的值。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PySet_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是一个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 实例，表示 Python [`set`](../library/stdtypes.md#set "set") 类型。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyFrozenSet_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是一个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 实例，表示 Python [`frozenset`](../library/stdtypes.md#frozenset "frozenset") 类型。

下列类型检查宏适用于指向任意 Python 对象的指针。 类似地，这些构造函数也适用于任意可迭代的 Python 对象。

int PySet_Check([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果 _p_ 是一个 [`set`](../library/stdtypes.md#set "set") 对象或者是其子类型的实例则返回真值。 此函数总是会成功执行。

int PyFrozenSet_Check([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果 _p_ 是一个 [`frozenset`](../library/stdtypes.md#frozenset "frozenset") 对象或者是其子类型的实例则返回真值。 此函数总是会成功执行。

int PyAnySet_Check([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果 _p_ 是一个 [`set`](../library/stdtypes.md#set "set") 对象、[`frozenset`](../library/stdtypes.md#frozenset "frozenset") 对象或者是其子类型的实例则返回真值。 此函数总是会成功执行。

int PySet_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果 _p_ 是一个 [`set`](../library/stdtypes.md#set "set") 对象但不是其子类型的实例则返回真值。 此函数总是会成功执行。

在 3.10 版本加入.

int PyAnySet_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果 _p_ 是一个 [`set`](../library/stdtypes.md#set "set") 或 [`frozenset`](../library/stdtypes.md#frozenset "frozenset") 对象但不是其子类型的实例则返回真值。 此函数总是会成功执行。

int PyFrozenSet_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果 _p_ 是一个 [`frozenset`](../library/stdtypes.md#frozenset "frozenset") 对象但不是其子类型的实例则返回真值。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PySet_New([PyObject](structures.md#c.PyObject "PyObject") *iterable)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回一个新的 [`set`](../library/stdtypes.md#set "set")，其中包含 _iterable_ 所返回的对象。 _iterable_ 可以为 `NULL` 表示创建一个新的空集合。 成功时返回新的集合，失败时返回 `NULL`。 如果 _iterable_ 实际上不是可迭代对象则引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError")。 该构造器也适用于拷贝集合 (`c=set(s)`)。

[PyObject](structures.md#c.PyObject "PyObject") *PyFrozenSet_New([PyObject](structures.md#c.PyObject "PyObject") *iterable)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回一个新的 [`frozenset`](../library/stdtypes.md#frozenset "frozenset")，其中包含 _iterable_ 所返回的对象。 _iterable_ 可以为 `NULL` 表示创建一个新的空冻结集合。 成功时返回新的冻结集合，失败时返回 `NULL`。 如果 _iterable_ 实际上不是可迭代对象则引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError")。

下列函数和宏适用于 [`set`](../library/stdtypes.md#set "set") 或 [`frozenset`](../library/stdtypes.md#frozenset "frozenset") 的实例或是其子类型的实例。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PySet_Size([PyObject](structures.md#c.PyObject "PyObject") *anyset)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 [`set`](../library/stdtypes.md#set "set") 或 [`frozenset`](../library/stdtypes.md#frozenset "frozenset") 对象的长度。 等同于 `len(anyset)`。 如果 _anyset_ 不是 [`set`](../library/stdtypes.md#set "set"), [`frozenset`](../library/stdtypes.md#frozenset "frozenset") 或其子类型的实例，则会引发 [`SystemError`](../library/exceptions.md#SystemError "SystemError")。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PySet_GET_SIZE([PyObject](structures.md#c.PyObject "PyObject") *anyset)¶  
~~~
    

~~~
