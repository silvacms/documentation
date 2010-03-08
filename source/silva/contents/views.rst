Adding public views
===================

.. contents::

Creating a view
---------------

To show your content to the public we'll use Grok views. For instance
if we take our blog content type illustrated here :ref:`base` we can
create a public view like this:

.. sourcecode:: python
   :linenos:

   from Products.Silva.Publication import Publication
   from silva.core.views import views as silvaviews
   from five import grok

   class Blog(Publication):
       ...

   class BlogPublicView(silvaviews.View):

       grok.context(Blog)

       def title(self):
            return self.content.get_title()

The class ``BlogPublicView`` represents your view. It inherits from
``silva.core.conf.View`` (aliased as ``silvaviews``) *(line 8)*. The
configuration directive ``grok.context`` tells the view which class to
work on *(line 10)*. You can specify an interface with your content as
well (for example *IBlog*, assuming you have created and IBlog
interface that has been implemented in the Blog class).

Now you can create a template that will use your view.

1. Create a directory called ``blog_templates``. The name of the
   directory is prefixed with the module name where your view code is
   located ``blog``. Append to the module name ``_templates``.

2. In this directory, add a file called ``silvablogpublicview.pt``,
   this will be your template. This template will be linked to your
   view class, since it's the same filename as your class (in lower
   case). This file contains:

   .. sourcecode:: xml

      <div>My Blog <tal:replace tal:replace="view/title" /></div>

   The default language used to write templates is ZPT (`Zope Page
   Template`_) where, ``context`` is mapped to the object on which you
   apply the view, and ``view`` to your view class.

   You can define some helpers for your template as methods on your
   view class.

Namespace
---------

In page templates, you have the following variables available:

``context``

   Refers to the object request in the URL, for example in the
   :ref:`base` code this would be a blog Article.

``content``

   Refers to the object on which the view is actually registered,
   which may not be the same as context. For example in :ref:`base`
   code a blog ArticleVersion.

``view``

   Refers to your view class that you defined above. You can add
   method helper on your class, and use them in your templates:

   .. sourcecode:: xml

      <tal:repeat tal:repeat="article view/fetch_articles">
        <a href="#"
          tal:attributes="href article/url"
          tal:content="article/title"> Title </a>
      </tal:repeat>

``template``

   Refers to your template.

Tips
~~~~

- If you don't want to use a template with your view class, just
  define a ``render`` method:

  .. sourcecode:: python
    :linenos:

    class BlogPublicView(silvaviews.View):

        grok.context(Blog)

        def render(self):
            return u'<div> Hello %s !</div>' % self.content.get_value()

  The ``content`` attribute on the class *(line 6)* refers to the
  object to render, like the ``content`` variable available in page
  templates.

- You can execute code in an ``update`` method before the rendering of
  your template (or before the method ``render`` is called). In that
  method you can precompute needed values to render your view, and set
  the values in attributes on the class. Afterwards you will be able
  to access those attributes from your template, using
  ``view/attribute_name`` (or from ``render`` method).

- You can use the configuration directive ``grok.templatedir`` in
  your module if you want to put your templates in a different
  directory than ``name-of-your-python-module_templates``.

- You can use a different templates engine. The template engine used
  is selected using the extension of your template file. ``.pt`` will
  be rendered by the `Zope Page Template`_ engine. You could use for
  instance Chameleon with the help of `megrok.chameleon
  <http://pypi.python.org/pypi/megrok.chameleon>`_.

- We don't recommend to create views with a template which contains
  only macros designed to be used by other views. Using content
  providers and viewlets for this task will make cleaner and faster
  code.

- You can use the configuration directive ``grok.name`` to provide a
  name to your view, so it won't be the default one. For example, you
  can define a new template for RSS on the blog like this:

  .. sourcecode:: python

    class RSSBlogView(BlogPublicView):

         grok.name('rss.xml')

  After you create the template ``rsssilvablogview.pt`` in the
  ``blog_templates`` directory containing the corresponding code to
  render an RSS view on the content. Since your view inherits
  ``BlogPublicView``, it will automatically be a view for ``Blog``
  objects, and have all the helpers you defined before.

Getting the URL of an object
----------------------------

In python code you can do:

.. sourcecode:: python

   from zope.traversing.browser import absoluteURL

   object_url = absoluteURL(self.context, self.request)

``self.context`` is the object you want the URL from, and
``self.request`` is the request for which you want to get the URL for.

In a page template, you can use ``context/@@absolute_url``:

.. sourcecode:: html

   <a href="#"
      tal:attributes="href context/@@absolute_url">My link</a>

.. _Zope Page Template: http://docs.zope.org/zope2/zope2book/ZPT.html
