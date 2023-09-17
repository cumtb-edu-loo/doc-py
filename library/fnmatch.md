# `fnmatch` \--- Unix 文件名模式匹配¶

**源代码:** [Lib/fnmatch.py](https://github.com/python/cpython/tree/3.12/Lib/fnmatch.py)

* * *

此模块提供了 Unix shell 风格的通配符，它们 _并不_ 等同于正则表达式（关于后者的文档参见 [`re`](re.md#module-re "re: Regular expression operations.") 模块）。 shell 风格通配符所使用的特殊字符如下：

模式

|

含意  
  
---|---  
  
`*`

|

匹配所有  
  
`?`

|

匹配任何单个字符  
  
`[seq]`

|

匹配 _seq_ 中的任何字符  
  
`[!seq]`

|

匹配任何不在 _seq_ 中的字符  
  
对于字面值匹配，请将原字符用方括号括起来。 例如，`'[?]'` 将匹配字符 `'?'`。

注意文件名分隔符 (Unix 上为 `'/'`) _不会_ 被此模块特别对待。 请参见 [`glob`](glob.md#module-glob "glob: Unix shell style pathname pattern expansion.") 模块了解文件名扩展 ([`glob`](glob.md#module-glob "glob: Unix shell style pathname pattern expansion.") 使用 `filter()` 来匹配文件名的各个部分)。 类似地，以一个句点打头的文件名也不会被此模块特别对待，可以通过 `*` 和 `?` 模式来匹配。

还要注意是使用将 _maxsize_ 设为 32768 的 [`functools.lru_cache()`](functools.md#functools.lru_cache "functools.lru_cache") 来缓存下列函数中的已编译正则表达式: `fnmatch()`, `fnmatchcase()`, `filter()`。

fnmatch.fnmatch( _filename_ , _pattern_ )¶

    

~~~
检测 _filename_ 字符串是否匹配 _pattern_ 字符串，返回 [`True`](constants.md#True "True") 或 [`False`](constants.md#False "False")。 两个形参都会使用 [`os.path.normcase()`](os.path.md#os.path.normcase "os.path.normcase") 进行大小写正规化。 `fnmatchcase()` 可被用于执行大小写敏感的比较，无论这是否为所在操作系统的标准。

这个例子将打印当前目录下带有扩展名 `.txt` 的所有文件名:
~~~
    
    
~~~
import fnmatch
import os

for file in os.listdir('.'):
    if fnmatch.fnmatch(file, '*.txt'):
        print(file)
~~~

fnmatch.fnmatchcase( _filename_ , _pattern_ )¶

    

~~~
检测 _filename_ 是否匹配 _pattern_ ，返回 [`True`](constants.md#True "True") 或 [`False`](constants.md#False "False")；此比较是大小写敏感的，并且不会应用 [`os.path.normcase()`](os.path.md#os.path.normcase "os.path.normcase")。

fnmatch.filter( _names_ , _pattern_ )¶
~~~
    

~~~
基于可迭代对象 _names_ 中匹配 _pattern_ 的元素构造一个列表。 它等价于 `[n for n in names if fnmatch(n, pattern)]`，但实现得更有效率。

fnmatch.translate( _pattern_ )¶
~~~
    

~~~
返回 shell 风格 _pattern_ 转换成的正则表达式以便用于 [`re.match()`](re.md#re.match "re.match")。

示例:
~~~
    
    
~~~shell
>>> import fnmatch, re
>>>
>>> regex = fnmatch.translate('*.txt')
>>> regex
'(?s:.*\\.txt)\\Z'
>>> reobj = re.compile(regex)
>>> reobj.match('foobar.txt')
<re.Match object; span=(0, 10), match='foobar.txt'>
~~~

参见

模块 [`glob`](glob.md#module-glob "glob: Unix shell style pathname pattern expansion.")

    

~~~
