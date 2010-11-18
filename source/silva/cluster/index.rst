Silva High-Availability Installation
====================================

For site with lot of traffic, having only Zope instance to handle all
the traffic might not be enough. You have to setup a cluster in this case.

Depending of your requirements, different installations are possible.

.. toctree::
   :maxdepth: 2

   zeo
   relstorage


Moreover, if you have more than one Zope instance using the database,
we recommend to install and configure ``memcached`` for Silva:

.. toctree::
   :maxdepth: 2

   memcached



