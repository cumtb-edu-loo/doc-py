# `contextlib` \--- 为 `with`语句上下文提供的工具¶

**源代码** [Lib/contextlib.py](https://github.com/python/cpython/tree/3.12/Lib/contextlib.py)

* * *

此模块为涉及 [`with`](8.%20复合语句.md#with) 语句的常见任务提供了实用的工具。更多信息请参见 [上下文管理器类型](stdtypes.md#typecontextmanager) 和 [with 语句上下文管理器](3.%20数据模型.md#context-managers)。

## 工具¶

提供的函数和类：

_class _contextlib.AbstractContextManager¶

    

~~~
一个为实现了 [`object.__enter__()`](../reference/datamodel.md#object.__enter__ "object.__enter__") 与 [`object.__exit__()`](../reference/datamodel.md#object.__exit__ "object.__exit__") 的类提供的 [abstract base class](../glossary.md#term-abstract-base-class)。为 [`object.__enter__()`](../reference/datamodel.md#object.__enter__ "object.__enter__") 提供的一个默认实现是返回 `self` 而 [`object.__exit__()`](../reference/datamodel.md#object.__exit__ "object.__exit__") 是一个默认返回 `None` 的抽象方法。 参见 [上下文管理器类型](stdtypes.md#typecontextmanager) 的定义。

在 3.6 版本加入.

_class _contextlib.AbstractAsyncContextManager¶
~~~
    

~~~
一个为实现了 [`object.__aenter__()`](../reference/datamodel.md#object.__aenter__ "object.__aenter__") 与 [`object.__aexit__()`](../reference/datamodel.md#object.__aexit__ "object.__aexit__") 的类提供的 [abstract base class](../glossary.md#term-abstract-base-class)。 为 [`object.__aenter__()`](../reference/datamodel.md#object.__aenter__ "object.__aenter__") 提供的一个默认实现是返回 `self` 而 [`object.__aexit__()`](../reference/datamodel.md#object.__aexit__ "object.__aexit__") 是一个默认返回 `None` 的抽象方法。 参见 [异步上下文管理器](../reference/datamodel.md#async-context-managers) 的定义。

在 3.7 版本加入.

@contextlib.contextmanager¶
~~~
    

~~~
这个函数是一个 [decorator](../glossary.md#term-decorator) ，它可以定义一个支持 [`with`](../reference/compound_stmts.md#with) 语句上下文管理器的工厂函数， 而不需要创建一个类或区 `__enter__()` 与 `__exit__()` 方法。

尽管许多对象原生支持使用 with 语句，但有些需要被管理的资源并不是上下文管理器，并且没有实现 `close()` 方法而不能使用 `contextlib.closing` 。

下面是一个抽象的示例，展示如何确保正确的资源管理:
~~~
    
    
~~~
from contextlib import contextmanager

@contextmanager
def managed_resource(*args, **kwds):
    # Code to acquire resource, e.g.:
    resource = acquire_resource(*args, **kwds)
    try:
        yield resource
    finally:
        # Code to release resource, e.g.:
        release_resource(resource)
~~~

随后可以这样使用此函数:

    
    
~~~shell
>>> with managed_resource(timeout=3600) as resource:
...     # Resource is released at the end of this block,
...     # even if code in the block raises an exception
~~~

被装饰的函数在被调用时，必须返回一个 [generator](../glossary.md#term-generator) 迭代器。 这个迭代器必须只 yield 一个值出来，这个值会被用在 [`with`](8.%20复合语句.md#with) 语句中，绑定到 `as` 后面的变量，如果给定了的话。

当生成器发生 yield 时，嵌套在 [`with`](8.%20复合语句.md#with) 语句中的语句体会被执行。 语句体执行完毕离开之后，该生成器将被恢复执行。 如果在该语句体中发生了未处理的异常，则该异常会在生成器发生 yield 时重新被引发。 因此，你可以使用 [`try`](8.%20复合语句.md#try)...[`except`](8.%20复合语句.md#except)...[`finally`](8.%20复合语句.md#finally) 语句来捕获该异常（如果有的话），或确保进行了一些清理。 如果仅出于记录日志或执行某些操作（而非完全抑制异常）的目的捕获了异常，生成器必须重新引发该异常。 否则生成器的上下文管理器将向 `with` 语句指示该异常已经被处理，程序将立即在 `with` 语句之后恢复并继续执行。

`contextmanager()` 使用 `ContextDecorator` 因此它创建的上下文管理器不仅可以用在 [`with`](8.%20复合语句.md#with) 语句中，还可以用作一个装饰器。当它用作一个装饰器时，每一次函数调用时都会隐式创建一个新的生成器实例（这使得 `contextmanager()` 创建的上下文管理器满足了支持多次调用以用作装饰器的需求，而非“一次性”的上下文管理器）。

在 3.2 版本发生变更: `ContextDecorator` 的使用。

@contextlib.asynccontextmanager¶

    

~~~
> 与 `contextmanager()` 类似，但创建的是 [asynchronous context manager](../reference/datamodel.md#async-context-managers) 。
>
> 这个函数是一个 [decorator](../glossary.md#term-decorator) ，它可以定义一个支持 [`async with`](../reference/compound_stmts.md#async-with) 语句的异步上下文管理器的工厂函数， 而不需要创建一个类或区分 `__aenter__()` 与 `__aexit__()` 方法。它必须被作用在一个 [asynchronous generator](../glossary.md#term-asynchronous-generator) 函数上
>
> 一个简单的示例：
>  
>  
>     from contextlib import asynccontextmanager
>  
>     @asynccontextmanager
>     async def get_connection():
>         conn = await acquire_db_connection()
>         try:
>             yield conn
>         finally:
>             await release_db_connection(conn)
>  
>     async def get_all_users():
>         async with get_connection() as conn:
>             return conn.query('SELECT ...')
>  
>
> 在 3.7 版本加入.
>
> 使用 `asynccontextmanager()` 定义的上下文管理器可以用作装饰器，也可以在 [`async with`](../reference/compound_stmts.md#async-with) 语句中使用。
>  
>  
>     import time
>     from contextlib import asynccontextmanager
>  
>     @asynccontextmanager
>     async def timeit():
>         now = time.monotonic()
>         try:
>             yield
>         finally:
>             print(f'it took {time.monotonic() - now}s to run')
>  
>     @timeit()
>     async def main():
>         # ... async code ...
>  
>
> 用作装饰器时，每次函数调用都会隐式创建一个新的生成器实例。这使得由 `asynccontextmanager()` 创建的 “一次性” 上下文管理器能够满足作为装饰器所需要的支持多次调用的要求。

在 3.10 版本发生变更: 使用 `asynccontextmanager()` 创建的异步上下文管理器可以用作装饰器。

contextlib.closing( _thing_ )¶
~~~
    

~~~
返回一个在语句块执行完成时关闭 _things_ 的上下文管理器。这基本上等价于：
~~~
    
    
~~~
from contextlib import contextmanager

@contextmanager
def closing(thing):
    try:
        yield thing
    finally:
        thing.close()
~~~

并允许你编写这样的代码：

    
    
~~~
from contextlib import closing
from urllib.request import urlopen

with closing(urlopen('https://www.python.org')) as page:
    for line in page:
        print(line)
~~~

而无需显式地关闭 `page` 。 即使发生错误，在退出 [`with`](8.%20复合语句.md#with) 语句块时， `page.close()` 也同样会被调用。

contextlib.aclosing( _thing_ )¶

    

~~~
返回一个在语句块执行完成时调用 `aclose()` 方法来关闭 _things_ 的异步上下文管理器。这基本上等价于：
~~~
    
    
~~~
from contextlib import asynccontextmanager

@asynccontextmanager
async def aclosing(thing):
    try:
        yield thing
    finally:
        await thing.aclose()
~~~

重要的是，`aclosing()` 支持在异步生成器因遭遇 [`break`](7.%20简单语句.md#break) 或异常而提前退出时对其执行确定性的清理。 例如:

    
    
~~~
from contextlib import aclosing

async with aclosing(my_generator()) as values:
    async for value in values:
        if value == 42:
            break
~~~

此模块将确保生成器的异步退出代码在与其迭代相同的上下文中执行（这样异常和上下文变量将能按预期工作，并且退出代码不会在其所依赖的某些任务的生命期结束后继续运行）。

在 3.10 版本加入.

contextlib.nullcontext( _enter_result =None_)¶

    

~~~
返回一个从 `__enter__` 返回 _enter_result_ 的上下文管理器，除此之外不执行任何操作。它旨在用于可选上下文管理器的一种替代，例如：
~~~
    
    
~~~python
def myfunction(arg, ignore_exceptions=False):
    if ignore_exceptions:
        # Use suppress to ignore all exceptions.
        cm = contextlib.suppress(Exception)
    else:
        # Do not ignore any exceptions, cm has no effect.
        cm = contextlib.nullcontext()
    with cm:
        # Do something
~~~

一个使用 _enter_result_ 的例子：

    
    
~~~python
def process_file(file_or_path):
    if isinstance(file_or_path, str):
        # If string, open file
        cm = open(file_or_path)
    else:
        # Caller is responsible for closing file
        cm = nullcontext(file_or_path)

    with cm as file:
        # Perform processing on the file
~~~

它也可以替代 [asynchronous context managers](3.%20数据模型.md#async-context-managers) ：

    
    
~~~
async def send_http(session=None):
    if not session:
        # If no http session, create it with aiohttp
        cm = aiohttp.ClientSession()
    else:
        # Caller is responsible for closing the session
        cm = nullcontext(session)

    async with cm as session:
        # Send http requests with session
~~~

在 3.7 版本加入.

在 3.10 版本发生变更: 增加了对 [asynchronous context manager](../glossary.md#term-asynchronous-context-manager) 的支持。

contextlib.suppress( _* exceptions_)¶

    

~~~
返回一个当指定的异常在 `with` 语句体中发生时会屏蔽它们然后从 `with` 语句结束后的第一条语言开始恢复执行的上下文管理器。

与完全抑制异常的任何其他机制一样，该上下文管理器应当只用来抑制非常具体的错误，并确保该场景下静默地继续执行程序是通用的正确做法。

例如：
~~~
    
    
~~~
from contextlib import suppress

with suppress(FileNotFoundError):
    os.remove('somefile.tmp')

with suppress(FileNotFoundError):
    os.remove('someotherfile.tmp')
~~~

这段代码等价于：

    
    
~~~
try:
    os.remove('somefile.tmp')
except FileNotFoundError:
    pass

try:
    os.remove('someotherfile.tmp')
except FileNotFoundError:
    pass
~~~

该上下文管理器是 reentrant 。

如果 `with` 语句块内的代码引发了一个 [`ExceptionGroup`](3.标准库/exceptions.md#ExceptionGroup "ExceptionGroup")，将从该分组中移除被屏蔽的异常。 如果该分组中有任何异常未被屏蔽，则会重新引发一个包含它们的分组。

在 3.4 版本加入.

在 3.12 版本发生变更: `suppress` 现在支持屏蔽作为一个 [`ExceptionGroup`](3.标准库/exceptions.md#ExceptionGroup "ExceptionGroup") 的组成部分被引发的异常。

contextlib.redirect_stdout( _new_target_ )¶

    

~~~
用于将 [`sys.stdout`](sys.md#sys.stdout "sys.stdout") 临时重定向到一个文件或类文件对象的上下文管理器。

该工具给已有的将输出硬编码写到 stdout 的函数或类提供了额外的灵活性。

例如， [`help()`](functions.md#help "help") 的输出通常会被发送到 _sys.stdout_ 。 你可以通过将输出重定向到一个 [`io.StringIO`](io.md#io.StringIO "io.StringIO") 对象来将该输出捕获到字符串。 替换的流是由 `__enter__` 返回的因此可以被用作 [`with`](../reference/compound_stmts.md#with) 语句的目标:
~~~
    
    
~~~
with redirect_stdout(io.StringIO()) as f:
    help(pow)
s = f.getvalue()
~~~

如果要把 [`help()`](functions.md#help "help") 的输出写到磁盘上的一个文件，重定向该输出到一个常规文件：

    
    
~~~
with open('help.txt', 'w') as f:
    with redirect_stdout(f):
        help(pow)
~~~

如果要把 [`help()`](functions.md#help "help") 的输出写到 _sys.stderr_ ：

    
    
~~~
with redirect_stdout(sys.stderr):
    help(pow)
~~~

需要注意的点在于， [`sys.stdout`](3.标准库/sys.md#sys.stdout "sys.stdout") 的全局副作用意味着此上下文管理器不适合在库代码和大多数多线程应用程序中使用。它对子进程的输出没有影响。不过对于许多工具脚本而言，它仍然是一个有用的方法。

该上下文管理器是 reentrant 。

在 3.4 版本加入.

contextlib.redirect_stderr( _new_target_ )¶

    

~~~
与 `redirect_stdout()` 类似，不过是将 [`sys.stderr`](sys.md#sys.stderr "sys.stderr") 重定向到一个文件或类文件对象。

该上下文管理器是 reentrant 。

在 3.5 版本加入.

contextlib.chdir( _path_ )¶
~~~
    

~~~
用于改变当前工作目录的非并行安全的上下文管理器。 由于这会改变一个全局状态，即工作目录，因此它不适合在大多数线程或异步上下文中使用。 它也不适合大多数非线性的代码执行，比如生成器，在此类代码中程序的执行会被临时性挂起 -- 除非明确希望如此，当该上下文管理器被激活时你不应执行 yield 语句。

这是一个对 [`chdir()`](os.md#os.chdir "os.chdir") 的简单包装器，它会在进入时改变当前工作目录并在退出时恢复。

该上下文管理器是 reentrant 。

在 3.11 版本加入.

_class _contextlib.ContextDecorator¶
~~~
    

~~~
一个使上下文管理器能用作装饰器的基类。

与往常一样，继承自 `ContextDecorator` 的上下文管理器必须实现 `__enter__` 与 `__exit__` 。即使用作装饰器， `__exit__` 依旧会保持可能的异常处理。

`ContextDecorator` 被用在 `contextmanager()` 中，因此你自然获得了这项功能。

`ContextDecorator` 的示例:
~~~
    
    
~~~
from contextlib import ContextDecorator

class mycontext(ContextDecorator):
    def __enter__(self):
        print('Starting')
        return self

    def __exit__(self, *exc):
        print('Finishing')
        return False
~~~

随后可以这样使用该类:

    
    
~~~shell
>>> @mycontext()
... def function():
...     print('The bit in the middle')
...
>>> function()
Starting
The bit in the middle
Finishing

>>> with mycontext():
...     print('The bit in the middle')
...
Starting
The bit in the middle
Finishing
~~~

这个改动只是针对如下形式的一个语法糖：

    
    
~~~python
def f():
    with cm():
        # Do stuff
~~~

`ContextDecorator` 使得你可以这样改写：

    
    
~~~
@cm()
def f():
    # Do stuff
~~~

这能清楚地表明， `cm` 作用于整个函数，而不仅仅是函数的一部分（同时也能保持不错的缩进层级）。

现有的上下文管理器即使已经有基类，也可以使用 `ContextDecorator` 作为混合类进行扩展：

    
    
~~~
from contextlib import ContextDecorator

class mycontext(ContextBaseClass, ContextDecorator):
    def __enter__(self):
        return self

    def __exit__(self, *exc):
        return False
~~~

备注

由于被装饰的函数必须能够被多次调用，因此对应的上下文管理器必须支持在多个 [`with`](8.%20复合语句.md#with) 语句中使用。如果不是这样，则应当使用原来的具有显式 `with` 语句的形式使用该上下文管理器。

在 3.2 版本加入.

_class _contextlib.AsyncContextDecorator¶

    

~~~
和 `ContextDecorator` 类似，但是用于异步函数。

`AsyncContextDecorator` 的示例:
~~~
    
    
~~~
from asyncio import run
from contextlib import AsyncContextDecorator

class mycontext(AsyncContextDecorator):
    async def __aenter__(self):
        print('Starting')
        return self

    async def __aexit__(self, *exc):
        print('Finishing')
        return False
~~~

随后可以这样使用该类:

    
    
~~~shell
>>> @mycontext()
... async def function():
...     print('The bit in the middle')
...
>>> run(function())
Starting
The bit in the middle
Finishing

>>> async def function():
...    async with mycontext():
...         print('The bit in the middle')
...
>>> run(function())
Starting
The bit in the middle
Finishing
~~~

在 3.10 版本加入.

_class _contextlib.ExitStack¶

    

~~~
该上下文管理器的设计目标是使得在编码中组合其他上下文管理器和清理函数更加容易，尤其是那些可选的或由输入数据驱动的上下文管理器。

例如，通过一个如下的 with 语句可以很容易处理一组文件：
~~~
    
    
~~~
with ExitStack() as stack:
    files = [stack.enter_context(open(fname)) for fname in filenames]
    # All opened files will automatically be closed at the end of
    # the with statement, even if attempts to open files later
    # in the list raise an exception
~~~

`__enter__()` 方法返回 `ExitStack` 实例，并且不会执行额外的操作。

每个实例维护一个注册了一组回调的栈，这些回调在实例关闭时以相反的顺序被调用（显式或隐式地在 [`with`](8.%20复合语句.md#with) 语句的末尾）。请注意，当一个栈实例被垃圾回收时，这些回调将 _不会_ 被隐式调用。

通过使用这个基于栈的模型，那些通过 `__init__` 方法获取资源的上下文管理器（如文件对象）能够被正确处理。

由于注册的回调函数是按照与注册相反的顺序调用的，因此最终的行为就像多个嵌套的 [`with`](8.%20复合语句.md#with) 语句用在这些注册的回调函数上。这个行为甚至扩展到了异常处理：如果内部的回调函数抑制或替换了异常，则外部回调收到的参数是基于该更新后的状态得到的。

这是一个相对底层的 API，它负责正确处理栈里回调退出时依次展开的细节。它为相对高层的上下文管理器提供了一个合适的基础，使得它能根据应用程序的需求使用特定方式操作栈。

在 3.3 版本加入.

enter_context( _cm_ )¶

    

~~~
进入一个新的上下文管理器并将其 `__exit__()` 方法添加到回调栈中。 返回值是该上下文管理器自己的 `__enter__()` 方法的输出结果。

这些上下文管理器可能会屏蔽异常就如当它们作为 [`with`](../reference/compound_stmts.md#with) 语句的一部分直接使用时通常表现的行为一样。

在 3.11 版本发生变更: 如果 _cm_ 不是上下文管理器则会引发 [`TypeError`](exceptions.md#TypeError "TypeError") 而不是 [`AttributeError`](exceptions.md#AttributeError "AttributeError")。

push( _exit_ )¶
~~~
    

~~~
将一个上下文管理器的 `__exit__()` 方法添加到回调栈。

由于 `__enter__` _没有_ 被发起调用，此方法可以被用来通过一个上下文管理器自己的 `__exit__()` 覆盖 `__enter__()` 的部分实现。

如果传递了一个不是上下文管理器的对象，此方法将假定它是一个回调 具有与上下文管理器的 `__exit__()` 方法相同的签名的回调并会直接将其添加到回调栈中。

通过返回真值，这些回调可以通过与上下文管理器 `__exit__()` 方法相同的方式屏蔽异常。

传入的对象将被该函数返回，允许此方法作为函数装饰器使用。

callback( _callback_ , _/_ , _* args_, _** kwds_)¶
~~~
    

~~~
接受一个任意的回调函数和参数并将其添加到回调栈。

与其他方法不同，通过此方式添加的回调无法屏蔽异常（因为异常的细节不会被传递给它们）。

传入的回调将被该函数返回，允许此方法作为函数装饰器使用。

pop_all()¶
~~~
    

~~~
将回调栈传输到一个新的 `ExitStack` 实例并返回它。 此操作不会发起调用任何回调 —— 作为替代，现在当新栈被关闭时它们将（显式地或是在一条 [`with`](../reference/compound_stmts.md#with) 语句结束时隐式地）被发起调用。

例如，一组文件可以像下面这样以“一个都不能少”的操作方式被打开:
~~~
    
    
~~~
with ExitStack() as stack:
    files = [stack.enter_context(open(fname)) for fname in filenames]
    # Hold onto the close method, but don't call it yet.
    close_files = stack.pop_all().close
    # If opening any file fails, all previously opened files will be
    # closed automatically. If all files are opened successfully,
    # they will remain open even after the with statement ends.
    # close_files() can then be invoked explicitly to close them all.
~~~

close()¶

    

~~~
立即展开回调栈，按注册时的相反顺序发起调用其中的回调。 对于任何已注册的上下文管理器和退出回调，传入的参数将表明没有发生异常。

_class _contextlib.AsyncExitStack¶
~~~
    

~~~
一个 [异步上下文管理器](../reference/datamodel.md#async-context-managers)，类似于 `ExitStack`，它支持组合同步和异步上下文管理器，并拥有针对清理逻辑的协程。

`close()` 方法没有被实现，使用 `aclose()` 替代。

_coroutine _enter_async_context( _cm_ )¶
~~~
    

~~~
与 `enter_context()` 相似，但是需要一个异步上下文管理器。

在 3.11 版本发生变更: 如果 _cm_ 不是异步上下文管理器则会引发 [`TypeError`](exceptions.md#TypeError "TypeError") 而不是 [`AttributeError`](exceptions.md#AttributeError "AttributeError")。

push_async_exit( _exit_ )¶
~~~
    

~~~
与 `push()` 相似，但是需要一个异步上下文管理器或协程函数。

push_async_callback( _callback_ , _/_ , _* args_, _** kwds_)¶
~~~
    

~~~
与 `callback()` 相似，但是需要一个协程函数。

_coroutine _aclose()¶
~~~
    

~~~
和 `close()` 相似，但是它正确处理了可等待对象。

从 `asynccontextmanager()` 的例子继续：
~~~
    
    
~~~
async with AsyncExitStack() as stack:
    connections = [await stack.enter_async_context(get_connection())
        for i in range(5)]
    # All opened connections will automatically be released at the end of
    # the async with statement, even if attempts to open a connection
    # later in the list raise an exception.
~~~

在 3.7 版本加入.

## 例子和配方¶

本节描述了一些用于有效利用 `contextlib` 所提供的工具的示例和步骤说明。

### 支持可变数量的上下文管理器¶

`ExitStack` 的主要应用场景已在该类的文档中给出：在单个 [`with`](8.%20复合语句.md#with) 语句中支持可变数量的上下文管理器和其他清理操作。 这个可变性可以来自通过用户输入驱动所需的上下文管理器数量（例如打开用户所指定的文件集），或者来自将某些上下文管理器作为可选项。

    
    
~~~
with ExitStack() as stack:
    for resource in resources:
        stack.enter_context(resource)
    if need_special_resource():
        special = acquire_special_resource()
        stack.callback(release_special_resource, special)
    # Perform operations that use the acquired resources
~~~

如上所示，`ExitStack` 还让使用 [`with`](8.%20复合语句.md#with) 语句来管理任意非原生支持上下文管理协议的资源变得相当容易。

### 捕获 `__enter__` 方法产生的异常¶

有时人们会想要从 `__enter__` 方法的实现中捕获异常，而 _不会_ 无意中捕获来自 [`with`](8.%20复合语句.md#with) 语句体或上下文管理器的 `__exit__` 方法的异常。 通过使用 `ExitStack` 可以将上下文管理协议中的步骤稍微分开以允许这样做:

    
    
~~~
stack = ExitStack()
try:
    x = stack.enter_context(cm)
except Exception:
    # handle __enter__ exception
else:
    with stack:
        # Handle normal case
~~~

实际上需要这样做很可能表明下层的 API 应该提供一个直接的资源管理接口以便与 [`try`](8.%20复合语句.md#try)/[`except`](8.%20复合语句.md#except)/[`finally`](8.%20复合语句.md#finally) 语句配合使用，但并不是所有的 API 在这方面都设计得很好。 当上下文管理器是所提供的唯一资源管理 API 时，则 `ExitStack` 可以让处理各种无法在 [`with`](8.%20复合语句.md#with) 语句中直接处理的情况变得更为容易。

### 在一个 `__enter__` 方法的实现中进行清理¶

正如 `ExitStack.push()` 的文档中所指出，如果在 `__enter__()` 实现中的后续步骤失败则此方法在清理已分配的资源时将很有用处。

下面是为一个上下文管理器做这件事的例子，该上下文管理器可接受资源获取和释放函数，以及可选的验证函数，并将它们映射到上下文管理协议:

    
    
~~~
from contextlib import contextmanager, AbstractContextManager, ExitStack

class ResourceManager(AbstractContextManager):

    def __init__(self, acquire_resource, release_resource, check_resource_ok=None):
        self.acquire_resource = acquire_resource
        self.release_resource = release_resource
        if check_resource_ok is None:
            def check_resource_ok(resource):
                return True
        self.check_resource_ok = check_resource_ok

    @contextmanager
    def _cleanup_on_error(self):
        with ExitStack() as stack:
            stack.push(self)
            yield
            # The validation check passed and didn't raise an exception
            # Accordingly, we want to keep the resource, and pass it
            # back to our caller
            stack.pop_all()

    def __enter__(self):
        resource = self.acquire_resource()
        with self._cleanup_on_error():
            if not self.check_resource_ok(resource):
                msg = "Failed validation for {!r}"
                raise RuntimeError(msg.format(resource))
        return resource

    def __exit__(self, *exc_details):
        # We don't need to duplicate any of our resource release logic
        self.release_resource()
~~~

### 替换任何对 `try-finally` 和旗标变量的使用¶

一种你有时将看到的模式是 `try-finally` 语句附带一个旗标变量来指明 `finally` 子句体是否要被执行。 在其最简单的形式中（它无法仅仅通过改用一条 `except` 子句来预先处理），看起来会是这样:

    
    
~~~
cleanup_needed = True
try:
    result = perform_operation()
    if result:
        cleanup_needed = False
finally:
    if cleanup_needed:
        cleanup_resources()
~~~

就如任何基于 `try` 语句的代码一样，这可能会导致开发和审查方面的问题，因为设置代码和清理代码最终可能会被任意长的代码部分所分隔。

`ExitStack` 将允许选择在一条 `with` 语句末尾注册一个用于执行的回调的替代方式，等以后再决定是否跳过该回调的执行:

    
    
~~~
from contextlib import ExitStack

with ExitStack() as stack:
    stack.callback(cleanup_resources)
    result = perform_operation()
    if result:
        stack.pop_all()
~~~

这允许在事先显式地指明预期的清理行为，而不需要一个单独的旗标变量。

如果某个应用程序大量使用此模式，则可以通过使用一个较小的辅助类来进一步地简化它:

    
    
~~~
from contextlib import ExitStack

class Callback(ExitStack):
    def __init__(self, callback, /, *args, **kwds):
        super().__init__()
        self.callback(callback, *args, **kwds)

    def cancel(self):
        self.pop_all()

with Callback(cleanup_resources) as cb:
    result = perform_operation()
    if result:
        cb.cancel()
~~~

如果资源清理操作尚未完善地捆绑到一个独立的函数中，则仍然可以使用 `ExitStack.callback()` 的装饰器形式来提前声明资源清理:

    
    
~~~
from contextlib import ExitStack

with ExitStack() as stack:
    @stack.callback
    def cleanup_resources():
        ...
    result = perform_operation()
    if result:
        stack.pop_all()
~~~

受装饰器协议工作方式的影响，以此方式声明的回调函数无法接受任何形参。 作为替代，任何要释放的资源必须作为闭包变量来访问。

### 将上下文管理器作为函数装饰器使用¶

`ContextDecorator` 类允许将上下文管理器作为函数装饰器使用，而不仅在 `with` 语句块中使用。

例如，有时将函数或语句组包装在一个可以跟踪进入和退出时间的记录器中是很有用的。 与其为任务同时编写函数装饰器和上下文管理器，不如继承 `ContextDecorator` 在一个定义中同时提供这两种能力:

    
    
~~~
from contextlib import ContextDecorator
import logging

logging.basicConfig(level=logging.INFO)

class track_entry_and_exit(ContextDecorator):
    def __init__(self, name):
        self.name = name

    def __enter__(self):
        logging.info('Entering: %s', self.name)

    def __exit__(self, exc_type, exc, exc_tb):
        logging.info('Exiting: %s', self.name)
~~~

这个类的实例既可以被用作上下文管理器：

    
    
~~~
with track_entry_and_exit('widget loader'):
    print('Some time consuming activity goes here')
    load_widget()
~~~

也可以被用作函数装饰器：

    
    
~~~
@track_entry_and_exit('widget loader')
def activity():
    print('Some time consuming activity goes here')
    load_widget()
~~~

使用上下文管理器作为函数装饰器时，有一个额外限制：没有办法得到 `__enter__()` 的返回值。如果需要返回值，你仍然需要显式使用 `with` 语句。

参见

[**PEP 343**](https://peps.python.org/pep-0343/) \- "with" 语句

    

~~~
Python [`with`](../reference/compound_stmts.md#with) 语句的规范描述、背景和示例。

## 单独使用，可重用并可重进入的上下文管理器¶

大多数上下文管理器的编写方式意味着它们只能在一个 [`with`](../reference/compound_stmts.md#with) 语句中被有效使用一次。 这些一次性使用的上下文管理器必须在每次被使用时重新创建 —— 试图第二次使用它们将会触发异常或是不能正确工作。

这个常见的限制意味着通常来说都建议在 [`with`](../reference/compound_stmts.md#with) 语句开头上下文管理器被使用的位置直接创建它们（如下面所有的使用示例所显示的）。

文件是一个高效的单次使用上下文管理器的例子，因为第一个 [`with`](../reference/compound_stmts.md#with) 语句将关闭文件，防止任何后续的使用该文件对象的 IO 操作。

使用 `contextmanager()` 创建的上下文管理器也是单次使用的上下文管理器，并会在试图第二次使用它们时报告下层生成器无法执行产生操作:
~~~
    
    
~~~shell
>>> from contextlib import contextmanager
>>> @contextmanager
... def singleuse():
...     print("Before")
...     yield
...     print("After")
...
>>> cm = singleuse()
>>> with cm:
...     pass
...
Before
After
>>> with cm:
...     pass
...
Traceback (most recent call last):
    ...
RuntimeError: generator didn't yield
~~~

### 重进入上下文管理器¶

更复杂的上下文管理器可以“重进入”。 这些上下文管理器不但可以被用于多个 [`with`](8.%20复合语句.md#with) 语句中，还可以被用于已经在使用同一个上下文管理器的 `with` 语句 _内部_ 。

[`threading.RLock`](threading.md#threading.RLock "threading.RLock") 是一个可重入上下文管理器的例子，`suppress()`, `redirect_stdout()` 和 `chdir()` 也是。 下面是一个非常简单的使用重入的示例:

    
    
~~~shell
>>> from contextlib import redirect_stdout
>>> from io import StringIO
>>> stream = StringIO()
>>> write_to_stream = redirect_stdout(stream)
>>> with write_to_stream:
...     print("This is written to the stream rather than stdout")
...     with write_to_stream:
...         print("This is also written to the stream")
...
>>> print("This is written directly to stdout")
This is written directly to stdout
>>> print(stream.getvalue())
This is written to the stream rather than stdout
This is also written to the stream
~~~

现实世界的可重入例子更可能涉及到多个函数的相互调用因此会比这个例子要复杂得多。

还要注意可重入与线程安全 _不是_ 一回事。 举例来说，`redirect_stdout()` 肯定不是线程安全的，因为它会通过将 [`sys.stdout`](3.标准库/sys.md#sys.stdout "sys.stdout") 绑定到一个不同的流对系统状态做了全局性的修改。

### 可重用的上下文管理器¶

与单次使用和可重入上下文管理器都不同的还有“可重用”上下文管理器（或者，使用完全显式的表达应为“可重用，但不可重入”上下文管理器，因为可重入上下文管理器也会是可重用的）。 这些上下文管理器支持多次使用，但如果特定的上下文管理器实例已经在包含它的 with 语句中被使用过则将失败（或者不能正确工作）。

[`threading.Lock`](threading.md#threading.Lock "threading.Lock") 是一个可重用，但是不可重入的上下文管理器的例子（对于可重入锁，则有必要使用 [`threading.RLock`](threading.md#threading.RLock "threading.RLock") 来代替）。

另一个可重用，但不可重入的上下文管理器的例子是 `ExitStack`，因为它在离开任何 with 语句时会发起调用 _所有_ 当前已注册的回调，不论回调是在哪里添加的:

    
    
~~~shell
>>> from contextlib import ExitStack
>>> stack = ExitStack()
>>> with stack:
...     stack.callback(print, "Callback: from first context")
...     print("Leaving first context")
...
Leaving first context
Callback: from first context
>>> with stack:
...     stack.callback(print, "Callback: from second context")
...     print("Leaving second context")
...
Leaving second context
Callback: from second context
>>> with stack:
...     stack.callback(print, "Callback: from outer context")
...     with stack:
...         stack.callback(print, "Callback: from inner context")
...         print("Leaving inner context")
...     print("Leaving outer context")
...
Leaving inner context
Callback: from inner context
Callback: from outer context
Leaving outer context
~~~

正如这个例子的输出所显示的，在多个 with 语句中重用一个单独的栈对象可以正确工作，但调试嵌套它们就将导致栈在最内层的 with 语句结束时被清空，这不大可能是符合期望的行为。

使用单独的 `ExitStack` 实例而不是重复使用一个实例可以避免此问题:

    
    
~~~shell
>>> from contextlib import ExitStack
>>> with ExitStack() as outer_stack:
...     outer_stack.callback(print, "Callback: from outer context")
...     with ExitStack() as inner_stack:
...         inner_stack.callback(print, "Callback: from inner context")
...         print("Leaving inner context")
...     print("Leaving outer context")
...
Leaving inner context
Callback: from inner context
Leaving outer context
Callback: from outer context
~~~

