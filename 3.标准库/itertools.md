# `itertools` \--- 为高效循环而创建迭代器的函数¶

* * *

本模块实现一系列 [iterator](../glossary.md#term-iterator) ，这些迭代器受到APL，Haskell和SML的启发。为了适用于Python，它们都被重新写过。

本模块标准化了一个快速、高效利用内存的核心工具集，这些工具本身或组合都很有用。它们一起形成了“迭代器代数”，这使得在纯Python中有可能创建简洁又高效的专用工具。

例如，SML有一个制表工具： `tabulate(f)`，它可产生一个序列 `f(0), f(1), ...`。在Python中可以组合 [`map()`](functions.md#map "map") 和 `count()` 实现： `map(f, count())`。

这些工具及其内置对应物也能很好地配合 [`operator`](operator.md#module-operator "operator: Functions corresponding to the standard operators.") 模块中的快速函数来使用。 例如，乘法运算符可以被映射到两个向量之间执行高效的点积: `sum(starmap(operator.mul, zip(vec1, vec2, strict=True)))`。

**无穷迭代器：**

迭代器

|

实参

|

结果

|

示例  
  
---|---|---|---  
  
`count()`

|

start, [step]

|

start, start+step, start+2*step, ...

|

`count(10) --> 10 11 12 13 14 ...`  
  
`cycle()`

|

p

|

p0, p1, ... plast, p0, p1, ...

|

`cycle('ABCD') --> A B C D A B C D ...`  
  
`repeat()`

|

elem [,n]

|

elem, elem, elem, ... 重复无限次或n次

|

`repeat(10, 3) --> 10 10 10`  
  
**根据最短输入序列长度停止的迭代器：**

迭代器

|

实参

|

结果

|

示例  
  
---|---|---|---  
  
`accumulate()`

|

p [,func]

|

p0, p0+p1, p0+p1+p2, ...

|

`accumulate([1,2,3,4,5]) --> 1 3 6 10 15`  
  
`batched()`

|

p, n

|

(p0, p1, ..., p_n-1), ...

|

`batched('ABCDEFG', n=3) --> ABC DEF G`  
  
`chain()`

|

p, q, ...

|

p0, p1, ... plast, q0, q1, ...

|

`chain('ABC', 'DEF') --> A B C D E F`  
  
`chain.from_iterable()`

|

iterable -- 可迭代对象

|

p0, p1, ... plast, q0, q1, ...

|

`chain.from_iterable(['ABC', 'DEF']) --> A B C D E F`  
  
`compress()`

|

data, selectors

|

(d[0] if s[0]), (d[1] if s[1]), ...

|

`compress('ABCDEF', [1,0,1,0,1,1]) --> A C E F`  
  
`dropwhile()`

|

pred, seq

|

seq[n], seq[n+1], ... 从pred首次真值测试失败开始

|

`dropwhile(lambda x: x<5, [1,4,6,4,1]) --> 6 4 1`  
  
`filterfalse()`

|

pred, seq

|

seq中pred(x)为假值的元素，x是seq中的元素。

|

`filterfalse(lambda x: x%2, range(10)) --> 0 2 4 6 8`  
  
`groupby()`

|

iterable[, key]

|

根据key(v)值分组的迭代器

|  
  
`islice()`

|

seq, [start,] stop [, step]

|

seq[start:stop:step]中的元素

|

`islice('ABCDEFG', 2, None) --> C D E F G`  
  
`pairwise()`

|

iterable -- 可迭代对象

|

(p[0], p[1]), (p[1], p[2])

|

`pairwise('ABCDEFG') --> AB BC CD DE EF FG`  
  
`starmap()`

|

func, seq

|

func(*seq[0]), func(*seq[1]), ...

|

`starmap(pow, [(2,5), (3,2), (10,3)]) --> 32 9 1000`  
  
`takewhile()`

|

pred, seq

|

seq[0], seq[1], ..., 直到pred真值测试失败

|

`takewhile(lambda x: x<5, [1,4,6,4,1]) --> 1 4`  
  
`tee()`

|

it, n

|

it1, it2, ... itn 将一个迭代器拆分为n个迭代器

|  
  
`zip_longest()`

|

p, q, ...

|

(p[0], q[0]), (p[1], q[1]), ...

|

`zip_longest('ABCD', 'xy', fillvalue='-') --> Ax By C- D-`  
  
**排列组合迭代器：**

迭代器

|

实参

|

结果  
  
---|---|---  
  
`product()`

|

p, q, ... [repeat=1]

|

笛卡尔积，相当于嵌套的for循环  
  
`permutations()`

|

p[, r]

|

长度r元组，所有可能的排列，无重复元素  
  
`combinations()`

|

p, r

|

长度r元组，有序，无重复元素  
  
`combinations_with_replacement()`

|

p, r

|

长度r元组，有序，元素可重复  
  
例子

|

结果  
  
---|---  
  
`product('ABCD', repeat=2)`

|

`AA AB AC AD BA BB BC BD CA CB CC CD DA DB DC DD`  
  
`permutations('ABCD', 2)`

|

`AB AC AD BA BC BD CA CB CD DA DB DC`  
  
`combinations('ABCD', 2)`

|

`AB AC AD BC BD CD`  
  
`combinations_with_replacement('ABCD', 2)`

|

`AA AB AC AD BB BC BD CC CD DD`  
  
## Itertool函数¶

下列模块函数均创建并返回迭代器。有些迭代器不限制输出流长度，所以它们只应在能截断输出流的函数或循环中使用。

itertools.accumulate( _iterable_ [, _func_ , _*_ , _initial=None_ ])¶

    

~~~
创建一个迭代器，返回累积汇总值或其他双目运算函数的累积结果值（通过可选的 _func_ 参数指定）。

如果提供了 _func_ ，它应当为带有两个参数的函数。 输入 _iterable_ 的元素可以是能被 _func_ 接受为参数的任意类型。 （例如，对于默认的加法运算，元素可以是任何可相加的类型包括 [`Decimal`](decimal.md#decimal.Decimal "decimal.Decimal") 或 [`Fraction`](fractions.md#fractions.Fraction "fractions.Fraction")。）

通常，输出的元素数量与输入的可迭代对象是一致的。 但是，如果提供了关键字参数 _initial_ ，则累加会以 _initial_ 值开始，这样输出就比输入的可迭代对象多一个元素。

大致相当于：
~~~
    
    
~~~python
def accumulate(iterable, func=operator.add, *, initial=None):
    'Return running totals'
    # accumulate([1,2,3,4,5]) --> 1 3 6 10 15
    # accumulate([1,2,3,4,5], initial=100) --> 100 101 103 106 110 115
    # accumulate([1,2,3,4,5], operator.mul) --> 1 2 6 24 120
    it = iter(iterable)
    total = initial
    if initial is None:
        try:
            total = next(it)
        except StopIteration:
            return
    yield total
    for element in it:
        total = func(total, element)
        yield total
~~~

_func_ 参数有多种用法。 它可设为 [`min()`](functions.md#min "min") 表示运行中的最小值，[`max()`](functions.md#max "max") 表示运行中的最大值，或者 [`operator.mul()`](operator.md#operator.mul "operator.mul") 表示运行中的积。 可以通过累积利息并应用付款额来构建摊销表:

    
    
~~~shell
>>> data = [3, 4, 6, 2, 1, 9, 0, 7, 5, 8]
>>> list(accumulate(data, operator.mul))     # running product
[3, 12, 72, 144, 144, 1296, 0, 0, 0, 0]
>>> list(accumulate(data, max))              # running maximum
[3, 4, 6, 6, 6, 9, 9, 9, 9, 9]

# Amortize a 5% loan of 1000 with 10 annual payments of 90
>>> account_update = lambda bal, pmt: round(bal * 1.05) + pmt
>>> list(accumulate(repeat(-90, 10), account_update, initial=1_000))
[1000, 960, 918, 874, 828, 779, 728, 674, 618, 559, 497]
~~~

参考一个类似函数 [`functools.reduce()`](functools.md#functools.reduce "functools.reduce") ，它只返回一个最终累积值。

在 3.2 版本加入.

在 3.3 版本发生变更: 增加可选参数 _func_ 。

在 3.8 版本发生变更: 添加了可选的 _initial_ 形参。

itertools.batched( _iterable_ , _n_ )¶

    

~~~
来自 _iterable_ 的长度为 _n_ 元组形式的批次数据。 最后一个批次可能短于 _n_ 。

循环处理输入可迭代对象并将数据积累为长度至多为 _n_ 的元组。 输入将被惰性地消耗，能填满一个批次即可。 结果将在批次填满或输入可迭代对象被耗尽时产生:
~~~
    
    
~~~shell
>>> flattened_data = ['roses', 'red', 'violets', 'blue', 'sugar', 'sweet']
>>> unflattened = list(batched(flattened_data, 2))
>>> unflattened
[('roses', 'red'), ('violets', 'blue'), ('sugar', 'sweet')]

>>> for batch in batched('ABCDEFG', 3):
...     print(batch)
...
('A', 'B', 'C')
('D', 'E', 'F')
('G',)
~~~

大致相当于：

    
    
~~~python
def batched(iterable, n):
    # batched('ABCDEFG', 3) --> ABC DEF G
    if n < 1:
        raise ValueError('n must be at least one')
    it = iter(iterable)
    while batch := tuple(islice(it, n)):
        yield batch
~~~

在 3.12 版本加入.

itertools.chain( _* iterables_)¶

    

~~~
创建一个迭代器，它首先返回第一个可迭代对象中所有元素，接着返回下一个可迭代对象中所有元素，直到耗尽所有可迭代对象中的元素。可将多个序列处理为单个序列。大致相当于：
~~~
    
    
~~~python
def chain(*iterables):
    # chain('ABC', 'DEF') --> A B C D E F
    for it in iterables:
        for element in it:
            yield element
~~~

_classmethod _chain.from_iterable( _iterable_ )¶

    

~~~
构建类似 `chain()` 迭代器的另一个选择。从一个单独的可迭代参数中得到链式输入，该参数是延迟计算的。大致相当于：
~~~
    
    
~~~python
def from_iterable(iterables):
    # chain.from_iterable(['ABC', 'DEF']) --> A B C D E F
    for it in iterables:
        for element in it:
            yield element
~~~

itertools.combinations( _iterable_ , _r_ )¶

    

~~~
返回由输入 _iterable_ 中元素组成长度为 _r_ 的子序列。

组合元组是根据输入的 _iterable_ 顺序以词典排序的方式发出的。 因此，如果输入的 _iterable_ 是已排序的，则输出的元组将按排序后的顺序产生。

元素的唯一性是基于它们的位置，而不是它们的值。 因此如果输入的元素都是唯一的，则在每个组合中将不会有重复的值。

大致相当于：
~~~
    
    
~~~python
def combinations(iterable, r):
    # combinations('ABCD', 2) --> AB AC AD BC BD CD
    # combinations(range(4), 3) --> 012 013 023 123
    pool = tuple(iterable)
    n = len(pool)
    if r > n:
        return
    indices = list(range(r))
    yield tuple(pool[i] for i in indices)
    while True:
        for i in reversed(range(r)):
            if indices[i] != i + n - r:
                break
        else:
            return
        indices[i] += 1
        for j in range(i+1, r):
            indices[j] = indices[j-1] + 1
        yield tuple(pool[i] for i in indices)
~~~

`combinations()` 的代码可被改写为 `permutations()` 过滤后的子序列，（相对于元素在输入中的位置）元素不是有序的。

    
    
~~~python
def combinations(iterable, r):
    pool = tuple(iterable)
    n = len(pool)
    for indices in permutations(range(n), r):
        if sorted(indices) == list(indices):
            yield tuple(pool[i] for i in indices)
~~~

当 `0 <= r <= n` 时，返回项的个数是 `n! / r! / (n-r)!`；当 `r > n` 时，返回项个数为0。

itertools.combinations_with_replacement( _iterable_ , _r_ )¶

    

~~~
返回由输入 _iterable_ 中元素组成的长度为 _r_ 的子序列，允许每个元素可重复出现。

组合元组是根据输入的 _iterable_ 顺序以词典排序的方式发出的。 因此，如果输入的 _iterable_ 是已排序的，则输出的元组将按排序后的顺序产生。

不同位置的元素是不同的，即使它们的值相同。因此如果输入中的元素都是不同的话，返回的组合中元素也都会不同。

大致相当于：
~~~
    
    
~~~python
def combinations_with_replacement(iterable, r):
    # combinations_with_replacement('ABC', 2) --> AA AB AC BB BC CC
    pool = tuple(iterable)
    n = len(pool)
    if not n and r:
        return
    indices = [0] * r
    yield tuple(pool[i] for i in indices)
    while True:
        for i in reversed(range(r)):
            if indices[i] != n - 1:
                break
        else:
            return
        indices[i:] = [indices[i] + 1] * (r - i)
        yield tuple(pool[i] for i in indices)
~~~

`combinations_with_replacement()` 的代码可被改写为 `production()` 过滤后的子序列，（相对于元素在输入中的位置）元素不是有序的。

    
    
~~~python
def combinations_with_replacement(iterable, r):
    pool = tuple(iterable)
    n = len(pool)
    for indices in product(range(n), repeat=r):
        if sorted(indices) == list(indices):
            yield tuple(pool[i] for i in indices)
~~~

当 `n > 0` 时，返回项个数为 `(n+r-1)! / r! / (n-1)!`.

在 3.1 版本加入.

itertools.compress( _data_ , _selectors_ )¶

    

~~~
创建一个迭代器，它返回 _data_ 中经 _selectors_ 真值测试为 `True` 的元素。迭代器在两者较短的长度处停止。大致相当于：
~~~
    
    
~~~python
def compress(data, selectors):
    # compress('ABCDEF', [1,0,1,0,1,1]) --> A C E F
    return (d for d, s in zip(data, selectors) if s)
~~~

在 3.1 版本加入.

itertools.count( _start =0_, _step =1_)¶

    

~~~
创建一个迭代器，它从 _start_ 值开始，返回均匀间隔的值。常用于 [`map()`](functions.md#map "map") 中的实参来生成连续的数据点。此外，还用于 [`zip()`](functions.md#zip "zip") 来添加序列号。大致相当于：
~~~
    
    
~~~python
def count(start=0, step=1):
    # count(10) --> 10 11 12 13 14 ...
    # count(2.5, 0.5) --> 2.5 3.0 3.5 ...
    n = start
    while True:
        yield n
        n += step
~~~

当对浮点数计数时，替换为乘法代码有时精度会更好，例如： `(start + step * i for i in count())` 。

在 3.1 版本发生变更: 增加参数 _step_ ，允许非整型。

itertools.cycle( _iterable_ )¶

    

~~~
创建一个迭代器，返回 _iterable_ 中所有元素并保存一个副本。当取完 _iterable_ 中所有元素，返回副本中的所有元素。无限重复。大致相当于：
~~~
    
    
~~~python
def cycle(iterable):
    # cycle('ABCD') --> A B C D A B C D A B C D ...
    saved = []
    for element in iterable:
        yield element
        saved.append(element)
    while saved:
        for element in saved:
              yield element
~~~

注意，该函数可能需要相当大的辅助空间（取决于 _iterable_ 的长度）。

itertools.dropwhile( _predicate_ , _iterable_ )¶

    

~~~
创建一个迭代器，如果 _predicate_ 为true，迭代器丢弃这些元素，然后返回其他元素。注意，迭代器在 _predicate_ 首次为false之前不会产生任何输出，所以可能需要一定长度的启动时间。大致相当于：
~~~
    
    
~~~python
def dropwhile(predicate, iterable):
    # dropwhile(lambda x: x<5, [1,4,6,4,1]) --> 6 4 1
    iterable = iter(iterable)
    for x in iterable:
        if not predicate(x):
            yield x
            break
    for x in iterable:
        yield x
~~~

itertools.filterfalse( _predicate_ , _iterable_ )¶

    

~~~
创建一个过滤来自 iterable 中元素从而只返回其中 predicate 为假值的元素的迭代器。 如果 _predicate_ 为 `None`，则返回为假值的项。 大致等价于:
~~~
    
    
~~~python
def filterfalse(predicate, iterable):
    # filterfalse(lambda x: x%2, range(10)) --> 0 2 4 6 8
    if predicate is None:
        predicate = bool
    for x in iterable:
        if not predicate(x):
            yield x
~~~

itertools.groupby( _iterable_ , _key =None_)¶

    

~~~
创建一个迭代器，返回 _iterable_ 中连续的键和组。 _key_ 是一个计算元素键值函数。如果未指定或为 `None`， _key_ 缺省为恒等函数（identity function），返回元素不变。一般来说， _iterable_ 需用同一个键值函数预先排序。

`groupby()` 操作类似于Unix中的 `uniq`。当每次 _key_ 函数产生的键值改变时，迭代器会分组或生成一个新组（这就是为什么通常需要使用同一个键值函数先对数据进行排序）。这种行为与SQL的GROUP BY操作不同，SQL的操作会忽略输入的顺序将相同键值的元素分在同组中。

返回的组本身也是一个迭代器，它与 `groupby()` 共享底层的可迭代对象。因为源是共享的，当 `groupby()` 对象向后迭代时，前一个组将消失。因此如果稍后还需要返回结果，可保存为列表：
~~~
    
    
~~~
groups = []
uniquekeys = []
data = sorted(data, key=keyfunc)
for k, g in groupby(data, keyfunc):
    groups.append(list(g))      # Store group iterator as a list
    uniquekeys.append(k)
~~~

`groupby()` 大致相当于：

    
    
~~~
class groupby:
    # [k for k, g in groupby('AAAABBBCCDAABBB')] --> A B C D A B
    # [list(g) for k, g in groupby('AAAABBBCCD')] --> AAAA BBB CC D

    def __init__(self, iterable, key=None):
        if key is None:
            key = lambda x: x
        self.keyfunc = key
        self.it = iter(iterable)
        self.tgtkey = self.currkey = self.currvalue = object()

    def __iter__(self):
        return self

    def __next__(self):
        self.id = object()
        while self.currkey == self.tgtkey:
            self.currvalue = next(self.it)    # Exit on StopIteration
            self.currkey = self.keyfunc(self.currvalue)
        self.tgtkey = self.currkey
        return (self.currkey, self._grouper(self.tgtkey, self.id))

    def _grouper(self, tgtkey, id):
        while self.id is id and self.currkey == tgtkey:
            yield self.currvalue
            try:
                self.currvalue = next(self.it)
            except StopIteration:
                return
            self.currkey = self.keyfunc(self.currvalue)
~~~

itertools.islice( _iterable_ , _stop_ )¶

itertools.islice( _iterable_ , _start_ , _stop_ [, _step_ ])

    

~~~
制作一个从可迭代对象返回选定元素的迭代器。 如果 _start_ 为非零值，则会跳过可迭代对象中的部分元素直至到达 start 位置。 在此之后，将连续返回元素除非 _step_ 被设为大于 1 的值而会间隔跳过部分结果。 如果 _stop_ 为 `None`，则迭代将持续进行直至迭代器中的元素耗尽；在其他情况下，它将在指定的位置上停止。

如果 _start_ 为 `None`，迭代从0开始。如果 _step_ 为 `None` ，步长缺省为1。

与常规的切片不同，`islice()` 不支持 _start_ , _stop_ 或 _step_ 为负值。 可被用来从内部结构已被展平的数据中提取相关字段（例如，一个多行报告可以每三行列出一个名称字段）。

大致相当于：
~~~
    
    
~~~python
def islice(iterable, *args):
    # islice('ABCDEFG', 2) --> A B
    # islice('ABCDEFG', 2, 4) --> C D
    # islice('ABCDEFG', 2, None) --> C D E F G
    # islice('ABCDEFG', 0, None, 2) --> A C E G
    s = slice(*args)
    start, stop, step = s.start or 0, s.stop or sys.maxsize, s.step or 1
    it = iter(range(start, stop, step))
    try:
        nexti = next(it)
    except StopIteration:
        # Consume *iterable* up to the *start* position.
        for i, element in zip(range(start), iterable):
            pass
        return
    try:
        for i, element in enumerate(iterable):
            if i == nexti:
                yield element
                nexti = next(it)
    except StopIteration:
        # Consume to *stop*.
        for i, element in zip(range(i + 1, stop), iterable):
            pass
~~~

itertools.pairwise( _iterable_ )¶

    

~~~
返回从输入 _iterable_ 中获取的连续重叠对。

输出迭代器中 2 元组的数量将比输入的数量少一个。 如果输入可迭代对象中少于两个值则它将为空。

大致相当于：
~~~
    
    
~~~python
def pairwise(iterable):
    # pairwise('ABCDEFG') --> AB BC CD DE EF FG
    a, b = tee(iterable)
    next(b, None)
    return zip(a, b)
~~~

在 3.10 版本加入.

itertools.permutations( _iterable_ , _r =None_)¶

    

~~~
连续返回由 _iterable_ 元素生成长度为 _r_ 的排列。

如果 _r_ 未指定或为 `None` ， _r_ 默认设置为 _iterable_ 的长度，这种情况下，生成所有全长排列。

组合元组是根据输入的 _iterable_ 顺序以词典排序的形式发出的。 因此，如果输入的 _iterable_ 是已排序的，则输出的元组将按排序后的顺序产生。

元素是基于其位置，而非其值来认定唯一性的。 因此如果输入的元素都是唯一的，则在组合中就不会有重复的值。

大致相当于：
~~~
    
    
~~~python
def permutations(iterable, r=None):
    # permutations('ABCD', 2) --> AB AC AD BA BC BD CA CB CD DA DB DC
    # permutations(range(3)) --> 012 021 102 120 201 210
    pool = tuple(iterable)
    n = len(pool)
    r = n if r is None else r
    if r > n:
        return
    indices = list(range(n))
    cycles = list(range(n, n-r, -1))
    yield tuple(pool[i] for i in indices[:r])
    while n:
        for i in reversed(range(r)):
            cycles[i] -= 1
            if cycles[i] == 0:
                indices[i:] = indices[i+1:] + indices[i:i+1]
                cycles[i] = n - i
            else:
                j = cycles[i]
                indices[i], indices[-j] = indices[-j], indices[i]
                yield tuple(pool[i] for i in indices[:r])
                break
        else:
            return
~~~

`permutations()` 的代码也可被改写为 `product()` 的子序列，只要将含有重复元素（来自输入中同一位置的）的项排除。

    
    
~~~python
def permutations(iterable, r=None):
    pool = tuple(iterable)
    n = len(pool)
    r = n if r is None else r
    for indices in product(range(n), repeat=r):
        if len(set(indices)) == r:
            yield tuple(pool[i] for i in indices)
~~~

当 `0 <= r <= n` ，返回项个数为 `n! / (n-r)!` ；当 `r > n` ，返回项个数为0。

itertools.product( _* iterables_, _repeat =1_)¶

    

~~~
可迭代对象输入的笛卡儿积。

大致相当于生成器表达式中的嵌套循环。例如， `product(A, B)` 和 `((x,y) for x in A for y in B)` 返回结果一样。

嵌套循环像里程表那样循环变动，每次迭代时将最右侧的元素向后迭代。这种模式形成了一种字典序，因此如果输入的可迭代对象是已排序的，笛卡尔积元组依次序发出。

要计算可迭代对象自身的笛卡尔积，将可选参数 _repeat_ 设定为要重复的次数。例如，`product(A, repeat=4)` 和 `product(A, A, A, A)` 是一样的。

该函数大致相当于下面的代码，只不过实际实现方案不会在内存中创建中间结果。
~~~
    
    
~~~python
def product(*args, repeat=1):
    # product('ABCD', 'xy') --> Ax Ay Bx By Cx Cy Dx Dy
    # product(range(2), repeat=3) --> 000 001 010 011 100 101 110 111
    pools = [tuple(pool) for pool in args] * repeat
    result = [[]]
    for pool in pools:
        result = [x+[y] for x in result for y in pool]
    for prod in result:
        yield tuple(prod)
~~~

在 `product()` 运行之前，它会完全耗尽输入的可迭代对象，在内存中保留值的临时池以生成结果积。 相应地，它只适用于有限的输入。

itertools.repeat( _object_ [, _times_ ])¶

    

~~~
创建一个持续地返回 _object_ 的迭代器。 将会无限期地运行除非指定了 _times_ 参数。

大致相当于：
~~~
    
    
~~~python
def repeat(object, times=None):
    # repeat(10, 3) --> 10 10 10
    if times is None:
        while True:
            yield object
    else:
        for i in range(times):
            yield object
~~~

_repeat_ 的一个常见用途是向 _map_ 或 _zip_ 提供一个常量值的流:

    
    
~~~shell
>>> list(map(pow, range(10), repeat(2)))
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
~~~

itertools.starmap( _function_ , _iterable_ )¶

    

~~~
创建一个使用从 iterable 获取的参数来运算 function 的迭代器。 当参数形参已经从单个可迭代对象分组为多个元组时（当数据已被 "预 zip" 时）代替 [`map()`](functions.md#map "map") 使用。

[`map()`](functions.md#map "map") 和 `starmap()` 之间的区别类似于 `function(a,b)` 和 `function(*c)` 之间的差异。 大致相当于:
~~~
    
    
~~~python
def starmap(function, iterable):
    # starmap(pow, [(2,5), (3,2), (10,3)]) --> 32 9 1000
    for args in iterable:
        yield function(*args)
~~~

itertools.takewhile( _predicate_ , _iterable_ )¶

    

~~~
创建一个迭代器，只要 predicate 为真就从可迭代对象中返回元素。大致相当于:
~~~
    
    
~~~python
def takewhile(predicate, iterable):
    # takewhile(lambda x: x<5, [1,4,6,4,1]) --> 1 4
    for x in iterable:
        if predicate(x):
            yield x
        else:
            break
~~~

itertools.tee( _iterable_ , _n =2_)¶

    

~~~
从一个可迭代对象中返回 _n_ 个独立的迭代器。

下面的 Python 代码能帮助解释 _tee_ 做了什么（尽管实际的实现更复杂并且仅使用了一个底层的 FIFO 队列）:
~~~
    
    
~~~python
def tee(iterable, n=2):
    it = iter(iterable)
    deques = [collections.deque() for i in range(n)]
    def gen(mydeque):
        while True:
            if not mydeque:             # when the local deque is empty
                try:
                    newval = next(it)   # fetch a new value and
                except StopIteration:
                    return
                for d in deques:        # load it to all the deques
                    d.append(newval)
            yield mydeque.popleft()
    return tuple(gen(d) for d in deques)
~~~

一旦 `tee()` 已被创建，原有的 _iterable_ 就不应在任何其他地方使用；否则， _iterable_ 可能会被向下执行而不通知 tee 对象。

`tee` 迭代器不是线程安全的。 当同时使用由同一个 `tee()` 调用所返回的迭代器时可能引发 [`RuntimeError`](3.标准库/exceptions.md#RuntimeError "RuntimeError")，即使原本的 _iterable_ 是线程安全的。is threadsafe.

该迭代工具可能需要相当大的辅助存储空间（这取决于要保存多少临时数据）。通常，如果一个迭代器在另一个迭代器开始之前就要使用大部份或全部数据，使用 [`list()`](stdtypes.md#list "list") 会比 `tee()` 更快。

itertools.zip_longest( _* iterables_, _fillvalue =None_)¶

    

~~~
创建一个迭代器，从每个可迭代对象中收集元素。如果可迭代对象的长度未对齐，将根据 _fillvalue_ 填充缺失值。迭代持续到耗光最长的可迭代对象。大致相当于：
~~~
    
    
~~~python
def zip_longest(*args, fillvalue=None):
    # zip_longest('ABCD', 'xy', fillvalue='-') --> Ax By C- D-
    iterators = [iter(it) for it in args]
    num_active = len(iterators)
    if not num_active:
        return
    while True:
        values = []
        for i, it in enumerate(iterators):
            try:
                value = next(it)
            except StopIteration:
                num_active -= 1
                if not num_active:
                    return
                iterators[i] = repeat(fillvalue)
                value = fillvalue
            values.append(value)
        yield tuple(values)
~~~

如果其中一个可迭代对象有无限长度，`zip_longest()` 函数应封装在限制调用次数的场景中（例如 `islice()` 或 `takewhile()`）。除非指定， _fillvalue_ 默认为 `None` 。

## itertools 配方¶

本节将展示如何使用现有的 itertools 作为基础构件来创建扩展的工具集。

这些 itertools 例程的主要目的是教学。 各个例程显示了对单个工具的各种思维方式 — 例如，`chain.from_iterable` 被关联到展平的概念。 这些例程还给出了有关这些工具的组合方式的想法 — 例如，`compress()` 和 `range()` 应当如何一起工作。 这些例程还显示了 itertools 与 [`operator`](operator.md#module-operator "operator: Functions corresponding to the standard operators.") 和 [`collections`](collections.md#module-collections "collections: Container datatypes") 模块以及内置迭代工具如 `map()`, `filter()`, `reversed()` 和 `enumerate()` 相互配合的使用模式。

这些例程的次要目的是作为一个孵化器使用。 `accumulate()`, `compress()` 和 `pairwise()` 迭代工具最初就是作为例程引入的。 目前，`sliding_window()` 和 `iter_index()` 例程正在进行测试以看它们是否能其价值。recipes are being tested to see whether they prove their worth.

基本上所有这些西方和许许多多其他的配方都可以通过 Python Package Index 上的 [more-itertools 项目](https://pypi.org/project/more-itertools/) 来安装:

    
    
~~~
python -m pip install more-itertools
~~~

许多配方提供了与底层工具集相当的高性能。 通过每次只处理一个元素而不是将整个可迭代对象放入内存保持了更好的内存效率。 通过以有助于消除临时变量的函数式风格将这些工作链接在一起来保持代码量的精简。 通过倾向于使用 "矢量化" 构件取代会造成较大解释器开销的 for 循环和 [generator](../glossary.md#term-generator) 来保持高速度。

    
    
~~~
import collections
import functools
import math
import operator
import random

def take(n, iterable):
    "Return first n items of the iterable as a list"
    return list(islice(iterable, n))

def prepend(value, iterator):
    "Prepend a single value in front of an iterator"
    # prepend(1, [2, 3, 4]) --> 1 2 3 4
    return chain([value], iterator)

def tabulate(function, start=0):
    "Return function(0), function(1), ..."
    return map(function, count(start))

def repeatfunc(func, times=None, *args):
    """Repeat calls to func with specified arguments.

    Example:  repeatfunc(random.random)
    """
    if times is None:
        return starmap(func, repeat(args))
    return starmap(func, repeat(args, times))

def flatten(list_of_lists):
    "Flatten one level of nesting"
    return chain.from_iterable(list_of_lists)

def ncycles(iterable, n):
    "Returns the sequence elements n times"
    return chain.from_iterable(repeat(tuple(iterable), n))

def tail(n, iterable):
    "Return an iterator over the last n items"
    # tail(3, 'ABCDEFG') --> E F G
    return iter(collections.deque(iterable, maxlen=n))

def consume(iterator, n=None):
    "Advance the iterator n-steps ahead. If n is None, consume entirely."
    # Use functions that consume iterators at C speed.
    if n is None:
        # feed the entire iterator into a zero-length deque
        collections.deque(iterator, maxlen=0)
    else:
        # advance to the empty slice starting at position n
        next(islice(iterator, n, n), None)

def nth(iterable, n, default=None):
    "Returns the nth item or a default value"
    return next(islice(iterable, n, None), default)

def quantify(iterable, pred=bool):
    "Count how many times the predicate is True"
    return sum(map(pred, iterable))

def all_equal(iterable):
    "Returns True if all the elements are equal to each other"
    g = groupby(iterable)
    return next(g, True) and not next(g, False)

def first_true(iterable, default=False, pred=None):
    """Returns the first true value in the iterable.

    If no true value is found, returns *default*

    If *pred* is not None, returns the first item
    for which pred(item) is true.

    """
    # first_true([a,b,c], x) --> a or b or c or x
    # first_true([a,b], x, f) --> a if f(a) else b if f(b) else x
    return next(filter(pred, iterable), default)

def iter_index(iterable, value, start=0, stop=None):
    "Return indices where a value occurs in a sequence or iterable."
    # iter_index('AABCADEAF', 'A') --> 0 1 4 7
    seq_index = getattr(iterable, 'index', None)
    if seq_index is None:
        # Slow path for general iterables
        it = islice(iterable, start, stop)
        for i, element in enumerate(it, start):
            if element is value or element == value:
                yield i
    else:
        # Fast path for sequences
        stop = len(iterable) if stop is None else stop
        i = start - 1
        try:
            while True:
                yield (i := seq_index(value, i+1, stop))
        except ValueError:
            pass

def iter_except(func, exception, first=None):
    """ Call a function repeatedly until an exception is raised.

    Converts a call-until-exception interface to an iterator interface.
    Like builtins.iter(func, sentinel) but uses an exception instead
    of a sentinel to end the loop.

    Examples:
        iter_except(functools.partial(heappop, h), IndexError)   # priority queue iterator
        iter_except(d.popitem, KeyError)                         # non-blocking dict iterator
        iter_except(d.popleft, IndexError)                       # non-blocking deque iterator
        iter_except(q.get_nowait, Queue.Empty)                   # loop over a producer Queue
        iter_except(s.pop, KeyError)                             # non-blocking set iterator

    """
    try:
        if first is not None:
            yield first()            # For database APIs needing an initial cast to db.first()
        while True:
            yield func()
    except exception:
        pass

def grouper(iterable, n, *, incomplete='fill', fillvalue=None):
    "Collect data into non-overlapping fixed-length chunks or blocks"
    # grouper('ABCDEFG', 3, fillvalue='x') --> ABC DEF Gxx
    # grouper('ABCDEFG', 3, incomplete='strict') --> ABC DEF ValueError
    # grouper('ABCDEFG', 3, incomplete='ignore') --> ABC DEF
    args = [iter(iterable)] * n
    if incomplete == 'fill':
        return zip_longest(*args, fillvalue=fillvalue)
    if incomplete == 'strict':
        return zip(*args, strict=True)
    if incomplete == 'ignore':
        return zip(*args)
    else:
        raise ValueError('Expected fill, strict, or ignore')

def sliding_window(iterable, n):
    # sliding_window('ABCDEFG', 4) --> ABCD BCDE CDEF DEFG
    it = iter(iterable)
    window = collections.deque(islice(it, n-1), maxlen=n)
    for x in it:
        window.append(x)
        yield tuple(window)

def roundrobin(*iterables):
    "roundrobin('ABC', 'D', 'EF') --> A D E B F C"
    # Recipe credited to George Sakkis
    num_active = len(iterables)
    nexts = cycle(iter(it).__next__ for it in iterables)
    while num_active:
        try:
            for next in nexts:
                yield next()
        except StopIteration:
            # Remove the iterator we just exhausted from the cycle.
            num_active -= 1
            nexts = cycle(islice(nexts, num_active))

def partition(pred, iterable):
    """Partition entries into false entries and true entries.

    If *pred* is slow, consider wrapping it with functools.lru_cache().
    """
    # partition(is_odd, range(10)) --> 0 2 4 6 8   and  1 3 5 7 9
    t1, t2 = tee(iterable)
    return filterfalse(pred, t1), filter(pred, t2)

def subslices(seq):
    "Return all contiguous non-empty subslices of a sequence"
    # subslices('ABCD') --> A AB ABC ABCD B BC BCD C CD D
    slices = starmap(slice, combinations(range(len(seq) + 1), 2))
    return map(operator.getitem, repeat(seq), slices)

def before_and_after(predicate, it):
    """ Variant of takewhile() that allows complete
        access to the remainder of the iterator.

        >>> it = iter('ABCdEfGhI')
        >>> all_upper, remainder = before_and_after(str.isupper, it)
        >>> ''.join(all_upper)
        'ABC'
        >>> ''.join(remainder)     # takewhile() would lose the 'd'
        'dEfGhI'

        Note that the first iterator must be fully
        consumed before the second iterator can
        generate valid results.
    """
    it = iter(it)
    transition = []
    def true_iterator():
        for elem in it:
            if predicate(elem):
                yield elem
            else:
                transition.append(elem)
                return
    def remainder_iterator():
        yield from transition
        yield from it
    return true_iterator(), remainder_iterator()

def unique_everseen(iterable, key=None):
    "List unique elements, preserving order. Remember all elements ever seen."
    # unique_everseen('AAAABBBCCDAABBB') --> A B C D
    # unique_everseen('ABBcCAD', str.lower) --> A B c D
    seen = set()
    if key is None:
        for element in filterfalse(seen.__contains__, iterable):
            seen.add(element)
            yield element
        # For order preserving deduplication,
        # a faster but non-lazy solution is:
        #     yield from dict.fromkeys(iterable)
    else:
        for element in iterable:
            k = key(element)
            if k not in seen:
                seen.add(k)
                yield element
        # For use cases that allow the last matching element to be returned,
        # a faster but non-lazy solution is:
        #      t1, t2 = tee(iterable)
        #      yield from dict(zip(map(key, t1), t2)).values()

def unique_justseen(iterable, key=None):
    "List unique elements, preserving order. Remember only the element just seen."
    # unique_justseen('AAAABBBCCDAABBB') --> A B C D A B
    # unique_justseen('ABBcCAD', str.lower) --> A B c A D
    return map(next, map(operator.itemgetter(1), groupby(iterable, key)))
~~~

下面的例程具有更数学化的风格:

    
    
~~~python
def powerset(iterable):
    "powerset([1,2,3]) --> () (1,) (2,) (3,) (1,2) (1,3) (2,3) (1,2,3)"
    s = list(iterable)
    return chain.from_iterable(combinations(s, r) for r in range(len(s)+1))

def sieve(n):
    "Primes less than n."
    # sieve(30) --> 2 3 5 7 11 13 17 19 23 29
    if n > 2:
        yield 2
    start = 3
    data = bytearray((0, 1)) * (n // 2)
    limit = math.isqrt(n) + 1
    for p in iter_index(data, 1, start, limit):
        yield from iter_index(data, 1, start, p*p)
        data[p*p : n : p+p] = bytes(len(range(p*p, n, p+p)))
        start = p*p
    yield from iter_index(data, 1, start)

def factor(n):
    "Prime factors of n."
    # factor(99) --> 3 3 11
    # factor(1_000_000_000_000_007) --> 47 59 360620266859
    # factor(1_000_000_000_000_403) --> 1000000000000403
    for prime in sieve(math.isqrt(n) + 1):
        while not n % prime:
            yield prime
            n //= prime
            if n == 1:
                return
    if n > 1:
        yield n

def sum_of_squares(it):
    "Add up the squares of the input values."
    # sum_of_squares([10, 20, 30]) -> 1400
    return math.sumprod(*tee(it))

def transpose(it):
    "Swap the rows and columns of the input."
    # transpose([(1, 2, 3), (11, 22, 33)]) --> (1, 11) (2, 22) (3, 33)
    return zip(*it, strict=True)

def matmul(m1, m2):
    "Multiply two matrices."
    # matmul([(7, 5), (3, 5)], [(2, 5), (7, 9)]) --> (49, 80), (41, 60)
    n = len(m2[0])
    return batched(starmap(math.sumprod, product(m1, transpose(m2))), n)

def convolve(signal, kernel):
    """Linear convolution of two iterables.

    Article:  https://betterexplained.com/articles/intuitive-convolution/
    Video:    https://www.youtube.com/watch?v=KuXjwB4LzSA
    """
    # convolve(data, [0.25, 0.25, 0.25, 0.25]) --> Moving average (blur)
    # convolve(data, [1, -1]) --> 1st finite difference (1st derivative)
    # convolve(data, [1, -2, 1]) --> 2nd finite difference (2nd derivative)
    kernel = tuple(kernel)[::-1]
    n = len(kernel)
    padded_signal = chain(repeat(0, n-1), signal, repeat(0, n-1))
    windowed_signal = sliding_window(padded_signal, n)
    return map(math.sumprod, repeat(kernel), windowed_signal)

def polynomial_from_roots(roots):
    """Compute a polynomial's coefficients from its roots.

       (x - 5) (x + 4) (x - 3)  expands to:   x³ -4x² -17x + 60
    """
    # polynomial_from_roots([5, -4, 3]) --> [1, -4, -17, 60]
    factors = zip(repeat(1), map(operator.neg, roots))
    return list(functools.reduce(convolve, factors, [1]))

def polynomial_eval(coefficients, x):
    """Evaluate a polynomial at a specific value.

    Computes with better numeric stability than Horner's method.
    """
    # Evaluate x³ -4x² -17x + 60 at x = 2.5
    # polynomial_eval([1, -4, -17, 60], x=2.5) --> 8.125
    n = len(coefficients)
    if n == 0:
        return x * 0  # coerce zero to the type of x
    powers = map(pow, repeat(x), reversed(range(n)))
    return math.sumprod(coefficients, powers)

def polynomial_derivative(coefficients):
    """Compute the first derivative of a polynomial.

       f(x)  =  x³ -4x² -17x + 60
       f'(x) = 3x² -8x  -17
    """
    # polynomial_derivative([1, -4, -17, 60]) -> [3, -8, -17]
    n = len(coefficients)
    powers = reversed(range(1, n))
    return list(map(operator.mul, coefficients, powers))

def nth_combination(iterable, r, index):
    "Equivalent to list(combinations(iterable, r))[index]"
    pool = tuple(iterable)
    n = len(pool)
    c = math.comb(n, r)
    if index < 0:
        index += c
    if index < 0 or index >= c:
        raise IndexError
    result = []
    while r:
        c, n, r = c*r//n, n-1, r-1
        while index >= c:
            index -= c
            c, n = c*(n-r)//n, n-1
        result.append(pool[-1-n])
    return tuple(result)
~~~

