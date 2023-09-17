# Python 性能分析器¶

**源代码：** [Lib/profile.py](https://github.com/python/cpython/tree/3.12/Lib/profile.py) 和 [Lib/pstats.py](https://github.com/python/cpython/tree/3.12/Lib/pstats.py)

* * *

## 性能分析器简介¶

`cProfile` 和 `profile` 提供了 Python 程序的 _确定性性能分析_ 。 _profile_ 是一组统计数据，描述程序的各个部分执行的频率和时间。这些统计数据可以通过 `pstats` 模块格式化为报表。

Python 标准库提供了同一分析接口的两种不同实现：

  1. 对于大多数用户，建议使用 `cProfile` ；这是一个 C 扩展插件，因为其合理的运行开销，所以适合于分析长时间运行的程序。该插件基于 `lsprof` ，由 Brett Rosen 和 Ted Chaotter 贡献。

  2. `profile` 是一个纯 Python 模块（`cProfile` 就是模拟其接口的 C 语言实现），但它会显著增加配置程序的开销。如果你正在尝试以某种方式扩展分析器，则使用此模块可能会更容易完成任务。该模块最初由 Jim Roskind 设计和编写。

备注

profiler 分析器模块被设计为给指定的程序提供执行概要文件，而不是用于基准测试目的（ [`timeit`](timeit.md#module-timeit "timeit: Measure the execution time of small code snippets.") 才是用于此目标的，它能获得合理准确的结果）。这特别适用于将 Python 代码与 C 代码进行基准测试：分析器为Python 代码引入开销，但不会为 C级别的函数引入开销，因此 C 代码似乎比任何Python 代码都更快。

## 实时用户手册¶

本节是为 “不想阅读手册” 的用户提供的。它提供了非常简短的概述，并允许用户快速对现有应用程序执行评测。

要分析采用单个参数的函数，可以执行以下操作：

    
    
~~~
import cProfile
import re
cProfile.run('re.compile("foo|bar")')
~~~

（如果 `cProfile` 在您的系统上不可用，请使用 `profile` 。）

上述操作将运行 [`re.compile()`](re.md#re.compile "re.compile") 并打印分析结果，如下所示：

    
    
~~~
      214 function calls (207 primitive calls) in 0.002 seconds

Ordered by: cumulative time

ncalls  tottime  percall  cumtime  percall filename:lineno(function)
     1    0.000    0.000    0.002    0.002 {built-in method builtins.exec}
     1    0.000    0.000    0.001    0.001 <string>:1(<module>)
     1    0.000    0.000    0.001    0.001 __init__.py:250(compile)
     1    0.000    0.000    0.001    0.001 __init__.py:289(_compile)
     1    0.000    0.000    0.000    0.000 _compiler.py:759(compile)
     1    0.000    0.000    0.000    0.000 _parser.py:937(parse)
     1    0.000    0.000    0.000    0.000 _compiler.py:598(_code)
     1    0.000    0.000    0.000    0.000 _parser.py:435(_parse_sub)
~~~

第一行显示有 214 个调用被监控。 在这些调用中，207 个为 _primitive_ ，表示这些调用不是通过递归引起的。 下一行: `Ordered by: cumulative time`，表示最右边那一列中的文本字符串被用来对输出进行排序。 列标题包括:

ncalls

    

~~~
调用次数

tottime
~~~
    

~~~
在指定函数中消耗的总时间（不包括调用子函数的时间）

percall
~~~
    

~~~
是 `tottime` 除以 `ncalls` 的商

cumtime
~~~
    

~~~
指定的函数及其所有子函数（从调用到退出）消耗的累积时间。这个数字对于递归函数来说是准确的。

percall
~~~
    

~~~
是 `cumtime` 除以原始调用（次数）的商（即：函数运行一次的平均时间）

filename:lineno(function)
~~~
    

~~~
提供相应数据的每个函数

如果第一列中有两个数字（例如3/1），则表示函数递归。第二个值是原始调用次数，第一个是调用的总次数。请注意，当函数不递归时，这两个值是相同的，并且只打印单个数字。

profile 运行结束时，打印输出不是必须的。也可以通过为 `run()` 函数指定文件名，将结果保存到文件中：
~~~
    
    
~~~
import cProfile
import re
cProfile.run('re.compile("foo|bar")', 'restats')
~~~

`pstats.Stats` 类从文件中读取 profile 结果，并以各种方式对其进行格式化。

`cProfile` 和 `profile` 文件也可以作为脚本调用，以分析另一个脚本。例如：

    
    
~~~
python -m cProfile [-o output_file] [-s sort_order] (-m module | myscript.py)
~~~

`-o` 将profile 结果写入文件而不是标准输出

`-s` 指定 `sort_stats()` 排序值之一以对输出进行排序。这仅适用于未提供 `-o` 的情况

`-m` 指定要分析的是模块而不是脚本。

> 在 3.7 版本加入: `cProfile` 添加 `-m` 选项
>
> 在 3.8 版本加入: `profile` 添加 `-m` 选项

`pstats` 模块的 `Stats` 类具有各种方法用来操纵和打印保存到性能分析结果文件的数据。

    
    
~~~
import pstats
from pstats import SortKey
p = pstats.Stats('restats')
p.strip_dirs().sort_stats(-1).print_stats()
~~~

`strip_dirs()` 方法移除了所有模块名称中的多余路径。 `sort_stats()` 方法按照打印出来的标准模块/行/名称对所有条目进行排序。 `print_stats()` 方法打印出所有的统计数据。 你可以尝试下列排序调用:

    
    
~~~
p.sort_stats(SortKey.NAME)
p.print_stats()
~~~

第一个调用实际上将按函数名称对列表进行排序，而第二个调用将打印出统计数据。 下面是一些可以尝试的有趣调用:

    
    
~~~
p.sort_stats(SortKey.CUMULATIVE).print_stats(10)
~~~

这将按一个函数中的累计时间对性能分析数据进行排序，然后只打印出最重要的十行。 如果你了解哪些算法在耗费时间，上面这一行就是你应该使用的。

如果你想要看看哪些函数的循环次数多，且耗费时间长，你应当这样做:

    
    
~~~
p.sort_stats(SortKey.TIME).print_stats(10)
~~~

以按照每个函数耗费的时间进行排序，然后打印前十个函数的统计数据。

你也可以尝试：

    
    
~~~
p.sort_stats(SortKey.FILENAME).print_stats('__init__')
~~~

这将按照文件名对所有统计数据进行排序，然后只打印出类初始化方法的统计数据 (因为它们的名称中都有 `__init__`)。 作为最后一个例子，你可以尝试:

    
    
~~~
p.sort_stats(SortKey.TIME, SortKey.CUMULATIVE).print_stats(.5, 'init')
~~~

这一行以时间为主键，并以累计时间为次键进行排序，然后打印出部分统计数据。 具体来说，该列表首先被缩减至原始大小的 50% (即: `.5`)，然后只保留包含 `init` 的行，并打印该子列表。

如果你想知道有哪些函数调用了上述函数，你现在就可以做 (`p` 仍然会按照最后一个标准进行排序):

    
    
~~~
p.print_callers(.5, 'init')
~~~

这样你将得到每个被列出的函数的调用方列表。

如果你想要更多的功能，你就必须阅读手册，或者自行猜测下列函数的作用:

    
    
~~~
p.print_callees()
p.add('restats')
~~~

作为脚本发起调用，`pstats` 模块是一个用于读取和性能分析转储文件的统计数据浏览器。 它有一个简单的面向行的界面（使用 [`cmd`](cmd.md#module-cmd "cmd: Build line-oriented command interpreters.") 实现）和交互式的帮助。

## `profile` 和 `cProfile` 模块参考¶

`profile` 和 `cProfile` 模块都提供下列函数：

profile.run( _command_ , _filename =None_, _sort =-1_)¶

    

~~~
此函数接受一个可被传递给 [`exec()`](functions.md#exec "exec") 函数的单独参数，以及一个可选的文件名。 在所有情况下这个例程都会执行:
~~~
    
    
~~~
exec(command, __main__.__dict__, __main__.__dict__)
~~~

并收集执行过程中的性能分析统计数据。 如果未提供文件名，则此函数会自动创建一个 `Stats` 实例并打印一个简单的性能分析报告。 如果指定了 sort 值，则它会被传递给这个 `Stats` 实例以控制结果的排序方式。

profile.runctx( _command_ , _globals_ , _locals_ , _filename =None_, _sort =-1_)¶

    

~~~
此函数类似于 `run()`，带有为 _command_ 字符串提供全局和局部字典的附加参数。 这个例程会执行:
~~~
    
    
~~~
exec(command, globals, locals)
~~~

并像在上述的 `run()` 函数中一样收集性能分析数据。

_class _profile.Profile( _timer =None_, _timeunit =0.0_, _subcalls =True_, _builtins =True_)¶

    

~~~
这个类通常只在需要比 `cProfile.run()` 函数所能提供的更精确的性能分析控制时被使用。

可以通过 _timer_ 参数提供一个自定义计时器来测量代码运行花费了多长时间。 它必须是一个返回代表当前时间的单个数字的函数。 如果该数字为整数，则 _timeunit_ 指定一个表示每个时间单位持续时间的乘数。 例如，如果定时器返回以千秒为计量单位的时间值，则时间单位将为 `.001`。

直接使用 `Profile` 类将允许格式化性能分析结果而无需将性能分析数据写入到文件:
~~~
    
    
~~~
import cProfile, pstats, io
from pstats import SortKey
pr = cProfile.Profile()
pr.enable()
# ... do something ...
pr.disable()
s = io.StringIO()
sortby = SortKey.CUMULATIVE
ps = pstats.Stats(pr, stream=s).sort_stats(sortby)
ps.print_stats()
print(s.getvalue())
~~~

`Profile` 类也可作为上下文管理器使用 (仅在 `cProfile` 模块中支持。 参见 [上下文管理器类型](stdtypes.md#typecontextmanager)):

    
    
~~~
import cProfile

with cProfile.Profile() as pr:
    # ... do something ...

    pr.print_stats()
~~~

在 3.8 版本发生变更: 添加了上下文管理器支持。

enable()¶

    

~~~
开始收集分析数据。仅在 `cProfile` 可用。

disable()¶
~~~
    

~~~
停止收集分析数据。仅在 `cProfile` 可用。

create_stats()¶
~~~
    

~~~
停止收集分析数据，并在内部将结果记录为当前 profile。

print_stats( _sort =-1_)¶
~~~
    

~~~
根据当前性能分析数据创建一个 `Stats` 对象并将结果打印到 stdout。

dump_stats( _filename_ )¶
~~~
    

~~~
将当前profile 的结果写入 _filename_ 。

run( _cmd_ )¶
~~~
    

~~~
通过 [`exec()`](functions.md#exec "exec") 对该命令进行性能分析。

runctx( _cmd_ , _globals_ , _locals_ )¶
~~~
    

~~~
通过 [`exec()`](functions.md#exec "exec") 并附带指定的全局和局部环境对该命令进行性能分析。

runcall( _func_ , _/_ , _* args_, _** kwargs_)¶
~~~
    

~~~
对 `func(*args, **kwargs)` 进行性能分析

请注意性能分析只有在被调用的命令/函数确实能返回时才可用。 如果解释器被终结（例如在被调用的命令/函数执行期间通过 [`sys.exit()`](sys.md#sys.exit "sys.exit") 调用）则将不会打印性能分析结果。

## `Stats` 类¶

性能数据的分析是使用 `Stats` 类来完成的。

_class _pstats.Stats( _*filenames or profile_, _stream=sys.stdout_ )¶
~~~
    

~~~
这个类构造器会基于 _filename_ (或文件名列表) 或者 `Profile` 实例创建一个“统计对象”。 输出将被打印到由 _stream_ 所指定的流。

上述构造器所选择的文件必须由相应版本的 `profile` 或 `cProfile` 来创建。 具体来说， _不会_ 保证文件与此性能分析器的未来版本兼容，也不会保证与其他性能分析器，或运行于不同操作系统的同一性能分析器所产生的文件兼容。 如果提供了几个文件，则相同函数的所有统计数据将被聚合在一起，这样就可以在单个报告中同时考虑几个进程的总体情况。 如果额外的文件需要与现有 `Stats` 对象中的数据相结合，则可以使用 `add()` 方法。

作为从一个文件读取性能分析数据的替代，可以使用 `cProfile.Profile` 或 `profile.Profile` 对象作为性能分析数据源。

`Stats` 对象有以下方法:

strip_dirs()¶
~~~
    

~~~
这个用于 `Stats` 类的方法会从文件名中去除所有前导路径信息。 它对于减少打印输出的大小以适应（接近） 80 列限制。 这个方法会修改对象，被去除的信息将会丢失。 在执行去除操作后，可以认为对象拥有的条目将使用“随机”顺序，就像它刚在对象初始化并加载之后一样。 如果 `strip_dirs()` 导致两个函数名变得无法区分（它们位于相同文件名的相同行，并且具有相同的函数名），那么这两个条目的统计数据将被累积到单个条目中。

add( _* filenames_)¶
~~~
    

~~~
`Stats` 类的这个方法会将额外的性能分析信息累积到当前的性能分析对象中。 它的参数应当指向由相应版本的 `profile.run()` 或 `cProfile.run()` 所创建的文件名。 相同名称（包括 file, line, name）函数的统计信息会自动累积到单个函数的统计信息。

dump_stats( _filename_ )¶
~~~
    

~~~
将加载至 `Stats` 对象内的数据保存到名为 _filename_ 的文件。 该文件如果不存在则将被创建，如果已存在则将被覆盖。 这等价于 `profile.Profile` 和 `cProfile.Profile` 类上的同名方法。

sort_stats( _* keys_)¶
~~~
    

~~~
此方法通过根据所提供的准则修改 `Stats` 对象的排序。 其参数可以是一个字符串或标识排序准则的 SortKey 枚举 (例如: `'time'`, `'name'`, `SortKey.TIME` 或 `SortKey.NAME`)。 SortKey 枚举参数优于字符串参数因为它更为健壮且更不容易出错。

当提供一个以上的键时，额外的键将在之前选择的所有键的值相等时被用作次级准则。 例如，`sort_stats(SortKey.NAME, SortKey.FILE)` 将根据其函数名对所有条目排序，并通过按文件名排序来处理所有平局（即函数名相同）。

对于字符串参数，可以对任何键名使用缩写形式，只要缩写是无歧义的。

以下是有效的字符串和 SortKey:

有效字符串参数

|

有效枚举参数

|

含意  
  
---|---|---  
  
`'calls'`

|

SortKey.CALLS

|

调用次数  
  
`'cumulative'`

|

SortKey.CUMULATIVE

|

累积时间  
  
`'cumtime'`

|

N/A

|

累积时间  
  
`'file'`

|

N/A

|

文件名  
  
`'filename'`

|

SortKey.FILENAME

|

文件名  
  
`'module'`

|

N/A

|

文件名  
  
`'ncalls'`

|

N/A

|

调用次数  
  
`'pcalls'`

|

SortKey.PCALLS

|

原始调用计数  
  
`'line'`

|

SortKey.LINE

|

行号  
  
`'name'`

|

SortKey.NAME

|

函数名称  
  
`'nfl'`

|

SortKey.NFL

|

名称/文件/行  
  
`'stdname'`

|

SortKey.STDNAME

|

标准名称  
  
`'time'`

|

SortKey.TIME

|

内部时间  
  
`'tottime'`

|

N/A

|

内部时间  
  
请注意对统计信息的所有排序都是降序的（将最耗时的条目放在最前面），其中名称、文件和行号搜索则是升序的（字母顺序）。 `SortKey.NFL` 和 `SortKey.STDNAME` 之间的细微区别在于标准名称是按打印形式来排序名称的，这意味着嵌入的行号将以一种怪异的方式进行比较。 例如，第 3, 20 和 40 行将会按字符串顺序 20, 3 和 40 显示（如果文件名相同的话）。 相反地，`SortKey.NFL` 则会对行号进行数值比较。 实际上，`sort_stats(SortKey.NFL)` 就等同于 `sort_stats(SortKey.NAME, SortKey.FILENAME, SortKey.LINE)`。

出于向下兼容的理由，数值参数 `-1`, `0`, `1` 和 `2` 也是被允许的。 它们将被分别解读为 `'stdname'`, `'calls'`, `'time'` 和 `'cumulative'`。 如果使用这种老旧格式（数值），则将只使用一个排序键（数字键），额外的参数将被静默地忽略。

在 3.7 版本加入: 增加了 SortKey 枚举。

reverse_order()¶
~~~
    

~~~
这个用于 `Stats` 类的方法将会反转对象内基本列表的顺序。 请注意在默认情况下升序和降序排列将基于所选定的排序键来进行适当的选择。

print_stats( _* restrictions_)¶
~~~
    

~~~
这个用于 `Stats` 类的方法将打印出在 `profile.run()` 定义中描述的报告。

打印的顺序是基于在对象上执行的最后一次 `sort_stats()` 操作（需要注意 `add()` 和 `strip_dirs()` 规则）。

所提供的参数（如果存在）可被用来将列表限制为重要的条目。 在初始状态下，列表将为加入性能分析的函数的完整集合。 每条限制可以是一个整数（用来选择行数），或是一个 0.0 至 1.0 范围内左开右闭的十进制小数（用来选择行数百分比），或是一个将被解读为正则表达式的字符串（用来匹配要打印的标准名称的模式）。 如果提供了多条限制，则它们将逐个被应用。 例如:
~~~
    
    
~~~
print_stats(.1, 'foo:')
~~~

将首先限制为打印列表的前 10%，然后再限制为仅打印在名为 `.*foo:` 的文件内的函数。 作为对比，以下命令:

    
    
~~~
print_stats('foo:', .1)
~~~

将列表限制为名为 `.*foo:` 的文件内的所有函数，然后再限制为仅打印它们当中的前 10%。

print_callers( _* restrictions_)¶

    

~~~
这个用于 `Stats` 类的方法将打印调用了加入性能分析数据库的每个函数的所有函数的列表。 打印顺序与 `print_stats()` 所提供的相同，受限参数的定义也是相同的。 每个调用方将在单独的行中报告。 具体格式根据产生统计数据的性能分析器的不同而有所差异。

  * 使用 `profile` 时，将在每个调用方之后的圆括号内显示一个数字来指明相应的调用执行了多少次。 为了方便起见，右侧还有第二个不带圆括号的数字来重复显示该函数累计耗费的时间。

  * 使用 `cProfile` 时，每个调用方前面将有三个数字：这个调用的执行次数，以及当前函数在被这个调用方发起调用其中共计和累计耗费的时间。

print_callees( _* restrictions_)¶
~~~
    

~~~
这个用于 `Stats` 类的方法将打印被指定的函数所调用的所有函数的列表。 除了调用方向是逆序的（对应：被调用和被调用方），其参数和顺序与 `print_callers()` 方法相同。

get_stats_profile()¶
~~~
    

~~~
此方法返回一个 StatsProfile 的实例，它包含从函数名称到 FunctionProfile 实例的映射。 每个 FunctionProfile 实例保存了相应函数性能分析的有关信息如函数运行耗费了多长时间，它被调用了多少次等等……

在 3.9 版本加入: 添加了以下数据类: StatsProfile, FunctionProfile。 添加了以下函数: get_stats_profile。

## 什么是确定性性能分析？¶

_确定性性能分析_ 旨在反映这样一个事实：即所有 _函数调用_ 、 _函数返回_ 和 _异常_ 事件都被监控，并且对这些事件之间的间隔（在此期间用户的代码正在执行）进行精确计时。相反，统计分析（不是由该模块完成）随机采样有效指令指针，并推断时间花费在哪里。后一种技术传统上涉及较少的开销（因为代码不需要检测），但只提供了时间花在哪里的相对指示。

在Python中，由于在执行过程中总有一个活动的解释器，因此执行确定性评测不需要插入指令的代码。Python 自动为每个事件提供一个 _钩子_ （可选回调）。此外，Python 的解释特性往往会给执行增加太多开销，以至于在典型的应用程序中，确定性分析往往只会增加很小的处理开销。结果是，确定性分析并没有那么代价高昂，但是它提供了有关 Python 程序执行的大量运行时统计信息。

调用计数统计信息可用于识别代码中的错误（意外计数），并识别可能的内联扩展点（高频调用）。内部时间统计可用于识别应仔细优化的 "热循环" 。累积时间统计可用于识别算法选择上的高级别错误。请注意，该分析器中对累积时间的异常处理，允许直接比较算法的递归实现与迭代实现的统计信息。

## 局限性¶

一个限制是关于时间信息的准确性。确定性性能分析存在一个涉及精度的基本问题。最明显的限制是，底层的 "时钟" 周期大约为0.001秒（通常）。因此，没有什么测量会比底层时钟更精确。如果进行了足够的测量，那么 "误差" 将趋于平均。不幸的是，删除第一个错误会导致第二个错误来源。

第二个问题是，从调度事件到分析器调用获取时间函数实际 _获取_ 时钟状态，这需要 "一段时间" 。类似地，从获取时钟值（然后保存）开始，直到再次执行用户代码为止，退出分析器事件句柄时也存在一定的延迟。因此，多次调用单个函数或调用多个函数通常会累积此错误。尽管这种方式的误差通常小于时钟的精度（小于一个时钟周期），但它 _可以_ 累积并变得非常可观。

与开销较低的 `cProfile` 相比， `profile` 的问题更为严重。出于这个原因， `profile` 提供了一种针对指定平台的自我校准方法，以便可以在很大程度上（平均地）消除此误差。 校准后，结果将更准确（在最小二乘意义上），但它有时会产生负数（当调用计数异常低，且概率之神对您不利时：-）。因此 _不要_ 对产生的负数感到惊慌。它们应该只在你手工校准分析器的情况下才会出现，实际上结果比没有校准的情况要好。

## 准确估量¶

`profile` 模块的 profiler 会从每个事件处理时间中减去一个常量，以补偿调用 time 函数和存储结果的开销。默认情况下，常数为0。对于特定的平台，可用以下程序获得更好修正常数（ 局限性 ）。
~~~
    
    
~~~
import profile
pr = profile.Profile()
for i in range(5):
    print(pr.calibrate(10000))
~~~

此方法将执行由参数所给定次数的 Python 调用，在性能分析器之下直接和再次地执行，并对两次执行计时。 它将随后计算每个性能分析器事件的隐藏开销，并将其以浮点数的形式返回。 例如，在一台运行 macOS 的 1.8Ghz Intel Core i5 上，使用 Python 的 time.process_time() 作为计时器，魔数大约为 4.04e-6。

此操作的目标是获得一个相当稳定的结果。 如果你的计算机 _非常_ 快速，或者你的计时器函数的分辨率很差，你可能必须传入 100000，甚至 1000000，才能得到稳定的结果。

当你有一个一致的答案时，有三种方法可以使用：

    
    
~~~
import profile

# 1. Apply computed bias to all Profile instances created hereafter.
profile.Profile.bias = your_computed_bias

# 2. Apply computed bias to a specific Profile instance.
pr = profile.Profile()
pr.bias = your_computed_bias

# 3. Specify computed bias in instance constructor.
pr = profile.Profile(bias=your_computed_bias)
~~~

如果你可以选择，那么选择更小的常量会更好，这样你的结果将“更不容易”在性能分析统计中显示负值。

## 使用自定义计时器¶

如果你想要改变当前时间的确定方式（例如，强制使用时钟时间或进程持续时间），请向 `Profile` 类构造器传入你想要的计时函数:

    
    
~~~
pr = profile.Profile(your_time_func)
~~~

结果性能分析器将随后调用 `your_time_func`。 根据你使用的是 `profile.Profile` 还是 `cProfile.Profile`，`your_time_func` 的返回值将有不同的解读方式:

`profile.Profile`

    

~~~
`your_time_func` 应当返回一个数字，或一个总和为当前时间的数字列表（如同 [`os.times()`](os.md#os.times "os.times") 所返回的内容）。 如果该函数返回一个数字，或所返回的数字列表长度为 2，则你将得到一个特别快速的调度例程版本。

请注意你应当为你选择的计时器函数校准性能分析器类 (参见 准确估量)。 对于大多数机器来说，一个返回长整数值的计时器在性能分析期间将提供在低开销方面的最佳结果。 ([`os.times()`](os.md#os.times "os.times") 是 _相当_ 糟糕的，因为它返回一个浮点数值的元组)。 如果你想以最干净的方式替换一个更好的计时器，请派生一个类并硬连线一个能最佳地处理计时器调用的替换调度方法，并使用适当的校准常量。

`cProfile.Profile`
~~~
    

~~~
`your_time_func` 应当返回一个数字。 如果它返回整数，你还可以通过第二个参数指定一个单位时间的实际持续长度来发起调用类构造器。 举例来说，如果 `your_integer_time_func` 返回以千秒为单位的时间，则你应当以如下方式构造 `Profile` 实例:
~~~
    
    
~~~
pr = cProfile.Profile(your_integer_time_func, 0.001)
~~~

由于 `cProfile.Profile` 类无法被校准，因此自定义计时器函数应当要小心地使用并应当尽可能地快速。 为了使自定义计时器获得最佳结果，可能需要在内部 `_lsprof` 模块的 C 源代码中对其进行硬编码。

Python 3.3 在 [`time`](time.md#module-time "time: Time access and conversions.") 中添加了几个可被用来精确测量进程或时钟时间的新函数。 例如，参见 [`time.perf_counter()`](time.md#time.perf_counter "time.perf_counter")。

