Configuring uWSGI and Nginx to present your site to the world
=============================================================

You can install `uWSGI`_ and `Nginx`_ in order to serve your Silva
site. This is recommended way for large installations. In this setup
you will have an `uWSGI`_ server that runs independently and an
`Nginx`_ server that connects to it.

.. contents::

Installing uWSGI with buildout
------------------------------

Because `uWSGI`_ must be compiled with the same Python interpreter
than the one used to install Silva, and that its configuration
contains all the paths to the various Python packages used by Silva,
the best way to install it is to use :term:`Buildout`. To do so, you
need to be already familiar with :term:`Buildout`. If you are not
please read :ref:`extending-and-customising-your-installation` first.

You can use the recipe `infrae.uwsgi`_ in order to install
`Nginx`_. For instance in your buildout configuration file
``buildout.cfg`` you can add:

.. code-block:: buildout
   :linenos:

   [buildout]
   parts +=
      uwsgi

   [uwsgi]
   recipe = infrae.uwsgi
   download-url = http://projects.unbit.it/downloads/uwsgi-1.4.9.tar.gz
   master = True
   http-socket = 0.0.0.0:8080
   uwsgi-socket = 127.0.0.1:8081
   paste = config:${buildout:directory}/deploy.ini
   paste-logger = True
   processes = 1
   threads = 15
   eggs = ${instance:eggs}
   single-interpreter = True
   lazy = True
   daemonize = ${buildout:directory}/var/log/uwsgi.log
   pidfile = ${buildout:directory}/var/instance/uwsgi.pid


Lines 5 to 19 defines a new part to install ``uwsgi``:

- Line 9 and 10 specify the IP and port number the uWSGI server will
  bind and listen on. The uWSGI socket defined on line 10 will be used
  to interact with Nginx. The http socket defined on line 9 is
  optional, but let you access your uWSGI server with your web
  browser if you define it.

- Line 11 and 12 ask uWSGI to load the Paster configuration generated
  by buildout. This points to the same configuration file that is used
  if you start your instance the in a simple fashion
  (:ref:`starting-creating-silva-site`).

- Lin 13 and 14 defines the number of processus and threads you want
  to use. If you want to use more than one processus, you need to
  setup either a RelStorage or a ZEO server (see
  :ref:`silva-high-availability-installation`). Specifying an higher
  number of threads than what is given in this example won't improve
  at all the performances and might even reduce them.

- Line 17 defines the option ``lazy`` which is critical in order to
  operate the ZODB. **Not specifying this option might results in
  losing data**.

- Line 18 and 19 tells uWSGI to act as a daemon and go in the
  background after it started. If you wish to run it as foreground
  instead you can omit those two directives.

You can specify here in the :term:`Buildout` configuration any
existing `uWSGI configuration option`.


Managing your uWSGI instance
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can start your `uWSGI`_ server like this:

.. code-block:: sh

   $ bin/uwsgi parts/uwsgi/uwsgi.xml

You can stop your `uWSGI`_ server like this:

.. code-block:: sh

   $ bin/uwsgi --stop var/instance/uwsgi.pid

We recommend to run the server with an unprivileged user on Unix. If
you wish to start `uWSGI`_ as *root*, you should either use ``sudo``
or configure `uWSGI` to drop its privileges to a regular user after it
started. In order to do so you can use the ``uid`` and ``gid`` options
in the :term:`Buildout` part.


Installing and configuring Nginx
--------------------------------

Unlike `uWSGI`_, we recommend to install `Nginx`_ using your system
package manager, and not use :term:`Buildout` in order to do it.

You can configure `Nginx`_ in order to redirect requests to `uWSGI`_,
using the default module ngx_http_uwsgi. No custom built of `Nginx`_
is needed.

.. code-block:: nginx
   :linenos:

    upstream silva {
       server 127.0.0.1:8081;
    }

    server {
        listen       80;
        server_name  localhost;
        location / {
            uwsgi_pass silva;
            include uwsgi_params;
        }
    }

- Line 1 to 3 define an Nginx ``upstream``. You need to specify an IP
  and port number where a uWSGI server is running: this must be the
  port number associated with the uWSGI socket, not the http
  socket. Inside an ``upstream`` section, you have the possiblity to
  define multiple uWSGI servers, in order to do load-balancing. Other
  options are available too, like different load-balancing algorithms,
  backup servers, for more information please refer to the `Nginx
  documentation`_.

.. note::

   If you have multiple Nginx frontend you will need to
   :ref:`configure-an-upload-server`.



Starting and Stopping your site
-------------------------------

To start you site you need to *start* both uWSGI and Nginx. To stop
it, well, you can stop uWSGI.

.. _Nginx: http://nginx.org/
.. _Nginx documentation: http://nginx.org/en/docs/
.. _uWSGI: http://uwsgi-docs.readthedocs.org/
.. _uWSGI configuration option: http://uwsgi-docs.readthedocs.org/en/latest/Options.html
.. _infrae.uwsgi: https://pypi.python.org/pypi/infrae.uwsgi
