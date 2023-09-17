# 切片对象¶

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PySlice_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

切片对象的类型对象。 它与 Python 层面的 [`slice`](../library/functions.md#slice "slice") 是相同的对象。

int PySlice_Check([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  

    

~~~
如果 _ob_ 是一个 slice 对象则返回真值； _ob_ 必须不为 `NULL`。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PySlice_New([PyObject](structures.md#c.PyObject "PyObject") *start, [PyObject](structures.md#c.PyObject "PyObject") *stop, [PyObject](structures.md#c.PyObject "PyObject") *step)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回一个具有给定值的新切片对象。 _start_ , _stop_ 和 _step_ 形参会被用作 slice 对象相应名称的属性的值。 这些值中的任何一个都可以为 `NULL`，在这种情况下将使用 `None` 作为对应属性的值。 如果新对象无法被分配则返回 `NULL`。

int PySlice_GetIndices([PyObject](structures.md#c.PyObject "PyObject") *slice, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") length, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *start, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *stop, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *step)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

从切片对象 _slice_ 提取 start, stop 和 step 索引号，将序列长度视为 _length_ 。 大于 _length_ 的序列号将被当作错误。

成功时返回 `0`，出错时返回 `-1` 并且不设置异常（除非某个索引号不为 `None` 且无法被转换为整数，在这种情况下将返回 `-1` 并且设置一个异常）。

你可能不会打算使用此函数。

在 3.2 版本发生变更: 之前 _slice_ 形参的形参类型是 `PySliceObject*`。

int PySlice_GetIndicesEx([PyObject](structures.md#c.PyObject "PyObject") *slice, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") length, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *start, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *stop, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *step, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *slicelength)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

`PySlice_GetIndices()` 的可用替代。 从切片对象 _slice_ 提取 start, stop 和 step 索引号，将序列长度视为 _length_ ，并将切片的长度保存在 _slicelength_ 中，超出范围的索引号会以与普通切片一致的方式进行剪切。

成功时返回 `0`，出错时返回 `-1` 并且不设置异常。

备注

此函数对于可变大小序列来说是不安全的。 对它的调用应被替换为 `PySlice_Unpack()` 和 `PySlice_AdjustIndices()` 的组合，其中
~~~
    
    
~~~
if (PySlice_GetIndicesEx(slice, length, &start, &stop, &step, &slicelength) < 0) {
    // return error
}
~~~

会被替换为

    
    
~~~
if (PySlice_Unpack(slice, &start, &stop, &step) < 0) {
    // return error
}
slicelength = PySlice_AdjustIndices(length, &start, &stop, step);
~~~

在 3.2 版本发生变更: 之前 _slice_ 形参的形参类型是 `PySliceObject*`。

在 3.6.1 版本发生变更: 如果 `Py_LIMITED_API` 未设置或设置为 `0x03050400` 与 `0x03060000` 之间的值（不包括边界）或 `0x03060100` 或更大则 `PySlice_GetIndicesEx()` 会被实现为一个使用 `PySlice_Unpack()` 和 `PySlice_AdjustIndices()` 的宏。 参数 _start_ , _stop_ 和 _step_ 会被多被求值。

自 3.6.1 版本弃用: 如果 `Py_LIMITED_API` 设置为小于 `0x03050400` 或 `0x03060000` 与 `0x03060100` 之间的值（不包括边界）则 `PySlice_GetIndicesEx()` 为已弃用的函数。

int PySlice_Unpack([PyObject](structures.md#c.PyObject "PyObject") *slice, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *start, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *stop, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *step)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

从切片对象中将 start, stop 和 step 数据成员提取为 C 整数。 会静默地将大于 `PY_SSIZE_T_MAX` 的值减小为 `PY_SSIZE_T_MAX`，静默地将小于 `PY_SSIZE_T_MIN` 的 start 和 stop 值增大为 `PY_SSIZE_T_MIN`，并静默地将小于 `-PY_SSIZE_T_MAX` 的 step 值增大为 `-PY_SSIZE_T_MAX`。

出错时返回 `-1`，成功时返回 `0`。

在 3.6.1 版本加入.

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PySlice_AdjustIndices([Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") length, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *start, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *stop, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") step)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

将 start/end 切片索引号根据指定的序列长度进行调整。 超出范围的索引号会以与普通切片一致的方式进行剪切。

返回切片的长度。 此操作总是会成功。 不会调用 Python 代码。

在 3.6.1 版本加入.

## Ellipsis 对象¶

[PyObject](structures.md#c.PyObject "PyObject") *Py_Ellipsis¶  

    

~~~
