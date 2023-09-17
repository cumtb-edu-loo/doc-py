# 导入模块¶

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_ImportModule(const char *name)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

这是下面 `PyImport_ImportModuleEx()` 的简化版接口，将 _globals_ 和 _locals_ 参数设为 `NULL` 并将 _level_ 设为 0。 当 _name_ 参数包含一个点号（即指定了一个包的子模块）时， _fromlist_ 参数会被设为列表 `['*']` 这样返回值将为所指定的模块而不像在其他情况下那样为包含模块的最高层级包。 （不幸的是，这在 _name_ 实际上是指定一个子包而非子模块时将有一个额外的副作用：在包的 `__all__` 变量中指定的子模块会被加载。） 返回一个对所导入模块的新引用，或是在导入失败时返回 `NULL` 并设置一个异常。 模块导入失败同模块不会留 在 [`sys.modules`](3.标准库/sys.md#sys.modules "sys.modules") 中。

该函数总是使用绝对路径导入。

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_ImportModuleNoBlock(const char *name)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

该函数是 `PyImport_ImportModule()` 的一个被遗弃的别名。

在 3.3 版本发生变更: 在导入锁被另一线程掌控时此函数会立即失败。 但是从 Python 3.3 起，锁方案在大多数情况下都已切换为针对每个模块加锁，所以此函数的特殊行为已无必要。

从 3.13 版起不建议使用，将在 3.15 版中移除: Use `PyImport_ImportModule()` instead.

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_ImportModuleEx(const char *name, [PyObject](structures.md#c.PyObject "PyObject") *globals, [PyObject](structures.md#c.PyObject "PyObject") *locals, [PyObject](structures.md#c.PyObject "PyObject") *fromlist)¶  

    _返回值：新的引用。_

导入一个模块。 请参阅内置 Python 函数 [`__import__()`](functions.md#import__ "__import__") 获取完善的相关描述。

返回值是一个对所导入模块或最高层级包的新引用，或是在导入失败时则为 `NULL` 并设置一个异常。 与 [`__import__()`](functions.md#import__ "__import__") 类似，当请求一个包的子模块时返回值通常为该最高层级包，除非给出了一个非空的 _fromlist_ 。

导入失败将移动不完整的模块对象，就像 `PyImport_ImportModule()` 那样。

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_ImportModuleLevelObject([PyObject](structures.md#c.PyObject "PyObject") *name, [PyObject](structures.md#c.PyObject "PyObject") *globals, [PyObject](structures.md#c.PyObject "PyObject") *locals, [PyObject](structures.md#c.PyObject "PyObject") *fromlist, int level)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

导入一个模块。 关于此函数的最佳说明请参考内置 Python 函数 [`__import__()`](functions.md#import__ "__import__")，因为标准 [`__import__()`](functions.md#import__ "__import__") 函数会直接调用此函数。

返回值是一个对所导入模块或最高层级包的新引用，或是在导入失败时则为 `NULL` 并设置一个异常。 与 [`__import__()`](functions.md#import__ "__import__") 类似，当请求一个包的子模块时返回值通常为该最高层级包，除非给出了一个非空的 _fromlist_ 。

在 3.3 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_ImportModuleLevel(const char *name, [PyObject](structures.md#c.PyObject "PyObject") *globals, [PyObject](structures.md#c.PyObject "PyObject") *locals, [PyObject](structures.md#c.PyObject "PyObject") *fromlist, int level)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

类似于 `PyImport_ImportModuleLevelObject()`，但其名称为 UTF-8 编码的字符串而不是 Unicode 对象。

在 3.3 版本发生变更: 不再接受 _level_ 为负数值。

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_Import([PyObject](structures.md#c.PyObject "PyObject") *name)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

这是一个调用了当前“导入钩子函数”的更高层级接口（显式指定 _level_ 为 0，表示绝对导入）。 它将发起调用当前全局作用域下 `__builtins__` 中的 [`__import__()`](functions.md#import__ "__import__") 函数。 这意味着将使用当前环境下安装的任何导入钩子来完成导入。

该函数总是使用绝对路径导入。

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_ReloadModule([PyObject](structures.md#c.PyObject "PyObject") *m)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

重载一个模块。 返回一个指向被重载模块的新引用，或者在失败时返回 `NULL` 并设置一个异常（在此情况下模块仍然会存在）。

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_AddModuleRef(const char *name)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.13._

Return the module object corresponding to a module name.

The _name_ argument may be of the form `package.module`. First check the modules dictionary if there's one there, and if not, create a new one and insert it in the modules dictionary.

Return a [strong reference](../glossary.md#term-strong-reference) to the module on success. Return `NULL` with an exception set on failure.

The module name _name_ is decoded from UTF-8.

This function does not load or import the module; if the module wasn't already loaded, you will get an empty module object. Use `PyImport_ImportModule()` or one of its variants to import a module. Package structures implied by a dotted name for _name_ are not created if not already present.

在 3.13 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_AddModuleObject([PyObject](structures.md#c.PyObject "PyObject") *name)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Similar to `PyImport_AddModuleRef()`, but return a [borrowed reference](../glossary.md#term-borrowed-reference) and _name_ is a Python [`str`](stdtypes.md#str "str") object.

在 3.3 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_AddModule(const char *name)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Similar to `PyImport_AddModuleRef()`, but return a [borrowed reference](../glossary.md#term-borrowed-reference).

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_ExecCodeModule(const char *name, [PyObject](structures.md#c.PyObject "PyObject") *co)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

给定一个模块名称（可能为 `package.module` 形式）和一个从 Python 字节码文件读取或从内置函数 [`compile()`](functions.md#compile "compile") 获取的代码对象，加载该模块。 返回对该模块对象的新引用，或者如果发生错误则返回 `NULL` 并设置一个异常。 在发生错误的情况下 _name_ 会从 [`sys.modules`](3.标准库/sys.md#sys.modules "sys.modules") 中被移除，即使 _name_ 在进入 `PyImport_ExecCodeModule()` 时已存在于 [`sys.modules`](3.标准库/sys.md#sys.modules "sys.modules") 中。 在 [`sys.modules`](3.标准库/sys.md#sys.modules "sys.modules") 中保留未完全初始化的模块是危险的，因为导入这样的模块没有办法知识模块对象是否处于一种未知的（对于模块作者的意图来说可能是已损坏的）状态。

模块的 [`__spec__`](5.%20导入系统.md#spec__ "__spec__") 和 [`__loader__`](5.%20导入系统.md#loader__ "__loader__") 如果尚未设置的话，将被设为适当的值。 相应 spec 的加载器（如果已设置）将被设为模块的 `__loader__` 而在其他情况下将被设为 [`SourceFileLoader`](importlib.md#importlib.machinery.SourceFileLoader "importlib.machinery.SourceFileLoader") 的实例。

模块的 [`__file__`](5.%20导入系统.md#file__ "__file__") 属性将被设为代码对象的 `co_filename`。 如果适用，还将设置 [`__cached__`](5.%20导入系统.md#cached__ "__cached__")。

如果模块已被导入则此函数将重载它。 请参阅 `PyImport_ReloadModule()` 了解重载模块的预定方式。

如果 _name_ 指向一个形式为 `package.module` 的带点号的名称，则任何尚未创建的包结构仍然不会被创建。

另请参阅 `PyImport_ExecCodeModuleEx()` 和 `PyImport_ExecCodeModuleWithPathnames()`。

在 3.12 版本发生变更: [`__cached__`](5.%20导入系统.md#cached__ "__cached__") 和 [`__loader__`](5.%20导入系统.md#loader__ "__loader__") 的设置已被弃用。 替代设置参见 [`ModuleSpec`](importlib.md#importlib.machinery.ModuleSpec "importlib.machinery.ModuleSpec")。

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_ExecCodeModuleEx(const char *name, [PyObject](structures.md#c.PyObject "PyObject") *co, const char *pathname)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

类似于 `PyImport_ExecCodeModule()`，但如果 _pathname_ 不为 `NULL` 则会被设为模块对象的 [`__file__`](5.%20导入系统.md#file__ "__file__") 属性的值。

参见 `PyImport_ExecCodeModuleWithPathnames()`。

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_ExecCodeModuleObject([PyObject](structures.md#c.PyObject "PyObject") *name, [PyObject](structures.md#c.PyObject "PyObject") *co, [PyObject](structures.md#c.PyObject "PyObject") *pathname, [PyObject](structures.md#c.PyObject "PyObject") *cpathname)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

类似于 `PyImport_ExecCodeModuleEx()`，但如果 _cpathname_ 不为 `NULL` 则会被设为模块对象的 [`__cached__`](5.%20导入系统.md#cached__ "__cached__") 值。 在三个函数中，这是推荐使用的一个。

在 3.3 版本加入.

在 3.12 版本发生变更: [`__cached__`](5.%20导入系统.md#cached__ "__cached__") 的设置已被弃用。 替代设置参见 [`ModuleSpec`](importlib.md#importlib.machinery.ModuleSpec "importlib.machinery.ModuleSpec")。

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_ExecCodeModuleWithPathnames(const char *name, [PyObject](structures.md#c.PyObject "PyObject") *co, const char *pathname, const char *cpathname)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

类似于 `PyImport_ExecCodeModuleObject()`，但 _name_ , _pathname_ 和 _cpathname_ 为 UTF-8 编码的字符串。如果 _pathname_ 也被设为 `NULL` 则还会尝试根据 _cpathname_ 推断出前者的值。

在 3.2 版本加入.

在 3.3 版本发生变更: 如果只提供了字节码路径则会使用 `imp.source_from_cache()` 来计算源路径。

在 3.12 版本发生变更: 不再使用已移除的 `imp` 模块。

long PyImport_GetMagicNumber()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 Python 字节码文件（即 `.pyc` 文件）的魔数。 此魔数应当存在于字节码文件的开头四个字节中，按照小端字节序。 出错时返回 `-1`。

在 3.3 版本发生变更: 失败时返回值 `-1`。

const char *PyImport_GetMagicTag()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

针对 [**PEP 3147**](https://peps.python.org/pep-3147/) 格式的 Python 字节码文件名返回魔术标签字符串。 请记住在 `sys.implementation.cache_tag` 上的值是应当被用来代替此函数的更权威的值。

在 3.2 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_GetModuleDict()¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回用于模块管理的字典 (即 `sys.modules`)。 请注意这是针对每个解释器的变量。

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_GetModule([PyObject](structures.md#c.PyObject "PyObject") *name)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.8._

返回给定名称的已导入模块。 如果模块尚未导入则返回 `NULL` 但不会设置错误。 如果查找失败则返回 `NULL` 并设置错误。

在 3.7 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyImport_GetImporter([PyObject](structures.md#c.PyObject "PyObject") *path)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回针对一个 [`sys.path`](3.标准库/sys.md#sys.path "sys.path")/`pkg.__path__` 中条目 _path_ 的查找器对象，可能会从 [`sys.path_importer_cache`](3.标准库/sys.md#sys.path_importer_cache "sys.path_importer_cache") 字典中获取。 如果它尚未被缓存，则会遍历 [`sys.path_hooks`](3.标准库/sys.md#sys.path_hooks "sys.path_hooks") 直至找到一个能处理该路径条目的钩子。 如果没有可用的钩子则返回 `None`；这将告知调用方 [path based finder](../glossary.md#term-path-based-finder) 无法为该路径条目找到查找器。 结果将缓存到 [`sys.path_importer_cache`](3.标准库/sys.md#sys.path_importer_cache "sys.path_importer_cache") 中。 返回一个指向查找器对象的新引用。

int PyImport_ImportFrozenModuleObject([PyObject](structures.md#c.PyObject "PyObject") *name)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

加载名称为 _name_ 的已冻结模块。 成功时返回 `1`，如果未找到模块则返回 `0`，如果初始化失败则返回 `-1` 并设置一个异常。 要在加载成功后访问被导入的模块，请使用 `PyImport_ImportModule()`。 （请注意此名称有误导性 --- 如果模块已被导入此函数将重载它。）

在 3.3 版本加入.

在 3.4 版本发生变更: `__file__` 属性将不再在模块上设置。

int PyImport_ImportFrozenModule(const char *name)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

类似于 `PyImport_ImportFrozenModuleObject()`，但其名称为 UTF-8 编码的字符串而不是 Unicode 对象。

struct _frozen¶  

    

~~~
这是针对已冻结模块描述器的结构类型定义，与由 **freeze** 工具所生成的一致 (请参看 Python 源代码发行版中的 `Tools/freeze/`)。 其定义可在 `Include/import.h` 中找到:
~~~
    
    
~~~
struct _frozen {
    const char *name;
    const unsigned char *code;
    int size;
    bool is_package;
};
~~~

在 3.11 版本发生变更: 新的 `is_package` 字段指明模块是否为一个包。 这替代了将 `size` 设为负值的做法。

const struct _frozen *PyImport_FrozenModules¶  

    

~~~
该指针被初始化为指向一个 `_frozen` 记录的数组，以一个所有成员均为 `NULL` 或零的记录表示结束。 当一个冻结模块被导入时，它将在此表中被搜索。 第三方代码可以利用此方式来提供动态创建的冻结模块集。

int PyImport_AppendInittab(const char *name, [PyObject](structures.md#c.PyObject "PyObject") *(*initfunc)(void))¶  

    _Part of the[ Stable ABI](stable.md#stable)._

向现有的内置模块表添加一个模块。 这是对 `PyImport_ExtendInittab()` 的便捷包装，如果无法扩展表则返回 `-1`。 新的模块可使用名称 _name_ 来导入，并使用函数 _initfunc_ 作为在第一次尝试导入时调用的初始化函数。 此函数应当在 [`Py_Initialize()`](init.md#c.Py_Initialize "Py_Initialize") 之前调用。

struct _inittab¶  
~~~
    

~~~
描述内置模块列表中一个单独条目的结构体。 嵌入 Python 的程序可以将这些结构体的数组与 `PyImport_ExtendInittab()` 结合使用以提供额外的内置模块。 该结构体由两个成员组成:

const char *name¶  
~~~
    

~~~
模块名称，为一个 ASCII 编码的字符串。

int PyImport_ExtendInittab(struct _inittab *newtab)¶  
~~~
    

~~~
