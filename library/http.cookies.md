# `http.cookies` \--- HTTP状态管理¶

**源代码:** [Lib/http/cookies.py](https://github.com/python/cpython/tree/3.12/Lib/http/cookies.py)

* * *

`http.cookies` 模块定义的类将 cookie 的概念抽象了出来，这是一种 HTTP 状态的管理机制。它既支持简单的纯字符串形式的 cookie，也为任何可序列化数据类型的 cookie 提供抽象。

以前，该模块严格套用 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.md) 和 [**RFC 2068**](https://datatracker.ietf.org/doc/html/rfc2068.md) 规范中描述的解析规则。后来人们发现，MSIE 3.0 并不遵循这些规范中的字符规则，而且目前许多浏览器和服务器在处理 cookie 时也放宽了解析规则。 因此，这里用到的解析规则没有那么严格。

字符集 [`string.ascii_letters`](string.md#string.ascii_letters "string.ascii_letters") 、 [`string.digits`](string.md#string.digits "string.digits") 和 `!#$%&'*+-.^_`|~:` 给出了本模块允许出现在 cookie 名称中的有效字符集（如 `key`）。

在 3.3 版本发生变更: “:”字符可用于有效的 cookie 名称。

备注

当遇到无效 cookie 时会触发 `CookieError`，所以若 cookie 数据来自浏览器，一定要做好应对无效数据的准备，并在解析时捕获 `CookieError`。

_exception _http.cookies.CookieError¶

    

~~~
出现异常的原因，可能是不符合 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.md) ：属性不正确、 _Set-Cookie_ 头部信息不正确等等。

_class _http.cookies.BaseCookie([ _input_ ])¶
~~~
    

~~~
类似字典的对象，字典键为字符串，字典值是 `Morsel` 实例。请注意，在将键值关联时，首先会把值转换为包含键和值的 `Morsel` 对象。

若给出 _input_ ，将会传给 `load()` 方法。

_class _http.cookies.SimpleCookie([ _input_ ])¶
~~~
    

~~~
该类派生于 `BaseCookie`，并覆盖了 `value_decode()` 和 `value_encode()` 方法。SimpleCookie 允许用字符串作为 cookie 值。在设置值时，SimpleCookie 将调用内置的 [`str()`](stdtypes.md#str "str") 将其转换为字符串。从 HTTP 接收到的值将作为字符串保存。

参见

[`http.cookiejar`](http.cookiejar.md#module-http.cookiejar "http.cookiejar: Classes for automatic handling of HTTP cookies.") 模块
~~~
    

~~~
处理网络 _客户端_ 的 HTTP cookie。 [`http.cookiejar`](http.cookiejar.md#module-http.cookiejar "http.cookiejar: Classes for automatic handling of HTTP cookies.") 和 `http.cookies` 模块相互没有依赖关系。

[**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.md) \- HTTP状态管理机制
~~~
    

~~~
这是本模块实现的状态管理规范。

## Cookie 对象¶

BaseCookie.value_decode( _val_ )¶
~~~
    

~~~
由字符串返回元组 `(real_value, coded_value)`。`real_value` 可为任意类型。`BaseCookie` 中的此方法未实现任何解码工作——只为能被子类重写。

BaseCookie.value_encode( _val_ )¶
~~~
    

~~~
返回元组 `(real_value, coded_value)`。 _val_ 可为任意类型，`coded_value` 则会转换为字符串。 `BaseCookie` 中的此方法未实现任何编码工作——只为能被子类重写。

通常在 _value_decode_ 的取值范围内，`value_encode()` 和 `value_decode()` 应为可互逆操作。

BaseCookie.output( _attrs =None_, _header ='Set-Cookie:'_, _sep ='\r\n'_)¶
~~~
    

~~~
返回可作为 HTTP 标头信息发送的字符串表示。 _attrs_ 和 _header_ 会传给每个 `Morsel` 的 `output()` 方法。 _sep_ 用来将标头连接在一起，默认为 `'\r\n'` (CRLF) 组合。

BaseCookie.js_output( _attrs =None_)¶
~~~
    

~~~
返回一段可供嵌入的 JavaScript 代码，若在支持 JavaScript 的浏览器上运行，其作用如同发送 HTTP 头部信息一样。

_attrs_ 的含义与 `output()` 的相同。

BaseCookie.load( _rawdata_ )¶
~~~
    

~~~
若 _rawdata_ 为字符串，则会作为 `HTTP_COOKIE` 进行解析，并将找到的值添加为 `Morsel`。 如果是字典值，则等价于:
~~~
    
    
~~~
for k, v in rawdata.items():
    cookie[k] = v
~~~

## Morsel 对象¶

_class _http.cookies.Morsel¶

    

~~~
对键/值对的抽象，带有 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.md) 的部分属性。

morsel 对象类似于字典，键的组成是常量——均为有效的 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.md) 属性，包括：

  * `expires`

  * `path`

  * `comment`

  * `domain`

  * `max-age`

  * `secure`

  * `version`

  * `httponly`

  * `samesite`

`httponly` 属性指明了该 cookie 仅在 HTTP 请求中传输，且不能通过 JavaScript 访问。这是为了减轻某些跨站脚本攻击的危害。

`samesite` 属性指明了浏览器不得与跨站请求一起发送该 cookie。这有助于减轻 CSRF 攻击的危害。此属性的有效值为 “Strict”和“Lax”。

键不区分大小写，默认值为 `''`。

在 3.5 版本发生变更: 现在，`__eq__()` 会同时考虑 `key` 和 `value` 。

在 3.7 版本发生变更: Attributes `key`, `value` and `coded_value` are read-only. Use `set()` for setting them.

在 3.8 版本发生变更: 增加对 `samesite` 属性的支持。

Morsel.value¶
~~~
    

~~~
Cookie的值。

Morsel.coded_value¶
~~~
    

~~~
编码后的 cookie 值——也即要发送的内容。

Morsel.key¶
~~~
    

~~~
cookie 名称

Morsel.set( _key_ , _value_ , _coded_value_ )¶
~~~
    

~~~
设置 _key_ 、 _value_ 和 _coded_value_ 属性。

Morsel.isReservedKey( _K_ )¶
~~~
    

~~~
判断 _K_ 是否属于 `Morsel` 的键。

Morsel.output( _attrs =None_, _header ='Set-Cookie:'_)¶
~~~
    

~~~
返回 morsel 的字符串形式，适用于作为 HTTP 头部信息进行发送。默认包含所有属性，除非给出 _attrs_ 属性列表。 _header_ 默认为 `"Set-Cookie:"`。

Morsel.js_output( _attrs =None_)¶
~~~
    

~~~
返回一段可供嵌入的 JavaScript 代码，若在支持 JavaScript 的浏览器上运行，其作用如同发送 HTTP 头部信息一样。

_attrs_ 的含义与 `output()` 的相同。

Morsel.OutputString( _attrs =None_)¶
~~~
    

~~~
返回 morsel 的字符串形式，不含 HTTP 或 JavaScript 数据。

_attrs_ 的含义与 `output()` 的相同。

Morsel.update( _values_ )¶
~~~
    

~~~
用字典 _values_ 中的值更新 morsel 字典中的值。若有 _values_ 字典中的键不是有效的 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.md) 属性，则会触发错误。

在 3.5 版本发生变更: 无效键会触发错误。

Morsel.copy( _value_ )¶
~~~
    

~~~
返回 morsel 对象的浅表复制副本。

在 3.5 版本发生变更: 返回一个 morsel 对象，而非字典。

Morsel.setdefault( _key_ , _value =None_)¶
~~~
    

~~~
若 key 不是有效的 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.md) 属性则触发错误，否则与 [`dict.setdefault()`](stdtypes.md#dict.setdefault "dict.setdefault") 相同。

## 示例¶

以下例子演示了 `http.cookies` 模块的用法。
~~~
    
    
~~~shell
>>> from http import cookies
>>> C = cookies.SimpleCookie()
>>> C["fig"] = "newton"
>>> C["sugar"] = "wafer"
>>> print(C) # generate HTTP headers
Set-Cookie: fig=newton
Set-Cookie: sugar=wafer
>>> print(C.output()) # same thing
Set-Cookie: fig=newton
Set-Cookie: sugar=wafer
>>> C = cookies.SimpleCookie()
>>> C["rocky"] = "road"
>>> C["rocky"]["path"] = "/cookie"
>>> print(C.output(header="Cookie:"))
Cookie: rocky=road; Path=/cookie
>>> print(C.output(attrs=[], header="Cookie:"))
Cookie: rocky=road
>>> C = cookies.SimpleCookie()
>>> C.load("chips=ahoy; vienna=finger") # load from a string (HTTP header)
>>> print(C)
Set-Cookie: chips=ahoy
Set-Cookie: vienna=finger
>>> C = cookies.SimpleCookie()
>>> C.load('keebler="E=everybody; L=\\"Loves\\"; fudge=\\012;";')
>>> print(C)
Set-Cookie: keebler="E=everybody; L=\"Loves\"; fudge=\012;"
>>> C = cookies.SimpleCookie()
>>> C["oreo"] = "doublestuff"
>>> C["oreo"]["path"] = "/"
>>> print(C)
Set-Cookie: oreo=doublestuff; Path=/
>>> C = cookies.SimpleCookie()
>>> C["twix"] = "none for you"
>>> C["twix"].value
'none for you'
>>> C = cookies.SimpleCookie()
>>> C["number"] = 7 # equivalent to C["number"] = str(7)
>>> C["string"] = "seven"
>>> C["number"].value
'7'
>>> C["string"].value
'seven'
>>> print(C)
Set-Cookie: number=7
Set-Cookie: string=seven
~~~

