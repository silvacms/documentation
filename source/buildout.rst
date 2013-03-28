
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

   All the requirements are required. If they are not installed, you
   won't be able to install Silva correctly.

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

1. Make sure that any extra and/or custom extensions you are using
   with Silva works with the new version you are upgrading to. Failing
   to test this before the migration might break your Silva site and
   make the upgrade fail.

   If you are a developer, you might want to consult the API changes
   notes:

   .. toctree::
      :maxdepth: 2

      changes/index

2. Stop your Zope instance:

   .. code-block:: sh

      $ cd Silva
      $ ./bin/paster serve deploy.ini stop

3. Do a backup of your whole Buildout directory. Doing this will
   permit you to restore your Silva site in case of problems,

4. Update the version of Silva you are running on the file-system:

   - if you have an SVN checkout, you can switch to the new version by
     running ``svn switch`` in your Buildout directory. For instance
     to upgrade to the version ``2.3.1``:

      .. code-block:: sh

         $ cd Silva
         $ svn switch https://svn.infrae.com/buildout/silva/tag/Silva-2.3.1

   - if you made your own buildout configuration that refers to the
     Silva Buildout configuration using buildout's ``extends`` option,
     you need update this URL.

5. Re-run buildout to get the new version:

   .. code-block:: sh

      $ cd Silva
      $ bin/buildout -v

6. Verify the :ref:`upgrade-notes`. If a note is available for the
   version you are upgrading to, you need to follow them:

   .. toctree::
      :maxdepth: 2

      buildout/upgrade

7. If there is no upgrade note, restart your Zope instance, visit each
   Silva Root you have and access the *service extensions* in ZMI
   (*Zope Management Interface*)
   (``silvaroot-url/service_extensions/manage_extensions``), click on
   the button upgrade content.

.. warning::

   You cannot upgrade only one instance of Silva inside a Zope
   instance to a specific version. All Silva sites in a Zope instance
   run the same version of Silva (which is the one installed on the
   file-system).

Downgrade of versions is not supported. After you upgrade a Silva
site, the only way to get back to the old version you were running is
to restore a backup.

We recommend to first copy your site in a test installation and apply
the upgrade procedure on this installation. After testing that
everything on your site works, you can apply the same procedure on
your production site. This is especially recommended for upgrades that
jump between major versions of Silva.


Using Buildout
--------------

If you changed your configuration files or updated your buildout
directory from SVN, you need to re-run buildout and restart your
instance to apply the changes:

.. code-block:: sh

   $ cd Silva
   $ ./bin/paster serve deploy.ini stop
   $ ./bin/buildout
   $ ./bin/paster serve deploy.ini start

.. note::

   We recommend stopping your Zope instance(s) before running
   buildout.

What's in the buildout directory
````````````````````````````````

A Buildout directory contains the following sub-directories:

``bin``

   Contains all generated scripts for using the installed software. This
   should contain start/stop scripts for your Zope/ZEO
   instance/server, maildrophost server.

``parts``

   Contains software components. When you re-run buildout to upgrade
   your installation **this directory might be deleted**. You should
   not make any changes inside it.

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

Buildout downloads and installs software inside the ``download`` and
``eggs`` directories by default. When you have multiple Buildout
directories on the same computer it is possible to share those
directories between all the Buildout installations, preventing to
download and install duplicate packages. This saves disk space and
time.

In a network setup, it is even possible to share the ``download``
directory among different computers using NFS or something similar.

To create those directories you need to create a ``.buildout``
directory in your home directory, which contains a ``default.cfg``
configuration file. The content of this file is:

.. code-block:: buildout

  [buildout]
  eggs-directory = /path/to/your/cache/eggs
  download-cache = /path/to/your/cache/download
  download-directory = /path/to/your/cache/download

In this example, a directory called ``cache`` located inside the
``/path/to/your`` directory is used. You need to create inside it the
sub directories ``eggs`` and ``download`` in advanced.


Troubleshooting
```````````````

In case of any problems or questions while running Buildout, please
refer to the link below before asking questions on the mailing-list.

.. toctree::
   :maxdepth: 2

   buildout/troubleshooting

