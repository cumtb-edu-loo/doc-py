# `hmac` \--- 基于密钥的消息验证¶

**源代码:** [Lib/hmac.py](https://github.com/python/cpython/tree/3.12/Lib/hmac.py)

* * *

此模块实现了 HMAC 算法，算法的描述参见 [**RFC 2104**](https://datatracker.ietf.org/doc/html/rfc2104.md)。

hmac.new( _key_ , _msg =None_, _digestmod =''_)¶

    

~~~
返回一个新的 hmac 对象。 _key_ 是一个指定密钥的 bytes 或 bytearray 对象。 如果提供了 _msg_ ，将会调用 `update(msg)` 方法。 _digestmod_ 为 HMAC 对象所用的摘要名称、摘要构造器或模块。 它可以是适用于 [`hashlib.new()`](hashlib.md#hashlib.new "hashlib.new") 的任何名称。 虽然该参数位置靠后，但它却是必须的。

在 3.4 版本发生变更: 形参 _key_ 可以为 bytes 或 bytearray 对象。 形参 _msg_ 可以为 [`hashlib`](hashlib.md#module-hashlib "hashlib: Secure hash and message digest algorithms.") 所支持的任意类型。 形参 _digestmod_ 可以为某种哈希算法的名称。

从 3.4 版起不建议使用，已在 3.8 版中移除: MD5 作为 _digestmod_ 的隐式默认摘要已被弃用。 digestmod 形参现在是必须的。 请将其作为关键字参数传入以避免当你没有初始 msg 时将导致的麻烦。

hmac.digest( _key_ , _msg_ , _digest_ )¶
~~~
    

~~~
基于给定密钥 _key_ 和 _digest_ 返回 _msg_ 的摘要。 此函数等价于 `HMAC(key, msg, digest).digest()`，但使用了优化的 C 或内联实现，对放入内存的消息能处理得更快。 形参 _key_ , _msg_ 和 _digest_ 具有与 `new()` 中相同的含义。

作为 CPython 的实现细节，优化的 C 实现仅当 _digest_ 为字符串并且是一个 OpenSSL 所支持的摘要算法的名称时才会被使用。

在 3.7 版本加入.

HMAC 对象具有下列方法:

HMAC.update( _msg_ )¶
~~~
    

~~~
用 _msg_ 来更新 hmac 对象。 重复调用相当于单次调用并传入所有参数的拼接结果: `m.update(a); m.update(b)` 等价于 `m.update(a + b)`。

在 3.4 版本发生变更: 形参 _msg_ 可以为 [`hashlib`](hashlib.md#module-hashlib "hashlib: Secure hash and message digest algorithms.") 所支持的任何类型。

HMAC.digest()¶
~~~
    

~~~
返回当前已传给 `update()` 方法的字节串数据的摘要。 这个字节串数据的长度将与传给构造器的摘要的长度 _digest_size_ 相同。 它可以包含非 ASCII 的字节，包括 NUL 字节。

警告

在验证例程运行期间将 `digest()` 的输出与外部提供的摘要进行比较时，建议使用 `compare_digest()` 函数而不是 `==` 运算符以减少面对定时攻击的弱点。

HMAC.hexdigest()¶
~~~
    

~~~
类似于 `digest()` 但摘要会以两倍长度字符串的形式返回，其中仅包含十六进制数码。 这可以被用于在电子邮件或其他非二进制环境中安全地交换数据值。

警告

在验证例程运行期间将 `hexdigest()` 的输出与外部提供的摘要进行比较时，建议使用 `compare_digest()` 函数而不是 `==` 运算符以减少面对定时攻击的弱点。

HMAC.copy()¶
~~~
    

~~~
返回 hmac 对象的副本（“克隆）。 这可被用来高效地计算共享相同初始子串的数据的摘要。

hash 对象具有以下属性：

HMAC.digest_size¶
~~~
    

~~~
以字节表示的结果 HMAC 摘要的大小。

HMAC.block_size¶
~~~
    

~~~
以字节表示的哈希算法的内部块大小。

在 3.4 版本加入.

HMAC.name¶
~~~
    

~~~
HMAC 的规范名称，总是为小写形式，例如 `hmac-md5`。

在 3.4 版本加入.

自 3.9 版本弃用: 未写入文档的属性 `HMAC.digest_cons`, `HMAC.inner` 和 `HMAC.outer` 属于内部实现细节，将在 Python 3.10 中被移除。

这个模块还提供了下列辅助函数:

hmac.compare_digest( _a_ , _b_ )¶
~~~
    

~~~
返回 `a == b`。 此函数使用一种经专门设计的方式通过避免基于内容的短路行为来防止定时分析，使得它适合处理密码。 _a_ 和 _b_ 必须为相同的类型：或者是 [`str`](stdtypes.md#str "str") (仅限 ASCII 字符，如 `HMAC.hexdigest()` 的返回值)，或者是 [bytes-like object](../glossary.md#term-bytes-like-object)。

备注

如果 _a_ 和 _b_ 具有不同的长度，或者如果发生了错误，定时攻击在理论上可以获取有关 _a_ 和 _b_ 的类型和长度信息 — 但不能获取它们的值。

在 3.3 版本加入.

在 3.10 版本发生变更: 此函数在可能的情况下会在内部使用 OpenSSL 的 `CRYPTO_memcmp()`。

参见

模块 [`hashlib`](hashlib.md#module-hashlib "hashlib: Secure hash and message digest algorithms.")
~~~
    

~~~
