Rendering your content to the public
====================================

You can display your content to the public by writing a view. This
follows the well know pattern `Model-View-Controller`_.

.. contents::

.. _creating-a-default-view:

Creating a default view
-----------------------

A view class is able to display (or render) your content as an HTML
page. It will have to display *only* your content, the layout system
will include the layout around the HTML produced by the view.

As example, in a ``blog.py`` file you can define:

.. code-block:: python
   :linenos:

   from Products.Silva.Publication import Publication
   from silva.core.views import views as silvaviews
   from five import grok

   class Blog(Publication):
       pass

   class BlogView(silvaviews.View):
       grok.context(Blog)

       def title(self):
            return self.content.get_title()

On line 5, the class ``BlogView`` implement your view. It inherits
from ``silva.core.views.views.View`` (aliased as ``silvaviews.View``,
*line 8*).

On line 10, the configuration directive ``grok.context`` tells the
view which content to render, here the ``Blog`` content type.

You can associate a page template to your view class, that will be
used to generate the HTML output:

1. Create a template directory called ``blog_templates``. The name of
   the directory is prefixed with the module name where your view
   class is located, ``blog`` to which is appended ``_templates``.

2. In this directory, you add a template in a file called
   ``blogview.pt``. As the name of the file (minus the extension)
   correspond to the class name of your view (in lower case), they
   will be linked together.

   .. code-block:: html

      <div>My Blog <tal:replace tal:replace="view/title" /></div>

As your template have the extension ``.pt``, it will interpreted as a
`Zope Page Template`_.

Template Namespace
~~~~~~~~~~~~~~~~~~

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


Tips about Views
~~~~~~~~~~~~~~~~

- If you don't want to use a template with your view class, just
  define a ``render`` that return the HTML as a an unicode string:

  .. sourcecode:: python
    :linenos:

    class BlogPublicView(silvaviews.View):
        grok.context(Blog)

        def render(self):
            return u'<div> Hello %s !</div>' % self.content.get_value()

  The ``content`` and ``context`` attribute on the view have the same
  meaning than in the template.

- You can execute complex code in an ``update`` method before your
  template is rendered (or the method ``render`` called). In that
  method you can pre-compute values, and set them on attributes of the
  view class. From the template, you will be able to access those
  attributes, using ``view/attribute_name``.

- You can use the configuration directive ``grok.templatedir`` in
  your module if you want to put your templates in a different
  directory than ``name-of-your-python-module_templates``.

- You can use a different templating system. The templating system
  used is selected using the extension of your template file. ``.pt``
  will be rendered by the `Zope Page Template`_ engine. You could use
  for instance Chameleon with the help of `megrok.chameleon
  <http://pypi.python.org/pypi/megrok.chameleon>`_, that provides Zope
  Page Template-like and Genshi-like templating system as well.

.. note::

   In the `Model-View-Controller`_ pattern, your template would be the
   *View* as it render the content as HTML (or other), the view class
   would be the *Controller* as it contains the logical code needed by
   the *View* and can modify the content depending on action triggered
   in the *View* and your content would be the *Model*.


Adding more views with the layout
---------------------------------

You can write a new page on your content using a
``silva.core.views.views.Page`` (alias to ``silvaviews.Page`` in the
example below). Like for your view class, your page will have *only*
to render the content HTML, and the layout will be added around it.

Following the previous example given in
:ref:`creating-a-default-view`, you can create a new page
``post.html`` on your content:

.. code-block:: python
   :linenos:

   class PostBlogView(silvaviews.Page):
       grok.context(Blog)
       grok.name('post.html')
       grok.require('silva.ChangeSilvaContent')

       def update(self, title=None, text=None):
           if title is not None and text is not None:
               # Create a new article using title and text
               pass

- On line 2, we says that our page will be available on ``Blog`` content type.

- On line 3, we says that it will be called ``post.html``.

- On line 4, we require the user to have at least the security
  permission ``silva.ChangeSilvaContent`` to be able to see this
  page. You can refer to :ref:`available-permissions` to have a full
  listing of all available permissions. If you don't specify any
  security restriction, the default permission required to see a page
  (or a view) is ``zope2.View``.

- On line 6, in the ``update`` method, we take two parameters that
  could have been posted by a HTML form, to create an article in the
  Blog content type.

For all other details, a ``silvaviews.Page`` provides the same API
than a ``silvaviews.View``.


Adding more views without the layout
------------------------------------

If you write a ``silvaviews.View`` and give a name to it, using the
Grok directive ``grok.name``, like, ``view.html`` it will make it
available directly available to the user at the URL
``content-url/view.html``. This won't include the layout.

A common use-case would be for instance to write a custom RSS view on
your content, which not being HTML at all should not be wrapped into
your site layout.

For instance, you can inherit from your default view (since it is a
``silvaviews.View``) defined previously:

.. code-block:: python

   class RSSBlogView(BlogPublicView):
       grok.name('rss.xml')

With it, you need to create the template ``rsssilvablogview.pt`` in
the ``blog_templates`` directory containing the corresponding template
code needed to render the custom RSS feed.

.. note::

   The fact you use ``silvaviews.View`` instead of ``silvaviews.Page``
   to render your content as HTML to the public comes from
   compatibility issues with the old ZODB based layout system and the
   content versionning system.


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


Getting the content object of the current site
----------------------------------------------

The root content if your site might not be the Silva root, if you
create sub-site using publication, so the method ``get_root`` will not
work.

From a python file, you can use the
:py:interface:`silva.core.views.interfaces.IVirtualSite` adapter:

.. code-block:: python

   from silva.core.views.interfaces import IVirtualSite

   class MyView(silvaviews.View):

       def update(self):
           site_info = IVirtualSite(self.request)
           self.root = site.get_root()
           self.root_url = site.get_root_url()


.. _Zope Page Template: thtp://docs.zope.org/zope2/zope2book/ZPT.html
.. _Model-View-Controller: http://en.wikipedia.org/wiki/Model-View-Controller
