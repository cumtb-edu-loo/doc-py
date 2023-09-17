# `email.iterators`: 迭代器¶

**源代码:** [Lib/email/iterators.py](https://github.com/python/cpython/tree/3.12/Lib/email/iterators.py)

* * *

通过 [`Message.walk`](email.compat32-message.md#email.message.Message.walk "email.message.Message.walk") 方法来迭代消息对象树是相当容易的。 `email.iterators` 模块提供了一些适用于消息对象树的高层级迭代器。

email.iterators.body_line_iterator( _msg_ , _decode =False_)¶

    

~~~
此函数会迭代 _msg_ 的所有子部分中的所有载荷，逐行返回字符串载荷。 它会跳过所有子部分的标头，并且它也会跳过任何包含不为 Python 字符串的载荷的子部分。 这基本上等价于使用 [`readline()`](io.md#io.TextIOBase.readline "io.TextIOBase.readline") 从一个文件读取消息的纯文本表示形式，并跳过所有中间的标头。

可选的 _decode_ 会被传递给 [`Message.get_payload`](email.compat32-message.md#email.message.Message.get_payload "email.message.Message.get_payload")。

email.iterators.typed_subpart_iterator( _msg_ , _maintype ='text'_, _subtype =None_)¶
~~~
    

~~~
此函数会迭代 _msg_ 的所有子部分，只返回其中与 _maintype_ 和 _subtype_ 所指定的 MIME 类型相匹配的子部分。

请注意 _subtype_ 是可选项；如果省略，则仅使用主类型来进行子部分 MIME 类型的匹配。 _maintype_ 也是可选项；它的默认值为 _text_ 。

因此，在默认情况下 `typed_subpart_iterator()` 会返回每一个 MIME 类型为 _text/*_ 的子部分。

增加了以下函数作为有用的调试工具。 它 _不应当_ 被视为该包所支持的公共接口的组成部分。

email.iterators._structure( _msg_ , _fp =None_, _level =0_, _include_default =False_)¶
~~~
    

~~~
打印消息对象结构的内容类型的缩进表示形式。 例如:
~~~
    
    
~~~shell
>>> msg = email.message_from_file(somefile)
>>> _structure(msg)
multipart/mixed
    text/plain
    text/plain
    multipart/digest
        message/rfc822
            text/plain
        message/rfc822
            text/plain
        message/rfc822
            text/plain
        message/rfc822
            text/plain
        message/rfc822
            text/plain
    text/plain
~~~

可选项 _fp_ 是一个作为打印输出目标的文件类对象。 它必须适用于 Python 的 [`print()`](functions.md#print "print") 函数。 _level_ 是供内部使用的。 _include_default_ 如果为真值，则会同时打印默认类型。

