Configuring Apache to present your site to the world
====================================================

By default Zope has its own web server, which can host multiple Silva
sites. However it is difficult to configure to which URLs (like
``http://mysite.com``) Zope is going to reply, and with which Silva site.

To solve this problem, you can use Apache in proxy mode, to *redirect*
HTTP requests to the correct Silva site within Zope. For instance
Apache can redirect requests for ``www.mysite.com`` to the Silva site
called ``mysite`` served by the local Zope server listening on the
port 8080 (``http://localhost:8080/mysite``).

As well, Apache is going to *rewrite URLs* to make Zope generate URLs
starting with ``http://www.mysite.com`` instead of
``http://localhost:8080/mysite``, so future requests to the site will
go through Apache as well.


.. note:: You are free to use other proxy software if they support
  rewriting of URLs. You can use `Nginx`_ or directly `Squid`_ if you
  want to do caching as well.

Configuring rewrite rules in Apache
-----------------------------------

First you need enable the following modules in Apache:

- `mod_rewrite`_.

- `mod_proxy`_ and `mod_proxy_http`_. You don't need to authorize any
  proxy operation.

After you can edit your Apache configuration to define a virtual host
for your site. With ``mod_rewrite`` you will be able to effectively
define the redirection from your site (``http://mysite.com``) to your Zope
server and your Silva site within (``http://localhost:8080/mysite``):

.. code-block:: apache
   :linenos:

   <VirtualHost *:80>
     ServerName www.mysite.com
     RewriteEngine On
     RewriteRule ^/(.*) http://localhost:8080/VirtualHostBase/http/%{SERVER_NAME}:80/mysite/VirtualHostRoot/$1 [L,P]
   </VirtualHost>

Explanation:

- On line 2, we declare the site we are handling.

- On line 3, we activate ``mod_rewrite`` for this site.

- On line 4, we redirect all requests to Zope rewriting them for the
  Zope virtual host rewrite engine. At the end of the line, the ``P``
  flag tells ``mod_rewrite`` to proxy the request to the rewritten URL
  and return the result.

.. _zope-virtual_host_monster:

How the Zope Virtual Host Rewrite Engine work
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In our case, Zope listens on localhost, port 8080. Apache relocates
the received request to our Zope server on
``http://localhost:8080/``. After this the URL is going to be prefixed with::

  VirtualHostBase/protocol/site-name/path/to/silva/site/VirtualHostRoot

``VirtualHostBase`` tells Zope that you want to rewrite generated URLs
in the produced HTML. ``protocol`` defines which protocol the new URLs
should have (``http`` or ``https``). ``site-name`` is the domain name
to use in those URLs (``www.mysite.com`` in our case). The previous
example uses the Apache variable ``%{SERVER_NAME}`` which is replaced
by Apache with the domain name mentioned in request (that should be
the same than ``ServerName``, except if you used ``ServerAlias`` or if
it is the default Apache site). After this, ``path/to/silva/site``
represent the path in Zope to the Silva Root you
created. ``VirtualHostRoot`` mark the end of this previously mentioned
path.

For more information on Apache configuration see `the official website
<http://httpd.apache.org/docs>`_.

If you want more details about the rewriting of URLs in Apache, you
can have a look to `the official user guide for mod_rewrite
<http://httpd.apache.org/docs/2.2/rewrite/>`_


Using rewrite rules to manipulate Silva URLs
--------------------------------------------

Some people have the use case to have different URLs structure inside
Silva and for the public.  This presents a problem because the URL
rewriting system is not able to compute URLs that are not inside the
virtual rewriting root.

.. warning::

  **We recommend not to use rewrite rules to do this, this will created
  lot of problems in your sites.**

Here is an example:

1. You have a Silva Root accessible via a rewrite rule at
   ``http://www.mysite.com``. The Silva Root contains two publications
   ``outside`` and ``inside`` (those two also contains others contents).

2. You set a rewrite rule so that you can access ``inside`` via the URL
   ``http://www.mysite.com/alias``.

Now in your Silva site, in ``inside`` you have a link to
``outside``. Zope cannot compute the URL
``http://www.mysite.com/outside``, because for Zope the site begins at
``inside`` represented by ``alias`` (``http://www.mysite.com/alias``)
and that ``outside`` is not inside ``inside``. Probably, by
acquisition Zope might be able to create the buggy URL:
``http://www.mysite.com/alias/outside``.

It is buggy because:

- People having permissions to edit the ``inside`` publication can add
  themselves a content called ``outside`` that visitors will get instead
  the intended publication (it is phishing),

- If you have a document with the same identifier in ``inside``, a link
  to content located inside of ``outside`` might result in 404 error
  instead of the real content,

- URLs appearing in the browser will look wrong (and that’s why they
  will have those unwanted behaviors),

- The virtual root is the starting point to cache visual design
  resources (CSS, images). Adding a lot more of them makes caching
  less efficient, as more caches will have to be created. Visitors
  will have to re-download CSS and images many times, and will make
  the site slower to browser for them.


Solution
~~~~~~~~

You can install and use the Silva extension `silva.app.forest`_, that
have been built for this use-case. It will remove all this complicated
configuration from your Apache configuration, and store this
information in Zope. It supports multiple sites, with aliases and the
equivalent of multiple rewrite rules inside a given site.


.. _Nginx: http://nginx.org/
.. _Squid: http://www.squid-cache.org/
.. _mod_proxy: http://httpd.apache.org/docs/2.2/mod/mod_proxy.html
.. _mod_proxy_http: http://httpd.apache.org/docs/2.2/mod/mod_proxy_http.html
.. _mod_rewrite: http://httpd.apache.org/docs/2.2/mod/mod_rewrite.html
.. _silva.app.forest: http://infrae.com/download/silva_all/silva.app.forest
