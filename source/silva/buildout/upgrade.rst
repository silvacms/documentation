
Upgrade notes
=============

Specific upgrades notes for specific Silva versions.

.. contents::


Silva 2.2 upgrade notes
-----------------------

You have to install Silva using buildout first. After, *before*
starting your instance, you have to run ``zodbupdate`` on every database
you have:

.. code-block:: sh

   $ ./bin/zodbupdate -f var/filestorage/Data.fs

After, you can start Zope, and access, for each Silva root go to the
URL ``silvaroot-url/service_extensions/manage_extensions`` and click
on update content. Please type the URL in your browser (you can't get
to it by clicking around). It's the first thing you need to do before
trying to access any Silva interface or content. Don't click on
refresh all first.

If your add custom Python scripts in ZODB, you should problary fo the URL
``zope-url/manage_addProduct/PythonScripts/recompile``.
