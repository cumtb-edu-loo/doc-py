# `numbers` \--- 数字的抽象基类¶

**源代码：** [Lib/numbers.py](https://github.com/python/cpython/tree/3.12/Lib/numbers.py)

* * *

`numbers` 模块 ([ **PEP 3141**](https://peps.python.org/pep-3141/)) 定义了数字 [抽象基类](../glossary.md#term-abstract-base-class) 的层级结构，其中逐级定义了更多操作。 此模块中定义的类型都不可被实例化。

_class _numbers.Number¶

    

~~~
数字的层次结构的基础。 如果你只想确认参数 _x_ 是不是数字而不关心其类型，则使用 `isinstance(x, Number)`。

## 数字的层次¶

_class _numbers.Complex¶
~~~
    

~~~
这个类型的子类描述了复数并包括了适用于内置 [`complex`](functions.md#complex "complex") 类型的操作。 这些操作有: 转换为 [`complex`](functions.md#complex "complex") 和 [`bool`](functions.md#bool "bool"), `real`, `imag`, `+`, `-`, `*`, `/`, `**`, [`abs()`](functions.md#abs "abs"), `conjugate()`, `==` 以及 `!=`。 除 `-` 和 `!=` 之外所有操作都是抽象的。

real¶
~~~
    

~~~
抽象的。得到该数字的实数部分。

imag¶
~~~
    

~~~
抽象的。得到该数字的虚数部分。

_abstractmethod _conjugate()¶
~~~
    

~~~
抽象的。返回共轭复数。例如 `(1+3j).conjugate() == (1-3j)`。

_class _numbers.Real¶
~~~
    

~~~
相对于 `Complex`，`Real` 加入了只有实数才能进行的操作。

简单的说，它们是：转化至 [`float`](functions.md#float "float")，[`math.trunc()`](math.md#math.trunc "math.trunc")、 [`round()`](functions.md#round "round")、 [`math.floor()`](math.md#math.floor "math.floor")、 [`math.ceil()`](math.md#math.ceil "math.ceil")、 [`divmod()`](functions.md#divmod "divmod")、 `//`、 `%`、 `<`、 `<=`、 `>`、 和 `>=`。

实数同样默认支持 [`complex()`](functions.md#complex "complex")、 `real`、 `imag` 和 `conjugate()`。

_class _numbers.Rational¶
~~~
    

~~~
子类型 `Real` 并加入 `numerator` 和 `denominator` 两种特征属性。 它还为 [`float()`](functions.md#float "float") 提供了默认值。

`numerator` 和 `denominator` 值应为 `Integral` 的实例并且应当是具有 `denominator` 正值的最低项。

numerator¶
~~~
    

~~~
抽象的。

denominator¶
~~~
    

~~~
抽象的。

_class _numbers.Integral¶
~~~
    

~~~
子类型 `Rational` 还增加了到 [`int`](functions.md#int "int") 的转换操作。 为 [`float()`](functions.md#float "float"), `numerator` 和 `denominator` 提供了默认支持。 为 [`pow()`](functions.md#pow "pow") 方法增加了求余和按位字符串运算的抽象方法: `<<`, `>>`, `&`, `^`, `|`, `~`。

## 类型接口注释。¶

实现者需要注意使相等的数字相等并拥有同样的值。当这两个数使用不同的扩展模块时，这其中的差异是很微妙的。例如，用 [`fractions.Fraction`](fractions.md#fractions.Fraction "fractions.Fraction") 实现 [`hash()`](functions.md#hash "hash") 如下:
~~~
    
    
~~~python
def __hash__(self):
    if self.denominator == 1:
        # Get integers right.
        return hash(self.numerator)
    # Expensive check, but definitely correct.
    if self == float(self):
        return hash(float(self))
    else:
        # Use tuple's hash to avoid a high collision rate on
        # simple fractions.
        return hash((self.numerator, self.denominator))
~~~

### 加入更多数字的ABC¶

当然，这里有更多支持数字的ABC，如果不加入这些，就将缺少层次感。你可以用如下方法在 `Complex` 和 `Real` 中加入 `MyFoo`:

    
    
~~~
class MyFoo(Complex): ...
MyFoo.register(Real)
~~~

### 实现算术运算¶

我们希望实现计算，因此，混合模式操作要么调用一个作者知道参数类型的实现，要么转变成为最接近的内置类型并对这个执行操作。对于子类 `Integral`，这意味着 `__add__()` 和 `__radd__()` 必须用如下方式定义：

    
    
~~~
class MyIntegral(Integral):

    def __add__(self, other):
        if isinstance(other, MyIntegral):
            return do_my_adding_stuff(self, other)
        elif isinstance(other, OtherTypeIKnowAbout):
            return do_my_other_adding_stuff(self, other)
        else:
            return NotImplemented

    def __radd__(self, other):
        if isinstance(other, MyIntegral):
            return do_my_adding_stuff(other, self)
        elif isinstance(other, OtherTypeIKnowAbout):
            return do_my_other_adding_stuff(other, self)
        elif isinstance(other, Integral):
            return int(other) + int(self)
        elif isinstance(other, Real):
            return float(other) + float(self)
        elif isinstance(other, Complex):
            return complex(other) + complex(self)
        else:
            return NotImplemented
~~~

`Complex` 有 5 种不同的混合类型的操作。 我将上面提到的所有代码作为“模板”称作 `MyIntegral` 和 `OtherTypeIKnowAbout`。 `a` 是 `Complex` 的子类型 `A` 的实例 (`a : A <: Complex`)，同时 `b : B <: Complex`。 我将要计算 `a + b`:

>   1. 如果 `A` 被定义成一个承认 `b` 的 `__add__()`，一切都没有问题。
>
>   2. 如果 `A` 转回成“模板”失败，它将返回一个属于 `__add__()` 的值，我们需要避免 `B` 定义了一个更加智能的 `__radd__()`，因此模板需要返回一个属于 `__add__()` 的 [`NotImplemented`](constants.md#NotImplemented "NotImplemented") 。（或者 `A` 可能完全不实现 `__add__()` 。）
>
>   3. 接着看 `B` 的 `__radd__()` 。如果它承认 `a` ，一切都没有问题。
>
>   4. 如果没有成功回退到模板，就没有更多的方法可以去尝试，因此这里将使用默认的实现。
>
>   5. 如果 `B <: A` ， Python 在 `A.__add__` 之前尝试 `B.__radd__` 。 这是可行的，是通过对 `A` 的认识实现的，因此这可以在交给 `Complex` 处理之前处理这些实例。
>
>

如果 `A <: Complex` 和 `B <: Real` 没有共享任何资源，那么适当的共享操作涉及内置的 [`complex`](functions.md#complex "complex") ，并且分别获得 `__radd__()` ，因此 `a+b == b+a`。

由于对任何一直类型的大部分操作是十分相似的，可以定义一个帮助函数，即一个生成后续或相反的实例的生成器。例如，使用 [`fractions.Fraction`](fractions.md#fractions.Fraction "fractions.Fraction") 如下：

    
    
~~~python
def _operator_fallbacks(monomorphic_operator, fallback_operator):
    def forward(a, b):
        if isinstance(b, (int, Fraction)):
            return monomorphic_operator(a, b)
        elif isinstance(b, float):
            return fallback_operator(float(a), b)
        elif isinstance(b, complex):
            return fallback_operator(complex(a), b)
        else:
            return NotImplemented
    forward.__name__ = '__' + fallback_operator.__name__ + '__'
    forward.__doc__ = monomorphic_operator.__doc__

    def reverse(b, a):
        if isinstance(a, Rational):
            # Includes ints.
            return monomorphic_operator(a, b)
        elif isinstance(a, Real):
            return fallback_operator(float(a), float(b))
        elif isinstance(a, Complex):
            return fallback_operator(complex(a), complex(b))
        else:
            return NotImplemented
    reverse.__name__ = '__r' + fallback_operator.__name__ + '__'
    reverse.__doc__ = monomorphic_operator.__doc__

    return forward, reverse

def _add(a, b):
    """a + b"""
    return Fraction(a.numerator * b.denominator +
                    b.numerator * a.denominator,
                    a.denominator * b.denominator)

__add__, __radd__ = _operator_fallbacks(_add, operator.add)

# ...
~~~

