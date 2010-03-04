Configuring Apache to present your site to the world
====================================================

Zope is runs its own web server. It can host multiple Silva sites with
different URLs. However it is difficult to configure which URLs the
Zope will, with which Silva site. To solve this problem use Apache to
handle *routing*.

Configuring rewrite rules in Apache
-----------------------------------

First enable the following modules in Apache:

- ``mod_rewrite``.

- ``mod_proxy`` (actually ``mod_proxy_http``). You don't need to
  authorize any proxy operation because it denies everything by
  default.

After you've enable the Apache modules open the apache configuration
file. In your configuration you can use the following lines to enable
processing requests to Zope:

.. code-block:: apache
   :linenos:

   ServerName your-site.com
   RewriteEngine On
   RewriteRule ^/(.*) http://localhost:8080/VirtualHostBase/http/%{SERVER_NAME}:80/silva/VirtualHostRoot/$1 [L,P]

Explanation:

- On line 1, we declare the site we are handling.

- On line 2, we activate ``mod_rewrite`` for this server (or virtual host).

- On line 3, we redirect all requests to Zope.

In our case, Zope listens to localhost on port 8080. Apache relocates
the URL to our Zope server ``http://localhost:8080/``. VirtualHostBase
is with Zope and is responsible for rewriting the links with Zope.

This means that ``http://localhost:8080/silva`` will be accessible
using the URL ``http://your-site.com``.

For more information on Apache configuration see `the official website
<http://httpd.apache.org/docs>`_.


Using rewrite rules to manipulate Silva URLs
--------------------------------------------

Some people have the use case to have different URLs within
Silva. This present a small problem because the URL rewriting system
is not able to compute URLs that are not inside the virtual root.

Here is an example:

1. You have a Silva root accessible via a rewrite rule with the name
   ``your-site.com``. The silva root It contains two publications
   ``pub1`` and ``pub2`` (these two items also contain data).

2. You set a rewrite rule so that you can access ``pub2`` via the URL
   ``your-site.com/foo``.

Now on your site, inside ``pub2`` you have a link to ``pub1``. Zope
cannot compute the URL ``your-site.com/pub1``, because for Zope the
site begins at ``foo``. Via acquistion Zope will create the URL:
``your-site.com/foo/pub1``.

To avoid this we suggest adding Silva Links at the root of your site
redirecting you to the correct content. For instance, in this example
you could create a Silva Link called ``foo``, which points to
``your-site.com/pub2``.
