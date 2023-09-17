# 函数式编程模块¶

本章里描述的模块提供了函数和类，以支持函数式编程风格和在可调用对象上的通用操作。

本章包含以下模块的文档：

  * [`itertools` \--- 为高效循环而创建迭代器的函数](itertools.md)
    * [Itertool函数](itertools.md#itertool-functions)
      * [`accumulate()`](itertools.md#itertools.accumulate)
      * [`batched()`](itertools.md#itertools.batched)
      * [`chain()`](itertools.md#itertools.chain)
        * [`chain.from_iterable()`](itertools.md#itertools.chain.from_iterable)
      * [`combinations()`](itertools.md#itertools.combinations)
      * [`combinations_with_replacement()`](itertools.md#itertools.combinations_with_replacement)
      * [`compress()`](itertools.md#itertools.compress)
      * [`count()`](itertools.md#itertools.count)
      * [`cycle()`](itertools.md#itertools.cycle)
      * [`dropwhile()`](itertools.md#itertools.dropwhile)
      * [`filterfalse()`](itertools.md#itertools.filterfalse)
      * [`groupby()`](itertools.md#itertools.groupby)
      * [`islice()`](itertools.md#itertools.islice)
      * [`pairwise()`](itertools.md#itertools.pairwise)
      * [`permutations()`](itertools.md#itertools.permutations)
      * [`product()`](itertools.md#itertools.product)
      * [`repeat()`](itertools.md#itertools.repeat)
      * [`starmap()`](itertools.md#itertools.starmap)
      * [`takewhile()`](itertools.md#itertools.takewhile)
      * [`tee()`](itertools.md#itertools.tee)
      * [`zip_longest()`](itertools.md#itertools.zip_longest)
    * [itertools 配方](itertools.md#itertools-recipes)
  * [`functools` \--- 高阶函数和可调用对象上的操作](functools.md)
    * [`cache()`](functools.md#functools.cache)
    * [`cached_property()`](functools.md#functools.cached_property)
    * [`cmp_to_key()`](functools.md#functools.cmp_to_key)
    * [`lru_cache()`](functools.md#functools.lru_cache)
    * [`total_ordering()`](functools.md#functools.total_ordering)
    * [`partial()`](functools.md#functools.partial)
    * [`partialmethod`](functools.md#functools.partialmethod)
    * [`reduce()`](functools.md#functools.reduce)
    * [`singledispatch()`](functools.md#functools.singledispatch)
    * [`singledispatchmethod`](functools.md#functools.singledispatchmethod)
    * [`update_wrapper()`](functools.md#functools.update_wrapper)
    * [`wraps()`](functools.md#functools.wraps)
    * [`partial` 对象](functools.md#partial-objects)
      * [`partial.func`](functools.md#functools.partial.func)
      * [`partial.args`](functools.md#functools.partial.args)
      * [`partial.keywords`](functools.md#functools.partial.keywords)
  * [`operator` \--- 标准运算符替代函数](operator.md)
    * [`lt()`](operator.md#operator.lt)
    * [`le()`](operator.md#operator.le)
    * [`eq()`](operator.md#operator.eq)
    * [`ne()`](operator.md#operator.ne)
    * [`ge()`](operator.md#operator.ge)
    * [`gt()`](operator.md#operator.gt)
    * [`__lt__()`](operator.md#operator.__lt__)
    * [`__le__()`](operator.md#operator.__le__)
    * [`__eq__()`](operator.md#operator.__eq__)
    * [`__ne__()`](operator.md#operator.__ne__)
    * [`__ge__()`](operator.md#operator.__ge__)
    * [`__gt__()`](operator.md#operator.__gt__)
    * [`not_()`](operator.md#operator.not_)
    * [`__not__()`](operator.md#operator.__not__)
    * [`truth()`](operator.md#operator.truth)
    * [`is_()`](operator.md#operator.is_)
    * [`is_not()`](operator.md#operator.is_not)
    * [`abs()`](operator.md#operator.abs)
    * [`__abs__()`](operator.md#operator.__abs__)
    * [`add()`](operator.md#operator.add)
    * [`__add__()`](operator.md#operator.__add__)
    * [`and_()`](operator.md#operator.and_)
    * [`__and__()`](operator.md#operator.__and__)
    * [`floordiv()`](operator.md#operator.floordiv)
    * [`__floordiv__()`](operator.md#operator.__floordiv__)
    * [`index()`](operator.md#operator.index)
    * [`__index__()`](operator.md#operator.__index__)
    * [`inv()`](operator.md#operator.inv)
    * [`invert()`](operator.md#operator.invert)
    * [`__inv__()`](operator.md#operator.__inv__)
    * [`__invert__()`](operator.md#operator.__invert__)
    * [`lshift()`](operator.md#operator.lshift)
    * [`__lshift__()`](operator.md#operator.__lshift__)
    * [`mod()`](operator.md#operator.mod)
    * [`__mod__()`](operator.md#operator.__mod__)
    * [`mul()`](operator.md#operator.mul)
    * [`__mul__()`](operator.md#operator.__mul__)
    * [`matmul()`](operator.md#operator.matmul)
    * [`__matmul__()`](operator.md#operator.__matmul__)
    * [`neg()`](operator.md#operator.neg)
    * [`__neg__()`](operator.md#operator.__neg__)
    * [`or_()`](operator.md#operator.or_)
    * [`__or__()`](operator.md#operator.__or__)
    * [`pos()`](operator.md#operator.pos)
    * [`__pos__()`](operator.md#operator.__pos__)
    * [`pow()`](operator.md#operator.pow)
    * [`__pow__()`](operator.md#operator.__pow__)
    * [`rshift()`](operator.md#operator.rshift)
    * [`__rshift__()`](operator.md#operator.__rshift__)
    * [`sub()`](operator.md#operator.sub)
    * [`__sub__()`](operator.md#operator.__sub__)
    * [`truediv()`](operator.md#operator.truediv)
    * [`__truediv__()`](operator.md#operator.__truediv__)
    * [`xor()`](operator.md#operator.xor)
    * [`__xor__()`](operator.md#operator.__xor__)
    * [`concat()`](operator.md#operator.concat)
    * [`__concat__()`](operator.md#operator.__concat__)
    * [`contains()`](operator.md#operator.contains)
    * [`__contains__()`](operator.md#operator.__contains__)
    * [`countOf()`](operator.md#operator.countOf)
    * [`delitem()`](operator.md#operator.delitem)
    * [`__delitem__()`](operator.md#operator.__delitem__)
    * [`getitem()`](operator.md#operator.getitem)
    * [`__getitem__()`](operator.md#operator.__getitem__)
    * [`indexOf()`](operator.md#operator.indexOf)
    * [`setitem()`](operator.md#operator.setitem)
    * [`__setitem__()`](operator.md#operator.__setitem__)
    * [`length_hint()`](operator.md#operator.length_hint)
    * [`call()`](operator.md#operator.call)
    * [`__call__()`](operator.md#operator.__call__)
    * [`attrgetter()`](operator.md#operator.attrgetter)
    * [`itemgetter()`](operator.md#operator.itemgetter)
    * [`methodcaller()`](operator.md#operator.methodcaller)
    * [将运算符映射到函数](operator.md#mapping-operators-to-functions)
    * [原地运算符](operator.md#in-place-operators)
      * [`iadd()`](operator.md#operator.iadd)
      * [`__iadd__()`](operator.md#operator.__iadd__)
      * [`iand()`](operator.md#operator.iand)
      * [`__iand__()`](operator.md#operator.__iand__)
      * [`iconcat()`](operator.md#operator.iconcat)
      * [`__iconcat__()`](operator.md#operator.__iconcat__)
      * [`ifloordiv()`](operator.md#operator.ifloordiv)
      * [`__ifloordiv__()`](operator.md#operator.__ifloordiv__)
      * [`ilshift()`](operator.md#operator.ilshift)
      * [`__ilshift__()`](operator.md#operator.__ilshift__)
      * [`imod()`](operator.md#operator.imod)
      * [`__imod__()`](operator.md#operator.__imod__)
      * [`imul()`](operator.md#operator.imul)
      * [`__imul__()`](operator.md#operator.__imul__)
      * [`imatmul()`](operator.md#operator.imatmul)
      * [`__imatmul__()`](operator.md#operator.__imatmul__)
      * [`ior()`](operator.md#operator.ior)
      * [`__ior__()`](operator.md#operator.__ior__)
      * [`ipow()`](operator.md#operator.ipow)
      * [`__ipow__()`](operator.md#operator.__ipow__)
      * [`irshift()`](operator.md#operator.irshift)
      * [`__irshift__()`](operator.md#operator.__irshift__)
      * [`isub()`](operator.md#operator.isub)
      * [`__isub__()`](operator.md#operator.__isub__)
      * [`itruediv()`](operator.md#operator.itruediv)
      * [`__itruediv__()`](operator.md#operator.__itruediv__)
      * [`ixor()`](operator.md#operator.ixor)
      * [`__ixor__()`](operator.md#operator.__ixor__)
