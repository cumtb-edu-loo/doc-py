# `shutil` \--- 高阶文件操作¶

**源代码：** [Lib/shutil.py](https://github.com/python/cpython/tree/3.12/Lib/shutil.py)

* * *

`shutil` 模块提供了一系列对文件和文件集合的高阶操作。 特别是提供了一些支持文件拷贝和删除的函数。 对于单个文件的操作，请参阅 [`os`](os.md#module-os "os: Miscellaneous operating system interfaces.") 模块。

警告

即便是高阶文件拷贝函数 (`shutil.copy()`, `shutil.copy2()`) 也无法拷贝所有的文件元数据。

在 POSIX 平台上，这意味着将丢失文件所有者和组以及 ACL 数据。 在 Mac OS 上，资源钩子和其他元数据不被使用。 这意味着将丢失这些资源并且文件类型和创建者代码将不正确。 在 Windows 上，将不会拷贝文件所有者、ACL 和替代数据流。

## 目录和文件操作¶

shutil.copyfileobj( _fsrc_ , _fdst_ [, _length_ ])¶

    

~~~
将文件类对象 _fsrc_ 的内容拷贝到文件类对象 _fdst_ 。 整数值 _length_ 如果给出则为缓冲区大小。 特别地， _length_ 为负值表示拷贝数据时不对源数据进行分块循环处理；默认情况下会分块读取数据以避免不受控制的内存消耗。 请注意如果 _fsrc_ 对象的当前文件位置不为 0，则只有从当前文件位置到文件末尾的内容会被拷贝。

shutil.copyfile( _src_ , _dst_ , _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
将名为 _src_ 的文件的内容（不包括元数据）拷贝到名为 _dst_ 的文件并以尽可能高效的方式返回 _dst_ 。 _src_ 和 _dst_ 均为路径类对象或以字符串形式给出的路径名。

_dst_ 必须是完整的目标文件名；对于接受目标目录路径的拷贝请参见 `copy()`。 如果 _src_ 和 _dst_ 指定了同一个文件，则将引发 `SameFileError`。

目标位置必须是可写的；否则将引发 [`OSError`](exceptions.md#OSError "OSError") 异常。 如果 _dst_ 已经存在，它将被替换。 特殊文件如字符或块设备以及管道无法用此函数来拷贝。

如果 _follow_symlinks_ 为假值且 _src_ 为符号链接，则将创建一个新的符号链接而不是拷贝 _src_ 所指向的文件。

引发一个 [审计事件](sys.md#auditing) `shutil.copyfile` 附带参数 `src`, `dst`。

在 3.3 版本发生变更: 曾经是引发 [`IOError`](exceptions.md#IOError "IOError") 而不是 [`OSError`](exceptions.md#OSError "OSError")。 增加了 _follow_symlinks_ 参数。 现在是返回 _dst_ 。

在 3.4 版本发生变更: 引发 `SameFileError` 而不是 `Error`。 由于前者是后者的子类，此改变是向后兼容的。

在 3.8 版本发生变更: 可能会在内部使用平台专属的快速拷贝系统调用以更高效地拷贝文件。 参见 依赖于具体平台的高效拷贝操作 一节。

_exception _shutil.SameFileError¶
~~~
    

~~~
此异常会在 `copyfile()` 中的源和目标为同一文件时被引发。

在 3.4 版本加入.

shutil.copymode( _src_ , _dst_ , _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
从 _src_ 拷贝权限位到 _dst_ 。 文件的内容、所有者和分组将不受影响。 _src_ 和 _dst_ 均为路径类对象或字符串形式的路径名。 如果 _follow_symlinks_ 为假值，并且 _src_ 和 _dst_ 均为符号链接，`copymode()` 将尝试修改 _dst_ 本身的模式（而非它所指向的文件）。 此功能并不是在所有平台上均可用；请参阅 `copystat()` 了解详情。 如果 `copymode()` 无法修改本机平台上的符号链接，而它被要求这样做，它将不做任何操作即返回。

引发一个 [审计事件](sys.md#auditing) `shutil.copymode` 附带参数 `src`, `dst`。

在 3.3 版本发生变更: 加入 _follow_symlinks_ 参数。

shutil.copystat( _src_ , _dst_ , _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
从 _src_ 拷贝权限位、最近访问时间、最近修改时间以及旗标到 _dst_ 。 在 Linux上，`copystat()` 还会在可能的情况下拷贝“扩展属性”。 文件的内容、所有者和分组将不受影响。 _src_ 和 _dst_ 均为路径类对象或字符串形式的路径名。

如果 _follow_symlinks_ 为假值，并且 _src_ 和 _dst_ 均指向符号链接，`copystat()` 将作用于符号链接本身而非该符号链接所指向的文件 — 从 _src_ 符号链接读取信息，并将信息写入 _dst_ 符号链接。

备注

并非所有平台者提供检查和修改符号链接的功能。 Python 本身可以告诉你哪些功能是在本机上可用的。

  * 如果 `os.chmod in os.supports_follow_symlinks` 为 `True`，则 `copystat()` 可以修改符号链接的权限位。

  * 如果 `os.utime in os.supports_follow_symlinks` 为 `True`，则 `copystat()` 可以修改符号链接的最近访问和修改时间。

  * 如果 `os.chflags in os.supports_follow_symlinks` 为 `True`，则 `copystat()` 可以修改符号链接的旗标。 (`os.chflags` 不是在所有平台上均可用。)

在此功能部分或全部不可用的平台上，当被要求修改一个符号链接时，`copystat()` 将尽量拷贝所有内容。 `copystat()` 一定不会返回失败信息。

更多信息请参阅 [`os.supports_follow_symlinks`](os.md#os.supports_follow_symlinks "os.supports_follow_symlinks")。

引发一个 [审计事件](sys.md#auditing) `shutil.copystat` 附带参数 `src`, `dst`。

在 3.3 版本发生变更: 添加了 _follow_symlinks_ 参数并且支持 Linux 扩展属性。

shutil.copy( _src_ , _dst_ , _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
将文件 _src_ 拷贝到文件或目录 _dst_ 。 _src_ 和 _dst_ 应为 [路径类对象](../glossary.md#term-path-like-object) 或字符串。 如果 _dst_ 指定了一个目录，文件将使用 _src_ 中的基准文件名拷贝到 _dst_ 中。 如果 _dst_ 指定了一个已存在的文件，它将被替换。 返回新创建文件所对应的路径。

如果 _follow_symlinks_ 为假值且 _src_ 为符号链接，则 _dst_ 也将被创建为符号链接。 如果 _follow_symlinks_ 为真值且 _src_ 为符号链接， _dst_ 将成为 _src_ 所指向的文件的一个副本。

`copy()` 会拷贝文件数据和文件的权限模式 (参见 [`os.chmod()`](os.md#os.chmod "os.chmod"))。 其他元数据，例如文件的创建和修改时间不会被保留。 要保留所有原有的元数据，请改用 `copy2()` 。

引发一个 [审计事件](sys.md#auditing) `shutil.copyfile` 附带参数 `src`, `dst`。

引发一个 [审计事件](sys.md#auditing) `shutil.copymode` 附带参数 `src`, `dst`。

在 3.3 版本发生变更: 添加了 _follow_symlinks_ 参数。 现在会返回新创建文件的路径。

在 3.8 版本发生变更: 可能会在内部使用平台专属的快速拷贝系统调用以更高效地拷贝文件。 参见 依赖于具体平台的高效拷贝操作 一节。

shutil.copy2( _src_ , _dst_ , _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
类似于 `copy()`，区别在于 `copy2()` 还会尝试保留文件的元数据。

当 _follow_symlinks_ 为假值，并且 _src_ 为符号链接时，`copy2()` 会尝试将来自 _src_ 符号链接的所有元数据拷贝到新创建的 _dst_ 符号链接。 但是，此功能不是在所有平台上均可用。 在此功能部分或全部不可用的平台上，`copy2()` 将尽量保留所有元数据，`copy2()` 一定不会由于无法保留文件元数据而引发异常。

`copy2()` 会使用 `copystat()` 来拷贝文件元数据。 请参阅 `copystat()` 了解有关修改符号链接元数据的平台支持的更多信息。

引发一个 [审计事件](sys.md#auditing) `shutil.copyfile` 附带参数 `src`, `dst`。

引发一个 [审计事件](sys.md#auditing) `shutil.copystat` 附带参数 `src`, `dst`。

在 3.3 版本发生变更: 添加了 _follow_symlinks_ 参数，还会尝试拷贝扩展文件系统属性（目前仅限 Linux）。 现在会返回新创建文件的路径。

在 3.8 版本发生变更: 可能会在内部使用平台专属的快速拷贝系统调用以更高效地拷贝文件。 参见 依赖于具体平台的高效拷贝操作 一节。

shutil.ignore_patterns( _* patterns_)¶
~~~
    

~~~
这个工厂函数会创建一个函数，它可被用作 `copytree()` 的 _ignore_ 可调用对象参数，以忽略那些匹配所提供的 glob 风格的 _patterns_ 之一的文件和目录。 参见以下示例。

shutil.copytree( _src_ , _dst_ , _symlinks =False_, _ignore =None_, _copy_function =copy2_, _ignore_dangling_symlinks =False_, _dirs_exist_ok =False_)¶
~~~
    

~~~
递归地将以 _src_ 为根起点的整个目录树拷贝到名为 _dst_ 的目录并返回目标目录。 所需的包含 _dst_ 的中间目录在默认情况下也将被创建。

目录的权限和时间会通过 `copystat()` 来拷贝，单个文件则会使用 `copy2()` 来拷贝。

如果 _symlinks_ 为真值，源目录树中的符号链接会在新目录树中表示为符号链接，并且原链接的元数据在平台允许的情况下也会被拷贝；如果为假值或省略，则会将被链接文件的内容和元数据拷贝到新目录树。

当 _symlinks_ 为假值时，如果符号链接所指向的文件不存在，则会在拷贝进程的末尾将一个异常添加到 `Error` 异常中的错误列表。 如果你希望屏蔽此异常那就将可选的 _ignore_dangling_symlinks_ 旗标设为真值。 请注意此选项在不支持 [`os.symlink()`](os.md#os.symlink "os.symlink") 的平台上将不起作用。

如果给出了 _ignore_ ，它必须是一个可调用对象，该对象将接受 `copytree()` 所访问的目录以及 [`os.listdir()`](os.md#os.listdir "os.listdir") 所返回的目录内容列表作为其参数。 由于 `copytree()` 是递归地被调用的， _ignore_ 可调用对象对于每个被拷贝目录都将被调用一次。 该可调用对象必须返回一个相对于当前目录的目录和文件名序列（即其第二个参数的子集）；随后这些名称将在拷贝进程中被忽略。 `ignore_patterns()` 可被用于创建这种基于 glob 风格模式来忽略特定名称的可调用对象。

如果发生了（一个或多个）异常，将引发一个附带原因列表的 `Error`。

如果给出了 _copy_function_ ，它必须是一个将被用来拷贝每个文件的可调用对象。 它在被调用时会将源路径和目标路径作为参数传入。 默认情况下，`copy2()` 将被使用，但任何支持同样签名（与 `copy()` 一致）都可以使用。

如果 _dirs_exist_ok_ 为（默认的）假值且 _dst_ 已存在，则会引发 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError")。 如果 _dirs_exist_ok_ 为真值，则如果拷贝操作遇到已存在的目录时将继续执行，并且在 _dst_ 目录树中的文件将被 _src_ 目录树中对应的文件所覆盖。

引发一个 [审计事件](sys.md#auditing) `shutil.copytree` 附带参数 `src`, `dst`。

在 3.3 版本发生变更: 当 _symlinks_ 为假值时拷贝元数据。 现在会返回 _dst_ 。

在 3.2 版本发生变更: 添加了 _copy_function_ 参数以允许提供定制的拷贝函数。 添加了 _ignore_dangling_symlinks_ 参数以便在 _symlinks_ 为假值时屏蔽目标不存在的符号链接。

在 3.8 版本发生变更: 可能会在内部使用平台专属的快速拷贝系统调用以更高效地拷贝文件。 参见 依赖于具体平台的高效拷贝操作 一节。

在 3.8 版本加入: _dirs_exist_ok_ 形参。

shutil.rmtree( _path_ , _ignore_errors =False_, _onerror =None_, _*_ , _onexc =None_, _dir_fd =None_)¶
~~~
    

~~~
删除一个完整的目录树； _path_ 必须指向一个目录（但不能是一个目录的符号链接）。 如果 _ignore_errors_ 为真值，则删除失败导致的错误将被忽略；如果为假值或被省略，则此类错误将通过调用由 _onexc_ 或 _onerror_ 所指定的处理句柄来处理，或者如果此参数被省略，异常将被传播给调用方。

本函数支持 [基于目录描述符的相对路径](os.md#dir-fd)。

备注

在支持必要的基于 fd 的函数的平台上，默认会使用 `rmtree()` 的可防御符号链接攻击的版本。 在其他平台上，`rmtree()` 较易遭受符号链接攻击：给定适当的时间和环境，攻击者可以操纵文件系统中的符号链接来删除他们在其他情况下无法访问的文件。 应用程序可以使用 `rmtree.avoids_symlink_attacks` 函数属性来确定此类情况具体是哪一些。

如果提供了 _onexc_ ，它必须为接受三个形参的可调用对象: _function_ , _path_ 和 _excinfo_ 。

第一个形参 _function_ 是引发异常的函数；它依赖于具体的平台和实现。 第二个形参 _path_ 将为传递给 _function_ 的路径名称。 第三个形参 _excinfo_ 是被引发的异常。 由 _onexc_ 所引发的异常将不会被捕获。

已弃用的 _onerror_ 与 _onexc_ 类似，区别在于它接受的第三个形参是从 [`sys.exc_info()`](sys.md#sys.exc_info "sys.exc_info") 返回的元组。

引发了个 [审计事件](sys.md#auditing) `shutil.rmtree`，附带参数 `path`, `dir_fd`。

在 3.3 版本发生变更: 添加了一个防御符号链接攻击的版本，如果平台支持基于 fd 的函数就会被使用。

在 3.8 版本发生变更: 在 Windows 上将不会再在移除连接之前删除目录连接中的内容。

在 3.11 版本发生变更: _dir_fd_ 参数。

在 3.12 版本发生变更: 增加了 _onexc_ 形参，弃用了 _onerror_ 。

rmtree.avoids_symlink_attacks¶
~~~
    

~~~
指明当前平台和实现是否提供防御符号链接攻击的 `rmtree()` 版本。 目前它仅在平台支持基于 fd 的目录访问函数时才返回真值。

在 3.3 版本加入.

shutil.move( _src_ , _dst_ , _copy_function =copy2_)¶
~~~
    

~~~
递归地将一个文件或目录 ( _src_ ) 移至另一位置 ( _dst_ ) 并返回目标位置。

如果目标是已存在的目录，则 _src_ 会被移至该目录下。 如果目标已存在但不是目录，它可能会被覆盖，具体取决于 [`os.rename()`](os.md#os.rename "os.rename") 的语义。

如果目标是在当前文件系统中，则会使用 [`os.rename()`](os.md#os.rename "os.rename")。 在其他情况下， _src_ 将被拷贝至 _dst_ ，使用的函数为 _copy_function_ ，然后目标会被移除。 对于符号链接，则将在 _dst_ 之下或以其本身为名称创建一个指向 _src_ 目标的新符号链接，并且 _src_ 将被移除。

如果给出了 _copy_function_ ，则它必须为接受两个参数 _src_ 和 _dst_ 的可调用对象，并将在 [`os.rename()`](os.md#os.rename "os.rename") 无法使用时被用来将 _src_ 拷贝到 _dst_ 。 如果源是一个目录，则会调用 `copytree()`，并向它传入 _copy_function_ 。 默认的 _copy_function_ 是 `copy2()`。 使用 `copy()` 作为 _copy_function_ 允许在无法附带拷贝元数据时让移动操作成功执行，但其代价是不拷贝任何元数据。

引发一个 [审计事件](sys.md#auditing) `shutil.move` 附带参数 `src`, `dst`。

在 3.3 版本发生变更: 为异类文件系统添加了显式的符号链接处理，以便使它适应 GNU 的 **mv** 的行为。 现在会返回 _dst_ 。

在 3.5 版本发生变更: 增加了 _copy_function_ 关键字参数。

在 3.8 版本发生变更: 可能会在内部使用平台专属的快速拷贝系统调用以更高效地拷贝文件。 参见 依赖于具体平台的高效拷贝操作 一节。

在 3.9 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object) 作为 _src_ 和 _dst_ 。

shutil.disk_usage( _path_ )¶
~~~
    

~~~
返回给定路径的磁盘使用统计数据，形式为一个 [named tuple](../glossary.md#term-named-tuple)，其中包含 _total_ , _used_ 和 _free_ 属性，分别表示总计、已使用和未使用空间的字节数。 _path_ 可以是一个文件或是一个目录。

备注

在 Unix 文件系统中， _path_ 必须指向一个 **已挂载** 文件系统分区中的路径。 在这些平台上，CPython 不会尝试从未挂载的文件系统中获取磁盘使用信息。

在 3.3 版本加入.

在 3.8 版本发生变更: 在 Windows 上， _path_ 现在可以是一个文件或目录。

[可用性](intro.md#availability): Unix, Windows。

shutil.chown( _path_ , _user =None_, _group =None_)¶
~~~
    

~~~
修改给定 _path_ 的所有者 _user_ 和/或 _group_ 。

_user_ 可以是一个系统用户名或 uid； _group_ 同样如此。 要求至少有一个参数。

另请参阅下层的函数 [`os.chown()`](os.md#os.chown "os.chown")。

引发一个 [审计事件](sys.md#auditing) `shutil.chown` 附带参数 `path`, `user`, `group`。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

shutil.which( _cmd_ , _mode =os.F_OK | os.X_OK_, _path =None_)¶
~~~
    

~~~
返回当给定的 _cmd_ 被调用时将要运行的可执行文件的路径。 如果没有 _cmd_ 会被调用则返回 `None`。

_mode_ 是一个传递给 [`os.access()`](os.md#os.access "os.access") 的权限掩码，在默认情况下将确定文件是否存在并且为可执行文件。

当未指定 _path_ 时，将会使用 [`os.environ()`](os.md#os.environ "os.environ") 的结果，返回 "PATH" 值或回退为 [`os.defpath`](os.md#os.defpath "os.defpath")。

在 Windows 上，如果 _mode_ 不包括 `os.X_OK` 则会将当前目录添加到 _path_ 中。 当 _mode_ 包括 `os.X_OK` 时，则将通过 Windows API `NeedCurrentDirectoryForExePathW` 来确定当前目录是否应当添加到 _path_ 中。 要避免在当前工作目录下查找可执行文件：可设置 `NoDefaultCurrentDirectoryInExePath` 环境变量。

在 Windows 上，还会使用 `PATHEXT` 变量来查找尚未包括某个扩展名的命令。 举例来说，如果你调用 `shutil.which("python")`，`which()` 将搜索 `PATHEXT` 以获知应当在 _path_ 中查找 `python.exe`。 例如，在 Windows 上:
~~~
    
    
~~~shell
>>> shutil.which("python")
'C:\\Python33\\python.EXE'
~~~

这也适用于当 _cmd_ 是一个包含目录组成部分路径的情况:

    
    
~~~
>> shutil.which("C:\\Python33\\python")
'C:\\Python33\\python.EXE'
~~~

在 3.3 版本加入.

在 3.8 版本发生变更: 现在可以接受 [`bytes`](stdtypes.md#bytes "bytes") 类型。 如果 _cmd_ 的类型为 [`bytes`](stdtypes.md#bytes "bytes")，结果的类型也将为 [`bytes`](stdtypes.md#bytes "bytes")。

在 3.12 版本发生变更: 在 Windows 上，如果 _mode_ 包括 `os.X_OK` 且 WinAPI `NeedCurrentDirectoryForExePathW(cmd)` 为假值则不会再将当前目录添加到搜索路径中，否则即使当前目录已经在搜索路径中仍会再次添加它；现在 `PATHEXT` 即使当 _cmd_ 包括目录组成部分或以 `PATHEXT` 中的扩展名结束时仍然会被使用；并且没有扩展名的文件名现在也能被找到。

_exception _shutil.Error¶

    

~~~
此异常会收集在多文件操作期间所引发的异常。 对于 `copytree()`，此异常参数将是一个由三元组 ( _srcname_ , _dstname_ , _exception_ ) 构成的列表。

### 依赖于具体平台的高效拷贝操作¶

从 Python 3.8 开始，所有涉及文件拷贝的函数 (`copyfile()`, `copy()`, `copy2()`, `copytree()` 以及 `move()`) 将会使用平台专属的 "fast-copy" 系统调用以便更高效地拷贝文件 (参见 [bpo-33671](https://bugs.python.org/issue?@action=redirect&bpo=33671))。 "fast-copy" 意味着拷贝操作将发生于内核之中，避免像在 "`outfd.write(infd.read())`" 中那样使用 Python 用户空间的缓冲区。

在 macOS 上将会使用 [fcopyfile](http://www.manpagez.com/man/3/copyfile/) 来拷贝文件内容（不含元数据）。

在 Linux 上将会使用 [`os.sendfile()`](os.md#os.sendfile "os.sendfile")。

在 Windows 上 `shutil.copyfile()` 将会使用更大的默认缓冲区（1 MiB 而非 64 KiB）并且会使用基于 [`memoryview()`](stdtypes.md#memoryview "memoryview") 的 `shutil.copyfileobj()` 变种形式。

如果快速拷贝操作失败并且没有数据被写入目标文件，则 shutil 将在内部静默地回退到使用效率较低的 `copyfileobj()` 函数。

在 3.8 版本发生变更.

### copytree 示例¶

一个使用 `ignore_patterns()` 辅助函数的例子:
~~~
    
    
~~~
from shutil import copytree, ignore_patterns

copytree(source, destination, ignore=ignore_patterns('*.pyc', 'tmp*'))
~~~

这将会拷贝除 `.pyc` 文件和以 `tmp` 打头的文件或目录以外的所有条目.

另一个使用 _ignore_ 参数来添加记录调用的例子:

    
    
~~~
from shutil import copytree
import logging

def _logpath(path, names):
    logging.info('Working in %s', path)
    return []   # nothing will be ignored

copytree(source, destination, ignore=_logpath)
~~~

### rmtree 示例¶

这个例子演示了如何在 Windows 上删除一个目录树，其中部分文件设置了只读属性位。 它会使用 onexc 回调函数来清除只读属性并再次尝试删除。 任何后续的失败都将被传播。

    
    
~~~
import os, stat
import shutil

def remove_readonly(func, path, _):
    "Clear the readonly bit and reattempt the removal"
    os.chmod(path, stat.S_IWRITE)
    func(path)

shutil.rmtree(directory, onexc=remove_readonly)
~~~

## 归档操作¶

在 3.2 版本加入.

在 3.5 版本发生变更: 添加了对 _xztar_ 格式的支持。

本模块也提供了用于创建和读取压缩和归档文件的高层级工具。 它们依赖于 [`zipfile`](zipfile.md#module-zipfile "zipfile: Read and write ZIP-format archive files.") 和 [`tarfile`](tarfile.md#module-tarfile "tarfile: Read and write tar-format archive files.") 模块。

shutil.make_archive( _base_name_ , _format_ [, _root_dir_ [, _base_dir_ [, _verbose_ [, _dry_run_ [, _owner_ [, _group_ [, _logger_ ]]]]]]])¶

    

~~~
创建一个归档文件（例如 zip 或 tar）并返回其名称。

_base_name_ 是要创建的文件名称，包括路径，去除任何特定格式的扩展名。 _format_ 是归档格式：为 "zip" (如果 [`zlib`](zlib.md#module-zlib "zlib: Low-level interface to compression and decompression routines compatible with gzip.") 模块可用), "tar", "gztar" (如果 [`zlib`](zlib.md#module-zlib "zlib: Low-level interface to compression and decompression routines compatible with gzip.") 模块可用), "bztar" (如果 [`bz2`](bz2.md#module-bz2 "bz2: Interfaces for bzip2 compression and decompression.") 模块可用) 或 "xztar" (如果 [`lzma`](lzma.md#module-lzma "lzma: A Python wrapper for the liblzma compression library.") 模块可用) 中的一个。

_root_dir_ 是一个目录，它将作为归档文件的根目录，归档中的所有路径都将是它的相对路径；例如，我们通常会在创建归档之前用 chdir 命令切换到 _root_dir_ 。

_base_dir_ 是我们要执行归档的起始目录；也就是说 _base_dir_ 将成为归档中所有文件和目录共有的路径前缀。 _base_dir_ 必须相对于 _root_dir_ 给出。 请参阅 使用 base_dir 的归档程序示例 了解如何同时使用 _base_dir_ 和 _root_dir_ 。

_root_dir_ 和 _base_dir_ 默认均为当前目录。

如果 _dry_run_ 为真值，则不会创建归档文件，但将要被执行的操作会被记录到 _logger_ 。

_owner_ 和 _group_ 将在创建 tar 归档文件时被使用。 默认会使用当前的所有者和分组。

_logger_ 必须是一个兼容 [**PEP 282**](https://peps.python.org/pep-0282/) 的对象，通常为 [`logging.Logger`](logging.md#logging.Logger "logging.Logger") 的实例。

_verbose_ 参数已不再使用并进入弃用状态。

引发一个 [审计事件](sys.md#auditing) `shutil.make_archive` 并附带参数 `base_name`, `format`, `root_dir`, `base_dir`。

备注

此函数在通过 `register_archive_format()` 注册的自定义归档程序不支持 _root_dir_ 参数时时不是线程安全的。 在这种情况下它会临时改变进程的当前工作目录到 _root_dir_ 来执行归档操作。

在 3.8 版本发生变更: 现在对于通过 `format="tar"` 创建的归档文件将使用新式的 pax (POSIX.1-2001) 格式而非旧式的 GNU 格式。

在 3.10.6 版本发生变更: 目前此函数在创建标准 `.zip` 和 tar 归档文件期间会确保是线程安全的。

shutil.get_archive_formats()¶
~~~
    

~~~
返回支持的归档格式列表。 所返回序列中的每个元素为一个元组 `(name, description)`。

默认情况下 `shutil` 提供以下格式:

  * _zip_ : ZIP 文件（如果 [`zlib`](zlib.md#module-zlib "zlib: Low-level interface to compression and decompression routines compatible with gzip.") 模块可用）。

  * _tar_ : 未压缩的 tar 文件。 对于新归档文件将使用 POSIX.1-2001 pax 格式。

  * _gztar_ : gzip 压缩的 tar 文件（如果 [`zlib`](zlib.md#module-zlib "zlib: Low-level interface to compression and decompression routines compatible with gzip.") 模块可用）。

  * _bztar_ : bzip2 压缩的 tar 文件（如果 [`bz2`](bz2.md#module-bz2 "bz2: Interfaces for bzip2 compression and decompression.") 模块可用）。

  * _xztar_ : xz 压缩的 tar 文件（如果 [`lzma`](lzma.md#module-lzma "lzma: A Python wrapper for the liblzma compression library.") 模块可用）。

你可以通过使用 `register_archive_format()` 注册新的格式或为任何现有格式提供你自己的归档器。

shutil.register_archive_format( _name_ , _function_ [, _extra_args_ [, _description_ ]])¶
~~~
    

~~~
为 _name_ 格式注册一个归档器。

_function_ 是将被用来解包归档文件的可调用对象。 该可调用对象将接收要创建文件的 _base_name_ ，再加上要归档内容的 _base_dir_ (其默认值为 [`os.curdir`](os.md#os.curdir "os.curdir"))。 更多参数会被作为关键字参数传入: _owner_ , _group_ , _dry_run_ 和 _logger_ (与向 `make_archive()` 传入的参数一致)。

如果 _function_ 将自定义属性 `function.supports_root_dir` 设为 `True`，则会以关键字参数形式传递 _root_dir_ 参数。 否则进程的当前工作目录将在调用 _function_ 之前被临时更改为 _root_dir_ 。 在此情况下 `make_archive()` 将不是线程安全的。

如果给出了 _extra_args_ ，则其应为一个 `(name, value)` 对的序列，将在归档器可调用对象被使用时作为附加的关键字参数。

_description_ 由 `get_archive_formats()` 使用，它将返回归档器的列表。 默认值为一个空字符串。

在 3.12 版本发生变更: 增加了对支持 _root_dir_ 参数的函数的支持。

shutil.unregister_archive_format( _name_ )¶
~~~
    

~~~
从支持的格式中移除归档格式 _name_ 。

shutil.unpack_archive( _filename_ [, _extract_dir_ [, _format_ [, _filter_ ]]])¶
~~~
    

~~~
解包一个归档文件。 _filename_ 是归档文件的完整路径。

_extract_dir_ 是归档文件解包的目标目录名称。 如果未提供，则将使用当前工作目录。

_format_ 是归档格式：应为 "zip", "tar", "gztar", "bztar" 或 "xztar" 之一。 或者任何通过 `register_unpack_format()` 注册的其他格式。 如果未提供，`unpack_archive()` 将使用归档文件的扩展名来检查是否注册了对应于该扩展名的解包器。 在未找到任何解包器的情况下，将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

仅限关键字参数 _filter_ 将被传给下层的解包函数。 对于 zip 文件， _filter_ 将不被接受。 对于 tar 文件，推荐将其设为 `'data'`，除非使用了 tar 专属的特征且为 UNIX 类文件系统。 （请参阅 [解压缩过滤器](tarfile.md#tarfile-extraction-filter) 了解详情。） `'data'` 将在 Python 3.14 中成为 tar 文件的默认过滤器。

引发一个 [审计事件](sys.md#auditing) `shutil.unpack_archive` 附带参数 `filename`, `extract_dir`, `format`。

警告

绝不要未经预先检验就从不可靠的源中提取归档文件。 这样有可能会在 _extract_dir_ 参数所指定的路径之外创建文件，例如某些成员具有以 "/" 打头的绝对路径文件名或是以两个点号 ".." 打头的文件名。

在 3.7 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object) 作为 _filename_ 和 _extract_dir_ 。

在 3.12 版本发生变更: 增加了 _filter_ 参数。

shutil.register_unpack_format( _name_ , _extensions_ , _function_ [, _extra_args_ [, _description_ ]])¶
~~~
    

~~~
注册一个解包格式。 _name_ 为格式名称而 _extensions_ 为对应于该格式的扩展名列表，例如 Zip 文件的扩展名为 `.zip`。

_function_ 是将被用于解包归档的可调用对象。 该可调用对象将接受:

  * 归档的路径，为位置参数;

  * 归档要提取到的目录，为位置参数;

  * 可选的 _filter_ 关键字参数，如果有提供给 `unpack_archive()` 的话;

  * 额外的关键字参数，由 `(name, value)` 元组组成的序列 _extra_args_ 指明。

可以提供 _description_ 来描述该格式，它将被 `get_unpack_formats()` 返回。

shutil.unregister_unpack_format( _name_ )¶
~~~
    

~~~
撤销注册一个解包格式。 _name_ 为格式的名称。

shutil.get_unpack_formats()¶
~~~
    

~~~
返回所有已注册的解包格式列表。 所返回序列中的每个元素为一个元组 `(name, extensions, description)`。

默认情况下 `shutil` 提供以下格式:

  * _zip_ : ZIP 文件（只有在相应模块可用时才能解包压缩文件）。

  * _tar_ : 未压缩的 tar 文件。

  * _gztar_ : gzip 压缩的 tar 文件（如果 [`zlib`](zlib.md#module-zlib "zlib: Low-level interface to compression and decompression routines compatible with gzip.") 模块可用）。

  * _bztar_ : bzip2 压缩的 tar 文件（如果 [`bz2`](bz2.md#module-bz2 "bz2: Interfaces for bzip2 compression and decompression.") 模块可用）。

  * _xztar_ : xz 压缩的 tar 文件（如果 [`lzma`](lzma.md#module-lzma "lzma: A Python wrapper for the liblzma compression library.") 模块可用）。

你可以通过使用 `register_unpack_format()` 注册新的格式或为任何现有格式提供你自己的解包器。

### 归档程序示例¶

在这个示例中，我们创建了一个 gzip 压缩的 tar 归档文件，其中包含用户的 `.ssh` 目录下的所有文件:
~~~
    
    
~~~shell
>>> from shutil import make_archive
>>> import os
>>> archive_name = os.path.expanduser(os.path.join('~', 'myarchive'))
>>> root_dir = os.path.expanduser(os.path.join('~', '.ssh'))
>>> make_archive(archive_name, 'gztar', root_dir)
'/Users/tarek/myarchive.tar.gz'
~~~

结果归档文件中包含有:

    
    
~~~
$ tar -tzvf /Users/tarek/myarchive.tar.gz
drwx------ tarek/staff       0 2010-02-01 16:23:40 ./
-rw-r--r-- tarek/staff     609 2008-06-09 13:26:54 ./authorized_keys
-rwxr-xr-x tarek/staff      65 2008-06-09 13:26:54 ./config
-rwx------ tarek/staff     668 2008-06-09 13:26:54 ./id_dsa
-rwxr-xr-x tarek/staff     609 2008-06-09 13:26:54 ./id_dsa.pub
-rw------- tarek/staff    1675 2008-06-09 13:26:54 ./id_rsa
-rw-r--r-- tarek/staff     397 2008-06-09 13:26:54 ./id_rsa.pub
-rw-r--r-- tarek/staff   37192 2010-02-06 18:23:10 ./known_hosts
~~~

### 使用 _base_dir_ 的归档程序示例¶

在这个例子中，与 上面的例子 类似，我们演示了如何使用 `make_archive()`，但这次是使用 _base_dir_ 。 我们现在具有如下的目录结构:

    
    
~~~
$ tree tmp
tmp
└── root
    └── structure
        ├── content
            └── please_add.txt
        └── do_not_add.txt
~~~

在最终的归档中，应当会包括 `please_add.txt`，但不应当包括 `do_not_add.txt`。 因此我们使用以下代码:

    
    
~~~shell
>>> from shutil import make_archive
>>> import os
>>> archive_name = os.path.expanduser(os.path.join('~', 'myarchive'))
>>> make_archive(
...     archive_name,
...     'tar',
...     root_dir='tmp/root',
...     base_dir='structure/content',
... )
'/Users/tarek/my_archive.tar'
~~~

列出结果归档中的文件我们将会得到:

    
    
~~~
$ python -m tarfile -l /Users/tarek/myarchive.tar
structure/content/
structure/content/please_add.txt
~~~

## 查询输出终端的尺寸¶

shutil.get_terminal_size( _fallback =(columns, lines)_)¶

    

~~~
