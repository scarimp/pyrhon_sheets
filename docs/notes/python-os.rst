.. meta::
    :description lang=en: Collect useful snippets of Python
    :keywords: Python, Python Cheat Sheet, Operating System

================
Operating System
================

.. contents:: Table of Contents
    :backlinks: none

Get Number of CPUs
------------------

.. code-block:: python

   >>> import os
   >>> os.cpu_count()

Set Affinity
------------

.. code-block:: python

    # run on linux
    import os

    pid = os.getpid()
    affinity = {1}
    os.sched_setaffinity(pid, affinity)
