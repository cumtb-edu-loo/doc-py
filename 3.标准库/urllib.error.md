# `urllib.error` \--- urllib.request 引发的异常类¶

**源代码：** [Lib/urllib/error.py](https://github.com/python/cpython/tree/3.12/Lib/urllib/error.py)

* * *

`urllib.error` 模块为 [`urllib.request`](urllib.request.md#module-urllib.request "urllib.request: Extensible library for opening URLs.") 所引发的异常定义了异常类。 基础异常类是 `URLError`。

下列异常会被 `urllib.error` 按需引发：

_exception _urllib.error.URLError¶

    

~~~
处理程序在遇到问题时会引发此异常（或其派生的异常）。 它是 [`OSError`](exceptions.md#OSError "OSError") 的一个子类。

reason¶
~~~
    

~~~
此错误的原因。 它可以是一个消息字符串或另一个异常实例。

在 3.3 版本发生变更: `URLError` 已被设为 [`OSError`](exceptions.md#OSError "OSError") 而不是 [`IOError`](exceptions.md#IOError "IOError") 的子类。

_exception _urllib.error.HTTPError( _url_ , _code_ , _msg_ , _hdrs_ , _fp_ )¶
~~~
    

~~~
虽然是一个异常（`URLError` 的一个子类），`HTTPError` 也可以作为一个非异常的文件类返回值（与 [`urlopen()`](urllib.request.md#urllib.request.urlopen "urllib.request.urlopen") 返所回的对象相同）。 这适用于处理特殊 HTTP 错误例如作为认证请求的时候。

url¶
~~~
    

~~~
包含请求 URL。 是 _filename_ 属性的别名。

code¶
~~~
    

~~~
一个 HTTP 状态码，具体定义见 [**RFC 2616**](https://datatracker.ietf.org/doc/html/rfc2616.md)。 这个数字的值对应于存放在 [`http.server.BaseHTTPRequestHandler.responses`](http.server.md#http.server.BaseHTTPRequestHandler.responses "http.server.BaseHTTPRequestHandler.responses") 代码字典中的某个值。

reason¶
~~~
    

~~~
这通常是一个解释本次错误原因的字符串。 为 _msg_ 属性的别名。

headers¶
~~~
    

~~~
导致 `HTTPError` 的特定 HTTP 请求的 HTTP 响应头。 为 _hdrs_ 属性的别名。

在 3.4 版本加入.

fp¶
~~~
    

~~~
可供读取 HTTP 错误消息体的文件类对象。

_exception _urllib.error.ContentTooShortError( _msg_ , _content_ )¶
~~~
    

~~~
此异常会在 [`urlretrieve()`](urllib.request.md#urllib.request.urlretrieve "urllib.request.urlretrieve") 函数检测到已下载的数据量少于预期量（由 _Content-Length_ 标头给出）时被引发。

content¶
~~~
    

~~~
