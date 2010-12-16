Tests in Silva
==============

.. contents::


Running existing Silva tests
----------------------------

If you used the :term:`Buildout` profile ``development.cfg`` or
``silva-development.cfg`` you should have tests scripts installed in
the ``bin`` folder of your :term:`Buildout` directory.

To run all the existing Silva tests you can just run the ``test-all``
script:

.. code-block:: sh

  $ ./bin/test-all

You can use the ``test`` script to run only one extension test, using
the option ``-s``:

.. code-block:: sh

  $ ./bin/test -s Products.Silva

Or if you which to run only a test file, you can use the same ``test``
script, with the ``-m`` option:

.. code-block:: sh

  $ ./bin/test -m Products.Silva.tests.test_file


To see the other options, you can use the ``--help`` option of the
``test`` script:

.. code-block:: sh

  $ ./bin/test --help

.. _creating-a-test-script:

Creating a new test script in Buildout
--------------------------------------

If you whish to test a new extension, you need to create a
`zope.testrunner`_ script to run this extension tests. You can do this
in :term:`Buildout` by adding a new section using the
`zc.recipe.testrunner`_ recipe:

.. code-block:: buildout
   :linenos:

   [buildout]
   parts +=
      blog_tests

   [blog_tests]
   recipe = zc.recipe.testrunner
   eggs =
      silva.app.blog
   defaults = ['-v']

On line 5 through 9 we define a section which create our test script,
``blog_tests``. On line 7 we give a list of extension we want to test,
here ``silva.app.blog``. On line 9, we give some default options to
the test runner, here to be verbose by default.

To know more about the test script options, you can use ``--help`` on
the created script:

.. code-block:: sh

   $ ./bin/blog_tests --help

To run the tests, like for the existing Silva tests, just un the script:

.. code-block:: sh

   $ ./bin/blog_tests


Writing a new test
------------------

Tests are written using the Python :py:mod:`unittest` framework. To
run them, `zope.testrunner`_ is used (please refer to
:ref:`creating-a-test-script` in order to know how to run them).

All modules containing tests should have the prefix ``test_`` in their
module name, and be located in a ``tests`` sub-package of the tested
package.

In a testing module, you can rewrite a test case like this:

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

On line 4, we create a new test case by inheriting from the
:py:class:`unittest.TestCase` class. On line 5, we set the :term:`test
layer` to be the Silva
:py:data:`~Products.Silva.testing.FunctionalLayer`, that will create a
Silva test site for our testing.

On line 7 to 9, we set up ``self.root`` to be the root of the Silva
site, and we logged into Zope as the user *author*.

On line 14 to 17, we declare in a ``test_suite`` function which test
case we want to be run. This is not required, if we didn't do that,
all classes inheriting from :py:class:`unittest.TestCase` from the
current would have been considered as test case to run while running
the tests.

.. glossary::

   *test layer*
      A test layer factorize in a class all the code needed to set up
      and cleanup the test environment before and after the test is
      run. This let you reuse and extend at will complex set up code
      need to be able to run your test.


Documentation on test API
-------------------------

.. toctree::
   :maxdepth: 2

   layers
   helpers

.. _zope.testrunner: http://pypi.python.org/pypi/zope.testrunner
.. _zc.recipe.testrunner: http://pypi.python.org/pypi/zc.recipe.testrunner
