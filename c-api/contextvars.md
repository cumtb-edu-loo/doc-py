# 上下文变量对象¶

在 3.7.1 版本发生变更:

备注

在 Python 3.7.1 中，所有上下文变量 C API 的签名被 **更改** 为使用 [`PyObject`](structures.md#c.PyObject "PyObject") 指针而不是 `PyContext`, `PyContextVar` 以及 `PyContextToken`，例如:

    
    
~~~
// in 3.7.0:
PyContext *PyContext_New(void);

// in 3.7.1+:
PyObject *PyContext_New(void);
~~~

请参阅 [bpo-34762](https://bugs.python.org/issue?@action=redirect&bpo=34762) 了解详情。

在 3.7 版本加入.

本节深入介绍了 [`contextvars`](../library/contextvars.md#module-contextvars "contextvars: Context Variables") 模块的公用 C API。

type PyContext¶  

    

~~~
用于表示 [`contextvars.Context`](../library/contextvars.md#contextvars.Context "contextvars.Context") 对象的 C 结构体。

type PyContextVar¶  
~~~
    

~~~
用于表示 [`contextvars.ContextVar`](../library/contextvars.md#contextvars.ContextVar "contextvars.ContextVar") 对象的 C 结构体。

type PyContextToken¶  
~~~
    

~~~
用于表示 [`contextvars.Token`](../library/contextvars.md#contextvars.Token "contextvars.Token") 对象的 C 结构体。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyContext_Type¶  
~~~
    

~~~
表示 _context_ 类型的类型对象。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyContextVar_Type¶  
~~~
    

~~~
表示 _context variable_ 类型的类型对象。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyContextToken_Type¶  
~~~
    

~~~
表示 _context variable token_ 类型的类型对象。

类型检查宏：

int PyContext_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果 _o_ 的类型为 `PyContext_Type` 则返回真值。 _o_ 必须不为 `NULL`。 此函数总是会成功执行。

int PyContextVar_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果 _o_ 的类型为 `PyContextVar_Type` 则返回真值。 _o_ 必须不为 `NULL`。 此函数总是会成功执行。

int PyContextToken_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果 _o_ 的类型为 `PyContextToken_Type` 则返回真值。 _o_ 必须不为 `NULL`。 此函数总是会成功执行。

上下文对象管理函数:

[PyObject](structures.md#c.PyObject "PyObject") *PyContext_New(void)¶  

    _返回值：新的引用。_

创建一个新的空上下文对象。 如果发生错误则返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyContext_Copy([PyObject](structures.md#c.PyObject "PyObject") *ctx)¶  

    _返回值：新的引用。_

创建所传入的 _ctx_ 上下文对象的浅拷贝。 如果发生错误则返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyContext_CopyCurrent(void)¶  

    _返回值：新的引用。_

创建当前线程上下文的浅拷贝。 如果发生错误则返回 `NULL`。

int PyContext_Enter([PyObject](structures.md#c.PyObject "PyObject") *ctx)¶  
~~~
    

~~~
将 _ctx_ 设为当前线程的当前上下文。 成功时返回 `0`，出错时返回 `-1`。

int PyContext_Exit([PyObject](structures.md#c.PyObject "PyObject") *ctx)¶  
~~~
    

~~~
取消激活 _ctx_ 上下文并将之前的上下文恢复为当前线程的当前上下文。 成功时返回 `0`，出错时返回 `-1`。

上下文变量函数:

[PyObject](structures.md#c.PyObject "PyObject") *PyContextVar_New(const char *name, [PyObject](structures.md#c.PyObject "PyObject") *def)¶  

    _返回值：新的引用。_

创建一个新的 `ContextVar` 对象。 形参 _name_ 用于自我检查和调试目的。 形参 _def_ 为上下文变量指定默认值，或为 `NULL` 表示无默认值。 如果发生错误，这个函数会返回 `NULL`。

int PyContextVar_Get([PyObject](structures.md#c.PyObject "PyObject") *var, [PyObject](structures.md#c.PyObject "PyObject") *default_value, [PyObject](structures.md#c.PyObject "PyObject") **value)¶  
~~~
    

~~~
获取上下文变量的值。如果在查找过程中发生错误，返回' ' -1 ' '，如果没有发生错误，无论是否找到值，都返回' ' 0 ' '，

如果找到上下文变量， _value_ 将是指向它的指针。 如果上下文变量 _没有_ 找到， _value_ 将指向：

  * _default_value_ ，如果非 `NULL`;

  * _var_ 的默认值，如果不是 `NULL`；

  * `NULL`

除了返回 `NULL`，这个函数会返回一个新的引用。

[PyObject](structures.md#c.PyObject "PyObject") *PyContextVar_Set([PyObject](structures.md#c.PyObject "PyObject") *var, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  

    _返回值：新的引用。_

在当前上下文中将 _var_ 设为 _value_ 。 返回针对此修改的新凭据对象，或者如果发生错误则返回 `NULL`。

int PyContextVar_Reset([PyObject](structures.md#c.PyObject "PyObject") *var, [PyObject](structures.md#c.PyObject "PyObject") *token)¶  
~~~
    

~~~
