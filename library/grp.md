# `grp` \--- 组数据库¶

* * *

该模块提供对Unix组数据库的访问。 它在所有Unix版本上都可用。

[可用性](intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

组数据库条目被报告为类似元组的对象，其属性对应于 `group` 结构的成员 (下面的属性字段，请参见 `<grp.h>`):

索引

|

属性

|

含意  
  
---|---|---  
  
0

|

gr_name

|

组名  
  
1

|

gr_passwd

|

（加密的）组密码； 通常为空  
  
2

|

gr_gid

|

数字组ID  
  
3

|

gr_mem

|

组内所有成员的用户名  
  
gid 是整数，名称和密码是字符串，成员列表是字符串列表。 （注意，大多数用户未根据密码数据库显式列为所属组的成员。请检查两个数据库以获取完整的成员资格信息。还要注意，以 `+` 或 `-` 开头的 `gr_name` 可能是 YP/NIS 引用，可能无法通过 `getgrnam()` 或 `getgrgid()` 访问。）

本模块定义如下内容：

grp.getgrgid( _id_ )¶

    

~~~
返回给定数字组 ID 的组数据库条目。 如果请求的条目无法找到则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

在 3.10 版本发生变更: 对于非整数参数如浮点数或字符串将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

grp.getgrnam( _name_ )¶
~~~
    

~~~
返回给定组名的组数据库条目。 如果找不到要求的条目，则会引发 [`KeyError`](exceptions.md#KeyError "KeyError") 错误。

grp.getgrall()¶
~~~
    

~~~
以任意顺序返回所有可用组条目的列表。

参见

模块 [`pwd`](pwd.md#module-pwd "pwd: The password database \(getpwnam\(\) and friends\). \(Unix\)")
~~~
    

~~~
