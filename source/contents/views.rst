Rendering your content
======================

You can display your content by writing a view. This follows the well
know pattern `Model-View-Controller`_.

.. contents::

.. py:module:: silva.core.views.views

.. _creating-a-default-view:

Creating a default view
-----------------------

A :term:`view` class is able to display (or render) your content as an
HTML page. It will have to display *only* your content. In the case of
the default view, the layout system will include the :term:`layout`
around the HTML produced by the view with the help of a default
:term:`page`.

.. py:class:: silva.core.views.views.View

   Base class used to create new :term:`view`.

   .. method:: update()

      This method is called before the template is rendered. You can
      override this method in order to compute values to use in your
      template.

   .. method:: render()

      This method renders the template associated with the view. You
      can override it if you don't want to use a template to render
      your content, but Python code instead. You need to return a
      *unicode* Python string.


As example for our ``silva.app.blog`` extension, in the ``blog.py``
file you can define:

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


On line 8, the class ``BlogView`` implement your view. It inherits
from ``silva.core.views.views.View`` (aliased as ``silvaviews.View``
on line 2).

On line 9, the :term:`Grok directive` :py:func:`grok.context` tells
the view which content to render, here the ``Blog`` content type.

You can associate a page template to your view class, it will be
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
`Zope Page Template`_. You can refer for the `Zope Page Template`_
documentation for more information about using the template language.

You might also want to use the ``.cpt`` extension which stands for
`Chameleon Page Template`_. It is really close to the `Zope Page Template`_
template language but has a more modern implementation and you may find
it more flexible.


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
   and not the versioned content itself, that is ``context``. To
   collect information to display, you should always use this
   ``content`` instead of ``context``.

``request``
   Refers to the Zope request object.

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

- You can use the configuration directive :py:func:`grok.templatedir`
  in your module if you want to put your templates in a different
  directory than ``name-of-your-python-module_templates``.

- You can use a different templating system. The templating system
  used is selected using the extension of your template file. ``.pt``
  will be rendered by the `Zope Page Template`_ engine. You could use
  for instance Chameleon with the help of `grokcore.chameleon
  <http://pypi.python.org/pypi/grokcore.chameleon>`_, that provides Zope
  Page Template-like and Genshi-like templating system as well.

.. note::

   In the `Model-View-Controller`_ pattern, your template would be the
   *View* as it render the content as HTML (or other), the view class
   would be the *Controller* as it contains the logical code needed by
   the *View* and can modify the content depending on action triggered
   in the *View* and your content would be the *Model*.


Adding more pages using the site layout
---------------------------------------

You can write a new standalone :term:`page` on your content. Like for
your default :term:`view` class, your :term:`page` will have *only* to
render the content HTML, and the :term:`layout` will be added around
it.

.. py:class:: silva.core.views.views.Page

    Base class used to create new :term:`page`.

   .. method:: update()

      This method is called before the template is rendered. You can
      override this method in order to compute values to use in your
      template.

   .. method:: render()

      This method renders the template associated with the page. You
      can override it if you don't want to use a template to render
      your content, but Python code instead. You need to return a
      *unicode* Python string.

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

- On line 2, we use the :term:`Grok directive` :py:func:`grok.context`
  to associate our page to the ``Blog`` content type.

- On line 3, we use the :term:`Grok directive` :py:func:`grok.name` to
  name our page ``post.html``. Unlike the default view of a content,
  you need to alaways given a name to page in order to be able to
  access it.

- On line 4, we use the :term:`Grok directive` :py:func:`grok.require`
  to require the user to have at least the security permission
  ``silva.ChangeSilvaContent`` to be able to see this page. You can
  refer to :ref:`available-permissions` to have a full listing of all
  available permissions. If you don't specify any security
  restriction, the default permission required to see a page (or a
  view) is ``zope2.View``.

- On line 6, in the ``update`` method, we take two parameters that
  could have been posted by a HTML form, to create an article in the
  Blog content type.

For all other details, a ``silvaviews.Page`` provides the same API
than a ``silvaviews.View``.

.. note::

   The default view for a content is not built using a page, as extra
   logic is required for it. A default page is already available taking
   care of this logic, and looking for a view to render the content.


Adding more views not using the site layout
-------------------------------------------

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
   content versioning system.


.. _adding-resources-to-a-view:

Adding specific resources to a view
-----------------------------------

.. py:module:: silva.fanstatic

It is possible to include specific resources, like CSS or Javascript
files only in a given view. To do this you can define an interface to
which you associated the resources you want to include and use the
function ``need`` to include them in your view:

.. code-block:: python
   :linenos:

   from silva.core import conf as silvaconf
   from silva.fanstatic import need
   from zope.publisher.interfaces.browser import IDefaultBrowserLayer

   class IViewResources(IDefaultBrowserLayer):
       silvaconf.resources('view.js')
       silvaconf.resources('view.css')

   class MyView(silvaviews.View):

       def update(self):
           need(IViewResources)


- On line 4 to 6 we define a new :term:`Zope interface` to which we
  associate the resources ``view.js`` and ``view.css``. The
  corresponding resources files should exists inside a ``static``
  located in the same folder than the Python module source file.

- On line 11 we include the resources associated with the :term:`Zope
  interface` previously defined with the help of
  :py:func:`~silva.fanstatic.need` inside the ``update`` method.

The resources will always be included, independently of the selected
Silva theme. You can include in the same fashion resources to a
:term:`page` or any other components generating HTML to the end user.


.. py:function:: need(resources)

   Include the given resources in the rendering of the page.

.. seealso::

   This mechanism is similar to the inclusion process of resources
   inside a Silva theme. For more information please refer to
   :ref:`inclusion-of-resources`.


Getting the URL of a content
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


Getting the top level content of the current site
-------------------------------------------------

The top level content of your site might not be the Silva Root
content, if you created a sub-site using a publication for instance.

From a python file, you can use the
:py:interface:`silva.core.views.interfaces.IVirtualSite` adapter on
the request object:

.. code-block:: python

   from silva.core.views.interfaces import IVirtualSite

   class MyView(silvaviews.View):

       def update(self):
           site_info = IVirtualSite(self.request)
           self.root = site_info.get_root()
           self.root_url = site_info.get_root_url()


.. warning::

   Using the method ``get_root`` from :py:interface:`IRoot` will not
   give you this top level content.


.. _Zope Page Template: http://docs.zope.org/zope2/zope2book/ZPT.html
.. _Model-View-Controller: http://en.wikipedia.org/wiki/Model-View-Controller
.. _Chameleon Page Template: http://chameleon.readthedocs.org/en/latest/
