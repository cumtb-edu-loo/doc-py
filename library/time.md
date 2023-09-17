# `time` \--- 时间的访问和转换¶

* * *

该模块提供了各种与时间相关的函数。相关功能还可以参阅 [`datetime`](datetime.md#module-datetime "datetime: Basic date and time types.") 和 [`calendar`](calendar.md#module-calendar "calendar: Functions for working with calendars, including some emulation of the Unix cal program.") 模块。

尽管所有平台皆可使用此模块，但模块内的函数并非所有平台都可用。此模块中定义的大多数函数的实现都是调用其所在平台的C语言库的同名函数。因为这些函数的语义可能因平台而异，所以使用时最好查阅对应平台的相关文档。

下面是一些术语和惯例的解释.

  * _epoch_ 是起始的时间点，即 `time.gmtime(0)` 的返回值。 这在所有平台上都是 1970-01-01, 00:00:00 (UTC)。

  * 术语 _纪元秒数_ 是指自 epoch （纪元）时间点以来经过的总秒数，通常不包括 [闰秒](https://en.wikipedia.org/wiki/Leap_second)。 在所有符合 POSIX 标准的平台上，闰秒都不会记录在总秒数中。

  * 此模块中的函数可能无法处理 epoch 之前或遥远未来的日期和时间。 “遥远未来”的分界点是由 C 库确定的；对于 32 位系统，它通常是在 2038 年。

  * 函数 `strptime()` 在接收到 `%y` 格式代码时可以解析使用 2 位数表示的年份。当解析 2 位数年份时，函数会按照 POSIX 和 ISO C 标准进行年份转换：数值 69--99 被映射为 1969--1999；数值 0--68 被映射为 2000--2068。

  * UTC是协调世界时（Coordinated Universal Time）的缩写。它以前也被称为格林威治标准时间（GMT）。使用UTC而不是CUT作为缩写是英语与法语（Temps Universel Coordonné）之间妥协的结果，不是什么低级错误。

  * DST是夏令时（Daylight Saving Time）的缩写，在一年的某一段时间中将当地时间调整（通常）一小时。 DST的规则非常神奇（由当地法律确定），并且每年的起止时间都不同。C语言库中有一个表格，记录了各地的夏令时规则（实际上，为了灵活性，C语言库通常是从某个系统文件中读取这张表）。从这个角度而言，这张表是夏令时规则的唯一权威真理。

  * 由于平台限制，各种实时函数的精度可能低于其值或参数所要求（或给定）的精度。例如，在大多数Unix系统上，时钟频率仅为每秒50或100次。

  * 另一方面， `time()` 和 `sleep()` 的精度优于它们的Unix等价物：时间表示为浮点数，`time()` 返回最准确的时间 （使用Unix `gettimeofday()` 如果可用），并且 `sleep()` 将接受非零分数的时间（Unix `select()` 用于实现此功能，如果可用）。

  * 时间值由 `gmtime()`，`localtime()` 和 `strptime()` 返回，并被 `asctime()`， `mktime()` 和 `strftime()` 接受，是一个 9 个整数的序列。 `gmtime()`， `localtime()` 和 `strptime()` 的返回值还提供各个字段的属性名称。

请参阅 `struct_time` 以获取这些对象的描述。

在 3.3 版本发生变更: 在平台支持相应的 `struct tm` 成员时，`struct_time` 类型被扩展提供 `tm_gmtoff` 和 `tm_zone` 属性。

在 3.6 版本发生变更: `struct_time` 的属性 `tm_gmtoff` 和 `tm_zone` 现在可在所有平台上使用。

  * 使用以下函数在时间表示之间进行转换：

从

|

到

|

使用  
  
---|---|---  
  
自纪元以来的秒数

|

UTC 的 `struct_time`

|

`gmtime()`  
  
自纪元以来的秒数

|

本地时间的 `struct_time`

|

`localtime()`  
  
UTC 的 `struct_time`

|

自纪元以来的秒数

|

[`calendar.timegm()`](calendar.md#calendar.timegm "calendar.timegm")  
  
本地时间的 `struct_time`

|

自纪元以来的秒数

|

`mktime()`  
  

## 函数¶

time.asctime([ _t_ ])¶

    

~~~
转换由 `gmtime()` 或 `localtime()` 所返回的表示时间的元组或 `struct_time` 为以下形式的字符串: `'Sun Jun 20 23:21:05 1993'`。 日期字段的长度为两个字符，如果日期只有一个数字则会以零填充，例如: `'Wed Jun 9 04:26:40 1993'`。

如果未提供 _t_ ，则会使用 `localtime()` 所返回的当前时间。 `asctime()` 不会使用区域设置信息。

备注

与同名的C函数不同， `asctime()` 不添加尾随换行符。

time.pthread_getcpuclockid( _thread_id_ )¶
~~~
    

~~~
返回指定的 _thread_id_ 的特定于线程的CPU时间时钟的 _clk_id_ 。

使用 [`threading.Thread`](threading.md#threading.Thread "threading.Thread") 对象的 [`threading.get_ident()`](threading.md#threading.get_ident "threading.get_ident") 或 [`ident`](threading.md#threading.Thread.ident "threading.Thread.ident") 属性为 _thread_id_ 获取合适的值。

警告

传递无效的或过期的 _thread_id_ 可能会导致未定义的行为，例如段错误。

[可用性](intro.md#availability): Unix

请参阅 _[pthread_getcpuclockid(3)](https://manpages.debian.org/pthread_getcpuclockid\(3\))_ 的手册页面了解更多信息。

在 3.7 版本加入.

time.clock_getres( _clk_id_ )¶
~~~
    

~~~
返回指定时钟 _clk_id_ 的分辨率（精度）。有关 _clk_id_ 的可接受值列表，请参阅 Clock ID 常量 。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

time.clock_gettime( _clk_id_ ) -> [float](functions.md#float "float")¶
~~~
    

~~~
返回指定 _clk_id_ 时钟的时间。有关 _clk_id_ 的可接受值列表，请参阅 Clock ID 常量 。

使用 `clock_gettime_ns()` 以避免 [`float`](functions.md#float "float") 类型导致的精度损失。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

time.clock_gettime_ns( _clk_id_ ) -> [int](functions.md#int "int")¶
~~~
    

~~~
与 `clock_gettime()` 相似，但返回时间为纳秒。

[可用性](intro.md#availability): Unix。

在 3.7 版本加入.

time.clock_settime( _clk_id_ , _time : [float](functions.md#float "float")_)¶
~~~
    

~~~
设置指定 _clk_id_ 时钟的时间。 目前， `CLOCK_REALTIME` 是 _clk_id_ 唯一可接受的值。

使用 `clock_settime_ns()` 以避免 [`float`](functions.md#float "float") 类型导致的精度损失。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

time.clock_settime_ns( _clk_id_ , _time : [int](functions.md#int "int")_)¶
~~~
    

~~~
与 `clock_settime()` 相似，但设置时间为纳秒。

[可用性](intro.md#availability): Unix。

在 3.7 版本加入.

time.ctime([ _secs_ ])¶
~~~
    

~~~
将以距离 epoch 的秒数表示的时间转换为以下形式的字符串: `'Sun Jun 20 23:21:05 1993'` 代表本地时间。 日期字段的长度为两个字符且如果日期只有一位数字则会以空格填充，例如: `'Wed Jun 9 04:26:40 1993'`。

如果 _secs_ 未提供或为 [`None`](constants.md#None "None")，则使用 `time()` 所返回的当前时间。 `ctime(secs)` 等价于 `asctime(localtime(secs))`。 `ctime()` 不会使用区域设置信息。

time.get_clock_info( _name_ )¶
~~~
    

~~~
获取有关指定时钟的信息作为命名空间对象。 支持的时钟名称和读取其值的相应函数是：

  * `'monotonic'`: `time.monotonic()`

  * `'perf_counter'`: `time.perf_counter()`

  * `'process_time'`: `time.process_time()`

  * `'thread_time'`: `time.thread_time()`

  * `'time'`: `time.time()`

结果具有以下属性：

  * _adjustable_ ： 如果时钟可以自动更改（例如通过NTP守护程序）或由系统管理员手动更改，则为 `True` ，否则为 `False` 。

  * _implementation_ ： 用于获取时钟值的基础C函数的名称。有关可能的值，请参阅 Clock ID 常量 。

  * _monotonic_ ：如果时钟不能倒退，则为 `True` ，否则为 `False` 。

  * _resolution_ ： 以秒为单位的时钟分辨率（ [`float`](functions.md#float "float") ）

在 3.3 版本加入.

time.gmtime([ _secs_ ])¶
~~~
    

~~~
将以自 epoch 开始的秒数表示的时间转换为 UTC 的 `struct_time`，其中 dst 旗标始终为零。 如果未提供 _secs_ 或为 [`None`](constants.md#None "None")，则使用 `time()` 所返回的当前时间。 一秒以内的小数将被忽略。 有关 `struct_time` 对象的说明请参见上文。 有关此函数的逆操作请参阅 [`calendar.timegm()`](calendar.md#calendar.timegm "calendar.timegm")。

time.localtime([ _secs_ ])¶
~~~
    

~~~
与 `gmtime()` 相似但转换为当地时间。如果未提供 _secs_ 或为 [`None`](constants.md#None "None") ，则使用由 `time()` 返回的当前时间。当 DST 适用于给定时间时，dst标志设置为 `1` 。

`localtime()` 可能会引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError") ，如果时间戳超出平台 C `localtime()` 或 `gmtime()` 函数支持的范围，并会在 `localtime()` 或 `gmtime()` 失败时引发 [`OSError`](exceptions.md#OSError "OSError") 。这通常被限制在1970至2038年之间。

time.mktime( _t_ )¶
~~~
    

~~~
这是 `localtime()` 的反函数。它的参数是 `struct_time` 或者完整的 9 元组（因为需要 dst 标志；如果它是未知的则使用 `-1` 作为dst标志），它表示 _local_ 的时间，而不是 UTC 。它返回一个浮点数，以便与 `time()` 兼容。如果输入值不能表示为有效时间，则 [`OverflowError`](exceptions.md#OverflowError "OverflowError") 或 [`ValueError`](exceptions.md#ValueError "ValueError") 将被引发（这取决于Python或底层C库是否捕获到无效值）。它可以生成时间的最早日期取决于平台。

time.monotonic() -> [float](functions.md#float "float")¶
~~~
    

~~~
（以小数表示的秒为单位）返回一个单调时钟的值，即不能倒退的时钟。 该时钟不受系统时钟更新的影响。 返回值的参考点未被定义，因此只有两次调用之间的差值才是有效的。

使用 `monotonic_ns()` 以避免 [`float`](functions.md#float "float") 类型导致的精度损失。

在 3.3 版本加入.

在 3.5 版本发生变更: 该功能现在始终可用且始终在系统范围内。

在 3.10 版本发生变更: 在 macOS 上，现在这个函数作用于全系统。

time.monotonic_ns() -> [int](functions.md#int "int")¶
~~~
    

~~~
与 `monotonic()` 相似，但是返回时间为纳秒数。

在 3.7 版本加入.

time.perf_counter() -> [float](functions.md#float "float")¶
~~~
    

~~~
（以小数表示的秒为单位）返回一个性能计数器的值，即用于测量较短持续时间的具有最高有效精度的时钟。 它会包括睡眠状态所消耗的时间并且作用于全系统范围。 返回值的参考点未被定义，因此只有两次调用之间的差值才是有效的。

使用 `perf_counter_ns()` 以避免 [`float`](functions.md#float "float") 类型导致的精度损失。

在 3.3 版本加入.

在 3.10 版本发生变更: 在 Windows 上，现在这个函数作用于全系统。

time.perf_counter_ns() -> [int](functions.md#int "int")¶
~~~
    

~~~
与 `perf_counter()` 相似，但是返回时间为纳秒。

在 3.7 版本加入.

time.process_time() -> [float](functions.md#float "float")¶
~~~
    

~~~
（以小数表示的秒为单位）返回当前进程的系统和用户 CPU 时间的总计值。 它不包括睡眠状态所消耗的时间。 根据定义它只作用于进程范围。 返回值的参考点未被定义，因此只有两次调用之间的差值才是有效的。

使用 `process_time_ns()` 以避免 [`float`](functions.md#float "float") 类型导致的精度损失。

在 3.3 版本加入.

time.process_time_ns() -> [int](functions.md#int "int")¶
~~~
    

~~~
与 `process_time()` 相似，但是返回时间为纳秒。

在 3.7 版本加入.

time.sleep( _secs_ )¶
~~~
    

~~~
调用方线程暂停执行给定的秒数。 该参数可以为浮点数以指定一个更精确的休眠时间。

如果休眠被信号打断并且信号处理句柄未引发异常，休眠将基于重新计算的时延重新开始。If the sleep is interrupted by a signal and no exception is raised by the signal handler, the sleep is restarted with a recomputed timeout.

暂停时间有可能比请求的要长出一段不确定的时间，因为会受系统中的其他活动排期影响。

在 Windows 上，如果 _secs_ 为零，线程会将其时间片的剩余部分让渡给任何其他准备要运行的线程。 如果没有准备要运行的其他线程，该函数将立即返回，而线程将继续执行。 在 Windows 8.1 及更新版本中的实现使用了提供 100 纳秒分辨率的 [高分辨率定时器](https://docs.microsoft.com/en-us/windows-hardware/drivers/kernel/high-resolution-timers)。 如果 _secs_ 为零，则会使用 `Sleep(0)`。

Unix 实现:

  * 如果可能则使用 `clock_nanosleep()` (精度: 1 纳秒);

  * 或者如果可能则使用 `nanosleep()` (精度: 1 纳秒);

  * 或者使用 `select()` (精度: 1 微秒).

Raises an [auditing event](sys.md#auditing) `time.sleep` with argument `secs`.

在 3.11 版本发生变更: 在 Unix 上，现在将在可能的情况下使用 `clock_nanosleep()` 和 `nanosleep()` 函数。 在 Windows 上，现在将使用可等待的计时器。

在 3.5 版本发生变更: 现在，即使该睡眠过程被信号中断，该函数也会保证调用它的线程至少会睡眠 _secs_ 秒。信号处理例程抛出异常的情况除外。（欲了解我们做出这次改变的原因，请参见 [**PEP 475**](https://peps.python.org/pep-0475/) ）

在 3.13 版本发生变更: Raises an auditing event.

time.strftime( _format_ [, _t_ ])¶
~~~
    

~~~
转换一个元组或 `struct_time` 表示的由 `gmtime()` 或 `localtime()` 返回的时间到由 _format_ 参数指定的字符串。如果未提供 _t_ ，则使用由 `localtime()` 返回的当前时间。 _format_ 必须是一个字符串。如果 _t_ 中的任何字段超出允许范围，则引发 [`ValueError`](exceptions.md#ValueError "ValueError") 。

0是时间元组中任何位置的合法参数；如果它通常是非法的，则该值被强制改为正确的值。

以下指令可以嵌入 _format_ 字符串中。它们显示时没有可选的字段宽度和精度规范，并被 `strftime()` 结果中的指示字符替换：

指令

|

含意

|

备注  
  
---|---|---  
  
`%a`

|

本地化的缩写星期中每日的名称。

|  
  
`%A`

|

本地化的星期中每日的完整名称。

|  
  
`%b`

|

本地化的月缩写名称。

|  
  
`%B`

|

本地化的月完整名称。

|  
  
`%c`

|

本地化的适当日期和时间表示。

|  
  
`%d`

|

十进制数 [01,31] 表示的月中日。

|  
  
`%H`

|

十进制数 [00,23] 表示的小时（24小时制）。

|  
  
`%I`

|

十进制数 [01,12] 表示的小时（12小时制）。

|  
  
`%j`

|

十进制数 [001,366] 表示的年中日。

|  
  
`%m`

|

十进制数 [01,12] 表示的月。

|  
  
`%M`

|

十进制数 [00,59] 表示的分钟。

|  
  
`%p`

|

本地化的 AM 或 PM 。

|

(1)  
  
`%S`

|

十进制数 [00,61] 表示的秒。

|

(2)  
  
`%U`

|

十进制数 [00,53] 表示的一年中的周数（星期日作为一周的第一天）。 在第一个星期日之前的新年中的所有日子都被认为是在第 0 周。

|

(3)  
  
`%w`

|

十进制数 [0(星期日),6] 表示的周中日。

|  
  
`%W`

|

十进制数 [00,53] 表示的一年中的周数（星期一作为一周的第一天）。 在第一个星期一之前的新年中的所有日子被认为是在第 0 周。

|

(3)  
  
`%x`

|

本地化的适当日期表示。

|  
  
`%X`

|

本地化的适当时间表示。

|  
  
`%y`

|

十进制数 [00,99] 表示的没有世纪的年份。

|  
  
`%Y`

|

十进制数表示的带世纪的年份。

|  
  
`%z`

|

时区偏移以格式 +HHMM 或 -HHMM 形式的 UTC/GMT 的正或负时差指示，其中H表示十进制小时数字，M表示小数分钟数字 [-23:59, +23:59] 。[1]

|  
  
`%Z`

|

时区名称（如果不存在时区，则不包含字符）。已弃用。 [1]

|  
  
`%%`

|

字面的 `'%'` 字符。

|  
  
注释：

  1. 当与 `strptime()` 函数一起使用时，如果使用 `%I` 指令来解析小时， `%p` 指令只影响输出小时字段。

  2. 范围真的是 `0` 到 `61` ；值 `60` 在表示 [leap seconds](https://en.wikipedia.org/wiki/Leap_second) 的时间戳中有效，并且由于历史原因支持值 `61` 。

  3. 当与 `strptime()` 函数一起使用时， `%U` 和 `%W` 仅用于指定星期几和年份的计算。

下面是一个示例，一个与 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) Internet电子邮件标准以兼容的日期格式。 [1]
~~~
    
    
~~~shell
>>> from time import gmtime, strftime
>>> strftime("%a, %d %b %Y %H:%M:%S +0000", gmtime())
'Thu, 28 Jun 2001 14:17:15 +0000'
~~~

某些平台可能支持其他指令，但只有此处列出的指令具有 ANSI C 标准化的含义。要查看平台支持的完整格式代码集，请参阅 _[strftime(3)](https://manpages.debian.org/strftime\(3\))_ 文档。

在某些平台上，可选的字段宽度和精度规范可以按照以下顺序紧跟在指令的初始 `'%'` 之后；这也不可移植。字段宽度通常为2，除了 `%j` ，它是3。

time.strptime( _string_ [, _format_ ])¶

    

~~~
根据格式解析表示时间的字符串。 返回值为一个被 `gmtime()` 或 `localtime()` 返回的 `struct_time` 。

_format_ 参数使用与 `strftime()` 相同的指令。 它默认为匹配 `ctime()` 所返回的格式 `"%a %b %d %H:%M:%S %Y"`` 。 如果 _string_ 不能根据 _format_ 来解析，或者解析后它有多余的数据，则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 当无法推断出更准确的值时，用于填充任何缺失数据的默认值是 `(1900, 1, 1, 0, 0, 0, 0, 1, -1)` 。 _string_ 和 _format_ 都必须为字符串。

例如:
~~~
    
    
~~~shell
>>> import time
>>> time.strptime("30 Nov 00", "%d %b %y")   
time.struct_time(tm_year=2000, tm_mon=11, tm_mday=30, tm_hour=0, tm_min=0,
                 tm_sec=0, tm_wday=3, tm_yday=335, tm_isdst=-1)
~~~

支持 `%Z` 指令是基于 `tzname` 中包含的值以及 `daylight` 是否为真。因此，它是特定于平台的，除了识别始终已知的 UTC 和 GMT （并且被认为是非夏令时时区）。

仅支持文档中指定的指令。因为每个平台都实现了 `strftime()` ，它有时会提供比列出的指令更多的指令。但是 `strptime()` 独立于任何平台，因此不一定支持所有未记录为支持的可用指令。

_class _time.struct_time¶

    

~~~
返回的时间值序列的类型为 `gmtime()` 、 `localtime()` 和 `strptime()` 。它是一个带有 [named tuple](../glossary.md#term-named-tuple) 接口的对象：可以通过索引和属性名访问值。 存在以下值：

索引

|

属性

|

值  
  
---|---|---  
  
0

|

`tm_year`

|

（例如，1993）  
  
1

|

`tm_mon`

|

range [1, 12]  
  
2

|

`tm_mday`

|

range [1, 31]  
  
3

|

`tm_hour`

|

range [0, 23]  
  
4

|

`tm_min`

|

range [0, 59]  
  
5

|

`tm_sec`

|

range [0, 61]； 见 `strftime()` 介绍中的 **(2)**  
  
6

|

`tm_wday`

|

range [0, 6] ，周一为 0  
  
7

|

`tm_yday`

|

range [1, 366]  
  
8

|

`tm_isdst`

|

0, 1 或 -1；如下所示  
  
N/A

|

`tm_zone`

|

时区名称的缩写  
  
N/A

|

`tm_gmtoff`

|

以秒为单位的UTC以东偏离  
  
请注意，与C结构不同，月份值是 [1,12] 的范围，而不是 [0,11] 。

在调用 `mktime()` 时， `tm_isdst` 可以在夏令时生效时设置为1，而在夏令时不生效时设置为0。 值-1表示这是未知的，并且通常会导致填写正确的状态。

当一个长度不正确的元组被传递给期望 `struct_time` 的函数，或者具有错误类型的元素时，会引发 [`TypeError`](exceptions.md#TypeError "TypeError") 。

time.time() -> [float](functions.md#float "float")¶
~~~
    

~~~
返回以浮点数表示的从 epoch 开始的秒数形式的时间。 对 [leap seconds](https://en.wikipedia.org/wiki/Leap_second) 的处理取决于具体平台。 在 Windows 和大多数 Unix 系统中，闰秒不会被计入从 epoch 开始的秒数形式的时间中。 这通常被称为 [Unix 时间](https://en.wikipedia.org/wiki/Unix_time)。

请注意，即使时间总是作为浮点数返回，但并非所有系统都提供高于1秒的精度。虽然此函数通常返回非递减值，但如果在两次调用之间设置了系统时钟，则它可以返回比先前调用更低的值。

返回的数字 `time()` 可以通过将其传递给 `gmtime()` 函数或转换为UTC中更常见的时间格式（即年、月、日、小时等）或通过将它传递给 `localtime()` 函数获得本地时间。在这两种情况下都返回一个 `struct_time` 对象，日历日期组件可以从中作为属性访问。

使用 `time_ns()` 以避免 [`float`](functions.md#float "float") 类型导致的精度损失。

time.time_ns() -> [int](functions.md#int "int")¶
~~~
    

~~~
与 `time()` 相似，但返回时间为用整数表示的自 epoch 以来所经过的纳秒数。

在 3.7 版本加入.

time.thread_time() -> [float](functions.md#float "float")¶
~~~
    

~~~
（以小数表示的秒为单位）返回当前线程的系统和用户 CPU 时间的总计值。 它不包括睡眠状态所消耗的时间。 根据定义它只作用于线程范围。 返回值的参考点未被定义，因此只有两次调用之间的差值才是有效的。

使用 `thread_time_ns()` 以避免 [`float`](functions.md#float "float") 类型导致的精度损失。

[可用性](intro.md#availability): Linux, Unix, Windows。

支持 `CLOCK_THREAD_CPUTIME_ID` 的 Unix 系统。

在 3.7 版本加入.

time.thread_time_ns() -> [int](functions.md#int "int")¶
~~~
    

~~~
与 `thread_time()` 相似，但返回纳秒时间。

在 3.7 版本加入.

time.tzset()¶
~~~
    

~~~
重置库例程使用的时间转换规则。环境变量 `TZ` 指定如何完成。它还将设置变量 `tzname` （来自 `TZ` 环境变量）， `timezone` （UTC的西部非DST秒）， `altzone` （UTC以西的DST秒）和 `daylight` （如果此时区没有任何夏令时规则则为0，如果有夏令时适用的时间，无论过去、现在或未来，则为非零）。

[可用性](intro.md#availability): Unix。

备注

虽然在很多情况下，更改 `TZ` 环境变量而不调用 `tzset()` 可能会影响函数的输出，例如 `localtime()` ，不应该依赖此行为。

`TZ` 不应该包含空格。

`TZ` 环境变量的标准格式是（为了清晰起见，添加了空格）:
~~~
    
    
~~~
std offset [dst [offset [,start[/time], end[/time]]]]
~~~

组件的位置是：

`std` 和 `dst`

    

~~~
三个或更多字母数字，给出时区缩写。这些将传到 time.tzname

`offset`
~~~
    

~~~
偏移量的形式为： `± hh[:mm[:ss]]` 。这表示添加到达UTC的本地时间的值。如果前面有 '-' ，则时区位于本初子午线的东边；否则，在它是西边。如果dst之后没有偏移，则假设夏令时比标准时间提前一小时。

`start[/time], end[/time]`
~~~
    

~~~
指示何时更改为DST和从DST返回。开始日期和结束日期的格式为以下之一：

`J _n_`
~~~
    

~~~
Julian日 _n_ （1 <= _n_ <= 365）。闰日不计算在内，因此在所有年份中，2月28日是第59天，3月1日是第60天。

`_n_`
~~~
    

~~~
从零开始的Julian日（0 <= _n_ <= 365）。 闰日计入，可以引用2月29日。

`M _m_. _n_. _d_`
~~~
    

~~~
一年中 _m_ 月的第 _n_ 周（1 <= _n_ <= 5 ，1 <= _m_ <= 12 ，第 5 周表示 “可能在 _m_ 月第 4 周或第 5 周出现的最后第 _d_ 日”）的第 _d_ 天（0 <= _d_ <= 6）。 第 1 周是第 _d_ 天发生的第一周。 第 0 天是星期天。

`time` 的格式与 `offset` 的格式相同，但不允许使用前导符号（ '-' 或 '+' ）。如果没有给出时间，则默认值为02:00:00。
~~~
    
    
~~~shell
>>> os.environ['TZ'] = 'EST+05EDT,M4.1.0,M10.5.0'
>>> time.tzset()
>>> time.strftime('%X %x %Z')
'02:07:36 05/08/03 EDT'
>>> os.environ['TZ'] = 'AEST-10AEDT-11,M10.5.0,M3.5.0'
>>> time.tzset()
>>> time.strftime('%X %x %Z')
'16:08:12 05/08/03 AEST'
~~~

在许多Unix系统（包括 *BSD ， Linux ， Solaris 和 Darwin 上），使用系统的区域信息（ _[tzfile(5)](https://manpages.debian.org/tzfile\(5\))_ ）数据库来指定时区规则会更方便。为此，将 `TZ` 环境变量设置为所需时区数据文件的路径，相对于系统 'zoneinfo' 时区数据库的根目录，通常位于 `/usr/share/zoneinfo` 。 例如，`'US/Eastern'` 、 `'Australia/Melbourne'` 、 `'Egypt'` 或 `'Europe/Amsterdam'`。

    
    
~~~shell
>>> os.environ['TZ'] = 'US/Eastern'
>>> time.tzset()
>>> time.tzname
('EST', 'EDT')
>>> os.environ['TZ'] = 'Egypt'
>>> time.tzset()
>>> time.tzname
('EET', 'EEST')
~~~

## Clock ID 常量¶

这些常量用作 `clock_getres()` 和 `clock_gettime()` 的参数。

time.CLOCK_BOOTTIME¶

    

~~~
与 `CLOCK_MONOTONIC` 相同，除了它还包括系统暂停的任何时间。

这允许应用程序获得一个暂停感知的单调时钟，而不必处理 `CLOCK_REALTIME` 的复杂性，如果使用 `settimeofday()` 或类似的时间更改时间可能会有不连续性。

[可用性](intro.md#availability): Linux >= 2.6.39。

在 3.7 版本加入.

time.CLOCK_HIGHRES¶
~~~
    

~~~
Solaris OS 有一个 `CLOCK_HIGHRES` 计时器，试图使用最佳硬件源，并可能提供接近纳秒的分辨率。 `CLOCK_HIGHRES` 是不可调节的高分辨率时钟。

[可用性](intro.md#availability): Solaris.

在 3.3 版本加入.

time.CLOCK_MONOTONIC¶
~~~
    

~~~
无法设置的时钟，表示自某些未指定的起点以来的单调时间。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

time.CLOCK_MONOTONIC_RAW¶
~~~
    

~~~
类似于 `CLOCK_MONOTONIC` ，但可以访问不受NTP调整影响的原始硬件时间。

[可用性](intro.md#availability): Linux >= 2.6.28, macOS >= 10.12。

在 3.3 版本加入.

time.CLOCK_PROCESS_CPUTIME_ID¶
~~~
    

~~~
来自CPU的高分辨率每进程计时器。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

time.CLOCK_PROF¶
~~~
    

~~~
来自CPU的高分辨率每进程计时器。

[可用性](intro.md#availability): FreeBSD, NetBSD >= 7, OpenBSD。

在 3.7 版本加入.

time.CLOCK_TAI¶
~~~
    

~~~
[国际原子时间](https://www.nist.gov/pml/time-and-frequency-division/nist-time-frequently-asked-questions-faq#tai)

该系统必须有一个当前闰秒表以便能给出正确的回答。 PTP 或 NTP 软件可以用来维护闰秒表。

[可用性](intro.md#availability): Linux。

在 3.9 版本加入.

time.CLOCK_THREAD_CPUTIME_ID¶
~~~
    

~~~
特定于线程的CPU时钟。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

time.CLOCK_UPTIME¶
~~~
    

~~~
该时间的绝对值是系统运行且未暂停的时间，提供准确的正常运行时间测量，包括绝对值和间隔值。

[可用性](intro.md#availability): FreeBSD, OpenBSD >= 5.5。

在 3.7 版本加入.

time.CLOCK_UPTIME_RAW¶
~~~
    

~~~
单调递增的时钟，记录从一个任意起点开始的时间，不受频率或时间调整的影响，并且当系统休眠时将不会递增。

[可用性](intro.md#availability): macOS >= 10.12。

在 3.8 版本加入.

以下常量是唯一可以发送到 `clock_settime()` 的参数。

time.CLOCK_REALTIME¶
~~~
    

~~~
系统范围的实时时钟。 设置此时钟需要适当的权限。

[可用性](intro.md#availability): Unix。

在 3.3 版本加入.

## 时区常量¶

time.altzone¶
~~~
    

~~~
本地DST时区的偏移量，以UTC为单位的秒数，如果已定义。如果当地DST时区在UTC以东（如在西欧，包括英国），则是负数。 只有当 `daylight` 非零时才使用它。 见下面的注释。

time.daylight¶
~~~
    

~~~
如果定义了DST时区，则为非零。 见下面的注释。

time.timezone¶
~~~
    

~~~
本地（非DST）时区的偏移量，UTC以西的秒数（西欧大部分地区为负，美国为正，英国为零）。 见下面的注释。

time.tzname¶
~~~
    

~~~
两个字符串的元组：第一个是本地非DST时区的名称，第二个是本地DST时区的名称。 如果未定义DST时区，则不应使用第二个字符串。 见下面的注释。

备注

对于上述时区常量（ `altzone` 、 `daylight` 、 `timezone` 和 `tzname` ），该值由模块加载时有效的时区规则确定，或者最后一次 `tzset()` 被调用时，并且在过去的时间可能不正确。建议使用来自 `localtime()` 结果的 `tm_gmtoff` 和 `tm_zone` 来获取时区信息。

参见

模块 [`datetime`](datetime.md#module-datetime "datetime: Basic date and time types.")
~~~
    

~~~
更多面向对象的日期和时间接口。

模块 [`locale`](locale.md#module-locale "locale: Internationalization services.")
~~~
    

~~~
国际化服务。 区域设置会影响 `strftime()` 和 `strptime()` 中许多格式说明符的解析。

模块 [`calendar`](calendar.md#module-calendar "calendar: Functions for working with calendars, including some emulation of the Unix cal program.")
~~~
    

~~~
