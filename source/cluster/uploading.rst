
.. _configure-an-upload-server:

Configuring an upload server for files
======================================

You have the possibility to configure a separate server in order to
upload files in Silva. This is useful if you have a large setup and/or
upload a lot of files, since the upload will no longer interfer with the
performances of the installation. Since those requests can last long,
this prevent them to block resources.

The upload server is a small WSGI application. Like for Silva, you can
choose the WSGI server of your choice.


.. note::

   Because Nginx bufferize every upload in memory or temporary files,
   we don't recommend to use it with the upload server, or to use the
   `upload progress module`_. This module is compatible with Silva if
   it is configured to generate a JSONP output.

.. _upload progress module: http://wiki.nginx.org/HttpUploadProgressModule
