# `symtable` ——访问编译器的符号表¶

**Source code：** [Lib/symtable.py](https://github.com/python/cpython/tree/3.12/Lib/symtable.py)

* * *

符号表由编译器在生成字节码之前根据 AST 生成。符号表负责计算代码中每个标识符的作用域。 `symtable` 提供了一个查看这些表的接口。

## 符号表的生成¶

symtable.symtable( _code_ , _filename_ , _compile_type_ )¶

    

~~~
返回 Python 源 _代码_ 顶层的 `SymbolTable`。 _filename_ 是代码文件名。 _compile_type_ 的含义类似 [`compile()`](functions.md#compile "compile") 的 _mode_ 参数。

## 符号表的查看¶

_class _symtable.SymbolTable¶
~~~
    

~~~
某个代码块的命名空间表。构造函数不公开。

get_type()¶
~~~
    

~~~
返回符号表的类型。可能是 `'class'` 、`'module'` 或 `'function'`。

get_id()¶
~~~
    

~~~
返回符号表的标识符

get_name()¶
~~~
    

~~~
返回符号表的名称。 若为类的符号表则返回类名；若为函数的符号表则为函数名；若是全局符号表则为 `'top'` (`get_type()` 返回 `'module'`)。

get_lineno()¶
~~~
    

~~~
返回符号表所代表代码块的第一行编号。

is_optimized()¶
~~~
    

~~~
如果符号表中的局部变量可能被优化过，则返回 `True`。

is_nested()¶
~~~
    

~~~
如果代码块是嵌套类或函数，则返回 `True`。

has_children()¶
~~~
    

~~~
如果代码块中有嵌套的命名空间，则返回 `True`。可通过 `get_children()` 读取。

get_identifiers()¶
~~~
    

~~~
返回一个包含表中符号名称的视图对象。 参见 [视图对象文档](stdtypes.md#dict-views)。

lookup( _name_ )¶
~~~
    

~~~
在符号表中查找 _name_ 并返回一个 `Symbol` 实例。

get_symbols()¶
~~~
    

~~~
返回符号表中所有符号的 `Symbol` 实例的列表。

get_children()¶
~~~
    

~~~
返回嵌套符号表的列表。

_class _symtable.Function¶
~~~
    

~~~
函数或方法的命名空间。该类继承自 `SymbolTable`。

get_parameters()¶
~~~
    

~~~
返回由函数的参数名组成的元组。

get_locals()¶
~~~
    

~~~
返回函数中局部变量名组成的元组。

get_globals()¶
~~~
    

~~~
返回函数中全局变量名组成的元组。

get_nonlocals()¶
~~~
    

~~~
返回函数中非局部变量名组成的元组。

get_frees()¶
~~~
    

~~~
返回函数中自由变量名组成的元组。

_class _symtable.Class¶
~~~
    

~~~
类的命名空间。继承自 `SymbolTable`。

get_methods()¶
~~~
    

~~~
返回类中声明的方法名组成的元组。

_class _symtable.Symbol¶
~~~
    

~~~
`SymbolTable` 中的数据项，对应于源码中的某个标识符。构造函数不公开。

get_name()¶
~~~
    

~~~
返回符号名

is_referenced()¶
~~~
    

~~~
如果符号在代码块中被引用了，则返回 `True`。

is_imported()¶
~~~
    

~~~
如果符号是由导入语句创建的，则返回 `True`。

is_parameter()¶
~~~
    

~~~
如果符号是参数，返回 `True`。

is_global()¶
~~~
    

~~~
如果符号是全局变量，则返回 `True`。

is_nonlocal()¶
~~~
    

~~~
如果符号为非局部变量，则返回 `True`。

is_declared_global()¶
~~~
    

~~~
如果符号用 global 声明为全局变量，则返回 `True`。

is_local()¶
~~~
    

~~~
如果符号是代码块内的局部变量，则返回 `True`。

is_annotated()¶
~~~
    

~~~
如果符号带有注解，则返回 `True`。

在 3.6 版本加入.

is_free()¶
~~~
    

~~~
如果符号在代码块中被引用，但未赋值，则返回 `True`。

is_assigned()¶
~~~
    

~~~
如果符号在代码块中赋值，则返回 `True`。

is_namespace()¶
~~~
    

~~~
如果符号名绑定引入了新的命名空间，则返回 `True`。

如果符号名用于函数或类定义语句，则为 True。

例如：
~~~
    
    
~~~shell
>>> table = symtable.symtable("def some_func(): pass", "string", "exec")
>>> table.lookup("some_func").is_namespace()
True
~~~

注意，一个符号名可以与多个对象绑定。如果结果为 `True`，则该符号名还可以绑定到其他对象上，比如 int 或 list ，且不会引入新的命名空间。

get_namespaces()¶

    

~~~
返回与符号名绑定的命名空间的列表。

get_namespace()¶
~~~
    

~~~
