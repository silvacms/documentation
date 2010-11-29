Creating new Silva content
==========================

.. contents::

Create a new content
--------------------

To create a new Silva content, you need to define a class that inherit
from one of the base content class of Silva, for instance
:py:class:`Products.Silva.Asset.Asset`:

.. code-block:: python
  :linenos:

  from Products.Silva.Asset import Asset

  class Video(Asset):
      meta_type = 'My Video'

      def get_video_properties(self):
          # Provides some more API on a Video file.
          pass

It is all you need to do. On line 4, we set a ``meta_type``. It is a
Zope 2 concept, and should identify uniquely your content type.


Creating a new versioned content
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Versioned content are bit more tricky, as they are composed of two
parts: the content class, and the version class. The first one
implements all content related behavior, and the later will just store
the data.

As example, you can imagine a blog article:

.. code-block:: python
   :linenos:

   from Products.Silva.VersionedContent import VersionedContent
   from Products.Silva.Version import Version

   class ArticleVersion(Version):
       meta_type = 'Blog Article Version'

       def get_processed_text(self):
           # Do things on self.text and set it into processed_text
           return processed_text

   class Article(VersionedContent):
       meta_type = 'Blog Article'



On line 11, we define the content class. On line 4, we define the
version class. Note that on line 5, the version class have a different
``meta_type`` than the content class, ``Version`` have been appended
to it.


Available Silva bases content classes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. XXX the next line doesn't work and need to be changed

.. currentmodule:: silva.core.interfaces.content

All those base classes are usable to create new Silva content:

.. class:: Products.Silva.Publishabled.NonPublishable

  Base for Silva content that should not directly appear to the
  public. You need to use this base class with either
  ``OFS.SimpleItem.SimpleItem`` or ``OFS.Folder.Folder`` depending if
  you wish to a non-folderish or folderish type of content. You can
  refer to the :py:interface:`INonPublishable` interface for more
  information.

.. class:: Products.Silva.Asset.Asset

  ``File`` and ``Image`` are subclasses of this base class. Refer to
  the :py:interface:`IAsset` interface for more information.

.. class:: Products.Silva.Content.Content

  Base class for simple publicly viewable content.

  Note that to directly use this base class, you still to inherit as
  well of ``OFS.SimpleItem.SimpleItem`` or ``OFS.Folder.Folder``
  depending if you wish a non-folderish or folderish type of
  content. You can refer to the :py:interface:`IContent` interface for
  more information.

.. class:: Products.Silva.VersionedContent.VersionedContent

  Base class for publicly viewable versioned content. For each
  versioned content, you have to create a version content inheriting
  of :py:class:`Version<Products.Silva.Version.Version>`, that should
  be used to effectively store the content data. You can refer to the
  :py:interface:`IVersionedContent` interface for more information.

.. class:: Products.Silva.Version.Version

  Base class for a version of a versioned content. A version have no
  meaning if it is not associated to a versioned content, see
  :py:class:`VersionedContent<Products.Silva.VersionedContent.VersionedContent>`.
  You can refer to :py:interface:`IVersion` interface for more
  information.


.. class:: Products.Silva.Folder.Folder

  Base class for containers publicly viewable. Refer to the
  :py:interface:`IFolder` interface for more information.

.. class:: Products.Silva.Publication.Publication

  Base class for publicly viewable main application container. Refer
  to the :py:interface:`IPublication` interface for more information.

Registering a new content with Grok
-----------------------------------

You can register a new content using either :term:`Grok` or :term:`ZCML`.

.. note::

   You have to choose one way, and only way to do it. You cannot mix
   different kind of registration.

Regular content
~~~~~~~~~~~~~~~

After enabling :term:`Grok` in your extension, your content will be
registered for you. You customize the registration using
:term:`Grok directives<Grok directive>`:

.. code-block:: python
   :linenos:

   from Products.Silva.Publication import Publication
   from silva.core import conf as silvaconf

   class Blog(Publication):
       meta_type = 'Blog'
       silvaconf.icon('blog.png')
       silvaconf.priority(-3)

On line 6, the ``silvaconf.icon`` directive sets the pathname to an
icon file (``GIF`` or ``PNG``) to use as content icon. On line 7, the
priority of the content in the addable menu is changed with the help
of ``silvaconf.priority``.


By default Silva will create a factory for your content.

.. glossary::

   *Content factory*
     A content factory is a function that create a new Zope 2 content
     in Zope 2, and add in one container.

However, you can still provide yourself your own factory function to
customize the creation of your content. You have some responsibilities
if you do it, as triggering some :term:`Zope event`:

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

And on your content you need to use the directive
``silvaconf.factory`` (in the content class):

.. code-block:: python

   silvaconf.factory('manage_addBlog')

You can define a factory which is a ZMI add form, declare it on your
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


Versioned content
~~~~~~~~~~~~~~~~~

Registering a new versioned content in Silva works exactly registering
a regular content, except there is an additional :term:`Grok
directive` needed to associate the version class to the versioned
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

The extra directive is used on line 11, and takes as argument directly
the version class.


Registering a new content with ZCML
-----------------------------------

:term:`Grok` registration should be preferred over :term:`ZCML`
registration, but if you wish, you can still use :term:`ZCML` to
register your contents.


Regular content
~~~~~~~~~~~~~~~

In your ``configure.zcml`` file, you need to use the ``silva:content``
ZCML directive to register your content type:

.. code-block:: xml
   :linenos:

   <silva:content
     extension_name="SilvaBlog"
     content=".blog.Blog"
     icon="blog.png"
     />


Versioned content
~~~~~~~~~~~~~~~~~

A versioned content does not use the same ZCML directive than a
regular content to be registered:

.. code-block:: xml
   :linenos:

   <silva:versionedcontent
     extension_name="SilvaBlog"
     content=".article.Article"
     version=".article.ArticleVersion"
     icon="article.png"
     / >


Adding a new content using Python code
--------------------------------------

After you created and registered your content in Zope, you can, in
Python code add a new content in the container of your choice:

.. code-block:: python
   :linenos:

   factory = container.manage_addProduct['silva.app.blog']
   factory.manage_addArticle('identifier', u'My content')

On line 1, we look for the factories of our extension, we called
*silva.app.blog* during the :ref:`registering-extension-using-grok` of
the extension. On line 2, we invoke the factory to create a new
*Article* content.

.. note::

   Not only extension content are added into Zope like this, official
   Silva content as well. For instance to add a new folder, you should
   do:

   .. code-block:: python

      factory = container.manage_addProduct['Silva']
      factory.manage_addFolder('folder', u'My Folder')

