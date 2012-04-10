
.. _upgrade-notes:

Upgrade notes
=============

Upgrades notes for specific Silva versions

.. contents::


Silva 2.2 upgrade notes
-----------------------

Upgrade your Silva installation using buildout. After, *before*
starting your Zope instance, run ``zodbupdate`` on every database you have:

.. code-block:: sh

   $ ./bin/zodbupdate -f var/filestorage/Data.fs


Only after this step, you can start your Zope instance. Before using
your site, access, for each Silva Root, the URL
``silvaroot-url/service_extensions/manage_extensions`` and click on
the button update content.

Please type this last URL in your browser (you can’t get to it by
clicking around). *It is important to do it before using anything in
your upgraded Silva*. Don’t click on refresh all before.

Recompile your python scripts in the ZODB by accessing the URL
``zope-url/manage_addProduct/PythonScripts/recompile``.


Silva 2.3 upgrade notes
-----------------------

Like for 2.2, you need to upgrade your installation using
buildout. After running buildout, you have to run ``zodbupdate`` on every
database you have, with the flag ``-p Python``:

.. code-block:: sh

   $ ./bin/zodbupdate -p Python -f var/filestorage/Data.fs


After this, you can either start your Zope instance and access the
*service extensions* to upgrade your content there *or* directly use
the script ``silvaupdate``. **Do not start your Zope instance if you
wish to use that script**. We recommend the script if you have a large
site, as it might be more efficient.

.. code-block:: sh

   $ ./bin/silvaupdate -c parts/instance/etc/zope.conf /mysite

Where ``/mysite`` is the path inside Zope where your Silva Root
is. You can specify more than one Silva Root to the command line.

You can specify the ``--pack`` option if you wish to pack your
database after the upgrade.

After running ``silvaupdate`` you can start your Zope instance, and
access the URL ``zope-url/manage_addProduct/PythonScripts/recompile``.

You can upgrade directly from Silva 2.1 to Silva 2.3 following those
last notes.


.. warning::

   On large databases, lot of temporary files might be created. If you
   ``/tmp`` directory is too small you will get an error. You can
   specify an another directory to create temporary files with the
   ``TMPDIR`` environment variable


Silva 3.0 upgrade notes
-----------------------

TODO.
