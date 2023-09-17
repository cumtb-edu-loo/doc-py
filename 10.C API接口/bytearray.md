# 字节数组对象¶

type PyByteArrayObject¶  

    

~~~
这个 [`PyObject`](structures.md#c.PyObject "PyObject") 的子类型表示一个 Python 字节数组对象。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyByteArray_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Python bytearray 类型表示为 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的实例；这与Python层面的 [`bytearray`](../library/stdtypes.md#bytearray "bytearray") 是相同的对象。

## 类型检查宏¶

int PyByteArray_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果对象 _o_ 是一个 bytearray 对象或者 bytearray 类型的子类型的实例则返回真值。 此函数总是会成功执行。

int PyByteArray_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果对象 _o_ 是一个 bytearray 对象但不是 bytearray 类型的子类型的实例则返回真值。 此函数总是会成功执行。

## 直接 API 函数¶

[PyObject](structures.md#c.PyObject "PyObject") *PyByteArray_FromObject([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

根据任何实现了 [缓冲区协议](buffer.md#bufferobjects) 的对象 _o_ ，返回一个新的字节数组对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyByteArray_FromStringAndSize(const char *string, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") len)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

根据 _string_ 及其长度 _len_ 创建一个新的 bytearray 对象。 当失败时返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyByteArray_Concat([PyObject](structures.md#c.PyObject "PyObject") *a, [PyObject](structures.md#c.PyObject "PyObject") *b)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

连接字节数组 _a_ 和 _b_ 并返回一个带有结果的新的字节数组。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyByteArray_Size([PyObject](structures.md#c.PyObject "PyObject") *bytearray)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

在检查 `NULL` 指针后返回 _bytearray_ 的大小。

char *PyByteArray_AsString([PyObject](structures.md#c.PyObject "PyObject") *bytearray)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

在检查 `NULL` 指针后返回将 _bytearray_ 返回为一个字符数组。 返回的数组总是会附加一个额外的空字节。

int PyByteArray_Resize([PyObject](structures.md#c.PyObject "PyObject") *bytearray, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") len)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将 _bytearray_ 的内部缓冲区的大小调整为 _len_ 。

## 宏¶

这些宏减低安全性以换取性能，它们不检查指针。

char *PyByteArray_AS_STRING([PyObject](structures.md#c.PyObject "PyObject") *bytearray)¶  
~~~
    

~~~
类似于 `PyByteArray_AsString()`，但是不带错误检测。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyByteArray_GET_SIZE([PyObject](structures.md#c.PyObject "PyObject") *bytearray)¶  
~~~
    

~~~
