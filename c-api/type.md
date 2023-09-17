# 类型对象¶

type PyTypeObject¶  

    _Part of the[ Limited API](stable.md#stable) (as an opaque struct)._

对象的 C 结构用于描述 built-in 类型。

PyTypeObject PyType_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是属于 type 对象的 type object，它在 Python 层面和 [`type`](../library/functions.md#type "type") 是相同的对象。

int PyType_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    

~~~
如果对象 _o_ 是一个类型对象，包括派生自标准类型对象的类型实例则返回非零值。 在所有其它情况下都返回 0。 此函数将总是成功执行。

int PyType_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果对象 _o_ 是一个类型对象，但不是标准类型对象的子类型则返回非零值。 在所有其它情况下都返回 0。 此函数将总是成功执行。

unsigned int PyType_ClearCache()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

清空内部查找缓存。 返回当前版本标签。

unsigned long PyType_GetFlags(PyTypeObject *type)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _type_ 的 [`tp_flags`](typeobj.md#c.PyTypeObject.tp_flags "PyTypeObject.tp_flags") 成员。 此函数主要是配合 `Py_LIMITED_API` 使用；单独的旗标位会确保在各个 Python 发布版之间保持稳定，但对 [`tp_flags`](typeobj.md#c.PyTypeObject.tp_flags "PyTypeObject.tp_flags") 本身的访问并不是 [受限 API](stable.md#limited-c-api) 的一部分。

在 3.2 版本加入.

在 3.4 版本发生变更: 返回类型现在是 `unsigned long` 而不是 `long`。

[PyObject](structures.md#c.PyObject "PyObject") *PyType_GetDict(PyTypeObject *type)¶  
~~~
    

~~~
返回类型对象的内部命名空间，它在其他情况下只能通过只读代理 (`cls.__dict__`) 公开。 这可以代替直接访问 [`tp_dict`](typeobj.md#c.PyTypeObject.tp_dict "PyTypeObject.tp_dict") 的方式。 返回的字典必须当作是只读的。

该函数用于特定的嵌入和语言绑定场景，在这些场景下需要直接访问该字典而间接访问（例如通过代理或 [`PyObject_GetAttr()`](object.md#c.PyObject_GetAttr "PyObject_GetAttr") 访问）并不足够。

扩展模块在设置它们自己的类型时应当继续直接或间接地使用 `tp_dict`。

在 3.12 版本加入.

void PyType_Modified(PyTypeObject *type)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

使该类型及其所有子类型的内部查找缓存失效。 此函数必须在对该类型的属性或基类进行任何手动修改之后调用。

int PyType_AddWatcher(PyType_WatchCallback callback)¶  
~~~
    

~~~
注册 _callback_ 作为类型监视器。 返回一个非负的整数 ID，它必须传给将来对 `PyType_Watch()` 的调用。 如果出错（例如没有足够的可用监视器 ID）,则返回 `-1` 并设置一个异常。

在 3.12 版本加入.

int PyType_ClearWatcher(int watcher_id)¶  
~~~
    

~~~
清除由 _watcher_id_ (之前从 `PyType_AddWatcher()` 返回) 所标识的 watcher。 成功时返回 `0`，出错时（例如 _watcher_id_ 未被注册）返回 `-1`。

扩展在调用 `PyType_ClearWatcher` 时绝不能使用不是之前调用 `PyType_AddWatcher()` 所返回的 _watcher_id_ 。

在 3.12 版本加入.

int PyType_Watch(int watcher_id, [PyObject](structures.md#c.PyObject "PyObject") *type)¶  
~~~
    

~~~
将 _type_ 标记为已监视。 每当 `PyType_Modified()` 报告 _type_ 发生变化时 `PyType_AddWatcher()` 赋予 _watcher_id_ 的回调将被调用。 （如果在 _type_ 的一系列连续修改之间没有调用 `_PyType_Lookup()`，则回调只能被调用一次；这是一个实现细节并可能发生变化）。

扩展在调用 `PyType_Watch` 时绝不能使用不是之前调用 `PyType_AddWatcher()` 所返回的 _watcher_id_ 。

在 3.12 版本加入.

typedef int (*PyType_WatchCallback)([PyObject](structures.md#c.PyObject "PyObject") *type)¶  
~~~
    

~~~
类型监视器回调函数的类型。

回调不可以修改 _type_ 或是导致 `PyType_Modified()` 在 _type_ 或其 MRO 中的任何类型上被调用；违反此规则可能导致无限递归。

在 3.12 版本加入.

int PyType_HasFeature(PyTypeObject *o, int feature)¶  
~~~
    

~~~
如果类型对象 _o_ 设置了特性 _feature_ 则返回非零值。 类型特性是用单个比特位旗标来表示的。

int PyType_IS_GC(PyTypeObject *o)¶  
~~~
    

~~~
如果类型对象包括了对循环检测器的支持则返回真值；这将测试类型旗标 [`Py_TPFLAGS_HAVE_GC`](typeobj.md#c.Py_TPFLAGS_HAVE_GC "Py_TPFLAGS_HAVE_GC")。

int PyType_IsSubtype(PyTypeObject *a, PyTypeObject *b)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果 _a_ 是 _b_ 的子类型则返回真值。

此函数只检查实际的子类型，这意味着 [`__subclasscheck__()`](../reference/datamodel.md#class.__subclasscheck__ "class.__subclasscheck__") 不会在 _b_ 上被调用。 请调用 [`PyObject_IsSubclass()`](object.md#c.PyObject_IsSubclass "PyObject_IsSubclass") 来执行与 [`issubclass()`](../library/functions.md#issubclass "issubclass") 所做的相同检查。

[PyObject](structures.md#c.PyObject "PyObject") *PyType_GenericAlloc(PyTypeObject *type, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") nitems)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

类型对象的 [`tp_alloc`](typeobj.md#c.PyTypeObject.tp_alloc "PyTypeObject.tp_alloc") 槽位的通用处理句柄。 请使用 Python 的默认内存分配机制来分配一个新的实例并将其所有内容初始化为 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyType_GenericNew(PyTypeObject *type, [PyObject](structures.md#c.PyObject "PyObject") *args, [PyObject](structures.md#c.PyObject "PyObject") *kwds)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

类型对象的 [`tp_new`](typeobj.md#c.PyTypeObject.tp_new "PyTypeObject.tp_new") 槽位的通用处理句柄。 请使用类型的 [`tp_alloc`](typeobj.md#c.PyTypeObject.tp_alloc "PyTypeObject.tp_alloc") 槽位来创建一个新的实例。

int PyType_Ready(PyTypeObject *type)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

最终化一个类型对象。 这应当在所有类型对象上调用以完成它们的初始化。 此函数会负责从一个类型的基类添加被继承的槽位。 成功时返回 `0`，或是在出错时返回 `-1` 并设置一个异常。

备注

如果某些基类实现了 GC 协议并且所提供的类型的旗标中未包括 [`Py_TPFLAGS_HAVE_GC`](typeobj.md#c.Py_TPFLAGS_HAVE_GC "Py_TPFLAGS_HAVE_GC")，则将自动从其父类实现 GC 协议。 相反地，如果被创建的类型的旗标中确实包含 [`Py_TPFLAGS_HAVE_GC`](typeobj.md#c.Py_TPFLAGS_HAVE_GC "Py_TPFLAGS_HAVE_GC") 则它 **必须** 自己实现 GC 协议，至少要实现 [`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 句柄。

[PyObject](structures.md#c.PyObject "PyObject") *PyType_GetName(PyTypeObject *type)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

返回类型名称。 等同于获取类型的 `__name__` 属性。

在 3.11 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyType_GetQualName(PyTypeObject *type)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.11._

返回类型的限定名称。 等同于获取类型的 `__qualname__` 属性。

在 3.11 版本加入.

void *PyType_GetSlot(PyTypeObject *type, int slot)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.4._

返回存储在给定槽位中的函数指针。 如果结果为 `NULL`，则表示或者该槽位为 `NULL`，或者该函数调用传入了无效的形参。 调用方通常要将结果指针转换到适当的函数类型。

请参阅 `PyType_Slot.slot` 查看可用的 _slot_ 参数值。

在 3.4 版本加入.

在 3.10 版本发生变更: `PyType_GetSlot()` 现在可以接受所有类型。 在此之前，它被限制为 [堆类型](typeobj.md#heap-types)。

[PyObject](structures.md#c.PyObject "PyObject") *PyType_GetModule(PyTypeObject *type)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

返回当使用 `PyType_FromModuleAndSpec()` 创建类型时关联到给定类型的模块对象。

如果没有关联到给定类型的模块，则设置 [`TypeError`](../library/exceptions.md#TypeError "TypeError") 并返回 `NULL`。

此函数通常被用于获取方法定义所在的模块。 请注意在这样的方法中，`PyType_GetModule(Py_TYPE(self))` 可能不会返回预期的结果。 `Py_TYPE(self)` 可以是目标类的一个 _子类_ ，而子类并不一定是在与其超类相同的模块中定义的。 请参阅 [`PyCMethod`](structures.md#c.PyCMethod "PyCMethod") 了解如何获取方法定义所在的类。 请参阅 `PyType_GetModuleByDef()` 了解有关无法使用 `PyCMethod` 的情况。

在 3.9 版本加入.

void *PyType_GetModuleState(PyTypeObject *type)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

返回关联到给定类型的模块对象的状态。 这是一个在 `PyType_GetModule()` 的结果上调用 [`PyModule_GetState()`](module.md#c.PyModule_GetState "PyModule_GetState") 的快捷方式。

如果没有关联到给定类型的模块，则设置 [`TypeError`](../library/exceptions.md#TypeError "TypeError") 并返回 `NULL`。

如果 _type_ 有关联的模块但其状态为 `NULL`，则返回 `NULL` 且不设置异常。

在 3.9 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyType_GetModuleByDef(PyTypeObject *type, struct [PyModuleDef](module.md#c.PyModuleDef "PyModuleDef") *def)¶  
~~~
    

~~~
找到所属模块基于给定的 [`PyModuleDef`](module.md#c.PyModuleDef "PyModuleDef") _def_ 创建的第一个上级类，并返回该模块。

如果未找到模块，则会引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError") 并返回 `NULL`。

此函数预期会与 [`PyModule_GetState()`](module.md#c.PyModule_GetState "PyModule_GetState") 一起使用以便从槽位方法 (如 [`tp_init`](typeobj.md#c.PyTypeObject.tp_init "PyTypeObject.tp_init") 或 [`nb_add`](typeobj.md#c.PyNumberMethods.nb_add "PyNumberMethods.nb_add")) 及其他定义方法的类无法使用 [`PyCMethod`](structures.md#c.PyCMethod "PyCMethod") 调用惯例来传递的场合获取模块状态。

在 3.11 版本加入.

int PyUnstable_Type_AssignVersionTag(PyTypeObject *type)¶  
~~~
    

~~~
_This is[ Unstable API](stable.md#unstable-c-api). It may change without warning in minor releases._

尝试为给定的类型设置一个版本标签。

如果类型已有合法的版本标签或已设置了新的版本标签则返回 1，或者如果无法设置新的标签则返回 0。

在 3.12 版本加入.

## 创建堆分配类型¶

下列函数和结构体可被用来创建 [堆类型](typeobj.md#heap-types)。

[PyObject](structures.md#c.PyObject "PyObject") *PyType_FromMetaclass(PyTypeObject *metaclass, [PyObject](structures.md#c.PyObject "PyObject") *module, PyType_Spec *spec, [PyObject](structures.md#c.PyObject "PyObject") *bases)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.12._

根据 _spec_ (参见 [`Py_TPFLAGS_HEAPTYPE`](typeobj.md#c.Py_TPFLAGS_HEAPTYPE "Py_TPFLAGS_HEAPTYPE")) 创建并返回一个 [堆类型](typeobj.md#heap-types)。

元类 _metaclass_ 用于构建结果类型对象。 当 _metaclass_ 为 `NULL` 时，元类将派生自 _bases_ (或者如果 _bases_ 为 `NULL` 则派生自 _Py_tp_base[s]_ 槽位，见下文)。

不支持重载 [`tp_new`](typeobj.md#c.PyTypeObject.tp_new "PyTypeObject.tp_new") 的元类，除非 `tp_new` 为 `NULL`。 （为了向下兼容，其他 `PyType_From*` 函数允许这样的元类。 它们将忽略 `tp_new`，可能导致不完整的初始化。 这样的元类已被弃用并在 Python 3.14+ 中停止支持。）

_bases_ 参数可被用来指定基类；它可以是单个类或由多个类组成的元组。 如果 _bases_ 为 `NULL`，则会改用 _Py_tp_bases_ 槽位。 如果该槽位也为 `NULL`，则会改用 _Py_tp_base_ 槽位。 如果该槽位同样为 `NULL`，则新类型将派生自 [`object`](../library/functions.md#object "object")。

_module_ 参数可被用来记录新类定义所在的模块。 它必须是一个模块对象或为 `NULL`。 如果不为 `NULL`，则该模块会被关联到新类型并且可在之后通过 `PyType_GetModule()` 来获取。 这个关联模块不可被子类继承；它必须为每个类单独指定。

此函数会在新类型上调用 `PyType_Ready()`。

请注意此函数 _不能_ 完全匹配调用 [`type()`](../library/functions.md#type "type") 或使用 [`class`](../reference/compound_stmts.md#class) 语句的行为。 对于用户提供的类型或元类，推荐 [调用](call.md#capi-call) [`type`](../library/functions.md#type "type") (或元类) 而不是 `PyType_From*` 函数。 特别地:

  * [`__new__()`](../reference/datamodel.md#object.__new__ "object.__new__") 不会在新类上被调用 (它必须被设为 `type.__new__`)。

  * [`__init__()`](../reference/datamodel.md#object.__init__ "object.__init__") 不会在新类上被调用。

  * [`__init_subclass__()`](../reference/datamodel.md#object.__init_subclass__ "object.__init_subclass__") 不会在任何基类上调用。

  * [`__set_name__()`](../reference/datamodel.md#object.__set_name__ "object.__set_name__") 不会在新的描述器上调用。

在 3.12 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyType_FromModuleAndSpec([PyObject](structures.md#c.PyObject "PyObject") *module, PyType_Spec *spec, [PyObject](structures.md#c.PyObject "PyObject") *bases)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

等价于 `PyType_FromMetaclass(NULL, module, spec, bases)`。

在 3.9 版本加入.

在 3.10 版本发生变更: 此函数现在接受一个单独类作为 _bases_ 参数并接受 `NULL` 作为 `tp_doc` 槽位。

在 3.12 版本发生变更: 该函数现在可以找到并使用与所提供的基类相对应的元类。 在此之前，只会返回 [`type`](../library/functions.md#type "type") 实例。

元类的 [`tp_new`](typeobj.md#c.PyTypeObject.tp_new "PyTypeObject.tp_new") 将被 _忽略_ 。 这可能导致不完整的初始化。 创建元类重载 [`tp_new`](typeobj.md#c.PyTypeObject.tp_new "PyTypeObject.tp_new") 的类的做法已被弃用并且在 Python 3.14+ 中将不再被允许。

[PyObject](structures.md#c.PyObject "PyObject") *PyType_FromSpecWithBases(PyType_Spec *spec, [PyObject](structures.md#c.PyObject "PyObject") *bases)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.3._

等价于 `PyType_FromMetaclass(NULL, NULL, spec, bases)`。

在 3.3 版本加入.

在 3.12 版本发生变更: 该函数现在可以找到并使用与所提供的基类相对应的元类。 在此之前，只会返回 [`type`](../library/functions.md#type "type") 实例。

元类的 [`tp_new`](typeobj.md#c.PyTypeObject.tp_new "PyTypeObject.tp_new") 将被 _忽略_ 。 这可能导致不完整的初始化。 创建元类重载 [`tp_new`](typeobj.md#c.PyTypeObject.tp_new "PyTypeObject.tp_new") 的类的做法已被弃用并且在 Python 3.14+ 中将不再被允许。

[PyObject](structures.md#c.PyObject "PyObject") *PyType_FromSpec(PyType_Spec *spec)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

等价于 `PyType_FromMetaclass(NULL, NULL, spec, NULL)`。

在 3.12 版本发生变更: 该函数现在可以找到并使用与 _Py_tp_base[s]_ 槽位中提供的基类相对应的元类。 在此之前，只会返回 [`type`](../library/functions.md#type "type") 实例。

元类的 [`tp_new`](typeobj.md#c.PyTypeObject.tp_new "PyTypeObject.tp_new") 将被 _忽略_ 。 这可能导致不完整的初始化。 创建元类重载 [`tp_new`](typeobj.md#c.PyTypeObject.tp_new "PyTypeObject.tp_new") 的类的做法已被弃用并且在 Python 3.14+ 中将不再被允许。

type PyType_Spec¶  

    _Part of the[ Stable ABI](stable.md#stable) (including all members)._

定义一个类型的行为的结构体。

const char *name¶  
~~~
    

~~~
类型的名称，用来设置 [`PyTypeObject.tp_name`](typeobj.md#c.PyTypeObject.tp_name "PyTypeObject.tp_name")。

int basicsize¶  
~~~
    

~~~
如果为正数，则以字节为单位指定实例的大小。 它用于设置 [`PyTypeObject.tp_basicsize`](typeobj.md#c.PyTypeObject.tp_basicsize "PyTypeObject.tp_basicsize")。

如果为零，则指定应当继承 [`tp_basicsize`](typeobj.md#c.PyTypeObject.tp_basicsize "PyTypeObject.tp_basicsize")。

如果为负数，则以其绝对值指定该类的实例在超类的 _基础之上_ 还需要多少空间。 使用 [`PyObject_GetTypeData()`](object.md#c.PyObject_GetTypeData "PyObject_GetTypeData") 来获取通过此方式保留的子类专属内存的指针。

在 3.12 版本发生变更: 在之前版本中，此字段不能为负数。

int itemsize¶  
~~~
    

~~~
可变大小类型中一个元素的大小，以字节为单位。 用于设置 [`PyTypeObject.tp_itemsize`](typeobj.md#c.PyTypeObject.tp_itemsize "PyTypeObject.tp_itemsize")。 注意事项请参阅 `tp_itemsize` 文档。

如果为零，则会继承 [`tp_itemsize`](typeobj.md#c.PyTypeObject.tp_itemsize "PyTypeObject.tp_itemsize")。 扩展任意可变大小的类是很危险的，因为某些类型使用固定偏移量来标识可变大小的内存，这样就会与子类使用的固定大小的内存相重叠。 为了防止出错，只有在以下情况下才可以继承 `itemsize`:

  * 基类不是可变大小的 (即其 [`tp_itemsize`](typeobj.md#c.PyTypeObject.tp_itemsize "PyTypeObject.tp_itemsize"))。

  * 所请求的 `PyType_Spec.basicsize` 为正值，表明基类的内存布局是已知的。

  * 所请求的 `PyType_Spec.basicsize` 为零，表明子类不会直接访问实例的内存。

  * 具有 [`Py_TPFLAGS_ITEMS_AT_END`](typeobj.md#c.Py_TPFLAGS_ITEMS_AT_END "Py_TPFLAGS_ITEMS_AT_END") 旗标。

unsigned int flags¶  
~~~
    

~~~
类型旗标，用来设置 [`PyTypeObject.tp_flags`](typeobj.md#c.PyTypeObject.tp_flags "PyTypeObject.tp_flags")。

如果未设置 `Py_TPFLAGS_HEAPTYPE` 旗标，则 `PyType_FromSpecWithBases()` 会自动设置它。

PyType_Slot *slots¶  
~~~
    

~~~
`PyType_Slot` 结构体的数组。 以特殊槽位值 `{0, NULL}` 来结束。

每个槽位 ID 应当只被指定一次。

type PyType_Slot¶  

    _Part of the[ Stable ABI](stable.md#stable) (including all members)._

定义一个类型的可选功能的结构体，包含一个槽位 ID 和一个值指针。

int slot¶  
~~~
    

~~~
> 槽位 ID。
>
> 槽位 ID 的类名像是结构体 `PyTypeObject`, [`PyNumberMethods`](typeobj.md#c.PyNumberMethods "PyNumberMethods"), [`PySequenceMethods`](typeobj.md#c.PySequenceMethods "PySequenceMethods"), [`PyMappingMethods`](typeobj.md#c.PyMappingMethods "PyMappingMethods") 和 [`PyAsyncMethods`](typeobj.md#c.PyAsyncMethods "PyAsyncMethods") 的字段名附加一个 `Py_` 前缀。 举例来说，使用:
>
>   * `Py_tp_dealloc` 设置 [`PyTypeObject.tp_dealloc`](typeobj.md#c.PyTypeObject.tp_dealloc "PyTypeObject.tp_dealloc")
>
>   * `Py_nb_add` 设置 [`PyNumberMethods.nb_add`](typeobj.md#c.PyNumberMethods.nb_add "PyNumberMethods.nb_add")
>
>   * `Py_sq_length` 设置 [`PySequenceMethods.sq_length`](typeobj.md#c.PySequenceMethods.sq_length "PySequenceMethods.sq_length")
>
>

>
> 下列字段完全无法使得 `PyType_Spec` 和 `PyType_Slot` 来设置:
>
>   * [`tp_dict`](typeobj.md#c.PyTypeObject.tp_dict "PyTypeObject.tp_dict")
>
>   * [`tp_mro`](typeobj.md#c.PyTypeObject.tp_mro "PyTypeObject.tp_mro")
>
>   * [`tp_cache`](typeobj.md#c.PyTypeObject.tp_cache "PyTypeObject.tp_cache")
>
>   * [`tp_subclasses`](typeobj.md#c.PyTypeObject.tp_subclasses "PyTypeObject.tp_subclasses")
>
>   * [`tp_weaklist`](typeobj.md#c.PyTypeObject.tp_weaklist "PyTypeObject.tp_weaklist")
>
>   * [`tp_vectorcall`](typeobj.md#c.PyTypeObject.tp_vectorcall "PyTypeObject.tp_vectorcall")
>
>   * [`tp_weaklistoffset`](typeobj.md#c.PyTypeObject.tp_weaklistoffset "PyTypeObject.tp_weaklistoffset") (改用 [`Py_TPFLAGS_MANAGED_WEAKREF`](typeobj.md#c.Py_TPFLAGS_MANAGED_WEAKREF "Py_TPFLAGS_MANAGED_WEAKREF"))
>
>   * [`tp_dictoffset`](typeobj.md#c.PyTypeObject.tp_dictoffset "PyTypeObject.tp_dictoffset") (改用 [`Py_TPFLAGS_MANAGED_DICT`](typeobj.md#c.Py_TPFLAGS_MANAGED_DICT "Py_TPFLAGS_MANAGED_DICT"))
>
>   * [`tp_vectorcall_offset`](typeobj.md#c.PyTypeObject.tp_vectorcall_offset "PyTypeObject.tp_vectorcall_offset") (参见 [PyMemberDef](structures.md#pymemberdef-offsets))
>
>

>
> 在某些平台上设置 `Py_tp_bases` 或 `Py_tp_base` 可能会有问题。 为了避免问题，请改用 `PyType_FromSpecWithBases()` 的 _bases_ 参数。

在 3.9 版本发生变更: [`PyBufferProcs`](typeobj.md#c.PyBufferProcs "PyBufferProcs") 中的槽位可能会在不受限 API 中被设置。

在 3.11 版本发生变更: 现在 [`bf_getbuffer`](typeobj.md#c.PyBufferProcs.bf_getbuffer "PyBufferProcs.bf_getbuffer") 和 [`bf_releasebuffer`](typeobj.md#c.PyBufferProcs.bf_releasebuffer "PyBufferProcs.bf_releasebuffer") 将在 [受限 API](stable.md#limited-c-api) 中可用。

void *pfunc¶  
~~~
    

~~~
