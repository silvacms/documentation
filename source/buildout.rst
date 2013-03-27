
Installing Silva with Buildout
==============================

First some terminology about the tools used to install Silva.

.. glossary::

   *Buildout*
     Buildout is a tool used to automate software
     deployment. Installing and upgrading Silva using buildout will
     greatly reduce the chance of errors. You can find lots of
     information on the official site http://www.buildout.org/.

   *Setuptools*
     Setuptools is a Python extension to help packaging and
     distributing Python packages.


Requirements
------------

You need to install all the requirements corresponding to your
operating system:

.. toctree::
   :maxdepth: 2

   buildout/requirements

.. warning::

   These are all required. If they are not installed, you won't be
   able to install Silva.

Installing Silva for a quick drive
----------------------------------

In order to test Silva, we recommend you to install the Virtual Images
available on http://silvacms.org/.


Installing Silva from SVN for development or production
-------------------------------------------------------

For production deployment or development we recommend you to install
Silva by getting it from the Infrae subversion.

You need to install Subversion if it's not already installed on your
system.

There are different buildout SVN trees for different Silva
versions. An SVN tag corresponds to a specific release of Silva, the
``tag`` repository is located here::

  https://svn.infrae.com/buildout/silva/tag

We highly recommend to use tags for production websites.

The development branches of Silva versions are located in in the
``branch`` repository``::

  https://svn.infrae.com/buildout/silva/branch

For the latest development version of Silva go to the ``trunk``
repository::

  https://svn.infrae.com/buildout/silva/trunk

All buildout directories work the same way.

.. note::

   Pay close attention to how your SVN is configured. Especially the
   ``global-ignores`` section of you SVN configuration file. For more
   information see :ref:`svn-configuration`.

Getting your Buildout directory
```````````````````````````````

Run the following command to fetch files from SVN:

.. code-block:: sh

   $ svn co https://svn.infrae.com/buildout/silva/tag/Silva-3.0 Silva

You can consult the ``README.txt`` file in the newly created ``Silva``
directory which contains additional information for the specific Silva
version.

The Buildout directory will be referenced as the ``Silva`` directory
from now on.

Running the installation
````````````````````````

First you need to *bootstrap* your Buildout directory, and create a
configuration file. You can create a new configuration as an extension
of one of the available ones located in the ``profiles`` sub-directory
of your Buildout directory. Below we will use the
``simple-instance.cfg`` configuration:

.. code-block:: sh

   $ python2.7 bootstrap.py --buildout-profile profiles/simple-instance.cfg

Running this command creates required files to run the installation,
most importantly one called ``buildout.cfg``. It is the default
Buildout configuration file. It controls what you want installed, and
how. To extend this configuration, see
:ref:`extending-and-customising-your-installation`.

After generating the buildout files, run the installation:

.. code-block:: sh

   $ ./bin/buildout

When finished the command ``bin/paster`` will be available. It will let
you start and use Silva: :ref:`starting-creating-silva-site`.

Available configurations
~~~~~~~~~~~~~~~~~~~~~~~~

The following configurations are available in the ``profiles``
directory:


``simple-instance.cfg``

   Recommended profile for a production instance.

``zeo-instance.cfg``

   Base profile for a :term:`ZEO` setup. This installs a :term:`ZEO`
   server and a :term:`ZEO` client instead of a simple instance. Refer
   to :ref:`zeo-setup` for more details about that profile.

``development.cfg``

   Setup Silva in development mode. Debug options are activated by
   default, and some extra development tools are installed.

``silva-development.cfg``

   Install Silva from its latest source code. This configuration is
   intended to be used when you are either working on the Silva source
   code, or wish to get edge version.

.. note::

   A Buildout configuration that is extended might called a Buildout
   profile.

Extending your installation
---------------------------

You can read more about the following sections to customize your
setup:

.. toctree::
   :maxdepth: 2

   buildout/extending


Upgrading your installation
---------------------------

The generic procedure to upgrade a Silva site is:

1. stop your Zope instance,

2. do a backup of your whole Zope instance,

3. change the version of Silva you are running on the file-system (see
   below),

4. re-run buildout to get the new version,

5. verify the :ref:`upgrade-notes` for the version you are upgrading
   and follow them,

6. if there are no upgrade notes, restart your Zope instance, visit
   each Silva Root you have and access the *service extensions* in ZMI
   (*Zope Management Interface*)
   (``silvaroot-url/service_extensions/manage_extensions``), click on
   the button upgrade content.

You cannot upgrade only one instance of Silva inside a Zope instance
to a specific version. All Silva sites in a Zope instance run the same
version of Silva (which is the one installed on the file-system).

Downgrade of versions is not supported. After you upgrade a Silva
site, the only way to get back to the old version you were running is
to restore a backup.

To change the version of Silva your are running on the file-system:

- if you have an SVN checkout, you can switch Silva versions by
  running ``svn switch``, for instance to upgrade to the version
  ``2.3.1``:

   .. code-block:: sh

      $ cd Silva
      $ ./bin/paster serve deploy.ini stop
      $ svn switch https://svn.infrae.com/buildout/silva/tag/Silva-2.3.1
      $ ./bin/buildout

- if you made your own buildout configuration that refers directly to
  the Silva Buildout configuration using buildout's ``extends`` option
  be sure to update the URL used, and rerun buildout:

   .. code-block:: sh

      $ cd Silva
      $ ./bin/paster serve deploy.ini stop
      $ ./bin/buildout

.. warning::

   If you are using any specific extra and/or custom extensions with
   Silva, you should check that they are compatible with the version
   of Silva you want to upgrade to. Failing to test these extensions
   may break your site and cause the upgrade to fail.

We recommend to first copy your site as a test site, and then do the
upgrade procedure on the copy. After checking that everything on your
site works, you can do the same procedure on your production
site. This is especially recommended for upgrades that jump between
versions, and for major releases of Silva.

.. note::

   Please refer to the specific upgrade notes for the version you are
   upgrading to.

.. toctree::
   :maxdepth: 2

   buildout/upgrade


.. warning::

   It's recommended to backup your data before attempting any
   upgrade operations.

Using Buildout
--------------

If you changed your configuration files, or updated your buildout
tree, you need to re-run buildout:

.. code-block:: sh

   $ ./bin/buildout

.. note::

   We highly recommend stopping your Zope instance(s) before running
   buildout.

What's in the buildout directory
````````````````````````````````

A Buildout tree contains the following sub-directories:

``bin``

   Contains all generated scripts for using the installed software. This
   should contain start/stop scripts for your Zope/ZEO
   instance/server, maildrophost server.

``parts``

   Contains software components. When you re-run buildout to upgrade
   your tree **this directory might be deleted**. You should not make
   any changes inside it.

``profiles``

   Contains default configuration files, that can be directly used or
   extended.

``var``

   Contains all data created and used by the software. The
   ``var/filestorage`` directory contains the Zope database, and
   possibility the externally stored fieles. In order to back up
   your website, **this is the directory you want to backup**.

   ``var/log`` contains all the logs generated by Zope and Silva. You
   might want to install a tool like `logrotate
   <http://linuxcommand.org/man_pages/logrotate8.html>`_ in order to
   rotate them and control their sizes.

.. _configuring-a-cache-for-buildout:

Configuring a cache
```````````````````

Buildout downloads and install software in the ``download`` and
``eggs`` directories by default. When you have multiple Buildout
directories on the same computer it is possible to share those
directories between all the Buildout installation, preventing to
download and install duplicate packages.

In a network setup, it is even possible to share the ``download``
directory among different computers using NFS or something similar.

To create a central directory you need to create a ``.buildout``
directory in your home directory, which contains a ``default.cfg``
file. The content of this file is:

.. code-block:: buildout

  [buildout]
  eggs-directory = /path/to/your/cache/eggs
  download-cache = /path/to/your/cache/download
  download-directory = /path/to/your/cache/download

In this example I used a directory called ``cache`` located in
``/path/to/your`` directory. You will also see the directories
``eggs`` and ``download``. You need to create these directories in
your ``cache`` directory.

Troubleshooting
```````````````

Please refer first to those sections in case of any
problems or questions while running Buildout, or see the link below.

.. toctree::
   :maxdepth: 2

   buildout/troubleshooting

