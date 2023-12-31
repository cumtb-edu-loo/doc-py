# Python运行时服务¶

本章里描述的模块提供了和Python解释器及其环境交互相关的广泛服务。以下是综述：

  * [`sys` \--- 系统相关的形参和函数](3.标准库/sys.md)
    * [`abiflags`](3.标准库/sys.md#sys.abiflags)
    * [`addaudithook()`](3.标准库/sys.md#sys.addaudithook)
    * [`argv`](3.标准库/sys.md#sys.argv)
    * [`audit()`](3.标准库/sys.md#sys.audit)
    * [`base_exec_prefix`](3.标准库/sys.md#sys.base_exec_prefix)
    * [`base_prefix`](3.标准库/sys.md#sys.base_prefix)
    * [`byteorder`](3.标准库/sys.md#sys.byteorder)
    * [`builtin_module_names`](3.标准库/sys.md#sys.builtin_module_names)
    * [`call_tracing()`](3.标准库/sys.md#sys.call_tracing)
    * [`copyright`](3.标准库/sys.md#sys.copyright)
    * [`_clear_type_cache()`](3.标准库/sys.md#sys._clear_type_cache)
    * [`_current_frames()`](3.标准库/sys.md#sys._current_frames)
    * [`_current_exceptions()`](3.标准库/sys.md#sys._current_exceptions)
    * [`breakpointhook()`](3.标准库/sys.md#sys.breakpointhook)
    * [`_debugmallocstats()`](3.标准库/sys.md#sys._debugmallocstats)
    * [`dllhandle`](3.标准库/sys.md#sys.dllhandle)
    * [`displayhook()`](3.标准库/sys.md#sys.displayhook)
    * [`dont_write_bytecode`](3.标准库/sys.md#sys.dont_write_bytecode)
    * [`_emscripten_info`](3.标准库/sys.md#sys._emscripten_info)
      * [`_emscripten_info.emscripten_version`](3.标准库/sys.md#sys._emscripten_info.emscripten_version)
      * [`_emscripten_info.runtime`](3.标准库/sys.md#sys._emscripten_info.runtime)
      * [`_emscripten_info.pthreads`](3.标准库/sys.md#sys._emscripten_info.pthreads)
      * [`_emscripten_info.shared_memory`](3.标准库/sys.md#sys._emscripten_info.shared_memory)
    * [`pycache_prefix`](3.标准库/sys.md#sys.pycache_prefix)
    * [`excepthook()`](3.标准库/sys.md#sys.excepthook)
    * [`__breakpointhook__`](3.标准库/sys.md#sys.__breakpointhook__)
    * [`__displayhook__`](3.标准库/sys.md#sys.__displayhook__)
    * [`__excepthook__`](3.标准库/sys.md#sys.__excepthook__)
    * [`__unraisablehook__`](3.标准库/sys.md#sys.__unraisablehook__)
    * [`exception()`](3.标准库/sys.md#sys.exception)
    * [`exc_info()`](3.标准库/sys.md#sys.exc_info)
    * [`exec_prefix`](3.标准库/sys.md#sys.exec_prefix)
    * [`executable`](3.标准库/sys.md#sys.executable)
    * [`exit()`](3.标准库/sys.md#sys.exit)
    * [`flags`](3.标准库/sys.md#sys.flags)
      * [`flags.debug`](3.标准库/sys.md#sys.flags.debug)
      * [`flags.inspect`](3.标准库/sys.md#sys.flags.inspect)
      * [`flags.interactive`](3.标准库/sys.md#sys.flags.interactive)
      * [`flags.isolated`](3.标准库/sys.md#sys.flags.isolated)
      * [`flags.optimize`](3.标准库/sys.md#sys.flags.optimize)
      * [`flags.dont_write_bytecode`](3.标准库/sys.md#sys.flags.dont_write_bytecode)
      * [`flags.no_user_site`](3.标准库/sys.md#sys.flags.no_user_site)
      * [`flags.no_site`](3.标准库/sys.md#sys.flags.no_site)
      * [`flags.ignore_environment`](3.标准库/sys.md#sys.flags.ignore_environment)
      * [`flags.verbose`](3.标准库/sys.md#sys.flags.verbose)
      * [`flags.bytes_warning`](3.标准库/sys.md#sys.flags.bytes_warning)
      * [`flags.quiet`](3.标准库/sys.md#sys.flags.quiet)
      * [`flags.hash_randomization`](3.标准库/sys.md#sys.flags.hash_randomization)
      * [`flags.dev_mode`](3.标准库/sys.md#sys.flags.dev_mode)
      * [`flags.utf8_mode`](3.标准库/sys.md#sys.flags.utf8_mode)
      * [`flags.safe_path`](3.标准库/sys.md#sys.flags.safe_path)
      * [`flags.int_max_str_digits`](3.标准库/sys.md#sys.flags.int_max_str_digits)
      * [`flags.warn_default_encoding`](3.标准库/sys.md#sys.flags.warn_default_encoding)
    * [`float_info`](3.标准库/sys.md#sys.float_info)
      * [`float_info.epsilon`](3.标准库/sys.md#sys.float_info.epsilon)
      * [`float_info.dig`](3.标准库/sys.md#sys.float_info.dig)
      * [`float_info.mant_dig`](3.标准库/sys.md#sys.float_info.mant_dig)
      * [`float_info.max`](3.标准库/sys.md#sys.float_info.max)
      * [`float_info.max_exp`](3.标准库/sys.md#sys.float_info.max_exp)
      * [`float_info.max_10_exp`](3.标准库/sys.md#sys.float_info.max_10_exp)
      * [`float_info.min`](3.标准库/sys.md#sys.float_info.min)
      * [`float_info.min_exp`](3.标准库/sys.md#sys.float_info.min_exp)
      * [`float_info.min_10_exp`](3.标准库/sys.md#sys.float_info.min_10_exp)
      * [`float_info.radix`](3.标准库/sys.md#sys.float_info.radix)
      * [`float_info.rounds`](3.标准库/sys.md#sys.float_info.rounds)
    * [`float_repr_style`](3.标准库/sys.md#sys.float_repr_style)
    * [`getallocatedblocks()`](3.标准库/sys.md#sys.getallocatedblocks)
    * [`getunicodeinternedsize()`](3.标准库/sys.md#sys.getunicodeinternedsize)
    * [`getandroidapilevel()`](3.标准库/sys.md#sys.getandroidapilevel)
    * [`getdefaultencoding()`](3.标准库/sys.md#sys.getdefaultencoding)
    * [`getdlopenflags()`](3.标准库/sys.md#sys.getdlopenflags)
    * [`getfilesystemencoding()`](3.标准库/sys.md#sys.getfilesystemencoding)
    * [`getfilesystemencodeerrors()`](3.标准库/sys.md#sys.getfilesystemencodeerrors)
    * [`get_int_max_str_digits()`](3.标准库/sys.md#sys.get_int_max_str_digits)
    * [`getrefcount()`](3.标准库/sys.md#sys.getrefcount)
    * [`getrecursionlimit()`](3.标准库/sys.md#sys.getrecursionlimit)
    * [`getsizeof()`](3.标准库/sys.md#sys.getsizeof)
    * [`getswitchinterval()`](3.标准库/sys.md#sys.getswitchinterval)
    * [`_getframe()`](3.标准库/sys.md#sys._getframe)
    * [`_getframemodulename()`](3.标准库/sys.md#sys._getframemodulename)
    * [`getprofile()`](3.标准库/sys.md#sys.getprofile)
    * [`gettrace()`](3.标准库/sys.md#sys.gettrace)
    * [`getwindowsversion()`](3.标准库/sys.md#sys.getwindowsversion)
    * [`get_asyncgen_hooks()`](3.标准库/sys.md#sys.get_asyncgen_hooks)
    * [`get_coroutine_origin_tracking_depth()`](3.标准库/sys.md#sys.get_coroutine_origin_tracking_depth)
    * [`hash_info`](3.标准库/sys.md#sys.hash_info)
      * [`hash_info.width`](3.标准库/sys.md#sys.hash_info.width)
      * [`hash_info.modulus`](3.标准库/sys.md#sys.hash_info.modulus)
      * [`hash_info.inf`](3.标准库/sys.md#sys.hash_info.inf)
      * [`hash_info.nan`](3.标准库/sys.md#sys.hash_info.nan)
      * [`hash_info.imag`](3.标准库/sys.md#sys.hash_info.imag)
      * [`hash_info.algorithm`](3.标准库/sys.md#sys.hash_info.algorithm)
      * [`hash_info.hash_bits`](3.标准库/sys.md#sys.hash_info.hash_bits)
      * [`hash_info.seed_bits`](3.标准库/sys.md#sys.hash_info.seed_bits)
    * [`hexversion`](3.标准库/sys.md#sys.hexversion)
    * [`implementation`](3.标准库/sys.md#sys.implementation)
    * [`int_info`](3.标准库/sys.md#sys.int_info)
      * [`int_info.bits_per_digit`](3.标准库/sys.md#sys.int_info.bits_per_digit)
      * [`int_info.sizeof_digit`](3.标准库/sys.md#sys.int_info.sizeof_digit)
      * [`int_info.default_max_str_digits`](3.标准库/sys.md#sys.int_info.default_max_str_digits)
      * [`int_info.str_digits_check_threshold`](3.标准库/sys.md#sys.int_info.str_digits_check_threshold)
    * [`__interactivehook__`](3.标准库/sys.md#sys.__interactivehook__)
    * [`intern()`](3.标准库/sys.md#sys.intern)
    * [`is_finalizing()`](3.标准库/sys.md#sys.is_finalizing)
    * [`last_exc`](3.标准库/sys.md#sys.last_exc)
    * [`last_type`](3.标准库/sys.md#sys.last_type)
    * [`last_value`](3.标准库/sys.md#sys.last_value)
    * [`last_traceback`](3.标准库/sys.md#sys.last_traceback)
    * [`maxsize`](3.标准库/sys.md#sys.maxsize)
    * [`maxunicode`](3.标准库/sys.md#sys.maxunicode)
    * [`meta_path`](3.标准库/sys.md#sys.meta_path)
    * [`modules`](3.标准库/sys.md#sys.modules)
    * [`orig_argv`](3.标准库/sys.md#sys.orig_argv)
    * [`path`](3.标准库/sys.md#sys.path)
    * [`path_hooks`](3.标准库/sys.md#sys.path_hooks)
    * [`path_importer_cache`](3.标准库/sys.md#sys.path_importer_cache)
    * [`platform`](3.标准库/sys.md#sys.platform)
    * [`platlibdir`](3.标准库/sys.md#sys.platlibdir)
    * [`prefix`](3.标准库/sys.md#sys.prefix)
    * [`ps1`](3.标准库/sys.md#sys.ps1)
    * [`ps2`](3.标准库/sys.md#sys.ps2)
    * [`setdlopenflags()`](3.标准库/sys.md#sys.setdlopenflags)
    * [`set_int_max_str_digits()`](3.标准库/sys.md#sys.set_int_max_str_digits)
    * [`setprofile()`](3.标准库/sys.md#sys.setprofile)
    * [`setrecursionlimit()`](3.标准库/sys.md#sys.setrecursionlimit)
    * [`setswitchinterval()`](3.标准库/sys.md#sys.setswitchinterval)
    * [`settrace()`](3.标准库/sys.md#sys.settrace)
    * [`set_asyncgen_hooks()`](3.标准库/sys.md#sys.set_asyncgen_hooks)
    * [`set_coroutine_origin_tracking_depth()`](3.标准库/sys.md#sys.set_coroutine_origin_tracking_depth)
    * [`activate_stack_trampoline()`](3.标准库/sys.md#sys.activate_stack_trampoline)
    * [`deactivate_stack_trampoline()`](3.标准库/sys.md#sys.deactivate_stack_trampoline)
    * [`is_stack_trampoline_active()`](3.标准库/sys.md#sys.is_stack_trampoline_active)
    * [`_enablelegacywindowsfsencoding()`](3.标准库/sys.md#sys._enablelegacywindowsfsencoding)
    * [`stdin`](3.标准库/sys.md#sys.stdin)
    * [`stdout`](3.标准库/sys.md#sys.stdout)
    * [`stderr`](3.标准库/sys.md#sys.stderr)
    * [`__stdin__`](3.标准库/sys.md#sys.__stdin__)
    * [`__stdout__`](3.标准库/sys.md#sys.__stdout__)
    * [`__stderr__`](3.标准库/sys.md#sys.__stderr__)
    * [`stdlib_module_names`](3.标准库/sys.md#sys.stdlib_module_names)
    * [`thread_info`](3.标准库/sys.md#sys.thread_info)
      * [`thread_info.name`](3.标准库/sys.md#sys.thread_info.name)
      * [`thread_info.lock`](3.标准库/sys.md#sys.thread_info.lock)
      * [`thread_info.version`](3.标准库/sys.md#sys.thread_info.version)
    * [`tracebacklimit`](3.标准库/sys.md#sys.tracebacklimit)
    * [`unraisablehook()`](3.标准库/sys.md#sys.unraisablehook)
    * [`version`](3.标准库/sys.md#sys.version)
    * [`api_version`](3.标准库/sys.md#sys.api_version)
    * [`version_info`](3.标准库/sys.md#sys.version_info)
    * [`warnoptions`](3.标准库/sys.md#sys.warnoptions)
    * [`winver`](3.标准库/sys.md#sys.winver)
    * [`_xoptions`](3.标准库/sys.md#sys._xoptions)
  * [`sys.monitoring` \--- Execution event monitoring](sys.monitoring.md)
    * [Tool identifiers](sys.monitoring.md#tool-identifiers)
      * [Registering and using tools](sys.monitoring.md#registering-and-using-tools)
        * [`use_tool_id()`](sys.monitoring.md#sys.monitoring.use_tool_id)
        * [`free_tool_id()`](sys.monitoring.md#sys.monitoring.free_tool_id)
        * [`get_tool()`](sys.monitoring.md#sys.monitoring.get_tool)
    * [Events](sys.monitoring.md#events)
      * [Local events](sys.monitoring.md#local-events)
      * [Ancillary events](sys.monitoring.md#ancillary-events)
      * [Other events](sys.monitoring.md#other-events)
      * [The STOP_ITERATION event](sys.monitoring.md#the-stop-iteration-event)
    * [Turning events on and off](sys.monitoring.md#turning-events-on-and-off)
      * [Setting events globally](sys.monitoring.md#setting-events-globally)
        * [`get_events()`](sys.monitoring.md#sys.monitoring.get_events)
        * [`set_events()`](sys.monitoring.md#sys.monitoring.set_events)
      * [Per code object events](sys.monitoring.md#per-code-object-events)
        * [`get_local_events()`](sys.monitoring.md#sys.monitoring.get_local_events)
        * [`set_local_events()`](sys.monitoring.md#sys.monitoring.set_local_events)
      * [Disabling events](sys.monitoring.md#disabling-events)
    * [Registering callback functions](sys.monitoring.md#registering-callback-functions)
      * [`register_callback()`](sys.monitoring.md#sys.monitoring.register_callback)
      * [Callback function arguments](sys.monitoring.md#callback-function-arguments)
  * [`sysconfig` —— 提供对 Python 配置信息的访问支持](sysconfig.md)
    * [配置变量](sysconfig.md#configuration-variables)
      * [`get_config_vars()`](sysconfig.md#sysconfig.get_config_vars)
      * [`get_config_var()`](sysconfig.md#sysconfig.get_config_var)
    * [安装路径](sysconfig.md#installation-paths)
      * [`get_scheme_names()`](sysconfig.md#sysconfig.get_scheme_names)
      * [`get_default_scheme()`](sysconfig.md#sysconfig.get_default_scheme)
      * [`get_preferred_scheme()`](sysconfig.md#sysconfig.get_preferred_scheme)
      * [`_get_preferred_schemes()`](sysconfig.md#sysconfig._get_preferred_schemes)
      * [`get_path_names()`](sysconfig.md#sysconfig.get_path_names)
      * [`get_path()`](sysconfig.md#sysconfig.get_path)
      * [`get_paths()`](sysconfig.md#sysconfig.get_paths)
    * [其他功能](sysconfig.md#other-functions)
      * [`get_python_version()`](sysconfig.md#sysconfig.get_python_version)
      * [`get_platform()`](sysconfig.md#sysconfig.get_platform)
      * [`is_python_build()`](sysconfig.md#sysconfig.is_python_build)
      * [`parse_config_h()`](sysconfig.md#sysconfig.parse_config_h)
      * [`get_config_h_filename()`](sysconfig.md#sysconfig.get_config_h_filename)
      * [`get_makefile_filename()`](sysconfig.md#sysconfig.get_makefile_filename)
    * [将 `sysconfig` 作为脚本使用](sysconfig.md#using-sysconfig-as-a-script)
  * [`builtins` \--- 内建对象](builtins.md)
  * [`__main__` \--- 最高层级代码环境](__main__.md)
    * [`__name__ == '__main__'`](__main__.md#name-main)
      * [什么是“最高层级代码环境”？](__main__.md#what-is-the-top-level-code-environment)
      * [常见用法](__main__.md#idiomatic-usage)
      * [打包考量](__main__.md#packaging-considerations)
    * [Python 包中的 `__main__.py`](__main__.md#main-py-in-python-packages)
      * [常见用法](__main__.md#id1)
    * [`import __main__`](__main__.md#import-main)
  * [`warnings` —— 警告信息的控制](warnings.md)
    * [警告类别](warnings.md#warning-categories)
    * [警告过滤器](warnings.md#the-warnings-filter)
      * [警告过滤器的介绍](warnings.md#describing-warning-filters)
      * [默认警告过滤器](warnings.md#default-warning-filter)
      * [重写默认的过滤器](warnings.md#overriding-the-default-filter)
    * [暂时禁止警告](warnings.md#temporarily-suppressing-warnings)
    * [测试警告](warnings.md#testing-warnings)
    * [为新版本的依赖关系更新代码](warnings.md#updating-code-for-new-versions-of-dependencies)
    * [可用的函数](warnings.md#available-functions)
      * [`warn()`](warnings.md#warnings.warn)
      * [`warn_explicit()`](warnings.md#warnings.warn_explicit)
      * [`showwarning()`](warnings.md#warnings.showwarning)
      * [`formatwarning()`](warnings.md#warnings.formatwarning)
      * [`filterwarnings()`](warnings.md#warnings.filterwarnings)
      * [`simplefilter()`](warnings.md#warnings.simplefilter)
      * [`resetwarnings()`](warnings.md#warnings.resetwarnings)
    * [可用的上下文管理器](warnings.md#available-context-managers)
      * [`catch_warnings`](warnings.md#warnings.catch_warnings)
  * [`dataclasses` \--- 数据类](dataclasses.md)
    * [模块内容](dataclasses.md#module-contents)
      * [`dataclass()`](dataclasses.md#dataclasses.dataclass)
      * [`field()`](dataclasses.md#dataclasses.field)
      * [`Field`](dataclasses.md#dataclasses.Field)
      * [`fields()`](dataclasses.md#dataclasses.fields)
      * [`asdict()`](dataclasses.md#dataclasses.asdict)
      * [`astuple()`](dataclasses.md#dataclasses.astuple)
      * [`make_dataclass()`](dataclasses.md#dataclasses.make_dataclass)
      * [`replace()`](dataclasses.md#dataclasses.replace)
      * [`is_dataclass()`](dataclasses.md#dataclasses.is_dataclass)
      * [`MISSING`](dataclasses.md#dataclasses.MISSING)
      * [`KW_ONLY`](dataclasses.md#dataclasses.KW_ONLY)
      * [`FrozenInstanceError`](dataclasses.md#dataclasses.FrozenInstanceError)
    * [初始化后处理](dataclasses.md#post-init-processing)
      * [`__post_init__()`](dataclasses.md#dataclasses.__post_init__)
    * [类变量](dataclasses.md#class-variables)
    * [仅初始化变量](dataclasses.md#init-only-variables)
    * [冻结的实例](dataclasses.md#frozen-instances)
    * [继承](dataclasses.md#inheritance)
    * [`__init__()` 中仅限关键字形参的重新排序](dataclasses.md#re-ordering-of-keyword-only-parameters-in-init)
    * [默认工厂函数](dataclasses.md#default-factory-functions)
    * [可变的默认值](dataclasses.md#mutable-default-values)
    * [字段标注描述器类型](dataclasses.md#descriptor-typed-fields)
  * [`contextlib` \--- 为 `with`语句上下文提供的工具](contextlib.md)
    * [工具](contextlib.md#utilities)
      * [`AbstractContextManager`](contextlib.md#contextlib.AbstractContextManager)
      * [`AbstractAsyncContextManager`](contextlib.md#contextlib.AbstractAsyncContextManager)
      * [`contextmanager()`](contextlib.md#contextlib.contextmanager)
      * [`asynccontextmanager()`](contextlib.md#contextlib.asynccontextmanager)
      * [`closing()`](contextlib.md#contextlib.closing)
      * [`aclosing()`](contextlib.md#contextlib.aclosing)
      * [`nullcontext()`](contextlib.md#contextlib.nullcontext)
      * [`suppress()`](contextlib.md#contextlib.suppress)
      * [`redirect_stdout()`](contextlib.md#contextlib.redirect_stdout)
      * [`redirect_stderr()`](contextlib.md#contextlib.redirect_stderr)
      * [`chdir()`](contextlib.md#contextlib.chdir)
      * [`ContextDecorator`](contextlib.md#contextlib.ContextDecorator)
      * [`AsyncContextDecorator`](contextlib.md#contextlib.AsyncContextDecorator)
      * [`ExitStack`](contextlib.md#contextlib.ExitStack)
        * [`ExitStack.enter_context()`](contextlib.md#contextlib.ExitStack.enter_context)
        * [`ExitStack.push()`](contextlib.md#contextlib.ExitStack.push)
        * [`ExitStack.callback()`](contextlib.md#contextlib.ExitStack.callback)
        * [`ExitStack.pop_all()`](contextlib.md#contextlib.ExitStack.pop_all)
        * [`ExitStack.close()`](contextlib.md#contextlib.ExitStack.close)
      * [`AsyncExitStack`](contextlib.md#contextlib.AsyncExitStack)
        * [`AsyncExitStack.enter_async_context()`](contextlib.md#contextlib.AsyncExitStack.enter_async_context)
        * [`AsyncExitStack.push_async_exit()`](contextlib.md#contextlib.AsyncExitStack.push_async_exit)
        * [`AsyncExitStack.push_async_callback()`](contextlib.md#contextlib.AsyncExitStack.push_async_callback)
        * [`AsyncExitStack.aclose()`](contextlib.md#contextlib.AsyncExitStack.aclose)
    * [例子和配方](contextlib.md#examples-and-recipes)
      * [支持可变数量的上下文管理器](contextlib.md#supporting-a-variable-number-of-context-managers)
      * [捕获 `__enter__` 方法产生的异常](contextlib.md#catching-exceptions-from-enter-methods)
      * [在一个 `__enter__` 方法的实现中进行清理](contextlib.md#cleaning-up-in-an-enter-implementation)
      * [替换任何对 `try-finally` 和旗标变量的使用](contextlib.md#replacing-any-use-of-try-finally-and-flag-variables)
      * [将上下文管理器作为函数装饰器使用](contextlib.md#using-a-context-manager-as-a-function-decorator)
    * [单独使用，可重用并可重进入的上下文管理器](contextlib.md#single-use-reusable-and-reentrant-context-managers)
      * [重进入上下文管理器](contextlib.md#reentrant-context-managers)
      * [可重用的上下文管理器](contextlib.md#reusable-context-managers)
  * [`abc` \--- 抽象基类](abc.md)
    * [`ABC`](abc.md#abc.ABC)
    * [`ABCMeta`](abc.md#abc.ABCMeta)
      * [`ABCMeta.register()`](abc.md#abc.ABCMeta.register)
      * [`ABCMeta.__subclasshook__()`](abc.md#abc.ABCMeta.__subclasshook__)
    * [`abstractmethod()`](abc.md#abc.abstractmethod)
    * [`abstractclassmethod()`](abc.md#abc.abstractclassmethod)
    * [`abstractstaticmethod()`](abc.md#abc.abstractstaticmethod)
    * [`abstractproperty()`](abc.md#abc.abstractproperty)
    * [`get_cache_token()`](abc.md#abc.get_cache_token)
    * [`update_abstractmethods()`](abc.md#abc.update_abstractmethods)
  * [`atexit` \--- 退出处理器](atexit.md)
    * [`register()`](atexit.md#atexit.register)
    * [`unregister()`](atexit.md#atexit.unregister)
    * [`atexit` 示例](atexit.md#atexit-example)
  * [`traceback` —— 打印或读取堆栈的跟踪信息](traceback.md)
    * [`print_tb()`](traceback.md#traceback.print_tb)
    * [`print_exception()`](traceback.md#traceback.print_exception)
    * [`print_exc()`](traceback.md#traceback.print_exc)
    * [`print_last()`](traceback.md#traceback.print_last)
    * [`print_stack()`](traceback.md#traceback.print_stack)
    * [`extract_tb()`](traceback.md#traceback.extract_tb)
    * [`extract_stack()`](traceback.md#traceback.extract_stack)
    * [`format_list()`](traceback.md#traceback.format_list)
    * [`format_exception_only()`](traceback.md#traceback.format_exception_only)
    * [`format_exception()`](traceback.md#traceback.format_exception)
    * [`format_exc()`](traceback.md#traceback.format_exc)
    * [`format_tb()`](traceback.md#traceback.format_tb)
    * [`format_stack()`](traceback.md#traceback.format_stack)
    * [`clear_frames()`](traceback.md#traceback.clear_frames)
    * [`walk_stack()`](traceback.md#traceback.walk_stack)
    * [`walk_tb()`](traceback.md#traceback.walk_tb)
    * [`TracebackException` 对象](traceback.md#tracebackexception-objects)
      * [`TracebackException`](traceback.md#traceback.TracebackException)
        * [`TracebackException.__cause__`](traceback.md#traceback.TracebackException.__cause__)
        * [`TracebackException.__context__`](traceback.md#traceback.TracebackException.__context__)
        * [`TracebackException.exceptions`](traceback.md#traceback.TracebackException.exceptions)
        * [`TracebackException.__suppress_context__`](traceback.md#traceback.TracebackException.__suppress_context__)
        * [`TracebackException.__notes__`](traceback.md#traceback.TracebackException.__notes__)
        * [`TracebackException.stack`](traceback.md#traceback.TracebackException.stack)
        * [`TracebackException.exc_type`](traceback.md#traceback.TracebackException.exc_type)
        * [`TracebackException.filename`](traceback.md#traceback.TracebackException.filename)
        * [`TracebackException.lineno`](traceback.md#traceback.TracebackException.lineno)
        * [`TracebackException.end_lineno`](traceback.md#traceback.TracebackException.end_lineno)
        * [`TracebackException.text`](traceback.md#traceback.TracebackException.text)
        * [`TracebackException.offset`](traceback.md#traceback.TracebackException.offset)
        * [`TracebackException.end_offset`](traceback.md#traceback.TracebackException.end_offset)
        * [`TracebackException.msg`](traceback.md#traceback.TracebackException.msg)
        * [`TracebackException.from_exception()`](traceback.md#traceback.TracebackException.from_exception)
        * [`TracebackException.print()`](traceback.md#traceback.TracebackException.print)
        * [`TracebackException.format()`](traceback.md#traceback.TracebackException.format)
        * [`TracebackException.format_exception_only()`](traceback.md#traceback.TracebackException.format_exception_only)
    * [`StackSummary` 对象](traceback.md#stacksummary-objects)
      * [`StackSummary`](traceback.md#traceback.StackSummary)
        * [`StackSummary.extract()`](traceback.md#traceback.StackSummary.extract)
        * [`StackSummary.from_list()`](traceback.md#traceback.StackSummary.from_list)
        * [`StackSummary.format()`](traceback.md#traceback.StackSummary.format)
        * [`StackSummary.format_frame_summary()`](traceback.md#traceback.StackSummary.format_frame_summary)
    * [`FrameSummary` 对象](traceback.md#framesummary-objects)
      * [`FrameSummary`](traceback.md#traceback.FrameSummary)
    * [回溯示例](traceback.md#traceback-examples)
  * [`__future__` \--- Future 语句定义](__future__.md)
  * [`gc` \--- 垃圾回收器接口](gc.md)
    * [`enable()`](gc.md#gc.enable)
    * [`disable()`](gc.md#gc.disable)
    * [`isenabled()`](gc.md#gc.isenabled)
    * [`collect()`](gc.md#gc.collect)
    * [`set_debug()`](gc.md#gc.set_debug)
    * [`get_debug()`](gc.md#gc.get_debug)
    * [`get_objects()`](gc.md#gc.get_objects)
    * [`get_stats()`](gc.md#gc.get_stats)
    * [`set_threshold()`](gc.md#gc.set_threshold)
    * [`get_count()`](gc.md#gc.get_count)
    * [`get_threshold()`](gc.md#gc.get_threshold)
    * [`get_referrers()`](gc.md#gc.get_referrers)
    * [`get_referents()`](gc.md#gc.get_referents)
    * [`is_tracked()`](gc.md#gc.is_tracked)
    * [`is_finalized()`](gc.md#gc.is_finalized)
    * [`freeze()`](gc.md#gc.freeze)
    * [`unfreeze()`](gc.md#gc.unfreeze)
    * [`get_freeze_count()`](gc.md#gc.get_freeze_count)
    * [`garbage`](gc.md#gc.garbage)
    * [`callbacks`](gc.md#gc.callbacks)
    * [`DEBUG_STATS`](gc.md#gc.DEBUG_STATS)
    * [`DEBUG_COLLECTABLE`](gc.md#gc.DEBUG_COLLECTABLE)
    * [`DEBUG_UNCOLLECTABLE`](gc.md#gc.DEBUG_UNCOLLECTABLE)
    * [`DEBUG_SAVEALL`](gc.md#gc.DEBUG_SAVEALL)
    * [`DEBUG_LEAK`](gc.md#gc.DEBUG_LEAK)
  * [`inspect` \--- 检查对象](inspect.md)
    * [类型和成员](inspect.md#types-and-members)
      * [`getmembers()`](inspect.md#inspect.getmembers)
      * [`getmembers_static()`](inspect.md#inspect.getmembers_static)
      * [`getmodulename()`](inspect.md#inspect.getmodulename)
      * [`ismodule()`](inspect.md#inspect.ismodule)
      * [`isclass()`](inspect.md#inspect.isclass)
      * [`ismethod()`](inspect.md#inspect.ismethod)
      * [`isfunction()`](inspect.md#inspect.isfunction)
      * [`isgeneratorfunction()`](inspect.md#inspect.isgeneratorfunction)
      * [`isgenerator()`](inspect.md#inspect.isgenerator)
      * [`iscoroutinefunction()`](inspect.md#inspect.iscoroutinefunction)
      * [`markcoroutinefunction()`](inspect.md#inspect.markcoroutinefunction)
      * [`iscoroutine()`](inspect.md#inspect.iscoroutine)
      * [`isawaitable()`](inspect.md#inspect.isawaitable)
      * [`isasyncgenfunction()`](inspect.md#inspect.isasyncgenfunction)
      * [`isasyncgen()`](inspect.md#inspect.isasyncgen)
      * [`istraceback()`](inspect.md#inspect.istraceback)
      * [`isframe()`](inspect.md#inspect.isframe)
      * [`iscode()`](inspect.md#inspect.iscode)
      * [`isbuiltin()`](inspect.md#inspect.isbuiltin)
      * [`ismethodwrapper()`](inspect.md#inspect.ismethodwrapper)
      * [`isroutine()`](inspect.md#inspect.isroutine)
      * [`isabstract()`](inspect.md#inspect.isabstract)
      * [`ismethoddescriptor()`](inspect.md#inspect.ismethoddescriptor)
      * [`isdatadescriptor()`](inspect.md#inspect.isdatadescriptor)
      * [`isgetsetdescriptor()`](inspect.md#inspect.isgetsetdescriptor)
      * [`ismemberdescriptor()`](inspect.md#inspect.ismemberdescriptor)
    * [获取源代码](inspect.md#retrieving-source-code)
      * [`getdoc()`](inspect.md#inspect.getdoc)
      * [`getcomments()`](inspect.md#inspect.getcomments)
      * [`getfile()`](inspect.md#inspect.getfile)
      * [`getmodule()`](inspect.md#inspect.getmodule)
      * [`getsourcefile()`](inspect.md#inspect.getsourcefile)
      * [`getsourcelines()`](inspect.md#inspect.getsourcelines)
      * [`getsource()`](inspect.md#inspect.getsource)
      * [`cleandoc()`](inspect.md#inspect.cleandoc)
    * [使用 Signature 对象对可调用对象进行内省](inspect.md#introspecting-callables-with-the-signature-object)
      * [`signature()`](inspect.md#inspect.signature)
      * [`Signature`](inspect.md#inspect.Signature)
        * [`Signature.empty`](inspect.md#inspect.Signature.empty)
        * [`Signature.parameters`](inspect.md#inspect.Signature.parameters)
        * [`Signature.return_annotation`](inspect.md#inspect.Signature.return_annotation)
        * [`Signature.bind()`](inspect.md#inspect.Signature.bind)
        * [`Signature.bind_partial()`](inspect.md#inspect.Signature.bind_partial)
        * [`Signature.replace()`](inspect.md#inspect.Signature.replace)
        * [`Signature.from_callable()`](inspect.md#inspect.Signature.from_callable)
      * [`Parameter`](inspect.md#inspect.Parameter)
        * [`Parameter.empty`](inspect.md#inspect.Parameter.empty)
        * [`Parameter.name`](inspect.md#inspect.Parameter.name)
        * [`Parameter.default`](inspect.md#inspect.Parameter.default)
        * [`Parameter.annotation`](inspect.md#inspect.Parameter.annotation)
        * [`Parameter.kind`](inspect.md#inspect.Parameter.kind)
          * [`Parameter.kind.description`](inspect.md#inspect.Parameter.kind.description)
        * [`Parameter.replace()`](inspect.md#inspect.Parameter.replace)
      * [`BoundArguments`](inspect.md#inspect.BoundArguments)
        * [`BoundArguments.arguments`](inspect.md#inspect.BoundArguments.arguments)
        * [`BoundArguments.args`](inspect.md#inspect.BoundArguments.args)
        * [`BoundArguments.kwargs`](inspect.md#inspect.BoundArguments.kwargs)
        * [`BoundArguments.signature`](inspect.md#inspect.BoundArguments.signature)
        * [`BoundArguments.apply_defaults()`](inspect.md#inspect.BoundArguments.apply_defaults)
    * [类与函数](inspect.md#classes-and-functions)
      * [`getclasstree()`](inspect.md#inspect.getclasstree)
      * [`getfullargspec()`](inspect.md#inspect.getfullargspec)
      * [`getargvalues()`](inspect.md#inspect.getargvalues)
      * [`formatargvalues()`](inspect.md#inspect.formatargvalues)
      * [`getmro()`](inspect.md#inspect.getmro)
      * [`getcallargs()`](inspect.md#inspect.getcallargs)
      * [`getclosurevars()`](inspect.md#inspect.getclosurevars)
      * [`unwrap()`](inspect.md#inspect.unwrap)
      * [`get_annotations()`](inspect.md#inspect.get_annotations)
    * [解释器栈](inspect.md#the-interpreter-stack)
      * [`FrameInfo`](inspect.md#inspect.FrameInfo)
        * [`FrameInfo.frame`](inspect.md#inspect.FrameInfo.frame)
        * [`FrameInfo.filename`](inspect.md#inspect.FrameInfo.filename)
        * [`FrameInfo.lineno`](inspect.md#inspect.FrameInfo.lineno)
        * [`FrameInfo.function`](inspect.md#inspect.FrameInfo.function)
        * [`FrameInfo.code_context`](inspect.md#inspect.FrameInfo.code_context)
        * [`FrameInfo.index`](inspect.md#inspect.FrameInfo.index)
        * [`FrameInfo.positions`](inspect.md#inspect.FrameInfo.positions)
      * [`Traceback`](inspect.md#inspect.Traceback)
        * [`Traceback.filename`](inspect.md#inspect.Traceback.filename)
        * [`Traceback.lineno`](inspect.md#inspect.Traceback.lineno)
        * [`Traceback.function`](inspect.md#inspect.Traceback.function)
        * [`Traceback.code_context`](inspect.md#inspect.Traceback.code_context)
        * [`Traceback.index`](inspect.md#inspect.Traceback.index)
        * [`Traceback.positions`](inspect.md#inspect.Traceback.positions)
      * [`getframeinfo()`](inspect.md#inspect.getframeinfo)
      * [`getouterframes()`](inspect.md#inspect.getouterframes)
      * [`getinnerframes()`](inspect.md#inspect.getinnerframes)
      * [`currentframe()`](inspect.md#inspect.currentframe)
      * [`stack()`](inspect.md#inspect.stack)
      * [`trace()`](inspect.md#inspect.trace)
    * [静态地获取属性](inspect.md#fetching-attributes-statically)
      * [`getattr_static()`](inspect.md#inspect.getattr_static)
    * [生成器、协程和异步生成器的当前状态](inspect.md#current-state-of-generators-coroutines-and-asynchronous-generators)
      * [`getgeneratorstate()`](inspect.md#inspect.getgeneratorstate)
      * [`getcoroutinestate()`](inspect.md#inspect.getcoroutinestate)
      * [`getasyncgenstate()`](inspect.md#inspect.getasyncgenstate)
      * [`getgeneratorlocals()`](inspect.md#inspect.getgeneratorlocals)
      * [`getcoroutinelocals()`](inspect.md#inspect.getcoroutinelocals)
      * [`getasyncgenlocals()`](inspect.md#inspect.getasyncgenlocals)
    * [代码对象位标志](inspect.md#code-objects-bit-flags)
      * [`CO_OPTIMIZED`](inspect.md#inspect.CO_OPTIMIZED)
      * [`CO_NEWLOCALS`](inspect.md#inspect.CO_NEWLOCALS)
      * [`CO_VARARGS`](inspect.md#inspect.CO_VARARGS)
      * [`CO_VARKEYWORDS`](inspect.md#inspect.CO_VARKEYWORDS)
      * [`CO_NESTED`](inspect.md#inspect.CO_NESTED)
      * [`CO_GENERATOR`](inspect.md#inspect.CO_GENERATOR)
      * [`CO_COROUTINE`](inspect.md#inspect.CO_COROUTINE)
      * [`CO_ITERABLE_COROUTINE`](inspect.md#inspect.CO_ITERABLE_COROUTINE)
      * [`CO_ASYNC_GENERATOR`](inspect.md#inspect.CO_ASYNC_GENERATOR)
    * [缓冲区旗标](inspect.md#buffer-flags)
      * [`BufferFlags`](inspect.md#inspect.BufferFlags)
        * [`BufferFlags.SIMPLE`](inspect.md#inspect.BufferFlags.SIMPLE)
        * [`BufferFlags.WRITABLE`](inspect.md#inspect.BufferFlags.WRITABLE)
        * [`BufferFlags.FORMAT`](inspect.md#inspect.BufferFlags.FORMAT)
        * [`BufferFlags.ND`](inspect.md#inspect.BufferFlags.ND)
        * [`BufferFlags.STRIDES`](inspect.md#inspect.BufferFlags.STRIDES)
        * [`BufferFlags.C_CONTIGUOUS`](inspect.md#inspect.BufferFlags.C_CONTIGUOUS)
        * [`BufferFlags.F_CONTIGUOUS`](inspect.md#inspect.BufferFlags.F_CONTIGUOUS)
        * [`BufferFlags.ANY_CONTIGUOUS`](inspect.md#inspect.BufferFlags.ANY_CONTIGUOUS)
        * [`BufferFlags.INDIRECT`](inspect.md#inspect.BufferFlags.INDIRECT)
        * [`BufferFlags.CONTIG`](inspect.md#inspect.BufferFlags.CONTIG)
        * [`BufferFlags.CONTIG_RO`](inspect.md#inspect.BufferFlags.CONTIG_RO)
        * [`BufferFlags.STRIDED`](inspect.md#inspect.BufferFlags.STRIDED)
        * [`BufferFlags.STRIDED_RO`](inspect.md#inspect.BufferFlags.STRIDED_RO)
        * [`BufferFlags.RECORDS`](inspect.md#inspect.BufferFlags.RECORDS)
        * [`BufferFlags.RECORDS_RO`](inspect.md#inspect.BufferFlags.RECORDS_RO)
        * [`BufferFlags.FULL`](inspect.md#inspect.BufferFlags.FULL)
        * [`BufferFlags.FULL_RO`](inspect.md#inspect.BufferFlags.FULL_RO)
        * [`BufferFlags.READ`](inspect.md#inspect.BufferFlags.READ)
        * [`BufferFlags.WRITE`](inspect.md#inspect.BufferFlags.WRITE)
    * [命令行界面](inspect.md#command-line-interface)
  * [`site` —— 指定域的配置钩子](site.md)
    * [Readline 配置](site.md#readline-configuration)
    * [模块内容](site.md#module-contents)
      * [`PREFIXES`](site.md#site.PREFIXES)
      * [`ENABLE_USER_SITE`](site.md#site.ENABLE_USER_SITE)
      * [`USER_SITE`](site.md#site.USER_SITE)
      * [`USER_BASE`](site.md#site.USER_BASE)
      * [`main()`](site.md#site.main)
      * [`addsitedir()`](site.md#site.addsitedir)
      * [`getsitepackages()`](site.md#site.getsitepackages)
      * [`getuserbase()`](site.md#site.getuserbase)
      * [`getusersitepackages()`](site.md#site.getusersitepackages)
    * [命令行界面](site.md#command-line-interface)

