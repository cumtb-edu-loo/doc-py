# `enum` \--- 对枚举的支持¶

在 3.4 版本加入.

**源代码：** [Lib/enum.py](https://github.com/python/cpython/tree/3.12/Lib/enum.py)

Important

此页面仅包含 API 参考信息。教程信息和更多高级用法的讨论，请参阅

  * [基础教程](6.常用指引/enum.md#enum-basic-tutorial)

  * [进阶教程](6.常用指引/enum.md#enum-advanced-tutorial)

  * [枚举指南](6.常用指引/enum.md#enum-cookbook)

* * *

一个枚举:

  * 是绑定到唯一值的符号名称（成员）集合

  * 可以被执行迭代以按定义顺序返回其规范的（即非别名的）成员

  * 使用 _调用_ 语法按值返回成员

  * 使用 _索引_ 语法按名称返回成员

枚举是通过使用 [`class`](compound_stmts.md#class) 语法或是通过使用函数调用语法来创建的:

    
    
~~~shell
>>> from enum import Enum

>>> # class syntax
>>> class Color(Enum):
...     RED = 1
...     GREEN = 2
...     BLUE = 3

>>> # functional syntax
>>> Color = Enum('Color', ['RED', 'GREEN', 'BLUE'])
~~~

虽然我们可以使用 [`class`](compound_stmts.md#class) 语法来创建枚举，但枚举并不是常规的 Python 类。 请参阅 [枚举有什么不同？](6.常用指引/enum.md#enum-class-differences) 了解更多细节。

备注

命名法

  * 类 `Color` 是一个 _枚举_ （或 _enum_ ）

  * 属性 `Color.RED` 、 `Color.GREEN` 等是 _枚举成员_ （或 _members_ ）并且在功能上是常量。

  * 枚举成员有 _names_ 和 _values_ (`Color.RED` 的名称是 `RED`，`Color.BLUE` 的值是 `3`，等等)

* * *

## 模块内容¶

> `EnumType`
>

>> The `type` for Enum and its subclasses.

>
> `Enum`
>

>> 用于创建枚举常量的基类。

>
> `IntEnum`
>

>> 用于创建枚举常量的基类，这些常量也是 [`int`](functions.md#int "int") 的子类。 (Notes)

>
> `StrEnum`
>

>> 用于创建枚举常量的基类，这些常量也是 [`str`](stdtypes.md#str "str") 的子类。 (Notes)

>
> `Flag`
>

>> 创建可与位运算符搭配使用，又不会失去 `Flag` 成员资格的枚举常量的基类。

>
> `IntFlag`
>

>> 创建可与位运算符搭配使用，又不失去 `IntFlag` 成员资格的枚举常量的基类。`IntFlag` 成员也是 [`int`](functions.md#int "int") 的子类。 (Notes)

>
> `ReprEnum`
>

>> 由 `IntEnum` 、`StrEnum` 和 `IntFlag` 用来保持混合类型的 [`str()`](stdtypes.md#str "str") 。

>
> `EnumCheck`
>

>> 具有值 `CONTINUOUS`、`NAMED_FLAGS` 和 `UNIQUE` 的枚举，用于 `verify()` 以确保给定枚举满足各种约束。

>
> `FlagBoundary`
>

>> 具有值 `STRICT` 、 `CONFORM` 、 `EJECT` 和 `KEEP` 的枚举，允许对枚举中无效值的处理方式进行更细粒度的控制。

>
> `auto`
>

>> 实例被替换为枚举成员的适当值。 `StrEnum` 默认为成员名称的小写版本，而其他枚举默认为 1 并由此递增。

>
> `property()`
>

>> 允许 `Enum` 成员拥有属性而不会与成员名称相冲突。 `value` 和 `name` 属性都是以这样的方式实现的。

>
> `unique()`
>

>> 确保一个名称只绑定一个值的 Enum 类装饰器。

>
> `verify()`
>

>> 检查枚举的用户可选择约束的枚举类装饰器。

>
> `member()`
>

>> 使 `obj` 成为成员。可以用作装饰器。

>
> `nonmember()`
>

>> 使 `obj` 不为成员。可以用作装饰器。

>
> `global_enum()`
>

>> 修改枚举的 [`str()`](stdtypes.md#str "str") 和 [`repr()`](functions.md#repr "repr") 以将其成员显示为属于模块而不是其类，并将枚举成员导出到全局命名空间。

>
> `show_flag_values()`
>

>> 返回标志中包含的所有二次幂整数的列表。

在 3.6 版本加入: `Flag`, `IntFlag`, `auto`

在 3.11 版本加入: `StrEnum`, `EnumCheck`, `ReprEnum`, `FlagBoundary`, `property`, `member`, `nonmember`, `global_enum`, `show_flag_values`

* * *

## 数据类型¶

_class _enum.EnumType¶

    

~~~
_EnumType_ 是 _enum_ 枚举的 [metaclass](../glossary.md#term-metaclass) 。可以对 _EnumType_ 进行子类化——有关详细信息，请参阅 [Subclassing EnumType](../howto/enum.md#enumtype-examples)。

_EnumType_ 负责在最终的 _enum_ 上设置正确的 `__repr__()` 、`__str__()` 、`__format__()` 和 `__reduce__()` 方法，如以及创建枚举成员、正确处理重复项、提供枚举类的迭代等。

__call__( _cls_ , _value_ , _names=None_ , _\\*_ , _module=None_ , _qualname=None_ , _type=None_ , _start=1_ , _boundary=None_ )¶
~~~
    

~~~
此方法以两种不同的方式调用：

  * 查找现有成员：

> cls:
>  
>
> 被调用的枚举类。
>
> value:
>  
>
> 要查找的值。

  * 使用 `cls` 枚举创建新枚举（仅当现有枚举没有任何成员时）：

> cls:
>  
>
> 被调用的枚举类。
>
> value:
>  
>
> 要创建的新枚举的名称。
>
> names:
>  
>
> 新枚举成员的名称/值。
>
> module -- 模块:
>  
>
> 在其中创建新枚举的模块的名称。
>
> qualname:
>  
>
> 可以找到此枚举的模块中的实际位置。
>
> type -- 类型:
>  
>
> 新枚举的混合类型。
>
> start:
>  
>
> 枚举的第一个整数值（由 `auto` 使用）。
>
> 边界:
>  
>
> 如何处理来自位操作的超出范围的值（仅限 `Flag` ）。

__contains__( _cls_ , _member_ )¶
~~~
    

~~~
如果成员属于``cls`` 则返回``True``
~~~
    
    
~~~shell
>>> some_var = Color.RED
>>> some_var in Color
True
~~~

备注

在 Python 3.12 中，可以检查成员值而不仅仅是成员；在此之前，如果在包含检查中使用非枚举成员，则会引发 `TypeError`。

__dir__( _cls_ )¶

    

~~~
返回 `['__class__', '__doc__', '__members__', '__module__']` 和 _cls_ 中的成员名称
~~~
    
    
~~~shell
>>> dir(Color)
['BLUE', 'GREEN', 'RED', '__class__', '__contains__', '__doc__', '__getitem__', '__init_subclass__', '__iter__', '__len__', '__members__', '__module__', '__name__', '__qualname__']
~~~

__getitem__( _cls_ , _name_ )¶

    

~~~
返回 _cls_ 中匹配 _name_ 的 Enum 成员，或者引发 [`KeyError`](exceptions.md#KeyError "KeyError"):
~~~
    
    
~~~shell
>>> Color['BLUE']
<Color.BLUE: 3>
~~~

__iter__( _cls_ )¶

    

~~~
按定义顺序返回 _cls_ 中的每个成员:
~~~
    
    
~~~shell
>>> list(Color)
[<Color.RED: 1>, <Color.GREEN: 2>, <Color.BLUE: 3>]
~~~

__len__( _cls_ )¶

    

~~~
返回 _cls_ 中成员的数量:
~~~
    
    
~~~shell
>>> len(Color)
3
~~~

__reversed__( _cls_ )¶

    

~~~
按定义的逆序返回 _cls_ 中的每个成员:
~~~
    
    
~~~shell
>>> list(reversed(Color))
[<Color.BLUE: 3>, <Color.GREEN: 2>, <Color.RED: 1>]
~~~

在 3.11 版本加入: 在 3.11 之前 `enum` 使用 `EnumMeta` 类型，它被作为别名保留。

_class _enum.Enum¶

    

~~~
_Enum_ 是所有 _enum_ 枚举的基类。

name¶
~~~
    

~~~
用于定义 `Enum` 成员的名称:
~~~
    
    
~~~shell
>>> Color.BLUE.name
'BLUE'
~~~

value¶

    

~~~
赋给 `Enum` 成员的值:
~~~
    
    
~~~shell
>>> Color.RED.value
1
~~~

备注

Enum 成员值

成员值可以为任意类型: [`int`](functions.md#int "int"), [`str`](stdtypes.md#str "str") 等等。 如果具体的值不重要则你可以使用 `auto` 实例这将为你选择一个适当的值。 详情参见 `auto`。

_ignore_¶

    

~~~
`_ignore_` 仅在创建期间使用并会在创建完成后立即从枚举中移除。

`_ignore_` 是由不会被作为成员的名称组成的列包，并且这些名称还将从完成的枚举中移除。 请参阅 [TimePeriod](../howto/enum.md#enum-time-period) 获取示例。

__dir__( _self_ )¶
~~~
    

~~~
返回 `['__class__', '__doc__', '__module__', 'name', 'value']` 以及在 _self.__class___ 上定义的任何公有方法:
~~~
    
    
~~~shell
>>> from datetime import date
>>> class Weekday(Enum):
...     MONDAY = 1
...     TUESDAY = 2
...     WEDNESDAY = 3
...     THURSDAY = 4
...     FRIDAY = 5
...     SATURDAY = 6
...     SUNDAY = 7
...     @classmethod
...     def today(cls):
...         print('today is %s' % cls(date.today().isoweekday()).name)
...
>>> dir(Weekday.SATURDAY)
['__class__', '__doc__', '__eq__', '__hash__', '__module__', 'name', 'today', 'value']
~~~

_generate_next_value_( _name_ , _start_ , _count_ , _last_values_ )¶

    

~~~
> name:
>  
>
> 定义的成员名称（例如 'RED'）。
>
> start:
>  
>
> Enum 的起始值；默认为 1。
>
> count:
>  
>
> 当前定义的成员数量，不包括这一个。
>
> last_values:
>  
>
> 由前面的值组成的列表。

一个用来确定由 `auto` 所返回的下一个值的 _静态方法_ :
~~~
    
    
~~~shell
>>> from enum import auto
>>> class PowersOfThree(Enum):
...     @staticmethod
...     def _generate_next_value_(name, start, count, last_values):
...         return 3 ** (count + 1)
...     FIRST = auto()
...     SECOND = auto()
...
>>> PowersOfThree.SECOND.value
9
~~~

__init_subclass__( _cls_ , _\\**kwds_ )¶

    

~~~
一个用来进一步配置后续子类的 _类方法_ 。 在默认情况下，将不做任何事。

_missing_( _cls_ , _value_ )¶
~~~
    

~~~
一个用来查找不存在于 _cls_ 中的值的 _类方法_ 。 在默认情况下它将不做任何事，但可以被重载以实现自定义的搜索行为:
~~~
    
    
~~~shell
>>> from enum import StrEnum
>>> class Build(StrEnum):
...     DEBUG = auto()
...     OPTIMIZED = auto()
...     @classmethod
...     def _missing_(cls, value):
...         value = value.lower()
...         for member in cls:
...             if member.value == value:
...                 return member
...         return None
...
>>> Build.DEBUG.value
'debug'
>>> Build('deBUG')
<Build.DEBUG: 'debug'>
~~~

__repr__( _self_ )¶

    

~~~
返回用于 _repr()_ 调用的字符串。 在默认情况下，将返回 _Enum_ 名称、成员名称和值，但也可以被重载:
~~~
    
    
~~~shell
>>> class OtherStyle(Enum):
...     ALTERNATE = auto()
...     OTHER = auto()
...     SOMETHING_ELSE = auto()
...     def __repr__(self):
...         cls_name = self.__class__.__name__
...         return f'{cls_name}.{self.name}'
...
>>> OtherStyle.ALTERNATE, str(OtherStyle.ALTERNATE), f"{OtherStyle.ALTERNATE}"
(OtherStyle.ALTERNATE, 'OtherStyle.ALTERNATE', 'OtherStyle.ALTERNATE')
~~~

__str__( _self_ )¶

    

~~~
返回用于 _str()_ 调用的字符串。 在默认情况下，返回 _Enum_ 名称和成员名称，但也可以被重载:
~~~
    
    
~~~shell
>>> class OtherStyle(Enum):
...     ALTERNATE = auto()
...     OTHER = auto()
...     SOMETHING_ELSE = auto()
...     def __str__(self):
...         return f'{self.name}'
...
>>> OtherStyle.ALTERNATE, str(OtherStyle.ALTERNATE), f"{OtherStyle.ALTERNATE}"
(<OtherStyle.ALTERNATE: 1>, 'ALTERNATE', 'ALTERNATE')
~~~

__format__( _self_ )¶

    

~~~
返回用于 _format()_ 和 _f-string_ 调用的字符串。 在默认情况下，将返回 `__str__()` 的返回值，但也可以被重载:
~~~
    
    
~~~shell
>>> class OtherStyle(Enum):
...     ALTERNATE = auto()
...     OTHER = auto()
...     SOMETHING_ELSE = auto()
...     def __format__(self, spec):
...         return f'{self.name}'
...
>>> OtherStyle.ALTERNATE, str(OtherStyle.ALTERNATE), f"{OtherStyle.ALTERNATE}"
(<OtherStyle.ALTERNATE: 1>, 'OtherStyle.ALTERNATE', 'ALTERNATE')
~~~

备注

将 `auto` 用于 `Enum` 将得到递增的整数值，从 `1` 开始。

在 3.12 版本发生变更: 增加了 [数据类支持](6.常用指引/enum.md#enum-dataclass-support)

_class _enum.IntEnum¶

    

~~~
_IntEnum_ 与 _Enum_ 是一样的，但其成员还属于整数并可被用在任何可以使用整数的地方。 如果对一个 _IntEnum_ 成员执行整数运算，结果值将失去其枚举状态。
~~~
    
    
~~~shell
>>> from enum import IntEnum
>>> class Number(IntEnum):
...     ONE = 1
...     TWO = 2
...     THREE = 3
...
>>> Number.THREE
<Number.THREE: 3>
>>> Number.ONE + Number.TWO
3
>>> Number.THREE + 5
8
>>> Number.THREE == 3
True
~~~

备注

将 `auto` 用于 `IntEnum` 将得到递增的整数值，从 `1` 开始。

在 3.11 版本发生变更: [`__str__()`](datamodel.md#object.__str__ "object.__str__") 现在是 `int.__str__()` 以更好地支持 _现有常量的替换_ 应用场景。 出于同样的原因 [`__format__()`](datamodel.md#object.__format__ "object.__format__") 也已经是 `int.__format__()`。

_class _enum.StrEnum¶

    

~~~
_StrEnum_ 与 _Enum_ 是一样的，但其成员还属于字符串并可被用在任何可以使用字符串的地方。 对一个 _StrEnum_ 成员执行字符串操作的结果将不是该枚举的一部分。

备注

在标准库中有些地方会检查是否是真正的 [`str`](stdtypes.md#str "str") 而不是 [`str`](stdtypes.md#str "str") 的子类 (例如使用 `type(unknown) == str` 而不是 `isinstance(unknown, str)`)，在这些地方你将需要使用 `str(StrEnum.member)`。

备注

将 `auto` 用于 `StrEnum` 将得到小写形式的成员名称字符串值。

备注

[`__str__()`](../reference/datamodel.md#object.__str__ "object.__str__") 是 `str.__str__()` 以更好地支持 _现有常量的替换_ 应用场景。 出于同样的原因 [`__format__()`](../reference/datamodel.md#object.__format__ "object.__format__") 也是 `str.__format__()`。

在 3.11 版本加入.

_class _enum.Flag¶
~~~
    

~~~
_Flag_ 成员支持位运算符 `&` ( _AND_ ), `|` ( _OR_ ), `^` ( _XOR_ ) 和 `~` ( _INVERT_ )；这些运算的结果将是该枚举的成员。

__contains__( _self_ , _value_ )¶
~~~
    

~~~
如果 value 是 self 之中则返回 _True_ :
~~~
    
    
~~~shell
>>> from enum import Flag, auto
>>> class Color(Flag):
...     RED = auto()
...     GREEN = auto()
...     BLUE = auto()
...
>>> purple = Color.RED | Color.BLUE
>>> white = Color.RED | Color.GREEN | Color.BLUE
>>> Color.GREEN in purple
False
>>> Color.GREEN in white
True
>>> purple in white
True
>>> white in purple
False
~~~

__iter__(self):

    

~~~
返回所有包含的非别名成员:
~~~
    
    
~~~shell
>>> list(Color.RED)
[<Color.RED: 1>]
>>> list(purple)
[<Color.RED: 1>, <Color.BLUE: 4>]
~~~

在 3.11 版本发生变更: 在迭代期间别名将不会再被返回。

__len__(self):

    

~~~
返回旗标中成员的数量:
~~~
    
    
~~~shell
>>> len(Color.GREEN)
1
>>> len(white)
3
~~~

__bool__(self):

    

~~~
如果旗标中有成员则返回 _True_ ，否则返回 _False_ :
~~~
    
    
~~~shell
>>> bool(Color.GREEN)
True
>>> bool(white)
True
>>> black = Color(0)
>>> bool(black)
False
~~~

__or__( _self_ , _other_ )¶

    

~~~
返回当前旗标与另一个旗标执行二进制或运算的结果:
~~~
    
    
~~~shell
>>> Color.RED | Color.GREEN
<Color.RED|GREEN: 3>
~~~

__and__( _self_ , _other_ )¶

    

~~~
返回当前旗标与另一个旗标执行二进制与运算的结果:
~~~
    
    
~~~shell
>>> purple & white
<Color.RED|BLUE: 5>
>>> purple & Color.GREEN
<Color: 0>
~~~

__xor__( _self_ , _other_ )¶

    

~~~
返回当前旗标与另一个旗标执行二进制异或运算的结果:
~~~
    
    
~~~shell
>>> purple ^ white
<Color.GREEN: 2>
>>> purple ^ Color.GREEN
<Color.RED|GREEN|BLUE: 7>
~~~

__invert__(self):

    

~~~
返回 _type(self)_ 中所有不在 self 中的旗标:
~~~
    
    
~~~shell
>>> ~white
<Color: 0>
>>> ~purple
<Color.GREEN: 2>
>>> ~Color.RED
<Color.GREEN|BLUE: 6>
~~~

_numeric_repr_()¶

    

~~~
用于格式化任何其他未命名数字值的函数。 默认为数字值的 repr；常见的选择有 [`hex()`](functions.md#hex "hex") 和 [`oct()`](functions.md#oct "oct")。

备注

将 `auto` 用于 `Flag` 将得到二的整数次方，从 `1` 开始。

在 3.11 版本发生变更: 零值旗标的 _repr()_ 已被修改。 现在将是:
~~~
    
    
~~~shell
>>> Color(0) 
<Color: 0>
~~~

_class _enum.IntFlag¶

    

~~~
_IntFlag_ 与 _Flag_ 相同，但其成员还属于整数类型并能被用于任何可以使用整数的地方。
~~~
    
    
~~~shell
>>> from enum import IntFlag, auto
>>> class Color(IntFlag):
...     RED = auto()
...     GREEN = auto()
...     BLUE = auto()
...
>>> Color.RED & 2
<Color: 0>
>>> Color.RED | 2
<Color.RED|GREEN: 3>
~~~

如果对一个 _IntFlag_ 成员执行任何整数运算，结果将不再是一个 _IntFlag_ :

    
    
~~~shell
>>> Color.RED + 2
3
~~~

如果对一个 _IntFlag_ 成员执行 _Flag_ 操作并且:

>   * 结果是一个合法的 _IntFlag_ : 将返回一个 _IntFlag_
>
>   * 结果不是一个合法的 _IntFlag_ : 结果将取决于 _FlagBoundary_ 设置
>
>

未命名零值旗标的 _repr()_ 已被修改。 现在将是:

    
    
~~~shell
>>> Color(0)
<Color: 0>
~~~

备注

将 `auto` 用于 `IntFlag` 将得到二的整数次方，从 `1` 开始。

在 3.11 版本发生变更: [`__str__()`](datamodel.md#object.__str__ "object.__str__") 现在是 `int.__str__()` 以更好地支持 _现有常量的替换_ 应用场景。 出于同样的原因 [`__format__()`](datamodel.md#object.__format__ "object.__format__") 也已经是 `int.__format__()`。

对一个 `IntFlag` 的反转现在将返回一个等于不在给定旗标中的所有旗标的并集的正值，而非一个负值。 这与现有 `Flag` 的行为相匹配。

_class _enum.ReprEnum¶

    

~~~
`ReprEnum` 将使用 `Enum` 的 `repr()`，但使用混入数据类型的 [`str()`](stdtypes.md#str "str"):

>   * `int.__str__()` 用于 `IntEnum` 和 `IntFlag`
>
>   * `str.__str__()` 用于 `StrEnum`
>
>

从 `ReprEnum` 继承以存放混入数据类型的 [`str()`](stdtypes.md#str "str") / [`format()`](functions.md#format "format") 而不是使用 `Enum` 默认的 `str()`。

在 3.11 版本加入.

_class _enum.EnumCheck¶
~~~
    

~~~
_EnumCheck_ 包含由 `verify()` 装饰器用来确保各种约束的选项；失败的约束将导致 [`ValueError`](exceptions.md#ValueError "ValueError")。

UNIQUE¶
~~~
    

~~~
确保每个值只有一个名称:
~~~
    
    
~~~shell
>>> from enum import Enum, verify, UNIQUE
>>> @verify(UNIQUE)
... class Color(Enum):
...     RED = 1
...     GREEN = 2
...     BLUE = 3
...     CRIMSON = 1
Traceback (most recent call last):
...
ValueError: aliases found in <enum 'Color'>: CRIMSON -> RED
~~~

CONTINUOUS¶

    

~~~
确保在最低值成员和最高值成员之间没有缺失的值:
~~~
    
    
~~~shell
>>> from enum import Enum, verify, CONTINUOUS
>>> @verify(CONTINUOUS)
... class Color(Enum):
...     RED = 1
...     GREEN = 2
...     BLUE = 5
Traceback (most recent call last):
...
ValueError: invalid enum 'Color': missing values 3, 4
~~~

NAMED_FLAGS¶

    

~~~
确保任何旗标分组/掩码只包含已命名的旗标 -- 在值是明确指定而不是由 `auto()` 生成时将很有用处:
~~~
    
    
~~~shell
>>> from enum import Flag, verify, NAMED_FLAGS
>>> @verify(NAMED_FLAGS)
... class Color(Flag):
...     RED = 1
...     GREEN = 2
...     BLUE = 4
...     WHITE = 15
...     NEON = 31
Traceback (most recent call last):
...
ValueError: invalid Flag 'Color': aliases WHITE and NEON are missing combined values of 0x18 [use enum.show_flag_values(value) for details]
~~~

备注

CONTINUOUS 和 NAMED_FLAGS 被设计用于配合整数值成员。

在 3.11 版本加入.

_class _enum.FlagBoundary¶

    

~~~
_FlagBoundary_ 控制在 _Flag_ 及其子类中如何处理超出范围的值。

STRICT¶
~~~
    

~~~
超出范围的值将导致引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 这是 `Flag` 的默认设置:
~~~
    
    
~~~shell
>>> from enum import Flag, STRICT, auto
>>> class StrictFlag(Flag, boundary=STRICT):
...     RED = auto()
...     GREEN = auto()
...     BLUE = auto()
...
>>> StrictFlag(2**2 + 2**4)
Traceback (most recent call last):
...
ValueError: <flag 'StrictFlag'> invalid value 20
    given 0b0 10100
  allowed 0b0 00111
~~~

CONFORM¶

    

~~~
超出范围的值将导致无效的值被移除，保留有效的 _Flag_ 值:
~~~
    
    
~~~shell
>>> from enum import Flag, CONFORM, auto
>>> class ConformFlag(Flag, boundary=CONFORM):
...     RED = auto()
...     GREEN = auto()
...     BLUE = auto()
...
>>> ConformFlag(2**2 + 2**4)
<ConformFlag.BLUE: 4>
~~~

EJECT¶

    

~~~
超出范围的值将失去它们的 _Flag_ 成员属性并转换为 [`int`](functions.md#int "int")。
~~~
    
    
~~~shell
>>> from enum import Flag, EJECT, auto
>>> class EjectFlag(Flag, boundary=EJECT):
...     RED = auto()
...     GREEN = auto()
...     BLUE = auto()
...
>>> EjectFlag(2**2 + 2**4)
20
~~~

KEEP¶

    

~~~
超出范围的值将被保留， _Flag_ 成员属性也将被保留。 这是 `IntFlag` 的默认设置:
~~~
    
    
~~~shell
>>> from enum import Flag, KEEP, auto
>>> class KeepFlag(Flag, boundary=KEEP):
...     RED = auto()
...     GREEN = auto()
...     BLUE = auto()
...
>>> KeepFlag(2**2 + 2**4)
<KeepFlag.BLUE|16: 20>
~~~

在 3.11 版本加入.

* * *

### 支持的 `__dunder__` 名称¶

`__members__` 是由 `member_name`:`member` 条目组成的只读有序映射。 它只在类上可用。

如果指定了 [`__new__()`](datamodel.md#object.__new__ "object.__new__")，它必须创建并返回枚举成员；相应地设置成员的 `_value_` 也是一个很好的主意。 一旦所有成员都创建完成它将不再被使用。

### 支持的 `_sunder_` 名称¶

  * `_name_` \-- 成员的名称

  * `_value_` \-- 成员的值；可以在 `__new__` 中设置 / 修改

  * `_missing_` \-- 当未发现某个值时所使用的查找函数；可被重载

  * `_ignore_` \-- 一个名称列表，可以为 [`list`](stdtypes.md#list "list") 或 [`str`](stdtypes.md#str "str")，它不会被转化为成员，并将从最终类中被移除

  * `_order_` \-- 用于 Python 2/3 代码以确保成员顺序一致（类属性，在类创建期间会被移除）

  * `_generate_next_value_` \-- 用于为枚举成员获取适当的值；可被重载

> 备注
>
> 对于标准的 `Enum` 类，选择的下一个值是最后所见值加 1。
>
> 对于 `Flag` 类，下一个选择的值将是下一个最高的 2 次幂数，与最后所见值无关。

在 3.6 版本加入: `_missing_`, `_order_`, `_generate_next_value_`

在 3.7 版本加入: `_ignore_`

* * *

## 工具与装饰器¶

_class _enum.auto¶

    

~~~
_auto_ 可被用来替换某个值。 如果使用， _Enum_ 机制将调用一个 _Enum_ 的 `_generate_next_value_()` 来获取适当的值。 对于 _Enum_ 和 _IntEnum_ 这个适当的值将为最近的值加一；对于 _Flag_ 和 _IntFlag_ 它将为大于最大的值的最大的二的整数次方；对于 _StrEnum_ 它将为成员名称的小写版本。 如果将 _auto()_ 与手动指定的值混用则必须十分小心。

_auto_ 实际仅会在赋值操作的最高层级上被解析:

>   * `FIRST = auto()` 将是可用的 (auto() 会被替换为 `1`);
>
>   * `SECOND = auto(), -2` 将是可用的 (auto 会被替换为 `2`，因此 `2, -2` 将
>  
>
> 被用来创建 `SECOND` 枚举成员;
>
>   * `THREE = [auto(), -3]` 将 _不可用_ (`<auto instance>, -3` 将被用来创建 `THREE` 枚举成员)
>
>

在 3.11.1 版本发生变更: 在之前的版本中，`auto()` 必须为赋值行中唯一的内容才是可用的。

`_generate_next_value_` 可以被重载以便自定义 _auto_ 所使用的值。

备注

在 3.13 中默认的 `_generate_next_value_` 将总是返回最高成员值递增 1 的结果，并且如果有任何成员为不兼容的类型则将失败。, and will fail if any member is an incompatible type.

@enum.property¶
~~~
    

~~~
一个类似于内置 _property_ 的装饰器，但是专用于枚举。 它允许成员属性具有与成员自身相同的名称。

备注

_property_ 和成员必须在单独的类中定义；例如 _value_ 和 _name_ 属性是在 _Enum_ 类中定义，而 _Enum_ 的子类可以定义名称为 `value` 和 `name` 的成员。

在 3.11 版本加入.

@enum.unique¶
~~~
    

~~~
一个专用于枚举的 [`class`](../reference/compound_stmts.md#class) 装饰器。 它将搜索一个枚举的 `__members__`，收集它所找到的任何别名；如果找到了任何别名则会引发 [`ValueError`](exceptions.md#ValueError "ValueError") 并附带详情:
~~~
    
    
~~~shell
>>> from enum import Enum, unique
>>> @unique
... class Mistake(Enum):
...     ONE = 1
...     TWO = 2
...     THREE = 3
...     FOUR = 3
...
Traceback (most recent call last):
...
ValueError: duplicate values found in <enum 'Mistake'>: FOUR -> THREE
~~~

@enum.verify¶

    

~~~
一个专用于枚举的 [`class`](../reference/compound_stmts.md#class) 装饰器。 将使用来自 `EnumCheck` 的成员指明应当在被装饰的枚举上检查哪些约束。

在 3.11 版本加入.

@enum.member¶
~~~
    

~~~
一个在枚举中使用的装饰器：它的目标将成为一个成员。

在 3.11 版本加入.

@enum.nonmember¶
~~~
    

~~~
一个在枚举中使用的装饰器：它的目标将不会成员一个成员。

在 3.11 版本加入.

@enum.global_enum¶
~~~
    

~~~
一个修改枚举的 [`str()`](stdtypes.md#str "str") 和 [`repr()`](functions.md#repr "repr") 来将其成员显示为属于模块而不是类的装饰器。 应当仅在枚举成员被导出到模块全局命名空间时（请参看 [`re.RegexFlag`](re.md#re.RegexFlag "re.RegexFlag") 获取示例）使用。

在 3.11 版本加入.

enum.show_flag_values( _value_ )¶
~~~
    

~~~
