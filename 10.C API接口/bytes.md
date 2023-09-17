# bytes 对象¶

这些函数在期望附带一个字节串形参但却附带了一个非字节串形参被调用时会引发 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError")。

type PyBytesObject¶  

    

~~~
这种 [`PyObject`](structures.md#c.PyObject "PyObject") 的子类型表示一个 Python 字节对象。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyBytes_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

[`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的实例代表一个 Python 字节类型，在 Python 层面它与 [`bytes`](../library/stdtypes.md#bytes "bytes") 是相同的对象。

int PyBytes_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果对象 _o_ 是一个 bytes 对象或者 bytes 类型的子类型的实例则返回真值。 此函数总是会成功执行。

int PyBytes_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果对象 _o_ 是一个 bytes 对象但不是 bytes 类型的子类型的实例则返回真值。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PyBytes_FromString(const char *v)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回一个以字符串 _v_ 的副本为值的新字节串对象，失败时返回 `NULL`。 形参 _v_ 不可为 `NULL`；它不会被检查。

[PyObject](structures.md#c.PyObject "PyObject") *PyBytes_FromStringAndSize(const char *v, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") len)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回一个以字符串 _v_ 的副本为值且长度为 _len_ 的新字节串对象，失败时返回 `NULL`。 如果 _v_ 为 `NULL`，则不初始化字节串对象的内容。

[PyObject](structures.md#c.PyObject "PyObject") *PyBytes_FromFormat(const char *format, ...)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

接受一个 C `printf()` 风格的 _format_ 字符串和可变数量的参数，计算结果 Python 字节串对象的大小并返回参数值经格式化后的字节串对象。 可变数量的参数必须均为 C 类型并且必须恰好与 _format_ 字符串中的格式字符相对应。 允许使用下列格式字符串:

格式字符

|

类型

|

注释  
  
---|---|---  
  
`%%`

|

_不适用_

|

文字%字符。  
  
`%c`

|

int

|

一个字节，被表示为一个 C 语言的整型  
  
`%d`

|

int

|

相当于 `printf("%d")`. [1]  
  
`%u`

|

unsigned int

|

相当于 `printf("%u")`. [1]  
  
`%ld`

|

长整型

|

相当于 `printf("%ld")`. [1]  
  
`%lu`

|

unsigned long

|

相当于 `printf("%lu")`. [1]  
  
`%zd`

|

[`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t")

|

相当于 `printf("%zd")`. [1]  
  
`%zu`

|

size_t

|

相当于 `printf("%zu")`. [1]  
  
`%i`

|

int

|

相当于 `printf("%i")`. [1]  
  
`%x`

|

int

|

相当于 `printf("%x")`. [1]  
  
`%s`

|

const char*

|

以 null 为终止符的 C 字符数组。  
  
`%p`

|

const void*

|

一个 C 指针的十六进制表示形式。 基本等价于 `printf("%p")` 但它会确保以字面值 `0x` 开头，不论系统平台上 `printf` 的输出是什么。  
  
无法识别的格式字符会导致将格式字符串的其余所有内容原样复制到结果对象，并丢弃所有多余的参数。

[1] (1,2,3,4,5,6,7,8)

对于整数说明符（d，u，ld，lu，zd，zu，i，x）：当给出精度时，0 转换标志是有效的。

[PyObject](structures.md#c.PyObject "PyObject") *PyBytes_FromFormatV(const char *format, va_list vargs)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

与 `PyBytes_FromFormat()` 完全相同，除了它需要两个参数。

[PyObject](structures.md#c.PyObject "PyObject") *PyBytes_FromObject([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回字节表示实现缓冲区协议的对象*o*。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyBytes_Size([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回字节对象*o*中字节的长度。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyBytes_GET_SIZE([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
类似于 `PyBytes_Size()`，但是不带错误检测。

char *PyBytes_AsString([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回对应 _o_ 的内容的指针。 该指针指向 _o_ 的内部缓冲区，其中包含 `len(o) + 1` 个字节。 缓冲区的最后一个字节总是为空，不论是否存在其他空字节。 该数据不可通过任何形式来修改，除非是刚使用 `PyBytes_FromStringAndSize(NULL, size)` 创建该对象。 它不可被撤销分配。 如果 _o_ 根本不是一个字节串对象，则 `PyBytes_AsString()` 将返回 `NULL` 并引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError")。

char *PyBytes_AS_STRING([PyObject](structures.md#c.PyObject "PyObject") *string)¶  
~~~
    

~~~
类似于 `PyBytes_AsString()`，但是不带错误检测。

int PyBytes_AsStringAndSize([PyObject](structures.md#c.PyObject "PyObject") *obj, char **buffer, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *length)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

通过输出变量 _buffer_ 和 _length_ 返回以 null 为终止符的对象 _obj_ 的内容。

如果 _length_ 为 `NULL`，字节串对象就不包含嵌入的空字节；如果包含，则该函数将返回 `-1` 并引发 [`ValueError`](../library/exceptions.md#ValueError "ValueError")。

该缓冲区指向 _obj_ 的内部缓冲，它的末尾包含一个额外的空字节（不算在 _length_ 当中）。 该数据不可通过任何方式来修改，除非是刚使用 `PyBytes_FromStringAndSize(NULL, size)` 创建该对象。 它不可被撤销分配。 如果 _obj_ 根本不是一个字节串对象，则 `PyBytes_AsStringAndSize()` 将返回 `-1` 并引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError")。

在 3.5 版本发生变更: 以前，当字节串对象中出现嵌入的空字节时将引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError")。

void PyBytes_Concat([PyObject](structures.md#c.PyObject "PyObject") **bytes, [PyObject](structures.md#c.PyObject "PyObject") *newpart)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

在 _*bytes_ 中创建新的字节串对象，其中包含添加到 _bytes_ 的 _newpart_ 的内容；调用者将获得新的引用。 对 _bytes_ 原值的引用将被收回。 如果无法创建新对象，对 _bytes_ 的旧引用仍将被丢弃且 _*bytes_ 的值将被设为 `NULL`；并将设置适当的异常。

void PyBytes_ConcatAndDel([PyObject](structures.md#c.PyObject "PyObject") **bytes, [PyObject](structures.md#c.PyObject "PyObject") *newpart)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

在 _*bytes_ 中创建一个新的字节串对象，其中包含添加到 _bytes_ 的 _newpart_ 的内容。 此版本将释放对 _newpart_ 的 [strong reference](../glossary.md#term-strong-reference) (即递减其引用计数)。

int _PyBytes_Resize([PyObject](structures.md#c.PyObject "PyObject") **bytes, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") newsize)¶  
~~~
    

~~~
