Silva Application Programming Interface
=======================================

The Silva API uses the following Zope concepts:

.. glossary::

   *Zope interface*
     An interface is a Zope concept used to describe what an object
     (or a class) provides (or implements) as methods and attributes,
     and what are theirs signatures (argument list). This is just a
     definition of the API that an object provides, that can be
     tested.

     It is defined with the help of a regular Python class that
     inherit from ``zope.interface.Interface``. By convention, they
     are always defined in a Python module called ``interfaces`` (i.e
     in a file called ``interfaces.py``).

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


To use the Silva API you need to retrieve and use Zope components:

.. toctree::
   :maxdepth: 2

   zca

Each content in Silva is based on a base class, defining its core
features. Management operations on them are done using :term:`Zope
adapter`:

.. toctree::
   :maxdepth: 2

   objects
   contents
   versions
   assets
   folders

Documentation on services and extra classes:

.. toctree::
   :maxdepth: 2

   services
   members
   events
   errors
