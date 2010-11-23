Tests in Silva
==============


How to run the Silva test suite
-------------------------------

If you used the Buildout profile ``development.cfg`` or
``silva-development.cfg`` you should have tests scripts installed in
the ``bin`` folder of your Buildout directory.

To run all the Silva tests you can use the ``test-all`` script:

.. code-block:: sh

  $ ./bin/test-all

You can use the ``test`` script to run only one extension test, using
the option ``-s``:

.. code-block:: sh

  $ ./bin/test -s Products.Silva

Or if you which to run tests only in a file, you can use the same
``test`` script, with the ``-m`` option:

.. code-block:: sh

  $ ./bin/test -m Products.Silva.tests.test_file


To see the other options, you can use the ``--help`` option of the
``test`` script:

.. code-block:: sh

  $ ./bin/test --help


Writing a new test
------------------

All test modules should have the prefix ``test_`` in the module name,
and be located in a ``tests`` sub-package of the package that you want
to test.

Hereby a example of a functional test that need a Silva site:

.. code-block:: python
  :linenos:

  from Products.Silva.testing import FunctionalLayer
  import unittest

  class RootSilvaTestCase(unittest.TestCase):
      layer = FunctionalLayer

      def setUp(self):
          self.root = self.layer.get_application()
          self.layer.login('author')

      def test_has_root(self):
          assert hasattr(self.root, 'root')

  def test_suite():
      suite = unittest.TestSuite()
      suite.addTest(unittest.makeSuite(TestSilvaTestCase))
      return suite


The default Python ``unittest`` framework is used for testing. You can
get more information about writing tests in Python on the `Python
website <http://docs.python.org/lib/module-unittest.html>`_.


Documentation on test API
-------------------------

.. toctree::
   :maxdepth: 2

   helpers
