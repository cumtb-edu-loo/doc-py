# `test` \--- Python 回归测试包¶

备注

`test` 包只供 Python 内部使用。它的记录是为了让 Python 的核心开发者受益。我们不鼓励在 Python 标准库之外使用这个包，因为这里提到的代码在 Python 的不同版本之间可能会改变或被删除而不另行通知。

* * *

`test` 包包含了 Python 的所有回归测试，以及 `test.support` 和 `test.regrtest` 模块。 `test.support` 用于增强你的测试，而 `test.regrtest` 驱动测试套件。

`test`包中每个名字以 ``test_`` 开头的模块都是一个特定模块或功能的测试套件。所有新的测试应该使用 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 或 [`doctest`](doctest.md#module-doctest "doctest: Test pieces of code within docstrings.") 模块编写。一些旧的测试是使用“传统”的测试风格编写的，即比较打印出来的输出到 `sys.stdout`；这种测试风格被认为是过时的。

参见

模块 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.")

    

~~~
编写 PyUnit 回归测试.

[`doctest`](doctest.md#module-doctest "doctest: Test pieces of code within docstrings.") \--- 文档测试模块
~~~
    

~~~
嵌入到文档字符串的测试。

## 为 `test` 包编写单元测试¶

使用 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 模块的测试最好是遵循一些准则。 其中一条是测试模块的名称要以 `test_` 打头并以被测试模块的名称结尾。 测试模块中的测试方法应当以 `test_` 打头并以该方法所测试的内容的说明结尾。 这很有必要因为这样测试驱动程序就会将这些方法识别为测试方法。 此外，该方法不应当包括任何文档字符串。 应当使用注释 (例如 `# Tests function returns only True or False`) 来为测试方法提供文档说明。 这样做是因为文档字符串如果存在则会被打印出来因此无法指明正在运行哪个测试。

有一个基本模板经常会被使用:
~~~
    
    
~~~
import unittest
from test import support

class MyTestCase1(unittest.TestCase):

    # Only use setUp() and tearDown() if necessary

    def setUp(self):
        ... code to execute in preparation for tests ...

    def tearDown(self):
        ... code to execute to clean up after tests ...

    def test_feature_one(self):
        # Test feature one.
        ... testing code ...

    def test_feature_two(self):
        # Test feature two.
        ... testing code ...

    ... more test methods ...

class MyTestCase2(unittest.TestCase):
    ... same structure as MyTestCase1 ...

... more test classes ...

if __name__ == '__main__':
    unittest.main()
~~~

这种代码模式允许测试套件由 `test.regrtest` 运行，作为支持 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") CLI 的脚本单独运行，或者通过 `python -m unittest` CLI 来运行。

回归测试的目标是尝试破坏代码。 这引出了一些需要遵循的准则:

  * 测试套件应当测试所有的类、函数和常量。 这不仅包括要向外界展示的外部 API 也包括“私有”的代码。

  * 白盒测试（在编写测试时检查被测试的代码）是最推荐的。 黑盒测试（只测试已发布的用户接口）因不够完整而不能确保所有边界和边缘情况都被测试到。

  * 确保所有可能的值包括无效的值都被测试到。 这能确保不仅全部的有效值都可被接受而且不适当的值也能被正确地处理。

  * 消耗尽可能多的代码路径。 测试发生分支的地方从而调整输入以确保通过代码采取尽可能多的不同路径。

  * 为受测试的代码所发现的任何代码缺陷添加明确的测试。 这将确保如果代码在将来被改变错误也不会再次出现。

  * 确保在你的测试完成后执行清理（例如关闭并删除所有临时文件）。

  * 如果某个测试依赖于操作系统上的特定条件那么要在尝试测试之前先验证该条件是否已存在。

  * 尽可能少地导入模块并尽可能快地完成操作。 这可以最大限度地减少测试的外部依赖性并且还可以最大限度地减少导入模块带来的附带影响所导致的异常行为。

  * 尝试最大限度地重用代码。 在某些情况下，测试结果会因使用不同类型的输入这样的小细节而变化。 可通过一个指定输入的类来子类化一个基本测试类来最大限度地减少重复代码:
    
        class TestFuncAcceptsSequencesMixin:
    
        func = mySuperWhammyFunction
    
        def test_func(self):
            self.func(self.arg)
    
    class AcceptLists(TestFuncAcceptsSequencesMixin, unittest.TestCase):
        arg = [1, 2, 3]
    
    class AcceptStrings(TestFuncAcceptsSequencesMixin, unittest.TestCase):
        arg = 'abc'
    
    class AcceptTuples(TestFuncAcceptsSequencesMixin, unittest.TestCase):
        arg = (1, 2, 3)
    

当使用这种模式时，请记住所有继承自 [`unittest.TestCase`](unittest.md#unittest.TestCase "unittest.TestCase") 的类都会作为测试来运行。 上面例子中的 `Mixin` 类没有任何数据所以其本身是无法运行的，因此它不是继承自 [`unittest.TestCase`](unittest.md#unittest.TestCase "unittest.TestCase")。

参见

测试驱动的开发

    

~~~
Kent Beck 所著的阐述在实现代码之前编写驱动的书。

## 使用命令行界面运行测试¶

通过使用 [`-m`](../using/cmdline.md#cmdoption-m) 选项 `test` 包可以作为脚本运行以驱动 Python 的回归测试套件: **python -m test** 。 在内部，它使用 `test.regrtest`；之前 Python 版本所使用的 **python -m test.regrtest** 调用仍然有效。 运行该脚本自身会自动开始运行 `test` 包中的所有回归测试。 它通过在包中查找所有名称以 `test_` 打头的模块，导入它们，并在有 `test_main()` 函数时执行它或是在没有 `test_main` 时通过 unittest.TestLoader.loadTestsFromModule 载入测试。 要执行的测试的名称也可以被传递给脚本。 指定一个单独的回归测试 ( **python -m test test_spam** ) 将使输出最小化并且只打印测试通过或失败的消息。

直接运行 `test` 将允许设置哪些资源可供测试使用。 你可以通过使用 `-u` 命令行选项来做到这一点。 指定 `all` 作为 `-u` 选项的值将启用所有可能的资源: **python -m test -uall** 。 如果只需要一项资源（这是更为常见的情况），可以在 `all` 之后加一个以逗号分隔的列表来指明不需要的资源。 命令 **python -m test -uall,-audio,-largefile** 将运行 `test` 并使用除 `audio` 和 `largefile` 资源之外的所有资源。 要查看所有资源的列表和更多的命令行选项，请运行 **python -m test -h** 。

另外一些执行回归测试的方式依赖于执行测试所在的系统平台。 在 Unix 上，你可以在构建 Python 的最高层级目录中运行 **make test** 。 在 Windows 上，在你的 `PCbuild` 目录中执行 **rt.bat** 将运行所有的回归测试。

# `test.support` \--- 针对 Python 测试套件的工具¶

`test.support` 模块提供了对 Python 的回归测试套件的支持。

备注

`test.support` 不是一个公用模块。 这篇文档是为了帮助 Python 开发者编写测试。 此模块的 API 可能被改变而不顾及发行版本之间的向下兼容性问题。

此模块定义了以下异常:

_exception _test.support.TestFailed¶
~~~
    

~~~
当一个测试失败时将被引发的异常。 此异常已被弃用而应改用基于 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 的测试以及 [`unittest.TestCase`](unittest.md#unittest.TestCase "unittest.TestCase") 的断言方法。

_exception _test.support.ResourceDenied¶
~~~
    

~~~
[`unittest.SkipTest`](unittest.md#unittest.SkipTest "unittest.SkipTest") 的子类。 当一个资源（例如网络连接）不可用时将被引发。 由 `requires()` 函数所引发。

`test.support` 模块定义了以下常量:

test.support.verbose¶
~~~
    

~~~
当启用详细输出时为 `True`。 当需要有关运行中的测试的更详细信息时应当被选择。 _verbose_ 是由 `test.regrtest` 来设置的。

test.support.is_jython¶
~~~
    

~~~
如果所运行的解释器是 Jython 时为 `True`。

test.support.is_android¶
~~~
    

~~~
如果系统是 Android 时为 `True`。

test.support.unix_shell¶
~~~
    

~~~
如果系统不是 Windows 时则为 shell 的路径；否则为 `None`。

test.support.LOOPBACK_TIMEOUT¶
~~~
    

~~~
使用网络服务器监听网络本地环回接口如 `127.0.0.1` 的测试的以秒为单位的超时值。

该超时长到足以防止测试失败：它要考虑客户端和服务器可能会在不同线程甚至不同进程中运行。

该超时应当对于 [`socket.socket`](socket.md#socket.socket "socket.socket") 的 [`connect()`](socket.md#socket.socket.connect "socket.socket.connect"), [`recv()`](socket.md#socket.socket.recv "socket.socket.recv") 和 [`send()`](socket.md#socket.socket.send "socket.socket.send") 方法都足够长。

其默认值为5秒。

参见 `INTERNET_TIMEOUT`。

test.support.INTERNET_TIMEOUT¶
~~~
    

~~~
发往互联网的网络请求的以秒为单位的超时值。

该超时短到足以避免测试在互联网请求因任何原因被阻止时等待太久。

通常使用 `INTERNET_TIMEOUT` 的超时不应该将测试标记为失败，而是跳过测试：参见 `transient_internet()`。

其默认值是1分钟。

参见 `LOOPBACK_TIMEOUT`。

test.support.SHORT_TIMEOUT¶
~~~
    

~~~
如果测试耗时“太长”而要将测试标记为失败的以秒为单位的超时值。

该超时值取决于 regrtest `--timeout` 命令行选项。

如果一个使用 `SHORT_TIMEOUT` 的测试在慢速 buildbots 上开始随机失败，请使用 `LONG_TIMEOUT` 来代替。

其默认值为30秒。

test.support.LONG_TIMEOUT¶
~~~
    

~~~
用于检测测试何时挂起的以秒为单位的超时值。

它的长度足够在最慢的 Python buildbot 上降低测试失败的风险。 如果测试耗时“过长”也不应当用它将该测试标记为失败。 此超时值依赖于 regrtest `--timeout` 命令行选项。

其默认值为5分钟。

另请参见 `LOOPBACK_TIMEOUT`, `INTERNET_TIMEOUT` 和 `SHORT_TIMEOUT`。

test.support.PGO¶
~~~
    

~~~
当测试对 PGO 没有用处时设置是否要跳过测试。

test.support.PIPE_MAX_SIZE¶
~~~
    

~~~
一个通常大于下层 OS 管道缓冲区大小的常量，以产生写入阻塞。

test.support.Py_DEBUG¶
~~~
    

~~~
如果 Python 编译时定义了 `Py_DEBUG` 宏则为 True: Python 是否 [以调试模式编译](../using/configure.md#debug-build) ([`./configure --with-pydebug`](../using/configure.md#cmdoption-with-pydebug))。

在 3.12 版本加入.

test.support.SOCK_MAX_SIZE¶
~~~
    

~~~
一个通常大于下层 OS 套接字缓冲区大小的常量，以产生写入阻塞。

test.support.TEST_SUPPORT_DIR¶
~~~
    

~~~
设为包含 `test.support` 的最高层级目录。

test.support.TEST_HOME_DIR¶
~~~
    

~~~
设为 test 包的最高层级目录。

test.support.TEST_DATA_DIR¶
~~~
    

~~~
设为 test 包中的 `data` 目录。

test.support.MAX_Py_ssize_t¶
~~~
    

~~~
设为大内存测试的 [`sys.maxsize`](sys.md#sys.maxsize "sys.maxsize")。

test.support.max_memuse¶
~~~
    

~~~
通过 `set_memlimit()` 设为针对大内存测试的内存限制。 受 `MAX_Py_ssize_t` 的限制。

test.support.real_max_memuse¶
~~~
    

~~~
通过 `set_memlimit()` 设为针对大内存测试的内存限制。 不受 `MAX_Py_ssize_t` 的限制。

test.support.MISSING_C_DOCSTRINGS¶
~~~
    

~~~
如果 Python 编译时不带文档字符串（即未定义 `WITH_DOC_STRINGS` 宏）则设为 `True`。 参见 [`configure --without-doc-strings`](../using/configure.md#cmdoption-without-doc-strings) 选项。

另请参阅 `HAVE_DOCSTRINGS` 变量。

test.support.HAVE_DOCSTRINGS¶
~~~
    

~~~
如果函数带有文档字符串则设为 `True`。 参见 [`python -OO`](../using/cmdline.md#cmdoption-O) 选项，该选项会去除在 Python 中实现的函数的文档字符串。

另请参阅 `MISSING_C_DOCSTRINGS` 变量。

test.support.TEST_HTTP_URL¶
~~~
    

~~~
定义用于网络测试的韧性 HTTP 服务器的 URL。

test.support.ALWAYS_EQ¶
~~~
    

~~~
等于任何对象的对象。 用于测试混合类型比较。

test.support.NEVER_EQ¶
~~~
    

~~~
不等于任何对象的对象 (即使是 `ALWAYS_EQ`)。 用于测试混合类型比较。

test.support.LARGEST¶
~~~
    

~~~
大于任何对象的对象（除了其自身）。 用于测试混合类型比较。

test.support.SMALLEST¶
~~~
    

~~~
小于任何对象的对象（除了其自身）。 用于测试混合类型比较。Used to test mixed type comparison.

`test.support` 模块定义了以下函数:

test.support.busy_retry( _timeout_ , _err_msg =None_, _/_ , _*_ , _error =True_)¶
~~~
    

~~~
运行循环体直到以 `break` 停止循环。

在 _timeout_ 秒后，如果 _error_ 为真值则引发 [`AssertionError`](exceptions.md#AssertionError "AssertionError")，或者如果 _error_ 为假值则只停止循环。

示例：
~~~
    
    
~~~
for _ in support.busy_retry(support.SHORT_TIMEOUT):
    if check():
        break
~~~

error=False 的用法示例:

    
    
~~~
for _ in support.busy_retry(support.SHORT_TIMEOUT, error=False):
    if check():
        break
else:
    raise RuntimeError('my custom error')
~~~

test.support.sleeping_retry( _timeout_ , _err_msg =None_, _/_ , _*_ , _init_delay =0.010_, _max_delay =1.0_, _error =True_)¶

    

~~~
应用指数回退的等待策略。

运行循环体直到以 `break` 停止循环。 在每次循环迭代时休眠，但第一次迭代时除外。 每次迭代的休眠延时都将加倍（至多 _max_delay_ 秒）。

请参阅 `busy_retry()` 文档了解相关形参的用法。

在 SHORT_TIMEOUT 秒后引发异常的示例:
~~~
    
    
~~~
for _ in support.sleeping_retry(support.SHORT_TIMEOUT):
    if check():
        break
~~~

error=False 的用法示例:

    
    
~~~
for _ in support.sleeping_retry(support.SHORT_TIMEOUT, error=False):
    if check():
        break
else:
    raise RuntimeError('my custom error')
~~~

test.support.is_resource_enabled( _resource_ )¶

    

~~~
如果 _resource_ 已启用并可用则返回 `True`。 可用资源列表只有当 `test.regrtest` 正在执行测试时才会被设置。

test.support.python_is_optimized()¶
~~~
    

~~~
如果 Python 编译未使用 `-O0` 或 `-Og` 则返回 `True`。

test.support.with_pymalloc()¶
~~~
    

~~~
返回 `_testcapi.WITH_PYMALLOC`。

test.support.requires( _resource_ , _msg =None_)¶
~~~
    

~~~
如果 _resource_ 不可用则引发 `ResourceDenied`。 如果该异常被引发则 _msg_ 为传给 `ResourceDenied` 的参数。 如果被 `__name__` 为 `'__main__'` 的函数调用则总是返回 `True`。 在测试由 `test.regrtest` 执行时使用。

test.support.sortdict( _dict_ )¶
~~~
    

~~~
返回 _dict_ 按键排序的 repr。

test.support.findfile( _filename_ , _subdir =None_)¶
~~~
    

~~~
返回名为 _filename_ 的文件的路径。 如果未找到匹配结果则返回 _filename_ 。 这并不等于失败因为它也算是该文件的路径。

设置 _subdir_ 指明要用来查找文件的相对路径而不是直接在路径目录中查找。

test.support.match_test( _test_ )¶
~~~
    

~~~
确定 _test_ 是否匹配在 `set_match_tests()` 中设置的模式。

test.support.set_match_tests( _accept_patterns =None_, _ignore_patterns =None_)¶
~~~
    

~~~
定义测试文件名和测试方法名的匹配模式用于筛选测试。

test.support.run_unittest( _* classes_)¶
~~~
    

~~~
执行传给函数的 [`unittest.TestCase`](unittest.md#unittest.TestCase "unittest.TestCase") 子类。 此函数会扫描类中带有 `test_` 前缀的方法并单独执行这些测试。

将字符串作为形参传递也是合法的；这些形参应为 `sys.modules` 中的键。 每个被关联的模块将由 `unittest.TestLoader.loadTestsFromModule()` 执行扫描。 这通常出现在以下 `test_main()` 函数中:
~~~
    
    
~~~python
def test_main():
    support.run_unittest(__name__)
~~~

这将运行在指定模块中定义的所有测试。

test.support.run_doctest( _module_ , _verbosity =None_, _optionflags =0_)¶

    

~~~
在给定的 _module_ 上运行 [`doctest.testmod()`](doctest.md#doctest.testmod "doctest.testmod")。 返回 `(failure_count, test_count)`。

如果 _verbosity_ 为 `None`，[`doctest.testmod()`](doctest.md#doctest.testmod "doctest.testmod") 运行时的消息详细程度将设为 `verbose`。 否则，运行时的消息详细程度将设为 `None`。 _optionflags_ 将作为 `optionflags` 传给 [`doctest.testmod()`](doctest.md#doctest.testmod "doctest.testmod")。

test.support.get_pagesize()¶
~~~
    

~~~
获取以字节表示的分页大小。

在 3.12 版本加入.

test.support.setswitchinterval( _interval_ )¶
~~~
    

~~~
将 [`sys.setswitchinterval()`](sys.md#sys.setswitchinterval "sys.setswitchinterval") 设为给定的 _interval_ 。 请为 Android 系统定义一个最小间隔以防止系统挂起。

test.support.check_impl_detail( _** guards_)¶
~~~
    

~~~
使用此检测来保护 CPython 实现专属的测试或者仅在有这些参数保护的实现上运行它们。 此函数将根据主机系统平台的不同返回 `True` 或 `False`。 用法示例:
~~~
    
    
~~~
check_impl_detail()               # Only on CPython (default).
check_impl_detail(jython=True)    # Only on Jython.
check_impl_detail(cpython=False)  # Everywhere except CPython.
~~~

test.support.set_memlimit( _limit_ )¶

    

~~~
针对大内存测试设置 `max_memuse` 和 `real_max_memuse` 的值。

test.support.record_original_stdout( _stdout_ )¶
~~~
    

~~~
存放来自 _stdout_ 的值。 它会在回归测试开始时处理 stdout。

test.support.get_original_stdout()¶
~~~
    

~~~
返回 `record_original_stdout()` 所设置的原始 stdout 或者如果未设置则为 `sys.stdout`。

test.support.args_from_interpreter_flags()¶
~~~
    

~~~
返回在 `sys.flags` 和 `sys.warnoptions` 中重新产生当前设置的命令行参数列表。

test.support.optim_args_from_interpreter_flags()¶
~~~
    

~~~
返回在 `sys.flags` 中重新产生当前优化设置的命令行参数列表。

test.support.captured_stdin()¶

test.support.captured_stdout()¶

test.support.captured_stderr()¶
~~~
    

~~~
使用 [`io.StringIO`](io.md#io.StringIO "io.StringIO") 对象临时替换指定流的上下文管理器。

使用输出流的示例:
~~~
    
    
~~~
with captured_stdout() as stdout, captured_stderr() as stderr:
    print("hello")
    print("error", file=sys.stderr)
assert stdout.getvalue() == "hello\n"
assert stderr.getvalue() == "error\n"
~~~

使用输入流的示例:

    
    
~~~
with captured_stdin() as stdin:
    stdin.write('hello\n')
    stdin.seek(0)
    # call test code that consumes from sys.stdin
    captured = input()
self.assertEqual(captured, "hello")
~~~

test.support.disable_faulthandler()¶

    

~~~
临时禁用 [`faulthandler`](faulthandler.md#module-faulthandler "faulthandler: Dump the Python traceback.") 的上下文管理器。

test.support.gc_collect()¶
~~~
    

~~~
强制收集尽可能多的对象。 这是有必要的因为垃圾回收器并不能保证及时回收资源。 这意味着 `__del__` 方法的调用可能会晚于预期而弱引用的存活长于预期。

test.support.disable_gc()¶
~~~
    

~~~
在进入时禁用垃圾回收器的上下文管理器。 在退出时，垃圾回收器将恢复到先前状态。

test.support.swap_attr( _obj_ , _attr_ , _new_val_ )¶
~~~
    

~~~
上下文管理器用一个新对象来交换一个属性。

用法：
~~~
    
    
~~~
with swap_attr(obj, "attr", 5):
    ...
~~~

这将把 `obj.attr` 设为 5 并在 `with` 语句块内保持，在语句块结束时恢复旧值。 如果 `attr` 不存在于 `obj` 中，它将被创建并在语句块结束时被删除。

旧值 (或者如果不存在旧值则为 `None`) 将被赋给 "as" 子句的目标，如果存在子句的话。

test.support.swap_item( _obj_ , _attr_ , _new_val_ )¶

    

~~~
上下文件管理器用一个新对象来交换一个条目。

用法：
~~~
    
    
~~~
with swap_item(obj, "item", 5):
    ...
~~~

这将把 `obj["item"]` 设为 5 并在 `with` 语句块内保持，在语句块结束时恢复旧值。 如果 `item` 不存在于 `obj` 中，它将被创建并在语句块结束时被删除。

旧值 (或者如果不存在旧值则为 `None`) 将被赋给 "as" 子句的目标，如果存在子句的话。

test.support.flush_std_streams()¶

    

~~~
在 [`sys.stdout`](sys.md#sys.stdout "sys.stdout") 然后又在 [`sys.stderr`](sys.md#sys.stderr "sys.stderr") 上调用 `flush()` 方法。 它可被用来确保日志顺序在写入到 stderr 之前的一致性。

在 3.11 版本加入.

test.support.print_warning( _msg_ )¶
~~~
    

~~~
打印一个警告到 [`sys.__stderr__`](sys.md#sys.__stderr__ "sys.__stderr__")。 将消息格式化为: `f"Warning -- {msg}"`。 如果 _msg_ 包含多行，则为每行添加 `"Warning -- "` 前缀。

在 3.9 版本加入.

test.support.wait_process( _pid_ , _*_ , _exitcode_ , _timeout =None_)¶
~~~
    

~~~
等待直到进程 _pid_ 结束并检查进程退出代码是否为 _exitcode_ 。

如果进程退出代码不等于 _exitcode_ 则引发 [`AssertionError`](exceptions.md#AssertionError "AssertionError")。

如果进程运行时长超过 _timeout_ 秒 (默认为 `SHORT_TIMEOUT`)，则杀死进程并引发 [`AssertionError`](exceptions.md#AssertionError "AssertionError")。 超时特性在 Windows 上不可用。

在 3.9 版本加入.

test.support.calcobjsize( _fmt_ )¶
~~~
    

~~~
返回 [`PyObject`](../c-api/structures.md#c.PyObject "PyObject") 的大小，其结构成员由 _fmt_ 定义。 返回的值包括 Python 对象头的大小和对齐方式。

test.support.calcvobjsize( _fmt_ )¶
~~~
    

~~~
返回 [`PyVarObject`](../c-api/structures.md#c.PyVarObject "PyVarObject") 的大小，其结构成员由 _fmt_ 定义。 返回的值包括 Python 对象头的大小和对齐方式。

test.support.checksizeof( _test_ , _o_ , _size_ )¶
~~~
    

~~~
对于测试用例 _test_ ，断言 _o_ 的 `sys.getsizeof` 加 GC 头的大小等于 _size_ 。

@test.support.anticipate_failure( _condition_ )¶
~~~
    

~~~
一个有条件地用 [`unittest.expectedFailure()`](unittest.md#unittest.expectedFailure "unittest.expectedFailure") 来标记测试的装饰器。 任何对此装饰器的使用都应当具有标识相应追踪事项的有关联注释。

test.support.system_must_validate_cert( _f_ )¶
~~~
    

~~~
一个在 TLS 证书验证失败时跳过被装饰测试的装饰器。

@test.support.run_with_locale( _catstr_ , _* locales_)¶
~~~
    

~~~
一个在不同语言区域下运行函数的装饰器，并在其结束后正确地重置语言区域。 _catstr_ 是字符串形式的语言区域类别 (例如 `"LC_ALL"`)。 传入的 _locales_ 将依次被尝试，并将使用第一个有效的语言区域。

@test.support.run_with_tz( _tz_ )¶
~~~
    

~~~
一个在指定时区下运行函数的装饰器，并在其结束后正确地重置时区。

@test.support.requires_freebsd_version( _* min_version_)¶
~~~
    

~~~
当在 FreeBSD 上运行测试时指定最低版本的装饰器。 如果 FreeBSD 版本号低于指定值，测试将被跳过。

@test.support.requires_linux_version( _* min_version_)¶
~~~
    

~~~
当在 Linux 上运行测试时指定最低版本的装饰器。 如果 Linux 版本号低于指定值，测试将被跳过。

@test.support.requires_mac_version( _* min_version_)¶
~~~
    

~~~
当在 macOS 上运行测试时指定最低版本的装饰器。 如果 macOS 版本号低于指定值，测试将被跳过。

@test.support.requires_IEEE_754¶
~~~
    

~~~
用于在非 non-IEEE 754 平台上跳过测试的装饰器。

@test.support.requires_zlib¶
~~~
    

~~~
用于当 [`zlib`](zlib.md#module-zlib "zlib: Low-level interface to compression and decompression routines compatible with gzip.") 不存在时跳过测试的装饰器。

@test.support.requires_gzip¶
~~~
    

~~~
用于当 [`gzip`](gzip.md#module-gzip "gzip: Interfaces for gzip compression and decompression using file objects.") 不存在时跳过测试的装饰器。

@test.support.requires_bz2¶
~~~
    

~~~
用于当 [`bz2`](bz2.md#module-bz2 "bz2: Interfaces for bzip2 compression and decompression.") 不存在时跳过测试的装饰器。

@test.support.requires_lzma¶
~~~
    

~~~
用于当 [`lzma`](lzma.md#module-lzma "lzma: A Python wrapper for the liblzma compression library.") 不存在时跳过测试的装饰器。

@test.support.requires_resource( _resource_ )¶
~~~
    

~~~
用于当 _resource_ 不可用时跳过测试的装饰器。

@test.support.requires_docstrings¶
~~~
    

~~~
用于仅当 `HAVE_DOCSTRINGS` 时才运行测试的装饰器。

@test.support.requires_limited_api¶
~~~
    

~~~
设置仅在 [受限 C API](../c-api/stable.md#limited-c-api) 可用时运行测试的装饰器。

@test.support.cpython_only¶
~~~
    

~~~
表示仅适用于 CPython 的测试的装饰器。

@test.support.impl_detail( _msg =None_, _** guards_)¶
~~~
    

~~~
用于在 _guards_ 上发起调用 `check_impl_detail()` 的装饰器。 如果调用返回 `False`，则使用 _msg_ 作为跳过测试的原因。

@test.support.no_tracing¶
~~~
    

~~~
用于在测试期间临时关闭追踪的装饰器。

@test.support.refcount_test¶
~~~
    

~~~
用于涉及引用计数的测试的装饰器。 如果测试不是由 CPython 运行则该装饰器不会运行测试。 在测试期间会取消设置任何追踪函数以由追踪函数导致的意外引用计数。

@test.support.bigmemtest( _size_ , _memuse_ , _dry_run =True_)¶
~~~
    

~~~
用于大内存测试的装饰器。

_size_ 是测试所请求的大小（以任意的，由测试解读的单位。） _memuse_ 是测试的每单元字节数，或是对它的良好估计。 例如，一个需要两个字节缓冲区，每个缓冲区 4 GiB，则可以用 `@bigmemtest(size=_4G, memuse=2)` 来装饰。

_size_ 参数通常作为额外参数传递给被测试的方法。 如果 _dry_run_ 为 `True`，则传给测试方法的值可能少于所请求的值。 如果 _dry_run_ 为 `False`，则意味着当当未指定 `-M` 时测试将不支持虚拟运行。

@test.support.bigaddrspacetest¶
~~~
    

~~~
用于填充地址空间的测试的装饰器。

test.support.check_syntax_error( _testcase_ , _statement_ , _errtext =''_, _*_ , _lineno =None_, _offset =None_)¶
~~~
    

~~~
用于通过尝试编译 _statement_ 来测试 _statement_ 中的语法错误。 _testcase_ 是测试的 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 实例。 _errtext_ 是应当匹配所引发的 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError") 的字符串表示形式的正则表达式。 如果 _lineno_ 不为 `None`，则与异常所在的行进行比较。 如果 _offset_ 不为 `None`，则与异常的偏移量进行比较。

test.support.open_urlresource( _url_ , _* args_, _** kw_)¶
~~~
    

~~~
打开 _url_ 。 如果打开失败，则引发 `TestFailed`。

test.support.reap_children()¶
~~~
    

~~~
只要有子进程启动就在 `test_main` 的末尾使用此函数。 这将有助于确保没有多余的子进程（僵尸）存在占用资源并在查找引用泄漏时造成问题。

test.support.get_attribute( _obj_ , _name_ )¶
~~~
    

~~~
获取一个属性，如果引发了 [`AttributeError`](exceptions.md#AttributeError "AttributeError") 则会引发 [`unittest.SkipTest`](unittest.md#unittest.SkipTest "unittest.SkipTest")。

test.support.catch_unraisable_exception()¶
~~~
    

~~~
使用 [`sys.unraisablehook()`](sys.md#sys.unraisablehook "sys.unraisablehook") 来捕获不可引发的异常的上下文管理器。

存储异常值 (`cm.unraisable.exc_value`) 会创建一个引用循环。 引用循环将在上下文管理器退出时被显式地打破。

存储对象 (`cm.unraisable.object`) 如果被设置为一个正在最终化的对象则可以恢复它。 退出上下文管理器将清除已存在对象。

用法：
~~~
    
    
~~~
with support.catch_unraisable_exception() as cm:
    # code creating an "unraisable exception"
    ...

    # check the unraisable exception: use cm.unraisable
    ...

# cm.unraisable attribute no longer exists at this point
# (to break a reference cycle)
~~~

在 3.8 版本加入.

test.support.load_package_tests( _pkg_dir_ , _loader_ , _standard_tests_ , _pattern_ )¶

    

~~~
在测试包中使用的 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") `load_tests` 协议的通用实现。 _pkg_dir_ 是包的根目录； _loader_ , _standard_tests_ 和 _pattern_ 是 `load_tests` 所期望的参数。 在简单的情况下，测试包的 `__init__.py` 可以是下面这样的:
~~~
    
    
~~~
import os
from test.support import load_package_tests

def load_tests(*args):
    return load_package_tests(os.path.dirname(__file__), *args)
~~~

test.support.detect_api_mismatch( _ref_api_ , _other_api_ , _*_ , _ignore =()_)¶

    

~~~
返回未在 _other_api_ 中找到的 _ref_api_ 的属性、函数或方法的集合，除去在 _ignore_ 中指明的要在这个检查中忽略的已定义条目列表。

在默认情况下这将跳过以 '_' 打头的私有属性但包括所有魔术方法，即以 '__' 打头和结尾的方法。

在 3.5 版本加入.

test.support.patch( _test_instance_ , _object_to_patch_ , _attr_name_ , _new_value_ )¶
~~~
    

~~~
用 _new_value_ 重载 _object_to_patch.attr_name_ 。并向 _test_instance_ 添加清理过程以便为 _attr_name_ 恢复 _object_to_patch_ 。 _attr_name_ 应当是 _object_to_patch_ 的一个有效属性。

test.support.run_in_subinterp( _code_ )¶
~~~
    

~~~
在子解释器中运行 _code_ 。 如果启用了 [`tracemalloc`](tracemalloc.md#module-tracemalloc "tracemalloc: Trace memory allocations.") 则会引发 [`unittest.SkipTest`](unittest.md#unittest.SkipTest "unittest.SkipTest")。

test.support.check_free_after_iterating( _test_ , _iter_ , _cls_ , _args =()_)¶
~~~
    

~~~
断言 _cls_ 的实例在迭代后被释放。

test.support.missing_compiler_executable( _cmd_names =[]_)¶
~~~
    

~~~
检查在 _cmd_names_ 中列出名称的或者当 _cmd_names_ 为空时所有的编译器可执行文件是否存在并返回第一个丢失的可执行文件或者如果未发现任何丢失则返回 `None`。

test.support.check__all__( _test_case_ , _module_ , _name_of_module =None_, _extra =()_, _not_exported =()_)¶
~~~
    

~~~
断言 _module_ 的 `__all__` 变量包含全部公共名称。

模块的公共名称（它的 API）是根据它们是否符合公共名称惯例并在 _module_ 中被定义来自动检测的。

_name_of_module_ 参数可以（用字符串或元组的形式）指定一个 API 可以被定义为什么模块以便被检测为一个公共 API。 一种这样的情况会在 _module_ 从其他模块，可能是一个 C 后端 (如 `csv` 和它的 `_csv`) 导入其公共 API 的某一组成部分时发生。

_extra_ 参数可以是一个在其他情况下不会被自动检测为 "public" 的名称集合，例如没有适当 `__module__` 属性的对象。 如果提供该参数，它将被添加到自动检测到的对象中。

_not_exported_ 参数可以是一个不可被当作公共 API 的一部分的名称集合，即使其名称没有显式指明这一点。

用法示例:
~~~
    
    
~~~
import bar
import foo
import unittest
from test import support

class MiscTestCase(unittest.TestCase):
    def test__all__(self):
        support.check__all__(self, foo)

class OtherTestCase(unittest.TestCase):
    def test__all__(self):
        extra = {'BAR_CONST', 'FOO_CONST'}
        not_exported = {'baz'}  # Undocumented name.
        # bar imports part of its API from _bar.
        support.check__all__(self, bar, ('bar', '_bar'),
                             extra=extra, not_exported=not_exported)
~~~

在 3.6 版本加入.

test.support.skip_if_broken_multiprocessing_synchronize()¶

    

~~~
如果没有 `multiprocessing.synchronize` 模块，没有可用的 semaphore 实现，或者如果创建一个锁会引发 [`OSError`](exceptions.md#OSError "OSError") 则跳过测试。

在 3.10 版本加入.

test.support.check_disallow_instantiation( _test_case_ , _tp_ , _* args_, _** kwds_)¶
~~~
    

~~~
断言类型 _tp_ 不能使用 _args_ 和 _kwds_ 来实例化。

在 3.10 版本加入.

test.support.adjust_int_max_str_digits( _max_digits_ )¶
~~~
    

~~~
此函数返回一个将在上下文生效期间改变全局 [`sys.set_int_max_str_digits()`](sys.md#sys.set_int_max_str_digits "sys.set_int_max_str_digits") 设置的上下文管理器以便允许执行当在整数和字符串之间进行转换时需要对位数有不同限制的测试代码。

在 3.11 版本加入.

`test.support` 模块定义了以下的类:

_class _test.support.SuppressCrashReport¶
~~~
    

~~~
一个用于在预期会使子进程崩溃的测试时尽量防止弹出崩溃对话框的上下文管理器。

在 Windows 上，它会使用 [SetErrorMode](https://msdn.microsoft.com/en-us/library/windows/desktop/ms680621.aspx) 来禁用 Windows 错误报告对话框。

在 UNIX 上，会使用 [`resource.setrlimit()`](resource.md#resource.setrlimit "resource.setrlimit") 来将 [`resource.RLIMIT_CORE`](resource.md#resource.RLIMIT_CORE "resource.RLIMIT_CORE") 的软限制设为 0 以防止创建核心转储文件。

在这两个平台上，旧值都将被 `__exit__()` 恢复。

_class _test.support.SaveSignals¶
~~~
    

~~~
用于保存和恢复由 Python 句柄的所注册的信号处理句柄。

save( _self_ )¶
~~~
    

~~~
将信号处理句柄保存到一个将信号编号映射到当前信号处理句柄的字典。

restore( _self_ )¶
~~~
    

~~~
将来自 `save()` 字典的信号编号设置到已保存的处理句柄上。

_class _test.support.Matcher¶
~~~
    

~~~
matches( _self_ , _d_ , _** kwargs_)¶
~~~
    

~~~
尝试对单个字典与所提供的参数进行匹配。

match_value( _self_ , _k_ , _dv_ , _v_ )¶
~~~
    

~~~
尝试对单个已存储值 ( _dv_ ) 与所提供的值 ( _v_ ) 进行匹配。

# `test.support.socket_helper` \--- 用于套接字测试的工具¶

`test.support.socket_helper` 模块提供了对套接字测试的支持。

在 3.9 版本加入.

test.support.socket_helper.IPV6_ENABLED¶
~~~
    

~~~
设置为 `True` 如果主机打开IPv6, 否则 `False` .

test.support.socket_helper.find_unused_port( _family =socket.AF_INET_, _socktype =socket.SOCK_STREAM_)¶
~~~
    

~~~
返回一个应当适合绑定的未使用端口。 这是通过创建一个与 `sock` 形参相同协议族和类型的临时套接字来达成的 (默认为 [`AF_INET`](socket.md#socket.AF_INET "socket.AF_INET"), [`SOCK_STREAM`](socket.md#socket.SOCK_STREAM "socket.SOCK_STREAM"))，并将其绑定到指定的主机地址 (默认为 `0.0.0.0`) 并将端口设为 0，以从 OS 引出一个未使用的瞬时端口。 这个临时套接字随后将被关闭并删除，然后返回该瞬时端口。

这个方法或 `bind_port()` 应当被用于任何在测试期间需要绑定到特定端口的测试。 具体使用哪个取决于调用方代码是否会创建 Python 套接字，或者是否需要在构造器中提供或向外部程序提供未使用的端口（例如传给 openssl 的 s_server 模式的 `-accept` 参数）。 在可能的情况下将总是优先使用 `bind_port()` 而非 `find_unused_port()`。 不建议使用硬编码的端口因为将使测试的多个实例无法同时运行，这对 buildbot 来说是个问题。

test.support.socket_helper.bind_port( _sock_ , _host =HOST_)¶
~~~
    

~~~
将套接字绑定到一个空闲端口并返回端口号。 这依赖于瞬时端口以确保我们能使用一个未绑定端口。 这很重要因为可能会有许多测试同时运行，特别是在 buildbot 环境中。 如果 `sock.family` 为 [`AF_INET`](socket.md#socket.AF_INET "socket.AF_INET") 而 `sock.type` 为 [`SOCK_STREAM`](socket.md#socket.SOCK_STREAM "socket.SOCK_STREAM")，并且套接字上设置了 `SO_REUSEADDR` 或 `SO_REUSEPORT` 则此方法将引发异常。 测试绝不应该为 TCP/IP 套接字设置这些套接字选项。 唯一需要设置这些选项的情况是通过多个 UDP 套接字来测试组播。

此外，如果 `SO_EXCLUSIVEADDRUSE` 套接字选项是可用的（例如在 Windows 上），它将在套接字上被设置。 这将阻止其他任何人在测试期间绑定到我们的主机/端口。

test.support.socket_helper.bind_unix_socket( _sock_ , _addr_ )¶
~~~
    

~~~
绑定一个 Unix 套接字，如果 [`PermissionError`](exceptions.md#PermissionError "PermissionError") 被引发则会引发 [`unittest.SkipTest`](unittest.md#unittest.SkipTest "unittest.SkipTest")。

@test.support.socket_helper.skip_unless_bind_unix_socket¶
~~~
    

~~~
一个用于运行需要 Unix 套接字 `bind()` 功能的测试的装饰器。

test.support.socket_helper.transient_internet( _resource_name_ , _*_ , _timeout =30.0_, _errnos =()_)¶
~~~
    

~~~
一个在互联网连接的各种问题以异常的形式表现出来时会引发 `ResourceDenied` 的上下文管理器。

# `test.support.script_helper` \--- 用于 Python 执行测试工具¶

`test.support.script_helper` 模块提供了对 Python 的脚本执行测试的支持。

test.support.script_helper.interpreter_requires_environment()¶
~~~
    

~~~
如果 `sys.executable interpreter` 需要环境变量才能运行则返回 `True`。

这被设计用来配合 `@unittest.skipIf()` 以便标注需要使用to annotate tests that need to use an `assert_python*()` 函数来启动隔离模式 (`-I`) 或无环境模式 (`-E`) 子解释器的测试。

正常的编译和测试运行不会进入这种状况但它在尝试从一个使用 Python 的当前家目录查找逻辑找不到明确的家目录的解释器运行标准库测试套件时有可能发生。

设置 [`PYTHONHOME`](../using/cmdline.md#envvar-PYTHONHOME) 是一种能让大多数测试套件在这种情况下运行的办法。 [`PYTHONPATH`](../using/cmdline.md#envvar-PYTHONPATH) 或 `PYTHONUSERSITE` 是另外两个可影响解释器是否能启动的常见环境变量。

test.support.script_helper.run_python_until_end( _* args_, _** env_vars_)¶
~~~
    

~~~
基于 _env_vars_ 设置环境以便在子进程中运行解释器。 它的值可以包括 `__isolated`, `__cleanenv`, `__cwd`, and `TERM`。

在 3.9 版本发生变更: 此函数不会再从 _stderr_ 去除空格符。

test.support.script_helper.assert_python_ok( _* args_, _** env_vars_)¶
~~~
    

~~~
断言附带 _args_ 和可选的环境变量 _env_vars_ 运行解释器会成功 (`rc == 0`) 并返回一个 `(return code, stdout, stderr)` 元组。

如果设置了 ___cleanenv_ 仅限关键字形参， _env_vars_ 会被用作一个全新的环境。

Python 是以隔离模式 (命令行选项 `-I`) 启动的，除非 ___isolated_ 仅限关键字形参被设为 `False`。

在 3.9 版本发生变更: 此函数不会再从 _stderr_ 去除空格符。

test.support.script_helper.assert_python_failure( _* args_, _** env_vars_)¶
~~~
    

~~~
断言附带 _args_ 和可选的环境变量 _env_vars_ 运行解释器会失败 (`rc != 0`) 并返回一个 `(return code, stdout, stderr)` 元组。

更多选项请参阅 `assert_python_ok()`。

在 3.9 版本发生变更: 此函数不会再从 _stderr_ 去除空格符。

test.support.script_helper.spawn_python( _* args_, _stdout =subprocess.PIPE_, _stderr =subprocess.STDOUT_, _** kw_)¶
~~~
    

~~~
使用给定的参数运行一个 Python 子进程。

_kw_ 是要传给 [`subprocess.Popen()`](subprocess.md#subprocess.Popen "subprocess.Popen") 的额外关键字参数。 返回一个 [`subprocess.Popen`](subprocess.md#subprocess.Popen "subprocess.Popen") 对象。

test.support.script_helper.kill_python( _p_ )¶
~~~
    

~~~
运行给定的 [`subprocess.Popen`](subprocess.md#subprocess.Popen "subprocess.Popen") 进程直至完成并返回 stdout。

test.support.script_helper.make_script( _script_dir_ , _script_basename_ , _source_ , _omit_suffix =False_)¶
~~~
    

~~~
在路径 _script_dir_ 和 _script_basename_ 中创建包含 _source_ 的脚本。 如果 _omit_suffix_ 为 `False`，则为名称添加 `.py`。 返回完整的脚本路径。

test.support.script_helper.make_zip_script( _zip_dir_ , _zip_basename_ , _script_name_ , _name_in_zip =None_)¶
~~~
    

~~~
使用 _zip_dir_ 和 _zip_basename_ 创建扩展名为 `zip` 的 zip 文件，其中包含 _script_name_ 中的文件。 _name_in_zip_ 为归档名。 返回一个包含 `(full path, full path of archive name)` 的元组。

test.support.script_helper.make_pkg( _pkg_dir_ , _init_source =''_)¶
~~~
    

~~~
创建一个名为 _pkg_dir_ 的目录，其中包含一个 `__init__` 文件并以 _init_source_ 作为其内容。

test.support.script_helper.make_zip_pkg( _zip_dir_ , _zip_basename_ , _pkg_name_ , _script_basename_ , _source_ , _depth =1_, _compiled =False_)¶
~~~
    

~~~
使用 _zip_dir_ 和 _zip_basename_ 创建一个 zip 包目录，其中包含一个空的 `__init__` 文件和一个包含 _source_ 的文件 _script_basename_ 。 如果 _compiled_ 为 `True`，则两个源文件将被编译并添加到 zip 包中。 返回一个以完整 zip 路径和 zip 文件归档名为元素的元组。

# `test.support.bytecode_helper` \--- 用于测试正确字节码生成的支持工具¶

`test.support.bytecode_helper` 模块提供了对测试和检查字节码生成的支持。

在 3.9 版本加入.

The module defines the following class:

_class _test.support.bytecode_helper.BytecodeTestCase( _unittest.TestCase_ )¶
~~~
    

~~~
这个类具有用于检查字节码的自定义断言。

BytecodeTestCase.get_disassembly_as_string( _co_ )¶
~~~
    

~~~
以字符串形式返回 _co_ 的汇编码。

BytecodeTestCase.assertInBytecode( _x_ , _opname_ , _argval =_UNSPECIFIED_)¶
~~~
    

~~~
如果找到 _opname_ 则返回 instr，否则抛出 [`AssertionError`](exceptions.md#AssertionError "AssertionError")。

BytecodeTestCase.assertNotInBytecode( _x_ , _opname_ , _argval =_UNSPECIFIED_)¶
~~~
    

~~~
如果找到 _opname_ 则抛出 [`AssertionError`](exceptions.md#AssertionError "AssertionError")。

# `test.support.threading_helper` \--- 用于线程测试的工具¶

`test.support.threading_helper` 模块提供了对线程测试的支持。

在 3.10 版本加入.

test.support.threading_helper.join_thread( _thread_ , _timeout =None_)¶
~~~
    

~~~
在 _timeout_ 秒之内合并一个 _thread_ 。 如果线程在 _timeout_ 秒后仍然存活则引发 [`AssertionError`](exceptions.md#AssertionError "AssertionError")。

@test.support.threading_helper.reap_threads¶
~~~
    

~~~
用于确保即使测试失败线程仍然会被清理的装饰器。

test.support.threading_helper.start_threads( _threads_ , _unlock =None_)¶
~~~
    

~~~
启动 _threads_ 的上下文管理器，该参数为一个线程序列。 _unlock_ 是一个在所有线程启动之后被调用的函数，即使引发了异常也会执行；一个例子是 [`threading.Event.set()`](threading.md#threading.Event.set "threading.Event.set")。 `start_threads` 将在退出时尝试合并已启动的线程。

test.support.threading_helper.threading_cleanup( _* original_values_)¶
~~~
    

~~~
清理未在 _original_values_ 中指定的线程。 被设计为如果有一个测试在后台离开正在运行的线程时会发出警告。

test.support.threading_helper.threading_setup()¶
~~~
    

~~~
返回当前线程计数和悬空线程的副本。

test.support.threading_helper.wait_threads_exit( _timeout =None_)¶
~~~
    

~~~
等待直到 `with` 语句中所有已创建线程退出的上下文管理器。

test.support.threading_helper.catch_threading_exception()¶
~~~
    

~~~
使用 [`threading.excepthook()`](threading.md#threading.excepthook "threading.excepthook") 来捕获 [`threading.Thread`](threading.md#threading.Thread "threading.Thread") 异常的上下文管理器。

当异常被捕获时要设置的属性:

  * `exc_type`

  * `exc_value`

  * `exc_traceback`

  * `thread`

参见 [`threading.excepthook()`](threading.md#threading.excepthook "threading.excepthook") 文档。

这些属性在上下文管理器退出时将被删除。

用法：
~~~
    
    
~~~
with threading_helper.catch_threading_exception() as cm:
    # code spawning a thread which raises an exception
    ...

    # check the thread exception, use cm attributes:
    # exc_type, exc_value, exc_traceback, thread
    ...

# exc_type, exc_value, exc_traceback, thread attributes of cm no longer
# exists at this point
# (to avoid reference cycles)
~~~

在 3.8 版本加入.

# `test.support.os_helper` \--- 用于操作系统测试的工具¶

`test.support.os_helper` 模块提供了对操作系统测试的支持。

在 3.10 版本加入.

test.support.os_helper.FS_NONASCII¶

    

~~~
一个可通过 [`os.fsencode()`](os.md#os.fsencode "os.fsencode") 编码的非 ASCII 字符。

test.support.os_helper.SAVEDCWD¶
~~~
    

~~~
设置为 [`os.getcwd()`](os.md#os.getcwd "os.getcwd")。

test.support.os_helper.TESTFN¶
~~~
    

~~~
设置为一个可以安全地用作临时文件名的名称。 任何被创建的临时文件都应当被关闭和撤销链接（移除）。

test.support.os_helper.TESTFN_NONASCII¶
~~~
    

~~~
如果存在的话，设置为一个包含 `FS_NONASCII` 字符的文件名。 这会确保当文件名存在时，它可使用默认文件系统编码格式来编码和解码。 这允许需要非 ASCII 文件名的测试在其不可用的平台上被方便地跳过。

test.support.os_helper.TESTFN_UNENCODABLE¶
~~~
    

~~~
设置为一个应当在严格模式下不可使用文件系统编码格式来编码的文件名（str 类型）。 如果无法生成这样的文件名则可以为 `None`。

test.support.os_helper.TESTFN_UNDECODABLE¶
~~~
    

~~~
设置为一个应当在严格模式下不可使用文件系统编码格式来编码的文件名（bytes 类型）。 如果无法生成这样的文件名则可以为 `None`。

test.support.os_helper.TESTFN_UNICODE¶
~~~
    

~~~
设置为用于临时文件的非 ASCII 名称。

_class _test.support.os_helper.EnvironmentVarGuard¶
~~~
    

~~~
用于临时性地设置或取消设置环境变量的类。 其实例可被用作上下文管理器并具有完整的字典接口用来查询/修改下层的 `os.environ`。 在从上下文管理器退出之后所有通过此实例对环境变量进行的修改都将被回滚。

在 3.1 版本发生变更: 增加了字典接口。

_class _test.support.os_helper.FakePath( _path_ )¶
~~~
    

~~~
简单的 [path-like object](../glossary.md#term-path-like-object)。 它实现了 `__fspath__()` 方法，该方法将返回 _path_ 参数。 如果 _path_ 为一个异常，它将在 `__fspath__()` 中被引发。

EnvironmentVarGuard.set( _envvar_ , _value_ )¶
~~~
    

~~~
临时性地将环境变量 `envvar` 的值设为 `value`。

EnvironmentVarGuard.unset( _envvar_ )¶
~~~
    

~~~
临时性地取消设置环境变量 `envvar`。

test.support.os_helper.can_symlink()¶
~~~
    

~~~
如果操作系统支持符号链接则返回 `True`，否则返回 `False`。

test.support.os_helper.can_xattr()¶
~~~
    

~~~
如果操作系统支持 xattr 支返回 `True`，否则返回 `False`。

test.support.os_helper.change_cwd( _path_ , _quiet =False_)¶
~~~
    

~~~
一个临时性地将当前工作目录改为 _path_ 并输出该目录的上下文管理器。

如果 _quiet_ 为 `False`，此上下文管理器将在发生错误时引发一个异常。 在其他情况下，它将只发出一个警告并将当前工作目录保持原状。

test.support.os_helper.create_empty_file( _filename_ )¶
~~~
    

~~~
创建一个名为 _filename_ 的空文件。 如果文件已存在，则清空其内容。

test.support.os_helper.fd_count()¶
~~~
    

~~~
统计打开的文件描述符数量。

test.support.os_helper.fs_is_case_insensitive( _directory_ )¶
~~~
    

~~~
如果 _directory_ 的文件系统对大小写敏感则返回 `True`。

test.support.os_helper.make_bad_fd()¶
~~~
    

~~~
通过打开并关闭临时文件来创建一个无效的文件描述符，并返回其描述器。

test.support.os_helper.rmdir( _filename_ )¶
~~~
    

~~~
在 _filename_ 上调用 [`os.rmdir()`](os.md#os.rmdir "os.rmdir")。 在 Windows 平台上，这将使用一个检测文件是否存在的等待循环来包装，需要这样做是因为反病毒程序会保持文件打开并阻止其被删除。

test.support.os_helper.rmtree( _path_ )¶
~~~
    

~~~
在 _path_ 上调用 [`shutil.rmtree()`](shutil.md#shutil.rmtree "shutil.rmtree") 或者调用 [`os.lstat()`](os.md#os.lstat "os.lstat") 和 [`os.rmdir()`](os.md#os.rmdir "os.rmdir") 来移除一个路径及其内容。 与 `rmdir()` 一样，在 Windows 平台上这将使用一个检测文件是否存在的等待循环来包装。

@test.support.os_helper.skip_unless_symlink¶
~~~
    

~~~
一个用于运行需要符号链接支持的测试的装饰器。

@test.support.os_helper.skip_unless_xattr¶
~~~
    

~~~
一个用于运行需要 xattr 支持的测试的装饰器。

test.support.os_helper.temp_cwd( _name ='tempcwd'_, _quiet =False_)¶
~~~
    

~~~
一个临时性地创建新目录并改变当前工作目录（CWD）的上下文管理器。

临时性地改变当前工作目录之前此上下文管理器会在当前目录下创建一个名为 _name_ 的临时目录。 如果 _name_ 为 `None`，则会使用 [`tempfile.mkdtemp()`](tempfile.md#tempfile.mkdtemp "tempfile.mkdtemp") 创建临时目录。

如果 _quiet_ 为 `False` 并且无法创建或修改 CWD，则会引发一个错误。 在其他情况下，只会引发一个警告并使用原始 CWD。

test.support.os_helper.temp_dir( _path =None_, _quiet =False_)¶
~~~
    

~~~
一个在 _path_ 上创建临时目录并输出该目录的上下文管理器。

如果 _path_ 为 `None`，则会使用 [`tempfile.mkdtemp()`](tempfile.md#tempfile.mkdtemp "tempfile.mkdtemp") 来创建临时目录。 如果 _quiet_ 为 `False`，则该上下文管理器在发生错误时会引发一个异常。 在其他情况下，如果 _path_ 已被指定并且无法创建，则只会发出一个警告。

test.support.os_helper.temp_umask( _umask_ )¶
~~~
    

~~~
一个临时性地设置进程掩码的上下文管理器。

test.support.os_helper.unlink( _filename_ )¶
~~~
    

~~~
在 _filename_ 上调用 [`os.unlink()`](os.md#os.unlink "os.unlink")。 与 `rmdir()` 一样，在 Windows 平台上这将使用一个检测文本是否存在的等待循环来包装。

# `test.support.import_helper` \--- 用于导入测试的工具¶

`test.support.import_helper` 模块提供了对导入测试的支持。

在 3.10 版本加入.

test.support.import_helper.forget( _module_name_ )¶
~~~
    

~~~
从 `sys.modules` 移除名为 _module_name_ 的模块并删除该模块的已编译字节码文件。

test.support.import_helper.import_fresh_module( _name_ , _fresh =()_, _blocked =()_, _deprecated =False_)¶
~~~
    

~~~
此函数会在执行导入之前通过从 `sys.modules` 移除指定模块来导入并返回指定 Python 模块的新副本。 请注意这不同于 `reload()`，原来的模块不会受到此操作的影响。

_fresh_ 是包含在执行导入之前还要从 `sys.modules` 缓存中移除的附加模块名称的可迭代对象。

_blocked_ 是包含模块名称的可迭代对象，导入期间在模块缓存中它会被替换为 `None` 以确保尝试导入将引发 [`ImportError`](exceptions.md#ImportError "ImportError")。

指定名称的模块以及任何在 _fresh_ 和 _blocked_ 形参中指明的模块会在开始导入之前被保存并在全新导入完成时被重新插入到 `sys.modules` 中。

如果 _deprecated_ 为 `True` 则在此导入操作期间模块和包的弃用消息会被屏蔽。

如果指定名称的模块无法被导入则此函数将引发 [`ImportError`](exceptions.md#ImportError "ImportError")。

用法示例:
~~~
    
    
~~~
# Get copies of the warnings module for testing without affecting the
# version being used by the rest of the test suite. One copy uses the
# C implementation, the other is forced to use the pure Python fallback
# implementation
py_warnings = import_fresh_module('warnings', blocked=['_warnings'])
c_warnings = import_fresh_module('warnings', fresh=['_warnings'])
~~~

在 3.1 版本加入.

test.support.import_helper.import_module( _name_ , _deprecated =False_, _*_ , _required_on =()_)¶

    

~~~
此函数会导入并返回指定名称的模块。 不同于正常的导入，如果模块无法被导入则此函数将引发 [`unittest.SkipTest`](unittest.md#unittest.SkipTest "unittest.SkipTest")。

如果 _deprecated_ 为 `True` 则在此导入操作期间模块和包的弃用消息会被屏蔽。 如果某个模块在特定平台上是必需的而在其他平台上是可选的，请为包含平台前缀的可迭代对象设置 _required_on_ ，此对象将与 [`sys.platform`](sys.md#sys.platform "sys.platform") 进行比对。

在 3.1 版本加入.

test.support.import_helper.modules_setup()¶
~~~
    

~~~
返回 [`sys.modules`](sys.md#sys.modules "sys.modules") 的副本。

test.support.import_helper.modules_cleanup( _oldmodules_ )¶
~~~
    

~~~
移除 _oldmodules_ 和 `encodings` 以外的模块以保留内部缓冲区。

test.support.import_helper.unload( _name_ )¶
~~~
    

~~~
从 `sys.modules` 中删除 _name_ 。

test.support.import_helper.make_legacy_pyc( _source_ )¶
~~~
    

~~~
将 [**PEP 3147**](https://peps.python.org/pep-3147/)/[ **PEP 488**](https://peps.python.org/pep-0488/) pyc 文件移至旧版 pyc 位置并返回该旧版 pyc 文件的文件系统路径。 _source_ 的值是源文件的文件系统路径。 它不必真实存在，但是 PEP 3147/488 pyc 文件必须存在。

_class _test.support.import_helper.CleanImport( _* module_names_)¶
~~~
    

~~~
强制导入以返回一个新的模块引用的上下文管理器。 这适用于测试模块层级的行为，例如在导入时发出 [`DeprecationWarning`](exceptions.md#DeprecationWarning "DeprecationWarning")。 示例用法:
~~~
    
    
~~~
with CleanImport('foo'):
    importlib.import_module('foo')  # New reference.
~~~

_class _test.support.import_helper.DirsOnSysPath( _* paths_)¶

    

~~~
一个临时性地向 [`sys.path`](sys.md#sys.path "sys.path") 添加目录的上下文管理器。

这将创建 [`sys.path`](sys.md#sys.path "sys.path") 的一个副本，添加作为位置参数传入的任何目录，然后在上下文结束时将 [`sys.path`](sys.md#sys.path "sys.path") 还原到副本的设置。

请注意该上下文管理器代码块中 _所有_ 对 [`sys.path`](sys.md#sys.path "sys.path") 的修改，包括对象的替换，都将在代码块结束时被还原。

# `test.support.warnings_helper` \--- 用于警告测试的工具¶

`test.support.warnings_helper` 模块提供了对警告测试的支持。

在 3.10 版本加入.

test.support.warnings_helper.ignore_warnings( _*_ , _category_ )¶
~~~
    

~~~
抑制作为 _category_ 实例的警告，它必须为 [`Warning`](exceptions.md#Warning "Warning") 或其子类。 大致等价于 [`warnings.catch_warnings()`](warnings.md#warnings.catch_warnings "warnings.catch_warnings") 设置 [`warnings.simplefilter('ignore', category=category)`](warnings.md#warnings.simplefilter "warnings.simplefilter")。 例如:
~~~
    
    
~~~
@warning_helper.ignore_warnings(category=DeprecationWarning)
def test_suppress_warning():
    # do something
~~~

在 3.8 版本加入.

test.support.warnings_helper.check_no_resource_warning( _testcase_ )¶

    

~~~
检测是否没有任何 [`ResourceWarning`](exceptions.md#ResourceWarning "ResourceWarning") 被引发的上下文管理器。 你必须在该上下文管理器结束之前移除可能发出 [`ResourceWarning`](exceptions.md#ResourceWarning "ResourceWarning") 的对象。

test.support.warnings_helper.check_syntax_warning( _testcase_ , _statement_ , _errtext =''_, _*_ , _lineno =1_, _offset =None_)¶
~~~
    

~~~
用于通过尝试编译 _statement_ 来测试 _statement_ 中的语法警告。 还会测试 [`SyntaxWarning`](exceptions.md#SyntaxWarning "SyntaxWarning") 是否只发出了一次，以及它在转成错误时是否将被转换为 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError")。 _testcase_ 是用于测试的 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 实例。 _errtext_ 是应当匹配所发出的 [`SyntaxWarning`](exceptions.md#SyntaxWarning "SyntaxWarning") 以及所引发的 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError") 的字符串表示形式的正则表达式。 如果 _lineno_ 不为 `None`，则与警告和异常所在的行进行比较。 如果 _offset_ 不为 `None`，则与异常的偏移量进行比较。

在 3.8 版本加入.

test.support.warnings_helper.check_warnings( _* filters_, _quiet =True_)¶
~~~
    

~~~
一个用于 [`warnings.catch_warnings()`](warnings.md#warnings.catch_warnings "warnings.catch_warnings") 以更容易地测试特定警告是否被正确引发的便捷包装器。 它大致等价于调用 `warnings.catch_warnings(record=True)` 并将 [`warnings.simplefilter()`](warnings.md#warnings.simplefilter "warnings.simplefilter") 设为 `always` 并附带自动验证已记录结果的选项。

`check_warnings` 接受 `("message regexp", WarningCategory)` 形式的 2 元组作为位置参数。 如果提供了一个或多个 _filters_ ，或者如果可选的关键字参数 _quiet_ 为 `False`，则它会检查确认警告是符合预期的：每个已指定的过滤器必须匹配至少一个被包围的代码或测试失败时引发的警告，并且如果有任何未能匹配已指定过滤器的警告被引发则测试将失败。 要禁用这些检查中的第一项，请将 _quiet_ 设为 `True`。

如果未指定任何参数，则默认为:
~~~
    
    
~~~
check_warnings(("", Warning), quiet=True)
~~~

在此情况下所有警告都会被捕获而不会引发任何错误。

在进入该上下文管理器时，将返回一个 `WarningRecorder` 实例。 来自 [`catch_warnings()`](warnings.md#warnings.catch_warnings "warnings.catch_warnings") 的下层警告列表可通过该记录器对象的 [`warnings`](warnings.md#module-warnings "warnings: Issue warning messages and control their disposition.") 属性来访问。 作为一个便捷方式，该对象中代表最近的警告的属性也可通过该记录器对象来直接访问（参见以下示例）。 如果未引发任何警告，则在其他情况下预期代表一个警告的任何对象属性都将返回 `None`。

该记录器对象还有一个 `reset()` 方法，该方法会清空警告列表。

该上下文管理器被设计为像这样来使用:

    
    
~~~
with check_warnings(("assertion is always true", SyntaxWarning),
                    ("", UserWarning)):
    exec('assert(False, "Hey!")')
    warnings.warn(UserWarning("Hide me!"))
~~~

在此情况下如果两个警告都未被引发，或是引发了其他的警告，则 `check_warnings()` 将会引发一个错误。

当一个测试需要更深入地查看这些警告，而不是仅仅检查它们是否发生时，可以使用这样的代码:

    
    
~~~
with check_warnings(quiet=True) as w:
    warnings.warn("foo")
    assert str(w.args[0]) == "foo"
    warnings.warn("bar")
    assert str(w.args[0]) == "bar"
    assert str(w.warnings[0].args[0]) == "foo"
    assert str(w.warnings[1].args[0]) == "bar"
    w.reset()
    assert len(w.warnings) == 0
~~~

在这里所有的警告都将被捕获，而测试代码会直接测试被捕获的警告。

在 3.2 版本发生变更: 新增可选参数 _filters_ 和 _quiet_ 。

_class _test.support.warnings_helper.WarningsRecorder¶

    

~~~
