Test in Silva
=============

To run the Silva tests you can use the ``test`` subcommand of
``instance`` (or the script used to control your Zope instance). So,
assuming the current working directory is your Zope
instance/buildout::

  ./bin/instance test -s Products.Silva

In case you'd like to run only one particular set of tests you can do this::

  ./bin/instance test -m Products.Silva.test.test_silvaviews

There're a lot more options for the test subcommand. For more information
you can issue::

  ./bin/instance test --help

.. note::

  If you don't use buildout, ``instance`` might be called ``zopectl``.

.. note::

  If you use buildout, you might already have a script called
  ``bin/test`` which run tests for Silva. You don't need to add any
  argument to that script.


Writing a new test
------------------

Your test module should have the prefix ``test_``, and be located in a
``tests`` package of the package that you want to test. Hereby a
example of a test:

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

Your test is represented by the method ``test_hasRoot``. You can add
more test to your test case, but they should start with ``test_`` like
the file does.

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
