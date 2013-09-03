
.. _configure-an-upload-server:

Configuring an upload server for files
======================================

You have the possibility to configure a separate server in order to
upload files in Silva. This is usefull if you have a large setup
and/or upload lot of files, since the upload will no longer interfer
with the performances of the setup (since they can last long, this
prevent them to block resources).

The upload server is a small WSGI application. Like for Silva, you can
choose the WSGI server of your choice.
