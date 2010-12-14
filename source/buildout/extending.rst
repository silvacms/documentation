.. _extending-and-customising-your-installation:

Extending and customising your installation
===========================================

Add additional configuration information in the ``buildout.cfg``
file.

Buildout configuration file format
----------------------------------

The ``buildout.cfg`` file follows a format like Windows INI files,
using 'sections', sections are also called 'parts' (from this point
forward I will refer to sections as parts). For example a part looks
like this:

.. code-block:: ini

   [buildout]
   ...

Each part is responsible for the installing only that part of the
software. For example a ``[zope2]`` part takes care of installing
Zope. An ``[instance]`` part takes care of creating a Zope
instance. See the example taken from ``profiles/base.cfg``:

.. code-block:: ini

   [buildout]
   ...

   [zope2]
   ...

   [instance]
   ...

   ...

Within each part are *options*. Options define what gets installed
into a part. This is governed by the *recipe* option.

.. code-block:: ini

   [zope2]
   recipe = plone.recipe.zope2install
   ...

.. note::

   The ``[buildout]`` part is used as an aggregator to combine all the
   other sections, it should never be assigned a ``recipe`` option.

A configuration file can be extended by another configuration file,
modifying its behavior. That's how profiles are implemented. The
``buildout.cfg`` file extends the profile development.cfg located in
the ``profiles`` directory.

.. code-block:: ini

   [buildout]
   extends = profile/development.cfg

Default parts defined in the Silva Buildout are:

``zope2``
   Zope 2 installation. You may not want to change that part, as it's
   required for Silva. Available options are defined `on the
   zope2install recipe description page
   <http://pypi.python.org/pypi/plone.recipe.zope2install>`_.

``silva-all``
   All Silva software, coming from the Infrae SVN repository.

``instance``
   Your Zope 2 instance. You may want to add/customize settings
   here. All available options are referenced `on the zope2instance
   recipe description page
   <http://pypi.python.org/pypi/plone.recipe.zope2instance>`_.

To extend or modify options in a part, add the option to the correct
part. For instance to change the port number of your zope instance to
8086, you can add this to your configuration file:

.. code-block:: ini

  [instance]
  http-address = 8086

Some configuration options accept more than one value. In that case
they are mentioned as one value per line. You can extend existing
options using the ``+=`` operator instead of ``=`` to add new values,
or ``-=`` to remove existing ones.

.. note::

   To re-create your environment you just need to keep your
   ``buildout.cfg`` file. You can do a Subversion checkout of a new
   Buildout tree, put your ``buildout.cfg`` in that directory, run
   ``python2.4 bootstrap.py`` and after ``./bin/buildout`` to
   re-create exactly the same environment.

Adding new softwares to your setup
----------------------------------

You can add packaged software to your setup which can come from either
a tarball on a website, an Subversion server, or a Python egg.

You can also install software from other Version Control Systems, not
just Subversion. This is not covered by this documentation.

* Software packaged as a tarball:

  To add software packaged as a tarball, add a ``[distros-extra]``
  part to the ``buildout.cfg`` and use the `distros recipe
  <http://pypi.python.org/pypi/plone.recipe.distros>`_.

  For example to install `PASRaduis
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

* Software coming from a Subversion repository:

  Just like for a tarball-distributed package, add a new part:
  ``[svn-extra]`` using the `subversion recipe
  <http://pypi.python.org/pypi/infrae.subversion>`_ and refer it to
  our instance.

  Here we use the SilvaMailing product trunk as an example:

  .. code-block:: ini

     [svn-extra]
     recipe = infrae.subversion
     urls =
         https://svn.infrae.com/SilvaMailing/trunk SilvaMailing

     [instance]
     products +=
         ${svn-extra:location}

  Also just like for tarball-distributions, you can refer more than
  one SVN URL.

  .. note::

     We recommend not to use a *trunk* version of any software SVN
     repository if you want to setup a production instance. Best
     practice is to use a *tag* version of the software.

* Software packaged as a Python egg:

  Simply reference the packages in your ``instance`` section:

  .. code-block:: ini

     [instance]
     eggs +=
         silva.app.base
     zcml +=
         silva.app.base

  The ``eggs`` directive adds it to the Zope environment, the
  ``zcml`` lets Zope load its Zope 3 configuration.

* Software not packaged, being a Zope product:

  You just drop them in the sub-directory ``products`` of your
  Buildout tree.

Others recipes can be used to install software differently. To find
more recipes, search `PyPi
<http://pypi.python.org/pypi?:action=browse&show=all&c=512>`_.

Example
```````

Here, is a full example of a buidout configuration with new
software. Not handled by the buildout file we put the `ZMysqlDA
<http://www.zope.org/Members/adustman/Products/ZMySQLDA>`_ adapter
into the ``products`` folder of the Buildout tree.

In the configuration file we SVN checkout the SilvaMailing product
using the `infrae subversion
<http://pypi.python.org/pypi/infrae.subversion>`_ recipe, we download
the Radius authentication with PAS tarball using the `distros
<http://pypi.python.org/pypi/plone.recipe.distros>`_ recipe, and
MaildropHost tarball using `infrae maildrophost
<http://pypi.python.org/pypi/infrae.maildrophost>`_ recipe. We also
get the MySQL-python and silva.pas.base eggs.

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

Changing your Zope instance settings
------------------------------------

You can change a couple of settings in the Zope instance, by adding
options to the ``[instance]`` part. Most popular settings are:

``http-address``
   Address/Port the instance should listen to.

``effective-user``
   Which user Zope should try to become if it's started as root.

``debug-mode``
   Toggle the debug mode on or off.

You can have a complete listing of available options `on the
zope2instance recipe description page
<http://pypi.python.org/pypi/plone.recipe.zope2instance>`_.

.. _zeo-setup:

ZEO Setup
---------

You can define a ZEO-setup with the help of Buildout. Since it's going
to reliably reproduce the exact same setup, you will be sure that all
your ZEO nodes run exactly the same software releases/products.

In the ``profiles`` sub-directory of your Buildout tree is defined a
``zeo-instance.cfg`` profile. It defines a new part, called
``[zeoserver]``. This will be the ZEO server. It's created with the
help of the `zope2zeoserver recipe
<http://pypi.python.org/pypi/plone.recipe.zope2zeoserver>`_. After
running buildout this will create a script called ``bin/zeoserver``
which controls your ZEO server. By default it listen to port 8100.

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
   $ python2.4 bootstrap.py --buildout-profile mycorp.cfg

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
