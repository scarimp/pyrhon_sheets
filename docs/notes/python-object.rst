===================
Classes and Objects
===================

.. contents:: Table of Contents
    :backlinks: none

List Attributes
---------------

.. code-block:: python

    >>> dir(list)  # check all attr of list
    ['__add__', '__class__', ...]

Get Instance Type
-----------------

.. code-block:: python

    >>> ex = 10
    >>> isinstance(ex, int)
    True

Declare a Class
---------------

.. code-block:: python

    >>> def fib(self, n):
    ...     if n <= 2:
    ...         return 1
    ...     return fib(self, n-1) + fib(self, n-2)
    ...
    >>> Fib = type('Fib', (object,), {'val': 10,
    ...                               'fib': fib})
    >>> f = Fib()
    >>> f.val
    10
    >>> f.fib(f.val)
    55

Equals to

.. code-block:: python

    >>> class Fib(object):
    ...     val = 10
    ...     def fib(self, n):
    ...         if n <=2:
    ...             return 1
    ...         return self.fib(n-1)+self.fib(n-2)
    ...
    >>> f = Fib()
    >>> f.val
    10
    >>> f.fib(f.val)
    55

Has / Get / Set Attributes
--------------------------

.. code-block:: python

    >>> class Example(object):
    ...   def __init__(self):
    ...     self.name = "ex"
    ...   def printex(self):
    ...     print("This is an example")
    ...
    >>> ex = Example()
    >>> hasattr(ex,"name")
    True
    >>> hasattr(ex,"printex")
    True
    >>> hasattr(ex,"print")
    False
    >>> getattr(ex,'name')
    'ex'
    >>> setattr(ex,'name','example')
    >>> ex.name
    'example'

Check Inheritance
-----------------

.. code-block:: python

    >>> class Example(object):
    ...   def __init__(self):
    ...     self.name = "ex"
    ...   def printex(self):
    ...     print("This is an Example")
    ...
    >>> issubclass(Example, object)
    True

Get Class Name
--------------

.. code-block:: python

    >>> class ExampleClass(object):
    ...   pass
    ...
    >>> ex = ExampleClass()
    >>> ex.__class__.__name__
    'ExampleClass'

New and Init
------------

``__init__`` will be invoked

.. code-block:: python

    >>> class ClassA(object):
    ...     def __new__(cls, arg):
    ...         print('__new__ ' + arg)
    ...         return object.__new__(cls, arg)
    ...     def __init__(self, arg):
    ...         print('__init__ ' + arg)
    ...
    >>> o = ClassA("Hello")
    __new__ Hello
    __init__ Hello

``__init__`` won't be invoked

.. code-block:: python

    >>> class ClassB(object):
    ...     def __new__(cls, arg):
    ...         print('__new__ ' + arg)
    ...         return object
    ...     def __init__(self, arg):
    ...         print('__init__ ' + arg)
    ...
    >>> o = ClassB("Hello")
    __new__ Hello


The Diamond Problem
-------------------

The problem of multiple inheritance in searching a method

.. code-block:: python

    >>> def foo_a(self):
    ...     print("This is ClsA")
    ...
    >>> def foo_b(self):
    ...     print("This is ClsB")
    ...
    >>> def foo_c(self):
    ...     print("This is ClsC")
    ...
    >>> class Type(type):
    ...     def __repr__(cls):
    ...         return cls.__name__
    ...
    >>> ClsA = Type("ClsA", (object,), {'foo': foo_a})
    >>> ClsB = Type("ClsB", (ClsA,), {'foo': foo_b})
    >>> ClsC = Type("ClsC", (ClsA,), {'foo': foo_c})
    >>> ClsD = Type("ClsD", (ClsB, ClsC), {})
    >>> ClsD.mro()
    [ClsD, ClsB, ClsC, ClsA, <type 'object'>]
    >>> ClsD().foo()
    This is ClsB

Representation of a Class
-------------------------

.. code-block:: python

    >>> class Example(object):
    ...    def __str__(self):
    ...       return "Example __str__"
    ...    def __repr__(self):
    ...       return "Example __repr__"
    ...
    >>> print(str(Example()))
    Example __str__
    >>> Example()
    Example __repr__

Callable Object
---------------

.. code-block:: python

    >>> class CallableObject(object):
    ...   def example(self, *args, **kwargs):
    ...     print("I am callable!")
    ...   def __call__(self, *args, **kwargs):
    ...     self.example(*args, **kwargs)
    ...
    >>> ex = CallableObject()
    >>> ex()
    I am callable!

Context Manager
---------------

.. code-block:: python

    # replace try: ... finally: ...
    # see: PEP343
    # common use in open and close

    import socket

    class Socket(object):
        def __init__(self, host, port):
            self.host = host
            self.port = port

        def __enter__(self):
            sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            sock.bind((self.host,self.port))
            sock.listen(5)
            self.sock = sock
            return self.sock

        def __exit__(self,*exc_info):
            if exc_info[0] is not None:
                import traceback
                traceback.print_exception(*exc_info)
            self.sock.close()

    if __name__=="__main__":
        host = 'localhost'
        port = 5566
        with Socket(host, port) as s:
            while True:
                conn, addr = s.accept()
                msg = conn.recv(1024)
                print(msg)
                conn.send(msg)
                conn.close()

Using contextlib
----------------

.. code-block:: python

    from contextlib import contextmanager

    @contextmanager
    def opening(filename, mode='r'):
       f = open(filename, mode)
       try:
          yield f
       finally:
          f.close()

    with opening('example.txt') as fd:
       fd.read()

Property
--------

.. code-block:: python

    >>> class Example(object):
    ...     def __init__(self, value):
    ...        self._val = value
    ...     @property
    ...     def val(self):
    ...         return self._val
    ...     @val.setter
    ...     def val(self, value):
    ...         if not isinstance(value, int):
    ...             raise TypeError("Expected int")
    ...         self._val = value
    ...     @val.deleter
    ...     def val(self):
    ...         del self._val
    ...
    >>> ex = Example(123)
    >>> ex.val = "str"
    Traceback (most recent call last):
      File "", line 1, in
      File "test.py", line 12, in val
        raise TypeError("Expected int")
    TypeError: Expected int

Equals to

.. code-block:: python

    >>> class Example(object):
    ...     def __init__(self, value):
    ...        self._val = value
    ...
    ...     def _val_getter(self):
    ...         return self._val
    ...
    ...     def _val_setter(self, value):
    ...         if not isinstance(value, int):
    ...             raise TypeError("Expected int")
    ...         self._val = value
    ...
    ...     def _val_deleter(self):
    ...         del self._val
    ...
    ...     val = property(fget=_val_getter, fset=_val_setter, fdel=_val_deleter, doc=None)
    ...

Computed Attributes
-------------------

``@property`` computes a value of a attribute only when we need. Not store in
memory previously.

.. code-block:: python

    >>> class Example(object):
    ...   @property
    ...   def square3(self):
    ...     return 2**3
    ...
    >>> ex = Example()
    >>> ex.square3
    8

Descriptor
----------

.. code-block:: python

    >>> class Integer(object):
    ...   def __init__(self, name):
    ...     self._name = name
    ...   def __get__(self, inst, cls):
    ...     if inst is None:
    ...       return self
    ...     else:
    ...       return inst.__dict__[self._name]
    ...   def __set__(self, inst, value):
    ...     if not isinstance(value, int):
    ...       raise TypeError("Expected int")
    ...     inst.__dict__[self._name] = value
    ...   def __delete__(self,inst):
    ...     del inst.__dict__[self._name]
    ...
    >>> class Example(object):
    ...   x = Integer('x')
    ...   def __init__(self, val):
    ...     self.x = val
    ...
    >>> ex1 = Example(1)
    >>> ex1.x
    1
    >>> ex2 = Example("str")
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
      File "<stdin>", line 4, in __init__
      File "<stdin>", line 11, in __set__
    TypeError: Expected an int
    >>> ex3 = Example(3)
    >>> hasattr(ex3, 'x')
    True
    >>> del ex3.x
    >>> hasattr(ex3, 'x')
    False

Singleton Decorator
-------------------

Singleton is a design pattern that restricts the creation of instances of a class so that it only creates one instance of the class that implements it.

.. code-block:: python

    #!/usr/bin/env python3
    """Singleton decorator class."""

    class Singleton(object):

        def __init__(self, cls):
            self.__cls = cls
            self.__obj = None

        def __call__(self, *args, **kwargs):
            if not self.__obj:
                self.__obj = self.__cls(*args, **kwargs)
            return self.__obj

    if __name__ == "__main__":
        # Testing ...

        @Singleton
        class Test(object):

            def __init__(self, text):
                self.text = text

        a = Test("Hello")
        b = Test("World")

        print("id(a):", id(a), "id(b):", id(b), "Diff:", id(a)-id(b))

Static and Class Methond
------------------------

``@classmethod`` is bound to a class. ``@staticmethod`` is similar to a python
function but define in a class.

.. code-block:: python

    >>> class example(object):
    ...   @classmethod
    ...   def clsmethod(cls):
    ...     print("I am classmethod")
    ...   @staticmethod
    ...   def stmethod():
    ...     print("I am staticmethod")
    ...   def instmethod(self):
    ...     print("I am instancemethod")
    ...
    >>> ex = example()
    >>> ex.clsmethod()
    I am classmethod
    >>> ex.stmethod()
    I am staticmethod
    >>> ex.instmethod()
    I am instancemethod
    >>> example.clsmethod()
    I am classmethod
    >>> example.stmethod()
    I am staticmethod
    >>> example.instmethod()
    Traceback (most recent call last):
      File "", line 1, in
    TypeError: unbound method instmethod() ...

Abstract Method
---------------

``abc`` is used to define methods but not implement

.. code-block:: python

    >>> from abc import ABCMeta, abstractmethod
    >>> class base(object):
    ...   __metaclass__ = ABCMeta
    ...   @abstractmethod
    ...   def absmethod(self):
    ...     """ Abstract method """
    ...
    >>> class example(base):
    ...   def absmethod(self):
    ...     print("abstract")
    ...
    >>> ex = example()
    >>> ex.absmethod()
    abstract

Another common way is to ``raise NotImplementedError``

.. code-block:: python

    >>> class base(object):
    ...   def absmethod(self):
    ...     raise NotImplementedError
    ...
    >>> class example(base):
    ...   def absmethod(self):
    ...     print("abstract")
    ...
    >>> ex = example()
    >>> ex.absmethod()
    abstract

Using slot to Save Memory
-------------------------

.. code-block:: python

    #!/usr/bin/env python3

    import resource
    import platform
    import functools


    def profile_mem(func):
        @functools.wraps(func)
        def wrapper(*a, **k):
            s = resource.getrusage(resource.RUSAGE_SELF).ru_maxrss
            ret = func(*a, **k)
            e = resource.getrusage(resource.RUSAGE_SELF).ru_maxrss

            uname = platform.system()
            if uname == "Linux":
                print(f"mem usage: {e - s} kByte")
            elif uname == "Darwin":
                print(f"mem usage: {e - s} Byte")
            else:
                raise Exception("not support")
            return ret
        return wrapper


    class S(object):
        __slots__ = ['attr1', 'attr2', 'attr3']

        def __init__(self):
            self.attr1 = "Foo"
            self.attr2 = "Bar"
            self.attr3 = "Baz"


    class D(object):

        def __init__(self):
            self.attr1 = "Foo"
            self.attr2 = "Bar"
            self.attr3 = "Baz"


    @profile_mem
    def alloc(cls):
        _ = [cls() for _ in range(1000000)]


    alloc(S)
    alloc(D)

output:

.. code-block:: console

    $ python3.6 s.py
    mem usage: 70922240 Byte
    mem usage: 100659200 Byte

Common Magic
------------

.. code-block:: python

    # see python document: data model
    # For command class
    __main__
    __name__
    __file__
    __module__
    __all__
    __dict__
    __class__
    __doc__
    __init__(self, [...)
    __str__(self)
    __repr__(self)
    __del__(self)

    # For Descriptor
    __get__(self, instance, owner)
    __set__(self, instance, value)
    __delete__(self, instance)

    # For Context Manager
    __enter__(self)
    __exit__(self, exc_ty, exc_val, tb)

    # Emulating container types
    __len__(self)
    __getitem__(self, key)
    __setitem__(self, key, value)
    __delitem__(self, key)
    __iter__(self)
    __contains__(self, value)

    # Controlling Attribute Access
    __getattr__(self, name)
    __setattr__(self, name, value)
    __delattr__(self, name)
    __getattribute__(self, name)

    # Callable object
    __call__(self, [args...])

    # Compare related
    __cmp__(self, other)
    __eq__(self, other)
    __ne__(self, other)
    __lt__(self, other)
    __gt__(self, other)
    __le__(self, other)
    __ge__(self, other)

    # arithmetical operation related
    __add__(self, other)
    __sub__(self, other)
    __mul__(self, other)
    __div__(self, other)
    __mod__(self, other)
    __and__(self, other)
    __or__(self, other)
    __xor__(self, other)
