# 使对象类型支持循环垃圾回收¶

Python 对循环引用的垃圾检测与回收需要“容器”对象类型的支持，此类型的容器对象中可能包含其它容器对象。不保存其它对象的引用的类型，或者只保存原子类型（如数字或字符串）的引用的类型，不需要显式提供垃圾回收的支持。

要创建一个容器类，类型对象的 [`tp_flags`](typeobj.md#c.PyTypeObject.tp_flags "PyTypeObject.tp_flags") 字段必须包括 [`Py_TPFLAGS_HAVE_GC`](typeobj.md#c.Py_TPFLAGS_HAVE_GC "Py_TPFLAGS_HAVE_GC") 并提供一个 [`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 处理句柄的实现。 如果该类型的实例是可变的，则还必须提供 [`tp_clear`](typeobj.md#c.PyTypeObject.tp_clear "PyTypeObject.tp_clear") 的实现。

[`Py_TPFLAGS_HAVE_GC`](typeobj.md#c.Py_TPFLAGS_HAVE_GC "Py_TPFLAGS_HAVE_GC")

    

~~~
设置了此标志位的类型的对象必须符合此处记录的规则。为方便起见，下文把这些对象称为容器对象。

容器类型的构造函数必须符合两个规则：

  1. 该对象的内在必须使用 `PyObject_GC_New` 或 `PyObject_GC_NewVar` 来分配。

  2. 初始化了所有可能包含其他容器的引用的字段后，它必须调用 `PyObject_GC_Track()` 。

同样的，对象的释放器必须符合两个类似的规则：

  1. 在引用其它容器的字段失效前，必须调用 `PyObject_GC_UnTrack()` 。

  2. 必须使用 `PyObject_GC_Del()` 释放对象的内存。

警告

如果一个类型添加了 Py_TPFLAGS_HAVE_GC，则它 _必须_ 实现至少一个 [`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 句柄或显式地使用来自其一个或多个子类的句柄。

当调用 [`PyType_Ready()`](type.md#c.PyType_Ready "PyType_Ready") 或者某些间接调用该函数的 API 如 [`PyType_FromSpecWithBases()`](type.md#c.PyType_FromSpecWithBases "PyType_FromSpecWithBases") 或 [`PyType_FromSpec()`](type.md#c.PyType_FromSpec "PyType_FromSpec") 时解释器将自动填充 [`tp_flags`](typeobj.md#c.PyTypeObject.tp_flags "PyTypeObject.tp_flags"), [`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 和 [`tp_clear`](typeobj.md#c.PyTypeObject.tp_clear "PyTypeObject.tp_clear") 字段，如果该类型是继承自实现了垃圾回收器协议的类并且该子类 _没有_ 包括 [`Py_TPFLAGS_HAVE_GC`](typeobj.md#c.Py_TPFLAGS_HAVE_GC "Py_TPFLAGS_HAVE_GC") 旗标的话。

PyObject_GC_New(TYPE, typeobj)¶  
~~~
    

~~~
类似于 [`PyObject_New`](allocation.md#c.PyObject_New "PyObject_New") 但专用于设置了 [`Py_TPFLAGS_HAVE_GC`](typeobj.md#c.Py_TPFLAGS_HAVE_GC "Py_TPFLAGS_HAVE_GC") 旗标的容器对象。

PyObject_GC_NewVar(TYPE, typeobj, size)¶  
~~~
    

~~~
与 [`PyObject_NewVar`](allocation.md#c.PyObject_NewVar "PyObject_NewVar") 类似但专用于设置了 [`Py_TPFLAGS_HAVE_GC`](typeobj.md#c.Py_TPFLAGS_HAVE_GC "Py_TPFLAGS_HAVE_GC") 旗标的容器对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyUnstable_Object_GC_NewWithExtraData([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type, size_t extra_size)¶  
~~~
    

~~~
_This is[ Unstable API](stable.md#unstable-c-api). It may change without warning in minor releases._

与 `PyObject_GC_New` 类似但会在对象的末尾分配 _extra_size_ 个字节（在 [`tp_basicsize`](typeobj.md#c.PyTypeObject.tp_basicsize "PyTypeObject.tp_basicsize") 偏移量处）。 除 [`Python 对象标头`](structures.md#c.PyObject "PyObject") 外，分配的内存将初始化为零。

附加数据将与对象一起被释放，但在其他情况下则不会由 Python 来管理。

警告

此函数被标记为非稳定的因为在实例之后保留附加数据的机制尚未确定。 要分配可变数量的字段，推荐改用 [`PyVarObject`](structures.md#c.PyVarObject "PyVarObject") 和 [`tp_itemsize`](typeobj.md#c.PyTypeObject.tp_itemsize "PyTypeObject.tp_itemsize")。

在 3.12 版本加入.

TYPE *PyObject_GC_Resize(TYPE, [PyVarObject](structures.md#c.PyVarObject "PyVarObject") *op, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") newsize)¶  
~~~
    

~~~
重新调整 [`PyObject_NewVar`](allocation.md#c.PyObject_NewVar "PyObject_NewVar") 所分配对象的大小。 返回调整大小后的对象或在失败时返回 `NULL`。 _op_ 必须尚未被垃圾回收器追踪。

void PyObject_GC_Track([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

把对象 _op_ 加入到垃圾回收器跟踪的容器对象中。对象在被回收器跟踪时必须保持有效的，因为回收器可能在任何时候开始运行。在 [`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 处理前的所有字段变为有效后，必须调用此函数，通常在靠近构造函数末尾的位置。

int PyObject_IS_GC([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  
~~~
    

~~~
如果对象实现了垃圾回收器协议则返回非零值，否则返回 0。

如果此函数返回 0 则对象无法被垃圾回收器追踪。

int PyObject_GC_IsTracked([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.9._

如果 _op_ 对象的类型实现了 GC 协议且 _op_ 目前正被垃圾回收器追踪则返回 1， 否则返回 0。

这类似于 Python 函数 [`gc.is_tracked()`](../library/gc.md#gc.is_tracked "gc.is_tracked")。

在 3.9 版本加入.

int PyObject_GC_IsFinalized([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.9._

如果 _op_ 对象的类型实现了 GC 协议且 _op_ 已经被垃圾回收器终结则返回 1， 否则返回 0。

这类似于 Python 函数 [`gc.is_finalized()`](../library/gc.md#gc.is_finalized "gc.is_finalized")。

在 3.9 版本加入.

void PyObject_GC_Del(void *op)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

使用 `PyObject_GC_New` 或 `PyObject_GC_NewVar` 释放分配给对象的内存。

void PyObject_GC_UnTrack(void *op)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

从回收器跟踪的容器对象集合中移除 _op_ 对象。 请注意可以在此对象上再次调用 `PyObject_GC_Track()` 以将其加回到被跟踪对象集合。 释放器 ([`tp_dealloc`](typeobj.md#c.PyTypeObject.tp_dealloc "PyTypeObject.tp_dealloc") 句柄) 应当在 [`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 句柄所使用的任何字段失效之前为对象调用此函数。

在 3.8 版本发生变更: `_PyObject_GC_TRACK()` 和 `_PyObject_GC_UNTRACK()` 宏已从公有 C API 中删除。

[`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 处理接收以下类型的函数形参。

typedef int (*visitproc)([PyObject](structures.md#c.PyObject "PyObject") *object, void *arg)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

传给 [`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 处理的访问函数的类型。 _object_ 是容器中需要被遍历的一个对象，第三个形参对应于 [`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 处理的 _arg_ 。Python核心使用多个访问者函数实现循环引用的垃圾检测，不需要用户自行实现访问者函数。

[`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 处理必须是以下类型：

typedef int (*traverseproc)([PyObject](structures.md#c.PyObject "PyObject") *self, visitproc visit, void *arg)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

用于容器对象的遍历函数。 它的实现必须对 _self_ 所直接包含的每个对象调用 _visit_ 函数， _visit_ 的形参为所包含对象和传给处理程序的 _arg_ 值。 _visit_ 函数调用不可附带 `NULL` 对象作为参数。 如果 _visit_ 返回非零值，则该值应当被立即返回。

为了简化 [`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 处理的实现，Python提供了一个 `Py_VISIT()` 宏。若要使用这个宏，必须把 [`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 的参数命名为 _visit_ 和 _arg_ 。

void Py_VISIT([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果 _o_ 不为 `NULL`，则调用 _visit_ 回调函数，附带参数 _o_ 和 _arg_ 。 如果 _visit_ 返回一个非零值，则返回该值。 使用此宏之后，[`tp_traverse`](typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 处理程序的形式如下:
~~~
    
    
~~~cpp
static int
my_traverse(Noddy *self, visitproc visit, void *arg)
{
    Py_VISIT(self->foo);
    Py_VISIT(self->bar);
    return 0;
}
~~~

[`tp_clear`](typeobj.md#c.PyTypeObject.tp_clear "PyTypeObject.tp_clear") 处理程序必须为 `inquiry` 类型，如果对象不可变则为 `NULL`。

typedef int (*inquiry)([PyObject](structures.md#c.PyObject "PyObject") *self)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

丢弃产生循环引用的引用。不可变对象不需要声明此方法，因为他们不可能直接产生循环引用。需要注意的是，对象在调用此方法后必须仍是有效的（不能对引用只调用 [`Py_DECREF()`](refcounting.md#c.Py_DECREF "Py_DECREF") 方法）。当垃圾回收器检测到该对象在循环引用中时，此方法会被调用。

## 控制垃圾回收器状态¶

这个 C-API 提供了以下函数用于控制垃圾回收的运行。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyGC_Collect(void)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

执行完全的垃圾回收，如果垃圾回收器已启用的话。 （请注意 [`gc.collect()`](../library/gc.md#gc.collect "gc.collect") 会无条件地执行它。）

返回已回收的 + 无法回收的不可获取对象的数量。 如果垃圾回收器被禁用或已在执行回收，则立即返回 `0`。 在垃圾回收期间发生的错误会被传给 [`sys.unraisablehook`](../library/sys.md#sys.unraisablehook "sys.unraisablehook")。 此函数不会引发异常。

int PyGC_Enable(void)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

启用垃圾回收器：类似于 [`gc.enable()`](../library/gc.md#gc.enable "gc.enable")。 返回之前的状态，0 为禁用而 1 为启用。

在 3.10 版本加入.

int PyGC_Disable(void)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

禁用垃圾回收器：类似于 [`gc.disable()`](../library/gc.md#gc.disable "gc.disable")。 返回之前的状态，0 为禁用而 1 为启用。

在 3.10 版本加入.

int PyGC_IsEnabled(void)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

查询垃圾回收器的状态：类似于 [`gc.isenabled()`](../library/gc.md#gc.isenabled "gc.isenabled")。 返回当前的状态，0 为禁用而 1 为启用。

在 3.10 版本加入.

## 查询垃圾回收器状态¶

该 C-API 提供了以下接口用于查询有关垃圾回收器的信息。

void PyUnstable_GC_VisitObjects(gcvisitobjects_t callback, void *arg)¶  

    

~~~
_This is[ Unstable API](stable.md#unstable-c-api). It may change without warning in minor releases._

在全部活动的支持 GC 的对象上运行所提供的 _callback_ 。 _arg_ 会被传递给所有 _callback_ 的发起调用。

警告

如果新对象被回调（取消）分配后再被访问其行为是未定义的。

垃圾回收在运行期间被禁用。 在回调中显式地运行回收可能导致未定义的行为，例如多次访问同一对象或完全不访问。

在 3.12 版本加入.

typedef int (*gcvisitobjects_t)([PyObject](structures.md#c.PyObject "PyObject") *object, void *arg)¶  
~~~
    

~~~
