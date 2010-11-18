.. _memcached-setup:

Configure a cache using memcached
=================================

Silva can use `memcached`_ as cache backend. We recommend to use it as
soon as you have more than one Zope instance to host the same Silva site.

You need of course first to install memcached, either on your system,
or with the help of buildout.

Installation memcached using buildout
-------------------------------------

You can download and install `memcached`_ using a the buildout recipe
`hexagonit.recipe.cmmi`_. In your buildout you can add the following
sections:

.. code-block:: ini

   [libevent]
   recipe = hexagonit.recipe.cmmi
   url = http://www.monkey.org/~provos/libevent-2.0.7-rc.tar.gz

   [memcached]
   recipe = hexagonit.recipe.cmmi
   url = http://memcached.googlecode.com/files/memcached-1.4.5.tar.gz
   configure-options = --with-libevent=${libevent:location}

   [memcached-script]
   recipe = collective.recipe.template
   input = ${buildout:directory}/local-templates/memcached-ctl.in
   output = ${buildout:bin-directory}/memcached
   mode = 0755

The last section ``memcached-script`` will create a script to start
and stop the `memcached`_ server using the template located in the
``local-templates`` of your buildout installation.


XXX

Installation a memcached client library
---------------------------------------

We recommend to use the client library ``pylibmc``. Other are
supported, but are slower and have bugs. This client library uses the
C library ``libmemached`` that you need to install as well.

In your buildout you can add the following sections:

.. code-block:: ini


  [libmemcached]
  recipe = hexagonit.recipe.cmmi
  url = http://launchpad.net/libmemcached/1.0/0.40/+download/libmemcached-0.40.tar.gz
  md5sum = 3566611b0cff70cfde3979a95f62fe85
  configure-options = --with-libevent=${libevent:location} --without-memcached

  [pylibmc]
  recipe = zc.recipe.egg:custom
  eggs = pylibmc
  include-dirs = ${libmemcached:location}/include
  library-dirs = ${libmemcached:location}/lib
  rpath = ${libmemcached:location}/lib


After you need in your instance section to add ``pylibmc`` to the list
of dependencies to see it included in Zope:

.. code-block:: ini

  [instance]
  eggs +=
     ${pylibmc:eggs}


Configuring Silva to use your memcached server
----------------------------------------------

XXX


.. _memcached: http://www.memcached.org
.. _hexagonit.recipe.cmmi: http://pypi.python.org/pypi/hexagonit.recipe.cmmi
