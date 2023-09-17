# `timeit` \--- 测量小代码片段的执行时间¶

**源码：** [Lib/timeit.py](https://github.com/python/cpython/tree/3.12/Lib/timeit.py)

* * *

此模块提供了一种简单的方法来计算一小段 Python 代码的耗时。 它有 命令行接口 以及一个 可调用 方法。 它避免了许多测量时间的常见陷阱。 另见 Tim Peter 在 O'Reilly 出版的 _Python Cookbook_ 第二版中“算法”章节的概述。

## 基本示例¶

以下示例显示了如何使用 命令行接口 来比较三个不同的表达式：

    
    
~~~
$ python -m timeit "'-'.join(str(n) for n in range(100))"
10000 loops, best of 5: 30.2 usec per loop
$ python -m timeit "'-'.join([str(n) for n in range(100)])"
10000 loops, best of 5: 27.5 usec per loop
$ python -m timeit "'-'.join(map(str, range(100)))"
10000 loops, best of 5: 23.2 usec per loop
~~~

这可以通过 Python 接口 实现

    
    
~~~shell
>>> import timeit
>>> timeit.timeit('"-".join(str(n) for n in range(100))', number=10000)
0.3018611848820001
>>> timeit.timeit('"-".join([str(n) for n in range(100)])', number=10000)
0.2727368790656328
>>> timeit.timeit('"-".join(map(str, range(100)))', number=10000)
0.23702679807320237
~~~

从 Python 接口 还可以传出一个可调用对象:

    
    
~~~shell
>>> timeit.timeit(lambda: "-".join(map(str, range(100))), number=10000)
0.19665591977536678
~~~

但请注意 `timeit()` 仅在使用命令行界面时会自动确定重复次数。 在 例子 一节你可以找到更多的进阶示例。

## Python 接口¶

该模块定义了三个便利函数和一个公共类：

timeit.timeit( _stmt='pass'_ , _setup='pass'_ , _timer= <default timer>_, _number=1000000_ , _globals=None_ )¶

    

~~~
使用给定语句、 _setup_ 代码和 _timer_ 函数创建一个 `Timer` 实例，并执行 _number_ 次其 `timeit()` 方法。可选的 _globals_ 参数指定用于执行代码的命名空间。

在 3.5 版本发生变更: 添加可选参数 _globals_ 。

timeit.repeat( _stmt='pass'_ , _setup='pass'_ , _timer= <default timer>_, _repeat=5_ , _number=1000000_ , _globals=None_ )¶
~~~
    

~~~
使用给定语句、 _setup_ 代码和 _timer_ 函数创建一个 `Timer` 实例，并使用给定的 _repeat_ 计数和 _number_ 执行运行其 `repeat()` 方法。可选的 _globals_ 参数指定用于执行代码的命名空间。

在 3.5 版本发生变更: 添加可选参数 _globals_ 。

在 3.7 版本发生变更: _repeat_ 的默认值由 3 更改为 5 。

timeit.default_timer()¶
~~~
    

~~~
默认计时器始终为 time.perf_counter()，它返回浮点形式的秒数。 另一个选择是 time.perf_counter_ns，它返回整数形式的纳秒数。

在 3.3 版本发生变更: [`time.perf_counter()`](time.md#time.perf_counter "time.perf_counter") 现在是默认计时器。

_class _timeit.Timer( _stmt='pass'_ , _setup='pass'_ , _timer= <timer function>_, _globals=None_ )¶
~~~
    

~~~
用于小代码片段的计数执行速度的类。

构造函数接受一个将计时的语句、一个用于设置的附加语句和一个定时器函数。两个语句都默认为 `'pass'` ；计时器函数与平台有关（请参阅模块文档字符串）。 _stmt_ 和 _setup_ 也可能包含多个以 `;` 或换行符分隔的语句，只要它们不包含多行字符串文字即可。该语句默认在 timeit 的命名空间内执行；可以通过将命名空间传递给 _globals_ 来控制此行为。

要测量第一个语句的执行时间，请使用 `timeit()` 方法。 `repeat()` 和 `autorange()` 方法是方便的方法来调用 `timeit()` 多次。

_setup_ 的执行时间从总体计时执行中排除。

_stmt_ 和 _setup_ 参数也可以使用不带参数的可调用对象。这将在一个计时器函数中嵌入对它们的调用，然后由 `timeit()` 执行。请注意，由于额外的函数调用，在这种情况下，计时开销会略大一些。

在 3.5 版本发生变更: 添加可选参数 _globals_ 。

timeit( _number =1000000_)¶
~~~
    

~~~
主语句执行次数 _number_ 。 这会执行一次设置语句，然后返回执行主语句若干次所需的时间。 默认计时器以浮点形式返回秒数。 参数是循环的次数，默认为一百万次。 主语句、设置语句和要使用的定时器函数都将被传递给构造器。

备注

默认情况下， `timeit()` 暂时关闭 [garbage collection](../glossary.md#term-garbage-collection) 。这种方法的优点在于它使独立时序更具可比性。缺点是GC可能是所测量功能性能的重要组成部分。如果是这样，可以在 _setup_ 字符串中的第一个语句重新启用GC。例如:
~~~
    
    
~~~
timeit.Timer('for i in range(10): oct(i)', 'gc.enable()').timeit()
~~~

autorange( _callback =None_)¶

    

~~~
自动决定调用多少次 `timeit()` 。

这是一个便利函数，它反复调用 `timeit()` ，以便总时间 >= 0.2 秒，返回最终（循环次数，循环所用的时间）。它调用 `timeit()` 的次数以序列 1, 2, 5, 10, 20, 50, ... 递增，直到所用的时间至少为0.2秒。

如果给出 _callback_ 并且不是 `None` ，则在每次试验后将使用两个参数调用它： `callback(number, time_taken)` 。

在 3.6 版本加入.

repeat( _repeat =5_, _number =1000000_)¶
~~~
    

~~~
调用 `timeit()` 几次。

这是一个方便的函数，它反复调用 `timeit()` ，返回结果列表。第一个参数指定调用 `timeit()` 的次数。第二个参数指定 `timeit()` 的 _number_ 参数。

备注

从结果向量计算并报告平均值和标准差这些是很诱人的。但是，这不是很有用。在典型情况下，最低值给出了机器运行给定代码段的速度的下限；结果向量中较高的值通常不是由Python的速度变化引起的，而是由于其他过程干扰你的计时准确性。所以结果的 [`min()`](functions.md#min "min") 可能是你应该感兴趣的唯一数字。之后，你应该看看整个向量并应用常识而不是统计。

在 3.7 版本发生变更: _repeat_ 的默认值由 3 更改为 5 。

print_exc( _file =None_)¶
~~~
    

~~~
帮助程序从计时代码中打印回溯。

典型使用:
~~~
    
    
~~~
t = Timer(...)       # outside the try/except
try:
    t.timeit(...)    # or t.repeat(...)
except Exception:
    t.print_exc()
~~~

与标准回溯相比，优势在于将显示已编译模板中的源行。可选的 _file_ 参数指向发送回溯的位置；它默认为 [`sys.stderr`](3.标准库/sys.md#sys.stderr "sys.stderr") 。

## 命令行接口¶

从命令行调用程序时，使用以下表单:

    
    
~~~
python -m timeit [-n N] [-r N] [-u U] [-s S] [-p] [-v] [-h] [statement ...]
~~~

如果了解以下选项：

-n N, \--number=N¶
    

执行 '语句' 多少次

-r N, \--repeat=N¶
    

重复计时器的次数（默认为5）

-s S, \--setup=S¶
    

最初要执行一次的语句（默认为 `pass` ）

-p, \--process¶
    

测量进程时间，而不是 wallclock 时间，使用 [`time.process_time()`](time.md#time.process_time "time.process_time") 而不是 [`time.perf_counter()`](time.md#time.perf_counter "time.perf_counter") ，这是默认值

在 3.3 版本加入.

-u, \--unit=U¶
    

为定时器输出指定一个时间单位；可以选择 `nsec`, `usec`, `msec` 或 `sec`

在 3.5 版本加入.

-v, \--verbose¶
    

打印原始计时结果；重复更多位数精度

-h, \--help¶
    

打印一条简短的使用信息并退出

可以通过将每一行指定为单独的语句参数来给出多行语句；通过在引号中包含参数并使用前导空格可以缩进行。多个 `-s` 选项的处理方式相似。

如果未给出 `-n`，则会通过尝试按序列 1, 2, 5, 10, 20, 50, ... 递增的数值来计算合适的循环次数，直到总计时间至少为 0.2 秒。

`default_timer()` 测量可能受到在同一台机器上运行的其他程序的影响，因此在需要精确计时时最好的做法是重复几次计时并使用最佳时间。 `-r` 选项对此有利；在大多数情况下，默认的 5 次重复可能就足够了。 你可以使用 [`time.process_time()`](time.md#time.process_time "time.process_time") 来测量CPU时间。

备注

执行 pass 语句会产生一定的基线开销。这里的代码不会试图隐藏它，但你应该知道它。可以通过不带参数调用程序来测量基线开销，并且Python版本之间可能会有所不同。

## 例子¶

可以提供一个在开头只执行一次的 setup 语句：

    
    
~~~
$ python -m timeit -s "text = 'sample string'; char = 'g'" "char in text"
5000000 loops, best of 5: 0.0877 usec per loop
$ python -m timeit -s "text = 'sample string'; char = 'g'" "text.find(char)"
1000000 loops, best of 5: 0.342 usec per loop
~~~

在输出信息中，共有三个字段。 首先是 loop count，它告诉你每个计时循环重复运行了多少次语句体。 然后是 repetition count ('best of 5')，它告诉你计时循环重复了多少次，最后是语句体在计时循环重复中最好的平均耗时。 即最快一次重复的耗时除以循环计数。

    
    
~~~shell
>>> import timeit
>>> timeit.timeit('char in text', setup='text = "sample string"; char = "g"')
0.41440500499993504
>>> timeit.timeit('text.find(char)', setup='text = "sample string"; char = "g"')
1.7246671520006203
~~~

使用 `Timer` 类及其方法可以完成同样的操作:

    
    
~~~shell
>>> import timeit
>>> t = timeit.Timer('char in text', setup='text = "sample string"; char = "g"')
>>> t.timeit()
0.3955516149999312
>>> t.repeat()
[0.40183617287970225, 0.37027556854118704, 0.38344867356679524, 0.3712595970846668, 0.37866875250654886]
~~~

以下示例显示如何计算包含多行的表达式。 在这里我们对比使用 [`hasattr()`](functions.md#hasattr "hasattr") 与 [`try`](8.%20复合语句.md#try)/[`except`](8.%20复合语句.md#except) 的开销来测试缺失与提供对象属性:

    
    
~~~
$ python -m timeit "try:" "  str.__bool__" "except AttributeError:" "  pass"
20000 loops, best of 5: 15.7 usec per loop
$ python -m timeit "if hasattr(str, '__bool__'): pass"
50000 loops, best of 5: 4.26 usec per loop

$ python -m timeit "try:" "  int.__bool__" "except AttributeError:" "  pass"
200000 loops, best of 5: 1.43 usec per loop
$ python -m timeit "if hasattr(int, '__bool__'): pass"
100000 loops, best of 5: 2.23 usec per loop
~~~
    
    
~~~shell
>>> import timeit
>>> # attribute is missing
>>> s = """\
... try:
...     str.__bool__
... except AttributeError:
...     pass
... """
>>> timeit.timeit(stmt=s, number=100000)
0.9138244460009446
>>> s = "if hasattr(str, '__bool__'): pass"
>>> timeit.timeit(stmt=s, number=100000)
0.5829014980008651
>>>
>>> # attribute is present
>>> s = """\
... try:
...     int.__bool__
... except AttributeError:
...     pass
... """
>>> timeit.timeit(stmt=s, number=100000)
0.04215312199994514
>>> s = "if hasattr(int, '__bool__'): pass"
>>> timeit.timeit(stmt=s, number=100000)
0.08588060699912603
~~~

要让 `timeit` 模块访问你定义的函数，你可以传递一个包含 import 语句的 _setup_ 参数:

    
    
~~~python
def test():
    """Stupid test function"""
    L = [i for i in range(100)]

if __name__ == '__main__':
    import timeit
    print(timeit.timeit("test()", setup="from __main__ import test"))
~~~

另一种选择是将 [`globals()`](functions.md#globals "globals") 传递给 _globals_ 参数，这将导致代码在当前的全局命名空间中执行。这比单独指定 import 更方便

    
    
~~~python
def f(x):
    return x**2
def g(x):
    return x**4
def h(x):
    return x**8

import timeit
print(timeit.timeit('[func(42) for func in (f,g,h)]', globals=globals()))
~~~

