# `operator` \--- 标准运算符替代函数¶

**源代码:** [Lib/operator.py](https://github.com/python/cpython/tree/3.12/Lib/operator.py)

* * *

`operator` 模块提供了一套与Python的内置运算符对应的高效率函数。例如，`operator.add(x, y)` 与表达式 `x+y` 相同。 许多函数名与特殊方法名相同，只是没有双下划线。为了向后兼容性，也保留了许多包含双下划线的函数。为了表述清楚，建议使用没有双下划线的函数。

函数包含的种类有：对象的比较运算、逻辑运算、数学运算以及序列运算。

对象比较函数适用于所有的对象，函数名根据它们对应的比较运算符命名。

operator.lt( _a_ , _b_ )¶

operator.le( _a_ , _b_ )¶

operator.eq( _a_ , _b_ )¶

operator.ne( _a_ , _b_ )¶

operator.ge( _a_ , _b_ )¶

operator.gt( _a_ , _b_ )¶

operator.__lt__( _a_ , _b_ )¶

operator.__le__( _a_ , _b_ )¶

operator.__eq__( _a_ , _b_ )¶

operator.__ne__( _a_ , _b_ )¶

operator.__ge__( _a_ , _b_ )¶

operator.__gt__( _a_ , _b_ )¶

    

~~~
在 _a_ 和 _b_ 之间进行全比较。具体的，`lt(a, b)` 与 `a < b` 相同， `le(a, b)` 与 `a <= b` 相同，`eq(a, b)` 与 `a == b` 相同，`ne(a, b)` 与 `a != b` 相同，`gt(a, b)` 与 `a > b` 相同，`ge(a, b)` 与 `a >= b` 相同。注意这些函数可以返回任何值，无论它是否可当作布尔值。关于全比较的更多信息请参考 [比较运算](../reference/expressions.md#comparisons) 。

逻辑运算通常也适用于所有对象，并且支持真值检测、标识检测和布尔运算：

operator.not_( _obj_ )¶

operator.__not__( _obj_ )¶
~~~
    

~~~
返回 [`not`](../reference/expressions.md#not) _obj_ 的结果。 （请注意对象实例并没有 `__not__()` 方法；只有解释器核心可定义此操作。 结果会受到 [`__bool__()`](../reference/datamodel.md#object.__bool__ "object.__bool__") 和 [`__len__()`](../reference/datamodel.md#object.__len__ "object.__len__") 方法的影响。）

operator.truth( _obj_ )¶
~~~
    

~~~
如果 _obj_ 为真值则返回 [`True`](constants.md#True "True")，否则返回 [`False`](constants.md#False "False")。 这等价于使用 [`bool`](functions.md#bool "bool") 构造器。

operator.is_( _a_ , _b_ )¶
~~~
    

~~~
返回 `a is b`。 检测对象标识。

operator.is_not( _a_ , _b_ )¶
~~~
    

~~~
返回 `a is not b`。 检测对象标识。

数学和按位运算的种类是最多的：

operator.abs( _obj_ )¶

operator.__abs__( _obj_ )¶
~~~
    

~~~
返回 _obj_ 的绝对值。

operator.add( _a_ , _b_ )¶

operator.__add__( _a_ , _b_ )¶
~~~
    

~~~
对于数字 _a_ 和 _b_ ，返回 `a + b`。

operator.and_( _a_ , _b_ )¶

operator.__and__( _a_ , _b_ )¶
~~~
    

~~~
返回 _x_ 和 _y_ 按位与的结果。

operator.floordiv( _a_ , _b_ )¶

operator.__floordiv__( _a_ , _b_ )¶
~~~
    

~~~
返回 `a // b`。

operator.index( _a_ )¶

operator.__index__( _a_ )¶
~~~
    

~~~
返回 _a_ 转换为整数的结果。 等价于 `a.__index__()`。

在 3.10 版本发生变更: 结果总是为 [`int`](functions.md#int "int") 类型。 在之前版本中，结果可能为 `int` 的子类的实例。

operator.inv( _obj_ )¶

operator.invert( _obj_ )¶

operator.__inv__( _obj_ )¶

operator.__invert__( _obj_ )¶
~~~
    

~~~
返回数字 _obj_ 按位取反的结果。 这等价于 `~obj`。

operator.lshift( _a_ , _b_ )¶

operator.__lshift__( _a_ , _b_ )¶
~~~
    

~~~
返回 _a_ 左移 _b_ 位的结果。

operator.mod( _a_ , _b_ )¶

operator.__mod__( _a_ , _b_ )¶
~~~
    

~~~
返回 `a % b`。

operator.mul( _a_ , _b_ )¶

operator.__mul__( _a_ , _b_ )¶
~~~
    

~~~
对于数字 _a_ 和 _b_ ，返回 `a * b`。

operator.matmul( _a_ , _b_ )¶

operator.__matmul__( _a_ , _b_ )¶
~~~
    

~~~
返回 `a @ b`。

在 3.5 版本加入.

operator.neg( _obj_ )¶

operator.__neg__( _obj_ )¶
~~~
    

~~~
返回 _obj_ 取负的结果 (`-obj`)。

operator.or_( _a_ , _b_ )¶

operator.__or__( _a_ , _b_ )¶
~~~
    

~~~
返回 _a_ 和 _b_ 按位或的结果。

operator.pos( _obj_ )¶

operator.__pos__( _obj_ )¶
~~~
    

~~~
返回 _obj_ 取正的结果 (`+obj`)。

operator.pow( _a_ , _b_ )¶

operator.__pow__( _a_ , _b_ )¶
~~~
    

~~~
对于数字 _a_ 和 _b_ ，返回 `a ** b`。

operator.rshift( _a_ , _b_ )¶

operator.__rshift__( _a_ , _b_ )¶
~~~
    

~~~
返回 _a_ 右移 _b_ 位的结果。

operator.sub( _a_ , _b_ )¶

operator.__sub__( _a_ , _b_ )¶
~~~
    

~~~
返回 `a - b`。

operator.truediv( _a_ , _b_ )¶

operator.__truediv__( _a_ , _b_ )¶
~~~
    

~~~
返回 `a / b` 例如 2/3 将等于 .66 而不是 0。 这也被称为“真”除法。

operator.xor( _a_ , _b_ )¶

operator.__xor__( _a_ , _b_ )¶
~~~
    

~~~
返回 _a_ 和 _b_ 按位异或的结果。

适用于序列的操作（其中一些也适用于映射）包括：

operator.concat( _a_ , _b_ )¶

operator.__concat__( _a_ , _b_ )¶
~~~
    

~~~
对于序列 _a_ 和 _b_ ，返回 `a + b`。

operator.contains( _a_ , _b_ )¶

operator.__contains__( _a_ , _b_ )¶
~~~
    

~~~
返回 `b in a` 检测的结果。 请注意操作数是反序的。

operator.countOf( _a_ , _b_ )¶
~~~
    

~~~
返回 _b_ 在 _a_ 中的出现次数。

operator.delitem( _a_ , _b_ )¶

operator.__delitem__( _a_ , _b_ )¶
~~~
    

~~~
移除 _a_ 中索引号为 _b_ 的值。

operator.getitem( _a_ , _b_ )¶

operator.__getitem__( _a_ , _b_ )¶
~~~
    

~~~
返回 _a_ 中索引为 _b_ 的值。

operator.indexOf( _a_ , _b_ )¶
~~~
    

~~~
返回 _b_ 在 _a_ 中首次出现所在的索引号。

operator.setitem( _a_ , _b_ , _c_ )¶

operator.__setitem__( _a_ , _b_ , _c_ )¶
~~~
    

~~~
将 _a_ 中索引号为 _b_ 的值设为 _c_ 。

operator.length_hint( _obj_ , _default =0_)¶
~~~
    

~~~
返回对象 _obj_ 的估计长度。 首先尝试返回其实际长度，再使用 [`object.__length_hint__()`](../reference/datamodel.md#object.__length_hint__ "object.__length_hint__") 得出估计值，最后返回默认值。

在 3.4 版本加入.

以下操作适用于可调用对象:

operator.call( _obj_ , _/_ , _* args_, _** kwargs_)¶

operator.__call__( _obj_ , _/_ , _* args_, _** kwargs_)¶
~~~
    

~~~
返回 `obj(*args, **kwargs)`。

在 3.11 版本加入.

`operator` 模块还定义了一些用于常规属性和条目查找的工具。 这些工具适合用来编写快速字段提取器作为 [`map()`](functions.md#map "map"), [`sorted()`](functions.md#sorted "sorted"), [`itertools.groupby()`](itertools.md#itertools.groupby "itertools.groupby") 或其他需要相应函数参数的函数的参数。

operator.attrgetter( _attr_ )¶

operator.attrgetter( _* attrs_)
~~~
    

~~~
返回一个可从操作数中获取 _attr_ 的可调用对象。 如果请求了一个以上的属性，则返回一个属性元组。 属性名称还可包含点号。 例如：

  * 在 `f = attrgetter('name')` 之后，调用 `f(b)` 将返回 `b.name`。

  * 在 `f = attrgetter('name', 'date')` 之后，调用 `f(b)` 将返回 `(b.name, b.date)`。

  * 在 `f = attrgetter('name.first', 'name.last')` 之后，调用 `f(b)` 将返回 `(b.name.first, b.name.last)`。

等价于:
~~~
    
    
~~~python
def attrgetter(*items):
    if any(not isinstance(item, str) for item in items):
        raise TypeError('attribute name must be a string')
    if len(items) == 1:
        attr = items[0]
        def g(obj):
            return resolve_attr(obj, attr)
    else:
        def g(obj):
            return tuple(resolve_attr(obj, attr) for attr in items)
    return g

def resolve_attr(obj, attr):
    for name in attr.split("."):
        obj = getattr(obj, name)
    return obj
~~~

operator.itemgetter( _item_ )¶

operator.itemgetter( _* items_)

    

~~~
返回一个使用操作数的 `__getitem__()` 方法从操作数中获取 _item_ 的可调用对象。 如果指定了多个条目，则返回一个查找值的元组。 例如：

  * 在 `f = itemgetter(2)` 之后，调用 `f(r)` 将返回 `r[2]`。

  * 在 `g = itemgetter(2, 5, 3)` 之后，调用 `g(r)` 将返回 `(r[2], r[5], r[3])`。

等价于:
~~~
    
    
~~~python
def itemgetter(*items):
    if len(items) == 1:
        item = items[0]
        def g(obj):
            return obj[item]
    else:
        def g(obj):
            return tuple(obj[item] for item in items)
    return g
~~~

条目可以是操作数的 `__getitem__()` 方法 所接受的任何类型。 字典接受任意 [hashable](../glossary.md#term-hashable) 值。 列表、元组和字符串接受索引或切片对象:

    
    
~~~shell
>>> itemgetter(1)('ABCDEFG')
'B'
>>> itemgetter(1, 3, 5)('ABCDEFG')
('B', 'D', 'F')
>>> itemgetter(slice(2, None))('ABCDEFG')
'CDEFG'
>>> soldier = dict(rank='captain', name='dotterbart')
>>> itemgetter('rank')(soldier)
'captain'
~~~

使用 `itemgetter()` 从元组的记录中提取特定字段的例子：

    
    
~~~shell
>>> inventory = [('apple', 3), ('banana', 2), ('pear', 5), ('orange', 1)]
>>> getcount = itemgetter(1)
>>> list(map(getcount, inventory))
[3, 2, 5, 1]
>>> sorted(inventory, key=getcount)
[('orange', 1), ('banana', 2), ('apple', 3), ('pear', 5)]
~~~

operator.methodcaller( _name_ , _/_ , _* args_, _** kwargs_)¶

    

~~~
返回一个在操作数上调用 _name_ 方法的可调用对象。 如果给出额外的参数和/或关键字参数，它们也将被传给该方法。 例如：

  * 在 `f = methodcaller('name')` 之后，调用 `f(b)` 将返回 `b.name()`。

  * 在 `f = methodcaller('name', 'foo', bar=1)` 之后，调用 `f(b)` 将返回 `b.name('foo', bar=1)`。

等价于:
~~~
    
    
~~~python
def methodcaller(name, /, *args, **kwargs):
    def caller(obj):
        return getattr(obj, name)(*args, **kwargs)
    return caller
~~~

## 将运算符映射到函数¶

以下表格显示了抽象运算是如何对应于 Python 语法中的运算符和 `operator` 模块中的函数的。

运算

|

语法

|

函数  
  
---|---|---  
  
加法

|

`a + b`

|

`add(a, b)`  
  
字符串拼接

|

`seq1 + seq2`

|

`concat(seq1, seq2)`  
  
包含测试

|

`obj in seq`

|

`contains(seq, obj)`  
  
除法

|

`a / b`

|

`truediv(a, b)`  
  
除法

|

`a // b`

|

`floordiv(a, b)`  
  
按位与

|

`a & b`

|

`and_(a, b)`  
  
按位异或

|

`a ^ b`

|

`xor(a, b)`  
  
按位取反

|

`~ a`

|

`invert(a)`  
  
按位或

|

`a | b`

|

`or_(a, b)`  
  
取幂

|

`a ** b`

|

`pow(a, b)`  
  
标识

|

`a is b`

|

`is_(a, b)`  
  
标识

|

`a is not b`

|

`is_not(a, b)`  
  
索引赋值

|

`obj[k] = v`

|

`setitem(obj, k, v)`  
  
索引删除

|

`del obj[k]`

|

`delitem(obj, k)`  
  
索引取值

|

`obj[k]`

|

`getitem(obj, k)`  
  
左移

|

`a << b`

|

`lshift(a, b)`  
  
取模

|

`a % b`

|

`mod(a, b)`  
  
乘法

|

`a * b`

|

`mul(a, b)`  
  
矩阵乘法

|

`a @ b`

|

`matmul(a, b)`  
  
取反（算术）

|

`- a`

|

`neg(a)`  
  
取反（逻辑）

|

`not a`

|

`not_(a)`  
  
正数

|

`+ a`

|

`pos(a)`  
  
右移

|

`a >> b`

|

`rshift(a, b)`  
  
切片赋值

|

`seq[i:j] = values`

|

`setitem(seq, slice(i, j), values)`  
  
切片删除

|

`del seq[i:j]`

|

`delitem(seq, slice(i, j))`  
  
切片取值

|

`seq[i:j]`

|

`getitem(seq, slice(i, j))`  
  
字符串格式化

|

`s % obj`

|

`mod(s, obj)`  
  
减法

|

`a - b`

|

`sub(a, b)`  
  
真值测试

|

`obj`

|

`truth(obj)`  
  
比较

|

`a < b`

|

`lt(a, b)`  
  
比较

|

`a <= b`

|

`le(a, b)`  
  
相等

|

`a == b`

|

`eq(a, b)`  
  
不等

|

`a != b`

|

`ne(a, b)`  
  
比较

|

`a >= b`

|

`ge(a, b)`  
  
比较

|

`a > b`

|

`gt(a, b)`  
  
## 原地运算符¶

许多运算都有“原地”版本。 以下列出的是提供对原地运算符相比通常语法更底层访问的函数，例如 [statement](../glossary.md#term-statement) `x += y` 相当于 `x = operator.iadd(x, y)`。 换一种方式来讲就是 `z = operator.iadd(x, y)` 等价于语句块 `z = x; z += y`。

在这些例子中，请注意当调用一个原地方法时，运算和赋值是分成两个步骤来执行的。 下面列出的原地函数只执行第一步即调用原地方法。 第二步赋值则不加处理。

对于不可变的目标例如字符串、数字和元组，更新的值会被计算，但不会被再被赋值给输入变量：

    
    
~~~shell
>>> a = 'hello'
>>> iadd(a, ' world')
'hello world'
>>> a
'hello'
~~~

对于可变的目标例如列表和字典，原地方法将执行更新，因此不需要后续赋值操作：

    
    
~~~shell
>>> s = ['h', 'e', 'l', 'l', 'o']
>>> iadd(s, [' ', 'w', 'o', 'r', 'l', 'd'])
['h', 'e', 'l', 'l', 'o', ' ', 'w', 'o', 'r', 'l', 'd']
>>> s
['h', 'e', 'l', 'l', 'o', ' ', 'w', 'o', 'r', 'l', 'd']
~~~

operator.iadd( _a_ , _b_ )¶

operator.__iadd__( _a_ , _b_ )¶

    

~~~
`a = iadd(a, b)` 等价于 `a += b`。

operator.iand( _a_ , _b_ )¶

operator.__iand__( _a_ , _b_ )¶
~~~
    

~~~
`a = iand(a, b)` 等价于 `a &= b`。

operator.iconcat( _a_ , _b_ )¶

operator.__iconcat__( _a_ , _b_ )¶
~~~
    

~~~
`a = iconcat(a, b)` 等价于 `a += b` 其中 _a_ 和 _b_ 为序列。

operator.ifloordiv( _a_ , _b_ )¶

operator.__ifloordiv__( _a_ , _b_ )¶
~~~
    

~~~
`a = ifloordiv(a, b)` 等价于 `a //= b`。

operator.ilshift( _a_ , _b_ )¶

operator.__ilshift__( _a_ , _b_ )¶
~~~
    

~~~
`a = ilshift(a, b)` 等价于 `a <<= b`。

operator.imod( _a_ , _b_ )¶

operator.__imod__( _a_ , _b_ )¶
~~~
    

~~~
`a = imod(a, b)` 等价于 `a %= b`。

operator.imul( _a_ , _b_ )¶

operator.__imul__( _a_ , _b_ )¶
~~~
    

~~~
`a = imul(a, b)` 等价于 `a *= b`。

operator.imatmul( _a_ , _b_ )¶

operator.__imatmul__( _a_ , _b_ )¶
~~~
    

~~~
`a = imatmul(a, b)` 等价于 `a @= b`。

在 3.5 版本加入.

operator.ior( _a_ , _b_ )¶

operator.__ior__( _a_ , _b_ )¶
~~~
    

~~~
`a = ior(a, b)` 等价于 `a |= b`。

operator.ipow( _a_ , _b_ )¶

operator.__ipow__( _a_ , _b_ )¶
~~~
    

~~~
`a = ipow(a, b)` 等价于 `a **= b`。

operator.irshift( _a_ , _b_ )¶

operator.__irshift__( _a_ , _b_ )¶
~~~
    

~~~
`a = irshift(a, b)` 等价于 `a >>= b`。

operator.isub( _a_ , _b_ )¶

operator.__isub__( _a_ , _b_ )¶
~~~
    

~~~
`a = isub(a, b)` 等价于 `a -= b`。

operator.itruediv( _a_ , _b_ )¶

operator.__itruediv__( _a_ , _b_ )¶
~~~
    

~~~
`a = itruediv(a, b)` 等价于 `a /= b`。

operator.ixor( _a_ , _b_ )¶

operator.__ixor__( _a_ , _b_ )¶
~~~
    

~~~
