# 布尔对象¶

在 Python 中布尔值是作为整数的子类实现的。 只有两个布尔值，`Py_False` 和 `Py_True`。 因此，正常的创建和删除功能不适用于布尔值。 不过，下列的宏则是可用的。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyBool_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的实例代表一个 Python 布尔类型；它与 Python 层面的 [`bool`](../library/functions.md#bool "bool") 是相同的对象。

int PyBool_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    

~~~
如果 _o_ 的类型为 `PyBool_Type` 则返回真值。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *Py_False¶  
~~~
    

~~~
Python `False` 对象。 该对象没有任何方法并且是 [永久性对象](https://peps.python.org/pep-0683/)。

在 3.12 版本发生变更: `Py_False` 是永久性对象。

[PyObject](structures.md#c.PyObject "PyObject") *Py_True¶  
~~~
    

~~~
Python `True` 对象。 该对象没有任何方法并且是 [永久性对象](https://peps.python.org/pep-0683/)。

在 3.12 版本发生变更: `Py_True` 是永久性对象。

Py_RETURN_FALSE¶  
~~~
    

~~~
从一个函数返回 `Py_False`。

Py_RETURN_TRUE¶  
~~~
    

~~~
