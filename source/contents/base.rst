Creating new Silva content
==========================

.. contents::

Create a new content
--------------------

To create a new Silva content, you need to define a Python class that
inherits from one of the base content class defined in Silva. For
instance :py:class:`Products.Silva.Asset.Asset`:

.. code-block:: python
  :linenos:

  from Products.Silva.Asset import Asset

  class Video(Asset):
      meta_type = 'My Video'

      def get_video_properties(self):
          # Provides some more API on a Video file.
          pass

It is all you need to do. On line 4, you set a ``meta_type``. It is a
Zope 2 concept that is used to uniquely identify your content
type. This class will old all the content related logic and data
associated with your content, and be automatically saved into the ZODB.

.. _creating-a-versioned-content:

Creating a new versioned content
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Versioned content are bit more tricky than regular ones, as they are
composed of two parts: the **content** class, and the **version**
class. The first one implements all content related logic and the
later will just store the data. By doing this, you can have different
versions of your data.

As an example, you can imagine a blog article:

.. code-block:: python
   :linenos:

   from Products.Silva.VersionedContent import VersionedContent
   from Products.Silva.Version import Version

   class ArticleVersion(Version):
       meta_type = 'Blog Article Version'

       def set_processed_text(self, text):
           self.processed_text = text

       def get_processed_text(self):
           # Do things on self.text and set it into processed_text
           return self.processed_text

   class Article(VersionedContent):
       meta_type = 'Blog Article'


On line 14, you define the content class, that will hold later related
logic. On line 4, you define the version class. Note that on line 5,
the version class that actually store the data. Not that the version
class have a different ``meta_type`` than the content class,
``Version`` have been appended to it.


Available Silva bases content classes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

All those base classes are usable to create new content in Silva:

.. class:: Products.Silva.Publishabled.NonPublishable

  Base for content that should not directly appear to the public. You
  need to use this base class with either
  ``OFS.SimpleItem.SimpleItem`` or ``OFS.Folder.Folder`` if you want
  your content to behave like a folder.  You can refer to the
  :py:interface:`~silva.core.interfaces.content.INonPublishable`
  interface for more information.

.. class:: Products.Silva.VersionedContent.VersionedNonPublishable

  Base for versioned content that should not directly appear to the
  public.

.. class:: Products.Silva.Asset.Asset

  ``File`` and ``Image`` are sub-classes of this base class. Refer to
  the :py:interface:`~silva.core.interfaces.content.IAsset` interface
  for more information.

.. class:: Products.Silva.Content.Content

  Base class for simple publicly viewable content.

  Note that to directly use this base class, you still need to inherit
  from ``OFS.SimpleItem.SimpleItem`` or ``OFS.Folder.Folder`` if you
  want your content to behave like a folder.  You can refer to the
  :py:interface:`~silva.core.interfaces.content.IContent` interface for more
  information.

.. class:: Products.Silva.VersionedContent.VersionedContent

  Base class for publicly viewable versioned content. For each
  versioned content, you have to create a version content inheriting
  :py:class:`~Products.Silva.Version.Version`. This should
  be used to effectively store the content data. You can refer to the
  :py:interface:`~silva.core.interfaces.content.IVersionedContent`
  interface for more information.

.. class:: Products.Silva.Version.Version

  Base class for a version of a versioned content. A version have no
  meaning if it is not associated to a versioned content, see
  :py:class:`~Products.Silva.VersionedContent.VersionedContent`.  You
  can refer to :py:interface:`~silva.core.interfaces.content.IVersion`
  interface for more information.

.. class:: Products.Silva.Folder.Folder

  Base class for containers publicly viewable. Refer to the
  :py:interface:`~silva.core.interfaces.content.IFolder` interface for
  more information.

.. class:: Products.Silva.Publication.Publication

  Base class for publicly viewable main application container. Refer
  to the :py:interface:`~silva.core.interfaces.content.IPublication`
  interface for more information.

In addition to those classes, any already existing content is
extendable by using the same technique.

Extending Silva File
~~~~~~~~~~~~~~~~~~~~

Silva File have multiple implementation, depending of which storage
you select in the service files. If you want to extend Silva File, you
need to extend a specific given implementation, and not their base
class.

.. class:: Products.Silva.File.BlobFile

   Implementation for files storing their data into ZODB blobs.

.. class:: Products.Silva.File.ZODBFile

   Implementation for files storing their data directly inside the ZODB.

For site holding large amount of files, it is recommended to extend
:py:class:`~Products.Silva.File.BlobFile`.

Registering a new content with Grok
-----------------------------------

You can register your new content using :term:`Grok`.

Regular content
~~~~~~~~~~~~~~~

After enabling :term:`Grok` in your extension, your content will be
registered for you automatically. You can customize the registration
using :term:`Grok directives<Grok directive>`:

.. code-block:: python
   :linenos:

   from Products.Silva.Publication import Publication
   from silva.core import conf as silvaconf

   class Blog(Publication):
       meta_type = 'Blog'
       silvaconf.icon('blog.png')
       silvaconf.priority(-3)

On line 6, the ``silvaconf.icon`` directive sets  the filename of an
icon file (``GIF`` or ``PNG``) to use as content icon. On line 7, the
priority of the content in the addable menu is modified with the directive
``silvaconf.priority``.


By default Silva will create a content factory for your content.

.. glossary::

   *Content factory*
     A content factory is a function that create a new Zope 2 content
     in Zope 2, and add it to the container specified in parameter.

You can override this default factory with your own function in order
to customize the creation of the content. You have some
responsibilities if you do so, like triggering some :term:`Zope
event`. For example you can customize the creation of the blog content:

.. code-block:: python

   from zope.event import notify
   from zope.lifecycleevent import ObjectCreatedEvent

   def manage_addBlog(container, identifier, title):
       container[identifier] = Blog(identifier)
       # Use the blog instance coming from container, to get acquisition
       blog = container[identifier]
       blog.set_title(title)
       # Do what ever you want with blog
       notify(ObjectCreatedEvent(blog))

On your content you need to use the directive ``silvaconf.factory``
(in the content class) in order to declare your custom factory:

.. code-block:: python

   silvaconf.factory('manage_addBlog')

If you define a factory that is a ZMI add form, declare it on your
content *before* the real Python factory, and use the directive
``silvaconf.zmi_addable`` (in the content class) to make your content
addable from ZMI:

.. code-block:: python

   silvaconf.zmi_addable()

.. warning::

   By convention all :term:`Content factory` are called
   ``manage_add`` + the content class name.

.. warning::

   Due to many acquisition side effect, we strongly recommend **not**
   to define custom :term:`Content factory`, and use the Silva generic
   one. If you need to execute some code on your content after its
   creation, the best is to listen the
   :py:event:`IObjectCreatedEvent<zope.lifecycleevent.interfaces.IObjectCreatedEvent>`
   event to do it.


.. _registring-a-versioned-content:

Versioned content
~~~~~~~~~~~~~~~~~

Registering a new versioned content in Silva works exactly like
registering a regular content, except there is an additional
:term:`Grok directive` is needed to associate the version class to the
content class:

.. code-block:: python
   :linenos:

   from Products.Silva.VersionedContent import VersionedContent
   from Products.Silva.Version import Version
   from silva.core import conf as silvaconf

   class ArticleVersion(Version):
       meta_type = 'Article Version'

   class Article(VersionedContent):
       meta_type = 'Article'
       silvaconf.icon('article.png')
       silvaconf.version_class(ArticleVersion)

The extra directive is used on line 11 and takes as argument the
version class.


.. _adding-a-content-python:

Adding a new content using Python code
--------------------------------------

After you created and registered your content in Zope, you can, with
Python code add a new content in the container of your choice:

.. code-block:: python
   :linenos:

   factory = container.manage_addProduct['silva.app.blog']
   factory.manage_addArticle('identifier', u'My content')

On line 1, you look for the factories registered by your extension,
that you called *silva.app.blog* during the
:ref:`registering-extension-using-grok` of the extension. On line 2,
you invoke the factory to create a new *Article* content, with the
given title and content.

.. note::

   Not only custom content are created and added this way, official
   Silva content as well. For instance, here follows how to add a new
   Silva Folder.

   .. code-block:: python

      factory = container.manage_addProduct['Silva']
      factory.manage_addFolder('folder', u'My Folder')

