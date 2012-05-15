.. _folders:

Folderish Objects
=================

All container objects have to implement at least the container
interface.

.. contents::


Container
---------

You have two different sort of containers, ordered ones, and
non-ordered ones:

.. autointerface:: silva.core.interfaces.content.IContainer

.. autointerface:: silva.core.interfaces.content.IOrderableContainer

.. versionadded:: 3.0

Folder Objects
~~~~~~~~~~~~~~

Folders are the basic content type that author used to organize content in Silva.

.. autointerface:: silva.core.interfaces.content.IFolder

Publication Objects
~~~~~~~~~~~~~~~~~~~

.. autointerface:: silva.core.interfaces.content.IPublication

Root Objects
~~~~~~~~~~~~

There is only one Root object in Silva, created when the Silva
instance is added to the database. It is impossible for a user to an
another Root object inside an existing one.

.. autointerface:: silva.core.interfaces.content.IRoot


Operations
----------

A set of :term:`Zope adapter` let you do advanced operations on
existing containers (modification, listing)...

Managing container's content
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can use the following :term:`Zope adapter` to manage any content
of a :py:interface:`~silva.core.interfaces.content.IContainer`:

.. autointerface:: silva.core.interfaces.adapters.IContainerManager

.. versionadded:: 3.0

Managing container's content order
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can use the following :term:`Zope adapter` to manage the order of
the contents contained inside an
:py:interface:`~silva.core.interfaces.content.IOrderableContainer`:

.. autointerface:: silva.core.interfaces.adapters.IOrderManager

.. versionadded:: 3.0

Listing container trees
~~~~~~~~~~~~~~~~~~~~~~~

The tree feature is now provided by a :term:`Zope adapter` as well, on
:py:interface:`~silva.core.interfaces.content.IContainer`:

.. autointerface:: silva.core.interfaces.adapters.ITreeContents

.. note::

   Those operations are really slow on large containers.

.. versionadded:: 3.0

Container authorized addable
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A :term:`Zope adapter` let you access the list of content you are
authorized to create within a
:py:interface:`~silva.core.interfaces.content.IContainer`. Effectively,
the implementation of this adapter can be different depending of the
container types: some containers like a News Publication requires more
limitation than the standard Silva container types.

.. autointerface:: silva.core.interfaces.adapters.IAddableContents

.. versionadded:: 3.0


Site information
~~~~~~~~~~~~~~~~

.. currentmodule:: silva.core.interfaces.content

You often need to retrieve the root content of your current site in a
layout. We don't recommend to use ``get_root`` from
:py:interface:`IRoot`. You should preferably use the following
:term:`Zope adapter`, on the request object:

.. autointerface:: silva.core.views.interfaces.IVirtualSite

.. versionadded:: 2.2

