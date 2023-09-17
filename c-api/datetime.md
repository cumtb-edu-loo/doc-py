# DateTime 对象¶

[`datetime`](../library/datetime.md#module-datetime "datetime: Basic date and time types.") 模块提供了各种日期和时间对象。 在使用这些函数之前，必须在你的源代码中包含头文件 `datetime.h` (请注意此文件并未包括在 `Python.h` 中)，并且 `PyDateTime_IMPORT` 必须被发起调用，通常是作为模块初始化函数的一部分。 这个宏会将指向特定 C 结构体的指针放入一个静态变量 `PyDateTimeAPI` 中，它将被下列的宏所使用。

type PyDateTime_Date¶  

    

~~~
[`PyObject`](structures.md#c.PyObject "PyObject") 的这个子类型表示 Python 日期对象。

type PyDateTime_DateTime¶  
~~~
    

~~~
[`PyObject`](structures.md#c.PyObject "PyObject") 的这个子类型表示 Python 日期时间对象。

type PyDateTime_Time¶  
~~~
    

~~~
[`PyObject`](structures.md#c.PyObject "PyObject") 的这个子类型表示 Python 时间对象。

type PyDateTime_Delta¶  
~~~
    

~~~
[`PyObject`](structures.md#c.PyObject "PyObject") 的这个子类型表示两个日期时间值之间的差值。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyDateTime_DateType¶  
~~~
    

~~~
这个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的实例代表 Python 日期类型；它与 Python 层面的 [`datetime.date`](../library/datetime.md#datetime.date "datetime.date") 对象相同。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyDateTime_DateTimeType¶  
~~~
    

~~~
这个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的实例代表 Python 日期时间类型；它与 Python 层面的 [`datetime.datetime`](../library/datetime.md#datetime.datetime "datetime.datetime") 对象相同。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyDateTime_TimeType¶  
~~~
    

~~~
这个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的实例代表 Python 时间类型；它与 Python 层面的 [`datetime.time`](../library/datetime.md#datetime.time "datetime.time") 对象相同。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyDateTime_DeltaType¶  
~~~
    

~~~
这个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的实例是代表两个日期时间值之间差值的 Python 类型；它与 Python 层面的 [`datetime.timedelta`](../library/datetime.md#datetime.timedelta "datetime.timedelta") 对象相同。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyDateTime_TZInfoType¶  
~~~
    

~~~
这个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的实例代表 Python 时区信息类型；它与 Python 层面的 [`datetime.tzinfo`](../library/datetime.md#datetime.tzinfo "datetime.tzinfo") 对象相同。

宏访问UTC单例:

[PyObject](structures.md#c.PyObject "PyObject") *PyDateTime_TimeZone_UTC¶  
~~~
    

~~~
返回表示 UTC 的时区单例，与 [`datetime.timezone.utc`](../library/datetime.md#datetime.timezone.utc "datetime.timezone.utc") 为同一对象。

在 3.7 版本加入.

类型检查宏：

int PyDate_Check([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
如果 _ob_ 为 `PyDateTime_DateType` 类型或 `PyDateTime_DateType` 的某个子类型则返回真值。 _ob_ 不能为 `NULL`。 此函数总是会成功执行。

int PyDate_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
如果 _ob_ 为 `PyDateTime_DateType` 类型则返回真值。 _ob_ 不能为 `NULL`。 此函数总是会成功执行。

int PyDateTime_Check([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
如果 _ob_ 为 `PyDateTime_DateTimeType` 类型或 `PyDateTime_DateTimeType` 的某个子类型则返回真值。 _ob_ 不能为 `NULL`。 此函数总是会成功执行。

int PyDateTime_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
如果 _ob_ 为 `PyDateTime_DateTimeType` 类型则返回真值。 _ob_ 不能为 `NULL`。 此函数总是会成功执行。

int PyTime_Check([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
如果 _ob_ 为 `PyDateTime_TimeType` 类型或 `PyDateTime_TimeType` 的某个子类型则返回真值。 _ob_ 不能为 `NULL`。 此函数总是会成功执行。

int PyTime_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
如果 _ob_ 为 `PyDateTime_TimeType` 类型则返回真值。 _ob_ 不能为 `NULL`。 此函数总是会成功执行。

int PyDelta_Check([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
如果 _ob_ 为 `PyDateTime_DeltaType` 类型或 `PyDateTime_DeltaType` 的某个子类型则返回真值。 _ob_ 不能为 `NULL`。 此函数总是会成功执行。

int PyDelta_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
如果 _ob_ 为 `PyDateTime_DeltaType` 类型则返回真值。 _ob_ 不能为 `NULL`。 此函数总是会成功执行。

int PyTZInfo_Check([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
如果 _ob_ 为 `PyDateTime_TZInfoType` 类型或 `PyDateTime_TZInfoType` 的某个子类型则返回真值。 _ob_ 不能为 `NULL`。 此函数总是会成功执行。

int PyTZInfo_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
如果 _ob_ 为 `PyDateTime_TZInfoType` 类型则返回真值。 _ob_ 不能为 `NULL`。 此函数总是会成功执行。

用于创建对象的宏：

[PyObject](structures.md#c.PyObject "PyObject") *PyDate_FromDate(int year, int month, int day)¶  

    _返回值：新的引用。_

返回指定年、月、日的 [`datetime.date`](../library/datetime.md#datetime.date "datetime.date") 对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyDateTime_FromDateAndTime(int year, int month, int day, int hour, int minute, int second, int usecond)¶  

    _返回值：新的引用。_

返回具有指定 year, month, day, hour, minute, second 和 microsecond 属性的 [`datetime.datetime`](../library/datetime.md#datetime.datetime "datetime.datetime") 对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyDateTime_FromDateAndTimeAndFold(int year, int month, int day, int hour, int minute, int second, int usecond, int fold)¶  

    _返回值：新的引用。_

返回具有指定 year, month, day, hour, minute, second, microsecond 和 fold 属性的 [`datetime.datetime`](../library/datetime.md#datetime.datetime "datetime.datetime") 对象。

在 3.6 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyTime_FromTime(int hour, int minute, int second, int usecond)¶  

    _返回值：新的引用。_

返回具有指定 hour, minute, second and microsecond 属性的 [`datetime.time`](../library/datetime.md#datetime.time "datetime.time") 对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyTime_FromTimeAndFold(int hour, int minute, int second, int usecond, int fold)¶  

    _返回值：新的引用。_

返回具有指定 hour, minute, second, microsecond 和 fold 属性的 [`datetime.time`](../library/datetime.md#datetime.time "datetime.time") 对象。

在 3.6 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyDelta_FromDSU(int days, int seconds, int useconds)¶  

    _返回值：新的引用。_

返回代表给定天、秒和微秒数的 [`datetime.timedelta`](../library/datetime.md#datetime.timedelta "datetime.timedelta") 对象。 将执行正规化操作以使最终的微秒和秒数处在 [`datetime.timedelta`](../library/datetime.md#datetime.timedelta "datetime.timedelta") 对象的文档指明的区间之内。

[PyObject](structures.md#c.PyObject "PyObject") *PyTimeZone_FromOffset([PyObject](structures.md#c.PyObject "PyObject") *offset)¶  

    _返回值：新的引用。_

返回一个 [`datetime.timezone`](../library/datetime.md#datetime.timezone "datetime.timezone") 对象，该对象具有以 _offset_ 参数表示 的未命名固定时差。

在 3.7 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyTimeZone_FromOffsetAndName([PyObject](structures.md#c.PyObject "PyObject") *offset, [PyObject](structures.md#c.PyObject "PyObject") *name)¶  

    _返回值：新的引用。_

返回一个 [`datetime.timezone`](../library/datetime.md#datetime.timezone "datetime.timezone") 对象，该对象具有以 _offset_ 参数表示的固定时差和时区名称 _name_ 。

在 3.7 版本加入.

一些用来从日期对象中提取字段的宏。 参数必须是 `PyDateTime_Date` 包括其子类 (如 `PyDateTime_DateTime`) 的实例。 参数不能为 `NULL`，且不会检查类型:

int PyDateTime_GET_YEAR(PyDateTime_Date *o)¶  
~~~
    

~~~
以正整数的形式返回年份值。

int PyDateTime_GET_MONTH(PyDateTime_Date *o)¶  
~~~
    

~~~
返回月，从0到12的整数。

int PyDateTime_GET_DAY(PyDateTime_Date *o)¶  
~~~
    

~~~
返回日期，从0到31的整数。

一些用来从日期时间对象中提取字段的宏。 参数必须是 `PyDateTime_DateTime` 包括其子类的实例。 参数不能为 `NULL`，并且不会检查类型:

int PyDateTime_DATE_GET_HOUR(PyDateTime_DateTime *o)¶  
~~~
    

~~~
返回小时，从0到23的整数。

int PyDateTime_DATE_GET_MINUTE(PyDateTime_DateTime *o)¶  
~~~
    

~~~
返回分钟，从0到59的整数。

int PyDateTime_DATE_GET_SECOND(PyDateTime_DateTime *o)¶  
~~~
    

~~~
返回秒，从0到59的整数。

int PyDateTime_DATE_GET_MICROSECOND(PyDateTime_DateTime *o)¶  
~~~
    

~~~
返回微秒，从0到999999的整数。

int PyDateTime_DATE_GET_FOLD(PyDateTime_DateTime *o)¶  
~~~
    

~~~
返回折叠值，为整数 0 或 1。

在 3.6 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyDateTime_DATE_GET_TZINFO(PyDateTime_DateTime *o)¶  
~~~
    

~~~
返回 tzinfo (可以为 `None`)。

在 3.10 版本加入.

一些用来从时间对象中提取字段的宏。 参数必须是 `PyDateTime_Time` 包括其子类的实例。 参数不能为 `NULL`，且不会检查类型:

int PyDateTime_TIME_GET_HOUR(PyDateTime_Time *o)¶  
~~~
    

~~~
返回小时，从0到23的整数。

int PyDateTime_TIME_GET_MINUTE(PyDateTime_Time *o)¶  
~~~
    

~~~
返回分钟，从0到59的整数。

int PyDateTime_TIME_GET_SECOND(PyDateTime_Time *o)¶  
~~~
    

~~~
返回秒，从0到59的整数。

int PyDateTime_TIME_GET_MICROSECOND(PyDateTime_Time *o)¶  
~~~
    

~~~
返回微秒，从0到999999的整数。

int PyDateTime_TIME_GET_FOLD(PyDateTime_Time *o)¶  
~~~
    

~~~
返回折叠值，为整数 0 或 1。

在 3.6 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyDateTime_TIME_GET_TZINFO(PyDateTime_Time *o)¶  
~~~
    

~~~
返回 tzinfo (可以为 `None`)。

在 3.10 版本加入.

一些用来从时间差对象中提取字段的宏。 参数必须是 `PyDateTime_Delta` 包括其子类的实例。参数不能为 `NULL`，并且不会检查类型:

int PyDateTime_DELTA_GET_DAYS(PyDateTime_Delta *o)¶  
~~~
    

~~~
返回天数，从-999999999到999999999的整数。

在 3.3 版本加入.

int PyDateTime_DELTA_GET_SECONDS(PyDateTime_Delta *o)¶  
~~~
    

~~~
返回秒数，从0到86399的整数。

在 3.3 版本加入.

int PyDateTime_DELTA_GET_MICROSECONDS(PyDateTime_Delta *o)¶  
~~~
    

~~~
