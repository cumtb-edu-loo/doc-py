# `types` \--- 动态类型创建和内置类型名称¶

**源代码:** [Lib/types.py](https://github.com/python/cpython/tree/3.12/Lib/types.py)

* * *

此模块定义了一些工具函数，用于协助动态创建新的类型。

它还为某些对象类型定义了名称，这些名称由标准 Python 解释器所使用，但并不像内置的 [`int`](functions.md#int "int") 或 [`str`](stdtypes.md#str "str") 那样对外公开。

最后，它还额外提供了一些类型相关但重要程度不足以作为内置对象的工具类和函数。

## 动态类型创建¶

types.new_class( _name_ , _bases =()_, _kwds =None_, _exec_body =None_)¶

    

~~~
使用适当的元类动态地创建一个类对象。

前三个参数是组成类定义头的部件：类名称，基类 (有序排列)，关键字参数 (例如 `metaclass`)。

_exec_body_ 参数是一个回调函数，用于填充新创建类的命名空间。 它应当接受类命名空间作为其唯一的参数并使用类内容直接更新命名空间。 如果未提供回调函数，则它就等效于传入 `lambda ns: None`。

在 3.3 版本加入.

types.prepare_class( _name_ , _bases =()_, _kwds =None_)¶
~~~
    

~~~
计算适当的元类并创建类命名空间。

参数是组成类定义头的部件：类名称，基类 (有序排列) 以及关键字参数 (例如 `metaclass`)。

返回值是一个 3 元组: `metaclass, namespace, kwds`

_metaclass_ 是适当的元类， _namespace_ 是预备好的类命名空间而 _kwds_ 是所传入 _kwds_ 参数移除每个 `'metaclass'` 条目后的已更新副本。 如果未传入 _kwds_ 参数，这将为一个空字典。

在 3.3 版本加入.

在 3.6 版本发生变更: 所返回元组中 `namespace` 元素的默认值已被改变。 现在当元类没有 `__prepare__` 方法时将会使用一个保留插入顺序的映射。

参见

[元类](../reference/datamodel.md#metaclasses)
~~~
    

~~~
这些函数所支持的类创建过程的完整细节

[**PEP 3115**](https://peps.python.org/pep-3115/) \- Python 3000 中的元类
~~~
    

~~~
引入 `__prepare__` 命名空间钩子

types.resolve_bases( _bases_ )¶
~~~
    

~~~
动态地解析 MRO 条目，具体描述见 [**PEP 560**](https://peps.python.org/pep-0560/)。

此函数会在 _bases_ 中查找不是 [`type`](functions.md#type "type") 的实例的项，并返回一个元组，其中每个具有 [`__mro_entries__()`](../reference/datamodel.md#object.__mro_entries__ "object.__mro_entries__") 方法的此种对象将被替换为调用该方法解包后的结果。 如果一个 _bases_ 项是 [`type`](functions.md#type "type") 的实例，或它不具有 `__mro_entries__()` 方法 ，则它将不加改变地被包括在返回的元组中。

在 3.7 版本加入.

types.get_original_bases( _cls_ , _/_ )¶
~~~
    

~~~
在 [`__mro_entries__()`](../reference/datamodel.md#object.__mro_entries__ "object.__mro_entries__") 方法在任何基类上被调用之前返回最初是作为 _cls_ 的基类给出的对象元组（根据 [**PEP 560**](https://peps.python.org/pep-0560/) 所描述的机制）。 这在对 [泛型](typing.md#user-defined-generics) 进行内省时很有用处。

对于具有 `__orig_bases__` 属性的类，此函数将返回 `cls.__orig_bases__` 的值。 对于没有 `__orig_bases__` 属性的类，则将返回 `cls.__bases__`。

示例:
~~~
    
    
~~~
from typing import TypeVar, Generic, NamedTuple, TypedDict

T = TypeVar("T")
class Foo(Generic[T]): ...
class Bar(Foo[int], float): ...
class Baz(list[str]): ...
Eggs = NamedTuple("Eggs", [("a", int), ("b", str)])
Spam = TypedDict("Spam", {"a": int, "b": str})

assert Bar.__bases__ == (Foo, float)
assert get_original_bases(Bar) == (Foo[int], float)

assert Baz.__bases__ == (list,)
assert get_original_bases(Baz) == (list[str],)

assert Eggs.__bases__ == (tuple,)
assert get_original_bases(Eggs) == (NamedTuple,)

assert Spam.__bases__ == (dict,)
assert get_original_bases(Spam) == (TypedDict,)

assert int.__bases__ == (object,)
assert get_original_bases(int) == (object,)
~~~

在 3.12 版本加入.

参见

[**PEP 560**](https://peps.python.org/pep-0560/) \- 对 typing 模块和泛型类型的核心支持

## 标准解释器类型¶

此模块为许多类型提供了实现 Python 解释器所要求的名称。 它刻意地避免了包含某些仅在处理过程中偶然出现的类型，例如 `listiterator` 类型。

此种名称的典型应用如 [`isinstance()`](functions.md#isinstance "isinstance") 或 [`issubclass()`](functions.md#issubclass "issubclass") 检测。

如果你要实例化这些类型中的任何一种，请注意其签名在不同 Python 版本之间可能出现变化。

以下类型有相应的标准名称定义：

types.NoneType¶

    

~~~
[`None`](constants.md#None "None") 的类型。

在 3.10 版本加入.

types.FunctionType¶

types.LambdaType¶
~~~
    

~~~
用户自定义函数以及由 [`lambda`](../reference/expressions.md#lambda) 表达式所创建函数的类型。

引发一个 [审计事件](sys.md#auditing) `function.__new__`，附带参数 `code`。

此审计事件只会被函数对象的直接实例化引发，而不会被普通编译所引发。

types.GeneratorType¶
~~~
    

~~~
[generator](../glossary.md#term-generator) 迭代器对象的类型，由生成器函数创建。

types.CoroutineType¶
~~~
    

~~~
[coroutine](../glossary.md#term-coroutine) 对象的类型，由 [`async def`](../reference/compound_stmts.md#async-def) 函数创建。

在 3.5 版本加入.

types.AsyncGeneratorType¶
~~~
    

~~~
[asynchronous generator](../glossary.md#term-asynchronous-generator) 迭代器对象的类型，由异步生成器函数创建。

在 3.6 版本加入.

_class _types.CodeType( _** kwargs_)¶
~~~
    

~~~
代码对象的类型，例如 [`compile()`](functions.md#compile "compile") 的返回值。

引发 [审计事件](sys.md#auditing) `code.__new__` 附带参数 `code`, `filename`, `name`, `argcount`, `posonlyargcount`, `kwonlyargcount`, `nlocals`, `stacksize`, `flags`。

请注意被审计的参数可能与初始化代码所要求的名称或位置不相匹配。 审计事件只会被代码对象的直接实例化引发，而不会被普通编译所引发。

replace( _** kwargs_)¶
~~~
    

~~~
返回代码对象的一个副本，使用指定的新字段值。

Code objects are also supported by generic function [`copy.replace()`](copy.md#copy.replace "copy.replace").

在 3.8 版本加入.

types.CellType¶
~~~
    

~~~
单元对象的类型：这种对象被用作函数中自由变量的容器。

在 3.8 版本加入.

types.MethodType¶
~~~
    

~~~
用户自定义类实例方法的类型。

types.BuiltinFunctionType¶

types.BuiltinMethodType¶
~~~
    

~~~
内置函数例如 [`len()`](functions.md#len "len") 或 [`sys.exit()`](sys.md#sys.exit "sys.exit") 以及内置类方法的类型。 （这里所说的“内置”是指“以 C 语言编写”。）

types.WrapperDescriptorType¶
~~~
    

~~~
某些内置数据类型和基类的方法的类型，例如 [`object.__init__()`](../reference/datamodel.md#object.__init__ "object.__init__") 或 [`object.__lt__()`](../reference/datamodel.md#object.__lt__ "object.__lt__")。

在 3.7 版本加入.

types.MethodWrapperType¶
~~~
    

~~~
某些内置数据类型和基类的 _绑定_ 方法的类型。 例如 `object().__str__` 所属的类型。

在 3.7 版本加入.

types.NotImplementedType¶
~~~
    

~~~
[`NotImplemented`](constants.md#NotImplemented "NotImplemented") 的类型。

在 3.10 版本加入.

types.MethodDescriptorType¶
~~~
    

~~~
某些内置数据类型方法例如 [`str.join()`](stdtypes.md#str.join "str.join") 的类型。

在 3.7 版本加入.

types.ClassMethodDescriptorType¶
~~~
    

~~~
某些内置数据类型 _非绑定_ 类方法例如 `dict.__dict__['fromkeys']` 的类型。

在 3.7 版本加入.

_class _types.ModuleType( _name_ , _doc =None_)¶
~~~
    

~~~
[模块](../glossary.md#term-module) 的类型。 构造器接受待创建模块的名称并以其 [docstring](../glossary.md#term-docstring) 作为可选参数。

备注

如果你希望设置各种由导入控制的属性，请使用 [`importlib.util.module_from_spec()`](importlib.md#importlib.util.module_from_spec "importlib.util.module_from_spec") 来创建一个新模块。

__doc__¶
~~~
    

~~~
模块的 [docstring](../glossary.md#term-docstring)。 默认为 `None`。

__loader__¶
~~~
    

~~~
用于加载模块的 [loader](../glossary.md#term-loader)。 默认为 `None`。

此属性会匹配保存在 [`__spec__`](../reference/import.md#spec__ "__spec__") object 对象中的 [`importlib.machinery.ModuleSpec.loader`](importlib.md#importlib.machinery.ModuleSpec.loader "importlib.machinery.ModuleSpec.loader")。

备注

未来的 Python 版本可能会停止默认设置此属性。 为了避免这个潜在变化的影响，如果你明确地需要使用此属性则推荐改从 [`__spec__`](../reference/import.md#spec__ "__spec__") 属性读取或是使用 `getattr(module, "__loader__", None)`。

在 3.4 版本发生变更: 默认为 `None`。 之前该属性为可选项。

__name__¶
~~~
    

~~~
模块的名称。 应当能匹配 [`importlib.machinery.ModuleSpec.name`](importlib.md#importlib.machinery.ModuleSpec.name "importlib.machinery.ModuleSpec.name")。

__package__¶
~~~
    

~~~
一个模块所属的 [package](../glossary.md#term-package)。 如果模块为最高层级的（即不是任何特定包的组成部分）则该属性应设为 `''`，否则它应设为特定包的名称 (如果模块本身也是一个包则名称可以为 [`__name__`](../reference/import.md#name__ "__name__"))。 默认为 `None`。

此属性会匹配保存在 [`__spec__`](../reference/import.md#spec__ "__spec__") 对象中的 [`importlib.machinery.ModuleSpec.parent`](importlib.md#importlib.machinery.ModuleSpec.parent "importlib.machinery.ModuleSpec.parent")。

备注

未来的 Python 版本可能停止默认设置此属性。 为了避免这个潜在变化的影响，如果你明确地需要使用此属性则推荐改从 [`__spec__`](../reference/import.md#spec__ "__spec__") 属性读取或是使用 `getattr(module, "__package__", None)`。

在 3.4 版本发生变更: 默认为 `None`。 之前该属性为可选项。

__spec__¶
~~~
    

~~~
模块的导入系统相关状态的记录。 应当是一个 [`importlib.machinery.ModuleSpec`](importlib.md#importlib.machinery.ModuleSpec "importlib.machinery.ModuleSpec") 的实例。

在 3.4 版本加入.

types.EllipsisType¶
~~~
    

~~~
[`Ellipsis`](constants.md#Ellipsis "Ellipsis") 的类型。

在 3.10 版本加入.

_class _types.GenericAlias( _t_origin_ , _t_args_ )¶
~~~
    

~~~
[形参化泛型](stdtypes.md#types-genericalias) 的类型，例如 `list[int]`。

`t_origin` 应当是一个非形参化的泛型类，例如 `list`, `tuple` 或 `dict`。 `t_args` 应当是一个形参化 `t_origin` 的 [`tuple`](stdtypes.md#tuple "tuple") (长度可以为 1):
~~~
    
    
~~~shell
>>> from types import GenericAlias

>>> list[int] == GenericAlias(list, (int,))
True
>>> dict[str, int] == GenericAlias(dict, (str, int))
True
~~~

在 3.9 版本加入.

在 3.9.2 版本发生变更: 此类型现在可以被子类化。

参见

[泛用别名类型](stdtypes.md#types-genericalias)

    

~~~
有关 `types.GenericAlias` 实例的详细文档

[**PEP 585**](https://peps.python.org/pep-0585/) \- 标准多项集中的类型提示泛型
~~~
    

~~~
引入 `types.GenericAlias` 类

_class _types.UnionType¶
~~~
    

~~~
[合并类型表达式](stdtypes.md#types-union) 的类型。

在 3.10 版本加入.

_class _types.TracebackType( _tb_next_ , _tb_frame_ , _tb_lasti_ , _tb_lineno_ )¶
~~~
    

~~~
回溯对象的类型，如在 `sys.exception().__traceback__` 中找到的一样。

请查看 [语言参考](../reference/datamodel.md#traceback-objects) 了解可用属性和操作的细节，以及动态地创建回溯对象的指南。

types.FrameType¶
~~~
    

~~~
帧对象的类型，例如 `tb.tb_frame` 中的对象，其中 `tb` 是一个回溯对象。

请查看 [语言参考](../reference/datamodel.md#frame-objects) 了解可用属性和操作的细节。

types.GetSetDescriptorType¶
~~~
    

~~~
使用 `PyGetSetDef` 在扩展模块中定义的对象的类型，例如 `FrameType.f_locals` 或 `array.array.typecode`。 此类型被用作对象属性的描述器；它的目的与 [`property`](functions.md#property "property") 类型相同，但专门针对在扩展模块中定义的类。

types.MemberDescriptorType¶
~~~
    

~~~
使用 `PyMemberDef` 在扩展模块中定义的对象的类型，例如 `datetime.timedelta.days`。 此类型被用作使用标准转换函数的简单 C 数据成员的描述器；它的目的与 [`property`](functions.md#property "property") 类型相同，但专门针对在扩展模块中定义的类。

**CPython 实现细节：** 在 Python 的其它实现中，此类型可能与 `GetSetDescriptorType` 完全相同。

_class _types.MappingProxyType( _mapping_ )¶
~~~
    

~~~
一个映射的只读代理。 它提供了对映射条目的动态视图，这意味着当映射发生改变时，视图会反映这些改变。

在 3.3 版本加入.

在 3.9 版本发生变更: 更新为支持 [**PEP 584**](https://peps.python.org/pep-0584/) 所新增的合并 (`|`) 运算符，它会简单地委托给下层的映射。

key in proxy
~~~
    

~~~
如果下层的映射中存在键 _key_ 则返回 `True`，否则返回 `False`。

proxy[key]
~~~
    

~~~
返回下层的映射中以 _key_ 为键的项。 如果下层的映射中不存在键 _key_ 则引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

iter(proxy)
~~~
    

~~~
返回由下层映射的键为元素的迭代器。 这是 `iter(proxy.keys())` 的快捷方式。

len(proxy)
~~~
    

~~~
返回下层映射中的项数。

copy()¶
~~~
    

~~~
返回下层映射的浅拷贝。

get( _key_ [, _default_ ])¶
~~~
    

~~~
如果 _key_ 存在于下层映射中则返回 _key_ 的值，否则返回 _default_ 。 如果 _default_ 未给出则默认为 `None`，因而此方法绝不会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

items()¶
~~~
    

~~~
返回由下层映射的项 (`(键, 值)` 对) 组成的一个新视图。

keys()¶
~~~
    

~~~
返回由下层映射的键组成的一个新视图。

values()¶
~~~
    

~~~
返回由下层映射的值组成的一个新视图。

reversed(proxy)
~~~
    

~~~
返回一个包含下层映射的键的反向迭代器。

在 3.9 版本加入.

hash(proxy)
~~~
    

~~~
返回下层映射的哈希值。

在 3.12 版本加入.

## 附加工具类和函数¶

_class _types.SimpleNamespace¶
~~~
    

~~~
一个简单的 [`object`](functions.md#object "object") 子类，提供了访问其命名空间的属性，以及一个有意义的 repr。

不同于 [`object`](functions.md#object "object")，对于 `SimpleNamespace` 你可以添加和移除属性。 如果一个 `SimpleNamespace` 对象使用关键字参数进行初始化，这些参数会被直接加入下层命名空间。

此类型大致等价于以下代码:
~~~
    
    
~~~
class SimpleNamespace:
    def __init__(self, /, **kwargs):
        self.__dict__.update(kwargs)

    def __repr__(self):
        items = (f"{k}={v!r}" for k, v in self.__dict__.items())
        return "{}({})".format(type(self).__name__, ", ".join(items))

    def __eq__(self, other):
        if isinstance(self, SimpleNamespace) and isinstance(other, SimpleNamespace):
           return self.__dict__ == other.__dict__
        return NotImplemented
~~~

`SimpleNamespace` 可被用于替代 `class NS: pass`。 但是，对于结构化记录类型则应改用 [`namedtuple()`](collections.md#collections.namedtuple "collections.namedtuple")。

`SimpleNamespace` objects are supported by [`copy.replace()`](copy.md#copy.replace "copy.replace").

在 3.3 版本加入.

在 3.9 版本发生变更: repr 中的属性顺序由字母顺序改为插入顺序 (类似 `dict`)。

types.DynamicClassAttribute( _fget =None_, _fset =None_, _fdel =None_, _doc =None_)¶

    

~~~
在类上访问 __getattr__ 的路由属性。

这是一个描述器，用于定义通过实例与通过类访问时具有不同行为的属性。 当实例访问时保持正常行为，但当类访问属性时将被路由至类的 __getattr__ 方法；这是通过引发 AttributeError 来完成的。

这允许有在实例上激活的特性属性，同时又有在类上的同名虚拟属性 (一个例子请参见 [`enum.Enum`](enum.md#enum.Enum "enum.Enum"))。

在 3.4 版本加入.

## 协程工具函数¶

types.coroutine( _gen_func_ )¶
~~~
    

~~~
