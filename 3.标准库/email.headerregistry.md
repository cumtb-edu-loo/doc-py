# `email.headerregistry`: 自定义标头对象¶

**源代码:** [Lib/email/headerregistry.py](https://github.com/python/cpython/tree/3.12/Lib/email/headerregistry.py)

* * *

在 3.6 版本加入: [1]

标头是由 [`str`](stdtypes.md#str "str") 的自定义子类来表示的。 用于表示给定标头的特定类则由创建标头时生效的 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 的 [`header_factory`](email.policy.md#email.policy.EmailPolicy.header_factory "email.policy.EmailPolicy.header_factory") 确定。 这一节记录了 email 包为处理兼容 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 的电子邮件消息所实现的特定 `header_factory`，它不仅为各种标头类型提供了自定义的标头对象，还为应用程序提供了添加其自定义标头类型的扩展机制。

当使用派生自 [`EmailPolicy`](email.policy.md#email.policy.EmailPolicy "email.policy.EmailPolicy") 的任何策略对象时，所有标头都通过 `HeaderRegistry` 产生并且以 `BaseHeader` 作为其最后一个基类。 每个标头类都有一个由该标头类型确定的附加基类。 例如，许多标头都以 `UnstructuredHeader` 类作为其另一个基类。 一个标头专用的第二个类是由标头名称使用存储在 `HeaderRegistry` 中的查找表来确定的。 所有这些都针对典型应用程序进行透明的管理，但也为修改默认行为提供了接口，以便由更复杂的应用使用。

以下各节首先记录了标头基类及其属性，然后是用于修改 `HeaderRegistry` 行为的 API，最后是用于表示从结构化标头解析的数据的支持类。

_class _email.headerregistry.BaseHeader( _name_ , _value_ )¶

    

~~~
_name_ 和 _value_ 会从 [`header_factory`](email.policy.md#email.policy.EmailPolicy.header_factory "email.policy.EmailPolicy.header_factory") 调用传递给 `BaseHeader`。 任何标头对象的字符串值都是完成解码为 unicode 的 _value_ 。

这个基类定义了下列只读属性:

name¶
~~~
    

~~~
标头的名称（字段在 ':' 之前的部分）。 这就是 _name_ 的 [`header_factory`](email.policy.md#email.policy.EmailPolicy.header_factory "email.policy.EmailPolicy.header_factory") 调用所传递的值；也就是说会保持大小写形式。

defects¶
~~~
    

~~~
一个包含 `HeaderDefect` 实例的元组，这些实例报告了在解析期间发现的任何 RFC 合规性问题。 email 包会尝试尽可能地检测合规性问题。 请参阅 [`errors`](email.errors.md#module-email.errors "email.errors: The exception classes used by the email package.") 模块了解可能被报告的缺陷类型的相关讨论。

max_count¶
~~~
    

~~~
此类型标头可具有相同 `name` 的最大数量。 `None` 值表示无限制。 此属性的 `BaseHeader` 值为 `None`；专用的标头类预期将根据需要重载这个值。

`BaseHeader` 还提供了以下方法，它由 email 库代码调用，通常不应当由应用程序来调用。

fold( _*_ , _policy_ )¶
~~~
    

~~~
返回一个字符串，其中包含用来根据 _policy_ 正确地折叠标头的 [`linesep`](email.policy.md#email.policy.Policy.linesep "email.policy.Policy.linesep") 字符。 [`cte_type`](email.policy.md#email.policy.Policy.cte_type "email.policy.Policy.cte_type") 为 `8bit` 时将被作为 `7bit` 来处理，因为标头不能包含任意二进制数据。 如果 [`utf8`](email.policy.md#email.policy.EmailPolicy.utf8 "email.policy.EmailPolicy.utf8") 为 `False`，则非 ASCII 数据将根据 [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.md) 来编码。

`BaseHeader` 本身不能被用于创建标头对象。 它定义了一个与每个专用标头相配合的协议以便生成标头对象。 具体来说，`BaseHeader` 要求专用类提供一个名为 `parse` 的 [`classmethod()`](functions.md#classmethod "classmethod")。 此方法的调用形式如下:
~~~
    
    
~~~
parse(string, kwds)
~~~

`kwds` 是包含了一个预初始化键 `defects` 的字典。 `defects` 是一个空列表。 parse 方法应当将任何已检测到的缺陷添加到此列表中。 在返回时，`kwds` 字典 _必须_ 至少包含 `decoded` 和 `defects` 等键的值。 `decoded` 应当是标头的字符串值（即完全解码为 unicode 的标头值）。 parse 方法应当假定 _string_ 可能包含 content-transfer-encoded 部分，但也应当正确地处理全部有效的 unicode 字符以便它能解析未经编码的标头值。

随后 `BaseHeader` 的 `__new__` 会创建标头实例，并调用其 `init` 方法。 专属类如果想要设置 `BaseHeader` 自身所提供的属性之外的附加属性，只需提供一个 `init` 方法。 这样的 `init` 看起来应该是这样:

    
    
~~~python
def init(self, /, *args, **kw):
    self._myattr = kw.pop('myattr')
    super().init(*args, **kw)
~~~

也就是说，专属类放入 `kwds` 字典的任何额外内容都应当被移除和处理，并且 `kw` (和 `args`) 的剩余内容会被传递给 `BaseHeader` `init` 方法。

_class _email.headerregistry.UnstructuredHeader¶

    

~~~
"非结构化" 标头是 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 中默认的标头类型。 任何没有指定语法的标头都会被视为是非结构化的。 非结构化标头的经典例子是 _Subject_ 标头。

在 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 中，非结构化标头是指一段以 ASCII 字符集表示的任意文本。 但是 [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.md) 具有一个 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 兼容机制用来将标头值中的非 ASCII 文本编码为 ASCII 字符。 当包含已编码字的 _value_ 被传递给构造器时，`UnstructuredHeader` 解析器会按照非结构化文本的 [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.md) 规则将此类已编码字转换为 unicode。 解析器会使用启发式机制来尝试解码一些不合规的已编码字。 在此种情况下各类缺陷，例如已编码字或未编码文本中的无效字符问题等缺陷将会被注册。

此标头类型未提供附加属性。

_class _email.headerregistry.DateHeader¶
~~~
    

~~~
[**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 为电子邮件标头内的日期指定了非常明确的格式。 `DateHeader` 解析器会识别该日期格式，并且也能识别间或出现的一些“不规范”变种形式。

这个标头类型提供了以下附加属性。

datetime¶
~~~
    

~~~
如果标头值能被识别为某一种有效的日期形式，此属性将包含一个代表该日期的 [`datetime`](datetime.md#datetime.datetime "datetime.datetime") 实例。 如果输入日期的时区被指定为 `-0000` (表示它是 UTC 但不包含源时区的相关信息)，则 `datetime` 将为简单型 [`datetime`](datetime.md#datetime.datetime "datetime.datetime")。 如果找到了特定的时区时差值 (包括 `+0000`)，则 `datetime` 将包含一个使用 [`datetime.timezone`](datetime.md#datetime.timezone "datetime.timezone") 来记录时区时差的感知型 `datetime`。

标头的 `decoded` 值是由按照 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 对 `datetime` 进行格式化来确定的；也就是说，它会被设为:
~~~
    
    
~~~
email.utils.format_datetime(self.datetime)
~~~

当创建 `DateHeader` 时， _value_ 可以为 [`datetime`](3.标准库/datetime.md#datetime.datetime "datetime.datetime") 实例。 例如这意味着以下代码是有效的并能实现人们预期的行为:

    
    
~~~
msg['Date'] = datetime(2011, 7, 15, 21)
~~~

因为这是个简单型 `datetime` 它将被解读为 UTC 时间戳，并且结果值的时区将为 `-0000`。 使用来自 [`utils`](email.utils.md#module-email.utils "email.utils: Miscellaneous email package utilities.") 模块的 [`localtime()`](email.utils.md#email.utils.localtime "email.utils.localtime") 函数会更有用:

    
    
~~~
msg['Date'] = utils.localtime()
~~~

这个例子将日期标头设为使用当前时区时差值的当前时间和日期。

_class _email.headerregistry.AddressHeader¶

    

~~~
地址标头是最复杂的结构化标头类型之一。 `AddressHeader` 类提供了适合任何地址标头的泛用型接口。

这个标头类型提供了以下附加属性。

groups¶
~~~
    

~~~
编码了在标头值中找到的地址和分组的 `Group` 对象的元组。 非分组成员的地址在此列表中表示为 `display_name` 为 `None` 的单地址 `Groups`。

addresses¶
~~~
    

~~~
编码了来自标头值的所有单独地址的 `Address` 对象的元组。 如果标头值包含任何分组，则来自分组的单个地址将包含在该分组出现在值中的点上列出（也就是说，地址列表会被“展平”为一维列表）。

标头的 `decoded` 值将把所有已编码字解码为 unicode。 [`idna`](codecs.md#module-encodings.idna "encodings.idna: Internationalized Domain Names implementation") 编码的域名也会被解码为 unicode。 `decoded` 值是通过对 `groups` 属性的元素的 [`str`](stdtypes.md#str "str") 值使用 `', '` 进行 [合并](stdtypes.md#meth-str-join) 来设置的。

可以使用 `Address` 与 `Group` 对象的任意组合的列表来设置一个地址标头的值。 `display_name` 为 `None` 的 `Group` 对象将被解读为单独地址，这允许一个地址列表可以附带通过使用从源标头的 `groups` 属性获取的列表而保留原分组。

_class _email.headerregistry.SingleAddressHeader¶
~~~
    

~~~
`AddressHeader` 的子类，添加了一个额外的属性:

address¶
~~~
    

~~~
由标头值编码的单个地址。 如果标头值实际上包含一个以上的地址（这在默认 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 下将违反 RFC），则访问此属性将导致 [`ValueError`](exceptions.md#ValueError "ValueError")。

上述类中许多还具有一个 `Unique` 变体 (例如 `UniqueUnstructuredHeader`)。 其唯一差别是在 `Unique` 变体中 `max_count` 被设为 1。

_class _email.headerregistry.MIMEVersionHeader¶
~~~
    

~~~
实际上 _MIME-Version_ 标头只有一个有效的值，即 `1.0`。 为了将来的扩展，这个标头类还支持其他的有效版本号。 如果一个版本号是 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md) 的有效值，则标头对象的以下属性将具有不为 `None` 的值:

version¶
~~~
    

~~~
字符串形式的版本号。 任何空格和/或注释都会被移除。

major¶
~~~
    

~~~
整数形式的主版本号

minor¶
~~~
    

~~~
整数形式的次版本号

_class _email.headerregistry.ParameterizedMIMEHeader¶
~~~
    

~~~
MIME 标头都以前缀 'Content-' 打头。 每个特定标头都具有特定的值，其描述在该标头的类之中。 有些也可以接受一个具有通用格式的补充形参形表。 这个类被用作所有接受形参的 MIME 标头的基类。

params¶
~~~
    

~~~
一个将形参名映射到形参值的字典。

_class _email.headerregistry.ContentTypeHeader¶
~~~
    

~~~
处理 _Content-Type_ 标头的 `ParameterizedMIMEHeader` 类。

content_type¶
~~~
    

~~~
`maintype/subtype` 形式的内容类型字符串。

maintype¶
~~~
    

~~~
subtype¶
~~~
    

~~~
_class _email.headerregistry.ContentDispositionHeader¶
~~~
    

~~~
处理 _Content-Disposition_ 标头的 `ParameterizedMIMEHeader` 类。

content_disposition¶
~~~
    

~~~
`inline` 和 `attachment` 是仅有的常用有效值。

_class _email.headerregistry.ContentTransferEncoding¶
~~~
    

~~~
处理 _Content-Transfer-Encoding_ 标头。

cte¶
~~~
    

~~~
可用的有效值为 `7bit`, `8bit`, `base64` 和 `quoted-printable`。 更多信息请参阅 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md)。

_class _email.headerregistry.HeaderRegistry( _base_class =BaseHeader_, _default_class =UnstructuredHeader_, _use_default_map =True_)¶
~~~
    

~~~
这是由 [`EmailPolicy`](email.policy.md#email.policy.EmailPolicy "email.policy.EmailPolicy") 在默认情况下使用的工厂函数。 `HeaderRegistry` 会使用 _base_class_ 和从它所保存的注册表中获取的专用类来构建用于动态地创建标头实例的类。 当给定的标头名称未在注册表中出现时，则会使用由 _default_class_ 所指定的类作为专用类。 当 _use_default_map_ 为 `True` (默认值) 时，则会在初始化期间把将标头名称与类的标准映射拷贝到注册表中。 _base_class_ 始终会是所生成类的 `__bases__` 列表中的最后一个类。

默认的映射有:

> subject:
>  
>
> UniqueUnstructuredHeader
>
> date:
>  
>
> UniqueDateHeader
>
> resent-date:
>  
>
> DateHeader
>
> orig-date:
>  
>
> UniqueDateHeader
>
> sender:
>  
>
> UniqueSingleAddressHeader
>
> resent-sender:
>  
>
> SingleAddressHeader
>
> 到:
>  
>
> UniqueAddressHeader
>
> resent-to:
>  
>
> AddressHeader
>
> cc:
>  
>
> UniqueAddressHeader
>
> resent-cc:
>  
>
> AddressHeader
>
> bcc:
>  
>
> UniqueAddressHeader
>
> resent-bcc:
>  
>
> AddressHeader
>
> 从:
>  
>
> UniqueAddressHeader
>
> resent-from:
>  
>
> AddressHeader
>
> reply-to:
>  
>
> UniqueAddressHeader
>
> mime-version:
>  
>
> MIMEVersionHeader
>
> content-type:
>  
>
> ContentTypeHeader
>
> content-disposition:
>  
>
> ContentDispositionHeader
>
> content-transfer-encoding:
>  
>
> ContentTransferEncodingHeader
>
> message-id:
>  
>
> MessageIDHeader

`HeaderRegistry` 具有下列方法:

map_to_type( _self_ , _name_ , _cls_ )¶
~~~
    

~~~
_name_ 是要映射的标头名称。 它将在注册表中被转换为小写形式。 _cls_ 是要与 _base_class_ 一起被用来创建用于实例化与 _name_ 相匹配的标头的类的专用类。

__getitem__( _name_ )¶
~~~
    

~~~
构造并返回一个类来处理 _name_ 标头的创建。

__call__( _name_ , _value_ )¶
~~~
    

~~~
从注册表获得与 _name_ 相关联的专用标头 (如果 _name_ 未在注册表中出现则使用 _default_class_ ) 并将其与 _base_class_ 相组合以产生类，调用被构造类的构造器，传入相同的参数列表，并最终返回由此创建的类实例。

以下的类是用于表示从结构化标头解析的数据的类，并且通常会由应用程序使用以构造结构化的值并赋给特定的标头。

_class _email.headerregistry.Address( _display_name =''_, _username =''_, _domain =''_, _addr_spec =None_)¶
~~~
    

~~~
用于表示电子邮件地址的类。 地址的一般形式为:
~~~
    
    
~~~
[display_name] <username@domain>
~~~

或者：

    
    
~~~
username@domain
~~~

其中每个部分都必须符合在 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 中阐述的特定语法规则。

为了方便起见可以指定 _addr_spec_ 来替代 _username_ 和 _domain_ ，在此情况下 _username_ 和 _domain_ 将从 _addr_spec_ 中解析。 _addr_spec_ 应当是一个正确地引用了 RFC 的字符串；如果它不是 `Address` 则将引发错误。 Unicode 字符也允许使用并将在序列化时被正确地编码。 但是，根据 RFC，地址的 username 部分 _不允许_ 有 unicode。

display_name¶

    

~~~
地址的显示名称部分（如果有的话）并去除所有引用项。 如果地址没有显示名称，则此属性将为空字符串。

username¶
~~~
    

~~~
地址的 `username` 部分，去除所有引用项。

domain¶
~~~
    

~~~
地址的 `domain` 部分。

addr_spec¶
~~~
    

~~~
地址的 `username@domain` 部分，经过正确引用处理以作为纯地址使用（上面显示的第二种形式）。 此属性不可变。

__str__()¶
~~~
    

~~~
对象的 `str` 值是根据 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 规则进行引用处理的地址，但不带任何非 ASCII 字符的 Content Transfer Encoding。

为了支持 SMTP ([ **RFC 5321**](https://datatracker.ietf.org/doc/html/rfc5321.md))，`Address` 会处理一种特殊情况：如果 `username` 和 `domain` 均为空字符串 (或为 `None`)，则 `Address` 的字符串值为 `<>`。

_class _email.headerregistry.Group( _display_name =None_, _addresses =None_)¶
~~~
    

~~~
用于表示地址组的类。 地址组的一般形式为:
~~~
    
    
~~~
display_name: [address-list];
~~~

作为处理由组和单个地址混合构成的列表的便捷方式，`Group` 也可以通过将 _display_name_ 设为 `None` 以用来表示不是某个组的一部分的单独地址并提供单独地址的列表作为 _addresses_ 。

display_name¶

    

~~~
组的 `display_name`。 如果其为 `None` 并且恰好有一个 `Address` 在 `addresses` 中，则 `Group` 表示一个不在某个组中的单独地址。

addresses¶
~~~
    

~~~
一个可能为空的表示组中地址的包含 `Address` 对象的元组。

__str__()¶
~~~
    

~~~
