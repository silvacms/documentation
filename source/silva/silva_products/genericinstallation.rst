Generic product installation steps
==================================

Below describes the different ways you can install a Silva Product.

Extending buildout
------------------

There are several different ways to install a Silva product. If you
are using a buildout be sure that the buildout doesn't already install
the product you wish to install. To see the initial product list see
the ``profiles/base.cfg`` file. If the product you wish to install is
not included in the ``base.cfg`` file see :doc:`../buildout/extending`

svn checkout or tarball
-----------------------

If Silva is built manually (has not been built using buildout), svn
checkout SilvaMailing or download the SilvaMailing tarball into the
relevant "Products" directory.

Zope Management Interface Steps
-------------------------------

Start the zope server and either navigate to an existing Silva root or
create a Silva root.