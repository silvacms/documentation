Presenting your Silva instance to the world
===========================================

Using ``bin/paster`` is fine for small installation. We recommand to
configure Apache in front of it in order to have a more user-friendly
URL in order to access your Silva instance.

For larger installation we recommand to use either uWSGI and NGinx, or
``mod_wsgi``.

.. toctree::
   :maxdepth: 2

   apache
   uwsgi
   wsgi

