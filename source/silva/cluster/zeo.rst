
.. _zeo-setup:

Zope Enterprise Object Setup
============================

Zope Enterprise Object, or ZEO is a database server that can be used
to share the same Zope database between different Zope servers. The
database server is often called ZEO server, and the regular Zope
servers connected to it ZEO clients.

The ZEO clients connects to the ZEO server using a regular network
connection, so the clients can be on the same *or* on different
physical servers than the server.

You can define a ZEO-setup with the help of Buildout.

.. contents::

.. _zeo-server-installation:

ZEO server installation
-----------------------

In the ``profiles`` sub-directory of your Buildout tree is defined a
``zeo-instance.cfg`` profile. It defines a new part, called
``zeoserver``. This will be the ZEO server. It is created with the
help of the `zodbrecipes recipe`_. After running Buildout
this will create a script called ``bin/zeoserver`` which controls your
ZEO server. By default it listen to port 8100, and store its database
into the directory ``var/filestorage``, like for a regular Zope
instance.

For instance, if you wish to setup a ZEO server on a different port,
8888 you can extend the profile like this:

.. code-block:: buildout

   [buildout]
   extends = profiles/zeo-instance.cfg
   parts =
      zeoserver

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

Like for the :ref:zeo-server-installation, you can extend the
``zeo-instance.cfg`` Buildout profile, and reuse the ``instance``
section defined there.

The ``zeo-address`` of the ``instance`` will determine to which ZEO
server the Zope instance connects to. If your setup is distributed
across different computers, you will have to specify the address of
the ZEO server using this option, the format being ``hostname:port``.

.. warning::

   If you use Blobs, the blob directory **must** be accessible from all
   the Zope instance at the same path. On Unix, you can share it to
   the computer running the Zope instances using NFS for instance.


For instance, if you want create a Zope instance that connect to the
ZEO server running on a computer called ``webdb`` in the network, on
port 8888 you can extend the ``zeo-instance.cfg`` Buildout profile:

.. code-block:: buildout

   [buildout]
   extends = profiles/zeo-instance.cfg
   parts =
     instance

   [instance]
   zeo-address = webdb:8888


Multiple ZEO clients in one Buildout
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can within the same buildout install multiple Zope instances
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

On line 3 to 7, you indicate you want to install the
``client1``. ``client2``, ``client3`` and ``client4`` sections. On
line 10, you specify the address of the ZEO server.

Line 13 is a special syntax of Buildout that says that the current
section (``client1``) reuse all the options of the ``instance``
section. This let you define from line 12 to 26 all the client
sections mentioned by the ``part`` option of the ``buildout`` section,
changing each time the ``http-address`` option.

Of course you can use this technique to add as many clients you like
in a Buildout configuration file.

.. note::

   For questions about Buildout configuration, please refer to
   :ref:`extending-and-customising-your-installation`.


.. _zodbrecipes recipe: http://pypi.python.org/pypi/zc.zodbrecipes
