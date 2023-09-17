# 如何将 Python 2 代码移植到 Python 3¶

作者:

    

~~~
Brett Cannon

摘要

Python 2 生命期在 2020 年初正式结束。 这意味着 Python 2 将不再接受新的错误报告、修复或更改。

本指南旨在为你的代码提供一条通往 Python 3 的路径，其中包括作为第一步的如何与 Python 2 兼容。

如果您希望迁移扩展模块而不是纯 Python 代码，请参阅 [将扩展模块移植到 Python 3](cporting.md#cporting-howto)。

已归档的 [python-porting](https://mail.python.org/pipermail/python-porting/) 邮件列表可能包含一些有用的指导。

## 简要说明¶

要在单一代码库中实现 Python 2/3 兼容性，基本步骤如下：

  1. 只担心支持Python 2.7的问题

  2. 确保你有良好的测试覆盖率（可以用 [coverage.py](https://pypi.org/project/coverage)；`python -m pip install coverage`）。

  3. Learn the differences between Python 2 and 3

  4. 使用 [Futurize](https://python-future.org/automatic_conversion.md) ([或Modernize](https://python-modernize.readthedocs.io/)) 来更新你的代码 (例如 `python -m pip install future`)。

  5. 使用 [Pylint](https://pypi.org/project/pylint) 来帮助确保你在Python 3支持上不倒退(`python -m pip install pylint`)

  6. 使用 [caniusepython3](https://pypi.org/project/caniusepython3) 来找出你的哪些依赖关系阻碍了你对 Python 3 的使用 (`python -m pip install caniusepython3`)

  7. Once your dependencies are no longer blocking you, use continuous integration to make sure you stay compatible with Python 2 and 3 ([tox](https://pypi.org/project/tox) can help test against multiple versions of Python; `python -m pip install tox`)

  8. Consider using optional static type checking to make sure your type usage works in both Python 2 and 3 (e.g. use [mypy](https://mypy-lang.org/) to check your typing under both Python 2 and Python 3; `python -m pip install mypy`).

备注

注意：使用 `python -m pip install` 来确保你发起调用的 `pip` 就是当前使用的 Python 所安装的那一个，无论它是系统级的 `pip` 还是安装在 [虚拟环境](../tutorial/venv.md#tut-venv) 中的。

## 详情¶

Even if other factors - say, dependencies over which you have no control - still require you to support Python 2, that does not prevent you taking the step of including Python 3 support.

Most changes required to support Python 3 lead to cleaner code using newer practices even in Python 2 code.

### Different versions of Python 2¶

Ideally, your code should be compatible with Python 2.7, which was the last supported version of Python 2.

Some of the tools mentioned in this guide will not work with Python 2.6.

If absolutely necessary, the [six](https://pypi.org/project/six) project can help you support Python 2.5 and 3 simultaneously. Do realize, though, that nearly all the projects listed in this guide will not be available to you.

If you are able to skip Python 2.5 and older, the required changes to your code will be minimal. At worst you will have to use a function instead of a method in some instances or have to import a function instead of using a built-in one.

### 确保你在你的 `setup.py` 文件中指定适当的版本支持¶

在你的 `setup.py` 文件中，你应该有适当的 [trove classifier](https://pypi.org/classifiers) 指定你支持哪些版本的 Python。由于你的项目还不支持 Python 3，你至少应该指定 `Programming Language :: Python :: 2 :: Only`。理想情况下，你还应该指定你所支持的Python的每个主要/次要版本，例如：`Programming Language :: Python :: 2.7`。.

### 良好的测试覆盖率¶

一旦你的代码支持了你希望的Python 2的最老版本，你将希望确保你的测试套件有良好的覆盖率。一个好的经验法则是，如果你想对你的测试套件有足够的信心，在让工具重写你的代码后出现的任何故障都是工具中的实际错误，而不是你的代码中的错误。如果你想要一个目标数字，试着获得超过80%的覆盖率（如果你发现很难获得好于90%的覆盖率，也不要感到遗憾）。如果你还没有一个测量测试覆盖率的工具，[那么推荐使用coverage.py](https://pypi.org/project/coverage)。

### Be aware of the differences between Python 2 and 3¶

Once you have your code well-tested you are ready to begin porting your code to Python 3! But to fully understand how your code is going to change and what you want to look out for while you code, you will want to learn what changes Python 3 makes in terms of Python 2.

Some resources for understanding the differences and their implications for you code:

  * the ["What's New"](../whatsnew/index.md#whatsnew-index) doc for each release of Python 3

  * the [Porting to Python 3](http://python3porting.com/) book (which is free online)

  * the handy [cheat sheet](https://python-future.org/compatible_idioms.md) from the Python-Future project.

### 更新代码¶

There are tools available that can port your code automatically.

[Futurize](https://python-future.org/automatic_conversion.md) does its best to make Python 3 idioms and practices exist in Python 2, e.g. backporting the `bytes` type from Python 3 so that you have semantic parity between the major versions of Python. This is the better approach for most cases.

[Modernize](https://python-modernize.readthedocs.io/), on the other hand, is more conservative and targets a Python 2/3 subset of Python, directly relying on [six](https://pypi.org/project/six) to help provide compatibility.

A good approach is to run the tool over your test suite first and visually inspect the diff to make sure the transformation is accurate. After you have transformed your test suite and verified that all the tests still pass as expected, then you can transform your application code knowing that any tests which fail is a translation failure.

Unfortunately the tools can't automate everything to make your code work under Python 3, and you will also need to read the tools' documentation in case some options you need are turned off by default.

Key issues to be aware of and check for:

#### 除法¶

In Python 3, `5 / 2 == 2.5` and not `2` as it was in Python 2; all division between `int` values result in a `float`. This change has actually been planned since Python 2.2 which was released in 2002. Since then users have been encouraged to add `from __future__ import division` to any and all files which use the `/` and `//` operators or to be running the interpreter with the `-Q` flag. If you have not been doing this then you will need to go through your code and do two things:

  1. 添加 `from __future__ import division` 到你的文件。

  2. 根据需要更新任何除法运算符，要么使用 `//` 来使用向下取整除法，要么继续使用 `/` 并得到一个浮点数

之所以没有简单地将 `/` 自动翻译成 `//`，是因为如果一个对象定义了一个 `__truediv__` 方法，但没有定义 `__floordiv__`，那么你的代码就会运行失败（例如，一个用户定义的类用 `/` 来表示一些操作，但没有用 `//` 来表示同样的事情或根本没有定义）。

#### 文本与二进制数据¶

在Python 2中，你可以对文本和二进制数据都使用 `str``类型。不幸的是，这两个不同概念的融合可能会导致脆弱的代码，有时对任何一种数据都有效，有时则无效。如果人们没有明确说明某种接受``str``东西可以接受文本或二进制数据，而不是一种特定的类型，这也会导致API的混乱。这使情况变得复杂，特别是对于任何支持多种语言的人来说，因为API在声称支持文本数据时不会显式支持``unicode`。

Python 3 made text and binary data distinct types that cannot simply be mixed together. For any code that deals only with text or only binary data, this separation doesn't pose an issue. But for code that has to deal with both, it does mean you might have to now care about when you are using text compared to binary data, which is why this cannot be entirely automated.

Decide which APIs take text and which take binary (it is **highly** recommended you don't design APIs that can take both due to the difficulty of keeping the code working; as stated earlier it is difficult to do well). In Python 2 this means making sure the APIs that take text can work with `unicode` and those that work with binary data work with the `bytes` type from Python 3 (which is a subset of `str` in Python 2 and acts as an alias for `bytes` type in Python 2). Usually the biggest issue is realizing which methods exist on which types in Python 2 and 3 simultaneously (for text that's `unicode` in Python 2 and `str` in Python 3, for binary that's `str`/`bytes` in Python 2 and `bytes` in Python 3).

The following table lists the **unique** methods of each data type across Python 2 and 3 (e.g., the `decode()` method is usable on the equivalent binary data type in either Python 2 or 3, but it can't be used by the textual data type consistently between Python 2 and 3 because `str` in Python 3 doesn't have the method). Do note that as of Python 3.5 the `__mod__` method was added to the bytes type.

**文本数据**

|

**二进制数据**  
  
---|---  
  
|

decode  
  
encode

|  
  
format

|  
  
isdecimal

|  
  
isnumeric

|  
  
通过在你的代码边缘对二进制数据和文本进行编码和解码，可以使这种区分更容易处理。这意味着，当你收到二进制数据的文本时，你应该立即对其进行解码。而如果你的代码需要将文本作为二进制数据发送，那么就尽可能晚地对其进行编码。这使得你的代码在内部只与文本打交道，从而不必再去跟踪你所处理的数据类型。

The next issue is making sure you know whether the string literals in your code represent text or binary data. You should add a `b` prefix to any literal that presents binary data. For text you should add a `u` prefix to the text literal. (There is a [`__future__`](../library/__future__.md#module-__future__ "__future__: Future statement definitions") import to force all unspecified literals to be Unicode, but usage has shown it isn't as effective as adding a `b` or `u` prefix to all literals explicitly)

You also need to be careful about opening files. Possibly you have not always bothered to add the `b` mode when opening a binary file (e.g., `rb` for binary reading). Under Python 3, binary files and text files are clearly distinct and mutually incompatible; see the [`io`](../library/io.md#module-io "io: Core tools for working with streams.") module for details. Therefore, you **must** make a decision of whether a file will be used for binary access (allowing binary data to be read and/or written) or textual access (allowing text data to be read and/or written). You should also use [`io.open()`](../library/io.md#io.open "io.open") for opening files instead of the built-in [`open()`](../library/functions.md#open "open") function as the [`io`](../library/io.md#module-io "io: Core tools for working with streams.") module is consistent from Python 2 to 3 while the built-in [`open()`](../library/functions.md#open "open") function is not (in Python 3 it's actually [`io.open()`](../library/io.md#io.open "io.open")). Do not bother with the outdated practice of using [`codecs.open()`](../library/codecs.md#codecs.open "codecs.open") as that's only necessary for keeping compatibility with Python 2.5.

The constructors of both `str` and `bytes` have different semantics for the same arguments between Python 2 and 3. Passing an integer to `bytes` in Python 2 will give you the string representation of the integer: `bytes(3) == '3'`. But in Python 3, an integer argument to `bytes` will give you a bytes object as long as the integer specified, filled with null bytes: `bytes(3) == b'\x00\x00\x00'`. A similar worry is necessary when passing a bytes object to `str`. In Python 2 you just get the bytes object back: `str(b'3') == b'3'`. But in Python 3 you get the string representation of the bytes object: `str(b'3') == "b'3'"`.

最后，二进制数据的索引需要仔细处理（切片 **不需要** 任何特殊处理）。在 Python 2 中 `b'123'[1] == b'2'`，而在 Python 3 中 `b'123'[1] == 50`。 因为二进制数据只是二进制数的集合，Python 3 会返回你索引的字节的整数值。 但是在 Python 2 中，因为 `bytes == str`，索引会返回一个单项的字节片断。 [six](https://pypi.org/project/six) 项目有一个名为 `six.indexbytes()` 的函数，它将像在 Python 3 中一样返回一个整数: `six.indexbytes(b'123', 1)`。

总结一下：

  1. 决定你的API中哪些采用文本，哪些采用二进制数据

  2. 确保你对文本工作的代码也能对 `unicode` 工作，对二进制数据的代码在Python 2中能对 `bytes` 工作（关于每种类型不能使用的方法，见上表）。

  3. 用 `b` 前缀标记所有二进制字词，用 `u` 前缀标记文本字词

  4. 尽快将二进制数据解码为文本，尽可能晚地将文本编码为二进制数据

  5. 使用 [`io.open()`](../library/io.md#io.open "io.open") 打开文件，并确保在适当时候指定 `b` 模式。

  6. 在对二进制数据进行索引时要小心

#### 使用特征检测而不是版本检测¶

你不可避免地会有一些代码需要根据运行的 Python 版本来选择要做什么。做到这一点的最好方法是对你运行的 Python 版本是否支持你所需要的东西进行特征检测。如果由于某种原因这不起作用，那么你应该让版本检测针对 Python 2 而不是 Python 3。为了帮助解释这个问题，让我们看一个例子。

假设你需要访问 [`importlib`](../library/importlib.md#module-importlib "importlib: The implementation of the import machinery.") 的一个功能，该功能自Python 3.3开始在Python的标准库中提供，并且通过PyPI上的 [importlib2](https://pypi.org/project/importlib2) 提供给Python 2。你可能会想写代码来访问例如 [`importlib.abc`](../library/importlib.md#module-importlib.abc "importlib.abc: Abstract base classes related to import") 模块，方法如下:
~~~
    
    
~~~
import sys

if sys.version_info[0] == 3:
    from importlib import abc
else:
    from importlib2 import abc
~~~

这段代码的问题是，当Python 4出来的时候会发生什么？最好是将Python 2作为例外情况，而不是Python 3，并假设未来的Python版本与Python 3的兼容性比Python 2更强:

    
    
~~~
import sys

if sys.version_info[0] > 2:
    from importlib import abc
else:
    from importlib2 import abc
~~~

不过，最好的解决办法是根本不做版本检测，而是依靠特征检测。这就避免了任何潜在的版本检测错误的问题，并有助于保持你对未来的兼容:

    
    
~~~
try:
    from importlib import abc
except ImportError:
    from importlib2 import abc
~~~

### 防止兼容性退步¶

一旦你完全翻译了你的代码，使之与 Python 3 兼容，你将希望确保你的代码不会退步，不会在 Python 3上停止工作。如果你有一个依赖关系阻碍了你目前在Python 3上的实际运行，那就更是如此了。

为了帮助保持兼容，你创建的任何新模块都应该在其顶部至少有以下代码块:

    
    
~~~
from __future__ import absolute_import
from __future__ import division
from __future__ import print_function
~~~

你也可以在运行Python 2时使用 `-3` 标志，对你的代码在执行过程中引发的各种兼容性问题进行警告。如果你用 `-Werror` 把警告变成错误，那么你可以确保你不会意外地错过一个警告。

你也可以使用 [Pylint](https://pypi.org/project/pylint) 项目和它的 `--py3k` 标志来提示你的代码，当你的代码开始偏离 Python 3 的兼容性时，就会收到警告。这也避免了你不得不定期在你的代码上运行 [Modernize](https://python-modernize.readthedocs.io/) 或 [Futurize](https://python-future.org/automatic_conversion.md) 来捕捉兼容性的退步。这确实要求你只支持Python 2.7和Python 3.4或更新的版本，因为这是Pylint支持的最小Python版本。

### 检查哪些依赖性会阻碍你的过渡¶

在你使你的代码与 Python 3 兼容**之后**，你应该开始关心你的依赖关系是否也被移植了。 [caniusepython3](https://pypi.org/project/caniusepython3) 项目的建立是为了帮助你确定哪些项目——直接或间接地——阻碍了你对Python 3的支持。它既有一个命令行工具，也有一个在 <https://caniusepython3.com> 的网页界面。

该项目还提供了一些代码，你可以将其集成到你的测试套件中，这样，当你不再有依赖关系阻碍你使用Python 3时，你将有一个失败的测试。这使你不必手动检查你的依赖性，并在你可以开始在Python 3上运行时迅速得到通知。

### 更新你的 `setup.py` 文件以表示对Python 3的兼容¶

一旦你的代码在 Python 3 下工作，你应该更新你 `setup.py` 中的分类器，使其包含 `Programming Language :: Python :: 3` 并不指定单独的 Python 2 支持。这将告诉使用你的代码的人，你支持Python 2 **和** 3。理想情况下，你也希望为你现在支持的Python的每个主要/次要版本添加分类器。

### 使用持续集成以保持兼容¶

Once you are able to fully run under Python 3 you will want to make sure your code always works under both Python 2 and 3. Probably the best tool for running your tests under multiple Python interpreters is [tox](https://pypi.org/project/tox). You can then integrate tox with your continuous integration system so that you never accidentally break Python 2 or 3 support.

你可能还想在 Python 3 解释器中使用 `-bb``标志，以便在你将bytes与string或bytes与int进行比较时触发一个异常（后者从 Python 3.5 开始可用）。默认情况下，类型不同的比较只是简单地返回``False`，但是如果你在文本/二进制数据处理或字节的索引分离中犯了一个错误，你就不容易发现这个错误。当这些类型的比较发生时，这个标志会触发一个异常，使错误更容易被发现。

### 考虑使用可选的静态类型检查¶

另一个帮助移植你的代码的方法是在你的代码上使用静态类型检查器，如 [mypy](https://mypy-lang.org/) 或 [pytype](https://github.com/google/pytype)。这些工具可以用来分析你的代码，就像它在Python 2下运行一样，然后你可以第二次运行这个工具，就像你的代码在Python 3下运行一样。通过像这样两次运行静态类型检查器，你可以发现你是否错误地使用了二进制数据类型，例如在Python的一个版本中与另一个版本相比。如果你在你的代码中添加了可选的类型提示，你也可以明确说明你的API是使用文本数据还是二进制数据，这有助于确保在两个版本的Python中所有的功能都符合预期。

