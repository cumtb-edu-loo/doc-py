# `pwd` \--- 用户密码数据库¶

* * *

此模块可以访问 Unix 用户账户名及密码数据库，在所有 Unix 版本上均可使用。

[可用性](3.标准库/intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](3.标准库/intro.md#wasm-availability) 了解详情。

密码数据库中的条目以元组对象返回，属性对应 `passwd` 中的结构（属性如下所示，可参考 `<pwd.h>`）：

索引

|

属性

|

含意  
  
---|---|---  
  
0

|

`pw_name`

|

登录名  
  
1

|

`pw_passwd`

|

密码，可能已经加密  
  
2

|

`pw_uid`

|

用户 ID 数值  
  
3

|

`pw_gid`

|

组 ID 数值  
  
4

|

`pw_gecos`

|

用户名或备注  
  
5

|

`pw_dir`

|

用户主目录  
  
6

|

`pw_shell`

|

用户的命令解释器  
  
其中 uid 和 gid 是整数，其他是字符串，如果找不到对应的项目，抛出 [`KeyError`](3.标准库/exceptions.md#KeyError "KeyError") 异常。

备注

In traditional Unix the field `pw_passwd` usually contains a password encrypted with a DES derived algorithm. However most modern unices use a so-called _shadow password_ system. On those unices the _pw_passwd_ field only contains an asterisk (`'*'`) or the letter `'x'` where the encrypted password is stored in a file `/etc/shadow` which is not world readable. Whether the _pw_passwd_ field contains anything useful is system-dependent.

本模块定义如下内容：

pwd.getpwuid( _uid_ )¶

    

~~~
给定用户的数值 ID，返回密码数据库的对应项目。

pwd.getpwnam( _name_ )¶
~~~
    

~~~
给定用户名，返回密码数据库的对应项目。

pwd.getpwall()¶
~~~
    

~~~
返回密码数据库中所有项目的列表，顺序不是固定的。

参见

模块 [`grp`](grp.md#module-grp "grp: The group database \(getgrnam\(\) and friends\). \(Unix\)")
~~~
    

~~~
