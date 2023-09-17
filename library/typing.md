# `typing` —— 对类型提示的支持¶

在 3.5 版本加入.

**源代码：** [Lib/typing.py](https://github.com/python/cpython/tree/3.12/Lib/typing.py)

备注

Python 运行时不强制要求函数与变量类型注解。它们可被类型检查器、IDE、错误检查器等第三方工具使用。

* * *

本模块提供对类型提示的运行时支持。对于类型系统的原始说明，请参阅 [**PEP 484**](https://peps.python.org/pep-0484/)。一个更简明的介绍是 [**PEP 483**](https://peps.python.org/pep-0483/)。

下面的函数接收与返回的都是字符串，注解方式如下：

    
    
~~~python
def greeting(name: str) -> str:
    return 'Hello ' + name
~~~

`greeting` 函数中，参数 `name` 的类型应是 [`str`](stdtypes.md#str "str")，返回类型是 [`str`](stdtypes.md#str "str")。子类型也可以作为参数。

新的功能频繁地被添加到 `typing` 模块中。[typing_extensions](https://pypi.org/project/typing-extensions/) 包提供了这些新功能对旧版本 Python 的向后移植。

要获取已弃用特性及其弃用时间线的概要，请参阅 Deprecation Timeline of Major Features。

参见

["类型系统备忘单"](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.md)

    

~~~
关于类型提示的概览（发布于 mypy 文档站点）

[mypy 文档](https://mypy.readthedocs.io/en/stable/index.md) 的 "Type System Reference" 章节
~~~
    

~~~
Python 类型系统是通过 PEP 来标准化的，因此该参考应当广泛适用于大多数 Python 类型检查器。 （但某些部分仍然是 mypy 专属的。）

["Static Typing with Python"](https://typing.readthedocs.io/en/latest/)
~~~
    

~~~
由社区编写的不限定具体类型检查器的文档，详细讲解了类型系统特性，有用的类型相关工具以及类型的最佳实践。

## 相关的 PEP¶

自从在 [**PEP 484**](https://peps.python.org/pep-0484/) 和 [**PEP 483**](https://peps.python.org/pep-0483/) 中首次引入类型提示之来，已有多个 PEP 对 Python 的类型标注框架进行了修改和加强:

The full list of PEPs

  * [**PEP 526**](https://peps.python.org/pep-0526/): 变量注解的语法
~~~

 _引入_ 在函数定义之外标注变量的语法，以及 `ClassVar` 。

  * [**PEP 544**](https://peps.python.org/pep-0544/): Protocol：结构子类型（静态鸭子类型）。
    

 _引入_ `Protocol` 和 `@runtime_checkable` 装饰器。

  * [**PEP 585**](https://peps.python.org/pep-0585/): 标准集合中的类型提示泛型
    

 _引入_ [`types.GenericAlias`](types.md#types.GenericAlias "types.GenericAlias") 和使用标准库类作为 [通用类型](stdtypes.md#types-genericalias) 的能力。

  * [**PEP 586**](https://peps.python.org/pep-0586/): 文字类型
    

 _引入_ `Literal`

  * [**PEP 589**](https://peps.python.org/pep-0589/): TypedDict: 具有固定键集的字典的类型提示
    

 _引入_ `TypedDict`

  * [**PEP 591**](https://peps.python.org/pep-0591/): 为 typing 添加最终限定符
    

 _引入_ `Final` 和 `@final` 装饰器

  * [**PEP 593**](https://peps.python.org/pep-0593/): 灵活的函数和变量注解
    

 _引入_ `Annotated`

  * [**PEP 604**](https://peps.python.org/pep-0604/): 允许 `X | Y` 形式的联合类型写法
    

 _引入_ [`types.UnionType`](types.md#types.UnionType "types.UnionType") 和使用二元或运算符 `|` 来表示 [类型联合](stdtypes.md#types-union) 的能力。

  * [**PEP 612**](https://peps.python.org/pep-0612/): 形参规格变量
    

 _引入_ `ParamSpec` 和 `Concatenate`

  * [**PEP 613**](https://peps.python.org/pep-0613/): 显式类型别名
    

 _引入_ `TypeAlias`

  * [**PEP 646**](https://peps.python.org/pep-0646/)：可变参数泛型
    

 _引入_ `TypeVarTuple`

  * [**PEP 647**](https://peps.python.org/pep-0647/): 用户自定义的类型保护器
    

 _引入_ `TypeGuard`

  * [**PEP 655**](https://peps.python.org/pep-0655/)：将单个 TypedDict 项标记为必填或非必填项
    

 _引入_ `Required` 和 `NotRequired`

  * [**PEP 673**](https://peps.python.org/pep-0673/)：Self 类型
    

 _引入_ `Self`

  * [**PEP 675**](https://peps.python.org/pep-0675/)：任意字面值字符串类型
    

 _引入_ `LiteralString`

  * [**PEP 681**](https://peps.python.org/pep-0681/)：数据类变换
    

 _引入_ `@dataclass_transform` 装饰器

  * [**PEP 692**](https://peps.python.org/pep-0692/): 使用 `TypedDict` 进行更精确的 `**kwargs` 类型标注
    

通过 `Unpack` 和 `TypedDict` _引入了_ 一种对 `**kwargs` 进行类型标注的新方式

  * [**PEP 695**](https://peps.python.org/pep-0695/): 类型形参语法
    

 _引入_ 创建泛型函数、类和类型别名的语法。

  * [**PEP 698**](https://peps.python.org/pep-0698/): 在 typing 中添加一个重写装饰器
    

 _引入_ `@override` 装饰器

  

~~~
## 类型别名¶

类型别名是使用 [`type`](../reference/simple_stmts.md#type) 语句来定义的，它将创建一个 `TypeAliasType` 的实例。 在这个示例中，`Vector` 和 `list[float]` 将被静态类型检查器等同处理:
~~~
    
    
~~~
type Vector = list[float]

def scale(scalar: float, vector: Vector) -> Vector:
    return [scalar * num for num in vector]

# passes type checking; a list of floats qualifies as a Vector.
new_vector = scale(2.0, [1.0, -4.2, 5.4])
~~~

类型别名适用于简化复杂的类型签名。例如：

    
    
~~~
from collections.abc import Sequence

type ConnectionOptions = dict[str, str]
type Address = tuple[str, int]
type Server = tuple[Address, ConnectionOptions]

def broadcast_message(message: str, servers: Sequence[Server]) -> None:
    ...

# The static type checker will treat the previous type signature as
# being exactly equivalent to this one.
def broadcast_message(
        message: str,
        servers: Sequence[tuple[tuple[str, int], dict[str, str]]]) -> None:
    ...
~~~

[`type`](../reference/simple_stmts.md#type) 语句是在 Python 3.12 中新增加的。 为了向下兼容，类型别名也可以通过简单的赋值来创建:

    
    
~~~
Vector = list[float]
~~~

或者用 `TypeAlias` 标记来显式说明这是一个类型别名，而非一般的变量赋值：

    
    
~~~
from typing import TypeAlias

Vector: TypeAlias = list[float]
~~~

## NewType¶

用 `NewType` 助手创建与原类型不同的类型：

    
    
~~~
from typing import NewType

UserId = NewType('UserId', int)
some_id = UserId(524313)
~~~

静态类型检查器把新类型当作原始类型的子类，这种方式适用于捕捉逻辑错误：

    
    
~~~python
def get_user_name(user_id: UserId) -> str:
    ...

# passes type checking
user_a = get_user_name(UserId(42351))

# fails type checking; an int is not a UserId
user_b = get_user_name(-1)
~~~

`UserId` 类型的变量可执行所有 `int` 操作，但返回结果都是 `int` 类型。这种方式允许在预期 `int` 时传入 `UserId`，还能防止意外创建无效的 `UserId`：

    
    
~~~
# 'output' is of type 'int', not 'UserId'
output = UserId(23413) + UserId(54341)
~~~

注意，这些检查只由静态类型检查器强制执行。在运行时，语句 `Derived = NewType('Derived', Base)` 将产生一个 `Derived` 可调用对象，该对象立即返回你传递给它的任何参数。 这意味着语句 `Derived(some_value)` 不会创建一个新的类，也不会引入超出常规函数调用的很多开销。

更确切地说，在运行时，`some_value is Derived(some_value)` 表达式总为 True。

创建 `Derived` 的子类型是无效的:

    
    
~~~
from typing import NewType

UserId = NewType('UserId', int)

# Fails at runtime and does not pass type checking
class AdminUserId(UserId): pass
~~~

然而，我们可以在 "派生的" `NewType` 的基础上创建一个 `NewType`。

    
    
~~~
from typing import NewType

UserId = NewType('UserId', int)

ProUserId = NewType('ProUserId', UserId)
~~~

同时，`ProUserId` 的类型检查也可以按预期执行。

详见 [**PEP 484**](https://peps.python.org/pep-0484/)。

备注

请记住使用类型别名将声明两个类型是相互 _等价_ 的。 使用 `type Alias = Original` 将使静态类型检查器在任何情况下都把 `Alias` 视为与 `Original` _完全等价_ 。 这在你想要简化复杂的类型签名时会很有用处。

反之，`NewType` 声明把一种类型当作另一种类型的 _子类型_ 。`Derived = NewType('Derived', Original)` 时，静态类型检查器把 `Derived` 当作 `Original` 的 _子类_ ，即，`Original` 类型的值不能用在预期 `Derived` 类型的位置。这种方式适用于以最小运行时成本防止逻辑错误。

在 3.5.2 版本加入.

在 3.10 版本发生变更: `NewType` 现在是一个类而不是一个函数。 因此，当调用 `NewType` 而非常规函数时会有一些额外的运行时开销。

在 3.11 版本发生变更: 调用 `NewType` 的性能已恢复到 Python 3.9 时的水平。

## 标注可调用对象¶

函数 -- 或其他 [callable](../glossary.md#term-callable) 对象 -- 可以使用 [`collections.abc.Callable`](collections.abc.md#collections.abc.Callable "collections.abc.Callable") 或 `typing.Callable` 来标注。 `Callable[[int], str]` 表示一个接受 [`int`](functions.md#int "int") 类型的单个参数并返回 [`str`](stdtypes.md#str "str") 的函数。

例如:

    
    
~~~
from collections.abc import Callable, Awaitable

def feeder(get_next_item: Callable[[], str]) -> None:
    ...  # Body

def async_query(on_success: Callable[[int], None],
                on_error: Callable[[int, Exception], None]) -> None:
    ...  # Body

async def on_update(value: str) -> None:
    ...  # Body

callback: Callable[[str], Awaitable[None]] = on_update
~~~

下标语法总是要刚好使用两个值：参数列表和返回类型。 参数列表必须是一个由类型组成的列表、`ParamSpec`、`Concatenate` 或省略号。 返回类型必须是单一类型。

如果将一个省略号字面值 `...` 作为参数列表，则表示可以接受包含任意形参列表的可调用对象:

    
    
~~~python
def concat(x: str, y: str) -> str:
    return x + y

x: Callable[..., str]
x = str     # OK
x = concat  # Also OK
~~~

`Callable` 无法表达复杂的签名如接受可变数量参数的函数、`重载的函数` 或具有仅限关键字形参的函数。 但是，这些签名可通过定义具有 [`__call__()`](../reference/datamodel.md#object.__call__ "object.__call__") 方法的 `Protocol` 类来表达:

    
    
~~~
from collections.abc import Iterable
from typing import Protocol

class Combiner(Protocol):
    def __call__(self, *vals: bytes, maxlen: int | None = None) -> list[bytes]: ...

def batch_proc(data: Iterable[bytes], cb_results: Combiner) -> bytes:
    for item in data:
        ...

def good_cb(*vals: bytes, maxlen: int | None = None) -> list[bytes]:
    ...
def bad_cb(*vals: bytes, maxitems: int | None) -> list[bytes]:
    ...

batch_proc([], good_cb)  # OK
batch_proc([], bad_cb)   # Error! Argument 2 has incompatible type because of
                         # different name and kind in the callback
~~~

以其他可调用对象为参数的可调用对象可以使用 `ParamSpec` 来表明其参数类型是相互依赖的。 此外，如果该可调用对象增加或删除了其他可调用对象的参数，可以使用 `Concatenate` 操作符。 它们分别采取 `Callable[ParamSpecVariable, ReturnType]` 和 `Callable[Concatenate[Arg1Type, Arg2Type, ..., ParamSpecVariable], ReturnType]` 的形式。

在 3.10 版本发生变更: `Callable` 现在支持 `ParamSpec` 和 `Concatenate`。 详情见 [**PEP 612**](https://peps.python.org/pep-0612/)。

参见

`ParamSpec` 和 `Concatenate` 的文档提供了在 `Callable` 中使用的例子。

## 泛型（Generic）¶

由于无法以通用方式静态地推断容器中保存的对象的类型信息，标准库中的许多容器类都支持下标操作来以表示容器元素的预期类型。

    
    
~~~
from collections.abc import Mapping, Sequence

class Employee: ...

# Sequence[Employee] indicates that all elements in the sequence
# must be instances of "Employee".
# Mapping[str, str] indicates that all keys and all values in the mapping
# must be strings.
def notify_by_email(employees: Sequence[Employee],
                    overrides: Mapping[str, str]) -> None: ...
~~~

泛型函数和类可以通过使用 [类型形参语法](../reference/compound_stmts.md#type-params) 来实现参数化:

    
    
~~~
from collections.abc import Sequence

def first[T](l: Sequence[T]) -> T:  # Function is generic over the TypeVar "T"
    return l[0]
~~~

或直接使用 `TypeVar` 工厂：

    
    
~~~
from collections.abc import Sequence
from typing import TypeVar

U = TypeVar('U')                  # Declare type variable "U"

def second(l: Sequence[U]) -> U:  # Function is generic over the TypeVar "U"
    return l[1]
~~~

在 3.12 版本发生变更: Syntactic support for generics is new in Python 3.12.

## Annotating tuples¶

For most containers in Python, the typing system assumes that all elements in the container will be of the same type. For example:

    
    
~~~
from collections.abc import Mapping

# Type checker will infer that all elements in ``x`` are meant to be ints
x: list[int] = []

# Type checker error: ``list`` only accepts a single type argument:
y: list[int, str] = [1, 'foo']

# Type checker will infer that all keys in ``z`` are meant to be strings,
# and that all values in ``z`` are meant to be either strings or ints
z: Mapping[str, str | int] = {}
~~~

[`list`](stdtypes.md#list "list") only accepts one type argument, so a type checker would emit an error on the `y` assignment above. Similarly, [`Mapping`](collections.abc.md#collections.abc.Mapping "collections.abc.Mapping") only accepts two type arguments: the first indicates the type of the keys, and the second indicates the type of the values.

Unlike most other Python containers, however, it is common in idiomatic Python code for tuples to have elements which are not all of the same type. For this reason, tuples are special-cased in Python's typing system. [`tuple`](stdtypes.md#tuple "tuple") accepts _any number_ of type arguments:

    
    
~~~
# OK: ``x`` is assigned to a tuple of length 1 where the sole element is an int
x: tuple[int] = (5,)

# OK: ``y`` is assigned to a tuple of length 2;
# element 1 is an int, element 2 is a str
y: tuple[int, str] = (5, "foo")

# Error: the type annotation indicates a tuple of length 1,
# but ``z`` has been assigned to a tuple of length 3
z: tuple[int] = (1, 2, 3)
~~~

To denote a tuple which could be of _any_ length, and in which all elements are of the same type `T`, use `tuple[T, ...]`. To denote an empty tuple, use `tuple[()]`. Using plain `tuple` as an annotation is equivalent to using `tuple[Any, ...]`:

    
    
~~~
x: tuple[int, ...] = (1, 2)
# These reassignments are OK: ``tuple[int, ...]`` indicates x can be of any length
x = (1, 2, 3)
x = ()
# This reassignment is an error: all elements in ``x`` must be ints
x = ("foo", "bar")

# ``y`` can only ever be assigned to an empty tuple
y: tuple[()] = ()

z: tuple = ("foo", "bar")
# These reassignments are OK: plain ``tuple`` is equivalent to ``tuple[Any, ...]``
z = (1, 2, 3)
z = ()
~~~

## The type of class objects¶

A variable annotated with `C` may accept a value of type `C`. In contrast, a variable annotated with `type[C]` (or `typing.Type[C]`) may accept values that are classes themselves -- specifically, it will accept the _class object_ of `C`. For example:

    
    
~~~
a = 3         # Has type ``int``
b = int       # Has type ``type[int]``
c = type(a)   # Also has type ``type[int]``
~~~

注意，`type[C]` 是协变的：

    
    
~~~
class User: ...
class ProUser(User): ...
class TeamUser(User): ...

def make_new_user(user_class: type[User]) -> User:
    # ...
    return user_class()

make_new_user(User)      # OK
make_new_user(ProUser)   # Also OK: ``type[ProUser]`` is a subtype of ``type[User]``
make_new_user(TeamUser)  # Still fine
make_new_user(User())    # Error: expected ``type[User]`` but got ``User``
make_new_user(int)       # Error: ``type[int]`` is not a subtype of ``type[User]``
~~~

The only legal parameters for [`type`](functions.md#type "type") are classes, `Any`, type variables, and unions of any of these types. For example:

    
    
~~~python
def new_non_team_user(user_class: type[BasicUser | ProUser]): ...

new_non_team_user(BasicUser)  # OK
new_non_team_user(ProUser)    # OK
new_non_team_user(TeamUser)   # Error: ``type[TeamUser]`` is not a subtype
                              # of ``type[BasicUser | ProUser]``
new_non_team_user(User)       # Also an error
~~~

`type[Any]` is equivalent to [`type`](functions.md#type "type"), which is the root of Python's [metaclass hierarchy](../reference/datamodel.md#metaclasses).

## 用户定义的泛型类型¶

用户定义的类可以定义为泛型类。

    
    
~~~
from logging import Logger

class LoggedVar[T]:
    def __init__(self, value: T, name: str, logger: Logger) -> None:
        self.name = name
        self.logger = logger
        self.value = value

    def set(self, new: T) -> None:
        self.log('Set ' + repr(self.value))
        self.value = new

    def get(self) -> T:
        self.log('Get ' + repr(self.value))
        return self.value

    def log(self, message: str) -> None:
        self.logger.info('%s: %s', self.name, message)
~~~

This syntax indicates that the class `LoggedVar` is parameterised around a single `type variable` `T` . This also makes `T` valid as a type within the class body.

Generic classes implicitly inherit from `Generic`. For compatibility with Python 3.11 and lower, it is also possible to inherit explicitly from `Generic` to indicate a generic class:

    
    
~~~
from typing import TypeVar, Generic

T = TypeVar('T')

class LoggedVar(Generic[T]):
    ...
~~~

Generic classes have [`__class_getitem__()`](../reference/datamodel.md#object.__class_getitem__ "object.__class_getitem__") methods, meaning they can be parameterised at runtime (e.g. `LoggedVar[int]` below):

    
    
~~~
from collections.abc import Iterable

def zero_all_vars(vars: Iterable[LoggedVar[int]]) -> None:
    for var in vars:
        var.set(0)
~~~

一个泛型可以有任何数量的类型变量。所有种类的 `TypeVar` 都可以作为泛型的参数:

    
    
~~~
from typing import TypeVar, Generic, Sequence

class WeirdTrio[T, B: Sequence[bytes], S: (int, str)]:
    ...

OldT = TypeVar('OldT', contravariant=True)
OldB = TypeVar('OldB', bound=Sequence[bytes], covariant=True)
OldS = TypeVar('OldS', int, str)

class OldWeirdTrio(Generic[OldT, OldB, OldS]):
    ...
~~~

`Generic` 类型变量的参数应各不相同。下列代码就是无效的：

    
    
~~~
from typing import TypeVar, Generic
...

class Pair[M, M]:  # SyntaxError
    ...

T = TypeVar('T')

class Pair(Generic[T, T]):   # INVALID
    ...
~~~

Generic classes can also inherit from other classes:

    
    
~~~
from collections.abc import Sized

class LinkedList[T](Sized):
    ...
~~~

When inheriting from generic classes, some type parameters could be fixed:

    
    
~~~
from collections.abc import Mapping

class MyDict[T](Mapping[str, T]):
    ...
~~~

比如，本例中 `MyDict` 调用的单参数，`T`。

Using a generic class without specifying type parameters assumes `Any` for each position. In the following example, `MyIterable` is not generic but implicitly inherits from `Iterable[Any]`:

    
    
~~~
from collections.abc import Iterable

class MyIterable(Iterable): # Same as Iterable[Any]
    ...
~~~

User-defined generic type aliases are also supported. Examples:

    
    
~~~
from collections.abc import Iterable

type Response[S] = Iterable[S] | int

# Return type here is same as Iterable[str] | int
def response(query: str) -> Response[str]:
    ...

type Vec[T] = Iterable[tuple[T, T]]

def inproduct[T: (int, float, complex)](v: Vec[T]) -> T: # Same as Iterable[tuple[T, T]]
    return sum(x*y for x, y in v)
~~~

For backward compatibility, generic type aliases can also be created through a simple assignment:

    
    
~~~
from collections.abc import Iterable
from typing import TypeVar

S = TypeVar("S")
Response = Iterable[S] | int
~~~

在 3.7 版本发生变更: `Generic` 不再支持自定义元类。

在 3.12 版本发生变更: Syntactic support for generics and type aliases is new in version 3.12. Previously, generic classes had to explicitly inherit from `Generic` or contain a type variable in one of their bases.

User-defined generics for parameter expressions are also supported via parameter specification variables in the form `[**P]`. The behavior is consistent with type variables' described above as parameter specification variables are treated by the typing module as a specialized type variable. The one exception to this is that a list of types can be used to substitute a `ParamSpec`:

    
    
~~~shell
>>> class Z[T, **P]: ...  # T is a TypeVar; P is a ParamSpec
...
>>> Z[int, [dict, float]]
__main__.Z[int, [dict, float]]
~~~

Classes generic over a `ParamSpec` can also be created using explicit inheritance from `Generic`. In this case, `**` is not used:

    
    
~~~
from typing import ParamSpec, Generic

P = ParamSpec('P')

class Z(Generic[P]):
    ...
~~~

Another difference between `TypeVar` and `ParamSpec` is that a generic with only one parameter specification variable will accept parameter lists in the forms `X[[Type1, Type2, ...]]` and also `X[Type1, Type2, ...]` for aesthetic reasons. Internally, the latter is converted to the former, so the following are equivalent:

    
    
~~~shell
>>> class X[**P]: ...
...
>>> X[int, str]
__main__.X[[int, str]]
>>> X[[int, str]]
__main__.X[[int, str]]
~~~

Note that generics with `ParamSpec` may not have correct `__parameters__` after substitution in some cases because they are intended primarily for static type checking.

在 3.10 版本发生变更: `Generic` 现在可以通过参数表达式进行参数化。参见 `ParamSpec` 和 [**PEP 612**](https://peps.python.org/pep-0612/) 以了解更多细节。

用户定义的泛型类可以将 ABC 作为基类而不会导致元类冲突。 参数化泛型的输出结果会被缓存，且 typing 模块中的大多数类型都是 [hashable](../glossary.md#term-hashable) 并且支持相等性比较。

## `Any` 类型¶

`Any` 是一种特殊的类型。静态类型检查器认为所有类型均与 `Any` 兼容，同样，`Any` 也与所有类型兼容。

也就是说，可对 `Any` 类型的值执行任何操作或方法调用，并赋值给任意变量：

    
    
~~~
from typing import Any

a: Any = None
a = []          # OK
a = 2           # OK

s: str = ''
s = a           # OK

def foo(item: Any) -> int:
    # Passes type checking; 'item' could be any type,
    # and that type might have a 'bar' method
    item.bar()
    ...
~~~

注意，`Any` 类型的值赋给更精确的类型时，不执行类型检查。例如，把 `a` 赋给 `s`，在运行时，即便 `s` 已声明为 [`str`](stdtypes.md#str "str") 类型，但接收 [`int`](functions.md#int "int") 值时，静态类型检查器也不会报错。

此外，未指定返回值与参数类型的函数，都隐式地默认使用 `Any`：

    
    
~~~python
def legacy_parser(text):
    ...
    return data

# A static type checker will treat the above
# as having the same signature as:
def legacy_parser(text: Any) -> Any:
    ...
    return data
~~~

需要混用动态与静态类型代码时，此操作把 `Any` 当作 _应急出口_ 。

`Any` 和 [`object`](functions.md#object "object") 的区别。与 `Any` 相似，所有类型都是 [`object`](functions.md#object "object") 的子类型。然而，与 `Any` 不同，object 不可逆：[`object`](functions.md#object "object") _不是_ 其它类型的子类型。

就是说，值的类型是 [`object`](functions.md#object "object") 时，类型检查器几乎会拒绝所有对它的操作，并且，把它赋给更精确的类型变量（或返回值）属于类型错误。例如：

    
    
~~~python
def hash_a(item: object) -> int:
    # Fails type checking; an object does not have a 'magic' method.
    item.magic()
    ...

def hash_b(item: Any) -> int:
    # Passes type checking
    item.magic()
    ...

# Passes type checking, since ints and strs are subclasses of object
hash_a(42)
hash_a("foo")

# Passes type checking, since Any is compatible with all types
hash_b(42)
hash_b("foo")
~~~

使用 [`object`](functions.md#object "object")，说明值能以类型安全的方式转为任何类型。使用 `Any`，说明值是动态类型。

## 名义子类型 vs 结构子类型¶

最初 [**PEP 484**](https://peps.python.org/pep-0484/) 将 Python 静态类型系统定义为使用 _名义子类型_ 。这意味着当且仅当类 `A` 是 `B` 的子类时，才满足有类 `B` 预期时使用类 `A` 。

此项要求以前也适用于抽象基类，例如，[`Iterable`](collections.abc.md#collections.abc.Iterable "collections.abc.Iterable") 。这种方式的问题在于，定义类时必须显式说明，既不 Pythonic，也不是动态类型式 Python 代码的惯用写法。例如，下列代码就遵从了 [**PEP 484**](https://peps.python.org/pep-0484/) 的规范：

    
    
~~~
from collections.abc import Sized, Iterable, Iterator

class Bucket(Sized, Iterable[int]):
    ...
    def __len__(self) -> int: ...
    def __iter__(self) -> Iterator[int]: ...
~~~

[**PEP 544**](https://peps.python.org/pep-0544/) 允许用户在类定义时不显式说明基类，从而解决了这一问题，静态类型检查器隐式认为 `Bucket` 既是 `Sized` 的子类型，又是 `Iterable[int]` 的子类型。这就是 _结构子类型_ （又称为静态鸭子类型）：

    
    
~~~
from collections.abc import Iterator, Iterable

class Bucket:  # Note: no base classes
    ...
    def __len__(self) -> int: ...
    def __iter__(self) -> Iterator[int]: ...

def collect(items: Iterable[int]) -> int: ...
result = collect(Bucket())  # Passes type check
~~~

此外，结构子类型的优势在于，通过继承特殊类 `Protocol` ，用户可以定义新的自定义协议（见下文中的例子）。

## 模块内容¶

The `typing` module defines the following classes, functions and decorators.

### 特殊类型原语¶

#### 特殊类型¶

These can be used as types in annotations. They do not support subscription using `[]`.

typing.Any¶

    

~~~
不受限的特殊类型。

  * 所有类型都与 `Any` 兼容。

  * `Any` 与所有类型都兼容。

在 3.11 版本发生变更: `Any` 现在可以用作基类。这有助于避免类型检查器在高度动态或可通过鸭子类型使用的类上报错。

typing.AnyStr¶
~~~
    

~~~
A constrained type variable.

Definition:
~~~
    
    
~~~
AnyStr = TypeVar('AnyStr', str, bytes)
~~~

`AnyStr` is meant to be used for functions that may accept [`str`](stdtypes.md#str "str") or [`bytes`](stdtypes.md#bytes "bytes") arguments but cannot allow the two to mix.

例如：

    
    
~~~python
def concat(a: AnyStr, b: AnyStr) -> AnyStr:
    return a + b

concat("foo", "bar")    # OK, output has type 'str'
concat(b"foo", b"bar")  # OK, output has type 'bytes'
concat("foo", b"bar")   # Error, cannot mix str and bytes
~~~

Note that, despite its name, `AnyStr` has nothing to do with the `Any` type, nor does it mean "any string". In particular, `AnyStr` and `str | bytes` are different from each other and have different use cases:

    
    
~~~
# Invalid use of AnyStr:
# The type variable is used only once in the function signature,
# so cannot be "solved" by the type checker
def greet_bad(cond: bool) -> AnyStr:
    return "hi there!" if cond else b"greetings!"

# The better way of annotating this function:
def greet_proper(cond: bool) -> str | bytes:
    return "hi there!" if cond else b"greetings!"
~~~

从 3.13 版起不建议使用，将在 3.18 版中移除: Deprecated in favor of the new [type parameter syntax](../reference/compound_stmts.md#type-params). Use `class A[T: (str, bytes)]: ...` instead of importing `AnyStr`. See [**PEP 695**](https://peps.python.org/pep-0695/) for more details.

In Python 3.16, `AnyStr` will be removed from `typing.__all__`, and deprecation warnings will be emitted at runtime when it is accessed or imported from `typing`. `AnyStr` will be removed from `typing` in Python 3.18.

typing.LiteralString¶

    

~~~
Special type that includes only literal strings.

Any string literal is compatible with `LiteralString`, as is another `LiteralString`. However, an object typed as just `str` is not. A string created by composing `LiteralString`-typed objects is also acceptable as a `LiteralString`.

示例:
~~~
    
    
~~~python
def run_query(sql: LiteralString) -> None:
    ...

def caller(arbitrary_string: str, literal_string: LiteralString) -> None:
    run_query("SELECT * FROM students")  # OK
    run_query(literal_string)  # OK
    run_query("SELECT * FROM " + literal_string)  # OK
    run_query(arbitrary_string)  # type checker error
    run_query(  # type checker error
        f"SELECT * FROM students WHERE name = {arbitrary_string}"
    )
~~~

`LiteralString` is useful for sensitive APIs where arbitrary user-generated strings could generate problems. For example, the two cases above that generate type checker errors could be vulnerable to an SQL injection attack.

See [**PEP 675**](https://peps.python.org/pep-0675/) for more details.

在 3.11 版本加入.

typing.Never¶

    

~~~
[底类型](https://en.wikipedia.org/wiki/Bottom_type)，一个没有成员的类型。

这可以用于定义一个永不应该被调用的函数，或一个永不返回的函数：
~~~
    
    
~~~
from typing import Never

def never_call_me(arg: Never) -> None:
    pass

def int_or_str(arg: int | str) -> None:
    never_call_me(arg)  # type checker error
    match arg:
        case int():
            print("It's an int")
        case str():
            print("It's a str")
        case _:
            never_call_me(arg)  # OK, arg is of type Never
~~~

在 3.11 版本加入: 在更老的 Python 版本上，`NoReturn` 可被用于表达相同的概念。 `Never` 为了更显式地表达这个意图被加入。

typing.NoReturn¶

    

~~~
Special type indicating that a function never returns.

例如：
~~~
    
    
~~~
from typing import NoReturn

def stop() -> NoReturn:
    raise RuntimeError('no way')
~~~

`NoReturn` 也可以用于 [底类型](https://en.wikipedia.org/wiki/Bottom_type) 的定义，这是一种没有值的类型。自从 Python 3.11 开始，应该使用 `Never` 类型代替这个概念。类型检查器应该将这两种类型视为等价。

在 3.5.4 版本加入.

在 3.6.2 版本加入.

typing.Self¶

    

~~~
Special type to represent the current enclosed class.

例如：
~~~
    
    
~~~
from typing import Self, reveal_type

class Foo:
    def return_self(self) -> Self:
        ...
        return self

class SubclassOfFoo(Foo): pass

reveal_type(Foo().return_self())  # Revealed type is "Foo"
reveal_type(SubclassOfFoo().return_self())  # Revealed type is "SubclassOfFoo"
~~~

此标准在语法上等价于以下代码，但形式更为简洁:

    
    
~~~
from typing import TypeVar

Self = TypeVar("Self", bound="Foo")

class Foo:
    def return_self(self: Self) -> Self:
        ...
        return self
~~~

In general, if something returns `self`, as in the above examples, you should use `Self` as the return annotation. If `Foo.return_self` was annotated as returning `"Foo"`, then the type checker would infer the object returned from `SubclassOfFoo.return_self` as being of type `Foo` rather than `SubclassOfFoo`.

其它常见用例包括：

  * 被用作替代构造器的 [`classmethod`](functions.md#classmethod "classmethod")，它将返回 `cls` 形参的实例。

  * 标注一个返回自身的 [`__enter__()`](../reference/datamodel.md#object.__enter__ "object.__enter__") 方法。

You should not use `Self` as the return annotation if the method is not guaranteed to return an instance of a subclass when the class is subclassed:

    
    
~~~
class Eggs:
    # Self would be an incorrect return annotation here,
    # as the object returned is always an instance of Eggs,
    # even in subclasses
    def returns_eggs(self) -> "Eggs":
        return Eggs()
~~~

更多细节请参见 [**PEP 673**](https://peps.python.org/pep-0673/)。

在 3.11 版本加入.

typing.TypeAlias¶

    

~~~
Special annotation for explicitly declaring a type alias.

例如：
~~~
    
    
~~~
from typing import TypeAlias

Factors: TypeAlias = list[int]
~~~

`TypeAlias` is particularly useful on older Python versions for annotating aliases that make use of forward references, as it can be hard for type checkers to distinguish these from normal variable assignments:

    
    
~~~
from typing import Generic, TypeAlias, TypeVar

T = TypeVar("T")

# "Box" does not exist yet,
# so we have to use quotes for the forward reference on Python <3.12.
# Using ``TypeAlias`` tells the type checker that this is a type alias declaration,
# not a variable assignment to a string.
BoxOfStrings: TypeAlias = "Box[str]"

class Box(Generic[T]):
    @classmethod
    def make_box_of_strings(cls) -> BoxOfStrings: ...
~~~

请参阅 [**PEP 613**](https://peps.python.org/pep-0613/) 了解详情。

在 3.10 版本加入.

自 3.12 版本弃用: `TypeAlias` is deprecated in favor of the [`type`](../reference/simple_stmts.md#type) statement, which creates instances of `TypeAliasType` and which natively supports forward references. Note that while `TypeAlias` and `TypeAliasType` serve similar purposes and have similar names, they are distinct and the latter is not the type of the former. Removal of `TypeAlias` is not currently planned, but users are encouraged to migrate to [`type`](../reference/simple_stmts.md#type) statements.

#### 特殊形式¶

These can be used as types in annotations. They all support subscription using `[]`, but each has a unique syntax.

typing.Union¶

    

~~~
联合类型； `Union[X, Y]` 等价于 `X | Y` ，意味着满足 X 或 Y 之一。

要定义一个联合类型，可以使用类似 `Union[int, str]` 或简写 `int | str`。建议使用这种简写。细节:

  * 参数必须是某种类型，且至少有一个。

  * 联合类型之联合类型会被展平，例如：
    
        Union[Union[int, str], float] == Union[int, str, float]
~~~

  * 单参数之联合类型就是该参数自身，例如：
    
        Union[int] == int  # The constructor actually returns int
    

  * 冗余的参数会被跳过，例如：
    
        Union[int, str, int] == Union[int, str] == int | str
    

  * 比较联合类型，不涉及参数顺序，例如：
    
        Union[int, str] == Union[str, int]
    

  * 不可创建 `Union` 的子类或实例。

  * 没有 `Union[X][Y]` 这种写法。

在 3.7 版本发生变更: 在运行时，不要移除联合类型中的显式子类。

在 3.10 版本发生变更: 联合类型现在可以写成 `X | Y`。 参见 [联合类型表达式](stdtypes.md#types-union)。

typing.Optional¶

    

~~~
`Optional[X]` 等价于 `X | None` （或 `Union[X, None]` ） 。

注意，可选类型与含默认值的可选参数不同。含默认值的可选参数不需要在类型注解上添加 `Optional` 限定符，因为它仅是可选的。例如：
~~~
    
    
~~~python
def foo(arg: int = 0) -> None:
    ...
~~~

另一方面，显式应用 `None` 值时，不管该参数是否可选， `Optional` 都适用。例如：

    
    
~~~python
def foo(arg: Optional[int] = None) -> None:
    ...
~~~

在 3.10 版本发生变更: 可选参数现在可以写成 `X | None`。 参见 [联合类型表达式](stdtypes.md#types-union)。

typing.Concatenate¶

    

~~~
Special form for annotating higher-order functions.

`Concatenate` can be used in conjunction with Callable and `ParamSpec` to annotate a higher-order callable which adds, removes, or transforms parameters of another callable. Usage is in the form `Concatenate[Arg1Type, Arg2Type, ..., ParamSpecVariable]`. `Concatenate` is currently only valid when used as the first argument to a Callable. The last parameter to `Concatenate` must be a `ParamSpec` or ellipsis (`...`).

例如，为了注释一个装饰器 `with_lock`，它为被装饰的函数提供了 [`threading.Lock`](threading.md#threading.Lock "threading.Lock")，`Concatenate` 可以用来表示 `with_lock` 期望一个可调用对象，该对象接收一个 `Lock` 作为第一个参数，并返回一个具有不同类型签名的可调用对象。 在这种情况下，`ParamSpec` 表示返回的可调用对象的参数类型取决于被传入的可调用程序的参数类型:
~~~
    
    
~~~
from collections.abc import Callable
from threading import Lock
from typing import Concatenate, ParamSpec, TypeVar

P = ParamSpec('P')
R = TypeVar('R')

# Use this lock to ensure that only one thread is executing a function
# at any time.
my_lock = Lock()

def with_lock(f: Callable[Concatenate[Lock, P], R]) -> Callable[P, R]:
    '''A type-safe decorator which provides a lock.'''
    def inner(*args: P.args, **kwargs: P.kwargs) -> R:
        # Provide the lock as the first argument.
        return f(my_lock, *args, **kwargs)
    return inner

@with_lock
def sum_threadsafe(lock: Lock, numbers: list[float]) -> float:
    '''Add a list of numbers together in a thread-safe manner.'''
    with lock:
        return sum(numbers)

# We don't need to pass in the lock ourselves thanks to the decorator.
sum_threadsafe([1.1, 2.2, 3.3])
~~~

在 3.10 版本加入.

参见

  * [**PEP 612**](https://peps.python.org/pep-0612/) \-- Parameter Specification Variables (the PEP which introduced `ParamSpec` and `Concatenate`)

  * `ParamSpec`

  * 标注可调用对象

typing.Literal¶

    

~~~
Special typing form to define "literal types".

`Literal` can be used to indicate to type checkers that the annotated object has a value equivalent to one of the provided literals.

例如：
~~~
    
    
~~~python
def validate_simple(data: Any) -> Literal[True]:  # always returns True
    ...

type Mode = Literal['r', 'rb', 'w', 'wb']
def open_helper(file: str, mode: Mode) -> str:
    ...

open_helper('/some/path', 'r')      # Passes type check
open_helper('/other/path', 'typo')  # Error in type checker
~~~

`Literal[...]` 不能创建子类。在运行时，任意值均可作为 `Literal[...]` 的类型参数，但类型检查器可以对此加以限制。字面量类型详见 [**PEP 586**](https://peps.python.org/pep-0586/) 。

在 3.8 版本加入.

在 3.9.1 版本发生变更: `Literal` 现在能去除形参的重复。 `Literal` 对象的相等性比较不再依赖顺序。 现在如果有某个参数不为 [hashable](../glossary.md#term-hashable)，`Literal` 对象在相等性比较期间将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

typing.ClassVar¶

    

~~~
标记类变量的特殊类型构造器。

如 [**PEP 526**](https://peps.python.org/pep-0526/) 所述，打包在 ClassVar 内的变量注解是指，给定属性应当用作类变量，而不应设置在类实例上。用法如下：
~~~
    
    
~~~
class Starship:
    stats: ClassVar[dict[str, int]] = {} # class variable
    damage: int = 10                     # instance variable
~~~

`ClassVar` 仅接受类型，也不能使用下标。

`ClassVar` 本身不是类，不应用于 [`isinstance()`](functions.md#isinstance "isinstance") 或 [`issubclass()`](functions.md#issubclass "issubclass")。`ClassVar` 不改变 Python 运行时行为，但可以用于第三方类型检查器。例如，类型检查器会认为以下代码有错：

    
    
~~~
enterprise_d = Starship(3000)
enterprise_d.stats = {} # Error, setting class variable on instance
Starship.stats = {}     # This is OK
~~~

在 3.5.3 版本加入.

typing.Final¶

    

~~~
Special typing construct to indicate final names to type checkers.

Final names cannot be reassigned in any scope. Final names declared in class scopes cannot be overridden in subclasses.

例如：
~~~
    
    
~~~
MAX_SIZE: Final = 9000
MAX_SIZE += 1  # Error reported by type checker

class Connection:
    TIMEOUT: Final[int] = 10

class FastConnector(Connection):
    TIMEOUT = 1  # Error reported by type checker
~~~

这些属性没有运行时检查。详见 [**PEP 591**](https://peps.python.org/pep-0591/)。

在 3.8 版本加入.

typing.Required¶

    

~~~
Special typing construct to mark a `TypedDict` key as required.

This is mainly useful for `total=False` TypedDicts. See `TypedDict` and [**PEP 655**](https://peps.python.org/pep-0655/) for more details.

在 3.11 版本加入.

typing.NotRequired¶
~~~
    

~~~
Special typing construct to mark a `TypedDict` key as potentially missing.

详情参见 `TypedDict` 和 [**PEP 655**](https://peps.python.org/pep-0655/)。

在 3.11 版本加入.

typing.Annotated¶
~~~
    

~~~
Special typing form to add context-specific metadata to an annotation.

Add metadata `x` to a given type `T` by using the annotation `Annotated[T, x]`. Metadata added using `Annotated` can be used by static analysis tools or at runtime. At runtime, the metadata is stored in a `__metadata__` attribute.

If a library or tool encounters an annotation `Annotated[T, x]` and has no special logic for the metadata, it should ignore the metadata and simply treat the annotation as `T`. As such, `Annotated` can be useful for code that wants to use annotations for purposes outside Python's static typing system.

Using `Annotated[T, x]` as an annotation still allows for static typechecking of `T`, as type checkers will simply ignore the metadata `x`. In this way, `Annotated` differs from the `@no_type_check` decorator, which can also be used for adding annotations outside the scope of the typing system, but completely disables typechecking for a function or class.

The responsibility of how to interpret the metadata lies with the the tool or library encountering an `Annotated` annotation. A tool or library encountering an `Annotated` type can scan through the metadata elements to determine if they are of interest (e.g., using [`isinstance()`](functions.md#isinstance "isinstance")).

Annotated[<type>, <metadata>]
~~~
    

~~~
Here is an example of how you might use `Annotated` to add metadata to type annotations if you were doing range analysis:
~~~
    
    
~~~
@dataclass
class ValueRange:
    lo: int
    hi: int

T1 = Annotated[int, ValueRange(-10, 5)]
T2 = Annotated[T1, ValueRange(-20, 3)]
~~~

Details of the syntax:

  * `Annotated` 的第一个参数必须是有效类型。

  * Multiple metadata elements can be supplied (`Annotated` supports variadic arguments):
    
        @dataclass
    class ctype:
        kind: str
    
    Annotated[int, ValueRange(3, 10), ctype("char")]
    

It is up to the tool consuming the annotations to decide whether the client is allowed to add multiple metadata elements to one annotation and how to merge those annotations.

  * `Annotated` must be subscripted with at least two arguments ( `Annotated[int]` is not valid)

  * The order of the metadata elements is preserved and matters for equality checks:
    
        assert Annotated[int, ValueRange(3, 10), ctype("char")] != Annotated[
        int, ctype("char"), ValueRange(3, 10)
    ]
    

  * Nested `Annotated` types are flattened. The order of the metadata elements starts with the innermost annotation:
    
        assert Annotated[Annotated[int, ValueRange(3, 10)], ctype("char")] == Annotated[
        int, ValueRange(3, 10), ctype("char")
    ]
    

  * Duplicated metadata elements are not removed:
    
        assert Annotated[int, ValueRange(3, 10)] != Annotated[
        int, ValueRange(3, 10), ValueRange(3, 10)
    ]
    

  * `Annotated` can be used with nested and generic aliases:
    
        @dataclass
    class MaxLen:
        value: int
    
    type Vec[T] = Annotated[list[tuple[T, T]], MaxLen(10)]
    
    # When used in a type annotation, a type checker will treat "V" the same as
    # ``Annotated[list[tuple[int, int]], MaxLen(10)]``:
    type V = Vec[int]
    

  * `Annotated` cannot be used with an unpacked `TypeVarTuple`:
    
        type Variadic[*Ts] = Annotated[*Ts, Ann1]  # NOT valid
    

This would be equivalent to:

    
        Annotated[T1, T2, T3, ..., Ann1]
    

where `T1`, `T2`, etc. are `TypeVars`. This would be invalid: only one type should be passed to Annotated.

  * By default, `get_type_hints()` strips the metadata from annotations. Pass `include_extras=True` to have the metadata preserved:
    
        >>> from typing import Annotated, get_type_hints
    >>> def func(x: Annotated[int, "metadata"]) -> None: pass
    ...
    >>> get_type_hints(func)
    {'x': <class 'int'>, 'return': <class 'NoneType'>}
    >>> get_type_hints(func, include_extras=True)
    {'x': typing.Annotated[int, 'metadata'], 'return': <class 'NoneType'>}
    

  * At runtime, the metadata associated with an `Annotated` type can be retrieved via the `__metadata__` attribute:
    
        >>> from typing import Annotated
    >>> X = Annotated[int, "very", "important", "metadata"]
    >>> X
    typing.Annotated[int, 'very', 'important', 'metadata']
    >>> X.__metadata__
    ('very', 'important', 'metadata')
    

参见

[**PEP 593**](https://peps.python.org/pep-0593/) \- Flexible function and variable annotations

    

~~~
The PEP introducing `Annotated` to the standard library.

在 3.9 版本加入.

typing.TypeGuard¶
~~~
    

~~~
Special typing construct for marking user-defined type guard functions.

`TypeGuard` can be used to annotate the return type of a user-defined type guard function. `TypeGuard` only accepts a single type argument. At runtime, functions marked this way should return a boolean.

PX旨在使 _类型缩小_ 受益--这是静态类型检查器用来确定程序代码流中表达式的更精确类型的一种技术。通常，类型缩小是通过分析条件性代码流并将缩小的结果应用于一个代码块来完成的。 这里的条件表达式有时被称为 "类型保护":
~~~
    
    
~~~python
def is_str(val: str | float):
    # "isinstance" type guard
    if isinstance(val, str):
        # Type of ``val`` is narrowed to ``str``
        ...
    else:
        # Else, type of ``val`` is narrowed to ``float``.
        ...
~~~

有时，使用一个用户定义的布尔函数作为类型保护会很方便。 这样的函数应该使用 `TypeGuard[...]` 作为其返回类型，以提醒静态类型检查器注意这一意图。

对于一个给定的函数，使用 `-> TypeGuard` 告诉静态类型检查器:

  1. 返回值是一个布尔值。

  2. 如果返回值是 `True`，其参数的类型是 `TypeGuard` 里面的类型。

例如：

    
    
~~~python
def is_str_list(val: list[object]) -> TypeGuard[list[str]]:
    '''Determines whether all objects in the list are strings'''
    return all(isinstance(x, str) for x in val)

def func1(val: list[object]):
    if is_str_list(val):
        # Type of ``val`` is narrowed to ``list[str]``.
        print(" ".join(val))
    else:
        # Type of ``val`` remains as ``list[object]``.
        print("Not a list of strings!")
~~~

如果 `is_str_list` 是一个类或实例方法，那么 `TypeGuard` 中的类型映射到 `cls` 或 `self` 之后的第二个参数的类型。

简而言之，`def foo(arg: TypeA) -> TypeGuard[TypeB]: ...` 形式的意思是：如果 `foo(arg)` 返回 `True`，那么 `arg` 将把 `TypeA` 缩小为 `TypeB`。

备注

`TypeB` 无需为 `TypeA` 的缩小形式 -- 它甚至可以是扩大形式。 主要原因是允许像把 `list[object]` 缩小到 `list[str]` 这样的事情，即使后者不是前者的一个子类型，因为 `list` 是不变的。 编写类型安全的类型防护的责任留给了用户。

`TypeGuard` 也适用于类型变量。 详情参见 [**PEP 647**](https://peps.python.org/pep-0647/)。

在 3.10 版本加入.

typing.Unpack¶

    

~~~
Typing operator to conceptually mark an object as having been unpacked.

For example, using the unpack operator `*` on a `type variable tuple` is equivalent to using `Unpack` to mark the type variable tuple as having been unpacked:
~~~
    
    
~~~
Ts = TypeVarTuple('Ts')
tup: tuple[*Ts]
# Effectively does:
tup: tuple[Unpack[Ts]]
~~~

In fact, `Unpack` can be used interchangeably with `*` in the context of `typing.TypeVarTuple` and [`builtins.tuple`](stdtypes.md#tuple "tuple") types. You might see `Unpack` being used explicitly in older versions of Python, where `*` couldn't be used in certain places:

    
    
~~~
# In older versions of Python, TypeVarTuple and Unpack
# are located in the `typing_extensions` backports package.
from typing_extensions import TypeVarTuple, Unpack

Ts = TypeVarTuple('Ts')
tup: tuple[*Ts]         # Syntax error on Python <= 3.10!
tup: tuple[Unpack[Ts]]  # Semantically equivalent, and backwards-compatible
~~~

`Unpack` can also be used along with `typing.TypedDict` for typing `**kwargs` in a function signature:

    
    
~~~
from typing import TypedDict, Unpack

class Movie(TypedDict):
    name: str
    year: int

# This function expects two keyword arguments - `name` of type `str`
# and `year` of type `int`.
def foo(**kwargs: Unpack[Movie]): ...
~~~

See [**PEP 692**](https://peps.python.org/pep-0692/) for more details on using `Unpack` for `**kwargs` typing.

在 3.11 版本加入.

#### Building generic types and type aliases¶

The following classes should not be used directly as annotations. Their intended purpose is to be building blocks for creating generic types and type aliases.

These objects can be created through special syntax ([type parameter lists](../reference/compound_stmts.md#type-params) and the [`type`](../reference/simple_stmts.md#type) statement). For compatibility with Python 3.11 and earlier, they can also be created without the dedicated syntax, as documented below.

_class _typing.Generic¶

    

~~~
用于泛型类型的抽象基类。

A generic type is typically declared by adding a list of type parameters after the class name:
~~~
    
    
~~~
class Mapping[KT, VT]:
    def __getitem__(self, key: KT) -> VT:
        ...
        # Etc.
~~~

Such a class implicitly inherits from `Generic`. The runtime semantics of this syntax are discussed in the [Language Reference](../reference/compound_stmts.md#generic-classes).

该类的用法如下：

    
    
~~~python
def lookup_name[X, Y](mapping: Mapping[X, Y], key: X, default: Y) -> Y:
    try:
        return mapping[key]
    except KeyError:
        return default
~~~

Here the brackets after the function name indicate a [generic function](../reference/compound_stmts.md#generic-functions).

For backwards compatibility, generic classes can also be declared by explicitly inheriting from `Generic`. In this case, the type parameters must be declared separately:

    
    
~~~
KT = TypeVar('KT')
VT = TypeVar('VT')

class Mapping(Generic[KT, VT]):
    def __getitem__(self, key: KT) -> VT:
        ...
        # Etc.
~~~

_class _typing.TypeVar( _name_ , _* constraints_, _bound =None_, _covariant =False_, _contravariant =False_, _infer_variance =False_)¶

    

~~~
类型变量。

The preferred way to construct a type variable is via the dedicated syntax for [generic functions](../reference/compound_stmts.md#generic-functions), [generic classes](../reference/compound_stmts.md#generic-classes), and [generic type aliases](../reference/compound_stmts.md#generic-type-aliases):
~~~
    
    
~~~
class Sequence[T]:  # T is a TypeVar
    ...
~~~

This syntax can also be used to create bound and constrained type variables:

    
    
~~~
class StrSequence[S: str]:  # S is a TypeVar bound to str
    ...
~~~
    
    class StrOrBytesSequence[A: (str, bytes)]:  # A is a TypeVar constrained to str or bytes
        ...
    

However, if desired, reusable type variables can also be constructed manually, like so:

    
    
~~~
T = TypeVar('T')  # Can be anything
S = TypeVar('S', bound=str)  # Can be any subtype of str
A = TypeVar('A', str, bytes)  # Must be exactly str or bytes
~~~

Type variables exist primarily for the benefit of static type checkers. They serve as the parameters for generic types as well as for generic function and type alias definitions. See `Generic` for more information on generic types. Generic functions work as follows:

    
    
~~~python
def repeat[T](x: T, n: int) -> Sequence[T]:
    """Return a list containing n references to x."""
    return [x]*n
~~~
    
    def print_capitalized[S: str](x: S) -> S:
        """Print x capitalized, and return x."""
        print(x.capitalize())
        return x
    
    
    def concatenate[A: (str, bytes)](x: A, y: A) -> A:
        """Add two strings or bytes objects together."""
        return x + y
    

请注意，类型变量可以是 _被绑定的_ ， _被约束的_ ，或者两者都不是，但不能既是被绑定的 _又是_ 被约束的。

The variance of type variables is inferred by type checkers when they are created through the [type parameter syntax](../reference/compound_stmts.md#type-params) or when `infer_variance=True` is passed. Manually created type variables may be explicitly marked covariant or contravariant by passing `covariant=True` or `contravariant=True`. By default, manually created type variables are invariant. See [**PEP 484**](https://peps.python.org/pep-0484/) and [**PEP 695**](https://peps.python.org/pep-0695/) for more details.

绑定类型变量和约束类型变量在几个重要方面具有不同的主义。 使用 _绑定_ 类型变量意味着 `TypeVar` 将尽可能使用最为专属的类型来解析:

    
    
~~~
x = print_capitalized('a string')
reveal_type(x)  # revealed type is str

class StringSubclass(str):
    pass

y = print_capitalized(StringSubclass('another string'))
reveal_type(y)  # revealed type is StringSubclass

z = print_capitalized(45)  # error: int is not a subtype of str
~~~

类型变量可以被绑定到具体类型、抽象类型（ ABC 或 protocol ），甚至是类型的联合:

    
    
~~~
# Can be anything with an __abs__ method
def print_abs[T: SupportsAbs](arg: T) -> None:
    print("Absolute value:", abs(arg))

U = TypeVar('U', bound=str|bytes)  # Can be any subtype of the union str|bytes
V = TypeVar('V', bound=SupportsAbs)  # Can be anything with an __abs__ method
~~~

但是，如果使用 _约束_ 类型变量，则意味着 `TypeVar` 只能被解析为恰好是给定的约束之一:

    
    
~~~
a = concatenate('one', 'two')
reveal_type(a)  # revealed type is str

b = concatenate(StringSubclass('one'), StringSubclass('two'))
reveal_type(b)  # revealed type is str, despite StringSubclass being passed in

c = concatenate('one', b'two')  # error: type variable 'A' can be either str or bytes in a function call, but not both
~~~

At runtime, `isinstance(x, T)` will raise [`TypeError`](exceptions.md#TypeError "TypeError").

__name__¶

    

~~~
The name of the type variable.

__covariant__¶
~~~
    

~~~
Whether the type var has been explicitly marked as covariant.

__contravariant__¶
~~~
    

~~~
Whether the type var has been explicitly marked as contravariant.

__infer_variance__¶
~~~
    

~~~
Whether the type variable's variance should be inferred by type checkers.

在 3.12 版本加入.

__bound__¶
~~~
    

~~~
The bound of the type variable, if any.

在 3.12 版本发生变更: For type variables created through [type parameter syntax](../reference/compound_stmts.md#type-params), the bound is evaluated only when the attribute is accessed, not when the type variable is created (see [惰性求值](../reference/executionmodel.md#lazy-evaluation)).

__constraints__¶
~~~
    

~~~
A tuple containing the constraints of the type variable, if any.

在 3.12 版本发生变更: For type variables created through [type parameter syntax](../reference/compound_stmts.md#type-params), the constraints are evaluated only when the attribute is accessed, not when the type variable is created (see [惰性求值](../reference/executionmodel.md#lazy-evaluation)).

在 3.12 版本发生变更: Type variables can now be declared using the [type parameter](../reference/compound_stmts.md#type-params) syntax introduced by [**PEP 695**](https://peps.python.org/pep-0695/). The `infer_variance` parameter was added.

_class _typing.TypeVarTuple( _name_ )¶
~~~
    

~~~
类型变量元组。 一种启用了 _variadic_ 泛型的专属 `类型变量` 形式。

Type variable tuples can be declared in [type parameter lists](../reference/compound_stmts.md#type-params) using a single asterisk (`*`) before the name:
~~~
    
    
~~~python
def move_first_element_to_last[T, *Ts](tup: tuple[T, *Ts]) -> tuple[*Ts, T]:
    return (*tup[1:], tup[0])
~~~

Or by explicitly invoking the `TypeVarTuple` constructor:

    
    
~~~
T = TypeVar("T")
Ts = TypeVarTuple("Ts")

def move_first_element_to_last(tup: tuple[T, *Ts]) -> tuple[*Ts, T]:
    return (*tup[1:], tup[0])
~~~

一个普通类型变量将启用单个类型的形参化。 作为对比，一个类型变量元组通过将 _任意_ 数量的类型变量封包在一个元组中来允许 _任意_ 数量类型的形参化。 例如:

    
    
~~~
# T is bound to int, Ts is bound to ()
# Return value is (1,), which has type tuple[int]
move_first_element_to_last(tup=(1,))

# T is bound to int, Ts is bound to (str,)
# Return value is ('spam', 1), which has type tuple[str, int]
move_first_element_to_last(tup=(1, 'spam'))

# T is bound to int, Ts is bound to (str, float)
# Return value is ('spam', 3.0, 1), which has type tuple[str, float, int]
move_first_element_to_last(tup=(1, 'spam', 3.0))

# This fails to type check (and fails at runtime)
# because tuple[()] is not compatible with tuple[T, *Ts]
# (at least one element is required)
move_first_element_to_last(tup=())
~~~

请注意解包运算符 `*` 在 `tuple[T, *Ts]` 中的使用。 在概念上，你可以将 `Ts` 当作一个由类型变量组成的元组 `(T1, T2, ...)`。 那么 `tuple[T, *Ts]` 就将变为 `tuple[T, *(T1, T2, ...)]`，这等价于 `tuple[T, T1, T2, ...]`。 （请注意在旧版本 Python 中，你可能会看到改用 `Unpack` 的写法，如 `Unpack[Ts]`。）

类型变量元组 _总是_ 要被解包。 这有助于区分类型变量元组和普通类型变量:

    
    
~~~
x: Ts          # Not valid
x: tuple[Ts]   # Not valid
x: tuple[*Ts]  # The correct way to do it
~~~

类型变量元组可被用在与普通类型变量相同的上下文中。 例如，在类定义、参数和返回类型中:

    
    
~~~
class Array[*Shape]:
    def __getitem__(self, key: tuple[*Shape]) -> float: ...
    def __abs__(self) -> "Array[*Shape]": ...
    def get_shape(self) -> tuple[*Shape]: ...
~~~

Type variable tuples can be happily combined with normal type variables:

    
    
~~~
class Array[DType, *Shape]:  # This is fine
    pass

class Array2[*Shape, DType]:  # This would also be fine
    pass

class Height: ...
class Width: ...

float_array_1d: Array[float, Height] = Array()     # Totally fine
int_array_2d: Array[int, Height, Width] = Array()  # Yup, fine too
~~~

但是，请注意在一个类型参数或类型形参列表中最多只能有一个类型变量元组:

    
    
~~~
x: tuple[*Ts, *Ts]            # Not valid
class Array[*Shape, *Shape]:  # Not valid
    pass
~~~

最后，一个已解包的类型变量元组可以被用作 `*args` 的类型标注:

    
    
~~~python
def call_soon[*Ts](
         callback: Callable[[*Ts], None],
         *args: *Ts
) -> None:
    ...
    callback(*args)
~~~

相比非解包的 `*args` 标注 —— 例如 `*args: int`，它将指明 _所有_ 参数均为 `int` —— `*args: *Ts` 启用了对 `*args` 中 _单个_ 参数的类型的引用。 在此，这允许我们确保传入 `call_soon` 的 `*args` 的类型与 `callback` 的（位置）参数的类型相匹配。

关于类型变量元组的更多细节，请参见 [**PEP 646**](https://peps.python.org/pep-0646/)。

__name__¶

    

~~~
The name of the type variable tuple.

在 3.11 版本加入.

在 3.12 版本发生变更: Type variable tuples can now be declared using the [type parameter](../reference/compound_stmts.md#type-params) syntax introduced by [**PEP 695**](https://peps.python.org/pep-0695/).

_class _typing.ParamSpec( _name_ , _*_ , _bound =None_, _covariant =False_, _contravariant =False_)¶
~~~
    

~~~
参数规范变量。 `类型变量` 的一个专门版本。

In [type parameter lists](../reference/compound_stmts.md#type-params), parameter specifications can be declared with two asterisks (`**`):
~~~
    
    
~~~
type IntFunc[**P] = Callable[P, int]
~~~

For compatibility with Python 3.11 and earlier, `ParamSpec` objects can also be created as follows:

    
    
~~~
P = ParamSpec('P')
~~~

参数规范变量的存在主要是为了使静态类型检查器受益。 它们被用来将一个可调用对象的参数类型转发给另一个可调用对象的参数类型——这种模式通常出现在高阶函数和装饰器中。 它们只有在 `Concatenate` 中使用时才有效，或者作为 `Callable` 的第一个参数，或者作为用户定义的泛型的参数。 参见 `Generic` 以了解更多关于泛型的信息。

例如，为了给一个函数添加基本的日志记录，我们可以创建一个装饰器 `add_logging` 来记录函数调用。 参数规范变量告诉类型检查器，传入装饰器的可调用对象和由其返回的新可调用对象有相互依赖的类型参数:

    
    
~~~
from collections.abc import Callable
import logging

def add_logging[T, **P](f: Callable[P, T]) -> Callable[P, T]:
    '''A type-safe decorator to add logging to a function.'''
    def inner(*args: P.args, **kwargs: P.kwargs) -> T:
        logging.info(f'{f.__name__} was called')
        return f(*args, **kwargs)
    return inner

@add_logging
def add_two(x: float, y: float) -> float:
    '''Add two numbers together.'''
    return x + y
~~~

如果没有 `ParamSpec`，以前注释这个的最简单的方法是使用一个 `TypeVar` 与绑定 `Callable[..., Any]`。

  1. 类型检查器不能对 `inner` 函数进行类型检查，因为 `*args` 和 `**kwargs` 的类型必须是 `Any`。

  2. `cast()` 在返回 `inner` 函数时，可能需要在 `add_logging` 装饰器的主体中进行，或者必须告诉静态类型检查器忽略 `return inner`。

args¶

    

~~~
kwargs¶
~~~
    

~~~
由于 `ParamSpec` 同时捕获了位置参数和关键字参数，`P.args` 和 `P.kwargs` 可以用来将 `ParamSpec` 分割成其组成部分。 `P.args` 代表给定调用中的位置参数的元组，只能用于注释 `*args`。 `P.kwargs` 代表给定调用中的关键字参数到其值的映射，只能用于注释 `**kwargs`。在运行时，`P.args` 和 `P.kwargs` 分别是 `ParamSpecArgs` 和 `ParamSpecKwargs` 的实例。

__name__¶
~~~
    

~~~
The name of the parameter specification.

用 `covariant=True` 或 `contravariant=True` 创建的参数规范变量可以用来声明协变或逆变泛型类型。 参数 `bound` 也被接受，类似于 `TypeVar`。 然而这些关键字的实际语义还有待决定。

在 3.10 版本加入.

在 3.12 版本发生变更: Parameter specifications can now be declared using the [type parameter](../reference/compound_stmts.md#type-params) syntax introduced by [**PEP 695**](https://peps.python.org/pep-0695/).

备注

只有在全局范围内定义的参数规范变量可以被 pickle。

参见

  * [**PEP 612**](https://peps.python.org/pep-0612/) \-- Parameter Specification Variables (the PEP which introduced `ParamSpec` and `Concatenate`)

  * `Concatenate`

  * 标注可调用对象

typing.ParamSpecArgs¶
~~~
    

~~~
typing.ParamSpecKwargs¶
~~~
    

~~~
`ParamSpec`的参数和关键字参数属性。``ParamSpec`` 的 `P.args` 属性是 `ParamSpecArgs` 的一个实例，`P.kwargs` 是 `ParamSpecKwargs` 的一个实例。 它们的目的是用于运行时内部检查的，对静态类型检查器没有特殊意义。

Calling `get_origin()` on either of these objects will return the original `ParamSpec`:
~~~
    
    
~~~shell
>>> from typing import ParamSpec
>>> P = ParamSpec("P")
>>> get_origin(P.args) is P
True
>>> get_origin(P.kwargs) is P
True
~~~

在 3.10 版本加入.

_class _typing.TypeAliasType( _name_ , _value_ , _*_ , _type_params =()_)¶

    

~~~
The type of type aliases created through the [`type`](../reference/simple_stmts.md#type) statement.

示例:
~~~
    
    
~~~shell
>>> type Alias = int
>>> type(Alias)
<class 'typing.TypeAliasType'>
~~~

在 3.12 版本加入.

__name__¶

    

~~~
The name of the type alias:
~~~
    
    
~~~shell
>>> type Alias = int
>>> Alias.__name__
'Alias'
~~~

__module__¶

    

~~~
The module in which the type alias was defined:
~~~
    
    
~~~shell
>>> type Alias = int
>>> Alias.__module__
'__main__'
~~~

__type_params__¶

    

~~~
The type parameters of the type alias, or an empty tuple if the alias is not generic:
~~~
    
    
~~~shell
>>> type ListOrSet[T] = list[T] | set[T]
>>> ListOrSet.__type_params__
(T,)
>>> type NotGeneric = int
>>> NotGeneric.__type_params__
()
~~~

__value__¶

    

~~~
The type alias's value. This is [lazily evaluated](../reference/executionmodel.md#lazy-evaluation), so names used in the definition of the alias are not resolved until the `__value__` attribute is accessed:
~~~
    
    
~~~shell
>>> type Mutually = Recursive
>>> type Recursive = Mutually
>>> Mutually
Mutually
>>> Recursive
Recursive
>>> Mutually.__value__
Recursive
>>> Recursive.__value__
Mutually
~~~

#### 其他特殊指令¶

These functions and classes should not be used directly as annotations. Their intended purpose is to be building blocks for creating and declaring types.

_class _typing.NamedTuple¶

    

~~~
[`collections.namedtuple()`](collections.md#collections.namedtuple "collections.namedtuple") 的类型版本。

用法：
~~~
    
    
~~~
class Employee(NamedTuple):
    name: str
    id: int
~~~

这相当于：

    
    
~~~
Employee = collections.namedtuple('Employee', ['name', 'id'])
~~~

为字段提供默认值，要在类体内赋值：

    
    
~~~
class Employee(NamedTuple):
    name: str
    id: int = 3

employee = Employee('Guido')
assert employee.id == 3
~~~

带默认值的字段必须在不带默认值的字段后面。

由此产生的类有一个额外的属性 `__annotations__` ，给出一个 dict ，将字段名映射到字段类型。（字段名在 `_fields` 属性中，默认值在 `_field_defaults` 属性中，这两者都是 [`namedtuple()`](collections.md#collections.namedtuple "collections.namedtuple") API 的一部分。）

`NamedTuple` 子类也支持文档字符串与方法：

    
    
~~~
class Employee(NamedTuple):
    """Represents an employee."""
    name: str
    id: int = 3

    def __repr__(self) -> str:
        return f'<Employee {self.name}, id={self.id}>'
~~~

`NamedTuple` 子类也可以为泛型：

    
    
~~~
class Group[T](NamedTuple):
    key: T
    group: list[T]
~~~

反向兼容用法：

    
    
~~~
# For creating a generic NamedTuple on Python 3.11 or lower
class Group(NamedTuple, Generic[T]):
    key: T
    group: list[T]

# A functional syntax is also supported
Employee = NamedTuple('Employee', [('name', str), ('id', int)])
~~~

在 3.6 版本发生变更: 添加了对 [**PEP 526**](https://peps.python.org/pep-0526/) 中变量注解句法的支持。

在 3.6.1 版本发生变更: 添加了对默认值、方法、文档字符串的支持。

在 3.8 版本发生变更: `_field_types` 和 `__annotations__` 属性现已使用常规字典，不再使用 `OrderedDict` 实例。

在 3.9 版本发生变更: 移除了 `_field_types` 属性， 改用具有相同信息，但更标准的 `__annotations__` 属性。

在 3.11 版本发生变更: 添加对泛型命名元组的支持。

从 3.13 版起不建议使用，将在 3.15 版中移除: The undocumented keyword argument syntax for creating NamedTuple classes (`NT = NamedTuple("NT", x=int)`) is deprecated, and will be disallowed in 3.15. Use the class-based syntax or the functional syntax instead.

从 3.13 版起不建议使用，将在 3.15 版中移除: When using the functional syntax to create a NamedTuple class, failing to pass a value to the 'fields' parameter (`NT = NamedTuple("NT")`) is deprecated. Passing `None` to the 'fields' parameter (`NT = NamedTuple("NT", None)`) is also deprecated. Both will be disallowed in Python 3.15. To create a NamedTuple class with 0 fields, use `class NT(NamedTuple): pass` or `NT = NamedTuple("NT", [])`.

_class _typing.NewType( _name_ , _tp_ )¶

    

~~~
Helper class to create low-overhead distinct types.

A `NewType` is considered a distinct type by a typechecker. At runtime, however, calling a `NewType` returns its argument unchanged.

用法：
~~~
    
    
~~~
UserId = NewType('UserId', int)  # Declare the NewType "UserId"
first_user = UserId(1)  # "UserId" returns the argument unchanged at runtime
~~~

__module__¶

    

~~~
The module in which the new type is defined.

__name__¶
~~~
    

~~~
The name of the new type.

__supertype__¶
~~~
    

~~~
The type that the new type is based on.

在 3.5.2 版本加入.

在 3.10 版本发生变更: `NewType` 现在是一个类而不是函数。

_class _typing.Protocol( _Generic_ )¶
~~~
    

~~~
Base class for protocol classes.

Protocol classes are defined like this:
~~~
    
    
~~~
class Proto(Protocol):
    def meth(self) -> int:
        ...
~~~

这些类主要与静态类型检查器搭配使用，用来识别结构子类型（静态鸭子类型），例如：

    
    
~~~
class C:
    def meth(self) -> int:
        return 0

def func(x: Proto) -> int:
    return x.meth()

func(C())  # Passes static type check
~~~

请参阅 [**PEP 544**](https://peps.python.org/pep-0544/) 了解详情。 使用 `runtime_checkable()` 装饰的协议类（稍后将介绍）可作为只检查给定属性是否存在，而忽略其类型签名的简单的运行时协议。

Protocol 类可以是泛型，例如：

    
    
~~~
class GenProto[T](Protocol):
    def meth(self) -> T:
        ...
~~~

In code that needs to be compatible with Python 3.11 or older, generic Protocols can be written as follows:

    
    
~~~
T = TypeVar("T")

class GenProto(Protocol[T]):
    def meth(self) -> T:
        ...
~~~

在 3.8 版本加入.

@typing.runtime_checkable¶

    

~~~
用于把 Protocol 类标记为运行时协议。

该协议可以与 [`isinstance()`](functions.md#isinstance "isinstance") 和 [`issubclass()`](functions.md#issubclass "issubclass") 一起使用。应用于非协议的类时，会触发 [`TypeError`](exceptions.md#TypeError "TypeError")。该指令支持简易结构检查，与 [`collections.abc`](collections.abc.md#module-collections.abc "collections.abc: Abstract base classes for containers") 的 [`Iterable`](collections.abc.md#collections.abc.Iterable "collections.abc.Iterable") 非常类似，只擅长做一件事。 例如：
~~~
    
    
~~~
@runtime_checkable
class Closable(Protocol):
    def close(self): ...

assert isinstance(open('/some/file'), Closable)

@runtime_checkable
class Named(Protocol):
    name: str

import threading
assert isinstance(threading.Thread(name='Bob'), Named)
~~~

备注

`runtime_checkable()` will check only the presence of the required methods or attributes, not their type signatures or types. For example, [`ssl.SSLObject`](ssl.md#ssl.SSLObject "ssl.SSLObject") is a class, therefore it passes an [`issubclass()`](functions.md#issubclass "issubclass") check against Callable. However, the `ssl.SSLObject.__init__` method exists only to raise a [`TypeError`](exceptions.md#TypeError "TypeError") with a more informative message, therefore making it impossible to call (instantiate) [`ssl.SSLObject`](ssl.md#ssl.SSLObject "ssl.SSLObject").

备注

针对运行时可检查协议的 [`isinstance()`](functions.md#isinstance "isinstance") 检查相比针对非协议类的 `isinstance()` 检查可能会惊人的缓慢。 请考虑在性能敏感的代码中使用替代性写法如 [`hasattr()`](functions.md#hasattr "hasattr") 调用进行结构检查。

在 3.8 版本加入.

在 3.12 版本发生变更: The internal implementation of [`isinstance()`](functions.md#isinstance "isinstance") checks against runtime-checkable protocols now uses [`inspect.getattr_static()`](inspect.md#inspect.getattr_static "inspect.getattr_static") to look up attributes (previously, [`hasattr()`](functions.md#hasattr "hasattr") was used). As a result, some objects which used to be considered instances of a runtime-checkable protocol may no longer be considered instances of that protocol on Python 3.12+, and vice versa. Most users are unlikely to be affected by this change.

在 3.12 版本发生变更: The members of a runtime-checkable protocol are now considered "frozen" at runtime as soon as the class has been created. Monkey-patching attributes onto a runtime-checkable protocol will still work, but will have no impact on [`isinstance()`](functions.md#isinstance "isinstance") checks comparing objects to the protocol. See ["What's new in Python 3.12"](../whatsnew/3.12.md#whatsnew-typing-py312) for more details.

_class _typing.TypedDict( _dict_ )¶

    

~~~
把类型提示添加至字典的特殊构造器。在运行时，它是纯 [`dict`](stdtypes.md#dict "dict")。

`TypedDict` 声明一个字典类型，该类型预期所有实例都具有一组键集，其中，每个键都与对应类型的值关联。运行时不检查此预期，而是由类型检查器强制执行。用法如下：
~~~
    
    
~~~
class Point2D(TypedDict):
    x: int
    y: int
    label: str

a: Point2D = {'x': 1, 'y': 2, 'label': 'good'}  # OK
b: Point2D = {'z': 3, 'label': 'bad'}           # Fails type check

assert Point2D(x=1, y=2, label='first') == dict(x=1, y=2, label='first')
~~~

An alternative way to create a `TypedDict` is by using function-call syntax. The second argument must be a literal [`dict`](stdtypes.md#dict "dict"):

    
    
~~~
Point2D = TypedDict('Point2D', {'x': int, 'y': int, 'label': str})
~~~

This functional syntax allows defining keys which are not valid [identifiers](../reference/lexical_analysis.md#identifiers), for example because they are keywords or contain hyphens:

    
    
~~~
# raises SyntaxError
class Point2D(TypedDict):
    in: int  # 'in' is a keyword
    x-y: int  # name with hyphens

# OK, functional syntax
Point2D = TypedDict('Point2D', {'in': int, 'x-y': int})
~~~

默认情况下，所有的键都必须出现在一个 `TypedDict` 中。 可以使用 `NotRequired` 将单独的键标记为非必要的:

    
    
~~~
class Point2D(TypedDict):
    x: int
    y: int
    label: NotRequired[str]

# Alternative syntax
Point2D = TypedDict('Point2D', {'x': int, 'y': int, 'label': NotRequired[str]})
~~~

这意味着一个 `Point2D` `TypedDict` 可以省略 `label` 键。

也可以通过全部指定 `False` 将所有键都标记为默认非必要的:

    
    
~~~
class Point2D(TypedDict, total=False):
    x: int
    y: int

# Alternative syntax
Point2D = TypedDict('Point2D', {'x': int, 'y': int}, total=False)
~~~

这意味着一个 `Point2D` `TypedDict` 可以省略任何一个键。 类型检查器只需要支持一个字面的 `False` 或 `True` 作为 `total` 参数的值。 `True` 是默认的，它使类主体中定义的所有项目都是必需的。

一个 `total=False` `TypedDict` 中单独的键可以使用 `Required` 标记为必要的:

    
    
~~~
class Point2D(TypedDict, total=False):
    x: Required[int]
    y: Required[int]
    label: str

# Alternative syntax
Point2D = TypedDict('Point2D', {
    'x': Required[int],
    'y': Required[int],
    'label': str
}, total=False)
~~~

一个 `TypedDict` 类型有可能使用基于类的语法从一个或多个其他 `TypedDict` 类型继承。用法:

    
    
~~~
class Point3D(Point2D):
    z: int
~~~

`Point3D` 有三个项目 : `x` , `y` 和 `z` 。 其等价于定义:

    
    
~~~
class Point3D(TypedDict):
    x: int
    y: int
    z: int
~~~

`TypedDict` 不能从非 `TypedDict` 类继承，除了 `Generic`。 例如:

    
    
~~~
class X(TypedDict):
    x: int

class Y(TypedDict):
    y: int

class Z(object): pass  # A non-TypedDict class

class XY(X, Y): pass  # OK

class XZ(X, Z): pass  # raises TypeError
~~~

`TypedDict` 也可以为泛型的：

    
    
~~~
class Group[T](TypedDict):
    key: T
    group: list[T]
~~~

To create a generic `TypedDict` that is compatible with Python 3.11 or lower, inherit from `Generic` explicitly:

    
    
~~~
T = TypeVar("T")

class Group(TypedDict, Generic[T]):
    key: T
    group: list[T]
~~~

`TypedDict` 可以通过注解字典（参见 [对象注解属性的最佳实践](../howto/annotations.md#annotations-howto) 了解更多关于注解的最佳实践）、 `__total__` 、 `__required_keys__` 和 `__optional_keys__` 进行内省。

__total__¶

    

~~~
`Point2D.__total__` gives the value of the `total` argument. Example:
~~~
    
    
~~~shell
>>> from typing import TypedDict
>>> class Point2D(TypedDict): pass
>>> Point2D.__total__
True
>>> class Point2D(TypedDict, total=False): pass
>>> Point2D.__total__
False
>>> class Point3D(Point2D): pass
>>> Point3D.__total__
True
~~~

__required_keys__¶

    

~~~
在 3.9 版本加入.

__optional_keys__¶
~~~
    

~~~
`Point2D.__required_keys__` 和 `Point2D.__optional_keys__` 返回分别包含必要的和非必要的键的 [`frozenset`](stdtypes.md#frozenset "frozenset") 对象。

标记为 `Required` 的键总是会出现在 `__required_keys__` 中而标记为 `NotRequired` 的键总是会出现在 `__optional_keys__` 中。

For backwards compatibility with Python 3.10 and below, it is also possible to use inheritance to declare both required and non-required keys in the same `TypedDict` . This is done by declaring a `TypedDict` with one value for the `total` argument and then inheriting from it in another `TypedDict` with a different value for `total`:
~~~
    
    
~~~shell
>>> class Point2D(TypedDict, total=False):
...     x: int
...     y: int
...
>>> class Point3D(Point2D):
...     z: int
...
>>> Point3D.__required_keys__ == frozenset({'z'})
True
>>> Point3D.__optional_keys__ == frozenset({'x', 'y'})
True
~~~

在 3.9 版本加入.

更多示例与 `TypedDict` 的详细规则，详见 [**PEP 589**](https://peps.python.org/pep-0589/)。

在 3.8 版本加入.

在 3.11 版本发生变更: 增加了对将单独的键标记为 `Required` 或 `NotRequired` 的支持。 参见 [**PEP 655**](https://peps.python.org/pep-0655/)。

在 3.11 版本发生变更: 添加对泛型 `TypedDict` 的支持。

在 3.13 版本发生变更: Removed support for the keyword-argument method of creating `TypedDict`s.

从 3.13 版起不建议使用，将在 3.15 版中移除: When using the functional syntax to create a TypedDict class, failing to pass a value to the 'fields' parameter (`TD = TypedDict("TD")`) is deprecated. Passing `None` to the 'fields' parameter (`TD = TypedDict("TD", None)`) is also deprecated. Both will be disallowed in Python 3.15. To create a TypedDict class with 0 fields, use `class TD(TypedDict): pass` or `TD = TypedDict("TD", {})`.

### 协议¶

下列协议由 typing 模块提供并已全被装饰为 `可在运行时检查的`。

_class _typing.SupportsAbs¶

    

~~~
一个抽象基类，含一个抽象方法 `__abs__`，该方法与其返回类型协变。

_class _typing.SupportsBytes¶
~~~
    

~~~
一个抽象基类，含一个抽象方法 `__bytes__`。

_class _typing.SupportsComplex¶
~~~
    

~~~
一个抽象基类，含一个抽象方法 `__complex__`。

_class _typing.SupportsFloat¶
~~~
    

~~~
一个抽象基类，含一个抽象方法 `__float__`。

_class _typing.SupportsIndex¶
~~~
    

~~~
一个抽象基类，含一个抽象方法 `__index__`。

在 3.8 版本加入.

_class _typing.SupportsInt¶
~~~
    

~~~
一个抽象基类，含一个抽象方法 `__int__`。

_class _typing.SupportsRound¶
~~~
    

~~~
一个抽象基类，含一个抽象方法 `__round__`，该方法与其返回类型协变。

### 与 IO 相关的抽象基类¶

_class _typing.IO¶

_class _typing.TextIO¶

_class _typing.BinaryIO¶
~~~
    

~~~
泛型 `IO[AnyStr]` 及其子类 `TextIO(IO[str])`、`BinaryIO(IO[bytes])` 表示 I/O 流——例如 [`open()`](functions.md#open "open") 返回的对象——的类型。

### 函数与装饰器¶

typing.cast( _typ_ , _val_ )¶
~~~
    

~~~
把一个值转换为指定的类型。

这会把值原样返回。对类型检查器而言这代表了返回值具有指定的类型，在运行时我们故意没有设计任何检查（我们希望让这尽量快）。

typing.assert_type( _val_ , _typ_ , _/_ )¶
~~~
    

~~~
让静态类型检查器确认 _val_ 具有推断为 _typ_ 的类型。

在运行时这将不做任何事：它会原样返回第一个参数而没有任何检查或附带影响，无论参数的实际类型是什么。

当静态类型检查器遇到对 `assert_type()` 的调用时，如果该值不是指定的类型则会报错:
~~~
    
    
~~~python
def greet(name: str) -> None:
    assert_type(name, str)  # OK, inferred type of `name` is `str`
    assert_type(name, int)  # type checker error
~~~

此函数适用于确保类型检查器对脚本的理解符合开发者的意图:

    
    
~~~python
def complex_function(arg: object):
    # Do some complex type-narrowing logic,
    # after which we hope the inferred type will be `int`
    ...
    # Test whether the type checker correctly understands our function
    assert_type(arg, int)
~~~

在 3.11 版本加入.

typing.assert_never( _arg_ , _/_ )¶

    

~~~
让静态类型检查器确认一行代码是不可达的。

示例：
~~~
    
    
~~~python
def int_or_str(arg: int | str) -> None:
    match arg:
        case int():
            print("It's an int")
        case str():
            print("It's a str")
        case _ as unreachable:
            assert_never(unreachable)
~~~

Here, the annotations allow the type checker to infer that the last case can never execute, because `arg` is either an [`int`](functions.md#int "int") or a [`str`](stdtypes.md#str "str"), and both options are covered by earlier cases.

If a type checker finds that a call to `assert_never()` is reachable, it will emit an error. For example, if the type annotation for `arg` was instead `int | str | float`, the type checker would emit an error pointing out that `unreachable` is of type [`float`](functions.md#float "float"). For a call to `assert_never` to pass type checking, the inferred type of the argument passed in must be the bottom type, `Never`, and nothing else.

在运行时，如果调用此函数将抛出一个异常。

参见

[Unreachable Code and Exhaustiveness Checking](https://typing.readthedocs.io/en/latest/source/unreachable.md) 有更多关于使用静态类型进行穷尽性检查的信息。

在 3.11 版本加入.

typing.reveal_type( _obj_ , _/_ )¶

    

~~~
揭示一个表达式的推断静态类型。

当静态类型检查器遇到一个对此函数的调用时，它将发出包含参数类型的诊断信息。 例如:
~~~
    
    
~~~
x: int = 1
reveal_type(x)  # Revealed type is "builtins.int"
~~~

这在你想要调试你的类型检查器如何处理一段特定代码时很有用处。

该函数将不加修改地返回其参数，这将允许在表达式中使用它:

    
    
~~~
x = reveal_type(1)  # Revealed type is "builtins.int"
~~~

大多数类型检查器都能在任何地方支持 `reveal_type()`，即使并未从 `typing` 导入该名称。 从 `typing` 导入该名称能让你的代码运行时不会出现运行时错误并且更清晰地传递意图。

在运行时，该函数会将其参数的运行时类型打印到 stderr 并不加修改地返回它:

    
    
~~~
x = reveal_type(1)  # prints "Runtime type is int"
print(x)  # prints "1"
~~~

在 3.11 版本加入.

@typing.dataclass_transform( _*_ , _eq_default =True_, _order_default =False_, _kw_only_default =False_, _frozen_default =False_, _field_specifiers =()_, _** kwargs_)¶

    

~~~
Decorator to mark an object as providing [`dataclass`](dataclasses.md#dataclasses.dataclass "dataclasses.dataclass")-like behavior.

`dataclass_transform` may be used to decorate a class, metaclass, or a function that is itself a decorator. The presence of `@dataclass_transform()` tells a static type checker that the decorated object performs runtime "magic" that transforms a class in a similar way to [`@dataclasses.dataclass`](dataclasses.md#dataclasses.dataclass "dataclasses.dataclass").

Example usage with a decorator function:
~~~
    
    
~~~
@dataclass_transform()
def create_model[T](cls: type[T]) -> type[T]:
    ...
    return cls

@create_model
class CustomerModel:
    id: int
    name: str
~~~

在基类上:

    
    
~~~
@dataclass_transform()
class ModelBase: ...

class CustomerModel(ModelBase):
    id: int
    name: str
~~~

在元类上:

    
    
~~~
@dataclass_transform()
class ModelMeta(type): ...

class ModelBase(metaclass=ModelMeta): ...

class CustomerModel(ModelBase):
    id: int
    name: str
~~~

上面定义的 `CustomerModel` 类将被类型检查器视为类似于使用 [`@dataclasses.dataclass`](dataclasses.md#dataclasses.dataclass "dataclasses.dataclass") 创建的类。 例如，类型检查器将假定这些类具有接受 `id` 和 `name` 的 `__init__` 方法。

被装饰的类、元类或函数可以接受以下布尔值参数，类型检查器将假定它们具有与 [`@dataclasses.dataclass`](dataclasses.md#dataclasses.dataclass "dataclasses.dataclass") 装饰器相同的效果: `init`, `eq`, `order`, `unsafe_hash`, `frozen`, `match_args`, `kw_only` 和 `slots`。 这些参数的值 (`True` 或 `False`) 必须可以被静态地求值。

传给 `dataclass_transform` 装饰器的参数可以被用来定制被装饰的类、元类或函数的默认行为:

参数:

    

~~~
  * **eq_default** ([ _bool_](functions.md#bool "bool")) -- Indicates whether the `eq` parameter is assumed to be `True` or `False` if it is omitted by the caller. Defaults to `True`.

  * **order_default** ([ _bool_](functions.md#bool "bool")) -- Indicates whether the `order` parameter is assumed to be `True` or `False` if it is omitted by the caller. Defaults to `False`.

  * **kw_only_default** ([ _bool_](functions.md#bool "bool")) -- Indicates whether the `kw_only` parameter is assumed to be `True` or `False` if it is omitted by the caller. Defaults to `False`.

  * **frozen_default** ([ _bool_](functions.md#bool "bool")) -- 

Indicates whether the `frozen` parameter is assumed to be `True` or `False` if it is omitted by the caller. Defaults to `False`.

在 3.12 版本加入.

  * **field_specifiers** ([ _tuple_](stdtypes.md#tuple "tuple") _[_[ _Callable_](collections.abc.md#collections.abc.Callable "collections.abc.Callable") _[_ _..._ _,_ _Any_ _]_ _,_ _..._ _]_ ) -- Specifies a static list of supported classes or functions that describe fields, similar to [`dataclasses.field()`](dataclasses.md#dataclasses.field "dataclasses.field"). Defaults to `()`.

  * ****kwargs** ( _Any_ ) -- 接受任何其他关键字以便允许可能的未来扩展。

Type checkers recognize the following optional parameters on field specifiers:

**Recognised parameters for field specifiers**¶

Parameter name

|

描述  
  
---|---  
  
`init`

|

Indicates whether the field should be included in the synthesized `__init__` method. If unspecified, `init` defaults to `True`.  
  
`default`

|

Provides the default value for the field.  
  
`default_factory`

|

Provides a runtime callback that returns the default value for the field. If neither `default` nor `default_factory` are specified, the field is assumed to have no default value and must be provided a value when the class is instantiated.  
  
`factory`

|

An alias for the `default_factory` parameter on field specifiers.  
  
`kw_only`

|

Indicates whether the field should be marked as keyword-only. If `True`, the field will be keyword-only. If `False`, it will not be keyword-only. If unspecified, the value of the `kw_only` parameter on the object decorated with `dataclass_transform` will be used, or if that is unspecified, the value of `kw_only_default` on `dataclass_transform` will be used.  
  
`alias`

|

Provides an alternative name for the field. This alternative name is used in the synthesized `__init__` method.  
  
在运行时，该装饰器会将其参数记录到被装饰对象的 `__dataclass_transform__` 属性。 它没有其他的运行时影响。

更多细节请参见 [**PEP 681**](https://peps.python.org/pep-0681/)。

在 3.11 版本加入.

@typing.overload¶
~~~
    

~~~
Decorator for creating overloaded functions and methods.

The `@overload` decorator allows describing functions and methods that support multiple different combinations of argument types. A series of `@overload`-decorated definitions must be followed by exactly one non-`@overload`-decorated definition (for the same function/method).

`@overload`-decorated definitions are for the benefit of the type checker only, since they will be overwritten by the non-`@overload`-decorated definition. The non-`@overload`-decorated definition, meanwhile, will be used at runtime but should be ignored by a type checker. At runtime, calling an `@overload`-decorated function directly will raise [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError").

An example of overload that gives a more precise type than can be expressed using a union or a type variable:
~~~
    
    
~~~
@overload
def process(response: None) -> None:
    ...
@overload
def process(response: int) -> tuple[int, str]:
    ...
@overload
def process(response: bytes) -> str:
    ...
def process(response):
    ...  # actual implementation goes here
~~~

请参阅 [**PEP 484**](https://peps.python.org/pep-0484/) 了解更多细节以及与其他类型语义的比较。

在 3.11 版本发生变更: 过载的函数现在可以使用 `get_overloads()` 在运行时进行内省。

typing.get_overloads( _func_ )¶

    

~~~
Return a sequence of `@overload`-decorated definitions for _func_.

_func_ is the function object for the implementation of the overloaded function. For example, given the definition of `process` in the documentation for `@overload`, `get_overloads(process)` will return a sequence of three function objects for the three defined overloads. If called on a function with no overloads, `get_overloads()` returns an empty sequence.

`get_overloads()` 可被用来在运行时内省一个过载函数。

在 3.11 版本加入.

typing.clear_overloads()¶
~~~
    

~~~
Clear all registered overloads in the internal registry.

This can be used to reclaim the memory used by the registry.

在 3.11 版本加入.

@typing.final¶
~~~
    

~~~
Decorator to indicate final methods and final classes.

Decorating a method with `@final` indicates to a type checker that the method cannot be overridden in a subclass. Decorating a class with `@final` indicates that it cannot be subclassed.

例如：
~~~
    
    
~~~
class Base:
    @final
    def done(self) -> None:
        ...
class Sub(Base):
    def done(self) -> None:  # Error reported by type checker
        ...

@final
class Leaf:
    ...
class Other(Leaf):  # Error reported by type checker
    ...
~~~

这些属性没有运行时检查。详见 [**PEP 591**](https://peps.python.org/pep-0591/)。

在 3.8 版本加入.

在 3.11 版本发生变更: The decorator will now attempt to set a `__final__` attribute to `True` on the decorated object. Thus, a check like `if getattr(obj, "__final__", False)` can be used at runtime to determine whether an object `obj` has been marked as final. If the decorated object does not support setting attributes, the decorator returns the object unchanged without raising an exception.

@typing.no_type_check¶

    

~~~
标明注解不是类型提示的装饰器。

This works as a class or function [decorator](../glossary.md#term-decorator). With a class, it applies recursively to all methods and classes defined in that class (but not to methods defined in its superclasses or subclasses). Type checkers will ignore all annotations in a function or class with this decorator.

`@no_type_check` mutates the decorated object in place.

@typing.no_type_check_decorator¶
~~~
    

~~~
让其他装饰器具有 `no_type_check()` 效果的装饰器。

本装饰器用 `no_type_check()` 里的装饰函数打包其他装饰器。

从 3.13 版起不建议使用，将在 3.15 版中移除: No type checker ever added support for `@no_type_check_decorator`. It is therefore deprecated, and will be removed in Python 3.15.

@typing.override¶
~~~
    

~~~
Decorator to indicate that a method in a subclass is intended to override a method or attribute in a superclass.

Type checkers should emit an error if a method decorated with `@override` does not, in fact, override anything. This helps prevent bugs that may occur when a base class is changed without an equivalent change to a child class.

例如:
~~~
    
    
~~~
class Base:
    def log_status(self) -> None:
        ...

class Sub(Base):
    @override
    def log_status(self) -> None:  # Okay: overrides Base.log_status
        ...

    @override
    def done(self) -> None:  # Error reported by type checker
        ...
~~~

There is no runtime checking of this property.

The decorator will attempt to set an `__override__` attribute to `True` on the decorated object. Thus, a check like `if getattr(obj, "__override__", False)` can be used at runtime to determine whether an object `obj` has been marked as an override. If the decorated object does not support setting attributes, the decorator returns the object unchanged without raising an exception.

See [**PEP 698**](https://peps.python.org/pep-0698/) for more details.

在 3.12 版本加入.

@typing.type_check_only¶

    

~~~
Decorator to mark a class or function as unavailable at runtime.

在运行时，该装饰器本身不可用。实现返回的是私有类实例时，它主要是用于标记在类型存根文件中定义的类。
~~~
    
    
~~~
@type_check_only
class Response:  # private or not available at runtime
    code: int
    def get_header(self, name: str) -> str: ...

def fetch_response() -> Response: ...
~~~

注意，建议不要返回私有类实例，最好将之设为公共类。

### 内省辅助器¶

typing.get_type_hints( _obj_ , _globalns =None_, _localns =None_, _include_extras =False_)¶

    

~~~
返回函数、方法、模块、类对象的类型提示的字典。

这往往与 `obj.__annotations__` 相同。 此外，编码为字符串字面值的前向引用是通过在 `globals` 与 `locals` 命名空间中执行求值来处理的。 对于一个类 `C`，则返回一个由所有 `__annotations__` 与 `C.__mro__` 逆序合并所构建的字典。

The function recursively replaces all `Annotated[T, ...]` with `T`, unless `include_extras` is set to `True` (see `Annotated` for more information). For example:
~~~
    
    
~~~
class Student(NamedTuple):
    name: Annotated[str, 'some marker']

assert get_type_hints(Student) == {'name': str}
assert get_type_hints(Student, include_extras=False) == {'name': str}
assert get_type_hints(Student, include_extras=True) == {
    'name': Annotated[str, 'some marker']
}
~~~

备注

`get_type_hints()` 在导入的 类型别名 中不工作，包括前向引用。启用注解的延迟评估（ [**PEP 563**](https://peps.python.org/pep-0563/) ）可能会消除对大多数前向引用的需要。

在 3.9 版本发生变更: Added `include_extras` parameter as part of [**PEP 593**](https://peps.python.org/pep-0593/). See the documentation on `Annotated` for more information.

在 3.11 版本发生变更: 在之前，如果设置了等于 `None` 的默认值则会为函数和方法标注添加 `Optional[t]`。 现在标注将被不加修改地返回。

typing.get_origin( _tp_ )¶

    

~~~
Get the unsubscripted version of a type: for a typing object of the form `X[Y, Z, ...]` return `X`.

If `X` is a typing-module alias for a builtin or [`collections`](collections.md#module-collections "collections: Container datatypes") class, it will be normalized to the original class. If `X` is an instance of `ParamSpecArgs` or `ParamSpecKwargs`, return the underlying `ParamSpec`. Return `None` for unsupported objects.

示例：
~~~
    
    
~~~
assert get_origin(str) is None
assert get_origin(Dict[str, int]) is dict
assert get_origin(Union[int, str]) is Union
P = ParamSpec('P')
assert get_origin(P.args) is P
assert get_origin(P.kwargs) is P
~~~

在 3.8 版本加入.

typing.get_args( _tp_ )¶

    

~~~
Get type arguments with all substitutions performed: for a typing object of the form `X[Y, Z, ...]` return `(Y, Z, ...)`.

If `X` is a union or `Literal` contained in another generic type, the order of `(Y, Z, ...)` may be different from the order of the original arguments `[Y, Z, ...]` due to type caching. Return `()` for unsupported objects.

示例：
~~~
    
    
~~~
assert get_args(int) == ()
assert get_args(Dict[int, str]) == (int, str)
assert get_args(Union[int, str]) == (int, str)
~~~

在 3.8 版本加入.

typing.get_protocol_members( _tp_ )¶

    

~~~
Return the set of members defined in a `Protocol`.
~~~
    
    
~~~shell
>>> from typing import Protocol, get_protocol_members
>>> class P(Protocol):
...     def a(self) -> str: ...
...     b: int
>>> get_protocol_members(P)
frozenset({'a', 'b'})
~~~

Raise [`TypeError`](exceptions.md#TypeError "TypeError") for arguments that are not Protocols.

在 3.13 版本加入.

typing.is_protocol( _tp_ )¶

    

~~~
Determine if a type is a `Protocol`.

例如：
~~~
    
    
~~~
class P(Protocol):
    def a(self) -> str: ...
    b: int

is_protocol(P)    # => True
is_protocol(int)  # => False
~~~

在 3.13 版本加入.

typing.is_typeddict( _tp_ )¶

    

~~~
检查一个类型是否为 `TypedDict`。

例如:
~~~
    
    
~~~
class Film(TypedDict):
    title: str
    year: int

assert is_typeddict(Film)
assert not is_typeddict(list | str)

# TypedDict is a factory for creating typed dicts,
# not a typed dict itself
assert not is_typeddict(TypedDict)
~~~

在 3.10 版本加入.

_class _typing.ForwardRef¶

    

~~~
Class used for internal typing representation of string forward references.

For example, `List["SomeClass"]` is implicitly transformed into `List[ForwardRef("SomeClass")]`. `ForwardRef` should not be instantiated by a user, but may be used by introspection tools.

备注

[**PEP 585**](https://peps.python.org/pep-0585/) 泛型类型例如 `list["SomeClass"]` 将不会被隐式地转换为 `list[ForwardRef("SomeClass")]` 因而将不会自动解析为 `list[SomeClass]`。

在 3.7.4 版本加入.

### 常量¶

typing.TYPE_CHECKING¶
~~~
    

~~~
A special constant that is assumed to be `True` by 3rd party static type checkers. It is `False` at runtime.

用法：
~~~
    
    
~~~
if TYPE_CHECKING:
    import expensive_mod

def fun(arg: 'expensive_mod.SomeType') -> None:
    local_var: expensive_mod.AnotherType = other_fun()
~~~

第一个类型注解必须用引号标注，才能把它当作“前向引用”，从而在解释器运行时中隐藏 `expensive_mod` 引用。局部变量的类型注释不会被评估，因此，第二个注解不需要用引号引起来。

备注

使用 `from __future__ import` 时，函数定义时不处理注解， 而是把注解当作字符串存在 `__annotations__` 里，这样就不必为注解使用引号。（详见 [**PEP 563**](https://peps.python.org/pep-0563/)）。

在 3.5.2 版本加入.

### 已弃用的别名¶

This module defines several deprecated aliases to pre-existing standard library classes. These were originally included in the typing module in order to support parameterizing these generic classes using `[]`. However, the aliases became redundant in Python 3.9 when the corresponding pre-existing classes were enhanced to support `[]` (see [**PEP 585**](https://peps.python.org/pep-0585/)).

The redundant types are deprecated as of Python 3.9. However, while the aliases may be removed at some point, removal of these aliases is not currently planned. As such, no deprecation warnings are currently issued by the interpreter for these aliases.

If at some point it is decided to remove these deprecated aliases, a deprecation warning will be issued by the interpreter for at least two releases prior to removal. The aliases are guaranteed to remain in the typing module without deprecation warnings until at least Python 3.14.

Type checkers are encouraged to flag uses of the deprecated types if the program they are checking targets a minimum Python version of 3.9 or newer.

#### Aliases to built-in types¶

_class _typing.Dict( _dict, MutableMapping[KT, VT]_)¶

    

~~~
Deprecated alias to [`dict`](stdtypes.md#dict "dict").

Note that to annotate arguments, it is preferred to use an abstract collection type such as `Mapping` rather than to use [`dict`](stdtypes.md#dict "dict") or `typing.Dict`.

该类型用法如下：
~~~
    
    
~~~python
def count_words(text: str) -> Dict[str, int]:
    ...
~~~

自 3.9 版本弃用: [`builtins.dict`](stdtypes.md#dict "dict") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.List( _list, MutableSequence[T]_)¶

    

~~~
Deprecated alias to [`list`](stdtypes.md#list "list").

Note that to annotate arguments, it is preferred to use an abstract collection type such as `Sequence` or `Iterable` rather than to use [`list`](stdtypes.md#list "list") or `typing.List`.

该类型用法如下：
~~~
    
    
~~~python
def vec2[T: (int, float)](x: T, y: T) -> List[T]:
    return [x, y]

def keep_positives[T: (int, float)](vector: Sequence[T]) -> List[T]:
    return [item for item in vector if item > 0]
~~~

自 3.9 版本弃用: [`builtins.list`](stdtypes.md#list "list") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.Set( _set, MutableSet[T]_)¶

    

~~~
Deprecated alias to [`builtins.set`](stdtypes.md#set "set").

Note that to annotate arguments, it is preferred to use an abstract collection type such as `AbstractSet` rather than to use [`set`](stdtypes.md#set "set") or `typing.Set`.

自 3.9 版本弃用: [`builtins.set`](stdtypes.md#set "set") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.FrozenSet( _frozenset, AbstractSet[T_co]_)¶
~~~
    

~~~
Deprecated alias to [`builtins.frozenset`](stdtypes.md#frozenset "frozenset").

自 3.9 版本弃用: [`builtins.frozenset`](stdtypes.md#frozenset "frozenset") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

typing.Tuple¶
~~~
    

~~~
Deprecated alias for [`tuple`](stdtypes.md#tuple "tuple").

[`tuple`](stdtypes.md#tuple "tuple") and `Tuple` are special-cased in the type system; see Annotating tuples for more details.

自 3.9 版本弃用: [`builtins.tuple`](stdtypes.md#tuple "tuple") 现在支持下标操作（`[]`）。参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.Type( _Generic[CT_co]_ )¶
~~~
    

~~~
Deprecated alias to [`type`](functions.md#type "type").

See The type of class objects for details on using [`type`](functions.md#type "type") or `typing.Type` in type annotations.

在 3.5.2 版本加入.

自 3.9 版本弃用: [`builtins.type`](functions.md#type "type") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

#### Aliases to types in [`collections`](collections.md#module-collections "collections: Container datatypes")¶

_class _typing.DefaultDict( _collections.defaultdict, MutableMapping[KT, VT]_)¶
~~~
    

~~~
Deprecated alias to [`collections.defaultdict`](collections.md#collections.defaultdict "collections.defaultdict").

在 3.5.2 版本加入.

自 3.9 版本弃用: [`collections.defaultdict`](collections.md#collections.defaultdict "collections.defaultdict") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.OrderedDict( _collections.OrderedDict, MutableMapping[KT, VT]_)¶
~~~
    

~~~
Deprecated alias to [`collections.OrderedDict`](collections.md#collections.OrderedDict "collections.OrderedDict").

在 3.7.2 版本加入.

自 3.9 版本弃用: [`collections.OrderedDict`](collections.md#collections.OrderedDict "collections.OrderedDict") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.ChainMap( _collections.ChainMap, MutableMapping[KT, VT]_)¶
~~~
    

~~~
Deprecated alias to [`collections.ChainMap`](collections.md#collections.ChainMap "collections.ChainMap").

在 3.5.4 版本加入.

在 3.6.1 版本加入.

自 3.9 版本弃用: [`collections.ChainMap`](collections.md#collections.ChainMap "collections.ChainMap") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.Counter( _collections.Counter, Dict[T, int]_)¶
~~~
    

~~~
Deprecated alias to [`collections.Counter`](collections.md#collections.Counter "collections.Counter").

在 3.5.4 版本加入.

在 3.6.1 版本加入.

自 3.9 版本弃用: [`collections.Counter`](collections.md#collections.Counter "collections.Counter") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.Deque( _deque, MutableSequence[T]_)¶
~~~
    

~~~
Deprecated alias to [`collections.deque`](collections.md#collections.deque "collections.deque").

在 3.5.4 版本加入.

在 3.6.1 版本加入.

自 3.9 版本弃用: [`collections.deque`](collections.md#collections.deque "collections.deque") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

#### Aliases to other concrete types¶

_class _typing.Pattern¶

_class _typing.Match¶
~~~
    

~~~
Deprecated aliases corresponding to the return types from [`re.compile()`](re.md#re.compile "re.compile") and [`re.match()`](re.md#re.match "re.match").

These types (and the corresponding functions) are generic over `AnyStr`. `Pattern` can be specialised as `Pattern[str]` or `Pattern[bytes]`; `Match` can be specialised as `Match[str]` or `Match[bytes]`.

自 3.9 版本弃用: [`re`](re.md#module-re "re: Regular expression operations.") 模块中的 `Pattern` 与 `Match` 类现已支持 `[]`。详见 [**PEP 585**](https://peps.python.org/pep-0585/) 与 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.Text¶
~~~
    

~~~
Deprecated alias for [`str`](stdtypes.md#str "str").

`Text` is provided to supply a forward compatible path for Python 2 code: in Python 2, `Text` is an alias for `unicode`.

使用 `Text` 时，值中必须包含 unicode 字符串，以兼容 Python 2 和 Python 3：
~~~
    
    
~~~python
def add_unicode_checkmark(text: Text) -> Text:
    return text + u' \u2713'
~~~

在 3.5.2 版本加入.

自 3.11 版本弃用: Python 2 已不再受支持，并且大部分类型检查器也都不再支持 Python 2 代码的类型检查。 目录还没有移除该别名的计划，但建议用户使用 [`str`](stdtypes.md#str "str") 来代替 `Text`。

#### Aliases to container ABCs in [`collections.abc`](collections.abc.md#module-collections.abc "collections.abc: Abstract base classes for containers")¶

_class _typing.AbstractSet( _Collection[T_co]_ )¶

    

~~~
Deprecated alias to [`collections.abc.Set`](collections.abc.md#collections.abc.Set "collections.abc.Set").

自 3.9 版本弃用: [`collections.abc.Set`](collections.abc.md#collections.abc.Set "collections.abc.Set") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.ByteString( _Sequence[int]_ )¶
~~~
    

~~~
该类型代表了 [`bytes`](stdtypes.md#bytes "bytes")、[`bytearray`](stdtypes.md#bytearray "bytearray")、[`memoryview`](stdtypes.md#memoryview "memoryview") 等字节序列类型。

从 3.9 版起不建议使用，将在 3.14 版中移除: 首选 [`collections.abc.Buffer`](collections.abc.md#collections.abc.Buffer "collections.abc.Buffer")，或是 `bytes | bytearray | memoryview` 这样的并集。

_class _typing.Collection( _Sized, Iterable[T_co], Container[T_co]_)¶
~~~
    

~~~
[`collections.abc.Collection`](collections.abc.md#collections.abc.Collection "collections.abc.Collection") 的已被弃用的别名。

在 3.6.0 版本加入.

自 3.9 版本弃用: [`collections.abc.Collection`](collections.abc.md#collections.abc.Collection "collections.abc.Collection") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.Container( _Generic[T_co]_ )¶
~~~
    

~~~
[`collections.abc.Container`](collections.abc.md#collections.abc.Container "collections.abc.Container") 的已被弃用的别名。

自 3.9 版本弃用: [`collections.abc.Container`](collections.abc.md#collections.abc.Container "collections.abc.Container") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.ItemsView( _MappingView, AbstractSet[tuple[KT_co, VT_co]]_)¶
~~~
    

~~~
[`collections.abc.ItemsView`](collections.abc.md#collections.abc.ItemsView "collections.abc.ItemsView") 的已被弃用的别名。

自 3.9 版本弃用: [`collections.abc.ItemsView`](collections.abc.md#collections.abc.ItemsView "collections.abc.ItemsView") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.KeysView( _MappingView, AbstractSet[KT_co]_)¶
~~~
    

~~~
[`collections.abc.KeysView`](collections.abc.md#collections.abc.KeysView "collections.abc.KeysView") 的已被弃用的别名。

自 3.9 版本弃用: [`collections.abc.KeysView`](collections.abc.md#collections.abc.KeysView "collections.abc.KeysView") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.Mapping( _Collection[KT], Generic[KT, VT_co]_)¶
~~~
    

~~~
[`collections.abc.Mapping`](collections.abc.md#collections.abc.Mapping "collections.abc.Mapping") 的已被弃用的别名。

该类型用法如下：
~~~
    
    
~~~python
def get_position_in_index(word_list: Mapping[str, int], word: str) -> int:
    return word_list[word]
~~~

自 3.9 版本弃用: [`collections.abc.Mapping`](collections.abc.md#collections.abc.Mapping "collections.abc.Mapping") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.MappingView( _Sized_ )¶

    

~~~
[`collections.abc.MappingView`](collections.abc.md#collections.abc.MappingView "collections.abc.MappingView") 的已被弃用的别名。

自 3.9 版本弃用: [`collections.abc.MappingView`](collections.abc.md#collections.abc.MappingView "collections.abc.MappingView") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.MutableMapping( _Mapping[KT, VT]_)¶
~~~
    

~~~
[`collections.abc.MutableMapping`](collections.abc.md#collections.abc.MutableMapping "collections.abc.MutableMapping") 的已被弃用的别名。

自 3.9 版本弃用: [`collections.abc.MutableMapping`](collections.abc.md#collections.abc.MutableMapping "collections.abc.MutableMapping") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.MutableSequence( _Sequence[T]_ )¶
~~~
    

~~~
[`collections.abc.MutableSequence`](collections.abc.md#collections.abc.MutableSequence "collections.abc.MutableSequence") 的已被弃用的别名。

自 3.9 版本弃用: [`collections.abc.MutableSequence`](collections.abc.md#collections.abc.MutableSequence "collections.abc.MutableSequence") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.MutableSet( _AbstractSet[T]_ )¶
~~~
    

~~~
[`collections.abc.MutableSet`](collections.abc.md#collections.abc.MutableSet "collections.abc.MutableSet") 的已被弃用的别名。

自 3.9 版本弃用: [`collections.abc.MutableSet`](collections.abc.md#collections.abc.MutableSet "collections.abc.MutableSet") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.Sequence( _Reversible[T_co], Collection[T_co]_)¶
~~~
    

~~~
[`collections.abc.Sequence`](collections.abc.md#collections.abc.Sequence "collections.abc.Sequence") 的已被弃用的别名。

自 3.9 版本弃用: [`collections.abc.Sequence`](collections.abc.md#collections.abc.Sequence "collections.abc.Sequence") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.ValuesView( _MappingView, Collection[_VT_co]_)¶
~~~
    

~~~
[`collections.abc.ValuesView`](collections.abc.md#collections.abc.ValuesView "collections.abc.ValuesView") 的已被弃用的别名。

自 3.9 版本弃用: [`collections.abc.ValuesView`](collections.abc.md#collections.abc.ValuesView "collections.abc.ValuesView") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

#### [`collections.abc`](collections.abc.md#module-collections.abc "collections.abc: Abstract base classes for containers") 中异步 ABC 的别名¶

_class _typing.Coroutine( _Awaitable[ReturnType], Generic[YieldType, SendType, ReturnType]_)¶
~~~
    

~~~
[`collections.abc.Coroutine`](collections.abc.md#collections.abc.Coroutine "collections.abc.Coroutine") 的已被弃用的别名。

类型变量的变化形式和顺序与 `Generator` 的相对应，例如:
~~~
    
    
~~~
from collections.abc import Coroutine
c: Coroutine[list[str], str, int]  # Some coroutine defined elsewhere
x = c.send('hi')                   # Inferred type of 'x' is list[str]
async def bar() -> None:
    y = await c                    # Inferred type of 'y' is int
~~~

在 3.5.3 版本加入.

自 3.9 版本弃用: [`collections.abc.Coroutine`](collections.abc.md#collections.abc.Coroutine "collections.abc.Coroutine") 现在支持下标操作（`[]`）。参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.AsyncGenerator( _AsyncIterator[YieldType], Generic[YieldType, SendType]_)¶

    

~~~
[`collections.abc.AsyncGenerator`](collections.abc.md#collections.abc.AsyncGenerator "collections.abc.AsyncGenerator") 的已被弃用的别名。

异步生成器可由泛型类型 `AsyncGenerator[YieldType, SendType]` 注解。例如：
~~~
    
    
~~~
async def echo_round() -> AsyncGenerator[int, float]:
    sent = yield 0
    while sent >= 0.0:
        rounded = await round(sent)
        sent = yield rounded
~~~

与常规生成器不同，异步生成器不能返回值，因此没有 `ReturnType` 类型参数。 与 `Generator` 类似，`SendType` 也属于逆变行为。

如果生成器只产生值，可将 `SendType` 设置为 `None`：

    
    
~~~
async def infinite_stream(start: int) -> AsyncGenerator[int, None]:
    while True:
        yield start
        start = await increment(start)
~~~

此外，可用 `AsyncIterable[YieldType]` 或 `AsyncIterator[YieldType]` 注解生成器的返回类型：

    
    
~~~
async def infinite_stream(start: int) -> AsyncIterator[int]:
    while True:
        yield start
        start = await increment(start)
~~~

在 3.6.1 版本加入.

自 3.9 版本弃用: [`collections.abc.AsyncGenerator`](collections.abc.md#collections.abc.AsyncGenerator "collections.abc.AsyncGenerator") 现在支持下标操作（`[]`）。参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.AsyncIterable( _Generic[T_co]_ )¶

    

~~~
[`collections.abc.AsyncIterable`](collections.abc.md#collections.abc.AsyncIterable "collections.abc.AsyncIterable") 的已被弃用的别名。

在 3.5.2 版本加入.

自 3.9 版本弃用: [`collections.abc.AsyncIterable`](collections.abc.md#collections.abc.AsyncIterable "collections.abc.AsyncIterable") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.AsyncIterator( _AsyncIterable[T_co]_ )¶
~~~
    

~~~
[`collections.abc.AsyncIterator`](collections.abc.md#collections.abc.AsyncIterator "collections.abc.AsyncIterator") 的已被弃用的别名。

在 3.5.2 版本加入.

自 3.9 版本弃用: [`collections.abc.AsyncIterator`](collections.abc.md#collections.abc.AsyncIterator "collections.abc.AsyncIterator") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.Awaitable( _Generic[T_co]_ )¶
~~~
    

~~~
[`collections.abc.Awaitable`](collections.abc.md#collections.abc.Awaitable "collections.abc.Awaitable") 的已被弃用的别名。

在 3.5.2 版本加入.

自 3.9 版本弃用: [`collections.abc.Awaitable`](collections.abc.md#collections.abc.Awaitable "collections.abc.Awaitable") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

#### [`collections.abc`](collections.abc.md#module-collections.abc "collections.abc: Abstract base classes for containers") 中其他 ABC 的别名¶

_class _typing.Iterable( _Generic[T_co]_ )¶
~~~
    

~~~
[`collections.abc.Iterable`](collections.abc.md#collections.abc.Iterable "collections.abc.Iterable") 的已被弃用的别名

自 3.9 版本弃用: [`collections.abc.Iterable`](collections.abc.md#collections.abc.Iterable "collections.abc.Iterable") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.Iterator( _Iterable[T_co]_ )¶
~~~
    

~~~
[`collections.abc.Iterator`](collections.abc.md#collections.abc.Iterator "collections.abc.Iterator") 的已被弃用的别名。

自 3.9 版本弃用: [`collections.abc.Iterator`](collections.abc.md#collections.abc.Iterator "collections.abc.Iterator") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

typing.Callable¶
~~~
    

~~~
[`collections.abc.Callable`](collections.abc.md#collections.abc.Callable "collections.abc.Callable") 的已被弃用的别名。

有关如何在类型标注中使用 [`collections.abc.Callable`](collections.abc.md#collections.abc.Callable "collections.abc.Callable") 和 `typing.Callable` 的详细信息请参阅 标注可调用对象。

自 3.9 版本弃用: [`collections.abc.Callable`](collections.abc.md#collections.abc.Callable "collections.abc.Callable") 现在支持下标操作（`[]`）。参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

在 3.10 版本发生变更: `Callable` 现在支持 `ParamSpec` 和 `Concatenate`。 详情见 [**PEP 612**](https://peps.python.org/pep-0612/)。

_class _typing.Generator( _Iterator[YieldType], Generic[YieldType, SendType, ReturnType]_)¶
~~~
    

~~~
[`collections.abc.Generator`](collections.abc.md#collections.abc.Generator "collections.abc.Generator") 的已被弃用的别名。

生成器可以由泛型类型 `Generator[YieldType, SendType, ReturnType]` 注解。例如：
~~~
    
    
~~~python
def echo_round() -> Generator[int, float, str]:
    sent = yield 0
    while sent >= 0:
        sent = yield round(sent)
    return 'Done'
~~~

注意，与 typing 模块里的其他泛型不同， `Generator` 的 `SendType` 属于逆变行为，不是协变行为，也是不变行为。

如果生成器只产生值，可将 `SendType` 与 `ReturnType` 设为 `None`：

    
    
~~~python
def infinite_stream(start: int) -> Generator[int, None, None]:
    while True:
        yield start
        start += 1
~~~

此外，还可以把生成器的返回类型注解为 `Iterable[YieldType]` 或 `Iterator[YieldType]`：

    
    
~~~python
def infinite_stream(start: int) -> Iterator[int]:
    while True:
        yield start
        start += 1
~~~

自 3.9 版本弃用: [`collections.abc.Generator`](collections.abc.md#collections.abc.Generator "collections.abc.Generator") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.Hashable¶

    

~~~
[`collections.abc.Hashable`](collections.abc.md#collections.abc.Hashable "collections.abc.Hashable") 的已被弃用的别名。

自 3.12 版本弃用: 请改为直接使用 [`collections.abc.Hashable`](collections.abc.md#collections.abc.Hashable "collections.abc.Hashable")。

_class _typing.Reversible( _Iterable[T_co]_ )¶
~~~
    

~~~
[`collections.abc.Reversible`](collections.abc.md#collections.abc.Reversible "collections.abc.Reversible") 的已被弃用的别名。

自 3.9 版本弃用: [`collections.abc.Reversible`](collections.abc.md#collections.abc.Reversible "collections.abc.Reversible") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.Sized¶
~~~
    

~~~
[`collections.abc.Sized`](collections.abc.md#collections.abc.Sized "collections.abc.Sized") 的已被弃用的别名。

自 3.12 版本弃用: 请改为直接使用 [`collections.abc.Sized`](collections.abc.md#collections.abc.Sized "collections.abc.Sized")。

#### [`contextlib`](contextlib.md#module-contextlib "contextlib: Utilities for with-statement contexts.") ABC 的别名¶

_class _typing.ContextManager( _Generic[T_co]_ )¶
~~~
    

~~~
[`contextlib.AbstractContextManager`](contextlib.md#contextlib.AbstractContextManager "contextlib.AbstractContextManager") 的已被弃用的别名。

在 3.5.4 版本加入.

在 3.6.0 版本加入.

自 3.9 版本弃用: [`contextlib.AbstractContextManager`](contextlib.md#contextlib.AbstractContextManager "contextlib.AbstractContextManager") 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型](stdtypes.md#types-genericalias)。

_class _typing.AsyncContextManager( _Generic[T_co]_ )¶
~~~
    

~~~
