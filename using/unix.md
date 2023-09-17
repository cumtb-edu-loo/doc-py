# 2\. 在类Unix环境下使用Python¶

## 2.1. 获得并安装Python的最新版本¶

### 2.1.1. 在Linux中¶

Python预装在大多数Linux发行版上，并作为一个包提供给所有其他用户。 但是，您可能想要使用的某些功能在发行版提供的软件包中不可用。这时您可以从源代码轻松编译最新版本的Python。

如果Python没有预先安装并且不在发行版提供的库中，您可以轻松地为自己使用的发行版创建包。 参阅以下链接：

参见

<https://www.debian.org/doc/manuals/maint-guide/first.en.md>

    

~~~
对于Debian用户

<https://en.opensuse.org/Portal:Packaging>
~~~
    

~~~
对于OpenSuse用户

<https://docs-old.fedoraproject.org/en-US/Fedora_Draft_Documentation/0.1/html/RPM_Guide/ch-creating-rpms.md>
~~~
    

~~~
对于Fedora用户

<http://www.slackbook.org/html/package-management-making-packages.md>
~~~
    

~~~
对于Slackware用户

### 2.1.2. 在FreeBSD和OpenBSD上¶

  * FreeBSD用户，使用以下命令添加包:
    
        pkg install python3
~~~

  * OpenBSD用户，使用以下命令添加包:
    
        pkg_add -r python
    
    pkg_add ftp://ftp.openbsd.org/pub/OpenBSD/4.2/packages/<insert your architecture here>/python-<version>.tgz
    

例如：i386用户获取Python 2.5.1的可用版本:

    
        pkg_add ftp://ftp.openbsd.org/pub/OpenBSD/4.2/packages/i386/python-2.5.1p2.tgz
    

## 2.2. 构建Python¶

如果你想自己编译 CPython，首先要做的是获取 [源代码](https://www.python.org/downloads/source/)。 你可以下载最新发布版的 source 或是直接抓取最新的 [clone](https://devguide.python.org/setup/#get-the-source-code)。 （如果你想要贡献补丁，那么你就必须先 clone。）

构建过程由常用命令组成：

    
    
~~~
./configure
make
make install
~~~

特定 Unix 平台的 [配置选项](configure.md#configure-options) 和注意事项通常会详细地记录在 Python 源代码树的根目录下的 [README.rst](https://github.com/python/cpython/tree/3.12/README.rst) 文件中。

警告

`make install` 可以覆盖或伪装 `python3` 二进制文件。因此，建议使用 `make altinstall` 而不是 `make install` ，因为后者只安装了 `_exec_prefix_ /bin/python _version_` 。

## 2.3. 与Python相关的路径和文件¶

这些取决于本机安装惯例的不同；[`prefix`](configure.md#cmdoption-prefix) 和 [`exec_prefix`](configure.md#cmdoption-exec-prefix) 依赖于具体安装并且应当被解读为针对 GNU 软件；它们可能具有相同的含义。

例如，在大多数Linux系统上，两者的默认值是 `/usr` 。

文件/目录

|

含意  
  
---|---  
  
`_exec_prefix_ /bin/python3`

|

解释器的推荐位置  
  
`_prefix_ /lib/python _version_`, `_exec_prefix_ /lib/python _version_`

|

包含标准模块的目录的推荐位置  
  
`_prefix_ /include/python _version_`, `_exec_prefix_ /include/python _version_`

|

包含开发Python扩展和嵌入解释器所需的include文件的目录的推荐位置  
  
## 2.4. 杂项¶

要在Unix上使用Python脚本，需要添加可执行权限，例如：

    
    
~~~
$ chmod +x script
~~~

并在脚本的顶部放置一个合适的Shebang线。一个很好的选择通常是:

    
    
~~~
#!/usr/bin/env python3
~~~

将在整个 `PATH` 中搜索Python解释器。但是，某些Unix系统可能没有 **env** 命令，因此可能需要将 `/usr/bin/python3` 硬编码为解释器路径。

要在Python脚本中使用shell命令，请查看 [`subprocess`](../library/subprocess.md#module-subprocess "subprocess: Subprocess management.") 模块。

## 2.5. 自定义 OpenSSL¶

  1. 要使用发行商的 OpenSSL 配置和系统信任存储库，请找到包含 `openssl.cnf` 文件或符号链接的目录，它位于 `/etc` 中。 在大多数发行版上该文件是在 `/etc/ssl` 或者 `/etc/pki/tls` 中。 该目录还应当包含一个 `cert.pem` 文件和/或一个 `certs` 目录。
    
        $ find /etc/ -name openssl.cnf -printf "%h\n"
    /etc/ssl
    

  2. 下载、编译并安装 OpenSSL。 请确保你使用 `install_sw` 而不是 `install`。 `install_sw` 的目标不会覆盖 `openssl.cnf`。
    
        $ curl -O https://www.openssl.org/source/openssl-VERSION.tar.gz
    $ tar xzf openssl-VERSION
    $ pushd openssl-VERSION
    $ ./config \
        --prefix=/usr/local/custom-openssl \
        --libdir=lib \
        --openssldir=/etc/ssl
    $ make -j1 depend
    $ make -j8
    $ make install_sw
    $ popd
    

  3. 使用自定义的 OpenSSL 编译 Python （参考配置 `--with-openssl` 和 `--with-openssl-rpath` 选项）
    
        $ pushd python-3.x.x
    $ ./configure -C \
        --with-openssl=/usr/local/custom-openssl \
        --with-openssl-rpath=auto \
        --prefix=/usr/local/python-3.x.x
    $ make -j8
    $ make altinstall
    

备注

OpenSSL 的补丁发布版具有向下兼容的 ABI。 你不需要重新编译 Python 来更新 OpenSSL。 使用一个新的版本来替代自定义 OpenSSL 安装版就可以了。

