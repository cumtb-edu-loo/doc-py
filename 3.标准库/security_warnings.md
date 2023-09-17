# 安全考量¶

下列模块具有专门的安全事项:

  * [`base64`](base64.md#module-base64 "base64: RFC 4648: Base16, Base32, Base64 Data Encodings; Base85 and Ascii85"): [base64 安全事项](base64.md#base64-security)，参见 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.md)

  * [`hashlib`](hashlib.md#module-hashlib "hashlib: Secure hash and message digest algorithms."): [所有构造器都接受一个 "usedforsecurity" 仅限关键字参数以停用已知的不安全和已封禁的算法](hashlib.md#hashlib-usedforsecurity)

  * [`http.server`](http.server.md#module-http.server "http.server: HTTP server and request handlers.") 不适合生产用途，只实现了基本的安全检查。 请参阅 [安全性考量](http.server.md#http-server-security)。

  * [`logging`](3.标准库/logging.md#module-logging "logging: Flexible event logging system for applications."): [日志记录配置使用了 eval()](logging.config.md#logging-eval-security)

  * [`multiprocessing`](multiprocessing.md#module-multiprocessing "multiprocessing: Process-based parallelism."): [Connection.recv() 使用了 pickle](multiprocessing.md#multiprocessing-recv-pickle-security)

  * [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back."): [在 pickle 中限制全局变量](pickle.md#pickle-restrict)

  * [`random`](random.md#module-random "random: Generate pseudo-random numbers with various common distributions.") 不应当被用于安全目的，而应改用 [`secrets`](secrets.md#module-secrets "secrets: Generate secure random numbers for managing secrets.")

  * [`shelve`](shelve.md#module-shelve "shelve: Python object persistence."): [shelve 是基于 pickle 的因此不适用于处理不受信任的源](shelve.md#shelve-security)

  * [`ssl`](ssl.md#module-ssl "ssl: TLS/SSL wrapper for socket objects"): [SSL/TLS 安全事项](ssl.md#ssl-security)

  * [`subprocess`](subprocess.md#module-subprocess "subprocess: Subprocess management."): [子进程安全事项](subprocess.md#subprocess-security)

  * [`tempfile`](tempfile.md#module-tempfile "tempfile: Generate temporary files and directories."): [mktemp 由于存在竞争条件缺陷已被弃用](tempfile.md#tempfile-mktemp-deprecated)

  * [`xml`](xml.md#module-xml "xml: Package containing XML processing modules"): [XML 安全缺陷](xml.md#xml-vulnerabilities)

  * [`zipfile`](zipfile.md#module-zipfile "zipfile: Read and write ZIP-format archive files."): [恶意处理的 .zip 文件可能导致硬盘空间耗尽](zipfile.md#zipfile-resources-limitations)

[`-I`](1.%20命令行与环境.md#cmdoption-I) 命令行选项可被用来在隔离模式下运行 Python。 当它无法使用时，可以使用 [`-P`](1.%20命令行与环境.md#cmdoption-P) 选项或 [`PYTHONSAFEPATH`](1.%20命令行与环境.md#envvar-PYTHONSAFEPATH) 环境变量以避免在 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 中预置一个潜在的不安全路径，如当前目录、脚本的目录或一个空字符串。

