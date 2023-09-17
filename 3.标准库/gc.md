# `gc` \--- 垃圾回收器接口¶

* * *

此模块提供可选的垃圾回收器的接口，提供的功能包括：关闭收集器、调整收集频率、设置调试选项。它同时提供对回收器找到但是无法释放的不可达对象的访问。由于 Python 使用了带有引用计数的回收器，如果你确定你的程序不会产生循环引用，你可以关闭回收器。可以通过调用 `gc.disable()` 关闭自动垃圾回收。若要调试一个存在内存泄漏的程序，调用 `gc.set_debug(gc.DEBUG_LEAK)` ；需要注意的是，它包含 `gc.DEBUG_SAVEALL` ，使得被垃圾回收的对象会被存放在 gc.garbage 中以待检查。

`gc` 模块提供下列函数：

gc.enable()¶

    

~~~
启用自动垃圾回收

gc.disable()¶
~~~
    

~~~
停用自动垃圾回收

gc.isenabled()¶
~~~
    

~~~
如果启用了自动回收则返回 `True`。

gc.collect( _generation =2_)¶
~~~
    

~~~
若被调用时不包含参数，则启动完全的垃圾回收。可选的参数 _generation_ 可以是一个整数，指明需要回收哪一代（从 0 到 2 ）的垃圾。当参数 _generation_ 无效时，会引发 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。返回发现的不可达对象的数目。

每当运行完整收集或最高代 (2) 收集时，为多个内置类型所维护的空闲列表会被清空。 由于特定类型特别是 [`float`](functions.md#float "float") 的实现，在某些空闲列表中并非所有项都会被释放。

当解释器已经在执行收集任务时调用 `gc.collect()` 的效果是未定义的。

gc.set_debug( _flags_ )¶
~~~
    

~~~
设置垃圾回收器的调试标识位。调试信息会被写入 `sys.stderr` 。此文档末尾列出了各个标志位及其含义；可以使用位操作对多个标志位进行设置以控制调试器。

gc.get_debug()¶
~~~
    

~~~
返回当前调试标识位。

gc.get_objects( _generation =None_)¶
~~~
    

~~~
返回一个收集器所跟踪的所有对象的列表，所返回的列表除外。 如果 _generation_ 不为 None，则只返回收集器所跟踪的属于该生成的对象。

在 3.8 版本发生变更: 新的 _generation_ 形参。

引发一个 [审计事件](sys.md#auditing) `gc.get_objects`，附带参数 `generation`。

gc.get_stats()¶
~~~
    

~~~
返回一个包含三个字典对象的列表，每个字典分别包含对应代的从解释器开始运行的垃圾回收统计数据。字典的键的数目在将来可能发生改变，目前每个字典包含以下内容：

  * `collections` 是该代被回收的次数；

  * `collected` 是该代中被回收的对象总数；

  * `uncollectable` 是在这一代中被发现无法收集的对象总数 （因此被移动到 `garbage` 列表中）。

在 3.4 版本加入.

gc.set_threshold( _threshold0_ [, _threshold1_ [, _threshold2_ ]])¶
~~~
    

~~~
设置垃圾回收阈值（收集频率）。 将 _threshold0_ 设为零会禁用回收。

垃圾回收器把所有对象分类为三代，其依据是对象在多少次垃圾回收后幸存。 新建对象会被放在最年轻代（第 `0` 代）。 如果一个对象在一次垃圾回收后幸存，它会被移入下一个较老代。 由于第 `2` 代是最老代，这一代的对象在一次垃圾回收后仍会保留原样。 为了确定何时要运行，垃圾回收器会跟踪自上一次回收后对象分配和释放的数量。 当分配数量减去释放数量的结果值大于 _threshold0_ 时，垃圾回收就会开始。 初始时只有第 `0` 代会被检查。 如果自第 `1` 代被检查后第 `0` 代已被检查超过 _threshold1_ 次，则第 `1` 也会被检查。 对于第三代来说情况还会更复杂，请参阅 [Collecting the oldest generation](https://devguide.python.org/garbage_collector/#collecting-the-oldest-generation) 来了解详情。

gc.get_count()¶
~~~
    

~~~
将当前回收计数以形为 `(count0, count1, count2)` 的元组返回。

gc.get_threshold()¶
~~~
    

~~~
将当前回收阈值以形为 `(threshold0, threshold1, threshold2)` 的元组返回。

gc.get_referrers( _* objs_)¶
~~~
    

~~~
返回直接引用任意一个 _objs_ 的对象列表。这个函数只定位支持垃圾回收的容器；引用了其它对象但不支持垃圾回收的扩展类型不会被找到。

需要注意的是，已经解除对 _objs_ 引用的对象，但仍存在于循环引用中未被回收时，仍然会被作为引用者出现在返回的列表当中。若要获取当前正在引用 _objs_ 的对象，需要调用 `collect()` 然后再调用 `get_referrers()` 。

警告

在使用 `get_referrers()` 返回的对象时必须要小心，因为其中一些对象可能仍在构造中因此处于暂时的无效状态。不要把 `get_referrers()` 用于调试以外的其它目的。

引发一个 [审计事件](sys.md#auditing) `gc.get_referrers`，附带参数 `objs`。

gc.get_referents( _* objs_)¶
~~~
    

~~~
返回被任意一个参数中的对象直接引用的对象的列表。返回的被引用对象是被参数中的对象的C语言级别方法（若存在） [`tp_traverse`](../c-api/typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 访问到的对象，可能不是所有的实际直接可达对象。只有支持垃圾回收的对象支持 [`tp_traverse`](../c-api/typeobj.md#c.PyTypeObject.tp_traverse "PyTypeObject.tp_traverse") 方法，并且此方法只会在需要访问涉及循环引用的对象时使用。因此，可以有以下例子：一个整数对其中一个参数是直接可达的，这个整数有可能出现或不出现在返回的结果列表当中。

引发一个 [审计事件](sys.md#auditing) `gc.get_referents`，附带参数 `objs`。

gc.is_tracked( _obj_ )¶
~~~
    

~~~
当对象正在被垃圾回收器监控时返回 `True` ，否则返回 `False` 。一般来说，原子类的实例不会被监控，而非原子类（如容器、用户自定义的对象）会被监控。然而，会有一些特定类型的优化以便减少垃圾回收器在简单实例（如只含有原子性的键和值的字典）上的消耗。
~~~
    
    
~~~shell
>>> gc.is_tracked(0)
False
>>> gc.is_tracked("a")
False
>>> gc.is_tracked([])
True
>>> gc.is_tracked({})
False
>>> gc.is_tracked({"a": 1})
False
>>> gc.is_tracked({"a": []})
True
~~~

在 3.1 版本加入.

gc.is_finalized( _obj_ )¶

    

~~~
如果给定对象已被垃圾回收器终结则返回 `True`，否则返回 `False`。
~~~
    
    
~~~shell
>>> x = None
>>> class Lazarus:
...     def __del__(self):
...         global x
...         x = self
...
>>> lazarus = Lazarus()
>>> gc.is_finalized(lazarus)
False
>>> del lazarus
>>> gc.is_finalized(x)
True
~~~

在 3.9 版本加入.

gc.freeze()¶

    

~~~
冻结由垃圾回收器追踪的所有对象；将它们移至永久世代并在所有未来的回收操作中忽略它们。

如果一个进程将执行 `fork()` 而不执行 `exec()`，则在子进程中避免不必要的写入时拷贝将最大化内存共享并减少总体内存使用。 这需要同时在父进程的内存页中避免创建已释放的“空洞”并确保在子进程中的 GC 回收不会触及源自父进程的长寿对象的 `gc_refs` 计数器。 要同时达成这两个目标，请在父进程中尽早调用 `gc.disable()`，在 `fork()` 之前调用 `gc.freeze()`，并在子进程中尽早调用 `gc.enable()`。

在 3.7 版本加入.

gc.unfreeze()¶
~~~
    

~~~
解冻永久代中的对象，并将它们放回到年老代中。

在 3.7 版本加入.

gc.get_freeze_count()¶
~~~
    

~~~
返回永久代中的对象数量。

在 3.7 版本加入.

提供以下变量仅供只读访问（你可以修改但不应该重绑定它们）：

gc.garbage¶
~~~
    

~~~
一个回收器发现不可达而又无法被释放的对象（不可回收对象）列表。 从 Python 3.4 开始，该列表在大多数时候都应该是空的，除非使用了含有非 `NULL` `tp_del` 空位的 C 扩展类型的实例。

如果设置了 `DEBUG_SAVEALL` ，则所有不可访问对象将被添加至该列表而不会被释放。

在 3.2 版本发生变更: 当 [interpreter shutdown](../glossary.md#term-interpreter-shutdown) 即解释器关闭时，若此列表非空，会产生 [`ResourceWarning`](exceptions.md#ResourceWarning "ResourceWarning") ，即资源警告，在默认情况下此警告不会被提醒。如果设置了 `DEBUG_UNCOLLECTABLE` ，所有无法被回收的对象会被打印。

在 3.4 版本发生变更: 根据 [**PEP 442**](https://peps.python.org/pep-0442/)，具有 [`__del__()`](../reference/datamodel.md#object.__del__ "object.__del__") 方法的对象不会再由 `gc.garbage` 来处理。

gc.callbacks¶
~~~
    

~~~
在垃圾回收器开始前和完成后会被调用的一系列回调函数。这些回调函数在被调用时使用两个参数： _phase_ 和 _info_ 。

_phase_ 可为以下两值之一：

> "start": 垃圾回收即将开始。
>
> "stop": 垃圾回收已结束。

_info_ is a dict providing more information for the callback. The following keys are currently defined:

> "generation"（代） ：正在被回收的最久远的一代。
>
> "collected"（已回收的 ）: 当*phase* 为 "stop" 时，被成功回收的对象的数目。
>
> "uncollectable"（不可回收的）: 当 _phase_ 为 "stop" 时，不能被回收并被放入 `garbage` 的对象的数目。

应用程序可以把他们自己的回调函数加入此列表。主要的使用场景有：

> 统计垃圾回收的数据，如：不同代的回收频率、回收所花费的时间。
>
> 使应用程序可以识别和清理他们自己的在 `garbage` 中的不可回收类型的对象。

在 3.3 版本加入.

以下常量被用于 `set_debug()` ：

gc.DEBUG_STATS¶
~~~
    

~~~
在回收完成后打印统计信息。当回收频率设置较高时，这些信息会比较有用。

gc.DEBUG_COLLECTABLE¶
~~~
    

~~~
当发现可回收对象时打印信息。

gc.DEBUG_UNCOLLECTABLE¶
~~~
    

~~~
打印找到的不可回收对象的信息（指不能被回收器回收的不可达对象）。这些对象会被添加到 `garbage` 列表中。

在 3.2 版本发生变更: 当 [interpreter shutdown](../glossary.md#term-interpreter-shutdown) 时，即解释器关闭时，若 `garbage` 列表中存在对象，这些对象也会被打印输出。

gc.DEBUG_SAVEALL¶
~~~
    

~~~
设置后，所有回收器找到的不可达对象会被添加进 _garbage_ 而不是直接被释放。这在调试一个内存泄漏的程序时会很有用。

gc.DEBUG_LEAK¶
~~~
    

~~~
