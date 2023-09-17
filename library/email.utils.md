# `email.utils`: 其他工具¶

**源代码:** [Lib/email/utils.py](https://github.com/python/cpython/tree/3.12/Lib/email/utils.py)

* * *

`email.utils` 模块提供如下几个工具

email.utils.localtime( _dt =None_)¶

    

~~~
将当地时间作为感知型datetime 对象返回。 如果不带参数地调用，则返回当前时间。 否则 _dt_ 参数应为 [`datetime`](datetime.md#datetime.datetime "datetime.datetime") 的实例，并将根据系统时区数据库将其转换为当地时区。 如果 _dt_ 为简单型 (即 `dt.tzinfo` 为 `None`)，它将被假定为当地时间。 _isdst_ 形参将被忽略。

在 3.3 版本加入.

从 3.12 版起不建议使用，将在 3.14 版中移除: _isdst_ 形参。

email.utils.make_msgid( _idstring =None_, _domain =None_)¶
~~~
    

~~~
返回一个适合作为兼容 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 的 _Message-ID_ 标头的字符串。可选参数 _idstring_ 可传入一字符串以增强该消息 ID 的唯一性。可选参数 _domain_ 可用于提供消息 ID 中字符 '@' 之后的部分，其默认值是本机的主机名。正常情况下无需覆盖此默认值，但在特定情况下覆盖默认值可能会有用，比如构建一个分布式系统，在多台主机上采用一致的域名。

在 3.2 版本发生变更: 增加了关键字 _domain_

下列函数是旧（`Compat32`）电子邮件 API 的一部分。新 API 提供的解析和格式化在标头解析机制中已经被自动完成，故在使用新 API 时没有必要直接使用它们函数。

email.utils.quote( _str_ )¶
~~~
    

~~~
返回一个新的字符串， _str_ 中的反斜杠被替换为两个反斜杠，并且双引号被替换为反斜杠加双引号。

email.utils.unquote( _str_ )¶
~~~
    

~~~
返回 _str_ 被去除引用版本的字符串。如果 _str_ 开头和结尾均是双引号，则这对双引号被去除。类似地，如果 _str_ 开头和结尾都是尖角括号，这对尖角括号会被去除。

email.utils.parseaddr( _address_ )¶
~~~
    

~~~
将地址（应为诸如 _To_ 或者 _Cc_ 之类包含地址的字段值）解析为构成之的 _真实名字_ 和 _电子邮件地址_ 部分。返回包含这两个信息的一个元组；如若解析失败，则返回一个二元组 `('', '')` 。

email.utils.formataddr( _pair_ , _charset ='utf-8'_)¶
~~~
    

~~~
是 `parseaddr()` 的逆操作，接受一个 `(真实名字, 电子邮件地址)` 的二元组，并返回适合于 _To_ or _Cc_ 标头的字符串。如果第一个元素为假性值，则第二个元素将被原样返回。

可选地，如果指定 _charset_ ，则被视为一符合 [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.md) 的编码字符集，用于编码 `真实名字` 中的非 ASCII 字符。可以是一个 [`str`](stdtypes.md#str "str") 类的实例，或者一个 [`Charset`](email.charset.md#email.charset.Charset "email.charset.Charset") 类。默认为 `utf-8` 。

在 3.3 版本发生变更: 添加了 _charset_ 选项。

email.utils.getaddresses( _fieldvalues_ )¶
~~~
    

~~~
该方法返回一个形似 `parseaddr()` 返回的二元组的列表。 _fieldvalues_ 是一个序列，包含了形似 [`Message.get_all`](email.compat32-message.md#email.message.Message.get_all "email.message.Message.get_all") 返回值的标头字段值。获取了一消息的所有收件人的简单示例如下：
~~~
    
    
~~~
from email.utils import getaddresses

tos = msg.get_all('to', [])
ccs = msg.get_all('cc', [])
resent_tos = msg.get_all('resent-to', [])
resent_ccs = msg.get_all('resent-cc', [])
all_recipients = getaddresses(tos + ccs + resent_tos + resent_ccs)
~~~

email.utils.parsedate( _date_ )¶

    

~~~
尝试根据 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 的规则解析一个日期。然而，有些寄信人不严格遵守这一格式，所以这种情况下 `parsedate()` 会尝试猜测其形式。 _date_ 是一个字符串包含了一个形如 `"Mon, 20 Nov 1995 19:12:08 -0500"` 的 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 格式日期。如果日期解析成功， `parsedate()` 将返回一个九元组，可直接传递给 [`time.mktime()`](time.md#time.mktime "time.mktime")；否则返回 `None`。注意返回的元组中下标为 6、7、8 的部分是无用的。

email.utils.parsedate_tz( _date_ )¶
~~~
    

~~~
执行与 `parsedate()` 相同的功能，但会返回 `None` 或是一个 10 元组；前 9 个元素构成一个可以直接传给 [`time.mktime()`](time.md#time.mktime "time.mktime") 的元组，而第十个元素则是该日期的时区与 UTC (格林威治平均时 GMT 的正式名称) [1] 的时差。 如果输入字符串不带时区，则所返回元组的最后一个元素将为 `0`，这表示 UTC。 请注意结果元组的索引号 6, 7 和 8 是不可用的。

email.utils.parsedate_to_datetime( _date_ )¶
~~~
    

~~~
`format_datetime()` 的逆操作。 执行与 `parsedate()` 相同的功能，但会在成功时返回一个 [`datetime`](datetime.md#datetime.datetime "datetime.datetime")；否则如果 _date_ 包含无效的值例如小时值大于 23 或时区偏移量不在 -24 和 24 时范围之内则会引发 `ValueError`。 如果输入日期的时区值为 `-0000`，则 `datetime` 将为一个简单形 `datetime`，而如果日期符合 RFC 标准则它将代表一个 UTC 时间，但是并不指明日期所在消息的实际源时区。 如果输入日期具有任何其他有效的时区偏移量，则 `datetime` 将是一个感知型 `datetime` 并与 [`timezone`](datetime.md#datetime.timezone "datetime.timezone") [`tzinfo`](datetime.md#datetime.tzinfo "datetime.tzinfo") 相对应。

在 3.3 版本加入.

email.utils.mktime_tz( _tuple_ )¶
~~~
    

~~~
将 `parsedate_tz()` 所返回的 10 元组转换为一个 UTC 时间戳（相距 Epoch 纪元初始的秒数）。 如果元组中的时区项为 `None`，则视为当地时间。

email.utils.formatdate( _timeval =None_, _localtime =False_, _usegmt =False_)¶
~~~
    

~~~
返回 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 标准的日期字符串，例如:
~~~
    
    
~~~
Fri, 09 Nov 2001 01:08:47 -0000
~~~

可选的 _timeval_ 如果给出，则是一个可被 [`time.gmtime()`](time.md#time.gmtime "time.gmtime") 和 [`time.localtime()`](time.md#time.localtime "time.localtime") 接受的浮点数时间值，否则会使用当前时间。

可选的 _localtime_ 是一个旗标，当为 `True` 时，将会解析 _timeval_ ，并返回一个相对于当地时区而非 UTC 的日期值，并会适当地考虑夏令时。 默认值 `False` 表示使用 UTC。

可选的 _usegmt_ 是一个旗标，当为 `True` 时，将会输出一个日期字符串，其中时区表示为 ascii 字符串 `GMT` 而非数字形式的 `-0000`。 这对某些协议（例如 HTTP）来说是必要的。 这仅在 _localtime_ 为 `False` 时应用。 默认值为 `False`。

email.utils.format_datetime( _dt_ , _usegmt =False_)¶

    

~~~
类似于 `formatdate`，但输入的是一个 [`datetime`](datetime.md#module-datetime "datetime: Basic date and time types.") 实例。 如果实例是一个简单型 datetime，它会被视为 "不带源时区信息的 UTC"，并且使用传统的 `-0000` 作为时区。 如果实例是一个感知型 `datetime`，则会使用数字形式的时区时差。 如果实例是感知型且时区时差为零，则 _usegmt_ 可能会被设为 `True`，在这种情况下将使用字符串 `GMT` 而非数字形式的时区时差。 这提供了一种生成符合标准 HTTP 日期标头的方式。

在 3.3 版本加入.

email.utils.decode_rfc2231( _s_ )¶
~~~
    

~~~
根据 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md) 解码字符串 _s_ 。

email.utils.encode_rfc2231( _s_ , _charset =None_, _language =None_)¶
~~~
    

~~~
根据 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md) 对字符串 _s_ 进行编码。 可选的 _charset_ 和 _language_ 如果给出，则为指明要使用的字符集名称和语言名称。 如果两者均未给出，则会原样返回 _s_ 。 如果给出 _charset_ 但未给出 _language_ ，则会使用空字符串作为 _language_ 值来对字符串进行编码。

email.utils.collapse_rfc2231_value( _value_ , _errors ='replace'_, _fallback_charset ='us-ascii'_)¶
~~~
    

~~~
当以 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md) 格式来编码标头形参时，[`Message.get_param`](email.compat32-message.md#email.message.Message.get_param "email.message.Message.get_param") 可能返回一个包含字符集、语言和值的 3 元组。 `collapse_rfc2231_value()` 会将此返回为一个 unicode 字符串。 可选的 _errors_ 会被传递给 [`str`](stdtypes.md#str "str") 的 [`encode()`](stdtypes.md#str.encode "str.encode") 方法的 _errors_ 参数；它的默认值为 `'replace'`。 可选的 _fallback_charset_ 指定当 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md) 标头中的字符集无法被 Python 识别时要使用的字符集；它的默认值为 `'us-ascii'`。

为方便起见，如果传给 `collapse_rfc2231_value()` 的 _value_ 不是一个元组，则应为一个字符串并会将其原样返回。

email.utils.decode_params( _params_ )¶
~~~
    

~~~
