# `logging.config` \--- 日志记录配置¶

**源代码：** [Lib/logging/config.py](https://github.com/python/cpython/tree/3.12/Lib/logging/config.py)

Important

此页面仅包含参考信息。有关教程，请参阅

  * [基础教程](6.常用指引/logging.md#logging-basic-tutorial)

  * [进阶教程](6.常用指引/logging.md#logging-advanced-tutorial)

  * [日志记录操作手册](logging-cookbook.md#logging-cookbook)

* * *

这一节描述了用于配置 logging 模块的 API。

## 配置函数¶

下列函数可配置 logging 模块。 它们位于 `logging.config` 模块中。 它们的使用是可选的 --- 要配置 logging 模块你可以使用这些函数，也可以通过调用主 API (在 [`logging`](3.标准库/logging.md#module-logging "logging: Flexible event logging system for applications.") 本身定义) 并定义在 [`logging`](3.标准库/logging.md#module-logging "logging: Flexible event logging system for applications.") 或 [`logging.handlers`](logging.handlers.md#module-logging.handlers "logging.handlers: Handlers for the logging module.") 中声明的处理器。

logging.config.dictConfig( _config_ )¶

    

~~~
从一个字典获取日志记录配置。 字典的内容描述见下文的 配置字典架构。

如果在配置期间遇到错误，此函数将引发 [`ValueError`](exceptions.md#ValueError "ValueError"), [`TypeError`](exceptions.md#TypeError "TypeError"), [`AttributeError`](exceptions.md#AttributeError "AttributeError") 或 [`ImportError`](exceptions.md#ImportError "ImportError") 并附带适当的描述性消息。 下面是将会引发错误的（可能不完整的）条件列表:

  * `level` 不是字符串或者不是对应于实际日志记录级别的字符串。

  * `propagate` 值不是布尔类型。

  * id 没有对应的目标。

  * 在增量调用期间发现不存在的处理器 id。

  * 无效的日志记录器名称。

  * 无法解析为内部或外部对象。

解析由 `DictConfigurator` 类执行，该类的构造器可传入用于配置的字典，并且具有 `configure()` 方法。 `logging.config` 模块具有可调用属性 `dictConfigClass`，其初始值设为 `DictConfigurator`。 你可以使用你自己的适当实现来替换 `dictConfigClass` 的值。

`dictConfig()` 会调用 `dictConfigClass` 并传入指定的字典，然后在所返回的对象上调用 `configure()` 方法以使配置生效:
~~~
    
    
~~~python
def dictConfig(config):
    dictConfigClass(config).configure()
~~~

例如，`DictConfigurator` 的子类可以在它自己的 `__init__()` 中调用 `DictConfigurator.__init__()`，然后设置可以在后续 `configure()` 调用中使用的自定义前缀。 `dictConfigClass` 将被绑定到这个新的子类，然后就可以与在默认的未定制状态下完全相同的方式调用 `dictConfig()`。

在 3.2 版本加入.

logging.config.fileConfig( _fname_ , _defaults =None_, _disable_existing_loggers =True_, _encoding =None_)¶

    

~~~
从一个 [`configparser`](configparser.md#module-configparser "configparser: Configuration file parser.") 格式文件中读取日志记录配置。 文件格式应当与 配置文件格式 中的描述一致。 此函数可在应用程序中被多次调用，以允许最终用户在多个预设配置中进行选择（如果开发者提供了展示选项并加载选定配置的机制）。

如果文件不存在将引发 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError") 而如果文件无效或为空则将引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

参数:
~~~
    

~~~
  * **fname** \-- 一个文件名，或一个文件类对象，或是一个派生自 [`RawConfigParser`](configparser.md#configparser.RawConfigParser "configparser.RawConfigParser") 的实例。 如果传入了一个派生自 `RawConfigParser` 的实例，它会被原样使用。 否则，将会实例化一个 `Configparser`，并且它会从作为 `fname` 传入的对象中读取配置。 如果存在 [`readline()`](readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 方法，则它会被当作一个文件类对象并使用 [`read_file()`](configparser.md#configparser.ConfigParser.read_file "configparser.ConfigParser.read_file") 来读取；在其它情况下，它会被当作一个文件名并传递给 [`read()`](configparser.md#configparser.ConfigParser.read "configparser.ConfigParser.read")。

  * **defaults** \-- 要传递给 ConfigParser 的默认值可在此参数中指定。

  * **disable_existing_loggers** \-- 如果指定为 `False`，则当执行此调用时已存在的日志记录器会保持启用。 默认值为 `True` 因为这将以向下兼容方式启用旧行为。 此行为是禁用任何现有的非根日志记录器除非它们或它们的上级在日志记录配置中被显式地命名。

  * **encoding** \-- 当 _fname_ 为文件名时被用于打开文件的编码格式。

在 3.4 版本发生变更: 现在接受 [`RawConfigParser`](configparser.md#configparser.RawConfigParser "configparser.RawConfigParser") 子类的实例作为 `fname` 的值。 这有助于:

>   * 使用一个配置文件，其中日志记录配置只是全部应用程序配置的一部分。
>
>   * 使用从一个文件读取的配置，它随后会在被传给 `fileConfig` 之前由使用配置的应用程序来修改（例如基于命令行参数或运行时环境的其他部分）。
>
>

在 3.10 版本加入: 增加了 _encoding_ 形参。

在 3.12 版本发生变更: 如果所提供的文件不存在或无效或为空则将抛出一个异常。

logging.config.listen( _port =DEFAULT_LOGGING_CONFIG_PORT_, _verify =None_)¶
~~~
    

~~~
在指定的端口上启动套接字服务器，并监听新的配置。 如果未指定端口，则会使用模块默认的 `DEFAULT_LOGGING_CONFIG_PORT`。 日志记录配置将作为适合由 `dictConfig()` 或 `fileConfig()` 进行处理的文件来发送。 返回一个 [`Thread`](threading.md#threading.Thread "threading.Thread") 实例，你可以在该实例上调用 [`start()`](threading.md#threading.Thread.start "threading.Thread.start") 来启动服务器，对该服务器你可以在适当的时候执行 [`join()`](threading.md#threading.Thread.join "threading.Thread.join")。 要停止该服务器，请调用 `stopListening()`。

如果指定 `verify` 参数，则它应当是一个可调用对象，该对象应当验证通过套接字接收的字节数据是否有效且应被处理。 这可以通过对通过套接字发送的内容进行加密和/或签名来完成，这样 `verify` 可调用对象就能执行签名验证和/或解密。 `verify` 可调用对象的调用会附带一个参数 —— 通过套接字接收的字节数据 —— 并应当返回要处理的字节数据，或者返回 `None` 来指明这些字节数据应当被丢弃。 返回的字节数据可以与传入的字节数据相同（例如在只执行验证的时候），或者也可以完全不同（例如在可能执行了解密的时候）。

要将配置发送到套接字，请读取配置文件并将其作为字节序列发送到套接字，字节序列要以使用 `struct.pack('>L', n)` 打包为二进制格式的四字节长度的字符串打头。

备注

因为配置的各部分是通过 [`eval()`](functions.md#eval "eval") 传递的，使用此函数可能让用户面临安全风险。虽然此函数仅绑定到 `localhost` 上的套接字，因此并不接受来自远端机器的连接，但在某些场景中不受信任的代码可以在调用 `listen()` 的进程的账户下运行。具体来说，如果如果调用 `listen()` 的进程在用户无法彼此信任的多用户机器上运行，则恶意用户就能简单地通过连接到受害者的 `listen()` 套接字并发送运行攻击者想在受害者的进程上执行的任何代码的配置的方式，安排运行几乎任意的代码。如果是使用默认端口这会特别容易做到，即便使用了不同端口也不难做到。要避免发生这种情况的风险，请在 `listen()` 中使用 `verify` 参数来防止未经认可的配置被应用。

在 3.4 版本发生变更: 添加了 `verify` 参数。

备注

如果你希望将配置发送给未禁用现有日志记录器的监听器，你将需要使用 JSON 格式的配置，该格式将使用 `dictConfig()` 进行配置。 此方法允许你在你发送的配置中将 `disable_existing_loggers` 指定为 `False`。

logging.config.stopListening()¶
~~~
    

~~~
停止通过对 `listen()` 的调用所创建的监听服务器。 此函数的调用通常会先于在 `listen()` 的返回值上调用 `join()`。

## 安全考量¶

日志配置功能试图提供便利，从某种角度来说，这是通过将配置文件中的文本转换为日志配置中使用的 Python 对象来完成的 —— 如 用户定义对象 中所述。但是，这些相同的机制（从用户定义的模块中导入可调用对象并使用配置中的参数调用它们）可用于调用您指定的任何代码，因此您应该*非常谨慎*地处理来自非信任源的配置文件。并且在实际加载前，您应该确信加载不会导致坏事情。

## 配置字典架构¶

描述日志记录配置需要列出要创建的不同对象及它们之间的连接；例如，你可以创建一个名为 'console' 的处理器，然后名为 'startup' 的日志记录器将可以把它的消息发送给 'console' 处理器。 这些对象并不仅限于 [`logging`](logging.md#module-logging "logging: Flexible event logging system for applications.") 模块所提供的对象，因为你还可以编写你自己的格式化或处理器类。 这些类的形参可能还需要包括 `sys.stderr` 这样的外部对象。 描述这些对象和连接的语法会在下面的 对象连接 中定义。

### 字典架构细节¶

传给 `dictConfig()` 的字典必须包含以下的键:

  * _version_ \- 应设为代表架构版本的整数值。 目前唯一有效的值是 1，使用此键可允许架构在继续演化的同时保持向下兼容性。

所有其他键都是可选项，但如存在它们将根据下面的描述来解读。 在下面提到 'configuring dict' 的所有情况下，都将检查它的特殊键 `'()'` 以确定是否需要自定义实例化。 如果需要，则会使用下面 用户定义对象 所描述的机制来创建一个实例；否则，会使用上下文来确定要实例化的对象。

  * _formatters_ \- 对应的值将是一个字典，其中每个键是一个格式器 ID 而每个值则是一个描述如何配置相应 [`Formatter`](logging.md#logging.Formatter "logging.Formatter") 实例的字典。

在配置字典中搜索以下可选键，这些键对应于创建 [`Formatter`](logging.md#logging.Formatter "logging.Formatter") 对象时传入的参数。:

>     * `format`
>
>     * `datefmt`
>
>     * `style`
>
>     * `validate` (从版本 >=3.8 起)
>
>     * `defaults` (版本 >=3.12)

可选的 `class` 键指定格式化器类的名称（形式为带点号的模块名和类名）。 实例化的参数与 [`Formatter`](logging.md#logging.Formatter "logging.Formatter") 的相同，因此这个键对于实例化自定义的 [`Formatter`](logging.md#logging.Formatter "logging.Formatter") 子类最为有用。 如果，替代类可能 会以扩展和精简格式呈现异常回溯信息。 如果你的格式化器需要不同的或额外的配置键，你应当使用 用户定义对象。

  * _filters_ \- 对应的值将是一个字典，其中每个键是一个过滤器 ID 而每个值则是一个描述如何配置相应 Filter 实例的字典。

将在配置字典中搜索键 `name` (默认值为空字符串) 并且该键会被用于构造 [`logging.Filter`](logging.md#logging.Filter "logging.Filter") 实例。

  * _handlers_ \- 对应的值将是一个字典，其中每个键是一个处理器 ID 而每个值则是一个描述如何配置相应 Handler 实例的字典。

将在配置字典中搜索下列键:

    * `class` (强制)。 这是处理器类的完整限定名称。

    * `level` (可选)。 处理器的级别。

    * `formatter` (可选)。 处理器所对应格式化器的 ID。

    * `filters` (可选)。 由处理器所对应过滤器的 ID 组成的列表。

在 3.11 版本发生变更: `filters` 除了 id 以外还能接受 filter 实例。

所有 _其他_ 键会被作为关键字参数传递给处理器类的构造器。 例如，给定如下配置:
~~~
    
        handlers:
      console:
        class : logging.StreamHandler
        formatter: brief
        level   : INFO
        filters: [allow_foo]
        stream  : ext://sys.stdout
      file:
        class : logging.handlers.RotatingFileHandler
        formatter: precise
        filename: logconfig.log
        maxBytes: 1024
        backupCount: 3
    

ID 为 `console` 的处理器会被实例化为 [`logging.StreamHandler`](logging.handlers.md#logging.StreamHandler "logging.StreamHandler")，并使用 `sys.stdout` 作为下层流。 ID 为 `file` 的处理器会被实例化为 [`logging.handlers.RotatingFileHandler`](logging.handlers.md#logging.handlers.RotatingFileHandler "logging.handlers.RotatingFileHandler")，并附带关键字参数 `filename='logconfig.log', maxBytes=1024, backupCount=3`。

  * _loggers_ \- 对应的值将是一个字典，其中每个键是一个日志记录器名称而每个值则是一个描述如何配置相应 Logger 实例的字典。

将在配置字典中搜索下列键:

    * `level` (可选)。 日志记录器的级别。

    * `propagate` (可选)。 日志记录器的传播设置。

    * `filters` (可选)。 由日志记录器对应过滤器的 ID 组成的列表。

在 3.11 版本发生变更: `filters` 除了 id 以外还能接受 filter 实例。

    * `handlers` (可选)。 由日志记录器对应处理器的 ID 组成的列表。

指定的记录器将根据指定的级别、传播、过滤器和处理器来配置。

  * _root_ \- 这将成为根日志记录器对应的配置。 配置的处理方式将与所有日志记录器一致，除了 `propagate` 设置将不可用之外。

  * _incremental_ \- 配置是否要被解读为在现有配置上新增。 该值默认为 `False`，这意味着指定的配置将以与当前 `fileConfig()` API 所使用的相同语义来替代现有的配置。

如果指定的值为 `True`，配置会按照 增量配置 部分所描述的方式来处理。

  * _disable_existing_loggers_ \- 是否要禁用任何现有的非根日志记录器。 该设置对应于 `fileConfig()` 中的同名形参。 如果省略，则此形参默认为 `True`。 如果 _incremental_ 为 `True` 则该省会被忽略。

### 增量配置¶

为增量配置提供完全的灵活性是很困难的。 例如，由于过滤器和格式化器这样的对象是匿名的，一旦完成配置，在增加配置时就不可能引用这些匿名对象。

此外，一旦完成了配置，在运行时任意改变日志记录器、处理器、过滤器、格式化器的对象图就不是很有必要；日志记录器和处理器的详细程度只需通过设置级别即可实现控制（对于日志记录器则可设置传播旗标）。 在多线程环境中以安全的方式任意改变对象图也许会导致问题；虽然并非不可能，但这样做的好处不足以抵销其所增加的实现复杂度。

这样，当配置字典的 `incremental` 键存在且为 `True` 时，系统将完全忽略任何 `formatters` 和 `filters` 条目，并仅会处理 `handlers` 条目中的 `level` 设置，以及 `loggers` 和 `root` 条目中的 `level` 和 `propagate` 设置。

使用配置字典中的值可让配置以封存字典对象的形式通过线路传送给套接字监听器。 这样，长时间运行的应用程序的日志记录的详细程度可随时间改变而无须停止并重新启动应用程序。

### 对象连接¶

该架构描述了一组日志记录对象 —— 日志记录器、处理器、格式化器、过滤器 —— 它们在对象图中彼此连接。 因此，该架构需要能表示对象之间的连接。 例如，在配置完成后，一个特定的日志记录器关联到了一个特定的处理器。 出于讨论的目的，我们可以说该日志记录器代表两者间连接的源头，而处理器则代表对应的目标。 当然在已配置对象中这是由包含对处理器的引用的日志记录器来代表的。 在配置字典中，这是通过给每个目标对象一个 ID 来无歧义地标识它，然后在源头对象中使用该 ID 来实现的。

因此，举例来说，考虑以下 YAML 代码段:

    
    
~~~
formatters:
  brief:
    # configuration for formatter with id 'brief' goes here
  precise:
    # configuration for formatter with id 'precise' goes here
handlers:
  h1: #This is an id
   # configuration of handler with id 'h1' goes here
   formatter: brief
  h2: #This is another id
   # configuration of handler with id 'h2' goes here
   formatter: precise
loggers:
  foo.bar.baz:
    # other configuration for logger 'foo.bar.baz'
    handlers: [h1, h2]
~~~

（注：这里使用 YAML 是因为它的可读性比表示字典的等价 Python 源码形式更好。）

日志记录器 ID 就是日志记录器的名称，它会在程序中被用来获取对日志记录器的引用，例如 `foo.bar.baz`。 格式化器和过滤器的 ID 可以是任意字符串值 (例如上面的 `brief`, `precise`) 并且它们是瞬态的，因为它们仅对处理配置字典有意义并会被用来确定对象之间的连接，而当配置调用完成时不会在任何地方保留。

上面的代码片段指明名为 `foo.bar.baz` 的日志记录器应当关联到两个处理器，它们的 ID 是 `h1` 和 `h2`。 `h1` 的格式化器的 ID 是 `brief`，而 `h2` 的格式化器的 ID 是 `precise`。

### 用户定义对象¶

此架构支持用户定义对象作为处理器、过滤器和格式化器。 （日志记录器的不同实例不需要具有不同类型，因此这个配置架构并不支持用户定义日志记录器类。）

要配置的对象是由字典描述的，其中包含它们的配置详情。 在某些地方，日志记录系统将能够从上下文中推断出如何实例化一个对象，但是当要实例化一个用户自定义对象时，系统将不知道要如何做。 为了提供用户自定义对象实例化的完全灵活性，用户需要提供一个‘工厂’函数 —— 即在调用时传入配置字典并返回实例化对象的可调用对象。 这是用一个通过特殊键 `'()'` 来访问的工厂函数的绝对导入路径来标示的。 下面是一个实际的例子:

    
    
~~~
formatters:
  brief:
    format: '%(message)s'
  default:
    format: '%(asctime)s %(levelname)-8s %(name)-15s %(message)s'
    datefmt: '%Y-%m-%d %H:%M:%S'
  custom:
      (): my.package.customFormatterFactory
      bar: baz
      spam: 99.9
      answer: 42
~~~

上面的 YAML 代码片段定义了三个格式化器。 第一个的 ID 为 `brief`，是带有特殊格式字符串的标准 [`logging.Formatter`](3.标准库/logging.md#logging.Formatter "logging.Formatter") 实例。 第二个的 ID 为 `default`，具有更长的格式同时还显式地定义了时间格式，并将最终实例化一个带有这两个格式字符串的 [`logging.Formatter`](3.标准库/logging.md#logging.Formatter "logging.Formatter")。 以 Python 源代码形式显示的 `brief` 和 `default` 格式化器分别具有下列配置子字典:

    
    
~~~
{
  'format' : '%(message)s'
}
~~~

和:

    
    
~~~
{
  'format' : '%(asctime)s %(levelname)-8s %(name)-15s %(message)s',
  'datefmt' : '%Y-%m-%d %H:%M:%S'
}
~~~

并且由于这些字典不包含特殊键 `'()'`，实例化方式是从上下文中推断出来的：结果会创建标准的 [`logging.Formatter`](3.标准库/logging.md#logging.Formatter "logging.Formatter") 实例。 第三个格式器的 ID 为 `custom`，对应配置子字典为:

    
    
~~~
{
  '()' : 'my.package.customFormatterFactory',
  'bar' : 'baz',
  'spam' : 99.9,
  'answer' : 42
}
~~~

并且它包含特殊键 `'()'`，这意味着需要用户自定义实例化方式。 在此情况下，将使用指定的工厂可调用对象。 如果它本身就是一个可调用对象则将被直接使用 —— 否则如果你指定了一个字符串（如这个例子所示）则将使用正常的导入机制来定位实例的可调用对象。 调用该可调用对象将传入配置子字典中 **剩余的** 条目作为关键字参数。 在上面的例子中，调用将预期返回 ID 为 `custom` 的格式化器:

    
    
~~~
my.package.customFormatterFactory(bar='baz', spam=99.9, answer=42)
~~~

警告

上面示例中 `bar`, `spam` 和 `answer` 等键的值不应是配置目录或引用如 `cfg://foo` 或 `ext://bar`，因为它们将不会被配置机制所处理，而是被原样传给可调用对象。

将 `'()'` 用作特殊键是因为它不是一个有效的关键字形参名称，这样就不会与调用中使用的关键字参数发生冲突。 `'()'` 还被用作表明对应值为可调用对象的助记符。

> 在 3.11 版本发生变更: `handlers` 和 `loggers` 的 `filters` 成员除了 id 以外还能接受 filter 实例。

你还可以指定一个特殊键 `'.'` 其值应为一个将属性名称映射到对应值的字典。 如果找到，则指定的属性在被返回之前将在用户定义的对象上设置。 因此，使用以下配置:

    
    
~~~
{
  '()' : 'my.package.customFormatterFactory',
  'bar' : 'baz',
  'spam' : 99.9,
  'answer' : 42,
  '.' {
    'foo': 'bar',
    'baz': 'bozz'
  }
}
~~~

被返回的格式化器的 `foo` 属性将设为 `'bar'` 而 `baz` 属性将设为 `'bozz'`。

警告

上面示例中 `foo` 和 `baz` 等属性的值不应是配置目录或引用如 `cfg://foo` 或 `ext://bar`，因为它们将不会被配置机制所处理，而是被原样设置为属性。

### 处理句柄配置顺序¶

处理句柄按其键的字母顺序进行配置，而已配置的处理句柄将替换配置方案内部 `handlers` 字典（的一个工作副本）中的配置字典。 如果你使用 `cfg://handlers.foo` 这样的构造，那么在初始状态下 `handlers['foo']` 会指向名为 `foo` 的处理句柄的配置字典，随后（一旦配置了该处理句柄）它将指向已配置的处理句柄实例。 因此，`cfg://handlers.foo` 可以解析为一个字典或处理句柄实例。 通常来说，对于带依赖的处理句柄采用在它们所依赖的任何处理句柄完成配置 _之后_ 再进行配置的方式来命名处理句柄是一种明智的做法；这将允许使用 `cfg://handlers.foo` 这样的构造来配置依赖于处理句柄 `foo` 的处理句柄。 如果这个带依赖的处理句柄被命名为 `bar`，则会导致问题，因为 `bar` 的配置将在 `foo` 的配置之前被尝试使用，而 `foo` 将尚未配置完成。 但是，如果带依赖的处理句柄被命名为 `foobar`，则它将在 `foo` 之后被配置，结果就是 `cfg://handlers.foo` 将被解析为已配置的处理句柄 `foo`，而不是其配置字典。

### 访问外部对象¶

有时一个配置需要引用配置以外的对象，例如 `sys.stderr`。 如果配置字典是使用 Python 代码构造的，这会很直观，但是当配置是通过文本文件（例如 JSON, YAML）提供的时候就会引发问题。 在一个文本文件中，没有将 `sys.stderr` 与字符串字面值 `'sys.stderr'` 区分开来的标准方式。 为了实现这种区分，配置系统会在字符串值中查找规定的特殊前缀并对其做特殊处理。 例如，如果在配置中将字符串字面值 `'ext://sys.stderr'` 作为一个值来提供，则 `ext://` 将被去除而该值的剩余部分将使用正常导入机制来处理。

此类前缀的处理方式类似于协议处理：存在一种通用机制来查找与正则表达式 `^(?P<prefix>[a-z]+)://(?P<suffix>.*)$` 相匹配的前缀，如果识别出了 `prefix`，则 `suffix` 会以与前缀相对应的方式来处理并且处理的结果将替代原字符串值。 如果未识别出前缀，则原字符串将保持不变。

### 访问内部对象¶

除了外部对象，有时还需要引用配置中的对象。 这将由配置系统针对它所了解的内容隐式地完成。 例如，在日志记录器或处理器中表示 `level` 的字符串值 `'DEBUG'` 将被自动转换为值 `logging.DEBUG`，而 `handlers`, `filters` 和 `formatter` 条目将接受一个对象 ID 并解析为适当的目标对象。

但是，对于 [`logging`](3.标准库/logging.md#module-logging "logging: Flexible event logging system for applications.") 模块所不了解的用户自定义对象则需要一种更通用的机制。 例如，考虑 [`logging.handlers.MemoryHandler`](logging.handlers.md#logging.handlers.MemoryHandler "logging.handlers.MemoryHandler")，它接受一个 `target` 参数即其所委托的另一个处理器。 由于系统已经知道存在该类，因而在配置中，给定的 `target` 只需为相应目标处理器的的对象 ID 即可，而系统将根据该 ID 解析出处理器。 但是，如果用户定义了一个具有 `alternate` 处理器的 `my.package.MyHandler`，则配置程序将不知道 `alternate` 指向的是一个处理器。 为了应对这种情况，通用解析系统允许用户指定:

    
    
~~~
handlers:
  file:
    # configuration of file handler goes here

  custom:
    (): my.package.MyHandler
    alternate: cfg://handlers.file
~~~

字符串字面值 `'cfg://handlers.file'` 将按照与 `ext://` 前缀类似的方式被解析为结果字符串，但查找操作是在配置自身而不是在导入命名空间中进行。 该机制允许按点号或按索引来访问，与 `str.format` 所提供的方式类似。 这样，给定以下代码段:

    
    
~~~
handlers:
  email:
    class: logging.handlers.SMTPHandler
    mailhost: localhost
    fromaddr: my_app@domain.tld
    toaddrs:
      - support_team@domain.tld
      - dev_team@domain.tld
    subject: Houston, we have a problem.
~~~

in the configuration, the string `'cfg://handlers'` would resolve to the dict with key `handlers`, the string `'cfg://handlers.email` would resolve to the dict with key `email` in the `handlers` dict, and so on. The string `'cfg://handlers.email.toaddrs[1]` would resolve to `'dev_team@domain.tld'` and the string `'cfg://handlers.email.toaddrs[0]'` would resolve to the value `'support_team@domain.tld'`. The `subject` value could be accessed using either `'cfg://handlers.email.subject'` or, equivalently, `'cfg://handlers.email[subject]'`. The latter form only needs to be used if the key contains spaces or non-alphanumeric characters. Please note that the characters `[` and `]` are not allowed in the keys. If an index value consists only of decimal digits, access will be attempted using the corresponding integer value, falling back to the string value if needed.

给定字符串 `cfg://handlers.myhandler.mykey.123`，这将解析为 `config_dict['handlers']['myhandler']['mykey']['123']`。 如果字符串被指定为 `cfg://handlers.myhandler.mykey[123]`，系统将尝试从 `config_dict['handlers']['myhandler']['mykey'][123]` 中提取值，并在尝试失败时回退为 `config_dict['handlers']['myhandler']['mykey']['123']`。

### 导入解析与定制导入器¶

导入解析默认使用内置的 [`__import__()`](functions.md#import__ "__import__") 函数来执行导入。 你可能想要将其替换为你自己的导入机制：如果是这样的话，你可以替换 `DictConfigurator` 或其超类 `BaseConfigurator` 类的 `importer` 属性。 但是你必须小心谨慎，因为函数是从类中通过描述器方式来访问的。 如果你使用 Python 可调用对象来执行导入，并且你希望在类层级而不是在实例层级上定义它，则你需要用 [`staticmethod()`](functions.md#staticmethod "staticmethod") 来装饰它。 例如:

    
    
~~~
from importlib import import_module
from logging.config import BaseConfigurator

BaseConfigurator.importer = staticmethod(import_module)
~~~

如果你是在一个配置器的 _实例_ 上设置导入可调用对象则你不需要用 [`staticmethod()`](functions.md#staticmethod "staticmethod") 来装饰。

### 配置 QueueHandler 和 QueueListener¶

如果你想要配置一个 [`QueueHandler`](logging.handlers.md#logging.handlers.QueueHandler "logging.handlers.QueueHandler")，请注意它通常是与 [`QueueListener`](logging.handlers.md#logging.handlers.QueueListener "logging.handlers.QueueListener") 一起使用的，你可以同时配置这两者。 配置完成之后，`QueueListener` 实例将可作为所创建的处理句柄的 [`listener`](logging.handlers.md#logging.handlers.QueueHandler.listener "logging.handlers.QueueHandler.listener") 属性来访问，而你也将可以使用 [`getHandlerByName()`](3.标准库/logging.md#logging.getHandlerByName "logging.getHandlerByName") 来访问它并将你所使用的名称作为配置中的 `QueueHandler` 传入。 用于配置这两者的字典规格显示在下面的 YAML 实例代码段中。

    
    
~~~
handlers:
  qhand:
    class: logging.handlers.QueueHandler
    queue: my.module.queue_factory
    listener: my.package.CustomListener
    handlers:
      - hand_name_1
      - hand_name_2
      ...
~~~

`queue` 和 `listener` 键是可选的。

如果存在 `queue` 键，相应的值可以是下列几项之一:

  * 一个实际的 [`queue.Queue`](queue.md#queue.Queue "queue.Queue") 实例或其子类的实例。 这当然仅在你在代码中构造或修改了该配置字典时才是可能的。

  * 一个将被求值为可调用对象的字符串，当不带任何参数被调用时，该可调用对象将返回要使用的 [`queue.Queue`](queue.md#queue.Queue "queue.Queue") 实例。 该可调用对象可以是一个 [`queue.Queue`](queue.md#queue.Queue "queue.Queue") 子类或一个返回适当的队列实例的函数，如 `my.module.queue_factory()`。

  * 一个带有 `'()'` 键的字典，它使用 用户定义对象 中所介绍的通常方式构造。 这样构造的结果应当是一个 [`queue.Queue`](queue.md#queue.Queue "queue.Queue") 实例。

如果没有 `queue` 键，则会创建并使用一个标准的未绑定 [`queue.Queue`](queue.md#queue.Queue "queue.Queue") 实例。

如果存在 `listener` 键，则相应的值可以是下列几项中的一个:

  * 一个 [`logging.handlers.QueueListener`](logging.handlers.md#logging.handlers.QueueListener "logging.handlers.QueueListener") 的子类。 这当然仅在你通过代码构造或修改配置字典时才是可能的。

  * 一个将被求值为属于 `QueueListener` 的子类的类的字符串，例如 `'my.package.CustomListener'`。

  * 一个带有 `'()'` 键的字典，它使用 用户定义对象 中所介绍的通常方式构造。 这样构造的结果应当是一个与 `QueueListener` 初始化器具有相同签名的可调用对象。

如果不存在 `listener` 键，则会使用 [`logging.handlers.QueueListener`](logging.handlers.md#logging.handlers.QueueListener "logging.handlers.QueueListener")。

在 `handlers` 键之下的值是配置中其他处理句柄的名称（未显示在上面的代码片段中），它们将被传给队列监听器。

任何队列处理句柄和监听器类都需要定义为具有与 [`QueueHandler`](logging.handlers.md#logging.handlers.QueueHandler "logging.handlers.QueueHandler") 和 [`QueueListener`](logging.handlers.md#logging.handlers.QueueListener "logging.handlers.QueueListener") 相同的初始化签名。

在 3.12 版本加入.

## 配置文件格式¶

`fileConfig()` 所能理解的配置文件格式是基于 [`configparser`](configparser.md#module-configparser "configparser: Configuration file parser.") 功能的。 该文件必须包含 `[loggers]`, `[handlers]` 和 `[formatters]` 等小节，它们通过名称来标识文件中定义的每种类型的实体。 对于每个这样的实体，都有单独的小节来标识实体的配置方式。 因此，对于 `[loggers]` 小节中名为 `log01` 的日志记录器，相应的配置详情保存在 `[logger_log01]` 小节中。 类似地，对于 `[handlers]` 小节中名为 `hand01` 的处理器，其配置将保存在名为 `[handler_hand01]` 的小节中，而对于 `[formatters]` 小节中名为 `form01` 的格式化器，其配置将在名为 `[formatter_form01]` 的小节中指定。 根日志记录器的配置必须在名为 `[logger_root]` 的小节中指定。

备注

`fileConfig()` API 比 `dictConfig()` API 更旧因而没有提供涵盖日志记录特定方面的功能。 例如，你无法配置 [`Filter`](3.标准库/logging.md#logging.Filter "logging.Filter") 对象，该对象使用 `fileConfig()` 提供超出简单整数级别的消息过滤功能。 如果你想要在你的日志记录配置中包含 [`Filter`](3.标准库/logging.md#logging.Filter "logging.Filter") 的实例，你将必须使用 `dictConfig()`。 请注意未来还将向 `dictConfig()` 添加对配置功能的强化，因此值得考虑在方便的时候转换到这个新 API。

在文件中这些小节的例子如下所示。

    
    
~~~
[loggers]
keys=root,log02,log03,log04,log05,log06,log07

[handlers]
keys=hand01,hand02,hand03,hand04,hand05,hand06,hand07,hand08,hand09

[formatters]
keys=form01,form02,form03,form04,form05,form06,form07,form08,form09
~~~

根日志记录器必须指定一个级别和一个处理器列表。 根日志小节的例子如下所示。

    
    
~~~
[logger_root]
level=NOTSET
handlers=hand01
~~~

`level` 条目可以为 `DEBUG, INFO, WARNING, ERROR, CRITICAL` 或 `NOTSET` 之一。 作为仅适用于根日志记录器的设置，`NOTSET` 表示将会记录所有消息。 级别值会在 `logging` 包命名空间的上下文中 [进行求值](functions.md#func-eval)。

`handlers` 条目是以逗号分隔的处理器名称列表，它必须出现于 `[handlers]` 小节并且在配置文件中有相应的小节。

对于根日志记录器以外的日志记录器，还需要某些附加信息。 下面的例子演示了这些信息。

    
    
~~~
[logger_parser]
level=DEBUG
handlers=hand01
propagate=1
qualname=compiler.parser
~~~

`level` 和 `handlers` 条目的解释方式与根日志记录器的一致，不同之处在于如果一个非根日志记录器的级别被指定为 `NOTSET`，则系统会咨询更高层级的日志记录器来确定该日志记录器的有效级别。 `propagate` 条目设为 1 表示消息必须从此日志记录器传播到更高层级的处理器，设为 0 表示消息 **不会** 传播到更高层级的处理器。 `qualname` 条目是日志记录器的层级通道名称，也就是应用程序获取日志记录器所用的名称。

指定处理器配置的小节说明如下。

    
    
~~~
[handler_hand01]
class=StreamHandler
level=NOTSET
formatter=form01
args=(sys.stdout,)
~~~

`class` 条目指明处理器的类（由 `logging` 包命名空间中的 [`eval()`](functions.md#eval "eval") 来确定）。 `level` 会以与日志记录器相同的方式来解读，`NOTSET` 会被视为表示‘记录一切消息’。

`formatter` 条目指明此处理器的格式化器的键名称。 如为空白，则会使用默认的格式化器 (`logging._defaultFormatter`)。 如果指定了名称，则它必须出现于 `[formatters]` 小节并且在配置文件中有相应的小节。

`args` 条目，当在 `logging` 包命名空间的上下文中被 [求值](functions.md#func-eval) 时，将是传给处理句柄类构造器的参数列表。 请参阅相应处理句柄的构造器说明，或是下面的示例，以了解典型的条目是如何构造的。 如果未提供，则其默认值为 `()`。

可选的 `kwargs` 条目，当在 `logging` 包命名空间的上下文中被 [求值](functions.md#func-eval) 时，将是传给处理句柄的构造器的关键字参数字典。 如果未提供，则其默认值为 `{}`。

    
    
~~~
[handler_hand02]
class=FileHandler
level=DEBUG
formatter=form02
args=('python.log', 'w')

[handler_hand03]
class=handlers.SocketHandler
level=INFO
formatter=form03
args=('localhost', handlers.DEFAULT_TCP_LOGGING_PORT)

[handler_hand04]
class=handlers.DatagramHandler
level=WARN
formatter=form04
args=('localhost', handlers.DEFAULT_UDP_LOGGING_PORT)

[handler_hand05]
class=handlers.SysLogHandler
level=ERROR
formatter=form05
args=(('localhost', handlers.SYSLOG_UDP_PORT), handlers.SysLogHandler.LOG_USER)

[handler_hand06]
class=handlers.NTEventLogHandler
level=CRITICAL
formatter=form06
args=('Python Application', '', 'Application')

[handler_hand07]
class=handlers.SMTPHandler
level=WARN
formatter=form07
args=('localhost', 'from@abc', ['user1@abc', 'user2@xyz'], 'Logger Subject')
kwargs={'timeout': 10.0}

[handler_hand08]
class=handlers.MemoryHandler
level=NOTSET
formatter=form08
target=
args=(10, ERROR)

[handler_hand09]
class=handlers.HTTPHandler
level=NOTSET
formatter=form09
args=('localhost:9022', '/log', 'GET')
kwargs={'secure': True}
~~~

指定格式化器配置的小节说明如下。

    
    
~~~
[formatter_form01]
format=F1 %(asctime)s %(levelname)s %(message)s %(customfield)s
datefmt=
style=%
validate=True
defaults={'customfield': 'defaultvalue'}
class=logging.Formatter
~~~

用于格式化器配置的参数与字典规范 格式化器部分 中的键相同。

`defaults` 条目，当在 `logging` 包的命名空间的上下文中 [求值](functions.md#func-eval) 时，将是一个由自定义格式化字段的默认值组成的字典。 如果未提供，则默认为 `None`。

备注

由于如上所述使用了 [`eval()`](functions.md#eval "eval")，因此使用 `listen()` 通过套接字来发送和接收配置会导致潜在的安全风险。 此风险仅限于相互间没有信任的多个用户在同一台机器上运行代码的情况；请参阅 `listen()` 了解更多信息。

参见

模块 [`logging`](3.标准库/logging.md#module-logging "logging: Flexible event logging system for applications.")

    

~~~
日志记录模块的 API 参考。

[`logging.handlers`](logging.handlers.md#module-logging.handlers "logging.handlers: Handlers for the logging module.") 模块
~~~
    

~~~
