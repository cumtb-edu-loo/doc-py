# Unix syslog 库例程¶

* * *

此模块提供一个接口到Unix `syslog` 日常库. 参考 Unix 手册页关于 `syslog` 设施的详细描述.

此模块包装了系统的 `syslog` 例程族。 一个能与 syslog 服务器对话的纯 Python 库则以 [`logging.handlers`](logging.handlers.md#module-logging.handlers "logging.handlers: Handlers for the logging module.") 模块中 `SysLogHandler` 类的形式提供。

[可用性](3.标准库/intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](3.标准库/intro.md#wasm-availability) 了解详情。

这个模块定义了以下函数：

syslog.syslog( _message_ )¶

syslog.syslog( _priority_ , _message_ )

    

~~~
将字符串 _message_ 发送到系统日志记录器。 如有必要会添加末尾换行符。 每条消息都带有一个由 _facility_ 和 _level_ 组成的优先级标价签。 可选的 _priority_ 参数默认值为 `LOG_INFO`，它确定消息的优先级。 如果未在 _priority_ 中使用逻辑或 (`LOG_INFO | LOG_USER`) 对 facility 进行编码，则会使用在 `openlog()` 调用中所给定的值。

如果 `openlog()` 未在对 `syslog()` 的调用之前被调用，则将不带参数地调用 `openlog()`。

引发 [审计事件](sys.md#auditing) `syslog.syslog` 使用参数 `priority`, `message`.

在 3.2 版本发生变更: 在之前的版本中，如果 `openlog()` 未在对 `syslog()` 的调用之前被调用则它将不会被自动调用，而是由 syslog 实现来负责调用 `openlog()`。

在 3.12 版本发生变更: 此函数在子解释器中受到限制。 （该限制只影响在多解释器中运行的代码因而与大多数用户无关。） `openlog()` 必须在子解释器使用 `syslog()` 之前在主解释器中被调用。 否则它将引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

syslog.openlog([ _ident_ [, _logoption_ [, _facility_ ]]])¶
~~~
    

~~~
后续 `syslog()` 调用的日志选项可以通过调用 `openlog()` 来设置。 如果日志当前未打开则 `syslog()` 将不带参数地调用 `openlog()`。

可选的 _ident_ 关键字参数是在每条消息前添加的字符串，默认为 `sys.argv[0]` 去除打头的路径部分。 可选的 _logoption_ 关键字参数（默认为 0）是一个位字段 -- 请参见下文了解可能的组合值。 可选的 _facility_ 关键字参数 (默认为 `LOG_USER`) 为没有显式编码 facility 的消息设置默认的 facility。

引发 [审计事件](sys.md#auditing) `syslog.openlog` 使用参数 `ident`, `logoption`, `facility`.

在 3.2 版本发生变更: 在之前的版本中，不允许使用关键字参数，并且要求必须有 _ident_ 。

在 3.12 版本发生变更: 此函数在子解释器中受到限制。 （该限制只影响在多解释器中运行的代码因而与大多数用户无关。） 此函数只能在主解释器中被调用。 如果在子解释器中被调用它将引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

syslog.closelog()¶
~~~
    

~~~
重置日志模块值并且调用系统库 `closelog()`.

这使得此模块在初始导入时行为固定。 例如，`openlog()` 将在首次调用 `syslog()` 时被调用（如果 `openlog()` 还未被调用过），并且 _ident_ 和其他 `openlog()` 形参会被重置为默认值。

引发一个 [审计事件](sys.md#auditing) `syslog.closelog` 不附带任何参数。

在 3.12 版本发生变更: 此函数在子解释器中受到限制。 （该限制只影响在多解释器中运行的代码因而与大多数用户无关。） 此函数只能在主解释器中被调用。 如果在子解释器中被调用它将引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

syslog.setlogmask( _maskpri_ )¶
~~~
    

~~~
将优先级掩码设为 _maskpri_ 并返回之前的掩码值。 调用 `syslog()` 并附带未在 _maskpri_ 中设置的优先级将会被忽略。 默认设置为记录所有优先级。 函数 `LOG_MASK(pri)` 可计算单个优先级 _pri_ 的掩码。 函数 `LOG_UPTO(pri)` 可计算包括 _pri_ 在内的所有优先级的掩码。

引发一个 [审计事件](sys.md#auditing) `syslog.setlogmask` 附带参数 `maskpri`。

此模块定义了一下常量:

优先级级别 (高到低):
~~~
    

~~~
`LOG_EMERG`, `LOG_ALERT`, `LOG_CRIT`, `LOG_ERR`, `LOG_WARNING`, `LOG_NOTICE`, `LOG_INFO`, `LOG_DEBUG`.

设施:
~~~
    

~~~
`LOG_KERN`, `LOG_USER`, `LOG_MAIL`, `LOG_DAEMON`, `LOG_AUTH`, `LOG_LPR`, `LOG_NEWS`, `LOG_UUCP`, `LOG_CRON`, `LOG_SYSLOG`, `LOG_LOCAL0` to `LOG_LOCAL7`，如果 `<syslog.h>` 中有定义则还有 `LOG_AUTHPRIV`。

日志选项:
~~~
    

~~~
`LOG_PID`, `LOG_CONS`, `LOG_NDELAY`，如果 `<syslog.h>` 中有定义则还有 `LOG_ODELAY`, `LOG_NOWAIT` 以及 `LOG_PERROR`。

## 例子¶

### 简单示例¶

一个简单的示例集:
~~~
    
    
~~~
import syslog

syslog.syslog('Processing started')
if error:
    syslog.syslog(syslog.LOG_ERR, 'Processing started')
~~~

一个设置多种日志选项的示例，其中有在日志消息中包含进程 ID，以及将消息写入用于邮件日志记录的目标设施等:

    
    
~~~
syslog.openlog(logoption=syslog.LOG_PID, facility=syslog.LOG_MAIL)
syslog.syslog('E-mail processing initiated...')
~~~

