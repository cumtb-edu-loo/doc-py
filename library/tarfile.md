# `tarfile` \--- 读写tar归档文件¶

**源代码:** [Lib/tarfile.py](https://github.com/python/cpython/tree/3.12/Lib/tarfile.py)

* * *

`tarfile` 模块可以用来读写 tar 归档，包括使用 gzip, bz2 和 lzma 压缩的归档。 请使用 [`zipfile`](zipfile.md#module-zipfile "zipfile: Read and write ZIP-format archive files.") 模块来读写 `.zip` 文件，或者使用 [shutil](shutil.md#archiving-operations) 的高层级函数。

一些事实和数字:

  * 读写 [`gzip`](gzip.md#module-gzip "gzip: Interfaces for gzip compression and decompression using file objects."), [`bz2`](bz2.md#module-bz2 "bz2: Interfaces for bzip2 compression and decompression.") 和 [`lzma`](lzma.md#module-lzma "lzma: A Python wrapper for the liblzma compression library.") 解压的归档要求相应的模块可用。

  * 支持读取 / 写入 POSIX.1-1988 (ustar) 格式。

  * 对 GNU tar 格式的读/写支持，包括 _longname_ 和 _longlink_ 扩展，对所有种类 _sparse_ 扩展的只读支持，包括 sparse 文件的恢复。

  * 对 POSIX.1-2001 (pax) 格式的读/写支持。

  * 处理目录、正常文件、硬链接、符号链接、fifo 管道、字符设备和块设备，并且能够获取和恢复文件信息例如时间戳、访问权限和所有者等。

在 3.3 版本发生变更: 添加了对 [`lzma`](lzma.md#module-lzma "lzma: A Python wrapper for the liblzma compression library.") 压缩的支持。

在 3.12 版本发生变更: 归档文件使用 过滤器 来提取，这将可以限制令人惊讶/危险的特性，或确认它们符合预期并且归档文档受到完全信任。 在默认情况下，归档文档将受到完全信任，但此默认选项已被弃用并计划在 Python 3.14 中改变。

tarfile.open( _name =None_, _mode ='r'_, _fileobj =None_, _bufsize =10240_, _** kwargs_)¶

    

~~~
针对路径名 _name_ 返回 `TarFile` 对象。 有关 `TarFile` 对象以及所允许的关键字参数的详细信息请参阅 TarFile 对象。

_mode_ 必须是 `'filemode[:compression]'` 形式的字符串，其默认值为 `'r'`。 以下是模式组合的完整列表:

模式

|

action  
  
---|---  
  
`'r' or 'r:*'`

|

打开和读取使用透明压缩（推荐）。  
  
`'r:'`

|

打开和读取不使用压缩。  
  
`'r:gz'`

|

打开和读取使用gzip 压缩。  
  
`'r:bz2'`

|

打开和读取使用bzip2 压缩。  
  
`'r:xz'`

|

打开和读取使用lzma 压缩。  
  
`'x'` 或 `'x:'`

|

单独创建一个 tarfile 而不带压缩。 如果它已经存在则会引发 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError") 异常。  
  
`'x:gz'`

|

使用 gzip 压缩创建一个 tarfile。 如果它已经存在则会引发 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError") 异常。  
  
`'x:bz2'`

|

使用 bzip2 压缩创建一个 tarfile。 如果它已经存在则会引发 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError") 异常。  
  
`'x:xz'`

|

使用 lzma 压缩创建一个 tarfile。 如果它已经存在则会引发 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError") 异常。  
  
`'a' or 'a:'`

|

打开以便在没有压缩的情况下追加。如果文件不存在，则创建该文件。  
  
`'w' or 'w:'`

|

打开用于未压缩的写入。  
  
`'w:gz'`

|

打开用于 gzip 压缩的写入。  
  
`'w:bz2'`

|

打开用于 bzip2 压缩的写入。  
  
`'w:xz'`

|

打开用于 lzma 压缩的写入。  
  
请注意 `'a:gz'`, `'a:bz2'` 或 `'a:xz'` 是不可能的组合。 如果 _mode_ 不适用于打开特定（压缩的）文件用于读取，则会引发 `ReadError`。 请使用 _mode_ `'r'` 来避免这种情况。 如果某种压缩方法不受支持，则会引发 `CompressionError`。

如果指定了 _fileobj_ ，它会被用作对应于 _name_ 的以二进制模式打开的 [file object](../glossary.md#term-file-object) 的替代。 它会被设定为处在位置 0。

对于 `'w:gz'`, `'x:gz'`, `'w|gz'`, `'w:bz2'`, `'x:bz2'`, `'w|bz2'` 等模式，`tarfile.open()` 接受关键字参数 _compresslevel_ (默认值为 `9`) 用于指定文件的压缩等级。

对于 `'w:xz'` 和 `'x:xz'` 模式，`tarfile.open()` 接受关键字参数 _preset_ 来指定文件的压缩等级。

针对特殊的目的，还存在第二种 _mode_ 格式: `'filemode|[compression]'`。 `tarfile.open()` 将返回一个将其数据作为数据块流来处理的 `TarFile` 对象。 对此文件将不能执行随机查找。 如果给定了 _fileobj_ ，它可以是任何具有 `read()` 或 `write()` 方法 (由 _mode_ 确定) 的对象。 _bufsize_ 指定块大小，默认值为 `20 * 512` 字节。 可与此格式组合使用的有 `sys.stdin`, 套接字 [file object](../glossary.md#term-file-object) 或磁带设备等。 但是，对于这样的 `TarFile` 对象存在不允许随机访问的限制，参见 例子。 目前可用的模式如下:

模式

|

动作  
  
---|---  
  
`'r|*'`

|

打开 tar 块的 _流_ 以进行透明压缩读取。  
  
`'r|'`

|

打开一个未压缩的 tar 块的 _stream_ 用于读取。  
  
`'r|gz'`

|

打开一个 gzip 压缩的 _stream_ 用于读取。  
  
`'r|bz2'`

|

打开一个 bzip2 压缩的 _stream_ 用于读取。  
  
`'r|xz'`

|

打开一个 lzma 压缩 _stream_ 用于读取。  
  
`'w|'`

|

打开一个未压缩的 _stream_ 用于写入。  
  
`'w|gz'`

|

打开一个 gzip 压缩的 _stream_ 用于写入。  
  
`'w|bz2'`

|

打开一个 bzip2 压缩的 _stream_ 用于写入。  
  
`'w|xz'`

|

打开一个 lzma 压缩的 _stream_ 用于写入。  
  
在 3.5 版本发生变更: 添加了 `'x'` (单独创建) 模式。

在 3.6 版本发生变更: _name_ 形参接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.12 版本发生变更: _compresslevel_ 关键字参数也适用于流式数据。

_class _tarfile.TarFile
~~~
    

~~~
用于读取和写入 tar 归档的类。 请不要直接使用这个类：而要使用 `tarfile.open()`。 参见 TarFile 对象。

tarfile.is_tarfile( _name_ )¶
~~~
    

~~~
如果 _name_ 是一个 `tarfile` 能读取的 tar 归档文件则返回 [`True`](constants.md#True "True")。 _name_ 可以为 [`str`](stdtypes.md#str "str")，文件或文件类对象。

在 3.9 版本发生变更: 支持文件或类文件对象。

`tarfile` 模块定义了以下异常:

_exception _tarfile.TarError¶
~~~
    

~~~
所有 `tarfile` 异常的基类。

_exception _tarfile.ReadError¶
~~~
    

~~~
当一个不能被 `tarfile` 模块处理或者因某种原因而无效的 tar 归档被打开时将被引发。

_exception _tarfile.CompressionError¶
~~~
    

~~~
当一个压缩方法不受支持或者当数据无法被正确解码时将被引发。

_exception _tarfile.StreamError¶
~~~
    

~~~
当达到流式 `TarFile` 对象的典型限制时将被引发。

_exception _tarfile.ExtractError¶
~~~
    

~~~
当使用 `TarFile.extract()` 时针对 _non-fatal_ 所引发的异常，但是仅限 `TarFile.errorlevel``== 2`。

_exception _tarfile.HeaderError¶
~~~
    

~~~
如果获取的缓冲区无效则会由 `TarInfo.frombuf()` 引发的异常。

_exception _tarfile.FilterError¶
~~~
    

~~~
被过滤器 拒绝 的成员的基类。

tarinfo¶
~~~
    

~~~
关于过滤器拒绝提取的成员的信息，为 TarInfo 类型。

_exception _tarfile.AbsolutePathError¶
~~~
    

~~~
在拒绝提取具有绝对路径的成员时引发。

_exception _tarfile.OutsideDestinationError¶
~~~
    

~~~
在拒绝提取目标目录以外的成员时引发。

_exception _tarfile.SpecialFileError¶
~~~
    

~~~
在拒绝提取特殊文件（例如设备或管道）时引发。

_exception _tarfile.AbsoluteLinkError¶
~~~
    

~~~
在拒绝提取具有绝对路径的符号链接时引发。

_exception _tarfile.LinkOutsideDestinationError¶
~~~
    

~~~
在拒绝提取指向目标目录以外的符号链接时引发。

以下常量在模块层级上可用:

tarfile.ENCODING¶
~~~
    

~~~
默认的字符编码格式：在 Windows 上为 `'utf-8'`，其他系统上则为 [`sys.getfilesystemencoding()`](sys.md#sys.getfilesystemencoding "sys.getfilesystemencoding") 所返回的值。

以下常量各自定义了一个 `tarfile` 模块能够创建的 tar 归档格式。 相关细节请参阅 受支持的 tar 格式 小节。

tarfile.USTAR_FORMAT¶
~~~
    

~~~
POSIX.1-1988 (ustar) 格式。

tarfile.GNU_FORMAT¶
~~~
    

~~~
GNU tar 格式。

tarfile.PAX_FORMAT¶
~~~
    

~~~
POSIX.1-2001 (pax) 格式。

tarfile.DEFAULT_FORMAT¶
~~~
    

~~~
用于创建归档的默认格式。 目前为 `PAX_FORMAT`。

在 3.8 版本发生变更: 新归档的默认格式已更改为 `PAX_FORMAT` 而不再是 `GNU_FORMAT`。

参见

模块 [`zipfile`](zipfile.md#module-zipfile "zipfile: Read and write ZIP-format archive files.")
~~~
    

~~~
[`zipfile`](zipfile.md#module-zipfile "zipfile: Read and write ZIP-format archive files.") 标准模块的文档。

[归档操作](shutil.md#archiving-operations)
~~~
    

~~~
标准 [`shutil`](shutil.md#module-shutil "shutil: High-level file operations, including copying.") 模块所提供的高层级归档工具的文档。

[GNU tar manual, Basic Tar Format](https://www.gnu.org/software/tar/manual/html_node/Standard.md)
~~~
    

~~~
针对 tar 归档文件的文档，包含 GNU tar 扩展。

## TarFile 对象¶

`TarFile` 对象提供了一个 tar 归档的接口。 一个 tar 归档就是数据块的序列。 一个归档成员（被保存文件）是由一个标头块加多个数据块组成的。 一个文件可以在一个 tar 归档中多次被保存。 每个归档成员都由一个 `TarInfo` 对象来代表，详情参见 TarInfo 对象。

`TarFile` 对象可在 [`with`](../reference/compound_stmts.md#with) 语句中作为上下文管理器使用。 当语句块结束时它将自动被关闭。 请注意在发生异常事件时被打开用于写入的归档将不会被终结；只有内部使用的文件对象将被关闭。 相关用例请参见 例子。

在 3.2 版本加入: 添加了对上下文管理器协议的支持。

_class _tarfile.TarFile( _name =None_, _mode ='r'_, _fileobj =None_, _format =DEFAULT_FORMAT_, _tarinfo =TarInfo_, _dereference =False_, _ignore_zeros =False_, _encoding =ENCODING_, _errors ='surrogateescape'_, _pax_headers =None_, _debug =0_, _errorlevel =1_, _stream =False_)¶
~~~
    

~~~
下列所有参数都是可选项并且也可作为实例属性来访问。

_name_ 是归档的路径名称。 _name_ 可以是一个 [path-like object](../glossary.md#term-path-like-object)。 如果给定了 _fileobj_ 则它可以被省略。 在此情况下，如果对象的 `name` 属性存在则它会被使用。

_mode_ 可以为 `'r'` 表示从现有归档读取，`'a'` 表示将数据追加到现有文件，`'w'` 表示创建新文件覆盖现有文件，或者 `'x'` 表示仅在文件不存在时创建新文件。

如果给定了 _fileobj_ ，它会被用于读取或写入数据。 如果可以被确定，则 _mode_ 会被 _fileobj_ 的模式所覆盖。 _fileobj_ 的使用将从位置 0 开始。

备注

当 `TarFile` 被关闭时， _fileobj_ 不会被关闭。

_format_ 控制用于写入的归档格式。 它必须为在模块层级定义的常量 `USTAR_FORMAT`, `GNU_FORMAT` 或 `PAX_FORMAT` 中的一个。 当读取时，格式将被自动检测，即使单个归档中存在不同的格式。

_tarinfo_ 参数可以被用来将默认的 `TarInfo` 类替换为另一个。

如果 _dereference_ 为 [`False`](constants.md#False "False")，则会将符号链接和硬链接添加到归档中。 如果为 [`True`](constants.md#True "True")，则会将目标文件的内容添加到归档中。 在不支持符号链接的系统上参数将不起作用。

如果 _ignore_zeros_ 为 [`False`](constants.md#False "False")，则会将空的数据块当作归档的末尾来处理。 如果为 [`True`](constants.md#True "True")，则会跳过空的（和无效的）数据块并尝试获取尽可能多的成员。 此参数仅适用于读取拼接的或损坏的归档。

_debug_ 可设为从 `0` (无调试消息) 到 `3` (全部调试消息)。 消息会被写入到 `sys.stderr`。

_errorlevel_ 控制如何处理提取错误，参见 `相应的属性`。

_encoding_ 和 _errors_ 参数定义了读取或写入归档所使用的字符编码格式以及要如何处理转换错误。 默认设置将适用于大多数用户。 要深入了解详情可参阅 Unicode 问题 小节。

可选的 _pax_headers_ 参数是字符串的字典，如果 _format_ 为 `PAX_FORMAT` 它将被作为 pax 全局标头被添加。

If _stream_ is set to [`True`](constants.md#True "True") then while reading the archive info about files in the archive are not cached, saving memory.

在 3.2 版本发生变更: 使用 `'surrogateescape'` 作为 _errors_ 参数的默认值。

在 3.5 版本发生变更: 添加了 `'x'` (单独创建) 模式。

在 3.6 版本发生变更: _name_ 形参接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.13 版本发生变更: Add the _stream_ parameter.

_classmethod _TarFile.open( _..._ )¶
~~~
    

~~~
作为替代的构造器。 `tarfile.open()` 函数实际上是这个类方法的快捷方式。

TarFile.getmember( _name_ )¶
~~~
    

~~~
返回成员 _name_ 的 `TarInfo` 对象。 如果 _name_ 在归档中找不到，则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

备注

如果一个成员在归档中出现超过一次，它的最后一次出现会被视为是最新的版本。

TarFile.getmembers()¶
~~~
    

~~~
以 `TarInfo` 对象列表的形式返回归档的成员。 列表的顺序与归档中成员的顺序一致。

TarFile.getnames()¶
~~~
    

~~~
以名称列表的形式返回成员。 它的顺序与 `getmembers()` 所返回列表的顺序一致。

TarFile.list( _verbose =True_, _*_ , _members =None_)¶
~~~
    

~~~
将内容清单打印到 `sys.stdout`。 如果 _verbose_ 为 [`False`](constants.md#False "False")，则将只打印成员名称。 如果为 [`True`](constants.md#True "True")，则输出将类似于 **ls -l** 的输出效果。 如果给定了可选的 _members_ ，它必须为 `getmembers()` 所返回的列表的一个子集。

在 3.5 版本发生变更: 添加了 _members_ 形参。

TarFile.next()¶
~~~
    

~~~
当 `TarFile` 被打开用于读取时，以 `TarInfo` 对象的形式返回归档的下一个成员。 如果不再有可用对象则返回 [`None`](constants.md#None "None")。

TarFile.extractall( _path ='.'_, _members =None_, _*_ , _numeric_owner =False_, _filter =None_)¶
~~~
    

~~~
将归档中的所有成员提取到当前工作目录或 _path_ 目录。 如果给定了可选的 _members_ ，则它必须为 `getmembers()` 所返回的列表的一个子集。 字典信息例如所有者、修改时间和权限会在所有成员提取完毕后被设置。 这样做是为了避免两个问题：目录的修改时间会在每当在其中创建文件时被重置。 并且如果目录的权限不允许写入，提取文件到目录的操作将失败。

如果 _numeric_owner_ 为 [`True`](constants.md#True "True")，则将使用来自 tarfile 的 uid 和 gid 数值来设置被提取文件的所有者/用户组。 在其他情况下，则会使用来自 tarfile 的名称值。

_filter_ 参数指明在提取之前要如何修改或拒绝 `members`。 请参阅 解压缩过滤器 了解详情。 建议应根据你需要支持的 _tar_ 特征显式地设置该参数。

警告

绝不要未经预先检验就从不可靠的源中提取归档文件。 这样有可能在 _path_ 之外创建文件，例如某些成员具有以 `"/"` 开始的绝对路径文件名或带有两个点号 `".."` 的文件名。

设置 `filter='data'` 来防止最危险的安全问题，并请参阅 解压缩过滤器 一节了解详情。section for details.

在 3.5 版本发生变更: 添加了 _numeric_owner_ 形参。

在 3.6 版本发生变更: _path_ 形参接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.12 版本发生变更: 添加了 _filter_ 形参。

TarFile.extract( _member_ , _path =''_, _set_attrs =True_, _*_ , _numeric_owner =False_, _filter =None_)¶
~~~
    

~~~
从归档中提取出一个成员放入当前工作目录，将使用其完整名称。 成员的文件信息会尽可能精确地被提取。 _member_ 可以是一个文件名或 `TarInfo` 对象。 你可以使用 _path_ 指定一个不同的目录。 _path_ 可以是一个 [path-like object](../glossary.md#term-path-like-object)。 将会设置文件属性 (owner, mtime, mode) 除非 _set_attrs_ 为假值。

_numeric_owner_ 和 _filter_ 参数与 `extractall()` 中的相同。

备注

`extract()` 方法不会处理某些提取问题。 在大多数情况下你应当考虑使用 `extractall()` 方法。

警告

查看 `extractall()` 的警告信息。

设置 `filter='data'` 来防止最危险的安全问题，并请参阅 解压缩过滤器 一节了解详情。section for details.

在 3.2 版本发生变更: 添加了 _set_attrs_ 形参。

在 3.5 版本发生变更: 添加了 _numeric_owner_ 形参。

在 3.6 版本发生变更: _path_ 形参接受一个 [path-like object](../glossary.md#term-path-like-object)。

在 3.12 版本发生变更: 添加了 _filter_ 形参。

TarFile.extractfile( _member_ )¶
~~~
    

~~~
将归档中的一个成员提取为文件对象。 _member_ 可以是一个文件名或 `TarInfo` 对象。 如果 _member_ 是一个常规文件或链接，则会返回一个 [`io.BufferedReader`](io.md#io.BufferedReader "io.BufferedReader") 对象。 对于所有其他现有成员，则都将返回 [`None`](constants.md#None "None")。 如果 _member_ 未在归档中出现，则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

在 3.3 版本发生变更: 返回一个 [`io.BufferedReader`](io.md#io.BufferedReader "io.BufferedReader") 对象。

TarFile.errorlevel _: [int](functions.md#int "int")_¶
~~~
    

~~~
如果 _errorlevel_ 为 `0`，则在使用 `TarFile.extract()` 和 `TarFile.extractall()` 时错误会被忽略。 不过，当 _debug_ 大于 0 时它们将会作为错误消息在调试输出中出现。 如果 _errorlevel*为 ``1`` (默认值)，则所有 *fatal_ 错误都会作为 [`OSError`](exceptions.md#OSError "OSError") 或 `FilterError` 异常被引发。 如果为 `2`，则所有 _non-fatal_ 错误也会作为 `TarError` 异常被引发。

某些异常，如参数类型错误或数据损坏导致的异常，总是会被触发。

自定义 提取过滤器 应针对 _fatal_ 错误引发 `FilterError`，针对 _non-fatal_ 错误引发 `ExtractError`。

请注意，当出现异常时，存档可能会被部分提取。用需要户负责进行清理。

TarFile.extraction_filter¶
~~~
    

~~~
在 3.12 版本加入.

被用作 `extract()` 和 `extractall()` 的 _filter_ 参数的默认值的 提取过滤器。

该属性可以为 `None` 或是一个可调用对象。 与 `extract()` 的 _filter_ 参数不同，该属性不允许使用字符串名称。

If `extraction_filter` is `None` (the default), calling an extraction method without a _filter_ argument will raise a `DeprecationWarning`, and fall back to the `fully_trusted` filter, whose dangerous behavior matches previous versions of Python.

In Python 3.14+, leaving `extraction_filter=None` will cause extraction methods to use the `data` filter by default.

The attribute may be set on instances or overridden in subclasses. It also is possible to set it on the `TarFile` class itself to set a global default, although, since it affects all uses of _tarfile_ , it is best practice to only do so in top-level applications or [`site configuration`](site.md#module-site "site: Module responsible for site-specific configuration."). To set a global default this way, a filter function needs to be wrapped in [`staticmethod()`](functions.md#staticmethod "staticmethod") to prevent injection of a `self` argument.

TarFile.add( _name_ , _arcname =None_, _recursive =True_, _*_ , _filter =None_)¶
~~~
    

~~~
将文件 _name_ 添加到归档。 _name_ 可以为任意类型的文件（目录、fifo、符号链接等等）。 如果给出 _arcname_ 则它将为归档中的文件指定一个替代名称。 默认情况下会递归地添加目录。 这可以通过将 _recursive_ 设为 [`False`](constants.md#False "False") 来避免。 递归操作会按排序顺序添加条目。 如果给定了 _filter_ ，它应当为一个接受 `TarInfo` 对象并返回已修改 `TarInfo` 对象的函数。 如果它返回 [`None`](constants.md#None "None") 则 `TarInfo` 对象将从归档中被排除。 具体示例参见 例子。

在 3.2 版本发生变更: 添加了 _filter_ 形参。

在 3.7 版本发生变更: 递归操作按排序顺序添加条目。

TarFile.addfile( _tarinfo_ , _fileobj =None_)¶
~~~
    

~~~
将 `TarInfo` 对象 _tarinfo_ 添加到归档。 如果给定了 _fileobj_ ，它应当是一个 [binary file](../glossary.md#term-binary-file)，并会从中读取 `tarinfo.size` 个字节添加到归档。 你可以直接创建 `TarInfo` 对象，或是使用 `gettarinfo()` 来创建。

TarFile.gettarinfo( _name =None_, _arcname =None_, _fileobj =None_)¶
~~~
    

~~~
基于 [`os.stat()`](os.md#os.stat "os.stat") 的结果或者现有文件的相同数据创建一个 `TarInfo`。 文件或者是命名为 _name_ ，或者是使用文件描述符指定为一个 [file object](../glossary.md#term-file-object) _fileobj_ 。 _name_ 可以是一个 [path-like object](../glossary.md#term-path-like-object)。 如果给定了 _arcname_ ，则它将为归档中的文件指定一个替代名称，在其他情况下，名称将从 _fileobj_ 的 [`name`](io.md#io.FileIO.name "io.FileIO.name") 属性或 _name_ 参数获取。 名称应当是一个文本字符串。

你可以在使用 `addfile()` 添加 `TarInfo` 的某些属性之前修改它们。 如果文件对象不是从文件开头进行定位的普通文件对象，`size` 之类的属性就可能需要修改。 例如 [`GzipFile`](gzip.md#gzip.GzipFile "gzip.GzipFile") 之类的文件就属于这种情况。 `name` 也可以被修改，在这种情况下 _arcname_ 可以是一个占位字符串。

在 3.6 版本发生变更: _name_ 形参接受一个 [path-like object](../glossary.md#term-path-like-object)。

TarFile.close()¶
~~~
    

~~~
关闭 `TarFile`。 在写入模式下，会向归档添加两个表示结束的零数据块。

TarFile.pax_headers¶
~~~
    

~~~
一个包含 pax 全局标头的键值对的字典。

## TarInfo 对象¶

`TarInfo` 对象代表 `TarFile` 中的一个文件。 除了会存储所有必要的文件属性（例如文件类型、大小、时间、权限、所有者等），它还提供了一些确定文件类型的有用方法。 此对象 _并不_ 包含文件数据本身。

`TarInfo` objects are returned by `TarFile`'s methods `getmember()`, `getmembers()` and `gettarinfo()`.

Modifying the objects returned by `getmember()` or `getmembers()` will affect all subsequent operations on the archive. For cases where this is unwanted, you can use [`copy.copy()`](copy.md#module-copy "copy: Shallow and deep copy operations.") or call the `replace()` method to create a modified copy in one step.

Several attributes can be set to `None` to indicate that a piece of metadata is unused or unknown. Different `TarInfo` methods handle `None` differently:

  * The `extract()` or `extractall()` methods will ignore the corresponding metadata, leaving it set to a default.

  * `addfile()` 将会失败。

  * `list()` 将打印一个占位字符串。

_class _tarfile.TarInfo( _name =''_)¶
~~~
    

~~~
创建一个 `TarInfo` 对象。

_classmethod _TarInfo.frombuf( _buf_ , _encoding_ , _errors_ )¶
~~~
    

~~~
基于字符串缓冲区 _buf_ 创建并返回一个 `TarInfo` 对象。

如果缓冲区无效则会引发 `HeaderError`。

_classmethod _TarInfo.fromtarfile( _tarfile_ )¶
~~~
    

~~~
从 `TarFile` 对象 _tarfile_ 读取下一个成员并将其作为 `TarInfo` 对象返回。

TarInfo.tobuf( _format =DEFAULT_FORMAT_, _encoding =ENCODING_, _errors ='surrogateescape'_)¶
~~~
    

~~~
基于 `TarInfo` 对象创建一个字符串缓冲区。 有关参数的信息请参见 `TarFile` 类的构造器。

在 3.2 版本发生变更: 使用 `'surrogateescape'` 作为 _errors_ 参数的默认值。

`TarInfo` 对象具有以下公有数据属性:

TarInfo.name _: [str](stdtypes.md#str "str")_¶
~~~
    

~~~
归档成员的名称。

TarInfo.size _: [int](functions.md#int "int")_¶
~~~
    

~~~
以字节表示的大小。

TarInfo.mtime _: [int](functions.md#int "int") | [float](functions.md#float "float")_¶
~~~
    

~~~
以 [Unix 纪元](time.md#epoch) 秒数表示的最近修改时间，与 [`os.stat_result.st_mtime`](os.md#os.stat_result.st_mtime "os.stat_result.st_mtime") 相同。

在 3.12 版本发生变更: 对于 `extract()` 和 `extractall()` 可设为 `None`，以使解压缩操作跳过应用此属性。

TarInfo.mode _: [int](functions.md#int "int")_¶
~~~
    

~~~
权限比特位，与 [`os.chmod()`](os.md#os.chmod "os.chmod") 相同。

在 3.12 版本发生变更: 对于 `extract()` 和 `extractall()` 可设为 `None`，以使解压缩操作跳过应用此属性。

TarInfo.type¶
~~~
    

~~~
文件类型。 _type_ 通常为以下常量之一: `REGTYPE`, `AREGTYPE`, `LNKTYPE`, `SYMTYPE`, `DIRTYPE`, `FIFOTYPE`, `CONTTYPE`, `CHRTYPE`, `BLKTYPE`, `GNUTYPE_SPARSE`。 要更方便地确定一个 `TarInfo` 对象的类型，请使用下述的 `is*()` 方法。

TarInfo.linkname _: [str](stdtypes.md#str "str")_¶
~~~
    

~~~
目标文件名的名称，该属性仅在类型为 `LNKTYPE` 和 `SYMTYPE` 的 `TarInfo` 对象中存在。

For symbolic links (`SYMTYPE`), the _linkname_ is relative to the directory that contains the link. For hard links (`LNKTYPE`), the _linkname_ is relative to the root of the archive.

TarInfo.uid _: [int](functions.md#int "int")_¶
~~~
    

~~~
最初保存该成员的用户的用户 ID。

在 3.12 版本发生变更: 对于 `extract()` 和 `extractall()` 可设为 `None`，以使解压缩操作跳过应用此属性。

TarInfo.gid _: [int](functions.md#int "int")_¶
~~~
    

~~~
最初保存该成员的用户的分组 ID。

在 3.12 版本发生变更: 对于 `extract()` 和 `extractall()` 可设为 `None`，以使解压缩操作跳过应用此属性。

TarInfo.uname _: [str](stdtypes.md#str "str")_¶
~~~
    

~~~
用户名。

在 3.12 版本发生变更: 对于 `extract()` 和 `extractall()` 可设为 `None`，以使解压缩操作跳过应用此属性。

TarInfo.gname _: [str](stdtypes.md#str "str")_¶
~~~
    

~~~
分组名。

在 3.12 版本发生变更: 对于 `extract()` 和 `extractall()` 可设为 `None`，以使解压缩操作跳过应用此属性。

TarInfo.pax_headers _: [dict](stdtypes.md#dict "dict")_¶
~~~
    

~~~
一个包含所关联的 pax 扩展标头的键值对的字典。

TarInfo.replace(name=..., mtime=..., mode=..., linkname=...,

uid=..., gid=..., uname=..., gname=...,

deep=True)
~~~
    

~~~
在 3.12 版本加入.

Return a _new_ copy of the `TarInfo` object with the given attributes changed. For example, to return a `TarInfo` with the group name set to `'staff'`, use:
~~~
    
    
~~~
new_tarinfo = old_tarinfo.replace(gname='staff')
~~~

By default, a deep copy is made. If _deep_ is false, the copy is shallow, i.e. `pax_headers` and any custom attributes are shared with the original `TarInfo` object.

`TarInfo` 对象还提供了一些便捷查询方法:

TarInfo.isfile()¶

    

~~~
如果 `Tarinfo` 对象为普通文件则返回 [`True`](constants.md#True "True")。

TarInfo.isreg()¶
~~~
    

~~~
与 `isfile()` 相同。

TarInfo.isdir()¶
~~~
    

~~~
如果为目录则返回 [`True`](constants.md#True "True")。

TarInfo.issym()¶
~~~
    

~~~
如果为符号链接则返回 [`True`](constants.md#True "True")。

TarInfo.islnk()¶
~~~
    

~~~
如果为硬链接则返回 [`True`](constants.md#True "True")。

TarInfo.ischr()¶
~~~
    

~~~
如果为字符设备则返回 [`True`](constants.md#True "True")。

TarInfo.isblk()¶
~~~
    

~~~
如果为块设备则返回 [`True`](constants.md#True "True")。

TarInfo.isfifo()¶
~~~
    

~~~
如果为 FIFO 则返回 [`True`](constants.md#True "True")。.

TarInfo.isdev()¶
~~~
    

~~~
如果为字符设备、块设备或 FIFO 之一则返回 [`True`](constants.md#True "True")。

## 解压缩过滤器¶

在 3.12 版本加入.

The _tar_ format is designed to capture all details of a UNIX-like filesystem, which makes it very powerful. Unfortunately, the features make it easy to create tar files that have unintended -- and possibly malicious -- effects when extracted. For example, extracting a tar file can overwrite arbitrary files in various ways (e.g. by using absolute paths, `..` path components, or symlinks that affect later members).

In most cases, the full functionality is not needed. Therefore, _tarfile_ supports extraction filters: a mechanism to limit functionality, and thus mitigate some of the security issues.

参见

[**PEP 706**](https://peps.python.org/pep-0706/)
~~~
    

~~~
Contains further motivation and rationale behind the design.

The _filter_ argument to `TarFile.extract()` or `extractall()` can be:

  * the string `'fully_trusted'`: Honor all metadata as specified in the archive. Should be used if the user trusts the archive completely, or implements their own complex verification.

  * the string `'tar'`: Honor most _tar_ -specific features (i.e. features of UNIX-like filesystems), but block features that are very likely to be surprising or malicious. See `tar_filter()` for details.

  * the string `'data'`: Ignore or block most features specific to UNIX-like filesystems. Intended for extracting cross-platform data archives. See `data_filter()` for details.

  * `None` (default): Use `TarFile.extraction_filter`.

If that is also `None` (the default), raise a `DeprecationWarning`, and fall back to the `'fully_trusted'` filter, whose dangerous behavior matches previous versions of Python.

In Python 3.14, the `'data'` filter will become the default instead. It's possible to switch earlier; see `TarFile.extraction_filter`.

  * A callable which will be called for each extracted member with a TarInfo describing the member and the destination path to where the archive is extracted (i.e. the same path is used for all members):
    
        filter(member: TarInfo, path: str, /) -> TarInfo | None
~~~

The callable is called just before each member is extracted, so it can take the current state of the disk into account. It can:

    * return a `TarInfo` object which will be used instead of the metadata in the archive, or

    * return `None`, in which case the member will be skipped, or

    * raise an exception to abort the operation or skip the member, depending on `errorlevel`. Note that when extraction is aborted, `extractall()` may leave the archive partially extracted. It does not attempt to clean up.

### Default named filters¶

The pre-defined, named filters are available as functions, so they can be reused in custom filters:

tarfile.fully_trusted_filter( _member_ , _path_ )¶

    

~~~
Return _member_ unchanged.

This implements the `'fully_trusted'` filter.

tarfile.tar_filter( _member_ , _path_ )¶
~~~
    

~~~
Implements the `'tar'` filter.

  * Strip leading slashes (`/` and [`os.sep`](os.md#os.sep "os.sep")) from filenames.

  * Refuse to extract files with absolute paths (in case the name is absolute even after stripping slashes, e.g. `C:/foo` on Windows). This raises `AbsolutePathError`.

  * Refuse to extract files whose absolute path (after following symlinks) would end up outside the destination. This raises `OutsideDestinationError`.

  * Clear high mode bits (setuid, setgid, sticky) and group/other write bits (`S_IWOTH`).

Return the modified `TarInfo` member.

tarfile.data_filter( _member_ , _path_ )¶
~~~
    

~~~
Implements the `'data'` filter. In addition to what `tar_filter` does:

  * Refuse to extract links (hard or soft) that link to absolute paths, or ones that link outside the destination.

This raises `AbsoluteLinkError` or `LinkOutsideDestinationError`.

Note that such files are refused even on platforms that do not support symbolic links.

  * Refuse to extract device files (including pipes). This raises `SpecialFileError`.

  * For regular files, including hard links:

    * Set the owner read and write permissions (`S_IWUSR`).

    * Remove the group & other executable permission (`S_IXOTH`) if the owner doesn’t have it ([`S_IXUSR`](stat.md#stat.S_IXUSR "stat.S_IXUSR")).

  * For other files (directories), set `mode` to `None`, so that extraction methods skip applying permission bits.

  * Set user and group info (`uid`, `gid`, `uname`, `gname`) to `None`, so that extraction methods skip setting it.

Return the modified `TarInfo` member.

### Filter errors¶

When a filter refuses to extract a file, it will raise an appropriate exception, a subclass of `FilterError`. This will abort the extraction if `TarFile.errorlevel` is 1 or more. With `errorlevel=0` the error will be logged and the member will be skipped, but extraction will continue.

### Hints for further verification¶

Even with `filter='data'`, _tarfile_ is not suited for extracting untrusted files without prior inspection. Among other issues, the pre-defined filters do not prevent denial-of-service attacks. Users should do additional checks.

Here is an incomplete list of things to consider:

  * Extract to a [`new temporary directory`](tempfile.md#tempfile.mkdtemp "tempfile.mkdtemp") to prevent e.g. exploiting pre-existing links, and to make it easier to clean up after a failed extraction.

  * When working with untrusted data, use external (e.g. OS-level) limits on disk, memory and CPU usage.

  * Check filenames against an allow-list of characters (to filter out control characters, confusables, foreign path separators, etc.).

  * Check that filenames have expected extensions (discouraging files that execute when you “click on them”, or extension-less files like Windows special device names).

  * Limit the number of extracted files, total size of extracted data, filename length (including symlink length), and size of individual files.

  * Check for files that would be shadowed on case-insensitive filesystems.

Also note that:

  * Tar files may contain multiple versions of the same file. Later ones are expected to overwrite any earlier ones. This feature is crucial to allow updating tape archives, but can be abused maliciously.

  * _tarfile_ does not protect against issues with “live” data, e.g. an attacker tinkering with the destination (or source) directory while extraction (or archiving) is in progress.

### Supporting older Python versions¶

Extraction filters were added to Python 3.12, but may be backported to older versions as security updates. To check whether the feature is available, use e.g. `hasattr(tarfile, 'data_filter')` rather than checking the Python version.

The following examples show how to support Python versions with and without the feature. Note that setting `extraction_filter` will affect any subsequent operations.

  * Fully trusted archive:
    
        my_tarfile.extraction_filter = (lambda member, path: member)
    my_tarfile.extractall()
~~~

  * Use the `'data'` filter if available, but revert to Python 3.11 behavior (`'fully_trusted'`) if this feature is not available:
    
        my_tarfile.extraction_filter = getattr(tarfile, 'data_filter',
                                           (lambda member, path: member))
    my_tarfile.extractall()
    

  * Use the `'data'` filter; _fail_ if it is not available:
    
        my_tarfile.extractall(filter=tarfile.data_filter)
    

或者:

    
        my_tarfile.extraction_filter = tarfile.data_filter
    my_tarfile.extractall()
    

  * Use the `'data'` filter; _warn_ if it is not available:
    
        if hasattr(tarfile, 'data_filter'):
        my_tarfile.extractall(filter='data')
    else:
        # remove this when no longer needed
        warn_the_user('Extracting may be unsafe; consider updating Python')
        my_tarfile.extractall()
    

### Stateful extraction filter example¶

While _tarfile_ 's extraction methods take a simple _filter_ callable, custom filters may be more complex objects with an internal state. It may be useful to write these as context managers, to be used like this:

    
    
~~~
with StatefulFilter() as filter_func:
    tar.extractall(path, filter=filter_func)
~~~

Such a filter can be written as, for example:

    
    
~~~
class StatefulFilter:
    def __init__(self):
        self.file_count = 0

    def __enter__(self):
        return self

    def __call__(self, member, path):
        self.file_count += 1
        return member

    def __exit__(self, *exc_info):
        print(f'{self.file_count} files extracted')
~~~

## 命令行接口¶

在 3.4 版本加入.

`tarfile` 模块提供了简单的命令行接口以便与 tar 归档进行交互。

如果你想要创建一个新的 tar 归档，请在 `-c` 选项后指定其名称然后列出应当被包含的文件名:

    
    
~~~
$ python -m tarfile -c monty.tar  spam.txt eggs.txt
~~~

传入一个字典也是可接受的:

    
    
~~~
$ python -m tarfile -c monty.tar life-of-brian_1979/
~~~

如果你想要将一个 tar 归档提取到指定的目录，请使用 `-e` 选项:

    
    
~~~
$ python -m tarfile -e monty.tar
~~~

你也可以通过传入目录名称将一个 tar 归档提取到不同的目录:

    
    
~~~
$ python -m tarfile -e monty.tar  other-dir/
~~~

要获取一个 tar 归档中文件的列表，请使用 `-l` 选项:

    
    
~~~
$ python -m tarfile -l monty.tar
~~~

### 命令行选项¶

-l <tarfile>¶
\--list <tarfile>¶

    

~~~
列出一个 tarfile 中的文件名。

-c <tarfile> <source1> ... <sourceN>¶
\--create <tarfile> <source1> ... <sourceN>¶
~~~
    

~~~
基于源文件创建 tarfile。

-e <tarfile> [<output_dir>]¶
\--extract <tarfile> [<output_dir>]¶
~~~
    

~~~
如果未指定 _output_dir_ 则会将 tarfile 提取到当前目录。

-t <tarfile>¶
\--test <tarfile>¶
~~~
    

~~~
检测 tarfile 是否有效。

-v, \--verbose¶
~~~

更详细地输出结果。

\--filter <filtername>¶

    

~~~
Specifies the _filter_ for `--extract`. See 解压缩过滤器 for details. Only string names are accepted (that is, `fully_trusted`, `tar`, and `data`).

## 例子¶

如何将整个 tar 归档提取到当前工作目录:
~~~
    
    
~~~
import tarfile
tar = tarfile.open("sample.tar.gz")
tar.extractall(filter='data')
tar.close()
~~~

如何通过 `TarFile.extractall()` 使用生成器函数而非列表来提取一个 tar 归档的子集:

    
    
~~~
import os
import tarfile

def py_files(members):
    for tarinfo in members:
        if os.path.splitext(tarinfo.name)[1] == ".py":
            yield tarinfo

tar = tarfile.open("sample.tar.gz")
tar.extractall(members=py_files(tar))
tar.close()
~~~

如何基于一个文件名列表创建未压缩的 tar 归档:

    
    
~~~
import tarfile
tar = tarfile.open("sample.tar", "w")
for name in ["foo", "bar", "quux"]:
    tar.add(name)
tar.close()
~~~

使用 [`with`](../reference/compound_stmts.md#with) 语句的同一个示例:

    
    
~~~
import tarfile
with tarfile.open("sample.tar", "w") as tar:
    for name in ["foo", "bar", "quux"]:
        tar.add(name)
~~~

如何读取一个 gzip 压缩的 tar 归档并显示一些成员信息:

    
    
~~~
import tarfile
tar = tarfile.open("sample.tar.gz", "r:gz")
for tarinfo in tar:
    print(tarinfo.name, "is", tarinfo.size, "bytes in size and is ", end="")
    if tarinfo.isreg():
        print("a regular file.")
    elif tarinfo.isdir():
        print("a directory.")
    else:
        print("something else.")
tar.close()
~~~

如何创建一个归档并使用 `TarFile.add()` 中的 _filter_ 形参来重置用户信息:

    
    
~~~
import tarfile
def reset(tarinfo):
    tarinfo.uid = tarinfo.gid = 0
    tarinfo.uname = tarinfo.gname = "root"
    return tarinfo
tar = tarfile.open("sample.tar.gz", "w:gz")
tar.add("foo", filter=reset)
tar.close()
~~~

## 受支持的 tar 格式¶

通过 `tarfile` 模块可以创建三种 tar 格式:

  * The POSIX.1-1988 ustar 格式 (`USTAR_FORMAT`)。 它支持最多 256 个字符的文件名长度和最多 100 个字符的链接名长度。 文件大小上限为 8 GiB。 这是一种老旧但广受支持的格式。

  * GNU tar 格式 (`GNU_FORMAT`)。 它支持长文件名和链接名、大于 8 GiB 的文件以及稀疏文件。 它是 GNU/Linux 系统上的事实标准。 `tarfile` 完全支持针对长名称的 GNU tar 扩展，稀疏文件支持则限制为只读。

  * POSIX.1-2001 pax 格式 (`PAX_FORMAT`)。 它是几乎无限制的最灵活格式。 它支持长文件名和链接名，大文件以及使用可移植方式存储路径名。 现代的 tar 实现，包括 GNU tar, bsdtar/libarchive 和 star，都完全支持扩展的 _pax_ 特性；某些老旧或不再维护的库可能不支持，但应当会将 _pax_ 归档视为广受支持的 _ustar_ 格式。 它是当前新建归档的默认格式。

它扩展了现有的 _ustar_ 格式，包括用于无法以其他方式存储的附加标头。 存在两种形式的 pax 标头：扩展标头只影响后续的文件标头，全局标头则适用于完整归档并会影响所有后续的文件。 为了便于移植，在 pax 标头中的所有数据均以 _UTF-8_ 编码。

还有一些 tar 格式的其他变种，它们可以被读取但不能被创建:

  * 古老的 V7 格式。 这是来自 Unix 第七版的第一个 tar 格式，它只存储常规文件和目录。 名称长度不能超过 100 个字符，并且没有用户/分组名信息。 某些归档在带有非 ASCII 字符字段的情况下会产生计算错误的标头校验和。

  * SunOS tar 扩展格式。 此格式是 POSIX.1-2001 pax 格式的一个变种，但并不保持兼容。

## Unicode 问题¶

最初 tar 格式被设计用来在磁带机上生成备份，主要关注于保存文件系统信息。 现在 tar 归档通常用于文件分发和在网络上交换归档。 最初格式（它是所有其他格式的基础）的一个问题是它没有支持不同字符编码格式的概念。 例如，一个在 _UTF-8_ 系统上创建的普通 tar 归档如果包含非 _ASCII_ 字符则将无法在 _Latin-1_ 系统上被正确读取。 文本元数据（例如文件名，链接名，用户/分组名）将变为损坏状态。 不幸的是，没有什么办法能够自动检测一个归档的编码格式。 pax 格式被设计用来解决这个问题。 它使用通用字符编码格式 _UTF-8_ 来存储非 ASCII 元数据。

在 `tarfile` 中字符转换的细节由 `TarFile` 类的 _encoding_ 和 _errors_ 关键字参数控制。

_encoding_ 定义了用于归档中元数据的字符编码格式。 默认值为 [`sys.getfilesystemencoding()`](sys.md#sys.getfilesystemencoding "sys.getfilesystemencoding") 或是回退选项 `'ascii'`。 根据归档是被读取还是被写入，元数据必须被解码或编码。 如果没有正确设置 _encoding_ ，转换可能会失败。

_errors_ 参数定义了不能被转换的字符将如何处理。 可能的取值在 [错误处理方案](codecs.md#error-handlers) 小节列出。 默认方案为 `'surrogateescape'`，它也被 Python 用于文件系统调用，参见 [文件名，命令行参数，以及环境变量。](os.md#os-filenames)。

对于 `PAX_FORMAT` 归档（默认格式）， _encoding_ 通常是不必要的，因为所有元数据都使用 _UTF-8_ 来存储。 _encoding_ 仅在解码二进制 pax 标头或存储带有替代字符的字符串等少数场景下会被使用。

