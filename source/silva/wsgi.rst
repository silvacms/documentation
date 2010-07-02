Configuring Apache's mod_wsgi to present your site to the world
===============================================================

Silva now support WSGI. You can use Apache's ``mod_wsgi`` to directly
serve it, without having it running in a different daemon and proxing
request to it.

.. contents::


Requirements
------------

For this you need to install ``mod_wsgi`` for the Apache you are using.


.. warning::

  When you install ``mod_wsgi`` it is compiled to use a specific
  version and installation of Python, usually the one of your system
  if you install it with a your system packaging system.

  ``mod_wsgi`` **should** use Python 2.6, and you **should** install
  Silva with this same Python.


For this reason we recommend you to install ``mod_wsgi`` by hand.


Configuration
-------------


Multiple processus
~~~~~~~~~~~~~~~~~~

If you wish to configure multiple WSGI processus, you need first to
install a ZEO server. You **cannot** configure multiple processus
without a ZEO server or might corrupt your Zope database.


Usage
-----

Now Silva is directly embed in Apache. To *restart* Silva, you need to
restart Apache. To *stop* Silva, you need to stop Apache. You **should
not** use the installed Silva daemon while Silva is running within
Apache.
