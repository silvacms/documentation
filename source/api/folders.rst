.. _folders:

Folderish Objects
=================

All container objects have to implement at least the container
interface.

.. contents::

Container Objects
-----------------

.. autointerface:: silva.core.interfaces.content.IContainer

Folder Objects
--------------

.. autointerface:: silva.core.interfaces.content.IFolder

Publication Objects
-------------------

.. autointerface:: silva.core.interfaces.content.IPublication

Root Objects
------------

.. autointerface:: silva.core.interfaces.content.IRoot


Site information
~~~~~~~~~~~~~~~~

.. currentmodule:: silva.core.interfaces.content

You often need to retrieve the root content of your current site in a
layout. We don't recommend to use ``get_root`` from
:py:interface:`IRoot`. You should more use the following :term:`Zope
adapter`:

.. autointerface:: silva.core.views.interfaces.IVirtualSite

.. versionadded:: 2.2
