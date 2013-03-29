
Creating a Silva theme
======================

A Silva theme is built as a Silva extension. So the first step to
create a Silva theme is to create a new Silva extension. By
convention, all the Silva extensions containing only themes are called
``silvatheme.name`` where ``name`` is the name picked for the theme.

In order to create the extension, you can follow the generic
guidelines, :ref:`creating-an-extension`.

.. note::

   If your extension only contains a Silva theme then you don't need
   to install your Silva extension with ``service_extension`` and can
   bypass its registration to it.

.. contents::

As an example in this documentation you can create a Silva extension
called ``silvatheme.blogtheme``. After creation, the empty Silva
extension should contains the following files::

  setup.py
  src/silvatheme/
  src/silvatheme/__init__.py
  src/silvatheme/blogtheme/
  src/silvatheme/blogtheme/__init__.py
  src/silvatheme/blogtheme/configure.zcml


Creation of a layer and a skin
------------------------------

The first task in creating a theme is to create a :term:`layer` and
:term:`skin` so that our resources and templates can be registered on
our theme. Since those are :term:`Zope interface`, they should by
convention be defined inside a file called ``interfaces.py``.

.. interface:: silva.core.layout.interfaces.ISilvaLayer

   Base interface for a :term:`layer` that associate themes components
   together.

.. interface:: silva.core.layout.interfaces.ISilvaSkin

   Base interface for a :term:`skin` that regroup one or multiple
   :term:`layer`.

In case of the ``silvatheme.blogtheme`` example, they should be added
to a file called ``src/silvatheme/blogtheme/interfaces.py`` like this:

.. code-block:: python
   :linenos:

   from silva.core import conf as silvaconf
   from silva.core.layout.interfaces import ISilvaSkin, ISilvaLayer


   class IBlogLayer(ISilvaLayer):
       """Blog layer used to attach resources.
       """

   class IBlogTheme(IBlogLayer, ISilvaSkin):
       """Blog skin.
       """
       silvaconf.skin('Blog theme')


- Line 6 define a :term:`layer` as a Python class. It inherit from the
  base interface ``ISilvaLayer``.

- Line 10 define the :term:`skin` as a Python class. It inherit form
  the previously defined :term:`layer` to include its elements and of
  the interface ``ISilvaSkin`` that defines it as a :term:`skin`.

- Line 13 register the :term:`skin` in Silva with the help of the
  :term:`Grok directive` :py:func:`grok.skin` (imported via
  ``silva.core.conf`` here on line 1). This will make the skin
  selectable as a desktop skin in the Settings of the :term:`SMI`.


Adding file resources
---------------------

To add file resources to your theme, like CSS, Javascripts files or
images, you can create a directory called ``static`` in your Silva
extension. Every file and sub-directories inside this ``static``
directory will be automatically exported as resources through Silva.

In the ``silvatheme.blogtheme`` example, you can create a directory
called ``src/silvatheme/blogtheme/static`` and add a file called
``blog.css`` inside it containing the CSS you wish to apply.


Automatic inclusion of resources in a layer
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can automatically include CSS and Javascript files in the HTML of
your theme by registring them on a :term:`layer` of your skin. This is
done with the :term:`Grok directive` called ``resource``.

In the ``silvatheme.blogtheme`` example you can automatically include
the ``blog.css`` file by editing the ``interfaces.py`` file like this:

.. sourcecode:: python
   :linenos:

   from silva.core import conf as silvaconf

   class IBlogLayer(IPorto):
       """Blog layer used to attach resources.
       """
       silvaconf.resource('blog.css')


Including resources automatically have multiple advantages:

- In debug mode, the resources will not be cached by either the Zope
  instance or your browser, improving your workflow of editing and
  testing the changes in a Web browser.

- In production mode, the resources will be merged and minified
  version will be used, in order to improving the number of requests
  and size of the downloaded resources. In addition to this HTTP
  headers will be set in order to ensure the resources are cached by
  Web browser and proxies and not requested multiple times.

- The resources are included in a fashion that make possible to easily
  refer other resources directly from them. For instance in a CSS file
  you can include a background image available in the ``static``
  directory like this:

  .. code-block:: css

     background-image: url('background.png');


Getting the URL of a resource from a template
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Sometime it is useful to get the direct URL to a resource in order to
include it in the HTML of your theme for instance.

Any components :term:`layout`, :term:`view`, :term:`page`,
:term:`content provider`, :term:`viewlet manager` or :term:`viewlet`
have an attribute ``static`` that behave like a Python
dictionnary. This attribute let you acccess the files present in the
static directory and retrieve the URL to it by calling it.

For example if from a Python code you want to get the URL of an image:

.. code-block:: python

   def get_reply_image(self):
       return self.static['image_folder']['reply.png']()

The ``static`` attributes is available as an option through a page
template as well. With a `Chameleon Page Template`_ for instance:

.. code-block:: html

   <img src="${static['image_folder']['reply.png']()}" alt="Reply" />


Defining a layout
-----------------

In order to render the HTML page with the content coming from Silva
inside it, you need to provide an HTML template as :term:`layout` for
your theme. It will be the HTML skeleton of your HTML pages in which
the content coming from Silva will be inserted.

.. class:: silva.core.views.views.Layout

   Base class used to create a layout.

   .. method:: update()

      This method is called before the template is rendered. You can
      override this method in order to compute values to use in your
      template.

You will be able to associate your layout to the layer of your theme
with the :term:`Grok directive` :py:func:`grok.layer`. Optionaly you
can define multiple layout and associate some of them to specific
content type with the :term:`Grok directive` :py:func:`grok.context`.

For the ``silvatheme.blogtheme`` example you can define this really
simple layout:

.. code-block:: python
   :linenos:

   from datetime import datetime

   from silva.core import conf as silvaconf
   from silva.core.views import views as silvaviews

   class BlogTheme(silvaviews.Layout):
        silvaconf.layer(IBlogLayer)

        def get_current_year(self):
            return datetime.today().year

- Line 7 associates your :term:`layout` class to your :term:`layer`
  with the :term:`Grok directive` :py:func:`grok.layer` (imported from
  ``silva.core.conf`` on line 3).


To this class you need to associate a template. The rules to associate
a template to a :term:`layout` are exactly the same than for a
:ref:`creating-a-default-view`, so please refer to those rules. In
your template namespace, your layout class will accessible with the
variable `layout`. In order to render the content of the :term:`page`
you are rendering, you will need to call the method ``content()`` of
the page, available through the variable `view`.

For instance you can use the following `Chameleon Page Template`_ with
the :term:`layout` defined above:

.. code-block:: html
   :linenos:

   <html>
     <head>
       <title tal:content="context.get_title()">Title</title>
     </head>
     <body>
      <article tal:content="structure view.content()"></article>
      <footer>Copyrighted blog content 2000-<span tal:replace="layout.get_current_year()"/></footer>
     </body>
   </html>

- Line 6 includes the content of the :term:`page` with the tal
  expression ``structure view.content()``.

- Line 7 uses the method `get_current_year` defined on the example
  ``BlogTheme`` class.


Using a content provider for a flexible inclusion of custom HTML
----------------------------------------------------------------

The concept of :term:`Content provider` can help you to include custom
HTML in a theme on a given :term:`page` (or :term:`view`) and content
type.

.. class:: silva.core.views.views.ContentProvider

   Base class used to create a new :term:`Content provider`, that can
   called to render specific HTML parts. It can be called via a
   template, from any components (:term:`layout`, :term:`view`,
   :term:`page`, :term:`content provider`, :term:`viewlet manager` and
   :term:`viewlet`).

   .. method:: update()

      This method is called before the template is rendered. You can
      override this method in order to compute values to use in your
      template.

   .. method:: render()


      This method is called to render the associated template to the
      :term:`viewlet`. If you do not wish to use a template, you can
      override this method and return a Python *unicode* string
      corresponding to the HTML you want to render.

On a :term:`content provider` you can use the following :term:`Grok
directive`:

- :py:func:`grok.layer` in order to associate your content provider to
  a given layer,

- :py:func:`grok.context` in order to associate your content provider
  to a given content type,

- :py:func:`grok.view` in order to associate your content provider to
  a given :term:`view` or :term:`page`.

- :py:func:`grok.name` in order to change the name under which your
  content provider is registered. By default it will be registered
  with the same name than the Python class defining it.

From a `Chameleon Page Template`_ you can include your content
provider with the expression prefix ``provider``:

.. code-block:: html
   :linenos:

   <tal:content tal:replace="structure provider:name" />


Using a viewlet manager for a pluggable inclusion of custom HTML
----------------------------------------------------------------


You can create new :term:`Viewlet Manager`:

.. class:: silva.core.views.views.ViewletManager

   Base class used to create a :term:`viewlet manager` that is used to
   hold the result of one or multiple :term:`viewlet`.

   .. attribute:: viewlets

      List of the viewlets than should be rendered by this viewlet
      manager. If you specify a custom template to your viewlet
      manager, you will need to iterate through this list in order to
      call the method ``render`` on each viewlet to get its generated
      HTML in your template.

The :term:`Grok directive` you can use to configure a :term:`viewlet
manager` are the same than for a :term:`Content Provider`.

And in order to insert HTML into :term:`Viewlet Manager` new
:term:`Viewlet`:

.. class:: silva.core.views.views.Viewlet

   Base class used to create a :term:`viewlet`, that renders a
   specific bit of HTML that can be inserted into a given
   :term:`viewlet manager`.

   .. method:: update()

      This method is called before the template is rendered. You can
      override this method in order to compute values to use in your
      template.

   .. method:: render()

      This method is called to render the associated template to the
      :term:`viewlet`. If you do not wish to use a template, you can
      override this method and return a Python *unicode* string
      corresponding to the HTML you want to render.

On a :term:`viewlet` you can use the :term:`Grok directive`
:py:func:`grok.viewletmanager()` to specify for which :term:`viewlet
manager` your viewlet should be register. :py:func:`grok.order()` can
be used to specify in which order the viewlets should be rendered by
their manager.

You can as well use any other directives you would have used on a
viewlet manager on your viewlet.

As example :term:`viewlet manager` and :term:`viewlet` can be used to
create pluggable menu that contains different actions depending on the
content accessed:

.. sourcecode:: python
   :linenos:

   from silva.core import conf as silvaconf
   from silva.core.views import views as silvaviews
   from siva.app.blog.interfaces import IBlog, IBlogArticle

   class BlogActions(silvaviews.ViewletManager):
       silvaconf.layer(IBlogLayer)

   class BlogComment(silvaviews.Viewlet)
       silvaconf.viewletmanager(BlogActions)
       silvaconf.order(10)

   class BlogShare(silvaviews.Viewlet)
       silvaconf.viewletmanager(BlogActions)
       silvaconf.context(IBlogArticle)
       silvaconf.order(20)

   class BlogRSS(silvaviews.Viewlet)
       silvaconf.viewletmanager(BlogActions)
       silvaconf.context(IBlog)
       silvaconf.order(20)

       def update(self)
           self.rss_url = absoluteURL(self.context, self.request) + '/rss.xml'


- Line 5 defines the menu called ``blogactions`` that will be only
  available in our theme.

- Line 8, 11 and 15 defines 3 differents actions that will be inserted
  into ``blogactions``: a comment actions that is always available, a
  share action that is only available on the blog articles and a
  comment action that is only available on he blog itself.

If this code lives in a Python module called ``blogactions.py``, you
can create next to a template directory called
``blogactions_templates`` and add 4 templates to render them
``blogactions.cpt``, ``blogcomment.cpt``, ``blogshare.cpt`` and
``blogrss.cpt``. In your Chameleon Page Template you can invoke this
menu:

.. code-block:: html
   :linenos:

   <nav tal:content="structure provider:blogactions"></nav>

.. _Chameleon Page Template: http://chameleon.readthedocs.org/en/latest/
