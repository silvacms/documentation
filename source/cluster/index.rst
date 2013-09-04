.. _silva-high-availability-installation:

Silva High-Availability Installation
====================================

For sites with lots of traffic, only one Silva server, with one
processus might not be enough to handle all the incoming requests. You
need to install a cluster in this case. The cluster will be composed
of at least a database server, multiple Silva servers (with multiple
processus) using this database, a load balancer or an HTTP frontend
server or even an HTTP cache proxy that distributes the incoming traffic
among the different Silva servers.

Each of those components can be installed on different hardware
servers, or on the same, depending on the expected load, the hardware
and the degree of failover you wish to attain.

The configuration of multiple Silva server and the various existing
possibilities to distribute requests on them is described in
:ref:`presenting-your-silva-site-to-the-world`.

Depending on your requirements, you can configure the database server
in two different ways:

.. toctree::
   :maxdepth: 2

   zeo
   relstorage


Moreover, if you have more than one Silva processus using the same
database, you need to install and configure ``memcached`` for Silva:

.. toctree::
   :maxdepth: 2

   memcached


In case of large cluster, you can configure an upload server for files:

.. toctree::
   :maxdepth: 2

   uploading

If you send lot of emails from Silva (notifications and such), we
recommend you to use MaildropHost. This can be installed with the help
of the recipe `infrae.maildrophost`_ in :term:`Buildout` (see
:ref:`extending-and-customising-your-installation` for more
information). MaildropHost works with the help of an additional
processus to actually send the emails. If you have multiple Silva
servers on the same physical server, only one MaildropHost processus
is needed for the physical server.

Note about the date and time configuration
------------------------------------------

If you have multiple physical or virtual servers inside your cluster,
we recommend you to properly configure each server so their time
settings is properly synchronized with a common source. You can
enforce it on Unix by configuring an NTP server with
``ntpdate``. Failing to do so might creates problems with the default
authentication method used in Silva.

.. _infrae.maildrophost: https://pypi.python.org/pypi/infrae.maildrophost
