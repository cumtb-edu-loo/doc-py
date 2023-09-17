# 内置类型¶

以下部分描述了解释器中内置的标准类型。

主要内置类型有数字、序列、映射、类、实例和异常。

有些多项集类是可变的。 它们用于添加、移除或重排其成员的方法将原地执行，并不返回特定的项，绝对不会返回多项集实例自身而是返回 `None`。

有些操作受多种对象类型的支持；特别地，实际上所有对象都可以比较是否相等、检测逻辑值，以及转换为字符串（使用 [`repr()`](functions.md#repr "repr") 函数或略有差异的 `str()` 函数）。 后一个函数是在对象由 [`print()`](functions.md#print "print") 函数输出时被隐式地调用的。

## 逻辑值检测¶

任何对象都可以进行逻辑值的检测，以便在 [`if`](compound_stmts.md#if) 或 [`while`](compound_stmts.md#while) 作为条件或是作为下文所述布尔运算的操作数来使用。

在默认情况下，一个对象会被视为具有真值，除非其所属的类定义了 [`__bool__()`](datamodel.md#object.__bool__ "object.__bool__") 方法且在调用时返回 `False` 或是定义了 `__len__()` 方法且在调用时返回零。 [1] 这里基本完整地列出了会被视为假值的内置对象:

>   * 被定义为假值的常量: `None` 和 `False`

  * 任何数值类型的零: `0`, `0.0`, `0j`, `Decimal(0)`, `Fraction(0, 1)`

  * 空的序列和多项集: `''`, `()`, `[]`, `{}`, `set()`, `range(0)`

产生布尔值结果的运算和内置函数总是返回 `0` 或 `False` 作为假值，`1` 或 `True` 作为真值，除非另行说明。 （重要例外：布尔运算 `or` 和 `and` 总是返回其中一个操作数。）

## 布尔运算 --- `and`, `or`, `not`¶

这些属于布尔运算，按优先级升序排列:

运算

|

结果：

|

备注  
  
---|---|---  
  
`x or y`

|

如果 _x_ 为真值，则 _x_ ，否则 _y_

|

(1)  
  
`x and y`

|

if _x_ is false, then _x_ , else _y_

|

(2)  
  
`not x`

|

if _x_ is false, then `True`, else `False`

|

(3)  
  
注释：

  1. 这是个短路运算符，因此只有在第一个参数为假值时才会对第二个参数求值。

  2. 这是个短路运算符，因此只有在第一个参数为真值时才会对第二个参数求值。

  3. `not` 的优先级比非布尔运算符低，因此 `not a == b` 会被解读为 `not (a == b)` 而 `a == not b` 会引发语法错误。

## 比较运算¶

在 Python 中有八种比较运算符。 它们的优先级相同（比布尔运算的优先级高）。 比较运算可以任意串连；例如，`x < y <= z` 等价于 `x < y and y <= z`，前者的不同之处在于 _y_ 只被求值一次（但在两种情况下当 `x < y` 结果为假值时 _z_ 都不会被求值）。

此表格汇总了比较运算:

运算

|

含意  
  
---|---  
  
`<`

|

严格小于  
  
`<=`

|

小于或等于  
  
`>`

|

严格大于  
  
`>=`

|

大于或等于  
  
`==`

|

等于  
  
`!=`

|

不等于  
  
`is`

|

对象标识  
  
`is not`

|

否定的对象标识  
  
除不同的数字类型外，不同类型的对象不能进行相等比较。`==` 运算符总有定义，但对于某些对象类型（例如，类对象），它等于 [`is`](expressions.md#is) 。其他 `<`、`<=`、`>` 和 `>=` 运算符仅在有意义的地方定义。例如，当参与比较的参数之一为复数时，它们会抛出 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError") 异常。

具有不同标识的类的实例比较结果通常为不相等，除非类定义了 [`__eq__()`](datamodel.md#object.__eq__ "object.__eq__") 方法。

一个类的实例不能与相同类的其他实例或其他类型的对象进行排序，除非定义该类定义了足够多的方法，包括 [`__lt__()`](datamodel.md#object.__lt__ "object.__lt__"), [`__le__()`](datamodel.md#object.__le__ "object.__le__"), [`__gt__()`](datamodel.md#object.__gt__ "object.__gt__") 以及 [`__ge__()`](datamodel.md#object.__ge__ "object.__ge__") (而如果你想实现常规意义上的比较操作，通常只要有 [`__lt__()`](datamodel.md#object.__lt__ "object.__lt__") 和 [`__eq__()`](datamodel.md#object.__eq__ "object.__eq__") 就可以了)。

[`is`](expressions.md#is) 和 [`is not`](expressions.md#is-not) 运算符无法自定义；并且它们可以被应用于任意两个对象而不会引发异常。

还有两种具有相同语法优先级的运算 [`in`](expressions.md#in) 和 [`not in`](expressions.md#not-in)，它们被 [iterable](../glossary.md#term-iterable) 或实现了 `__contains__()` 方法的类型所支持。

## 数字类型 --- [`int`](functions.md#int "int"), [`float`](functions.md#float "float"), [`complex`](functions.md#complex "complex")¶

存在三种不同的数字类型: _整数_ , _浮点数_ 和 _复数_ 。 此外，布尔值属于整数的子类型。 整数具有无限的精度。 浮点数通常使用 C 中的 double 来实现；有关你的程序运行所在机器上浮点数的精度和内部表示法可在 [`sys.float_info`](3.标准库/sys.md#sys.float_info "sys.float_info") 中查看。 复数包含实部和虚部，分别以一个浮点数表示。 要从一个复数 _z_ 中提取这两个部分，可使用 `z.real` 和 `z.imag`。 （标准库包含附加的数字类型，如表示有理数的 [`fractions.Fraction`](fractions.md#fractions.Fraction "fractions.Fraction") 以及以用户定制精度表示浮点数的 [`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal")。）

数字是由数字字面值或内置函数与运算符的结果来创建的。 不带修饰的整数字面值（包括十六进制、八进制和二进制数）会生成整数。 包含小数点或幂运算符的数字字面值会生成浮点数。 在数字字面值末尾加上 `'j'` 或 `'J'` 会生成虚数（实部为零的复数），你可以将其与整数或浮点数相加来得到具有实部和虚部的复数。

Python 完全支持混合运算：当一个二元算术运算符的操作数有不同数值类型时，"较窄"类型的操作数会拓宽到另一个操作数的类型，其中整数比浮点数窄，浮点数比复数窄。不同类型的数字之间的比较，同比较这些数字的精确值一样。[2]

构造函数 [`int()`](functions.md#int "int")、 [`float()`](functions.md#float "float") 和 [`complex()`](functions.md#complex "complex") 可以用来构造特定类型的数字。

所有数字类型（复数除外）都支持下列运算（有关运算优先级，请参阅：[运算符优先级](expressions.md#operator-summary)）:

运算

|

结果：

|

备注

|

完整文档  
  
---|---|---|---  
  
`x + y`

|

_x_ 和 _y_ 的和

|  |  
  
`x - y`

|

_x_ 和 _y_ 的差

|  |  
  
`x * y`

|

_x_ 和 _y_ 的乘积

|  |  
  
`x / y`

|

_x_ 和 _y_ 的商

|  |  
  
`x // y`

|

_x_ 和 _y_ 的商数

|

(1)(2)

|  
  
`x % y`

|

`x / y` 的余数

|

(2)

|  
  
`-x`

|

_x_ 取反

|  |  
  
`+x`

|

_x_ 不变

|  |  
  
`abs(x)`

|

_x_ 的绝对值或大小

|  |

[`abs()`](functions.md#abs "abs")  
  
`int(x)`

|

将 _x_ 转换为整数

|

(3)(6)

|

[`int()`](functions.md#int "int")  
  
`float(x)`

|

将 _x_ 转换为浮点数

|

(4)(6)

|

[`float()`](functions.md#float "float")  
  
`complex(re, im)`

|

一个带有实部 _re_ 和虚部 _im_ 的复数。 _im_ 默认为0。

|

(6)

|

[`complex()`](functions.md#complex "complex")  
  
`c.conjugate()`

|

复数 _c_ 的共轭

|  |  
  
`divmod(x, y)`

|

`(x // y, x % y)`

|

(2)

|

[`divmod()`](functions.md#divmod "divmod")  
  
`pow(x, y)`

|

_x_ 的 _y_ 次幂

|

(5)

|

[`pow()`](functions.md#pow "pow")  
  
`x ** y`

|

_x_ 的 _y_ 次幂

|

(5)

|  
  
注释：

  1. 也称为整数除法。 对于 [`int`](functions.md#int "int") 类型的操作数，结果的类型为 [`int`](functions.md#int "int")。 对于 [`float`](functions.md#float "float") 类型的操作数，结果的类型为 [`float`](functions.md#float "float")。 总的说来，结果是一个整数，但结果的类型不一定为 [`int`](functions.md#int "int")。 结果总是向负无穷的方向舍入: `1//2` 为``0``，`(-1)//2` 为 `-1`，`1//(-2)` 为 `-1`，`(-1)//(-2)` 为 `0`。

  2. 不可用于复数。 而应在适当条件下使用 [`abs()`](functions.md#abs "abs") 转换为浮点数。

  3. 从 [`float`](functions.md#float "float") 转换为 [`int`](functions.md#int "int") 将会执行截断，丢弃掉小数部分。 请参阅 [`math.floor()`](math.md#math.floor "math.floor") 和 [`math.ceil()`](math.md#math.ceil "math.ceil") 函数了解替代的转换方式。

  4. float 也接受字符串 "nan" 和附带可选前缀 "+" 或 "-" 的 "inf" 分别表示非数字 (NaN) 以及正或负无穷。

  5. Python 将 `pow(0, 0)` 和 `0 ** 0` 定义为 `1`，这是编程语言的普遍做法。

  6. 接受的数字字面值包括数码 `0` 到 `9` 或任何等效的 Unicode 字符（具有 `Nd` 特征属性的代码点）。

请参阅 [Unicode 标准](https://unicode.org/Public/UNIDATA/extracted/DerivedNumericType.txt) 了解具有 `Nd` 特征属性的码位完整列表。

所有 [`numbers.Real`](numbers.md#numbers.Real "numbers.Real") 类型 ([`int`](functions.md#int "int") 和 [`float`](functions.md#float "float")) 还包括下列运算:

运算

|

结果：  
  
---|---  
  
[`math.trunc(x)`](math.md#math.trunc "math.trunc")

|

_x_ 截断为 [`Integral`](numbers.md#numbers.Integral "numbers.Integral")  
  
[`round(x[, n])`](functions.md#round "round")

|

_x_ 舍入到 _n_ 位小数，半数值会舍入到偶数。 如果省略 _n_ ，则默认为 0。  
  
[`math.floor(x)`](math.md#math.floor "math.floor")

|

<= _x_ 的最大 [`Integral`](numbers.md#numbers.Integral "numbers.Integral")  
  
[`math.ceil(x)`](math.md#math.ceil "math.ceil")

|

>= _x_ 的最小 [`Integral`](numbers.md#numbers.Integral "numbers.Integral")  
  
有关更多的数字运算请参阅 [`math`](math.md#module-math "math: Mathematical functions \(sin\(\) etc.\).") 和 [`cmath`](cmath.md#module-cmath "cmath: Mathematical functions for complex numbers.") 模块。

### 整数类型的按位运算¶

按位运算只对整数有意义。 计算按位运算的结果，就相当于使用无穷多个二进制符号位对二的补码执行操作。

二进制按位运算的优先级全都低于数字运算，但又高于比较运算；一元运算 `~` 具有与其他一元算术运算 (`+` and `-`) 相同的优先级。

此表格是以优先级升序排序的按位运算列表:

运算

|

结果：

|

备注  
  
---|---|---  
  
`x | y`

|

_x_ 和 _y_ 按位 _或_

|

(4)  
  
`x ^ y`

|

_x_ 和 _y_ 按位 _异或_

|

(4)  
  
`x & y`

|

_x_ 和 _y_ 按位 _与_

|

(4)  
  
`x << n`

|

_x_ 左移 _n_ 位

|

(1)(2)  
  
`x >> n`

|

_x_ 右移 _n_ 位

|

(1)(3)  
  
`~x`

|

_x_ 逐位取反

|  
  
注释：

  1. 负的移位数是非法的，会导致引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError")。

  2. 左移 _n_ 位等价于乘以 `pow(2, n)` 。

  3. 右移 _n_ 位等价于除以 `pow(2, n)` ，作向下取整除法。

  4. 使用带有至少一个额外符号扩展位的有限个二进制补码表示（有效位宽度为 `1 + max(x.bit_length(), y.bit_length())` 或以上）执行这些计算就足以获得相当于有无数个符号位时的同样结果。

### 整数类型的附加方法¶

int 类型实现了 [`numbers.Integral`](numbers.md#numbers.Integral "numbers.Integral") [abstract base class](../glossary.md#term-abstract-base-class)。 此外，它还提供了其他几个方法:

int.bit_length()¶

    

~~~
返回以二进制表示一个整数所需要的位数，不包括符号位和前面的零:
~~~
    
    
~~~shell
>>> n = -37
>>> bin(n)
'-0b100101'
>>> n.bit_length()
6
~~~

更准确地说，如果 `x` 非零，则 `x.bit_length()` 是使得 `2**(k-1) <= abs(x) < 2**k` 的唯一正整数 `k`。 同样地，当 `abs(x)` 小到足以具有正确的舍入对数时，则 `k = 1 + int(log(abs(x), 2))`。 如果 `x` 为零，则 `x.bit_length()` 返回 `0`。

等价于:

    
    
~~~python
def bit_length(self):
    s = bin(self)       # binary representation:  bin(-37) --> '-0b100101'
    s = s.lstrip('-0b') # remove leading zeros and minus sign
    return len(s)       # len('100101') --> 6
~~~

在 3.1 版本加入.

int.bit_count()¶

    

~~~
返回整数的绝对值的二进制表示中 1 的个数。也被称为 population count。示例:
~~~
    
    
~~~shell
>>> n = 19
>>> bin(n)
'0b10011'
>>> n.bit_count()
3
>>> (-n).bit_count()
3
~~~

等价于:

    
    
~~~python
def bit_count(self):
    return bin(self).count("1")
~~~

在 3.10 版本加入.

int.to_bytes( _length =1_, _byteorder ='big'_, _*_ , _signed =False_)¶

    

~~~
返回表示一个整数的字节数组。
~~~
    
    
~~~shell
>>> (1024).to_bytes(2, byteorder='big')
b'\x04\x00'
>>> (1024).to_bytes(10, byteorder='big')
b'\x00\x00\x00\x00\x00\x00\x00\x00\x04\x00'
>>> (-1024).to_bytes(10, byteorder='big', signed=True)
b'\xff\xff\xff\xff\xff\xff\xff\xff\xfc\x00'
>>> x = 1000
>>> x.to_bytes((x.bit_length() + 7) // 8, byteorder='little')
b'\xe8\x03'
~~~

整数会使用 _length_ 个字节来表示，默认为 1。 如果整数不能用给定的字节数来表示则会引发 [`OverflowError`](3.标准库/exceptions.md#OverflowError "OverflowError")。

_byteorder_ 参数确定用于表示整数的字节顺序，默认为 `"big"`。 如果 _byteorder_ 为 `"big"`，则最高位字节放在字节数组的开头。 如果 _byteorder_ 为 `"little"`，则最高位字节放在字节数组的末尾。

_signed_ 参数确定是否使用二的补码来表示整数。 如果 _signed_ 为 `False` 并且给出的是负整数，则会引发 [`OverflowError`](3.标准库/exceptions.md#OverflowError "OverflowError")。 _signed_ 的默认值为 `False`。

默认值可用于方便地将整数转为一个单字节对象:

    
    
~~~shell
>>> (65).to_bytes()
b'A'
~~~

但是，当使用默认参数时，请不要试图转换大于 255 的值否则会引发 [`OverflowError`](3.标准库/exceptions.md#OverflowError "OverflowError")。

等价于:

    
    
~~~python
def to_bytes(n, length=1, byteorder='big', signed=False):
    if byteorder == 'little':
        order = range(length)
    elif byteorder == 'big':
        order = reversed(range(length))
    else:
        raise ValueError("byteorder must be either 'little' or 'big'")

    return bytes((n >> i*8) & 0xff for i in order)
~~~

在 3.2 版本加入.

在 3.11 版本发生变更: 添加了 `length` 和 `byteorder` 的默认参数值。

_classmethod _int.from_bytes( _bytes_ , _byteorder ='big'_, _*_ , _signed =False_)¶

    

~~~
返回由给定字节数组所表示的整数。
~~~
    
    
~~~shell
>>> int.from_bytes(b'\x00\x10', byteorder='big')
16
>>> int.from_bytes(b'\x00\x10', byteorder='little')
4096
>>> int.from_bytes(b'\xfc\x00', byteorder='big', signed=True)
-1024
>>> int.from_bytes(b'\xfc\x00', byteorder='big', signed=False)
64512
>>> int.from_bytes([255, 0, 0], byteorder='big')
16711680
~~~

_bytes_ 参数必须为一个 [bytes-like object](../glossary.md#term-bytes-like-object) 或是生成字节的可迭代对象。

_byteorder_ 参数确定用于表示整数的字节顺序，默认为 `"big"`。 如果 _byteorder_ 为 `"big"`，则最高位字节放在字节数组的开头。 如果 _byteorder_ 为 `"little"`，则最高位字节放在字节数组的末尾。 要请求主机系统上的原生字节顺序，请使用 [`sys.byteorder`](3.标准库/sys.md#sys.byteorder "sys.byteorder") 作为字节顺序值。

_signed_ 参数指明是否使用二的补码来表示整数。

等价于:

    
    
~~~python
def from_bytes(bytes, byteorder='big', signed=False):
    if byteorder == 'little':
        little_ordered = list(bytes)
    elif byteorder == 'big':
        little_ordered = list(reversed(bytes))
    else:
        raise ValueError("byteorder must be either 'little' or 'big'")

    n = sum(b << i*8 for i, b in enumerate(little_ordered))
    if signed and little_ordered and (little_ordered[-1] & 0x80):
        n -= 1 << 8*len(little_ordered)

    return n
~~~

在 3.2 版本加入.

在 3.11 版本发生变更: 添加了 `byteorder` 的默认参数值。

int.as_integer_ratio()¶

    

~~~
返回一对整数，其比率正好等于原整数并且分母为正数。 整数的比率总是用这个整数本身作为分子并以 `1` 作为分母。

在 3.8 版本加入.

int.is_integer()¶
~~~
    

~~~
返回 `True`。 存在于兼容 `float.is_integer()` 的鸭子类型。

在 3.12 版本加入.

### 浮点类型的附加方法¶

float 类型实现了 [`numbers.Real`](numbers.md#numbers.Real "numbers.Real") [abstract base class](../glossary.md#term-abstract-base-class)。 float 还具有以下附加方法。

float.as_integer_ratio()¶
~~~
    

~~~
返回一对整数，其比率正好等于原浮点数。 该比率为最简形式且分母为正值。 无穷大会引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError") 而 NaN 则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

float.is_integer()¶
~~~
    

~~~
如果 float 实例可用有限位整数表示则返回 `True`，否则返回 `False`:
~~~
    
    
~~~shell
>>> (-2.0).is_integer()
True
>>> (3.2).is_integer()
False
~~~

两个方法均支持与十六进制数字符串之间的转换。 由于 Python 浮点数在内部存储为二进制数，因此浮点数与 _十进制数_ 字符串之间的转换往往会导致微小的舍入错误。 而十六进制数字符串却允许精确地表示和描述浮点数。 这在进行调试和数值工作时非常有用。

float.hex()¶

    

~~~
以十六进制字符串的形式返回一个浮点数表示。 对于有限浮点数，这种表示法将总是包含前导的 `0x` 和尾随的 `p` 加指数。

_classmethod _float.fromhex( _s_ )¶
~~~
    

~~~
返回以十六进制字符串 _s_ 表示的浮点数的类方法。 字符串 _s_ 可以带有前导和尾随的空格。

请注意 `float.hex()` 是实例方法，而 `float.fromhex()` 是类方法。

十六进制字符串采用的形式为:
~~~
    
    
~~~
[sign] ['0x'] integer ['.' fraction] ['p' exponent]
~~~

可选的 `sign` 可以是 `+` 或 `-`，`integer` 和 `fraction` 是十六进制数码组成的字符串，`exponent` 是带有可选前导符的十进制整数。 大小写没有影响，在 integer 或 fraction 中必须至少有一个十六进制数码。 此语法类似于 C99 标准的 6.4.4.2 小节中所描述的语法，也是 Java 1.5 以上所使用的语法。 特别地，`float.hex()` 的输出可以用作 C 或 Java 代码中的十六进制浮点数字面值，而由 C 的 `%a` 格式字符或 Java 的 `Double.toHexString` 所生成的十六进制数字符串由为 `float.fromhex()` 所接受。

请注意 exponent 是十进制数而非十六进制数，它给出要与系数相乘的 2 的幂次。 例如，十六进制数字符串 `0x3.a7p10` 表示浮点数 `(3 + 10./16 + 7./16**2) * 2.0**10` 即 `3740.0`:

    
    
~~~shell
>>> float.fromhex('0x3.a7p10')
3740.0
~~~

对 `3740.0` 应用反向转换会得到另一个代表相同数值的十六进制数字符串:

    
    
~~~shell
>>> float.hex(3740.0)
'0x1.d380000000000p+11'
~~~

### 数字类型的哈希运算¶

对于可能为不同类型的数字 `x` 和 `y`，要求 `x == y` 时必定 `hash(x) == hash(y)` (详情参见 [`__hash__()`](datamodel.md#object.__hash__ "object.__hash__") 方法的文档)。 为了便于在各种数字类型 (包括 [`int`](functions.md#int "int"), [`float`](functions.md#float "float"), [`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal") 和 [`fractions.Fraction`](fractions.md#fractions.Fraction "fractions.Fraction")) 上实现并保证效率，Python 对数字类型的哈希运算是基于为任意有理数定义统一的数学函数，因此该运算对 [`int`](functions.md#int "int") 和 [`fractions.Fraction`](fractions.md#fractions.Fraction "fractions.Fraction") 的全部实例，以及 [`float`](functions.md#float "float") 和 [`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal") 的全部有限实例均可用。 从本质上说，此函数是通过以一个固定质数 `P` 进行 `P` 降模给出的。 `P` 的值在 Python 中可以 [`sys.hash_info`](3.标准库/sys.md#sys.hash_info "sys.hash_info") 的 `modulus` 属性的形式被访问。

**CPython 实现细节：** 目前所用的质数设定，在 C long 为 32 位的机器上 `P = 2**31 - 1` 而在 C long 为 64 位的机器上 `P = 2**61 - 1`。

详细规则如下所述:

  * 如果 `x = m / n` 是一个非负的有理数且 `n` 不可被 `P` 整除，则定义 `hash(x)` 为 `m * invmod(n, P) % P`，其中 `invmod(n, P)` 是对 `n` 模 `P` 取反。

  * 如果 `x = m / n` 是一个非负的有理数且 `n` 可被 `P` 整除（但 `m` 不能）则 `n` 不能对 `P` 降模，以上规则不适用；在此情况下则定义 `hash(x)` 为常数值 `sys.hash_info.inf`。

  * 如果 `x = m / n` 是一个负的有理数则定义 `hash(x)` 为 `-hash(-x)`。 如果结果哈希值为 `-1` 则将其替换为 `-2`。

  * 特殊值 `sys.hash_info.inf` 和 `-sys.hash_info.inf` 分别用于正无穷或负无穷的哈希值。

  * 对于一个 [`complex`](functions.md#complex "complex") 值 `z`，会通过计算 `hash(z.real) + sys.hash_info.imag * hash(z.imag)` 将实部和虚部的哈希值结合起来，并进行降模 `2**sys.hash_info.width` 以使其处于 `range(-2**(sys.hash_info.width - 1), 2**(sys.hash_info.width - 1))` 范围之内。 同样地，如果结果为 `-1` 则将其替换为 `-2`。

为了阐明上述规则，这里有一些等价于内置哈希算法的 Python 代码示例，可用于计算有理数、[`float`](functions.md#float "float") 或 [`complex`](functions.md#complex "complex") 的哈希值:

    
    
~~~
import sys, math

def hash_fraction(m, n):
    """Compute the hash of a rational number m / n.

    Assumes m and n are integers, with n positive.
    Equivalent to hash(fractions.Fraction(m, n)).

    """
    P = sys.hash_info.modulus
    # Remove common factors of P.  (Unnecessary if m and n already coprime.)
    while m % P == n % P == 0:
        m, n = m // P, n // P

    if n % P == 0:
        hash_value = sys.hash_info.inf
    else:
        # Fermat's Little Theorem: pow(n, P-1, P) is 1, so
        # pow(n, P-2, P) gives the inverse of n modulo P.
        hash_value = (abs(m) % P) * pow(n, P - 2, P) % P
    if m < 0:
        hash_value = -hash_value
    if hash_value == -1:
        hash_value = -2
    return hash_value

def hash_float(x):
    """Compute the hash of a float x."""

    if math.isnan(x):
        return object.__hash__(x)
    elif math.isinf(x):
        return sys.hash_info.inf if x > 0 else -sys.hash_info.inf
    else:
        return hash_fraction(*x.as_integer_ratio())

def hash_complex(z):
    """Compute the hash of a complex number z."""

    hash_value = hash_float(z.real) + sys.hash_info.imag * hash_float(z.imag)
    # do a signed reduction modulo 2**sys.hash_info.width
    M = 2**(sys.hash_info.width - 1)
    hash_value = (hash_value & (M - 1)) - (hash_value & M)
    if hash_value == -1:
        hash_value = -2
    return hash_value
~~~

## 布尔类型 - [`bool`](functions.md#bool "bool")¶

代表真值的布尔对象。 [`bool`](functions.md#bool "bool") 类型只有两个常量实例: `True` 和 `False`。

内置函数 [`bool()`](functions.md#bool "bool") 可将任意值转换为布尔值，如果该值可以被解读为逻辑值的话（参见上面的 逻辑值检测 小节）。

对于逻辑运算，请使用 布尔运算符 `and`, `or` 和 `not`。 当于两个布尔值应用按位运算符 `&`, `|`, `^` 时，它们将返回一个等价于逻辑运算 "与", "或", "异或" 的布尔值。 但是，更推荐使用逻辑运算符 `and`, `or` 和 `!=` 而不是 `&`, `|` 和 `^`。

自 3.12 版本弃用: 按位取反运算符 `~` 已被弃用并将在 Python 3.14 中引发异常。

[`bool`](functions.md#bool "bool") 是 [`int`](functions.md#int "int") 的子类 (参见 数字类型 --- int, float, complex)。 在许多数字场景下，`False` 和 `True` 的行为分别与整数 0 和 1 类似。 但是，不建议这样使用；请使用 [`int()`](functions.md#int "int") 显式地执行转换。

## 迭代器类型¶

Python 支持在容器中进行迭代的概念。 这是通过使用两个单独方法来实现的；它们被用于允许用户自定义类对迭代的支持。 将在下文中详细描述的序列总是支持迭代方法。

容器对象要提供 [iterable](../glossary.md#term-iterable) 支持，必须定义一个方法:

container.__iter__()¶

    

~~~
返回一个 [iterator](../glossary.md#term-iterator) 对象。 该对象需要支持下文所述的迭代器协议。 如果容器支持不同的迭代类型，则可以提供额外的方法来专门地请求不同迭代类型的迭代器。 （支持多种迭代形式的对象的例子有同时支持广度优先和深度优先遍历的树结果。） 此方法对应于 Python/C API 中 Python 对象类型结构体的 [`tp_iter`](../c-api/typeobj.md#c.PyTypeObject.tp_iter "PyTypeObject.tp_iter") 槽位。

迭代器对象自身需要支持以下两个方法，它们共同组成了 _迭代器协议_ :

iterator.__iter__()¶
~~~
    

~~~
返回 [iterator](../glossary.md#term-iterator) 对象本身。 这是同时允许容器和迭代器配合 [`for`](../reference/compound_stmts.md#for) 和 [`in`](../reference/expressions.md#in) 语句使用所必须的。 此方法对应于 Python/C API 中 Python 对象类型结构体的 [`tp_iter`](../c-api/typeobj.md#c.PyTypeObject.tp_iter "PyTypeObject.tp_iter") 槽位。

iterator.__next__()¶
~~~
    

~~~
[iterator](../glossary.md#term-iterator) 中返回下一项。 如果已经没有可返回的项，则会引发 [`StopIteration`](exceptions.md#StopIteration "StopIteration") 异常。 此方法对应于 Python/C API 中 Python 对象类型结构体的 [`tp_iternext`](../c-api/typeobj.md#c.PyTypeObject.tp_iternext "PyTypeObject.tp_iternext") 槽位。

Python 定义了几种迭代器对象以支持对一般和特定序列类型、字典和其他更特别的形式进行迭代。 除了迭代器协议的实现，特定类型的其他性质对迭代操作来说都不重要。

一旦迭代器的 `__next__()` 方法引发了 [`StopIteration`](exceptions.md#StopIteration "StopIteration")，它必须一直对后续调用引发同样的异常。 不遵循此行为特性的实现将无法正常使用。

### 生成器类型¶

Python 的 [generator](../glossary.md#term-generator) 提供了一种实现迭代器协议的便捷方式。 如果容器对象 `__iter__()` 方法被实现为一个生成器，它将自动返回一个迭代器对象（从技术上说是一个生成器对象），该对象提供 `__iter__()` 和 [`__next__()`](../reference/expressions.md#generator.__next__ "generator.__next__") 方法。 有关生成器的更多信息可以参阅 [yield 表达式的文档](../reference/expressions.md#yieldexpr)。

## 序列类型 --- `list`, `tuple`, `range`¶

有三种基本序列类型：list, tuple 和 range 对象。 为处理 二进制数据 和 文本字符串 而特别定制的附加序列类型会在专门的小节中描述。

### 通用序列操作¶

大多数序列类型，包括可变类型和不可变类型都支持下表中的操作。 [`collections.abc.Sequence`](collections.abc.md#collections.abc.Sequence "collections.abc.Sequence") ABC 被提供用来更容易地在自定义序列类型上正确地实现这些操作。

此表按优先级升序列出了序列操作。 在表格中， _s_ 和 _t_ 是具有相同类型的序列， _n_ , _i_ , _j_ 和 _k_ 是整数而 _x_ 是任何满足 _s_ 所规定的类型和值限制的任意对象。

`in` 和 `not in` 操作具有与比较操作相同的优先级。 `+` (拼接) 和 `*` (重复) 操作具有与对应数值运算相同的优先级。 [3]

运算

|

结果：

|

备注  
  
---|---|---  
  
`x in s`

|

如果 _s_ 中的某项等于 _x_ 则结果为 `True`，否则为 `False`

|

(1)  
  
`x not in s`

|

如果 _s_ 中的某项等于 _x_ 则结果为 `False`，否则为 `True`

|

(1)  
  
`s + t`

|

_s_ 与 _t_ 相拼接

|

(6)(7)  
  
`s * n` 或 `n * s`

|

相当于 _s_ 与自身进行 _n_ 次拼接

|

(2)(7)  
  
`s[i]`

|

_s_ 的第 _i_ 项，起始为 0

|

(3)  
  
`s[i:j]`

|

_s_ 从 _i_ 到 _j_ 的切片

|

(3)(4)  
  
`s[i:j:k]`

|

_s_ 从 _i_ 到 _j_ 步长为 _k_ 的切片

|

(3)(5)  
  
`len(s)`

|

_s_ 的长度

|  
  
`min(s)`

|

_s_ 的最小项

|  
  
`max(s)`

|

_s_ 的最大项

|  
  
`s.index(x[, i[, j]])`

|

_x_ 在 _s_ 中首次出现项的索引号（索引号在 _i_ 或其后且在 _j_ 之前）

|

(8)  
  
`s.count(x)`

|

_x_ 在 _s_ 中出现的总次数

|  
  
相同类型的序列也支持比较。 特别地，tuple 和 list 的比较是通过比较对应元素的字典顺序。 这意味着想要比较结果相等，则每个元素比较结果都必须相等，并且两个序列长度必须相同。 （完整细节请参阅语言参考的 [比较运算](../reference/expressions.md#comparisons) 部分。）

可变序列的正向和逆向迭代器使用一个索引来访问值。 即使底层序列被改变该索引也将持续向前（或向后）步进。 迭代器只有在遇到 [`IndexError`](exceptions.md#IndexError "IndexError") 或 a [`StopIteration`](exceptions.md#StopIteration "StopIteration") 时才会终结（或是当索引降至零以下）。

注释：

  1. 虽然 `in` 和 `not in` 操作在通常情况下仅被用于简单的成员检测，某些专门化序列 (例如 `str`, `bytes` 和 `bytearray`) 也使用它们进行子序列检测:
    
        >>> "gg" in "eggs"
    True
~~~

  2. 小于 `0` 的 _n_ 值会被当作 `0` 来处理 (生成一个与 _s_ 同类型的空序列)。 请注意序列 _s_ 中的项并不会被拷贝；它们会被多次引用。 这一点经常会令 Python 编程新手感到困扰；例如:
    
        >>> lists = [[]] * 3
    >>> lists
    [[], [], []]
    >>> lists[0].append(3)
    >>> lists
    [[3], [3], [3]]
    

具体的原因在于 `[[]]` 是一个包含了一个空列表的单元素列表，所以 `[[]] * 3` 结果中的三个元素都是对这一个空列表的引用。 修改 `lists` 中的任何一个元素实际上都是对这一个空列表的修改。 你可以用以下方式创建以不同列表为元素的列表:

    
        >>> lists = [[] for i in range(3)]
    >>> lists[0].append(3)
    >>> lists[1].append(5)
    >>> lists[2].append(7)
    >>> lists
    [[3], [5], [7]]
    

进一步的解释可以在 FAQ 条目 [如何创建多维列表？](programming.md#faq-multidimensional-list) 中查看。

  3. 如果 _i_ 或 _j_ 为负值，则索引顺序是相对于序列 _s_ 的末尾: 索引号会被替换为 `len(s) + i` 或 `len(s) + j`。 但要注意 `-0` 仍然为 `0`。

  4. _s_ 从 _i_ 到 _j_ 的切片被定义为所有满足 `i <= k < j` 的索引号 _k_ 的项组成的序列。 如果 _i_ 或 _j_ 大于 `len(s)`，则使用 `len(s)`。 如果 _i_ 被省略或为 `None`，则使用 `0`。 如果 _j_ 被省略或为 `None`，则使用 `len(s)`。 如果 _i_ 大于等于 _j_ ，则切片为空。

  5. _s_ 从 _i_ 到 _j_ 步长为 _k_ 的切片被定义为所有满足 `0 <= n < (j-i)/k` 的索引号 `x = i + n*k` 的项组成的序列。 换句话说，索引号为 `i`, `i+k`, `i+2*k`, `i+3*k`，以此类推，当达到 _j_ 时停止 (但一定不包括 _j_ )。 当 _k_ 为正值时， _i_ 和 _j_ 会被减至不大于 `len(s)`。 当 _k_ 为负值时， _i_ 和 _j_ 会被减至不大于 `len(s) - 1`。 如果 _i_ 或 _j_ 被省略或为 `None`，它们会成为“终止”值 (是哪一端的终止值则取决于 _k_ 的符号)。 请注意， _k_ 不可为零。 如果 _k_ 为 `None`，则当作 `1` 处理。

  6. 拼接不可变序列总是会生成新的对象。 这意味着通过重复拼接来构建序列的运行时开销将会基于序列总长度的乘方。 想要获得线性的运行时开销，你必须改用下列替代方案之一：

    * 如果拼接 `str` 对象，你可以构建一个列表并在最后使用 `str.join()` 或是写入一个 [`io.StringIO`](io.md#io.StringIO "io.StringIO") 实例并在结束时获取它的值

    * 如果拼接 `bytes` 对象，你可以类似地使用 `bytes.join()` 或 [`io.BytesIO`](io.md#io.BytesIO "io.BytesIO")，或者你也可以使用 `bytearray` 对象进行原地拼接。 `bytearray` 对象是可变的，并且具有高效的重分配机制

    * 如果拼接 `tuple` 对象，请改为扩展 `list` 类

    * 对于其它类型，请查看相应的文档

  7. 某些序列类型 (例如 `range`) 仅支持遵循特定模式的项序列，因此并不支持序列拼接或重复。

  8. 当 _x_ 在 _s_ 中找不到时 `index` 会引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError")。 不是所有实现都支持传入额外参数 _i_ 和 _j_ 。 这两个参数允许高效地搜索序列的子序列。 传入这两个额外参数大致相当于使用 `s[i:j].index(x)`，但是不会复制任何数据，并且返回的索引是相对于序列的开头而非切片的开头。

### 不可变序列类型¶

不可变序列类型普遍实现而可变序列类型未实现的唯一操作就是对 [`hash()`](functions.md#hash "hash") 内置函数的支持。

这种支持允许不可变类型，例如 `tuple` 实例被用作 `dict` 键，以及存储在 `set` 和 `frozenset` 实例中。

尝试对包含有不可哈希值的不可变序列进行哈希运算将会导致 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError")。

### 可变序列类型¶

以下表格中的操作是在可变序列类型上定义的。 [`collections.abc.MutableSequence`](collections.abc.md#collections.abc.MutableSequence "collections.abc.MutableSequence") ABC 被提供用来更容易地在自定义序列类型上正确实现这些操作。

表格中的 _s_ 是可变序列类型的实例， _t_ 是任意可迭代对象，而 _x_ 是符合对 _s_ 所规定类型与值限制的任何对象 (例如，`bytearray` 仅接受满足 `0 <= x <= 255` 值限制的整数)。

运算

|

结果：

|

备注  
  
---|---|---  
  
`s[i] = x`

|

将 _s_ 的第 _i_ 项替换为 _x_

|  
  
`s[i:j] = t`

|

将 _s_ 从 _i_ 到 _j_ 的切片替换为可迭代对象 _t_ 的内容

|  
  
`del s[i:j]`

|

等同于 `s[i:j] = []`

|  
  
`s[i:j:k] = t`

|

将 `s[i:j:k]` 的元素替换为 _t_ 的元素

|

(1)  
  
`del s[i:j:k]`

|

从列表中移除 `s[i:j:k]` 的元素

|  
  
`s.append(x)`

|

将 _x_ 添加到序列的末尾 (等同于 `s[len(s):len(s)] = [x]`)

|  
  
`s.clear()`

|

从 _s_ 中移除所有项 (等同于 `del s[:]`)

|

(5)  
  
`s.copy()`

|

创建 _s_ 的浅拷贝 (等同于 `s[:]`)

|

(5)  
  
`s.extend(t)` 或 `s += t`

|

用 _t_ 的内容扩展 _s_ (基本上等同于 `s[len(s):len(s)] = t`)

|  
  
`s *= n`

|

使用 _s_ 的内容重复 _n_ 次来对其进行更新

|

(6)  
  
`s.insert(i, x)`

|

在由 _i_ 给出的索引位置将 _x_ 插入 _s_ (等同于 `s[i:i] = [x]`)

|  
  
`s.pop()` 或 `s.pop(i)`

|

提取在 _i_ 位置上的项，并将其从 _s_ 中移除

|

(2)  
  
`s.remove(x)`

|

删除 _s_ 中第一个 `s[i]` 等于 _x_ 的项目。

|

(3)  
  
`s.reverse()`

|

就地将列表中的元素逆序。

|

(4)  
  
注释：

  1. _t_ 必须与它所替换的切片具有相同的长度。

  2. 可选参数 _i_ 默认为 `-1`，因此在默认情况下会移除并返回最后一项。

  3. 当在 _s_ 中找不到 _x_ 时 `remove()` 操作会引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError")。

  4. 当反转大尺寸序列时 `reverse()` 方法会原地修改该序列以保证空间经济性。 为提醒用户此操作是通过间接影响进行的，它并不会返回反转后的序列。

  5. 包括 `clear()` 和 `copy()` 是为了与不支持切片操作的可变容器 (例如 `dict` 和 `set`) 的接口保持一致。 `copy()` 不是 [`collections.abc.MutableSequence`](collections.abc.md#collections.abc.MutableSequence "collections.abc.MutableSequence") ABC 的一部分，但大多数具体的可变序列类都提供了它。

在 3.3 版本加入: `clear()` 和 `copy()` 方法。

  6. _n_ 值为一个整数，或是一个实现了 [`__index__()`](datamodel.md#object.__index__ "object.__index__") 的对象。 _n_ 值为零或负数将清空序列。 序列中的项不会被拷贝；它们会被多次引用，正如 通用序列操作 中有关 `s * n` 的说明。

### 列表¶

列表是可变序列，通常用于存放同类项目的集合（其中精确的相似程度将根据应用而变化）。

_class _list([ _iterable_ ])¶

    

~~~
可以用多种方式构建列表：

  * 使用一对方括号来表示空列表: `[]`

  * 使用方括号，其中的项以逗号分隔: `[a]`, `[a, b, c]`

  * 使用列表推导式: `[x for x in iterable]`

  * 使用类型的构造器: `list()` 或 `list(iterable)`

构造器将构造一个列表，其中的项与 _iterable_ 中的项具有相同的的值与顺序。 _iterable_ 可以是序列、支持迭代的容器或其它可迭代对象。 如果 _iterable_ 已经是一个列表，将创建并返回其副本，类似于 `iterable[:]`。 例如，`list('abc')` 返回 `['a', 'b', 'c']` 而 `list( (1, 2, 3) )` 返回 `[1, 2, 3]`。 如果没有给出参数，构造器将创建一个空列表 `[]`。

其它许多操作也会产生列表，包括 [`sorted()`](functions.md#sorted "sorted") 内置函数。

列表实现了所有 一般 和 可变 序列的操作。 列表还额外提供了以下方法：

sort( _*_ , _key =None_, _reverse =False_)¶
~~~
    

~~~
此方法会对列表进行原地排序，只使用 `<` 来进行各项间比较。 异常不会被屏蔽 —— 如果有任何比较操作失败，整个排序操作将失败（而列表可能会处于被部分修改的状态）。

`sort()` 接受两个仅限以关键字形式传入的参数 ([仅限关键字参数](../glossary.md#keyword-only-parameter)):

_key_ 指定带有一个参数的函数，用于从每个列表元素中提取比较键 (例如 `key=str.lower`)。 对应于列表中每一项的键会被计算一次，然后在整个排序过程中使用。 默认值 `None` 表示直接对列表项排序而不计算一个单独的键值。

可以使用 [`functools.cmp_to_key()`](functools.md#functools.cmp_to_key "functools.cmp_to_key") 将 2.x 风格的 _cmp_ 函数转换为 _key_ 函数。

_reverse_ 为一个布尔值。 如果设为 `True`，则每个列表元素将按反向顺序比较进行排序。

当顺序大尺寸序列时此方法会原地修改该序列以保证空间经济性。 为提醒用户此操作是通过间接影响进行的，它并不会返回排序后的序列（请使用 [`sorted()`](functions.md#sorted "sorted") 显示地请求一个新的已排序列表实例）。

`sort()` 方法确保是稳定的。 如果一个排序确保不会改变比较结果相等的元素的相对顺序就称其为稳定的 --- 这有利于进行多重排序（例如先按部门、再接薪级排序）。

有关排序示例和简要排序教程，请参阅 [排序指南](../howto/sorting.md#sortinghowto) 。

**CPython 实现细节：** 在一个列表被排序期间，尝试改变甚至进行检测也会造成未定义的影响。 Python 的 C 实现会在排序期间将列表显示为空，如果发现列表在排序期间被改变将会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

### 元组¶

元组是不可变序列，通常用于储存异构数据的多项集（例如由 [`enumerate()`](functions.md#enumerate "enumerate") 内置函数所产生的二元组）。 元组也被用于需要同构数据的不可变序列的情况（例如允许存储到 `set` 或 `dict` 的实例）。

_class _tuple([ _iterable_ ])¶
~~~
    

~~~
可以用多种方式构建元组：

  * 使用一对圆括号来表示空元组: `()`

  * 使用一个后缀的逗号来表示单元组: `a,` 或 `(a,)`

  * 使用以逗号分隔的多个项: `a, b, c` or `(a, b, c)`

  * 使用内置的 `tuple()`: `tuple()` 或 `tuple(iterable)`

构造器将构造一个元组，其中的项与 _iterable_ 中的项具有相同的值与顺序。 _iterable_ 可以是序列、支持迭代的容器或其他可迭代对象。 如果 _iterable_ 已经是一个元组，会不加改变地将其返回。 例如，`tuple('abc')` 返回 `('a', 'b', 'c')` 而 `tuple( [1, 2, 3] )` 返回 `(1, 2, 3)`。 如果没有给出参数，构造器将创建一个空元组 `()`。

请注意决定生成元组的其实是逗号而不是圆括号。 圆括号只是可选的，生成空元组或需要避免语法歧义的情况除外。 例如，`f(a, b, c)` 是在调用函数时附带三个参数，而 `f((a, b, c))` 则是在调用函数时附带一个三元组。

元组实现了所有 一般 序列的操作。

对于通过名称访问相比通过索引访问更清晰的异构数据多项集，[`collections.namedtuple()`](collections.md#collections.namedtuple "collections.namedtuple") 可能是比简单元组对象更为合适的选择。

### range 对象¶

`range` 类型表示不可变的数字序列，通常用于在 [`for`](../reference/compound_stmts.md#for) 循环中循环指定的次数。

_class _range( _stop_ )¶

_class _range( _start_ , _stop_ [, _step_ ])
~~~
    

~~~
range 构造器的参数必须为整数（可以是内置的 [`int`](functions.md#int "int") 或任何实现了 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__") 特殊方法的对象）。 如果省略 _step_ 参数，则默认为 `1`。 如果省略 _start_ 参数，则默认为 `0`。 如果 _step_ 为零，则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

如果 _step_ 为正值，确定 range `r` 内容的公式为 `r[i] = start + step*i` 其中 `i >= 0` 且 `r[i] < stop`。

如果 _step_ 为负值，确定 range 内容的公式仍然为 `r[i] = start + step*i`，但限制条件改为 `i >= 0` 且 `r[i] > stop`.

如果 `r[0]` 不符合值的限制条件，则该 range 对象为空。 range 对象确实支持负索引，但是会将其解读为从正索引所确定的序列的末尾开始索引。

元素绝对值大于 [`sys.maxsize`](sys.md#sys.maxsize "sys.maxsize") 的 range 对象是被允许的，但某些特性 (例如 [`len()`](functions.md#len "len")) 可能引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError")。

一些 range 对象的例子:
~~~
    
    
~~~shell
>>> list(range(10))
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> list(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> list(range(0, 30, 5))
[0, 5, 10, 15, 20, 25]
>>> list(range(0, 10, 3))
[0, 3, 6, 9]
>>> list(range(0, -10, -1))
[0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
>>> list(range(0))
[]
>>> list(range(1, 0))
[]
~~~

range 对象实现了 一般 序列的所有操作，但拼接和重复除外（这是由于 range 对象只能表示符合严格模式的序列，而重复和拼接通常都会违反这样的模式）。

start¶

    

~~~
_start_ 形参的值 (如果该形参未提供则为 `0`)

stop¶
~~~
    

~~~
_stop_ 形参的值

step¶
~~~
    

~~~
_step_ 形参的值 (如果该形参未提供则为 `1`)

`range` 类型相比常规 `list` 或 `tuple` 的优势在于一个 `range` 对象总是占用固定数量的（较小）内存，不论其所表示的范围有多大（因为它只保存了 `start`, `stop` 和 `step` 值，并会根据需要计算具体单项或子范围的值）。

range 对象实现了 [`collections.abc.Sequence`](collections.abc.md#collections.abc.Sequence "collections.abc.Sequence") ABC，提供如包含检测、元素索引查找、切片等特性，并支持负索引 (参见 序列类型 --- list, tuple, range):
~~~
    
    
~~~shell
>>> r = range(0, 20, 2)
>>> r
range(0, 20, 2)
>>> 11 in r
False
>>> 10 in r
True
>>> r.index(10)
5
>>> r[5]
10
>>> r[:5]
range(0, 10, 2)
>>> r[-1]
18
~~~

使用 `==` 和 `!=` 检测 range 对象是否相等是将其作为序列来比较。 也就是说，如果两个 range 对象表示相同的值序列就认为它们是相等的。 （请注意比较结果相等的两个 range 对象可能会具有不同的 `start`, `stop` 和 `step` 属性，例如 `range(0) == range(2, 1, 3)` 而 `range(0, 3, 2) == range(0, 4, 2)`。）

在 3.2 版本发生变更: 实现 Sequence ABC。 支持切片和负数索引。 使用 [`int`](functions.md#int "int") 对象在固定时间内进行成员检测，而不是逐一迭代所有项。

在 3.3 版本发生变更: 定义 '==' 和 '!=' 以根据 range 对象所定义的值序列来进行比较（而不是根据对象的标识）。

在 3.3 版本加入: `start`, `stop` 和 `step` 属性。

参见

  * [linspace recipe](https://code.activestate.com/recipes/579000/) 演示了如何实现一个延迟求值版本的适合浮点数应用的 range 对象。

## 文本序列类型 --- `str`¶

在 Python 中处理文本数据是使用 `str` 对象，也称为 _字符串_ 。 字符串是由 Unicode 码位构成的不可变 序列。 字符串字面值有多种不同的写法：

  * 单引号: `'允许包含有 "双" 引号'`

  * 双引号: `"允许嵌入 '单' 引号"`

  * 三重引号: `'''三重单引号'''`, `"""三重双引号"""`

使用三重引号的字符串可以跨越多行 —— 其中所有的空白字符都将包含在该字符串字面值中。

作为单一表达式组成部分，之间只由空格分隔的多个字符串字面值会被隐式地转换为单个字符串字面值。 也就是说，`("spam " "eggs") == "spam eggs"`。

请参阅 [字符串与字节串字面值](lexical_analysis.md#strings) 了解有关不同字符串字面值的更多信息，包括所支持的转义序列，以及使用 `r` ("raw") 前缀来禁用大多数转义序列的处理。

字符串也可以通过使用 `str` 构造器从其他对象创建。

由于不存在单独的“字符”类型，对字符串做索引操作将产生一个长度为 1 的字符串。 也就是说，对于一个非空字符串 _s_ , `s[0] == s[0:1]`。

不存在可变的字符串类型，但是 `str.join()` 或 [`io.StringIO`](io.md#io.StringIO "io.StringIO") 可以被被用来根据多个片段高效率地构建字符串。

在 3.3 版本发生变更: 为了与 Python 2 系列的向下兼容，再次允许字符串字面值使用 `u` 前缀。 它对字符串字面值的含义没有影响，并且不能与 `r` 前缀同时出现。

_class _str( _object =''_)¶

_class _str( _object =b''_, _encoding ='utf-8'_, _errors ='strict'_)

    

~~~
返回 _object_ 的 字符串 版本。 如果未提供 _object_ 则返回空字符串。 在其他情况下 `str()` 的行为取决于 _encoding_ 或 _errors_ 是否有给出，具体见下。

如果 _encoding_ 或 _errors_ 均未给出，则 `str(object)` 将返回 [`type(object).__str__(object)`](../reference/datamodel.md#object.__str__ "object.__str__")，这是 _object_ 的“非正式”而适合显示的字符串表示形式。 对于字符串对象，这就是该字符串本身。 如果 _object_ 没有 [`__str__()`](../reference/datamodel.md#object.__str__ "object.__str__") 方法，则 `str()` 将回退为返回 [`repr(object)`](functions.md#repr "repr")。

如果 _encoding_ 或 _errors_ 至少给出其中之一，则 _object_ 应该是一个 [bytes-like object](../glossary.md#term-bytes-like-object) (例如 `bytes` 或 `bytearray`)。 在此情况下，如果 _object_ 是一个 `bytes` (或 `bytearray`) 对象，则 `str(bytes, encoding, errors)` 等价于 `bytes.decode(encoding, errors)`。 否则的话，会在调用 `bytes.decode()` 之前获取缓冲区对象下层的 bytes 对象。 请参阅 二进制序列类型 --- bytes, bytearray, memoryview 与 [缓冲协议](../c-api/buffer.md#bufferobjects) 了解有关缓冲区对象的信息。

将一个 `bytes` 对象传入 `str()` 而不给出 _encoding_ 或 _errors_ 参数的操作属于第一种情况， 将返回非正式的字符串表示（另请参阅 Python 的 [`-b`](../using/cmdline.md#cmdoption-b) 命令行选项）。 例如:
~~~
    
    
~~~shell
>>> str(b'Zoot!')
"b'Zoot!'"
~~~

有关 `str` 类及其方法的更多信息，请参阅下面的 文本序列类型 --- str 和 字符串的方法 小节。 要输出格式化字符串，请参阅 [格式字符串字面值](lexical_analysis.md#f-strings) 和 [格式字符串语法](string.md#formatstrings) 小节。 此外还可以参阅 [文本处理服务](text.md#stringservices) 小节。

### 字符串的方法¶

字符串实现了所有 一般 序列的操作，还额外提供了以下列出的一些附加方法。

字符串还支持两种字符串格式化样式，一种提供了很大程度的灵活性和可定制性 (参阅 `str.format()`, [格式字符串语法](string.md#formatstrings) 和 [自定义字符串格式化](string.md#string-formatting)) 而另一种是基于 C `printf` 样式的格式化，它可处理的类型范围较窄，并且更难以正确使用，但对于它可处理的情况往往会更为快速 (printf 风格的字符串格式化)。

标准库的 [文本处理服务](text.md#textservices) 部分涵盖了许多其他模块，提供各种文本相关工具（例如包含于 [`re`](re.md#module-re "re: Regular expression operations.") 模块中的正则表达式支持）。

str.capitalize()¶

    

~~~
返回原字符串的副本，其首个字符大写，其余为小写。

在 3.8 版本发生变更: 第一个字符现在被放入了 titlecase 而不是 uppercase。 这意味着复合字母类字符将只有首个字母改为大写，而再不是全部字符大写。

str.casefold()¶
~~~
    

~~~
返回原字符串消除大小写的副本。 消除大小写的字符串可用于忽略大小写的匹配。

消除大小写类似于转为小写，但是更加彻底一些，因为它会移除字符串中的所有大小写变化形式。 例如，德语小写字母 `'ß'` 相当于 `"ss"`。 由于它已经是小写了，`lower()` 不会对 `'ß'` 做任何改变；而 `casefold()` 则会将其转换为 `"ss"`。

大小写折叠算法 [在 Unicode 标准 3.13 节 'Default Case Folding' 中描述](https://www.unicode.org/versions/Unicode15.0.0/ch03.pdf)。

在 3.3 版本加入.

str.center( _width_ [, _fillchar_ ])¶
~~~
    

~~~
返回长度为 _width_ 的字符串，原字符串在其正中。 使用指定的 _fillchar_ 填充两边的空位（默认使用 ASCII 空格符）。 如果 _width_ 小于等于 `len(s)` 则返回原字符串的副本。

str.count( _sub_ [, _start_ [, _end_ ]])¶
~~~
    

~~~
返回子字符串 _sub_ 在 [ _start_ , _end_ ] 范围内非重叠出现的次数。 可选参数 _start_ 与 _end_ 会被解读为切片表示法。

如果 _sub_ 为空，则返回字符之间的空字符串数，即字符串的长度加一。

str.encode( _encoding ='utf-8'_, _errors ='strict'_)¶
~~~
    

~~~
返回编码为 `bytes` 的字符串。

_encoding_ 默认为 `'utf-8'` ；请参阅 [标准编码](codecs.md#standard-encodings) 了解其他可能的值。

_errors_ 控制如何处理编码错误。 如为 `'strict'` (默认值)，则会引发 [`UnicodeError`](exceptions.md#UnicodeError "UnicodeError")。 其他可能的值有 `'ignore'`, `'replace'`, `'xmlcharrefreplace'`, `'backslashreplace'` 以及通过 [`codecs.register_error()`](codecs.md#codecs.register_error "codecs.register_error") 注册的任何其他名称。 请参阅 [错误处理方案](codecs.md#error-handlers) 了解详情。

出于性能原因，除非真正发生了编码错误，启用了 [Python 开发模式](devmode.md#devmode) 或使用了 [调试编译版](../using/configure.md#debug-build) 否则不会检查 _errors_ 值的有效性。

在 3.1 版本发生变更: 加入了对关键字参数的支持。

在 3.9 版本发生变更: 现在会在 [Python 开发模式](devmode.md#devmode) 和 [调试模式](../using/configure.md#debug-build) 下检查 _errors_ 参数的值。

str.endswith( _suffix_ [, _start_ [, _end_ ]])¶
~~~
    

~~~
如果字符串以指定的 _suffix_ 结束返回 `True`，否则返回 `False`。 _suffix_ 也可以为由多个供查找的后缀构成的元组。 如果有可选项 _start_ ，将从所指定位置开始检查。 如果有可选项 _end_ ，将在所指定位置停止比较。

str.expandtabs( _tabsize =8_)¶
~~~
    

~~~
返回字符串的副本，其中所有的制表符会由一个或多个空格替换，具体取决于当前列位置和给定的制表符宽度。 每 _tabsize_ 个字符设为一个制表位（默认值 8 时设定的制表位在列 0, 8, 16 依次类推）。 要展开字符串，当前列将被设为零并逐一检查字符串中的每个字符。 如果字符为制表符 (`\t`)，则会在结果中插入一个或多个空格符，直到当前列等于下一个制表位。 （制表符本身不会被复制。） 如果字符为换行符 (`\n`) 或回车符 (`\r`)，它会被复制并将当前列重设为零。 任何其他字符会被不加修改地复制并将当前列加一，不论该字符在被打印时会如何显示。
~~~
    
    
~~~shell
>>> '01\t012\t0123\t01234'.expandtabs()
'01      012     0123    01234'
>>> '01\t012\t0123\t01234'.expandtabs(4)
'01  012 0123    01234'
~~~

str.find( _sub_ [, _start_ [, _end_ ]])¶

    

~~~
返回子字符串 _sub_ 在 `s[start:end]` 切片内被找到的最小索引。 可选参数 _start_ 与 _end_ 会被解读为切片表示法。 如果 _sub_ 未被找到则返回 `-1`。

备注

`find()` 方法应该只在你需要知道 _sub_ 所在位置时使用。 要检查 _sub_ 是否为子字符串，请使用 [`in`](../reference/expressions.md#in) 操作符:
~~~
    
    
~~~shell
>>> 'Py' in 'Python'
True
~~~

str.format( _* args_, _** kwargs_)¶

    

~~~
执行字符串格式化操作。 调用此方法的字符串可以包含字符串字面值或者以花括号 `{}` 括起来的替换域。 每个替换域可以包含一个位置参数的数字索引，或者一个关键字参数的名称。 返回的字符串副本中每个替换域都会被替换为对应参数的字符串值。
~~~
    
    
~~~shell
>>> "The sum of 1 + 2 is {0}".format(1+2)
'The sum of 1 + 2 is 3'
~~~

请参阅 [格式字符串语法](string.md#formatstrings) 了解有关可以在格式字符串中指定的各种格式选项的说明。

备注

当使用 `n` 类型 (例如: `'{:n}'.format(1234)`) 来格式化数字 ([`int`](functions.md#int "int"), [`float`](functions.md#float "float"), [`complex`](functions.md#complex "complex"), [`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal") 及其子类) 的时候，该函数会临时性地将 `LC_CTYPE` 区域设置为 `LC_NUMERIC` 区域以解码 `localeconv()` 的 `decimal_point` 和 `thousands_sep` 字段，如果它们是非 ASCII 字符或长度超过 1 字节的话，并且 `LC_NUMERIC` 区域会与 `LC_CTYPE` 区域不一致。 这个临时更改会影响其他线程。

在 3.7 版本发生变更: 当使用 `n` 类型格式化数字时，该函数在某些情况下会临时性地将 `LC_CTYPE` 区域设置为 `LC_NUMERIC` 区域。

str.format_map( _mapping_ )¶

    

~~~
类似于 `str.format(**mapping)`，不同之处在于 `mapping` 会被直接使用而不是复制到一个 `dict`。 适宜使用此方法的一个例子是当 `mapping` 为 dict 的子类的情况：
~~~
    
    
~~~shell
>>> class Default(dict):
...     def __missing__(self, key):
...         return key
...
>>> '{name} was born in {country}'.format_map(Default(name='Guido'))
'Guido was born in country'
~~~

在 3.2 版本加入.

str.index( _sub_ [, _start_ [, _end_ ]])¶

    

~~~
类似于 `find()`，但在找不到子字符串时会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

str.isalnum()¶
~~~
    

~~~
如果字符串中的所有字符都是字母或数字且至少有一个字符，则返回 `True` ， 否则返回 `False` 。 如果 `c.isalpha()` ， `c.isdecimal()` ， `c.isdigit()` ，或 `c.isnumeric()` 之中有一个返回 `True` ，则字符 `c` 是字母或数字。

str.isalpha()¶
~~~
    

~~~
如果字符串中的所有字符都为字母并且至少有一个字符则返回 `True`，否则返回 `False`。 字母字符是指在 Unicode 字符数据库中被定义为 "Letter" 的字符，即具有通用类型属性 "Lm", "Lt", "Lu", "Ll" 或 "Lo" 之一的字符。 请注意这不同于 [Unicode 标准 4.10 节 'Letters, Alphabetic, and Ideographic' 中定义的 Alphabetic 属性](https://www.unicode.org/versions/Unicode15.0.0/ch04.pdf)。

str.isascii()¶
~~~
    

~~~
如果字符串为空或字符串中的所有字符都是 ASCII ，返回 `True` ，否则返回 `False` 。ASCII 字符的码点范围是 U+0000-U+007F 。

在 3.7 版本加入.

str.isdecimal()¶
~~~
    

~~~
如果字符串中的所有字符都是十进制字符且该字符串至少有一个字符，则返回 `True` ， 否则返回 `False` 。十进制字符指那些可以用来组成10进制数字的字符，例如 U+0660 ，即阿拉伯字母数字0 。 严格地讲，十进制字符是 Unicode 通用类别 "Nd" 中的一个字符。

str.isdigit()¶
~~~
    

~~~
如果字符串中的所有字符都是数字，并且至少有一个字符，返回 `True` ，否则返回 `False` 。 数字包括十进制字符和需要特殊处理的数字，如兼容性上标数字。这包括了不能用来组成 10 进制数的数字，如 Kharosthi 数。 严格地讲，数字是指属性值为 Numeric_Type=Digit 或 Numeric_Type=Decimal 的字符。

str.isidentifier()¶
~~~
    

~~~
如果字符串是有效的标识符，返回 `True` ，依据语言定义， [标识符和关键字](../reference/lexical_analysis.md#identifiers) 节。

[`keyword.iskeyword()`](keyword.md#keyword.iskeyword "keyword.iskeyword") 可被用来测试字符串 `s` 是否为保留的标识符，如 [`def`](../reference/compound_stmts.md#def) 和 [`class`](../reference/compound_stmts.md#class)。

示例：
~~~
    
    
~~~shell
>>> from keyword import iskeyword

>>> 'hello'.isidentifier(), iskeyword('hello')
(True, False)
>>> 'def'.isidentifier(), iskeyword('def')
(True, True)
~~~

str.islower()¶

    

~~~
如果字符串中至少有一个区分大小写的字符 [4] 且此类字符均为小写则返回 `True` ，否则返回 `False` 。

str.isnumeric()¶
~~~
    

~~~
如果字符串中至少有一个字符且所有字符均为数值字符则返回 `True` ，否则返回 `False` 。 数值字符包括数字字符，以及所有在 Unicode 中设置了数值特性属性的字符，例如 U+2155, VULGAR FRACTION ONE FIFTH。 正式的定义为：数值字符就是具有特征属性值 Numeric_Type=Digit, Numeric_Type=Decimal 或 Numeric_Type=Numeric 的字符。

str.isprintable()¶
~~~
    

~~~
如果字符串中所有字符均为可打印字符或字符串为空则返回 `True` ，否则返回 `False` 。 不可打印字符是在 Unicode 字符数据库中被定义为 "Other" 或 "Separator" 的字符，例外情况是 ASCII 空格字符 (0x20) 被视作可打印字符。 （请注意在此语境下可打印字符是指当对一个字符串发起调用 [`repr()`](functions.md#repr "repr") 时不必被转义的字符。 它们与字符串写入 [`sys.stdout`](sys.md#sys.stdout "sys.stdout") 或 [`sys.stderr`](sys.md#sys.stderr "sys.stderr") 时所需的处理无关。）

str.isspace()¶
~~~
    

~~~
如果字符串中只有空白字符且至少有一个字符则返回 `True` ，否则返回 `False` 。

_空白_ 字符是指在 Unicode 字符数据库 (参见 [`unicodedata`](unicodedata.md#module-unicodedata "unicodedata: Access the Unicode Database.")) 中主要类别为 `Zs` ("Separator, space") 或所属双向类为 `WS`, `B` 或 `S` 的字符。

str.istitle()¶
~~~
    

~~~
如果字符串中至少有一个字符且为标题字符串则返回 `True` ，例如大写字符之后只能带非大写字符而小写字符必须有大写字符打头。 否则返回 `False` 。

str.isupper()¶
~~~
    

~~~
如果字符串中至少有一个区分大小写的字符 [4] 且此类字符均为大写则返回 `True` ，否则返回 `False` 。
~~~
    
    
~~~shell
>>> 'BANANA'.isupper()
True
>>> 'banana'.isupper()
False
>>> 'baNana'.isupper()
False
>>> ' '.isupper()
False
~~~

str.join( _iterable_ )¶

    

~~~
返回一个由 _iterable_ 中的字符串拼接而成的字符串。 如果 _iterable_ 中存在任何非字符串值包括 `bytes` 对象则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。 调用该方法的字符串将作为元素之间的分隔。

str.ljust( _width_ [, _fillchar_ ])¶
~~~
    

~~~
返回长度为 _width_ 的字符串，原字符串在其中靠左对齐。 使用指定的 _fillchar_ 填充空位 (默认使用 ASCII 空格符)。 如果 _width_ 小于等于 `len(s)` 则返回原字符串的副本。

str.lower()¶
~~~
    

~~~
返回原字符串的副本，其所有区分大小写的字符 [4] 均转换为小写。

所使用的小写算法 [在 Unicode 标准 3.13 节 'Default Case Folding' 中描述](https://www.unicode.org/versions/Unicode15.0.0/ch03.pdf)。

str.lstrip([ _chars_ ])¶
~~~
    

~~~
返回原字符串的副本，移除其中的前导字符。 _chars_ 参数为指定要移除字符的字符串。 如果省略或为 `None`，则 _chars_ 参数默认移除空白符。 实际上 _chars_ 参数并非指定单个前缀；而是会移除参数值的所有组合:
~~~
    
    
~~~shell
>>> '   spacious   '.lstrip()
'spacious   '
>>> 'www.example.com'.lstrip('cmowz.')
'example.com'
~~~

参见 `str.removeprefix()` ，该方法将删除单个前缀字符串，而不是全部给定集合中的字符。 例如:

    
    
~~~shell
>>> 'Arthur: three!'.lstrip('Arthur: ')
'ee!'
>>> 'Arthur: three!'.removeprefix('Arthur: ')
'three!'
~~~

_static _str.maketrans( _x_ [, _y_ [, _z_ ]])¶

    

~~~
此静态方法返回一个可供 `str.translate()` 使用的转换对照表。

如果只有一个参数，则它必须是一个将 Unicode 码位序号（整数）或字符（长度为 1 的字符串）映射到 Unicode 码位序号、（任意长度的）字符串或 `None` 的字典。 字符键将会被转换为码位序号。

如果有两个参数，则它们必须是两个长度相等的字符串，并且在结果字典中，x 中每个字符将被映射到 y 中相同位置的字符。 如果有第三个参数，它必须是一个字符串，其中的字符将在结果中被映射到 `None`。

str.partition( _sep_ )¶
~~~
    

~~~
在 _sep_ 首次出现的位置拆分字符串，返回一个 3 元组，其中包含分隔符之前的部分、分隔符本身，以及分隔符之后的部分。 如果分隔符未找到，则返回的 3 元组中包含字符本身以及两个空字符串。

str.removeprefix( _prefix_ , _/_ )¶
~~~
    

~~~
如果字符串以 _prefix_ 字符串开头，返回 `string[len(prefix):]`。 否则，返回原始字符串的副本：
~~~
    
    
~~~shell
>>> 'TestHook'.removeprefix('Test')
'Hook'
>>> 'BaseTestCase'.removeprefix('Test')
'BaseTestCase'
~~~

在 3.9 版本加入.

str.removesuffix( _suffix_ , _/_ )¶

    

~~~
如果字符串以 _suffix_ 字符串结尾，并且 _suffix_ 非空，返回 `string[:-len(suffix)]`。 否则，返回原始字符串的副本:
~~~
    
    
~~~shell
>>> 'MiscTests'.removesuffix('Tests')
'Misc'
>>> 'TmpDirMixin'.removesuffix('Tests')
'TmpDirMixin'
~~~

在 3.9 版本加入.

str.replace( _old_ , _new_ , _count =-1_)¶

    

~~~
Return a copy of the string with all occurrences of substring _old_ replaced by _new_. If _count_ is given, only the first _count_ occurrences are replaced. If _count_ is not specified or `-1`, then all occurrences are replaced.

在 3.13 版本发生变更: _count_ is now supported as a keyword argument.

str.rfind( _sub_ [, _start_ [, _end_ ]])¶
~~~
    

~~~
返回子字符串 _sub_ 在字符串内被找到的最大（最右）索引，这样 _sub_ 将包含在 `s[start:end]` 当中。 可选参数 _start_ 与 _end_ 会被解读为切片表示法。 如果未找到则返回 `-1`。

str.rindex( _sub_ [, _start_ [, _end_ ]])¶
~~~
    

~~~
类似于 `rfind()`，但在子字符串 _sub_ 未找到时会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

str.rjust( _width_ [, _fillchar_ ])¶
~~~
    

~~~
返回长度为 _width_ 的字符串，原字符串在其中靠右对齐。 使用指定的 _fillchar_ 填充空位 (默认使用 ASCII 空格符)。 如果 _width_ 小于等于 `len(s)` 则返回原字符串的副本。

str.rpartition( _sep_ )¶
~~~
    

~~~
在 _sep_ 最后一次出现的位置拆分字符串，返回一个 3 元组，其中包含分隔符之前的部分、分隔符本身，以及分隔符之后的部分。 如果分隔符未找到，则返回的 3 元组中包含两个空字符串以及字符串本身。

str.rsplit( _sep =None_, _maxsplit =-1_)¶
~~~
    

~~~
返回一个由字符串内单词组成的列表，使用 _sep_ 作为分隔字符串。 如果给出了 _maxsplit_ ，则最多进行 _maxsplit_ 次拆分，从 _最右边_ 开始。 如果 _sep_ 未指定或为 `None`，任何空白字符串都会被作为分隔符。 除了从右边开始拆分，`rsplit()` 的其他行为都类似于下文所述的 `split()`。

str.rstrip([ _chars_ ])¶
~~~
    

~~~
返回原字符串的副本，移除其中的末尾字符。 _chars_ 参数为指定要移除字符的字符串。 如果省略或为 `None`，则 _chars_ 参数默认移除空白符。 实际上 _chars_ 参数并非指定单个后缀；而是会移除参数值的所有组合:
~~~
    
    
~~~shell
>>> '   spacious   '.rstrip()
'   spacious'
>>> 'mississippi'.rstrip('ipz')
'mississ'
~~~

要删除单个后缀字符串，而不是全部给定集合中的字符，请参见 `str.removesuffix()` 方法。 例如:

    
    
~~~shell
>>> 'Monty Python'.rstrip(' Python')
'M'
>>> 'Monty Python'.removesuffix(' Python')
'Monty'
~~~

str.split( _sep =None_, _maxsplit =-1_)¶

    

~~~
返回一个由字符串内单词组成的列表，使用 _sep_ 作为分隔字符串。 如果给出了 _maxsplit_ ，则最多进行 _maxsplit_ 次拆分（因此，列表最多会有 `maxsplit+1` 个元素）。 如果 _maxsplit_ 未指定或为 `-1`，则不限制拆分次数（进行所有可能的拆分）。

如果给出了 _sep_ ，则连续的分隔符不会被组合在一起而是被视为分隔空字符串 (例如 `'1,,2'.split(',')` 将返回 `['1', '', '2']`)。 _sep_ 参数可能由多个字符组成 (例如 `'1<>2<>3'.split('<>')` 将返回 `['1', '2', '3']`)。 使用指定的分隔符拆分空字符串将返回 `['']`。

例如：
~~~
    
    
~~~shell
>>> '1,2,3'.split(',')
['1', '2', '3']
>>> '1,2,3'.split(',', maxsplit=1)
['1', '2,3']
>>> '1,2,,3,'.split(',')
['1', '2', '', '3', '']
~~~

如果 _sep_ 未指定或为 `None`，则会应用另一种拆分算法：连续的空格会被视为单个分隔符，其结果将不包含开头或末尾的空字符串，如果字符串包含前缀或后缀空格的话。 因此，使用 `None` 拆分空字符串或仅包含空格的字符串将返回 `[]`。

例如：

    
    
~~~shell
>>> '1 2 3'.split()
['1', '2', '3']
>>> '1 2 3'.split(maxsplit=1)
['1', '2 3']
>>> '   1   2   3   '.split()
['1', '2', '3']
~~~

str.splitlines( _keepends =False_)¶

    

~~~
返回由原字符串中各行组成的列表，在行边界的位置拆分。 结果列表中不包含行边界，除非给出了 _keepends_ 且为真值。

此方法会以下列行边界进行拆分。 特别地，行边界是 [universal newlines](../glossary.md#term-universal-newlines) 的一个超集。

表示符

|

描述  
  
---|---  
  
`\n`

|

换行  
  
`\r`

|

回车  
  
`\r\n`

|

回车 + 换行  
  
`\v` 或 `\x0b`

|

行制表符  
  
`\f` 或 `\x0c`

|

换表单  
  
`\x1c`

|

文件分隔符  
  
`\x1d`

|

组分隔符  
  
`\x1e`

|

记录分隔符  
  
`\x85`

|

下一行 (C1 控制码)  
  
`\u2028`

|

行分隔符  
  
`\u2029`

|

段分隔符  
  
在 3.2 版本发生变更: `\v` 和 `\f` 被添加到行边界列表

例如：
~~~
    
    
~~~shell
>>> 'ab c\n\nde fg\rkl\r\n'.splitlines()
['ab c', '', 'de fg', 'kl']
>>> 'ab c\n\nde fg\rkl\r\n'.splitlines(keepends=True)
['ab c\n', '\n', 'de fg\r', 'kl\r\n']
~~~

不同于 `split()`，当给出了分隔字符串 _sep_ 时，对于空字符串此方法将返回一个空列表，而末尾的换行不会令结果中增加额外的行:

    
    
~~~shell
>>> "".splitlines()
[]
>>> "One line\n".splitlines()
['One line']
~~~

作为比较，`split('\n')` 的结果为:

    
    
~~~shell
>>> ''.split('\n')
['']
>>> 'Two lines\n'.split('\n')
['Two lines', '']
~~~

str.startswith( _prefix_ [, _start_ [, _end_ ]])¶

    

~~~
如果字符串以指定的 _prefix_ 开始则返回 `True`，否则返回 `False`。 _prefix_ 也可以为由多个供查找的前缀构成的元组。 如果有可选项 _start_ ，将从所指定位置开始检查。 如果有可选项 _end_ ，将在所指定位置停止比较。

str.strip([ _chars_ ])¶
~~~
    

~~~
返回原字符串的副本，移除其中的前导和末尾字符。 _chars_ 参数为指定要移除字符的字符串。 如果省略或为 `None`，则 _chars_ 参数默认移除空白符。 实际上 _chars_ 参数并非指定单个前缀或后缀；而是会移除参数值的所有组合:
~~~
    
    
~~~shell
>>> '   spacious   '.strip()
'spacious'
>>> 'www.example.com'.strip('cmowz.')
'example'
~~~

最外侧的前导和末尾 _chars_ 参数值将从字符串中移除。 开头端的字符的移除将在遇到一个未包含于 _chars_ 所指定字符集的字符时停止。 类似的操作也将在结尾端发生。 例如:

    
    
~~~shell
>>> comment_string = '#....... Section 3.2.1 Issue #32 .......'
>>> comment_string.strip('.#! ')
'Section 3.2.1 Issue #32'
~~~

str.swapcase()¶

    

~~~
返回原字符串的副本，其中大写字符转换为小写，反之亦然。 请注意 `s.swapcase().swapcase() == s` 并不一定为真值。

str.title()¶
~~~
    

~~~
返回原字符串的标题版本，其中每个单词第一个字母为大写，其余字母为小写。

例如：
~~~
    
    
~~~shell
>>> 'Hello world'.title()
'Hello World'
~~~

该算法使用一种简单的与语言无关的定义，将连续的字母组合视为单词。 该定义在多数情况下都很有效，但它也意味着代表缩写形式与所有格的撇号也会成为单词边界，这可能导致不希望的结果:

    
    
~~~shell
>>> "they're bill's friends from the UK".title()
"They'Re Bill'S Friends From The Uk"
~~~

[`string.capwords()`](string.md#string.capwords "string.capwords") 函数没有此问题，因为它只用空格来拆分单词。

作为替代，可以使用正则表达式来构造针对撇号的变通处理:

    
    
~~~shell
>>> import re
>>> def titlecase(s):
...     return re.sub(r"[A-Za-z]+('[A-Za-z]+)?",
...                   lambda mo: mo.group(0).capitalize(),
...                   s)
...
>>> titlecase("they're bill's friends.")
"They're Bill's Friends."
~~~

str.translate( _table_ )¶

    

~~~
返回原字符串的副本，其中每个字符按给定的转换表进行映射。 转换表必须是一个使用 `__getitem__()` 来实现索引操作的对象，通常为 [mapping](../glossary.md#term-mapping) 或 [sequence](../glossary.md#term-sequence)。 当以 Unicode 码位序号（整数）为索引时，转换表对象可以做以下任何一种操作：返回 Unicode 序号或字符串，将字符映射为一个或多个字符；返回 `None`，将字符从结果字符串中删除；或引发 [`LookupError`](exceptions.md#LookupError "LookupError") 异常，将字符映射为其自身。

你可以使用 `str.maketrans()` 基于不同格式的字符到字符映射来创建一个转换映射表。

另请参阅 [`codecs`](codecs.md#module-codecs "codecs: Encode and decode data and streams.") 模块以了解定制字符映射的更灵活方式。

str.upper()¶
~~~
    

~~~
返回原字符串的副本，其中所有区分大小写的字符 [4] 均转换为大写。 请注意如果 `s` 包含不区分大小写的字符或者如果结果字符的 Unicode 类别不是 "Lu" (Letter, uppercase) 而是 "Lt" (Letter, titlecase) 则 `s.upper().isupper()` 有可能为 `False`。

所使用的大写算法 [在 Unicode 标准 3.13 'Default Case Folding' 中描述](https://www.unicode.org/versions/Unicode15.0.0/ch03.pdf)。

str.zfill( _width_ )¶
~~~
    

~~~
返回原字符串的副本，在左边填充 ASCII `'0'` 数码使其长度变为 _width_ 。 正负值前缀 (`'+'`/`'-'`) 的处理方式是在正负符号 _之后_ 填充而非在之前。 如果 _width_ 小于等于 `len(s)` 则返回原字符串的副本。

例如：
~~~
    
    
~~~shell
>>> "42".zfill(5)
'00042'
>>> "-42".zfill(5)
'-0042'
~~~

### `printf` 风格的字符串格式化¶

备注

此处介绍的格式化操作具有多种怪异特性，可能导致许多常见错误（例如无法正确显示元组和字典）。 使用较新的 [格式化字符串字面值](lexical_analysis.md#f-strings)，`str.format()` 接口或 [模板字符串](string.md#template-strings) 有助于避免这样的错误。 这些替代方案中的每一种都更好地权衡并提供了简单、灵活以及可扩展性优势。

字符串具有一种特殊的内置操作：使用 `%` (取模) 运算符。 这也被称为字符串的 _格式化_ 或 _插值_ 运算符。 对于 `format % values` (其中 _format_ 为一个字符串)，在 _format_ 中的 `%` 转换标记符将被替换为零个或多个 _values_ 条目。 其效果类似于在 C 语言中使用 `sprintf()`。

如果 _format_ 要求一个单独参数，则 _values_ 可以为一个非元组对象。 [5] 否则的话， _values_ 必须或者是一个包含项数与格式字符串中指定的转换符项数相同的元组，或者是一个单独映射对象（例如字典）。

转换标记符包含两个或更多字符并具有以下组成，且必须遵循此处规定的顺序：

  1. `'%'` 字符，用于标记转换符的起始。

  2. 映射键（可选），由加圆括号的字符序列组成 (例如 `(somename)`)。

  3. 转换旗标（可选），用于影响某些转换类型的结果。

  4. 最小字段宽度（可选）。 如果指定为 `'*'` (星号)，则实际宽度会从 _values_ 元组的下一元素中读取，要转换的对象则为最小字段宽度和可选的精度之后的元素。

  5. 精度（可选），以在 `'.'` (点号) 之后加精度值的形式给出。 如果指定为 `'*'` (星号)，则实际精度会从 _values_ 元组的下一元素中读取，要转换的对象则为精度之后的元素。

  6. 长度修饰符（可选）。

  7. 转换类型。

当右边的参数为一个字典（或其他映射类型）时，字符串中的格式 _必须_ 包含加圆括号的映射键，对应 `'%'` 字符之后字典中的每一项。 映射键将从映射中选取要格式化的值。 例如：

    
    
~~~shell
>>> print('%(language)s has %(number)03d quote types.' %
...       {'language': "Python", "number": 2})
Python has 002 quote types.
~~~

在此情况下格式中不能出现 `*` 标记符（因其需要一个序列类的参数列表）。

转换旗标为：

旗标

|

含意  
  
---|---  
  
`'#'`

|

值的转换将使用“替代形式”（具体定义见下文）。  
  
`'0'`

|

转换将为数字值填充零字符。  
  
`'-'`

|

转换值将靠左对齐（如果同时给出 `'0'` 转换，则会覆盖后者）。  
  
`' '`

|

(空格) 符号位转换产生的正数（或空字符串）前将留出一个空格。  
  
`'+'`

|

符号字符 (`'+'` 或 `'-'`) 将显示于转换结果的开头（会覆盖 "空格" 旗标）。  
  
可以给出长度修饰符 (`h`, `l` 或 `L`)，但会被忽略，因为对 Python 来说没有必要 -- 所以 `%ld` 等价于 `%d`。

转换类型为：

转换符

|

含意

|

备注  
  
---|---|---  
  
`'d'`

|

有符号十进制整数。

|  
  
`'i'`

|

有符号十进制整数。

|  
  
`'o'`

|

有符号八进制数。

|

(1)  
  
`'u'`

|

过时类型 -- 等价于 `'d'`。

|

(6)  
  
`'x'`

|

有符号十六进制数（小写）。

|

(2)  
  
`'X'`

|

有符号十六进制数（大写）。

|

(2)  
  
`'e'`

|

浮点指数格式（小写）。

|

(3)  
  
`'E'`

|

浮点指数格式（大写）。

|

(3)  
  
`'f'`

|

浮点十进制格式。

|

(3)  
  
`'F'`

|

浮点十进制格式。

|

(3)  
  
`'g'`

|

浮点格式。 如果指数小于 -4 或不小于精度则使用小写指数格式，否则使用十进制格式。

|

(4)  
  
`'G'`

|

浮点格式。 如果指数小于 -4 或不小于精度则使用大写指数格式，否则使用十进制格式。

|

(4)  
  
`'c'`

|

单个字符（接受整数或单个字符的字符串）。

|  
  
`'r'`

|

字符串（使用 [`repr()`](functions.md#repr "repr") 转换任何 Python 对象）。

|

(5)  
  
`'s'`

|

字符串（使用 `str()` 转换任何 Python 对象）。

|

(5)  
  
`'a'`

|

字符串（使用 [`ascii()`](functions.md#ascii "ascii") 转换任何 Python 对象）。

|

(5)  
  
`'%'`

|

不转换参数，在结果中输出一个 `'%'` 字符。

|  
  
注释：

  1. 此替代形式会在第一个数码之前插入标示八进制数的前缀 (`'0o'`)。

  2. 此替代形式会在第一个数码之前插入 `'0x'` 或 `'0X'` 前缀（取决于是使用 `'x'` 还是 `'X'` 格式）。

  3. 此替代形式总是会在结果中包含一个小数点，即使其后并没有数码。

小数点后的数码位数由精度决定，默认为 6。

  4. 此替代形式总是会在结果中包含一个小数点，末尾各位的零不会如其他情况下那样被移除。

小数点前后的有效数码位数由精度决定，默认为 6。

  5. 如果精度为 `N`，输出将截短为 `N` 个字符。

  6. 参见 [**PEP 237**](https://peps.python.org/pep-0237/)。

由于 Python 字符串显式指明长度，`%s` 转换不会将 `'\0'` 视为字符串的结束。

在 3.1 版本发生变更: 绝对值超过 1e50 的 `%f` 转换不会再被替换为 `%g` 转换。

## 二进制序列类型 --- `bytes`, `bytearray`, `memoryview`¶

操作二进制数据的核心内置类型是 `bytes` 和 `bytearray`。 它们由 `memoryview` 提供支持，该对象使用 [缓冲区协议](buffer.md#bufferobjects) 来访问其他二进制对象所在内存，不需要创建对象的副本。

[`array`](array.md#module-array "array: Space efficient arrays of uniformly typed numeric values.") 模块支持高效地存储基本数据类型，例如 32 位整数和 IEEE754 双精度浮点值。

### bytes 对象¶

bytes 对象是由单个字节构成的不可变序列。 由于许多主要二进制协议都基于 ASCII 文本编码，因此 bytes 对象提供了一些仅在处理 ASCII 兼容数据时可用，并且在许多特性上与字符串对象紧密相关的方法。

_class _bytes([ _source_ [, _encoding_ [, _errors_ ]]])¶

    

~~~
首先，表示 bytes 字面值的语法与字符串字面值的大致相同，只是添加了一个 `b` 前缀：

  * 单引号: `b'同样允许嵌入 "双" 引号'`。

  * 双引号: `b"仍然允许嵌入 '单' 引号"`

  * 三重引号: `b'''三重单引号'''`, `b"""三重双引号"""`

bytes 字面值中只允许 ASCII 字符（无论源代码声明的编码为何）。 任何超出 127 的二进制值必须使用相应的转义序列形式加入 bytes 字面值。

像字符串字面值一样，bytes 字面值也可以使用 `r` 前缀来禁用转义序列处理。 请参阅 [字符串与字节串字面值](../reference/lexical_analysis.md#strings) 了解有关各种 bytes 字面值形式的详情，包括所支持的转义序列。

虽然 bytes 字面值和表示法是基于 ASCII 文本的，但 bytes 对象的行为实际上更像是不可变的整数序列，序列中的每个值的大小被限制为 `0 <= x < 256` (如果违反此限制将引发 [`ValueError`](exceptions.md#ValueError "ValueError"))。 这种限制是有意设计用以强调以下事实，虽然许多二进制格式都包含基于 ASCII 的元素，可以通过某些面向文本的算法进行有用的操作，但情况对于任意二进制数据来说通常却并非如此（盲目地将文本处理算法应用于不兼容 ASCII 的二进制数据格式往往将导致数据损坏）。

除了字面值形式，bytes 对象还可以通过其他几种方式来创建：

  * 指定长度的以零值填充的 bytes 对象: `bytes(10)`

  * 通过由整数组成的可迭代对象: `bytes(range(20))`

  * 通过缓冲区协议复制现有的二进制数据: `bytes(obj)`

另请参阅 [bytes](functions.md#func-bytes) 内置类型。

由于两个十六进制数码精确对应一个字节，因此十六进制数是描述二进制数据的常用格式。 相应地，bytes 类型具有从此种格式读取数据的附加类方法：

_classmethod _fromhex( _string_ )¶
~~~
    

~~~
此 `bytes` 类方法返回一个解码给定字符串的 bytes 对象。 字符串必须由表示每个字节的两个十六进制数码构成，其中的 ASCII 空白符会被忽略。
~~~
    
    
~~~shell
>>> bytes.fromhex('2Ef0 F1f2  ')
b'.\xf0\xf1\xf2'
~~~

在 3.7 版本发生变更: `bytes.fromhex()` 现在会忽略所有 ASCII 空白符而不只是空格符。

存在一个反向转换函数，可以将 bytes 对象转换为对应的十六进制表示。

hex([ _sep_ [, _bytes_per_sep_ ]])¶

    

~~~
返回一个字符串对象，该对象包含实例中每个字节的两个十六进制数字。
~~~
    
    
~~~shell
>>> b'\xf0\xf1\xf2'.hex()
'f0f1f2'
~~~

如果你希望令十六进制数字符串更易读，你可以指定单个字符分隔符作为 _sep_ 形参包含于输出中。 默认情况下，该分隔符会放在每个字节之间。 第二个可选的 _bytes_per_sep_ 形参控制间距。 正值会从右开始计算分隔符的位置，负值则是从左开始。

    
    
~~~shell
>>> value = b'\xf0\xf1\xf2'
>>> value.hex('-')
'f0-f1-f2'
>>> value.hex('_', 2)
'f0_f1f2'
>>> b'UUDDLRLRAB'.hex(' ', -4)
'55554444 4c524c52 4142'
~~~

在 3.5 版本加入.

在 3.8 版本发生变更: `bytes.hex()` 现在支持可选的 _sep_ 和 _bytes_per_sep_ 形参以在十六进制输出的字节之间插入分隔符。

由于 bytes 对象是由整数构成的序列（类似于元组），因此对于一个 bytes 对象 _b_ ，`b[0]` 将为一个整数，而 `b[0:1]` 将为一个长度为 1 的 bytes 对象。 （这与文本字符串不同，索引和切片所产生的将都是一个长度为 1 的字符串）。

bytes 对象的表示使用字面值格式 (`b'...'`)，因为它通常都要比像 `bytes([46, 46, 46])` 这样的格式更好用。 你总是可以使用 `list(b)` 将 bytes 对象转换为一个由整数构成的列表。

### bytearray 对象¶

`bytearray` 对象是 `bytes` 对象的可变对应物。

_class _bytearray([ _source_ [, _encoding_ [, _errors_ ]]])¶

    

~~~
bytearray 对象没有专属的字面值语法，它们总是通过调用构造器来创建：

  * 创建一个空实例: `bytearray()`

  * 创建一个指定长度的以零值填充的实例: `bytearray(10)`

  * 通过由整数组成的可迭代对象: `bytearray(range(20))`

  * 通过缓冲区协议复制现有的二进制数据: `bytearray(b'Hi!')`

由于 bytearray 对象是可变的，该对象除了 bytes 和 bytearray 操作 中所描述的 bytes 和 bytearray 共有操作之外，还支持 可变 序列操作。

另请参见 [bytearray](functions.md#func-bytearray) 内置类型。

由于两个十六进制数码精确对应一个字节，因此十六进制数是描述二进制数据的常用格式。 相应地，bytearray 类型具有从此种格式读取数据的附加类方法：

_classmethod _fromhex( _string_ )¶
~~~
    

~~~
`bytearray` 类方法返回一个解码给定字符串的 bytearray 对象。 字符串必须由表示每个字节的两个十六进制数码构成，其中的 ASCII 空白符会被忽略。
~~~
    
    
~~~shell
>>> bytearray.fromhex('2Ef0 F1f2  ')
bytearray(b'.\xf0\xf1\xf2')
~~~

在 3.7 版本发生变更: `bytearray.fromhex()` 现在会忽略所有 ASCII 空白符而不只是空格符。

存在一个反向转换函数，可以将 bytearray 对象转换为对应的十六进制表示。

hex([ _sep_ [, _bytes_per_sep_ ]])¶

    

~~~
返回一个字符串对象，该对象包含实例中每个字节的两个十六进制数字。
~~~
    
    
~~~shell
>>> bytearray(b'\xf0\xf1\xf2').hex()
'f0f1f2'
~~~

在 3.5 版本加入.

在 3.8 版本发生变更: 与 `bytes.hex()` 相似， `bytearray.hex()` 现在支持可选的 _sep_ 和 _bytes_per_sep_ 参数以在十六进制输出的字节之间插入分隔符。

由于 bytearray 对象是由整数构成的序列（类似于列表），因此对于一个 bytearray 对象 _b_ ，`b[0]` 将为一个整数，而 `b[0:1]` 将为一个长度为 1 的 bytearray 对象。 （这与文本字符串不同，索引和切片所产生的将都是一个长度为 1 的字符串）。

bytearray 对象的表示使用 bytes 对象字面值格式 (`bytearray(b'...')`)，因为它通常都要比 `bytearray([46, 46, 46])` 这样的格式更好用。 你总是可以使用 `list(b)` 将 bytearray 对象转换为一个由整数构成的列表。

### bytes 和 bytearray 操作¶

bytes 和 bytearray 对象都支持 通用 序列操作。 它们不仅能与相同类型的操作数，也能与任何 [bytes-like object](../glossary.md#term-bytes-like-object) 进行互操作。 由于这样的灵活性，它们可以在操作中自由地混合而不会导致错误。 但是，操作结果的返回值类型可能取决于操作数的顺序。

备注

bytes 和 bytearray 对象的方法不接受字符串作为其参数，就像字符串的方法不接受 bytes 对象作为其参数一样。 例如，你必须使用以下写法:

    
    
~~~
a = "abc"
b = a.replace("a", "f")
~~~

和:

    
    
~~~
a = b"abc"
b = a.replace(b"a", b"f")
~~~

某些 bytes 和 bytearray 操作假定使用兼容 ASCII 的二进制格式，因此在处理任意二进数数据时应当避免使用。 这些限制会在下文中说明。

备注

使用这些基于 ASCII 的操作来处理未以基于 ASCII 的格式存储的二进制数据可能会导致数据损坏。

bytes 和 bytearray 对象的下列方法可以用于任意二进制数据。

bytes.count( _sub_ [, _start_ [, _end_ ]])¶

bytearray.count( _sub_ [, _start_ [, _end_ ]])¶

    

~~~
返回子序列 _sub_ 在 [ _start_ , _end_ ] 范围内非重叠出现的次数。 可选参数 _start_ 与 _end_ 会被解读为切片表示法。

要搜索的子序列可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object) 或是 0 至 255 范围内的整数。

如果 _sub_ 为空，则返回字符之间的空切片的数量即字节串对象的长度加一。

在 3.3 版本发生变更: 也接受 0 至 255 范围内的整数作为子序列。

bytes.removeprefix( _prefix_ , _/_ )¶

bytearray.removeprefix( _prefix_ , _/_ )¶
~~~
    

~~~
如果二进制数据以 _prefix_ 字符串开头，返回 `bytes[len(prefix):]`。 否则，返回原始二进制数据的副本：
~~~
    
    
~~~shell
>>> b'TestHook'.removeprefix(b'Test')
b'Hook'
>>> b'BaseTestCase'.removeprefix(b'Test')
b'BaseTestCase'
~~~

_prefix_ 可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object)。

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

在 3.9 版本加入.

bytes.removesuffix( _suffix_ , _/_ )¶

bytearray.removesuffix( _suffix_ , _/_ )¶

    

~~~
如果二进制数据以 _suffix_ 字符串结尾，并且 _suffix_ 非空，返回 `bytes[:-len(suffix)]`。 否则，返回原始二进制数据的副本:
~~~
    
    
~~~shell
>>> b'MiscTests'.removesuffix(b'Tests')
b'Misc'
>>> b'TmpDirMixin'.removesuffix(b'Tests')
b'TmpDirMixin'
~~~

_suffix_ 可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object)。

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

在 3.9 版本加入.

bytes.decode( _encoding ='utf-8'_, _errors ='strict'_)¶

bytearray.decode( _encoding ='utf-8'_, _errors ='strict'_)¶

    

~~~
返回解码为 `str` 的字节串。

_encoding_ 默认为 `'utf-8'` ；请参阅 [标准编码](codecs.md#standard-encodings) 了解其他可能的值。

_errors_ 控制如何处理编码错误。 如为 `'strict'` (默认值)，则会引发 [`UnicodeError`](exceptions.md#UnicodeError "UnicodeError")。 其他可能的值有 `'ignore'`, `'replace'` 以及通过 [`codecs.register_error()`](codecs.md#codecs.register_error "codecs.register_error") 注册的任何其他名称。 请参阅 [错误处理方案](codecs.md#error-handlers) 了解详情。

出于性能原因，除非真正发生了编码错误，启用了 [Python 开发模式](devmode.md#devmode) 或使用了 [调试编译版](../using/configure.md#debug-build) 否则不会检查 _errors_ 值的有效性。

备注

将 _encoding_ 参数传给 `str` 允许直接解码任何 [bytes-like object](../glossary.md#term-bytes-like-object)，无须创建临时的 `bytes` 或 `bytearray` 对象。

在 3.1 版本发生变更: 加入了对关键字参数的支持。

在 3.9 版本发生变更: 现在会在 [Python 开发模式](devmode.md#devmode) 和 [调试模式](../using/configure.md#debug-build) 下检查 _errors_ 参数的值。

bytes.endswith( _suffix_ [, _start_ [, _end_ ]])¶

bytearray.endswith( _suffix_ [, _start_ [, _end_ ]])¶
~~~
    

~~~
如果二进制数据以指定的 _suffix_ 结束则返回 `True`，否则返回 `False`。 _suffix_ 也可以为由多个供查找的后缀构成的元组。 如果有可选项 _start_ ，将从所指定位置开始检查。 如果有可选项 _end_ ，将在所指定位置停止比较。

要搜索的后缀可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object)。

bytes.find( _sub_ [, _start_ [, _end_ ]])¶

bytearray.find( _sub_ [, _start_ [, _end_ ]])¶
~~~
    

~~~
返回子序列 _sub_ 在数据中被找到的最小索引， _sub_ 包含于切片 `s[start:end]` 之内。 可选参数 _start_ 与 _end_ 会被解读为切片表示法。 如果 _sub_ 未被找到则返回 `-1`。

要搜索的子序列可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object) 或是 0 至 255 范围内的整数。

备注

`find()` 方法应该只在你需要知道 _sub_ 所在位置时使用。 要检查 _sub_ 是否为子串，请使用 [`in`](../reference/expressions.md#in) 操作符:
~~~
    
    
~~~shell
>>> b'Py' in b'Python'
True
~~~

在 3.3 版本发生变更: 也接受 0 至 255 范围内的整数作为子序列。

bytes.index( _sub_ [, _start_ [, _end_ ]])¶

bytearray.index( _sub_ [, _start_ [, _end_ ]])¶

    

~~~
类似于 `find()`，但在找不到子序列时会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

要搜索的子序列可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object) 或是 0 至 255 范围内的整数。

在 3.3 版本发生变更: 也接受 0 至 255 范围内的整数作为子序列。

bytes.join( _iterable_ )¶

bytearray.join( _iterable_ )¶
~~~
    

~~~
返回一个由 _iterable_ 中的二进制数据序列拼接而成的 bytes 或 bytearray 对象。 如果 _iterable_ 中存在任何非 [字节类对象](../glossary.md#term-bytes-like-object) 包括存在 `str` 对象值则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。 提供该方法的 bytes 或 bytearray 对象的内容将作为元素之间的分隔。

_static _bytes.maketrans( _from_ , _to_ )¶

_static _bytearray.maketrans( _from_ , _to_ )¶
~~~
    

~~~
此静态方法返回一个可用于 `bytes.translate()` 的转换对照表，它将把 _from_ 中的每个字符映射为 _to_ 中相同位置上的字符； _from_ 与 _to_ 必须都是 [字节类对象](../glossary.md#term-bytes-like-object) 并且具有相同的长度。

在 3.1 版本加入.

bytes.partition( _sep_ )¶

bytearray.partition( _sep_ )¶
~~~
    

~~~
在 _sep_ 首次出现的位置拆分序列，返回一个 3 元组，其中包含分隔符之前的部分、分隔符本身或其 bytearray 副本，以及分隔符之后的部分。 如果分隔符未找到，则返回的 3 元组中包含原序列以及两个空的 bytes 或 bytearray 对象。

要搜索的分隔符可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object)。

bytes.replace( _old_ , _new_ [, _count_ ])¶

bytearray.replace( _old_ , _new_ [, _count_ ])¶
~~~
    

~~~
返回序列的副本，其中出现的所有子序列 _old_ 都将被替换为 _new_ 。 如果给出了可选参数 _count_ ，则只替换前 _count_ 次出现。

要搜索的子序列及其替换序列可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object)。

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

bytes.rfind( _sub_ [, _start_ [, _end_ ]])¶

bytearray.rfind( _sub_ [, _start_ [, _end_ ]])¶
~~~
    

~~~
返回子序列 _sub_ 在序列内被找到的最大（最右）索引，这样 _sub_ 将包含在 `s[start:end]` 当中。 可选参数 _start_ 与 _end_ 会被解读为切片表示法。 如果未找到则返回 `-1`。

要搜索的子序列可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object) 或是 0 至 255 范围内的整数。

在 3.3 版本发生变更: 也接受 0 至 255 范围内的整数作为子序列。

bytes.rindex( _sub_ [, _start_ [, _end_ ]])¶

bytearray.rindex( _sub_ [, _start_ [, _end_ ]])¶
~~~
    

~~~
类似于 `rfind()`，但在子序列 _sub_ 未找到时会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

要搜索的子序列可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object) 或是 0 至 255 范围内的整数。

在 3.3 版本发生变更: 也接受 0 至 255 范围内的整数作为子序列。

bytes.rpartition( _sep_ )¶

bytearray.rpartition( _sep_ )¶
~~~
    

~~~
在 _sep_ 最后一次出现的位置拆分序列，返回一个 3 元组，其中包含分隔符之前的部分，分隔符本身或其 bytearray 副本，以及分隔符之后的部分。 如果分隔符未找到，则返回的 3 元组中包含两个空的 bytes 或 bytearray 对象以及原序列的副本。

要搜索的分隔符可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object)。

bytes.startswith( _prefix_ [, _start_ [, _end_ ]])¶

bytearray.startswith( _prefix_ [, _start_ [, _end_ ]])¶
~~~
    

~~~
如果二进制数据以指定的 _prefix_ 开头则返回 `True`，否则返回 `False`。 _prefix_ 也可以为由多个供查找的前缀构成的元组。 如果有可选项 _start_ ，将从所指定位置开始检查。 如果有可选项 _end_ ，将在所指定位置停止比较。

要搜索的前缀可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object)。

bytes.translate( _table_ , _/_ , _delete =b''_)¶

bytearray.translate( _table_ , _/_ , _delete =b''_)¶
~~~
    

~~~
返回原 bytes 或 bytearray 对象的副本，移除其中所有在可选参数 _delete_ 中出现的 bytes，其余 bytes 将通过给定的转换表进行映射，该转换表必须是长度为 256 的 bytes 对象。

你可以使用 `bytes.maketrans()` 方法来创建转换表。

对于仅需移除字符的转换，请将 _table_ 参数设为 `None`:
~~~
    
    
~~~shell
>>> b'read this short text'.translate(None, b'aeiou')
b'rd ths shrt txt'
~~~

在 3.6 版本发生变更: 现在支持将 _delete_ 作为关键字参数。

以下 bytes 和 bytearray 对象的方法的默认行为会假定使用兼容 ASCII 的二进制格式，但通过传入适当的参数仍然可用于任意二进制数据。 请注意本小节中所有的 bytearray 方法都 _不是_ 原地执行操作，而是会产生新的对象。

bytes.center( _width_ [, _fillbyte_ ])¶

bytearray.center( _width_ [, _fillbyte_ ])¶

    

~~~
返回原对象的副本，在长度为 _width_ 的序列内居中，使用指定的 _fillbyte_ 填充两边的空位（默认使用 ASCII 空格符）。 对于 `bytes` 对象，如果 _width_ 小于等于 `len(s)` 则返回原序列的副本。

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

bytes.ljust( _width_ [, _fillbyte_ ])¶

bytearray.ljust( _width_ [, _fillbyte_ ])¶
~~~
    

~~~
返回原对象的副本，在长度为 _width_ 的序列中靠左对齐。 使用指定的 _fillbyte_ 填充空位（默认使用 ASCII 空格符）。 对于 `bytes` 对象，如果 _width_ 小于等于 `len(s)` 则返回原序列的副本。

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

bytes.lstrip([ _chars_ ])¶

bytearray.lstrip([ _chars_ ])¶
~~~
    

~~~
返回原序列的副本，移除指定的前导字节。 _chars_ 参数为指定要移除字节值集合的二进制序列 —— 这个名称表明此方法通常是用于 ASCII 字符。 如果省略或为 `None`，则 _chars_ 参数默认移除 ASCII 空白符。 _chars_ 参数并非指定单个前缀；而是会移除参数值的所有组合:
~~~
    
    
~~~shell
>>> b'   spacious   '.lstrip()
b'spacious   '
>>> b'www.example.com'.lstrip(b'cmowz.')
b'example.com'
~~~

要移除的二进制序列可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object) 。 要删除单个前缀字符串，而不是全部给定集合中的字符，请参见 `str.removeprefix()` 方法。 例如:

    
    
~~~shell
>>> b'Arthur: three!'.lstrip(b'Arthur: ')
b'ee!'
>>> b'Arthur: three!'.removeprefix(b'Arthur: ')
b'three!'
~~~

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

bytes.rjust( _width_ [, _fillbyte_ ])¶

bytearray.rjust( _width_ [, _fillbyte_ ])¶

    

~~~
返回原对象的副本，在长度为 _width_ 的序列中靠右对齐。 使用指定的 _fillbyte_ 填充空位（默认使用 ASCII 空格符）。 对于 `bytes` 对象，如果 _width_ 小于等于 `len(s)` 则返回原序列的副本。

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

bytes.rsplit( _sep =None_, _maxsplit =-1_)¶

bytearray.rsplit( _sep =None_, _maxsplit =-1_)¶
~~~
    

~~~
将二进制序列拆分为相同类型的子序列，使用 _sep_ 作为分隔符。 如果给出了 _maxsplit_ ，则最多进行 _maxsplit_ 次拆分，从 _最右边_ 开始。 如果 _sep_ 未指定或为 `None`，任何只包含 ASCII 空白符的子序列都会被作为分隔符。 除了从右边开始拆分，`rsplit()` 的其他行为都类似于下文所述的 `split()`。

bytes.rstrip([ _chars_ ])¶

bytearray.rstrip([ _chars_ ])¶
~~~
    

~~~
返回原序列的副本，移除指定的末尾字节。 _chars_ 参数为指定要移除字节值集合的二进制序列 —— 这个名称表明此方法通常是用于 ASCII 字符。 如果省略或为 `None`，则 _chars_ 参数默认移除 ASCII 空白符。 _chars_ 参数并非指定单个后缀；而是会移除参数值的所有组合:
~~~
    
    
~~~shell
>>> b'   spacious   '.rstrip()
b'   spacious'
>>> b'mississippi'.rstrip(b'ipz')
b'mississ'
~~~

要移除的二进制序列可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object) 。 要删除单个后缀字符串，而不是全部给定集合中的字符，请参见 `str.removesuffix()` 方法。 例如:

    
    
~~~shell
>>> b'Monty Python'.rstrip(b' Python')
b'M'
>>> b'Monty Python'.removesuffix(b' Python')
b'Monty'
~~~

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

bytes.split( _sep =None_, _maxsplit =-1_)¶

bytearray.split( _sep =None_, _maxsplit =-1_)¶

    

~~~
将二进制序列拆分为相同类型的子序列，使用 _sep_ 作为分隔符。 如果给出了 _maxsplit_ 且非负值，则最多进行 _maxsplit_ 次拆分（因此，列表最多会有 `maxsplit+1` 个元素）。 如果 _maxsplit_ 未指定或为 `-1`，则不限制拆分次数（进行所有可能的拆分）。

如果给出了 _sep_ ，则连续的分隔符不会被组合在一起而是被视为分隔空子序列 (例如 `b'1,,2'.split(b',')` 将返回 `[b'1', b'', b'2']`)。 _sep_ 参数可能为一个多字节序列 (例如 `b'1<>2<>3'.split(b'<>')` 将返回 `[b'1', b'2', b'3']`)。 使用指定的分隔符拆分空序列将返回 `[b'']` 或 `[bytearray(b'')]`，具体取决于被拆分对象的类型。 _sep_ 参数可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object)。

例如：
~~~
    
    
~~~shell
>>> b'1,2,3'.split(b',')
[b'1', b'2', b'3']
>>> b'1,2,3'.split(b',', maxsplit=1)
[b'1', b'2,3']
>>> b'1,2,,3,'.split(b',')
[b'1', b'2', b'', b'3', b'']
~~~

如果 _sep_ 未指定或为 `None`，则会应用另一种拆分算法：连续的 ASCII 空白符会被视为单个分隔符，其结果将不包含序列开头或末尾的空白符。 因此，在不指定分隔符的情况下对空序列或仅包含 ASCII 空白符的序列进行拆分将返回 `[]`。

例如：

    
    
~~~shell
>>> b'1 2 3'.split()
[b'1', b'2', b'3']
>>> b'1 2 3'.split(maxsplit=1)
[b'1', b'2 3']
>>> b'   1   2   3   '.split()
[b'1', b'2', b'3']
~~~

bytes.strip([ _chars_ ])¶

bytearray.strip([ _chars_ ])¶

    

~~~
返回原序列的副本，移除指定的开头和末尾字节。 _chars_ 参数为指定要移除字节值集合的二进制序列 —— 这个名称表明此方法通常是用于 ASCII 字符。 如果省略或为 `None`，则 _chars_ 参数默认移除 ASCII 空白符。 _chars_ 参数并非指定单个前缀或后缀；而是会移除参数值的所有组合:
~~~
    
    
~~~shell
>>> b'   spacious   '.strip()
b'spacious'
>>> b'www.example.com'.strip(b'cmowz.')
b'example'
~~~

要移除的字节值二进制序列可以是任意 [bytes-like object](../glossary.md#term-bytes-like-object)。

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

以下 bytes 和 bytearray 对象的方法会假定使用兼容 ASCII 的二进制格式，不应当被应用于任意二进制数据。 请注意本小节中所有的 bytearray 方法都 _不是_ 原地执行操作，而是会产生新的对象。

bytes.capitalize()¶

bytearray.capitalize()¶

    

~~~
返回原序列的副本，其中每个字节将都将被解读为一个 ASCII 字符，并且第一个字节的字符大写而其余的小写。 非 ASCII 字节值将保持原样不变。

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

bytes.expandtabs( _tabsize =8_)¶

bytearray.expandtabs( _tabsize =8_)¶
~~~
    

~~~
返回序列的副本，其中所有的 ASCII 制表符会由一个或多个 ASCII 空格替换，具体取决于当前列位置和给定的制表符宽度。 每 _tabsize_ 个字节设为一个制表位（默认值 8 时设定的制表位在列 0, 8, 16 依次类推）。 要展开序列，当前列位置将被设为零并逐一检查序列中的每个字节。 如果字节为 ASCII 制表符 (`b'\t'`)，则并在结果中插入一个或多个空格符，直到当前列等于下一个制表位。 （制表符本身不会被复制。） 如果当前字节为 ASCII 换行符 (`b'\n'`) 或回车符 (`b'\r'`)，它会被复制并将当前列重设为零。 任何其他字节会被不加修改地复制并将当前列加一，不论该字节值在被打印时会如何显示:
~~~
    
    
~~~shell
>>> b'01\t012\t0123\t01234'.expandtabs()
b'01      012     0123    01234'
>>> b'01\t012\t0123\t01234'.expandtabs(4)
b'01  012 0123    01234'
~~~

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

bytes.isalnum()¶

bytearray.isalnum()¶

    

~~~
如果序列中所有字节都是字母类 ASCII 字符或 ASCII 十进制数码并且序列非空则返回 `True` ，否则返回 `False` 。 字母类 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。 ASCII 十进制数码就是字节值包含在序列 `b'0123456789'` 中的字符。

例如：
~~~
    
    
~~~shell
>>> b'ABCabc1'.isalnum()
True
>>> b'ABC abc1'.isalnum()
False
~~~

bytes.isalpha()¶

bytearray.isalpha()¶

    

~~~
如果序列中所有字节都是字母类 ASCII 字符并且序列不非空则返回 `True` ，否则返回 `False` 。 字母类 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。

例如：
~~~
    
    
~~~shell
>>> b'ABCabc'.isalpha()
True
>>> b'ABCabc1'.isalpha()
False
~~~

bytes.isascii()¶

bytearray.isascii()¶

    

~~~
如果序列为空或序列中所有字节都是 ASCII 字节则返回 `True` ，否则返回 `False` 。 ASCII 字节的取值范围是 0-0x7F。

在 3.7 版本加入.

bytes.isdigit()¶

bytearray.isdigit()¶
~~~
    

~~~
如果序列中所有字节都是 ASCII 十进制数码并且序列非空则返回 `True` ，否则返回 `False` 。 ASCII 十进制数码就是字节值包含在序列 `b'0123456789'` 中的字符。

例如：
~~~
    
    
~~~shell
>>> b'1234'.isdigit()
True
>>> b'1.23'.isdigit()
False
~~~

bytes.islower()¶

bytearray.islower()¶

    

~~~
如果序列中至少有一个小写的 ASCII 字符并且没有大写的 ASCII 字符则返回 `True` ，否则返回 `False` 。

例如：
~~~
    
    
~~~shell
>>> b'hello world'.islower()
True
>>> b'Hello world'.islower()
False
~~~

小写 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyz'` 中的字符。 大写 ASCII 字符就是字节值包含在序列 `b'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。

bytes.isspace()¶

bytearray.isspace()¶

    

~~~
如果序列中所有字节都是 ASCII 空白符并且序列非空则返回 `True` ，否则返回 `False` 。 ASCII 空白符就是字节值包含在序列 `b' \t\n\r\x0b\f'` (空格, 制表, 换行, 回车, 垂直制表, 进纸) 中的字符。

bytes.istitle()¶

bytearray.istitle()¶
~~~
    

~~~
如果序列为 ASCII 标题大小写形式并且序列非空则返回 `True` ，否则返回 `False` 。 请参阅 `bytes.title()` 了解有关“标题大小写”的详细定义。

例如：
~~~
    
    
~~~shell
>>> b'Hello World'.istitle()
True
>>> b'Hello world'.istitle()
False
~~~

bytes.isupper()¶

bytearray.isupper()¶

    

~~~
如果序列中至少有一个大写字母 ASCII 字符并且没有小写 ASCII 字符则返回 `True` ，否则返回 `False` 。

例如：
~~~
    
    
~~~shell
>>> b'HELLO WORLD'.isupper()
True
>>> b'Hello world'.isupper()
False
~~~

小写 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyz'` 中的字符。 大写 ASCII 字符就是字节值包含在序列 `b'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。

bytes.lower()¶

bytearray.lower()¶

    

~~~
返回原序列的副本，其所有大写 ASCII 字符均转换为对应的小写形式。

例如：
~~~
    
    
~~~shell
>>> b'Hello World'.lower()
b'hello world'
~~~

小写 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyz'` 中的字符。 大写 ASCII 字符就是字节值包含在序列 `b'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

bytes.splitlines( _keepends =False_)¶

bytearray.splitlines( _keepends =False_)¶

    

~~~
返回由原二进制序列中各行组成的列表，在 ASCII 行边界符的位置拆分。 此方法使用 [universal newlines](../glossary.md#term-universal-newlines) 方式来分行。 结果列表中不包含换行符，除非给出了 _keepends_ 且为真值。

例如：
~~~
    
    
~~~shell
>>> b'ab c\n\nde fg\rkl\r\n'.splitlines()
[b'ab c', b'', b'de fg', b'kl']
>>> b'ab c\n\nde fg\rkl\r\n'.splitlines(keepends=True)
[b'ab c\n', b'\n', b'de fg\r', b'kl\r\n']
~~~

不同于 `split()`，当给出了分隔符 _sep_ 时，对于空字符串此方法将返回一个空列表，而末尾的换行不会令结果中增加额外的行:

    
    
~~~shell
>>> b"".split(b'\n'), b"Two lines\n".split(b'\n')
([b''], [b'Two lines', b''])
>>> b"".splitlines(), b"One line\n".splitlines()
([], [b'One line'])
~~~

bytes.swapcase()¶

bytearray.swapcase()¶

    

~~~
返回原序列的副本，其所有小写 ASCII 字符均转换为对应的大写形式，反之亦反。

例如：
~~~
    
    
~~~shell
>>> b'Hello World'.swapcase()
b'hELLO wORLD'
~~~

小写 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyz'` 中的字符。 大写 ASCII 字符就是字节值包含在序列 `b'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。

不同于 `str.swapcase()`，在些二进制版本下 `bin.swapcase().swapcase() == bin` 总是成立。 大小写转换在 ASCII 中是对称的，即使其对于任意 Unicode 码位来说并不总是成立。

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

bytes.title()¶

bytearray.title()¶

    

~~~
返回原二进制序列的标题版本，其中每个单词以一个大写 ASCII 字符为开头，其余字母为小写。 不区别大小写的字节值将保持原样不变。

例如：
~~~
    
    
~~~shell
>>> b'Hello world'.title()
b'Hello World'
~~~

小写 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyz'` 中的字符。 大写 ASCII 字符就是字节值包含在序列 `b'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。 所有其他字节值都不区分大小写。

该算法使用一种简单的与语言无关的定义，将连续的字母组合视为单词。 该定义在多数情况下都很有效，但它也意味着代表缩写形式与所有格的撇号也会成为单词边界，这可能导致不希望的结果:

    
    
~~~shell
>>> b"they're bill's friends from the UK".title()
b"They'Re Bill'S Friends From The Uk"
~~~

可以使用正则表达式来构建针对撇号的特别处理:

    
    
~~~shell
>>> import re
>>> def titlecase(s):
...     return re.sub(rb"[A-Za-z]+('[A-Za-z]+)?",
...                   lambda mo: mo.group(0)[0:1].upper() +
...                              mo.group(0)[1:].lower(),
...                   s)
...
>>> titlecase(b"they're bill's friends.")
b"They're Bill's Friends."
~~~

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

bytes.upper()¶

bytearray.upper()¶

    

~~~
返回原序列的副本，其所有小写 ASCII 字符均转换为对应的大写形式。

例如：
~~~
    
    
~~~shell
>>> b'Hello World'.upper()
b'HELLO WORLD'
~~~

小写 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyz'` 中的字符。 大写 ASCII 字符就是字节值包含在序列 `b'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

bytes.zfill( _width_ )¶

bytearray.zfill( _width_ )¶

    

~~~
返回原序列的副本，在左边填充 `b'0'` 数码使序列长度为 _width_ 。 正负值前缀 (`b'+'`/ `b'-'`) 的处理方式是在正负符号 _之后_ 填充而非在之前。 对于 `bytes` 对象，如果 _width_ 小于等于 `len(seq)` 则返回原序列。

例如：
~~~
    
    
~~~shell
>>> b"42".zfill(5)
b'00042'
>>> b"-42".zfill(5)
b'-0042'
~~~

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

### `printf` 风格的字节串格式化¶

备注

此处介绍的格式化操作具有多种怪异特性，可能导致许多常见错误（例如无法正确显示元组和字典）。 如果要打印的值可能为元组或字典，请将其放入一个元组中。

字节串对象 (`bytes`/`bytearray`) 具有一种特殊的内置操作：使用 `%` (取模) 运算符。 这也被称为字节串的 _格式化_ 或 _插值_ 运算符。 对于 `format % values` (其中 _format_ 为一个字节串对象)，在 _format_ 中的 `%` 转换标记符将被替换为零个或多个 _values_ 条目。 其效果类似于在 C 语言中使用 `sprintf()`。

如果 _format_ 要求一个单独参数，则 _values_ 可以为一个非元组对象。 [5] 否则的话， _values_ 必须或是是一个包含项数与格式字节串对象中指定的转换符项数相同的元组，或者是一个单独的映射对象（例如元组）。

转换标记符包含两个或更多字符并具有以下组成，且必须遵循此处规定的顺序：

  1. `'%'` 字符，用于标记转换符的起始。

  2. 映射键（可选），由加圆括号的字符序列组成 (例如 `(somename)`)。

  3. 转换旗标（可选），用于影响某些转换类型的结果。

  4. 最小字段宽度（可选）。 如果指定为 `'*'` (星号)，则实际宽度会从 _values_ 元组的下一元素中读取，要转换的对象则为最小字段宽度和可选的精度之后的元素。

  5. 精度（可选），以在 `'.'` (点号) 之后加精度值的形式给出。 如果指定为 `'*'` (星号)，则实际精度会从 _values_ 元组的下一元素中读取，要转换的对象则为精度之后的元素。

  6. 长度修饰符（可选）。

  7. 转换类型。

当右边的参数为一个字典（或其他映射类型）时，字节串对象中的格式 _必须_ 包含加圆括号的映射键，对应 `'%'` 字符之后字典中的每一项。 映射键将从映射中选取要格式化的值。 例如：

    
    
~~~shell
>>> print(b'%(language)s has %(number)03d quote types.' %
...       {b'language': b"Python", b"number": 2})
b'Python has 002 quote types.'
~~~

在此情况下格式中不能出现 `*` 标记符（因其需要一个序列类的参数列表）。

转换旗标为：

旗标

|

含意  
  
---|---  
  
`'#'`

|

值的转换将使用“替代形式”（具体定义见下文）。  
  
`'0'`

|

转换将为数字值填充零字符。  
  
`'-'`

|

转换值将靠左对齐（如果同时给出 `'0'` 转换，则会覆盖后者）。  
  
`' '`

|

(空格) 符号位转换产生的正数（或空字符串）前将留出一个空格。  
  
`'+'`

|

符号字符 (`'+'` 或 `'-'`) 将显示于转换结果的开头（会覆盖 "空格" 旗标）。  
  
可以给出长度修饰符 (`h`, `l` 或 `L`)，但会被忽略，因为对 Python 来说没有必要 -- 所以 `%ld` 等价于 `%d`。

转换类型为：

转换符

|

含意

|

备注  
  
---|---|---  
  
`'d'`

|

有符号十进制整数。

|  
  
`'i'`

|

有符号十进制整数。

|  
  
`'o'`

|

有符号八进制数。

|

(1)  
  
`'u'`

|

过时类型 -- 等价于 `'d'`。

|

(8)  
  
`'x'`

|

有符号十六进制数（小写）。

|

(2)  
  
`'X'`

|

有符号十六进制数（大写）。

|

(2)  
  
`'e'`

|

浮点指数格式（小写）。

|

(3)  
  
`'E'`

|

浮点指数格式（大写）。

|

(3)  
  
`'f'`

|

浮点十进制格式。

|

(3)  
  
`'F'`

|

浮点十进制格式。

|

(3)  
  
`'g'`

|

浮点格式。 如果指数小于 -4 或不小于精度则使用小写指数格式，否则使用十进制格式。

|

(4)  
  
`'G'`

|

浮点格式。 如果指数小于 -4 或不小于精度则使用大写指数格式，否则使用十进制格式。

|

(4)  
  
`'c'`

|

单个字节（接受整数或单个字节对象）。

|  
  
`'b'`

|

字节串（任何遵循 [缓冲区协议](buffer.md#bufferobjects) 或是具有 `__bytes__()` 的对象）。

|

(5)  
  
`'s'`

|

`'s'` 是 `'b'` 的一个别名，只应当在基于 Python2/3 的代码中使用。

|

(6)  
  
`'a'`

|

字节串（使用 `repr(obj).encode('ascii', 'backslashreplace')` 来转换任意 Python 对象）。

|

(5)  
  
`'r'`

|

`'r'` 是 `'a'` 的一个别名，只应当在基于 Python2/3 的代码中使用。

|

(7)  
  
`'%'`

|

不转换参数，在结果中输出一个 `'%'` 字符。

|  
  
注释：

  1. 此替代形式会在第一个数码之前插入标示八进制数的前缀 (`'0o'`)。

  2. 此替代形式会在第一个数码之前插入 `'0x'` 或 `'0X'` 前缀（取决于是使用 `'x'` 还是 `'X'` 格式）。

  3. 此替代形式总是会在结果中包含一个小数点，即使其后并没有数码。

小数点后的数码位数由精度决定，默认为 6。

  4. 此替代形式总是会在结果中包含一个小数点，末尾各位的零不会如其他情况下那样被移除。

小数点前后的有效数码位数由精度决定，默认为 6。

  5. 如果精度为 `N`，输出将截短为 `N` 个字符。

  6. `b'%s'` 已弃用，但在 3.x 系列中将不会被移除。

  7. `b'%r'` 已弃用，但在 3.x 系列中将不会被移除。

  8. 参见 [**PEP 237**](https://peps.python.org/pep-0237/)。

备注

此方法的 bytearray 版本 _并非_ 原地操作 —— 它总是产生一个新对象，即便没有做任何改变。

参见

[**PEP 461**](https://peps.python.org/pep-0461/) \- 为 bytes 和 bytearray 添加 % 格式化

在 3.5 版本加入.

### 内存视图¶

`memoryview` 对象允许 Python 代码访问一个对象的内部数据，只要该对象支持 [缓冲区协议](buffer.md#bufferobjects) 而无需进行拷贝。

_class _memoryview( _object_ )¶

    

~~~
创建一个引用 _object_ 的 `memoryview` 。 _object_ 必须支持缓冲区协议。支持缓冲区协议的内置对象有 `bytes` 和 `bytearray` 。

`memoryview` 有 **元素** 的概念， **元素** 指由原始 _object_ 处理的原子内存单元。对于许多简单的类型，如 `bytes` 和 `bytearray` ，一个元素是一个字节，但其他类型，如 [`array.array`](array.md#array.array "array.array") 可能有更大的元素。

`len(view)` 等于 `tolist` 的长度，即视图的嵌套列表表示形式。 如果 `view.ndim = 1`，它将等于视图中元素的数量。

在 3.12 版本发生变更: 如果 `view.ndim == 0`，现在 `len(view)` 将引发 [`TypeError`](exceptions.md#TypeError "TypeError") 而不是返回 1.

`itemsize` 属性将给出单个元素的字节数。

`memoryview` 支持通过切片和索引访问其元素。 一维切片的结果将是一个子视图:
~~~
    
    
~~~shell
>>> v = memoryview(b'abcefg')
>>> v[1]
98
>>> v[-1]
103
>>> v[1:4]
<memory at 0x7f3ddc9f4350>
>>> bytes(v[1:4])
b'bce'
~~~

如果 `format` 是一个来自于 [`struct`](struct.md#module-struct "struct: Interpret bytes as packed binary data.") 模块的原生格式说明符，则也支持使用整数或由整数构成的元组进行索引，并返回具有正确类型的单个 _元素_ 。 一维内存视图可以使用一个整数或由一个整数构成的元组进行索引。 多维内存视图可以使用由恰好 _ndim_ 个整数构成的元素进行索引， _ndim_ 即其维度。 零维内存视图可以使用空元组进行索引。

这里是一个使用非字节格式的例子:

    
    
~~~shell
>>> import array
>>> a = array.array('l', [-11111111, 22222222, -33333333, 44444444])
>>> m = memoryview(a)
>>> m[0]
-11111111
>>> m[-1]
44444444
>>> m[::2].tolist()
[-11111111, -33333333]
~~~

如果下层对象是可写的，则内存视图支持一维切片赋值。 改变大小则不被允许:

    
    
~~~shell
>>> data = bytearray(b'abcefg')
>>> v = memoryview(data)
>>> v.readonly
False
>>> v[0] = ord(b'z')
>>> data
bytearray(b'zbcefg')
>>> v[1:4] = b'123'
>>> data
bytearray(b'z123fg')
>>> v[2:3] = b'spam'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: memoryview assignment: lvalue and rvalue have different structures
>>> v[2:6] = b'spam'
>>> data
bytearray(b'z1spam')
~~~

格式符为 'B', 'b' 或 'c' 的 [hashable](../glossary.md#term-hashable) (只读) 类型的一维内存视图也是可哈希对象。 哈希被定义为 `hash(m) == hash(m.tobytes())`:

    
    
~~~shell
>>> v = memoryview(b'abcefg')
>>> hash(v) == hash(b'abcefg')
True
>>> hash(v[2:4]) == hash(b'ce')
True
>>> hash(v[::-2]) == hash(b'abcefg'[::-2])
True
~~~

在 3.3 版本发生变更: 一维内存视图现在可以被切片。 格式符为 'B', 'b' 或 'c' 的一维内存视图现在是 [hashable](../glossary.md#term-hashable)。

在 3.4 版本发生变更: 内存视图现在会自动注册为 [`collections.abc.Sequence`](collections.abc.md#collections.abc.Sequence "collections.abc.Sequence")

在 3.5 版本发生变更: 内存视图现在可使用整数元组进行索引。

`memoryview` 具有以下一些方法：

__eq__( _exporter_ )¶

    

~~~
memoryview 与 [**PEP 3118**](https://peps.python.org/pep-3118/) 中的导出器这两者如果形状相同，并且如果当使用 [`struct`](struct.md#module-struct "struct: Interpret bytes as packed binary data.") 语法解读操作数的相应格式代码时所有对应值都相同，则它们就是等价的。

对于 `tolist()` 当前所支持的 [`struct`](struct.md#module-struct "struct: Interpret bytes as packed binary data.") 格式字符串子集，如果 `v.tolist() == w.tolist()` 则 `v` 和 `w` 相等:
~~~
    
    
~~~shell
>>> import array
>>> a = array.array('I', [1, 2, 3, 4, 5])
>>> b = array.array('d', [1.0, 2.0, 3.0, 4.0, 5.0])
>>> c = array.array('b', [5, 3, 1])
>>> x = memoryview(a)
>>> y = memoryview(b)
>>> x == a == y == b
True
>>> x.tolist() == a.tolist() == y.tolist() == b.tolist()
True
>>> z = y[::-2]
>>> z == c
True
>>> z.tolist() == c.tolist()
True
~~~

如果两边的格式字符串都不被 [`struct`](struct.md#module-struct "struct: Interpret bytes as packed binary data.") 模块所支持，则两对象比较结果总是不相等（即使格式字符串和缓冲区内容相同）:

    
    
~~~shell
>>> from ctypes import BigEndianStructure, c_long
>>> class BEPoint(BigEndianStructure):
...     _fields_ = [("x", c_long), ("y", c_long)]
...
>>> point = BEPoint(100, 200)
>>> a = memoryview(point)
>>> b = memoryview(point)
>>> a == point
False
>>> a == b
False
~~~

请注意，与浮点数的情况一样，对于内存视图对象来说，`v is w` 也 _并不_ 意味着 `v == w`。

在 3.3 版本发生变更: 之前的版本比较原始内存时会忽略条目的格式与逻辑数组结构。

tobytes( _order ='C'_)¶

    

~~~
将缓冲区中的数据作为字节串返回。 这相当于在内存视图上调用 `bytes` 构造器。
~~~
    
    
~~~shell
>>> m = memoryview(b"abc")
>>> m.tobytes()
b'abc'
>>> bytes(m)
b'abc'
~~~

对于非连续数组，结果等于平面化表示的列表，其中所有元素都转换为字节串。 `tobytes()` 支持所有格式字符串，不符合 [`struct`](struct.md#module-struct "struct: Interpret bytes as packed binary data.") 模块语法的那些也包括在内。

在 3.8 版本加入: _order_ 可以为 {'C', 'F', 'A'}。 当 _order_ 为 'C' 或 'F' 时，原始数组的数据会被转换至 C 或 Fortran 顺序。 对于连续视图，'A' 会返回物理内存的精确副本。 特别地，内存中的 Fortran 顺序会被保留。对于非连续视图，数据会先被转换为 C 形式。 _order=None_ 与 _order='C'_ 是相同的。

hex([ _sep_ [, _bytes_per_sep_ ]])¶

    

~~~
返回一个字符串对象，其中分别以两个十六进制数码表示缓冲区里的每个字节。
~~~
    
    
~~~shell
>>> m = memoryview(b"abc")
>>> m.hex()
'616263'
~~~

在 3.5 版本加入.

在 3.8 版本发生变更: 与 `bytes.hex()` 相似， `memoryview.hex()` 现在支持可选的 _sep_ 和 _bytes_per_sep_ 参数以在十六进制输出的字节之间插入分隔符。

tolist()¶

    

~~~
将缓冲区内的数据以一个元素列表的形式返回。
~~~
    
    
~~~shell
>>> memoryview(b'abc').tolist()
[97, 98, 99]
>>> import array
>>> a = array.array('d', [1.1, 2.2, 3.3])
>>> m = memoryview(a)
>>> m.tolist()
[1.1, 2.2, 3.3]
~~~

在 3.3 版本发生变更: `tolist()` 现在支持 [`struct`](struct.md#module-struct "struct: Interpret bytes as packed binary data.") 模块语法中的所有单字符原生格式以及多维表示形式。

toreadonly()¶

    

~~~
返回 memoryview 对象的只读版本。 原始的 memoryview 对象不会被改变。
~~~
    
    
~~~shell
>>> m = memoryview(bytearray(b'abc'))
>>> mm = m.toreadonly()
>>> mm.tolist()
[97, 98, 99]
>>> mm[0] = 42
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: cannot modify read-only memory
>>> m[0] = 43
>>> mm.tolist()
[43, 98, 99]
~~~

在 3.8 版本加入.

release()¶

    

~~~
释放由内存视图对象所公开的底层缓冲区。 许多对象在被视图所获取时都会采取特殊动作（例如，`bytearray` 将会暂时禁止调整大小）；因此，调用 release() 可以方便地尽早去除这些限制（并释放任何多余的资源）。

在此方法被调用后，任何对视图的进一步操作将引发 [`ValueError`](exceptions.md#ValueError "ValueError") (`release()` 本身除外，它可以被多次调用):
~~~
    
    
~~~shell
>>> m = memoryview(b'abc')
>>> m.release()
>>> m[0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: operation forbidden on released memoryview object
~~~

使用 `with` 语句，可以通过上下文管理协议达到类似的效果:

    
    
~~~shell
>>> with memoryview(b'abc') as m:
...     m[0]
...
97
>>> m[0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: operation forbidden on released memoryview object
~~~

在 3.2 版本加入.

cast( _format_ [, _shape_ ])¶

    

~~~
将内存视图转化为新的格式或形状。 _shape_ 默认为 `[byte_length//new_itemsize]`，这意味着结果视图将是一维的。 返回值是一个新的内存视图，但缓冲区本身不会被复制。 支持的转化有 1D -> C-[contiguous](../glossary.md#term-contiguous) 和 C-contiguous -> 1D。

目标格式被限制为 [`struct`](struct.md#module-struct "struct: Interpret bytes as packed binary data.") 语法中的单一元素的原生格式。 这些格式中的一种必须为字节格式 ('B', 'b' 或 'c')。 结果的字节长度必须与原始长度相同。 请注意全部字节长度可能取决于具体操作系统。

将 1D/long 转换为 1D/unsigned bytes:
~~~
    
    
~~~shell
>>> import array
>>> a = array.array('l', [1,2,3])
>>> x = memoryview(a)
>>> x.format
'l'
>>> x.itemsize
8
>>> len(x)
3
>>> x.nbytes
24
>>> y = x.cast('B')
>>> y.format
'B'
>>> y.itemsize
1
>>> len(y)
24
>>> y.nbytes
24
~~~

将 1D/unsigned bytes 转换为 1D/char:

    
    
~~~shell
>>> b = bytearray(b'zyz')
>>> x = memoryview(b)
>>> x[0] = b'a'
Traceback (most recent call last):
  ...
TypeError: memoryview: invalid type for format 'B'
>>> y = x.cast('c')
>>> y[0] = b'a'
>>> b
bytearray(b'ayz')
~~~

将 1D/bytes 转换为 3D/ints 再转换为 1D/signed char:

    
    
~~~shell
>>> import struct
>>> buf = struct.pack("i"*12, *list(range(12)))
>>> x = memoryview(buf)
>>> y = x.cast('i', shape=[2,2,3])
>>> y.tolist()
[[[0, 1, 2], [3, 4, 5]], [[6, 7, 8], [9, 10, 11]]]
>>> y.format
'i'
>>> y.itemsize
4
>>> len(y)
2
>>> y.nbytes
48
>>> z = y.cast('b')
>>> z.format
'b'
>>> z.itemsize
1
>>> len(z)
48
>>> z.nbytes
48
~~~

将 1D/unsigned long 转换为 2D/unsigned long:

    
    
~~~shell
>>> buf = struct.pack("L"*6, *list(range(6)))
>>> x = memoryview(buf)
>>> y = x.cast('L', shape=[2,3])
>>> len(y)
2
>>> y.nbytes
48
>>> y.tolist()
[[0, 1, 2], [3, 4, 5]]
~~~

在 3.3 版本加入.

在 3.5 版本发生变更: 当转换为字节视图时，源格式将不再受限。

还存在一些可用的只读属性：

obj¶

    

~~~
内存视图的下层对象:
~~~
    
    
~~~shell
>>> b  = bytearray(b'xyz')
>>> m = memoryview(b)
>>> m.obj is b
True
~~~

在 3.3 版本加入.

nbytes¶

    

~~~
`nbytes == product(shape) * itemsize == len(m.tobytes())`。 这是数组在连续表示时将会占用的空间总字节数。 它不一定等于 `len(m)`:
~~~
    
    
~~~shell
>>> import array
>>> a = array.array('i', [1,2,3,4,5])
>>> m = memoryview(a)
>>> len(m)
5
>>> m.nbytes
20
>>> y = m[::2]
>>> len(y)
3
>>> y.nbytes
12
>>> len(y.tobytes())
12
~~~

多维数组:

    
    
~~~shell
>>> import struct
>>> buf = struct.pack("d"*12, *[1.5*x for x in range(12)])
>>> x = memoryview(buf)
>>> y = x.cast('d', shape=[3,4])
>>> y.tolist()
[[0.0, 1.5, 3.0, 4.5], [6.0, 7.5, 9.0, 10.5], [12.0, 13.5, 15.0, 16.5]]
>>> len(y)
3
>>> y.nbytes
96
~~~

在 3.3 版本加入.

readonly¶

    

~~~
一个表明内存是否只读的布尔值。

format¶
~~~
    

~~~
一个字符串，包含视图中每个元素的格式（表示为 [`struct`](struct.md#module-struct "struct: Interpret bytes as packed binary data.") 模块样式）。 内存视图可以从具有任意格式字符串的导出器创建，但某些方法 (例如 `tolist()`) 仅限于原生的单元素格式。

在 3.3 版本发生变更: 格式 `'B'` 现在会按照 struct 模块语法来处理。 这意味着 `memoryview(b'abc')[0] == b'abc'[0] == 97`。

itemsize¶
~~~
    

~~~
memoryview 中每个元素以字节表示的大小:
~~~
    
    
~~~shell
>>> import array, struct
>>> m = memoryview(array.array('H', [32000, 32001, 32002]))
>>> m.itemsize
2
>>> m[0]
32000
>>> struct.calcsize('H') == m.itemsize
True
~~~

ndim¶

    

~~~
一个整数，表示内存所代表的多维数组具有多少个维度。

shape¶
~~~
    

~~~
一个整数元组，通过 `ndim` 的长度值给出内存所代表的 N 维数组的形状。

在 3.3 版本发生变更: 当 ndim = 0 时值为空元组而不再为 `None`。

strides¶
~~~
    

~~~
一个整数元组，通过 `ndim` 的长度给出以字节表示的大小，以便访问数组中每个维度上的每个元素。

在 3.3 版本发生变更: 当 ndim = 0 时值为空元组而不再为 `None`。

suboffsets¶
~~~
    

~~~
供 PIL 风格的数组内部使用。 该值仅作为参考信息。

c_contiguous¶
~~~
    

~~~
一个表明内存是否为 C-[contiguous](../glossary.md#term-contiguous) 的布尔值。

在 3.3 版本加入.

f_contiguous¶
~~~
    

~~~
一个表明内存是否为 Fortran [contiguous](../glossary.md#term-contiguous) 的布尔值。

在 3.3 版本加入.

contiguous¶
~~~
    

~~~
一个表明内存是否为 [contiguous](../glossary.md#term-contiguous) 的布尔值。

在 3.3 版本加入.

## 集合类型 --- `set`, `frozenset`¶

_set_ 对象是由具有唯一性的 [hashable](../glossary.md#term-hashable) 对象所组成的无序多项集。 常见的用途包括成员检测、从序列中去除重复项以及数学中的集合类计算，例如交集、并集、差集与对称差集等等。 （关于其他容器对象请参看 `dict`, `list` 与 `tuple` 等内置类，以及 [`collections`](collections.md#module-collections "collections: Container datatypes") 模块。）

与其他多项集一样，集合也支持 `x in set`, `len(set)` 和 `for x in set`。 作为一种无序的多项集，集合并不记录元素位置或插入顺序。 相应地，集合不支持索引、切片或其他序列类的操作。

目前有两种内置集合类型，`set` 和 `frozenset`。 `set` 类型是可变的 --- 其内容可以使用 `add()` 和 `remove()` 这样的方法来改变。 由于是可变类型，它没有哈希值，且不能被用作字典的键或其他集合的元素。 `frozenset` 类型是不可变并且为 [hashable](../glossary.md#term-hashable) \--- 其内容在被创建后不能再改变；因此它可以被用作字典的键或其他集合的元素。

除了可以使用 `set` 构造器，非空的 set (不是 frozenset) 还可以通过将以逗号分隔的元素列表包含于花括号之内来创建，例如: `{'jack', 'sjoerd'}`。

两个类的构造器具有相同的作用方式：

_class _set([ _iterable_ ])¶

_class _frozenset([ _iterable_ ])¶
~~~
    

~~~
返回一个新的 set 或 frozenset 对象，其元素来自于 _iterable_ 。 集合的元素必须为 [hashable](../glossary.md#term-hashable)。 要表示由集合对象构成的集合，所有的内层集合必须为 `frozenset` 对象。 如果未指定 _iterable_ ，则将返回一个新的空集合。

集合可用多种方式来创建:

  * 使用花括号内以逗号分隔元素的方式: `{'jack', 'sjoerd'}`

  * 使用集合推导式: `{c for c in 'abracadabra' if c not in 'abc'}`

  * 使用类型构造器: `set()`, `set('foobar')`, `set(['a', 'b', 'foo'])`

`set` 和 `frozenset` 的实例提供以下操作：

len(s)
~~~
    

~~~
返回集合 _s_ 中的元素数量（即 _s_ 的基数）。

x in s
~~~
    

~~~
检测 _x_ 是否为 _s_ 中的成员。

x not in s
~~~
    

~~~
检测 _x_ 是否非 _s_ 中的成员。

isdisjoint( _other_ )¶
~~~
    

~~~
如果集合中没有与 _other_ 共有的元素则返回 `True`。 当且仅当两个集合的交集为空集合时，两者为不相交集合。

issubset( _other_ )¶

set <= other
~~~
    

~~~
检测是否集合中的每个元素都在 _other_ 之中。

set < other
~~~
    

~~~
检测集合是否为 _other_ 的真子集，即 `set <= other and set != other`。

issuperset( _other_ )¶

set >= other
~~~
    

~~~
检测是否 _other_ 中的每个元素都在集合之中。

set > other
~~~
    

~~~
检测集合是否为 _other_ 的真超集，即 `set >= other and set != other`。

union( _* others_)¶

set | other | ...
~~~
    

~~~
返回一个新集合，其中包含来自原集合以及 others 指定的所有集合中的元素。

intersection( _* others_)¶

set & other & ...
~~~
    

~~~
返回一个新集合，其中包含原集合以及 others 指定的所有集合中共有的元素。

difference( _* others_)¶

set - other - ...
~~~
    

~~~
返回一个新集合，其中包含原集合中在 others 指定的其他集合中不存在的元素。

symmetric_difference( _other_ )¶

set ^ other
~~~
    

~~~
返回一个新集合，其中的元素或属于原集合或属于 _other_ 指定的其他集合，但不能同时属于两者。

copy()¶
~~~
    

~~~
返回原集合的浅拷贝。

注意， `union()` 、 `intersection()` 、 `difference()` 、 `symmetric_difference()` 、 `issubset()` 和 `issuperset()` 方法的非运算符版本可以接受任何可迭代对象作为一个参数。相比之下，基于运算符的对应方法则要求参数为集合对象。这就避开了像 `set('abc') & 'cbs'` 这样容易出错的结构，而换成了可读性更好的 `set('abc').intersection('cbs')`。

`set` 和 `frozenset` 均支持集合与集合的比较。 两个集合当且仅当每个集合中的每个元素均包含于另一个集合之内（即各为对方的子集）时则相等。 一个集合当且仅当其为另一个集合的真子集（即为后者的子集但两者不相等）时则小于另一个集合。 一个集合当且仅当其为另一个集合的真超集（即为后者的超集但两者不相等）时则大于另一个集合。

`set` 的实例与 `frozenset` 的实例之间基于它们的成员进行比较。 例如 `set('abc') == frozenset('abc')` 返回 `True`，`set('abc') in set([frozenset('abc')])` 也一样。

子集与相等比较并不能推广为完全排序函数。 例如，任意两个非空且不相交的集合不相等且互不为对方的子集，因此以下 _所有_ 比较均返回 `False`: `a<b`, `a==b`, or `a>b`。

由于集合仅定义了部分排序（子集关系），因此由集合构成的列表 `list.sort()` 方法的输出并无定义。

集合的元素，与字典的键类似，必须为 [hashable](../glossary.md#term-hashable)。

混合了 `set` 实例与 `frozenset` 的二进制位运算将返回与第一个操作数相同的类型。例如: `frozenset('ab') | set('bc')` 将返回 `frozenset` 的实例。

下表列出了可用于 `set` 而不能用于不可变的 `frozenset` 实例的操作：

update( _* others_)¶

set |= other | ...
~~~
    

~~~
更新集合，添加来自 others 中的所有元素。

intersection_update( _* others_)¶

set &= other & ...
~~~
    

~~~
更新集合，只保留其中在所有 others 中也存在的元素。

difference_update( _* others_)¶

set -= other | ...
~~~
    

~~~
更新集合，移除其中也存在于 others 中的元素。

symmetric_difference_update( _other_ )¶

set ^= other
~~~
    

~~~
更新集合，只保留存在于集合的一方而非共同存在的元素。

add( _elem_ )¶
~~~
    

~~~
将元素 _elem_ 添加到集合中。

remove( _elem_ )¶
~~~
    

~~~
从集合中移除元素 _elem_ 。 如果 _elem_ 不存在于集合中则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

discard( _elem_ )¶
~~~
    

~~~
如果元素 _elem_ 存在于集合中则将其移除。

pop()¶
~~~
    

~~~
从集合中移除并返回任意一个元素。 如果集合为空则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

clear()¶
~~~
    

~~~
从集合中移除所有元素。

请注意，非运算符版本的 `update()`, `intersection_update()`, `difference_update()` 和 `symmetric_difference_update()` 方法将接受任意可迭代对象作为参数。

请注意，`__contains__()`, `remove()` 和 `discard()` 方法的 _elem_ 参数可能是一个 set。 为支持对一个等价的 frozenset 进行搜索，会根据 _elem_ 临时创建一个该类型对象。

## 映射类型 --- `dict`¶

[mapping](../glossary.md#term-mapping) 对象会将 [hashable](../glossary.md#term-hashable) 值映射到任意对象。 映射属于可变对象。 目前仅有一种标准映射类型 _字典_ 。 （关于其他容器对象请参看 `list`, `set` 与 `tuple` 等内置类，以及 [`collections`](collections.md#module-collections "collections: Container datatypes") 模块。）

字典的键 _几乎_ 可以为任何值。 不是 [hashable](../glossary.md#term-hashable) 的值，即包含列表、字典或其他可变类型（按值比较而非按对象标识比较）的值不可被用作键。 比较结果相等的值（如 `1`, `1.0` 和 `True` 等）可被互换使用以索引同一个字典条目。

_class _dict( _** kwargs_)¶

_class _dict( _mapping_ , _** kwargs_)

_class _dict( _iterable_ , _** kwargs_)
~~~
    

~~~
返回一个新的字典，基于可选的位置参数和可能为空的关键字参数集来初始化。

字典可用多种方式来创建:

  * 使用花括号内以逗号分隔 `键: 值` 对的方式: `{'jack': 4098, 'sjoerd': 4127}` or `{4098: 'jack', 4127: 'sjoerd'}`

  * 使用字典推导式: `{}`, `{x: x ** 2 for x in range(10)}`

  * 使用类型构造器: `dict()`, `dict([('foo', 100), ('bar', 200)])`, `dict(foo=100, bar=200)`

如果没有给出位置参数，将创建一个空字典。 如果给出一个位置参数并且其属于映射对象，将创建一个具有与映射对象相同键值对的字典。 否则的话，位置参数必须为一个 [iterable](../glossary.md#term-iterable) 对象。 该可迭代对象中的每一项本身必须为一个刚好包含两个元素的可迭代对象。 每一项中的第一个对象将成为新字典的一个键，第二个对象将成为其对应的值。 如果一个键出现一次以上，该键的最后一个值将成为其在新字典中对应的值。

如果给出了关键字参数，则关键字参数及其值会被加入到基于位置参数创建的字典。 如果要加入的键已存在，来自关键字参数的值将替代来自位置参数的值。

作为演示，以下示例返回的字典均等于 `{"one": 1, "two": 2, "three": 3}`:
~~~
    
    
~~~shell
>>> a = dict(one=1, two=2, three=3)
>>> b = {'one': 1, 'two': 2, 'three': 3}
>>> c = dict(zip(['one', 'two', 'three'], [1, 2, 3]))
>>> d = dict([('two', 2), ('one', 1), ('three', 3)])
>>> e = dict({'three': 3, 'one': 1, 'two': 2})
>>> f = dict({'one': 1, 'three': 3}, two=2)
>>> a == b == c == d == e == f
True
~~~

像第一个例子那样提供关键字参数的方式只能使用有效的 Python 标识符作为键。 其他方式则可使用任何有效的键。

这些是字典所支持的操作（因而自定义的映射类型也应当支持）：

list(d)

    

~~~
返回字典 _d_ 中使用的所有键的列表。

len(d)
~~~
    

~~~
返回字典 _d_ 中的项数。

d[key]
~~~
    

~~~
返回 _d_ 中以 _key_ 为键的项。 如果映射中不存在 _key_ 则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

如果字典的子类定义了方法 `__missing__()` 并且 _key_ 不存在，则 `d[key]` 操作将调用该方法并附带键 _key_ 作为参数。 `d[key]` 随后将返回或引发 `__missing__(key)` 调用所返回或引发的任何对象或异常。 没有其他操作或方法会发起调用 `__missing__()`。 如果未定义 `__missing__()`，则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。 `__missing__()` 必须是一个方法；它不能是一个实例变量:
~~~
    
    
~~~shell
>>> class Counter(dict):
...     def __missing__(self, key):
...         return 0
...
>>> c = Counter()
>>> c['red']
0
>>> c['red'] += 1
>>> c['red']
1
~~~

上面的例子显示了 [`collections.Counter`](collections.md#collections.Counter "collections.Counter") 实现的部分代码。 还有另一个不同的 `__missing__` 方法是由 [`collections.defaultdict`](collections.md#collections.defaultdict "collections.defaultdict") 所使用的。

d[key] = value

    

~~~
将 `d[key]` 设为 _value_ 。

del d[key]
~~~
    

~~~
将 `d[key]` 从 _d_ 中移除。 如果映射中不存在 _key_ 则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

key in d
~~~
    

~~~
如果 _d_ 中存在键 _key_ 则返回 `True`，否则返回 `False`。

key not in d
~~~
    

~~~
等价于 `not key in d`。

iter(d)
~~~
    

~~~
返回以字典的键为元素的迭代器。 这是 `iter(d.keys())` 的快捷方式。

clear()¶
~~~
    

~~~
移除字典中的所有元素。

copy()¶
~~~
    

~~~
返回原字典的浅拷贝。

_classmethod _fromkeys( _iterable_ [, _value_ ])¶
~~~
    

~~~
使用来自 _iterable_ 的键创建一个新字典，并将键值设为 _value_ 。

`fromkeys()` 是一个返回新字典的类方法。 _value_ 默认为 `None`。 所有值都只引用一个单独的实例，因此让 _value_ 成为一个可变对象例如空列表通常是没有意义的。 要获取不同的值，请改用 [字典推导式](../reference/expressions.md#dict)。

get( _key_ [, _default_ ])¶
~~~
    

~~~
如果 _key_ 存在于字典中则返回 _key_ 的值，否则返回 _default_ 。 如果 _default_ 未给出则默认为 `None`，因而此方法绝不会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

items()¶
~~~
    

~~~
返回由字典项 (`(键, 值)` 对) 组成的一个新视图。 参见 视图对象文档。

keys()¶
~~~
    

~~~
返回由字典键组成的一个新视图。 参见 视图对象文档。

pop( _key_ [, _default_ ])¶
~~~
    

~~~
如果 _key_ 存在于字典中则将其移除并返回其值，否则返回 _default_ 。 如果 _default_ 未给出且 _key_ 不存在于字典中，则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

popitem()¶
~~~
    

~~~
从字典中移除并返回一个 `(键, 值)` 对。 键值对会按 LIFO 的顺序被返回。

`popitem()` 适用于对字典进行消耗性的迭代，这在集合算法中经常被使用。 如果字典为空，调用 `popitem()` 将引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

在 3.7 版本发生变更: 现在会确保采用 LIFO 顺序。 在之前的版本中，`popitem()` 会返回一个任意的键/值对。

reversed(d)
~~~
    

~~~
返回一个逆序获取字典键的迭代器。 这是 `reversed(d.keys())` 的快捷方式。

在 3.8 版本加入.

setdefault( _key_ [, _default_ ])¶
~~~
    

~~~
如果字典存在键 _key_ ，返回它的值。如果不存在，插入值为 _default_ 的键 _key_ ，并返回 _default_ 。 _default_ 默认为 `None`。

update([ _other_ ])¶
~~~
    

~~~
使用来自 _other_ 的键/值对更新字典，覆盖原有的键。 返回 `None`。

`update()` 接受另一个字典对象，或者一个包含键/值对（以长度为二的元组或其他可迭代对象表示）的可迭代对象。 如果给出了关键字参数，则会以其所指定的键/值对更新字典: `d.update(red=1, blue=2)`。

values()¶
~~~
    

~~~
返回由字典值组成的一个新视图。 参见 视图对象文档。

两个 `dict.values()` 视图之间的相等性比较将总是返回 `False`。 这在 `dict.values()` 与其自身比较时也同样适用:
~~~
    
    
~~~shell
>>> d = {'a': 1}
>>> d.values() == d.values()
False
~~~

d | other

    

~~~
合并 _d_ 和 _other_ 中的键和值来创建一个新的字典，两者必须都是字典。当 _d_ 和 _other_ 有相同键时， _other_ 的值优先。

在 3.9 版本加入.

d |= other
~~~
    

~~~
用 _other_ 的键和值更新字典 _d_ ， _other_ 可以是 [mapping](../glossary.md#term-mapping) 或 [iterable](../glossary.md#term-iterable) 的键值对。当 _d_ 和 _other_ 有相同键时， _other_ 的值优先。

在 3.9 版本加入.

两个字典的比较当且仅当它们具有相同的 `(键, 值)` 对时才会相等（不考虑顺序）。 排序比较 ('<', '<=', '>=', '>') 会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

字典会保留插入时的顺序。 请注意对键的更新不会影响顺序。 删除并再次添加的键将被插入到末尾。
~~~
    
    
~~~shell
>>> d = {"one": 1, "two": 2, "three": 3, "four": 4}
>>> d
{'one': 1, 'two': 2, 'three': 3, 'four': 4}
>>> list(d)
['one', 'two', 'three', 'four']
>>> list(d.values())
[1, 2, 3, 4]
>>> d["one"] = 42
>>> d
{'one': 42, 'two': 2, 'three': 3, 'four': 4}
>>> del d["two"]
>>> d["two"] = None
>>> d
{'one': 42, 'three': 3, 'four': 4, 'two': None}
~~~

在 3.7 版本发生变更: 字典顺序会确保为插入顺序。 此行为是自 3.6 版开始的 CPython 实现细节。

字典和字典视图都是可逆的。

    
    
~~~shell
>>> d = {"one": 1, "two": 2, "three": 3, "four": 4}
>>> d
{'one': 1, 'two': 2, 'three': 3, 'four': 4}
>>> list(reversed(d))
['four', 'three', 'two', 'one']
>>> list(reversed(d.values()))
[4, 3, 2, 1]
>>> list(reversed(d.items()))
[('four', 4), ('three', 3), ('two', 2), ('one', 1)]
~~~

在 3.8 版本发生变更: 字典现在是可逆的。

参见

[`types.MappingProxyType`](types.md#types.MappingProxyType "types.MappingProxyType") 可被用来创建一个 `dict` 的只读视图。

### 字典视图对象¶

由 `dict.keys()`, `dict.values()` 和 `dict.items()` 所返回的对象是 _视图对象_ 。 该对象提供字典条目的一个动态视图，这意味着当字典改变时，视图也会相应改变。

字典视图可以被迭代以产生与其对应的数据，并支持成员检测：

len(dictview)

    

~~~
返回字典中的条目数。

iter(dictview)
~~~
    

~~~
返回字典中的键、值或项（以 `(键, 值)` 为元素的元组表示）的迭代器。

键和值是按插入时的顺序进行迭代的。 这样就允许使用 [`zip()`](functions.md#zip "zip") 来创建 `(值, 键)` 对: `pairs = zip(d.values(), d.keys())`。 另一个创建相同列表的方式是 `pairs = [(v, k) for (k, v) in d.items()]`.

在添加或删除字典中的条目期间对视图进行迭代可能引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 或者无法完全迭代所有条目。

在 3.7 版本发生变更: 字典顺序会确保为插入顺序。

x in dictview
~~~
    

~~~
如果 _x_ 是对应字典中存在的键、值或项（在最后一种情况下 _x_ 应为一个 `(键, 值)` 元组） 则返回 `True`。

reversed(dictview)
~~~
    

~~~
返回一个逆序获取字典键、值或项的迭代器。 视图将按与插入时相反的顺序进行迭代。

在 3.8 版本发生变更: 字典视图现在是可逆的。

dictview.mapping
~~~
    

~~~
返回 [`types.MappingProxyType`](types.md#types.MappingProxyType "types.MappingProxyType") 对象，封装了字典视图指向的原始字典。

在 3.10 版本加入.

键视图与集合类似因为它们的条目是唯一并且为 [hashable](../glossary.md#term-hashable)。 如果所有值都是可哈希对象，因而 `(key, value)` 对也是唯一并且为可哈希对象，那么条目视图也将与集合类似。 （值视图不会被认为与集合类似因为其条目通常都不是唯一的。） 对于与集合类似的视图，为抽象基类 [`collections.abc.Set`](collections.abc.md#collections.abc.Set "collections.abc.Set") 定义的所有操作都将是可用的 (例如 `==`, `<` 或 `^`)。 虽然使用了集合运算符，但与集合类似的视图接受任何可迭代对象作为其操作数，而不像集合哪样只接受集合作为输入。

一个使用字典视图的示例:
~~~
    
    
~~~shell
>>> dishes = {'eggs': 2, 'sausage': 1, 'bacon': 1, 'spam': 500}
>>> keys = dishes.keys()
>>> values = dishes.values()

>>> # iteration
>>> n = 0
>>> for val in values:
...     n += val
...
>>> print(n)
504

>>> # keys and values are iterated over in the same order (insertion order)
>>> list(keys)
['eggs', 'sausage', 'bacon', 'spam']
>>> list(values)
[2, 1, 1, 500]

>>> # view objects are dynamic and reflect dict changes
>>> del dishes['eggs']
>>> del dishes['sausage']
>>> list(keys)
['bacon', 'spam']

>>> # set operations
>>> keys & {'eggs', 'bacon', 'salad'}
{'bacon'}
>>> keys ^ {'sausage', 'juice'} == {'juice', 'sausage', 'bacon', 'spam'}
True
>>> keys | ['juice', 'juice', 'juice'] == {'bacon', 'spam', 'juice'}
True

>>> # get back a read-only proxy for the original dictionary
>>> values.mapping
mappingproxy({'bacon': 1, 'spam': 500})
>>> values.mapping['spam']
500
~~~

## 上下文管理器类型¶

Python 的 [`with`](compound_stmts.md#with) 语句支持通过上下文管理器所定义的运行时上下文这一概念。 此对象的实现使用了一对专门方法，允许用户自定义类来定义运行时上下文，在语句体被执行前进入该上下文，并在语句执行完毕时退出该上下文：

contextmanager.__enter__()¶

    

~~~
进入运行时上下文并返回此对象或关联到该运行时上下文的其他对象。 此方法的返回值会绑定到使用此上下文管理器的 [`with`](../reference/compound_stmts.md#with) 语句的 `as` 子句中的标识符。

一个返回其自身的上下文管理器的例子是 [file object](../glossary.md#term-file-object)。 文件对象会从 __enter__() 返回其自身，以允许 [`open()`](functions.md#open "open") 被用作 [`with`](../reference/compound_stmts.md#with) 语句中的上下文表达式。

一个返回关联对象的上下文管理器的例子是 [`decimal.localcontext()`](decimal.md#decimal.localcontext "decimal.localcontext") 所返回的对象。 此种管理器会将活动的 decimal 上下文设为原始 decimal 上下文的一个副本并返回该副本。 这允许对 [`with`](../reference/compound_stmts.md#with) 语句的语句体中的当前 decimal 上下文进行更改，而不会影响 `with` 语句以外的代码。

contextmanager.__exit__( _exc_type_ , _exc_val_ , _exc_tb_ )¶
~~~
    

~~~
退出运行时上下文并返回一个布尔值旗标来表明所发生的任何异常是否应当被屏蔽。 如果在执行 [`with`](../reference/compound_stmts.md#with) 语句的语句体期间发生了异常，则参数会包含异常的类型、值以及回溯信息。 在其他情况下三个参数均为 `None`。

自此方法返回一个真值将导致 [`with`](../reference/compound_stmts.md#with) 语句屏蔽异常并继续执行紧随在 `with` 语句之后的语句。 否则异常将在此方法结束执行后继续传播。 在此方法执行期间发生的异常将会取代 `with` 语句的语句体中发生的任何异常。

传入的异常绝对不应当被显式地重新引发 —— 相反地，此方法应当返回一个假值以表明方法已成功完成并且不希望屏蔽被引发的异常。 这允许上下文管理代码方便地检测 `__exit__()` 方法是否确实已失败。

Python 定义了一些上下文管理器来支持简易的线程同步、文件或其他对象的快速关闭，以及更方便地操作活动的十进制算术上下文。 除了实现上下文管理协议以外，不同类型不会被特殊处理。 请参阅 [`contextlib`](contextlib.md#module-contextlib "contextlib: Utilities for with-statement contexts.") 模块查看相关的示例。

Python 的 [generator](../glossary.md#term-generator) 和 [`contextlib.contextmanager`](contextlib.md#contextlib.contextmanager "contextlib.contextmanager") 装饰器提供了实现这些协议的便捷方式。 如果使用 [`contextlib.contextmanager`](contextlib.md#contextlib.contextmanager "contextlib.contextmanager") 装饰器来装饰一个生成器函数，它将返回一个实现了必要的 `__enter__()` 和 `__exit__()` 方法的上下文管理器，而不再是由未经装饰的生成器所产生的迭代器。

请注意，Python/C API 中 Python 对象的类型结构中并没有针对这些方法的专门槽位。 想要定义这些方法的扩展类型必须将它们作为普通的 Python 可访问方法来提供。 与设置运行时上下文的开销相比，单个类字典查找的开销可以忽略不计。

## 类型注解的类型 --- Generic Alias 、 Union¶

[type annotations](../glossary.md#term-annotation) 的内置类型为 Generic Alias 和 Union。

### GenericAlias 类型¶

`GenericAlias` 对象通常是通过 [抽取](../reference/expressions.md#subscriptions) 一个类来创建的。 它们最常被用于 [容器类](../reference/datamodel.md#sequence-types)，如 `list` 或 `dict`。 举例来说，`list[int]` 这个 `GenericAlias` 对象是通过附带 [`int`](functions.md#int "int") 参数抽取 `list` 类来创建的。 `GenericAlias` 对象的主要目的是用于 [类型标注](../glossary.md#term-annotation)。

备注

通常一个类只有在实现了特殊方法 [`__class_getitem__()`](../reference/datamodel.md#object.__class_getitem__ "object.__class_getitem__") 时才支持抽取操作。

`GenericAlias` 对象可作为 [generic type](../glossary.md#term-generic-type) 的代理，实现了 _形参化泛型_ 。

对于一个容器类，提供给类的 [抽取](../reference/expressions.md#subscriptions) 操作的参数可以指明对象所包含的元素类型。 例如，`set[bytes]` 可在类型标注中用来表示一个 `set` 中的所有元素均为 `bytes` 类型。

对于一个定义了 [`__class_getitem__()`](../reference/datamodel.md#object.__class_getitem__ "object.__class_getitem__") 但不属于容器的类，提供给类的抽取操作的参数往往会指明在对象上定义的一个或多个方法的返回值类型。 例如，[`正则表达式`](re.md#module-re "re: Regular expression operations.") 可以被用在 `str` 数据类型和 `bytes` 数据类型上:

  * 如果 `x = re.search('foo', 'foo')`，则 `x` 将为一个 [re.Match](re.md#match-objects) 对象而 `x.group(0)` 和 `x[0]` 的返回值将均为 `str` 类型。 我们可以在类型标注中使用 `GenericAlias` `re.Match[str]` 来代表这种对象。

  * 如果 `y = re.search(b'bar', b'bar')`，(注意 `b` 表示 `bytes`)，则 `y` 也将为一个 `re.Match` 的实例，但 `y.group(0)` 和 `y[0]` 的返回值将均为 `bytes` 类型。 在类型标注中，我们将使用 `re.Match[bytes]` 来代表这种形式的 [re.Match](re.md#match-objects) 对象。

`GenericAlias` 对象是 [`types.GenericAlias`](types.md#types.GenericAlias "types.GenericAlias") 类的实例，该类也可被用来直接创建 `GenericAlias` 对象。

T[X, Y, ...]
~~~
    

~~~
创建一个代表由类型 _X_ , _Y_ 来参数化的类型 `T` 的 `GenericAlias`，此类型会更依赖于所使用的 `T`。 例如，一个接受包含 [`float`](functions.md#float "float") 元素的 `list` 的函数:
~~~
    
    
~~~python
def average(values: list[float]) -> float:
    return sum(values) / len(values)
~~~

另一个例子是关于 [mapping](../glossary.md#term-mapping) 对象的，用到了 `dict`，泛型的两个类型参数分别代表了键类型和值类型。本例中的函数需要一个 `dict`，其键的类型为 `str`，值的类型为 [`int`](functions.md#int "int"):。

    
    
~~~python
def send_post_request(url: str, body: dict[str, int]) -> None:
    ...
~~~

内置函数 [`isinstance()`](functions.md#isinstance "isinstance") 和 [`issubclass()`](functions.md#issubclass "issubclass") 不接受第二个参数为 `GenericAlias` 类型：

    
    
~~~shell
>>> isinstance([1, 2], list[str])
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: isinstance() argument 2 cannot be a parameterized generic
~~~

Python 运行时不会强制执行 [类型标注](../glossary.md#term-annotation)。 这种行为扩展到了泛型及其类型形参。 当由 `GenericAlias` 创建容器对象时，并不会检查容器中为元素指定的类型。 例如，以下代码虽然不被鼓励，但运行时并不会报错:

    
    
~~~shell
>>> t = list[str]
>>> t([1, 2, 3])
[1, 2, 3]
~~~

不仅如此，在创建对象的过程中，应用了参数后的泛型还会抹除类型参数：

    
    
~~~shell
>>> t = list[str]
>>> type(t)
<class 'types.GenericAlias'>

>>> l = t()
>>> type(l)
<class 'list'>
~~~

在泛型上调用 [`repr()`](functions.md#repr "repr") 或 `str()` 会显示应用参数之后的类型：

    
    
~~~shell
>>> repr(list[int])
'list[int]'

>>> str(list[int])
'list[int]'
~~~

调用泛型容器的 [`__getitem__()`](datamodel.md#object.__getitem__ "object.__getitem__") 方法将引发异常以防出现 `dict[str][str]` 之类的错误:

    
    
~~~shell
>>> dict[str][str]
Traceback (most recent call last):
  ...
TypeError: dict[str] is not a generic class
~~~

不过，当使用了 [类型变量](typing.md#generics) 时这种表达式是无效的。 索引必须有与 `GenericAlias` 对象的 `__args__` 中的类型变量条目数量相当的元素。

    
    
~~~shell
>>> from typing import TypeVar
>>> Y = TypeVar('Y')
>>> dict[str, Y][int]
dict[str, int]
~~~

#### 标准泛型类¶

下列标准库类支持形参化的泛型。 此列表并不是详尽无遗的。

  * `tuple`

  * `list`

  * `dict`

  * `set`

  * `frozenset`

  * [`type`](functions.md#type "type")

  * [`collections.deque`](collections.md#collections.deque "collections.deque")

  * [`collections.defaultdict`](collections.md#collections.defaultdict "collections.defaultdict")

  * [`collections.OrderedDict`](collections.md#collections.OrderedDict "collections.OrderedDict")

  * [`collections.Counter`](collections.md#collections.Counter "collections.Counter")

  * [`collections.ChainMap`](collections.md#collections.ChainMap "collections.ChainMap")

  * [`collections.abc.Awaitable`](collections.abc.md#collections.abc.Awaitable "collections.abc.Awaitable")

  * [`collections.abc.Coroutine`](collections.abc.md#collections.abc.Coroutine "collections.abc.Coroutine")

  * [`collections.abc.AsyncIterable`](collections.abc.md#collections.abc.AsyncIterable "collections.abc.AsyncIterable")

  * [`collections.abc.AsyncIterable`](collections.abc.md#collections.abc.AsyncIterable "collections.abc.AsyncIterable")

  * [`collections.abc.AsyncGenerator`](collections.abc.md#collections.abc.AsyncGenerator "collections.abc.AsyncGenerator")

  * [`collections.abc.Iterable`](collections.abc.md#collections.abc.Iterable "collections.abc.Iterable")

  * [`collections.abc.Iterator`](collections.abc.md#collections.abc.Iterator "collections.abc.Iterator")

  * [`collections.abc.Generator`](collections.abc.md#collections.abc.Generator "collections.abc.Generator")

  * [`collections.abc.Reversible`](collections.abc.md#collections.abc.Reversible "collections.abc.Reversible")

  * [`collections.abc.Container`](collections.abc.md#collections.abc.Container "collections.abc.Container")

  * [`collections.abc.Collection`](collections.abc.md#collections.abc.Collection "collections.abc.Collection")

  * [`collections.abc.Callable`](collections.abc.md#collections.abc.Callable "collections.abc.Callable")

  * [`collections.abc.Set`](collections.abc.md#collections.abc.Set "collections.abc.Set")

  * [`collections.abc.MutableSet`](collections.abc.md#collections.abc.MutableSet "collections.abc.MutableSet")

  * [`collections.abc.Mapping`](collections.abc.md#collections.abc.Mapping "collections.abc.Mapping")

  * [`collections.abc.MutableMapping`](collections.abc.md#collections.abc.MutableMapping "collections.abc.MutableMapping")

  * [`collections.abc.Sequence`](collections.abc.md#collections.abc.Sequence "collections.abc.Sequence")

  * [`collections.abc.MutableSequence`](collections.abc.md#collections.abc.MutableSequence "collections.abc.MutableSequence")

  * [`collections.abc.ByteString`](collections.abc.md#collections.abc.ByteString "collections.abc.ByteString")

  * [`collections.abc.MappingView`](collections.abc.md#collections.abc.MappingView "collections.abc.MappingView")

  * [`collections.abc.KeysView`](collections.abc.md#collections.abc.KeysView "collections.abc.KeysView")

  * [`collections.abc.ItemsView`](collections.abc.md#collections.abc.ItemsView "collections.abc.ItemsView")

  * [`collections.abc.ValuesView`](collections.abc.md#collections.abc.ValuesView "collections.abc.ValuesView")

  * [`contextlib.AbstractContextManager`](contextlib.md#contextlib.AbstractContextManager "contextlib.AbstractContextManager")

  * [`contextlib.AbstractAsyncContextManager`](contextlib.md#contextlib.AbstractAsyncContextManager "contextlib.AbstractAsyncContextManager")

  * [`dataclasses.Field`](dataclasses.md#dataclasses.Field "dataclasses.Field")

  * [`functools.cached_property`](functools.md#functools.cached_property "functools.cached_property")

  * [`functools.partialmethod`](functools.md#functools.partialmethod "functools.partialmethod")

  * [`os.PathLike`](os.md#os.PathLike "os.PathLike")

  * [`queue.LifoQueue`](queue.md#queue.LifoQueue "queue.LifoQueue")

  * [`queue.Queue`](queue.md#queue.Queue "queue.Queue")

  * [`queue.PriorityQueue`](queue.md#queue.PriorityQueue "queue.PriorityQueue")

  * [`queue.SimpleQueue`](queue.md#queue.SimpleQueue "queue.SimpleQueue")

  * [re.Pattern](re.md#re-objects)

  * [re.Match](re.md#match-objects)

  * [`shelve.BsdDbShelf`](shelve.md#shelve.BsdDbShelf "shelve.BsdDbShelf")

  * [`shelve.DbfilenameShelf`](shelve.md#shelve.DbfilenameShelf "shelve.DbfilenameShelf")

  * [`shelve.Shelf`](shelve.md#shelve.Shelf "shelve.Shelf")

  * [`types.MappingProxyType`](types.md#types.MappingProxyType "types.MappingProxyType")

  * [`weakref.WeakKeyDictionary`](3.标准库/weakref.md#weakref.WeakKeyDictionary "weakref.WeakKeyDictionary")

  * [`weakref.WeakMethod`](3.标准库/weakref.md#weakref.WeakMethod "weakref.WeakMethod")

  * [`weakref.WeakSet`](3.标准库/weakref.md#weakref.WeakSet "weakref.WeakSet")

  * [`weakref.WeakValueDictionary`](3.标准库/weakref.md#weakref.WeakValueDictionary "weakref.WeakValueDictionary")

#### `GenericAlias` 对象的特殊属性¶

应用参数后的泛型都实现了一些特殊的只读属性：

genericalias.__origin__¶

    

~~~
本属性指向未应用参数之前的泛型类：
~~~
    
    
~~~shell
>>> list[int].__origin__
<class 'list'>
~~~

genericalias.__args__¶

    

~~~
该属性是传给泛型类的原始 [`__class_getitem__()`](../reference/datamodel.md#object.__class_getitem__ "object.__class_getitem__") 的泛型所组成的 `tuple` (长度可能为 1):
~~~
    
    
~~~shell
>>> dict[str, list[int]].__args__
(<class 'str'>, list[int])
~~~

genericalias.__parameters__¶

    

~~~
该属性是延迟计算出来的一个元组（可能为空），包含了 `__args__` 中的类型变量。
~~~
    
    
~~~shell
>>> from typing import TypeVar

>>> T = TypeVar('T')
>>> list[T].__parameters__
(~T,)
~~~

备注

带有参数 [`typing.ParamSpec`](typing.md#typing.ParamSpec "typing.ParamSpec") 的 `GenericAlias` 对象，在类型替换后其 `__parameters__` 可能会不准确，因为 [`typing.ParamSpec`](typing.md#typing.ParamSpec "typing.ParamSpec") 主要用于静态类型检查。

genericalias.__unpacked__¶

    

~~~
一个布尔值，如果别名已使用 `*` 运算符进行解包则为真值 (参见 [`TypeVarTuple`](typing.md#typing.TypeVarTuple "typing.TypeVarTuple"))。

在 3.11 版本加入.

参见

[**PEP 484**](https://peps.python.org/pep-0484/) —— 类型注解
~~~
    

~~~
介绍 Python 中用于类型标注的框架。

[**PEP 585**](https://peps.python.org/pep-0585/) \- 标准多项集中的类型提示泛型
~~~
    

~~~
介绍了对标准库类进行原生形参化的能力，只要它们实现了特殊的类方法 [`__class_getitem__()`](../reference/datamodel.md#object.__class_getitem__ "object.__class_getitem__")。

[泛型（Generic）](typing.md#generics), [用户自定义泛型](typing.md#user-defined-generics) 和 [`typing.Generic`](typing.md#typing.Generic "typing.Generic")
~~~
    

~~~
有关如何实现可在运行时被形参化并能被静态类型检查器所识别的泛用类的文档。

在 3.9 版本加入.

### union 类型¶

联合对象包含了在多个 类型对象 上执行 `|` (按位或) 运算后的值。 这些类型主要用于 [类型标注](../glossary.md#term-annotation)。与 [`typing.Union`](typing.md#typing.Union "typing.Union") 相比，联合类型表达式可以实现更简洁的类型提示语法。

X | Y | ...
~~~
    

~~~
定义包含了 _X_ 、 _Y_ 等类型的 union 对象。 `X | Y` 表示 X 或 Y。相当于 `typing.Union[X, Y]` 。比如以下函数的参数应为类型 [`int`](functions.md#int "int") 或 [`float`](functions.md#float "float") ：
~~~
    
    
~~~python
def square(number: int | float) -> int | float:
    return number ** 2
~~~

备注

不可在运行时使用 `|` 操作数来定义有一个或多个成员为前向引用的并集。 例如，`int | "Foo"`，其中 `"Foo"` 是指向某个尚未定义的类的引用，在运行时将会失败。 对于包括前向引用的并集，请将整个表达式用字符串来表示，例如 `"int | Foo"`。

union_object == other

    

~~~
union 对象可与其他 union 对象进行比较。详细结果如下：

  * 多次组合的结果会平推：
    
        (int | str) | float == int | str | float
~~~

  * 冗余的类型会被删除：
    
        int | str | int == int | str
    

  * 在相互比较时，会忽略顺序：
    
        int | str == str | int
    

  * 与 `typing.union` 兼容：
    
        int | str == typing.Union[int, str]
    

  * Optional 类型可表示为与 `None` 的组合。
    
        str | None == typing.Optional[str]
    

isinstance(obj, union_object)

    

~~~
issubclass(obj, union_object)
~~~
    

~~~
[`isinstance()`](functions.md#isinstance "isinstance") 和 [`issubclass()`](functions.md#issubclass "issubclass") 也支持 union 对象：
~~~
    
    
~~~shell
>>> isinstance("", int | str)
True
~~~

但是联合对象中的 参数化泛型 将无法被检测:

    
    
~~~shell
>>> isinstance(1, int | list[int])  # short-circuit evaluation
True
>>> isinstance([1], int | list[int])
Traceback (most recent call last):
  ...
TypeError: isinstance() argument 2 cannot be a parameterized generic
~~~

union 对象构成的用户类型可以经由 [`types.UnionType`](types.md#types.UnionType "types.UnionType") 访问，并可用于 [`isinstance()`](functions.md#isinstance "isinstance") 检查。 而不能由类型直接实例化为对象：

    
    
~~~shell
>>> import types
>>> isinstance(int | str, types.UnionType)
True
>>> types.UnionType()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: cannot create 'types.UnionType' instances
~~~

备注

为了支持 `X | Y` 语法，类型对象加入了 `__or__()` 方法。若是元类已实现了 `__or__()`，union 也可以覆盖掉：

    
    
~~~shell
>>> class M(type):
...     def __or__(self, other):
...         return "Hello"
...
>>> class C(metaclass=M):
...     pass
...
>>> C | int
'Hello'
>>> int | C
int | __main__.C
~~~

参见

[**PEP 604**](https://peps.python.org/pep-0604/) —— 提出了 `X | Y` 语法和 union 类型。

在 3.10 版本加入.

## 其他内置类型¶

解释器支持一些其他种类的对象。 这些对象大都仅支持一两种操作。

### 模块¶

模块唯一的特殊操作是属性访问: `m.name`，这里 _m_ 为一个模块而 _name_ 访问定义在 _m_ 的符号表中的一个名称。 模块属性可以被赋值。 （请注意 [`import`](simple_stmts.md#import) 语句严格来说也是对模块对象的一种操作；`import foo` 不要求存在一个名为 _foo_ 的模块对象，而是要求存在一个对于名为 _foo_ 的模块的 (永久性) _定义_ 。）

每个模块都有一个特殊属性 `__dict__`。 这是包含模块的符号表的字典。 修改此字典将实际改变模块的符号表，但是无法直接对 `__dict__` 赋值 (你可以写 `m.__dict__['a'] = 1`，这会将 `m.a` 定义为 `1`，但是你不能写 `m.__dict__ = {}`)。 不建议直接修改 `__dict__`。

内置于解释器中的模块会写成这样: `<module 'sys' (built-in)>`。 如果是从一个文件加载，则会写成 `<module 'os' from '/usr/local/lib/pythonX.Y/os.pyc'>`。

### 类与类实例¶

关于这些类型请参阅 [对象、值与类型](datamodel.md#objects) 和 [类定义](compound_stmts.md#class)。

### 函数¶

函数对象是通过函数定义创建的。 对函数对象的唯一操作是调用它: `func(argument-list)`。

实际上存在两种不同的函数对象：内置函数和用户自定义函数。 两者支持同样的操作（调用函数），但实现方式不同，因此对象类型也不同。

更多信息请参阅 [函数定义](compound_stmts.md#function)。

### 方法¶

方法是使用属性表示法来调用的函数。 存在两种形式：内置方法（例如列表的 `append()` 方法）和类实例方法。 内置方法由支持它们的类型来描述。

如果你通过一个实例来访问方法（即定义在类命名空间内的函数），你会得到一个特殊对象: _绑定方法_ (或称 _实例方法_ ) 对象。 当被调用时，它会将 `self` 参数添加到参数列表。 绑定方法具有两个特殊的只读属性: `m.__self__` 操作该方法的对象，而 `m.__func__` 是实现该方法的函数。 调用 `m(arg-1, arg-2, ..., arg-n)` 完全等价于调用 `m.__func__(m.__self__, arg-1, arg-2, ..., arg-n)`。

与函数对象类似，绑定方法对象也支持获取任意属性。 但是，由于方法属性实际上保存于下层的函数对象中 (`meth.__func__`)，因此不允许设置绑定方法的方法属性。 尝试设置方法的属性将会导致引发 [`AttributeError`](3.标准库/exceptions.md#AttributeError "AttributeError")。 想要设置方法属性，你必须在下层的函数对象中显式地对其进行设置:

    
    
~~~shell
>>> class C:
...     def method(self):
...         pass
...
>>> c = C()
>>> c.method.whoami = 'my name is method'  # can't set on the method
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'method' object has no attribute 'whoami'
>>> c.method.__func__.whoami = 'my name is method'
>>> c.method.whoami
'my name is method'
~~~

更多信息请参阅 [标准类型层级结构](datamodel.md#types)。

### 代码对象¶

代码对象被具体实现用来表示“伪编译”的可执行 Python 代码，例如一个函数体。 它们不同于函数对象，因为它们不包含对其全局执行环境的引用。 代码对象由内置的 [`compile()`](functions.md#compile "compile") 函数返回，并可通过从函数对象的 `__code__` 属性从中提取。 另请参阅 [`code`](3.标准库/code.md#module-code "code: Facilities to implement read-eval-print loops.") 模块。

访问 `__code__` 会触发 [审计事件](3.标准库/sys.md#auditing) `object.__getattr__`，参数为 `obj` 和 `"__code__"`。

可以通过将代码对象（而非源码字符串）传给 [`exec()`](functions.md#exec "exec") 或 [`eval()`](functions.md#eval "eval") 内置函数来执行或求值。

更多信息请参阅 [标准类型层级结构](datamodel.md#types)。

### 类型对象¶

类型对象表示各种对象类型。 对象的类型可通过内置函数 [`type()`](functions.md#type "type") 来获取。 类型没有特殊的操作。 标准库模块 [`types`](types.md#module-types "types: Names for built-in types.") 定义了所有标准内置类型的名称。

类型以这样的写法来表示: `<class 'int'>`。

### 空对象¶

此对象会由不显式地返回值的函数所返回。 它不支持任何特殊的操作。 空对象只有一种值 `None` (这是个内置名称)。 `type(None)()` 会生成同一个单例。

该对象的写法为 `None`。

### 省略符对象¶

此对象常被用于切片 (参见 [切片](expressions.md#slicings))。 它不支持任何特殊的操作。 省略符对象只有一种值 [`Ellipsis`](constants.md#Ellipsis "Ellipsis") (这是个内置名称)。 `type(Ellipsis)()` 会生成 [`Ellipsis`](constants.md#Ellipsis "Ellipsis") 单例。

该对象的写法为 `Ellipsis` 或 `...`。

### 未实现对象¶

此对象会被作为比较和二元运算被应用于它们所不支持的类型时的返回值。 请参阅 [比较运算](expressions.md#comparisons) 了解更多信息。 未实现对象只有一种值 `NotImplemented`。 `type(NotImplemented)()` 会生成这个单例。

该对象的写法为 `NotImplemented`。

### 内部对象¶

有关此对象的信息请参阅 [标准类型层级结构](datamodel.md#types)。 其中描述了栈帧对象、回溯对象以及切片对象等等。

## 特殊属性¶

语言实现为部分对象类型添加了一些特殊的只读属性，它们具有各自的作用。 其中一些并不会被 [`dir()`](functions.md#dir "dir") 内置函数所列出。

object.__dict__¶

    

~~~
一个字典或其他类型的映射对象，用于存储对象的（可写）属性。

instance.__class__¶
~~~
    

~~~
类实例所属的类。

class.__bases__¶
~~~
    

~~~
由类对象的基类所组成的元组。

definition.__name__¶
~~~
    

~~~
类、函数、方法、描述器或生成器实例的名称。

definition.__qualname__¶
~~~
    

~~~
类、函数、方法、描述器或生成器实例的 [qualified name](../glossary.md#term-qualified-name)。

在 3.3 版本加入.

definition.__type_params__¶
~~~
    

~~~
以下对象的 [类型形参](../reference/compound_stmts.md#type-params): 泛型类、函数和 [类型别名](typing.md#type-aliases)。

在 3.12 版本加入.

class.__mro__¶
~~~
    

~~~
此属性是由类组成的元组，在方法解析期间会基于它来查找基类。

class.mro()¶
~~~
    

~~~
此方法可被一个元类来重载，以为其实例定制方法解析顺序。 它会在类实例化时被调用，其结果存储于 `__mro__` 之中。

class.__subclasses__()¶
~~~
    

~~~
每个类都存有对直接子类的弱引用列表。本方法返回所有存活引用的列表。列表的顺序按照子类定义的排列。例如：
~~~
    
    
~~~shell
>>> int.__subclasses__()
[<class 'bool'>, <enum 'IntEnum'>, <flag 'IntFlag'>, <class 're._constants._NamedIntConstant'>]
~~~

## 整数字符串转换长度限制¶

CPython 对于 [`int`](functions.md#int "int") 和 `str` 之间的转换有一个全局限制以缓解拒绝服务攻击。 此限制 _仅会_ 作用于十进制或其他以非二的乘方为基数的数字。 十六进制、八进制和二进制转换不受限制。 该限制可以被配置。

[`int`](functions.md#int "int") 类型在 CPython 中是存储为二进制形式的任意长度的数字（通常称为“大数字”）。 不存在可在线性时间内将一个字符串转换为二进制整数或将一个二进制整数转换为字符串的算法， _除非_ 基数为 2 的乘方。 对于基数为 10 来说已知最好的算法也有亚二次方复杂度。 转换一个大数值如 `int('1' * 500_000)` 在快速的 CPU 上也会花费一秒以上的时间。

限制转换大小提供了一个避免 [CVE-2020-10735](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-10735) 的可行方法。

此限制会在可能涉及非线性转换算法时作用于输入或输出字符串中的数字型字符数量。 下划线和正负号不计入限制数量。

当一个操作会超出限制时，将引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError"):

    
    
~~~shell
>>> import sys
>>> sys.set_int_max_str_digits(4300)  # Illustrative, this is the default.
>>> _ = int('2' * 5432)
Traceback (most recent call last):
...
ValueError: Exceeds the limit (4300 digits) for integer string conversion: value has 5432 digits; use sys.set_int_max_str_digits() to increase the limit
>>> i = int('2' * 4300)
>>> len(str(i))
4300
>>> i_squared = i*i
>>> len(str(i_squared))
Traceback (most recent call last):
...
ValueError: Exceeds the limit (4300 digits) for integer string conversion; use sys.set_int_max_str_digits() to increase the limit
>>> len(hex(i_squared))
7144
>>> assert int(hex(i_squared), base=16) == i*i  # Hexadecimal is unlimited.
~~~

默认限制为 4300 位即 [`sys.int_info.default_max_str_digits`](3.标准库/sys.md#sys.int_info "sys.int_info") 的值。 最低限制可被配置为 640 位即 [`sys.int_info.str_digits_check_threshold`](3.标准库/sys.md#sys.int_info "sys.int_info")。

验证:

    
    
~~~shell
>>> import sys
>>> assert sys.int_info.default_max_str_digits == 4300, sys.int_info
>>> assert sys.int_info.str_digits_check_threshold == 640, sys.int_info
>>> msg = int('578966293710682886880994035146873798396722250538762761564'
...           '9252925514383915483333812743580549779436104706260696366600'
...           '571186405732').to_bytes(53, 'big')
...
~~~

在 3.11 版本加入.

### 受影响的 API¶

此限制仅会作用于 [`int`](functions.md#int "int") 和 `str` 和 `bytes` 之间存在速度变慢可能的转换:

  * `int(string)` 默认以 10 为基数。

  * `int(string, base)` 用于所有不为 2 的乘方的基数。

  * `str(integer)`。

  * `repr(integer)`。

  * 任何其他目标是以 10 为基数的字符串转换，例如 `f"{integer}"`, `"{}".format(integer)` 或 `b"%d" % integer`。

此限制不会作用于使用线性算法的函数:

  * `int(string, base)` 中 base 可以为 2, 4, 8, 16 或 32。

  * `int.from_bytes()` 和 `int.to_bytes()`。

  * [`hex()`](functions.md#hex "hex"), [`oct()`](functions.md#oct "oct"), [`bin()`](functions.md#bin "bin")。

  * [格式规格迷你语言](string.md#formatspec) 用于十六进制、八进制和二进制数。

  * `str` 至 [`float`](functions.md#float "float")。

  * `str` 至 [`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal")。

### 配置限制值¶

在 Python 启动之前你可以使用环境变量或解释器命令行旗标来配置限制值:

  * [`PYTHONINTMAXSTRDIGITS`](cmdline.md#envvar-PYTHONINTMAXSTRDIGITS)，例如 `PYTHONINTMAXSTRDIGITS=640 python3` 是将限制设为 640 而 `PYTHONINTMAXSTRDIGITS=0 python3` 是禁用此限制。

  * [`-X int_max_str_digits`](cmdline.md#cmdoption-X)，例如 `python3 -X int_max_str_digits=640`

  * [`sys.flags.int_max_str_digits`](3.标准库/sys.md#sys.flags.int_max_str_digits "sys.flags.int_max_str_digits") 包含 [`PYTHONINTMAXSTRDIGITS`](cmdline.md#envvar-PYTHONINTMAXSTRDIGITS) 或 [`-X int_max_str_digits`](cmdline.md#cmdoption-X) 的值。 如果环境变量和 `-X` 选项均有设置，则 `-X` 选项优先。 值为 _-1_ 表示两者均未设置，因此会在初始化时使用 [`sys.int_info.default_max_str_digits`](3.标准库/sys.md#sys.int_info.default_max_str_digits "sys.int_info.default_max_str_digits") 的值。

从代码中，你可以检查当前的限制并使用这些 [`sys`](3.标准库/sys.md#module-sys "sys: Access system-specific parameters and functions.") API 来设置新值:

  * [`sys.get_int_max_str_digits()`](3.标准库/sys.md#sys.get_int_max_str_digits "sys.get_int_max_str_digits") 和 [`sys.set_int_max_str_digits()`](3.标准库/sys.md#sys.set_int_max_str_digits "sys.set_int_max_str_digits") 是解释器级限制的读取器和设置器。 子解释器具有它们自己的限制。

有关默认值和最小值的信息可在 [`sys.int_info`](3.标准库/sys.md#sys.int_info "sys.int_info") 中找到:

  * [`sys.int_info.default_max_str_digits`](3.标准库/sys.md#sys.int_info "sys.int_info") 是已编译的默认限制。

  * [`sys.int_info.str_digits_check_threshold`](3.标准库/sys.md#sys.int_info "sys.int_info") 是该限制可接受的最低值（禁用该限制的 0 除外）。

在 3.11 版本加入.

小心

设置较低的限制值 _可能_ 导致问题。 虽然不常见，但还是会有在其源代码中包含超出最小阈值的十进制整数常量的代码存在。 设置此限制的一个后果将是包含比此限制长的十进制整数字面值的 Python 源代码将在解析期间遇到错误，通常是在启动时或导入时甚至是在安装时 —— 只要对于某个代码还不存在已更新的 `.pyc` 就会发生。 一种在包含此类大数值常量的源代码中绕过该问题的办法是将它们转换为不受限制的 `0x` 十六进制形式。

如果你使用了较低的限制则请要彻底地测试你的应用程序。 确保你的测试通过环境变量或旗标尽早设置该限制来运行以便在启动期间甚至是在可能发起调用 Python 来将 `.py` 源文件预编译为 `.pyc` 文件的任何安装步骤其间应用该限制。

### 推荐配置¶

默认的 [`sys.int_info.default_max_str_digits`](3.标准库/sys.md#sys.int_info.default_max_str_digits "sys.int_info.default_max_str_digits") 被预期对于大多数应用程序来说都是合理的。 如果你的应用程序需要不同的限制值，请使用不预设 Python 版本的代码从你的主入口点进行设置，因为这些 API 是在 3.12 之前的版本所发布的安全补丁中添加的。

示例：

    
    
~~~shell
>>> import sys
>>> if hasattr(sys, "set_int_max_str_digits"):
...     upper_bound = 68000
...     lower_bound = 4004
...     current_limit = sys.get_int_max_str_digits()
...     if current_limit == 0 or current_limit > upper_bound:
...         sys.set_int_max_str_digits(upper_bound)
...     elif current_limit < lower_bound:
...         sys.set_int_max_str_digits(lower_bound)
~~~

如果你需要完全禁用它，请将其设为 `0`。

备注

[1]

有关这些特殊方法的额外信息可参看 Python 参考指南 ([基本定制](datamodel.md#customization))。

[2]

作为结果，列表 `[1, 2]` 与 `[1.0, 2.0]` 是相等的，元组的情况也类似。

[3]

必须如此，因为解析器无法判断操作数的类型。

[4] (1,2,3,4)

区分大小写的字符是指所属一般类别属性为 "Lu" (Letter, uppercase), "Ll" (Letter, lowercase) 或 "Lt" (Letter, titlecase) 之一的字符。

[5] (1,2)

若只是要格式化一个元组，则应提供一个单例元组，其中只包含一个元素，就是需要格式化的那个元组。

