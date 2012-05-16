.. _folders:

Folderish Objects
=================

All container objects have to implement at least the container
interface.

.. contents::

.. currentmodule:: silva.core.interfaces.content

Container
---------

You have two different sort of containers, ordered ones, and
non-ordered ones:

.. autointerface:: IContainer

.. autointerface:: IOrderableContainer

.. versionadded:: 3.0

Folder Objects
~~~~~~~~~~~~~~

Folders are the basic content type that author use to organize
content in Silva.

.. autointerface:: IFolder

Publication Objects
~~~~~~~~~~~~~~~~~~~

.. autointerface:: IPublication

Root Objects
~~~~~~~~~~~~

There is only one Root object in Silva, created when the Silva
instance is added to the database. It is impossible for an author,
editor or manager to an another Root object inside an existing one.

.. autointerface:: IRoot


Operations
----------

A set of :term:`Zope adapter` let you do advanced operations on
existing containers (modification, listing)...

Managing container's content
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can use the following :term:`Zope adapter` to manage any content
of a :py:interface:`IContainer`:

.. autointerface:: silva.core.interfaces.adapters.IContainerManager

.. versionadded:: 3.0

To use this adapter, you must use the methods of this adapter as
coroutines. You do it using the method as a context manager. Here is
an example where we copy a content into a folder:

.. code-block:: python

   from silva.core.interfaces import IContainerManager

   with IContainerManager(self.root.folder).copier() as copier:
       copy = copier(self.root.document_to_copy)

In case of removing content, verification will be done then the
coroutine is called, however the content won't be effectively removed
before the context manager exists.


Managing container's content order
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can use the following :term:`Zope adapter` to manage the order of
the contents contained inside an :py:interface:`IOrderableContainer`:

.. autointerface:: silva.core.interfaces.adapters.IOrderManager

.. versionadded:: 3.0

Listing container trees
~~~~~~~~~~~~~~~~~~~~~~~

The tree feature is now provided by a :term:`Zope adapter` as well, on
:py:interface:`IContainer`:

.. autointerface:: silva.core.interfaces.adapters.ITreeContents

.. note::

   Those operations are really slow on large containers.

.. versionadded:: 3.0

Container authorized addable
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A :term:`Zope adapter` let you access the list of content you are
authorized to create within a :py:interface:`IContainer`. Effectively,
the implementation of this adapter can be different depending of the
container types: some containers like a News Publication requires more
limitation than the standard Silva container types.

.. autointerface:: silva.core.interfaces.adapters.IAddableContents

.. versionadded:: 3.0


Local site management
~~~~~~~~~~~~~~~~~~~~~

A :term:`Zope adapter` let you manage the status of the local site on
a :py:interface:`IPublication`.

.. autointerface:: silva.core.interfaces.adapters.ISiteManager

.. versionadded:: 2.2

Site information
~~~~~~~~~~~~~~~~

You often need to retrieve the root content of your current site in a
layout. We don't recommend to use ``get_root`` from
:py:interface:`IRoot`. You should preferably use the following
:term:`Zope adapter`, on the request object:

.. autointerface:: silva.core.views.interfaces.IVirtualSite

.. versionadded:: 2.2

