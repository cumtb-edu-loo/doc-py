# `mimetypes` \--- 映射文件名到 MIME 类型¶

**源代码:** [Lib/mimetypes.py](https://github.com/python/cpython/tree/3.12/Lib/mimetypes.py)

* * *

`mimetypes` 模块可以在文件名或 URL 和关联到文件扩展名的 MIME 类型之间执行转换。 所提供的转换包括从文件名到 MIME 类型和从 MIME 类型到文件扩展名；后一种转换不支持编码格式。

该模块提供了一个类和一些便捷函数。 这些函数是该模块通常的接口，但某些应用程序可能也会希望使用类。

下列函数提供了此模块的主要接口。 如果此模块尚未被初始化，它们将会调用 `init()`，如果它们依赖于 `init()` 所设置的信息的话。

mimetypes.guess_type( _url_ , _strict =True_)¶

    

~~~
根据 _url_ 给出的文件名、路径或 URL 来猜测文件的类型，URL 可以为字符串或 [path-like object](../glossary.md#term-path-like-object)。

返回值是一个元组 `(type, encoding)` 其中 _type_ 在无法猜测（后缀不存在或者未知）时为 `None`，或者为 `'type/subtype'` 形式的字符串，可以作为 MIME _content-type_ 标头。

_encoding_ 在无编码格式时为 `None`，或者为程序所用的编码格式 (例如 **compress** 或 **gzip** )。 它可以作为 _Content-Encoding_ 标头，但 **不可** 作为 _Content-Transfer-Encoding_ 标头。 映射是表格驱动的。 编码格式前缀对大小写敏感；类型前缀会先以大小写敏感方式检测再以大小写不敏感方式检测。

可选的 _strict_ 参数是一个旗标，指明要将已知 MIME 类型限制在 [IANA 已注册](https://www.iana.org/assignments/media-types/media-types.xhtml) 的官方类型之内。 当 _strict_ 为 `True` 时（默认值），则仅支持 IANA 类型；当 _strict_ 为 `False` 时，则还支持某些附加的非标准但常用的 MIME 类型。

在 3.8 版本发生变更: 增加了 [path-like object](../glossary.md#term-path-like-object) 作为 url 的支持。

mimetypes.guess_all_extensions( _type_ , _strict =True_)¶
~~~
    

~~~
根据由 _type_ 给出的文件 MIME 类型猜测其扩展名。 返回值是由所有可能的文件扩展名组成的字符串列表，包括开头的点号 (`'.'`)。 这些扩展名不保证能关联到任何特定的数据流，但是将会由 `guess_type()` 映射到 MIME 类型 _type_ 。

可选的 _strict_ 参数具有与 `guess_type()` 函数一致的含义。

mimetypes.guess_extension( _type_ , _strict =True_)¶
~~~
    

~~~
根据由 _type_ 给出的文件 MIME 类型猜测其扩展名。 返回值是一个表示文件扩展名的字符串，包括开头的点号 (`'.'`)。 该扩展名不保证能关联到任何特定的数据流，但是将会由 `guess_type()` 映射到 MIME 类型 _type_ 。 如果不能猜测出 _type_ 的扩展名，则将返回 `None`。

可选的 _strict_ 参数具有与 `guess_type()` 函数一致的含义。

有一些附加函数和数据项可被用于控制此模块的行为。

mimetypes.init( _files =None_)¶
~~~
    

~~~
初始化内部数据结构。 _files_ 如果给出则必须是一个文件名序列，它应当被用于协助默认的类型映射。 如果省略则要使用的文件名会从 `knownfiles` 中获取； 在 Windows 上，将会载入当前注册表设置。 在 _files_ 或 `knownfiles` 中指定的每个文件名的优先级将高于在它之前的文件名。 `init()` 允许被重复调用。

为 _files_ 指定一个空列表将防止应用系统默认选项：将只保留来自内置列表的常用值。

如果 _files_ 为 `None` 则内部数据结构会完全重建为其初始默认值。 这是一个稳定操作并将在多次调用时产生相同的结果。

在 3.2 版本发生变更: 在之前版本中，Windows 注册表设置会被忽略。

mimetypes.read_mime_types( _filename_ )¶
~~~
    

~~~
载入在文件 _filename_ 中给定的类型映射，如果文件存在的话。 返回的类型映射会是一个字典，其中的键值对为文件扩展名包括开头的点号 (`'.'`) 与 `'type/subtype'` 形式的字符串。 如果文件 _filename_ 不存在或无法被读取，则返回 `None`。

mimetypes.add_type( _type_ , _ext_ , _strict =True_)¶
~~~
    

~~~
添加一个从 MIME 类型 _type_ 到扩展名 _ext_ 的映射。 当扩展名已知时，新类型将替代旧类型。 当类型已知时，扩展名将被添加到已知扩展名列表。

当 _strict_ 为 `True` 时（默认值），映射将被添加到官方 MIME 类型，否则添加到非标准类型。

mimetypes.inited¶
~~~
    

~~~
指明全局数据结构是否已被初始化的旗标。 这会由 `init()` 设为 `True`。

mimetypes.knownfiles¶
~~~
    

~~~
通常安装的类型映射文件名列表。 这些文件一般被命名为 `mime.types` 并会由不同的包安装在不同的位置。

mimetypes.suffix_map¶
~~~
    

~~~
将后缀映射到其他后缀的字典。 它被用来允许识别已编码的文件，其编码格式和类型是由相同的扩展名来指明的。 例如，`.tgz` 扩展名被映射到 `.tar.gz` 以允许编码格式和类型被分别识别。

mimetypes.encodings_map¶
~~~
    

~~~
映射文件扩展名到编码格式类型的字典。

mimetypes.types_map¶
~~~
    

~~~
映射文件扩展名到 MIME 类型的字典。

mimetypes.common_types¶
~~~
    

~~~
映射文件扩展名到非标准但常见的 MIME 类型的字典。

此模块一个使用示例:
~~~
    
    
~~~shell
>>> import mimetypes
>>> mimetypes.init()
>>> mimetypes.knownfiles
['/etc/mime.types', '/etc/httpd/mime.types', ... ]
>>> mimetypes.suffix_map['.tgz']
'.tar.gz'
>>> mimetypes.encodings_map['.gz']
'gzip'
>>> mimetypes.types_map['.tgz']
'application/x-tar-gz'
~~~

## MimeTypes 对象¶

`MimeTypes` 类可以被用于那些需要多个 MIME 类型数据库的应用程序；它提供了与 `mimetypes` 模块所提供的类似接口。

_class _mimetypes.MimeTypes( _filenames =()_, _strict =True_)¶

    

~~~
这个类表示 MIME 类型数据库。 默认情况下，它提供了对与此模块其余部分一致的数据库的访问权限。 这个初始数据库是此模块所提供数据库的一个副本，并可以通过使用 `read()` 或 `readfp()` 方法将附加的 `mime.types` 样式文载入到数据库中来进行扩展。 如果不需要默认数据的话这个映射字典也可以在载入附加数据之前先被清空。

可选的 _filenames_ 形参可被用来让附加文件被载入到默认数据库“之上”。

suffix_map¶
~~~
    

~~~
将后缀映射到其他后缀的字典。 它被用来允许识别已编码的文件，其编码格式和类型是由相同的扩展名来指明的。 例如，`.tgz` 扩展名被映射到 `.tar.gz` 以允许编码格式和类型被分别识别。 这是在模块中定义的全局 `suffix_map` 的一个副本。

encodings_map¶
~~~
    

~~~
映射文件扩展名到编码格式类型的字典。 这是在模块中定义的全局 `encodings_map` 的一个副本。

types_map¶
~~~
    

~~~
包含两个字典的元组，将文件扩展名映射到 MIME 类型：第一个字典针对非标准类型而第二个字典针对标准类型。 它们会由 `common_types` 和 `types_map` 来初始化。

types_map_inv¶
~~~
    

~~~
包含两个字典的元组，将 MIME 类型映射到文件扩展名列表：第一个字典针对非标准类型而第二个字典针对标准类型。 它们会由 `common_types` 和 `types_map` 来初始化。

guess_extension( _type_ , _strict =True_)¶
~~~
    

~~~
类似于 `guess_extension()` 函数，使用存储的表作为对象的一部分。

guess_type( _url_ , _strict =True_)¶
~~~
    

~~~
类似于 `guess_type()` 函数，使用存储的表作为对象的一部分。

guess_all_extensions( _type_ , _strict =True_)¶
~~~
    

~~~
类似于 `guess_all_extensions()` 函数，使用存储的表作为对象的一部分。

read( _filename_ , _strict =True_)¶
~~~
    

~~~
从名称为 _filename_ 的文件载入 MIME 信息。 此方法使用 `readfp()` 来解析文件。

如果 _strict_ 为 `True`，信息将被添加到标准类型列表，否则添加到非标准类型列表。

readfp( _fp_ , _strict =True_)¶
~~~
    

~~~
从打开的文件 _fp_ 载入 MIME 类型信息。 文件必须具有标准 `mime.types` 文件的格式。

如果 _strict_ 为 `True`，信息将被添加到标准类型列表，否则添加到非标准类型列表。

read_windows_registry( _strict =True_)¶
~~~
    

~~~
