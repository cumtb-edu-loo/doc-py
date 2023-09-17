# `http` \--- HTTP 模块¶

**源代码:** [Lib/http/__init__.py](https://github.com/python/cpython/tree/3.12/Lib/http/__init__.py)

* * *

`http` 是一个包，它收集了多个用于处理超文本传输协议的模块:

  * [`http.client`](http.client.md#module-http.client "http.client: HTTP and HTTPS protocol client \(requires sockets\).") 是一个底层的 HTTP 协议客户端；对于高层级的 URL 访问请使用 [`urllib.request`](urllib.request.md#module-urllib.request "urllib.request: Extensible library for opening URLs.")

  * [`http.server`](http.server.md#module-http.server "http.server: HTTP server and request handlers.") 包含基于 [`socketserver`](socketserver.md#module-socketserver "socketserver: A framework for network servers.") 的基本 HTTP 服务类

  * [`http.cookies`](http.cookies.md#module-http.cookies "http.cookies: Support for HTTP state management \(cookies\).") 包含一些有用来实现通过 cookies 进行状态管理的工具

  * [`http.cookiejar`](http.cookiejar.md#module-http.cookiejar "http.cookiejar: Classes for automatic handling of HTTP cookies.") 提供了 cookies 的持久化

`http` 模块还定义了下列枚举来帮助你使用 http 相关的代码:

_class _http.HTTPStatus¶

    

~~~
在 3.5 版本加入.

[`enum.IntEnum`](enum.md#enum.IntEnum "enum.IntEnum") 的子类，它定义了组 HTTP 状态码，原理短语以及用英语书写的长描述文本。

用法：
~~~
    
    
~~~shell
>>> from http import HTTPStatus
>>> HTTPStatus.OK
HTTPStatus.OK
>>> HTTPStatus.OK == 200
True
>>> HTTPStatus.OK.value
200
>>> HTTPStatus.OK.phrase
'OK'
>>> HTTPStatus.OK.description
'Request fulfilled, document follows'
>>> list(HTTPStatus)
[HTTPStatus.CONTINUE, HTTPStatus.SWITCHING_PROTOCOLS, ...]
~~~

## HTTP 状态码¶

已支持的，[IANA 注册的状态码](https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml) 在 `http.HTTPStatus` 中可用的有:

双字母代码

|

映射名

|

详情  
  
---|---|---  
  
`100`

|

`CONTINUE`：继续

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), 6.2.1 节  
  
`101`

|

`SWITCHING_PROTOCOLS`

|

HTTP/1.1 RFC 7231, 6.2.2 节  
  
`102`

|

`PROCESSING`

|

WebDAV RFC 2518, 10.1 节  
  
`103`

|

`EARLY_HINTS`

|

用于指定提示 [**RFC 8297**](https://datatracker.ietf.org/doc/html/rfc8297.md) 的 HTTP 状态码  
  
`200`

|

`OK`

|

HTTP/1.1 RFC 7231, 6.3.1 节  
  
`201`

|

`CREATED`

|

HTTP/1.1 RFC 7231, 6.3.2 节  
  
`202`

|

`ACCEPTED`

|

HTTP/1.1 RFC 7231, 6.3.3 节  
  
`203`

|

`NON_AUTHORITATIVE_INFORMATION`

|

HTTP/1.1 RFC 7231, 6.3.4 节  
  
`204`

|

`NO_CONTENT`: 没有内容

|

HTTP/1.1 RFC 7231, 6.3.5 节  
  
`205`

|

`RESET_CONTENT`

|

HTTP/1.1 RFC 7231, 6.3.6 节  
  
`206`

|

`PARTIAL_CONTENT`

|

HTTP/1.1 RFC 7233, 4.1 节  
  
`207`

|

`MULTI_STATUS`

|

WebDAV RFC 4918, 11.1 节  
  
`208`

|

`ALREADY_REPORTED`

|

WebDAV Binding Extensions RFC 5842, 7.1 节（实验性）  
  
`226`

|

`IM_USED`

|

Delta Encoding in HTTP RFC 3229, 10.4.1 节  
  
`300`

|

`MULTIPLE_CHOICES`：有多种资源可选择

|

HTTP/1.1 RFC 7231, 6.4.1 节  
  
`301`

|

`MOVED_PERMANENTLY`：永久移动

|

HTTP/1.1 RFC 7231, 6.4.2 节  
  
`302`

|

`FOUND`：临时移动

|

HTTP/1.1 RFC 7231, 6.4.3 节  
  
`303`

|

`SEE_OTHER`：已经移动

|

HTTP/1.1 RFC 7231, 6.4.4 节  
  
`304`

|

`NOT_MODIFIED`：没有修改

|

HTTP/1.1 RFC 7232, 4.1 节  
  
`305`

|

`USE_PROXY`：使用代理

|

HTTP/1.1 RFC 7231, 6.4.5 节  
  
`307`

|

`TEMPORARY_REDIRECT`：临时重定向

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), 6.4.7 节  
  
`308`

|

`PERMANENT_REDIRECT`：永久重定向

|

Permanent Redirect [**RFC 7238**](https://datatracker.ietf.org/doc/html/rfc7238.md), Section 3 (Experimental)  
  
`400`

|

`BAD_REQUEST`：错误请求

|

HTTP/1.1 RFC 7231, 6.5.1 节  
  
`401`

|

`UNAUTHORIZED`：未授权

|

HTTP/1.1 Authentication RFC 7235, 3.1 节  
  
`402`

|

`PAYMENT_REQUIRED`：保留，将来使用

|

HTTP/1.1 RFC 7231, 6.5.2 节  
  
`403`

|

`FORBIDDEN`：禁止

|

HTTP/1.1 RFC 7231, 6.5.3 节  
  
`404`

|

`NOT_FOUND`：没有找到

|

HTTP/1.1 RFC 7231, 6.5.4 节  
  
`405`

|

`METHOD_NOT_ALLOWED`：该请求方法不允许

|

HTTP/1.1 RFC 7231, 6.5.5 节  
  
`406`

|

`NOT_ACCEPTABLE`：不可接受

|

HTTP/1.1 RFC 7231, 6.5.6 节  
  
`407`

|

`PROXY_AUTHENTICATION_REQUIRED`：要求使用代理验明正身

|

HTTP/1.1 Authentication RFC 7235, 3.1 节  
  
`408`

|

`REQUEST_TIMEOUT`：请求超时

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), 6.5.7 节  
  
`409`

|

`CONFLICT`：冲突

|

HTTP/1.1 RFC 7231, 6.5.8 节  
  
`410`

|

`GONE`：已经不在了

|

HTTP/1.1 RFC 7231, 6.5.9 节  
  
`411`

|

`LENGTH_REQUIRED`：长度要求

|

HTTP/1.1 RFC 7231, 6.5.10 节  
  
`412`

|

`PRECONDITION_FAILED`：前提条件错误

|

HTTP/1.1 RFC 7232, 4.2 节  
  
`413`

|

`REQUEST_ENTITY_TOO_LARGE`：请求体太大了

|

HTTP/1.1 RFC 7231, 6.5.11 节  
  
`414`

|

`REQUEST_URI_TOO_LONG`：请求URI太长了

|

HTTP/1.1 RFC 7231, 6.5.12 节  
  
`415`

|

`UNSUPPORTED_MEDIA_TYPE`：不支持的媒体格式

|

HTTP/1.1 RFC 7231, 6.5.13 节  
  
`416`

|

`REQUESTED_RANGE_NOT_SATISFIABLE`

|

HTTP/1.1 Range Requests RFC 7233, 4.4 节  
  
`417`

|

`EXPECTATION_FAILED`：期望失败

|

HTTP/1.1 RFC 7231, 6.5.14 节  
  
`418`

|

`IM_A_TEAPOT`

|

HTCPCP/1.0 [**RFC 2324**](https://datatracker.ietf.org/doc/html/rfc2324.md), Section 2.3.2  
  
`421`

|

`MISDIRECTED_REQUEST`

|

HTTP/2 [**RFC 7540**](https://datatracker.ietf.org/doc/html/rfc7540.md), 9.1.2 节  
  
`422`

|

`UNPROCESSABLE_ENTITY`：可加工实体

|

WebDAV RFC 4918, 11.2 节  
  
`423`

|

`LOCKED`：锁着

|

WebDAV RFC 4918, 11.3 节  
  
`424`

|

`FAILED_DEPENDENCY`：失败的依赖

|

WebDAV RFC 4918, 11.4 节  
  
`425`

|

`TOO_EARLY`

|

使用 HTTP [**RFC 8470**](https://datatracker.ietf.org/doc/html/rfc8470.md) 中的早期数据  
  
`426`

|

`UPGRADE_REQUIRED`：升级需要

|

HTTP/1.1 RFC 7231, 6.5.15 节  
  
`428`

|

`PRECONDITION_REQUIRED`：先决条件要求

|

Additional HTTP Status Codes RFC 6585  
  
`429`

|

`TOO_MANY_REQUESTS`：太多的请求

|

Additional HTTP Status Codes RFC 6585  
  
`431`

|

`REQUEST_HEADER_FIELDS_TOO_LARGE`：请求头太大

|

Additional HTTP Status Codes RFC 6585  
  
`451`

|

`UNAVAILABLE_FOR_LEGAL_REASONS`

|

HTTP 状态码用于报告法律障碍 [**RFC 7725**](https://datatracker.ietf.org/doc/html/rfc7725.md)  
  
`500`

|

`INTERNAL_SERVER_ERROR`：内部服务错误

|

HTTP/1.1 RFC 7231, 6.6.1 节  
  
`501`

|

`NOT_IMPLEMENTED`：不可执行

|

HTTP/1.1 RFC 7231, 6.6.2 节  
  
`502`

|

`BAD_GATEWAY`：无效网关

|

HTTP/1.1 RFC 7231, 6.6.3 节  
  
`503`

|

`SERVICE_UNAVAILABLE`：服务不可用

|

HTTP/1.1 RFC 7231, 6.6.4 节  
  
`504`

|

`GATEWAY_TIMEOUT`：网关超时

|

HTTP/1.1 RFC 7231, 6.6.5 节  
  
`505`

|

`HTTP_VERSION_NOT_SUPPORTED`：HTTP版本不支持

|

HTTP/1.1 RFC 7231, 6.6.6 节  
  
`506`

|

`VARIANT_ALSO_NEGOTIATES`：服务器存在内部配置错误

|

透明内容协商在： HTTP [**RFC 2295**](https://datatracker.ietf.org/doc/html/rfc2295.md), 8.1 节（实验性）  
  
`507`

|

`INSUFFICIENT_STORAGE`：存储不足

|

WebDAV RFC 4918, 11.5 节  
  
`508`

|

`LOOP_DETECTED`：循环检测

|

WebDAV Binding Extensions RFC 5842, 7.2 节（实验性）  
  
`510`

|

`NOT_EXTENDED`：不扩展

|

WebDAV Binding Extensions RFC 5842, 7.2 节（实验性）  
  
`511`

|

`NETWORK_AUTHENTICATION_REQUIRED`：要求网络身份验证

|

Additional HTTP Status Codes [**RFC 6585**](https://datatracker.ietf.org/doc/html/rfc6585.md), 6 节  
  
为了保持向后兼容性，枚举值也以常量形式出现在 [`http.client`](http.client.md#module-http.client "http.client: HTTP and HTTPS protocol client \(requires sockets\).") 模块中，。 枚举名等于常量名 (例如 `http.HTTPStatus.OK` 也可以是 `http.client.OK`)。

在 3.7 版本发生变更: 添加了 `421 MISDIRECTED_REQUEST` 状态码。

在 3.8 版本加入: 添加了 `451 UNAVAILABLE_FOR_LEGAL_REASONS` 状态码。

在 3.9 版本加入: 增加了 `103 EARLY_HINTS`, `418 IM_A_TEAPOT` 和 `425 TOO_EARLY` 状态码

## HTTP 状态类别¶

在 3.12 版本加入.

这些枚举值具有一些用于指明 HTTP 状态类别的特征属性:

特征属性

|

表示

|

详情  
  
---|---|---  
  
`is_informational`

|

`100 <= status <= 199`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 6  
  
`is_success`

|

`200 <= status <= 299`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 6  
  
`is_redirection`

|

`300 <= status <= 399`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 6  
  
`is_client_error`

|

`400 <= status <= 499`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 6  
  
`is_server_error`

|

`500 <= status <= 599`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 6  
  
> 用法：
>  
>  
>     >>> from http import HTTPStatus
>     >>> HTTPStatus.OK.is_success
>     True
>     >>> HTTPStatus.OK.is_client_error
>     False
>  

_class _http.HTTPMethod¶

    

~~~
在 3.11 版本加入.

一个 [`enum.StrEnum`](enum.md#enum.StrEnum "enum.StrEnum") 的子类，它定义了一组 HTTP 方法以及用英文书写的描述。

用法：
~~~
    
    
~~~shell
>>> from http import HTTPMethod
>>>
>>> HTTPMethod.GET
<HTTPMethod.GET>
>>> HTTPMethod.GET == 'GET'
True
>>> HTTPMethod.GET.value
'GET'
>>> HTTPMethod.GET.description
'Retrieve the target.'
>>> list(HTTPMethod)
[<HTTPMethod.CONNECT>,
 <HTTPMethod.DELETE>,
 <HTTPMethod.GET>,
 <HTTPMethod.HEAD>,
 <HTTPMethod.OPTIONS>,
 <HTTPMethod.PATCH>,
 <HTTPMethod.POST>,
 <HTTPMethod.PUT>,
 <HTTPMethod.TRACE>]
~~~

## HTTP 方法¶

已支持的，[IANA 注册的方法](https://www.iana.org/assignments/http-methods/http-methods.xhtml) 在 `http.HTTPMethod` 中可用的有:

方法

|

映射名

|

详情  
  
---|---|---  
  
`GET`

|

`GET`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 4.3.1  
  
`HEAD`

|

`HEAD`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 4.3.2  
  
`POST`

|

`POST`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 4.3.3  
  
`PUT`

|

`PUT`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 4.3.4  
  
`DELETE`

|

`DELETE`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 4.3.5  
  
`CONNECT`

|

`CONNECT`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 4.3.6  
  
`OPTIONS`

|

`OPTIONS`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 4.3.7  
  
`TRACE`

|

`TRACE`

|

HTTP/1.1 [**RFC 7231**](https://datatracker.ietf.org/doc/html/rfc7231.md), Section 4.3.8  
  
`PATCH`

|

`PATCH`

|

HTTP/1.1 [**RFC 5789**](https://datatracker.ietf.org/doc/html/rfc5789.md)  
  
