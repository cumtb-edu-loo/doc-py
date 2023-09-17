# 在堆上分配对象¶

[PyObject](structures.md#c.PyObject "PyObject") *_PyObject_New([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type)¶  

    _返回值：新的引用。_

[PyVarObject](structures.md#c.PyVarObject "PyVarObject") *_PyObject_NewVar([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size)¶  

    _返回值：新的引用。_

[PyObject](structures.md#c.PyObject "PyObject") *PyObject_Init([PyObject](structures.md#c.PyObject "PyObject") *op, [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

用它的类型和初始引用来初始化新分配对象 _op_ 。返回已初始化的对象。如果 _type_ 明该对象参与循环垃圾检测器，则将其添加到检测器的观察对象集中。 对象的其他字段不受影响。

[PyVarObject](structures.md#c.PyVarObject "PyVarObject") *PyObject_InitVar([PyVarObject](structures.md#c.PyVarObject "PyVarObject") *op, [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

它的功能和 `PyObject_Init()` 一样，并且会初始化变量大小对象的长度信息。

PyObject_New(TYPE, typeobj)¶  

    

~~~
使用 C 结构类型 _TYPE_ 和 Python 类型对象 _typeobj_ (`PyTypeObject*`) 分配一个新的 Python 对象。 f未在该 Python 对象标头中定义的字段不会被初始化。 调用方将拥有对该对象的唯一引用（即引用计数将为 1）。 内存分配的大小由类型对象的 [`tp_basicsize`](typeobj.md#c.PyTypeObject.tp_basicsize "PyTypeObject.tp_basicsize") 字段决定。

PyObject_NewVar(TYPE, typeobj, size)¶  
~~~
    

~~~
使用 C 结构类型 _TYPE_ 和 Python 类型对象 _typeobj_ (`PyTypeObject*`) 分配一个新的 Python 对象。 未在该 Python 对象标头中定义的字段不会被初始化。 被分配的内存允许 _TYPE_ 结构加 _typeobj_ 的 [`tp_itemsize`](typeobj.md#c.PyTypeObject.tp_itemsize "PyTypeObject.tp_itemsize") 字段所给出的 _size_ (`Py_ssize_t`) 个字段。 这对于实现像元组这样能够在构造时确定其大小的对象来说很有用。 将字段数组嵌入到相同的内在分配中可减少内存分配的次数，这提高了内存管理效率。

void PyObject_Del(void *op)¶  
~~~
    

~~~
释放使用 `PyObject_New` 或 `PyObject_NewVar` 分配给一个对象的内存。 这通常由在对象的类型中指定的 [`tp_dealloc`](typeobj.md#c.PyTypeObject.tp_dealloc "PyTypeObject.tp_dealloc") 处理句柄来调用。 在此调用之后该对象中的字段不应再被访问因为原来的内存已不再是一个有效的 Python 对象。

[PyObject](structures.md#c.PyObject "PyObject") _Py_NoneStruct¶  
~~~
    

~~~
这个对象是像 `None` 一样的 Python 对象。它可以使用 [`Py_None`](none.md#c.Py_None "Py_None") 宏访问，该宏的拿到指向该对象的指针。

参见

[`PyModule_Create()`](module.md#c.PyModule_Create "PyModule_Create")
~~~
    

~~~
