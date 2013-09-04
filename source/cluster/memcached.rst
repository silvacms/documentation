.. _memcached-setup:

Configuring a cache using memcached
===================================

Silva can use `memcached`_ as cache backend. It is required to use it
as soon as you have more than one Silva servers hosting the same Silva
site (meaning you use :term:`ZEO` or RelStorage).

.. contents::

Installation of  memcached
--------------------------

We recommand to install memcache using your system package manager,
and not use Buildout in order to do it.


Configure Silva to use your memcached server
--------------------------------------------

To configure Silva to use your `memcached`_ server, you need to add to
your buildout configuration:

.. code-block:: buildout
   :linenos:

   [instance]
   memcache-address = localhost:11211
   zope-conf-additional =
     <product-config silva.core.cache>
       memcache ${instance:memcache-address}
       default.type ext:memcached
       default.lock_dir ${buildout:directory}/var/cache/lock/default
       default.url ${instance:memcache-address}
       auth.type ext:memcached
       auth.lock_dir ${buildout:directory}/var/cache/lock/auth
       auth.url ${instance:memcache-address}
     </product-config>


Line 2 defines for convenience an option ``memcache-address``. It is
reused it after in the product configuration for `silva.core.cache`_
from line 4 to 11.


.. _memcached: http://www.memcached.org
.. _silva.core.cache: http://infrae.com/download/silva_all/silva.core.cache
