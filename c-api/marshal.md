# 数据 marshal 操作支持¶

这些例程允许 C 代码处理与 [`marshal`](../library/marshal.md#module-marshal "marshal: Convert Python objects to streams of bytes and back \(with different constraints\).") 模块所用相同数据格式的序列化对象。 其中有些函数可用来将数据写入这种序列化格式，另一些函数则可用来读取并恢复数据。 用于存储 marshal 数据的文件必须以二进制模式打开。

数字值在存储时会将最低位字节放在开头。

此模块支持两种数据格式版本：第 0 版为历史版本，第 1 版本会在文件和 marshal 反序列化中共享固化的字符串。 第 2 版本会对浮点数使用二进制格式。 `Py_MARSHAL_VERSION` 指明了当前文件的格式（当前取值为 2）。

void PyMarshal_WriteLongToFile(long value, FILE *file, int version)¶  

    

~~~
将一个 long 整数 _value_ 以 marshal 格式写入 _file_ 。 这将只写入 _value_ 中最低的 32 个比特位；无论本机的 long 类型的大小如何。 _version_ 指明文件格式的版本。

此函数可能失败，在这种情况下它半设置错误提示符。 请使用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 进行检测。

void PyMarshal_WriteObjectToFile([PyObject](structures.md#c.PyObject "PyObject") *value, FILE *file, int version)¶  
~~~
    

~~~
将一个 Python 对象 _value_ 以 marshal 格式写入 _file_ 。 _version_ 指明文件格式的版本。

此函数可能失败，在这种情况下它半设置错误提示符。 请使用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 进行检测。

[PyObject](structures.md#c.PyObject "PyObject") *PyMarshal_WriteObjectToString([PyObject](structures.md#c.PyObject "PyObject") *value, int version)¶  

    _返回值：新的引用。_

返回一个包含 _value_ 的 marshal 表示形式的字节串对象。 _version_ 指明文件格式的版本。

以下函数允许读取并恢复存储为 marshal 格式的值。

long PyMarshal_ReadLongFromFile(FILE *file)¶  
~~~
    

~~~
从打开用于读取的 FILE* 对应的数据流返回一个 C long。 使用此函数只能读取 32 位的值，无论本机 long 类型的大小如何。

发生错误时，将设置适当的异常 ([`EOFError`](../library/exceptions.md#EOFError "EOFError")) 并返回 `-1`。

int PyMarshal_ReadShortFromFile(FILE *file)¶  
~~~
    

~~~
