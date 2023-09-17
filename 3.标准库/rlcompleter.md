# `rlcompleter` \--- GNU readline 的补全函数¶

**源代码:** [Lib/rlcompleter.py](https://github.com/python/cpython/tree/3.12/Lib/rlcompleter.py)

* * *

`rlcompeleter` 通过补全有效的 Python 标识符和关键字定义了一个适用于 [`readline`](readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 模块的补全函数。

当此模块在具有可用的 [`readline`](readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 模块的 Unix 平台被导入, 一个 `Completer` 实例将被自动创建并且它的 `complete()` 方法将设置为 [`readline`](readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 的补全器.

示例:

    
    
~~~shell
>>> import rlcompleter
>>> import readline
>>> readline.parse_and_bind("tab: complete")
>>> readline. <TAB PRESSED>
readline.__doc__          readline.get_line_buffer(  readline.read_init_file(
readline.__file__         readline.insert_text(      readline.set_completer(
readline.__name__         readline.parse_and_bind(
>>> readline.
~~~

`rlcompleter` 模块是为了使用 Python 的 [交互模式](2.%20Python%20解释器.md#tut-interactive) 而设计的。 除非 Python 是通过 [`-S`](1.%20命令行与环境.md#cmdoption-S) 选项运行, 这个模块总是自动地被导入且配置 (参见 [Readline 配置](site.md#rlcompleter-config))。

在没有 [`readline`](readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 的平台, 此模块定义的 `Completer` 类仍然可以用于自定义行为.

## Completer 对象¶

Completer 对象具有以下方法：

Completer.complete( _text_ , _state_ )¶

    

~~~
