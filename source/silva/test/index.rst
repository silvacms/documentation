Test in Silva
=============

To run the Silva tests you can use the ``test`` subcommand of
``instance`` (or the script used to control your Zope instance). So,
assuming the current working directory is your Zope
instance/buildout::

  ./bin/instance test -s Products.Silva

Use the ``-m`` option to run a single test module.::

  ./bin/instance test -m Products.Silva.test.test_silvaviews

To see the other options for the test subcommand use the ``--help``
option.

  ./bin/instance test --help

.. note::

  If your not using buildout, ``instance`` is called ``zopectl``.

.. note::

  Using the buildout, will generate a script in bin call ``test``.
  The ``tests`` script will run all the tests for Silva without
  passing any options.

Writing a new test
------------------

All test modules should have the prefix ``test_`` in the module name,
and be located in a ``tests`` directory of the package that you want
to test. Hereby a example of a test:

.. code-block:: python

  from Products.Silva.tests import SilvaTestCase

  class TestSilvaTestCase(SilvaTestCase.SilvaTestCase):

      def afterSetUp(self):
          pass

      def test_hasRoot(self):
          assert hasattr(self.app, 'root')

  def test_suite():
      import unittest
      suite = unittest.TestSuite()
      suite.addTest(unittest.makeSuite(TestSilvaTestCase))
      return suite

Within the test module you need to create a test class as shown above
in ``TestSilvaTestCase``. To inherit important testing functionality
it is always a good idea to pass the ``SilvaTestCase.SilvaTestCase``
class. Inside the test class are methods. These methods are your
actual tests. Each test method must begin with the prefix ``test_``.

You can get more information about writing tests in Python on the
`Python website <http://docs.python.org/lib/module-unittest.html>`_.

Useful information:

  * Zope root: ``self.app``,

  * Silva root: ``self.root``,

  * Role of default user: ``ChiefEditor``.

Documentation on test API
-------------------------

.. toctree::
   :maxdepth: 2

   silvatestcase
   helpers
   silvabrowser
