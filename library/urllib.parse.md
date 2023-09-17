# `urllib.parse` 用于解析 URL¶

**源代码:** [Lib/urllib/parse.py](https://github.com/python/cpython/tree/3.12/Lib/urllib/parse.py)

* * *

该模块定义了一个标准接口，用于将统一资源定位符（URL）字符串拆分为不同部分（协议、网络位置、路径等），或将各个部分组合回 URL 字符串，并将“相对 URL”转换为基于给定的“基准 URL”的绝对 URL。

该模块被设计为匹配针对相对统一资源定位符的因特网 RFC。 它支持下列 URL 类别: `file`, `ftp`, `gopher`, `hdl`, `http`, `https`, `imap`, `mailto`, `mms`, `news`, `nntp`, `prospero`, `rsync`, `rtsp`, `rtsps`, `rtspu`, `sftp`, `shttp`, `sip`, `sips`, `snews`, `svn`, `svn+ssh`, `telnet`, `wais`, `ws`, `wss`。

`urllib.parse` 模块定义的函数可分为两个主要门类: URL 解析和 URL 转码。 这些函数将在以下各节中详细说明。

## URL 解析¶

URL 解析函数用于将一个 URL 字符串分割成其组成部分，或者将 URL 的多个部分组合成一个 URL 字符串。

urllib.parse.urlparse( _urlstring_ , _scheme =''_, _allow_fragments =True_)¶

    

~~~
将一个 URL 解析为六个部分，返回一个包含 6 项的 [named tuple](../glossary.md#term-named-tuple)。 这对应于 URL 的主要结构: `scheme://netloc/path;parameters?query#fragment`。 每个元组项均为字符串，可能为空字符串。 这些部分不会再被拆分为更小的部分（例如，netloc 将为单个字符串），并且 % 转义不会被扩展。 上面显示的分隔符不会出现在结果中，只有 _path_ 部分的开头斜杠例外，它如果存在则会被保留。 例如:
~~~
    
    
~~~shell
>>> from urllib.parse import urlparse
>>> urlparse("scheme://netloc/path;parameters?query#fragment")
ParseResult(scheme='scheme', netloc='netloc', path='/path;parameters', params='',
            query='query', fragment='fragment')
>>> o = urlparse("http://docs.python.org:80/3/library/urllib.parse.md?"
...              "highlight=params#url-parsing")
>>> o
ParseResult(scheme='http', netloc='docs.python.org:80',
            path='/3/library/urllib.parse.md', params='',
            query='highlight=params', fragment='url-parsing')
>>> o.scheme
'http'
>>> o.netloc
'docs.python.org:80'
>>> o.hostname
'docs.python.org'
>>> o.port
80
>>> o._replace(fragment="").geturl()
'http://docs.python.org:80/3/library/urllib.parse.md?highlight=params'
~~~

根据 [**RFC 1808**](https://datatracker.ietf.org/doc/html/rfc1808.md) 中的语法规范，urlparse 仅在 netloc 前面正确地附带了 '//' 的情况下才会识别它。 否则输入会被当作是一个相对 URL 因而以路径的组成部分开头。

    
    
~~~shell
>>> from urllib.parse import urlparse
>>> urlparse('//www.cwi.nl:80/%7Eguido/Python.md')
ParseResult(scheme='', netloc='www.cwi.nl:80', path='/%7Eguido/Python.md',
            params='', query='', fragment='')
>>> urlparse('www.cwi.nl/%7Eguido/Python.md')
ParseResult(scheme='', netloc='', path='www.cwi.nl/%7Eguido/Python.md',
            params='', query='', fragment='')
>>> urlparse('help/Python.md')
ParseResult(scheme='', netloc='', path='help/Python.md', params='',
            query='', fragment='')
~~~

_scheme_ 参数给出了默认的协议，只有在 URL 未指定协议的情况下才会被使用。 它应该是与 _urlstring_ 相同的类型（文本或字节串），除此之外默认值 `''` 也总是被允许，并会在适当情况下自动转换为 `b''`。

如果 _allow_fragments_ 参数为假值，则片段标识符不会被识别。 它们会被解析为路径、参数或查询部分，在返回值中 `fragment` 会被设为空字符串。

返回值是一个 [named tuple](../glossary.md#term-named-tuple)，这意味着它的条目可以通过索引或作为命名属性来访问，这些属性是：

属性

|

索引

|

值

|

值（如果不存在）  
  
---|---|---|---  
  
`scheme`

|

0

|

URL 协议说明符

|

_scheme_ 参数  
  
`netloc`

|

1

|

网络位置部分

|

空字符串  
  
`path`

|

2

|

分层路径

|

空字符串  
  
`params`

|

3

|

最后路径元素的参数

|

空字符串  
  
`query`

|

4

|

查询组件

|

空字符串  
  
`fragment`

|

5

|

片段标识符

|

空字符串  
  
`username`

|  |

用户名

|

[`None`](constants.md#None "None")  
  
`password`

|  |

密码

|

[`None`](constants.md#None "None")  
  
`hostname`

|  |

主机名（小写）

|

[`None`](constants.md#None "None")  
  
`port`

|  |

端口号为整数（如果存在）

|

[`None`](constants.md#None "None")  
  
如果在 URL 中指定了无效的端口，读取 `port` 属性将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 有关结果对象的更多信息请参阅 结构化解析结果 一节。

在 `netloc` 属性中不匹配的方括号将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

如果 `netloc` 属性中的字符在 NFKC 规范化下（如 IDNA 编码格式所使用的）被分解成 `/`, `?`, `#`, `@` 或 `:` 则将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 如果在解析之前 URL 就被分解，则不会引发错误。

与所有具名元组的情况一样，该子类还有一些特别有用的附加方法和属性。 其中一个方法是 `_replace()`。 `_replace()` 方法将返回一个新的 ParseResult 对象来将指定字段替换为新的值。

    
    
~~~shell
>>> from urllib.parse import urlparse
>>> u = urlparse('//www.cwi.nl:80/%7Eguido/Python.md')
>>> u
ParseResult(scheme='', netloc='www.cwi.nl:80', path='/%7Eguido/Python.md',
            params='', query='', fragment='')
>>> u._replace(scheme='http')
ParseResult(scheme='http', netloc='www.cwi.nl:80', path='/%7Eguido/Python.md',
            params='', query='', fragment='')
~~~

警告

`urlparse()` 不会执行验证。 请参阅 URL 解析安全 了解详情。

在 3.2 版本发生变更: 添加了IPv6 URL解析功能。

在 3.3 版本发生变更: 会对所有 URL 协议解析片段（除非 _allow_fragment_ 为假值），依据 [**RFC 3986**](https://datatracker.ietf.org/doc/html/rfc3986.md) 的规范。 在之前版本中，存在一个支持片段的协议允许名单。

在 3.6 版本发生变更: 超范围的端口号现在会引发 [`ValueError`](exceptions.md#ValueError "ValueError")，而不是返回 [`None`](constants.md#None "None")。

在 3.8 版本发生变更: 在 NFKC 规范化下会影响 netloc 解析的字符现在将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

urllib.parse.parse_qs( _qs_ , _keep_blank_values =False_, _strict_parsing =False_, _encoding ='utf-8'_, _errors ='replace'_, _max_num_fields =None_, _separator ='&'_)¶

    

~~~
解析以字符串参数形式（类型为 _application/x-www-form-urlencoded_ 的数据）给出的查询字符串。 返回字典形式的数据。 结果字典的键为唯一的查询变量名而值为每个变量名对应的值列表。

可选参数 _keep_blank_values_ 是一个旗标，指明是否要将以百分号转码的空值作为空字符串处理。 真值表示空值应当被保留作为空字符串。 默认的假值表示空值会被忽略并将其视作未包括的值。

可选参数 _strict_parsing_ 是一个旗标，指明要如何处理解析错误。 如为假值（默认），错误会被静默地忽略。 如为真值，错误会引发 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。

可选的 _encoding_ 和 _errors_ 形参指定如何将以百分号编码的序列解码为 Unicode 字符，即作为 [`bytes.decode()`](stdtypes.md#bytes.decode "bytes.decode") 方法所接受的数据。

可选参数 _max_num_fields_ 是要读取的最大字段数量的。 如果设置，则如果读取的字段超过 _max_num_fields_ 会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

可选参数 _separator_ 是用来分隔查询参数的符号。 默认为 `&`。

使用 `urllib.parse.urlencode()` 函数 (并将 `doseq` 形参设为 `True`) 将这样的字典转换为查询字符串。

在 3.2 版本发生变更: 增加了 _encoding_ 和 _errors_ 形参。

在 3.8 版本发生变更: 增加了 _max_num_fields_ 形参。

在 3.10 版本发生变更: 增加了 _separator_ 形参，默认值为 `&`。 Python 在早于 Python 3.10 的版本中允许使用 `;` 和 `&` 作为查询参数分隔符。 此设置已被改为只允许单个分隔符键，并以 `&` 作为默认的分隔符。

urllib.parse.parse_qsl( _qs_ , _keep_blank_values =False_, _strict_parsing =False_, _encoding ='utf-8'_, _errors ='replace'_, _max_num_fields =None_, _separator ='&'_)¶
~~~
    

~~~
解析以字符串参数形式（类型为 _application/x-www-form-urlencoded_ 的数据）给出的查询字符串。 数据以字段名和字段值对列表的形式返回。

可选参数 _keep_blank_values_ 是一个旗标，指明是否要将以百分号转码的空值作为空字符串处理。 真值表示空值应当被保留作为空字符串。 默认的假值表示空值会被忽略并将其视作未包括的值。

可选参数 _strict_parsing_ 是一个旗标，指明要如何处理解析错误。 如为假值（默认），错误会被静默地忽略。 如为真值，错误会引发 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。

可选的 _encoding_ 和 _errors_ 形参指定如何将以百分号编码的序列解码为 Unicode 字符，即作为 [`bytes.decode()`](stdtypes.md#bytes.decode "bytes.decode") 方法所接受的数据。

可选参数 _max_num_fields_ 是要读取的最大字段数量的。 如果设置，则如果读取的字段超过 _max_num_fields_ 会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

可选参数 _separator_ 是用来分隔查询参数的符号。 默认为 `&`。

使用 `urllib.parse.urlencode()` 函数将这样的名值对列表转换为查询字符串。

在 3.2 版本发生变更: 增加了 _encoding_ 和 _errors_ 形参。

在 3.8 版本发生变更: 增加了 _max_num_fields_ 形参。

在 3.10 版本发生变更: 增加了 _separator_ 形参，默认值为 `&`。 Python 在早于 Python 3.10 的版本中允许使用 `;` 和 `&` 作为查询参数分隔符。 此设置已被改为只允许单个分隔符键，并以 `&` 作为默认的分隔符。

urllib.parse.urlunparse( _parts_ )¶
~~~
    

~~~
根据 `urlparse()` 所返回的元组来构造一个 URL。 _parts_ 参数可以是任何包含六个条目的可迭代对象。 构造的结果可能是略有不同但保持等价的 URL，如果被解析的 URL 原本包含不必要的分隔符（例如，带有空查询的 `?`；RFC 已声明这是等价的）。

urllib.parse.urlsplit( _urlstring_ , _scheme =''_, _allow_fragments =True_)¶
~~~
    

~~~
此函数类似于 `urlparse()`，但不会拆分来自 URL 的参数。 此函数通常应当在需要允许将参数应用到 URL 的 _path_ 部分的每个分节的较新的 URL 语法的情况下 (参见 [**RFC 2396**](https://datatracker.ietf.org/doc/html/rfc2396.md)) 被用来代替 `urlparse()`。 需要使用一个拆分函数来拆分路径分节和参数。 此函数将返回包含 5 个条目的 [named tuple](../glossary.md#term-named-tuple):
~~~
    
    
~~~
(addressing scheme, network location, path, query, fragment identifier).
~~~

返回值是一个 [named tuple](../glossary.md#term-named-tuple)，它的条目可以通过索引或作为命名属性来访问:

属性

|

索引

|

值

|

值（如果不存在）  
  
---|---|---|---  
  
`scheme`

|

0

|

URL 协议说明符

|

_scheme_ 参数  
  
`netloc`

|

1

|

网络位置部分

|

空字符串  
  
`path`

|

2

|

分层路径

|

空字符串  
  
`query`

|

3

|

查询组件

|

空字符串  
  
`fragment`

|

4

|

片段标识符

|

空字符串  
  
`username`

|  |

用户名

|

[`None`](constants.md#None "None")  
  
`password`

|  |

密码

|

[`None`](constants.md#None "None")  
  
`hostname`

|  |

主机名（小写）

|

[`None`](constants.md#None "None")  
  
`port`

|  |

端口号为整数（如果存在）

|

[`None`](constants.md#None "None")  
  
如果在 URL 中指定了无效的端口，读取 `port` 属性将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 有关结果对象的更多信息请参阅 结构化解析结果 一节。

在 `netloc` 属性中不匹配的方括号将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

如果 `netloc` 属性中的字符在 NFKC 规范化下（如 IDNA 编码格式所使用的）被分解成 `/`, `?`, `#`, `@` 或 `:` 则将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 如果在解析之前 URL 就被分解，则不会引发错误。

按照针对 RFC 3986 进行更新的 [WHATWG spec](https://url.spec.whatwg.org/#concept-basic-url-parser)，打头的 C0 控制符和空格符将从 URL 中去除。 任意位置上的 `\n`, `\r` 和制表符 `\t` 等字符也将从 URL 中去除。at any position.

警告

`urlsplit()` 不会执行验证。 请参阅 URL 解析安全 了解详情。

在 3.6 版本发生变更: 超范围的端口号现在会引发 [`ValueError`](exceptions.md#ValueError "ValueError")，而不是返回 [`None`](constants.md#None "None")。

在 3.8 版本发生变更: 在 NFKC 规范化下会影响 netloc 解析的字符现在将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

在 3.10 版本发生变更: ASCII 换行符和制表符会从 URL 中被去除。

在 3.12 版本发生变更: 打头的 WHATWG C0 控制符和空格符将从 URL 中去除。

urllib.parse.urlunsplit( _parts_ )¶

    

~~~
将 `urlsplit()` 所返回的元组中的元素合并为一个字符串形式的完整 URL。 _parts_ 参数可以是任何包含五个条目的可迭代对象。 其结果可能是略有不同但保持等价的 URL，如果被解析的 URL 原本包含不必要的分隔符（例如，带有空查询的 ?；RFC 已声明这是等价的）。

urllib.parse.urljoin( _base_ , _url_ , _allow_fragments =True_)¶
~~~
    

~~~
通过合并一个 "基准 URL" ( _base_ ) 和另一个 URL ( _url_ ) 来构造一个完整 ("absolute") URL。 在非正式情况下，这将使用基准 URL 的各部分，特别是地址协议、网络位置和 (一部分) 路径来提供相对 URL 中缺失的部分。 例如:
~~~
    
    
~~~shell
>>> from urllib.parse import urljoin
>>> urljoin('http://www.cwi.nl/%7Eguido/Python.md', 'FAQ.md')
'http://www.cwi.nl/%7Eguido/FAQ.md'
~~~

_allow_fragments_ 参数具有与 `urlparse()` 中的对应参数一致的含义与默认值。

备注

如果 _url_ 为绝对 URL (即以 `//` 或 `scheme://` 打头)，则 _url_ 的主机名和/或协议将出现在结果中。 例如:

    
    
~~~shell
>>> urljoin('http://www.cwi.nl/%7Eguido/Python.md',
...         '//www.python.org/%7Eguido')
'http://www.python.org/%7Eguido'
~~~

如果你不想要那样的行为，请使用 `urlsplit()` 和 `urlunsplit()` 对 _url_ 进行预处理，移除可能存在的 _scheme_ 和 _netloc_ 部分。

在 3.5 版本发生变更: 更新行为以匹配 [**RFC 3986**](https://datatracker.ietf.org/doc/html/rfc3986.md) 中定义的语义。

urllib.parse.urldefrag( _url_ )¶

    

~~~
如果 _url_ 包含片段标识符，则返回不带片段标识符的 _url_ 修改版本。 如果 _url_ 中没有片段标识符，则返回未经修改的 _url_ 和一个空字符串。

返回值是一个 [named tuple](../glossary.md#term-named-tuple)，它的条目可以通过索引或作为命名属性来访问:

属性

|

索引

|

值

|

值（如果不存在）  
  
---|---|---|---  
  
`url`

|

0

|

不带片段的 URL

|

空字符串  
  
`fragment`

|

1

|

片段标识符

|

空字符串  
  
请参阅 结构化解析结果 一节了解有关结果对象的更多信息。

在 3.2 版本发生变更: 结果为已构造好的对象而不是一个简单的 2 元组。-tuple.

urllib.parse.unwrap( _url_ )¶
~~~
    

~~~
从已包装的 URL (即被格式化为 `<URL:scheme://host/path>`, `<scheme://host/path>`, `URL:scheme://host/path` 或 `scheme://host/path` 的字符串) 中提取 URL。 如果 _url_ 不是一个已包装的 URL，它将被原样返回。

## URL 解析安全¶

`urlsplit()` 和 `urlparse()` API 不会对输入进行 **验证** 。 它们可能不会因其他应用程序认为不合法的输入而引发错误。 它们还可能在其他地方认为不是 URL 的输入上成功运行。 它们的目标是达成实际的功能而不是保持纯净。

他们在非正常的输入上可能不会引发异常，而是以空字符串的形式返回某些部分。 或者可能会包含某些不应包含的部分。

我们建议这些 API 的用户在任何使用的值具有安全意义的地方应用防御性代码。 在你的代码中进行某些验证之后再信任被返回的组件。 这个 `scheme` 合理吗？那个 `path` 正确吗？ 那个 `hostname` 是否存在怪异之处？等等。

一个 URL 由哪些内容组成并没有通用的良好定义。 不同应用程序有不同的需求和想要的约束。 举例来说现有的 [WHATWG spec](https://url.spec.whatwg.org/#concept-basic-url-parser) 描述了面向用户的 Web 客户端如 Web 浏览器的需求。 而 [**RFC 3986**](https://datatracker.ietf.org/doc/html/rfc3986.md) 则更为一般化。 这些函数涵盖了这两种领域的某些部分，但称不上能兼容任何一种。 这些 API 和早于这两个标准的现有用户代码对于其他特定行为的期望使得我们对 API 行为的更改变得非常谨慎。

## 解析ASCII编码字节¶

这些 URL 解析函数最初设计只用于操作字符串。 但在实践中，它也能够操作经过正确转码和编码的 ASCII 字节序列形式的 URL。 相应地，此模块中的 URL 解析函数既可以操作 [`str`](stdtypes.md#str "str") 对象也可以操作 [`bytes`](stdtypes.md#bytes "bytes") 和 [`bytearray`](stdtypes.md#bytearray "bytearray") 对象。

如果传入 [`str`](stdtypes.md#str "str") 数据，结果将只包含 [`str`](stdtypes.md#str "str") 数据。 如果传入 [`bytes`](stdtypes.md#bytes "bytes") 或 [`bytearray`](stdtypes.md#bytearray "bytearray") 数据，则结果也将只包含 [`bytes`](stdtypes.md#bytes "bytes") 数据。

试图在单个函数调用中混用 [`str`](stdtypes.md#str "str") 数据和 [`bytes`](stdtypes.md#bytes "bytes") 或 [`bytearray`](stdtypes.md#bytearray "bytearray") 数据将导致引发 [`TypeError`](exceptions.md#TypeError "TypeError")，而试图传入非 ASCII 字节值则将引发 [`UnicodeDecodeError`](exceptions.md#UnicodeDecodeError "UnicodeDecodeError")。

为了支持结果对象在 [`str`](stdtypes.md#str "str") 和 [`bytes`](stdtypes.md#bytes "bytes") 之间方便地转换，所有来自 URL 解析函数的返回值都会提供 `encode()` 方法 (当结果包含 [`str`](stdtypes.md#str "str") 数据) 或 `decode()` 方法 (当结果包含 [`bytes`](stdtypes.md#bytes "bytes") 数据)。 这些方法的签名与 [`str`](stdtypes.md#str "str") 和 [`bytes`](stdtypes.md#bytes "bytes") 的对应方法相匹配 (不同之处在于其默认编码格式是 `'ascii'` 而非 `'utf-8'`)。 每个方法会输出包含相应类型的 [`bytes`](stdtypes.md#bytes "bytes") 数据 (对于 `encode()` 方法) 或 [`str`](stdtypes.md#str "str") 数据 (对于 `decode()` 方法) 的值。

对于某些需要在有可能不正确地转码的包含非 ASCII 数据的 URL 上进行操作的应用程序来说，在发起调用 URL 解析方法之前必须自行将字节串解码为字符。

在本节中描述的行为仅适用于 URL 解析函数。 URL 转码函数在产生和消耗字节序列时使用它们自己的规则，详情参见单独 URL 转码函数的文档。

在 3.2 版本发生变更: URL 解析函数现在接受 ASCII 编码的字节序列

## 结构化解析结果¶

`urlparse()`, `urlsplit()` 和 `urldefrag()` 函数的结果对象是 [`tuple`](stdtypes.md#tuple "tuple") 类型的子类。 这些子类中增加了在那些函数的文档中列出的属性，之前小节中描述的编码和解码支持，以及一个附加方法:

urllib.parse.SplitResult.geturl()¶
~~~
    

~~~
以字符串形式返回原始 URL 的重合并版本。 这可能与原始 URL 有所不同，例如协议的名称可能被正规化为小写字母、空的组成部分可能被丢弃。 特别地，空的参数、查询和片段标识符将会被移除。

对于 `urldefrag()` 的结果，只有空的片段标识符会被移除。 对于 `urlsplit()` 和 `urlparse()` 的结果，所有被记录的改变都会被应用到此方法所返回的 URL 上。

如果是通过原始的解析方法传回则此方法的结果会保持不变:
~~~
    
    
~~~shell
>>> from urllib.parse import urlsplit
>>> url = 'HTTP://www.Python.org/doc/#'
>>> r1 = urlsplit(url)
>>> r1.geturl()
'http://www.Python.org/doc/'
>>> r2 = urlsplit(r1.geturl())
>>> r2.geturl()
'http://www.Python.org/doc/'
~~~

下面的类提供了当在 [`str`](stdtypes.md#str "str") 对象上操作时对结构化解析结果的实现:

_class _urllib.parse.DefragResult( _url_ , _fragment_ )¶

    

~~~
用于 `urldefrag()` 结果的实体类，包含有 [`str`](stdtypes.md#str "str") 数据。 `encode()` 方法会返回一个 `DefragResultBytes` 实例。

在 3.2 版本加入.

_class _urllib.parse.ParseResult( _scheme_ , _netloc_ , _path_ , _params_ , _query_ , _fragment_ )¶
~~~
    

~~~
用于 `urlparse()` 结果的实体类，包含有 [`str`](stdtypes.md#str "str") 数据。 `encode()` 方法会返回一个 `ParseResultBytes` 实例。

_class _urllib.parse.SplitResult( _scheme_ , _netloc_ , _path_ , _query_ , _fragment_ )¶
~~~
    

~~~
用于 `urlsplit()` 结果的实体类，包含有 [`str`](stdtypes.md#str "str") 数据。 `encode()` 方法会返回一个 `SplitResultBytes` 实例。

下面的类提供了当在 [`bytes`](stdtypes.md#bytes "bytes") 或 [`bytearray`](stdtypes.md#bytearray "bytearray") 对象上操作时对解析结果的实现:

_class _urllib.parse.DefragResultBytes( _url_ , _fragment_ )¶
~~~
    

~~~
用于 `urldefrag()` 结果的实体类，包含有 [`bytes`](stdtypes.md#bytes "bytes") 数据。 `decode()` 方法会返回一个 `DefragResult` 实例。

在 3.2 版本加入.

_class _urllib.parse.ParseResultBytes( _scheme_ , _netloc_ , _path_ , _params_ , _query_ , _fragment_ )¶
~~~
    

~~~
用于 `urlparse()` 结果的实体类，包含有 [`bytes`](stdtypes.md#bytes "bytes") 数据。 `decode()` 方法会返回一个 `ParseResult` 实例。

在 3.2 版本加入.

_class _urllib.parse.SplitResultBytes( _scheme_ , _netloc_ , _path_ , _query_ , _fragment_ )¶
~~~
    

~~~
用于 `urlsplit()` 结果的实体类，包含有 [`bytes`](stdtypes.md#bytes "bytes") 数据。 `decode()` 方法会返回一个 `SplitResult` 实例。

在 3.2 版本加入.

## URL 转码¶

URL 转码函数的功能是接收程序数据并通过对特殊字符进行转码并正确编码非 ASCII 文本来将其转为可以安全地用作 URL 组成部分的形式。 它们还支持逆转此操作以便从作为 URL 组成部分的内容中重建原始数据，如果上述的 URL 解析函数还未覆盖此功能的话。

urllib.parse.quote( _string_ , _safe ='/'_, _encoding =None_, _errors =None_)¶
~~~
    

~~~
使用 `%xx` 转义符替换 _string_ 中的特殊字符。 字母、数字和 `'_.-~'` 等字符一定不会被转码。 在默认情况下，此函数只对 URL 的路径部分进行转码。 可选的 _safe_ 形参额外指定不应被转码的 ASCII 字符 --- 其默认值为 `'/'`。

_string_ 可以是 [`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 对象。

在 3.7 版本发生变更: 从 [**RFC 2396**](https://datatracker.ietf.org/doc/html/rfc2396.md) 迁移到 [**RFC 3986**](https://datatracker.ietf.org/doc/html/rfc3986.md) 以转码 URL 字符串。 "~" 现在已被包括在非保留字符集中。

可选的 _encoding_ 和 _errors_ 形参指明如何处理非 ASCII 字符，与 [`str.encode()`](stdtypes.md#str.encode "str.encode") 方法所接受的值一样。 _encoding_ 默认为 `'utf-8'`。 _errors_ 默认为 `'strict'`，表示不受支持的字符将引发 [`UnicodeEncodeError`](exceptions.md#UnicodeEncodeError "UnicodeEncodeError")。 如果 _string_ 为 [`bytes`](stdtypes.md#bytes "bytes") 则不可提供 _encoding_ 和 _errors_ ，否则将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

请注意 `quote(string, safe, encoding, errors)` 等价于 `quote_from_bytes(string.encode(encoding, errors), safe)`。

例如: `quote('/El Niño/')` 将产生 `'/El%20Ni%C3%B1o/'`。

urllib.parse.quote_plus( _string_ , _safe =''_, _encoding =None_, _errors =None_)¶
~~~
    

~~~
类似于 `quote()`，但还会使用加号来替换空格，如在构建放入 URL 的查询字符串时对于转码 HTML 表单值时所要求的那样。 原始字符串中的加号会被转义，除非它们已包括在 _safe_ 中。 它也不会将 _safe_ 的默认值设为 `'/'`。

例如: `quote_plus('/El Niño/')` 将产生 `'%2FEl+Ni%C3%B1o%2F'`。

urllib.parse.quote_from_bytes( _bytes_ , _safe ='/'_)¶
~~~
    

~~~
类似于 `quote()`，但是接受 [`bytes`](stdtypes.md#bytes "bytes") 对象而非 [`str`](stdtypes.md#str "str")，并且不执行从字符串到字节串的编码。

例如: `quote_from_bytes(b'a&\xef')` 将产生 `'a%26%EF'`。

urllib.parse.unquote( _string_ , _encoding ='utf-8'_, _errors ='replace'_)¶
~~~
    

~~~
将 `%xx` 转义符替换为其单字符等价物。 可选的 _encoding_ 和 _errors_ 形参指定如何将以百分号编码的序列解码为 Unicode 字符，即 [`bytes.decode()`](stdtypes.md#bytes.decode "bytes.decode") 方法所接受的数据。

_string_ 可以是 [`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 对象。

_encoding_ 默认为 `'utf-8'`。 _errors_ 默认为 `'replace'`，表示无效的序列将被替换为占位字符。

例如: `unquote('/El%20Ni%C3%B1o/')` 将产生 `'/El Niño/'`。

在 3.9 版本发生变更: _string_ 形参支持 bytes 和 str 对象（之前仅支持 str）。

urllib.parse.unquote_plus( _string_ , _encoding ='utf-8'_, _errors ='replace'_)¶
~~~
    

~~~
类似于 `unquote()`，但还会将加号替换为空格，如反转码表单值所要求的。

_string_ 必须为 [`str`](stdtypes.md#str "str")。

例如: `unquote_plus('/El+Ni%C3%B1o/')` 将产生 `'/El Niño/'`。

urllib.parse.unquote_to_bytes( _string_ )¶
~~~
    

~~~
将 `%xx` 转义符替换为其单八位等价物，并返回一个 [`bytes`](stdtypes.md#bytes "bytes") 对象。

_string_ 可以是 [`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 对象。

如果它是 [`str`](stdtypes.md#str "str")，则 _string_ 中未转义的非 ASCII 字符会被编码为 UTF-8 字节串。

例如: `unquote_to_bytes('a%26%EF')` y将产生 `b'a&\xef'`。

urllib.parse.urlencode( _query_ , _doseq =False_, _safe =''_, _encoding =None_, _errors =None_, _quote_via =quote_plus_)¶
~~~
    

~~~
将一个包含有 [`str`](stdtypes.md#str "str") 或 [`bytes`](stdtypes.md#bytes "bytes") 对象的映射对象或二元组序列转换为以百分号编码的 ASCII 文本字符串。 如果所产生的字符串要被用作 [`urlopen()`](urllib.request.md#urllib.request.urlopen "urllib.request.urlopen") 函数的 POST 操作的 _data_ ，则它应当被编码为字节串，否则它将导致 [`TypeError`](exceptions.md#TypeError "TypeError")。

结果字符串是一系列 `key=value` 对，由 `'&'` 字符进行分隔，其中 _key_ 和 _value_ 都已使用 _quote_via_ 函数转码。 在默认情况下，会使用 `quote_plus()` 来转码值，这意味着空格会被转码为 `'+'` 字符而 '/' 字符会被转码为 `%2F`，即遵循 GET 请求的标准 (`application/x-www-form-urlencoded`)。 另一个可以作为 _quote_via_ 传入的替代函数是 `quote()`，它将把空格转码为 `%20` 并且不编码 '/' 字符。 为了最大程度地控制要转码的内容，请使用 `quote` 并指定 _safe_ 的值。

当使用二元组序列作为 _query_ 参数时，每个元组的第一个元素为键而第二个元素为值。 值元素本身也可以为一个序列，在那种情况下，如果可选的形参 _doseq_ 的值为 `True`，则每个键的值序列元素生成单个 `key=value` 对（以 `'&'` 分隔）。 被编码的字符串中的参数顺序将与序列中的形参元素顺序相匹配。

_safe_ , _encoding_ 和 _errors_ 形参会被传递给 _quote_via_ ( _encoding_ 和 _errors_ 形参仅在查询元素为 [`str`](stdtypes.md#str "str") 时会被传递)。

为了反向执行这个编码过程，此模块提供了 `parse_qs()` 和 `parse_qsl()` 来将查询字符串解析为 Python 数据结构。

请参考 [urllib 示例](urllib.request.md#urllib-examples) 来了解如何使用 `urllib.parse.urlencode()` 方法来生成 URL 的查询字符串或 POST 请求的数据。

在 3.2 版本发生变更: 查询支持字节和字符串对象。

在 3.5 版本加入: _quote_via_ 参数.

参见

[WHATWG](https://url.spec.whatwg.org/) \- URL 现有标准
~~~
    

~~~
定义 URL、域名、IP 地址、application/x-www-form-urlencoded 格式及其 API 的工作组。

[**RFC 3986**](https://datatracker.ietf.org/doc/html/rfc3986.md) \- 统一资源标识符
~~~
    

~~~
这是当前的标准 (STD66)。 任何对于 urllib.parse 模块的修改都必须遵循该标准。 某些偏离也可能会出现，这大都是出于向下兼容的目的以及特定的经常存在于各主要浏览器上的实际解析需求。

[**RFC 2732**](https://datatracker.ietf.org/doc/html/rfc2732.md) \- URL 中的 IPv6 Addresses 地址显示格式。
~~~
    

~~~
这指明了 IPv6 URL 的解析要求。

[**RFC 2396**](https://datatracker.ietf.org/doc/html/rfc2396.md) \- 统一资源标识符（URI）：通用语法
~~~
    

~~~
描述统一资源名称 (URN) 和统一资源定位符 (URL) 通用语义要求的文档。

[**RFC 2368**](https://datatracker.ietf.org/doc/html/rfc2368.md) \- mailto URL 模式。
~~~
    

~~~
mailto URL 模式的解析要求。

[**RFC 1808**](https://datatracker.ietf.org/doc/html/rfc1808.md) \- 相对统一资源定位符
~~~
    

~~~
这个请求注释包括联结绝对和相对 URL 的规则，其中包括大量控制边界情况处理的 "异常示例"。

[**RFC 1738**](https://datatracker.ietf.org/doc/html/rfc1738.md) \- 统一资源定位符 (URL)
~~~
    

~~~
