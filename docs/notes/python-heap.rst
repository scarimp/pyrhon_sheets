.. meta::
    :description lang=en: Collect useful snippets of Python heap
    :keywords: Python, Python Cheat Sheet, heap, heapq, priority queue

====
Heap
====

.. contents:: Table of Contents
    :backlinks: none

Heap Sort
---------

.. code-block:: python

    >>> import heapq
    >>> a = [5, 1, 3, 2, 6]
    >>> h = []
    >>> for x in a:
    ...     heapq.heappush(h, x)
    ...
    >>> x = [heapq.heappop(h) for _ in range(len(a))]
    >>> x
    [1, 2, 3, 5, 6]

Priority Queue
--------------

.. code-block:: python

   import heapq

    h = []
    heapq.heappush(h, (1, "1")) # (priority, value)
    heapq.heappush(h, (5, "5"))
    heapq.heappush(h, (3, "3"))
    heapq.heappush(h, (2, "2"))
    x = [heapq.heappop(h) for _ in range(len(h))]
    print(x)

.. code-block:: python

    import heapq

    class Num(object):
        def __init__(self, n):
            self._n = n

        def __lt__(self, other):
            return self._n < other._n

        def __repr__(self):
            return self.__str__()

        def __str__(self):
            return f"Num({self._n})"

    h = []
    heapq.heappush(h, Num(5))
    heapq.heappush(h, Num(2))
    heapq.heappush(h, Num(1))
    x = [heapq.heappop(h) for _ in range(len(h))]
    print(x)
    # output: [Num(1), Num(2), Num(5)]
