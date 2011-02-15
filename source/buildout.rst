
Installing Silva with Buildout
==============================

.. glossary::

   *Buildout*
     Buildout is a tool used to automate software
     deployment. Installing and upgrading Silva using buildout can
     greatly reduce the chances of errors and the time needed.

Requirements
------------

Please refer to the section regarding your system. **You cannot
install properly Silva if you didn't installed the requirements
before**.

.. toctree::
   :maxdepth: 2

   buildout/requirements

Installing Silva for a quick drive with Paster and ZopeSkel
-----------------------------------------------------------

If you want to take Silva for a quick test drive you can install Silva
using `Paster`_ and `ZopeSkel`_. To set up a production or development
environment we recommend you do a SVN check out of the Buildout
located in the Infrae `SVN
<https://svn.infrae.com/buildout/silva/>`_. This will let you upgrade
your Silva instance more easily at a later date.

.. glossary::

  *Paster*
     `Paster`_ is a multi-purpose tool. One use is to create a project
     skeleton out of a configurable template. Paster helps you to get
     started by creating all the boilerplate requirements for your
     project.

  *ZopeSkel*
     `ZopeSkel`_ is a set of templates for `Paster`_ which are Python,
     Zope, Plone and Silva related.

.. _installing-paster:

Installing Paster and ZopeSkel
``````````````````````````````

First install Python Setuptools which is needed to install
`ZopeSkel`_. On UNIX systems, this can be installed through your OS
package manager, for example on Ubuntu / Debian systems, run:

.. code-block:: sh

  $ sudo apt-get install python-setuptools

Under Mac OS X, you can use MacPorts:

.. code-block:: sh

  $ sudo port install py-setuptools

Otherwise you can install setuptools manually by downloading the
``ez_setup`` script from
http://peak.telecommunity.com/dist/ez_setup.py and then running it
with the python version you want to use. For example under Unix:

.. code-block:: sh

   $ cd /tmp
   $ wget http://peak.telecommunity.com/dist/ez_setup.py
   $ sudo python2.4 ez_setup.py

Now that we have the setuptools installed, we can use
``easy_install``. Just run the following command:

.. code-block:: sh

   $ sudo easy_install-2.4 -U ZopeSkel

If you are using Mac OS X, we recommend to use the option ``-s``. By
default ``easy_install`` puts all scripts in the same directory as
Python, which is hidden inside a framework on Mac OS X. By specifying
``-s /opt/local/bin`` installed scripts are going to be installed in
the same directory than Mac Ports softwares:

.. code-block:: sh

   $ sudo easy_install-2.4 -s /opt/local/bin -U ZopeSkel

.. note::

    This install can take a while. ``easy_install`` will download some
    python packages from external software repositories like
    `sourceforge.net <http://sourceforge.net/>`_. It could be that
    one of these servers is temporarily unavailable. In that case you
    will get a connection timeout error, but you can try it again,
    until the command succeeds.

Creating a Silva Buildout tree
``````````````````````````````

The ZopeSkel package installed a script called ``paster`` on your
system. This can be used to install several CMS products including
Silva. First we'll test if the Silva templates are present by running:

.. code-block:: sh

   $ paster create --list-templates

Paster will now print a list of templates, this list should contain an
entry called ``silva_buildout``.

Now we will create a Silva environment with paster.  First go to a
directory where you want to install Silva, then run the paster script
with the following arguments:

.. code-block:: sh

   $ paster create -t silva_buildout

Paster will now ask you the following questions:

``Enter project name``

   This is the name of your project, a directory with this name will
   be created to install the Silva instance in.

``Enter zope2_install``

   Path to an existing Zope 2 setup, leave this blank to download and
   create new one.

``Enter silva_distribution``

   Choose to install stable version of Silva.

``Enter zope_user``

   The name of the user to access and administrate the site.

``Enter zope_password``

   A password for the user mentioned above. You must fill in something
   here, otherwise you will not be able to log in.

``Enter http_port``

   HTTP Port number on which the server will run.

``Enter debug_mode``

   Debug mode is useful for tracking down problems when
   developing. For a simple test deployment we recommend to turn it
   off.

``Enter verbose_security``

  Again, really only useful in a development context.

After replying to these questions, paster will generate a Silva
environment in the ``project name`` directory.

Running the installation
````````````````````````

Now that we have a Silva project environment, we can run the Buildout
script to automaticly pull in the Silva code and configure it.

.. code-block:: sh

   $ cd 'project name'
   $ python2.4 bootstrap.py
   $ ./bin/buildout

After this the command ``bin/instance`` will be available. It will let
you start and use Silva: :ref:`starting-creating-silva-site`.

Installing Silva from SVN for development or production
-------------------------------------------------------

For production deployment or development we recommend you use a SVN
checkout of the Infrae buildout for Silva.

You are going to need to install subversion if it's not already
installed on your system.

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

All buildout trees work the same way.

.. note::

   Pay close attention to how your SVN is configured. Especially the
   ``global-ignores`` section of you SVN ``config`` file. For more
   information see :ref:`svn-configuration`.

Getting your Buildout tree
``````````````````````````

Run the following command to fetch files from SVN:

.. code-block:: sh

   $ svn co https://svn.infrae.com/buildout/silva/tag/Silva-2.2 Silva

You can consult the ``README.txt`` file in the newly created ``Silva``
directory which contains additional information for the specific Silva
version.

The ``Silva`` directory will be referenced as your Buildout tree from
now on.

Running the installation
````````````````````````

First you need to *bootstrap* your Buildout directory, and create a
configuration file. You can create a new configuration as an extension
of one of the available profiles located in the ``profiles``
sub-directory of your Buildout tree. Below we will use the
``simple-instance.cfg`` profile:

.. code-block:: sh

   $ python2.4 bootstrap.py --buildout-profile profiles/simple-instance.cfg

Running this command creates important files, most importantly one
called ``buildout.cfg``. The ``buildout.cfg`` file is a default
Buildout configuration file, it controls what you want install, and
how. To extend your buildout see
:ref:`extending-and-customising-your-installation`.

After generating the buildout files run the installation:

.. code-block:: sh

   $ ./bin/buildout

Now the command ``bin/instance`` will be available. It will let
you start and use Silva: :ref:`starting-creating-silva-site`.

Available profiles
~~~~~~~~~~~~~~~~~~

The following profiles are available in the ``profiles`` directory:

``development.cfg``

   Setup Silva in development mode. Debug options are activated by
   default, and some extra development tools are installed.

``simple-instance.cfg``

   Recommanded profiles for a production instance.

``zeo-instance.cfg``

   Base profile for a ZEO setup. This installs a ZEO server and a ZEO
   client instead of a simple instance. Refer to :ref:`zeo-setup` for
   more details about that profile.

Extending your installation
```````````````````````````

You can read more about the following sections to customize your
setup:

.. toctree::
   :maxdepth: 2

   buildout/extending

Upgrading your setup
````````````````````

The generic procedure to upgrade a Silva site is:

1. to stop your Zope instance,

2. change the version of Silva you are running on the file-system,

3. re-run buildout to get the new version,

4. verify the :ref:`upgrade-notes` for the version you are upgrading
   and follow them,

5. if there was no upgrade notes, restart your Zope instance, visit
   each Silva Root you have and access the *service extensions* in ZMI
   (*Zope Management Interface*)
   (``silvaroot-url/service_extensions/manage_extensions``), click on
   the button upgrade content.

You cannot upgrade only one instance of Silva inside a Zope instance
to a specific version. All Silva sites in a Zope instance run the same
version of Silva (which the one installed on the file-system).

Downgrade of versions is not supported. After you upgraded a Silva
site, the only way to get back to the old version you were running is
to restore a backup.

To change the version of Silva your are running on the file-system:

- if you have an SVN checkout, you can switch Silva versions by
  running ``svn switch``, for instance to upgrade to the version
  ``2.2.1``:

   .. code-block:: sh

      $ cd Silva
      $ ./bin/instance stop
      $ svn switch https://svn.infrae.com/buildout/silva/tag/Silva-2.2.1
      $ ./bin/buildout

- if you made your own buildout profile that refers directly to the
  Silva Buildout configuration using buildout's ``extends`` option be
  sure to update the URL used.

.. warning::

   If you are using any specific extra and/or custom extensions with
   Silva, you should check that they are compatible with the version
   of Silva you want to upgrade to. Failing to test these extensions
   may break your site and cause the upgrade to fail.

As standard upgrade procedure, we recommend to first copy your site as
a test site, and then do the upgrade procedure on the copy. After
checking that everything on your site works, you can do the same
procedure on your production site. This is especially recommended for
upgrades that jump between versions, and for major release of Silva.

.. note::

   Please refer to the specific upgrade notes for the version you are
   upgrading to.

.. toctree::
   :maxdepth: 2

   buildout/upgrade


.. warning::

   It's recommended to backup of your data before attempting any
   upgrade operation.



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

   Contains all generated scripts to use installed software. This
   should contain start/stop scripts for your Zope/ZEO
   instance/server, maildrophost server.

``parts``

   Contains software components. **This directory could be deleted**
   when you re-run buildout to upgrade your tree. You should not make
   any changes inside it.

``products``

   Directory that can contains additional Zope products to be used.

``profiles``

   Contains default configuration profiles, that can be used or
   extended.

``var``

   Contains all data created and used by the
   software. ``var/filestorage`` contains the Zope database. **This is
   the directory you want to backup** (for backing up your website).

   ``var/log`` contains all the logs generated by Zope and Silva. You
   might want to install a tool like `logrotate
   <http://linuxcommand.org/man_pages/logrotate8.html>`_ in order to
   rotate them to control their sizes.

.. _configuring-a-cache-for-buildout:

Configuring a cache
```````````````````

Buildout downloads software in the ``eggs`` and ``download`` directory
by default. When you use buildout multiple times on the same host it's
best to share those directories between other buildout trees, this
prevents downloading duplicate packages and centralizes your packages.

In a network setup, it is even possible to share the ``download``
directory among different hosts using NFS or something similar.

To create a cache you need to create a ``.buildout`` directory in your
home directory, which contains a ``default.cfg`` file. The content of
this file is:

.. code-block:: ini

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

Please refer first to those sections of this document in case of any
problems or questions while running Buildout or see the link below.

.. toctree::
   :maxdepth: 2

   buildout/troubleshooting

.. _Paster: http://pythonpaste.org/
.. _ZopeSkel: http://pypi.python.org/pypi/ZopeSkel

