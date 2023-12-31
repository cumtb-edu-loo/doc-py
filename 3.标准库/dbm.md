# `dbm` \--- Unix "数据库" 接口¶

**源代码:** [Lib/dbm/__init__.py](https://github.com/python/cpython/tree/3.12/Lib/dbm/__init__.py)

* * *

`dbm` 是一种泛用接口，针对各种 DBM 数据库 --- 包括 `dbm.gnu` 或 `dbm.ndbm`。 如果未安装这些模块中的任何一种，则将使用 `dbm.dumb` 模块中慢速但简单的实现。 还有一个适用于 Oracle Berkeley DB 的 [第三方接口](https://www.jcea.es/programacion/pybsddb.htm)。

_exception _dbm.error¶

    

~~~
一个元组，其中包含每个受支持的模块可引发的异常，另外还有一个名为 `dbm.error` 的特殊异常作为第一项 --- 后者最在引发 `dbm.error` 时被使用。

dbm.whichdb( _filename_ )¶
~~~
    

~~~
此函数会猜测各种简单数据库模块中的哪一个是可用的 --- `dbm.gnu`, `dbm.ndbm` 还是 `dbm.dumb` \--- 应该被用来打开给定的文件。

返回下列值中的一个：如果文件由于不可读或不存在而无法打开则返回 `None`；如果文件的格式无法猜测则返回空字符串 (`''`)；或是包含所需模块名称的字符串，例如 `'dbm.ndbm'` 或 `'dbm.gnu'`。

在 3.11 版本发生变更: 接受 [path-like object](../glossary.md#term-path-like-object) 作为文件名。

dbm.open( _file_ , _flag ='r'_, _mode =0o666_)¶
~~~
    

~~~
打开数据库文件 _file_ 并返回一个相应的对象。

如果数据库文件已存在，则使用 `whichdb()` 函数来确定其类型和要使用的适当模块；如果文件不存在，则会使用上述可导入模块中的第一个。

可选的 _flag_ 参数可以是：

值

|

含意  
  
---|---  
  
`'r'`

|

以只读方式打开现有数据库（默认）  
  
`'w'`

|

以读写方式打开现有数据库  
  
`'c'`

|

以读写方式打开数据库，如果不存在则创建它  
  
`'n'`

|

始终创建一个新的空数据库，以读写方式打开  
  
可选的 _mode_ 参数是文件的 Unix 模式，仅在要创建数据库时才会被使用。 其默认值为八进制数 `0o666` (并将被当前的 umask 所修改)。

`open()` 所返回的对象支持与字典相同的基本功能；可以存储、获取和删除键及其对应的值，并可使用 [`in`](../reference/expressions.md#in) 运算符和 `keys()` 方法，以及 `get()` 和 `setdefault()`。

在 3.2 版本发生变更: 现在 `get()` 和 `setdefault()` 在所有数据库模块中均可用。

在 3.8 版本发生变更: 从只读数据库中删除键将引发数据库模块专属的错误而不是 [`KeyError`](exceptions.md#KeyError "KeyError")。

在 3.11 版本发生变更: 接受 [path-like object](../glossary.md#term-path-like-object) 作为文件。

键和值总是被存储为字节串。 这意味着当使用字符串时它们会在被存储之前隐式地转换至默认编码格式。

这些对象也支持在 [`with`](../reference/compound_stmts.md#with) 语句中使用，当语句结束时将自动关闭它们。

在 3.4 版本发生变更: 向 `open()` 所返回的对象添加了上下文管理协议的原生支持。

以下示例记录了一些主机名和对应的标题，随后将数据库的内容打印出来。:
~~~
    
    
~~~
import dbm

# Open database, creating it if necessary.
with dbm.open('cache', 'c') as db:

    # Record some values
    db[b'hello'] = b'there'
    db['www.python.org'] = 'Python Website'
    db['www.cnn.com'] = 'Cable News Network'

    # Note that the keys are considered bytes now.
    assert db[b'www.python.org'] == b'Python Website'
    # Notice how the value is now in bytes.
    assert db['www.cnn.com'] == b'Cable News Network'

    # Often-used methods of the dict interface work too.
    print(db.get('python.org', b'not present'))

    # Storing a non-string key or value will raise an exception (most
    # likely a TypeError).
    db['www.yahoo.com'] = 4

# db is automatically closed when leaving the with statement.
~~~

参见

模块 [`shelve`](shelve.md#module-shelve "shelve: Python object persistence.")

    

~~~
存储非字符串数据的持久化模块。

以下部分描述了各个单独的子模块。

## `dbm.gnu` \--- GNU 对 dbm 的重解析¶

**源代码:** [Lib/dbm/gnu.py](https://github.com/python/cpython/tree/3.12/Lib/dbm/gnu.py)

* * *

此模块与 `dbm` 模块很相似，但是改用 GNU 库 `gdbm` 来提供某些附加功能。 请注意由 `dbm.gnu` 与 `dbm.ndbm` 所创建的文件格式是不兼容的。

`dbm.gnu` 模块提供了对 GNU DBM 库的接口。 `dbm.gnu.gdbm` 对象的行为类似于映射（字典），区别在于其键和值总是会在存储之前被转换为字节串。 打印 `gdbm` 对象不会打印出键和值，并且 `items()` 和 `values()` 等方法也不受支持。

_exception _dbm.gnu.error¶
~~~
    

~~~
针对 `dbm.gnu` 专属错误例如 I/O 错误引发。 [`KeyError`](exceptions.md#KeyError "KeyError") 的引发则针对一般映射错误例如指定了不正确的键。

dbm.gnu.open( _filename_ [, _flag_ [, _mode_ ]])¶
~~~
    

~~~
打开一个 `gdbm` 数据库并返回 `gdbm` 对象。 _filename_ 参数为数据库文件名称。

可选的 _flag_ 参数可以是：

值

|

含意  
  
---|---  
  
`'r'`

|

以只读方式打开现有数据库（默认）  
  
`'w'`

|

以读写方式打开现有数据库  
  
`'c'`

|

以读写方式打开数据库，如果不存在则创建它  
  
`'n'`

|

始终创建一个新的空数据库，以读写方式打开  
  
下列附加字符可被添加至旗标以控制数据库的打开方式：

值

|

含意  
  
---|---  
  
`'f'`

|

以快速模式打开数据库。写入数据库将不会同步。  
  
`'s'`

|

同步模式。这将导致数据库的更改立即写入文件。  
  
`'u'`

|

不要锁定数据库。  
  
不是所有旗标都可用于所有版本的 `gdbm`。 模块常量 `open_flags` 为包含受支持旗标字符的字符串。 如果指定了无效的旗标则会引发 `error`。

可选的 _mode_ 参数是文件的 Unix 模式，仅在要创建数据库时才会被使用。 其默认值为八进制数 `0o666`。

除了与字典类似的方法，`gdbm` 对象还有以下方法：

在 3.11 版本发生变更: 接受 [path-like object](../glossary.md#term-path-like-object) 作为文件名。

gdbm.firstkey()¶
~~~
    

~~~
使用此方法和 `nextkey()` 方法可以循环遍历数据库中的每个键。 遍历的顺序是按照 `gdbm` 的内部哈希值，而不会根据键的值排序。 此方法将返回起始键。

gdbm.nextkey( _key_ )¶
~~~
    

~~~
在遍历中返回 _key_ 之后的的下一个键。 以下代码将打印数据库 `db` 中的每个键，而不会在内存中创建一个包含所有键的列表:
~~~
    
    
~~~
k = db.firstkey()
while k is not None:
    print(k)
    k = db.nextkey(k)
~~~

gdbm.reorganize()¶

    

~~~
如果你进行了大量删除操作并且想要缩减 `gdbm` 文件所使用的空间，此例程将可重新组织数据库。 除非使用此重组功能否则 `gdbm` 对象不会缩减数据库文件大小；在其他情况下，被删除的文件空间将会保留并在添加新的 (键, 值) 对时被重用。

gdbm.sync()¶
~~~
    

~~~
当以快速模式打开数据库时，此方法会将任何未写入数据强制写入磁盘。

gdbm.close()¶
~~~
    

~~~
关闭 `gdbm` 数据库。

gdbm.clear()¶
~~~
    

~~~
Remove all items from the `gdbm` database.

在 3.13 版本加入.

## `dbm.ndbm` \--- 基于 ndbm 的接口¶

**源代码:** [Lib/dbm/ndbm.py](https://github.com/python/cpython/tree/3.12/Lib/dbm/ndbm.py)

* * *

`dbm.ndbm` 模块提供了对 Unix "(n)dbm" 库的接口。 Dbm 对象的行为类似于映射（字典），区别在于其键和值总是被存储为字节串。 打印 `dbm` 对象不会打印出键和值，并且 `items()` 和 `values()` 等方法也不受支持。

此模块可与 "经典classic" ndbm 接口或 GNU GDBM 兼容接口一同使用。 在 Unix 上， **configure** 脚本将尝试定位适当的头文件来简化此模块的构建。

_exception _dbm.ndbm.error¶
~~~
    

~~~
针对 `dbm.ndbm` 专属错误例如 I/O 错误引发。 [`KeyError`](exceptions.md#KeyError "KeyError") 的引发则针对一般映射错误例如指定了不正确的键。

dbm.ndbm.library¶
~~~
    

~~~
所使用的 `ndbm` 实现库的名称。

dbm.ndbm.open( _filename_ [, _flag_ [, _mode_ ]])¶
~~~
    

~~~
打开一个 dbm 数据库并返回 `ndbm` 对象。 _filename_ 参数为数据库文件名称（不带 `.dir` 或 `.pag` 扩展名）。

可选的 _flag_ 参数必须是下列值之一：

值

|

含意  
  
---|---  
  
`'r'`

|

以只读方式打开现有数据库（默认）  
  
`'w'`

|

以读写方式打开现有数据库  
  
`'c'`

|

以读写方式打开数据库，如果不存在则创建它  
  
`'n'`

|

始终创建一个新的空数据库，以读写方式打开  
  
可选的 _mode_ 参数是文件的 Unix 模式，仅在要创建数据库时才会被使用。 其默认值为八进制数 `0o666` (并将被当前的 umask 所修改)。

除了与字典类似的方法，`ndbm` 对象还有以下方法：

在 3.11 版本发生变更: 接受 [path-like object](../glossary.md#term-path-like-object) 作为文件名。

ndbm.close()¶
~~~
    

~~~
关闭 `ndbm` 数据库。

ndbm.clear()¶
~~~
    

~~~
Remove all items from the `ndbm` database.

在 3.13 版本加入.

## `dbm.dumb` \--- 便携式 DBM 实现¶

**源代码:** [Lib/dbm/dumb.py](https://github.com/python/cpython/tree/3.12/Lib/dbm/dumb.py)

备注

`dbm.dumb` 模块的目的是在更健壮的模块不可用时作为 `dbm` 模块的最终回退项。 `dbm.dumb` 不是为高速运行而编写的，也不像其他数据库模块一样被经常使用。

* * *

`dbm.dumb` 模块提供了一个完全以 Python 编写的持久化字典类接口。 不同于 `dbm.gnu` 等其他模块，它不需要外部库。 与其他持久化映射一样，它的键和值也总是被存储为字节串。

该模块定义以下内容：

_exception _dbm.dumb.error¶
~~~
    

~~~
针对 `dbm.dumb` 专属错误例如 I/O 错误引发。 [`KeyError`](exceptions.md#KeyError "KeyError") 的引发则针对一般映射例如指定了不正确的键。

dbm.dumb.open( _filename_ [, _flag_ [, _mode_ ]])¶
~~~
    

~~~
打开一个 `dumbdbm` 数据库并返回 dumbdbm 对象。 _filename_ 参数为数据库文件的主名称（不带任何特定扩展名）。 创建一个 dumbdbm 数据库时将创建多个带有 `.dat` 和 `.dir` 扩展名的文件。

可选的 _flag_ 参数可以是：

值

|

含意  
  
---|---  
  
`'r'`

|

以只读方式打开现有数据库（默认）  
  
`'w'`

|

以读写方式打开现有数据库  
  
`'c'`

|

以读写方式打开数据库，如果不存在则创建它  
  
`'n'`

|

始终创建一个新的空数据库，以读写方式打开  
  
可选的 _mode_ 参数是文件的 Unix 模式，仅在要创建数据库时才会被使用。 其默认值为八进制数 `0o666` (并将被当前的 umask 所修改)。

警告

当载入包含足够巨大/复杂条目的数据库时有可能导致 Python 解释器的崩溃，这是由于 Python AST 编译器有栈深度限制。

在 3.5 版本发生变更: `open()` 在 flag 值为 `'n'` 时将总是创建一个新的数据库。

在 3.8 版本发生变更: 附带 `'r'` 旗标打开的数据库现在将是只读的。 附带 `'r'` 和 `'w'` 旗标的打开操作不会再创建数据库。

在 3.11 版本发生变更: 接受 [path-like object](../glossary.md#term-path-like-object) 作为文件名。

除了 [`collections.abc.MutableMapping`](collections.abc.md#collections.abc.MutableMapping "collections.abc.MutableMapping") 类所提供的方法，`dumbdbm` 对象还提供了以下方法：

dumbdbm.sync()¶
~~~
    

~~~
同步磁盘上的目录和数据文件。 此方法会由 `Shelve.sync()` 方法来调用。

dumbdbm.close()¶
~~~
    

~~~
