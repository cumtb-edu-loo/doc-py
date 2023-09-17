# 排序指南¶

作者:

    

~~~
Andrew Dalke 和 Raymond Hettinger

发布版本:
~~~
    

~~~
0.1

Python 列表有一个内置的 [`list.sort()`](../library/stdtypes.md#list.sort "list.sort") 方法可以直接修改列表。还有一个 [`sorted()`](../library/functions.md#sorted "sorted") 内置函数，它会从一个可迭代对象构建一个新的排序列表。

在本文档中，我们将探索使用Python对数据进行排序的各种技术。

## 基本排序¶

简单的升序排序非常简单：只需调用 [`sorted()`](../library/functions.md#sorted "sorted") 函数。它返回一个新的排序后列表：
~~~
    
    
~~~shell
>>> sorted([5, 2, 3, 1, 4])
[1, 2, 3, 4, 5]
~~~

你也可以使用 [`list.sort()`](../library/stdtypes.md#list.sort "list.sort") 方法，它会直接修改原列表（并返回 `None` 以避免混淆），通常来说它不如 [`sorted()`](../library/functions.md#sorted "sorted") 方便 ——— 但如果你不需要原列表，它会更有效率。

    
    
~~~shell
>>> a = [5, 2, 3, 1, 4]
>>> a.sort()
>>> a
[1, 2, 3, 4, 5]
~~~

另外一个区别是， [`list.sort()`](../library/stdtypes.md#list.sort "list.sort") 方法只是为列表定义的，而 [`sorted()`](../library/functions.md#sorted "sorted") 函数可以接受任何可迭代对象。

    
    
~~~shell
>>> sorted({1: 'D', 2: 'B', 3: 'B', 4: 'E', 5: 'A'})
[1, 2, 3, 4, 5]
~~~

## 关键函数¶

[`list.sort()`](../library/stdtypes.md#list.sort "list.sort") 和 [`sorted()`](../library/functions.md#sorted "sorted") 都有一个 _key_ 形参用来指定在进行比较前要在每个列表元素上调用的函数（或其他可调用对象）。

例如，下面是一个不区分大小写的字符串比较：

    
    
~~~shell
>>> sorted("This is a test string from Andrew".split(), key=str.lower)
['a', 'Andrew', 'from', 'is', 'string', 'test', 'This']
~~~

_key_ 形参的值应该是个函数（或其他可调用对象），它接受一个参数并返回一个用于排序的键。 这种机制速度很快，因为对于每个输入记录只会调用一次键函数。

一种常见的模式是使用对象的一些索引作为键对复杂对象进行排序。例如：

    
    
~~~shell
>>> student_tuples = [
...     ('john', 'A', 15),
...     ('jane', 'B', 12),
...     ('dave', 'B', 10),
... ]
>>> sorted(student_tuples, key=lambda student: student[2])   # sort by age
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
~~~

同样的技术也适用于具有命名属性的对象。例如：

    
    
~~~shell
>>> class Student:
...     def __init__(self, name, grade, age):
...         self.name = name
...         self.grade = grade
...         self.age = age
...     def __repr__(self):
...         return repr((self.name, self.grade, self.age))

>>> student_objects = [
...     Student('john', 'A', 15),
...     Student('jane', 'B', 12),
...     Student('dave', 'B', 10),
... ]
>>> sorted(student_objects, key=lambda student: student.age)   # sort by age
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
~~~

## Operator 模块函数¶

上面显示的键函数模式非常常见，因此 Python 提供了便利功能，使访问器功能更容易，更快捷。 [`operator`](../library/operator.md#module-operator "operator: Functions corresponding to the standard operators.") 模块有 [`itemgetter()`](../library/operator.md#operator.itemgetter "operator.itemgetter") 、 [`attrgetter()`](../library/operator.md#operator.attrgetter "operator.attrgetter") 和 [`methodcaller()`](../library/operator.md#operator.methodcaller "operator.methodcaller") 函数。

使用这些函数，上述示例变得更简单，更快捷：

    
    
~~~shell
>>> from operator import itemgetter, attrgetter

>>> sorted(student_tuples, key=itemgetter(2))
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]

>>> sorted(student_objects, key=attrgetter('age'))
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
~~~

Operator 模块功能允许多级排序。 例如，按 _grade_ 排序，然后按 _age_ 排序：

    
    
~~~shell
>>> sorted(student_tuples, key=itemgetter(1,2))
[('john', 'A', 15), ('dave', 'B', 10), ('jane', 'B', 12)]

>>> sorted(student_objects, key=attrgetter('grade', 'age'))
[('john', 'A', 15), ('dave', 'B', 10), ('jane', 'B', 12)]
~~~

## 升序和降序¶

[`list.sort()`](../library/stdtypes.md#list.sort "list.sort") 和 [`sorted()`](../library/functions.md#sorted "sorted") 接受布尔值的 _reverse_ 参数。这用于标记降序排序。 例如，要以反向 _age_ 顺序获取学生数据：

    
    
~~~shell
>>> sorted(student_tuples, key=itemgetter(2), reverse=True)
[('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]

>>> sorted(student_objects, key=attrgetter('age'), reverse=True)
[('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
~~~

## 排序稳定性和排序复杂度¶

排序保证是 [稳定](https://en.wikipedia.org/wiki/Sorting_algorithm#Stability) 的。 这意味着当多个记录具有相同的键值时，将保留其原始顺序。

    
    
~~~shell
>>> data = [('red', 1), ('blue', 1), ('red', 2), ('blue', 2)]
>>> sorted(data, key=itemgetter(0))
[('blue', 1), ('blue', 2), ('red', 1), ('red', 2)]
~~~

注意 _blue_ 的两个记录如何保留它们的原始顺序，以便 `('blue', 1)` 保证在 `('blue', 2)` 之前。

这个美妙的属性允许你在一系列排序步骤中构建复杂的排序。例如，要按 _grade_ 降序然后 _age_ 升序对学生数据进行排序，请先 _age_ 排序，然后再使用 _grade_ 排序：

    
    
~~~shell
>>> s = sorted(student_objects, key=attrgetter('age'))     # sort on secondary key
>>> sorted(s, key=attrgetter('grade'), reverse=True)       # now sort on primary key, descending
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
~~~

这可以被抽象为一个包装函数，该函数能接受一个列表以及字段和顺序的元组，以对它们进行多重排序。

    
    
~~~shell
>>> def multisort(xs, specs):
...     for key, reverse in reversed(specs):
...         xs.sort(key=attrgetter(key), reverse=reverse)
...     return xs

>>> multisort(list(student_objects), (('grade', True), ('age', False)))
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
~~~

Python 中使用的 [Timsort](https://en.wikipedia.org/wiki/Timsort) 算法可以有效地进行多种排序，因为它可以利用数据集中已存在的任何排序。

## 装饰-排序-取消装饰¶

这个三个步骤被称为 Decorate-Sort-Undecorate ：

  * 首先，初始列表使用控制排序顺序的新值进行修饰。

  * 然后，装饰列表已排序。

  * 最后，删除装饰，创建一个仅包含新排序中初始值的列表。

例如，要使用DSU方法按 _grade_ 对学生数据进行排序：

    
    
~~~shell
>>> decorated = [(student.grade, i, student) for i, student in enumerate(student_objects)]
>>> decorated.sort()
>>> [student for grade, i, student in decorated]               # undecorate
[('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
~~~

这方法语有效是因为元组按字典顺序进行比较，先比较第一项；如果它们相同则比较第二个项目，依此类推。

不一定在所有情况下都要在装饰列表中包含索引 _i_ ，但包含它有两个好处：

  * 排序是稳定的——如果两个项具有相同的键，它们的顺序将保留在排序列表中。

  * 原始项目不必具有可比性，因为装饰元组的排序最多由前两项决定。 因此，例如原始列表可能包含无法直接排序的复数。

这个方法的另一个名字是 Randal L. Schwartz 在 Perl 程序员中推广的 [Schwartzian transform](https://en.wikipedia.org/wiki/Schwartzian_transform)。

既然 Python 排序提供了键函数，那么通常不需要这种技术。

## 比较函数¶

与返回一个用于排序的绝对值的键函数不同，比较函数是计算两个输入的相对排序。

例如，一个 [天平](https://upload.wikimedia.org/wikipedia/commons/1/17/Balance_à_tabac_1850.JPG) 会比较两个样本并给出一个相对排序：较轻、相等或较重。 类似地，一个比较函数如 `cmp(a, b)` 将返回一个负值表示小于，零表示相等，或是一个正值表示大于。

当从其他语言转写算法时经常会遇到比较函数。 此外，某些库也提供了比较函数作为其 API 的组成部分。 例如，[`locale.strcoll()`](../library/locale.md#locale.strcoll "locale.strcoll") 就是一个比较函数。

为了适应这些情况，Python 提供了 [`functools.cmp_to_key`](../library/functools.md#functools.cmp_to_key "functools.cmp_to_key") 用来包装比较函数使其可以作为键函数来使用:

    
    
~~~
sorted(words, key=cmp_to_key(strcoll))  # locale-aware sort order
~~~

## 杂项说明¶

  * 对于可感知语言区域的排序，请使用 [`locale.strxfrm()`](../library/locale.md#locale.strxfrm "locale.strxfrm") 作为键函数或使用 [`locale.strcoll()`](../library/locale.md#locale.strcoll "locale.strcoll") 作为比较函数。 因为在不同语言中即便字母表相同“字母”排列顺序也可能不同所以这样做是必要的。

  * _reverse_ 参数仍然保持排序稳定性（因此具有相等键的记录保留原始顺序）。 有趣的是，通过使用内置的 [`reversed()`](../library/functions.md#reversed "reversed") 函数两次，可以在没有参数的情况下模拟该效果：
    
        >>> data = [('red', 1), ('blue', 1), ('red', 2), ('blue', 2)]
    >>> standard_way = sorted(data, key=itemgetter(0), reverse=True)
    >>> double_reversed = list(reversed(sorted(reversed(data), key=itemgetter(0))))
    >>> assert standard_way == double_reversed
    >>> standard_way
    [('red', 1), ('red', 2), ('blue', 1), ('blue', 2)]
    

  * 排序例程在两个对象之间进行比较时使用 `<`。 因此，通过定义一个 [`__lt__()`](../reference/datamodel.md#object.__lt__ "object.__lt__") 方法，就可以轻松地为类添加标准排序顺序:
    
        >>> Student.__lt__ = lambda self, other: self.age < other.age
    >>> sorted(student_objects)
    [('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
    

不过，请注意如果没有 [`__lt__()`](../reference/datamodel.md#object.__lt__ "object.__lt__")，则 `<` 可以退回到使用 [`__gt__()`](../reference/datamodel.md#object.__gt__ "object.__gt__") (参见 [`object.__lt__()`](../reference/datamodel.md#object.__lt__ "object.__lt__"))。

  * 键函数不需要直接依赖于被排序的对象。键函数还可以访问外部资源。例如，如果学生成绩存储在字典中，则可以使用它们对单独的学生姓名列表进行排序：
    
        >>> students = ['dave', 'john', 'jane']
    >>> newgrades = {'john': 'F', 'jane':'A', 'dave': 'C'}
    >>> sorted(students, key=newgrades.__getitem__)
    ['jane', 'dave', 'john']
    

