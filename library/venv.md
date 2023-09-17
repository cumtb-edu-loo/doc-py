# `venv` \--- 创建虚拟环境¶

在 3.3 版本加入.

**源码：** [Lib/venv/](https://github.com/python/cpython/tree/3.12/Lib/venv/)

* * *

`venv` 模块支持创建轻量的“虚拟环境”，每个虚拟环境将拥有它们自己独立的安装在其 [`site`](site.md#module-site "site: Module responsible for site-specific configuration.") 目录中的 Python 软件包集合。 虚拟环境是在现有的 Python 安装版基础之上创建的，这被称为虚拟环境的“基础”Python，并且还可选择与基础环境中的软件包隔离开来，这样只有在虚拟环境中显式安装的软件包才是可用的。

当在一个虚拟环境内使用时，常见安装工具如 [pip](https://pypi.org/project/pip/) 将把 Python 软件包安装到虚拟环境中而无需显式地指明这一点。

请参阅 [**PEP 405**](https://peps.python.org/pep-0405/) 了解有关 Python 虚拟环境的更多背景信息。

参见

[Python 打包用户指南：创建和使用虚拟环境](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/#creating-a-virtual-environment)

[可用性](intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

## 创建虚拟环境¶

通过执行 `venv` 指令来创建一个 虚拟环境:

    
    
~~~
python -m venv /path/to/new/virtual/environment
~~~

运行此命令将创建目标目录（父目录若不存在也将创建），并放置一个 `pyvenv.cfg` 文件在其中，文件中有一个 `home` 键，它的值指向运行此命令的 Python 安装（目标目录的常用名称是 `.venv`）。它还会创建一个 `bin` 子目录（在 Windows 上是 `Scripts`），其中包含 Python 二进制文件的副本或符号链接（视创建环境时使用的平台或参数而定）。它还会创建一个（初始为空的） `lib/pythonX.Y/site-packages` 子目录（在 Windows 上是 `Lib\site-packages`）。如果指定了一个现有的目录，这个目录就将被重新使用。

自 3.6 版本弃用: `pyvenv` 是针对 Python 3.3 和 3.4 创建虚拟环境的推荐工具，并 [在 Python 3.6 中被弃用](../whatsnew/3.6.md#whatsnew36-venv)。

在 3.5 版本发生变更: 现在推荐使用 `venv` 来创建虚拟环境。

在 Windows 上，调用 `venv` 命令如下:

    
    
~~~
c:\>Python35\python -m venv c:\path\to\myenv
~~~

或者，如果已经为 [Python 安装](../using/windows.md#using-on-windows) 配置好 `PATH` 和 `PATHEXT` 变量:

    
    
~~~
c:\>python -m venv c:\path\to\myenv
~~~

本命令如果以 `-h` 参数运行，将显示可用的选项:

    
    
~~~
usage: venv [-h] [--system-site-packages] [--symlinks | --copies] [--clear]
            [--upgrade] [--without-pip] [--prompt PROMPT] [--upgrade-deps]
            [--without-scm-ignore-file]
            ENV_DIR [ENV_DIR ...]

Creates virtual Python environments in one or more target directories.

positional arguments:
ENV_DIR               A directory to create the environment in.

options:
-h, --help            show this help message and exit
--system-site-packages
                        Give the virtual environment access to the system
                        site-packages dir.
--symlinks            Try to use symlinks rather than copies, when
                        symlinks are not the default for the platform.
--copies              Try to use copies rather than symlinks, even when
                        symlinks are the default for the platform.
--clear               Delete the contents of the environment directory if
                        it already exists, before environment creation.
--upgrade             Upgrade the environment directory to use this
                        version of Python, assuming Python has been upgraded
                        in-place.
--without-pip         Skips installing or upgrading pip in the virtual
                        environment (pip is bootstrapped by default)
--prompt PROMPT       Provides an alternative prompt prefix for this
                        environment.
--upgrade-deps        Upgrade core dependencies (pip) to the latest
                        version in PyPI
--without-scm-ignore-file
                        Skips adding the default SCM ignore file to the
                        environment directory (the default is a .gitignore
                        file).

Once an environment has been created, you may wish to activate it, e.g. by
sourcing an activate script in its bin directory.
~~~

在 3.13 版本发生变更: `--without-scm-ignore-file` was added along with creating an ignore file for `git` by default.

在 3.12 版本发生变更: `setuptools` 不再是核心的 venv 依赖项。

在 3.9 版本发生变更: 添加 `--upgrade-deps` 选项，用于将 pip + setuptools 升级到 PyPI 上的最新版本

在 3.4 版本发生变更: 默认安装 pip，并添加 `--without-pip` 和 `--copies` 选项

在 3.4 版本发生变更: 在早期版本中，如果目标目录已存在，将引发错误，除非使用了 `--clear` 或 `--upgrade` 选项。

备注

虽然 Windows 支持符号链接，但不推荐使用它们。特别注意，在文件资源管理器中双击 `python.exe` 将立即解析符号链接，并忽略虚拟环境。

备注

在 Microsoft Windows 上，为了启用 `Activate.ps1` 脚本，可能需要修改用户的执行策略。可以运行以下 PowerShell 命令来执行此操作：

PS C:> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

参阅 [About Execution Policies](https://go.microsoft.com/fwlink/?LinkID=135170) 以获取更多信息。

生成的 `pyvenv.cfg` 文件还包括 `include-system-site-packages` 键，如果运行 `venv` 时带有 `--system-site-packages` 选项，则键值为 `true`，否则为 `false`。

除非采用 `--without-pip` 选项，否则将会调用 [`ensurepip`](ensurepip.md#module-ensurepip "ensurepip: Bootstrapping the "pip" installer into an existing Python installation or virtual environment.") 将 `pip` 引导到虚拟环境中。

可以向 `venv` 传入多个路径，此时将根据给定的选项，在所给的每个路径上创建相同的虚拟环境。

## 虚拟环境是如何实现的¶

当运行虚拟环境中的 Python 解释器时，[`sys.prefix`](sys.md#sys.prefix "sys.prefix") 和 [`sys.exec_prefix`](sys.md#sys.exec_prefix "sys.exec_prefix") 将指向该虚拟环境的相应目录，而 [`sys.base_prefix`](sys.md#sys.base_prefix "sys.base_prefix") 和 [`sys.base_exec_prefix`](sys.md#sys.base_exec_prefix "sys.base_exec_prefix") 将指向用于创建该虚拟环境的基础 Python 的相应目录。 只需检测 `sys.prefix != sys.base_prefix` 就足以确定当前解释器是否运行于虚拟环境中。

一个虚拟环境可以通过位于其二进制文件目录目录 (在 POSIX 上为 `bin`；在 Windows 上为 `Scripts` ) 中的脚本来“激活”。 这会将该目录添加到你的 `PATH`，这样运行 **python** 时就会发起调用虚拟环境的 Python 解释器，从而可以运行该目录中安装的脚本而不必使用其完整路径。 激活脚本的发起调用方式是平台专属的 (` _< venv>_` 必须用包含虚拟环境目录的路径来替换):

平台

|

Shell

|

用于激活虚拟环境的命令  
  
---|---|---  
  
POSIX

|

bash/zsh

|

`$ source _< venv>_/bin/activate`  
  
fish

|

`$ source _< venv>_/bin/activate.fish`  
  
csh/tcsh

|

`$ source _< venv>_/bin/activate.csh`  
  
PowerShell

|

`$ _< venv>_/bin/Activate.ps1`  
  
Windows

|

cmd.exe

|

`C:\> _< venv>_\Scripts\activate.bat`  
  
PowerShell

|

`PS C:\> _< venv>_\Scripts\Activate.ps1`  
  
在 3.4 版本加入: **fish** 和 **csh** 激活脚本。

在 3.8 版本加入: 在 POSIX 上安装 PowerShell 激活脚本，以支持 PowerShell Core。

激活一个虚拟环境的操作 _不是必需的_ ，因为你完全可以在发起调用 Python 时指明特定虚拟环境的 Python 解释器的完整路径。 更进一步地说，安装在虚拟环境中的所有脚本也都可以在不激活该虚拟环境的情况下运行。

为了达成此目的，安装到虚拟环境中的脚本将包含一个以“井号叹号”打头的行用来指定虚拟环境的 Python 解释器，例如 `#!/ _< path-to-venv>_/bin/python`。 这意味着无论 `PATH` 的值是什么该脚本都将使用指定的解释器运行。 在 Windows 上对“井号叹号”行的处理将在你安装了 [适用于Windows的Python启动器](../using/windows.md#launcher) 的情况下获得支持。 这样，在 Windows 资源管理器窗口中双击一个已安装的脚本应当会使用正确的解释器运行它而无需激活相应虚拟环境或设置 `PATH`。

当一个虚拟环境已被激活时，`VIRTUAL_ENV` 环境变量会被设为该虚拟环境的路径。 由于使用虚拟环境并不需要显式地激活它，因此 `VIRTUAL_ENV` 并不能被用来可靠地确定是否正在使用虚拟环境。

警告

因为安装在虚拟环境中的脚本不应要求必须激活该虚拟环境，所以它们的“井号叹号”行会包含虚拟环境的绝对路径。 因为这一点，所以虚拟环境在通常情况下都是不可移植的。 你应当保证提供重建一个虚拟环境的简便方式（举例来说，如果你准备了需求文件 `requirements.txt`，则可以使用虚拟环境的 `pip` 执行 `pip install -r requirements.txt` 来安装虚拟环境所需的所有软件包）。 如果出于某种原因你需要将虚拟环境移动到一个新的位置，则你应当在目标位置上重建它并删除旧位置上的虚拟环境。 如果出于某种原因你移动了一个虚拟环境的上级目录，你也应当在新位置上重建该虚拟环境。 否则，安装到该虚拟环境的软件包可能无法正常工作。

你可以通过在 shell 中输入 `deactivate` 来取消激活一个虚拟环境。 取消激活的实现机制依赖于具体平台并且属于内部实现细节（通常，将会使用一个脚本或者 shell 函数）。

## API¶

上述的高级方法使用了一个简单的 API，该 API 提供了一种机制，第三方虚拟环境创建者可以根据其需求自定义环境创建过程，该 API 为 `EnvBuilder` 类。

_class _venv.EnvBuilder( _system_site_packages =False_, _clear =False_, _symlinks =False_, _upgrade =False_, _with_pip =False_, _prompt =None_, _upgrade_deps =False_, _*_ , _scm_ignore_files =frozenset()_)¶

    

~~~
`EnvBuilder` 类在实例化时接受以下关键字参数：

  * `system_site_packages` \-- 一个布尔值，要求系统 Python 的 site-packages 对环境可用（默认为 `False`）。

  * `clear` \-- 一个布尔值，如果为 true，则在创建环境前将删除目标目录的现有内容。

  * `symlinks` \-- 一个布尔值，指示是否尝试符号链接 Python 二进制文件，而不是进行复制。

  * `upgrade` \-- 一个布尔值，如果为 true，则将使用当前运行的 Python 去升级一个现有的环境，这主要在原位置的 Python 更新后使用（默认为 `False`）。

  * `with_pip` \-- 一个布尔值，如果为 true，则确保在虚拟环境中已安装 pip。这使用的是带有 `--default-pip` 选项的 [`ensurepip`](ensurepip.md#module-ensurepip "ensurepip: Bootstrapping the "pip" installer into an existing Python installation or virtual environment.")。

  * `prompt` \-- 激活虚拟环境后显示的提示符（默认为 `None`，表示使用环境所在的目录名称）。如果使用了 `"."` 这一特殊字符串，则使用当前目录的基本名称作为提示符。

  * `upgrade_deps` \-- 将基本 venv 模块更新为 PyPI 上的最新版本。

  * `scm_ignore_files` \-- Create ignore files based for the specified source control managers (SCM) in the iterable. Support is defined by having a method named `create_{scm}_ignore_file`. The only value supported by default is `"git"` via `create_git_ignore_file()`.

在 3.4 版本发生变更: 添加 `with_pip` 参数

在 3.6 版本加入: 添加 `prompt` 参数

在 3.9 版本加入: 添加 `upgrade_deps` 参数

在 3.13 版本加入: Added the `scm_ignore_files` parameter

第三方虚拟环境工具的创建者可以自由地将此处提供的 `EnvBuilder` 类作为基类。

返回的 env-builder 是一个对象，包含一个 `create` 方法：

create( _env_dir_ )¶
~~~
    

~~~
指定要建立虚拟环境的目标目录（绝对路径或相对于当前路径）来创建虚拟环境。`create` 方法将在指定目录中创建环境，或者引发对应的异常。

`EnvBuilder` 类的 `create` 方法定义了可用于定制子类的钩子:
~~~
    
    
~~~python
def create(self, env_dir):
    """
    Create a virtualized Python environment in a directory.
    env_dir is the target directory to create an environment in.
    """
    env_dir = os.path.abspath(env_dir)
    context = self.ensure_directories(env_dir)
    self.create_configuration(context)
    self.setup_python(context)
    self.setup_scripts(context)
    self.post_setup(context)
~~~

每个方法 `ensure_directories()`, `create_configuration()`, `setup_python()`, `setup_scripts()` 和 `post_setup()` 都可以被重写。

ensure_directories( _env_dir_ )¶

    

~~~
创建虚拟环境目录及尚不存在的所有必要的子目录，并返回一个上下文对象。 这个上下文对象被用于存放供其他方法使用的属性（如路径等）。 如果 `EnvBuilder` 是附带参数 `clear=True` 创建的，则虚拟环境的内容将被清除并将重新创建所有必要的子目录。

返回的上下文对象是一个具有以下属性的 [`types.SimpleNamespace`](types.md#types.SimpleNamespace "types.SimpleNamespace"):

  * `env_dir` \- 虚拟环境的位置。 将被用作激活脚本中的 `__VENV_DIR__` (参见 `install_scripts()`)。

  * `env_name` \- 虚拟环境的名称。 将被用作激活脚本中的 `__VENV_NAME__` (参见 `install_scripts()`)。

  * `prompt` \- 激活脚本要使用的提示符。 将被用作激活脚本中的 `__VENV_PROMPT__` (参见 `install_scripts()`)。

  * `executable` \- 虚拟环境所使用的下层 Python 可执行文件。 这会将基于另一个虚拟环境创建虚拟环境的情况也纳入考虑。

  * `inc_path` \- 虚拟环境的 include 路径。

  * `lib_path` \- 虚拟环境的 purelib 路径。

  * `bin_path` \- 虚拟环境的 script 路径。

  * `bin_name` \- 相对于虚拟环境位置的 script 路径名称。 用于激活脚本中的 `__VENV_BIN_NAME__` (参见 `install_scripts()`)。

  * `env_exe` \- 虚拟环境中 Python 解释器的名称。 用于激活脚本中的 `__VENV_PYTHON__` (参见 `install_scripts()`)。

  * `env_exec_cmd` \- Python 解释器的名称，会将文件系统重定向也纳入考虑。 这可被用于在虚拟环境中运行 Python。

在 3.12 版本发生变更: 将属性 `lib_path` 添加到上下文中，并将上下文对象写入文档。

在 3.11 版本发生变更: _venv_ [sysconfig installation scheme](sysconfig.md#installation-paths) 被用于构造所创建目录的路径。

create_configuration( _context_ )¶
~~~
    

~~~
在环境中创建 `pyvenv.cfg` 配置文件。

setup_python( _context_ )¶
~~~
    

~~~
在环境中创建 Python 可执行文件的拷贝或符号链接。在 POSIX 系统上，如果给定了可执行文件 `python3.x`，将创建指向该可执行文件的 `python` 和 `python3` 符号链接，除非相同名称的文件已经存在。

setup_scripts( _context_ )¶
~~~
    

~~~
将适用于平台的激活脚本安装到虚拟环境中。

upgrade_dependencies( _context_ )¶
~~~
    

~~~
升级环境中核心的 venv 依赖包 (目前为 `pip`)。 这是通过将 shell 转出给环境中的 `pip` 可执行文件来实现的。

在 3.9 版本加入.

在 3.12 版本发生变更: `setuptools` 不再是核心的 venv 依赖项。

post_setup( _context_ )¶
~~~
    

~~~
占位方法，可以在第三方实现中重写，用于在虚拟环境中预安装软件包，或是其他创建后要执行的步骤。

在 3.7.2 版本发生变更: Windows 现在为 `python[w].exe` 使用重定向脚本，而不是复制实际的二进制文件。仅在 3.7.2 中，除非运行的是源码树中的构建，否则 `setup_python()` 不会执行任何操作。

在 3.7.3 版本发生变更: Windows 将重定向脚本复制为 `setup_python()` 的一部分而非 `setup_scripts()`。在 3.7.2 中不是这种情况。使用符号链接时，将链接至原始可执行文件。

此外，`EnvBuilder` 提供了如下实用方法，可以从子类的 `setup_scripts()` 或 `post_setup()` 调用，用来将自定义脚本安装到虚拟环境中。

install_scripts( _context_ , _path_ )¶
~~~
    

~~~
_path_ 是一个目录的路径，该目录应包含子目录 "common", "posix", "nt"，每个子目录存有发往对应环境中 bin 目录的脚本。在下列占位符替换完毕后，将复制 "common" 的内容和与 [`os.name`](os.md#os.name "os.name") 对应的子目录：

  * `__VENV_DIR__` 会被替换为环境目录的绝对路径。

  * `__VENV_NAME__` 会被替换为环境名称（环境目录的最后一个字段）。

  * `__VENV_PROMPT__` 会被替换为提示符（用括号括起来的环境名称紧跟着一个空格）。

  * `__VENV_BIN_NAME__` 会被替换为 bin 目录的名称（ `bin` 或 `Scripts` ）。

  * `__VENV_PYTHON__` 会被替换为环境可执行文件的绝对路径。

允许目录已存在（用于升级现有环境时）。

create_git_ignore_file( _context_ )¶
~~~
    

~~~
Creates a `.gitignore` file within the virtual environment that causes the entire directory to be ignored by the `git` source control manager.

在 3.13 版本加入.

有一个方便实用的模块级别的函数:

venv.create( _env_dir_ , _system_site_packages =False_, _clear =False_, _symlinks =False_, _with_pip =False_, _prompt =None_, _upgrade_deps =False_, _*_ , _scm_ignore_files =frozenset()_)¶
~~~
    

~~~
通过关键词参数来创建一个 `EnvBuilder`，并且使用 _env_dir_ 参数来调用它的 `create()` 方法。

在 3.3 版本加入.

在 3.4 版本发生变更: 添加 `with_pip` 参数

在 3.6 版本发生变更: 添加 `prompt` 参数

在 3.9 版本发生变更: 添加 `upgrade_deps` 参数

在 3.13 版本发生变更: Added the `scm_ignore_files` parameter

## 一个扩展 `EnvBuilder` 的例子¶

下面的脚本展示了如何通过实现一个子类来扩展 `EnvBuilder`。这个子类会安装 setuptools 和 pip 到被创建的虚拟环境中。
~~~
    
    
~~~
import os
import os.path
from subprocess import Popen, PIPE
import sys
from threading import Thread
from urllib.parse import urlparse
from urllib.request import urlretrieve
import venv

class ExtendedEnvBuilder(venv.EnvBuilder):
    """
    This builder installs setuptools and pip so that you can pip or
    easy_install other packages into the created virtual environment.

    :param nodist: If true, setuptools and pip are not installed into the
                   created virtual environment.
    :param nopip: If true, pip is not installed into the created
                  virtual environment.
    :param progress: If setuptools or pip are installed, the progress of the
                     installation can be monitored by passing a progress
                     callable. If specified, it is called with two
                     arguments: a string indicating some progress, and a
                     context indicating where the string is coming from.
                     The context argument can have one of three values:
                     'main', indicating that it is called from virtualize()
                     itself, and 'stdout' and 'stderr', which are obtained
                     by reading lines from the output streams of a subprocess
                     which is used to install the app.

                     If a callable is not specified, default progress
                     information is output to sys.stderr.
    """

    def __init__(self, *args, **kwargs):
        self.nodist = kwargs.pop('nodist', False)
        self.nopip = kwargs.pop('nopip', False)
        self.progress = kwargs.pop('progress', None)
        self.verbose = kwargs.pop('verbose', False)
        super().__init__(*args, **kwargs)

    def post_setup(self, context):
        """
        Set up any packages which need to be pre-installed into the
        virtual environment being created.

        :param context: The information for the virtual environment
                        creation request being processed.
        """
        os.environ['VIRTUAL_ENV'] = context.env_dir
        if not self.nodist:
            self.install_setuptools(context)
        # Can't install pip without setuptools
        if not self.nopip and not self.nodist:
            self.install_pip(context)

    def reader(self, stream, context):
        """
        Read lines from a subprocess' output stream and either pass to a progress
        callable (if specified) or write progress information to sys.stderr.
        """
        progress = self.progress
        while True:
            s = stream.readline()
            if not s:
                break
            if progress is not None:
                progress(s, context)
            else:
                if not self.verbose:
                    sys.stderr.write('.')
                else:
                    sys.stderr.write(s.decode('utf-8'))
                sys.stderr.flush()
        stream.close()

    def install_script(self, context, name, url):
        _, _, path, _, _, _ = urlparse(url)
        fn = os.path.split(path)[-1]
        binpath = context.bin_path
        distpath = os.path.join(binpath, fn)
        # Download script into the virtual environment's binaries folder
        urlretrieve(url, distpath)
        progress = self.progress
        if self.verbose:
            term = '\n'
        else:
            term = ''
        if progress is not None:
            progress('Installing %s ...%s' % (name, term), 'main')
        else:
            sys.stderr.write('Installing %s ...%s' % (name, term))
            sys.stderr.flush()
        # Install in the virtual environment
        args = [context.env_exe, fn]
        p = Popen(args, stdout=PIPE, stderr=PIPE, cwd=binpath)
        t1 = Thread(target=self.reader, args=(p.stdout, 'stdout'))
        t1.start()
        t2 = Thread(target=self.reader, args=(p.stderr, 'stderr'))
        t2.start()
        p.wait()
        t1.join()
        t2.join()
        if progress is not None:
            progress('done.', 'main')
        else:
            sys.stderr.write('done.\n')
        # Clean up - no longer needed
        os.unlink(distpath)

    def install_setuptools(self, context):
        """
        Install setuptools in the virtual environment.

        :param context: The information for the virtual environment
                        creation request being processed.
        """
        url = "https://bootstrap.pypa.io/ez_setup.py"
        self.install_script(context, 'setuptools', url)
        # clear up the setuptools archive which gets downloaded
        pred = lambda o: o.startswith('setuptools-') and o.endswith('.tar.gz')
        files = filter(pred, os.listdir(context.bin_path))
        for f in files:
            f = os.path.join(context.bin_path, f)
            os.unlink(f)

    def install_pip(self, context):
        """
        Install pip in the virtual environment.

        :param context: The information for the virtual environment
                        creation request being processed.
        """
        url = 'https://bootstrap.pypa.io/get-pip.py'
        self.install_script(context, 'pip', url)
~~~
    
    def main(args=None):
        import argparse
    
        parser = argparse.ArgumentParser(prog=__name__,
                                         description='Creates virtual Python '
                                                     'environments in one or '
                                                     'more target '
                                                     'directories.')
        parser.add_argument('dirs', metavar='ENV_DIR', nargs='+',
                            help='A directory in which to create the '
                                 'virtual environment.')
        parser.add_argument('--no-setuptools', default=False,
                            action='store_true', dest='nodist',
                            help="Don't install setuptools or pip in the "
                                 "virtual environment.")
        parser.add_argument('--no-pip', default=False,
                            action='store_true', dest='nopip',
                            help="Don't install pip in the virtual "
                                 "environment.")
        parser.add_argument('--system-site-packages', default=False,
                            action='store_true', dest='system_site',
                            help='Give the virtual environment access to the '
                                 'system site-packages dir.')
        if os.name == 'nt':
            use_symlinks = False
        else:
            use_symlinks = True
        parser.add_argument('--symlinks', default=use_symlinks,
                            action='store_true', dest='symlinks',
                            help='Try to use symlinks rather than copies, '
                                 'when symlinks are not the default for '
                                 'the platform.')
        parser.add_argument('--clear', default=False, action='store_true',
                            dest='clear', help='Delete the contents of the '
                                               'virtual environment '
                                               'directory if it already '
                                               'exists, before virtual '
                                               'environment creation.')
        parser.add_argument('--upgrade', default=False, action='store_true',
                            dest='upgrade', help='Upgrade the virtual '
                                                 'environment directory to '
                                                 'use this version of '
                                                 'Python, assuming Python '
                                                 'has been upgraded '
                                                 'in-place.')
        parser.add_argument('--verbose', default=False, action='store_true',
                            dest='verbose', help='Display the output '
                                                 'from the scripts which '
                                                 'install setuptools and pip.')
        options = parser.parse_args(args)
        if options.upgrade and options.clear:
            raise ValueError('you cannot supply --upgrade and --clear together.')
        builder = ExtendedEnvBuilder(system_site_packages=options.system_site,
                                       clear=options.clear,
                                       symlinks=options.symlinks,
                                       upgrade=options.upgrade,
                                       nodist=options.nodist,
                                       nopip=options.nopip,
                                       verbose=options.verbose)
        for d in options.dirs:
            builder.create(d)
    
    if __name__ == '__main__':
        rc = 1
        try:
            main()
            rc = 0
        except Exception as e:
            print('Error: %s' % e, file=sys.stderr)
        sys.exit(rc)
    

这个脚本同样可以 [在线下载](https://gist.github.com/vsajip/4673395)。

