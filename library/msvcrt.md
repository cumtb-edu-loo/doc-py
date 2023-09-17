# `msvcrt` \--- 来自 MS VC++ 运行时的有用例程¶

* * *

这些函数提供了对 Windows 平台上一些有用功能的访问。一些更高级别的模块使用这些函数来构建其服务的 Windows 实现。 例如， [`getpass`](getpass.md#module-getpass "getpass: Portable reading of passwords and retrieval of the userid.") 模块在实现 [`getpass()`](getpass.md#module-getpass "getpass: Portable reading of passwords and retrieval of the userid.") 函数时使用了这些函数。

关于这些函数的更多信息可以在平台 API 文档中找到。

该模块实现了控制台 I/O API 的普通和宽字符变体。普通的 API 只处理ASCII字符，国际化应用受限。应该尽可能地使用宽字符 API 。

在 3.3 版本发生变更: 此模块中过去会引发 [`IOError`](exceptions.md#IOError "IOError") 的操作现在将引发 [`OSError`](exceptions.md#OSError "OSError")。

## 文件操作¶

msvcrt.locking( _fd_ , _mode_ , _nbytes_ )¶

    

~~~
基于文件描述符 _fd_ 从 C 运行时锁定文件的某一部分。 失败时引发 [`OSError`](exceptions.md#OSError "OSError")。 锁定的文件区域从当前文件位置扩展 _nbytes_ 个字节，并可能持续到走出文件末尾。 _mode_ 必须为下面列出的 `LK_*` 常量之一。 一个文件中的多个区域可以被同时锁定，但是不能重叠。 相邻的区域不会被合并；它们必须单独被解锁。

引发一个 [审计事件](sys.md#auditing) `msvcrt.locking`，附带参数 `fd`, `mode`, `nbytes`。

msvcrt.LK_LOCK¶

msvcrt.LK_RLCK¶
~~~
    

~~~
锁定指定的字节数据。 如果字节数据无法被锁定，程序会在 1 秒之后立即重试。 如果在 10 次尝试后字节数据仍无法被锁定，则会引发 [`OSError`](exceptions.md#OSError "OSError")。

msvcrt.LK_NBLCK¶

msvcrt.LK_NBRLCK¶
~~~
    

~~~
锁定指定的字节数据。 如果字节数据无法被锁定，则会引发 [`OSError`](exceptions.md#OSError "OSError")。

msvcrt.LK_UNLCK¶
~~~
    

~~~
解锁指定的字节数据，该对象必须在之前被锁定。

msvcrt.setmode( _fd_ , _flags_ )¶
~~~
    

~~~
设置文件描述符 _fd_ 的行结束符转写模式。 要将其设为文本模式，则 _flags_ 应当为 [`os.O_TEXT`](os.md#os.O_TEXT "os.O_TEXT")；设为二进制模式，则应当为 [`os.O_BINARY`](os.md#os.O_BINARY "os.O_BINARY")。

msvcrt.open_osfhandle( _handle_ , _flags_ )¶
~~~
    

~~~
基于文件句柄 _handle_ 创建一个 C 运行时文件描述符。 _flags_ 形参应当 [`os.O_APPEND`](os.md#os.O_APPEND "os.O_APPEND"), [`os.O_RDONLY`](os.md#os.O_RDONLY "os.O_RDONLY") 和 [`os.O_TEXT`](os.md#os.O_TEXT "os.O_TEXT") 按位 OR 的结果。 返回的文件描述符可以被用作 [`os.fdopen()`](os.md#os.fdopen "os.fdopen") 的形参以创建一个文件对象。

引发一个 [审计事件](sys.md#auditing) `msvcrt.open_osfhandle`，附带参数 `handle`, `flags`。

msvcrt.get_osfhandle( _fd_ )¶
~~~
    

~~~
返回文件描述符 _fd_ 的文件句柄。 如果 _fd_ 不能被识别则会引发 [`OSError`](exceptions.md#OSError "OSError")。

引发一个 [审计事件](sys.md#auditing) `msvcrt.get_osfhandle`，附带参数 `fd`。

## 控制台 I/O¶

msvcrt.kbhit()¶
~~~
    

~~~
Returns a nonzero value if a keypress is waiting to be read. Otherwise, return 0.

msvcrt.getch()¶
~~~
    

~~~
读取一个按键并将结果字符返回为一个字节串。 不会有内容回显到缝制台。 如果还没有任何键被按下此调用将会阻塞，但它将不会等待 `Enter` 被按下。 如果按下的键是一个特殊功能键，此函数将返回 `'\000'` 或 `'\xe0'`；下一次调用将返回键代码。 ``Control`-`C`` 按钮无法使用此函数来读取。

msvcrt.getwch()¶
~~~
    

~~~
`getch()` 的宽字符版本，返回一个 Unicode 值。

msvcrt.getche()¶
~~~
    

~~~
类似于 `getch()`，但按键如果表示一个可打印字符则它将被回显。

msvcrt.getwche()¶
~~~
    

~~~
`getche()` 的宽字符版本，返回一个 Unicode 值。

msvcrt.putch( _char_ )¶
~~~
    

~~~
将字符串 _char_ 打印到终端，不使用缓冲区。

msvcrt.putwch( _unicode_char_ )¶
~~~
    

~~~
`putch()` 的宽字符版本，接受一个 Unicode 值。

msvcrt.ungetch( _char_ )¶
~~~
    

~~~
使得字节串 _char_ 被“推回”终端缓冲区；它将是被 `getch()` 或 `getche()` 读取的下一个字符。

msvcrt.ungetwch( _unicode_char_ )¶
~~~
    

~~~
`ungetch()` 的宽字符版本，接受一个 Unicode 值。

## 其他函数¶

msvcrt.heapmin()¶
~~~
    

~~~
