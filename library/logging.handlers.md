# `logging.handlers` \--- 日志处理程序¶

**源代码:** [Lib/logging/handlers.py](https://github.com/python/cpython/tree/3.12/Lib/logging/handlers.py)

Important

此页面仅包含参考信息。有关教程，请参阅

  * [基础教程](../howto/logging.md#logging-basic-tutorial)

  * [进阶教程](../howto/logging.md#logging-advanced-tutorial)

  * [日志记录操作手册](../howto/logging-cookbook.md#logging-cookbook)

* * *

这个包提供了以下有用的处理程序。 请注意有三个处理程序类 (`StreamHandler`, `FileHandler` 和 `NullHandler`) 实际上是在 [`logging`](logging.md#module-logging "logging: Flexible event logging system for applications.") 模块本身定义的，但其文档与其他处理程序一同记录在此。

## StreamHandler¶

`StreamHandler` 类位于核心 [`logging`](logging.md#module-logging "logging: Flexible event logging system for applications.") 包，它可将日志记录输出发送到数据流例如 _sys.stdout_ , _sys.stderr_ 或任何文件类对象（或者更精确地说，任何支持 `write()` 和 `flush()` 方法的对象）。

_class _logging.StreamHandler( _stream =None_)¶

    

~~~
返回一个新的 `StreamHandler` 类。 如果指定了 _stream_ ，则实例将用它作为日志记录输出；在其他情况下将使用 _sys.stderr_ 。

emit( _record_ )¶
~~~
    

~~~
如果指定了一个格式化器，它会被用来格式化记录。 随后记录会被写入到 `terminator` 之后的流中。 如果存在异常信息，则会使用 [`traceback.print_exception()`](traceback.md#traceback.print_exception "traceback.print_exception") 来格式化并添加到流中。

flush()¶
~~~
    

~~~
通过调用流的 `flush()` 方法来刷新它。 请注意 `close()` 方法是继承自 [`Handler`](logging.md#logging.Handler "logging.Handler") 的所以没有输出，因此有时可能需要显式地调用 `flush()`。

setStream( _stream_ )¶
~~~
    

~~~
将实例的流设为指定值，如果两者不一致的话。 旧的流会在设置新的流之前被刷新。

参数:
~~~
    

~~~
**stream** \-- 处理程序应当使用的流。

返回:
~~~
    

~~~
旧的流，如果流已被改变的话，如果未被改变则为 _None_ 。

在 3.7 版本加入.

terminator¶
~~~
    

~~~
当将已格式化的记录写入到流时被用作终止符的字符串。 默认值为 `'\n'`。

如果你不希望以换行符终止，你可以将处理程序类实例的 `terminator` 属性设为空字符串。

在较早的版本中，终止符被硬编码为 `'\n'`。

在 3.2 版本加入.

## FileHandler¶

`FileHandler` 类位于核心 [`logging`](logging.md#module-logging "logging: Flexible event logging system for applications.") 包，它可将日志记录输出到磁盘文件中。 它从 `StreamHandler` 继承了输出功能。

_class _logging.FileHandler( _filename_ , _mode ='a'_, _encoding =None_, _delay =False_, _errors =None_)¶
~~~
    

~~~
返回一个 `FileHandler` 类的新实例。 将打开指定的文件并将其用作日志记录流。 如果未指定 _mode_ ，则会使用 `'a'`。 如果 _encoding_ 不为 `None`，则会将其用作打开文件的编码格式。 如果 _delay_ 为真值，则文件打开会被推迟至第一次调用 `emit()` 时。 默认情况下，文件会无限增长。 如果指定了 _errors_ ，它会被用于确定编码格式错误的处理方式。

在 3.6 版本发生变更: 除了字符串值，也接受 [`Path`](pathlib.md#pathlib.Path "pathlib.Path") 对象作为 _filename_ 参数。

在 3.9 版本发生变更: 增加了 _errors_ 形参。

close()¶
~~~
    

~~~
关闭文件。

emit( _record_ )¶
~~~
    

~~~
将记录输出到文件。

请注意如果文件因日志记录在退出后终止而被关闭并且文件打开模式为 'w'，则记录将不会被发送 (参见 [bpo-42378](https://bugs.python.org/issue?@action=redirect&bpo=42378))。

## NullHandler¶

在 3.1 版本加入.

`NullHandler` 类位于核心 [`logging`](logging.md#module-logging "logging: Flexible event logging system for applications.") 包，它不执行任何格式化或输出。 它实际上是一个供库开发者使用的‘无操作’处理程序。

_class _logging.NullHandler¶
~~~
    

~~~
返回一个 `NullHandler` 类的新实例。

emit( _record_ )¶
~~~
    

~~~
此方法不执行任何操作。

handle( _record_ )¶
~~~
    

~~~
此方法不执行任何操作。

createLock()¶
~~~
    

~~~
此方法会对锁返回 `None`，因为没有下层 I/O 的访问需要被序列化。

请参阅 [为库配置日志](../howto/logging.md#library-config) 了解有关如何使用 `NullHandler` 的更多信息。

## WatchedFileHandler¶

`WatchedFileHandler` 类位于 `logging.handlers` 模块，这个 `FileHandler` 用于监视它所写入日志记录的文件。 如果文件发生变化，它会被关闭并使用文件名重新打开。

发生文件更改可能是由于使用了执行文件轮换的程序例如 _newsyslog_ 和 _logrotate_ 。 这个处理程序在 Unix/Linux 下使用，它会监视文件来查看自上次发出数据后是否有更改。 （如果文件的设备或 inode 发生变化就认为已被更改。） 如果文件被更改，则会关闭旧文件流，并再打开文件以获得新文件流。

这个处理程序不适合在 Windows 下使用，因为在 Windows 下打开的日志文件无法被移动或改名 —— 日志记录会使用排他的锁来打开文件 —— 因此这样的处理程序是没有必要的。 此外， _ST_INO_ 在 Windows 下不受支持；[`stat()`](os.md#os.stat "os.stat") 将总是为该值返回零。

_class _logging.handlers.WatchedFileHandler( _filename_ , _mode ='a'_, _encoding =None_, _delay =False_, _errors =None_)¶
~~~
    

~~~
返回一个 `WatchedFileHandler` 类的新实例。 将打开指定的文件并将其用作日志记录流。 如果未指定 _mode_ ，则会使用 `'a'`。 如果 _encoding_ 不为 `None`，则会将其用作打开文件的编码格式。 如果 _delay_ 为真值，则文件打开会被推迟至第一次调用 `emit()` 时。 默认情况下，文件会无限增长。 如果提供了 _errors_ ，它会被用于确定编码格式错误的处理方式。

在 3.6 版本发生变更: 除了字符串值，也接受 [`Path`](pathlib.md#pathlib.Path "pathlib.Path") 对象作为 _filename_ 参数。

在 3.9 版本发生变更: 增加了 _errors_ 形参。

reopenIfNeeded()¶
~~~
    

~~~
检查文件是否已更改。 如果已更改，则会刷新并关闭现有流然后重新打开文件，这通常是将记录输出到文件的先导操作。

在 3.6 版本加入.

emit( _record_ )¶
~~~
    

~~~
将记录输出到文件，但如果文件已更改则会先调用 `reopenIfNeeded()` 来重新打开它。

## BaseRotatingHandler¶

`BaseRotatingHandler` 类位于 `logging.handlers` 模块中，它是轮换文件处理程序类 `RotatingFileHandler` 和 `TimedRotatingFileHandler` 的基类。 你不需要实例化此类，但它具有你可能需要重载的属性和方法。

_class _logging.handlers.BaseRotatingHandler( _filename_ , _mode_ , _encoding =None_, _delay =False_, _errors =None_)¶
~~~
    

~~~
类的形参与 `FileHandler` 的相同。 其属性有:

namer¶
~~~
    

~~~
如果此属性被设为一个可调用对象，则 `rotation_filename()` 方法会委托给该可调用对象。 传给该可调用对象的形参与传给 `rotation_filename()` 的相同。

备注

namer 函数会在轮换期间被多次调用，因此它应当尽可能的简单快速。 它还应当对给定的输入每次都返回相同的输出，否则轮换行为可能无法按预期工作。

还有一点值得注意的是当使用命名器来保存文件名中要在轮换中使用的特定属性时必须小心处理。 例如，`RotatingFileHandler` 会要求有一组名称中包含连续整数的日志文件，以便轮换的效果能满足预期，而 `TimedRotatingFileHandler` 会通过确定要删除的最旧文件（根据传递纵使中处理句柄的初始化器的 `backupCount` 形参）来删除旧的日志文件。 为了达成这样的效果，文件名应当是可以使用文件名的日期/时间部分来排序的，而且命名器需要遵循此排序。 （如果想使用不遵循此规则的命名器，则需要在一个重载了 `getFilesToDelete()` 方法的 `TimedRotatingFileHandler` 的子类中使用它以便与自定义的命名规则进行配合。）

在 3.3 版本加入.

rotator¶
~~~
    

~~~
如果此属性被设为一个可调用对象，则 `rotate()` 方法会委托给该可调用对象。 传给该可调用对象的形参与传给 `rotate()` 的相同。

在 3.3 版本加入.

rotation_filename( _default_name_ )¶
~~~
    

~~~
当轮换时修改日志文件的文件名。

提供该属性以便可以提供自定义文件名。

默认实现会调用处理程序的 'namer' 属性，如果它是可调用对象的话，并传给它默认的名称。 如果该属性不是可调用对象 (默认值为 `None`)，则将名称原样返回。

参数:
~~~
    

~~~
**default_name** \-- 日志文件的默认名称。

在 3.3 版本加入.

rotate( _source_ , _dest_ )¶
~~~
    

~~~
当执行轮换时，轮换当前日志。

默认实现会调用处理程序的 'rotator' 属性，如果它是可调用对象的话，并传给它 source 和 dest 参数。 如果该属性不是可调用对象 (默认值为 `None`)，则将源简单地重命名为目标。

参数:
~~~
    

~~~
  * **source** \-- 源文件名。 这通常为基本文件名，例如 'test.log'。

  * **dest** \-- 目标文件名。 这通常是源被轮换后的名称，例如 'test.log.1'。

在 3.3 版本加入.

该属性存在的理由是让你不必进行子类化 —— 你可以使用与 `RotatingFileHandler` 和 `TimedRotatingFileHandler` 的实例相同的可调用对象。 如果 namer 或 rotator 可调用对象引发了异常，将会按照与 `emit()` 调用期间的任何其他异常相同的方式来处理，例如通过处理程序的 `handleError()` 方法。

如果你需要对轮换进程执行更多的修改，你可以重载这些方法。

请参阅 [利用 rotator 和 namer 自定义日志轮换操作](../howto/logging-cookbook.md#cookbook-rotator-namer) 获取具体示例。

## RotatingFileHandler¶

`RotatingFileHandler` 类位于 `logging.handlers` 模块，它支持磁盘日志文件的轮换。

_class _logging.handlers.RotatingFileHandler( _filename_ , _mode ='a'_, _maxBytes =0_, _backupCount =0_, _encoding =None_, _delay =False_, _errors =None_)¶
~~~
    

~~~
返回一个 `RotatingFileHandler` 类的新实例。 将打开指定的文件并将其用作日志记录流。 如果未指定 _mode_ ，则会使用 `'a'`。 如果 _encoding_ 不为 `None`，则会将其用作打开文件的编码格式。 如果 _delay_ 为真值，则文件打开会被推迟至第一次调用 `emit()`。 默认情况下，文件会无限增长。 如果提供了 _errors_ ，它会被用于确定编码格式错误的处理方式。

你可以使用 _maxBytes_ 和 _backupCount_ 值来允许文件以预定的大小执行 _rollover_ 。 当即将超出预定大小时，将关闭旧文件并打开一个新文件用于输出。 只要当前日志文件长度接近 _maxBytes_ 就会发生轮换；但是如果 _maxBytes_ 或 _backupCount_ 两者之一的值为零，就不会发生轮换，因此你通常要设置 _backupCount_ 至少为 1，而 _maxBytes_ 不能为零。 当 _backupCount_ 为非零值时，系统将通过为原文件名添加扩展名 '.1', '.2' 等来保存旧日志文件。 例如，当 _backupCount_ 为 5 而基本文件名为 `app.log` 时，你将得到 `app.log`, `app.log.1`, `app.log.2` 直至 `app.log.5`。 当前被写入的文件总是 `app.log`。 当此文件写满时，它会被关闭并重户名为 `app.log.1`，而如果文件 `app.log.1`, `app.log.2` 等存在，则它们会被分别重命名为 `app.log.2`, `app.log.3` 等等。

在 3.6 版本发生变更: 除了字符串值，也接受 [`Path`](pathlib.md#pathlib.Path "pathlib.Path") 对象作为 _filename_ 参数。

在 3.9 版本发生变更: 增加了 _errors_ 形参。

doRollover()¶
~~~
    

~~~
执行上文所描述的轮换。

emit( _record_ )¶
~~~
    

~~~
将记录输出到文件，以适应上文所描述的轮换。

## TimedRotatingFileHandler¶

`TimedRotatingFileHandler` 类位于 `logging.handlers` 模块，它支持基于特定时间间隔的磁盘日志文件轮换。

_class _logging.handlers.TimedRotatingFileHandler( _filename_ , _when ='h'_, _interval =1_, _backupCount =0_, _encoding =None_, _delay =False_, _utc =False_, _atTime =None_, _errors =None_)¶
~~~
    

~~~
返回一个新的 `TimedRotatingFileHandler` 类实例。 指定的文件会被打开并用作日志记录的流。 对于轮换操作它还会设置文件名前缀。 轮换的发生是基于 _when_ 和 _interval_ 的积。

你可以使用 _when_ 来指定 _interval_ 的类型。 可能的值列表如下。 请注意它们不是大小写敏感的。

值

|

间隔类型

|

如果/如何使用 _atTime_  
  
---|---|---  
  
`'S'`

|

秒

|

忽略  
  
`'M'`

|

分钟

|

忽略  
  
`'H'`

|

小时

|

忽略  
  
`'D'`

|

天

|

忽略  
  
`'W0'-'W6'`

|

工作日(0=星期一)

|

用于计算初始轮换时间  
  
`'midnight'`

|

如果未指定 _atTime_ 则在午夜执行轮换，否则将使用 _atTime_ 。

|

用于计算初始轮换时间  
  
当使用基于星期的轮换时，星期一为 'W0'，星期二为 'W1'，以此类推直至星期日为 'W6'。 在这种情况下，传入的 _interval_ 值不会被使用。

系统将通过为文件名添加扩展名来保存旧日志文件。 扩展名是基于日期和时间的，根据轮换间隔的长短使用 strftime 格式 `%Y-%m-%d_%H-%M-%S` 或是其中有变动的部分。

当首次计算下次轮换的时间时（即当处理程序被创建时），现有日志文件的上次被修改时间或者当前时间会被用来计算下次轮换的发生时间。

如果 _utc_ 参数为真值，将使用 UTC 时间；否则会使用本地时间。

如果 _backupCount_ 不为零，则最多将保留 _backupCount_ 个文件，而如果当轮换发生时创建了更多的文件，则最旧的文件会被删除。 删除逻辑使用间隔时间来确定要删除的文件，因此改变间隔时间可能导致旧文件被继续保留。

如果 _delay_ 为真值，则会将文件打开延迟到首次调用 `emit()` 的时候。

如果 _atTime_ 不为 `None`，则它必须是一个 `datetime.time` 的实例，该实例指定轮换在一天内的发生时间，用于轮换被设为“在午夜”或“在每星期的某一天”之类的情况。 请注意在这些情况下， _atTime_ 值实际上会被用于计算 _初始_ 轮换，而后续轮换将会通过正常的间隔时间计算来得出。

如果指定了 _errors_ ，它会被用来确定编码错误的处理方式。

备注

初始轮换时间的计算是在处理程序被初始化时执行的。 后续轮换时间的计算则仅在轮换发生时执行，而只有当提交输出时轮换才会发生。 如果不记住这一点，你就可能会感到困惑。 例如，如果设置时间间隔为“每分钟”，那并不意味着你总会看到（文件名中）带有间隔一分钟时间的日志文件；如果在应用程序执行期间，日志记录输出的生成频率高于每分钟一次， _那么_ 你可以预期看到间隔一分钟时间的日志文件。 另一方面，如果（假设）日志记录消息每五分钟才输出一次，那么文件时间将会存在对应于没有输出（因而没有轮换）的缺失。

在 3.4 版本发生变更: 添加了 _atTime_ 形参。

在 3.6 版本发生变更: 除了字符串值，也接受 [`Path`](pathlib.md#pathlib.Path "pathlib.Path") 对象作为 _filename_ 参数。

在 3.9 版本发生变更: 增加了 _errors_ 形参。

doRollover()¶
~~~
    

~~~
执行上文所描述的轮换。

emit( _record_ )¶
~~~
    

~~~
将记录输出到文件，以适应上文所描述的轮换。

getFilesToDelete()¶
~~~
    

~~~
返回由应当作为轮转的一部分被删除的文件名组成的列表。 它们是由处理程序写入的最旧的备份日志文件的绝对路径。

## SocketHandler¶

`SocketHandler` 类位于 `logging.handlers` 模块，它会将日志记录输出发送到网络套接字。 基类所使用的是 TCP 套接字。

_class _logging.handlers.SocketHandler( _host_ , _port_ )¶
~~~
    

~~~
返回一个 `SocketHandler` 类的新实例，该实例旨在与使用 _host_ 与 _port_ 给定地址的远程主机进行通信。

在 3.4 版本发生变更: 如果 `port` 指定为 `None`，会使用 `host` 中的值来创建一个 Unix 域套接字 —— 在其他情况下，则会创建一个 TCP 套接字。

close()¶
~~~
    

~~~
关闭套接字。

emit()¶
~~~
    

~~~
对记录的属性字典执行封存并以二进制格式将其写入套接字。 如果套接字存在错误，则静默地丢弃数据包。 如果连接在此之前丢失，则重新建立连接。 要在接收端将记录解封并输出到 [`LogRecord`](logging.md#logging.LogRecord "logging.LogRecord")，请使用 [`makeLogRecord()`](logging.md#logging.makeLogRecord "logging.makeLogRecord") 函数。

handleError()¶
~~~
    

~~~
处理在 `emit()` 期间发生的错误。 最可能的原因是连接丢失。 关闭套接字以便我们能在下次事件时重新尝试。

makeSocket()¶
~~~
    

~~~
这是一个工厂方法，它允许子类定义它们想要的套接字的准确类型。 默认实现会创建一个 TCP 套接字 ([`socket.SOCK_STREAM`](socket.md#socket.SOCK_STREAM "socket.SOCK_STREAM"))。

makePickle( _record_ )¶
~~~
    

~~~
将记录的属性字典封存为带有长度前缀的二进制格式，并将其返回以准备通过套接字进行传输。 此操作在细节上相当于:
~~~
    
    
~~~
data = pickle.dumps(record_attr_dict, 1)
datalen = struct.pack('>L', len(data))
return datalen + data
~~~

请注意封存操作不是绝对安全的。 如果你关心安全问题，你可能会想要重载此方法以实现更安全的机制。 例如，你可以使用 HMAC 对封存对象进行签名然后在接收端验证它们，或者你也可以在接收端禁用全局对象的解封操作。

send( _packet_ )¶

    

~~~
将封存后的字节串 _packet_ 发送到套接字。 所发送字节串的格式与 `makePickle()` 文档中的描述一致。

此函数允许部分发送，这可能会在网络繁忙时发生。

createSocket()¶
~~~
    

~~~
尝试创建一个套接字；失败时将使用指数化回退算法处理。 在失败初次发生时，处理程序将丢弃它正尝试发送的消息。 当后续消息交由同一实例处理时，它将不会尝试连接直到经过一段时间以后。 默认形参设置为初始延迟一秒，如果在延迟之后连接仍然无法建立，处理程序将每次把延迟翻倍直至达到 30 秒的最大值。

此行为由下列处理程序属性控制:

  * `retryStart` (初始延迟，默认为 1.0 秒)。

  * `retryFactor` (倍数，默认为 2.0)。

  * `retryMax` (最大延迟，默认为 30.0 秒)。

这意味着如果远程监听器在处理程序被使用 _之后_ 启动，你可能会丢失消息（因为处理程序在延迟结束之前甚至不会尝试连接，而在延迟期间静默地丢弃消息）。

## DatagramHandler¶

`DatagramHandler` 类位于 `logging.handlers` 模块，它继承自 `SocketHandler`，支持通过 UDP 套接字发送日志记录消息。

_class _logging.handlers.DatagramHandler( _host_ , _port_ )¶
~~~
    

~~~
返回一个 `DatagramHandler` 类的新实例，该实例旨在与使用 _host_ 与 _port_ 给定地址的远程主机进行通信。

备注

由于 UDP 不是流式协议，在该处理句柄与 _host_ 之前不存在持久连接。 因为这个原因，当使用网络套接字时，每当有事件被写入日志时都可能要进行 DNS 查询，这会给系统带来一些延迟。 如果这对你有影响，你可以自己执行查询并使用已查询到的 IP 地址而不是主机名来初始始这个处理句柄。

在 3.4 版本发生变更: 如果 `port` 指定为 `None`，会使用 `host` 中的值来创建一个 Unix 域套接字 —— 在其他情况下，则会创建一个 UDP 套接字。

emit()¶
~~~
    

~~~
对记录的属性字典执行封存并以二进制格式将其写入套接字。 如果套接字存在错误，则静默地丢弃数据包。 要在接收端将记录解封并输出到 [`LogRecord`](logging.md#logging.LogRecord "logging.LogRecord")，请使用 [`makeLogRecord()`](logging.md#logging.makeLogRecord "logging.makeLogRecord") 函数。

makeSocket()¶
~~~
    

~~~
`SocketHandler` 的工厂方法会在此被重载以创建一个 UDP 套接字 ([`socket.SOCK_DGRAM`](socket.md#socket.SOCK_DGRAM "socket.SOCK_DGRAM"))。

send( _s_ )¶
~~~
    

~~~
将封存后的字节串发送到套接字。 所发送字节串的格式与 `SocketHandler.makePickle()` 文档中的描述一致。

## SysLogHandler¶

`SysLogHandler` 类位于 `logging.handlers` 模块，它支持将日志记录消息发送到远程或本地 Unix syslog。

_class _logging.handlers.SysLogHandler( _address =('localhost', SYSLOG_UDP_PORT)_, _facility =LOG_USER_, _socktype =socket.SOCK_DGRAM_)¶
~~~
    

~~~
返回一个 `SysLogHandler` 类的新实例用来与通过 _address_ 以 `(host, port)` 元组形式给出地址的远程 Unix 机器进行通讯。 如果未指定 _address_ ，则使用 `('localhost', 514)`。 该地址会被用于打开套接字。 提供 `(host, port)` 元组的一种替代方式是提供字符串形式的地址，例如 '/dev/log'。 在这种情况下，会使用 Unix 域套接字将消息发送到 syslog。 如果未指定 _facility_ ，则使用 `LOG_USER`。 打开的套接字类型取决于 _socktype_ 参数，该参数的默认值为 [`socket.SOCK_DGRAM`](socket.md#socket.SOCK_DGRAM "socket.SOCK_DGRAM") 即打开一个 UDP 套接字。 要打开一个 TCP 套接字（用来配合较新的 syslog 守护程序例如 rsyslog 使用），请指定值为 [`socket.SOCK_STREAM`](socket.md#socket.SOCK_STREAM "socket.SOCK_STREAM")。

请注意如果你的服务器不是在 UDP 端口 514 上进行侦听，则 `SysLogHandler` 可能无法正常工作。 在这种情况下，请检查你应当为域套接字所使用的地址 —— 它依赖于具体的系统。 例如，在 Linux 上通常为 '/dev/log' 而在 OS/X 上则为 '/var/run/syslog'。 你需要检查你的系统平台并使用适当的地址（如果你的应用程序需要在多个平台上运行则可能需要在运行时进行这样的检查）。 在 Windows 上，你大概必须要使用 UDP 选项。

备注

在 macOS 12.x (Monterey) 上，Apple 修改了其 syslog 守护进程的行为 —— 它不再监听某个域套接字。 因此，你不能再预期 `SysLogHandler` 在此系统上有效。

请参阅 [gh-91070](https://github.com/python/cpython/issues/91070) 了解更多信息。

在 3.2 版本发生变更: 添加了 _socktype_ 。

close()¶
~~~
    

~~~
关闭连接远程主机的套接字。

createSocket()¶
~~~
    

~~~
尝试创建一个套接字，如果它不是一个数据报套接字，则将其连接到另一端。 此方法会在处理句柄初始化期间被调用，但是如果此时另一端还没有监听则它不会被视为出错 —— 此方法将在发出事件时再次被调用，但是如果另一端还没有监听则它也不会被视为出错 --- 如果此时套接字还不存在，此方法将在发出事件时再次被调用。

在 3.11 版本加入.

emit( _record_ )¶
~~~
    

~~~
记录会被格式化，然后发送到 syslog 服务器。 如果存在异常信息，则它 _不会_ 被发送到服务器。

在 3.2.1 版本发生变更: (参见: [bpo-12168](https://bugs.python.org/issue?@action=redirect&bpo=12168)。) 在较早的版本中，发送至 syslog 守护程序的消息总是以一个 NUL 字节结束，因为守护程序的早期版本期望接收一个以 NUL 结束的消息 —— 即使它不包含于对应的规范说明 ([ **RFC 5424**](https://datatracker.ietf.org/doc/html/rfc5424.md))。 这些守护程序的较新版本不再期望接收 NUL 字节，如果其存在则会将其去除，而最新的守护程序（更紧密地遵循 RFC 5424）会将 NUL 字节作为消息的一部分传递出去。

为了在面对所有这些不同守护程序行为时能够更方便地处理 syslog 消息，通过使用类层级属性 `append_nul`，添加 NUL 字节的操作已被作为可配置项。 该属性默认为 `True` (保留现有行为) 但可在 `SysLogHandler` 实例上设为 `False` 以便让实例 _不会_ 添加 NUL 结束符。

在 3.3 版本发生变更: (参见: [bpo-12419](https://bugs.python.org/issue?@action=redirect&bpo=12419)。) 在较早的版本中，没有 "ident" 或 "tag" 前缀工具可以用来标识消息的来源。 现在则可以使用一个类层级属性来设置它，该属性默认为 `""` 表示保留现有行为，但可在 `SysLogHandler` 实例上重载以便让实例不会为所处理的每条消息添加标识。 请注意所提供的标识必须为文本而非字节串，并且它会被原封不动地添加到消息中。

encodePriority( _facility_ , _priority_ )¶
~~~
    

~~~
将功能和优先级编码为一个整数。 你可以传入字符串或者整数 —— 如果传入的是字符串，则会使用内部的映射字典将其转换为整数。

符号 `LOG_` 的值在 `SysLogHandler` 中定义并且是 `sys/syslog.h` 头文件中所定义值的镜像。

**优先级**

名称（字符串）

|

符号值  
  
---|---  
  
`alert`

|

LOG_ALERT  
  
`crit` 或 `critical`

|

LOG_CRIT  
  
`debug`

|

LOG_DEBUG  
  
`emerg` 或 `panic`

|

LOG_EMERG  
  
`err` 或 `error`

|

LOG_ERR  
  
`info`

|

LOG_INFO  
  
`notice`

|

LOG_NOTICE  
  
`warn` 或 `warning`

|

LOG_WARNING  
  
**设备**

名称（字符串）

|

符号值  
  
---|---  
  
`auth`

|

LOG_AUTH  
  
`authpriv`

|

LOG_AUTHPRIV  
  
`cron`

|

LOG_CRON  
  
`daemon`

|

LOG_DAEMON  
  
`ftp`

|

LOG_FTP  
  
`kern`

|

LOG_KERN  
  
`lpr`

|

LOG_LPR  
  
`mail`

|

LOG_MAIL  
  
`news`

|

LOG_NEWS  
  
`syslog`

|

LOG_SYSLOG  
  
`user`

|

LOG_USER  
  
`uucp`

|

LOG_UUCP  
  
`local0`

|

LOG_LOCAL0  
  
`local1`

|

LOG_LOCAL1  
  
`local2`

|

LOG_LOCAL2  
  
`local3`

|

LOG_LOCAL3  
  
`local4`

|

LOG_LOCAL4  
  
`local5`

|

LOG_LOCAL5  
  
`local6`

|

LOG_LOCAL6  
  
`local7`

|

LOG_LOCAL7  
  
mapPriority( _levelname_ )¶
~~~
    

~~~
将日志记录级别名称映射到 syslog 优先级名称。 如果你使用自定义级别，或者如果默认算法不适合你的需要，你可能需要重载此方法。 默认算法将 `DEBUG`, `INFO`, `WARNING`, `ERROR` 和 `CRITICAL` 映射到等价的 syslog 名称，并将所有其他级别名称映射到 'warning'。

## NTEventLogHandler¶

`NTEventLogHandler` 类位于 `logging.handlers` 模块，它支持将日志记录消息发送到本地 Windows NT, Windows 2000 或 Windows XP 事件日志。 在你使用它之前，你需要安装 Mark Hammond 的 Python Win32 扩展。

_class _logging.handlers.NTEventLogHandler( _appname_ , _dllname =None_, _logtype ='Application'_)¶
~~~
    

~~~
返回一个 `NTEventLogHandler` 类的新实例。 _appname_ 用来定义出现在事件日志中的应用名称。 将使用此名称创建适当的注册表条目。 _dllname_ 应当给出要包含在日志中的消息定义的 .dll 或 .exe 的完整限定路径名称（如未指定则会使用 `'win32service.pyd'` —— 此文件随 Win32 扩展安装且包含一些基本的消息定义占位符。 请注意使用这些占位符将使你的事件日志变得很大，因为整个消息源都会被放入日志。 如果你希望有较小的日志，你必须自行传入包含你想要在事件日志中使用的消息定义的 .dll 或 .exe 名称）。 _logtype_ 为 `'Application'`, `'System'` 或 `'Security'` 之一，且默认值为 `'Application'`。

close()¶
~~~
    

~~~
这时，你就可以从注册表中移除作为事件日志条目来源的应用名称。 但是，如果你这样做，你将无法如你所预期的那样在事件日志查看器中看到这些事件 —— 它必须能访问注册表来获取 .dll 名称。 当前版本并不会这样做。

emit( _record_ )¶
~~~
    

~~~
确定消息 ID，事件类别和事件类型，然后将消息记录到 NT 事件日志中。

getEventCategory( _record_ )¶
~~~
    

~~~
返回记录的事件类别。 如果你希望指定你自己的类别就要重载此方法。 此版本将返回 0。

getEventType( _record_ )¶
~~~
    

~~~
返回记录的事件类型。 如果你希望指定你自己的类型就要重载此方法。 此版本将使用处理程序的 typemap 属性来执行映射，该属性在 `__init__()` 被设置为一个字典，其中包含 `DEBUG`, `INFO`, `WARNING`, `ERROR` 和 `CRITICAL` 的映射。 如果你使用你自己的级别，你将需要重载此方法或者在处理程序的 _typemap_ 属性中放置一个合适的字典。

getMessageID( _record_ )¶
~~~
    

~~~
返回记录的消息 ID。 如果你使用你自己的消息，你可以通过将 _msg_ 传给日志记录器作为 ID 而非格式字符串实现此功能。 然后，你可以在这里使用字典查找来获取消息 ID。 此版本将返回 1，是 `win32service.pyd` 中的基本消息 ID。

## SMTPHandler¶

`SMTPHandler` 类位于 `logging.handlers` 模块，它支持将日志记录消息通过 SMTP 发送到一个电子邮件地址。

_class _logging.handlers.SMTPHandler( _mailhost_ , _fromaddr_ , _toaddrs_ , _subject_ , _credentials =None_, _secure =None_, _timeout =1.0_)¶
~~~
    

~~~
返回一个 `SMTPHandler` 类的新实例。 该实例使用电子邮件的发件人、收件人地址和主题行进行初始化。 _toaddrs_ 应当为字符串列表。 要指定一个非标准 SMTP 端口，请使用 (host, port) 元组格式作为 _mailhost_ 参数。 如果你使用一个字符串，则会使用标准 SMTP 端口。 如果你的 SMTP 服务器要求验证，你可以指定一个 (username, password) 元组作为 _credentials_ 参数。

要指定使用安全协议 (TLS)，请传入一个元组作为 _secure_ 参数。 这将仅在提供了验证凭据时才能被使用。 元组应当或是一个空元组，或是一个包含密钥文件名的单值元组，或是一个包含密钥文件和证书文件的 2 值元组。 （此元组会被传给 [`smtplib.SMTP.starttls()`](smtplib.md#smtplib.SMTP.starttls "smtplib.SMTP.starttls") 方法。）

可以使用 _timeout_ 参数为与 SMTP 服务器的通信指定超时限制。

在 3.3 版本加入: 增加了 _timeout_ 参数。

emit( _record_ )¶
~~~
    

~~~
对记录执行格式化并将其发送到指定的地址。

getSubject( _record_ )¶
~~~
    

~~~
如果你想要指定一个基于记录的主题行，请重载此方法。

## MemoryHandler¶

`MemoryHandler` 类位于 `logging.handlers` 模块，它支持在内存中缓冲日志记录，并定期将其刷新到 _target_ 处理程序中。 刷新会在缓冲区满的时候，或是在遇到特定或更高严重程度事件的时候发生。

`MemoryHandler` 是更通用的 `BufferingHandler` 的子类，后者属于抽象类。 它会在内存中缓冲日志记录。 当每条记录被添加到缓冲区时，会通过调用 `shouldFlush()` 来检查缓冲区是否应当刷新。 如果应当刷新，则要使用 `flush()` 来执行刷新。

_class _logging.handlers.BufferingHandler( _capacity_ )¶
~~~
    

~~~
使用指定容量的缓冲区初始化处理程序。 这里， _capacity_ 是指缓冲的日志记录数量。

emit( _record_ )¶
~~~
    

~~~
将记录添加到缓冲区。 如果 `shouldFlush()` 返回真值，则会调用 `flush()` 来处理缓冲区。

flush()¶
~~~
    

~~~
对于 `BufferingHandler` 的实例，刷新意味着将缓冲区设为一个空列表。 此方法可被覆盖以实现更有用的刷新行为。

shouldFlush( _record_ )¶
~~~
    

~~~
如果缓冲区容量已满则返回 `True`。 可以重载此方法以实现自定义的刷新策略。

_class _logging.handlers.MemoryHandler( _capacity_ , _flushLevel =ERROR_, _target =None_, _flushOnClose =True_)¶
~~~
    

~~~
返回一个 `MemoryHandler` 类的新实例。 该实例使用 _capacity_ 指定的缓冲区大小（要缓冲的记录数量）来初始化。 如果 _flushLevel_ 未指定，则使用 `ERROR`。 如果未指定 _target_ ，则需要在此处理程序执行任何实际操作之前使用 `setTarget()` 来设置目标。 如果 _flushOnClose_ 指定为 `False`，则当处理程序被关闭时 _不会_ 刷新缓冲区。 如果未指定或指定为 `True`，则当处理程序被关闭时将会发生之前的缓冲区刷新行为。

在 3.6 版本发生变更: 增加了 _flushOnClose_ 形参。

close()¶
~~~
    

~~~
调用 `flush()`，设置目标为 `None` 并清空缓冲区。

flush()¶
~~~
    

~~~
对于 `MemoryHandler` 的实例，刷新意味着将缓冲的记录发送到目标，如果目标存在的话。 当缓冲的记录被发送到目标时缓冲区也将被清空。 如果你想要不同的行为请重载此方法。

setTarget( _target_ )¶
~~~
    

~~~
设置此处理程序的目标处理程序。

shouldFlush( _record_ )¶
~~~
    

~~~
检测缓冲区是否已满或是有记录为 _flushLevel_ 或更高级别。

## HTTPHandler¶

`HTTPHandler` 类位于 `logging.handlers` 模块，它支持使用 `GET` 或 `POST` 语义将日志记录消息发送到 Web 服务器。

_class _logging.handlers.HTTPHandler( _host_ , _url_ , _method ='GET'_, _secure =False_, _credentials =None_, _context =None_)¶
~~~
    

~~~
返回一个 `HTTPHandler` 类的新实例。 _host_ 可以为 `host:port` 的形式，如果你需要使用指定端口号的话。 如果没有指定 _method_ ，则会使用 `GET`。 如果 _secure_ 为真值，则将使用 HTTPS 连接。 _context_ 形参可以设为一个 [`ssl.SSLContext`](ssl.md#ssl.SSLContext "ssl.SSLContext") 实例以配置用于 HTTPS 连接的 SSL 设置。 如果指定了 _credentials_ ，它应当为包含 userid 和 password 的二元组，该元组将被放入使用 Basic 验证的 HTTP 'Authorization' 标头中。 如果你指定了 credentials，你还应当指定 secure=True 这样你的 userid 和 password 就不会以明文在线路上传输。

在 3.5 版本发生变更: 增加了 _context_ 形参。

mapLogRecord( _record_ )¶
~~~
    

~~~
基于 `record` 提供一个字典，它将被执行 URL 编码并发送至 Web 服务器。 默认实现仅返回 `record.__dict__`。 在只需将 [`LogRecord`](logging.md#logging.LogRecord "logging.LogRecord") 的某个子集发送至 Web 服务器，或者需要对发送至服务器的内容进行更多定制时可以重载此方法。

emit( _record_ )¶
~~~
    

~~~
将记录以 URL 编码字典的形式发送至 Web 服务器。 `mapLogRecord()` 方法会被用来将要发送的记录转换为字典。

备注

由于记录发送至 Web 服务器所需的预处理与通用的格式化操作不同，使用 [`setFormatter()`](logging.md#logging.Handler.setFormatter "logging.Handler.setFormatter") 来指定一个 [`Formatter`](logging.md#logging.Formatter "logging.Formatter") 用于 `HTTPHandler` 是没有效果的。 此处理程序不会调用 [`format()`](logging.md#logging.Handler.format "logging.Handler.format")，而是调用 `mapLogRecord()` 然后再调用 [`urllib.parse.urlencode()`](urllib.parse.md#urllib.parse.urlencode "urllib.parse.urlencode") 来以适合发送至 Web 服务器的形式对字典进行编码。

## QueueHandler¶

在 3.2 版本加入.

`QueueHandler` 类位于 `logging.handlers` 模块，它支持将日志记录消息发送到一个队列，例如在 [`queue`](queue.md#module-queue "queue: A synchronized queue class.") 或 [`multiprocessing`](multiprocessing.md#module-multiprocessing "multiprocessing: Process-based parallelism.") 模块中实现的队列。

配合 `QueueListener` 类使用，`QueueHandler` 可被用来使处理程序在与执行日志记录的线程不同的线程上完成工作。 这对 Web 应用程序以及其他服务于客户端的线程需要尽可能快地响应的服务应用程序来说很重要，任何潜在的慢速操作（例如通过 `SMTPHandler` 发送邮件）都要在单独的线程上完成。

_class _logging.handlers.QueueHandler( _queue_ )¶
~~~
    

~~~
返回一个 `QueueHandler` 类的新实例。 该实例使用队列来初始化以向其发送消息。 _queue_ 可以为任何队列类对象；它由 `enqueue()` 方法来使用，该方法需要知道如何向其发送消息。 队列 _不要求_ 具有任务跟踪 API，这意味着你可以为 _queue_ 使用 [`SimpleQueue`](queue.md#queue.SimpleQueue "queue.SimpleQueue") 实例。

备注

如果你在使用 [`multiprocessing`](multiprocessing.md#module-multiprocessing "multiprocessing: Process-based parallelism.")，则你应当避免使用 [`SimpleQueue`](queue.md#queue.SimpleQueue "queue.SimpleQueue") 而要改用 [`multiprocessing.Queue`](multiprocessing.md#multiprocessing.Queue "multiprocessing.Queue")。

emit( _record_ )¶
~~~
    

~~~
将准备 LogRecord 的结果排入队列。 如果发生了异常（例如由于有界队列已满），则会调用 [`handleError()`](logging.md#logging.Handler.handleError "logging.Handler.handleError") 方法来处理错误。 这可能导致记录被静默地丢弃 (当 `logging.raiseExceptions` 为 `False` 时) 或者消息被打印到 `sys.stderr` (当 `logging.raiseExceptions` 为 `True` 时)。

prepare( _record_ )¶
~~~
    

~~~
准备用于队列的记录。 此方法返回的对象会被排入队列。

该基本实现会对记录进行格式化以合并消息、参数、异常和栈信息，如果它们存在的话。 它还会从记录中原地移除不可 pickle 的条目。 具体来说，它会用合并后的消息（通过调用处理句柄的 [`format()`](functions.md#format "format") 方法获得）覆盖记录的 `msg` 和 `message` 属性，并将 `args`, `exc_info` 和 `exc_text` 属性设置为 `None`。

如果你想要将记录转换为 dict 或 JSON 字符串，或者发送记录被修改后的副本而让初始记录保持原样，则你可能会想要重载此方法。

备注

该基本实现会使用这些参数对消息进行格式化，将 `message` 和 `msg` 属性设置为已格式化的消息并将 `args` 和 `exc_text` 属性设置为 `None` 以允许 pickle 操作并防止更多的格式化尝试。 这意味着 `QueueListener` 一方的处理句柄将没有自定义格式化所需的信息，例如异常信息等。 你可能会想要子类化 `QueueHandler` 并重载此方法以便避免将 `exc_text` 设置为 `None`。 请注意对 `message` / `msg` / `args` 的改变与确保记录可以 pickle 是相关联的，根据你的 `args` 是否可以 pickle 你将可能或不可能避免这样做。 （请注意你可能必须不仅要考虑你自己的代码还要考虑你所使用的任何库中的代码。）

enqueue( _record_ )¶
~~~
    

~~~
使用 `put_nowait()` 将记录排入队列；如果你想要使用阻塞行为，或超时设置，或自定义的队列实现，则你可能会想要重载此方法。

listener¶
~~~
    

~~~
当通过使用 [`dictConfig()`](logging.config.md#logging.config.dictConfig "logging.config.dictConfig") 的配置创建时，该属性将包含一个 `QueueListener` 实例供此处理句柄使用。 在其他情况下，它将为 `None`。

在 3.12 版本加入.

## QueueListener¶

在 3.2 版本加入.

`QueueListener` 类位于 `logging.handlers` 模块，它支持从一个队列接收日志记录消息，例如在 [`queue`](queue.md#module-queue "queue: A synchronized queue class.") 或 [`multiprocessing`](multiprocessing.md#module-multiprocessing "multiprocessing: Process-based parallelism.") 模块中实现的队列。 消息是在内部线程中从队列接收并在同一线程上传递到一个或多个处理程序进行处理的。 尽管 `QueueListener` 本身并不是一个处理程序，但由于它要与 `QueueHandler` 配合工作，因此也在此处介绍。

配合 `QueueHandler` 类使用，`QueueListener` 可被用来使处理程序在与执行日志记录的线程不同的线程上完成工作。 这对 Web 应用程序以及其他服务于客户端的线程需要尽可能快地响应的服务应用程序来说很重要，任何潜在的慢速动作（例如通过 `SMTPHandler` 发送邮件）都要在单独的线程上完成。

_class _logging.handlers.QueueListener( _queue_ , _* handlers_, _respect_handler_level =False_)¶
~~~
    

~~~
返回一个 `QueueListener` 类的新实例。 该实例初始化时要传入一个队列以向其发送消息，还要传入一个处理程序列表用来处理放置在队列中的条目。 队列可以是任何队列类对象；它会被原样传给 `dequeue()` 方法，该方法需要知道如何从其获取消息。 队列 _不要求_ 具有任务跟踪 API（但如提供则会使用它），这意味着你可以为 _queue_ 使用 [`SimpleQueue`](queue.md#queue.SimpleQueue "queue.SimpleQueue") 实例。

备注

如果你在使用 [`multiprocessing`](multiprocessing.md#module-multiprocessing "multiprocessing: Process-based parallelism.")，则你应当避免使用 [`SimpleQueue`](queue.md#queue.SimpleQueue "queue.SimpleQueue") 而要改用 [`multiprocessing.Queue`](multiprocessing.md#multiprocessing.Queue "multiprocessing.Queue")。

如果 `respect_handler_level` 为 `True`，则在决定是否将消息传递给处理程序之前会遵循处理程序的级别（与消息的级别进行比较）；在其他情况下，其行为与之前的 Python 版本一致 —— 总是将每条消息传递给每个处理程序。

在 3.5 版本发生变更: 增加了 `respect_handler_level` 参数。

dequeue( _block_ )¶
~~~
    

~~~
从队列移出一条记录并将其返回，可以选择阻塞。

基本实现使用 `get()`。 如果你想要使用超时设置或自定义的队列实现，则你可能会想要重载此方法。

prepare( _record_ )¶
~~~
    

~~~
准备一条要处理的记录。

该实现只是返回传入的记录。 如果你想要对记录执行任何自定义的 marshal 操作或在将其传给处理程序之前进行调整，则你可能会想要重载此方法。

handle( _record_ )¶
~~~
    

~~~
处理一条记录。

此方法简单地循环遍历处理程序，向它们提供要处理的记录。 实际传给处理程序的对象就是从 `prepare()` 返回的对象。

start()¶
~~~
    

~~~
启动监听器。

此方法启动一个后台线程来监视 LogRecords 队列以进行处理。

stop()¶
~~~
    

~~~
停止监听器。

此方法要求线程终止，然后等待它完成终止操作。 请注意在你的应用程序退出之前如果你没有调用此方法，则可能会有一些记录在留在队列中，它们将不会被处理。

enqueue_sentinel()¶
~~~
    

~~~
将一个标记写入队列以通知监听器退出。 此实现会使用 `put_nowait()`。 如果你想要使得超时设置或自定义的队列实现，则你可能会想要重载此方法。

在 3.3 版本加入.

参见

模块 [`logging`](logging.md#module-logging "logging: Flexible event logging system for applications.")
~~~
    

~~~
日志记录模块的 API 参考。

[`logging.config`](logging.config.md#module-logging.config "logging.config: Configuration of the logging module.") 模块
~~~
    

~~~
