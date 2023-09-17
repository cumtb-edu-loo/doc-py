# `mmap` \--- 内存映射文件支持¶

* * *

[可用性](3.标准库/intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](3.标准库/intro.md#wasm-availability) 了解详情。

内存映射文件对象的行为既像 [`bytearray`](stdtypes.md#bytearray "bytearray") 又像 [文件对象](../glossary.md#term-file-object)。 你可以在大部分接受 [`bytearray`](stdtypes.md#bytearray "bytearray") 的地方使用 mmap 对象；例如，你可以使用 [`re`](re.md#module-re "re: Regular expression operations.") 模块来搜索一个内存映射文件。 你也可以通过执行 `obj[index] = 97` 来修改单个字节，或者通过对切片赋值来修改一个子序列: `obj[i1:i2] = b'...'`。 你还可以在文件的当前位置开始读取和写入数据，并使用 `seek()` 前往另一个位置。

内存映射文件是由 `mmap` 构造函数创建的，其在 Unix 和 Windows 上是不同的。 无论哪种情况，你都必须为一个打开的文件提供文件描述符以进行更新。 如果你希望映射一个已有的 Python 文件对象，请使用该对象的 `fileno()` 方法来获取 _fileno_ 参数的正确值。 否则，你可以使用 [`os.open()`](os.md#os.open "os.open") 函数来打开这个文件，这会直接返回一个文件描述符（结束时仍然需要关闭该文件）。

备注

如果要为可写的缓冲文件创建内存映射，则应当首先 [`flush()`](io.md#io.IOBase.flush "io.IOBase.flush") 该文件。 这确保了对缓冲区的本地修改在内存映射中可用。

对于 Unix 和 Windows 版本的构造函数，可以将 _access_ 指定为可选的关键字参数。 _access_ 接受以下四个值之一： `ACCESS_READ` ， `ACCESS_WRITE` 或 `ACCESS_COPY` 分别指定只读，直写或写时复制内存，或 `ACCESS_DEFAULT` 推迟到 _prot_ 。 _access_ 可以在 Unix 和 Windows 上使用。如果未指定 _access_ ，则 Windows mmap 返回直写映射。这三种访问类型的初始内存值均取自指定的文件。向 `ACCESS_READ` 内存映射赋值会引发 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError") 异常。 向 `ACCESS_WRITE` 内存映射赋值会影响内存和底层的文件。 向 `ACCESS_COPY` 内存映射赋值会影响内存，但不会更新底层的文件。

在 3.7 版本发生变更: 添加了 `ACCESS_DEFAULT` 常量。

要映射匿名内存，应将 -1 作为 fileno 和 length 一起传递。

_class _mmap.mmap( _fileno_ , _length_ , _tagname=None_ , _access=ACCESS_DEFAULT_ [, _offset_ ])¶

    

~~~
**（ Windows 版本）** 映射被文件句柄 _fileno_ 指定的文件的 _length_ 个字节，并创建一个 mmap 对象。如果 _length_ 大于当前文件大小，则文件将扩展为包含 _length_ 个字节。如果 _length_ 为 `0`，则映射的最大长度为当前文件大小。如果文件为空， Windows 会引发异常（你无法在Windows上创建空映射）。

如果 _tagname_ 被指定且不是 `None` ，则是为映射提供标签名称的字符串。 Windows 允许你对同一文件拥有许多不同的映射。如果指定现有标签的名称，则会打开该标签，否则将创建该名称的新标签。如果省略此参数或设置为 `None` ，则创建的映射不带名称。避免使用 tag 参数将有助于使代码在Unix和Windows之间可移植。

_offset_ 可以被指定为非负整数偏移量。 mmap 引用将相对于从文件开头的偏移。 _offset_ 默认为0。 _offeset_ 必须是 `ALLOCATIONGRANULARITY` 的倍数。

引发一个 [审计事件](sys.md#auditing) `mmap.__new__` 附带参数 `fileno`, `length`, `access`, `offset`。

_class _mmap.mmap( _fileno_ , _length_ , _flags=MAP_SHARED_ , _prot=PROT_WRITE|PROT_READ_ , _access=ACCESS_DEFAULT_ [, _offset_ ])
~~~
    

~~~
**(Unix 版本)** 映射文件描述符 _fileno_ 指定的文件的 _length_ 个字节，并返回一个 mmap 对象。如果 _length_ 为 `0` ，则当调用 `mmap` 时，映射的最大长度将为文件的当前大小。

_flags_ 指明映射的性质。 `MAP_PRIVATE` 会创建私有的写入时拷贝映射，因此对 mmap 对象内容的修改将为该进程所私有。 而 `MAP_SHARED` 会创建与其他映射同一文件区域的进程所共享的映射。 默认值为 `MAP_SHARED`。 某些系统还具有额外的可用旗标，完整列表会在 MAP_* 常量 中指明。

如果指明了 _prot_ ，它将给出所需的内存保护方式；最有用的两个值是 `PROT_READ` 和 `PROT_WRITE`，分别指明页面为可读或可写。 _prot_ 默认为 `PROT_READ | PROT_WRITE`。

可以指定 _access_ 作为替代 _flags_ 和 _prot_ 的可选关键字形参。 同时指定 _flags_ , _prot_ 和 _access_ 将导致错误。 请参阅上文中 _access_ 的描述了解有关如何使用此形参的信息。

_offset_ 可以被指定为非负整数偏移量。 mmap 引用将相对于从文件开头的偏移。 _offset_ 默认为 0。 _offset_ 必须是 `ALLOCATIONGRANULARITY` 的倍数，它在 Unix 系统上等价于 `PAGESIZE`。

为了确保已创建内存映射的有效性，描述符 _fileno_ 所指定的文件在 macOS 上会与物理后备存储进行内部自动同步。

这个例子演示了使用 `mmap` 的简单方式:
~~~
    
    
~~~
import mmap

# write a simple example file
with open("hello.txt", "wb") as f:
    f.write(b"Hello Python!\n")

with open("hello.txt", "r+b") as f:
    # memory-map the file, size 0 means whole file
    mm = mmap.mmap(f.fileno(), 0)
    # read content via standard file methods
    print(mm.readline())  # prints b"Hello Python!\n"
    # read content via slice notation
    print(mm[:5])  # prints b"Hello"
    # update content using slice notation;
    # note that new content must have same size
    mm[6:] = b" world!\n"
    # ... and read again using standard file methods
    mm.seek(0)
    print(mm.readline())  # prints b"Hello  world!\n"
    # close the map
    mm.close()
~~~

`mmap` 也可以在 [`with`](8.%20复合语句.md#with) 语句中被用作上下文管理器:

    
    
~~~
import mmap

with mmap.mmap(-1, 13) as mm:
    mm.write(b"Hello world!")
~~~

在 3.2 版本加入: 上下文管理器支持。

下面的例子演示了如何创建一个匿名映射并在父进程和子进程之间交换数据。:

    
    
~~~
import mmap
import os

mm = mmap.mmap(-1, 13)
mm.write(b"Hello world!")

pid = os.fork()

if pid == 0:  # In a child process
    mm.seek(0)
    print(mm.readline())

    mm.close()
~~~

引发一个 [审计事件](3.标准库/sys.md#auditing) `mmap.__new__` 附带参数 `fileno`, `length`, `access`, `offset`。

映射内存的文件对象支持以下方法:

close()¶

    

~~~
关闭 mmap。 后续调用该对象的其他方法将导致引发 ValueError 异常。 此方法将不会关闭打开的文件。

closed¶
~~~
    

~~~
如果文件已关闭则返回 `True`。

在 3.2 版本加入.

find( _sub_ [, _start_ [, _end_ ]])¶
~~~
    

~~~
返回子序列 _sub_ 在对象内被找到的最小索引号，使得 _sub_ 被包含在 [ _start_ , _end_ ] 范围中。 可选参数 _start_ 和 _end_ 会被解读为切片表示法。 如果未找到则返回 `-1`。

在 3.5 版本发生变更: 现在接受可写的 [字节类对象](../glossary.md#term-bytes-like-object)。

flush([ _offset_ [, _size_ ]])¶
~~~
    

~~~
将对文件的内存副本的修改刷新至磁盘。 如果不使用此调用则无法保证在对象被销毁前将修改写回存储。 如果指定了 _offset_ 和 _size_ ，则只将对指定范围内字节的修改刷新至磁盘；在其他情况下，映射的全部范围都会被刷新。 _offset_ 必须为 `PAGESIZE` 或 `ALLOCATIONGRANULARITY` 的倍数。

返回 `None` 以表示成功。 当调用失败时将引发异常。

在 3.8 版本发生变更: 在之前版本中，成功时将返回非零值；在 Windows 下当发生错误时将返回零。 在 Unix 下 成功时将返回零值；当发生错误时将引发异常。

madvise( _option_ [, _start_ [, _length_ ]])¶
~~~
    

~~~
将有关内存区域的建议 _option_ 发送至内核，从 _start_ 开始扩展 _length_ 个字节。 _option_ 必须为系统中可用的 MADV_* 常量 之一。 如果省略 _start_ 和 _length_ ，则会包含整个映射。 在某些系统中（包括 Linux）， _start_ 必须为 `PAGESIZE` 的倍数。

可用性: 具有 `madvise()` 系统调用的系统。

在 3.8 版本加入.

move( _dest_ , _src_ , _count_ )¶
~~~
    

~~~
将从偏移量 _src_ 开始的 _count_ 个字节拷贝到目标索引号 _dest_ 。 如果 mmap 创建时设置了 `ACCESS_READ`，则调用 move 将引发 [`TypeError`](exceptions.md#TypeError "TypeError") 异常。

read([ _n_ ])¶
~~~
    

~~~
返回一个 [`bytes`](stdtypes.md#bytes "bytes")，其中包含从当前文件位置开始的至多 _n_ 个字节。 如果参数省略，为 `None` 或负数，则返回从当前文件位置开始直至映射结尾的所有字节。 文件位置会被更新为返回字节数据之后的位置。

在 3.3 版本发生变更: 参数可被省略或为 `None`。

read_byte()¶
~~~
    

~~~
将当前文件位置上的一个字节以整数形式返回，并让文件位置前进 1。

readline()¶
~~~
    

~~~
返回一个单独的行，从当前文件位置开始直到下一个换行符。 文件位置会被更新为返回字节数据之后的位置。

resize( _newsize_ )¶
~~~
    

~~~
改变映射以及下层文件的大小，如果存在的话。 如果 mmap 创建时设置了 `ACCESS_READ` 或 `ACCESS_COPY`，则改变映射大小将引发 [`TypeError`](exceptions.md#TypeError "TypeError") 异常。

**在 Windows 上** : 如果存在其他针对相同名称文件的映射则改变映射大小将引发 [`OSError`](exceptions.md#OSError "OSError")。 改变匿名映射（即针对分页文件）的大小将静默地创建一个新映射并将原始数据复制到对应新大小的长度。

在 3.11 版本发生变更: 如果在持有另一个映射允许在 Windows 上针对匿名映射改变大小的情况下尝试改变大小则会正确地报告失败

rfind( _sub_ [, _start_ [, _end_ ]])¶
~~~
    

~~~
返回子序列 _sub_ 在对象内被找到的最大索引号，使得 _sub_ 被包含在 [ _start_ , _end_ ] 范围中。 可选参数 _start_ 和 _end_ 会被解读为切片表示法。 如果未找到则返回 `-1`。

在 3.5 版本发生变更: 现在接受可写的 [字节类对象](../glossary.md#term-bytes-like-object)。

seek( _pos_ [, _whence_ ])¶
~~~
    

~~~
设置文件的当前位置。 _whence_ 参数为可选项并且默认为 `os.SEEK_SET` 或 `0` (绝对文件定位)；其他值还有 `os.SEEK_CUR` 或 `1` (相对当前位置查找) 和 `os.SEEK_END` 或 `2` (相对文件末尾查找)。

size()¶
~~~
    

~~~
返回文件的长度，该数值可以大于内存映射区域的大小。

tell()¶
~~~
    

~~~
返回文件指针的当前位置。

write( _bytes_ )¶
~~~
    

~~~
将 _bytes_ 中的字节数据写入文件指针当前位置的内存并返回写入的字节总数 (一定不小于 `len(bytes)`，因为如果写入失败，将会引发 [`ValueError`](exceptions.md#ValueError "ValueError"))。 在字节数据被写入后文件位置将会更新。 如果 mmap 创建时设置了 `ACCESS_READ`，则向其写入将引发 [`TypeError`](exceptions.md#TypeError "TypeError") 异常。

在 3.5 版本发生变更: 现在接受可写的 [字节类对象](../glossary.md#term-bytes-like-object)。

在 3.6 版本发生变更: 现在会返回写入的字节总数。

write_byte( _byte_ )¶
~~~
    

~~~
将整数值 _byte_ 写入文件指针当前位置的内存；文件位置前进 `1`。 如果 mmap 创建时设置了 `ACCESS_READ`，则向其写入将引发 [`TypeError`](exceptions.md#TypeError "TypeError") 异常。

## MADV_* 常量¶

mmap.MADV_NORMAL¶

mmap.MADV_RANDOM¶

mmap.MADV_SEQUENTIAL¶

mmap.MADV_WILLNEED¶

mmap.MADV_DONTNEED¶

mmap.MADV_REMOVE¶

mmap.MADV_DONTFORK¶

mmap.MADV_DOFORK¶

mmap.MADV_HWPOISON¶

mmap.MADV_MERGEABLE¶

mmap.MADV_UNMERGEABLE¶

mmap.MADV_SOFT_OFFLINE¶

mmap.MADV_HUGEPAGE¶

mmap.MADV_NOHUGEPAGE¶

mmap.MADV_DONTDUMP¶

mmap.MADV_DODUMP¶

mmap.MADV_FREE¶

mmap.MADV_NOSYNC¶

mmap.MADV_AUTOSYNC¶

mmap.MADV_NOCORE¶

mmap.MADV_CORE¶

mmap.MADV_PROTECT¶

mmap.MADV_FREE_REUSABLE¶

mmap.MADV_FREE_REUSE¶
~~~
    

~~~
这些选项可被传给 `mmap.madvise()`。 不是每个选项都存在于每个系统中。

可用性: 具有 madvise() 系统调用的系统。

在 3.8 版本加入.

## MAP_* 常量¶

mmap.MAP_SHARED¶

mmap.MAP_PRIVATE¶

mmap.MAP_DENYWRITE¶

mmap.MAP_EXECUTABLE¶

mmap.MAP_ANON¶

mmap.MAP_ANONYMOUS¶

mmap.MAP_POPULATE¶

mmap.MAP_STACK¶

mmap.MAP_ALIGNED_SUPER¶

mmap.MAP_CONCEAL¶
~~~
    

~~~
