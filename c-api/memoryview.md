# MemoryView 对象¶

一个 [`memoryview`](../library/stdtypes.md#memoryview "memoryview") 对象C级别的 [缓冲区接口](buffer.md#bufferobjects) 暴露为一个可以像任何其他对象一样传递的 Python 对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyMemoryView_FromObject([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

从提供缓冲区接口的对象创建 memoryview 对象。 如果 _obj_ 支持可写缓冲区导出，则 memoryview 对象将可以被读/写，否则它可能是只读的，也可以是导出器自行决定的读/写。

[PyObject](structures.md#c.PyObject "PyObject") *PyMemoryView_FromMemory(char *mem, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size, int flags)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

使用 _mem_ 作为底层缓冲区创建一个 memoryview 对象。 _flags_ 可以是 `PyBUF_READ` 或者 `PyBUF_WRITE` 之一.

在 3.3 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyMemoryView_FromBuffer(const [Py_buffer](buffer.md#c.Py_buffer "Py_buffer") *view)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

创建一个包含给定缓冲区结构 _view_ 的 memoryview 对象。 对于简单的字节缓冲区，`PyMemoryView_FromMemory()` 是首选函数。

[PyObject](structures.md#c.PyObject "PyObject") *PyMemoryView_GetContiguous([PyObject](structures.md#c.PyObject "PyObject") *obj, int buffertype, char order)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

从定义缓冲区接口的对象创建一个 memoryview 对象 [contiguous](../glossary.md#term-contiguous) 内存块（在 'C' 或 'F'ortran _order_ 中）。 如果内存是连续的，则 memoryview 对象指向原始内存。 否则，复制并且 memoryview 指向新的 bytes 对象。

int PyMemoryView_Check([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  

    

~~~
如果 _obj_ 是一个 memoryview 对象则返回真值。 目前不允许创建 [`memoryview`](../library/stdtypes.md#memoryview "memoryview") 的子类。 此函数总是会成功执行。

[Py_buffer](buffer.md#c.Py_buffer "Py_buffer") *PyMemoryView_GET_BUFFER([PyObject](structures.md#c.PyObject "PyObject") *mview)¶  
~~~
    

~~~
返回指向 memoryview 的导出缓冲区私有副本的指针。 _mview_ **必须** 是一个 memoryview 实例；这个宏不检查它的类型，你必须自己检查，否则你将面临崩溃风险。

[PyObject](structures.md#c.PyObject "PyObject") *PyMemoryView_GET_BASE([PyObject](structures.md#c.PyObject "PyObject") *mview)¶  
~~~
    

~~~
