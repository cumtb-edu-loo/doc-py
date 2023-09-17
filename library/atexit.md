# `atexit` \--- 退出处理器¶

* * *

`atexit` 模块定义了清理函数的注册和反注册函数. 被注册的函数会在解释器正常终止时执行. `atexit` 会按照注册顺序的*逆序*执行; 如果你注册了 `A`, `B` 和 `C`, 那么在解释器终止时会依序执行 `C`, `B`, `A`.

**注意:** 通过该模块注册的函数, 在程序被未被 Python 捕获的信号杀死时并不会执行, 在检测到 Python 内部致命错误以及调用了 [`os._exit()`](os.md#os._exit "os._exit") 时也不会执行.

**注意:** 在清理函数内部注册或注销函数可能产生的影响是未定义的。

在 3.7 版本发生变更: 当配合 C-API 子解释器使用时，已注册函数是它们所注册解释器中的局部对象。

atexit.register( _func_ , _* args_, _** kwargs_)¶

    

~~~
将 _func_ 注册为终止时执行的函数. 任何传给 _func_ 的可选的参数都应当作为参数传给 `register()`. 可以多次注册同样的函数及参数.

在正常的程序终止时 (举例来说, 当调用了 [`sys.exit()`](sys.md#sys.exit "sys.exit") 或是主模块的执行完成时), 所有注册过的函数都会以后进先出的顺序执行. 这样做是假定更底层的模块通常会比高层模块更早引入, 因此需要更晚清理.

如果在 exit 处理句柄执行期间引发了异常，将会打印回溯信息 (除非引发的是 [`SystemExit`](exceptions.md#SystemExit "SystemExit")) 并且异常信息会被保存。 在所有 exit 处理句柄都获得运行机会之后，所引发的最后一个异常会被重新引发。

这个函数返回 _func_ 对象，可以把它当作装饰器使用。

警告

启动新线程或从已注册的函数调用 [`os.fork()`](os.md#os.fork "os.fork") 可能导致主 Python 运行时线程释放线程状态而内部 [`threading`](threading.md#module-threading "threading: Thread-based parallelism.") 例程或新进程试图使用该状态之间的竞争条件。 这会造成程序崩溃而不是正常关闭。

在 3.12 版本发生变更: 现在尝试启动新线程或在已注册的函数中 [`os.fork()`](os.md#os.fork "os.fork") 新进程会导致 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

atexit.unregister( _func_ )¶
~~~
    

~~~
将 _func_ 移出当解释器关闭时要运行的函数列表。 如果 _func_ 之前未被注册则 `unregister()` 将静默地不做任何事。 如果 _func_ 已被注册一次以上，则该函数每次在 `atexit` 调用栈中的出现都将被移除。 当取消注册时会在内部使用相等性比较 (`==`)，因而函数引用不需要具有匹配的标识号。

参见

模块 [`readline`](readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)")
~~~
    

~~~
使用 `atexit` 读写 [`readline`](readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 历史文件的有用的例子。

## `atexit` 示例¶

以下简单例子演示了一个模块在被导入时如何从文件初始化一个计数器，并在程序终结时自动保存计数器的更新值，此操作不依赖于应用在终结时对此模块进行显式调用。:
~~~
    
    
~~~
try:
    with open('counterfile') as infile:
        _count = int(infile.read())
except FileNotFoundError:
    _count = 0

def incrcounter(n):
    global _count
    _count = _count + n

def savecounter():
    with open('counterfile', 'w') as outfile:
        outfile.write('%d' % _count)

import atexit

atexit.register(savecounter)
~~~

位置和关键字参数也可传入 `register()` 以便传递给被调用的已注册函数:

    
    
~~~python
def goodbye(name, adjective):
    print('Goodbye %s, it was %s to meet you.' % (name, adjective))

import atexit

atexit.register(goodbye, 'Donny', 'nice')
# or:
atexit.register(goodbye, adjective='nice', name='Donny')
~~~

作为 [decorator](../glossary.md#term-decorator): 使用:

    
    
~~~
import atexit

@atexit.register
def goodbye():
    print('You are now leaving the Python sector.')
~~~

只有在函数不需要任何参数调用时才能工作.

