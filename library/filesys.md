# 文件和目录访问¶

本章中描述的模块处理磁盘文件和目录。 例如，有一些模块用于读取文件的属性，以可移植的方式操作路径以及创建临时文件。 本章的完整模块列表如下：

  * [`pathlib` \--- 面向对象的文件系统路径](pathlib.md)
    * [基础使用](pathlib.md#basic-use)
    * [Exceptions](pathlib.md#exceptions)
      * [`UnsupportedOperation`](pathlib.md#pathlib.UnsupportedOperation)
    * [纯路径](pathlib.md#pure-paths)
      * [`PurePath`](pathlib.md#pathlib.PurePath)
      * [`PurePosixPath`](pathlib.md#pathlib.PurePosixPath)
      * [`PureWindowsPath`](pathlib.md#pathlib.PureWindowsPath)
      * [通用性质](pathlib.md#general-properties)
      * [运算符](pathlib.md#operators)
      * [访问个别部分](pathlib.md#accessing-individual-parts)
        * [`PurePath.parts`](pathlib.md#pathlib.PurePath.parts)
      * [方法和特征属性](pathlib.md#methods-and-properties)
        * [`PurePath.pathmod`](pathlib.md#pathlib.PurePath.pathmod)
        * [`PurePath.drive`](pathlib.md#pathlib.PurePath.drive)
        * [`PurePath.root`](pathlib.md#pathlib.PurePath.root)
        * [`PurePath.anchor`](pathlib.md#pathlib.PurePath.anchor)
        * [`PurePath.parents`](pathlib.md#pathlib.PurePath.parents)
        * [`PurePath.parent`](pathlib.md#pathlib.PurePath.parent)
        * [`PurePath.name`](pathlib.md#pathlib.PurePath.name)
        * [`PurePath.suffix`](pathlib.md#pathlib.PurePath.suffix)
        * [`PurePath.suffixes`](pathlib.md#pathlib.PurePath.suffixes)
        * [`PurePath.stem`](pathlib.md#pathlib.PurePath.stem)
        * [`PurePath.as_posix()`](pathlib.md#pathlib.PurePath.as_posix)
        * [`PurePath.as_uri()`](pathlib.md#pathlib.PurePath.as_uri)
        * [`PurePath.is_absolute()`](pathlib.md#pathlib.PurePath.is_absolute)
        * [`PurePath.is_relative_to()`](pathlib.md#pathlib.PurePath.is_relative_to)
        * [`PurePath.is_reserved()`](pathlib.md#pathlib.PurePath.is_reserved)
        * [`PurePath.joinpath()`](pathlib.md#pathlib.PurePath.joinpath)
        * [`PurePath.match()`](pathlib.md#pathlib.PurePath.match)
        * [`PurePath.relative_to()`](pathlib.md#pathlib.PurePath.relative_to)
        * [`PurePath.with_name()`](pathlib.md#pathlib.PurePath.with_name)
        * [`PurePath.with_stem()`](pathlib.md#pathlib.PurePath.with_stem)
        * [`PurePath.with_suffix()`](pathlib.md#pathlib.PurePath.with_suffix)
        * [`PurePath.with_segments()`](pathlib.md#pathlib.PurePath.with_segments)
    * [具体路径](pathlib.md#concrete-paths)
      * [`Path`](pathlib.md#pathlib.Path)
      * [`PosixPath`](pathlib.md#pathlib.PosixPath)
      * [`WindowsPath`](pathlib.md#pathlib.WindowsPath)
      * [方法](pathlib.md#methods)
        * [`Path.cwd()`](pathlib.md#pathlib.Path.cwd)
        * [`Path.home()`](pathlib.md#pathlib.Path.home)
        * [`Path.stat()`](pathlib.md#pathlib.Path.stat)
        * [`Path.chmod()`](pathlib.md#pathlib.Path.chmod)
        * [`Path.exists()`](pathlib.md#pathlib.Path.exists)
        * [`Path.expanduser()`](pathlib.md#pathlib.Path.expanduser)
        * [`Path.glob()`](pathlib.md#pathlib.Path.glob)
        * [`Path.group()`](pathlib.md#pathlib.Path.group)
        * [`Path.is_dir()`](pathlib.md#pathlib.Path.is_dir)
        * [`Path.is_file()`](pathlib.md#pathlib.Path.is_file)
        * [`Path.is_junction()`](pathlib.md#pathlib.Path.is_junction)
        * [`Path.is_mount()`](pathlib.md#pathlib.Path.is_mount)
        * [`Path.is_symlink()`](pathlib.md#pathlib.Path.is_symlink)
        * [`Path.is_socket()`](pathlib.md#pathlib.Path.is_socket)
        * [`Path.is_fifo()`](pathlib.md#pathlib.Path.is_fifo)
        * [`Path.is_block_device()`](pathlib.md#pathlib.Path.is_block_device)
        * [`Path.is_char_device()`](pathlib.md#pathlib.Path.is_char_device)
        * [`Path.iterdir()`](pathlib.md#pathlib.Path.iterdir)
        * [`Path.walk()`](pathlib.md#pathlib.Path.walk)
        * [`Path.lchmod()`](pathlib.md#pathlib.Path.lchmod)
        * [`Path.lstat()`](pathlib.md#pathlib.Path.lstat)
        * [`Path.mkdir()`](pathlib.md#pathlib.Path.mkdir)
        * [`Path.open()`](pathlib.md#pathlib.Path.open)
        * [`Path.owner()`](pathlib.md#pathlib.Path.owner)
        * [`Path.read_bytes()`](pathlib.md#pathlib.Path.read_bytes)
        * [`Path.read_text()`](pathlib.md#pathlib.Path.read_text)
        * [`Path.readlink()`](pathlib.md#pathlib.Path.readlink)
        * [`Path.rename()`](pathlib.md#pathlib.Path.rename)
        * [`Path.replace()`](pathlib.md#pathlib.Path.replace)
        * [`Path.absolute()`](pathlib.md#pathlib.Path.absolute)
        * [`Path.resolve()`](pathlib.md#pathlib.Path.resolve)
        * [`Path.rglob()`](pathlib.md#pathlib.Path.rglob)
        * [`Path.rmdir()`](pathlib.md#pathlib.Path.rmdir)
        * [`Path.samefile()`](pathlib.md#pathlib.Path.samefile)
        * [`Path.symlink_to()`](pathlib.md#pathlib.Path.symlink_to)
        * [`Path.hardlink_to()`](pathlib.md#pathlib.Path.hardlink_to)
        * [`Path.touch()`](pathlib.md#pathlib.Path.touch)
        * [`Path.unlink()`](pathlib.md#pathlib.Path.unlink)
        * [`Path.write_bytes()`](pathlib.md#pathlib.Path.write_bytes)
        * [`Path.write_text()`](pathlib.md#pathlib.Path.write_text)
    * [对应的 `os` 模块的工具](pathlib.md#correspondence-to-tools-in-the-os-module)
  * [`os.path` \--- 常用路径操作](os.path.md)
    * [`abspath()`](os.path.md#os.path.abspath)
    * [`basename()`](os.path.md#os.path.basename)
    * [`commonpath()`](os.path.md#os.path.commonpath)
    * [`commonprefix()`](os.path.md#os.path.commonprefix)
    * [`dirname()`](os.path.md#os.path.dirname)
    * [`exists()`](os.path.md#os.path.exists)
    * [`lexists()`](os.path.md#os.path.lexists)
    * [`expanduser()`](os.path.md#os.path.expanduser)
    * [`expandvars()`](os.path.md#os.path.expandvars)
    * [`getatime()`](os.path.md#os.path.getatime)
    * [`getmtime()`](os.path.md#os.path.getmtime)
    * [`getctime()`](os.path.md#os.path.getctime)
    * [`getsize()`](os.path.md#os.path.getsize)
    * [`isabs()`](os.path.md#os.path.isabs)
    * [`isfile()`](os.path.md#os.path.isfile)
    * [`isdir()`](os.path.md#os.path.isdir)
    * [`isjunction()`](os.path.md#os.path.isjunction)
    * [`islink()`](os.path.md#os.path.islink)
    * [`ismount()`](os.path.md#os.path.ismount)
    * [`isdevdrive()`](os.path.md#os.path.isdevdrive)
    * [`join()`](os.path.md#os.path.join)
    * [`normcase()`](os.path.md#os.path.normcase)
    * [`normpath()`](os.path.md#os.path.normpath)
    * [`realpath()`](os.path.md#os.path.realpath)
    * [`relpath()`](os.path.md#os.path.relpath)
    * [`samefile()`](os.path.md#os.path.samefile)
    * [`sameopenfile()`](os.path.md#os.path.sameopenfile)
    * [`samestat()`](os.path.md#os.path.samestat)
    * [`split()`](os.path.md#os.path.split)
    * [`splitdrive()`](os.path.md#os.path.splitdrive)
    * [`splitroot()`](os.path.md#os.path.splitroot)
    * [`splitext()`](os.path.md#os.path.splitext)
    * [`supports_unicode_filenames`](os.path.md#os.path.supports_unicode_filenames)
  * [`fileinput` \--- 迭代来自多个输入流的行](fileinput.md)
    * [`input()`](fileinput.md#fileinput.input)
    * [`filename()`](fileinput.md#fileinput.filename)
    * [`fileno()`](fileinput.md#fileinput.fileno)
    * [`lineno()`](fileinput.md#fileinput.lineno)
    * [`filelineno()`](fileinput.md#fileinput.filelineno)
    * [`isfirstline()`](fileinput.md#fileinput.isfirstline)
    * [`isstdin()`](fileinput.md#fileinput.isstdin)
    * [`nextfile()`](fileinput.md#fileinput.nextfile)
    * [`close()`](fileinput.md#fileinput.close)
    * [`FileInput`](fileinput.md#fileinput.FileInput)
    * [`hook_compressed()`](fileinput.md#fileinput.hook_compressed)
    * [`hook_encoded()`](fileinput.md#fileinput.hook_encoded)
  * [`stat` \--- 解析 `stat()` 结果](stat.md)
    * [`S_ISDIR()`](stat.md#stat.S_ISDIR)
    * [`S_ISCHR()`](stat.md#stat.S_ISCHR)
    * [`S_ISBLK()`](stat.md#stat.S_ISBLK)
    * [`S_ISREG()`](stat.md#stat.S_ISREG)
    * [`S_ISFIFO()`](stat.md#stat.S_ISFIFO)
    * [`S_ISLNK()`](stat.md#stat.S_ISLNK)
    * [`S_ISSOCK()`](stat.md#stat.S_ISSOCK)
    * [`S_ISDOOR()`](stat.md#stat.S_ISDOOR)
    * [`S_ISPORT()`](stat.md#stat.S_ISPORT)
    * [`S_ISWHT()`](stat.md#stat.S_ISWHT)
    * [`S_IMODE()`](stat.md#stat.S_IMODE)
    * [`S_IFMT()`](stat.md#stat.S_IFMT)
    * [`filemode()`](stat.md#stat.filemode)
    * [`ST_MODE`](stat.md#stat.ST_MODE)
    * [`ST_INO`](stat.md#stat.ST_INO)
    * [`ST_DEV`](stat.md#stat.ST_DEV)
    * [`ST_NLINK`](stat.md#stat.ST_NLINK)
    * [`ST_UID`](stat.md#stat.ST_UID)
    * [`ST_GID`](stat.md#stat.ST_GID)
    * [`ST_SIZE`](stat.md#stat.ST_SIZE)
    * [`ST_ATIME`](stat.md#stat.ST_ATIME)
    * [`ST_MTIME`](stat.md#stat.ST_MTIME)
    * [`ST_CTIME`](stat.md#stat.ST_CTIME)
    * [`S_IFSOCK`](stat.md#stat.S_IFSOCK)
    * [`S_IFLNK`](stat.md#stat.S_IFLNK)
    * [`S_IFREG`](stat.md#stat.S_IFREG)
    * [`S_IFBLK`](stat.md#stat.S_IFBLK)
    * [`S_IFDIR`](stat.md#stat.S_IFDIR)
    * [`S_IFCHR`](stat.md#stat.S_IFCHR)
    * [`S_IFIFO`](stat.md#stat.S_IFIFO)
    * [`S_IFDOOR`](stat.md#stat.S_IFDOOR)
    * [`S_IFPORT`](stat.md#stat.S_IFPORT)
    * [`S_IFWHT`](stat.md#stat.S_IFWHT)
    * [`S_ISUID`](stat.md#stat.S_ISUID)
    * [`S_ISGID`](stat.md#stat.S_ISGID)
    * [`S_ISVTX`](stat.md#stat.S_ISVTX)
    * [`S_IRWXU`](stat.md#stat.S_IRWXU)
    * [`S_IRUSR`](stat.md#stat.S_IRUSR)
    * [`S_IWUSR`](stat.md#stat.S_IWUSR)
    * [`S_IXUSR`](stat.md#stat.S_IXUSR)
    * [`S_IRWXG`](stat.md#stat.S_IRWXG)
    * [`S_IRGRP`](stat.md#stat.S_IRGRP)
    * [`S_IWGRP`](stat.md#stat.S_IWGRP)
    * [`S_IXGRP`](stat.md#stat.S_IXGRP)
    * [`S_IRWXO`](stat.md#stat.S_IRWXO)
    * [`S_IROTH`](stat.md#stat.S_IROTH)
    * [`S_IWOTH`](stat.md#stat.S_IWOTH)
    * [`S_IXOTH`](stat.md#stat.S_IXOTH)
    * [`S_ENFMT`](stat.md#stat.S_ENFMT)
    * [`S_IREAD`](stat.md#stat.S_IREAD)
    * [`S_IWRITE`](stat.md#stat.S_IWRITE)
    * [`S_IEXEC`](stat.md#stat.S_IEXEC)
    * [`UF_NODUMP`](stat.md#stat.UF_NODUMP)
    * [`UF_IMMUTABLE`](stat.md#stat.UF_IMMUTABLE)
    * [`UF_APPEND`](stat.md#stat.UF_APPEND)
    * [`UF_OPAQUE`](stat.md#stat.UF_OPAQUE)
    * [`UF_NOUNLINK`](stat.md#stat.UF_NOUNLINK)
    * [`UF_COMPRESSED`](stat.md#stat.UF_COMPRESSED)
    * [`UF_HIDDEN`](stat.md#stat.UF_HIDDEN)
    * [`SF_ARCHIVED`](stat.md#stat.SF_ARCHIVED)
    * [`SF_IMMUTABLE`](stat.md#stat.SF_IMMUTABLE)
    * [`SF_APPEND`](stat.md#stat.SF_APPEND)
    * [`SF_NOUNLINK`](stat.md#stat.SF_NOUNLINK)
    * [`SF_SNAPSHOT`](stat.md#stat.SF_SNAPSHOT)
    * [`FILE_ATTRIBUTE_ARCHIVE`](stat.md#stat.FILE_ATTRIBUTE_ARCHIVE)
    * [`FILE_ATTRIBUTE_COMPRESSED`](stat.md#stat.FILE_ATTRIBUTE_COMPRESSED)
    * [`FILE_ATTRIBUTE_DEVICE`](stat.md#stat.FILE_ATTRIBUTE_DEVICE)
    * [`FILE_ATTRIBUTE_DIRECTORY`](stat.md#stat.FILE_ATTRIBUTE_DIRECTORY)
    * [`FILE_ATTRIBUTE_ENCRYPTED`](stat.md#stat.FILE_ATTRIBUTE_ENCRYPTED)
    * [`FILE_ATTRIBUTE_HIDDEN`](stat.md#stat.FILE_ATTRIBUTE_HIDDEN)
    * [`FILE_ATTRIBUTE_INTEGRITY_STREAM`](stat.md#stat.FILE_ATTRIBUTE_INTEGRITY_STREAM)
    * [`FILE_ATTRIBUTE_NORMAL`](stat.md#stat.FILE_ATTRIBUTE_NORMAL)
    * [`FILE_ATTRIBUTE_NOT_CONTENT_INDEXED`](stat.md#stat.FILE_ATTRIBUTE_NOT_CONTENT_INDEXED)
    * [`FILE_ATTRIBUTE_NO_SCRUB_DATA`](stat.md#stat.FILE_ATTRIBUTE_NO_SCRUB_DATA)
    * [`FILE_ATTRIBUTE_OFFLINE`](stat.md#stat.FILE_ATTRIBUTE_OFFLINE)
    * [`FILE_ATTRIBUTE_READONLY`](stat.md#stat.FILE_ATTRIBUTE_READONLY)
    * [`FILE_ATTRIBUTE_REPARSE_POINT`](stat.md#stat.FILE_ATTRIBUTE_REPARSE_POINT)
    * [`FILE_ATTRIBUTE_SPARSE_FILE`](stat.md#stat.FILE_ATTRIBUTE_SPARSE_FILE)
    * [`FILE_ATTRIBUTE_SYSTEM`](stat.md#stat.FILE_ATTRIBUTE_SYSTEM)
    * [`FILE_ATTRIBUTE_TEMPORARY`](stat.md#stat.FILE_ATTRIBUTE_TEMPORARY)
    * [`FILE_ATTRIBUTE_VIRTUAL`](stat.md#stat.FILE_ATTRIBUTE_VIRTUAL)
    * [`IO_REPARSE_TAG_SYMLINK`](stat.md#stat.IO_REPARSE_TAG_SYMLINK)
    * [`IO_REPARSE_TAG_MOUNT_POINT`](stat.md#stat.IO_REPARSE_TAG_MOUNT_POINT)
    * [`IO_REPARSE_TAG_APPEXECLINK`](stat.md#stat.IO_REPARSE_TAG_APPEXECLINK)
  * [`filecmp` \--- 文件及目录的比较](filecmp.md)
    * [`cmp()`](filecmp.md#filecmp.cmp)
    * [`cmpfiles()`](filecmp.md#filecmp.cmpfiles)
    * [`clear_cache()`](filecmp.md#filecmp.clear_cache)
    * [`dircmp` 类](filecmp.md#the-dircmp-class)
      * [`dircmp`](filecmp.md#filecmp.dircmp)
        * [`dircmp.report()`](filecmp.md#filecmp.dircmp.report)
        * [`dircmp.report_partial_closure()`](filecmp.md#filecmp.dircmp.report_partial_closure)
        * [`dircmp.report_full_closure()`](filecmp.md#filecmp.dircmp.report_full_closure)
        * [`dircmp.left`](filecmp.md#filecmp.dircmp.left)
        * [`dircmp.right`](filecmp.md#filecmp.dircmp.right)
        * [`dircmp.left_list`](filecmp.md#filecmp.dircmp.left_list)
        * [`dircmp.right_list`](filecmp.md#filecmp.dircmp.right_list)
        * [`dircmp.common`](filecmp.md#filecmp.dircmp.common)
        * [`dircmp.left_only`](filecmp.md#filecmp.dircmp.left_only)
        * [`dircmp.right_only`](filecmp.md#filecmp.dircmp.right_only)
        * [`dircmp.common_dirs`](filecmp.md#filecmp.dircmp.common_dirs)
        * [`dircmp.common_files`](filecmp.md#filecmp.dircmp.common_files)
        * [`dircmp.common_funny`](filecmp.md#filecmp.dircmp.common_funny)
        * [`dircmp.same_files`](filecmp.md#filecmp.dircmp.same_files)
        * [`dircmp.diff_files`](filecmp.md#filecmp.dircmp.diff_files)
        * [`dircmp.funny_files`](filecmp.md#filecmp.dircmp.funny_files)
        * [`dircmp.subdirs`](filecmp.md#filecmp.dircmp.subdirs)
      * [`DEFAULT_IGNORES`](filecmp.md#filecmp.DEFAULT_IGNORES)
  * [`tempfile` \--- 生成临时文件和目录](tempfile.md)
    * [`TemporaryFile()`](tempfile.md#tempfile.TemporaryFile)
    * [`NamedTemporaryFile()`](tempfile.md#tempfile.NamedTemporaryFile)
    * [`SpooledTemporaryFile`](tempfile.md#tempfile.SpooledTemporaryFile)
    * [`TemporaryDirectory`](tempfile.md#tempfile.TemporaryDirectory)
    * [`mkstemp()`](tempfile.md#tempfile.mkstemp)
    * [`mkdtemp()`](tempfile.md#tempfile.mkdtemp)
    * [`gettempdir()`](tempfile.md#tempfile.gettempdir)
    * [`gettempdirb()`](tempfile.md#tempfile.gettempdirb)
    * [`gettempprefix()`](tempfile.md#tempfile.gettempprefix)
    * [`gettempprefixb()`](tempfile.md#tempfile.gettempprefixb)
    * [`tempdir`](tempfile.md#tempfile.tempdir)
    * [例子](tempfile.md#examples)
    * [已弃用的函数和变量](tempfile.md#deprecated-functions-and-variables)
      * [`mktemp()`](tempfile.md#tempfile.mktemp)
  * [`glob` \--- Unix 风格路径名模式扩展](glob.md)
    * [`glob()`](glob.md#glob.glob)
    * [`iglob()`](glob.md#glob.iglob)
    * [`escape()`](glob.md#glob.escape)
  * [`fnmatch` \--- Unix 文件名模式匹配](fnmatch.md)
    * [`fnmatch()`](fnmatch.md#fnmatch.fnmatch)
    * [`fnmatchcase()`](fnmatch.md#fnmatch.fnmatchcase)
    * [`filter()`](fnmatch.md#fnmatch.filter)
    * [`translate()`](fnmatch.md#fnmatch.translate)
  * [`linecache` \--- 随机读写文本行](linecache.md)
    * [`getline()`](linecache.md#linecache.getline)
    * [`clearcache()`](linecache.md#linecache.clearcache)
    * [`checkcache()`](linecache.md#linecache.checkcache)
    * [`lazycache()`](linecache.md#linecache.lazycache)
  * [`shutil` \--- 高阶文件操作](shutil.md)
    * [目录和文件操作](shutil.md#directory-and-files-operations)
      * [`copyfileobj()`](shutil.md#shutil.copyfileobj)
      * [`copyfile()`](shutil.md#shutil.copyfile)
      * [`SameFileError`](shutil.md#shutil.SameFileError)
      * [`copymode()`](shutil.md#shutil.copymode)
      * [`copystat()`](shutil.md#shutil.copystat)
      * [`copy()`](shutil.md#shutil.copy)
      * [`copy2()`](shutil.md#shutil.copy2)
      * [`ignore_patterns()`](shutil.md#shutil.ignore_patterns)
      * [`copytree()`](shutil.md#shutil.copytree)
      * [`rmtree()`](shutil.md#shutil.rmtree)
        * [`rmtree.avoids_symlink_attacks`](shutil.md#shutil.rmtree.avoids_symlink_attacks)
      * [`move()`](shutil.md#shutil.move)
      * [`disk_usage()`](shutil.md#shutil.disk_usage)
      * [`chown()`](shutil.md#shutil.chown)
      * [`which()`](shutil.md#shutil.which)
      * [`Error`](shutil.md#shutil.Error)
      * [依赖于具体平台的高效拷贝操作](shutil.md#platform-dependent-efficient-copy-operations)
      * [copytree 示例](shutil.md#copytree-example)
      * [rmtree 示例](shutil.md#rmtree-example)
    * [归档操作](shutil.md#archiving-operations)
      * [`make_archive()`](shutil.md#shutil.make_archive)
      * [`get_archive_formats()`](shutil.md#shutil.get_archive_formats)
      * [`register_archive_format()`](shutil.md#shutil.register_archive_format)
      * [`unregister_archive_format()`](shutil.md#shutil.unregister_archive_format)
      * [`unpack_archive()`](shutil.md#shutil.unpack_archive)
      * [`register_unpack_format()`](shutil.md#shutil.register_unpack_format)
      * [`unregister_unpack_format()`](shutil.md#shutil.unregister_unpack_format)
      * [`get_unpack_formats()`](shutil.md#shutil.get_unpack_formats)
      * [归档程序示例](shutil.md#archiving-example)
      * [使用 _base_dir_ 的归档程序示例](shutil.md#archiving-example-with-base-dir)
    * [查询输出终端的尺寸](shutil.md#querying-the-size-of-the-output-terminal)
      * [`get_terminal_size()`](shutil.md#shutil.get_terminal_size)

参见

模块 [`os`](os.md#module-os "os: Miscellaneous operating system interfaces.")

    

~~~
操作系统接口，包括处理比 Python [文件对象](../glossary.md#term-file-object) 更低级别文件的功能。

模块 [`io`](io.md#module-io "io: Core tools for working with streams.")
~~~
    

~~~
Python的内置 I/O 库，包括抽象类和一些具体的类，如文件 I/O 。

内置函数 [`open()`](functions.md#open "open")
~~~
    

~~~