.. meta::
    :description lang=en: Collect useful snippets of Python datetime
    :keywords: Python, Python Cheat Sheet, datetime

========
Datetime
========

.. contents:: Table of Contents
    :backlinks: none

Timestamp
---------

.. code-block:: python

    >>> import time
    >>> time.time()
    1613526236.395773

    >>> datetime.utcnow()
    datetime.datetime(2021, 2, 17, 1, 45, 19, 312513)

    >>> t = time.time()
    >>> datetime.fromtimestamp(t)
    datetime.datetime(2021, 2, 17, 9, 45, 41, 95756)

    >>> d = datetime.fromtimestamp(t)
    >>> d.timestamp()
    1613526341.095756

Date
----

.. code-block:: python

    >>> from datetime import date
    >>> date.today()
    datetime.date(2021, 2, 17)

Format
------

.. code-block:: python

    >>> from datetime import datetime
    >>> d = datetime.utcnow()
    >>> d.isoformat()
    '2021-02-17T02:26:59.584044'

Convert ``date`` to ``datetime``
--------------------------------

.. code-block:: python

    >>> from datetime import datetime, date
    >>> today = date.today()
    >>> d = datetime.combine(today, datetime.min.time())
    >>> d
    datetime.datetime(2021, 2, 17, 0, 0)
