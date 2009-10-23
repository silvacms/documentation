
Upgrade to Silva 2.2
====================

You have to install Silva using buildout first. After, *before*
starting your instance, you have to run zodbupdate on every database
you have:

.. code:: shell

   $ ./bin/zodbupdate -i -f var/filestorage/Data.fs

After, you can start Zope, and access, for each Silva root you have
``silvaroot/service_extensions/manage_extensions`` and click on update
content. It's the first thing you need to do before trying to access
any Silva interface or content. Don't click on refresh all first.

