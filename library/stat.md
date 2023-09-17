# `stat` \--- 解析 [`stat()`](os.md#os.stat "os.stat") 结果¶

**源代码：** [Lib/stat.py](https://github.com/python/cpython/tree/3.12/Lib/stat.py)

* * *

`stat` 模块定义了一些用于解读 [`os.stat()`](os.md#os.stat "os.stat"), [`os.fstat()`](os.md#os.fstat "os.fstat") 和 [`os.lstat()`](os.md#os.lstat "os.lstat") (如果它们存在) 输出结果的常量和函数。 有关 `stat()`, `fstat()` 和 `lstat()` 调用的完整细节，请参阅你的系统文档。

在 3.4 版本发生变更: stat 模块是通过 C 实现来支持的。

`stat` 模块定义了以下函数来检测特定文件类型：

stat.S_ISDIR( _mode_ )¶

    

~~~
如果 mode 来自一个目录则返回非零值。

stat.S_ISCHR( _mode_ )¶
~~~
    

~~~
如果 mode 来自一个字符特殊设备文件则返回非零值。

stat.S_ISBLK( _mode_ )¶
~~~
    

~~~
如果 mode 来自一个块特殊设备文件则返回非零值。

stat.S_ISREG( _mode_ )¶
~~~
    

~~~
如果 mode 来自一个常规文件则返回非零值。

stat.S_ISFIFO( _mode_ )¶
~~~
    

~~~
如果 mode 来自一个 FIFO (命名管道) 则返回非零值。

stat.S_ISLNK( _mode_ )¶
~~~
    

~~~
如果 mode 来自一个符号链接则返回非零值。

stat.S_ISSOCK( _mode_ )¶
~~~
    

~~~
如果 mode 来自一个套接字则返回非零值。

stat.S_ISDOOR( _mode_ )¶
~~~
    

~~~
如果 mode 来自一个门则返回非零值。

在 3.4 版本加入.

stat.S_ISPORT( _mode_ )¶
~~~
    

~~~
如果 mode 来自一个事件端口则返回非零值。

在 3.4 版本加入.

stat.S_ISWHT( _mode_ )¶
~~~
    

~~~
如果 mode 来自一个白输出则返回非零值。

在 3.4 版本加入.

定义了两个附加函数用于对文件模式进行更一般化的操作：

stat.S_IMODE( _mode_ )¶
~~~
    

~~~
返回文件模式中可由 [`os.chmod()`](os.md#os.chmod "os.chmod") 进行设置的部分 --- 即文件的 permission 位，加上 sticky 位、set-group-id 以及 set-user-id 位（在支持这些部分的系统上）。

stat.S_IFMT( _mode_ )¶
~~~
    

~~~
返回文件模式中描述文件类型的部分（供上面的 `S_IS*()` 函数使用）。

通常，你将使用 `os.path.is*()` 函数来检测文件的类型；这里提供的函数在你要对同一文件执行多项检测并且希望避免每项检测的 `stat()` 系统调用的开销时会很有用。 这些函数也适用于检测有关未被 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 处理的信息，如检测块和字符设备等。

示例:
~~~
    
    
~~~
import os, sys
from stat import *

def walktree(top, callback):
    '''recursively descend the directory tree rooted at top,
       calling the callback function for each regular file'''

    for f in os.listdir(top):
        pathname = os.path.join(top, f)
        mode = os.lstat(pathname).st_mode
        if S_ISDIR(mode):
            # It's a directory, recurse into it
            walktree(pathname, callback)
        elif S_ISREG(mode):
            # It's a file, call the callback function
            callback(pathname)
        else:
            # Unknown file type, print a message
            print('Skipping %s' % pathname)

def visitfile(file):
    print('visiting', file)

if __name__ == '__main__':
    walktree(sys.argv[1], visitfile)
~~~

另外还提供了一个附加的辅助函数用来将文件模式转换为人类易读的字符串：

stat.filemode( _mode_ )¶

    

~~~
将文件模式转换为 '-rwxrwxrwx' 形式的字符串。

在 3.3 版本加入.

在 3.4 版本发生变更: 此函数支持 `S_IFDOOR`, `S_IFPORT` and `S_IFWHT`。

以下所有变量是一些简单的符号索引，用于访问 [`os.stat()`](os.md#os.stat "os.stat"), [`os.fstat()`](os.md#os.fstat "os.fstat") 或 [`os.lstat()`](os.md#os.lstat "os.lstat") 所返回的 10 条目元组。

stat.ST_MODE¶
~~~
    

~~~
inode 保护模式。

stat.ST_INO¶
~~~
    

~~~
Inode 号

stat.ST_DEV¶
~~~
    

~~~
Inode 所在的设备。

stat.ST_NLINK¶
~~~
    

~~~
Inode 拥有的链接数量。

stat.ST_UID¶
~~~
    

~~~
所有者的用户 ID。

stat.ST_GID¶
~~~
    

~~~
所有者的用户组ID。

stat.ST_SIZE¶
~~~
    

~~~
以字节为单位的普通文件大小；对于某些特殊文件则是所等待的数据量。

stat.ST_ATIME¶
~~~
    

~~~
上次访问的时间。

stat.ST_MTIME¶
~~~
    

~~~
上次修改的时间。

stat.ST_CTIME¶
~~~
    

~~~
操作系统所报告的 "ctime"。 在某些系统上（例如 Unix）是元数据的最后修改时间，而在其他系统上（例如 Windows）则是创建时间（请参阅系统平台的文档了解相关细节）。

对于“文件大小”的解析可因文件类型的不同而变化。 对于普通文件就是文件的字节数。 对于大部分种类的 Unix（特别包括 Linux）的 FIFO 和套接字来说，“大小”则是指在调用 [`os.stat()`](os.md#os.stat "os.stat"), [`os.fstat()`](os.md#os.fstat "os.fstat") 或 [`os.lstat()`](os.md#os.lstat "os.lstat") 时等待读取的字节数；这在某些时候很有用处，特别是在一个非阻塞的打开后轮询这些特殊文件中的一个时。 其他字符和块设备的文件大小字段的含义还会有更多变化，具体取决于底层系统调用的实现方式。

以下变量定义了在 `ST_MODE` 字段中使用的旗标。

使用上面的函数会比使用第一组旗标更容易移植：

stat.S_IFSOCK¶
~~~
    

~~~
套接字。

stat.S_IFLNK¶
~~~
    

~~~
符号链接。

stat.S_IFREG¶
~~~
    

~~~
普通文件。

stat.S_IFBLK¶
~~~
    

~~~
块设备。

stat.S_IFDIR¶
~~~
    

~~~
目录。

stat.S_IFCHR¶
~~~
    

~~~
字符设备。

stat.S_IFIFO¶
~~~
    

~~~
先进先出。

stat.S_IFDOOR¶
~~~
    

~~~
门。

在 3.4 版本加入.

stat.S_IFPORT¶
~~~
    

~~~
事件端口。

在 3.4 版本加入.

stat.S_IFWHT¶
~~~
    

~~~
白输出。

在 3.4 版本加入.

备注

`S_IFDOOR`, `S_IFPORT` or `S_IFWHT` 等文件类型在不受系统平台支持时会被定义为 0。

以下旗标还可以 [`os.chmod()`](os.md#os.chmod "os.chmod") 的在 _mode_ 参数中使用：

stat.S_ISUID¶
~~~
    

~~~
设置 UID 位。

stat.S_ISGID¶
~~~
    

~~~
设置分组 ID 位。 这个位有几种特殊用途。 对于目录它表示该目录将使用 BSD 语义：在其中创建的文件将从目录继承其分组 ID，而不是从创建进程的有效分组 ID 继承，并且在其中创建的目录也将设置 `S_ISGID` 位。 对于没有设置分组执行位 (`S_IXGRP`) 的文件，设置分组 ID 位表示强制性文件/记录锁定 (另请参见 `S_ENFMT`)。

stat.S_ISVTX¶
~~~
    

~~~
固定位。 当对目录设置该位时则意味着此目录中的文件只能由文件所有者、目录所有者或特权进程来重命名或删除。

stat.S_IRWXU¶
~~~
    

~~~
文件所有者权限的掩码。

stat.S_IRUSR¶
~~~
    

~~~
所有者具有读取权限。

stat.S_IWUSR¶
~~~
    

~~~
所有者具有写入权限。

stat.S_IXUSR¶
~~~
    

~~~
所有者具有执行权限。

stat.S_IRWXG¶
~~~
    

~~~
组权限的掩码。

stat.S_IRGRP¶
~~~
    

~~~
组具有读取权限。

stat.S_IWGRP¶
~~~
    

~~~
组具有写入权限。

stat.S_IXGRP¶
~~~
    

~~~
组具有执行权限。

stat.S_IRWXO¶
~~~
    

~~~
其他人（不在组中）的权限掩码。

stat.S_IROTH¶
~~~
    

~~~
其他人具有读取权限。

stat.S_IWOTH¶
~~~
    

~~~
其他人具有写入权限。

stat.S_IXOTH¶
~~~
    

~~~
其他人具有执行权限。

stat.S_ENFMT¶
~~~
    

~~~
System V 执行文件锁定。 此旗标是与 `S_ISGID` 共享的：文件/记录锁定会针对未设置分组执行位 (`S_IXGRP`) 的文件强制执行。

stat.S_IREAD¶
~~~
    

~~~
Unix V7 中 `S_IRUSR` 的同义词。

stat.S_IWRITE¶
~~~
    

~~~
Unix V7 中 `S_IWUSR` 的同义词。

stat.S_IEXEC¶
~~~
    

~~~
Unix V7 中 `S_IXUSR` 的同义词。

以下旗标可以在 [`os.chflags()`](os.md#os.chflags "os.chflags") 的 _flags_ 参数中使用：

stat.UF_NODUMP¶
~~~
    

~~~
不要转储文件。

stat.UF_IMMUTABLE¶
~~~
    

~~~
文件不能被更改。

stat.UF_APPEND¶
~~~
    

~~~
文件只能被附加。

stat.UF_OPAQUE¶
~~~
    

~~~
当通过联合堆栈查看时，目录是不透明的。

stat.UF_NOUNLINK¶
~~~
    

~~~
文件不能重命名或删除。

stat.UF_COMPRESSED¶
~~~
    

~~~
文件是压缩存储的（macOS 10.6+）。

stat.UF_HIDDEN¶
~~~
    

~~~
文件不可被显示在 GUI 中（macOS 10.5+）。

stat.SF_ARCHIVED¶
~~~
    

~~~
文件可能已存档。

stat.SF_IMMUTABLE¶
~~~
    

~~~
文件不能被更改。

stat.SF_APPEND¶
~~~
    

~~~
文件只能被附加。

stat.SF_NOUNLINK¶
~~~
    

~~~
文件不能重命名或删除。

stat.SF_SNAPSHOT¶
~~~
    

~~~
文件有一个快照文件

请参阅 *BSD 或 macOS 系统的指南页 _[chflags(2)](https://manpages.debian.org/chflags\(2\))_ 来了解详情。

在 Windows 上，以下文件属性常量可被用来检测 [`os.stat()`](os.md#os.stat "os.stat") 所返回的 `st_file_attributes` 成员中的位。 请参阅 [Windows API 文档](https://msdn.microsoft.com/en-us/library/windows/desktop/gg258117.aspx) 了解有关这些常量含义的详情。

stat.FILE_ATTRIBUTE_ARCHIVE¶

stat.FILE_ATTRIBUTE_COMPRESSED¶

stat.FILE_ATTRIBUTE_DEVICE¶

stat.FILE_ATTRIBUTE_DIRECTORY¶

stat.FILE_ATTRIBUTE_ENCRYPTED¶

stat.FILE_ATTRIBUTE_HIDDEN¶

stat.FILE_ATTRIBUTE_INTEGRITY_STREAM¶

stat.FILE_ATTRIBUTE_NORMAL¶

stat.FILE_ATTRIBUTE_NOT_CONTENT_INDEXED¶

stat.FILE_ATTRIBUTE_NO_SCRUB_DATA¶

stat.FILE_ATTRIBUTE_OFFLINE¶

stat.FILE_ATTRIBUTE_READONLY¶

stat.FILE_ATTRIBUTE_REPARSE_POINT¶

stat.FILE_ATTRIBUTE_SPARSE_FILE¶

stat.FILE_ATTRIBUTE_SYSTEM¶

stat.FILE_ATTRIBUTE_TEMPORARY¶

stat.FILE_ATTRIBUTE_VIRTUAL¶
~~~
    

~~~
在 3.5 版本加入.

在 Windows 上，以下常量可被用来与 [`os.lstat()`](os.md#os.lstat "os.lstat") 所返回的 `st_reparse_tag` 成员进行比较。 这些是最主要的常量，而不是详尽的清单。

stat.IO_REPARSE_TAG_SYMLINK¶

stat.IO_REPARSE_TAG_MOUNT_POINT¶

stat.IO_REPARSE_TAG_APPEXECLINK¶
~~~
    

~~~
