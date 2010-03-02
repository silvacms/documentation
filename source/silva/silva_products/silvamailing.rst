Silva Mailing
=============

Requirements
------------

SilvaMailing allows users to create mailing lists. SilvaMailing is
valid for Silva-1.6 and Silva-2.x.

Install
-------

There are several different ways to install a Silva product. If you
are using a buildout be sure that the buildout doesn't already install
the product you wish to install. To see the initial product list see
the ``profiles/base.cfg`` file. If the product you wish to install is
not included in the ``base.cfg`` file see :doc:`../buildout/extending`

If Silva is built manually (has not been built using buildout), svn
checkout SilvaMailing or download the SilvaMailing tarball into the
relevant "Products" directory.

Start the zope server and either navigate to an existing Silva root or
create a Silva root. Then navigate to the ``services`` page and then
the ``service_extensions`` page. Locate the product you just installed
and click ``activate``.