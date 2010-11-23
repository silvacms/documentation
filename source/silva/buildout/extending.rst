.. _extending-and-customising-your-installation:

Extending and customising your installation with Buildout
=========================================================

You can additional configuration information in the ``buildout.cfg``
file, or create your own default configuration to use in
``buildout.cfg``.

.. contents::

Buildout configuration file format
----------------------------------

The ``buildout.cfg`` file follows a format like Windows INI files,
using *sections*. For example a part looks like this:

.. code-block:: buildout

   [buildout]
   # options ...

Each section is responsible for the installing a part of the software,
usually in the ``parts`` directory of the buildout. For example a
``zope2`` section takes care of installing Zope 2. An ``instance``
section takes care of creating a Zope instance. See the example taken
from ``profiles/base.cfg``:

.. code-block:: buildout
   :linenos:

   [buildout]
   # options ...

   [zope2]
   recipe = other.recipe
   name = Zope 2
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

Within each sections are *options*. Options are parameters to help to
define what gets installed and how. They can be defined on multiple
lines (see line 11 to 14), and by so considered as a list of
options. They can reuse values of other options, located in a
different section (see line 15). In that case, the reference to the
option is just replaced by its value.

The *recipe* option of the section define a piece of code that will be
responsible to install the part of the software described in the
section. Those pieces of code are called *recipes* and are usually
packaged in standalone extensions. You can find lot of buildout
recipes on the `PyPi`_ repository.

.. code-block:: buildout

   [zope2]
   recipe = plone.recipe.zope2install

.. note::

   The ``buildout`` section is used to set Buildout settings. It
   doesn't use a ``recipe`` option, and it doesn't install any
   software part.

Buildout will install the software parts of the sections mentioned in
the ``parts`` option of the section ``buildout``, in the order they
are mentioned:

.. code-block:: buildout
   :linenos:

   [buildout]
   parts =
      zope2
      products
      instance

   [zope2]
   # options

   [products]
   # options

   [instance]
   # options

Line 2 to 5 will trigger the installation of the ``zope2`` section, then
of the ``products`` section and at last of the ``instance`` section.


Extending a buildout configuration file
---------------------------------------

A configuration file can be extended by another configuration file,
modifying its behavior. You can see it as well as including the
configuration file you are extending. This is done by specifying an
existing file using the ``extends`` option of the ``buildout``
section:

.. code-block:: buildout

   [buildout]
   extends = profile/development.cfg


If the path to include is not absolute, the relative position is
interpreted from the directory containing the file use the option
``extends`` (i.e. a file located in a directory called ``profiles``
using ``extends`` with the filename ``base.cfg`` will open the file
``profiles/base.cfg``). Remote HTTP URLs also do work as include path.

This feature is used to implement Buildout profiles : different
typical default configuration provided with Silva. Example of profiles
can be a configuration for production, one for development with more
development tools and debug mode activated, a configuration to test
some generic extension.

Using this feature, you can create your own buildout configuration
file that extends one of Silva. Like this, you can add or override
options defined in the configuration you extends:

.. code-block:: buildout
   :linenos:

   [buildout]
   extends = profiles/base.cfg

   [instance]
   http-address = 9000
   fast-mode = true

Here we change the value of the ``http-address`` option to 9000 on
line 5, and set the previously not set option ``fast-mode`` to true on
line 6.

If you redefine a list of options (option on multiple lines), you can
use ``+=`` and ``-=`` to add or remove options (lines) specified in
the base configuration:

.. code-block:: buildout
   :linenos:

   [buildout]
   extends = profiles/base.cfg

   [instance]
   products -=
      ${buildout:directory}/products
   eggs +=
      silvatheme.multiflex


On line 6 we remove the value ``${buildout:directory}/products`` from
the list of products. On line 8 we add the egg
``silvatheme.multiflex`` to the list already present of eggs to
install.

.. note::

   To re-create your environment you just need to keep your buildout
   configuration file. You can do a Subversion checkout of a new Silva
   buildout tree, put your ``buildout.cfg`` in that directory, run
   ``python2.6 bootstrap.py`` and after ``./bin/buildout`` to
   re-create exactly the same environment.


Default Silva buildout configuration files
------------------------------------------

A number of buildout configuration provided with Silva can be extended:

- ``profiles/base.cfg``: base configuration for all Silva installation,

- ``profiles/development.cfg``: base configuration for development. Debug mode
  is activated, some extra debugging tools are installed.

- ``profiles/simple-instance.cfg``: base configuration for production. This
  install a simple Zope instance with Silva in production mode.

Each of those configuration defines a section ``instance`` that will
be responsible to create the Zope instance with Silva. Changing
options in this section will affect your Zope and Silva installation.

Adding new softwares to your setup
----------------------------------

You can add packaged software to your setup which can come from either
a tarball on a website, an Subversion server, or a Python egg.

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
  settings described by the `zope2instance recipe`_ will work, as those
  settings applies to the Zope server which is not use in that kind of
  setup.

.. _PyPi: http://pypi.python.org/pypi
.. _zope2instance recipe: http://pypi.python.org/pypi/plone.recipe.zope2instance
.. _Infrae package index: http://infrae.com/download/simple
