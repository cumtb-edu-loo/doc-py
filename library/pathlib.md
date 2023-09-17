# `pathlib` \--- 面向对象的文件系统路径¶

在 3.4 版本加入.

**源代码** [Lib/pathlib.py](https://github.com/python/cpython/tree/3.12/Lib/pathlib.py)

* * *

该模块提供表示文件系统路径的类，其语义适用于不同的操作系统。路径类被分为提供纯计算操作而没有 I/O 的 纯路径，以及从纯路径继承而来但提供 I/O 操作的 具体路径。

![../_images/pathlib-inheritance.png](../_images/pathlib-inheritance.png)

如果以前从未用过此模块，或不确定哪个类适合完成任务，那要用的可能就是 `Path`。它在运行代码的平台上实例化为 具体路径。

在一些用例中纯路径很有用，例如：

  1. 如果你想要在 Unix 设备上操作 Windows 路径（或者相反）。你不应在 Unix 上实例化一个 `WindowsPath`，但是你可以实例化 `PureWindowsPath`。

  2. 你只想操作路径但不想实际访问操作系统。在这种情况下，实例化一个纯路径是有用的，因为它们没有任何访问操作系统的操作。

参见

[**PEP 428**](https://peps.python.org/pep-0428/)：pathlib 模块 -- 面向对象的文件系统路径。

参见

对于底层的路径字符串操作，你也可以使用 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") 模块。

## 基础使用¶

导入主类:

    
    
~~~shell
>>> from pathlib import Path
~~~

列出子目录:

    
    
~~~shell
>>> p = Path('.')
>>> [x for x in p.iterdir() if x.is_dir()]
[PosixPath('.hg'), PosixPath('docs'), PosixPath('dist'),
 PosixPath('__pycache__'), PosixPath('build')]
~~~

列出当前目录树下的所有 Python 源代码文件:

    
    
~~~shell
>>> list(p.glob('**/*.py'))
[PosixPath('test_pathlib.py'), PosixPath('setup.py'),
 PosixPath('pathlib.py'), PosixPath('docs/conf.py'),
 PosixPath('build/lib/pathlib.py')]
~~~

在目录树中移动:

    
    
~~~shell
>>> p = Path('/etc')
>>> q = p / 'init.d' / 'reboot'
>>> q
PosixPath('/etc/init.d/reboot')
>>> q.resolve()
PosixPath('/etc/rc.d/init.d/halt')
~~~

查询路径的属性:

    
    
~~~shell
>>> q.exists()
True
>>> q.is_dir()
False
~~~

打开一个文件:

    
    
~~~shell
>>> with q.open() as f: f.readline()
...
'#!/bin/bash\n'
~~~

## Exceptions¶

_exception _pathlib.UnsupportedOperation¶

    

~~~
An exception inheriting [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") that is raised when an unsupported operation is called on a path object.

在 3.13 版本加入.

## 纯路径¶

纯路径对象提供了不实际访问文件系统的路径处理操作。有三种方式来访问这些类，也是不同的风格：

_class _pathlib.PurePath( _* pathsegments_)¶
~~~
    

~~~
一个通用的类，代表当前系统的路径风格（实例化为 `PurePosixPath` 或者 `PureWindowsPath`）:
~~~
    
    
~~~shell
>>> PurePath('setup.py')      # Running on a Unix machine
PurePosixPath('setup.py')
~~~

_pathsegments_ 的每个元素既可以是代表一个路径段的字符串，也可以是实现了 [`os.PathLike`](os.md#os.PathLike "os.PathLike") 接口的对象，其中 [`__fspath__()`](os.md#os.PathLike.__fspath__ "os.PathLike.__fspath__") 方法返回一个字符串，例如另一个路径对象:

    
    
~~~shell
>>> PurePath('foo', 'some/path', 'bar')
PurePosixPath('foo/some/path/bar')
>>> PurePath(Path('foo'), Path('bar'))
PurePosixPath('foo/bar')
~~~

当 _pathsegments_ 为空的时候，假定为当前目录:

    
    
~~~shell
>>> PurePath()
PurePosixPath('.')
~~~

如果某个段为绝对路径，则其前面的所有段都会被忽略 (类似 [`os.path.join()`](os.path.md#os.path.join "os.path.join")):

    
    
~~~shell
>>> PurePath('/etc', '/usr', 'lib64')
PurePosixPath('/usr/lib64')
>>> PureWindowsPath('c:/Windows', 'd:bar')
PureWindowsPath('d:bar')
~~~

在 Windows 上，当遇到带根符号的路径段 (如 `r'\foo'`) 时驱动器将不会被重置:

    
    
~~~shell
>>> PureWindowsPath('c:/Windows', '/Program Files')
PureWindowsPath('c:/Program Files')
~~~

假斜杠和单个点号会被消除，但双点号 (`'..'`) 和打头的双斜杠 (`'//'`) 不会，因为这会出于各种原因改变路径的实际含义 (例如符号链接、UNC 路径等):

    
    
~~~shell
>>> PurePath('foo//bar')
PurePosixPath('foo/bar')
>>> PurePath('//foo/bar')
PurePosixPath('//foo/bar')
>>> PurePath('foo/./bar')
PurePosixPath('foo/bar')
>>> PurePath('foo/../bar')
PurePosixPath('foo/../bar')
~~~

（一个很 naïve 的做法是让 `PurePosixPath('foo/../bar')` 等同于 `PurePosixPath('bar')`，如果 `foo` 是一个指向其他目录的符号链接那么这个做法就将出错）

纯路径对象实现了 [`os.PathLike`](os.md#os.PathLike "os.PathLike") 接口，允许它们在任何接受此接口的地方使用。

在 3.6 版本发生变更: 添加了 [`os.PathLike`](os.md#os.PathLike "os.PathLike") 接口支持。

_class _pathlib.PurePosixPath( _* pathsegments_)¶

    

~~~
一个 `PurePath` 的子类，路径风格不同于 Windows 文件系统:
~~~
    
    
~~~shell
>>> PurePosixPath('/etc')
PurePosixPath('/etc')
~~~

_pathsegments_ 参数的指定和 `PurePath` 相同。

_class _pathlib.PureWindowsPath( _* pathsegments_)¶

    

~~~
`PurePath` 的一个子类，此路径风格代表 Windows 文件系统路径，包括 [UNC paths](https://en.wikipedia.org/wiki/Path_\(computing\)#UNC):
~~~
    
    
~~~shell
>>> PureWindowsPath('c:/Program Files/')
PureWindowsPath('c:/Program Files')
>>> PureWindowsPath('//server/share/file')
PureWindowsPath('//server/share/file')
~~~

_pathsegments_ 参数的指定和 `PurePath` 相同。

无论你正运行什么系统，你都可以实例化这些类，因为它们提供的操作不做任何系统调用。

### 通用性质¶

路径是不可变并且 [hashable](../glossary.md#term-hashable)。 相同风格的路径可以排序和比较。 这此特性会尊重对应风格的大小写转换语义。:

    
    
~~~shell
>>> PurePosixPath('foo') == PurePosixPath('FOO')
False
>>> PureWindowsPath('foo') == PureWindowsPath('FOO')
True
>>> PureWindowsPath('FOO') in { PureWindowsPath('foo') }
True
>>> PureWindowsPath('C:') < PureWindowsPath('d:')
True
~~~

不同风格的路径比较得到不等的结果并且无法被排序:

    
    
~~~shell
>>> PureWindowsPath('foo') == PurePosixPath('foo')
False
>>> PureWindowsPath('foo') < PurePosixPath('foo')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: '<' not supported between instances of 'PureWindowsPath' and 'PurePosixPath'
~~~

### 运算符¶

斜杠操作符可以帮助创建子路径，如 [`os.path.join()`](os.path.md#os.path.join "os.path.join")。 如果参数为一个绝对路径，则之前的路径会被忽略。 在 Windows 上，当参数为一个带根符号的相对路径 (如 `r'\foo'`) 时驱动器将不会被重置:

    
    
~~~shell
>>> p = PurePath('/etc')
>>> p
PurePosixPath('/etc')
>>> p / 'init.d' / 'apache2'
PurePosixPath('/etc/init.d/apache2')
>>> q = PurePath('bin')
>>> '/usr' / q
PurePosixPath('/usr/bin')
>>> p / '/an_absolute_path'
PurePosixPath('/an_absolute_path')
>>> PureWindowsPath('c:/Windows', '/Program Files')
PureWindowsPath('c:/Program Files')
~~~

文件对象可用于任何接受 [`os.PathLike`](os.md#os.PathLike "os.PathLike") 接口实现的地方。

    
    
~~~shell
>>> import os
>>> p = PurePath('/etc')
>>> os.fspath(p)
'/etc'
~~~

路径的字符串表示法为它自己原始的文件系统路径（以原生形式，例如在 Windows 下使用反斜杠）。你可以传递给任何需要字符串形式路径的函数。

    
    
~~~shell
>>> p = PurePath('/etc')
>>> str(p)
'/etc'
>>> p = PureWindowsPath('c:/Program Files')
>>> str(p)
'c:\\Program Files'
~~~

类似地，在路径上调用 [`bytes`](stdtypes.md#bytes "bytes") 将原始文件系统路径作为字节对象给出，就像被 [`os.fsencode()`](os.md#os.fsencode "os.fsencode") 编码一样:

    
    
~~~shell
>>> bytes(p)
b'/etc'
~~~

备注

只推荐在 Unix 下调用 [`bytes`](stdtypes.md#bytes "bytes")。在 Windows， unicode 形式是文件系统路径的规范表示法。

### 访问个别部分¶

为了访问路径独立的部分 （组件），使用以下特征属性：

PurePath.parts¶

    

~~~
一个元组，可以访问路径的多个组件:
~~~
    
    
~~~shell
>>> p = PurePath('/usr/bin/python3')
>>> p.parts
('/', 'usr', 'bin', 'python3')

>>> p = PureWindowsPath('c:/Program Files/PSF')
>>> p.parts
('c:\\', 'Program Files', 'PSF')
~~~

（注意盘符和本地根目录是如何重组的）

### 方法和特征属性¶

纯路径提供以下方法和特征属性：

PurePath.pathmod¶

    

~~~
The implementation of the [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.") module used for low-level path operations: either `posixpath` or `ntpath`.

在 3.13 版本加入.

PurePath.drive¶
~~~
    

~~~
一个表示驱动器盘符或命名的字符串，如果存在:
~~~
    
    
~~~shell
>>> PureWindowsPath('c:/Program Files/').drive
'c:'
>>> PureWindowsPath('/Program Files/').drive
''
>>> PurePosixPath('/etc').drive
''
~~~

UNC 分享也被认作驱动器:

    
    
~~~shell
>>> PureWindowsPath('//host/share/foo.txt').drive
'\\\\host\\share'
~~~

PurePath.root¶

    

~~~
一个表示（本地或全局）根的字符串，如果存在:
~~~
    
    
~~~shell
>>> PureWindowsPath('c:/Program Files/').root
'\\'
>>> PureWindowsPath('c:Program Files/').root
''
>>> PurePosixPath('/etc').root
'/'
~~~

UNC 分享一样拥有根:

    
    
~~~shell
>>> PureWindowsPath('//host/share').root
'\\'
~~~

如果路径以超过两个连续斜框打头，`PurePosixPath` 会合并它们:

    
    
~~~shell
>>> PurePosixPath('//etc').root
'//'
>>> PurePosixPath('///etc').root
'/'
>>> PurePosixPath('////etc').root
'/'
~~~

备注

此行为符合 _The Open Group Base Specifications Issue 6_ , paragraph [4.11 Pathname Resolution](https://pubs.opengroup.org/onlinepubs/009695399/basedefs/xbd_chap04.md#tag_04_11):

_" 以连续两个斜杠打头的路径名可能会以具体实现所定义的方式被解读，但是两个以上的前缀斜杠则应当被当作一个斜杠来处理。"_

PurePath.anchor¶

    

~~~
驱动器和根的联合:
~~~
    
    
~~~shell
>>> PureWindowsPath('c:/Program Files/').anchor
'c:\\'
>>> PureWindowsPath('c:Program Files/').anchor
'c:'
>>> PurePosixPath('/etc').anchor
'/'
>>> PureWindowsPath('//host/share').anchor
'\\\\host\\share\\'
~~~

PurePath.parents¶

    

~~~
提供访问此路径的逻辑祖先的不可变序列:
~~~
    
    
~~~shell
>>> p = PureWindowsPath('c:/foo/bar/setup.py')
>>> p.parents[0]
PureWindowsPath('c:/foo/bar')
>>> p.parents[1]
PureWindowsPath('c:/foo')
>>> p.parents[2]
PureWindowsPath('c:/')
~~~

在 3.10 版本发生变更: parents 序列现在支持 [切片](../glossary.md#term-slice) 和负的索引值。

PurePath.parent¶

    

~~~
此路径的逻辑父路径:
~~~
    
    
~~~shell
>>> p = PurePosixPath('/a/b/c/d')
>>> p.parent
PurePosixPath('/a/b/c')
~~~

你不能超过一个 anchor 或空路径:

    
    
~~~shell
>>> p = PurePosixPath('/')
>>> p.parent
PurePosixPath('/')
>>> p = PurePosixPath('.')
>>> p.parent
PurePosixPath('.')
~~~

备注

这是一个单纯的词法操作，因此有以下行为:

    
    
~~~shell
>>> p = PurePosixPath('foo/..')
>>> p.parent
PurePosixPath('foo')
~~~

如果你想要向上遍历任意文件系统路径，建议首先调用 `Path.resolve()` 以便解析符号链接并消除 `".."` 部分。

PurePath.name¶

    

~~~
一个表示最后路径组件的字符串，排除了驱动器与根目录，如果存在的话:
~~~
    
    
~~~shell
>>> PurePosixPath('my/library/setup.py').name
'setup.py'
~~~

UNC 驱动器名不被考虑:

    
    
~~~shell
>>> PureWindowsPath('//some/share/setup.py').name
'setup.py'
>>> PureWindowsPath('//some/share').name
''
~~~

PurePath.suffix¶

    

~~~
The last dot-separated portion of the final component, if any:
~~~
    
    
~~~shell
>>> PurePosixPath('my/library/setup.py').suffix
'.py'
>>> PurePosixPath('my/library.tar.gz').suffix
'.gz'
>>> PurePosixPath('my/library').suffix
''
~~~

This is commonly called the file extension.

PurePath.suffixes¶

    

~~~
A list of the path's suffixes, often called file extensions:
~~~
    
    
~~~shell
>>> PurePosixPath('my/library.tar.gar').suffixes
['.tar', '.gar']
>>> PurePosixPath('my/library.tar.gz').suffixes
['.tar', '.gz']
>>> PurePosixPath('my/library').suffixes
[]
~~~

PurePath.stem¶

    

~~~
最后一个路径组件，除去后缀:
~~~
    
    
~~~shell
>>> PurePosixPath('my/library.tar.gz').stem
'library.tar'
>>> PurePosixPath('my/library.tar').stem
'library'
>>> PurePosixPath('my/library').stem
'library'
~~~

PurePath.as_posix()¶

    

~~~
返回使用正斜杠（`/`）的路径字符串:
~~~
    
    
~~~shell
>>> p = PureWindowsPath('c:\\windows')
>>> str(p)
'c:\\windows'
>>> p.as_posix()
'c:/windows'
~~~

PurePath.as_uri()¶

    

~~~
将路径表示为 `file` URL。如果并非绝对路径，抛出 [`ValueError`](exceptions.md#ValueError "ValueError")。
~~~
    
    
~~~shell
>>> p = PurePosixPath('/etc/passwd')
>>> p.as_uri()
'file:///etc/passwd'
>>> p = PureWindowsPath('c:/Windows')
>>> p.as_uri()
'file:///c:/Windows'
~~~

PurePath.is_absolute()¶

    

~~~
返回此路径是否为绝对路径。如果路径同时拥有驱动器符与根路径（如果风格允许）则将被认作绝对路径。
~~~
    
    
~~~shell
>>> PurePosixPath('/a/b').is_absolute()
True
>>> PurePosixPath('a/b').is_absolute()
False

>>> PureWindowsPath('c:/a/b').is_absolute()
True
>>> PureWindowsPath('/a/b').is_absolute()
False
>>> PureWindowsPath('c:').is_absolute()
False
>>> PureWindowsPath('//some/share').is_absolute()
True
~~~

PurePath.is_relative_to( _other_ )¶

    

~~~
返回此路径是否相对于 _other_ 的路径。
~~~
    
    
~~~shell
>>> p = PurePath('/etc/passwd')
>>> p.is_relative_to('/etc')
True
>>> p.is_relative_to('/usr')
False
~~~

在 3.9 版本加入.

从 3.12 版起不建议使用，将在 3.14 版中移除: 传入附加参数的做法已被弃用；如果提供了附加参数，它们将与 _other_ 合并。

PurePath.is_reserved()¶

    

~~~
在 `PureWindowsPath`，如果路径是被 Windows 保留的则返回 `True`，否则 `False`。在 `PurePosixPath`，总是返回 `False`。
~~~
    
    
~~~shell
>>> PureWindowsPath('nul').is_reserved()
True
>>> PurePosixPath('nul').is_reserved()
False
~~~

当保留路径上的文件系统被调用，则可能出现玄学失败或者意料之外的效应。

PurePath.joinpath( _* pathsegments_)¶

    

~~~
调用此方法等同于依次将路径与给定的每个 _pathsegments_ 组合到一起:
~~~
    
    
~~~shell
>>> PurePosixPath('/etc').joinpath('passwd')
PurePosixPath('/etc/passwd')
>>> PurePosixPath('/etc').joinpath(PurePosixPath('passwd'))
PurePosixPath('/etc/passwd')
>>> PurePosixPath('/etc').joinpath('init.d', 'apache2')
PurePosixPath('/etc/init.d/apache2')
>>> PureWindowsPath('c:').joinpath('/Program Files')
PureWindowsPath('c:/Program Files')
~~~

PurePath.match( _pattern_ , _*_ , _case_sensitive =None_)¶

    

~~~
将此路径与提供的通配符风格的模式匹配。如果匹配成功则返回 `True`，否则返回 `False`。

如果 _pattern_ 是相对的，则路径可以是相对路径或绝对路径，并且匹配是从右侧完成的：
~~~
    
    
~~~shell
>>> PurePath('a/b.py').match('*.py')
True
>>> PurePath('/a/b/c.py').match('b/*.py')
True
>>> PurePath('/a/b/c.py').match('a/*.py')
False
~~~

如果 _pattern_ 是绝对的，则路径必须是绝对的，并且路径必须完全匹配:

    
    
~~~shell
>>> PurePath('/a.py').match('/*.py')
True
>>> PurePath('a/b.py').match('/*.py')
False
~~~

_pattern_ 可以是另一个路径对象；这样可以加快多个文件匹配同一模式的速度:

    
    
~~~shell
>>> pattern = PurePath('*.py')
>>> PurePath('a/b.py').match(pattern)
True
~~~

与其他方法一样，是否大小写敏感遵循平台的默认规则:

    
    
~~~shell
>>> PurePosixPath('b.py').match('*.PY')
False
>>> PureWindowsPath('b.py').match('*.PY')
True
~~~

将 _case_sensitive_ 设为 `True` 或 `False` 来重载此行为。

在 3.12 版本发生变更: 增加了 _case_sensitive_ 形参。

在 3.13 版本发生变更: Support for the recursive wildcard "`**`" was added. In previous versions, it acted like the non-recursive wildcard "`*`".

PurePath.relative_to( _other_ , _walk_up =False_)¶

    

~~~
计算此路径相对于 _other_ 所表示路径的版本。 如果不可计算，则引发 [`ValueError`](exceptions.md#ValueError "ValueError"):
~~~
    
    
~~~shell
>>> p = PurePosixPath('/etc/passwd')
>>> p.relative_to('/')
PurePosixPath('etc/passwd')
>>> p.relative_to('/etc')
PurePosixPath('passwd')
>>> p.relative_to('/usr')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "pathlib.py", line 941, in relative_to
    raise ValueError(error_message.format(str(self), str(formatted)))
ValueError: '/etc/passwd' is not in the subpath of '/usr' OR one path is relative and the other is absolute.
~~~

当 _walk_up_ 为 False（默认值）时，路径必须以 _other_ 开始。 当参数为 True 时，可能会添加 `..` 条目以形成相对路径。 在所有其他情况下，如引用不同驱动器的路径，则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。:

    
    
~~~shell
>>> p.relative_to('/usr', walk_up=True)
PurePosixPath('../etc/passwd')
>>> p.relative_to('foo', walk_up=True)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "pathlib.py", line 941, in relative_to
    raise ValueError(error_message.format(str(self), str(formatted)))
ValueError: '/etc/passwd' is not on the same drive as 'foo' OR one path is relative and the other is absolute.
~~~

警告

该函数是 `PurePath` 的一部分并适用于字符串。 它不会检查或访问下层的文件结构体。 这可能会影响 _walk_up_ 选项因为它假定路径中不存在符号链接；如果需要处理符号链接请先调用 `resolve()`。

在 3.12 版本发生变更: 增加了 _walk_up_ 形参数（原本的行为与 `walk_up=False` 相同）。

从 3.12 版起不建议使用，将在 3.14 版中移除: 传入附加位置参数的做法已被弃用；如果提供的话，它们将与 _other_ 合并。

PurePath.with_name( _name_ )¶

    

~~~
返回一个新的路径并修改 `name`。如果原本路径没有 name，ValueError 被抛出:
~~~
    
    
~~~shell
>>> p = PureWindowsPath('c:/Downloads/pathlib.tar.gz')
>>> p.with_name('setup.py')
PureWindowsPath('c:/Downloads/setup.py')
>>> p = PureWindowsPath('c:/')
>>> p.with_name('setup.py')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/antoine/cpython/default/Lib/pathlib.py", line 751, in with_name
    raise ValueError("%r has an empty name" % (self,))
ValueError: PureWindowsPath('c:/') has an empty name
~~~

PurePath.with_stem( _stem_ )¶

    

~~~
返回一个带有修改后 `stem` 的新路径。 如果原路径没有名称，则会引发 ValueError:
~~~
    
    
~~~shell
>>> p = PureWindowsPath('c:/Downloads/draft.txt')
>>> p.with_stem('final')
PureWindowsPath('c:/Downloads/final.txt')
>>> p = PureWindowsPath('c:/Downloads/pathlib.tar.gz')
>>> p.with_stem('lib')
PureWindowsPath('c:/Downloads/lib.gz')
>>> p = PureWindowsPath('c:/')
>>> p.with_stem('')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/antoine/cpython/default/Lib/pathlib.py", line 861, in with_stem
    return self.with_name(stem + self.suffix)
  File "/home/antoine/cpython/default/Lib/pathlib.py", line 851, in with_name
    raise ValueError("%r has an empty name" % (self,))
ValueError: PureWindowsPath('c:/') has an empty name
~~~

在 3.9 版本加入.

PurePath.with_suffix( _suffix_ )¶

    

~~~
返回一个新的路径并修改 `suffix`。如果原本的路径没有后缀，新的 _suffix_ 则被追加以代替。如果 _suffix_ 是空字符串，则原本的后缀被移除:
~~~
    
    
~~~shell
>>> p = PureWindowsPath('c:/Downloads/pathlib.tar.gz')
>>> p.with_suffix('.bz2')
PureWindowsPath('c:/Downloads/pathlib.tar.bz2')
>>> p = PureWindowsPath('README')
>>> p.with_suffix('.txt')
PureWindowsPath('README.txt')
>>> p = PureWindowsPath('README.txt')
>>> p.with_suffix('')
PureWindowsPath('README')
~~~

PurePath.with_segments( _* pathsegments_)¶

    

~~~
通过组合给定的 _pathsegments_ 创建一个相同类型的新路径对象。 每当创建派生路径，如从 `parent` 和 `relative_to()` 创建时都会调用此方法。 子类可以覆盖此方法以便向派生路径传递信息，例如:
~~~
    
    
~~~
from pathlib import PurePosixPath

class MyPath(PurePosixPath):
    def __init__(self, *pathsegments, session_id):
        super().__init__(*pathsegments)
        self.session_id = session_id

    def with_segments(self, *pathsegments):
        return type(self)(*pathsegments, session_id=self.session_id)

etc = MyPath('/etc', session_id=42)
hosts = etc / 'hosts'
print(hosts.session_id)  # 42
~~~

在 3.12 版本加入.

## 具体路径¶

具体路径是纯路径的子类。除了后者提供的操作之外，它们还提供了对路径对象进行系统调用的方法。有三种方法可以实例化具体路径:

_class _pathlib.Path( _* pathsegments_)¶

    

~~~
一个 `PurePath` 的子类，此类以当前系统的路径风格表示路径（实例化为 `PosixPath` 或 `WindowsPath`）:
~~~
    
    
~~~shell
>>> Path('setup.py')
PosixPath('setup.py')
~~~

_pathsegments_ 参数的指定和 `PurePath` 相同。

_class _pathlib.PosixPath( _* pathsegments_)¶

    

~~~
一个 `Path` 和 `PurePosixPath` 的子类，此类表示一个非 Windows 文件系统的具体路径:
~~~
    
    
~~~shell
>>> PosixPath('/etc')
PosixPath('/etc')
~~~

_pathsegments_ 参数的指定和 `PurePath` 相同。

在 3.13 版本发生变更: Raises `UnsupportedOperation` on Windows. In previous versions, [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") was raised instead.

_class _pathlib.WindowsPath( _* pathsegments_)¶

    

~~~
`Path` 和 `PureWindowsPath` 的子类，从类表示一个 Windows 文件系统的具体路径:
~~~
    
    
~~~shell
>>> WindowsPath('c:/Program Files/')
WindowsPath('c:/Program Files')
~~~

_pathsegments_ 参数的指定和 `PurePath` 相同。

在 3.13 版本发生变更: Raises `UnsupportedOperation` on non-Windows platforms. In previous versions, [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") was raised instead.

你只能实例化与当前系统风格相同的类（允许系统调用作用于不兼容的路径风格可能在应用程序中导致缺陷或失败）:

    
    
~~~shell
>>> import os
>>> os.name
'posix'
>>> Path('setup.py')
PosixPath('setup.py')
>>> PosixPath('setup.py')
PosixPath('setup.py')
>>> WindowsPath('setup.py')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "pathlib.py", line 798, in __new__
    % (cls.__name__,))
UnsupportedOperation: cannot instantiate 'WindowsPath' on your system
~~~

### 方法¶

除纯路径方法外，实体路径还提供以下方法。 如果系统调用失败（例如因为路径不存在）这些方法中许多都会引发 [`OSError`](exceptions.md#OSError "OSError")。

在 3.8 版本发生变更: 对于包含 OS 层级无法表示字符的路径，`exists()`, `is_dir()`, `is_file()`, `is_mount()`, `is_symlink()`, `is_block_device()`, `is_char_device()`, `is_fifo()`, `is_socket()` 现在将返回 `False` 而不是引发异常。

_classmethod _Path.cwd()¶

    

~~~
返回一个新的表示当前目录的路径对象（和 [`os.getcwd()`](os.md#os.getcwd "os.getcwd") 返回的相同）:
~~~
    
    
~~~shell
>>> Path.cwd()
PosixPath('/home/antoine/pathlib')
~~~

_classmethod _Path.home()¶

    

~~~
返回一个表示用户家目录的新路径对象（与带 `~` 构造的 [`os.path.expanduser()`](os.path.md#os.path.expanduser "os.path.expanduser") 所返回的相同）。 如果无法解析家目录，则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。
~~~
    
    
~~~shell
>>> Path.home()
PosixPath('/home/antoine')
~~~

在 3.5 版本加入.

Path.stat( _*_ , _follow_symlinks =True_)¶

    

~~~
返回一个 [`os.stat_result`](os.md#os.stat_result "os.stat_result") 对象，其中包含有关此路径的信息，例如 [`os.stat()`](os.md#os.stat "os.stat")。 结果会在每次调用此方法时重新搜索。

此方法通常会跟随符号链接；要对 symlink 使用 stat 请添加参数 `follow_symlinks=False`，或者使用 `lstat()`。
~~~
    
    
~~~shell
>>> p = Path('setup.py')
>>> p.stat().st_size
956
>>> p.stat().st_mtime
1327883547.852554
~~~

在 3.10 版本发生变更: 增加了 _follow_symlinks_ 形参。

Path.chmod( _mode_ , _*_ , _follow_symlinks =True_)¶

    

~~~
改变文件模式和权限，和 [`os.chmod()`](os.md#os.chmod "os.chmod") 一样。

此方法通常会跟随符号链接。 某些 Unix 变种支持改变 symlink 本身的权限；在这些平台上你可以添加参数 `follow_symlinks=False`，或者使用 `lchmod()`。
~~~
    
    
~~~shell
>>> p = Path('setup.py')
>>> p.stat().st_mode
33277
>>> p.chmod(0o444)
>>> p.stat().st_mode
33060
~~~

在 3.10 版本发生变更: 增加了 _follow_symlinks_ 形参。

Path.exists( _*_ , _follow_symlinks =True_)¶

    

~~~
如果路径指向现有的文件或目录则返回 `True`。

此方法通常会跟随符号链接；要检查符号链接是否存在，请添加参数 `follow_symlinks=False`。
~~~
    
    
~~~shell
>>> Path('.').exists()
True
>>> Path('setup.py').exists()
True
>>> Path('/etc').exists()
True
>>> Path('nonexistentfile').exists()
False
~~~

在 3.12 版本发生变更: 增加了 _follow_symlinks_ 形参。

Path.expanduser()¶

    

~~~
返回带有扩展 `~` 和 `~user` 构造的新路径，与 [`os.path.expanduser()`](os.path.md#os.path.expanduser "os.path.expanduser") 所返回的相同。 如果无法解析家目录，则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。
~~~
    
    
~~~shell
>>> p = PosixPath('~/films/Monty Python')
>>> p.expanduser()
PosixPath('/home/eric/films/Monty Python')
~~~

在 3.5 版本加入.

Path.glob( _pattern_ , _*_ , _case_sensitive =None_, _follow_symlinks =None_)¶

    

~~~
解析相对于此路径的通配符 _pattern_ ，产生所有匹配的文件:
~~~
    
    
~~~shell
>>> sorted(Path('.').glob('*.py'))
[PosixPath('pathlib.py'), PosixPath('setup.py'), PosixPath('test_pathlib.py')]
>>> sorted(Path('.').glob('*/*.py'))
[PosixPath('docs/conf.py')]
~~~

pattern 的形式与 [`fnmatch`](fnmatch.md#module-fnmatch "fnmatch: Unix shell style filename pattern matching.") 的相同，还增加了 "`**`" 表示 "此目录以及所有子目录，递归"。 换句话说，它启用递归通配:

    
    
~~~shell
>>> sorted(Path('.').glob('**/*.py'))
[PosixPath('build/lib/pathlib.py'),
 PosixPath('docs/conf.py'),
 PosixPath('pathlib.py'),
 PosixPath('setup.py'),
 PosixPath('test_pathlib.py')]
~~~

备注

在一个较大的目录树中使用 "`**`" 模式可能会消耗非常多的时间。

小技巧

Set _follow_symlinks_ to `True` or `False` to improve performance of recursive globbing.

在默认情况下，或当 _case_sensitive_ 关键字参数被设为 `None` 时，该方法将使用特定平台的大小写规则匹配路径：通常，在 POSIX 上区分大小写，而在 Windows 上不区分大小写。将 _case_sensitive_ 设为 `True` 或 `False` 可覆盖此行为。

By default, or when the _follow_symlinks_ keyword-only argument is set to `None`, this method follows symlinks except when expanding "`**`" wildcards. Set _follow_symlinks_ to `True` to always follow symlinks, or `False` to treat all symlinks as files.

引发一个 [审计事件](sys.md#auditing) `pathlib.Path.glob` 附带参数 `self`, `pattern`。

在 3.11 版本发生变更: 如果 _pattern_ 以一个路径名称部分的分隔符结束 ([`sep`](os.md#os.sep "os.sep") 或 [`altsep`](os.md#os.altsep "os.altsep")) 则只返回目录。

在 3.12 版本发生变更: 增加了 _case_sensitive_ 形参。

在 3.13 版本发生变更: 增加了 _follow_symlinks_ 形参。

在 3.13 版本发生变更: Emits [`FutureWarning`](exceptions.md#FutureWarning "FutureWarning") if the pattern ends with "`**`". In a future Python release, patterns with this ending will match both files and directories. Add a trailing slash to match only directories.

Path.group()¶

    

~~~
返回拥有此文件的用户组。如果文件的 GID 无法在系统数据库中找到，将抛出 [`KeyError`](exceptions.md#KeyError "KeyError") 。

在 3.13 版本发生变更: Raises `UnsupportedOperation` if the [`grp`](grp.md#module-grp "grp: The group database \(getgrnam\(\) and friends\). \(Unix\)") module is not available. In previous versions, [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") was raised.

Path.is_dir( _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
Return `True` if the path points to a directory, `False` if it points to another kind of file.

当路径不存在或者是一个破损的符号链接时也会返回 `False`；其他错误（例如权限错误）被传播。

This method normally follows symlinks; to exclude symlinks to directories, add the argument `follow_symlinks=False`.

在 3.13 版本发生变更: 增加了 _follow_symlinks_ 形参。

Path.is_file( _*_ , _follow_symlinks =True_)¶
~~~
    

~~~
Return `True` if the path points to a regular file, `False` if it points to another kind of file.

当路径不存在或者是一个破损的符号链接时也会返回 `False`；其他错误（例如权限错误）被传播。

This method normally follows symlinks; to exclude symlinks, add the argument `follow_symlinks=False`.

在 3.13 版本发生变更: 增加了 _follow_symlinks_ 形参。

Path.is_junction()¶
~~~
    

~~~
如果路径是指向一个接合点则返回 `True`，如果是其他文件类型则返回 `False`。 目前只有 Windows 支持接合点。

在 3.12 版本加入.

Path.is_mount()¶
~~~
    

~~~
如果路径是一个 _挂载点_ : 在文件系统中被其他不同文件系统挂载的位置则返回 `True`。 在 POSIX 上，此函数将检查 _path_ 的上一级 `path/..` 是否位于和 _path_ 不同的设备中，或者 `path/..` 和 _path_ 是否指向位于相同设置的相同 i-node --- 这应当能检测所有 Unix 和 POSIX 变种上的挂载点。 在 Windows 上，挂载点是被视为驱动器盘符的根目录 (例如 `c:\`)、UNC 共享目录 (例如 `\\server\share`) 或已挂载的文件系统目录。

在 3.7 版本加入.

在 3.12 版本发生变更: 添加了 Windows 支持。

Path.is_symlink()¶
~~~
    

~~~
如果路径指向符号链接则返回 `True`， 否则 `False`。

如果路径不存在也返回 `False`；其他错误（例如权限错误）被传播。

Path.is_socket()¶
~~~
    

~~~
如果路径指向一个 Unix socket 文件（或者指向 Unix socket 文件的符号链接）则返回 `True`，如果指向其他类型的文件则返回 `False`。

当路径不存在或者是一个破损的符号链接时也会返回 `False`；其他错误（例如权限错误）被传播。

Path.is_fifo()¶
~~~
    

~~~
如果路径指向一个先进先出存储（或者指向先进先出存储的符号链接）则返回 `True` ，指向其他类型的文件则返回 `False`。

当路径不存在或者是一个破损的符号链接时也会返回 `False`；其他错误（例如权限错误）被传播。

Path.is_block_device()¶
~~~
    

~~~
如果文件指向一个块设备（或者指向块设备的符号链接）则返回 `True`，指向其他类型的文件则返回 `False`。

当路径不存在或者是一个破损的符号链接时也会返回 `False`；其他错误（例如权限错误）被传播。

Path.is_char_device()¶
~~~
    

~~~
如果路径指向一个字符设备（或指向字符设备的符号链接）则返回 `True`，指向其他类型的文件则返回 `False`。

当路径不存在或者是一个破损的符号链接时也会返回 `False`；其他错误（例如权限错误）被传播。

Path.iterdir()¶
~~~
    

~~~
当路径指向一个目录时，产生该路径下的对象的路径:
~~~
    
    
~~~shell
>>> p = Path('docs')
>>> for child in p.iterdir(): child
...
PosixPath('docs/conf.py')
PosixPath('docs/_templates')
PosixPath('docs/make.bat')
PosixPath('docs/index.rst')
PosixPath('docs/_build')
PosixPath('docs/_static')
PosixPath('docs/Makefile')
~~~

子条目会以任意顺序生成，并且不包括特殊条目 `'.'` 和 `'..'`。 如果迭代器创建之后有文件在目录中被移除或添加，是否要包括该文件所对应的路径对象并没有明确规定。

Path.walk( _top_down =True_, _on_error =None_, _follow_symlinks =False_)¶

    

~~~
通过对目录树自上而下或自下而上的遍历来生成其中的文件名。

对于根位置为 _self_ 的目录树中的每个目录（包括 _self_ 但不包括 '.' 和 '..'），该方法会产生一个 3 元组 `(dirpath, dirnames, filenames)`。

_dirpath_ 是指向当前正被遍历到的目录的 `Path`， _dirnames_ 是由表示 _dirpath_ 中子目录名称的字符串组成的列表 (不包括 `'.'` 和 `'..'`)， _filenames_ 是由表示 _dirpath_ 中非目录文件名称的字符串组成的列表。 要获取 _dirpath_ 中文件或目录的完整路径 (以 _self_ 开头)，可使用 `dirpath / name`。 这些列表是否排序取决于具体的文件系统。

如果可选参数 _top_down_ 为（默认的）真值，则会在所有子目录的三元组生成之前生成父目录的三元组（目录是自上而下遍历的）。 如果 _top_down_ 为假值，则会在所有子目录的三元组生成之后再生成父目录的三元组（目录是是自下而上遍历的）。无论 _top_down_ 的值是什么，都会在遍历目录及其子目录的三元组之前提取子目录列表。

当 _top_down_ 为真值时，调用者可以就地修改 _dirnames_ 列表（例如，使用 [`del`](../reference/simple_stmts.md#del) 或切片赋值），而 `Path.walk()` 将只向名称保留在 _dirnames_ 中的子目录递归。 这可用于减少搜索量，或加入特定的访问顺序，甚至是在继续 `Path.walk()` 之前通知 `Path.walk()` 调用者创建或重命名的目录的相关信息。 当 _top_down_ 为假值时修改 _dirnames_ 不会影响 `Path.walk()` 的行为，因为在 _dirnames_ 被提供给调用者时 _dirnames_ 中的目录已经生成了。

在默认情况下，来自 [`os.scandir()`](os.md#os.scandir "os.scandir") 的错误将被忽略。 如果指定了可选参数 _on_error_ ，则它应为一个可调用对象；调用它需要传入一个参数，即 [`OSError`](exceptions.md#OSError "OSError") 的实例。 该可调用对象能处理错误以继续执行遍历或是重新引发错误以停止遍历。 请注意可以通过异常对象的 `filename` 属性来获取文件名。

在默认情况下，`Path.walk()` 不会跟踪符号链接，而是将其添加到 _filenames_ 列表中。 将 _follow_symlinks*设为真值可解析符号链接并根据它们的目标将其放入*dirnames_ 和 _filenames_ 中，从而（在受支持的系统上）访问符号链接所指向的目录。

备注

请注意将 _follow_symlinks_ 设为真值会在链接指向自身的父目录时导致无限递归。 `Path.walk()` 不会跟踪已它访问过的目录。

备注

`Path.walk()` 会假定在执行过程中它所遍历的目录没有被修改。 例如，如果 _dirnames_ 中的某个目录已被替换为符号链接并且 _follow_symlinks_ 为假值，则 `Path.walk()` 仍会尝试进入该目录。 为防止出现这种行为，请相应地移除 _dirnames_ 中的目录。

备注

与 [`os.walk()`](os.md#os.walk "os.walk") 不同，当 _follow_symlinks_ 为假值时 `Path.walk()` 会在 _filenames_ 中列出指向目录的符号链接。

这个例子显示每个目录中所有文件使用的总字节数，忽略其中的 `__pycache__` 目录:
~~~
    
    
~~~
from pathlib import Path
for root, dirs, files in Path("cpython/Lib/concurrent").walk(on_error=print):
  print(
      root,
      "consumes",
      sum((root / file).stat().st_size for file in files),
      "bytes in",
      len(files),
      "non-directory files"
  )
  if '__pycache__' in dirs:
        dirs.remove('__pycache__')
~~~

下一个例子是 [`shutil.rmtree()`](shutil.md#shutil.rmtree "shutil.rmtree") 的一个简单实现。 由于 `rmdir()` 不允许在目录为空之前删除该目录因此自下而上地遍历目录树是至关重要的:

    
    
~~~
# Delete everything reachable from the directory "top".
# CAUTION:  This is dangerous! For example, if top == Path('/'),
# it could delete all of your files.
for root, dirs, files in top.walk(top_down=False):
    for name in files:
        (root / name).unlink()
    for name in dirs:
        (root / name).rmdir()
~~~

在 3.12 版本加入.

Path.lchmod( _mode_ )¶

    

~~~
就像 `Path.chmod()` 但是如果路径指向符号链接则是修改符号链接的模式，而不是修改符号链接的目标。

Path.lstat()¶
~~~
    

~~~
就和 `Path.stat()` 一样，但是如果路径指向符号链接，则是返回符号链接而不是目标的信息。

Path.mkdir( _mode =0o777_, _parents =False_, _exist_ok =False_)¶
~~~
    

~~~
新建给定路径的目录。如果给出了 _mode_ ，它将与当前进程的 `umask` 值合并来决定文件模式和访问标志。如果路径已经存在，则抛出 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError")。

如果 _parents_ 为真值，任何找不到的父目录都会伴随着此路径被创建；它们会以默认权限被创建，而不考虑 _mode_ 设置（模仿 POSIX 的 `mkdir -p` 命令）。

如果 _parents_ 为假值（默认），则找不到的父级目录会引发 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError")。

如果 _exist_ok_ 为 false（默认），则在目标已存在的情况下抛出 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError")。

如果 _exist_ok_ 为 true， 则 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError") 异常将被忽略（和 POSIX `mkdir -p` 命令行为相同），但是只有在最后一个路径组件不是现存的非目录文件时才生效。

在 3.5 版本发生变更: _exist_ok_ 形参被加入。

Path.open( _mode ='r'_, _buffering =-1_, _encoding =None_, _errors =None_, _newline =None_)¶
~~~
    

~~~
打开路径指向的文件，就像内置的 [`open()`](functions.md#open "open") 函数所做的一样:
~~~
    
    
~~~shell
>>> p = Path('setup.py')
>>> with p.open() as f:
...     f.readline()
...
'#!/usr/bin/env python3\n'
~~~

Path.owner()¶

    

~~~
返回拥有此文件的用户名。如果文件的 UID 无法在系统数据库中找到，则抛出 [`KeyError`](exceptions.md#KeyError "KeyError")。

在 3.13 版本发生变更: Raises `UnsupportedOperation` if the [`pwd`](pwd.md#module-pwd "pwd: The password database \(getpwnam\(\) and friends\). \(Unix\)") module is not available. In previous versions, [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") was raised.

Path.read_bytes()¶
~~~
    

~~~
以字节对象的形式返回路径指向的文件的二进制内容:
~~~
    
    
~~~shell
>>> p = Path('my_binary_file')
>>> p.write_bytes(b'Binary file contents')
20
>>> p.read_bytes()
b'Binary file contents'
~~~

在 3.5 版本加入.

Path.read_text( _encoding =None_, _errors =None_)¶

    

~~~
以字符串形式返回路径指向的文件的解码后文本内容。
~~~
    
    
~~~shell
>>> p = Path('my_text_file')
>>> p.write_text('Text file contents')
18
>>> p.read_text()
'Text file contents'
~~~

文件先被打开然后关闭。有和 [`open()`](functions.md#open "open") 一样的可选形参。

在 3.5 版本加入.

Path.readlink()¶

    

~~~
返回符号链接所指向的路径（即 [`os.readlink()`](os.md#os.readlink "os.readlink") 的返回值）:
~~~
    
    
~~~shell
>>> p = Path('mylink')
>>> p.symlink_to('setup.py')
>>> p.readlink()
PosixPath('setup.py')
~~~

在 3.9 版本加入.

在 3.13 版本发生变更: Raises `UnsupportedOperation` if [`os.readlink()`](os.md#os.readlink "os.readlink") is not available. In previous versions, [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") was raised.

Path.rename( _target_ )¶

    

~~~
将文件名目录重命名为给定的 _target_ ，并返回一个新的指向 _target_ 的 Path 实例。 在 Unix 上，如果 _target_ 存在且为一个文件，如果用户有足够权限则它将被静默地替换。 在 Windows 上，如果 _target_ 存在，则将会引发 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError")。 _target_ 可以是一个字符串或者另一个路径对象:
~~~
    
    
~~~shell
>>> p = Path('foo')
>>> p.open('w').write('some text')
9
>>> target = Path('bar')
>>> p.rename(target)
PosixPath('bar')
>>> target.open().read()
'some text'
~~~

目标路径可能为绝对或相对路径。 相对路径将被解释为相对于当前工作目录，而 _不是_ 相对于 Path 对象的目录。

它根据 [`os.rename()`](os.md#os.rename "os.rename") 实现并给出了同样的保证。

在 3.8 版本发生变更: 添加了返回值，返回新的 Path 实例。

Path.replace( _target_ )¶

    

~~~
将文件或目录重命名为给定的 _target_ ，并返回一个新的指向 _target_ 的 Path 实例。 如果 _target_ 指向一个现有文件或空目录，则它将被无条件地替换。

目标路径可能为绝对或相对路径。 相对路径将被解释为相对于当前工作目录，而 _不是_ 相对于 Path 对象的目录。

在 3.8 版本发生变更: 添加了返回值，返回新的 Path 实例。

Path.absolute()¶
~~~
    

~~~
改为绝对路径，不会执行正规化或解析符号链接。 返回一个新的路径对象:
~~~
    
    
~~~shell
>>> p = Path('tests')
>>> p
PosixPath('tests')
>>> p.absolute()
PosixPath('/home/antoine/pathlib/tests')
~~~

Path.resolve( _strict =False_)¶

    

~~~
将路径绝对化，解析任何符号链接。返回新的路径对象:
~~~
    
    
~~~shell
>>> p = Path()
>>> p
PosixPath('.')
>>> p.resolve()
PosixPath('/home/antoine/pathlib')
~~~

"`..`" 组件也将被消除（只有这一种方法这么做）:

    
    
~~~shell
>>> p = Path('docs/../setup.py')
>>> p.resolve()
PosixPath('/home/antoine/pathlib/setup.py')
~~~

如果路径不存在并且 _strict_ 设为 `True`，则抛出 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError")。如果 _strict_ 为 `False`，则路径将被尽可能地解析并且任何剩余部分都会被不检查是否存在地追加。如果在解析路径上发生无限循环，则抛出 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

在 3.6 版本发生变更: 增加了 _strict_ 参数（3.6 之前的行为是默认采用 strict 模式）。

Path.rglob( _pattern_ , _*_ , _case_sensitive =None_, _follow_symlinks =None_)¶

    

~~~
递归地对给定的相对 _pattern_ 执行 glob 通配。 这类似于当调用 `Path.glob()` 时在 _pattern_ 之前加上 "`**/`"，其中 _patterns_ 与 [`fnmatch`](fnmatch.md#module-fnmatch "fnmatch: Unix shell style filename pattern matching.") 中的相同:
~~~
    
    
~~~shell
>>> sorted(Path().rglob("*.py"))
[PosixPath('build/lib/pathlib.py'),
 PosixPath('docs/conf.py'),
 PosixPath('pathlib.py'),
 PosixPath('setup.py'),
 PosixPath('test_pathlib.py')]
~~~

在默认情况下，或当 _case_sensitive_ 关键字参数被设为 `None` 时，该方法将使用特定平台的大小写规则匹配路径：通常，在 POSIX 上区分大小写，而在 Windows 上不区分大小写。将 _case_sensitive_ 设为 `True` 或 `False` 可覆盖此行为。

By default, or when the _follow_symlinks_ keyword-only argument is set to `None`, this method follows symlinks except when expanding "`**`" wildcards. Set _follow_symlinks_ to `True` to always follow symlinks, or `False` to treat all symlinks as files.

引发一个 [审计事件](sys.md#auditing) `pathlib.Path.rglob` 附带参数 `self`, `pattern`。

在 3.11 版本发生变更: 如果 _pattern_ 以一个路径名称部分的分隔符结束 ([`sep`](os.md#os.sep "os.sep") 或 [`altsep`](os.md#os.altsep "os.altsep")) 则只返回目录。

在 3.12 版本发生变更: 增加了 _case_sensitive_ 形参。

在 3.13 版本发生变更: 增加了 _follow_symlinks_ 形参。

Path.rmdir()¶

    

~~~
移除此目录。此目录必须为空的。

Path.samefile( _other_path_ )¶
~~~
    

~~~
返回此目录是否指向与可能是字符串或者另一个路径对象的 _other_path_ 相同的文件。语义类似于 [`os.path.samefile()`](os.path.md#os.path.samefile "os.path.samefile") 与 [`os.path.samestat()`](os.path.md#os.path.samestat "os.path.samestat")。

如果两者都以同一原因无法访问，则抛出 [`OSError`](exceptions.md#OSError "OSError")。
~~~
    
    
~~~shell
>>> p = Path('spam')
>>> q = Path('eggs')
>>> p.samefile(q)
False
>>> p.samefile('spam')
True
~~~

在 3.5 版本加入.

Path.symlink_to( _target_ , _target_is_directory =False_)¶

    

~~~
将此路径创建为指向 _target_ 的符号链接。在 Windows 下，如果链接的目标是一个目录则 _target_is_directory_ 必须为 true （默认为 `False`）。在 POSIX 下， _target_is_directory_ 的值将被忽略。
~~~
    
    
~~~shell
>>> p = Path('mylink')
>>> p.symlink_to('setup.py')
>>> p.resolve()
PosixPath('/home/antoine/pathlib/setup.py')
>>> p.stat().st_size
956
>>> p.lstat().st_size
8
~~~

备注

参数的顺序（link, target) 和 [`os.symlink()`](os.md#os.symlink "os.symlink") 是相反的。

在 3.13 版本发生变更: Raises `UnsupportedOperation` if [`os.symlink()`](os.md#os.symlink "os.symlink") is not available. In previous versions, [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") was raised.

Path.hardlink_to( _target_ )¶

    

~~~
将此路径设为一个指向与 _target_ 相同文件的硬链接。

备注

参数顺序 (link, target) 和 [`os.link()`](os.md#os.link "os.link") 是相反的。

在 3.10 版本加入.

在 3.13 版本发生变更: Raises `UnsupportedOperation` if [`os.link()`](os.md#os.link "os.link") is not available. In previous versions, [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError") was raised.

Path.touch( _mode =0o666_, _exist_ok =True_)¶
~~~
    

~~~
将给定的路径创建为文件。如果给出了 _mode_ 它将与当前进程的 `umask` 值合并以确定文件的模式和访问标志。如果文件已经存在，则当 _exist_ok_ 为 true 则函数仍会成功（并且将它的修改事件更新为当前事件），否则抛出 [`FileExistsError`](exceptions.md#FileExistsError "FileExistsError")。

Path.unlink( _missing_ok =False_)¶
~~~
    

~~~
移除此文件或符号链接。如果路径指向目录，则用 `Path.rmdir()` 代替。

如果 _missing_ok_ 为假值（默认），则如果路径不存在将会引发 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError")。

如果 _missing_ok_ 为真值，则 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError") 异常将被忽略（和 POSIX `rm -f` 命令的行为相同）。

在 3.8 版本发生变更: 增加了 _missing_ok_ 形参。

Path.write_bytes( _data_ )¶
~~~
    

~~~
将文件以二进制模式打开，写入 _data_ 并关闭:
~~~
    
    
~~~shell
>>> p = Path('my_binary_file')
>>> p.write_bytes(b'Binary file contents')
20
>>> p.read_bytes()
b'Binary file contents'
~~~

一个同名的现存文件将被覆盖。

在 3.5 版本加入.

Path.write_text( _data_ , _encoding =None_, _errors =None_, _newline =None_)¶

    

~~~
将文件以文本模式打开，写入 _data_ 并关闭:
~~~
    
    
~~~shell
>>> p = Path('my_text_file')
>>> p.write_text('Text file contents')
18
>>> p.read_text()
'Text file contents'
~~~

同名的现有文件会被覆盖。 可选形参的含义与 [`open()`](functions.md#open "open") 的相同。

在 3.5 版本加入.

在 3.10 版本发生变更: 增加了 _newline_ 形参。

## 对应的 [`os`](os.md#module-os "os: Miscellaneous operating system interfaces.") 模块的工具¶

以下是一个映射了 [`os`](os.md#module-os "os: Miscellaneous operating system interfaces.") 与 `PurePath`/`Path` 对应相同的函数的表。

备注

以下函数/方法对并不全都是等价的。 在它们之中，有的虽然具有相互重叠的使用场景，但其语义有所不同。 这包括 [`os.path.abspath()`](os.path.md#os.path.abspath "os.path.abspath") 与 `Path.absolute()`, [`os.path.relpath()`](os.path.md#os.path.relpath "os.path.relpath") 与 `PurePath.relative_to()`。

[`os`](os.md#module-os "os: Miscellaneous operating system interfaces.") 和 [`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.")

|

`pathlib`  
  
---|---  
  
[`os.path.abspath()`](os.path.md#os.path.abspath "os.path.abspath")

|

`Path.absolute()` [1]  
  
[`os.path.realpath()`](os.path.md#os.path.realpath "os.path.realpath")

|

`Path.resolve()`  
  
[`os.chmod()`](os.md#os.chmod "os.chmod")

|

`Path.chmod()`  
  
[`os.mkdir()`](os.md#os.mkdir "os.mkdir")

|

`Path.mkdir()`  
  
[`os.makedirs()`](os.md#os.makedirs "os.makedirs")

|

`Path.mkdir()`  
  
[`os.rename()`](os.md#os.rename "os.rename")

|

`Path.rename()`  
  
[`os.replace()`](os.md#os.replace "os.replace")

|

`Path.replace()`  
  
[`os.rmdir()`](os.md#os.rmdir "os.rmdir")

|

`Path.rmdir()`  
  
[`os.remove()`](os.md#os.remove "os.remove"), [`os.unlink()`](os.md#os.unlink "os.unlink")

|

`Path.unlink()`  
  
[`os.getcwd()`](os.md#os.getcwd "os.getcwd")

|

`Path.cwd()`  
  
[`os.path.exists()`](os.path.md#os.path.exists "os.path.exists")

|

`Path.exists()`  
  
[`os.path.expanduser()`](os.path.md#os.path.expanduser "os.path.expanduser")

|

`Path.expanduser()` 和 `Path.home()`  
  
[`os.listdir()`](os.md#os.listdir "os.listdir")

|

`Path.iterdir()`  
  
[`os.walk()`](os.md#os.walk "os.walk")

|

`Path.walk()`  
  
[`os.path.isdir()`](os.path.md#os.path.isdir "os.path.isdir")

|

`Path.is_dir()`  
  
[`os.path.isfile()`](os.path.md#os.path.isfile "os.path.isfile")

|

`Path.is_file()`  
  
[`os.path.islink()`](os.path.md#os.path.islink "os.path.islink")

|

`Path.is_symlink()`  
  
[`os.link()`](os.md#os.link "os.link")

|

`Path.hardlink_to()`  
  
[`os.symlink()`](os.md#os.symlink "os.symlink")

|

`Path.symlink_to()`  
  
[`os.readlink()`](os.md#os.readlink "os.readlink")

|

`Path.readlink()`  
  
[`os.path.relpath()`](os.path.md#os.path.relpath "os.path.relpath")

|

`PurePath.relative_to()` [2]  
  
[`os.stat()`](os.md#os.stat "os.stat")

|

`Path.stat()`, `Path.owner()`, `Path.group()`  
  
[`os.path.isabs()`](os.path.md#os.path.isabs "os.path.isabs")

|

`PurePath.is_absolute()`  
  
[`os.path.join()`](os.path.md#os.path.join "os.path.join")

|

`PurePath.joinpath()`  
  
[`os.path.basename()`](os.path.md#os.path.basename "os.path.basename")

|

`PurePath.name`  
  
[`os.path.dirname()`](os.path.md#os.path.dirname "os.path.dirname")

|

`PurePath.parent`  
  
[`os.path.samefile()`](os.path.md#os.path.samefile "os.path.samefile")

|

`Path.samefile()`  
  
[`os.path.splitext()`](os.path.md#os.path.splitext "os.path.splitext")

|

`PurePath.stem` 和 `PurePath.suffix`  
  
备注

[1]

[`os.path.abspath()`](os.path.md#os.path.abspath "os.path.abspath") 会对结果路径执行正规化，当存在符号链接时这可能改变其含义，而 `Path.absolute()` 则不会。

[2]

`PurePath.relative_to()` 要求 `self` 为参数的子路径，但 [`os.path.relpath()`](os.path.md#os.path.relpath "os.path.relpath") 则不要求。

