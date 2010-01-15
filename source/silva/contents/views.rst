Adding public views
===================

.. contents::

Creating a view
---------------

You can provide Grok views on your content as public views. For
instance, if we take back the example of the blog, in the ``blog.py``
file we can create a public view:

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



The class ``BlogPublicView`` represent the view.  It inherit from
``silva.core.conf.View`` *(line 8)*. The configuration directive
``silvaconf.context`` tells the view for which class it is working
*(line 10)*. You can specify an interface by your content as well
(like *IBlog*).

After you have to create a template associated to that view:

1. Create a directory called ``blog_templates``. It is the name of the
   Python module where the view code is, at which you append
   ``_templates``,

2. In this directory, you can add a file called
   ``silvablogpublicview.pt``, it will be your template. This template
   will be linked to your view class, since it's the same filename
   than your class (in lower case). This file can contains for example:

   .. sourcecode:: xml

        <div> My Blog <tal:replace tal:replace="view/title" /></div>


   The default language used to write templates is ZPT (`Zope Page
   Template`_) where, ``context`` is mapped to the object on which you
   apply the view, and ``view`` to your view class.

   You can define some helpers for your template as methods on your
   view class.


Namespace
---------

In page templates, you have the following variables available:

``context``

   Refer to the content object on which the rendering is done.

``content``

   Refer to the content object which should rendered. It can be
   different than context: if you have a versioned content object it
   will the viewable version of your object (for regular views), or
   the previewable version of your object (for preview).

``view``

   Refer to your view class you defined. You can add method helper on
   your class, and use them in your templates:

   .. sourcecode:: xml

     <tal:repeat tal:repeat="article view/fetch_articles">
         <a href="#"
            tal:attributes="href article/url"
            tal:content="article/title"> Title </a>
     </tal:repeat>


``template``

   Refer to your templates, where you should be able to use
   ``templates/marcos/mymacro`` if you define a macro called
   ``mymacro`` in your template.

Tips
~~~~

- If you don't want to use a template with your view class, you just
  have to define a ``render`` method:

  .. sourcecode:: python
    :linenos:

    class BlogPublicView(silvaviews.View):

         grok.context(Blog)

         def render(self):
             return u'<div> Hello %s !</div>' % self.content.get_value()


  The ``content`` attribute on the class *(line 6)* refer to the
  object to render, like the ``content`` variable available in page
  templates.

- You can execute code in the ``update`` method before the rendering
  of your template (or before the method ``render`` is called). In
  that method you can precompute needed values to render your view,
  and set theirs values in attributes on the class. Afterwards you
  will be able to access those attributes from your template, using
  ``view/attribute_name`` (or from ``render`` method).

- You can use the configuration directive ``grok.templatedir`` in
  your module if you want to put your templates in a different
  directory than ``name-of-your-python-module_templates``.

- You can use different templates engines. The template engine used is
  selected using the extension of your template file. ``.pt`` will be
  rendered by the `Zope Page Template`_ engine. For instance you can
  use Chameleon with the help of `megrok.chameleon
  <http://pypi.python.org/pypi/megrok.chameleon>`_.

- We don't recommend to create views with a template which contains
  only macros designed to be used by other views. Using content
  providers and viewlets for this task will make a cleaner and faster
  code.

- You can use the configuration directive ``grok.name`` to provide a
  name to your view, so it won't be the default one. For instance,
  your can define a new template for RSS on the blog like that:


  .. sourcecode:: python

    class RSSBlogView(BlogPublicView):

         grok.name('rss.xml')

  And after you create the template ``rsssilvablogview.pt`` in the
  ``blog_templates`` directory containing the corresponding code to
  render a RSS view on the content. Since your view inherit of
  ``BlogPublicView``, it will be automatically a view for ``Blog``
  objects, and have all the helpers you defined before.


Getting the URL of an object
----------------------------

In a python code you can do:

.. sourcecode:: python

   from zope.traversing.browser import absoluteURL

   object_url = absoluteURL(self.context, self.request)

Where ``self.context`` if the object you want the URL from, and
``self.request`` is the request for which you want to get the URL for.

In a page template, you can use ``context/@@absolute_url``:

.. sourcecode:: html

   <a href="#"
      tal:attributes="href context/@@absolute_url">My link</a>


.. _Zope Page Template: http://docs.zope.org/zope2/zope2book/ZPT.html
