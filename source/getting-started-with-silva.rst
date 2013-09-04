Using your Silva instance
=========================

.. contents::

.. _starting-creating-silva-site:

Starting your Zope instance and creating a Silva site
-----------------------------------------------------

After installing your Silva environment, you should have a
``bin/paster`` script in your installation directory. You can use it to
start the Zope instance:

.. code-block:: sh

   $ bin/paster serve deploy.ini start

After few seconds, the server should be running, you can open a web
browser and type in the server location:

    http://localhost:8080/

You will be watching the start page of your Zope instance. A welcome
page lists all the Silva sites which have been created in this Zope
instance, or let you add a new ones.

You can enter a site identifier (usually only lowercase letters
without spaces), and a site title. You can choose to automatically
install the user documentation and add search functionality, but if
you choose not, those can be installed later on.

To add a new site, you will be prompted for a user name and a password. The
installation defaults are ``admin`` and ``admin`` if you didn't specify
otherwise while creating your Buildout directory.

When the Silva site is created, you will be redirected to its
management interface, the :term:`SMI`. You can rely on the user
documentation to be guided through it.

.. glossary::

   *SMI*
     The SMI, or Silva Management Interface, is the user interface
     where an author or editor can add, delete and modify content in
     the Silva site.

   *ZMI*
     The ZMI, or Zope Management Interface, is the Zope user interface
     were administrators (called Managers) can configure the advanced
     settings of Silva. Regular Silva user don't have access to this
     interface.

.. note::

   You can add more than one *Silva Root* (or Silva instance) in one
   Zope instance and use them independently.

Stopping and restarting your Zope instance
------------------------------------------

You can stop your Silva instance with:

.. code-block:: sh

   $ ./bin/paster serve deploy.ini stop

You can restart directly the instance with:

.. code-block:: sh

   $ ./bin/paster serve deploy.ini restart

And the following command can tell you if Silva is running.

.. code-block:: sh

   $ ./bin/paster serve deploy.ini status

For obvious security reasons, we don't recommand to run Silva using a
privileged user like ``root`` on Unix, but a regular non-privileged
user. If you wish to start the server as such, we recommend you to use
``sudo`` at all time.


Running in development mode
---------------------------

If you want use you silva instance for development, you can run Silva in
development mode. To achieve this, you have to use ``debug.ini``
configuration file instead of ``deploy.ini`` when starting paster.
In development mode all caches are disabled, and a interactive debugger
can help your do debug errors by allowing you to introspect python backtrace.

You can for instance run Zope in foreground mode and debug:

.. code-block:: sh

  $ ./bin/paster serve debug.ini

.. note::

   The fact that Zope is running in foreground mode does not imply that it runs
   debug mode. This behavior is the result of using ``debug.ini`` configuration
   instead of ``deploy.ini``.

.. warning::

   In any case you should not use the development mode as a production
   configuration, this would be a serious security issue, due to the
   interactive debugger.


More information
----------------

You used Paste Deploy to manage your Zope instance. You can get more
information on the command or on the configuration file:

- by consulting the help of the command:

  .. code-block:: sh

     $ ./bin/paster serve --help

- by reading the official documentation: http://pythonpaste.org/deploy/
