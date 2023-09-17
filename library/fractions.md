# `fractions` \--- 分数¶

**源代码** [Lib/fractions.py](https://github.com/python/cpython/tree/3.12/Lib/fractions.py)

* * *

`fractions` 模块支持分数运算。

分数实例可以由一对整数，一个分数，或者一个字符串构建而成。

_class _fractions.Fraction( _numerator =0_, _denominator =1_)¶

_class _fractions.Fraction( _other_fraction_ )

_class _fractions.Fraction( _float_ )

_class _fractions.Fraction( _decimal_ )

_class _fractions.Fraction( _string_ )

    

~~~
第一个版本要求 _numerator_ 和 _denominator_ 是 [`numbers.Rational`](numbers.md#numbers.Rational "numbers.Rational") 的实例，并返回一个值为 `numerator/denominator` 的新 `Fraction` 实例。 如果 _denominator_ 是 `0` 则会引发 [`ZeroDivisionError`](exceptions.md#ZeroDivisionError "ZeroDivisionError")。 第二个版本要求 _other_fraction_ 是 [`numbers.Rational`](numbers.md#numbers.Rational "numbers.Rational") 的实例，并返回具有相同值的 `Fraction` 实例。 接下来的两个版本接受 [`float`](functions.md#float "float") 或 [`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal") 实例，并返回具有完全相同值的 `Fraction` 实例。 请注意由于二进制浮点运算通常存在的问题 (参见 [浮点算术：争议和限制](../tutorial/floatingpoint.md#tut-fp-issues))，`Fraction(1.1)` 的参数并不完全等于 11/10，因此 `Fraction(1.1)` 也 _不会_ 像人们所期望的那样返回 `Fraction(11, 10)`。 （请参阅下面 `limit_denominator()` 方法的文档。） 最后一个版本的构造器接受一个字符串或 unicode 实例。 该实例的通常形式为:
~~~
    
    
~~~
[sign] numerator ['/' denominator]
~~~

其中的可选项 `sign` 可能为 '+' 或 '-' 且 `numerator` 和 `denominator` (如果存在) 是十进制数码的字符串 (可以如代码中的整数字面值一样使用下划线来分隔数码)。 此外，[`float`](functions.md#float "float") 构造器所接受的任何代表一个有限值的字符串也都为 `Fraction` 构造器所接受。 不论哪 种形式的输入字符串也都可以带有开头和/或末尾空格符。 这里是一些示例:

    
    
~~~shell
>>> from fractions import Fraction
>>> Fraction(16, -10)
Fraction(-8, 5)
>>> Fraction(123)
Fraction(123, 1)
>>> Fraction()
Fraction(0, 1)
>>> Fraction('3/7')
Fraction(3, 7)
>>> Fraction(' -3/7 ')
Fraction(-3, 7)
>>> Fraction('1.414213 \t\n')
Fraction(1414213, 1000000)
>>> Fraction('-.125')
Fraction(-1, 8)
>>> Fraction('7e-6')
Fraction(7, 1000000)
>>> Fraction(2.25)
Fraction(9, 4)
>>> Fraction(1.1)
Fraction(2476979795053773, 2251799813685248)
>>> from decimal import Decimal
>>> Fraction(Decimal('1.1'))
Fraction(11, 10)
~~~

`Fraction` 类继承自抽象基类 [`numbers.Rational`](numbers.md#numbers.Rational "numbers.Rational")，并实现了该类的所有方法和操作。 `Fraction` 实例是 [hashable](../glossary.md#term-hashable) 对象，并应当被视为不可变对象。 此外，`Fraction` 还具有以下特征属性和方法:

在 3.2 版本发生变更: `Fraction` 构造器现在接受 [`float`](functions.md#float "float") 和 [`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal") 实例。

在 3.9 版本发生变更: 现在会使用 [`math.gcd()`](math.md#math.gcd "math.gcd") 函数来正规化 _numerator_ 和 _denominator_ 。 [`math.gcd()`](math.md#math.gcd "math.gcd") 总是返回 [`int`](functions.md#int "int") 类型。 在之前版本中，GCD 的类型取决于 _numerator_ 和 _denominator_ 的类型。

在 3.11 版本发生变更: 现在当使用字符串创建 `Fraction` 实例时已允许使用下划线，遵循 [**PEP 515**](https://peps.python.org/pep-0515/) 规则。

在 3.11 版本发生变更: `Fraction` 现在实现了 `__int__` 以满足 `typing.SupportsInt` 实例检测。

在 3.12 版本发生变更: 允许字符串输入在斜杠两边添加空格: `Fraction('2 / 3')`。

在 3.12 版本发生变更: `Fraction` 实例现在支持浮点风格的格式化，使用 `"e"`, `"E"`, `"f"`, `"F"`, `"g"`, `"G"` 和 `"%""` 等表示类型。.

numerator¶

    

~~~
最简分数形式的分子。

denominator¶
~~~
    

~~~
最简分数形式的分母。

as_integer_ratio()¶
~~~
    

~~~
返回由两个整数组成的元组，两数之比等于原 Fraction 的值且其分母为正数。

在 3.8 版本加入.

is_integer()¶
~~~
    

~~~
如果 Fraction 为整数则返回 `True`。

在 3.12 版本加入.

_classmethod _from_float( _flt_ )¶
~~~
    

~~~
只接受 [`float`](functions.md#float "float") 或 [`numbers.Integral`](numbers.md#numbers.Integral "numbers.Integral") 实例的替代性构造器。 请注意 `Fraction.from_float(0.3)` 与 `Fraction(3, 10)` 的值是不同的。

备注

从 Python 3.2 开始，在构造 `Fraction` 实例时可以直接使用 [`float`](functions.md#float "float")。

_classmethod _from_decimal( _dec_ )¶
~~~
    

~~~
只接受 [`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal") 或 [`numbers.Integral`](numbers.md#numbers.Integral "numbers.Integral") 实例的替代性构造器。

备注

从 Python 3.2 开始，在构造 `Fraction` 实例时可以直接使用 [`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal") 实例。

limit_denominator( _max_denominator =1000000_)¶
~~~
    

~~~
找到并返回一个 `Fraction` 使得其值最接近 `self` 并且分母不大于 max_denominator。 此方法适用于找出给定浮点数的有理数近似值：
~~~
    
    
~~~shell
>>> from fractions import Fraction
>>> Fraction('3.1415926535897932').limit_denominator(1000)
Fraction(355, 113)
~~~

或是用来恢复被表示为一个浮点数的有理数：

    
    
~~~shell
>>> from math import pi, cos
>>> Fraction(cos(pi/3))
Fraction(4503599627370497, 9007199254740992)
>>> Fraction(cos(pi/3)).limit_denominator()
Fraction(1, 2)
>>> Fraction(1.1).limit_denominator()
Fraction(11, 10)
~~~

__floor__()¶

    

~~~
返回最大的 [`int`](functions.md#int "int") `<= self`。 此方法也可通过 [`math.floor()`](math.md#math.floor "math.floor") 函数来使用：
~~~
    
    
~~~shell
>>> from math import floor
>>> floor(Fraction(355, 113))
3
~~~

__ceil__()¶

    

~~~
返回最小的 [`int`](functions.md#int "int") `>= self`。 此方法也可通过 [`math.ceil()`](math.md#math.ceil "math.ceil") 函数来使用。

__round__()¶

__round__( _ndigits_ )
~~~
    

~~~
第一个版本返回一个 [`int`](functions.md#int "int") 使得其值最接近 `self`，位值为二分之一时只对偶数舍入。第二个版本会将 `self` 舍入到最接近 `Fraction(1, 10**ndigits)` 的倍数（如果 `ndigits` 为负值则为逻辑运算），位值为二分之一时同样只对偶数舍入。 此方法也可通过 [`round()`](functions.md#round "round") 函数来使用。

__format__( _format_spec_ , _/_ )¶
~~~
    

~~~
通过 [`str.format()`](stdtypes.md#str.format "str.format") 方法、[`format()`](functions.md#format "format") 内置函数或 [格式化字符串字面值](../reference/lexical_analysis.md#f-strings) 为浮点风格的 `Fraction` 实例格式化提供支持。 支持 `"e"`, `"E"`, `"f"`, `"F"`, `"g"`, `"G"` 和 `"%"` 等表示类型。 对于这些表示类型，`Fraction` 对象 `x` 的格式化遵循 [格式规格迷你语言](string.md#formatspec) 小节中针对 [`float`](functions.md#float "float") 类型的规则说明。

这是一些例子:
~~~
    
    
~~~shell
>>> from fractions import Fraction
>>> format(Fraction(1, 7), '.40g')
'0.1428571428571428571428571428571428571429'
>>> format(Fraction('1234567.855'), '_.2f')
'1_234_567.86'
>>> f"{Fraction(355, 113):*>20.6e}"
'********3.141593e+00'
>>> old_price, new_price = 499, 672
>>> "{:.2%} price increase".format(Fraction(new_price, old_price) - 1)
'34.67% price increase'
~~~

参见

[`numbers`](numbers.md#module-numbers "numbers: Numeric abstract base classes \(Complex, Real, Integral, etc.\).") 模块

    

~~~
