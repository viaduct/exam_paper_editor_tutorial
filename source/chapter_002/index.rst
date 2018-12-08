Chapter 2. Data Containers
=========================================

In this chapter, I will finish explaining about ``Container`` class and
its subclasses.

You need to open the source code committed with message
``002. Data Containers`` before read the extra chapter.

Superclass ``Container``
--------------------------

The ``Container`` class is a superclass for 4 data container implementations,
*Single*, *List*, *Set*, and *Map*. List, set, and map are well known, and
you may not heard about something like single container but still you can
guess what it is: it's container for only one instance.

``Container`` provides common pure virtual functions which can be applied to
all of 4 containers, or more if you add some new container types later.
They are ``size()`` and ``datas()``. They are pretty straight-forward, but
I still need to explain: ``size()`` returns the number of items in the
container. For ``SingleContainer``, it returns 1 if the container has valid
object, unless returns 0.

``data()`` returns ordered list of all ``Data`` instances the container has.
``SetContainer`` and ``MapContainer`` returns child instances in sorted order.
Key is not included for ``MapContainer``. For ``SingleContainer``, if it has
valid object, the function returns list of one item, otherwise return null
list.

All the ``Container`` subclasses provides 2 version of container: they are
ownership container and reference container. Ownership container takes
its owner's children, while reference container takes reference of other
``Data`` instances which have separate parent.

``Container``\ 's subclasses are class templates, which usually take ``T``
and ``Ptr`` as its parameters. For ``MapContainer``, it takes one more
parameter, ``Key``. ``T`` is a subtype of ``Data`` to be stored, and
``Ptr`` is a smart pointer type which is intended to be used. ``get()`` and
``operator *`` should be implemented: ``std::shared_ptr`` satisfies the
condition. ``Ptr`` can be ``TypeEnum::Ref``, which may invoke reference
version container instead of ownership version.

``SingleContainer``
-----------------------------

In ``single_container.h``, ``SingleContainer`` is defined. It provides
two setters and two events, ``setValid()``, ``setNul()``, ``sig_validSet()``,
and ``sig_beforeNullSet()``.

``setValid()`` is a setter which can be used for null container to set
valid, new data instance. Only valid object is allowed, and calling this with
``nullptr`` with expecting same result with calling ``setNull()`` will show
undefined behavior instead. Nor this should not be called when a valid instance
is already set: ``setNull()`` must be called first then this function is
followed.

``setNull()`` is in opposite side of ``setValid()``. It should be called
only when the container is holding a valid instance, to set it null.

``sig_validSet()`` will be called after ``setValid()`` does its job.

``sig_beforeNullSet()`` will be called **before** ``setValid()`` **does its
job**. Attention at the word "before".

``SetContainer``
--------------------

This container introduces ``add()``, ``remove()``, ``clear()`` setters,
``has()``, ``list()`` getters, and ``sig_added()``, ``sig_beforeRemoved()``,
and ``sig_beforeCleared()``.

``add()``, ``remove()``, and ``clear()`` work just as what general programmers
expect for a general set container, except for that ``add()`` and ``remove()``
will show undefined behavior if existing instance is added again or
non-existing instance is removed.

``has()`` returns true if the instance exists, otherwise returns false.
``list()`` is same with ``datas()``, but it returns in type of template, not
``Data`` superclass.

``sig_added()`` and ``sig_beforeRemoved()`` work as expected, similar with
``SingleContainer``\ 's one.
``sig_beforeCleared()`` is introduced to provide better performance to
signal accepters. When ``clear()`` is called, instead of ``sig_beforeRemoved()``
is called for every instances, ``sig_beforeCleared()`` will be called once.

``ListContainer``
------------------

``single()`` returns an instance placed at passed index.

``list()`` is same with ``datas()``, but returns in type of template,
not ``Data``.

``index()`` is opposite of ``single()``, it takes an instance and returns
position of it. *isValid* out parameter is set false if there's no matching
instance, otherwise it will be set true. If no output *isValid* parameter is
given, the function may show undefined behavior if there's no matching instance.
However, that undefined behavior will be assertion.

``append()``, ``insert()``, ``remove()``, ``move()``, and ``clear()`` work
as just expected for general list container. One uncommon method is ``move()``,
it is not common move operation in C/C++, but takes 2 indexes and swap
the instances' position.

``sig_added()``, ``sig_beforeRemoved()``, and ``sig_beforeCleared()``
are called in same manner with
``SetContainer`` and ``SingleContainer``. ``sig_moved()`` is called **after**
``move()`` finishes its job.

``MapContainer``
-------------------------

``keyList()``, ``valueList()`` and ``list()`` returns list of key, value, or
both.

``value()`` returns an instance which is corresponding to the parameter key.
Undefined behavior will occur if the parameter key does not exist.

``key()`` is opposite of ``value()``: it returns a key matching with an
parameter instance. The implementation may need to iterate through all
instances, so it can be expensive, depended on the number of instances.
Undefined behavior will occur if the parameter instance does not exist.

``hasKey()`` and ``hasValue()`` can be used to check existence of specific
key or instance. Returns true if the parameter exists, otherwise returns false.
``hasValue()`` may be expensive, because the implementation may require
iteration through all instances.


``sig_added()``, ``sig_beforeRemoved()``, and ``sig_beforeCleared()`` follows
exactly same manner with ``SetContainer``.

``SinglePrimContainer``
-----------------------------

Well, this is not a ``Data`` instance container, but it's for general value
types such as ``int``, ``float``, ``std::string``, image, vector, animation,
geometry, and similar ones.

It follows general getter-setter pattern, except modification event is
provided as well. (``sig_updated()``)

Its setter is not separated into valid setter and null setter, since there's no
null value for value types: null is a concept for object types. 
