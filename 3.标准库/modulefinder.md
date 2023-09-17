# `modulefinder` \--- 查找脚本使用的模块¶

**源码：** [Lib/modulefinder.py](https://github.com/python/cpython/tree/3.12/Lib/modulefinder.py)

* * *

该模块提供了一个 `ModuleFinder` 类，可用于确定脚本导入的模块集。 `modulefinder.py` 也可以作为脚本运行，给出 Python 脚本的文件名作为参数，之后将打印导入模块的报告。

modulefinder.AddPackagePath( _pkg_name_ , _path_ )¶

    

~~~
记录名为 _pkg_name_ 的包可以在指定的 _path_ 中找到。

modulefinder.ReplacePackage( _oldname_ , _newname_ )¶
~~~
    

~~~
允许指定名为 _oldname_ 的模块实际上是名为 _newname_ 的包。

_class _modulefinder.ModuleFinder( _path =None_, _debug =0_, _excludes =[]_, _replace_paths =[]_)¶
~~~
    

~~~
该类提供 `run_script()` 和 `report()` 方法，用于确定脚本导入的模块集。 _path_ 可以是搜索模块的目录列表；如果没有指定，则使用 `sys.path` 。 _debug_ 设置调试级别；更高的值使类打印调试消息，关于它正在做什么。 _excludes_ 是要从分析中排除的模块名称列表。 _replace_paths_ 是将在模块路径中替换的 `(oldpath, newpath)` 元组的列表。

report()¶
~~~
    

~~~
将报告打印到标准输出，列出脚本导入的模块及其路径，以及缺少或似乎缺失的模块。

run_script( _pathname_ )¶
~~~
    

~~~
分析 _pathname_ 文件的内容，该文件必须包含 Python 代码。

modules¶
~~~
    

~~~
一个将模块名称映射到模块的字典。 请参阅 ModuleFinder 的示例用法。

## `ModuleFinder` 的示例用法¶

稍后将分析的脚本（bacon.py）:
~~~
    
    
~~~
import re, itertools

try:
    import baconhameggs
except ImportError:
    pass

try:
    import guido.python.ham
except ImportError:
    pass
~~~

将输出 bacon.py 报告的脚本:

    
    
~~~
from modulefinder import ModuleFinder

finder = ModuleFinder()
finder.run_script('bacon.py')

print('Loaded modules:')
for name, mod in finder.modules.items():
    print('%s: ' % name, end='')
    print(','.join(list(mod.globalnames.keys())[:3]))

print('-'*50)
print('Modules not imported:')
print('\n'.join(finder.badmodules.keys()))
~~~

输出样例（可能因架构而异）:

    
    
~~~
Loaded modules:
_types:
copyreg:  _inverted_registry,_slotnames,__all__
re._compiler:  isstring,_sre,_optimize_unicode
_sre:
re._constants:  REPEAT_ONE,makedict,AT_END_LINE
sys:
re:  __module__,finditer,_expand
itertools:
__main__:  re,itertools,baconhameggs
re._parser:  _PATTERNENDERS,SRE_FLAG_UNICODE
array:
types:  __module__,IntType,TypeType
---------------------------------------------------
Modules not imported:
guido.python.ham
baconhameggs
~~~

