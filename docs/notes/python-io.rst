.. meta::
    :description lang=en: Collect useful snippets of I/O operations.
    :keywords: Python, Python I/O Operations

=============
Files and I/O
=============

.. contents:: Table of Contents
    :backlinks: none

Read a File
-----------

In Python 2, the content of the file which read from file system does not
decode. That is, the content of the file is a byte string, not a Unicode
string.

.. code-block:: python

    >>> with open("/etc/passwd") as f:
    ...    content = f.read()
    >>> print(type(content))
    <type 'str'>
    >>> print(type(content.decode("utf-8")))
    <type 'unicode'>

In Python 3, `open <https://docs.python.org/3/library/functions.html#open>`_
provides ``encoding`` option. If files do not open in binary mode, the encoding
will be determined by ``locale.getpreferredencoding(False)`` or user's input.

.. code-block:: python

    >>> with open("/etc/hosts", encoding="utf-8") as f:
    ...     content = f.read()
    ...
    >>> print(type(content))
    <class 'str'>

Binary mode

.. code-block:: python

    >>> with open("/etc/hosts", "rb") as f:
    ...     content = f.read()
    ...
    >>> print(type(content))
    <class 'bytes'>

Readline
--------

.. code-block:: python

    >>> with open("/etc/hosts") as f:
    ...     for line in f:
    ...         print(line, end='')
    ...
    127.0.0.1       localhost
    255.255.255.255	broadcasthost
    ::1             localhost

Reading File Chunks
-------------------

.. code-block:: python


    >>> chunk_size = 16
    >>> content = ''
    >>> with open('/etc/hosts') as f:
    ...     for c in iter(lambda: f.read(chunk_size), ''):
    ...         content += c
    ...
    >>> print(content)
    127.0.0.1       localhost
    255.255.255.255 broadcasthost
    ::1             localhost

Write a File
------------

.. code-block:: python

    >>> content = "Awesome Python!"
    >>> with open("foo.txt", "w") as f:
    ...     f.write(content)

Create a Symbolic Link
----------------------

.. code-block:: python

    >>> import os
    >>> os.symlink("foo", "bar")
    >>> os.readlink("bar")
    'foo'

Copy a File
-----------

.. code-block:: python

    >>> from distutils.file_util import copy_file
    >>> copy_file("foo", "bar")
    ('bar', 1)

Move a File
-----------

.. code-block:: python

    >>> from distutils.file_util import move_file
    >>> move_file("./foo", "./bar")
    './bar'

List a Directory
----------------

.. code-block:: python

    >>> >>> import os
    >>> dirs = os.listdir(".")

After Python 3.6, we can use ``os.scandir`` to list a directory. It is more
convenient because ``os.scandir`` return an iterator of ``os.DirEntry`` objects.
In this case, we can get file information through access the attributes of
``os.DirEntry``. Further information can be found on the
`document <https://docs.python.org/3/library/os.html#os.scandir>`_.

.. code-block:: python

    >>> with os.scandir("foo") as it:
    ...     for entry in it:
    ...         st = entry.stat()
    ...

Create Directories
------------------

Similar to ``mkdir -p /path/to/dest``

.. code-block:: python

    >>> from distutils.dir_util import mkpath
    >>> mkpath("foo/bar/baz")
    ['foo', 'foo/bar', 'foo/bar/baz']

Copy a Directory
----------------

.. code-block:: python

    >>> from distutils.dir_util import copy_tree
    >>> copy_tree("foo", "bar")
    ['bar/baz']

Remove a Directory
------------------

.. code-block:: python

    >>> from distutils.dir_util import remove_tree
    >>> remove_tree("dir")

Path Join
---------

.. code-block:: python

    >>> from pathlib import Path
    >>> p = Path("/Users")
    >>> p = p / "Guido" / "pysheeet"
    >>> p
    PosixPath('/Users/Guido/pysheeet')

Get Absolute Path
-----------------

.. code-block:: python

    >>> from pathlib import Path
    >>> p = Path("README.rst")
    PosixPath('/Users/Guido/pysheeet/README.rst')

Get Home Directory
------------------

.. code-block:: python

    >>> from pathlib import Path
    >>> Path.home()
    PosixPath('/Users/Guido')

Get Current Directory
---------------------

.. code-block:: python

    >>> from pathlib import Path
    >>> p = Path("README.rst")
    >>> p.cwd()
    PosixPath('/Users/Guido/pysheeet')

Get Path Properties
-------------------

.. code-block:: python

    >>> from pathlib import Path
    >>> p = Path("README.rst").absolute()
    >>> p.root
    '/'
    >>> p.anchor
    '/'
    >>> p.parent
    PosixPath('/Users/Guido/pysheeet')
    >>> p.parent.parent
    PosixPath('/Users/Guido')
    >>> p.name
    'README.rst'
    >>> p.suffix
    '.rst'
    >>> p.stem
    'README'
    >>> p.as_uri()
    'file:///Users/Guido/pysheeet/README.rst'

Linux Inotify
-------------

.. code-block:: python3

    import selectors
    import struct
    import ctypes
    import sys
    import os

    from pathlib import Path
    from ctypes.util import find_library

    # ref: <sys/inotify.h>
    IN_CREATE = 0x00000100
    IN_DELETE = 0x00000200

    INOTIFY_EVENT = "iIII"
    INOTIFY_EVENT_LEN = struct.calcsize(INOTIFY_EVENT)

    lib = find_library("c")
    assert lib

    libc = ctypes.CDLL(lib)


    class Inotify(object):
        def __init__(self, path):
            self._path = path
            self._fd = None
            self._wd = None
            self._buf = b""
            self._sel = selectors.DefaultSelector()

        def init(self):
            fd = libc.inotify_init()
            if fd < 0:
                errno = ctypes.get_errno()
                raise OSError(errno, f"{os.strerror(errno)}")
            return fd

        def watch(self, fd, path):
            p = str(path).encode("utf8")
            wd = libc.inotify_add_watch(fd, p, IN_CREATE | IN_DELETE)
            if wd < 0:
                errno = ctypes.get_errno()
                raise OSError(errno, f"{os.strerror(errno)}")
            return wd

        def remove(self, fd, wd):
            libc.inotify_rm_watch(self._fd, self._wd)

        def handle(self, fd, *a):
            b = os.read(fd, 1024)
            if not b:
                return
            yield from self.parse(b);

        def parse(self, buf):
            self._buf += buf
            while True:
                l = len(self._buf)
                if l < INOTIFY_EVENT_LEN:
                    break

                hd = self._buf[:INOTIFY_EVENT_LEN]
                wd, mask, cookie, length = struct.unpack(INOTIFY_EVENT, hd)
                event_length = INOTIFY_EVENT_LEN + length
                if l < event_length:
                    break

                filename = self._buf[INOTIFY_EVENT_LEN:event_length]
                self._buf = self._buf[event_length:]
                yield mask, filename.rstrip(b"\0").decode("utf8")

        def __enter__(self):
            self._fd = self.init()
            self._wd = self.watch(self._fd, self._path)
            self._sel.register(self._fd, selectors.EVENT_READ, self.handle)
            return self

        def __exit__(self, *e):
            self.remove(self._fd, self._wd)
            if len(e) > 0 and e[0]:
                print(e, file=sys.stderr)

        def run(self):
            while True:
                events = self._sel.select()
                for k, mask in events:
                    cb = k.data
                    yield from cb(k.fileobj, mask)


    with Inotify(Path("/tmp")) as i:
        for m, f in i.run():
            print(m, f)
