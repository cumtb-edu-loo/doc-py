# `tracemalloc` \--- 跟踪内存分配¶

在 3.4 版本加入.

**源代码：** [Lib/tracemalloc.py](https://github.com/python/cpython/tree/3.12/Lib/tracemalloc.py)

* * *

tracemalloc 模块是一个用于对 python 已申请的内存块进行debug的工具。它能提供以下信息:

  * 回溯对象分配内存的位置

  * 按文件、按行统计python的内存块分配情况: 内存块总大小、数量以及块平均大小。

  * 对比两个内存快照的差异，以便排查内存泄漏

要追踪 Python 所分配的大部分内存块，模块应当通过将 [`PYTHONTRACEMALLOC`](1.%20命令行与环境.md#envvar-PYTHONTRACEMALLOC) 环境变量设置为 `1`，或是通过使用 [`-X`](1.%20命令行与环境.md#cmdoption-X) `tracemalloc` 命令行选项来尽可能早地启动。 可以在运行时调用 `tracemalloc.start()` 函数来启动追踪 Python 内存分配。memory allocations.

在默认情况下，一个已分配内存块的追踪将只储存最新的帧 (1 帧)。 要要启动时储存 25 帧：将 [`PYTHONTRACEMALLOC`](1.%20命令行与环境.md#envvar-PYTHONTRACEMALLOC) 环境变量设为 `25`，或使用 [`-X`](1.%20命令行与环境.md#cmdoption-X) `tracemalloc=25` 命令行选项。

## 例子¶

### 显示前10项¶

显示内存分配最多的10个文件：

    
    
~~~
import tracemalloc

tracemalloc.start()

# ... run your application ...

snapshot = tracemalloc.take_snapshot()
top_stats = snapshot.statistics('lineno')

print("[ Top 10 ]")
for stat in top_stats[:10]:
    print(stat)
~~~

Python测试套件的输出示例：

    
    
~~~
[ Top 10 ]
<frozen importlib._bootstrap>:716: size=4855 KiB, count=39328, average=126 B
<frozen importlib._bootstrap>:284: size=521 KiB, count=3199, average=167 B
/usr/lib/python3.4/collections/__init__.py:368: size=244 KiB, count=2315, average=108 B
/usr/lib/python3.4/unittest/case.py:381: size=185 KiB, count=779, average=243 B
/usr/lib/python3.4/unittest/case.py:402: size=154 KiB, count=378, average=416 B
/usr/lib/python3.4/abc.py:133: size=88.7 KiB, count=347, average=262 B
<frozen importlib._bootstrap>:1446: size=70.4 KiB, count=911, average=79 B
<frozen importlib._bootstrap>:1454: size=52.0 KiB, count=25, average=2131 B
<string>:5: size=49.7 KiB, count=148, average=344 B
/usr/lib/python3.4/sysconfig.py:411: size=48.0 KiB, count=1, average=48.0 KiB
~~~

我们可以看到 Python 从模块载入了 `4855 KiB` 数据（字节码和常量）并且 [`collections`](collections.md#module-collections "collections: Container datatypes") 模块分配了 `244 KiB` 来构建 [`namedtuple`](collections.md#collections.namedtuple "collections.namedtuple") 类型。

更多选项，请参见 `Snapshot.statistics()`

### 计算差异¶

获取两个快照并显示差异：

    
    
~~~
import tracemalloc
tracemalloc.start()
# ... start your application ...

snapshot1 = tracemalloc.take_snapshot()
# ... call the function leaking memory ...
snapshot2 = tracemalloc.take_snapshot()

top_stats = snapshot2.compare_to(snapshot1, 'lineno')

print("[ Top 10 differences ]")
for stat in top_stats[:10]:
    print(stat)
~~~

运行 Python 测试套件的部分测试之前/之后的输出样例:

    
    
~~~
[ Top 10 differences ]
<frozen importlib._bootstrap>:716: size=8173 KiB (+4428 KiB), count=71332 (+39369), average=117 B
/usr/lib/python3.4/linecache.py:127: size=940 KiB (+940 KiB), count=8106 (+8106), average=119 B
/usr/lib/python3.4/unittest/case.py:571: size=298 KiB (+298 KiB), count=589 (+589), average=519 B
<frozen importlib._bootstrap>:284: size=1005 KiB (+166 KiB), count=7423 (+1526), average=139 B
/usr/lib/python3.4/mimetypes.py:217: size=112 KiB (+112 KiB), count=1334 (+1334), average=86 B
/usr/lib/python3.4/http/server.py:848: size=96.0 KiB (+96.0 KiB), count=1 (+1), average=96.0 KiB
/usr/lib/python3.4/inspect.py:1465: size=83.5 KiB (+83.5 KiB), count=109 (+109), average=784 B
/usr/lib/python3.4/unittest/mock.py:491: size=77.7 KiB (+77.7 KiB), count=143 (+143), average=557 B
/usr/lib/python3.4/urllib/parse.py:476: size=71.8 KiB (+71.8 KiB), count=969 (+969), average=76 B
/usr/lib/python3.4/contextlib.py:38: size=67.2 KiB (+67.2 KiB), count=126 (+126), average=546 B
~~~

我们可以看到 Python 已载入了 `8173 KiB` 模块数据（字节码和常量），并且这比测试之前，即保存前一个快照时载入的数据多出了 `4428 KiB`。 类似地， [`linecache`](linecache.md#module-linecache "linecache: Provides random access to individual lines from text files.") 模块已缓存 `940 KiB` 的 Python 源代码至格式回溯中，即从前一个快照开始的所有数据。

如果系统空闲内存太少，可以使用 `Snapshot.dump()` 方法将快照写入磁盘来离线分析快照。 然后使用 `Snapshot.load()` 方法重载快照。

### 获取一个内存块的溯源¶

一段找出程序中最大内存块溯源的代码:

    
    
~~~
import tracemalloc

# Store 25 frames
tracemalloc.start(25)

# ... run your application ...

snapshot = tracemalloc.take_snapshot()
top_stats = snapshot.statistics('traceback')

# pick the biggest memory block
stat = top_stats[0]
print("%s memory blocks: %.1f KiB" % (stat.count, stat.size / 1024))
for line in stat.traceback.format():
    print(line)
~~~

一段Python单元测试的输出案例(限制最大25层堆栈)

    
    
~~~
903 memory blocks: 870.1 KiB
  File "<frozen importlib._bootstrap>", line 716
  File "<frozen importlib._bootstrap>", line 1036
  File "<frozen importlib._bootstrap>", line 934
  File "<frozen importlib._bootstrap>", line 1068
  File "<frozen importlib._bootstrap>", line 619
  File "<frozen importlib._bootstrap>", line 1581
  File "<frozen importlib._bootstrap>", line 1614
  File "/usr/lib/python3.4/doctest.py", line 101
    import pdb
  File "<frozen importlib._bootstrap>", line 284
  File "<frozen importlib._bootstrap>", line 938
  File "<frozen importlib._bootstrap>", line 1068
  File "<frozen importlib._bootstrap>", line 619
  File "<frozen importlib._bootstrap>", line 1581
  File "<frozen importlib._bootstrap>", line 1614
  File "/usr/lib/python3.4/test/support/__init__.py", line 1728
    import doctest
  File "/usr/lib/python3.4/test/test_pickletools.py", line 21
    support.run_doctest(pickletools)
  File "/usr/lib/python3.4/test/regrtest.py", line 1276
    test_runner()
  File "/usr/lib/python3.4/test/regrtest.py", line 976
    display_failure=not verbose)
  File "/usr/lib/python3.4/test/regrtest.py", line 761
    match_tests=ns.match_tests)
  File "/usr/lib/python3.4/test/regrtest.py", line 1563
    main()
  File "/usr/lib/python3.4/test/__main__.py", line 3
    regrtest.main_in_temp_cwd()
  File "/usr/lib/python3.4/runpy.py", line 73
    exec(code, run_globals)
  File "/usr/lib/python3.4/runpy.py", line 160
    "__main__", fname, loader, pkg_name)
~~~

我们可以看到大部分内存都被分配到 [`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.") 模块中以便从模块中加载数据（字节码和常量）: `870.1 KiB`。 回溯位置是 [`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.") 最近加载数据的地方：在 [`doctest`](doctest.md#module-doctest "doctest: Test pieces of code within docstrings.") 模块的 `import pdb` 行。 如果加载了新模块则回溯可能发生改变。line of the. The traceback may change if a new module is loaded.

### 美化的 top¶

使用美化输出显示分配最多内存的 10 行的代码，忽略 `<frozen importlib._bootstrap>` 和 `<unknown>` 文件:

    
    
~~~
import linecache
import os
import tracemalloc

def display_top(snapshot, key_type='lineno', limit=10):
    snapshot = snapshot.filter_traces((
        tracemalloc.Filter(False, "<frozen importlib._bootstrap>"),
        tracemalloc.Filter(False, "<unknown>"),
    ))
    top_stats = snapshot.statistics(key_type)

    print("Top %s lines" % limit)
    for index, stat in enumerate(top_stats[:limit], 1):
        frame = stat.traceback[0]
        print("#%s: %s:%s: %.1f KiB"
              % (index, frame.filename, frame.lineno, stat.size / 1024))
        line = linecache.getline(frame.filename, frame.lineno).strip()
        if line:
            print('    %s' % line)

    other = top_stats[limit:]
    if other:
        size = sum(stat.size for stat in other)
        print("%s other: %.1f KiB" % (len(other), size / 1024))
    total = sum(stat.size for stat in top_stats)
    print("Total allocated size: %.1f KiB" % (total / 1024))

tracemalloc.start()

# ... run your application ...

snapshot = tracemalloc.take_snapshot()
display_top(snapshot)
~~~

Python测试套件的输出示例：

    
    
~~~
Top 10 lines
#1: Lib/base64.py:414: 419.8 KiB
    _b85chars2 = [(a + b) for a in _b85chars for b in _b85chars]
#2: Lib/base64.py:306: 419.8 KiB
    _a85chars2 = [(a + b) for a in _a85chars for b in _a85chars]
#3: collections/__init__.py:368: 293.6 KiB
    exec(class_definition, namespace)
#4: Lib/abc.py:133: 115.2 KiB
    cls = super().__new__(mcls, name, bases, namespace)
#5: unittest/case.py:574: 103.1 KiB
    testMethod()
#6: Lib/linecache.py:127: 95.4 KiB
    lines = fp.readlines()
#7: urllib/parse.py:476: 71.8 KiB
    for a in _hexdig for b in _hexdig}
#8: <string>:5: 62.0 KiB
#9: Lib/_weakrefset.py:37: 60.0 KiB
    self.data = set()
#10: Lib/base64.py:142: 59.8 KiB
    _b32tab2 = [a + b for a in _b32tab for b in _b32tab]
6220 other: 3602.8 KiB
Total allocated size: 5303.1 KiB
~~~

更多选项，请参见 `Snapshot.statistics()`

#### 记录所有被追踪内存块的当前和峰值大小¶

以下代码通过创建一个包含数字的列表来低效率地计算总计值如 `0 + 1 + 2 + ...`。 该列表会临时消耗大量内存。 我们可以使用 `get_traced_memory()` 和 `reset_peak()` 来观察计算总计值之后的内存使用减少以及计算过程中的内存使用峰值:

    
    
~~~
import tracemalloc

tracemalloc.start()

# Example code: compute a sum with a large temporary list
large_sum = sum(list(range(100000)))

first_size, first_peak = tracemalloc.get_traced_memory()

tracemalloc.reset_peak()

# Example code: compute a sum with a small temporary list
small_sum = sum(list(range(1000)))

second_size, second_peak = tracemalloc.get_traced_memory()

print(f"{first_size=}, {first_peak=}")
print(f"{second_size=}, {second_peak=}")
~~~

输出:

    
    
~~~
first_size=664, first_peak=3592984
second_size=804, second_peak=29704
~~~

使用 `reset_peak()` 将确保我们能够准确地记录 `small_sum` 计算期间的峰值，即使它远小于从 `start()` 调用以来内存块的总体峰值大小。 如果没有对 `reset_peak()` 的调用，`second_peak` 将仍为计算 `large_sum` 时的峰值 (也就是说，等于 `first_peak`)。 在这种情况下，两个峰值都将比最终的内存使用量高得多，这表明我们可以进行优化 (通过移除不必要的对 [`list`](stdtypes.md#list "list") 的调用，并改写为 `sum(range(...))`)。

## API¶

### 函数¶

tracemalloc.clear_traces()¶

    

~~~
清空 Python 所分配的内存块的追踪数据。

另见 `stop()`.

tracemalloc.get_object_traceback( _obj_ )¶
~~~
    

~~~
获取 Python 对象 _obj_ 被分配位置的回溯。 返回一个 `Traceback` 实例，或者如果 `tracemalloc` 模块未追踪任何内存分配或未追踪该对象的分配则返回 `None`。

另请参阅 [`gc.get_referrers()`](gc.md#gc.get_referrers "gc.get_referrers") 和 [`sys.getsizeof()`](sys.md#sys.getsizeof "sys.getsizeof") 函数。

tracemalloc.get_traceback_limit()¶
~~~
    

~~~
获取保存在一个追踪的回溯中的最大帧数。

`tracemalloc` 模块必须正在追踪内存分配才能获得该限制值，否则将引发异常。

该限制是由 `start()` 函数设置的。

tracemalloc.get_traced_memory()¶
~~~
    

~~~
获取一个元组形式的由 `tracemalloc` 模块所追踪的内存块的当前大小和峰值大小: `(current: int, peak: int)`。

tracemalloc.reset_peak()¶
~~~
    

~~~
将由 `tracemalloc` 模块所追踪的内存块的峰值大小设置为当前大小。

如果 `tracemalloc` 模块未在追踪内存分配则不做任何事。

此函数只修改已记录的峰值大小，而不会修改或清空任何追踪，这不同于 `clear_traces()`。 在调用 `reset_peak()` 之前使用 `take_snapshot()` 保存的快照可以与调用之后保存的快照进行有意义的比较。

另请参阅 `get_traced_memory()`。

在 3.9 版本加入.

tracemalloc.get_tracemalloc_memory()¶
~~~
    

~~~
获取 `tracemalloc` 模块用于保存内存块追踪所使用的内存字节数。 返回一个 [`int`](functions.md#int "int")。

tracemalloc.is_tracing()¶
~~~
    

~~~
如果 `tracemalloc` 模块正在追踪 Python 内存分配则返回 `True`，否则返回 `False`。

另请参阅 `start()` 和 `stop()` 函数。

tracemalloc.start( _nframe : [int](functions.md#int "int") = 1_)¶
~~~
    

~~~
开始追踪 Python 内存分配：在 Python 内存分配器上安装钩子。 收集的追踪回溯将被限制为 _nframe_ 个帧。 在默认情况下，一个内存块的追踪将只保存最近的帧：即限制为 `1`。 _nframe_ 必须大于等于 `1`。

你仍然可以通过访问 `Traceback.total_nframe` 属性来读取组成回溯的原始总帧数。

保存 `1` 帧以上仅适用于计算由 `'traceback'` 分组的统计数据或计算累积的统计数据：请参阅 `Snapshot.compare_to()` 和 `Snapshot.statistics()` 方法。

保存更多帧会增加 `tracemalloc` 模块的内存和 CPU 开销。 请使用 `get_tracemalloc_memory()` 函数来检测 `tracemalloc` 模块消耗了多少内存。

[`PYTHONTRACEMALLOC`](../using/cmdline.md#envvar-PYTHONTRACEMALLOC) 环境变量 (`PYTHONTRACEMALLOC=NFRAME`) 和 [`-X`](../using/cmdline.md#cmdoption-X) `tracemalloc=NFRAME` 命令行选项可被用来在启动时开始追踪。

另请参阅 `stop()`, `is_tracing()` 和 `get_traceback_limit()` 等函数。

tracemalloc.stop()¶
~~~
    

~~~
停止追踪 Python 内存分配：卸载 Python 内存分配器上的钩子。 并清空之前收集的所有由 Python 分配的内存块的追踪。

调用 `take_snapshot()` 函数在清空追踪之前保存它们的快照。

另请参阅 `start()`, `is_tracing()` 和 `clear_traces()` 等函数。

tracemalloc.take_snapshot()¶
~~~
    

~~~
保存一个由 Python 分配的内存块的追踪的快照。 返回一个新的 `Snapshot` 实例。

该快照不包括在 `tracemalloc` 模块开始追踪内存分配之前分配的内存块。

追踪的回溯被限制为 `get_traceback_limit()` 个帧。 可使用 `start()` 函数的 _nframe_ 形参来保存更多的帧。

`tracemalloc` 模块必须正在追踪内存分配才能保存快照，参见 `start()` 函数。

另请参阅 `get_object_traceback()` 函数。

### 域过滤器¶

_class _tracemalloc.DomainFilter( _inclusive : [bool](functions.md#bool "bool")_, _domain : [int](functions.md#int "int")_)¶
~~~
    

~~~
按地址空间（域）来过滤内存块的追踪。

在 3.6 版本加入.

inclusive¶
~~~
    

~~~
如果 _inclusive_ 为 `True` (包括)，则匹配分配于地址空间 `domain` 中的内存块。

如果 _inclusive_ 为 `False` (排除)，则匹配不是分配于地址空间 `domain` 中的内存块。

domain¶
~~~
    

~~~
内存块的地址空间 (`int`)。 只读的特征属性。 Read-only property.

### 过滤器¶

_class _tracemalloc.Filter( _inclusive : [bool](functions.md#bool "bool")_, _filename_pattern : [str](stdtypes.md#str "str")_, _lineno : [int](functions.md#int "int") = None_, _all_frames : [bool](functions.md#bool "bool") = False_, _domain : [int](functions.md#int "int") = None_)¶
~~~
    

~~~
对内存块的跟踪进行筛选。

请参阅 [`fnmatch.fnmatch()`](fnmatch.md#fnmatch.fnmatch "fnmatch.fnmatch") 函数来了解 _filename_pattern_ 的语法。 `'.pyc'` 文件扩展名以 `'.py'` 替换。

示例：

  * `Filter(True, subprocess.__file__)` 只包括 [`subprocess`](subprocess.md#module-subprocess "subprocess: Subprocess management.") 模块的追踪数据

  * `Filter(False, tracemalloc.__file__)` 排除了 `tracemalloc` 模块的追踪数据

  * `Filter(False, "<unknown>")` 排除了空的回溯信息

在 3.5 版本发生变更: `'.pyo'` 文件扩展名不会再被替换为 `'.py'`。

在 3.6 版本发生变更: 增加了 `domain` 属性。

domain¶
~~~
    

~~~
内存块的地址空间 (`int` 或 `None`)。

tracemalloc 使用 `0` 号域来追踪 Python 的内存分配操作。 C 扩展可以使用其他域来追踪其他资源。extensions can use other domains to trace other resources.

inclusive¶
~~~
    

~~~
如果 _inclusive_ 为 `True` (包括)，则只匹配名称与 `filename_pattern` 匹配的文件在行号为 `lineno` 的位置上分配的内存块。

如果 _inclusive_ 为 `False` (排除)，则忽略名称与 `filename_pattern` 匹配的文件在行号为 `lineno` 的位置上分配的内存块。

lineno¶
~~~
    

~~~
过滤器的行号 (`int`)。 如果 _lineno_ 为 `None`，则该过滤器将匹配任意行号。

filename_pattern¶
~~~
    

~~~
过滤器的文件名模型 (`str`)。 只读的特征属性。

all_frames¶
~~~
    

~~~
如果 _all_frames_ 为 `True`，则回溯的所有帧都会被检查。 如果 _all_frames_ 为 `False`，则只有最近的帧会被检查。

如果回溯限制为 `1` 则该属性将没有效果。 参见 `get_traceback_limit()` 函数和 `Snapshot.traceback_limit` 属性。

### 帧¶

_class _tracemalloc.Frame¶
~~~
    

~~~
回溯的帧。

`Traceback` 类是一个 `Frame` 实例的序列。instances.

filename¶
~~~
    

~~~
文件名（`字符串`）

lineno¶
~~~
    

~~~
行号（`整形`）

### 快照¶

_class _tracemalloc.Snapshot¶
~~~
    

~~~
由 Python 分配的内存块的追踪的快照。

`take_snapshot()` 函数创建一个快照实例。

compare_to( _old_snapshot : Snapshot_, _key_type : [str](stdtypes.md#str "str")_, _cumulative : [bool](functions.md#bool "bool") = False_)¶
~~~
    

~~~
计算与某个旧快照的差异。 获取按 _key_type_ 分组的 `StatisticDiff` 实例的已排序列表形式的统计信息。

请参阅 `Snapshot.statistics()` 方法了解 _key_type_ 和 _cumulative_ 形参。

结果将按以下值从大到小排序: `StatisticDiff.size_diff` 的绝对值, `StatisticDiff.size`, `StatisticDiff.count_diff` 的绝对值, `Statistic.count` 然后是 `StatisticDiff.traceback`。

dump( _filename_ )¶
~~~
    

~~~
将快照写入文件

使用 `load()` 重载快照。

filter_traces( _filters_ )¶
~~~
    

~~~
使用已过滤的 `traces` 序列新建一个 `Snapshot` 实例， _filters_ 是 `DomainFilter` 和 `Filter` 实例的列表。 如果 _filters_ 为空列表，则返回一个包含追踪的副本的新的 `Snapshot` 实例。

包括的所有过滤器将同时被应用，一个追踪如果没有任何包括的过滤器与其匹配则会被忽略。 一个追踪如果有至少一个排除的过滤器与其匹配将会被忽略。

在 3.6 版本发生变更: `DomainFilter` 实例现在同样被 _filters_ 所接受。

_classmethod _load( _filename_ )¶
~~~
    

~~~
从文件载入快照。

另见 `dump()`.

statistics( _key_type : [str](stdtypes.md#str "str")_, _cumulative : [bool](functions.md#bool "bool") = False_)¶
~~~
    

~~~
获取 `Statistic` 信息列表，按 _key_type_ 分组排序：

key_type

|

description  
  
---|---  
  
`'filename'`

|

文件名  
  
`'lineno'`

|

文件名和行号  
  
`'traceback'`

|

回溯  
  
如果 _cumulative_ 为 `True`，则累积一个追踪的回溯的所有帧的内存块的大小和数量，而不只是最近的帧。 累积模式只能在 _key_type_ 等于 `'filename'` 和 `'lineno'` 的情况下使用。

结果将按以下值从大到小排序: `Statistic.size`, `Statistic.count` 然后是 `Statistic.traceback`。

traceback_limit¶
~~~
    

~~~
保存在 `traces` 的回溯中的帧的最大数量：当快照被保存时 `get_traceback_limit()` 的结果。

traces¶
~~~
    

~~~
由 Python 分配的所有内存块的追踪: `Trace` 实例的序列。

该序列的顺序是未定义的。 请使用 `Snapshot.statistics()` 方法来获取统计信息的已排序列表。

### 统计¶

_class _tracemalloc.Statistic¶
~~~
    

~~~
统计内存分配

`Snapshot.statistics()` 返回 `Statistic` 实例的列表。.

参见 `StatisticDiff` 类。

count¶
~~~
    

~~~
内存块数（`整形`）。

size¶
~~~
    

~~~
以字节数表示的内存块总计大小 (`int`)。

traceback¶
~~~
    

~~~
内存块分配位置的回溯，`Traceback` 实例。

### StatisticDiff¶

_class _tracemalloc.StatisticDiff¶
~~~
    

~~~
在旧的和新的 `Snapshot` 实例之间内存分配上的统计差异。

`Snapshot.compare_to()` 返回一个 `StatisticDiff` 实例的列表。 另请参看 `Statistic` 类。

count¶
~~~
    

~~~
新快照中的内存块数量 (`int`): 如果在新快照中内存块已被释放则为 `0`。

count_diff¶
~~~
    

~~~
在旧的新的快照之间内存块数量之差 (`int`): 如果在新快照中内存块已被分配则为 `0`。

size¶
~~~
    

~~~
新快速中以字节数表示的内存块总计大小 (`int`): 如果在新快照中内存块已被释放则为 `0`。

size_diff¶
~~~
    

~~~
在旧的新的快照之间以字节数表示的内存块总计大小之差 (`int`): 如果在新快照中内存块已被分配则为 `0`。

traceback¶
~~~
    

~~~
内存块分配位置的回溯，`Traceback` 实例。

### 跟踪¶

_class _tracemalloc.Trace¶
~~~
    

~~~
一个内存块的跟踪信息。

`Snapshot.traces` 属性是一个 `Trace` 实例的序列。

在 3.6 版本发生变更: 增加了 `domain` 属性。

domain¶
~~~
    

~~~
内存块的地址空间 (`int`)。 只读的特征属性。 Read-only property.

tracemalloc 使用 `0` 号域来追踪 Python 的内存分配操作。 C 扩展可以使用其他域来追踪其他资源。extensions can use other domains to trace other resources.

size¶
~~~
    

~~~
以字节数表示的内存块大小 (`int`)。

traceback¶
~~~
    

~~~
内存块分配位置的回溯，`Traceback` 实例。

### 回溯¶

_class _tracemalloc.Traceback¶
~~~
    

~~~
`Frame` 实例的序列将按从最旧的帧到最新的帧排序。

一个回溯包含至少 `1` 个帧。 如果 `tracemalloc` 模块无法获取帧，则会使用文件名 `"<unknown>"` 和行号 `0`。

当保存一个快照时，追踪的回溯被限制为 `get_traceback_limit()` 个帧。 参见 `take_snapshot()` 函数。 回溯的原始帧数存放在 `Traceback.total_nframe` 属性中。 这可以让人了解一个回溯是否因回溯限制而被截断。

`Trace.traceback` 属性是一个 `Traceback` 对象的实例。

在 3.7 版本发生变更: 现在帧的排序是从最旧到最新，而不是从最新到最旧。

total_nframe¶
~~~
    

~~~
在截断之前组成回溯的总帧数。 如果此信息不可用则该属性可被设为 `None`。

在 3.9 版本发生变更: 增加了 `Traceback.total_nframe` 属性。

format( _limit =None_, _most_recent_first =False_)¶
~~~
    

~~~
将回溯格式化为由行组成的列表。 使用 [`linecache`](linecache.md#module-linecache "linecache: Provides random access to individual lines from text files.") 模块从源代码提取行。 如果设置了 _limit_ ，则当 _limit_ 为正值时将格式化 _limit_ 个最新的帧。 在其他情况下，则格式化 `abs(limit)` 个最新的帧。 如果 _most_recent_first_ 为 `True`，则将反转已格式化帧的顺序，首先返回最新的帧而不是最旧的。

类似于 [`traceback.format_tb()`](traceback.md#traceback.format_tb "traceback.format_tb") 函数，不同之处是 `format()` 不包括换行符。

示例:
~~~
    
    
~~~
print("Traceback (most recent call first):")
for line in traceback:
    print(line)
~~~

输出:

    
    
~~~
Traceback (most recent call first):
  File "test.py", line 9
    obj = Object()
  File "test.py", line 12
    tb = tracemalloc.get_object_traceback(f())
~~~

