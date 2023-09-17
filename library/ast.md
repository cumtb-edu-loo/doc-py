# `ast` \--- 抽象语法树¶

**源代码：** [Lib/ast.py](https://github.com/python/cpython/tree/3.12/Lib/ast.py)

* * *

`ast` 模块帮助 Python 程序处理 Python 语法的抽象语法树。抽象语法或许会随着 Python 的更新版发行而改变；该模块能够帮助理解当前语法在编程层面的样貌。

抽象语法树可通过将 `ast.PyCF_ONLY_AST` 作为旗标传递给 [`compile()`](functions.md#compile "compile") 内置函数来生成，或是使用此模块中提供的 `parse()` 辅助函数。返回结果将是一个由许多对象构成的树，这些对象所属的类都继承自 `ast.AST`。抽象语法树可被内置的 [`compile()`](functions.md#compile "compile") 函数编译为一个 Python 代码对象。

## 抽象文法¶

抽象文法目前定义如下

    
    
~~~
-- ASDL's 4 builtin types are:
-- identifier, int, string, constant

module Python
{
    mod = Module(stmt* body, type_ignore* type_ignores)
        | Interactive(stmt* body)
        | Expression(expr body)
        | FunctionType(expr* argtypes, expr returns)

    stmt = FunctionDef(identifier name, arguments args,
                       stmt* body, expr* decorator_list, expr? returns,
                       string? type_comment, type_param* type_params)
          | AsyncFunctionDef(identifier name, arguments args,
                             stmt* body, expr* decorator_list, expr? returns,
                             string? type_comment, type_param* type_params)

          | ClassDef(identifier name,
             expr* bases,
             keyword* keywords,
             stmt* body,
             expr* decorator_list,
             type_param* type_params)
          | Return(expr? value)

          | Delete(expr* targets)
          | Assign(expr* targets, expr value, string? type_comment)
          | TypeAlias(expr name, type_param* type_params, expr value)
          | AugAssign(expr target, operator op, expr value)
          -- 'simple' indicates that we annotate simple name without parens
          | AnnAssign(expr target, expr annotation, expr? value, int simple)

          -- use 'orelse' because else is a keyword in target languages
          | For(expr target, expr iter, stmt* body, stmt* orelse, string? type_comment)
          | AsyncFor(expr target, expr iter, stmt* body, stmt* orelse, string? type_comment)
          | While(expr test, stmt* body, stmt* orelse)
          | If(expr test, stmt* body, stmt* orelse)
          | With(withitem* items, stmt* body, string? type_comment)
          | AsyncWith(withitem* items, stmt* body, string? type_comment)

          | Match(expr subject, match_case* cases)

          | Raise(expr? exc, expr? cause)
          | Try(stmt* body, excepthandler* handlers, stmt* orelse, stmt* finalbody)
          | TryStar(stmt* body, excepthandler* handlers, stmt* orelse, stmt* finalbody)
          | Assert(expr test, expr? msg)

          | Import(alias* names)
          | ImportFrom(identifier? module, alias* names, int? level)

          | Global(identifier* names)
          | Nonlocal(identifier* names)
          | Expr(expr value)
          | Pass | Break | Continue

          -- col_offset is the byte offset in the utf8 string the parser uses
          attributes (int lineno, int col_offset, int? end_lineno, int? end_col_offset)

          -- BoolOp() can use left & right?
    expr = BoolOp(boolop op, expr* values)
         | NamedExpr(expr target, expr value)
         | BinOp(expr left, operator op, expr right)
         | UnaryOp(unaryop op, expr operand)
         | Lambda(arguments args, expr body)
         | IfExp(expr test, expr body, expr orelse)
         | Dict(expr* keys, expr* values)
         | Set(expr* elts)
         | ListComp(expr elt, comprehension* generators)
         | SetComp(expr elt, comprehension* generators)
         | DictComp(expr key, expr value, comprehension* generators)
         | GeneratorExp(expr elt, comprehension* generators)
         -- the grammar constrains where yield expressions can occur
         | Await(expr value)
         | Yield(expr? value)
         | YieldFrom(expr value)
         -- need sequences for compare to distinguish between
         -- x < 4 < 3 and (x < 4) < 3
         | Compare(expr left, cmpop* ops, expr* comparators)
         | Call(expr func, expr* args, keyword* keywords)
         | FormattedValue(expr value, int conversion, expr? format_spec)
         | JoinedStr(expr* values)
         | Constant(constant value, string? kind)

         -- the following expression can appear in assignment context
         | Attribute(expr value, identifier attr, expr_context ctx)
         | Subscript(expr value, expr slice, expr_context ctx)
         | Starred(expr value, expr_context ctx)
         | Name(identifier id, expr_context ctx)
         | List(expr* elts, expr_context ctx)
         | Tuple(expr* elts, expr_context ctx)

         -- can appear only in Subscript
         | Slice(expr? lower, expr? upper, expr? step)

          -- col_offset is the byte offset in the utf8 string the parser uses
          attributes (int lineno, int col_offset, int? end_lineno, int? end_col_offset)

    expr_context = Load | Store | Del

    boolop = And | Or

    operator = Add | Sub | Mult | MatMult | Div | Mod | Pow | LShift
                 | RShift | BitOr | BitXor | BitAnd | FloorDiv

    unaryop = Invert | Not | UAdd | USub

    cmpop = Eq | NotEq | Lt | LtE | Gt | GtE | Is | IsNot | In | NotIn

    comprehension = (expr target, expr iter, expr* ifs, int is_async)

    excepthandler = ExceptHandler(expr? type, identifier? name, stmt* body)
                    attributes (int lineno, int col_offset, int? end_lineno, int? end_col_offset)

    arguments = (arg* posonlyargs, arg* args, arg? vararg, arg* kwonlyargs,
                 expr* kw_defaults, arg? kwarg, expr* defaults)

    arg = (identifier arg, expr? annotation, string? type_comment)
           attributes (int lineno, int col_offset, int? end_lineno, int? end_col_offset)

    -- keyword arguments supplied to call (NULL identifier for **kwargs)
    keyword = (identifier? arg, expr value)
               attributes (int lineno, int col_offset, int? end_lineno, int? end_col_offset)

    -- import name with optional 'as' alias.
    alias = (identifier name, identifier? asname)
             attributes (int lineno, int col_offset, int? end_lineno, int? end_col_offset)

    withitem = (expr context_expr, expr? optional_vars)

    match_case = (pattern pattern, expr? guard, stmt* body)

    pattern = MatchValue(expr value)
            | MatchSingleton(constant value)
            | MatchSequence(pattern* patterns)
            | MatchMapping(expr* keys, pattern* patterns, identifier? rest)
            | MatchClass(expr cls, pattern* patterns, identifier* kwd_attrs, pattern* kwd_patterns)

            | MatchStar(identifier? name)
            -- The optional "rest" MatchMapping parameter handles capturing extra mapping keys

            | MatchAs(pattern? pattern, identifier? name)
            | MatchOr(pattern* patterns)

             attributes (int lineno, int col_offset, int end_lineno, int end_col_offset)

    type_ignore = TypeIgnore(int lineno, string tag)

    type_param = TypeVar(identifier name, expr? bound)
               | ParamSpec(identifier name)
               | TypeVarTuple(identifier name)
               attributes (int lineno, int col_offset, int end_lineno, int end_col_offset)
}
~~~

## 节点类¶

_class _ast.AST¶

    

~~~
这是所有 AST 节点类的基类。实际上，这些节点类来源于 `Parser/Python.asdl` 文件，文件内容已被完整展示 如上。它们在 C 模块 `_ast` 中定义，并在这个 `ast` 模块中被“再导出”给用户。

抽象文法中的每个等号左边的符号（比方说， `ast.stmt` 或者 `ast.expr`）定义了一个类。另外，在等号右边，对每一个构造器也定义了一个类；这些类继承自等号左边的类。比如，`ast.BinOp` 继承自 `ast.expr`。对于多分支产生式（也就是含有“ | ”的产生式），左边的类是抽象的；只有具体构造器类的实例能够被 compile 函数构造。

_fields¶
~~~
    

~~~
每个具体类都有属性 `_fields`，用来给出所有子节点的名字。

每个具体类的实例为自己的每个子节点都准备了一个属性来引用该子节点，属性的类型就是文法中所定义的。比如，`ast.BinOp` 的实例有个属性 `left`，类型是 `ast.expr`。

如果这些属性在文法中标记为可选（用问号标记），对应值可能会是 `None`。如果这些属性可有零或多个值（用星号标记），对应值会用Python的列表来表示。在用 [`compile()`](functions.md#compile "compile") 将AST编译为可执行代码时，所有的属性必须已经被赋值为有效的值。

lineno¶

col_offset¶

end_lineno¶

end_col_offset¶
~~~
    

~~~
`ast.expr` 和 `ast.stmt` 的子类的实例的属性包括 `lineno`、`col_offset`、`end_lineno` 和 `end_col_offset`。`lineno` 和 `end_lineno` 是源码中属于该节点的部分从哪一行开始，到哪一行结束（数字 1 指第一行，以此类推）；`col_offset` 和 `end_col_offset` 是第一个和最后一个属于该节点的 token 的 UTF-8 字节偏移量。记录 UTF-8 偏移量的原因是解析器内部使用 UTF-8。

注意编译器不需要结束位置，所以结束位置是可选的。结束偏移在最后一个符号 _之后_ ，例如你可以通过 `source_line[node.col_offset : node.end_col_offset]` 获得一个单行表达式节点的源码片段。

`ast.T` 类的构造器像下面这样解析它的参数：

  * 如果只用位置参数，参数的数量必须和 `T._fields` 中的项一样多；它们会按顺序赋值到这些属性上。

  * 如果有关键字参数，它们会为与其关键字同名的属性赋值。

比方说，要创建和填充节点 `ast.UnaryOp`，你得用
~~~
    
    
~~~
node = ast.UnaryOp()
node.op = ast.USub()
node.operand = ast.Constant()
node.operand.value = 5
node.operand.lineno = 0
node.operand.col_offset = 0
node.lineno = 0
node.col_offset = 0
~~~

或者更紧凑点

    
    
~~~
node = ast.UnaryOp(ast.USub(), ast.Constant(5, lineno=0, col_offset=0),
                   lineno=0, col_offset=0)
~~~

在 3.8 版本发生变更: `ast.Constant` 类现在用于所有常量。

在 3.9 版本发生变更: 简单索引由它们的值表示，扩展切片表示为元组。

自 3.8 版本弃用: 旧的类 `ast.Num`、`ast.Str`、 `ast.Bytes`、`ast.NameConstant` 和 `ast.Ellipsis` 仍然有效，但是它们会在未来的 Python 版本中被移除。同时，实例化它们会返回一个不同类的实例。

自 3.9 版本弃用: 旧的类 `ast.Index` 和 `ast.ExtSlice` 仍然有效，但是它们会在未来的 Python 版本中被移除。同时，实例化它们会返回一个不同类的实例。

备注

在此显示的特定节点类的描述最初是改编自杰出的 [Green Tree Snakes](https://greentreesnakes.readthedocs.io/en/latest/) 项目及其所有贡献者。

### 根节点¶

_class _ast.Module( _body_ , _type_ignores_ )¶

    

~~~
一个 Python 模块，用于 [文件输入](../reference/toplevel_components.md#file-input)。 由 `ast.parse()` 以默认 `"exec"` _mode_ 生成的节点类型。

_body_ 是由该模块的 语句 组成的 [`list`](stdtypes.md#list "list")。

_type_ignores_ 是由该模块的类型忽略注释组成的 [`list`](stdtypes.md#list "list")；请参阅 `ast.parse()` 了解更多细节。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('x = 1'), indent=4))
Module(
    body=[
        Assign(
            targets=[
                Name(id='x', ctx=Store())],
            value=Constant(value=1))],
    type_ignores=[])
~~~

_class _ast.Expression( _body_ )¶

    

~~~
单个 Python [表达式输入](../reference/toplevel_components.md#expression-input)。 当 _mode_ 为 `"eval"` 时由 `ast.parse()` 所生成的节点类型。

_body_ 为单独节点，是 表达式类型 中的某一个。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('123', mode='eval'), indent=4))
Expression(
    body=Constant(value=123))
~~~

_class _ast.Interactive( _body_ )¶

    

~~~
单个 [交互式输入](../reference/toplevel_components.md#interactive)，就像在 [交互模式](../tutorial/appendix.md#tut-interac) 中一样。 当 _mode_ 为 `"single"` 时由 `ast.parse()` 所生成的节点类型。

_body_ 是由 语句节点 组成的 [`list`](stdtypes.md#list "list")。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('x = 1; y = 2', mode='single'), indent=4))
Interactive(
    body=[
        Assign(
            targets=[
                Name(id='x', ctx=Store())],
            value=Constant(value=1)),
        Assign(
            targets=[
                Name(id='y', ctx=Store())],
            value=Constant(value=2))])
~~~

_class _ast.FunctionType( _argtypes_ , _returns_ )¶

    

~~~
函数的旧风格类型注释表示形式，因为 Python 3.5 之前的版本不支持 [**PEP 484**](https://peps.python.org/pep-0484/) 标注。 当 _mode_ is `"func_type"` 时由 `ast.parse()` 所生成的节点类型。

此种类型注释的形式是这样的:
~~~
    
    
~~~python
def sum_two_number(a, b):
    # type: (int, int) -> int
    return a + b
~~~

_argtypes_ 是由 表达式节点 组成的 [`list`](stdtypes.md#list "list")。

_returns_ 是单独的 表达式节点。

    
    
~~~shell
>>> print(ast.dump(ast.parse('(int, str) -> List[int]', mode='func_type'), indent=4))
FunctionType(
    argtypes=[
        Name(id='int', ctx=Load()),
        Name(id='str', ctx=Load())],
    returns=Subscript(
        value=Name(id='List', ctx=Load()),
        slice=Name(id='int', ctx=Load()),
        ctx=Load()))
~~~

在 3.8 版本加入.

### 字面值¶

_class _ast.Constant( _value_ )¶

    

~~~
一个常量。 `Constant` 字面值的 `value` 属性即为其代表的 Python 对象。它可以代表简单的数字，字符串或者 `None` 对象，但是也可以代表所有元素都是常量的不可变容器（例如元组或冻结集合）。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('123', mode='eval'), indent=4))
Expression(
    body=Constant(value=123))
~~~

_class _ast.FormattedValue( _value_ , _conversion_ , _format_spec_ )¶

    

~~~
节点是以一个 f-字符串形式的格式化字段来代表的。 如果该字符串只包含单个格式化字段而没有任何其他内容则节点可以被隔离，否则它将在 `JoinedStr` 中出现。

  * `value` 为任意的表达式节点（如一个字面值、变量或函数调用）。

  * `conversion` 是一个整数:

    * -1: 无格式化

    * 115: `!s` 字符串格式化

    * 114: `!r` repr 格式化

    * 97: `!a` ascii 格式化

  * `format_spec` 是一个代表值的格式化的 `JoinedStr` 节点，或者如果未指定格式则为 `None`。 `conversion` 和 `format_spec` 可以被同时设置。

_class _ast.JoinedStr( _values_ )¶
~~~
    

~~~
一个 f-字符串，由一系列 `FormattedValue` 和 `Constant` 节点组成。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('f"sin({a}) is {sin(a):.3}"', mode='eval'), indent=4))
Expression(
    body=JoinedStr(
        values=[
            Constant(value='sin('),
            FormattedValue(
                value=Name(id='a', ctx=Load()),
                conversion=-1),
            Constant(value=') is '),
            FormattedValue(
                value=Call(
                    func=Name(id='sin', ctx=Load()),
                    args=[
                        Name(id='a', ctx=Load())],
                    keywords=[]),
                conversion=-1,
                format_spec=JoinedStr(
                    values=[
                        Constant(value='.3')]))]))
~~~

_class _ast.List( _elts_ , _ctx_ )¶

_class _ast.Tuple( _elts_ , _ctx_ )¶

    

~~~
一个列表或元组。 `elts` 保存一个代表元素的节点的列表。 `ctx` 在容器为赋值的目标时 (如 `(x,y)=something`) 是 `Store`，否则是 `Load`。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('[1, 2, 3]', mode='eval'), indent=4))
Expression(
    body=List(
        elts=[
            Constant(value=1),
            Constant(value=2),
            Constant(value=3)],
        ctx=Load()))
>>> print(ast.dump(ast.parse('(1, 2, 3)', mode='eval'), indent=4))
Expression(
    body=Tuple(
        elts=[
            Constant(value=1),
            Constant(value=2),
            Constant(value=3)],
        ctx=Load()))
~~~

_class _ast.Set( _elts_ )¶

    

~~~
一个集合。 `elts` 保存一个代表集合的元组的节点的列表。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('{1, 2, 3}', mode='eval'), indent=4))
Expression(
    body=Set(
        elts=[
            Constant(value=1),
            Constant(value=2),
            Constant(value=3)]))
~~~

_class _ast.Dict( _keys_ , _values_ )¶

    

~~~
一个字典。 `keys` 和 `values` 保存分别代表键和值的节点的列表，按照匹配的顺序（即当调用 `dictionary.keys()` 和 `dictionary.values()` 时将返回的结果）。

当使用字典字面值进行字典解包操作时要扩展的表达式放入 `values` 列表，并将 `None` 放入 `keys` 的对应位置。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('{"a":1, **d}', mode='eval'), indent=4))
Expression(
    body=Dict(
        keys=[
            Constant(value='a'),
            None],
        values=[
            Constant(value=1),
            Name(id='d', ctx=Load())]))
~~~

### 变量¶

_class _ast.Name( _id_ , _ctx_ )¶

    

~~~
一个变量名。 `id` 将名称保存为字符串，而 `ctx` 为下列类型之一。

_class _ast.Load¶

_class _ast.Store¶

_class _ast.Del¶
~~~
    

~~~
变量引用可被用来载入一个变量的值，为其赋一个新值，或是将其删除。 变量引用会给出一个上下文来区分这几种情况。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('a'), indent=4))
Module(
    body=[
        Expr(
            value=Name(id='a', ctx=Load()))],
    type_ignores=[])

>>> print(ast.dump(ast.parse('a = 1'), indent=4))
Module(
    body=[
        Assign(
            targets=[
                Name(id='a', ctx=Store())],
            value=Constant(value=1))],
    type_ignores=[])

>>> print(ast.dump(ast.parse('del a'), indent=4))
Module(
    body=[
        Delete(
            targets=[
                Name(id='a', ctx=Del())])],
    type_ignores=[])
~~~

_class _ast.Starred( _value_ , _ctx_ )¶

    

~~~
一个 `*var` 变量引用。 `value` 保存变量，通常为一个 `Name` 节点。 此类型必须在构建 `Call` 节点并传入 `*args` 时被使用。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('a, *b = it'), indent=4))
Module(
    body=[
        Assign(
            targets=[
                Tuple(
                    elts=[
                        Name(id='a', ctx=Store()),
                        Starred(
                            value=Name(id='b', ctx=Store()),
                            ctx=Store())],
                    ctx=Store())],
            value=Name(id='it', ctx=Load()))],
    type_ignores=[])
~~~

### 表达式¶

_class _ast.Expr( _value_ )¶

    

~~~
当一个表达式，例如函数调用，本身作为一个语句出现并且其返回值未被使用或存储时，它会被包装在此容器中。 `value` 保存本节中的其他节点之一，一个 `Constant`, `Name`, `Lambda`, `Yield` 或者 `YieldFrom` 节点。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('-a'), indent=4))
Module(
    body=[
        Expr(
            value=UnaryOp(
                op=USub(),
                operand=Name(id='a', ctx=Load())))],
    type_ignores=[])
~~~

_class _ast.UnaryOp( _op_ , _operand_ )¶

    

~~~
一个单目运算。 `op` 是运算符，而 `operand` 是任意表达式节点。

_class _ast.UAdd¶

_class _ast.USub¶

_class _ast.Not¶

_class _ast.Invert¶
~~~
    

~~~
单目运算符对应的形符。 `Not` 是 `not` 关键字，`Invert` 是 `~` 运算符。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('not x', mode='eval'), indent=4))
Expression(
    body=UnaryOp(
        op=Not(),
        operand=Name(id='x', ctx=Load())))
~~~

_class _ast.BinOp( _left_ , _op_ , _right_ )¶

    

~~~
一个双目运算（如相加或相减）。 `op` 是运算符，而 `left` 和 `right` 是任意表达式节点。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('x + y', mode='eval'), indent=4))
Expression(
    body=BinOp(
        left=Name(id='x', ctx=Load()),
        op=Add(),
        right=Name(id='y', ctx=Load())))
~~~

_class _ast.Add¶

_class _ast.Sub¶

_class _ast.Mult¶

_class _ast.Div¶

_class _ast.FloorDiv¶

_class _ast.Mod¶

_class _ast.Pow¶

_class _ast.LShift¶

_class _ast.RShift¶

_class _ast.BitOr¶

_class _ast.BitXor¶

_class _ast.BitAnd¶

_class _ast.MatMult¶

    

~~~
双目运算符对应的形符。

_class _ast.BoolOp( _op_ , _values_ )¶
~~~
    

~~~
一个布尔运算，'or' 或者 'and'。 `op` 是 `Or` 或者 `And`。 `values` 是参与运算的值。 具有相同运算符的连续运算，如 `a or b or c`，会被折叠为具有多个值的单个节点。

这不包括 `not`，它属于 `UnaryOp`。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('x or y', mode='eval'), indent=4))
Expression(
    body=BoolOp(
        op=Or(),
        values=[
            Name(id='x', ctx=Load()),
            Name(id='y', ctx=Load())]))
~~~

_class _ast.And¶

_class _ast.Or¶

    

~~~
布尔运算符对应的形符。

_class _ast.Compare( _left_ , _ops_ , _comparators_ )¶
~~~
    

~~~
两个或更多值之间的比较运算。 `left` 是参加比较的第一个值，`ops` 是由运算符组成的列表，而 `comparators` 是由参加比较的第一个元素之后的值组成的列表。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('1 <= a < 10', mode='eval'), indent=4))
Expression(
    body=Compare(
        left=Constant(value=1),
        ops=[
            LtE(),
            Lt()],
        comparators=[
            Name(id='a', ctx=Load()),
            Constant(value=10)]))
~~~

_class _ast.Eq¶

_class _ast.NotEq¶

_class _ast.Lt¶

_class _ast.LtE¶

_class _ast.Gt¶

_class _ast.GtE¶

_class _ast.Is¶

_class _ast.IsNot¶

_class _ast.In¶

_class _ast.NotIn¶

    

~~~
比较运算符对应的形符。

_class _ast.Call( _func_ , _args_ , _keywords_ )¶
~~~
    

~~~
一个函数调用。 `func` 是函数，它通常是一个 `Name` 或 `Attribute` 对象。 对于其参数:

  * `args` 保存由按位置传入的参数组成的列表。

  * `keywords` 保存了一个代表以关键字传入的参数的 `keyword` 对象的列表。

当创建 `Call` 节点时，需要有 `args` 和 `keywords`，但它们可以为空列表。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('func(a, b=c, *d, **e)', mode='eval'), indent=4))
Expression(
    body=Call(
        func=Name(id='func', ctx=Load()),
        args=[
            Name(id='a', ctx=Load()),
            Starred(
                value=Name(id='d', ctx=Load()),
                ctx=Load())],
        keywords=[
            keyword(
                arg='b',
                value=Name(id='c', ctx=Load())),
            keyword(
                value=Name(id='e', ctx=Load()))]))
~~~

_class _ast.keyword( _arg_ , _value_ )¶

    

~~~
传给函数调用或类定义的关键字参数。 `arg` 是形参名称对应的原始字符串，`value` 是要传入的节点。

_class _ast.IfExp( _test_ , _body_ , _orelse_ )¶
~~~
    

~~~
一个表达式例如 `a if b else c`。 每个字段保存一个单独节点，因而在下面的示例中，三个节点均为 `Name` 节点。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('a if b else c', mode='eval'), indent=4))
Expression(
    body=IfExp(
        test=Name(id='b', ctx=Load()),
        body=Name(id='a', ctx=Load()),
        orelse=Name(id='c', ctx=Load())))
~~~

_class _ast.Attribute( _value_ , _attr_ , _ctx_ )¶

    

~~~
属性访问，例如 `d.keys`。 `value` 是一个节点，通常为 `Name`。 `attr` 是一个给出属性名称的纯字符串，而 `ctx` 根据属性操作的方式可以为 `Load`, `Store` 或 `Del`。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('snake.colour', mode='eval'), indent=4))
Expression(
    body=Attribute(
        value=Name(id='snake', ctx=Load()),
        attr='colour',
        ctx=Load()))
~~~

_class _ast.NamedExpr( _target_ , _value_ )¶

    

~~~
一个带名称的表达式。 此 AST 节点是由赋值表达式运算符（或称海象运算符）产生的。 与第一个参数可以有多个节点的 `Assign` 节点不同，在此情况下 `target` 和 `value` 都必须为单独节点。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('(x := 4)', mode='eval'), indent=4))
Expression(
    body=NamedExpr(
        target=Name(id='x', ctx=Store()),
        value=Constant(value=4)))
~~~

在 3.8 版本加入.

#### 抽取¶

_class _ast.Subscript( _value_ , _slice_ , _ctx_ )¶

    

~~~
抽取操作，如 `l[1]`。 `value` 是被抽取的对象（通常为序列或映射）。 `slice` 是索引号、切片或键。 它可以是一个包含 `Slice` 的 `Tuple`。 `ctx` 根据抽取所执行的操作可以为 `Load`, `Store` 或 `Del`。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('l[1:2, 3]', mode='eval'), indent=4))
Expression(
    body=Subscript(
        value=Name(id='l', ctx=Load()),
        slice=Tuple(
            elts=[
                Slice(
                    lower=Constant(value=1),
                    upper=Constant(value=2)),
                Constant(value=3)],
            ctx=Load()),
        ctx=Load()))
~~~

_class _ast.Slice( _lower_ , _upper_ , _step_ )¶

    

~~~
常规切片 (形式如 `lower:upper` 或 `lower:upper:step`)。 只能在 `Subscript` 的 _slice_ 字段内部出现，可以是直接切片对象或是作为 `Tuple` 的元素。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('l[1:2]', mode='eval'), indent=4))
Expression(
    body=Subscript(
        value=Name(id='l', ctx=Load()),
        slice=Slice(
            lower=Constant(value=1),
            upper=Constant(value=2)),
        ctx=Load()))
~~~

#### 推导式¶

_class _ast.ListComp( _elt_ , _generators_ )¶

_class _ast.SetComp( _elt_ , _generators_ )¶

_class _ast.GeneratorExp( _elt_ , _generators_ )¶

_class _ast.DictComp( _key_ , _value_ , _generators_ )¶

    

~~~
列表和集合推导式、生成器表达式以及字典推导式。 `elt` (或 `key` 和 `value`) 是一个代表将针对每个条目被求值的部分的单独节点。

`generators` 是一个由 `comprehension` 节点组成的列表。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('[x for x in numbers]', mode='eval'), indent=4))
Expression(
    body=ListComp(
        elt=Name(id='x', ctx=Load()),
        generators=[
            comprehension(
                target=Name(id='x', ctx=Store()),
                iter=Name(id='numbers', ctx=Load()),
                ifs=[],
                is_async=0)]))
>>> print(ast.dump(ast.parse('{x: x**2 for x in numbers}', mode='eval'), indent=4))
Expression(
    body=DictComp(
        key=Name(id='x', ctx=Load()),
        value=BinOp(
            left=Name(id='x', ctx=Load()),
            op=Pow(),
            right=Constant(value=2)),
        generators=[
            comprehension(
                target=Name(id='x', ctx=Store()),
                iter=Name(id='numbers', ctx=Load()),
                ifs=[],
                is_async=0)]))
>>> print(ast.dump(ast.parse('{x for x in numbers}', mode='eval'), indent=4))
Expression(
    body=SetComp(
        elt=Name(id='x', ctx=Load()),
        generators=[
            comprehension(
                target=Name(id='x', ctx=Store()),
                iter=Name(id='numbers', ctx=Load()),
                ifs=[],
                is_async=0)]))
~~~

_class _ast.comprehension( _target_ , _iter_ , _ifs_ , _is_async_ )¶

    

~~~
推导式中的一个 `for` 子句。 `target` 是针对每个元素使用的引用 —— 通常为一个 `Name` 或 `Tuple` 节点。 `iter` 是要执行迭代的对象。 `ifs` 是一个由测试表达式组成的列表：每个 `for` 子句都可以拥有多个 `ifs`。

`is_async` 表明推导式是异步的 (使用 `async for` 而不是 `for`)。 它的值是一个整数 (0 或 1)。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('[ord(c) for line in file for c in line]', mode='eval'),
...                indent=4)) # Multiple comprehensions in one.
Expression(
    body=ListComp(
        elt=Call(
            func=Name(id='ord', ctx=Load()),
            args=[
                Name(id='c', ctx=Load())],
            keywords=[]),
        generators=[
            comprehension(
                target=Name(id='line', ctx=Store()),
                iter=Name(id='file', ctx=Load()),
                ifs=[],
                is_async=0),
            comprehension(
                target=Name(id='c', ctx=Store()),
                iter=Name(id='line', ctx=Load()),
                ifs=[],
                is_async=0)]))

>>> print(ast.dump(ast.parse('(n**2 for n in it if n>5 if n<10)', mode='eval'),
...                indent=4)) # generator comprehension
Expression(
    body=GeneratorExp(
        elt=BinOp(
            left=Name(id='n', ctx=Load()),
            op=Pow(),
            right=Constant(value=2)),
        generators=[
            comprehension(
                target=Name(id='n', ctx=Store()),
                iter=Name(id='it', ctx=Load()),
                ifs=[
                    Compare(
                        left=Name(id='n', ctx=Load()),
                        ops=[
                            Gt()],
                        comparators=[
                            Constant(value=5)]),
                    Compare(
                        left=Name(id='n', ctx=Load()),
                        ops=[
                            Lt()],
                        comparators=[
                            Constant(value=10)])],
                is_async=0)]))

>>> print(ast.dump(ast.parse('[i async for i in soc]', mode='eval'),
...                indent=4)) # Async comprehension
Expression(
    body=ListComp(
        elt=Name(id='i', ctx=Load()),
        generators=[
            comprehension(
                target=Name(id='i', ctx=Store()),
                iter=Name(id='soc', ctx=Load()),
                ifs=[],
                is_async=1)]))
~~~

### 语句¶

_class _ast.Assign( _targets_ , _value_ , _type_comment_ )¶

    

~~~
一次赋值。 `targets` 是一个由节点组成的列表，而 `value` 是一个单独节点。

`targets` 中有多个节点表示将同一个值赋给多个目标。 解包操作是通过在 `targets` 中放入一个 `Tuple` 或 `List` 来表示的。

type_comment¶
~~~
    

~~~
`type_comment` 是带有以注释表示的类型标注的可选的字符串。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('a = b = 1'), indent=4)) # Multiple assignment
Module(
    body=[
        Assign(
            targets=[
                Name(id='a', ctx=Store()),
                Name(id='b', ctx=Store())],
            value=Constant(value=1))],
    type_ignores=[])

>>> print(ast.dump(ast.parse('a,b = c'), indent=4)) # Unpacking
Module(
    body=[
        Assign(
            targets=[
                Tuple(
                    elts=[
                        Name(id='a', ctx=Store()),
                        Name(id='b', ctx=Store())],
                    ctx=Store())],
            value=Name(id='c', ctx=Load()))],
    type_ignores=[])
~~~

_class _ast.AnnAssign( _target_ , _annotation_ , _value_ , _simple_ )¶

    

~~~
一次带有类型标注的赋值。 `target` 为单独节点并可以是 `Name`, `Attribute` 或 `Subscript` 之一。 `annotation` 为标注，例如一个 `Constant` 或 `Name` 节点。 `value` 为可选的单独节点。 `simple` 为一个布尔整数，其值对于 `target` 中不出现在括号之内因而是纯名称而非表达式的 `Name` 节点将为 True。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('c: int'), indent=4))
Module(
    body=[
        AnnAssign(
            target=Name(id='c', ctx=Store()),
            annotation=Name(id='int', ctx=Load()),
            simple=1)],
    type_ignores=[])

>>> print(ast.dump(ast.parse('(a): int = 1'), indent=4)) # Annotation with parenthesis
Module(
    body=[
        AnnAssign(
            target=Name(id='a', ctx=Store()),
            annotation=Name(id='int', ctx=Load()),
            value=Constant(value=1),
            simple=0)],
    type_ignores=[])

>>> print(ast.dump(ast.parse('a.b: int'), indent=4)) # Attribute annotation
Module(
    body=[
        AnnAssign(
            target=Attribute(
                value=Name(id='a', ctx=Load()),
                attr='b',
                ctx=Store()),
            annotation=Name(id='int', ctx=Load()),
            simple=0)],
    type_ignores=[])

>>> print(ast.dump(ast.parse('a[1]: int'), indent=4)) # Subscript annotation
Module(
    body=[
        AnnAssign(
            target=Subscript(
                value=Name(id='a', ctx=Load()),
                slice=Constant(value=1),
                ctx=Store()),
            annotation=Name(id='int', ctx=Load()),
            simple=0)],
    type_ignores=[])
~~~

_class _ast.AugAssign( _target_ , _op_ , _value_ )¶

    

~~~
增强赋值，如 `a += 1`。 在下面的例子中，`target` 是一个针对 `x` (带有 `Store` 上下文) 的 `Name` 节点，`op` 为 `Add`，而 `value` 是一个值为 1 的 `Constant`。

`target` 属性不可以是 `Tuple` 或 `List` 类，这与 `Assign` 的目标不同。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('x += 2'), indent=4))
Module(
    body=[
        AugAssign(
            target=Name(id='x', ctx=Store()),
            op=Add(),
            value=Constant(value=2))],
    type_ignores=[])
~~~

_class _ast.Raise( _exc_ , _cause_ )¶

    

~~~
一条 `raise` 语句。 `exc` 是要引发的异常，对于一个单独的 `raise` 通常为 `Call` 或 `Name`，或者为 `None`。 `cause` 是针对 `raise x from y` 中 `y` 的可选部分。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('raise x from y'), indent=4))
Module(
    body=[
        Raise(
            exc=Name(id='x', ctx=Load()),
            cause=Name(id='y', ctx=Load()))],
    type_ignores=[])
~~~

_class _ast.Assert( _test_ , _msg_ )¶

    

~~~
一条断言。 `test` 保存条件，例如为一个 `Compare` 节点。 `msg` 保存失败消息。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('assert x,y'), indent=4))
Module(
    body=[
        Assert(
            test=Name(id='x', ctx=Load()),
            msg=Name(id='y', ctx=Load()))],
    type_ignores=[])
~~~

_class _ast.Delete( _targets_ )¶

    

~~~
代表一条 `del` 语句。 `targets` 是一个由节点组成的列表，例如 `Name`, `Attribute` 或 `Subscript` 节点。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('del x,y,z'), indent=4))
Module(
    body=[
        Delete(
            targets=[
                Name(id='x', ctx=Del()),
                Name(id='y', ctx=Del()),
                Name(id='z', ctx=Del())])],
    type_ignores=[])
~~~

_class _ast.Pass¶

    

~~~
一条 `pass` 语句。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('pass'), indent=4))
Module(
    body=[
        Pass()],
    type_ignores=[])
~~~

_class _ast.TypeAlias( _name_ , _type_params_ , _value_ )¶

    

~~~
通过 [`type`](../reference/simple_stmts.md#type) 语句创建的 [类型别名](typing.md#type-aliases)。 `name` 是别名的名称，`type_params` 是 类型形参 的列表，而 `value` 是类型别名的值。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('type Alias = int'), indent=4))
Module(
    body=[
        TypeAlias(
            name=Name(id='Alias', ctx=Store()),
            type_params=[],
            value=Name(id='int', ctx=Load()))],
    type_ignores=[])
~~~

在 3.12 版本加入.

其他仅在函数或循环内部可用的语句将在其他小节中描述。

#### 导入¶

_class _ast.Import( _names_ )¶

    

~~~
一条导入语句。 `names` 是一个由 `alias` 节点组成的列表。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('import x,y,z'), indent=4))
Module(
    body=[
        Import(
            names=[
                alias(name='x'),
                alias(name='y'),
                alias(name='z')])],
    type_ignores=[])
~~~

_class _ast.ImportFrom( _module_ , _names_ , _level_ )¶

    

~~~
代表 `from x import y`。 `module` 是一个 'from' 名称的原始字符串，不带任何前导点号，或者为 `None` 表示 `from . import foo` 这样的语句。 `level` 是一个保存相对导入层级的整数（0 表示绝对导入）。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('from y import x,y,z'), indent=4))
Module(
    body=[
        ImportFrom(
            module='y',
            names=[
                alias(name='x'),
                alias(name='y'),
                alias(name='z')],
            level=0)],
    type_ignores=[])
~~~

_class _ast.alias( _name_ , _asname_ )¶

    

~~~
两个形参均为名称的原始字符串。 如果要使用常规名称则 `asname` 可以为 `None`。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('from ..foo.bar import a as b, c'), indent=4))
Module(
    body=[
        ImportFrom(
            module='foo.bar',
            names=[
                alias(name='a', asname='b'),
                alias(name='c')],
            level=2)],
    type_ignores=[])
~~~

### 控制流¶

备注

可选的子句如 `else` 如果不存在则会被存储为一个空列表。

_class _ast.If( _test_ , _body_ , _orelse_ )¶

    

~~~
一条 `if` 语句。 `test` 保存一个单独节点，如一个 `Compare` 节点。 `body` 和 `orelse` 各自保存一个节点列表。

`elif` 子句在 AST 中没有特别的表示形式，而是作为上文介绍的 `orelse` 部分之内的一个额外 `If` 节点出现。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... if x:
...    ...
... elif y:
...    ...
... else:
...    ...
... """), indent=4))
Module(
    body=[
        If(
            test=Name(id='x', ctx=Load()),
            body=[
                Expr(
                    value=Constant(value=Ellipsis))],
            orelse=[
                If(
                    test=Name(id='y', ctx=Load()),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))],
                    orelse=[
                        Expr(
                            value=Constant(value=Ellipsis))])])],
    type_ignores=[])
~~~

_class _ast.For( _target_ , _iter_ , _body_ , _orelse_ , _type_comment_ )¶

    

~~~
一个 `for` 循环。 `target` 保存循环赋值的变量，是一个单独的 `Name`, `Tuple`, `List`, `Attribute` 或 `Subscript` 节点。 `iter` 保存要被循环的条目，同样也是一个单独节点。 `body` 和 `orelse` 包含要执行的节点列表。 `orelse` 中的语句会在循环正常结束时被执行，而不是通过 `break` 语句执行。

type_comment¶
~~~
    

~~~
`type_comment` 是带有以注释表示的类型标注的可选的字符串。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... for x in y:
...     ...
... else:
...     ...
... """), indent=4))
Module(
    body=[
        For(
            target=Name(id='x', ctx=Store()),
            iter=Name(id='y', ctx=Load()),
            body=[
                Expr(
                    value=Constant(value=Ellipsis))],
            orelse=[
                Expr(
                    value=Constant(value=Ellipsis))])],
    type_ignores=[])
~~~

_class _ast.While( _test_ , _body_ , _orelse_ )¶

    

~~~
一个 `while` 循环。 `test` 保存条件，如一个 `Compare` 节点。
~~~
    
    
~~~
>> print(ast.dump(ast.parse("""
... while x:
...    ...
... else:
...    ...
... """), indent=4))
Module(
    body=[
        While(
            test=Name(id='x', ctx=Load()),
            body=[
                Expr(
                    value=Constant(value=Ellipsis))],
            orelse=[
                Expr(
                    value=Constant(value=Ellipsis))])],
    type_ignores=[])
~~~

_class _ast.Break¶

_class _ast.Continue¶

    

~~~
`break` 和 `continue` 语句。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""\
... for a in b:
...     if a > 5:
...         break
...     else:
...         continue
...
... """), indent=4))
Module(
    body=[
        For(
            target=Name(id='a', ctx=Store()),
            iter=Name(id='b', ctx=Load()),
            body=[
                If(
                    test=Compare(
                        left=Name(id='a', ctx=Load()),
                        ops=[
                            Gt()],
                        comparators=[
                            Constant(value=5)]),
                    body=[
                        Break()],
                    orelse=[
                        Continue()])],
            orelse=[])],
    type_ignores=[])
~~~

_class _ast.Try( _body_ , _handlers_ , _orelse_ , _finalbody_ )¶

    

~~~
`try` 代码块。 所有属性都是要执行的节点列表，除了 `handlers`，它是一个 `ExceptHandler` 节点列表。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... try:
...    ...
... except Exception:
...    ...
... except OtherException as e:
...    ...
... else:
...    ...
... finally:
...    ...
... """), indent=4))
Module(
    body=[
        Try(
            body=[
                Expr(
                    value=Constant(value=Ellipsis))],
            handlers=[
                ExceptHandler(
                    type=Name(id='Exception', ctx=Load()),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))]),
                ExceptHandler(
                    type=Name(id='OtherException', ctx=Load()),
                    name='e',
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])],
            orelse=[
                Expr(
                    value=Constant(value=Ellipsis))],
            finalbody=[
                Expr(
                    value=Constant(value=Ellipsis))])],
    type_ignores=[])
~~~

_class _ast.TryStar( _body_ , _handlers_ , _orelse_ , _finalbody_ )¶

    

~~~
`try` 代码块后带有 `except*` 子句。 包含的属性与 `Try` 的相同但 `ExceptHandler` 节点在 `handlers` 中会被解读为 `except*` 而不是 `except` 代码块。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... try:
...    ...
... except* Exception:
...    ...
... """), indent=4))
Module(
    body=[
        TryStar(
            body=[
                Expr(
                    value=Constant(value=Ellipsis))],
            handlers=[
                ExceptHandler(
                    type=Name(id='Exception', ctx=Load()),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])],
            orelse=[],
            finalbody=[])],
    type_ignores=[])
~~~

在 3.11 版本加入.

_class _ast.ExceptHandler( _type_ , _name_ , _body_ )¶

    

~~~
一个单独的 `except` 子句。 `type` 是它将匹配的异常，通常为一个 `Name` 节点（或 `None` 表示捕获全部的 `except:` 子句）。 `name` 是一个用于存放异常的别名的原始字符串，或者如果子句没有 `as foo` 则为 `None`。 `body` 为一个节点列表。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""\
... try:
...     a + 1
... except TypeError:
...     pass
... """), indent=4))
Module(
    body=[
        Try(
            body=[
                Expr(
                    value=BinOp(
                        left=Name(id='a', ctx=Load()),
                        op=Add(),
                        right=Constant(value=1)))],
            handlers=[
                ExceptHandler(
                    type=Name(id='TypeError', ctx=Load()),
                    body=[
                        Pass()])],
            orelse=[],
            finalbody=[])],
    type_ignores=[])
~~~

_class _ast.With( _items_ , _body_ , _type_comment_ )¶

    

~~~
一个 `with` 代码块。 `items` 是一个代表上下文管理器的 `withitem` 节点列表，而 `body` 是该上下文中的缩进代码块。

type_comment¶
~~~
    

~~~
`type_comment` 是带有以注释表示的类型标注的可选的字符串。

_class _ast.withitem( _context_expr_ , _optional_vars_ )¶
~~~
    

~~~
一个 `with` 代码块中单独的上下文管理器。 `context_expr` 为上下文管理器，通常为一个 `Call` 节点。 `optional_vars` 为一个针对 `as foo` 部分的 `Name`, `Tuple` 或 `List`，或者如果未使用别名则为 `None`。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""\
... with a as b, c as d:
...    something(b, d)
... """), indent=4))
Module(
    body=[
        With(
            items=[
                withitem(
                    context_expr=Name(id='a', ctx=Load()),
                    optional_vars=Name(id='b', ctx=Store())),
                withitem(
                    context_expr=Name(id='c', ctx=Load()),
                    optional_vars=Name(id='d', ctx=Store()))],
            body=[
                Expr(
                    value=Call(
                        func=Name(id='something', ctx=Load()),
                        args=[
                            Name(id='b', ctx=Load()),
                            Name(id='d', ctx=Load())],
                        keywords=[]))])],
    type_ignores=[])
~~~

### 模式匹配¶

_class _ast.Match( _subject_ , _cases_ )¶

    

~~~
一条 `match` 语句。 `subject` 保存匹配的目标（与 cases 相匹配的对象）而 `cases` 包含一个由不同分支的 `match_case` 节点组成的可迭代对象。

在 3.10 版本加入.

_class _ast.match_case( _pattern_ , _guard_ , _body_ )¶
~~~
    

~~~
一个 `match` 语句中单独的 case 模式。 `pattern` 包含目标将要去匹配的匹配模式。 请注意针对模式所产生的 `AST` 节点不同于针对表达式所产生的节点，即使它们共享相同的语法。

`guard` 属性包含一个当模式与目标相匹配时将被求值的表达式。

`body` 包含一个当模式匹配并且对 guard 表达式求值的结果为真时要执行的节点列表。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... match x:
...     case [x] if x>0:
...         ...
...     case tuple():
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchSequence(
                        patterns=[
                            MatchAs(name='x')]),
                    guard=Compare(
                        left=Name(id='x', ctx=Load()),
                        ops=[
                            Gt()],
                        comparators=[
                            Constant(value=0)]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))]),
                match_case(
                    pattern=MatchClass(
                        cls=Name(id='tuple', ctx=Load()),
                        patterns=[],
                        kwd_attrs=[],
                        kwd_patterns=[]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])],
    type_ignores=[])
~~~

在 3.10 版本加入.

_class _ast.MatchValue( _value_ )¶

    

~~~
一个按相等性进行比较的匹配字面值或值模式。 `value` 为一个表达式节点。 允许的值节点被限制为 match 语句文档中所描述的节点。 如果匹配目标等于 value 的求值结果则模式匹配成功。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... match x:
...     case "Relevant":
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchValue(
                        value=Constant(value='Relevant')),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])],
    type_ignores=[])
~~~

在 3.10 版本加入.

_class _ast.MatchSingleton( _value_ )¶

    

~~~
一个按标识号进行比较的匹配字面值模式。 `value` 为用于比较的单例对象: `None`, `True` 或 `False`。 如果匹配目标为给定的常量则该模式匹配成功。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... match x:
...     case None:
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchSingleton(value=None),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])],
    type_ignores=[])
~~~

在 3.10 版本加入.

_class _ast.MatchSequence( _patterns_ )¶

    

~~~
一个匹配序列模式。 `patterns` 包含当目标为一个序列时要与目标元素进行匹配的模式。 如果某一子模式为 `MatchStar` 节点则将匹配一个变长度序列，否则将匹配一个固定长度序列。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... match x:
...     case [1, 2]:
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchSequence(
                        patterns=[
                            MatchValue(
                                value=Constant(value=1)),
                            MatchValue(
                                value=Constant(value=2))]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])],
    type_ignores=[])
~~~

在 3.10 版本加入.

_class _ast.MatchStar( _name_ )¶

    

~~~
匹配一个可变长度匹配序列模式中的剩余部分序列。 如果 `name` 不为 `None`，则当整个序列模式匹配成功时将把一个包含剩余序列元素的列表绑定到该名称。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... match x:
...     case [1, 2, *rest]:
...         ...
...     case [*_]:
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchSequence(
                        patterns=[
                            MatchValue(
                                value=Constant(value=1)),
                            MatchValue(
                                value=Constant(value=2)),
                            MatchStar(name='rest')]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))]),
                match_case(
                    pattern=MatchSequence(
                        patterns=[
                            MatchStar()]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])],
    type_ignores=[])
~~~

在 3.10 版本加入.

_class _ast.MatchMapping( _keys_ , _patterns_ , _rest_ )¶

    

~~~
一个匹配的映射模式。 `keys` 为一个由表达式节点组成的序列。 `patterns` 为一个由对应的模式节点组成的序列。 `rest` 是一个可被指定用来捕获剩余映射元素的可选名称。 允许的关键字表达式被限制为与 match 语句文档中所描述的一致。

如果目标为一个映射、所有被求值的表达式都存在于该映射中，并且对应于每个键的值都与对应的子模式相匹配则此模式匹配成功。 如果 `rest` 不为 `None`，则当整个映射模式匹配成功时会将一个包含剩余映射元素的字典绑定到该名称。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... match x:
...     case {1: _, 2: _}:
...         ...
...     case {**rest}:
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchMapping(
                        keys=[
                            Constant(value=1),
                            Constant(value=2)],
                        patterns=[
                            MatchAs(),
                            MatchAs()]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))]),
                match_case(
                    pattern=MatchMapping(keys=[], patterns=[], rest='rest'),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])],
    type_ignores=[])
~~~

在 3.10 版本加入.

_class _ast.MatchClass( _cls_ , _patterns_ , _kwd_attrs_ , _kwd_patterns_ )¶

    

~~~
一个 match 类模式。 `cls` 为一个给出要匹配的名义类的表达式。 `patterns` 为一个由要与该类所定义的模式匹配属性相匹配的模式节点组成的序列。 `kwd_attrs` 为一个由要匹配的附加属性（指定为该类模式中的关键字参数）组成的序列，`kwd_patterns` 为对应的模式（指定为该类模式中的关键字值）。

如果目标为被指名类的一个实例、所有的位置模式都与对应的类定义属性相匹配，并且任何被指定的关键字属性都与其对应的模式相匹配则此模式匹配成功。

注意：类可能会定义一个返回自身的特征属性以便能将一个模式节点与被匹配的实例相匹配。 某些内置类型也是以这种方式来匹配的，与 match 语句文档中所描述的一致。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... match x:
...     case Point2D(0, 0):
...         ...
...     case Point3D(x=0, y=0, z=0):
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchClass(
                        cls=Name(id='Point2D', ctx=Load()),
                        patterns=[
                            MatchValue(
                                value=Constant(value=0)),
                            MatchValue(
                                value=Constant(value=0))],
                        kwd_attrs=[],
                        kwd_patterns=[]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))]),
                match_case(
                    pattern=MatchClass(
                        cls=Name(id='Point3D', ctx=Load()),
                        patterns=[],
                        kwd_attrs=[
                            'x',
                            'y',
                            'z'],
                        kwd_patterns=[
                            MatchValue(
                                value=Constant(value=0)),
                            MatchValue(
                                value=Constant(value=0)),
                            MatchValue(
                                value=Constant(value=0))]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])],
    type_ignores=[])
~~~

在 3.10 版本加入.

_class _ast.MatchAs( _pattern_ , _name_ )¶

    

~~~
一个匹配 "as-模式"、捕获模式或通配符模式。 `pattern` 包含将要与目标相匹配的匹配模式。 如果模式为 `None`，则该节点代表一个捕获模式（即一个简单的名称）并将总是会成功。

`name` 属性包含当模式匹配成功时将要绑定的名称。 如果 `name` 为 `None`，则 `pattern` 也必须为 `None` 并且该节点代表的是通配符模式。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... match x:
...     case [x] as y:
...         ...
...     case _:
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchAs(
                        pattern=MatchSequence(
                            patterns=[
                                MatchAs(name='x')]),
                        name='y'),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))]),
                match_case(
                    pattern=MatchAs(),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])],
    type_ignores=[])
~~~

在 3.10 版本加入.

_class _ast.MatchOr( _patterns_ )¶

    

~~~
一个匹配 "or-模式"。 or-模式会依次将其每个子模式与目标相匹配，直到有一个匹配成功。 此时该 or-模式将被视为匹配成功。 如果没有一个子模式匹配成功则该 or-模式匹配失败。 `patterns` 属性包含一个由将与目标相匹配的匹配模式节点组成的列表。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""
... match x:
...     case [x] | (y):
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchOr(
                        patterns=[
                            MatchSequence(
                                patterns=[
                                    MatchAs(name='x')]),
                            MatchAs(name='y')]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])],
    type_ignores=[])
~~~

在 3.10 版本加入.

### 类型形参¶

[类型形参](../reference/compound_stmts.md#type-params) 可以存在于类、函数和类型别名中。

_class _ast.TypeVar( _name_ , _bound_ )¶

    

~~~
一个 [`typing.TypeVar`](typing.md#typing.TypeVar "typing.TypeVar")。 `name` 是类型变量的名称。 `bound` 是可能存在的边界或约束。 如果 `bound` 是一个 `Tuple`，它代表约束；否则它代表边界。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("type Alias[T: int] = list[T]"), indent=4))
Module(
    body=[
        TypeAlias(
            name=Name(id='Alias', ctx=Store()),
            type_params=[
                TypeVar(
                    name='T',
                    bound=Name(id='int', ctx=Load()))],
            value=Subscript(
                value=Name(id='list', ctx=Load()),
                slice=Name(id='T', ctx=Load()),
                ctx=Load()))],
    type_ignores=[])
~~~

在 3.12 版本加入.

_class _ast.ParamSpec( _name_ )¶

    

~~~
一个 [`typing.ParamSpec`](typing.md#typing.ParamSpec "typing.ParamSpec")。 `name` 是形参规格说明的名称。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("type Alias[**P] = Callable[P, int]"), indent=4))
Module(
    body=[
        TypeAlias(
            name=Name(id='Alias', ctx=Store()),
            type_params=[
                ParamSpec(name='P')],
            value=Subscript(
                value=Name(id='Callable', ctx=Load()),
                slice=Tuple(
                    elts=[
                        Name(id='P', ctx=Load()),
                        Name(id='int', ctx=Load())],
                    ctx=Load()),
                ctx=Load()))],
    type_ignores=[])
~~~

在 3.12 版本加入.

_class _ast.TypeVarTuple( _name_ )¶

    

~~~
一个 [`typing.TypeVarTuple`](typing.md#typing.TypeVarTuple "typing.TypeVarTuple")。 `name` 是类型变量元组的名称。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("type Alias[*Ts] = tuple[*Ts]"), indent=4))
Module(
    body=[
        TypeAlias(
            name=Name(id='Alias', ctx=Store()),
            type_params=[
                TypeVarTuple(name='Ts')],
            value=Subscript(
                value=Name(id='tuple', ctx=Load()),
                slice=Tuple(
                    elts=[
                        Starred(
                            value=Name(id='Ts', ctx=Load()),
                            ctx=Load())],
                    ctx=Load()),
                ctx=Load()))],
    type_ignores=[])
~~~

在 3.12 版本加入.

### 函数与类定义¶

_class _ast.FunctionDef( _name_ , _args_ , _body_ , _decorator_list_ , _returns_ , _type_comment_ , _type_params_ )¶

    

~~~
一个函数定义。

  * `name` 是函数名称的原始字符串。

  * `args` 是一个 `arguments` 节点。

  * `body` 是函数内部的节点列表。

  * `decorator_list` 是要应用的装饰器列表，最外层的最先保存（即列表中的第一项将最后被应用）。

  * `returns` 是返回标注。

  * `type_params` 是一个 类型形参 的列表。

type_comment¶
~~~
    

~~~
`type_comment` 是带有以注释表示的类型标注的可选的字符串。

在 3.12 版本发生变更: 增加了 `type_params`。

_class _ast.Lambda( _args_ , _body_ )¶
~~~
    

~~~
`lambda` 是可在表达式内部使用的最小化函数定义。 不同于 `FunctionDef`，`body` 是保存一个单独节点。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('lambda x,y: ...'), indent=4))
Module(
    body=[
        Expr(
            value=Lambda(
                args=arguments(
                    posonlyargs=[],
                    args=[
                        arg(arg='x'),
                        arg(arg='y')],
                    kwonlyargs=[],
                    kw_defaults=[],
                    defaults=[]),
                body=Constant(value=Ellipsis)))],
    type_ignores=[])
~~~

_class _ast.arguments( _posonlyargs_ , _args_ , _vararg_ , _kwonlyargs_ , _kw_defaults_ , _kwarg_ , _defaults_ )¶

    

~~~
函数的参数。

  * `posonlyargs`, `args` 和 `kwonlyargs` 均为 `arg` 节点的列表。

  * `vararg` 和 `kwarg` 均为单独的 `arg` 节点，指向 `*args, **kwargs` 形参。

  * `kw_defaults` 是一个由仅限关键字参数默认值组成的列表。 如果有一个为 `None`，则对应的参数为必须的参数。

  * `defaults` 是一个由可按位置传入的参数的默认值组成的列表。 如果默认值个数少于参数个数，则它们将对应最后 n 个参数。

_class _ast.arg( _arg_ , _annotation_ , _type_comment_ )¶
~~~
    

~~~
列表中的一个单独参数。 `arg` 为参数名称原始字符串，`annotation` 为其标注，如一个 `Str` 或 `Name` 节点。

type_comment¶
~~~
    

~~~
`type_comment` 是一个可选的将注释用作类型标注的字符串。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""\
... @decorator1
... @decorator2
... def f(a: 'annotation', b=1, c=2, *d, e, f=3, **g) -> 'return annotation':
...     pass
... """), indent=4))
Module(
    body=[
        FunctionDef(
            name='f',
            args=arguments(
                posonlyargs=[],
                args=[
                    arg(
                        arg='a',
                        annotation=Constant(value='annotation')),
                    arg(arg='b'),
                    arg(arg='c')],
                vararg=arg(arg='d'),
                kwonlyargs=[
                    arg(arg='e'),
                    arg(arg='f')],
                kw_defaults=[
                    None,
                    Constant(value=3)],
                kwarg=arg(arg='g'),
                defaults=[
                    Constant(value=1),
                    Constant(value=2)]),
            body=[
                Pass()],
            decorator_list=[
                Name(id='decorator1', ctx=Load()),
                Name(id='decorator2', ctx=Load())],
            returns=Constant(value='return annotation'),
            type_params=[])],
    type_ignores=[])
~~~

_class _ast.Return( _value_ )¶

    

~~~
一条 `return` 语句。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('return 4'), indent=4))
Module(
    body=[
        Return(
            value=Constant(value=4))],
    type_ignores=[])
~~~

_class _ast.Yield( _value_ )¶

_class _ast.YieldFrom( _value_ )¶

    

~~~
一个 `yield` 或 `yield from` 表达式。 因为这些属于表达式，所以如果发回的值未被使用则必须将它们包装在 `Expr` 节点中。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('yield x'), indent=4))
Module(
    body=[
        Expr(
            value=Yield(
                value=Name(id='x', ctx=Load())))],
    type_ignores=[])

>>> print(ast.dump(ast.parse('yield from x'), indent=4))
Module(
    body=[
        Expr(
            value=YieldFrom(
                value=Name(id='x', ctx=Load())))],
    type_ignores=[])
~~~

_class _ast.Global( _names_ )¶

_class _ast.Nonlocal( _names_ )¶

    

~~~
`global` 和 `nonlocal` 语句。 `names` 为一个由原始字符串组成的列表。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse('global x,y,z'), indent=4))
Module(
    body=[
        Global(
            names=[
                'x',
                'y',
                'z'])],
    type_ignores=[])

>>> print(ast.dump(ast.parse('nonlocal x,y,z'), indent=4))
Module(
    body=[
        Nonlocal(
            names=[
                'x',
                'y',
                'z'])],
    type_ignores=[])
~~~

_class _ast.ClassDef( _name_ , _bases_ , _keywords_ , _body_ , _decorator_list_ , _type_params_ )¶

    

~~~
一个类定义。

  * `name` 为类名称的原始字符串。

  * `bases` 为一个由显式指明的基类节点组成的列表。

  * `keywords` 是一个 `keyword` 节点的列表，主要用于‘元类’。 其他关键字将被传给相应的元类，参见 [PEP-3115](https://peps.python.org/pep-3115/)。

  * `body` 是一个由代表类定义内部代码的节点组成的列表。

  * `decorator_list` 是一个节点的列表，与 `FunctionDef` 中的一致。

  * `type_params` 是一个 类型形参 的列表。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""\
... @decorator1
... @decorator2
... class Foo(base1, base2, metaclass=meta):
...     pass
... """), indent=4))
Module(
    body=[
        ClassDef(
            name='Foo',
            bases=[
                Name(id='base1', ctx=Load()),
                Name(id='base2', ctx=Load())],
            keywords=[
                keyword(
                    arg='metaclass',
                    value=Name(id='meta', ctx=Load()))],
            body=[
                Pass()],
            decorator_list=[
                Name(id='decorator1', ctx=Load()),
                Name(id='decorator2', ctx=Load())],
            type_params=[])],
    type_ignores=[])
~~~

在 3.12 版本发生变更: 增加了 `type_params`。

### async 与 await¶

_class _ast.AsyncFunctionDef( _name_ , _args_ , _body_ , _decorator_list_ , _returns_ , _type_comment_ , _type_params_ )¶

    

~~~
一个 `async def` 函数定义。 具有与 `FunctionDef` 相同的字段。

在 3.12 版本发生变更: 增加了 `type_params`。

_class _ast.Await( _value_ )¶
~~~
    

~~~
一个 `await` 表达式。 `value` 是它所等待的值。 仅在 `AsyncFunctionDef` 的函数体内可用。
~~~
    
    
~~~shell
>>> print(ast.dump(ast.parse("""\
... async def f():
...     await other_func()
... """), indent=4))
Module(
    body=[
        AsyncFunctionDef(
            name='f',
            args=arguments(
                posonlyargs=[],
                args=[],
                kwonlyargs=[],
                kw_defaults=[],
                defaults=[]),
            body=[
                Expr(
                    value=Await(
                        value=Call(
                            func=Name(id='other_func', ctx=Load()),
                            args=[],
                            keywords=[])))],
            decorator_list=[],
            type_params=[])],
    type_ignores=[])
~~~

_class _ast.AsyncFor( _target_ , _iter_ , _body_ , _orelse_ , _type_comment_ )¶

_class _ast.AsyncWith( _items_ , _body_ , _type_comment_ )¶

    

~~~
`async for` 循环和 `async with` 上下文管理器。 它们分别具有与 `For` 和 `With` 相同的字段。 仅在 `AsyncFunctionDef` 的函数体内可用。

备注

当一个字符串由 `ast.parse()` 来解析时，所返回的树中的运算符节点 (为 `ast.operator`, `ast.unaryop`, `ast.cmpop`, `ast.boolop` 和 `ast.expr_context` 的子类) 将均为单例对象。 对其中某一个 (例如 `ast.Add`) 的修改将反映到同一个值所出现的其他位置上。

## `ast` 中的辅助函数¶

除了节点类， `ast` 模块里为遍历抽象语法树定义了这些工具函数和类:

ast.parse( _source_ , _filename ='<unknown>'_, _mode ='exec'_, _*_ , _type_comments =False_, _feature_version =None_, _optimize =-1_)¶
~~~
    

~~~
Parse the source into an AST node. Equivalent to `compile(source, filename, mode, flags=FLAGS_VALUE, optimize=optimize)`, where `FLAGS_VALUE` is `ast.PyCF_ONLY_AST` if `optimize <= 0` and `ast.PyCF_OPTIMIZED_AST` otherwise.

如果给出 `type_comments=True`，解析器会被修改以检查并返回 [**PEP 484**](https://peps.python.org/pep-0484/) 和 [**PEP 526**](https://peps.python.org/pep-0526/) 所描述的类型注释。 这相当于将 `ast.PyCF_TYPE_COMMENTS` 添加到传给 [`compile()`](functions.md#compile "compile") 的旗标中。 这将报告针对未在正确放置类型注释的语法错误。 没有这个旗标，类型注释将被忽略，而指定 AST 节点上的 `type_comment` 字段将总是为 `None`。 此外，`# type: ignore` 注释的位置将作为 `Module` 的 `type_ignores` 属性被返回（在其他情况下则总是为空列表）。

并且，如果 `mode` 为 `'func_type'`，则输入语法会进行与 [**PEP 484**](https://peps.python.org/pep-0484/) "签名类型注释" 对应的修改，例如 `(str, int) -> List[str]`。

Also, setting `feature_version` to a tuple `(major, minor)` will attempt to parse using that Python version's grammar. Currently `major` must equal to `3`. For example, setting `feature_version=(3, 4)` will allow the use of `async` and `await` as variable names. The lowest supported version is `(3, 7)`; the highest is `sys.version_info[0:2]`.

如果源包含一个空字符 ('0')，则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

警告

请注意成功将源代码解析为 AST 对象并不能保证该源代码提供了可被执行的有效 Python 代码，因为编译步骤还可能引发其他的 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError") 异常。 例如，对于源代码 `return 42` 可以生成一条有效的 return 语句 AST 节点，但它不能被单独编译（它必须位于一个函数节点之内）。

特别地，`ast.parse()` 不会执行任何作用域检查，这是由编译步骤来做的。

警告

足够复杂或是巨大的字符串可能导致Python解释器的崩溃，因为Python的AST编译器是有栈深限制的。

在 3.8 版本发生变更: 增加了 `type_comments`, `mode='func_type'` 和 `feature_version`。

在 3.13 版本发生变更: The minimum supported version for feature_version is now (3,7) The `optimize` argument was added.

ast.unparse( _ast_obj_ )¶
~~~
    

~~~
反向解析一个 `ast.AST` 对象并生成一个包含当再次使用 `ast.parse()` 解析时将产生同样的 `ast.AST` 对象的代码的字符串。

警告

所产生的代码字符串将不一定与生成 `ast.AST` 对象的原始代码完全一致（不带任何编译器优化，例如常量元组/冻结集合等）。

警告

尝试反向解析一个高度复杂的表达式可能会导致 [`RecursionError`](exceptions.md#RecursionError "RecursionError")。

在 3.9 版本加入.

ast.literal_eval( _node_or_string_ )¶
~~~
    

~~~
对表达式节点或仅包含 Python 字面值或容器表示形式的字符串进行求值。 所提供的字符串或节点可能只包含下列 Python 字面值结构：字符串、字节串、数字、元组、字典、集合、布尔值、`None` 和 `Ellipsis`。

这可被用于对包含 Python 值的字符串进行求值而不必解析这些值本身。 它不能对任意的复杂表达式进行求值，例如涉及运算符或索引操作的表达式。

此函数过去曾被描述为“安全”但并未定义其含义。 这是存在误导性的。 此函数被专门设计为不执行 Python 代码，这与更通用的 [`eval()`](functions.md#eval "eval") 不同。 它没有命名空间、没有名称查找或执行外部调用的能力。 但是它并不能完全避免攻击：一个相对较小的输入有可能导致内存耗尽或 C 栈耗尽，使得进程崩溃。 还可能在某些输入上出现过度消耗 CPU 的拒绝服务问题。 因此不建议在未被信任的数据上调用它。

警告

有可能由于 Python 的 AST 编译器中的栈深度限制而导致 Python 解释器的崩溃。

根据输入错误的具体形式它可能引发 [`ValueError`](exceptions.md#ValueError "ValueError"), [`TypeError`](exceptions.md#TypeError "TypeError"), [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError"), [`MemoryError`](exceptions.md#MemoryError "MemoryError") 以及 [`RecursionError`](exceptions.md#RecursionError "RecursionError")。

在 3.2 版本发生变更: 目前支持字节和集合。

在 3.9 版本发生变更: 现在支持通过 `'set()'` 创建空集合。

在 3.10 版本发生变更: 对于字符串输入，打头的空格和制表符现在会被去除。

ast.get_docstring( _node_ , _clean =True_)¶
~~~
    

~~~
返回给定 _node_ (必须为 `FunctionDef`, `AsyncFunctionDef`, `ClassDef` 或 `Module` 节点) 的文档字符串，或者如果没有文档字符串则返回 `None`。 如果 _clean_ 为真值，则通过 [`inspect.cleandoc()`](inspect.md#inspect.cleandoc "inspect.cleandoc") 清除文档字符串的缩进。

在 3.5 版本发生变更: 目前支持 `AsyncFunctionDef`

ast.get_source_segment( _source_ , _node_ , _*_ , _padded =False_)¶
~~~
    

~~~
获取生成 _node_ 的 _source_ 的源代码段。 如果丢失了某些位置信息 (`lineno`, `end_lineno`, `col_offset` 或 `end_col_offset`)，则返回 `None`。

如果 _padded_ 为 `True`，则多行语句的第一行将以与其初始位置相匹配的空格填充。

在 3.8 版本加入.

ast.fix_missing_locations( _node_ )¶
~~~
    

~~~
当你通过 [`compile()`](functions.md#compile "compile") 来编译节点树时，编译器会准备接受每个支持 `lineno` 和 `col_offset` 属性的节点的相应信息。 对已生成节点来说这是相当繁琐的，因此这个辅助工具会递归地为尚未设置这些属性的节点添加它们，具体做法是将其设为父节点的对应值。 它将从 _node_ 开始递归地执行。

ast.increment_lineno( _node_ , _n =1_)¶
~~~
    

~~~
从 _node_ 开始按 _n_ 递增节点树中每个节点的行号和结束行号。 这在“移动代码”到文件中的不同位置时很有用处。

ast.copy_location( _new_node_ , _old_node_ )¶
~~~
    

~~~
在可能的情况下将源位置 (`lineno`, `col_offset`, `end_lineno` 和 `end_col_offset`) 从 _old_node_ 拷贝到 _new_node_ ，并返回 _new_node_ 。

ast.iter_fields( _node_ )¶
~~~
    

~~~
针对于 _node_ 上在 `node._fields` 中出现的每个字段产生一个 `(fieldname, value)` 元组。

ast.iter_child_nodes( _node_ )¶
~~~
    

~~~
产生 _node_ 所有的直接子节点，也就是说，所有为节点的字段所有为节点列表的字段条目。

ast.walk( _node_ )¶
~~~
    

~~~
递归地产生节点树中从 _node_ 开始（包括 _node_ 本身）的所有下级节点，没有确定的排序方式。 这在你仅想要原地修改节点而不关心具体上下文时很有用处。

_class _ast.NodeVisitor¶
~~~
    

~~~
一个遍历抽象语法树并针对所找到的每个节点调用访问器函数的节点访问器基类。 该函数可能会返回一个由 `visit()` 方法所提供的值。

这个类应当被子类化，并由子类来添加访问器方法。

visit( _node_ )¶
~~~
    

~~~
访问一个节点。 默认实现会调用名为 `self.visit_ _classname_` 的方法其中 _classname_ 为节点类的名称，或者如果该方法不存在则为 `generic_visit()`。

generic_visit( _node_ )¶
~~~
    

~~~
该访问器会在节点的所有子节点上调用 `visit()`。

请注意所有包含自定义访问器方法的节点的子节点将不会被访问除非访问器调用了 `generic_visit()` 或是自行访问它们。

如果你想在遍历期间应用对节点的修改则请不要使用 `NodeVisitor`。 对此目的可使用一个允许修改的特殊访问器 (`NodeTransformer`)。

自 3.8 版本弃用: `visit_Num()`, `visit_Str()`, `visit_Bytes()`, `visit_NameConstant()` 和 `visit_Ellipsis()` 等方法现在已被弃用且在未来的 Python 版本中将不会再被调用。 请添加 `visit_Constant()` 方法来处理所有常量节点。

_class _ast.NodeTransformer¶
~~~
    

~~~
子类 `NodeVisitor` 用于遍历抽象语法树，并允许修改节点。

`NodeTransformer` 将遍历抽象语法树并使用visitor方法的返回值去替换或移除旧节点。如果visitor方法的返回值为 `None` , 则该节点将从其位置移除，否则将替换为返回值。当返回值是原始节点时，无需替换。

如下是一个转换器示例，它将所有出现的名称 (`foo`) 重写为 `data['foo']`:
~~~
    
    
~~~
class RewriteName(NodeTransformer):

    def visit_Name(self, node):
        return Subscript(
            value=Name(id='data', ctx=Load()),
            slice=Constant(value=node.id),
            ctx=node.ctx
        )
~~~

请记住，如果您正在操作的节点具有子节点，则必须先转换其子节点或为该节点调用 `generic_visit()` 方法。

对于属于语句集合（适用于所有语句节点）的节点，访问者还可以返回节点列表而不仅仅是单个节点。

如果 `NodeTransformer` 引入了新的（不属于原节点树一部分的）节点而没有给出它们的位置信息（如 `lineno` 等），则应当调用 `fix_missing_locations()` 并传入新的子节点树来重新计算位置信息:

    
    
~~~
tree = ast.parse('foo', mode='eval')
new_tree = fix_missing_locations(RewriteName().visit(tree))
~~~

通常你可以像这样使用转换器:

    
    
~~~
node = YourTransformer().visit(node)
~~~

ast.dump( _node_ , _annotate_fields =True_, _include_attributes =False_, _*_ , _indent =None_)¶

    

~~~
返回 _node_ 中树结构的格式化转储。 这主要适用于调试目的。 如果 _annotate_fields_ 为真值（默认），返回的字符串将显示字段的名称和值。 如果 _annotate_fields_ 为假值，结果字符串将通过省略无歧义的字段名称变得更为紧凑。 默认情况下不会转储行号和列偏移等属性。 如果需要，可将 _include_attributes_ 设为真值。

如果 _indent_ 是一个非负整数或者字符串，那么节点树将被美化输出为指定的缩进级别。 如果缩进级别为 0、负数或者 `""` 则将只插入换行符。 `None` (默认) 将选择最紧凑的表示形式。 使用一个正整数将让每个级别缩进相应数量的空格。 如果 _indent_ 是一个字符串 (如 `"\t"`)，该字符串会被用于缩进每个级别。

在 3.9 版本发生变更: 添加了 _indent_ 选项。

## 编译器旗标¶

下列旗标可被传给 [`compile()`](functions.md#compile "compile") 用来改变程序编译的效果:

ast.PyCF_ALLOW_TOP_LEVEL_AWAIT¶
~~~
    

~~~
启用对最高层级 `await`, `async for`, `async with` 以及异步推导式的支持。

在 3.8 版本加入.

ast.PyCF_ONLY_AST¶
~~~
    

~~~
生成并返回一个抽象语法树而不是返回一个已编译的代码对象。

ast.PyCF_TYPE_COMMENTS¶
~~~
    

~~~
启用对 [**PEP 484**](https://peps.python.org/pep-0484/) 和 [**PEP 526**](https://peps.python.org/pep-0526/) 风格的类型注释的支持 (`# type: <type>`, `# type: ignore <stuff>`)。

在 3.8 版本加入.

## 命令行用法¶

在 3.9 版本加入.

`ast` 模块可以在命令行下作为脚本来执行。 具体做法非常简单:
~~~
    
    
~~~
python -m ast [-m <mode>] [-a] [infile]
~~~

可以接受以下选项：

-h, \--help¶
    

显示帮助信息并退出。

-m <mode>¶
\--mode <mode>¶

    

~~~
指明哪种代码必须被编译，相当于 `parse()` 中的 _mode_ 参数。

\--no-type-comments¶
~~~
    

~~~
不要解析类型注释。

-a, \--include-attributes¶
~~~

包括属性如行号和列偏移。

-i <indent>¶
\--indent <indent>¶

    

~~~
