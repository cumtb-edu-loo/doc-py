# 字典对象¶

type PyDictObject¶  

    

~~~
这个 [`PyObject`](structures.md#c.PyObject "PyObject") 的子类型代表一个Python字典对象。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyDict_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Python字典类型表示为 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的实例。这与Python层面的 [`dict`](../library/stdtypes.md#dict "dict") 是相同的对象。

int PyDict_Check([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果 _p_ 是一个 dict 对象或者 dict 类型的子类型的实例则返回真值。 此函数总是会成功执行。

int PyDict_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果 _p_ 是一个 dict 对象但不是 dict 类型的子类型的实例则返回真值。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PyDict_New()¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回一个新的空字典，失败时返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyDictProxy_New([PyObject](structures.md#c.PyObject "PyObject") *mapping)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 [`types.MappingProxyType`](../library/types.md#types.MappingProxyType "types.MappingProxyType") 对象，用于强制执行只读行为的映射。这通常用于创建视图以防止修改非动态类类型的字典。

void PyDict_Clear([PyObject](structures.md#c.PyObject "PyObject") *p)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

清空现有字典的所有键值对。

int PyDict_Contains([PyObject](structures.md#c.PyObject "PyObject") *p, [PyObject](structures.md#c.PyObject "PyObject") *key)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

确定 _key_ 是否包含在字典 _p_ 中。如果 _key_ 匹配上 _p_ 的某一项，则返回 `1` ，否则返回 `0` 。返回 `-1` 表示出错。这等同于Python表达式 `key in p` 。

int PyDict_ContainsString([PyObject](structures.md#c.PyObject "PyObject") *p, const char *key)¶  
~~~
    

~~~
This is the same as `PyDict_Contains()`, but _key_ is specified as a const char* UTF-8 encoded bytes string, rather than a [PyObject](structures.md#c.PyObject "PyObject")*.

在 3.13 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyDict_Copy([PyObject](structures.md#c.PyObject "PyObject") *p)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回与 _p_ 包含相同键值对的新字典。

int PyDict_SetItem([PyObject](structures.md#c.PyObject "PyObject") *p, [PyObject](structures.md#c.PyObject "PyObject") *key, [PyObject](structures.md#c.PyObject "PyObject") *val)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

使用 _key_ 作为键将 _val_ 插入字典 _p_ 。 _key_ 必须为 [hashable](../glossary.md#term-hashable)；如果不是，则将引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError")。 成功时返回 `0`，失败时返回 `-1`。 此函数 _不会_ 附带对 _val_ 的引用。

int PyDict_SetItemString([PyObject](structures.md#c.PyObject "PyObject") *p, const char *key, [PyObject](structures.md#c.PyObject "PyObject") *val)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这与 `PyDict_SetItem()` 相同，但 _key_ 被指定为 const char* UTF-8 编码的字节串，而不是 [PyObject](structures.md#c.PyObject "PyObject")*。

int PyDict_DelItem([PyObject](structures.md#c.PyObject "PyObject") *p, [PyObject](structures.md#c.PyObject "PyObject") *key)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

移除字典 _p_ 中键为 _key_ 的条目。 _key_ 必须是 [hashable](../glossary.md#term-hashable)；如果不是，则会引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError")。 如果字典中没有 _key_ ，则会引发 [`KeyError`](../library/exceptions.md#KeyError "KeyError")。 成功时返回 `0` 或者失败时返回 `-1`。

int PyDict_DelItemString([PyObject](structures.md#c.PyObject "PyObject") *p, const char *key)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这与 `PyDict_DelItem()` 相同，但 _key_ 被指定为 const char* UTF-8 编码的字节串，而不是 [PyObject](structures.md#c.PyObject "PyObject")*。

int PyDict_GetItemRef([PyObject](structures.md#c.PyObject "PyObject") *p, [PyObject](structures.md#c.PyObject "PyObject") *key, [PyObject](structures.md#c.PyObject "PyObject") **result)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.13._

Return a new [strong reference](../glossary.md#term-strong-reference) to the object from dictionary _p_ which has a key _key_ :

  * If the key is present, set _*result_ to a new [strong reference](../glossary.md#term-strong-reference) to the value and return `1`.

  * If the key is missing, set _*result_ to `NULL` and return `0`.

  * On error, raise an exception and return `-1`.

在 3.13 版本加入.

See also the [`PyObject_GetItem()`](object.md#c.PyObject_GetItem "PyObject_GetItem") function.

[PyObject](structures.md#c.PyObject "PyObject") *PyDict_GetItem([PyObject](structures.md#c.PyObject "PyObject") *p, [PyObject](structures.md#c.PyObject "PyObject") *key)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Return a [borrowed reference](../glossary.md#term-borrowed-reference) to the object from dictionary _p_ which has a key _key_. Return `NULL` if the key _key_ is missing _without_ setting an exception.

备注

在调用 [`__hash__()`](../reference/datamodel.md#object.__hash__ "object.__hash__") 和 [`__eq__()`](../reference/datamodel.md#object.__eq__ "object.__eq__") 方法时发生的异常将被静默地忽略。 建议改用 `PyDict_GetItemWithError()` 函数。

在 3.10 版本发生变更: 在不保持 [GIL](../glossary.md#term-GIL) 的情况下调用此 API 曾因历史原因而被允许。 现在已不再被允许。

[PyObject](structures.md#c.PyObject "PyObject") *PyDict_GetItemWithError([PyObject](structures.md#c.PyObject "PyObject") *p, [PyObject](structures.md#c.PyObject "PyObject") *key)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

`PyDict_GetItem()` 的变种，它不会屏蔽异常。 当异常发生时将返回 `NULL` **并且** 设置一个异常。 如果键不存在则返回 `NULL` **并且不会** 设置一个异常。

[PyObject](structures.md#c.PyObject "PyObject") *PyDict_GetItemString([PyObject](structures.md#c.PyObject "PyObject") *p, const char *key)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

这与 `PyDict_GetItem()` 一样，但 _key_ 是由一个 const char* UTF-8 编码的字节串来指定的，而不是 [PyObject](structures.md#c.PyObject "PyObject")*。

备注

在调用 [`__hash__()`](../reference/datamodel.md#object.__hash__ "object.__hash__") 和 [`__eq__()`](../reference/datamodel.md#object.__eq__ "object.__eq__") 方法时或者在创建临时 [`str`](../library/stdtypes.md#str "str") 对象期间发生的异常将被静默地忽略。 建议改用 `PyDict_GetItemWithError()` 函数并附带你自己的 [`PyUnicode_FromString()`](unicode.md#c.PyUnicode_FromString "PyUnicode_FromString") _key_ 。

int PyDict_GetItemStringRef([PyObject](structures.md#c.PyObject "PyObject") *p, const char *key, [PyObject](structures.md#c.PyObject "PyObject") **result)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.13._

Similar than `PyDict_GetItemRef()`, but _key_ is specified as a const char* UTF-8 encoded bytes string, rather than a [PyObject](structures.md#c.PyObject "PyObject")*.

在 3.13 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyDict_SetDefault([PyObject](structures.md#c.PyObject "PyObject") *p, [PyObject](structures.md#c.PyObject "PyObject") *key, [PyObject](structures.md#c.PyObject "PyObject") *defaultobj)¶  

    _返回值：借入的引用。_

这跟Python层面的 [`dict.setdefault()`](../library/stdtypes.md#dict.setdefault "dict.setdefault") 一样。如果键 _key_ 存在，它返回在字典 _p_ 里面对应的值。如果键不存在，它会和值 _defaultobj_ 一起插入并返回 _defaultobj_ 。这个函数只计算 _key_ 的哈希函数一次，而不是在查找和插入时分别计算它。

在 3.4 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyDict_Items([PyObject](structures.md#c.PyObject "PyObject") *p)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回一个包含字典中所有键值项的 [`PyListObject`](list.md#c.PyListObject "PyListObject")。

[PyObject](structures.md#c.PyObject "PyObject") *PyDict_Keys([PyObject](structures.md#c.PyObject "PyObject") *p)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回一个包含字典中所有键(keys)的 [`PyListObject`](list.md#c.PyListObject "PyListObject")。

[PyObject](structures.md#c.PyObject "PyObject") *PyDict_Values([PyObject](structures.md#c.PyObject "PyObject") *p)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回一个包含字典中所有值(values)的 [`PyListObject`](list.md#c.PyListObject "PyListObject")。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyDict_Size([PyObject](structures.md#c.PyObject "PyObject") *p)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回字典中项目数，等价于对字典 _p_ 使用 `len(p)`。

int PyDict_Next([PyObject](structures.md#c.PyObject "PyObject") *p, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *ppos, [PyObject](structures.md#c.PyObject "PyObject") **pkey, [PyObject](structures.md#c.PyObject "PyObject") **pvalue)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

迭代字典 _p_ 中的所有键值对。 在第一次调用此函数开始迭代之前，由 _ppos_ 所引用的 [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 必须被初始化为 `0`；该函数将为字典中的每个键值对返回真值，一旦所有键值对都报告完毕则返回假值。 形参 _pkey_ 和 _pvalue_ 应当指向 [PyObject](structures.md#c.PyObject "PyObject")* 变量，它们将分别使用每个键和值来填充，或者也可以为 `NULL`。 通过它们返回的任何引用都是暂借的。 _ppos_ 在迭代期间不应被更改。 它的值表示内部字典结构中的偏移量，并且由于结构是稀疏的，因此偏移量并不连续。

例如：
~~~
    
    
~~~
PyObject *key, *value;
Py_ssize_t pos = 0;

while (PyDict_Next(self->dict, &pos, &key, &value)) {
    /* do something interesting with the values... */
    ...
}
~~~

字典 _p_ 不应该在遍历期间发生改变。在遍历字典时，改变键中的值是安全的，但仅限于键的集合不发生改变。例如:

    
    
~~~
PyObject *key, *value;
Py_ssize_t pos = 0;

while (PyDict_Next(self->dict, &pos, &key, &value)) {
    long i = PyLong_AsLong(value);
    if (i == -1 && PyErr_Occurred()) {
        return -1;
    }
    PyObject *o = PyLong_FromLong(i + 1);
    if (o == NULL)
        return -1;
    if (PyDict_SetItem(self->dict, key, o) < 0) {
        Py_DECREF(o);
        return -1;
    }
    Py_DECREF(o);
}
~~~

int PyDict_Merge([PyObject](structures.md#c.PyObject "PyObject") *a, [PyObject](structures.md#c.PyObject "PyObject") *b, int override)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

对映射对象 _b_ 进行迭代，将键值对添加到字典 _a_ 。 _b_ 可以是一个字典，或任何支持 [`PyMapping_Keys()`](mapping.md#c.PyMapping_Keys "PyMapping_Keys") 和 [`PyObject_GetItem()`](object.md#c.PyObject_GetItem "PyObject_GetItem") 的对象。 如果 _override_ 为真值，则如果在 _b_ 中找到相同的键则 _a_ 中已存在的相应键值对将被替换，否则如果在 _a_ 中没有相同的键则只是添加键值对。 当成功时返回 `0` 或者当引发异常时返回 `-1`。

int PyDict_Update([PyObject](structures.md#c.PyObject "PyObject") *a, [PyObject](structures.md#c.PyObject "PyObject") *b)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这与 C 中的 `PyDict_Merge(a, b, 1)` 一样，也类似于 Python 中的 `a.update(b)`，差别在于 `PyDict_Update()` 在第二个参数没有 "keys" 属性时不会回退到迭代键值对的序列。 当成功时返回 `0` 或者当引发异常时返回 `-1`。

int PyDict_MergeFromSeq2([PyObject](structures.md#c.PyObject "PyObject") *a, [PyObject](structures.md#c.PyObject "PyObject") *seq2, int override)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

将 _seq2_ 中的键值对更新或合并到字典 _a_ 。 _seq2_ 必须为产生长度为 2 的用作键值对的元素的可迭代对象。 当存在重复的键时，如果 _override_ 真值则最后出现的键胜出。 当成功时返回 `0` 或者当引发异常时返回 `-1`。 等价的 Python 代码（返回值除外）:

    
    
~~~python
def PyDict_MergeFromSeq2(a, seq2, override):
    for key, value in seq2:
        if override or key not in a:
            a[key] = value
~~~

int PyDict_AddWatcher(PyDict_WatchCallback callback)¶  

    

~~~
在字典上注册 _callback_ 来作为 watcher。返回值为非负数的整数 id，作为将来调用 `PyDict_Watch()` 的时候使用。如果出现错误（比如没有足够的可用 watcher ID），返回 `-1` 并且设置异常。

在 3.12 版本加入.

int PyDict_ClearWatcher(int watcher_id)¶  
~~~
    

~~~
清空由之前从 `PyDict_AddWatcher()` 返回的 _watcher_id_ 所标识的 watcher。 成功时返回 `0`，出错时（例如当给定的 _watcher_id_ 未被注册）返回 `-1`。

在 3.12 版本加入.

int PyDict_Watch(int watcher_id, [PyObject](structures.md#c.PyObject "PyObject") *dict)¶  
~~~
    

~~~
将字典 _dict_ 标记为已被监视。 由 `PyDict_AddWatcher()` 授权 _watcher_id_ 对应的回调将在 _dict_ 被修改或释放时被调用。 成功时返回 `0`，出错时返回 `-1`。

在 3.12 版本加入.

int PyDict_Unwatch(int watcher_id, [PyObject](structures.md#c.PyObject "PyObject") *dict)¶  
~~~
    

~~~
将字典 _dict_ 标记为不再被监视。 由 `PyDict_AddWatcher()` 授权 _watcher_id_ 对应的回调在 _dict_ 被修改或释放时将不再被调用。 该字典在此之前必须已被此监视器所监视。 成功时返回 `0`，出错时返回 `-1`。

在 3.12 版本加入.

type PyDict_WatchEvent¶  
~~~
    

~~~
由以下可能的字典监视器事件组成的枚举: `PyDict_EVENT_ADDED`, `PyDict_EVENT_MODIFIED`, `PyDict_EVENT_DELETED`, `PyDict_EVENT_CLONED`, `PyDict_EVENT_CLEARED` 或 `PyDict_EVENT_DEALLOCATED`。

在 3.12 版本加入.

typedef int (*PyDict_WatchCallback)(PyDict_WatchEvent event, [PyObject](structures.md#c.PyObject "PyObject") *dict, [PyObject](structures.md#c.PyObject "PyObject") *key, [PyObject](structures.md#c.PyObject "PyObject") *new_value)¶  
~~~
    

~~~
