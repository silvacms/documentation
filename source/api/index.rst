Silva Application Programming Interface
=======================================

All the API used the following Zope concepts:

.. glossary::

   *Zope interface*
     An interface is a Zope concept used to describe what an object
     (or a class) provides (or implements) as methods and attributes,
     and what are theirs signatures (argument list). This is just a
     verified definition of the API that an object provides.

   *Zope adapter*
     An adapter provides additional behavior (i.e. attributes and
     methods) to a content (or more generically an object) it
     adapts. In practice, for a content providing a :term:`Zope
     interface`, they can provides a different one. In order to
     retrieve an adapter, you can refer to
     :ref:`query-a-zope-adapter`.

   *Zope utility*
     An utility is a non-content related object that provides a set of
     non-content related methods, or configuration attributes. In
     Silva, all *services* are utilities. Utilities are identified by
     the :term:`Zope interface` they implements. in order to retrieve
     an utility, or a Silva service, you can refer to
     :ref:`query-a-zope-utility`.

   *Zope Component Architecture*
     The Zope Component Architecture, or *ZCA* for short, is the
     object oriented development paradigm that is principally composed
     of the :term:`Zope interface`, :term:`Zope adapter` and
     :term:`Zope utility` concepts.



In Silva, all content object implements a :term:`Zope interface`,
which describe their *official API*. Some other Silva functionality
are obtained via :term:`Zope adapter`, by adapting a content
object. Silva services are in fact :term:`Zope utility`.


.. toctree::
   :maxdepth: 2

   zca
   objects
   members
   contents
   versions
   assets
   folders
   events
