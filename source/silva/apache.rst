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

- On line 1, we declare which is the site we are handling.

- On line 2, we activate ``mod_rewrite`` for this server (or virtual host).

- On line 3, we redirect all requests to Zope.

  In our case, Zope listen on localhost, port 8080. After the URL
  component ``VirtualHostBase`` tell Zope to use the URL rewrite
  engine.

  It will generate all links in web pages using http://your-site.com
  instead of http://localhost:8080, and to use ``/silva`` as path to
  the site root element instead of ``/``.

  So that's means that http://localhost:8080/silva will be accessible
  using the URL http://your-site.com via Apache.

You can have more information on Apache configuration on `the official
website <http://httpd.apache.org/docs>`_.


Using rewrite rules to manipulate Silva URLs
--------------------------------------------

Some people have the use case to have different URLs inside Silva and
for the public. There is a restriction to that, the URL rewriting system
is not able to compute URLs that are not inside the virtual root.

Let's take an example:

1. You have a Silva root accessible via a rewrite rule with the name
   ``your-site.com``. It contains two publications ``pub1`` and
   ``pub2`` (which have themselves some data).

2. You set a rewrite rule so that you can access ``pub2`` via the URL
   ``your-site.com/foo``.

Now on your site, inside ``pub2`` you have a link to ``pub1``, in
navigation or inside a document. Zope is not going to compute the URL
``your-site.com/pub1`` for you, as for him the world starts at
``foo``. It is going to create you that URL:
``your-site.com/foo/pub1``, which, because of Zope Acquisition *might*
work.

The cases where that URL doesn't work are:

- People having the right to edit the ``pub2`` publication can add
  themselves a content called ``pub1`` that the people will get the
  intended publication (like phis-hing)

- If indeed you have a document with the same identifier in ``pub2``,
  a link to content of ``pub1`` might result in 404 error instead of
  the real content.

- Links appearing in the browser will look wrong (and that's why they
  will have those unwanted behaviors).

- The virtual root is the starting point to cache theme resources
  (CSS, images). Adding a lot more of them makes caching less
  efficient and visitors will have to re-download CSS and images many
  times.

Solution
~~~~~~~~

Most of the time, you just wanted to add Silva Links at the root of
your site to redirect you to the write content. For instance, you
could have created a Silva Link called ``foo``, which points to
``your-site.com/pub2`` in the previous example.
