Store Zope database into a SQL server with RelStorage
=====================================================

`Relstorage`_ let Zope store its data into a SQL server.

Multiple Zope instance can connect simultaneously to the same SQL
database to use the same data, making possible to distribute the
requests among those multiple Zope instance. This create an
installation equivalent to one using ZEO, without ZEO.

If your SQL server support fail-over, you can have a full fail-over installation.

Currently, the following SQL servers are supported:

- MySQL,

- PostGreSQL,

- Oracle.


.. warning:: Those data are not exploitable by other system than Zope,
   and should not be touched by anything else than Zope.


.. warning:: If you wish to use ZEO *and* RelStorage, the ZEO server
   should be the only client connected the SQL server.


Installation with buildout
--------------------------

XXX

Using memcached to improve performances
---------------------------------------

`RelStorage`_ can use a `memcached`_ server to improve its own
performances.

You will need at first to install `memcached`_ and add a memcache
client library to your instance dependencies. You can refer to
:ref:`memcached-setup` for it.

After in your buildout configuration, you need to add the following
configuration to the relstorage section:

.. code-block:: ini
   :linenos:

   [instance]
   rel-storage =
      [normal rel-storage options]
      cache-module-name relstorage.pylibmc_wrapper
      cache-servers localhost:11211

We recommend to use a different memcache instance than the one used
for Silva to cache `RelStorage`_ data.

XXX


.. _RelStorage: http://pypi.python.org/pypi/RelStorage
.. _memcached: http://www.memcached.org
