# 公用对象结构体¶

大量的结构体被用于定义Python的对象类型。这一节描述了这些的结构体和它们的使用方法。

## 基本的对象类型和宏¶

所有的 Python 对象最终都会在对象的内存表示的开始部分共享少量的字段。 这些字段由 `PyObject` 和 `PyVarObject` 类型来表示，相应地，这些类型又是由一些宏扩展来定义的，它们也直接或间接地被用于所有其他 Python 对象的定义。 附加的宏可以在 [引用计数](refcounting.md#countingrefs) 下找到。

type PyObject¶  

    _Part of the[ Limited API](stable.md#stable). (Only some members are part of the stable ABI.)_

所有对象类型都是此类型的扩展。 这是一个包含了 Python 将对象的指针当作对象来处理所需的信息的类型。 在一个普通的“发行”编译版中，它只包含对象的引用计数和指向对应类型对象的指针。 没有什么对象被实际声明为 `PyObject`，但每个指向 Python 对象的指针都可以被转换为 PyObject*。 对成员的访问必须通过使用 [`Py_REFCNT`](refcounting.md#c.Py_REFCNT "Py_REFCNT") 和 `Py_TYPE` 宏来完成。

type PyVarObject¶  

    _Part of the[ Limited API](stable.md#stable). (Only some members are part of the stable ABI.)_

这是一个添加了 [`ob_size`](typeobj.md#c.PyVarObject.ob_size "PyVarObject.ob_size") 字段的 `PyObject` 扩展。 它仅用于具有某些 _长度_ 标记的对象。 此类型并不经常在 Python/C API 中出现。 对成员的访问必须通过使用 [`Py_REFCNT`](refcounting.md#c.Py_REFCNT "Py_REFCNT"), `Py_TYPE` 和 `Py_SIZE` 宏来完成。

PyObject_HEAD¶  

    

~~~
这是一个在声明代表无可变长度对象的新类型时所使用的宏。 PyObject_HEAD 宏被扩展为:
~~~
    
    
~~~
PyObject ob_base;
~~~

参见上面 `PyObject` 的文档。

PyObject_VAR_HEAD¶  

    

~~~
这是一个在声明代表每个实例具有可变长度的对象时所使用的宏。 PyObject_VAR_HEAD 宏被扩展为:
~~~
    
    
~~~
PyVarObject ob_base;
~~~

参见上面 `PyVarObject` 的文档。

int Py_Is(PyObject *x, PyObject *y)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

测试 _x_ 是否为 _y_ 对象，与 Python 中的 `x is y` 相同。

在 3.10 版本加入.

int Py_IsNone(PyObject *x)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

测试一个对象是否为 `None` 单例，与 Python 中的 `x is None` 相同。

在 3.10 版本加入.

int Py_IsTrue(PyObject *x)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

测试一个对象是否为 `True` 单例，与 Python 中的 `x is True` 相同。

在 3.10 版本加入.

int Py_IsFalse(PyObject *x)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

测试一个对象是否为 `False` 单例，与 Python 中的 `x is False` 相同。

在 3.10 版本加入.

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *Py_TYPE(PyObject *o)¶  

    _返回值：借入的引用。_

获取 Python 对象 _o_ 的类型。

返回一个 [borrowed reference](../glossary.md#term-borrowed-reference)。

使用 `Py_SET_TYPE()` 函数来设置一个对象类型。

在 3.11 版本发生变更: `Py_TYPE()` 被改为一个内联的静态函数。 形参类型不再是 const PyObject*。

int Py_IS_TYPE(PyObject *o, [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type)¶  

    

~~~
如果对象 _o_ 的类型为 _type_ 则返回非零值。 否则返回零。 等价于: `Py_TYPE(o) == type`。

在 3.9 版本加入.

void Py_SET_TYPE(PyObject *o, [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type)¶  
~~~
    

~~~
将对象 _o_ 的类型设为 _type_ 。

在 3.9 版本加入.

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") Py_SIZE(PyVarObject *o)¶  
~~~
    

~~~
获取 Python 对象 _o_ 的大小。

使用 `Py_SET_SIZE()` 函数来设置一个对象大小。

在 3.11 版本发生变更: `Py_SIZE()` 被改为一个内联静态函数。 形参类型不再是 const PyVarObject*。

void Py_SET_SIZE(PyVarObject *o, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size)¶  
~~~
    

~~~
将对象 _o_ 的大小设为 _size_ 。

在 3.9 版本加入.

PyObject_HEAD_INIT(type)¶  
~~~
    

~~~
这是一个为新的 `PyObject` 类型扩展初始化值的宏。 该宏扩展为:
~~~
    
    
~~~
_PyObject_EXTRA_INIT
1, type,
~~~

PyVarObject_HEAD_INIT(type, size)¶  

    

~~~
这是一个为新的 `PyVarObject` 类型扩展初始化值的宏，包括 [`ob_size`](typeobj.md#c.PyVarObject.ob_size "PyVarObject.ob_size") 字段。 该宏会扩展为:
~~~
    
    
~~~
_PyObject_EXTRA_INIT
1, type, size,
~~~

## 实现函数和方法¶

type PyCFunction¶  

    _Part of the[ Stable ABI](stable.md#stable)._

用于在 C 中实现大多数 Python 可调用对象的函数类型。 该类型的函数接受两个 PyObject* 形参并返回一个这样的值。 如果返回值为 `NULL`，则将设置一个异常。 如果不为 `NULL`，则返回值将被解读为 Python 中暴露的函数的返回值。 此函数必须返回一个新的引用。

函数的签名为:

    
    
~~~
PyObject *PyCFunction(PyObject *self,
                      PyObject *args);
~~~

type PyCFunctionWithKeywords¶  

    _Part of the[ Stable ABI](stable.md#stable)._

用于在 C 中实现具有 METH_VARARGS | METH_KEYWORDS 签名的 Python 可调用对象的函数类型。 函数的签名为:

    
    
~~~
PyObject *PyCFunctionWithKeywords(PyObject *self,
                                  PyObject *args,
                                  PyObject *kwargs);
~~~

type _PyCFunctionFast¶  

    

~~~
用于在 C 中实现具有 `METH_FASTCALL` 签名的 Python 可调用对象的函数类型。 函数的签名为:
~~~
    
    
~~~
PyObject *_PyCFunctionFast(PyObject *self,
                           PyObject *const *args,
                           Py_ssize_t nargs);
~~~

type _PyCFunctionFastWithKeywords¶  

    

~~~
用于在 C 中实现具有 METH_FASTCALL | METH_KEYWORDS 签名的 Python 可调用对象的函数类型。 函数的签名为:
~~~
    
    
~~~
PyObject *_PyCFunctionFastWithKeywords(PyObject *self,
                                       PyObject *const *args,
                                       Py_ssize_t nargs,
                                       PyObject *kwnames);
~~~

type PyCMethod¶  

    

~~~
用于在 C 中实现具有 METH_METHOD | METH_FASTCALL | METH_KEYWORDS 签名的 Python 可调用对象的函数类型。 函数的签名为:
~~~
    
    
~~~
PyObject *PyCMethod(PyObject *self,
                    PyTypeObject *defining_class,
                    PyObject *const *args,
                    Py_ssize_t nargs,
                    PyObject *kwnames)
~~~

在 3.9 版本加入.

type PyMethodDef¶  

    _Part of the[ Stable ABI](stable.md#stable) (including all members)._

用于描述一个扩展类型的方法的结构体。 该结构体有四个字段:

const char *ml_name¶  

    

~~~
方法的名称。

PyCFunction ml_meth¶  
~~~
    

~~~
指向 C 语言实现的指针。

int ml_flags¶  
~~~
    

~~~
指明调用应当如何构建的旗标位。

const char *ml_doc¶  
~~~
    

~~~
指向文档字符串的内容。

`ml_meth` 是一个 C 函数指针。 该函数可以为不同类型，但它们将总是返回 PyObject*。 如果该函数不属于 `PyCFunction`，则编译器将要求在方法表中进行转换。 尽管 `PyCFunction` 将第一个参数定义为 PyObject*，但该方法的实现使用 _self_ 对象的特定 C 类型也很常见。

`ml_flags` 字段是可以包含以下旗标的位字段。 每个旗标表示一个调用惯例或绑定惯例。

调用惯例有如下这些:

METH_VARARGS¶  
~~~
    

~~~
这是典型的调用惯例，其中方法的类型为 `PyCFunction`。 该函数接受两个 PyObject* 值。 第一个是用于方法的 _self_ 对象；对于模块函数，它将为模块对象。 第二个形参 (常被命名为 _args_ ) 是一个代表所有参数的元组对象。 该形参通常是使用 [`PyArg_ParseTuple()`](arg.md#c.PyArg_ParseTuple "PyArg_ParseTuple") 或 [`PyArg_UnpackTuple()`](arg.md#c.PyArg_UnpackTuple "PyArg_UnpackTuple") 来处理的。

METH_KEYWORDS¶  
~~~
    

~~~
只能用于同其他旗标形成特定的组合: METH_VARARGS | METH_KEYWORDS, METH_FASTCALL | METH_KEYWORDS 和 METH_METHOD | METH_FASTCALL | METH_KEYWORDS。

METH_VARARGS | METH_KEYWORDS
~~~
    

~~~
带有这些旗标的方法必须为 `PyCFunctionWithKeywords` 类型。 该函数接受三个形参: _self_ , _args_ , _kwargs_ 其中 _kwargs_ 是一个包含所有关键字参数的字典或者如果没有关键字参数则可以为 `NULL`。 这些形参通常是使用 [`PyArg_ParseTupleAndKeywords()`](arg.md#c.PyArg_ParseTupleAndKeywords "PyArg_ParseTupleAndKeywords") 来处理的。

METH_FASTCALL¶  
~~~
    

~~~
快速调用惯例仅支持位置参数。 这些方法的类型为 `_PyCFunctionFast`。 第一个形参为 _self_ ，第二个形参是由表示参数的 PyObject* 值组成的数组而第三个形参是参数的数量（数组的长度）。

在 3.7 版本加入.

在 3.10 版本发生变更: `METH_FASTCALL` 现在是 [稳定 ABI](stable.md#stable-abi) 的一部分。

METH_FASTCALL | METH_KEYWORDS
~~~
    

~~~
`METH_FASTCALL` 的扩展也支持关键字参数，它使用类型为 `_PyCFunctionFastWithKeywords` 的方法。 关键字参数的传递方式与 [vectorcall 协议](call.md#vectorcall) 中的相同：还存在额外的第四个 PyObject* 参数，它是一个代表关键字参数名称（它将保证为字符串）的元组，或者如果没有关键字则可以为 `NULL`。 关键字参数的值存放在 _args_ 数组中，在位置参数之后。

在 3.7 版本加入.

METH_METHOD¶  
~~~
    

~~~
只能与其他旗标组合使用: METH_METHOD | METH_FASTCALL | METH_KEYWORDS。

METH_METHOD | METH_FASTCALL | METH_KEYWORDS
~~~
    

~~~
METH_FASTCALL | METH_KEYWORDS 的扩展支持 _定义式类_ ，也就是包含相应方法的类。 定义式类可以是 `Py_TYPE(self)` 的超类。

该方法必须为 `PyCMethod` 类型，与在 `self` 之后添加了 `defining_class` 参数的 `METH_FASTCALL | METH_KEYWORDS` 一样。

在 3.9 版本加入.

METH_NOARGS¶  
~~~
    

~~~
如果通过 `METH_NOARGS` 旗标列出了参数则没有形参的方法无需检查是否给出了参数。 它们必须为 `PyCFunction` 类型。 第一个形参通常被命名为 _self_ 并将持有对模块或对象实例的引用。 在所有情况下第二个形参都将为 `NULL`。

该函数必须有 2 个形参。 由于第二个形参不会被使用，[`Py_UNUSED`](intro.md#c.Py_UNUSED "Py_UNUSED") 可以被用来防止编译器警告。

METH_O¶  
~~~
    

~~~
具有一个单独对象参数的方法可使用 `METH_O` 旗标列出，而不必发起调用 [`PyArg_ParseTuple()`](arg.md#c.PyArg_ParseTuple "PyArg_ParseTuple") 并附带 `"O"` 参数。 它们的类型为 `PyCFunction`，带有 _self_ 形参，以及代表该单独参数的 PyObject* 形参。

这两个常量不是被用来指明调用惯例而是在配合类方法使用时指明绑定。 它们不会被用于在模块上定义的函数。 对于任何给定方法这些旗标最多只会设置其中一个。

METH_CLASS¶  
~~~
    

~~~
该方法将接受类型对象而不是类型的实例作为第一个形参。 它会被用于创建 _类方法_ ，类似于使用 [`classmethod()`](../library/functions.md#classmethod "classmethod") 内置函数所创建的结果。

METH_STATIC¶  
~~~
    

~~~
该方法将接受 `NULL` 而不是类型的实例作为第一个形参。 它会被用于创建 _静态方法_ ，类似于使用 [`staticmethod()`](../library/functions.md#staticmethod "staticmethod") 内置函数所创建的结果。

另一个常量控制方法是否将被载入来替代具有相同方法名的另一个定义。

METH_COEXIST¶  
~~~
    

~~~
该方法将被加载以替代现有的定义。 如果没有 _METH_COEXIST_ ，默认将跳过重复的定义。 由于槽位包装器会在方法表之前被加载，例如 当存在 _sq_contains_ 槽位时，将会生成一个名为 [`__contains__()`](../reference/datamodel.md#object.__contains__ "object.__contains__") 的已包装方法并阻止加载同名的相应 PyCFunction。 如果定义了此旗标，PyCFunction 将被加载以替代此包装器对象并与槽位共存。 因为对 PyCFunction 的调用相比对包装器对象调用更为优化所以这是很有帮助的。

## 访问扩展类型的属性¶

type PyMemberDef¶  

    _Part of the[ Stable ABI](stable.md#stable) (including all members)._

描述与 C 结构成员相对应的类型属性的结构体。 其字段依次为:

const char *name¶  
~~~
    

~~~
成员名称。 NULL 值表示 `PyMemberDef[]` 数组的结束。

字符串应当是静态的，它不会被复制。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") offset¶  
~~~
    

~~~
成员在类型的对象结构体中所在位置的以字节为单位的偏移量。

int type¶  
~~~
    

~~~
C 结构体中成员的类型。 请参阅 成员类型 了解可能的取值。

int flags¶  
~~~
    

~~~
零个或多个 成员旗标，使用按位或运算进行组合。

const char *doc¶  
~~~
    

~~~
文档字符串，或者为空。 该字符串应当是静态的，它不会被拷贝。 通常，它是使用 [`PyDoc_STR`](intro.md#c.PyDoc_STR "PyDoc_STR") 来定义的。

默认情况下 (当 `flags` 为 `0` 时)，成员同时允许读取和写入访问。 使用 `Py_READONLY` 旗标表示只读访问。 某些类型，如 `Py_T_STRING`，隐含要求 `Py_READONLY`。 只有 `Py_T_OBJECT_EX` (以及旧式的 `T_OBJECT`) 成员可以删除。

对于堆分配类型（使用 [`PyType_FromSpec()`](type.md#c.PyType_FromSpec "PyType_FromSpec") 或类似函数创建），`PyMemberDef` 可能包含特殊成员 `"__vectorcalloffset__"` 的定义，与类型对象中的 [`tp_vectorcall_offset`](typeobj.md#c.PyTypeObject.tp_vectorcall_offset "PyTypeObject.tp_vectorcall_offset") 相对应。 它们必须用 `Py_T_PYSSIZET` 和 `Py_READONLY` 来定义，例如:
~~~
    
    
~~~cpp
static PyMemberDef spam_type_members[] = {
    {"__vectorcalloffset__", Py_T_PYSSIZET,
     offsetof(Spam_object, vectorcall), Py_READONLY},
    {NULL}  /* Sentinel */
};
~~~

（您可能需要为 `offsetof()` 添加 `#include <stddef.h>`。）

旧式的偏移量 [`tp_dictoffset`](typeobj.md#c.PyTypeObject.tp_dictoffset "PyTypeObject.tp_dictoffset") 和 [`tp_weaklistoffset`](typeobj.md#c.PyTypeObject.tp_weaklistoffset "PyTypeObject.tp_weaklistoffset") 可使用 `"__dictoffset__"` 和 `"__weaklistoffset__"` 成员进行类似的定义，但强烈建议扩展程序改用 [`Py_TPFLAGS_MANAGED_DICT`](typeobj.md#c.Py_TPFLAGS_MANAGED_DICT "Py_TPFLAGS_MANAGED_DICT") 和 [`Py_TPFLAGS_MANAGED_WEAKREF`](typeobj.md#c.Py_TPFLAGS_MANAGED_WEAKREF "Py_TPFLAGS_MANAGED_WEAKREF")。

在 3.12 版本发生变更: `PyMemberDef` 将始终可用。在之前版本中，它需要包括 `"structmember.h"`。

PyObject *PyMember_GetOne(const char *obj_addr, struct PyMemberDef *m)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

获取属于地址Get an attribute belonging to the object at address _obj_addr_ 上的对象的某个属性。 该属性是以 `PyMemberDef` _m_ 来描述的。 出错时返回 `NULL`。

在 3.12 版本发生变更: `PyMember_GetOne` 将总是可用。 在之前版本中，它需要包括 `"structmember.h"`。

int PyMember_SetOne(char *obj_addr, struct PyMemberDef *m, PyObject *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将属于位于地址 _obj_addr_ 的对象的属性设置到对象 _o_ 。 要设置的属性由 `PyMemberDef` _m_ 描述。 成功时返回 `0` 而失败时返回负值。

在 3.12 版本发生变更: `PyMember_SetOne` 将总是可用。 在之前版本中，它需要包括 `"structmember.h"`。

### 成员旗标¶

以下旗标可被用于 `PyMemberDef.flags`:

Py_READONLY¶  

    

~~~
不可写入。

Py_AUDIT_READ¶  
~~~
    

~~~
在读取之前发出一个 `object.__getattr__` [审计事件](../library/audit_events.md#audit-events)。

Py_RELATIVE_OFFSET¶  
~~~
    

~~~
表示该 `PyMemberDef` 条目的 `offset` 是指明来自子类专属数据的偏移量，而不是来自 `PyObject` 的偏移量。

只能在使用负的 [`basicsize`](type.md#c.PyType_Spec.basicsize "PyType_Spec.basicsize") 创建类时用作 [`Py_tp_members`](typeobj.md#c.PyTypeObject.tp_members "PyTypeObject.tp_members") `槽位` 的组成部分。 它在此种情况下是强制要求。

这个旗标只能在 `PyTypeSlot` 中使用。 在类创建期间中设置 [`tp_members`](typeobj.md#c.PyTypeObject.tp_members "PyTypeObject.tp_members") 时，Python 会清除它并将 `PyMemberDef.offset` 设为相对于 `PyObject` 结构的偏移量。

在 3.10 版本发生变更: 通过 `#include "structmember.h"` 提供的 `RESTRICTED`、`READ_RESTRICTED` 和 `WRITE_RESTRICTED` 宏已被弃用。 `READ_RESTRICTED` 和 `RESTRICTED` 等同于 `Py_AUDIT_READ`；`WRITE_RESTRICTED` 则没有任何作用。

在 3.12 版本发生变更: `READONLY` 宏被更名为 `Py_READONLY`。 `PY_AUDIT_READ` 宏被更名为 `Py_` 前缀。 新名称现在将始终可用。 在之前的版本中，这些名称需要 `#include "structmember.h"`。该头文件仍然可用并提供了原有的名称。

### 成员类型¶

`PyMemberDef.type` 可以是下列与各种 C 类型相对应的宏之一。 在 Python 中访问该成员时，它将被转换为对应的 Python 类型。 当从 Python 设置成员时，它将被转换回 C 类型。 如果无法转换，则会引发一个异常如 [`TypeError`](../library/exceptions.md#TypeError "TypeError") 或 [`ValueError`](../library/exceptions.md#ValueError "ValueError")。

除非标记为 (D)，否则不能使用 [`del`](../reference/simple_stmts.md#del) 或 [`delattr()`](../library/functions.md#delattr "delattr") 删除以这种方式定义的属性。

宏名称

|

C 类型

|

Python 类型  
  
---|---|---  
  
Py_T_BYTE¶  
~~~
    
|

char

|

[`int`](functions.md#int "int")  
  
Py_T_SHORT¶  

    
|

short

|

[`int`](functions.md#int "int")  
  
Py_T_INT¶  

    
|

int

|

[`int`](functions.md#int "int")  
  
Py_T_LONG¶  

    
|

long

|

[`int`](functions.md#int "int")  
  
Py_T_LONGLONG¶  

    
|

long long

|

[`int`](functions.md#int "int")  
  
Py_T_UBYTE¶  

    
|

unsigned char

|

[`int`](functions.md#int "int")  
  
Py_T_UINT¶  

    
|

unsigned int

|

[`int`](functions.md#int "int")  
  
Py_T_USHORT¶  

    
|

unsigned short

|

[`int`](functions.md#int "int")  
  
Py_T_ULONG¶  

    
|

unsigned long

|

[`int`](functions.md#int "int")  
  
Py_T_ULONGLONG¶  

    
|

unsigned long long

|

[`int`](functions.md#int "int")  
  
Py_T_PYSSIZET¶  

    
|

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t")

|

[`int`](functions.md#int "int")  
  
Py_T_FLOAT¶  

    
|

float

|

[`float`](functions.md#float "float")  
  
Py_T_DOUBLE¶  

    
|

double

|

[`float`](functions.md#float "float")  
  
Py_T_BOOL¶  

    
|

char (写为 0 或 1)

|

[`bool`](functions.md#bool "bool")  
  
Py_T_STRING¶  

    
|

const char* (*)

|

[`str`](stdtypes.md#str "str") (RO)  
  
Py_T_STRING_INPLACE¶  

    
|

const char[] (*)

|

[`str`](stdtypes.md#str "str") (RO)  
  
Py_T_CHAR¶  

    
|

char (0-127)

|

[`str`](stdtypes.md#str "str") (**)  
  
Py_T_OBJECT_EX¶  

    
|

PyObject*

|

[`object`](functions.md#object "object") (D)  
  
> (*): 以零结束的 UTF8 编码的 C 字符串。 使用:c:macro:!Py_T_STRING 时的 C 表示形式是指针；使用 `Py_T_STRING_INLINE` 时字符串将直接存储在结构体中。
>
> (**): 长度为 1 的字符串。 只接受 ASCII 字符。
>
> (RO)：表示 `Py_READONLY`。
>
> (D)：可以删除，在这种情况下指针会被设为 `NULL`。 读取 `NULL` 指针会引发 [`AttributeError`](3.标准库/exceptions.md#AttributeError "AttributeError")。

在 3.12 版本加入: 在之前的版本中，这些宏仅通过 `#include "structmember.h"` 提供并且其名称不带 `Py_` 前缀 (例如 `T_INT`)。 头文件仍然可用并包含这些旧名称，以及下列已被弃用的类型：

T_OBJECT¶  

    

~~~
与 `Py_T_OBJECT_EX` 类似，但 `NULL` 会被转换为 `None`。 这将在 Python 中产生令人吃惊的行为：删除该属性实际上会将其设置为 `None`。

T_NONE¶  
~~~
    

~~~
总是为 `None`。 必须与 `Py_READONLY` 一起使用。

### 定义读取器和设置器¶

type PyGetSetDef¶  

    _Part of the[ Stable ABI](stable.md#stable) (including all members)._

用于定义针对某个类型的特征属性式的访问的结构体。 另请参阅 [`PyTypeObject.tp_getset`](typeobj.md#c.PyTypeObject.tp_getset "PyTypeObject.tp_getset") 槽位的描述。

const char *name¶  
~~~
    

~~~
属性名称

getter get¶  
~~~
    

~~~
用于获取属性的 C 函数。

setter set¶  
~~~
    

~~~
用于设置或删除属性的可选 C 函数，如果省略则属性将为只读。

const char *doc¶  
~~~
    

~~~
可选的文档字符串

void *closure¶  
~~~
    

~~~
可选的函数指针，为 getter 和 setter 提供附加数据。

`get` 函数接受一个 PyObject* 形参 (实例) 和一个函数指针 (关联的 `closure`):
~~~
    
    
~~~
typedef PyObject *(*getter)(PyObject *, void *);
~~~

它应当在成功时返回一个新的引用或在失败时返回 `NULL` 并设置异常。

`set` 函数接受两个 PyObject* 形参 (实例和要设置的值) 和一个函数指针 (关联的 `closure`):

    
    
~~~
typedef int (*setter)(PyObject *, PyObject *, void *);
~~~

对于属性要被删除的情况第二个形参应为 `NULL`。 成功时应返回 `0` 或在失败时返回 `-1` 并设置异常。

