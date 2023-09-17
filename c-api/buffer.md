# 缓冲协议¶

在 Python 中可使用一些对象来包装对底层内存数组或称 _缓冲_ 的访问。此类对象包括内置的 [`bytes`](../library/stdtypes.md#bytes "bytes") 和 [`bytearray`](../library/stdtypes.md#bytearray "bytearray") 以及一些如 [`array.array`](../library/array.md#array.array "array.array") 这样的扩展类型。第三方库也可能会为了特殊的目的而定义它们自己的类型，例如用于图像处理和数值分析等。

虽然这些类型中的每一种都有自己的语义，但它们具有由可能较大的内存缓冲区支持的共同特征。 在某些情况下，希望直接访问该缓冲区而无需中间复制。

Python 以 缓冲协议 的形式在 C 层级上提供这样的功能。 此协议包括两个方面:

  * 在生产者这一方面，该类型的协议可以导出一个“缓冲区接口”，允许公开它的底层缓冲区信息。该接口的描述信息在 [Buffer Object Structures](typeobj.md#buffer-structs) 一节中；

  * 在消费者一侧，有几种方法可用于获得指向对象的原始底层数据的指针（例如一个方法的形参）。

一些简单的对象例如 [`bytes`](../library/stdtypes.md#bytes "bytes") 和 [`bytearray`](../library/stdtypes.md#bytearray "bytearray") 会以面向字节的形式公开它们的底层缓冲区。 也可能会用其他形式；例如 [`array.array`](../library/array.md#array.array "array.array") 所公开的元素可以是多字节值。

缓冲区接口的消费者的一个例子是文件对象的 [`write()`](../library/io.md#io.BufferedIOBase.write "io.BufferedIOBase.write") 方法：任何可以输出为一系列字节流的对象都可以被写入文件。 然而 `write()` 只需要对传入对象内容的只读权限，其他的方法如 [`readinto()`](../library/io.md#io.BufferedIOBase.readinto "io.BufferedIOBase.readinto") 需要对参数内容的写入权限。 缓冲区接口使用对象可以选择性地允许或拒绝读写或只读缓冲区的导出。

对于缓冲区接口的使用者而言，有两种方式来获取一个目的对象的缓冲：

  * 使用正确的参数来调用 `PyObject_GetBuffer()` 函数；

  * 调用 [`PyArg_ParseTuple()`](arg.md#c.PyArg_ParseTuple "PyArg_ParseTuple") (或其同级对象之一) 并传入 `y*`, `w*` or `s*` [格式代码](arg.md#arg-parsing) 中的一个。

在这两种情况下，当不再需要缓冲区时必须调用 `PyBuffer_Release()` 。如果此操作失败，可能会导致各种问题，例如资源泄漏。

## 缓冲区结构¶

缓冲区结构(或者简单地称为“buffers”)对于将二进制数据从另一个对象公开给 Python 程序员非常有用。它们还可以用作零拷贝切片机制。使用它们引用内存块的能力，可以很容易地将任何数据公开给 Python 程序员。内存可以是 C 扩展中的一个大的常量数组，也可以是在传递到操作系统库之前用于操作的原始内存块，或者可以用来传递本机内存格式的结构化数据。

与 Python 解释器公开的大多部数据类型不同，缓冲区不是 [`PyObject`](structures.md#c.PyObject "PyObject") 指针而是简单的 C 结构。 这使得它们可以非常简单地创建和复制。 当需要为缓冲区加上泛型包装器时，可以创建一个 [内存视图](memoryview.md#memoryview-objects) 对象。

有关如何编写并导出对象的简短说明，请参阅 [缓冲区对象结构](typeobj.md#buffer-structs)。 要获取缓冲区对象，请参阅 `PyObject_GetBuffer()`。

type Py_buffer¶  

    _Part of the[ Stable ABI](stable.md#stable) (including all members) since version 3.11._

void *buf¶  

    

~~~
指向由缓冲区字段描述的逻辑结构开始的指针。 这可以是导出程序底层物理内存块中的任何位置。 例如，使用负的 `strides` 值可能指向内存块的末尾。

对于 [contiguous](../glossary.md#term-contiguous) ，‘邻接’数组，值指向内存块的开头。

[PyObject](structures.md#c.PyObject "PyObject") *obj¶  
~~~
    

~~~
对导出对象的新引用。 该引用由消费方拥有，并由 `PyBuffer_Release()` 自动释放（即引用计数递减）并设置为 `NULL`。 该字段相当于任何标准 C-API 函数的返回值。

作为一种特殊情况，对于由 [`PyMemoryView_FromBuffer()`](memoryview.md#c.PyMemoryView_FromBuffer "PyMemoryView_FromBuffer") 或 `PyBuffer_FillInfo()` 包装的 _temporary_ 缓冲区，此字段为 `NULL`。 通常，导出对象不得使用此方案。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") len¶  
~~~
    

~~~
`product(shape) * itemsize`。对于连续数组，这是基础内存块的长度。对于非连续数组，如果逻辑结构复制到连续表示形式，则该长度将具有该长度。

仅当缓冲区是通过保证连续性的请求获取时，才访问 `((char *)buf)[0] up to ((char *)buf)[len-1]` 时才有效。在大多数情况下，此类请求将为 `PyBUF_SIMPLE` 或 `PyBUF_WRITABLE`。

int readonly¶  
~~~
    

~~~
缓冲区是否为只读的指示器。此字段由 `PyBUF_WRITABLE` 标志控制。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") itemsize¶  
~~~
    

~~~
单个元素的项大小（以字节为单位）。与 [`struct.calcsize()`](../library/struct.md#struct.calcsize "struct.calcsize") 调用非 `NULL` `format` 的值相同。

重要例外：如果使用者请求的缓冲区没有 `PyBUF_FORMAT` 标志，`format` 将设置为 `NULL`，但 `itemsize` 仍具有原始格式的值。

如果 `shape` 存在，则相等的 `product(shape) * itemsize == len` 仍然存在，使用者可以使用 `itemsize` 来导航缓冲区。

如果 `shape` 是 `NULL`，因为结果为 `PyBUF_SIMPLE` 或 `PyBUF_WRITABLE` 请求，则使用者必须忽略 `itemsize`，并假设 `itemsize == 1`。

const char *format¶  
~~~
    

~~~
在 [`struct`](../library/struct.md#module-struct "struct: Interpret bytes as packed binary data.") 模块样式语法中 _NUL_ 字符串，描述单个项的内容。如果这是 `NULL`，则假定为 `"B"` (无符号字节) 。

此字段由 `PyBUF_FORMAT` 标志控制。

int ndim¶  
~~~
    

~~~
内存表示为 n 维数组形式对应的维度数。 如果为 `0`，则 `buf` 指向表示标量的单个条目。 在这种情况下，`shape`, `strides` 和 `suboffsets` 必须为 `NULL`。 最大维度数由 `PyBUF_MAX_NDIM` 给出。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *shape¶  
~~~
    

~~~
一个长度为 [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 的数组 `ndim` 表示作为 n 维数组的内存形状。 请注意，`shape[0] * ... * shape[ndim-1] * itemsize` 必须等于 `len`。

Shape 形状数组中的值被限定在 `shape[n] >= 0` 。 `shape[n] == 0` 这一情形需要特别注意。更多信息请参阅 complex arrays 。

shape 数组对于使用者来说是只读的。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *strides¶  
~~~
    

~~~
一个长度为 [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 的数组 `ndim` 给出要跳过的字节数以获取每个尺寸中的新元素。

Stride 步幅数组中的值可以为任何整数。对于常规数组，步幅通常为正数，但是使用者必须能够处理 `strides[n] <= 0` 的情况。更多信息请参阅 complex arrays 。

strides数组对用户来说是只读的。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *suboffsets¶  
~~~
    

~~~
一个长度为 `ndim` 类型为 [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 的数组 。如果 `suboffsets[n] >= 0`，则第 n 维存储的是指针，suboffset 值决定了解除引用时要给指针增加多少字节的偏移。suboffset 为负值，则表示不应解除引用（在连续内存块中移动）。

如果所有子偏移均为负（即无需取消引用），则此字段必须为 `NULL` （默认值）。

Python Imaging Library (PIL) 中使用了这种类型的数组表达方式。请参阅 complex arrays 来了解如何从这样一个数组中访问元素。

suboffsets 数组对于使用者来说是只读的。

void *internal¶  
~~~
    

~~~
供输出对象内部使用。比如可能被输出程序重组为一个整数，用于存储一个标志，标明在缓冲区释放时是否必须释放 shape、strides 和 suboffsets 数组。消费者程序 _不得_ 修改该值。

常量:

PyBUF_MAX_NDIM¶  
~~~
    

~~~
内存表示的最大维度数。 导出程序必须遵守这个限制，多维缓冲区的使用者应该能够处理最多 `PyBUF_MAX_NDIM` 个维度。 目前设置为 64。

## 缓冲区请求的类型¶

通常，通过 `PyObject_GetBuffer()` 向输出对象发送缓冲区请求，即可获得缓冲区。由于内存的逻辑结构复杂，可能会有很大差异，缓冲区使用者可用 _flags_ 参数指定其能够处理的缓冲区具体类型。

所有 `Py_buffer` 字段均由请求类型无歧义地定义。

### 与请求无关的字段¶

以下字段不会被 _flags_ 影响，并且必须总是用正确的值填充：`obj`, `buf`，`len`，`itemsize`，`ndim`。

### 只读，格式¶

> PyBUF_WRITABLE¶  
>
>  
>
> 控制 `readonly` 字段。如果设置了，输出程序 _必须_ 提供一个可写的缓冲区，否则报告失败。若未设置，输出程序 _可以_ 提供只读或可写的缓冲区，但对所有消费者程序 _必须_ 保持一致。
>
> PyBUF_FORMAT¶  
>
>  
>
> 控制 `format` 字段。 如果设置，则必须正确填写此字段。其他情况下，此字段必须为 `NULL`。

`PyBUF_WRITABLE` 可以和下一节的所有标志联用。由于 `PyBUF_SIMPLE` 定义为 0，所以 `PyBUF_WRITABLE` 可以作为一个独立的标志，用于请求一个简单的可写缓冲区。

`PyBUF_FORMAT` 可以被设为除了 `PyBUF_SIMPLE` 之外的任何标志。 后者已经按暗示了 `B` (无符号字节串)格式。

### 形状，步幅，子偏移量¶

控制内存逻辑结构的标志按照复杂度的递减顺序列出。注意，每个标志包含它下面的所有标志。

请求

|

形状

|

步幅

|

子偏移量  
  
---|---|---|---  
  
PyBUF_INDIRECT¶  
~~~
    
|

是

|

是

|

如果需要的话  
  
PyBUF_STRIDES¶  

    
|

是

|

是

|

NULL  
  
PyBUF_ND¶  

    
|

是

|

NULL

|

NULL  
  
PyBUF_SIMPLE¶  

    
|

NULL

|

NULL

|

NULL  
  
### 连续性的请求¶

可以显式地请求C 或 Fortran [连续](../glossary.md#term-contiguous) ，不管有没有步幅信息。若没有步幅信息，则缓冲区必须是 C-连续的。

请求

|

形状

|

步幅

|

子偏移量

|

邻接  
  
---|---|---|---|---  
  
PyBUF_C_CONTIGUOUS¶  

    
|

是

|

是

|

NULL

|

C  
  
PyBUF_F_CONTIGUOUS¶  

    
|

是

|

是

|

NULL

|

F  
  
PyBUF_ANY_CONTIGUOUS¶  

    
|

是

|

是

|

NULL

|

C 或 F  
  
`PyBUF_ND`

|

是

|

NULL

|

NULL

|

C  
  
### 复合请求¶

所有可能的请求都由上一节中某些标志的组合完全定义。为方便起见，缓冲区协议提供常用的组合作为单个标志。

在下表中， _U_ 代表连续性未定义。消费者程序必须调用 `PyBuffer_IsContiguous()` 以确定连续性。

请求

|

形状

|

步幅

|

子偏移量

|

邻接

|

只读

|

format  
  
---|---|---|---|---|---|---  
  
PyBUF_FULL¶  

    
|

是

|

是

|

如果需要的话

|

U

|

0

|

是  
  
PyBUF_FULL_RO¶  

    
|

是

|

是

|

如果需要的话

|

U

|

1 或 0

|

是  
  
PyBUF_RECORDS¶  

    
|

是

|

是

|

NULL

|

U

|

0

|

是  
  
PyBUF_RECORDS_RO¶  

    
|

是

|

是

|

NULL

|

U

|

1 或 0

|

是  
  
PyBUF_STRIDED¶  

    
|

是

|

是

|

NULL

|

U

|

0

|

NULL  
  
PyBUF_STRIDED_RO¶  

    
|

是

|

是

|

NULL

|

U

|

1 或 0

|

NULL  
  
PyBUF_CONTIG¶  

    
|

是

|

NULL

|

NULL

|

C

|

0

|

NULL  
  
PyBUF_CONTIG_RO¶  

    
|

是

|

NULL

|

NULL

|

C

|

1 或 0

|

NULL  
  
## 复杂数组¶

### NumPy-风格：形状和步幅¶

NumPy 风格数组的逻辑结构由 `itemsize` 、 `ndim` 、 `shape` 和 `strides` 定义。

如果 `ndim == 0` ， `buf` 指向的内存位置被解释为大小为 `itemsize` 的标量。这时， `shape` 和 `strides` 都为 `NULL`。

如果 `strides` 为 `NULL`，则数组将被解释为一个标准的 n 维 C 语言数组。否则，消费者程序必须按如下方式访问 n 维数组：

    
    
~~~
ptr = (char *)buf + indices[0] * strides[0] + ... + indices[n-1] * strides[n-1];
item = *((typeof(item) *)ptr);
~~~

如上所述，`buf` 可以指向实际内存块中的任意位置。输出者程序可以用该函数检查缓冲区的有效性。

    
    
~~~python
def verify_structure(memlen, itemsize, ndim, shape, strides, offset):
    """Verify that the parameters represent a valid array within
       the bounds of the allocated memory:
           char *mem: start of the physical memory block
           memlen: length of the physical memory block
           offset: (char *)buf - mem
    """
    if offset % itemsize:
        return False
    if offset < 0 or offset+itemsize > memlen:
        return False
    if any(v % itemsize for v in strides):
        return False

    if ndim <= 0:
        return ndim == 0 and not shape and not strides
    if 0 in shape:
        return True

    imin = sum(strides[j]*(shape[j]-1) for j in range(ndim)
               if strides[j] <= 0)
    imax = sum(strides[j]*(shape[j]-1) for j in range(ndim)
               if strides[j] > 0)

    return 0 <= offset+imin and offset+imax+itemsize <= memlen
~~~

### PIL-风格：形状，步幅和子偏移量¶

除了常规项之外， PIL 风格的数组还可以包含指针，必须跟随这些指针才能到达维度的下一个元素。例如，常规的三维 C 语言数组 `char v[2][2][3]` 可以看作是一个指向 2 个二维数组的 2 个指针：`char (*v[2])[2][3]`。在子偏移表示中，这两个指针可以嵌入在 `buf` 的开头，指向两个可以位于内存任何位置的 `char x[2][3]` 数组。

这是一个函数，当n维索引所指向的N-D数组中有 `NULL` 步长和子偏移量时，它返回一个指针

    
    
~~~
void *get_item_pointer(int ndim, void *buf, Py_ssize_t *strides,
                       Py_ssize_t *suboffsets, Py_ssize_t *indices) {
    char *pointer = (char*)buf;
    int i;
    for (i = 0; i < ndim; i++) {
        pointer += strides[i] * indices[i];
        if (suboffsets[i] >=0 ) {
            pointer = *((char**)pointer) + suboffsets[i];
        }
    }
    return (void*)pointer;
}
~~~

## 缓冲区相关函数¶

int PyObject_CheckBuffer([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

如果 _obj_ 支持缓冲区接口，则返回 `1`，否则返回 `0`。 返回 `1` 时不保证 `PyObject_GetBuffer()` 一定成功。本函数一定调用成功。

int PyObject_GetBuffer([PyObject](structures.md#c.PyObject "PyObject") *exporter, Py_buffer *view, int flags)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

向 _exporter_ 发送请求以按照 _flags_ 指定的内容填充 _view_ 。 如果 exporter 无法提供要求类型的缓冲区，则它必须引发 [`BufferError`](../library/exceptions.md#BufferError "BufferError")，将 `view->obj` 设为 `NULL` 并返回 `-1`。

成功时，填充 _view_ ，将 `view->obj` 设为对 _exporter_ 的新引用，并返回 0。 当链式缓冲区提供程序将请求重定向到一个对象时，`view->obj` 可以引用该对象而不是 _exporter_ (参见 [缓冲区对象结构](typeobj.md#buffer-structs))。

`PyObject_GetBuffer()` 必须与 `PyBuffer_Release()` 同时调用成功，类似于 `malloc()` 和 `free()`。因此，消费者程序用完缓冲区后， `PyBuffer_Release()` 必须保证被调用一次。

void PyBuffer_Release(Py_buffer *view)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

释放缓冲区 _view_ 并释放对视图的支持对象 `view->obj` 的 [strong reference](../glossary.md#term-strong-reference) (即递减引用计数)。 该函数必须在缓冲区不再使用时调用，否则可能会发生引用泄漏。

若该函数针对的缓冲区不是通过 `PyObject_GetBuffer()` 获得的，将会出错。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyBuffer_SizeFromFormat(const char *format)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

从 `format` 返回隐含的 `itemsize`。 如果出错，则引发异常并返回 -1。

在 3.9 版本加入.

int PyBuffer_IsContiguous(const Py_buffer *view, char order)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

如果 _view_ 定义的内存是 C 风格（ _order_ 为 `'C'`）或 Fortran 风格（ _order_ 为 `'F'`） [contiguous](../glossary.md#term-contiguous) 或其中之一（ _order_ 是 `'A'`），则返回 `1`。否则返回 `0`。该函数总会成功。

void *PyBuffer_GetPointer(const Py_buffer *view, const [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *indices)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

获取给定 _view_ 内的 _indices_ 所指向的内存区域。 _indices_ 必须指向一个 `view->ndim` 索引的数组。

int PyBuffer_FromContiguous(const Py_buffer *view, const void *buf, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") len, char fort)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

从 _buf_ 复制连续的 _len_ 字节到 _view_ 。 _fort_ 可以是 `'C'` 或 `'F'` （对应于 C 风格或 Fortran 风格的顺序）。成功时返回 `0` ，错误时返回 `-1` 。

int PyBuffer_ToContiguous(void *buf, const Py_buffer *src, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") len, char order)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

从 _src_ 复制 _len_ 字节到 _buf_ ，成为连续字节串的形式。 _order_ 可以是 `'C'` 或 `'F'` 或 `'A'` （对应于 C 风格、Fortran 风格的顺序或其中任意一种）。成功时返回 `0` ，出错时返回 `-1` 。

如果 _len_ != _src- >len_ 则此函数将报错。

int PyObject_CopyData([PyObject](structures.md#c.PyObject "PyObject") *dest, [PyObject](structures.md#c.PyObject "PyObject") *src)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

将数据从 _src_ 拷贝到 _dest_ 缓冲区。 可以在 C 风格或 Fortran 风格的缓冲区之间进行转换。

成功时返回 `0`，出错时返回 `-1`。

void PyBuffer_FillContiguousStrides(int ndims, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *shape, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *strides, int itemsize, char order)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

用给定形状的 [contiguous](../glossary.md#term-contiguous) 字节串数组 (如果 _order_ 为 `'C'` 则为 C 风格，如果 _order_ 为 `'F'` 则为 Fortran 风格) 来填充 _strides_ 数组，每个元素具有给定的字节数。

int PyBuffer_FillInfo(Py_buffer *view, [PyObject](structures.md#c.PyObject "PyObject") *exporter, void *buf, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") len, int readonly, int flags)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

处理导出程序的缓冲区请求，该导出程序要公开大小为 _len_ 的 _buf_ ，并根据 _readonly_ 设置可写性。 _bug_ 被解释为一个无符号字节序列。

参数 _flags_ 表示请求的类型。该函数总是按照 flag 指定的内容填入 _view_ ，除非 _buf_ 设为只读，并且 _flag_ 中设置了 `PyBUF_WRITABLE` 标志。

成功时，将 `view->obj` 设为对 _exporter_ 的新引用并返回 0。 否则，引发 [`BufferError`](../library/exceptions.md#BufferError "BufferError")，将 `view->obj` 设为 `NULL` 并返回 `-1`；

如果此函数用作 [getbufferproc](typeobj.md#buffer-structs) 的一部分，则 _exporter_ 必须设置为导出对象，并且必须在未修改的情况下传递 _flags_ 。否则， _exporter_ 必须是 `NULL`。

