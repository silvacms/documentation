Using your Silva instance
=========================

.. contents::

.. _starting-creating-silva-site:

Starting your Zope instance and creating a Silva site
-----------------------------------------------------

After having installed your Silva environment, you should have a
``bin/paster`` script in your installation directory. You can use it to
start the Zope instance:

.. code-block:: sh

   $ bin/paster serve deploy.ini start

After few seconds, the server should be running, so you can open a web
browser and type in the server location:

    http://localhost:8080

You will be here on the starting page of your Zope instance. A welcome
page list all the Silva sites who have been created in this Zope
instance, or let you add a new ones.

You can enter a site identifier (usually only lowercase letters
without spaces), and a site title. You can choose to automatically
install the user documentation and add search functionality, but if
you choose not, those can be installed later on.

To add a new site, you will be prompt for a user and a password. The
installation default are ``admin`` and ``admin`` if you didn't specify
any while creating your Zope instance before.

When the Silva site is created, you will be redirected to its
management interface (SMI, Silva Management Interface). You can after
rely on the user documentation to be guided through it.

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


Running in development mode
---------------------------

If you wish to do development, you can run Silva in development
mode. For this you can use the configuration ``debug.ini`` instead of
``deploy.ini``. In development mode all caches will be disabled, and a
interactive debugger will help your do debug your errors.

You can for instance run Zope in foreground mode and debug like this:

.. code-block:: sh

  $ ./bin/paster serve debug.ini

.. note::

   It is not because Zope is running in foreground mode it will be in
   mode debug. It is because you used the configuration file
   ``debug.ini`` instead of ``deploy.ini``.

.. warning::

   In any case you should not use the development mode as a production
   mode, this would be a serious security issue, due to the
   interactive debugger.


More information
----------------

You used Paste Deploy to manage your Zope instance. You can have more
information on the command or on the configuration file:

- by consulting the help of the command:

  .. code-block:: sh

     $ ./bin/paster serve --help

- by reading the official documentation: http://pythonpaste.org/deploy/
