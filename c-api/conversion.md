# 字符串转换与格式化¶

用于数字转换和格式化字符串输出的函数

int PyOS_snprintf(char *str, size_t size, const char *format, ...)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

根据格式字符串 _format_ 和额外参数，输出不超过 _size_ 个字节到 _str_ 。 参见 Unix 手册页面 _[snprintf(3)](https://manpages.debian.org/snprintf\(3\))_ 。

int PyOS_vsnprintf(char *str, size_t size, const char *format, va_list va)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

根据格式字符串 _format_ 和变量参数列表 _va_ ，输出不超过 _size_ 个字节到 _str_ 。 参见 Unix 手册页面 _[vsnprintf(3)](https://manpages.debian.org/vsnprintf\(3\))_ 。

`PyOS_snprintf()` 和 `PyOS_vsnprintf()` 包装 C 标准库函数 `snprintf()` 和 `vsnprintf()` 。它们的目的是保证在极端情况下的一致行为，而标准 C 的函数则不然。

此包装器会确保 `str[size-1]` 在返回时始终为 `'\0'`。 它们从不写入超过 _size_ 字节 (包括末尾的 `'\0'`) 到 str。 两个函数都要求 `str != NULL`, `size > 0`, `format != NULL` 且 `size < INT_MAX`。 请注意这意味着不存在可确定所需缓冲区大小的 C99 `n = snprintf(NULL, 0, ...)` 的等价物。

这些函数的返回值（ _rv_ ）应按照以下规则被解释：

  * 当 `0 <= rv < size` 时，输出转换即成功并将 _rv_ 个字符写入到 _str_ (不包括末尾 `str[rv]` 位置的 `'\0'` 字节)。

  * 当 `rv >= size` 时，输出转换会被截断并且需要一个具有 `rv + 1` 字节的缓冲区才能成功执行。 在此情况下 `str[size-1]` 为 `'\0'`。

  * 当 `rv < 0` 时，"会发生不好的事情。" 在此情况下 `str[size-1]` 也为 `'\0'`，但 _str_ 的其余部分是未定义的。 错误的确切原因取决于底层平台。

以下函数提供与语言环境无关的字符串到数字转换。

double PyOS_string_to_double(const char *s, char **endptr, [PyObject](structures.md#c.PyObject "PyObject") *overflow_exception)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将字符串 `s` 转换为 double 类型，失败时会引发 Python 异常。 接受的字符串集合对应于可被 Python 的 [`float()`](../library/functions.md#float "float") 构造器所接受的字符集集合，除了 `s` 必须没有前导或尾随空格。 转换必须独立于当前的语言区域。

如果 `endptr` 是 `NULL` ，转换整个字符串。引发 [`ValueError`](../library/exceptions.md#ValueError "ValueError") 并且 返回 `-1.0` 如果字符串不是浮点数的有效的表达方式。

如果 `endptr` 不是 `NULL` ，尽可能多的转换字符串并将 `*endptr` 设置为指向第一个未转换的字符。如果字符串的初始段不是浮点数的有效的表达方式，将 `*endptr` 设置为指向字符串的开头，引发 ValueError 异常，并且返回 `-1.0` 。

如果 `s` 表示一个太大而不能存储在一个浮点数中的值（比方说， `"1e500"` 在许多平台上是一个字符串）然后如果 `overflow_exception` 是 `NULL` 返回 `Py_HUGE_VAL` （用适当的符号）并且不设置任何异常。 在其他方面， `overflow_exception` 必须指向一个 Python 异常对象；引发异常并返回 `-1.0` 。在这两种情况下，设置 `*endptr` 指向转换值之后的第一个字符。

如果在转换期间发生任何其他错误（比如一个内存不足的错误），设置适当的 Python 异常并且返回 `-1.0` 。

在 3.1 版本加入.

char *PyOS_double_to_string(double val, char format_code, int precision, int flags, int *ptype)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将 double _val_ 转换为一个使用给定的 _format_code_ , _precision_ 和 _flags_ 的字符串。

_格式码_ 必须是以下其中之一, `'e'`, `'E'`, `'f'`, `'F'`, `'g'`, `'G'` 或者 `'r'`。对于 `'r'` , 提供的 _精度_ 必须是0。`'r'` 格式码指定了标准函数 [`repr()`](../library/functions.md#repr "repr") 格式。

_flags_ 可以为零或者其他值 `Py_DTSF_SIGN`, `Py_DTSF_ADD_DOT_0` 或 `Py_DTSF_ALT` 或其组合：

  * `Py_DTSF_SIGN` 表示总是在返回的字符串前附加一个符号字符，即使 _val_ 为非负数。

  * `Py_DTSF_ADD_DOT_0` 表示确保返回的字符串看起来不像是一个整数。

  * `Py_DTSF_ALT` 表示应用 "替代的" 格式化规则。 相关细节请参阅 `PyOS_snprintf()` `'#'` 定义文档。

如果 _ptype_ 不为 `NULL`，则它指向的值将被设为 `Py_DTST_FINITE`, `Py_DTST_INFINITE` 或 `Py_DTST_NAN` 中的一个，分别表示 _val_ 是一个有限数字、无限数字或非数字。

返回值是一个指向包含转换后字符串的 _buffer_ 的指针，如果转换失败则为 `NULL`。 调用方要负责调用 [`PyMem_Free()`](memory.md#c.PyMem_Free "PyMem_Free") 来释放返回的字符串。

在 3.1 版本加入.

int PyOS_stricmp(const char *s1, const char *s2)¶  

    

~~~
不区分大小写的字符串比较。 除了忽略大小写之外，该函数的工作方式与 `strcmp()` 相同。

int PyOS_strnicmp(const char *s1, const char *s2, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size)¶  
~~~
    

~~~
