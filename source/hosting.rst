.. _presenting-your-silva-site-to-the-world:

Presenting your Silva site to the world
=======================================

Using ``bin/paster`` is fine for small installation. We recommend to
configure Apache in front of it in order to have a more user-friendly
URL in order to access your Silva server.

For larger installation we recommend to use either `uWSGI`_ and
`Nginx`_, or ``mod_wsgi``.


.. toctree::
   :maxdepth: 2

   apache
   uwsgi
   wsgi


You will probably want to rewrite the URL of your Silva in order to
expose it to the public. In order to do so you can either use the Zope
Virtual Host (see :ref:`zope-virtual_host_monster`) for small
installations, or `silva.app.forest`_ for larger installations that
will provides you with more flexibility.


In case of large installations you might to install an HTTP cache in
front of your Silva servers. Recommended possibilities are:

- `Varnish`_,

- `Squid`_.

If you still have multiple Silva servers and do not wish to install a
cache you will still need to install a load balancer in front of
them. Recommended possibilities are:

- Using `Nginx`_ and `uWSGI`_ in the previously proposed configuration
  already provides this feature natively,

- Apache `mod_proxy_balancer`_,

- `Squid`_ or `Varnish`_,

- `Pound`_,

- An hardware solution (usually expensive).

For large installations, you can refer to
:ref:`silva-high-availability-installation` for more information.


.. _mod_proxy_balancer: http://httpd.apache.org/docs/2.2/mod/mod_proxy_balancer.html
.. _Pound: http://www.apsis.ch/pound/
.. _Varnish: https://www.varnish-cache.org/
.. _Squid: http://www.squid-cache.org/
.. _uWSGI: http://uwsgi-docs.readthedocs.org/
.. _Nginx: http://nginx.org/
.. _silva.app.forest: http://silvacms.org/getsilva/packages/silva_all/silva.app.forest
