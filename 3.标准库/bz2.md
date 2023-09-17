# `bz2` \--- 对 **bzip2** 压缩算法的支持¶

**源代码：** [Lib/bz2.py](https://github.com/python/cpython/tree/3.12/Lib/bz2.py)

* * *

此模块提供了使用 bzip2 压缩算法压缩和解压数据的一套完整的接口。

`bz2` 模块包含：

  * 用于读写压缩文件的 `open()` 函数和 `BZ2File` 类。

  * 用于增量压缩和解压的 `BZ2Compressor` 和 `BZ2Decompressor` 类。

  * 用于一次性压缩和解压的 `compress()` 和 `decompress()` 函数。

## 文件压缩和解压¶

bz2.open( _filename_ , _mode ='rb'_, _compresslevel =9_, _encoding =None_, _errors =None_, _newline =None_)¶

    

~~~
以二进制或文本模式打开 bzip2 压缩文件，返回一个 [file object](../glossary.md#term-file-object)。

和 `BZ2File` 的构造函数类似， _filename_ 参数可以是一个实际的文件名（[`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 对象），或是已有的可供读取或写入的文件对象。

_mode_ 参数可设为二进制模式的 `'r'`、`'rb'`、`'w'`、`'wb'`、`'x'`、`'xb'`、`'a'` 或 `'ab'`，或者文本模式的 `'rt'`、`'wt'`、`'xt'` 或 `'at'`。默认是 `'rb'`。

_compresslevel_ 参数是 1 到 9 的整数，和 `BZ2File` 的构造函数一样。

对于二进制模式，这个函数等价于 `BZ2File` 构造器: `BZ2File(filename, mode, compresslevel=compresslevel)`。 在这种情况下，不可提供 _encoding_ , _errors_ 和 _newline_ 参数。

对于文本模式，将会创建一个 `BZ2File` 对象，并将它包装到一个 [`io.TextIOWrapper`](io.md#io.TextIOWrapper "io.TextIOWrapper") 实例中，此实例带有指定的编码格式、错误处理行为和行结束符。

在 3.3 版本加入.

在 3.4 版本发生变更: 添加了 `'x'` (单独创建) 模式。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

_class _bz2.BZ2File( _filename_ , _mode ='r'_, _*_ , _compresslevel =9_)¶
~~~
    

~~~
用二进制模式打开 bzip2 压缩文件。

如果 _filename_ 是一个 [`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 对象，则打开名称对应的文件目录。 否则的话， _filename_ 应当是一个 [file object](../glossary.md#term-file-object)，它将被用来读取或写入压缩数据。

_mode_ 参数可以是表示读取的 `'r'` (默认值)，表示覆写的 `'w'`，表示单独创建的 `'x'`，或表示添加的 `'a'`。 这些模式还可分别以 `'rb'`, `'wb'`, `'xb'` 和 `'ab'` 的等价形式给出。

如果 _filename_ 是一个文件对象（而不是实际的文件名），则 `'w'` 模式并不会截断文件，而是会等价于 `'a'`。

如果 _mode_ 为 `'w'` 或 `'a'`，则 _compresslevel_ 可以是 `1` 到 `9` 之间的整数，用于指定压缩等级: `1` 产生最低压缩率，而 `9` (默认值) 产生最高压缩率。

如果 _mode_ 为 `'r'`，则输入文件可以为多个压缩流的拼接。

`BZ2File` provides all of the members specified by the [`io.BufferedIOBase`](io.md#io.BufferedIOBase "io.BufferedIOBase"), except for [`detach()`](io.md#io.BufferedIOBase.detach "io.BufferedIOBase.detach") and [`truncate()`](io.md#io.IOBase.truncate "io.IOBase.truncate"). Iteration and the [`with`](../reference/compound_stmts.md#with) statement are supported.

`BZ2File` 还提供了以下方法：

peek([ _n_ ])¶
~~~
    

~~~
返回缓冲的数据而不前移文件位置。 至少将返回一个字节的数据（除非为 EOF）。 实际返回的字节数不确定。

备注

虽然调用 `peek()` 不会改变 `BZ2File` 的文件位置，但它可能改变下层文件对象的位置（举例来说如果 `BZ2File` 是通过传入一个文件对象作为 _filename_ 的话）。

在 3.3 版本加入.

在 3.1 版本发生变更: 添加了对 [`with`](../reference/compound_stmts.md#with) 语句的支持。

在 3.3 版本发生变更: 添加了 `fileno()`, `readable()`, `seekable()`, `writable()`, `read1()` 和 `readinto()` 方法。

在 3.3 版本发生变更: 添加了对 _filename_ 使用 [file object](../glossary.md#term-file-object) 而非实际文件名的支持。

在 3.3 版本发生变更: 添加了 `'a'` (append) 模式，以及对读取多数据流文件的支持。

在 3.4 版本发生变更: 添加了 `'x'` (单独创建) 模式。

在 3.5 版本发生变更: [`read()`](io.md#io.BufferedIOBase.read "io.BufferedIOBase.read") 方法现在接受 `None` 作为参数。

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.9 版本发生变更: _buffering_ 形参已被移除。 它自 Python 3.0 起即被忽略并弃用。 请传入一个打开文件对象来控制文件的打开方式。

_compresslevel_ 形参成为仅限关键字参数。

在 3.10 版本发生变更: 这个类在面对多个同时读取器和写入器时是线程安全的，就如它在 [`gzip`](gzip.md#module-gzip "gzip: Interfaces for gzip compression and decompression using file objects.") 和 [`lzma`](lzma.md#module-lzma "lzma: A Python wrapper for the liblzma compression library.") 中的等价类所具有的特性一样。

## 增量压缩和解压¶

_class _bz2.BZ2Compressor( _compresslevel =9_)¶
~~~
    

~~~
创建一个新的压缩器对象。 此对象可被用来执行增量数据压缩。 对于一次性压缩，请改用 `compress()` 函数。

如果给定 _compresslevel_ ，它必须为 `1` 至 `9` 之间的整数。 默认值为 `9`。

compress( _data_ )¶
~~~
    

~~~
向压缩器对象提供数据。 在可能的情况下返回一段已压缩数据，否则返回空字节串。

当你已结束向压缩器提供数据时，请调用 `flush()` 方法来完成压缩进程。

flush()¶
~~~
    

~~~
结束压缩进程，返回内部缓冲中剩余的压缩完成的数据。

调用此方法之后压缩器对象将不可再被使用。

_class _bz2.BZ2Decompressor¶
~~~
    

~~~
创建一个新的解压缩器对象。 此对象可被用来执行增量数据解压缩。 对于一次性解压缩，请改用 `decompress()` 函数。

备注

这个类不会透明地处理包含多个已压缩数据流的输入，这不同于 `decompress()` 和 `BZ2File`。 如果你需要通过 `BZ2Decompressor` 来解压缩多个数据流输入，你必须为每个数据流都使用新的解压缩器。

decompress( _data_ , _max_length =-1_)¶
~~~
    

~~~
解压缩 _data_ (一个 [bytes-like object](../glossary.md#term-bytes-like-object))，返回字节串形式的解压缩数据。 某些 _data_ 可以在内部被缓冲，以便用于后续的 `decompress()` 调用。 返回的数据应当与之前任何 `decompress()` 调用的输出进行拼接。

如果 _max_length_ 为非负数，将返回至多 _max_length_ 个字节的解压缩数据。 如果达到此限制并且可以产生后续输出，则 `needs_input` 属性将被设为 `False`。 在这种情况下，下一次 `decompress()` 调用提供的 _data_ 可以为 `b''` 以获取更多的输出。

如果所有输入数据都已被解压缩并返回（或是因为它少于 _max_length_ 个字节，或是因为 _max_length_ 为负数），则 `needs_input` 属性将被设为 `True`。

在到达数据流末尾之后再尝试解压缩数据会引发 [`EOFError`](exceptions.md#EOFError "EOFError")。 在数据流末尾之后获取的任何数据都会被忽略并存储至 `unused_data` 属性。

在 3.5 版本发生变更: 添加了 _max_length_ 形参。

eof¶
~~~
    

~~~
若达到了数据流的末尾标记则为 `True`。

在 3.3 版本加入.

unused_data¶
~~~
    

~~~
在压缩数据流的末尾之后获取的数据。

如果在达到数据流末尾之前访问此属性，其值将为 `b''`。

needs_input¶
~~~
    

~~~
如果在要求新的未解压缩输入之前 `decompress()` 方法可以提供更多的解压缩数据则为 `False`。

在 3.5 版本加入.

## 一次性压缩或解压缩¶

bz2.compress( _data_ , _compresslevel =9_)¶
~~~
    

~~~
压缩 _data_ ，此参数为一个 [字节类对象](../glossary.md#term-bytes-like-object)。

如果给定 _compresslevel_ ，它必须为 `1` 至 `9` 之间的整数。 默认值为 `9`。

对于增量压缩，请改用 `BZ2Compressor`。

bz2.decompress( _data_ )¶
~~~
    

~~~
解压缩 _data_ ，此参数为一个 [字节类对象](../glossary.md#term-bytes-like-object)。

如果 _data_ 是多个压缩数据流的拼接，则解压缩所有数据流。

对于增量解压缩，请改用 `BZ2Decompressor`。

在 3.3 版本发生变更: 支持了多数据流的输入。

## 用法示例¶

以下是 `bz2` 模块典型用法的一些示例。

使用 `compress()` 和 `decompress()` 来显示往复式的压缩：
~~~
    
    
~~~shell
>>> import bz2
>>> data = b"""\
... Donec rhoncus quis sapien sit amet molestie. Fusce scelerisque vel augue
... nec ullamcorper. Nam rutrum pretium placerat. Aliquam vel tristique lorem,
... sit amet cursus ante. In interdum laoreet mi, sit amet ultrices purus
... pulvinar a. Nam gravida euismod magna, non varius justo tincidunt feugiat.
... Aliquam pharetra lacus non risus vehicula rutrum. Maecenas aliquam leo
... felis. Pellentesque semper nunc sit amet nibh ullamcorper, ac elementum
... dolor luctus. Curabitur lacinia mi ornare consectetur vestibulum."""
>>> c = bz2.compress(data)
>>> len(data) / len(c)  # Data compression ratio
1.513595166163142
>>> d = bz2.decompress(c)
>>> data == d  # Check equality to original object after round-trip
True
~~~

使用 `BZ2Compressor` 进行增量压缩：

    
    
~~~shell
>>> import bz2
>>> def gen_data(chunks=10, chunksize=1000):
...     """Yield incremental blocks of chunksize bytes."""
...     for _ in range(chunks):
...         yield b"z" * chunksize
...
>>> comp = bz2.BZ2Compressor()
>>> out = b""
>>> for chunk in gen_data():
...     # Provide data to the compressor object
...     out = out + comp.compress(chunk)
...
>>> # Finish the compression process.  Call this once you have
>>> # finished providing data to the compressor.
>>> out = out + comp.flush()
~~~

上面的示例使用了一个相当 "非随机" 的数据流（即 `b"z"` 块的数据流）。 随机数据的压缩率通常很差，而有序、重复的数据通常会产生很高的压缩率。

用二进制模式写入和读取 bzip2 压缩文件：

    
    
~~~shell
>>> import bz2
>>> data = b"""\
... Donec rhoncus quis sapien sit amet molestie. Fusce scelerisque vel augue
... nec ullamcorper. Nam rutrum pretium placerat. Aliquam vel tristique lorem,
... sit amet cursus ante. In interdum laoreet mi, sit amet ultrices purus
... pulvinar a. Nam gravida euismod magna, non varius justo tincidunt feugiat.
... Aliquam pharetra lacus non risus vehicula rutrum. Maecenas aliquam leo
... felis. Pellentesque semper nunc sit amet nibh ullamcorper, ac elementum
... dolor luctus. Curabitur lacinia mi ornare consectetur vestibulum."""
>>> with bz2.open("myfile.bz2", "wb") as f:
...     # Write compressed data to file
...     unused = f.write(data)
...
>>> with bz2.open("myfile.bz2", "rb") as f:
...     # Decompress data from file
...     content = f.read()
...
>>> content == data  # Check equality to original object after round-trip
True
~~~

