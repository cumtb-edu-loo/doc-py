# `bdb` \--- 调试器框架¶

**源代码:** [Lib/bdb.py](https://github.com/python/cpython/tree/3.12/Lib/bdb.py)

* * *

`bdb` 模块处理基本的调试器函数，例如设置中断点或通过调试器来管理执行。

定义了以下异常：

_exception _bdb.BdbQuit¶

    

~~~
由 `Bdb` 类引发用于退出调试器的异常。

`bdb` 模块还定义了两个类:

_class _bdb.Breakpoint( _self_ , _file_ , _line_ , _temporary =False_, _cond =None_, _funcname =None_)¶
~~~
    

~~~
这个类实现了临时性中断点、忽略计数、禁用与（重新）启用，以及条件设置等。

中断点通过一个名为 `bpbynumber` 的列表基于数字并通过 `bplist` 基于 `(file, line)` 对进行索引。 前者指向一个 `Breakpoint` 类的单独实例。 后者指向一个由这种实例组成的列表，因为在每一行中可能存在多个中断点。

当创建一个中断点时，它所关联的 `文件名` 应当为规范形式。 如果定义了 `funcname`，则当该函数的第一行被执行时将增加一次中断点 `命中` 次数。 `有条件的` 中断点将总是会计入 `命中` 次数。

`Breakpoint` 的实例具有下列方法:

deleteMe()¶
~~~
    

~~~
从关联到文件/行的列表中删除此中断点。 如果它是该位置上的最后一个中断点，还将删除相应的文件/行条目。

enable()¶
~~~
    

~~~
将此中断点标记为启用。

disable()¶
~~~
    

~~~
将此中断点标记为禁用。

bpformat()¶
~~~
    

~~~
返回一个带有关于此中断点的所有信息的，格式良好的字符串:

  * 中断点编号 。

  * 临时状态（删除或保留）。

  * 文件/行位置。

  * 中断条件

  * 要忽略的次数。

  * 命中的次数。

在 3.2 版本加入.

bpprint( _out =None_)¶
~~~
    

~~~
将 `bpformat()` 的输出打印到文件 _out_ ，或者如果为 `None` 则打印到标准输出。, to standard output.

`Breakpoint` 实例具有以下属性:

file¶
~~~
    

~~~
`Breakpoint` 的文件名。

line¶
~~~
    

~~~
`Breakpoint` 在 `file` 中的行号。

temporary¶
~~~
    

~~~
如果 (file, line) 上的 `Breakpoint` 是临时性的则返回 True。

cond¶
~~~
    

~~~
在 (file, line) 上对 `Breakpoint` 求值的条件。

funcname¶
~~~
    

~~~
用于定义在进入函数时一个 `Breakpoint` 是否命中的函数的名称。

enabled¶
~~~
    

~~~
如果 `Breakpoint` 被启用则为 True。

bpbynumber¶
~~~
    

~~~
一个 `Breakpoint` 单独实例的数字索引。

bplist¶
~~~
    

~~~
以 (`file`, `line`) 元组作为索引的 `Breakpoint` 实例的字典。

ignore¶
~~~
    

~~~
忽略一个 `Breakpoint` 的次数。

hits¶
~~~
    

~~~
命中一个 `Breakpoint` 的次数统计。

_class _bdb.Bdb( _skip =None_)¶
~~~
    

~~~
`Bdb` 类是作为通用的 Python 调试器基类。

这个类负责追踪工具的细节；所派生的类应当实现用户交互。 标准调试器类 ([`pdb.Pdb`](pdb.md#pdb.Pdb "pdb.Pdb")) 就是一个例子。

如果给出了 _skip_ 参数，它必须是一个包含 glob 风格的模块名称模式的可迭代对象。 调试器将不会步进到来自与这些模式相匹配的模块的帧。 一个帧是否会被视为来自特定的模块是由帧的 `__name__` 全局变量来确定的。

在 3.1 版本加入: _skip_ 参数。

`Bdb` 的以下方法通常不需要被重载。

canonic( _filename_ )¶
~~~
    

~~~
返回 _filename_ 的规范形式。

对于真实的文件名称，此规范形式是一个依赖于具体操作系统的，[`大小写规范的`](os.path.md#os.path.normcase "os.path.normcase") [`绝对路径`](os.path.md#os.path.abspath "os.path.abspath")。 在交互模式下生成的带有尖括号的 _filename_ ，如 `"<stdin>"`，会被不加修改地返回。

reset()¶
~~~
    

~~~
将 `botframe`, `stopframe`, `returnframe` 和 `quitting` 属性设置为准备开始调试的值。

trace_dispatch( _frame_ , _event_ , _arg_ )¶
~~~
    

~~~
此函数被安装为被调试帧的追踪函数。 它的返回值是新的追踪函数（在大多数情况下就是它自身）。

默认实现会决定如何分派帧，这取决于即将被执行的事件的类型（作为字符串传入）。 _event_ 可以是下列值之一:

  * `"line"`: 一个新的代码行即将被执行。

  * `"call"`: 一个函数即将被调用，或者进入了另一个代码块。

  * `"return"`: 一个函数或其他代码块即将返回。

  * `"exception"`: 一个异常已发生。

  * `"c_call"`: 一个 C 函数即将被调用。

  * `"c_return"`: 一个 C 函数已返回。

  * `"c_exception"`: 一个 C 函数引发了异常。

对于 Python 事件，调用了专门的函数（见下文）。 对于 C 事件，不执行任何操作。

_arg_ 形参取决于之前的事件。

请参阅 [`sys.settrace()`](sys.md#sys.settrace "sys.settrace") 的文档了解追踪函数的更多信息。 对于代码和帧对象的详情，请参考 [标准类型层级结构](../reference/datamodel.md#types)。

dispatch_line( _frame_ )¶
~~~
    

~~~
如果调试器应当在当前行上停止，则发起调用 `user_line()` 方法（该方法应当在子类中被重载）。 如果设置了 `Bdb.quitting` 旗标（可以通过 `user_line()` 设置）则将引发 `BdbQuit` 异常。 返回一个对 `trace_dispatch()` 方法的引用以便在该作用域内进一步地追踪。

dispatch_call( _frame_ , _arg_ )¶
~~~
    

~~~
如果调试器应当在此函数调用上停止，则发起调用 `user_call()` 方法（该方法应当在子类中被重载）。 如果设置了 `Bdb.quitting` 旗标（可以通过 `user_call()` 设置）则将引发 `BdbQuit` 异常。 返回一个对 `trace_dispatch()` 方法的引用以便在该作用域内进一步地追踪。

dispatch_return( _frame_ , _arg_ )¶
~~~
    

~~~
如果调试器应当在此函数返回时停止，则发起调用 `user_return()` 方法（该方法应当在子类中被重载）。 如果设置了 `Bdb.quitting` 旗标（可以通过 `user_return()` 设置）则将引发 `BdbQuit` 异常。 返回一个对 `trace_dispatch()` 方法的引用以便在该作用域内进一步地追踪。

dispatch_exception( _frame_ , _arg_ )¶
~~~
    

~~~
如果调试器应当在此异常上停止，则发起调用 `user_exception()` 方法（该方法应当在子类中被重载）。 如果设置了 `Bdb.quitting` 旗标（可以通过 `user_exception()` 设置）则将引发 `BdbQuit` 异常。 返回一个对 `trace_dispatch()` 方法的引用以便在该作用域内进一步地追踪。

通常情况下派生的类不会重载下列方法，但是如果想要重新定义停止和中断点的定义则可能会重载它们。

is_skipped_line( _module_name_ )¶
~~~
    

~~~
如果 _module_name_ 匹配到任何跳过模式则返回 True。

stop_here( _frame_ )¶
~~~
    

~~~
如果 _frame_ 在栈的起始帧之下则返回 True。

break_here( _frame_ )¶
~~~
    

~~~
如果该行有生效的中断点则返回 True。

检测某行或某函数是否存在中断点且处于生效状态。 基于来自 `effective()` 的信息删除临时中断点。

break_anywhere( _frame_ )¶
~~~
    

~~~
如果存在任何针对 _frame_ 的文件名的中断点则返回 True。

派生的类应当重载这些方法以获取调试器操作的控制权。

user_call( _frame_ , _argument_list_ )¶
~~~
    

~~~
如果中断可能在被调用的函数内停止则会从 `dispatch_call()` 来调用。

user_line( _frame_ )¶
~~~
    

~~~
当 `stop_here()` 或 `break_here()` 返回 `True` 时则会从 `dispatch_line()` 来调用。

user_return( _frame_ , _return_value_ )¶
~~~
    

~~~
当 `stop_here()` 返回 `True` 时则会从 `dispatch_return()` 来调用。

user_exception( _frame_ , _exc_info_ )¶
~~~
    

~~~
当 `stop_here()` 返回 `True` 时则会从 `dispatch_exception()` 来调用。

do_clear( _arg_ )¶
~~~
    

~~~
处理当一个中断点属于临时性中断点时是否必须要移除它。

此方法必须由派生类来实现。

派生类与客户端可以调用以下方法来影响步进状态。

set_step()¶
~~~
    

~~~
在一行代码之后停止。

set_next( _frame_ )¶
~~~
    

~~~
在给定的帧以内或以下的下一行停止。

set_return( _frame_ )¶
~~~
    

~~~
当从给定的帧返回时停止。

set_until( _frame_ , _lineno =None_)¶
~~~
    

~~~
在 _lineno_ 行大于当前所到达的行或者在从当前帧返回时停止。

set_trace([ _frame_ ])¶
~~~
    

~~~
从 _frame_ 开始调试。 如果未指定 _frame_ ，则从调用者的帧开始调试。

set_continue()¶
~~~
    

~~~
仅在中断点上或是当结束时停止。 如果不存在中断点，则将系统追踪函数设为 `None`。

set_quit()¶
~~~
    

~~~
将 `quitting` 属性设为 `True`。 这将在对某个 `dispatch_*()` 方法的下一次调用中引发 `BdbQuit`。

派生的类和客户端可以调用下列方法来操纵中断点。 如果出现错误则这些方法将返回一个包含错误消息的字符串，或者如果一切正常则返回 `None`。

set_break( _filename_ , _lineno_ , _temporary =False_, _cond =None_, _funcname =None_)¶
~~~
    

~~~
设置一个新的中断点。 如果对于作为参数传入的 _filename_ 不存在 _lineno_ ，则返回一条错误消息。 _filename_ 应为规范的形式，如在 `canonic()` 方法中描述的。

clear_break( _filename_ , _lineno_ )¶
~~~
    

~~~
删除位于 _filename_ 和 _lineno_ 的中断点。 如果未设置过中断点，则返回一条错误消息。

clear_bpbynumber( _arg_ )¶
~~~
    

~~~
删除 `Breakpoint.bpbynumber` 中索引号为 _arg_ 的中断点。 如果 _arg_ 不是数字或超出范围，则返回一条错误消息。

clear_all_file_breaks( _filename_ )¶
~~~
    

~~~
删除位于 _filename_ 的所有中断点。 如果未设置过中断点，则返回一条错误消息。

clear_all_breaks()¶
~~~
    

~~~
删除所有现存的中断点。 如果未设置过中断点，则返回一条错误消息。

get_bpbynumber( _arg_ )¶
~~~
    

~~~
返回由指定数字所指明的中断点。 如果 _arg_ 是一个字符串，它将被转换为一个数字。 如果 _arg_ 不是一个表示数字的字符串，如果给定的中断点不存在或者已被删除，则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

在 3.2 版本加入.

get_break( _filename_ , _lineno_ )¶
~~~
    

~~~
如果 _filename_ 中的 _lineno_ 上有中断点则返回 True。

get_breaks( _filename_ , _lineno_ )¶
~~~
    

~~~
返回 _filename_ 中在 _lineno_ 上的所有中断点，或者如果未设置任何中断点则返回一个空列表。

get_file_breaks( _filename_ )¶
~~~
    

~~~
返回 _filename_ 中的所有中断点，或者如果未设置任何中断点则返回一个空列表。

get_all_breaks()¶
~~~
    

~~~
返回已设置的所有中断点。

派生类与客户端可以调用以下方法来获取一个代表栈回溯信息的数组结构。

get_stack( _f_ , _t_ )¶
~~~
    

~~~
返回一个栈回溯中 (frame, lineno) 元组的列表，及一个大小值。

最近调用的帧将排在列表的末尾。 大小值即调试器被发起调用所在帧之下的帧数量。

format_stack_entry( _frame_lineno_ , _lprefix =': '_)¶
~~~
    

~~~
返回一个字符串，其内容为有关以 `(frame, lineno)` 元组表示的特定栈条目的信息。 返回的字符串包含:

  * 包含该帧的规范文件名。

  * 函数名称或 `"<lambda>"`。

  * 输入参数。

  * 返回值。

  * 代码的行（如果存在）。

以下两个方法可由客户端调用以使用一个调试器来调试一条以字符串形式给出的 [statement](../glossary.md#term-statement)。

run( _cmd_ , _globals =None_, _locals =None_)¶
~~~
    

~~~
调试一条通过 [`exec()`](functions.md#exec "exec") 函数执行的语句。 _globals_ 默认为 `__main__.__dict__`， _locals_ 默认为 _globals_ 。

runeval( _expr_ , _globals =None_, _locals =None_)¶
~~~
    

~~~
调试一条通过 [`eval()`](functions.md#eval "eval") 函数执行的表达式。 _globals_ 和 _locals_ 的含义与在 `run()` 中的相同。

runctx( _cmd_ , _globals_ , _locals_ )¶
~~~
    

~~~
为了保证向下兼容性。 调用 `run()` 方法。

runcall( _func_ , _/_ , _* args_, _** kwds_)¶
~~~
    

~~~
调试一个单独的函数调用，并返回其结果。

最后，这个模块定义了以下函数:

bdb.checkfuncname( _b_ , _frame_ )¶
~~~
    

~~~
如果要在此中断则返回 True，具体取决于 `Breakpoint` _b_ 的设置方式。

如果是通过行号设置的，它将检查 `b.line` 是否与 _frame_ 中的行一致。 如果中断点是通过 `函数名称` 设置的，则必须检查是否位于正确的 _帧_ (正确的函数) 以及是否位于其中第一个可执行的行。

bdb.effective( _file_ , _line_ , _frame_ )¶
~~~
    

~~~
返回 `(active breakpoint, delete temporary flag)` 或 `(None, None)` 作为目标中断点。

_激活的中断点_ 是 `bplist` 中对应 (`file`, `line`) 的第一个 `已启用的` 条目 (它必须存在)，对应的 `checkfuncname()` 为 True，并且没有 False `cond` 或为正值的 `ignore` 计数。 _flag_ 表示临时中断点应当被删除，它仅在 `cond` 无法被求值时 (在此情况下，`ignore` 计数会被忽略) 才会为 False。

如果不存在这样的条目，则返回 (None, None)。

bdb.set_trace()¶
~~~
    

~~~
