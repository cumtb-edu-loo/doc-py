# 列表对象¶

type PyListObject¶  

    

~~~
这个C类型 [`PyObject`](structures.md#c.PyObject "PyObject") 的子类型代表一个Python列表对象。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyList_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是个属于 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的代表Python列表类型的实例。在Python层面和类型 [`list`](../library/stdtypes.md#list "list") 是同一个对象。

int PyList_Check([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果 _p_ 是一个 list 对象或者 list 类型的子类型的实例则返回真值。 此函数总是会成功执行。

int PyList_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果 _p_ 是一个 list 对象但不是 list 类型的子类型的实例则返回真值。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PyList_New([Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") len)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回一个长度为 _len_ 的新列表，失败时返回 `NULL`。

备注

当 _len_ 大于零时，被返回的列表对象项目被设成 `NULL`。因此你不能用类似C函数 [`PySequence_SetItem()`](sequence.md#c.PySequence_SetItem "PySequence_SetItem") 的抽象API或者用C函数 `PyList_SetItem()` 将所有项目设置成真实对象前对Python代码公开这个对象。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyList_Size([PyObject](structures.md#c.PyObject "PyObject") *list)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _list_ 中列表对象的长度；这等于在列表对象调用 `len(list)` 。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyList_GET_SIZE([PyObject](structures.md#c.PyObject "PyObject") *list)¶  
~~~
    

~~~
类似于 `PyList_Size()`，但是不带错误检测。

[PyObject](structures.md#c.PyObject "PyObject") *PyList_GetItem([PyObject](structures.md#c.PyObject "PyObject") *list, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") index)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _list_ 所指向列表中 _index_ 位置上的对象。 位置值必须为非负数；不支持从列表末尾进行索引。 如果 _index_ 超出边界 (<0 or >=len(list))，则返回 `NULL` 并设置 [`IndexError`](../library/exceptions.md#IndexError "IndexError") 异常。

[PyObject](structures.md#c.PyObject "PyObject") *PyList_GET_ITEM([PyObject](structures.md#c.PyObject "PyObject") *list, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") i)¶  

    _返回值：借入的引用。_

类似于 `PyList_GetItem()`，但是不带错误检测。

int PyList_SetItem([PyObject](structures.md#c.PyObject "PyObject") *list, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") index, [PyObject](structures.md#c.PyObject "PyObject") *item)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将列表中索引为 _index_ 的项设为 _item_ 。 成功时返回 `0`。 如果 _index_ 超出范围则返回 `-1` 并设定 [`IndexError`](../library/exceptions.md#IndexError "IndexError") 异常。

备注

此函数会“偷走”一个对 _item_ 的引用并丢弃一个对列表中受影响位置上的已有条目的引用。

void PyList_SET_ITEM([PyObject](structures.md#c.PyObject "PyObject") *list, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") i, [PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
