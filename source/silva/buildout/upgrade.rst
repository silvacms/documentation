
.. _upgrade-notes:

Upgrade notes
=============

Specific upgrades notes for specific Silva versions

.. contents::


Silva 2.2 upgrade notes
-----------------------

First install Silva using buildout. After, *before* starting your
instance, run ``zodbupdate`` on every database you have:

.. code-block:: sh

   $ ./bin/zodbupdate -f var/filestorage/Data.fs


Only after this step, you can start Zope. Before using your site
access, for each Silva Root you have go to the URL
``silvaroot-url/service_extensions/manage_extensions`` and click on
the button update content.

Please type this last URL in your browser (you can’t get to it by
clicking around). *It is important to do it before using anything in
your upgraded Silva*. Don’t click on refresh all before.

Recompile your python scripts in the ZODB by accessing the URL
``zope-url/manage_addProduct/PythonScripts/recompile``.

