# `contextvars` \--- 上下文变量¶

* * *

本模块提供了相关API用于管理、存储和访问上下文相关的状态。 `ContextVar` 类用于声明 _上下文变量_ 并与其一起使用。函数 `copy_context()` 和类 `Context` 用于管理当前上下文和异步框架中。

在多并发环境中，有状态上下文管理器应该使用上下文变量，而不是 [`threading.local()`](threading.md#threading.local "threading.local") 来防止他们的状态意外泄露到其他代码。

更多信息参见 [**PEP 567**](https://peps.python.org/pep-0567/) 。

在 3.7 版本加入.

## 上下文变量¶

_class _contextvars.ContextVar( _name_ [, _*_ , _default_ ])¶

    

~~~
此类用于声明一个新的上下文变量，如:
~~~
    
    
~~~
var: ContextVar[int] = ContextVar('var', default=42)
~~~

_name_ 参数用于内省和调试，必需。

调用 `ContextVar.get()` 时，如果上下文中没有找到此变量的值，则返回可选的仅命名参数 _default_ 。

**重要：** 上下文变量应该在顶级模块中创建，且永远不要在闭包中创建。 `Context` 对象拥有对上下文变量的强引用，这可以让上下文变量被垃圾收集器正确回收。

name¶

    

~~~
上下文变量的名称，只读属性。

在 3.7.1 版本加入.

get([ _default_ ])¶
~~~
    

~~~
返回当前上下文中此上下文变量的值。

如果当前上下文中此变量没有值，则此方法会:

  * 如果提供了 _default_ ，返回其值；或者

  * 返回上下文变量本身的默认值， 如果创建此上下文变量时提供了默认值；或者

  * 抛出 [`LookupError`](exceptions.md#LookupError "LookupError") 异常。

set( _value_ )¶
~~~
    

~~~
调用此方法设置上下文变量在当前上下文中的值。

必选参数 _value_ 是上下文变量的新值。

返回一个 `Token` 对象，可通过 `ContextVar.reset()` 方法将上下文变量还原为之前某个状态。

reset( _token_ )¶
~~~
    

~~~
将上下文变量重置为调用 `ContextVar.set()` 之前、创建 _token_ 时候的状态。

例如：
~~~
    
    
~~~
var = ContextVar('var')

token = var.set('new value')
# code that uses 'var'; var.get() returns 'new value'.
var.reset(token)

# After the reset call the var has no value again, so
# var.get() would raise a LookupError.
~~~

_class _contextvars.Token¶

    

~~~
`ContextVar.set()` 方法返回 _Token_ 对象。此对象可以传递给 `ContextVar.reset()` 方法用于将上下文变量还原为调用 _set_ 前的状态。

var¶
~~~
    

~~~
只读属性。指向创建此 token 的 `ContextVar` 对象。

old_value¶
~~~
    

~~~
一个只读属性。 会被设为在创建此令牌的 `ContextVar.set()` 方法调用之前该变量所具有的值。 如果调用之前变量没有设置值则它会指令 `Token.MISSING`。

MISSING¶
~~~
    

~~~
`Token.old_value` 会用到的一个标记对象。

## 手动上下文管理¶

contextvars.copy_context()¶
~~~
    

~~~
返回当前上下文中 `Context` 对象的拷贝。

以下代码片段会获取当前上下文的拷贝并打印设置到其中的所有变量及其值:
~~~
    
    
~~~
ctx: Context = copy_context()
print(list(ctx.items()))
~~~

此函数复杂度为 O(1) ，也就是说对于只包含几个上下文变量和很多上下文变量的情况，他们是一样快的。

_class _contextvars.Context¶

    

~~~
`ContextVars` 与其值的映射。

`Context()` 创建一个不包含任何值的空上下文。如果要获取当前上下文的拷贝，使用 `copy_context()` 函数。

每个线程将有一个不同的最高层级 `Context` 对象。 这意味着当在不同的线程中赋值时 `ContextVar` 对象的行为方式与 [`threading.local()`](threading.md#threading.local "threading.local") 类似。

Context 实现了 [`collections.abc.Mapping`](collections.abc.md#collections.abc.Mapping "collections.abc.Mapping") 接口。

run( _callable_ , _* args_, _** kwargs_)¶
~~~
    

~~~
按照 _run_ 方法中的参数在上下文对象中执行 `callable(*args, **kwargs)` 代码。返回执行结果，如果发生异常，则将异常透传出来。

_callable_ 对上下文变量所做的任何修改都会保留在上下文对象中:
~~~
    
    
~~~
var = ContextVar('var')
var.set('spam')

def main():
    # 'var' was set to 'spam' before
    # calling 'copy_context()' and 'ctx.run(main)', so:
    # var.get() == ctx[var] == 'spam'

    var.set('ham')

    # Now, after setting 'var' to 'ham':
    # var.get() == ctx[var] == 'ham'

ctx = copy_context()

# Any changes that the 'main' function makes to 'var'
# will be contained in 'ctx'.
ctx.run(main)

# The 'main()' function was run in the 'ctx' context,
# so changes to 'var' are contained in it:
# ctx[var] == 'ham'

# However, outside of 'ctx', 'var' is still set to 'spam':
# var.get() == 'spam'
~~~

当在多个系统线程或者递归调用同一个上下文对象的此方法，抛出 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError") 异常。

copy()¶

    

~~~
返回此上下文对象的浅拷贝。

var in context
~~~
    

~~~
如果 _context_ 中含有名称为 _var_ 的变量，返回 `True`， 否则返回 `False`。

context[var]
~~~
    

~~~
返回名称为 _var_ 的 `ContextVar` 变量。如果上下文对象中不包含这个变量，则抛出 [`KeyError`](exceptions.md#KeyError "KeyError") 异常。

get( _var_ [, _default_ ])¶
~~~
    

~~~
如果 _var_ 在上下文对象中具有值则返回 _var_ 的值。 在其他情况下返回 _default_ 。 如果未给出 _default_ 则返回 `None`。

iter(context)
~~~
    

~~~
返回一个存储在上下文对象中的变量的迭代器。

len(proxy)
~~~
    

~~~
返回上下文对象中所设的变量的数量。

keys()¶
~~~
    

~~~
返回上下文对象中的所有变量的列表。

values()¶
~~~
    

~~~
返回上下文对象中所有变量值的列表。

items()¶
~~~
    

~~~
返回包含上下文对象中所有变量及其值的 2 元组的列表。

## asyncio 支持¶

上下文变量在 [`asyncio`](asyncio.md#module-asyncio "asyncio: Asynchronous I/O.") 中有原生的支持并且无需任何额外配置即可被使用。 例如，以下是一个简单的回显服务器，它使用上下文变量来让远程客户端的地址在处理该客户端的 Task 中可用:
~~~
    
    
~~~
import asyncio
import contextvars

client_addr_var = contextvars.ContextVar('client_addr')

def render_goodbye():
    # The address of the currently handled client can be accessed
    # without passing it explicitly to this function.

    client_addr = client_addr_var.get()
    return f'Good bye, client @ {client_addr}\n'.encode()

async def handle_request(reader, writer):
    addr = writer.transport.get_extra_info('socket').getpeername()
    client_addr_var.set(addr)

    # In any code that we call is now possible to get
    # client's address by calling 'client_addr_var.get()'.

    while True:
        line = await reader.readline()
        print(line)
        if not line.strip():
            break
        writer.write(line)

    writer.write(render_goodbye())
    writer.close()

async def main():
    srv = await asyncio.start_server(
        handle_request, '127.0.0.1', 8081)

    async with srv:
        await srv.serve_forever()

asyncio.run(main())

# To test it you can use telnet:
#     telnet 127.0.0.1 8081
~~~
