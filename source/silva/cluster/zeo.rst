
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

ZEO server installation
-----------------------

XXX

In the ``profiles`` sub-directory of your Buildout tree is defined a
``zeo-instance.cfg`` profile. It defines a new part, called
``[zeoserver]``. This will be the ZEO server. It's created with the
help of the `zodbrecipes recipe
<http://pypi.python.org/pypi/zc.zodbrecipes>`_. After running buildout
this will create a script called ``bin/zeoserver`` which controls your
ZEO server. By default it listen to port 8100.


ZEO client installation
-----------------------

XXX

Your ZEO setup can be distributed on more than one computer, so in
fact we are going to build a profile for your setup which can then be
extended locally for each computer to select only what you want to
run.

After extending the ZEO configuration like explained, you add all the
desired options in your ``buildout.cfg`` file, like for a normal Zope
instance (add a reference to new Products, Python extensions and so
on). Rename it to the name you want, it will be your base profile to
re-use:

.. code-block:: sh

   $ mv buildout.cfg mycorp.cfg
   $ python2.6 bootstrap.py --buildout-profile mycorp.cfg

.. note::

   You need to keep this new profile file with your ``buildout.cfg``
   to be able to re-create your environment. It is a good idea to back
   up the file somewhere 'off server' and if possible to keep it under
   version control, to be able to go back to earlier set ups.

Now, it's going to be slightly more complicated. We want to have more
than one instance with the same configuration, so more than one part
with the same options, but we don't want to copy them more than once,
in order to prevent synchronization errors between them. Our
``instance`` section will become our configuration, and we are going
to use the `macro recipe
<http://pypi.python.org/pypi/zc.recipe.macro>`_ to create several Zope
instances with the same configuration.

We are going to say that ``instance`` is just used as a configuration
entry in our profile, and define 6 Zope instances, with special
settings for each of them.

.. code-block:: ini

   [instance]
   recipe = zc.recipe.macro:empty
   http-address = $${:port}

   [client1-conf]
   port = 8080

   [client2-conf]
   port = 8082

   [client3-conf]
   port = 8084

   [client4-conf]
   port = 8086

   [client5-conf]
   port = 8088

   [client6-conf]
   port = 8090

Now we make a part for each Zope instance, always in the same profile
file:

.. code-block:: ini

   [zeoclients]
   recipe = zc.recipe.macro
   macro = instance
   result-recipe = plone.recipe.zope2instance
   targets =
      client1:client1-conf
      client2:client2-conf
      client3:client3-conf
      client4:client4-conf
      client5:client5-conf
      client6:client6-conf

We can now use the profile. Use the ``buildout.cfg`` file to control
the creation of the Zeo server and clients. The following example
creates a Zeo server with 2 clients.

.. code-block:: ini

   [buildout]
   extends = mycorp.cfg
   parts =
       zope2
       silva-all
       zeoserver
       zeoclients
       client1
       client2

Again, here we state that we want to install Zope 2, Silva, a ZEO
server, create ZEO clients and setup two Zope instances ``client1``,
and ``client2``.

On another computer, using another ``buildout.cfg`` we can run four
ZEO clients connected on the ZEO server located on the computer called
``zeoserver.mycorp`` in the DNS:

.. code-block:: ini

   [buildout]
   extends = mycorp.cfg
   parts =
        zope2
        silva-all
        zeoclients
        client1
        client2
        client3
        client4

   [instance]
   zeo-address = zeoserver.mycorp:8100
