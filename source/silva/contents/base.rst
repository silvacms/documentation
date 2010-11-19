.. _base:

Creating content objects
========================

Okay, the favorite. Lets make a simple blog in Silva!

To start we'll create a module called 'article.py'. in the products
directory of your Builout tree. The article.py will function as a blog
article.

.. code-block:: python

  from Products.Silva.VersionedContent import CatalogedVersionedContent
  from Products.Silva.Version import CatalogedVersion

  class Article(CatalogedVersionedContent):
      meta_type = 'Silva Blog Article'

  class ArticleVersion(CatalogedVersion):
      meta_type = 'Silva Blog Article Version'

The first thing we do here is import two Classes that handle cataloged
content meaning it will be 'searchable'.

.. note::

   Cataloging has nothing to do with *saving* objects.

Since most blogs allow for searchable content, we'll create that same
functionality for our example.

Versioned content types consist of a versioned object, in this example
``Article``, which is associated to versions of an object
(``ArticleVersion``). ``meta_type`` is a class attribute that
identifies a content type in Zope2. The ``meta_type`` class attribute
should be unique for each content type you create.

Now lets go on to create another module call 'blog.py'. In the
``blog.py`` module we'll create a non-versioned 'container' or
'folderish' type (see :ref:`folders`).

.. code-block:: python

  from Products.Silva.Publication import Publication

  class Blog(Publication):
      meta_type = 'Silva Blog'

Available Silva bases classes
-----------------------------

All Silva base classes are located in Silva, ``Products.Silva``.

.. toctree::
   :maxdepth: 1

   api/assets
   api/folders
   api/versions

``Asset.Asset``
  ``File`` and ``Image`` are subclasses of the ``Assets`` class.

``Folder.Folder``
  Base for Silva folderish item. A Publication
  (``Publication.Publication``) is a subclass of ``Folder``, and is
  recommended for your main application container.

``Content.Content``
  Base for non Silva folderish content. Note that to directly use this
  base class, you still to inherit as well of
  ``OFS.SimpleItem.SimpleItem`` or ``OFS.Folder.Folder``.

``VersionedContent.VersionedContent``
  Base for versioned content. For each versioned content, you have to
  create a version content inheriting of ``Version.Version``.


Registering your content
------------------------

To register our content type we'll use the ``silva.core.conf``
package.

``silva.core.conf`` is an extension which lets you configure and
register Silva contents and extensions using either Grok or ZCML.

Regular content
~~~~~~~~~~~~~~~

All content in Silva inherits from ``Asset`` or ``Folder`` so when
making your own content you simply have to inherit from a Silva
content object.

Lets add some directives in your Python code:

.. code-block:: python

   from silva.core import conf as silvaconf
   from Products.Silva.Publication import Publication

   class Blog(Publication):
       meta_type = 'Silva Blog'
       silvaconf.icon('blog.png')          # Set an icon for the content
       silvaconf.priority(-3)              # Set priority in addable menu


If you don't provide a factory, a default one will be created for
you. It will create your content giving `id` to the constructor, and
set `title` on it using the Silva method ``set_title``.

.. You can give more than one factory, and if the first one is a adding
.. form (the name should finish by ``Form``), it will be used to create
.. the content in ZMI using the adding menu.


Versioned content
~~~~~~~~~~~~~~~~~

This works like regular content, your content must inherit from
VersionedContent. You must specify which class should be used as
version of your content:

.. code-block:: python

   from silva.core import conf as silvaconf
   from Products.Silva.VersionedContent import VersionedContent
   from Products.Silva.Version import Version

   class ArticleVersion(Version):
       ...

   class Article(VersionedContent):
       ...
       # Register a directive.
       silvaconf.versionClass(ArticleVersion)

Just like for regular content, you can provide a factory for your
versioned content, *in* ``Article``:

.. code-block:: python

   class Article(VersionedContent):
       ...
       # Register a factory.
       silvaconf.versionFactory('manage_addArticleVersion')

Use ZCML to register your content
---------------------------------

Regular content
~~~~~~~~~~~~~~~

First in the products directory you must create a 'configure.zcml'
file this file will register your new content type with Zope.

.. code-block:: xml

   <configure
     xmlns="http://namespaces.zope.org/zope"
     xmlns:five="http://namespaces.zope.org/five"
     xmlns:silva="http://infrae.com/ns/silvaconf"
     xmlns:browser="http://namespaces.zope.org/browser">

   </configure>

This ``zcml`` file will not work because we haven't actually connected
anything. But each ``configure.zcml`` must have at least the these
tags.

Now in the ``configure.zcml`` add this to register your content type:

.. code-block:: xml

  <silva:content
    extension_name="SilvaBlog"
    content=".blog.Blog"
    icon="blog.png"/>

Versioned content
~~~~~~~~~~~~~~~~~

Now lets add the versioned content to your ``configure.zcml``.

.. code-block:: xml

  <silva:versionedcontent
    extension_name="SilvaBlog"
    content=".article.Article"
    version=".article.ArticleVersion"
    icon="article.png"
    />

.. note::

   There are attributes of these zcml directives that aren't covered
   in this document.  To learn more, read the definitions in
   ``silva.core.zcml.directives``.
