Store Zope database into a SQL server with RelStorage
=====================================================

`Relstorage`_ let Zope stores its data into a SQL server.

Multiple Zope instance can connect simultaneously to the same SQL
database to use the same data, making possible it to distribute the
requests among those multiple Zope instance. This creates an
installation equivalent to one using ZEO, without ZEO.

If your SQL server support fail-over, you can have a full fail-over
installation.

Currently, the following SQL servers are supported:

- MySQL,

- PostGreSQL,

- Oracle.

We don't recommand to store the blobs inside the SQL server, but to
continue to store them on the filesystem, inside a directory that is
shared among the various servers you have, if you have more than one.

.. warning::

   Those data are not exploitable by other system than Zope, and
   should not be touched by anything else than Zope.


.. warning::

   If you wish to use ZEO *and* RelStorage, the ZEO server should be
   the only client connected the SQL server. But ZEO is useless in
   this case.


Installation with buildout
--------------------------

We need to modify the ``instance`` section in buildout to use
`RelStorage`_:

.. code-block:: buildout
   :linenos:

   [instance]
   eggs +=
     RelStorage
   rel-storage =
     type mysql
     host localhost
     db zodb
     user root
     passwd admin
     blob-dir ${buildout:directory}/var/files
     cache-local-mb 100


- The line 5 configures the type of SQL database to
  use. ``postgresql`` and ``oracle`` are valid options. Line 44 to 47
  defines how to connect to the MySQL server. Options to connect to
  PostGreSQl and Oracle are different, refer to the documentation of
  `RelStorage`_ for those.

- Line 10 specifies the directory to store Blobs files. *All* Zope
  servers connected to the SQL database should share the *same* Blobs
  directory. If they are located on different physical servers, you
  can share the directory using NFS for instance.

- Line 11 define a in memory cache. Don't abuse of this option,
  install `memcached`_ instead.


Using memcached to improve performances
---------------------------------------

`RelStorage`_ can use a `memcached`_ server to improve its own
performances.

You will need at first to install `memcached`_ and add a memcache
client library to your instance dependencies. You can refer to
:ref:`memcached-setup` for it.

After in your buildout configuration, you need to add the following
configuration to the relstorage section:


.. code-block:: buildout
   :linenos:

   [instance]
   rel-storage =
      # normal rel-storage options
      cache-module-name relstorage.pylibmc_wrapper
      cache-servers localhost:11211

On line 4, we configure RelStorage to use `pylibmc`_, the memcached
client library we installed. On line 5, we specify the address of the
memcached server.


.. warning::

    We recommend to use a different memcache instance than the one used
    for Silva to cache RelStorage data. *All* Zope server connected
    to the same SQL database should share the *same* memcached
    server.


.. _RelStorage: http://pypi.python.org/pypi/RelStorage
.. _memcached: http://www.memcached.org
.. _pylibmc: http://pypi.python.org/pypi/pylibmc/1.1.1
