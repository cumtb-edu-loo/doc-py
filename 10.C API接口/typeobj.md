# 类型对象¶

Python 对象系统中最重要的一个结构体也许是定义新类型的结构体: [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 结构体。 类型对象可以使用任何 `PyObject_*` 或 `PyType_*` 函数来处理，但并未提供大多数 Python 应用程序会感兴趣的东西。 这些对象是对象行为的基础，所以它们对解释器本身及任何实现新类型的扩展模块都非常重要。

与大多数标准类型相比，类型对象相当大。这么大的原因是每个类型对象存储了大量的值，大部分是C函数指针，每个指针实现了类型功能的一小部分。本节将详细描述类型对象的字段。这些字段将按照它们在结构中出现的顺序进行描述。

除了下面的快速参考， 例子 小节提供了快速了解 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的含义和用法的例子。

## 快速参考¶

### "tp_方法槽"¶

PyTypeObject 槽 [1]

|

Type

|

特殊方法/属性

|

信息 [2]  
  
---|---|---|---  
  
O

|

T

|

D

|

I  
  
<R> `tp_name`

|

const char *

|

__name__

|

X

|

X

|  |  
  
`tp_basicsize`

|

[`Py_ssize_t`](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t")

|  |

X

|

X

|  |

X  
  
`tp_itemsize`

|

[`Py_ssize_t`](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t")

|  |  |

X

|  |

X  
  
`tp_dealloc`

|

`destructor`

|  |

X

|

X

|  |

X  
  
`tp_vectorcall_offset`

|

[`Py_ssize_t`](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t")

|  |  |

X

|  |

X  
  
(`tp_getattr`)

|

`getattrfunc`

|

__getattribute__, __getattr__

|  |  |  |

G  
  
(`tp_setattr`)

|

`setattrfunc`

|

__setattr__, __delattr__

|  |  |  |

G  
  
`tp_as_async`

|

`PyAsyncMethods` *

|

子方法槽（方法域）

|  |  |  |

%  
  
`tp_repr`

|

`reprfunc`

|

__repr__

|

X

|

X

|  |

X  
  
`tp_as_number`

|

`PyNumberMethods` *

|

子方法槽（方法域）

|  |  |  |

%  
  
`tp_as_sequence`

|

`PySequenceMethods` *

|

子方法槽（方法域）

|  |  |  |

%  
  
`tp_as_mapping`

|

`PyMappingMethods` *

|

子方法槽（方法域）

|  |  |  |

%  
  
`tp_hash`

|

`hashfunc`

|

__hash__

|

X

|  |  |

G  
  
`tp_call`

|

`ternaryfunc`

|

__call__

|  |

X

|  |

X  
  
`tp_str`

|

`reprfunc`

|

__str__

|

X

|  |  |

X  
  
`tp_getattro`

|

`getattrofunc`

|

__getattribute__, __getattr__

|

X

|

X

|  |

G  
  
`tp_setattro`

|

`setattrofunc`

|

__setattr__, __delattr__

|

X

|

X

|  |

G  
  
`tp_as_buffer`

|

`PyBufferProcs` *

|  |  |  |  |

%  
  
`tp_flags`

|

unsigned long

|  |

X

|

X

|  |

?  
  
`tp_doc`

|

const char *

|

__doc__

|

X

|

X

|  |  
  
`tp_traverse`

|

[`traverseproc`](gcsupport.md#c.traverseproc "traverseproc")

|  |  |

X

|  |

G  
  
`tp_clear`

|

[`inquiry`](gcsupport.md#c.inquiry "inquiry")

|  |  |

X

|  |

G  
  
`tp_richcompare`

|

`richcmpfunc`

|

__lt__, __le__, __eq__, __ne__, __gt__, __ge__

|

X

|  |  |

G  
  
(`tp_weaklistoffset`)

|

[`Py_ssize_t`](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t")

|  |  |

X

|  |

?  
  
`tp_iter`

|

`getiterfunc`

|

__iter__

|  |  |  |

X  
  
`tp_iternext`

|

`iternextfunc`

|

__next__

|  |  |  |

X  
  
`tp_methods`

|

[`PyMethodDef`](structures.md#c.PyMethodDef "PyMethodDef") []

|  |

X

|

X

|  |  
  
`tp_members`

|

[`PyMemberDef`](structures.md#c.PyMemberDef "PyMemberDef") []

|  |  |

X

|  |  
  
`tp_getset`

|

[`PyGetSetDef`](structures.md#c.PyGetSetDef "PyGetSetDef") []

|  |

X

|

X

|  |  
  
`tp_base`

|

[`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") *

|

__base__

|  |  |

X

|  
  
`tp_dict`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

__dict__

|  |  |

?

|  
  
`tp_descr_get`

|

`descrgetfunc`

|

__get__

|  |  |  |

X  
  
`tp_descr_set`

|

`descrsetfunc`

|

__set__, __delete__

|  |  |  |

X  
  
(`tp_dictoffset`)

|

[`Py_ssize_t`](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t")

|  |  |

X

|  |

?  
  
`tp_init`

|

`initproc`

|

__init__

|

X

|

X

|  |

X  
  
`tp_alloc`

|

`allocfunc`

|  |

X

|  |

?

|

?  
  
`tp_new`

|

`newfunc`

|

__new__

|

X

|

X

|

?

|

?  
  
`tp_free`

|

`freefunc`

|  |

X

|

X

|

?

|

?  
  
`tp_is_gc`

|

[`inquiry`](gcsupport.md#c.inquiry "inquiry")

|  |  |

X

|  |

X  
  
<`tp_bases`>

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

__bases__

|  |  |

~

|  
  
<`tp_mro`>

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

__mro__

|  |  |

~

|  
  
[`tp_cache`]

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

|  |  |  |  
  
[`tp_subclasses`]

|

void *

|

__subclasses__

|  |  |  
  
[`tp_weaklist`]

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

|  |  |  |  
  
(`tp_del`)

|

`destructor`

|  |  |  |  |  
  
[`tp_version_tag`]

|

unsigned int

|  |  |  |  
  
`tp_finalize`

|

`destructor`

|

__del__

|  |  |  |

X  
  
`tp_vectorcall`

|

[`vectorcallfunc`](call.md#c.vectorcallfunc "vectorcallfunc")

|  |  |  |  |  
  
[`tp_watched`]

|

unsigned char

|  |  |  |  |  
[1]

**（）** ：括号中的插槽名称表示（实际上）已弃用。

**< >**: 尖括号内的名称在初始时应设为 `NULL` 并被视为是只读的。

**[]** : 方括号内的名称仅供内部使用。

**< R>** (作为前缀) 表示字段是必需的 (不能是 `NULL`)。

[2]

列：

**" O"**: 在 `PyBaseObject_Type` 上设置

**" T"**: 在 [`PyType_Type`](type.md#c.PyType_Type "PyType_Type") 上设置

**" D"**: 默认设置(如果方法槽被设置为NULL)

    
    
~~~
X - PyType_Ready sets this value if it is NULL
~ - PyType_Ready always sets this value (it should be NULL)
? - PyType_Ready may set this value depending on other slots

Also see the inheritance column ("I").
~~~

**" I"**: 继承

    
    
~~~
X - type slot is inherited via *PyType_Ready* if defined with a *NULL* value
% - the slots of the sub-struct are inherited individually
G - inherited, but only in combination with other slots; see the slot's description
? - it's complicated; see the slot's description
~~~

注意，有些方法槽是通过普通属性查找链有效继承的。

### 子方法槽（方法域）¶

方法槽

|

Type

|

特殊方法  
  
---|---|---  
  
`am_await`

|

`unaryfunc`

|

__await__  
  
`am_aiter`

|

`unaryfunc`

|

__aiter__  
  
`am_anext`

|

`unaryfunc`

|

__anext__  
  
`am_send`

|

`sendfunc`

|  
  
`nb_add`

|

`binaryfunc`

|

__add__ __radd__  
  
`nb_inplace_add`

|

`binaryfunc`

|

__iadd__  
  
`nb_subtract`

|

`binaryfunc`

|

__sub__ __rsub__  
  
`nb_inplace_subtract`

|

`binaryfunc`

|

__isub__  
  
`nb_multiply`

|

`binaryfunc`

|

__mul__ __rmul__  
  
`nb_inplace_multiply`

|

`binaryfunc`

|

__imul__  
  
`nb_remainder`

|

`binaryfunc`

|

__mod__ __rmod__  
  
`nb_inplace_remainder`

|

`binaryfunc`

|

__imod__  
  
`nb_divmod`

|

`binaryfunc`

|

__divmod__ __rdivmod__  
  
`nb_power`

|

`ternaryfunc`

|

__pow__ __rpow__  
  
`nb_inplace_power`

|

`ternaryfunc`

|

__ipow__  
  
`nb_negative`

|

`unaryfunc`

|

__neg__  
  
`nb_positive`

|

`unaryfunc`

|

__pos__  
  
`nb_absolute`

|

`unaryfunc`

|

__abs__  
  
`nb_bool`

|

[`inquiry`](gcsupport.md#c.inquiry "inquiry")

|

__bool__  
  
`nb_invert`

|

`unaryfunc`

|

__invert__  
  
`nb_lshift`

|

`binaryfunc`

|

__lshift__ __rlshift__  
  
`nb_inplace_lshift`

|

`binaryfunc`

|

__ilshift__  
  
`nb_rshift`

|

`binaryfunc`

|

__rshift__ __rrshift__  
  
`nb_inplace_rshift`

|

`binaryfunc`

|

__irshift__  
  
`nb_and`

|

`binaryfunc`

|

__and__ __rand__  
  
`nb_inplace_and`

|

`binaryfunc`

|

__iand__  
  
`nb_xor`

|

`binaryfunc`

|

__xor__ __rxor__  
  
`nb_inplace_xor`

|

`binaryfunc`

|

__ixor__  
  
`nb_or`

|

`binaryfunc`

|

__or__ __ror__  
  
`nb_inplace_or`

|

`binaryfunc`

|

__ior__  
  
`nb_int`

|

`unaryfunc`

|

__int__  
  
`nb_reserved`

|

void *

|  
  
`nb_float`

|

`unaryfunc`

|

__float__  
  
`nb_floor_divide`

|

`binaryfunc`

|

__floordiv__  
  
`nb_inplace_floor_divide`

|

`binaryfunc`

|

__ifloordiv__  
  
`nb_true_divide`

|

`binaryfunc`

|

__truediv__  
  
`nb_inplace_true_divide`

|

`binaryfunc`

|

__itruediv__  
  
`nb_index`

|

`unaryfunc`

|

__index__  
  
`nb_matrix_multiply`

|

`binaryfunc`

|

__matmul__ __rmatmul__  
  
`nb_inplace_matrix_multiply`

|

`binaryfunc`

|

__imatmul__  
  
`mp_length`

|

`lenfunc`

|

__len__  
  
`mp_subscript`

|

`binaryfunc`

|

__getitem__  
  
`mp_ass_subscript`

|

`objobjargproc`

|

__setitem__, __delitem__  
  
`sq_length`

|

`lenfunc`

|

__len__  
  
`sq_concat`

|

`binaryfunc`

|

__add__  
  
`sq_repeat`

|

`ssizeargfunc`

|

__mul__  
  
`sq_item`

|

`ssizeargfunc`

|

__getitem__  
  
`sq_ass_item`

|

`ssizeobjargproc`

|

__setitem__ __delitem__  
  
`sq_contains`

|

`objobjproc`

|

__contains__  
  
`sq_inplace_concat`

|

`binaryfunc`

|

__iadd__  
  
`sq_inplace_repeat`

|

`ssizeargfunc`

|

__imul__  
  
`bf_getbuffer`

|

`getbufferproc()`

|  
  
`bf_releasebuffer`

|

`releasebufferproc()`

|  
  
### 槽位 typedef¶

typedef

|

参数类型

|

返回类型  
  
---|---|---  
  
`allocfunc`

|

[`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") *

[`Py_ssize_t`](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t")

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`destructor`

|

void *

|

void  
  
`freefunc`

|

void *

|

void  
  
[`traverseproc`](gcsupport.md#c.traverseproc "traverseproc")

|

void *

[`visitproc`](gcsupport.md#c.visitproc "visitproc")

void *

|

int  
  
`newfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`initproc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

int  
  
`reprfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`getattrfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

const char *

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`setattrfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

const char *

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

int  
  
`getattrofunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`setattrofunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

int  
  
`descrgetfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`descrsetfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

int  
  
`hashfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

Py_hash_t  
  
`richcmpfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

int

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`getiterfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`iternextfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`lenfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

[`Py_ssize_t`](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t")  
  
`getbufferproc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`Py_buffer`](buffer.md#c.Py_buffer "Py_buffer") *

int

|

int  
  
`releasebufferproc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`Py_buffer`](buffer.md#c.Py_buffer "Py_buffer") *

|

void  
  
[`inquiry`](gcsupport.md#c.inquiry "inquiry")

|

void *

|

int  
  
`unaryfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`binaryfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`ternaryfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`ssizeargfunc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`Py_ssize_t`](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t")

|

[`PyObject`](structures.md#c.PyObject "PyObject") *  
  
`ssizeobjargproc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`Py_ssize_t`](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t")

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

int  
  
`objobjproc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

int  
  
`objobjargproc`

|

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

[`PyObject`](structures.md#c.PyObject "PyObject") *

|

int  
  
请参阅 Slot Type typedefs 里有更多详细信息。

## PyTypeObject 定义¶

[`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的结构定义可以在 `Include/object.h` 中找到。 为了方便参考，此处复述了其中的定义:

    
    
~~~
typedef struct _typeobject {
    PyObject_VAR_HEAD
    const char *tp_name; /* For printing, in format "<module>.<name>" */
    Py_ssize_t tp_basicsize, tp_itemsize; /* For allocation */

    /* Methods to implement standard operations */

    destructor tp_dealloc;
    Py_ssize_t tp_vectorcall_offset;
    getattrfunc tp_getattr;
    setattrfunc tp_setattr;
    PyAsyncMethods *tp_as_async; /* formerly known as tp_compare (Python 2)
                                    or tp_reserved (Python 3) */
    reprfunc tp_repr;

    /* Method suites for standard classes */

    PyNumberMethods *tp_as_number;
    PySequenceMethods *tp_as_sequence;
    PyMappingMethods *tp_as_mapping;

    /* More standard operations (here for binary compatibility) */

    hashfunc tp_hash;
    ternaryfunc tp_call;
    reprfunc tp_str;
    getattrofunc tp_getattro;
    setattrofunc tp_setattro;

    /* Functions to access object as input/output buffer */
    PyBufferProcs *tp_as_buffer;

    /* Flags to define presence of optional/expanded features */
    unsigned long tp_flags;

    const char *tp_doc; /* Documentation string */

    /* Assigned meaning in release 2.0 */
    /* call function for all accessible objects */
    traverseproc tp_traverse;

    /* delete references to contained objects */
    inquiry tp_clear;

    /* Assigned meaning in release 2.1 */
    /* rich comparisons */
    richcmpfunc tp_richcompare;

    /* weak reference enabler */
    Py_ssize_t tp_weaklistoffset;

    /* Iterators */
    getiterfunc tp_iter;
    iternextfunc tp_iternext;

    /* Attribute descriptor and subclassing stuff */
    struct PyMethodDef *tp_methods;
    struct PyMemberDef *tp_members;
    struct PyGetSetDef *tp_getset;
    // Strong reference on a heap type, borrowed reference on a static type
    struct _typeobject *tp_base;
    PyObject *tp_dict;
    descrgetfunc tp_descr_get;
    descrsetfunc tp_descr_set;
    Py_ssize_t tp_dictoffset;
    initproc tp_init;
    allocfunc tp_alloc;
    newfunc tp_new;
    freefunc tp_free; /* Low-level free-memory routine */
    inquiry tp_is_gc; /* For PyObject_IS_GC */
    PyObject *tp_bases;
    PyObject *tp_mro; /* method resolution order */
    PyObject *tp_cache;
    PyObject *tp_subclasses;
    PyObject *tp_weaklist;
    destructor tp_del;

    /* Type attribute cache version tag. Added in version 2.6 */
    unsigned int tp_version_tag;

    destructor tp_finalize;
    vectorcallfunc tp_vectorcall;

    /* bitset of which type-watchers care about this type */
    unsigned char tp_watched;
} PyTypeObject;
~~~

## PyObject 槽位¶

类型对象结构体扩展了 [`PyVarObject`](structures.md#c.PyVarObject "PyVarObject") 结构体。 `ob_size` 字段用于动态类型（由 `type_new()` 创建，通常由 class 语句调用）。 请注意 [`PyType_Type`](type.md#c.PyType_Type "PyType_Type") （元类型）会初始化 `tp_itemsize`，这意味着它的实例（即类型对象） _必须_ 具有 `ob_size` 字段。

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") [PyObject](structures.md#c.PyObject "PyObject").ob_refcnt¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是类型对象的引用计数，由 `PyObject_HEAD_INIT` 宏初始化为 `1`。 请注意对于 静态分配的类型对象，类型的实例（其 `ob_type` 指向该类型的对象） _不会被_ 计入引用。 但对于 动态分配的类型对象，实例 _会被_ 计入引用。

**继承：**

子类型不继承此字段。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *[PyObject](structures.md#c.PyObject "PyObject").ob_type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是类型的类型，换句话说就是元类型，它由宏 `PyObject_HEAD_INIT` 的参数来做初始化，它的值一般情况下是 `&PyType_Type` 。可是为了使动态可载入扩展模块至少在Windows上可用，编译器会报错这是一个不可用的初始化。因此按照惯例传递 `NULL` 给宏 `PyObject_HEAD_INIT` 并且在模块的初始化函数开始时候其他任何操作之前初始化这个字段。典型做法是这样的：

    
    
~~~
Foo_Type.ob_type = &PyType_Type;
~~~

这应当在创建类型的任何实例之前完成。 [`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready") 会检查 `ob_type` 是否为 `NULL`，如果是，则将其初始化为基类的 `ob_type` 字段。 如果该字段为非零值则 [`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready") 将不会更改它。

**继承：**

此字段会被子类型继承。

## PyVarObject 槽位¶

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") [PyVarObject](structures.md#c.PyVarObject "PyVarObject").ob_size¶  

    _Part of the[ Stable ABI](stable.md#stable)._

对于 静态分配的内存对象，它应该初始化为 0。对于 动态分配的类型对象，该字段具有特殊的内部含义。

**继承：**

子类型不继承此字段。

## PyTypeObject 槽¶

每个槽位都有一个小节来描述继承关系。 如果 [`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready") 可以在字段被设为 `NULL` 时设置一个值那么还会有一个“默认”小节。 （请注意在 `PyBaseObject_Type` 和 [`PyType_Type`](type.md#c.PyType_Type "PyType_Type") 上设置的许多字段实际上就是默认值。）

const char *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_name¶  

    

~~~
指向包含类型名称的以 NUL 结尾的字符串的指针。 对于可作为模块全局访问的类型，该字符串应为模块全名，后面跟一个点号，然后再加类型名称；对于内置类型，它应当只是类型名称。 如果模块是包的子模块，则包的全名将是模块的全名的一部分。 例如，在包 `P` 的子包 `Q` 中的模块 `M` 中定义的名为 `T` 的类型应当具有 `tp_name` 初始化器 `"P.Q.M.T"`。

对于 动态分配的类型对象，这应为类型名称，而模块名称将作为 `'__module__'` 键的值显式地保存在类型字典中。

对于 静态分配的类型对象， _tp_name_ 字段应当包含一个点号。 最后一个点号之前的所有内容都可作为 `__module__` 属性访问，而最后一个点号之后的所有内容都可作为 [`__name__`](../library/stdtypes.md#definition.__name__ "definition.__name__") 属性访问。

如果不存在点号，则整个 `tp_name` 字段将作为 [`__name__`](../library/stdtypes.md#definition.__name__ "definition.__name__") 属性访问，而 `__module__` 属性则将是未定义的（除非在字典中显式地设置，如上文所述）。 这意味着你的类型将无法执行 pickle。 此外，用 pydoc 创建的模块文档中也不会列出该类型。

该字段不可为 `NULL`。 它是 [`PyTypeObject()`](type.md#c.PyTypeObject "PyTypeObject") 中唯一的必填字段（除了潜在的 `tp_itemsize` 以外）。

**继承：**

子类型不继承此字段。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_basicsize¶  

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_itemsize¶  
~~~
    

~~~
通过这些字段可以计算出该类型实例以字节为单位的大小。

存在两种类型：具有固定长度实例的类型其 `tp_itemsize` 字段为零；具有可变长度实例的类型其 `tp_itemsize` 字段不为零。 对于具有固定长度实例的类型，所有实例的大小都相同，具体大小由 `tp_basicsize` 给出。

对于具有可变长度实例的类型，实例必须有一个 `ob_size` 字段，实例大小为 `tp_basicsize` 加上 N 乘以 `tp_itemsize`，其中 N 是对象的“长度”。 N 的值通常存储在实例的 `ob_size` 字段中。 但也有例外：举例来说，整数类型使用负的 `ob_size` 来表示负数，N 在这里就是 `abs(ob_size)`。 此外，在实例布局中存在 `ob_size` 字段并不意味着实例结构是可变长度的（例如，列表类型的结构体有固定长度的实例，但这些实例却包含一个有意义的 `ob_size` 字段）。

基本大小包括由宏 [`PyObject_HEAD`](structures.md#c.PyObject_HEAD "PyObject_HEAD") 或 [`PyObject_VAR_HEAD`](structures.md#c.PyObject_VAR_HEAD "PyObject_VAR_HEAD") （以用于声明实例结构的宏为准）声明的实例中的字段，如果存在 `_ob_prev` 和 `_ob_next` 字段则将相应地包括这些字段。 这意味着为 `tp_basicsize` 获取初始化器的唯一正确方式是在用于声明实例布局的结构上使用 `sizeof` 操作符。 基本大小不包括 GC 标头的大小。

关于对齐的说明：如果变量条目需要特定的对齐，则应通过 `tp_basicsize` 的值来处理。 例如：假设某个类型实现了一个 `double` 数组。 `tp_itemsize` 就是 `sizeof(double)`。 程序员有责任确保 `tp_basicsize` 是 `sizeof(double)` 的倍数（假设这是 `double` 的对齐要求）。

对于任何具有可变长度实例的类型，该字段不可为 `NULL`。

**继承：**

这些字段将由子类分别继承。 如果基本类型有一个非零的 `tp_itemsize`，那么在子类型中将 `tp_itemsize` 设置为不同的非零值通常是不安全的（不过这取决于该基本类型的具体实现）。

destructor [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_dealloc¶  
~~~
    

~~~
指向实例析构函数的指针。除非保证类型的实例永远不会被释放（就像单例对象 `None` 和 `Ellipsis` 那样），否则必须定义这个函数。函数声明如下：
~~~
    
    
~~~
void tp_dealloc(PyObject *self);
~~~

当新引用计数为零时，[`Py_DECREF()`](refcounting.md#c.Py_DECREF "Py_DECREF") 和 [`Py_XDECREF()`](refcounting.md#c.Py_XDECREF "Py_XDECREF") 宏会调用析构函数。 此时，实例仍然存在，但已没有了对它的引用。 析构函数应当释放该实例拥有的所有引用，释放实例拥有的所有内存缓冲区（使用与分配缓冲区时所用分配函数相对应的释放函数），并调用类型的 `tp_free` 函数。 如果该类型不可子类型化（未设置 `Py_TPFLAGS_BASETYPE` 旗标位），则允许直接调用对象的释放器而不必通过 `tp_free`。 对象的释放器应为分配实例时所使用的释放器；如果实例是使用 [`PyObject_New`](allocation.md#c.PyObject_New "PyObject_New") 或 [`PyObject_NewVar`](allocation.md#c.PyObject_NewVar "PyObject_NewVar") 分配的，则释放器通常为 [`PyObject_Del()`](allocation.md#c.PyObject_Del "PyObject_Del")；如果实例是使用 [`PyObject_GC_New`](gcsupport.md#c.PyObject_GC_New "PyObject_GC_New") 或 [`PyObject_GC_NewVar`](gcsupport.md#c.PyObject_GC_NewVar "PyObject_GC_NewVar") 分配的，则释放器通常为 [`PyObject_GC_Del()`](gcsupport.md#c.PyObject_GC_Del "PyObject_GC_Del")。

如果该类型支持垃圾回收（设置了 `Py_TPFLAGS_HAVE_GC` 旗标位），则析构器应在清除任何成员字段之前调用 [`PyObject_GC_UnTrack()`](gcsupport.md#c.PyObject_GC_UnTrack "PyObject_GC_UnTrack")。

    
    
~~~cpp
static void foo_dealloc(foo_object *self) {
    PyObject_GC_UnTrack(self);
    Py_CLEAR(self->ref);
    Py_TYPE(self)->tp_free((PyObject *)self);
}
~~~

最后，如果该类型是堆分配的 (`Py_TPFLAGS_HEAPTYPE`)，则在调用类型释放器后，释放器应释放对其类型对象的所有引用 (通过 [`Py_DECREF()`](refcounting.md#c.Py_DECREF "Py_DECREF"))。 为了避免悬空指针，建议的实现方式如下：

    
    
~~~cpp
static void foo_dealloc(foo_object *self) {
    PyTypeObject *tp = Py_TYPE(self);
    // free references and buffers here
    tp->tp_free(self);
    Py_DECREF(tp);
}
~~~

**继承：**

此字段会被子类型继承。

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_vectorcall_offset¶  

    

~~~
一个相对使用 [vectorcall 协议](call.md#vectorcall) 实现调用对象的实例级函数的可选的偏移量，这是一种比简单的 `tp_call` 更有效的替代选择。

该字段仅在设置了 `Py_TPFLAGS_HAVE_VECTORCALL` 旗标时使用。 在此情况下，它必须为一个包含 [`vectorcallfunc`](call.md#c.vectorcallfunc "vectorcallfunc") 指针实例中的偏移量的正整数。

The _vectorcallfunc_ pointer may be `NULL`, in which case the instance behaves as if `Py_TPFLAGS_HAVE_VECTORCALL` was not set: calling the instance falls back to `tp_call`.

Any class that sets `Py_TPFLAGS_HAVE_VECTORCALL` must also set `tp_call` and make sure its behaviour is consistent with the _vectorcallfunc_ function. This can be done by setting _tp_call_ to [`PyVectorcall_Call()`](call.md#c.PyVectorcall_Call "PyVectorcall_Call").

在 3.8 版本发生变更: Before version 3.8, this slot was named `tp_print`. In Python 2.x, it was used for printing to a file. In Python 3.0 to 3.7, it was unused.

在 3.12 版本发生变更: Before version 3.12, it was not recommended for mutable heap types to implement the vectorcall protocol. When a user sets [`__call__`](../reference/datamodel.md#object.__call__ "object.__call__") in Python code, only _tp_call_ is updated, likely making it inconsistent with the vectorcall function. Since 3.12, setting `__call__` will disable vectorcall optimization by clearing the `Py_TPFLAGS_HAVE_VECTORCALL` flag.

**继承：**

This field is always inherited. However, the `Py_TPFLAGS_HAVE_VECTORCALL` flag is not always inherited. If it's not set, then the subclass won't use [vectorcall](call.md#vectorcall), except when [`PyVectorcall_Call()`](call.md#c.PyVectorcall_Call "PyVectorcall_Call") is explicitly called.

getattrfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_getattr¶  
~~~
    

~~~
一个指向获取属性字符串函数的可选指针。

该字段已弃用。当它被定义时，应该和 `tp_getattro` 指向同一个函数，但接受一个C字符串参数表示属性名，而不是Python字符串对象。

**继承：**

分组: `tp_getattr`, `tp_getattro`

该字段会被子类和 `tp_getattro` 所继承：当子类型的 `tp_getattr` 和 `tp_getattro` 均为 `NULL` 时该子类型将从它的基类型同时继承 `tp_getattr` 和 `tp_getattro`。

setattrfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_setattr¶  
~~~
    

~~~
一个指向函数以便设置和删除属性的可选指针。

该字段已弃用。当它被定义时，应该和 `tp_setattro` 指向同一个函数，但接受一个C字符串参数表示属性名，而不是Python字符串对象。

**继承：**

分组: `tp_setattr`, `tp_setattro`

该字段会被子类型和 `tp_setattro` 所继承：当子类型的 `tp_setattr` 和 `tp_setattro` 均为 `NULL` 时该子类型将同时从它的基类型继承 `tp_setattr` 和 `tp_setattro`。

PyAsyncMethods *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_as_async¶  
~~~
    

~~~
指向一个包含仅与在 C 层级上实现 [awaitable](../glossary.md#term-awaitable) 和 [asynchronous iterator](../glossary.md#term-asynchronous-iterator) 协议的对象相关联的字段的附加结构体。 请参阅 Async Object Structures 了解详情。

在 3.5 版本加入: 在之前被称为 `tp_compare` 和 `tp_reserved`。

**继承：**

`tp_as_async` 字段不会被继承，但所包含的字段会被单独继承。

reprfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_repr¶  
~~~
    

~~~
一个实现了内置函数 [`repr()`](../library/functions.md#repr "repr") 的函数的可选指针。

该签名与 [`PyObject_Repr()`](object.md#c.PyObject_Repr "PyObject_Repr") 的相同:
~~~
    
    
~~~
PyObject *tp_repr(PyObject *self);
~~~

该函数必须返回一个字符串或 Unicode 对象。 在理想情况下，该函数应当返回一个字符串，当将其传给 [`eval()`](functions.md#eval "eval") 时，只要有合适的环境，就会返回一个具有相同值的对象。 如果这不可行，则它应当返回一个以 `'<'` 开头并以 `'>'` 结尾的可被用来推断出对象的类型和值的字符串。

**继承：**

此字段会被子类型继承。

**默认：**

When this field is not set, a string of the form `<%s object at %p>` is returned, where `%s` is replaced by the type name, and `%p` by the object's memory address.

PyNumberMethods *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_as_number¶  

    

~~~
Pointer to an additional structure that contains fields relevant only to objects which implement the number protocol. These fields are documented in Number Object Structures.

**继承：**

The `tp_as_number` field is not inherited, but the contained fields are inherited individually.

PySequenceMethods *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_as_sequence¶  
~~~
    

~~~
Pointer to an additional structure that contains fields relevant only to objects which implement the sequence protocol. These fields are documented in Sequence Object Structures.

**继承：**

The `tp_as_sequence` field is not inherited, but the contained fields are inherited individually.

PyMappingMethods *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_as_mapping¶  
~~~
    

~~~
Pointer to an additional structure that contains fields relevant only to objects which implement the mapping protocol. These fields are documented in Mapping Object Structures.

**继承：**

The `tp_as_mapping` field is not inherited, but the contained fields are inherited individually.

hashfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_hash¶  
~~~
    

~~~
An optional pointer to a function that implements the built-in function [`hash()`](../library/functions.md#hash "hash").

The signature is the same as for [`PyObject_Hash()`](object.md#c.PyObject_Hash "PyObject_Hash"):
~~~
    
    
~~~
Py_hash_t tp_hash(PyObject *);
~~~

The value `-1` should not be returned as a normal return value; when an error occurs during the computation of the hash value, the function should set an exception and return `-1`.

When this field is not set ( _and_ `tp_richcompare` is not set), an attempt to take the hash of the object raises [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError"). This is the same as setting it to [`PyObject_HashNotImplemented()`](object.md#c.PyObject_HashNotImplemented "PyObject_HashNotImplemented").

This field can be set explicitly to [`PyObject_HashNotImplemented()`](object.md#c.PyObject_HashNotImplemented "PyObject_HashNotImplemented") to block inheritance of the hash method from a parent type. This is interpreted as the equivalent of `__hash__ = None` at the Python level, causing `isinstance(o, collections.Hashable)` to correctly return `False`. Note that the converse is also true - setting `__hash__ = None` on a class at the Python level will result in the `tp_hash` slot being set to [`PyObject_HashNotImplemented()`](object.md#c.PyObject_HashNotImplemented "PyObject_HashNotImplemented").

**继承：**

Group: `tp_hash`, `tp_richcompare`

This field is inherited by subtypes together with `tp_richcompare`: a subtype inherits both of `tp_richcompare` and `tp_hash`, when the subtype's `tp_richcompare` and `tp_hash` are both `NULL`.

ternaryfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_call¶  

    

~~~
An optional pointer to a function that implements calling the object. This should be `NULL` if the object is not callable. The signature is the same as for [`PyObject_Call()`](call.md#c.PyObject_Call "PyObject_Call"):
~~~
    
    
~~~
PyObject *tp_call(PyObject *self, PyObject *args, PyObject *kwargs);
~~~

**继承：**

此字段会被子类型继承。

reprfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_str¶  

    

~~~
An optional pointer to a function that implements the built-in operation [`str()`](../library/stdtypes.md#str "str"). (Note that [`str`](../library/stdtypes.md#str "str") is a type now, and [`str()`](../library/stdtypes.md#str "str") calls the constructor for that type. This constructor calls [`PyObject_Str()`](object.md#c.PyObject_Str "PyObject_Str") to do the actual work, and [`PyObject_Str()`](object.md#c.PyObject_Str "PyObject_Str") will call this handler.)

The signature is the same as for [`PyObject_Str()`](object.md#c.PyObject_Str "PyObject_Str"):
~~~
    
    
~~~
PyObject *tp_str(PyObject *self);
~~~

The function must return a string or a Unicode object. It should be a "friendly" string representation of the object, as this is the representation that will be used, among other things, by the [`print()`](functions.md#print "print") function.

**继承：**

此字段会被子类型继承。

**默认：**

When this field is not set, [`PyObject_Repr()`](object.md#c.PyObject_Repr "PyObject_Repr") is called to return a string representation.

getattrofunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_getattro¶  

    

~~~
An optional pointer to the get-attribute function.

The signature is the same as for [`PyObject_GetAttr()`](object.md#c.PyObject_GetAttr "PyObject_GetAttr"):
~~~
    
    
~~~
PyObject *tp_getattro(PyObject *self, PyObject *attr);
~~~

It is usually convenient to set this field to [`PyObject_GenericGetAttr()`](object.md#c.PyObject_GenericGetAttr "PyObject_GenericGetAttr"), which implements the normal way of looking for object attributes.

**继承：**

分组: `tp_getattr`, `tp_getattro`

This field is inherited by subtypes together with `tp_getattr`: a subtype inherits both `tp_getattr` and `tp_getattro` from its base type when the subtype's `tp_getattr` and `tp_getattro` are both `NULL`.

**默认：**

`PyBaseObject_Type` uses [`PyObject_GenericGetAttr()`](object.md#c.PyObject_GenericGetAttr "PyObject_GenericGetAttr").

setattrofunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_setattro¶  

    

~~~
一个指向函数以便设置和删除属性的可选指针。

The signature is the same as for [`PyObject_SetAttr()`](object.md#c.PyObject_SetAttr "PyObject_SetAttr"):
~~~
    
    
~~~
int tp_setattro(PyObject *self, PyObject *attr, PyObject *value);
~~~

In addition, setting _value_ to `NULL` to delete an attribute must be supported. It is usually convenient to set this field to [`PyObject_GenericSetAttr()`](object.md#c.PyObject_GenericSetAttr "PyObject_GenericSetAttr"), which implements the normal way of setting object attributes.

**继承：**

分组: `tp_setattr`, `tp_setattro`

This field is inherited by subtypes together with `tp_setattr`: a subtype inherits both `tp_setattr` and `tp_setattro` from its base type when the subtype's `tp_setattr` and `tp_setattro` are both `NULL`.

**默认：**

`PyBaseObject_Type` uses [`PyObject_GenericSetAttr()`](object.md#c.PyObject_GenericSetAttr "PyObject_GenericSetAttr").

PyBufferProcs *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_as_buffer¶  

    

~~~
Pointer to an additional structure that contains fields relevant only to objects which implement the buffer interface. These fields are documented in Buffer Object Structures.

**继承：**

The `tp_as_buffer` field is not inherited, but the contained fields are inherited individually.

unsigned long [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_flags¶  
~~~
    

~~~
This field is a bit mask of various flags. Some flags indicate variant semantics for certain situations; others are used to indicate that certain fields in the type object (or in the extension structures referenced via `tp_as_number`, `tp_as_sequence`, `tp_as_mapping`, and `tp_as_buffer`) that were historically not always present are valid; if such a flag bit is clear, the type fields it guards must not be accessed and must be considered to have a zero or `NULL` value instead.

**继承：**

Inheritance of this field is complicated. Most flag bits are inherited individually, i.e. if the base type has a flag bit set, the subtype inherits this flag bit. The flag bits that pertain to extension structures are strictly inherited if the extension structure is inherited, i.e. the base type's value of the flag bit is copied into the subtype together with a pointer to the extension structure. The `Py_TPFLAGS_HAVE_GC` flag bit is inherited together with the `tp_traverse` and `tp_clear` fields, i.e. if the `Py_TPFLAGS_HAVE_GC` flag bit is clear in the subtype and the `tp_traverse` and `tp_clear` fields in the subtype exist and have `NULL` values. .. XXX are most flag bits _really_ inherited individually?

**默认：**

`PyBaseObject_Type` 使用 `Py_TPFLAGS_DEFAULT | Py_TPFLAGS_BASETYPE`。

**位掩码:**

目前定义了以下位掩码；可以使用 `|` 运算符对它们进行 OR 运算以形成 `tp_flags` 字段的值。 宏 [`PyType_HasFeature()`](type.md#c.PyType_HasFeature "PyType_HasFeature") 接受一个类型和一个旗标值 _tp_ 和 _f_ ，并检查 `tp->tp_flags & f` 是否为非零值。

Py_TPFLAGS_HEAPTYPE¶  
~~~
    

~~~
This bit is set when the type object itself is allocated on the heap, for example, types created dynamically using [`PyType_FromSpec()`](type.md#c.PyType_FromSpec "PyType_FromSpec"). In this case, the `ob_type` field of its instances is considered a reference to the type, and the type object is INCREF'ed when a new instance is created, and DECREF'ed when an instance is destroyed (this does not apply to instances of subtypes; only the type referenced by the instance's ob_type gets INCREF'ed or DECREF'ed).

**继承：**

？？？

Py_TPFLAGS_BASETYPE¶  
~~~
    

~~~
当此类型可被用作另一个类型的基类型时该比特位将被设置。 如果该比特位被清除，则此类型将无法被子类型化（类似于 Java 中的 "final" 类）。

**继承：**

？？？

Py_TPFLAGS_READY¶  
~~~
    

~~~
当此类型对象通过 [`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready") 被完全实例化时该比特位将被设置。

**继承：**

？？？

Py_TPFLAGS_READYING¶  
~~~
    

~~~
当 [`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready") 处在初始化此类型对象过程中时该比特位将被设置。

**继承：**

？？？

Py_TPFLAGS_HAVE_GC¶  
~~~
    

~~~
This bit is set when the object supports garbage collection. If this bit is set, instances must be created using [`PyObject_GC_New`](gcsupport.md#c.PyObject_GC_New "PyObject_GC_New") and destroyed using [`PyObject_GC_Del()`](gcsupport.md#c.PyObject_GC_Del "PyObject_GC_Del"). More information in section [使对象类型支持循环垃圾回收](gcsupport.md#supporting-cycle-detection). This bit also implies that the GC-related fields `tp_traverse` and `tp_clear` are present in the type object.

**继承：**

Group: `Py_TPFLAGS_HAVE_GC`, `tp_traverse`, `tp_clear`

The `Py_TPFLAGS_HAVE_GC` flag bit is inherited together with the `tp_traverse` and `tp_clear` fields, i.e. if the `Py_TPFLAGS_HAVE_GC` flag bit is clear in the subtype and the `tp_traverse` and `tp_clear` fields in the subtype exist and have `NULL` values.

Py_TPFLAGS_DEFAULT¶  
~~~
    

~~~
This is a bitmask of all the bits that pertain to the existence of certain fields in the type object and its extension structures. Currently, it includes the following bits: `Py_TPFLAGS_HAVE_STACKLESS_EXTENSION`.

**继承：**

？？？

Py_TPFLAGS_METHOD_DESCRIPTOR¶  
~~~
    

~~~
This bit indicates that objects behave like unbound methods.

If this flag is set for `type(meth)`, then:

  * `meth.__get__(obj, cls)(*args, **kwds)` (with `obj` not None) must be equivalent to `meth(obj, *args, **kwds)`.

  * `meth.__get__(None, cls)(*args, **kwds)` must be equivalent to `meth(*args, **kwds)`.

This flag enables an optimization for typical method calls like `obj.meth()`: it avoids creating a temporary "bound method" object for `obj.meth`.

在 3.8 版本加入.

**继承：**

This flag is never inherited by types without the `Py_TPFLAGS_IMMUTABLETYPE` flag set. For extension types, it is inherited whenever `tp_descr_get` is inherited.

Py_TPFLAGS_MANAGED_DICT¶  
~~~
    

~~~
This bit indicates that instances of the class have a `__dict__` attribute, and that the space for the dictionary is managed by the VM.

If this flag is set, `Py_TPFLAGS_HAVE_GC` should also be set.

在 3.12 版本加入.

**继承：**

This flag is inherited unless the `tp_dictoffset` field is set in a superclass.

Py_TPFLAGS_MANAGED_WEAKREF¶  
~~~
    

~~~
This bit indicates that instances of the class should be weakly referenceable.

在 3.12 版本加入.

**继承：**

This flag is inherited unless the `tp_weaklistoffset` field is set in a superclass.

Py_TPFLAGS_ITEMS_AT_END¶  
~~~
    

~~~
Only usable with variable-size types, i.e. ones with non-zero `tp_itemsize`.

Indicates that the variable-sized portion of an instance of this type is at the end of the instance's memory area, at an offset of `Py_TYPE(obj)->tp_basicsize` (which may be different in each subclass).

When setting this flag, be sure that all superclasses either use this memory layout, or are not variable-sized. Python does not check this.

在 3.12 版本加入.

**继承：**

This flag is inherited.

Py_TPFLAGS_LONG_SUBCLASS¶  
~~~
    

~~~
Py_TPFLAGS_LIST_SUBCLASS¶  
~~~
    

~~~
Py_TPFLAGS_TUPLE_SUBCLASS¶  
~~~
    

~~~
Py_TPFLAGS_BYTES_SUBCLASS¶  
~~~
    

~~~
Py_TPFLAGS_UNICODE_SUBCLASS¶  
~~~
    

~~~
Py_TPFLAGS_DICT_SUBCLASS¶  
~~~
    

~~~
Py_TPFLAGS_BASE_EXC_SUBCLASS¶  
~~~
    

~~~
Py_TPFLAGS_TYPE_SUBCLASS¶  
~~~
    

~~~
These flags are used by functions such as [`PyLong_Check()`](long.md#c.PyLong_Check "PyLong_Check") to quickly determine if a type is a subclass of a built-in type; such specific checks are faster than a generic check, like [`PyObject_IsInstance()`](object.md#c.PyObject_IsInstance "PyObject_IsInstance"). Custom types that inherit from built-ins should have their `tp_flags` set appropriately, or the code that interacts with such types will behave differently depending on what kind of check is used.

Py_TPFLAGS_HAVE_FINALIZE¶  
~~~
    

~~~
当类型结构体中存在 `tp_finalize` 槽位时会设置这个比特位。

在 3.4 版本加入.

自 3.8 版本弃用: 此旗标已不再是必要的，因为解释器会假定类型结构体中总是存在 `tp_finalize` 槽位。

Py_TPFLAGS_HAVE_VECTORCALL¶  
~~~
    

~~~
当类实现了 [vectorcall 协议](call.md#vectorcall) 时会设置这个比特位。 请参阅 `tp_vectorcall_offset` 了解详情。

**继承：**

如果继承了 `tp_call` 则也会继承这个比特位。

在 3.9 版本加入.

在 3.12 版本发生变更: This flag is now removed from a class when the class's [`__call__()`](../reference/datamodel.md#object.__call__ "object.__call__") method is reassigned.

This flag can now be inherited by mutable classes.

Py_TPFLAGS_IMMUTABLETYPE¶  
~~~
    

~~~
不可变的类型对象会设置这个比特位：类型属性无法被设置或删除。

[`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready") 会自动对 静态类型 应用这个旗标。

**继承：**

这个旗标不会被继承。

在 3.10 版本加入.

Py_TPFLAGS_DISALLOW_INSTANTIATION¶  
~~~
    

~~~
不允许创建此类型的实例：将 `tp_new` 设为 NULL 并且不会在类型字符中创建 `__new__` 键。

这个旗标必须在创建该类型之前设置，而不是在之后。 例如，它必须在该类型调用 [`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready") 之前被设置。

The flag is set automatically on static types if `tp_base` is NULL or `&PyBaseObject_Type` and `tp_new` is NULL.

**继承：**

This flag is not inherited. However, subclasses will not be instantiable unless they provide a non-NULL `tp_new` (which is only possible via the C API).

备注

要禁止直接实例化一个类但允许实例化其子类 (例如对于 [abstract base class](../glossary.md#term-abstract-base-class))，请勿使用此旗标。 替代的做法是，让 `tp_new` 只对子类可用。

在 3.10 版本加入.

Py_TPFLAGS_MAPPING¶  
~~~
    

~~~
This bit indicates that instances of the class may match mapping patterns when used as the subject of a [`match`](../reference/compound_stmts.md#match) block. It is automatically set when registering or subclassing [`collections.abc.Mapping`](../library/collections.abc.md#collections.abc.Mapping "collections.abc.Mapping"), and unset when registering [`collections.abc.Sequence`](../library/collections.abc.md#collections.abc.Sequence "collections.abc.Sequence").

备注

`Py_TPFLAGS_MAPPING` and `Py_TPFLAGS_SEQUENCE` are mutually exclusive; it is an error to enable both flags simultaneously.

**继承：**

This flag is inherited by types that do not already set `Py_TPFLAGS_SEQUENCE`.

参见

[**PEP 634**](https://peps.python.org/pep-0634/) —— 结构化模式匹配：规范

在 3.10 版本加入.

Py_TPFLAGS_SEQUENCE¶  
~~~
    

~~~
This bit indicates that instances of the class may match sequence patterns when used as the subject of a [`match`](../reference/compound_stmts.md#match) block. It is automatically set when registering or subclassing [`collections.abc.Sequence`](../library/collections.abc.md#collections.abc.Sequence "collections.abc.Sequence"), and unset when registering [`collections.abc.Mapping`](../library/collections.abc.md#collections.abc.Mapping "collections.abc.Mapping").

备注

`Py_TPFLAGS_MAPPING` and `Py_TPFLAGS_SEQUENCE` are mutually exclusive; it is an error to enable both flags simultaneously.

**继承：**

This flag is inherited by types that do not already set `Py_TPFLAGS_MAPPING`.

参见

[**PEP 634**](https://peps.python.org/pep-0634/) —— 结构化模式匹配：规范

在 3.10 版本加入.

Py_TPFLAGS_VALID_VERSION_TAG¶  
~~~
    

~~~
Internal. Do not set or unset this flag. To indicate that a class has changed call [`PyType_Modified()`](type.md#c.PyType_Modified "PyType_Modified")

警告

This flag is present in header files, but is an internal feature and should not be used. It will be removed in a future version of CPython

const char *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_doc¶  
~~~
    

~~~
An optional pointer to a NUL-terminated C string giving the docstring for this type object. This is exposed as the `__doc__` attribute on the type and instances of the type.

**继承：**

This field is _not_ inherited by subtypes.

[traverseproc](gcsupport.md#c.traverseproc "traverseproc") [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_traverse¶  
~~~
    

~~~
An optional pointer to a traversal function for the garbage collector. This is only used if the `Py_TPFLAGS_HAVE_GC` flag bit is set. The signature is:
~~~
    
    
~~~
int tp_traverse(PyObject *self, visitproc visit, void *arg);
~~~

More information about Python's garbage collection scheme can be found in section [使对象类型支持循环垃圾回收](gcsupport.md#supporting-cycle-detection).

The `tp_traverse` pointer is used by the garbage collector to detect reference cycles. A typical implementation of a `tp_traverse` function simply calls [`Py_VISIT()`](gcsupport.md#c.Py_VISIT "Py_VISIT") on each of the instance's members that are Python objects that the instance owns. For example, this is function `local_traverse()` from the `_thread` extension module:

    
    
~~~cpp
static int
local_traverse(localobject *self, visitproc visit, void *arg)
{
    Py_VISIT(self->args);
    Py_VISIT(self->kw);
    Py_VISIT(self->dict);
    return 0;
}
~~~

Note that [`Py_VISIT()`](gcsupport.md#c.Py_VISIT "Py_VISIT") is called only on those members that can participate in reference cycles. Although there is also a `self->key` member, it can only be `NULL` or a Python string and therefore cannot be part of a reference cycle.

On the other hand, even if you know a member can never be part of a cycle, as a debugging aid you may want to visit it anyway just so the [`gc`](gc.md#module-gc "gc: Interface to the cycle-detecting garbage collector.") module's [`get_referents()`](gc.md#gc.get_referents "gc.get_referents") function will include it.

警告

When implementing `tp_traverse`, only the members that the instance _owns_ (by having [strong references](../glossary.md#term-strong-reference) to them) must be visited. For instance, if an object supports weak references via the `tp_weaklist` slot, the pointer supporting the linked list (what _tp_weaklist_ points to) must **not** be visited as the instance does not directly own the weak references to itself (the weakreference list is there to support the weak reference machinery, but the instance has no strong reference to the elements inside it, as they are allowed to be removed even if the instance is still alive).

Note that [`Py_VISIT()`](gcsupport.md#c.Py_VISIT "Py_VISIT") requires the _visit_ and _arg_ parameters to `local_traverse()` to have these specific names; don't name them just anything.

Instances of heap-allocated types hold a reference to their type. Their traversal function must therefore either visit [`Py_TYPE(self)`](structures.md#c.Py_TYPE "Py_TYPE"), or delegate this responsibility by calling `tp_traverse` of another heap-allocated type (such as a heap-allocated superclass). If they do not, the type object may not be garbage-collected.

在 3.9 版本发生变更: Heap-allocated types are expected to visit `Py_TYPE(self)` in `tp_traverse`. In earlier versions of Python, due to [bug 40217](https://bugs.python.org/issue40217), doing this may lead to crashes in subclasses.

**继承：**

Group: `Py_TPFLAGS_HAVE_GC`, `tp_traverse`, `tp_clear`

This field is inherited by subtypes together with `tp_clear` and the `Py_TPFLAGS_HAVE_GC` flag bit: the flag bit, `tp_traverse`, and `tp_clear` are all inherited from the base type if they are all zero in the subtype.

[inquiry](gcsupport.md#c.inquiry "inquiry") [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_clear¶  

    

~~~
An optional pointer to a clear function for the garbage collector. This is only used if the `Py_TPFLAGS_HAVE_GC` flag bit is set. The signature is:
~~~
    
    
~~~
int tp_clear(PyObject *);
~~~

The `tp_clear` member function is used to break reference cycles in cyclic garbage detected by the garbage collector. Taken together, all `tp_clear` functions in the system must combine to break all reference cycles. This is subtle, and if in any doubt supply a `tp_clear` function. For example, the tuple type does not implement a `tp_clear` function, because it's possible to prove that no reference cycle can be composed entirely of tuples. Therefore the `tp_clear` functions of other types must be sufficient to break any cycle containing a tuple. This isn't immediately obvious, and there's rarely a good reason to avoid implementing `tp_clear`.

Implementations of `tp_clear` should drop the instance's references to those of its members that may be Python objects, and set its pointers to those members to `NULL`, as in the following example:

    
    
~~~cpp
static int
local_clear(localobject *self)
{
    Py_CLEAR(self->key);
    Py_CLEAR(self->args);
    Py_CLEAR(self->kw);
    Py_CLEAR(self->dict);
    return 0;
}
~~~

The [`Py_CLEAR()`](refcounting.md#c.Py_CLEAR "Py_CLEAR") macro should be used, because clearing references is delicate: the reference to the contained object must not be released (via [`Py_DECREF()`](refcounting.md#c.Py_DECREF "Py_DECREF")) until after the pointer to the contained object is set to `NULL`. This is because releasing the reference may cause the contained object to become trash, triggering a chain of reclamation activity that may include invoking arbitrary Python code (due to finalizers, or weakref callbacks, associated with the contained object). If it's possible for such code to reference _self_ again, it's important that the pointer to the contained object be `NULL` at that time, so that _self_ knows the contained object can no longer be used. The [`Py_CLEAR()`](refcounting.md#c.Py_CLEAR "Py_CLEAR") macro performs the operations in a safe order.

Note that `tp_clear` is not _always_ called before an instance is deallocated. For example, when reference counting is enough to determine that an object is no longer used, the cyclic garbage collector is not involved and `tp_dealloc` is called directly.

Because the goal of `tp_clear` functions is to break reference cycles, it's not necessary to clear contained objects like Python strings or Python integers, which can't participate in reference cycles. On the other hand, it may be convenient to clear all contained Python objects, and write the type's `tp_dealloc` function to invoke `tp_clear`.

More information about Python's garbage collection scheme can be found in section [使对象类型支持循环垃圾回收](gcsupport.md#supporting-cycle-detection).

**继承：**

Group: `Py_TPFLAGS_HAVE_GC`, `tp_traverse`, `tp_clear`

This field is inherited by subtypes together with `tp_traverse` and the `Py_TPFLAGS_HAVE_GC` flag bit: the flag bit, `tp_traverse`, and `tp_clear` are all inherited from the base type if they are all zero in the subtype.

richcmpfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_richcompare¶  

    

~~~
An optional pointer to the rich comparison function, whose signature is:
~~~
    
    
~~~
PyObject *tp_richcompare(PyObject *self, PyObject *other, int op);
~~~

The first parameter is guaranteed to be an instance of the type that is defined by [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject").

The function should return the result of the comparison (usually `Py_True` or `Py_False`). If the comparison is undefined, it must return `Py_NotImplemented`, if another error occurred it must return `NULL` and set an exception condition.

The following constants are defined to be used as the third argument for `tp_richcompare` and for [`PyObject_RichCompare()`](object.md#c.PyObject_RichCompare "PyObject_RichCompare"):

常量

|

对照  
  
---|---  
  
Py_LT¶  

    
|

`<`  
  
Py_LE¶  

    
|

`<=`  
  
Py_EQ¶  

    
|

`==`  
  
Py_NE¶  

    
|

`!=`  
  
Py_GT¶  

    
|

`>`  
  
Py_GE¶  

    
|

`>=`  
  
定义以下宏是为了简化编写丰富的比较函数：

Py_RETURN_RICHCOMPARE(VAL_A, VAL_B, op)¶  

    

~~~
Return `Py_True` or `Py_False` from the function, depending on the result of a comparison. VAL_A and VAL_B must be orderable by C comparison operators (for example, they may be C ints or floats). The third argument specifies the requested operation, as for [`PyObject_RichCompare()`](object.md#c.PyObject_RichCompare "PyObject_RichCompare").

The returned value is a new [strong reference](../glossary.md#term-strong-reference).

On error, sets an exception and returns `NULL` from the function.

在 3.7 版本加入.

**继承：**

Group: `tp_hash`, `tp_richcompare`

This field is inherited by subtypes together with `tp_hash`: a subtype inherits `tp_richcompare` and `tp_hash` when the subtype's `tp_richcompare` and `tp_hash` are both `NULL`.

**默认：**

`PyBaseObject_Type` provides a `tp_richcompare` implementation, which may be inherited. However, if only `tp_hash` is defined, not even the inherited function is used and instances of the type will not be able to participate in any comparisons.

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_weaklistoffset¶  
~~~
    

~~~
While this field is still supported, `Py_TPFLAGS_MANAGED_WEAKREF` should be used instead, if at all possible.

If the instances of this type are weakly referenceable, this field is greater than zero and contains the offset in the instance structure of the weak reference list head (ignoring the GC header, if present); this offset is used by [`PyObject_ClearWeakRefs()`](weakref.md#c.PyObject_ClearWeakRefs "PyObject_ClearWeakRefs") and the `PyWeakref_*` functions. The instance structure needs to include a field of type [PyObject](structures.md#c.PyObject "PyObject")* which is initialized to `NULL`.

Do not confuse this field with `tp_weaklist`; that is the list head for weak references to the type object itself.

It is an error to set both the `Py_TPFLAGS_MANAGED_WEAKREF` bit and `tp_weaklist`.

**继承：**

This field is inherited by subtypes, but see the rules listed below. A subtype may override this offset; this means that the subtype uses a different weak reference list head than the base type. Since the list head is always found via `tp_weaklistoffset`, this should not be a problem.

**默认：**

If the `Py_TPFLAGS_MANAGED_WEAKREF` bit is set in the `tp_dict` field, then `tp_weaklistoffset` will be set to a negative value, to indicate that it is unsafe to use this field.

getiterfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_iter¶  
~~~
    

~~~
一个可选的指向函数的指针，该函数返回对象的 [iterator](../glossary.md#term-iterator)。 它的存在通常表明该类型的实例为 [iterable](../glossary.md#term-iterable) (尽管序列在没有此函数的情况下也可能为可迭代对象)。

This function has the same signature as [`PyObject_GetIter()`](object.md#c.PyObject_GetIter "PyObject_GetIter"):
~~~
    
    
~~~
PyObject *tp_iter(PyObject *self);
~~~

**继承：**

此字段会被子类型继承。

iternextfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_iternext¶  

    

~~~
一个可选的指向函数的指针，该函数返回 [iterator](../glossary.md#term-iterator) 中的下一项。 其签名为:
~~~
    
    
~~~
PyObject *tp_iternext(PyObject *self);
~~~

When the iterator is exhausted, it must return `NULL`; a [`StopIteration`](3.标准库/exceptions.md#StopIteration "StopIteration") exception may or may not be set. When another error occurs, it must return `NULL` too. Its presence signals that the instances of this type are iterators.

Iterator types should also define the `tp_iter` function, and that function should return the iterator instance itself (not a new iterator instance).

This function has the same signature as [`PyIter_Next()`](iter.md#c.PyIter_Next "PyIter_Next").

**继承：**

此字段会被子类型继承。

struct [PyMethodDef](structures.md#c.PyMethodDef "PyMethodDef") *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_methods¶  

    

~~~
An optional pointer to a static `NULL`-terminated array of [`PyMethodDef`](structures.md#c.PyMethodDef "PyMethodDef") structures, declaring regular methods of this type.

For each entry in the array, an entry is added to the type's dictionary (see `tp_dict` below) containing a method descriptor.

**继承：**

This field is not inherited by subtypes (methods are inherited through a different mechanism).

struct [PyMemberDef](structures.md#c.PyMemberDef "PyMemberDef") *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_members¶  
~~~
    

~~~
An optional pointer to a static `NULL`-terminated array of [`PyMemberDef`](structures.md#c.PyMemberDef "PyMemberDef") structures, declaring regular data members (fields or slots) of instances of this type.

For each entry in the array, an entry is added to the type's dictionary (see `tp_dict` below) containing a member descriptor.

**继承：**

This field is not inherited by subtypes (members are inherited through a different mechanism).

struct [PyGetSetDef](structures.md#c.PyGetSetDef "PyGetSetDef") *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_getset¶  
~~~
    

~~~
An optional pointer to a static `NULL`-terminated array of [`PyGetSetDef`](structures.md#c.PyGetSetDef "PyGetSetDef") structures, declaring computed attributes of instances of this type.

For each entry in the array, an entry is added to the type's dictionary (see `tp_dict` below) containing a getset descriptor.

**继承：**

This field is not inherited by subtypes (computed attributes are inherited through a different mechanism).

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_base¶  
~~~
    

~~~
An optional pointer to a base type from which type properties are inherited. At this level, only single inheritance is supported; multiple inheritance require dynamically creating a type object by calling the metatype.

备注

Slot initialization is subject to the rules of initializing globals. C99 requires the initializers to be "address constants". Function designators like [`PyType_GenericNew()`](type.md#c.PyType_GenericNew "PyType_GenericNew"), with implicit conversion to a pointer, are valid C99 address constants.

However, the unary '&' operator applied to a non-static variable like `PyBaseObject_Type` is not required to produce an address constant. Compilers may support this (gcc does), MSVC does not. Both compilers are strictly standard conforming in this particular behavior.

Consequently, `tp_base` should be set in the extension module's init function.

**继承：**

This field is not inherited by subtypes (obviously).

**默认：**

This field defaults to `&PyBaseObject_Type` (which to Python programmers is known as the type [`object`](../library/functions.md#object "object")).

[PyObject](structures.md#c.PyObject "PyObject") *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_dict¶  
~~~
    

~~~
The type's dictionary is stored here by [`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready").

This field should normally be initialized to `NULL` before PyType_Ready is called; it may also be initialized to a dictionary containing initial attributes for the type. Once [`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready") has initialized the type, extra attributes for the type may be added to this dictionary only if they don't correspond to overloaded operations (like [`__add__()`](../reference/datamodel.md#object.__add__ "object.__add__")). Once initialization for the type has finished, this field should be treated as read-only.

Some types may not store their dictionary in this slot. Use [`PyType_GetDict()`](type.md#c.PyType_GetDict "PyType_GetDict") to retrieve the dictionary for an arbitrary type.

在 3.12 版本发生变更: Internals detail: For static builtin types, this is always `NULL`. Instead, the dict for such types is stored on `PyInterpreterState`. Use [`PyType_GetDict()`](type.md#c.PyType_GetDict "PyType_GetDict") to get the dict for an arbitrary type.

**继承：**

This field is not inherited by subtypes (though the attributes defined in here are inherited through a different mechanism).

**默认：**

If this field is `NULL`, [`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready") will assign a new dictionary to it.

警告

It is not safe to use [`PyDict_SetItem()`](dict.md#c.PyDict_SetItem "PyDict_SetItem") on or otherwise modify `tp_dict` with the dictionary C-API.

descrgetfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_descr_get¶  
~~~
    

~~~
An optional pointer to a "descriptor get" function.

函数的签名为:
~~~
    
    
~~~
PyObject * tp_descr_get(PyObject *self, PyObject *obj, PyObject *type);
~~~

**继承：**

此字段会被子类型继承。

descrsetfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_descr_set¶  

    

~~~
An optional pointer to a function for setting and deleting a descriptor's value.

函数的签名为:
~~~
    
    
~~~
int tp_descr_set(PyObject *self, PyObject *obj, PyObject *value);
~~~

The _value_ argument is set to `NULL` to delete the value.

**继承：**

此字段会被子类型继承。

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_dictoffset¶  

    

~~~
While this field is still supported, `Py_TPFLAGS_MANAGED_DICT` should be used instead, if at all possible.

If the instances of this type have a dictionary containing instance variables, this field is non-zero and contains the offset in the instances of the type of the instance variable dictionary; this offset is used by [`PyObject_GenericGetAttr()`](object.md#c.PyObject_GenericGetAttr "PyObject_GenericGetAttr").

Do not confuse this field with `tp_dict`; that is the dictionary for attributes of the type object itself.

The value specifies the offset of the dictionary from the start of the instance structure.

The `tp_dictoffset` should be regarded as write-only. To get the pointer to the dictionary call [`PyObject_GenericGetDict()`](object.md#c.PyObject_GenericGetDict "PyObject_GenericGetDict"). Calling [`PyObject_GenericGetDict()`](object.md#c.PyObject_GenericGetDict "PyObject_GenericGetDict") may need to allocate memory for the dictionary, so it is may be more efficient to call [`PyObject_GetAttr()`](object.md#c.PyObject_GetAttr "PyObject_GetAttr") when accessing an attribute on the object.

It is an error to set both the `Py_TPFLAGS_MANAGED_WEAKREF` bit and `tp_dictoffset`.

**继承：**

This field is inherited by subtypes. A subtype should not override this offset; doing so could be unsafe, if C code tries to access the dictionary at the previous offset. To properly support inheritance, use `Py_TPFLAGS_MANAGED_DICT`.

**默认：**

This slot has no default. For static types, if the field is `NULL` then no [`__dict__`](../library/stdtypes.md#object.__dict__ "object.__dict__") gets created for instances.

If the `Py_TPFLAGS_MANAGED_DICT` bit is set in the `tp_dict` field, then `tp_dictoffset` will be set to `-1`, to indicate that it is unsafe to use this field.

initproc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_init¶  
~~~
    

~~~
An optional pointer to an instance initialization function.

This function corresponds to the [`__init__()`](../reference/datamodel.md#object.__init__ "object.__init__") method of classes. Like `__init__()`, it is possible to create an instance without calling `__init__()`, and it is possible to reinitialize an instance by calling its `__init__()` method again.

函数的签名为:
~~~
    
    
~~~
int tp_init(PyObject *self, PyObject *args, PyObject *kwds);
~~~

The self argument is the instance to be initialized; the _args_ and _kwds_ arguments represent positional and keyword arguments of the call to [`__init__()`](3.%20数据模型.md#object.__init__ "object.__init__").

The `tp_init` function, if not `NULL`, is called when an instance is created normally by calling its type, after the type's `tp_new` function has returned an instance of the type. If the `tp_new` function returns an instance of some other type that is not a subtype of the original type, no `tp_init` function is called; if `tp_new` returns an instance of a subtype of the original type, the subtype's `tp_init` is called.

成功时返回 `0`，发生错误时则返回 `-1` 并在错误上设置一个异常。and sets an exception on error.

**继承：**

此字段会被子类型继承。

**默认：**

对于 静态类型 来说该字段没有默认值。

allocfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_alloc¶  

    

~~~
指向一个实例分配函数的可选指针。

函数的签名为:
~~~
    
    
~~~
PyObject *tp_alloc(PyTypeObject *self, Py_ssize_t nitems);
~~~

**继承：**

This field is inherited by static subtypes, but not by dynamic subtypes (subtypes created by a class statement).

**默认：**

For dynamic subtypes, this field is always set to [`PyType_GenericAlloc()`](type.md#c.PyType_GenericAlloc "PyType_GenericAlloc"), to force a standard heap allocation strategy.

For static subtypes, `PyBaseObject_Type` uses [`PyType_GenericAlloc()`](type.md#c.PyType_GenericAlloc "PyType_GenericAlloc"). That is the recommended value for all statically defined types.

newfunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_new¶  

    

~~~
An optional pointer to an instance creation function.

函数的签名为:
~~~
    
    
~~~
PyObject *tp_new(PyTypeObject *subtype, PyObject *args, PyObject *kwds);
~~~

The _subtype_ argument is the type of the object being created; the _args_ and _kwds_ arguments represent positional and keyword arguments of the call to the type. Note that _subtype_ doesn't have to equal the type whose `tp_new` function is called; it may be a subtype of that type (but not an unrelated type).

The `tp_new` function should call `subtype->tp_alloc(subtype, nitems)` to allocate space for the object, and then do only as much further initialization as is absolutely necessary. Initialization that can safely be ignored or repeated should be placed in the `tp_init` handler. A good rule of thumb is that for immutable types, all initialization should take place in `tp_new`, while for mutable types, most initialization should be deferred to `tp_init`.

Set the `Py_TPFLAGS_DISALLOW_INSTANTIATION` flag to disallow creating instances of the type in Python.

**继承：**

This field is inherited by subtypes, except it is not inherited by static types whose `tp_base` is `NULL` or `&PyBaseObject_Type`.

**默认：**

For static types this field has no default. This means if the slot is defined as `NULL`, the type cannot be called to create new instances; presumably there is some other way to create instances, like a factory function.

freefunc [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_free¶  

    

~~~
An optional pointer to an instance deallocation function. Its signature is:
~~~
    
    
~~~
void tp_free(void *self);
~~~

An initializer that is compatible with this signature is [`PyObject_Free()`](memory.md#c.PyObject_Free "PyObject_Free").

**继承：**

This field is inherited by static subtypes, but not by dynamic subtypes (subtypes created by a class statement)

**默认：**

In dynamic subtypes, this field is set to a deallocator suitable to match [`PyType_GenericAlloc()`](type.md#c.PyType_GenericAlloc "PyType_GenericAlloc") and the value of the `Py_TPFLAGS_HAVE_GC` flag bit.

For static subtypes, `PyBaseObject_Type` uses [`PyObject_Del()`](allocation.md#c.PyObject_Del "PyObject_Del").

[inquiry](gcsupport.md#c.inquiry "inquiry") [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_is_gc¶  

    

~~~
可选的指向垃圾回收器所调用的函数的指针。

The garbage collector needs to know whether a particular object is collectible or not. Normally, it is sufficient to look at the object's type's `tp_flags` field, and check the `Py_TPFLAGS_HAVE_GC` flag bit. But some types have a mixture of statically and dynamically allocated instances, and the statically allocated instances are not collectible. Such types should define this function; it should return `1` for a collectible instance, and `0` for a non-collectible instance. The signature is:
~~~
    
    
~~~
int tp_is_gc(PyObject *self);
~~~

(The only example of this are types themselves. The metatype, [`PyType_Type`](type.md#c.PyType_Type "PyType_Type"), defines this function to distinguish between statically and dynamically allocated types.)

**继承：**

此字段会被子类型继承。

**默认：**

This slot has no default. If this field is `NULL`, `Py_TPFLAGS_HAVE_GC` is used as the functional equivalent.

[PyObject](structures.md#c.PyObject "PyObject") *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_bases¶  

    

~~~
Tuple of base types.

This field should be set to `NULL` and treated as read-only. Python will fill it in when the type is [`initialized`](type.md#c.PyType_Ready "PyType_Ready").

For dynamically created classes, the `Py_tp_bases` [`slot`](type.md#c.PyType_Slot "PyType_Slot") can be used instead of the _bases_ argument of [`PyType_FromSpecWithBases()`](type.md#c.PyType_FromSpecWithBases "PyType_FromSpecWithBases"). The argument form is preferred.

警告

Multiple inheritance does not work well for statically defined types. If you set `tp_bases` to a tuple, Python will not raise an error, but some slots will only be inherited from the first base.

**继承：**

这个字段不会被继承。

[PyObject](structures.md#c.PyObject "PyObject") *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_mro¶  
~~~
    

~~~
包含基类型的扩展集的元组，以类型本身开始并以 [`object`](../library/functions.md#object "object") 作为结束，使用方法解析顺序。

This field should be set to `NULL` and treated as read-only. Python will fill it in when the type is [`initialized`](type.md#c.PyType_Ready "PyType_Ready").

**继承：**

这个字段不会被继承；它是通过 [`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready") 计算得到的。

[PyObject](structures.md#c.PyObject "PyObject") *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_cache¶  
~~~
    

~~~
尚未使用。 仅供内部使用。

**继承：**

这个字段不会被继承。

void *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_subclasses¶  
~~~
    

~~~
A collection of subclasses. Internal use only. May be an invalid pointer.

To get a list of subclasses, call the Python method [`__subclasses__()`](../library/stdtypes.md#class.__subclasses__ "class.__subclasses__").

在 3.12 版本发生变更: For some types, this field does not hold a valid [PyObject](structures.md#c.PyObject "PyObject")*. The type was changed to void* to indicate this.

**继承：**

这个字段不会被继承。

[PyObject](structures.md#c.PyObject "PyObject") *[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_weaklist¶  
~~~
    

~~~
Weak reference list head, for weak references to this type object. Not inherited. Internal use only.

在 3.12 版本发生变更: Internals detail: For the static builtin types this is always `NULL`, even if weakrefs are added. Instead, the weakrefs for each are stored on `PyInterpreterState`. Use the public C-API or the internal `_PyObject_GET_WEAKREFS_LISTPTR()` macro to avoid the distinction.

**继承：**

这个字段不会被继承。

destructor [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_del¶  
~~~
    

~~~
This field is deprecated. Use `tp_finalize` instead.

unsigned int [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_version_tag¶  
~~~
    

~~~
Used to index into the method cache. Internal use only.

**继承：**

这个字段不会被继承。

destructor [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_finalize¶  
~~~
    

~~~
An optional pointer to an instance finalization function. Its signature is:
~~~
    
    
~~~
void tp_finalize(PyObject *self);
~~~

If `tp_finalize` is set, the interpreter calls it once when finalizing an instance. It is called either from the garbage collector (if the instance is part of an isolated reference cycle) or just before the object is deallocated. Either way, it is guaranteed to be called before attempting to break reference cycles, ensuring that it finds the object in a sane state.

`tp_finalize` should not mutate the current exception status; therefore, a recommended way to write a non-trivial finalizer is:

    
    
~~~cpp
static void
local_finalize(PyObject *self)
{
    PyObject *error_type, *error_value, *error_traceback;

    /* Save the current exception, if any. */
    PyErr_Fetch(&error_type, &error_value, &error_traceback);

    /* ... */

    /* Restore the saved exception. */
    PyErr_Restore(error_type, error_value, error_traceback);
}
~~~

Also, note that, in a garbage collected Python, `tp_dealloc` may be called from any Python thread, not just the thread which created the object (if the object becomes part of a refcount cycle, that cycle might be collected by a garbage collection on any thread). This is not a problem for Python API calls, since the thread on which tp_dealloc is called will own the Global Interpreter Lock (GIL). However, if the object being destroyed in turn destroys objects from some other C or C++ library, care should be taken to ensure that destroying those objects on the thread which called tp_dealloc will not violate any assumptions of the library.

**继承：**

此字段会被子类型继承。

在 3.4 版本加入.

在 3.8 版本发生变更: Before version 3.8 it was necessary to set the `Py_TPFLAGS_HAVE_FINALIZE` flags bit in order for this field to be used. This is no longer required.

参见

"Safe object finalization" ([ **PEP 442**](https://peps.python.org/pep-0442/))

[vectorcallfunc](call.md#c.vectorcallfunc "vectorcallfunc") [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_vectorcall¶  

    

~~~
Vectorcall function to use for calls of this type object. In other words, it is used to implement [vectorcall](call.md#vectorcall) for `type.__call__`. If `tp_vectorcall` is `NULL`, the default call implementation using [`__new__()`](../reference/datamodel.md#object.__new__ "object.__new__") and [`__init__()`](../reference/datamodel.md#object.__init__ "object.__init__") is used.

**继承：**

这个字段不会被继承。

在 3.9 版本加入: （这个字段从 3.8 起即存在，但是从 3.9 开始投入使用）

unsigned char [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject").tp_watched¶  
~~~
    

~~~
Internal. Do not use.

在 3.12 版本加入.

## Static Types¶

Traditionally, types defined in C code are _static_ , that is, a static [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") structure is defined directly in code and initialized using [`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready").

This results in types that are limited relative to types defined in Python:

  * Static types are limited to one base, i.e. they cannot use multiple inheritance.

  * Static type objects (but not necessarily their instances) are immutable. It is not possible to add or modify the type object's attributes from Python.

  * Static type objects are shared across [sub-interpreters](init.md#sub-interpreter-support), so they should not include any subinterpreter-specific state.

Also, since [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") is only part of the [Limited API](stable.md#limited-c-api) as an opaque struct, any extension modules using static types must be compiled for a specific Python minor version.

## 堆类型¶

An alternative to static types is _heap-allocated types_ , or _heap types_ for short, which correspond closely to classes created by Python's `class` statement. Heap types have the `Py_TPFLAGS_HEAPTYPE` flag set.

This is done by filling a [`PyType_Spec`](type.md#c.PyType_Spec "PyType_Spec") structure and calling [`PyType_FromSpec()`](type.md#c.PyType_FromSpec "PyType_FromSpec"), [`PyType_FromSpecWithBases()`](type.md#c.PyType_FromSpecWithBases "PyType_FromSpecWithBases"), [`PyType_FromModuleAndSpec()`](type.md#c.PyType_FromModuleAndSpec "PyType_FromModuleAndSpec"), or [`PyType_FromMetaclass()`](type.md#c.PyType_FromMetaclass "PyType_FromMetaclass").

# Number Object Structures¶

type PyNumberMethods¶  
~~~
    

~~~
This structure holds pointers to the functions which an object uses to implement the number protocol. Each function is used by the function of similar name documented in the [数字协议](number.md#number) section.

Here is the structure definition:
~~~
    
    
~~~
typedef struct {
     binaryfunc nb_add;
     binaryfunc nb_subtract;
     binaryfunc nb_multiply;
     binaryfunc nb_remainder;
     binaryfunc nb_divmod;
     ternaryfunc nb_power;
     unaryfunc nb_negative;
     unaryfunc nb_positive;
     unaryfunc nb_absolute;
     inquiry nb_bool;
     unaryfunc nb_invert;
     binaryfunc nb_lshift;
     binaryfunc nb_rshift;
     binaryfunc nb_and;
     binaryfunc nb_xor;
     binaryfunc nb_or;
     unaryfunc nb_int;
     void *nb_reserved;
     unaryfunc nb_float;

     binaryfunc nb_inplace_add;
     binaryfunc nb_inplace_subtract;
     binaryfunc nb_inplace_multiply;
     binaryfunc nb_inplace_remainder;
     ternaryfunc nb_inplace_power;
     binaryfunc nb_inplace_lshift;
     binaryfunc nb_inplace_rshift;
     binaryfunc nb_inplace_and;
     binaryfunc nb_inplace_xor;
     binaryfunc nb_inplace_or;

     binaryfunc nb_floor_divide;
     binaryfunc nb_true_divide;
     binaryfunc nb_inplace_floor_divide;
     binaryfunc nb_inplace_true_divide;

     unaryfunc nb_index;

     binaryfunc nb_matrix_multiply;
     binaryfunc nb_inplace_matrix_multiply;
} PyNumberMethods;
~~~

备注

Binary and ternary functions must check the type of all their operands, and implement the necessary conversions (at least one of the operands is an instance of the defined type). If the operation is not defined for the given operands, binary and ternary functions must return `Py_NotImplemented`, if another error occurred they must return `NULL` and set an exception.

备注

The `nb_reserved` field should always be `NULL`. It was previously called `nb_long`, and was renamed in Python 3.0.1.

binaryfunc PyNumberMethods.nb_add¶  

    

~~~
binaryfunc PyNumberMethods.nb_subtract¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_multiply¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_remainder¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_divmod¶  
~~~
    

~~~
ternaryfunc PyNumberMethods.nb_power¶  
~~~
    

~~~
unaryfunc PyNumberMethods.nb_negative¶  
~~~
    

~~~
unaryfunc PyNumberMethods.nb_positive¶  
~~~
    

~~~
unaryfunc PyNumberMethods.nb_absolute¶  
~~~
    

~~~
[inquiry](gcsupport.md#c.inquiry "inquiry") PyNumberMethods.nb_bool¶  
~~~
    

~~~
unaryfunc PyNumberMethods.nb_invert¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_lshift¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_rshift¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_and¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_xor¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_or¶  
~~~
    

~~~
unaryfunc PyNumberMethods.nb_int¶  
~~~
    

~~~
void *PyNumberMethods.nb_reserved¶  
~~~
    

~~~
unaryfunc PyNumberMethods.nb_float¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_inplace_add¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_inplace_subtract¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_inplace_multiply¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_inplace_remainder¶  
~~~
    

~~~
ternaryfunc PyNumberMethods.nb_inplace_power¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_inplace_lshift¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_inplace_rshift¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_inplace_and¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_inplace_xor¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_inplace_or¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_floor_divide¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_true_divide¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_inplace_floor_divide¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_inplace_true_divide¶  
~~~
    

~~~
unaryfunc PyNumberMethods.nb_index¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_matrix_multiply¶  
~~~
    

~~~
binaryfunc PyNumberMethods.nb_inplace_matrix_multiply¶  
~~~
    

~~~
# Mapping Object Structures¶

type PyMappingMethods¶  
~~~
    

~~~
This structure holds pointers to the functions which an object uses to implement the mapping protocol. It has three members:

lenfunc PyMappingMethods.mp_length¶  
~~~
    

~~~
This function is used by [`PyMapping_Size()`](mapping.md#c.PyMapping_Size "PyMapping_Size") and [`PyObject_Size()`](object.md#c.PyObject_Size "PyObject_Size"), and has the same signature. This slot may be set to `NULL` if the object has no defined length.

binaryfunc PyMappingMethods.mp_subscript¶  
~~~
    

~~~
This function is used by [`PyObject_GetItem()`](object.md#c.PyObject_GetItem "PyObject_GetItem") and [`PySequence_GetSlice()`](sequence.md#c.PySequence_GetSlice "PySequence_GetSlice"), and has the same signature as `PyObject_GetItem()`. This slot must be filled for the [`PyMapping_Check()`](mapping.md#c.PyMapping_Check "PyMapping_Check") function to return `1`, it can be `NULL` otherwise.

objobjargproc PyMappingMethods.mp_ass_subscript¶  
~~~
    

~~~
This function is used by [`PyObject_SetItem()`](object.md#c.PyObject_SetItem "PyObject_SetItem"), [`PyObject_DelItem()`](object.md#c.PyObject_DelItem "PyObject_DelItem"), [`PySequence_SetSlice()`](sequence.md#c.PySequence_SetSlice "PySequence_SetSlice") and [`PySequence_DelSlice()`](sequence.md#c.PySequence_DelSlice "PySequence_DelSlice"). It has the same signature as `PyObject_SetItem()`, but _v_ can also be set to `NULL` to delete an item. If this slot is `NULL`, the object does not support item assignment and deletion.

# Sequence Object Structures¶

type PySequenceMethods¶  
~~~
    

~~~
This structure holds pointers to the functions which an object uses to implement the sequence protocol.

lenfunc PySequenceMethods.sq_length¶  
~~~
    

~~~
This function is used by [`PySequence_Size()`](sequence.md#c.PySequence_Size "PySequence_Size") and [`PyObject_Size()`](object.md#c.PyObject_Size "PyObject_Size"), and has the same signature. It is also used for handling negative indices via the `sq_item` and the `sq_ass_item` slots.

binaryfunc PySequenceMethods.sq_concat¶  
~~~
    

~~~
This function is used by [`PySequence_Concat()`](sequence.md#c.PySequence_Concat "PySequence_Concat") and has the same signature. It is also used by the `+` operator, after trying the numeric addition via the `nb_add` slot.

ssizeargfunc PySequenceMethods.sq_repeat¶  
~~~
    

~~~
This function is used by [`PySequence_Repeat()`](sequence.md#c.PySequence_Repeat "PySequence_Repeat") and has the same signature. It is also used by the `*` operator, after trying numeric multiplication via the `nb_multiply` slot.

ssizeargfunc PySequenceMethods.sq_item¶  
~~~
    

~~~
This function is used by [`PySequence_GetItem()`](sequence.md#c.PySequence_GetItem "PySequence_GetItem") and has the same signature. It is also used by [`PyObject_GetItem()`](object.md#c.PyObject_GetItem "PyObject_GetItem"), after trying the subscription via the `mp_subscript` slot. This slot must be filled for the [`PySequence_Check()`](sequence.md#c.PySequence_Check "PySequence_Check") function to return `1`, it can be `NULL` otherwise.

Negative indexes are handled as follows: if the `sq_length` slot is filled, it is called and the sequence length is used to compute a positive index which is passed to `sq_item`. If `sq_length` is `NULL`, the index is passed as is to the function.

ssizeobjargproc PySequenceMethods.sq_ass_item¶  
~~~
    

~~~
This function is used by [`PySequence_SetItem()`](sequence.md#c.PySequence_SetItem "PySequence_SetItem") and has the same signature. It is also used by [`PyObject_SetItem()`](object.md#c.PyObject_SetItem "PyObject_SetItem") and [`PyObject_DelItem()`](object.md#c.PyObject_DelItem "PyObject_DelItem"), after trying the item assignment and deletion via the `mp_ass_subscript` slot. This slot may be left to `NULL` if the object does not support item assignment and deletion.

objobjproc PySequenceMethods.sq_contains¶  
~~~
    

~~~
该函数可供 [`PySequence_Contains()`](sequence.md#c.PySequence_Contains "PySequence_Contains") 使用并具有相同的签名。 此槽位可以保持为 `NULL`，在此情况下 `PySequence_Contains()` 只需遍历该序列直到找到一个匹配。

binaryfunc PySequenceMethods.sq_inplace_concat¶  
~~~
    

~~~
This function is used by [`PySequence_InPlaceConcat()`](sequence.md#c.PySequence_InPlaceConcat "PySequence_InPlaceConcat") and has the same signature. It should modify its first operand, and return it. This slot may be left to `NULL`, in this case `PySequence_InPlaceConcat()` will fall back to [`PySequence_Concat()`](sequence.md#c.PySequence_Concat "PySequence_Concat"). It is also used by the augmented assignment `+=`, after trying numeric in-place addition via the `nb_inplace_add` slot.

ssizeargfunc PySequenceMethods.sq_inplace_repeat¶  
~~~
    

~~~
This function is used by [`PySequence_InPlaceRepeat()`](sequence.md#c.PySequence_InPlaceRepeat "PySequence_InPlaceRepeat") and has the same signature. It should modify its first operand, and return it. This slot may be left to `NULL`, in this case `PySequence_InPlaceRepeat()` will fall back to [`PySequence_Repeat()`](sequence.md#c.PySequence_Repeat "PySequence_Repeat"). It is also used by the augmented assignment `*=`, after trying numeric in-place multiplication via the `nb_inplace_multiply` slot.

# Buffer Object Structures¶

type PyBufferProcs¶  
~~~
    

~~~
This structure holds pointers to the functions required by the [Buffer protocol](buffer.md#bufferobjects). The protocol defines how an exporter object can expose its internal data to consumer objects.

getbufferproc PyBufferProcs.bf_getbuffer¶  
~~~
    

~~~
The signature of this function is:
~~~
    
    
~~~
int (PyObject *exporter, Py_buffer *view, int flags);
~~~

Handle a request to _exporter_ to fill in _view_ as specified by _flags_. Except for point (3), an implementation of this function MUST take these steps:

  1. Check if the request can be met. If not, raise [`BufferError`](3.标准库/exceptions.md#BufferError "BufferError"), set view->obj to `NULL` and return `-1`.

  2. Fill in the requested fields.

  3. Increment an internal counter for the number of exports.

  4. Set view->obj to _exporter_ and increment view->obj.

  5. Return `0`.

If _exporter_ is part of a chain or tree of buffer providers, two main schemes can be used:

  * Re-export: Each member of the tree acts as the exporting object and sets view->obj to a new reference to itself.

  * Redirect: The buffer request is redirected to the root object of the tree. Here, view->obj will be a new reference to the root object.

The individual fields of _view_ are described in section [Buffer structure](buffer.md#buffer-structure), the rules how an exporter must react to specific requests are in section [Buffer request types](buffer.md#buffer-request-types).

All memory pointed to in the [`Py_buffer`](buffer.md#c.Py_buffer "Py_buffer") structure belongs to the exporter and must remain valid until there are no consumers left. [`format`](buffer.md#c.Py_buffer.format "Py_buffer.format"), [`shape`](buffer.md#c.Py_buffer.shape "Py_buffer.shape"), [`strides`](buffer.md#c.Py_buffer.strides "Py_buffer.strides"), [`suboffsets`](buffer.md#c.Py_buffer.suboffsets "Py_buffer.suboffsets") and [`internal`](buffer.md#c.Py_buffer.internal "Py_buffer.internal") are read-only for the consumer.

[`PyBuffer_FillInfo()`](buffer.md#c.PyBuffer_FillInfo "PyBuffer_FillInfo") provides an easy way of exposing a simple bytes buffer while dealing correctly with all request types.

[`PyObject_GetBuffer()`](buffer.md#c.PyObject_GetBuffer "PyObject_GetBuffer") is the interface for the consumer that wraps this function.

releasebufferproc PyBufferProcs.bf_releasebuffer¶  

    

~~~
The signature of this function is:
~~~
    
    
~~~
void (PyObject *exporter, Py_buffer *view);
~~~

Handle a request to release the resources of the buffer. If no resources need to be released, `PyBufferProcs.bf_releasebuffer` may be `NULL`. Otherwise, a standard implementation of this function will take these optional steps:

  1. Decrement an internal counter for the number of exports.

  2. If the counter is `0`, free all memory associated with _view_.

The exporter MUST use the [`internal`](buffer.md#c.Py_buffer.internal "Py_buffer.internal") field to keep track of buffer-specific resources. This field is guaranteed to remain constant, while a consumer MAY pass a copy of the original buffer as the _view_ argument.

This function MUST NOT decrement view->obj, since that is done automatically in [`PyBuffer_Release()`](buffer.md#c.PyBuffer_Release "PyBuffer_Release") (this scheme is useful for breaking reference cycles).

[`PyBuffer_Release()`](buffer.md#c.PyBuffer_Release "PyBuffer_Release") is the interface for the consumer that wraps this function.

# Async Object Structures¶

在 3.5 版本加入.

type PyAsyncMethods¶  

    

~~~
This structure holds pointers to the functions required to implement [awaitable](../glossary.md#term-awaitable) and [asynchronous iterator](../glossary.md#term-asynchronous-iterator) objects.

Here is the structure definition:
~~~
    
    
~~~
typedef struct {
    unaryfunc am_await;
    unaryfunc am_aiter;
    unaryfunc am_anext;
    sendfunc am_send;
} PyAsyncMethods;
~~~

unaryfunc PyAsyncMethods.am_await¶  

    

~~~
The signature of this function is:
~~~
    
    
~~~
PyObject *am_await(PyObject *self);
~~~

返回的对象必须为 [iterator](../glossary.md#term-iterator)，即对其执行 [`PyIter_Check()`](iter.md#c.PyIter_Check "PyIter_Check") 必须返回 `1`。

This slot may be set to `NULL` if an object is not an [awaitable](../glossary.md#term-awaitable).

unaryfunc PyAsyncMethods.am_aiter¶  

    

~~~
The signature of this function is:
~~~
    
    
~~~
PyObject *am_aiter(PyObject *self);
~~~

Must return an [asynchronous iterator](../glossary.md#term-asynchronous-iterator) object. See [`__anext__()`](3.%20数据模型.md#object.__anext__ "object.__anext__") for details.

This slot may be set to `NULL` if an object does not implement asynchronous iteration protocol.

unaryfunc PyAsyncMethods.am_anext¶  

    

~~~
The signature of this function is:
~~~
    
    
~~~
PyObject *am_anext(PyObject *self);
~~~

Must return an [awaitable](../glossary.md#term-awaitable) object. See [`__anext__()`](3.%20数据模型.md#object.__anext__ "object.__anext__") for details. This slot may be set to `NULL`.

sendfunc PyAsyncMethods.am_send¶  

    

~~~
The signature of this function is:
~~~
    
    
~~~
PySendResult am_send(PyObject *self, PyObject *arg, PyObject **result);
~~~

See [`PyIter_Send()`](iter.md#c.PyIter_Send "PyIter_Send") for details. This slot may be set to `NULL`.

在 3.10 版本加入.

# Slot Type typedefs¶

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*allocfunc)([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *cls, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") nitems)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

The purpose of this function is to separate memory allocation from memory initialization. It should return a pointer to a block of memory of adequate length for the instance, suitably aligned, and initialized to zeros, but with `ob_refcnt` set to `1` and `ob_type` set to the type argument. If the type's `tp_itemsize` is non-zero, the object's `ob_size` field should be initialized to _nitems_ and the length of the allocated memory block should be `tp_basicsize + nitems*tp_itemsize`, rounded up to a multiple of `sizeof(void*)`; otherwise, _nitems_ is not used and the length of the block should be `tp_basicsize`.

This function should not do any other instance initialization, not even to allocate additional memory; that should be done by `tp_new`.

typedef void (*destructor)([PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

typedef void (*freefunc)(void*)¶  

    

~~~
参见 `tp_free`。

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*newfunc)([PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

参见 `tp_new`。

typedef int (*initproc)([PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

参见 `tp_init`。

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*reprfunc)([PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

参见 `tp_repr`。

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*getattrfunc)([PyObject](structures.md#c.PyObject "PyObject") *self, char *attr)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回对象的指定属性的值。

typedef int (*setattrfunc)([PyObject](structures.md#c.PyObject "PyObject") *self, char *attr, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Set the value of the named attribute for the object. The value argument is set to `NULL` to delete the attribute.

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*getattrofunc)([PyObject](structures.md#c.PyObject "PyObject") *self, [PyObject](structures.md#c.PyObject "PyObject") *attr)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回对象的指定属性的值。

参见 `tp_getattro`。

typedef int (*setattrofunc)([PyObject](structures.md#c.PyObject "PyObject") *self, [PyObject](structures.md#c.PyObject "PyObject") *attr, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Set the value of the named attribute for the object. The value argument is set to `NULL` to delete the attribute.

参见 `tp_setattro`。

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*descrgetfunc)([PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

参见 `tp_descr_get`。

typedef int (*descrsetfunc)([PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

参见 `tp_descr_set`。

typedef Py_hash_t (*hashfunc)([PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

参见 `tp_hash`。

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*richcmpfunc)([PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*, int)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

参见 `tp_richcompare`。

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*getiterfunc)([PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

参见 `tp_iter`。

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*iternextfunc)([PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

参见 `tp_iternext`。

typedef [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") (*lenfunc)([PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

typedef int (*getbufferproc)([PyObject](structures.md#c.PyObject "PyObject")*, [Py_buffer](buffer.md#c.Py_buffer "Py_buffer")*, int)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

typedef void (*releasebufferproc)([PyObject](structures.md#c.PyObject "PyObject")*, [Py_buffer](buffer.md#c.Py_buffer "Py_buffer")*)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*unaryfunc)([PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*binaryfunc)([PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

typedef [PySendResult](iter.md#c.PySendResult "PySendResult") (*sendfunc)([PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")**)¶  
~~~
    

~~~
参见 `am_send`。

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*ternaryfunc)([PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

typedef [PyObject](structures.md#c.PyObject "PyObject") *(*ssizeargfunc)([PyObject](structures.md#c.PyObject "PyObject")*, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t"))¶  

    _Part of the[ Stable ABI](stable.md#stable)._

typedef int (*ssizeobjargproc)([PyObject](structures.md#c.PyObject "PyObject")*, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t"), [PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

typedef int (*objobjproc)([PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

typedef int (*objobjargproc)([PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*, [PyObject](structures.md#c.PyObject "PyObject")*)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

# 例子¶

下面是一些 Python 类型定义的简单示例。 其中包括你可能会遇到的通常用法。 有些演示了令人困惑的边际情况。 要获取更多示例、实践信息以及教程，请参阅 [自定义扩展类型：教程](../extending/newtypes_tutorial.md#defining-new-types) 和 [定义扩展类型：已分类主题](../extending/newtypes.md#new-types-topics)。

一个基本的 静态类型:
~~~
    
    
~~~
typedef struct {
    PyObject_HEAD
    const char *data;
} MyObject;

static PyTypeObject MyObject_Type = {
    PyVarObject_HEAD_INIT(NULL, 0)
    .tp_name = "mymod.MyObject",
    .tp_basicsize = sizeof(MyObject),
    .tp_doc = PyDoc_STR("My objects"),
    .tp_new = myobj_new,
    .tp_dealloc = (destructor)myobj_dealloc,
    .tp_repr = (reprfunc)myobj_repr,
};
~~~

你可能还会看到带有更繁琐的初始化器的较旧代码（特别是在 CPython 代码库中）:

    
    
~~~cpp
static PyTypeObject MyObject_Type = {
    PyVarObject_HEAD_INIT(NULL, 0)
    "mymod.MyObject",               /* tp_name */
    sizeof(MyObject),               /* tp_basicsize */
    0,                              /* tp_itemsize */
    (destructor)myobj_dealloc,      /* tp_dealloc */
    0,                              /* tp_vectorcall_offset */
    0,                              /* tp_getattr */
    0,                              /* tp_setattr */
    0,                              /* tp_as_async */
    (reprfunc)myobj_repr,           /* tp_repr */
    0,                              /* tp_as_number */
    0,                              /* tp_as_sequence */
    0,                              /* tp_as_mapping */
    0,                              /* tp_hash */
    0,                              /* tp_call */
    0,                              /* tp_str */
    0,                              /* tp_getattro */
    0,                              /* tp_setattro */
    0,                              /* tp_as_buffer */
    0,                              /* tp_flags */
    PyDoc_STR("My objects"),        /* tp_doc */
    0,                              /* tp_traverse */
    0,                              /* tp_clear */
    0,                              /* tp_richcompare */
    0,                              /* tp_weaklistoffset */
    0,                              /* tp_iter */
    0,                              /* tp_iternext */
    0,                              /* tp_methods */
    0,                              /* tp_members */
    0,                              /* tp_getset */
    0,                              /* tp_base */
    0,                              /* tp_dict */
    0,                              /* tp_descr_get */
    0,                              /* tp_descr_set */
    0,                              /* tp_dictoffset */
    0,                              /* tp_init */
    0,                              /* tp_alloc */
    myobj_new,                      /* tp_new */
};
~~~

一个支持弱引用、实例字典和哈希运算的类型:

    
    
~~~
typedef struct {
    PyObject_HEAD
    const char *data;
} MyObject;

static PyTypeObject MyObject_Type = {
    PyVarObject_HEAD_INIT(NULL, 0)
    .tp_name = "mymod.MyObject",
    .tp_basicsize = sizeof(MyObject),
    .tp_doc = PyDoc_STR("My objects"),
    .tp_flags = Py_TPFLAGS_DEFAULT | Py_TPFLAGS_BASETYPE |
         Py_TPFLAGS_HAVE_GC | Py_TPFLAGS_MANAGED_DICT |
         Py_TPFLAGS_MANAGED_WEAKREF,
    .tp_new = myobj_new,
    .tp_traverse = (traverseproc)myobj_traverse,
    .tp_clear = (inquiry)myobj_clear,
    .tp_alloc = PyType_GenericNew,
    .tp_dealloc = (destructor)myobj_dealloc,
    .tp_repr = (reprfunc)myobj_repr,
    .tp_hash = (hashfunc)myobj_hash,
    .tp_richcompare = PyBaseObject_Type.tp_richcompare,
};
~~~

A str subclass that cannot be subclassed and cannot be called to create instances (e.g. uses a separate factory func) using `Py_TPFLAGS_DISALLOW_INSTANTIATION` flag:

    
    
~~~
typedef struct {
    PyUnicodeObject raw;
    char *extra;
} MyStr;

static PyTypeObject MyStr_Type = {
    PyVarObject_HEAD_INIT(NULL, 0)
    .tp_name = "mymod.MyStr",
    .tp_basicsize = sizeof(MyStr),
    .tp_base = NULL,  // set to &PyUnicode_Type in module init
    .tp_doc = PyDoc_STR("my custom str"),
    .tp_flags = Py_TPFLAGS_DEFAULT | Py_TPFLAGS_DISALLOW_INSTANTIATION,
    .tp_repr = (reprfunc)myobj_repr,
};
~~~

最简单的固定长度实例 静态类型:

    
    
~~~
typedef struct {
    PyObject_HEAD
} MyObject;

static PyTypeObject MyObject_Type = {
    PyVarObject_HEAD_INIT(NULL, 0)
    .tp_name = "mymod.MyObject",
};
~~~

The simplest static type with variable-length instances:

    
    
~~~
typedef struct {
    PyObject_VAR_HEAD
    const char *data[1];
} MyObject;

static PyTypeObject MyObject_Type = {
    PyVarObject_HEAD_INIT(NULL, 0)
    .tp_name = "mymod.MyObject",
    .tp_basicsize = sizeof(MyObject) - sizeof(char *),
    .tp_itemsize = sizeof(char *),
};
~~~

