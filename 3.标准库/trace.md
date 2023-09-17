# `trace` —— 跟踪 Python 语句的执行¶

**源代码** ： [Lib/trace.py](https://github.com/python/cpython/tree/3.12/Lib/trace.py)

* * *

模块 `trace` 模块用于跟踪程序的执行过程，可生成带注释的语句覆盖率列表，打印调用/被调用关系，列出程序运行期间执行过的函数。该模块可在其他程序或命令行中使用。

参见

[Coverage.py](https://coverage.readthedocs.io/)

    

~~~
流行的第三方代码覆盖工具，可输出 HTML ，并提供分支覆盖等高级功能。

## 命令行用法¶

`trace` 模块可由命令行调用。用法如此简单：
~~~
    
    
~~~
python -m trace --count -C . somefile.py ...
~~~

上述命令将执行 `somefile.py` ，并在当前目录生成执行期间所有已导入 Python 模块的带注解列表。

\--help¶

    

~~~
显示用法并退出。

\--version¶
~~~
    

~~~
显示模块版本并退出。

在 3.8 版本加入: 加入了 `--module` 选项，允许运行可执行模块。

### 主要的可选参数¶

在调用 `trace` 时，至少须指定以下可选参数之一。 `-listfuncs` 与 `-trace` 、 `-count` 相互排斥。如果给出 `--listfuncs`，就再不会接受 `--count` 和 `--trace` ，反之亦然。

-c, \--count¶
~~~

在程序完成时生成一组带有注解的报表文件，显示每个语句被执行的次数。 参见下面的 `-coverdir` 、`-file` 和 `-no-report`。

-t, \--trace¶
    

执行时显示每一行。

-l, \--listfuncs¶
    

显示程序运行时执行到的函数。

-r, \--report¶
    

由之前用了 `--count` 和 `--file` 运行的程序产生一个带有注解的报表。 不会执行代码。

-T, \--trackcalls¶
    

显示程序运行时暴露出来的调用关系。

### 修饰器¶

-f, \--file=<file>¶
    

用于累计多次跟踪运行计数的文件名。应与 `--count` 一起使用。

-C, \--coverdir=<dir>¶
    

报表文件的所在目录。`package.module` 的覆盖率报表将被写入文件 `_dir_ / _package_ / _module_.cover`。

-m, \--missing¶
    

生成带注解的报表时，用 `>>>>>>` 标记未执行的行。

-s, \--summary¶
    

在用到 `--count` 或 `--report` 时，将每个文件的简短摘要输出到 stdout。

-R, \--no-report¶
    

不生成带注解的报表。如果打算用 `--count` 执行多次运行，然后在最后产生一组带注解的报表，该选项就很有用。

-g, \--timing¶
    

在每一行前面加上时间，自程序运行算起。只在跟踪时有用。

### 过滤器¶

以下参数可重复多次。

\--ignore-module=<mod>¶

    

~~~
忽略给出的模块名及其子模块（若为包）。参数可为逗号分隔的名称列表。

\--ignore-dir=<dir>¶
~~~
    

~~~
忽略指定目录及其子目录下的所有模块和包。参数可为 [`os.pathsep`](os.md#os.pathsep "os.pathsep") 分隔的目录列表。

## 编程接口¶

_class _trace.Trace( _count =1_, _trace =1_, _countfuncs =0_, _countcallers =0_, _ignoremods =()_, _ignoredirs =()_, _infile =None_, _outfile =None_, _timing =False_)¶
~~~
    

~~~
创建一个对象来跟踪单个语句或表达式的执行。所有参数均为选填。 _count_ 可对行号计数。 _trace_ 启用单行执行跟踪。 _countfuncs_ 可列出运行过程中调用的函数。 _countcallers_ 可跟踪调用关系。 _ignoremods_ 是要忽略的模块或包的列表。 _ignoredirs_ 是要忽略的模块或包的目录列表。 _infile_ 是个文件名，从该文件中读取存储的计数信息。 _outfile_ 是用来写入最新计数信息的文件名。 _timing_ 可以显示相对于跟踪开始时间的时间戳。

run( _cmd_ )¶
~~~
    

~~~
执行命令，并根据当前跟踪参数从执行过程中收集统计数据。 _cmd_ 必须为字符串或 code 对象，可供传入 [`exec()`](functions.md#exec "exec")。

runctx( _cmd_ , _globals =None_, _locals =None_)¶
~~~
    

~~~
在定义的全局和局部环境中，执行命令并收集当前跟踪参数下的执行统计数据。若没有定义 _globals_ 和 _locals_ ，则默认为空字典。

runfunc( _func_ , _/_ , _* args_, _** kwds_)¶
~~~
    

~~~
在 `Trace` 对象的控制下，用给定的参数调用 _func_ ，并采用当前的跟踪参数。

results()¶
~~~
    

~~~
返回一个 `CoverageResults` 对象，包含之前对指定 `Trace` 实例调用 `run`、`runctx` 和 `runfunc` 的累积结果。 累积的跟踪结果不会重置。

_class _trace.CoverageResults¶
~~~
    

~~~
存放代码覆盖结果的容器，由 `Trace.results()` 创建。用户不应直接去创建。

update( _other_ )¶
~~~
    

~~~
从另一个 `CoverageResults` 对象中合并代码覆盖数据。

write_results( _show_missing =True_, _summary =False_, _coverdir =None_)¶
~~~
    

~~~
写入代码覆盖结果。设置 _show_missing_ 可显示未命中的行。设置*summary* 可在输出中包含每个模块的覆盖率摘要信息。 _coverdir_ 可指定覆盖率结果文件的输出目录，为 `None` 则结果将置于源文件所在目录中。

以下例子简单演示了编程接口的用法：
~~~
    
    
~~~
import sys
import trace

# create a Trace object, telling it what to ignore, and whether to
# do tracing or line-counting or both.
tracer = trace.Trace(
    ignoredirs=[sys.prefix, sys.exec_prefix],
    trace=0,
    count=1)

# run the new command using the given tracer
tracer.run('main()')

# make a report, placing output in the current directory
r = tracer.results()
r.write_results(show_missing=True, coverdir=".")
~~~

