Requirements to install Silva
=============================

Installation under Linux Ubuntu/Debian
--------------------------------------

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
---------------------------

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
--------------------------

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
--------------------------

We don't recommand (and support) Windows as a production environment.

1. First you need to install `Python 2.4
   <http://www.python.org/ftp/python/2.4.4/python-2.4.4.msi>`_.

   .. warning::

      Don't install Python in a directory so it will contain spaces in
      its path when you are going to call the binary afterwards. Some
      Python programs might have problems with that. The default
      installation path is perfect.

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

3. We need Subversion. You can download and install it from the `Slik
   distribution page <http://www.sliksvn.com/en/download>`_ (select
   the version 1.5.2).

   After, you should be able to type ``svn help`` in a newly created
   shell.

   We recommand to install `PySVN for Windows, Python 2.4 and SVN 1.5.2
   <http://pysvn.tigris.org/files/documents/1233/43646/py24-pysvn-svn152-1.6.1-1024.exe>`_.

4. You can download and install ``libjpeg`` from the `GNUWin32
   Sourceforge page <http://gnuwin32.sourceforge.net/downlinks/jpeg.php>`_.

   *You need to select the same path than MinGW for installation*, and
   remove any ``GNUWin32`` append to that installation path, to get
   exactly the same installation path.


5. You need to install as well `pywin32
   <http://sourceforge.net/projects/pywin32/>`_, for Python 2.4.

.. warning::

   It's recommanded to work in directories which don't have any spaces
   in their paths. When you will be asked to checkout files from SVN
   to create your buildout directory, keep this in mind (or you will
   have problems).

.. note::

   Windows doesn't use the same separator in paths, so when you will be
   asked to type ``bin/buildout`` in your shell, type ``bin\buildout``
   instead.

Installation notes for others systems
-------------------------------------

If you want to install Python by hand, don't forget that it need to
have support for ZLib, and SSL (usually provided by OpenSSL).
