
Infrae coding style
===================

.. contents::

Coding style
------------

Silva, and other Infrae products should follow the coding style
defined by `pep8`_, with the following enforcement.


Spaces
~~~~~~

- Code is indented with spaces,

- Trailing white-spaces at the end of lines should be removed,

- Files should not be saved with Windows end of lines (only Unix ones),

- ASCII encoding is preferred when saving files. If it is not enough,
  utf-8 encoding should be used, with a coding cookie on the first
  line of the file (``# -*- coding: utf-8 -*-``),

Doc string
~~~~~~~~~~

- All methods and classes should have a doc string,

- In a method, the code should start right after the doc string,
  without an empty separating the both of them,

Classes
~~~~~~~

- All classes should inherit at least from ``object``.

Imports
~~~~~~~

- No local imports should be used,

- Imports should be sorted by alphabetical order,

- Unused imports should be removed.

Example
-------

Here is an example:

.. code-block:: python

   # Copyright (c) 2010 Infrae. All rights reserved.
   # See also LICENSE.txt
   # $Id$

   from silva.foo.bar.interfaces import IBar
   from zope import component, interface

   CONST_VALUE = 42


   def do_something(self):
       """Method to do something.
       """
       return None


   class BarImplementation(object):
       """Implement a bar following the Netherlands specification.
       """
       some_product = []

       def retrieve_data(self):
           """Retrieve Bar data.
           """
           return self.some_product

Tools
-----

The following tools can help you:

- `pep8 script`_ to validate that your code follow the pep8 coding style,

- `pyflakes`_ help you to identify unused and missing imports,

- `dependencychecker`_ does a similar job as `pyflakes`_ but is able
  to identify missing requirements defined in your extension (by
  parsing the ``egg-info`` directory).

.. _dependencychecker: http://pypi.python.org/pypi/z3c.dependencychecker
.. _pyflakes: http://pypi.python.org/pypi/pyflakes
.. _pep8 script: http://pypi.python.org/pypi/pep8
.. _pep8: http://www.python.org/dev/peps/pep-0008

