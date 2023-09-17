# `unittest.mock` \--- 模拟对象库¶

在 3.3 版本加入.

**源代码：** [Lib/unittest/mock.py](https://github.com/python/cpython/tree/3.12/Lib/unittest/mock.py)

* * *

`unittest.mock` 是一个用于测试的Python库。它允许使用模拟对象来替换受测系统的一些部分，并对这些部分如何被使用进行断言判断。

`unittest.mock` 提供的 `Mock` 类，能在整个测试套件中模拟大量的方法。创建后，就可以断言调用了哪些方法/属性及其参数。还可以以常规方式指定返回值并设置所需的属性。

此外，mock 还提供了用于修补测试范围内模块和类级别属性的 `patch()` 装饰器，和用于创建独特对象的 `sentinel` 。 阅读 quick guide 中的案例了解如何使用 `Mock` ，`MagicMock` 和 `patch()` 。

mock 被设计为配合 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 使用且它是基于 'action -> assertion' 模式而非许多模拟框架所使用的 'record -> replay' 模式。

在 Python 的早期版本要单独使用 `unittest.mock` ，在 [PyPI 获取 mock](https://pypi.org/project/mock) 。

## 快速上手¶

当您访问对象时， `Mock` 和 `MagicMock` 将创建所有属性和方法，并保存他们在使用时的细节。你可以通过配置，指定返回值或者限制可访问属性，然后断言他们如何被调用：

    
    
~~~shell
>>> from unittest.mock import MagicMock
>>> thing = ProductionClass()
>>> thing.method = MagicMock(return_value=3)
>>> thing.method(3, 4, 5, key='value')
3
>>> thing.method.assert_called_with(3, 4, 5, key='value')
~~~

通过 `side_effect` 设置副作用(side effects) ，可以是一个 mock 被调用时抛出的异常：

    
    
~~~shell
>>> from unittest.mock import Mock
>>> mock = Mock(side_effect=KeyError('foo'))
>>> mock()
Traceback (most recent call last):
 ...
KeyError: 'foo'
~~~
    
    
~~~shell
>>> values = {'a': 1, 'b': 2, 'c': 3}
>>> def side_effect(arg):
...     return values[arg]
...
>>> mock.side_effect = side_effect
>>> mock('a'), mock('b'), mock('c')
(1, 2, 3)
>>> mock.side_effect = [5, 4, 3, 2, 1]
>>> mock(), mock(), mock()
(5, 4, 3)
~~~

Mock 还可以通过其他方法配置和控制其行为。例如 mock 可以通过设置 _spec_ 参数来从一个对象中获取其规格(specification)。如果访问 mock 的属性或方法不在 spec 中，会报 [`AttributeError`](exceptions.md#AttributeError "AttributeError") 错误。

使用 `patch()` 装饰去/上下文管理器，可以更方便地测试一个模块下的类或对象。你指定的对象会在测试过程中替换成 mock （或者其他对象），测试结束后恢复。

    
    
~~~shell
>>> from unittest.mock import patch
>>> @patch('module.ClassName2')
... @patch('module.ClassName1')
... def test(MockClass1, MockClass2):
...     module.ClassName1()
...     module.ClassName2()
...     assert MockClass1 is module.ClassName1
...     assert MockClass2 is module.ClassName2
...     assert MockClass1.called
...     assert MockClass2.called
...
>>> test()
~~~

备注

当你嵌套 patch 装饰器时，mock 将以执行顺序传递给装饰器函数（ _Python_ 装饰器正常顺序）。由于从下至上，因此在上面的示例中，首先 mock 传入的 `module.ClassName1` 。

在查找对象的名称空间中修补对象使用 `patch()` 。使用起来很简单，阅读 在哪里打补丁 来快速上手。

`patch()` 也可以 with 语句中使用上下文管理。

    
    
~~~shell
>>> with patch.object(ProductionClass, 'method', return_value=None) as mock_method:
...     thing = ProductionClass()
...     thing.method(1, 2, 3)
...
>>> mock_method.assert_called_once_with(1, 2, 3)
~~~

还有一个 `patch.dict()` 用于在一定范围内设置字典中的值，并在测试结束时将字典恢复为其原始状态：

    
    
~~~shell
>>> foo = {'key': 'value'}
>>> original = foo.copy()
>>> with patch.dict(foo, {'newkey': 'newvalue'}, clear=True):
...     assert foo == {'newkey': 'newvalue'}
...
>>> assert foo == original
~~~

Mock支持 Python 魔术方法 。使用模式方法最简单的方式是使用 `MagicMock` class. 。它可以做如下事情：

    
    
~~~shell
>>> mock = MagicMock()
>>> mock.__str__.return_value = 'foobarbaz'
>>> str(mock)
'foobarbaz'
>>> mock.__str__.assert_called_with()
~~~

Mock 能指定函数（或其他 Mock 实例）为魔术方法，它们将被适当地调用。 `MagicMock` 是预先创建了所有魔术方法（所有有用的方法） 的 Mock 。

下面是一个使用了普通 Mock 类的魔术方法的例子

    
    
~~~shell
>>> mock = Mock()
>>> mock.__str__ = Mock(return_value='wheeeeee')
>>> str(mock)
'wheeeeee'
~~~

使用 auto-speccing 可以保证测试中的模拟对象与要替换的对象具有相同的api 。在 patch 中可以通过 _autospec_ 参数实现自动推断，或者使用 `create_autospec()` 函数。自动推断会创建一个与要替换对象相同的属相和方法的模拟对象，并且任何函数和方法（包括构造函数）都具有与真实对象相同的调用签名。

这么做是为了因确保不当地使用 mock 导致与生产代码相同的失败：

    
    
~~~shell
>>> from unittest.mock import create_autospec
>>> def function(a, b, c):
...     pass
...
>>> mock_function = create_autospec(function, return_value='fishy')
>>> mock_function(1, 2, 3)
'fishy'
>>> mock_function.assert_called_once_with(1, 2, 3)
>>> mock_function('wrong arguments')
Traceback (most recent call last):
 ...
TypeError: missing a required argument: 'b'
~~~

在类中使用 `create_autospec()` 时，会复制 `__init__` 方法的签名，另外在可调用对象上使用时，会复制 `__call__` 方法的签名。

## Mock 类¶

`Mock` 是一个可以灵活的替换存根 (stubs) 的对象，可以测试所有代码。 Mock 是可调用的，在访问其属性时创建一个新的 mock [1] 。访问相同的属性只会返回相同的 mock 。 Mock 会保存调用记录，可以通过断言获悉代码的调用。

`MagicMock` 是 `Mock` 的子类，它有所有预创建且可使用的魔术方法。在需要模拟不可调用对象时，可以使用 `NonCallableMock` 和 `NonCallableMagicMock`

`patch()` 装饰器使得用 `Mock` 对象临时替换特定模块中的类非常方便。 默认情况下 `patch()` 将为你创建一个 `MagicMock`。 你可以使用 `patch()` 的 _new_callable_ 参数指定替代 `Mock` 的类。

_class _unittest.mock.Mock( _spec =None_, _side_effect =None_, _return_value =DEFAULT_, _wraps =None_, _name =None_, _spec_set =None_, _unsafe =False_, _** kwargs_)¶

    

~~~
创建一个新的 `Mock` 对象。通过可选参数指定 `Mock` 对象的行为：

  * _spec_ : 可以是要给字符串列表，也可以是充当模拟对象规范的现有对象（类或实例）。如果传入一个对象，则通过在该对象上调用 dir 来生成字符串列表（不支持的魔法属性和方法除外）。访问不在此列表中的任何属性都将触发 [`AttributeError`](exceptions.md#AttributeError "AttributeError") 。

如果 _spec_ 是一个对象（而不是字符串列表），则 [`__class__`](stdtypes.md#instance.__class__ "instance.__class__") 返回 _spec_ 对象的类。 这允许模拟程序通过 [`isinstance()`](functions.md#isinstance "isinstance") 测试。

  * _spec_set_ ： _spec_ 的更严格的变体。如果使用了该属性，尝试模拟 _set_ 或 _get_ 的属性不在 _spec_set_ 所包含的对象中时，会抛出 [`AttributeError`](exceptions.md#AttributeError "AttributeError") 。

  * _side_effect_ ：每当调用 Mock 时都会调用的函数。 参见 `side_effect` 属性。 对于引发异常或动态更改返回值很有用。 该函数使用与 mock 函数相同的参数调用，并且除非返回 `DEFAULT` ，否则该函数的返回值将用作返回值。

另外， _side_effect_ 可以是异常类或实例。 此时，调用模拟程序时将引发异常。

如果 _side_effect_ 是可迭代对象，则每次调用 mock 都将返回可迭代对象的下一个值。

设置 _side_effect_ 为 `None` 即可清空。

  * _return_value_ ：调用 mock 的返回值。 默认情况下，是一个新的Mock（在首次访问时创建）。 参见 `return_value` 属性 。

  * _unsafe_ : 在默认情况下，访问任何名字以 _assert_ , _assret_ , _asert_ , _aseert_ 或 _assrt_ 开头的属性都将引发 [`AttributeError`](exceptions.md#AttributeError "AttributeError")。 传入 `unsafe=True` 将允许访问这些属性。

在 3.5 版本加入.

  * _wraps_ ：要包装的 mock 对象。 如果 _wraps_ 不是 `None` ，那么调用 Mock 会将调用传递给 _wraps_ 的对象（返回实际结果）。 对模拟的属性访问将返回一个 Mock 对象，该对象包装了 _wraps_ 对象的相应属性（因此，尝试访问不存在的属性将引发 [`AttributeError`](exceptions.md#AttributeError "AttributeError") ）。

如果明确指定 _return_value_ ，则调用时不会返回包装对象，而是返回 _return_value_ 。

  * _name_ ：mock 的名称。 在调试时很有用。 名称会传递到子 mock 。

还可以使用任意关键字参数来调用 mock 。 创建模拟后，将使用这些属性来设置 mock 的属性。 有关详细信息，请参见 `configure_mock()` 方法。

assert_called()¶
~~~
    

~~~
断言 mock 已被调用至少一次。
~~~
    
    
~~~shell
>>> mock = Mock()
>>> mock.method()
<Mock name='mock.method()' id='...'>
>>> mock.method.assert_called()
~~~

在 3.6 版本加入.

assert_called_once()¶

    

~~~
断言 mock 已被调用恰好一次。
~~~
    
    
~~~shell
>>> mock = Mock()
>>> mock.method()
<Mock name='mock.method()' id='...'>
>>> mock.method.assert_called_once()
>>> mock.method()
<Mock name='mock.method()' id='...'>
>>> mock.method.assert_called_once()
Traceback (most recent call last):
...
AssertionError: Expected 'method' to have been called once. Called 2 times.
Calls: [call(), call()].
~~~

在 3.6 版本加入.

assert_called_with( _* args_, _** kwargs_)¶

    

~~~
此方法是断言上次调用已以特定方式进行的一种便捷方法：
~~~
    
    
~~~shell
>>> mock = Mock()
>>> mock.method(1, 2, 3, test='wow')
<Mock name='mock.method()' id='...'>
>>> mock.method.assert_called_with(1, 2, 3, test='wow')
~~~

assert_called_once_with( _* args_, _** kwargs_)¶

    

~~~
断言 mock 已被调用恰好一次，并且向该调用传入了指定的参数。
~~~
    
    
~~~shell
>>> mock = Mock(return_value=None)
>>> mock('foo', bar='baz')
>>> mock.assert_called_once_with('foo', bar='baz')
>>> mock('other', bar='values')
>>> mock.assert_called_once_with('other', bar='values')
Traceback (most recent call last):
  ...
AssertionError: Expected 'mock' to be called once. Called 2 times.
Calls: [call('foo', bar='baz'), call('other', bar='values')].
~~~

assert_any_call( _* args_, _** kwargs_)¶

    

~~~
断言 mock 已被调用并附带了指定的参数。

如果 mock _曾经_ 被调用过则断言通过，不同于 `assert_called_with()` 和 `assert_called_once_with()` 那样只有在调用是最近的一次时才会通过，而对于 `assert_called_once_with()` 则它还必须是唯一的一次调用。
~~~
    
    
~~~shell
>>> mock = Mock(return_value=None)
>>> mock(1, 2, arg='thing')
>>> mock('some', 'thing', 'else')
>>> mock.assert_any_call(1, 2, arg='thing')
~~~

assert_has_calls( _calls_ , _any_order =False_)¶

    

~~~
断言 mock 已附带指定的参数被调用。 将针对这些调用检查 `mock_calls` 列表。

如果 _any_order_ 为假值则调用必须是顺序进行的。 在指定的调用之前或之后还可以有额外的调用。

如果 _any_order_ 为真值则调用可以是任意顺序的，但它们都必须在 `mock_calls` 中出现。
~~~
    
    
~~~shell
>>> mock = Mock(return_value=None)
>>> mock(1)
>>> mock(2)
>>> mock(3)
>>> mock(4)
>>> calls = [call(2), call(3)]
>>> mock.assert_has_calls(calls)
>>> calls = [call(4), call(2), call(3)]
>>> mock.assert_has_calls(calls, any_order=True)
~~~

assert_not_called()¶

    

~~~
断言 mock 从未被调用过。
~~~
    
    
~~~shell
>>> m = Mock()
>>> m.hello.assert_not_called()
>>> obj = m.hello()
>>> m.hello.assert_not_called()
Traceback (most recent call last):
  ...
AssertionError: Expected 'hello' to not have been called. Called 1 times.
Calls: [call()].
~~~

在 3.5 版本加入.

reset_mock( _*_ , _return_value =False_, _side_effect =False_)¶

    

~~~
reset_mock 方法将在 mock 对象上重围所有的调用属性:
~~~
    
    
~~~shell
>>> mock = Mock(return_value=None)
>>> mock('hello')
>>> mock.called
True
>>> mock.reset_mock()
>>> mock.called
False
~~~

在 3.6 版本发生变更: 向 reset_mock 函数添加了两个仅限关键字参数。

这在你想要创建一系列重用相同对象的断言时会很有用处。 请注意 `reset_mock()` _不会_ 清除返回值、`side_effect` 或任何你使用普通赋值所默认设置的子属性。 如果你想要重置 _return_value_ 或 `side_effect`，则要为相应的形参传入 `True`。 子 mock 和返回值 mock (如果有的话) 也会被重置。

备注

_return_value_ ，以及 `side_effect` 均为仅限关键字参数。

mock_add_spec( _spec_ , _spec_set =False_)¶

    

~~~
为 mock 添加描述。 _spec_ 可以是一个对象或字符串列表。 只有 _spec_ 上的属性可以作为来自 mock 的属性被获取。

如果 _spec_set_ 为真值则只有 spec 上的属性可以被设置。

attach_mock( _mock_ , _attribute_ )¶
~~~
    

~~~
附加一个 mock 作为这一个的属性，替换它的名称和上级。 对附加 mock 的调用将记录在这一个的 `method_calls` 和 `mock_calls` 属性中。

configure_mock( _** kwargs_)¶
~~~
    

~~~
通过关键字参数在 mock 上设置属性。

属性加返回值和附带影响可以使用标准点号标记在子 mock 上设置并在方法调用中解包一个字典:
~~~
    
    
~~~shell
>>> mock = Mock()
>>> attrs = {'method.return_value': 3, 'other.side_effect': KeyError}
>>> mock.configure_mock(**attrs)
>>> mock.method()
3
>>> mock.other()
Traceback (most recent call last):
  ...
KeyError
~~~

同样的操作可在对 mock 的构造器调用中达成:

    
    
~~~shell
>>> attrs = {'method.return_value': 3, 'other.side_effect': KeyError}
>>> mock = Mock(some_attribute='eggs', **attrs)
>>> mock.some_attribute
'eggs'
>>> mock.method()
3
>>> mock.other()
Traceback (most recent call last):
  ...
KeyError
~~~

`configure_mock()` 的存在是使得 mock 被创建之后的配置更为容易。

__dir__()¶

    

~~~
`Mock` 对象会将 `dir(some_mock)` 的结果限制为有用结果。 对于带有 _spec_ 的 mock 这还包括 mock 的所有被允许的属性。

请查看 `FILTER_DIR` 了解此过滤做了什么，以及如何停用它。

_get_child_mock( _** kw_)¶
~~~
    

~~~
创建针对属性和返回值的子 mock。 默认情况下子 mock 将为与其上级相同的类型。 Mock 的子类可能需要重载它来定制子 mock 的创建方式。

对于非可调用的 mock 将会使用可调用的变化形式（而非不是任意的自定义子类）。

called¶
~~~
    

~~~
一个表示 mock 对象是否已被调用的布尔值:
~~~
    
    
~~~shell
>>> mock = Mock(return_value=None)
>>> mock.called
False
>>> mock()
>>> mock.called
True
~~~

call_count¶

    

~~~
一个告诉你 mock 对象已被调用多少次的整数值:
~~~
    
    
~~~shell
>>> mock = Mock(return_value=None)
>>> mock.call_count
0
>>> mock()
>>> mock()
>>> mock.call_count
2
~~~

return_value¶

    

~~~
设置这个来配置通过调用该 mock 所返回的值:
~~~
    
    
~~~shell
>>> mock = Mock()
>>> mock.return_value = 'fish'
>>> mock()
'fish'
~~~

默认的返回值是一个 mock 对象并且你可以通过正常方式来配置它:

    
    
~~~shell
>>> mock = Mock()
>>> mock.return_value.attribute = sentinel.Attribute
>>> mock.return_value()
<Mock name='mock()()' id='...'>
>>> mock.return_value.assert_called_with()
~~~

`return_value` 也可以在构造器中设置:

    
    
~~~shell
>>> mock = Mock(return_value=3)
>>> mock.return_value
3
>>> mock()
3
~~~

side_effect¶

    

~~~
这可以是当该This can either be a function to be called when the mock 被调用时将被调用的一个函数，可调用对象或者要被引发的异常（类或实例）。

如果你传入一个函数则它将附带与该 mock 相同的参数被调用并且除了该函数返回 `DEFAULT` 单例的情况以外对该 mock 的调用都将随后返回该函数所返回的任何东西。 如果该函数返回 `DEFAULT` 则该 mock 将返回其正常值 (来自 `return_value`)。

如果你传入一个可迭代对象，它会被用来获取一个在每次调用时必须产生一个值的迭代器。 这个值可以是一个要被引发的异常实例，或是一个要从该调用返回给 mock 的值 (`DEFAULT` 处理与函数的情况一致)。

一个引发异常（来测试 API 的异常处理）的 mock 的示例:
~~~
    
    
~~~shell
>>> mock = Mock()
>>> mock.side_effect = Exception('Boom!')
>>> mock()
Traceback (most recent call last):
  ...
Exception: Boom!
~~~

使用 `side_effect` 来返回包含多个值的序列:

    
    
~~~shell
>>> mock = Mock()
>>> mock.side_effect = [3, 2, 1]
>>> mock(), mock(), mock()
(3, 2, 1)
~~~

使用一个可调用对象:

    
    
~~~shell
>>> mock = Mock(return_value=3)
>>> def side_effect(*args, **kwargs):
...     return DEFAULT
...
>>> mock.side_effect = side_effect
>>> mock()
3
~~~

`side_effect` 可以在构造器中设置。 下面是在 mock 被调用时增加一个该属性值并返回它的例子:

    
    
~~~shell
>>> side_effect = lambda value: value + 1
>>> mock = Mock(side_effect=side_effect)
>>> mock(3)
4
>>> mock(-8)
-7
~~~

将 `side_effect` 设为 `None` 可以清除它:

    
    
~~~shell
>>> m = Mock(side_effect=KeyError, return_value=3)
>>> m()
Traceback (most recent call last):
 ...
KeyError
>>> m.side_effect = None
>>> m()
3
~~~

call_args¶

    

~~~
这可以是 `None` (如果 mock 没有被调用)，或是 mock 最近一次被调用时附带的参数。 这将采用元组的形式：第一个成员也可以通过 `args` 特征属性来访问，它是 mock 被调用时所附带的任何位置参数 (或为空元组)，而第二个成员也可以通过 `kwargs` 特征属性来访问，它则是任何关键字参数 (或为空字典)。
~~~
    
    
~~~shell
>>> mock = Mock(return_value=None)
>>> print(mock.call_args)
None
>>> mock()
>>> mock.call_args
call()
>>> mock.call_args == ()
True
>>> mock(3, 4)
>>> mock.call_args
call(3, 4)
>>> mock.call_args == ((3, 4),)
True
>>> mock.call_args.args
(3, 4)
>>> mock.call_args.kwargs
{}
>>> mock(3, 4, 5, key='fish', next='w00t!')
>>> mock.call_args
call(3, 4, 5, key='fish', next='w00t!')
>>> mock.call_args.args
(3, 4, 5)
>>> mock.call_args.kwargs
{'key': 'fish', 'next': 'w00t!'}
~~~

`call_args`，以及列表 `call_args_list`, `method_calls` 和 `mock_calls` 的成员都是 `call` 对象。 这些对象属性元组，因此它们可被解包以获得单独的参数并创建更复杂的断言。 参见 作为元组的 call。

在 3.8 版本发生变更: 增加了 `args` 和 `kwargs` 特征属性。properties.

call_args_list¶

    

~~~
这是一个已排序的对 mock 对象的所有调用的列表（因此该列表的长度就是它已被调用的次数）。 在执行任何调用之前它将是一个空列表。 `call` 对象可以被用来方便地构造调用列表以与 `call_args_list` 相比较。
~~~
    
    
~~~shell
>>> mock = Mock(return_value=None)
>>> mock()
>>> mock(3, 4)
>>> mock(key='fish', next='w00t!')
>>> mock.call_args_list
[call(), call(3, 4), call(key='fish', next='w00t!')]
>>> expected = [(), ((3, 4),), ({'key': 'fish', 'next': 'w00t!'},)]
>>> mock.call_args_list == expected
True
~~~

`call_args_list` 的成员均为 `call` 对象。 它们可作为元组被解包以获得单个参数。 参见 作为元组的 call。

method_calls¶

    

~~~
除了会追踪对其自身的调用，mock 还会追踪对方法和属性，以及 _它们的_ 方法和属性的访问:
~~~
    
    
~~~shell
>>> mock = Mock()
>>> mock.method()
<Mock name='mock.method()' id='...'>
>>> mock.property.method.attribute()
<Mock name='mock.property.method.attribute()' id='...'>
>>> mock.method_calls
[call.method(), call.property.method.attribute()]
~~~

`method_calls` 的成员均为 `call` 对象。 它们可以作为元组被解包以获得单个参数。 参见 作为元组的 call。

mock_calls¶

    

~~~
`mock_calls` 会记录 _所有_ 对 mock 对象、它的方法、魔术方法的调用 _以及_ 返回值的 mock。
~~~
    
    
~~~shell
>>> mock = MagicMock()
>>> result = mock(1, 2, 3)
>>> mock.first(a=3)
<MagicMock name='mock.first()' id='...'>
>>> mock.second()
<MagicMock name='mock.second()' id='...'>
>>> int(mock)
1
>>> result(1)
<MagicMock name='mock()()' id='...'>
>>> expected = [call(1, 2, 3), call.first(a=3), call.second(),
... call.__int__(), call()(1)]
>>> mock.mock_calls == expected
True
~~~

`mock_calls` 的成员均为 `call` 对象。 它们可以作为元组被解包以获得单个参数。 参见 作为元组的 call。

备注

`mock_calls` 的记录方式意味着在进行嵌套调用时，之前调用的形参不会被记录因而这样的比较将总是相等:

    
    
~~~shell
>>> mock = MagicMock()
>>> mock.top(a=3).bottom()
<MagicMock name='mock.top().bottom()' id='...'>
>>> mock.mock_calls
[call.top(a=3), call.top().bottom()]
>>> mock.mock_calls[-1] == call.top(a=-1).bottom()
True
~~~

__class__¶

    

~~~
通常一个对象的 `__class__` 属性将返回其类型。 对于具有 `spec` 的 mock 对象来说，`__class__` 将改为返回 spec 类。 这将允许 mock 对象为它们所替换 / 屏蔽的对象跳过 [`isinstance()`](functions.md#isinstance "isinstance") 测试:
~~~
    
    
~~~shell
>>> mock = Mock(spec=3)
>>> isinstance(mock, int)
True
~~~

`__class__` 是可以被赋值的，这将允许 mock 跳过 [`isinstance()`](functions.md#isinstance "isinstance") 检测而不强制要求你使用 spec:

    
    
~~~shell
>>> mock = Mock()
>>> mock.__class__ = dict
>>> isinstance(mock, dict)
True
~~~

_class _unittest.mock.NonCallableMock( _spec =None_, _wraps =None_, _name =None_, _spec_set =None_, _** kwargs_)¶

    

~~~
不可调用的 `Mock` 版本。 其构造器的形参具有与 `Mock` 相同的含义，区别在于 _return_value_ 和 _side_effect_ 在不可调用的 mock 上没有意义。

使用类或实例作为 `spec` 或 `spec_set` 的 mock 对象能够跳过 [`isinstance()`](functions.md#isinstance "isinstance") 测试:
~~~
    
    
~~~shell
>>> mock = Mock(spec=SomeClass)
>>> isinstance(mock, SomeClass)
True
>>> mock = Mock(spec_set=SomeClass())
>>> isinstance(mock, SomeClass)
True
~~~

`Mock` 类具有对 mock 操作魔术方法的支持。 请参阅 魔术方法 了解完整细节。

mock 操作类和 `patch()` 装饰器都接受任意关键字参数用于配置。 对于 `patch()` 装饰器来说关键字参数会被传给所创建 mock 的构造器。 这些关键字被用于配置 mock 的属性:

    
    
~~~shell
>>> m = MagicMock(attribute=3, other='fish')
>>> m.attribute
3
>>> m.other
'fish'
~~~

子 mock 的返回值和附带效果也可使用带点号的标记通过相同的方式来设置。 由于你无法直接在调用中使用带点号的名称因此你需要创建一个字典并使用 `**` 来解包它:

    
    
~~~shell
>>> attrs = {'method.return_value': 3, 'other.side_effect': KeyError}
>>> mock = Mock(some_attribute='eggs', **attrs)
>>> mock.some_attribute
'eggs'
>>> mock.method()
3
>>> mock.other()
Traceback (most recent call last):
  ...
KeyError
~~~

使用 _spec_ (或 _spec_set_ ) 创建的可调用 mock 将在匹配调用与 mock 时内省规格说明对象的签名。 因此，它可以匹配实际调用的参数而不必关心它们是按位置还是按名称传入的:

    
    
~~~shell
>>> def f(a, b, c): pass
...
>>> mock = Mock(spec=f)
>>> mock(1, 2, c=3)
<Mock name='mock()' id='140161580456576'>
>>> mock.assert_called_with(1, 2, 3)
>>> mock.assert_called_with(a=1, b=2, c=3)
~~~

这适用于 `assert_called_with()`, `assert_called_once_with()`, `assert_has_calls()` 和 `assert_any_call()`。 当执行 自动 spec 时，它还将应用于 mock 对象的方法调用。

> 在 3.4 版本发生变更: 添加了在附带规格说明和自动规格说明的 mock 对象上的签名内省

_class _unittest.mock.PropertyMock( _* args_, _** kwargs_)¶

    

~~~
旨在作为类的特征属性或其他描述器使用的 mock。 `PropertyMock` 提供了 `__get__()` 和 `__set__()` 方法以便你可以在它被提取时指定一个返回值。

当从一个对象提取 `PropertyMock` 实例时将不附带任何参数地调用该 mock。 如果设置它则调用该 mock 时将附带被设置的值。
~~~
    
    
~~~shell
>>> class Foo:
...     @property
...     def foo(self):
...         return 'something'
...     @foo.setter
...     def foo(self, value):
...         pass
...
>>> with patch('__main__.Foo.foo', new_callable=PropertyMock) as mock_foo:
...     mock_foo.return_value = 'mockity-mock'
...     this_foo = Foo()
...     print(this_foo.foo)
...     this_foo.foo = 6
...
mockity-mock
>>> mock_foo.mock_calls
[call(), call(6)]
~~~

由于 mock 属性的存储方式你无法直接将 `PropertyMock` 附加到一个 mock 对象。 但是你可以将它附加到 mock 类型对象:

    
    
~~~shell
>>> m = MagicMock()
>>> p = PropertyMock(return_value=3)
>>> type(m).foo = p
>>> m.foo
3
>>> p.assert_called_once_with()
~~~

_class _unittest.mock.AsyncMock( _spec =None_, _side_effect =None_, _return_value =DEFAULT_, _wraps =None_, _name =None_, _spec_set =None_, _unsafe =False_, _** kwargs_)¶

    

~~~
`MagicMock` 的异步版本。 `AsyncMock` 对象的行为方式将使该对象被识别为异步函数，其调用的结果将为可等待对象。
~~~
    
    
~~~shell
>>> mock = AsyncMock()
>>> asyncio.iscoroutinefunction(mock)
True
>>> inspect.isawaitable(mock())  
True
~~~

调用 `mock()` 的结果是一个异步函数，它在被等待之后将具有 `side_effect` 或 `return_value` 的结果:

  * 如果 `side_effect` 是一个函数，则异步函数将返回该函数的结果,

  * 如果 `side_effect` 是一个异常，则异步函数将引发该异常,

  * 如果 `side_effect` 是一个可迭代对象，则异步函数将返回该可迭代对象的下一个值，但是，如果结果序列被耗尽，则会立即引发 `StopAsyncIteration`,

  * 如果 `side_effect` 未被定义，则异步函数将返回is not defined, the async function will return the value defined by `return_value` 所定义的值，因而，在默认情况下，异步函数会返回一个新的 `AsyncMock` 对象。

将 `Mock` 或 `MagicMock` 的 _spec_ 设为异步函数将导致在调用后返回一个协程对象。

    
    
~~~shell
>>> async def async_func(): pass
...
>>> mock = MagicMock(async_func)
>>> mock
<MagicMock spec='function' id='...'>
>>> mock()  
<coroutine object AsyncMockMixin._mock_call at ...>
~~~

将 `Mock`, `MagicMock` 或 `AsyncMock` 的 _spec_ 设为带有异步和同步函数的类将自动删除其中的同步函数并将它们设为 `MagicMock` (如果上级 mock 是 `AsyncMock` 或 `MagicMock`) 或者 `Mock` (如果上级 mock 是 `Mock`)。 所有异步函数都将为 `AsyncMock`。

    
    
~~~shell
>>> class ExampleClass:
...     def sync_foo():
...         pass
...     async def async_foo():
...         pass
...
>>> a_mock = AsyncMock(ExampleClass)
>>> a_mock.sync_foo
<MagicMock name='mock.sync_foo' id='...'>
>>> a_mock.async_foo
<AsyncMock name='mock.async_foo' id='...'>
>>> mock = Mock(ExampleClass)
>>> mock.sync_foo
<Mock name='mock.sync_foo' id='...'>
>>> mock.async_foo
<AsyncMock name='mock.async_foo' id='...'>
~~~

在 3.8 版本加入.

assert_awaited()¶

    

~~~
断言 mock 已被等待至少一次。 请注意这是从被调用的对象中分离出来的，必须要使用 `await` 关键字:
~~~
    
    
~~~shell
>>> mock = AsyncMock()
>>> async def main(coroutine_mock):
...     await coroutine_mock
...
>>> coroutine_mock = mock()
>>> mock.called
True
>>> mock.assert_awaited()
Traceback (most recent call last):
...
AssertionError: Expected mock to have been awaited.
>>> asyncio.run(main(coroutine_mock))
>>> mock.assert_awaited()
~~~

assert_awaited_once()¶

    

~~~
断言 mock 已被等待恰好一次。
~~~
    
    
~~~shell
>>> mock = AsyncMock()
>>> async def main():
...     await mock()
...
>>> asyncio.run(main())
>>> mock.assert_awaited_once()
>>> asyncio.run(main())
>>> mock.assert_awaited_once()
Traceback (most recent call last):
...
AssertionError: Expected mock to have been awaited once. Awaited 2 times.
~~~

assert_awaited_with( _* args_, _** kwargs_)¶

    

~~~
断言上一次等待传入了指定的参数。
~~~
    
    
~~~shell
>>> mock = AsyncMock()
>>> async def main(*args, **kwargs):
...     await mock(*args, **kwargs)
...
>>> asyncio.run(main('foo', bar='bar'))
>>> mock.assert_awaited_with('foo', bar='bar')
>>> mock.assert_awaited_with('other')
Traceback (most recent call last):
...
AssertionError: expected await not found.
Expected: mock('other')
Actual: mock('foo', bar='bar')
~~~

assert_awaited_once_with( _* args_, _** kwargs_)¶

    

~~~
断言 mock 已被等待恰好一次并且附带了指定的参数。
~~~
    
    
~~~shell
>>> mock = AsyncMock()
>>> async def main(*args, **kwargs):
...     await mock(*args, **kwargs)
...
>>> asyncio.run(main('foo', bar='bar'))
>>> mock.assert_awaited_once_with('foo', bar='bar')
>>> asyncio.run(main('foo', bar='bar'))
>>> mock.assert_awaited_once_with('foo', bar='bar')
Traceback (most recent call last):
...
AssertionError: Expected mock to have been awaited once. Awaited 2 times.
~~~

assert_any_await( _* args_, _** kwargs_)¶

    

~~~
断言 mock 已附带了指定的参数被等待。
~~~
    
    
~~~shell
>>> mock = AsyncMock()
>>> async def main(*args, **kwargs):
...     await mock(*args, **kwargs)
...
>>> asyncio.run(main('foo', bar='bar'))
>>> asyncio.run(main('hello'))
>>> mock.assert_any_await('foo', bar='bar')
>>> mock.assert_any_await('other')
Traceback (most recent call last):
...
AssertionError: mock('other') await not found
~~~

assert_has_awaits( _calls_ , _any_order =False_)¶

    

~~~
断言 mock 已附带了指定的调用被等待。 将针对这些等待检查 `await_args_list` 列表。

如果 _any_order_ 为假值则等待必须是顺序进行的。 在指定的等待之前或之后还可以有额外的调用。

如果 _any_order_ 为真值则等待可以是任意顺序的，但它们都必须在 `await_args_list` 中出现。
~~~
    
    
~~~shell
>>> mock = AsyncMock()
>>> async def main(*args, **kwargs):
...     await mock(*args, **kwargs)
...
>>> calls = [call("foo"), call("bar")]
>>> mock.assert_has_awaits(calls)
Traceback (most recent call last):
...
AssertionError: Awaits not found.
Expected: [call('foo'), call('bar')]
Actual: []
>>> asyncio.run(main('foo'))
>>> asyncio.run(main('bar'))
>>> mock.assert_has_awaits(calls)
~~~

assert_not_awaited()¶

    

~~~
断言 mock 从未被等待过。
~~~
    
    
~~~shell
>>> mock = AsyncMock()
>>> mock.assert_not_awaited()
~~~

reset_mock( _* args_, _** kwargs_)¶

    

~~~
参见 `Mock.reset_mock()`。 还会将 `await_count` 设为 0，将 `await_args` 设为 None，并清空 `await_args_list`。

await_count¶
~~~
    

~~~
一个追踪 mock 对象已被等待多少次的整数值。
~~~
    
    
~~~shell
>>> mock = AsyncMock()
>>> async def main():
...     await mock()
...
>>> asyncio.run(main())
>>> mock.await_count
1
>>> asyncio.run(main())
>>> mock.await_count
2
~~~

await_args¶

    

~~~
这可能为 `None` (如果 mock 从未被等待)，或为该 mock 上一次被等待所附带的参数。 其功能与 `Mock.call_args` 相同。
~~~
    
    
~~~shell
>>> mock = AsyncMock()
>>> async def main(*args):
...     await mock(*args)
...
>>> mock.await_args
>>> asyncio.run(main('foo'))
>>> mock.await_args
call('foo')
>>> asyncio.run(main('bar'))
>>> mock.await_args
call('bar')
~~~

await_args_list¶

    

~~~
这是由对 mock 对象按顺序执行的所有等待组成的列表（因此该列表的长度即它被等待的次数）。 在有任何等待被执行之前它将为一个空列表。
~~~
    
    
~~~shell
>>> mock = AsyncMock()
>>> async def main(*args):
...     await mock(*args)
...
>>> mock.await_args_list
[]
>>> asyncio.run(main('foo'))
>>> mock.await_args_list
[call('foo')]
>>> asyncio.run(main('bar'))
>>> mock.await_args_list
[call('foo'), call('bar')]
~~~

_class _unittest.mock.ThreadingMock( _spec =None_, _side_effect =None_, _return_value =DEFAULT_, _wraps =None_, _name =None_, _spec_set =None_, _unsafe =False_, _*_ , _timeout =UNSET_, _** kwargs_)¶

    

~~~
A version of `MagicMock` for multithreading tests. The `ThreadingMock` object provides extra methods to wait for a call to be invoked, rather than assert on it immediately.

The default timeout is specified by the `timeout` argument, or if unset by the `ThreadingMock.DEFAULT_TIMEOUT` attribute, which defaults to blocking (`None`).

You can configure the global default timeout by setting `ThreadingMock.DEFAULT_TIMEOUT`.

wait_until_called( _*_ , _timeout =UNSET_)¶
~~~
    

~~~
Waits until the mock is called.

If a timeout was passed at the creation of the mock or if a timeout argument is passed to this function, the function raises an [`AssertionError`](exceptions.md#AssertionError "AssertionError") if the call is not performed in time.
~~~
    
    
~~~shell
>>> mock = ThreadingMock()
>>> thread = threading.Thread(target=mock)
>>> thread.start()
>>> mock.wait_until_called(timeout=1)
>>> thread.join()
~~~

wait_until_any_call_with( _* args_, _** kwargs_)¶

    

~~~
Waits until the the mock is called with the specified arguments.

If a timeout was passed at the creation of the mock the function raises an [`AssertionError`](exceptions.md#AssertionError "AssertionError") if the call is not performed in time.
~~~
    
    
~~~shell
>>> mock = ThreadingMock()
>>> thread = threading.Thread(target=mock, args=("arg1", "arg2",), kwargs={"arg": "thing"})
>>> thread.start()
>>> mock.wait_until_any_call_with("arg1", "arg2", arg="thing")
>>> thread.join()
~~~

DEFAULT_TIMEOUT¶

    

~~~
Global default timeout in seconds to create instances of `ThreadingMock`.

在 3.13 版本加入.

### 调用¶

Mock 对象是可调用对象。 调用将把值集合作为 `return_value` 属性返回。 默认的返回值是一个新的 Mock 对象；它会在对返回值的首次访问（不论是显式访问还是通过调用 Mock）时被创建 —— 但它会被保存并且每次都返回相同的对象。

对该对象的调用将被记录在 `call_args` 和 `call_args_list` 等属性中。

如果设置了 `side_effect` 则它将在调用被记录之后被调用，因此如果 `side_effect` 引发了异常该调用仍然会被记录。

让一个 mock 在被调用时引发异常的最简单方式是将 `side_effect` 设为一个异常类或实例:
~~~
    
    
~~~shell
>>> m = MagicMock(side_effect=IndexError)
>>> m(1, 2, 3)
Traceback (most recent call last):
  ...
IndexError
>>> m.mock_calls
[call(1, 2, 3)]
>>> m.side_effect = KeyError('Bang!')
>>> m('two', 'three', 'four')
Traceback (most recent call last):
  ...
KeyError: 'Bang!'
>>> m.mock_calls
[call(1, 2, 3), call('two', 'three', 'four')]
~~~

如果 `side_effect` 为函数则该函数所返回的对象就是调用该 mock 所返回的对象。 `side_effect` 函数在被调用时将附带与该 mock 相同的参数。 这允许你根据输入动态地改变返回值:

    
    
~~~shell
>>> def side_effect(value):
...     return value + 1
...
>>> m = MagicMock(side_effect=side_effect)
>>> m(1)
2
>>> m(2)
3
>>> m.mock_calls
[call(1), call(2)]
~~~

如果你想让该 mock 仍然返回默认的返回值（一个新的 mock对象），或是任何设定的返回值，那么有两种方式可以做到这一点。 从 `side_effect` 内部返回 `mock.return_value`，或者返回 `DEFAULT`:

    
    
~~~shell
>>> m = MagicMock()
>>> def side_effect(*args, **kwargs):
...     return m.return_value
...
>>> m.side_effect = side_effect
>>> m.return_value = 3
>>> m()
3
>>> def side_effect(*args, **kwargs):
...     return DEFAULT
...
>>> m.side_effect = side_effect
>>> m()
3
~~~

要移除一个 `side_effect`，并返回到默认的行为，请将 `side_effect` 设为 `None`:

    
    
~~~shell
>>> m = MagicMock(return_value=6)
>>> def side_effect(*args, **kwargs):
...     return 3
...
>>> m.side_effect = side_effect
>>> m()
3
>>> m.side_effect = None
>>> m()
6
~~~

`side_effect` 也可以是任意可迭代对象。 对该 mock 的重复调用将返回来自该可迭代对象的值（直到该可迭代对象被耗尽并导致 [`StopIteration`](exceptions.md#StopIteration "StopIteration") 被引发）:

    
    
~~~shell
>>> m = MagicMock(side_effect=[1, 2, 3])
>>> m()
1
>>> m()
2
>>> m()
3
>>> m()
Traceback (most recent call last):
  ...
StopIteration
~~~

如果该可迭代对象有任何成员属于异常则它们将被引发而不是被返回:

    
    
~~~shell
>>> iterable = (33, ValueError, 66)
>>> m = MagicMock(side_effect=iterable)
>>> m()
33
>>> m()
Traceback (most recent call last):
 ...
ValueError
>>> m()
66
~~~

### 删除属性¶

Mock 对象会根据需要创建属性。 这允许它们可以假装成任意类型的对象。

你可能想要一个 mock 对象在调用 [`hasattr()`](functions.md#hasattr "hasattr") 时返回 `False`，或者在获取某个属性时引发 [`AttributeError`](exceptions.md#AttributeError "AttributeError")。 你可以通过提供一个对象作为 mock 的 `spec` 属性来做到这点，但这并不总是很方便。

你可以通过删除属性来“屏蔽”它们。 属性一旦被删除，访问它将引发 [`AttributeError`](exceptions.md#AttributeError "AttributeError")。

    
    
~~~shell
>>> mock = MagicMock()
>>> hasattr(mock, 'm')
True
>>> del mock.m
>>> hasattr(mock, 'm')
False
>>> del mock.f
>>> mock.f
Traceback (most recent call last):
    ...
AttributeError: f
~~~

### Mock 的名称与 name 属性¶

由于 "name" 是 `Mock` 构造器的参数之一，如果你想让你的 mock 对象具有 "name" 属性你不可以在创建时传入该参数。 有两个替代方式。 一个选项是使用 `configure_mock()`:

    
    
~~~shell
>>> mock = MagicMock()
>>> mock.configure_mock(name='my_name')
>>> mock.name
'my_name'
~~~

一个更简单的选项是在 mock 创建之后简单地设置 "name" 属性:

    
    
~~~shell
>>> mock = MagicMock()
>>> mock.name = "foo"
~~~

### 附加 Mock 作为属性¶

当你附加一个 mock 作为另一个 mock 的属性（或作为返回值）时它会成为该 mock 的 "子对象"。 对子对象的调用会被记录在父对象的 `method_calls` 和 `mock_calls` 属性中。 这适用于配置子 mock 然后将它们附加到父对象，或是将 mock 附加到将记录所有对子对象的调用的父对象上并允许你创建有关 mock 之间的调用顺序的断言:

    
    
~~~shell
>>> parent = MagicMock()
>>> child1 = MagicMock(return_value=None)
>>> child2 = MagicMock(return_value=None)
>>> parent.child1 = child1
>>> parent.child2 = child2
>>> child1(1)
>>> child2(2)
>>> parent.mock_calls
[call.child1(1), call.child2(2)]
~~~

这里有一个例外情况是如果 mock 设置了名称。 这允许你在出于某些理由不希望其发生时避免 "父对象" 的影响。

    
    
~~~shell
>>> mock = MagicMock()
>>> not_a_child = MagicMock(name='not-a-child')
>>> mock.attribute = not_a_child
>>> mock.attribute()
<MagicMock name='not-a-child()' id='...'>
>>> mock.mock_calls
[]
~~~

通过 `patch()` 创建的 mock 会被自动赋予名称。 要将具有名称的 mock 附加到父对象上你应当使用 `attach_mock()` 方法:

    
    
~~~shell
>>> thing1 = object()
>>> thing2 = object()
>>> parent = MagicMock()
>>> with patch('__main__.thing1', return_value=None) as child1:
...     with patch('__main__.thing2', return_value=None) as child2:
...         parent.attach_mock(child1, 'child1')
...         parent.attach_mock(child2, 'child2')
...         child1('one')
...         child2('two')
...
>>> parent.mock_calls
[call.child1('one'), call.child2('two')]
~~~

[1]

仅有的例外是魔术方法和属性（其名称前后都带有双下划线）。 Mock 不会创建它们而是将引发 [`AttributeError`](exceptions.md#AttributeError "AttributeError")。 这是因为解释器将会经常隐式地请求这些方法，并且在它准备接受一个魔术方法却得到一个新的 Mock 对象时会 _相当_ 困惑。 如果你需要魔术方法支持请参阅 魔术方法。

## patch 装饰器¶

patch 装饰器仅被用于在它们所装饰的函数作用域内部为对象添加补丁。 它们会自动为你执行去除补丁的处理，即使是在引发了异常的情况下。 所有这些函数都还可在 with 语句中使用或是作为类装饰器。

### patch¶

备注

问题的关键是要在正确的命名空间中打补丁。 参见 where to patch 一节。

unittest.mock.patch( _target_ , _new =DEFAULT_, _spec =None_, _create =False_, _spec_set =None_, _autospec =None_, _new_callable =None_, _** kwargs_)¶

    

~~~
`patch()` 可以作为函数装饰器、类装饰器或上下文管理器。 在函数或 with 语句的内部， _target_ 会打上一个 _new_ 对象补丁。 当函数/with 语句退出时补丁将被撤销。

如果 _new_ 被省略，那么如果被打补丁的对象是一个异步函数则 target 将被替换为 `AsyncMock` 否则替换为 `MagicMock`。 如果 `patch()` 被用作装饰器并且 _new_ 被省略，那么已创建的 mock 将作为一个附加参数传入被装饰的函数。 如果 `patch()` 被用作上下文管理器那么已创建的 mock 将被该上下文管理器所返回。

_target_ 应当为 `'package.module.ClassName'` 形式的字符串。 _target_ 将被导入并且该指定对象会被替换为 _new_ 对象，因此 _target_ 必须是可以从你调用 `patch()` 的环境中导入的。 target 会在被装饰的函数被执行的时候被导入，而非在装饰的时候。

_spec_ 和 _spec_set_ 关键字参数会被传递给 `MagicMock`，如果 patch 为你创建了此对象的话。

此外你还可以传入 `spec=True` 或 `spec_set=True`，这将使 patch 将被模拟的对象作为 spec/spec_set 对象传入。

_new_callable_ 允许你指定一个不同的类，或者可调用对象，它将被调用以创建 _新的_ 对象。 在默认情况下将指定 `AsyncMock` 用于异步函数，`MagicMock` 用于其他函数。

另一种更强形式的 _spec_ 是 _autospec_ 。 如果你设置了 `autospec=True` 则将以来自被替换对象的 spec 来创建 mock。 mock 的所有属性也将具有被替换对象相应属性的 spec。 被模拟的方法和函数将检查它们的参数并且如果使用了错误的签名调用它们则将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。 对于替换了一个类的 mock，它们的返回值（即‘实例’）将具有与该类相同的 spec。 请参阅 `create_autospec()` 函数以及 自动 spec。

除了 `autospec=True` 你还可以传入 `autospec=some_object` 以使用任意对象而不是被替换的对象作为 spec。

在默认情况下 `patch()` 将无法替换不存在的属性。 如果你传入 `create=True`，且该属性并不存在，则 patch 将在调用被打补丁的函数时为你创建该属性，并在退出被打补丁的函数时再次删除它。 这适用于编写针对生产代码在运行时创建的属性的测试。 它默认是被关闭的因为这具有危险性。 当它被开启时你将能够针对实际上并不存在的 API 编写通过测试！

备注

在 3.5 版本发生变更: 如果你要给某个模块的内置函数打补丁则不必传入 `create=True`，它默认就会被添加。

Patch 可以被用作 `TestCase` 类装饰器。 其作用是装饰类中的每个测试方法。 当你的测试方法共享同一个补丁集时这将减少模板代码。 `patch()` 会通过查找以 `patch.TEST_PREFIX` 打头的名称来找到测试。 其默认值为 `'test'`，这与 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 打到测试的方式一致。 你可以通过设置 `patch.TEST_PREFIX` 来指定其他的前缀。

Patch 可以通过 with 语句作为上下文管理器使用。 这时补丁将应用于 with 语句的缩进代码块。 如果你使用了 "as" 则打补丁的对象将被绑定到 "as" 之后的名称；这非常适用于当 `patch()` 为你创建 mock 对象的情况。

`patch()` 可接受任意关键字参数。 如果打补丁的对象是异步的则这些参数将被传给 `AsyncMock`，否则传给 `MagicMock`，或者是指定的 _new_callable_ 。

`patch.dict(...)`, `patch.multiple(...)` 和 `patch.object(...)` 可用于其他使用场景。

`patch()` 作为函数装饰器，为你创建 mock 并将其传入被装饰的函数:
~~~
    
    
~~~shell
>>> @patch('__main__.SomeClass')
... def function(normal_argument, mock_class):
...     print(mock_class is SomeClass)
...
>>> function(None)
True
~~~

为类打补丁将把该类替换为 `MagicMock` 的 _实例_ 。 如果该类是在受测试的代码中被实例化的则它将为所要使用的 mock 的 `return_value`。

如果该类被多次实例化则你可以使用 `side_effect` 来每次返回一个新 mock。 或者你也可以将 _return_value_ 设为你希望的任何对象。

要在被打补丁的类的 _实例_ 的方法上配置返回值你必须在 `return_value` 操作。 例如:

    
    
~~~shell
>>> class Class:
...     def method(self):
...         pass
...
>>> with patch('__main__.Class') as MockClass:
...     instance = MockClass.return_value
...     instance.method.return_value = 'foo'
...     assert Class() is instance
...     assert Class().method() == 'foo'
...
~~~

如果你使用 _spec_ 或 _spec_set_ 并且 `patch()` 替换的是 _class_ ，那么所创建的 mock 的返回值将具有同样的 spec。

    
    
~~~shell
>>> Original = Class
>>> patcher = patch('__main__.Class', spec=True)
>>> MockClass = patcher.start()
>>> instance = MockClass()
>>> assert isinstance(instance, Original)
>>> patcher.stop()
~~~

_new_callable_ 参数适用于当你想要使用其他类来替代所创建的 mock 默认的 `MagicMock` 的场合。 例如，如果你想要使用 `NonCallableMock`:

    
    
~~~shell
>>> thing = object()
>>> with patch('__main__.thing', new_callable=NonCallableMock) as mock_thing:
...     assert thing is mock_thing
...     thing()
...
Traceback (most recent call last):
  ...
TypeError: 'NonCallableMock' object is not callable
~~~

另一个使用场景是用 [`io.StringIO`](io.md#io.StringIO "io.StringIO") 实例来替换某个对象:

    
    
~~~shell
>>> from io import StringIO
>>> def foo():
...     print('Something')
...
>>> @patch('sys.stdout', new_callable=StringIO)
... def test(mock_stdout):
...     foo()
...     assert mock_stdout.getvalue() == 'Something\n'
...
>>> test()
~~~

当 `patch()` 为你创建 mock 时，通常你需要做的第一件事就是配置该 mock。 某些配置可以在对 patch 的调用中完成。 你在调用时传入的任何关键字参数都将被用来在所创建的 mock 上设置属性:

    
    
~~~shell
>>> patcher = patch('__main__.thing', first='one', second='two')
>>> mock_thing = patcher.start()
>>> mock_thing.first
'one'
>>> mock_thing.second
'two'
~~~

除了所创建的 mock 的属性上的属性，例如 `return_value` 和 `side_effect`，还可以配置子 mock 的属性。 将这些属性直接作为关键字参数传入在语义上是无效的，但是仍然能够使用 `**` 将以这些属性为键的字典扩展至一个 `patch()` 调用中

    
    
~~~shell
>>> config = {'method.return_value': 3, 'other.side_effect': KeyError}
>>> patcher = patch('__main__.thing', **config)
>>> mock_thing = patcher.start()
>>> mock_thing.method()
3
>>> mock_thing.other()
Traceback (most recent call last):
  ...
KeyError
~~~

在默认情况下，尝试给某个模块中并不存在的函数（或者某个类中的方法或属性）打补丁将会失败并引发 [`AttributeError`](exceptions.md#AttributeError "AttributeError"):

    
    
~~~shell
>>> @patch('sys.non_existing_attribute', 42)
... def test():
...     assert sys.non_existing_attribute == 42
...
>>> test()
Traceback (most recent call last):
  ...
AttributeError: <module 'sys' (built-in)> does not have the attribute 'non_existing_attribute'
~~~

但在对 `patch()` 的调用中添加 `create=True` 将使之前示例的效果符合预期:

    
    
~~~shell
>>> @patch('sys.non_existing_attribute', 42, create=True)
... def test(mock_stdout):
...     assert sys.non_existing_attribute == 42
...
>>> test()
~~~

在 3.8 版本发生变更: 如果目标为异步函数那么 `patch()` 现在将返回一个 `AsyncMock`。

### patch.object¶

patch.object( _target_ , _attribute_ , _new =DEFAULT_, _spec =None_, _create =False_, _spec_set =None_, _autospec =None_, _new_callable =None_, _** kwargs_)¶

    

~~~
用一个 mock 对象为对象 ( _target_ ) 中指定名称的成员 ( _attribute_ ) 打补丁。

`patch.object()` 可以被用作装饰器、类装饰器或上下文管理器。 _new_ , _spec_ , _create_ , _spec_set_ , _autospec_ 和 _new_callable_ 等参数的含义与 `patch()` 的相同。 与 `patch()` 类似，`patch.object()` 接受任意关键字参数用于配置它所创建的 mock 对象。

当用作类装饰器时 `patch.object()` 将认可 `patch.TEST_PREFIX` 作为选择所要包装方法的标准。

你可以附带三个参数或两个参数来调用 `patch.object()`。 三个参数的形式将接受要打补丁的对象、属性的名称以及将要替换该属性的对象。

将附带两个参数的形式调用时你将省略替换对象，还会为你创建一个 mock 并作为附加参数传入被装饰的函数:
~~~
    
    
~~~shell
>>> @patch.object(SomeClass, 'class_method')
... def test(mock_method):
...     SomeClass.class_method(3)
...     mock_method.assert_called_with(3)
...
>>> test()
~~~

传给 `patch.object()` 的 _spec_ , _create_ 和其他参数的含义与 `patch()` 的同名参数相同。

### patch.dict¶

patch.dict( _in_dict_ , _values =()_, _clear =False_, _** kwargs_)¶

    

~~~
为一个字典或字典类对象打补丁，并在测试之后将该目录恢复到其初始状态。

_in_dict_ 可以是一个字典或映射类容器。 如果它是一个映射则它必须至少支持获取、设置和删除条目以及对键执行迭代。

_in_dict_ 也可以是一个指定字典名称的字符串，然后将通过导入操作来获取该字典。

_values_ 可以是一个要在字典中设置的值的字典。 _values_ 也可以是一个包含 `(key, value)` 对的可迭代对象。

如果 _clear_ 为真值则该字典将在设置新值之前先被清空。

`patch.dict()` 也可以附带任意关键字参数调用以设置字典中的值。

在 3.8 版本发生变更: 现在当 `patch.dict()` 被用作上下文管理器时将返回被打补丁的字典。now returns the patched dictionary when used as a context manager.

`patch.dict()` 可被用作上下文管理器、装饰器或类装饰器:
~~~
    
    
~~~shell
>>> foo = {}
>>> @patch.dict(foo, {'newkey': 'newvalue'})
... def test():
...     assert foo == {'newkey': 'newvalue'}
...
>>> test()
>>> assert foo == {}
~~~

当被用作类装饰器时 `patch.dict()` 将认可 `patch.TEST_PREFIX` (默认值为 `'test'`) 作为选择所在包装方法的标准:

    
    
~~~shell
>>> import os
>>> import unittest
>>> from unittest.mock import patch
>>> @patch.dict('os.environ', {'newkey': 'newvalue'})
... class TestSample(unittest.TestCase):
...     def test_sample(self):
...         self.assertEqual(os.environ['newkey'], 'newvalue')
~~~

如果你在为你的测试使用不同的前缀，你可以通过设置 `patch.TEST_PREFIX` 来将不同的前缀告知打补丁方。 有关如何修改该值的详情请参阅 TEST_PREFIX。

`patch.dict()` 可被用来向一个字典添加成员，或者简单地让测试修改一个字典，并确保当测试结束时恢复该字典。

    
    
~~~shell
>>> foo = {}
>>> with patch.dict(foo, {'newkey': 'newvalue'}) as patched_foo:
...     assert foo == {'newkey': 'newvalue'}
...     assert patched_foo == {'newkey': 'newvalue'}
...     # You can add, update or delete keys of foo (or patched_foo, it's the same dict)
...     patched_foo['spam'] = 'eggs'
...
>>> assert foo == {}
>>> assert patched_foo == {}
~~~
    
    
~~~shell
>>> import os
>>> with patch.dict('os.environ', {'newkey': 'newvalue'}):
...     print(os.environ['newkey'])
...
newvalue
>>> assert 'newkey' not in os.environ
~~~

可以在 `patch.dict()` 调用中使用关键字来设置字典的值:

    
    
~~~shell
>>> mymodule = MagicMock()
>>> mymodule.function.return_value = 'fish'
>>> with patch.dict('sys.modules', mymodule=mymodule):
...     import mymodule
...     mymodule.function('some', 'args')
...
'fish'
~~~

`patch.dict()` 可以用于实际上不是字典的字典类对象。 它们最少必须支持条目获取、设置、删除以及迭代或成员检测两者中的一个。 这对应于魔术方法 `__getitem__()`, `__setitem__()`, `__delitem__()` 以及 `__iter__()` 或 `__contains__()`。

    
    
~~~shell
>>> class Container:
...     def __init__(self):
...         self.values = {}
...     def __getitem__(self, name):
...         return self.values[name]
...     def __setitem__(self, name, value):
...         self.values[name] = value
...     def __delitem__(self, name):
...         del self.values[name]
...     def __iter__(self):
...         return iter(self.values)
...
>>> thing = Container()
>>> thing['one'] = 1
>>> with patch.dict(thing, one=2, two=3):
...     assert thing['one'] == 2
...     assert thing['two'] == 3
...
>>> assert thing['one'] == 1
>>> assert list(thing) == ['one']
~~~

### patch.multiple¶

patch.multiple( _target_ , _spec =None_, _create =False_, _spec_set =None_, _autospec =None_, _new_callable =None_, _** kwargs_)¶

    

~~~
在单个调用中执行多重补丁。 它接受要打补丁的对象（一个对象或一个通过导入来获取对象的字符串）以及用于补丁的关键字参数:
~~~
    
    
~~~
with patch.multiple(settings, FIRST_PATCH='one', SECOND_PATCH='two'):
    ...
~~~

如果你希望 `patch.multiple()` 为你创建 mock 则要使用 `DEFAULT` 作为值。 在此情况下所创建的 mock 会通过关键字参数传入被装饰的函数，而当 `patch.multiple()` 被用作上下文管理器时则将返回一个字典。

`patch.multiple()` 可以被用作装饰器、类装饰器或上下文管理器。 _spec_ , _spec_set_ , _create_ , _autospec_ 和 _new_callable_ 等参数的含义与 `patch()` 的相同。 这些参数将被应用到 `patch.multiple()` 所打的 _所有_ 补丁。

当被用作类装饰器时 `patch.multiple()` 将认可 `patch.TEST_PREFIX` 作为选择所要包装方法的标准。

如果你希望 `patch.multiple()` 为你创建 mock，那么你可以使用 `DEFAULT` 作为值。 如果你使用 `patch.multiple()` 作为装饰器则所创建的 mock 会作为关键字参数传入被装饰的函数。

    
    
~~~shell
>>> thing = object()
>>> other = object()

>>> @patch.multiple('__main__', thing=DEFAULT, other=DEFAULT)
... def test_function(thing, other):
...     assert isinstance(thing, MagicMock)
...     assert isinstance(other, MagicMock)
...
>>> test_function()
~~~

`patch.multiple()` 可以与其他 `patch` 装饰器嵌套使用，但要将作为关键字传入的参数要放在 `patch()` 所创建的标准参数 _之后_ :

    
    
~~~shell
>>> @patch('sys.exit')
... @patch.multiple('__main__', thing=DEFAULT, other=DEFAULT)
... def test_function(mock_exit, other, thing):
...     assert 'other' in repr(other)
...     assert 'thing' in repr(thing)
...     assert 'exit' in repr(mock_exit)
...
>>> test_function()
~~~

如果 `patch.multiple()` 被用作上下文管理器，则上下文管理器的返回值将是一个以所创建的 mock 的名称为键的字典:

    
    
~~~shell
>>> with patch.multiple('__main__', thing=DEFAULT, other=DEFAULT) as values:
...     assert 'other' in repr(values['other'])
...     assert 'thing' in repr(values['thing'])
...     assert values['thing'] is thing
...     assert values['other'] is other
...
~~~

### 补丁方法: start 和 stop¶

所有补丁对象都具有 `start()` 和 `stop()` 方法。 使用这些方法可以更简单地在 `setUp` 方法上打补丁，还可以让你不必嵌套使用装饰器或 with 语句就能打多重补丁。

要使用这些方法请按正常方式调用 `patch()`, `patch.object()` 或 `patch.dict()` 并保留一个指向所返回 `patcher` 对象的引用。 然后你可以调用 `start()` 来原地打补丁并调用 `stop()` 来恢复它。

如果你使用 `patch()` 来创建自己的 mock 那么将可通过调用 `patcher.start` 来返回它。

    
    
~~~shell
>>> patcher = patch('package.module.ClassName')
>>> from package import module
>>> original = module.ClassName
>>> new_mock = patcher.start()
>>> assert module.ClassName is not original
>>> assert module.ClassName is new_mock
>>> patcher.stop()
>>> assert module.ClassName is original
>>> assert module.ClassName is not new_mock
~~~

此操作的一个典型应用场景是在一个 `TestCase` 的 `setUp` 方法中执行多重补丁:

    
    
~~~shell
>>> class MyTest(unittest.TestCase):
...     def setUp(self):
...         self.patcher1 = patch('package.module.Class1')
...         self.patcher2 = patch('package.module.Class2')
...         self.MockClass1 = self.patcher1.start()
...         self.MockClass2 = self.patcher2.start()
...
...     def tearDown(self):
...         self.patcher1.stop()
...         self.patcher2.stop()
...
...     def test_something(self):
...         assert package.module.Class1 is self.MockClass1
...         assert package.module.Class2 is self.MockClass2
...
>>> MyTest('test_something').run()
~~~

小心

如果你要使用这个技巧则你必须通过调用 `stop` 来确保补丁被“恢复”。 这可能要比你想像的更麻烦，因为如果在 `setUp` 中引发了异常那么 `tearDown` 将不会被调用。 [`unittest.TestCase.addCleanup()`](unittest.md#unittest.TestCase.addCleanup "unittest.TestCase.addCleanup") 可以简化此操作:

    
    
~~~shell
>>> class MyTest(unittest.TestCase):
...     def setUp(self):
...         patcher = patch('package.module.Class')
...         self.MockClass = patcher.start()
...         self.addCleanup(patcher.stop)
...
...     def test_something(self):
...         assert package.module.Class is self.MockClass
...
~~~

一项额外的好处是你不再需要保留指向 `patcher` 对象的引用。

还可以通过使用 `patch.stopall()` 来停止已启动的所有补丁。

patch.stopall()¶

    

~~~
停止所有激活的补丁。 仅会停止通过 `start` 启动的补丁。

### 为内置函数打补丁¶

你可以为一个模块中的任何内置函数打补丁。 以以示例是为内置函数 [`ord()`](functions.md#ord "ord") 打补丁:
~~~
    
    
~~~shell
>>> @patch('__main__.ord')
... def test(mock_ord):
...     mock_ord.return_value = 101
...     print(ord('c'))
...
>>> test()
101
~~~

### TEST_PREFIX¶

所有补丁都可被用作类装饰器。 当以这种方式使用时它们将会包装类中的每个测试方法。 补丁会将以名字以 `'test'` 开头的方法识别为测试方法。 这与 [`unittest.TestLoader`](unittest.md#unittest.TestLoader "unittest.TestLoader") 查找测试方法的默认方式相同。

你可能会想要为你的测试使用不同的前缀。 你可以通过设置 `patch.TEST_PREFIX` 来告知打补丁方不同的前缀:

    
    
~~~shell
>>> patch.TEST_PREFIX = 'foo'
>>> value = 3
>>>
>>> @patch('__main__.value', 'not three')
... class Thing:
...     def foo_one(self):
...         print(value)
...     def foo_two(self):
...         print(value)
...
>>>
>>> Thing().foo_one()
not three
>>> Thing().foo_two()
not three
>>> value
3
~~~

### 嵌套补丁装饰器¶

如果你想要应用多重补丁那么你可以简单地堆叠多个装饰器。

你可以使用以下模式来堆叠多个补丁装饰器:

    
    
~~~shell
>>> @patch.object(SomeClass, 'class_method')
... @patch.object(SomeClass, 'static_method')
... def test(mock1, mock2):
...     assert SomeClass.static_method is mock1
...     assert SomeClass.class_method is mock2
...     SomeClass.static_method('foo')
...     SomeClass.class_method('bar')
...     return mock1, mock2
...
>>> mock1, mock2 = test()
>>> mock1.assert_called_once_with('foo')
>>> mock2.assert_called_once_with('bar')
~~~

请注意装饰器是从下往上被应用的。 这是 Python 应用装饰器的标准方式。 被创建并传入你的测试函数的 mock 的顺序也将匹配这个顺序。

### 补丁的位置¶

`patch()` 通过（临时性地）修改某一个对象的 _名称_ 指向另一个对象来发挥作用。 可以有多个名称指向任意单独对象，因此要让补丁起作用你必须确保已为被测试的系统所使用的名称打上补丁。

基本原则是你要在对象 _被查找_ 的地方打补丁，这不一定就是它被定义的地方。 一组示例将有助于厘清这一点。

想像我们有一个想要测试的具有如下结构的项目:

    
    
~~~
a.py
    -> Defines SomeClass

b.py
    -> from a import SomeClass
    -> some_function instantiates SomeClass
~~~

现在我们要测试 `some_function` 但我们想使用 `patch()` 来模拟 `SomeClass`。 问题在于当我们导入模块 b 时，我们将必须让它从模块 a 导入 `SomeClass`。 如果我们使用 `patch()` 来模拟 `a.SomeClass` 那么它将不会对我们的测试造成影响；模块 b 已经拥有对 _真正的_ `SomeClass` 的引用因此看上去我们的补丁不会有任何影响。

关键在于对 `SomeClass` 打补丁操作是在它被使用（或它被查找）的地方。 在此情况下实际上 `some_function` 将在模块 b 中查找 `SomeClass`，而我们已经在那里导入了它。 补丁看上去应该是这样:

    
    
~~~
@patch('b.SomeClass')
~~~

但是，再考虑另一个场景，其中不是 `from a import SomeClass` 而是模块 b 执行了 `import a` 并且 `some_function` 使用了 `a.SomeClass`。 这两个导入形式都很常见。 在这种情况下我们要打补丁的类将在该模块中被查找因而我们必须改为对 `a.SomeClass` 打补丁:

    
    
~~~
@patch('a.SomeClass')
~~~

### 对描述器和代理对象打补丁¶

patch 和 patch.object 都能正确地对描述器打补丁并恢复：包含类方法、静态方法和特征属性。 你应当在 _类_ 而不是实例上为它们打补丁。 它们也适用于代理属性访问的 _部分_ 对象，例如 [django settings object](https://web.archive.org/web/20200603181648/http://www.voidspace.org.uk/python/weblog/arch_d7_2010_12_04.shtml#e1198)。

## MagicMock 与魔术方法支持¶

### 模拟魔术方法¶

`Mock` 支持模拟 Python 协议方法，或称“魔术方法”。 这允许 mock 对象替代容器或其他实现了 Python 协议的对象。

因为查找魔术方法的方式不同于普通方法 [2]，这种支持采用了特别的实现。 这意味着只有特定的魔术方法受到支持。 受支持的是 _几乎_ 所有魔术方法。 如果有你需要但被遗漏的请告知我们。

你可以通过在某个函数或 mock 实例中设置魔术方法来模拟它们。 如果你是使用函数则它 _必须_ 接受 `self` 作为第一个参数 [3]。

    
    
~~~shell
>>> def __str__(self):
...     return 'fooble'
...
>>> mock = Mock()
>>> mock.__str__ = __str__
>>> str(mock)
'fooble'
~~~
    
    
~~~shell
>>> mock = Mock()
>>> mock.__str__ = Mock()
>>> mock.__str__.return_value = 'fooble'
>>> str(mock)
'fooble'
~~~
    
    
~~~shell
>>> mock = Mock()
>>> mock.__iter__ = Mock(return_value=iter([]))
>>> list(mock)
[]
~~~

一个这样的应用场景是在 [`with`](../reference/compound_stmts.md#with) 语句中模拟作为上下文管理器的对象:

    
    
~~~shell
>>> mock = Mock()
>>> mock.__enter__ = Mock(return_value='foo')
>>> mock.__exit__ = Mock(return_value=False)
>>> with mock as m:
...     assert m == 'foo'
...
>>> mock.__enter__.assert_called_with()
>>> mock.__exit__.assert_called_with(None, None, None)
~~~

对魔术方法的调用不会在 `method_calls` 中出现，但它们会被记录在 `mock_calls` 中。

备注

如果你使用 _spec_ 关键字参数来创建 mock 那么尝试设置不包含在 spec 中的魔术方法将引发 [`AttributeError`](exceptions.md#AttributeError "AttributeError")。

受支持魔术方法的完整列表如下:

  * `__hash__`, `__sizeof__`, `__repr__` 和 `__str__`

  * `__dir__`, `__format__` 和 `__subclasses__`

  * `__round__`, `__floor__`, `__trunc__` 和 `__ceil__`

  * 比较运算: `__lt__`, `__gt__`, `__le__`, `__ge__`, `__eq__` 和 `__ne__`

  * 容器方法: `__getitem__`, `__setitem__`, `__delitem__`, `__contains__`, `__len__`, `__iter__`, `__reversed__` 和 `__missing__`

  * 上下文管理器: `__enter__`, `__exit__`, `__aenter__` 和 `__aexit__`

  * 单目数值运算方法: `__neg__`, `__pos__` 和 `__invert__`

  * 数值运算方法（包括右侧和原地两种形式）: `__add__`, `__sub__`, `__mul__`, `__matmul__`, `__truediv__`, `__floordiv__`, `__mod__`, `__divmod__`, `__lshift__`, `__rshift__`, `__and__`, `__xor__`, `__or__` 和 `__pow__`

  * 数值转换方法: `__complex__`, `__int__`, `__float__` 和 `__index__`

  * 描述器方法: `__get__`, `__set__` and `__delete__`

  * 封存方法: `__reduce__`, `__reduce_ex__`, `__getinitargs__`, `__getnewargs__`, `__getstate__` 和 `__setstate__`

  * 文件系统路径表示: `__fspath__`

  * 异步迭代方法: `__aiter__` and `__anext__`

在 3.8 版本发生变更: 增加了对 [`os.PathLike.__fspath__()`](os.md#os.PathLike.__fspath__ "os.PathLike.__fspath__") 的支持。

在 3.8 版本发生变更: 增加了对 `__aenter__`, `__aexit__`, `__aiter__` 和 `__anext__` 的支持。

下列方法均存在但是 _不受_ 支持，因为它们或者被 mock 所使用，或者无法动态设置，或者可能导致问题:

  * `__getattr__`, `__setattr__`, `__init__` 和 `__new__`

  * `__prepare__`, `__instancecheck__`, `__subclasscheck__`, `__del__`

### MagicMock¶

存在两个版本的 `MagicMock`: `MagicMock` 和 `NonCallableMagicMock`.

_class _unittest.mock.MagicMock( _* args_, _** kw_)¶

    

~~~
`MagicMock` 是包含了大部分魔术方法的默认实现的 `Mock` 的子类。 你可以使用 `MagicMock` 而无须自行配置魔术方法。without having to configure the magic methods yourself.

构造器形参的含义与 `Mock` 的相同。

如果你使用了 _spec_ 或 _spec_set_ 参数则将 _只有_ 存在于 spec 中的魔术方法会被创建。

_class _unittest.mock.NonCallableMagicMock( _* args_, _** kw_)¶
~~~
    

~~~
`MagicMock` 的不可调用版本。

其构造器的形参具有与 `MagicMock` 相同的含义，区别在于 _return_value_ 和 _side_effect_ 在不可调用的 mock 上没有意义。

魔术方法是通过 `MagicMock` 对象来设置的，因此你可以用通常的方式来配置它们并使用它们:
~~~
    
    
~~~shell
>>> mock = MagicMock()
>>> mock[3] = 'fish'
>>> mock.__setitem__.assert_called_with(3, 'fish')
>>> mock.__getitem__.return_value = 'result'
>>> mock[2]
'result'
~~~

在默认情况下许多协议方法都需要返回特定类型的对象。 这些方法都预先配置了默认的返回值，以便它们在你对返回值不感兴趣时可以不做任何事就能被使用。 如果你想要修改默认值则你仍然可以手动 _设置_ 返回值。

方法及其默认返回值:

  * `__lt__`: `NotImplemented`

  * `__gt__`: `NotImplemented`

  * `__le__`: `NotImplemented`

  * `__ge__`: `NotImplemented`

  * `__int__`: `1`

  * `__contains__`: `False`

  * `__len__`: `0`

  * `__iter__`: `iter([])`

  * `__exit__`: `False`

  * `__aexit__`: `False`

  * `__complex__`: `1j`

  * `__float__`: `1.0`

  * `__bool__`: `True`

  * `__index__`: `1`

  * `__hash__`: mock 的默认 hash

  * `__str__`: mock 的默认 str

  * `__sizeof__`: mock 的默认 sizeof

例如:

    
    
~~~shell
>>> mock = MagicMock()
>>> int(mock)
1
>>> len(mock)
0
>>> list(mock)
[]
>>> object() in mock
False
~~~

两个相等性方法 `__eq__()` 和 `__ne__()` 是特殊的。 它们基于标识号进行默认的相等性比较，使用 `side_effect` 属性，除非你修改它们的返回值以返回其他内容:

    
    
~~~shell
>>> MagicMock() == 3
False
>>> MagicMock() != 3
True
>>> mock = MagicMock()
>>> mock.__eq__.return_value = True
>>> mock == 3
True
~~~

`MagicMock.__iter__()` 的返回值可以是任意可迭代对象而不要求必须是迭代器:

    
    
~~~shell
>>> mock = MagicMock()
>>> mock.__iter__.return_value = ['a', 'b', 'c']
>>> list(mock)
['a', 'b', 'c']
>>> list(mock)
['a', 'b', 'c']
~~~

如果返回值 _是_ 迭代器，则对其执行一次迭代就会将它耗尽因而后续执行的迭代将会输出空列表:

    
    
~~~shell
>>> mock.__iter__.return_value = iter(['a', 'b', 'c'])
>>> list(mock)
['a', 'b', 'c']
>>> list(mock)
[]
~~~

`MagicMock` 已配置了所有受支持的魔术方法，只有某些晦涩和过时的魔术方法是例外。 如果你需要仍然可以设置它们。

在 `MagicMock` 中受到支持但默认未被设置的魔术方法有:

  * `__subclasses__`

  * `__dir__`

  * `__format__`

  * `__get__`, `__set__` 和 `__delete__`

  * `__reversed__` 和 `__missing__`

  * `__reduce__`, `__reduce_ex__`, `__getinitargs__`, `__getnewargs__`, `__getstate__` 和 `__setstate__`

  * `__getformat__`

[2]

魔术方法 _应当_ 是在类中而不是在实例中查找。 不同的 Python 版本对这个规则的应用并不一致。 受支持的协议方法应当适用于所有受支持的 Python 版本。

[3]

该函数基本上是与类挂钩的，但每个 `Mock` 实例都会与其他实例保持隔离。

## 辅助对象¶

### sentinel¶

unittest.mock.sentinel¶

    

~~~
`sentinel` 对象提供了一种为你的测试提供独特对象的便捷方式。

属性是在你通过名称访问它们时按需创建的。 访问相同的属性将始终返回相同的对象。 返回的对象会有一个合理的 repr 以使测试失败消息易于理解。

在 3.7 版本发生变更: 现在 `sentinel` 属性会在它们被 [`copy`](copy.md#module-copy "copy: Shallow and deep copy operations.") 或 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.") 时保存其标识。

在测试时你可能需要测试是否有一个特定的对象作为参数被传给了另一个方法，或是被其返回。 通常的做法是创建一个指定名称的 sentinel 对象来执行这种测试。 `sentinel` 提供了一种创建和测试此类对象的标识的便捷方式。

在这个示例中我们为 `method` 打上便捷补丁以返回 `sentinel.some_object`:
~~~
    
    
~~~shell
>>> real = ProductionClass()
>>> real.method = Mock(name="method")
>>> real.method.return_value = sentinel.some_object
>>> result = real.method()
>>> assert result is sentinel.some_object
>>> result
sentinel.some_object
~~~

### DEFAULT¶

unittest.mock.DEFAULT¶

    

~~~
`DEFAULT` 对象是一个预先创建的 sentinel (实际为 `sentinel.DEFAULT`)。 它可被 `side_effect` 函数用来指明其应当使用正常的返回值。

### call¶

unittest.mock.call( _* args_, _** kwargs_)¶
~~~
    

~~~
`call()` 是一个可创建更简单断言的辅助对象，用于同 `call_args`, `call_args_list`, `mock_calls` 和 `method_calls` 进行比较。 `call()` 也可配合 `assert_has_calls()` 使用。
~~~
    
    
~~~shell
>>> m = MagicMock(return_value=None)
>>> m(1, 2, a='foo', b='bar')
>>> m()
>>> m.call_args_list == [call(1, 2, a='foo', b='bar'), call()]
True
~~~

call.call_list()¶

    

~~~
对于代表多个调用的 call 对象，`call_list()` 将返回一个包含所有中间调用以及最终调用的列表。

`call_list` 特别适用于创建针对“链式调用”的断言。 链式调用是指在一行代码中执行的多个调用。 这将使得一个 mock 的中存在多个条目 `mock_calls`。 手动构造调用的序列将会很烦琐。

`call_list()` 可以根据同一个链式调用构造包含多个调用的序列:
~~~
    
    
~~~shell
>>> m = MagicMock()
>>> m(1).method(arg='foo').other('bar')(2.0)
<MagicMock name='mock().method().other()()' id='...'>
>>> kall = call(1).method(arg='foo').other('bar')(2.0)
>>> kall.call_list()
[call(1),
 call().method(arg='foo'),
 call().method().other('bar'),
 call().method().other()(2.0)]
>>> m.mock_calls == kall.call_list()
True
~~~

根据构造方式的不同，`call` 对象可以是一个 (位置参数, 关键字参数) 或 (名称, 位置参数, 关键字参数) 元组。 当你自行构造它们时这没有什么关系，但是 `Mock.call_args`, `Mock.call_args_list` 和 `Mock.mock_calls` 属性当中的 `call` 对象可以被反查以获取它们所包含的单个参数。

`Mock.call_args` 和 `Mock.call_args_list` 中的 `call` 对象是 (位置参数, 关键字参数) 二元组而 `Mock.mock_calls` 中以及你自行构造的 `call` 对象则是 (名称, 位置参数, 关键字参数) 三元组。

你可以使用它们作为“元组”的特性来为更复杂的内省和断言功能获取单个参数。 位置参数是一个元组（如无位置参数则为空元组）而关键字参数是一个字典:

    
    
~~~shell
>>> m = MagicMock(return_value=None)
>>> m(1, 2, 3, arg='one', arg2='two')
>>> kall = m.call_args
>>> kall.args
(1, 2, 3)
>>> kall.kwargs
{'arg': 'one', 'arg2': 'two'}
>>> kall.args is kall[0]
True
>>> kall.kwargs is kall[1]
True
~~~
    
    
~~~shell
>>> m = MagicMock()
>>> m.foo(4, 5, 6, arg='two', arg2='three')
<MagicMock name='mock.foo()' id='...'>
>>> kall = m.mock_calls[0]
>>> name, args, kwargs = kall
>>> name
'foo'
>>> args
(4, 5, 6)
>>> kwargs
{'arg': 'two', 'arg2': 'three'}
>>> name is m.mock_calls[0][0]
True
~~~

### create_autospec¶

unittest.mock.create_autospec( _spec_ , _spec_set =False_, _instance =False_, _** kwargs_)¶

    

~~~
使用另一对象作为 spec 来创建 mock 对象。 mock 的属性将使用 _spec_ 对象上的对应属性作为其 spec。

被模拟的函数或方法的参数将会被检查以确保它们是附带了正确的签名被调用的。

如果 _spec_set_ 为 `True` 则尝试设置不存在于 spec 对象中的属性将引发 [`AttributeError`](exceptions.md#AttributeError "AttributeError")。

如果将类用作 spec 则 mock（该类的实例）的返回值将为这个 spec。 你可以通过传入 `instance=True` 来将某个类用作一个实例对象的 spec。 被返回的 mock 将仅在该 mock 的实例为可调用对象时才会是可调用对象。

`create_autospec()` 也接受被传入所创建 mock 的构造器的任意关键字参数。

请参阅 自动 spec 来了解如何通过 `create_autospec()` 以及 `patch()` 的 _autospec_ 参数来自动设置 spec。

在 3.8 版本发生变更: 如果目标为异步函数那么 `create_autospec()` 现在将返回一个 `AsyncMock`。

### ANY¶

unittest.mock.ANY¶
~~~
    

~~~
有时你可能需要设置关于要模拟的调用中的 _某些_ 参数的断言，但是又不想理会其他参数或者想要从 `call_args` 中单独拿出它们并针对它们设置更复杂的断言。

为了忽略某些参数你可以传入与 _任意对象_ 相等的对象。 这样再调用 `assert_called_with()` 和 `assert_called_once_with()` 时无论传入什么参数都将执行成功。
~~~
    
    
~~~shell
>>> mock = Mock(return_value=None)
>>> mock('foo', bar=object())
>>> mock.assert_called_once_with('foo', bar=ANY)
~~~

`ANY` 也可被用在与 `mock_calls` 这样的调用列表相比较的场合:

    
    
~~~shell
>>> m = MagicMock(return_value=None)
>>> m(1)
>>> m(1, 2)
>>> m(object())
>>> m.mock_calls == [call(1), call(1, 2), ANY]
True
~~~

### FILTER_DIR¶

unittest.mock.FILTER_DIR¶

    

~~~
`FILTER_DIR` 是一个控制 mock 对象响应 [`dir()`](functions.md#dir "dir") 的方式的模块组变量。 默认值为 `True`，这将使用下文所描述的过滤操作，以便只显示有用的成员。 如果你不喜欢这样的过滤，或是出于诊断目的需要将其关闭，则应设置 `mock.FILTER_DIR = False`。

当启用过滤时，`dir(some_mock)` 将只显示有用的属性并将包括正常情况下不会被显示的任何动态创建的属性。 如果 mock 是使用 _spec_ (当然也可以是 _autospec_ ) 创建的则原有的所有属性都将被显示，即使它们还未被访问过:
~~~
    
    
~~~shell
>>> dir(Mock())
['assert_any_call',
 'assert_called',
 'assert_called_once',
 'assert_called_once_with',
 'assert_called_with',
 'assert_has_calls',
 'assert_not_called',
 'attach_mock',
 ...
>>> from urllib import request
>>> dir(Mock(spec=request))
['AbstractBasicAuthHandler',
 'AbstractDigestAuthHandler',
 'AbstractHTTPHandler',
 'BaseHandler',
 ...
~~~

许多不太有用的（是 `Mock` 的而不是被模拟对象的私有成员）开头带有下划线和双下划线的属性已从在 `Mock` 上对 [`dir()`](functions.md#dir "dir") 的调用的结果中被过滤。 如果你不喜欢此行为你可以通过设置模块级开关 `FILTER_DIR` 来将其关闭:

    
    
~~~shell
>>> from unittest import mock
>>> mock.FILTER_DIR = False
>>> dir(mock.Mock())
['_NonCallableMock__get_return_value',
 '_NonCallableMock__get_side_effect',
 '_NonCallableMock__return_value_doc',
 '_NonCallableMock__set_return_value',
 '_NonCallableMock__set_side_effect',
 '__call__',
 '__class__',
 ...
~~~

你也可以选择使用 `vars(my_mock)` (实例成员) 和 `dir(type(my_mock))` (类型成员) 来绕过不考虑 `mock.FILTER_DIR` 的过滤。

### mock_open¶

unittest.mock.mock_open( _mock =None_, _read_data =None_)¶

    

~~~
创建 mock 来代替使用 [`open()`](functions.md#open "open") 的辅助函数。 它对于 [`open()`](functions.md#open "open") 被直接调用或被用作上下文管理器的情况都适用。

_mock_ 参数是要配置的 mock 对象。 如为 `None` (默认值) 则将为你创建一个 `MagicMock`，其 API 会被限制为可用于标准文件处理的方法或属性。

_read_data_ 是供文件处理的 `read()`, [`readline()`](io.md#io.IOBase.readline "io.IOBase.readline") 和 [`readlines()`](io.md#io.IOBase.readlines "io.IOBase.readlines") 方法返回的字符串。 调用这些方法将会从 _read_data_ 获取数据直到它被耗尽。 对这些方法的模拟是相当简化的：每次 _mock_ 被调用时， _read_data_ 都将从头开始。 如果你需要对你提供给测试代码的数据有更多控制那么你将需要自行定制这个 mock。 如果这还不够用，那么通过一些 [PyPI](https://pypi.org) 上的内存文件系统包可以提供更真实的测试文件系统。

在 3.4 版本发生变更: 增加了对 [`readline()`](io.md#io.IOBase.readline "io.IOBase.readline") 和 [`readlines()`](io.md#io.IOBase.readlines "io.IOBase.readlines") 的支持。 对 `read()` 的模拟被改为消耗 _read_data_ 而不是每次调用时返回它。

在 3.5 版本发生变更: _read_data_ 现在会在每次 _mock_ 的调用时重置。

在 3.8 版本发生变更: 为实现增加了 `__iter__()` 以使迭代操作（例如在 for 循环中）能正确地消耗 _read_data_ 。

将 [`open()`](functions.md#open "open") 用作上下文管理器一确保你的文件处理被正确关闭的最佳方式因而十分常用:
~~~
    
    
~~~
with open('/some/path', 'w') as f:
    f.write('something')
~~~

这里的问题在于即使你模拟了对 [`open()`](functions.md#open "open") 的调用但被用作上下文管理器（并调用其 `__enter__()` 和 `__exit__()` 方法）的也是 _被返回的对象_ 。

通过 `MagicMock` 来模拟上下文管理器是十分常见且十分麻烦的因此需要使用一个辅助函数。

    
    
~~~shell
>>> m = mock_open()
>>> with patch('__main__.open', m):
...     with open('foo', 'w') as h:
...         h.write('some stuff')
...
>>> m.mock_calls
[call('foo', 'w'),
 call().__enter__(),
 call().write('some stuff'),
 call().__exit__(None, None, None)]
>>> m.assert_called_once_with('foo', 'w')
>>> handle = m()
>>> handle.write.assert_called_once_with('some stuff')
~~~

以及针对读取文件:

    
    
~~~shell
>>> with patch('__main__.open', mock_open(read_data='bibble')) as m:
...     with open('foo') as h:
...         result = h.read()
...
>>> m.assert_called_once_with('foo')
>>> assert result == 'bibble'
~~~

### 自动 spec¶

自动 spec 是基于现有 mock 的 `spec` 特性。 它将 mock 的 api 限制为原始对象 (spec) 的 api，但它是递归（惰性实现）的因而 mock 的属性只有与 spec 的属性相同的 api。 除此之外被模块的函数 / 方法具有与原对应物相同的调用签名因此如果它们被不正确地调用时会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

在我开始解释自动 spec 如何运作之前，先说明一下它的必要性。

`Mock` 是一个非常强大而灵活的对象，但当它被用来模拟来自被测试系统的对象时有两个缺陷。 其中一个缺陷是 `Mock` 的 api 特有的而另一个则是使用 mock 对象时普遍存在的问题。

第一个问题为 `Mock` 所专属。 `Mock` 具有两个特别好用的断言方法: `assert_called_with()` 和 `assert_called_once_with()`。

    
    
~~~shell
>>> mock = Mock(name='Thing', return_value=None)
>>> mock(1, 2, 3)
>>> mock.assert_called_once_with(1, 2, 3)
>>> mock(1, 2, 3)
>>> mock.assert_called_once_with(1, 2, 3)
Traceback (most recent call last):
 ...
AssertionError: Expected 'mock' to be called once. Called 2 times.
~~~

因为 mock 会根据需要自动创建属性，并允许你附带任意参数调用它们，所以如果你写错了这两个断言方法则你的断言将会失效:

    
    
~~~shell
>>> mock = Mock(name='Thing', return_value=None)
>>> mock(1, 2, 3)
>>> mock.assret_called_once_with(4, 5, 6)  # Intentional typo!
~~~

你的测试将因这个拼写错误而静默并且错误地通过。

第二个问题是 mock 操作中普遍存在的。 如果你重构了你的部分代码，例如修改了一些成员的名称等等，则代码中任何仍在使用 _旧 api_ 但其使用的是 mock 而非真实对象的测试仍将通过。 这意味着即使你的代码已被破坏你的测试却仍可能全部通过。

请注意这是为什么你在单元测试之外还需要集成测试的原因之一。 孤立地测试每个部分时全都正常而顺滑，但是如果你没有测试你的各个单元“联成一体”的情况如何那么就仍然存在测试可以发现大量错误的空间。

`mock` 已经提供了一个对此有帮助的特性，称为 spec 控制。 如果你使用类或实例作为一个 mock 的 `spec` 那么你将仅能访问 mock 中只存在于实际的类中的属性。

    
    
~~~shell
>>> from urllib import request
>>> mock = Mock(spec=request.Request)
>>> mock.assret_called_with  # Intentional typo!
Traceback (most recent call last):
 ...
AttributeError: Mock object has no attribute 'assret_called_with'
~~~

这个 spec 仅会应用于 mock 本身，因此对于 mock 中的任意方法我们仍然面临相同的问题:

    
    
~~~shell
>>> mock.has_data()
<mock.Mock object at 0x...>
>>> mock.has_data.assret_called_with()  # Intentional typo!
~~~

自动 spec 解决了这个问题。 你可以将 `autospec=True` 传给 `patch()` / `patch.object()` 或是使用 `create_autospec()` 函数来创建带有 spec 的 mock。 如果你是将 `autospec=True` 参数传给 `patch()` 那么被替代的那个对象将被用作 spec 对象。 因为 spec 控制是“惰性地”执行的（spec 在 mock 中的属性被访问时才会被创建）所以即使是非常复杂或深度嵌套的对象（例如需要导入本身已导入了多个模块的模块）你也可以使用它而不会有太大的性能损失。

以下是一个实际应用的示例:

    
    
~~~shell
>>> from urllib import request
>>> patcher = patch('__main__.request', autospec=True)
>>> mock_request = patcher.start()
>>> request is mock_request
True
>>> mock_request.Request
<MagicMock name='request.Request' spec='Request' id='...'>
~~~

你可以看到 `request.Request` 有一个 spec。 `request.Request` 的构造器接受两个参数 (其中一个是 _self_ )。 如果我们尝试不正确地调用它则将导致:

    
    
~~~shell
>>> req = request.Request()
Traceback (most recent call last):
 ...
TypeError: <lambda>() takes at least 2 arguments (1 given)
~~~

该 spec 也将应用于被实例化的类（即附带i.e. the return value of spec 的 mock 的返回值）:

    
    
~~~shell
>>> req = request.Request('foo')
>>> req
<NonCallableMagicMock name='request.Request()' spec='Request' id='...'>
~~~

`Request` 对象是不可调用对象，因此实例化我们的被模拟 `request.Request` 的返回值是一个不可调用的 mock。 有了这个 spec 我们的断言中出现任何拼写问题都将引发正确的错误:

    
    
~~~shell
>>> req.add_header('spam', 'eggs')
<MagicMock name='request.Request().add_header()' id='...'>
>>> req.add_header.assret_called_with  # Intentional typo!
Traceback (most recent call last):
 ...
AttributeError: Mock object has no attribute 'assret_called_with'
>>> req.add_header.assert_called_with('spam', 'eggs')
~~~

在许多情况下你将只需将 `autospec=True` 添加到你现有的 `patch()` 调用中即可防止拼写错误和 api 变化所导致的问题。

除了通过 `patch()` 来使用 _autospec_ 还有一个 `create_autospec()` 可以直接创建带有自动 spec 的 mock:

    
    
~~~shell
>>> from urllib import request
>>> mock_request = create_autospec(request)
>>> mock_request.Request('foo', 'bar')
<NonCallableMagicMock name='mock.Request()' spec='Request' id='...'>
~~~

不过这并非没有缺点和限制，这也就是为什么它不是默认行为。 为了知道在 spec 对象上有哪些属性是可用的，autospec 必须对 spec 进行自省（访问其属性）。 当你遍历 mock 上的属性时在原始对象上的对应遍历也将在底层进行。 如果你的任何带 spec 的对象具有可触发代码执行的特征属性或描述器则你可能会无法使用 autospec。 在另一方面更好的的做法是将你的对象设计为可以安全地执行自省 [4]。

一个更严重的问题在于实例属性往往是在 `__init__()` 方法中被创建而在类中完全不存在。 _autospec_ 无法获取动态创建的属性而使得 api 被限制于可见的属性。

    
    
~~~shell
>>> class Something:
...   def __init__(self):
...     self.a = 33
...
>>> with patch('__main__.Something', autospec=True):
...   thing = Something()
...   thing.a
...
Traceback (most recent call last):
  ...
AttributeError: Mock object has no attribute 'a'
~~~

要解决这个问题有几种不同的方式。 最容易但多少有些烦扰的方式是简单地在 mock 创建完成后再设置所需的属性。 Just because _autospec_ 只是不允许你获取不存在于 spec 上的属性但并不会阻止你设置它们:

    
    
~~~shell
>>> with patch('__main__.Something', autospec=True):
...   thing = Something()
...   thing.a = 33
...
~~~

_spec_ 和 _autospec_ 都有更严格的版本 _确实能_ 阻止你设置不存在的属性。 这在你希望确保你的代码只能 _设置_ 有效的属性时也很有用，但显然它会阻止下面这个特定的应用场景:

    
    
~~~shell
>>> with patch('__main__.Something', autospec=True, spec_set=True):
...   thing = Something()
...   thing.a = 33
...
Traceback (most recent call last):
 ...
AttributeError: Mock object has no attribute 'a'
~~~

解决此问题的最好方式可能是添加类属性作为在 `__init__()` 中初始化的实例属性的默认值。 请注意如果你只在 `__init__()` 中设置默认属性那么通过类属性来提供它们（当然会在实例之间共享）也将有更快的速度。 例如

    
    
~~~
class Something:
    a = 33
~~~

这带来了另一个问题。 为今后将变为不同类型对象的那些成员提供默认值 `None` 是比较常见的做法。 `None` 作为 spec 是没有用处的，因为它会使你无法访问 _any_ 任何属性或方法。 由于 `None` 作为 spec 将 _永远不会_ 有任何用处，并且有可能要指定某个通常为其他类型的成员，因此 autospec 不会为被设为 `None` 的成员使用 spec。 它们将为普通的 mock (嗯 —— 应为 MagicMocks):

    
    
~~~shell
>>> class Something:
...     member = None
...
>>> mock = create_autospec(Something)
>>> mock.member.foo.bar.baz()
<MagicMock name='mock.member.foo.bar.baz()' id='...'>
~~~

如果你不喜欢修改你的生产类来添加默认值那么还有其他的选项。 其中之一是简单地使用一个实例而非类作为 spec。 另一选项则是创建一个生产类的子类并向该子类添加默认值而不影响到生产类。 这两个选项都需要你使用一个替代对象作为 spec。 值得庆幸的是 `patch()` 支持这样做 —— 你可以简单地传入替代对象作为 _autospec_ 参数:

    
    
~~~shell
>>> class Something:
...   def __init__(self):
...     self.a = 33
...
>>> class SomethingForTest(Something):
...   a = 33
...
>>> p = patch('__main__.Something', autospec=SomethingForTest)
>>> mock = p.start()
>>> mock.a
<NonCallableMagicMock name='Something.a' spec='int' id='...'>
~~~

[4]

这仅适用于类或已实例化的对象。 调用一个被模拟的类来创建一个 mock 实例 _不会_ 创建真的实例。 只有属性查找 —— 以及对 [`dir()`](functions.md#dir "dir") 的调用 —— 会被执行。

### 将 mock 封包¶

unittest.mock.seal( _mock_ )¶

    

~~~
封包将在访问被封包的 mock 的属性或其任何已经被递归地模拟的属性时禁止自动创建 mock。

如果一个带有名称或 spec 的 mock 实例被分配给一个属性则将不会在封包链中处理它。 这可以防止人们对 mock 对象的固定部分执行封包。
~~~
    
    
~~~shell
>>> mock = Mock()
>>> mock.submock.attribute1 = 2
>>> mock.not_submock = mock.Mock(name="sample_name")
>>> seal(mock)
>>> mock.new_attribute  # This will raise AttributeError.
>>> mock.submock.attribute2  # This will raise AttributeError.
>>> mock.not_submock.attribute2  # This won't raise.
~~~

在 3.7 版本加入.

