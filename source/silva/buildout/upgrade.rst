
Upgrade notes
=============

Specific upgrades notes for specific Silva versions.

.. contents::


Silva 2.2 upgrade notes
-----------------------

First install Silva using buildout. After the buildout has been
installed but *before* starting your instance, run ``zodbupdate`` on
every database you have:

.. code-block:: sh

   $ ./bin/zodbupdate -f var/filestorage/Data.fs

After running ``zodbupdate`` now you can start Zope. Once Zope is
running in the Zope Management Interface (ZMI) go to each Silva root
and navigate to the *service extenstions*
(``silvaroot-url/service_extensions/manage_extensions``) page. Click
on upgrade content. This should be done *before* accessing any Silva
Management Interface or any Silva content.

Recompile your python scripts in the ZODB by running
``zope-url/manage_addProduct/PythonScripts/recompile``.
