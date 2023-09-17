# 元组对象¶

type PyTupleObject¶  

    

~~~
这个 [`PyObject`](structures.md#c.PyObject "PyObject") 的子类型代表一个 Python 的元组对象。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyTuple_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

[`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的实例代表一个 Python 元组类型，这与 Python 层面的 [`tuple`](../library/stdtypes.md#tuple "tuple") 是相同的对象。

int PyTuple_Check([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果 _p_ 是一个 tuple 对象或者 tuple 类型的子类型的实例则返回真值。 此函数总是会成功执行。

int PyTuple_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果 _p_ 是一个 tuple 对象但不是 tuple 类型的子类型的实例则返回真值。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PyTuple_New([Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") len)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回一个新的元组对象，长度为 _len_ ，失败时返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyTuple_Pack([Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") n, ...)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回一个新的元组对象，大小为 _n_ ，失败时返回 `NULL`。 元组值初始化为指向 Python 对象的后续 _n_ 个 C 参数。 `PyTuple_Pack(2, a, b)` 和 `Py_BuildValue("(OO)", a, b)` 相等。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyTuple_Size([PyObject](structures.md#c.PyObject "PyObject") *p)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

获取指向元组对象的指针，并返回该元组的大小。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyTuple_GET_SIZE([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
返回元组 _p_ 的大小，它必须为非 `NULL` 并且指向一个元组；不执行错误检查。

[PyObject](structures.md#c.PyObject "PyObject") *PyTuple_GetItem([PyObject](structures.md#c.PyObject "PyObject") *p, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") pos)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _p_ 所指向的元组中位于 _pos_ 处的对象。 如果 _pos_ 为负值或超出范围，则返回 `NULL` 并设置一个 [`IndexError`](../library/exceptions.md#IndexError "IndexError") 异常。

[PyObject](structures.md#c.PyObject "PyObject") *PyTuple_GET_ITEM([PyObject](structures.md#c.PyObject "PyObject") *p, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") pos)¶  

    _返回值：借入的引用。_

类似于 `PyTuple_GetItem()`，但不检查其参数。

[PyObject](structures.md#c.PyObject "PyObject") *PyTuple_GetSlice([PyObject](structures.md#c.PyObject "PyObject") *p, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") low, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") high)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _p_ 所指向的元组的从 _low_ 到 _high_ 的切片，或者在失败时返回 `NULL`。 这等价于 Python 表达式 `p[low:high]`。 不支持从元组末尾进行索引。

int PyTuple_SetItem([PyObject](structures.md#c.PyObject "PyObject") *p, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") pos, [PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

在 _p_ 指向的元组的 _pos_ 位置插入对对象 _o_ 的引用。 成功时返回 `0`；如果 _pos_ 越界，则返回 `-1`，并抛出一个 [`IndexError`](../library/exceptions.md#IndexError "IndexError") 异常。

备注

此函数会“窃取”对 _o_ 的引用，并丢弃对元组中已在受影响位置的条目的引用。

void PyTuple_SET_ITEM([PyObject](structures.md#c.PyObject "PyObject") *p, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") pos, [PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
类似于 `PyTuple_SetItem()`，但不进行错误检查，并且应该 _只是_ 被用来填充全新的元组。

Bounds checking is performed as an assertion if Python is built in [debug mode](../using/configure.md#debug-build) or [`with assertions`](../using/configure.md#cmdoption-with-assertions).

备注

这个函数会“窃取”一个对 _o_ 的引用，但是，不与 `PyTuple_SetItem()` 不同，它 _不会_ 丢弃对任何被替换项的引用；元组中位于 _pos_ 位置的任何引用都将被泄漏。

int _PyTuple_Resize([PyObject](structures.md#c.PyObject "PyObject") **p, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") newsize)¶  
~~~
    

~~~
可以用于调整元组的大小。 _newsize_ 将是元组的新长度。 因为元组 _被认为_ 是不可变的，所以只有在对象仅有一个引用时，才应该使用它。 如果元组已经被代码的其他部分所引用，请不要使用此项。 元组在最后总是会增长或缩小。 把它看作是销毁旧元组并创建一个新元组，只会更有效。 成功时返回 `0` 。 客户端代码不应假定 `*p` 的结果值将与调用此函数之前的值相同。 如果替换了 `*p` 引用的对象，则原始的 `*p` 将被销毁。 失败时，返回 `-1`，将 `*p` 设置为 `NULL`，并引发 [`MemoryError`](../library/exceptions.md#MemoryError "MemoryError") 或者 [`SystemError`](../library/exceptions.md#SystemError "SystemError")。

# 结构序列对象¶

结构序列对象是等价于 [`namedtuple()`](../library/collections.md#collections.namedtuple "collections.namedtuple") 的 C 对象，即一个序列，其中的条目也可以通过属性访问。 要创建结构序列，你首先必须创建特定的结构序列类型。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *PyStructSequence_NewType(PyStructSequence_Desc *desc)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

根据 _desc_ 中的数据创建一个新的结构序列类型，如下所述。 可以使用 `PyStructSequence_New()` 创建结果类型的实例。

void PyStructSequence_InitType([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type, PyStructSequence_Desc *desc)¶  
~~~
    

~~~
从 _desc_ 就地初始化结构序列类型 _type_ 。

int PyStructSequence_InitType2([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type, PyStructSequence_Desc *desc)¶  
~~~
    

~~~
与 `PyStructSequence_InitType` 相同，但成功时返回 `0` ，失败时返回 `-1` 。

在 3.4 版本加入.

type PyStructSequence_Desc¶  

    _Part of the[ Stable ABI](stable.md#stable) (including all members)._

包含要创建的结构序列类型的元信息。

const char *name¶  
~~~
    

~~~
结构序列类型的名称。

const char *doc¶  
~~~
    

~~~
指向类型的文档字符串的指针或以 `NULL` 表示忽略。

PyStructSequence_Field *fields¶  
~~~
    

~~~
指向以 `NULL` 结尾的数组的指针，该数组包含新类型的字段名。

int n_in_sequence¶  
~~~
    

~~~
Python 端可见的字段数（如果用作元组）。

type PyStructSequence_Field¶  

    _Part of the[ Stable ABI](stable.md#stable) (including all members)._

描述结构序列的一个字段。 由于结构序列是以元组为模型的，因此所有字段的类型都是 [PyObject](structures.md#c.PyObject "PyObject")*。 `PyStructSequence_Desc` 的 `fields` 数组中的索引决定了描述结构序列的是哪个字段。

const char *name¶  
~~~
    

~~~
字段的名称或 `NULL` 表示结束已命名字段列表，设为 `PyStructSequence_UnnamedField` 则保持未命名状态。

const char *doc¶  
~~~
    

~~~
字段文档字符串或 `NULL` 表示省略。

const char *const PyStructSequence_UnnamedField¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

字段名的特殊值将保持未命名状态。

在 3.9 版本发生变更: 这个类型已从 `char *` 更改。

[PyObject](structures.md#c.PyObject "PyObject") *PyStructSequence_New([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

创建 _type_ 的实例，该实例必须使用 `PyStructSequence_NewType()` 创建。

[PyObject](structures.md#c.PyObject "PyObject") *PyStructSequence_GetItem([PyObject](structures.md#c.PyObject "PyObject") *p, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") pos)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Return the object at position _pos_ in the struct sequence pointed to by _p_.

Bounds checking is performed as an assertion if Python is built in [debug mode](../using/configure.md#debug-build) or [`with assertions`](../using/configure.md#cmdoption-with-assertions).

[PyObject](structures.md#c.PyObject "PyObject") *PyStructSequence_GET_ITEM([PyObject](structures.md#c.PyObject "PyObject") *p, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") pos)¶  

    _返回值：借入的引用。_

Alias to `PyStructSequence_GetItem()`.

在 3.13 版本发生变更: Now implemented as an alias to `PyStructSequence_GetItem()`.

void PyStructSequence_SetItem([PyObject](structures.md#c.PyObject "PyObject") *p, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") pos, [PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将结构序列 _p_ 的索引 _pos_ 处的字段设置为值 _o_ 。 与 `PyTuple_SET_ITEM()` 一样，它应该只用于填充全新的实例。

Bounds checking is performed as an assertion if Python is built in [debug mode](../using/configure.md#debug-build) or [`with assertions`](../using/configure.md#cmdoption-with-assertions).

备注

这个函数“窃取”了指向 _o_ 的一个引用。

void PyStructSequence_SET_ITEM([PyObject](structures.md#c.PyObject "PyObject") *p, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *pos, [PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
