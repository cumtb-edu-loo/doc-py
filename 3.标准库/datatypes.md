# 数据类型¶

本章所描述的模块提供了许多专门的数据类型，如日期和时间、固定类型的数组、堆队列、双端队列、以及枚举。

Python也提供一些内置数据类型，特别是，[`dict`](stdtypes.md#dict "dict")、 [`list`](stdtypes.md#list "list")、[`set`](stdtypes.md#set "set")、[`frozenset`](stdtypes.md#frozenset "frozenset")、以及 [`tuple`](stdtypes.md#tuple "tuple")。[`str`](stdtypes.md#str "str") 这个类是用来存储Unicode字符串的，而 [`bytes`](stdtypes.md#bytes "bytes") 和 [`bytearray`](stdtypes.md#bytearray "bytearray") 这两个类是用来存储二进制数据的。

本章包含以下模块的文档：

  * [`datetime` \--- 基本日期和时间类型](3.标准库/datetime.md)
    * [感知型对象和简单型对象](3.标准库/datetime.md#aware-and-naive-objects)
    * [常量](3.标准库/datetime.md#constants)
      * [`MINYEAR`](3.标准库/datetime.md#datetime.MINYEAR)
      * [`MAXYEAR`](3.标准库/datetime.md#datetime.MAXYEAR)
      * [`UTC`](3.标准库/datetime.md#datetime.UTC)
    * [有效的类型](3.标准库/datetime.md#available-types)
      * [通用的特征属性](3.标准库/datetime.md#common-properties)
      * [确定一个对象是感知型还是简单型](3.标准库/datetime.md#determining-if-an-object-is-aware-or-naive)
    * [`timedelta` 类对象](3.标准库/datetime.md#timedelta-objects)
      * [`timedelta`](3.标准库/datetime.md#datetime.timedelta)
        * [`timedelta.min`](3.标准库/datetime.md#datetime.timedelta.min)
        * [`timedelta.max`](3.标准库/datetime.md#datetime.timedelta.max)
        * [`timedelta.resolution`](3.标准库/datetime.md#datetime.timedelta.resolution)
        * [`timedelta.total_seconds()`](3.标准库/datetime.md#datetime.timedelta.total_seconds)
      * [`timedelta` 用法示例](3.标准库/datetime.md#examples-of-usage-timedelta)
    * [`date` 对象](3.标准库/datetime.md#date-objects)
      * [`date`](3.标准库/datetime.md#datetime.date)
        * [`date.today()`](3.标准库/datetime.md#datetime.date.today)
        * [`date.fromtimestamp()`](3.标准库/datetime.md#datetime.date.fromtimestamp)
        * [`date.fromordinal()`](3.标准库/datetime.md#datetime.date.fromordinal)
        * [`date.fromisoformat()`](3.标准库/datetime.md#datetime.date.fromisoformat)
        * [`date.fromisocalendar()`](3.标准库/datetime.md#datetime.date.fromisocalendar)
        * [`date.min`](3.标准库/datetime.md#datetime.date.min)
        * [`date.max`](3.标准库/datetime.md#datetime.date.max)
        * [`date.resolution`](3.标准库/datetime.md#datetime.date.resolution)
        * [`date.year`](3.标准库/datetime.md#datetime.date.year)
        * [`date.month`](3.标准库/datetime.md#datetime.date.month)
        * [`date.day`](3.标准库/datetime.md#datetime.date.day)
        * [`date.replace()`](3.标准库/datetime.md#datetime.date.replace)
        * [`date.timetuple()`](3.标准库/datetime.md#datetime.date.timetuple)
        * [`date.toordinal()`](3.标准库/datetime.md#datetime.date.toordinal)
        * [`date.weekday()`](3.标准库/datetime.md#datetime.date.weekday)
        * [`date.isoweekday()`](3.标准库/datetime.md#datetime.date.isoweekday)
        * [`date.isocalendar()`](3.标准库/datetime.md#datetime.date.isocalendar)
        * [`date.isoformat()`](3.标准库/datetime.md#datetime.date.isoformat)
        * [`date.__str__()`](3.标准库/datetime.md#datetime.date.__str__)
        * [`date.ctime()`](3.标准库/datetime.md#datetime.date.ctime)
        * [`date.strftime()`](3.标准库/datetime.md#datetime.date.strftime)
        * [`date.__format__()`](3.标准库/datetime.md#datetime.date.__format__)
      * [`date` 用法示例](3.标准库/datetime.md#examples-of-usage-date)
    * [`datetime` 对象](3.标准库/datetime.md#datetime-objects)
      * [`datetime`](3.标准库/datetime.md#datetime.datetime)
        * [`datetime.today()`](3.标准库/datetime.md#datetime.datetime.today)
        * [`datetime.now()`](3.标准库/datetime.md#datetime.datetime.now)
        * [`datetime.utcnow()`](3.标准库/datetime.md#datetime.datetime.utcnow)
        * [`datetime.fromtimestamp()`](3.标准库/datetime.md#datetime.datetime.fromtimestamp)
        * [`datetime.utcfromtimestamp()`](3.标准库/datetime.md#datetime.datetime.utcfromtimestamp)
        * [`datetime.fromordinal()`](3.标准库/datetime.md#datetime.datetime.fromordinal)
        * [`datetime.combine()`](3.标准库/datetime.md#datetime.datetime.combine)
        * [`datetime.fromisoformat()`](3.标准库/datetime.md#datetime.datetime.fromisoformat)
        * [`datetime.fromisocalendar()`](3.标准库/datetime.md#datetime.datetime.fromisocalendar)
        * [`datetime.strptime()`](3.标准库/datetime.md#datetime.datetime.strptime)
        * [`datetime.min`](3.标准库/datetime.md#datetime.datetime.min)
        * [`datetime.max`](3.标准库/datetime.md#datetime.datetime.max)
        * [`datetime.resolution`](3.标准库/datetime.md#datetime.datetime.resolution)
        * [`datetime.year`](3.标准库/datetime.md#datetime.datetime.year)
        * [`datetime.month`](3.标准库/datetime.md#datetime.datetime.month)
        * [`datetime.day`](3.标准库/datetime.md#datetime.datetime.day)
        * [`datetime.hour`](3.标准库/datetime.md#datetime.datetime.hour)
        * [`datetime.minute`](3.标准库/datetime.md#datetime.datetime.minute)
        * [`datetime.second`](3.标准库/datetime.md#datetime.datetime.second)
        * [`datetime.microsecond`](3.标准库/datetime.md#datetime.datetime.microsecond)
        * [`datetime.tzinfo`](3.标准库/datetime.md#datetime.datetime.tzinfo)
        * [`datetime.fold`](3.标准库/datetime.md#datetime.datetime.fold)
        * [`datetime.date()`](3.标准库/datetime.md#datetime.datetime.date)
        * [`datetime.time()`](3.标准库/datetime.md#datetime.datetime.time)
        * [`datetime.timetz()`](3.标准库/datetime.md#datetime.datetime.timetz)
        * [`datetime.replace()`](3.标准库/datetime.md#datetime.datetime.replace)
        * [`datetime.astimezone()`](3.标准库/datetime.md#datetime.datetime.astimezone)
        * [`datetime.utcoffset()`](3.标准库/datetime.md#datetime.datetime.utcoffset)
        * [`datetime.dst()`](3.标准库/datetime.md#datetime.datetime.dst)
        * [`datetime.tzname()`](3.标准库/datetime.md#datetime.datetime.tzname)
        * [`datetime.timetuple()`](3.标准库/datetime.md#datetime.datetime.timetuple)
        * [`datetime.utctimetuple()`](3.标准库/datetime.md#datetime.datetime.utctimetuple)
        * [`datetime.toordinal()`](3.标准库/datetime.md#datetime.datetime.toordinal)
        * [`datetime.timestamp()`](3.标准库/datetime.md#datetime.datetime.timestamp)
        * [`datetime.weekday()`](3.标准库/datetime.md#datetime.datetime.weekday)
        * [`datetime.isoweekday()`](3.标准库/datetime.md#datetime.datetime.isoweekday)
        * [`datetime.isocalendar()`](3.标准库/datetime.md#datetime.datetime.isocalendar)
        * [`datetime.isoformat()`](3.标准库/datetime.md#datetime.datetime.isoformat)
        * [`datetime.__str__()`](3.标准库/datetime.md#datetime.datetime.__str__)
        * [`datetime.ctime()`](3.标准库/datetime.md#datetime.datetime.ctime)
        * [`datetime.strftime()`](3.标准库/datetime.md#datetime.datetime.strftime)
        * [`datetime.__format__()`](3.标准库/datetime.md#datetime.datetime.__format__)
      * [用法示例: `datetime`](3.标准库/datetime.md#examples-of-usage-datetime)
    * [`time` 对象](3.标准库/datetime.md#time-objects)
      * [`time`](3.标准库/datetime.md#datetime.time)
        * [`time.min`](3.标准库/datetime.md#datetime.time.min)
        * [`time.max`](3.标准库/datetime.md#datetime.time.max)
        * [`time.resolution`](3.标准库/datetime.md#datetime.time.resolution)
        * [`time.hour`](3.标准库/datetime.md#datetime.time.hour)
        * [`time.minute`](3.标准库/datetime.md#datetime.time.minute)
        * [`time.second`](3.标准库/datetime.md#datetime.time.second)
        * [`time.microsecond`](3.标准库/datetime.md#datetime.time.microsecond)
        * [`time.tzinfo`](3.标准库/datetime.md#datetime.time.tzinfo)
        * [`time.fold`](3.标准库/datetime.md#datetime.time.fold)
        * [`time.fromisoformat()`](3.标准库/datetime.md#datetime.time.fromisoformat)
        * [`time.replace()`](3.标准库/datetime.md#datetime.time.replace)
        * [`time.isoformat()`](3.标准库/datetime.md#datetime.time.isoformat)
        * [`time.__str__()`](3.标准库/datetime.md#datetime.time.__str__)
        * [`time.strftime()`](3.标准库/datetime.md#datetime.time.strftime)
        * [`time.__format__()`](3.标准库/datetime.md#datetime.time.__format__)
        * [`time.utcoffset()`](3.标准库/datetime.md#datetime.time.utcoffset)
        * [`time.dst()`](3.标准库/datetime.md#datetime.time.dst)
        * [`time.tzname()`](3.标准库/datetime.md#datetime.time.tzname)
      * [用法示例: `time`](3.标准库/datetime.md#examples-of-usage-time)
    * [`tzinfo` 对象](3.标准库/datetime.md#tzinfo-objects)
      * [`tzinfo`](3.标准库/datetime.md#datetime.tzinfo)
        * [`tzinfo.utcoffset()`](3.标准库/datetime.md#datetime.tzinfo.utcoffset)
        * [`tzinfo.dst()`](3.标准库/datetime.md#datetime.tzinfo.dst)
        * [`tzinfo.tzname()`](3.标准库/datetime.md#datetime.tzinfo.tzname)
        * [`tzinfo.fromutc()`](3.标准库/datetime.md#datetime.tzinfo.fromutc)
    * [`timezone` 对象](3.标准库/datetime.md#timezone-objects)
      * [`timezone`](3.标准库/datetime.md#datetime.timezone)
        * [`timezone.utcoffset()`](3.标准库/datetime.md#datetime.timezone.utcoffset)
        * [`timezone.tzname()`](3.标准库/datetime.md#datetime.timezone.tzname)
        * [`timezone.dst()`](3.标准库/datetime.md#datetime.timezone.dst)
        * [`timezone.fromutc()`](3.标准库/datetime.md#datetime.timezone.fromutc)
        * [`timezone.utc`](3.标准库/datetime.md#datetime.timezone.utc)
    * [`strftime()` 和 `strptime()` 的行为](3.标准库/datetime.md#strftime-and-strptime-behavior)
      * [`strftime()` 和 `strptime()` Format Codes](3.标准库/datetime.md#strftime-and-strptime-format-codes)
      * [技术细节](3.标准库/datetime.md#technical-detail)
  * [`zoneinfo` \--- IANA 时区支持](zoneinfo.md)
    * [使用 `ZoneInfo`](zoneinfo.md#using-zoneinfo)
    * [数据源](zoneinfo.md#data-sources)
      * [配置数据源](zoneinfo.md#configuring-the-data-sources)
        * [编译时配置](zoneinfo.md#compile-time-configuration)
        * [环境配置](zoneinfo.md#environment-configuration)
        * [运行时配置](zoneinfo.md#runtime-configuration)
    * [`ZoneInfo` 类](zoneinfo.md#the-zoneinfo-class)
      * [`ZoneInfo`](zoneinfo.md#zoneinfo.ZoneInfo)
        * [`ZoneInfo.from_file()`](zoneinfo.md#zoneinfo.ZoneInfo.from_file)
        * [`ZoneInfo.no_cache()`](zoneinfo.md#zoneinfo.ZoneInfo.no_cache)
        * [`ZoneInfo.clear_cache()`](zoneinfo.md#zoneinfo.ZoneInfo.clear_cache)
        * [`ZoneInfo.key`](zoneinfo.md#zoneinfo.ZoneInfo.key)
      * [字符串表示](zoneinfo.md#string-representations)
      * [封存序列化](zoneinfo.md#pickle-serialization)
    * [函数](zoneinfo.md#functions)
      * [`available_timezones()`](zoneinfo.md#zoneinfo.available_timezones)
      * [`reset_tzpath()`](zoneinfo.md#zoneinfo.reset_tzpath)
    * [全局变量](zoneinfo.md#globals)
      * [`TZPATH`](zoneinfo.md#zoneinfo.TZPATH)
    * [异常与警告](zoneinfo.md#exceptions-and-warnings)
      * [`ZoneInfoNotFoundError`](zoneinfo.md#zoneinfo.ZoneInfoNotFoundError)
      * [`InvalidTZPathWarning`](zoneinfo.md#zoneinfo.InvalidTZPathWarning)
  * [`calendar` \--- 日历相关函数](calendar.md)
    * [`Calendar`](calendar.md#calendar.Calendar)
      * [`Calendar.iterweekdays()`](calendar.md#calendar.Calendar.iterweekdays)
      * [`Calendar.itermonthdates()`](calendar.md#calendar.Calendar.itermonthdates)
      * [`Calendar.itermonthdays()`](calendar.md#calendar.Calendar.itermonthdays)
      * [`Calendar.itermonthdays2()`](calendar.md#calendar.Calendar.itermonthdays2)
      * [`Calendar.itermonthdays3()`](calendar.md#calendar.Calendar.itermonthdays3)
      * [`Calendar.itermonthdays4()`](calendar.md#calendar.Calendar.itermonthdays4)
      * [`Calendar.monthdatescalendar()`](calendar.md#calendar.Calendar.monthdatescalendar)
      * [`Calendar.monthdays2calendar()`](calendar.md#calendar.Calendar.monthdays2calendar)
      * [`Calendar.monthdayscalendar()`](calendar.md#calendar.Calendar.monthdayscalendar)
      * [`Calendar.yeardatescalendar()`](calendar.md#calendar.Calendar.yeardatescalendar)
      * [`Calendar.yeardays2calendar()`](calendar.md#calendar.Calendar.yeardays2calendar)
      * [`Calendar.yeardayscalendar()`](calendar.md#calendar.Calendar.yeardayscalendar)
    * [`TextCalendar`](calendar.md#calendar.TextCalendar)
      * [`TextCalendar.formatmonth()`](calendar.md#calendar.TextCalendar.formatmonth)
      * [`TextCalendar.prmonth()`](calendar.md#calendar.TextCalendar.prmonth)
      * [`TextCalendar.formatyear()`](calendar.md#calendar.TextCalendar.formatyear)
      * [`TextCalendar.pryear()`](calendar.md#calendar.TextCalendar.pryear)
    * [`HTMLCalendar`](calendar.md#calendar.HTMLCalendar)
      * [`HTMLCalendar.formatmonth()`](calendar.md#calendar.HTMLCalendar.formatmonth)
      * [`HTMLCalendar.formatyear()`](calendar.md#calendar.HTMLCalendar.formatyear)
      * [`HTMLCalendar.formatyearpage()`](calendar.md#calendar.HTMLCalendar.formatyearpage)
      * [`HTMLCalendar.cssclasses`](calendar.md#calendar.HTMLCalendar.cssclasses)
      * [`HTMLCalendar.cssclass_noday`](calendar.md#calendar.HTMLCalendar.cssclass_noday)
      * [`HTMLCalendar.cssclasses_weekday_head`](calendar.md#calendar.HTMLCalendar.cssclasses_weekday_head)
      * [`HTMLCalendar.cssclass_month_head`](calendar.md#calendar.HTMLCalendar.cssclass_month_head)
      * [`HTMLCalendar.cssclass_month`](calendar.md#calendar.HTMLCalendar.cssclass_month)
      * [`HTMLCalendar.cssclass_year`](calendar.md#calendar.HTMLCalendar.cssclass_year)
      * [`HTMLCalendar.cssclass_year_head`](calendar.md#calendar.HTMLCalendar.cssclass_year_head)
    * [`LocaleTextCalendar`](calendar.md#calendar.LocaleTextCalendar)
    * [`LocaleHTMLCalendar`](calendar.md#calendar.LocaleHTMLCalendar)
    * [`setfirstweekday()`](calendar.md#calendar.setfirstweekday)
    * [`firstweekday()`](calendar.md#calendar.firstweekday)
    * [`isleap()`](calendar.md#calendar.isleap)
    * [`leapdays()`](calendar.md#calendar.leapdays)
    * [`weekday()`](calendar.md#calendar.weekday)
    * [`weekheader()`](calendar.md#calendar.weekheader)
    * [`monthrange()`](calendar.md#calendar.monthrange)
    * [`monthcalendar()`](calendar.md#calendar.monthcalendar)
    * [`prmonth()`](calendar.md#calendar.prmonth)
    * [`month()`](calendar.md#calendar.month)
    * [`prcal()`](calendar.md#calendar.prcal)
    * [`calendar()`](calendar.md#calendar.calendar)
    * [`timegm()`](calendar.md#calendar.timegm)
    * [`day_name`](calendar.md#calendar.day_name)
    * [`day_abbr`](calendar.md#calendar.day_abbr)
    * [`MONDAY`](calendar.md#calendar.MONDAY)
    * [`TUESDAY`](calendar.md#calendar.TUESDAY)
    * [`WEDNESDAY`](calendar.md#calendar.WEDNESDAY)
    * [`THURSDAY`](calendar.md#calendar.THURSDAY)
    * [`FRIDAY`](calendar.md#calendar.FRIDAY)
    * [`SATURDAY`](calendar.md#calendar.SATURDAY)
    * [`SUNDAY`](calendar.md#calendar.SUNDAY)
    * [`Day`](calendar.md#calendar.Day)
    * [`month_name`](calendar.md#calendar.month_name)
    * [`month_abbr`](calendar.md#calendar.month_abbr)
    * [`JANUARY`](calendar.md#calendar.JANUARY)
    * [`FEBRUARY`](calendar.md#calendar.FEBRUARY)
    * [`MARCH`](calendar.md#calendar.MARCH)
    * [`APRIL`](calendar.md#calendar.APRIL)
    * [`MAY`](calendar.md#calendar.MAY)
    * [`JUNE`](calendar.md#calendar.JUNE)
    * [`JULY`](calendar.md#calendar.JULY)
    * [`AUGUST`](calendar.md#calendar.AUGUST)
    * [`SEPTEMBER`](calendar.md#calendar.SEPTEMBER)
    * [`OCTOBER`](calendar.md#calendar.OCTOBER)
    * [`NOVEMBER`](calendar.md#calendar.NOVEMBER)
    * [`DECEMBER`](calendar.md#calendar.DECEMBER)
    * [`Month`](calendar.md#calendar.Month)
    * [`IllegalMonthError`](calendar.md#calendar.IllegalMonthError)
      * [`IllegalMonthError.month`](calendar.md#calendar.IllegalMonthError.month)
    * [`IllegalWeekdayError`](calendar.md#calendar.IllegalWeekdayError)
      * [`IllegalWeekdayError.weekday`](calendar.md#calendar.IllegalWeekdayError.weekday)
    * [命令行用法](calendar.md#command-line-usage)
  * [`collections` \--- 容器数据类型](collections.md)
    * [`ChainMap` 对象](collections.md#chainmap-objects)
      * [`ChainMap`](collections.md#collections.ChainMap)
        * [`ChainMap.maps`](collections.md#collections.ChainMap.maps)
        * [`ChainMap.new_child()`](collections.md#collections.ChainMap.new_child)
        * [`ChainMap.parents`](collections.md#collections.ChainMap.parents)
      * [`ChainMap` 例子和方法](collections.md#chainmap-examples-and-recipes)
    * [`Counter` 对象](collections.md#counter-objects)
      * [`Counter`](collections.md#collections.Counter)
        * [`Counter.elements()`](collections.md#collections.Counter.elements)
        * [`Counter.most_common()`](collections.md#collections.Counter.most_common)
        * [`Counter.subtract()`](collections.md#collections.Counter.subtract)
        * [`Counter.total()`](collections.md#collections.Counter.total)
        * [`Counter.fromkeys()`](collections.md#collections.Counter.fromkeys)
        * [`Counter.update()`](collections.md#collections.Counter.update)
    * [`deque` 对象](collections.md#deque-objects)
      * [`deque`](collections.md#collections.deque)
        * [`deque.append()`](collections.md#collections.deque.append)
        * [`deque.appendleft()`](collections.md#collections.deque.appendleft)
        * [`deque.clear()`](collections.md#collections.deque.clear)
        * [`deque.copy()`](collections.md#collections.deque.copy)
        * [`deque.count()`](collections.md#collections.deque.count)
        * [`deque.extend()`](collections.md#collections.deque.extend)
        * [`deque.extendleft()`](collections.md#collections.deque.extendleft)
        * [`deque.index()`](collections.md#collections.deque.index)
        * [`deque.insert()`](collections.md#collections.deque.insert)
        * [`deque.pop()`](collections.md#collections.deque.pop)
        * [`deque.popleft()`](collections.md#collections.deque.popleft)
        * [`deque.remove()`](collections.md#collections.deque.remove)
        * [`deque.reverse()`](collections.md#collections.deque.reverse)
        * [`deque.rotate()`](collections.md#collections.deque.rotate)
        * [`deque.maxlen`](collections.md#collections.deque.maxlen)
      * [`deque` 用法](collections.md#deque-recipes)
    * [`defaultdict` 对象](collections.md#defaultdict-objects)
      * [`defaultdict`](collections.md#collections.defaultdict)
        * [`defaultdict.__missing__()`](collections.md#collections.defaultdict.__missing__)
        * [`defaultdict.default_factory`](collections.md#collections.defaultdict.default_factory)
      * [`defaultdict` 例子](collections.md#defaultdict-examples)
    * [`namedtuple()` 命名元组的工厂函数](collections.md#namedtuple-factory-function-for-tuples-with-named-fields)
      * [`namedtuple()`](collections.md#collections.namedtuple)
      * [`somenamedtuple._make()`](collections.md#collections.somenamedtuple._make)
      * [`somenamedtuple._asdict()`](collections.md#collections.somenamedtuple._asdict)
      * [`somenamedtuple._replace()`](collections.md#collections.somenamedtuple._replace)
      * [`somenamedtuple._fields`](collections.md#collections.somenamedtuple._fields)
      * [`somenamedtuple._field_defaults`](collections.md#collections.somenamedtuple._field_defaults)
    * [`OrderedDict` 对象](collections.md#ordereddict-objects)
      * [`OrderedDict`](collections.md#collections.OrderedDict)
        * [`OrderedDict.popitem()`](collections.md#collections.OrderedDict.popitem)
        * [`OrderedDict.move_to_end()`](collections.md#collections.OrderedDict.move_to_end)
      * [`OrderedDict` 例子和用法](collections.md#ordereddict-examples-and-recipes)
    * [`UserDict` 对象](collections.md#userdict-objects)
      * [`UserDict`](collections.md#collections.UserDict)
        * [`UserDict.data`](collections.md#collections.UserDict.data)
    * [`UserList` 对象](collections.md#userlist-objects)
      * [`UserList`](collections.md#collections.UserList)
        * [`UserList.data`](collections.md#collections.UserList.data)
    * [`UserString` 对象](collections.md#userstring-objects)
      * [`UserString`](collections.md#collections.UserString)
        * [`UserString.data`](collections.md#collections.UserString.data)
  * [`collections.abc` \--- 容器的抽象基类](collections.abc.md)
    * [容器抽象基类](collections.abc.md#collections-abstract-base-classes)
    * [多项集抽象基类 -- 详细描述](collections.abc.md#collections-abstract-base-classes-detailed-descriptions)
      * [`Container`](collections.abc.md#collections.abc.Container)
      * [`Hashable`](collections.abc.md#collections.abc.Hashable)
      * [`Sized`](collections.abc.md#collections.abc.Sized)
      * [`Callable`](collections.abc.md#collections.abc.Callable)
      * [`Iterable`](collections.abc.md#collections.abc.Iterable)
      * [`Collection`](collections.abc.md#collections.abc.Collection)
      * [`Iterator`](collections.abc.md#collections.abc.Iterator)
      * [`Reversible`](collections.abc.md#collections.abc.Reversible)
      * [`Generator`](collections.abc.md#collections.abc.Generator)
      * [`Sequence`](collections.abc.md#collections.abc.Sequence)
      * [`MutableSequence`](collections.abc.md#collections.abc.MutableSequence)
      * [`ByteString`](collections.abc.md#collections.abc.ByteString)
      * [`Set`](collections.abc.md#collections.abc.Set)
      * [`MutableSet`](collections.abc.md#collections.abc.MutableSet)
      * [`Mapping`](collections.abc.md#collections.abc.Mapping)
      * [`MutableMapping`](collections.abc.md#collections.abc.MutableMapping)
      * [`MappingView`](collections.abc.md#collections.abc.MappingView)
      * [`ItemsView`](collections.abc.md#collections.abc.ItemsView)
      * [`KeysView`](collections.abc.md#collections.abc.KeysView)
      * [`ValuesView`](collections.abc.md#collections.abc.ValuesView)
      * [`Awaitable`](collections.abc.md#collections.abc.Awaitable)
      * [`Coroutine`](collections.abc.md#collections.abc.Coroutine)
      * [`AsyncIterable`](collections.abc.md#collections.abc.AsyncIterable)
      * [`AsyncIterator`](collections.abc.md#collections.abc.AsyncIterator)
      * [`AsyncGenerator`](collections.abc.md#collections.abc.AsyncGenerator)
      * [`Buffer`](collections.abc.md#collections.abc.Buffer)
    * [例子和配方](collections.abc.md#examples-and-recipes)
  * [`heapq` \--- 堆队列算法](heapq.md)
    * [`heappush()`](heapq.md#heapq.heappush)
    * [`heappop()`](heapq.md#heapq.heappop)
    * [`heappushpop()`](heapq.md#heapq.heappushpop)
    * [`heapify()`](heapq.md#heapq.heapify)
    * [`heapreplace()`](heapq.md#heapq.heapreplace)
    * [`merge()`](heapq.md#heapq.merge)
    * [`nlargest()`](heapq.md#heapq.nlargest)
    * [`nsmallest()`](heapq.md#heapq.nsmallest)
    * [基本示例](heapq.md#basic-examples)
    * [优先队列实现说明](heapq.md#priority-queue-implementation-notes)
    * [理论](heapq.md#theory)
  * [`bisect` \--- 数组二分查找算法](bisect.md)
    * [`bisect_left()`](bisect.md#bisect.bisect_left)
    * [`bisect_right()`](bisect.md#bisect.bisect_right)
    * [`bisect()`](bisect.md#bisect.bisect)
    * [`insort_left()`](bisect.md#bisect.insort_left)
    * [`insort_right()`](bisect.md#bisect.insort_right)
    * [`insort()`](bisect.md#bisect.insort)
    * [性能说明](bisect.md#performance-notes)
    * [搜索有序列表](bisect.md#searching-sorted-lists)
    * [例子](bisect.md#examples)
  * [`array` \--- 高效的数字数组](array.md)
    * [`typecodes`](array.md#array.typecodes)
    * [`array`](array.md#array.array)
      * [`array.typecode`](array.md#array.array.typecode)
      * [`array.itemsize`](array.md#array.array.itemsize)
      * [`array.append()`](array.md#array.array.append)
      * [`array.buffer_info()`](array.md#array.array.buffer_info)
      * [`array.byteswap()`](array.md#array.array.byteswap)
      * [`array.count()`](array.md#array.array.count)
      * [`array.extend()`](array.md#array.array.extend)
      * [`array.frombytes()`](array.md#array.array.frombytes)
      * [`array.fromfile()`](array.md#array.array.fromfile)
      * [`array.fromlist()`](array.md#array.array.fromlist)
      * [`array.fromunicode()`](array.md#array.array.fromunicode)
      * [`array.index()`](array.md#array.array.index)
      * [`array.insert()`](array.md#array.array.insert)
      * [`array.pop()`](array.md#array.array.pop)
      * [`array.remove()`](array.md#array.array.remove)
      * [`array.reverse()`](array.md#array.array.reverse)
      * [`array.tobytes()`](array.md#array.array.tobytes)
      * [`array.tofile()`](array.md#array.array.tofile)
      * [`array.tolist()`](array.md#array.array.tolist)
      * [`array.tounicode()`](array.md#array.array.tounicode)
  * [`weakref` \--- 弱引用](3.标准库/weakref.md)
    * [`ref`](3.标准库/weakref.md#weakref.ref)
      * [`ref.__callback__`](3.标准库/weakref.md#weakref.ref.__callback__)
    * [`proxy()`](3.标准库/weakref.md#weakref.proxy)
    * [`getweakrefcount()`](3.标准库/weakref.md#weakref.getweakrefcount)
    * [`getweakrefs()`](3.标准库/weakref.md#weakref.getweakrefs)
    * [`WeakKeyDictionary`](3.标准库/weakref.md#weakref.WeakKeyDictionary)
      * [`WeakKeyDictionary.keyrefs()`](3.标准库/weakref.md#weakref.WeakKeyDictionary.keyrefs)
    * [`WeakValueDictionary`](3.标准库/weakref.md#weakref.WeakValueDictionary)
      * [`WeakValueDictionary.valuerefs()`](3.标准库/weakref.md#weakref.WeakValueDictionary.valuerefs)
    * [`WeakSet`](3.标准库/weakref.md#weakref.WeakSet)
    * [`WeakMethod`](3.标准库/weakref.md#weakref.WeakMethod)
    * [`finalize`](3.标准库/weakref.md#weakref.finalize)
      * [`finalize.__call__()`](3.标准库/weakref.md#weakref.finalize.__call__)
      * [`finalize.detach()`](3.标准库/weakref.md#weakref.finalize.detach)
      * [`finalize.peek()`](3.标准库/weakref.md#weakref.finalize.peek)
      * [`finalize.alive`](3.标准库/weakref.md#weakref.finalize.alive)
      * [`finalize.atexit`](3.标准库/weakref.md#weakref.finalize.atexit)
    * [`ReferenceType`](3.标准库/weakref.md#weakref.ReferenceType)
    * [`ProxyType`](3.标准库/weakref.md#weakref.ProxyType)
    * [`CallableProxyType`](3.标准库/weakref.md#weakref.CallableProxyType)
    * [`ProxyTypes`](3.标准库/weakref.md#weakref.ProxyTypes)
    * [弱引用对象](3.标准库/weakref.md#weak-reference-objects)
    * [示例](3.标准库/weakref.md#example)
    * [终结器对象](3.标准库/weakref.md#finalizer-objects)
    * [比较终结器与 `__del__()` 方法](3.标准库/weakref.md#comparing-finalizers-with-del-methods)
  * [`types` \--- 动态类型创建和内置类型名称](types.md)
    * [动态类型创建](types.md#dynamic-type-creation)
      * [`new_class()`](types.md#types.new_class)
      * [`prepare_class()`](types.md#types.prepare_class)
      * [`resolve_bases()`](types.md#types.resolve_bases)
      * [`get_original_bases()`](types.md#types.get_original_bases)
    * [标准解释器类型](types.md#standard-interpreter-types)
      * [`NoneType`](types.md#types.NoneType)
      * [`FunctionType`](types.md#types.FunctionType)
      * [`LambdaType`](types.md#types.LambdaType)
      * [`GeneratorType`](types.md#types.GeneratorType)
      * [`CoroutineType`](types.md#types.CoroutineType)
      * [`AsyncGeneratorType`](types.md#types.AsyncGeneratorType)
      * [`CodeType`](types.md#types.CodeType)
        * [`CodeType.replace()`](types.md#types.CodeType.replace)
      * [`CellType`](types.md#types.CellType)
      * [`MethodType`](types.md#types.MethodType)
      * [`BuiltinFunctionType`](types.md#types.BuiltinFunctionType)
      * [`BuiltinMethodType`](types.md#types.BuiltinMethodType)
      * [`WrapperDescriptorType`](types.md#types.WrapperDescriptorType)
      * [`MethodWrapperType`](types.md#types.MethodWrapperType)
      * [`NotImplementedType`](types.md#types.NotImplementedType)
      * [`MethodDescriptorType`](types.md#types.MethodDescriptorType)
      * [`ClassMethodDescriptorType`](types.md#types.ClassMethodDescriptorType)
      * [`ModuleType`](types.md#types.ModuleType)
        * [`ModuleType.__doc__`](types.md#types.ModuleType.__doc__)
        * [`ModuleType.__loader__`](types.md#types.ModuleType.__loader__)
        * [`ModuleType.__name__`](types.md#types.ModuleType.__name__)
        * [`ModuleType.__package__`](types.md#types.ModuleType.__package__)
        * [`ModuleType.__spec__`](types.md#types.ModuleType.__spec__)
      * [`EllipsisType`](types.md#types.EllipsisType)
      * [`GenericAlias`](types.md#types.GenericAlias)
      * [`UnionType`](types.md#types.UnionType)
      * [`TracebackType`](types.md#types.TracebackType)
      * [`FrameType`](types.md#types.FrameType)
      * [`GetSetDescriptorType`](types.md#types.GetSetDescriptorType)
      * [`MemberDescriptorType`](types.md#types.MemberDescriptorType)
      * [`MappingProxyType`](types.md#types.MappingProxyType)
        * [`MappingProxyType.copy()`](types.md#types.MappingProxyType.copy)
        * [`MappingProxyType.get()`](types.md#types.MappingProxyType.get)
        * [`MappingProxyType.items()`](types.md#types.MappingProxyType.items)
        * [`MappingProxyType.keys()`](types.md#types.MappingProxyType.keys)
        * [`MappingProxyType.values()`](types.md#types.MappingProxyType.values)
    * [附加工具类和函数](types.md#additional-utility-classes-and-functions)
      * [`SimpleNamespace`](types.md#types.SimpleNamespace)
      * [`DynamicClassAttribute()`](types.md#types.DynamicClassAttribute)
    * [协程工具函数](types.md#coroutine-utility-functions)
      * [`coroutine()`](types.md#types.coroutine)
  * [`copy` \--- 浅层 (shallow) 和深层 (deep) 复制操作](copy.md)
    * [`copy()`](copy.md#copy.copy)
    * [`deepcopy()`](copy.md#copy.deepcopy)
    * [`replace()`](copy.md#copy.replace)
    * [`Error`](copy.md#copy.Error)
  * [`pprint` \--- 数据美化输出](pprint.md)
    * [`PrettyPrinter`](pprint.md#pprint.PrettyPrinter)
    * [`pformat()`](pprint.md#pprint.pformat)
    * [`pp()`](pprint.md#pprint.pp)
    * [`pprint()`](pprint.md#pprint.pprint)
    * [`isreadable()`](pprint.md#pprint.isreadable)
    * [`isrecursive()`](pprint.md#pprint.isrecursive)
    * [`saferepr()`](pprint.md#pprint.saferepr)
    * [PrettyPrinter 对象](pprint.md#prettyprinter-objects)
      * [`PrettyPrinter.pformat()`](pprint.md#pprint.PrettyPrinter.pformat)
      * [`PrettyPrinter.pprint()`](pprint.md#pprint.PrettyPrinter.pprint)
      * [`PrettyPrinter.isreadable()`](pprint.md#pprint.PrettyPrinter.isreadable)
      * [`PrettyPrinter.isrecursive()`](pprint.md#pprint.PrettyPrinter.isrecursive)
      * [`PrettyPrinter.format()`](pprint.md#pprint.PrettyPrinter.format)
    * [示例](pprint.md#example)
  * [`reprlib` \--- 另一种 `repr()` 实现](reprlib.md)
    * [`Repr`](reprlib.md#reprlib.Repr)
    * [`aRepr`](reprlib.md#reprlib.aRepr)
    * [`repr()`](reprlib.md#reprlib.repr)
    * [`recursive_repr()`](reprlib.md#reprlib.recursive_repr)
    * [Repr 对象](reprlib.md#repr-objects)
      * [`Repr.fillvalue`](reprlib.md#reprlib.Repr.fillvalue)
      * [`Repr.maxlevel`](reprlib.md#reprlib.Repr.maxlevel)
      * [`Repr.maxdict`](reprlib.md#reprlib.Repr.maxdict)
      * [`Repr.maxlist`](reprlib.md#reprlib.Repr.maxlist)
      * [`Repr.maxtuple`](reprlib.md#reprlib.Repr.maxtuple)
      * [`Repr.maxset`](reprlib.md#reprlib.Repr.maxset)
      * [`Repr.maxfrozenset`](reprlib.md#reprlib.Repr.maxfrozenset)
      * [`Repr.maxdeque`](reprlib.md#reprlib.Repr.maxdeque)
      * [`Repr.maxarray`](reprlib.md#reprlib.Repr.maxarray)
      * [`Repr.maxlong`](reprlib.md#reprlib.Repr.maxlong)
      * [`Repr.maxstring`](reprlib.md#reprlib.Repr.maxstring)
      * [`Repr.maxother`](reprlib.md#reprlib.Repr.maxother)
      * [`Repr.indent`](reprlib.md#reprlib.Repr.indent)
      * [`Repr.repr()`](reprlib.md#reprlib.Repr.repr)
      * [`Repr.repr1()`](reprlib.md#reprlib.Repr.repr1)
    * [子类化 Repr 对象](reprlib.md#subclassing-repr-objects)
  * [`enum` \--- 对枚举的支持](3.标准库/enum.md)
    * [模块内容](3.标准库/enum.md#module-contents)
    * [数据类型](3.标准库/enum.md#data-types)
      * [`EnumType`](3.标准库/enum.md#enum.EnumType)
        * [`EnumType.__call__()`](3.标准库/enum.md#enum.EnumType.__call__)
        * [`EnumType.__contains__()`](3.标准库/enum.md#enum.EnumType.__contains__)
        * [`EnumType.__dir__()`](3.标准库/enum.md#enum.EnumType.__dir__)
        * [`EnumType.__getitem__()`](3.标准库/enum.md#enum.EnumType.__getitem__)
        * [`EnumType.__iter__()`](3.标准库/enum.md#enum.EnumType.__iter__)
        * [`EnumType.__len__()`](3.标准库/enum.md#enum.EnumType.__len__)
        * [`EnumType.__reversed__()`](3.标准库/enum.md#enum.EnumType.__reversed__)
      * [`Enum`](3.标准库/enum.md#enum.Enum)
        * [`Enum.name`](3.标准库/enum.md#enum.Enum.name)
        * [`Enum.value`](3.标准库/enum.md#enum.Enum.value)
        * [`Enum._ignore_`](3.标准库/enum.md#enum.Enum._ignore_)
        * [`Enum.__dir__()`](3.标准库/enum.md#enum.Enum.__dir__)
        * [`Enum._generate_next_value_()`](3.标准库/enum.md#enum.Enum._generate_next_value_)
        * [`Enum.__init_subclass__()`](3.标准库/enum.md#enum.Enum.__init_subclass__)
        * [`Enum._missing_()`](3.标准库/enum.md#enum.Enum._missing_)
        * [`Enum.__repr__()`](3.标准库/enum.md#enum.Enum.__repr__)
        * [`Enum.__str__()`](3.标准库/enum.md#enum.Enum.__str__)
        * [`Enum.__format__()`](3.标准库/enum.md#enum.Enum.__format__)
      * [`IntEnum`](3.标准库/enum.md#enum.IntEnum)
      * [`StrEnum`](3.标准库/enum.md#enum.StrEnum)
      * [`Flag`](3.标准库/enum.md#enum.Flag)
        * [`Flag.__contains__()`](3.标准库/enum.md#enum.Flag.__contains__)
        * [`Flag.__or__()`](3.标准库/enum.md#enum.Flag.__or__)
        * [`Flag.__and__()`](3.标准库/enum.md#enum.Flag.__and__)
        * [`Flag.__xor__()`](3.标准库/enum.md#enum.Flag.__xor__)
        * [`Flag._numeric_repr_()`](3.标准库/enum.md#enum.Flag._numeric_repr_)
      * [`IntFlag`](3.标准库/enum.md#enum.IntFlag)
      * [`ReprEnum`](3.标准库/enum.md#enum.ReprEnum)
      * [`EnumCheck`](3.标准库/enum.md#enum.EnumCheck)
        * [`EnumCheck.UNIQUE`](3.标准库/enum.md#enum.EnumCheck.UNIQUE)
        * [`EnumCheck.CONTINUOUS`](3.标准库/enum.md#enum.EnumCheck.CONTINUOUS)
        * [`EnumCheck.NAMED_FLAGS`](3.标准库/enum.md#enum.EnumCheck.NAMED_FLAGS)
      * [`FlagBoundary`](3.标准库/enum.md#enum.FlagBoundary)
        * [`FlagBoundary.STRICT`](3.标准库/enum.md#enum.FlagBoundary.STRICT)
        * [`FlagBoundary.CONFORM`](3.标准库/enum.md#enum.FlagBoundary.CONFORM)
        * [`FlagBoundary.EJECT`](3.标准库/enum.md#enum.FlagBoundary.EJECT)
        * [`FlagBoundary.KEEP`](3.标准库/enum.md#enum.FlagBoundary.KEEP)
      * [支持的 `__dunder__` 名称](3.标准库/enum.md#supported-dunder-names)
      * [支持的 `_sunder_` 名称](3.标准库/enum.md#supported-sunder-names)
    * [工具与装饰器](3.标准库/enum.md#utilities-and-decorators)
      * [`auto`](3.标准库/enum.md#enum.auto)
      * [`property()`](3.标准库/enum.md#enum.property)
      * [`unique()`](3.标准库/enum.md#enum.unique)
      * [`verify()`](3.标准库/enum.md#enum.verify)
      * [`member()`](3.标准库/enum.md#enum.member)
      * [`nonmember()`](3.标准库/enum.md#enum.nonmember)
      * [`global_enum()`](3.标准库/enum.md#enum.global_enum)
      * [`show_flag_values()`](3.标准库/enum.md#enum.show_flag_values)
    * [备注](3.标准库/enum.md#notes)
  * [`graphlib` \--- 操作类似图的结构的功能](graphlib.md)
    * [`TopologicalSorter`](graphlib.md#graphlib.TopologicalSorter)
      * [`TopologicalSorter.add()`](graphlib.md#graphlib.TopologicalSorter.add)
      * [`TopologicalSorter.prepare()`](graphlib.md#graphlib.TopologicalSorter.prepare)
      * [`TopologicalSorter.is_active()`](graphlib.md#graphlib.TopologicalSorter.is_active)
      * [`TopologicalSorter.done()`](graphlib.md#graphlib.TopologicalSorter.done)
      * [`TopologicalSorter.get_ready()`](graphlib.md#graphlib.TopologicalSorter.get_ready)
      * [`TopologicalSorter.static_order()`](graphlib.md#graphlib.TopologicalSorter.static_order)
    * [异常](graphlib.md#exceptions)
      * [`CycleError`](graphlib.md#graphlib.CycleError)

