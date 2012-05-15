

Versioned content Objects
=========================

Versioned content objects extends regular content objects to support
different version of the same content, and all related operations
(like creating a new version, publishing, expiration of older,
comparaisons).

.. contents::


Versioned content objects
-------------------------

Versioning is built using two different type of objects in Silva: a
:py:interface:`~silva.core.interfaces.content.IVersion`, that is
contained inside a
:py:interface:`~silva.core.interfaces.content.IVersionedContent`.

Version
~~~~~~~

A version will store the content data associated with a versioned
content.

.. autointerface:: silva.core.interfaces.content.IVersion

.. warning::

   It is important not to store any user data on the versioned content
   itself, but only on the version. A versioned content can be
   reverted to a previous version. A version can be rendered, even if
   it is not currently published: one or two previous versions can be
   viewed, or compared together.

Versioning
~~~~~~~~~~

The versioning interface define the basic API to work with versioned
content, and will be available through the
:py:interface:`~silva.core.interfaces.content.IVersionedContent`.

.. autointerface:: silva.core.interfaces.content.IVersioning

.. note::

   This is a low-level API. In order to work on a versioned content,
   please use the
   :py:interface:`~silva.core.interfaces.adapters.IPublicationWorkflow`
   :term:`Zope adapter` instead.

Versioned content
~~~~~~~~~~~~~~~~~

A versioned content is always associated with a version of a
content. It is responsible to manage the different version of the
content. A versioned content must not be used to store any content
data (this must be done on the version of the content).

.. autointerface:: silva.core.interfaces.content.IVersionedContent


Operations
----------

Two different :term:`Zope adapter` let you manage the version and
versioned contents.

Managing version
~~~~~~~~~~~~~~~~

On a :py:interface:`~silva.core.interfaces.content.IVersion`, you can
use the following :term:`Zope adapter` to manage and access information:

.. autointerface:: silva.core.interfaces.adapters.IVersionManager

.. versionadded:: 3.0

Managing versioned content
~~~~~~~~~~~~~~~~~~~~~~~~~~

To manage the different versions, and the publication status of a
:py:interface:`~silva.core.interfaces.content.IVersionedContent`, you
can use the following :term:`Zope adapter`:

.. autointerface:: silva.core.interfaces.adapters.IPublicationWorkflow

.. versionadded:: 3.0

