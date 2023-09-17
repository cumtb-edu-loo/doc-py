# [`unittest.mock`](unittest.mock.md#module-unittest.mock "unittest.mock: Mock object library.") \--- 上手指南¶

在 3.3 版本加入.

## 使用 mock¶

### 模拟方法调用¶

使用 [`Mock`](unittest.mock.md#unittest.mock.Mock "unittest.mock.Mock") 的常见场景：

  * 模拟函数调用

  * 记录在对象上的方法调用

你可能需要替换一个对象上的方法，用于确认此方法被系统中的其他部分调用过，并且调用时使用了正确的参数。

    
    
~~~shell
>>> real = SomeClass()
>>> real.method = MagicMock(name='method')
>>> real.method(3, 4, 5, key='value')
<MagicMock name='method()' id='...'>
~~~

使用了 mock (本例中的 `real.method`) 之后，它有方法和属性可以让你针对它是被如何使用的下断言。

备注

在多数示例中，[`Mock`](unittest.mock.md#unittest.mock.Mock "unittest.mock.Mock") 与 [`MagicMock`](unittest.mock.md#unittest.mock.MagicMock "unittest.mock.MagicMock") 两个类可以相互替换，而 `MagicMock` 是一个更适用的类，通常情况下，使用它就可以了。

如果 mock 被调用，它的 [`called`](unittest.mock.md#unittest.mock.Mock.called "unittest.mock.Mock.called") 属性就会变成 `True`，更重要的是，我们可以使用 [`assert_called_with()`](unittest.mock.md#unittest.mock.Mock.assert_called_with "unittest.mock.Mock.assert_called_with") 或者 [`assert_called_once_with()`](unittest.mock.md#unittest.mock.Mock.assert_called_once_with "unittest.mock.Mock.assert_called_once_with") 方法来确认它在被调用时使用了正确的参数。

在如下的测试示例中，验证对于 `ProductionClass().method` 的调用会导致 `something` 的调用。

    
    
~~~shell
>>> class ProductionClass:
...     def method(self):
...         self.something(1, 2, 3)
...     def something(self, a, b, c):
...         pass
...
>>> real = ProductionClass()
>>> real.something = MagicMock()
>>> real.method()
>>> real.something.assert_called_once_with(1, 2, 3)
~~~

### 对象上的方法调用的 mock¶

上一个例子中我们直接在对象上给方法打补丁以检查它是否被正确地调用。 另一个常见的用例是将一个对象传给一个方法（或被测试系统的某个部分）然后检查它是否以正确的方式被使用。

下面这个简单的 `ProductionClass` 具有一个 `closer` 方法。 如果它附带一个对象被调用那么它就会调用其中的 `close`。

    
    
~~~shell
>>> class ProductionClass:
...     def closer(self, something):
...         something.close()
...
~~~

所以为了测试它我们需要传入一个带有 `close` 方法的对象并检查它是否被正确地调用。

    
    
~~~shell
>>> real = ProductionClass()
>>> mock = Mock()
>>> real.closer(mock)
>>> mock.close.assert_called_with()
~~~

我们不需要做任何事来在我们的 mock 上提供 'close' 方法。 访问 close 的操作就会创建它。 因此，如果 'close' 还未被调用那么在测试时访问它就将创建它，但是 [`assert_called_with()`](unittest.mock.md#unittest.mock.Mock.assert_called_with "unittest.mock.Mock.assert_called_with") 则会引发一个失败的异常。

### 模拟类¶

一个常见的用例是模拟被测试的代码所实例化的类。 当你给一个类打上补丁，该类就会被替换为一个 mock。 实例是通过 _调用该类_ 来创建的。 这意味着你要通过查看被模拟类的返回值来访问“mock 实例”。

在下面的例子中我们有一个函数 `some_function` 实例化了 `Foo` 并调用该实例中的一个方法。 对 [`patch()`](unittest.mock.md#unittest.mock.patch "unittest.mock.patch") 的调用会将类 `Foo` 替换为一个 mock。 `Foo` 实例是调用该 mock 的结果，所以它是通过修改 [`return_value`](unittest.mock.md#unittest.mock.Mock.return_value "unittest.mock.Mock.return_value") 来配置的。

    
    
~~~shell
>>> def some_function():
...     instance = module.Foo()
...     return instance.method()
...
>>> with patch('module.Foo') as mock:
...     instance = mock.return_value
...     instance.method.return_value = 'the result'
...     result = some_function()
...     assert result == 'the result'
~~~

### 命名你的 mock¶

给你的 mock 起个名字可能会很有用。 名字会显示在 mock 的 repr 中并在 mock 出现于测试失败消息中时可以帮助理解。 这个名字也会被传播给 mock 的属性或方法:

    
    
~~~shell
>>> mock = MagicMock(name='foo')
>>> mock
<MagicMock name='foo' id='...'>
>>> mock.method
<MagicMock name='foo.method' id='...'>
~~~

### 追踪所有的调用¶

通常你会想要追踪对某个方法的多次调用。 [`mock_calls`](unittest.mock.md#unittest.mock.Mock.mock_calls "unittest.mock.Mock.mock_calls") 属性记录了所有对 mock 的子属性的调用 —— 并且还包括对它们的子属性的调用。

    
    
~~~shell
>>> mock = MagicMock()
>>> mock.method()
<MagicMock name='mock.method()' id='...'>
>>> mock.attribute.method(10, x=53)
<MagicMock name='mock.attribute.method()' id='...'>
>>> mock.mock_calls
[call.method(), call.attribute.method(10, x=53)]
~~~

如果你做了一个有关 `mock_calls` 的断言并且有任何非预期的方法被调用，则断言将失败。 这很有用处，因为除了断言你所预期的调用已被执行，你还会检查它们是否以正确的顺序被执行并且没有额外的调用:

你使用 [`call`](unittest.mock.md#unittest.mock.call "unittest.mock.call") 对象来构造列表以便与 `mock_calls` 进行比较:

    
    
~~~shell
>>> expected = [call.method(), call.attribute.method(10, x=53)]
>>> mock.mock_calls == expected
True
~~~

然而，返回 mock 的调用的形参不会被记录，这意味着不可能追踪附带了重要形参的创建上级对象的嵌套调用:

    
    
~~~shell
>>> m = Mock()
>>> m.factory(important=True).deliver()
<Mock name='mock.factory().deliver()' id='...'>
>>> m.mock_calls[-1] == call.factory(important=False).deliver()
True
~~~

### 设置返回值和属性¶

在 mock 对象上设置返回值是非常容易的:

    
    
~~~shell
>>> mock = Mock()
>>> mock.return_value = 3
>>> mock()
3
~~~

当然你也可以对 mock 上的方法做同样的操作:

    
    
~~~shell
>>> mock = Mock()
>>> mock.method.return_value = 3
>>> mock.method()
3
~~~

返回值也可以在构造器中设置:

    
    
~~~shell
>>> mock = Mock(return_value=3)
>>> mock()
3
~~~

如果你需要在你的 mock 上设置一个属性，只需这样做:

    
    
~~~shell
>>> mock = Mock()
>>> mock.x = 3
>>> mock.x
3
~~~

有时你会想要模拟更复杂的情况，例如这个例子 `mock.connection.cursor().execute("SELECT 1")`。 如果我们希望这个调用返回一个列表，那么我们还必须配置嵌套调用的结果。

我们可以像这样使用 [`call`](unittest.mock.md#unittest.mock.call "unittest.mock.call") 在一个“链式调用”中构造调用集合以便随后方便地设置断言:

    
    
~~~shell
>>> mock = Mock()
>>> cursor = mock.connection.cursor.return_value
>>> cursor.execute.return_value = ['foo']
>>> mock.connection.cursor().execute("SELECT 1")
['foo']
>>> expected = call.connection.cursor().execute("SELECT 1").call_list()
>>> mock.mock_calls
[call.connection.cursor(), call.connection.cursor().execute('SELECT 1')]
>>> mock.mock_calls == expected
True
~~~

对 `.call_list()` 的调用会将我们的调用对象转成一个代表链式调用的调用列表。

### 通过 mock 引发异常¶

一个很有用的属性是 [`side_effect`](unittest.mock.md#unittest.mock.Mock.side_effect "unittest.mock.Mock.side_effect")。 如果你将该属性设为一个异常类或者实例那么当 mock 被调用时该异常将会被引发。

    
    
~~~shell
>>> mock = Mock(side_effect=Exception('Boom!'))
>>> mock()
Traceback (most recent call last):
  ...
Exception: Boom!
~~~

### 附带影响函数和可迭代对象¶

`side_effect` 也可以被设为一个函数或可迭代对象。 `side_effect` 作为可迭代对象的应用场景适用于你的 mock 将要被多次调用，并且你希望每次调用都返回不同的值的情况。 当你将 `side_effect` 设为一个可迭代对象时每次对 mock 的调用将返回可迭代对象的下一个值。

    
    
~~~shell
>>> mock = MagicMock(side_effect=[4, 5, 6])
>>> mock()
4
>>> mock()
5
>>> mock()
6
~~~

对于更高级的用例，例如根据 mock 调用时附带的参数动态改变返回值，`side_effect` 可以指定一个函数。 该函数将附带与 mock 相同的参数被调用。 该函数所返回的就是调用所返回的对象:

    
    
~~~shell
>>> vals = {(1, 2): 1, (2, 3): 2}
>>> def side_effect(*args):
...     return vals[args]
...
>>> mock = MagicMock(side_effect=side_effect)
>>> mock(1, 2)
1
>>> mock(2, 3)
2
~~~

### 模拟异步迭代器¶

从 Python 3.8 起，`AsyncMock` 和 `MagicMock` 支持通过 `__aiter__` 来模拟 [异步迭代器](3.%20数据模型.md#async-iterators)。 `__aiter__` 的 [`return_value`](unittest.mock.md#unittest.mock.Mock.return_value "unittest.mock.Mock.return_value") 属性可以被用来设置要用于迭代的返回值。

    
    
~~~shell
>>> mock = MagicMock()  # AsyncMock also works here
>>> mock.__aiter__.return_value = [1, 2, 3]
>>> async def main():
...     return [i async for i in mock]
...
>>> asyncio.run(main())
[1, 2, 3]
~~~

### 模拟异步上下文管理器¶

从 Python 3.8 起，`AsyncMock` 和 `MagicMock` 支持通过 `__aenter__` 和 `__aexit__` 来模拟 [异步上下文管理器](3.%20数据模型.md#async-context-managers)。 在默认情况下，`__aenter__` 和 `__aexit__` 将为返回异步函数的 `AsyncMock` 实例。

    
    
~~~shell
>>> class AsyncContextManager:
...     async def __aenter__(self):
...         return self
...     async def __aexit__(self, exc_type, exc, tb):
...         pass
...
>>> mock_instance = MagicMock(AsyncContextManager())  # AsyncMock also works here
>>> async def main():
...     async with mock_instance as result:
...         pass
...
>>> asyncio.run(main())
>>> mock_instance.__aenter__.assert_awaited_once()
>>> mock_instance.__aexit__.assert_awaited_once()
~~~

### 基于现有对象创建模拟对象¶

使用模拟操作的一个问题是它会将你的测试与你的 mock 实现相关联而不是与你的真实代码相关联。 假设你有一个实现了 `some_method` 的类。 在对另一个类的测试中，你提供了一个 _同样_ 提供了 `some_method` 的模拟该对象的 mock 对象。 如果后来你重构了第一个类，使得它不再具有 `some_method` —— 那么你的测试将继续保持通过，尽管现在你的代码已经被破坏了！

[`Mock`](unittest.mock.md#unittest.mock.Mock "unittest.mock.Mock") 允许你使用allows you to provide an object as a specification for the mock, using the _spec_ 关键字参数来提供一个对象作为 mock 的规格说明。 在 mock 上访问不存在于你的规格说明对象中的方法 / 属性将立即引发一个属性错误。 如果你修改你的规格说明的实现，，那么使用了该类的测试将立即开始失败而不需要你在这些测试中实例化该类。

    
    
~~~shell
>>> mock = Mock(spec=SomeClass)
>>> mock.old_method()
Traceback (most recent call last):
   ...
AttributeError: Mock object has no attribute 'old_method'. Did you mean: 'class_method'?
~~~

使用规格说明还可以启用对 mock 的调用的更聪明的匹配操作，无论是否有将某些形参作为位置或关键字参数传入:

    
    
~~~shell
>>> def f(a, b, c): pass
...
>>> mock = Mock(spec=f)
>>> mock(1, 2, 3)
<Mock name='mock()' id='140161580456576'>
>>> mock.assert_called_with(a=1, b=2, c=3)
~~~

如果你想要让这些更聪明的匹配操作也适用于 mock 上的方法调用，你可以使用 [auto-speccing](unittest.mock.md#auto-speccing)。

如果你想要更强形式的规格说明以防止设置任意属性并获取它们那么你可以使用 _spec_set_ 来代替 _spec_ 。

### 使用 side_effect 返回每个文件的内容¶

[`mock_open()`](unittest.mock.md#unittest.mock.mock_open "unittest.mock.mock_open") 被用来为 [`open()`](functions.md#open "open") 方法打补丁。 [`side_effect`](unittest.mock.md#unittest.mock.Mock.side_effect "unittest.mock.Mock.side_effect") 可被用来在每次调用中返回一个新的 Mock 对象。 这可被用来返回存储在字典中的每个文件的不同内容:

    
    
~~~
DEFAULT = "default"
data_dict = {"file1": "data1",
             "file2": "data2"}

def open_side_effect(name):
    return mock_open(read_data=data_dict.get(name, DEFAULT))()

with patch("builtins.open", side_effect=open_side_effect):
    with open("file1") as file1:
        assert file1.read() == "data1"

    with open("file2") as file2:
        assert file2.read() == "data2"

    with open("file3") as file2:
        assert file2.read() == "default"
~~~

## 补丁装饰器¶

备注

在查找对象的名称空间中修补对象使用 [`patch()`](unittest.mock.md#unittest.mock.patch "unittest.mock.patch") 。使用起来很简单，阅读 [补丁的位置](unittest.mock.md#where-to-patch) 来快速上手。

测试中的一个常见需求是为类属性或模块属性打补丁，例如修补内置对象或修补某个模块中的类来测试其是否被实例化。 模块和类都可算是全局对象，因此对它们打补丁的操作必须在测试完成之后被还原否则补丁将持续影响其他测试并导致难以诊断的问题。

为此 mock 提供了三个便捷的装饰器: [`patch()`](unittest.mock.md#unittest.mock.patch "unittest.mock.patch"), [`patch.object()`](unittest.mock.md#unittest.mock.patch.object "unittest.mock.patch.object") 和 [`patch.dict()`](unittest.mock.md#unittest.mock.patch.dict "unittest.mock.patch.dict")。 `patch` 接受单个字符串，其形式 `package.module.Class.attribute` 指明你要修补的属性。 它还可选择接受一个值用来替换指定的属性（或者类对象等等）。 'patch.object' 接受一个对象和你想要修补的属性名称，并可选择接受要用作补丁的值。

`patch.object`:

    
    
~~~shell
>>> original = SomeClass.attribute
>>> @patch.object(SomeClass, 'attribute', sentinel.attribute)
... def test():
...     assert SomeClass.attribute == sentinel.attribute
...
>>> test()
>>> assert SomeClass.attribute == original

>>> @patch('package.module.attribute', sentinel.attribute)
... def test():
...     from package.module import attribute
...     assert attribute is sentinel.attribute
...
>>> test()
~~~

如果你要给一个模块 (包括 [`builtins`](builtins.md#module-builtins "builtins: The module that provides the built-in namespace.")) 打补丁则可使用 [`patch()`](unittest.mock.md#unittest.mock.patch "unittest.mock.patch") 来代替 [`patch.object()`](unittest.mock.md#unittest.mock.patch.object "unittest.mock.patch.object"):

    
    
~~~shell
>>> mock = MagicMock(return_value=sentinel.file_handle)
>>> with patch('builtins.open', mock):
...     handle = open('filename', 'r')
...
>>> mock.assert_called_with('filename', 'r')
>>> assert handle == sentinel.file_handle, "incorrect file handle returned"
~~~

如有必要模块名可以是“带点号”的，其形式如 `package.module`:

    
    
~~~shell
>>> @patch('package.module.ClassName.attribute', sentinel.attribute)
... def test():
...     from package.module import ClassName
...     assert ClassName.attribute == sentinel.attribute
...
>>> test()
~~~

一个良好的模式是实际地装饰测试方法本身:

    
    
~~~shell
>>> class MyTest(unittest.TestCase):
...     @patch.object(SomeClass, 'attribute', sentinel.attribute)
...     def test_something(self):
...         self.assertEqual(SomeClass.attribute, sentinel.attribute)
...
>>> original = SomeClass.attribute
>>> MyTest('test_something').test_something()
>>> assert SomeClass.attribute == original
~~~

如果你想要通过 Mock 来打补丁，你可以只附带一个参数使用 [`patch()`](unittest.mock.md#unittest.mock.patch "unittest.mock.patch") (或附带两个参数使用 [`patch.object()`](unittest.mock.md#unittest.mock.patch.object "unittest.mock.patch.object"))。 这将为你创建 mock 并传递给测试函数 / 方法:

    
    
~~~shell
>>> class MyTest(unittest.TestCase):
...     @patch.object(SomeClass, 'static_method')
...     def test_something(self, mock_method):
...         SomeClass.static_method()
...         mock_method.assert_called_with()
...
>>> MyTest('test_something').test_something()
~~~

你可以使用以下模式来堆叠多个补丁装饰器:

    
    
~~~shell
>>> class MyTest(unittest.TestCase):
...     @patch('package.module.ClassName1')
...     @patch('package.module.ClassName2')
...     def test_something(self, MockClass2, MockClass1):
...         self.assertIs(package.module.ClassName1, MockClass1)
...         self.assertIs(package.module.ClassName2, MockClass2)
...
>>> MyTest('test_something').test_something()
~~~

当你嵌套 patch 装饰器时将以它们被应用的相同顺序（即 _Python_ 应用装饰器的正常顺序）将 mock 传入被装饰的函数。 也就是说从下往上，因此在上面的示例中 `test_module.ClassName2` 的 mock 会被最先传入。

还有一个 [`patch.dict()`](unittest.mock.md#unittest.mock.patch.dict "unittest.mock.patch.dict") 用于在一定范围内设置字典中的值，并在测试结束时将字典恢复为其原始状态：

    
    
~~~shell
>>> foo = {'key': 'value'}
>>> original = foo.copy()
>>> with patch.dict(foo, {'newkey': 'newvalue'}, clear=True):
...     assert foo == {'newkey': 'newvalue'}
...
>>> assert foo == original
~~~

`patch`, `patch.object` 和 `patch.dict` 都可被用作上下文管理器。

在你使用 [`patch()`](unittest.mock.md#unittest.mock.patch "unittest.mock.patch") 为你创建 mock 时，你可以使用 with 语句的 "as" 形式来获得对 mock 的引用:

    
    
~~~shell
>>> class ProductionClass:
...     def method(self):
...         pass
...
>>> with patch.object(ProductionClass, 'method') as mock_method:
...     mock_method.return_value = None
...     real = ProductionClass()
...     real.method(1, 2, 3)
...
>>> mock_method.assert_called_with(1, 2, 3)
~~~

作为替代 `patch`, `patch.object` 和 `patch.dict` 可以被用作类装饰器。 当以此方式使用时其效果与将装饰器单独应用到每个以 "test" 打头的方法上相同。

## 更多示例¶

下面是一些针对更为高级应用场景的补充示例。

### 模拟链式调用¶

实际上一旦你理解了 [`return_value`](unittest.mock.md#unittest.mock.Mock.return_value "unittest.mock.Mock.return_value") 属性那么使用 mock 模拟链式调用就会相当直观。 当一个 mock 首次被调用，或者当你在它被调用前获取其 `return_value` 时，将会创建一个新的 [`Mock`](unittest.mock.md#unittest.mock.Mock "unittest.mock.Mock")。

这意味着你可以通过检视 `return_value` mock 来了解从调用被模拟对象返回的对象是如何被使用的:

    
    
~~~shell
>>> mock = Mock()
>>> mock().foo(a=2, b=3)
<Mock name='mock().foo()' id='...'>
>>> mock.return_value.foo.assert_called_with(a=2, b=3)
~~~

从这里开始只需一个步骤即可配置并创建有关链式调用的断言。 当然还有另一种选择是首先以更易于测试的方式来编写你的代码...

因此，如果我们有这样一些代码:

    
    
~~~shell
>>> class Something:
...     def __init__(self):
...         self.backend = BackendProvider()
...     def method(self):
...         response = self.backend.get_endpoint('foobar').create_call('spam', 'eggs').start_call()
...         # more code
~~~

假定 `BackendProvider` 已经过良好测试，我们要如何测试 `method()`？ 特别地，我们希望测试代码段 `# more code` 是否以正确的方式使用了响应对象。

由于这个链式调用来自一个实例属性我们可以对 `backend` 属性在 `Something` 实例上进行猴子式修补。 在这个特定情况下我们只对最后调用 `start_call` 的返回值感兴趣所以我们不需要进行太多的配置。 让我们假定它返回的是“文件类”对象，因此我们将确保我们的响应对象使用内置的 [`open()`](functions.md#open "open") 作为其 `spec`。

为了做到这一点我们创建一个 mock 实例作为我们的 mock 后端并为它创建一个 mock 响应对象。 要将该响应对象设为最后的 `start_call` 的返回值我们可以这样做:

    
    
~~~
mock_backend.get_endpoint.return_value.create_call.return_value.start_call.return_value = mock_response
~~~

我们可以通过更好一些的方式做到这一点，即使用 [`configure_mock()`](unittest.mock.md#unittest.mock.Mock.configure_mock "unittest.mock.Mock.configure_mock") 方法直接为我们设置返回值:

    
    
~~~shell
>>> something = Something()
>>> mock_response = Mock(spec=open)
>>> mock_backend = Mock()
>>> config = {'get_endpoint.return_value.create_call.return_value.start_call.return_value': mock_response}
>>> mock_backend.configure_mock(**config)
~~~

有了这些我们就能准备好给“mock 后端”打上猴子补丁并可以执行真正的调用:

    
    
~~~shell
>>> something.backend = mock_backend
>>> something.method()
~~~

使用 [`mock_calls`](unittest.mock.md#unittest.mock.Mock.mock_calls "unittest.mock.Mock.mock_calls") 我们可以通过一个断言来检查链式调用。 一个链式调用就是在一行代码中连续执行多个调用，所以在 `mock_calls` 中将会有多个条目。 我们可以使用 [`call.call_list()`](unittest.mock.md#unittest.mock.call.call_list "unittest.mock.call.call_list") 来为我们创建这个调用列表:

    
    
~~~shell
>>> chained = call.get_endpoint('foobar').create_call('spam', 'eggs').start_call()
>>> call_list = chained.call_list()
>>> assert mock_backend.mock_calls == call_list
~~~

### 部分模拟¶

在某些测试中我希望模拟对 [`datetime.date.today()`](3.标准库/datetime.md#datetime.date.today "datetime.date.today") 的调用来返回一个已知的日期，但又不想阻止被测试的代码创建新的日期对象。 不幸的是 [`datetime.date`](3.标准库/datetime.md#datetime.date "datetime.date") 是用 C 语言编写的，因此我不能简单地给静态 [`datetime.date.today()`](3.标准库/datetime.md#datetime.date.today "datetime.date.today") 方法打上猴子补丁。

我找到了实现这一点的简单方式即通过一个 mock 来实际包装日期类，但通过对构造器的调用传递给真实的类（并返回真实的实例）。

这里使用 [`patch 装饰器`](unittest.mock.md#unittest.mock.patch "unittest.mock.patch") 来模拟被测试模块中的 `date` 类。 然后将模拟 date 类的 [`side_effect`](unittest.mock.md#unittest.mock.Mock.side_effect "unittest.mock.Mock.side_effect") 属性设为一个返回真实日期的 lambda 函数。 当模拟 date 类被调用时，将通过 `side_effect` 构造并返回一个真实日期。

    
    
~~~shell
>>> from datetime import date
>>> with patch('mymodule.date') as mock_date:
...     mock_date.today.return_value = date(2010, 10, 8)
...     mock_date.side_effect = lambda *args, **kw: date(*args, **kw)
...
...     assert mymodule.date.today() == date(2010, 10, 8)
...     assert mymodule.date(2009, 6, 8) == date(2009, 6, 8)
~~~

请注意我们没有在全局范围上修补 [`datetime.date`](3.标准库/datetime.md#datetime.date "datetime.date")，我们只是在 _使用_ 它的模块中给 `date` 打补丁。 参见 [补丁的位置](unittest.mock.md#where-to-patch)。

当 `date.today()` 被调用时将返回一个已知的日期，但对 `date(...)` 构造器的调用仍会返回普通的日期。 如果不是这样你会发现你必须使用与被测试的代码完全相同的算法来计算出预期的结果，这是测试工作中的一个经典的反模式。

对 date 构造器的调用会被记录在 `mock_date` 属性中 (`call_count` 等)，它们也可能对你的测试有用处。

有关处理模块日期或其他内置类的一种替代方式的讨论请参见 [这篇博客文章](https://williambert.online/2011/07/how-to-unit-testing-in-django-with-mocking-and-patching/)。

### 模拟生成器方法¶

Python 生成器是指在被迭代时使用 [`yield`](7.%20简单语句.md#yield) 语句来返回一系列值的函数或方法 [1]。

调用生成器方法 / 函数将返回生成器对象。 生成器对象随后会被迭代。 迭代操作对应的协议方法是 [`__iter__()`](stdtypes.md#container.__iter__ "container.__iter__")，因此我们可以使用 [`MagicMock`](unittest.mock.md#unittest.mock.MagicMock "unittest.mock.MagicMock") 来模拟它。

以下是一个使用 "iter" 方法模拟为生成器的示例类:

    
    
~~~shell
>>> class Foo:
...     def iter(self):
...         for i in [1, 2, 3]:
...             yield i
...
>>> foo = Foo()
>>> list(foo.iter())
[1, 2, 3]
~~~

我们要如何模拟这个类，特别是它的 "iter" 方法呢？

为了配置从迭代操作（隐含在对 [`list`](stdtypes.md#list "list") 的调用中）返回的值，我们需要配置调用 `foo.iter()` 所返回的对象。

    
    
~~~shell
>>> mock_foo = MagicMock()
>>> mock_foo.iter.return_value = iter([1, 2, 3])
>>> list(mock_foo.iter())
[1, 2, 3]
~~~

[1]

此外还有生成器表达式和更多的生成器 [进阶用法](http://www.dabeaz.com/coroutines/index.md)，但在这里我们不去关心它们。 有关生成器及其强大功能的一个很好的介绍请参阅: [针对系统程序员的生成器妙招](http://www.dabeaz.com/generators/)。

### 对每个测试方法应用相同的补丁¶

如果你想要为多个测试方法准备好多个补丁那么最简单的方式就是将 patch 装饰器应用到每个方法上。 这在感觉上像上不必要的重复。 对此，你可以使用 [`patch()`](unittest.mock.md#unittest.mock.patch "unittest.mock.patch") (包括基各种不同形式) 作为类装饰器。 这将把补丁应用于类上的所有测试方法。 测试方法是通过以 `test` 打头的名称来标识的:

    
    
~~~shell
>>> @patch('mymodule.SomeClass')
... class MyTest(unittest.TestCase):
...
...     def test_one(self, MockSomeClass):
...         self.assertIs(mymodule.SomeClass, MockSomeClass)
...
...     def test_two(self, MockSomeClass):
...         self.assertIs(mymodule.SomeClass, MockSomeClass)
...
...     def not_a_test(self):
...         return 'something'
...
>>> MyTest('test_one').test_one()
>>> MyTest('test_two').test_two()
>>> MyTest('test_two').not_a_test()
'something'
~~~

另一种管理补丁的方式是使用 [补丁方法: start 和 stop](unittest.mock.md#start-and-stop)。 它允许你将打补丁操作移至你的 `setUp` 和 `tearDown` 方法中。

    
    
~~~shell
>>> class MyTest(unittest.TestCase):
...     def setUp(self):
...         self.patcher = patch('mymodule.foo')
...         self.mock_foo = self.patcher.start()
...
...     def test_foo(self):
...         self.assertIs(mymodule.foo, self.mock_foo)
...
...     def tearDown(self):
...         self.patcher.stop()
...
>>> MyTest('test_foo').run()
~~~

如果你要使用这个技巧则你必须通过调用 `stop` 来确保补丁被“恢复”。 这可能要比你想像的更麻烦，因为如果在 setUp 中引发了异常那么 tearDown 将不会被调用。 [`unittest.TestCase.addCleanup()`](unittest.md#unittest.TestCase.addCleanup "unittest.TestCase.addCleanup") 可以做到更方便:

    
    
~~~shell
>>> class MyTest(unittest.TestCase):
...     def setUp(self):
...         patcher = patch('mymodule.foo')
...         self.addCleanup(patcher.stop)
...         self.mock_foo = patcher.start()
...
...     def test_foo(self):
...         self.assertIs(mymodule.foo, self.mock_foo)
...
>>> MyTest('test_foo').run()
~~~

### 模拟未绑定方法¶

当前在编写测试时我需要修补一个 _未绑定方法_ (在类上而不是在实例上为方法打补丁)。 我需要将 self 作为第一个参数传入因为我想对哪些对象在调用这个特定方法进行断言。 问题是这里你不能用 mock 来打补丁，因为如果你用 mock 来替换一个未绑定方法那么当从实例中获取时它就不会成为一个已绑定方法，因而它不会获得传入的 self。 绕过此问题的办法是改用一个真正的函数来修补未绑定方法。 [`patch()`](unittest.mock.md#unittest.mock.patch "unittest.mock.patch") 装饰器让使用 mock 来给方法打补丁变得如此简单以至于创建一个真正的函数成为一件麻烦事。

如果将 `autospec=True` 传给 patch 那么它就会用一个 _真正的_ 函数对象来打补丁。 这个函数对象具有与它所替换的函数相同的签名，但会在内部将操作委托给一个 mock。 你仍然可以通过与以前完全相同的方式来自动创建你的 mock。 但是这将意味着一件事，就是如果你用它来修补一个类上的非绑定方法那么如果它是从一个实例中获取则被模拟的函数将被转为已绑定方法。 传给它的第一个参数将为 `self`，而这真是我想要的:

    
    
~~~shell
>>> class Foo:
...   def foo(self):
...     pass
...
>>> with patch.object(Foo, 'foo', autospec=True) as mock_foo:
...   mock_foo.return_value = 'foo'
...   foo = Foo()
...   foo.foo()
...
'foo'
>>> mock_foo.assert_called_once_with(foo)
~~~

如果我们不使用 `autospec=True` 那么这个未绑定方法会改为通过一个 Mock 补丁来修补，而不是附带 `self` 来调用。

### 通过 mock 检查多次调用¶

mock 有一个很好的 API 用于针对你的 mock 对象如何被使用来下断言。

    
    
~~~shell
>>> mock = Mock()
>>> mock.foo_bar.return_value = None
>>> mock.foo_bar('baz', spam='eggs')
>>> mock.foo_bar.assert_called_with('baz', spam='eggs')
~~~

如果你的 mock 只会被调用一次那么你可以使用 [`assert_called_once_with()`](unittest.mock.md#unittest.mock.Mock.assert_called_once_with "unittest.mock.Mock.assert_called_once_with") 方法，该方法也会断言 [`call_count`](unittest.mock.md#unittest.mock.Mock.call_count "unittest.mock.Mock.call_count") 的值为一。

    
    
~~~shell
>>> mock.foo_bar.assert_called_once_with('baz', spam='eggs')
>>> mock.foo_bar()
>>> mock.foo_bar.assert_called_once_with('baz', spam='eggs')
Traceback (most recent call last):
    ...
AssertionError: Expected 'foo_bar' to be called once. Called 2 times.
Calls: [call('baz', spam='eggs'), call()].
~~~

`assert_called_with` 和 `assert_called_once_with` 都是有关 _最近_ 调用的断言。 如果你的 mock 将被多次调用，并且你想要针对 _所有_ 这些调用下断言你可以使用 [`call_args_list`](unittest.mock.md#unittest.mock.Mock.call_args_list "unittest.mock.Mock.call_args_list"):

    
    
~~~shell
>>> mock = Mock(return_value=None)
>>> mock(1, 2, 3)
>>> mock(4, 5, 6)
>>> mock()
>>> mock.call_args_list
[call(1, 2, 3), call(4, 5, 6), call()]
~~~

使用 [`call`](unittest.mock.md#unittest.mock.call "unittest.mock.call") 辅助对象可以方便地针对这些调用下断言。 你可以创建一个预期调用的列表并将其与 `call_args_list` 比较。 这看起来与 `call_args_list` 的 repr 非常相似:

    
    
~~~shell
>>> expected = [call(1, 2, 3), call(4, 5, 6), call()]
>>> mock.call_args_list == expected
True
~~~

### 处理可变参数¶

另一种很少见，但可能给你带来麻烦的情况会在你的 mock 附带可变参数被调用的时候发生。 `call_args` 和 `call_args_list` 将保存对这些参数的 _引用_ 。 如果这些参数被受测试的代码所改变那么你将无法再针对当该 mock 被调用时附带的参数值下断言。

下面是一些演示此问题的示例代码。 设想在 'mymodule' 中定义了下列函数:

    
    
~~~python
def frob(val):
    pass

def grob(val):
    "First frob and then clear val"
    frob(val)
    val.clear()
~~~

当我们想要测试 `grob` 调用 `frob` 并附带了正确的参数时将可看到发生了什么:

    
    
~~~shell
>>> with patch('mymodule.frob') as mock_frob:
...     val = {6}
...     mymodule.grob(val)
...
>>> val
set()
>>> mock_frob.assert_called_with({6})
Traceback (most recent call last):
    ...
AssertionError: Expected: (({6},), {})
Called with: ((set(),), {})
~~~

对于 mock 的一个可能性是复制你传入的参数。 如果你创建依赖于对象标识号相等性的断言那么这可能会在后面导致问题。

下面是一个使用 [`side_effect`](unittest.mock.md#unittest.mock.Mock.side_effect "unittest.mock.Mock.side_effect") 功能的解决方案。 如果你为 mock 提供了 `side_effect` 函数那么 `side_effect` 将附带与该 mock 相同的参数被调用。 这样我们就有机会拷贝这些参数并将其保存起来用于之后执行断言。 在本例中我使用了 _另一个_ mock 来保存参数以便可以使用该 mock 的方法来执行断言。 在这里辅助函数再次为我设置好了这一切。

    
    
~~~shell
>>> from copy import deepcopy
>>> from unittest.mock import Mock, patch, DEFAULT
>>> def copy_call_args(mock):
...     new_mock = Mock()
...     def side_effect(*args, **kwargs):
...         args = deepcopy(args)
...         kwargs = deepcopy(kwargs)
...         new_mock(*args, **kwargs)
...         return DEFAULT
...     mock.side_effect = side_effect
...     return new_mock
...
>>> with patch('mymodule.frob') as mock_frob:
...     new_mock = copy_call_args(mock_frob)
...     val = {6}
...     mymodule.grob(val)
...
>>> new_mock.assert_called_with({6})
>>> new_mock.call_args
call({6})
~~~

调用 `copy_call_args` 时会传入将被调用的 mock。 它将返回一个新的 mock 供我们进行断言。 `side_effect` 函数会拷贝这些参数并附带该副本来调用我们的 `new_mock`。

备注

如果你的 mock 只会被使用一次那么有更容易的方式可以在它们被调用时检查参数。 你可以简单地在 `side_effect` 函数中执行检查。

    
    
~~~shell
>>> def side_effect(arg):
...     assert arg == {6}
...
>>> mock = Mock(side_effect=side_effect)
>>> mock({6})
>>> mock(set())
Traceback (most recent call last):
    ...
AssertionError
~~~

一个替代方式是创建一个 [`Mock`](unittest.mock.md#unittest.mock.Mock "unittest.mock.Mock") 或 [`MagicMock`](unittest.mock.md#unittest.mock.MagicMock "unittest.mock.MagicMock") 的子类来拷贝 (使用 [`copy.deepcopy()`](copy.md#copy.deepcopy "copy.deepcopy")) 参数。 下面是一个示例实现:

    
    
~~~shell
>>> from copy import deepcopy
>>> class CopyingMock(MagicMock):
...     def __call__(self, /, *args, **kwargs):
...         args = deepcopy(args)
...         kwargs = deepcopy(kwargs)
...         return super().__call__(*args, **kwargs)
...
>>> c = CopyingMock(return_value=None)
>>> arg = set()
>>> c(arg)
>>> arg.add(1)
>>> c.assert_called_with(set())
>>> c.assert_called_with(arg)
Traceback (most recent call last):
    ...
AssertionError: expected call not found.
Expected: mock({1})
Actual: mock(set())
>>> c.foo
<CopyingMock name='mock.foo' id='...'>
~~~

当你子类化 `Mock` 或 `MagicMock` 时所有动态创建的属性以及 `return_value` 都将自动使用你的子类。 这意味着 `CopyingMock` 的所有子类也都将为 `CopyingMock` 类型。

### 嵌套补丁¶

使用 patch 作为上下文管理器很不错，但是如果你要执行多个补丁你将不断嵌套 with 语句使得代码越来越深地向右缩进:

    
    
~~~shell
>>> class MyTest(unittest.TestCase):
...
...     def test_foo(self):
...         with patch('mymodule.Foo') as mock_foo:
...             with patch('mymodule.Bar') as mock_bar:
...                 with patch('mymodule.Spam') as mock_spam:
...                     assert mymodule.Foo is mock_foo
...                     assert mymodule.Bar is mock_bar
...                     assert mymodule.Spam is mock_spam
...
>>> original = mymodule.Foo
>>> MyTest('test_foo').test_foo()
>>> assert mymodule.Foo is original
~~~

使用 unittest `cleanup` 函数和 [补丁方法: start 和 stop](unittest.mock.md#start-and-stop) 我们可以达成同样的效果而无须嵌套缩进。 一个简单的辅助方法 `create_patch` 会为我们执行打补丁操作并返回所创建的 mock:

    
    
~~~shell
>>> class MyTest(unittest.TestCase):
...
...     def create_patch(self, name):
...         patcher = patch(name)
...         thing = patcher.start()
...         self.addCleanup(patcher.stop)
...         return thing
...
...     def test_foo(self):
...         mock_foo = self.create_patch('mymodule.Foo')
...         mock_bar = self.create_patch('mymodule.Bar')
...         mock_spam = self.create_patch('mymodule.Spam')
...
...         assert mymodule.Foo is mock_foo
...         assert mymodule.Bar is mock_bar
...         assert mymodule.Spam is mock_spam
...
>>> original = mymodule.Foo
>>> MyTest('test_foo').run()
>>> assert mymodule.Foo is original
~~~

### 使用 MagicMock 模拟字典¶

你可能会想要模拟一个字典或其他容器对象，记录所有对它的访问并让它的行为仍然像是一个字典。

要做到这点我们可以用 [`MagicMock`](unittest.mock.md#unittest.mock.MagicMock "unittest.mock.MagicMock")，它的行为类似于字典，并会使用 [`side_effect`](unittest.mock.md#unittest.mock.Mock.side_effect "unittest.mock.Mock.side_effect") 将字典访问委托给下层的在我们控制之下的一个真正的字典。

当我们的 `MagicMock` 的 [`__getitem__()`](3.%20数据模型.md#object.__getitem__ "object.__getitem__") 和 [`__setitem__()`](3.%20数据模型.md#object.__setitem__ "object.__setitem__") 方法被调用（即正常的字典访问操作）时 `side_effect` 将附带相应的键（对于 `__setitem__` 还将附带值）被调用。 我们还可以控制返回的内容。

在 `MagicMock` 被使用之后我们可以使用 [`call_args_list`](unittest.mock.md#unittest.mock.Mock.call_args_list "unittest.mock.Mock.call_args_list") 等属性来针对该字典是如何被使用的下断言。

    
    
~~~shell
>>> my_dict = {'a': 1, 'b': 2, 'c': 3}
>>> def getitem(name):
...      return my_dict[name]
...
>>> def setitem(name, val):
...     my_dict[name] = val
...
>>> mock = MagicMock()
>>> mock.__getitem__.side_effect = getitem
>>> mock.__setitem__.side_effect = setitem
~~~

备注

`MagicMock` 的一个可用替代是使用 `Mock` 并 _仅仅_ 提供你明确需要的魔术方法:

    
    
~~~shell
>>> mock = Mock()
>>> mock.__getitem__ = Mock(side_effect=getitem)
>>> mock.__setitem__ = Mock(side_effect=setitem)
~~~

_第三个_ 选项是使用 `MagicMock` 但传入 `dict` 作为 _spec_ (或 _spec_set_ ) 参数以使得所创建的 `MagicMock` 只有字典魔术方法是可用的:

    
    
~~~shell
>>> mock = MagicMock(spec_set=dict)
>>> mock.__getitem__.side_effect = getitem
>>> mock.__setitem__.side_effect = setitem
~~~

通过提供这些附带影响函数，`mock` 的行为将类似于普通字典但又会记录所有访问。 如果你尝试访问一个不存在的键它甚至会引发 [`KeyError`](3.标准库/exceptions.md#KeyError "KeyError")。

    
    
~~~shell
>>> mock['a']
1
>>> mock['c']
3
>>> mock['d']
Traceback (most recent call last):
    ...
KeyError: 'd'
>>> mock['b'] = 'fish'
>>> mock['d'] = 'eggs'
>>> mock['b']
'fish'
>>> mock['d']
'eggs'
~~~

在它被使用之后你可以使用普通的 mock 方法和属性进行有关访问操作的断言:

    
    
~~~shell
>>> mock.__getitem__.call_args_list
[call('a'), call('c'), call('d'), call('b'), call('d')]
>>> mock.__setitem__.call_args_list
[call('b', 'fish'), call('d', 'eggs')]
>>> my_dict
{'a': 1, 'b': 'fish', 'c': 3, 'd': 'eggs'}
~~~

### 模拟子类及其属性¶

你可能出于各种原因想要子类化 [`Mock`](unittest.mock.md#unittest.mock.Mock "unittest.mock.Mock")。 其中一个可能的原因是为了添加辅助方法。 下面是一个笨兮兮的示例:

    
    
~~~shell
>>> class MyMock(MagicMock):
...     def has_been_called(self):
...         return self.called
...
>>> mymock = MyMock(return_value=None)
>>> mymock
<MyMock id='...'>
>>> mymock.has_been_called()
False
>>> mymock()
>>> mymock.has_been_called()
True
~~~

The standard behaviour for `Mock` 实例的标准行为是属性和返回值 mock 具有与它们所访问的 mock 相同的类型。 这将确保 `Mock` 的属性均为 `Mocks` 而 `MagicMock` 的属性均为 `MagicMocks` [2]。 因此如果你通过子类化来添加辅助方法那么它们也将在你的子类的实例的属性和返回值 mock 上可用。

    
    
~~~shell
>>> mymock.foo
<MyMock name='mock.foo' id='...'>
>>> mymock.foo.has_been_called()
False
>>> mymock.foo()
<MyMock name='mock.foo()' id='...'>
>>> mymock.foo.has_been_called()
True
~~~

有时这很不方便。 例如，[一位用户](https://code.google.com/archive/p/mock/issues/105) 子类化了 mock 来创建一个 [Twisted 适配器](https://twisted.org/documents/11.0.0/api/twisted.python.components.md)。 将它也应用于属性实际上会导致出错。

`Mock` (它的所有形式) 使用一个名为 `_get_child_mock` 的方法来创建这些用于属性和返回值的“子 mock”。 你可以通过重载此方法来防止你的子类被用于属性。 其签名被设为接受任意关键字参数 (`**kwargs`) 并且它们会被传递给 mock 构造器:

    
    
~~~shell
>>> class Subclass(MagicMock):
...     def _get_child_mock(self, /, **kwargs):
...         return MagicMock(**kwargs)
...
>>> mymock = Subclass()
>>> mymock.foo
<MagicMock name='mock.foo' id='...'>
>>> assert isinstance(mymock, Subclass)
>>> assert not isinstance(mymock.foo, Subclass)
>>> assert not isinstance(mymock(), Subclass)
~~~

[2]

此规则的一个例外涉及不可调用 mock。 属性会使用可调用对象版本是因为如非如此则不可调用 mock 将无法拥有可调用的方法。

### 通过 patch.dict 模拟导入¶

有一种会令模拟变困难的情况是当你在函数内部有局部导入。 这更难模拟的原因是它们不是使用来自我们能打补丁的模拟命名空间中的对象。

一般来说局部导入是应当避免的。 局部导入有时是为了防止循环依赖，而这个问题 _通常_ 都有更好的解决办法（重构代码）或者通过延迟导入来防止“前期成本”。 这也可以通过比无条件地局部导入更好的方式来解决（将模块保存为一个类或模块属性并且只在首次使用时执行导入）。

除此之外还有一个办法可以使用 `mock` 来影响导入的结果。 导入操作会从 [`sys.modules`](3.标准库/sys.md#sys.modules "sys.modules") 字典提取一个 _对象_ 。 请注意是提取一个 _对象_ ，它不是必须为模块。 首次导入一个模块将使一个模块对象被放入 `sys.modules`，因此通常当你执行导入时你将得到一个模块。 但是并非必然如此。

这意味着你可以使用 [`patch.dict()`](unittest.mock.md#unittest.mock.patch.dict "unittest.mock.patch.dict") 来 _临时性地_ 将一个 mock 放入 [`sys.modules`](3.标准库/sys.md#sys.modules "sys.modules")。 在补丁激活期间的任何导入操作都将得到该 mock。 当补丁完成时（被装饰的函数退出，with 语句代码块结束或者 `patcher.stop()` 被调用）则之前存在的任何东西都将被安全地恢复。

下面是一个模拟 'fooble' 模拟的示例。

    
    
~~~shell
>>> import sys
>>> mock = Mock()
>>> with patch.dict('sys.modules', {'fooble': mock}):
...    import fooble
...    fooble.blob()
...
<Mock name='mock.blob()' id='...'>
>>> assert 'fooble' not in sys.modules
>>> mock.blob.assert_called_once_with()
~~~

你可以看到 `import fooble` 成功执行，而当退出时 [`sys.modules`](3.标准库/sys.md#sys.modules "sys.modules") 中将不再有 'fooble'。

这同样适用于 `from module import name` 形式:

    
    
~~~shell
>>> mock = Mock()
>>> with patch.dict('sys.modules', {'fooble': mock}):
...    from fooble import blob
...    blob.blip()
...
<Mock name='mock.blob.blip()' id='...'>
>>> mock.blob.blip.assert_called_once_with()
~~~

稍微多做一点工作你还可以模拟包的导入:

    
    
~~~shell
>>> mock = Mock()
>>> modules = {'package': mock, 'package.module': mock.module}
>>> with patch.dict('sys.modules', modules):
...    from package.module import fooble
...    fooble()
...
<Mock name='mock.module.fooble()' id='...'>
>>> mock.module.fooble.assert_called_once_with()
~~~

### 追踪调用顺序和不太冗长的调用断言¶

[`Mock`](unittest.mock.md#unittest.mock.Mock "unittest.mock.Mock") 类允许你通过 [`method_calls`](unittest.mock.md#unittest.mock.Mock.method_calls "unittest.mock.Mock.method_calls") 属性来追踪在你的 mock 对象上的方法调用的 _顺序_ 。 这并不允许你追踪单独 mock 对象之间的调用顺序，但是我们可以使用 [`mock_calls`](unittest.mock.md#unittest.mock.Mock.mock_calls "unittest.mock.Mock.mock_calls") 来达到同样的效果。

因为 mock 会追踪 `mock_calls` 中对子 mock 的调用，并且访问 mock 的任意属性都会创建一个子 mock，所以我们可以基于父 mock 创建单独的子 mock。 随后对这些子 mock 的调用将按顺序被记录在父 mock 的 `mock_calls` 中:

    
    
~~~shell
>>> manager = Mock()
>>> mock_foo = manager.foo
>>> mock_bar = manager.bar
~~~
    
    
~~~shell
>>> mock_foo.something()
<Mock name='mock.foo.something()' id='...'>
>>> mock_bar.other.thing()
<Mock name='mock.bar.other.thing()' id='...'>
~~~
    
    
~~~shell
>>> manager.mock_calls
[call.foo.something(), call.bar.other.thing()]
~~~

我们可以随后通过与管理器 mock 上的 `mock_calls` 属性进行比较来进行有关这些调用，包括调用顺序的断言:

    
    
~~~shell
>>> expected_calls = [call.foo.something(), call.bar.other.thing()]
>>> manager.mock_calls == expected_calls
True
~~~

如果 `patch` 创建并准备好了你的 mock 那么你可以使用 [`attach_mock()`](unittest.mock.md#unittest.mock.Mock.attach_mock "unittest.mock.Mock.attach_mock") 方法将它们附加到管理器 mock 上。 在附加之后所有调用都将被记录在管理器的 `mock_calls` 中。

    
    
~~~shell
>>> manager = MagicMock()
>>> with patch('mymodule.Class1') as MockClass1:
...     with patch('mymodule.Class2') as MockClass2:
...         manager.attach_mock(MockClass1, 'MockClass1')
...         manager.attach_mock(MockClass2, 'MockClass2')
...         MockClass1().foo()
...         MockClass2().bar()
<MagicMock name='mock.MockClass1().foo()' id='...'>
<MagicMock name='mock.MockClass2().bar()' id='...'>
>>> manager.mock_calls
[call.MockClass1(),
call.MockClass1().foo(),
call.MockClass2(),
call.MockClass2().bar()]
~~~

如果已经进行了许多调用，但是你只对它们的一个特定序列感兴趣则有一种替代方式是使用 [`assert_has_calls()`](unittest.mock.md#unittest.mock.Mock.assert_has_calls "unittest.mock.Mock.assert_has_calls") 方法。 这需要一个调用的列表（使用 [`call`](unittest.mock.md#unittest.mock.call "unittest.mock.call") 对象来构建）。 如果该调用序列在 [`mock_calls`](unittest.mock.md#unittest.mock.Mock.mock_calls "unittest.mock.Mock.mock_calls") 中则断言将成功。

    
    
~~~shell
>>> m = MagicMock()
>>> m().foo().bar().baz()
<MagicMock name='mock().foo().bar().baz()' id='...'>
>>> m.one().two().three()
<MagicMock name='mock.one().two().three()' id='...'>
>>> calls = call.one().two().three().call_list()
>>> m.assert_has_calls(calls)
~~~

即使链式调用 `m.one().two().three()` 不是对 mock 的唯一调用，该断言仍将成功。

有时可能会对一个 mock 进行多次调用，而你只对断言其中的 _某些_ 调用感兴趣。 你甚至可能对顺序也不关心。 在这种情况下你可以将 `any_order=True` 传给 `assert_has_calls`:

    
    
~~~shell
>>> m = MagicMock()
>>> m(1), m.two(2, 3), m.seven(7), m.fifty('50')
(...)
>>> calls = [call.fifty('50'), call(1), call.seven(7)]
>>> m.assert_has_calls(calls, any_order=True)
~~~

### 更复杂的参数匹配¶

使用与 [`ANY`](unittest.mock.md#unittest.mock.ANY "unittest.mock.ANY") 一样的基本概念我们可以实现匹配器以便在用作 mock 的参数的对象上执行更复杂的断言。

假设我们准备将某个对象传给一个在默认情况下基于对象标识相等（这是 Python 中用户自定义类的默认行为）的 mock。 要使用 [`assert_called_with()`](unittest.mock.md#unittest.mock.Mock.assert_called_with "unittest.mock.Mock.assert_called_with") 我们就将必须传入完全相同的对象。 如果我们只对该对象的某些属性感兴趣那么我们可以创建一个能为我们检查这些属性的匹配器。

在这个示例中你可以看到为何执行对 `assert_called_with` 的‘标准’调用并不足够:

    
    
~~~shell
>>> class Foo:
...     def __init__(self, a, b):
...         self.a, self.b = a, b
...
>>> mock = Mock(return_value=None)
>>> mock(Foo(1, 2))
>>> mock.assert_called_with(Foo(1, 2))
Traceback (most recent call last):
    ...
AssertionError: expected call not found.
Expected: mock(<__main__.Foo object at 0x...>)
Actual: mock(<__main__.Foo object at 0x...>)
~~~

一个针对我们的 `Foo` 类的比较函数看上去会是这样的:

    
    
~~~shell
>>> def compare(self, other):
...     if not type(self) == type(other):
...         return False
...     if self.a != other.a:
...         return False
...     if self.b != other.b:
...         return False
...     return True
...
~~~

而一个可以使用这样的比较函数进行相等性比较运算的匹配器对象看上去会是这样的:

    
    
~~~shell
>>> class Matcher:
...     def __init__(self, compare, some_obj):
...         self.compare = compare
...         self.some_obj = some_obj
...     def __eq__(self, other):
...         return self.compare(self.some_obj, other)
...
~~~

将所有这些放在一起:

    
    
~~~shell
>>> match_foo = Matcher(compare, Foo(1, 2))
>>> mock.assert_called_with(match_foo)
~~~

`Matcher` 是用我们的比较函数和我们想要比较的 `Foo` 对象来实例化的。 在 `assert_called_with` 中将会调用 `Matcher` 的相等性方法，它会将调用 mock 时附带的对象与我们创建我们的匹配器时附带的对象进行比较。 如果它们匹配则 `assert_called_with` 通过，而如果不匹配则会引发 [`AssertionError`](3.标准库/exceptions.md#AssertionError "AssertionError"):

    
    
~~~shell
>>> match_wrong = Matcher(compare, Foo(3, 4))
>>> mock.assert_called_with(match_wrong)
Traceback (most recent call last):
    ...
AssertionError: Expected: ((<Matcher object at 0x...>,), {})
Called with: ((<Foo object at 0x...>,), {})
~~~

通过一些调整你可以让比较函数直接引发 [`AssertionError`](3.标准库/exceptions.md#AssertionError "AssertionError") 并提供更有用的失败消息。

从 1.5 版开始，Python 测试库 [PyHamcrest](https://pyhamcrest.readthedocs.io/) 提供了类似的功能，在这里可能会很有用，它采用的形式是相等性匹配器 ([hamcrest.library.integration.match_equality](https://pyhamcrest.readthedocs.io/en/release-1.8/integration/#module-hamcrest.library.integration.match_equality))。

