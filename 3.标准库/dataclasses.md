# `dataclasses` \--- 数据类¶

**源码：** [Lib/dataclasses.py](https://github.com/python/cpython/tree/3.12/Lib/dataclasses.py)

* * *

这个模块提供了一个装饰器和一些函数，用于自动为用户自定义的类添加生成的 [special method](../glossary.md#term-special-method) 例如 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 和 [`__repr__()`](datamodel.md#object.__repr__ "object.__repr__")。 它的初始描述见 [**PEP 557**](https://peps.python.org/pep-0557/)。

在这些生成的方法中使用的成员变量是使用 [**PEP 526**](https://peps.python.org/pep-0526/) 类型标注来定义的。例如以下代码：

    
    
~~~
from dataclasses import dataclass

@dataclass
class InventoryItem:
    """Class for keeping track of an item in inventory."""
    name: str
    unit_price: float
    quantity_on_hand: int = 0

    def total_cost(self) -> float:
        return self.unit_price * self.quantity_on_hand
~~~

除其他内容以外，还将添加如下所示的 [`__init__()`](datamodel.md#object.__init__ "object.__init__"):

    
    
~~~python
def __init__(self, name: str, unit_price: float, quantity_on_hand: int = 0):
    self.name = name
    self.unit_price = unit_price
    self.quantity_on_hand = quantity_on_hand
~~~

请注意，此方法会自动添加到类中：而不是在如上所示的 `InventoryItem` 定义中被直接指定。

在 3.7 版本加入.

## 模块内容¶

@dataclasses.dataclass( _*_ , _init =True_, _repr =True_, _eq =True_, _order =False_, _unsafe_hash =False_, _frozen =False_, _match_args =True_, _kw_only =False_, _slots =False_, _weakref_slot =False_)¶

    

~~~
这个函数是 [decorator](../glossary.md#term-decorator) ，用于将生成的 [special method](../glossary.md#term-special-method) 添加到类中，如下所述。

`dataclass()` 装饰器会检查类以查找 `field` —— `field` 被定义为具有 [类型标注](../glossary.md#term-variable-annotation) 的类变量。除了下面描述的两个例外，在 `dataclass()` 中没有什么东西会去检查这些变量标注成了何种类型。

这些字段在所有生成的方法中的顺序，都是它们在类定义中出现的顺序。

`dataclass()` 装饰器将向类中添加如下的各种 dunder 方法。如果所添加的方法已存在于类中，则行为将取决于下面所列出的形参。该装饰器会返回调用它的类；不会创建新的类。

如果 `dataclass()` 仅用作没有参数的简单装饰器，它将使用它的函数签名中的默认值。也就是说，这三种 `dataclass()` 用法是等价的：
~~~
    
    
~~~
@dataclass
class C:
    ...

@dataclass()
class C:
    ...

@dataclass(init=True, repr=True, eq=True, order=False, unsafe_hash=False, frozen=False,
           match_args=True, kw_only=False, slots=False, weakref_slot=False)
class C:
    ...
~~~

`dataclass()` 的参数有：

  * `init`: 如为真值（默认），将生成一个 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 方法。

如果该类已经定义了 [`__init__()`](datamodel.md#object.__init__ "object.__init__")，则忽略此形参。

  * `repr`: 如果为真值（默认），将生成一个 [`__repr__()`](datamodel.md#object.__repr__ "object.__repr__") 方法。 生成的 repr 字符串将带有类名及每个字符的名称和 repr，并按它们在类中定义的顺序排列。 不包括被标记为从 repr 排除的字段。 例如: `InventoryItem(name='widget', unit_price=3.0, quantity_on_hand=10)`。

如果该类已经定义了 [`__repr__()`](datamodel.md#object.__repr__ "object.__repr__")，则忽略此形参。

  * `eq`: 如果为真值（默认），将生成 [`__eq__()`](datamodel.md#object.__eq__ "object.__eq__") 方法。 此方法将把类当作由其字段组成的元组那样按顺序进行比较。 要比较的两个实例必须是相同的类型。

如果该类已经定义了 [`__eq__()`](datamodel.md#object.__eq__ "object.__eq__")，则忽略此形参。

  * `order`: 如果为真值 (默认为 `False`)，将生成 [`__lt__()`](datamodel.md#object.__lt__ "object.__lt__"), [`__le__()`](datamodel.md#object.__le__ "object.__le__"), [`__gt__()`](datamodel.md#object.__gt__ "object.__gt__") 和 [`__ge__()`](datamodel.md#object.__ge__ "object.__ge__") 方法。 这些方法将把类当作由其字段组成的元组那样按顺序进行比较。 要比较的两个实例必须是相同的类型。 如果 `order` 为真值并且 `eq` 为假值，则会引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError")。

如果该类已经定义了 [`__lt__()`](datamodel.md#object.__lt__ "object.__lt__"), [`__le__()`](datamodel.md#object.__le__ "object.__le__"), [`__gt__()`](datamodel.md#object.__gt__ "object.__gt__") 或者 [`__ge__()`](datamodel.md#object.__ge__ "object.__ge__") 中的任意一个，则会引发 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError")。

  * `unsafe_hash`: 如果为 `False` (默认值)，则会根据 `eq` 和 `frozen` 的设置情况生成 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 方法。

[`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 将由内置的 [`hash()`](functions.md#hash "hash") 使用，并会在对象被添加到可哈希的多项集如字典和集合时被调用。 具有 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 意味着类的实例是不可变对象。 可变性是一个复杂的特性，它依赖于程序员的意图，[`__eq__()`](datamodel.md#object.__eq__ "object.__eq__") 的存在和行为方式，以及 `dataclass()` 装饰器中 `eq` 和 `frozen` 旗标的值。

在默认情况下，`dataclass()` 不会隐式地添加 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 方法，除非这样做是安全的。 它也不会添加或更改现有的显式定义的 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 方法。 设置类属性 `__hash__ = None` 对 Python 来说有特别的含义，如 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 文档所描述的。

如果 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 没有被显式地定义，或者如果它被设为 `None`，则 `dataclass()` _可能_ 会添加一个隐式的 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 方法。 虽然并不推荐，但你可以设置 `unsafe_hash=True` 来强制 `dataclass()` 创建一个 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 方法。 如果你的类在逻辑上不可变但仍然可被修改那么可能就是这种情况。 这是一个特殊应用场景并且应当被谨慎考虑。

以下是隐式创建 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 方法的规则。 请注意你不能在你的数据类中即定义显式的 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 方法又设置 `unsafe_hash=True`；这会导致 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError")。

如果 `eq` 和 `frozen` 都为真值，则 `dataclass()` 将默认为你生成一个 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 方法。 如果 `eq` 为真值且 `frozen` 为假值，则 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 将被设为 `None`，以标记其为不可哈希的（因为它是可变对象）。 如果 `eq` 为假值，则 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 将保持不变，这意味着将使用超类的 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 方法（如果超类是 [`object`](functions.md#object "object")，这意味着它将回退为基于 id 的哈希）。

  * `frozen`: 如果为真值 (默认为 `False`)，则对字段赋值将引发异常。 这模拟了只读的冻结实例。 如果在类中定义了 [`__setattr__()`](datamodel.md#object.__setattr__ "object.__setattr__") 或 [`__delattr__()`](datamodel.md#object.__delattr__ "object.__delattr__")，则将引发 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError")。 参见下文的讨论。

  * `match_args`: 如果为真值 (默认为 `True`)，则将根据传给已生成的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 方法的形参列表来创建 `__match_args__` 元组 (即使没有生成 [`__init__()`](datamodel.md#object.__init__ "object.__init__")，见上文)。 如果为假值，或者如果 `__match_args__` 已在类中定义，则不会生成 `__match_args__`。

> 在 3.10 版本加入.

  * `kw_only`: 如果为真值 (默认为 `False`)，则所有字段都将被标记为仅限关键字字段。 如果一个字段被标记为仅限关键字字段，则唯一的影响是由仅限关键字字段生成的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 的对应形参在 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 被调用时必须以关键字形式指定。 而 dataclass 的任何其它行为都不会受影响。 详情参见 [parameter](../glossary.md#term-parameter) 术语表条目。 另请参见 `KW_ONLY` 一节。

> 在 3.10 版本加入.

  * `slots`: 如果为真值 (默认为 `False`)，则将生成 [`__slots__`](datamodel.md#object.__slots__ "object.__slots__") 属性并返回一个新类而非原来的类。 如果 [`__slots__`](datamodel.md#object.__slots__ "object.__slots__") 已在类中定义，则会引发 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError")。

> 在 3.10 版本加入.
>
> 在 3.11 版本发生变更: 如果某个字段名称已经包括在基类的 `__slots__` 中，它将不会被包括在所生成的 `__slots__` 中以防止 [覆盖它们](datamodel.md#datamodel-note-slots)。 因此，请不要使用 `__slots__` 来获取数据类的字段名称。 而应改用 `fields()`。 为了能够确定所继承的槽位，基类 `__slots__` 可以为任意可迭代对象，但是 _不可以为_ 迭代器。

  * `weakref_slot`：如果为真值（默认为 `False`），则添加一个名为 “__weakref__” 的槽位，这是使得一个实例可以被弱引用所必需的。指定 `weakref_slot=True` 而不同时指定 `slots=True` 将会导致错误。

> 在 3.11 版本加入.

可以用普通的 Python 语法为各个 `field` 指定默认值：

    
    
~~~
@dataclass
class C:
    a: int       # 'a' has no default value
    b: int = 0   # assign a default value for 'b'
~~~

在这个例子中，`a` 和 `b` 都将被包括在所添加的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 方法中，该方法将被定义为:

    
    
~~~python
def __init__(self, a: int, b: int = 0):
~~~

如果在具有默认值的字段之后存在没有默认值的字段，将会引发 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError")。无论此情况是发生在单个类中还是作为类继承的结果，都是如此。

dataclasses.field( _*_ , _default =MISSING_, _default_factory =MISSING_, _init =True_, _repr =True_, _hash =None_, _compare =True_, _metadata =None_, _kw_only =MISSING_)¶

    

~~~
大多数时候，对于简单常见的用途，前述的功能已经足够了。而有些功能需要字段提供额外的信息来启用。为了满足这种对附加信息的需求，你可以通过调用提供的 `field()` 函数来替换字段默认值。例如：
~~~
    
    
~~~
@dataclass
class C:
    mylist: list[int] = field(default_factory=list)

c = C()
c.mylist += [1, 2, 3]
~~~

如上所示，`MISSING` 值是一个哨兵对象，用于检测一些形参是否由用户提供。使用它是因为 `None` 对于一些形参来说是有效的用户值。任何代码都不应该直接使用 `MISSING` 值。

`field()` 的形参有：

  * `default`：如果提供，这将是该字段的默认值。设计这个形参是因为 `field()` 调用将会占据原来用来提供默认值的位置。

  * `default_factory`：如果提供，它必须是一个需要零个参数的可调用对象，当该字段需要一个默认值时，它将被调用。这能解决当默认值是可变对象时会带来的问题，如下所述。同时指定 `default` 和 `default_factory` 将产生错误。

  * `init`: 如果为真值（默认），则该字段将作为一个形参被包括在所生成的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 方法中。

  * `repr`: 如果为真值（默认），则该字段将被包括在所生成的 [`__repr__()`](datamodel.md#object.__repr__ "object.__repr__") 方法返回的字符串中。

  * `hash`: 这可以是一个布尔值或为 `None`。 如果为真值，则此字段将被包括在所生成的 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 方法中。 如果为 `None` (默认)，则将使用 `compare` 的值：这通常是预期的行为。 一个字段如果被用于比较那么就应当在哈希时考虑到它。 不建议将该值设为 `None` 以外的任何其他对象。

设置 `hash=False` 但 `compare=True` 的一个合理情况是，一个计算哈希值的代价很高的字段是检验等价性需要的，且还有其他字段可以用于计算类型的哈希值。可以从哈希值中排除该字段，但仍令它用于比较。

  * `compare`: 如果为真值（默认），则该字段将被包括在所生成的相等性和大小比较方法中 ([`__eq__()`](datamodel.md#object.__eq__ "object.__eq__"), [`__gt__()`](datamodel.md#object.__gt__ "object.__gt__") 等等)。

  * `metadata`：可以是映射或 None。None 被视为一个空的字典。这个值将被包装在 [`MappingProxyType()`](types.md#types.MappingProxyType "types.MappingProxyType") 中，使其只读，并暴露在 `Field` 对象上。数据类不使用它——它是作为第三方扩展机制提供的。多个第三方可以各自拥有自己的键，以用作元数据中的命名空间。

  * `kw_only`: 如果为真值，则该字段将被标记为仅限关键字字段。 这将在计算所生成的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 方法的形参时被使用。

> 在 3.10 版本加入.

如果通过调用 `field()` 指定字段的默认值，则该字段对应的类属性的值将最终被替换为指定的 `default` 值。如果没有提供 `default`，那么将删除该字段对应的类属性。目的是在 `dataclass()` 装饰器运行之后，类属性将包含字段的默认值，和直接指定了默认值一样。例如，在运行如下代码之后：

    
    
~~~
@dataclass
class C:
    x: int
    y: int = field(repr=False)
    z: int = field(repr=False, default=10)
    t: int = 20
~~~

类属性 `C.z` 将是 `10`，类属性 `C.t` 将是 `20`，类属性 `C.x` 和 `C.y` 将不设置。

_class _dataclasses.Field¶

    

~~~
`Field` 对象描述每个已定义的字段。这些对象在内部被创建，并由 `fields()` 模块级方法返回（见下）。用户永远不应该直接实例化 `Field` 对象。它的下列属性的含义是由文档规定的：

>   * `name`：字段的名称。
>
>   * `type`：字段的类型。
>
>   * `default`, `default_factory`, `init`, `repr`, `hash`, `compare`, `metadata` 和 `kw_only` 具有与 `field()` 函数中对应参数相同的含义和值。
>
>

可能存在其他属性，但它们是私有的。用户不应检查或依赖于这些属性。

dataclasses.fields( _class_or_instance_ )¶
~~~
    

~~~
返回一个能描述此数据类所包含的字段的元组，元组的每一项都是 `Field` 对象。接受数据类或数据类的实例。如果没有传递一个数据类或实例将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。不返回 `ClassVar` 或 `InitVar` 等伪字段。

dataclasses.asdict( _obj_ , _*_ , _dict_factory =dict_)¶
~~~
    

~~~
将数据类 `obj` 转换为一个字典（使用工厂函数 `dict_factory`）。每个数据类被转换为以 `name: value` 键值对来储存其字段的字典。数据类、字典、列表和元组的内容会被递归地访问。其它对象用 [`copy.deepcopy()`](copy.md#copy.deepcopy "copy.deepcopy") 来复制。

在嵌套的数据类上使用 `asdict()` 的例子：
~~~
    
    
~~~
@dataclass
class Point:
     x: int
     y: int

@dataclass
class C:
     mylist: list[Point]

p = Point(10, 20)
assert asdict(p) == {'x': 10, 'y': 20}

c = C([Point(0, 0), Point(10, 4)])
assert asdict(c) == {'mylist': [{'x': 0, 'y': 0}, {'x': 10, 'y': 4}]}
~~~

要创建一个浅拷贝，可以使用以下的变通方法：

    
    
~~~
dict((field.name, getattr(obj, field.name)) for field in fields(obj))
~~~

如果 `obj` 不是一个数据类实例，`asdict()` 引发 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError")。

dataclasses.astuple( _obj_ , _*_ , _tuple_factory =tuple_)¶

    

~~~
将数据类 `obj` 转换为一个元组（使用工厂函数 `tuple_factory`）。每个数据类被转换为其字段的值的元组。数据类、字典、列表和元组的内容会被递归地访问。其它对象用 [`copy.deepcopy()`](copy.md#copy.deepcopy "copy.deepcopy") 来复制。

继续前一个例子：
~~~
    
    
~~~
assert astuple(p) == (10, 20)
assert astuple(c) == ([(0, 0), (10, 4)],)
~~~

要创建一个浅拷贝，可以使用以下的变通方法：

    
    
~~~
tuple(getattr(obj, field.name) for field in dataclasses.fields(obj))
~~~

如果 `obj` 不是一个数据类实例，`astuple()` 引发 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError")。

dataclasses.make_dataclass( _cls_name_ , _fields_ , _*_ , _bases =()_, _namespace =None_, _init =True_, _repr =True_, _eq =True_, _order =False_, _unsafe_hash =False_, _frozen =False_, _match_args =True_, _kw_only =False_, _slots =False_, _weakref_slot =False_, _module =None_)¶

    

~~~
创建一个新数据类，名为 `cls_name`，包含的字段为 `fields`，基类为 `bases`，并且用 `namespace` 指定的命名空间初始化。`fields` 是一个可迭代对象，其每一个元素的形式都可以取 `name`，`(name, type)` 或 `(name, type, Field)` 中的一种。若只提供 `name`，`type` 则为 `typing.Any`。后面这些参数：`init`、`repr`、`eq`、`order`、`unsafe_hash`、`frozen`、`match_args`、`kw_only`、`slots`、`weakref_slot` 全都与 `dataclass()` 的同名参数作用相同。

如果定义了 `module`，则该数据类的 `__module__` 属性将被设为它所指定的值。 在默认情况下，它将被设为调用方的模块名。

此函数不是必需的，因为任何用于创建带有 `__annotations__` 的新类的 Python 机制都可以进一步用 `dataclass()` 函数将创建的类转换为数据类。提供此函数是为了方便。例如：
~~~
    
    
~~~
C = make_dataclass('C',
                   [('x', int),
                     'y',
                    ('z', int, field(default=5))],
                   namespace={'add_one': lambda self: self.x + 1})
~~~

等价于：

    
    
~~~
@dataclass
class C:
    x: int
    y: 'typing.Any'
    z: int = 5

    def add_one(self):
        return self.x + 1
~~~

dataclasses.replace( _obj_ , _/_ , _** changes_)¶

    

~~~
创建一个与 `obj` 类型相同的新对象，将字段替换为 `changes` 里的值。如果 `obj` 不是数据类，引发 [`TypeError`](exceptions.md#TypeError "TypeError") 。如果 `changes` 里的值没有指定要替换的字段名，引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

新返回的对象是通过调用数据类的 [`__init__()`](../reference/datamodel.md#object.__init__ "object.__init__") 方法来创建的。 这确保了如果存在 `__post_init__()`，则它也会被调用。

如果存在任何没有默认值的仅初始化变量，那么必须在调用 `replace()` 时给出它们的值，以便它们可以被传递给 [`__init__()`](../reference/datamodel.md#object.__init__ "object.__init__") 和 `__post_init__()`。

`changes` 试图为任何定义为 `init=False` 的字段赋值，会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

提前提醒 `init=False` 字段在 `replace()` 被调用时的行为。如果它们被初始化的话，它们不是从源对象复制的，而是在 `__post_init__()` 中初始化。除非保持审慎，否则 `init=False` 字段大概很少能被正确地使用。如果使用它们，那么使用另外的类构造器，或自定义 `replace()` 方法（或类似名称的方法）来复制实例，可能是明智的选择。

Dataclass instances are also supported by generic function [`copy.replace()`](copy.md#copy.replace "copy.replace").

dataclasses.is_dataclass( _obj_ )¶
~~~
    

~~~
如果其形参为数据类，或其实例，返回 `True`，否则返回 `False`。

如果你需要知道一个类是否是一个数据类的实例（而不是一个数据类本身），那么再添加一个 `not isinstance(obj, type)` 检查：
~~~
    
    
~~~python
def is_dataclass_instance(obj):
    return is_dataclass(obj) and not isinstance(obj, type)
~~~

dataclasses.MISSING¶

    

~~~
一个指明“没有提供 default 或 default_factory”的监视值。

dataclasses.KW_ONLY¶
~~~
    

~~~
一个用作类型标注的监视值。 任何在伪字段之后的类型为 `KW_ONLY` 的字段会被标记为仅限关键字字段。 请注意在其他情况下 `KW_ONLY` 类型的伪字段会被完全忽略。 这包括此类字段的名称。 根据惯例，名称 `_` 会被用作 `KW_ONLY` 字段。 仅限关键字字段指明当类被实例化时 [`__init__()`](../reference/datamodel.md#object.__init__ "object.__init__") 形参必须以关键字形式来指定。

在这个例子中，字段 `y` 和 `z` 将被标记为仅限关键字字段:
~~~
    
    
~~~
@dataclass
class Point:
    x: float
    _: KW_ONLY
    y: float
    z: float

p = Point(0, y=1.5, z=2.0)
~~~

在单个数据类中，指定一个以上 `KW_ONLY` 类型的字段将导致错误。

在 3.10 版本加入.

_exception _dataclasses.FrozenInstanceError¶

    

~~~
在定义时设置了 `frozen=True` 的类上调用隐式定义的 [`__setattr__()`](../reference/datamodel.md#object.__setattr__ "object.__setattr__") 或 [`__delattr__()`](../reference/datamodel.md#object.__delattr__ "object.__delattr__") 时引发。 这是 [`AttributeError`](exceptions.md#AttributeError "AttributeError") 的一个子类。

## 初始化后处理¶

dataclasses.__post_init__()¶
~~~
    

~~~
当在类上定义时，它将被所生成的 [`__init__()`](../reference/datamodel.md#object.__init__ "object.__init__") 调用，通常是以 `self.__post_init__()` 的形式。 但是，如果定义了任何 `InitVar` 字段，它们也将按照它们在类中定义的顺序被传递给 `__post_init__()`。 如果没有生成 [`__init__()`](../reference/datamodel.md#object.__init__ "object.__init__") 方法，那么 `__post_init__()` 将不会被自动调用。

在其他用途中，这允许初始化依赖于一个或多个其他字段的字段值。例如:
~~~
    
    
~~~
@dataclass
class C:
    a: float
    b: float
    c: float = field(init=False)

    def __post_init__(self):
        self.c = self.a + self.b
~~~

由 `dataclass()` 生成的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 方法不会调用基类的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 方法。 如果基类有必须被调用的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 方法，通常是在 `__post_init__()` 方法中调用此方法:

    
    
~~~
@dataclass
class Rectangle:
    height: float
    width: float

@dataclass
class Square(Rectangle):
    side: float

    def __post_init__(self):
        super().__init__(self.side, self.side)
~~~

但是，请注意一般来说 dataclass 生成的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 方法不需要被调用，因为派生的 dataclass 将负责初始化任何本身为 dataclass 的基类的所有字段。

有关将参数传递给 `__post_init__()` 的方法，请参阅下面有关仅初始化变量的段落。另请参阅关于 `replace()` 处理 `init=False` 字段的警告。

## 类变量¶

在 `dataclass()` 会实际检查字段类型的少数几个地方之一是确定字符是否为如 [**PEP 526**](https://peps.python.org/pep-0526/) 所定义的类变量。 它通过检查字段的类型是否为 `typing.ClassVar` 来实现这一点。 如果一个字段是 `ClassVar`，它将被排除在考虑范围之外并被数据类机制所忽略。 这样的 `ClassVar` 伪字段将不会被模块层级的 `fields()` 函数返回。

## 仅初始化变量¶

另一个 `dataclass()` 会检查类型标注的地方是为了确定一个字段是否为仅限初始化的变量。 它通过查看字段的类型是否为 `dataclasses.InitVar` 类型来实现这一点。 如果一个字段是 `InitVar`，它会被当作是名为仅限初始化字段的伪字段。 因为它不是一个真正的字段，所以它不会被模块层级的 `fields()` 函数返回。 仅限初始化字段会作为形参被添加到所生成的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 方法中，并被传递给可选的 `__post_init__()` 方法。 在其他情况下它们将不会被数据类所使用。

例如，假设在创建类时没有为某个字段提供值，初始化时将从数据库中取值:

    
    
~~~
@dataclass
class C:
    i: int
    j: int | None = None
    database: InitVar[DatabaseType | None] = None

    def __post_init__(self, database):
        if self.j is None and database is not None:
            self.j = database.lookup('j')

c = C(10, database=my_database)
~~~

在这种情况下， `fields()` 将返回 `i` 和 `j` 的 `Field` 对象，但不包括 `database` 。

## 冻结的实例¶

不可能创建真正不可变的 Python 对象。 但是，通过将 `frozen=True` 传递给 `dataclass()` 装饰器，你可以模拟出不可变性。 在这种情况下，dataclass 将向类添加 [`__setattr__()`](datamodel.md#object.__setattr__ "object.__setattr__") 和 [`__delattr__()`](datamodel.md#object.__delattr__ "object.__delattr__") 方法。 当被发起调用时这些方法将会引发 `FrozenInstanceError`。

在使用 `frozen=True` 时会有微小的性能损失: [`__init__()`](datamodel.md#object.__init__ "object.__init__") 不能使用简单赋值来初始化字段，而必须使用 `object.__setattr__()`。

## 继承¶

当数据类由 `dataclass()` 装饰器创建时，它会按反向 MRO 顺序（即，从 [`object`](functions.md#object "object") 开始）查看它的所有基类，并且将找到的每个数据类的字段添加到一个有序映射中。添加完所有基类字段后，它会将自己的字段添加到这个有序映射中。所有生成的方法都将使用这个有序映射。字段会遵守它们被插入的顺序，因此派生类会重写基类。一个例子：

    
    
~~~
@dataclass
class Base:
    x: Any = 15.0
    y: int = 0

@dataclass
class C(Base):
    z: int = 10
    x: int = 15
~~~

最后的字段列表依次是 `x` 、 `y` 、 `z` 。 `x` 的最终类型是 `int` ，如类 `C` 中所指定的那样。

为 `C` 生成的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 方法看起来像是这样:

    
    
~~~python
def __init__(self, x: int = 15, y: int = 0, z: int = 10):
~~~

## [`__init__()`](datamodel.md#object.__init__ "object.__init__") 中仅限关键字形参的重新排序¶

在计算出 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 所需要的形参之后，任何仅限关键字形参会被移至所有常规（非仅限关键字）形参的后面。 这是 Python 中实现仅限关键字形参所要求的：它们必须位于非仅限关键字形参之后。

在这个例子中，`Base.y`, `Base.w`, and `D.t` 是仅限关键字字段，而 `Base.x` 和 `D.z` 是常规字段:

    
    
~~~
@dataclass
class Base:
    x: Any = 15.0
    _: KW_ONLY
    y: int = 0
    w: int = 1

@dataclass
class D(Base):
    z: int = 10
    t: int = field(kw_only=True, default=0)
~~~

为 `D` 生成的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 方法看起来像是这样:

    
    
~~~python
def __init__(self, x: Any = 15.0, z: int = 10, *, y: int = 0, w: int = 1, t: int = 0):
~~~

请注意形参原来在字段列表中出现的位置已被重新排序：前面是来自常规字段的形参而后面是来自仅限关键字字段的形参。

仅限关键字形参的相对顺序会在重新排序的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 形参列表中继续保持。

## 默认工厂函数¶

如果一个 `field()` 指定了一个 `default_factory` ，当需要该字段的默认值时，将使用零参数调用它。例如，要创建列表的新实例，请使用:

    
    
~~~
mylist: list = field(default_factory=list)
~~~

如果一个字段被排除在 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 之外 (使用 `init=False`) 但该字段同样也指定了 `default_factory`，则默认的工厂函数将始终从生成的 [`__init__()`](datamodel.md#object.__init__ "object.__init__") 函数中被调用。 发生这种情况是因为没有其它方法能为字段提供初始值。

## 可变的默认值¶

Python 在类属性中存储默认成员变量值。思考这个例子，不使用数据类:

    
    
~~~
class C:
    x = []
    def add(self, element):
        self.x.append(element)

o1 = C()
o2 = C()
o1.add(1)
o2.add(2)
assert o1.x == [1, 2]
assert o1.x is o2.x
~~~

请注意，类 `C` 的两个实例共享相同的类变量 `x` ，如预期的那样。

使用数据类， _如果_ 此代码有效：

    
    
~~~
@dataclass
class D:
    x: list = []      # This code raises ValueError
    def add(self, element):
        self.x += element
~~~

它生成的代码类似于:

    
    
~~~
class D:
    x = []
    def __init__(self, x=x):
        self.x = x
    def add(self, element):
        self.x += element

assert D().x is D().x
~~~

这具有与使用 `C` 类的原始示例相同的问题。 也就是说，当创建类实例的时候如果 `D` 类的两个实例没有为 `x` 指定值则将共享同一个 `x` 的副本。 因为数据类只是使用普通的 Python 类创建方式所心它们也会共享此行为。 数据类没有任何通用方式来检测这种情况。 相反地，`dataclass()` 装饰器在检测到不可哈希的默认形参时将会引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError")。 这一行为假定如果一个值是不可哈希的，则它就是可变对象。 这是一个部分解决方案，但它确实能防止许多常见错误。

使用默认工厂函数是一种创建可变类型新实例的方法，并将其作为字段的默认值:

    
    
~~~
@dataclass
class D:
    x: list = field(default_factory=list)

assert D().x is not D().x
~~~

在 3.11 版本发生变更: 现在不再是寻找并阻止使用类型为 `list`, `dict` 或 `set` 的对象，而是不允许使用不可哈希的对象作为默认值。 就是将不可哈希性当作是不可变性的等价物。

## 字段标注描述器类型¶

当字段被 [描述器对象](datamodel.md#descriptors) 赋值为默认值时会遵循以下行为:

  * 字段的值被传递到数据类的 `__init__` 方法时，会传递给描述器的 `__set__` 方法，而不会覆盖掉描述器对象。

  * 相似的是，当我们获取或设置字段的值时，不会覆盖或返回描述器对象，而是会调用描述器的 `__get__` 或 `__set__` 方法后返回。

  * 检测字段是否存在默认值时，`dataclasses``会通过类方法的形式去调用描述器的``__get__``方法(即为``descriptor.__get__(obj=None, type=cls)`)。如果描述器有返回值，则返回值为字段的默认值，若调用描述器时抛出 [`AttributeError`](3.标准库/exceptions.md#AttributeError "AttributeError") 错误，则该字段无默认值。

    
    
~~~
class IntConversionDescriptor:
    def __init__(self, *, default):
        self._default = default

    def __set_name__(self, owner, name):
        self._name = "_" + name

    def __get__(self, obj, type):
        if obj is None:
            return self._default

        return getattr(obj, self._name, self._default)

    def __set__(self, obj, value):
        setattr(obj, self._name, int(value))

@dataclass
class InventoryItem:
    quantity_on_hand: IntConversionDescriptor = IntConversionDescriptor(default=100)

i = InventoryItem()
print(i.quantity_on_hand)   # 100
i.quantity_on_hand = 2.5    # calls __set__ with 2.5
print(i.quantity_on_hand)   # 2
~~~

如果一个字段被描述器标注类型，但默认值并不是描述器对象，那么该字段就只能像平常的字段一样工作。

