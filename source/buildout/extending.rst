.. _extending-and-customising-your-installation:

Extending and customising your installation with Buildout
=========================================================

You can edit the ``buildout.cfg`` configuration file in order to
install more Silva extensions in your installation, or deactivate
default ones. Alternatively, you can create your own base
configuration file to use with ``buildout.cfg``.

.. contents::

Buildout configuration file format
----------------------------------

The ``buildout.cfg`` configuration file follows a format like Windows
INI files, using *sections*. For example a section looks like this:

.. code-block:: buildout

   [buildout]
   # options ...

Each section is responsible for installing a part of the software,
usually in the ``parts`` directory of the buildout. For example an
``instance`` section takes care of creating a Zope instance
(server). See the example taken from ``profiles/base.cfg``:

.. code-block:: buildout
   :linenos:

   [buildout]
   # options ...

   [instance]
   recipe = some.recipe
   long_option = this is a
      multi
      line
      option
   replaced_option = this options uses ${zope2:name}.
   # options ...
   # other options ...

Within each sections are *options*. Options are parameters to
configure what gets installed and how. They can be defined on one or
multiple lines (see line 11 to 14) in order to define a value or
multiple ones. They can reuse values of other options, located in the
same or a different section (see line 15).

The *recipe* option of the section define a piece of code that will be
responsible for installing the part of the software, using the options
of the section. Those pieces of code are called *recipes* and are
usually packaged in standalone Python extensions. You can find lots of
buildout recipes on the `PyPi`_ repository.

.. code-block:: buildout

   [zope2]
   recipe = plone.recipe.zope2instance

.. note::

   The ``buildout`` section is used to set Buildout general
   options. It doesn't use a ``recipe`` option, and it doesn't install
   any software part.

Buildout will install the software parts of the sections mentioned in
the ``parts`` option of the section ``buildout``, in the order they
are mentioned:

.. code-block:: buildout
   :linenos:

   [buildout]
   parts =
      products
      instance

   [products]
   # options

   [instance]
   # options

Line 2 to 4 will trigger the installation of the ``products`` section
and after the ``instance`` section.


Extending a buildout configuration file
---------------------------------------

An existing Buildout configuration file can be extended by another
one, modifying its behavior. It can be seen as including an another
configuration file. It is accomplished by specifying an existing local
or remote configuration file with the help of the ``extends`` option
of the ``buildout`` section:

.. code-block:: buildout

   [buildout]
   extends = profiles/development.cfg


If the path is not absolute, it is interpreted from the directory
containing the configuration file that used the option ``extends``
(i.e. a file located in a directory called ``profiles`` using
``extends`` with the filename ``base.cfg`` will open the file
``profiles/base.cfg``). Remote files can be used with the help of an
HTTP URL.

This feature is used to implement Buildout profiles: generic
configurations file that are meant to be extended for a specific
usage. An example can be to extend a base configuration file
installing a list of Silva extension you wish to use with:

- different settings on your Zope instance for production,

- extra development tools and debug mode activated on your Zope
  instance for development,

- extra configuration to test Silva extensions.

Using this feature, you can create your own Buildout configuration
file that extends one of Silva's. This way, you can have a Silva
installation with exactly what you need:

.. code-block:: buildout
   :linenos:

   [buildout]
   extends = profiles/simple-instance.cfg

   [instance]
   http-address = 9000
   debug-mode = false

Here you change the value of the ``http-address`` option of the
``instance`` section to 9000 on line 5, and set the previously not set
option ``debug-mode`` to false on line 6.

You have the possibility to redefine an existing list of options
(option on multiple lines) with ``+=`` and ``-=`` in order to add or
remove options (lines) specified in the parent configuration file:

.. code-block:: buildout
   :linenos:

   [buildout]
   extends = profiles/base.cfg

   [instance]
   products -=
      ${buildout:directory}/products
   eggs +=
      silvatheme.multiflex


On line 6 we remove the directory ``${buildout:directory}/products``
from the list of products. On line 8 we add the egg
``silvatheme.multiflex`` to the existing list of extensions to
install.

.. note::

   To re-create your Silva installation you just need to keep your
   Buildout configuration file. You can do a Subversion checkout of a
   new Silva Buildout, put your ``buildout.cfg`` in that directory,
   run ``python2.7 bootstrap.py`` and after ``./bin/buildout`` to
   re-create exactly the same installation.


Default Silva Buildout configuration files
------------------------------------------

A number of buildout configuration provided with Silva can be extended:

- ``profiles/base.cfg``: base configuration for all Silva installation,

- ``profiles/simple-instance.cfg``: base configuration for production. This
  install a simple Zope instance with Silva in production mode.

- ``profiles/development.cfg``: base configuration for development. Debug mode
  is activated, some extra debugging tools are installed.

Each of those configuration defines a section ``instance`` that will
be responsible for creating a Zope instance with Silva. Modifying
options in this section will affect your Zope and Silva installation.

Adding new software to your setup
---------------------------------

You can add packaged software to your setup which can come from either
a tarball or a website, a Subversion server, or a Python egg.

You can also install software from other Version Control Systems, not
just Subversion. This is not covered by this documentation.

* Software packaged as a tarball:

  To add software packaged as a tarball, add a ``distros-extra``
  section to the ``buildout.cfg`` and use the `distros recipe
  <http://pypi.python.org/pypi/plone.recipe.distros>`_.

  For example to install `PASRaduis
  <http://www.zope.org/Members/shimizukawa/PASRadius>`_:

  .. code-block:: buildout

     [distros-extra]
     recipe = plone.recipe.distros
     urls =
         http://www.zope.org/Members/shimizukawa/PASRadius/PASRadius-0.2/PASRadius-0.2.tgz

     [instance]
     products +=
         ${distros-extra:location}

  You can use a list of URLs on multiple lines to install multiples products.

* Software coming from a Subversion repository:

  Just like for a tarball-distributed package, add a new part:
  ``svn-extra`` using the `subversion recipe
  <http://pypi.python.org/pypi/infrae.subversion>`_ and refer it to
  our instance.

  Here we use the SilvaMailing product trunk as an example:

  .. code-block:: buildout

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
     practice is to use a *tag* version of the software, to be sure to
     have a repeatable Buildout configuration

* Software packaged as a Python egg:

  Simply reference the packages in your ``instance`` section, and it
  will be downloaded from the `PyPi`_ or the `Infrae package index`_ and
  installed:

  .. code-block:: buildout

     [instance]
     eggs +=
         silva.app.base
     zcml +=
         silva.app.base

  The ``eggs`` option adds the packages to the Zope environment. If
  those packages needs to have ZCML configuration files to be loaded,
  the ``zcml`` option let Zope load theirs configuration.

* Software packaged as a Python egg located on your computer:

  You can use the option `develop` of the `buildout` section to tell
  buildout that your software is already on your computer. For
  instance, if you have two extension in the ``src`` directory of your
  Buildout:

  .. code-block:: buildout

     [buildout]
     develop +=
         src/silvatheme.mycompany
         src/silva.app.mycompany

     [instance]
     eggs +=
         silvatheme.mycompany
         silva.app.mycompany
     zcml +=
         silvatheme.mycompany
         silva.app.mycompany

  This will look for an extension in those directories, and use them
  if they are required.

Others recipes can be used to install software differently. To find
more recipes, search them on the `PyPi`_.

Example
```````

Here is a full example of a buidout configuration with new
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

.. code-block:: buildout

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
options to the ``instance`` part. Most popular settings are:

``http-address``
   Address or port the instance should listen to.

You can have a complete listing of available options on the
`zope2instance recipe`_ description page.

.. warning:: If you use a WSGI server, not all server configuration
  settings defined by the `zope2instance recipe`_ will work, as those
  settings apply to the Zope server which is not use in that kind of
  setup. However, any setting related to ZODB configuration for example
  stays valid.

.. _PyPi: http://pypi.python.org/pypi
.. _zope2instance recipe: http://pypi.python.org/pypi/plone.recipe.zope2instance
.. _Infrae package index: http://infrae.com/download/simple
