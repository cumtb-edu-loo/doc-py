# `email.message`: 表示一封电子邮件信息¶

**源代码:** [Lib/email/message.py](https://github.com/python/cpython/tree/3.12/Lib/email/message.py)

* * *

在 3.6 版本加入: [1]

位于 [`email`](email.md#module-email "email: Package supporting the parsing, manipulating, and generating email messages.") 包的中心的类就是 `EmailMessage` 类。这个类导入自 `email.message` 模块。它是 [`email`](email.md#module-email "email: Package supporting the parsing, manipulating, and generating email messages.") 对象模型的基类。`EmailMessage` 为设置和查询头字段内容、访问信息体的内容、以及创建和修改结构化信息提供了核心功能。

一份电子邮件信息由 _标头_ 和 _载荷_ （又被称为 _内容_ ）组成。 标头遵循 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 或者 [**RFC 6532**](https://datatracker.ietf.org/doc/html/rfc6532.md) 风格的字段名和值，字段名和字段值之间由一个冒号隔开。 这个冒号既不属于字段名，也不属于字段值。信息的载荷可能是一段简单的文字消息，也可能是一个二进制的对象，更可能是由多个拥有各自标头和载荷的子信息组成的结构化子信息序列。 对于后者类型的载荷，信息的 MIME 类型将会被指明为诸如 _multipart/*_ 或 _message/rfc822_ 的类型。

`EmailMessage` 对象所提供的抽象概念模型是一个头字段组成的有序字典加一个代表 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.md) 标准的信息体的 _载荷_ 。 载荷有可能是一系列子 `EmailMessage` 对象的列表。你除了可以通过一般的字典方法来访问头字段名和值，还可以使用特制方法来访问头的特定字段（比如说 MIME 内容类型字段）、操纵载荷、生成信息的序列化版本、递归遍历对象树。

`EmailMessage` 的类字典接口的字典索引是头字段名，头字段名必须是ASCII值。字典值是带有一些附加方法的字符串。虽然头字段的存储和获取都是保留其原始大小写的，但是字段名的匹配是大小写不敏感的。与真正的字典不同，键与键之间不但存在顺序关系，还可以重复。我们提供了额外的方法来处理含有重复键的头。

_载荷_ 是多样的。对于简单的信息对象，它是字符串或字节对象；对于诸如 _multipart/*_ 和 _message/rfc822_ 信息对象的 MIME 容器文档，它是一个 `EmailMessage` 对象列表。

_class _email.message.EmailMessage( _policy =default_)¶

    

~~~
如果指定了 _policy_ ，消息将由这个 _policy_ 所指定的规则来更新和序列化信息的表达。如果没有指定 _policy_ ，其将默认使用 [`default`](email.policy.md#email.policy.default "email.policy.default") 策略。这个策略遵循电子邮件的RFC标准，除了行终止符号（RFC要求使用 `\r\n` ，此策略使用Python标准的 `\n` 行终止符）。请前往 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 的文档获取更多信息。

as_string( _unixfrom =False_, _maxheaderlen =None_, _policy =None_)¶
~~~
    

~~~
以一段字符串的形式返回整个消息对象。 若可选的 _unixfrom_ 为真值，信封头将包括在返回的字符串中。 _unixfrom_ 默认为 `False`。 为了保持向下兼容基类 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 还接受 _maxheaderlen_ ，但其默认为 `None`，这意味着在默认情况下行长度将由策略的 [`max_line_length`](email.policy.md#email.policy.Policy.max_line_length "email.policy.Policy.max_line_length") 来控制。 _policy_ 参数可以被用于覆盖从消息实例获取到的默认策略。 这可以被用来控制该方法所产生的某些格式，因为指定的 _policy_ 将被传给 [`Generator`](email.generator.md#email.generator.Generator "email.generator.Generator")。

扁平化信息可能会对 `EmailMessage` 做出修改。这是因为为了完成向字符串的转换，一些内容需要使用默认值填入（举个例子，MIME 边界字段可能会被生成或被修改）。

请注意，这个方法是为了便利而提供，不一定是适合你的应用程序的最理想的序列化信息的方法。这在你处理多封信息的时候尤甚。如果你需要使用更加灵活的API来序列化信息，请参见 [`email.generator.Generator`](email.generator.md#email.generator.Generator "email.generator.Generator") 。同时请注意，当 [`utf8`](email.policy.md#email.policy.EmailPolicy.utf8 "email.policy.EmailPolicy.utf8") 属性为其默认值 `False` 的时候，本方法将限制其行为为生成以“7 bit clean”方式序列化的信息。

在 3.6 版本发生变更: _maxheaderlen_ 没有被指定时的默认行为从默认为0修改为默认为策略的 _max_line_length_ 值。

__str__()¶
~~~
    

~~~
与 `as_string(policy=self.policy.clone(utf8=True))` 等价。这将让 `str(msg)` 产生的字符串包含人类可读的的序列化信息内容。

在 3.4 版本发生变更: 本方法开始使用 `utf8=True` ，而非 `as_string()` 的直接替身。使用 `utf8=True` 会产生类似于 [**RFC 6531**](https://datatracker.ietf.org/doc/html/rfc6531.md) 的信息表达。

as_bytes( _unixfrom =False_, _policy =None_)¶
~~~
    

~~~
以字节串对象的形式返回整个扁平化后的消息。 当可选的 _unixfrom_ 为真值时，返回的字符串会包含信封标头。 _unixfrom_ 的默认值为 `False`。 _policy_ 参数可被用于重载从消息实例获取的默认 policy。 这可被用来控制该方法所产生的部分格式效果，因为指定的 _policy_ 将被传递给 [`BytesGenerator`](email.generator.md#email.generator.BytesGenerator "email.generator.BytesGenerator")。

扁平化信息可能会对 `EmailMessage` 做出修改。这是因为为了完成向字符串的转换，一些内容需要使用默认值填入（举个例子，MIME 边界字段可能会被生成或被修改）。

请注意，这个方法是为了便利而提供，不一定是适合你的应用程序的最理想的序列化信息的方法。这在你处理多封信息的时候尤甚。如果你需要使用更加灵活的API来序列化信息，请参见 [`email.generator.BytesGenerator`](email.generator.md#email.generator.BytesGenerator "email.generator.BytesGenerator") 。

__bytes__()¶
~~~
    

~~~
与 `as_bytes()` 等价。这将让 `bytes(msg)` 产生一个包含序列化信息内容的字节序列对象。

is_multipart()¶
~~~
    

~~~
如果该信息的载荷是一个子 `EmailMessage` 对象列表，返回 `True` ；否则返回 `False` 。在 `is_multipart()` 返回 `True` 的场合下，载荷应当是一个字符串对象（有可能是一个使用了内容传输编码进行编码的二进制载荷）。请注意， `is_multipart()` 返回 `True` 不意味着 `msg.get_content_maintype() == 'multipart'` 也会返回 `True` 。举个例子， `is_multipart` 在 `EmailMessage` 是 `message/rfc822` 类型的信息的情况下，其返回值也是 `True` 。

set_unixfrom( _unixfrom_ )¶
~~~
    

~~~
将信息的信封头设置为 _unixform_ ，这应当是一个字符串。（在 [`mboxMessage`](mailbox.md#mailbox.mboxMessage "mailbox.mboxMessage") 中有关于这个头的一段简短介绍。）

get_unixfrom()¶
~~~
    

~~~
返回消息的信封头。如果信封头从未被设置过，默认返回 `None` 。

以下方法实现了对信息的头字段进行访问的类映射接口。请留意，只是类映射接口，这与平常的映射接口（比如说字典映射）有一些语义上的不同。举个例子，在一个字典当中，键之间不可重复，但是信息头字段是可以重复的。不光如此，在字典当中调用 `keys()` 方法返回的结果，其顺序没有保证；但是在一个 `EmailMessage` 对象当中，返回的头字段永远以其在原信息当中出现的顺序，或以其加入信息的顺序为序。任何删了后又重新加回去的头字段总是添加在当时列表的末尾。

这些语义上的不同是刻意而为之的，是出于在绝大多数常见使用情景中都方便的初衷下设计的。

请注意在任何情况下，消息当中的任何封包标头都不会包含在映射接口当中。

__len__()¶
~~~
    

~~~
返回标头的总数，包括重复项。

__contains__( _name_ )¶
~~~
    

~~~
如果消息对象中有一个名为 _name_ 的字段，其返回值为 `True` 。匹配无视大小写差异， _name_ 也不包含末尾的的冒号。 `in` 操作符的实现中用到了这个方法，比如说：
~~~
    
    
~~~
if 'message-id' in myMessage:
   print('Message-ID:', myMessage['message-id'])
~~~

__getitem__( _name_ )¶

    

~~~
返回头字段名对应的字段值。 _name_ 不含冒号分隔符。如果字段未找到，返回 `None` 。 [`KeyError`](exceptions.md#KeyError "KeyError") 异常永不抛出。

请注意，如果对应名字的字段找到了多个，具体返回哪个字段值是未定义的。请使用 `get_all()` 方法获取当前匹配字段名的所有字段值。

使用标准策略（非 `compat32`）时，返回值是 [`email.headerregistry.BaseHeader`](email.headerregistry.md#email.headerregistry.BaseHeader "email.headerregistry.BaseHeader") 的某个子类的一个实例。

__setitem__( _name_ , _val_ )¶
~~~
    

~~~
在信息头中添加名为 _name_ 值为 _val_ 的字段。这个字段会被添加在已有字段列表的结尾处。

请注意，这个方法 _既不会_ 覆盖 _也不会_ 删除任何字段名重名的已有字段。如果你确实想保证新字段是整个信息头当中唯一拥有 _name_ 字段名的字段，你需要先把旧字段删除。例如：
~~~
    
    
~~~
del msg['subject']
msg['subject'] = 'Python roolz!'
~~~

如果 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 将特定标头定义为唯一的（就像标准策略所做的一样），则当这样的标头已存在时试图为其赋值此方法会引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError")。 采取此种行为是出于保持一致性的考量，但不能依赖它因为在未来我们可能会选择让这样的赋值操作自动删除现有的标头。

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
返回对应字段名的字段值。这个方法与 `__getitem__()` 是一样的，只不过如果对应字段名的字段没有找到，该方法会返回 _failobj_ 。这个参数是可选的（默认值为 `None` ）。

以下是一些与头有关的更多有用方法：

get_all( _name_ , _failobj =None_)¶
~~~
    

~~~
返回字段名为 _name_ 的所有字段值的列表。如果信息内不存在匹配的字段，返回 _failobj_ （其默认值为 `None` ）。

add_header( __name_ , __value_ , _** _params_)¶
~~~
    

~~~
高级头字段设定。这个方法与 `__setitem__()` 类似，不过你可以使用关键字参数为字段提供附加参数。 __name_ 是字段名， __value_ 是字段 _主_ 值。

对于关键字参数字典 __params_ 的每个键值对而言，它的键被用作参数的名字，其中下划线被替换为短横杠（毕竟短横杠不是合法的Python标识符）。一般来讲，参数以 `键="值"` 的方式添加，除非值是 `None` 。要真的是这样的话，只有键会被添加。

如果值含有非 ASCII 字符，你可以将值写成 `(CHARSET, LANGUAGE, VALUE)` 形式的三元组，这样你可以人为控制字符的字符集和语言。 `CHARSET` 是一个字符串，它为你的值的编码命名； `LANGUAGE` 一般可以直接设为 `None` ，也可以直接设为空字符串（其他可能取值参见 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md) ）； `VALUE` 是一个字符串值，其包含非ASCII的码点。如果你没有使用三元组，你的字符串又含有非 ASCII 字符，那么它就会使用 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md) 中， `CHARSET` 为 `utf-8` ， `LANGUAGE` 为 `None` 的格式编码。

例如：
~~~
    
    
~~~
msg.add_header('Content-Disposition', 'attachment', filename='bud.gif')
~~~

会添加一个形如下文的头字段：

    
    
~~~
Content-Disposition: attachment; filename="bud.gif"
~~~

带有非ASCII字符的拓展接口：

    
    
~~~
msg.add_header('Content-Disposition', 'attachment',
               filename=('iso-8859-1', '', 'Fußballer.ppt'))
~~~

replace_header( __name_ , __value_ )¶

    

~~~
替换头字段。只会替换掉信息内找到的第一个字段名匹配 __name_ 的字段值。字段的顺序不变，原字段名的大小写也不变。如果没有找到匹配的字段，抛出 [`KeyError`](exceptions.md#KeyError "KeyError") 异常。

get_content_type()¶
~~~
    

~~~
返回信息的内容类型，其形如 _maintype/subtype_ ，强制全小写。如果信息的 _Content-Type_ 头字段不存在则返回 `get_default_type()` 的返回值；如果信息的 _Content-Type_ 头字段无效则返回 `text/plain` 。

（根据 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md) 所述，信息永远都有一个默认类型，所以 `get_content_type()` 一定会返回一个值。 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md) 定义信息的默认类型为 _text/plain_ 或 _message/rfc822_ ，其中后者仅出现在消息头位于一个 _multipart/digest_ 容器中的场合中。如果消息头的 _Content-Type_ 字段所指定的类型是无效的， [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.md) 令其默认类型为 _text/plain_ 。）

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
设置默认的内容类型。 尽管并非强制，但是 _ctype_ 仍应当是 _text/plain_ 或 _message/rfc822_ 二者取一。默认内容类型并不存储在 _Content-Type_ 头字段当中，所以设置此项的唯一作用就是决定当 _Content-Type_ 头字段在信息中不存在时，`get_content_type` 方法的返回值。

set_param( _param_ , _value_ , _header ='Content-Type'_, _requote =True_, _charset =None_, _language =''_, _replace =False_)¶
~~~
    

~~~
在 _Content-Type_ 头字段当中设置一个参数。如果该参数已于字段中存在，将其旧值替换为 _value_ 。如果 _header_ 是 `Content-Type` （默认值），并且该头字段于信息中尚未存在，则会先添加该字段，将其值设置为 _text/plain_ ，并附加参数值。可选的 _header_ 可以让你指定 _Content-Type_ 之外的另一个头字段。

如果值包含非ASCII字符，其字符集和语言可以通过可选参数 _charset_ 和 _language_ 显式指定。可选参数 _language_ 指定 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.md) 当中的语言，其默认值是空字符串。 _charset_ 和 _language_ 都应当字符串。默认使用的是 `utf8` _charset_ ， _language_ 为 `None` 。

如果 _replace_ 为 `False` （默认值），该头字段会被移动到所有头字段列表的末尾。如果 _replace_ 为 `True` ，字段会被原地更新。

于 `EmailMessage` 对象而言， _requote_ 参数已被弃用。

请注意标头现有的形参值可以通过标头值的 [`params`](email.headerregistry.md#email.headerregistry.ParameterizedMIMEHeader.params "email.headerregistry.ParameterizedMIMEHeader.params") 属性来访问 (例如 `msg['Content-Type'].params['charset']`)。

在 3.4 版本发生变更: 添加了 `replace` 关键字。

del_param( _param_ , _header ='content-type'_, _requote =True_)¶
~~~
    

~~~
从 _Content-Type_ 头字段中完全移去给定的参数。头字段会被原地重写，重写后的字段不含参数和值。可选的 _header_ 可以让你指定 _Content-Type_ 之外的另一个字段。

于 `EmailMessage` 对象而言， _requote_ 参数已被弃用。

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

请注意使用这个方法与直接删除旧的 _Content-Type_ 头字段然后使用 `add_header()` 方法添加一个带有新边界值参数的 _Content-Type_ 头字段有细微差距。 `set_boundary()` 方法会保留 _Content-Type_ 头字段在原信息头当中的位置。

get_content_charset( _failobj =None_)¶
~~~
    

~~~
返回 _Content-Type_ 头字段中的 `charset` 参数，强制小写。如果字段当中没有此参数，或者这个字段压根不存在，返回 _failobj_ 。

get_charsets( _failobj =None_)¶
~~~
    

~~~
返回一个包含了信息内所有字符集名字的列表。 如果信息是 _multipart_ 类型的，那么列表当中的每一项都对应其载荷的子部分的字符集名字。 否则，该列表是一个长度为 1 的列表。

列表当中的每一项都是一个字符串，其值为对应子部分的 _Content-Type_ 头字段的 `charset` 参数值。如果该子部分没有此头字段，或者没有此参数，或者其主要 MIME 类型并非 _text_ ，那么列表中的那一项即为 _failobj_ 。

is_attachment()¶
~~~
    

~~~
如果信息头当中存在一个名为 _Content-Disposition_ 的字段，且该字段的值为 `attachment` （大小写无关），返回 `True` 。否则，返回 `False` 。

在 3.4.2 版本发生变更: 为了与 [`is_multipart()`](email.compat32-message.md#email.message.Message.is_multipart "email.message.Message.is_multipart") 方法一致，is_attachment 现在是一个方法，不再是属性了。

get_content_disposition()¶
~~~
    

~~~
如果信息的 _Content-Disposition_ 头字段存在，返回其字段值；否则返回 `None` 。返回的值均为小写，不包含参数。如果信息遵循 [**RFC 2183**](https://datatracker.ietf.org/doc/html/rfc2183.md) 标准，则此方法的返回值只可能在 _inline_ 、 _attachment_ 和 `None` 之间选择。

在 3.5 版本加入.

下列方法与信息内容（载荷）之访问与操控有关。

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
>>> from email.iterators import _structure
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

get_body( _preferencelist =('related', 'html', 'plain')_)¶

    

~~~
返回信息的 MIME 部分。这个部分是最可能成为信息体的部分。

_preferencelist_ 必须是一个字符串序列，其内容从 `related` 、 `html` 和 `plain` 这三者组成的集合中选取。这个序列代表着返回的部分的内容类型之偏好。

在 `get_body` 方法被调用的对象上寻找匹配的候选者。

如果 `related` 未包括在 _preferencelist_ 中，可考虑将所遇到的任意相关的根部分（或根部分的子部分）在该（子）部分与一个首选项相匹配时作为候选项。

当遇到一个 `multipart/related` 时，将检查 `start` 形参并且如果找到了一个匹配 _Content-ID_ 的部分，在查找候选匹配时只考虑它。 在其他情况下则只考虑 `multipart/related` 的第一个（默认的根）部分。

如果一个部分具有 _Content-Disposition_ 标头，则当标头值为 `inline` 时将只考虑将该部分作为候选匹配。

如果没有任何候选部分匹配 _preferencelist_ 中的任何首选项，则返回 `None`。

注: (1) 对于大多数应用来说有意义的 _preferencelist_ 组合仅有 `('plain',)`, `('html', 'plain')` 以及默认的 `('related', 'html', 'plain')`。 (2) 由于匹配是从调用 `get_body` 的对象开始的，因此在 `multipart/related` 上调用 `get_body` 将返回对象本身，除非 _preferencelist_ 具有非默认值。 (3) 未指定 _Content-Type_ 或者 _Content-Type_ 标头无效的消息（或消息部分）将被当作具有 `text/plain` 类型来处理，这有时可能导致 `get_body` 返回非预期的结果。

iter_attachments()¶
~~~
    

~~~
返回包含所有不是候选 "body" 部分的消息的直接子部分的迭代器。 也就是说，跳过首次出现的每个 `text/plain`, `text/html`, `multipart/related` 或 `multipart/alternative` (除非通过 _Content-Disposition: attachment_ 将它们显式地标记为附件)，并返回所有的其余部分。 当直接应用于 `multipart/related` 时，将返回包含除根部分之外所有相关部分的迭代器（即由 `start` 形参所指向的部分，或者当没有 `start` 形参或 `start` 形参不能匹配任何部分的 _Content-ID_ 时则为第一部分）。 当直接应用于 `multipart/alternative` 或非 `multipart` 时，将返回一个空迭代器。

iter_parts()¶
~~~
    

~~~
返回包含消息的所有直接子部分的迭代器，对于非 `multipart` 将为空对象。 （另请参阅 `walk()`。）

get_content( _* args_, _content_manager =None_, _** kw_)¶
~~~
    

~~~
调用 _content_manager_ 的 [`get_content()`](email.contentmanager.md#email.contentmanager.ContentManager.get_content "email.contentmanager.ContentManager.get_content") 方法，将自身作为消息对象传入，并将其他参数或关键字作为额外参数传入。 如果未指定 _content_manager_ ，则会使用当前 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 所指定的 `content_manager`。

set_content( _* args_, _content_manager =None_, _** kw_)¶
~~~
    

~~~
调用 _content_manager_ 的 [`set_content()`](email.contentmanager.md#email.contentmanager.ContentManager.set_content "email.contentmanager.ContentManager.set_content") 方法，将自身作为消息传入，并将其他参数或关键字作为额外参数传入。 如果未指定 _content_manager_ ，则会使用当前 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 所指定的 `content_manager`。

make_related( _boundary =None_)¶
~~~
    

~~~
将非 `multipart` 消息转换为 `multipart/related` 消息，将任何现有的 _Content-_ 标头和载荷移入 `multipart` 的（新加）首部分。 如果指定了 _boundary_ ，会用它作为 multipart 中的分界字符串，否则会在必要时自动创建分界（例如当消息被序列化时）。

make_alternative( _boundary =None_)¶
~~~
    

~~~
将非 `multipart` 或 `multipart/related` 转换为 `multipart/alternative`，将任何现有的 _Content-_ 标头和载荷移入 `multipart` 的（新加）首部分。 如果指定了 _boundary_ ，会用它作为 multipart 中的分界字符串，否则会在必要时自动创建分界（例如当消息被序列化时）。

make_mixed( _boundary =None_)¶
~~~
    

~~~
将非 `multipart`, `multipart/related` 或 `multipart-alternative` 转换为 `multipart/mixed`，将任何现有的 _Content-_ 标头和载荷移入 `multipart` 的（新加）首部分。 如果指定了 _boundary_ ，会用它作为 multipart 中的分界字符串，否则会在必要时自动创建分界（例如当消息被序列化时）。

add_related( _* args_, _content_manager =None_, _** kw_)¶
~~~
    

~~~
如果消息为 `multipart/related`，则创建一个新的消息对象，将所有参数传给其 `set_content()` 方法，并将其 [`attach()`](email.compat32-message.md#email.message.Message.attach "email.message.Message.attach") 到 `multipart`。 如果消息为非 `multipart`，则先调用 `make_related()` 然后再继续上述步骤。 如果消息为任何其他类型的 `multipart`，则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。 如果未指定 _content_manager_ ，则使用当前 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 所指定的 `content_manager`。 如果添加的部分没有 _Content-Disposition_ 标头，则会添加一个值为 `inline` 的标头。

add_alternative( _* args_, _content_manager =None_, _** kw_)¶
~~~
    

~~~
如果消息为 `multipart/alternative`，则创建一个新的消息对象，将所有参数传给其 `set_content()` 方法，并将其 [`attach()`](email.compat32-message.md#email.message.Message.attach "email.message.Message.attach") 到 `multipart`。 如果消息为非 `multipart` 或 `multipart/related`，则先调用 `make_alternative()` 然后再继续上述步骤。 如果消息为任何其他类型的 `multipart`，则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。 如果未指定 _content_manager_ ，则会使用当前 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 所指定的 `content_manager`。

add_attachment( _* args_, _content_manager =None_, _** kw_)¶
~~~
    

~~~
如果消息为 `multipart/mixed`，则创建一个新的消息对象，将所有参数传给其 `set_content()` 方法，并将其 [`attach()`](email.compat32-message.md#email.message.Message.attach "email.message.Message.attach") 到 `multipart`。 如果消息为非 `multipart`, `multipart/related` 或 `multipart/alternative`，则先调用 `make_mixed()` 然后再继续上述步骤。 如果未指定 _content_manager_ ，则使用当前 [`policy`](email.policy.md#module-email.policy "email.policy: Controlling the parsing and generating of messages") 所指定的 `content_manager`。 如果添加的部分没有 _Content-Disposition_ 标头，则会添加一个值为 `attachment` 的标头。 此方法对于显式附件 ( _Content-Disposition: attachment_ ) 和 `inline` 附件 ( _Content-Disposition: inline_ ) 均可使用，只须向 `content_manager` 传入适当的选项即可。

clear()¶
~~~
    

~~~
移除所有载荷和所有标头。

clear_content()¶
~~~
    

~~~
移除载荷以及所有 _!Content-_ 标头，保持所有其他标头不变并保留其原始顺序。

`EmailMessage` 对象具有下列实例属性:

preamble¶
~~~
    

~~~
MIME 文档格式在标头之后的空白行以及第一个多部分的分界字符串之间允许添加一些文本， 通常，此文本在支持 MIME 的邮件阅读器中永远不可见，因为它处在标准 MIME 保护范围之外。 但是，当查看消息的原始文本，或当在不支持 MIME 的阅读器中查看消息时，此文本会变得可见。

_preamble_ 属性包含 MIME 文档开头部分的这些处于保护范围之外的文本。 当 [`Parser`](email.parser.md#email.parser.Parser "email.parser.Parser") 在标头之后及第一个分界字符串之前发现一些文本时，它会将这些文本赋值给消息的 _preamble_ 属性。 当 [`Generator`](email.generator.md#email.generator.Generator "email.generator.Generator") 写出 MIME 消息的纯文本表示形式时，如果它发现消息具有 _preamble_ 属性，它将在标头及第一个分界之间区域写出这些文本。 请参阅 [`email.parser`](email.parser.md#module-email.parser "email.parser: Parse flat text email messages to produce a message object structure.") 和 [`email.generator`](email.generator.md#module-email.generator "email.generator: Generate flat text email messages from a message structure.") 了解更多细节。

请注意如果消息对象没有前导文本，则 _preamble_ 属性将为 `None`。

epilogue¶
~~~
    

~~~
_epilogue_ 属性的作用方式与 _preamble_ 相同，区别在于它包含在最后一个分界及消息结尾之间出现的文本。 与 `preamble` 类似，如果没有附加文本，则此属性将为 `None`。

defects¶
~~~
    

~~~
_defects_ 属性包含在解析消息时发现的所有问题的列表。 请参阅 [`email.errors`](email.errors.md#module-email.errors "email.errors: The exception classes used by the email package.") 了解可能的解析缺陷的详细描述。

_class _email.message.MIMEPart( _policy =default_)¶
~~~
    

~~~
