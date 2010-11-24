Rendering your content to the public
====================================

.. contents::

Creating a default view
-----------------------

A view class is able to display (or render) your content to the
public. It will have to display *only* your content, the layout system
will include the layout around the HTML produced by the view.

As example, in a ``blog.py`` file you can define:

.. code-block:: python
   :linenos:

   from Products.Silva.Publication import Publication
   from silva.core.views import views as silvaviews
   from five import grok

   class Blog(Publication):
       pass

   class BlogPublicView(silvaviews.View):
       grok.context(Blog)

       def title(self):
            return self.content.get_title()

The class ``BlogPublicView`` represents your view. It inherits from
``silva.core.views.views.View`` (aliased as ``silvaviews.View``, *line 8)*.

The configuration directive ``grok.context`` tells the view which
content to render *(line 10)*. You can specify either directly the
content class or an interface implemented by this last one.

You can associate a page template to your view class, that will be
rendered, using that last one. To do this you need to:

1. Create a directory called ``blog_templates``. The name of the
   directory is prefixed with the module name where your view class is
   located: ``blog``. Append to the module name ``_templates``.

2. In this directory, you can add a file called
   ``silvablogpublicview.pt``, this will be your template file. It
   will be linked to your view class, since its filename is the same
   as your view class (in lower case). For instance, it can contains:

   .. sourcecode:: xml

      <div>My Blog <tal:replace tal:replace="view/title" /></div>

   This is the Zope Page Template Markup language, called ZPT for
   short.


Template Namespace
------------------

In page templates, you have access to the following variables:

``context``
   Content object on which the rendering is done. This is the same as
   the ``context`` attribute on the view.

``content``
   Content or version that should be rendered. This is different than
   ``context``, in that in case of a versioned content, it will be the
   published version (or editable version if you are previewing it),
   and not the versioned content itself. To collect information to
   display, you should always use this ``content`` instead of
   ``context``.

``view``
   Refers to your view class to which the template is associated. You
   can add method helper on your class, and use them in your
   template. For instance, if you defined a method ``fetch_articles``
   on your view, you can in your template use it:

   .. code-block:: xml

      <tal:repeat tal:repeat="article view/fetch_articles">
        <a href="#"
          tal:attributes="href article/url"
          tal:content="article/title"> Title </a>
      </tal:repeat>

``template``
   Refers to your template (this can be used if you define TAL macros
   in your template, to reuse them in that same template).

.. warning::

   TAL macros are difficult to maintain and by so should be avoided to
   share template code between two views. In a good design, a
   :term:`Content Provider` or a :term:`Viewlet` can accomplish the
   same thing more neatly.


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

  The ``content`` attribute on the class *(line 5)* refers to the
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

Adding more views with the layout
---------------------------------

XXX

Adding more views without the layout
------------------------------------

XXX

Getting the URL of an object
----------------------------

From a python file, you can use the function ``absoluteURL``:

.. code-block:: python

   from zope.traversing.browser import absoluteURL

   absoluteURL(self.context, self.request)


``self.context`` is the object you want the URL from, and
``self.request`` is the request for which you want to get the URL for.

In a view class, you can use the ``url`` method, that return the URL
for the given object, or the current one if none is given:

.. code-block:: python

   class MyView(silvaviews.View):

       def update(self):
           self.my_url = self.url()
           self.publication_url = self.url(self.context.get_publication())


From a page template, you can use the view ``absolute_url``:

.. code-block:: html

   <a href="#"
      tal:attributes="href context/@@absolute_url">My link</a>


.. warning::

   The Zope 2 method ``absolute_url`` on a content object is
   deprecated and should not be used anymore.


Getting the root object of the current site
-------------------------------------------

From a python file, you can use the
:py:interface:`silva.core.views.interfaces.IVirtualSite` adapter:

.. code-block:: python

   from silva.core.views.interfaces import IVirtualSite

   class MyView(silvaviews.View):

       def update(self):
           site_info = IVirtualSite(self.request)
           self.root = site.get_root()
           self.root_url = site.get_root_url()


.. _Zope Page Template: http://docs.zope.org/zope2/zope2book/ZPT.html
