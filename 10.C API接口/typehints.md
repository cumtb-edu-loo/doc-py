# 类型注解对象¶

提供几种用于类型提示的内置类型。 目前存在两种类型 -- [GenericAlias](stdtypes.md#types-genericalias) 和 [Union](stdtypes.md#types-union)。 只有 `GenericAlias` 会向 C 开放。

[PyObject](structures.md#c.PyObject "PyObject") *Py_GenericAlias([PyObject](structures.md#c.PyObject "PyObject") *origin, [PyObject](structures.md#c.PyObject "PyObject") *args)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.9._

创建一个 [GenericAlias](stdtypes.md#types-genericalias) 对象。 相当于调用 Python 类 [`types.GenericAlias`](types.md#types.GenericAlias "types.GenericAlias")。 参数 _origin_ 和 _args_ 分别设置 `GenericAlias` 的 `__origin__` 和 `__args__` 属性。 _origin_ 应该是一个 [PyTypeObject](type.md#c.PyTypeObject "PyTypeObject")*，而 _args_ 可以是一个 [PyTupleObject](tuple.md#c.PyTupleObject "PyTupleObject")* 或者任意 `PyObject*`。 如果传递的 _args_ 不是一个元组，则会自动构造一个单元组并将 `__args__` 设置为 `(args,)`。 对参数进行了最小限度的检查，因此即使 _origin_ 不是类型函数也会成功。 `GenericAlias` 的 `__parameters__` 属性是从 `__args__` 懒加载的。 如果失败，则会引发一个异常并返回 `NULL`。

下面是一个如何创建一个扩展类型泛型的例子:

    
    
~~~
...
static PyMethodDef my_obj_methods[] = {
    // Other methods.
    ...
    {"__class_getitem__", Py_GenericAlias, METH_O|METH_CLASS, "See PEP 585"}
    ...
}
~~~

参见

数据模型方法 [`__class_getitem__()`](datamodel.md#object.__class_getitem__ "object.__class_getitem__")。

在 3.9 版本加入.

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") Py_GenericAliasType¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.9._

由 `Py_GenericAlias()` 所返回的对象的 C 类型。等价于 Python 中的 [`types.GenericAlias`](types.md#types.GenericAlias "types.GenericAlias") 。

在 3.9 版本加入.

