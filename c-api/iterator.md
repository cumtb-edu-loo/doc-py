# 迭代器对象¶

Python 提供了两个通用迭代器对象。 第一个是序列迭代器，它可与支持 [`__getitem__()`](../reference/datamodel.md#object.__getitem__ "object.__getitem__") 方法的任意序列一起使用。 第二个迭代器使用一个可调用对象和一个哨兵值，为序列中的每个项目调用可调用对象，并在返回哨兵值时结束迭代。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PySeqIter_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

`PySeqIter_New()` 返回迭代器对象的类型对象和内置序列类型内置函数 [`iter()`](../library/functions.md#iter "iter") 的单参数形式。

int PySeqIter_Check([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    

~~~
如果 _op_ 的类型为 `PySeqIter_Type` 则返回真值。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PySeqIter_New([PyObject](structures.md#c.PyObject "PyObject") *seq)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回一个与常规序列对象一起使用的迭代器 _seq_ 。 当序列订阅操作引发 [`IndexError`](../library/exceptions.md#IndexError "IndexError") 时，迭代结束。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyCallIter_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

由函数 `PyCallIter_New()` 和 [`iter()`](../library/functions.md#iter "iter") 内置函数的双参数形式返回的迭代器对象类型对象。

int PyCallIter_Check([PyObject](structures.md#c.PyObject "PyObject") *op)¶  
~~~
    

~~~
