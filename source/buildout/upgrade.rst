
.. _upgrade-notes:

Upgrade notes
=============

Upgrades notes for specific Silva versions

.. contents::

.. warning::

   On large databases, lot of temporary files might be created. If you
   ``/tmp`` directory is too small you will get an error. You can
   specify an another directory to create temporary files with the
   ``TMPDIR`` environment variable


Silva 3.0 upgrade notes
-----------------------

You need to upgrade you installation using buildout. After running
buildout, you have to run ``zodbupdate`` on every database you
have. If the version of those databases is prior to Silva 2.3, you
have to specify the flag ``-p Python``, otherwise:

.. code-block:: sh

   $ ./bin/zodbupdate -f var/filestorage/Data.fs

After this first step, you must start Zope, and find every codesource
in Silva, and fix them. *Silva will not work and should not used
before the upgrade is over, or this may cause permanent looses of
data*. The ``service_codesource`` will help you to locate the code
sources and their problems. For this, you can access it, and click on
the button *Find* on the service it will visit the Zope database to
find code sources in it, and the problems associated to them. In the
code source parameters form, you must pay attention not to change the
parameters name, or the values associated to them that are stored in
the Silva Document will be lost.

When this is over, you can either start the upgrade process for each
Silva instance located in your Zope instance via the
``service_extensions``, or stop your Zope instance and use the
``bin/silva`` script. We recommend to use the script if you have a
large site.

For instance if you a Silva instance located at ``/mysite`` in your
Zope instance, you can use the script like this:

.. code-block:: sh

   $ ./bin/silva -c parts/instance/etc/zope.conf upgrade /mysite


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

