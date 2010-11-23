.. _assets:

Asset Objects
=============

Assets are non-publishable objects used only to hold data. That data
can be uploaded, transformed, and download, but usually not edited on
the server itself.

Exemples of assets are files and images.

.. contents::


Asset Objects
-------------

.. autointerface:: silva.core.interfaces.INonPublishable


.. autointerface:: silva.core.interfaces.IAsset


File Objects
------------

Silva File provides the following API:

.. autointerface:: silva.core.interfaces.IFile

This API is shared by all existing file implementation in Silva:

.. autointerface:: silva.core.interfaces.IZODBFile

.. autointerface:: silva.core.interfaces.IBlobFile

.. autointerface:: silva.core.interfaces.IFileSystemFile


Image Objects
-------------

.. autointerface:: silva.core.interfaces.IImage
