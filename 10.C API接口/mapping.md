# 映射协议¶

参见 [`PyObject_GetItem()`](object.md#c.PyObject_GetItem "PyObject_GetItem")、[`PyObject_SetItem()`](object.md#c.PyObject_SetItem "PyObject_SetItem") 与 [`PyObject_DelItem()`](object.md#c.PyObject_DelItem "PyObject_DelItem")。

int PyMapping_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果对象提供了映射协议或是支持切片则返回 `1`，否则返回 `0`。 请注意它将为具有 [`__getitem__()`](datamodel.md#object.__getitem__ "object.__getitem__") 方法的 Python 类返回 `1`，因为在通常情况下无法确定该类支持哪种键类型。 此函数总是会成功执行。

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") PyMapping_Size([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") PyMapping_Length([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

成功时返回对象 _o_ 中键的数量，失败时返回 `-1`。 这相当于 Python 表达式 `len(o)`。

[PyObject](structures.md#c.PyObject "PyObject") *PyMapping_GetItemString([PyObject](structures.md#c.PyObject "PyObject") *o, const char *key)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

这与 [`PyObject_GetItem()`](object.md#c.PyObject_GetItem "PyObject_GetItem") 相同，但 _key_ 被指定为 const char* UTF-8 编码的字节串，而不是 [PyObject](structures.md#c.PyObject "PyObject")*。

int PyMapping_GetOptionalItem([PyObject](structures.md#c.PyObject "PyObject") *obj, [PyObject](structures.md#c.PyObject "PyObject") *key, [PyObject](structures.md#c.PyObject "PyObject") **result)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.13._

Variant of [`PyObject_GetItem()`](object.md#c.PyObject_GetItem "PyObject_GetItem") which doesn't raise [`KeyError`](3.标准库/exceptions.md#KeyError "KeyError") if the key is not found.

If the key is found, return `1` and set _*result_ to a new [strong reference](../glossary.md#term-strong-reference) to the corresponding value. If the key is not found, return `0` and set _*result_ to `NULL`; the [`KeyError`](3.标准库/exceptions.md#KeyError "KeyError") is silenced. If an error other than [`KeyError`](3.标准库/exceptions.md#KeyError "KeyError") is raised, return `-1` and set _*result_ to `NULL`.

在 3.13 版本加入.

int PyMapping_GetOptionalItemString([PyObject](structures.md#c.PyObject "PyObject") *obj, const char *key, [PyObject](structures.md#c.PyObject "PyObject") **result)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.13._

This is the same as `PyMapping_GetOptionalItem()`, but _key_ is specified as a const char* UTF-8 encoded bytes string, rather than a [PyObject](structures.md#c.PyObject "PyObject")*.

在 3.13 版本加入.

int PyMapping_SetItemString([PyObject](structures.md#c.PyObject "PyObject") *o, const char *key, [PyObject](structures.md#c.PyObject "PyObject") *v)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这与 [`PyObject_SetItem()`](object.md#c.PyObject_SetItem "PyObject_SetItem") 相同，但 _key_ 被指定为 const char* UTF-8 编码的字节串，而不是 [PyObject](structures.md#c.PyObject "PyObject")*。

int PyMapping_DelItem([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *key)¶  

    

~~~
这是 [`PyObject_DelItem()`](object.md#c.PyObject_DelItem "PyObject_DelItem") 的一个别名。

int PyMapping_DelItemString([PyObject](structures.md#c.PyObject "PyObject") *o, const char *key)¶  
~~~
    

~~~
