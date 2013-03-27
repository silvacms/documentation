
.. _zeo-setup:

Zope Enterprise Object Setup
============================

.. glossary::

   *ZEO*
     Zope Enterprise Object, or ZEO is a database server that can be
     used to share the same Zope database between different Zope
     servers. The database server is often called ZEO server, and the
     regular Zope servers connected to it ZEO clients.


.. contents::


ZEO concepts
------------

The ZEO clients connects to the ZEO server to access the database
using a regular network connection. The ZEO clients can be installed
on the same *or* on different physical servers than the ZEO server.

You can define a ZEO-setup with the help of Buildout.


.. _zeo-server-installation:

ZEO server installation
-----------------------

In the ``profiles`` sub-directory of your Buildout directory is
defined a ``zeo-instance.cfg`` configuraiton file. It defines a new
section, called ``zeoserver``. This will install a ZEO server. It is
created with the help of the `zodbrecipes recipe`_. After running
Buildout this will create a script called ``bin/zeoserver`` which
controls your ZEO server. By default it listen to port 8100, and store
its database into the directory ``var/filestorage``, like for a
regular Zope instance.

Starting and stopping the ZEO server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can start the ZEO server with the newly generated script:

.. code-block:: sh

   $ bin/zeoserver start

You can stop the ZEO server with the same script:

.. code-block:: sh

   $ bin/zeoserver stop


Changing the ZEO server configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For instance, if you wish to setup a ZEO server on a different port,
8888 you can extend the profile like this:

.. code-block:: buildout

   [buildout]
   extends = profiles/zeo-instance.cfg

   [zeoserver]
   zeo-address = 8888

.. warning::

   You cannot use at the same time a Zope instance and a ZEO server
   using the same database if the Zope instance is not a client of the
   ZEO server.

.. note::

   For questions about Buildout configuration, please refer to
   :ref:`extending-and-customising-your-installation`.


ZEO client installation
-----------------------

Like for the :ref:`zeo-server-installation`, you can extend the
``zeo-instance.cfg`` Buildout profile, and reuse the ``instance``
section defined there.

The ``zeo-address`` of the ``instance`` will determine to which ZEO
server the Zope instance connects to. If your setup is distributed
across different severs, you will have to specify the address of
the ZEO server using this option, the format being ``hostname:port``.

For instance, if you want create a ZEO client that connect to the ZEO
server running on a server called ``webdb`` in the network, on port
8888 you can extend the ``zeo-instance.cfg`` Buildout configuration
like this to configure it:

.. code-block:: buildout

   [buildout]
   extends = profiles/zeo-instance.cfg

   [instance]
   zeo-address = webdb:8888


.. warning::

   If you use Blobs, the blob directory **must** be accessible from
   all the ZEO clients under **the same path**. On Unix, you can share
   it with the servers running the ZEO clients using NFS for instance.


Multiple ZEO clients in one Buildout
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can within the same buildout install multiple ZEO clients that
connected to the same ZEO server. To do so, you can use a special
syntax of buildout:

.. code-block:: buildout
   :linenos:

   [buildout]
   extends = profiles/zeo-instance.cfg
   parts =
     client1
     client2
     client3
     client4

   [instance]
   zeo-address = webdb:8888

   [client1]
   <= instance
   http-address = 8081

   [client2]
   <= instance
   http-address = 8082

   [client3]
   <= instance
   http-address = 8083

   [client4]
   <= instance
   http-address = 8084

Line 3 to 7 indicates you want to install the
``client1``. ``client2``, ``client3`` and ``client4`` sections.  Line
10 specifies the address of the ZEO server.

Line 13 is a special syntax of Buildout that makes the current
section (``client1``) reuse all the options of the ``instance``
section. This let you define from line 12 to 26 all the client
sections mentioned by the ``part`` option of the ``buildout`` section,
changing each time the ``http-address`` option.

Of course you can use this technique to add as many clients you like
in a Buildout configuration file.

.. note::

   For questions about Buildout configuration, please refer to
   :ref:`extending-and-customising-your-installation`.

.. note::

   It is not needed to create multiple ZEO clients this if you use
   ``mod_wsgi`` or ``uWSGI`` (:ref:`mod-wsgi-configuration`).

.. _zodbrecipes recipe: http://pypi.python.org/pypi/zc.zodbrecipes
