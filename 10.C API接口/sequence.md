# 序列协议¶

int PySequence_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果对象提供了序列协议则返回 `1`，否则返回 `0`。 请注意对于具有 [`__getitem__()`](datamodel.md#object.__getitem__ "object.__getitem__") 方法的 Python 类返回 `1` ，除非它们是 [`dict`](stdtypes.md#dict "dict") 的子类，因为在通常情况下无法确定这种类支持哪种键类型。 该函数总是会成功执行。

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") PySequence_Size([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") PySequence_Length([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

成功时返回序列中*o*的对象数,失败时返回 `-1` . 相当于Python的 `len(o)` 表达式.

[PyObject](structures.md#c.PyObject "PyObject") *PySequence_Concat([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回 _o1_ 和 _o2_ 的拼接，失败时返回 `NULL`。 这等价于 Python 表达式 `o1 + o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PySequence_Repeat([PyObject](structures.md#c.PyObject "PyObject") *o, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") count)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回序列对象 _o_ 重复 _count_ 次的结果，失败时返回 `NULL`。 这等价于 Python 表达式 `o * count`。

[PyObject](structures.md#c.PyObject "PyObject") *PySequence_InPlaceConcat([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回 _o1_ 和 _o2_ 的拼接，失败时返回 `NULL`。 在 _o1_ 支持的情况下操作将 _原地_ 完成。 这等价于 Python 表达式 `o1 += o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PySequence_InPlaceRepeat([PyObject](structures.md#c.PyObject "PyObject") *o, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") count)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Return the result of repeating sequence object返回序列对象 _o_ 重复 _count_ 次的结果，失败时返回 `NULL`。 在 _o_ 支持的情况下该操作会 _原地_ 完成。 这等价于 Python 表达式 `o *= count`。

[PyObject](structures.md#c.PyObject "PyObject") *PySequence_GetItem([PyObject](structures.md#c.PyObject "PyObject") *o, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") i)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o_ 中的第 _i_ 号元素，失败时返回 `NULL`。 这等价于 Python 表达式 `o[i]`。

[PyObject](structures.md#c.PyObject "PyObject") *PySequence_GetSlice([PyObject](structures.md#c.PyObject "PyObject") *o, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") i1, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") i2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回序列对象 _o_ 的 _i1_ 到 _i2_ 的切片，失败时返回 `NULL`。 这等价于 Python 表达式 `o[i1:i2]`。

int PySequence_SetItem([PyObject](structures.md#c.PyObject "PyObject") *o, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") i, [PyObject](structures.md#c.PyObject "PyObject") *v)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将对象 _v_ 赋值给 _o_ 的第 _i_ 号元素。 失败时会引发异常并返回 `-1`；成功时返回 `0`。 这相当于 Python 语句 `o[i] = v`。 此函数 _不会_ 改变对 _v_ 的引用。

如果 _v_ 为 `NULL`，元素将被删除，但是此特性已被弃用而应改用 `PySequence_DelItem()`。

int PySequence_DelItem([PyObject](structures.md#c.PyObject "PyObject") *o, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") i)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

删除对象 _o_ 的第 _i_ 号元素。 失败时返回 `-1`。 这相当于 Python 语句 `del o[i]`。

int PySequence_SetSlice([PyObject](structures.md#c.PyObject "PyObject") *o, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") i1, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") i2, [PyObject](structures.md#c.PyObject "PyObject") *v)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将序列对象 _v_ 赋值给序列对象 _o_ 的从 _i1_ 到 _i2_ 切片。 这相当于 Python 语句 `o[i1:i2] = v`。

int PySequence_DelSlice([PyObject](structures.md#c.PyObject "PyObject") *o, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") i1, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") i2)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

删除序列对象 _o_ 的从 _i1_ 到 _i2_ 的切片。 失败时返回 `-1`。 这相当于 Python 语句 `del o[i1:i2]`。

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") PySequence_Count([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _value_ 在 _o_ 中出现的次数，即返回使得 `o[key] == value` 的键的数量。 失败时返回 `-1`。 这相当于 Python 表达式 `o.count(value)`。

int PySequence_Contains([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

确定 _o_ 是否包含 _value_ 。 如果 _o_ 中的某一项等于 _value_ ，则返回 `1`，否则返回 `0`。 出错时，返回 `-1`。 这相当于 Python 表达式 `value in o`。

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") PySequence_Index([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回第一个索引*i*,其中 `o[i] == value` .出错时,返回 `-1` .相当于Python的 `o.index(value)` 表达式.

[PyObject](structures.md#c.PyObject "PyObject") *PySequence_List([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回一个列表对象，其内容与序列或可迭代对象 _o_ 相同，失败时返回 `NULL`。 返回的列表保证是一个新对象。 这等价于 Python 表达式 `list(o)`。

[PyObject](structures.md#c.PyObject "PyObject") *PySequence_Tuple([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回一个元组对象，其内容与序列或可迭代对象 _o_ 相同，失败时返回 `NULL`。 如果 _o_ 为元组，则将返回一个新的引用，在其他情况下将使用适当的内容构造一个元组。 这等价于 Python 表达式 `tuple(o)`。

[PyObject](structures.md#c.PyObject "PyObject") *PySequence_Fast([PyObject](structures.md#c.PyObject "PyObject") *o, const char *m)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

将序列或可迭代对象 _o_ 作为其他 `PySequence_Fast*` 函数族可用的对象返回。 如果该对象不是序列或可迭代对象，则会引发 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError") 并将 _m_ 作为消息文本。 失败时返回 `NULL`。

`PySequence_Fast*` 函数之所以这样命名，是因为它们会假定 _o_ 是一个 [`PyTupleObject`](tuple.md#c.PyTupleObject "PyTupleObject") 或 [`PyListObject`](list.md#c.PyListObject "PyListObject") 并直接访问 _o_ 的数据字段。

作为 CPython 的实现细节，如果 _o_ 已经是一个序列或列表，它将被直接返回。

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") PySequence_Fast_GET_SIZE([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    

~~~
在 _o_ 由 `PySequence_Fast()` 返回且 _o_ 不为 `NULL` 的情况下返回 _o_ 长度。 也可以通过在 _o_ 上调用 `PySequence_Size()` 来获取大小，但是 `PySequence_Fast_GET_SIZE()` 的速度更快因为它可以假定 _o_ 为列表或元组。

[PyObject](structures.md#c.PyObject "PyObject") *PySequence_Fast_GET_ITEM([PyObject](structures.md#c.PyObject "PyObject") *o, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") i)¶  

    _返回值：借入的引用。_

在 _o_ 由 `PySequence_Fast()` 返回且 _o_ 不 `NULL`，并且 _i_ d在索引范围内的情况下返回 _o_ 的第 _i_ 号元素。

[PyObject](structures.md#c.PyObject "PyObject") **PySequence_Fast_ITEMS([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
