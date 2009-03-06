Extending and customing your installation
=========================================

You can add additional configuration information in the
``buildout.cfg`` file directly. This file follows a format like
Windows INI files, with sections which install parts of the software,
and options in those sections which control how these software parts
are installed.

Default parts defined in the Silva are:

``zope2``

   Zope 2 installation. You may not want to change that part, as it's
   rarely needed for Silva. Available options are defined `on the
   zope2install recipe description page
   <http://pypi.python.org/pypi/plone.recipe.zope2install>`_.

``silva-all``

   All Silva software, coming from the Infrae SVN repository.

``instance``

   Your Zope 2 instance. You may want to add/customize settings
   here. All available options are referenced `on the zope2instance
   recipe description page
   <http://pypi.python.org/pypi/plone.recipe.zope2instance>`_.


To extend or modify settings in a part, you just add it to your
configuration file, ``buildout.cfg``. For instance to change the port
number of your zope instance to 8086, add this:

.. code-block:: ini

  [instance]
  http-address = 8086


Some configuration options accept more than one value. In that case
they are mentioned as one value per line. You can extend existing
options using the ``+=`` operator instead of ``=`` to add new values,
or ``-=`` to remove existing ones.

.. note::

   To re-create your environment you just need to keep your
   ``buildout.cfg`` file. You can do a SVN checkout of a new Buildout
   tree, put your ``buildout.cfg`` in that directory, run ``python2.4
   bootstrap.py`` and after ``./bin/buildout`` to re-create exactly
   the same environment.

Adding new software to your setup
---------------------------------

* Software packaged as a tarball:

  We can add a new part to install software packaged as a tarball,
  using the `distros recipe
  <http://pypi.python.org/pypi/plone.recipe.distros>`_ and refer it to
  our instance.

  So for instance to install `PASRaduis
  <http://www.zope.org/Members/shimizukawa/PASRadius>`_:

  .. code-block:: ini

     [distros-extra]
     recipe = plone.recipe.distros
     urls =
         http://www.zope.org/Members/shimizukawa/PASRadius/PASRadius-0.2/PASRadius-0.2.tgz

     [instance]
     products +=
         ${distros-extra:location}

  You can use more than one URL of course.

* Software coming from a SVN repository:

  Like for tarball-distributed software, we are going to add a new
  part using the `subversion recipe
  <http://pypi.python.org/pypi/infrae.subversion>`_ and refer it to
  our instance.

  We take here the example of the trunk of the SilvaMailing product:

  .. code-block:: ini

     [svn-extra]
     recipe = infrae.subversion
     urls =
         https://svn.infrae.com/SilvaMailing/trunk SilvaMailing

     [instance]
     products +=
         ${svn-extra:location}

  Like for tarball-distribution, you can refer more than one SVN URL.

  .. note::

     We don't recommend to *trunk* version of any SVN repository if you
     want to setup an instance for production, but *tag*.

* Software packaged as a Python egg:

  You simply reference them in your ``instance`` section:

  .. code-block:: ini

     [instance]
     eggs +=
         silva.app.base
     zcml +=
         silva.app.base

  The ``eggs`` directive adds it to the Zope environment, the
  ``zcml`` lets Zope load its Zope 3 configuration.

* Software not packaged, being a Zope product:

  You just drop them in the sub-directory ``products`` of your Buildout tree.


Example
```````

Here, a full example of a configuration with new software. We dropped
``ZMysqlDA`` in the ``products`` folder of the Buildout tree, and add
SilvaMailing product from SVN, Raduis authentication with PAS. We
install ``MySQL-python`` as a dependency for ``ZMysqlDA``, and
MaildropHost with the help of the `maildrophost recipe
<http://pypi.python.org/pypi/infrae.maildrophost>`_ to send mail.

.. code-block:: ini

  [buildout]
  extends = profiles/simple-instance.cfg

  [svn-extra]
  recipe = infrae.subversion
  urls =
      https://svn.infrae.com/SilvaMailing/trunk SilvaMailing

  [distro-extra]
  recipe = plone.recipe.distros
  urls =
      http://www.zope.org/Members/shimizukawa/PASRadius/PASRadius-0.2/PASRadius-0.2.tgz

  [maildrophost]
  recipe = infrae.maildrophost
  smtp_host = localhost
  smtp_port = 25
  url =
      http://www.dataflake.org/software/maildrophost/maildrophost_1.20/MaildropHost-1.20.tgz

  [instance]
  http-address = 8090
  eggs +=
      MySQL-python
      silva.pas.base
  zcml +=
      silva.pas.base
  products +=
      ${svn-extra:location}
      ${distro-extra:location}
      ${maildrophost:location}


The ``maildrophost`` part will install and configure MaildropHost, and
create a ``bin/maildrophost`` script to start/stop the MaildropHost
daemon.

.. _zeo-setup:

ZEO Setup
---------

You can define a ZEO-setup with the help of Buildout. Since it's going
to reliably reproduce the exact same setup, you will be sure that all
your ZEO node run exactly the same software releases/products.

In the ``profiles`` sub-directory of your Buildout tree is defined a
``zeo-instance.cfg`` profile. You can extend that one instead of
``simple-instance.cfg``. It defines a new part, called ``zeoserver``
which will be the ZEO server. It's created with the help of the
`zope2zeoserver recipe
<http://pypi.python.org/pypi/plone.recipe.zope2zeoserver>`_. This will
create a script called ``bin/zeoserver`` which controls your ZEO
server. By default it listen on the port 8100 of the computer.

Your ZEO setup can be distributed on more than one computer, so in
fact we are going to build a profile for your setup which can be
extended again locally on each computer to select only what you want
to run.

After extending the ZEO configuration like explained, you add all the
desired options in your ``buildout.cfg`` file, like for a normal Zope
instance (add reference to new Products, Python extensions and so
on). Rename it to the name you want, it will be your base profile to
re-use:

.. code-block:: sh

   $ mv buildout.cfg mycorp.cfg
   $ python2.4 bootstrap.py --buildout-profile mycorp.cfg

.. note::

   You need to keep this new profile file with your ``buildout.cfg``
   to be able to re-create your environment. It is a good idea to back
   it up somewhere 'off server' and possible to keep it under version
   control, to be able to go back to an earlier set-up.

Now, it's going to be slightly more complicated. We want to have more
than one instance with the same configuration, so more than one part
with the same options, but we don't want to copy them more than once,
to prevent synchronization errors between them. So our ``instance``
section will become our configuration, and we are going to use the
`macro recipe <http://pypi.python.org/pypi/zc.recipe.macro>`_ to
create several Zope instances with the same configuration.

For the moment, we need to use a development version of this
recipe. In you profile file ``mycorp.cfg``, add the options to the
``buildout`` section:

.. code-block:: ini

   [buildout]
   extensions = gp.svndevelop>0.3
   develop-dir = src
   svn-extend-develop =
      svn://svn.zope.org/repos/main/zc.recipe.macro/branches/infrae-force-recipe#egg=zc.recipe.macro

After, we are going to say that ``instance`` is just used as
configuration entry in our profile, and define 6 Zope instances, with
special settings for each of them.

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

And now we generate a part for each Zope instance, always in the same
profile file:

.. code-block:: ini

   [zeoclients]
   recipe = zc.recipe.macro
   macro = instance
   result-recipe = plone.recipe.zope2instance
   force-recipe = true
   targets =
      client1:client1-conf
      client2:client2-conf
      client3:client3-conf
      client4:client4-conf
      client5:client5-conf
      client6:client6-conf

We can now use our profile. Your ``buildout.cfg`` file will be for
your ZEO server, with two ZEO clients:

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

We say here we want to install Zope 2, Silva, a ZEO server, create ZEO
clients configuration and setup two Zope instances ``client1``, and
``client2``.

On an other computer, we can run four ZEO clients connected on the ZEO
server located on the computer called ``zeoserver.mycorp`` in the DNS:

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
