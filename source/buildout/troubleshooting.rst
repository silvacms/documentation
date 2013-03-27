Troubleshooting using buildout
==============================

If you cannot solve your problem, please report a bug on
https://bugs.launchpad.net/silva.

Non-error messages while running Buildout
-----------------------------------------

When you run Buildout, you can have non-fatal errors. **Don't panic**,
it's not a problem that will prevent you to install Silva. When you
**do** have a real error, Buildout will stop and tell you:

.. code-block:: sh

   [...]
   While:
     Installing something.
     Doing something.
   Error: Reason.

As well, on Unix, the command line status will not be zero (like all
programs which end unexpectedly with an error). If Buildout finishes
its job, and return with a status code of zero, that means anything
you encountered along the way was not a real problem.

**When you have an error, take the time to read the error message** If
it says 'download error', try to download the file by yourself. Maybe
the remote site is down or slow, or you internet connection is
flaky. If you succeed to download the file, try to run buildout again.

Example of non-fatal errors:

- Some download error from a site, or invalid URL. A different
  location for that package will be tried.

  Sometimes fetching a package might take time if your internet or the
  remote site is slow.

- Zope uses Python Script which can be imported in the ZODB. These are
  not valid python files, but buildout will try to compile them as
  python files (in order to have compiled code to run). Doing this fails:

  .. code-block:: sh

     "/somewhere/Products.GenericSetup-1.3.4-py2.7.egg/Products/GenericSetup/doc/SampleSite/profiles/default/siteroot/bar.py",
     line 22
        return printed
     SyntaxError: 'return' outside function

- When you compile a Python extension, you can have
  **warnings**. Those are *warnings*, not *errors*. They are not
  intended for you, but for the developer who made the Python
  extension:

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
need to install the development headers of that software. 

Development header can have names such as ``libsoftware-dev``,
``software-dev`` or ``software-devel``.

If you don't required development header packages install you will get
an error like the following:

.. code-block:: sh

   software.c:n:n: error: software.h: No such file or directory
   software:n:n: error: other_software.h: No such file or directory
   software.c:n:n: error: software_error.h: No such file or directory
   [...]
   error: Setup script exited with error: command 'gcc' failed with exit status 1
   An error occurred when trying to install software-extension version. Look above
   this message for any errors that were output by easy_install.
   While:
     Installing zeoclient1.
     Getting distribution for 'software-extension'.
   Error: Couldn't install: software-extension version

Of course the message can be slightly different depending on the
missing package name.

Installing MySQL-python
```````````````````````

To install MySQL you need to install the MySQL client libraries and
:ref:`development-headers`:

Using Debian/Ubuntu:

.. code-block:: sh

  $ sudo apt-get install libmysqlclient15-dev

Installing py-ldap
``````````````````

To install the OpenLDAP client libraries, and
:ref:`development-headers` you do:

Under Debian/Ubuntu:

.. code-block:: sh

  $ sudo apt-get install libldap-dev

Installing psycopg or psycopg2
``````````````````````````````

Install the PostGreSQL client libraries, and
:ref:`development-headers`:

Under Debian/Ubuntu:

.. code-block:: sh

  $ sudo apt-get install postgresql-dev

Version conflicts between system packages and Silva ones
--------------------------------------------------------

It is possible that when you are installing Silva, a required package
of Silva has already been installed in your system python
(``/usr/lib/python2.x/[site/dist]-packages``), thereby creating a
version conflict. You can run the buildout in a `virtualenv`_. You do
this with the help of:

.. code-block:: sh

  $ python2.7 bootstrap.py --virtualenv
  $ ./bin/buildout -v


.. _virtualenv: http://pypi.python.org/pypi/virtualenv
