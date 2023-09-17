# `email.message.Message`: 使用 [`compat32`](email.policy.md#email.policy.compat32 "email.policy.compat32") API 来表示电子邮件消息¶

`Message` 类与 [`EmailMessage`](email.message.md#email.message.EmailMessage "email.message.EmailMessage") 类非常相似，但没有该类所添加的方法，并且某些方法的默认行为也略有不同。 我们还在这里记录了一些虽然被 [`EmailMessage`](email.message.md#email.message.EmailMessage "email.message.EmailMessage") 类所支持但并不推荐的方法，除非你是在处理旧有代码。

在其他情况下这两个类的理念和结构都是相同的。

本文档描述了默认 (对于 `Message`) 策略 [`Compat32`](email.policy.md#email.policy.Compat32 "email.policy.Compat32") 之下的行为。 如果你要使用其他策略，你应当改用 [`EmailMessage`](email.message.md#email.message.EmailMessage "email.message.EmailMessage") 类。

电子邮件消息由多个 _标头_ 和一个 _载荷_ 组成。 标头必须为 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 风格的名称和值，其中字典名和值由冒号分隔。 冒号不是字段名或字段值的组成部分。 载荷可以是简单的文本消息，或是二进制对象，或是多个子消息的结构化序列，每个子消息都有自己的标头集合和自己的载荷。 后一种类型的载荷是由具有 _multipart/*_ 或 _message/rfc822_ 等 MIME 类型的消息来指明的。

`Message` 对象所提供了概念化模型是由标头组成的有序字典，加上用于访问标头中的特殊信息以及访问载荷的额外方法，以便能生成消息的序列化版本，并递归地遍历对象树。 请注意重复的标头是受支持的，但必须使用特殊的方法来访问它们。

`Message` 伪字典以标头名作为索引，标头名必须为 ASCII 值。 字典的值为应当只包含 ASCII 字符的字符串；对于非 ASCII 输入有一些特殊处理，但这并不总能产生正确的结果。 标头以保留原大小写的形式存储和返回，但字段名称匹配对大小写不敏感。 还可能会有一个单独的封包标头，也称 _Unix-From_ 标头或 `From_` 标头。 _载荷_ 对于简单消息对象的情况是一个字符串或字节串，对于 MIME 容器文档的情况 (例如 _multipart/*_ 和 _message/rfc822_ ) 则是一个 `Message` 对象。

以下是 `Message` 类的方法:

_class _email.message.Message( _policy =compat32_)¶

    

~~~
如果指定了 _policy_ (它必须为 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 类的实例) 则使用它所设置的规则来更新和序列化消息的表示形式。 如果未设置 _policy_ ，则使用 [`compat32`](email.policy.md#email.policy.Compat32 "email.policy.Compat32") 策略，该策略会保持对 Python 3.2 版 email 包的向下兼容性。 更多信息请参阅 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 文档。

在 3.3 版本发生变更: 增加了 _policy_ 关键字参数。

as_string( _unixfrom =False_, _maxheaderlen =0_, _policy =None_)¶
~~~
    

~~~
以展平的字符串形式返回整个消息对象。 或可选的 _unixfrom_ 为真值，返回的字符串会包括封包标头。 _unixfrom_ 的默认值是 `False`。 出于保持向下兼容性的原因， _maxheaderlen_ 的默认值是 `0`，因此如果你想要不同的值你必须显式地重载它（在策略中为 _max_line_length_ 指定的值将被此方法忽略）。 _policy_ 参数可被用于覆盖从消息实例获取的默认策略。 这可以用来对该方法所输出的格式进行一些控制，因为指定的 _policy_ 将被传递给 `Generator`。

如果需要填充默认值以完成对字符串的转换则展平消息可能触发对 `Message` 的修改（例如，MIME 边界可能会被生成或被修改）。

请注意此方法是出于便捷原因提供的，并可能无法总是以你想要的方式来格式化消息。 例如，在默认情况下它不会按 Unix mbox 格式的要求对以 `From` 打头的行执行调整。 为了获得更高灵活性，请实例化一个 [`Generator`](email.generator.md#email.generator.Generator "email.generator.Generator") 实例并直接使用其 [`flatten()`](email.generator.md#email.generator.Generator.flatten "email.generator.Generator.flatten") 方法。 例如:
~~~
    
    
~~~
from io import StringIO
from email.generator import Generator
fp = StringIO()
g = Generator(fp, mangle_from_=True, maxheaderlen=60)
g.flatten(msg)
text = fp.getvalue()
~~~

如果消息对象包含未按照 RFC 标准进行编码的二进制数据，则这些不合规数据将被 unicode "unknown character" 码位值所替代。 （另请参阅 `as_bytes()` 和 [`BytesGenerator`](email.generator.md#email.generator.BytesGenerator "email.generator.BytesGenerator")。）

在 3.4 版本发生变更: 增加了 _policy_ 关键字参数。

__str__()¶

    

~~~
与 `as_string()` 等价。 这将让 `str(msg)` 产生一个包含已格式化消息的字符号。

as_bytes( _unixfrom =False_, _policy =None_)¶
~~~
    

~~~
以字节串对象的形式返回整个扁平化后的消息。 当可选的 _unixfrom_ 为真值时，返回的字符串会包括封包标头。 _unixfrom_ 的默认值为 `False`。 _policy_ 参数可被用于重载从消息实例获取的默认策略。 这可被用来控制该方法所产生的部分格式化效果，因为指定的 _policy_ 将被传递给 `BytesGenerator`。

如果需要填充默认值以完成对字符串的转换则展平消息可能触发对 `Message` 的修改（例如，MIME 边界可能会被生成或被修改）。

请注意此方法是出于便捷原因提供的，并可能无法总是以你想要的方式来格式化消息。 例如，在默认情况下它不会按 Unix mbox 格式的要求对以 `From` 打头的行执行调整。 为了获得更高灵活性，请实例化一个 [`BytesGenerator`](email.generator.md#email.generator.BytesGenerator "email.generator.BytesGenerator") 实例并直接使用其 [`flatten()`](email.generator.md#email.generator.BytesGenerator.flatten "email.generator.BytesGenerator.flatten") 方法。 例如:
~~~
    
    
~~~
from io import BytesIO
from email.generator import BytesGenerator
fp = BytesIO()
g = BytesGenerator(fp, mangle_from_=True, maxheaderlen=60)
g.flatten(msg)
text = fp.getvalue()
~~~

在 3.4 版本加入.

__bytes__()¶

    

~~~
与 `as_bytes()` 等价。 这将让 `bytes(msg)` 产生一个包含已格式化消息的字节串对象。

在 3.4 版本加入.

is_multipart()¶
~~~
    

~~~
如果该消息的载荷是一个子 `Message` 对象列表则返回 `True`，否则返回 `False`。 当 `is_multipart()` 返回 `False` 时，载荷应当是一个字符串对象（有可能是一个 CTE 编码的二进制载荷）。 （请注意 `is_multipart()` 返回 `True` 并不意味着 "msg.get_content_maintype() == 'multipart'" 将返回 `True`。 例如，`is_multipart` 在 `Message` 类型为 `message/rfc822` 时也将返回 `True`。）

set_unixfrom( _unixfrom_ )¶
~~~
    

~~~
将消息的封包标头设为 _unixfrom_ ，这应当是一个字符串。

get_unixfrom()¶
~~~
    

~~~
返回消息的信封头。如果信封头从未被设置过，默认返回 `None` 。

attach( _payload_ )¶
~~~
    

~~~
将给定的 _payload_ 添加到当前载荷中，当前载荷在该调用之前必须为 `None` 或是一个 `Message` 对象列表。 在调用之后，此载荷将总是一个 `Message` 对象列表。 如果你想将此载荷设为一个标量对象（如字符串），请改用 `set_payload()`。

这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被 [`set_content()`](email.message.md#email.message.EmailMessage.set_content "email.message.EmailMessage.set_content") 及相应的 `make` 和 `add` 方法所替代。

get_payload( _i =None_, _decode =False_)¶
~~~
    

~~~
返回当前的载荷，它在 `is_multipart()` 为 `True` 时将是一个 `Message` 对象列表，在 `is_multipart()` 为 `False` 时则是一个字符串。 如果该载荷是一个列表且你修改了这个列表对象，那么你就是原地修改了消息的载荷。

传入可选参数 _i_ 时，如果 `is_multipart()` 为 `True`，`get_payload()` 将返回载荷从零开始计数的第 _i_ 个元素。 如果 _i_ 小于 0 或大于等于载荷中的条目数则将引发 [`IndexError`](exceptions.md#IndexError "IndexError")。 如果载荷是一个字符串 (即 `is_multipart()` 为 `False`) 且给出了 _i_ ，则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

可选的 _decode_ 是一个指明载荷是否应根据 _Content-Transfer-Encoding_ 标头被解码的旗标。 当其值为 `True` 且消息没有多个部分时，如果此标头值为 `quoted-printable` 或 `base64` 则载荷将被解码。 如果使用了其他编码格式，或者找不到 _Content-Transfer-Encoding_ 标头时，载荷将被原样返回（不编码）。 在所有情况下返回值都是二进制数据。 如果消息有多个部分且 _decode_ 旗标为 `True`，则将返回 `None`。 如果载荷为 base64 但内容不完全正确（如缺少填充符、存在 base64 字母表以外的字符等），则将在消息的缺陷属性中添加适当的缺陷值 (分别为 `InvalidBase64PaddingDefect` 或 `InvalidBase64CharactersDefect`)。

当 _decode_ 为 `False` (默认值) 时消息体会作为字符串返回而不解码 _Content-Transfer-Encoding_ 。 但是，对于 _Content-Transfer-Encoding_ 为 8bit 的情况，会尝试使用 _Content-Type_ 标头指定的 `charset` 来解码原始字节串，并使用 `replace` 错误处理程序。 如果未指定 `charset`，或者如果指定的 `charset` 未被 email 包所识别，则会使用默认的 ASCII 字符集来解码消息体。

这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被 [`get_content()`](email.message.md#email.message.EmailMessage.get_content "email.message.EmailMessage.get_content") 和 [`iter_parts()`](email.message.md#email.message.EmailMessage.iter_parts "email.message.EmailMessage.iter_parts") 方法所替代。

set_payload( _payload_ , _charset =None_)¶
~~~
    

~~~
将整个消息对象的载荷设为 _payload_ 。 客户端要负责确保载荷的不变性。 可选的 _charset_ 用于设置消息的默认字符集；详情请参阅 `set_charset()`。

这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被 [`set_content()`](email.message.md#email.message.EmailMessage.set_content "email.message.EmailMessage.set_content") 方法所替代。

set_charset( _charset_ )¶
~~~
    

~~~
将载荷的字符集设为 _charset_ ，它可以是 [`Charset`](email.charset.md#email.charset.Charset "email.charset.Charset") 实例 (参见 [`email.charset`](email.charset.md#module-email.charset "email.charset: Character Sets"))、字符集名称字符串或 `None`。 如果是字符串，它将被转换为一个 [`Charset`](email.charset.md#email.charset.Charset "email.charset.Charset") 实例。 如果 _charset_ 是 `None`，`charset` 形参将从 _Content-Type_ 标头中被删除（消息将不会进行其他修改）。 任何其他值都将导致 [`TypeError`](exceptions.md#TypeError "TypeError")。

如果 _MIME-Version_ 标头不存在则将被添加。 如果 _Content-Type_ 标头不存在，则将添加一个值为 _text/plain_ 的该标头。 无论 _Content-Type_ 标头是否已存在，其 `charset` 形参都将被设为 _charset.output_charset_ 。 如果 _charset.input_charset_ 和 _charset.output_charset_ 不同，则载荷将被重编码为 _output_charset_ 。 如果 _Content-Transfer-Encoding_ 标头不存在，则载荷将在必要时使用指定的 [`Charset`](email.charset.md#email.charset.Charset "email.charset.Charset") 来转换编码，并将添加一个具有相应值的标头。 如果 _Content-Transfer-Encoding_ 标头已存在，则会假定载荷已使用该 _Content-Transfer-Encoding_ 进行正确编码并不会再被修改。

这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被 `email.emailmessage.EmailMessage.set_content()` 方法的 _charset_ 形参所替代。

get_charset()¶
~~~
    

~~~
返回与消息的载荷相关联的 [`Charset`](email.charset.md#email.charset.Charset "email.charset.Charset") 实例。

这是一个过时的方法。 在 `EmailMessage` 类上它将总是返回 `None`。

以下方法实现了用于访问消息的 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.md) 标头的类映射接口。 请注意这些方法和普通映射（例如字典）接口之间存在一些语义上的不同。 举例来说，在一个字典中不能有重复的键，但消息标头则可能有重复。 并且，在字典中由 `keys()` 返回的键的顺序是没有保证的，但在 `Message` 对象中，标头总是会按它们在原始消息中的出现或后继加入顺序返回。 任何已删除再重新加入的标头总是会添加到标头列表的末尾。

这些语义上的差异是有意为之且其目的是为了提供最大的便利性。

请注意在任何情况下，消息当中的任何封包标头都不会包含在映射接口当中。

在由字符串生成的模型中，任何包含非 ASCII 字节数据（违反 RFC）的标头值在通过此接口来获取时，将被表示为使用 `unknown-8bit` 字符集的 [`Header`](email.header.md#email.header.Header "email.header.Header") 对象。

__len__()¶
~~~
    

~~~
返回标头的总数，包括重复项。

__contains__( _name_ )¶
~~~
    

~~~
如果消息对象中有一个名为 _name_ 的字段则返回 `True`。 匹配操作对大小写不敏感并且 _name_ 不应包括末尾的冒号。 用于 `in` 运算符，例如:
~~~
    
    
~~~
if 'message-id' in myMessage:
   print('Message-ID:', myMessage['message-id'])
~~~

__getitem__( _name_ )¶

    

~~~
返回指定名称标头字段的值。 _name_ 不应包括作为字段分隔符的冒号。 如果标头未找到，则返回 `None`；[`KeyError`](exceptions.md#KeyError "KeyError") 永远不会被引发。

请注意如果指定名称的字段在消息标头中多次出现，具体将返回哪个字段值是未定义的。 请使用 `get_all()` 方法来获取所有指定名称标头的值。

__setitem__( _name_ , _val_ )¶
~~~
    

~~~
将具有字段名 _name_ 和值 _val_ 的标头添加到消息中。 字段会被添加到消息的现有字段的末尾。

请注意，这个方法 _既不会_ 覆盖 _也不会_ 删除任何字段名重名的已有字段。如果你确实想保证新字段是整个信息头当中唯一拥有 _name_ 字段名的字段，你需要先把旧字段删除。例如：
~~~
    
    
~~~
del msg['subject']
msg['subject'] = 'Python roolz!'
~~~

__delitem__( _name_ )¶

    

~~~
删除信息头当中字段名匹配 _name_ 的所有字段。如果匹配指定名称的字段没有找到，也不会抛出任何异常。

keys()¶
~~~
    

~~~
以列表形式返回消息头中所有的字段名。

values()¶
~~~
    

~~~
以列表形式返回消息头中所有的字段值。

items()¶
~~~
    

~~~
以二元元组的列表形式返回消息头中所有的字段名和字段值。

get( _name_ , _failobj =None_)¶
~~~
    

~~~
返回指定名称标头字段的值。 这与 `__getitem__()` 是一样的，不同之处在于如果指定名称标头未找到则会返回可选的 _failobj_ (默认为 `None`)。

以下是一些有用的附加方法:

get_all( _name_ , _failobj =None_)¶
~~~
    

~~~
返回字段名为 _name_ 的所有字段值的列表。如果信息内不存在匹配的字段，返回 _failobj_ （其默认值为 `None` ）。

add_header( __name_ , __value_ , _** _params_)¶
~~~
    

~~~
高级头字段设定。这个方法与 `__setitem__()` 类似，不过你可以使用关键字参数为字段提供附加参数。 __name_ 是字段名， __value_ 是字段 _主_ 值。

对于关键字参数字典 __params_ 中的每一项，其键会被当作形参名，并执行下划线和连字符间的转换（因为连字符不是合法的 Python 标识符）。 通常，形参将以 `key="value"` 的形式添加，除非值为 `None`，在这种情况下将只添加键。 如果值包含非 ASCII 字符，可将其指定为格式为 `(CHARSET, LANGUAGE, VALUE)` 的三元组，其中 `CHARSET` 为要用来编码值的字符集名称字符串，`LANGUAGE` 通常可设为 `None` 或空字符串（请参阅 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md) 了解其他可能的取值），而 `VALUE` 为包含非 ASCII 码位的字符串值。 如果不是传入一个三元组且值包含非 ASCII 字符，则会自动以 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md) 格式使用 `CHARSET` 为 `utf-8` 和 `LANGUAGE` 为 `None` 对其进行编码。

以下是为示例代码:
~~~
    
    
~~~
msg.add_header('Content-Disposition', 'attachment', filename='bud.gif')
~~~

会添加一个形如下文的头字段：

    
    
~~~
Content-Disposition: attachment; filename="bud.gif"
~~~

使用非 ASCII 字符的示例代码:

    
    
~~~
msg.add_header('Content-Disposition', 'attachment',
               filename=('iso-8859-1', '', 'Fußballer.ppt'))
~~~

它的输出结果为

    
    
~~~
Content-Disposition: attachment; filename*="iso-8859-1''Fu%DFballer.ppt"
~~~

replace_header( __name_ , __value_ )¶

    

~~~
替换一个标头。 将替换在匹配 __name_ 的消息中找到的第一个标头，标头顺序和字段名大小写保持不变。 如果未找到匹配的标头，则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

get_content_type()¶
~~~
    

~~~
返回消息的内容类型。 返回的字符串会强制转换为 _maintype/subtype_ 的全小写形式。 如果消息中没有 _Content-Type_ 标头则将返回由 `get_default_type()` 给出的默认类型。 因为根据 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md)，消息总是要有一个默认类型，所以 `get_content_type()` 将总是返回一个值。

[**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md) 将消息的默认类型定义为 _text/plain_ ，除非它是出现在 _multipart/digest_ 容器内，在这种情况下其类型应为 _message/rfc822_ 。 如果 _Content-Type_ 标头指定了无效的类型，[ **RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md) 规定其默认类型应为 _text/plain_ 。

get_content_maintype()¶
~~~
    

~~~
返回信息的主要内容类型。准确来说，此方法返回的是 `get_content_type()` 方法所返回的形如 _maintype/subtype_ 的字符串当中的 _maintype_ 部分。

get_content_subtype()¶
~~~
    

~~~
返回信息的子内容类型。准确来说，此方法返回的是 `get_content_type()` 方法所返回的形如 _maintype/subtype_ 的字符串当中的 _subtype_ 部分。

get_default_type()¶
~~~
    

~~~
返回默认的内容类型。绝大多数的信息，其默认内容类型都是 _text/plain_ 。作为 _multipart/digest_ 容器内子部分的信息除外，它们的默认内容类型是 _message/rfc822_ 。

set_default_type( _ctype_ )¶
~~~
    

~~~
设置默认的内容类型。 _ctype_ 应当为 _text/plain_ 或者 _message/rfc822_ ，尽管这并非强制。 默认的内容类型不会存储在 _Content-Type_ 标头中。

get_params( _failobj =None_, _header ='content-type'_, _unquote =True_)¶
~~~
    

~~~
将消息的 _Content-Type_ 形参作为列表返回。 所返回列表的元素为以 `'='` 号拆分出的键/值对 2 元组。 `'='` 左侧的为键，右侧的为值。 如果形参值中没有 `'='` 号，否则该将值如 `get_param()` 描述并且在可选 _unquote_ 为 `True` (默认值) 时会被取消转义。

可选的 _failobj_ 是在没有 _Content-Type_ 标头时要返回的对象。 可选的 _header_ 是要替代 _Content-Type_ 被搜索的标头。

这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被标头访问方法所返回的单独标头对象的 _params_ 特征属性所替代。

get_param( _param_ , _failobj =None_, _header ='content-type'_, _unquote =True_)¶
~~~
    

~~~
将 _Content-Type_ 标头的形参 _param_ 作为字符串返回。 如果消息没有 _Content-Type_ 标头或者没有这样的形参，则返回 _failobj_ (默认为 `None`)。

如果给出可选的 _header_ ，它会指定要替代 _Content-Type_ 来使用的消息标头。

形参的键总是以大小写不敏感的方式来比较的。 返回值可以是一个字符串，或者如果形参以 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md) 编码则是一个 3 元组。 当为 3 元组时，值中的元素采用 `(CHARSET, LANGUAGE, VALUE)` 的形式。 请注意 `CHARSET` 和 `LANGUAGE` 都可以为 `None`，在此情况下你应当将 `VALUE` 当作以 `us-ascii` 字符集来编码。 你可以总是忽略 `LANGUAGE`。

如果你的应用不关心形参是否以 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md) 来编码，你可以通过调用 [`email.utils.collapse_rfc2231_value()`](email.utils.md#email.utils.collapse_rfc2231_value "email.utils.collapse_rfc2231_value") 来展平形参值，传入来自 `get_param()` 的返回值。 当值为元组时这将返回一个经适当编码的 Unicode 字符串，否则返回未经转换的原字符串。 例如:
~~~
    
    
~~~
rawparam = msg.get_param('foo')
param = email.utils.collapse_rfc2231_value(rawparam)
~~~

无论在哪种情况下，形参值（或为返回的字符串，或为 3 元组形式的 `VALUE` 条目）总是未经转换的，除非 _unquote_ 被设为 `False`。

这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被标头访问方法所返回的单独标头对象的 _params_ 特征属性所替代。

set_param( _param_ , _value_ , _header ='Content-Type'_, _requote =True_, _charset =None_, _language =''_, _replace =False_)¶

    

~~~
在 _Content-Type_ 标头中设置一个形参。 如果该形参已存在于标头中，它的值将被替换为 _value_ 。 如果此消息还未定义 _Content-Type_ 标头，它将被设为 _text/plain_ 且新的形参值将按 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md) 的要求添加。

可选的 _header_ 指定一个 _Content-Type_ 的替代标头，并且所有形参将根据需要被转换，除非可选的 _requote_ 为 `False` (默认为 `True`)。

如果指定了可选的 _charset_ ，形参将按照 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md) 来编码。 可选的 _language_ 指定了 RFC 2231 的语言，默认为空字符串。 _charset_ 和 _language_ 都应为字符串。

如果 _replace_ 为 `False` （默认值），该头字段会被移动到所有头字段列表的末尾。如果 _replace_ 为 `True` ，字段会被原地更新。

在 3.4 版本发生变更: 添加了 `replace` 关键字。

del_param( _param_ , _header ='content-type'_, _requote =True_)¶
~~~
    

~~~
从 _Content-Type_ 标头中完全移除给定的形参。 标头将被原地重写并不带该形参或它的值。 所有的值将根据需要被转换，除非 _requote_ 为 `False` (默认为 `True`)。 可选的 _header_ 指定 _Content-Type_ 的一个替代项。

set_type( _type_ , _header ='Content-Type'_, _requote =True_)¶
~~~
    

~~~
设置 _Content-Type_ 标头的主类型和子类型。 _type_ 必须为 _maintype/subtype_ 形式的字符串，否则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

此方法可替换 _Content-Type_ 标头，并保持所有形参不变。 如果 _requote_ 为 `False`，这会保持原有标头引用转换不变，否则形参将被引用转换（默认行为）。

可以在 _header_ 参数中指定一个替代标头。 当 _Content-Type_ 标头被设置时也会添加一个 _MIME-Version_ 标头。

这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被 `make_` 和 `add_` 方法所替代。

get_filename( _failobj =None_)¶
~~~
    

~~~
返回信息头当中 _Content-Disposition_ 字段当中名为 `filename` 的参数值。如果该字段当中没有此参数，该方法会退而寻找 _Content-Type_ 字段当中的 `name` 参数值。如果这个也没有找到，或者这些个字段压根就不存在，返回 _failobj_ 。返回的字符串永远按照 [`email.utils.unquote()`](email.utils.md#email.utils.unquote "email.utils.unquote") 方法去除引号。

get_boundary( _failobj =None_)¶
~~~
    

~~~
返回信息头当中 _Content-Type_ 字段当中名为 `boundary` 的参数值。如果字段当中没有此参数，或者这些个字段压根就不存在，返回 _failobj_ 。返回的字符串永远按照 [`email.utils.unquote()`](email.utils.md#email.utils.unquote "email.utils.unquote") 方法去除引号。

set_boundary( _boundary_ )¶
~~~
    

~~~
将 _Content-Type_ 头字段的 `boundary` 参数设置为 _boundary_ 。 `set_boundary()` 方法永远都会在必要的时候为 _boundary_ 添加引号。如果信息对象中没有 _Content-Type_ 头字段，抛出 [`HeaderParseError`](email.errors.md#email.errors.HeaderParseError "email.errors.HeaderParseError") 异常。

请注意使用这个方法与删除旧的 _Content-Type_ 标头并通过 `add_header()` 添加一个带有新边界的新标头有细微的差异，因为 `set_boundary()` 会保留 _Content-Type_ 标头在原标头列表中的顺序。 但是，它 _不会_ 保留原 _Content-Type_ 标头中可能存在的任何连续的行。

get_content_charset( _failobj =None_)¶
~~~
    

~~~
返回 _Content-Type_ 头字段中的 `charset` 参数，强制小写。如果字段当中没有此参数，或者这个字段压根不存在，返回 _failobj_ 。

请注意此方法不同于 `get_charset()`，后者会返回 [`Charset`](email.charset.md#email.charset.Charset "email.charset.Charset") 实例作为消息体的默认编码格式。

get_charsets( _failobj =None_)¶
~~~
    

~~~
返回一个包含了信息内所有字符集名字的列表。 如果信息是 _multipart_ 类型的，那么列表当中的每一项都对应其载荷的子部分的字符集名字。 否则，该列表是一个长度为 1 的列表。

列表中的每一项都是字符串，它们是其所表示的子部分的 _Content-Type_ 标头中 `charset` 形参的值。 但是，如果该子部分没有 _Content-Type_ 标头，或没有 `charset` 形参，或者主 MIME 类型不是 _text_ ，则所返回列表中的对应项将为 _failobj_ 。

get_content_disposition()¶
~~~
    

~~~
如果信息的 _Content-Disposition_ 头字段存在，返回其字段值；否则返回 `None` 。返回的值均为小写，不包含参数。如果信息遵循 [**RFC 2183**](https://datatracker.ietf.org/doc/html/rfc2183.md) 标准，则此方法的返回值只可能在 _inline_ 、 _attachment_ 和 `None` 之间选择。

在 3.5 版本加入.

walk()¶
~~~
    

~~~
`walk()` 方法是一个多功能生成器。它可以被用来以深度优先顺序遍历信息对象树的所有部分和子部分。一般而言， `walk()` 会被用作 `for` 循环的迭代器，每一次迭代都返回其下一个子部分。

以下例子会打印出一封具有多部分结构之信息的每个部分的 MIME 类型。
~~~
    
    
~~~shell
>>> for part in msg.walk():
...     print(part.get_content_type())
multipart/report
text/plain
message/delivery-status
text/plain
text/plain
message/rfc822
text/plain
~~~

`walk` 会遍历所有 `is_multipart()` 方法返回 `True` 的部分之子部分，哪怕 `msg.get_content_maintype() == 'multipart'` 返回的是 `False` 。使用 `_structure` 除错帮助函数可以帮助我们在下面这个例子当中看清楚这一点：

    
    
~~~shell
>>> for part in msg.walk():
...     print(part.get_content_maintype() == 'multipart',
...           part.is_multipart())
True True
False False
False True
False False
False False
False True
False False
>>> _structure(msg)
multipart/report
    text/plain
    message/delivery-status
        text/plain
        text/plain
    message/rfc822
        text/plain
~~~

在这里， `message` 的部分并非 `multiparts` ，但是它们真的包含子部分！ `is_multipart()` 返回 `True` ， `walk` 也深入进这些子部分中。

`Message` 对象也可以包含两个可选的实例属性，它们可被用于生成纯文本的 MIME 消息。

preamble¶

    

~~~
MIME 文档格式在标头之后的空白行以及第一个多部分的分界字符串之间允许添加一些文本， 通常，此文本在支持 MIME 的邮件阅读器中永远不可见，因为它处在标准 MIME 保护范围之外。 但是，当查看消息的原始文本，或当在不支持 MIME 的阅读器中查看消息时，此文本会变得可见。

_preamble_ 属性包含 MIME 文档开头部分的这些处于保护范围之外的文本。 当 [`Parser`](email.parser.md#email.parser.Parser "email.parser.Parser") 在标头之后及第一个分界字符串之前发现一些文本时，它会将这些文本赋值给消息的 _preamble_ 属性。 当 [`Generator`](email.generator.md#email.generator.Generator "email.generator.Generator") 写出 MIME 消息的纯文本表示形式时，如果它发现消息具有 _preamble_ 属性，它将在标头及第一个分界之间区域写出这些文本。 请参阅 [`email.parser`](email.parser.md#module-email.parser "email.parser: Parse flat text email messages to produce a message object structure.") 和 [`email.generator`](email.generator.md#module-email.generator "email.generator: Generate flat text email messages from a message structure.") 了解更多细节。

请注意如果消息对象没有前导文本，则 _preamble_ 属性将为 `None`。

epilogue¶
~~~
    

~~~
_epilogue_ 属性的作用方式与 _preamble_ 属性相同，区别在于它包含出现于最后一个分界与消息结尾之间的文本。

你不需要将 epilogue 设为空字符串以便让 [`Generator`](email.generator.md#email.generator.Generator "email.generator.Generator") 在文件末尾打印一个换行符。

defects¶
~~~
    

~~~
