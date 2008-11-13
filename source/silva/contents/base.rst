Creating content objects
========================

To illustrate that, lets create a module called `article.py` in your
product. This module will contain a new content object for Silva:

.. code-block:: python

  from Products.Silva.VersionedContent import CatalogedVersionedContent
  from Products.Silva.Version import CatalogedVersion

  class Article(CatalogedVersionedContent):
      meta_type = 'Silva Blog Article'

  class ArticleVersion(CatalogedVersion):
      meta_type = 'Silva Blog Article Version'

We assume here that you want to create a versioned content object.
Versioned content types consist of a versioned object (``Article`` in
this example) and associated version objects (``ArticleVersion``). The
``meta_type`` is a class attribute identifying a content type in Zope
2, and should be unique for each content type.

We are going to create a second module ``blog.py`` which is going to
contain a non-versioned folderish type:

.. code-block:: python

  from Products.Silva.Publication import Publication

  class Blog(Publication):
      meta_type = 'Silva Blog'


Available bases classes
-----------------------

All these class are located in Silva, ``Products.Silva`` python package.

``Asset.Asset``

  Basic asset, like file (``File.File``) or image (``Image.Image``).

``Folder.Folder``

  Basic folderish item. A Publication (``Publication.Publication``) is
  a subclass of it, and is recommended for main application container.

``VersionedContent.VersionedContent``

  A basic versioned content. You have to create a version object
  inheriting of ``Version.Version``.

``VersionedContent.CatalogedVersionedContent``

  Extension of the basic versioned content to support catalog. The
  version object should inherit of ``Version.CatalogedVersion``.




Use Grok to register your content
---------------------------------

Regular content
~~~~~~~~~~~~~~~

If your content inherit from ``Asset`` or ``Folder``, that's done. All
content in Silva does, so you just have to inherit from one Silva
content to make yours.

You can add some directive in your Python code:

.. code-block:: python

   from Products.Silva.Publication import Publication

   class Blog(Publication):
       meta_type = 'Silva Blog'
       silvaconf.icon(blog.png')           # Set an icon for the content
       silvaconf.factory('manage_addBlog') # Add a Zope factory for your content
       silvaconf.priority(-3)              # Set priority in addable menu

   def manage_addBlog(context, id, title):
       # Regular Zope manage_add function.

You can give more than one factory, and if the first one is a adding
form (the name should finish by ``Form``), it will be used to create
the content in ZMI using the adding menu.

If you don't provide a factory, a default one will be created for
you. It will create your content giving `id` to the constructor, and
set `title` on it using the Silva method ``set_title``.

Versioned content
~~~~~~~~~~~~~~~~~

This work like a regular content, your content have to inherit of
VersionedContent. You have to specify in this last one which class
should be used as version of your content:

.. code-block:: python

   from Products.Silva.VersionedContent import VersionedContent
   from Products.Silva.Version import Version

   class ArticleVersion(Version):
      ...

   class Article(VersionedContent):
      ...                                  # Other registration directives
      silvaconf.versionClass(ArticleVersion)


Like for regular content, you can as well provide a factory for your
version content, *in* ``Article``:

.. code-block:: python


      silvaconf.versionFactory('manage_addArticleVersion')


Use ZCML to register your content
---------------------------------

Regular content
~~~~~~~~~~~~~~~

In your ``configure.zcml`` add this to register your content type:

.. code-block:: xml

  <silva:content
    extension_name="SilvaBlog"
    content=".blog.Blog"
    icon="blog.png"
    />

The `icon` attribute may be left out.

Versioned content
~~~~~~~~~~~~~~~~~

In your ``configure.zcml`` add this to register your versioned content
type:

.. code-block:: xml

  <silva:versionedcontent
    extension_name="SilvaBlog"
    content=".article.Article"
    version=".article.ArticleVersion"
    icon="article.png"
    />

Again, `icon` is optional.


.. note::

   There are attributes of these zcml directives that aren't covered
   in this document.  To learn more, read the definitions in
   ``silva.core.zcml.directives``.
