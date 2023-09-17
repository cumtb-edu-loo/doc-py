# 弱引用对象¶

Python 支持 “弱引用” 作为一类对象。具体来说，有两种直接实现弱引用的对象。第一种就是简单的引用对象，第二种尽可能地作用为一个原对象的代理。

int PyWeakref_Check([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  

    

~~~
Return non-zero if _ob_ is either a reference or proxy object. This function always succeeds.

int PyWeakref_CheckRef([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
Return non-zero if _ob_ is a reference object. This function always succeeds.

int PyWeakref_CheckProxy([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
