# 模块对象¶

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyModule_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这个 C 类型实例 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 用来表示Python中的模块类型。在 Python 程序中该实例被暴露为 `types.ModuleType`。

int PyModule_Check([PyObject](structures.md#c.PyObject "PyObject") *p)¶  

    

~~~
当 _p_ 为模块类型的对象，或是模块子类型的对象时返回真值。该函数永远有返回值。

int PyModule_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
当 _p_ 为模块类型的对象且不是 `PyModule_Type` 的子类型的对象时返回真值。该函数永远有返回值。

[PyObject](structures.md#c.PyObject "PyObject") *PyModule_NewObject([PyObject](structures.md#c.PyObject "PyObject") *name)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

返回新的模块对象，其属性 [`__name__`](../reference/import.md#name__ "__name__") 为 _name_ 。模块的如下属性 [`__name__`](../reference/import.md#name__ "__name__"), `__doc__`, [`__package__`](../reference/import.md#package__ "__package__"), and [`__loader__`](../reference/import.md#loader__ "__loader__") 都会被自动填充。（所有属性除了 [`__name__`](../reference/import.md#name__ "__name__") 都被设为 `None`）。调用时应当提供 [`__file__`](../reference/import.md#file__ "__file__") 属性。

在 3.3 版本加入.

在 3.4 版本发生变更: 属性 [`__package__`](../reference/import.md#package__ "__package__") 和 [`__loader__`](../reference/import.md#loader__ "__loader__") 被设为 `None`。

[PyObject](structures.md#c.PyObject "PyObject") *PyModule_New(const char *name)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

这类似于 `PyModule_NewObject()`， 但其名称为 UTF-8 编码的字符串而不是 Unicode 对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyModule_GetDict([PyObject](structures.md#c.PyObject "PyObject") *module)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回实现 _module_ 的命名空间的字典对象；此对象与模块对象的 [`__dict__`](../library/stdtypes.md#object.__dict__ "object.__dict__") 属性相同。 如果 _module_ 不是一个模块对象（或模块对象的子类型），则会引发 [`SystemError`](../library/exceptions.md#SystemError "SystemError") 并返回 `NULL`。

建议扩展使用其他 `PyModule_*` 和 `PyObject_*` 函数而不是直接操纵模块的 [`__dict__`](../library/stdtypes.md#object.__dict__ "object.__dict__")。

[PyObject](structures.md#c.PyObject "PyObject") *PyModule_GetNameObject([PyObject](structures.md#c.PyObject "PyObject") *module)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

返回 _module_ 的 [`__name__`](../reference/import.md#name__ "__name__") 值。 如果模块未提供该值，或者如果它不是一个字符串，则会引发 [`SystemError`](../library/exceptions.md#SystemError "SystemError") 并返回 `NULL`。

在 3.3 版本加入.

const char *PyModule_GetName([PyObject](structures.md#c.PyObject "PyObject") *module)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

类似于 `PyModule_GetNameObject()` 但返回 `'utf-8'` 编码的名称。

void *PyModule_GetState([PyObject](structures.md#c.PyObject "PyObject") *module)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回模块的“状态”，也就是说，返回指向在模块创建时分配的内存块的指针，或者 `NULL`。 参见 `PyModuleDef.m_size`。

PyModuleDef *PyModule_GetDef([PyObject](structures.md#c.PyObject "PyObject") *module)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回指向模块创建所使用的 `PyModuleDef` 结构体的指针，或者如果模块不是使用结构体定义创建的则返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyModule_GetFilenameObject([PyObject](structures.md#c.PyObject "PyObject") *module)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回使用 _module_ 的 [`__file__`](../reference/import.md#file__ "__file__") 属性所加载的 _模块_ 的文件名。 如果属性未定义，或者如果它不是一个 Unicode 字符串，则会引发 [`SystemError`](../library/exceptions.md#SystemError "SystemError") 并返回 `NULL`；在其他情况下将返回一个指向 Unicode 对象的引用。

在 3.2 版本加入.

const char *PyModule_GetFilename([PyObject](structures.md#c.PyObject "PyObject") *module)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

类似于 `PyModule_GetFilenameObject()` 但会返回编码为 'utf-8' 的文件名。

自 3.2 版本弃用: `PyModule_GetFilename()` 对于不可编码的文件名会引发 [`UnicodeEncodeError`](../library/exceptions.md#UnicodeEncodeError "UnicodeEncodeError")，请改用 `PyModule_GetFilenameObject()`。

## 初始化 C 模块¶

模块对象通常是基于扩展模块（导出初始化函数的共享库），或内部编译模块（其中使用 [`PyImport_AppendInittab()`](import.md#c.PyImport_AppendInittab "PyImport_AppendInittab") 添加初始化函数）。 请参阅 [构建C/C++扩展](../extending/building.md#building) 或 [对嵌入 Python 功能进行扩展](../extending/embedding.md#extending-with-embedding) 了解详情。

初始化函数可以向 `PyModule_Create()` 传入一个模块定义实例，并返回结果模块对象，或者通过返回定义结构体本身来请求“多阶段初始化”。

type PyModuleDef¶  

    _Part of the[ Stable ABI](stable.md#stable) (including all members)._

模块定义结构，它保存创建模块对象所需的所有信息。每个模块通常只有一个这种类型的静态初始化变量

PyModuleDef_Base m_base¶  
~~~
    

~~~
始终将此成员初始化为 `PyModuleDef_HEAD_INIT`。

const char *m_name¶  
~~~
    

~~~
新模块的名称。

const char *m_doc¶  
~~~
    

~~~
模块的文档字符串；一般会使用通过 [`PyDoc_STRVAR`](intro.md#c.PyDoc_STRVAR "PyDoc_STRVAR") 创建的文档字符串变量。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") m_size¶  
~~~
    

~~~
可以把模块的状态保存在为单个模块分配的内存区域中，使用 `PyModule_GetState()` 检索，而不是保存在静态全局区。这使得模块可以在多个子解释器中安全地使用。

这个内存区域将在创建模块时根据 _m_size_ 分配，并在调用 `m_free` 函数（如果存在）在取消分配模块对象时释放。

将 `m_size` 设置为 `-1`，意味着这个模块具有全局状态，因此不支持子解释器。

将其设置为非负值，意味着模块可以重新初始化，并指定其状态所需要的额外内存大小。多阶段初始化需要非负的 `m_size`。

请参阅 [**PEP 3121**](https://peps.python.org/pep-3121/) 了解详情。

[PyMethodDef](structures.md#c.PyMethodDef "PyMethodDef") *m_methods¶  
~~~
    

~~~
一个指向模块函数表的指针，由 [`PyMethodDef`](structures.md#c.PyMethodDef "PyMethodDef") 描述。如果模块没有函数，可以为 `NULL`。

PyModuleDef_Slot *m_slots¶  
~~~
    

~~~
由针对多阶段初始化的槽位定义组成的数组，以一个 `{0, NULL}` 条目结束。 当使用单阶段初始化时， _m_slots_ 必须为 `NULL`。

在 3.5 版本发生变更: 在 3.5 版之前，此成员总是被设为 `NULL`，并被定义为:

> [inquiry](gcsupport.md#c.inquiry "inquiry") m_reload¶  
>
>  

[traverseproc](gcsupport.md#c.traverseproc "traverseproc") m_traverse¶  
~~~
    

~~~
在模块对象的垃圾回收遍历期间所调用的遍历函数，如果不需要则为 `NULL`。

如果模块状态已被请求但尚未分配则不会调用此函数。 在模块创建之后至模块执行之前（调用 `Py_mod_exec` 函数）就属于这种情况。 更确切地说，如果 `m_size` 大于 0 且模块状态（由 `PyModule_GetState()` 返回）为 `NULL` 则不会调用此函数。

在 3.9 版本发生变更: 在模块状态被分配之前不再调用。

[inquiry](gcsupport.md#c.inquiry "inquiry") m_clear¶  
~~~
    

~~~
在模块对象的垃圾回收清理期间所调用的清理函数，如果不需要则为 `NULL`。

如果模块状态已被请求但尚未分配则不会调用此函数。 在模块创建之后至模块执行之前（调用 `Py_mod_exec` 函数）就属于这种情况。 更确切地说，如果 `m_size` 大于 0 且模块状态（由 `PyModule_GetState()` 返回）为 `NULL` 则不会调用此函数。

就像 [`PyTypeObject.tp_clear`](typeobj.md#c.PyTypeObject.tp_clear "PyTypeObject.tp_clear") 那样，这个函数并不总是在模块被释放前被调用。例如，当引用计数足以确定一个对象不再被使用时，就会直接调用 `m_free`，而不使用循环垃圾回收器。

在 3.9 版本发生变更: 在模块状态被分配之前不再调用。

[freefunc](typeobj.md#c.freefunc "freefunc") m_free¶  
~~~
    

~~~
在模块对象的释放期间所调用的函数，如果不需要则为 `NULL`。

如果模块状态已被请求但尚未分配则不会调用此函数。 在模块创建之后至模块执行之前（调用 `Py_mod_exec` 函数）就属于这种情况。 更确切地说，如果 `m_size` 大于 0 且模块状态（由 `PyModule_GetState()` 返回）为 `NULL` 则不会调用此函数。

在 3.9 版本发生变更: 在模块状态被分配之前不再调用。

### 单阶段初始化¶

模块初始化函数可以直接创建并返回模块对象，称为“单阶段初始化”，使用以下两个模块创建函数中的一个：

[PyObject](structures.md#c.PyObject "PyObject") *PyModule_Create(PyModuleDef *def)¶  

    _返回值：新的引用。_

根据在 _def_ 中给出的定义创建一个新的模块对象。 它的行为类似于 `PyModule_Create2()` 将 _module_api_version_ 设为 `PYTHON_API_VERSION`。

[PyObject](structures.md#c.PyObject "PyObject") *PyModule_Create2(PyModuleDef *def, int module_api_version)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

创建一个新的模块对象，在参数 _def_ 中给出定义，设定API版本为参数 _module_api_version_ 。如果该版本与正在运行的解释器版本不匹配，则会触发 [`RuntimeWarning`](../library/exceptions.md#RuntimeWarning "RuntimeWarning")。

备注

大多数时候应该使用 `PyModule_Create()` 代替使用此函数，除非你确定需要使用它。

在初始化函数返回之前，生成的模块对象通常使用 `PyModule_AddObjectRef()` 等函数进行填充。

### 多阶段初始化¶

指定扩展的另一种方式是请求“多阶段初始化”。 以这种方式创建的扩展模块的行为更类似 Python 模块：初始化分为 _创建阶段_ 即创建模块对象时和 _执行阶段_ 即填充模块对象时。 这种区分类似于类的 `__new__()` 和 `__init__()` 方法。

与使用单阶段初始化创建的模块不同，这些模块不是单例：如果移除 _sys.modules_ 条目并重新导入模块，将会创建一个新的模块对象，而旧的模块则会成为常规的垃圾回收目标 —— 就像 Python 模块那样。 默认情况下，根据同一个定义创建的多个模块应该是相互独立的：对其中一个模块的更改不应影响其他模块。 这意味着所有状态都应该是模块对象 (例如使用 `PyModule_GetState()` ) 或其内容 (例如模块的 [`__dict__`](../library/stdtypes.md#object.__dict__ "object.__dict__") 或使用 [`PyType_FromSpec()`](type.md#c.PyType_FromSpec "PyType_FromSpec") 创建的单独类) 的特定状态。

所有使用多阶段初始化创建的模块都应该支持 [子解释器](init.md#sub-interpreter-support)。保证多个模块之间相互独立，通常就可以实现这一点。

要请求多阶段初始化，初始化函数 (PyInit_modulename) 返回一个包含非空的 `m_slots` 属性的 `PyModuleDef` 实例。在它被返回之前，这个 `PyModuleDef` 实例必须先使用以下函数初始化：

[PyObject](structures.md#c.PyObject "PyObject") *PyModuleDef_Init(PyModuleDef *def)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.5._

确保模块定义是一个正确初始化的Python对象，拥有正确的类型和引用计数。

返回转换为 `PyObject*` 的 _def_ ，如果发生错误，则返回 `NULL`。

在 3.5 版本加入.

模块定义的 _m_slots_ 成员必须指向一个 `PyModuleDef_Slot` 结构体数组:

type PyModuleDef_Slot¶  
~~~
    

~~~
int slot¶  
~~~
    

~~~
槽位 ID，从下面介绍的可用值中选择。

void *value¶  
~~~
    

~~~
槽位值，其含义取决于槽位 ID。

在 3.5 版本加入.

_m_slots_ 数组必须以一个 id 为 0 的槽位结束。

可用的槽位类型是:

Py_mod_create¶  
~~~
    

~~~
指定一个函数供调用以创建模块对象本身。 该槽位的 _value_ 指针必须指向一个具有如下签名的函数:

[PyObject](structures.md#c.PyObject "PyObject") *create_module([PyObject](structures.md#c.PyObject "PyObject") *spec, PyModuleDef *def)  
~~~
    

~~~
该函数接受一个 [`ModuleSpec`](../library/importlib.md#importlib.machinery.ModuleSpec "importlib.machinery.ModuleSpec") 实例，如 [**PEP 451**](https://peps.python.org/pep-0451/) 所定义的，以及模块定义。 它应当返回一个新的模块对象，或者设置一个错误并返回 `NULL`。

此函数应当保持最小化。 特别地，它不应当调用任意 Python 代码，因为尝试再次导入同一个模块可能会导致无限循环。

多个 `Py_mod_create` 槽位不能在一个模块定义中指定。

如果未指定 `Py_mod_create`，导入机制将使用 `PyModule_New()` 创建一个普通的模块对象。 名称是获取自 _spec_ 而非定义，以允许扩展模块动态地调整它们在模块层级结构中的位置并通过符号链接以不同的名称被导入，同时共享同一个模块定义。

不要求返回的对象必须为 `PyModule_Type` 的实例。 任何类型均可使用，只要它支持设置和获取导入相关的属性。 但是，如果 `PyModuleDef` 具有非 `NULL` 的 `m_traverse`, `m_clear`, `m_free`；非零的 `m_size`；或者 `Py_mod_create` 以外的槽位则只能返回 `PyModule_Type` 的实例。

Py_mod_exec¶  
~~~
    

~~~
指定一个供调用以 _执行_ 模块的函数。 这造价于执行一个 Python 模块的代码：通常，此函数会向模块添加类和常量。 此函数的签名为:

int exec_module([PyObject](structures.md#c.PyObject "PyObject") *module)  
~~~
    

~~~
如果指定了多个 `Py_mod_exec` 槽位，将按照它们在*m_slots*数组中出现的顺序进行处理。

Py_mod_multiple_interpreters¶  
~~~
    

~~~
指定以下的值之一:

Py_MOD_MULTIPLE_INTERPRETERS_NOT_SUPPORTED¶  
~~~
    

~~~
该模块不支持在子解释器中导入。

Py_MOD_MULTIPLE_INTERPRETERS_SUPPORTED¶  
~~~
    

~~~
该模块支持在子解释器中导入，但是它们必须要共享主解释器的 GIL。 （参见 [隔离扩展模块](../howto/isolating-extensions.md#isolating-extensions-howto)。）

Py_MOD_PER_INTERPRETER_GIL_SUPPORTED¶  
~~~
    

~~~
该模块支持在子解释器中导入，即使它们有自己的 GIL。 （参见 [隔离扩展模块](../howto/isolating-extensions.md#isolating-extensions-howto)。）

此槽位决定在子解释器中导入此模块是否会失败。

在一个模块定义中不能指定多个 `Py_mod_multiple_interpreters` 槽位。

如果未指定 `Py_mod_multiple_interpreters`，则导入机制默认为 `Py_MOD_MULTIPLE_INTERPRETERS_NOT_SUPPORTED`。

在 3.12 版本加入.

有关多阶段初始化的更多细节，请参阅PEP:489

### 底层模块创建函数¶

当使用多阶段初始化时，将会调用以下函数。例如，在动态创建模块对象的时候，可以直接使用它们。注意，必须调用 `PyModule_FromDefAndSpec` 和 `PyModule_ExecDef` 来完整地初始化一个模块。

[PyObject](structures.md#c.PyObject "PyObject") *PyModule_FromDefAndSpec(PyModuleDef *def, [PyObject](structures.md#c.PyObject "PyObject") *spec)¶  

    _返回值：新的引用。_

根据在 _def_ 中给出的定义和 ModuleSpec _spec_ 创建一个新的模块对象。 它的行为类似于 `PyModule_FromDefAndSpec2()` 将 _module_api_version_ 设为 `PYTHON_API_VERSION`。

在 3.5 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyModule_FromDefAndSpec2(PyModuleDef *def, [PyObject](structures.md#c.PyObject "PyObject") *spec, int module_api_version)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

创建一个新的模块对象，在参数 _def_ 和 _spec_ 中给出定义，设置API版本为参数 _module_api_version_ 。如果该版本与正在运行的解释器版本不匹配，则会触发 [`RuntimeWarning`](../library/exceptions.md#RuntimeWarning "RuntimeWarning")。

备注

大多数时候应该使用 `PyModule_FromDefAndSpec()` 代替使用此函数，除非你确定需要使用它。

在 3.5 版本加入.

int PyModule_ExecDef([PyObject](structures.md#c.PyObject "PyObject") *module, PyModuleDef *def)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

执行参数*def*中给出的任意执行槽（`Py_mod_exec`）。

在 3.5 版本加入.

int PyModule_SetDocString([PyObject](structures.md#c.PyObject "PyObject") *module, const char *docstring)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

将*module*的文档字符串设置为*docstring*。当使用 `PyModule_Create` 或 `PyModule_FromDefAndSpec` 从 `PyModuleDef` 创建模块时，会自动调用此函数。

在 3.5 版本加入.

int PyModule_AddFunctions([PyObject](structures.md#c.PyObject "PyObject") *module, [PyMethodDef](structures.md#c.PyMethodDef "PyMethodDef") *functions)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

将以 `NULL` 结尾的*functions*数组中的函数添加到*module*模块中。有关单个条目的更多细节，请参与 [`PyMethodDef`](structures.md#c.PyMethodDef "PyMethodDef") 文档（由于缺少共享的模块命名空间，在C中实现的模块级“函数”通常将模块作为它的第一个参数，与Python类的实例方法类似）。当使用 `PyModule_Create` 或 `PyModule_FromDefAndSpec` 从 `PyModuleDef` 创建模块时，会自动调用此函数。

在 3.5 版本加入.

### 支持函数¶

模块初始化函数（单阶段初始化）或通过模块的执行槽位调用的函数（多阶段初始化），可以使用以下函数，来帮助初始化模块的状态：

int PyModule_AddObjectRef([PyObject](structures.md#c.PyObject "PyObject") *module, const char *name, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

将一个名称为*name*的对象添加到*module*模块中。这是一个方便的函数，可以在模块的初始化函数中使用。

如果成功，返回 `0`。如果发生错误，引发异常并返回 `-1`。

如果*value*为 `NULL`，返回 `NULL`。在调用它时发生这种情况，必须抛出异常。

用法示例：
~~~
    
    
~~~cpp
static int
add_spam(PyObject *module, int value)
{
    PyObject *obj = PyLong_FromLong(value);
    if (obj == NULL) {
        return -1;
    }
    int res = PyModule_AddObjectRef(module, "spam", obj);
    Py_DECREF(obj);
    return res;
 }
~~~

这个例子也可以写成不显式地检查 _obj_ 是否为 `NULL`:

    
    
~~~cpp
static int
add_spam(PyObject *module, int value)
{
    PyObject *obj = PyLong_FromLong(value);
    int res = PyModule_AddObjectRef(module, "spam", obj);
    Py_XDECREF(obj);
    return res;
 }
~~~

注意在此情况下应当使用 `Py_XDECREF()` 而不是 `Py_DECREF()`，因为 _obj_ 可能为 `NULL`。

在 3.10 版本加入.

int PyModule_Add([PyObject](structures.md#c.PyObject "PyObject") *module, const char *name, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.13._

Similar to `PyModule_AddObjectRef()`, but "steals" a reference to _value_. It can be called with a result of function that returns a new reference without bothering to check its result or even saving it to a variable.

用法示例：

    
    
~~~
if (PyModule_Add(module, "spam", PyBytes_FromString(value)) < 0) {
    goto error;
}
~~~

在 3.13 版本加入.

int PyModule_AddObject([PyObject](structures.md#c.PyObject "PyObject") *module, const char *name, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

类似于 `PyModule_AddObjectRef()`，但会在成功时偷取一个对 _value_ 的引用（如果它返回 `0` 值）。

The new `PyModule_Add()` or `PyModule_AddObjectRef()` functions are recommended, since it is easy to introduce reference leaks by misusing the `PyModule_AddObject()` function.

备注

与其他窃取引用的函数不同，`PyModule_AddObject()` 只在 **成功** 时释放对 _value_ 的引用。

This means that its return value must be checked, and calling code must [`Py_XDECREF()`](refcounting.md#c.Py_XDECREF "Py_XDECREF") _value_ manually on error.

用法示例：

    
    
~~~
PyObject *obj = PyBytes_FromString(value);
if (PyModule_AddObject(module, "spam", obj) < 0) {
    // If 'obj' is not NULL and PyModule_AddObject() failed,
    // 'obj' strong reference must be deleted with Py_XDECREF().
    // If 'obj' is NULL, Py_XDECREF() does nothing.
    Py_XDECREF(obj);
    goto error;
}
// PyModule_AddObject() stole a reference to obj:
// Py_XDECREF(obj) is not needed here.
~~~

自 3.13 版本弃用: `PyModule_AddObject()` is [soft deprecated](../glossary.md#term-soft-deprecated).

int PyModule_AddIntConstant([PyObject](structures.md#c.PyObject "PyObject") *module, const char *name, long value)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将一个名称为*name*的整型常量添加到*module*模块中。这个方便的函数可以在模块的初始化函数中使用。如果发生错误，返回 `-1`，成功返回 `0`。

int PyModule_AddStringConstant([PyObject](structures.md#c.PyObject "PyObject") *module, const char *name, const char *value)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将一个名称为*name*的字符串常量添加到*module*模块中。这个方便的函数可以在模块的初始化函数中使用。字符串*value*必须以 `NULL` 结尾。如果发生错误，返回 `-1` ，成功返回 `0` 。

PyModule_AddIntMacro(module, macro)¶  

    

~~~
将一个整型常量添加到*module*模块中。名称和值取自*macro*参数。例如， `PyModule_AddIntMacro(module, AF_INET)` 将值为*AF_INET*的整型常量*AF_INET*添加到*module*模块中。如果发生错误，返回 `-1` ，成功返回 `0` 。

PyModule_AddStringMacro(module, macro)¶  
~~~
    

~~~
