Testing in Silva
================

.. contents::


Running existing Silva tests
----------------------------

If you installed Silva with :term:`Buildout` configuration file
``development.cfg`` or ``silva-development.cfg`` test script should
have beedn installed in the ``bin`` folder of your :term:`Buildout`
directory.

To run all the existing Silva tests you can just run the ``test-silva``
script:

.. code-block:: sh

  $ ./bin/test-silva

You can use the ``test`` script to run only one extension test, using
the option ``-m``:

.. code-block:: sh

  $ ./bin/test -m Products.Silva

Or you can run only a specific test file:

.. code-block:: sh

  $ ./bin/test -m Products.Silva.tests.test_file

To see the other options, you can use the ``--help`` option of the
``test`` script:

.. code-block:: sh

  $ ./bin/test --help

Other test script are provided with Silva as well:

- ``test-infrae``: Test all the non-specific Silva component used with
  Silva.

- ``test-zeam``: Test all the form framework used with Silva.

.. _creating-a-test-script:

Creating a new test script with Buildout
----------------------------------------

In order to test a new Silva extension, a script is needed to run its
test. This can be done your :term:`Buildout` configuration file by
adding a new section using the `zc.recipe.testrunner`_ recipe.

If you want to test for example ``silva.app.blog`` you should add to
your ``buildout.cfg`` configuration file:

.. code-block:: buildout
   :linenos:

   [buildout]
   parts +=
      test-blog

   [test-blog]
   recipe = zc.recipe.testrunner
   eggs =
      silva.app.blog
   defaults = ['-v']

- Line 3 add the new section ``test-blog`` to the list of section to
  install

- Line 5 to 9 define the new section ``test-blog``. Line 6 specify the
  recipe to use and 7 to 8 the Silva extension to test. Line 9 define
  some default options to automatically give to the test script, like
  the verbose mode in this case.

To know more about the options you can give to your test script, you
can use ``--help`` on it:

.. code-block:: sh

   $ ./bin/test-blog --help

To run the tests, like for the existing Silva tests, just un the script:

.. code-block:: sh

   $ ./bin/test-blog

.. _writing-a-new-test:

Writing a new test
------------------

Tests are written using the Python :py:mod:`unittest` framework. To
run them, `zope.testrunner`_ is used (please refer to
:ref:`creating-a-test-script` in order to know how to run them).

All Python modules containing tests should have the prefix ``test_``
in their name, and be located inside a ``tests`` sub-diretory of the
tested package.

A test case can be written like this:

.. code-block:: python
  :linenos:

  from Products.Silva.testing import FunctionalLayer
  import unittest

  class SilvaTestCase(unittest.TestCase):
      layer = FunctionalLayer

      def setUp(self):
          self.root = self.layer.get_application()
          self.layer.login('author')

      def test_has_root(self):
          assert hasattr(self.root, 'root')

  def test_suite():
      suite = unittest.TestSuite()
      suite.addTest(unittest.makeSuite(SilvaTestCase))
      return suite

- Line 4 create a new test case by inheriting from the
  :py:class:`unittest.TestCase` class.

- Line 5 set the :term:`test layer` to be the default Silva one
  (:py:data:`~Products.Silva.testing.FunctionalLayer`), that will
  create a Silva test site for our testing.

- Line 7 to 9 set up ``self.root`` to be the root of the Silva site,
  and log the test case into Zope as the user *author*.

- Line 14 to 17 declares in a ``test_suite`` function which test case
  we want to be run. This is not required, if we didn't do that, all
  classes inheriting from :py:class:`unittest.TestCase` from the current
  would have been considered as test case to run while running the
  tests.

.. glossary::

   *test layer*
      A test layer factorize in a class all the code needed to set up
      and cleanup the test environment before and after the test is
      run. This let you reuse and extend at will complex set up code
      need to be able to run your test.


Testing API
-----------

.. toctree::
   :maxdepth: 2

   api

.. _zope.testrunner: http://pypi.python.org/pypi/zope.testrunner
.. _zc.recipe.testrunner: http://pypi.python.org/pypi/zc.recipe.testrunner
