# `gzip` \--- 对 **gzip** 格式的支持¶

**源代码：** [Lib/gzip.py](https://github.com/python/cpython/tree/3.12/Lib/gzip.py)

* * *

此模块提供的简单接口帮助用户压缩和解压缩文件，功能类似于 GNU 应用程序 **gzip** 和 **gunzip** 。

数据压缩由 [`zlib`](zlib.md#module-zlib "zlib: Low-level interface to compression and decompression routines compatible with gzip.") 模块提供。

`gzip` 模块提供 `GzipFile` 类和 `open()`、`compress()`、`decompress()` 几个便利的函数。`GzipFile` 类可以读写 **gzip** 格式的文件，还能自动压缩和解压缩数据，这让操作压缩文件如同操作普通的 [file object](../glossary.md#term-file-object) 一样方便。

注意，此模块不支持部分可以被 **gzip** 和 **gunzip** 解压的格式，如利用 **compress** 或 **pack** 压缩所得的文件。

这个模块定义了以下内容：

gzip.open( _filename_ , _mode ='rb'_, _compresslevel =9_, _encoding =None_, _errors =None_, _newline =None_)¶

    

~~~
以二进制方式或者文本方式打开一个 gzip 格式的压缩文件，返回一个 [file object](../glossary.md#term-file-object)。

_filename_ 参数可以是一个实际的文件名（一个 [`str`](stdtypes.md#str "str") 对象或者 [`bytes`](stdtypes.md#bytes "bytes") 对象），或者是一个用来读写的已存在的文件对象。

_mode_ 参数可以是二进制模式： `'r'`, `'rb'`, `'a'`, `'ab'`, `'w'`, `'wb'`, `'x'` or `'xb'` , 或者是文本模式 `'rt'`, `'at'`, `'wt'`, or `'xt'`。默认值是 `'rb'`。

The _compresslevel_ argument is an integer from 0 to 9, as for the `GzipFile` constructor.

对于二进制模式，这个函数等价于 `GzipFile` 构造器：`GzipFile(filename, mode, compresslevel)`。在这个例子中， _encoding_ , _errors_ 和 _newline_ 三个参数一定不要设置。

对于文本模式，将会创建一个 `GzipFile` 对象，并将它封装到一个 [`io.TextIOWrapper`](io.md#io.TextIOWrapper "io.TextIOWrapper") 实例中， 这个实例默认了指定编码，错误抓获行为和行。

在 3.3 版本发生变更: 支持 _filename_ 为一个文件对象，支持文本模式和 _encoding_ , _errors_ 和 _newline_ 参数。

在 3.4 版本发生变更: 支持 `'x'`, `'xb'` 和 `'xt'` 三种模式。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

_exception _gzip.BadGzipFile¶
~~~
    

~~~
针对无效 gzip 文件引发的异常。 它继承自 [`OSError`](exceptions.md#OSError "OSError")。 针对无效 gzip 文件也可能引发 [`EOFError`](exceptions.md#EOFError "EOFError") 和 [`zlib.error`](zlib.md#zlib.error "zlib.error")。

在 3.8 版本加入.

_class _gzip.GzipFile( _filename =None_, _mode =None_, _compresslevel =9_, _fileobj =None_, _mtime =None_)¶
~~~
    

~~~
`GzipFile` 类的构造器，它模拟了 [file object](../glossary.md#term-file-object) 的大部分方法，但 [`truncate()`](io.md#io.IOBase.truncate "io.IOBase.truncate") 方法除外。 _fileobj_ 和 _filename_ 中至少有一个必须为非空值。

新的实例基于 _fileobj_ ，它可以是一个普通文件，一个 [`io.BytesIO`](io.md#io.BytesIO "io.BytesIO") 对象，或者任何一个与文件相似的对象。当 _filename_ 是一个文件对象时，它的默认值是 `None`。

当 _fileobj_ 为 `None` 时， _filename_ 参数只用于 **gzip** 文件头中，这个文件有可能包含未压缩文件的源文件名。如果文件可以被识别，默认 _fileobj_ 的文件名；否则默认为空字符串，在这种情况下文件头将不包含源文件名。

_mode_ 参数可以是 `'r'`, `'rb'`, `'a'`, `'ab'`, `'w'`, `'wb'`, `'x'` 或 `'xb'` 中的一个，具体取决于文件将被读取还是被写入。 如果可识别则默认为 _fileobj_ 的模式；否则默认为 `'rb'`。 在未来的 Python 发布版中将不再使用 _fileobj_ 的模式。 最好总是指定 _mode_ 为写入模式。

需要注意的是，文件默认使用二进制模式打开。 如果要以文本模式打开一个压缩文件，请使用 `open()` 方法 (或者使用 [`io.TextIOWrapper`](io.md#io.TextIOWrapper "io.TextIOWrapper") 包装 `GzipFile`)。

_compresslevel_ 参数是一个从 `0` 到 `9` 的整数，用于控制压缩等级；`1` 最快但压缩比例最小，`9` 最慢但压缩比例最大。 `0` 不压缩。默认为 `9`。

_mtime_ 参数是一个可选的数字时间戳用于写入流的最后修改字段，。 _mtime_ 只在压缩模式中使用。如果省略或者值为 `None`，则使用当前时间。更多细节，详见 `mtime` 属性。

调用 `GzipFile` 的 `close()` 方法不会关闭 _fileobj_ ，因为你可以希望增加其它内容到已经压缩的数中。你可以将一个 [`io.BytesIO`](io.md#io.BytesIO "io.BytesIO") 对象作为 _fileobj_ ，也可以使用 [`io.BytesIO`](io.md#io.BytesIO "io.BytesIO") 的 [`getvalue()`](io.md#io.BytesIO.getvalue "io.BytesIO.getvalue") 方法从内存缓存中恢复数据。

`GzipFile` 支持 [`io.BufferedIOBase`](io.md#io.BufferedIOBase "io.BufferedIOBase") 接口，包括迭代和 [`with`](../reference/compound_stmts.md#with) 语句。 只有 [`truncate()`](io.md#io.IOBase.truncate "io.IOBase.truncate") 方法未被实现。

`GzipFile` 还提供了以下的方法和属性:

peek( _n_ )¶
~~~
    

~~~
在不移动文件指针的情况下读取 _n_ 个未压缩字节。最多只有一个单独的读取流来服务这个方法调用。返回的字节数不一定刚好等于要求的数量。

备注

调用 `peek()` 并没有改变 `GzipFile` 的文件指针，它可能改变潜在文件对象(例如： `GzipFile` 使用 _fileobj_ 参数进行初始化)。

在 3.2 版本加入.

mtime¶
~~~
    

~~~
在解压的过程中，最后修改时间字段的值可能来自于这个属性，以整数的形式出现。在读取任何文件头信息前，初始值为 `None`。

所有 **gzip** 东方压缩流中必须包含时间戳这个字段。以便于像 **gunzip** 这样的程序可以使用时间戳。格式与 [`time.time()`](time.md#time.time "time.time") 的返回值和 [`os.stat()`](os.md#os.stat "os.stat") 对象的 [`st_mtime`](os.md#os.stat_result.st_mtime "os.stat_result.st_mtime") 属性值一样。

name¶
~~~
    

~~~
指向磁盘上 gzip 文件的路径，为 [`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 对象。 等价于原始输入路径上 [`os.fspath()`](os.md#os.fspath "os.fspath") 的输出，不带其他标准化、解析或扩展。

在 3.1 版本发生变更: 支持 [`with`](../reference/compound_stmts.md#with) 语句，构造器参数 _mtime_ 和 `mtime` 属性。

在 3.2 版本发生变更: 添加了对零填充和不可搜索文件的支持。

在 3.3 版本发生变更: 实现 [`io.BufferedIOBase.read1()`](io.md#io.BufferedIOBase.read1 "io.BufferedIOBase.read1") 方法。

在 3.4 版本发生变更: 支持 `'x'` and `'xb'` 两种模式。

在 3.5 版本发生变更: 支持写入任意 [bytes-like objects](../glossary.md#term-bytes-like-object)。[`read()`](io.md#io.BufferedIOBase.read "io.BufferedIOBase.read") 方法可以接受 `None` 为参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.12 版本发生变更: 移除 `filename` 属性，改用 `name` 属性。

自 3.9 版本弃用: 打开 `GzipFile` 用于写入而不指定 _mode_ 参数的做法已被弃用。

gzip.compress( _data_ , _compresslevel =9_, _*_ , _mtime =None_)¶
~~~
    

~~~
压缩 _data_ ，返回一个包含已压缩数据的 [`bytes`](stdtypes.md#bytes "bytes") 对象。 _compresslevel_ 和 _mtime_ 与上述 `GzipFile` 构造器的对应参数含义相同。 当 _mtime_ 设为 `0` 时，此函数将等价于 [`zlib.compress()`](zlib.md#zlib.compress "zlib.compress") 的 _wbits_ 设为 `31`。 zlib 函数速度更快一些。

在 3.2 版本加入.

在 3.8 版本发生变更: 添加了 _mtime_ 形参用于可重复的输出。

在 3.11 版本发生变更: 通过一次性压缩全部数据而不是通过流方式提高了速度。 将 _mtime_ 设为 `0` 的调用将被委托给 [`zlib.compress()`](zlib.md#zlib.compress "zlib.compress") 以提高速度。

gzip.decompress( _data_ )¶
~~~
    

~~~
解压缩 _data_ ，返回一个包含已解压数据的 [`bytes`](stdtypes.md#bytes "bytes") 对象。 此函数可以解压缩多成员的 gzip 数据（即多个 gzip 块拼接在一起）。 当数据确定只包含一个成员时则 _wbits_ 设为 31 的 [`zlib.decompress()`](zlib.md#zlib.decompress "zlib.decompress") 函数更快一些。

在 3.2 版本加入.

在 3.11 版本发生变更: 通过一次性解压缩全部数据而不是通过流方式提高了速度。

## 用法示例¶

读取压缩文件示例：
~~~
    
    
~~~
import gzip
with gzip.open('/home/joe/file.txt.gz', 'rb') as f:
    file_content = f.read()
~~~

创建GZIP 文件示例：

    
    
~~~
import gzip
content = b"Lots of content here"
with gzip.open('/home/joe/file.txt.gz', 'wb') as f:
    f.write(content)
~~~

使用 GZIP 压缩已有的文件示例：

    
    
~~~
import gzip
import shutil
with open('/home/joe/file.txt', 'rb') as f_in:
    with gzip.open('/home/joe/file.txt.gz', 'wb') as f_out:
        shutil.copyfileobj(f_in, f_out)
~~~

使用 GZIP 压缩二进制字符串示例：

    
    
~~~
import gzip
s_in = b"Lots of content here"
s_out = gzip.compress(s_in)
~~~

参见

模块 [`zlib`](zlib.md#module-zlib "zlib: Low-level interface to compression and decompression routines compatible with gzip.")

    

~~~
支持 **gzip** 格式所需要的基本压缩模块。

## 命令行界面¶

`gzip` 模块提供了简单的命令行界面用于压缩和解压缩文件。

在执行后 `gzip` 模块会保留输入文件。

在 3.8 版本发生变更: 添加一个带有用法说明的新命令行界面命令。 默认情况下，当你要执行 CLI 时，默认压缩等级为 6。

### 命令行选项¶

file¶
~~~
    

~~~
如果未指定 _file_ ，则从 [`sys.stdin`](sys.md#sys.stdin "sys.stdin") 读取。

\--fast¶
~~~
    

~~~
指明最快速的压缩方法（较低压缩率）。

\--best¶
~~~
    

~~~
指明最慢速的压缩方法（最高压缩率）。

-d, \--decompress¶
~~~

解压缩给定的文件。

-h, \--help¶
    

显示帮助消息。

