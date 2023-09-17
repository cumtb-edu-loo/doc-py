# `pprint` \--- 数据美化输出¶

**源代码：** [Lib/pprint.py](https://github.com/python/cpython/tree/3.12/Lib/pprint.py)

* * *

`pprint` 模块提供了“美化打印”任意 Python 数据结构的功能，这种美化形式可用作对解释器的输入。 如果经格式化的结构包含非基本 Python 类型的对象，则其美化形式可能无法被加载。 包含文件、套接字或类对象，以及许多其他不能用 Python 字面值来表示的对象都有可能导致这样的结果。

格式化后的形式会在可能的情况下以单行来表示对象，并在无法在允许宽度内容纳对象的情况下将其分为多行。 如果你需要调整宽度限制则应显式地构造 `PrettyPrinter` 对象。

字典在计算其显示形式前会先根据键来排序。

在 3.9 版本发生变更: 添加了对美化打印 [`types.SimpleNamespace`](types.md#types.SimpleNamespace "types.SimpleNamespace") 的支持。

在 3.10 版本发生变更: 添加了对美化打印 [`dataclasses.dataclass`](dataclasses.md#dataclasses.dataclass "dataclasses.dataclass") 的支持。

`pprint` 模块定义了一个类：

_class _pprint.PrettyPrinter( _indent =1_, _width =80_, _depth =None_, _stream =None_, _*_ , _compact =False_, _sort_dicts =True_, _underscore_numbers =False_)¶

    

~~~
构造一个 `PrettyPrinter` 实例。 这个构造器支持一些关键字形参。

_stream_ (默认为 `sys.stdout`) 是一个 [file-like object](../glossary.md#term-file-like-object)，它是当调用其 `write()` 方法时的输出将要写入的目标。 如果 _stream_ 和 `sys.stdout` 均为 `None`，则 `pprint()` 将静默地返回。

其他值可用来配置复杂数据结构嵌套要以何种形式被展示。

_indent_ (默认为 1) 指定要为每个缩进层级添加的缩进量。

_depth_ 控制可被打印的缩进层级数量；如果要打印的数据结构层级过深，则其所包含的下一层级将用 `...` 替换。 默认情况下，对于被格式化对象的层级深度没有任何限制。

_width_ (默认为 80) 指定输出中每行所允许的最大字符数。 如果一个数据结构无法在宽度限制之内被格式化，将显示尽可能多的内容。

_compact_ 影响长序列（列表、元组、集合等等）的格式化方式。 如果 _compact_ 为假值（默认）则序列的每一项将格式化为单独的行。 如果 _compact_ 为真值，则每个输出行格式化时将在 _width_ 的限制之内尽可能地容纳多个条目。

如果 _sort_dicts_ 为真值（默认），字典在格式化时将基于键进行排序，否则它们将按插入顺序显示。

如果 _underscore_numbers_ 为真值，整数在格式化时将使用 `_` 字符作为千位分隔符，否则不显示下划线（默认）。

在 3.4 版本发生变更: 增加了 _compact_ 形参。

在 3.8 版本发生变更: 增加了 _sort_dicts_ 形参。

在 3.10 版本发生变更: 添加了 _underscore_numbers_ 形参。

在 3.11 版本发生变更: 如果 `sys.stdout` 为 `None` 则将不会尝试向其中写入。
~~~
    
    
~~~shell
>>> import pprint
>>> stuff = ['spam', 'eggs', 'lumberjack', 'knights', 'ni']
>>> stuff.insert(0, stuff[:])
>>> pp = pprint.PrettyPrinter(indent=4)
>>> pp.pprint(stuff)
[   ['spam', 'eggs', 'lumberjack', 'knights', 'ni'],
    'spam',
    'eggs',
    'lumberjack',
    'knights',
    'ni']
>>> pp = pprint.PrettyPrinter(width=41, compact=True)
>>> pp.pprint(stuff)
[['spam', 'eggs', 'lumberjack',
  'knights', 'ni'],
 'spam', 'eggs', 'lumberjack', 'knights',
 'ni']
>>> tup = ('spam', ('eggs', ('lumberjack', ('knights', ('ni', ('dead',
... ('parrot', ('fresh fruit',))))))))
>>> pp = pprint.PrettyPrinter(depth=6)
>>> pp.pprint(tup)
('spam', ('eggs', ('lumberjack', ('knights', ('ni', ('dead', (...)))))))
~~~

pprint.pformat( _object_ , _indent =1_, _width =80_, _depth =None_, _*_ , _compact =False_, _sort_dicts =True_, _underscore_numbers =False_)¶

    

~~~
将 _object_ 的格式化表示形式作为字符串返回。 _indent_ , _width_ , _depth_ , _compact_ , _sort_dicts_ 和 _underscore_numbers_ 将作为格式化形参传递给 `PrettyPrinter` 构造器，它们的含义见以上相应文档的说明。

pprint.pp( _object_ , _* args_, _sort_dicts =False_, _** kwargs_)¶
~~~
    

~~~
打印 _object_ 的格式化表示并附带一个换行符。 如果 _sort_dicts_ 为假值（默认），字典将按键的插入顺序显示，否则将按字典键排序。 _args_ 和 _kwargs_ 将作为格式化形参被传给 `pprint()`。

在 3.8 版本加入.

pprint.pprint( _object_ , _stream =None_, _indent =1_, _width =80_, _depth =None_, _*_ , _compact =False_, _sort_dicts =True_, _underscore_numbers =False_)¶
~~~
    

~~~
在 _stream_ 上打印 _object_ 的格式化表示形式。 如果 _stream_ 为 `None`，则会使用 `sys.stdout`。 这可以在交互式解释器中代替 [`print()`](functions.md#print "print") 函数使用以便检查对象的值（你甚至可以通过重赋值 `print = pprint.pprint` 在特定作用域内使用）。

配置形参 _stream_ , _indent_ , _width_ , _depth_ , _compact_ , _sort_dicts_ 和 _underscore_numbers_ 将被传递给 `PrettyPrinter` 构造器，它们的含义见以上相应文档的说明。
~~~
    
    
~~~shell
>>> import pprint
>>> stuff = ['spam', 'eggs', 'lumberjack', 'knights', 'ni']
>>> stuff.insert(0, stuff)
>>> pprint.pprint(stuff)
[<Recursion on list with id=...>,
 'spam',
 'eggs',
 'lumberjack',
 'knights',
 'ni']
~~~

pprint.isreadable( _object_ )¶

    

~~~
确定 _object_ 的格式化表示是否“可读”，或是否可被用来通过 [`eval()`](functions.md#eval "eval") 重新构建对象的值。 此函数对于递归对象总是返回 `False`。
~~~
    
    
~~~shell
>>> pprint.isreadable(stuff)
False
~~~

pprint.isrecursive( _object_ )¶

    

~~~
确定 _object_ 是否需要递归的表示。 此函数会受到下面 `saferepr()` 所提及的同样限制的影响并可能在无法检测到可递归对象时引发 [`RecursionError`](exceptions.md#RecursionError "RecursionError")。

此外还定义了一个支持函数：

pprint.saferepr( _object_ )¶
~~~
    

~~~
返回 _object_ 的字符串表示，并为某些通用数据结构提供防递归保护，包括 [`dict`](stdtypes.md#dict "dict"), [`list`](stdtypes.md#list "list") 和 [`tuple`](stdtypes.md#tuple "tuple") 或其未重载 `__repr__` 的子类的实例。 如果该对象表示形式公开了一个递归条目，该递归引用会被表示为 `<Recursion on typename with id=number>`。 否则该表示形式将不会被格式化。
~~~
    
    
~~~shell
>>> pprint.saferepr(stuff)
"[<Recursion on list with id=...>, 'spam', 'eggs', 'lumberjack', 'knights', 'ni']"
~~~

## PrettyPrinter 对象¶

`PrettyPrinter` 的实例具有下列方法：

PrettyPrinter.pformat( _object_ )¶

    

~~~
返回 _object_ 格式化表示。 这会将传给 `PrettyPrinter` 构造器的选项纳入考虑。

PrettyPrinter.pprint( _object_ )¶
~~~
    

~~~
在所配置的流上打印 _object_ 的格式化表示，并附加一个换行符。

下列方法提供了与同名函数相对应的实现。 在实例上使用这些方法效率会更高一些，因为不需要创建新的 `PrettyPrinter` 对象。

PrettyPrinter.isreadable( _object_ )¶
~~~
    

~~~
确定对象的格式化表示是否“可读”，或者是否可使用 [`eval()`](functions.md#eval "eval") 重建对象值。 请注意此方法对于递归对象将返回 `False`。 如果设置了 `PrettyPrinter` 的 _depth_ 形参并且对象深度超出允许范围，此方法将返回 `False`。

PrettyPrinter.isrecursive( _object_ )¶
~~~
    

~~~
确定对象是否需要递归表示。

此方法作为一个钩子提供，允许子类修改将对象转换为字符串的方式。 默认实现使用 `saferepr()` 实现的内部方式。

PrettyPrinter.format( _object_ , _context_ , _maxlevels_ , _level_ )¶
~~~
    

~~~
返回三个值：字符串形式的 _object_ 已格式化版本，指明结果是否可读的旗标，以及指明是否检测到递归的旗标。 第一个参数是要表示的对象。 第二个是以对象 [`id()`](functions.md#id "id") 为键的字典，这些对象是当前表示上下文的一部分（影响 _object_ 表示的直接和间接容器）；如果需要呈现一个已经在 _context_ 中表示的对象，则第三个返回值应当为 `True`。 对 `format()` 方法的递归调用应当将容器的附加条目添加到此字典中。 第三个参数 _maxlevels_ 给出了对递归的请求限制；如果没有请求限制则其值将为 `0`。 此参数应当不加修改地传给递归调用。 第四个参数 _level_ 给出于当前层级；传给递归调用的参数值应当小于当前调用的值。

## 示例¶

为了演示 `pprint()` 函数及其形参的几种用法，让我们从 [PyPI](https://pypi.org) 获取关于某个项目的信息:
~~~
    
    
~~~shell
>>> import json
>>> import pprint
>>> from urllib.request import urlopen
>>> with urlopen('https://pypi.org/pypi/sampleproject/json') as resp:
...     project_info = json.load(resp)['info']
~~~

`pprint()` 以其基本形式显示了整个对象:

    
    
~~~shell
>>> pprint.pprint(project_info)
{'author': 'The Python Packaging Authority',
 'author_email': 'pypa-dev@googlegroups.com',
 'bugtrack_url': None,
 'classifiers': ['Development Status :: 3 - Alpha',
                 'Intended Audience :: Developers',
                 'License :: OSI Approved :: MIT License',
                 'Programming Language :: Python :: 2',
                 'Programming Language :: Python :: 2.6',
                 'Programming Language :: Python :: 2.7',
                 'Programming Language :: Python :: 3',
                 'Programming Language :: Python :: 3.2',
                 'Programming Language :: Python :: 3.3',
                 'Programming Language :: Python :: 3.4',
                 'Topic :: Software Development :: Build Tools'],
 'description': 'A sample Python project\n'
                '=======================\n'
                '\n'
                'This is the description file for the project.\n'
                '\n'
                'The file should use UTF-8 encoding and be written using '
                'ReStructured Text. It\n'
                'will be used to generate the project webpage on PyPI, and '
                'should be written for\n'
                'that purpose.\n'
                '\n'
                'Typical contents for this file would include an overview of '
                'the project, basic\n'
                'usage examples, etc. Generally, including the project '
                'changelog in here is not\n'
                'a good idea, although a simple "What\'s New" section for the '
                'most recent version\n'
                'may be appropriate.',
 'description_content_type': None,
 'docs_url': None,
 'download_url': 'UNKNOWN',
 'downloads': {'last_day': -1, 'last_month': -1, 'last_week': -1},
 'home_page': 'https://github.com/pypa/sampleproject',
 'keywords': 'sample setuptools development',
 'license': 'MIT',
 'maintainer': None,
 'maintainer_email': None,
 'name': 'sampleproject',
 'package_url': 'https://pypi.org/project/sampleproject/',
 'platform': 'UNKNOWN',
 'project_url': 'https://pypi.org/project/sampleproject/',
 'project_urls': {'Download': 'UNKNOWN',
                  'Homepage': 'https://github.com/pypa/sampleproject'},
 'release_url': 'https://pypi.org/project/sampleproject/1.2.0/',
 'requires_dist': None,
 'requires_python': None,
 'summary': 'A sample Python project',
 'version': '1.2.0'}
~~~

结果可以被限制到特定的 _depth_ (更深层的内容将使用省略号):

    
    
~~~shell
>>> pprint.pprint(project_info, depth=1)
{'author': 'The Python Packaging Authority',
 'author_email': 'pypa-dev@googlegroups.com',
 'bugtrack_url': None,
 'classifiers': [...],
 'description': 'A sample Python project\n'
                '=======================\n'
                '\n'
                'This is the description file for the project.\n'
                '\n'
                'The file should use UTF-8 encoding and be written using '
                'ReStructured Text. It\n'
                'will be used to generate the project webpage on PyPI, and '
                'should be written for\n'
                'that purpose.\n'
                '\n'
                'Typical contents for this file would include an overview of '
                'the project, basic\n'
                'usage examples, etc. Generally, including the project '
                'changelog in here is not\n'
                'a good idea, although a simple "What\'s New" section for the '
                'most recent version\n'
                'may be appropriate.',
 'description_content_type': None,
 'docs_url': None,
 'download_url': 'UNKNOWN',
 'downloads': {...},
 'home_page': 'https://github.com/pypa/sampleproject',
 'keywords': 'sample setuptools development',
 'license': 'MIT',
 'maintainer': None,
 'maintainer_email': None,
 'name': 'sampleproject',
 'package_url': 'https://pypi.org/project/sampleproject/',
 'platform': 'UNKNOWN',
 'project_url': 'https://pypi.org/project/sampleproject/',
 'project_urls': {...},
 'release_url': 'https://pypi.org/project/sampleproject/1.2.0/',
 'requires_dist': None,
 'requires_python': None,
 'summary': 'A sample Python project',
 'version': '1.2.0'}
~~~

此外，还可以设置建议的最大字符 _width_ 。 如果一个对象无法被拆分，则将超出指定宽度:

    
    
~~~shell
>>> pprint.pprint(project_info, depth=1, width=60)
{'author': 'The Python Packaging Authority',
 'author_email': 'pypa-dev@googlegroups.com',
 'bugtrack_url': None,
 'classifiers': [...],
 'description': 'A sample Python project\n'
                '=======================\n'
                '\n'
                'This is the description file for the '
                'project.\n'
                '\n'
                'The file should use UTF-8 encoding and be '
                'written using ReStructured Text. It\n'
                'will be used to generate the project '
                'webpage on PyPI, and should be written '
                'for\n'
                'that purpose.\n'
                '\n'
                'Typical contents for this file would '
                'include an overview of the project, '
                'basic\n'
                'usage examples, etc. Generally, including '
                'the project changelog in here is not\n'
                'a good idea, although a simple "What\'s '
                'New" section for the most recent version\n'
                'may be appropriate.',
 'description_content_type': None,
 'docs_url': None,
 'download_url': 'UNKNOWN',
 'downloads': {...},
 'home_page': 'https://github.com/pypa/sampleproject',
 'keywords': 'sample setuptools development',
 'license': 'MIT',
 'maintainer': None,
 'maintainer_email': None,
 'name': 'sampleproject',
 'package_url': 'https://pypi.org/project/sampleproject/',
 'platform': 'UNKNOWN',
 'project_url': 'https://pypi.org/project/sampleproject/',
 'project_urls': {...},
 'release_url': 'https://pypi.org/project/sampleproject/1.2.0/',
 'requires_dist': None,
 'requires_python': None,
 'summary': 'A sample Python project',
 'version': '1.2.0'}
~~~

