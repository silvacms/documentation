.. _extending-and-customising-your-installation:

Extending and customising your installation
===========================================

Add additional configuration information in the ``buildout.cfg``
file.

Buildout configuration file format
----------------------------------

The ``buildout.cfg`` file follows a format like Windows INI files,
using 'sections'. For example a part looks like this:

.. code-block:: ini

   [buildout]
   ...

Each section is responsible for the installing a part of the software,
usually in the ``parts`` directory of the buildout. For example a
``[zope2]`` section takes care of installing Zope 2. An ``[instance]``
section takes care of creating a Zope instance. See the example taken
from ``profiles/base.cfg``:

.. code-block:: ini

   [buildout]
   ...

   [zope2]
   ...

   [instance]
   ...

   ...

Within each sections are *options*. Options define what gets installed
and how. The *recipe* option of the section define a piece of code
that will be responsible to install the part of the software described
in the section. Those pieces of code are called *recipes* and are
usually packaged in standalone extensions. You can find lot of
buildout recipes on the `PyPi`_ repository.

.. code-block:: ini

   [zope2]
   recipe = plone.recipe.zope2install
   ...

.. note::

   The ``[buildout]`` section is used as an aggregator to combine all the
   other sections, it should never be assigned a ``recipe`` option.

A configuration file can be extended by another configuration file,
modifying its behavior. We use it to implement Buildout profiles:
different typical default configuration. Example of profiles can be a
configuration for production, a configuration for development with
more development tools and debug mode activated, a configuration to
test such or such extension.

The ``buildout.cfg`` file extends the profile development.cfg located
in the ``profiles`` directory.

.. code-block:: ini

   [buildout]
   extends = profile/development.cfg

The most important  sections defined in the Silva Buildout is:

``instance``
   Your Zope 2 instance. You may want to add/customize settings
   here. All available options are referenced `on the zope2instance
   recipe description page
   <http://pypi.python.org/pypi/plone.recipe.zope2instance>`_.

To extend or modify options of a section when you extend a buildout
configuration file, add the option to the correct section. For
instance to change the port number of your zope instance to 8086, you
can add this to your configuration file ``buildout.cfg``:

.. code-block:: ini

  [instance]
  http-address = 8086

Some configuration options accept more than one value. In those case
they are mentioned as one value per line. You can extend existing
options using the ``+=`` operator instead of ``=`` to add new values,
or ``-=`` to remove existing ones.

.. note::

   To re-create your environment you just need to keep your
   ``buildout.cfg`` file. You can do a Subversion checkout of a new
   Buildout tree, put your ``buildout.cfg`` in that directory, run
   ``python2.6 bootstrap.py`` and after ``./bin/buildout`` to
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

Others recipes can be used to install software differently. To find
more recipes, search them on the `PyPi`_.

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


.. _PyPi: http://pypi.python.org/pypi
