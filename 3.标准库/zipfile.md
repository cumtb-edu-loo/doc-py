# `zipfile` \--- 使用ZIP存档¶

**源代码:** [Lib/zipfile/](https://github.com/python/cpython/tree/3.12/Lib/zipfile/)

* * *

ZIP 文件格式是一个常用的归档与压缩标准。 这个模块提供了创建、读取、写入、添加及列出 ZIP 文件的工具。 任何对此模块的进阶使用都将需要理解此格式，其定义参见 [PKZIP 应用程序笔记](https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT)。

此模块目前不能处理分卷 ZIP 文件。它可以处理使用 ZIP64 扩展（超过 4 GB 的 ZIP 文件）的 ZIP 文件。它支持解密 ZIP 归档中的加密文件，但是目前不能创建一个加密的文件。解密非常慢，因为它是使用原生 Python 而不是 C 实现的。

这个模块定义了以下内容：

_exception _zipfile.BadZipFile¶

    

~~~
为损坏的 ZIP 文件抛出的错误。

在 3.2 版本加入.

_exception _zipfile.BadZipfile¶
~~~
    

~~~
`BadZipFile` 的别名，与旧版本 Python 保持兼容性。

自 3.2 版本弃用.

_exception _zipfile.LargeZipFile¶
~~~
    

~~~
当 ZIP 文件需要 ZIP64 功能但是未启用时会抛出此错误。

_class _zipfile.ZipFile
~~~
    

~~~
用于读写 ZIP 文件的类。 欲了解构造函数的描述，参阅段落 ZipFile 对象。

_class _zipfile.Path
~~~
    

~~~
实现了 [`pathlib.Path`](pathlib.md#pathlib.Path "pathlib.Path") 所提供接口的一个子集的类，包括完整的 [`importlib.resources.abc.Traversable`](importlib.resources.abc.md#importlib.resources.abc.Traversable "importlib.resources.abc.Traversable") 接口。

在 3.8 版本加入.

_class _zipfile.PyZipFile
~~~
    

~~~
用于创建包含 Python 库的 ZIP 归档的类。

_class _zipfile.ZipInfo( _filename ='NoName'_, _date_time =(1980, 1, 1, 0, 0, 0)_)¶
~~~
    

~~~
用于表示档案内一个成员信息的类。 此类的实例会由 `ZipFile` 对象的 `getinfo()` 和 `infolist()` 方法返回。 大多数 `zipfile` 模块的用户都不必创建它们，只需使用此模块所创建的实例。 _filename_ 应当是档案成员的全名， _date_time_ 应当是包含六个字段的描述最近修改时间的元组；这些字段的描述请参阅 ZipInfo 对象。

zipfile.is_zipfile( _filename_ )¶
~~~
    

~~~
根据文件的 Magic Number，如果 _filename_ 是一个有效的 ZIP 文件则返回 `True`，否则返回 `False`。 _filename_ 也可能是一个文件或类文件对象。

在 3.1 版本发生变更: 支持文件或类文件对象。

zipfile.ZIP_STORED¶
~~~
    

~~~
未被压缩的归档成员的数字常数。

zipfile.ZIP_DEFLATED¶
~~~
    

~~~
常用的 ZIP 压缩方法的数字常数。需要 [`zlib`](zlib.md#module-zlib "zlib: Low-level interface to compression and decompression routines compatible with gzip.") 模块。

zipfile.ZIP_BZIP2¶
~~~
    

~~~
BZIP2 压缩方法的数字常数。需要 [`bz2`](bz2.md#module-bz2 "bz2: Interfaces for bzip2 compression and decompression.") 模块。

在 3.3 版本加入.

zipfile.ZIP_LZMA¶
~~~
    

~~~
LZMA 压缩方法的数字常数。需要 [`lzma`](lzma.md#module-lzma "lzma: A Python wrapper for the liblzma compression library.") 模块。

在 3.3 版本加入.

备注

ZIP 文件格式规范包括自 2001 年以来对 bzip2 压缩的支持，以及自 2006 年以来对 LZMA 压缩的支持。但是，一些工具（包括较旧的 Python 版本）不支持这些压缩方法，并且可能拒绝完全处理 ZIP 文件，或者无法提取单个文件。

参见

[PKZIP 应用程序笔记](https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT)
~~~
    

~~~
Phil Katz 编写的 ZIP 文件格式文档，此格式和使用的算法的创建者。

[Info-ZIP 主页](https://infozip.sourceforge.net/)
~~~
    

~~~
有关 Info-ZIP 项目的 ZIP 存档程序和开发库的信息。

## ZipFile 对象¶

_class _zipfile.ZipFile( _file_ , _mode ='r'_, _compression =ZIP_STORED_, _allowZip64 =True_, _compresslevel =None_, _*_ , _strict_timestamps =True_, _metadata_encoding =None_)¶
~~~
    

~~~
打开一个 ZIP 文件， _file_ 为一个指向文件的路径（字符串），一个类文件对象或者一个 [path-like object](../glossary.md#term-path-like-object)。

形参 _mode_ 应当为 `'r'` 来读取一个存在的文件，`'w'` 来截断并写入新的文件， `'a'` 来添加到一个存在的文件，或者 `'x'` 来仅新建并写入新的文件。如果 _mode_ 为 `'x'` 并且 _file_ 指向已经存在的文件，则抛出 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError")。如果 _mode_ 为 `'a'` 且 _file_ 为已存在的文件，则格外的文件将被加入。如果 _file_ 不指向 ZIP 文件，之后一个新的 ZIP 归档将被追加为此文件。这是为了将 ZIP 归档添加到另一个文件（例如 `python.exe`）。如果 _mode_ 为 `'a'` 并且文件不存在， 则会新建。如果 _mode_ 为 `'r'` 或 `'a'`， 则文件应当可定位。

_compression_ 是在写入归档时要使用的 ZIP 压缩方法，应为 `ZIP_STORED`, `ZIP_DEFLATED`, `ZIP_BZIP2` 或 `ZIP_LZMA`；不可识别的值将导致引发 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError")。 如果指定了 `ZIP_DEFLATED`, `ZIP_BZIP2` 或 `ZIP_LZMA` 但相应的模块 ([`zlib`](zlib.md#module-zlib "zlib: Low-level interface to compression and decompression routines compatible with gzip."), [`bz2`](bz2.md#module-bz2 "bz2: Interfaces for bzip2 compression and decompression.") 或 [`lzma`](lzma.md#module-lzma "lzma: A Python wrapper for the liblzma compression library.")) 不可用，则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。 默认值为 `ZIP_STORED`。

如果 _allowZip64_ 为 `True` (默认值) 则当 zipfile 大于 4 GiB 时 zipfile 将创建使用 ZIP64 扩展的 ZIP 文件。 如果该参数为 `false` 则当 ZIP 文件需要 ZIP64 扩展时 `zipfile` 将引发异常。

_compresslevel_ 形参控制在将文件写入归档时要使用的压缩等级。 当使用 `ZIP_STORED` 或 `ZIP_LZMA` 时无压缩效果。 当使用 `ZIP_DEFLATED` 时接受整数 `0` 至 `9` (更多信息参见 [`zlib`](zlib.md#zlib.compressobj "zlib.compressobj"))。 当使用 `ZIP_BZIP2` 时接受整数 `1` 至 `9` (更多信息参见 [`bz2`](bz2.md#bz2.BZ2File "bz2.BZ2File"))。

_strict_timestamps_ 参数在设为 `False` 时允许压缩早于 1980-01-01 的文件，代价时会将时间戳设为 1980-01-01。 类似的行为也会对晚于 2107-12-31 的文件发生，时间戳也会被设为该上限值。

当 mode 为 `'r'` 时，可以将 _metadata_encoding_ 设为某个编解码器的名称，它将被用来解码元数据如成员名称和 ZIP 注释等等。

如果创建文件时使用 `'w'`, `'x'` 或 `'a'` 模式并且未向归档添加任何文件就执行了 `closed`，则会将适当的空归档 ZIP 结构写入文件。

ZipFile 也是一个上下文管理器，因此支持 [`with`](../reference/compound_stmts.md#with) 语句。 在这个示例中， _myzip_ 将在 `with` 语句块执行完成之后被关闭 --- 即使是发生了异常:
~~~
    
    
~~~
with ZipFile('spam.zip', 'w') as myzip:
    myzip.write('eggs.txt')
~~~

备注

_metadata_encoding_ 是用于 ZipFile 的实例级设置。 目前无法在成员层级上设置此选项。

该属性是对旧式实现的变通处理，它产生的归档文件名会使用当前语言区域编码格式或代码页（主要是在 Windows 上）。 根据 .ZIP 标准，元数据的编码格式可以通过归档文件标头中的一个旗标指定为 IBM 代码页（默认）或 UTF-8。 该旗标优先于 _metadata_encoding_ ，后者是一个 Python 专属的扩展。

在 3.2 版本加入: 添加了将 `ZipFile` 用作上下文管理器的功能。

在 3.3 版本发生变更: 添加了对 [`bzip2`](bz2.md#module-bz2 "bz2: Interfaces for bzip2 compression and decompression.") 和 [`lzma`](lzma.md#module-lzma "lzma: A Python wrapper for the liblzma compression library.") 压缩的支持。

在 3.4 版本发生变更: 默认启用 ZIP64 扩展。

在 3.5 版本发生变更: 添加了对不可查找数据流的支持。 并添加了对 `'x'` 模式的支持。

在 3.6 版本发生变更: 在此之前，对于不可识别的压缩值将引发普通的 [`RuntimeError`](3.标准库/exceptions.md#RuntimeError "RuntimeError")。

在 3.6.2 版本发生变更: _file_ 形参接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.7 版本发生变更: 添加了 _compresslevel_ 形参。

在 3.8 版本加入: _strict_timestamps_ 仅限关键字参数

在 3.11 版本发生变更: 增加了对指定成员名称编码格式的支持以便在 ZIP 文件的目录和文件标头中读取元数据。

ZipFile.close()¶

    

~~~
关闭归档文件。 你必须在退出程序之前调用 `close()` 否则将不会写入关键记录数据。

ZipFile.getinfo( _name_ )¶
~~~
    

~~~
返回一个 `ZipInfo` 对象，其中包含有关归档成员 _name_ 的信息。 针对一个目前并不包含于归档中的名称调用 `getinfo()` 将会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

ZipFile.infolist()¶
~~~
    

~~~
返回一个列表，其中包含每个归档成员的 `ZipInfo` 对象。 如果是打开一个现有归档则这些对象的排列顺序与它们对应条目在磁盘上的实际 ZIP 文件中的顺序一致。

ZipFile.namelist()¶
~~~
    

~~~
返回按名称排序的归档成员列表。

ZipFile.open( _name_ , _mode ='r'_, _pwd =None_, _*_ , _force_zip64 =False_)¶
~~~
    

~~~
以二进制文件类对象的形式访问一个归档成员。 _name_ 可以是归档内某个文件的名称或是某个 `ZipInfo` 对象。 如果包括了 _mode_ 形参，则它必须为 `'r'` (默认值) 或 `'w'`。 _pwd_ 是用于解密 [`bytes`](stdtypes.md#bytes "bytes") 对象形式的已加密 ZIP 文件的密码。

`open()` 也是一个上下文管理器，因此支持 [`with`](../reference/compound_stmts.md#with) 语句:
~~~
    
    
~~~
with ZipFile('spam.zip') as myzip:
    with myzip.open('eggs.txt') as myfile:
        print(myfile.read())
~~~

如果 _mode_ 为 `'r'` 则文件类对象 (`ZipExtFile`) 将为只读并且提供下列方法: [`read()`](io.md#io.BufferedIOBase.read "io.BufferedIOBase.read"), [`readline()`](io.md#io.IOBase.readline "io.IOBase.readline"), [`readlines()`](io.md#io.IOBase.readlines "io.IOBase.readlines"), [`seek()`](io.md#io.IOBase.seek "io.IOBase.seek"), [`tell()`](io.md#io.IOBase.tell "io.IOBase.tell"), [`__iter__()`](stdtypes.md#container.__iter__ "container.__iter__"), [`__next__()`](stdtypes.md#iterator.__next__ "iterator.__next__")。 这些对象可独立于 ZipFile 进行操作。

如果 `mode='w'` 则返回一个可写入的文件句柄，它将支持 [`write()`](io.md#io.BufferedIOBase.write "io.BufferedIOBase.write") 方法。 当一个可写入的文件句柄被打开时，尝试读写 ZIP 文件中的其他文件将会引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError")。

当写入一个文件时，如果文件大小不能预先确定但是可能超过 2 GiB，可传入 `force_zip64=True` 以确保标头格式能够支持超大文件。 如果文件大小可以预先确定，则在构造 `ZipInfo` 对象时应设置 `file_size`，并将其用作 _name_ 形参。

备注

`open()`, `read()` 和 `extract()` 方法可接受文件名或 `ZipInfo` 对象。 当尝试读取一个包含重复名称成员的 ZIP 文件时你将发现此功能很有好处。

在 3.6 版本发生变更: 移除了对 `mode='U'` 的支持。 请使用 [`io.TextIOWrapper`](io.md#io.TextIOWrapper "io.TextIOWrapper") 以在 [universal newlines](../glossary.md#term-universal-newlines) 模式中读取已压缩的文本文件。

在 3.6 版本发生变更: 现在 `ZipFile.open()` 可以被用来配合 `mode='w'` 选项将文件写入归档。

在 3.6 版本发生变更: 在已关闭的 ZipFile 上调用 `open()` 将引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError")。 在之前的版本中则会引发 [`RuntimeError`](3.标准库/exceptions.md#RuntimeError "RuntimeError")。

ZipFile.extract( _member_ , _path =None_, _pwd =None_)¶

    

~~~
从归档中提取一个成员放入当前工作目录； _member_ 必须是一个成员的完整名称或 `ZipInfo` 对象。 成员的文件信息会尽可能精确地被提取。 _path_ 指定一个要放入的不同目录。 _member_ 可以是一个文件名或 `ZipInfo` 对象。 _pwd_ 是 [`bytes`](stdtypes.md#bytes "bytes") 对象形式的用于解密已加密文件的密码。

返回所创建的经正规化的路径（对应于目录或新文件）。

备注

如果一个成员文件名为绝对路径，则将去掉驱动器/UNC共享点和前导的（反）斜杠，例如: `///foo/bar` 在 Unix 上将变为 `foo/bar`，而 `C:\foo\bar` 在 Windows 上将变为 `foo\bar`。 并且一个成员文件名中的所有 `".."` 都将被移除，例如: `../../foo../../ba..r` 将变为 `foo../ba..r`。 在 Windows 上非法字符 (`:`, `<`, `>`, `|`, `"`, `?`, and `*`) 会被替换为下划线 (`_`)。

在 3.6 版本发生变更: 在已关闭的 ZipFile 上调用 `extract()` 将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 在之前的版本中则将引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

在 3.6.2 版本发生变更: _path_ 形参接受一个 [path-like object](../glossary.md#term-path-like-object)。

ZipFile.extractall( _path =None_, _members =None_, _pwd =None_)¶
~~~
    

~~~
从归档中提取出所有成员放入当前工作目录。 _path_ 指定一个要放入的不同目录。 _members_ 为可选项且必须为 `namelist()` 所返回列表的一个子集。 _pwd_ 是 [`bytes`](stdtypes.md#bytes "bytes") 对象形式的用于解密已加密文件的密码。

警告

绝不要未经预先检验就从不可靠的源中提取归档文件。 这样有可能在 _path_ 之外创建文件，例如某些成员具有以 `"/"` 开始的文件名或带有两个点号 `".."` 的文件名。 此模块会尝试防止这种情况。 参见 `extract()` 的注释。

在 3.6 版本发生变更: 在已关闭的 ZipFile 上调用 `extractall()` 将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 在之前的版本中则将引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

在 3.6.2 版本发生变更: _path_ 形参接受一个 [path-like object](../glossary.md#term-path-like-object)。

ZipFile.printdir()¶
~~~
    

~~~
将归档的目录表打印到 `sys.stdout`。

ZipFile.setpassword( _pwd_ )¶
~~~
    

~~~
将 _pwd_ (一个 [`bytes`](stdtypes.md#bytes "bytes") 对象) 设为用于提取已加密文件的默认密码。

ZipFile.read( _name_ , _pwd =None_)¶
~~~
    

~~~
返回归档中文件 _name_ 的字节数据。 _name_ 是归档中文件的名称，或是一个 `ZipInfo` 对象。 归档必须以读取或追加模式打开。 _pwd_ 为 [`bytes`](stdtypes.md#bytes "bytes") 对象形式的用于解密已加密文件的密码，并且如果指定了该参数则它将覆盖通过 `setpassword()` 设置的默认密码。 在使用 `ZIP_STORED`, `ZIP_DEFLATED`, `ZIP_BZIP2` or `ZIP_LZMA` 以外的压缩方法的 ZipFile 上调用 `read()` 将引发 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError")。 如果相应的压缩模块不可用也会引发错误。

在 3.6 版本发生变更: 在已关闭的 ZipFile 上调用 `read()` 将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 在之前的版本中则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

ZipFile.testzip()¶
~~~
    

~~~
读取归档中的所有文件并检查它们的 CRC 和文件头。 返回第一个已损坏文件的名称，在其他情况下则返回 `None`。

在 3.6 版本发生变更: 在已关闭的 ZipFile 上调用 `testzip()` 将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 在之前的版本中则将引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

ZipFile.write( _filename_ , _arcname =None_, _compress_type =None_, _compresslevel =None_)¶
~~~
    

~~~
将名为 _filename_ 的文件写入归档，给予的归档名为 _arcname_ (默认情况下将与 _filename_ 一致，但是不带驱动器盘符并会移除开头的路径分隔符)。 _compress_type_ 如果给出，它将覆盖作为构造器 _compression_ 形参对于新条目所给出的值。 类似地， _compresslevel_ 如果给出也将覆盖构造器。 归档必须使用 `'w'`, `'x'` 或 `'a'` 模式打开。

备注

ZIP 文件标准在历史上并未指定元数据编码格式，但是强烈建议使用 CP437（原始 IBM PC 编码格式）来实现互操作性。 最近的版本允许（仅）使用 UTF-8。 在这个模块中，如果成员名称包含任何非 ASCII 字符则将自动使用 UTF-8 来写入它们。 不可能用 ASCII 或 UTF-8 以外的任何其他编码格式来写入成员名称。

备注

归档名称应当是基于归档根目录的相对路径，也就是说，它们不应以路径分隔符开头。

备注

如果 `arcname` (或 `filename`，如果 `arcname` 未给出) 包含一个空字节，则归档中该文件的名称将在空字节位置被截断。

备注

文件名开头有一个斜杠可能导致存档文件无法在 Windows 系统上的某些 zip 程序中打开。

在 3.6 版本发生变更: 在使用 `'r'` 模式创建的 ZipFile 或已关闭的 ZipFile 上调用 `write()` 将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 在之前的版本中则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

ZipFile.writestr( _zinfo_or_arcname_ , _data_ , _compress_type =None_, _compresslevel =None_)¶
~~~
    

~~~
将一个文件写入归档。 内容为 _data_ ，它可以是一个 [`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 的实例；如果是 [`str`](stdtypes.md#str "str")，则会先使用 UTF-8 进行编码。 _zinfo_or_arcname_ 可以是它在归档中将被给予的名称，或者是 `ZipInfo` 的实例。 如果它是一个实例，则至少必须给定文件名、日期和时间。 如果它是一个名称，则日期和时间会被设为当前日期和时间。 归档必须以 `'w'`, `'x'` 或 `'a'` 模式打开。

如果给定了 _compress_type_ ，它将会覆盖作为新条目构造器的 _compression_ 形参或在 _zinfo_or_arcname_ (如果是一个 `ZipInfo` 实例) 中所给出的值。 类似地，如果给定了 _compresslevel_ ，它将会覆盖构造器。

备注

当传入一个 `ZipInfo` 实例作为 _zinfo_or_arcname_ 形参时，所使用的压缩方法将为在给定的 `ZipInfo` 实例的 _compress_type_ 成员中指定的方法。 默认情况下，`ZipInfo` 构造器将将此成员设为 `ZIP_STORED`。

在 3.2 版本发生变更: _compress_type_ 参数。

在 3.6 版本发生变更: 在使用 `'r'` 模式创建的 ZipFile 或已关闭的 ZipFile 上调用 `writestr()` 将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 在之前的版本中则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

ZipFile.mkdir( _zinfo_or_directory_ , _mode =511_)¶
~~~
    

~~~
在归档文件内创建一个目录。 如果 _zinfo_or_directory_ 是一个字符串，则会在归档文件中以 _mode_ 参数指定的模式创建目录。 但是，如果 _zinfo_or_directory_ 是一个 `ZipInfo` 实例则 _mode_ 参数将被忽略。

归档文件必须以 `'w'`, `'x'` 或 `'a'` 模式打开。

在 3.11 版本加入.

以下数据属性也是可用的:

ZipFile.filename¶
~~~
    

~~~
ZIP 文件的名称。

ZipFile.debug¶
~~~
    

~~~
要使用的调试输出等级。 这可以设为从 `0` (默认无输出) 到 `3` (最多输出) 的值。 调试信息会被写入 `sys.stdout`。

ZipFile.comment¶
~~~
    

~~~
关联到 ZIP 文件的 [`bytes`](stdtypes.md#bytes "bytes") 对象形式的说明。 如果将说明赋给以 `'w'`, `'x'` 或 `'a'` 模式创建的 `ZipFile` 实例，它的长度不应超过 65535 字节。 超过此长度的说明将被截断。

## Path 对象¶

_class _zipfile.Path( _root_ , _at =''_)¶
~~~
    

~~~
根据 `root` zipfile (它可以是一个 `ZipFile` 实例或适合传给 `ZipFile` 构造器的 `file`) 构造一个 Path 对象。

`at` 指定此 Path 在 zipfile 中的位置，例如 'dir/file.txt', 'dir/' 或 ''。 默认为空字符串，即指定跟目录。

Path 对象会公开 [`pathlib.Path`](pathlib.md#pathlib.Path "pathlib.Path") 对象的下列特性:

Path 对象可以使用 `/` 运算符或 `joinpath` 来进行遍历。

Path.name¶
~~~
    

~~~
最终的路径组成部分。

Path.open( _mode='r'_ , _*_ , _pwd_ , _**_ )¶
~~~
    

~~~
在当前路径上发起调用 `ZipFile.open()`。 允许通过支持的模式打开用于读取或写入文本或二进制数据: 'r', 'w', 'rb', 'wb'。 当以文本模式打开时位置和关键字参数会被传给 [`io.TextIOWrapper`](io.md#io.TextIOWrapper "io.TextIOWrapper")，在其他情况下则会被忽略。 `pwd` 是要传给 `ZipFile.open()` 的 `pwd` 形参。

在 3.9 版本发生变更: 增加了对以文本和二进制模式打开的支持。 现在默认为文本模式。

在 3.11.2 版本发生变更: `encoding` 形参可以作为位置参数来提供而不会引起 [`TypeError`](exceptions.md#TypeError "TypeError")。 这种情况在 3.9 中是会发生的。 需要与未打补丁的 3.10 和 3.11 版保持兼容的代码必须将所有 [`io.TextIOWrapper`](io.md#io.TextIOWrapper "io.TextIOWrapper") 参数，包括 `encoding` 作为关键字参数传入。

Path.iterdir()¶
~~~
    

~~~
枚举当前目录的子目录。

Path.is_dir()¶
~~~
    

~~~
如果当前上下文引用了一个目录则返回 `True`。

Path.is_file()¶
~~~
    

~~~
如果当前上下文引用了一个文件则返回 `True`。

Path.exists()¶
~~~
    

~~~
如果当前上下文引用了 zip 文件内的一个文件或目录则返回 `True`。

Path.suffix¶
~~~
    

~~~
The last dot-separated portion of the final component, if any. This is commonly called the file extension.

在 3.11 版本加入: 添加了 `Path.suffix` 特征属性。

Path.stem¶
~~~
    

~~~
路径的末尾部分，不带文件后缀。

在 3.11 版本加入: 添加了 `Path.stem` 特征属性。

Path.suffixes¶
~~~
    

~~~
A list of the path’s suffixes, commonly called file extensions.

在 3.11 版本加入: 添加了 `Path.suffixes` 特征属性。

Path.read_text( _*_ , _**_ )¶
~~~
    

~~~
读取当前文件为 unicode 文本。 位置和关键字参数会被传递给 [`io.TextIOWrapper`](io.md#io.TextIOWrapper "io.TextIOWrapper") (`buffer` 除外，它将由上下文确定)。

在 3.11.2 版本发生变更: `encoding` 形参可以作为位置参数来提供而不会引起 [`TypeError`](exceptions.md#TypeError "TypeError")。 这种情况在 3.9 中是会发生的。 需要与未打补丁的 3.10 和 3.11 版保持兼容的代码必须将所有 [`io.TextIOWrapper`](io.md#io.TextIOWrapper "io.TextIOWrapper") 参数，包括 `encoding` 作为关键字参数传入。

Path.read_bytes()¶
~~~
    

~~~
读取当前文件为字节串。

Path.joinpath( _* other_)¶
~~~
    

~~~
返回一个新的 Path 对象，其中合并了每个 _other_ 参数。 以下代码是等价的:
~~~
    
    
~~~shell
>>> Path(...).joinpath('child').joinpath('grandchild')
>>> Path(...).joinpath('child', 'grandchild')
>>> Path(...) / 'child' / 'grandchild'
~~~

在 3.10 版本发生变更: 在 3.10 之前，`joinpath` 未被写入文档并且只接受一个形参。

[zipp](https://pypi.org/project/zipp) 项目向较旧版本的 Python 提供了最新路径对象功能的向下移植。 为尽早应用这些改变请使用 `zipp.Path` 来替代 `zipfile.Path`。

## PyZipFile 对象¶

`PyZipFile` 构造器接受与 `ZipFile` 构造器相同的形参，以及一个额外的形参 _optimize_ 。

_class _zipfile.PyZipFile( _file_ , _mode ='r'_, _compression =ZIP_STORED_, _allowZip64 =True_, _optimize =-1_)¶

    

~~~
在 3.2 版本加入: _optimize_ 形参。

在 3.4 版本发生变更: 默认启用 ZIP64 扩展。

实例在 `ZipFile` 对象所具有的方法以外还附加了一个方法:

writepy( _pathname_ , _basename =''_, _filterfunc =None_)¶
~~~
    

~~~
查找 `*.py` 文件并将相应的文件添加到归档。

如果 `PyZipFile` 的 _optimize_ 形参未给定或为 `-1`，则相应的文件为 `*.pyc` 文件，并在必要时进行编译。

如果 `PyZipFile` 的 _optimize_ 形参为 `0`, `1` 或 `2`，则限具有相应优化级别 (参见 [`compile()`](functions.md#compile "compile")) 的文件会被添加到归档，并在必要时进行编译。

如果 _pathname_ 是文件，则文件名必须以 `.py` 为后缀，并且只有 (相应的 `*.pyc`) 文件会被添加到最高层级（不带路径信息）。 如果 _pathname_ 不是以 `.py` 为后缀的文件，则将引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。 如果它是目录，并且该目录不是一个包目录，则所有的 `*.pyc` 文件会被添加到最高层级。 如果目录是一个包目录，则所有的 `*.pyc` 会被添加到包名所表示的文件路径下，并且如果有任何子目录为包目录，则会以排好的顺序递归地添加这些目录。

_basename_ 仅限在内部使用。

如果给定 _filterfunc_ ，则它必须是一个接受单个字符串参数的函数。 在将其添加到归档之前它将被传入每个路径（包括每个单独的完整路径）。 如果 _filterfunc_ 返回假值，则路径将不会被添加，而如果它是一个目录则其内容将被忽略。 例如，如果我们的测试文件全都位于 `test` 目录或以字符串 `test_` 打头，则我们可以使用一个 _filterfunc_ 来排除它们:
~~~
    
    
~~~shell
>>> zf = PyZipFile('myprog.zip')
>>> def notests(s):
...     fn = os.path.basename(s)
...     return (not (fn == 'test' or fn.startswith('test_')))
...
>>> zf.writepy('myprog', filterfunc=notests)
~~~

`writepy()` 方法会产生带有这样一些文件名的归档:

    
    
~~~
string.pyc                   # Top level name
test/__init__.pyc            # Package directory
test/testall.pyc             # Module test.testall
test/bogus/__init__.pyc      # Subpackage directory
test/bogus/myfile.pyc        # Submodule test.bogus.myfile
~~~

在 3.4 版本加入: _filterfunc_ 形参。

在 3.6.2 版本发生变更: _pathname_ 形参接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.7 版本发生变更: 递归排序目录条目。

## ZipInfo 对象¶

`ZipInfo` 类的实例会通过 `getinfo()` 和 `ZipFile` 对象的 `infolist()` 方法返回。 每个对象将存储关于 ZIP 归档的一个成员的信息。

有一个类方法可以为文件系统文件创建 `ZipInfo` 实例:

_classmethod _ZipInfo.from_file( _filename_ , _arcname =None_, _*_ , _strict_timestamps =True_)¶

    

~~~
为文件系统中的文件构造一个 `ZipInfo` 实例，并准备将其添加到一个 zip 文件。

_filename_ 应为文件系统中某个文件或目录的路径。

如果指定了 _arcname_ ，它会被用作归档中的名称。 如果未指定 _arcname_ ，则所用名称与 _filename_ 相同，但将去除任何驱动器盘符和打头的路径分隔符。

_strict_timestamps_ 参数在设为 `False` 时允许压缩早于 1980-01-01 的文件，代价时会将时间戳设为 1980-01-01。 类似的行为也会对晚于 2107-12-31 的文件发生，时间戳也会被设为该上限值。

在 3.6 版本加入.

在 3.6.2 版本发生变更: _filename_ 形参接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.8 版本加入: _strict_timestamps_ 仅限关键字参数

实例具有下列方法和属性:

ZipInfo.is_dir()¶
~~~
    

~~~
如果此归档成员是一个目录则返回 `True`。

这会使用条目的名称：目录应当总是以 `/` 结尾。

在 3.6 版本加入.

ZipInfo.filename¶
~~~
    

~~~
归档中的文件名称。

ZipInfo.date_time¶
~~~
    

~~~
上次修改存档成员的时间和日期。这是六个值的元组：

索引

|

值  
  
---|---  
  
`0`

|

Year (>= 1980)  
  
`1`

|

月（1为基数）  
  
`2`

|

月份中的日期（1为基数）  
  
`3`

|

小时（0为基数）  
  
`4`

|

分钟（0为基数）  
  
`5`

|

秒（0为基数）  
  
备注

ZIP文件格式不支持1980年以前的时间戳。

ZipInfo.compress_type¶
~~~
    

~~~
归档成员的压缩类型。

ZipInfo.comment¶
~~~
    

~~~
[`bytes`](stdtypes.md#bytes "bytes") 对象形式的单个归档成员的注释。

ZipInfo.extra¶
~~~
    

~~~
扩展字段数据。 [PKZIP Application Note](https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT) 包含一些保存于该 [`bytes`](stdtypes.md#bytes "bytes") 对象中的内部结构的注释。

ZipInfo.create_system¶
~~~
    

~~~
创建 ZIP 归档所用的系统。

ZipInfo.create_version¶
~~~
    

~~~
创建 ZIP 归档所用的 PKZIP 版本。

ZipInfo.extract_version¶
~~~
    

~~~
需要用来提取归档的 PKZIP 版本。

ZipInfo.reserved¶
~~~
    

~~~
必须为零。

ZipInfo.flag_bits¶
~~~
    

~~~
ZIP 标志位。

ZipInfo.volume¶
~~~
    

~~~
文件头的分卷号。

ZipInfo.internal_attr¶
~~~
    

~~~
内部属性。

ZipInfo.external_attr¶
~~~
    

~~~
外部文件属性。

ZipInfo.header_offset¶
~~~
    

~~~
文件头的字节偏移量。

ZipInfo.CRC¶
~~~
    

~~~
未压缩文件的 CRC-32。

ZipInfo.compress_size¶
~~~
    

~~~
已压缩数据的大小。

ZipInfo.file_size¶
~~~
    

~~~
未压缩文件的大小。

## 命令行接口¶

`zipfile` 模块提供了简单的命令行接口用于与 ZIP 归档的交互。

如果你想要创建一个新的 ZIP 归档，请在 `-c` 选项后指定其名称然后列出应当被包含的文件名:
~~~
    
    
~~~
$ python -m zipfile -c monty.zip spam.txt eggs.txt
~~~

传入一个目录也是可接受的:

    
    
~~~
$ python -m zipfile -c monty.zip life-of-brian_1979/
~~~

如果你想要将一个 ZIP 归档提取到指定的目录，请使用 `-e` 选项:

    
    
~~~
$ python -m zipfile -e monty.zip target-dir/
~~~

要获取一个 ZIP 归档中的文件列表，请使用 `-l` 选项:

    
    
~~~
$ python -m zipfile -l monty.zip
~~~

### 命令行选项¶

-l <zipfile>¶
\--list <zipfile>¶

    

~~~
列出一个 zipfile 中的文件名。

-c <zipfile> <source1> ... <sourceN>¶
\--create <zipfile> <source1> ... <sourceN>¶
~~~
    

~~~
基于源文件创建 zipfile。

-e <zipfile> <output_dir>¶
\--extract <zipfile> <output_dir>¶
~~~
    

~~~
将 zipfile 提取到目标目录中。

-t <zipfile>¶
\--test <zipfile>¶
~~~
    

~~~
检测 zipfile 是否有效。

\--metadata-encoding <encoding>¶
~~~
    

~~~
