
Content Objects
===============

Contents objects are the main objects in Silva. They usually are
created by the authors and editors through the :term:`SMI`, are
edited, and published to make them available to the public when they
are ready.

.. contents::

Content
-------

.. autointerface:: silva.core.interfaces.content.IPublishable

.. autointerface:: silva.core.interfaces.content.IContent


Default Silva content
---------------------

Few default content in Silva are just simple contents. It's the case
of automatic table of contents and indexer contents.

.. autointerface:: silva.core.interfaces.content.IAutoTOC

.. autointerface:: silva.core.interfaces.content.IIndexer

Operations
----------

A Silva indexer will automatically index entries provided by Silva
content. In order to be indexed by a Silva indexer content, your
content need to implement, or provide a :term:`Zope adapter` for this
interface:

.. autointerface:: silva.core.interfaces.adapters.IIndexEntries

