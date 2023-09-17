# `builtins` \--- 内建对象¶

* * *

该模块提供对Python的所有“内置”标识符的直接访问；例如，`builtins.open` 是内置函数的全名 [`open()`](functions.md#open "open") 。请参阅 [内置函数](functions.md#built-in-funcs) 和 [内置常量](constants.md#built-in-consts) 的文档。

大多数应用程序通常不会显式访问此模块，但在提供与内置值同名的对象的模块中可能很有用，但其中还需要内置该名称。例如，在一个想要实现 [`open()`](functions.md#open "open") 函数的模块中，它包装了内置的 [`open()`](functions.md#open "open") ，这个模块可以直接使用:

    
    
~~~
import builtins

def open(path):
    f = builtins.open(path, 'r')
    return UpperCaser(f)

class UpperCaser:
    '''Wrapper around a file that converts output to uppercase.'''

    def __init__(self, f):
        self._f = f

    def read(self, count=-1):
        return self._f.read(count).upper()

    # ...
~~~

作为一个实现细节，大多数模块都将名称 `__builtins__` 作为其全局变量的一部分提供。 `__builtins__` 的值通常是这个模块或者这个模块的值 [`__dict__`](stdtypes.md#object.__dict__ "object.__dict__") 属性。由于这是一个实现细节，因此 Python 的替代实现可能不会使用它。

