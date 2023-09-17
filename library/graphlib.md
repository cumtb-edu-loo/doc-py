# `graphlib` \--- 操作类似图的结构的功能¶

**源代码:** [Lib/graphlib.py](https://github.com/python/cpython/tree/3.12/Lib/graphlib.py)

* * *

_class _graphlib.TopologicalSorter( _graph =None_)¶

    

~~~
提供以拓扑方式对由 [hashable](../glossary.md#term-hashable) 节点组成的图进行排序的功能。

拓扑排序是指图中顶点的线性排序，使得对于每条从顶点 u 到顶点 v 的有向边 u -> v，顶点 u 都排在顶点 v 之前。 例如，图的顶点可以代表要执行的任务，而边代表某一个任务必须在另一个任务之前执行的约束条件；在这个例子中，拓扑排序只是任务的有效序列。 完全拓扑排序 当且仅当图不包含有向环，也就是说为有向无环图时，完全拓扑排序才是可能的。

如果提供了可选的 _graph_ 参数则它必须为一个表示有向无环图的字典，其中的键为节点而值为包含图中该节点的所有上级节点（即具有指向键中的值的边的节点）的可迭代对象。 额外的节点可以使用 `add()` 方法添加到图中。

在通常情况下，对给定的图执行排序所需的步骤如下:

>   * 通过可选的初始图创建一个 `TopologicalSorter` 的实例。
>
>   * 添加额外的节点到图中。
>
>   * 在图上调用 `prepare()`。
>
>   * 当 `is_active()` 为 `True` 时，迭代 `get_ready()` 所返回的节点并加以处理。 完成处理后在每个节点上调用 `done()`。
>
>

在只需要对图中的节点进行立即排序并且不涉及并行性的情况下，可以直接使用便捷方法 `TopologicalSorter.static_order()`:
~~~
    
    
~~~shell
>>> graph = {"D": {"B", "C"}, "C": {"A"}, "B": {"A"}}
>>> ts = TopologicalSorter(graph)
>>> tuple(ts.static_order())
('A', 'C', 'B', 'D')
~~~

这个类被设计用来在节点就绪时方便地支持对其并行处理。 例如:

    
    
~~~
topological_sorter = TopologicalSorter()

# Add nodes to 'topological_sorter'...

topological_sorter.prepare()
while topological_sorter.is_active():
    for node in topological_sorter.get_ready():
        # Worker threads or processes take nodes to work on off the
        # 'task_queue' queue.
        task_queue.put(node)

    # When the work for a node is done, workers put the node in
    # 'finalized_tasks_queue' so we can get more nodes to work on.
    # The definition of 'is_active()' guarantees that, at this point, at
    # least one node has been placed on 'task_queue' that hasn't yet
    # been passed to 'done()', so this blocking 'get()' must (eventually)
    # succeed.  After calling 'done()', we loop back to call 'get_ready()'
    # again, so put newly freed nodes on 'task_queue' as soon as
    # logically possible.
    node = finalized_tasks_queue.get()
    topological_sorter.done(node)
~~~

add( _node_ , _* predecessors_)¶

    

~~~
将一个新节点及其上级节点添加到图中。 _node_ 和 _predecessors_ 中的所有元素都必须是 [hashable](../glossary.md#term-hashable)。

如果附带相同的节点参数多次调用，则依赖项的集合将为所有被传入依赖项的并集。

可以添加不带依赖项的节点 (即不提供 _predecessors_ ) 或者重复提供依赖项。 如果有先前未提供的节点包含在 _predecessors_ 中则它将被自动添加到图中并且不带自己的上级节点。

如果在 `prepare()` 之后被调用则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

prepare()¶
~~~
    

~~~
将图标记为已完成并检查图中是否存在环。 如何检测到任何环，则将引发 `CycleError`，但 `get_ready()` 仍可被用来获取尽可能多的节点直到环阻塞了操作过程。 在调用此函数后，图将无法再修改，因此不能再使用 `add()` 添加更多的节点。

is_active()¶
~~~
    

~~~
如果可以取得更多进展则返回 `True`，否则返回 `False`。 如果环没有阻塞操作，并且还存在尚未被 `TopologicalSorter.get_ready()` 返回的已就绪节点或者已标记为 `TopologicalSorter.done()` 的节点数量少于已被 `TopologicalSorter.get_ready()` 所返回的节点数量则还可以取得进展。

该类的 [`__bool__()`](../reference/datamodel.md#object.__bool__ "object.__bool__") 方法要使用此函数，因此除了:
~~~
    
    
~~~
if ts.is_active():
    ...
~~~

可能会简单地执行:

    
    
~~~
if ts:
    ...
~~~

如果之前未调用 `prepare()` 就调用此函数则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

done( _* nodes_)¶

    

~~~
将 `TopologicalSorter.get_ready()` 所返回的节点集合标记为已处理，解除对 _nodes_ 中每个节点的后续节点的阻塞以便在将来通过对 `TopologicalSorter.get_ready()` 的调用来返回它们。

如果 _nodes_ 中的任何节点已经被之前对该方法的调用标记为已处理或者如果未通过使用 `TopologicalSorter.add()` 将一个节点添加到图中，如果未调用 `prepare()` 即调用此方法或者如果节点尚未被 `get_ready()` 所返回则将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

get_ready()¶
~~~
    

~~~
返回由所有已就绪节点组成的 `tuple`。 初始状态下它将返回所有不带上级节点的节点，并且一旦通过调用 `TopologicalSorter.done()` 将它们标记为已处理，之后的调用将返回所有上级节点已被处理的新节点。 一旦无法再取得进展，则会返回空元组。

如果之前未调用 `prepare()` 就调用此函数则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

static_order()¶
~~~
    

~~~
返回一个迭代器，它将按照拓扑顺序来迭代所有节点。 当使用此方法时，`prepare()` 和 `done()` 不应被调用。 此方法等价于:
~~~
    
    
~~~python
def static_order(self):
    self.prepare()
    while self.is_active():
        node_group = self.get_ready()
        yield from node_group
        self.done(*node_group)
~~~

所返回的特定顺序可能取决于条目被插入图中的顺序。 例如:

    
    
~~~shell
>>> ts = TopologicalSorter()
>>> ts.add(3, 2, 1)
>>> ts.add(1, 0)
>>> print([*ts.static_order()])
[2, 0, 1, 3]

>>> ts2 = TopologicalSorter()
>>> ts2.add(1, 0)
>>> ts2.add(3, 2, 1)
>>> print([*ts2.static_order()])
[0, 2, 1, 3]
~~~

这是由于实际上 "0" 和 "2" 在图中的级别相同（它们将在对 `get_ready()` 的同一次调用中被返回） 并且它们之间的顺序是由插入顺序决定的。

如果检测到任何环，则将引发 `CycleError`。

在 3.9 版本加入.

## 异常¶

`graphlib` 模块定义了以下异常类:

_exception _graphlib.CycleError¶

    

~~~
