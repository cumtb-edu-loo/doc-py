# `collections.abc` \--- 容器的抽象基类¶

在 3.3 版本加入: 该模块曾是 [`collections`](collections.md#module-collections "collections: Container datatypes") 模块的组成部分。

**源代码：** [Lib/_collections_abc.py](https://github.com/python/cpython/tree/3.12/Lib/_collections_abc.py)

* * *

本模块提供了一些 [抽象基类](../glossary.md#term-abstract-base-class)，它们可用于测试一个类是否提供某个特定的接口；例如，它是否为 [hashable](../glossary.md#term-hashable) 或是否为映射等。

一个接口的 [`issubclass()`](functions.md#issubclass "issubclass") 或 [`isinstance()`](functions.md#isinstance "isinstance") 测试采用以下三种方式之一。

1) A newly written class can inherit directly from one of the abstract base classes. The class must supply the required abstract methods. The remaining mixin methods come from inheritance and can be overridden if desired. Other methods may be added as needed:

    
    
~~~
class C(Sequence):                      # Direct inheritance
    def __init__(self): ...             # Extra method not required by the ABC
    def __getitem__(self, index):  ...  # Required abstract method
    def __len__(self):  ...             # Required abstract method
    def count(self, value): ...         # Optionally override a mixin method
~~~
    
    
~~~shell
>>> issubclass(C, Sequence)
True
>>> isinstance(C(), Sequence)
True
~~~

2) Existing classes and built-in classes can be registered as "virtual subclasses" of the ABCs. Those classes should define the full API including all of the abstract methods and all of the mixin methods. This lets users rely on [`issubclass()`](functions.md#issubclass "issubclass") or [`isinstance()`](functions.md#isinstance "isinstance") tests to determine whether the full interface is supported. The exception to this rule is for methods that are automatically inferred from the rest of the API:

    
    
~~~
class D:                                 # No inheritance
    def __init__(self): ...              # Extra method not required by the ABC
    def __getitem__(self, index):  ...   # Abstract method
    def __len__(self):  ...              # Abstract method
    def count(self, value): ...          # Mixin method
    def index(self, value): ...          # Mixin method

Sequence.register(D)                     # Register instead of inherit
~~~
    
    
~~~shell
>>> issubclass(D, Sequence)
True
>>> isinstance(D(), Sequence)
True
~~~

在这个例子中，`D` 类不需要定义 `__contains__`, `__iter__` 和 `__reversed__` 因为 [in 运算符](../reference/expressions.md#comparisons), the [迭代](../glossary.md#term-iterable) 逻辑和 [`reversed()`](functions.md#reversed "reversed") 函数会自动回退为使用 `__getitem__` 和 `__len__`。

3) Some simple interfaces are directly recognizable by the presence of the required methods (unless those methods have been set to [`None`](constants.md#None "None")):

    
    
~~~
class E:
    def __iter__(self): ...
    def __next__(next): ...
~~~
    
    
~~~shell
>>> issubclass(E, Iterable)
True
>>> isinstance(E(), Iterable)
True
~~~

复杂的接口不支持最后这种技术手段因为接口并不只是作为方法名称存在。 接口指明了方法之间的语义和关系，这些是无法根据特定方法名称的存在推断出来的。 例如，知道一个类提供了 `__getitem__`, `__len__` 和 `__iter__` 并不足以区分 `Sequence` 和 `Mapping`。

在 3.9 版本加入: 这些抽象类现在都支持 `[]`。 参见 [GenericAlias 类型](stdtypes.md#types-genericalias) 和 [**PEP 585**](https://peps.python.org/pep-0585/)。

## 容器抽象基类¶

这个容器模块提供了以下 [ABCs](../glossary.md#term-abstract-base-class):

抽象基类

|

继承自

|

抽象方法

|

Mixin 方法  
  
---|---|---|---  
  
`Container` [1]

|  |

`__contains__`

|  
  
`Hashable` [1]

|  |

`__hash__`

|  
  
`Iterable` [1] [2]

|  |

`__iter__`

|  
  
`Iterator` [1]

|

`Iterable`

|

`__next__`

|

`__iter__`  
  
`Reversible` [1]

|

`Iterable`

|

`__reversed__`

|  
  
`Generator` [1]

|

`Iterator`

|

`send`, `throw`

|

`close`, `__iter__`, `__next__`  
  
`Sized` [1]

|  |

`__len__`

|  
  
`Callable` [1]

|  |

`__call__`

|  
  
`Collection` [1]

|

`Sized`, `Iterable`, `Container`

|

`__contains__`, `__iter__`, `__len__`

|  
  
`Sequence`

|

`Reversible`, `Collection`

|

`__getitem__`, `__len__`

|

`__contains__`, `__iter__`, `__reversed__`, `index`, and `count`  
  
`MutableSequence`

|

`Sequence`

|

`__getitem__`, `__setitem__`, `__delitem__`, `__len__`, `insert`

|

继承自 `Sequence` 的方法，以及 `append`, `reverse`, `extend`, `pop`, `remove`，和 `__iadd__`  
  
`ByteString`

|

`Sequence`

|

`__getitem__`, `__len__`

|

继承自 `Sequence` 的方法  
  
`Set`

|

`Collection`

|

`__contains__`, `__iter__`, `__len__`

|

`__le__`, `__lt__`, `__eq__`, `__ne__`, `__gt__`, `__ge__`, `__and__`, `__or__`, `__sub__`, `__xor__`, and `isdisjoint`  
  
`MutableSet`

|

`Set`

|

`__contains__`, `__iter__`, `__len__`, `add`, `discard`

|

继承自 `Set` 的方法以及 `clear`, `pop`, `remove`, `__ior__`, `__iand__`, `__ixor__`，和 `__isub__`  
  
`Mapping`

|

`Collection`

|

`__getitem__`, `__iter__`, `__len__`

|

`__contains__`, `keys`, `items`, `values`, `get`, `__eq__`, and `__ne__`  
  
`MutableMapping`

|

`Mapping`

|

`__getitem__`, `__setitem__`, `__delitem__`, `__iter__`, `__len__`

|

继承自 `Mapping` 的方法以及 `pop`, `popitem`, `clear`, `update`，和 `setdefault`  
  
`MappingView`

|

`Sized`

|  |

`__len__`  
  
`ItemsView`

|

`MappingView`, `Set`

|  |

`__contains__`, `__iter__`  
  
`KeysView`

|

`MappingView`, `Set`

|  |

`__contains__`, `__iter__`  
  
`ValuesView`

|

`MappingView`, `Collection`

|  |

`__contains__`, `__iter__`  
  
`Awaitable` [1]

|  |

`__await__`

|  
  
`Coroutine` [1]

|

`Awaitable`

|

`send`, `throw`

|

`close`  
  
`AsyncIterable` [1]

|  |

`__aiter__`

|  
  
`AsyncIterator` [1]

|

`AsyncIterable`

|

`__anext__`

|

`__aiter__`  
  
`AsyncGenerator` [1]

|

`AsyncIterator`

|

`asend`, `athrow`

|

`aclose`, `__aiter__`, `__anext__`  
  
`Buffer` [1]

|  |

`__buffer__`

|  
  
附注

[1] (1,2,3,4,5,6,7,8,9,10,11,12,13,14,15)

这些 ABC 重载了 `object.__subclasshook__()` 以便支持通过验证所需的方法是否存在并且没有被设为 [`None`](constants.md#None "None") 来测试一个接口。 这只适用于简单的接口。 更复杂的接口需要注册或直接子类化。

[2]

检查 `isinstance(obj, Iterable)` 是否侦测到被注册为 `Iterable` 或者具有 `__iter__()` 方法的类，但它不能侦测到使用 `__getitem__()` 方法进行迭代的类。 确定一个对象是否为 [iterable](../glossary.md#term-iterable) 的唯一可靠方式是调用 `iter(obj)`。

## 多项集抽象基类 -- 详细描述¶

_class _collections.abc.Container¶

    

~~~
提供了 `__contains__()` 方法的抽象基类。

_class _collections.abc.Hashable¶
~~~
    

~~~
提供了 `__hash__()` 方法的抽象基类。

_class _collections.abc.Sized¶
~~~
    

~~~
提供了 `__len__()` 方法的抽象基类。

_class _collections.abc.Callable¶
~~~
    

~~~
提供了 `__call__()` 方法的抽象基类。

_class _collections.abc.Iterable¶
~~~
    

~~~
提供了 `__iter__()` 方法的抽象基类。

使用 `isinstance(obj, Iterable)` 可以检测一个类是否已经注册到了 `Iterable` 或者实现了 `__iter__()` 函数，但是无法检测这个类是否能够使用 `__getitem__()` 方法进行迭代。检测一个对象是否是 [iterable](../glossary.md#term-iterable) 的唯一可信赖的方法是调用 `iter(obj)`。

_class _collections.abc.Collection¶
~~~
    

~~~
集合了 Sized 和 Iterable 类的抽象基类。

在 3.6 版本加入.

_class _collections.abc.Iterator¶
~~~
    

~~~
提供了 [`__iter__()`](stdtypes.md#iterator.__iter__ "iterator.__iter__") 和 [`__next__()`](stdtypes.md#iterator.__next__ "iterator.__next__") 方法的抽象基类。参见 [iterator](../glossary.md#term-iterator) 的定义。

_class _collections.abc.Reversible¶
~~~
    

~~~
为可迭代类提供了 `__reversed__()` 方法的抽象基类。

在 3.6 版本加入.

_class _collections.abc.Generator¶
~~~
    

~~~
生成器类，实现了 [**PEP 342**](https://peps.python.org/pep-0342/) 中定义的协议，继承并扩展了迭代器，提供了 [`send()`](../reference/expressions.md#generator.send "generator.send"), [`throw()`](../reference/expressions.md#generator.throw "generator.throw") 和 [`close()`](../reference/expressions.md#generator.close "generator.close") 方法。参见 [generator](../glossary.md#term-generator) 的定义。

在 3.5 版本加入.

_class _collections.abc.Sequence¶

_class _collections.abc.MutableSequence¶

_class _collections.abc.ByteString¶
~~~
    

~~~
只读的与可变的 [序列](../glossary.md#term-sequence) 的抽象基类。

实现笔记：一些混入（Maxin）方法比如 `__iter__()`, `__reversed__()` 和 `index()` 会重复调用底层的 `__getitem__()` 方法。因此，如果实现的 `__getitem__()` 是常数级访问速度，那么相应的混入方法会有一个线性的表现；然而，如果底层方法是线性实现（例如链表），那么混入方法将会是平方级的表现，这也许就需要被重构了。

在 3.5 版本发生变更: index() 方法支持 _stop_ 和 _start_ 参数。

从 3.12 版起不建议使用，将在 3.14 版中移除: `ByteString` ABC 已被弃用。 当用于类型标注时，建议改为并集形式，如 `bytes | bytearray`，或 `collections.abc.Buffer`。 当用作 ABC 时，建议改为 `Sequence` 或 `collections.abc.Buffer`。

_class _collections.abc.Set¶

_class _collections.abc.MutableSet¶
~~~
    

~~~
只读的与可变的集合的抽象基类。

_class _collections.abc.Mapping¶

_class _collections.abc.MutableMapping¶
~~~
    

~~~
只读的与可变的 [映射](../glossary.md#term-mapping) 的抽象基类。

_class _collections.abc.MappingView¶

_class _collections.abc.ItemsView¶

_class _collections.abc.KeysView¶

_class _collections.abc.ValuesView¶
~~~
    

~~~
映射及其键和值的 [视图](../glossary.md#term-dictionary-view) 的抽象基类。

_class _collections.abc.Awaitable¶
~~~
    

~~~
为可等待对象 [awaitable](../glossary.md#term-awaitable) 提供的类，可以被用于 [`await`](../reference/expressions.md#await) 表达式中。习惯上必须实现 `__await__()` 方法。

[协程](../glossary.md#term-coroutine) 对象和 `Coroutine` ABC 的实例都是这个 ABC 的实例。

备注

在 CPython 中，基于生成器的协程（使用 [`types.coroutine()`](types.md#types.coroutine "types.coroutine") 装饰的生成器）都是 _可等待对象_ ，即使它们没有 `__await__()` 方法。 对它们使用 `isinstance(gencoro, Awaitable)` 将返回 `False`。 请使用 [`inspect.isawaitable()`](inspect.md#inspect.isawaitable "inspect.isawaitable") 来检测它们。

在 3.5 版本加入.

_class _collections.abc.Coroutine¶
~~~
    

~~~
用于协程兼容类的抽象基类。实现了如下定义在 [协程对象](../reference/datamodel.md#coroutine-objects): 里的方法： [`send()`](../reference/datamodel.md#coroutine.send "coroutine.send")，[`throw()`](../reference/datamodel.md#coroutine.throw "coroutine.throw") 和 [`close()`](../reference/datamodel.md#coroutine.close "coroutine.close")。通常的实现里还需要实现 `__await__()` 方法。所有的 `Coroutine` 实例都必须是 `Awaitable` 实例。参见 [coroutine](../glossary.md#term-coroutine) 的定义。

备注

在 CPython 中，基于生成器的协程（使用 [`types.coroutine()`](types.md#types.coroutine "types.coroutine") 装饰的生成器）都是 _可等待对象_ ，即使它们没有 `__await__()` 方法。 对它们使用 `isinstance(gencoro, Coroutine)` 将返回 `False`。 请使用 [`inspect.isawaitable()`](inspect.md#inspect.isawaitable "inspect.isawaitable") 来检测它们。

在 3.5 版本加入.

_class _collections.abc.AsyncIterable¶
~~~
    

~~~
提供了 `__aiter__` 方法的抽象基类。参见 [asynchronous iterable](../glossary.md#term-asynchronous-iterable) 的定义。

在 3.5 版本加入.

_class _collections.abc.AsyncIterator¶
~~~
    

~~~
提供了 `__aiter__` 和 `__anext__` 方法的抽象基类。参见 [asynchronous iterator](../glossary.md#term-asynchronous-iterator) 的定义。

在 3.5 版本加入.

_class _collections.abc.AsyncGenerator¶
~~~
    

~~~
为异步生成器类提供的抽象基类，这些类实现了定义在 [**PEP 525**](https://peps.python.org/pep-0525/) 和 [**PEP 492**](https://peps.python.org/pep-0492/) 里的协议。

在 3.6 版本加入.

_class _collections.abc.Buffer¶
~~~
    

~~~
针对提供 [`__buffer__()`](../reference/datamodel.md#object.__buffer__ "object.__buffer__") 方法的类的 ABC，实现了 [缓冲区协议](../c-api/buffer.md#bufferobjects)。 参见 [**PEP 688**](https://peps.python.org/pep-0688/)。

在 3.12 版本加入.

## 例子和配方¶

ABC 允许我们询问类或实例是否提供特定的功能，例如:
~~~
    
    
~~~
size = None
if isinstance(myvar, collections.abc.Sized):
    size = len(myvar)
~~~

有些抽象基类也可以用作混入类（mixin），这可以更容易地开发支持容器 API 的类。例如，要写一个支持完整 `Set` API 的类，只需要提供下面这三个方法： `__contains__()`, `__iter__()` 和 `__len__()`。抽象基类会补充上其余的方法，比如 `__and__()` 和 `isdisjoint()`:

    
    
~~~
class ListBasedSet(collections.abc.Set):
    ''' Alternate set implementation favoring space over speed
        and not requiring the set elements to be hashable. '''
    def __init__(self, iterable):
        self.elements = lst = []
        for value in iterable:
            if value not in lst:
                lst.append(value)

    def __iter__(self):
        return iter(self.elements)

    def __contains__(self, value):
        return value in self.elements

    def __len__(self):
        return len(self.elements)

s1 = ListBasedSet('abcdef')
s2 = ListBasedSet('defghi')
overlap = s1 & s2            # The __and__() method is supported automatically
~~~

当把 `Set` 和 `MutableSet` 用作混入类时需注意：

  1. 由于某些集合操作会创建新集合，默认的混入方法需要一种从可迭代对象里创建新实例的方式。 假定类构造器具有 `ClassName(iterable)` 形式的签名。 这样它将执行一个名为 `_from_iterable()` 的内部类方法，该方法会调用 `cls(iterable)` 来产生一个新集合。 如果 `Set` 混入类在具有不同构造器签名的类中被使用，你将需要通过类方法或常规方法来重载 `_from_iterable()`，以便基于可迭代对象参数来构造新的实例。

  2. 重载比较符时时（想必是为了速度，因为其语义都是固定的），只需要重定义 `__le__()` 和 `__ge__()` 函数，然后其他的操作会自动跟进。

  3. `Set` 混入类提供了一个 `_hash()` 方法为集合计算哈希值；但是，没有定义 `__hash__()` 因为并非所有集合都是 [hashable](../glossary.md#term-hashable) 或不可变对象。 要使用混入类为集合添加哈希能力，可以同时继承 `Set()` 和 `Hashable()`，然后定义 `__hash__ = Set._hash`。

参见

  * [OrderedSet recipe](https://code.activestate.com/recipes/576694/) 是基于 `MutableSet` 构建的一个示例。

  * 对于抽象基类，参见 [`abc`](abc.md#module-abc "abc: Abstract base classes according to :pep:`3119`.") 模块和 [**PEP 3119**](https://peps.python.org/pep-3119/)。

