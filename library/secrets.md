# `secrets` \--- 生成管理密码的安全随机数¶

在 3.6 版本加入.

**源代码:** [Lib/secrets.py](https://github.com/python/cpython/tree/3.12/Lib/secrets.py)

* * *

`secrets` 模块用于生成高度加密的随机数，适于管理密码、账户验证、安全凭据及机密数据。

最好用 `secrets` 替代 [`random`](random.md#module-random "random: Generate pseudo-random numbers with various common distributions.") 模块的默认伪随机数生成器，该生成器适用于建模和模拟，不宜用于安全与加密。

参见

[**PEP 506**](https://peps.python.org/pep-0506/)

## 随机数¶

`secrets` 模块是操作系统提供的最安全地随机性来源。

_class _secrets.SystemRandom¶

    

~~~
用操作系统提供的最高质量源生成随机数的类。详见 [`random.SystemRandom`](random.md#random.SystemRandom "random.SystemRandom")。

secrets.choice( _sequence_ )¶
~~~
    

~~~
返回一个从非空序列中随机选取的元素。

secrets.randbelow( _n_ )¶
~~~
    

~~~
返回 [0, _n_ ) 范围内的随机整数。

secrets.randbits( _k_ )¶
~~~
    

~~~
返回 _k_ 个随机比特位的整数。

## 生成 Token¶

`secrets` 模块提供了生成安全 Token 的函数，适用于密码重置、密保 URL 等应用场景。

secrets.token_bytes([ _nbytes=None_ ])¶
~~~
    

~~~
返回含 _nbytes_ 个字节的随机字节字符串。如果未提供 _nbytes_ ，或*nbytes* 为 `None`，则使用合理的默认值。
~~~
    
    
~~~shell
>>> token_bytes(16)  
b'\xebr\x17D*t\xae\xd4\xe3S\xb6\xe2\xebP1\x8b'
~~~

secrets.token_hex([ _nbytes=None_ ])¶

    

~~~
返回十六进制随机文本字符串。字符串有 _nbytes_ 个随机字节，每个字节转换为两个十六进制数码。未提供 _nbytes_ 或为 `None` 时，则使用合理的默认值。
~~~
    
    
~~~shell
>>> token_hex(16)  
'f9bf78b9a18ce6d46a0cd2b0b86df9da'
~~~

secrets.token_urlsafe([ _nbytes=None_ ])¶

    

~~~
返回安全的 URL 随机文本字符串，包含 _nbytes_ 个随机字节。文本用 Base64 编码，平均来说，每个字节对应 1.3 个结果字符。未提供 _nbytes_ 或为 `None` 时，则使用合理的默认值。
~~~
    
    
~~~shell
>>> token_urlsafe(16)  
'Drmhze6EPcv0fN_81Bj-nA'
~~~

### Token 应当使用多少个字节？¶

为了在面对 [暴力攻击](https://en.wikipedia.org/wiki/Brute-force_attack) 时保证安全，Token 的随机性必须足够高。随着计算机推衍能力的不断提升，随机性的安全标准也要不断提高。比如 2015 年，32 字节（256 位）的随机性对于 `secrets` 模块的典型用例就已经足够了。

要自行管理 Token 长度的用户，可以通过为 `token_*` 函数指定 [`int`](functions.md#int "int") 参数显式指定 Token 要使用多大的随机性。该参数以字节数表示随机性大小。

反之，如果未提供参数，或参数为 `None`，则 `token_*` 函数将使用合理的默认值。

备注

该默认值随时可能会改变，比如，版本更新的时候。

## 其他功能¶

secrets.compare_digest( _a_ , _b_ )¶

    

~~~
如果字符串或 [字节类对象](../glossary.md#term-bytes-like-object) _a_ 与 _b_ 相等则返回 `True`，否则返回 `False`，使用“常态化比较”来降低 [定时攻击](https://codahale.com/a-lesson-in-timing-attacks/) 的风险。 请参阅 [`hmac.compare_digest()`](hmac.md#hmac.compare_digest "hmac.compare_digest") 了解更多细节。

## 应用技巧与最佳实践¶

本节展示了一些使用 `secrets` 管理基本安全级别的应用技巧和最佳实践。

生成长度为八个字符的字母数字密码：
~~~
    
    
~~~
import string
import secrets
alphabet = string.ascii_letters + string.digits
password = ''.join(secrets.choice(alphabet) for i in range(8))
~~~

备注

应用程序不可 [以可恢复的格式存储密码](https://cwe.mitre.org/data/definitions/257.md)，无论是纯文本还是加密形式。 它们应当使用高加密强度的单向（不可逆）哈希函数来加盐和生成哈希值。

生成长度为十个字符的字母数字密码，包含至少一个小写字母，至少一个大写字母以及至少三个数字：

    
    
~~~
import string
import secrets
alphabet = string.ascii_letters + string.digits
while True:
    password = ''.join(secrets.choice(alphabet) for i in range(10))
    if (any(c.islower() for c in password)
            and any(c.isupper() for c in password)
            and sum(c.isdigit() for c in password) >= 3):
        break
~~~

生成 [XKCD 风格的密码串](https://xkcd.com/936/)：

    
    
~~~
import secrets
# On standard Linux systems, use a convenient dictionary file.
# Other platforms may need to provide their own word-list.
with open('/usr/share/dict/words') as f:
    words = [word.strip() for word in f]
    password = ' '.join(secrets.choice(words) for i in range(4))
~~~

生成临时密保 URL，包含密码恢复应用的安全 Token：

    
    
~~~
import secrets
url = 'https://example.com/reset=' + secrets.token_urlsafe()
~~~

