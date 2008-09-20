
Installing Silva with Buildout
==============================

Buildout is a tool used to automate software installation. Installing
Silva using buildout will greatly reduce the chance of errors and the
time needed.

Requirements
------------

Installation under Linux Ubuntu/Debian
``````````````````````````````````````

1. Python 2.4

   Silva 2.x needs Python 2.4 to work, this is probably already
   present on your system. If not, you can compile it yourself or
   install it with through the package manager of your operating
   system. For example, on Ubuntu/Debian systems, type:

   .. code-block:: sh
 
      $ sudo apt-get install python2.4 

2. Python 2.4 headers and development add-ons

   On UNIX systems,  the buildout will also need the python header
   files. If you compiled python by hand, these files are already on your
   system, otherwise you can install them with the package manager of your 
   choice.

   f.e. on Ubuntu and Debian systems, run:

   .. code-block:: sh

      $ sudo apt-get install python2.4-dev build-essential

   You will also need a working C compiler (gcc), which is what the 
   ``build-essential`` package will install.

   Another set of headers that need to be present is the zlib compression
   library (``zlib.h``). To install this on Ubuntu / Debian run:

   .. code-block:: sh

	  $ sudo apt-get install zlib1g-dev 

   Silva uses PIL, the Python Imaging Library when using images.  You
   might want to make sure that you have the ``libjpeg``
   (``jpeglib.h``) library installed otherwise, PIL cannot use jpeg
   images.  On Ubuntu / Debian systems, run:

   .. code-block:: sh

	  $ sudo apt-get install libjpeg62-dev 

   Silva also uses libXML2 and libXSLT, so you need to install them as
   well:

   .. code-block:: sh
  
      $ sudo apt-get install libxml2-dev libxslt1-dev


Installation under Mac OS X
```````````````````````````

Silva 2.x needs Python 2.4 to work. You need first to install `XCode
<http://developer.apple.com/tools/xcode/>`_ in order to have a working
compiler.

After that, you can install `MacPorts <http://www.macports.org/>`_ to
install Python and other required tools. Don't forget to update your
``~/.bashrc`` file with:

.. code-block:: sh

   export PATH=/opt/local/bin:$PATH

Now, you can open a Terminal to install Python and required
components:

.. code-block:: sh

   $ sudo port -v install python24

You can install the ``libjpeg``:

.. code-block:: sh

   $ sudo port -v install jpeg

And ``libxml2``/``libxslt``:

.. code-block:: sh

   $ sudo port -v install libxml2
   $ sudo port -v install libxslt

Installation under FreeBSD
``````````````````````````

Silva 2.x needs Python 2.4 to work, this is probably already present
on your system. You can install it using the FreeBSD ports.

.. note::

   If the FreeBSD ports are not already installed on your system, you
   can install them using the ``sysintall`` command, in the
   *Configure* menu, select *Distributions* and them select the
   ``ports`` distributions, press tab to go on ``Okay`` and enter.

Installing Python 2.4:

.. code-block:: sh

   $ cd /usr/ports/lang/python24
   $ make install
   $ make distclean

You will need as well the ``libjpeg``:

.. code-block:: sh

   $ cd /usr/ports/graphics/jpeg
   $ make install
   $ make distclean

And ``libxml2`` and ``libxslt``:

.. code-block:: sh

  $ cd /usr/ports/textproc/libxslt
  $ make install
  $ make distclean
  

Installation under Windows 
``````````````````````````

We don't recommand (and support) Windows as a production environment.

1. First you need to install `Python 2.4
   <http://www.python.org/ftp/python/2.4.4/python-2.4.4.msi>`_.

   .. warning::

      Don't install Python in a directory so it will contain spaces in
      its path when you are going to call the binary afterwards. Some
      Python programs might have problems with that.

      The default installation path is perfect.

  After, right-click on *My Computer* on your desktop, and select
  *Manage*. Click on the *Advanced* tab, and click on the button
  *Environment variable*. Here you select *Path*, and click on
  modify. You append your path to your Python binary here, so
  ``C:\Python24`` for the default installation path.

  Now if you start a shell (click on *Start*, *Run*, type ``cmd``
  and enter), you should be able to run ``python`` directly in it.

2. We need to have a working compiler as well. So we are going to
   install MinGW. Download and run the installer from `Sourceforge
   <https://sourceforge.net/project/showfiles.php?group_id=2435&package_id=240780>`_.
   
   In the installer, select at least the minimal distribution, with
   the C++ compiler and the make utility. Like for Python, don't
   select an installation path with spaces, the default one is the
   perfect.

   Like you did for Python, just add your installation path plus
   ``/bin`` (i.e. ``C:\MinGW\bin`` for the default installation path)
   to your path environment variable. You should be able to type
   ``gcc`` in a newly created shell.

   In your Python installation path plus ``\Lib\distutils`` (so
   ``C:\Python24\Lib\distutils`` for the default installation path)
   create a file called ``distutils.cfg`` which contains:

   .. code-block:: ini

      [build]
      compiler=mingw32

   This will tells Python to use MinGW to compile needed extensions.

3. We need at last Subversion. You can download and install it from
   the `Slik distribution page <http://www.sliksvn.com/en/download>`_.

   After, you should be able to type ``svn help`` in a newly created
   shell.

.. warning::

   It's recommanded to work in directories which don't have any spaces
   in their paths. When you will be asked to checkout files from SVN
   to create your buildout directory, keep this in mind (or you will
   have problems).

.. note::

   Windows don't use the same separator in paths, so when you will be
   asked to type ``bin/buildout`` in your shell, type ``bin\buildout``
   instead.


Installing Silva to test it with Paster
---------------------------------------

You can install Silva using Paster if you want to take it for a quick
test drive. If you want to set up a production or development
environment we recommend you do a SVN check out of the buildout
located in the Infrae SVN. This will let you upgrade your Silva
instance more easily at a later date.

Installing Paster
`````````````````

You need to install Python Setuptools which is needed to install
ZopeSkel. On UNIX systems, this can be installed by your OS package
manager, for example on Ubuntu / Debian systems, run:

.. code-block:: sh

  $ sudo apt-get install python-setuptools

Under Mac OS X, you can use MacPorts:

.. code-block:: sh

  $ sudo port install py-setuptools

Otherwise you can install setuptools by hand by downloading the
``ez_setup`` script from
http://peak.telecommunity.com/dist/ez_setup.py and then running it
with the python that you want to use, for example under Unix:

.. code-block:: sh
        
   $ cd /tmp
   $ wget http://peak.telecommunity.com/dist/ez_setup.py
   $ sudo python2.4 ez_setup.py


Now that we have the setuptools installed, we can use ``easy_install``
to install ZopeSkel. This scripts have been installed in the same
directory than your Python directory. This should be very easy. Just
run the following command:

.. code-block:: sh

   $ sudo easy_install-2.4 -U ZopeSkel

If you are under Mac OS X, we recommand to use the option ``-s``. By
default ``easy_install`` put all scripts in the same directory than
Python, which is hided inside a framework on Mac OS X. By specifying
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

   The name of the initial user that will be created to access and
   administrate the site.


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


After this the command ``bin/instance`` will be available which will
start your Zope instance, after which you can manually create a Silva
Root inside it.


Installing Silva from SVN for development or production
-------------------------------------------------------

For production deployment or development we recommend you use a SVN
checkout of the Infrae buildout for Silva.

You are going to need to install subversion if it's not already
installed on your system.

There are different buildout SVN trees for the different Silva
versions. A SVN tag corresponds to a specific release of Silva, and is
located in::

  https://svn.infrae.com/buildout/silva/tag

We highly recommend to use tags for production website.

The development branches of main version are located in::

  https://svn.infrae.com/buildout/silva/branch 

and (for the main or newest development branch)::

  https://svn.infrae.com/buildout/silva/trunk

All buildout trees work the same way.

Getting your Buildout tree
``````````````````````````

Run the following command to fetch files from SVN:

.. code-block:: sh

   $ svn co https://svn.infrae.com/buildout/silva/tag/Silva-2.1 Silva

You can consult the ``README.txt`` file in the newly created ``Silva``
directory which can contain additional information for the specific
version.

The ``Silva`` directory will be referenced as your Buildout tree from
now on.

Running the installation
````````````````````````

You need first to *bootstrap* your Buildout directory, and create a
configuration file. You can create a new configuration as an extension
of one of the available profiles located in the ``profiles``
sub-directory of your Buildout tree. Here we are going to use the
``simple-instance`` profile:

.. code-block:: sh

   $ python2.4 bootstrap.py --buildout-profile profiles/simple-instance.cfg

This will create some files, most importantly one called
``buildout.cfg`` in your Buildout tree. It's going to be your
configuration file to control what you want installed, and how. Refer
to the next documentation part to learn how to extend it.

After this you can run the installation:

.. code-block:: sh

   $ ./bin/buildout

After this a script ``bin/instance`` will be available that can be
used to start your Zope instance, which would then be available on
port 8080. The default administrator user name is ``admin`` and the
corresponding password ``admin``. Of course we highly recommend that
you change the password in the Zope Management Interface.

Extending your installation
```````````````````````````

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
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

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
.......

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
  recipe = infrae.subersion
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

ZEO Setup
~~~~~~~~~

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

Upgrading your setup
````````````````````

In your SVN checkout of your buildout tree, just switch to a newer
tag, and re-run buildout:

.. code-block:: sh

   $ cd Silva
   $ ./bin/instance stop
   $ svn switch https://svn.infrae.com/buildout/silva/tags/Silva-2.1.1b1
   $ ./bin/buildout 
   $ ./bin/instance start

.. note::

   It's recommended to do a backup of your data before.

And that's it!


Using Buildout
--------------

If you changed your configuration files, or updated your buildout
tree, you just need to re-run buildout:

.. code-block:: sh

   $ ./bin/buildout

We  highly recommend  to  stop your  Zope  instance(s) before  running
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

   Contains default configurations profiles, that can
   be used or extended again.

``var``

   Contains all data used by the software. ``var/filestorage``
   contains for instance the Zope database.

Troubleshooting
```````````````
