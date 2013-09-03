.. _silva-high-availability-installation:

Silva High-Availability Installation
====================================

For sites with lots of traffic, only one Zope server to handle all the
incoming requests might not be enough. You have to setup a cluster in
this case. The cluster will be composed of a database server, multiple
Zope servers using this database and of a load balancer that
distributes the incoming traffic among the different Zope servers.

Each of those components can be installed on separate servers, or on
the same.

Depending on your requirements, you can configure the database server
in two different ways:

.. toctree::
   :maxdepth: 2

   zeo
   relstorage


Moreover, if you have more than one Zope server using the same database,
you need to install and configure ``memcached`` for Silva:

.. toctree::
   :maxdepth: 2

   memcached


As a load balancer in front, you can use:

- Apache `mod_proxy_balancer`_,

- `Pound`_,

- `Squid`_,

- `NGinx`_ and `uWSGI`_.

- An hardware solution (usually expensive).


In case of large cluster, you can configure an upload server for files:

.. toctree::
   :maxdepth: 2

   uploading


Note about the date and time configuration
------------------------------------------

If you have multiple physical or virtual servers inside your cluster,
we recommand you to properly configure each server so their time
settings is properly synchronized with a common source. You can
enforce it on Unix by configuring an NTP server with
``ntpdate``. Failing to do so might creates problems with the default
authentication method used in Silva.

.. _mod_proxy_balancer: http://httpd.apache.org/docs/2.2/mod/mod_proxy_balancer.html
.. _Squid: http://www.squid-cache.org/
.. _Pound: http://www.apsis.ch/pound/
.. _NGinx: http://nginx.org/
.. _uWSGI: http://projects.unbit.it/uwsgi/
