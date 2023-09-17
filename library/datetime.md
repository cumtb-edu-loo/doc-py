# `datetime` \--- 基本日期和时间类型¶

**源代码：** [Lib/datetime.py](https://github.com/python/cpython/tree/3.12/Lib/datetime.py)

* * *

`datetime` 模块提供用于处理日期和时间的类。

在支持日期时间数学运算的同时，实现的关注点更着重于如何能够更有效地解析其属性用于格式化输出和数据操作。

小技巧

跳到 格式代码。

参见

模块 [`calendar`](calendar.md#module-calendar "calendar: Functions for working with calendars, including some emulation of the Unix cal program.")

    

~~~
通用日历相关函数

模块 [`time`](time.md#module-time "time: Time access and conversions.")
~~~
    

~~~
时间的访问和转换

[`zoneinfo`](zoneinfo.md#module-zoneinfo "zoneinfo: IANA time zone support") 模块
~~~
    

~~~
代表 IANA 时区数据库的具体时区。

[dateutil](https://dateutil.readthedocs.io/en/stable/) 包
~~~
    

~~~
具有扩展时区和解析支持的第三方库。

[DateType](https://pypi.org/project/datetype/) 软件包
~~~
    

~~~
这个第三方库引入了几种独特的静态类型的第三方库，例如允许静态类型检查器区分简单型和感知型日期时间。

## 感知型对象和简单型对象¶

日期和时间对象可以根据它们是否包含时区信息而分为“感知型”和“简单型”两类。

充分掌握应用性算法和政治性时间调整信息例如时区和夏令时的情况下，一个 **感知型** 对象就能相对于其他感知型对象来精确定位自身时间点。 感知型对象是用来表示一个没有解释空间的固定时间点。 [1]

**简单型** 对象没有包含足够多的信息来无歧义地相对于其他 date/time 对象来定位自身时间点。 不论一个简单型对象所代表的是世界标准时间（UTC）、当地时间还是某个其他时区的时间完全取决于具体程序，就像一个特定数字所代表的是米、英里还是质量完全取决于具体程序一样。 简单型对象更易于理解和使用，代价则是忽略了某些现实性考量。

对于要求感知型对象的应用，`datetime` 和 `time` 对象具有一个可选的时区信息属性 `tzinfo`，它可被设为抽象类 `tzinfo` 的子类的一个实例。 这些 `tzinfo` 对象会捕获与 UTC 时间的差值、时区名称以及夏令时是否生效等信息。

`datetime` 模块只提供了一个具体的 `tzinfo` 类，即 `timezone` 类。 `timezone` 类可以表示具有相对于 UTC 的固定时差的简单时区，例如 UTC 本身或北美的 EST 和 EDT 时区等。 支持时间的详细程度取决于具体的应用。 世界各地的时间调整规则往往是政治性多于合理性，经常会发生变化，除了 UTC 之外并没有一个能适合所有应用的标准。

## 常量¶

The `datetime` module exports the following constants:

datetime.MINYEAR¶
~~~
    

~~~
`date` 或者 `datetime` 对象允许的最小年份。 常量 `MINYEAR` 是 `1` 。

datetime.MAXYEAR¶
~~~
    

~~~
`date` 或 `datetime` 对象允许最大的年份。常量 `MAXYEAR` 是 `9999` 。

datetime.UTC¶
~~~
    

~~~
UTC 时区单例 `datetime.timezone.utc` 的别名。

在 3.11 版本加入.

## 有效的类型¶

_class _datetime.date
~~~
    

~~~
一个理想化的简单型日期，它假设当今的公历在过去和未来永远有效。 属性: `year`, `month`, and `day`。

_class _datetime.time
~~~
    

~~~
一个独立于任何特定日期的理想化时间，它假设每一天都恰好等于 24*60*60 秒。 （这里没有“闰秒”的概念。） 包含属性: `hour`, `minute`, `second`, `microsecond` 和 `tzinfo`。

_class _datetime.datetime
~~~
    

~~~
日期和时间的结合。属性：`year`, `month`, `day`, `hour`, `minute`, `second`, `microsecond`, and `tzinfo`.

_class _datetime.timedelta
~~~
    

~~~
表示两个 `date` 对象或 `time` 对象，或者 `datetime` 对象之间的时间间隔，精确到微秒。

_class _datetime.tzinfo
~~~
    

~~~
一个描述时区信息对象的抽象基类。 用来给 `datetime` 和 `time` 类提供自定义的时间调整概念（例如处理时区和/或夏令时）。

_class _datetime.timezone
~~~
    

~~~
一个实现了 `tzinfo` 抽象基类的子类，用于表示相对于 世界标准时间（UTC）的偏移量。

在 3.2 版本加入.

这些类型的对象都是不可变的。

子类关系
~~~
    
    
~~~
object
    timedelta
    tzinfo
        timezone
    time
    date
        datetime
~~~

### 通用的特征属性¶

`date`, `datetime`, `time` 和 `timezone` 类型共享这些通用特性:

  * 这些类型的对象都是不可变的。

  * 这些类型的对象是 [hashable](../glossary.md#term-hashable)，意味着它们可以被用作字典的键。

  * 这些类型的对象支持通过 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.") 模块进行高效的封存。

### 确定一个对象是感知型还是简单型¶

`date` 类型的对象都是简单型的。

`time` 或 `datetime` 类型的对象可以是感知型或者简单型。

一个 `datetime` 对象 _d_ 在以下条件同时成立时将是感知型的：

  1. `d.tzinfo` 不为 `None`

  2. `d.tzinfo.utcoffset(d)` 不返回 `None`

在其他情况下， _d_ 将是简单型的。

一个 `time` 对象 _t_ 在以下条件同时成立时将是感知型的：

  1. `t.tzinfo` 不为 `None`

  2. `t.tzinfo.utcoffset(None)` 不返回 `None`。

在其他情况下， _t_ 将是简单型的。

感知型和简单型之间的区别不适用于 `timedelta` 对象。

## `timedelta` 类对象¶

`timedelta` 对象表示两个 date 或者 time 的时间间隔。

_class _datetime.timedelta( _days =0_, _seconds =0_, _microseconds =0_, _milliseconds =0_, _minutes =0_, _hours =0_, _weeks =0_)¶

    

~~~
所有参数都是可选的并且默认为 `0`。 这些参数可以是整数或者浮点数，也可以是正数或者负数。

只有 _days_ , _seconds_ 和 _microseconds_ 会存储在内部。 参数单位的换算规则如下：

  * 1毫秒会转换成1000微秒。

  * 1分钟会转换成60秒。

  * 1小时会转换成3600秒。

  * 1星期会转换成7天。

日期、秒、微秒都是标准化的，所以它们的表达方式也是唯一的，例：

  * `0 <= microseconds < 1000000`

  * `0 <= seconds < 3600*24` (一天的秒数)

  * `-999999999 <= days <= 999999999`

下面的例子演示了如何对 _days_ , _seconds_ 和 _microseconds_ 以外的任意参数执行“合并”操作并标准化为以上三个结果属性:
~~~
    
    
~~~shell
>>> from datetime import timedelta
>>> delta = timedelta(
...     days=50,
...     seconds=27,
...     microseconds=10,
...     milliseconds=29000,
...     minutes=5,
...     hours=8,
...     weeks=2
... )
>>> # Only days, seconds, and microseconds remain
>>> delta
datetime.timedelta(days=64, seconds=29156, microseconds=10)
~~~

在有任何参数为浮点型并且 microseconds 值为小数的情况下，从所有参数中余下的微秒数将被合并，并使用四舍五入偶不入奇的规则将总计值舍入到最接近的整数微秒值。 如果没有任何参数为浮点型的情况下，则转换和标准化过程将是完全精确的（不会丢失信息）。

如果标准化后的 days 数值超过了指定范围，将会抛出 [`OverflowError`](exceptions.md#OverflowError "OverflowError") 异常。

请注意对负数值进行标准化的结果可能会令人感到惊讶。 例如:

    
    
~~~shell
>>> from datetime import timedelta
>>> d = timedelta(microseconds=-1)
>>> (d.days, d.seconds, d.microseconds)
(-1, 86399, 999999)
~~~

类属性：

timedelta.min¶

    

~~~
The most negative `timedelta` object, `timedelta(-999999999)`.

timedelta.max¶
~~~
    

~~~
The most positive `timedelta` object, `timedelta(days=999999999, hours=23, minutes=59, seconds=59, microseconds=999999)`.

timedelta.resolution¶
~~~
    

~~~
两个不相等的 `timedelta` 类对象最小的间隔为 `timedelta(microseconds=1)`。

需要注意的是，因为标准化的缘故，`timedelta.max` > `-timedelta.min`，`-timedelta.max` 不可以表示一个 `timedelta` 类对象。

实例属性（只读）：

属性

|

值  
  
---|---  
  
`days`

|

-999999999 至 999999999 ，含999999999  
  
`seconds`

|

0 至 86399，包含86399  
  
`microseconds`

|

0 至 999999，包含999999  
  
支持的运算：

运算

|

结果：  
  
---|---  
  
`t1 = t2 + t3`

|

_t2_ 和 _t3_ 的和。 运算后 _t1_ - _t2_ == _t3_ and _t1_ - _t3_ == _t2_ 必为真值。(1)  
  
`t1 = t2 - t3`

|

_t2_ 减 _t3_ 的差。 运算后 _t1_ == _t2_ \- _t3_ and _t2_ == _t1_ \+ _t3_ 必为真值。 (1)(6)  
  
`t1 = t2 * i or t1 = i * t2`

|

乘以一个整数。运算后假如 `i != 0` 则 _t1_ // i == _t2_ 必为真值。  
  
|

In general, _t1_ * i == _t1_ * (i-1) + _t1_ is true. (1)  
  
`t1 = t2 * f or t1 = f * t2`

|

乘以一个浮点数，结果会被舍入到 timedelta 最接近的整数倍。 精度使用四舍五偶入奇不入规则。  
  
`f = t2 / t3`

|

总时间 _t2_ 除以间隔单位 _t3_ (3)。 返回一个 [`float`](functions.md#float "float") 对象。  
  
`t1 = t2 / f or t1 = t2 / i`

|

除以一个浮点数或整数。 结果会被舍入到 timedelta 最接近的整数倍。 精度使用四舍五偶入奇不入规则。  
  
`t1 = t2 // i` or `t1 = t2 // t3`

|

计算底数，其余部分（如果有）将被丢弃。在第二种情况下，将返回整数。 （3）  
  
`t1 = t2 % t3`

|

余数为一个 `timedelta` 对象。(3)  
  
`q, r = divmod(t1, t2)`

|

通过 : `q = t1 // t2` (3) and `r = t1 % t2` 计算出商和余数。q是一个整数，r是一个 `timedelta` 对象。  
  
`+t1`

|

返回一个相同数值的 `timedelta` 对象。  
  
`-t1`

|

等价于 `timedelta`(- _t1.days_ , - _t1.seconds_ , - _t1.microseconds_ ), 和 _t1_ * -1. (1)(4)  
  
`abs(t)`

|

当 `t.days >= 0` 时等于 + _t_ , 当 `t.days < 0` 时 - _t_ 。 (2)  
  
`str(t)`

|

返回一个形如 `[D day[s], ][H]H:MM:SS[.UUUUUU]` 的字符串，当 `t` 为负数的时候， D 也为负数。 (5)  
  
`repr(t)`

|

返回一个 `timedelta` 对象的字符串表示形式，作为附带正规属性值的构造器调用。  
  
注释：

  1. 结果正确，但可能会溢出。

  2. 结果正确，不会溢出。

  3. 除以0将会抛出异常 [`ZeroDivisionError`](exceptions.md#ZeroDivisionError "ZeroDivisionError") 。

  4. - _timedelta.max_ 不是一个 `timedelta` 类对象。

  5. `timedelta` 对象的字符串表示形式类似于其内部表示形式被规范化。对于负时间增量，这会导致一些不寻常的结果。例如:
    
        >>> timedelta(hours=-5)
    datetime.timedelta(days=-1, seconds=68400)
    >>> print(_)
    -1 day, 19:00:00
~~~

  6. 表达式 `t2 - t3` 通常与 `t2 + (-t3)` 是等价的，除非 t3 等于 `timedelta.max`; 在这种情况下前者会返回结果，而后者则会溢出。

除了上面列举的操作以外，`timedelta` 对象还支持与 `date` 和 `datetime` 对象进行特定的相加和相减运算（见下文）。

在 3.2 版本发生变更: 现在已支持 `timedelta` 对象与另一个 `timedelta` 对象相整除或相除，包括求余运算和 [`divmod()`](functions.md#divmod "divmod") 函数。 现在也支持 `timedelta` 对象加上或乘以一个 [`float`](functions.md#float "float") 对象。

支持 `timedelta` 对象之间进行比较，但其中有一些注意事项。

`==` 或 `!=` 比较 _总是_ 返回一个 [`bool`](functions.md#bool "bool") 对象，无论被比较的对象是什么类型:

    
    
~~~shell
>>> from datetime import timedelta
>>> delta1 = timedelta(seconds=57)
>>> delta2 = timedelta(hours=25, seconds=2)
>>> delta2 != delta1
True
>>> delta2 == 5
False
~~~

对于所有其他比较 (例如 `<` 和 `>`)，当一个 `timedelta` 对象与其他类型的对象比较时，将引发 [`TypeError`](exceptions.md#TypeError "TypeError"):

    
    
~~~shell
>>> delta2 > delta1
True
>>> delta2 > 5
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: '>' not supported between instances of 'datetime.timedelta' and 'int'
~~~

在布尔运算中，`timedelta` 对象当且仅当其不等于 `timedelta(0)` 时则会被视为真值。

实例方法：

timedelta.total_seconds()¶

    

~~~
返回期间占用了多少秒。等价于 `td / timedelta(seconds=1)`。对于秒以外的间隔单位，直接使用除法形式 (例如 `td / timedelta(microseconds=1)`)。

需要注意的是，时间间隔较大时，这个方法的结果中的微秒将会失真（大多数平台上大于270年视为一个较大的时间间隔）。

在 3.2 版本加入.

### `timedelta` 用法示例¶

一个标准化的附加示例:
~~~
    
    
~~~shell
>>> # Components of another_year add up to exactly 365 days
>>> from datetime import timedelta
>>> year = timedelta(days=365)
>>> another_year = timedelta(weeks=40, days=84, hours=23,
...                          minutes=50, seconds=600)
>>> year == another_year
True
>>> year.total_seconds()
31536000.0
~~~

`timedelta` 算术运算的示例:

    
    
~~~shell
>>> from datetime import timedelta
>>> year = timedelta(days=365)
>>> ten_years = 10 * year
>>> ten_years
datetime.timedelta(days=3650)
>>> ten_years.days // 365
10
>>> nine_years = ten_years - year
>>> nine_years
datetime.timedelta(days=3285)
>>> three_years = nine_years // 3
>>> three_years, three_years.days // 365
(datetime.timedelta(days=1095), 3)
~~~

## `date` 对象¶

`date` 对象代表一个理想化历法中的日期（年、月和日），即当今的格列高利历向前后两个方向无限延伸。

公元 1 年 1 月 1日是第 1 日，公元 1 年 1 月 2 日是第 2 日，依此类推。 [2]

_class _datetime.date( _year_ , _month_ , _day_ )¶

    

~~~
所有参数都是必要的。 参数必须是在下面范围内的整数：

  * `MINYEAR <= year <= MAXYEAR`

  * `1 <= month <= 12`

  * `1 <= 日期 <= 给定年月对应的天数`

如果参数不在这些范围内，则抛出 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。

其它构造器，所有的类方法：

_classmethod _date.today()¶
~~~
    

~~~
返回当前的本地日期。

这等价于 `date.fromtimestamp(time.time())`。

_classmethod _date.fromtimestamp( _timestamp_ )¶
~~~
    

~~~
返回对应于 POSIX 时间戳的当地时间，例如 [`time.time()`](time.md#time.time "time.time") 返回的就是时间戳。

这可能引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError")，如果时间戳数值超出所在平台 C `localtime()` 函数的支持范围的话，并且会在 `localtime()` 出错时引发 [`OSError`](exceptions.md#OSError "OSError")。 通常该数值会被限制在 1970 年至 2038 年之间。 请注意在时间戳概念包含闰秒的非 POSIX 系统上，闰秒会被 `fromtimestamp()` 所忽略。

在 3.3 版本发生变更: 引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError") 而不是 [`ValueError`](exceptions.md#ValueError "ValueError")，如果时间戳数值超出所在平台 C `localtime()` 函数的支持范围的话，并会在 `localtime()` 出错时引发 [`OSError`](exceptions.md#OSError "OSError") 而不是 [`ValueError`](exceptions.md#ValueError "ValueError")。

_classmethod _date.fromordinal( _ordinal_ )¶
~~~
    

~~~
返回对应于预期格列高利历序号的日期，其中公元 1 年 1 月 1 日的序号为 1。

除非 `1 <= ordinal <= date.max.toordinal()` 否则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。对于任意日期 _d_ ，`date.fromordinal(d.toordinal()) == d`。

_classmethod _date.fromisoformat( _date_string_ )¶
~~~
    

~~~
返回一个对应于以任何有效的 ISO 8601 格式给出的 _date_string_ 的 `date`，序数日期除外 (如 `YYYY-DDD`):
~~~
    
    
~~~shell
>>> from datetime import date
>>> date.fromisoformat('2019-12-04')
datetime.date(2019, 12, 4)
>>> date.fromisoformat('20191204')
datetime.date(2019, 12, 4)
>>> date.fromisoformat('2021-W01-1')
datetime.date(2021, 1, 4)
~~~

在 3.7 版本加入.

在 3.11 版本发生变更: 在之前版本中，此方法仅支持一种格式 `YYYY-MM-DD`。

_classmethod _date.fromisocalendar( _year_ , _week_ , _day_ )¶

    

~~~
返回指定 year, week 和 day 所对应 ISO 历法日期的 `date`。 这是函数 `date.isocalendar()` 的逆操作。

在 3.8 版本加入.

类属性：

date.min¶
~~~
    

~~~
最小的日期 `date(MINYEAR, 1, 1)` 。

date.max¶
~~~
    

~~~
最大的日期 ，`date(MAXYEAR, 12, 31)`。

date.resolution¶
~~~
    

~~~
两个日期对象的最小间隔，`timedelta(days=1)`。

实例属性（只读）：

date.year¶
~~~
    

~~~
在 `MINYEAR` 和 `MAXYEAR` 之间，包含边界。

date.month¶
~~~
    

~~~
1 至 12（含）

date.day¶
~~~
    

~~~
返回1到指定年月的天数间的数字。

支持的运算：

运算

|

结果：  
  
---|---  
  
`date2 = date1 + timedelta`

|

_date2_ 将为 _date1_ 之后的 `timedelta.days` 日。 (1)  
  
`date2 = date1 - timedelta`

|

计算 _date2_ 的值使得 `date2 + timedelta == date1`。 (2)  
  
`timedelta = date1 - date2`

|

(3)  
  
`date1 < date2`

|

如果 _date1_ 的时间在 _date2_ 之前则认为 _date1_ 小于 _date2_ 。 (4)  
  
注释：

  1. 如果 `timedelta.days > 0` 则 _date2_ 将在时间线上前进，如果 `timedelta.days < 0` 则将后退。 操作完成后 `date2 - date1 == timedelta.days`。 `timedelta.seconds` 和 `timedelta.microseconds` 会被忽略。 如果 `date2.year` 将小于 `MINYEAR` 或大于 `MAXYEAR` 则会引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError")。

  2. `timedelta.seconds` 和 `timedelta.microseconds` 会被忽略。

  3. 此值完全精确且不会溢出。 操作完成后 timedelta.seconds 和 timedelta.microseconds 均为 0，并且 date2 + timedelta == date1。

  4. 换句话说，当且仅当 `date1.toordinal() < date2.toordinal()` 时 `date1 < date2`。 日期比较会引发 [`TypeError`](exceptions.md#TypeError "TypeError")，如果比较目标不为 `date` 对象的话。 不过也可能会返回 `NotImplemented`，如果比较目标具有 `timetuple()` 属性的话。 这个钩子给予其他日期对象类型实现混合类型比较的机会。 否则，当 `date` 对象与不同类型的对象比较时将会引发 [`TypeError`](exceptions.md#TypeError "TypeError")，除非是 `==` 或 `!=` 比较。 后两种情况将分别返回 [`False`](constants.md#False "False") 或 [`True`](constants.md#True "True")。

在布尔运算中，所有 `date` 对象都会被视为真值。

实例方法：

date.replace( _year =self.year_, _month =self.month_, _day =self.day_)¶
~~~
    

~~~
返回一个具有同样值的日期，除非通过任何关键字参数给出了某些形参的新值。

示例:
~~~
    
    
~~~shell
>>> from datetime import date
>>> d = date(2002, 12, 31)
>>> d.replace(day=26)
datetime.date(2002, 12, 26)
~~~

`date` objects are also supported by generic function [`copy.replace()`](copy.md#copy.replace "copy.replace").

date.timetuple()¶

    

~~~
返回一个 [`time.struct_time`](time.md#time.struct_time "time.struct_time")，即 [`time.localtime()`](time.md#time.localtime "time.localtime") 所返回的类型。

hours, minutes 和 seconds 值均为 0，且 DST 旗标值为 -1。

`d.timetuple()` 等价于:
~~~
    
    
~~~
time.struct_time((d.year, d.month, d.day, 0, 0, 0, d.weekday(), yday, -1))
~~~

其中 `yday = d.toordinal() - date(d.year, 1, 1).toordinal() + 1` 是当前年份中的日期序号，1 月 1 日的序号为 `1`。

date.toordinal()¶

    

~~~
返回日期的预期格列高利历序号，其中公元 1 年 1 月 1 日的序号为 1。 对于任意 `date` 对象 _d_ ，`date.fromordinal(d.toordinal()) == d`。

date.weekday()¶
~~~
    

~~~
返回一个整数代表星期几，星期一为0，星期天为6。例如， `date(2002, 12, 4).weekday() == 2`，表示的是星期三。参阅 `isoweekday()`。

date.isoweekday()¶
~~~
    

~~~
返回一个整数代表星期几，星期一为1，星期天为7。例如：`date(2002, 12, 4).isoweekday() == 3`,表示星期三。参见 `weekday()`, `isocalendar()`。

date.isocalendar()¶
~~~
    

~~~
返回一个由三部分组成的 [named tuple](../glossary.md#term-named-tuple) 对象: `year`, `week` 和 `weekday`。

ISO 历法是一种被广泛使用的格列高利历。 [3]

ISO 年由 52 或 53 个完整星期构成，每个星期开始于星期一结束于星期日。 一个 ISO 年的第一个星期就是（格列高利）历法的一年中第一个包含星期四的星期。 这被称为 1 号星期，这个星期四所在的 ISO 年与其所在的格列高利年相同。

例如，2004 年的第一天是星期四，因此 ISO 2004 年的第一个星期开始于 2003 年 12 月 29 日星期一，结束于 2004 年 1 月 4 日星期日:
~~~
    
    
~~~shell
>>> from datetime import date
>>> date(2003, 12, 29).isocalendar()
datetime.IsoCalendarDate(year=2004, week=1, weekday=1)
>>> date(2004, 1, 4).isocalendar()
datetime.IsoCalendarDate(year=2004, week=1, weekday=7)
~~~

在 3.9 版本发生变更: 结果由元组改为 [named tuple](../glossary.md#term-named-tuple)。

date.isoformat()¶

    

~~~
返回一个以 ISO 8601 格式 `YYYY-MM-DD` 来表示日期的字符串:
~~~
    
    
~~~shell
>>> from datetime import date
>>> date(2002, 12, 4).isoformat()
'2002-12-04'
~~~

date.__str__()¶

    

~~~
对于日期对象 _d_ , `str(d)` 等价于 `d.isoformat()` 。

date.ctime()¶
~~~
    

~~~
返回一个表示日期的字符串:
~~~
    
    
~~~shell
>>> from datetime import date
>>> date(2002, 12, 4).ctime()
'Wed Dec  4 00:00:00 2002'
~~~

`d.ctime()` 等效于:

    
    
~~~
time.ctime(time.mktime(d.timetuple()))
~~~

在原生 C `ctime()` 函数 ([`time.ctime()`](time.md#time.ctime "time.ctime") 会发起调用该函数，但 `date.ctime()` 则不会) 遵循 C 标准的平台上。

date.strftime( _format_ )¶

    

~~~
返回一个由显式格式字符串所控制的，代表日期的字符串。 表示时、分或秒的格式代码值将为 0。 另请参阅 strftime() 和 strptime() 的行为 和 `date.isoformat()`。

date.__format__( _format_ )¶
~~~
    

~~~
与 `date.strftime()` 相同。 此方法使得在 [格式化字符串字面值](../reference/lexical_analysis.md#f-strings) 中以及使用 [`str.format()`](stdtypes.md#str.format "str.format") 时为 `date` 对象指定格式字符串成为可能。 另请参阅 strftime() 和 strptime() 的行为 和 `date.isoformat()`。

### `date` 用法示例¶

计算距离特定事件天数的例子:
~~~
    
    
~~~shell
>>> import time
>>> from datetime import date
>>> today = date.today()
>>> today
datetime.date(2007, 12, 5)
>>> today == date.fromtimestamp(time.time())
True
>>> my_birthday = date(today.year, 6, 24)
>>> if my_birthday < today:
...     my_birthday = my_birthday.replace(year=today.year + 1)
...
>>> my_birthday
datetime.date(2008, 6, 24)
>>> time_to_birthday = abs(my_birthday - today)
>>> time_to_birthday.days
202
~~~

使用 `date` 的更多例子：

    
    
~~~shell
>>> from datetime import date
>>> d = date.fromordinal(730920) # 730920th day after 1. 1. 0001
>>> d
datetime.date(2002, 3, 11)

>>> # Methods related to formatting string output
>>> d.isoformat()
'2002-03-11'
>>> d.strftime("%d/%m/%y")
'11/03/02'
>>> d.strftime("%A %d. %B %Y")
'Monday 11. March 2002'
>>> d.ctime()
'Mon Mar 11 00:00:00 2002'
>>> 'The {1} is {0:%d}, the {2} is {0:%B}.'.format(d, "day", "month")
'The day is 11, the month is March.'

>>> # Methods for to extracting 'components' under different calendars
>>> t = d.timetuple()
>>> for i in t:     
...     print(i)
2002                # year
3                   # month
11                  # day
0
0
0
0                   # weekday (0 = Monday)
70                  # 70th day in the year
-1
>>> ic = d.isocalendar()
>>> for i in ic:    
...     print(i)
2002                # ISO year
11                  # ISO week number
1                   # ISO day number ( 1 = Monday )

>>> # A date object is immutable; all operations produce a new object
>>> d.replace(year=2005)
datetime.date(2005, 3, 11)
~~~

## `datetime` 对象¶

`datetime` 对象是包含来自 `date` 对象和 `time` 对象的所有信息的单一对象。

与 `date` 对象一样，`datetime` 假定当前的格列高利历向前后两个方向无限延伸；与 `time` 对象一样，`datetime` 假定每一天恰好有 3600*24 秒。

构造器 ：

_class _datetime.datetime( _year_ , _month_ , _day_ , _hour =0_, _minute =0_, _second =0_, _microsecond =0_, _tzinfo =None_, _*_ , _fold =0_)¶

    

~~~
_year_ , _month_ 和 _day_ 参数是必须的。 _tzinfo_ 可以是 `None` 或者是一个 `tzinfo` 子类的实例。 其余的参数必须是在下面范围内的整数：

  * `MINYEAR <= year <= MAXYEAR`,

  * `1 <= month <= 12`,

  * `1 <= day <= 指定年月的天数`,

  * `0 <= hour < 24`,

  * `0 <= minute < 60`,

  * `0 <= second < 60`,

  * `0 <= microsecond < 1000000`,

  * `fold in [0, 1]`.

如果参数不在这些范围内，则抛出 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。

在 3.6 版本加入: 增加了 `fold` 参数。

其它构造器，所有的类方法：

_classmethod _datetime.today()¶
~~~
    

~~~
返回表示当前地方时的 datetime 对象，其中 `tzinfo` 为 `None`。

等价于:
~~~
    
    
~~~
datetime.fromtimestamp(time.time())
~~~

另请参阅 `now()`, `fromtimestamp()`。

此方法的功能等价于 `now()`，但是不带 `tz` 形参。

_classmethod _datetime.now( _tz =None_)¶

    

~~~
返回表示当前地方时的 date 和 time 对象。

如果可选参数 _tz_ 为 `None` 或未指定，这就类似于 `today()`，但该方法会在可能的情况下提供比通过 [`time.time()`](time.md#time.time "time.time") 时间戳所获时间值更高的精度（例如，在提供了 C `gettimeofday()` 函数的平台上就可以做到这一点）。

如果 _tz_ 不为 `None`，它必须是 `tzinfo` 子类的一个实例，并且当前日期和时间将被转换到 _tz_ 时区。

此函数可以替代 `today()` 和 `utcnow()`。

_classmethod _datetime.utcnow()¶
~~~
    

~~~
返回表示当前 UTC 时间的 date 和 time，其中 `tzinfo` 为 `None`。

这类似于 `now()`，但返回的是当前 UTC 日期和时间，类型为简单型 `datetime` 对象。 感知型的当前 UTC 日期时间可通过调用 `datetime.now(timezone.utc)` 来获得。 另请参阅 `now()`。

警告

由于简单型 `datetime` 对象会被许多 `datetime` 方法当作本地时间来处理，最好是使用感知型日期时间对象来表示 UTC 时间。 因此，创建表示当前 UTC 时间的对象的推荐方式是通过调用 `datetime.now(timezone.utc)`。

自 3.12 版本弃用: 请用带 `UTC` 的 `datetime.now()` 代替。

_classmethod _datetime.fromtimestamp( _timestamp_ , _tz =None_)¶
~~~
    

~~~
返回 POSIX 时间戳对应的本地日期和时间，如 [`time.time()`](time.md#time.time "time.time") 返回的。 如果可选参数 _tz_ 指定为 `None` 或未指定，时间戳将转换为平台的本地日期和时间，并且返回的 `datetime` 对象将为简单型。

如果 _tz_ 不为 `None`，它必须是 `tzinfo` 子类的一个实例，并且时间戳将被转换到 _tz_ 指定的时区。

`fromtimestamp()` 可能会引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError")，如果时间戳数值超出所在平台 C `localtime()` 或 `gmtime()` 函数的支持范围的话，并会在 `localtime()` 或 `gmtime()` 报错时引发 [`OSError`](exceptions.md#OSError "OSError")。 通常该数值会被限制在 1970 年至 2038 年之间。 请注意在时间戳概念包含闰秒的非 POSIX 系统上，闰秒会被 `fromtimestamp()` 所忽略，结果可能导致两个相差一秒的时间戳产生相同的 `datetime` 对象。 相比 `utcfromtimestamp()` 更推荐使用此方法。

在 3.3 版本发生变更: 引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError") 而不是 [`ValueError`](exceptions.md#ValueError "ValueError")，如果时间戳数值超出所在平台 C `localtime()` 或 `gmtime()` 函数的支持范围的话。 并会在 `localtime()` 或 `gmtime()` 出错时引发 [`OSError`](exceptions.md#OSError "OSError") 而不是 [`ValueError`](exceptions.md#ValueError "ValueError")。

在 3.6 版本发生变更: `fromtimestamp()` 可能返回 `fold` 值设为 1 的实例。

_classmethod _datetime.utcfromtimestamp( _timestamp_ )¶
~~~
    

~~~
返回对应于 POSIX 时间戳的 UTC `datetime`，其中 `tzinfo` 值为 `None`。 （结果为简单型对象。）

这可能引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError")，如果时间戳数值超出所在平台 C `gmtime()` 函数的支持范围的话，并会在 `gmtime()` 报错时引发 [`OSError`](exceptions.md#OSError "OSError")。 通常该数值会被限制在 1970 至 2038 年之间。

要得到一个感知型 `datetime` 对象，应调用 `fromtimestamp()`:
~~~
    
    
~~~
datetime.fromtimestamp(timestamp, timezone.utc)
~~~

在 POSIX 兼容的平台上，它等价于以下表达式:

    
    
~~~
datetime(1970, 1, 1, tzinfo=timezone.utc) + timedelta(seconds=timestamp)
~~~

不同之处在于后一种形式总是支持完整年份范围：从 `MINYEAR` 到 `MAXYEAR` 的开区间。

警告

由于简单型 `datetime` 对象会被许多 `datetime` 方法当作本地时间来处理，最好是使用感知型日期时间对象来表示 UTC 时间。 因此，创建表示特定 UTC 时间戳的日期时间对象的推荐方式是通过调用 `datetime.fromtimestamp(timestamp, tz=timezone.utc)`。

在 3.3 版本发生变更: 引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError") 而不是 [`ValueError`](exceptions.md#ValueError "ValueError")，如果时间戳数值超出所在平台 C `gmtime()` 函数的支持范围的话。 并会在 `gmtime()` 出错时引发 [`OSError`](exceptions.md#OSError "OSError") 而不是 [`ValueError`](exceptions.md#ValueError "ValueError")。

自 3.12 版本弃用: 请用带 `UTC` 的 `datetime.fromtimestamp()` 代替。

_classmethod _datetime.fromordinal( _ordinal_ )¶

    

~~~
返回对应于预期格列高利历序号的 `datetime`，其中公元 1 年 1 月 1 日的序号为 1。 除非 `1 <= ordinal <= datetime.max.toordinal()` 否则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 结果的 hour, minute, second 和 microsecond 值均为 0，并且 `tzinfo` 值为 `None`。

_classmethod _datetime.combine( _date_ , _time_ , _tzinfo =time.tzinfo_)¶
~~~
    

~~~
返回一个新的 `datetime` 对象，其日期部分等于给定的 `date` 对象的值，而其时间部分等于给定的 `time` 对象的值。 如果提供了 _tzinfo_ 参数，其值会被用来设置结果的 `tzinfo` 属性，否则将使用 _time_ 参数的 `tzinfo` 属性。 如果 _date_ 参数是一个 `datetime` 对象，则其时间部分和 `tzinfo` 属性将被忽略。

对于任意 `datetime` 对象 _d_ ，`d == datetime.combine(d.date(), d.time(), d.tzinfo)`。

在 3.6 版本发生变更: 增加了 _tzinfo_ 参数。

_classmethod _datetime.fromisoformat( _date_string_ )¶
~~~
    

~~~
返回一个对应于以任何有效的 8601 格式给出的 _date_string_ 的 `datetime`，下列格式除外:

  1. 时区时差可能会有带小数的秒值。

  2. `T` 分隔符可以用任何单个 unicode 字符来替换。

  3. 目前不支持序数日期。

  4. 带小数的时和分是不受支持的。

示例:
~~~
    
    
~~~shell
>>> from datetime import datetime
>>> datetime.fromisoformat('2011-11-04')
datetime.datetime(2011, 11, 4, 0, 0)
>>> datetime.fromisoformat('20111104')
datetime.datetime(2011, 11, 4, 0, 0)
>>> datetime.fromisoformat('2011-11-04T00:05:23')
datetime.datetime(2011, 11, 4, 0, 5, 23)
>>> datetime.fromisoformat('2011-11-04T00:05:23Z')
datetime.datetime(2011, 11, 4, 0, 5, 23, tzinfo=datetime.timezone.utc)
>>> datetime.fromisoformat('20111104T000523')
datetime.datetime(2011, 11, 4, 0, 5, 23)
>>> datetime.fromisoformat('2011-W01-2T00:05:23.283')
datetime.datetime(2011, 1, 4, 0, 5, 23, 283000)
>>> datetime.fromisoformat('2011-11-04 00:05:23.283')
datetime.datetime(2011, 11, 4, 0, 5, 23, 283000)
>>> datetime.fromisoformat('2011-11-04 00:05:23.283+00:00')
datetime.datetime(2011, 11, 4, 0, 5, 23, 283000, tzinfo=datetime.timezone.utc)
>>> datetime.fromisoformat('2011-11-04T00:05:23+04:00')   
datetime.datetime(2011, 11, 4, 0, 5, 23,
    tzinfo=datetime.timezone(datetime.timedelta(seconds=14400)))
~~~

在 3.7 版本加入.

在 3.11 版本发生变更: 在之前版本中，此方法仅支持可以由 `date.isoformat()` 或 `datetime.isoformat()` 发出的格式。

_classmethod _datetime.fromisocalendar( _year_ , _week_ , _day_ )¶

    

~~~
返回以 year, week 和 day 值指明的 ISO 历法日期所对应的 `datetime`。 该datetime 对象的非日期部分将使用其标准默认值来填充。 这是函数 `datetime.isocalendar()` 的逆操作。

在 3.8 版本加入.

_classmethod _datetime.strptime( _date_string_ , _format_ )¶
~~~
    

~~~
返回一个对应于 _date_string_ ，根据 _format_ 进行解析得到的 `datetime` 对象。

如果 _format_ 不包含微秒或时区信息，这将等价于:
~~~
    
    
~~~
datetime(*(time.strptime(date_string, format)[0:6]))
~~~

如果 date_string 和 format 无法被 [`time.strptime()`](time.md#time.strptime "time.strptime") 解析或它返回一个不是时间元组的值则将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 另请参阅 strftime() 和 strptime() 的行为 和 `datetime.fromisoformat()`。

类属性：

datetime.min¶

    

~~~
最早的可表示 `datetime`，`datetime(MINYEAR, 1, 1, tzinfo=None)`。

datetime.max¶
~~~
    

~~~
最晚的可表示 `datetime`，`datetime(MAXYEAR, 12, 31, 23, 59, 59, 999999, tzinfo=None)`。

datetime.resolution¶
~~~
    

~~~
两个不相等的 `datetime` 对象之间可能的最小间隔，`timedelta(microseconds=1)`。

实例属性（只读）：

datetime.year¶
~~~
    

~~~
在 `MINYEAR` 和 `MAXYEAR` 之间，包含边界。

datetime.month¶
~~~
    

~~~
1 至 12（含）

datetime.day¶
~~~
    

~~~
返回1到指定年月的天数间的数字。

datetime.hour¶
~~~
    

~~~
取值范围是 `range(24)`。

datetime.minute¶
~~~
    

~~~
取值范围是 `range(60)`。

datetime.second¶
~~~
    

~~~
取值范围是 `range(60)`。

datetime.microsecond¶
~~~
    

~~~
取值范围是 `range(1000000)`。

datetime.tzinfo¶
~~~
    

~~~
作为 _tzinfo_ 参数被传给 `datetime` 构造器的对象，如果没有传入值则为 `None`。

datetime.fold¶
~~~
    

~~~
取值范围是 `[0, 1]`。 用于在重复的时间段中消除边界时间歧义。 （当夏令时结束时回拨时钟或由于政治原因导致当明时区的 UTC 时差减少就会出现重复的时间段。） 取值 0 (1) 表示两个时刻早于（晚于）所代表的同一边界时间。

在 3.6 版本加入.

支持的运算：

运算

|

结果：  
  
---|---  
  
`datetime2 = datetime1 + timedelta`

|

(1)  
  
`datetime2 = datetime1 - timedelta`

|

(2)  
  
`timedelta = datetime1 - datetime2`

|

(3)  
  
`datetime1 < datetime2`

|

比较 `datetime` 与 `datetime`。 (4)  
  
  1. datetime2 是从 datetime1 去掉了一段 timedelta 的结果，如果 `timedelta.days` > 0 则是在时间线上前进，如果 `timedelta.days` < 0 则是在时间线上后退。 该结果具有与输入的 datetime 相同的 `tzinfo` 属性，并且操作完成后 datetime2 - datetime1 == timedelta。 如果 datetime2.year 将要小于 `MINYEAR` 或大于 `MAXYEAR` 则会引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError")。 请注意即使输入的是一个感知型对象，该方法也不会进行时区调整。

  2. 计算 datetime2 使得 datetime2 + timedelta == datetime1。 与相加操作一样，结果具有与输入的 datetime 相同的 `tzinfo` 属性，即使输入的是一个感知型对象，该方法也不会进行时区调整。

  3. 从一个 `datetime` 减去一个 `datetime` 仅对两个操作数均为简单型或均为感知型时有定义。 如果一个是感知型而另一个是简单型，则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

如果两个操作数都是简单型，或都是感知型并且具有相同的 `tzinfo` 属性，则 `tzinfo` 属性会被忽略，并且结果会是一个使得 `datetime2 + t == datetime1` 的 `timedelta` 对象 _t_ 。 在此情况下不会进行时区调整。

如果两个操作数都是感知型且具有不同的 `tzinfo` 属性，`a-b` 操作的效果就如同 _a_ 和 _b_ 首先被转换为简单型 UTC 日期时间。 结果将是 `(a.replace(tzinfo=None) - a.utcoffset()) - (b.replace(tzinfo=None) - b.utcoffset())`，除非具体实现绝对不溢出。

  4. 当 _datetime1_ 的时间在 _datetime2_ 之前则认为 _datetime1_ 小于 _datetime2_ 。

如果比较的一方是简单型而另一方是感知型，则如果尝试进行顺序比较将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。 对于相等比较，简单型实例将永远不等于感知型实例。

如果两个比较方都是感知型，且具有相同的 `tzinfo` 属性，则相同的 `tzinfo` 属性会被忽略并对基本日期时间值进行比较。 如果两个比较方都是感知型且具有不同的 `tzinfo` 属性，则两个比较方将首先通过减去它们的 UTC 差值（使用 `self.utcoffset()` 获取）来进行调整。

在 3.3 版本发生变更: 感知型和简单型 `datetime` 实例之间的相等比较不会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

备注

为了防止比较操作回退为默认的对象地址比较方式，datetime 比较通常会引发 [`TypeError`](exceptions.md#TypeError "TypeError")，如果比较目标不同样为 `datetime` 对象的话。 不过也可能会返回 `NotImplemented`，如果比较目标具有 `timetuple()` 属性的话。 这个钩子给予其他种类的日期对象实现混合类型比较的机会。 如果未实现，则当 `datetime` 对象与不同类型比较时将会引发 [`TypeError`](exceptions.md#TypeError "TypeError")，除非是 `==` 或 `!=` 比较。 后两种情况将分别返回 [`False`](constants.md#False "False") 或 [`True`](constants.md#True "True")。

实例方法：

datetime.date()¶
~~~
    

~~~
返回具有同样 year, month 和 day 值的 `date` 对象。

datetime.time()¶
~~~
    

~~~
返回具有同样 hour, minute, second, microsecond 和 fold 值的 `time` 对象。 `tzinfo` 值为 `None`。 另请参见 `timetz()` 方法。

在 3.6 版本发生变更: fold 值会被复制给返回的 `time` 对象。

datetime.timetz()¶
~~~
    

~~~
返回具有同样 hour, minute, second, microsecond, fold 和 tzinfo 属性性的 `time` 对象。 另请参见 [`time()`](time.md#module-time "time: Time access and conversions.") 方法。

在 3.6 版本发生变更: fold 值会被复制给返回的 `time` 对象。

datetime.replace( _year =self.year_, _month =self.month_, _day =self.day_, _hour =self.hour_, _minute =self.minute_, _second =self.second_, _microsecond =self.microsecond_, _tzinfo =self.tzinfo_, _*_ , _fold =0_)¶
~~~
    

~~~
返回一个具有同样属性值的 datetime，除非通过任何关键字参数为某些属性指定了新值。 请注意可以通过指定 `tzinfo=None` 来从一个感知型 datetime 创建一个简单型 datetime 而不必转换日期和时间数据。

`datetime` objects are also supported by generic function [`copy.replace()`](copy.md#copy.replace "copy.replace").

在 3.6 版本加入: 增加了 `fold` 参数。

datetime.astimezone( _tz =None_)¶
~~~
    

~~~
返回一个具有新的 `tzinfo` 属性 _tz_ 的 `datetime` 对象，并会调整日期和时间数据使得结果对应的 UTC 时间与 _self_ 相同，但为 _tz_ 时区的本地时间。

如果给出了 _tz_ ，则它必须是一个 `tzinfo` 子类的实例，并且其 `utcoffset()` 和 `dst()` 方法不可返回 `None`。 如果 _self_ 为简单型，它会被假定为基于系统时区表示的时间。

如果调用时不传入参数 (或传入 `tz=None`) 则将假定目标时区为系统的本地时区。 转换后 datetime 实例的 `.tzinfo` 属性将被设为一个 `timezone` 实例，时区名称和时差值将从 OS 获取。

如果 `self.tzinfo` 为 _tz_ ，`self.astimezone(tz)` 等于 _self_ : 不会对日期或时间数据进行调整。 否则结果为 _tz_ 时区的本地时间，代表的 UTC 时间与 _self_ 相同：在 `astz = dt.astimezone(tz)` 之后，`astz - astz.utcoffset()` 将具有与 `dt - dt.utcoffset()` 相同的日期和时间数据。

如果你只是想要附加一个时区对象 _tz_ 到一个 datetime 对象 _dt_ 而不调整日期和时间数据，请使用 `dt.replace(tzinfo=tz)`。 如果你只想从一个感知型 datetime 对象 _dt_ 移除时区对象，请使用 `dt.replace(tzinfo=None)`。

请注意默认的 `tzinfo.fromutc()` 方法在 `tzinfo` 的子类中可以被重载，从而影响 `astimezone()` 的返回结果。 如果忽略出错的情况，`astimezone()` 的行为就类似于:
~~~
    
    
~~~python
def astimezone(self, tz):
    if self.tzinfo is tz:
        return self
    # Convert self to UTC, and attach the new time zone object.
    utc = (self - self.utcoffset()).replace(tzinfo=tz)
    # Convert from UTC to tz's local time.
    return tz.fromutc(utc)
~~~

在 3.3 版本发生变更: _tz_ 现在可以被省略。

在 3.6 版本发生变更: `astimezone()` 方法可以由简单型实例调用，这将假定其表示本地时间。

datetime.utcoffset()¶

    

~~~
如果 `tzinfo` 为 `None`，则返回 `None`，否则返回 `self.tzinfo.utcoffset(self)`，并且在后者不返回 `None` 或者一个幅度小于一天的 `timedelta` 对象时将引发异常。

在 3.7 版本发生变更: UTC 时差不再限制为一个整数分钟值。

datetime.dst()¶
~~~
    

~~~
如果 `tzinfo` 为 `None`，则返回 `None`，否则返回 `self.tzinfo.dst(self)`，并且在后者不返回 `None` 或者一个幅度小于一天的 `timedelta` 对象时将引发异常。

在 3.7 版本发生变更: DST 差值不再限制为一个整数分钟值。

datetime.tzname()¶
~~~
    

~~~
如果 `tzinfo` 为 `None`，则返回 `None`，否则返回 `self.tzinfo.tzname(self)`，如果后者不返回 `None` 或者一个字符串对象则将引发异常。

datetime.timetuple()¶
~~~
    

~~~
返回一个 [`time.struct_time`](time.md#time.struct_time "time.struct_time")，即 [`time.localtime()`](time.md#time.localtime "time.localtime") 所返回的类型。

`d.timetuple()` 等价于:
~~~
    
    
~~~
time.struct_time((d.year, d.month, d.day,
                  d.hour, d.minute, d.second,
                  d.weekday(), yday, dst))
~~~

其中 `yday = d.toordinal() - date(d.year, 1, 1).toordinal() + 1` 是日期在当前年份中的序号，起始序号 `1` 表示 1 月 1 日。 结果的 `tm_isdst` 旗标的设定会依据 `dst()` 方法：如果 `tzinfo` 为 `None` 或 `dst()` 返回 `None`，则 `tm_isdst` 将设为 `-1`；否则如果 `dst()` 返回一个非零值，则 `tm_isdst` 将设为 `1`；在其他情况下 `tm_isdst` 将设为 `0`。

datetime.utctimetuple()¶

    

~~~
如果 `datetime` 实例 _d_ 为简单型，这类似于 `d.timetuple()`，不同之处在于 `tm_isdst` 会强制设为 0，无论 `d.dst()` 返回什么结果。 DST 对于 UTC 时间永远无效。

如果 _d_ 为感知型， _d_ 会通过减去 `d.utcoffset()` 来标准化为 UTC 时间，并返回该标准化时间所对应的 [`time.struct_time`](time.md#time.struct_time "time.struct_time")。 `tm_isdst` 会强制设为 0。 请注意如果 _d_.year 为 `MINYEAR` 或 `MAXYEAR` 并且 UTC 调整超出一年的边界则可能引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError")。

警告

由于简单型 `datetime` 对象会被许多 `datetime` 方法当作本地时间来处理，最好是使用感知型日期时间来表示 UTC 时间；因此，使用 `datetime.utctimetuple()` 可能会给出误导性的结果。 如果你有一个表示 UTC 的简单型 `datetime`，请使用 `datetime.replace(tzinfo=timezone.utc)` 将其改为感知型，这样你才能使用 `datetime.timetuple()`。

datetime.toordinal()¶
~~~
    

~~~
返回日期的预期格列高利历序号。 与 `self.date().toordinal()` 相同。

datetime.timestamp()¶
~~~
    

~~~
返回对应于 `datetime` 实例的 POSIX 时间戳。 此返回值是与 [`time.time()`](time.md#time.time "time.time") 返回值类似的 [`float`](functions.md#float "float") 对象。

简单型 `datetime` 实例会被假定为代表本地时间并且此方法依赖于平台的 C `mktime()` 函数来执行转换。 由于在许多平台上 `datetime` 支持的取值范围比 `mktime()` 更广，对于极其遥远的过去或未来此方法可能会引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError") 或 [`OSError`](exceptions.md#OSError "OSError")。

对于感知型 `datetime` 实例，返回值的计算方式为:
~~~
    
    
~~~
(dt - datetime(1970, 1, 1, tzinfo=timezone.utc)).total_seconds()
~~~

在 3.3 版本加入.

在 3.6 版本发生变更: `timestamp()` 方法使用 `fold` 属性来消除重复间隔中的时间歧义。

备注

没有一个方法能直接从表示 UTC 时间的简单型 `datetime` 实例获取 POSIX 时间戳。 如果你的应用程序使用此惯例并且你的系统时区不是设为 UTC，你可以通过提供 `tzinfo=timezone.utc` 来获取 POSIX 时间戳:

    
    
~~~
timestamp = dt.replace(tzinfo=timezone.utc).timestamp()
~~~

或者通过直接计算时间戳:

    
    
~~~
timestamp = (dt - datetime(1970, 1, 1)) / timedelta(seconds=1)
~~~

datetime.weekday()¶

    

~~~
返回一个整数代表星期几，星期一为 0，星期天为 6。 相当于 `self.date().weekday()`。 另请参阅 `isoweekday()`。

datetime.isoweekday()¶
~~~
    

~~~
返回一个整数代表星期几，星期一为 1，星期天为 7。 相当于 `self.date().isoweekday()`。 另请参阅 `weekday()`, `isocalendar()`。

datetime.isocalendar()¶
~~~
    

~~~
返回一个由三部分组成的 [named tuple](../glossary.md#term-named-tuple): `year`, `week` 和 `weekday`。 等同于 `self.date().isocalendar()`。

datetime.isoformat( _sep ='T'_, _timespec ='auto'_)¶
~~~
    

~~~
返回一个以 ISO 8601 格式表示的日期和时间字符串：

  * `YYYY-MM-DDTHH:MM:SS.ffffff`，如果 `microsecond` 不为 0

  * `YYYY-MM-DDTHH:MM:SS`，如果 `microsecond` 为 0

如果 `utcoffset()` 返回值不为 `None`，则添加一个字符串来给出 UTC 时差：

  * `YYYY-MM-DDTHH:MM:SS.ffffff+HH:MM[:SS[.ffffff]]`，如果 `microsecond` 不为 0

  * `YYYY-MM-DDTHH:MM:SS+HH:MM[:SS[.ffffff]]`，如果 `microsecond` 为 0

示例:
~~~
    
    
~~~shell
>>> from datetime import datetime, timezone
>>> datetime(2019, 5, 18, 15, 17, 8, 132263).isoformat()
'2019-05-18T15:17:08.132263'
>>> datetime(2019, 5, 18, 15, 17, tzinfo=timezone.utc).isoformat()
'2019-05-18T15:17:00+00:00'
~~~

可选参数 _sep_ (默认为 `'T'`) 为单个分隔字符，会被放在结果的日期和时间两部分之间。 例如:

    
    
~~~shell
>>> from datetime import tzinfo, timedelta, datetime
>>> class TZ(tzinfo):
...     """A time zone with an arbitrary, constant -06:39 offset."""
...     def utcoffset(self, dt):
...         return timedelta(hours=-6, minutes=-39)
...
>>> datetime(2002, 12, 25, tzinfo=TZ()).isoformat(' ')
'2002-12-25 00:00:00-06:39'
>>> datetime(2009, 11, 27, microsecond=100, tzinfo=TZ()).isoformat()
'2009-11-27T00:00:00.000100-06:39'
~~~

可选参数 _timespec_ 要包含的额外时间组件值 (默认为 `'auto'`)。它可以是以下值之一：

  * `'auto'`: 如果 `microsecond` 为 0 则与 `'seconds'` 相同，否则与 `'microseconds'` 相同。

  * `'hours'`: 以两个数码的 `HH` 格式 包含 `hour`。

  * `'minutes'`: 以 `HH:MM` 格式包含 `hour` 和 `minute`。

  * `'seconds'`: 以 `HH:MM:SS` 格式包含 `hour`, `minute` 和 `second`。

  * `'milliseconds'`: 包含完整时间，但将秒值的小数部分截断至毫秒。 格式为 `HH:MM:SS.sss`。

  * `'microseconds'`: 以 `HH:MM:SS.ffffff` 格式包含完整时间。

备注

排除掉的时间部分将被截断，而不是被舍入。

对于无效的 _timespec_ 参数将引发 [`ValueError`](exceptions.md#ValueError "ValueError"):

    
    
~~~shell
>>> from datetime import datetime
>>> datetime.now().isoformat(timespec='minutes')   
'2002-12-25T00:00'
>>> dt = datetime(2015, 1, 1, 12, 30, 59, 0)
>>> dt.isoformat(timespec='microseconds')
'2015-01-01T12:30:59.000000'
~~~

在 3.6 版本加入: 增加了 _timespec_ 参数。

datetime.__str__()¶

    

~~~
对于 `datetime` 实例 _d_ ，`str(d)` 等价于 `d.isoformat(' ')`。

datetime.ctime()¶
~~~
    

~~~
返回一个表示日期和时间的字符串:
~~~
    
    
~~~shell
>>> from datetime import datetime
>>> datetime(2002, 12, 4, 20, 30, 40).ctime()
'Wed Dec  4 20:30:40 2002'
~~~

输出字符串将 _并不_ 包括时区信息，无论输入的是感知型还是简单型。

`d.ctime()` 等效于:

    
    
~~~
time.ctime(time.mktime(d.timetuple()))
~~~

在原生 C `ctime()` 函数 ([`time.ctime()`](time.md#time.ctime "time.ctime") 会发起调用该函数，但 `datetime.ctime()` 则不会) 遵循 C 标准的平台上。

datetime.strftime( _format_ )¶

    

~~~
返回一个由显式格式字符串所控制的，代表日期和时间的字符串。 另请参阅 strftime() 和 strptime() 的行为 和 `datetime.isoformat()`。

datetime.__format__( _format_ )¶
~~~
    

~~~
与 `datetime.strftime()` 相同。 此方法使得在 [格式化字符串字面值](../reference/lexical_analysis.md#f-strings) 中以及使用 [`str.format()`](stdtypes.md#str.format "str.format") 时为 `datetime` 对象指定格式字符串成为可能。 另请参阅 strftime() 和 strptime() 的行为 和 `datetime.isoformat()`。

### 用法示例: `datetime`¶

使用 `datetime` 对象的例子：
~~~
    
    
~~~shell
>>> from datetime import datetime, date, time, timezone

>>> # Using datetime.combine()
>>> d = date(2005, 7, 14)
>>> t = time(12, 30)
>>> datetime.combine(d, t)
datetime.datetime(2005, 7, 14, 12, 30)

>>> # Using datetime.now()
>>> datetime.now()   
datetime.datetime(2007, 12, 6, 16, 29, 43, 79043)   # GMT +1
>>> datetime.now(timezone.utc)   
datetime.datetime(2007, 12, 6, 15, 29, 43, 79060, tzinfo=datetime.timezone.utc)

>>> # Using datetime.strptime()
>>> dt = datetime.strptime("21/11/06 16:30", "%d/%m/%y %H:%M")
>>> dt
datetime.datetime(2006, 11, 21, 16, 30)

>>> # Using datetime.timetuple() to get tuple of all attributes
>>> tt = dt.timetuple()
>>> for it in tt:   
...     print(it)
...
2006    # year
11      # month
21      # day
16      # hour
30      # minute
0       # second
1       # weekday (0 = Monday)
325     # number of days since 1st January
-1      # dst - method tzinfo.dst() returned None

>>> # Date in ISO format
>>> ic = dt.isocalendar()
>>> for it in ic:   
...     print(it)
...
2006    # ISO year
47      # ISO week
2       # ISO weekday

>>> # Formatting a datetime
>>> dt.strftime("%A, %d. %B %Y %I:%M%p")
'Tuesday, 21. November 2006 04:30PM'
>>> 'The {1} is {0:%d}, the {2} is {0:%B}, the {3} is {0:%I:%M%p}.'.format(dt, "day", "month", "time")
'The day is 21, the month is November, the time is 04:30PM.'
~~~

以下示例定义了一个 `tzinfo` 子类，它捕获 Kabul, Afghanistan 时区的信息，该时区使用 +4 UTC 直到 1945 年，之后则使用 +4:30 UTC:

    
    
~~~
from datetime import timedelta, datetime, tzinfo, timezone

class KabulTz(tzinfo):
    # Kabul used +4 until 1945, when they moved to +4:30
    UTC_MOVE_DATE = datetime(1944, 12, 31, 20, tzinfo=timezone.utc)

    def utcoffset(self, dt):
        if dt.year < 1945:
            return timedelta(hours=4)
        elif (1945, 1, 1, 0, 0) <= dt.timetuple()[:5] < (1945, 1, 1, 0, 30):
            # An ambiguous ("imaginary") half-hour range representing
            # a 'fold' in time due to the shift from +4 to +4:30.
            # If dt falls in the imaginary range, use fold to decide how
            # to resolve. See PEP495.
            return timedelta(hours=4, minutes=(30 if dt.fold else 0))
        else:
            return timedelta(hours=4, minutes=30)

    def fromutc(self, dt):
        # Follow same validations as in datetime.tzinfo
        if not isinstance(dt, datetime):
            raise TypeError("fromutc() requires a datetime argument")
        if dt.tzinfo is not self:
            raise ValueError("dt.tzinfo is not self")

        # A custom implementation is required for fromutc as
        # the input to this function is a datetime with utc values
        # but with a tzinfo set to self.
        # See datetime.astimezone or fromtimestamp.
        if dt.replace(tzinfo=timezone.utc) >= self.UTC_MOVE_DATE:
            return dt + timedelta(hours=4, minutes=30)
        else:
            return dt + timedelta(hours=4)

    def dst(self, dt):
        # Kabul does not observe daylight saving time.
        return timedelta(0)

    def tzname(self, dt):
        if dt >= self.UTC_MOVE_DATE:
            return "+04:30"
        return "+04"
~~~

上述 `KabulTz` 的用法:

    
    
~~~shell
>>> tz1 = KabulTz()

>>> # Datetime before the change
>>> dt1 = datetime(1900, 11, 21, 16, 30, tzinfo=tz1)
>>> print(dt1.utcoffset())
4:00:00

>>> # Datetime after the change
>>> dt2 = datetime(2006, 6, 14, 13, 0, tzinfo=tz1)
>>> print(dt2.utcoffset())
4:30:00

>>> # Convert datetime to another time zone
>>> dt3 = dt2.astimezone(timezone.utc)
>>> dt3
datetime.datetime(2006, 6, 14, 8, 30, tzinfo=datetime.timezone.utc)
>>> dt2
datetime.datetime(2006, 6, 14, 13, 0, tzinfo=KabulTz())
>>> dt2 == dt3
True
~~~

## `time` 对象¶

一个 [`time`](time.md#module-time "time: Time access and conversions.") 对象代表某日的（本地）时间，它独立于任何特定日期，并可通过 `tzinfo` 对象来调整。

_class _datetime.time( _hour =0_, _minute =0_, _second =0_, _microsecond =0_, _tzinfo =None_, _*_ , _fold =0_)¶

    

~~~
所有参数都是可选的。 _tzinfo_ 可以是 `None`，或者是一个 `tzinfo` 子类的实例。 其余的参数必须是在下面范围内的整数：

  * `0 <= hour < 24`,

  * `0 <= minute < 60`,

  * `0 <= second < 60`,

  * `0 <= microsecond < 1000000`,

  * `fold in [0, 1]`.

如果给出一个此范围以外的参数，则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 所有参数值默认为 `0`，只有 _tzinfo_ 默认为 [`None`](constants.md#None "None")。

类属性：

time.min¶
~~~
    

~~~
早最的可表示 `time`, `time(0, 0, 0, 0)`。

time.max¶
~~~
    

~~~
最晚的可表示 `time`, `time(23, 59, 59, 999999)`。

time.resolution¶
~~~
    

~~~
两个不相等的 `time` 对象之间可能的最小间隔，`timedelta(microseconds=1)`，但是请注意 `time` 对象并不支持算术运算。

实例属性（只读）：

time.hour¶
~~~
    

~~~
取值范围是 `range(24)`。

time.minute¶
~~~
    

~~~
取值范围是 `range(60)`。

time.second¶
~~~
    

~~~
取值范围是 `range(60)`。

time.microsecond¶
~~~
    

~~~
取值范围是 `range(1000000)`。

time.tzinfo¶
~~~
    

~~~
作为 tzinfo 参数被传给 `time` 构造器的对象，如果没有传入值则为 `None`。

time.fold¶
~~~
    

~~~
取值范围是 `[0, 1]`。 用于在重复的时间段中消除边界时间歧义。 （当夏令时结束时回拨时钟或由于政治原因导致当明时区的 UTC 时差减少就会出现重复的时间段。） 取值 0 (1) 表示两个时刻早于（晚于）所代表的同一边界时间。

在 3.6 版本加入.

`time` 对象支持 `time` 与 `time` 的比较，当 _a_ 时间在 _b_ 之前时，则认为 _a_ 小于 _b_ 。 如果比较的一方是简单型而另一方是感知型，则如果尝试进行顺序比较将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。 对于相等比较，简单型实例将永远不等于感知型实例。

如果两个比较方都是感知型，且具有相同的 `tzinfo` 属性，相同的 `tzinfo` 属性会被忽略并对基本时间值进行比较。 如果两个比较方都是感知型且具有不同的 `tzinfo` 属性，两个比较方将首先通过减去它们的 UTC 时差（从 `self.utcoffset()` 获取）来进行调整。 为了防止将混合类型比较回退为基于对象地址的默认比较，当 `time` 对象与不同类型的对象比较时，将会引发 [`TypeError`](exceptions.md#TypeError "TypeError")，除非比较运算符是 `==` 或 `!=`。 在后两种情况下将分别返回 [`False`](constants.md#False "False") 或 [`True`](constants.md#True "True")。

在 3.3 版本发生变更: 感知型和简单型 `time` 实例之间的相等比较不会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

在布尔运算时，`time` 对象总是被视为真值。

在 3.5 版本发生变更: 在 Python 3.5 之前，如果一个 `time` 对象代表 UTC 午夜零时则会被视为假值。 此行为被认为容易引发困惑和错误，因此从 Python 3.5 起已被去除。 详情参见 [bpo-13936](https://bugs.python.org/issue?@action=redirect&bpo=13936)。

其他构造方法：

_classmethod _time.fromisoformat( _time_string_ )¶
~~~
    

~~~
返回一个对应于以任何有效的 ISO 8601 格式给出的 _time_string_ 的 `time`，下列格式除外:

  1. 时区时差可能会有带小数的秒值。

  2. 打头的 `T`，通常在当日期和时间之间可能存在歧义时才有必要，不是必需的。

  3. 带小数的秒值可以有任意多位数码（超过 6 位将被截断）。

  4. 带小数的时和分是不受支持的。

示例:
~~~
    
    
~~~shell
>>> from datetime import time
>>> time.fromisoformat('04:23:01')
datetime.time(4, 23, 1)
>>> time.fromisoformat('T04:23:01')
datetime.time(4, 23, 1)
>>> time.fromisoformat('T042301')
datetime.time(4, 23, 1)
>>> time.fromisoformat('04:23:01.000384')
datetime.time(4, 23, 1, 384)
>>> time.fromisoformat('04:23:01,000')
datetime.time(4, 23, 1, 384)
>>> time.fromisoformat('04:23:01+04:00')
datetime.time(4, 23, 1, tzinfo=datetime.timezone(datetime.timedelta(seconds=14400)))
>>> time.fromisoformat('04:23:01Z')
datetime.time(4, 23, 1, tzinfo=datetime.timezone.utc)
>>> time.fromisoformat('04:23:01+00:00')
datetime.time(4, 23, 1, tzinfo=datetime.timezone.utc)
~~~

在 3.7 版本加入.

在 3.11 版本发生变更: 在之前版本中，此方法仅支持可由 `time.isoformat()` 发出的格式。

实例方法：

time.replace( _hour =self.hour_, _minute =self.minute_, _second =self.second_, _microsecond =self.microsecond_, _tzinfo =self.tzinfo_, _*_ , _fold =0_)¶

    

~~~
返回一个具有同样属性值的 `time`，除非通过任何关键字参数指定了某些属性值。 请注意可以通过指定 `tzinfo=None` 从一个感知型 `time` 创建一个简单型 `time`，而不必转换时间数据。

[`time`](time.md#module-time "time: Time access and conversions.") objects are also supported by generic function [`copy.replace()`](copy.md#copy.replace "copy.replace").

在 3.6 版本加入: 增加了 `fold` 参数。

time.isoformat( _timespec ='auto'_)¶
~~~
    

~~~
返回表示为下列 ISO 8601 格式之一的时间字符串：

  * `HH:MM:SS.ffffff`，如果 `microsecond` 不为 0

  * `HH:MM:SS`，如果 `microsecond` 为 0

  * `HH:MM:SS.ffffff+HH:MM[:SS[.ffffff]]`，如果 `utcoffset()` 不返回 `None`

  * `HH:MM:SS+HH:MM[:SS[.ffffff]]`，如果 `microsecond` 为 0 并且 `utcoffset()` 不返回 `None`

可选参数 _timespec_ 要包含的额外时间组件值 (默认为 `'auto'`)。它可以是以下值之一：

  * `'auto'`: 如果 `microsecond` 为 0 则与 `'seconds'` 相同，否则与 `'microseconds'` 相同。

  * `'hours'`: 以两个数码的 `HH` 格式 包含 `hour`。

  * `'minutes'`: 以 `HH:MM` 格式包含 `hour` 和 `minute`。

  * `'seconds'`: 以 `HH:MM:SS` 格式包含 `hour`, `minute` 和 `second`。

  * `'milliseconds'`: 包含完整时间，但将秒值的小数部分截断至毫秒。 格式为 `HH:MM:SS.sss`。

  * `'microseconds'`: 以 `HH:MM:SS.ffffff` 格式包含完整时间。

备注

排除掉的时间部分将被截断，而不是被舍入。

对于无效的 _timespec_ 参数将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

示例:
~~~
    
    
~~~shell
>>> from datetime import time
>>> time(hour=12, minute=34, second=56, microsecond=123456).isoformat(timespec='minutes')
'12:34'
>>> dt = time(hour=12, minute=34, second=56, microsecond=0)
>>> dt.isoformat(timespec='microseconds')
'12:34:56.000000'
>>> dt.isoformat(timespec='auto')
'12:34:56'
~~~

在 3.6 版本加入: 增加了 _timespec_ 参数。

time.__str__()¶

    

~~~
对于时间对象 _t_ , `str(t)` 等价于 `t.isoformat()`。

time.strftime( _format_ )¶
~~~
    

~~~
返回一个由显式格式字符串所控制的，代表时间的字符串。 另请参阅 strftime() 和 strptime() 的行为 和 `time.isoformat()`。

time.__format__( _format_ )¶
~~~
    

~~~
与 `time.strftime()` 相同。 此方法使得在 [格式化字符串字面值](../reference/lexical_analysis.md#f-strings) 中以及使用 [`str.format()`](stdtypes.md#str.format "str.format") 时为 `time` 对象指定格式字符串成为可能。 另请参阅 strftime() 和 strptime() 的行为 和 `time.isoformat()`。

time.utcoffset()¶
~~~
    

~~~
如果 `tzinfo` 为 `None`，则返回 `None`，否则返回 `self.tzinfo.utcoffset(None)`，并且在后者不返回 `None` 或一个幅度小于一天的 a `timedelta` 对象时将引发异常。

在 3.7 版本发生变更: UTC 时差不再限制为一个整数分钟值。

time.dst()¶
~~~
    

~~~
如果 `tzinfo` 为 `None`，则返回 `None`，否则返回 `self.tzinfo.dst(None)`，并且在后者不返回 `None` 或者一个幅度小于一天的 `timedelta` 对象时将引发异常。

在 3.7 版本发生变更: DST 差值不再限制为一个整数分钟值。

time.tzname()¶
~~~
    

~~~
如果 `tzinfo` 为 `None`，则返回 `None`，否则返回 `self.tzinfo.tzname(None)`，如果后者不返回 `None` 或者一个字符串对象则将引发异常。

### 用法示例: `time`¶

使用 `time` 对象的例子:
~~~
    
    
~~~shell
>>> from datetime import time, tzinfo, timedelta
>>> class TZ1(tzinfo):
...     def utcoffset(self, dt):
...         return timedelta(hours=1)
...     def dst(self, dt):
...         return timedelta(0)
...     def tzname(self,dt):
...         return "+01:00"
...     def  __repr__(self):
...         return f"{self.__class__.__name__}()"
...
>>> t = time(12, 10, 30, tzinfo=TZ1())
>>> t
datetime.time(12, 10, 30, tzinfo=TZ1())
>>> t.isoformat()
'12:10:30+01:00'
>>> t.dst()
datetime.timedelta(0)
>>> t.tzname()
'+01:00'
>>> t.strftime("%H:%M:%S %Z")
'12:10:30 +01:00'
>>> 'The {} is {:%H:%M}.'.format("time", t)
'The time is 12:10.'
~~~

## `tzinfo` 对象¶

_class _datetime.tzinfo¶

    

~~~
这是一个抽象基类，也就是说该类不应被直接实例化。 请定义 `tzinfo` 的子类来捕获有关特定时区的信息。

`tzinfo` 的（某个实体子类）的实例可以被传给 `datetime` 和 `time` 对象的构造器。 这些对象会将它们的属性视为对应于本地时间，并且 `tzinfo` 对象支持展示本地时间与 UTC 的差值、时区名称以及 DST 差值的方法，都是与传给它们的日期或时间对象的相对值。

你需要派生一个实体子类，并且（至少）提供你使用 `datetime` 方法所需要的标准 `tzinfo` 方法的实现。 `datetime` 模块提供了 `timezone`，这是 `tzinfo` 的一个简单实体子类，它能以与 UTC 的固定差值来表示不同的时区，例如 UTC 本身或北美的 EST 和 EDT。

对于封存操作的特殊要求：一个 `tzinfo` 子类必须具有可不带参数调用的 `__init__()` 方法，否则它虽然可以被封存，但可能无法再次解封。 这是个技术性要求，在未来可能会被取消。

一个 `tzinfo` 的实体子类可能需要实现以下方法。 具体需要实现的方法取决于感知型 `datetime` 对象如何使用它。 如果有疑问，可以简单地全都实现。

tzinfo.utcoffset( _dt_ )¶
~~~
    

~~~
将本地时间与 UTC 时差返回为一个 `timedelta` 对象，如果本地时区在 UTC 以东则为正值。 如果本地时区在 UTC 以西则为负值。

这表示与 UTC 的 _总计_ 时差；举例来说，如果一个 `tzinfo` 对象同时代表时区和 DST 调整，则 `utcoffset()` 应当返回两者的和。 如果 UTC 时差不确定则返回 `None`。 在其他情况下返回值必须为一个 `timedelta` 对象，其取值严格限制于 `-timedelta(hours=24)` 和 `timedelta(hours=24)` 之间（差值的幅度必须小于一天）。 大多数 `utcoffset()` 的实现看起来可能像是以下两者之一:
~~~
    
    
~~~
return CONSTANT                 # fixed-offset class
return CONSTANT + self.dst(dt)  # daylight-aware class
~~~

如果 `utcoffset()` 返回值不为 `None`，则 `dst()` 也不应返回 `None`。

默认的 `utcoffset()` 实现会引发 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError")。

在 3.7 版本发生变更: UTC 时差不再限制为一个整数分钟值。

tzinfo.dst( _dt_ )¶

    

~~~
将夏令时（DST）调整返回为一个 `timedelta` 对象，如果 DST 信息未知则返回 `None`。

如果 DST 未启用则返回 `timedelta(0)`。 如果 DST 已启用则将差值作为一个 `timedelta` 对象返回（参见 `utcoffset()` 了解详情）。 请注意 DST 差值如果可用，就会直接被加入 `utcoffset()` 所返回的 UTC 时差，因此无需额外查询 `dst()` 除非你希望单独获取 DST 信息。 例如，`datetime.timetuple()` 会调用其 `tzinfo` 属性的 `dst()` 方法来确定应该如何设置 `tm_isdst` 旗标，而 `tzinfo.fromutc()` 会调用 `dst()` 来在跨越时区时处理 DST 的改变。

一个可以同时处理标准时和夏令时的 `tzinfo` 子类的实例 _tz_ 必须在此情形中保持一致：

`tz.utcoffset(dt) - tz.dst(dt)`

必须为具有同样的 `tzinfo` 子类实例且 `dt.tzinfo == tz` 的每个 `datetime` 对象 _dt_ 返回同样的结果，此表达式会产生时区的“标准时差”，它不应取决于具体日期或时间，只取决于地理位置。 `datetime.astimezone()` 的实现依赖此方法，但无法检测违反规则的情况；确保符合规则是程序员的责任。 如果一个 `tzinfo` 子类不能保证这一点，也许可以重载 `tzinfo.fromutc()` 的默认实现以便在任何情况下与 `astimezone()` 正确配合。

大多数 `dst()` 的实现可能会如以下两者之一:
~~~
    
    
~~~python
def dst(self, dt):
    # a fixed-offset class:  doesn't account for DST
    return timedelta(0)
~~~

或者：

    
    
~~~python
def dst(self, dt):
    # Code to set dston and dstoff to the time zone's DST
    # transition times based on the input dt.year, and expressed
    # in standard local time.

    if dston <= dt.replace(tzinfo=None) < dstoff:
        return timedelta(hours=1)
    else:
        return timedelta(0)
~~~

默认的 `dst()` 实现会引发 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError")。

在 3.7 版本发生变更: DST 差值不再限制为一个整数分钟值。

tzinfo.tzname( _dt_ )¶

    

~~~
将对应于 `datetime` 对象 _dt_ 的时区名称作为字符串返回。 `datetime` 模块没有定义任何字符串名称相关内容，也不要求名称有任何特定含义。 例如 "GMT", "UTC", "-500", "-5:00", "EDT", "US/Eastern", "America/New York" 都是有效的返回值。 如果字符串名称未知则返回 `None`。 请注意这是一个方法而不是一个固定的字符串，这主要是因为某些 `tzinfo` 子类可能需要根据所传入的特定 _dt_ 值返回不同的名称，特别是当 `tzinfo` 类要负责处理夏令时的时候。

默认的 `tzname()` 实现会引发 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError")。

这些方法会被 `datetime` 或 `time` 对象调用，用来与它们的同名方法相对应。 `datetime` 对象会将自身作为传入参数，而 `time` 对象会将 `None` 作为传入参数。 这样 `tzinfo` 子类的方法应当准备好接受 _dt_ 参数值为 `None` 或是 `datetime` 类的实例。

当传入 `None` 时，应当由类的设计者来决定最佳回应方式。 例如，返回 `None` 适用于希望该类提示时间对象不参与 `tzinfo` 协议处理。 让 `utcoffset(None)` 返回标准 UTC 时差也许会更有用处，因为并没有其他可用于发现标准时差的约定惯例。

当传入一个 `datetime` 对象来回应 `datetime` 方法时，`dt.tzinfo` 与 _self_ 是同一对象。 `tzinfo` 方法可以依赖这一点，除非用户代码直接调用了 `tzinfo` 方法。 此行为的目的是使得 `tzinfo` 方法将 _dt_ 解读为本地时间，而不需要担心其他时区的相关对象。

还有一个额外的 `tzinfo` 方法，某个子类可能会希望重载它：

tzinfo.fromutc( _dt_ )¶
~~~
    

~~~
此方法会由默认的 `datetime.astimezone()` 实现来调用。 当被其调用时，`dt.tzinfo` 为 _self_ ，并且 _dt_ 的日期和时间数据会被视为表示 UTC 时间，`fromutc()` 的目标是调整日期和时间数据，返回一个等价的 datetime 来表示 _self_ 的本地时间。

大多数 `tzinfo` 子类应该能够毫无问题地继承默认的 `fromutc()` 实现。 它的健壮性足以处理固定差值的时区以及同时负责标准时和夏令时的时区，对于后者甚至还能处理 DST 转换时间在各个年份有变化的情况。 一个默认 `fromutc()` 实现可能无法在所有情况下正确处理的例子是（与 UTC 的）标准时差取决于所经过的特定日期和时间，这种情况可能由于政治原因而出现。 默认的 `astimezone()` 和 `fromutc()` 实现可能无法生成你希望的结果，如果这个结果恰好是跨越了标准时差发生改变的时刻当中的某个小时值的话。

忽略针对错误情况的代码，默认 `fromutc()` 实现的行为方式如下:
~~~
    
    
~~~python
def fromutc(self, dt):
    # raise ValueError error if dt.tzinfo is not self
    dtoff = dt.utcoffset()
    dtdst = dt.dst()
    # raise ValueError if dtoff is None or dtdst is None
    delta = dtoff - dtdst  # this is self's standard offset
    if delta:
        dt += delta   # convert to standard local time
        dtdst = dt.dst()
        # raise ValueError if dtdst is None
    if dtdst:
        return dt + dtdst
    else:
        return dt
~~~

在以下 [`tzinfo_examples.py`](../_downloads/6dc1f3f4f0e6ca13cb42ddf4d6cbc8af/tzinfo_examples.py) 文件中有一些 `tzinfo` 类的例子：

    
    
~~~
from datetime import tzinfo, timedelta, datetime

ZERO = timedelta(0)
HOUR = timedelta(hours=1)
SECOND = timedelta(seconds=1)

# A class capturing the platform's idea of local time.
# (May result in wrong values on historical times in
#  timezones where UTC offset and/or the DST rules had
#  changed in the past.)
import time as _time

STDOFFSET = timedelta(seconds = -_time.timezone)
if _time.daylight:
    DSTOFFSET = timedelta(seconds = -_time.altzone)
else:
    DSTOFFSET = STDOFFSET

DSTDIFF = DSTOFFSET - STDOFFSET

class LocalTimezone(tzinfo):

    def fromutc(self, dt):
        assert dt.tzinfo is self
        stamp = (dt - datetime(1970, 1, 1, tzinfo=self)) // SECOND
        args = _time.localtime(stamp)[:6]
        dst_diff = DSTDIFF // SECOND
        # Detect fold
        fold = (args == _time.localtime(stamp - dst_diff))
        return datetime(*args, microsecond=dt.microsecond,
                        tzinfo=self, fold=fold)

    def utcoffset(self, dt):
        if self._isdst(dt):
            return DSTOFFSET
        else:
            return STDOFFSET

    def dst(self, dt):
        if self._isdst(dt):
            return DSTDIFF
        else:
            return ZERO

    def tzname(self, dt):
        return _time.tzname[self._isdst(dt)]

    def _isdst(self, dt):
        tt = (dt.year, dt.month, dt.day,
              dt.hour, dt.minute, dt.second,
              dt.weekday(), 0, 0)
        stamp = _time.mktime(tt)
        tt = _time.localtime(stamp)
        return tt.tm_isdst > 0

Local = LocalTimezone()
~~~
    
    # A complete implementation of current DST rules for major US time zones.
    
    def first_sunday_on_or_after(dt):
        days_to_go = 6 - dt.weekday()
        if days_to_go:
            dt += timedelta(days_to_go)
        return dt
    
    
    # US DST Rules
    #
    # This is a simplified (i.e., wrong for a few cases) set of rules for US
    # DST start and end times. For a complete and up-to-date set of DST rules
    # and timezone definitions, visit the Olson Database (or try pytz):
    # http://www.twinsun.com/tz/tz-link.htm
    # https://sourceforge.net/projects/pytz/ (might not be up-to-date)
    #
    # In the US, since 2007, DST starts at 2am (standard time) on the second
    # Sunday in March, which is the first Sunday on or after Mar 8.
    DSTSTART_2007 = datetime(1, 3, 8, 2)
    # and ends at 2am (DST time) on the first Sunday of Nov.
    DSTEND_2007 = datetime(1, 11, 1, 2)
    # From 1987 to 2006, DST used to start at 2am (standard time) on the first
    # Sunday in April and to end at 2am (DST time) on the last
    # Sunday of October, which is the first Sunday on or after Oct 25.
    DSTSTART_1987_2006 = datetime(1, 4, 1, 2)
    DSTEND_1987_2006 = datetime(1, 10, 25, 2)
    # From 1967 to 1986, DST used to start at 2am (standard time) on the last
    # Sunday in April (the one on or after April 24) and to end at 2am (DST time)
    # on the last Sunday of October, which is the first Sunday
    # on or after Oct 25.
    DSTSTART_1967_1986 = datetime(1, 4, 24, 2)
    DSTEND_1967_1986 = DSTEND_1987_2006
    
    def us_dst_range(year):
        # Find start and end times for US DST. For years before 1967, return
        # start = end for no DST.
        if 2006 < year:
            dststart, dstend = DSTSTART_2007, DSTEND_2007
        elif 1986 < year < 2007:
            dststart, dstend = DSTSTART_1987_2006, DSTEND_1987_2006
        elif 1966 < year < 1987:
            dststart, dstend = DSTSTART_1967_1986, DSTEND_1967_1986
        else:
            return (datetime(year, 1, 1), ) * 2
    
        start = first_sunday_on_or_after(dststart.replace(year=year))
        end = first_sunday_on_or_after(dstend.replace(year=year))
        return start, end
    
    
    class USTimeZone(tzinfo):
    
        def __init__(self, hours, reprname, stdname, dstname):
            self.stdoffset = timedelta(hours=hours)
            self.reprname = reprname
            self.stdname = stdname
            self.dstname = dstname
    
        def __repr__(self):
            return self.reprname
    
        def tzname(self, dt):
            if self.dst(dt):
                return self.dstname
            else:
                return self.stdname
    
        def utcoffset(self, dt):
            return self.stdoffset + self.dst(dt)
    
        def dst(self, dt):
            if dt is None or dt.tzinfo is None:
                # An exception may be sensible here, in one or both cases.
                # It depends on how you want to treat them.  The default
                # fromutc() implementation (called by the default astimezone()
                # implementation) passes a datetime with dt.tzinfo is self.
                return ZERO
            assert dt.tzinfo is self
            start, end = us_dst_range(dt.year)
            # Can't compare naive to aware objects, so strip the timezone from
            # dt first.
            dt = dt.replace(tzinfo=None)
            if start + HOUR <= dt < end - HOUR:
                # DST is in effect.
                return HOUR
            if end - HOUR <= dt < end:
                # Fold (an ambiguous hour): use dt.fold to disambiguate.
                return ZERO if dt.fold else HOUR
            if start <= dt < start + HOUR:
                # Gap (a non-existent hour): reverse the fold rule.
                return HOUR if dt.fold else ZERO
            # DST is off.
            return ZERO
    
        def fromutc(self, dt):
            assert dt.tzinfo is self
            start, end = us_dst_range(dt.year)
            start = start.replace(tzinfo=self)
            end = end.replace(tzinfo=self)
            std_time = dt + self.stdoffset
            dst_time = std_time + HOUR
            if end <= dst_time < end + HOUR:
                # Repeated hour
                return std_time.replace(fold=1)
            if std_time < start or dst_time >= end:
                # Standard time
                return std_time
            if start <= std_time < end - HOUR:
                # Daylight saving time
                return dst_time
    
    
    Eastern  = USTimeZone(-5, "Eastern",  "EST", "EDT")
    Central  = USTimeZone(-6, "Central",  "CST", "CDT")
    Mountain = USTimeZone(-7, "Mountain", "MST", "MDT")
    Pacific  = USTimeZone(-8, "Pacific",  "PST", "PDT")
    

请注意同时负责标准时和夏令时的 `tzinfo` 子类在每年两次的 DST 转换点上会出现不可避免的微妙问题。具体而言，考虑美国东部时区 (UTC -0500)，它的 EDT 从三月的第二个星期天 1:59 (EST) 之后一分钟开始，并在十一月的第一天星期天 1:59 (EDT) 之后一分钟结束:

    
    
~~~
  UTC   3:MM  4:MM  5:MM  6:MM  7:MM  8:MM
  EST  22:MM 23:MM  0:MM  1:MM  2:MM  3:MM
  EDT  23:MM  0:MM  1:MM  2:MM  3:MM  4:MM

start  22:MM 23:MM  0:MM  1:MM  3:MM  4:MM

  end  23:MM  0:MM  1:MM  1:MM  2:MM  3:MM
~~~

当 DST 开始时（即 "start" 行），本地时钟从 1:59 跳到 3:00。 形式为 2:MM 的时间值在那一天是没有意义的，因此在 DST 开始那一天 `astimezone(Eastern)` 不会输出包含 `hour == 2` 的结果。 例如，在 2016 年春季时钟向前调整时，我们得到:

    
    
~~~shell
>>> from datetime import datetime, timezone
>>> from tzinfo_examples import HOUR, Eastern
>>> u0 = datetime(2016, 3, 13, 5, tzinfo=timezone.utc)
>>> for i in range(4):
...     u = u0 + i*HOUR
...     t = u.astimezone(Eastern)
...     print(u.time(), 'UTC =', t.time(), t.tzname())
...
05:00:00 UTC = 00:00:00 EST
06:00:00 UTC = 01:00:00 EST
07:00:00 UTC = 03:00:00 EDT
08:00:00 UTC = 04:00:00 EDT
~~~

当 DST 结束时（见 "end" 行），会有更糟糕的潜在问题：本地时间值中有一个小时是不可能没有歧义的：夏令时的最后一小时。 即以北美东部时间表示当天夏令时结束时的形式为 5:MM UTC 的时间。 本地时钟从 1:59 (夏令时) 再次跳回到 1:00 (标准时)。 形式为 1:MM 的本地时间就是有歧义的。 此时 `astimezone()` 是通过将两个相邻的 UTC 小时映射到两个相同的本地小时来模仿本地时钟的行为。 在这个北美东部时间的示例中，形式为 5:MM 和 6:MM 的 UTC 时间在转换为北美东部时间时都将被映射到 1:MM，但前一个时间会将 `fold` 属性设为 0 而后一个时间会将其设为 1。 例如，在 2016 年秋季时钟往回调整时，我们得到:

    
    
~~~shell
>>> u0 = datetime(2016, 11, 6, 4, tzinfo=timezone.utc)
>>> for i in range(4):
...     u = u0 + i*HOUR
...     t = u.astimezone(Eastern)
...     print(u.time(), 'UTC =', t.time(), t.tzname(), t.fold)
...
04:00:00 UTC = 00:00:00 EDT 0
05:00:00 UTC = 01:00:00 EDT 0
06:00:00 UTC = 01:00:00 EST 1
07:00:00 UTC = 02:00:00 EST 0
~~~

请注意不同的 `datetime` 实例仅通过 `fold` 属性值来加以区分，它们在比较时会被视为相等。

不允许时间显示存在歧义的应用需要显式地检查 `fold` 属性的值，或者避免使用混合式的 `tzinfo` 子类；当使用 `timezone` 或者任何其他固定差值的 `tzinfo` 子类例如仅表示 EST（固定差值 -5 小时）或仅表示 EDT（固定差值 -4 小时）的类时是不会有歧义的。

参见

> [`zoneinfo`](zoneinfo.md#module-zoneinfo "zoneinfo: IANA time zone support")
>  
>
> `datetime` 模块有一个基本 `timezone` 类（用来处理任意与 UTC 的固定时差）及其 `timezone.utc` 属性（一个 UTC 时区实例）。
>
> `zoneinfo` 为 Python 带来了 _IANA时区数据库_ （也被称为 Olson 数据库），推荐使用它。

[IANA 时区数据库](https://www.iana.org/time-zones)

    

~~~
该时区数据库 (通常称为 tz, tzdata 或 zoneinfo) 包含大量代码和数据用来表示全球许多有代表性的地点的本地时间的历史信息。 它会定期进行更新以反映各政治实体对时区边界、UTC 差值和夏令时规则的更改。

## `timezone` 对象¶

`timezone` 类是 `tzinfo` 的子类，它的每个实例都代表一个以与 UTC 的固定时差来定义的时区。

此类的对象不可被用于代表某些特殊地点的时区信息，这些地点在一年的不同日期会使用不同的时差，或是在历史上对民用时间进行过调整。

_class _datetime.timezone( _offset_ , _name =None_)¶
~~~
    

~~~
_offset_ 参数必须指定为一个 `timedelta` 对象，表示本地时间与 UTC 的时差。 它必须严格限制于 `-timedelta(hours=24)` 和 `timedelta(hours=24)` 之间，否则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

_name_ 参数是可选的。 如果指定则必须为一个字符串，它将被用作 `datetime.tzname()` 方法的返回值。

在 3.2 版本加入.

在 3.7 版本发生变更: UTC 时差不再限制为一个整数分钟值。

timezone.utcoffset( _dt_ )¶
~~~
    

~~~
返回当 `timezone` 实例被构造时指定的固定值。

_dt_ 参数会被忽略。 返回值是一个 `timedelta` 实例，其值等于本地时间与 UTC 之间的时差。

在 3.7 版本发生变更: UTC 时差不再限制为一个整数分钟值。

timezone.tzname( _dt_ )¶
~~~
    

~~~
返回当 `timezone` 实例被构造时指定的固定值。

如果没有在构造器中提供 _name_ ，则 `tzname(dt)` 所返回的名称将根据 `offset` 值按以下规则生成。 如果 _offset_ 为 `timedelta(0)`，则名称为“UTC”，否则为字符串 `UTC±HH:MM`，其中 ± 为 `offset` 的正负符号，HH 和 MM 分别为表示 `offset.hours` 和 `offset.minutes` 的两个数码。

在 3.6 版本发生变更: 由 `offset=timedelta(0)` 生成的名称现在是简单的 `'UTC'`，而不是 `'UTC+00:00'`。

timezone.dst( _dt_ )¶
~~~
    

~~~
总是返回 `None`。

timezone.fromutc( _dt_ )¶
~~~
    

~~~
返回 `dt + offset`。 _dt_ 参数必须为一个感知型 `datetime` 实例，其中 `tzinfo` 值设为 `self`。

类属性：

timezone.utc¶
~~~
    

~~~
UTC 时区，`timezone(timedelta(0))`。

## `strftime()` 和 `strptime()` 的行为¶

`date`, `datetime` 和 `time` 对象都支持 `strftime(format)` 方法，可用来创建由一个显式格式字符串所控制的表示时间的字符串。

相反地，`datetime.strptime()` 类会根据表示日期和时间的字符串和相应的格式字符串来创建一个 `datetime` 对象。

下表提供了 `strftime()` 与 `strptime()` 的高层级比较：

|

`strftime`

|

`strptime`  
  
---|---|---  
  
用法

|

根据给定的格式将对象转换为字符串

|

将字符串解析为给定相应格式的 `datetime` 对象  
  
方法类型

|

实例方法

|

类方法  
  
方法

|

`date`; `datetime`; `time`

|

`datetime`  
  
签名

|

`strftime(format)`

|

`strptime(date_string, format)`  
  
> ### `strftime()` 和 `strptime()` Format Codes¶

这些方法接受可被用于解析和格式化日期的格式代码:
~~~
    
    
~~~shell
>>> datetime.strptime('31/01/22 23:59:59.999999',
...                   '%d/%m/%y %H:%M:%S.%f')
datetime.datetime(2022, 1, 31, 23, 59, 59, 999999)
>>> _.strftime('%a %d %b %Y, %I:%M%p')
'Mon 31 Jan 2022, 11:59PM'
~~~

以下列表显示了 1989 版 C 标准所要求的全部格式代码，它们在带有标准 C 实现的所有平台上均可用。

指令

|

含意

|

示例

|

备注  
  
---|---|---|---  
  
`%a`

|

当地工作日的缩写。

|

Sun, Mon, ..., Sat (en_US);

So, Mo, ..., Sa (de_DE)

|

(1)  
  
`%A`

|

本地化的星期中每日的完整名称。

|

Sunday, Monday, ..., Saturday (en_US);

Sonntag, Montag, ..., Samstag (de_DE)

|

(1)  
  
`%w`

|

以十进制数显示的工作日，其中0表示星期日，6表示星期六。

|

0, 1, ..., 6

|  
  
`%d`

|

补零后，以十进制数显示的月份中的一天。

|

01, 02, ..., 31

|

(9)  
  
`%b`

|

当地月份的缩写。

|

Jan, Feb, ..., Dec (en_US);

Jan, Feb, ..., Dez (de_DE)

|

(1)  
  
`%B`

|

本地化的月份全名。

|

January, February, ..., December (en_US);

Januar, Februar, ..., Dezember (de_DE)

|

(1)  
  
`%m`

|

补零后，以十进制数显示的月份。

|

01, 02, ..., 12

|

(9)  
  
`%y`

|

补零后，以十进制数表示的，不带世纪的年份。

|

00, 01, ..., 99

|

(9)  
  
`%Y`

|

十进制数表示的带世纪的年份。

|

0001, 0002, ..., 2013, 2014, ..., 9998, 9999

|

(2)  
  
`%H`

|

以补零后的十进制数表示的小时（24 小时制）。

|

00, 01, ..., 23

|

(9)  
  
`%I`

|

以补零后的十进制数表示的小时（12 小时制）。

|

01, 02, ..., 12

|

(9)  
  
`%p`

|

本地化的 AM 或 PM 。

|

AM, PM (en_US);

am, pm (de_DE)

|

(1), (3)  
  
`%M`

|

补零后，以十进制数显示的分钟。

|

00, 01, ..., 59

|

(9)  
  
`%S`

|

补零后，以十进制数显示的秒。

|

00, 01, ..., 59

|

(4), (9)  
  
`%f`

|

微秒作为一个十进制数，零填充到 6 位。

|

000000, 000001, ..., 999999

|

(5)  
  
`%z`

|

UTC 偏移量，格式为 `±HHMM[SS[.ffffff]]` （如果是简单型对象则为空字符串）。

|

(空), +0000, -0400, +1030, +063415, -030712.345216

|

(6)  
  
`%Z`

|

时区名称（如果对象为简单型则为空字符串）。

|

(空), UTC, GMT

|

(6)  
  
`%j`

|

以补零后的十进制数表示的一年中的日序号。

|

001, 002, ..., 366

|

(9)  
  
`%U`

|

以补零后的十进制数表示的一年中的周序号（星期日作为每周的第一天）。 在新的一年中第一个星期日之前的所有日子都被视为是在第 0 周。

|

00, 01, ..., 53

|

(7), (9)  
  
`%W`

|

以补零后的十进制数表示的一年中的周序号（星期一作为每周的第一天）。 在新的一年中第一个星期一之前的所有日子都被视为是在第 0 周。

|

00, 01, ..., 53

|

(7), (9)  
  
`%c`

|

本地化的适当日期和时间表示。

|

Tue Aug 16 21:30:00 1988 (en_US);

Di 16 Aug 21:30:00 1988 (de_DE)

|

(1)  
  
`%x`

|

本地化的适当日期表示。

|

08/16/88 (None);

08/16/1988 (en_US);

16.08.1988 (de_DE)

|

(1)  
  
`%X`

|

本地化的适当时间表示。

|

21:30:00 (en_US);

21:30:00 (de_DE)

|

(1)  
  
`%%`

|

字面的 `'%'` 字符。

|

%

|  
  
为了方便起见，还包括了C89标准不需要的其他一些指令。这些参数都对应于ISO 8601日期值。

指令

|

含意

|

示例

|

备注  
  
---|---|---|---  
  
`%G`

|

带有世纪的 ISO 8601 年份，表示包含大部分 ISO 星期 (`%V`) 的年份。

|

0001, 0002, ..., 2013, 2014, ..., 9998, 9999

|

(8)  
  
`%u`

|

以十进制数显示的 ISO 8601 星期中的日序号，其中 1 表示星期一。

|

1, 2, ..., 7

|  
  
`%V`

|

以十进制数显示的 ISO 8601 星期，以星期一作为每周的第一天。 第 01 周为包含 1 月 4 日的星期。

|

01, 02, ..., 53

|

(8), (9)  
  
`%:z`

|

`±HH:MM[:SS[.ffffff]]` 形式的 UTC 偏移量（如果是简单型对象则为空字符串）。

|

(空), +00:00, -04:00, +10:30, +06:34:15, -03:07:12.345216

|

(6)  
  
这些代码可能不是在所有平台上都可与 `strftime()` 方法配合使用。 ISO 8601 年份和 ISO 8601 星期指令并不能与上面的年份和星期序号指令相互替代。 调用 `strptime()` 时传入不完整或有歧义的 ISO 8601 指令将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

对完整格式代码集的支持在不同平台上有所差异，因为 Python 要调用所在平台的 C 库的 `strftime()` 函数，而不同平台的差异是很常见的。 要查看你所用平台所支持的完整格式代码集，请参阅 _[strftime(3)](https://manpages.debian.org/strftime\(3\))_ 文档。不同的平台在处理不支持的格式指定符方面也有差异。

在 3.6 版本加入: 增加了 `%G`, `%u` 和 `%V`。

在 3.12 版本加入: 增加了 `%:z`。

### 技术细节¶

总体而言，`d.strftime(fmt)` 类似于 [`time`](time.md#module-time "time: Time access and conversions.") 模块的 `time.strftime(fmt, d.timetuple())`，但是并非所有对象都支持 `timetuple()` 方法。

对于 `datetime.strptime()` 类方法，默认值为 `1900-01-01T00:00:00.000`: 任何未在格式字符串中指定的部分都将从默认值中提取。 [4]

使用 `datetime.strptime(date_string, format)` 等价于:

    
    
~~~
datetime(*(time.strptime(date_string, format)[0:6]))
~~~

除非格式中包含秒以下的部分或时区差值信息，它们在 `datetime.strptime` 中受支持但会被 `time.strptime` 所丢弃。

对于 `time` 对象，年、月、日的格式代码不应被使用，因为 [`time`](time.md#module-time "time: Time access and conversions.") 对象没有这些值。 如果它们被使用，则年份将被替换为 `1900`，而月和日将被替换为 `1`。

对于 `date` 对象，时、分、秒和微秒的格式代码不应被使用，因为 `date` 对象没有这些值。 如果它们被使用，则它们都将被替换为 `0`。

出于相同的原因，对于包含当前区域设置字符集所无法表示的 Unicode 码位的格式字符串的处理方式也取决于具体平台。 在某些平台上这样的码位会不加修改地原样输出，而在其他平台上 `strftime` 则可能引发 [`UnicodeError`](exceptions.md#UnicodeError "UnicodeError") 或只返回一个空字符串。

注释：

  1. 因为该格式依赖于当前语言区域，所以在假定输出值时应当仔细考虑。 字段顺序可能会有变化（例如 "month/day/year" 和 "day/month/year"），并且输出还可能包含非 ASCII 字符。

  2. `strptime()` 方法能够解析整个 [1, 9999] 范围内的年份，但 < 1000 的年份必须加零填充为 4 位数字宽度。

在 3.2 版本发生变更: 在之前的版本中，`strftime()` 方法只限于 >= 1900 的年份。

在 3.3 版本发生变更: 在版本3.2中，`strftime()` 方法只限于 years >= 1000。

  3. 当与 `strptime()` 方法一起使用时，如果使用 `%I` 指令来解析小时，`%p` 指令只影响输出小时字段。

  4. 与 [`time`](time.md#module-time "time: Time access and conversions.") 模块不同的是， `datetime` 模块不支持闰秒。

  5. 当与 `strptime()` 方法一起使用时，`%f` 指令可接受一至六个数码及左边的零填充。 `%f` 是对 C 标准中格式字符集的扩展（但单独在 datetime 对象中实现，因此它总是可用）。

  6. 对于简单型对象，`%z`, `%:z` 和 `%Z` 格式代码会被替换为空字符串。

对于一个感知型对象而言：

`%z`

    

~~~
`utcoffset()` 会被转换为 `±HHMM[SS[.ffffff]]` 形式的字符串，其中 `HH` 为给出 UTC 时差的小时部分的 2 位数码字符串，`MM` 为给出 UTC 时差的分钟部分的 2 位数码字符串，`SS` 为给出 UTC 时差的秒部分的 2 位数码字符串，而 `ffffff` 为给出 UTC 时差的微秒部分的 6 位数码字符串。 当时差为整数秒时 `ffffff` 部分将被省略，而当时差为整数分钟时 `ffffff` 和 `SS` 部分都将被省略。 例如，如果 `utcoffset()` 返回 `timedelta(hours=-3, minutes=-30)`，则 `%z` 会被替换为字符串 `'-0330'`。

在 3.7 版本发生变更: UTC 时差不再限制为一个整数分钟值。

在 3.7 版本发生变更: 当提供 `%z` 指令给 `strptime()` 方法时，UTC 差值可以在时、分和秒之间使用冒号分隔符。 例如，`'+01:00:00'` 将被解读为一小时的差值。 此外，提供 `'Z'` 就相当于 `'+00:00'`。

`%:z`
~~~
    

~~~
行为与 `%z` 相同，但在时，分和秒之间有冒号分隔符。

`%Z`
~~~
    

~~~
