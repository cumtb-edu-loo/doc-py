# `abc` \--- 抽象基类¶

**源代码：** [Lib/abc.py](https://github.com/python/cpython/tree/3.12/Lib/abc.py)

* * *

该模块提供了在 Python 中定义 [抽象基类](../glossary.md#term-abstract-base-class) (ABC) 的组件，在 [**PEP 3119**](https://peps.python.org/pep-3119/) 中已有概述。查看 PEP 文档了解为什么需要在 Python 中增加这个模块。（也可查看 [**PEP 3141**](https://peps.python.org/pep-3141/) 以及 [`numbers`](numbers.md#module-numbers "numbers: Numeric abstract base classes \(Complex, Real, Integral, etc.\).") 模块了解基于 ABC 的数字类型继承关系。）

[`collections`](collections.md#module-collections "collections: Container datatypes") 模块中有一些派生自 ABC 的实体类；当然，这些类还可以进一步被派生。 此外，[`collections.abc`](collections.abc.md#module-collections.abc "collections.abc: Abstract base classes for containers") 子模块中有一些可被用于测试一个类或实例是否提供了特定接口的 ABC，例如，它是否为 [hashable](../glossary.md#term-hashable) 或者是否为映射等。

该模块提供了一个元类 `ABCMeta`，可以用来定义抽象类，另外还提供一个工具类 `ABC`，可以用它以继承的方式定义抽象基类。

_class _abc.ABC¶

    

~~~
一个使用 `ABCMeta` 作为元类的工具类。抽象基类可以通过从 `ABC` 派生来简单地创建，这就避免了在某些情况下会令人混淆的元类用法，例如：
~~~
    
    
~~~
from abc import ABC

class MyABC(ABC):
    pass
~~~

注意 `ABC` 的类型仍然是 `ABCMeta`，因此继承 `ABC` 仍然需要关注元类使用中的注意事项，比如可能会导致元类冲突的多重继承。当然你也可以直接使用 `ABCMeta` 作为元类来定义抽象基类，例如：

    
    
~~~
from abc import ABCMeta

class MyABC(metaclass=ABCMeta):
    pass
~~~

在 3.4 版本加入.

_class _abc.ABCMeta¶

    

~~~
用于定义抽象基类（ABC）的元类。

使用该元类以创建抽象基类。抽象基类可以像 mix-in 类一样直接被子类继承。你也可以将不相关的具体类（包括内建类）和抽象基类注册为“抽象子类” —— 这些类以及它们的子类会被内建函数 [`issubclass()`](functions.md#issubclass "issubclass") 识别为对应的抽象基类的子类，但是该抽象基类不会出现在其 MRO（Method Resolution Order，方法解析顺序）中，抽象基类中实现的方法也不可调用（即使通过 [`super()`](functions.md#super "super") 调用也不行）。[1]

使用 `ABCMeta` 作为元类创建的类含有如下方法：

register( _subclass_ )¶
~~~
    

~~~
将“子类”注册为该抽象基类的“抽象子类”，例如：
~~~
    
    
~~~
from abc import ABC

class MyABC(ABC):
    pass

MyABC.register(tuple)

assert issubclass(tuple, MyABC)
assert isinstance((), MyABC)
~~~

在 3.3 版本发生变更: 返回注册的子类，使其能够作为类装饰器。

在 3.4 版本发生变更: 你可以使用 `get_cache_token()` 函数来检测对 `register()` 的调用。

你也可以在虚基类中重载这个方法。

__subclasshook__( _subclass_ )¶

    

~~~
（必须定义为类方法。）

检查 _subclass_ 是否是该抽象基类的子类。也就是说对于那些你希望定义为该抽象基类的子类的类，你不用对每个类都调用 `register()` 方法了，而是可以直接自定义 `issubclass` 的行为。（这个类方法是在抽象基类的 `__subclasscheck__()` 方法中调用的。）

该方法必须返回 `True`, `False` 或是 `NotImplemented`。如果返回 `True`， _subclass_ 就会被认为是这个抽象基类的子类。如果返回 `False`，无论正常情况是否应该认为是其子类，统一视为不是。如果返回 `NotImplemented`，子类检查会按照正常机制继续执行。

为了对这些概念做一演示，请看以下定义 ABC 的示例：
~~~
    
    
~~~
class Foo:
    def __getitem__(self, index):
        ...
    def __len__(self):
        ...
    def get_iterator(self):
        return iter(self)

class MyIterable(ABC):

    @abstractmethod
    def __iter__(self):
        while False:
            yield None

    def get_iterator(self):
        return self.__iter__()

    @classmethod
    def __subclasshook__(cls, C):
        if cls is MyIterable:
            if any("__iter__" in B.__dict__ for B in C.__mro__):
                return True
        return NotImplemented

MyIterable.register(Foo)
~~~

ABC `MyIterable` 定义了标准的迭代方法 [`__iter__()`](stdtypes.md#iterator.__iter__ "iterator.__iter__") 作为一个抽象方法。这里给出的实现仍可在子类中被调用。`get_iterator()` 方法也是 `MyIterable` 抽象基类的一部分，但它并非必须被非抽象派生类所重载。

这里定义的 `__subclasshook__()` 类方法指明了任何在其 [`__dict__`](stdtypes.md#object.__dict__ "object.__dict__") (或在其通过 [`__mro__`](stdtypes.md#class.__mro__ "class.__mro__") 列表访问的基类) 中具有 [`__iter__()`](stdtypes.md#iterator.__iter__ "iterator.__iter__") 方法的类也都会被视为 `MyIterable`。

最后，末尾行使得 `Foo` 成为 `MyIterable` 的一个虚子类，即便它并没有定义 [`__iter__()`](stdtypes.md#iterator.__iter__ "iterator.__iter__") 方法（它使用了以 `__len__()` 和 `__getitem__()` 术语定义的旧式可迭代对象协议）。注意这将不会使 `get_iterator` 成为对 `Foo` 来说可用的方法，所以也另外提供了一个它。

此外，`abc` 模块还提供了这些装饰器：

@abc.abstractmethod¶

    

~~~
用于声明抽象方法的装饰器。

使用此装饰器要求类的元类是 `ABCMeta` 或是从该类派生。一个具有派生自 `ABCMeta` 的元类的类不可以被实例化，除非它全部的抽象方法和特征属性均已被重载。抽象方法可通过任何普通的“super”调用机制来调用。 `abstractmethod()` 可被用于声明特性属性和描述器的抽象方法。

动态地添加抽象方法到一个类，或尝试在方法或类被创建后修改其抽象状态等操作仅在使用 `update_abstractmethods()` 函数时受到支持。 `abstractmethod()` 只会影响使用常规继承所派生的子类；通过 ABC 的 `register()` 方法注册的“虚子类”不会受到影响。

当 `abstractmethod()` 与其他方法描述符配合应用时，它应当被应用为最内层的装饰器，如以下用法示例所示:
~~~
    
    
~~~
class C(ABC):
    @abstractmethod
    def my_abstract_method(self, arg1):
        ...
    @classmethod
    @abstractmethod
    def my_abstract_classmethod(cls, arg2):
        ...
    @staticmethod
    @abstractmethod
    def my_abstract_staticmethod(arg3):
        ...

    @property
    @abstractmethod
    def my_abstract_property(self):
        ...
    @my_abstract_property.setter
    @abstractmethod
    def my_abstract_property(self, val):
        ...

    @abstractmethod
    def _get_x(self):
        ...
    @abstractmethod
    def _set_x(self, val):
        ...
    x = property(_get_x, _set_x)
~~~

为了正确地与抽象基类机制互操作，描述器必须使用 `__isabstractmethod__` 将自身标为抽象的。通常，如果组成描述器的任一方法是抽象的，那么此属性就应为 `True`。例如，Python 内置的 [`property`](functions.md#property "property") 所做的就等价于：

    
    
~~~
class Descriptor:
    ...
    @property
    def __isabstractmethod__(self):
        return any(getattr(f, '__isabstractmethod__', False) for
                   f in (self._fget, self._fset, self._fdel))
~~~

备注

不同于 Java 抽象方法，这些抽象方法可能具有一个实现。 这个实现可在重载它的类上通过 [`super()`](functions.md#super "super") 机制来调用。 这在使用协作多重继承的框架中可以被用作 super 调用的一个终点。

`abc` 模块还支持下列旧式装饰器:

@abc.abstractclassmethod¶

    

~~~
在 3.2 版本加入.

自 3.3 版本弃用: 现在可以让 [`classmethod`](functions.md#classmethod "classmethod") 配合 `abstractmethod()` 使用，使得此装饰器变得冗余。

内置 [`classmethod()`](functions.md#classmethod "classmethod") 的子类，指明一个抽象类方法。 在其他方面它都类似于 `abstractmethod()`。

这个特例已被弃用，因为现在当 [`classmethod()`](functions.md#classmethod "classmethod") 装饰器应用于抽象方法时它会被正确地标识为抽象的:
~~~
    
    
~~~
class C(ABC):
    @classmethod
    @abstractmethod
    def my_abstract_classmethod(cls, arg):
        ...
~~~

@abc.abstractstaticmethod¶

    

~~~
在 3.2 版本加入.

自 3.3 版本弃用: 现在可以让 [`staticmethod`](functions.md#staticmethod "staticmethod") 配合 `abstractmethod()` 使用，使得此装饰器变得冗余。

内置 [`staticmethod()`](functions.md#staticmethod "staticmethod") 的子类，指明一个抽象静态方法。 在其他方面它都类似于 `abstractmethod()`。

这个特例已被弃用，因为现在当 [`staticmethod()`](functions.md#staticmethod "staticmethod") 装饰器应用于抽象方法时它会被正确地标识为抽象的:
~~~
    
    
~~~
class C(ABC):
    @staticmethod
    @abstractmethod
    def my_abstract_staticmethod(arg):
        ...
~~~

@abc.abstractproperty¶

    

~~~
自 3.3 版本弃用: 现在可以让 [`property`](functions.md#property "property"), `property.getter()`, `property.setter()` 和 `property.deleter()` 配合 `abstractmethod()` 使用，使得此装饰器变得冗余。

内置 [`property()`](functions.md#property "property") 的子类，指明一个抽象特性属性。

这个特例已被弃用，因为现在当 [`property()`](functions.md#property "property") 装饰器应用于抽象方法时它会被正确地标识为抽象的:
~~~
    
    
~~~
class C(ABC):
    @property
    @abstractmethod
    def my_abstract_property(self):
        ...
~~~

上面的例子定义了一个只读特征属性；你也可以通过适当地将一个或多个下层方法标记为抽象的来定义可读写的抽象特征属性:

    
    
~~~
class C(ABC):
    @property
    def x(self):
        ...

    @x.setter
    @abstractmethod
    def x(self, val):
        ...
~~~

如果只有某些组件是抽象的，则只需更新那些组件即可在子类中创建具体的特征属性:

    
    
~~~
class D(C):
    @C.x.setter
    def x(self, val):
        ...
~~~

`abc` 模块还提供了这些函数：

abc.get_cache_token()¶

    

~~~
返回当前抽象基类的缓存令牌

此令牌是一个不透明对象（支持相等性测试），用于为虚子类标识抽象基类缓存的当前版本。 此令牌会在任何 ABC 上每次调用 `ABCMeta.register()` 时发生更改。

在 3.4 版本加入.

abc.update_abstractmethods( _cls_ )¶
~~~
    

~~~
