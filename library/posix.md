# `posix` \--- 最常见的 POSIX 系统调用¶

* * *

此模块提供了对基于 C 标准和 POSIX 标准（一种稍加修改的 Unix 接口）进行标准化的系统功能的访问。

**请勿直接导入此模块。** 而应导入 [`os`](os.md#module-os "os: Miscellaneous operating system interfaces.") 模块，它提供了此接口的 _可移植_ 版本。 在 Unix 上，[`os`](os.md#module-os "os: Miscellaneous operating system interfaces.") 模块提供了 `posix` 接口的一个超集。 在非 Unix 操作系统上 `posix` 模块将不可用，但会通过 [`os`](os.md#module-os "os: Miscellaneous operating system interfaces.") 接口提供它的一个可用子集。 一旦导入了 [`os`](os.md#module-os "os: Miscellaneous operating system interfaces.")，用它替代 `posix` 时就 _没有_ 性能惩罚。 此外，[`os`](os.md#module-os "os: Miscellaneous operating system interfaces.") 还提供了一些附加功能，例如在 `os.environ` 中的某个条目被修改时会自动调用 [`putenv()`](os.md#os.putenv "os.putenv")。

错误将作为异常被报告；对于类型错误会给出普通异常，而系统调用所报告的异常则会引发 [`OSError`](exceptions.md#OSError "OSError")。

## 大文件支持¶

某些操作系统（包括 AIX 和 Solaris）可对 int 和 long 为 32 位值的 C 编程模型提供大于 2 GiB 文件的支持。 这在通常情况下是以将相关数据的大小和偏移量类型定义为 64 位值的方式来实现的。 这样的文件有时被称为 _大文件_ 。

Python 中的大文件支持会在 `off_t` 的大小超过 long 且 long long 的大小至少与 `off_t` 一样时被启用。 要启用此模式可能必须在启用特定编译旗标的情况下配置和编译 Python。 例如，在 Solaris 2.6 和 2.7 中你需要执行这样的操作:

    
    
~~~
CFLAGS="`getconf LFS_CFLAGS`" OPT="-g -O2 $CFLAGS" \
        ./configure
~~~

在支持大文件的 Linux 系统中，可以这样做:

    
    
~~~
CFLAGS='-D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64' OPT="-g -O2 $CFLAGS" \
        ./configure
~~~

## 重要的模块内容¶

除了 [`os`](os.md#module-os "os: Miscellaneous operating system interfaces.") 模块文档已说明的许多函数，`posix` 还定义了下列数据项:

posix.environ¶

    

~~~
