Troubleshooting using buildout
==============================

If you cannot solve your problem, please report a bug on
https://bugs.launchpad.net/silva.

Non-error messages while running Buildout
-----------------------------------------

When you run Buildout, you can have non-fatal errors. **Don't panic**,
it's not a problem. When you **do** have a real error, Buildout will
stop and tell you:

.. code-block:: sh

   [...]
   While:
     Installing something.
     Doing something.
   Error: Reason.

As well, the status will not be zero (like all programs which end
unexpectedly with an error). If Buildout finish its job, and return
with a status code of zero, that means anything you encountered along
the way was not a real error.

**When you have an error, read the error message!** If it says
'download error', try to download the file by yourself. Maybe the
remote site is down or slow, or you internet connection is flaky. If
you succed to download the file, try to run buildout again.

Exemple of non-fatal errors:

- Some download error from a site, or invalid URL. A different
  location for that pacakge will be tried.

  Sometimes fetching a package might take time if your internet or the
  remote site is slow.

- Zope uses Python Script which can be imported in the ZODB. These are
  not valid python files, but buildout will try to compile them as
  python files (in order to have compiled code to run). Doing this fails:

  .. code-block:: sh

     "/somewhere/Products.GenericSetup-1.3.4-py2.4.egg/Products/GenericSetup/doc/SampleSite/profiles/default/siteroot/bar.py",
     line 22
        return printed
     SyntaxError: 'return' outside function

- When you compile a Python extension, you can have
  **warnings**. These are *warnings*, not *errors*. These warnings are
  not intended for you, but for the developer who made the  Python extension:

  .. code-block:: sh

     libImaging/Effects.c:210: warning: ‘perlin_init’ defined but not used
     libImaging/Geometry.c:236: warning: ‘quadratic_transform’ defined but not used
     libImaging/Quant.c:311: warning: ‘test_sorted’ defined but not used
     libImaging/QuantHash.c:136: warning: ‘_hashtable_test’ defined but not used

.. _development-headers:

Development Headers
-------------------

If you need to install a Python extension which provides you the use
of a protocol/software, you need to install that protocol's
libraries/software before.

For instance, you are going to install an LDAP client to be able to
use LDAP in Python.

Since you are installing the Python extension from the source, you
need to install the development headers of that software. In those
cases, they might be provided with package names like
``libsoftware-dev``, ``software-dev`` or ``software-devel``. Refer to
the documentation of your system to find them.

If you don't install them you will get an error like:

.. code-block:: sh

   software.c:n:n: error: software.h: No such file or directory
   software:n:n: error: other_software.h: No such file or directory
   software.c:n:n: error: software_error.h: No such file or directory
   [...]
   error: Setup script exited with error: command 'gcc' failed with exit status 1
   An error occured when trying to install software-extension version. Look above
   this message for any errors that were output by easy_install.
   While:
     Installing zeoclient1.
     Getting distribution for 'software-extension'.
   Error: Couldn't install: software-extension version

Of course the message can be slightly different.

Installing MySQL-python
```````````````````````

You need to install the MySQL client libraries, and
:ref:`development-headers` for MySQL if there are packaged
differently.

Under Debian/Ubuntu:

.. code-block:: sh

  $ sudo apt-get install libmysqlclient15-dev

Installing py-ldap
``````````````````

You need to install the OpenLDAP client libraries, and
:ref:`development-headers` for OpenLDAP if there are packaged
differently.

Under Debian/Ubuntu:

.. code-block:: sh

  $ sudo apt-get install libldap-dev

Installing psycopg or psycopg2
``````````````````````````````

You need to install the PostGreSQL client libraries, and
:ref:`development-headers` for PostGreSQL if there are packaged
differently.

Under Debian/Ubuntu:

.. code-block:: sh

  $ sudo apt-get install postgresql-dev

Version conflicts between system packages and Silva ones
--------------------------------------------------------

It is possible that you already have installed packages required by
Silva in your (system) Python in a different version and that buildout
raise a conflict version because of that. In that case, you can run
buildout again, and gives the option ``-S`` to Python, which prevent
it to look at the extensions you installed in that same Python:

.. code-block:: sh

  $ python2.4 -S ./bin/buildout -v



SVN 1.5 and buildout
--------------------

Under Windows or Unix, with SVN 1.5:

.. code-block:: sh

  unrecognized .svn/entries format; skipping .
  Traceback (most recent call last):
    File "c:\docume~1\arthur\locals~1\temp\tmpnmclvm", line 11, in ?
      execfile('C:\\silva-trunk\\src/five.grok\\setup.py')
    [...]
    File "c:\silva-trunk\eggs\setuptools-0.6c8-py2.4.egg\setuptools\command\s
  dist.py", line 98, in entries_finder
  NameError: global name 'log' is not defined
  While:
    Installing.
    Processing develop directory 'C:\\silva-trunk\\src/five.grok'.

  An internal error occured due to a bug in either zc.buildout or in a
  recipe being used:
  Traceback (most recent call last):
    File "c:\docume~1\arthur\locals~1\temp\tmpgckuer\zc.buildout-1.1.1-py2.4.egg\z
  c\buildout\buildout.py", line 1477, in main
    File "c:\docume~1\arthur\locals~1\temp\tmpgckuer\zc.buildout-1.1.1-py2.4.egg\z
  c\buildout\buildout.py", line 324, in install
    File "c:\docume~1\arthur\locals~1\temp\tmpgckuer\zc.buildout-1.1.1-py2.4.egg\z
  c\buildout\buildout.py", line 556, in _develop
    File "c:\docume~1\arthur\locals~1\temp\tmpgckuer\zc.buildout-1.1.1-py2.4.egg\z
  c\buildout\easy_install.py", line 866, in develop
  AssertionError


You can fix it by upgrading setuptools to 0.6c9. If you installed it
using a package provided by your system, refer to your system
documentation to upgrade that package. If you installed it by running
the ``ez_setup.py`` script, use the following command to upgrade it:

.. code-block:: sh

   C:\Silva-trunk> easy_install -U setuptools==0.6c9

If you can't, you have to fix it by hand. In a shell, do:

.. code-block:: sh

   C:\Silva-trunk>cd eggs
   C:\Silva-trunk\eggs>move setuptools-0.6c8-py2.4.egg setuptools-0.6c8-py2.4.egg.zip

Of course, if you installed ``easy_install`` in your Python, go in
``python-installation/lib/python2.4/site-packages`` instead to find
the archive, or if you setup an egg cache, go in your egg cache.

After with the explorer, unpack the archive
``setuptools-0.6c8-py2.4.egg`` in that same folder to the same
folder. Delete the archive after. Edit the file
``setuptools-0.6c8-py2.4.egg\setuptools\commands\sdist.py``, add a
line at the top of it:

.. code-block:: python

   from distutils import log

Rerun buildout, and thanks setuptools' maintainers for a good test
coverage of their software.


