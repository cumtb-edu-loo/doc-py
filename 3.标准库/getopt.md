# `getopt` \--- C 风格的命令行选项解析器¶

**源代码:** [Lib/getopt.py](https://github.com/python/cpython/tree/3.12/Lib/getopt.py)

自 3.13 版本弃用: The `getopt` module is [soft deprecated](../glossary.md#term-soft-deprecated) and will not be developed further; development will continue with the [`argparse`](3.标准库/argparse.md#module-argparse "argparse: Command-line option and argument parsing library.") module.

备注

The `getopt` module is a parser for command line options whose API is designed to be familiar to users of the C `getopt()` function. Users who are unfamiliar with the C `getopt()` function or who would like to write less code and get better help and error messages should consider using the [`argparse`](3.标准库/argparse.md#module-argparse "argparse: Command-line option and argument parsing library.") module instead.

* * *

This module helps scripts to parse the command line arguments in `sys.argv`. It supports the same conventions as the Unix `getopt()` function (including the special meanings of arguments of the form '`-`' and '`--`'). Long options similar to those supported by GNU software may be used as well via an optional third argument.

此模块提供了两个函数和一个异常:

getopt.getopt( _args_ , _shortopts_ , _longopts =[]_)¶

    

~~~
Parses command line options and parameter list. _args_ is the argument list to be parsed, without the leading reference to the running program. Typically, this means `sys.argv[1:]`. _shortopts_ is the string of option letters that the script wants to recognize, with options that require an argument followed by a colon (`':'`; i.e., the same format that Unix `getopt()` uses).

备注

Unlike GNU `getopt()`, after a non-option argument, all further arguments are considered also non-options. This is similar to the way non-GNU Unix systems work.

如果指定了 _longopts_ ，则必须为一个由应当被支持的长选项名称组成的列表。 开头的 `'--'` 字符不应被包括在选项名称中。 要求参数的长选项后应当带一个等号 (`'='`)。 可选参数不被支持。 如果想仅接受长选项，则 _shortopts_ 应为一个空字符串。 命令行中的长选项只要提供了恰好能匹配可接受选项之一的选项名称前缀即可被识别。 举例来说，如果 _longopts_ 为 `['foo', 'frob']`，则选项 `--fo` 将匹配为 `--foo`，但 `--f` 将不能得到唯一匹配，因此将引发 `GetoptError`。

返回值由两个元素组成：第一个是 `(option, value)` 对的列表；第二个是在去除该选项列表后余下的程序参数列表（这也就是 _args_ 的尾部切片）。每个被返回的选项与值对的第一个元素是选项，短选项前缀一个连字符 (例如 `'-x'`)，长选项则前缀两个连字符 (例如 `'--long-option'`)，第二个元素是选项参数，如果选项不带参数则为空字符串。 列表中选项的排列顺序与它们被解析的顺序相同，因此允许多次出现。 长选项与短选项可以混用。

getopt.gnu_getopt( _args_ , _shortopts_ , _longopts =[]_)¶
~~~
    

~~~
此函数与 `getopt()` 类似，区别在于它默认使用 GNU 风格的扫描模式。 这意味着选项和非选项参数可能会混在一起。 `getopt()` 函数将在遇到非选项参数时立即停止处理选项。

If the first character of the option string is `'+'`, or if the environment variable `POSIXLY_CORRECT` is set, then option processing stops as soon as a non-option argument is encountered.

_exception _getopt.GetoptError¶
~~~
    

~~~
This is raised when an unrecognized option is found in the argument list or when an option requiring an argument is given none. The argument to the exception is a string indicating the cause of the error. For long options, an argument given to an option which does not require one will also cause this exception to be raised. The attributes `msg` and `opt` give the error message and related option; if there is no specific option to which the exception relates, `opt` is an empty string.

_exception _getopt.error¶
~~~
    

~~~
`GetoptError` 的别名；用于向后兼容。

一个仅使用 Unix 风格选项的例子:
~~~
    
    
~~~shell
>>> import getopt
>>> args = '-a -b -cfoo -d bar a1 a2'.split()
>>> args
['-a', '-b', '-cfoo', '-d', 'bar', 'a1', 'a2']
>>> optlist, args = getopt.getopt(args, 'abc:d:')
>>> optlist
[('-a', ''), ('-b', ''), ('-c', 'foo'), ('-d', 'bar')]
>>> args
['a1', 'a2']
~~~

使用长选项名也同样容易:

    
    
~~~shell
>>> s = '--condition=foo --testing --output-file abc.def -x a1 a2'
>>> args = s.split()
>>> args
['--condition=foo', '--testing', '--output-file', 'abc.def', '-x', 'a1', 'a2']
>>> optlist, args = getopt.getopt(args, 'x', [
...     'condition=', 'output-file=', 'testing'])
>>> optlist
[('--condition', 'foo'), ('--testing', ''), ('--output-file', 'abc.def'), ('-x', '')]
>>> args
['a1', 'a2']
~~~

在脚本中，典型的用法类似这样:

    
    
~~~
import getopt, sys

def main():
    try:
        opts, args = getopt.getopt(sys.argv[1:], "ho:v", ["help", "output="])
    except getopt.GetoptError as err:
        # print help information and exit:
        print(err)  # will print something like "option -a not recognized"
        usage()
        sys.exit(2)
    output = None
    verbose = False
    for o, a in opts:
        if o == "-v":
            verbose = True
        elif o in ("-h", "--help"):
            usage()
            sys.exit()
        elif o in ("-o", "--output"):
            output = a
        else:
            assert False, "unhandled option"
    # ...

if __name__ == "__main__":
    main()
~~~

请注意通过 [`argparse`](3.标准库/argparse.md#module-argparse "argparse: Command-line option and argument parsing library.") 模块可以使用更少的代码并附带更详细的帮助与错误消息生成等价的命令行接口:

    
    
~~~
import argparse

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('-o', '--output')
    parser.add_argument('-v', dest='verbose', action='store_true')
    args = parser.parse_args()
    # ... do something with args.output ...
    # ... do something with args.verbose ..
~~~

参见

模块 [`argparse`](3.标准库/argparse.md#module-argparse "argparse: Command-line option and argument parsing library.")

    

~~~
