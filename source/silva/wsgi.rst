Configuring Apache's mod_wsgi to present your site to the world
===============================================================

Silva now support WSGI. You can use Apache's ``mod_wsgi`` to directly
serve it, without having it running in a different daemon and proxing
request to it.

.. contents::


Requirements
------------

For this you need to install ``mod_wsgi`` for the Apache you are using.


.. warning::

  When you install ``mod_wsgi`` it is compiled to use a specific
  version and installation of Python, usually the one of your system
  if you install it with a your system packaging system.

  ``mod_wsgi`` **should** use Python 2.6, and you **should** install
  Silva with this same Python.


For this reason we recommend you to install ``mod_wsgi`` by hand.


Configuration in Apache
-----------------------

You have first to load ``mod_wsgi`` and ``mod_rewrite`` into your
Apache configuration.

After you can edit your Apache configuration to define a virtual host
for you site. You will use ``mod_rewrite`` to rewrite the request URL
in order to shape it correctly for the virtual host monster that will
make Zope generate URLs for the site URL you configured

.. code-block:: apache
   :linenos:

   WSGIDaemonProcess mysite user=username group=username threads=4 maximum-requests=10000 python-eggs=/tmp/python-eggs

   <VirtualHost *:80>
      ServerName www.mysite.com

      RewriteEngine On
      RewriteRule ^/(.*) /VirtualHostBase/http/%{HTTP_HOST}:80/mysite/VirtualHostRoot/$1 [PT]
      WSGIScriptAlias / /fs/path/to/silva/installation/parts/mod_wsgi_app/wsgi
      WSGIProcessGroup mysite
      WSGIPassAuthorization On

      <Directory /fs/path/to/silva/installation/parts/mod_wsgi_app>
        Order deny,allow
        Allow from all
     </Directory>
   </VirtualHost>


Explanation
~~~~~~~~~~~

On line 1 we define a process to run our Silva site in WSGI. We says
that it will run with the user ``username`` and group ``username`` on
the file-system. These user **should** be the non-privileged user you
used to install Silva. We says that are going to have 4 threads to
serve requests. **You should not** configure more than 7 threads in
any case. After we says that we want to renew our WSGI process every
10000 requests.


Multiple processus
~~~~~~~~~~~~~~~~~~

If you wish to configure multiple WSGI processus, you need first to
install a ZEO server. You **cannot** configure multiple processus
without a ZEO server or you might corrupt your Zope database.

In this configuration, all Zope processus will use the same Zope
configuration to connect to the ZEO server.

Precaution must be taken if you wish to use Blobs with this setup: the
blob storage directory **must be shared** between all Zope processus
connected to the ZEO server. That means either all the Zope processus
have to run on the same computer or the directory needs to be shared
between computers, like with NFS. Sharing Blobs over the network
doesn't bring any downside on the performances.


Starting and Stopping your site
-------------------------------

Now Silva is directly embed in Apache. To *restart* Silva, you need to
restart Apache. To *stop* Silva, you need to stop Apache. You **should
not** use the installed Silva daemon while Silva is running within
Apache.
