# 解析参数并构建值变量¶

在创建你自己的扩展函数和方法时，这些函数是有用的。其它的信息和样例见 [扩展和嵌入 Python 解释器](9.拓展和嵌入/index.md#extending-index) 。

这些函数描述的前三个，`PyArg_ParseTuple()`，`PyArg_ParseTupleAndKeywords()`，以及 `PyArg_Parse()`，它们都使用 _格式化字符串_ 来将函数期待的参数告知函数。这些函数都使用相同语法规则的格式化字符串。

## 解析参数¶

一个格式化字符串包含 0 或者更多的格式单元。一个格式单元用来描述一个 Python 对象；它通常是一个字符或者由括号括起来的格式单元序列。除了少数例外，一个非括号序列的格式单元通常对应这些函数的具有单一地址的参数。在接下来的描述中，双引号内的表达式是格式单元；圆括号 () 内的是对应这个格式单元的 Python 对象类型；方括号 [] 内的是传递的 C 变量(变量集)类型。

### 字符串和缓存区¶

备注

On Python 3.12 and older, the macro `PY_SSIZE_T_CLEAN` must be defined before including `Python.h` to use all `#` variants of formats (`s#`, `y#`, etc.) explained below. This is not necessary on Python 3.13 and later.

这些格式允许将对象按照连续的内存块形式进行访问。你没必要提供返回的 unicode 字符或者字节区的原始数据存储。

除非另有说明，缓冲区是不会以空终止的。

有三种办法可以将字符串和缓冲区转换到 C 类型:

  * 像 `y*` 和 `s*` 这样的格式会填充一个 [`Py_buffer`](buffer.md#c.Py_buffer "Py_buffer") 结构体。 这将锁定下层缓冲区以便调用者随后使用这个缓冲区即使是在 [`Py_BEGIN_ALLOW_THREADS`](init.md#c.Py_BEGIN_ALLOW_THREADS "Py_BEGIN_ALLOW_THREADS") 块中也不会有可变数据因大小调整或销毁所带来的风险。 因此，在你结束处理数据（或任何更早的中止场景）之前 **你必须调用** [`PyBuffer_Release()`](buffer.md#c.PyBuffer_Release "PyBuffer_Release")。

  * `es`, `es#`, `et` 和 `et#` 等格式会分配结果缓冲区。 在你结束处理数据（或任何更早的中止场景）之后 **你必须调用** [`PyMem_Free()`](memory.md#c.PyMem_Free "PyMem_Free")。

  * 其他格式接受一个 [`str`](stdtypes.md#str "str") 或只读的 [bytes-like object](../glossary.md#term-bytes-like-object)，如 [`bytes`](stdtypes.md#bytes "bytes")，并向其缓冲区提供一个 `const char *` 指针。 在缓冲区是“被借入”的情况下：它将由对应的 Python 对象来管理，并共享此对象的生命期。 你不需要自行释放任何内存。

为确保下层缓冲区可以安全地被借入，对象的 [`PyBufferProcs.bf_releasebuffer`](typeobj.md#c.PyBufferProcs.bf_releasebuffer "PyBufferProcs.bf_releasebuffer") 字段必须为 `NULL`。 这将不允许普通的可变对象如 [`bytearray`](stdtypes.md#bytearray "bytearray")，以及某些只读对象如 [`bytes`](stdtypes.md#bytes "bytes") 的 [`memoryview`](stdtypes.md#memoryview "memoryview")。

在这个 `bf_releasebuffer` 要求以外，没有用于验证输入对象是否为不可变对象的检查（例如它是否会接受可写缓冲区的请求，或者另一个线程是否能改变此数据）。

`s` ([`str`](stdtypes.md#str "str")) [const char *]

    

~~~
将一个 Unicode 对象转换成一个指向字符串的 C 指针。一个指针指向一个已经存在的字符串，这个字符串存储的是传如的字符指针变量。C 字符串是已空结束的。Python 字符串不能包含嵌入的无效的代码点；如果由，一个 [`ValueError`](../library/exceptions.md#ValueError "ValueError") 异常会被引发。Unicode 对象被转化成 `'utf-8'` 编码的 C 字符串。如果转换失败，一个 [`UnicodeError`](../library/exceptions.md#UnicodeError "UnicodeError") 异常被引发。

备注

这个表达式不接受 [bytes-like objects](../glossary.md#term-bytes-like-object)。如果你想接受文件系统路径并将它们转化成 C 字符串，建议使用 `O&` 表达式配合 [`PyUnicode_FSConverter()`](unicode.md#c.PyUnicode_FSConverter "PyUnicode_FSConverter") 作为 _转化函数_ 。

在 3.5 版本发生变更: 以前，当 Python 字符串中遇到了嵌入的 null 代码点会引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError") 。

`s*` ([`str`](../library/stdtypes.md#str "str") or [bytes-like object](../glossary.md#term-bytes-like-object)) [Py_buffer]
~~~
    

~~~
这个表达式既接受 Unicode 对象也接受类字节类型对象。它为由调用者提供的 [`Py_buffer`](buffer.md#c.Py_buffer "Py_buffer") 结构赋值。这里结果的 C 字符串可能包含嵌入的 NUL 字节。Unicode 对象通过 `'utf-8'` 编码转化成 C 字符串。

`s#` ([`str`](../library/stdtypes.md#str "str"), read-only [bytes-like object](../glossary.md#term-bytes-like-object)) [const char *, [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t")]
~~~
    

~~~
像是 `s*`，区别在于它提供了一个 借入的缓冲区。 结果存储在两个 C 变量中，第一个是指向 C 字符串的指针，第二个是其长度。 该字符串可能包含嵌入的空字节。 Unicode 对象会使用 `'utf-8'` 编码格式转换为 C 字符串。

`z` ([`str`](../library/stdtypes.md#str "str") or `None`) [const char *]
~~~
    

~~~
与 `s` 类似，但 Python 对象也可能为 `None`，在这种情况下，C 指针设置为 `NULL`。

`z*` ([`str`](../library/stdtypes.md#str "str"), [bytes-like object](../glossary.md#term-bytes-like-object) or `None`) [Py_buffer]
~~~
    

~~~
与 `s*` 类似，但 Python 对象也可能为 `None`，在这种情况下，[`Py_buffer`](buffer.md#c.Py_buffer "Py_buffer") 结构的 `buf` 成员设置为 `NULL`。

`z#` ([`str`](../library/stdtypes.md#str "str"), read-only [bytes-like object](../glossary.md#term-bytes-like-object) 或者 `None`) [const char *, [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t")]
~~~
    

~~~
与 `s#` 类似，但 Python 对象也可能为 `None`，在这种情况下，C 指针设置为 `NULL`。

`y` (read-only [bytes-like object](../glossary.md#term-bytes-like-object)) [const char *]
~~~
    

~~~
这个格式会将一个类字节对象转换为一个指向 借入的 字符串的 C 指针；它不接受 Unicode 对象。 字节缓冲区不可包含嵌入的空字节；如果包含这样的内容，将会引发 [`ValueError`](../library/exceptions.md#ValueError "ValueError") 异常。exception is raised.

在 3.5 版本发生变更: 以前，当字节缓冲区中遇到了嵌入的 null 字节会引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError") 。

`y*` ([bytes-like object](../glossary.md#term-bytes-like-object)) [Py_buffer]
~~~
    

~~~
`s*` 的变式，不接受 Unicode 对象，只接受类字节类型变量。 **这是接受二进制数据的推荐方法。**

`y#` (read-only [bytes-like object](../glossary.md#term-bytes-like-object)) [const char *, [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t")]
~~~
    

~~~
`s#` 的变式，不接受 Unicode 对象，只接受类字节类型变量。

`S` ([`bytes`](../library/stdtypes.md#bytes "bytes")) [PyBytesObject *]
~~~
    

~~~
要求 Python 对象为 [`bytes`](../library/stdtypes.md#bytes "bytes") 对象，不尝试进行任何转换。 如果该对象不为 bytes 对象则会引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError")。 C 变量也可被声明为 [PyObject](structures.md#c.PyObject "PyObject")*。

`Y` ([`bytearray`](../library/stdtypes.md#bytearray "bytearray")) [PyByteArrayObject *]
~~~
    

~~~
要求 Python 对象为 [`bytearray`](../library/stdtypes.md#bytearray "bytearray") 对象，不尝试进行任何转换。 如果该对象不为 [`bytearray`](../library/stdtypes.md#bytearray "bytearray") 对象则会引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError")。 C 变量也可被声明为 [PyObject](structures.md#c.PyObject "PyObject")*。

`U` ([`str`](../library/stdtypes.md#str "str")) [PyObject *]
~~~
    

~~~
要求 Python 对象为 Unicode 对象，不尝试进行任何转换。 如果该对象不为 Unicode 对象则会引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError")。 C 变量也可被声明为 [PyObject](structures.md#c.PyObject "PyObject")*。

`w*` (可读写 [bytes-like object](../glossary.md#term-bytes-like-object)) [Py_buffer]
~~~
    

~~~
这个表达式接受任何实现可读写缓存区接口的对象。它为调用者提供的 [`Py_buffer`](buffer.md#c.Py_buffer "Py_buffer") 结构赋值。缓冲区可能存在嵌入的 null 字节。当缓冲区使用完后调用者需要调用 [`PyBuffer_Release()`](buffer.md#c.PyBuffer_Release "PyBuffer_Release")。

`es` ([`str`](../library/stdtypes.md#str "str")) [const char *encoding, char **buffer]
~~~
    

~~~
`s` 的变式，它将编码后的 Unicode 字符存入字符缓冲区。它只处理没有嵌 NUL 字节的已编码数据。

此格式需要两个参数。 第一个仅用作输入，并且必须为 const char*，它指向一个以 NUL 结束的字符串表示的编码格式名称，或者为 `NULL`，这种情况会使用 `'utf-8'` 编码格式。 如果 Python 无法识别指定的编码格式则会引发异常。 第二个参数必须为 char**；它所引用的指针值将被设为带有参数文本内容的缓冲区。 文本将以第一个参数所指定的编码格式进行编码。

`PyArg_ParseTuple()` 会分配一个足够大小的缓冲区，将编码后的数据拷贝进这个缓冲区并且设置 _*buffer_ 引用这个新分配的内存空间。调用者有责任在使用后调用 [`PyMem_Free()`](memory.md#c.PyMem_Free "PyMem_Free") 去释放已经分配的缓冲区。

`et` ([`str`](../library/stdtypes.md#str "str"), [`bytes`](../library/stdtypes.md#bytes "bytes") or [`bytearray`](../library/stdtypes.md#bytearray "bytearray")) [const char *encoding, char **buffer]
~~~
    

~~~
和 `es` 相同，除了不用重编码传入的字符串对象。相反，它假设传入的参数是编码后的字符串类型。

`es#` ([`str`](../library/stdtypes.md#str "str")) [const char *encoding, char **buffer, [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") *buffer_length]
~~~
    

~~~
`s#` 的变式，它将已编码的 Unicode 字符存入字符缓冲区。不像 `es` 表达式，它允许传入的数据包含 NUL 字符。

它需要三个参数。 第一个仅用作输入，并且必须为 const char*，它指向一个以 NUL 结束的字符串表示的编码格式名称，或者为 `NULL`，这种情况会使用 `'utf-8'` 编码格式。 如果 Python 无法识别指定的编码格式则会引发异常。 第二个参数必须为 char**；它所引用的指针值将被设为带有参数文本内容的缓冲区。 文本将以第一个参数所指定的编码格式进行编码。 第三个参数必须为指向一个整数的指针；被引用的整数将被设为输出缓冲区中的字节数。

有两种操作方式：

如果 _*buffer_ 指向 `NULL` 指针，则函数将分配所需大小的缓冲区，将编码的数据复制到此缓冲区，并设置 _*buffer_ 以引用新分配的存储。 呼叫者负责调用 [`PyMem_Free()`](memory.md#c.PyMem_Free "PyMem_Free") 以在使用后释放分配的缓冲区。

如果 _*buffer_ 指向非 `NULL` 指针（已分配的缓冲区），则 `PyArg_ParseTuple()` 将使用此位置作为缓冲区，并将 _*buffer_length_ 的初始值解释为缓冲区大小。 然后，它将将编码的数据复制到缓冲区，并终止它。 如果缓冲区不够大，将设置一个 [`ValueError`](../library/exceptions.md#ValueError "ValueError")。

在这两个例子中， _*buffer_length_ 被设置为编码后结尾不为 NUL 的数据的长度。

`et#` ([`str`](../library/stdtypes.md#str "str"), [`bytes`](../library/stdtypes.md#bytes "bytes") 或 [`bytearray`](../library/stdtypes.md#bytearray "bytearray")) [const char *encoding, char **buffer, [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") *buffer_length]
~~~
    

~~~
和 `es#` 相同，除了不用重编码传入的字符串对象。相反，它假设传入的参数是编码后的字符串类型。

在 3.12 版本发生变更: `u`, `u#`, `Z` 和 `Z#` 已被移除因为它们只用于旧式的 `Py_UNICODE*` 表示形式。

### 数字¶

`b` ([`int`](../library/functions.md#int "int")) [unsigned char]
~~~
    

~~~
将非负的 Python 整数转换为无符号的微整数，存储为一个 C unsigned char。

`B` ([`int`](../library/functions.md#int "int")) [unsigned char]
~~~
    

~~~
将 Python 整数转换为微整数并且不进行溢出检查，存储为一个 C unsigned char。

`h` ([`int`](../library/functions.md#int "int")) [short int]
~~~
    

~~~
将 Python 整数转换为 C short int。

`H` ([`int`](../library/functions.md#int "int")) [unsigned short int]
~~~
    

~~~
将 Python 整数转换为 C unsigned short int，不进行溢出检查。

`i` ([`int`](../library/functions.md#int "int")) [int]
~~~
    

~~~
将 Python 整数转换为 C int。

`I` ([`int`](../library/functions.md#int "int")) [unsigned int]
~~~
    

~~~
将 Python 整数转换为 C unsigned int，不进行溢出检查。

`l` ([`int`](../library/functions.md#int "int")) [long int]
~~~
    

~~~
将 Python 整数转换为 C long int。

`k` ([`int`](../library/functions.md#int "int")) [unsigned long]
~~~
    

~~~
将 Python 整数转换为 C unsigned long，不进行溢出检查。

`L` ([`int`](../library/functions.md#int "int")) [long long]
~~~
    

~~~
将 Python 整数转换为 C long long。

`K` ([`int`](../library/functions.md#int "int")) [unsigned long long]
~~~
    

~~~
将Python整数转换为C:C:expr:'unsigned long-long'，而不进行溢出检查。

`n` ([`int`](../library/functions.md#int "int")) [[`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t")]
~~~
    

~~~
将一个 Python 整型转化成一个 C [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") Python 元大小类型。

`c` ([`bytes`](../library/stdtypes.md#bytes "bytes") 或者 [`bytearray`](../library/stdtypes.md#bytearray "bytearray") 长度为 1) [char]
~~~
    

~~~
将一个 Python 字节类型，如一个长度为 1 的 [`bytes`](../library/stdtypes.md#bytes "bytes") 或 [`bytearray`](../library/stdtypes.md#bytearray "bytearray") 对象，转换为 C char 。

在 3.3 版本发生变更: 允许 [`bytearray`](../library/stdtypes.md#bytearray "bytearray") 类型的对象。

`C` ([`str`](../library/stdtypes.md#str "str") 长度为 1) [int]
~~~
    

~~~
将一个 Python 字符，如一个长度为 1 的 [`str`](../library/stdtypes.md#str "str") 对象，转换为 C int。

`f` ([`float`](../library/functions.md#float "float")) [float]
~~~
    

~~~
将一个 Python 浮点数转换为 C float。

`d` ([`float`](../library/functions.md#float "float")) [double]
~~~
    

~~~
将一个 Python 浮点数转换为 C double。

`D` ([`complex`](../library/functions.md#complex "complex")) [Py_complex]
~~~
    

~~~
将一个 Python 复数类型转化成一个 C [`Py_complex`](complex.md#c.Py_complex "Py_complex") Python 复数类型。

### 其他对象¶

`O` (object) [PyObject *]
~~~
    

~~~
将 Python 对象（未经任何转换）存储到一个 C 对象指针中。 这样 C 程序就能接收到实际传递的对象。 对象的新 [strong reference](../glossary.md#term-strong-reference) 不会被创建（即其引用计数不会增加）。 存储的指针将不为 `NULL`。

`O!` (object) [ _typeobject_ , PyObject *]
~~~
    

~~~
将一个 Python 对象存入一个 C 对象指针。 这类似于 `O`，但是接受两个 C 参数：第一个是 Python 类型对象的地址，第二个是存储对象指针的 C 变量 (类型为 [PyObject](structures.md#c.PyObject "PyObject")*)。 如果 Python 对象不具有所要求的类型，则会引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError")。

`O&` (object) [ _converter_ , _anything_ ]
~~~
    

~~~
通过 _converter_ 函数将 Python 对象转换为 C 变量。这需要两个参数：第一个是函数，第二个是 C 变量（任意类型）的地址，转换为 void*。 _转换器_ 函数依次调用如下：
~~~
    
    
~~~
status = converter(object, address);
~~~

其中 _object_ 是待转换的 Python 对象而 _address_ 为传给 `PyArg_Parse*` 函数的 void* 参数。 返回的 _status_ 应当以 `1` 代表转换成功而以 `0` 代表转换失败。 当转换失败时， _converter_ 函数应当引发异常并让 _address_ 的内容保持未修改状态。

如果 _converter_ 返回 `Py_CLEANUP_SUPPORTED` ，则如果参数解析最终失败，它可能会再次调用该函数，从而使转换器有机会释放已分配的任何内存。在第二个调用中， _object_ 参数将为 `NULL` ;因此，该参数将为 `NULL` ;因此，该参数将为 `NULL` ，因此，该参数将为 `NULL` （如果值）为 `NULL` _address_ 的值与原始呼叫中的值相同。

在 3.1 版本发生变更: `Py_CLEANUP_SUPPORTED` 被添加。

`p` ([`bool`](functions.md#bool "bool")) [int]

    

~~~
测试传入的值是否为真(一个布尔判断)并且将结果转化为相对应的 C true/false 整型值。如果表达式为真置 `1`，假则置 `0`。它接受任何合法的 Python 值。参见 [逻辑值检测](../library/stdtypes.md#truth) 获取更多关于 Python 如何测试值为真的信息。

在 3.3 版本加入.

`(items)` ([`tuple`](../library/stdtypes.md#tuple "tuple")) [ _matching-items_ ]
~~~
    

~~~
对象必须是 Python 序列，它的长度是 _items_ 中格式单元的数量。C 参数必须对应 _items_ 中每一个独立的格式单元。序列中的格式单元可能有嵌套。

传递 "long" 整型 (取值超出平台的 `LONG_MAX` 限制的整形) 是可能的，然而不会进行适当的范围检测 --- 当接受字段太小而接收不到值时，最高有效比特位会被静默地截断 (实际上，该语义是继承自 C 的向下转换 --- 你的计数可能会发生变化)。

格式化字符串中还有一些其他的字符具有特殊的涵义。这些可能并不嵌套在圆括号中。它们是：

`|`
~~~
    

~~~
表明在 Python 参数列表中剩下的参数都是可选的。C 变量对应的可选参数需要初始化为默认值——当一个可选参数没有指定时， `PyArg_ParseTuple()` 不能访问相应的 C 变量(变量集)的内容。

`$`
~~~
    

~~~
`PyArg_ParseTupleAndKeywords()` only：表明在 Python 参数列表中剩下的参数都是强制关键字参数。当前，所有强制关键字参数都必须也是可选参数，所以格式化字符串中 `|` 必须一直在 `$` 前面。

在 3.3 版本加入.

`:`
~~~
    

~~~
格式单元的列表结束标志；冒号后的字符串被用来作为错误消息中的函数名(`PyArg_ParseTuple()` 函数引发的“关联值”异常)。

`;`
~~~
    

~~~
格式单元的列表结束标志；分号后的字符串被用来作为错误消息取代默认的错误消息。 `:` 和 `;` 相互排斥。

请注意提供给调用者的任何 Python 对象引用都是 _借入_ 引用；不要释放它们（即不要递减它们的引用计数）！

传递给这些函数的附加参数必须是由格式化字符串确定的变量的地址；这些都是用来存储输入元组的值。有一些情况，如上面的格式单元列表中所描述的，这些参数作为输入值使用；在这种情况下，它们应该匹配指定的相应的格式单元。

为了让转换成功， _arg_ 对象必须匹配格式并且格式必须被用尽。 当成功时，`PyArg_Parse*` 函数将返回真值，否则将返回假值并引发适当的异常。 当 `PyArg_Parse*` 函数由于某个格式单元转换出错而失败时，该格式单元及其后续格式单元对应的地址上的变量都将保持原样。

### API 函数¶

int PyArg_ParseTuple([PyObject](structures.md#c.PyObject "PyObject") *args, const char *format, ...)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

解析一个函数的参数，表达式中的参数按参数位置顺序存入局部变量中。成功返回 true；失败返回 false 并且引发相应的异常。

int PyArg_VaParse([PyObject](structures.md#c.PyObject "PyObject") *args, const char *format, va_list vargs)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

和 `PyArg_ParseTuple()` 相同，然而它接受一个 va_list 类型的参数而不是可变数量的参数集。

int PyArg_ParseTupleAndKeywords([PyObject](structures.md#c.PyObject "PyObject") *args, [PyObject](structures.md#c.PyObject "PyObject") *kw, const char *format, char *keywords[], ...)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

分析将位置参数和关键字参数同时转换为局部变量的函数的参数。 _keywords_ 参数是关键字参数名称的 `NULL` 终止数组。 空名称表示 [positional-only parameters](../glossary.md#positional-only-parameter)。成功时返回 true;发生故障时，它将返回 false 并引发相应的异常。

在 3.6 版本发生变更: 添加了 [positional-only parameters](../glossary.md#positional-only-parameter) 的支持。

int PyArg_VaParseTupleAndKeywords([PyObject](structures.md#c.PyObject "PyObject") *args, [PyObject](structures.md#c.PyObject "PyObject") *kw, const char *format, char *keywords[], va_list vargs)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

和 `PyArg_ParseTupleAndKeywords()` 相同，然而它接受一个va_list类型的参数而不是可变数量的参数集。

int PyArg_ValidateKeywordArguments([PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

确保字典中的关键字参数都是字符串。这个函数只被使用于 `PyArg_ParseTupleAndKeywords()` 不被使用的情况下，后者已经不再做这样的检查。

在 3.2 版本加入.

int PyArg_Parse([PyObject](structures.md#c.PyObject "PyObject") *args, const char *format, ...)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Parse the parameter of a function that takes a single positional parameter into a local variable. Returns true on success; on failure, it returns false and raises the appropriate exception.

Example:
~~~
    
    
~~~
// Function using METH_O calling convention
static PyObject*
my_function(PyObject *module, PyObject *arg)
{
    int value;
    if (!PyArg_Parse(arg, "i:my_function", &value)) {
        return NULL;
    }
    // ... use value ...
}
~~~

int PyArg_UnpackTuple([PyObject](structures.md#c.PyObject "PyObject") *args, const char *name, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") min, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") max, ...)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

一个更简单的形参提取形式，它不使用格式字符串来指定参数类型。 使用此方法来提取其形参的函数应当在函数或方法表中声明为 [`METH_VARARGS`](structures.md#c.METH_VARARGS "METH_VARARGS")。 包含实际形参的元组应当作为 _args_ 传入；它必须确实是一个元组。 该元组的长度必须至少为 _min_ 且不超过 _max_ ； _min_ 和 _max_ 可能相等。 额外的参数必须被传给函数，每个参数应当是一个指向 [PyObject](structures.md#c.PyObject "PyObject")* 变量的指针；它们将以来自 _args_ 的值来填充； 它们将包含 [借入引用](../glossary.md#term-borrowed-reference)。 对应于 _args_ 未给出的可选形参的变量不会被填充； 它们应当由调用方来初始化。 此函数在执行成功时返回真值而在 _args_ 不为元组或包含错误数量的元素时返回假值；如果执行失败则还将设置一个异常。

这是一个使用该函数的示例，取自 `_weakref` 弱引用辅助模块的源代码:

    
    
~~~cpp
static PyObject *
weakref_ref(PyObject *self, PyObject *args)
{
    PyObject *object;
    PyObject *callback = NULL;
    PyObject *result = NULL;

    if (PyArg_UnpackTuple(args, "ref", 1, 2, &object, &callback)) {
        result = PyWeakref_NewRef(object, callback);
    }
    return result;
}
~~~

这个例子中调用 `PyArg_UnpackTuple()` 完全等价于调用 `PyArg_ParseTuple()`:

    
    
~~~
PyArg_ParseTuple(args, "O|O:ref", &object, &callback)
~~~

## 创建变量¶

[PyObject](structures.md#c.PyObject "PyObject") *Py_BuildValue(const char *format, ...)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

基于类似 `PyArg_Parse*` 函数族所接受内容的格式字符串和一个值序列来创建一个新值。 返回该值或在发生错误的情况下返回 `NULL`；如果返回 `NULL` 则将引发一个异常。

`Py_BuildValue()` 并不一直创建一个元组。只有当它的格式化字符串包含两个或更多的格式单元才会创建一个元组。如果格式化字符串是空，它返回 `None`；如果它包含一个格式单元，它返回由格式单元描述的的任一对象。用圆括号包裹格式化字符串可以强制它返回一个大小为 0 或者 1 的元组。

当内存缓存区的数据以参数形式传递用来构建对象时，如 `s` 和 `s#` 格式单元，会拷贝需要的数据。调用者提供的缓冲区从来都不会被由 `Py_BuildValue()` 创建的对象来引用。换句话说，如果你的代码调用 `malloc()` 并且将分配的内存空间传递给 `Py_BuildValue()`，你的代码就有责任在 `Py_BuildValue()` 返回时调用 `free()` 。

在下面的描述中，双引号的表达式使格式单元；圆括号 () 内的是格式单元将要返回的 Python 对象类型；方括号 [] 内的是传递的 C 变量(变量集)的类型。

字符例如空格，制表符，冒号和逗号在格式化字符串中会被忽略(但是不包括格式单元，如 `s#`)。这可以使很长的格式化字符串具有更好的可读性。

`s` ([`str`](stdtypes.md#str "str") 或 `None`) [const char *]

    

~~~
使用 `'utf-8'` 编码将空终止的 C 字符串转换为 Python [`str`](../library/stdtypes.md#str "str") 对象。如果 C 字符串指针为 `NULL`，则使用 `None`。

`s#` ([`str`](../library/stdtypes.md#str "str") 或 `None`) [const char *, [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t")]
~~~
    

~~~
使用 `'utf-8'` 编码将 C 字符串及其长度转换为 Python [`str`](../library/stdtypes.md#str "str") 对象。如果 C 字符串指针为 `NULL`，则长度将被忽略，并返回 `None`。

`y` ([`bytes`](../library/stdtypes.md#bytes "bytes")) [const char *]
~~~
    

~~~
这将 C 字符串转换为 Python [`bytes`](../library/stdtypes.md#bytes "bytes") 对象。 如果 C 字符串指针为 `NULL`，则返回 `None`。

`y#` ([`bytes`](../library/stdtypes.md#bytes "bytes")) [const char *, [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t")]
~~~
    

~~~
这会将 C 字符串及其长度转换为一个 Python 对象。 如果该 C 字符串指针为 `NULL`，则返回 `None`。

`z` ([`str`](../library/stdtypes.md#str "str") or `None`) [const char *]
~~~
    

~~~
和 `s` 一样。

`z#` ([`str`](../library/stdtypes.md#str "str") 或 `None`) [const char *, [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t")]
~~~
    

~~~
和 `s#` 一样。

`u` ([`str`](../library/stdtypes.md#str "str")) [const wchar_t *]
~~~
    

~~~
将空终止的 `wchar_t` 的 Unicode （UTF-16 或 UCS-4） 数据缓冲区转换为 Python Unicode 对象。 如果 Unicode 缓冲区指针为 `NULL`，则返回 `None`。

`u#` ([`str`](../library/stdtypes.md#str "str")) [const wchar_t *, [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t")]
~~~
    

~~~
将 Unicode （UTF-16 或 UCS-4） 数据缓冲区及其长度转换为 Python Unicode 对象。 如果 Unicode 缓冲区指针为 `NULL`，则长度将被忽略，并返回 `None`。

`U` ([`str`](../library/stdtypes.md#str "str") 或 `None`) [const char *]
~~~
    

~~~
和 `s` 一样。

`U#` ([`str`](../library/stdtypes.md#str "str") 或 `None`) [const char *, [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t")]
~~~
    

~~~
和 `s#` 一样。

`i` ([`int`](../library/functions.md#int "int")) [int]
~~~
    

~~~
将一个基本 C int 转换为 Python 整数对象。

`b` ([`int`](../library/functions.md#int "int")) [char]
~~~
    

~~~
将一个基本 C char 转换为 Python 整数对象。

`h` ([`int`](../library/functions.md#int "int")) [short int]
~~~
    

~~~
将一个基本 C short int 转换为 Python 整数对象。

`l` ([`int`](../library/functions.md#int "int")) [long int]
~~~
    

~~~
将一个 C long int 转换为 Python 整数对象。

`B` ([`int`](../library/functions.md#int "int")) [unsigned char]
~~~
    

~~~
将一个 C unsigned char 转换为 Python 整数对象。

`H` ([`int`](../library/functions.md#int "int")) [unsigned short int]
~~~
    

~~~
将一个 C unsigned short int 转换为 Python 整数对象。

`I` ([`int`](../library/functions.md#int "int")) [unsigned int]
~~~
    

~~~
将一个 C unsigned int 转换为 Python 整数对象。

`k` ([`int`](../library/functions.md#int "int")) [unsigned long]
~~~
    

~~~
将一个 C unsigned long 转换为 Python 整数对象。

`L` ([`int`](../library/functions.md#int "int")) [long long]
~~~
    

~~~
将一个 C long long 转换为 Python 整数对象。

`K` ([`int`](../library/functions.md#int "int")) [unsigned long long]
~~~
    

~~~
将一个 C unsigned long long 转换为 Python 整数对象。

`n` ([`int`](../library/functions.md#int "int")) [[`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t")]
~~~
    

~~~
将一个 C [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 类型转化为 Python 整型。

`c` ([`bytes`](../library/stdtypes.md#bytes "bytes") 长度为1 ) [char]
~~~
    

~~~
将一个代表单个字节的 C int 转换为长度为 1 的 Python [`bytes`](../library/stdtypes.md#bytes "bytes") 对象。

`C` ([`str`](../library/stdtypes.md#str "str") 长度为 1) [int]
~~~
    

~~~
将一个代表单个字符的 C int 转换为长度为 1 的 Python [`str`](../library/stdtypes.md#str "str") 对象。

`d` ([`float`](../library/functions.md#float "float")) [double]
~~~
    

~~~
将一个 C double 转换为 Python 浮点数。

`f` ([`float`](../library/functions.md#float "float")) [float]
~~~
    

~~~
将一个 C float 转换为 Python 浮点数。

`D` ([`complex`](../library/functions.md#complex "complex")) [Py_complex *]
~~~
    

~~~
将一个 C [`Py_complex`](complex.md#c.Py_complex "Py_complex") 类型的结构转化为 Python 复数类型。

`O` (object) [PyObject *]
~~~
    

~~~
原封不动地传递一个 Python 对象，但为其创建一个新的 [strong reference](../glossary.md#term-strong-reference) (即其引用计数加一)。 如果传入的对象是一个 `NULL` 指针，则会假定这是因为产生该参数的调用发现了错误并设置了异常。 因此，`Py_BuildValue()` 将返回 `NULL` 但不会引发异常。 如果尚未引发异常，则会设置 [`SystemError`](../library/exceptions.md#SystemError "SystemError")。

`S` (object) [PyObject *]
~~~
    

~~~
和 `O` 相同。

`N` (object) [PyObject *]
~~~
    

~~~
与 `O` 相同，但它不会创建新的 [strong reference](../glossary.md#term-strong-reference)。 如果对象是通过调用参数列表中的对象构造器来创建的则该方法将很有用处。

`O&` (object) [ _converter_ , _anything_ ]
~~~
    

~~~
通过 _converter_ 函数将 _anything_ 转换为 Python 对象。 该函数在调用时附带 _anything_ (它应当兼容 void*) 作为其参数并且应返回一个 "新的" Python 对象，或者如果发生错误则返回 `NULL`。

`(items)` ([`tuple`](../library/stdtypes.md#tuple "tuple")) [ _matching-items_ ]
~~~
    

~~~
将一个 C 变量序列转换成 Python 元组并保持相同的元素数量。

`[items]` ([`list`](../library/stdtypes.md#list "list")) [ _相关的元素_ ]
~~~
    

~~~
将一个 C 变量序列转换成 Python 列表并保持相同的元素数量。

`{items}` ([`dict`](../library/stdtypes.md#dict "dict")) [ _相关的元素_ ]
~~~
    

~~~
