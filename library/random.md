# `random` \--- 生成伪随机数¶

**源码：** [Lib/random.py](https://github.com/python/cpython/tree/3.12/Lib/random.py)

* * *

该模块实现了各种分布的伪随机数生成器。

对于整数，从范围中有统一的选择。 对于序列，存在随机元素的统一选择、用于生成列表的随机排列的函数、以及用于随机抽样而无需替换的函数。

在实数轴上，有计算均匀、正态（高斯）、对数正态、负指数、伽马和贝塔分布的函数。 为了生成角度分布，可以使用 von Mises 分布。

几乎所有模块函数都依赖于基本函数 `random()`，它在左开右闭区间 `0.0 <= X < 1.0` 内均匀生成随机浮点数。 Python 使用 Mersenne Twister 作为核心生成器。 它产生 53 位精度的浮点数并且周期为 2**19937-1。 其在 C 中的这个底层实现既快速又线程安全。 Mersenne Twister 是目前经过最广泛测试的随机数生成器之一。 但是，因为是完全确定性的，它不适用于所有目的，并且完全不适用于加密目的。

这个模块提供的函数实际上是 `random.Random` 类的隐藏实例的绑定方法。 你可以实例化自己的 `Random` 类实例以获取不共享状态的生成器。

如果你想使用自己设计的不同基础生成器，类 `Random` 也可以作为子类：在这种情况下，重载 `random()` 、 `seed()` 、 `getstate()` 以及 `setstate()` 方法。可选地，新生成器可以提供 `getrandbits()` 方法——这允许 `randrange()` 在任意大的范围内产生选择。

`random` 模块还提供 `SystemRandom` 类，它使用系统函数 [`os.urandom()`](os.md#os.urandom "os.urandom") 从操作系统提供的源生成随机数。

警告

不应将此模块的伪随机生成器用于安全目的。 有关安全性或加密用途，请参阅 [`secrets`](secrets.md#module-secrets "secrets: Generate secure random numbers for managing secrets.") 模块。

参见

M. Matsumoto and T. Nishimura, "Mersenne Twister: A 623-dimensionally equidistributed uniform pseudorandom number generator", ACM Transactions on Modeling and Computer Simulation Vol. 8, No. 1, January pp.3--30 1998.

[Complementary-Multiply-with-Carry recipe](https://code.activestate.com/recipes/576707/) 用于兼容的替代随机数发生器，具有长周期和相对简单的更新操作。

## 簿记功能¶

random.seed( _a =None_, _version =2_)¶

    

~~~
初始化随机数生成器。

如果 _a_ 被省略或为 `None` ，则使用当前系统时间。 如果操作系统提供随机源，则使用它们而不是系统时间（有关可用性的详细信息，请参阅 [`os.urandom()`](os.md#os.urandom "os.urandom") 函数）。

如果 _a_ 是 int 类型，则直接使用。

对于版本2（默认的），[`str`](stdtypes.md#str "str") 、 [`bytes`](stdtypes.md#bytes "bytes") 或 [`bytearray`](stdtypes.md#bytearray "bytearray") 对象转换为 [`int`](functions.md#int "int") 并使用它的所有位。

对于版本1（用于从旧版本的Python再现随机序列），用于 [`str`](stdtypes.md#str "str") 和 [`bytes`](stdtypes.md#bytes "bytes") 的算法生成更窄的种子范围。

在 3.2 版本发生变更: 已移至版本2方案，该方案使用字符串种子中的所有位。

在 3.11 版本发生变更: _seed_ 必须是下列类型之一: _NoneType_ , [`int`](functions.md#int "int"), [`float`](functions.md#float "float"), [`str`](stdtypes.md#str "str"), [`bytes`](stdtypes.md#bytes "bytes") 或 [`bytearray`](stdtypes.md#bytearray "bytearray")。

random.getstate()¶
~~~
    

~~~
返回捕获生成器当前内部状态的对象。 这个对象可以传递给 `setstate()` 来恢复状态。

random.setstate( _state_ )¶
~~~
    

~~~
_state_ 应该是从之前调用 `getstate()` 获得的，并且 `setstate()` 将生成器的内部状态恢复到 `getstate()` 被调用时的状态。

## 用于字节数据的函数¶

random.randbytes( _n_ )¶
~~~
    

~~~
生成 _n_ 个随机字节。

此方法不可用于生成安全凭据。 那应当使用 [`secrets.token_bytes()`](secrets.md#secrets.token_bytes "secrets.token_bytes")。

在 3.9 版本加入.

## 整数用函数¶

random.randrange( _stop_ )¶

random.randrange( _start_ , _stop_ [, _step_ ])
~~~
    

~~~
返回从 `range(start, stop, step)` 随机选择的一个元素。

这大致等价于 `choice(range(start, stop, step))` 但是支持任意大的取值范围并针对常见场景进行了优化。

该位置参数的模式与 [`range()`](stdtypes.md#range "range") 函数相匹配。

关键字参数不应被使用因为它们可能以预料之外的方式被解读。 例如 `randrange(start=100)` 会被解读为 `randrange(0, 100, 1)`。

在 3.2 版本发生变更: `randrange()` 在生成均匀分布的值方面更为复杂。 以前它使用了像 `int(random()*n)` 这样的形式，它可以产生稍微不均匀的分布。

在 3.12 版本发生变更: 不再支持非整数类型的自动转换。 `randrange(10.0)` 和 `randrange(Fraction(10, 1))` 之类的调用现在将会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

random.randint( _a_ , _b_ )¶
~~~
    

~~~
返回随机整数 _N_ 满足 `a <= N <= b`。相当于 `randrange(a, b+1)`。

random.getrandbits( _k_ )¶
~~~
    

~~~
返回具有 _k_ 个随机比特位的非负 Python 整数。 此方法随 Mersenne Twister 生成器一起提供，其他一些生成器也可能将其作为 API 的可选部分提供。 在可能的情况下，`getrandbits()` 会启用 `randrange()` 来处理任意大的区间。

在 3.9 版本发生变更: 此方法现在接受零作为 _k_ 的值。

## 序列用函数¶

random.choice( _seq_ )¶
~~~
    

~~~
从非空序列 _seq_ 返回一个随机元素。 如果 _seq_ 为空，则引发 [`IndexError`](exceptions.md#IndexError "IndexError")。

random.choices( _population_ , _weights =None_, _*_ , _cum_weights =None_, _k =1_)¶
~~~
    

~~~
从 _population_ 中有重复地随机选取元素，返回大小为 _k_ 的元素列表。 如果 _population_ 为空，则引发 [`IndexError`](exceptions.md#IndexError "IndexError")。

如果指定了 _weight_ 序列，则根据相对权重进行选择。 或者，如果给出 _cum_weights_ 序列，则根据累积权重（可能使用 [`itertools.accumulate()`](itertools.md#itertools.accumulate "itertools.accumulate") 计算）进行选择。 例如，相对权重 `[10, 5, 30, 5]``相当于累积权重``[10, 15, 45, 50]`。 在内部，相对权重在进行选择之前会转换为累积权重，因此提供累积权重可以节省工作量。

如果既未指定 _weight_ 也未指定 _cum_weights_ ，则以相等的概率进行选择。 如果提供了权重序列，则它必须与 _population_ 序列的长度相同。 一个 [`TypeError`](exceptions.md#TypeError "TypeError") 指定了 _weights_ 和 _cum_weights_ 。

_weights_ 或 _cum_weights_ 可使用 `random()` 所返回的能与 [`float`](functions.md#float "float") 值进行相互运算的任何数字类型（包括整数、浮点数、分数但不包括 decimal）。 权重值应当非负且为有限的数值。 如果所有的权重值均为零则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

对于给定的种子，具有相等加权的 `choices()` 函数通常产生与重复调用 `choice()` 不同的序列。 `choices()` 使用的算法使用浮点运算来实现内部一致性和速度。 `choice()` 使用的算法默认为重复选择的整数运算，以避免因舍入误差引起的小偏差。

在 3.6 版本加入.

在 3.9 版本发生变更: 如果所有权重均为负值则将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

random.shuffle( _x_ )¶
~~~
    

~~~
就地将序列 _x_ 随机打乱位置。

要改变一个不可变的序列并返回一个新的打乱列表，请使用 `sample(x, k=len(x))`。

请注意，即使对于小的 `len(x)`， _x_ 的排列总数也可以快速增长，大于大多数随机数生成器的周期。 这意味着长序列的大多数排列永远不会产生。 例如，长度为2080的序列是可以在 Mersenne Twister 随机数生成器的周期内拟合的最大序列。

从 3.9 版起不建议使用，已在 3.11 版中移除: 可选形参 _random_ 。

random.sample( _population_ , _k_ , _*_ , _counts =None_)¶
~~~
    

~~~
返回从总体序列中选取的唯一元素的长度为 _k_ 的列表。 用于无重复的随机抽样。

返回包含来自总体的元素的新列表，同时保持原始总体不变。 结果列表按选择顺序排列，因此所有子切片也将是有效的随机样本。 这允许抽奖获奖者（样本）被划分为大奖和第二名获胜者（子切片）。

总体成员不必是 [hashable](../glossary.md#term-hashable) 或 unique 。 如果总体包含重复，则每次出现都是样本中可能的选择。

重复的元素可以一个个地直接列出，或使用可选的仅限关键字形参 _counts_ 来指定。 例如，`sample(['red', 'blue'], counts=[4, 2], k=5)` 等价于 `sample(['red', 'red', 'red', 'red', 'blue', 'blue'], k=5)`。

要从一系列整数中选择样本，请使用 [`range()`](stdtypes.md#range "range") 对象作为参数。 对于从大量人群中采样，这种方法特别快速且节省空间：`sample(range(10000000), k=60)` 。

如果样本大小大于总体大小，则引发 [`ValueError`](exceptions.md#ValueError "ValueError") 。

在 3.9 版本发生变更: 增加了 _counts_ 形参。

在 3.11 版本发生变更: _population_ 必须为一个序列。 不再支持将集合自动转换为列表。

## 离散分布¶

以下函数会生成离散分布。

random.binomialvariate( _n =1_, _p =0.5_)¶
~~~
    

~~~
[二项式分布](https://mathworld.wolfram.com/BinomialDistribution.md)。 返回 _n_ 次独立试验在每次试验的成功率为 _p_ 时的成功次数:

在数学上等价于:
~~~
    
    
~~~
sum(random() < p for i in range(n))
~~~

试验次数 _n_ 应为一个非负整数。 成功几率 _p_ 的取值范围应为 `0.0 <= p <= 1.0`。 结果是一个 `0 <= X <= n` 范围内的整数。

在 3.12 版本加入.

## 实值分布¶

以下函数生成特定的实值分布。如常用数学实践中所使用的那样，函数形参以分布方程中的相应变量命名，大多数这些方程都可以在任何统计学教材中找到。

random.random()¶

    

~~~
返回 `0.0 <= X < 1.0` 范围内的下一个随机浮点数。

random.uniform( _a_ , _b_ )¶
~~~
    

~~~
返回一个随机浮点数 _N_ ，当 `a <= b` 时 `a <= N <= b` ，当 `b < a` 时 `b <= N <= a` 。

取决于等式 `a + (b-a) * random()` 中的浮点舍入，终点 `b` 可以包括或不包括在该范围内。

random.triangular( _low_ , _high_ , _mode_ )¶
~~~
    

~~~
返回一个随机浮点数 _N_ ，使得 `low <= N <= high` 并在这些边界之间使用指定的 _mode_ 。 _low_ 和 _high_ 边界默认为零和一。 _mode_ 参数默认为边界之间的中点，给出对称分布。

random.betavariate( _alpha_ , _beta_ )¶
~~~
    

~~~
Beta 分布。 参数的条件是 `alpha > 0` 和 `beta > 0`。 返回值的范围介于 0 和 1 之间。

random.expovariate( _lambd =1.0_)¶
~~~
    

~~~
指数分布。 _lambd_ 是 1.0 除以所需的平均值，它应该是非零的。 （该参数本应命名为 “lambda” ，但这是 Python 中的保留字。）如果 _lambd_ 为正，则返回值的范围为 0 到正无穷大；如果 _lambd_ 为负，则返回值从负无穷大到 0。

在 3.12 版本发生变更: 添加了 `lambd` 的默认值。

random.gammavariate( _alpha_ , _beta_ )¶
~~~
    

~~~
Gamma 分布。 ( _不是_ gamma 函数！) shape 和 scale 形参，即 _alpha_ 和 _beta_ ，必须为正值。 （调用规范有变动并且有些源码会将 'beta' 定义为逆向的 scale)。

概率分布函数是:
~~~
    
    
~~~
          x ** (alpha - 1) * math.exp(-x / beta)
pdf(x) =  --------------------------------------
            math.gamma(alpha) * beta ** alpha
~~~

random.gauss( _mu =0.0_, _sigma =1.0_)¶

    

~~~
正态分布，也称高斯分布。 _mu_ 为平均值，而 _sigma_ 为标准差。 此函数要稍快于下面所定义的 `normalvariate()` 函数。

多线程注意事项：当两个线程同时调用此方法时，它们有可能将获得相同的返回值。 这可以通过三种办法来避免。 1) 让每个线程使用不同的随机数生成器实例。 2) 在所有调用外面加锁。 3) 改用速度较慢但是线程安全的 `normalvariate()` 函数。

在 3.11 版本发生变更: 现在 _mu_ 和 _sigma_ 均有默认参数。

random.lognormvariate( _mu_ , _sigma_ )¶
~~~
    

~~~
对数正态分布。 如果你采用这个分布的自然对数，你将得到一个正态分布，平均值为 _mu_ 和标准差为 _sigma_ 。 _mu_ 可以是任何值， _sigma_ 必须大于零。

random.normalvariate( _mu =0.0_, _sigma =1.0_)¶
~~~
    

~~~
正态分布。 _mu_ 是平均值， _sigma_ 是标准差。

在 3.11 版本发生变更: 现在 _mu_ 和 _sigma_ 均有默认参数。

random.vonmisesvariate( _mu_ , _kappa_ )¶
~~~
    

~~~
冯·米塞斯分布。 _mu_ 是平均角度，以弧度表示，介于0和 2* _pi_ 之间， _kappa_ 是浓度参数，必须大于或等于零。 如果 _kappa_ 等于零，则该分布在 0 到 2* _pi_ 的范围内减小到均匀的随机角度。

random.paretovariate( _alpha_ )¶
~~~
    

~~~
帕累托分布。 _alpha_ 是形状参数。

random.weibullvariate( _alpha_ , _beta_ )¶
~~~
    

~~~
威布尔分布。 _alpha_ 是比例参数， _beta_ 是形状参数。

## 替代生成器¶

_class _random.Random([ _seed_ ])¶
~~~
    

~~~
该类实现了 `random` 模块所用的默认伪随机数生成器。

从 3.9 版起不建议使用，已在 3.11 版中移除: 之前 _seed_ 可以是任何可哈希对象。 现在它被限制为: `NoneType`, [`int`](functions.md#int "int"), [`float`](functions.md#float "float"), [`str`](stdtypes.md#str "str"), [`bytes`](stdtypes.md#bytes "bytes") 或 [`bytearray`](stdtypes.md#bytearray "bytearray")。

_class _random.SystemRandom([ _seed_ ])¶
~~~
    

~~~
使用 [`os.urandom()`](os.md#os.urandom "os.urandom") 函数的类，用从操作系统提供的源生成随机数。 这并非适用于所有系统。 也不依赖于软件状态，序列不可重现。 因此，`seed()` 方法没有效果而被忽略。 `getstate()` 和 `setstate()` 方法如果被调用则引发 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError")。

## 关于再现性的说明¶

有时能够重现伪随机数生成器给出的序列是很有用处的。 通过重用一个种子值，只要没有运行多线程，相同的序列就应当可在多次运行中重现。

大多数随机模块的算法和种子函数都会在 Python 版本中发生变化，但保证两个方面不会改变：

  * 如果添加了新的播种方法，则将提供向后兼容的播种机。

  * 当兼容的播种机被赋予相同的种子时，生成器的 `random()` 方法将继续产生相同的序列。

## 例子¶

基本示例:
~~~
    
    
~~~shell
>>> random()                             # Random float:  0.0 <= x < 1.0
0.37444887175646646

>>> uniform(2.5, 10.0)                   # Random float:  2.5 <= x <= 10.0
3.1800146073117523

>>> expovariate(1 / 5)                   # Interval between arrivals averaging 5 seconds
5.148957571865031

>>> randrange(10)                        # Integer from 0 to 9 inclusive
7

>>> randrange(0, 101, 2)                 # Even integer from 0 to 100 inclusive
26

>>> choice(['win', 'lose', 'draw'])      # Single random element from a sequence
'draw'

>>> deck = 'ace two three four'.split()
>>> shuffle(deck)                        # Shuffle a list
>>> deck
['four', 'two', 'ace', 'three']

>>> sample([10, 20, 30, 40, 50], k=4)    # Four samples without replacement
[40, 10, 50, 30]
~~~

模拟:

    
    
~~~shell
>>> # Six roulette wheel spins (weighted sampling with replacement)
>>> choices(['red', 'black', 'green'], [18, 18, 2], k=6)
['red', 'green', 'black', 'black', 'red', 'black']

>>> # Deal 20 cards without replacement from a deck
>>> # of 52 playing cards, and determine the proportion of cards
>>> # with a ten-value:  ten, jack, queen, or king.
>>> deal = sample(['tens', 'low cards'], counts=[16, 36], k=20)
>>> deal.count('tens') / 20
0.15

>>> # Estimate the probability of getting 5 or more heads from 7 spins
>>> # of a biased coin that settles on heads 60% of the time.
>>> sum(binomialvariate(n=7, p=0.6) >= 5 for i in range(10_000)) / 10_000
0.4169

>>> # Probability of the median of 5 samples being in middle two quartiles
>>> def trial():
...     return 2_500 <= sorted(choices(range(10_000), k=5))[2] < 7_500
...
>>> sum(trial() for i in range(10_000)) / 10_000
0.7958
~~~

[statistical bootstrapping](https://en.wikipedia.org/wiki/Bootstrapping_\(statistics\)) 的示例，使用重新采样和替换来估计一个样本的均值的置信区间:

    
    
~~~
# https://www.thoughtco.com/example-of-bootstrapping-3126155
from statistics import fmean as mean
from random import choices

data = [41, 50, 29, 37, 81, 30, 73, 63, 20, 35, 68, 22, 60, 31, 95]
means = sorted(mean(choices(data, k=len(data))) for i in range(100))
print(f'The sample mean of {mean(data):.1f} has a 90% confidence '
      f'interval from {means[5]:.1f} to {means[94]:.1f}')
~~~

使用 [重新采样排列测试](https://en.wikipedia.org/wiki/Resampling_\(statistics\)#Permutation_tests) 来确定统计学显著性或者使用 [p-值](https://en.wikipedia.org/wiki/P-value) 来观察药物与安慰剂的作用之间差异的示例:

    
    
~~~
# Example from "Statistics is Easy" by Dennis Shasha and Manda Wilson
from statistics import fmean as mean
from random import shuffle

drug = [54, 73, 53, 70, 73, 68, 52, 65, 65]
placebo = [54, 51, 58, 44, 55, 52, 42, 47, 58, 46]
observed_diff = mean(drug) - mean(placebo)

n = 10_000
count = 0
combined = drug + placebo
for i in range(n):
    shuffle(combined)
    new_diff = mean(combined[:len(drug)]) - mean(combined[len(drug):])
    count += (new_diff >= observed_diff)

print(f'{n} label reshufflings produced only {count} instances with a difference')
print(f'at least as extreme as the observed difference of {observed_diff:.1f}.')
print(f'The one-sided p-value of {count / n:.4f} leads us to reject the null')
print(f'hypothesis that there is no difference between the drug and the placebo.')
~~~

多服务器队列的到达时间和服务交付模拟:

    
    
~~~
from heapq import heapify, heapreplace
from random import expovariate, gauss
from statistics import mean, quantiles

average_arrival_interval = 5.6
average_service_time = 15.0
stdev_service_time = 3.5
num_servers = 3

waits = []
arrival_time = 0.0
servers = [0.0] * num_servers  # time when each server becomes available
heapify(servers)
for i in range(1_000_000):
    arrival_time += expovariate(1.0 / average_arrival_interval)
    next_server_available = servers[0]
    wait = max(0.0, next_server_available - arrival_time)
    waits.append(wait)
    service_duration = max(0.0, gauss(average_service_time, stdev_service_time))
    service_completed = arrival_time + wait + service_duration
    heapreplace(servers, service_completed)

print(f'Mean wait: {mean(waits):.1f}   Max wait: {max(waits):.1f}')
print('Quartiles:', [round(q, 1) for q in quantiles(waits)])
~~~

参见

[Statistics for Hackers](https://www.youtube.com/watch?v=Iq9DzN6mvYA) [Jake Vanderplas](https://us.pycon.org/2016/speaker/profile/295/) 撰写的视频教程，使用一些基本概念进行统计分析，包括模拟、抽样、洗牌和交叉验证。

[Peter Norvig](https://norvig.com/bio.md) 编写的市场模拟 [Economics Simulation](https://nbviewer.jupyter.org/url/norvig.com/ipython/Economics.ipynb) 显示了此模块所提供的许多工具和分布（gauss, uniform, sample, betavariate, choice, triangular 和 randrange）的高效使用。

由 [Peter Norvig](https://norvig.com/bio.md) 撰写的教程 [A Concrete Introduction to Probability (using Python)](https://nbviewer.jupyter.org/url/norvig.com/ipython/Probability.ipynb) 涵盖了概率论基础知识，如何编写模拟，以及如何使用 Python 进行数据分析。

## 例程¶

这些例程演示了如何有效地使用 [`itertools`](itertools.md#module-itertools "itertools: Functions creating iterators for efficient looping.") 模块中的组合迭代器进行随机选取:

    
    
~~~python
def random_product(*args, repeat=1):
    "Random selection from itertools.product(*args, **kwds)"
    pools = [tuple(pool) for pool in args] * repeat
    return tuple(map(random.choice, pools))

def random_permutation(iterable, r=None):
    "Random selection from itertools.permutations(iterable, r)"
    pool = tuple(iterable)
    r = len(pool) if r is None else r
    return tuple(random.sample(pool, r))

def random_combination(iterable, r):
    "Random selection from itertools.combinations(iterable, r)"
    pool = tuple(iterable)
    n = len(pool)
    indices = sorted(random.sample(range(n), r))
    return tuple(pool[i] for i in indices)

def random_combination_with_replacement(iterable, r):
    "Choose r elements with replacement.  Order the result to match the iterable."
    # Result will be in set(itertools.combinations_with_replacement(iterable, r)).
    pool = tuple(iterable)
    n = len(pool)
    indices = sorted(random.choices(range(n), k=r))
    return tuple(pool[i] for i in indices)
~~~

默认的 `random()` 返回在 _0.0 ≤ x < 1.0_ 范围内 2⁻⁵³ 的倍数。 所有这些数值间隔相等并能精确表示为 Python 浮点数。 但是在此间隔上有许多其他可表示浮点数是不可能的选择。 例如，`0.05954861408025609` 就不是 2⁻⁵³ 的整数倍。

以下规范程序采取了一种不同的方式。 在间隔上的所有浮点数都是可能的选择。 它们的尾数取值来自 _2⁵² ≤ 尾数 < 2⁵³_ 范围内整数的均匀分布。 指数取值则来自几何分布，其中小于 _-53_ 的指数的出现频率为下一个较大指数的一半。

    
    
~~~
from random import Random
from math import ldexp

class FullRandom(Random):

    def random(self):
        mantissa = 0x10_0000_0000_0000 | self.getrandbits(52)
        exponent = -53
        x = 0
        while not x:
            x = self.getrandbits(32)
            exponent += x.bit_length() - 32
        return ldexp(mantissa, exponent)
~~~

该类中所有的 实值分布 都将使用新的方法:

    
    
~~~shell
>>> fr = FullRandom()
>>> fr.random()
0.05954861408025609
>>> fr.expovariate(0.25)
8.87925541791544
~~~

该规范程序在概念上等效于在 _0.0 ≤ x < 1.0_ 范围内对所有 2⁻¹⁰⁷⁴ 的倍数进行选择的算法。 所有这样的数字间隔都相等，但大多必须向下舍入为最接近的 Python 浮点数表示形式。 （2⁻¹⁰⁷⁴ 这个数值是等于 `math.ulp(0.0)` 的未经正规化的最小正浮点数。）

参见

[生成伪随机浮点数值](https://allendowney.com/research/rand/downey07randfloat.pdf) 为 Allen B. Downey 所撰写的描述如何生成相比通过 `random()` 正常生成的数值更细粒度浮点数的论文。

