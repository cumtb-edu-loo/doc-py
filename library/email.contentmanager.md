# `email.contentmanager`: 管理 MIME 内容¶

**源代码:** [Lib/email/contentmanager.py](https://github.com/python/cpython/tree/3.12/Lib/email/contentmanager.py)

* * *

在 3.6 版本加入: [1]

_class _email.contentmanager.ContentManager¶

    

~~~
内容管理器的基类。 提供注册 MIME 内容和其他表示形式间转换器的标准注册机制，以及 `get_content` 和 `set_content` 发送方法。

get_content( _msg_ , _* args_, _** kw_)¶
~~~
    

~~~
基于 _msg_ 的 `mimetype` 查找处理函数（参见下一段），调用该函数，传递所有参数，并返回调用的结果。 预期的效果是处理程序将从 _msg_ 中提取有效载荷并返回编码了有关被提取数据信息的对象。

要找到处理程序，将在注册表中查找以下键，找到第一个键即停止:

>   * 表示完整 MIME 类型的字符串 (`maintype/subtype`)
>
>   * 表示 `maintype` 的字符串
>
>   * 空字符串
>
>

如果这些键都没有产生处理程序，则为完整 MIME 类型引发一个 [`KeyError`](exceptions.md#KeyError "KeyError")。

set_content( _msg_ , _obj_ , _* args_, _** kw_)¶
~~~
    

~~~
如果 `maintype` 为 `multipart`，则引发 [`TypeError`](exceptions.md#TypeError "TypeError")；否则基于 _obj_ 的类型（参见下一段）查找处理函数，在 _msg_ 上调用 [`clear_content()`](email.message.md#email.message.EmailMessage.clear_content "email.message.EmailMessage.clear_content")，并调用处理函数，传递所有参数。 预期的效果是处理程序将转换 _obj_ 并存入 _msg_ ，并可能对 _msg_ 进行其他更改，例如添加各种 MIME 标头来编码需要用来解释所存储数据的信息。

要找到处理程序，将获取 _obj_ 的类型 (`typ = type(obj)`)，并在注册表中查找以下键，找到第一个键即停止:

>   * 类型本身 (`typ`)
>
>   * 类型的完整限定名称 (`typ.__module__ + '.' + typ.__qualname__`)。
>
>   * 类型的 qualname (`typ.__qualname__`)
>
>   * 类型的 name (`typ.__name__`)。
>
>

如果未匹配到上述的任何一项，则在 [MRO](../glossary.md#term-MRO) (`typ.__mro__`) 中为每个类型重复上述的所有检测。 最后，如果没有其他键产生处理程序，则为 `None` 键检测处理程序。 如果也没有 `None` 的处理程序，则为该类型的完整限定名称引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

并会添加一个 _MIME-Version_ 标头，如果没有的话 (另请参见 [`MIMEPart`](email.message.md#email.message.MIMEPart "email.message.MIMEPart"))。

add_get_handler( _key_ , _handler_ )¶
~~~
    

~~~
将 _handler_ 函数记录为 _key_ 的处理程序。 对于可能的 _key_ 键，请参阅 `get_content()`。

add_set_handler( _typekey_ , _handler_ )¶
~~~
    

~~~
将 _handler_ 记录为当一个匹配 _typekey_ 的类型对象被传递给 `set_content()` 时所要调用的函数。 对于可能的 _typekey_ 值，请参阅 `set_content()`。

## 内容管理器实例¶

目前 email 包只提供了一个实体内容管理器 `raw_data_manager`，不过在未来可能会添加更多。 `raw_data_manager` 是由 [`EmailPolicy`](email.policy.md#email.policy.EmailPolicy "email.policy.EmailPolicy") 及其衍生工具所提供的 [`content_manager`](email.policy.md#email.policy.EmailPolicy.content_manager "email.policy.EmailPolicy.content_manager")。

email.contentmanager.raw_data_manager¶
~~~
    

~~~
这个内容管理器仅提供了超出 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 本身提供内容的最小接口：它只处理文本、原始字节串和 [`Message`](email.compat32-message.md#email.message.Message "email.message.Message") 对象。 不过相比基础 API，它具有显著的优势：在文本部分上执行 `get_content` 将返回一个 unicode 字符串而无需由应用程序来手动解码，`set_content` 为控制添加到一个部分的标头和控制内容传输编码格式提供了丰富的选项集合，并且它还启用了多种 `add_` 方法，从而简化了多部分消息的创建过程。

email.contentmanager.get_content( _msg_ , _errors ='replace'_)¶
~~~
    

~~~
将指定部分的有效载荷作为字符串（对于 `text` 部分）, [`EmailMessage`](email.message.md#email.message.EmailMessage "email.message.EmailMessage") 对象（对于 `message/rfc822` 部分）或 `bytes` 对象（对于所有其他非多部分类型）返回。 如果是在 `multipart` 上调用则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。 如果指定部分是一个 `text` 部分并且指明了 _errors_ ，则会在将载荷解码为 unicode 时将其用作错误处理程序。 默认的错误处理程序是 `replace`。

email.contentmanager.set_content( _msg_ , _< 'str'>_, _subtype= "plain"_, _charset='utf-8'_ , _cte=None_ , _disposition=None_ , _filename=None_ , _cid=None_ , _params=None_ , _headers=None_ )¶

email.contentmanager.set_content( _msg_ , _< 'bytes'>_, _maintype_ , _subtype_ , _cte= "base64"_, _disposition=None_ , _filename=None_ , _cid=None_ , _params=None_ , _headers=None_ )

email.contentmanager.set_content( _msg_ , _< 'EmailMessage'>_, _cte=None_ , _disposition=None_ , _filename=None_ , _cid=None_ , _params=None_ , _headers=None_ )
~~~
    

~~~
